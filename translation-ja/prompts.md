# Prompts

- [イントロダクション](#introduction)
- [インストール](#installation)
  - [利用可能なプロンプト](#available-prompts)
    - [テキスト](#text)
    - [パスワード](#password)
    - [確認](#confirm)
    - [選択](#select)
    - [複数選択](#multiselect)
    - [候補](#suggest)
    - [検索](#search)
- [ターミナルの考察](#terminal-considerations)
- [未サポートの環境とフォールバック](#fallbacks)

<a name="introduction"></a>
## イントロダクション

Laravel Promptsは、美しくユーザーフレンドリーなUIをコマンドラインアプリケーションに追加するためのPHPパッケージで、プレースホルダテキストやバリデーションなどのブラウザにあるような機能を備えています。

Laravel Promptsは、[Artisanコンソールコマンド](/docs/{{version}}/artisan#writing-commands)でユーザー入力を受けるために最適ですが、コマンドラインのPHPプロジェクトでも使用できます。

> **Note**
> Laravel PromptsはmacOS、Linux、WindowsのWSLをサポートしています。詳しくは、[未サポートの環境とフォールバック](#fallbacks)のドキュメントをご覧ください。

<a name="インストール"></a>
## インストール

Laravelの最新リリースには、はじめからLaravel Promptsを用意してあります。

Composerパッケージマネージャを使用して、他のPHPプロジェクトにLaravel Promptsをインストールすることもできます。

```shell
composer require laravel/prompts
```

<a name="available-prompts"></a>
## 利用可能なプロンプト

<a name="text"></a>
### テキスト

`text`関数は、指定した質問でユーザーに入力を促し、回答を受け、それを返します。

```php
use function Laravel\Prompts\text;

$name = text('What is your name?');
```

プレースホルダ・テキストとデフォルト値も指定できます。

```php
$name = text(
    label: 'What is your name?',
    placeholder: 'E.g. Taylor Otwell',
    default: $user?->name
);
```

<a name="text-required"></a>
#### 必須値

入力値が必須の場合は、`required`引数を渡してください。

```php
$name = text(
    label: 'What is your name?',
    required: true
);
```

バリデーション・メッセージをカスタマイズしたい場合は、文字列を渡すこともできます。

```php
$name = text(
    label: 'What is your name?',
    required: 'Your name is required.'
);
```

<a name="text-validation"></a>
#### 追加のバリデーション

最後に、追加のバリデーションロジックを実行したい場合は、`validate`引数にクロージャを渡します。

```php
$name = text(
    label: 'What is your name?',
    validate: fn (string $value) => match (true) {
        strlen($value) < 3 => 'The name must be at least 3 characters.',
        strlen($value) > 255 => 'The name must not exceed 255 characters.',
        default => null
    }
);
```

クロージャは入力された値を受け取り、エラーメッセージを返すか、バリデーションに合格した場合は、`null`を返します。

<a name="password"></a>
### パスワード

`password`関数は`text`関数と似ていますが、コンソールで入力されるユーザー入力をマスクします。これはパスワードのような機密情報を要求するときに便利です：

```php
use function Laravel\Prompts\password;

$password = password('What is your password?');
```

プレースホルダーのテキストを含めることもできます。

```php
$password = password(
    label: 'What is your password?',
    placeholder: 'Minimum 8 characters...'
);
```

<a name="password-required"></a>
#### 必須値

入力値が必須の場合は、`required`引数を渡してください。

```php
$name = password(
    label: 'What is your password?',
    required: true
);
```

バリデーション・メッセージをカスタマイズしたい場合は、文字列を渡すこともできます。

```php
$name = password(
    label: 'What is your password?',
    required: 'The password is required.'
);
```

<a name="password-validation"></a>
#### 追加のバリデーション

最後に、追加のバリデーションロジックを実行したい場合は、`validate`引数にクロージャを渡します。

```php
$name = password(
    label: 'What is your password?',
    validate: fn (string $value) => match (true) {
        strlen($value) < 8 => 'The password must be at least 8 characters.',
        default => null
    }
);
```

クロージャは入力された値を受け取り、エラーメッセージを返すか、バリデーションに合格した場合は、`null`を返します。

<a name="confirm"></a>
### 確認

ユーザーに "yes／no"の確認を求める必要がある場合は、`confirm`関数を使います。ユーザーは矢印キーを使うか、`y`または`n`を押して回答を選択します。この関数は`true`または`false`を返します。

```php
use function Laravel\Prompts\confirm;

$confirmed = confirm('Do you accept the terms?');
```

デフォルトで、"Yes"の答えがあらかじめ選択されています。しかし、`default`引数に`false`を渡せば、デフォルトを"No"に設定できます。

```php
$confirmed = confirm(
    label: 'Do you accept the terms?',
    default: false,
);
```

`yes`と`no`の引数を渡すば、"Yes"と "No"のラベルに使われる文言をカスタマイズすることもできます。

```php
$confirmed = confirm(
    label: 'Do you accept the terms?',
    yes: 'I accept',
    no: 'I decline'
);
```

<a name="confirm-required"></a>
#### 必須の"Yes"

必要であれば、`required`引数を渡し、ユーザーに"Yes"を選択させることもできます。

```php
$confirmed = confirm(
    label: 'Do you accept the terms?',
    required: true
);
```

バリデーション・メッセージをカスタマイズしたい場合は、文字列を渡すこともできます。

```php
$confirmed = confirm(
    label: 'Do you accept the terms?',
    required: 'You must accept the terms to continue.'
);
```

<a name="select"></a>
### 選択

ユーザーにあらかじめ定義された選択肢から選ばせる必要がある場合は、`select`関数を使います。

```php
use function Laravel\Prompts\select;

$role = select(
    'What role should the user have?',
    ['Member', 'Contributor', 'Owner'],
);
```

`default`引数を渡せば、デフォルトの選択肢を指定することもできます。

```php
$role = select(
    label: 'What role should the user have?',
    options: ['Member', 'Contributor', 'Owner'],
    default: 'Owner'
);
```

`options`引数に連想配列を渡し、選択値の代わりにキーを返すこともできます。

```php
$role = select(
    label: 'What role should the user have?',
    options: [
        'member' => 'Member',
        'contributor' => 'Contributor',
        'owner' => 'Owner'
    ],
    default: 'owner'
);
```

５選択肢を超えると、選択肢がリスト表示されます。`scroll`引数を渡し、カスタマイズ可能です。

```php
$role = select(
    label: 'Which category would you like to assign?',
    options: Category::pluck('name', 'id'),
    scroll: 10
);
```

<a name="select-validation"></a>
#### バリデーション

他のプロンプト関数とは異なり、他に何も選択できなくなるため、`select`関数は`required`引数を受けません。しかし、選択肢を提示したいが、選択されないようにする必要がある場合は、`validate`引数にクロージャを渡してください。

```php
$role = select(
    label: 'What role should the user have?'
    options: [
        'member' => 'Member',
        'contributor' => 'Contributor',
        'owner' => 'Owner'
    ],
    validate: fn (string $value) =>
        $value === 'owner' && User::where('role', 'owner')->exists()
            ? 'An owner already exists.'
            : null
    }
)
```

`options`引数が連想配列の場合、クロージャは選択されたキーを受け取ります。連想配列でない場合は選択された値を受け取ります。クロージャはエラーメッセージを返すか、バリデーションに成功した場合は`null`を返してください。

<a name="multiselect"></a>
### 複数選択

ユーザーが複数の選択肢を選択できるようにする必要がある場合は、`multiselect`関数を使用します。

```php
use function Laravel\Prompts\multiselect;

$permissions = multiselect(
    'What permissions should be assigned?',
    ['Read', 'Create', 'Update', 'Delete']
);
```

`default`引数に配列を渡すことで、あらかじめ選択済みの選択肢を指定することもできます。

```php
use function Laravel\Prompts\multiselect;

$permissions = multiselect(
    label: 'What permissions should be assigned?',
    options: ['Read', 'Create', 'Update', 'Delete'],
    default: ['Read', 'Create']
);
```

`options`引数に連想配列を渡し、選択値の代わりにキーを返させることもできます。

```
$permissions = multiselect(
    label: 'What permissions should be assigned?',
    options: [
        'read' => 'Read',
        'create' => 'Create',
        'update' => 'Update',
        'delete' => 'Delete'
    ],
    default: ['read', 'create']
);
```

５選択肢を超えると、選択肢がリスト表示されます。`scroll`引数を渡し、カスタマイズ可能です。

```php
$role = multiselect(
    label: 'What categories should be assigned?',
    options: Category::pluck('name', 'id'),
    scroll: 10
);
```

<a name="multiselect-required"></a>
#### 必須値

デフォルトで、ユーザーは０個以上の選択肢を選択できます。`required`引数を渡せば、代わりに１つ以上の選択肢を強制できます。

```php
$role = multiselect(
    label: 'What categories should be assigned?',
    options: Category::pluck('name', 'id'),
    required: true,
);
```

<a name="multiselect-validation"></a>
#### バリデーション

選択肢を提示するが、その選択肢が選択されないようにする必要がある場合は、`validate`引数にクロージャを渡してください。

```
$permissions = select(
    label: 'What permissions should the user have?'
    options: [
        'read' => 'Read',
        'create' => 'Create',
        'update' => 'Update',
        'delete' => 'Delete'
    ],
    validate: fn (array $values) => ! in_array('read', $values)
        ? 'All users require the read permission.'
        : null
);
```

`options`引数が連想配列の場合、クロージャは選択されたキーを受け取ります。連想配列でない場合は選択された値を受け取ります。クロージャはエラーメッセージを返すか、バリデーションに成功した場合は`null`を返してください。

<a name="suggest"></a>
### 候補

`suggest`関数を使用すると、可能性のある選択肢を自動補完できます。ユーザーは自動補完のヒントに関係なく、任意の答えを入力することもできます。

```php
use function Laravel\Prompts\suggest;

$name = suggest('What is your name?', ['Taylor', 'Dayle']);
```

あるいは、`suggest`関数の第２引数にクロージャを渡すこともできます。クロージャはユーザーが入力文字をタイプするたびに呼び出されます。クロージャは文字列パラメータにこれまでのユーザー入力を受け取り、オートコンプリート用の選択肢配列を返す必要があります。

```php
$name = suggest(
    'What is your name?',
    fn ($value) => collect(['Taylor', 'Dayle'])
        ->filter(fn ($name) => Str::contains($name, $value, ignoreCase: true))
)
```

プレースホルダ・テキストとデフォルト値も指定できます。

```php
$name = suggest(
    label: 'What is your name?',
    options: ['Taylor', 'Dayle'],
    placeholder: 'E.g. Taylor',
    default: $user?->name
);
```

<a name="suggest-required"></a>
#### 必須値

入力値が必須の場合は、`required`引数を渡してください。

```php
$name = suggest(
    label: 'What is your name?',
    options: ['Taylor', 'Dayle'],
    required: true
);
```

バリデーション・メッセージをカスタマイズしたい場合は、文字列を渡すこともできます。

```php
$name = suggest(
    label: 'What is your name?',
    options: ['Taylor', 'Dayle'],
    required: 'Your name is required.'
);
```

<a name="suggest-validation"></a>
#### 追加のバリデーション

最後に、追加のバリデーションロジックを実行したい場合は、`validate`引数にクロージャを渡します。

```php
$name = suggest(
    label: 'What is your name?',
    options: ['Taylor', 'Dayle'],
    validate: fn (string $value) => match (true) {
        strlen($value) < 3 => 'The name must be at least 3 characters.',
        strlen($value) > 255 => 'The name must not exceed 255 characters.',
        default => null
    }
);
```

クロージャは入力された値を受け取り、エラーメッセージを返すか、バリデーションに合格した場合は、`null`を返します。

<a name="search"></a>
### 検索

ユーザーが選択できる選択肢がたくさんある場合、`search`機能を使えば、矢印キーを使って選択肢を選択する前に、検索クエリを入力して結果を絞り込むことができます。

```php
use function Laravel\Prompts\search;

$id = search(
    'Search for the user that should receive the mail',
    fn (string $value) => strlen($value) > 0
        ? User::where('name', 'like', "%{$value}%")->pluck('name', 'id')->all()
        : []
);
```

クロージャは、ユーザーがこれまでに入力したテキストを受け取り、 選択肢の配列を返さなければなりません。連想配列を返す場合は選択された選択肢のキーが返され、 そうでない場合はその値が代わりに返されます。

プレースホルダーのテキストを含めることもできます。

```php
$id = search(
    label: 'Search for the user that should receive the mail',
    placeholder: 'E.g. Taylor Otwell',
    options: fn (string $value) => strlen($value) > 0
        ? User::where('name', 'like', "%{$value}%")->pluck('name', 'id')->all()
        : []
);
```

５選択肢を超えると、選択肢がリスト表示されます。`scroll`引数を渡し、カスタマイズ可能です。

```php
$id = search(
    label: 'Search for the user that should receive the mail',
    options: fn (string $value) => strlen($value) > 0
        ? User::where('name', 'like', "%{$value}%")->pluck('name', 'id')->all()
        : [],
    scroll: 10
);
```

<a name="search-validation"></a>
#### バリデーション

追加のバリデーションロジックを実行したい場合は、`validate`引数にクロージャを渡します。

```php
$id = search(
    label: 'Search for the user that should receive the mail',
    options: fn (string $value) => strlen($value) > 0
        ? User::where('name', 'like', "%{$value}%")->pluck('name', 'id')->all()
        : []
    validate: function (int|string $value) {
        $user = User::findOrFail($value);

        if ($user->opted_out) {
            return 'This user has opted-out of receiving mail.';
        }
    }
);
```

`options`引数が連想配列の場合、クロージャは選択されたキーを受け取ります。連想配列でない場合は選択された値を受け取ります。クロージャはエラーメッセージを返すか、バリデーションに成功した場合は`null`を返してください。

<a name="terminal-considerations"></a>
### ターミナルの考察

<a name="terminal-width"></a>
#### ターミナルの横幅

ラベルやオプション、バリデーションメッセージの長さが、ユーザーの端末の「列」の文字数を超える場合、自動的に切り捨てます。ユーザーが狭い端末を使う可能性がある場合は、これらの文字列の長さを最小限にする検討をしてください。一般的に安全な最大長は、８０文字の端末をサポートする場合、７４文字です。

<a name="terminal-height"></a>
#### ターミナルの高さ

`scroll`引数を受け入れるプロンプトの場合、設定済み値は、検証メッセージ用のスペースを含めて、ユーザーの端末の高さに合わせて自動的に縮小されます。

<a name="fallbacks"></a>
### 未サポートの環境とフォールバック

Laravel PromptsはmacOS、Linux、WindowsのWSLをサポートしています。Windows版のPHPの制限により、現在のところWSL以外のWindowsでは、Laravel Promptsを使用できません。

このため、Laravel Promptsは[Symfony Console Question Helper](https://symfony.com/doc/current/components/console/helpers/questionhelper.html)のような代替実装へのフォールバックをサポートしています。

> **Note**
> LaravelフレームワークでLaravel Promptsを使用する場合、各プロンプトのフォールバックが設定済みで、未サポートの環境では自動的に有効になります。

<a name="fallback-conditions"></a>
#### フォールバックの条件

Laravelを使用していない場合や、フォールバック動作をカスタマイズする必要がある場合は、`Prompt`クラスの`fallbackWhen`へブール値を渡してください。

```php
use Laravel\Prompts\Prompt;

Prompt::fallbackWhen(
    ! $input->isInteractive() || windows_os() || app()->runningUnitTests()
);
```

<a name="fallback-behavior"></a>
#### フォールバックの振る舞い

Laravelを使用していない場合や、フォールバックの動作をカスタマイズする必要がある場合は、各プロンプトクラスの`fallbackUsing`静的メソッドへクロージャを渡してください。

```php
use Laravel\Prompts\TextPrompt;
use Symfony\Component\Console\Question\Question;
use Symfony\Component\Console\Style\SymfonyStyle;

TextPrompt::fallbackUsing(function (TextPrompt $prompt) use ($input, $output) {
    $question = (new Question($prompt->label, $prompt->default ?: null))
        ->setValidator(function ($answer) use ($prompt) {
            if ($prompt->required && $answer === null) {
                throw new \RuntimeException(is_string($prompt->required) ? $prompt->required : 'Required.');
            }

            if ($prompt->validate) {
                $error = ($prompt->validate)($answer ?? '');

                if ($error) {
                    throw new \RuntimeException($error);
                }
            }

            return $answer;
        });

    return (new SymfonyStyle($input, $output))
        ->askQuestion($question);
});
```

フォールバックは、プロンプトクラスごとに個別に設定する必要があります。クロージャはプロンプトクラスのインスタンスを受け取り、 プロンプトの適切な型を返す必要があります。
