Flintstone
==========

[![Total Downloads](https://img.shields.io/packagist/dm/fire015/flintstone.svg)](https://packagist.org/packages/fire015/flintstone)
[![Build Status](https://travis-ci.org/fire015/flintstone.svg?branch=master)](https://travis-ci.org/fire015/flintstone)

A key/value database store using flat files for PHP.

Features include:

* Memory efficient
* File locking
* Caching
* Gzip compression
* Easy to use

For full documentation please visit http://www.xeweb.net/flintstone/

### Installation

The easiest way to install Flintstone is via [composer](http://getcomposer.org/). Run the following command to install it.

```
composer require fire015/flintstone
```

```php
<?php
require 'vendor/autoload.php';

use Flintstone\Flintstone;

$users = new Flintstone('users', ['dir' => '/path/to/database/dir/']);
```

### Requirements

- PHP 5.6+

### Data types

Flintstone can store the following data types:

* Strings
* Integers
* Floats
* Arrays
* Null

### Options

|Name				|Type		|Default Value	|Description														|
|---				|---		|---				|---														|
|dir				|string				|the current working directory			|The directory where the database files are stored (this should be somewhere that is not web accessible) e.g. /path/to/database/			|
|ext				|string				|.dat		|The database file extension to use							|
|gzip				|boolean			|false		|Use gzip to compress the database							|
|cache				|boolean or object	|true		|Whether to cache `get()` results for faster data retrieval								|
|formatter			|null or object		|null		|The formatter class used to encode/decode data				|
|swap_memory_limit	|integer			|2097152	|The amount of memory to use before writing to a temporary file	|


### Usage examples

```php
<?php

// Set options
$options = ['dir' => '/path/to/database/dir/'];

// Load the databases
$users = new Flintstone('users', $options);
$settings = new Flintstone('settings', $options);

// Set keys
$users->set('bob', ['email' => 'bob@site.com', 'password' => '123456']);
$users->set('joe', ['email' => 'joe@site.com', 'password' => 'test']);
$settings->set('site_offline', 1);
$settings->set('site_back', '3 days');

// Retrieve keys
$user = $users->get('bob');
echo 'Bob, your email is ' . $user['email'];

$offline = $settings->get('site_offline');
if ($offline == 1) {
    echo 'Sorry, the website is offline<br />';
    echo 'We will be back in ' . $settings->get('site_back');
}

// Retrieve all key names
$keys = $users->getKeys(); // returns array('bob', 'joe', ...)

foreach ($keys as $username) {
    $user = $users->get($username);
    echo $username.', your email is ' . $user['email'];
    echo $username.', your password is ' . $user['password'];
}

// Delete a key
$users->delete('joe');

// Flush the database
$users->flush();
```

### Changing the formatter
By default Flintstone will encode/decode data using PHP's serialize functions, however you can override this with your own class if you prefer.

Just make sure it implements `Flintstone\Formatter\FormatterInterface` and then you can provide it as the `formatter` option.

If you wish to use JSON as the formatter, Flintstone already ships with this as per the example below:

```php
<?php
require 'vendor/autoload.php';

use Flintstone\Flintstone;
use Flintstone\Formatter\JsonFormatter;

$users = new Flintstone('users', [
    'dir' => __DIR__,
    'formatter' => new JsonFormatter()
]);
```

### Changing the cache
To speed up data retrieval Flintstone can store the results of `get()` in a cache store. By default this uses a simple array that only persist's for as long as the `Flintstone` object exists.

If you want to use your own cache store (such as Memcached) you can pass a class as the `cache` option. Just make sure it implements `Flintstone\Cache\CacheInterface`.

### Who is using Flintstone?

- [Key-Value Store](https://github.com/adammbalogh/key-value-store)
