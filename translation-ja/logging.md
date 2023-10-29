# ログ

- [イントロダクション](#introduction)
- [設定](#configuration)
    - [利用可能なチャンネルドライバ](#available-channel-drivers)
    - [チャンネルの事前設定](#channel-prerequisites)
    - [廃止ワーニングのログ](#logging-deprecation-warnings)
- [ログスタックの構築](#building-log-stacks)
- [ログメッセージの書き込み](#writing-log-messages)
    - [コンテキスト情報](#contextual-information)
    - [特定チャンネルへの書き込み](#writing-to-specific-channels)
- [monologチャンネルカスタマイズ](#monolog-channel-customization)
    - [チャンネル向けMonologカスタマイズ](#customizing-monolog-for-channels)
    - [Monolog処理チャンネルの作成](#creating-monolog-handler-channels)
    - [ファクトリによるカスタムチャンネルの生成](#creating-custom-channels-via-factories)

<a name="introduction"></a>
## イントロダクション

Laravelは、アプリケーション内で何が起こっているかを詳しく知らせるために、メッセージをファイル、システムエラーログ、さらにはSlackに記録してチーム全体に通知できる堅牢なログサービスを提供します。

Laravelのログは「チャンネル」に基づいています。各チャンネルは、ログ情報を書き込む特定の方法を表します。たとえば、`single`チャンネルはログファイルを単一のログファイルに書き込みますが、`slack`チャンネルはログメッセージをSlackへ送信します。ログメッセージは、重大度に基づいて複数のチャンネルに書き込まれる場合があります。

内部的にLaravelは[Monolog](https://github.com/Seldaek/monolog)ライブラリを利用しており、強力なログハンドラを多種に渡りサポートしています。Laravelでは、こうしたハンドラを簡単に設定できるため、ハンドラを組み合わせてアプリケーションのログ処理をカスタマイズできます。

<a name="configuration"></a>
## 設定

アプリケーションのログ動作のすべての設定オプションは、`config/logging.php`設定ファイルであります。このファイルでアプリケーションのログチャンネルを設定しているため、使用可能な各チャンネルとそのオプションを必ず確認してください。以下から、いくつかの一般的なオプションを確認していきます。

Laravelはメッセージをログに記録するときに、デフォルトで`stack`チャンネルを使用します。`stack`チャンネルは、複数のログチャンネルを単一のチャンネルに集約するために使用します。スタックの構築の詳細については、[以降のドキュメント](#building-log-stacks)を確認してください。

<a name="configuring-the-channel-name"></a>
#### チャンネル名の設定

デフォルトでMonologを`production`や`local`など、現在の環境に一致する「チャンネル名」でインスタンス化します。この値を変更するには、チャンネルの構成に`name`オプションを追加します。

    'stack' => [
        'driver' => 'stack',
        'name' => 'channel-name',
        'channels' => ['single', 'slack'],
    ],

<a name="available-channel-drivers"></a>
### 利用可能なチャンネルドライバ

各ログチャンネルは「ドライバ」によって駆動されます。ドライバは、ログメッセージが実際に記録される方法と場所を決定します。以下のログチャンネルドライバは、すべてのLaravelアプリケーションで利用できます。これらのドライバのほとんどのエントリは、アプリケーションの`config/logging.php`設定ファイルに予め用意しているため、このファイルを確認して内容をよく理解してください。

<div class="overflow-auto">

名前 | 説明
------------- | -------------
`custom` | 指定ファクトリを呼び出してチャンネルを作成するドライバ
`daily` | 日毎にファイルを切り替える`RotatingFileHandler`ベースのMonologドライバ
`errorlog` | `ErrorLogHandler`ベースのMonologドライバ
`monolog` | Monologがサポートしているハンドラを使用するMonologファクトリドライバ
`papertrail` | `SyslogUdpHandler`ベースのMonologドライバ
`single` | 単一のファイルまたはパスベースのロガーチャンネル(`StreamHandler`)
`slack` | `SlackWebhookHandler`ベースのMonologドライバ
`stack` | 「マルチチャンネル」チャンネルの作成を容易にするラッパー
`syslog` | `SyslogHandler`ベースのMonologドライバ

</div>

> **Note**
> [高度なチャンネルのカスタマイズ](#monolog-channel-customization)のドキュメントをチェックして、`monolog`および`custom`ドライバの詳細を確認してください。

<a name="channel-prerequisites"></a>
### チャンネルの事前設定

<a name="configuring-the-single-and-daily-channels"></a>
#### singleチャンネルとdailyチャンネルの設定

`single`チャンネルと`daily`チャンネルは、`bubble`、`permission`、`locking`の３オプションの設定オプションがあります。

<div class="overflow-auto">

名前 | 説明 | デフォルト
------------- | ------------- | -------------
`bubble` | メッセージが処理された後、他のチャンネルにバブルアップする必要があるかを示す | `true`
`locking` | ログファイルに書き込む前に、ログファイルのロックを試みるかを示す | `false`
`permission` | ログファイルのパーミッション | `0644`

</div>

さらに、`daily`チャンネルの保存ポリシーは、`days`オプションで設定できます。

<div class="overflow-auto">

名前 | 説明 | デフォルト
------------- | ------------- | -------------
`days` | デイリーログファイルを保持する日数 | `7`

</div>

<a name="configuring-the-papertrail-channel"></a>
#### Papertrailチャンネルの設定

`papertrail`チャンネルには、`host`および`port`設定オプションが必要です。これらの値は、[Papertrail](https://help.papertrailapp.com/kb/configuration/configuring-centralized-logging-from-php-apps/#send-events-from-php-app)から取得できます。

<a name="configuring-the-slack-channel"></a>
#### Slackチャンネルの設定

`slack`チャンネルには`url`設定オプションが必要です。このURLは、皆さんのSlackチーム用に設定した[受信Webフックの](https://slack.com/apps/A0F7XDUAZ-incoming-webhooks)のURLと一致する必要があります。

デフォルトでは、Slackは`critical`レベル以上のログのみ受信します。ただし`config/logging.php`設定ファイルで、Slackログチャンネルの設定配列内の`level`設定オプションを変更することで調整できます。

<a name="logging-deprecation-warnings"></a>
### 廃止ワーニングのログ

PHPやLaravelなどのライブラリは、その機能の一部が非推奨となり、将来のバージョンで削除されることをユーザーに通知することがよくあります。これらの非推奨の警告をログに残したい場合は、アプリケーションの`config/logging.php`設定ファイルで、お好きな`deprecations`ログチャンネルを指定できます。

    'deprecations' => env('LOG_DEPRECATIONS_CHANNEL', 'null'),

    'channels' => [
        ...
    ]

あるいは、`deprecations`という名前のログチャンネルを定義することもできます。この名前のログチャンネルが存在する場合、常にdeprecationsのログを記録するために使用されます。

    'channels' => [
        'deprecations' => [
            'driver' => 'single',
            'path' => storage_path('logs/php-deprecation-warnings.log'),
        ],
    ],

<a name="building-log-stacks"></a>
## ログスタックの構築

前述のように、`stack`ドライバを使用すると、便利に複数のチャンネルを１つのログチャンネルに組み合わせることができます。ログスタックの使用方法を説明するために、本番アプリケーションで使われる可能性のある構成例を見てみましょう。

    'channels' => [
        'stack' => [
            'driver' => 'stack',
            'channels' => ['syslog', 'slack'],
        ],

        'syslog' => [
            'driver' => 'syslog',
            'level' => 'debug',
        ],

        'slack' => [
            'driver' => 'slack',
            'url' => env('LOG_SLACK_WEBHOOK_URL'),
            'username' => 'Laravel Log',
            'emoji' => ':boom:',
            'level' => 'critical',
        ],
    ],

この構成を分析してみましょう。まず、`stack`チャンネルが`channels`オプションを介して他の2つのチャンネル`syslog`と`slack`を集約している点に注目してください。したがって、メッセージをログに記録するとき、これらのチャンネルの両方にメッセージをログに記録する機会があります。ただし、以降で説明するように、これらのチャンネルが実際にメッセージをログに記録するかどうかは、メッセージの重大度／「レベル」によって決定されます。

<a name="log-levels"></a>
#### ログレベル

上記の例の`syslog`および`slack`チャンネル設定に存在する`level`設定オプションに注意してください。このオプションは、チャンネルによってログに記録されるためにメッセージが必要とする最小の「レベル」を決定します。Laravelのログサービスを強化するMonologは、[RFC5424仕様](https://tools.ietf.org/html/rfc5424)で定義されているすべてのログレベルを提供しています。これらのログレベルは、重要度の高い順に、**emergency**、**alert**、**critical**、**error**、**warning**、**notice**、**info**、**debug**です。

では、`debug`メソッドを使用してメッセージをログに記録してみましょう。

    Log::debug('An informational message.');

前記の設定により、`syslog`チャンネルはメッセージをシステムログに書き込みます。ただし、エラーメッセージは`critical`以上ではないため、Slackには送信されません。ただし、`emergency`メッセージをログに記録すると、`emergency`レベルが両方のチャンネルの最小レベルしきい値を超えるため、システムログとSlackの両方に送信されます。

    Log::emergency('The system is down!');

<a name="writing-log-messages"></a>
## ログメッセージの書き込み

`Log`[ファサード](/docs/{{version}}/facades)を使用してログに情報を書き込むことができます。前述のように、ロガーは[RFC5424仕様](https://tools.ietf.org/html/rfc5424)で定義されている8つのログレベルを提供します**emergency**、**alert**、**critical**、**error**、**warning**、**notice**、**info**、**debug**）

    use Illuminate\Support\Facades\Log;

    Log::emergency($message);
    Log::alert($message);
    Log::critical($message);
    Log::error($message);
    Log::warning($message);
    Log::notice($message);
    Log::info($message);
    Log::debug($message);

これらのメソッドのいずれかを呼び出して、対応するレベルのメッセージをログに記録できます。デフォルトでは、メッセージは`logging`設定ファイル中に設定しているデフォルトのログチャンネルに書き込まれます。

    <?php

    namespace App\Http\Controllers;

    use App\Http\Controllers\Controller;
    use App\Models\User;
    use Illuminate\Support\Facades\Log;
    use Illuminate\View\View;

    class UserController extends Controller
    {
        /**
         * 特定のユーザーのプロファイルの表示
         */
        public function show(string $id): View
        {
            Log::info('Showing the user profile for user: {id}', ['id' => $id]);

            return view('user.profile', [
                'user' => User::findOrFail($id)
            ]);
        }
    }

<a name="contextual-information"></a>
### コンテキスト情報

コンテキストデータの配列をlogメソッドへ渡せます。このコンテキストデータはフォーマットされ、ログメッセージとともに表示されます。

    use Illuminate\Support\Facades\Log;

    Log::info('User {id} failed to login.', ['id' => $user->id]);

特定のチャンネルで、後に続くすべてのログエントリに含まれるコンテキスト情報を指定したい場合もあるでしょう。例えば、アプリケーションに入ってくる各リクエストに関連付けたリクエストIDをログに記録したい場合があります。これを行うには、`Log`ファサードの`withContext`メソッドを呼び出します。

    <?php

    namespace App\Http\Middleware;

    use Closure;
    use Illuminate\Http\Request;
    use Illuminate\Support\Facades\Log;
    use Illuminate\Support\Str;
    use Symfony\Component\HttpFoundation\Response;

    class AssignRequestId
    {
        /**
         * 受信リクエストの処理
         *
         * @param  \Closure(\Illuminate\Http\Request): (\Symfony\Component\HttpFoundation\Response)  $next
         */
        public function handle(Request $request, Closure $next): Response
        {
            $requestId = (string) Str::uuid();

            Log::withContext([
                'request-id' => $requestId
            ]);

            $response = $next($request);

            $response->headers->set('Request-Id', $requestId);

            return $response;
        }
    }

もし、*すべて*のログ収集チャンネルでコンテキスト情報を共有したい場合は、`Log::shareContext()`メソッドを呼び出してください。このメソッドは、作成したすべてのチャンネルと、以降に作成するすべてのチャンネルへ、コンテキスト情報を提供します。通常、`shareContext`メソッドは、アプリケーションサービスプロバイダの`boot`メソッドから呼び出すべきでしょう。

    use Illuminate\Support\Facades\Log;
    use Illuminate\Support\Str;

    class AppServiceProvider
    {
        /**
         * 全アプリケーションサービスの初期起動処理
         */
        public function boot(): void
        {
            Log::shareContext([
                'invocation-id' => (string) Str::uuid(),
            ]);
        }
    }

<a name="writing-to-specific-channels"></a>
### 特定チャンネルへの書き込み

アプリケーションのデフォルトチャンネル以外のチャンネルにメッセージを記録したいことも起きるでしょう。`Log`ファサードの`channel`メソッドを使用して、設定ファイルで定義している任意のチャンネルを取得し、ログへ記録できます。

    use Illuminate\Support\Facades\Log;

    Log::channel('slack')->info('Something happened!');

複数のチャンネルで構成されるログスタックをオンデマンドで作成する場合は、`stack`メソッドを使用できます。

    Log::stack(['single', 'slack'])->info('Something happened!');

<a name="on-demand-channels"></a>
#### オンデマンドチャンネル

アプリケーションの `logging` 設定ファイルに設定を用意しなくても、実行時に構成を指定することにより、オンデマンドチャンネルを作成することも可能です。そのためには、設定の配列を`Log`ファサードの`build`メソッドに渡してください。

    use Illuminate\Support\Facades\Log;

    Log::build([
      'driver' => 'single',
      'path' => storage_path('logs/custom.log'),
    ])->info('Something happened!');

オンデマンドチャンネルをオンデマンドログスタックに含めたい場合もあるでしょう。これを実現するには、`stack`メソッドに渡す配列へオンデマンドチャンネルのインスタンスを含めます。

    use Illuminate\Support\Facades\Log;

    $channel = Log::build([
      'driver' => 'single',
      'path' => storage_path('logs/custom.log'),
    ]);

    Log::stack(['slack', $channel])->info('Something happened!');

<a name="monolog-channel-customization"></a>
## monologチャンネルカスタマイズ

<a name="customizing-monolog-for-channels"></a>
### チャンネル向けMonologカスタマイズ

場合によっては、既存のチャンネルに対してMonologを設定する方法を完全に制御する必要が起きます。たとえば、Laravelの組み込みの`single`チャンネル用にカスタムMonolog `FormatterInterface`実装を設定したい場合です。

このためには、チャンネルの設定で`tap`配列を定義します。`tap`配列には、Monologインスタンスの作成後にカスタマイズする(またはtap into：入れ込む)機会が必要なクラスのリストを含める必要があります。これらのクラスを配置する決まった場所はないため、アプリケーション内にこれらのクラスを含むディレクトリを自由に作成できます。

    'single' => [
        'driver' => 'single',
        'tap' => [App\Logging\CustomizeFormatter::class],
        'path' => storage_path('logs/laravel.log'),
        'level' => 'debug',
    ],

チャンネルで`tap`オプションを設定したら、Monologインスタンスをカスタマイズするクラスを定義する準備が整います。このクラスに必要なメソッドは1つだけです。`__invoke`は`Illuminate\Log\Logger`インスタンスを受け取ります。`Illuminate\Log\Logger`インスタンスは、基礎となるMonologインスタンスへのすべてのメソッド呼び出しをプロキシします。

    <?php

    namespace App\Logging;

    use Illuminate\Log\Logger;
    use Monolog\Formatter\LineFormatter;

    class CustomizeFormatter
    {
        /**
         * 指定するロガーインスタンスをカスタマイズ
         */
        public function __invoke(Logger $logger): void
        {
            foreach ($logger->getHandlers() as $handler) {
                $handler->setFormatter(new LineFormatter(
                    '[%datetime%] %channel%.%level_name%: %message% %context% %extra%'
                ));
            }
        }
    }

> **Note**
> すべての「tap」クラスは[サービスコンテナ](/docs/{{version}}/container)によって解決されるため、必要なコンストラクターの依存関係は自動的に依存注入されます。

<a name="creating-monolog-handler-channels"></a>
### Monolog処理チャンネルの作成

Monologにはさまざまな[利用可能なハンドラ](https://github.com/Seldaek/monolog/tree/main/src/Monolog/Handler)があり、Laravelはそれぞれに対する組み込みチャンネルを用意していません。場合によっては、対応するLaravelログドライバを持たない特定のMonologハンドラの単なるインスタンスであるカスタムチャンネルを作成したい場合があります。これらのチャンネルは、`monolog`ドライバを使用して簡単に作成できます。

`monolog`ドライバを使用する場合、`handler`設定オプションを使用してインスタンス化するハンドラを指定します。オプションで、ハンドラが必要とするコンストラクターパラメーターは、`with`設定オプションを使用して指定できます。

    'logentries' => [
        'driver'  => 'monolog',
        'handler' => Monolog\Handler\SyslogUdpHandler::class,
        'with' => [
            'host' => 'my.logentries.internal.datahubhost.company.com',
            'port' => '10000',
        ],
    ],

<a name="monolog-formatters"></a>
#### Monologフォーマッター

`monolog`ドライバを使用する場合、Monolog`LineFormatter`がデフォルトのフォーマッターとして使用されます。ただし、`formatter`および`formatter_with`設定オプションを使用して、ハンドラへ渡すフォーマッタータイプをカスタマイズできます。

    'browser' => [
        'driver' => 'monolog',
        'handler' => Monolog\Handler\BrowserConsoleHandler::class,
        'formatter' => Monolog\Formatter\HtmlFormatter::class,
        'formatter_with' => [
            'dateFormat' => 'Y-m-d',
        ],
    ],

独自のフォーマッターを提供できるMonologハンドラを使用している場合は、`formatter`構成オプションの値を`default`に設定できます。

    'newrelic' => [
        'driver' => 'monolog',
        'handler' => Monolog\Handler\NewRelicHandler::class,
        'formatter' => 'default',
    ],


 <a name="monolog-processors"></a>
 #### Monologプロセッサ

Monologは、メッセージをログに記録する前に処理することもできます。独自のプロセッサを作成したり、[Monologが提供する既存のプロセッサ](https://github.com/Seldaek/monolog/tree/main/src/Monolog/Processor)を使用したりできます。

`monolog`ドライバのプロセッサをカスタマイズしたい場合は、チャネルの設定へ`processors`設定値を追加してください。

     'memory' => [
         'driver' => 'monolog',
         'handler' => Monolog\Handler\StreamHandler::class,
         'with' => [
             'stream' => 'php://stderr',
         ],
         'processors' => [
             // シンプルな記法
             Monolog\Processor\MemoryUsageProcessor::class,

             // 使用するオプション
             [
                'processor' => Monolog\Processor\PsrLogMessageProcessor::class,
                'with' => ['removeUsedContextFields' => true],
            ],
         ],
     ],


<a name="creating-custom-channels-via-factories"></a>
### ファクトリによるカスタムチャンネルの生成

Monologのインスタンス化と設定を完全に制御する、完全なカスタムチャンネルを定義する場合は、`config/logging.php`設定ファイルで`custom`ドライバタイプを指定します。設定には、Monologインスタンスを作成するために呼び出すファクトリクラスの名前を含む`via`オプションを含める必要があります。

    'channels' => [
        'example-custom-channel' => [
            'driver' => 'custom',
            'via' => App\Logging\CreateCustomLogger::class,
        ],
    ],

`custom`ドライバチャンネルを設定したら、Monologインスタンスを作成するクラスを定義する準備が整います。このクラスには、Monologロガーインスタンスを返す単一の`__invoke`メソッドのみが必要です。このメソッドは、チャンネル設定配列を唯一の引数として受け取ります。

    <?php

    namespace App\Logging;

    use Monolog\Logger;

    class CreateCustomLogger
    {
        /**
         * カスタムMonologインスタンスの生成
         */
        public function __invoke(array $config): Logger
        {
            return new Logger(/* ... */);
        }
    }
