# Users

- [Basics](#basics)
- [Creating Users](#creating-users)
	- [User Table](#user-table)
	- [Seperate Table](#seperate-table)


<a name="basics"></a>
## Basics

Laravel comes with 3 type of users `admin` `user` and `client`. For a basic cms website these 3 type of users will be enough. But when you are working for a big projects like `school management system` or `hospital management system` you have to deal with different type of users like `doctors` `office staff` `managers` `department heads` etc. 

<a name="creating-users"></a>

## Creating user

For a new type of user either you can make use of the existing `user` table or you can create separate tables for each type of users. You can decide which way you need to follow depending on the nature of your project. Here we explains both methods.

<a name="user-table"></a>
### User Table
If you are using user table for handling new type of user say `seller` you have to do the following steps for the same.

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
### Separate (new) table
For using new table as a user type in addition to the above steps do the additional points for the same.

#### Create table
First you have to create new table `seller` you can do it by making a copy of `client` table.

#### Create Model
For creating a model you an make use of existing client Model, make a copy of the  `App\Clinet` to `App\Seller` and its content will look like. 

```php

namespace App;

use Litepie\User\Models\Client as BaseClient;

class Client extends BaseClient
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
            'fillable'      => ['name', 'email', 'password', 'api_token', 'remember_token', 'sex', 'dob', 'mobile', 'phone', 'address', 'street', 'city', 'district', 'state', 'country', 'photo', 'web', 'status', 'upload_folder', 'deleted_at', 'created_at', 'updated_at'],
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
