# Hashids

###### Laravel 5 wrapper for the [Hashids](http://hashids.org)
[Hashids](http://hashids.org) is a small open-source library that generates short, unique, non-sequential ids from numbers.

## Configuration

Litepie Hashids requires connection configuration. You can edit the configuration file on `config/hashids.php`


## Usage

#### Litepie\Support\Facades\Hashids

This facade will dynamically pass static method calls to the `hashids` object in the ioc container.

### Examples
Here you can see an example of just how simple this package is to use. 
##### PHP
```
// You can alias this in config/app.php.
use Litepie\Support\Facades\Hashids;

Hashids::encode(4815162342);
// We're done here - how easy was that, it just works!

Hashids::decode('doyouthinkthatsairyourebreathingnow');
// This example is simple and there are far more methods available.
```
### Helpers
##### PHP
```


hashids_encode(4815162342);
// We're done here - how easy was that, it just works!

hashids_decode('doyouthinkthatsairyourebreathingnow');
// This example is simple and there are far more methods available.
```
### Traits
You can use hashids traits to decode id (primary key) of the model.
##### PHP
```
<?php

use Litepie\Database\Model;

use Litepie\Hashids\Traits\Hashids;

class MyModel extends Model
{
    use Hashids;

    ......
}
```
This trait overrides two model methods.
##### PHP
```
$model->getRouteKey()
$model->findorFail($hashed_id)
```
