# User

Manages different type of users in the project.

Lavalite user its [roles](http://lavalite.org/docs/master/roles) package for handling its roles and permissions. How ever you need to configure your new authentication models to access the features. Suppose you are creating  a new auth model  Clients you need to do the steps befor using it.

### Creating Roles
Create a role for the user which you are creating.

```php
use Litepie\Roles\Models\Role;

$clientRole = Role::create([
    'name' => 'Client',
    'slug' => 'client',
    'description' => '', // optional
    'level' => 1, // optional, set to 1 by default
]);
```
> You can make use of the admin interface to create the roles which can be accessed by the superuser, you can easly manage permissiond for the user through the admin console.

### Include trait
Include `CheckRoleAndPermission` trait and also implement `UserPolicy` contract inside your authentication model.

```php
namespace App;

use Illuminate\Database\Eloquent\SoftDeletes;
use Illuminate\Notifications\Notifiable;
use Litepie\Database\Model;
use Litepie\Database\Traits\Slugger;
use Litepie\Filer\Traits\Filer;
use Litepie\Foundation\Auth\User as Authenticatable;
use Litepie\Hashids\Traits\Hashids;
use Litepie\Repository\Traits\PresentableTrait;
use Litepie\Roles\Traits\CheckRoleAndPermission;
use Litepie\User\Contracts\UserPolicy;
use Litepie\User\Traits\User as UserProfile;

class Client extends Authenticatable implements UserPolicy
{
    use Filer, Notifiable, CheckRoleAndPermission, UserProfile, SoftDeletes, Hashids, Slugger, PresentableTrait;
    /**
     * Configuartion for the model.
     *
     * @var array
     */
    protected $config = 'users.client.model';


    /**
     * Initialiaze client modal.
     *
     * @param $name
     */
    public function __construct($attributes = [])
    {
        $config = config($this->config);

        foreach ($config as $key => $val) {

            if (property_exists(get_called_class(), $key)) {
                $this->$key = $val;
            }

        }

        $this->setRole('client');

        parent::__construct($attributes);
    }

    

```
 
 > Your authentication model shoud look like this.

### Helpers

Lavalite comes with the below hepler functions for the user module.

#### `user_id()`

The  `user_id()` function returner the current user id. If not authenticated it returns null.
    
    $attribute->user_id = user_id();

#### `user_type()` 

The  `user_type()` function returner the current user modal name.
    
    $attribute->user_type = user_type();
    // App/User

#### `user()` 

The  `user()` function returner the current user modal.
    
    user()->name;

    // Super User

Using 'user()' function you can access all the properties of the loggedin user.

# User Type

- [Basics](#basics)
- [Creating Users](#creating-users)
	- [User Table](#user-table)
	- [Seperate Table](#seperate-table)


<a name="basics"></a>
## Basics

Lavalite comes with 3 type of users `admin` `user` and `client`. For a basic cms website these 3 type of users will be enough. But when you are working for a big projects like `school management system` or `hospital management system` you have to deal with different type of users like `doctors` `office-staff` `managers` `department-heads` etc. 

<a name="creating-users"></a>

## Creating user

For a new type of user either you can make use of the existing `user` table or you can create separate tables for each type of users. You can decide which way you need to follow depending on the nature of your project. Here we explains both methods.

<a name="user-table"></a>
### User Table
If you are using `user` table for handling new type of user say `seller` you have to do the following steps for the same.

#### Create Role
For creating a role you can login to the `admin` screen and there you can add role as seller under Roles &amp; Permissions. And you can assign required permissions.


#### Update Config

The main thing you have to update is `config/auth.php` file add add these line of codes in the file.


```php

'guards'       => [


	...
    'seller'  => [
        'web' => [
            'driver'   => 'session',
            'provider' => 'seller',
        ],

        'api' => [
            'driver'   => 'token',
            'provider' => 'seller',
        ],

    ],
    ...
    
    
],

...
'providers'    => [


	...
    'seller'   => [
        'driver' => 'eloquent',
        'model'  => App\User::class,
    ],
	...
    
    
],
...
'passwords'    => [
    
    ...
    'seller'   => [
        'provider' => 'users',
        'table'    => 'password_resets',
        'expire'   => 60,
    ],
    ...

],

```

#### Assign Theme
In lavalite each type of user can have separate theme or can use same theme, you can find more details on theming on the [theming documentation](theming).

Once it is finished you can directly login to the new type of user on the link `http://path/to/your/application/seller`.
<a name="separate-table"></a>

## New Table
For using new table as a user type in addition to the above steps do the additional points for the same.

#### Create table
First you have to create new table `seller` you can do it by making a copy of `client` table.

#### Create Model
For creating a model you an make use of existing client Model, make a copy of the  `App\Clinet` to `App\Seller` and its content will look like. 

```php

namespace App;

use Litepie\User\Models\Client as BaseClient;

class Seller extends BaseClient
{
    /**
     * Configuartion for the model.
     *
     * @var array
     */
    protected $config = 'users.seller.model';
    

    /**
     * Roles for the model.
     *
     * @var array
     */
    protected $role = 'seller';

}
```

#### Update configuration
Update `config/user.php` file as follows, for publishing the configuration file use the artisan command `php artisan vendor:publish --provider="Litepie\User\UserServiceProvider" --tag="config"`.

```php


    'policies' => [
		...
        \App\Seller::class               => \Litepie\User\Policies\ClientPolicy::class
        ...
    ],

	...

    'client'   => [
        'model'      => [
            'model'         => \App\Seller::class,
            'table'         => 'sellers',
            'presenter'     => \Litepie\User\Repositories\Presenter\ClientPresenter::class,
            'hidden'        => [],
            'visible'       => [],
            'guarded'       => ['*'],
            'slugs'         => ['slug' => 'name'],
            'dates'         => ['deleted_at', 'createdat', 'updated_at'],
            'appends'       => [],
            'fillable'      => ['name', 'email', 'password', 'api_token', 'remember_token', 'sex', 'dob', 'mobile', 'phone'],
            'translatables' => [],
            'upload_folder' => 'user/seller',
            'uploads'       => [
                'photo' => [
                    'count' => 1,
                    'type'  => 'image',
                ],
            ],
            'casts'         => [
                'photo' => 'array',
            ],

            'revision'      => [],
            'perPage'       => '20',
            'search'        => [
                'name' => 'like',
                'status',
            ],
        ],

        'controller' => [
            'provider' => 'Litepie',
            'package'  => 'Clients',
            'module'   => 'Client',
        ],

    ],
    ...

    
```


> The first part of the configuration bind the policy. and second part defines the configuration for the model.
