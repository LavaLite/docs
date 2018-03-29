# Packages

- [Creation](#creation)
- [Structure](#structure)
- [Installation](#installation)
- [Customization](#customization)


<a name="creation"></a>
## Creation

Package creation is easy task in Lavalite, our [package builder](http://lavalite.org/package/create) helps you to build all basic files required for the package. The beautiful package building tool helps you to complete almost 60% of your coding task in just few hours. The more time you spend on the package builder will reduce the coding time for the entire project.

<a name="structure"></a>
## Structure
Here is the basic folder structure for a lavalite package. Based on other functionalities you select it will generate additional codes and folders in the package.
<pre>
 Root - <i>Package root folder.</i>
 	+ config - <i>Folder for configuration files.</i>
    + database - <i>Folder for migrations & seeds.</i>
        + migrations
        + seeds
    + public - <i>Folder for files to be publish to the public folder.</i>
    + resources
        + lang - <i>Folder for language files.</i>
            + en
        + views - <i>Views for different type of users.</i>
            + admin
            + public
            + default
    + routes - <i>Route files</i>
    + src  - <i>Contains all core programs.</i>
        + Facades
        + Http
        	+ Controllers
        	+ Request
        	+ Response 
        + Interfaces
        + Models
        + Policies
        + Providers
        + Repositories
            + Criteria
            + Eloquent
            + Presenter
    + tests - <i>Test files</i>
</pre>

<a name="installation"></a>
## Installation
Once downloaded the packages form the package builder extract the package to the `packages` folder in the project and add the following line of code in the `composer.json` file.

```
"psr-4": {
    ...
    "Provider\\Package\\": "packages/provider/Package/src",
    ...
},
"files": [
    ...
    // add this line of code if you have added any helper function for the package
    "packages/provider/Package/src/helpers.php"
    ...
]
 ```
Run `composer auto-load` command in the command line to add the package class to composer class map. Once it is done add service provider and facades to the `config/app.php` to link the package to the project.

```
'providers'       => [
      ...
      Provider\Package\Providers\PackageServiceProvider::class,
      ...
],

'aliases'         => [
      ...
      'Package'         => Provider\Package\Facades\Package::class,
      ...
],
```

### Publishing files
If you want to customize the configurations, languages, views and public files publish the files to respective folders using artisan command.

```
php artisan vendor:publish --provider="Provider\Package\Providers\PackageServiceProvider" --tag="config"
php artisan vendor:publish --provider="Provider\Package\Providers\PackageServiceProvider" --tag="lang"
php artisan vendor:publish --provider="Provider\Package\Providers\PackageServiceProvider" --tag="view"
php artisan vendor:publish --provider="Provider\Package\Providers\PackageServiceProvider" --tag="public"
```

### Migration and Seeds
Once package is registers with service provider you can use `php artisan migrate` command to migrate the database with the tables. And use `php artisan db:seed --class=PackageTableSeeder` to seed the tables with the default data if required.

> Replace `Provider` with actual provider name and `Package` with actual package name in all code samples. For more information about the package development view the [Package Development](https://laravel.com/docs/master/packages)  documentation in laravel.com.

<a name="customization"></a>
## Customization

Once the package is downloaded you have to customize the package according to the requirement. You can make you of our slack channel for clearing your doubts on package customization.
