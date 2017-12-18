# Database

`Litepie\Database\Modal` is an extension of `Illuminate\Database\Eloquent\Model`

In addition to it this few commonly used functionalities are included to facilitate the fast development.

## Initialization

Lavalite model variables are set through config files rather than specifying it directly in the modal class. For setting the variables `initialize()` function is used and it will iterate through each values and set the corresponding modal variable. The configuration path is specified in the config variable of the modal.

```php
	class Example extends Model
	{

	    /**
	     * Configuartion for the model.
	     *
	     * @var array
	     */
	    protected $config = 'lavalite.example.example.model';
```

> If you want to add new field in the modal as fillable just add it in the `fillable` array of the config. This feature is helpful when you are customizing a package developed for lavalite. Similar way you can do with all attributes like `hidden` `visible` `guard` `append` `casts` etc.

## Traits

### Manipulator

Some traits might want to manipulate with attributes of an Eloquent Model, this can however be done easy creating the getAttribute-method and/or setAttribute-method. However if multiple traits uses those methods then they will complain about each other since it is only allow for a class to use one trait with the same method. So if you are up to having a multiple traits using the same methods to manipulate the Eloquent Model attributes then you might use the manipulators for better support.

Here is how a trait using our methods should look like:

```php
	trait ExampleTrait
	{
		/* 
		* Modal attributes to be manipulated.
		*/
		$example = ['password'];

	    /**
	     * Boot the Example trait for a model.
	     *
	     * @return void
	     */
	    public static function bootExample()
	    {
	        static::addSetterManipulator('example.set', function ($model, $key, $value) {
	            if ($model->checkGetSetAttribute('example', $key)) {
	                return \Crypt::encrypt($value);;
	            }
	            return $value;
	        });

	        static::addGetterManipulator('example.get', function ($model, $key, $value) {
	            if ($model->checkGetSetAttribute('example', $key)) {
	                return \Crypt::decrypt($value);
	            }
	            return $value;
	        });
	    }
```

> `setExample`  and `getExample` can be the function in the trait.

Example model:
```php
	namespace App;

	use Litepie\Dtabase\Traits\Manipulator;
	use Vendor\Package\ExampleTrait;
	use Litepie\Database\Model;

	class User extends Model
	{
		use Manipulator;
	    use ExampleTrait;

	    //...
	}
```
> Lavalite model is already includes Manipulator trait.

Usage:
```php
	$user = new App\User;
	$user->password = 'secret';
	dump($user); // here you will see that the password is encrypted
	echo $user->password;
```

### Slugger

Slugs are meaningful codes that are commonly used in page URLs. To automatically generate a unique slug for your model, apply the Litepie\Database\Traits\Slugger trait and declare a $slugs property.

```php
	class User extends Model
	{
	    use \Litepie\Database\Traits\Slugger;

	    /**
	     * @var array Generate slugs for these attributes.
	     */
	    protected $slugs = ['slug' => 'name'];
	}
```

The $slugs property should be an array where the key is the destination column for the slug and the value is the source string used to generate the slug. In the above example, if the name column was set to Example, as a result the slug column would be set to example, example-1, or example-2, etc before the model is created.

To generate a slug from multiple sources, pass another array as the source value:

```php
	protected $slugs = [
	    'slug' => ['first_name', 'last_name']
	];
```


Slugs are only generated when a model first created. To override or disable this functionality, simply set the slug attribute manually:

```php
	$user = new User;
	$user->name = 'Remy';
	$user->slug = 'custom-slug';
	$user->save(); // Slug will not be generated
```

Use the slugAttributes method to generate slugs when updating a model:
```php

	$user = User::find(1);
	$user->slug = null;
	$user->slugAttributes();
	$user->save();
```

### Sorter

Sorted models will store a number value in sort_order which maintains the sort order of each individual model in a collection. To store a sort order for your models, apply the Litepie\Database\Traits\Sortable trait and ensure that your schema has a column defined for it to use (example: $table->integer('sort_order')->default(0);).

```php
	class User extends Model
	{
	    use \Litepie\Database\Traits\Sorter;
	}
```

You may modify the key name used to identify the sort order by defining the SORT_ORDER constant:

const SORT_ORDER = 'my_sort_order_column';
Use the setSortableOrder method to set the orders on a single record or multiple records.

```php
	// Sets the order of the user to 1...
	$user->setSortableOrder($user->id, 1);
```

// Sets the order of records 1, 2, 3 to 3, 2, 1 respectively...
$user->setSortableOrder([1, 2, 3], [3, 2, 1]);
Note: If adding this trait to a model where data (rows) already existed previously, the data set may need to be initialized before this trait will work correctly. To do so, either manually update each row's sort_order column or run a query against the data to copy the record's id column to the sort_order column (ex. UPDATE tabeble_name SET sort_order = id).

### DateFormatter

Dateformatter formats the date while setting and retrieving the date values form the model and date field should be included in the date attribute of the model.

Setting date attribute:
```php
	class Example extends Model
	{
	    use Litepie\Database\Traits\DateFormatter
	    /**
	     * @var array Generate slugs for these attributes.
	     */
	    protected $dates = ['date_field1', 'date_field1'];
	}
```

> In Lavalite you can set date attributes through config file

