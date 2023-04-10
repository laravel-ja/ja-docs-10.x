# スターターキット

- [イントロダクション](#introduction)
- [Laravel Breeze](#laravel-breeze)
    - [インストール](#laravel-breeze-installation)
    - [BreezeとBlade](#breeze-and-blade)
    - [BreezeとReact／Vue](#breeze-and-inertia)
    - [BreezeとNext.js／API](#breeze-and-next)
- [Laravel Jetstream](#laravel-jetstream)

<a name="introduction"></a>
## イントロダクション

新しいLaravelアプリケーションの構築をすぐに取りかかれるようするため、認証とアプリケーションのスターターキットを提供しています。これらのキットはアプリケーションのユーザーを登録および認証するために必要なルート、コントローラ、ビューを自動的にスカフォールドします。

皆さんがこうしたスターターキットを使用してくれるのは大歓迎ですが、これらは必須でありません。Laravelの真新しいコピーをインストールするだけで、自分自身のアプリケーションを自由にゼロから構築できます。いずれにせよ、みなさんが素晴らしいものを作り上げるのはわかっています！

<a name="laravel-breeze"></a>
## Laravel Breeze

[Laravel Breeze](https://github.com/laravel/breeze)は、ログイン、ユーザー登録、パスワードリセット、メール確認、パスワード確認など、すべての[認証機能](/docs/{{version}}/authentication)を最小かつシンプルにLaravelへ実装したものです。さらに、Breezeには、ユーザーが名前、電子メールアドレス、パスワードを更新できるシンプルな「プロファイル」ページが含まれています。

Laravel Breezeのデフォルトのビュー層は、[Tailwind CSS](https://tailwindcss.com)でスタイルした、シンプルな[Bladeテンプレート](/docs/{{version}}/blade)で構成しています。また、VueやReactと[Inertia](https://inertiajs.com)を使用したアプリケーションのスカフォールドを作ることも可能です。

Breezeは、新しいLaravelアプリケーションを始めるための素晴らしい出発点となり、Bladeテンプレートを[Laravel Livewire](https://laravel-livewire.com)を使用し、レベルを上げる計画をしているプロジェクトにも最適な選択肢です。

<img src="https://laravel.com/img/docs/breeze-register.png">

#### Laravel Bootcamp

Laravelが初めての方は、気軽に[Laravel Bootcamp](https://bootcamp.laravel.com)に飛び込んでみてください。Laravel Bootcampでは、Breezeを使用して最初のLaravelアプリケーションを構築する手順が説明されています。LaravelとBreezeのすべてを知るには最適な方法です。

<a name="laravel-breeze-installation"></a>
### インストール

まず、[新規Laravelアプリケーションの作成](/docs/{{version}}/installation)、データベースの設定、[データベースマイグレーション](/docs/{{version}}/migrations)を実行してください。Laravelアプリケーションを新規作成したら、Composerを使用してLaravel Breezeをインストールできます。

```shell
composer require laravel/breeze --dev
```

Breezeをインストールしたら、以下のドキュメントで説明するBreeze「スタック」のいずれかを使用し、アプリケーションのスカフォールドを生成できます。

<a name="breeze-and-blade"></a>
### BreezeとBlade

ComposerでLaravel Breezeパッケージをインストールしたら、`breeze:install` Artisanコマンドを実行してください。このコマンドは、認証ビュー、ルート、コントローラ、およびその他のリソースをアプリケーションへリソース公開します。Laravel Breezeは、すべてのコードをアプリケーションへリソース公開するため、その機能と実装を完全に制御し、可視化することができます。

デフォルトのBreeze「スタック」はBladeスタックで、シンプルな[Bladeテンプレート](/docs/{{version}}/blade)を使用して、アプリケーションのフロントエンドをレンダします。Bladeスタックは、`breeze:install`コマンドを追加引数なしで実行することでインストールできます。Breezeの雛形をインストールしたら、アプリケーションのフロントエンドアセットをコンパイルする必要があります。

```shell
php artisan breeze:install

php artisan migrate
npm install
npm run dev
```

続いて、Webブラウザでアプリケーションの`/login`または`/register`のURLにアクセスしてください。Breezeのすべてのルートは、`routes/auth.php'ファイル内に定義しています。

<a name="dark-mode"></a>
#### ダークモード

アプリケーションのフロントエンドをスカフォールドするとき、「ダークモード」のサポートをBreezeへさせたい場合は、`breeze:install`コマンドを実行時に、`--dark`ディレクティブを指定するだけです。

```shell
php artisan breeze:install --dark
```

> **Note**
> アプリケーションのCSSとJavaScriptのコンパイルについて詳しく知りたい方は、Laravelの[Viteドキュメント](/docs/{{version}}/vite#running-vite)を参照してください。

<a name="breeze-and-inertia"></a>
### BreezeとReact／Vue

Laravel Breezeは、[Inertia](https://inertiajs.com)フロントエンド実装により、ReactとVueを使用するスカフォールドも提供しています。Inertiaは、従来のサーバサイドルーティングとコントローラを使用して、モダンなシングルページのReactとVueのアプリケーションを構築することができます。

Inertiaは、ReactとVueが持つフロントエンドのパワーと、Laravelの驚異的なバックエンドの生産性、そして[Vite](https://vitejs.dev)の軽快なコンパイルを組み合わせた開発を楽しめます。Inertiaスタックを使用するには、`breeze:install` Artisanコマンドを実行する際に、使用するスタックとして`vue`または`react`を指定します。Breezeの雛形をインストールしたら、アプリケーションのフロントエンドアセットをコンパイルする必要があります。

```shell
php artisan breeze:install vue

# もしくは

php artisan breeze:install react

php artisan migrate
npm install
npm run dev
```

続いて、Webブラウザでアプリケーションの`/login`または`/register`のURLにアクセスしてください。Breezeのすべてのルートは、`routes/auth.php'ファイル内に定義しています。

<a name="server-side-rendering"></a>
#### サーバサイドレンダリング

Breezeの[Inertia SSR](https://inertiajs.com/server-side-rendering)のscaffoldサポートを使う場合は、`breeze:install`コマンドを実行時に、`ssr`オプションを指定してください。

```shell
php artisan breeze:install vue --ssr
php artisan breeze:install react --ssr
```

<a name="breeze-and-next"></a>
### BreezeとNext.js／API

Laravel Breezeは、[Next](https://nextjs.org)や[Nuxt](https://nuxtjs.org)などのモダンなJavaScriptアプリケーションで認証するAPIもスカフォールドできます。これを使い始めるには、`breeze:install` Artisanコマンドを実行する時に、`api`を希望スタックとして指定します。

```shell
php artisan breeze:install api

php artisan migrate
```

インストール中、Breezeはアプリケーションの`.env`ファイルへ、`FRONTEND_URL`環境変数を追加します。このURLは、JavaScriptアプリケーションのURLである必要があります。ローカル開発では、これは通常`http://localhost:3000`になります。さらに、`APP_URL`を`http://localhost:8000`に設定する必要があります。これは、`serve` Artisanコマンドで使用されるデフォルトのURLです。

<a name="next-reference-implementation"></a>
#### Next.jsリファレンス実装

ついに、このバックエンドとお好みのフロントエンドを組み合わせる準備ができました。BreezeフロントエンドのNextリファレンス実装は[GitHubで公開](https://github.com/laravel/breeze-next)しています。このフロントエンドはLaravelがメンテナンスし、Breezeが提供する従来のBladeスタックやInertiaスタックと同じユーザーインターフェイスを備えています。

<a name="laravel-jetstream"></a>
## Laravel Jetstream

Laravel Breezeは、Laravelアプリケーションを構築するためのシンプルで最小限の開始点を提供しますが、Jetstreamはより堅牢な機能と、追加のフロントエンドテクノロジースタックで、その機能を強化します。**Laravelを初めて使用する場合は、Laravel Jetstreamへ進む前に、Laravel Breezeで勘所を掴むことをおすめします。**

Jetstreamは、Laravelのために美しくデザインされたアプリケーションのスカフォールドを提供し、ログイン、登録、電子メール検証、二要素認証、セッション管理、Laravel Sanctum経由のAPIサポート、およびオプションのチーム管理を備えています。Jetstreamは、[Tailwind CSS](https://tailwindcss.com)を使用して設計しており、[Livewire](https://laravel-livewire.com)または[Inertia](https://inertiajs.com)駆動のフロントエンドスカフォールドから選択可能です。

Laravel Jetstreamをインストールするための完全なドキュメントは、[公式Jetstreamドキュメント](https://jetstream.laravel.com/3.x/introduction.html)にあります。
