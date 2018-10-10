# Product

Laravel package that provides product management facility for lavalite CMS.

## Installation

Require this package with composer. 

    composer require litecms/product

Laravel 5.5 uses Package Auto-Discovery, so doesn't require you to manually add the ServiceProvider.


## Migration and seeds

    php artisan migrate
    php artisan db:seed --class=Litecms\\ProductTableSeeder
    
## Publishing

**Configuration**

    php artisan vendor:publish --provider="Litecms\Product\Providers\ProductServiceProvider" --tag="config"

**Language**

    php artisan vendor:publish --provider="Litecms\Product\Providers\ProductServiceProvider" --tag="lang"

**Files**

    php artisan vendor:publish --provider="Litecms\Product\Providers\ProductServiceProvider" --tag="storage"

### Views

Publish views to resources\views\vendor directory

    php artisan vendor:publish --provider="Litecms\Product\Providers\ProductServiceProvider" --tag="view"

Publishes admin view to admin theme

    php artisan theme:publish --provider="Litecms\Product\Providers\ProductServiceProvider" --view="admin" --theme="admin"

Publishes public view to public theme

    php artisan theme:publish --provider="Litecms\Product\Providers\ProductServiceProvider" --view="public" --theme="public"