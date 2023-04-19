# インストール

- [Laravelとの出会い](#meet-laravel)
    - [なぜLaravelなのか？](#why-laravel)
- [最初のLaravelプロジェクト](#your-first-laravel-project)
- [LaravelとDocker](#laravel-and-docker)
    - [macOSで始める](#getting-started-on-macos)
    - [Windowsで始める](#getting-started-on-windows)
    - [Linuxで始める](#getting-started-on-linux)
    - [Sailサービスの選択](#choosing-your-sail-services)
- [初期設定](#initial-configuration)
    - [環境ベースの設定](#environment-based-configuration)
    - [データベースとマイグレーション](#databases-and-migrations)
- [次のステップ](#next-steps)
    - [Laravelフルスタックフレームワーク](#laravel-the-fullstack-framework)
    - [Laravel APIバックエンド](#laravel-the-api-backend)

<a name="meet-laravel"></a>
## Laravelとの出会い

Laravelは、表現力豊かでエレガントな構文を備えたWebアプリケーションフレームワークです。Webフレームワークは、アプリケーションを作成するための構造と開始点を提供します。これにより、細部に気を配りながら、すばらしいものの作成に集中できます。

Laravelは、すばらしい開発者エクスペリエンスの提供に努めています。同時に完全な依存注入、表現力豊かなデータベース抽象化レイヤー、キューとジョブのスケジュール、ユニットと統合テストなど、強力な機能もLaravelは提供しています。

PHP Webフレームワークをはじめて使用する場合も、長年の経験を持っている場合でも、Laravelは一緒に成長できるフレームワークです。私たちは皆さんがWeb開発者として最初の一歩を踏み出すのを支援したり、専門知識を次のレベルに引き上げる後押しをしたりしています。あなたが何を作り上げるのか楽しみにしています。

> **Note**
> Laravelは初めてですか？[Laravel Bootcamp](https://bootcamp.laravel.com)では、Laravelのフレームワークを実際に体験しながら、Laravelアプリケーションを構築できます。

<a name="why-laravel"></a>
### なぜLaravelなのか？

Webアプリケーションを構築するときに利用できるさまざまなツールとフレームワークがあります。そうした状況でも、Laravelは最新のフルスタックWebアプリケーションを構築するために最良の選択であると私たちは信じています。

#### 前進するフレームワーク

私たちはLaravelを「進歩的な」フレームワークと呼んでいます。つまり、Laravelはあなたと一緒に成長するという意味です。もしあなたが、Web開発の最初の一歩を踏み出したばかりの方であれば、Laravelの膨大なドキュメント、ガイド、および[ビデオチュートリアル](https://laracasts.com)のライブラリが、圧倒されず骨子を学ぶのに役立つでしょう。

開発の上級者でしたら、Laravelの[依存注入](/docs/{{version}}/container)、[単体テスト](/docs/{{version}}/testing)、[キュー](/docs/{{version}}/queues)、[リアルタイムイベント](/docs/{{version}}/broadcasting)など堅牢なツールが役立つでしょう。Laravelは、プロフェッショナルなWebアプリケーションを構築するため調整してあり、エンタープライズにおける作業負荷を処理する準備ができています。

#### スケーラブルなフレームワーク

Laravelは素晴らしくスケーラブルです。PHPのスケーリングに適した基本の性質と、Redisなど高速な分散キャッシュシステムに対するLaravelの組み込み済みサポートにより、Laravelを使用した水平スケーリングは簡単です。実際、Laravelアプリケーションは、月あたり数億のリクエストを処理するよう簡単に拡張できます。

極端なスケーリングが必要ですか？ [Laravel Vapor](https://vapor.laravel.com)のようなプラットフォームを使用すると、AWSの最新のサーバレステクノロジーでほぼ無制限の規模でLaravelアプリケーションを実行できます。

#### コミュニティによるフレームワーク

LaravelはPHPエコシステムで最高のパッケージを組み合わせ、もっとも堅牢で開発者に優しいフレームワークとして使用できるように提供しています。さらに、世界中の何千人もの才能ある開発者が[フレームワークに貢献](https://github.com/laravel/framework)しています。多分あなたもLaravelの貢献者になるかもしれませんね。

<a name="your-first-laravel-project"></a>
## 最初のLaravelプロジェクト

最初のLaravelプロジェクトを作成する前に、ローカルマシンにPHPと[Composer](https://getcomposer.org)をインストールしていることを確認してください。macOSで開発している場合、PHPとComposerは[Homebrew](https://brew.sh/)を使い、インストールできます。さらに、[NodeとNPMのインストール](https://nodejs.org)もおすすめします。

PHPとComposerをインストールしたら、Composerの`create-project`コマンドで、Laravelの新規プロジェクトを作成できます。

```nothing
composer create-project laravel/laravel example-app
```

または、Composer経由でLaravelインストーラをグローバルインストールし、新しいLaravelプロジェクトを作成することもできます。

```nothing
composer global require laravel/installer

laravel new example-app
```

プロジェクト作成後、LaravelのArtisan `serve` CLIコマンドを使用して、Laravelローカル開発サーバを起動します。

```nothing
cd example-app

php artisan serve
```

Artisan開発サーバを起動すると、Webブラウザで`http://localhost:8000`からアプリケーションへアクセスできるようになります。次に、[Laravelエコシステムへの次のステップを開始](#next-steps)する準備が整いました。もちろん、[データベースの設定](#databases-and-migrations)も必要になります。

> **Note**
> Laravelアプリケーションを開発する際に、有利なスタートダッシュを切りたければ、[スターターキット](/docs/{{version}}/starter-kits)の１つを使用することを検討してください。Laravelのスターターキットは、新しいLaravelアプリケーションのために、バックエンドとフロントエンド側の認証のスカフォールドを提供します。

<a name="laravel-and-docker"></a>
## LaravelとDocker

皆さんの好みのオペレーティングシステムが何であれ、できるだけ簡単にLaravelを始められるようにしたいと考えています。そのため、ローカルマシンでLaravelプロジェクトを開発・実行するための様々なオプションが用意されています。これらのオプションは後ほど検討していただけますが、Laravelでは[Sail](/docs/{{version}}/sail)という、[Docker](https://www.docker.com)を使用してLaravelプロジェクトを実行する組み込みソリューションを提供しています。

Dockerは、ローカルマシンにインストールしているソフトウェアや構成に干渉しない、小型で軽量の「コンテナ」でアプリケーションとサービスを実行するためのツールです。これはつまり、パーソナルマシン上のWebサーバやデータベースなどの複雑な開発ツールの構成や準備について心配する必要はないことを意味します。開発を開始するには、[Docker Desktop](https://www.docker.com/products/docker-desktop)をインストールするだけです。

Laravel Sailは、LaravelのデフォルトのDocker構成と、操作するための軽量のコマンドラインインターフェイスです。 Sailは、Dockerの経験がなくても、PHP、MySQL、Redisを使用してLaravelアプリケーションを構築するために良い出発点を提供しています。

> **Note**
> すでにDockerのエキスパートですか？ご心配なく！Laravelが提供する`docker-compose.yml`ファイルを使用して、Sailに関するすべてをカスタマイズできます。

<a name="getting-started-on-macos"></a>
### macOSで始める

Macで開発していて、[Docker Desktop](https://www.docker.com/products/docker-desktop)がすでにインストールされているならば、簡単なターミナルコマンドを使用して新しいLaravelプロジェクトを作成できます。たとえば、「example-app」という名前のディレクトリに新しいLaravelアプリケーションを作成するには、ターミナルで以下のコマンドを実行します。

```shell
curl -s "https://laravel.build/example-app" | bash
```

もちろん、このURLの"example-app"は好きなように変更できます。ただし、アプリケーション名は英数字とハイフン、アンダーバーだけで構成してください。Laravelアプリケーションのディレクトリは、コマンドを実行したディレクトリ内に作成されます。

Sailのインストールは、Sailのアプリケーションコンテナをローカルマシン上に構築するため、数分かかる場合があります。

プロジェクトを作成したら、アプリケーションディレクトリに移動してLaravel Sailを起動してください。Laravel Sailは、LaravelのデフォルトのDocker構成を操作するためのシンプルなコマンドラインインターフェイスを提供しています。

```shell
cd example-app

./vendor/bin/sail up
```

アプリケーションのDockerコンテナを開始したら、Webブラウザでアプリケーションのhttp://localhostにアクセスできます。

> **Note**
> Laravel Sailの詳細は、[完全なドキュメント](/docs/{{version}}/sail)で確認してください。

<a name="getting-started-on-windows"></a>
### Windowsで始める

Windowsマシンに新しいLaravelアプリケーションを作成する前に、必ず[Docker Desktop](https://www.docker.com/products/docker-desktop)をインストールしてください。次に、Windows Subsystem for Linux 2（WSL2）がインストールされ、有効になっていることを確認する必要があります。 WSLを使用すると、Linuxバイナリ実行可能ファイルをWindows 10でネイティブに実行できます。WSL2をインストールして有効にする方法については、Microsoftの[開発者環境ドキュメント](https://docs.microsoft.com/en-us/windows/wsl/install-win10)を参照してください。

> **Note**
> WSL2をインストールして有効にした後、Dockerデスクトップが[WSL2バックエンドを使用するように構成されている](https://docs.docker.com/docker-for-windows/wsl/)ことを確認する必要があります。

これで、最初のLaravelプロジェクトを作成する準備が整いました。[Windowsターミナル](https://www.microsoft.com/en-us/p/windows-terminal/9n0dx20hk701?rtc=1&activetab=pivot:overviewtab)を起動し、WSL2 Linuxオペレーティングシステムの新しいターミナルセッションを開始します。次に、簡単なターミナルコマンドを使用して新しいLaravelプロジェクトを作成してみましょう。たとえば、"example-app"という名前のディレクトリに新しいLaravelアプリケーションを作成するには、ターミナルで以下のコマンドを実行します。

```shell
curl -s https://laravel.build/example-app | bash
```

もちろん、このURLの"example-app"は好きなように変更できます。ただし、アプリケーション名は英数字とハイフン、アンダーバーだけで構成してください。Laravelアプリケーションのディレクトリは、コマンドを実行したディレクトリ内に作成されます。

Sailのインストールは、Sailのアプリケーションコンテナをローカルマシン上に構築するため、数分かかる場合があります。

プロジェクトを作成したら、アプリケーションディレクトリに移動してLaravel Sailを起動してください。Laravel Sailは、LaravelのデフォルトのDocker構成を操作するためのシンプルなコマンドラインインターフェイスを提供しています。

```shell
cd example-app

./vendor/bin/sail up
```

アプリケーションのDockerコンテナを開始したら、Webブラウザでアプリケーションの http://localhost へアクセスできます。

> **Note**
> Laravel Sailの詳細は、[完全なドキュメント](/docs/{{version}}/sail)で確認してください。

#### WSL2内での開発

もちろん、WSL2インストール内で作成されたLaravelアプリケーションファイルを変更する必要があります。これを実現するには、Microsoftの[Visual Studio Code](https://code.visualstudio.com)エディターと[リモート開発](https://marketplace.visualstudio.com/items?itemName=ms-vscode-remote.vscode-remote-extensionpack)用のファーストパーティ拡張機能を使用することをお勧めします。

これらのツールをインストールしたら、Windowsターミナルを使用してアプリケーションのルートディレクトリから `code .`コマンドを実行することで、任意のLaravelプロジェクトを開けます。

<a name="getting-started-on-linux"></a>
### Linuxで始める

Linuxで開発しており、[Docker Compose](https://docs.docker.com/compose/install/)がインストール済みの場合は、簡単なターミナルコマンドを使用して新しいLaravelプロジェクトを作成できます。たとえば、"example-app"という名前のディレクトリに新しいLaravelアプリケーションを作成するには、ターミナルで次のコマンドを実行します。

```shell
curl -s https://laravel.build/example-app | bash
```

もちろん、このURLの"example-app"は好きなように変更できます。ただし、アプリケーション名は英数字とハイフン、アンダーバーだけで構成してください。Laravelアプリケーションのディレクトリは、コマンドを実行したディレクトリ内に作成されます。

Sailのインストールは、Sailのアプリケーションコンテナをローカルマシン上に構築するため、数分かかる場合があります。

プロジェクトを作成したら、アプリケーションディレクトリに移動してLaravel Sailを起動してください。Laravel Sailは、LaravelのデフォルトのDocker構成を操作するためのシンプルなコマンドラインインターフェイスを提供しています。

```shell
cd example-app

./vendor/bin/sail up
```

アプリケーションのDockerコンテナを開始したら、Webブラウザでアプリケーションのhttp://localhost へアクセスできます。

> **Note**
> Laravel Sailの詳細は、[完全なドキュメント](/docs/{{version}}/sail)で確認してください。

<a name="choosing-your-sail-services"></a>
### Sailサービスの選択

Sailで新しいLaravelアプリケーションを作成する際に、`with`というクエリ文字列変数を使って、新しいアプリケーションの`docker-compose.yml`ファイルで設定するサービスを選択できます。利用可能なサービスは、`mysql`、`pgsql`、`mariadb`、`redis`、`memcached`、`meilisearch`、`minio`、`selenium`、`mailpit`です。

```shell
curl -s "https://laravel.build/example-app?with=mysql,redis" | bash
```

設定したいサービスを指定しない場合は、`mysql`、`redis`、`meilisearch`、`mailpit`、`selenium`のデフォルトのスタックが設定されます。

URLへ`devcontainer`パラメータを追加し、デフォルトの[Devcontainer](/docs/{{version}}/sail#using-devcontainers)をインストールするよう、Sailに指示できます。

```shell
curl -s "https://laravel.build/example-app?with=mysql,redis&devcontainer" | bash
```

<a name="initial-configuration"></a>
## 初期設定

Laravelフレームワークのすべての設定ファイルは、`config`ディレクトリに格納されています。各オプションにコメントが記述していますので、気兼ねなくファイルに目を通し、利用可能なオプションに馴染んでください。

Laravelでは、初期の追加設定はほとんど必要ありません。すぐに開発を始められます。しかし、`config/app.php`ファイルとそのコメントを確認することをお勧めします。このファイルには、`timezone`や`locale` など、アプリケーションに応じて更新しておきたいオプションが含まれています。

<a name="environment-based-configuration"></a>
### 環境ベースの設置

Laravelの設定オプションの値の多くは、アプリケーションがローカルマシンで動作しているか、実働Webサーバで動作しているかにより異なるため、多くの重要な設定値はアプリケーションルートに存在する、`.env`ファイルを用いて定義します。

`.env`ファイルは、アプリケーションのソース管理へコミットしてはいけません。なぜなら、アプリケーションを使用する開発者やサーバごとに、異なる環境設定が必要になる可能性があるからです。さらに、侵入者がソースコントロールリポジトリへアクセスした場合、機密情報が漏洩してしまうため、セキュリティリスクにもなります。

> **Note**
> `.env`ファイルと環境ベースの設定の詳細は、完全な[設定ドキュメント](/docs/{{version}}/configuration#environment-configuration)をチェックしてください。

<a name="databases-and-migrations"></a>
### データベースとマイグレーション

Laravelアプリケーションを作成したら、データをデータベースへ保存したいと思うことでしょう。アプリケーションの`.env`設定ファイルはデフォルトで、MySQLデータベースを操作するように指定し、`127.0.0.1`のデータベースへアクセスするようになっています。macOSで開発しており、MySQL、Postgres、Redisをローカルにインストールする必要がある場合、[DBngin](https://dbngin.com/)を利用すると便利です。

ローカルマシンにMySQLやPostgresをインストールしたくない場合は、いつでも[SQLite](https://www.sqlite.org/index.html)データベースを使用できます。SQLiteは小さく、高速で、自己完結型のデータベースエンジンです。使用し始めるには、空のSQLiteファイルを作成することにより、SQLiteデータベースを作成します。通常、このファイルはLaravelアプリケーションの`database`ディレクトリの中に設置します。

```shell
touch database/database.sqlite
```

次に、Laravelが`sqlite`データベースドライバを使用するよう、`.env`設定ファイルを変更します。他のデータベース設定オプションは削除してかまいません。

```ini
DB_CONNECTION=sqlite # [tl! add]
DB_CONNECTION=mysql # [tl! remove]
DB_HOST=127.0.0.1 # [tl! remove]
DB_PORT=3306 # [tl! remove]
DB_DATABASE=laravel # [tl! remove]
DB_USERNAME=root # [tl! remove]
DB_PASSWORD= # [tl! remove]
```

SQLiteデータベースの設定が終わったら、[データベースマイグレーション](/docs/{{version}}/migrations)を実行し、アプリケーションのデータベーステーブルを作成します。

```shell
php artisan migrate
```

<a name="next-steps"></a>
## 次のステップ

Laravelプロジェクトを設定し終えて、次に何を学ぶべきか迷っているかもしれません。まず、以下のドキュメントを読み、Laravelの仕組みを理解することを強く推奨いたします。

<div class="content-list" markdown="1">

- [リクエストのライフサイクル](/docs/{{version}}/lifecycle)
- [設定](/docs/{{version}}/configuration)
- [ディレクトリ構成](/docs/{{version}}/structure)
- [フロントエンド](/docs/{{version}}/frontend)
- [サービスコンテナ](/docs/{{version}}/container)
- [ファサード](/docs/{{version}}/facades)

</div>

Laravelをどのように使用するかにより、旅の次の行き先も決まります。Laravelを使用するにはさまざまな方法があります。以下では、フレームワークの２つの主要なユースケースについて説明します。

> **Note**
> Laravelは初めてですか？[Laravel Bootcamp](https://bootcamp.laravel.com)では、Laravelのフレームワークを実際に体験しながら、Laravelアプリケーションを構築できます。

<a name="laravel-the-fullstack-framework"></a>
### Laravelフルスタックフレームワーク

Laravelは、フルスタックフレームワークとして機能させることができます。「フルスタック」フレームワークとは、Laravelを使用して、アプリケーションへのリクエストをルーティングし、[Bladeテンプレート](/docs/{{version}}/blade)や[Inertia](https://inertiajs.com)などのシングルページアプリケーションハイブリッド技術でフロントエンドをレンダすることを意味します。これは、Laravelフレームワークの最も一般的な使用方法であり、私たちの意見では、Laravelを使用する最も生産的な方法です。

もし、Laravelをどうしようしようかと考えているのであれば、[フロントエンド開発](/docs/{{version}}/frontend)、[ルーティング](/docs/{{version}}/routing)、[ビュー](/docs/{{version}}/views)、[Eloquent ORM](/docs/{{version}}/eloquent)についてのドキュメントをチェックすると良いかも知れません。さらに、[Livewire](https://laravel-livewire.com)や[Inertia](https://inertiajs.com)といったコミュニティパッケージについても学ぶことに興味があるかもしれません。これらのパッケージにより、Laravelをフルスタックフレームワークとして使用しながら、シングルページのJavaScriptアプリケーションが提供するUIの、利点をたくさん享受できます。

Laravelをフルスタックフレームワークとして使用している場合、[Vite](/docs/{{version}}/vite)を使用してアプリケーションのCSSとJavaScriptをコンパイルする方法を学ぶのも強く推奨します。

> **Note**
> アプリケーションの構築をすぐに始めたい場合は、公式の[アプリケーションスターターキット](/docs/{{version}}/starter-kits)の１つをチェックしてください。

<a name="laravel-the-api-backend"></a>
### Laravel APIバックエンド

Laravelは、JavaScriptシングルページアプリケーションまたはモバイルアプリケーションへのAPIバックエンドとしても機能させることもあります。たとえば、[Next.js](https://nextjs.org)アプリケーションのAPIバックエンドとしてLaravelを使用できます。こうした使い方では、Laravelでアプリケーションに[認証](/docs/{{version}}/sanctum)とデータの保存/取得を提供すると同時に、キュー、メール、通知などのLaravelの強力なサービスを利用できます。

この方法でLaravelの使用を計画している場合は、[ルーティング](/docs/{{version}}/routing)、[Laravel Sanctum](/docs/{{version}}/sanctum)、[Eloquent ORM](/docs/{{version}}/eloquent)に関するドキュメントを確認することをお勧めします。

> **Note**
> LaravelのバックエンドとNext.jsのフロントエンドのスカフォールドから始める必要がありますか？Laravel Breezeは、[APIスタック](/docs/{{version}}/starter-kits#breeze-and-next)と[Next.jsフロントエンド実装](https://github.com/laravel/breeze-next)を提供しているため、すぐに開始できます。
