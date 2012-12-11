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

Código PHP deve usar as tags longas `<?php ?>` ou a short-tags para echo `<?= ?>`; não deve se utilizar outras tags.

### 2.2. Codificação de caracteres

Código PHP deve usar apenas UTF-8 sem BOM.

### 2.3. Efeitos secundários


Um arquivo deve declarar novos símbolos (classes, funções, contantes, etc.) e não causar outros efeitos, ou ele deve executar lógica com outros efeitos, mas não deve fazer ambos.

A expressão "efeitos secundários" significa a execução da lógica não diretamente ligada com
declaração de classes, funções, constantes, etc, apenas de incluir o arquivo.

"Efeitos secundários" incluem, mas não estão limitados a: A geração do output, uso explícito de `require` ou `include`, conexão a serviços externos, modificação de configurações ini, emissão erros ou exceções, modificação das variáveis ​​globais ou estáticas,
ler ou escrever em um arquivo, e assim por diante.

A seguir está um exemplo de um arquivo, com as declarações e efeitos secundários;
um exemplo de que deve ser evitado:

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

A seguir está um exemplo de um arquivo, com declarações sem efeitos secundários; um exemplo do que deve ser feito:

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

Namespaces e classes devem seguir a [PSR-0][].

Isto significa que cada classe está em um arquivo, por si mesmo, e é em um espaço de pelo menos um nível: um nível superior com o nome do fornecedor.

Classes devem ser declaradas em `StudlyCaps`.

Código escrito para PHP 5.3 ou superior deve utilizar namespaces reais.

For example:

```php
<?php
// PHP 5.3 and later:
namespace Vendor\Model;

class Foo
{
}
```

Código escrito para PHP 5.2.x ou inferior deve utilizar a convenção pseudo-namespace do `Vendor_` prefixando nos nomes das classes.

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
