# ヘルパ

- [イントロダクション](#introduction)
- [使用可能なメソッド](#available-methods)
- [その他のユーティリティ](#other-utilities)
    - [ベンチマーク](#benchmarking)
    - [パイプライン](#pipeline)
    - [抽選](#lottery)

<a name="introduction"></a>
## イントロダクション

Laravelはさまざまな、グローバル「ヘルパ」PHP関数を用意しています。これらの多くはフレームワーク自身で使用されています。便利なものが見つかれば、皆さんのアプリケーションでも大いに活用してください。

<a name="available-methods"></a>
## 使用可能なメソッド

<style>
    .collection-method-list > p {
        columns: 10.8em 3; -moz-columns: 10.8em 3; -webkit-columns: 10.8em 3;
    }

    .collection-method-list a {
        display: block;
        overflow: hidden;
        text-overflow: ellipsis;
        white-space: nowrap;
    }
</style>

<a name="arrays-and-objects-method-list"></a>
### 配列とオブジェクト

<div class="collection-method-list" markdown="1">

[Arr::accessible](#method-array-accessible)
[Arr::add](#method-array-add)
[Arr::collapse](#method-array-collapse)
[Arr::crossJoin](#method-array-crossjoin)
[Arr::divide](#method-array-divide)
[Arr::dot](#method-array-dot)
[Arr::except](#method-array-except)
[Arr::exists](#method-array-exists)
[Arr::first](#method-array-first)
[Arr::flatten](#method-array-flatten)
[Arr::forget](#method-array-forget)
[Arr::get](#method-array-get)
[Arr::has](#method-array-has)
[Arr::hasAny](#method-array-hasany)
[Arr::isAssoc](#method-array-isassoc)
[Arr::isList](#method-array-islist)
[Arr::join](#method-array-join)
[Arr::keyBy](#method-array-keyby)
[Arr::last](#method-array-last)
[Arr::map](#method-array-map)
[Arr::only](#method-array-only)
[Arr::pluck](#method-array-pluck)
[Arr::prepend](#method-array-prepend)
[Arr::prependKeysWith](#method-array-prependkeyswith)
[Arr::pull](#method-array-pull)
[Arr::query](#method-array-query)
[Arr::random](#method-array-random)
[Arr::set](#method-array-set)
[Arr::shuffle](#method-array-shuffle)
[Arr::sort](#method-array-sort)
[Arr::sortDesc](#method-array-sort-desc)
[Arr::sortRecursive](#method-array-sort-recursive)
[Arr::toCssClasses](#method-array-to-css-classes)
[Arr::undot](#method-array-undot)
[Arr::where](#method-array-where)
[Arr::whereNotNull](#method-array-where-not-null)
[Arr::wrap](#method-array-wrap)
[data_fill](#method-data-fill)
[data_get](#method-data-get)
[data_set](#method-data-set)
[head](#method-head)
[last](#method-last)
</div>

<a name="paths-method-list"></a>
### パス

<div class="collection-method-list" markdown="1">

[app_path](#method-app-path)
[base_path](#method-base-path)
[config_path](#method-config-path)
[database_path](#method-database-path)
[lang_path](#method-lang-path)
[mix](#method-mix)
[public_path](#method-public-path)
[resource_path](#method-resource-path)
[storage_path](#method-storage-path)

</div>

<a name="strings-method-list"></a>
### 文字列

<div class="collection-method-list" markdown="1">

[\__](#method-__)
[class_basename](#method-class-basename)
[e](#method-e)
[preg_replace_array](#method-preg-replace-array)
[Str::after](#method-str-after)
[Str::afterLast](#method-str-after-last)
[Str::ascii](#method-str-ascii)
[Str::before](#method-str-before)
[Str::beforeLast](#method-str-before-last)
[Str::between](#method-str-between)
[Str::betweenFirst](#method-str-between-first)
[Str::camel](#method-camel-case)
[Str::contains](#method-str-contains)
[Str::containsAll](#method-str-contains-all)
[Str::endsWith](#method-ends-with)
[Str::excerpt](#method-excerpt)
[Str::finish](#method-str-finish)
[Str::headline](#method-str-headline)
[Str::inlineMarkdown](#method-str-inline-markdown)
[Str::is](#method-str-is)
[Str::isAscii](#method-str-is-ascii)
[Str::isJson](#method-str-is-json)
[Str::isUlid](#method-str-is-ulid)
[Str::isUuid](#method-str-is-uuid)
[Str::kebab](#method-kebab-case)
[Str::lcfirst](#method-str-lcfirst)
[Str::length](#method-str-length)
[Str::limit](#method-str-limit)
[Str::lower](#method-str-lower)
[Str::markdown](#method-str-markdown)
[Str::mask](#method-str-mask)
[Str::orderedUuid](#method-str-ordered-uuid)
[Str::padBoth](#method-str-padboth)
[Str::padLeft](#method-str-padleft)
[Str::padRight](#method-str-padright)
[Str::password](#method-str-password)
[Str::plural](#method-str-plural)
[Str::pluralStudly](#method-str-plural-studly)
[Str::random](#method-str-random)
[Str::remove](#method-str-remove)
[Str::replace](#method-str-replace)
[Str::replaceArray](#method-str-replace-array)
[Str::replaceFirst](#method-str-replace-first)
[Str::replaceLast](#method-str-replace-last)
[Str::reverse](#method-str-reverse)
[Str::singular](#method-str-singular)
[Str::slug](#method-str-slug)
[Str::snake](#method-snake-case)
[Str::squish](#method-str-squish)
[Str::start](#method-str-start)
[Str::startsWith](#method-starts-with)
[Str::studly](#method-studly-case)
[Str::substr](#method-str-substr)
[Str::substrCount](#method-str-substrcount)
[Str::substrReplace](#method-str-substrreplace)
[Str::swap](#method-str-swap)
[Str::title](#method-title-case)
[Str::toHtmlString](#method-str-to-html-string)
[Str::ucfirst](#method-str-ucfirst)
[Str::ucsplit](#method-str-ucsplit)
[Str::upper](#method-str-upper)
[Str::ulid](#method-str-ulid)
[Str::uuid](#method-str-uuid)
[Str::wordCount](#method-str-word-count)
[Str::words](#method-str-words)
[str](#method-str)
[trans](#method-trans)
[trans_choice](#method-trans-choice)

</div>

<a name="fluent-strings-method-list"></a>
### Fluent文字列

<div class="collection-method-list" markdown="1">

[after](#method-fluent-str-after)
[afterLast](#method-fluent-str-after-last)
[append](#method-fluent-str-append)
[ascii](#method-fluent-str-ascii)
[basename](#method-fluent-str-basename)
[before](#method-fluent-str-before)
[beforeLast](#method-fluent-str-before-last)
[between](#method-fluent-str-between)
[betweenFirst](#method-fluent-str-between-first)
[camel](#method-fluent-str-camel)
[classBasename](#method-fluent-str-class-basename)
[contains](#method-fluent-str-contains)
[containsAll](#method-fluent-str-contains-all)
[dirname](#method-fluent-str-dirname)
[endsWith](#method-fluent-str-ends-with)
[excerpt](#method-fluent-str-excerpt)
[exactly](#method-fluent-str-exactly)
[explode](#method-fluent-str-explode)
[finish](#method-fluent-str-finish)
[headline](#method-fluent-str-headline)
[inlineMarkdown](#method-fluent-str-inline-markdown)
[is](#method-fluent-str-is)
[isAscii](#method-fluent-str-is-ascii)
[isEmpty](#method-fluent-str-is-empty)
[isNotEmpty](#method-fluent-str-is-not-empty)
[isJson](#method-fluent-str-is-json)
[isUlid](#method-fluent-str-is-ulid)
[isUuid](#method-fluent-str-is-uuid)
[kebab](#method-fluent-str-kebab)
[lcfirst](#method-fluent-str-lcfirst)
[length](#method-fluent-str-length)
[limit](#method-fluent-str-limit)
[lower](#method-fluent-str-lower)
[ltrim](#method-fluent-str-ltrim)
[markdown](#method-fluent-str-markdown)
[mask](#method-fluent-str-mask)
[match](#method-fluent-str-match)
[matchAll](#method-fluent-str-match-all)
[isMatch](#method-fluent-str-is-match)
[newLine](#method-fluent-str-new-line)
[padBoth](#method-fluent-str-padboth)
[padLeft](#method-fluent-str-padleft)
[padRight](#method-fluent-str-padright)
[pipe](#method-fluent-str-pipe)
[plural](#method-fluent-str-plural)
[prepend](#method-fluent-str-prepend)
[remove](#method-fluent-str-remove)
[replace](#method-fluent-str-replace)
[replaceArray](#method-fluent-str-replace-array)
[replaceFirst](#method-fluent-str-replace-first)
[replaceLast](#method-fluent-str-replace-last)
[replaceMatches](#method-fluent-str-replace-matches)
[rtrim](#method-fluent-str-rtrim)
[scan](#method-fluent-str-scan)
[singular](#method-fluent-str-singular)
[slug](#method-fluent-str-slug)
[snake](#method-fluent-str-snake)
[split](#method-fluent-str-split)
[squish](#method-fluent-str-squish)
[start](#method-fluent-str-start)
[startsWith](#method-fluent-str-starts-with)
[studly](#method-fluent-str-studly)
[substr](#method-fluent-str-substr)
[substrReplace](#method-fluent-str-substrreplace)
[swap](#method-fluent-str-swap)
[tap](#method-fluent-str-tap)
[test](#method-fluent-str-test)
[title](#method-fluent-str-title)
[trim](#method-fluent-str-trim)
[ucfirst](#method-fluent-str-ucfirst)
[ucsplit](#method-fluent-str-ucsplit)
[upper](#method-fluent-str-upper)
[when](#method-fluent-str-when)
[whenContains](#method-fluent-str-when-contains)
[whenContainsAll](#method-fluent-str-when-contains-all)
[whenEmpty](#method-fluent-str-when-empty)
[whenNotEmpty](#method-fluent-str-when-not-empty)
[whenStartsWith](#method-fluent-str-when-starts-with)
[whenEndsWith](#method-fluent-str-when-ends-with)
[whenExactly](#method-fluent-str-when-exactly)
[whenNotExactly](#method-fluent-str-when-not-exactly)
[whenIs](#method-fluent-str-when-is)
[whenIsAscii](#method-fluent-str-when-is-ascii)
[whenIsUlid](#method-fluent-str-when-is-ulid)
[whenIsUuid](#method-fluent-str-when-is-uuid)
[whenTest](#method-fluent-str-when-test)
[wordCount](#method-fluent-str-word-count)
[words](#method-fluent-str-words)

</div>

<a name="urls-method-list"></a>
### URL

<div class="collection-method-list" markdown="1">

[action](#method-action)
[asset](#method-asset)
[route](#method-route)
[secure_asset](#method-secure-asset)
[secure_url](#method-secure-url)
[to_route](#method-to-route)
[url](#method-url)

</div>

<a name="miscellaneous-method-list"></a>
### その他

<div class="collection-method-list" markdown="1">

[abort](#method-abort)
[abort_if](#method-abort-if)
[abort_unless](#method-abort-unless)
[app](#method-app)
[auth](#method-auth)
[back](#method-back)
[bcrypt](#method-bcrypt)
[blank](#method-blank)
[broadcast](#method-broadcast)
[cache](#method-cache)
[class_uses_recursive](#method-class-uses-recursive)
[collect](#method-collect)
[config](#method-config)
[cookie](#method-cookie)
[csrf_field](#method-csrf-field)
[csrf_token](#method-csrf-token)
[decrypt](#method-decrypt)
[dd](#method-dd)
[dispatch](#method-dispatch)
[dump](#method-dump)
[encrypt](#method-encrypt)
[env](#method-env)
[event](#method-event)
[fake](#method-fake)
[filled](#method-filled)
[info](#method-info)
[logger](#method-logger)
[method_field](#method-method-field)
[now](#method-now)
[old](#method-old)
[optional](#method-optional)
[policy](#method-policy)
[redirect](#method-redirect)
[report](#method-report)
[report_if](#method-report-if)
[report_unless](#method-report-unless)
[request](#method-request)
[rescue](#method-rescue)
[resolve](#method-resolve)
[response](#method-response)
[retry](#method-retry)
[session](#method-session)
[tap](#method-tap)
[throw_if](#method-throw-if)
[throw_unless](#method-throw-unless)
[today](#method-today)
[trait_uses_recursive](#method-trait-uses-recursive)
[transform](#method-transform)
[validator](#method-validator)
[value](#method-value)
[view](#method-view)
[with](#method-with)

</div>

<a name="method-listing"></a>
## メソッド一覧

<style>
    .collection-method code {
        font-size: 14px;
    }

    .collection-method:not(.first-collection-method) {
        margin-top: 50px;
    }
</style>

<a name="arrays"></a>
## 配列とオブジェクト

<a name="method-array-accessible"></a>
#### `Arr::accessible()` {.collection-method .first-collection-method}

`Arr::accessible`メソッドは、指定した値が配列アクセス可能かを判別します。

    use Illuminate\Support\Arr;
    use Illuminate\Support\Collection;

    $isAccessible = Arr::accessible(['a' => 1, 'b' => 2]);

    // true

    $isAccessible = Arr::accessible(new Collection);

    // true

    $isAccessible = Arr::accessible('abc');

    // false

    $isAccessible = Arr::accessible(new stdClass);

    // false

<a name="method-array-add"></a>
#### `Arr::add()` {.collection-method}

`Arr::add`メソッドは指定キー／値のペアをそのキーが存在していない場合と`null`がセットされている場合に、配列に追加します。

    use Illuminate\Support\Arr;

    $array = Arr::add(['name' => 'Desk'], 'price', 100);

    // ['name' => 'Desk', 'price' => 100]

    $array = Arr::add(['name' => 'Desk', 'price' => null], 'price', 100);

    // ['name' => 'Desk', 'price' => 100]


<a name="method-array-collapse"></a>
#### `Arr::collapse()` {.collection-method}

`Arr::collapse`メソッドは配列の配列を一次元の配列へ展開します。

    use Illuminate\Support\Arr;

    $array = Arr::collapse([[1, 2, 3], [4, 5, 6], [7, 8, 9]]);

    // [1, 2, 3, 4, 5, 6, 7, 8, 9]

<a name="method-array-crossjoin"></a>
#### `Arr::crossJoin()` {.collection-method}

`Arr::crossJoin`メソッドは指定配列をクロス結合し、可能性があるすべての順列の直積集合を返します。

    use Illuminate\Support\Arr;

    $matrix = Arr::crossJoin([1, 2], ['a', 'b']);

    /*
        [
            [1, 'a'],
            [1, 'b'],
            [2, 'a'],
            [2, 'b'],
        ]
    */

    $matrix = Arr::crossJoin([1, 2], ['a', 'b'], ['I', 'II']);

    /*
        [
            [1, 'a', 'I'],
            [1, 'a', 'II'],
            [1, 'b', 'I'],
            [1, 'b', 'II'],
            [2, 'a', 'I'],
            [2, 'a', 'II'],
            [2, 'b', 'I'],
            [2, 'b', 'II'],
        ]
    */

<a name="method-array-divide"></a>
#### `Arr::divide()` {.collection-method}

`Arr::divide`メソッドは２つの配列を返します。１つは指定した配列のキーを含み、もう１つは値を含みます。

    use Illuminate\Support\Arr;

    [$keys, $values] = Arr::divide(['name' => 'Desk']);

    // $keys: ['name']

    // $values: ['Desk']

<a name="method-array-dot"></a>
#### `Arr::dot()` {.collection-method}

`Arr::dot`メソッドは多次元配列を「ドット」記法で深さを表した一次元配列に変換します。

    use Illuminate\Support\Arr;

    $array = ['products' => ['desk' => ['price' => 100]]];

    $flattened = Arr::dot($array);

    // ['products.desk.price' => 100]

<a name="method-array-except"></a>
#### `Arr::except()` {.collection-method}

`Arr::except`メソッドは指定キー／値ペアを配列から削除します。

    use Illuminate\Support\Arr;

    $array = ['name' => 'Desk', 'price' => 100];

    $filtered = Arr::except($array, ['price']);

    // ['name' => 'Desk']

<a name="method-array-exists"></a>
#### `Arr::exists()` {.collection-method}

`Arr::exists`メソッドは指定キーが指定配列に存在するかをチェックします。

    use Illuminate\Support\Arr;

    $array = ['name' => 'John Doe', 'age' => 17];

    $exists = Arr::exists($array, 'name');

    // true

    $exists = Arr::exists($array, 'salary');

    // false

<a name="method-array-first"></a>
#### `Arr::first()` {.collection-method}

`Arr::first`メソッドは指定したテストにパスした最初の要素を返します。

    use Illuminate\Support\Arr;

    $array = [100, 200, 300];

    $first = Arr::first($array, function (int $value, int $key) {
        return $value >= 150;
    });

    // 200

デフォルト値を３つ目の引数で指定することもできます。この値はどの値もテストへパスしない場合に返されます。

    use Illuminate\Support\Arr;

    $first = Arr::first($array, $callback, $default);

<a name="method-array-flatten"></a>
#### `Arr::flatten()` {.collection-method}

`Arr::flatten`メソッドは、多次元配列を一次元配列へ変換します。

    use Illuminate\Support\Arr;

    $array = ['name' => 'Joe', 'languages' => ['PHP', 'Ruby']];

    $flattened = Arr::flatten($array);

    // ['Joe', 'PHP', 'Ruby']

<a name="method-array-forget"></a>
#### `Arr::forget()` {.collection-method}

`Arr::forget`メソッドは「ドット記法」で指定キー／値のペアを深くネストされた配列から取り除きます。

    use Illuminate\Support\Arr;

    $array = ['products' => ['desk' => ['price' => 100]]];

    Arr::forget($array, 'products.desk');

    // ['products' => []]

<a name="method-array-get"></a>
#### `Arr::get()` {.collection-method}

`Arr::get`メソッドは「ドット」記法で指定した値を深くネストされた配列から取得します。

    use Illuminate\Support\Arr;

    $array = ['products' => ['desk' => ['price' => 100]]];

    $price = Arr::get($array, 'products.desk.price');

    // 100

`Arr::get`メソッドはデフォルト値も引数に取ります。これは、指定したキーが配列で存在しない場合に返されます。

    use Illuminate\Support\Arr;

    $discount = Arr::get($array, 'products.desk.discount', 0);

    // 0

<a name="method-array-has"></a>
#### `Arr::has()` {.collection-method}

`Arr::has`メソッドは、「ドット」記法で指定したアイテムが配列に存在するかをチェックします。

    use Illuminate\Support\Arr;

    $array = ['product' => ['name' => 'Desk', 'price' => 100]];

    $contains = Arr::has($array, 'product.name');

    // true

    $contains = Arr::has($array, ['product.price', 'product.discount']);

    // false

<a name="method-array-hasany"></a>
#### `Arr::hasAny()` {.collection-method}

`Arr::hasAny`メソッドは「ドット」記法を使い、配列中に一連のアイテムが存在するかを調べます。

    use Illuminate\Support\Arr;

    $array = ['product' => ['name' => 'Desk', 'price' => 100]];

    $contains = Arr::hasAny($array, 'product.name');

    // true

    $contains = Arr::hasAny($array, ['product.name', 'product.discount']);

    // true

    $contains = Arr::hasAny($array, ['category', 'product.discount']);

    // false

<a name="method-array-isassoc"></a>
#### `Arr::isAssoc()` {.collection-method}

`Arr::isAssoc`メソッドは、指定した配列が連想配列である場合、`true`を返します。配列が0から始まる連続したキーを持たない場合、その配列を「連想配列」であると見なします。

    use Illuminate\Support\Arr;

    $isAssoc = Arr::isAssoc(['product' => ['name' => 'Desk', 'price' => 100]]);

    // true

    $isAssoc = Arr::isAssoc([1, 2, 3]);

    // false

<a name="method-array-islist"></a>
#### `Arr::isList()` {.collection-method}

`Arr::isList`メソッドは、指定した配列のキーが0から始まる連続した整数である場合、`true`を返します。

    use Illuminate\Support\Arr;

    $isList = Arr::isList(['foo', 'bar', 'baz']);

    // true

    $isList = Arr::isList(['product' => ['name' => 'Desk', 'price' => 100]]);

    // false

<a name="method-array-join"></a>
#### `Arr::join()` {.collection-method}

`Arr::join`メソッドは、配列の要素を文字列として結合します。このメソッドに第２引数を使用し、配列の最後の要素前に挿入する文字列を指定できます。

    use Illuminate\Support\Arr;

    $array = ['Tailwind', 'Alpine', 'Laravel', 'Livewire'];

    $joined = Arr::join($array, ', ');

    // Tailwind, Alpine, Laravel, Livewire

    $joined = Arr::join($array, ', ', ' and ');

    // Tailwind, Alpine, Laravel and Livewire

<a name="method-array-keyby"></a>
#### `Arr::keyBy()` {.collection-method}

`Arr::keyBy`メソッドは、指定したキーで配列へキーを設定します。複数のアイテムが同じキーを持つ場合、最後のものだけを新しい配列に残します。

    use Illuminate\Support\Arr;

    $array = [
        ['product_id' => 'prod-100', 'name' => 'Desk'],
        ['product_id' => 'prod-200', 'name' => 'Chair'],
    ];

    $keyed = Arr::keyBy($array, 'product_id');

    /*
        [
            'prod-100' => ['product_id' => 'prod-100', 'name' => 'Desk'],
            'prod-200' => ['product_id' => 'prod-200', 'name' => 'Chair'],
        ]
    */

<a name="method-array-last"></a>
#### `Arr::last()` {.collection-method}

`Arr::last`メソッドは、テストでパスした最後の配列要素を返します。

    use Illuminate\Support\Arr;

    $array = [100, 200, 300, 110];

    $last = Arr::last($array, function (int $value, int $key) {
        return $value >= 150;
    });

    // 300

メソッドの第３引数には、デフォルト値を渡します。テストでパスする値がない場合に、返されます。

    use Illuminate\Support\Arr;

    $last = Arr::last($array, $callback, $default);

<a name="method-array-map"></a>
#### `Arr::map()` {.collection-method}

`Arr::map`メソッドは配列を繰り返し処理し、それぞれの値とキーを指定したコールバックへ渡します。配列の値は、コールバックの返却値で置き換えます。

    use Illuminate\Support\Arr;

    $array = ['first' => 'james', 'last' => 'kirk'];

    $mapped = Arr::map($array, function (string $value, string $key) {
        return ucfirst($value);
    });

    // ['first' => 'James', 'last' => 'Kirk']

<a name="method-array-only"></a>
#### `Arr::only()` {.collection-method}

`Arr::only`メソッドは配列中に存在する、指定したキー／値ペアのアイテムのみを返します。

    use Illuminate\Support\Arr;

    $array = ['name' => 'Desk', 'price' => 100, 'orders' => 10];

    $slice = Arr::only($array, ['name', 'price']);

    // ['name' => 'Desk', 'price' => 100]

<a name="method-array-pluck"></a>
#### `Arr::pluck()` {.collection-method}

`Arr::pluck`メソッドは配列中の指定キーに対する値をすべて取得します。

    use Illuminate\Support\Arr;

    $array = [
        ['developer' => ['id' => 1, 'name' => 'Taylor']],
        ['developer' => ['id' => 2, 'name' => 'Abigail']],
    ];

    $names = Arr::pluck($array, 'developer.name');

    // ['Taylor', 'Abigail']

さらに、結果のリストのキー項目も指定できます。

    use Illuminate\Support\Arr;

    $names = Arr::pluck($array, 'developer.name', 'developer.id');

    // [1 => 'Taylor', 2 => 'Abigail']

<a name="method-array-prepend"></a>
#### `Arr::prepend()` {.collection-method}

`Arr::prepend`メソッドは配列の先頭にアイテムを追加します。

    use Illuminate\Support\Arr;

    $array = ['one', 'two', 'three', 'four'];

    $array = Arr::prepend($array, 'zero');

    // ['zero', 'one', 'two', 'three', 'four']

必要であれば、値に対するキーを指定できます。

    use Illuminate\Support\Arr;

    $array = ['price' => 100];

    $array = Arr::prepend($array, 'Desk', 'name');

    // ['name' => 'Desk', 'price' => 100]

<a name="method-array-prependkeyswith"></a>
#### `Arr::prependKeysWith()` {.collection-method}

`Arr::prependKeysWith`は、連想配列のすべてのキー名の前に、指定したプレフィックスを付加します。

    use Illuminate\Support\Arr;

    $array = [
        'name' => 'Desk',
        'price' => 100,
    ];

    $keyed = Arr::prependKeysWith($array, 'product.');

    /*
        [
            'product.name' => 'Desk',
            'product.price' => 100,
        ]
    */

<a name="method-array-pull"></a>
#### `Arr::pull()` {.collection-method}

`Arr::pull`メソッドは配列から指定キー／値ペアを取得し、同時に削除します。

    use Illuminate\Support\Arr;

    $array = ['name' => 'Desk', 'price' => 100];

    $name = Arr::pull($array, 'name');

    // $name: Desk

    // $array: ['price' => 100]

メソッドの第３引数として、デフォルト値を渡せます。この値はキーが存在しない場合に返されます。

    use Illuminate\Support\Arr;

    $value = Arr::pull($array, $key, $default);

<a name="method-array-query"></a>
#### `Arr::query()` {.collection-method}

`Arr::query`メソッドは配列をクエリ文字列へ変換します。

    use Illuminate\Support\Arr;

    $array = [
        'name' => 'Taylor',
        'order' => [
            'column' => 'created_at',
            'direction' => 'desc'
        ]
    ];

    Arr::query($array);

    // name=Taylor&order[column]=created_at&order[direction]=desc

<a name="method-array-random"></a>
#### `Arr::random()` {.collection-method}

`Arr::random`メソッドは配列からランダムに値を返します。

    use Illuminate\Support\Arr;

    $array = [1, 2, 3, 4, 5];

    $random = Arr::random($array);

    // 4 - (retrieved randomly)

オプションの２番目の引数に返すアイテム数を指定することもできます。この引数を指定すると、アイテムが１つだけ必要な場合でも、配列が返されることに注意してください。

    use Illuminate\Support\Arr;

    $items = Arr::random($array, 2);

    // [2, 5] - (retrieved randomly)

<a name="method-array-set"></a>
#### `Arr::set()` {.collection-method}

`Arr::set`メソッドは「ドット」記法を使用し、深くネストした配列に値をセットします。

    use Illuminate\Support\Arr;

    $array = ['products' => ['desk' => ['price' => 100]]];

    Arr::set($array, 'products.desk.price', 200);

    // ['products' => ['desk' => ['price' => 200]]]

<a name="method-array-shuffle"></a>
#### `Arr::shuffle()` {.collection-method}

`Arr::shuffle`メソッドは、配列中のアイテムをランダムにシャッフルします。

    use Illuminate\Support\Arr;

    $array = Arr::shuffle([1, 2, 3, 4, 5]);

    // [3, 2, 5, 1, 4] - (無作為に入れ替えた結果)

<a name="method-array-sort"></a>
#### `Arr::sort()` {.collection-method}

`Arr::sort`メソッドは、配列の値に基づきソートします。

    use Illuminate\Support\Arr;

    $array = ['Desk', 'Table', 'Chair'];

    $sorted = Arr::sort($array);

    // ['Chair', 'Desk', 'Table']

指定するクロージャの結果で配列を並べ替えることもできます。

    use Illuminate\Support\Arr;

    $array = [
        ['name' => 'Desk'],
        ['name' => 'Table'],
        ['name' => 'Chair'],
    ];

    $sorted = array_values(Arr::sort($array, function (array $value) {
        return $value['name'];
    }));

    /*
        [
            ['name' => 'Chair'],
            ['name' => 'Desk'],
            ['name' => 'Table'],
        ]
    */

<a name="method-array-sort-desc"></a>
#### `Arr::sortDesc()` {.collection-method}

`Arr::sortDesc`メソッドは、配列を値の降順でソートします。

    use Illuminate\Support\Arr;

    $array = ['Desk', 'Table', 'Chair'];

    $sorted = Arr::sortDesc($array);

    // ['Table', 'Desk', 'Chair']

指定するクロージャの結果で配列を並べ替えることもできます。

    use Illuminate\Support\Arr;

    $array = [
        ['name' => 'Desk'],
        ['name' => 'Table'],
        ['name' => 'Chair'],
    ];

    $sorted = array_values(Arr::sortDesc($array, function (array $value) {
        return $value['name'];
    }));

    /*
        [
            ['name' => 'Table'],
            ['name' => 'Desk'],
            ['name' => 'Chair'],
        ]
    */

<a name="method-array-sort-recursive"></a>
#### `Arr::sortRecursive()` {.collection-method}

`Arr::sortRecursive`メソッドは、数値インデックス付きサブ配列の場合は`sort`関数を使用し、連想サブ配列の場合は`ksort`関数を使用して、配列を再帰的に並べ替えます。

    use Illuminate\Support\Arr;

    $array = [
        ['Roman', 'Taylor', 'Li'],
        ['PHP', 'Ruby', 'JavaScript'],
        ['one' => 1, 'two' => 2, 'three' => 3],
    ];

    $sorted = Arr::sortRecursive($array);

    /*
        [
            ['JavaScript', 'PHP', 'Ruby'],
            ['one' => 1, 'three' => 3, 'two' => 2],
            ['Li', 'Roman', 'Taylor'],
        ]
    */

<a name="method-array-to-css-classes"></a>
#### `Arr::toCssClasses()` {.collection-method}

`Arr::toCssClasses`は、CSSクラス文字列を条件付きでコンパイルします。この方法はクラスの配列を引数に取り、配列キーに追加したいクラス、値は論理式です。配列要素に数字キーがある場合は、レンダするクラスリストへ常に含めます。

    use Illuminate\Support\Arr;

    $isActive = false;
    $hasError = true;

    $array = ['p-4', 'font-bold' => $isActive, 'bg-red' => $hasError];

    $classes = Arr::toCssClasses($array);

    /*
        'p-4 bg-red'
    */

このメソッドは、[Bladeコンポーネントのアトリビュートバッグを使ったクラスのマージ](/docs/{{version}}/blade#conditionally-merge-classes)と、`@class` [Bladeディレクティブ](/docs/{{version}}/blade#conditional-classes)を提供するLaravelの機能を強化します。

<a name="method-array-undot"></a>
#### `Arr::undot()` {.collection-method}

`Arr::undot`メソッドは、「ドット」記法を用いた一次元配列を多次元配列へ展開します。

    use Illuminate\Support\Arr;

    $array = [
        'user.name' => 'Kevin Malone',
        'user.occupation' => 'Accountant',
    ];

    $array = Arr::undot($array);

    // ['user' => ['name' => 'Kevin Malone', 'occupation' => 'Accountant']]

<a name="method-array-where"></a>
#### `Arr::where()` {.collection-method}

`Arr::where`メソッドは、指定したクロージャを使用して配列をフィルタリングします。

    use Illuminate\Support\Arr;

    $array = [100, '200', 300, '400', 500];

    $filtered = Arr::where($array, function (string|int $value, int $key) {
        return is_string($value);
    });

    // [1 => '200', 3 => '400']

<a name="method-array-where-not-null"></a>
#### `Arr::whereNotNull()` {.collection-method}

`Arr::whereNotNull`メソッドは、指定する配列からすべての`null`値を削除します。

    use Illuminate\Support\Arr;

    $array = [0, null];

    $filtered = Arr::whereNotNull($array);

    // [0 => 0]

<a name="method-array-wrap"></a>
#### `Arr::wrap()` {.collection-method}

`Arr::wrap`メソッドは、指定した値を配列にラップします。指定した値がすでに配列にある場合は、変更せずに返します。

    use Illuminate\Support\Arr;

    $string = 'Laravel';

    $array = Arr::wrap($string);

    // ['Laravel']

指定値が`null`の場合、空の配列を返します。

    use Illuminate\Support\Arr;

    $array = Arr::wrap(null);

    // []

<a name="method-data-fill"></a>
#### `data_fill()` {.collection-method}

`data_fill`関数は「ドット」記法を使用し、ターゲットの配列やオブジェクトへ足りない値をセットします。

    $data = ['products' => ['desk' => ['price' => 100]]];

    data_fill($data, 'products.desk.price', 200);

    // ['products' => ['desk' => ['price' => 100]]]

    data_fill($data, 'products.desk.discount', 10);

    // ['products' => ['desk' => ['price' => 100, 'discount' => 10]]]

この関数はアスタリスクもワイルドカードとして受け取り、それに応じてターゲットにデータを埋め込みます。

    $data = [
        'products' => [
            ['name' => 'Desk 1', 'price' => 100],
            ['name' => 'Desk 2'],
        ],
    ];

    data_fill($data, 'products.*.price', 200);

    /*
        [
            'products' => [
                ['name' => 'Desk 1', 'price' => 100],
                ['name' => 'Desk 2', 'price' => 200],
            ]
        ]
    */

<a name="method-data-get"></a>
#### `data_get()` {.collection-method}

`data_get`関数は「ドット」記法を使用し、ネストした配列やオブジェクトから値を取得します。

    $data = ['products' => ['desk' => ['price' => 100]]];

    $price = data_get($data, 'products.desk.price');

    // 100

`data_get`関数は、指定したキーが存在しない場合に返す、デフォルト値も指定できます。

    $discount = data_get($data, 'products.desk.discount', 0);

    // 0

配列やオブジェクトのいずれのキーにもマッチする、ワイルドカードとしてアスタリスクも使用できます。

    $data = [
        'product-one' => ['name' => 'Desk 1', 'price' => 100],
        'product-two' => ['name' => 'Desk 2', 'price' => 150],
    ];

    data_get($data, '*.name');

    // ['Desk 1', 'Desk 2'];

<a name="method-data-set"></a>
#### `data_set()` {.collection-method}

`data_set`関数は「ドット」記法を使用し、ネストした配列やオブジェクトに値をセットします。

    $data = ['products' => ['desk' => ['price' => 100]]];

    data_set($data, 'products.desk.price', 200);

    // ['products' => ['desk' => ['price' => 200]]]

この関数は、アスタリスクを使用したワイルドカードも受け入れ、それに応じてターゲットに値を設定します。

    $data = [
        'products' => [
            ['name' => 'Desk 1', 'price' => 100],
            ['name' => 'Desk 2', 'price' => 150],
        ],
    ];

    data_set($data, 'products.*.price', 200);

    /*
        [
            'products' => [
                ['name' => 'Desk 1', 'price' => 200],
                ['name' => 'Desk 2', 'price' => 200],
            ]
        ]
    */

デフォルトでは、既存の値はすべて上書きされます。値が存在しない場合にのみ値をセットする場合は、関数の4番目の引数に`false`を渡してください。

    $data = ['products' => ['desk' => ['price' => 100]]];

    data_set($data, 'products.desk.price', 200, overwrite: false);

    // ['products' => ['desk' => ['price' => 100]]]

<a name="method-head"></a>
#### `head()` {.collection-method}

`head`関数は、配列の最初の要素を返します。

    $array = [100, 200, 300];

    $first = head($array);

    // 100

<a name="method-last"></a>
#### `last()` {.collection-method}

`last`関数は指定した配列の最後の要素を返します。

    $array = [100, 200, 300];

    $last = last($array);

    // 300

<a name="paths"></a>
## パス

<a name="method-app-path"></a>
#### `app_path()` {.collection-method}

`app_path`関数は、アプリケーションの`app`ディレクトリの完全修飾パスを返します。`app_path`関数を使用して、アプリケーションディレクトリに関連するファイルへの完全修飾パスを生成することもできます。

    $path = app_path();

    $path = app_path('Http/Controllers/Controller.php');

<a name="method-base-path"></a>
#### `base_path()` {.collection-method}

`base_path`関数は、アプリケーションのルートディレクトリへの完全修飾パスを返します。`base_path`関数を使用して、プロジェクトのルートディレクトリに関連する特定のファイルへの完全修飾パスを生成することもできます。

    $path = base_path();

    $path = base_path('vendor/bin');

<a name="method-config-path"></a>
#### `config_path()` {.collection-method}

`config_path`関数は、アプリケーションの`config`ディレクトリへの完全修飾パスを返します。`config_path`関数を使用して、アプリケーションの構成ディレクトリ内の特定のファイルへの完全修飾パスを生成することもできます。

    $path = config_path();

    $path = config_path('app.php');

<a name="method-database-path"></a>
#### `database_path()` {.collection-method}

`database_path`関数は、アプリケーションの`database`ディレクトリへの完全修飾パスを返します。`database_path`関数を使用して、データベースディレクトリ内の特定のファイルへの完全修飾パスを生成することもできます。

    $path = database_path();

    $path = database_path('factories/UserFactory.php');

<a name="method-lang-path"></a>
#### `lang_path()` {.collection-method}

`lang_path`関数は、アプリケーションの`lang`ディレクトリの完全修飾パスを返します。また、`lang_path`関数を使用して、ディレクトリ内の指定したファイルの完全修飾パスを生成することもできます。

    $path = lang_path();

    $path = lang_path('en/messages.php');

> **Note**
> Laravelアプリケーションのスケルトンは、デフォルトで`lang`ディレクトリを用意していません。Laravelの言語ファイルをカスタマイズしたい場合は、`lang:publish` Artisanコマンドでリソース公開することができます。

<a name="method-mix"></a>
#### `mix()` {.collection-method}

`mix`関数は、[バージョンつけしたMixファイル](/docs/{{version}}/mix)のパスを取得します。

    $path = mix('css/app.css');

<a name="method-public-path"></a>
#### `public_path()` {.collection-method}

`public_path`関数は、アプリケーションの`public`ディレクトリへの完全修飾パスを返します。`public_path`関数を使用して、パブリックディレクトリ内の特定のファイルへの完全修飾パスを生成することもできます。

    $path = public_path();

    $path = public_path('css/app.css');

<a name="method-resource-path"></a>
#### `resource_path()` {.collection-method}

`resource_path`関数は、アプリケーションの`resources`ディレクトリへの完全修飾パスを返します。`resource_path`関数を使用して、リソースディレクトリ内の特定のファイルへの完全修飾パスを生成することもできます。

    $path = resource_path();

    $path = resource_path('sass/app.scss');

<a name="method-storage-path"></a>
#### `storage_path()` {.collection-method}

`storage_path`関数は、アプリケーションの`storage`ディレクトリへの完全修飾パスを返します。`storage_path`関数を使用して、ストレージディレクトリ内の特定のファイルへの完全修飾パスを生成することもできます。

    $path = storage_path();

    $path = storage_path('app/file.txt');

<a name="strings"></a>
## 文字列

<a name="method-__"></a>
#### `__()` {.collection-method}

`__`関数は、指定する翻訳文字列や翻訳キーを[言語ファイル](/docs/{{version}}/localization)を使って翻訳します。

    echo __('Welcome to our application');

    echo __('messages.welcome');

指定した翻訳文字列や翻訳キーが存在しない場合、`__`関数は指定値をそのまま返します。たとえば、上記の場合に翻訳キーが存在しなければ、`__`関数は`messages.welcome`を返します。

<a name="method-class-basename"></a>
#### `class_basename()` {.collection-method}

`class_basename`関数は、指定クラスの名前から名前空間を取り除いて返します。

    $class = class_basename('Foo\Bar\Baz');

    // Baz

<a name="method-e"></a>
#### `e()` {.collection-method}

`e`関数は、PHPの`htmlspecialchars`関数を`double_encode`オプションにデフォルトで`true`を指定し、実行します。

    echo e('<html>foo</html>');

    // &lt;html&gt;foo&lt;/html&gt;

<a name="method-preg-replace-array"></a>
#### `preg_replace_array()` {.collection-method}

`preg_replace_array`関数は、指定パターンを順番に配列中の値に置き換えます。

    $string = 'The event will take place between :start and :end';

    $replaced = preg_replace_array('/:[a-z_]+/', ['8:30', '9:00'], $string);

    // The event will take place between 8:30 and 9:00

<a name="method-str-after"></a>
#### `Str::after()` {.collection-method}

`Str::after`関数は、指定値に続く文字列をすべて返します。文字列中に指定値が存在しない場合は、文字列全体を返します。

    use Illuminate\Support\Str;

    $slice = Str::after('This is my name', 'This is');

    // ' my name'

<a name="method-str-after-last"></a>
#### `Str::afterLast()` {.collection-method}

`Str::afterLast`メソッドは、文字列で指定値が現れる最後の場所から、後ろの部分を返します。文字列中に指定値が存在しない場合は、文字列全体を返します。

    use Illuminate\Support\Str;

    $slice = Str::afterLast('App\Http\Controllers\Controller', '\');

    // 'Controller'

<a name="method-str-ascii"></a>
#### `Str::ascii()` {.collection-method}

`Str::ascii`メソッドは文字列をASCII値へ変換しようと試みます。

    use Illuminate\Support\Str;

    $slice = Str::ascii('û');

    // 'u'

<a name="method-str-before"></a>
#### `Str::before()` {.collection-method}

`Str::before`関数は、文字列中の指定値より前の文字列を全部返します。

    use Illuminate\Support\Str;

    $slice = Str::before('This is my name', 'my name');

    // 'This is '

<a name="method-str-before-last"></a>
#### `Str::beforeLast()` {.collection-method}

`Str::beforeLast`メソッドは、文字列で指定値が現れる最後の場所から、前の部分を返します。

    use Illuminate\Support\Str;

    $slice = Str::beforeLast('This is my name', 'is');

    // 'This '

<a name="method-str-between"></a>
#### `Str::between()` {.collection-method}

`Str::between`メソッドは、２つの値間の部分文字列を返します。

    use Illuminate\Support\Str;

    $slice = Str::between('This is my name', 'This', 'name');

    // ' is my '

<a name="method-str-between-first"></a>
#### `Str::betweenFirst()` {.collection-method}

`Str::betweenFirst`メソッドは、２つの値の間にある文字列のうち、可能な限り小さい部分を返します。

    use Illuminate\Support\Str;

    $slice = Str::betweenFirst('[a] bc [d]', '[', ']');

    // 'a'

<a name="method-camel-case"></a>
#### `Str::camel()` {.collection-method}

`Str::camel`メソッドは、文字列をキャメルケース（`camelCase`）へ変換します。

    use Illuminate\Support\Str;

    $converted = Str::camel('foo_bar');

    // fooBar

<a name="method-str-contains"></a>
#### `Str::contains()` {.collection-method}

`Str::contains`メソッドは、指定文字列に指定値が含まれているかどうかを判別します。このメソッドは大文字と小文字を区別します。

    use Illuminate\Support\Str;

    $contains = Str::contains('This is my name', 'my');

    // true

値の配列を渡して、指定文字列に配列内の値が含まれているかどうかを判断することもできます。

    use Illuminate\Support\Str;

    $contains = Str::contains('This is my name', ['my', 'foo']);

    // true

<a name="method-str-contains-all"></a>
#### `Str::containsAll()` {.collection-method}

`Str::containsAll`メソッドは、指定文字列に指定配列のすべての値が含まれているかどうかを判別します。

    use Illuminate\Support\Str;

    $containsAll = Str::containsAll('This is my name', ['my', 'name']);

    // true

<a name="method-ends-with"></a>
#### `Str::endsWith()` {.collection-method}

`Str::endsWith`メソッドは、最初の文字列が２つ目の引数の文字列で終わっているか判定します。

    use Illuminate\Support\Str;

    $result = Str::endsWith('This is my name', 'name');

    // true


値の配列を渡し、指定文字列が配列内の値のいずれかで終わるかどうかを判断することもできます。

    use Illuminate\Support\Str;

    $result = Str::endsWith('This is my name', ['name', 'foo']);

    // true

    $result = Str::endsWith('This is my name', ['this', 'foo']);

    // false

<a name="method-excerpt"></a>
#### `Str::excerpt()` {.collection-method}

`Str::excerpt`メソッドは指定した文字列から、その文字列内のフレーズの最初のインスタンスにマッチする部分を抜き出します。

    use Illuminate\Support\Str;

    $excerpt = Str::excerpt('This is my name', 'my', [
        'radius' => 3
    ]);

    // '...is my na...'

`radius`オプションはデフォルトが`100`で、抜き出した文字列の両側に表示する文字数を定義します。

さらに、`omission`オプションを使い、抜き出した文字列の前後に追加する文字列を定義できます。

    use Illuminate\Support\Str;

    $excerpt = Str::excerpt('This is my name', 'name', [
        'radius' => 3,
        'omission' => '(...) '
    ]);

    // '(...) my name'

<a name="method-str-finish"></a>
#### `Str::finish()` {.collection-method}

`Str::finish`メソッドは、指定値で終わっていない場合、その値の単一のインスタンスを文字列に追加します。

    use Illuminate\Support\Str;

    $adjusted = Str::finish('this/string', '/');

    // this/string/

    $adjusted = Str::finish('this/string/', '/');

    // this/string/

<a name="method-str-headline"></a>
#### `Str::headline()` {.collection-method}

`Str::headline`メソッドは、大・小文字、ハイフン、アンダースコアにより区切られた文字列から、各単語の最初の文字を大文字にしたスペース区切りの文字列へ変換します。

    use Illuminate\Support\Str;

    $headline = Str::headline('steve_jobs');

    // Steve Jobs

    $headline = Str::headline('EmailNotificationSent');

    // Email Notification Sent

<a name="method-str-inline-markdown"></a>
#### `Str::inlineMarkdown()` {.collection-method}

`Str::inlineMarkdown`メソッドは、GitHub仕様のMarkdownを[CommonMark](https://commonmark.thephpleague.com/)により、インラインHTMLへ変換します。しかし、`markdown`メソッドとは異なり、生成したすべてのHTMLをブロックレベルの要素中へラップはできません。

    use Illuminate\Support\Str;

    $html = Str::inlineMarkdown('**Laravel**');

    // <strong>Laravel</strong>

<a name="method-str-is"></a>
#### `Str::is()` {.collection-method}

`Str::is`メソッドは、指定文字列が指定パターンに一致するかどうかを判別します。アスタリスクをワイルドカード値として使用できます。

    use Illuminate\Support\Str;

    $matches = Str::is('foo*', 'foobar');

    // true

    $matches = Str::is('baz*', 'foobar');

    // false

<a name="method-str-is-ascii"></a>
#### `Str::isAscii()` {.collection-method}

`Str::isAscii`メソッドは、指定文字列が7ビットASCIIであるかを判定します。

    use Illuminate\Support\Str;

    $isAscii = Str::isAscii('Taylor');

    // true

    $isAscii = Str::isAscii('ü');

    // false

<a name="method-str-is-json"></a>
#### `Str::isJson()` {.collection-method}

`Str::isJson`メソッドは、指定文字列が有効なJSONであるかを判定します。

    use Illuminate\Support\Str;

    $result = Str::isJson('[1,2,3]');

    // true

    $result = Str::isJson('{"first": "John", "last": "Doe"}');

    // true

    $result = Str::isJson('{first: "John", last: "Doe"}');

    // false

<a name="method-str-is-ulid"></a>
#### `Str::isUlid()` {.collection-method}

`Str::isUlid`メソッドは、指定文字列が有効なULIDであることを判定します。

    use Illuminate\Support\Str;

    $isUlid = Str::isUlid('01gd6r360bp37zj17nxb55yv40');

    // true

    $isUlid = Str::isUlid('laravel');

    // false

<a name="method-str-is-uuid"></a>
#### `Str::isUuid()` {.collection-method}

`Str::isUuid`メソッドは、指定した文字列が有効なUUIDであることを判定します。

    use Illuminate\Support\Str;

    $isUuid = Str::isUuid('a0a2a2d2-0b87-4a18-83f2-2529882be2de');

    // true

    $isUuid = Str::isUuid('laravel');

    // false

<a name="method-kebab-case"></a>
#### `Str::kebab()` {.collection-method}

`Str::kebab`メソッドは、指定した文字列をケバブケース（`kebab-case`）に変換します。

    use Illuminate\Support\Str;

    $converted = Str::kebab('fooBar');

    // foo-bar

<a name="method-str-lcfirst"></a>
#### `Str::lcfirst()` {.collection-method}

`Str::lcfirst`メソッドは、指定した文字列の最初の文字を小文字に変換して返します。

    use Illuminate\Support\Str;

    $string = Str::lcfirst('Foo Bar');

    // foo Bar

<a name="method-str-length"></a>
#### `Str::length()` {.collection-method}

`Str::length`メソッドは、指定文字列の長さを返します。

    use Illuminate\Support\Str;

    $length = Str::length('Laravel');

    // 7

<a name="method-str-limit"></a>
#### `Str::limit()` {.collection-method}

`Str::limit`メソッドは、指定文字列を指定する長さへ切り捨てます。

    use Illuminate\Support\Str;

    $truncated = Str::limit('The quick brown fox jumps over the lazy dog', 20);

    // The quick brown fox...

メソッドに３番目の引数を渡し、切り捨てる文字列の末尾へ追加する文字列を変更できます。

    use Illuminate\Support\Str;

    $truncated = Str::limit('The quick brown fox jumps over the lazy dog', 20, ' (...)');

    // The quick brown fox (...)

<a name="method-str-lower"></a>
#### `Str::lower()` {.collection-method}

`Str::lower`メソッドは指定文字列を小文字に変換します。

    use Illuminate\Support\Str;

    $converted = Str::lower('LARAVEL');

    // laravel

<a name="method-str-markdown"></a>
#### `Str::markdown()` {.collection-method}

`Str::markdown`メソッドは、GitHub風なマークダウンをHTMLへ、[CommonMark](https://commonmark.thephpleague.com/)を用い変換します。

    use Illuminate\Support\Str;

    $html = Str::markdown('# Laravel');

    // <h1>Laravel</h1>

    $html = Str::markdown('# Taylor <b>Otwell</b>', [
        'html_input' => 'strip',
    ]);

    // <h1>Taylor Otwell</h1>

<a name="method-str-mask"></a>
#### `Str::mask()` {.collection-method}

`Str::mask`メソッドは、文字列の一部を繰り返し文字でマスクし、メールアドレスや電話番号などの文字列の一部を難読化するために使用します。

    use Illuminate\Support\Str;

    $string = Str::mask('taylor@example.com', '*', 3);

    // tay***************

必要であれば、`mask`メソッドの第３引数に負の数を指定し、文字列の最後から指定する文字数分戻った箇所からマスキングを開始するように指示できます。

    $string = Str::mask('taylor@example.com', '*', -15, 3);

    // tay***@example.com

<a name="method-str-ordered-uuid"></a>
#### `Str::orderedUuid()` {.collection-method}

`Str::orderedUuid`メソッドは、インデックス付きデータベース列に効率的に格納できる「タイムスタンプファースト」UUIDを生成します。このメソッドを使用して生成した各UUIDは、以前にこのメソッドを使用して生成されたUUIDの後にソートされます。

    use Illuminate\Support\Str;

    return (string) Str::orderedUuid();

<a name="method-str-padboth"></a>
#### `Str::padBoth()` {.collection-method}

`Str::padBoth`メソッドは、PHPの`str_pad`関数をラップし、最後の文字列が目的の長さに達するまで、文字列の両側を別の文字列でパディングします。

    use Illuminate\Support\Str;

    $padded = Str::padBoth('James', 10, '_');

    // '__James___'

    $padded = Str::padBoth('James', 10);

    // '  James   '

<a name="method-str-padleft"></a>
#### `Str::padLeft()` {.collection-method}

`String::padLeft`メソッドは、PHPの`str_pad`関数をラップし、最後の文字列が目的の長さに達するまで、文字列の左側を別の文字列でパディングします。

    use Illuminate\Support\Str;

    $padded = Str::padLeft('James', 10, '-=');

    // '-=-=-James'

    $padded = Str::padLeft('James', 10);

    // '     James'

<a name="method-str-padright"></a>
#### `Str::padRight()` {.collection-method}

`String::padRight`メソッドは、PHPの`str_pad`関数をラップし、最後の文字列が目的の長さに達するまで、文字列の右側を別の文字列でパディングします。

    use Illuminate\Support\Str;

    $padded = Str::padRight('James', 10, '-');

    // 'James-----'

    $padded = Str::padRight('James', 10);

    // 'James     '

<a name="method-str-password"></a>
#### `Str::password()` {.collection-method}

`Str::password`メソッドは、指定した長さの安全でランダムなパスワードを生成します。パスワードは文字、数字、シンボル、スペースの組み合わせで構成します。パスワードの長さは、デフォルトで３２文字です。

    use Illuminate\Support\Str;

    $password = Str::password();

    // 'EbJo2vE-AS:U,$%_gkrV4n,q~1xy/-_4'

    $password = Str::password(12);

    // 'qwuar>#V|i]N'

<a name="method-str-plural"></a>
#### `Str::plural()` {.collection-method}

`Str::plural`メソッドは、単数形の単語文字列を複数形に変換します。この関数は、[Laravelの複数形化機能により、どんな言語も](/docs/{{version}}/localization#pluralization-language)サポートしています。

    use Illuminate\Support\Str;

    $plural = Str::plural('car');

    // cars

    $plural = Str::plural('child');

    // children

整数をこのメソッドの第２引数に指定することで、文字列の単数形と複数形を切り替えて取得できます。

    use Illuminate\Support\Str;

    $plural = Str::plural('child', 2);

    // children

    $singular = Str::plural('child', 1);

    // child

<a name="method-str-plural-studly"></a>
#### `Str::pluralStudly()` {.collection-method}

`Str::pluralStudly`メソッドは、アッパーキャメルケースでフォーマットされた文字列の単語を複数形に変換します。この関数は、[Laravelの複数形化機能により、どんな言語も](/docs/{{version}}/localization#pluralization-language)サポートしています。

    use Illuminate\Support\Str;

    $plural = Str::pluralStudly('VerifiedHuman');

    // VerifiedHumans

    $plural = Str::pluralStudly('UserFeedback');

    // UserFeedback

整数をこのメソッドの第２引数に指定することで、文字列の単数形と複数形を切り替えて取得できます。

    use Illuminate\Support\Str;

    $plural = Str::pluralStudly('VerifiedHuman', 2);

    // VerifiedHumans

    $singular = Str::pluralStudly('VerifiedHuman', 1);

    // VerifiedHuman

<a name="method-str-random"></a>
#### `Str::random()` {.collection-method}

`Str::random`メソッドは指定した長さのランダムな文字列を生成します。このメソッドは、PHPの`random_bytes`関数を使用します。

    use Illuminate\Support\Str;

    $random = Str::random(40);

<a name="method-str-remove"></a>
#### `Str::remove()` {.collection-method}

`Str::remove`メソッドは、文字列から指定する値または値の配列を削除します。

    use Illuminate\Support\Str;

    $string = 'Peter Piper picked a peck of pickled peppers.';

    $removed = Str::remove('e', $string);

    // Ptr Pipr pickd a pck of pickld ppprs.

文字列を削除するときにケースを無視するには、`remove`メソッドの３番目の引数に`false`を渡してください。

<a name="method-str-replace"></a>
#### `Str::replace()` {.collection-method}

`Str::replace`メソッドは、文字列内の指定した文字列を置き換えます。

    use Illuminate\Support\Str;

    $string = 'Laravel 8.x';

    $replaced = Str::replace('8.x', '9.x', $string);

    // Laravel 9.x

<a name="method-str-replace-array"></a>
#### `Str::replaceArray()` {.collection-method}

`Str::replaceArray`メソッドは配列を使い、文字列を指定値へ順番に置き換えます。

    use Illuminate\Support\Str;

    $string = 'The event will take place between ? and ?';

    $replaced = Str::replaceArray('?', ['8:30', '9:00'], $string);

    // The event will take place between 8:30 and 9:00

<a name="method-str-replace-first"></a>
#### `Str::replaceFirst()` {.collection-method}

`Str::replaceFirst`メソッドは、文字列中で最初に出現した値を指定値で置き換えます。

    use Illuminate\Support\Str;

    $replaced = Str::replaceFirst('the', 'a', 'the quick brown fox jumps over the lazy dog');

    // a quick brown fox jumps over the lazy dog

<a name="method-str-replace-last"></a>
#### `Str::replaceLast()` {.collection-method}

`Str::replaceLast`メソッドは、文字列中で最後に出現した値を指定値で置き換えます。

    use Illuminate\Support\Str;

    $replaced = Str::replaceLast('the', 'a', 'the quick brown fox jumps over the lazy dog');

    // the quick brown fox jumps over a lazy dog


<a name="method-str-reverse"></a>
#### `Str::reverse()` {.collection-method}

`Str::reverse`メソッドは、指定文字列を逆順にします。

    use Illuminate\Support\Str;

    $reversed = Str::reverse('Hello World');

    // dlroW olleH

<a name="method-str-singular"></a>
#### `Str::singular()` {.collection-method}

`Str::singular`メソッドは複数形を単数形へ変換します。この関数は、[Laravelの複数形化機能により、どんな言語も](/docs/{{version}}/localization#pluralization-language)サポートしています。

    use Illuminate\Support\Str;

    $singular = Str::singular('cars');

    // car

    $singular = Str::singular('children');

    // child

<a name="method-str-slug"></a>
#### `Str::slug()` {.collection-method}

`Str::slug`メソッドは指定された文字列から、URLフレンドリーな「スラグ」を生成します。

    use Illuminate\Support\Str;

    $slug = Str::slug('Laravel 5 Framework', '-');

    // laravel-5-framework

<a name="method-snake-case"></a>
#### `Str::snake()` {.collection-method}

`Str::snake`メソッドは文字列をスネークケース（`snake_case`）に変換します。

    use Illuminate\Support\Str;

    $converted = Str::snake('fooBar');

    // foo_bar

    $converted = Str::snake('fooBar', '-');

    // foo-bar

<a name="method-str-squish"></a>
#### `Str::squish()` {.collection-method}

`Str::squish`メソッドは、単語間の余分な空白を含め、文字列から余分な空白をすべて削除します。

    use Illuminate\Support\Str;

    $string = Str::squish('    laravel    framework    ');

    // laravel framework

<a name="method-str-start"></a>
#### `Str::start()` {.collection-method}

`Str::start`メソッドは、文字列が指定値で開始されていない場合、その値の単一インスタンスを文字列の前に追加します。

    use Illuminate\Support\Str;

    $adjusted = Str::start('this/string', '/');

    // /this/string

    $adjusted = Str::start('/this/string', '/');

    // /this/string

<a name="method-starts-with"></a>
#### `Str::startsWith()` {.collection-method}

`Str::startsWith`メソッドは指定文字列が、２番めの引数の文字列で始まっているか判定します。

    use Illuminate\Support\Str;

    $result = Str::startsWith('This is my name', 'This');

    // true

可能な値の配列が渡された場合、`startsWith`メソッドは、文字列が与えられた値のいずれかで始まる場合に`true`を返します。

    $result = Str::startsWith('This is my name', ['This', 'That', 'There']);

    // true

<a name="method-studly-case"></a>
#### `Str::studly()` {.collection-method}

`Str::studly`メソッドは文字列をアッパーキャメルケース（`StudlyCase`）に変換します。

    use Illuminate\Support\Str;

    $converted = Str::studly('foo_bar');

    // FooBar

<a name="method-str-substr"></a>
#### `Str::substr()` {.collection-method}

`Str::substr`メソッドは開始位置と文字列長の引数で指定した部分文字列を返します。

    use Illuminate\Support\Str;

    $converted = Str::substr('The Laravel Framework', 4, 7);

    // Laravel

<a name="method-str-substrcount"></a>
#### `Str::substrCount()` {.collection-method}

`Str::substrCount`メソッドは、指定する文字列内に指定値がいくつ存在しているか返します。

    use Illuminate\Support\Str;

    $count = Str::substrCount('If you like ice cream, you will like snow cones.', 'like');

    // 2

<a name="method-str-substrreplace"></a>
#### `Str::substrReplace()` {.collection-method}

`Str::substrReplace`メソッドは、文字列の一部分のテキストを置き換えます。第３引数で指定した位置から始まり、第４引数で指定した文字数分を置き換えます。このメソッドの第４引数へ`0`を渡すと、文字列内の既存の文字を一切置き換えず、指定位置に文字列を挿入します。

    use Illuminate\Support\Str;

    $result = Str::substrReplace('1300', ':', 2);
    // 13:

    $result = Str::substrReplace('1300', ':', 2, 0);
    // 13:00

<a name="method-str-swap"></a>
#### `Str::swap()` {.collection-method}

`Str::swap`メソッドは、PHPの`strtr`関数を使い、指定した文字列中の複数の値を置き換えます。

    use Illuminate\Support\Str;

    $string = Str::swap([
        'Tacos' => 'Burritos',
        'great' => 'fantastic',
    ], 'Tacos are great!');

    // Burritos are fantastic!

<a name="method-title-case"></a>
#### `Str::title()` {.collection-method}

`Str::title`メソッドは、指定された文字列をタイトルケース（`Title Case`）へ変換します。

    use Illuminate\Support\Str;

    $converted = Str::title('a nice title uses the correct case');

    // A Nice Title Uses The Correct Case

<a name="method-str-to-html-string"></a>
#### `Str::toHtmlString()` {.collection-method}

`Str::toHtmlString`メソッドは、文字列インスタンスを`Illuminate\Support\HtmlString`インスタンスに変換し、Blade テンプレートで表示できるようにします。

    use Illuminate\Support\Str;

    $htmlString = Str::of('Nuno Maduro')->toHtmlString();

<a name="method-str-ucfirst"></a>
#### `Str::ucfirst()` {.collection-method}

`Str::ucfirst`メソッドは、指定文字列の最初の文字を大文字にして返します。

    use Illuminate\Support\Str;

    $string = Str::ucfirst('foo bar');

    // Foo bar

<a name="method-str-ucsplit"></a>
#### `Str::ucsplit()` {.collection-method}

`Str::ucsplit`メソッドは、指定した文字列を文字列を大文字で分割して配列にします。

    use Illuminate\Support\Str;

    $segments = Str::ucsplit('FooBar');

    // [0 => 'Foo', 1 => 'Bar']

<a name="method-str-upper"></a>
#### `Str::upper()` {.collection-method}

`Str::upper`メソッドは、指定文字列を大文字に変換します。

    use Illuminate\Support\Str;

    $string = Str::upper('laravel');

    // LARAVEL

<a name="method-str-ulid"></a>
#### `Str::ulid()` {.collection-method}

`Str::ulid`メソッドは、ULIDを生成します。

    use Illuminate\Support\Str;

    return (string) Str::ulid();

    // 01gd6r360bp37zj17nxb55yv40

<a name="method-str-uuid"></a>
#### `Str::uuid()` {.collection-method}

`Str::uuid`メソッドは、UUID（バージョン４）を生成します。

    use Illuminate\Support\Str;

    return (string) Str::uuid();

<a name="method-str-word-count"></a>
#### `Str::wordCount()` {.collection-method}

`Str::wordCount`関数は、文字列に含まれる単語の数を返します。

```php
use Illuminate\Support\Str;

Str::wordCount('Hello, world!'); // 2
```

<a name="method-str-words"></a>
#### `Str::words()` {.collection-method}

`Str::words`メソッドは、文字列内の単語数を制限します。3番目の引数で、切り捨てた文字列の末尾に追加する文字列を指定できます。

    use Illuminate\Support\Str;

    return Str::words('Perfectly balanced, as all things should be.', 3, ' >>>');

    // Perfectly balanced, as >>>

<a name="method-str"></a>
#### `str()` {.collection-method}

`str`関数は、指定した文字列の新しい`Illuminate\Support\Stringable`インスタンスを返します。この関数は `Str::of`メソッドと等価です。

    $string = str('Taylor')->append(' Otwell');

    // 'Taylor Otwell'

`str`関数に引数を与えなかった場合、`Illuminate\Support\Str`のインスタンスを返します。

    $snake = str()->snake('FooBar');

    // 'foo_bar'

<a name="method-trans"></a>
#### `trans()` {.collection-method}

`trans`関数は、指定した翻訳キーを[言語ファイル](/docs/{{version}}/localization)を使い、翻訳します。

    echo trans('messages.welcome');

指定した翻訳キーが存在しない場合、`trans`関数は指定値をそのまま返します。上記の場合に翻訳キーが存在しなければ、`messages.welcome`が返ります。

<a name="method-trans-choice"></a>
#### `trans_choice()` {.collection-method}

`trans_choice`関数は、数値をもとにし、指定翻訳キーを翻訳します。

    echo trans_choice('messages.notifications', $unreadCount);

指定した翻訳キーが存在しない場合、`trans_choice`関数は指定値をそのまま返します。上記の場合に翻訳キーが存在しなければ、`messages.welcome`が返ります。

<a name="fluent-strings"></a>
## Fluent文字列

Fluent文字列は読み書きしやすい（fluent）、オブジェクト指向で、複数の文字列操作をチェーンできるインターフェイスを提供します。古典的な文字列操作に比較すると、複数の文字列操作を読みやすい文法で使用できます。

<a name="method-fluent-str-after"></a>
#### `after` {.collection-method}

`after`関数は、指定値に続く文字列をすべて返します。文字列中に指定値が存在しない場合は、文字列全体を返します。

    use Illuminate\Support\Str;

    $slice = Str::of('This is my name')->after('This is');

    // ' my name'

<a name="method-fluent-str-after-last"></a>
#### `afterLast` {.collection-method}

`afterLast`メソッドは、文字列で指定値が最後に現れる場所から、後ろの部分を返します。文字列中に指定値が存在しない場合は、文字列全体を返します。

    use Illuminate\Support\Str;

    $slice = Str::of('App\Http\Controllers\Controller')->afterLast('\\');

    // 'Controller'

<a name="method-fluent-str-append"></a>
#### `append` {.collection-method}

`append`メソッドは、指定値を文字列へ追加します。

    use Illuminate\Support\Str;

    $string = Str::of('Taylor')->append(' Otwell');

    // 'Taylor Otwell'

<a name="method-fluent-str-ascii"></a>
#### `ascii` {.collection-method}

`ascii`メソッドは、文字列をアスキー値への変換を試みます。

    use Illuminate\Support\Str;

    $string = Str::of('ü')->ascii();

    // 'u'

<a name="method-fluent-str-basename"></a>
#### `basename` {.collection-method}

`basename`メソッドは、文字列の最後の名前部分を返します。

    use Illuminate\Support\Str;

    $string = Str::of('/foo/bar/baz')->basename();

    // 'baz'

必要であれば、最後の部分から削除したい「拡張子」を指定できます。

    use Illuminate\Support\Str;

    $string = Str::of('/foo/bar/baz.jpg')->basename('.jpg');

    // 'baz'

<a name="method-fluent-str-before"></a>
#### `before` {.collection-method}

`before`関数は、文字列中の指定値より前の文字列を全部返します

    use Illuminate\Support\Str;

    $slice = Str::of('This is my name')->before('my name');

    // 'This is '

<a name="method-fluent-str-before-last"></a>
#### `beforeLast` {.collection-method}

`beforeLast`メソッドは、文字列中で最初に指定値が現れる場所から、前の部分を返します。

    use Illuminate\Support\Str;

    $slice = Str::of('This is my name')->beforeLast('is');

    // 'This '

<a name="method-fluent-str-between"></a>
#### `between` {.collection-method}

`between`メソッドは、２つの値の間にある文字列を返します。

    use Illuminate\Support\Str;

    $converted = Str::of('This is my name')->between('This', 'name');

    // ' is my '

<a name="method-fluent-str-between-first"></a>
#### `betweenFirst` {.collection-method}

`betweenFirst`メソッドは、2つの値の間にある文字列のうち、可能な限り小さい部分を返します。

    use Illuminate\Support\Str;

    $converted = Str::of('[a] bc [d]')->betweenFirst('[', ']');

    // 'a'

<a name="method-fluent-str-camel"></a>
#### `camel` {.collection-method}

`camel`メソッドは、文字列をキャメルケース（`camelCase`）へ変換します

    use Illuminate\Support\Str;

    $converted = Str::of('foo_bar')->camel();

    // fooBar

<a name="method-fluent-str-class-basename"></a>
#### `classBasename` {.collection-method}

`classBasename`メソッドは、指定したクラスから名前空間を削除したクラス名を返す。

    use Illuminate\Support\Str;

    $class = Str::of('Foo\Bar\Baz')->classBasename();

    // Baz

<a name="method-fluent-str-contains"></a>
#### `contains` {.collection-method}

`contains`メソッドは、指定された文字列に指定された値が含まれているかどうかを判別します。このメソッドは大文字と小文字を区別します。

    use Illuminate\Support\Str;

    $contains = Str::of('This is my name')->contains('my');

    // true

値の配列を渡して、指定文字列に配列内の値が含まれているかどうかを判断することもできます。

    use Illuminate\Support\Str;

    $contains = Str::of('This is my name')->contains(['my', 'foo']);

    // true

<a name="method-fluent-str-contains-all"></a>
#### `containsAll` {.collection-method}

`containsAll`メソッドは、指定文字列に指定配列のすべての値が含まれているかどうかを判別します。

    use Illuminate\Support\Str;

    $containsAll = Str::of('This is my name')->containsAll(['my', 'name']);

    // true

<a name="method-fluent-str-dirname"></a>
#### `dirname` {.collection-method}

`dirname`メソッドは文字列の親ディレクトリ名部分を返します。

    use Illuminate\Support\Str;

    $string = Str::of('/foo/bar/baz')->dirname();

    // '/foo/bar'

必要に応じて、文字列から削除するディレクトリレベル数を指定できます。

    use Illuminate\Support\Str;

    $string = Str::of('/foo/bar/baz')->dirname(2);

    // '/foo'

<a name="method-fluent-str-excerpt"></a>
#### `excerpt` {.collection-method}

`excerpt`メソッドは文字列から、その文字列内のフレーズの最初のインスタンスにマッチする部分を抜粋して抜き出します。

    use Illuminate\Support\Str;

    $excerpt = Str::of('This is my name')->excerpt('my', [
        'radius' => 3
    ]);

    // '...is my na...'

`radius`オプションはデフォルトが`100`で、抜き出した文字列の両側に表示する文字数を定義します。

さらに、`omission`オプションを使い、抜き出した文字列の前後に追加する文字列を定義できます。

    use Illuminate\Support\Str;

    $excerpt = Str::of('This is my name')->excerpt('name', [
        'radius' => 3,
        'omission' => '(...) '
    ]);

    // '(...) my name'

<a name="method-fluent-str-ends-with"></a>
#### `endsWith` {.collection-method}

`endsWith`メソッドは、文字列が指定値で終わっているか判定します。

    use Illuminate\Support\Str;

    $result = Str::of('This is my name')->endsWith('name');

    // true

値の配列を渡し、指定文字列が配列内の値のいずれかで終わるかどうかを判断することもできます。

    use Illuminate\Support\Str;

    $result = Str::of('This is my name')->endsWith(['name', 'foo']);

    // true

    $result = Str::of('This is my name')->endsWith(['this', 'foo']);

    // false

<a name="method-fluent-str-exactly"></a>
#### `exactly` {.collection-method}

`exactly`メソッドは、文字列と指定値が完全に一致することを判定します。

    use Illuminate\Support\Str;

    $result = Str::of('Laravel')->exactly('Laravel');

    // true

<a name="method-fluent-str-explode"></a>
#### `explode` {.collection-method}

`explode`メソッドは文字列を指定デリミッタで分割し、分割した文字列を含むコレクションを返します。

    use Illuminate\Support\Str;

    $collection = Str::of('foo bar baz')->explode(' ');

    // collect(['foo', 'bar', 'baz'])

<a name="method-fluent-str-finish"></a>
#### `finish` {.collection-method}

`finish`メソッドは、文字列が指定値で終わっていない場合、その値の単一のインスタンスを追加します。

    use Illuminate\Support\Str;

    $adjusted = Str::of('this/string')->finish('/');

    // this/string/

    $adjusted = Str::of('this/string/')->finish('/');

    // this/string/

<a name="method-fluent-str-headline"></a>
#### `headline` {.collection-method}

`headline`メソッドは、大小文字、ハイフン、アンダースコアで区切られた文字列の各単語の頭文字を大文字にし、スペースで区切った文字列へ変換します。

    use Illuminate\Support\Str;

    $headline = Str::of('taylor_otwell')->headline();

    // Taylor Otwell

    $headline = Str::of('EmailNotificationSent')->headline();

    // Email Notification Sent

<a name="method-fluent-str-inline-markdown"></a>
#### `inlineMarkdown` {.collection-method}

`inlineMarkdown`メソッドは、GitHub仕様のMarkdownを[CommonMark](https://commonmark.thephpleague.com/)により、インラインHTMLへ変換します。しかし、`markdown`メソッドとは異なり、生成したすべてのHTMLをブロックレベルの要素中へラップはできません。

    use Illuminate\Support\Str;

    $html = Str::of('**Laravel**')->inlineMarkdown();

    // <strong>Laravel</strong>

<a name="method-fluent-str-is"></a>
#### `is` {.collection-method}

`is`メソッドは、指定文字列が指定パターンに一致するかどうかを判別します。アスタリスクはワイルドカード値として使用できます

    use Illuminate\Support\Str;

    $matches = Str::of('foobar')->is('foo*');

    // true

    $matches = Str::of('foobar')->is('baz*');

    // false

<a name="method-fluent-str-is-ascii"></a>
#### `isAscii` {.collection-method}

`isAscii`メソッドは、文字列がASCII文字列であるか判定します。

    use Illuminate\Support\Str;

    $result = Str::of('Taylor')->isAscii();

    // true

    $result = Str::of('ü')->isAscii();

    // false

<a name="method-fluent-str-is-empty"></a>
#### `isEmpty` {.collection-method}

`isEmpty`メソッドは、文字列が空であるか判定します。

    use Illuminate\Support\Str;

    $result = Str::of('  ')->trim()->isEmpty();

    // true

    $result = Str::of('Laravel')->trim()->isEmpty();

    // false

<a name="method-fluent-str-is-not-empty"></a>
#### `isNotEmpty` {.collection-method}

`isNotEmpty`メソッドは、文字列が空でないかを判定します。


    use Illuminate\Support\Str;

    $result = Str::of('  ')->trim()->isNotEmpty();

    // false

    $result = Str::of('Laravel')->trim()->isNotEmpty();

    // true

<a name="method-fluent-str-is-json"></a>
#### `isJson` {.collection-method}

`isJson`メソッドは、指定文字列が有効なJSONであるかを判定します。

    use Illuminate\Support\Str;

    $result = Str::of('[1,2,3]')->isJson();

    // true

    $result = Str::of('{"first": "John", "last": "Doe"}')->isJson();

    // true

    $result = Str::of('{first: "John", last: "Doe"}')->isJson();

    // false

<a name="method-fluent-str-is-ulid"></a>
#### `isUlid` {.collection-method}

`isUlid`メソッドは、指定文字列が有効なULIDであることを判定します。

    use Illuminate\Support\Str;

    $result = Str::of('01gd6r360bp37zj17nxb55yv40')->isUlid();

    // true

    $result = Str::of('Taylor')->isUlid();

    // false

<a name="method-fluent-str-is-uuid"></a>
#### `isUuid` {.collection-method}

`isUuid`メソッドは、文字列がUUIDかを判定します。

    use Illuminate\Support\Str;

    $result = Str::of('5ace9ab9-e9cf-4ec6-a19d-5881212a452c')->isUuid();

    // true

    $result = Str::of('Taylor')->isUuid();

    // false

<a name="method-fluent-str-kebab"></a>
#### `kebab` {.collection-method}

`kebab`メソッドは、文字列をケバブケース（`kebab-case`）へ変換します。

    use Illuminate\Support\Str;

    $converted = Str::of('fooBar')->kebab();

    // foo-bar

<a name="method-fluent-str-lcfirst"></a>
#### `lcfirst` {.collection-method}

`lcfirst`メソッドは、指定した文字列の最初の文字を小文字にしたものを返します。

    use Illuminate\Support\Str;

    $string = Str::of('Foo Bar')->lcfirst();

    // foo Bar


<a name="method-fluent-str-length"></a>
#### `length` {.collection-method}

`length`メソッドは、文字列の長さを返します。

    use Illuminate\Support\Str;

    $length = Str::of('Laravel')->length();

    // 7

<a name="method-fluent-str-limit"></a>
#### `limit` {.collection-method}

`limit`メソッドは、指定文字列を指定した長さに切り捨てます。

    use Illuminate\Support\Str;

    $truncated = Str::of('The quick brown fox jumps over the lazy dog')->limit(20);

    // The quick brown fox...

２番目の引数を渡して、切り捨てた文字列の末尾に追加する文字列を変更することもできます。

    use Illuminate\Support\Str;

    $truncated = Str::of('The quick brown fox jumps over the lazy dog')->limit(20, ' (...)');

    // The quick brown fox (...)

<a name="method-fluent-str-lower"></a>
#### `lower` {.collection-method}

`lower`メソッドは、文字列を小文字に変換します。

    use Illuminate\Support\Str;

    $result = Str::of('LARAVEL')->lower();

    // 'laravel'

<a name="method-fluent-str-ltrim"></a>
#### `ltrim` {.collection-method}

`ltrim`メソッドは、文字列の左側をトリムします。

    use Illuminate\Support\Str;

    $string = Str::of('  Laravel  ')->ltrim();

    // 'Laravel  '

    $string = Str::of('/Laravel/')->ltrim('/');

    // 'Laravel/'

<a name="method-fluent-str-markdown"></a>
#### `markdown` {.collection-method}

`markdown`メソッドはGitHub風マークダウンをHTMLに変換します。

    use Illuminate\Support\Str;

    $html = Str::of('# Laravel')->markdown();

    // <h1>Laravel</h1>

    $html = Str::of('# Taylor <b>Otwell</b>')->markdown([
        'html_input' => 'strip',
    ]);

    // <h1>Taylor Otwell</h1>

<a name="method-fluent-str-mask"></a>
#### `mask` {.collection-method}

`mask`メソッドは、文字列の一部を繰り返し文字でマスクし、メールアドレスや電話番号など、文字列の一部を難読化するのに使用します。

    use Illuminate\Support\Str;

    $string = Str::of('taylor@example.com')->mask('*', 3);

    // tay***************

必要であれば、`mask`メソッドの第３・４引数に負の数を指定し、文字列の最後から指定する文字数分戻った箇所からマスキングを開始するように指示できます。

    $string = Str::of('taylor@example.com')->mask('*', -15, 3);

    // tay***@example.com

    $string = Str::of('taylor@example.com')->mask('*', 4, -4);

    // tayl**********.com

<a name="method-fluent-str-match"></a>
#### `match` {.collection-method}

`match`メソッドは、指定した正規表現パターンに一致する部分文字列を返します。

    use Illuminate\Support\Str;

    $result = Str::of('foo bar')->match('/bar/');

    // 'bar'

    $result = Str::of('foo bar')->match('/foo (.*)/');

    // 'bar'

<a name="method-fluent-str-match-all"></a>
#### `matchAll` {.collection-method}

`matchAll`メソッドは、指定した正規表現パターンに一致した部分文字列を含むコレクションを返します。

    use Illuminate\Support\Str;

    $result = Str::of('bar foo bar')->matchAll('/bar/');

    // collect(['bar', 'bar'])

正規表現にマッチンググループを指定した場合は、そのグループに一致するコレクションを返します。

    use Illuminate\Support\Str;

    $result = Str::of('bar fun bar fly')->matchAll('/f(\w*)/');

    // collect(['un', 'ly']);

一致しなかった場合は、空のコレクションを返します。

<a name="method-fluent-str-is-match"></a>
#### `isMatch` {.collection-method}

`isMatch`メソッドは、文字列が指定正規表現にマッチした場合に`true`を返します。

    use Illuminate\Support\Str;

    $result = Str::of('foo bar')->isMatch('/foo (.*)/');

    // true

    $result = Str::of('laravel')->match('/foo (.*)/');

    // false

<a name="method-fluent-str-new-line"></a>
#### `newLine` {.collection-method}

`newLine`メソッドは、文字列に「行末」コードを追加します。

    use Illuminate\Support\Str;

    $padded = Str::of('Laravel')->newLine()->append('Framework');

    // 'Laravel
    //  Framework'

<a name="method-fluent-str-padboth"></a>
#### `padBoth` {.collection-method}

`padBoth`メソッドはPHPの`str_pad`関数をラップし、最後の文字列が目的の長さに達するまで、文字列の両側を別の文字列でパディングします。

    use Illuminate\Support\Str;

    $padded = Str::of('James')->padBoth(10, '_');

    // '__James___'

    $padded = Str::of('James')->padBoth(10);

    // '  James   '

<a name="method-fluent-str-padleft"></a>
#### `padLeft` {.collection-method}

`padLeft`メソッドはPHPの`str_pad`関数をラップし、最後の文字列が目的の長さに達するまで、文字列の左側を別の文字列でパディングします。

    use Illuminate\Support\Str;

    $padded = Str::of('James')->padLeft(10, '-=');

    // '-=-=-James'

    $padded = Str::of('James')->padLeft(10);

    // '     James'

<a name="method-fluent-str-padright"></a>
#### `padRight` {.collection-method}

`padRight`メソッドはPHPの`str_pad`関数をラップし、最後の文字列が目的の長さに達するまで、文字列の右側を別の文字列でパディングします。

    use Illuminate\Support\Str;

    $padded = Str::of('James')->padRight(10, '-');

    // 'James-----'

    $padded = Str::of('James')->padRight(10);

    // 'James     '

<a name="method-fluent-str-pipe"></a>
#### `pipe` {.collection-method}

`pipe`メソッドは、現在の値を指定Callableへ渡すことで、文字列を変換します。

    use Illuminate\Support\Str;
    use Illuminate\Support\Stringable;

    $hash = Str::of('Laravel')->pipe('md5')->prepend('Checksum: ');

    // 'Checksum: a5c95b86291ea299fcbe64458ed12702'

    $closure = Str::of('foo')->pipe(function (Stringable $str) {
        return 'bar';
    });

    // 'bar'

<a name="method-fluent-str-plural"></a>
#### `plural` {.collection-method}

`plural`メソッドは、単数形の単語文字列を複数形に変換します。この関数は、[Laravelの複数形化機能により、どんな言語も](/docs/{{version}}/localization#pluralization-language)サポートしています。

    use Illuminate\Support\Str;

    $plural = Str::of('car')->plural();

    // cars

    $plural = Str::of('child')->plural();

    // children

整数をこのメソッドの第２引数に指定することで、文字列の単数形と複数形を切り替えて取得できます。

    use Illuminate\Support\Str;

    $plural = Str::of('child')->plural(2);

    // children

    $plural = Str::of('child')->plural(1);

    // child

<a name="method-fluent-str-prepend"></a>
#### `prepend` {.collection-method}

`prepend`メソッドは、指定値を文字列の先頭へ追加します。

    use Illuminate\Support\Str;

    $string = Str::of('Framework')->prepend('Laravel ');

    // Laravel Framework

<a name="method-fluent-str-remove"></a>
#### `remove` {.collection-method}

`remove`メソッドは、指定する値か文字列の配列を文字列から削除します。

    use Illuminate\Support\Str;

    $string = Str::of('Arkansas is quite beautiful!')->remove('quite');

    // Arkansas is beautiful!

文字列削除時にケースを無視するため２番目のパラメータへ`false`を渡すこともできます。

<a name="method-fluent-str-replace"></a>
#### `replace` {.collection-method}

`replace`メソッドは、文字列中の指定値を置き換えます。

    use Illuminate\Support\Str;

    $replaced = Str::of('Laravel 6.x')->replace('6.x', '7.x');

    // Laravel 7.x

<a name="method-fluent-str-replace-array"></a>
#### `replaceArray` {.collection-method}

`replaceArray`メソッドは、配列を使用して文字列中の指定値を置き換えます。

    use Illuminate\Support\Str;

    $string = 'The event will take place between ? and ?';

    $replaced = Str::of($string)->replaceArray('?', ['8:30', '9:00']);

    // The event will take place between 8:30 and 9:00

<a name="method-fluent-str-replace-first"></a>
#### `replaceFirst` {.collection-method}

`replaceFirst`メソッドは、文字列中で最初に現れた指定値を置き換えます。

    use Illuminate\Support\Str;

    $replaced = Str::of('the quick brown fox jumps over the lazy dog')->replaceFirst('the', 'a');

    // a quick brown fox jumps over the lazy dog

<a name="method-fluent-str-replace-last"></a>
#### `replaceLast` {.collection-method}

`replaceLast`メソッドは文字列中で最後に現れた指定値を置き換えます。

    use Illuminate\Support\Str;

    $replaced = Str::of('the quick brown fox jumps over the lazy dog')->replaceLast('the', 'a');

    // the quick brown fox jumps over a lazy dog

<a name="method-fluent-str-replace-matches"></a>
#### `replaceMatches` {.collection-method}

`replaceMatches`メソッドは、パターンに一致する文字列のすべての部分を、指定した置換文字列で置き換えます。

    use Illuminate\Support\Str;

    $replaced = Str::of('(+1) 501-555-1000')->replaceMatches('/[^A-Za-z0-9]++/', '')

    // '15015551000'

`replaceMatches`メソッドは、指定したパターンに一致する文字列の各部分で呼び出されるクロージャも受け入れます。これにより、クロージャ内で置換ロジックを実行し、置換した値を返せます。

    use Illuminate\Support\Str;
    use Illuminate\Support\Stringable;

    $replaced = Str::of('123')->replaceMatches('/\d/', function (Stringable $match) {
        return '['.$match[0].']';
    });

    // '[1][2][3]'

<a name="method-fluent-str-rtrim"></a>
#### `rtrim` {.collection-method}

`rtrim`メソッドは、指定した文字列の右側をトリムします。

    use Illuminate\Support\Str;

    $string = Str::of('  Laravel  ')->rtrim();

    // '  Laravel'

    $string = Str::of('/Laravel/')->rtrim('/');

    // '/Laravel'

<a name="method-fluent-str-scan"></a>
#### `scan` {.collection-method}

`scan` メソッドは、[`sscanf` PHP関数](https://www.php.net/manual/ja/function.sscanf.php)がサポートするフォーマットに従い、文字列からコレクションへの入力をパースします。

    use Illuminate\Support\Str;

    $collection = Str::of('filename.jpg')->scan('%[^.].%s');

    // collect(['filename', 'jpg'])

<a name="method-fluent-str-singular"></a>
#### `singular` {.collection-method}

`singular`メソッドは、単語を単数形に変換します。この関数は、[Laravelの複数形化機能により、どんな言語も](/docs/{{version}}/localization#pluralization-language)サポートしています。

    use Illuminate\Support\Str;

    $singular = Str::of('cars')->singular();

    // car

    $singular = Str::of('children')->singular();

    // child

<a name="method-fluent-str-slug"></a>
#### `slug` {.collection-method}

`slug`メソッドは、文字列をURLフレンドリーな「スラグ」へ変換します。

    use Illuminate\Support\Str;

    $slug = Str::of('Laravel Framework')->slug('-');

    // laravel-framework

<a name="method-fluent-str-snake"></a>
#### `snake` {.collection-method}

The `snake` method converts the given string to `snake`メソッドは、文字列をスネークケース（`snake_case`）へ変換します。

    use Illuminate\Support\Str;

    $converted = Str::of('fooBar')->snake();

    // foo_bar

<a name="method-fluent-str-split"></a>
#### `split` {.collection-method}

`split`メソッドは正規表現を使い文字列をコレクションへ分割します。

    use Illuminate\Support\Str;

    $segments = Str::of('one, two, three')->split('/[\s,]+/');

    // collect(["one", "two", "three"])

<a name="method-fluent-str-squish"></a>
#### `squish` {.collection-method}

`squish`メソッドは、単語間の余分な空白を含め、文字列から余分な空白をすべて削除します。

    use Illuminate\Support\Str;

    $string = Str::of('    laravel    framework    ')->squish();

    // laravel framework

<a name="method-fluent-str-start"></a>
#### `start` {.collection-method}

`start`メソッドは、文字列が指定値で開始されていない場合、その値の単一のインスタンスを追加します。

    use Illuminate\Support\Str;

    $adjusted = Str::of('this/string')->start('/');

    // /this/string

    $adjusted = Str::of('/this/string')->start('/');

    // /this/string

<a name="method-fluent-str-starts-with"></a>
#### `startsWith` {.collection-method}

`startsWith`メソッドは、文字列が指定値から始まっているかを判定します。

    use Illuminate\Support\Str;

    $result = Str::of('This is my name')->startsWith('This');

    // true

<a name="method-fluent-str-studly"></a>
#### `studly` {.collection-method}

`studly`メソッドは、文字列をアッパーキャメルケース（`StudlyCase`）へ変換します。

    use Illuminate\Support\Str;

    $converted = Str::of('foo_bar')->studly();

    // FooBar

<a name="method-fluent-str-substr"></a>
#### `substr` {.collection-method}

`substr`メソッドは、引数で指定された開始位置と長さの部分文字列を返します。

    use Illuminate\Support\Str;

    $string = Str::of('Laravel Framework')->substr(8);

    // Framework

    $string = Str::of('Laravel Framework')->substr(8, 5);

    // Frame

<a name="method-fluent-str-substrreplace"></a>
#### `substrReplace` {.collection-method}

`substrReplace`メソッドは、文字列の一部分のテキストを置き換えるもので、第２引数で指定した位置から始まり、第３引数で指定した文字数分を置き換えます。このメソッドの第3引数に`0`を渡すと、文字列内の既存の文字を一切置き換えることなく、指定された位置に文字列を挿入します。

    use Illuminate\Support\Str;

    $string = Str::of('1300')->substrReplace(':', 2);

    // 13:

    $string = Str::of('The Framework')->substrReplace(' Laravel', 3, 0);

    // The Laravel Framework

<a name="method-fluent-str-swap"></a>
#### `swap` {.collection-method}

`swap`メソッドは、PHPの`strtr`関数を用いて、文字列内の複数の値を置き換えます。

    use Illuminate\Support\Str;

    $string = Str::of('Tacos are great!')
        ->swap([
            'Tacos' => 'Burritos',
            'great' => 'fantastic',
        ]);

    // Burritos are fantastic!

<a name="method-fluent-str-tap"></a>
#### `tap` {.collection-method}

`tap`メソッドは文字列を指定クロージャへ渡し、その文字列自体に影響を与えずに文字列を調べ操作することができます。クロージャが返す値に関係なく、`tap`メソッドはオリジナルの文字列を返します。

    use Illuminate\Support\Str;
    use Illuminate\Support\Stringable;

    $string = Str::of('Laravel')
        ->append(' Framework')
        ->tap(function (Stringable $string) {
            dump('String after append: '.$string);
        })
        ->upper();

    // LARAVEL FRAMEWORK

<a name="method-fluent-str-test"></a>
#### `test` {.collection-method}

`test`メソッドは、文字列が指定する正規表現パターンと一致するか判定します。

    use Illuminate\Support\Str;

    $result = Str::of('Laravel Framework')->test('/Laravel/');

    // true

<a name="method-fluent-str-title"></a>
#### `title` {.collection-method}

`title`メソッドは、文字列をタイトルケース（`Title Case`）へ変換します。

    use Illuminate\Support\Str;

    $converted = Str::of('a nice title uses the correct case')->title();

    // A Nice Title Uses The Correct Case

<a name="method-fluent-str-trim"></a>
#### `trim` {.collection-method}

`trim`メソッドは、文字列をトリムします。

    use Illuminate\Support\Str;

    $string = Str::of('  Laravel  ')->trim();

    // 'Laravel'

    $string = Str::of('/Laravel/')->trim('/');

    // 'Laravel'

<a name="method-fluent-str-ucfirst"></a>
#### `ucfirst` {.collection-method}

`ucfirst`メソッドは、文字列の最初の１文字目を大文字にします。

    use Illuminate\Support\Str;

    $string = Str::of('foo bar')->ucfirst();

    // Foo bar

<a name="method-fluent-str-ucsplit"></a>
#### `ucsplit` {.collection-method}

`ucsplit`メソッドは、指定した文字列を文字列を大文字で分割して配列にします。

    use Illuminate\Support\Str;

    $string = Str::of('Foo Bar')->ucsplit();

    // collect(['Foo', 'Bar'])

<a name="method-fluent-str-upper"></a>
#### `upper` {.collection-method}

`upper`メソッドは、文字列を大文字に変換します。

    use Illuminate\Support\Str;

    $adjusted = Str::of('laravel')->upper();

    // LARAVEL

<a name="method-fluent-str-when"></a>
#### `when` {.collection-method}

`when`メソッドは指定条件が`true`の場合、指定したクロージャを呼び出します。クロージャは、fluent文字列インスタンスを受け取ります。

    use Illuminate\Support\Str;
    use Illuminate\Support\Stringable;

    $string = Str::of('Taylor')
                    ->when(true, function (Stringable $string) {
                        return $string->append(' Otwell');
                    });

    // 'Taylor Otwell'

必要であれば、３番目のパラメータとして別のクロージャを`when`メソッドに渡せます。このクロージャは、条件パラメータが`false`と評価された場合に実行します。

<a name="method-fluent-str-when-contains"></a>
#### `whenContains` {.collection-method}

`whenContains`メソッドは、文字列が指定値を含んでいる場合に、渡したクロージャを呼び出します。クロージャは、Fluent文字列のインスタンスを受け取ります。

    use Illuminate\Support\Str;
    use Illuminate\Support\Stringable;

    $string = Str::of('tony stark')
                ->whenContains('tony', function (Stringable $string) {
                    return $string->title();
                });

    // 'Tony Stark'

必要であれば、`when`メソッドの第３パラメータへ、もう一つクロージャを渡せます。このクロージャは、文字列が指定値を含んでいない場合に実行されます。

値の配列を渡して、指定文字列に配列内の値が含まれているかどうかを判断することもできます。

    use Illuminate\Support\Str;
    use Illuminate\Support\Stringable;

    $string = Str::of('tony stark')
                ->whenContains(['tony', 'hulk'], function (Stringable $string) {
                    return $string->title();
                });

    // Tony Stark

<a name="method-fluent-str-when-contains-all"></a>
#### `whenContainsAll` {.collection-method}

`whenContainsAll`メソッドは、文字列が指定する部分文字列をすべて含んでいる場合に、指定クロージャを呼び出します。クロージャは、Fluent文字列のインスタンスを受け取ります。

    use Illuminate\Support\Str;
    use Illuminate\Support\Stringable;

    $string = Str::of('tony stark')
                    ->whenContainsAll(['tony', 'stark'], function (Stringable $string) {
                        return $string->title();
                    });

    // 'Tony Stark'

必要であれば、３番目のパラメータとして別のクロージャを`when`メソッドに渡せます。このクロージャは、条件パラメータが`false`と評価された場合に実行します。

<a name="method-fluent-str-when-empty"></a>
#### `whenEmpty` {.collection-method}

文字列が空の場合、`whenEmpty`メソッドは指定されたクロージャを呼び出します。クロージャが値を返す場合、その値を`whenEmpty`メソッドも返します。クロージャが値を返さない場合、fluent文字列インスタンスを返します。

    use Illuminate\Support\Str;
    use Illuminate\Support\Stringable;

    $string = Str::of('  ')->whenEmpty(function (Stringable $string) {
        return $string->trim()->prepend('Laravel');
    });

    // 'Laravel'

<a name="method-fluent-str-when-not-empty"></a>
#### `whenNotEmpty` {.collection-method}

`whenNotEmpty`メソッドは文字列が空でない場合、指定するクロージャを呼び出します。クロージャが値を返す場合は、`whenNotEmpty`メソッドもその値を返します 。クロージャが値を返さない場合は、Fluent文字列インスタンスを返します。

    use Illuminate\Support\Str;
    use Illuminate\Support\Stringable;

    $string = Str::of('Framework')->whenNotEmpty(function (Stringable $string) {
        return $string->prepend('Laravel ');
    });

    // 'Laravel Framework'

<a name="method-fluent-str-when-starts-with"></a>
#### `whenStartsWith` {.collection-method}

`whenStartsWith`メソッドは、文字列が指定する部分文字列から始まる場合に、指定クロージャを呼び出します。クロージャは、Fluent文字列インスタンスを受け取ります。

    use Illuminate\Support\Str;
    use Illuminate\Support\Stringable;

    $string = Str::of('disney world')->whenStartsWith('disney', function (Stringable $string) {
        return $string->title();
    });

    // 'Disney World'

<a name="method-fluent-str-when-ends-with"></a>
#### `whenEndsWith` {.collection-method}

`whenEndsWith`メソッドは、文字列が指定した部分文字列で終了する場合に、指定クロージャを呼び出します。クロージャは、Fluent文字列インスタンスを受け取ります。

    use Illuminate\Support\Str;
    use Illuminate\Support\Stringable;

    $string = Str::of('disney world')->whenEndsWith('world', function (Stringable $string) {
        return $string->title();
    });

    // 'Disney World'

<a name="method-fluent-str-when-exactly"></a>
#### `whenExactly` {.collection-method}

`whenExactly`メソッドは、文字列が指定文字列と厳密に一致する場合に、指定クロージャを呼び出します。クロージャは、Fluent文字列インスタンスを受け取ります。

    use Illuminate\Support\Str;
    use Illuminate\Support\Stringable;

    $string = Str::of('laravel')->whenExactly('laravel', function (Stringable $string) {
        return $string->title();
    });

    // 'Laravel'

<a name="method-fluent-str-when-not-exactly"></a>
#### `whenNotExactly` {.collection-method}

`whenNotExactly`メソッドは、文字列が指定文字列と厳密に一致しない場合に、指定クロージャを呼び出します。クロージャは、Fluent文字列インスタンスを受け取ります。

    use Illuminate\Support\Str;
    use Illuminate\Support\Stringable;

    $string = Str::of('framework')->whenNotExactly('laravel', function (Stringable $string) {
        return $string->title();
    });

    // 'Framework'

<a name="method-fluent-str-when-is"></a>
#### `whenIs` {.collection-method}

`whenIs`メソッドは、文字列が与えられたパターンにマッチする場合、指定クロージャを呼び出します。アスタリスクはワイルドカードとして使用できます。クロージャはFluent文字列インスタンスを受け取ります。

    use Illuminate\Support\Str;
    use Illuminate\Support\Stringable;

    $string = Str::of('foo/bar')->whenIs('foo/*', function (Stringable $string) {
        return $string->append('/baz');
    });

    // 'foo/bar/baz'

<a name="method-fluent-str-when-is-ascii"></a>
#### `whenIsAscii` {.collection-method}

`whenIsAscii`メソッドは、文字列が7bitのASCIIの場合、クロージャを呼び出します。クロージャはFluent文字列インスタンスを受け取ります。

    use Illuminate\Support\Str;
    use Illuminate\Support\Stringable;

    $string = Str::of('laravel')->whenIsAscii(function (Stringable $string) {
        return $string->title();
    });

    // 'Laravel'

<a name="method-fluent-str-when-is-ulid"></a>
#### `whenIsUlid` {.collection-method}

`whenIsUlid`メソッドは、文字列が有効なULIDである場合、指定するクロージャを呼び出します。クロージャは、Fluent文字列のインスタンスを受け取ります。

    use Illuminate\Support\Str;

    $string = Str::of('01gd6r360bp37zj17nxb55yv40')->whenIsUlid(function (Stringable $string) {
        return $string->substr(0, 8);
    });

    // '01gd6r36'

<a name="method-fluent-str-when-is-uuid"></a>
#### `whenIsUuid` {.collection-method}

`whenIsUuid`メソッドは、文字列が有効なUUIDである場合、指定クロージャを呼び出します。クロージャは、Fluent文字列インスタンスを受け取ります。

    use Illuminate\Support\Str;
    use Illuminate\Support\Stringable;

    $string = Str::of('a0a2a2d2-0b87-4a18-83f2-2529882be2de')->whenIsUuid(function (Stringable $string) {
        return $string->substr(0, 8);
    });

    // 'a0a2a2d2'

<a name="method-fluent-str-when-test"></a>
#### `whenTest` {.collection-method}

`whenTest`メソッドは、文字列が正規表現にマッチする場合、指定クロージャを呼び出します。クロージャはFluent文字列インスタンスを受け取ります。

    use Illuminate\Support\Str;
    use Illuminate\Support\Stringable;

    $string = Str::of('laravel framework')->whenTest('/laravel/', function (Stringable $string) {
        return $string->title();
    });

    // 'Laravel Framework'

<a name="method-fluent-str-word-count"></a>
#### `wordCount` {.collection-method}

`wordcount`メソッドは、文字列に含まれる単語の数を返します。

```php
use Illuminate\Support\Str;

Str::of('Hello, world!')->wordCount(); // 2
```

<a name="method-fluent-str-words"></a>
#### `words` {.collection-method}

`words`メソッドは、文字列内の単語数を制限します。必要に応じ、切り捨てた文字列に追加する文字列を指定できます。

    use Illuminate\Support\Str;

    $string = Str::of('Perfectly balanced, as all things should be.')->words(3, ' >>>');

    // Perfectly balanced, as >>>

<a name="urls"></a>
## URL

<a name="method-action"></a>
#### `action()` {.collection-method}

`action`関数は、指定されたコントローラアクションのURLを生成します。

    use App\Http\Controllers\HomeController;

    $url = action([HomeController::class, 'index']);

メソッドがルートパラメーターを受け付ける場合は、第２引数で指定してください。

    $url = action([UserController::class, 'profile'], ['id' => 1]);

<a name="method-asset"></a>
#### `asset()` {.collection-method}

`asset`関数は、現在のリクエストのスキーマ（HTTPかHTTPS）を使い、アセットへのURLを生成します。

    $url = asset('img/photo.jpg');

`.env`ファイルで`ASSET_URL`変数を設定することにより、アセットURLホストを設定できます。これは、AmazonS3や別のCDNなどの外部サービスでアセットをホストする場合に役立ちます。

    // ASSET_URL=http://example.com/assets

    $url = asset('img/photo.jpg'); // http://example.com/assets/img/photo.jpg

<a name="method-route"></a>
#### `route()` {.collection-method}

`route`関数は、指定した[名前付きルート](/docs/{{version}}/routing#named-routes)のURLを生成します。

    $url = route('route.name');

ルートがパラメーターを受け入れる場合は、それらを関数の２番目の引数として渡すことができます。

    $url = route('route.name', ['id' => 1]);

デフォルトでは、`route`関数は絶対URLを生成します。相対URLを生成する場合は、関数の３番目の引数として`false`を渡してください。

    $url = route('route.name', ['id' => 1], false);

<a name="method-secure-asset"></a>
#### `secure_asset()` {.collection-method}

`secure_asset`関数はHTTPSを使い、アセットへのURLを生成します。

    $url = secure_asset('img/photo.jpg');

<a name="method-secure-url"></a>
#### `secure_url()` {.collection-method}

`secure_url`関数は、指定されたパスへの完全修飾HTTPS URLを生成します。関数の２番目の引数で追加のURLセグメントを渡すことができます。

    $url = secure_url('user/profile');

    $url = secure_url('user/profile', [1]);

<a name="method-to-route"></a>
#### `to_route()` {.collection-method}

`to_route`関数は、指定した[名前付きルート](/docs/{{version}}/routing#named-routes)に対して、[リダイレクトHTTPレスポンス](/docs/{{version}}/responses#redirects)を生成します。

    return to_route('users.show', ['user' => 1]);

必要であれば、`to_route`メソッドの第３，第４引数へ、リダイレクトに割り当てるHTTPステータスコードと、追加のレスポンスヘッダを指定できます。

    return to_route('users.show', ['user' => 1], 302, ['X-Framework' => 'Laravel']);

<a name="method-url"></a>
#### `url()` {.collection-method}

`url`関数は指定したパスへの完全なURLを生成します。

    $url = url('user/profile');

    $url = url('user/profile', [1]);

パスを指定しない場合は、`Illuminate\Routing\UrlGenerator`インスタンスを返します。

    $current = url()->current();

    $full = url()->full();

    $previous = url()->previous();

<a name="miscellaneous"></a>
## その他

<a name="method-abort"></a>
#### `abort()` {.collection-method}

`abort`関数は、[例外ハンドラ](/docs/{{version}}/errors#the-exception-handler)によりレンダされるであろう、[HTTP例外](/docs/{{version}}/errors#http-exceptions)を投げます。

    abort(403);

ブラウザに送信する必要のある例外のメッセージとカスタムHTTP応答ヘッダを指定することもできます。

    abort(403, 'Unauthorized.', $headers);

<a name="method-abort-if"></a>
#### `abort_if()` {.collection-method}

`abort_if`関数は、指定された論理値が`true`と評価された場合に、HTTP例外を投げます。

    abort_if(! Auth::user()->isAdmin(), 403);

`abort`メソッドと同様に、例外の応答テキストを３番目の引数として指定し、カスタム応答ヘッダの配列を４番目の引数として関数に指定することもできます。

<a name="method-abort-unless"></a>
#### `abort_unless()` {.collection-method}

`abort_unless`関数は、指定した論理値が`false`と評価された場合に、HTTP例外を投げます。

    abort_unless(Auth::user()->isAdmin(), 403);

`abort`メソッドと同様に、例外の応答テキストを３番目の引数として指定し、カスタム応答ヘッダの配列を４番目の引数として関数に指定することもできます。

<a name="method-app"></a>
#### `app()` {.collection-method}

`app`関数は、[サービスコンテナ](/docs/{{version}}/container)のインスタンスを返します。

    $container = app();

コンテナにより依存解決する、クラス名かインターフェイス名を渡すこともできます。

    $api = app('HelpSpot\API');

<a name="method-auth"></a>
#### `auth()` {.collection-method}

`auth`関数は、[Authenticator](/docs/{{version}}/authentication)インスタンスを返します。`Auth`ファサードの代わりに使用できます。

    $user = auth()->user();

必要であれば、アクセスしたいガードインスタンスを指定することもできます。

    $user = auth('admin')->user();

<a name="method-back"></a>
#### `back()` {.collection-method}

`back`関数はユーザーの直前のロケーションへの[リダイレクトHTTPレスポンス](/docs/{{version}}/responses#redirects)を生成します。

    return back($status = 302, $headers = [], $fallback = '/');

    return back();

<a name="method-bcrypt"></a>
#### `bcrypt()` {.collection-method}

`bcrypt`関数はBcryptを使用して指定された値を[ハッシュ](/docs/{{version}}/hashing)します。この関数は、`Hash`ファサードの代わりに使用できます。

    $password = bcrypt('my-secret-password');

<a name="method-blank"></a>
#### `blank()` {.collection-method}

`blank`関数は、指定された値が「空白」であるかどうかを判別します。

    blank('');
    blank('   ');
    blank(null);
    blank(collect());

    // true

    blank(0);
    blank(true);
    blank(false);

    // false

`blank`の逆の動作は、[`filled`](#method-filled)メソッドです。

<a name="method-broadcast"></a>
#### `broadcast()` {.collection-method}

`broadcast`関数は、指定した[イベント](/docs/{{version}}/events)をリスナへ[ブロードキャスト](/docs/{{version}}/broadcasting)します。

    broadcast(new UserRegistered($user));

    broadcast(new UserRegistered($user))->toOthers();

<a name="method-cache"></a>
#### `cache()` {.collection-method}

`cache`関数は[キャッシュ](/docs/{{version}}/cache)から値を取得するために使用します。キャッシュに指定したキーが存在しない場合、オプション値が返されます。

    $value = cache('key');

    $value = cache('key', 'default');

関数にキー／値ペアの配列を渡すと、アイテムをキャッシュへ追加します。さらに秒数、もしくはキャッシュ値が有効であると推定される期限を渡すこともできます。

    cache(['key' => 'value'], 300);

    cache(['key' => 'value'], now()->addSeconds(10));

<a name="method-class-uses-recursive"></a>
#### `class_uses_recursive()` {.collection-method}

`class_uses_recursive`関数は、すべての親で使われているものも含め、クラス中で使用されているトレイトをすべて返します。

    $traits = class_uses_recursive(App\Models\User::class);

<a name="method-collect"></a>
#### `collect()` {.collection-method}

`collect`関数は、指定値から[コレクション](/docs/{{version}}/collections)インスタンスを生成します。

    $collection = collect(['taylor', 'abigail']);

<a name="method-config"></a>
#### `config()` {.collection-method}

`config`関数は[設定](/docs/{{version}}/configuration)変数の値を取得します。設定値はファイル名とアクセスしたいオプションを「ドット」記法で指定します。デフォルト値が指定でき、設定オプションが存在しない時に返されます。

    $value = config('app.timezone');

    $value = config('app.timezone', $default);

キー／値ペアの配列を渡すことにより、実行時に設定変数を設定できます。ただし、この関数は現在のリクエストの設定値にのみ影響し、実際の設定値は更新しないことに注意してください。

    config(['app.debug' => true]);

<a name="method-cookie"></a>
#### `cookie()` {.collection-method}

`cookie`関数は新しい[クッキー](/docs/{{version}}/requests#cookies)インスタンスを生成します。

    $cookie = cookie('name', 'value', $minutes);

<a name="method-csrf-field"></a>
#### `csrf_field()` {.collection-method}

`csrf_field`関数は、CSRFトークン値を持つHTML「隠し」入力フィールドを生成します。[ブレード記法](/docs/{{version}}/blade)を使用した例です。

    {{ csrf_field() }}

<a name="method-csrf-token"></a>
#### `csrf_token()` {.collection-method}

`csrf_token`関数は、現在のCSRFトークン値を取得します。

    $token = csrf_token();

<a name="method-decrypt"></a>
#### `decrypt()` {.collection-method}

`decrypt`関数は、指定した値を [復号化](/docs/{{version}}/encryption) します。この関数は、`Crypt`ファサードの代わりとして使用できます。

    $password = decrypt($value);

<a name="method-dd"></a>
#### `dd()` {.collection-method}

`dd`関数は指定された変数の内容を表示し、スクリプトの実行を停止します。

    dd($value);

    dd($value1, $value2, $value3, ...);

スクリプトの実行を停止したくない場合は、代わりに[`dump`](#method-dump)関数を使ってください。

<a name="method-dispatch"></a>
#### `dispatch()` {.collection-method}

`dispatch`関数は、指定した[ジョブ](/docs/{{version}}/queues#creating-jobs)をLaravelの[ジョブキュー](/docs/{{version}}/queues)へ投入します。

    dispatch(new App\Jobs\SendEmails);

<a name="method-dump"></a>
#### `dump()` {.collection-method}

`dump`関数は指定した変数をダンプします。

    dump($value);

    dump($value1, $value2, $value3, ...);

変数の値をダンプした後に実行を停止したい場合は、代わりに[`dd`](#method-dd)関数を使用してください。

<a name="method-encrypt"></a>
#### `encrypt()` {.collection-method}

`encrypt`関数は、指定した値を[暗号化](/docs/{{version}}/encryption)します。この関数は、`Crypt`ファサードの代わりに使用できます。

    $secret = encrypt('my-secret-value');

<a name="method-env"></a>
#### `env()` {.collection-method}

`env`関数は[環境変数](/docs/{{version}}/configuration#environment-configuration)の値を取得します。取得できない場合はデフォルト値を返します。

    $env = env('APP_ENV');

    $env = env('APP_ENV', 'production');

> **Warning**
> 開発期間中に`config:cache`コマンドを実行する場合は、設定ファイルの中で必ず`env`関数だけを使用してください。設定ファイルがキャッシュされると、`.env`ファイルはロードされなくなり、`env`関数の呼び出しはすべて`null`を返します。

<a name="method-event"></a>
#### `event()` {.collection-method}

`event`関数は、指定した[イベント](/docs/{{version}}/events)をリスナにディスパッチします。

    event(new UserRegistered($user));

<a name="method-fake"></a>
#### `fake()` {.collection-method}

`fake`関数は、コンテナから[Faker](https://github.com/FakerPHP/Faker)シングルトンを依存解決します。これは、モデルファクトリ、データベース初期値設定、テスト、プロトタイピングビューでフェイクデータを作成する場合に便利です。

```blade
@for($i = 0; $i < 10; $i++)
    <dl>
        <dt>Name</dt>
        <dd>{{ fake()->name() }}</dd>

        <dt>Email</dt>
        <dd>{{ fake()->unique()->safeEmail() }}</dd>
    </dl>
@endfor
```

`fake`関数はデフォルトで、`config/app.php`設定ファイルの`app.faker_locale`設定オプションを利用しますが、`fake`関数でロケールを直接指定することもできます。各ロケールは個々のシングルトンを解決します:

    fake('nl_NL')->name()

<a name="method-filled"></a>
#### `filled()` {.collection-method}

`filled`関数は、指定された値が「空白」でないかどうかを判別します。

    filled(0);
    filled(true);
    filled(false);

    // true

    filled('');
    filled('   ');
    filled(null);
    filled(collect());

    // false

`filled`の逆の動作は、[`blank`](#method-blank)メソッドです。

<a name="method-info"></a>
#### `info()` {.collection-method}

`info`関数は、アプリケーションの[ログ](/docs/{{version}}/logging)へ情報を書き込みます。

    info('Some helpful information!');

関連情報の配列を関数へ渡すこともできます。

    info('User login attempt failed.', ['id' => $user->id]);

<a name="method-logger"></a>
#### `logger()` {.collection-method}

`logger`関数は、`debug`レベルのメッセージを[ログ](/docs/{{version}}/logging)へ書き出します。

    logger('Debug message');

関連情報の配列を関数へ渡すこともできます。

    logger('User has logged in.', ['id' => $user->id]);

関数に値を渡さない場合は、[ロガー](/docs/{{version}}/errors#logging)インスタンスが返されます。

    logger()->error('You are not allowed here.');

<a name="method-method-field"></a>
#### `method_field()` {.collection-method}

`method_field`関数はフォームのHTTP動詞の見せかけの値を保持する「隠し」HTTP入力フィールドを生成します。[Blade記法](/docs/{{version}}/blade)を使う例です。

    <form method="POST">
        {{ method_field('DELETE') }}
    </form>

<a name="method-now"></a>
#### `now()` {.collection-method}

`now`関数は、現時点を表す新しい`Illuminate\Support\Carbon`インスタンスを生成します。

    $now = now();

<a name="method-old"></a>
#### `old()` {.collection-method}

`old`関数はセッションに一時保持データーとして保存されている[直前の入力値](/docs/{{version}}/requests#old-input)を[取得](/docs/{{version}}/requests#retrieving-input)します。

    $value = old('value');

    $value = old('value', 'default');

`old`関数の第２引数として指定される「デフォルト値」は、Eloquentモデルの属性であることが多いため、LaravelではEloquentモデル全体を`old`関数の第２引数として渡せるようになっています。この場合、Laravelは`old`関数に渡された最初の引数を「デフォルト値」とみなすべきEloquent属性の名前として扱います。

    {{ old('name', $user->name) }}

    // 上記は下記と同じ

    {{ old('name', $user) }}

<a name="method-optional"></a>
#### `optional()` {.collection-method}

`optional`関数はどんな引数も指定でき、そのオブジェクトのプロパティへアクセスするか、メソッドを呼び出せます。指定したオブジェクトが`null`だった場合、エラーを発生させる代わりに、プロパティとメソッドは`null`を返します。

    return optional($user->address)->street;

    {!! old('name', optional($user)->name) !!}

`optional`関数は、２番目の引数としてクロージャも受け入れます。最初の引数として指定された値がnullでない場合、クロージャが呼び出されます。

    return optional(User::find($id), function (User $user) {
        return $user->name;
    });

<a name="method-policy"></a>
#### `policy()` {.collection-method}

`policy`関数は、指定クラスの[ポリシー](/docs/{{version}}/authorization#creating-policies)インスタンスを取得します。

    $policy = policy(App\Models\User::class);

<a name="method-redirect"></a>
#### `redirect()` {.collection-method}

`redirect`関数は、[リダイレクトHTTPレスポンス](/docs/{{version}}/responses#redirects)を返します。引数無しで呼び出した場合は、リダイレクタインスタンスを返します。

    return redirect($to = null, $status = 302, $headers = [], $https = null);

    return redirect('/home');

    return redirect()->route('route.name');

<a name="method-report"></a>
#### `report()` {.collection-method}

`report`関数は[例外ハンドラ](/docs/{{version}}/errors#the-exception-handler)を使用して例外をレポートします。

    report($e);

`report`関数は文字列を引数に取ります。関数に文字列が与えられると、関数は指定する文字列をメッセージとする例外を作成します。

    report('Something went wrong.');

<a name="method-report-if"></a>
#### `report_if()` {.collection-method}

`report_if`関数は、指定条件が`true`であれば、あなたの[例外ハンドラ](/docs/{{version}}/errors#the-exception-handler)を使って例外を報告します。

    report_if($shouldReport, $e);

    report_if($shouldReport, 'Something went wrong.');

<a name="method-report-unless"></a>
#### `report_unless()` {.collection-method}

`report_unless`関数は、指定条件が`false`であれば、あなたの[例外ハンドラ](/docs/{{version}}/errors#the-exception-handler)を使って例外を報告します。

    report_unless($reportingDisabled, $e);

    report_unless($reportingDisabled, 'Something went wrong.');

<a name="method-request"></a>
#### `request()` {.collection-method}

`request`関数は、現在の[request](/docs/{{version}}/requests)インスタンスを返すか、現在のリクエストから入力フィールドの値を取得します。

    $request = request();

    $value = request('key', $default);

<a name="method-rescue"></a>
#### `rescue()` {.collection-method}

`rescue`関数は指定されたクロージャを実行し、その実行中に発生した例外をすべてキャッチします。キャッチした例外はすべて[例外ハンドラ](/docs/{{version}}/errors#the-exception-handler)へ送られますが、リクエストの処理は続行されます。

    return rescue(function () {
        return $this->method();
    });

`rescue`関数に２番目の引数を渡すこともできます。この引数は、クロージャの実行で例外が発生した場合に返す「デフォルト」値です。

    return rescue(function () {
        return $this->method();
    }, false);

    return rescue(function () {
        return $this->method();
    }, function () {
        return $this->failure();
    });

<a name="method-resolve"></a>
#### `resolve()` {.collection-method}

`resolve`関数は、[サービスコンテナ](/docs/{{version}}/container)を使用して、指定したクラスまたはインターフェイス名をインスタンスへ依存解決します。

    $api = resolve('HelpSpot\API');

<a name="method-response"></a>
#### `response()` {.collection-method}

`response`関数は[response](/docs/{{version}}/responses)インスタンスを返すか、レスポンスファクトリのインスタンスを取得します。

    return response('Hello World', 200, $headers);

    return response()->json(['foo' => 'bar'], 200, $headers);

<a name="method-retry"></a>
#### `retry()` {.collection-method}

`retry`関数は指定された最大試行回数を過ぎるまで、指定されたコールバックを実行します。コールバックが例外を投げなければ、返却値を返します。コールバックが例外を投げた場合は、自動的にリトライします。最大試行回数を超えると、例外を投げます。

    return retry(5, function () {
        // 試行間に100ms待ち、5回試みる
    }, 100);

もし、試行間隔を何ミリ秒にするかを手作業で計算したい場合は、`retry`関数の第３引数へクロージャを渡します。

    use Exception;

    return retry(5, function () {
        // ...
    }, function (int $attempt, Exception $exception) {
        return $attempt * 100;
    });

便利なように、`retry`関数の最初の引数には配列を指定することもできます。この配列は、次の再試行の間に何ミリ秒スリープさせるかを決定するために使用されます。

    return retry([100, 200], function () {
        // 最初に100ms、２回目に200msスリープする
    });

特定条件下でのみ再試行するには、`retry`関数への4番目の引数としてクロージャを渡せます。

    use Exception;

    return retry(5, function () {
        // ...
    }, 100, function (Exception $exception) {
        return $exception instanceof RetryException;
    });

<a name="method-session"></a>
#### `session()` {.collection-method}

`session`関数は[セッション](/docs/{{version}}/session)へ値を設定、もしくは取得するために使用します。

    $value = session('key');

キー／値ペアの配列を渡し、値を設定できます。

    session(['chairs' => 7, 'instruments' => 3]);

関数に引数を渡さない場合は、セッション保存域が返されます。

    $value = session()->get('key');

    session()->put('key', $value);

<a name="method-tap"></a>
#### `tap()` {.collection-method}

`tap`関数は、任意の`$value`とクロージャの２つの引数を受け入れます。`$value`はクロージャに渡され、`tap`関数によって返されます。クロージャの戻り値は関係ありません。

    $user = tap(User::first(), function (User $user) {
        $user->name = 'taylor';

        $user->save();
    });

`tap`関数にクロージャが渡されない場合は、指定した`$value`で任意のメソッドを呼び出せます。呼び出すメソッドの戻り値は、メソッドがその定義で実際に何を返すかに関係なく、常に`$value`になります。たとえば、Eloquentの`update`メソッドは通常、整数を返します。しかし、`tap`関数を介して`update`メソッド呼び出しをチェーンすることで、メソッドへモデル自体を返すように強制できます。

    $user = tap($user)->update([
        'name' => $name,
        'email' => $email,
    ]);

クラスへ`tap`メソッドを追加するには、`Illuminate\Support\Traits\Tappable`トレイトをそのクラスへ追加してください。このトレイトの`tap`メソッドはクロージャだけを引数に受け取ります。オブジェクトインスタンス自身がクロージャに渡され、`tap`メソッドによりリターンされます。

    return $user->tap(function (User $user) {
        // ...
    });

<a name="method-throw-if"></a>
#### `throw_if()` {.collection-method}

`throw_if`関数は、指定した論理式が`true`と評価された場合に、指定した例外を投げます。

    throw_if(! Auth::user()->isAdmin(), AuthorizationException::class);

    throw_if(
        ! Auth::user()->isAdmin(),
        AuthorizationException::class,
        'You are not allowed to access this page.'
    );

<a name="method-throw-unless"></a>
#### `throw_unless()` {.collection-method}

`throw_unless`関数は、指定した論理式が`false`と評価された場合に、指定した例外を投げます。

    throw_unless(Auth::user()->isAdmin(), AuthorizationException::class);

    throw_unless(
        Auth::user()->isAdmin(),
        AuthorizationException::class,
        'You are not allowed to access this page.'
    );

<a name="method-today"></a>
#### `today()` {.collection-method}

`today`関数は、現在の日付を表す新しい`Illuminate\Support\Carbon`インスタンスを生成します。

    $today = today();

<a name="method-trait-uses-recursive"></a>
#### `trait_uses_recursive()` {.collection-method}

`trait_uses_recursive`関数は、そのトレイトで使用されている全トレイトを返します。

    $traits = trait_uses_recursive(\Illuminate\Notifications\Notifiable::class);

<a name="method-transform"></a>
#### `transform()` {.collection-method}

`transform`関数は、値が[空白](#method-blank)でない場合、指定値に対してクロージャを実行し、クロージャの戻り値を返します。

    $callback = function (int $value) {
        return $value * 2;
    };

    $result = transform(5, $callback);

    // 10

デフォルト値またはクロージャは、関数の３番目の引数として渡せます。指定値が空白の場合、この値をが返します。

    $result = transform(null, $callback, 'The value is blank');

    // 値は空白

<a name="method-validator"></a>
#### `validator()` {.collection-method}

`validator`関数は、引数を使用して新しい[バリデータ](/docs/{{version}}/validation)インスタンスを作成します。`Validator`ファサードの代わりに使用できます。

    $validator = validator($data, $rules, $messages);

<a name="method-value"></a>
#### `value()` {.collection-method}

`value`関数は、指定値を返します。ただし、関数へクロージャを渡すと、そのクロージャを実行し、その戻り値を返します。

    $result = value(true);

    // true

    $result = value(function () {
        return false;
    });

    // false

`value`関数に追加の引数を渡すこともできます。最初の引数がクロージャであれば、追加した引数はクロージャの引数として渡されます。クロージャでなければ、無視します。

    $result = value(function (string $name) {
        return $name;
    }, 'Taylor');

    // 'Taylor'

<a name="method-view"></a>
#### `view()` {.collection-method}

`view`関数は[view](/docs/{{version}}/views)インスタンスを返します。

    return view('auth.login');

<a name="method-with"></a>
#### `with()` {.collection-method}

`with`関数は、指定値を返します。関数の２番目の引数としてクロージャを渡たすと、クロージャが実行され、その戻り値を返します。

    $callback = function (mixed $value) {
        return is_numeric($value) ? $value * 2 : 0;
    };

    $result = with(5, $callback);

    // 10

    $result = with(null, $callback);

    // 0

    $result = with(5, null);

    // 5

<a name="other-utilities"></a>
## その他のユーティリティ

<a name="benchmarking"></a>
### ベンチマーク

時には、アプリケーションの特定の部分のパフォーマンスを素早くテストしたいと思うこともあるでしょう。そのような場合は、`Benchmark`サポートクラスを利用して、指定するコールバックが完了するまでにかかるミリ秒を測定できます。

    <?php

    use App\Models\User;
    use Illuminate\Support\Benchmark;

    Benchmark::dd(fn () => User::find(1)); // 0.1 ms

    Benchmark::dd([
        'Scenario 1' => fn () => User::count(), // 0.5 ms
        'Scenario 2' => fn () => User::all()->count(), // 20.0 ms
    ]);

デフォルトでは、指定したコールバックは、１回（１繰り返し）実行され、その期間はブラウザ／コンソールに表示されます。

コールバックを複数回呼び出すには、メソッドの第２引数でコールバックを呼び出す反復回数を指定してください。コールバックを複数回実行する場合、`Benchmark`クラスはコールバックの実行にかかった平均ミリ秒を返します。

    Benchmark::dd(fn () => User::count(), iterations: 10); // 0.5 ms

<a name="pipeline"></a>
### パイプライン

Laravelの`Pipeline`ファサードは、指定する入力を一連の呼び出し可能なクラス、クロージャ、Callableを通して、「パイプ」接続する便利な方法を提供し、各クラスに入力を検査または修正する機会を与え、パイプラインの次のCallableを呼び出します。

```php
use Closure;
use App\Models\User;
use Illuminate\Support\Facades\Pipeline;

$user = Pipeline::send($user)
            ->through([
                function (User $user, Closure $next) {
                    // ...

                    return $next($user);
                },
                function (User $user, Closure $next) {
                    // ...

                    return $next($user);
                },
            ])
            ->then(fn (User $user) => $user);
```

ご覧のように、パイプライン中の呼び出し可能な各クラスやクロージャには、入力と`$next`クロージャを引数に渡します。`next`クロージャが呼び出されると、パイプラインの次の呼び出し可能なクラスを呼び出します。お気づきかもしれませんが、これは [ミドルウェア](/docs/{{version}}/middleware) と非常によく似ています。

パイプラインの最後のCallableが`$next`クロージャを呼び出すと、`then`メソッドに渡されたCallableを呼び出します。一般的に、このCallableは単に与えられた入力を返すだけです。

もちろん、前述したように、パイプラインに渡すのは、クロージャに限定されません。呼び出し可能なクラスを提供することもできます。クラス名が提供された場合、そのクラスはLaravelの[サービスコンテナ](/docs/{{version}}/container)を通じてインスタンス化し、呼び出し可能なクラスへ依存関係を注入します。

```php
$user = Pipeline::send($user)
            ->through([
                GenerateProfilePhoto::class,
                ActivateSubscription::class,
                SendWelcomeEmail::class,
            ])
            ->then(fn (User $user) => $user);
```

<a name="lottery"></a>
### 抽選

Laravelの抽選クラスは、指定する一組のオッズに基づいて、コールバックを実行するために使用します。これは、受信リクエストの数パーセントに対してのみコードを実行したい場合、特に便利です。

    use Illuminate\Support\Lottery;

    Lottery::odds(1, 20)
        ->winner(fn () => $user->won())
        ->loser(fn () => $user->lost())
        ->choose();

Laravelの抽選クラスと他のLaravelの機能を組み合わせることも可能です。例えば、スロークエリの一部だけを例外ハンドラへ報告するようにしたい場合です。また、抽選クラスはCallableなため、Callableのメソッドを引数に取るクラスへインスタンスを渡せます。

    use Carbon\CarbonInterval;
    use Illuminate\Support\Facades\DB;
    use Illuminate\Support\Lottery;

    DB::whenQueryingForLongerThan(
        CarbonInterval::seconds(2),
        Lottery::odds(1, 100)->winner(fn () => report('Querying > 2 seconds.')),
    );

<a name="testing-lotteries"></a>
#### 抽選のテスト

Laravelは、アプリケーションの抽選呼び出しを簡単にテストするために、シンプルなメソッドを用意しています。

    // 常に当選する
    Lottery::alwaysWin();

    // 常に外れる
    Lottery::alwaysLose();

    // 当選し、次に外れたあとは、通常の振る舞いをする
    Lottery::fix([true, false]);

    // 通常の振る舞いで抽選する
    Lottery::determineResultsNormally();
