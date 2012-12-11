Padrão básico de codificação
=====================

Esta seção do padrão compreende o que deve ser considerado dos elementos padrões de codificação que são necessários para um alto nível de interoperabilidade técnica entre código PHP compartilhado.

[PSR-0]: https://github.com/php-fig/fig-standards/blob/master/accepted/PSR-0.md


1. Visão geral
-----------

- Arquivos devem usar apenas as tags `<?php` e `<?=`.

- Arquivos devem usar apenas UTF-8 sem BOM para código PHP.

- Arquvos devem declarar símbolos (classes, funções, contantes, etc.) ou causar outros efeitos (ex: gerar output, alterar configurações .ini, etc.), mas não devem fazer as duas coisas.

- Namespaces e classes devem seguir a [PSR-0][].

- Nomes das classes devem ser declarados em `StudlyCaps`.

- Constantes de classes devem ser declaradas em letra maiúscula separado por underlines.

- Nomes de métodos devem ser declarados em `camelCase`.


2. Arquivos
--------

### 2.1. Tags PHP

PHP code MUST use the long `<?php ?>` tags or the short-echo `<?= ?>` tags; it
MUST NOT use the other tag variations.

### 2.2. Codificação de caracteres

PHP code MUST use only UTF-8 without BOM.

### 2.3. Efeitos secundários

A file SHOULD declare new symbols (classes, functions, constants,
etc.) and cause no other side effects, or it SHOULD execute logic with side
effects, but SHOULD NOT do both.

The phrase "side effects" means execution of logic not directly related to
declaring classes, functions, constants, etc., *merely from including the
file*.

"Side effects" include but are not limited to: generating output, explicit
use of `require` or `include`, connecting to external services, modifying ini
settings, emitting errors or exceptions, modifying global or static variables,
reading from or writing to a file, and so on.

The following is an example of a file with both declarations and side effects;
i.e, an example of what to avoid:

```php
<?php
// side effect: change ini settings
ini_set('error_reporting', E_ALL);

// side effect: loads a file
include "file.php";

// side effect: generates output
echo "<html>\n";

// declaration
function foo()
{
    // function body
}
```

The following example is of a file that contains declarations without side
effects; i.e., an example of what to emulate:

```php
<?php
// declaration
function foo()
{
    // function body
}

// conditional declaration is *not* a side effect
if (! function_exists('bar')) {
    function bar()
    {
        // function body
    }
}
```


3. Namespace e nomes de classes
----------------------------

Namespaces and classes MUST follow [PSR-0][].

This means each class is in a file by itself, and is in a namespace of at
least one level: a top-level vendor name.

Class names MUST be declared in `StudlyCaps`.

Code written for PHP 5.3 and after MUST use formal namespaces.

For example:

```php
<?php
// PHP 5.3 and later:
namespace Vendor\Model;

class Foo
{
}
```

Code written for 5.2.x and before SHOULD use the pseudo-namespacing convention
of `Vendor_` prefixes on class names.

```php
<?php
// PHP 5.2.x and earlier:
class Vendor_Model_Foo
{
}
```

4. Constantes de classes, propriedades e métodos
-------------------------------------------

The term "class" refers to all classes, interfaces, and traits.

### 4.1. Constantes

Class constants MUST be declared in all upper case with underscore separators.
For example:

```php
<?php
namespace Vendor\Model;

class Foo
{
    const VERSION = '1.0';
    const DATE_APPROVED = '2012-06-01';
}
```

### 4.2. propriedades

This guide intentionally avoids any recommendation regarding the use of
`$StudlyCaps`, `$camelCase`, or `$under_score` property names.

Whatever naming convention is used SHOULD be applied consistently within a
reasonable scope. That scope may be vendor-level, package-level, class-level,
or method-level.

### 4.3. Métodos

Method names MUST be declared in `camelCase()`.
