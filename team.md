Laravel package that provides team management facility for lavalite CMS.

## Installation

Require this package with composer. 

    composer require litecms/team

Laravel 5.5 uses Package Auto-Discovery, so doesn't require you to manually add the ServiceProvider.

## Migration and seeds

    php artisan migrate
    php artisan db:seed --class=Litecms\\TeamTableSeeder
    
## Publishing

**Configuration**

    php artisan vendor:publish --provider="Litecms\Team\Providers\TeamServiceProvider" --tag="config"

**Language**

    php artisan vendor:publish --provider="Litecms\Team\Providers\TeamServiceProvider" --tag="lang"

**Files**

    php artisan vendor:publish --provider="Litecms\Team\Providers\TeamServiceProvider" --tag="storage"

### Views

Publish views to resources\views\vendor directory

    php artisan vendor:publish --provider="Litecms\Team\Providers\TeamServiceProvider" --tag="view"

Publishes admin view to admin theme

    php artisan theme:publish --provider="Litecms\Team\Providers\TeamServiceProvider" --view="admin" --theme="admin"

Publishes public view to public theme

    php artisan theme:publish --provider="Litecms\Team\Providers\TeamServiceProvider" --view="public" --theme="public"