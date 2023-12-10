# データベース：ペジネーション

- [イントロダクション](#introduction)
- [基本的な使い方](#basic-usage)
    - [ペジネーションクエリビルダ結果](#paginating-query-builder-results)
    - [Eloquent結果のペジネーション](#paginating-eloquent-results)
    - [カーソルページング](#cursor-pagination)
    - [ペジネータの手作業生成](#manually-creating-a-paginator)
    - [ペジネーションURLのカスタマイズ](#customizing-pagination-urls)
- [ペジネーション結果の表示](#displaying-pagination-results)
    - [ペジネーションリンクウィンドウの調整](#adjusting-the-pagination-link-window)
    - [結果のJSONへの変換](#converting-results-to-json)
- [ペジネーションビューのカスタマイズ](#customizing-the-pagination-view)
    - [Bootstrapの使用](#using-bootstrap)
- [Paginator／LengthAwarePaginatorインスタンスのメソッド](#paginator-instance-methods)
- [カーソルPaginatorインスタンスのメソッド](#cursor-paginator-instance-methods)

<a name="introduction"></a>
## イントロダクション

他のフレームワークでは、ペジネーション（ページ付け）は非常に苦労することがあります。Laravelのペジネーションへのアプローチがが簡単であると思っていただけるよう願っています。Laravelのペジネーションは[クエリビルダ](/docs/{{version}}/queries)および[Eloquent ORM](/docs/{{version}}/eloquent)と統合されており、設定をしなくても便利で使いやすいペジネーションを提供します。

デフォルトでは、ペジネータによって生成されたHTMLは[Tailwind CSSフレームワーク](https://tailwindcss.com/)と互換性があります。ただし、Bootstrapペジネーションのサポートも利用できます。

<a name="tailwind-jit"></a>
#### Tailwind JIT

LaravelのデフォルトのTailwind pagination viewとTailwind JITエンジンを使用している場合、アプリケーションの`tailwind.config.js`ファイルの`content`キーで、Laravelのペジネーションビューを参照し、そのTailwindクラスがパージされないようにする必要があります。

```js
content: [
    './resources/**/*.blade.php',
    './resources/**/*.js',
    './resources/**/*.vue',
    './vendor/laravel/framework/src/Illuminate/Pagination/resources/views/*.blade.php',
],
```

<a name="basic-usage"></a>
## 基本的な使い方

<a name="paginating-query-builder-results"></a>
### ペジネーションクエリビルダ結果

アイテムをペジネーションする方法はいくつかあります。最も簡単な方法は、[クエリビルダ](/docs/{{version}}/querys)または[Eloquentクエリ](/docs/{{version}}/eloquent)で`paginate`メソッドを使用することです。`paginate`メソッドは、ユーザーが表示している現在のページに基づいて、クエリの"limit"と"offset"の設定を自動的に処理します。デフォルトでは、現在のページはHTTPリクエストの`page`クエリ文字列引数の値から検出されます。この値はLaravelによって自動的に検出され、ペジネータが生成するリンクにも自動的に挿入されます。

この例では、`paginate`メソッドへ渡す引数は、唯一「ページごと」に表示するアイテムの数です。例として、ページごとに「１５」個のアイテムを表示するように指定してみましょう。

    <?php

    namespace App\Http\Controllers;

    use App\Http\Controllers\Controller;
    use Illuminate\Support\Facades\DB;
    use Illuminate\View\View;

    class UserController extends Controller
    {
        /**
         * アプリケーションのすべてのユーザーを表示
         */
        public function index(): View
        {
            return view('user.index', [
                'users' => DB::table('users')->paginate(15)
            ]);
        }
    }

<a name="simple-pagination"></a>
#### シンプルなペジネーション

`paginate`メソッドは、データベースからレコードを取得する前に、クエリで一致するレコードの総数をカウントします。これは、ペジネータが合計で何ページ分のレコードがあるかを知るために行います。ただし、アプリケーションのUIに合計ページ数を表示する予定がない場合は、レコード数のクエリは不要です。

したがって、アプリケーションのUIに単純な「次へ」リンクと「前へ」リンクのみを表示する必要がある場合は、`simplePaginate`メソッドを使用して、単一で効率的なクエリが実行できます。

    $users = DB::table('users')->simplePaginate(15);

<a name="paginating-eloquent-results"></a>
### Eloquent結果のペジネーション

[Eloquent](/docs/{{version}}/eloquent)クエリをページ分割することもできます。この例では、`App\Models\User`モデルをページ分割し、ページごとに１５レコードを表示するプランであることを示します。ご覧のとおり、構文はクエリビルダの結果のペジネーションとほぼ同じです。

    use App\Models\User;

    $users = User::paginate(15);

もちろん、`where`句など、クエリに他の制約を設定した後、`paginate`メソッドを呼び出すこともできます。

    $users = User::where('votes', '>', 100)->paginate(15);

Eloquentモデルをペジネーションするときに`simplePaginate`メソッドを使用することもできます。

    $users = User::where('votes', '>', 100)->simplePaginate(15);

同様に、`cursorPaginate`メソッドをEloquentモデルのカーソルページングに使用できます。

    $users = User::where('votes', '>', 100)->cursorPaginate(15);

<a name="multiple-paginator-instances-per-page"></a>
#### １ページ上のマルチペジネータインスタンス

アプリケーションがレンダするひとつの画面上で、 2つの別々のぺジネータをレンダする必要がある場合があります。しかし、両方のペジネータのインスタンスが現在のページを格納するのに`page`というクエリ文字列パラメータを使っていると、２つのペジネータが衝突してしまいます。この衝突を解決するには`paginate`、`simplePaginate`、`cursorPaginate`の各メソッドの第３引数に、ペジネータの現在のページを格納するために使いたいクエリストリングパラメータの名前を渡してください。

    use App\Models\User;

    $users = User::where('votes', '>', 100)->paginate(
        $perPage = 15, $columns = ['*'], $pageName = 'users'
    );

<a name="cursor-pagination"></a>
### カーソルページング

`paginate`と`simplePaginate`がSQLの"offset"句を使用してクエリを作成するのに対し、カーソルペジネーションは "where"句を使い制約し、効率的なデータベースパフォーマンスを実現します。このペジネーションの方法は、特に大規模なデータセットや、「無限」にスクロールするユーザーインターフェイスに適しています。

ペジネイタが生成するURLのクエリ文字列にページ番号を含めるオフセットベースのペジネーションとは異なり、カーソルベースのペジネーションでは、クエリ文字列に「カーソル」文字列を配置します。カーソルは、ページ処理した次のクエリがページ処理を開始すべき場所と、ページ処理すべき方向を示すエンコードした文字列です。

```nothing
http://localhost/users?cursor=eyJpZCI6MTUsIl9wb2ludHNUb05leHRJdGVtcyI6dHJ1ZX0
```

カーソルベースのペジネータインスタンスを作成するには，クエリビルダが提供する`cursorPaginate`メソッドを使用します。このメソッドは，`Illuminate\Pagination\CursorPaginator`インスタンスを返します。

    $users = DB::table('users')->orderBy('id')->cursorPaginate(15);

カーソルページネータインスタンスを取得したら、`paginate`や`simplePaginate`メソッドを使うときと同様に、[ペジネーションの結果を表示](#displaying-pagination-results)します。カーソルペジネータが提供するインスタンスメソッドの詳細は、[カーソルペジネータインスタンスのドキュメント](#cursor-paginator-instance-methods)を参照してください。

> **Warning**
> カーソルのペジネーションを利用するには、クエリへ"order by"句を含める必要があります。さらに、クエリの順序を指定するカラムは、ペジネーションを行うテーブルに属している必要もあります。

<a name="cursor-vs-offset-pagination"></a>
#### カーソル vs. オフセットペジネーション

オフセットページングとカーソルページングの違いを説明するために、いくつかのSQLクエリの例を見てみましょう。以下のクエリはどちらも、`users`テーブルの結果を`id`で並べた「２ページ目」を表示します。

```sql
# オフセットページング
select * from users order by id asc limit 15 offset 15;

# カーソルページング
select * from users where id > 15 order by id asc limit 15;
```

カーソルページングクエリは、オフセットページングに比べて以下の利点があります。

- 大規模なデータセットの場合、"order by"のカラムにインデックスが付けられている場合、カーソルページングはパフォーマンスが向上します。これは、"offset"句が以前に一致したデータをすべてスキャンするためです。
- 頻繁な書き込みを伴うデータセットの場合、直前にレコードが追加／削除されている場合、ユーザーが現在閲覧しているページの結果からレコードが飛ばされたり、二重に表示されたりする可能性があります。

ただし、カーソルペジネーションには以下の制限があります。

- `simplePaginate`のように、カーソルのペジネーションは"次へ"と"前へ"のリンクを表示するためにのみ使用でき、ページ番号付きのリンクの生成をサポートしていません。
- ソート順は少なくとも１つの一意なカラム、または一意なカラムの組み合わせに基づく必要があります。`null`値を持つカラムはサポートしていません。
- "order by"句のクエリ表現はエイリアス化され、"select"句にも同様に追加されている場合のみサポートします。
- パラメータを含むクエリ表現は、サポートしていません。

<a name="manually-creating-a-paginator"></a>
### ペジネータの手作業生成

場合によっては、ペジネーションインスタンスを手作業で作成し、メモリ内にすでにあるアイテムの配列を渡すことができます。必要に応じて、`Illuminate\Pagination\Paginator`、`Illuminate\Pagination\LengthAwarePaginator`、`Illuminate\Pagination\CursorPaginator`インスタンスを生成することでこれが行えます。

`Paginator`と`CursorPaginator`クラスは結果セットのアイテムの総数を知る必要はありません。しかしこのため、これらのクラスには最後のページのインデックスを取得するメソッドがありません。`LengthAwarePaginator`は`Paginator`とほぼ同じ引数を取りますが、結果セットのアイテムの総数をカウントする必要があります．

つまり，`Paginator`はクエリビルダの`simplePaginate`メソッドに、`CursorPaginator`は`cursorPaginate`メソッドに，`LengthAwarePaginator`は`paginate`メソッドに、それぞれ対応しています。

> **Warning**
> ペジネーションインスタンスを手作業で作成する場合は、ペジネーションに渡す結果の配列を手作業で「スライス」する必要があります。これを行う方法がわからない場合は、[array_slice](https://secure.php.net/manual/en/function.array-slice.php)PHP関数を確認してください。

<a name="customizing-pagination-urls"></a>
### ペジネーションURLのカスタマイズ

デフォルトでは、ペジネータにが生成するリンクは、現在のリクエストのURIと一致します。ただし、ペジネータの`withPath`メソッドを使用すると、リンクを生成するときにペジネータが使用するURIをカスタマイズできます。たとえば、ペジネータで`http://example.com/admin/users？page=N`のようなリンクを生成したい場合は、`/admin/users`を`withPath`メソッドに渡します。

    use App\Models\User;

    Route::get('/users', function () {
        $users = User::paginate(15);

        $users->withPath('/admin/users');

        // ...
    });

<a name="appending-query-string-values"></a>
#### クエリ文字列の追加

`appends`メソッドを使用して、ペジネーションリンクのクエリ文字列へ追加できます。たとえば、各ペジネーションリンクに`sort=votes`を追加するには、`appends`を以下のように呼び出します。

    use App\Models\User;

    Route::get('/users', function () {
        $users = User::paginate(15);

        $users->appends(['sort' => 'votes']);

        // ...
    });

現在のリクエストのすべてのクエリ文字列値をペジネーションリンクに追加する場合は、`withQueryString`メソッドを使用できます。

    $users = User::paginate(15)->withQueryString();

<a name="appending-hash-fragments"></a>
#### ハッシュフラグメントの追加

paginatorによって生成されたURLに「ハッシュフラグメント」を追加する必要がある場合は、`fragment`メソッドを使用できます。たとえば、各ペジネーションリンクの最後に`#users`を追加するには、次のように`fragment`メソッドを呼び出します。

    $users = User::paginate(15)->fragment('users');

<a name="displaying-pagination-results"></a>
## ペジネーション結果の表示

`paginate`メソッドを呼ぶと、`Illuminate\Pagination\LengthAwarePaginator`インスタンスが返され，`simplePaginate`メソッドを呼ぶと、`Illuminate\Pagination\Paginator`インスタンスが返されます。そして、`cursorPaginate`メソッドを呼び出すと、`Illuminate\CursorPaginator`インスタンスが返されます。

これらのオブジェクトは、結果セットを表示するメソッドをいくつか提供しています。これらヘルパメソッドに加え、ペジネータインスタンスはイテレータであり、配列としてループ処理も可能です。つまり、結果を取得したら、[Blade](/docs/{{version}}/blade) を使って結果を表示したり、ページリンクをレンダしたりできるのです。

```blade
<div class="container">
    @foreach ($users as $user)
        {{ $user->name }}
    @endforeach
</div>

{{ $users->links() }}
```

`links`メソッドは、結果セットの残りのページへのリンクをレンダします。これらの各リンクには、適切な`page`クエリ文字列変数がすでに含まれています。`links`メソッドが生成するHTMLは、[Tailwind CSSフレームワーク](https://tailwindcss.com)と互換性があることを忘れないでください。

<a name="adjusting-the-pagination-link-window"></a>
### ペジネーションリンクウィンドウの調整

ページネータがページ処理用のリンクを表示する際には、現在のページ番号に加え、現在のページの前後３ページ分のリンクが表示されます。`onEachSide`メソッドを使用して、ページネータが生成するリンクの中央のスライディングウィンドウ内の現在のページの両側に表示する追加のリンク数を制御できます。

```blade
{{ $users->onEachSide(5)->links() }}
```

<a name="converting-results-to-json"></a>
### 結果のJSONへの変換

Laravelペジネータクラスは`Illuminate\Contracts\Support\Jsonable`インターフェイスコントラクトを実装し、`toJson`メソッドを提供しているため、ペジネーションの結果をJSONに変換するのは非常に簡単です。ルートまたはコントローラアクションから返すことで、ペジネーションインスタンスをJSONに変換することもできます。

    use App\Models\User;

    Route::get('/users', function () {
        return User::paginate();
    });

ペジネーターからのJSONは、`total`、`current_page`、`last_page`などのメタ情報を持っています。結果レコードは、JSON配列の`data`キーを介して利用できます。ルートからペジネーションインスタンスを返すことによって作成されたJSONの例を以下で紹介します。

    {
       "total": 50,
       "per_page": 15,
       "current_page": 1,
       "last_page": 4,
       "first_page_url": "http://laravel.app?page=1",
       "last_page_url": "http://laravel.app?page=4",
       "next_page_url": "http://laravel.app?page=2",
       "prev_page_url": null,
       "path": "http://laravel.app",
       "from": 1,
       "to": 15,
       "data":[
            {
                // レコード…
            },
            {
                // レコード…
            }
       ]
    }

<a name="customizing-the-pagination-view"></a>
## ペジネーションビューのカスタマイズ

デフォルトでは、ペジネーションリンクを表示するためにレンダリングされたビューは、[Tailwind CSS](https://tailwindcss.com)フレームワークと互換性があります。ただし、Tailwindを使用しない場合は、これらのリンクをレンダするために独自のビューを自由に定義できます。paginatorインスタンスで`links`メソッドを呼び出すとき、メソッドの最初の引数としてビュー名を渡すことができます。

```blade
{{ $paginator->links('view.name') }}

<!-- 追加データをビューへ渡す -->
{{ $paginator->links('view.name', ['foo' => 'bar']) }}
```

ただし、ペジネーションビューをカスタマイズする最も簡単な方法は、`vendor:publish`コマンドを使用して`resources/views/vendor`ディレクトリにエクスポートすることです。

```shell
php artisan vendor:publish --tag=laravel-pagination
```

このコマンドは、ビューをアプリケーションの`resources/views/vendor/pagination`ディレクトリに配置します。このディレクトリ内の`tailwind.blade.php`ファイルが、デフォルトのペジネーションビューに対応しています。このファイルを編集して、ペジネーションHTMLを変更できます。

別のファイルをデフォルトのペジネーションビューとして指定する場合は、`App\Providers\AppServiceProvider`クラスの`boot`メソッド内でペジネーションの`defaultView`メソッドと`defaultSimpleView`メソッドを呼び出すことができます。

    <?php

    namespace App\Providers;

    use Illuminate\Pagination\Paginator;
    use Illuminate\Support\ServiceProvider;

    class AppServiceProvider extends ServiceProvider
    {
        /**
         * 全アプリケーションサービスの初期起動処理
         */
        public function boot(): void
        {
            Paginator::defaultView('view-name');

            Paginator::defaultSimpleView('view-name');
        }
    }

<a name="using-bootstrap"></a>
### Bootstrapの使用

Laravelは、[Bootstrap CSS](https://getbootstrap.com/)を使用し構築した、ペジネーションビューを用意しています。デフォルトのTailwindビューの代わりにこのビューを使うには、`App\Providers\AppServiceProvider`クラスの`boot`メソッドないから、ペジネータの`useBootstrapFour`または`useBootstrapFive`メソッドを呼び出してください。

    use Illuminate\Pagination\Paginator;

    /**
     * 全アプリケーションサービスの初期起動処理
     */
    public function boot(): void
    {
        Paginator::useBootstrapFive();
        Paginator::useBootstrapFour();
    }

<a name="paginator-instance-methods"></a>
## Paginator／LengthAwarePaginatorインスタンスのメソッド

各ペジネーションインスタンスは、以下のメソッドで追加のペジネーション情報を提供します。

| メソッド                                | 説明                                                                               |
| --------------------------------------- | ---------------------------------------------------------------------------------- |
| `$paginator->count()`                   | 現在のページのアイテム数を取得                                                     |
| `$paginator->currentPage()`             | 現在のページ番号を取得                                                             |
| `$paginator->firstItem()`               | 結果の最初の項目の結果番号を取得                                                   |
| `$paginator->getOptions()`              | ペジネータオプションを取得                                                         |
| `$paginator->getUrlRange($start, $end)` | ペジネーションURLを範囲内で生成                                                    |
| `$paginator->hasPages()`                | 複数のページに分割するのに十分なアイテムがあるかどうかを判定                       |
| `$paginator->hasMorePages()`            | データストアにさらにアイテムがあるかどうかを判定                                   |
| `$paginator->items()`                   | 現在のページのアイテムを取得                                                       |
| `$paginator->lastItem()`                | 結果の最後のアイテムの結果番号を取得                                               |
| `$paginator->lastPage()`                | 最後に利用可能なページのページ番号を取得（`simplePaginate`使用時は使用不可能）     |
| `$paginator->nextPageUrl()`             | 次のページのURLを取得                                                              |
| `$paginator->onFirstPage()`             | ペジネータが最初のページにあるかを判定                                             |
| `$paginator->perPage()`                 | １ページ中に表示するアイテムの数                                                   |
| `$paginator->previousPageUrl()`         | 前のページのURLを取得                                                              |
| `$paginator->total()`                   | データストア内の一致するアイテムの総数を判定（`simplePaginate`使用時は使用不可能） |
| `$paginator->url($page)`                | 指定するページ番号のURLを取得                                                      |
| `$paginator->getPageName()`             | ページの保存に使用するクエリ文字列変数を取得                                       |
| `$paginator->setPageName($name)`        | ページの保存に使用するクエリ文字列変数を設定                                       |
| `$paginator->through($callback)`        | コールバックを使い、各アイテムを変換                                               |

<a name="cursor-paginator-instance-methods"></a>
## カーソルPaginatorインスタンスのメソッド

各カーソルペジネータインスタンスは、以降のメソッドで追加のペジネーション情報を提供します。

| メソッド                        | 説明                                                         |
| ------------------------------- | ------------------------------------------------------------ |
| `$paginator->count()`           | 現在のページのアイテム数を取得                               |
| `$paginator->cursor()`          | 現在のカーソルインスタンスを取得                             |
| `$paginator->getOptions()`      | ペジネータオプションを取得                                   |
| `$paginator->hasPages()`        | 複数のページに分割するのに十分なアイテムがあるかどうかを判定 |
| `$paginator->hasMorePages()`    | データストアにさらにアイテムがあるかどうかを判定             |
| `$paginator->getCursorName()`   | カーソルの保存で使用するクエリ文字列変数を取得               |
| `$paginator->items()`           | 現在のページのアイテムを取得                                 |
| `$paginator->nextCursor()`      | 次のアイテムセットのカーソルインスタンスを取得               |
| `$paginator->nextPageUrl()`     | 次のページのURLを取得                                        |
| `$paginator->onFirstPage()`     | ペジネータが最初のページにあるかを判定                       |
| `$paginator->onLastPage()`      | ペジネータが最後のページにあるかを判定                       |
| `$paginator->perPage()`         | １ページ中に表示するアイテムの数                             |
| `$paginator->previousCursor()`  | 前のアイテムセットのカーソルインスタンスを取得               |
| `$paginator->previousPageUrl()` | 前のページのURLを取得                                        |
| `$paginator->setCursorName()`   | カーソルの保存に使用するクエリ文字列変数を設定               |
| `$paginator->url($cursor)`      | 指定するカーソルインスタンスのURLを取得                      |
