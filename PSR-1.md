Padrão básico de codificação
=====================

Esta seção do padrão compreende o que deve ser considerado dos elementos padrões de codificação que são necessários para um alto nível de interoperabilidade técnica entre código PHP compartilhado.

[PSR-0]: https://github.com/enricopereira/PSR_PT-BR/blob/master/PSR-0.md


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
// efeito secundário: mudança nas configurações ini
ini_set('error_reporting', E_ALL);

// efeito secundário: carregamento de arquivo
include "file.php";

// efeito secundário: geração de output
echo "<html>\n";

// declaração
function foo()
{
    // corpo da função
}
```

A seguir está um exemplo de um arquivo, com declarações sem efeitos secundários; um exemplo do que deve ser feito:

```php
<?php
// declaração
function foo()
{
    // corpo da função
}

// declaração condicional não é um efeito secundário
if (! function_exists('bar')) {
    function bar()
    {
        // corpo da função
    }
}
```


3. Namespace e nomes de classes
----------------------------

Namespaces e classes devem seguir a [PSR-0][].

Isto significa que cada classe está em um arquivo, por si mesmo, e é em um espaço de pelo menos um nível: um nível superior com o nome do fornecedor.

Classes devem ser declaradas em `StudlyCaps`.

Código escrito para PHP 5.3 ou superior deve utilizar namespaces reais.

Por exemplo:

```php
<?php
// PHP 5.3 e superior:
namespace Vendor\Model;

class Foo
{
}
```

Código escrito para PHP 5.2.x ou inferior deve utilizar a convenção pseudo-namespace do `Vendor_` prefixando nos nomes das classes.

```php
<?php
// PHP 5.2.x e inferior:
class Vendor_Model_Foo
{
}
```

4. Constantes de classes, propriedades e métodos
-------------------------------------------

O termo "classes" se refere a todas as classes, interfaces e traits.

### 4.1. Constantes

Constantes de classes devem ser declaradas em letra maiúscula separado por underlines.
Por exemplo:

```php
<?php
namespace Vendor\Model;

class Foo
{
    const VERSION = '1.0';
    const DATE_APPROVED = '2012-06-01';
}
```

### 4.2. Propriedades

Este guia intencionalmente evita qualquer recomendação sobre o uso de
`$StudlyCaps`, `$camelCase`, ou `$under_score` em nomes de propriedades.

Seja qual for convenção de nomenclatura usada, ela deve ser aplicada de forma consistente dentro de um
escopo razoável. Esse escopo pode ser a nível de fornecedor, pacote, classe ou método.

### 4.3. Métodos

Métodos devem ser declarados em `camelCase()`.
