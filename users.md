# Users

- [Basics](#basics)
- [Creating users](#users)
	- [Roles](#roles)
	- [Guards](#customization)


<a name="basics"></a>
## Basics

Laravel comes with 3 type of users `admin` `user` and `client`. For a basic cms website these 3 type of users will be enough. But when you are working for a big projects like `school management system` or `hospital management system` you have to deal with different type of users like `doctors` `office staff` `managers` `department heads` etc. 

<a name="structure"></a>

## Creating user

For a new type of user either you can make use of the existing `user` table or you can create separate tables for each type of users. You can decide which way you need to follow depending on the nature of your project. Here we explains both methods.

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

### Separate (new) Table
For using new table as use 
