# Laravel Pulse

- [イントロダクション](#introduction)
- [インストール](#installation)
    - [設定](#configuration)
- [ダッシュボード](#dashboard)
    - [認可](#dashboard-authorization)
    - [カスタマイズ](#dashboard-customization)
    - [カード](#dashboard-cards)
- [エンティティのキャプチャ](#capturing-entries)
    - [レコード](#recorders)
    - [フィルタリング](#filtering)
- [パフォーマンス](#performance)
    - [他のデータベースの使用](#using-a-different-database)
    - [Redis統合](#ingest)
    - [サンプリング](#sampling)
    - [トリミング](#trimming)
    - [Pulse例外の処理](#pulse-exceptions)

<a name="introduction"></a>
## イントロダクション

[Laravel Pulse](https://github.com/laravel/pulse)で、アプリケーションのパフォーマンスと使用状況を一目で把握できます。Pulseを使えば、遅いジョブやエンドポイントなどのボトルネックを突き止めたり、最もアクティブなユーザーを見つけたりできます。

個々のイベントの詳細なデバッグには、[Laravel Telescope](/docs/{{version}}/telescope)をチェックしてください。

<a name="installation"></a>
## インストール

> **Warning**
> Pulseのファーストパーティストレージの実装は、現在MySQLかPostgreSQLデータベースを使っています。別のデータベースエンジンを使用している場合は別に、Pulseデータ用のMySQL／PostgreSQLデータベースが必要になります。

Pulseは現在ベータ版なため、ベータリリース版のパッケージをインストールできるように、アプリケーションの`composer.json`ファイルを調整する必要があるでしょう。

```json
"minimum-stability": "beta",
"prefer-stable": true
```

次に、Composerパッケージマネージャを使い、LaravelプロジェクトへPulseをインストールします。

```sh
composer require laravel/pulse
```

次に、`vendor:publish` Artisanコマンドを使用し、Pulse設定ファイルとマイグレーションファイルをリソース公開します。

```shell
php artisan vendor:publish --provider="Laravel\Pulse\PulseServiceProvider"
```

最後に、Pulseのデータを格納するテーブルを作成するため、`migrate`コマンドを実行します。

```shell
php artisan migrate
```

Pulseのデータベースマイグレーションを完了したら、`/pulse`ルートでPulseダッシュボードへアクセスできます。

> **Note**
> パルスデータをアプリケーションのプライマリデータベースに保存したくない場合は、[専用のデータベース接続を指定](#using-a-different-database)できます。

<a name="configuration"></a>
### 設定

Pulseの設定オプションの多くは、環境変数を使い制御できます。利用可能なオプションを確認したり、新しいレコーダを登録したり、高度なオプションを設定したりするには、`config/pulse.php`設定ファイルをリソース公開してください：

```sh
php artisan vendor:publish --tag=pulse-config
```

<a name="dashboard"></a>
## ダッシュボード

<a name="dashboard-authorization"></a>
### 認可

Pulseダッシュボードは、`/pulse`経由でアクセスします。このダッシュボードはデフォルトでアクセスできるのは`local`環境だけなため、`'viewPulse'`認可ゲートをカスタマイズして、本番環境用の許可設定する必要があります。この設定は、アプリケーションの`app/Providers/AuthServiceProvider.php`ファイルで行います。

```php
use App\Models\User;
use Illuminate\Support\Facades\Gate;

/**
 * すべての認証・認可サービスの登録
 */
public function boot(): void
{
    Gate::define('viewPulse', function (User $user) {
        return $user->isAdmin();
    });

    // ...
}
```

<a name="dashboard-customization"></a>
### カスタマイズ

Pulseダッシュボードのカードとレイアウトは、ダッシュボードビューをリソース公開することで設定できます。ダッシュボードビューは、`resources/views/vendor/pulse/dashboard.blade.php`として公開します。

```sh
php artisan vendor:publish --tag=pulse-dashboard
```

ダッシュボードは、[Livewire](https://livewire.laravel.com/)を活用しており、JavaScriptアセットをまったく再構築しなくとも、カードやレイアウトをカスタマイズできます。

このファイルの中では、`<x-pulse>`コンポーネントがダッシュボードのレンダを担当し、カードのグリッドレイアウトを提供しています。ダッシュボードを画面の幅いっぱいに表示したい場合は、`full-width`プロパティをコンポーネントへ指定します。

```blade
<x-pulse full-width>
    ...
</x-pulse>
```

`<x-pulse>`コンポーネントはデフォルトで、１２カラムのグリッドを作成しますが、`cols`プロップを使ってカスタマイズ可能です。

```blade
<x-pulse cols="16">
    ...
</x-pulse>
```

各カードは、`cols`と`rows`プロップを引数に取り、スペースと位置をコントロールできます。

```blade
<livewire:pulse.usage cols="4" rows="2" />
```

ほとんどのカードは、`expand`プロップも引数に取り、スクロールする代わりにカード全体を表示します。

```blade
<livewire:pulse.slow-queries expand />
```

<a name="dashboard-cards"></a>
### カード

<a name="servers-card"></a>
#### サーバ

`<livewire:pulse.servers />`カードは、`pulse:check`コマンドを実行しているすべてのサーバのシステムリソースの使用状況を表示します。システムリソースのレポートについては、[サーバレコーダ](#servers-recorder) のドキュメントを参照してください。

<a name="application-usage-card"></a>
#### Application Usage

`<livewire:pulse.usage />`カードは、アプリケーションへのリクエスト、ジョブのディスパッチ、遅いリクエストを作り出した上位１０ユーザーを表示します。

すべての使用量メトリクスを同時に画面に表示したい場合は、カードを複数インクルードし、`type`属性を指定してください。

```blade
<livewire:pulse.usage type="requests" />
<livewire:pulse.usage type="slow_requests" />
<livewire:pulse.usage type="jobs" />
```

Pulseはデフォルトで、`User`モデルにより`name`フィールドと`email`フィールドを解決し、Gravatarウェブサービスを使用してアバターを表示します。しかし、アプリケーションの`App\Providers\AppServiceProvider`クラス内で`Pulse::users`メソッドを呼び出せば、ユーザー情報の解決と表示をカスタマイズできます。

`users`メソッドはクロージャを引数に取ります。そのクロージャは表示するユーザーIDを引数に受け、各ユーザーIDに対する`id`、`name`、`extra`、`avatar`を含む配列またはコレクションを返してください。

```php
use Laravel\Pulse\Facades\Pulse;

Pulse::users(function ($ids) {
    return User::findMany($ids)->map(fn ($user) => [
        'id' => $user->id,
        'name' => $user->name,
        'extra' => $user->email,
        'avatar' => $user->avatar_url,
    ]);
});
```

> **Note**
> アプリケーションがたくさんのリクエストを受け取ったり、たくさんのジョブをディスパッチしたりする場合は、[サンプリング](#sampling)を有効にするとよいでしょう。詳しくは、[ユーザーリクエストのレコーダ](#user-requests-recorder)、[ユーザージョブのレコーダ](#user-jobs-recorder)、[遅いジョブのレコーダ](#slow-jobs-recorder)のドキュメントを参照してください。

<a name="exceptions-card"></a>
#### 例外

`<livewire:pulse.exceptions />`カードは、アプリケーションで発生した例外の発生頻度と間隔を表示します。デフォルトでは、例外は例外クラスと発生場所に基づいてグループ化されます。詳しくは [例外レコーダ](#exceptions-recorder)のドキュメントを参照してください。

<a name="queues-card"></a>
#### キュー

`<livewire:pulse.queues />`カードは、キュー投入されたジョブ、処理中、処理済み、解放、失敗などの数を含む、アプリケーション内のキューのスループットを表示します。詳細は [キューレコーダ](#queues-recorder) のドキュメントを参照してください。

<a name="slow-requests-card"></a>
#### スローリクエスト

`<livewire:pulse.slow-requests />`カードは、設定したしきい値(デフォルトでは1,000ms)を超えたアプリケーションへのリクエストを表示します。詳しくは、[スローリクエストレコーダ](#slow-requests-recorder)のドキュメントを参照してください。

<a name="slow-jobs-card"></a>
#### スロージョブ

`<livewire:pulse.slow-jobs />`カードは、設定したしきい値(デフォルトでは1,000ms)を超えたアプリケーションのキュー投入したジョブを表示します。詳しくは、[スロージョブレコーダ](#slow-jobs-recorder)のドキュメントを参照してください。

<a name="slow-queries-card"></a>
#### スロークエリ

`<livewire:pulse.slow-queries />`カードは、設定したしきい値（デフォルトでは1,000ms）を超えるアプリケーションのデータベースクエリを表示します。

スロークエリはデフォルトで、ＳＱＬクエリ（バインディングなし）と発生場所に基づいてグループ化されますが、ＳＱＬクエリのみでグループ化したい場合は、発生場所をキャプチャしないこともできます。

詳しくは[スロークエリレコーダ](#slow-queries-recorder)のドキュメントを参照してください。

<a name="slow-outgoing-requests-card"></a>
#### スロー送信リクエスト

`<livewire:pulse.slow-outgoing-requests />`カードは、Laravelの[HTTPクライアント](/docs/{{version}}/http-client)を使用して送信したたリクエストのうち、設定したしきい値（デフォルトでは1,000ms）を超えたものを表示します。

エントリはデフォルトで、完全なURLでグループ化されます。しかし、正規表現を使うことで、似たようなリクエストを正規化したり、グループ化したりできます。詳しくは [スロー送信リクエストレコーダ](#slow-outgoing-requests-recorder)のドキュメントを参照してください。

<a name="cache-card"></a>
#### キャッシュ

`<livewire:pulse.cache />`カードは、アプリケーションのキャッシュのヒットとミスの統計を、グローバルと個々のキーの両方で表示します。

エントリはデフォルトで、キーによりグループ化されます。ただし、正規表現を使用して、類似したキーを正規化またはグループ化できます。詳細は[キャッシュ操作レコーダ](#cache-interactions-recorder)のドキュメントを参照してください。

<a name="capturing-entries"></a>
## エンティティのキャプチャ

ほとんどのPulseレコーダは、Laravelが発行するフレームワークイベントに基づき、自動的にエントリーをキャプチャします。しかし、[サーバレコーダ](#servers-recorder)やいくつかのサードパーティカードは、定期的に情報をポーリングする必要があります。これらのカードを使用するには、個々のアプリケーションサーバ上で`pulse:check`デーモンを実行する必要があります：

```php
php artisan pulse:check
```

> **Note**
> `pulse:check`プロセスをバックグラウンドで永続的に実行し続けるには、Supervisorのようなプロセスモニタを使い、コマンドの実行が止まらないようにする必要があります。

<a name="recorders"></a>
### レコーダ

レコーダは、Pulseデータベースに記録するため、アプリケーションからのエントリをキャプチャする役割を果たします。レコーダは[Pulse設定ファイル](#configuration)の`recorders`セクションで登録・設定します。

<a name="cache-interactions-recorder"></a>
#### キャッシュ操作

`CacheInteractions`レコーダは、アプリケーションで発生した[キャッシュ](/docs/{{version}}/cache)のヒットとミスに関する情報を取得し、[キャッシュ](#cache-card)カードに表示します。

オプションで[サンプルレート](#sampling)と無視するキーのパターンを調整できます。

また、キーのグループ化を設定し、類似のキーを同じエントリとしてグループ化することもできます。例えば、同じタイプの情報をキャッシュするキーから、一意のIDを削除したい場合などです。グループは、キーの一部を「FindしてReplaceする」正規表現を使い設定する。一例は設定ファイルに含まれています。

```php
レコード\CacheInteractions::class => [
    // ...
    'groups' => [
        // '/:\d+/' => ':*',
    ],
],
```

最初にマッチしたパターンを適用します。マッチするパターンがなければ、キーをそのままキャプチャします。

<a name="exceptions-recorder"></a>
#### 例外

`Exceptions`レコーダは、アプリケーションで発生した報告可能(reportable)な例外に関する情報を記録し、[例外](#exceptions-card)カードに表示します。

オプションで[サンプルレート](#sampling)と無視する例外パターンを調整できます。例外が発生した場所をキャプチャするかどうかも設定可能です。キャプチャした場所は、Pulseダッシュボードに表示され、例外の発生元を追跡するのに役立ちます。ただし、同じ例外が複数の場所で発生した場合は、固有の場所ごとに複数回表示します。

<a name="queues-recorder"></a>
#### キュー

`Queues`レコーダーはアプリケーションのキューに関する情報を記録し、[キュー](#queues-card)に表示します。

オプションで[サンプルレート](#sampling)と無視するジョブパターンを調整できます。

<a name="slow-jobs-recorder"></a>
#### スロージョブ

`SlowJobs`レコーダーは、アプリケーションで発生したスロージョブの情報を[スロージョブ](#slow-jobs-recorder)カードに表示するためにキャプチャします。

オプションで、スロージョブのしきい値、[サンプル・レート](#sampling)、無視するジョブのパターンを調整できます。

<a name="slow-outgoing-requests-recorder"></a>
#### スロー送信リクエスト

`SlowOutgoingRequests`レコーダーは、Laravelの[HTTPクライアント](/docs/{{version}}/http-client)を使用して行われた送信HTTPリクエストのうち、設定した閾値を超えたリクエストに関する情報を取得し、[スロー送信リクエスト](#slow-outgoing-requests-card)カードに表示します。

オプションで、スロー送信リクエストのしきい値、[サンプルレート](#sampling)、無視するURLパターンを調整できます。

また、URLのグループ化を設定して、類似のURLを同じエントリとしてグループ化することもできます。例えば、URLパスから一意のIDを削除したり、ドメインのみでグループ化したりすることができます。グループは、URLの一部を「FindしてReplaceする」正規表現を使用して設定します。一例を設定ファイルに用意しています。

```php
レコード\OutgoingRequests::class => [
    // ...
    'groups' => [
        // '#^https://api\.github\.com/repos/.*$#' => 'api.github.com/repos/*',
        // '#^https?://([^/]*).*$#' => '\1',
        // '#/\d+#' => '/*',
    ],
],
```

最初にマッチしたパターンを使用します。マッチするパターンがない場合、URLをそのままキャプチャします。

<a name="slow-queries-recorder"></a>
#### スロークエリ

`SlowQueries`レコーダは、[スロークエリ](#slow-queries-card)カードに表示するため、設定したしきい値を超えるアプリケーションのデータベースクエリをキャプチャします。

オプションで、スロークエリのしきい値、[サンプルレート](#sampling)、無視するクエリパターンを調整できます。また、クエリの場所をキャプチャするかも設定可能です。キャプチャした場所は、Pulseダッシュボードに表示され、クエリの発信元を追跡するのに役立ちますが、同じクエリが複数の場所で行われた場合は、それぞれの場所で複数回表示します。

<a name="slow-requests-recorder"></a>
#### スローリクエスト

`Requests`レコーダは、アプリケーションへのリクエストに関する情報を記録し、[スローリクエスト](#slow-requests-card)カードと[アプリケーションの使用状況](#application-usage-card)カードに表示します。

オプションで、スロールートのしきい値、[サンプルレート](#sampling)、無視するパスを調整できます。

<a name="servers-recorder"></a>
#### サーバ

`Servers`レコーダは、アプリケーションを動かしているサーバのCPU、メモリ、ストレージの使用状況をキャプチャし、[サーバ](#servers-card)カードに表示します。このレコーダを使うには、[`pulse:check`コマンド](#capturing-entries)を監視する各サーバ上で実行する必要があります。

報告する各サーバは、一意な名前を持っていなければなりません。Pulseはデフォルトで、PHPの`gethostname`関数が返す値を使用します。これをカスタマイズしたい場合は、`PULSE_SERVER_NAME`環境変数を設定します。

```env
PULSE_SERVER_NAME=load-balancer
```

Pulse設定ファイルでは、監視するディレクトリをカスタマイズすることもできます。

<a name="user-jobs-recorder"></a>
#### ユーザージョブ

`UserJobs`レコーダは、[アプリケーションの使用状況](#application-usage-card)カードに表示するために、アプリケーションでジョブをディスパッチしているユーザーに関する情報を取得します。

オプションで[サンプルレート](#sampling)と無視するジョブパターンを調整できます。

<a name="user-requests-recorder"></a>
#### ユーザーリクエスト

`UserRequests`レコーダは、あなたのアプリケーションにリクエストをしているユーザーに関する情報を取得し、[アプリケーションの使用状況](#application-usage-card)カードに表示します。

オプションで[サンプルレート](#sampling)と無視するジョブパターンを調整できます。

<a name="filtering"></a>
### フィルタリング

これまで見てきたように、多くの[レコーダ](#recorder)は設定により、リクエストURLのような値に基づき、受信エントリを「無視」する機能を提供しています。しかし、時には他の要素、例えば現在認証済みのユーザーに基づいてレコードをフィルタリングすることが役立つこともあるでしょう。そのようにレコードをフィルタリングするには、Pulseの`filter`メソッドへクロージャを渡します。通常、`filter`メソッドはアプリケーションの`AppServiceProvider`の`boot`メソッド内で呼び出す必要があります。

```php
use Illuminate\Support\Facades\Auth;
use Laravel\Pulse\Entry;
use Laravel\Pulse\Facades\Pulse;
use Laravel\Pulse\Value;

/**
 * 全アプリケーションサービスの初期起動処理
 */
public function boot(): void
{
    Pulse::filter(function (Entry|Value $entry) {
        return Auth::user()->isNotAdmin();
    });

    // ...
}
```

<a name="performance"></a>
## パフォーマンス

Pulseはインフラを追加することなく、既存のアプリケーションに組み込めるように設計しています。しかし、トラフィックの多いアプリケーションでは、Pulseがアプリケーションのパフォーマンスに与える影響を取り除く方法がいくつかあります。

<a name="using-a-different-database"></a>
### 他のデータベースの使用

トラフィックの多いアプリケーションでは、アプリケーション・データベースへの影響を避けるため、Pulse専用データベース接続を使用を望まれるでしょう。

`PULSE_DB_CONNECTION`環境変数を設定すれば、Pulseで使用する[データベース接続](/docs/{{version}}/database#configuration)をカスタマイズできます。

```env
PULSE_DB_CONNECTION=pulse
```

<a name="ingest"></a>
### Redis統合

> **Warning**
> Redisを使用するには、Redis6.2以上と、アプリケーションの設定済みRedisクライアントドライバに、`phpredis`または`predis`が必要です。

Pulseはデフォルトで、HTTPレスポンスがクライアントに送信した後、またはジョブを処理した後に、[設定されたデータベース接続](#using-a-different-database)に直接エントリを保存します。しかし、PulseのRedisインジェストドライバを代わりに使用し、Redisストリームへエントリを送信することもできます。これは`PULSE_INGEST_DRIVER`環境変数を設定すると有効になります。

```
PULSE_INGEST_DRIVER=redis
```

Pulseはデフォルトの[Redis接続](/docs/{{version}}/redis#configuration)を使いますが、`PULSE_REDIS_CONNECTION`環境変数を使い、カスタマイズすることもできます。

```
PULSE_REDIS_CONNECTION=pulse
```

Redisインジェストを使用する場合は、`pulse:work`コマンドを実行する必要があります。ストリームを監視し、RedisからPulseのデータベーステーブルへエントリを移動させるためにです。

```php
php artisan pulse:work
```

> **Note**
> `pulse:work`プロセスをバックグラウンドで永久に実行し続けるには、Supervisorのようなプロセスモニタを使い、Pulseワーカの実行を止めないようにする必要があります。

<a name="sampling"></a>
### サンプリング

Pulseはデフォルトで、アプリケーションで発生するすべての関連イベントをキャプチャします。トラフィックの多いアプリケーションの場合、特に長期間では、ダッシュボードに何百万ものデータベース行を集約する必要が生じる可能性があります。

その代わりに、特定のPulseデータレコーダでは「サンプリング」を有効にできます。例えば、[`ユーザーリクエスト`](#user-requests-recorder)レコーダのサンプルレートを`0.1`に設定すると、アプリケーションへのリクエストの約10%だけを記録することになります。ダッシュボードでは、値はスケールアップされ、近似値であることを示すために、`~`を先頭に付けています。

一般的に、特定のメトリックのエントリが多いほど、精度を犠牲にすることなく、サンプルレートを低く設定できます。

<a name="trimming"></a>
### トリミング

一度ダッシュボードのウィンドウの外に出ると、Pulseは保存したエントリーを自動的にトリミングします。トリミングは、Pulseの[設定ファイル](#configuration)でカスタマイズできる抽選システムを使ってデータを取り込むときに行われます。

<a name="pulse-exceptions"></a>
### Pulse例外の処理

保存域のデータベースに接続できないなど、Pulseデータの取り込み中に例外が発生した場合、Pulseはアプリケーションに影響を与えないように静かに失敗します。

例外の処理方法をカスタマイズしたい場合は、`handleExceptionsUsing`メソッドにクロージャを指定してください。

```php
use Laravel\Pulse\Facades\Pulse;
use Illuminate\Support\Facades\Log;

Pulse::handleExceptionsUsing(function ($e) {
    Log::debug('An exception happened in Pulse', [
        'message' => $e->getMessage(),
        'stack' => $e->getTraceAsString(),
    ]);
});
```
