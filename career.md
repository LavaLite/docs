Laravel package that provides career management facility for lavalite CMS.

## Installation

Require this package with composer. 

    composer require litecms/career

Laravel 5.5 uses Package Auto-Discovery, so doesn't require you to manually add the ServiceProvider.


## Publishing

**Configuration**

    php artisan vendor:publish --provider="Litecms\Career\Providers\CareerServiceProvider" --tag="config"

**Language**

    php artisan vendor:publish --provider="Litecms\Career\Providers\CareerServiceProvider" --tag="lang"

**Files**

    php artisan vendor:publish --provider="Litecms\Career\Providers\CareerServiceProvider" --tag="storage"

### Views

Publish views to resources\views\vendor directory

    php artisan vendor:publish --provider="Litecms\Career\Providers\CareerServiceProvider" --tag="view"

Publishes admin view to admin theme

    php artisan theme:publish --provider="Litecms\Career\Providers\CareerServiceProvider" --view="admin" --theme="admin"

Publishes public view to public theme

    php artisan theme:publish --provider="Litecms\Career\Providers\CareerServiceProvider" --view="public" --theme="public"