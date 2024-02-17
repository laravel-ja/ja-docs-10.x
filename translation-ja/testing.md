# テスト: テストの準備

- [イントロダクション](#introduction)
- [環境](#environment)
- [テストの作成](#creating-tests)
- [テストの実行](#running-tests)
    - [テストを並列で実行](#running-tests-in-parallel)
    - [テストカバレージのレポート](#reporting-test-coverage)
    - [テストのプロファイル](#profiling-tests)

<a name="introduction"></a>
## イントロダクション

Laravelはユニットテストも考慮して構築されています。実際、PHPUnitをサポートしており、最初から含まれています。アプリケーションのために`phpunit.xml`ファイルも最初から準備されています。さらにフレームワークはアプリケーションを記述的にテストするために便利なヘルパメソッドも用意しています。

デフォルトでは、アプリケーションの`tests`ディレクトリには、`Feature`と`Unit`の２つのディレクトリを用意しています。単体テストは、コードの非常に小さな孤立した部分に焦点を当てたテストです。実際、ほとんどの単体テストはおそらく単一のメソッドに焦点を合わせています。「ユニット」テストディレクトリ内のテストはLaravelアプリケーションを起動しないため、アプリケーションのデータベースやその他のフレームワークサービスにアクセスできません。

機能テストでは、複数のオブジェクトが相互作用する方法や、JSONエンドポイントへの完全なHTTPリクエストなど、コードの広い部分をテストします。**一般的に、ほとんどのテストは機能テストである必要があります。これらのタイプのテストは、システム全体が意図したとおりに機能しているという信頼性を一番提供します。**

`ExampleTest.php`ファイルは`Feature`と`Unit`の両方のテストディレクトリで提供されます。新しいLaravelアプリケーションをインストールした後なら、`vendor/bin/phpunit`または`php artisan test`コマンドを実行してテストを実行できます。

<a name="environment"></a>
## 環境

テストを実行すると、Laravelは`phpunit.xml`ファイルで定義してある[設定環境](/docs/{{version}}/configuration#environment-configuration)により、設定環境を自動的に`testing`に設定します。Laravelはまた、テスト中にセッションとキャッシュを`array`ドライバに自動的に設定します。つまり、テスト中のセッションまたはキャッシュデータが保持されることはありません。

必要に応じて、他のテスト環境設定値を自由に定義できます。`testing`環境変数はアプリケーションの`phpunit.xml`ファイルで設定していますが、テストを実行する前は必ず`config:clear` Artisanコマンドを使用して設定のキャッシュをクリアしてください。

<a name="the-env-testing-environment-file"></a>
#### `.env.testing`環境ファイル

さらに、プロジェクトのルートに`.env.testing`ファイルを作成することもできます。このファイルは、PHPUnitテストを実行するとき、または`--env=testing`オプションを指定してArtisanコマンドを実行するときに、`.env`ファイルの代わりに使用されます。

<a name="the-creates-application-trait"></a>
#### `CreatesApplication`トレイト

Laravelには、アプリケーションのベース`TestCase`クラスに適用されている`CreatesApplication`トレイトがあります。このトレイトは、テストを実行する前に、Laravelアプリケーションをブートストラップする`createApplication`メソッドを持っています。Laravelの並列テスト機能など、いくつかの機能がこのトレイトへ依存しているため、このtraitを元の場所へ残しておくことは重要です。

<a name="creating-tests"></a>
## テストの作成

新しいテストケースを作成するには、`make:test` Artisanコマンドを使用します。デフォルトでは、テストは`tests/Feature`ディレクトリへ配置されます。

```shell
php artisan make:test UserTest
```

`tests/Unit`ディレクトリ内にテストを作成したい場合は、`make:test`コマンドを実行するときに`--unit`オプションを使用します。

```shell
php artisan make:test UserTest --unit
```

[Pest PHP](https://pestphp.com)テストを作成したい場合は、`make:test`コマンドに`--pest`オプションを指定してください。

```shell
php artisan make:test UserTest --pest
php artisan make:test UserTest --unit --pest
```

> [!NOTE]
> [stubのリソース公開](/docs/{{version}}/artisan#stub-customization) を使って、Testスタブをカスタマイズできます。

テストを生成したら、[PHPUnit](https://phpunit.de)を使用する場合と同様にテストメソッドを定義します。テストを実行するには、ターミナルから`vendor/bin/phpunit`または`php artisan test`コマンドを実行します。

    <?php

    namespace Tests\Unit;

    use PHPUnit\Framework\TestCase;

    class ExampleTest extends TestCase
    {
        /**
         * 基本的なテスト例
         */
        public function test_basic_test(): void
        {
            $this->assertTrue(true);
        }
    }

> [!WARNING]
> テストクラスに独自の`setUp`メソッドを定義する場合は、親のクラスの`parent::setUp()`／`parent::tearDown()`を確実に呼び出してください。通常、`parent::setUp()`は自分の `setUp`メソッドの最初で、`parent::tearDown()`は自分の`tearDown`メソッドの最後で呼び出します。

<a name="running-tests"></a>
## テストの実行

前述のとおり、テストが書き上がったら、`phpunit`を使って実行できます。

```shell
./vendor/bin/phpunit
```

テスト実行には`phpunit`コマンドに加え、`test` Artisanコマンドも使用できます。Artisanテストランナーは、開発とデバッグを容易にするため、詳細なテストレポートを提供します

```shell
php artisan test
```

`phpunit`コマンドで使用できる引数はすべてArtisan `test`コマンドにも渡せます。

```shell
php artisan test --testsuite=Feature --stop-on-failure
```

<a name="running-tests-in-parallel"></a>
### テストを並列で実行

LaravelとPHPUnitはデフォルトで、単一のプロセス内でテストを順番に実行します。しかし、複数のプロセス間で同時にテストを実行し、テスト時間を大幅に削減できます。これを利用開始するには、"dev"の依存パッケージとして`brianium/paratest` Composerパッケージを確実にインストールしてください。次に、`test` Artisanコマンドを実行するときに、`--parallel`オプションを指定します。

```shell
composer require brianium/paratest --dev

php artisan test --parallel
```

デフォルトで、Laravelはマシンで利用可能なCPUコアの数だけ、プロセスを作成します。しかし、`--processes`オプションでプロセス数を調整できます。

```shell
php artisan test --parallel --processes=4
```

> [!WARNING]
> テストをパラレル実行すると、PHPUnitのオプション(`-do-not-cafy-result`など)が利用できない場合があります。

<a name="parallel-testing-and-databases"></a>
#### 並列テストとデータベース

プライマリデータベース接続を設定さえすれば、Laravelは、テストを実行する並列プロセスごとに、テストデータベースの作成とマイグレーションを自動的に処理します。テストデータベースは、プロセスごとに一意のプロセストークンをサフィックス化します。たとえば、２つの並列テストプロセスがある場合、Laravelは`your_db_test_1`と`your_db_test_2`テストデータベースを作成して使用します。

デフォルトでは、テストデータベースは`test` Artisanコマンドへの呼び出し間で持続的に保持され、後続の`test`呼び出しで再使用できます。ただし、`--recreate-databases`オプションを使用してそれらを再作成できます。

```shell
php artisan test --parallel --recreate-databases
```

<a name="parallel-testing-hooks"></a>
#### 並列テストフック

アプリケーションのテストでときどき、複数のテストプロセスにより安全に使用される特定のリソースを準備する必要があるかもしれません。

`ParallelTesting`ファサードを使用して、プロセスやテストケースの`setUp`と`tearDown`で実行するコードを指定できます。指定するクロージャは、プロセストークンと現在のテストケースを含む`$token`と`$testcase`変数を受け取ります。

    <?php

    namespace App\Providers;

    use Illuminate\Support\Facades\Artisan;
    use Illuminate\Support\Facades\ParallelTesting;
    use Illuminate\Support\ServiceProvider;
    use PHPUnit\Framework\TestCase;

    class AppServiceProvider extends ServiceProvider
    {
        /**
         * 全アプリケーションサービスの初期起動処理
         */
        public function boot(): void
        {
            ParallelTesting::setUpProcess(function (int $token) {
                // ...
            });

            ParallelTesting::setUpTestCase(function (int $token, TestCase $testCase) {
                // ...
            });

            // テストデータベースが作成されたときに実行される
            ParallelTesting::setUpTestDatabase(function (string $database, int $token) {
                Artisan::call('db:seed');
            });

            ParallelTesting::tearDownTestCase(function (int $token, TestCase $testCase) {
                // ...
            });

            ParallelTesting::tearDownProcess(function (int $token) {
                // ...
            });
        }
    }

<a name="accessing-the-parallel-testing-token"></a>
#### 並列テストトークンへのアクセス

アプリケーションのテストコードの他の場所から、現在の並列プロセスの「トークン」にアクセスしたい場合は、`token`メソッドを使用します。このトークンは個々のテストプロセスのための一意な文字列の識別子であり、並列テストプロセス間でリソースを分割するために使用できます。たとえば、Laravelは各並行テストプロセスで作成するテストデータベースの末尾に、このトークンを自動的に付加します。

    $token = ParallelTesting::token();

<a name="reporting-test-coverage"></a>
### テストカバレージのレポート

> [!WARNING]
> この機能を使用するには、[Xdebug](https://xdebug.org)、または[PCOV](https://pecl.php.net/package/pcov)が必要です。

アプリケーションのテストを実行する際に、テストケースが実際にアプリケーションコードをどの程度カバーしているかどうか、また、テストを実行する際にどれだけのアプリケーションコードが使用されているかを確認したいと思うことでしょう。これを行うには、`test`コマンドを実行するときに、`--coverage`オプションを指定します。

```shell
php artisan test --coverage
```

<a name="enforcing-a-minimum-coverage-threshold"></a>
#### 最低限度のカバーレージ基準の強制

`min`オプションを使用すると、アプリケーションのテストカバレッジの最小値を定義できます。この閾値を満たさない場合、テストスイートは失敗します。

```shell
php artisan test --coverage --min=80.3
```

<a name="profiling-tests"></a>
### テストのプロファイル

Artisanテストランナは、アプリケーションの最も遅いテストをリストアップする、便利なメカニズムも用意しています。`test`コマンドへ`--profile`オプションを付けて起動すると、最も遅い１０テストのリストが表示され、テストスイートを高速化するため、どのテストを改善できるかを簡単に調査できます。

```shell
php artisan test --profile
```
