# リリースノート

- [バージョニング規約](#versioning-scheme)
- [サポートポリシー](#support-policy)
- [Laravel 10](#laravel-10)

<a name="versioning-scheme"></a>
## バージョニング規約

Laravelとファーストパーティパッケージは、[セマンティックバージョニング](https://semver.org)にしたがっています。メジャーなフレームのリリースは、毎年（第１四半期に）リリースします。マイナーとパッチリリースはより頻繁に毎週リリースします。マイナーとパッチリリースは、**決して**ブレーキングチェンジを含みません

あなたのアプリケーションやパッケージから、Laravelフレームワーク、もしくはコンポーネントを参照する場合は、Laravelのメジャーリリースには重大な変更が含まれているため、常に`^10.0`などのようにバージョンを指定する必要があります。ただし、私たちは１日かからずに新しいメジャーリリースへ更新できるように、常に努力しています。

<a name="named-arguments"></a>
#### 名前付き引数

[名前付き引数](https://www.php.net/manual/ja/functions.arguments.php#functions.named-arguments)は、Laravelの下位互換性ガイドラインの対象外です。Laravelコードベースを改善するために、必要に応じて関数の引数の名前を変更することもできます。したがって、Laravelメソッドを呼び出すときに名前付き引数を使用する場合は、パラメーター名が将来変更される可能性があることを理解した上で、慎重に行う必要があります。

<a name="support-policy"></a>
## サポートポリシー

Laravelのすべてのリリースは、バグフィックスは１８ヶ月、セキュリティフィックスは２年です。Lumenのようなその他の追加ライブラリでは、最新のメジャーリリースのみでバグフィックスを受け付けています。また、[Laravelがサポートする](/docs/{{version}}/database#introduction)データベースのサポートについても確認してください。


<div class="overflow-auto">

| バージョン | PHP (*) | リリース | バグフィックス期日 | セキュリティ修正期日 |
| -------- | -------- | ------- | ------------------ | ---------------------- |
| 8 | 7.3 - 8.1 | ２０２０年９月８日 | ２０２２年７月２６日 | ２０２３年１月２４日 |
| 9 | 8.0 - 8.2 | ２０２２年２月８日 | ２０２３年８月８日 | ２０２４年２月６日 |
| 10 | 8.1 - 8.2 | ２０２３年２月１４日 | ２０２４年８月６日 | ２０２５年２月４日 |
| 11 | 8.2 | ２０２４年第１四半期 | ２０２５年８月５日 | ２０２６年２月３日 |

</div>

<div class="version-colors">
    <div class="end-of-life">
        <div class="color-box"></div>
        <div>End of life</div>
    </div>
    <div class="security-fixes">
        <div class="color-box"></div>
        <div>Security fixes only</div>
    </div>
</div>

(*) 対応PHPバージョン

<a name="laravel-10"></a>
## Laravel 10

ご存知かもしれませんが、Laravel8のリリース時から、Laravelは年次リリースに移行しました。以前は、メジャーバージョンを６か月ごとにリリースしていました。この変更はコミュニティのメンテナンスの負担を軽減することと、開発チームが互換性を失う変更を加えることなく、驚くべき強力な新機能を出荷する試みができることを目的としています。そのため、下位互換性を損なうことなく、さまざまな堅牢な機能をLaravel9へ取り入れました。

したがって、現在のリリースへ優れた新機能を導入するこの取り組みにより、将来の「メジャー」リリースが主にアップストリームの依存関係のアップグレードなど、「メンテナンス」タスクに使用されるようになります。これは、これらのリリースノートに記載されています。

Laravel10は、Laravel9.xで行った改良を続け、アプリケーションの全スケルトンメソッド、およびフレームワーク全体でクラスを生成するために使用する全スタブファイルへ、引数と戻り値の型を導入しました。また、外部プロセスの開始と操作のために、開発者向けの新しい抽象化レイヤーを導入しました。更に、Laravel Pennantを導入し、アプリケーションの「機能フラグ」を管理するための素晴らしいアプローチを提供しました。

<a name="php-8"></a>
### PHP8.1

Laravel10.xは、PHPバージョン8.1以降が必要です。

<a name="types"></a>
### 型

_アプリケーションのスケルトンとスタブのタイプヒントは、[Nuno Maduro](https://github.com/nunomaduro)による貢献です_。

最初のリリース時、Laravelは当時のPHPで利用可能なタイプヒントの機能をすべて利用していました。しかし、その後、プリミティブ型ヒントの追加、戻り値型、ユニオン型など、多くの新機能がPHPに追加されました。

Laravel10.xでは、アプリケーションのスケルトン、およびフレームワークが利用するすべてのスタブを徹底的に更新し、すべてのメソッドシグネチャへ引数と戻り値の型を導入しました。また、無関係な「ドックブロック」タイプヒント情報を削除しました。

この変更は、既存のアプリケーションと完全に後方互換性があります。したがって、こうしたタイプヒントを持たない既存のアプリケーションは、引き続き正常に動作します。

<a name="laravel-pennant"></a>
### Laravel Pennant

_Laravel Pennantは、[Tim MacDonald](https://github.com/timacdonald)による貢献です_。

新しいファーストパーティパッケージ、Laravel Pennantをリリースしました。Laravel Pennantは、アプリケーションの機能フラグを管理するための軽量で合理的なアプローチを提供します。Pennantには、メモリ内の`array`ドライバと、永続的な機能ストレージを使用する`database`ドライバが含まれており、導入してすぐに使えます。

機能は、`Feature::define`メソッドで簡単に定義できます。

```php
use Laravel\Pennant\Feature;
use Illuminate\Support\Lottery;

Feature::define('new-onboarding-flow', function () {
    return Lottery::odds(1, 10);
});
```

機能を定義すると、現在のユーザーがその機能へアクセスできるかを簡単に判定できるようになります。

```php
if (Feature::active('new-onboarding-flow')) {
    // ...
}
```

もちろん、Bladeディレクティブでも使用可能で、便利に使えます。

```blade
@feature('new-onboarding-flow')
    <div>
        <!-- ... -->
    </div>
@endfeature
```

Pennantは、さらに高度な多くの機能とAPIを提供しています。詳しくは、[Pennantの完全なドキュメント](/docs/{{version}}/pennant)を参照してください。

<a name="process"></a>
### プロセス操作

_プロセス抽象レイヤは、[Nuno Maduro](https://github.com/nunomaduro)と[Taylor Otwell](https://github.com/taylorotwell)による貢献です_。

Laravel10.xでは、新しい`Process`ファサードを介して外部プロセスを起動し、操作するための美しい抽象化レイヤーを導入しています。

```php
use Illuminate\Support\Facades\Process;

$result = Process::run('ls -la');

return $result->output();
```

プロセスはプールで開始することもでき、便利に並行プロセスの実行と管理が可能です。

```php
use Illuminate\Process\Pool;
use Illuminate\Support\Facades\Process;

[$first, $second, $third] = Process::concurrently(function (Pool $pool) {
    $pool->command('cat first.txt');
    $pool->command('cat second.txt');
    $pool->command('cat third.txt');
});

return $first->output();
```

更に、テストで便利なように、プロセスをFakeできます。

```php
Process::fake();

// ...

Process::assertRan('ls -la');
```

プロセスと操作の詳細は、[プロセスの包括的なドキュメント](/docs/{{version}}/processes)を参照してください。

<a name="test-profiling"></a>
### テストプロファイル

_テストプロファイルは、[Nuno Maduro](https://github.com/nunomaduro)による貢献です_

Artisan `test`コマンドへ、新しい`--profile`オプションを追加し、アプリケーションで最も遅いテストを簡単に特定できるようになりました。

```shell
php artisan test --profile
```

使いやすいように、最も遅いテストはCLI出力内に直接表示します。

<p align="center">
    <img width="100%" src="https://user-images.githubusercontent.com/5457236/217328439-d8d983ec-d0fc-4cde-93d9-ae5bccf5df14.png"/>
</p>

<a name="pest-scaffolding"></a>
### Pestスカフォールド

新しいLaravelプロジェクトで、Pest testをデフォルトでスカフォールドするようになりました。この機能をオプトインするには、Laravelのインストーラで新しいアプリケーションを作成するときに、`--pest`フラグを指定します。

```shell
laravel new example-application --pest
```

<a name="generator-cli-prompts"></a>
### ジェネレータCLIプロンプト

_ジェネレータCLIプロンプトは、[Jess Archer](https://github.com/jessarcher)による貢献です_。

フレームワークの開発者体験を向上させるため、Laravelの組み込み`make`コマンドは、すべて入力が不要になりました。入力なしでコマンドを呼び出すと、必要な引数を入力するようプロンプトされます。

```shell
php artisan make:controller
```

<a name="horizon-telescope-facelift"></a>
### Horizon／Telescopeの改装

[Horizon](/docs/{{version}}/horizon)と[Telescope](/docs/{{version}}/telescope)は、タイポグラフィー、余白、デザインの改善を含む、新鮮でモダンな外観へ更新しました。

<img src="https://laravel.com/img/docs/horizon-example.png">
