#### USAGE
Laravel Localization uses the URL given for the request.  In order to achieve this purpose, a route group should be added into the `routes.php` file. It will filter all pages that must be localized.

	// app/Http/routes.php
	Route::group(['prefix' => trans_setlocale()], function()
	{
		/** ADD ALL LOCALIZED ROUTES INSIDE THIS GROUP **/
		Route::get('/', function()
		{
				return View::make('hello');
		});

		Route::get('test',function(){
				return View::make('test');
		});
	});

#OTHER PAGES THAT SHOULD NOT BE LOCALIZED 

Once this route group is added to the routes file, a user can access all locales added into `supportedLocales` ('en' and 'es' by default, look at the config section to change that option). For example, a user can now access two different locales, using the following addresses:

	http://url-to-laravel/en
	http://url-to-laravel/es
	http://url-to-laravel

If the locale is not present in the url or it is not defined in `supportedLocales`, the system will use the application default locale or the user's browser default locale (if defined in config file). Once the locale is defined, the locale variable will be stored in a session (if the middleware is enabled), so it is not necessary to write the /lang/ section in the url after defining it once, using the last known locale for the user. If the user accesses to a different locale this session value would be changed, translating any other page he visits with the last chosen locale. Template files and all locale files should follow the Lang class.

#### MIDDLEWARE
Moreover, this package includes a middleware object to redirect all "non-localized" routes to the corresponding "localized". If a user navigates to http://url-to-laravel/test and the system has this middleware active and 'en' as the current locale for this user, it would redirect (301) him automatically to http://url-to-laravel/en/test. This is mainly used to avoid duplicate content and improve SEO performance.

You have to register the middleware in the `app/Http/Kernel.php` file like this:

	namespace App\Http;

	use Illuminate\Foundation\Http\Kernel as HttpKernel;

	class Kernel extends HttpKernel {
		/**
		 * The application's route middleware.
		 *
		 * @var array
		 */
		protected $routeMiddleware = [
			/` OTHER MIDDLEWARE `/
			'localize' => \Litepie\Trans\Middleware\TransRoutes::class,
			'localizationRedirect' => \Litepie\Trans\Middleware\TransRedirectFilter::class,
			'localeSessionRedirect' => \Litepie\Trans\Middleware\LocaleSessionRedirect::class
			// REDIRECTION MIDDLEWARE
		];
	}

		..
	// app/Http/routes.php

	Route::group(
	[
		'prefix' => trans_setlocale(),
		'middleware' => [ 'localeSessionRedirect', 'localizationRedirect' ]
	],
	function()
	{
		/** ADD ALL LOCALIZED ROUTES INSIDE THIS GROUP **/
		Route::get('/', function()
		{
				return View::make('hello');
		});

		Route::get('test',function(){
				return View::make('test');
		});
	});

	/** OTHER PAGES THAT SHOULD NOT BE LOCALIZED **/

In order to activate it, you just have to attach this middleware to the routes you want to be accessible localized.

If you want to hide the default locale but always show other locales in the url, switch the `hideDefaultLocaleInURL` config value to true. Once it's true, if the default locale is en (english) all URLs containing /en/ would be redirected to the same url without this fragment '/' but maintaining the locale as en (English).

**IMPORTANT** - When `hideDefaultLocaleInURL` is set to true, the unlocalized root is treated as the applications default locale `app.locale`. Because of this language negotiation using the Accept-Language header will NEVER occur when `hideDefaultLocaleInURL` is true.

#### HELPERS
This package comes with some useful functions, like:

**Get URL for an specific locale**

	 /**
	 * Returns an URL adapted to $locale
	 *
	 * @param  string|boolean   $locale     Locale to adapt, false to remove locale
	 * @param  string|false     $url        URL to adapt in the current language. If not passed, the current url would be taken.
	 * @param  array            $attributes Attributes to add to the route, if empty, the system would try to extract them from the url.
	 *
	 * @throws UnsupportedLocaleException
	 *
	 * @return string|false             URL translated, False if url does not exist
	 */
	public function getLocalizedURL($locale = null, $url = null, $attributes = array())

	//Should be called in a view like this:
	{{ Trans::getLocalizedURL(optional string $locale, optional string $url, optional array $attributes) }}

Helper function is available for easy access

	trans_url( optional string $url, optional string $locale, optional array $attributes)

It returns a URL localized to the desired locale.


**Get Clean routes**

	 /**
	 * It returns an URL without locale (if it has it)
	 * Convenience function wrapping getLocalizedURL(false)
	 *
	 * @param  string|false     $url      URL to clean, if false, current url would be taken
	 *
	 * @return string          URL with no locale in path
	 */
	public function getNonLocalizedURL($url = null)

	//Should be called in a view like this:
	{{ Trans::getNonLocalizedURL(optional string $url) }}

It returns a URL clean of any localization.

**Get URL for an specific translation key** 

	/**
	 * Returns an URL adapted to the route name and the locale given
	 *
	 * @throws UnsupportedLocaleException
	 *
	 * @param  string|boolean   $locale             Locale to adapt
	 * @param  string           $transKeyName       Translation key name of the url to adapt
	 * @param  array            $attributes         Attributes for the route (only needed if transKeyName needs them)
	 *
	 * @return string|false     URL translated
	 */
	public function getURLFromRouteNameTranslated($locale, $transKeyName, $attributes = array())

	//Should be called in a view like this:
	{{ Trans::getURLFromRouteNameTranslated(string $locale, optional array $transKeyNames, optional array $attributes) }}

It returns a route, localized to the desired locale using the locale passed. If the translation key does not exist in the locale given, this function will return false.

**Get Supported Locales** 


	 /**
	 * Return an array of all supported Locales
	 *
	 * @return array
	 */
	 public function getSupportedLocales()

	//Should be called like this:
	{{ Trans::getSupportedLocales() }}

This function will return all supported locales and their properties as an array.

**Get Supported Locales Keys** 

	/**
	 * Returns supported languages language key
	 *
	 * @return array    keys of supported languages
	 */
	public function getSupportedLanguagesKeys()

	//Should be called like this:
	{{ Trans::getSupportedLanguagesKeys() }}

This function will return an array with all the keys for the supported locales.

**Set Locale** 


	/**
	 * Set and return current locale
	 *
	 * @param  string $locale           Locale to set the App to (optional)
	 *
	 * @return string                   Returns locale (if route has any) or null (if route does not have a locale)
	 */
	public function setLocale($locale = null)

	//Should be called in a view like this:
	{{ trans_setlocale(optional string $locale) }}

This function will change the application's current locale. If the locale is not passed, the locale will be determined via a cookie (if stored previously), the session (if stored previously), browser Accept-Language header or the default application locale (depending on your config file).

The function has to be called in the prefix of any route that should be translated (see Filters sections for further information).

**Get Current Locale** 


	/**
	 * Returns current language
	 *
	 * @return string current language
	 */
	public function getCurrentLocale()

	//Should be called in a view like this:
	{{ Trans::getCurrentLocale() }}

This function will return the key of the current locale.

**Get Current Locale Name** 

	/**
	 * Returns current locale name
	 *
	 * @return string current locale name
	 */
	public function getCurrentLocaleName()

	//Should be called in a view like this:
	{{ Trans::getCurrentLocaleName() }}

This function will return current locale's name as string (English/Spanish/Arabic/ ..etc).

**Get Current Locale Direction** 

	/**
	 * Returns current locale direction
	 *
	 * @return string current locale direction
	 */
	public function getCurrentLocaleDirection()

	//Should be called in a view like this:
	{{ Trans::getCurrentLocaleDirection() }}

This function will return current locale's direction as string (ltr/rtl).

**Get Current Locale Script** 


	/**
	 * Returns current locale script
	 *
	 * @return string current locale script
	 */
	public function getCurrentLocaleScript()

	//Should be called in a view like this:
	{{ Trans::getCurrentLocaleScript() }}

This function will return the ISO 15924 code for the current locale script as a string; "Latn", "Cyrl", "Arab", etc.

**Creating a language selector** 

If you are supporting multiple locales in your project you will probably want to provide the users with a way to change language. Below is a simple example of blade template code you can use to create your own language selector.

	<ul class="language_bar_chooser">
		@foreach(Trans::getSupportedLocales() as $localeCode => $properties)
			<li>
				<a rel="alternate" hreflang="{{$localeCode}}" href="{{Trans::getLocalizedURL($localeCode) }}">
						{{{ $properties['native'] }}}
				</a>
			</li>
		@endforeach
	</ul>

**Translated Routes**

You can adapt your URLs depending on the language you want to show them. For example, http://url/en/about and http://url/es/acerca (acerca is about in spanish) or http://url/en/view/5 and http://url/es/ver/5 (view == ver in spanish) would be redirected to the same controller using the proper filter and setting up the translation files as follows:

As it is a middleware, first you have to register in on your **app/Http/Kernel.php** file like this:

	namespace App\Http;

	use Illuminate\Foundation\Http\Kernel as HttpKernel;

	class Kernel extends HttpKernel {
		/**
		 * The application's route middleware.
		 *
		 * @var array
		 */
		protected $routeMiddleware = [
				/` OTHER MIDDLEWARE `/
				'localize' => 'Litepie\Trans\Middleware\TransRoutes',
				// TRANSLATE ROUTES MIDDLEWARE
		];

	}

	// app/Http/routes.php

	Route::group(
	[
			'prefix' => trans_setlocale(),
			'middleware' => [ 'localize' ] // Route translate middleware
	],
	function()
	{
		/** ADD ALL LOCALIZED ROUTES INSIDE THIS GROUP **/
		Route::get('/', function()
		{
			// This routes is useless to translate
			return View::make('hello');
		});

		Route::get(Trans::transRoute('routes.about'),function(){
				return View::make('about');
		});
		Route::get(Trans::transRoute('routes.view'),function($id){
				return View::make('view',['id'=>$id]);
		});
	});

	/** OTHER PAGES THAT SHOULD NOT BE LOCALIZED **/


In the routes file you just have to add the `TransRoutes` filter and the `Trans::transRoute` function to every route you want to translate using the translation key.

Then you have to create the translation files and add there every key you want to translate. I suggest to create a routes.php file inside your resources/lang/language_abbreviation folder. For the previous example, I have created two translations files, these two files would look like:


	// resources/lang/en/routes.php
	return [
		"about"       =>  "about",
		"view"        =>  "view/{id}", //we add a route parameter
		// other translated routes
	];

		// resources/lang/es/routes.php
	return [
		"about"       =>  "acerca",
		"view"        =>  "ver/{id}", //we add a route parameter
		// other translated routes
	];

Once files are saved, you can access the following without any problem  

	* http://url/en/about , 
	* http://url/es/acerca , 
	* http://url/en/view/5 
	* http://url/es/ver/5 

The getLanguageBar function would work as desired and it will translate the routes to all translated languages (don't forget to add any new route to the translation file).

#### EVENTS

You can capture the URL parameters during translation if you wish to translate them too. To do so, just create an event listener for the `routes.translation` event like so

	Event::listen('routes.translation', function($locale, $attributes)
	{
			// Do your magic

			return $attributes;
	});
	
Be sure to pass the locale and the attributes as parameters to the closure. You may also use Event Subscribers, see: http://laravel.com/docs/events#event-subscribers

####  Config Files

In order to edit the default configuration for this package you may edit `config/tarns.php.` Inside this file you will find all the fields that can be configured.



