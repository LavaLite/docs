# Presenter



This package provides an easy way to create Presenters that can be used to [decorate](https://en.wikipedia.org/wiki/Decorator_pattern) an Eloquent Model. It is heavily inspired by [Hemp Presenter](https://github.com/davidhemphill/presenter) and [Laravel API Resources](https://laravel.com/docs/6.x/eloquent-resources), however it comes with some key differences:
 - It is a *simplified* version that does not offers Traits and Macros
 - It provides an easy way to handle *paginated* models
 - It does not focus solely in JSON API responses but it serves as a *general purpose* decorator that can be used anywhere in the project
 
## TL;DR

A **Decorator** is an object that wraps another object in order to add functionality to the wrapped object. It also delegates calls to methods not available on the decorator to the class being decorated. Decorators can be useful when you need to modify the functionality of a class without relying on inheritance. You can use it to add optional features to your objects like logging, access-control, and things of that sort. A **Presenter** is a type of decorator used to present an object to the view of an application, such as a Blade view, or an API response.


## Usage

### Customizing Presenter Classes

Presenters are simple classes designed to encapsulate complex or repetitive view logic. For example, here is a simple `UserPresenter` class:

```php
<?php

namespace App\Presenters;

use CultureGr\Presenter\Presenter;

class UserPresenter extends Presenter
{

    public function fullname()
    {
        return $this->firstname.' '.$this->lastname;
    }
}
```

Note that the original model's properties (`firstname`, `lastname`) can be directly accessed using the `$this` variable. This is because the `Presenter` class automatically proxies property and method access down to the underlying model.

This class has a custom method `fullname` that can be called wherever an instance of this Presenter is used. 

```php
$presentedUser->firstname; //=> 'John'
$presentedUser->lastname; //=> 'Doe'
$presentedUser->fullname() //=> 'John Doe'
```

If the presented model needs to be transformed into a JSON structure the Presenter class should define a `toArray` method which returns the array of attributes that should be converted to JSON when sending the response.

```php
<?php

namespace App\Presenters;

use CultureGr\Presenter\Presenter;

class UserPresenter extends Presenter
{
    public function fullname()
    {
        return $this->firstname.' '.$this->lastname;
    }

    public function toArray()
    {
        return [
            'id' => $this->id,
            'fullname' => $this->fullname()
             // ...                  
        ];
    }
}
```

### Presenting Single Models

Once the presenter is defined, it may be used to create a presented model using the `make` factory method:

```php
$user = User::first();
$presentedUser = UserPresenter::make($user);
```
 
The presented model may be used anywhere in the project. If the model needs to be serialized into a response (like for a view or API response) the overwritten `toArray` method must be explicitly called,  or it can be directly returned from a route or controller: 

```php
<?php

namespace App\Http\Controllers;

use App\Models\User;
use App\Presenters\UserPresenter;

class UserController extends Controller
{
    public function show($id)
    {
        return view('users.show', [
            'user' => UserPresenter::make(User::find($id))->toArray()
        ]);
    }
}
```

### Presenting Collections

To present a collection of models, the static `collection` method on the Presenter class can be used:

```php
$users = User::all();
$presentedUsers = UserPresenter::collection($users);
```

The collection of presented models may be used anywhere in the project. If the collection needs to be serialized into a response (like for a view or API response) the overwritten `toArray` method must be explicitly called,  or it can be directly returned from a route or controller: 

```php
<?php

namespace App\Http\Controllers;

use App\Models\User;
use App\Presenters\UserPresenter;

class UserController extends Controller
{
    public function index()
    {
        $users = User::all();

        return UserPresenter::collection($users);
    }
}
```

The output will be something like this:

```json
[
    {"id": "1", "fullname": "John Doe"},
    {"id": "2", "fullname": "Jane Doe"}
]
```

### Presenting Paginated Collections

To present a paginated collection of models, the static `pagination` method on the Presenter class can be used:

```php
$users = User::paginate();
$presentedUsers = UserPresenter::pagination($users);
```

The paginated collection of presented models may be used anywhere in the project. If the collection needs to be serialized into a response (like for a view or API response) the overwritten `toArray` method must be explicitly called,  or it can be directly returned from a route or controller: 

```php
<?php

namespace App\Http\Controllers;

use App\Models\User;
use App\Presenters\UserPresenter;

class UserController extends Controller
{
    public function index()
    {
        $users = User::paginate();

        return UserPresenter::pagination($users);
    }
}
```

The output will contain the presented models wrapped in a `data` key, as long as `meta` and `links` keys with information about the paginator's state:

```json
{
    "data": [
        {"id": "1", "fullname": "John Doe"},
        {"id": "2", "fullname": "Jane Doe"}
    ],
    "links":{
        "first": "http://example.com/pagination?page=1",
        "last": "http://example.com/pagination?page=1",
        "prev": null,
        "next": null
    },
    "meta":{
        "current_page": 1,
        "from": 1,
        "last_page": 1,
        "path": "http://example.com/pagination",
        "per_page": 15,
        "to": 10,
        "total": 10
    }
}
```



