# php-kafka-consumer

An Apache Kafka consumer in PHP


## Install

1. Install [librdkafka c library](https://github.com/edenhill/librdkafka)

    ```bash
    $ cd /tmp
    $ mkdir librdkafka
    $ cd librdkafka
    $ curl -L https://github.com/edenhill/librdkafka/archive/v1.0.0.tar.gz | tar xz
    $ cd librdkafka-1.0.0
    $ ./configure
    $ make
    $ make install
    ```

2. Install the [php-rdkafka](https://github.com/arnaud-lb/php-rdkafka) PECL extension

    ```bash
    $ pecl install rdkafka
    ```

3. Add the following to your php.ini file to enable the php-rdkafka extension

    `extension=rdkafka.so`

4. Install this package via composer using:

    `composer require arquivei/php-kafka-consumer`

## Usage

```php
<?php

require_once 'vendor/autoload.php';

use Kafka\Consumer\Entities\Config;
use Kafka\Consumer\Contracts\Consumer;
use Kafka\Consumer\Entities\Config\Sasl;
use Kafka\Consumer\Entities\Config\MaxAttempt;

class DefaultConsumer extends Consumer
{
    public function handle(string $message): void
    {
        print 'Init: ' . date('Y-m-d H:i:s') . PHP_EOL;
        sleep(2);
        print 'Finish: ' . date('Y-m-d H:i:s') . PHP_EOL;
    }
}

$config = new Config(
    new Sasl('username', 'pasword', 'mechanisms'),
    'topic',
    'broker:port',
    1,
    'php-kafka-consumer-group-id',
    new DefaultConsumer(),
    new MaxAttempt(1),
    'security-protocol'
);

(new \Kafka\Consumer\Consumer($config))->consume();

```

## Usage with Laravel

You need to add the `php-kafka-consig.php` in `config` path:

```php
<?php

return [
    'topic' => 'topic',
    'broker' => 'broker',
    'groupId' => 'group-id',
    'securityProtocol' => 'security-protocol',
    'sasl' => [
        'mechanisms' => 'mechanisms',
        'username' => 'username',
        'password' => 'password',
    ],
];

```

Use the command to execute the consumer:

```bash
$ php artisan arquivei:php-kafka-consumer --consumer="App\Consumers\YourConsumer" --commit=1
```

## Build and test

If you want to contribute, there are a few utilities that will help.

First create a container:

`docker-composer up -d --build`

If you have make, you can use pre defined commands in the Makefile

`make build`

Then install the dependencies:

`docker-compose exec php-fpm composer install`

or with make:

`make composer install`

You can run tests locally:

`docker-compose exec php-fpm ./vendor/phpunit/phpunit/phpunit tests`

or with make:

`make test`

and check for coverage:

`docker-compose exec php-fpm phpdbg -qrr ./vendor/bin/phpunit --whitelist src/ --coverage-html coverage/`

or with make:

`make coverage`