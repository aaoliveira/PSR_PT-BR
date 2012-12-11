A seguir, o manual com os requisitos obrigatórios que devem ser seguidos para a interoperabilidade de autocarregamento (autoloading).

Obrigatório
---------

* Uma namespace totalmente qualificada e as classes devem seguir a seguinte estrutura `\<Nome do Fabricante>\(<Namespace>\)*<Nome da Classe>`
* Cada namespace deve ter uma namespace de nível superior ("Nome do fabricante").
* Cada namespace pode ter quantos sub-namespaces que quiser.
* Cada separador de namespace deve ser convertido para `DIRECTORY_SEPARATOR` durante o carregamento do sistema de arquivos.
* Cada caractere `_` no nome da classe é convertido para
  `DIRECTORY_SEPARATOR`. O caractere `_`não tem nenhum significado especial na namespace.
* A namespace totalmente qualificada e a classe é sufixada com `.php` durante o carregamento do sistema de arquivos.
* Caracteres alfabéticos em nomes de fabricante, em namespaces e em classes podem ser qualquer combinação de letras maiúsculas e minúsculas.

Exemplos
--------

* `\Doctrine\Common\IsolatedClassLoader` => `/caminho/parao/projeto/lib/vendor/Doctrine/Common/IsolatedClassLoader.php`
* `\Symfony\Core\Request` => `/caminho/parao/projeto/lib/vendor/Symfony/Core/Request.php`
* `\Zend\Acl` => `/caminho/parao/projeto/lib/vendor/Zend/Acl.php`
* `\Zend\Mail\Message` => `/caminho/parao/projeto/lib/vendor/Zend/Mail/Message.php`

Underlines em namespaces e em nomes de classes
-----------------------------------------

* `\namespace\package\Class_Name` => `/caminho/parao/projeto/lib/vendor/namespace/package/Class/Name.php`
* `\namespace\package_name\Class_Name` => `/caminho/parao/projeto/lib/vendor/namespace/package_name/Class/Name.php`

Os padrões que definimos aqui devem ser o menor denominador comum para
uma indolor interoperabilidade de autocarregamento. Você pode testar se está seguindo esses padrões, utilizando esta amostra da implementação da SplClassLoader que é capaz de carregar classes do PHP 5.3.

Exemplo de implementação
----------------------

Abaixo está um exemplo de função para simplesmente demonstrar como os padrões propostos acima são autocarregados.
```php
<?php

function autoload($className)
{
    $className = ltrim($className, '\\');
    $fileName  = '';
    $namespace = '';
    if ($lastNsPos = strrpos($className, '\\')) {
        $namespace = substr($className, 0, $lastNsPos);
        $className = substr($className, $lastNsPos + 1);
        $fileName  = str_replace('\\', DIRECTORY_SEPARATOR, $namespace) . DIRECTORY_SEPARATOR;
    }
    $fileName .= str_replace('_', DIRECTORY_SEPARATOR, $className) . '.php';

    require $fileName;
}
```

Implementação da SplClassLoader
-----------------------------

O seguinte gist é uma implementação da SplClassLoader que carrega suas classes se você seguir as propostas de interoperabilidade de autocarregamento citadas acima. Atualmente, é a forma recomendada para carregar classes de PHP 5.3 que seguem estes padrões.

* [http://gist.github.com/221634](http://gist.github.com/221634)

