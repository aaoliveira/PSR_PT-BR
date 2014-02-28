Logger Interface
================

Este documento descreve uma interface comum para bibliotecas de log.

O objetivo principal é permitir bibliotecas para receber um objeto `Psr\Log\LoggerInterface` e escrever logs para ele em uma forma simples e universal. Frameworks e CMSs que tem necessidades próprias podem estender a interface para sua própria proposta, mas deve manter a compatibilidade com este documetno. Isto assegura que as bibliotecas de terceiros que uma aplicação usa pode escrever para os logs centralizados da aplicação.

A palavra `implementadir` neste documento é para ser interpretada como alguém que implementa a `LoggerInterface` em uma biblioteca ou framework relacionado a log.

1. Especificação
-----------------

### 1.1 Básico

- A `LoggerInterface` expõe oito métodos par escrever logs para oito níveis (debug, info, notice, warning, error, critical, alert,
  emergency) como descrito na [RFC 5424][].

- Um nono método (`log`), aceita um nível de log como primeiro argumento. Chamando este método com uma das constantes dos níveis de log deve ter o mesmo resultado como chamando o método específico do nível. Chamando este método com um nível não definido por esta especificação deve lançar uma `Psr\Log\InvalidArgumentException` se a implementação não conhece o nível. Usuários não devem usar um nível customizado sem saber ao certo se a implementação atual suporta ele.

[RFC 5424]: http://tools.ietf.org/html/rfc5424

### 1.2 Mensagem

- Todo método aceita uma string como a mensagem, ou um objeto com um método `__toString()`. Implementadores podem ter um tratamento especial para os objetos passados. Se esse não foro caso, implementadores devem moldar ele para uma string.

- A mensagem pode conter placeholders que implementadores podem substituir com valores do array de contexto.

  Nomes de placeholders devem corresponder para chaves no array de contexto.

  Nomes  de placeholders devem ser delimitados com uma única abertura de chaves `{` e um único fechamento de chaves `}`. Não deve ter nenhum espaço em branco entre o delimitador e o nome do placeholder.

  Nomes de placeholders devem ser compostos apenas de caracteres `A-Z`, `a-z`,
  `0-9`, underscore `_`, e ponto `.`. O uso de outros caracteres é resevado para futuras modificações da especificação dos placeholders.

  Implementadores podem usar placeholders para implementar várias estratégias de escapamento e traduzir logs para exibir. Usuários não devem pré-escapar os valores do placeholder desde que eles não podem saber em qual contexto de dado serão exibidos.

  O seguinte é um exemplo de uma implementação de inserção de placeholder fornecida para propósitos de referência apenas.

  ```php
  /**
   * Insere contexto de valores dentro dos placeholders na mensagem.
   */
  function interpolate($message, array $context = array())
  {
      // cria uma substituição de array com chaves em torno das chaves de contexto
      $replace = array();
      foreach ($context as $key => $val) {
          $replace['{' . $key . '}'] = $val;
      }

      // insere uma substituição de valores dentro da mensagem e retorna
      return strtr($message, $replace);
  }

  // uma mensagem com delimitação de chaves nos nomes dos placeholders
  $message = "User {username} created";

  // um contexto de array dos nomes de placeholders => valores de substituição
  // a context array of placeholder names => va
  $context = array('username' => 'bolivar');

  // imprime "Username bolivar created"
  echo interpolate($message, $context);
  ```

### 1.3 Contexto

- Cada método aceita um array como dados de contexto. Isto se destina a manter qualquer informação irrelevante que não se encaixam bem em uma string. O array pode conter qualquer coisa. Implementadores devem garantir que eles tratem dados de contexto. Um valor dado no contexto não deve lançar uma exception nem qualquer erro php, waring ou notice.

- Se um objeto `Exception` for passado em um dado de contexto, ele deve ser na palavra-chave `'exception'`. Exceções de log é um padrão comum e isto permite implementadores extrairem um stack trace da exceção quando o log backend suporte ele. Implementadores devem ainda verificar que a palavra-chave `'exception'` é realmente uma `Exception` antes de usá-la como tal, pois ela pode conter qualquer coisa.

### 1.4 Classes helper e interfaces

- A classe `Psr\Log\AbstractLogger` permite você implementar a `LoggerInterface` muito facilmente estendendo ela e implementando o método genérico `log`. Os outros oito métodos são o encaminhamento da mensagem e contexto para isso.

- Similarmente, usando a `Psr\Log\LoggerTrait` apenas requer que você implemente o método genérico `log`. Note que uma vez que traits não podem implementar interfaces, neste caso você ainda tem que implementar `LoggerInterface`.

- O `Psr\Log\NullLogger` é fornecido junto com a interface. Ela pode ser usado por usuários da interface para fornecer uma implementação fall-back "black hole" se nenhum logger lhes é dado. No entanto log condicional
pode ser uma melhor abordagem, se a criação de dados de contexto é custoso.

- O `Psr\Log\LoggerAwareInterface` apenas contém um método `setLogger(LoggerInterface $logger)` e pode ser usada pelos frameworks para criar instâncias automáticas com um logger.

- A trait `Psr\Log\LoggerAwareTrait` pode ser usada para implementar a interface equivalente facilmente em qualquer classe. Ela dá acesso para `$this->logger`.

- A classe `Psr\Log\LogLevel` mantém constantes para os oito níveis de log.

2. Pacote
----------

As interfaces e classes descritas bem como as classes de exceptions e uma suíte de testes para verificar se sua implementação é fornecida como parte do pacote [psr/log](https://packagist.org/packages/psr/log).

3. `Psr\Log\LoggerInterface`
----------------------------

```php
<?php

namespace Psr\Log;

/**
 * Descreve a instância do logger
 *
 * A mensagem deve ser uma string ou um objeto implementando __toString().
 *
 * A mensagem pode conter placeholders na forma: {foo} onde foo será substituido pelo dado de contexto na chave "foo".
 *
 * O array de contexto pode conter dados específicos, a única hipótese que
 * pode ser feita pelos implementadores é que se uma instância de Exception é dada
 * para produzir um stack trace, ele deve estar em uma chave chamada "exceção".
 *
 * Veja https://github.com/php-fig/fig-standards/blob/master/accepted/PSR-3-logger-interface.md
 * para a especificação completa da interface
 */
interface LoggerInterface
{
    /**
     * O sistema é inutilizável.
     *
     * @param string $message
     * @param array $context
     * @return null
     */
    public function emergency($message, array $context = array());

    /**
     *
     * Ação deve ser tomada imediatamente.
     *
     * Exemplo: website fora do ar, banco de dados inacessível, etc. Isto deveria
     * adcionar os alertas de SMS e acordar você.
     *
     * @param string $message
     * @param array $context
     * @return null
     */
    public function alert($message, array $context = array());

    /**
     * Condições críticas.
     *
     * Exemplo: componente da aplicação não disponível, exception inesperada.
     *
     * @param string $message
     * @param array $context
     * @return null
     */
    public function critical($message, array $context = array());

    /**
     * Erros em tempo de execução que Não requer ação imediara mas deveria tipicamente
     * ser gravada em log e monitorada.
     *
     * @param string $message
     * @param array $context
     * @return null
     */
    public function error($message, array $context = array());

    /**
     * Ocorrências excepcionais que não são erros.
     *
     * Exemplo: uso de APIs obsoletas, mau uso de uma API, coisas indesejáveis que não são necessariamente erradas.
     *
     * @param string $message
     * @param array $context
     * @return null
     */
    public function warning($message, array $context = array());

    /**
     * Eventos normais mas significantes.
     *
     * @param string $message
     * @param array $context
     * @return null
     */
    public function notice($message, array $context = array());

    /**
     * Eventos interessantes.
     *
     * Exemplo: Logins de usuários, logs SQL.
     *
     * @param string $message
     * @param array $context
     * @return null
     */
    public function info($message, array $context = array());

    /**
     * Informação de debug detalhada.
     *
     * @param string $message
     * @param array $context
     * @return null
     */
    public function debug($message, array $context = array());

    /**
     * Logs com um nível específico.
     *
     * @param mixed $level
     * @param string $message
     * @param array $context
     * @return null
     */
    public function log($level, $message, array $context = array());
}
```

4. `Psr\Log\LoggerAwareInterface`
---------------------------------

```php
<?php

namespace Psr\Log;

/**
 * Descreve uma instância da logger-aware
 */
interface LoggerAwareInterface
{
    /**
     * Define uma instância do logger no objeto
     *
     * @param LoggerInterface $logger
     * @return null
     */
    public function setLogger(LoggerInterface $logger);
}
```

5. `Psr\Log\LogLevel`
---------------------

```php
<?php

namespace Psr\Log;

/**
 * Descreve os níveis de log
 */
class LogLevel
{
    const EMERGENCY = 'emergency';
    const ALERT     = 'alert';
    const CRITICAL  = 'critical';
    const ERROR     = 'error';
    const WARNING   = 'warning';
    const NOTICE    = 'notice';
    const INFO      = 'info';
    const DEBUG     = 'debug';
}
```