# ミドルウェア

- [イントロダクション](#introduction)
- [ミドルウェアの定義](#defining-middleware)
- [ミドルウェアの登録](#registering-middleware)
    - [グローバルミドルウェア](#global-middleware)
    - [ルートに対するミドルウェアの指定](#assigning-middleware-to-routes)
    - [ミドルウェアグループ](#middleware-groups)
    - [ミドルウェアの順序](#sorting-middleware)
- [ミドルウェアのパラメータ](#middleware-parameters)
- [終了処理ミドルウェア](#terminable-middleware)

<a name="introduction"></a>
## イントロダクション

ミドルウェアは、アプリケーションに入るHTTPリクエストを検査およびフィルタリングするための便利なメカニズムを提供します。たとえば、Laravelには、アプリケーションのユーザーが認証されていることを確認するミドルウェアが含まれています。ユーザーが認証されていない場合、ミドルウェアはユーザーをアプリケーションのログイン画面にリダイレクトします。逆に、ユーザーが認証されている場合、ミドルウェアはリクエストをアプリケーションへ進めることを許可します。

ミドルウェアを追加して、認証以外にもさまざまなタスクを実行できます。たとえば、ログミドルウェアなら、アプリケーションが受信したすべてのリクエストをログへ記録できるでしょう。Laravelフレームワークには、認証やCSRF保護用のミドルウェアなど、ミドルウェアがいくつか含まれています。これらのミドルウェアはすべて、`app/Http/Middleware`ディレクトリにあります。

<a name="defining-middleware"></a>
## ミドルウェアの定義

新しいミドルウェアを作成するには、`make:middleware`　Artisanコマンドを使用します。

```shell
php artisan make:middleware EnsureTokenIsValid
```

このコマンドは、新しい`EnsureTokenIsValid`クラスを`app/Http/Middleware`ディレクトリ内に配置します。例としてこのミドルウェアで、リクエストが供給する`token`入力が、指定値と一致する場合にのみ、ルートへのアクセスを許可します。それ以外の場合は、ユーザーを`home` URIへリダイレクトしましょう。

    <?php

    namespace App\Http\Middleware;

    use Closure;
    use Illuminate\Http\Request;
    use Symfony\Component\HttpFoundation\Response;

    class EnsureTokenIsValid
    {
        /**
         * 受信リクエストの処理
         *
         * @param  \Closure(\Illuminate\Http\Request): (\Symfony\Component\HttpFoundation\Response)  $next
         */
        public function handle(Request $request, Closure $next): Response
        {
            if ($request->input('token') !== 'my-secret-token') {
                return redirect('home');
            }

            return $next($request);
        }
    }

ご覧のとおり、与えられた`token`がシークレットトークンと一致しない場合、ミドルウェアはHTTPリダイレクトをクライアントに返します。それ以外の場合、リクエストはさらにアプリケーションに渡されます。リクエストをアプリケーションのより深いところに渡す(ミドルウェアが「パス」できるようにする)には、`$request`を使用して`$next`コールバックを呼び出す必要があります。

ミドルウェアは、HTTPリクエストがアプリケーションに到達する前に通過しなければならない一連の「レイヤー」として考えるのがベストです。各レイヤーはリクエストを検査したり、完全に拒否したりすることができます。

> **Note**
> すべてのミドルウェアは[サービスコンテナ](/docs/{{version}}/container)を介して依存解決されるため、ミドルウェアのコンストラクター内で必要な依存関係をタイプヒントで指定できます。

<a name="before-after-middleware"></a>
<a name="middleware-and-responses"></a>
#### ミドルウェアとレスポンス

もちろん、ミドルウェアはアプリケーションのより深部へリクエストの処理を委ねるその前後にあるタスクを実行できます。たとえば、次のミドルウェアは、リクエストがアプリケーションによって処理される**前に**いくつかのタスクを実行します。

    <?php

    namespace App\Http\Middleware;

    use Closure;
    use Illuminate\Http\Request;
    use Symfony\Component\HttpFoundation\Response;

    class BeforeMiddleware
    {
        public function handle(Request $request, Closure $next): Response
        {
            // アクションの実行…

            return $next($request);
        }
    }

一方、このミドルウェアは、リクエストがアプリケーションによって処理された**後に**そのタスクを実行します。

    <?php

    namespace App\Http\Middleware;

    use Closure;
    use Illuminate\Http\Request;
    use Symfony\Component\HttpFoundation\Response;

    class AfterMiddleware
    {
        public function handle(Request $request, Closure $next): Response
        {
            $response = $next($request);

            // アクションの実行…

            return $response;
        }
    }

<a name="registering-middleware"></a>
## ミドルウェアの登録

<a name="global-middleware"></a>
### グローバルミドルウェア

アプリケーションへのすべてのHTTPリクエスト中であるミドルウェアを実行する場合は、`app/Http/Kernel.php`クラスの`$middleware`プロパティにそのミドルウェアクラスをリストします。

<a name="assigning-middleware-to-routes"></a>
### ルートに対するミドルウェアの指定

特定のルートにミドルウェアを割り当てたい場合は、ルート定義時に`middleware`メソッドを呼び出してください。

    use App\Http\Middleware\Authenticate;

    Route::get('/profile', function () {
        // ...
    })->middleware(Authenticate::class);

ミドルウェア名の配列を`middleware`メソッドへ渡し、ルートに複数のミドルウェアを割り当てることもできます。

    Route::get('/', function () {
        // ...
    })->middleware([First::class, Second::class]);

アプリケーションの`app/Http/Kernel.php`ファイルで、ミドルウェアにエイリアスを割り当てておくと便利です。デフォルトで、このクラスの`$middlewareAliases`プロパティには、Laravelに含まれるミドルウェアのエントリーが含まれています。このリストに自分のミドルウェアを追加して、好きなエイリアスを割り当てられます。

    // App\Http\Kernelクラス内…

    protected $middlewareAliases = [
        'auth' => \App\Http\Middleware\Authenticate::class,
        'auth.basic' => \Illuminate\Auth\Middleware\AuthenticateWithBasicAuth::class,
        'bindings' => \Illuminate\Routing\Middleware\SubstituteBindings::class,
        'cache.headers' => \Illuminate\Http\Middleware\SetCacheHeaders::class,
        'can' => \Illuminate\Auth\Middleware\Authorize::class,
        'guest' => \App\Http\Middleware\RedirectIfAuthenticated::class,
        'signed' => \Illuminate\Routing\Middleware\ValidateSignature::class,
        'throttle' => \Illuminate\Routing\Middleware\ThrottleRequests::class,
        'verified' => \Illuminate\Auth\Middleware\EnsureEmailIsVerified::class,
    ];

HTTPカーネルでミドルウェアのエイリアスを定義したら、ルートにミドルウェアを割り当てるときに、そのエイリアスを使用できます。

    Route::get('/profile', function () {
        // ...
    })->middleware('auth');

<a name="excluding-middleware"></a>
#### 除外ミドルウェア

ミドルウェアをルートのグループに割り当てる場合、あるミドルウェアをグループ内の個々のルートに適用しないようにする必要が起きることもあります。これは、`withoutMiddleware`メソッドを使用して実行できます。

    use App\Http\Middleware\EnsureTokenIsValid;

    Route::middleware([EnsureTokenIsValid::class])->group(function () {
        Route::get('/', function () {
            // …
        });

        Route::get('/profile', function () {
            // …
        })->withoutMiddleware([EnsureTokenIsValid::class]);
    });

また、ルート定義の[グループ](/docs/{{version}}/routing#route-groups)全体から特定のミドルウェアのセットを除外することもできます。

    use App\Http\Middleware\EnsureTokenIsValid;

    Route::withoutMiddleware([EnsureTokenIsValid::class])->group(function () {
        Route::get('/profile', function () {
            // …
        });
    });

`withoutMiddleware`メソッドはルートミドルウェアのみを削除でき、[グローバルミドルウェア](#global-middleware)には適用されません。

<a name="middleware-groups"></a>
### ミドルウェアグループ

複数のミドルウェアを１つのキーにグループ化して、ルートへの割り当てを容易にしたい場合もあるでしょう。これは、HTTPカーネルの`$middlewareGroups`プロパティを使用して実現可能です。

Laravelは、一般的にWebおよびAPIルートへ適用される可能性のあるミドルウェアを`web`および`api`ミドルウェアグループへ予め定義しています。これらのミドルウェアグループは、アプリケーションの`App\Providers\RouteServiceProvider`サービスプロバイダによって、対応する`web`および`api`ルートファイル内のルートに自動的に適用されることに注意してください。

    /**
     * アプリケーションのルートミドルウェアグループ
     *
     * @var array
     */
    protected $middlewareGroups = [
        'web' => [
            \App\Http\Middleware\EncryptCookies::class,
            \Illuminate\Cookie\Middleware\AddQueuedCookiesToResponse::class,
            \Illuminate\Session\Middleware\StartSession::class,
            \Illuminate\View\Middleware\ShareErrorsFromSession::class,
            \App\Http\Middleware\VerifyCsrfToken::class,
            \Illuminate\Routing\Middleware\SubstituteBindings::class,
        ],

        'api' => [
            \Illuminate\Routing\Middleware\ThrottleRequests::class.':api',
            \Illuminate\Routing\Middleware\SubstituteBindings::class,
        ],
    ];

ミドルウェアグループは、個々のミドルウェアと同じ構文を使用して、ルートとコントローラアクションに割り当てることができます。繰り返しますが、ミドルウェアグループを使用すると、より便利に一度に多くのミドルウェアをルートに割り当てられます。

    Route::get('/', function () {
        // ...
    })->middleware('web');

    Route::middleware(['web'])->group(function () {
        // ...
    });

> **Note**
> 最初から、`web`および`api`ミドルウェアグループは、`App\Providers\RouteServiceProvider`によってアプリケーションの対応する`routes/web.php`および`routes/api.php`ファイルへ自動的に適用されます。

<a name="sorting-middleware"></a>
### ミドルウェアの順序

まれに、ミドルウェアを特定の順序で実行する必要があるでしょうが、ルートに割り当てられたときにはミドルウェアの順序を制御できません。この場合、`app/Http/Kernel.php`ファイルの`$middlewarePriority`プロパティを使用してミドルウェアの優先度を指定できます。このプロパティは、デフォルトではHTTPカーネルに存在していません。存在しない場合は、以下のデフォルト定義をコピーしてください。

    /**
     * ミドルウェアの優先順位でソートされたリスト
     *
     * これにより、非グローバルミドルウェアは常に指定する順序で実行されます。
     *
     * @var string[]
     */
    protected $middlewarePriority = [
        \Illuminate\Foundation\Http\Middleware\HandlePrecognitiveRequests::class,
        \Illuminate\Cookie\Middleware\EncryptCookies::class,
        \Illuminate\Session\Middleware\StartSession::class,
        \Illuminate\View\Middleware\ShareErrorsFromSession::class,
        \Illuminate\Contracts\Auth\Middleware\AuthenticatesRequests::class,
        \Illuminate\Routing\Middleware\ThrottleRequests::class,
        \Illuminate\Routing\Middleware\ThrottleRequestsWithRedis::class,
        \Illuminate\Contracts\Session\Middleware\AuthenticatesSessions::class,
        \Illuminate\Routing\Middleware\SubstituteBindings::class,
        \Illuminate\Auth\Middleware\Authorize::class,
    ];

<a name="middleware-parameters"></a>
## ミドルウェアのパラメータ

ミドルウェアは追加のパラメータを受け取ることもできます。たとえば、アプリケーションが特定のアクションを実行する前に、認証済みユーザーが特定の「役割り（role）」を持っていることを確認する必要がある場合、追加の引数として役割名を受け取る`EnsureUserHasRole`ミドルウェアを作成できます。

追加のミドルウェアパラメータは、`$next`引数の後にミドルウェアに渡されます。

    <?php

    namespace App\Http\Middleware;

    use Closure;
    use Illuminate\Http\Request;
    use Symfony\Component\HttpFoundation\Response;

    class EnsureUserHasRole
    {
        /**
         * 受信リクエストの処理
         *
         * @param  \Closure(\Illuminate\Http\Request): (\Symfony\Component\HttpFoundation\Response)  $next
         */
        public function handle(Request $request, Closure $next, string $role): Response
        {
            if (! $request->user()->hasRole($role)) {
                // リダイレクト…
            }

            return $next($request);
        }

    }

ミドルウェアのパラメータはルート定義時に、ミドルウェア名とパラメータを「:」で区切って指定します。

    Route::put('/post/{id}', function (string $id) {
        // ...
    })->middleware('role:editor');

複数のパラメーターはコンマで区切る必要があります。

    Route::put('/post/{id}', function (string $id) {
        // ...
    })->middleware('role:editor,publisher');

<a name="terminable-middleware"></a>
## 終了処理ミドルウェア

HTTPレスポンスがブラウザに送信された後、ミドルウェアが何らかの作業を行う必要がある場合があります。ミドルウェアで`terminate`メソッドを定義し、WebサーバがFastCGIを使用している場合、レスポンスがブラウザに送信された後、`terminate`メソッドが自動的に呼び出されます。

    <?php

    namespace Illuminate\Session\Middleware;

    use Closure;
    use Illuminate\Http\Request;
    use Symfony\Component\HttpFoundation\Response;

    class TerminatingMiddleware
    {
        /**
         * 受信リクエストの処理
         *
         * @param  \Closure(\Illuminate\Http\Request): (\Symfony\Component\HttpFoundation\Response)  $next
         */
        public function handle(Request $request, Closure $next): Response
        {
            return $next($request);
        }

        /**
         * レスポンスがブラウザに送信された後にタスクを処理
         */
        public function terminate(Request $request, Response $response): void
        {
            // …
        }
    }

`terminate`メソッドは、リクエストとレスポンスの両方を受信する必要があります。終了処理ミドルウェアを定義したら、それを`app/Http/Kernel.php`ファイルのルートまたはグローバルミドルウェアのリストに追加する必要があります。

ミドルウェアで`terminate`メソッドを呼び出すと、Laravelは[サービスコンテナ](/docs/{{version}}/container)からミドルウェアの新しいインスタンスを依存解決します。`handle`メソッドと`terminate`メソッドが呼び出されたときに同じミドルウェアインスタンスを使用する場合は、コンテナの`singleton`メソッドを使用してミドルウェアをコンテナに登録します。通常、これは`AppServiceProvider`の`register`メソッドで実行する必要があります。

    use App\Http\Middleware\TerminatingMiddleware;

    /**
     * 全アプリケーションサービスの登録
     */
    public function register(): void
    {
        $this->app->singleton(TerminatingMiddleware::class);
    }
