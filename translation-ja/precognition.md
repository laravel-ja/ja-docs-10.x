# Precognition

- [イントロダクション](#introduction)
- [ライブバリデーション](#live-validation)
    - [Vueの使用](#using-vue)
    - [VueとInertiaの使用](#using-vue-and-inertia)
    - [Reactの使用](#using-react)
    - [ReactとInertiaの使用](#using-react-and-inertia)
- [バリデーションルールのカスタマイズ](#customizing-validation-rules)
- [副作用の管理](#managing-side-effects)

<a name="introduction"></a>
## イントロダクション

Laravel Precognition（プリコグニション：予知）により、将来のHTTPリクエストの結果を予測できます。Precognitionの主な使用例の１つは、アプリケーションのバックエンドのバリデーションルールを複製せずとも、フロントエンドのJavaScriptアプリケーションの「ライブ」バリデーションを提供する能力です。Precognitionは、LaravelのInertiaベースの[スターターキット](/docs/{{version}}/starter-kits)と特に相性がよいです。

Laravelが「事前認識型リクエスト」を受け取ると、ルートのミドルウェアをすべて実行し、[フォームリクエスト](/docs/{{version}}/validation#form-request-validation)のバリデーションを含む、ルートのコントローラの依存解決を行いますが、実際にはルートのコントローラメソッドを実行しません。

<a name="live-validation"></a>
## ライブバリデーション

<a name="using-vue"></a>
### Vueの使用

Laravel Precognitionを使用すると、フロントエンドのVueアプリケーションでバリデーションルールを複製することなく、ユーザーにライブバリデーション体験を提供できます。その仕組みを説明するため、アプリケーション内で新規ユーザーを作成するためのフォームを作成してみましょう。

最初に、ルートに対するPrecognitionを有効にするには、ルート定義へ`HandlePrecognitiveRequests`ミドルウェアを追加する必要があります。さらに、ルートのバリデーションルールを格納するため、[フォームリクエスト](/docs/{{version}}/validation#form-request-validation)を作成する必要もあります。

```php
use App\Http\Requests\CreateUserRequest;
use Illuminate\Foundation\Http\Middleware\HandlePrecognitiveRequests;

Route::post('/users', function (CreateUserRequest $request) {
    // ...
})->middleware([HandlePrecognitiveRequests::class]);
```

次に、Vue用のLaravel PrecognitionフロントエンドヘルパをNPMにより、インストールします。

```shell
npm install laravel-precognition-vue
```

LaravelのPrecognitionパッケージをインストールすれば、Precognitionの`useForm`関数を使用して、HTTPメソッド（`post`）、ターゲットURL（`/users`）、初期フォームデータを指定して、フォームオブジェクトを作成できるようになります。

次に、ライブバリデーションを有効にするには、各入力の `change` イベントでフォームの`validate`メソッドを起動し、入力名を指定します。

```vue
<script setup>
import { useForm } from 'laravel-precognition-vue';

const form = useForm('post', '/users', {
    name: '',
    email: '',
});

const submit = () => form.submit();
</script>

<template>
    <form @submit.prevent="submit">
        <label for="name">Name</label>
        <input
            id="name"
            v-model="form.name"
            @change="form.validate('name')"
        />
        <div v-if="form.invalid('name')">
            {{ form.errors.name }}
        </div>

        <label for="email">Email</label>
        <input
            id="email"
            type="email"
            v-model="form.email"
            @change="form.validate('email')"
        />
        <div v-if="form.invalid('email')">
            {{ form.errors.email }}
        </div>

        <button>Create User</button>
    </form>
</template>
```

これで、フォームがユーザーにより入力されると、Precognitionはルートのフォームリクエストのバリデーションルールに従い、ライブバリデーション出力を提供します。フォームの入力が変更されると、デバウンスされた「事前認識型」バリデーションリクエストをLaravelアプリケーションへ送信します。デバウンスのタイムアウトは、フォームの `setValidationTimeout` 関数を呼び出すことで設定できます。

```js
form.setValidationTimeout(3000);
```

バリデーション要求がやり取り中の場合、フォームの`validating`プロパティは`true` になります。

```html
<div v-if="form.validating">
    Validating...
</div>
```

バリデーションリクエストやフォーム送信時に返される全てのバリデーションエラーは、自動的にフォームの`errors`オブジェクトへ格納します。

```html
<div v-if="form.invalid('email')">
    {{ form.errors.email }}
</div>
```

フォームにエラーがあるかは、フォームの`hasErrors`プロパティで判断できます。

```html
<div v-if="form.hasErrors">
    <!-- ... -->
</div>
```

また、入力がバリデーションに合格したか失敗したかを判断するには、入力名をフォームの`valid`関数か`invalid`関数へ渡してください。

```html
<span v-if="form.valid('email')">
    ✅
</span>

<span v-else-if="form.invalid('email')">
    ❌
</span>
```

> **Warning**
> フォーム入力が変更され、バリデーションレスポンスを受信した時点で、初めて有効または無効として表示されます。

もちろん、フォーム送信に対するレスポンスに反応してコードを実行することもできます。フォームの`submit`関数は、AxiosのリクエストPromiseを返します。これは、レスポンスペイロードへのアクセス、送信成功時のフォーム入力のリセット、または失敗したリクエストの処理に便利な方法を提供します。

```js
const submit = () => form.submit()
    .then(response => {
        form.reset();

        alert('User created.');
    })
    .catch(error => {
        alert('An error occurred.');
    });
```

<a name="using-vue-and-inertia"></a>
### VueとInertiaの使用

> **Note**
> VueとInertiaを使ってLaravelアプリケーションを開発するとき、有利に開始したい場合は、[スターターキット](/docs/{{version}}/starter-kits)の一つを使うことを検討してください。Laravelのスターターキットは、新しいLaravelアプリケーションにバックエンドとフロントエンドの認証へスカフォールドを提供します。

VueとInertiaと一緒にPrecognitionを使用する前に、より一般的な[VueでPrecognitionを使用する](#using-vue)ドキュメントを必ず確認してください。VueをInertiaで使用する場合、NPM経由でInertia互換のPrecognitionライブラリーをインストールする必要があります。

```shell
npm install laravel-precognition-vue-inertia
```

一度インストールしたら、Precognitionの`useForm`関数は、上で説明したバリデーション機能で強化した、Inertia[フォームヘルパ](https://inertiajs.com/forms#form-helper)を返します。

フォームヘルパの`submit`メソッドが効率化され、HTTPメソッドやURLを指定する必要がなくなりました。その代わりに、Inertiaの[visitオプション](https://inertiajs.com/manual-visits)を最初で唯一の引数として渡してください。また、`submit`メソッドは、上記のVueの例で見られるように、Promiseを返すことはありません。代わりに、Inertiaがサポートしている[イベントコールバック](https://inertiajs.com/manual-visits#event-callbacks)を`submit`メソッドへ指定するvisitオプションに指定します。

```vue
<script setup>
import { useForm } from 'laravel-precognition-vue-inertia';

const form = useForm('post', '/users', {
    name: '',
    email: '',
});

const submit = () => form.submit({
    preserveScroll: true,
    onSuccess: () => form.reset(),
});
</script>
```

<a name="using-react"></a>
### Reactの使用

Laravel Precognitionを使用すると、フロントエンドのReactアプリケーションへバリデーションルールを複製することなく、ユーザーにライブバリデーション体験を提供できます。その仕組みを説明するため、アプリケーション内で新規ユーザーを作成するためのフォームを作成してみましょう。

最初に、ルートでPrecognitionを有効にするには、ルート定義で`HandlePrecognitiveRequests`ミドルウェアを追加する必要があります。また、ルートのバリデーションルールを格納するために、[フォームrequest](/docs/{{version}}/validation#form-request-validation)を作成する必要があります。

```php
use App\Http\Requests\CreateUserRequest;
use Illuminate\Foundation\Http\Middleware\HandlePrecognitiveRequests;

Route::post('/users', function (CreateUserRequest $request) {
    // ...
})->middleware([HandlePrecognitiveRequests::class]);
```

次に、React用のLaravel PrecognitionフロントエンドヘルパをNPMによりインストールします。

```shell
npm install laravel-precognition-react
```

LaravelのPrecognitionパッケージをインストールすれば、Precognitionの`useForm`関数を使用して、HTTPメソッド（`post`）、ターゲットURL（`/users`）、初期フォームデータを指定し、フォームオブジェクトを作成できます。

ライブバリデーションを有効にするには、各入力の`change`イベントと`blur`イベントをリッスンする必要があります。`change`イベントハンドラでは、`setData`関数でフォームのデータをセットし、入力名と新しい値を渡します。次に、`blur`イベントハンドラで、入力名を指定し、フォームの`validate`メソッドを呼び出します。

```jsx
import { useForm } from 'laravel-precognition-react';

export default function Form() {
    const form = useForm('post', '/users', {
        name: '',
        email: '',
    });

    const submit = (e) => {
        e.preventDefault();

        form.submit();
    };

    return (
        <form onSubmit={submit}>
            <label for="name">Name</label>
            <input
                id="name"
                value={form.data.name}
                onChange={(e) => form.setData('name', e.target.value)}
                onBlur={() => form.validate('name')}
            />
            {form.invalid('name') ? (<div>{form.errors.name}</div>) : null}

            <label for="email">Email</label>
            <input
                id="email"
                value={form.data.email}
                onChange={(e) => form.setData('email', e.target.value)}
                onBlur={() => form.validate('email')}
            />
            {form.invalid('email') ? (<div>{form.errors.email}</div>) : null}

            <button>Create User</button>
        </form>
    );
};
```

フォームがユーザーにより入力されると、Precognitionはルートのフォームリクエストのバリデーションルールにより、ライブバリデーション出力を提供します。フォームの入力が変更されると、デバウンスされた「事前認識型」バリデーションリクエストをLaravelアプリケーションへ送信します。デバウンスのタイムアウトは、フォームの`setValidationTimeout`関数を呼び出し設定します。

```js
form.setValidationTimeout(3000);
```

バリデーション要求がやり取り中の場合、フォームの`validating`プロパティは`true` になります。

```jsx
{form.validating ? (<div>Validating...</div>) : null}
```

バリデーションリクエストやフォーム送信時に返される全てのバリデーションエラーは、自動的にフォームの`errors`オブジェクトへ格納します。

```jsx
{form.invalid('email') ? (<div>{form.errors.email}</div>) : null}
```

フォームにエラーがあるかは、フォームの`hasErrors`プロパティで判断できます。

```jsx
{form.hasErrors ? (<div><!-- ... --></div>) : null}
```

また、入力がバリデーションに合格したか失敗したかを判断するには、入力名をフォームの`valid`関数か`invalid`関数へ渡してください。

```jsx
{form.valid('email') ? (<span>✅</span>) : null}

{form.invalid('email') ? (<span>❌</span>) : null}
```

> **Warning**
> フォーム入力が変更され、バリデーションレスポンスを受信した時点で、初めて有効または無効として表示されます。

もちろん、フォーム送信に対するレスポンスに反応してコードを実行することもできます。フォームの`submit`関数は、AxiosのリクエストPromiseを返します。これは、レスポンスペイロードへのアクセス、送信成功時のフォーム入力のリセット、または失敗したリクエストの処理に便利な方法を提供します。

```js
const submit = (e) => {
    e.preventDefault();

    form.submit()
        .then(response => {
            form.reset();

            alert('User created.');
        })
        .catch(error => {
            alert('An error occurred.');
        });
};
```

<a name="using-react-and-inertia"></a>
### ReactとInertiaの使用

> **Note**
> ReactとInertiaを使ってLaravelアプリケーションを開発するとき、有利に開始したい場合は、[スターターキット](/docs/{{version}}/starter-kits)の一つを使うことを検討してください。Laravelのスターターキットは、新しいLaravelアプリケーションにバックエンドとフロントエンドの認証へスカフォールドを提供します。

ReactとInertiaと一緒にPrecognitionを使用する前に、より一般的な[VueでPrecognitionを使用する](#using-vue)ドキュメントを必ず確認してください。VueをInertiaで使用する場合、NPM経由でInertia互換のPrecognitionライブラリーをインストールする必要があります。

```shell
npm install laravel-precognition-react-inertia
```

一度インストールしたら、Precognitionの`useForm`関数は、上で説明したバリデーション機能で強化した、Inertia[フォームヘルパ](https://inertiajs.com/forms#form-helper)を返します。

フォームヘルパの`submit`メソッドが効率化され、HTTPメソッドやURLを指定する必要がなくなりました。その代わりに、Inertiaの[visitオプション](https://inertiajs.com/manual-visits)を最初で唯一の引数として渡してください。また、`submit`メソッドは、上記のReactの例で見られるように、Promiseを返すことはありません。代わりに、Inertiaがサポートしている[イベントコールバック](https://inertiajs.com/manual-visits#event-callbacks)を`submit`メソッドへ指定するvisitオプションに指定します。

```js
import { useForm } from 'laravel-precognition-react-inertia';

const form = useForm('post', '/users', {
    name: '',
    email: '',
});

const submit = (e) => {
    e.preventDefault();

    form.submit({
        preserveScroll: true,
        onSuccess: () => form.reset(),
    });
};
```

<a name="customizing-validation-rules"></a>
## バリデーションルールのカスタマイズ

リクエストの`isPrecognitive`メソッドを使用すれば、事前認識型リクエスト時に実行するバリデーションルールをカスタマイズできます。

例えば、ユーザー作成フォームにおいて、最終的なフォーム送信時にのみ、パスワードの「データ漏洩」をバリデートしたい場合があります。事前認識型のバリデーションリクエストでは、パスワードが必須であることと、最低8文字であることをバリデートします。`isPrecognitive`メソッドを使用すると、フォームリクエストで定義されたルールをカスタマイズできます。

```php
<?php

namespace App\Http\Requests;

use Illuminate\Foundation\Http\FormRequest;
use Illuminate\Validation\Rules\Password;

class StoreUserRequest extends FormRequest
{
    /**
     * リクエストへ適用するバリデーションルールの取得
     *
     * @return array
     */
    protected function rules()
    {
        return [
            'password' => [
                'required',
                $this->isPrecognitive()
                    ? Password::min(8)
                    : Password::min(8)->uncompromised(),
            ],
            // ...
        ];
    }
}
```

<a name="managing-side-effects"></a>
## 副作用の管理

`HandlePrecognitiveRequests`ミドルウェアをルートに追加する場合、**他の**ミドルウェアで事前認識型リクエスト時にスキップすべき副作用があるかどうかを検討する必要があります。

例えば、各ユーザーがアプリケーションとやり取りした「操作」の総数を増加させるミドルウェアがあり、事前認識型リクエストは操作としてカウントしたくない場合です。これを実現するには、操作数を増やす前にリクエストの`isPrecognitive`メソッドをチェックする必要があるでしょう。

```php
<?php

namespace App\Http\Middleware;

use App\Facades\Interaction;
use Closure;
use Illuminate\Http\Request;

class InteractionMiddleware
{
    /**
     * 受信リクエストの処理
     */
    public function handle(Request $request, Closure $next): mixed
    {
        if (! $request->isPrecognitive()) {
            Interaction::incrementFor($request->user());
        }

        return $next($request);
    }
}
```
