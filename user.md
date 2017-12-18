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

#### `user_id()` {#user_id}

The  `user_id()` function returner the current user id. If not authenticated it returns null.
    
    $attribute->user_id = user_id();

#### `user_type()` {#user_type}

The  `user_type()` function returner the current user modal name.
    
    $attribute->user_type = user_type();
    // App/User

#### `user()` {#user_modal}

The  `user()` function returner the current user modal.
    
    user()->name;

    // Super User

Using 'user()' function you can access all the properties of the loggedin user.
