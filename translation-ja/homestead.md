# Laravel Homestead

- [イントロダクション](#introduction)
- [インストールと設定](#installation-and-setup)
    - [最初の段階](#first-steps)
    - [Homestead設定](#configuring-homestead)
    - [Nginxサイトの構成](#configuring-nginx-sites)
    - [サービスの構成](#configuring-services)
    - [Vagrant Boxの実行](#launching-the-vagrant-box)
    - [プロジェクトごとのインストール](#per-project-installation)
    - [オプション機能のインストール](#installing-optional-features)
    - [エイリアス](#aliases)
- [Homesteadのアップデート](#updating-homestead)
- [日常の使用方法](#daily-usage)
    - [SSH接続](#connecting-via-ssh)
    - [サイトの追加](#adding-additional-sites)
    - [環境変数](#environment-variables)
    - [ポート](#ports)
    - [PHPバージョン](#php-versions)
    - [データベース接続](#connecting-to-databases)
    - [データベースのバックアップ](#database-backups)
    - [Cronスケジュール設定](#configuring-cron-schedules)
    - [Mailpitの設定](#configuring-mailpit)
    - [Minioの設定](#configuring-minio)
    - [Laravel Dusk](#laravel-dusk)
    - [環境の共有](#sharing-your-environment)
- [デバッグとプロファイリング](#debugging-and-profiling)
    - [XdebugによるWebリクエストのデバッグ](#debugging-web-requests)
    - [CLIアプリケーションのデバッグ](#debugging-cli-applications)
    - [Blackfireによるアプリケーションプロファイリング](#profiling-applications-with-blackfire)
- [ネットワークインターフェイス](#network-interfaces)
- [Homesteadの拡張](#extending-homestead)
- [プロパイダ固有の設定](#provider-specific-settings)
    - [VirtualBox](#provider-specific-virtualbox)

<a name="introduction"></a>
## イントロダクション

Laravelはローカル開発環境を含め、PHP開発体験全体を楽しいものにするよう努めています。[Laravel Homestead](https://github.com/laravel/homestead)は、PHP、Webサーバ、その他のサーバソフトウェアをローカルマシンにインストールしなくても、すばらしい開発環境を提供する公式のパッケージ済みVagrantボックスです。

[Vagrant](https://www.vagrantup.com)は、仮想マシンを管理およびプロビジョニングするためのシンプルでエレガントな方法を提供しています。Vagrantボックスは完全に使い捨てです。何か問題が発生した場合は、数分でボックスを破棄して再作成できます。

Homesteadは、Windows、macOS、Linuxシステムで実行でき、Nginx、PHP、MySQL、PostgreSQL、Redis、Memcached、Node、その他すばらしいLaravelアプリケーションの開発に必要なすべてのソフトウェアを含んでいます。

> [!WARNING]
> Windowsを使用している場合は、ハードウェア仮想化(VT-x)を有効にする必要があります。通常、BIOSにより有効にできます。UEFI system上のHyper-Vを使用している場合は、VT-xへアクセスするため、さらにHyper-Vを無効にする必要があります。

<a name="included-software"></a>
### 含んでいるソフトウェア

<style>
    #software-list > ul {
        column-count: 2; -moz-column-count: 2; -webkit-column-count: 2;
        column-gap: 5em; -moz-column-gap: 5em; -webkit-column-gap: 5em;
        line-height: 1.9;
    }
</style>

<div id="software-list" markdown="1">

- Ubuntu 22.04
- Git
- PHP 8.3
- PHP 8.2
- PHP 8.1
- PHP 8.0
- PHP 7.4
- PHP 7.3
- PHP 7.2
- PHP 7.1
- PHP 7.0
- PHP 5.6
- Nginx
- MySQL 8.0
- lmm
- Sqlite3
- PostgreSQL 15
- Composer
- Docker
- Node (Yarn、Bower、Bower、Grunt、Gulpを含む)
- Redis
- Memcached
- Beanstalkd
- Mailpit
- avahi
- ngrok
- Xdebug
- XHProf / Tideways / XHGui
- wp-cli

</div>

<a name="optional-software"></a>
### オプションのソフトウェア

<style>
    #software-list > ul {
        column-count: 2; -moz-column-count: 2; -webkit-column-count: 2;
        column-gap: 5em; -moz-column-gap: 5em; -webkit-column-gap: 5em;
        line-height: 1.9;
    }
</style>

<div id="software-list" markdown="1">

- Apache
- Blackfire
- Cassandra
- Chronograf
- CouchDB
- CrystalとLuckyフレームワーク
- Elasticsearch
- EventStoreDB
- Flyway
- Gearman
- Go
- Grafana
- InfluxDB
- Logstash
- MariaDB
- Meilisearch
- MinIO
- MongoDB
- Neo4j
- Oh My Zsh
- Open Resty
- PM2
- Python
- R
- RabbitMQ
- Rust
- RVM（Rubyバージョンマネージャ）
- Solr
- TimescaleDB
- Trader<small>（PHP拡張）</small>
- WebdriverとLaravel Duskユーティリティ

</div>

<a name="installation-and-setup"></a>
## インストールと設定

<a name="first-steps"></a>
### 最初の段階

Homestead環境を起動する前に、[Vagrant](https://developer.hashicorp.com/vagrant/downloads)と、サポートいている以下のプロバイダのいずれかをインストールする必要があります。

- [VirtualBox 6.1.x](https://www.virtualbox.org/wiki/Download_Old_Builds_6_1)
- [Parallels](https://www.parallels.com/products/desktop/)

これらすべてのソフトウェアパッケージは、人気のあるすべてのオペレーティングシステムへ使いやすいビジュアルインストーラを提供します。

Parallelsプロバイダを使用するには、[Parallels Vagrantプラグイン](https://github.com/Parallels/vagrant-parallels)をインストールする必要があります。これは無料です。

<a name="installing-homestead"></a>
#### Homesteadのインストール

ホストマシンへリポジトリをクローンし、Homesteadをインストールできます。自分の「ホーム」ディレクトリの中の`Homestead`フォルダへリポジトリをクローンするのことは、自分の全LaravelアプリケーションをホストしておくHomestead仮想マシンを用意するのだと考えてください。当ドキュメントでは、このディレクトリを「Homesteadディレクトリ」と呼びます。

```shell
git clone https://github.com/laravel/homestead.git ~/Homestead
```

Laravel Homesteadリポジトリのクローンを作成したら、`release`ブランチをチェックアウトする必要があります。このブランチには、Homesteadの最新の安定版リリースが常に含まれます。

```shell
cd ~/Homestead

git checkout release
```

次に、Homesteadディレクトリで`bash init.sh`コマンドを実行し、`Homestead.yaml`設定ファイルを作成します。`Homestead.yaml`ファイルは、Homesteadインストールのすべてを設定する場所です。このファイルは、Homesteadディレクトリに配置されます。

```shell
# macOS／Linux
bash init.sh

# Windows
init.bat
```

<a name="configuring-homestead"></a>
### Homestead設定

<a name="setting-your-provider"></a>
#### プロバイダの設定

`Homestead.yaml`ファイル中の`provider`キーは、Vagrantのプロバイダとして、`virtualbox`、`parallels`のどちらを使用するかを指定します。

    provider: virtualbox

> [!WARNING]
> Apple Siliconを使用する場合は、Parallelsプロバイダが必要です。

<a name="configuring-shared-folders"></a>
#### 共有フォルダの設定

`Homestead.yaml`ファイルの`folders`プロパティには、Homestead環境と共有したい全フォルダがリストされています。これらのフォルダの中のファイルが変更されると、ローカルマシンとHomestead仮想環境との間で同期されます。必要なだけ共有フォルダを設定してください！

```yaml
folders:
    - map: ~/code/project1
      to: /home/vagrant/project1
```

> [!WARNING]
> Windowsユーザーはパスを`~/`記法を使わず、代わりにたとえば`C:\Users\user\Code\project1`のように、プロジェクトのフルパスを使ってください。

すべてのアプリケーションを含む単一の大きなディレクトリをマッピングするのではなく、常に個々のアプリケーションを独自のフォルダマッピングにマッピングする必要があります。フォルダをマップするとき、仮想マシンはフォルダ内の**すべての**ファイルのすべてのディスクIOを追跡する必要があります。フォルダ内に多数のファイルがある場合、パフォーマンスの下する可能性があります。

```yaml
folders:
    - map: ~/code/project1
      to: /home/vagrant/project1
    - map: ~/code/project2
      to: /home/vagrant/project2
```

> [!WARNING]
> Homesteadを使用する場合、`.`（カレントディレクトリ）をマウントしないでください。そうすると、Vagrantはカレントフォルダを`/vagrant`へマップしない状況が起き、オプションの機能が壊れ、プロビジョン中に予期せぬ結果が起きます。

[NFS](https://developer.hashicorp.com/vagrant/docs/synced-folders/nfs)を有効にするには、フォルダのマッピングで`type`オプションを付けます。

```yaml
folders:
    - map: ~/code/project1
      to: /home/vagrant/project1
      type: "nfs"
```

> [!WARNING]
> Windows上でNFSを使用する場合は、[vagrant-winnfsd](https://github.com/winnfsd/vagrant-winnfsd)プラグインのインストールを考慮すべきでしょう。このプラグインは、Homestead仮想マシン下のファイルとディレクトリのユーザー／グループパーミッションを正しく維持します。

さらに、Vagrantの[同期フォルダ](https://developer.hashicorp.com/vagrant/docs/synced-folders/basic_usage)でサポートされている任意のオプションを、`options`キーの下に列挙して渡すことができます。

```yaml
folders:
    - map: ~/code/project1
      to: /home/vagrant/project1
      type: "rsync"
      options:
          rsync__args: ["--verbose", "--archive", "--delete", "-zz"]
          rsync__exclude: ["node_modules"]
```

<a name="configuring-nginx-sites"></a>
### Nginxサイトの設定

Nginxには詳しくない？　問題ありません。`Homestead.yaml`ファイルの`sites`プロパティでHomestead環境上のフォルダと「ドメイン」を簡単にマップできます。サイト設定のサンプルは、`Homestead.yaml`ファイルに含まれています。これも必要に応じ、Homestead環境へサイトを好きなだけ追加してください。便利に使えるよう、Homesteadは皆さんが作業するすべてのLaravelアプリケーションの仮想環境を提供します。

```yaml
sites:
    - map: homestead.test
      to: /home/vagrant/project1/public
```

`sites`プロパティをHomestead仮想環境のプロビジョニング後に変更した場合、仮想マシンのNginx設定を更新するため、`vagrant reload --provision`をターミナルで実行する必要があります。

> [!WARNING]
> Homesteadのスクリプトは可能な限り冪等性を保つように組まれています。しかしながら、プロビジョニング中に問題が起きたら、`vagrant destroy && vagrant up`コマンドを実行し、マシンを壊してから、再構築してください。

<a name="hostname-resolution"></a>
#### ホスト名の解決

Homesteadは、自動ホスト解決のために`mDNS`を使用してホスト名を公開します。`Homestead.yaml`ファイルで`hostname: homestead`を設定すると、ホストは`homestead.local`で利用できるようになります。macOS、iOS、およびLinuxデスクトップディストリビューションには、デフォルトで「mDNS」サポートが含まれています。Windowsを使用している場合は、[Bonjour Print Services for Windows](https://support.apple.com/kb/DL999?viewlocale=en_US&locale=en_US)をインストールする必要があります。

自動ホスト名の使用は、Homesteadの[プロジェクトごとのインストール](#per-project-installation)に最適です。１つのHomesteadインスタンスで複数のサイトをホストしている場合は、Webサイトの「ドメイン」をマシンの`hosts`ファイルに追加できます。`hosts`ファイルは、HomesteadサイトへのリクエストをHomestead仮想マシンにリダイレクトします。macOSおよびLinuxでは、このファイルは`/etc/hosts`にあります。Windowsでは、`C:\Windows\System32\drivers\etc\hosts`にあります。このファイルに追加する行は次のようになります。

    192.168.56.56  homestead.test

設定するIPアドレスには`Homestead.yaml`ファイルの中の値を確実に指定してください。ドメインを`hosts`ファイルへ追加したら、Webブラウザでサイトにアクセスできます。

```shell
http://homestead.test
```

<a name="configuring-services"></a>
### サービスの設定

Homesteadはデフォルトでいくつかのサービスを開始します。ただし、プロビジョニング中に有効／無効にするサービスをカスタマイズできます。たとえば、`Homestead.yaml`ファイル内の`services`オプションを変更することで、PostgreSQLを有効にし、MySQLを無効にできます。

```yaml
services:
    - enabled:
        - "postgresql"
    - disabled:
        - "mysql"
```

指定したサービスは、`enabled`および`disabled`ディレクティブの順序に基づいて開始または停止されます。

<a name="launching-the-vagrant-box"></a>
### Launching the Vagrant Box

`Homestead.yaml`のリンクを編集終えたら、Homesteadディレクトリで`vagrant up`コマンドを実行してください。Vagrantは仮想マシンを起動し、共有フォルダとNginxサイトを自動的に設定します。

仮想マシンを破壊するには、`vagrant destroy`コマンドを使用します。

<a name="per-project-installation"></a>
### プロジェクトごとにインストール

Homesteadをグローバルにインストールし、全プロジェクトで同じHomestead仮想環境を共有する代わりに、Homesteadインスタンスを管理下のプロジェクトごとに設定することもできます。プロジェクトごとにHomesteadをインストールする利点は、`Vagrantfile`をプロジェクトに用意すれば、プロジェクトに参加している他の人達も、プロジェクトのリポジトリをクローンしたあとに`vagrant up`ですぐに仕事にとりかかれることです。

Composerパッケージマネージャを使用して、Homesteadをプロジェクトにインストールできます。

```shell
composer require laravel/homestead --dev
```

Homesteadをインストールしたら、Homesteadの`make`コマンドを呼び出して、プロジェクトの`Vagrantfile`ファイルと`Homestead.yaml`ファイルを生成します。これらのファイルは、プロジェクトのルートに配置されます。`make`コマンドは、`Homestead.yaml`ファイルの`sites`および`folders`ディレクティブを自動的に構成します。

```shell
# macOS／Linux
php vendor/bin/homestead make

# Windows
vendor\\bin\\homestead make
```

次に`vagrant up`コマンドを端末で実行し、ブラウザで`http://homestead.test`のプロジェクトへアクセスしてください。自動[ホスト名解決](#hostname-resolution)を使わない場合は、`/etc/hosts`ファイルに`homestead.test`か、自分で選んだドメインのエントリーを追加する必要があることを忘れないでください。

<a name="installing-optional-features"></a>
### オプション機能のインストール

オプションのソフトウェアは、`Homestead.yaml`ファイル内の`features`オプションを使用してインストールします。ほとんどの機能は論理値で有効／無効にしますが、一部の機能は複数の構成オプションを使用できます。

```yaml
features:
    - blackfire:
        server_id: "server_id"
        server_token: "server_value"
        client_id: "client_id"
        client_token: "client_value"
    - cassandra: true
    - chronograf: true
    - couchdb: true
    - crystal: true
    - dragonflydb: true
    - elasticsearch:
        version: 7.9.0
    - eventstore: true
        version: 21.2.0
    - flyway: true
    - gearman: true
    - golang: true
    - grafana: true
    - influxdb: true
    - logstash: true
    - mariadb: true
    - meilisearch: true
    - minio: true
    - mongodb: true
    - neo4j: true
    - ohmyzsh: true
    - openresty: true
    - pm2: true
    - python: true
    - r-base: true
    - rabbitmq: true
    - rustc: true
    - rvm: true
    - solr: true
    - timescaledb: true
    - trader: true
    - webdriver: true
```

<a name="elasticsearch"></a>
#### Elasticsearch

サポートしているElasticsearchのバージョンを指定できます。これは、正確なバージョン番号(major.minor.patch)である必要があります。デフォルトのインストールでは、`homestead`という名前のクラスターを作成します。Elasticsearchにオペレーティングシステムのメモリの半分以上を割り当てないでください。そのため、Homestead仮想マシンでElasticsearchの割り当てが最低２倍あることを確認してください。

> [!NOTE]
> [Elasticsearchドキュメント](https://www.elastic.co/guide/en/elasticsearch/reference/current)をチェックして、設定をカスタマイズする方法を確認してください。

<a name="mariadb"></a>
#### MariaDB

MariaDBを有効にすると、MySQLを削除してMariaDBをインストールします。MariaDBはMySQLのそのまま置き換え可能な代替機能として通常動作します。そのため、アプリケーションのデータベース設定では、`mysql`データベースドライバをそのまま使ってください。

<a name="mongodb"></a>
#### MongoDB

デフォルト状態のMongoDBでは、データベースのユーザー名を`homestead`、パスワードを`secret`に設定します。

<a name="neo4j"></a>
#### Neo4j

デフォルト状態のNeo4jでは、データベースのユーザー名を`homestead`、パスワードを`secret`として設定します。Neo4jブラウザにアクセスするには、Webブラウザで`http://homestead.test:7474`にアクセスしてください。Neo4jクライアントのために、`7687` (Bolt)、`7474` (HTTP)、`7473` (HTTPS)ポートが用意されています。

<a name="aliases"></a>
### エイリアス

Homestead仮想マシンでBashのエイリアスを指定するには、Homesteadディレクトリにある `aliases` ファイルを編集します。

```shell
alias c='clear'
alias ..='cd ..'
```

`aliases`ファイルを更新した後、`vagrant reload --provision`コマンドを使用してHomestead仮想マシンを再プロビジョニングする必要があります。これにより、新しいエイリアスがマシンで使用できるようになります。

<a name="updating-homestead"></a>
## Homesteadのアップデート

Homesteadのアップデートを開始する前に、Homesteadディレクトリで以下のコマンドを実行して、現在の仮想マシンを確実に削除してください。

```shell
vagrant destroy
```

次に、Homesteadソースコードを更新する必要があります。リポジトリのクローンを作成した場合は、最初にリポジトリのクローンを作成した場所で次のコマンドを実行できます。

```shell
git fetch

git pull origin release
```

これらのコマンドは、GitHubリポジトリから最新のHomesteadコードをプルし、最新のタグをフェッチしてから、最新のタグ付きリリースをチェックアウトします。最新の安定版リリースバージョンは、Homesteadの[GitHubリリースページ](https://github.com/laravel/homestead/releases)にあります。

プロジェクトの`composer.json`ファイルを介してHomesteadをインストールした場合は、`composer.json`ファイルに`"laravel/homestead" : "^12"`が含まれていることを確認し、依存関係を更新する必要があります。

```shell
composer update
```

次に、`vagrant box update`コマンドを使用してVagrantボックスを更新する必要があります。

```shell
vagrant box update
```

Vagrantボックスを更新した後、Homesteadの追加の設定ファイルを更新するために、Homesteadディレクトリから`bashinit.sh`コマンドを実行する必要があります。既存の`Homestead.yaml`、`after.sh`、`aliases`ファイルを上書きするかどうか尋ねられます。

```shell
# macOS／Linux
bash init.sh

# Windows
init.bat
```

最後に、最新のVagrantインストールを利用するため、Homestead仮想マシンを再生成します。

```shell
vagrant up
```

<a name="daily-usage"></a>
## 日常の使用方法

<a name="connecting-via-ssh"></a>
### SSH接続

Homesteadディレクトリから`vagrant ssh`ターミナルコマンドを実行することにより、仮想マシンにSSH接続できます。

<a name="adding-additional-sites"></a>
### サイトの追加

Homestead環境をプロビジョニングし、実働した後に、LaravelプロジェクトをNginxサイトへ追加したいこともあるでしょう。希望するだけのLaravelプロジェクトを一つのHomestead環境上で実行できます。新しいサイトを追加するには、`Homestead.yaml`ファイルへ追加します。

```yaml
sites:
    - map: homestead.test
      to: /home/vagrant/project1/public
    - map: another.test
      to: /home/vagrant/project2/public
```

> [!WARNING]
> サイトを追加する前に、プロジェクトのディレクトリに[フォルダマッピング](#configuring-shared-folders)を確実に設定してください。

Vagrantが"hosts"ファイルを自動的に管理しない場合は、新しいサイトを追加する必要があります。このファイルはmacOSとLinuxでは、`/etc/hosts`にあります。Windowsでは、`C:\Windows\System32\drivers\etc\hosts`に位置します。

    192.168.56.56  homestead.test
    192.168.56.56  another.test

サイトを追加したら、`vagrant reload --provision`ターミナルコマンドをHomesteadディレクトリで実行します。

<a name="site-types"></a>
#### サイトタイプ

Laravelベースではないプロジェクトも簡単に実行できるようにするため、Homesteadはさまざまなタイプのサイトをサポートしています。たとえば、`statamic`サイトタイプを使えば、HomesteadにStatamicアプリケーションを簡単に追加できます。

```yaml
sites:
    - map: statamic.test
      to: /home/vagrant/my-symfony-project/web
      type: "statamic"
```

指定できるサイトタイプは`apache`、`apache-proxy`、`apigility`、`expressive`、`laravel`（デフォルト）、`proxy（nginx用）`、`silverstripe`、`statamic`、`symfony2`、`symfony4`、`zf`です。

<a name="site-parameters"></a>
#### サイトパラメータ

`params`サイトディレクティブを使用し、Nginxの`fastcgi_param`値を追加できます。

```yaml
sites:
    - map: homestead.test
      to: /home/vagrant/project1/public
      params:
          - key: FOO
            value: BAR
```

<a name="environment-variables"></a>
### 環境変数

グローバルな環境変数は、`Homestead.yaml`ファイルで追加定義できます。

```yaml
variables:
    - key: APP_ENV
      value: local
    - key: FOO
      value: bar
```

`Homestead.yaml`ファイルを更新した後、必ず`vagrant　reload　--provision`コマンドを実行してマシンを再プロビジョニングしてください。これにより、インストールしているすべてのPHPバージョンのPHP-FPM構成が更新され、`vagrant`ユーザーの環境も更新されます。

<a name="ports"></a>
### ポート

以下のポートが、Homestead環境へポートフォワードされています。

<div class="content-list" markdown="1">

- **HTTP:** 8000 &rarr; フォワード先 80
- **HTTPS:** 44300 &rarr; フォワード先 443

</div>

<a name="forwarding-additional-ports"></a>
#### 追加のフォワードポート

必要に応じて、`Homestead.yaml`ファイル内で`ports`設定エントリを定義することにより、追加のポートをVagrantボックスに転送できます。`Homestead.yaml`ファイルを更新した後は、必ず`vagrant reload --provision`コマンドを実行してマシンを再プロビジョニングしてください。

```yaml
ports:
    - send: 50000
      to: 5000
    - send: 7777
      to: 777
      protocol: udp
```

以下は、ホストマシンからVagrantボックスへマップする、Homesteadサービス追加ポートのリストです。

<div class="content-list" markdown="1">

- **SSH:** 2222 &rarr; フォワード先 22
- **ngrok UI:** 4040 &rarr; フォワード先 4040
- **MySQL:** 33060 &rarr; フォワード先 3306
- **PostgreSQL:** 54320 &rarr; フォワード先 5432
- **MongoDB:** 27017 &rarr; フォワード先 27017
- **Mailpit:** 8025 &rarr; フォワード先 8025
- **Minio:** 9600 &rarr; フォワード先 9600

</div>

<a name="php-versions"></a>
### PHPバージョン

Homesteadは、同じ仮想マシンで複数のバージョンのPHPを実行するためサポートしています。`Homestead.yaml`ファイル内の特定のサイトに使用するPHPのバージョンを指定できます。使用可能なPHPバージョンは、"5.6", "7.0", "7.1", "7.2", "7.3", "7.4"、"8.0"、"8.1"、"8.2"、"8.3"（デフォルト）です。

```yaml
sites:
    - map: homestead.test
      to: /home/vagrant/project1/public
      php: "7.1"
```

[Homestead仮想マシン内](#connecting-via-ssh)では、以下のようにCLIでサポートしているPHPバージョンのどれでも使用できます。

```shell
php5.6 artisan list
php7.0 artisan list
php7.1 artisan list
php7.2 artisan list
php7.3 artisan list
php7.4 artisan list
php8.0 artisan list
php8.1 artisan list
php8.2 artisan list
php8.3 artisan list
```

Homestead仮想マシン内から以下のコマンドを実行すれば、CLIで使用するPHPのデフォルトバージョンを変更できます。

```shell
php56
php70
php71
php72
php73
php74
php80
php81
php82
php83
```

<a name="connecting-to-databases"></a>
### データベースへの接続

`homestead`データベースは、MySQLとPostgreSQLの両方へすぐに設定できます。ホストマシンのデータベースクライアントからMySQLまたはPostgreSQLデータベースに接続するには、ポート`33060`（MySQL）または`54320`（PostgreSQL）で`127.0.0.1`へ接続してください。両方のデータベースのユーザー名とパスワードは`homestead`／`secret`です。

> [!WARNING]
> ホストマシンからデータベースに接続する場合にのみ、これらの非標準ポートを使用する必要があります。Laravelは仮想マシン内で実行するため、Laravelアプリケーションの`database`設定ファイルではデフォルトの3306ポートと5432ポートを使用しています。

<a name="database-backups"></a>
### データベースのバックアップ

Homesteadは、Homestead仮想マシンが破壊されたときに、データベースを自動的にバックアップできます。この機能を利用するには、Vagrant2.1.0以降を使用している必要があります。古いバージョンのVagrantを使用している場合は、`vagrant-triggers`プラグインをインストールする必要があります。データベースの自動バックアップを有効にするには、`Homestead.yaml`ファイルに次の行を追加します。

    backup: true

設定が完了すると、Homesteadは、`vagrant destroy`コマンドの実行時に、データベースを`.backup/mysql_backup`もしくは`.backup/postgres_backup`ディレクトリにエクスポートします。これらのディレクトリは、Homesteadをインストールしたフォルダ、または[プロジェクトごとのインストール](#per-project-installation)メソッドを使用している場合はプロジェクトのルートにできます。

<a name="configuring-cron-schedules"></a>
### cronスケジュールの設定

Laravelは、1分ごとに実行する単一の`schedule:run` Artisanコマンドをスケジュールすることにより、[cronジョブのスケジュール](/docs/{{version}}/scheduleing)に便利な方法を提供しています。`schedule:run`コマンドは、`App\Console\Kernel`クラスで定義したジョブスケジュールを調べて、どのスケジュール済みタスクを実行するかを決定します。

Homesteadサイトに対して`schedule:run`コマンドを実行する場合は、サイトを定義するときに`schedule`オプションを`true`に設定します。

```yaml
sites:
    - map: homestead.test
      to: /home/vagrant/project1/public
      schedule: true
```

サイトのcronジョブは、Homestead仮想マシンの`/etc/cron.d`ディレクトリで定義します。

<a name="configuring-mailpit"></a>
### Mailpitの設定

[Mailpit](https://github.com/axllent/mailpit)を使用すると、実際に受信者にメールを送信しなくても、送信メールを傍受して調査できます。使用するには、以下のメール設定を使用するためアプリケーションの`.env`ファイルを更新します。

```ini
MAIL_MAILER=smtp
MAIL_HOST=localhost
MAIL_PORT=1025
MAIL_USERNAME=null
MAIL_PASSWORD=null
MAIL_ENCRYPTION=null
```

Mailpitを設定したら、`http://localhost:8025`にある、Mailpitダッシュボードにアクセスできます。

<a name="configuring-minio"></a>
### Minioの設定

[Minio](https://github.com/minio/minio)は、Amazon S3互換のAPIを備えたオープンソースのオブジェクトストレージサーバです。Minioをインストールするには、`Homestead.yaml`ファイルの[オプション機能](#installing-optional-features)セクションで以下の設定オプションへ変更してください。

    minio: true

デフォルトでは、Minioはポート9600で使用できます。`http://localhost:9600`にアクセスし、Minioコントロールパネルを表示できます。デフォルトのアクセスキーは`homestead`、秘密キーは`secretkey`です。Minioにアクセスするときは、常にリージョン`us-east-1`を使用する必要があります。

Minioを使用するには、アプリケーションの`config/filesystems.php`設定ファイルで、S3ディスク設定を調整する必要があります。ディスク設定に`use_path_style_endpoint`オプションを追加し、`url`キーを`endpoint`へ変更する必要があります。

    's3' => [
        'driver' => 's3',
        'key' => env('AWS_ACCESS_KEY_ID'),
        'secret' => env('AWS_SECRET_ACCESS_KEY'),
        'region' => env('AWS_DEFAULT_REGION'),
        'bucket' => env('AWS_BUCKET'),
        'endpoint' => env('AWS_URL'),
        'use_path_style_endpoint' => true,
    ]

最後に、`.env`ファイルに次のオプションがあることを確認してください。

```ini
AWS_ACCESS_KEY_ID=homestead
AWS_SECRET_ACCESS_KEY=secretkey
AWS_DEFAULT_REGION=us-east-1
AWS_URL=http://localhost:9600
```

Minioを利用した「S3」バケットをプロビジョニングするには、`Homestead.yaml`ファイルに`buckets`ディレクティブを追加します。バケットを定義したら、ターミナルで`vagrant reload --provision`コマンドを実行する必要があります。

```yaml
buckets:
    - name: your-bucket
      policy: public
    - name: your-private-bucket
      policy: none
```

サポートしている`policy`値は、`none`、`download`、`upload`、`public`です。

<a name="laravel-dusk"></a>
### Laravel Dusk

Homestead内で[LaravelDusk](/docs/{{version}}/dusk)テストを実行するには、Homestead設定で[`webdriver`機能](#installing-optional-features)を有効にする必要があります。

```yaml
features:
    - webdriver: true
```

`webdriver`機能を有効にした後は、ターミナルで`vagrant reload --provision`コマンドを実行する必要があります。

<a name="sharing-your-environment"></a>
### 環境の共有

現在取り組んでいるものを同僚やクライアントと共有したい場合があります。Vagrantには、`vagrant share`コマンドを介したこれに対する組み込みのサポートがあります。ただし、`Homestead.yaml`ファイルで複数のサイトを構成している場合には機能しません。

この問題を解決するため、Homesteadは独自の`share`コマンドを用意しています。使い始めるには、`vagrant ssh`により[Homestead仮想マシンへSSH接続](#connecting-via-ssh)し、`share homestead.test`コマンドを実行します。このコマンドは、`Homestead.yaml`構成ファイルの`homestead.test`サイトを共有します。`homestead.test`の代わりに他の設定済みサイトを使用できます。

```shell
share homestead.test
```

コマンドを実行すると、アクティビティログと共有サイトの一般公開されているURLを含むNgrok画面が表示されます。カスタムリージョン、サブドメイン、またはその他のNgrokランタイムオプションを指定する場合は、それらを`share`コマンドへ追加できます。

```shell
share homestead.test -region=eu -subdomain=laravel
```

HTTPではなくHTTPSでコンテンツを共有する必要がある場合は、`share`の代わりに`sshare`コマンドを使用すれば可能です。

> [!WARNING]
> Vagrantは本質的に安全ではなく、`share`コマンドを実行するときに仮想マシンをインターネットに公開していることを忘れないでください。

<a name="debugging-and-profiling"></a>
## デバッグとプロファイリング

<a name="debugging-web-requests"></a>
### Xdebugを使用したWebリクエストのデバッグ

Homesteadは、[Xdebug](https://xdebug.org)を使用したステップデバッグのサポートを用意しています。たとえば、ブラウザでページにアクセスすると、PHPがIDEに接続し、実行中のコードを検査および変更できるようになります。

Xdebugはデフォルトではじめから実行しており、接続を受け付ける準備ができています。CLIでXdebugを有効にする必要がある場合は、Homestead仮想マシン内で`sudo　php　enmod　xdebug`コマンドを実行します。次に、IDEの指示に従ってデバッグを有効にします。最後に、拡張機能または[ブックマークレット](https://www.jetbrains.com/phpstorm/marklets/)を使用してXdebugをトリガーするようにブラウザを構成します。

> [!WARNING]
> Xdebugを使用すると、PHPの実行速度が大幅に低下します。Xdebugを無効にするには、Homestead仮想マシン内で`sudo phpdismod xdebug`を実行し、それからFPMサービスを再起動します。

<a name="autostarting-xdebug"></a>
#### Xdebugの自動起動

Webサーバにリクエストを送る機能テストをデバッグする場合、カスタムヘッダまたはCookieを用いデバッグをトリガーするようテストを変更するよりも、デバッグを自動開始する方が簡単です。Xdebugを強制的に自動で開始するには、Homestead仮想マシン内の`/etc/php/7.x/fpm/conf.d/20-xdebug.ini`ファイルを変更し、次の構成を追加します。

```ini
; Homestead.yamlでIPアドレスの異なるサブネットを指定している場合、このアドレスは異なるでしょう
xdebug.client_host = 192.168.10.1
xdebug.mode = debug
xdebug.start_with_request = yes
```

<a name="debugging-cli-applications"></a>
### CLIアプリケーションのデバッグ

PHP CLIアプリケーションをデバッグするには、Homestead仮想マシン内で`xphp`シェルエイリアスを使用します。

    xphp /path/to/script

<a name="profiling-applications-with-blackfire"></a>
### Blackfireを使用したアプリケーションのプロファイリング

[Blackfire](https://blackfire.io/docs/introduction)は、WebリクエストとCLIアプリケーションをプロファイリングするサービスです。コールグラフとタイムラインでプロファイルデータを表示するインタラクティブなユーザーインターフェイスを提供しています。開発、ステージング、および本番環境で使用するために構築されており、エンドユーザーのオーバーヘッドはありません。加えてBlackfireは、コードと`php.ini`設定のパフォーマンス、品質、およびセキュリティチェックも提供してくれます。

[Blackfire Player](https://blackfire.io/docs/player/index)は、プロファイリングシナリオをスクリプト化するために、Blackfireと連携して動作できるオープンソースのWebクロール、Webテスト、およびWebスクレイピングアプリケーションです。

Blackfireを有効にするには、Homestead設定ファイルの「機能（features）」設定を使用します。

```yaml
features:
    - blackfire:
        server_id: "server_id"
        server_token: "server_value"
        client_id: "client_id"
        client_token: "client_value"
```

Blackfireサーバの接続情報とクライアントの接続情報が[Blackfireアカウントで要求されます](https://blackfire.io/signup)。Blackfireには、CLIツールやブラウザ拡張機能など、アプリケーションをプロファイリングするためのさまざまなオプションが用意されています。[詳細については、Blackfireのドキュメントを確認してください](https://blackfire.io/docs/php/integrations/laravel/index)。

<a name="network-interfaces"></a>
## ネットワークインターフェイス

`Homestead.yaml`ファイルの`networks`プロパティは、Homestead仮想マシンのネットワークインターフェイスを設定します。必要な数のインターフェイスを構成できます。

```yaml
networks:
    - type: "private_network"
      ip: "192.168.10.20"
```

[bridged](https://developer.hashicorp.com/vagrant/docs/networking/public_network)インターフェイスを有効にするには、ネットワークの`bridge`設定を構成し、ネットワークタイプを`public_network`へ変更します。

```yaml
networks:
    - type: "public_network"
      ip: "192.168.10.20"
      bridge: "en1: Wi-Fi (AirPort)"
```

[DHCP](https://developer.hashicorp.com/vagrant/docs/networking/public_network#dhcp)を有効にするには、設定から`ip`オプションを削除するだけです。

```yaml
networks:
    - type: "public_network"
      bridge: "en1: Wi-Fi (AirPort)"
```

ネットワークで使用するデバイスを更新するには、ネットワークの設定へ`dev`オプションを追加します。デフォルトの`dev`値は`eth0`です。

```yaml
networks:
    - type: "public_network"
      ip: "192.168.10.20"
      bridge: "en1: Wi-Fi (AirPort)"
      dev: "enp2s0"
```

<a name="extending-homestead"></a>
## Homesteadの拡張

Homesteadディレクトリのルートにある`after.sh`スクリプトを使用して、Homesteadを拡張できます。このファイル内に、仮想マシンを適切に構成およびカスタマイズするために必要なシェルコマンドを追加します。

Homesteadをカスタマイズするときに、Ubuntuは、パッケージの元の構成を保持するか、新しい構成ファイルで上書きするかを尋ねる場合があります。これを回避するには、パッケージをインストールするときに以下のコマンドを使用して、Homesteadによって以前に作成された構成が上書きされないようにする必要があります。

```shell
sudo apt-get -y \
    -o Dpkg::Options::="--force-confdef" \
    -o Dpkg::Options::="--force-confold" \
    install package-name
```

<a name="user-customizations"></a>
### ユーザーのカスタマイズ

チームでHomesteadを使用するときは、Homesteadを微調整して、個人の開発スタイルに合わせて調整するのを推奨します。このためには、Homesteadディレクトリ（`Homestead.yaml`ファイルを含む同じディレクトリ）のルートに`user-customizations.sh`ファイルを作成します。このファイル内で、必要なカスタマイズを行うことができます。ただし、`user-customizations.sh`をバージョン管理してはいけません。

<a name="provider-specific-settings"></a>
## プロパイダ固有の設定

<a name="provider-specific-virtualbox"></a>
### VirtualBox

<a name="natdnshostresolver"></a>
#### `natdnshostresolver`

Homesteadは`natdnshostresolver`設定をデフォルトで`on`に設定します。これにより、HomesteadはホストオペレーティングシステムのDNS設定を使用できるようになります。この動作をオーバーライドする場合は、次の構成オプションを`Homestead.yaml`ファイルに追加します。

```yaml
provider: virtualbox
natdnshostresolver: 'off'
```
