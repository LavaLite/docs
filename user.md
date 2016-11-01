### User
##### Manages Access Control List and Users
#### USER MIDDLEWARES
If you have to control the access User provides middlewares to protect your routes. If you have to control the access through the Laravel routes, the User has some built-in middlewares for the trivial tasks. 

To utilize them, just put it in your ***app/Http/Kernel.php*** file.
##### PHP
```
<?

protected $routeMiddleware = [
    'auth'            => \App\Http\Middleware\Authenticate::class,
    'auth.basic'      => \Illuminate\Auth\Middleware\AuthenticateWithBasicAuth::class,
    'guest'           => \App\Http\Middleware\RedirectIfAuthenticated::class,

    // Simpler access control, uses only the groups
    'auth.role'       => \Litepie\User\Middlewares\NeedsRoleMiddleware::class
];
```
You can further study how to work with the middlewares below.

##### Create your own middleware

If the built-in middlewares doesn't fit your needs, you can make your own by using User's API to control the access.

#### USAGE
The User handles only access control. 

The authentication is still made by Laravel's Auth.

##### Create roles and permissions

##### With admin interface

You can create roles and permissions by login to the admin as super user for you application.

##### With the seeder or artisan tinker

You can also use the User's API. You can create a Laravel Seeder or use ***php artisan tinker***.

##### PHP
```
<?

use App\User;

$roleAdmin = User::createRole('admin');

// The first parameter is the permission name
// The second is the "friendly" version of the name. (usually for you to show it in your application).
$permission =  User::createPermission('user.create', 'Create Users');

// You can assign permission directly to a user.
$user = User::find(1);
$user->attachPermission($permission);

// or you can add the user to a group and that group has the power to rule create users.
$roleAdmin->attachPermission($permission);

// Now this user is in the Administrators group.
$user->attachRole($roleAdmin);
```
##### Using the middleware

You can protect your routes, through the use of the built-in middlewares.

User requires Laravel's Auth, so, use the auth middleware before the User's middleware that you intend to use.

##### Checking Permissions: needsPermissionMiddleware
##### PHP
```
<?

Route::get('foo', ['middleware' => ['auth', 'needsPermission'], 'shield' => 'user.create', function()
{
    return 'Yes I can!';
}]);
```
If you're using Laravel 5.1 it's possible to use Middleware Parameters.
##### PHP
```
<?

Route::get('foo', ['middleware' => ['auth', 'needsPermission:user.index'], function() {
    return 'Yes I can!';
}]);
```
With this syntax it's also possible to use the middlewaren within your controllers.
##### PHP
```
<?

$this->middleware('needsPermission:user.index');
```
You can pass an array of permissions to check on.
##### PHP
```
<?

Route::get('foo', ['middleware' => ['auth', 'needsPermission'], 'shield' => ['user.index', 'user.create'], function()
{
    return 'Yes I can!';
}]);
```
When using middleware parameters, use a | to separate multiple permissions.
##### PHP
```
<?

Route::get('foo', ['middleware' => ['auth', 'needsPermission:user.index|user.create'], function() {
    return 'Yes I can!';
}]);
```
Or within controllers:
```
<?

$this->middleware('needsPermission:user.index|user.create');
```
When you pass an array of permissions, the route will be fired only if the user has all the permissions. However, if you want to allow the access to the route when the user has at least one of the permissions, just add ***'any' => true***.
##### PHP
```
<?

Route::get('foo', ['middleware' => ['auth', 'needsPermission'], 'shield' => ['user.index', 'user.create'], 'any' => true, function()
{
    return 'Yes I can!';
}]);
```
Or, with middleware parameters, pass it as the 2nd parameter
```
<?

Route::get('foo', ['middleware' => ['auth', 'needsPermission:user.index|user.create,true'], function() {
    return 'Yes I can!';
}]);
```
Or within controllers:

```
<?

$this->middleware('needsPermission:user.index|user.create,true');
```

#### Checking Roles: needsRoleMiddleware
This is similar to the previous middleware, but only the roles are checked, it means that it doesn't check the permissions.
##### PHP
```
<?

Route::get('foo', ['middleware' => ['auth', 'needsRole'], 'is' => 'admin', function()
{
    return 'Yes I am!';
}]);
```
If you're using Laravel 5.1 it's possible to use Middleware Parameters.
```
<?

Route::get('foo', ['middleware' => ['auth', 'needsRole:admin'], function() {
    return 'Yes I am!';
}]);
```
With this syntax it's also possible to use the middlewaren within your controllers.
```
<?

$this->middleware('needsRole:admin');
```
You can pass an array of permissions to check on.
```
<?

Route::get('foo', ['middleware' => ['auth', 'needsRole'], 'shield' => ['admin', 'member'], function()
{
    return 'Yes I am!';
}]);
```
When using middleware parameters, use a **|** to separate multiple roles.

```
<?

Route::get('foo', ['middleware' => ['auth', 'needsRole:admin|editor'], function() {
    return 'Yes I am!';
}]);
```
Or within controllers:
```
<?

$this->middleware('needsRole:admin|editor');
```
When you pass an array of permissions, the route will be fired only if the user has all the permissions. However, if you want to allow the access to the route when the user has at least one of the permissions, just add ***'any' => true***.
```
<?

Route::get('foo', ['middleware' => ['auth', 'needsRole'], 'is' => ['admin', 'member'], 'any' => true, function()
{
    return 'Yes I am!';
}]);
```
Or, with middleware parameters, pass it as the 2nd parameter
```
<?

Route::get('foo', ['middleware' => ['auth', 'needsRole:admin|editor,true'], function() {
    return 'Yes I am!';
}]);
```
Or within controllers:
```
<?

$this->middleware('needsRole:admin|editor,true');
```
#### USING IN VIEWS
The Laravel's Blade extension for using User.

**@shield**
##### HTML

```
@shield('user.index')
    shows your protected stuff
@endshield
@shield('user.index')
    shows your protected stuff
@else
    shows the data for those who doesn't have the user.index permission
@endshield
```

**@is**

```
@is('admin')
    Shows data for the logged user and that belongs to the admin role
@endis
@is('admin')
    Shows data for the logged user and that belongs to the admin role
@else
    shows the data for those who doesn't have the admin permission
@endis
@is(['role1', 'role2'])
    Shows data for the logged user and that belongs to the admin role
@else
    shows the data for those who doesn't have the admin permission
@endis
```

#### USING THE FACADE
With the User's Facade you can access the API and use it at any part of your application.

**User::hasPermission($permission)**:

Check if the logged user has the ***$permission***.

**User::canDo($permission)**:

Check if the logged user has the ***$permission***. If the role ***superuser*** returns true

**User::roleHasPermission($permission)**:

Check if the logged user has the ***$permission*** checking only the role permissions.

**User::hasRole($roleName)**:

Check if the logged user belongs to the role ***$roleName***.

**User::roleExists($roleName)**:

Check if the role ***$roleName*** exists in the database.

**User::permissionExists($permissionName)**:

Check if the permission ***$permissionName*** exists in the database.

**User::findRole($roleName)**:

Find the role in the database by the name ***$roleName***.

**User::findRoleById($roleId)**:

Find the role in the database by the role ID roleId.

**User::findPermission($permissionName)**:

Find the permission in the database by the name ***$permissionName***.

**User::findPermissionById($permissionId)**:

Find the permission in the database by the ID ***$permissionId***.

**User::createRole($roleName)**:

Create a new role in the database.

**User::createPermission($permissionName)**:

Create a new permission in the database.

**User::is($roleName)**:

Check whether the current user belongs to the role.

**User::javascript()->render()**:

Returns a javascript script with a list of all roles and permissions of the current user. The variable name can be modified.

#### USING THE TRAIT
To add the User's features, you need to add the trait ***HasUser*** in you User model (usually ***App\User***).

**public function hasPermission($permission)**:

This method checks if the logged user has the permission ***$permission***

In User, there are 2 kind of permissions: ***User permissions*** and ***Role permissions***. By default, the permissions that the user inherits, are permissions of the roles that it belongs to. However, always that a user pemission is set, it will take precedence of role permission.
##### PHP
```
<?

public function foo(Authenticable $user)
{
    if ($user->hasPermission('user.create'));
}
``` 
**public function attachRole($role)**:

Attach the user to the role ***$role***. The ***$role*** variable might be an object of the type ***Litepie\User\Role*** or an array containing the ***ids*** of the roles.
##### PHP
```
<?

public function foo(Authenticable $user)
{
    $role = User::findRole('admin'); // Returns an Litepie\User\Role
    $user->attachRole($role);

    // or

    $roles = [1, 2, 3]; // Using an array of ids
    $user->attachRole($roles);
}
```
**public function detachRole($role)**:

Deatach the role ***$role*** from the user (inverse to ***attachRole()***).
##### PHP
```
<?

public function foo(Authenticable $user)
{
    $role = User::findRole('admin'); // Returns an Litepie\User\Role
    $user->detachRole($role);

    // ou

    $roles = [1, 2, 3]; // Using an array of ids
    $user->detachRole($roles);
}
```
**public function syncRoles(array $roles = array())**:

This is like the attachRole() method, but only the roles in the array $roles will be on the relationship after the method runs. $roles it's an array of ids for the needed roles.
##### PHP
```
<?

public function foo(Authenticable $user)
{
    $roles = [1, 2, 3]; // Using an array of ids

    $user->syncRoles($roles);
}
```
**public function attachPermission($permission, array $options = array())**:

Attach the user to the permission ***$permission***. The ***$permission*** variable is an instance of the ***Litepie\User\Permission*** class.
##### PHP
```
<?

public function foo(Authenticable $user)
{
    $permission = User::findPermission('user.create');

    $user->attachPermission($permission, [
        'value' => true // true = has the permission, false = doesn't have the permission,
    ]);
}
```

**public function detachPermission($permission)**:

Remove the permission ***$permission*** from the user. The ***$permission*** variable might be an instance of the ***Litepie\User\Permission*** class or an array of ids with the ***ids*** of the permissions to be removed.

##### PHP
```
<?

public function foo(Authenticable $user)
{
    $permission = User::findPermission('user.create');
    $user->detachPermission($permission);

    // or

    $permissions = [1, 3];
    $user->detachPermission($permissions);
}
```

**public function syncPermissions(array $permissions)**:

This is like the method ***syncRoles***. but only the roles in the array ***$permissions*** be on the relationship after the method runs.

```
public function foo(Authenticable $user)
{
    $permissions = [
        1 => ['value' => false],
        2 => ['value' => true,
        3 => ['value' => true]
    ];

    $user->syncPermissions($permissions);
}
```
**public function revokePermissions()**:

This removes all the user permissions.

##### PHP
```
<?

public function foo(Authenticable $user)
{
    $user->revokePermissions();
}
```
**public function revokeExpiredPermissions()**:

Remove all the temporary expired pemissions from the user. More about temporary permissions below.
##### PHP
```
<?

public function foo(Authenticable $user)
{
    $user->revokeExpiredPermissions();
}
```



