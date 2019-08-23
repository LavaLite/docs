# Installation

- [Installation](#installation)
    - [Installation](#install-lavalite)
    - [Setup](#install-setup)
    - [Login ](#install-login)
- [Configuration](#configuration)
    - [Basic Configuration](#basic-configuration)
    - [Environment Configuration](#environment-configuration)
    - [Configuration Caching](#configuration-caching)
    - [Accessing Configuration Values](#accessing-configuration-values)
    - [Naming Your Application](#naming-your-application)
- [Maintenance Mode](#maintenance-mode)

<a name="installation"></a>
## Installation

### Server Requirements

The Lavalite framework has a few system requirements. 

    - PHP >= 7.1
    - OpenSSL PHP Extension
    - PDO PHP Extension
    - Mbstring PHP Extension
    - Tokenizer PHP Extension
    - Fileinfo PHP Extension
    - GD Library
    - Imagick PHP Extension

<a name="install-lavalite"></a>
### Installing Lavalite

Lavalite utilizes [Composer](http://getcomposer.org) to manage its dependencies. So, before using Lavalite, make sure you have Composer installed on your machine.
#### Composer Create-Project

You may also install Lavalite by issuing the Composer `create-project` command in your terminal:

    composer create-project LavaLite/cms --prefer-dist website
    
<a name="install-setup"></a>
#### Setup 
After creating the project move to the project root folder eg: `cd website` and run the command to set up database and configuration files. 
```php
php artisan lavalite:install

php artisan key:generate // run this command if key not generated while installing.
```
After installation run the command `php artisan serve` or browse the link to view the homepage.
```
http://path-to-route-folder/public
```
    
<a name="install-login"></a>
#### Login details

**Administrator**

```
http://path-to-route-folder/public/admin
```
You can use the email id and password given at the time of Lavalite installation to login as Superuser. Superuser can manage all other type of users in the application. Lavalite come with two type of users `user` and `client`. The default login credential of `user` and `client` is as below.

**User**

```
http://path-to-route-folder/public/user
User:     user@lavalite.org 
Password: user@lavalite
```

**Client**

```
http://path-to-route-folder/public/client
User:     client@lavalite.org 
Password: client@lavalite
```

<a name="configuration"></a>
## Configuration

<a name="basic-configuration"></a>
### Basic Configuration

All of the configuration files for the Lavalite framework are stored in the `config` directory. Each option is documented, so feel free to look through the files and get familiar with the options available to you.

#### Directory Permissions

After installing Lavalite, you may need to configure some permissions. Directories within the `storage` and the `bootstrap/cache` directories should be writable by your web server. If you are using the [Homestead](/docs/{{version}}/homestead) virtual machine, these permissions should already be set.

#### Application Key

The next thing you should do after installing Lavalite is set your application key to a random string. If you installed Lavalite via Composer or the Lavalite installer, this key has already been set for you by the `key:generate` command. Typically, this string should be 32 characters long. The key can be set in the `.env` environment file. If you have not renamed the `.env.example` file to `.env`, you should do that now. **If the application key is not set, your user sessions and other encrypted data will not be secure!**

#### Additional Configuration

Lavalite needs almost no other configuration out of the box. You are free to get started developing! However, you may wish to review the `config/app.php` file and its documentation. It contains several options such as `timezone` and `locale` that you may wish to change according to your application.

You may also want to configure a few additional components of Lavalite, such as:

- [Cache](/docs/{{version}}/cache#configuration)
- [Database](/docs/{{version}}/database#configuration)
- [Session](/docs/{{version}}/session#configuration)

Once Lavalite is installed, you should also [configure your local environment](/docs/{{version}}/installation#environment-configuration).

<a name="pretty-urls"></a>
#### Pretty URLs

**Apache**

The framework ships with a `public/.htaccess` file that is used to allow URLs without `index.php`. If you use Apache to serve your Lavalite application, be sure to enable the `mod_rewrite` module.

If the `.htaccess` file that ships with Lavalite does not work with your Apache installation, try this one:

    Options +FollowSymLinks
    RewriteEngine On

    RewriteCond %{REQUEST_FILENAME} !-d
    RewriteCond %{REQUEST_FILENAME} !-f
    RewriteRule ^ index.php [L]

**Nginx**

On Nginx, the following directive in your site configuration will allow "pretty" URLs:

    location / {
        try_files $uri $uri/ /index.php?$query_string;
    }

Of course, when using [Homestead](/docs/{{version}}/homestead), pretty URLs will be configured automatically.

<a name="environment-configuration"></a>
### Environment Configuration

It is often helpful to have different configuration values based on the environment the application is running in. For example, you may wish to use a different cache driver locally than you do on your production server. It's easy using environment based configuration.

To make this a cinch, Lavalite utilizes the [DotEnv](https://github.com/vlucas/phpdotenv) PHP library by Vance Lucas. In a fresh Lavalite installation, the root directory of your application will contain a `.env.example` file. If you install Lavalite via Composer, this file will automatically be renamed to `.env`. Otherwise, you should rename the file manually.

All of the variables listed in this file will be loaded into the `$_ENV` PHP super-global when your application receives a request. You may use the `env` helper to retrieve values from these variables. In fact, if you review the Lavalite configuration files, you will notice several of the options already using this helper!

Feel free to modify your environment variables as needed for your own local server, as well as your production environment. However, your `.env` file should not be committed to your application's source control, since each developer / server using your application could require a different environment configuration.

If you are developing with a team, you may wish to continue including a `.env.example` file with your application. By putting place-holder values in the example configuration file, other developers on your team can clearly see which environment variables are needed to run your application.

#### Accessing The Current Application Environment

The current application environment is determined via the `APP_ENV` variable from your `.env` file. You may access this value via the `environment` method on the `App` [facade](/docs/{{version}}/facades):

    $environment = App::environment();

You may also pass arguments to the `environment` method to check if the environment matches a given value. You may even pass multiple values if necessary:

    if (App::environment('local')) {
        // The environment is local
    }

    if (App::environment('local', 'staging')) {
        // The environment is either local OR staging...
    }

An application instance may also be accessed via the `app` helper method:

    $environment = app()->environment();

<a name="configuration-caching"></a>
### Configuration Caching

To give your application a speed boost, you should cache all of your configuration files into a single file using the `config:cache` Artisan command. This will combine all of the configuration options for your application into a single file which can be loaded quickly by the framework.

You should typically run the `php artisan config:cache` command as part of your production deployment routine. The command should not be run during local development as configuration options will frequently need to be changed during the course of your application's development.

<a name="accessing-configuration-values"></a>
### Accessing Configuration Values

You may easily access your configuration values using the global `config` helper function. The configuration values may be accessed using "dot" syntax, which includes the name of the file and option you wish to access. A default value may also be specified and will be returned if the configuration option does not exist:

    $value = config('app.timezone');

To set configuration values at runtime, pass an array to the `config` helper:

    config(['app.timezone' => 'America/Chicago']);

<a name="naming-your-application"></a>
### Naming Your Application

After installing Lavalite, you may wish to "name" your application. By default, the `app` directory is namespaced under `App`, and autoloaded by Composer using the [PSR-4 autoloading standard](http://www.php-fig.org/psr/psr-4/). However, you may change the namespace to match the name of your application, which you can easily do via the `app:name` Artisan command.

For example, if your application is named "Horsefly", you could run the following command from the root of your installation:

    php artisan app:name Horsefly

Renaming your application is entirely optional, and you are free to keep the `App` namespace if you wish.

<a name="maintenance-mode"></a>
## Maintenance Mode

When your application is in maintenance mode, a custom view will be displayed for all requests into your application. This makes it easy to "disable" your application while it is updating or when you are performing maintenance. A maintenance mode check is included in the default middleware stack for your application. If the application is in maintenance mode, an `HttpException` will be thrown with a status code of 503.

To enable maintenance mode, simply execute the `down` Artisan command:

    php artisan down

To disable maintenance mode, use the `up` command:

    php artisan up

### Maintenance Mode Response Template

The default template for maintenance mode responses is located in `resources/views/errors/503.blade.php`.

### Maintenance Mode & Queues

While your application is in maintenance mode, no [queued jobs](/docs/{{version}}/queues) will be handled. The jobs will continue to be handled as normal once the application is out of maintenance mode.
