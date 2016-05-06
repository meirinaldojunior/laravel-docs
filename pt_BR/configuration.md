# Configuração



- [Introdução](#introduction)

- [Acessando os valores de configuração](#accessing-configuration-values)

- [Configurações por ambiente](#environment-configuration)

    - [Determinando o ambiente atual](#determining-the-current-environment)

- [Cache das configurações](#configuration-caching)

- [Modo de manutenção](#maintenance-mode)



<a name="introduction"></a>

## Introdução

Todos os arquivos de configuração do framework Laravel estão localizados na pasta `config`. Cada opção está documentada, sinta-se livre para ler os arquivos e se familiarizar com as opções disponíveis.


<a name="accessing-configuration-values"></a>

## Acessando os Valores de Configurações

Você pode facilmente acessar os valores das configurações usando o helper global `config` de qualquer lugar em sua aplicação. Os valores das configuração podem ser acessados usando a sintaxe de "ponto", o qual inclui o nome do arquivo e a opção que deseja acessar. Um valor padrão também pode ser especificado e será retornado se a opção da configuração não existir:


    $value = config('app.timezone');

Para definir os valores de configuração em tempo de execução, passe um vetor com os valores para o helper `config`:


    config(['app.timezone' => 'America/Chicago']);



<a name="environment-configuration"></a>

## Configuração por Ambiente



It is often helpful to have different configuration values based on the environment the application is running in. For example, you may wish to use a different cache driver locally than you do on your production server. It's easy using environment based configuration.

Para facilitar, o Laravel utiliza a biblioteca [DotEnv](https://github.com/vlucas/phpdotenv) do PHP, desenvolvida por Vance Lucas. Em uma instalação nova do Laravel, o diretório root de sua aplicação conterá um arquivo `.env.example`. Se você instalar o Laravel via Composer, este arquivo será automaticamente renomeado para `.env`. Caso contrário, você deverá renomear o arquivo manualmente.

Quando sua aplicação receber uma requisição, todas as variáveis do arquivo serão carregadas dentro da variável super-global `$_ENV`. Se isso não acontecer, você pode usar o helper `env` para pegar os valores destas variáveis em seu arquivo de configuração. De fato, se você rever os arquivos de configuração do Laravel, você notará que todas as opções usam o este helper:



    'debug' => env('APP_DEBUG', false),


O segundo valor passado para a função `env` é o "valor padrão". Este valor será usado se a variável de ambiente não existir para a chave passada no primeiro parâmetro.

Você não deveria enviar o arquivo `.env` para o servidor de código fonte. Cada desenvolvedor / servidor, possui seu próprio arquivo `.env`, e cada um deles com seus valores.

Se você está desenvolvendo em um time, você pode incluir o arquivo `.env.example` com sua aplicação. Colocando os valores de exemplo em seu arquivo de configuração, outros desenvolvedores do seu time podem facilmente ver quais variaveis são necessárias para executar sua aplicação.


<a name="determining-the-current-environment"></a>

### Determinando o Ambiente Atual

O ambiente atual da aplicação é determinado pela variável `APP_ENV` em seu arquivo `.env`. Você pode acessar este valor pelo método `environment` em sua [fachada](/docs/{{version}}/facades) `App`:


    $environment = App::environment();

Você também pode passar parâmetros para o método `environment` para verificar se o ambiente confere com o valor passado. Se necessário, você também pode passar vários valores para o método `environment`. Se o ambiente conferir com qualquer valor passado, o método retornará `true`:



    if (App::environment('local')) {

        // The environment is local

    }



    if (App::environment('local', 'staging')) {

        // The environment is either local OR staging...

    }


Uma instancia da aplicação pode também ser acessada pelo método do helper `app`:



    $environment = app()->environment();



<a name="configuration-caching"></a>

## Cache de Configurações

Para aumentar a velocidade de sua aplicação, você pode fazer um cache de todos os arquivos de configurações em uma único arquivo usando o comando Artisan `config:cache`. Este comando combinará todas as opções de configurações de sua aplicação em um único arquivo que será carregado mais rapidamente pelo framework.

Você deve executar o comando `php artisan config:cache` como parte de sua rotina de deploy da aplicação em ambiente de produção. O comando não deve ser executado em desenvolvimento onde as opções de configuração são frequentemente alteradas.


<a name="maintenance-mode"></a>

## Modo de Manutenção

Quando sua aplicação está em modo de manutenção, uma view personalizada será exibida para todas as requisições de sua aplicação. Isso é útils para "desativar" sua aplicação enquanto você faz um update ou alguma rotina de manutenção. Uma verificação de modo de manutenção é incluída no middleware padrão para sua aplicação. Se a aplicação estiver em modo de manutenção, uma `MaintenanceModeException` será lançada com o código de status 503.

Para ativar o modo de manutenção, execute o comando Artisan `down`:


    php artisan down

Você pode fornecer as opções `message` e `retry` para o comando `down`. O valor `message` é usado para exibir uma mensagem personalizada, enquanto o valor `retry` será definido no cabeçalho HTTP `Retry-After`:



    php artisan down --message='Upgrading Database' --retry=60


Para desativar o modo de manutenção, use o comando `up`:



    php artisan up



#### Template de Resposta para o Modo de Manutenção

O template padrão de resposta para o modo de manutenção está localizado em `resources/views/errors/503.blade.php`. Você pode modificá-lo como desejar para sua aplicação.



#### Modo de Manutenção e Filas

Enquanto sua aplicação estiver em modo de manutenção, nenhum [job](/docs/{{version}}/queues) será lançado. Os `jobs` continuarão normalmente assim que a aplicação sair do modo de manutenção.



#### Alternativas ao Modo de Manutenção


Alterar para o modo de manutenção requer alguns segundos para finalizar o processo. Você pode considerar alternativas como [Envoyer](https://envoyer.io) para zerar este tempo de deploy com o Laravel.

