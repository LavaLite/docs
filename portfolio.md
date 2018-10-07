# Portfolio

Laravel package that provides portfolio management facility for lavalite CMS.

## Installation

Require this package with composer. 

    composer require litecms/portfolio

Laravel 5.5 uses Package Auto-Discovery, so doesn't require you to manually add the ServiceProvider.

## Migration and seeds

    php artisan migrate
    php artisan db:seed --class=Litecms\\PortfolioTableSeeder



## Publishing

**Configuration**

    php artisan vendor:publish --provider="Litecms\Portfolio\Providers\PortfolioServiceProvider" --tag="config"

**Language**

    php artisan vendor:publish --provider="Litecms\Portfolio\Providers\PortfolioServiceProvider" --tag="lang"

**Files**

    php artisan vendor:publish --provider="Litecms\Portfolio\Providers\PortfolioServiceProvider" --tag="storage"

### Views

Publish views to resources\views\vendor directory

    php artisan vendor:publish --provider="Litecms\Portfolio\Providers\PortfolioServiceProvider" --tag="view"

Publishes admin view to admin theme

    php artisan theme:publish --provider="Litecms\Portfolio\Providers\PortfolioServiceProvider" --view="admin" --theme="admin"

Publishes public view to public theme

    php artisan theme:publish --provider="Litecms\Portfolio\Providers\PortfolioServiceProvider" --view="public" --theme="public"