# Form

Form is us an extension of [Fromer](http://formers.github.io/former/).

## Table of Contents  
- [Getting Statted](#getting-started)
- [Bootstrap and Foundation Integration](#out-of-the-box-integration-to-bootstrap-and-foundation)
  - [Custom Framework](#custom-framework)
- [Ties-in with Laravel's Validator](#ties-in-with-laravel-s-validator)  
- [Form Populating](#form-populating)  
- [Datalists](#datalists)  
- [Checkboxes and radios](#checkboxes-and-radios ) 
- [Localization helpers](#localization-helpers) 
- [Form value precedence](#form-value-precedence) 

## Getting Started
```php
<?= Form::open()->method('GET') ?>
	<?= Form::text('name')->required() ?>
<?= Form::close() ?>
```

If you're using Twig or other more closed view templating systems, you can still use Form's classes outside:

```php
$form = (string)Form::open()->method('GET');
	$form .= Form::text('name')->required();
$form .= Form::close();
```

Form uses a fluent interface for building up form objects. Form objects don't actually function until they are cast to a string or `->render()`'d.

## Out-of-the-box integration to Bootstrap and Foundation

Form's power lies in outputting CSS-framework HTML automatically. Form recognizes when you create an horizontal or vertical form, and wraps each field in a control group behind the scenes.

That means that when you type:

```php
Form::select('clients')->options($clients, 2)
  ->help('Pick some dude')
  ->state('warning')
```

What you actually get is (with Bootstrap):

```html
<div class="control-group warning">
  <label for="clients" class="control-label">Clients</label>
  <div class="controls">
    <select id="clients" name="clients">
      <option value="0">Mickael</option>
      <option value="1">Joseph</option>
      <option value="2" selected="selected">Patrick</option>
    </select>
    <span class="help-inline">Pick some dude</span>
  </div>
</div>
```

By default Form will use Twitter Bootstrap for its syntax but you can select which framework to use with `Form::framework()`. For the moment Form supports `'TwitterBootstrap3'`, `'ZurbFoundation'` and `'Nude'` (for no framework). 

```php
// Turn off Bootstrap syntax
Form::framework('Nude');

// Turn it on again
Form::framework('TwitterBootstrap3');
```

Here is an example for Foundation:

```php
Form::framework('ZurbFoundation');

Form::four_text('foo')->state('error')->help('bar')
```

Outputs:

```html
<div class="error">
  <label for="foo">Foo</label>
  <input class="four" type="text" name="foo" id="foo">
  <small>Bar</small>
</div>
```
### Custom Framework

You may also implement your own framework/way of doing this by setting a custom class for `Form::framework()` Like so:

```php
// Custom framework
Form::framework('CustomNameSpace\YourFramework');
```

## Ties-in with Laravel's Validator

Form provides a magic helper `withErrors`; if set, it checks for any errors that field might have, and sets the error as an `.help-inline`.

You may need to use Form differently according to how your code reacts after a failed validation:

### If your render a view on failed validation (no redirection)
```php
if ($validation->fails()) {
  Form::withErrors($validation);
  return View::make('myview');
}
```

### If your redirect on failed validation
```php
if ($validation->fails()) {
  return Redirect::to('login')
    ->withErrors($validation);
}
```

Note that on the last example you never actually call Form, but Form will automatically detect the form errors from `Input::old()` based on the name of your fields.

To provide help text that is not overwritten with an error message, use `{!! Form::text('foo')->blockHelp('Help text') !!}`. Form only overrides an element's `->inlineHelp()` with the element's error message.

You can disable Form's automatic error fetching in the config or with: `Form::config('fetch_errors', false)`

## Form populating

You can populate a form with the `Form::populate` function, either by the usual passing of an array of values, like this:

```php
// Will populate the field 'name' with the value 'value'
Form::populate( array('name' => 'value') )
```

or by passing an Eloquent model:

```php
Form::populate( Client::find(2) )
```

Form will recognize the model and populate the field with the model's attribute. If here per example our client has a `name` set to 'Foo' and a `firstname` set to 'Bar', Form will look for fields named 'name' and 'firstname' and fill them respectively with 'Foo' and 'Bar'.

Alternatively you can also populate a specific field after you've populated the whole form (for a relationship per example) by doing this:

```php
Form::populate($project)

Form::populateField('client', $project->client->name)
```

For the rest of the form, filling fields is basically as easy as doing `->value('something')`.

You can also use the results from an Eloquent/Fluent query as options for a select, like this:

```php
Form::select('foo')->fromQuery(Client::all(), 'name', 'id')
```

Where the second argument is which attribute will be used for the option's text, and the third argument is which attribute will be used for the option's value (defaults to the `id` attribute).

If you don't specify the second or third arguments, Form will call `__toString()` on each model to use as the value:

```php
class Client extends Eloquent
{
  public static $key = 'code';

  public function __toString()
  {
    return $this->name;
  }
}

Form::select('clients')->fromQuery(Client::all());
```

This will use the `code` field for each option's value, and the Client's `name` field as each option's label.

Form is also able to populate fields with relationships:

```php
Form::populate(Client::find(2))

// Will populate with $client->name
Form::text('name')

// Will populate with $client->store->name
Form::text('store.name')

// You can go as deep as you need to
Form::text('customer.name.address')

// Will populate with the date from all of the client's reservations
Form::select('reservations.date')

// Which is the same as this ^
Form::select('reservations')->fromQuery($client->reservations, 'date')

// If you're using a text and not a select, instead of listing the
// relationship's models as options, it wil concatenate them
Form::text('customers.name') // Will display "name, name, name"

// You can rename a field afterwards for easier Input handling
Form::text('comment.title')->name('title')
```

Kudos to [cviebrock](https://github.com/cviebrock) for the original idea.

## HTML5 validation

Modern browsers support instant validation via HTML attributes — no Javascript needed nor script nor polyfill. There are a few attributes that can do that kind of job for you, `pattern`, `required`, `max/min` to name a few.

Form uses a `->rules()` function to specify which HTML5 live validation rules to use:

```php
Form::open()->rules(array(
  'name'     => 'required|max:20|alpha',
  'age'      => 'between:18,24',
  'email'    => 'email',
  'show'     => 'in:batman,spiderman',
  'random'   => 'match:/[a-zA-Z]+/',
  'birthday' => 'before:1968-12-03',
  'avatar'   => 'image',
));
```

This would output:

```html
<input name="name"      type="text"   required maxlength="20" pattern="[a-zA-Z]+" />
<input name="age"       type="number" min="18" max="24" />
<input name="email"     type="email" />
<input name="show"      type="text"   pattern="^(batman|spiderman)$" />
<input name="random"    type="text"   pattern="[a-zA-Z]+" />
<input name="birthday"  type="date"   max="1968-12-03" />
<input name="avatar"    type="file"   accept="image/jpeg,image/png,image/gif,image/bmp" />
```

Note that you can always add custom rules the way you'd add any attributes.

```php
Form::number('age')->min(18)

Form::text('client_code')->pattern('[a-z]{4}[0-9]{2}')
```

You can also manually set the error state of a control group if you're using Bootstrap or Foundation. You can use any of the control group states which include `success`, `warning`, `error` and `info`.

```php
Form::text('name')->state('error')
```
## Files handling

In Form like in Laravel you can create a simple file field with `Form::file`. What's new, is you can also create a multiple files field by calling `Form::files` which which will generate `<input type="file" name="foo[]" multiple />`.

One of the special method is the `->accept()` with which you can do the following:

```php
// Use a shortcut (image, video or audio)
Form::files('avatar')->accept('image')

// Use an extension which will be converted to MIME by Laravel
Form::files('avatar')->accept('gif', 'jpg')

// Or directly use a MIME
Form::files('avatar')->accept('image/jpeg', 'image/png')
```

You can also set a maximum size easily by using either bits or bytes

```php
Form::file('foo')->max(2, 'MB')
Form::file('foo')->max(400, 'Kb')
Form::file('foo')->max(1, 'TB')
```

This will create an hidden `MAX_FILE_SIZE` field with the correct value in bytes.

## Checkboxes and Radios

Form has support for checkbox and radio groups as well:

```php
// Create a one-off checkbox
Form::checkbox('checkme')

// Create a one-off checkbox with a text, and check it
Form::checkbox('checkme')
  ->text('YO CHECK THIS OUT')
  ->check()

// Create four related checkboxes
Form::checkboxes('checkme')
  ->checkboxes('first', 'second', 'third', 'fourth')

// Create related checkboxes, and inline them
Form::checkboxes('checkme')
  ->checkboxes($checkboxes)->inline()

// Everything that works on a checkbox also works on a radio element
Form::radios('radio')
  ->radios(array('label' => 'name', 'label' => 'name'))
  ->stacked()

// Stacked and inline can also be called as magic methods
Form::inline_checkboxes('foo')->checkboxes('foo', 'bar')
Form::stacked_radios('foo')->radios('foo', 'bar')

// Set which checkables are checked or not in one move
Form::checkboxes('level')
  ->checkboxes(0, 1, 2)
  ->check(array('level_0' => true, 'level_1' => false, 'level_2' => true))

// Fine tune checkable elements
Form::radios('radio')
  ->radios(array(
    'label' => array('name' => 'foo', 'value' => 'bar', 'data-foo' => 'bar'),
    'label' => array('name' => 'foo', 'value' => 'bar', 'data-foo' => 'bar'),
  ))
```

**Important:** Form gives you an option to force-pushing of checkboxes. Because browsers do not submit unchecked checkboxes with their POST data, you will likely want to force-push an empty value for unchecked checkboxes.
You can change what value an unchecked checkbox possesses in the POST array via the `unchecked_value` option. This will create a hidden input above each checkbox with the same name as the checkbox, and set its value to the `unchecked_value`.

## Localization helpers

By default, when creating a field, if no label is specified Form will use the field name by default. But Form also tries to translate it automatically. This also applies to checkboxes labels, help texts and form legends. Which means the following:

```php
// This
Form::label(__('validation.attributes.name'))
Form::text('name', __('validation.attributes.name'))
Form::text('name')->inlineHelp(__('help.name'))
Form::checkbox('rules')->text(__('my.translation'))
<legend>{{ __('validation.attributes.mylegend') }}</legend>

// Is the same as this
Form::label('name')
Form::text('name')
Form::text('name')->inlineHelp('help.name')
Form::checkbox('rules')->text('my.translation')
Form::legend('mylegend')
```

Form will first try to translate the string in itself, ie `my.text` will return `__('my.text')` and if that fails, it will look for it in a fallback place. You can set where Form look for translations by changing the following variable: `Form::config('translate_from', [boolean])` (defaults to `validation.attributes`). Note that **it must be an array**.

## Form value precedence

To populate your field, Form set the following priorities to found values:

* The `->forceValue()` method wins over everything – it's a special branch of `->value()` created to force a value in a field no matter what happens
* Next is POST data – if a user just typed something in a field, chances are this *is* what they want to see in it next time
* Then any values set via `Form::text()->populate()`, so you can override any global population on a per-element basis.
* Then any values set via `Form::populate()` – that means that if you're editing something or are repopulating with some value, it can be overwritten with `forceValue`
* Finally the classic `->value()` gets the least priority – it is created for minimum and default field values and thus gets overwritten by population and POST data

## Form element types

### Open
```php
    <?=
        Form::open([
           'url' => 'foo/bar',
           'method' => 'put',
           'route' => 'route.name',
           'action' => 'Controller@method',
           'files' => true,
        ])
    ?>
```
Open also support some magic methods:
```php
    <?= Form::horizontal_open() ?>         # class="form-horizontal"
    <?= Form::open_for_files() ?>          # enctype="multipart/form-data"
    <?= Form::vertical_open_for_files() ?> # combines both
```
You can mix it up however you want as long as each block remain intact (ie. you can't do `files_open_for_vertical` because that makes no sense). Form will also output the CSRF token automatically with the open tag.

### Close
```php
    <?= Form::close() ?> # Prints out a `</form>` tag.
```
### Standard elements

Note that the first argument specifies the `name` attribute. If no label is given, Form will use `ucfirst($name)` to generate a label.
```php
    <?= Form::open() ?>
    <?= Form::hidden('foo')->value(1) ?>
    <?= Form::text('foo')->label('My field') ?>
    <?= Form::textarea('foo')->value('My value') ?>
    <?= Form::select('foo')->options(['value1' => 'Label 1', ...])->select('value1') ?>
    <?= Form::multiselect('foo[]')->options(['value1' => 'Name 1', ...]) ?>
    <?= Form::checkbox('foo')->check() ?>
    <?= Form::radio('foo')->check() ?>
    <?= Form::close() ?>
```
## Grouped checkboxes and radios

Grouped checkboxes and radios allow you to specify multiple elements in a single element definition:
```php
    # Short format of $label => $name
    <?= Form::checkboxes('foo[]')
        ->checkboxes(['A' => 'foo[1]', 'B' => 'foo[2]'])
        ->check('foo[1]') ?> # Check grouped checkboxes by their name attribute
    <?= Form::radios('foo[]')
        ->radios(['A' => 'foo[1]', 'B' => 'foo[2]'])
        ->check(0) ?> # Check grouped radios by their value attribute

    # Long format
    <?= Form::checkboxes('foo[]')
        ->checkboxes([
            'A' => ['name' => 'foo[1]', 'value' => 1],
            'B' => ['name' => 'foo[2]', 'value' => 1]
        ])
        ->check('foo[1]') ?> # Check grouped checkboxes by their name attribute
    <?= Form::radios('foo[]')
        ->radios([
            'A' => ['name' => 'foo[1]', 'value' => 0],
            'B' => ['name' => 'foo[2]', 'value' => 1]
        ])
        ->check(0) ?> # Check grouped radios by their value attribute
```
Note that when using `->check()`, for checkboxes you specify the name of the one you want to see checked, and for radios you specify the value.

### Actions / Buttons
```php
    <?= Form::actions([string, ...]) ?>
```
Creates a `<div class='form-actions'>` tag to wrap your submit/reset/back/etc buttons. To output multiple buttons simply use multiple arguments
```php
    // Button class from Bootstrapper
    <?= Form::actions( Button::submit('Submit'), Button::reset('Reset') ) ?>
```
## Common utilities for all elements

### Label
```php
    <?= Form::text('foo')->label('My label') ?>
```
Sets the label of a field to a string. If you're using Bootstrap this has a specific meaning since the label that will be created will automatically have the class `control-label`.

### Bootstrap classes

On any form element, you can add Bootstrap classes such as small/large/span1/span6/etc:
```php
    <?= Form::small_text('foo') ?>
    <?= Form::span1_large_select('bar') ?>
```
You can use all Bootstrap classes from **span1** to **span12** and **mini** to **xxlarge**.

### Add class
```php
    <?= Form::text('foo')->addClass('my_class') ?>
```
Adds a class to the current field **without** overwriting any existing one. This differs front `->class()` will — just like any attribute setter — overwrite any existing value.

To add a class or other attribute on the Bootstrap form-group containing div, use the `onGroup` prefix:

```php
    <?= Form::text('foo')->onGroupAddClass('my_class') ?>
    <?= Form::text('foo')->onGroupDataFoo('my_data') ?>
```

### Force value
```php
    <?= Form::text('foo')->forceValue('my_value') ?>
```
Forces the field value to a particular value, ignoring both POST data and `Form::populate()` values.

### Value
```php
    <?= Form::text('foo')->value('my_value') ?>
```
Sets a default value for a field — a text present in it but that will get overwritten by calls to `Form::populate`, POST data, or `->forceValue` data.

### Select placeholders
```php
    <?= Form::select('foo')->placeholder('Select one option...') ?>
```
Create a ghost option set as disabled by default to serve as placeholder for the select.

### Arbitrary attributes
```php
    <?= Form::text('foo')->foo([value]) ?>
    <?= Form::text('foo')->data_foo([value]) ?>
```
Sets the value of any attribute. Attributes containing dashes have to be replaced with an underscore, so that you'd call `data_foo('bar')` to set `data-foo="bar"`.
```php
    <?= Form::text('text')->setAttribute('my_attribute', 'my value') ?>
```
Can be used as a fallback to magic methods if you really have to set an attribute that contains an underscore.
```php
    <?= Form::text('foo')->setAttributes(['my_attribute' => 'my value', ...]) ?>
```
Allows you to mass-set a couple of attributes with an array. So the following examples do the exact same thing.
```php
    <?= Form::text('foo')->class('foo')->foo('bar') ?>
    <?= Form::text('foo')->setAttributes(['class' => 'foo', 'foo' => 'bar']) ?>
```
The second way is not the cleanest per say but is still useful if you have to set the same attributes for a group of fields, you can then just create an array with the attributes you want and assign it to each field.

## ControlGroup

Helpers and methods related to Bootstrap's control groups. If you set the `$useBootstrap` option to false earlier, this class is not accessible, nor are any of its methods.
```php
    <?= Form::text('foo')->state([error|warning|info|success]) ?>
```
Set the state of a control group to a particular Bootstrap class.
```php
    <?= Form::text('foo')->inlineHelp('bar') ?>
    <?= Form::text('foo')->blockHelp('bar') ?>
```
Adds an inline/block help text to the current field. Both can be called (meaning you can have both an inline and a block text) but can only be called once (which means if you call `inlineHelp` twice the latter will overwrite whatever you typed in the first one).
```php
    <?= Form::text('foo')->append('bar') ?>
    <?= Form::text('foo')->prepend('bar') ?>
```
Prepends one or more icons/text/buttons to the current field. Those functions use `func_get_args()` so you can per example do that:
```php
    <?= Form::text('foo')->prepend('@', '$') ?>
```
### Add group class
```php
    <?= Form::text('foo')->addGroupClass('bar') ?>
```
Adds specified class attributes to the parent control-group.

## Options

This is a set of options that can be set to modify Form's behavior.
```php
    Form::setOption('translate_from', 'validation.attributes') ('validation.attributes')
```
By default Form tries to translate most labels, legends and help texts. For that it first tries to translate the string passed as is, and then it tries to look in a special place that can be defined with this variable - defaulting to 'validation.attributes.mykey'.
```php
    Form::setOption('required_class', 'required') ('required')
```
A class to add to fields that are set as required.
```php
    Form::setOption('default_form_type', [horizontal|vertical|inline|search]) ('horizontal')
```
By default when you call `Form::open` a fallback form type gets assigned to the form – this is the option that says which one is that type. It can be any of those values, or null if you want don't want to use Bootstrap's form classes.
```php
    Form::setOption('fetch_errors', true) (true)
```
Whenever you call the `open` method, if this option is set to true, Form will look into the Session array for a `Message` objects that would have been created by the `->with_errors()` method of `Redirect`. Which means that if on failed validation you do `Redirect::to()->with_errors()`, Form will automatically fetch said errors and display them on the corresponding fields without having to type anything.
```php
    Form::setOption('automatic_label', true) (true)
```
Allows the user to turn on or off the automatic labeling feature. When it's on, if you give per example `foo` as a field name and no label, Form will assume `foo` is also to be used as label. Form will then attempt to translate it, capitalize it, and use it as label. With this option turned off, if no label is specific, no label tag will be printed out.
```php
    Form::setOption('live_validation', true) (true)
```
Whether Form should try to apply Laravel's Validator rules as live validation attributes.
```php
    Form::setOption('push_checkboxes', true) (true)
```
Whether checkboxes should always be present in the POST data, no matter if you checked them or not
```php
    Form::setOption('TwitterBootstrap3.labelWidths', ['large' => 2, 'small' => 3])
```
Set the Bootstrap label width for your form.