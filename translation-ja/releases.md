# リリースノート

- [バージョニング規約](#versioning-scheme)
- [サポートポリシー](#support-policy)
- [Laravel 10](#laravel-10)

<a name="versioning-scheme"></a>
## バージョニング規約

Laravel and its other first-party packages follow [Semantic Versioning](https://semver.org). Major framework releases are released every year (~Q1), while minor and patch releases may be released as often as every week. Minor and patch releases should **never** contain breaking changes.

When referencing the Laravel framework or its components from your application or package, you should always use a version constraint such as `^10.0`, since major releases of Laravel do include breaking changes. However, we strive to always ensure you may update to a new major release in one day or less.

<a name="named-arguments"></a>
#### 名前付き引数

[名前付き引数](https://www.php.net/manual/en/functions.arguments.php#functions.named-arguments)は、Laravelの下位互換性ガイドラインの対象外です。Laravelコードベースを改善するために、必要に応じて関数の引数の名前を変更することもできます。したがって、Laravelメソッドを呼び出すときに名前付き引数を使用する場合は、パラメーター名が将来変更される可能性があることを理解した上で、慎重に行う必要があります。

<a name="support-policy"></a>
## サポートポリシー

Laravelのすべてのリリースは、バグフィックスは１８ヶ月、セキュリティフィックスは２年です。Lumenのようなその他の追加ライブラリでは、最新のメジャーリリースのみでバグフィックスを受け付けています。また、[Laravelがサポートする](/docs/{{version}}/database#introduction)データベースのサポートについても確認してください。


<div class="overflow-auto">

| バージョン | PHP (*) | リリース | バグフィックス期日 | セキュリティ修正期日 |
| -------- | -------- | ------- | ------------------ | ---------------------- |
| 8 | 7.3 - 8.1 | ２０２０年９月８日 | ２０２２年７月２６日 | ２０２３年１月２４日 |
| 9 | 8.0 - 8.2 | ２０２２年２月８日 | ２０２３年８月８日 | ２０２４年２月６日 |
| 10 | 8.1 - 8.2 | February 14th, 2023 | August 6th, 2024 | February 4th, 2025 |
| 11 | 8.2 | Q1 2024 | August 5th, 2025 | February 3rd, 2026 |

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

As you may know, Laravel transitioned to yearly releases with the release of Laravel 8. Previously, major versions were released every 6 months. This transition is intended to ease the maintenance burden on the community and challenge our development team to ship amazing, powerful new features without introducing breaking changes. Therefore, we have shipped a variety of robust features to Laravel 9 without breaking backwards compatibility.

したがって、現在のリリースへ優れた新機能を導入するこの取り組みにより、将来の「メジャー」リリースが主にアップストリームの依存関係のアップグレードなど、「メンテナンス」タスクに使用されるようになります。これは、これらのリリースノートに記載されています。

Laravel 10 continues the improvements made in Laravel 9.x by introducing argument and return types to all application skeleton methods, as well as all stub files used to generate classes throughout the framework. In addition, a new, developer-friendly abstraction layer has been introduced for starting and interacting with external processes. Further, Laravel Pennant has been introduced to provide a wonderful approach to managing your application's "feature flags".

<a name="php-8"></a>
### PHP 8.1

Laravel 10.x requires a minimum PHP version of 8.1.

<a name="types"></a>
### Types

_Application skeleton and stub type-hints were contributed by [Nuno Maduro](https://github.com/nunomaduro)_.

On its initial release, Laravel utilized all of the type-hinting features available in PHP at the time. However, many new features have been added to PHP in the subsequent years, including additional primitive type-hints, return types, and union types.

Laravel 10.x thoroughly updates the application skeleton and all stubs utilized by the framework to introduce argument and return types to all method signatures. In addition, extraneous "doc block" type-hint information has been deleted:

```php
<?php

namespace App\Http\Controllers;

use App\Models\Flight;
use Illuminate\Http\RedirectResponse;
use Illuminate\Http\Request;
use Illuminate\Http\Response;

class FlightController extends Controller
{
    /**
     * Display a listing of the resource.
     */
    public function index(): Response
    {
        //
    }

    /**
     * Display the specified resource.
     */
    public function show(Flight $flight): Response
    {
        //
    }

    // ...

}
```

This change is entirely backwards compatible with existing applications. Therefore, existing applications that do not have these type-hints will continue to function normally.

<a name="laravel-pennant"></a>
### Laravel Pennant

_Laravel Pennant was developed by [Tim MacDonald](https://github.com/timacdonald)_.

A new first-party package, Laravel Pennant, has been released. Laravel Pennant offers a light-weight, streamlined approach to managing your application's feature flags. Out of the box, Pennant includes an in-memory `array` driver and a `database` driver for persistent feature storage.

Features can be easily defined via the `Feature::define` method:

```php
use Laravel\Pennant\Feature;
use Illuminate\Support\Lottery;

Feature::define('new-onboarding-flow', function () {
    return Lottery::odds(1, 10);
});
```

Once a feature has been defined, you may easily determine if the current user has access to the given feature:

```php
if (Feature::active('new-onboarding-flow')) {
    // ...
}
```

Of course, for convenience, Blade directives are also available:

```blade
@feature('new-onboarding-flow')
    <div>
        <!-- ... -->
    </div>
@endfeature
```

Pennant offers a variety of more advanced features and APIs. For more information, please consult the [comprehensive Pennant documentation](/docs/{{version}}/pennant).

<a name="process"></a>
### Process Interaction

_The process abstraction layer was contributed by [Nuno Maduro](https://github.com/nunomaduro) and [Taylor Otwell](https://github.com/taylorotwell)_.

Laravel 10.x introduces a beautiful abstraction layer for starting and interacting with external processes via a new `Process` facade:

```php
use Illuminate\Support\Facades\Process;

$result = Process::run('ls -la');

return $result->output();
```

Processes may even be started in pools, allowing for the convenient execution and management of concurrent processes:

```php
use Illuminate\Process\Pool;
use Illuminate\Support\Facades\Pool;

[$first, $second, $third] = Process::concurrently(function (Pool $pool) {
    $pool->command('cat first.txt');
    $pool->command('cat second.txt');
    $pool->command('cat third.txt');
});

return $first->output();
```

In addition, processes may be faked for convenient testing:

```php
Process::fake();

// ...

Process::assertRan('ls -la');
```

For more information on interacting with processes, please consult the [comprehensive process documentation](/docs/{{version}}/processes).

<a name="test-profiling"></a>
### Test Profiling

_Test profiling was contributed by [Nuno Maduro](https://github.com/nunomaduro)_.

The Artisan `test` command has received a new `--profile` option that allows you to easily identify the slowest tests in your application:

```shell
php artisan test --profile
```

For convenience, the slowest tests will be displayed directly within the CLI output:

<p align="center">
    <img width="100%" src="https://user-images.githubusercontent.com/5457236/217328439-d8d983ec-d0fc-4cde-93d9-ae5bccf5df14.png"/>
</p>

<a name="pest-scaffolding"></a>
### Pest Scaffolding

New Laravel projects may now be created with Pest test scaffolding by default. To opt-in to this feature, provide the `--pest` flag when creating a new application via the Laravel installer:

```shell
laravel new example-application --pest
```

<a name="generator-cli-prompts"></a>
### Generator CLI Prompts

_Generator CLI prompts were contributed by [Jess Archer](https://github.com/jessarcher)_.

To improve the framework's developer experience, all of Laravel's built-in `make` commands no longer require any input. If the commands are invoked without input, you will be prompted for the required arguments:

```shell
php artisan make:controller
```

<a name="horizon-telescope-facelift"></a>
### Horizon / Telescope Facelift

[Horizon](/docs/{{version}}/horizon) and [Telescope](/docs/{{version}}/telescope) have been updated with a fresh, modern look including improved typography, spacing, and design:

<img src="https://laravel.com/img/docs/horizon-example.png">
