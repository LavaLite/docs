# Repository

Litepie Repository is used to abstract the data layer. This allows the user to create a more flexible application and further providing an ease of maintenance on the same. 

#### Repository Interface
The Link to the same can be found under

```php
Litepie\Repository\Contracts\RepositoryInterface
```
The following methods are present under the Repository Interface

```php
all($columns = array('*'))
first($columns = array('*'))
paginate($limit = null, $columns = ['*'])
find($id, $columns = ['*'])
findByField($field, $value, $columns = ['*'])
findWhere(array $where, $columns = ['*'])
findWhereIn($field, array $where, $columns = [*])
findWhereNotIn($field, array $where, $columns = [*])
create(array $attributes)
update(array $attributes, $id)
delete($id)
with(array $relations);
hidden(array $fields);
visible(array $fields);
scopeQuery(Closure $scope);
getFieldsSearchable();
setPresenter($presenter);
skipPresenter($status = true);
```
#### Repository Criteria Interface
The Link to the same can be found under

```php
Litepie\Repository\Contracts\RepositoryCriteriaInterface
```
The following methods are present under the Repository Criteria Interface

```php
pushCriteria(CriteriaInterface $criteria)
getCriteria()
getByCriteria(CriteriaInterface $criteria)
skipCriteria($status = true)
getFieldsSearchable()
```
#### Cacheable Interface
The Link to the same can be found under


```php
Litepie\Repository\Contracts\CacheableInterface
```
The following methods are present under the Cacheable Interface

```php
setCacheRepository(CacheRepository $repository)
getCacheRepository()
getCacheKey($method, $args = null)
getCacheMinutes()
skipCache($status = true)
```
#### Presenter Interface
The Link to the same can be found under

```php
Litepie\Repository\Contracts\PresenterInterface
```
The following methods are present under the Cacheable Interface

```php
present($data);
```
#### Presentable
The Link to the same can be found under

```php
Litepie\Repository\Contracts\Presentable
```
The following methods are present under Presentable

```php
setPresenter(PresenterInterface $presenter);
presenter();
```
#### Criteria Interface
The Link to the same can be found under

```php
Litepie\Repository\Contracts\CriteriaInterface
```
The following methods under Criteria interface are as follows

```php
apply($model, RepositoryInterface $repository);
```
#### Transformable
The Link to the same can be found under

```php
Litepie\Repository\Contracts\Transformable
```
The following methods under Tranformable are as follows

```php
transform();
```
#### USAGE

#### CREATE A MODEL
The user can use the code to create a model normally, however it is important to ensure that attributes are defined from the input data.

```php
namespace App;

class Post extends Eloquent { // or Ardent, Or any other Model Class

    protected $fillable = [
        'title',
        'author',
        ...
     ];

     ...
}
```
#### CREATE A REPOSITORY
The user can develop a Repository for your Database by extending the Base repository.

```php
namespace App;

use Litepie\Repository\Eloquent\BaseRepository;

class PostRepository extends BaseRepository {

    /**
     * Specify Model class name
     *
     * @return string
     */
    function model()
    {
        return "App\\Post";
    }
}
```
#### GENERATORS
The Generators have enable users to create Repositories with ease.
#### Config
The user must first configure the respective storage location of the repository files. This is by default done through the "app" folder and the namespace "App".

```php
...
    'generator'=>[
        'basePath'=>app_path(),
        'rootNamespace'=>'App\\',
        'paths'=>[
            'models'=>'Entities',
            'repositories'=>'Repositories',
            'interfaces'=>'Repositories',
            'transformers'=>'Transformers',
            'presenters'=>'Presenters'
        ]
    ]
```
The user may want to save the root of your project folder outside the location of the app and add another namespace, For Eg.

```php
...
     'generator'=>[
        'basePath'      => base_path('src/Lorem'),
        'rootNamespace' => 'Lorem\\'
    ]
```
Additionally, the user may wish to customize where your generated classes gets saved. This can be accomplished by editing the **paths **node to your liking. For Eg.

```php
 'generator'=>[
        'basePath'=>app_path(),
        'rootNamespace'=>'App\\',
        'paths'=>[
            'models'=>'Models',
            'repositories'=>'Repositories\\Eloquent',
            'interfaces'=>'Contracts\\Repositories',
            'transformers'=>'Transformers',
            'presenters'=>'Presenters'
        ]
    ]
```

#### Binding the repository

Done, done that just now you do bind its interface for your real repository, for example in your own Repositories Service Provider.


```php
App::bind('{YOUR_NAMESPACE}Repositories\PostRepository', '{YOUR_NAMESPACE}Repositories\PostRepositoryEloquent');
```
And use

```php
public function __construct({YOUR_NAMESPACE}Repositories\PostRepository $repository){
    $this->repository = $repository;
}
```
#### USE METHOD

```php
namespace App\Http\Controllers;

use App\PostRepository;

class PostsController extends BaseController {

    /**
     * @var PostRepository
     */
    protected $repository;

    public function __construct(PostRepository $repository){
        $this->repository = $repository;
    }

    ....
}
```
Find all results in Repository

```php
$posts = $this->repository->all();
```
Find all results in Repository with pagination

```php
$posts = $this->repository->paginate($limit = null, $columns = ['*']);
```
Find by result by id

```php
$post = $this->repository->find($id);
```
Hiding attributes of the model

```php
$post = $this->repository->hidden(['country_id'])->find($id);
```
Showing only specific attributes of the model

```php
$post = $this->repository->visible(['id', 'state_id'])->find($id);
```
Loading the Model relationships

```php
$post = $this->repository->with(['state'])->find($id);
```
Find by result by field name

```php
$posts = $this->repository->findByField('country_id','15');
```
Find by result by multiple fields

```php
$posts = $this->repository->findWhere([
    //Default Condition =
    'state_id'=>'10',
    'country_id'=>'15',
    //Custom Condition
    ['columnName','>','10']
]);
```
Find by result by multiple values in one field

```php
$posts = $this->repository->findWhereIn('id', [1,2,3,4,5]);
```
Find by result by excluding multiple values in one field

```php
$posts = $this->repository->findWhereNotIn('id', [6,7,8,9,10]);
```
Find all using custom scope

```php
$posts = $this->repository->scopeQuery(function($query){
    return $query->orderBy('sort_order','asc');
})->all();
```
Create new entry in Repository

```php
$post = $this->repository->create( Input::all() );
```
Update entry in Repository

```php
$post = $this->repository->update( Input::all(), $id );
```
Delete entry in Repository

```php
$this->repository->delete($id)
```
#### CREATE A CRITERIA
The Criteria is a way to change the repository of the query by applying specific conditions according to the user's needs. The user can add multiple Criteria into the repository.

```php
use Litepie\Repository\Contracts\RepositoryInterface;
use Litepie\Repository\Contracts\CriteriaInterface;

class MyCriteria implements CriteriaInterface {

    public function apply($model, RepositoryInterface $repository)
    {
        $model = $model->where('user_id','=', Auth::user()->id );
        return $model;
    }
}
```
#### USING THE CRITERIA IN A CONTROLLER
The criteria can be utilized in the controller using the following code.
#### PHP
```
namespace App\Http\Controllers;

use App\PostRepository;

class PostsController extends BaseController {

    /**
     * @var PostRepository
     */
    protected $repository;

    public function __construct(PostRepository $repository){
        $this->repository = $repository;
    }


    public function index()
    {
        $this->repository->pushCriteria(new MyCriteria());
        $posts = $this->repository->all();
    ...
    }

}
```
The user could obtain the results from Criteria utilizing the following code.

```php
$posts = $this->repository->getByCriteria(new MyCriteria());
```
The user can set the default Criteria in Repository

```php
use Litepie\Repository\Eloquent\BaseRepository;

class PostRepository extends BaseRepository {

    public function boot(){
        $this->pushCriteria(new MyCriteria());
        $this->pushCriteria(new AnotherCriteria());
        ...
    }

    function model(){
       return "App\\Post";
    }
}
```
#### SKIP CRITERIA
The user can utilize the **skipCriteria** before any chaining method.

```php
$posts = $this->repository->skipCriteria()->all();
```
#### USING THE REQUESTCRITERIA
The RequestCriteria is a standard Criteria implementation. It enables filters to perform in the repository from parameters sent in the request.

The user can perform a dynamic search, filter the data and customize the queries.

To use the Criteria in your repository, the user can add a new criteria in the boot method of your repository, or directly use in your controller, in order to filter out only a few requests.
#### ENABLING YOUR REPOSITORY

```php
use Litepie\Repository\Eloquent\BaseRepository;
use Litepie\Repository\Criteria\RequestCriteria;


class PostRepository extends BaseRepository {

  /**
     * @var array
     */
    protected $fieldSearchable = [
        'name',
        'email'
    ];

    public function boot(){
        $this->pushCriteria(app('Litepie\Repository\Criteria\RequestCriteria'));
        ...
    }

    function model(){
       return "App\\Post";
    }
}
```
The user would need to define which fields from the model can be searchable.

In the repository the fields that are searchable should be set to $fieldSearchable.

```php
protected $fieldSearchable = [
  'name',
  'email'
];
```
You can set the type of condition which will be used to perform the query, the default condition is **"="** 

```php
protected $fieldSearchable = [
  'name'=>'like',
  'email', // Default Condition "="
  'your_field'=>'condition'
];
```
#### ENABLING IN CONTROLLER

```php
 public function index()
    {
        $this->repository->pushCriteria(app('Litepie\Repository\Criteria\RequestCriteria'));
        $posts = $this->repository->all();
    ...
    }
```
#### EXAMPLE THE CRITERIA
The user can Request all data without filter by request

```
http://lavalite.local/users
```

```php
[
    {
        "id": 1,
        "name": "John Doe",
        "email": "john@gmail.com",
        "created_at": "-0001-11-30 00:00:00",
        "updated_at": "-0001-11-30 00:00:00"
    },
    {
        "id": 2,
        "name": "Lorem Ipsum",
        "email": "lorem@ipsum.com",
        "created_at": "-0001-11-30 00:00:00",
        "updated_at": "-0001-11-30 00:00:00"
    },
    {
        "id": 3,
        "name": "Laravel",
        "email": "laravel@gmail.com",
        "created_at": "-0001-11-30 00:00:00",
        "updated_at": "-0001-11-30 00:00:00"
    }
]
```
Conducting research in the repository

```
http://lavalite.local/users?search=John%20Doe
```
***Or*** 

```
http://lavalite.local/users?search=John&searchFields=name:like
```

***Or*** 

```
http://lavalite.local/users?search=john@gmail.com&searchFields=email:=
```

***Or*** 

```
http://lavalite.local/users?search=name:John Doe;email:john@gmail.com
```

***Or*** 

```
http://lavalite.local/users
```

```
?search=name:John;email:john@gmail.com&searchFields=name:like;email:=
```


```php
[
    {
        "id": 1,
        "name": "John Doe",
        "email": "john@gmail.com",
        "created_at": "-0001-11-30 00:00:00",
        "updated_at": "-0001-11-30 00:00:00"
    }
]
```
Filtering fields
```
http://lavalite.local/users?filter=id;name
```

```php
[
    {
        "id": 1,
        "name": "John Doe"
    },
    {
        "id": 2,
        "name": "Lorem Ipsum"
    },
    {
        "id": 3,
        "name": "Laravel"
    }
]
```
Sorting the results
```
http://lavalite.local/users?filter=id;name&orderBy=id&sortedBy=desc
```

```php
[
    {
        "id": 3,
        "name": "Laravel"
    },
    {
        "id": 2,
        "name": "Lorem Ipsum"
    },
    {
        "id": 1,
        "name": "John Doe"
    }
]
```
Add relationship
```
http://lavalite.local/users?with=groups
```
#### OVERWRITE PARAMS NAME
The user can change the name of the parameters in the configuration file **config/repository.php**
#### CACHE
The user can further add a layer of cache easily to your repository
#### Cache Usage
Implements the interface CacheableInterface and use CacheableRepository Trait.

```php
use Litepie\Repository\Eloquent\BaseRepository;
use Litepie\Repository\Contracts\CacheableInterface;
use Litepie\Repository\Traits\CacheableRepository;

class PostRepository extends BaseRepository implements CacheableInterface {

    use CacheableRepository;

    ...
}
```
Here the repository will be cached , and the repository cache is cleared whenever an item is created, modified or deleted.
#### Cache Config
You can change the cache settings in the file config/repository.php and also directly on your repository.

***config/repository.php***

```php
'cache'=>[
    //Enable or disable cache repositories
    'enabled'   => true,

    //Lifetime of cache
    'minutes'   => 30,

    //Repository Cache, implementation Illuminate\Contracts\Cache\Repository
    'repository'=> 'cache',

    //Sets clearing the cache
    'clean'     => [
        //Enable, disable clearing the cache on changes
        'enabled' => true,

        'on' => [
            //Enable, disable clearing the cache when you create an item
            'create'=>true,

            //Enable, disable clearing the cache when upgrading an item
            'update'=>true,

            //Enable, disable clearing the cache when you delete an item
            'delete'=>true,
        ]
    ],
    'params' => [
        //Request parameter that will be used to bypass the cache repository
        'skipCache'=>'skipCache'
    ],
    'allowed'=>[
        //Allow caching only for some methods
        'only'  =>null,

        //Allow caching for all available methods, except
        'except'=>null
    ],
],
```
Further, it is possible to override these settings directly in the repository.

```php
use Litepie\Repository\Eloquent\BaseRepository;
use Litepie\Repository\Contracts\CacheableInterface;
use Litepie\Repository\Traits\CacheableRepository;

class PostRepository extends BaseRepository implements CacheableInterface {

    // Setting the lifetime of the cache to a repository specifically
    protected $cacheMinutes = 90;

    protected $cacheOnly = ['all', ...];
    //or
    protected $cacheExcept = ['find', ...];

    use CacheableRepository;

    ...
}
```
The cacheable methods are : all, paginate, find, findByField, findWhere, getByCriteria
#### VALIDATORS
Requires **lavalite/laravel-validator**. 
```
composer require lavalite/laravel-validator
```
#### USING A VALIDATOR CLASS
#### Create a Validator
In the example below, we define some rules for both creation and edition.

```php
use \Litepie\Validator\LaravelValidator;

class PostValidator extends LaravelValidator {

    protected $rules = [
        'title' => 'required',
        'text'  => 'min:3',
        'author'=> 'required'
    ];

}
```
To define specific rules, proceed as shown below:

```php
use \Litepie\Validator\Contracts\ValidatorInterface;
use \Litepie\Validator\LaravelValidator;

class PostValidator extends LaravelValidator {

    protected $rules = [
        ValidatorInterface::RULE_CREATE => [
            'title' => 'required',
            'text'  => 'min:3',
            'author'=> 'required'
        ],
        ValidatorInterface::RULE_UPDATE => [
            'title' => 'required'
        ]
   ];

}
```
#### Enabling Validator in the Repository

```php
use Litepie\Repository\Eloquent\BaseRepository;
use Litepie\Repository\Criteria\RequestCriteria;

class PostRepository extends BaseRepository {

    /**
     * Specify Model class name
     *
     * @return mixed
     */
    function model(){
       return "App\\Post";
    }

    /**
     * Specify Validator class name
     *
     * @return mixed
     */
    public function validator()
    {
        return "App\\PostValidator";
    }
}
```
#### Defining rules in the repository
Alternatively, instead of using a class to define its validation rules, the user can set your rules directly into the rules repository property, it will have the same effect as a Validation class.

```php
use Litepie\Repository\Eloquent\BaseRepository;
use Litepie\Repository\Criteria\RequestCriteria;
use Litepie\Validator\Contracts\ValidatorInterface;

class PostRepository extends BaseRepository {

    /**
     * Specify Validator Rules
     * @var array
     */
     protected $rules = [
        ValidatorInterface::RULE_CREATE => [
            'title' => 'required',
            'text'  => 'min:3',
            'author'=> 'required'
        ],
        ValidatorInterface::RULE_UPDATE => [
            'title' => 'required'
        ]
   ];

    /**
     * Specify Model class name
     *
     * @return mixed
     */
    function model(){
       return "App\\Post";
    }

}
```
Validation is now ready. In case of a failure an exception will be given of the type: ***Litepie\Validator\Exceptions\ValidatorException***
#### Presenters
Presenters function as a wrapper within the system and also a renderer for objects
#### Fractal Presenter
This requires [Fractal](http://fractal.thephpleague.com/) 
``` 
composer require league/fractal
```
There are two ways to implement the Presenter, 

1. The first is creating a TransformerAbstract and setting it using your Presenter class as described in the Create a Transformer Class.

2. The second way is to make your model implement the Transformable interface, and use the default Presenter ModelFractarPresenter, this will have the same effect. 

#### Transformer Class
You can **Create a transformer class ** using the following command.


```php
php artisan make:transformer Post
```
The code would generate the following class

```php
use League\Fractal\TransformerAbstract;

class PostTransformer extends TransformerAbstract
{
    public function transform(\Post $post)
    {
        return [
            'id'      => (int) $post->id,
            'title'   => $post->title,
            'content' => $post->content
        ];
    }
}
```
#### Create a Presenter
Create a Presenter using the command

```php
php artisan make:presenter Post
```
The command will further prompt you for creating a Transformer too if you haven't already.


Create a Presenter


```php
use Litepie\Repository\Presenter\FractalPresenter;

class PostPresenter extends FractalPresenter {

    /**
     * Prepare data to present
     *
     * @return \League\Fractal\TransformerAbstract
     */
    public function getTransformer()
    {
        return new PostTransformer();
    }
}
```
Enabling in your Repository

```php
use Litepie\Repository\Eloquent\BaseRepository;

class PostRepository extends BaseRepository {

    ...

    public function presenter()
    {
        return "App\\Presenter\\PostPresenter";
    }
}
```
Or enable it in your controller with the following code

```php
$this->repository->setPresenter("App\\Presenter\\PostPresenter");
```
**Using the presenter after from the Model** 

If you recorded a presenter and sometime used the **skipPresenter()** method or simply you do not want your result is not changed automatically by the presenter. You can implement Presentable interface on your model so you will be able to present your model at any time. 

In your model, implement the interface

```php
Litepie\Repository\Contracts\Presentable
```
And

```php
Litepie\Repository\Traits\PresentableTrait
```

```php
namespace App;

use Litepie\Repository\Contracts\Presentable;
use Litepie\Repository\Traits\PresentableTrait;

class Post extends Eloquent implements Presentable {

    use PresentableTrait;

    protected $fillable = [
        'title',
        'author',
        ...
     ];

     ...
}
```
Once this is done, the user can submit your Model individually, See an example:

```php
$repository = app('App\PostRepository');
$repository->setPresenter("Litepie\\Repository\\Presenter\\ModelFractalPresenter");

//Getting the result transformed by the presenter directly in the search
$post = $repository->find(1);

print_r( $post ); //It produces an output as array

...

//Skip presenter and bringing the original result of the Model
$post = $repository->skipPresenter()->find(1);

print_r( $post ); //It produces an output as a Model object
print_r( $post->presenter() ); //It produces an output as array
```
You can skip the presenter at every visit and use it on demand directly into the model, for it set the ***$skipPresenter*** attribute to true in your repository:

```php
use Litepie\Repository\Eloquent\BaseRepository;

class PostRepository extends BaseRepository {

    /**
    * @var bool
    */
    protected $skipPresenter = true;

    public function presenter()
    {
        return "App\\Presenter\\PostPresenter";
    }
}
```
#### Model Class
**Implement Interface**

```php
namespace App;

use Litepie\Repository\Contracts\Transformable;

class Post extends Eloquent implements Transformable {
     ...
     /**
      * @return array
      */
     public function transform()
     {
         return [
             'id'      => (int) $this->id,
             'title'   => $this->title,
             'content' => $this->content
         ];
     }
}
```
**Enabling in your Repository**

```php
Litepie\Repository\Presenter\ModelFractalPresenter
```
This Presenter is available for all Models which iplements Transformable

```php
use Litepie\Repository\Eloquent\BaseRepository;

class PostRepository extends BaseRepository {

    ...

    public function presenter()
    {
        return "Litepie\\Repository\\Presenter\\ModelFractalPresenter";
    }
}
```
You can also enable it in your controller with

```php
$this->repository->setPresenter("Litepie\\Repository\\Presenter\\ModelFractalPresenter");
```
#### Skip Presenter defined in the repository
Use ***skipPresenter ***before any other chaining method

```php
$posts = $this->repository->skipPresenter()->all();
```
Or

```php
$this->repository->skipPresenter();

$posts = $this->repository->all();
```
