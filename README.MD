Laravel Celcat Web API Integration
===============

[![Scrutinizer Code Quality](https://scrutinizer-ci.com/g/neilherbertuk/celcatwebapi/badges/quality-score.png?b=master)](https://scrutinizer-ci.com/g/neilherbertuk/celcatwebapi/?branch=master) [![Build Status](https://scrutinizer-ci.com/g/neilherbertuk/celcatwebapi/badges/build.png?b=master)](https://scrutinizer-ci.com/g/neilherbertuk/celcatwebapi/build-status/master)

This package provides access to the Celcat Web API as documented on the [Celcat Wiki - Web API](https://www.celcat.wiki/en/guides/developers_guides/webapi/webapi) page. This package has only been tested with Celcat 7 where the WebAPI is in beta. 

## Installation

Require the package using composer

```bash
$ composer require neilherbertuk/celcatwebapi:dev-master
```

If you are using `Laravel 5.5+` package auto-discovery should register the ```CelcatWebAPIServiceProvider::class``` and ```CelcatWebAPI``` facade for you.

If you are using `Laravel 5.4` or lower you will need to register the service provider and facade within your ```config\app.php``` file

### Service Provider
Add the following to your ```providers``` list within ```config\app.php``` 
```php
    neilherbertuk\celcatwebapi\CelcatWebAPIServiceProvider::class,
```

### Facade
Add the following to your ```aliases``` list within ```config\app.php```
```php
    'CelcatWebAPI' => neilherbertuk\celcatwebapi\Facades\CelcatWebAPI::class,
```

### Config File
If you would like to edit the config file you will need to publish it. All configuration items can be set using the ```.env``` file.
```bash
    $php artisan vendor:publish
``` 

## Configuration

Configuration can be set within your ```config\celcat.php``` file, or via your ```.env``` file.

The following must be provided in order to talk to the Celcat Web API

`ServerAddress` - Celcat Web API's Address, this must be https and should not include a trailing slash (/)

```dotenv
    CELCAT_WEB_API_SERVER_ADDRESS=https://localhost:5000/api
```

`APICode` - The API Code generated and put into your ```appsettings.production.json``` file as described on the [Celcat Wiki - Web API - Security](https://www.celcat.wiki/en/guides/developers_guides/webapi/security) page.

```dotenv    
    CELCAT_WEB_API_APICODE=123ABCDEfGHijKL4
```

`VerifySSL` - Whether to verify the Server SSL Certificate or not

```dotenv
    CELCAT_WEB_API_VERIFY_SSL=true
```

`PEM` - Location of the Certificate PEM if using self-signed certificates on the Celcat Web API server.

```dotenv
    CELCAT_WEB_API_PEM=storage/CelcatWebAPI/cert.pem
```

`Debug` - Enabled detailed logging
```dotenv
    CELCAT_WEB_API_DEBUG=false
``` 

`Proxy` - Proxy Server Address if required
```dotenv
   -- Used for both http and https --
    CELCAT_WEB_API_PROXY=http://localproxy:8080
    -- or --
    CELCAT_WEB_API_PROXY_HTTP=http://localproxy:8080
    CELCAT_WEB_API_PROXY_HTTPS=https://localproxy:8080
    
    -- Bypass proxy for addresses seperated by spaces --
    CELCAT_WEB_API_PROXY_BYPASS=*.domain.com
```

## Usage

All of the functionality of this package can be accessed through the ```CelcatWebAPI``` Laravel Facade or using ```app()->make('CelcatWebAPI')```. For the sake of uniformity and ease, all usage examples will access the package via the Laravel Facade.

### Available Resources

The following api resources have been implemented

| Resource Name |
| --- |
| `Rooms` |
| `Groups` |
| `Students` |
| `StudentMembership` |

You can use any of the below methods on any of the available resources listed above

#### Get All

Using the getAll() method on a resource will return all results.

```php
    CelcatWebAPI::resource()->getAll();
```

##### GetAll Optional: pageSize - number of entries to get per request
By default, getAll() will use a pageSize of 1,000 and perform as many requests as needed to get all results. You can change the pageSize to increase or reduce the number of requests made by passing an integer into the `getAll()` operator.

Example

The following will request 100 results per api call until all results have been received.
```php
CelcatWebAPI::resource()->getAll(100);
```

#### Get

Using the get() method on a resource will by default return the first 50 results.

Example
```php
CelcatWebAPI::resource()->get();
```

Result
```
array:3 [▼
  "pagination" => array:3 [▼
    "TotalRows" => 200
    "TotalPages" => 4
    "CurrentPage" => 0
  ]
  "data" => array:1 [▼
    0 => array:2 [▼
      "id" => 0
      "name" => "Some Name"
    ]
    1 => array:2 [▼
      "id" => 1
      "name" => "Some Other Name"
    ]
    2 => array:2[...]
    3 => array:2[...]
    ...
    49 => array:2[...]
       
  ]
]
```

##### Get Optional: pageSize - number of results to return
You can change the number of results returned by including an integer within the method signature.

Example

The following will return the first 100 results.
```php
CelcatWebAPI::resource()->get(100);
```

Result
```
array:3 [▼
  "pagination" => array:3 [▼
    "TotalRows" => 200
    "TotalPages" => 2
    "CurrentPage" => 0
  ]
  "parameters" => array:2 [▼
    "pageSize" => 100
    "page" => 0
  ]
  "data" => array:1 [▼
    0 => array:2 [▼
      "id" => 0
      "name" => "Some Name"
    ]
    1 => array:2 [▼
      "id" => 1
      "name" => "Some Other Name"
    ]
    2 => array:2[...]
    3 => array:2[...]
    ...
    99 => array:2[...]
       
  ]
]
```

##### Get Optional: page - results page to return
If the total number of results are higher than that returned, the results become paginated. You can request the page by using the `Where()` operator.

Example

The following will return page 2 or results 51 - 100
```php
CelcatWebAPI::resource()->where(['page' => 2])->get();
```

Result
```
array:3 [▼
  "pagination" => array:3 [▼
    "TotalRows" => 200
    "TotalPages" => 4
    "CurrentPage" => 2
  ]
  "parameters" => array:2 [▼
    "page" => 2
  ]
  "data" => array:1 [▼
    50 => array:2 [▼
      "id" => 0
      "name" => "Some Name"
    ]
    1 => array:2 [▼
      "id" => 1
      "name" => "Some Other Name"
    ]
    2 => array:2[...]
    3 => array:2[...]
    ...
    99 => array:2[...]  
  ]
]
```

The following will return page 3 or results 301 - 400
```php
CelcatWebAPI::resource()->where(['page' => 3])->get(100);
```

#### First

Using the first() method on a resource will return the first result.

```php
CelcatWebAPI::resource()->first();
```

```
array:3 [▼
  "pagination" => array:3 [▼
    "TotalRows" => 2103
    "TotalPages" => 2103
    "CurrentPage" => 0
  ]
  "parameters" => array:2 [▼
    "pageSize" => 1
    "page" => 0
  ]
  "data" => array:1 [▼
    0 => array:2 [▼
      "id" => 0
      "name" => "Some Name"
    ]
  ]
]
```

#### Where

The where operator allows you to define parameters for a query. Setting parameters on the `get` and `getAll` operators have been removed.

```php
CelcatWebAPI::resource()->where(['uniqueName' => '1234'])->get();
```

```
array:3 [▼
  "pagination" => array:3 [▼
    "TotalRows" => 2103
    "TotalPages" => 2103
    "CurrentPage" => 0
  ]
  "parameters" => array:2 [▼
    "pageSize" => 1
    "page" => 0
  ]
  "data" => array:1 [▼
    0 => array:2 [▼
      "id" => 0
      "uniqueName" => "1234"
      "name" => "Some Name"
    ]
  ]
]
```