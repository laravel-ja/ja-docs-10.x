# ブロードキャスト

- [イントロダクション](#introduction)
- [サーバ側インストール](#server-side-installation)
    - [設定](#configuration)
    - [Pusherチャンネル](#pusher-channels)
    - [Ably](#ably)
    - [オープンソースの代替](#open-source-alternatives)
- [クライアント側インストール](#client-side-installation)
    - [Pusherチャンネル](#client-pusher-channels)
    - [Ably](#client-ably)
- [概論](#concept-overview)
    - [サンプルアプリケーションの使用](#using-example-application)
- [ブロードキャストイベントの定義](#defining-broadcast-events)
    - [ブロードキャスト名](#broadcast-name)
    - [ブロードキャストデータ](#broadcast-data)
    - [ブロードキャストキュー](#broadcast-queue)
    - [ブロードキャスト条件](#broadcast-conditions)
    - [ブロードキャストとデータベーストランザクション](#broadcasting-and-database-transactions)
- [チャンネルの認可](#authorizing-channels)
    - [認可ルートの定義](#defining-authorization-routes)
    - [認可コールバックの定義](#defining-authorization-callbacks)
    - [チャンネルクラスの定義](#defining-channel-classes)
- [ブロードキャストイベント](#broadcasting-events)
    - [他の人だけへの送信](#only-to-others)
    - [コネクションのカスタマイズ](#customizing-the-connection)
- [ブロードキャストの受け取り](#receiving-broadcasts)
    - [イベントのリッスン](#listening-for-events)
    - [チャンネルの離脱](#leaving-a-channel)
    - [名前空間](#namespaces)
- [プレゼンスチャンネル](#presence-channels)
    - [プレゼンスチャンネルの認可](#authorizing-presence-channels)
    - [プレゼンスチャンネルへの接続](#joining-presence-channels)
    - [プレゼンスチャンネルへのブロードキャスト](#broadcasting-to-presence-channels)
- [モデルブロードキャスト](#model-broadcasting)
    - [モデルブロードキャスト規約](#model-broadcasting-conventions)
    - [モデルブロードキャストのリッスン](#listening-for-model-broadcasts)
- [クライアントイベント](#client-events)
- [通知](#notifications)

<a name="introduction"></a>
## イントロダクション

最近の多くのWebアプリケーションでは、WebSocketを使用して、リアルタイムのライブ更新ユーザーインターフェイスを実装しています。サーバ上で一部のデータが更新されると、通常、メッセージはWebSocket接続を介して送信され、クライアントによって処理されます。WebSocketは、UIに反映する必要のあるデータの変更をアプリケーションのサーバから継続的にポーリングするよりも、効率的な代替手段を提供しています。

たとえば、アプリケーションがユーザーのデータをCSVファイルにエクスポートして電子メールで送信できると想像してください。ただ、このCSVファイルの作成には数分かかるため、[キュー投入するジョブ](/docs/{{version}}/queues)内でCSVを作成し、メールで送信することを選択したとしましょう。CSVを作成しユーザーにメール送信したら、イベントブロードキャストを使用して、アプリケーションのJavaScriptで受信する`App\Events\UserDataExported`イベントをディスパッチできます。イベントを受信したら、ページを更新することなく、CSVをメールで送信済みであるメッセージをユーザーへ表示できます。

こうした機能の構築を支援するため、LaravelはWebSocket接続を介してサーバ側のLaravel[イベント](/docs/{{version}}/events)を簡単に「ブロードキャスト」できます。Laravelイベントをブロードキャストすると、サーバ側のLaravelアプリケーションとクライアント側のJavaScriptアプリケーション間で同じイベント名とデータを共有できます。

ブロードキャストの基本的なコンセプトは簡単で、クライアントはフロントエンドで指定したチャンネルへ接続し、Laravelアプリケーションはバックエンドでこれらのチャンネルに対し、イベントをブロードキャストします。こうしたイベントには、フロントエンドで利用する追加データを含められます。

<a name="supported-drivers"></a>
#### サポートしているドライバ

Laravelはデフォルトで、[Pusherチャンネル](https://pusher.com/channels)と[Ably](https://ably.com)、２つのサーバ側ブロードキャストドライバを用意しています。ただし、[laravel-websockets](https://beyondco.de/docs/laravel-websockets/getting-started/introduction)や[soketi](https://docs.soketi.app/)など、コミュニティ主導のパッケージでは、商用ブロードキャストプロバイダを必要としないドライバを提供しています。

> **Note**
> イベントブロードキャストに取り掛かる前に、[イベントとリスナ](/docs/{{version}}/events)に関するLaravelのドキュメントをしっかりと読んでください。

<a name="server-side-installation"></a>
## サーバ側インストール

Laravelのイベントブロードキャストの使用を開始するには、Laravelアプリケーション内でいくつかの設定を行い、いくつかのパッケージをインストールする必要があります。

イベントブロードキャストは、Laravel Echo(JavaScriptライブラリ)がブラウザクライアント内でイベントを受信できるように、Laravelイベントをブロードキャストするサーバ側ブロードキャストドライバによって実行されます。心配いりません。以降から、インストール手順の各部分を段階的に説明します。

<a name="configuration"></a>
### 設定

アプリケーションのイベントブロードキャスト設定はすべて、`config/broadcasting.php`設定ファイルに保存します。Laravelは、すぐに使用できるブロードキャストドライバをいくつかサポートしています。[Pusherチャンネル](https://pusher.com/channels)、[Redis](/docs/{{version}}/redis)、およびローカルでの開発とデバッグ用の`log`ドライバです。さらに、テスト中にブロードキャストを完全に無効にできる`null`ドライバも用意しています。これら各ドライバの設定例は、`config/broadcasting.php`設定ファイルにあります。

<a name="broadcast-service-provider"></a>
#### ブロードキャストサービスプロバイダ

イベントをブロードキャストする前に、まず`App\Providers\BroadcastServiceProvider`を登録する必要があります。新しいLaravelアプリケーションでは、`config/app.php`設定ファイルの`providers`配列で、このプロバイダをアンコメントするだけです。この`BroadcastServiceProvider`には、ブロードキャスト認可ルートとコールバックを登録するために必要なコードが含まれています。

<a name="queue-configuration"></a>
#### キュー設定

また、[キューワーカ](/docs/{{version}}/queues)を設定して実行する必要があります。すべてのイベントブロードキャストはジョブをキュー投入し実行されるため、アプリケーションの応答時間は、ブロードキャストするイベントによる深刻な影響を受けません。

<a name="pusher-channels"></a>
### Pusherチャンネル

[Pusherチャンネル](https://pusher.com/channels)を使用してイベントをブロードキャストする場合は、Composerパッケージマネージャを使用してPusher Channels PHP SDKをインストールする必要があります。

```shell
composer require pusher/pusher-php-server
```

次に、`config/broadcasting.php`設定ファイルでPusherチャンネルの利用資格情報を設定する必要があります。Pusherチャンネル設定の例は予めこのファイルに含まれているため、キー、シークレット、およびアプリケーションIDを簡単に指定できます。通常、これらの値は、`PUSHER_APP_KEY`、`PUSHER_APP_SECRET`、`PUSHER_APP_ID`[環境変数](/docs/{{version}}/configuration#environment-configuration)を介して設定する必要があります。

```ini
PUSHER_APP_ID=your-pusher-app-id
PUSHER_APP_KEY=your-pusher-key
PUSHER_APP_SECRET=your-pusher-secret
PUSHER_APP_CLUSTER=mt1
```

`config/broadcasting.php`ファイルの`pusher`設定では、クラスターなどチャンネルでサポートされている追加の`options`を指定することもできます。

次に、`.env`ファイルでブロードキャストドライバを`pusher`に変更する必要があります。

```ini
BROADCAST_DRIVER=pusher
```

これで、クライアント側でブロードキャストイベントを受信する[Laravel Echo](#client-side-installation)をインストールして設定する準備が整いました。

<a name="pusher-compatible-open-source-alternatives"></a>
#### オープンソースによるPusherの代替

[laravel-websockets](https://github.com/beyondcode/laravel-websockets)と[soketi](https://docs.soketi.app/)パッケージは、Laravel用のPusher互換WebSocketサーバを提供します。これらのパッケージを使用することにより、商用WebSocketプロバイダーを使わずとも、Laravelブロードキャストの機能をフルに活用できます。これらのパッケージのインストールと使用に関する詳細は、[オープンソース代替](#open-source-alternatives)のドキュメントを参照してください。

<a name="ably"></a>
### Ably

> **Note**
> 以下のドキュメントでは、Ablyを「Pusher互換」モードで使用する方法について説明していきます。しかし、Ablyチームは、Ablyが提供するユニークな機能を活用できるブロードキャスターとEchoクライアントを推奨し、保守しています。Ablyが保守するドライバの使用に関する詳細については、[AblyのLaravelブロードキャスターのドキュメントを参照](https://github.com/ably/laravel-broadcaster)してください。

[Ably](https://ably.com)を使用してイベントをブロードキャストする場合は、Composerパッケージマネージャを使用してAbly PHP SDKをインストールする必要があります。

```shell
composer require ably/ably-php
```

次に、`config/broadcasting.php`設定ファイルでAblyの接続資格情報を設定する必要があります。Ablyの設定例はすでにこのファイルに用意されているため、キーを手軽に指定できます。通常、この値は`ABLY_KEY`[環境変数](/docs/{{version}}/configuration#environment-configuration)により設定する必要があります。

```ini
ABLY_KEY=your-ably-key
```

次に、`.env`ファイルでブロードキャストドライバを`ably`に変更する必要があります。

```ini
BROADCAST_DRIVER=ably
```

これで、クライアント側でブロードキャストイベントを受信する[Laravel Echo](#client-side-installation)をインストールして設定する準備が整いました。

<a name="open-source-alternatives"></a>
### オープンソースの代替

<a name="open-source-alternatives-php"></a>
#### PHP

[laravel-websockets](https://github.com/beyondcode/laravel-websockets)パッケージは、PHP製のLaravel用Pusher互換WebSocketパッケージです。このパッケージを使用すると、商用WebSocketプロバイダを使用せずとも、Laravelブロードキャストの全機能を活用できます。このパッケージのインストールと使用の詳細は、[公式ドキュメント](https://beyondco.de/docs/laravel-websockets)を参照してください。

<a name="open-source-alternatives-node"></a>
#### Node

[Soketi](https://github.com/soketi/soketi)は、NodeベースでPusher互換のLaravel用WebSocketサーバです。Soketiの内部では、µWebSockets.jsを利用し、極めて高いスケーラビリティとスピードを実現しています。このパッケージにより、商用WebSocketプロバイダを使用せずにLaravelブロードキャスティングの能力をフルに活用することができます。このパッケージのインストールと使用に関する詳細は、[公式ドキュメント](https://docs.soketi.app/)を参照してください。

<a name="client-side-installation"></a>
## クライアント側インストール

<a name="client-pusher-channels"></a>
### Pusherチャンネル

[Laravel Echo](https://github.com/laravel/echo)はJavaScriptライブラリであり、チャンネルをサブスクライブし、サーバ側のブロードキャストドライバがブロードキャストしたイベントを簡単にリッスンできます。NPMパッケージマネージャを介してEchoをインストールします。以下の例では、Pusher Channelsブロードキャスタを使用するため、`pusher-js`パッケージもインストールしています。

```shell
npm install --save-dev laravel-echo pusher-js
```

Echoをインストールできたら、アプリケーションのJavaScriptで新しいEchoインスタンスを生成する用意が整います。これを実行するのに最適な場所は、Laravelフレームワークに含まれている`resources/js/bootstrap.js`ファイルの下部です。デフォルトで、Echo設定の例はすでにこのファイルに含まれています。アンコメントするだけです。

```js
import Echo from 'laravel-echo';
import Pusher from 'pusher-js';

window.Pusher = Pusher;

window.Echo = new Echo({
    broadcaster: 'pusher',
    key: import.meta.env.VITE_PUSHER_APP_KEY,
    cluster: import.meta.env.VITE_PUSHER_APP_CLUSTER,
    forceTLS: true
});
```

アンコメントし、必要に応じEcho設定を調整したら、アプリケーションのアセットをコンパイルします。

```shell
npm run build
```

> **Note**
> アプリケーションで使用しているJavaScriptリソースのコンパイルについて詳しく知りたい場合は、[Vite](/docs/{{version}}/vite)ドキュメントを参照してください。

<a name="using-an-existing-client-instance"></a>
#### 既存のクライアントインスタンスの使用

Echoで利用したい事前設定済みのPusherチャンネルクライアントインスタンスがすでにある場合は、`client`設定オプションによりEchoへ渡せます。

```js
import Echo from 'laravel-echo';
import Pusher from 'pusher-js';

const options = {
    broadcaster: 'pusher',
    key: 'your-pusher-channels-key'
}

window.Echo = new Echo({
    ...options,
    client: new Pusher(options.key, options)
});
```

<a name="client-ably"></a>
### Ably

> **Note**
> 以下のドキュメントでは、Ablyを「Pusher互換」モードで使用する方法について説明していきます。しかし、Ablyチームは、Ablyが提供するユニークな機能を活用できるブロードキャスターとEchoクライアントを推奨し、保守しています。Ablyが保守するドライバの使用に関する詳細については、[AblyのLaravelブロードキャスターのドキュメントを参照](https://github.com/ably/laravel-broadcaster)してください。

[Laravel Echo](https://github.com/laravel/echo)はJavaScriptライブラリであり、チャンネルをサブスクライブして、サーバ側のブロードキャストドライバがブロードキャストしたイベントを簡単にリッスンできます。NPMパッケージマネージャを介してEchoをインストールします。この例では、`pusher-js`パッケージもインストールしています。

イベントのブロードキャストにAblyを使用しているのに、なぜ`pusher-js`JavaScriptライブラリをインストールするのか不思議に思うかもしれません。ありがたいことに、AblyにはPusher互換モードが含まれており、クライアント側アプリケーションでイベントをリッスンするときにPusherプロトコルを使用できます。

```shell
npm install --save-dev laravel-echo pusher-js
```

**続行する前に、Ablyアプリケーション設定でPusherプロトコルサポートを有効にする必要があります。この機能は、Ablyアプリケーションの設定ダッシュボードの「プロトコルアダプター設定」部分で有効にできます。**

Echoをインストールしたら、アプリケーションのJavaScriptで新しいEchoインスタンスを生成する準備が整います。これを実行するのに最適な場所は、Laravelフレームワークに含まれている`resources/js/bootstrap.js`ファイルの下部です。デフォルトでは、Echo設定の例はすでにこのファイルに用意してあります。ただし、`bootstrap.js`ファイルのデフォルト設定はPusherを対象としています。以下の設定をコピーして、設定をAbly向きに変更できます。

```js
import Echo from 'laravel-echo';
import Pusher from 'pusher-js';

window.Pusher = Pusher;

window.Echo = new Echo({
    broadcaster: 'pusher',
    key: import.meta.env.VITE_ABLY_PUBLIC_KEY,
    wsHost: 'realtime-pusher.ably.io',
    wsPort: 443,
    disableStats: true,
    encrypted: true,
});
```

Ably　Echo設定は`VITE_ABLY_PUBLIC_KEY`環境変数を参照していることに注意してください。この変数の値へAbly公開鍵を指定する必要があります。公開鍵は、`:`文字の前にあるAbly鍵の部分です。

アンコメントし、必要に応じEcho設定を調整したら、アプリケーションのアセットをコンパイルします。

```shell
npm run dev
```

> **Note**
> アプリケーションで使用しているJavaScriptリソースのコンパイルについて詳しく知りたい場合は、[Vite](/docs/{{version}}/vite)ドキュメントを参照してください。

<a name="concept-overview"></a>
## 概論

Laravelのイベントブロードキャストを使用すると、WebSocketに対するドライバベースのアプローチを使用して、サーバ側のLaravelイベントをクライアント側のJavaScriptアプリケーションへブロードキャストできます。現在、Laravelは[Pusherチャンネル](https://pusher.com/channels)と[Ably](https://ably.com)ドライバを用意しています。イベントは、[Laravel Echo](#client-side-installation) JavaScriptパッケージを用い、クライアント側で簡単に利用できます。

イベントは「チャンネル」を介してブロードキャストされます。チャンネルは、パブリックまたはプライベートとして指定できます。アプリケーションへの訪問者は全員、認証や認可なしにパブリックチャンネルをサブスクライブできます。ただし、プライベートチャンネルをサブスクライブするには、ユーザーが認証され、そのチャンネルをリッスンする認可を持っている必要があります。

> **Note**
> Pusherの代わりにオープンソースを使いたい場合は、[オープンソースの代替](#open-source-alternatives)を読んでください。

<a name="using-example-application"></a>
### サンプルアプリケーションの使用

イベントブロードキャストの各コンポーネントに飛び込む前に、ｅコマースストアのサンプルを使用して概要を説明しましょう。

このアプリケーションでは、ユーザーが注文の配送ステータスを表示できるページがあると仮定します。また、出荷ステータスの更新がアプリケーションによって処理されるときに、`OrderShipmentStatusUpdated`イベントが発生すると仮定します。

    use App\Events\OrderShipmentStatusUpdated;

    OrderShipmentStatusUpdated::dispatch($order);

<a name="the-shouldbroadcast-interface"></a>
#### `ShouldBroadcast`インターフェイス

ユーザーが注文の１つを表示しているときに、ステータスの更新を表示するためにページを更新する必要はありません。その代わりに、発生時にアプリケーションへ更新をブロードキャストしたいと思います。したがって、`OrderShipmentStatusUpdated`イベントを`ShouldBroadcast`インターフェイスでマークする必要があります。これにより、イベントが発生したときにイベントをブロードキャストするようにLaravelへ指示します。

    <?php

    namespace App\Events;

    use App\Models\Order;
    use Illuminate\Broadcasting\Channel;
    use Illuminate\Broadcasting\InteractsWithSockets;
    use Illuminate\Broadcasting\PresenceChannel;
    use Illuminate\Contracts\Broadcasting\ShouldBroadcast;
    use Illuminate\Queue\SerializesModels;

    class OrderShipmentStatusUpdated implements ShouldBroadcast
    {
        /**
         * 注文インスタンス
         *
         * @var \App\Order
         */
        public $order;
    }

`ShouldBroadcast`インターフェイスは、イベントに対し`broadcastOn`メソッドを定義するよう要求します。このメソッドは、イベントがブロードキャストする必要があるチャンネルを返す役割を持っています。このメソッドの空のスタブは、生成したイベントクラスですでに定義済みなため、詳細を入力するだけで済みます。注文の作成者だけがステータスの更新を表示できるようにしたいので、その注文に関連付いたプラ​​イベートチャンネルでイベントをブロードキャストします。

    use Illuminate\Broadcasting\Channel;
    use Illuminate\Broadcasting\PrivateChannel;

    /**
     * イベントをブロードキャストするチャンネルを取得
     */
    public function broadcastOn(): Channel
    {
        return new PrivateChannel('orders.'.$this->order->id);
    }

If you wish the event to broadcast on multiple channels, you may return an `array` instead:

    use Illuminate\Broadcasting\PrivateChannel;

    /**
     * イベントをブロードキャストするチャンネルを取得
     *
     * @return array<int, \Illuminate\Broadcasting\Channel>
     */
    public function broadcastOn(): array
    {
        return [
            new PrivateChannel('orders.'.$this->order->id),
            // ...
        ];
    }

<a name="example-application-authorizing-channels"></a>
#### チャンネルの認可

プライベートチャンネルをリッスンするには、ユーザーが認可されていなければならないことを忘れないでください。チャンネルの認可ルールは、アプリケーションの`routes/channels.php`ファイルで定義できます。この例では、`orders.1`というプライベートチャンネルをリッスンするユーザーが、実際にそのオーダーの作成者であることを確認しています。

    use App\Models\Order;
    use App\Models\User;

    Broadcast::channel('orders.{orderId}', function (User $user, int $orderId) {
        return $user->id === Order::findOrNew($orderId)->user_id;
    });

`channel`メソッドは、チャンネルの名前と、ユーザーがチャンネルでのリッスンを許可されているかどうかを示す`true`または`false`を返すコールバックの２つの引数を取ります。

すべての認可コールバックは、現在認証されているユーザーを最初の引数として受け取り、追加のワイルドカードパラメーターを後続の引数として受け取ります。この例では、`{orderId}`プレースホルダーを使用して、チャンネル名の「ID」部分がワイルドカードであることを示しています。

<a name="listening-for-event-broadcasts"></a>
#### イベントブロードキャストのリッスン

他に残っているのは、JavaScriptアプリケーションでイベントをリッスンすることだけです。[Laravel Echo](#client-side-installation)を使用してこれを行えます。まず、`private`メソッドを使用し、プライベートチャンネルをサブスクライブします。次に、`listen`メソッドを使用して`OrderShipmentStatusUpdated`イベントをリッスンします。デフォルトでは、イベントのすべてのパブリックプロパティがブロードキャストイベントに含まれます。

```js
Echo.private(`orders.${orderId}`)
    .listen('OrderShipmentStatusUpdated', (e) => {
        console.log(e.order);
    });
```

<a name="defining-broadcast-events"></a>
## ブロードキャストイベントの定義

特定のイベントをブロードキャストする必要があることをLaravelに通知するには、イベントクラスに`Illuminate\Contracts\Broadcasting\ShouldBroadcast`インターフェイスを実装する必要があります。このインターフェイスは、フレームワークで生成したすべてのイベントクラスに、最初からインポートされているため、どのイベントでも簡単に追加できます。

`ShouldBroadcast`インターフェイスでは、`broadcastOn`という単一のメソッドを実装する必要があります。`broadcastOn`メソッドは、イベントがブロードキャストする必要があるチャンネルまたはチャンネルの配列を返す必要があります。チャンネルは、`Channel`、`PrivateChannel`、`PresenceChannel`のインスタンスである必要があります。`Channel`インスタンスは、すべてのユーザーがサブスクライブできるパブリックチャンネルを表し、`PrivateChannels`と`PresenceChannels`は、[チャンネル認証](#authorizing-channels)を必要とするプライベートチャンネルを表します。

    <?php

    namespace App\Events;

    use App\Models\User;
    use Illuminate\Broadcasting\Channel;
    use Illuminate\Broadcasting\InteractsWithSockets;
    use Illuminate\Broadcasting\PresenceChannel;
    use Illuminate\Broadcasting\PrivateChannel;
    use Illuminate\Contracts\Broadcasting\ShouldBroadcast;
    use Illuminate\Queue\SerializesModels;

    class ServerCreated implements ShouldBroadcast
    {
        use SerializesModels;

        /**
         * 新しいイベントインスタンスの生成
         */
        public function __construct(
            public User $user,
        ) {}

        /**
         * イベントがブロードキャストするチャンネルを取得
         *
         * @return array<int, \Illuminate\Broadcasting\Channel>
         */
        public function broadcastOn(): array
        {
            return [
                new PrivateChannel('user.'.$this->user->id),
            ];
        }
    }

`ShouldBroadcast`インターフェイスを実装した後は、通常どおり[イベントを発生させる](/docs/{{version}}/events)だけです。イベントが発生すると、[キューへジョブへ投入](/docs/{{version}}/queues)し、指定したブロードキャストドライバを使用し、イベントを自動的にブロードキャストします。

<a name="broadcast-name"></a>
### ブロードキャスト名

デフォルトでは、Laravelはイベントのクラス名を使用してイベントをブロードキャストします。ただし、イベントで`broadcastAs`メソッドを定義することにより、ブロードキャスト名をカスタマイズできます。

    /**
     * イベントのブロードキャスト名
     */
    public function broadcastAs(): string
    {
        return 'server.created';
    }

`broadcastAs`メソッドを使用してブロードキャスト名をカスタマイズする場合は、リスナを先頭の`.`文字で登録する必要があります。これにより、アプリケーションの名前空間をイベントの先頭に追加しないようにEchoに指示します。

    .listen('.server.created', function (e) {
        ....
    });

<a name="broadcast-data"></a>
### ブロードキャストデータ

イベントをブロードキャストすると、そのすべての`public`プロパティが自動的にシリアライズされ、イベントのペイロードとしてブロードキャストされるため、JavaScriptアプリケーションからそのパブリックデータにアクセスできます。したがって、たとえば、イベントにEloquentモデルを含む単一のパブリック`$user`プロパティがある場合、イベントのブロードキャストペイロードは次のようになります。

```json
{
    "user": {
        "id": 1,
        "name": "Patrick Stewart"
        ...
    }
}
```

ただし、ブロードキャストペイロードをよりきめ細かく制御したい場合は、イベントに`broadcastWith`メソッドを追加できます。このメソッドは、ブロードキャストするデータの配列をイベントペイロードとして返す必要があります。

    /**
     * ブロードキャストするデータを取得
     *
     * @return array<string, mixed>
     */
    public function broadcastWith(): array
    {
        return ['id' => $this->user->id];
    }

<a name="broadcast-queue"></a>
### ブロードキャストキュー

デフォルトで各ブロードキャストイベントは、`queue.php`設定ファイルで指定したデフォルトキュー接続のデフォルトキューへ配置されます。イベントクラスで`connection`プロパティと`queue`プロパティを定義することにより、ブロードキャスタが使用するキュー接続と名前をカスタマイズできます。

    /**
     * イベントをブロードキャストするときに使用するキュー接続の名前
     *
     * @var string
     */
    public $connection = 'redis';

    /**
     * ブロードキャストジョブを投入するキュー名
     *
     * @var string
     */
    public $queue = 'default';

あるいは、イベントに`broadcastQueue`メソッドを定義し、キュー名をカスタマイズできます。

    /**
     * ブロードキャストジョブを投入するキュー名
     */
    public function broadcastQueue(): string
    {
        return 'default';
    }

デフォルトのキュードライバではなく、`sync`キューを使用してイベントをブロードキャストしたい場合は、`ShouldBroadcast`の代わりに`ShouldBroadcastNow`インターフェイスを実装します。

    <?php

    use Illuminate\Contracts\Broadcasting\ShouldBroadcastNow;

    class OrderShipmentStatusUpdated implements ShouldBroadcastNow
    {
        // ...
    }

<a name="broadcast-conditions"></a>
### ブロードキャスト条件

特定の条件が真である場合にのみイベントをブロードキャストしたい場合があります。イベントクラスに`broadcastWhen`メソッドを追加することで、こうした条件を定義できます。

    /**
     * このイベントをブロードキャストするかどうかを判定
     */
    public function broadcastWhen(): bool
    {
        return $this->order->value > 100;
    }

<a name="broadcasting-and-database-transactions"></a>
#### ブロードキャストとデータベーストランザクション

ブロードキャストイベントがデータベーストランザクション内でディスパッチされると、データベーストランザクションがコミットされる前にキューによって処理される場合があります。これが起きると、データベーストランザクション中にモデルまたはデータベースレコードに加えた更新は、データベースにまだ反映されていない可能性があります。さらに、トランザクション内で作成されたモデルまたはデータベースレコードは、データベースに存在しない可能性があります。イベントがこれらのモデルに依存している場合、イベントをブロードキャストするジョブの処理時に予期しないエラーが発生する可能性があります。

キュー接続の`after_commit`設定オプションが`false`に設定されている場合でも、イベントクラスで`ShouldDispatchAfterCommit`インターフェイスを実装することにより、開いているすべてのデータベーストランザクションがコミットされた後に特定のブロードキャストイベントをディスパッチする必要があることを示すことができます。

    <?php

    namespace App\Events;

    use Illuminate\Contracts\Broadcasting\ShouldBroadcast;
    use Illuminate\Contracts\Events\ShouldDispatchAfterCommit;
    use Illuminate\Queue\SerializesModels;

    class ServerCreated implements ShouldBroadcast, ShouldDispatchAfterCommit
    {
        use SerializesModels;
    }

> **Note**
> こうした問題の回避方法の詳細は、[キュー投入済みジョブとデータベーストランザクション](/docs/{{version}}/queues#jobs-and-database-transactions)に関するドキュメントを確認してください。

<a name="authorizing-channels"></a>
## チャンネルの認可

プライベートチャンネルでは現在認証済みのユーザーが、実際にチャンネルをリッスンできることを認可する必要があります。これは、チャンネル名を使用してLaravelアプリケーションにHTTPリクエストを送信し、ユーザーがそのチャンネルでリッスンできるかどうかをアプリケーションが判断できるようにすることで実現します。[Laravel Echo](#client-side-installation)を使用すると、プライベートチャンネルへのサブスクリプションを認可するためのHTTPリクエストが自動的に行われます。ただし、これらのリクエストに応答するには、適切なルートを定義する必要があります。

<a name="defining-authorization-routes"></a>
### 認可ルートの定義

便利なことに、Laravelではチャンネル認可リクエストに応答するルートを簡単に定義できます。Laravelアプリケーションに含まれている`App\Providers\BroadcastServiceProvider`で、`Broadcast::routes`メソッドの呼び出しが見つかるでしょう。このメソッドは、認可リクエストを処理するために`/Broadcasting/auth`ルートを登録します。

    Broadcast::routes();

`Broadcast::routes`メソッドは自動的にそのルートを`web`ミドルウェアグループ内に配置します。ただし、割り当てられた属性をカスタマイズする場合は、ルート属性の配列をメソッドに渡してください。

    Broadcast::routes($attributes);

<a name="customizing-the-authorization-endpoint"></a>
#### 認可エンドポイントのカスタマイズ

デフォルトでEchoは`/Broadcasting/auth`エンドポイントを使用してチャンネルアクセスを認可します。ただし、`authEndpoint`設定オプションをEchoインスタンスに渡すことで、独自の認可エンドポイントを指定できます。

```js
window.Echo = new Echo({
    broadcaster: 'pusher',
    // ...
    authEndpoint: '/custom/endpoint/auth'
});
```

<a name="customizing-the-authorization-request"></a>
#### 認可リクエストのカスタマイズ

Laravel Echoの初期化時に、カスタムAuthorizerを指定し、Laravel Echoの認可リクエスト実行方法をカスタマイズできます。

```js
window.Echo = new Echo({
    // ...
    authorizer: (channel, options) => {
        return {
            authorize: (socketId, callback) => {
                axios.post('/api/broadcasting/auth', {
                    socket_id: socketId,
                    channel_name: channel.name
                })
                .then(response => {
                    callback(null, response.data);
                })
                .catch(error => {
                    callback(error);
                });
            }
        };
    },
})
```

<a name="defining-authorization-callbacks"></a>
### 認可コールバックの定義

次に、現在の認証済みユーザーが特定のチャンネルをリッスンできるかどうかを実際に決定するロジックを定義する必要があります。これは、アプリケーションに含まれている`routes/channels.php`ファイルで行います。このファイルで`Broadcast::channel`メソッドを使用し、チャンネル認可コールバックを登録します。

    use App\Models\User;

    Broadcast::channel('orders.{orderId}', function (User $user, int $orderId) {
        return $user->id === Order::findOrNew($orderId)->user_id;
    });

`channel`メソッドは、チャンネルの名前と、ユーザーがチャンネルでのリッスンを許可されているかどうかを示す`true`または`false`を返すコールバックの２つの引数を取ります。

すべての認可コールバックは、現在認証されているユーザーを最初の引数として受け取り、追加のワイルドカードパラメーターを後続の引数として受け取ります。この例では、`{orderId}`プレースホルダーを使用して、チャンネル名の「ID」部分がワイルドカードであることを示しています。

`channel:list` Artisanコマンドを使用すると、アプリケーションのブロードキャスト認可コールバックのリストを表示できます。

```shell
php artisan channel:list
```

<a name="authorization-callback-model-binding"></a>
#### 認可コールバックモデルのバインド

HTTPルートと同様に、チャンネルルートも暗黙的および明示的な[ルートモデルバインディング](/docs/{{version}}/routing#route-model-binding)を利用できます。たとえば、文字列または数値の注文IDを受け取る代わりに、実際の`Order`モデルインスタンスを要求できます。

    use App\Models\Order;
    use App\Models\User;

    Broadcast::channel('orders.{order}', function (User $user, Order $order) {
        return $user->id === $order->user_id;
    });

> **Warning**
> HTTPルートモデルバインディングとは異なり、チャンネルモデルバインディングは自動[暗黙的モデルバインディングスコープ](/docs/{{version}}/routing#implicit-model-binding-scoping)をサポートしていません。ただし、ほとんどのチャンネルは単一のモデルの一意の主キーに基づいてスコープを設定できるため、これが問題になることはめったにありません。

<a name="authorization-callback-authentication"></a>
#### 認可コールバック認証

プライベートおよびプレゼンスブロードキャストチャンネルは、アプリケーションのデフォルトの認証ガードを介して現在のユーザーを認証します。ユーザーが認証されていない場合、チャンネル認可は自動的に拒否され、認可コールバックは実行されません。ただし、必要に応じて、受信リクエストを認証する複数の必要なカスタムガードを割り当てることができます。

    Broadcast::channel('channel', function () {
        // ...
    }, ['guards' => ['web', 'admin']]);

<a name="defining-channel-classes"></a>
### チャンネルクラスの定義

アプリケーションが多くの異なるチャンネルを使用している場合、`routes/channels.php`ファイルがかさばる可能性が起きます。そのため、クロージャを使用してチャンネルを認可する代わりに、チャンネルクラスを使用できます。チャンネルクラスを生成するには、`make:channel` Artisanコマンドを使用します。このコマンドは、新しいチャンネルクラスを`App/Broadcasting`ディレクトリに配置します。

```shell
php artisan make:channel OrderChannel
```

次に、チャンネルを`routes/channels.php`ファイルに登録します。

    use App\Broadcasting\OrderChannel;

    Broadcast::channel('orders.{order}', OrderChannel::class);

最後に、チャンネルの認可ロジックをチャンネルクラスの`join`メソッドに配置できます。この`join`メソッドは、チャンネル認可クロージャに通常配置するのと同じロジックを格納します。チャンネルモデルバインディングを利用することもできます。

    <?php

    namespace App\Broadcasting;

    use App\Models\Order;
    use App\Models\User;

    class OrderChannel
    {
        /**
         * 新しいチャンネルインスタンスの生成
         */
        public function __construct()
        {
            // ...
        }

        /**
         * チャンネルへのユーザーのアクセスを認可
         */
        public function join(User $user, Order $order): array|bool
        {
            return $user->id === $order->user_id;
        }
    }

> **Note**
> Laravelの他の多くのクラスと同様に、チャンネルクラスは[サービスコンテナ](/docs/{{version}}/container)によって自動的に依存解決されます。そのため、コンストラクターでチャンネルに必要な依存関係をタイプヒントすることができます。

<a name="broadcasting-events"></a>
## ブロードキャストイベント

イベントを定義し、`ShouldBroadcast`インターフェイスでマークを付けたら、イベントのディスパッチメソッドを使用してイベントを発生させるだけです。イベントディスパッチャは、イベントが`ShouldBroadcast`インターフェイスでマークされていることに気付き、ブロードキャストのためにイベントをキューに入れます。

    use App\Events\OrderShipmentStatusUpdated;

    OrderShipmentStatusUpdated::dispatch($order);

<a name="only-to-others"></a>
### 他の人だけへの送信

イベントブロードキャストを利用するアプリケーションを構築する場合、特定のチャンネルで現在のユーザーを除く、すべてのサブスクライバにイベントをブロードキャストする必要が起きる場合があります。これは、`broadcast`ヘルパと`toOthers`メソッドを使用して実行できます。

    use App\Events\OrderShipmentStatusUpdated;

    broadcast(new OrderShipmentStatusUpdated($update))->toOthers();

`toOthers`メソッドをいつ使用したらよいかをよりよく理解するために、ユーザーがタスク名を入力して新しいタスクを作成できるタスクリストアプリケーションを想像してみましょう。タスクを作成するために、アプリケーションは、タスクの作成をブロードキャストし、新しいタスクのJSON表現を返す`/task`URLにリクエストを送信する場合があります。JavaScriptアプリケーションがエンドポイントから応答を受信すると、次のように新しいタスクをタスクリストに直接挿入する場合があるでしょう。

```js
axios.post('/task', task)
    .then((response) => {
        this.tasks.push(response.data);
    });
```

ただし、タスクの作成もブロードキャストすることを忘れないでください。JavaScriptアプリケーションがタスクリストにタスクを追加するためにこのイベントもリッスンしている場合、リストには重複するタスクが発生します。１つはエンドポイントからのもので、もう１つはブロードキャストからのものです。これを解決するには、`toOthers`メソッドを使用して、現在のユーザーにはイベントをブロードキャストしないようにブロードキャスタへ指示します。

> **Warning**
> `toOthers`メソッドを呼び出すには、イベントで`Illuminate\Broadcasting\InteractsWithSockets`トレイトをuseする必要があります。

<a name="only-to-others-configuration"></a>
#### 設定

Laravel Echoインスタンスを初期化すると、ソケットIDが接続に割り当てられます。グローバル[Axios](https://github.com/mzabriskie/axios)インスタンスを使用してJavaScriptアプリケーションからHTTPリクエストを作成している場合、ソケットIDはすべての送信リクエストに`X-Socket-ID`ヘッダとして自動的に添付されます。次に、`toOthers`メソッドを呼び出すと、LaravelはヘッダからソケットIDを抽出し、そのソケットIDを持つ接続にブロードキャストしないようにブロードキャスタに指示します。

グローバルAxiosインスタンスを使用しない場合は、すべての送信リクエストで`X-Socket-ID`ヘッダを送信するようにJavaScriptアプリケーションを手作業で設定する必要があります。`Echo.socketId`メソッドを使用してソケットIDを取得できます。

```js
var socketId = Echo.socketId();
```

<a name="customizing-the-connection"></a>
### コネクションのカスタマイズ

アプリケーションが複数のブロードキャスト接続とやりとりしており、デフォルト以外のブロードキャスタを使いイベントをブロードキャストしたい場合は、`via`メソッドを使ってどの接続へイベントをプッシュするか指定できます。

    use App\Events\OrderShipmentStatusUpdated;

    broadcast(new OrderShipmentStatusUpdated($update))->via('pusher');

もしくは、イベントのコンストラクタで `broadcastVia` メソッドを呼び出して、イベントのブロードキャスト接続を指定することもできます。ただし、そのときは、イベントクラスで確実に`InteractsWithBroadcasting`トレイトをuseしてください。

    <?php

    namespace App\Events;

    use Illuminate\Broadcasting\Channel;
    use Illuminate\Broadcasting\InteractsWithBroadcasting;
    use Illuminate\Broadcasting\InteractsWithSockets;
    use Illuminate\Broadcasting\PresenceChannel;
    use Illuminate\Broadcasting\PrivateChannel;
    use Illuminate\Contracts\Broadcasting\ShouldBroadcast;
    use Illuminate\Queue\SerializesModels;

    class OrderShipmentStatusUpdated implements ShouldBroadcast
    {
        use InteractsWithBroadcasting;

        /**
         * 新しいイベントインスタンスの生成
         */
        public function __construct()
        {
            $this->broadcastVia('pusher');
        }
    }

<a name="receiving-broadcasts"></a>
## ブロードキャストの受け取り

<a name="listening-for-events"></a>
### イベントのリッスン

[Laravel Echoをインストールしてインスタンス化](#client-side-installation)すると、Laravelアプリケーションからブロードキャストされるイベントをリッスンする準備が整います。まず、`channel`メソッドを使用してチャンネルのインスタンスを取得し、次に`listen`メソッドを呼び出して指定されたイベントをリッスンします。

```js
Echo.channel(`orders.${this.order.id}`)
    .listen('OrderShipmentStatusUpdated', (e) => {
        console.log(e.order.name);
    });
```

プライベートチャンネルでイベントをリッスンする場合は、代わりに`private`メソッドを使用してください。`listen`メソッドへの呼び出しをチェーンして、単一のチャンネルで複数のイベントをリッスンすることができます。

```js
Echo.private(`orders.${this.order.id}`)
    .listen(/* ... */)
    .listen(/* ... */)
    .listen(/* ... */);
```

<a name="stop-listening-for-events"></a>
#### イベントリッスンの中止

[チャンネルから離れる](#leaving-a-channel)ことなく特定のイベントのリッスンを停止したい場合は、`stopListening`メソッドを使用します。

```js
Echo.private(`orders.${this.order.id}`)
    .stopListening('OrderShipmentStatusUpdated')
```

<a name="leaving-a-channel"></a>
### チャンネルの離脱

チャンネルを離れるには、Echoインスタンスで`leaveChannel`メソッドを呼び出してください。

```js
Echo.leaveChannel(`orders.${this.order.id}`);
```

チャンネルとそれに関連するプライベートチャンネルおよびプレゼンスチャンネルを離れたい場合は、`leave`メソッドを呼び出してください。

```js
Echo.leave(`orders.${this.order.id}`);
```
<a name="namespaces"></a>
### 名前空間

上記の例で、イベントクラスに完全な`App\Events`名前空間を指定していないことに気付いたかもしれません。これは、Echoがイベントが`App\Events`名前空間にあると自動的に想定するためです。ただし、`namespace`設定オプションを渡すことにより、Echoをインスタンス化するときにルート名前空間を設定できます。

```js
window.Echo = new Echo({
    broadcaster: 'pusher',
    // ...
    namespace: 'App.Other.Namespace'
});
```

または、Echoを使用してサブスクライブするときに、イベントクラスの前に`.`を付けることもできます。これにより、常に完全修飾クラス名を指定できます。

```js
Echo.channel('orders')
    .listen('.Namespace\\Event\\Class', (e) => {
        // ...
    });
```

<a name="presence-channels"></a>
## プレゼンスチャンネル

プレゼンスチャンネルは、プライベートチャンネルのセキュリティを基盤とし、チャンネルにサブスクライブしているユーザーを認識するという追加機能を付け加えます。これにより、別のユーザーが同じページを表示しているときにユーザーに通知したり、チャットルームの住民を一覧表示したりするなど、強力なコラボレーションアプリケーション機能を簡単に構築できます。

<a name="authorizing-presence-channels"></a>
### プレゼンスチャンネルの認可

すべてのプレゼンスチャンネルもプライベートチャンネルです。したがって、ユーザーは[アクセス許可](#authorizing-channels)を持つ必要があります。ただし、プレゼンスチャンネルの認可コールバックを定義する場合、ユーザーがチャンネルへの参加を認可されている場合に、`true`を返しません。代わりに、ユーザーに関するデータの配列を返す必要があります。

認可コールバックが返すデータは、JavaScriptアプリケーションのプレゼンスチャンネルイベントリスナが利用できるようになります。ユーザーがプレゼンスチャンネルへの参加を許可されていない場合は、`false`または`null`を返す必要があります。

    use App\Models\User;

    Broadcast::channel('chat.{roomId}', function (User $user, int $roomId) {
        if ($user->canJoinRoom($roomId)) {
            return ['id' => $user->id, 'name' => $user->name];
        }
    });

<a name="joining-presence-channels"></a>
### プレゼンスチャンネルへの接続

プレゼンスチャンネルに参加するには、Echoの`join`メソッドを使用できます。`join`メソッドは`PresenceChannel`実装を返します。これは、`listen`メソッドを公開するとともに、`here`、`joining`、および`leaving`イベントをサブスクライブできるようにします。

```js
Echo.join(`chat.${roomId}`)
    .here((users) => {
        // ...
    })
    .joining((user) => {
        console.log(user.name);
    })
    .leaving((user) => {
        console.log(user.name);
    })
    .error((error) => {
        console.error(error);
    });
```

`here`コールバックは、チャンネルへ正常に参加するとすぐに実行され、現在チャンネルにサブスクライブしている他のすべてのユーザーのユーザー情報を含む配列を受け取ります。`joining`メソッドは、新しいユーザーがチャンネルに参加したときに実行され、`leaving`メソッドは、ユーザーがチャンネルを離れたときに実行されます。`error`メソッドは、認証エンドポイントが200以外のHTTPステータスコードを返した場合や、返されたJSONの解析で問題があった場合に実行されます。

<a name="broadcasting-to-presence-channels"></a>
### プレゼンスチャンネルへのブロードキャスト

プレゼンスチャンネルは、パブリックチャンネルまたはプライベートチャンネルと同じようにイベントを受信できます。チャットルームの例を使用して、`NewMessage`イベントをルームのプレゼンスチャンネルにブロードキャストしたいとしましょう。そのために、イベントの`broadcastOn`メソッドから`PresenceChannel`のインスタンスを返します。

    /**
     * イベントをブロードキャストするチャンネルを取得
     *
     * @return array<int, \Illuminate\Broadcasting\Channel>
     */
    public function broadcastOn(): array
    {
        return [
            new PresenceChannel('chat.'.$this->message->room_id),
        ];
    }

他のイベントと同様に、`broadcast`ヘルパと`toOthers`メソッドを使用して、現在のユーザーをブロードキャストの受信から除外できます。

    broadcast(new NewMessage($message));

    broadcast(new NewMessage($message))->toOthers();

他の典型的なタイプのイベントと同様に、Echoの`listen`メソッドを使用してプレゼンスチャンネルに送信されたイベントをリッスンできます。

```js
Echo.join(`chat.${roomId}`)
    .here(/* ... */)
    .joining(/* ... */)
    .leaving(/* ... */)
    .listen('NewMessage', (e) => {
        // ...
    });
```

<a name="model-broadcasting"></a>
## モデルブロードキャスト

> **Warning**
> モデルブロードキャストに関する以降のドキュメントを読む前に、Laravelのモデルブロードキャストサービスの一般的なコンセプトや、ブロードキャストイベントを手作業で作成したり、リッスンしたりする方法に精通しておくことをおすすめします。

アプリケーションの[Eloquentモデル](/docs/{{version}}/eloquent)が作成、更新、または削除されたときにイベントをブロードキャストするのは一般的です。もちろん、これは自前で[Eloquentモデルの状態変化を表すカスタムイベントを定義](/docs/{{version}}/eloquent#events)し、それらのイベントを`ShouldBroadcast`インターフェイスでマークすることで簡単に実現できます。

しかし、こうしたイベントをアプリケーション内で他の目的に使用しない場合、イベントをブロードキャストするためだけにイベントクラスを作成するのは面倒なことです。そのためLaravelは指定があれば、Eloquentモデルの状態変化を自動的にブロードキャストします。

まず始めに、Eloquentモデルで、`Illuminate\Database\BroadcastsEvents`トレイトを使用する必要があります。さらに、そのモデルで`broadcastOn`メソッドを定義する必要があります。このメソッドは、モデルイベントをブロードキャストするチャンネルの配列を返します。

```php
<?php

namespace App\Models;

use Illuminate\Broadcasting\Channel;
use Illuminate\Broadcasting\PrivateChannel;
use Illuminate\Database\Eloquent\BroadcastsEvents;
use Illuminate\Database\Eloquent\Factories\HasFactory;
use Illuminate\Database\Eloquent\Model;
use Illuminate\Database\Eloquent\Relations\BelongsTo;

class Post extends Model
{
    use BroadcastsEvents, HasFactory;

    /**
     * ポストが属するユーザーの取得
     */
    public function user(): BelongsTo
    {
        return $this->belongsTo(User::class);
    }

    /**
     * モデルイベントをブロードキャストするチャンネルを取得
     *
     * @return array<int, \Illuminate\Broadcasting\Channel|\Illuminate\Database\Eloquent\Model>
     */
    public function broadcastOn(string $event): array
    {
        return [$this, $this->user];
    }
}
```

モデルでこの特性を使い、そしてブロードキャスト・チャンネルを定義したら、モデルインスタンスの作成、更新、削除、ソフトデリートと復元のとき、自動的にイベントのブロードキャストが開始されます。

さらに、`broadcastOn` メソッドが文字列の`$event`引数を受け取っていることにお気づきでしょう。この引数には、モデルで発生したイベントの種類が含まれており、`created`、`updated`、`deleted`、`trashed`、`restored`のいずれかの値を持ちます。この変数の値を調べることで、必要であれば、特定のイベントに対しモデルをどのチャンネルへブロードキャストするかを判定できます。

```php
/**
 * モデルイベントをブロードキャストするチャンネルを取得
 *
 * @return array<string, array<int, \Illuminate\Broadcasting\Channel|\Illuminate\Database\Eloquent\Model>>
 */
public function broadcastOn(string $event): array
{
    return match ($event) {
        'deleted' => [],
        default => [$this, $this->user],
    };
}
```

<a name="customizing-model-broadcasting-event-creation"></a>
#### モデルブロードキャストのイベント生成のカスタマイズ

時には、Laravelのモデルブロードキャスティングイベントの作成方法をカスタマイズしたい場合も起きるでしょう。それには、Eloquentモデルに`newBroadcastableEvent`メソッドを定義してください。このメソッドは、`Illuminate\Database\Eloquent\BroadcastableModelEventOccurred`インスタンスを返す必要があります。

```php
use Illuminate\Database\Eloquent\BroadcastableModelEventOccurred;

/**
 * このモデルのための新しいブロードキャストモデルイベントを作成
 */
protected function newBroadcastableEvent(string $event): BroadcastableModelEventOccurred
{
    return (new BroadcastableModelEventOccurred(
        $this, $event
    ))->dontBroadcastToCurrentUser();
}
```

<a name="model-broadcasting-conventions"></a>
### モデルブロードキャスト規約

<a name="model-broadcasting-channel-conventions"></a>
#### チャンネル規約

お気づきかもしれませんが、上記のモデル例の`broadcastOn`メソッドは、`Channel`インスタンスを返していません。代わりにEloquentモデルを直接返しています。モデルの`broadcastOn`メソッドが、Eloquentモデルインスタンスを返す場合（または、メソッドが返す配列に含まれている場合）、Laravelはモデルのクラス名と主キー識別子をチャンネル名とする、モデルのプライベートチャンネルインスタンスを自動的にインスタンス化します。

つまり、`id`が`1`の`App\Models\User`モデルは、`App.Models.User.1`という名前の`Illuminate\Broadcasting\PrivateChannel`インスタンスへ変換されるわけです。もちろん、モデルの`broadcastOn`メソッドから、Eloquentモデルインスタンスを返すことに加え、モデルのチャンネル名を完全にコントロールするため、完全な`Channel`インスタンスを返すこともできます。

```php
use Illuminate\Broadcasting\PrivateChannel;

/**
 * モデルイベントをブロードキャストするチャンネルを取得
 *
 * @return array<int, \Illuminate\Broadcasting\Channel>
 */
public function broadcastOn(string $event): array
{
    return [
        new PrivateChannel('user.'.$this->id)
    ];
}
```

モデルの`broadcastOn`メソッドからチャンネルのインスタンスを明示的に返す場合は、チャンネルのコンストラクタにEloquentモデルのインスタンスを渡すことができます。そうすると、Laravelは上述のモデルチャンネルの規約を使って、Eloquentモデルをチャンネル名の文字列に変換します。

```php
return [new Channel($this->user)];
```

モデルのチャンネル名を決定する必要がある場合は、モデルインスタンスで`broadcastChannel`メソッドを呼び出してください。たとえば、`1`の`id`を持つ`App\Models\User`モデルに対し、このメソッドは文字列`App.Models.User.1`を返します。

```php
$user->broadcastChannel()
```

<a name="model-broadcasting-event-conventions"></a>
#### イベント規約

モデルのブロードキャストイベントは、アプリケーションの`App\Events`ディレクトリ内の「実際の」イベントとは関連していないので、規約に基づいて名前とペイロードが割り当てられます。Laravelの規約では、モデルのクラス名（名前空間を含まない）と、ブロードキャストのきっかけとなったモデルイベントの名前を使って、イベントをブロードキャストします。

ですから、例えば、`App\Models\Post`モデルの更新は、以下のペイロードを持つ`PostUpdated`として、クライアントサイドのアプリケーションにイベントをブロードキャストします。

```json
{
    "model": {
        "id": 1,
        "title": "My first post"
        ...
    },
    ...
    "socket": "someSocketId",
}
```

`App\Models\User`モデルが削除されると、`UserDeleted`という名前のイベントをブロードキャストします。

必要であれば、モデルに `broadcastAs` と `broadcastWith` メソッドを追加することで、カスタムのブロードキャスト名とペイロードを定義することができます。これらのメソッドは、発生しているモデルのイベント／操作の名前を受け取るので、モデルの操作ごとにイベントの名前やペイロードをカスタマイズできます。もし、`broadcastAs`メソッドから`null`が返された場合、Laravelはイベントをブロードキャストする際に、上記で説明したモデルのブロードキャストイベント名の規約を使用します。

```php
/**
 * モデルイベントのブロードキャスト名
 */
public function broadcastAs(string $event): string|null
{
    return match ($event) {
        'created' => 'post.created',
        default => null,
    };
}

/**
 * モデルのブロードキャストのデータ取得
 *
 * @return array<string, mixed>
 */
public function broadcastWith(string $event): array
{
    return match ($event) {
        'created' => ['title' => $this->title],
        default => ['model' => $this],
    };
}
```

<a name="listening-for-model-broadcasts"></a>
### モデルブロードキャストのリッスン

モデルへ`BroadcastsEvents`トレイトを追加し、モデルの`broadcastOn`メソッドを定義したら、クライアントサイドのアプリケーションで、ブロードキャストしたモデルイベントをリッスンする準備ができました。始める前に、[イベントのリッスン](#listening-for-events)の完全なドキュメントを参照しておくとよいでしょう。

まず、`private`メソッドでチャンネルのインスタンスを取得し、それから`listen`メソッドを呼び出して、指定したイベントをリッスンします。通常、`private`メソッドへ指定するチャンネル名は、Laravelの[モデルブロードキャスト規約](#model-broadcasting-conventions)に対応していなければなりません。

チャンネルインスタンスを取得したら、`listen`メソッドを使って特定のイベントをリッスンします。モデルのブロードキャストイベントは、アプリケーションの`App\Events`ディレクトリにある「実際の」イベントと関連付けられていないため、[イベント名](#model-broadcasting-event-conventions)の前に`.`を付けて、特定の名前空間に属していないことを示す必要があります。各モデルブロードキャストイベントは、そのモデルのブロードキャスト可能なプロパティをすべて含む`model`プロパティを持ちます。

```js
Echo.private(`App.Models.User.${this.user.id}`)
    .listen('.PostUpdated', (e) => {
        console.log(e.model);
    });
```

<a name="client-events"></a>
## クライアントイベント

> **Note**
> [Pusherチャンネル](https://pusher.com/channels)を使用する場合は、クライアントイベントを送信するために[アプリケーションダッシュボード](https://dashboard.pusher.com/)の"App Settings"セクションの"Client Events"オプションを有効にする必要があります。

Laravelアプリケーションにまったくアクセスせずに、接続済みの他のクライアントにイベントをブロードキャストしたい場合があります。これは、別のユーザーが特定の画面でメッセージを入力していることをアプリケーションのユーザーに警告する「入力」通知などに特に役立ちます。

クライアントイベントをブロードキャストするには、Echoの`whisper`メソッドを使用できます。

```js
Echo.private(`chat.${roomId}`)
    .whisper('typing', {
        name: this.user.name
    });
```

クライアントイベントをリッスンするには、`listenForWhisper`メソッドを使用します。

```js
Echo.private(`chat.${roomId}`)
    .listenForWhisper('typing', (e) => {
        console.log(e.name);
    });
```

<a name="notifications"></a>
## 通知

イベントブロードキャストを[通知](/docs/{{version}}/notifications)と組み合わせることで、JavaScriptアプリケーションは、ページを更新せず発生した新しい通知を受け取ることができます。実現する前に、[ブロードキャスト通知チャンネル](/docs/{{version}}/notifications#broadcast-notifications)の使用に関するドキュメントを必ずお読みください。

ブロードキャストチャンネルを使用するように通知を設定すると、Echoの`notification`メソッドを使用してブロードキャストイベントをリッスンできます。チャンネル名は、通知を受信するエンティティのクラス名と一致する必要があることに注意してください。

```js
Echo.private(`App.Models.User.${userId}`)
    .notification((notification) => {
        console.log(notification.type);
    });
```

この例では、`broadcast`チャンネルを介して`App\Models\User`インスタンスに送信されたすべての通知は、コールバックにより受け取られます。`App.Models.User.{id}`チャンネルのチャンネル認可コールバックは、Laravelフレームワークに付属するデフォルトの`BroadcastServiceProvider`に含まれています。
