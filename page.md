# Page

#### Manage pages for the CMS
This is a Laravel 4 package that provides multilingual page interface for lavalite framework.

## Installation

Begin by installing this package through Composer. Edit your project's `composer.json` file to require `lavalite/page`.

    "lavalite/page": "dev-master"

Next, update Composer from the Terminal:

    composer update

Once this operation completes, the final step is to add the service provider and page alias. Open `app/config/app.php`, and add a new item to the providers array.
##### PHP
```
'Lavalite\Page\PageServiceProvider'
```

And also add it to alias
##### PHP
```
'Page'  => 'Lavalite\Page\Facades\Page',
```

Use the below commands for publishing

Migration and seeds

    php artisan vendor:publish --provider="Lavalite\Page\Providers\PageServiceProvider" --tag="migrate"
    php artisan vendor:publish --provider="Lavalite\Page\Providers\PageServiceProvider" --tag="seeds"

Configuration

    php artisan vendor:publish --provider="Lavalite\Page\Providers\PageServiceProvider" --tag="config"

Views

    php artisan vendor:publish --provider="Lavalite\Page\Providers\PageServiceProvider" --tag="view-public"
    php artisan vendor:publish --provider="Lavalite\Page\Providers\PageServiceProvider" --tag="view-admin"


You are done!

## Usage

Add pages through `admin/pages`

Browser to get page browse `/{slug}.html`

Calling other pages inside a view or function
##### PHP
```
{{Page::heading('slug')}}
{{Page::content('slug')}}
{{Page::title('slug')}}
{{Page::keyword('slug')}}
{{Page::description('slug')}}
{{Page::banner('slug')}}
```
