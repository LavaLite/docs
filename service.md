Laravel package that provides service management facility for lavalite CMS.

## Installation

Require this package with composer. 

    composer require litecms/service

Laravel 5.5 uses Package Auto-Discovery, so doesn't require you to manually add the ServiceProvider.

## Migration and seeds

    php artisan migrate
    php artisan db:seed --class=Litecms\\ServiceTableSeeder
    

## Publishing

**Configuration**

    php artisan vendor:publish --provider="Litecms\Service\Providers\ServiceServiceProvider" --tag="config"

**Language**

    php artisan vendor:publish --provider="Litecms\Service\Providers\ServiceServiceProvider" --tag="lang"

**Files**

    php artisan vendor:publish --provider="Litecms\Service\Providers\ServiceServiceProvider" --tag="storage"

### Views

Publish views to resources\views\vendor directory

    php artisan vendor:publish --provider="Litecms\Service\Providers\ServiceServiceProvider" --tag="view"

Publishes admin view to admin theme

    php artisan theme:publish --provider="Litecms\Service\Providers\ServiceServiceProvider" --view="admin" --theme="admin"

Publishes public view to public theme

    php artisan theme:publish --provider="Litecms\Service\Providers\ServiceServiceProvider" --view="public" --theme="public"