# アップグレードガイド

- [9.xから10.0へのアップグレード](#upgrade-10.0)

<a name="high-impact-changes"></a>
## 影響度の高い変更

<div class="content-list" markdown="1">

- [依存パッケージの更新](#updating-dependencies)
- [最低安定度の更新](#updating-minimum-stability)

</div>

<a name="medium-impact-changes"></a>
## 影響度が中程度の変更

<div class="content-list" markdown="1">

- [データベース構文](#database-expressions)
- [モデルの"Dates"プロパティ](#model-dates-property)
- [Monolog3](#monolog-3)
- [Redisキャッシュタグ](#redis-cache-tags)
- [サービスのモック](#service-mocking)
- [言語ディレクトリ](#language-directory)

</div>

<a name="low-impact-changes"></a>
## 影響度の低い変更

<div class="content-list" markdown="1">

- [クロージャバリデーションルールメッセージ](#closure-validation-rule-messages)
- [Publicパスの結合](#public-path-binding)
- [クエリ例外のコンストラクタ](#query-exception-constructor)
- [レート制限の戻り値](#rate-limiter-return-values)
- [リレーションの`getBaseQuery`メソッド](#relation-getbasequery-method)
- [`Redirect::home`メソッド](#redirect-home)
- [`Bus::dispatchNow`メソッド](#dispatch-now)
- [`registerPolicies`メソッド](#register-policies)
- [ULIDカラム](#ulid-columns)

</div>

<a name="upgrade-10.0"></a>
## 9.xから10.0へのアップグレード

<a name="estimated-upgrade-time-??-minutes"></a>
#### アップデート見積もり時間：１０分

> **Note**
> 私たちは、互換性のない変更を可能な限りすべて文書化するよう努力しています。これらの互換性のない変更の一部はフレームワークの不明瞭な部分であり、こうした変更の一部が実際にあなたのアプリケーションに影響を与える可能性があります。時間を節約したいですか？[Laravel Shift](https://laravelshift.com/) を使用すると、アプリケーションのアップグレードを自動化できます。

<a name="updating-dependencies"></a>
### 依存パッケージのアップデート

**影響の可能性： 高い**

#### PHP8.1.0必須

現バージョンのLaravelは、PHP8.1.0以上が必要です。

#### Composer2.2.0必須

現バージョンのLaravelは、[Composer](https://getcomposer.org)2.2.0以上が必要です。

#### Composerの依存パッケージ

アプリケーションの`composer.json`ファイルにある、以下の依存パッケージを更新してください。

<div class="content-list" markdown="1">

- `laravel/framework`を`^10.0`
- `laravel/sanctum`を`^3.2`
- `doctrine/dbal`を`^3.0`
- `spatie/laravel-ignition`を`^2.0`
- `laravel/passport`を`^11.0` ([アップグレードガイド](https://github.com/laravel/passport/blob/11.x/UPGRADE.md))

</div>

Sanctum2.xリリースシリーズから3.xへアップグレードする場合は、[Sanctumアップグレードガイド](https://github.com/laravel/sanctum/blob/3.x/UPGRADE.md)を参照してください。

さらに、[PHPUnit10](https://phpunit.de/announcements/phpunit-10.html)を使用したい場合は、アプリケーションの`phpunit.xml`設定ファイルの`<coverage>`セクションから、`processUncoveredFiles`属性を削除する必要があります。次に、アプリケーションの`composer.json`ファイルで、以下の依存関係を更新します。

<div class="content-list" markdown="1">

- `nunomaduro/collision`を`^7.0`
- `phpunit/phpunit`を`^10.0`

</div>

最後に、アプリケーションで使用しているその他のサードパーティパッケージを調べ、Laravel10のサポートに適したバージョンを確実に使用してください。

<a name="updating-minimum-stability"></a>
#### 最低安定度

アプリケーションの`composer.json`ファイルの`minimum-stability`設定を`stable`へ更新する必要があります。もしくは、`minimum-stability`のデフォルト値は`stable`なので、アプリケーションの `composer.json`ファイルからこの設定を削除してください。

```json
"minimum-stability": "stable",
```

### アプリケーション

<a name="public-path-binding"></a>
#### Publicパスの結合

**影響の可能性： 低い**

アプリケーションでコンテナへ`path.public`を結合し、「公開パス」をカスタマイズしている場合は、代わりに `Illuminate\Foundation\Application`オブジェクトが提供する`usePublicPath`メソッドを呼び出すようにコードを更新する必要があります。

```php
app()->usePublicPath(__DIR__.'/public');
```

### 許可

<a name="register-policies"></a>
### `registerPolicies`メソッド

**影響の可能性：　低い**

`AuthServiceProvider`の`registerPolicies`メソッドをフレームワークが自動的に呼び出すようになりました。したがって、アプリケーションの`AuthServiceProvider`の`boot`メソッドから、このメソッドの呼び出しを削除できます。

### キャッシュ

<a name="redis-cache-tags"></a>
#### Redisキャッシュタグ

**影響の可能性： 中程度**

Redisの[キャッシュタグ](/docs/{{version}}/cache#cache-tags)サポートは、パフォーマンスとストレージ効率向上のために書き直しました。以前のLaravelのリリースでは、アプリケーションのキャッシュドライバにRedisを使用すると、古いキャッシュタグがキャッシュに蓄積していました。

そこで、古くなったキャッシュタグエントリーを適切に整理するため、Laravelの新しい`cache:prune-stale-tags` Artisanコマンドをアプリケーションの`App\Console\Kernel`クラスで[定期実行](/docs/{{version}}/scheduling)してください。

    $schedule->command('cache:prune-stale-tags')->hourly();

### データベース

<a name="database-expressions"></a>
#### データベース構文

**影響の可能性： 中程度**

データベースの「構文」（通常は `DB::raw` により生成）は、将来的に追加機能を提供するため、Laravel10.xで書き直しました。特に、構文の素の文字列の値は、構文の`getValue(Grammar $grammar)`メソッドで取得する必要があります。`(string)`を使う構文から文字列へのキャストは、既にサポートしていません。

**通常、これはエンドユーザーのアプリケーションには無影響です。**しかし、もしアプリケーションで`(string)`を使用してデータベース式を文字列へ手作業でキャストしたり、構文に対して`__toString`メソッドを直接呼び出している場合は、代わりに`getValue`メソッドを呼び出すようにコードを更新する必要があります。

```php
use Illuminate\Support\Facades\DB;

$expression = DB::raw('select 1');

$string = $expression->getValue(DB::connection()->getQueryGrammar());
```

<a name="query-exception-constructor"></a>
#### クエリ例外コンストラクタ

**影響の可能性： かなり低い**

`Illuminate\Database\QueryException`のコンストラクターで、文字列の接続名を最初の引数に取るようにしました。アプリケーションがこの例外を手作業で投げている場合は、それに応じてコードを調整する必要があります。

<a name="ulid-columns"></a>
#### ULIDカラム

**影響の可能性： 低い**

マイグレーションで、`ulid`メソッドを引数なしで呼び出すと、カラムは`ulid`という名前になります。Laravelの以前のリリースでは、引数なしでこのメソッドを呼び出すと、誤って`uuid`という名前のカラムが作成されていました。

    $table->ulid();

`ulid`メソッドを呼び出すとき、明示的にカラム名を指定するには、そのカラム名をメソッドへ渡してください。

    $table->ulid('ulid');

### Eloquent

<a name="model-dates-property"></a>
#### モデルの"Dates"プロパティ

**影響の可能性： 中程度**

Eloquentモデルで非推奨になっていた`$dates`プロパティを削除しました。アプリケーションでは、`$casts`プロパティを使用する必要があります。

```php
protected $casts = [
    'deployed_at' => 'datetime',
];
```

<a name="relation-getbasequery-method"></a>
#### リレーションの`getBaseQuery`メソッド

**影響の可能性： かなり低い**

`Illuminate\Database\Eloquent\Relations\Relation`クラスの`getBaseQuery`メソッドを`toBase`へ名称変更しました。

### 多言語化

<a name="language-directory"></a>
#### Languageディレクトリ

**影響の可能性： なし**

既存のアプリケーションには関係ありませんが、Laravelアプリケーションのスケルトンは、`lang`ディレクトリをデフォルトで用意しなくなりました。代わりに、新しいLaravelアプリケーションを書く際には、`lang:publish` Artisanコマンドを使用してリソース公開してください。

```shell
php artisan lang:publish
```

### ログ

<a name="monolog-3"></a>
#### Monolog3

**影響の可能性： 中程度**

LaravelのMonolog依存バージョンをMonolog3.xへ更新しました。アプリケーション内でMonologと直接やりとりする場合は、Monologの[アップグレードガイド](https://github.com/Seldaek/monolog/blob/main/UPGRADE.md)を確認する必要があります。

BugSnagやRollbarなどのサードパーティのログサービスを使用している場合、それらのサードパーティのパッケージをMonolog3.xとLaravel10.xをサポートするバージョンへアップグレードする必要がある場合があります。

### キュー

<a name="dispatch-now"></a>
#### The `Bus::dispatchNow` Method

**影響の可能性： 低い**

非推奨にしていた`Bus::dispatchNow`と`dispatch_now`メソッドを削除しました。代わりに、アプリケーションでは、それぞれ`Bus::dispatchSync`か`dispatch_sync`メソッドを使用する必要があります。

### ルート

<a name="middleware-aliases"></a>
#### ミドルウェアエイリアス

**影響の可能性： 条件による**

新しいLaravelアプリケーションでは、`App\Http\Kernel`クラスの`$routeMiddleware`プロパティは、その目的をよりよく表すために`$middlewareAliases`へ名前を変更しました。既存のアプリケーションでこのプロパティの名前を変更することは歓迎しますが、必須ではありません。

<a name="rate-limiter-return-values"></a>
#### レート制限の戻り値

**影響の可能性： 低い**

`RateLimiter::attempt`メソッドを呼び出すと、指定したクロージャが返す値をメソッドからも返すようにしました。何も返さないか`null`を返すと、`attempt`メソッドは`true`を返します。

```php
$value = RateLimiter::attempt('key', 10, fn () => ['example'], 1);

$value; // ['example']
```

<a name="redirect-home"></a>
#### `Redirect::home`メソッド

**影響の可能性： かなり低い**

非推奨だった`Redirect::home`メソッドを削除しました。代わりに、アプリケーションでは明示的に名付けたルートへリダイレクトする必要があります。

```php
return Redirect::route('home');
```

### テスト

<a name="service-mocking"></a>
#### サービスのモック

**影響の可能性： 中程度**

非推奨だった`MocksApplicationServices`トレイトをフレームワークから削除しました。このトレイトは`expectsEvents`、`expectsJobs`、`expectsNotifications`といったテストメソッドを提供していました。

もしあなたのアプリケーションでこれらのメソッドを使用するなら、それぞれ`Event::fake`、`Bus::fake`、`Notification::fake`へ移行することをお勧めします。フェイクを使ったモックについては、フェイクしようとしているコンポーネントの対応するドキュメントで詳しく説明されています。

### バリデーション

<a name="closure-validation-rule-messages"></a>
#### クロージャバリデーションルールメッセージ

**影響の可能性： かなり低い**

クロージャベースのカスタムバリデーションルールを書くとき、`$fail`コールバックを複数回呼び出すと、前のメッセージを上書きする代わりに、メッセージを配列へ追加するようになりました。通常、これはアプリケーションに影響を与えることはありません。

加えて、`$fail`コールバックはオブジェクトを返すようになりました。これまでバリデーションクロージャの戻り値の型をタイプヒントしていた場合は、タイプヒントを更新する必要があります。

```php
public function rules()
{
    'name' => [
        function ($attribute, $value, $fail) {
            $fail('validation.translation.key')->translate();
        },
    ],
}
```

<a name="miscellaneous"></a>
### その他

`laravel/laravel` [GitHubリポジトリ](https://github.com/laravel/laravel)の変更点をご覧いただくこともお勧めします。これらの変更の多くは必須ではありませんが、これらのファイルをあなたのアプリケーションと同期させておくとよいでしょう。これらの変更のいくつかは、このアップグレードガイドでカバーしていますが、設定ファイルやコメントの変更など、他のものはカバーしていません。

[GitHubのソース比較ツール](https://github.com/laravel/laravel/compare/9.x...10.x)で簡単に変更点を確認し、どのアップデートが自分にとって重要かを選択できます。ただし、GitHubソース比較ツールで表示される変更点の多くは、当オーガニゼーションがPHPネイティブ型を採用したことによるものです。これらの変更は後方互換性があり、Laravel 10への移行時に採用するかは任意です。
