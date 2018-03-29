# Theming

- [Basics](#basics)
- [Structure](#structure)
- [Creation](#creation)
- [Customization](#customization)


<a name="basics"></a>
## Basics

Laravel comes with 4 basic themes `admin` `user` `client` and `public`. This document helps you to get to know more about how to build a new theme and how to implement on the website or application.

<a name="structure"></a>

## Structure
The theme is usually come under the folder `public/themes` and folder structure be like as follows. 

```
The theme is usually come under `public/themes`
theme-name (Theme root folder.)
	+ assets (This folder conatins all the assets for the theme.)
		+ css
		+ images
		+ dist (Folder for the compiled assets.)
		+ js
		+ sass
	+ layouts (Folder for the theme layots.)
	+ partials (Folder for the theme partials like header, aside etc.)
	+ views (View folder for the theme.)
		+ auth (Folder for login register blades.)
		+ user
		+ vendor (Published views.)
```
<a name="creation"></a>
## Creation

If you wish to build a new theme either you can build it from scratch or you can copy one of the inbuilt theme and modify the same. The [theme documentation](https://lavalite.org/docs/master/theme) helps you to understand more about the theme.

<a name="customization"></a>
## Customization

Once you created the theme you have to link it with the application. Linking can be done through the configuration file `config/theme.php`. Publishing the theme configuration file using `php artisan vendor:publish --provider="Litepie\Theme\ThemeServiceProvider" --tag="config"`. 

Once published you can link the theme to a particular type of user through theme configuration file.
```
    'themes'        => [
        'default' => [
            'theme'  => 'public',
            'view'   => 'public',
        ],
        
        ...
        'client'    => [
            'theme'  => 'client',
            'view'   => 'client',
        ],
        ....
        
    ],
```

Like this you can have multiple theme for multiple type of users in the same project. In this example `client` is the type of user and `'theme'  => 'client'` specifies which them is to be loaded for that user type `'view'  => 'client'` specifies which view is to be loaded from the package and if the view doesn't exists it will load view in the `default` directory.

### Publishing views

In lavalite  `view()` function look for the views in the folders in the below order.
```
public/path/to/current/theme/views/vendor/package-namespace
resources/views/vendor/package-namespace
path/to/package/view/user-type (eg: admin / client)
path/to/package/view/default
```
If you are planing to customize view of a package based on a thme you can publish the view using the command `php artisan theme:publish` and it will guide step by step in publishing the views to the theme.  Once get published you can directly jump into the the published folder to edit the files based on your theme.

> If you are  developing theme for re-distribution or sale publish the views of all packages like blog, contacts, pages etc to the theme and customize it.
