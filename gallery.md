# Gallery

Laravel package that provides content gallery management facility for lavalite CMS.

## Installation

Require this package with composer. 

    composer require litecms/gallery

Laravel 5.5 uses Package Auto-Discovery, so doesn't require you to manually add the ServiceProvider.

## Migration and seeds

    php artisan migrate
    php artisan db:seed --class=Litecms\\GalleryTableSeeder


## Publishing

**Configuration**

    php artisan vendor:publish --provider="Litecms\Gallery\GalleryServiceProvider" --tag="config"

**Language**

    php artisan vendor:publish --provider="Litecms\Gallery\GalleryServiceProvider" --tag="lang"

**Files**

    php artisan vendor:publish --provider="Litecms\Gallery\GalleryServiceProvider" --tag="storage"

### Views

Publish views to resources\views\vendor directory

    php artisan vendor:publish --provider="Litecms\Gallery\GalleryServiceProvider" --tag="view"

Publishes admin view to admin theme

    php artisan theme:publish --provider="Litecms\Gallery\GalleryServiceProvider" --view="admin" --theme="admin"

Publishes public view to public theme

    php artisan theme:publish --provider="Litecms\Gallery\GalleryServiceProvider" --view="public" --theme="public"