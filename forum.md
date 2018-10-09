# Forum

Laravel package that provides forum  facility for lavalite CMS.

## Installation

Require this package with composer. 

    composer require litecms/forum

Laravel 5.5 uses Package Auto-Discovery, so doesn't require you to manually add the ServiceProvider.

## Migration and seeds

    php artisan migrate
    php artisan db:seed --class=Litecms\\ForumTableSeeder


## Publishing

**Configuration**

    php artisan vendor:publish --provider="Litecms\Forum\Providers\ForumServiceProvider" --tag="config"

**Language**

    php artisan vendor:publish --provider="Litecms\Forum\Providers\ForumServiceProvider" --tag="lang"

**Files**

    php artisan vendor:publish --provider="Litecms\Forum\Providers\ForumServiceProvider" --tag="storage"

### Views

Publish views to resources\views\vendor directory

    php artisan vendor:publish --provider="Litecms\Forum\Providers\ForumServiceProvider" --tag="view"

Publishes admin view to admin theme

    php artisan theme:publish --provider="Litecms\Forum\Providers\ForumServiceProvider" --view="admin" --theme="admin"

Publishes public view to public theme

    php artisan theme:publish --provider="Litecms\Forum\Providers\ForumServiceProvider" --view="public" --theme="public"