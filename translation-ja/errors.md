# エラー処理

- [イントロダクション](#introduction)
- [設定](#configuration)
- [例外ハンドラ](#the-exception-handler)
    - [例外のレポート](#reporting-exceptions)
    - [例外のログレベル](#exception-log-levels)
    - [タイプによる例外の無視](#ignoring-exceptions-by-type)
    - [例外のレンダ](#rendering-exceptions)
    - [Reportable／Renderable例外](#renderable-exceptions)
- [例外レポートの限定](#throttling-reported-exceptions)
- [HTTP例外](#http-exceptions)
    - [カスタムHTTPエラーページ](#custom-http-error-pages)

<a name="introduction"></a>
## イントロダクション

エラーと例外の処理は、新しいLaravelプロジェクトの開始時に最初から設定されています。`App\Exceptions\Handler`クラスは、アプリケーションが投げるすべての例外がログに記録され、ユーザーへレンダする場所です。このドキュメント全体を通して、このクラスについて詳しく説明します。

<a name="configuration"></a>
## 設定

`config/app.php`設定ファイルの`debug`オプションは、エラーに関する情報が実際にユーザーに表示される量を決定します。デフォルトでは、このオプションは、`.env`ファイルに保存されている`APP_DEBUG`環境変数の値を尊重するように設定されています。

ローカル開発中は、`APP_DEBUG`環境変数を`true`に設定する必要があります。**実稼働環境では、この値は常に`false`である必要があります。本番環境で値が`true`に設定されていると、機密性の高い設定値がアプリケーションのエンドユーザーに公開されるリスクが起きます。**

<a name="the-exception-handler"></a>
## 例外ハンドラ

<a name="reporting-exceptions"></a>
### 例外のレポート

すべての例外は、`App\Exceptions\Handler`クラスが処理します。このクラスは、カスタム例外レポートとレンダリングコールバックを登録できる`register`メソッドを持っています。こうした各概念について詳しく説明します。例外レポートは、例外をログに記録したり、[Flare](https://flareapp.io)、[Bugsnag](https://bugsnag.com)、[Sentry](https://github.com/getsentry/sentry-laravel)などの外部サービスへ送信したりするために使用します。デフォルトで例外は[ログ](/docs/{{version}}/logging)設定に基づいてログに記録します。ただし、必要に応じて例外を自由に記録できます。

さまざまなタイプの例外をさまざまな方法で報告する必要がある場合は、`reportable`メソッドを使用して、特定のタイプの例外を報告する必要があるときに実行するクロージャを登録できます。Laravelは、クロージャのタイプヒントを調べることで、クロージャが報告する例外のタイプを決定します。

    use App\Exceptions\InvalidOrderException;

    /**
     * アプリケーションの例外処理コールバックを登録
     */
    public function register(): void
    {
        $this->reportable(function (InvalidOrderException $e) {
            // ...
        });
    }

`reportable`メソッドを使用してカスタム例外レポートコールバックを登録した場合でも、Laravelはアプリケーションのデフォルトのログ設定を使用して例外をログに記録します。デフォルトのログスタックへ例外の伝播を停止する場合は、レポートコールバックを定義するときに`stop`メソッドを使用するか、コールバックから`false`を返します。

    $this->reportable(function (InvalidOrderException $e) {
        // ...
    })->stop();

    $this->reportable(function (InvalidOrderException $e) {
        return false;
    });

> **Note**
> 特定の例外のレポートをカスタマイズするには、[レポート可能な例外](/docs/{{version}}/errors#renderable-exceptions)を利用することもできます。

<a name="global-log-context"></a>
#### グローバルログコンテキスト

利用可能な場合、Laravelは現在のユーザーのIDをコンテキストデータとしてすべての例外のログメッセージに自動的に追加します。アプリケーションの`App\Exceptions\Handler`クラスの`context`メソッドを定義することで、独自のグローバルコンテキストデータを定義できます。この情報は、アプリケーションによって書き込まれるすべての例外のログメッセージに含まれます。

    /**
     * ログ用のデフォルトのコンテキスト変数を取得
     *
     * @return array<string, mixed>
     */
    protected function context(): array
    {
        return array_merge(parent::context(), [
            'foo' => 'bar',
        ]);
    }

<a name="exception-log-context"></a>
#### 例外ログコンテキスト

すべてのログメッセージにコンテキストを追加することは便利ですが、特定の例外にはログに含めたい固有のコンテキストがある場合もあります。アプリケーションの例外へ`context`メソッドを定義することにより、例外のログエントリに追加すべき関連データを指定できます。

    <?php

    namespace App\Exceptions;

    use Exception;

    class InvalidOrderException extends Exception
    {
        // ...

        /**
         * 例外のコンテキスト情報を取得
         *
         * @return array<string, mixed>
         */
        public function context(): array
        {
            return ['order_id' => $this->orderId];
        }
    }

<a name="the-report-helper"></a>
#### `report`ヘルパ

場合により、例外を報告する必要はあるが、現在のリクエストの処理を続行する必要がある場合もあります。`report`ヘルパ関数を使用すると、エラーページをユーザーに表示せずに、例外ハンドラを介して例外をすばやく報告できます。

    public function isValid(string $value): bool
    {
        try {
            // 値のバリデーション…
        } catch (Throwable $e) {
            report($e);

            return false;
        }
    }

<a name="deduplicating-reported-exceptions"></a>
#### Deduplicating Reported Exceptions

アプリケーション全体で`report`関数を使用している場合、同じ例外を複数回報告することがあり、ログに重複したエントリが作成されることがあります。

一つの例外インスタンスが一度だけ報告されるようにしたい場合は、アプリケーションの`AppExceptions`クラスの中で`$withoutDuplicates`プロパティを`true`に設定してください。

```php
namespace App\Exceptions;

use Illuminate\Foundation\Exceptions\Handler as ExceptionHandler;

class Handler extends ExceptionHandler
{
    /**
     * 例外インスタンスが一度だけ報告されるべきであることを示す
     *
     * @var bool
     */
    protected $withoutDuplicates = true;

    // ...
}
```

これで、同じ例外インスタンスで`report`ヘルパが呼び出された場合、最初に呼び出されたものだけが報告されるようになります。

```php
$original = new RuntimeException('Whoops!');

report($original); // レポートされる

try {
    throw $original;
} catch (Throwable $caught) {
    report($caught); // 無視される
}

report($original); // 無視される
report($caught); // 無視される
```

<a name="exception-log-levels"></a>
### 例外のログレベル

アプリケーションの[ログ](/docs/{{version}}/logging)にメッセージが書き込まれるとき、そのメッセージは指定された[ログレベル](/docs/{{version}}/logging#log-levels)で書かれ、これは書き込まれるメッセージの緊急度や重要度を表します。

前記のように、`reportable`メソッドを使用してカスタム例外レポートコールバックを登録した場合でも、Laravelはアプリケーションのデフォルトログ設定を使用して例外を記録します。しかし、ログレベルはメッセージを記録するチャンネルへ影響を与えることがあるため、特定の例外を記録するログレベルを設定したいでしょう。

これを実現するにはアプリケーションの例外ハンドラへ、`$levels`プロパティを定義します。このプロパティには、例外の種類とそれに関連するログレベルの配列を格納する必要があります：

    use PDOException;
    use Psr\Log\LogLevel;

    /**
     * 例外タイプと関係するカスタムログレベルのリスト
     *
     * @var array<class-string<\Throwable>, \Psr\Log\LogLevel::*>
     */
    protected $levels = [
        PDOException::class => LogLevel::CRITICAL,
    ];

<a name="ignoring-exceptions-by-type"></a>
### タイプによる例外の無視

アプリケーションを構築するときに、絶対に報告したくないタイプの例外が発生することがあります。こうした例外を無視するには、アプリケーションの例外ハンドラへ、`$dontReport`プロパティを定義します。このプロパティへ追加したクラスは、決して報告されませんが、カスタムレンダロジックを持つ可能性があります。

    use App\Exceptions\InvalidOrderException;

    /**
     * レポートしない例外タイプのリスト
     *
     * @var array<int, class-string<\Throwable>>
     */
    protected $dontReport = [
        InvalidOrderException::class,
    ];

内部的に、Laravelは予めいくつかのタイプのエラーを無視します。例えば、404 HTTPエラーや無効なCSRFトークンで生成された419 HTTPレスポンスから生じる例外などです。Laravelに指定する種類の例外を無視しないように指示したい場合は、例外ハンドラの`register`メソッド内で`stopIgnoring`メソッドを呼び出します。

    use Symfony\Component\HttpKernel\Exception\HttpException;

    /**
     * アプリケーションの例外処理コールバックを登録
     */
    public function register(): void
    {
        $this->stopIgnoring(HttpException::class);

        // ...
    }

<a name="rendering-exceptions"></a>
### 例外のレンダ

Laravelはデフォルトで、例外ハンドラが例外をHTTPレスポンスに変換します。しかし、指定タイプの例外に対して、カスタムレンダクロージャを自由に登録することもできます。例外ハンドラ内で、`renderable`メソッドを呼び出してください。

`renderable`メソッドへ渡すクロージャは、`Response`ヘルパを介して生成される`Illuminate\Http\Response`のインスタンスを返す必要があります。Laravelは、クロージャのタイプヒントを調べることで、どのタイプの例外をクロージャがレンダするのか決定します。

    use App\Exceptions\InvalidOrderException;
    use Illuminate\Http\Request;

    /**
     * アプリケーションの例外処理コールバックを登録
     */
    public function register(): void
    {
        $this->renderable(function (InvalidOrderException $e, Request $request) {
            return response()->view('errors.invalid-order', [], 500);
        });
    }

また、`renderable`メソッドを使い、`NotFoundHttpException`などのLaravelやSymfonyの組み込み例外のレンダ動作をオーバーライドすることもできます。`renderable`メソッドに指定したクロージャが値を返さない場合は、Laravelのデフォルト例外レンダが利用されます。

    use Illuminate\Http\Request;
    use Symfony\Component\HttpKernel\Exception\NotFoundHttpException;

    /**
     * アプリケーションの例外処理コールバックを登録
     */
    public function register(): void
    {
        $this->renderable(function (NotFoundHttpException $e, Request $request) {
            if ($request->is('api/*')) {
                return response()->json([
                    'message' => 'Record not found.'
                ], 404);
            }
        });
    }

<a name="renderable-exceptions"></a>
### Reportable／Renderable例外

例外ハンドラの`register`メソッドで、カスタムレポートやレンダ動作を定義する代わりに、アプリケーションの例外へ直接`report`と`render`メソッドを定義することもできます。これらのメソッドが存在する場合、フレームワークが自動的に呼び出します。

    <?php

    namespace App\Exceptions;

    use Exception;
    use Illuminate\Http\Request;
    use Illuminate\Http\Response;

    class InvalidOrderException extends Exception
    {
        /**
         * 例外を報告
         */
        public function report(): void
        {
            // ...
        }

        /**
         * 例外をHTTPレスポンスへレンダリング
         */
        public function render(Request $request): Response
        {
            return response(/* ... */);
        }
    }

LaravelやSymfonyの組み込み済み例外など、既存のレンダリング可能な例外を拡張している場合は、例外の`render`メソッドから`false`を返し、例外のデフォルトHTTPレスポンスをレンダできます。

    /**
     * Render the exception into an HTTP response.
     */
    public function render(Request $request): Response|bool
    {
        if (/** この例外をレポートする必要があるかを判断… */) {

            return response(/* ... */);
        }

        return false;
    }

特定の条件が満たされた場合にのみ必要なカスタムレポートロジックが例外に含まれている場合は、デフォルトの例外処理設定を使用して例外をレポートするようにLaravelに指示する必要が起き得ます。これを行うには、例外の`report`メソッドから`false`を返します。

    /**
     * 例外を報告
     */
    public function report(): bool
    {
        if (/** この例外をレポートする必要があるかを判断… */) {

            // ...

            return true;
        }

        return false;
    }

> **Note**
> `report`メソッドで必要な依存関係をタイプヒントすると、Laravelの[サービスコンテナ](/docs/{{version}}/container)がメソッドへ自動的に依存を注入します。

<a name="throttling-reported-exceptions"></a>
### 例外レポートの限定

アプリケーションが非常に多くの例外を報告する場合、実際にログに記録されたり、アプリケーションの外部エラー追跡サービスに送信されたりする例外の数を絞り込みたくなるでしょう。

ランダムな例外のサンプルレートを取るには、例外ハンドラの`throttle`メソッドから、`Lottery`インスタンスを返してください。`App\Exceptions\Handler`クラスにこのメソッドがない場合は、単純にこのメソッドを追加してください。

```php
use Illuminate\Support\Lottery;
use Throwable;

/**
 * 渡された例外の絞り込み
 */
protected function throttle(Throwable $e): mixed
{
    return Lottery::odds(1, 1000);
}
```

また、例外の種類に基づいた条件付きでサンプリングすることも可能です。特定の例外クラスのインスタンスのみをサンプリングしたい場合は、そのクラスの`Lottery`インスタンスのみを返します

```php
use App\Exceptions\ApiMonitoringException;
use Illuminate\Support\Lottery;
use Throwable;

/**
 * 渡された例外の絞り込み
 */
protected function throttle(Throwable $e): mixed
{
    if ($e instanceof ApiMonitoringException) {
        return Lottery::odds(1, 1000);
    }
}
```

また、`Lottery`の代わりに`Limit`インスタンスを返せば、ログに記録する例外や外部のエラー追跡サービスに送信する例外を制限できます。これは、アプリケーションで使用しているサードパーティのサービスがダウンした場合などで、突然例外がログに殺到するのを防ぎたい場合に便利です。

```php
use Illuminate\Broadcasting\BroadcastException;
use Illuminate\Cache\RateLimiting\Limit;
use Throwable;

/**
 * 渡された例外の絞り込み
 */
protected function throttle(Throwable $e): mixed
{
    if ($e instanceof BroadcastException) {
        return Limit::perMinute(300);
    }
}
```

リミットは例外のクラスをレートリミットキーに、デフォルトで使用します。これをカスタマイズするには、`Limit`の`by`メソッドを使用して独自キーを指定します。

```php
use Illuminate\Broadcasting\BroadcastException;
use Illuminate\Cache\RateLimiting\Limit;
use Throwable;

/**
 * 渡された例外の絞り込み
 */
protected function throttle(Throwable $e): mixed
{
    if ($e instanceof BroadcastException) {
        return Limit::perMinute(300)->by($e->getMessage());
    }
}
```

もちろん、異なる例外に対して`Lottery`と`Limit`のインスタンスを混ぜて返すこともできます。

```php
use App\Exceptions\ApiMonitoringException;
use Illuminate\Broadcasting\BroadcastException;
use Illuminate\Cache\RateLimiting\Limit;
use Illuminate\Support\Lottery;
use Throwable;

/**
 * 渡された例外の絞り込み
 */
protected function throttle(Throwable $e): mixed
{
    return match (true) {
        $e instanceof BroadcastException => Limit::perMinute(300),
        $e instanceof ApiMonitoringException => Lottery::odds(1, 1000),
        default => Limit::none(),
    };
}
```

<a name="http-exceptions"></a>
## HTTP例外

一部の例外は、サーバからのHTTPエラーコードを表します。たとえば、「ページが見つかりません」エラー(404)、「不正なエラー」(401)、または開発者が500エラーを生成する可能性もあります。アプリケーションのどこからでもこのようなレスポンスを生成したい場合は、`abort`ヘルパを使用できます。

    abort(404);

<a name="custom-http-error-pages"></a>
### カスタムHTTPエラーページ

Laravelを使用すると、さまざまなHTTPステータスコードのカスタムエラーページを簡単に表示できます。たとえば、404 HTTPステータスコードのエラーページをカスタマイズする場合は、`resources/views/errors/404.blade.php`ビューテンプレートを作成します。このビューは、アプリケーションが生成するすべての404エラーでレンダされます。このディレクトリ内のビューには、対応するHTTPステータスコードと一致する名前を付ける必要があります。`abort`関数によって生成された`Symfony\Component\HttpKernel\Exception\HttpException`インスタンスは`$exception`変数としてビューに渡されます。

    <h2>{{ $exception->getMessage() }}</h2>

`vendor:publish` Artisanコマンドを使用して、Laravelのデフォルトのエラーページテンプレートをリソース公開できます。テンプレートをリソース公開したら、好みに合わせてカスタマイズしてください。

```shell
php artisan vendor:publish --tag=laravel-errors
```

<a name="fallback-http-error-pages"></a>
#### HTTPエラーページのフォールバック

一連のHTTPステータスコードに対応する「フォールバック」エラーページを定義することもできます。このページは、発生した特定のHTTPステータスコードに対応するページが存在しない場合にレンダされます。これには、アプリケーションの`resources/views/errors`ディレクトリに、`4xx.blade.php`テンプレートと`5xx.blade.php`テンプレートを定義します。
