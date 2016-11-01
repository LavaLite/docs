## Form
###### Manages form controls.
The forms are aims to rethink elegantly creation of forms by transforming each field into its own model, with its own methods and attributes. This means that you can do this sort of stuff 
##### PHP
```
Form::horizontal_open()
  ->id('MyForm')
  ->secure()
  ->rules(['name' => 'required'])
  ->method('GET');

  Form::xlarge_text('name')
    ->class('myclass')
    ->value('Joseph')
    ->required();

  Form::textarea('comments')
    ->rows(10)->columns(20)
    ->autofocus();

  Form::actions()
    ->large_primary_submit('Submit')
    ->large_inverse_reset('Reset');

Form::close();
```
##### Every time you call a method that doesn't actually exist, Form assumes you're trying to set an attribute and creates it magically. That's why you can do in the above example ***->rows(10)*** ; in case you want to set attributes that contain dashes, just replace them by underscores : **-** ***>data_foo('bar')*** equals **data-foo="bar"**. Now of course in case you want to set an attribute that actually contains an underscore you can always use the fallback method ***setAttribute('data_foo', 'bar')***. You're welcome. 
### GETTING STARTED
#### Core Concept
Form is to be used as a View helper – meaning it provides for you a class that you can use directly in your views to output HTML code.
##### PHP
```
<?= Form::open()->method('GET') ?>
    <?= Form::text('name')->required() ?>
<?= Form::close() ?>
```
If you're using Twig or other more closed view templating systems, you can still use Form's classes outside 
##### PHP
```
$form = (string)Form::open()->method('GET');
    $form .= Form::text('name')->required();
$form .= Form::close();
```
#### FEATURES
#### Out-of-the-box integration to Bootstrap and Foundation
Form recognizes when a horizontal or vertical form is created, and goes the extra mile of wrapping each field in a control group, all behind the scenes. That means that when you type this 
##### PHP
```
Form::select('clients')->options($clients, 2)
  ->help('Pick some dude')
  ->state('warning')
```
What you actually get is the following output (with Bootstrap)
##### PHP
```
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
By default Form will use Twitter Bootstrap for its syntax but you can select which framework to use with the method ***Form::framework()***. For the moment Form supports ***'TwitterBootstrap', 'ZurbFoundation' ***and*** 'Nude'*** (for no framework).
##### PHP
```
// Turn off Bootstrap syntax
Form::framework('Nude');

// Turn it on again (MAKE UP YOUR MIND JEEZ)
Form::framework('TwitterBootstrap');
```
Here is an example of code for Foundation 
##### PHP
```
Form::framework('ZurbFoundation');

Form::four_text('foo')->state('error')->help('bar')
```
##### Outputs
##### PHP
```
<div class="error">
  <label for="foo">Foo</label>
  <input class="four" type="text" name="foo" id="foo">
  <small>Bar</small>
</div>
```
#### Custom Framework
You may also implement your own framework/way of doing this by setting a custom class for ***Form::framework()*** 
##### PHP
```
// Custom framework
Form::framework('CustomNameSpace\YourFramework');
```
#### Ties-in with Laravel's Validator
A lot of clutter removed by not having to call the lenghty ***Form::control_group()*** function.

You would not need to manually validate my form and su. 

Enters Form's magic helper ***withErrors***; wraps fields into control groups, it goes the distance, and gently check the ***Message*** object for any errors that field might have, and set that error as an ***.help-inline. 

**If your render a view on failed validation (no redirection)** 
##### PHP
```
if($validation->fails()) {
  Form::withErrors($validation);
  return view('myview');
}
```
**If your redirect on failed validation** 
##### PHP
```
if($validation->fails()) {
  return redirect('login')
    ->withErrors($validation);
}
```
In the last example you never actually call Form, be it in your controller or in your view. 

That's because when Form opens a form on a page, it will automatically check in Session if there's not an object called ***errors ***and if there is, it will try to use it without requiring you to call anything. 

You can disable Form's automatic errors fetching with the following option : ***Form::config('fetch_errors', false).*** 
#### FORM POPULATING
You can populate a form with value quite easily with the ***Form::populate*** function. 

There is two ways to do that. 

The first way is the usual passing of an array of values.
##### PHP
```
// Will populate the field 'name' with the value 'value'
Form::populate( array('name' => 'value') )
```
You can also populate a form by passing an Eloquent model to it, say you have a Client model.
##### PHP
```
Form::populate( Client::find(2) )
```
Form will recognize the model and populate the field with the model's attribute. If here per example our client has a ***name ***set to 'Foo' and a ***firstname ***set to 'Bar', Form will look for fields named 'name' and 'firstname' and fill them respectively with 'Foo' and 'Bar'.

Alternatively you can also populate a specific field after you've populated the whole form (for a relationship per example) by doing this 
##### PHP
```
Form::populate($project)

Form::populateField('client', $project->client->name)

For the rest of the form, filling fields is basically as easy as doing ***->value('something')***. To generate a list of options for a ***<select> ***you call ***Form::select('foo')->options([array], [facultative: selected value])***. You can also use the results from an Eloquent/Fluent query as options for a select.
```
##### PHP
```
Form::select('foo')->fromQuery(Client::all(), 'name', 'id')
```
Where the second argument is which attribute will be used for the option's text, and the third facultative argument is which attribute will be used for the option's value (defaults to the ***id*** attribute). 

Form also does some magic if none of those two arguments are specified. If you pass Eloquent models to Form and don't specify what is to be used as key or value. The Form will obtain the key by using Eloquent's ***get_key()*** method, and use any ***__toString()*** method binded to the model as raw value. 
##### PHP
```
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
Is the same as doing this but you know, in less painful and DRYer. This will use each Client's default key, and output the Client's name as the option's label.
##### PHP
```
<div class="control-group">
  <label for="foo" class="control-label">Foo</label>
  <div class="controls">
    <select id="foo" name="foo">
      @foreach(Client::all() as $client)
        @if(Input::get('foo', Input::old('foo')) == $client->code)
          <option selected="selected" value="{{ $client->code }}">{{ $client->name }}</option>
        @else
          <option value="{{ $client->code }}">{{ $client->name }}</option>
        @endif
      @endforeach
    </select>
  </div>
</div>
```
Form is also able to populate fields with relationships. Now an example is worth a thousand words (excepted if, you know, your example is a thousand words long)

##### PHP
```
Form::populate(Client::find(2))

// Will populate with $client->name
Form::text('name')

// Will populate with $client->store->name
Form::text('store.name')

// You can go as deep as you need to
Form::text('customer.name.adress')

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
#### Datalists
Datalists, allows people to get a choice or range of selections or input the selection that they desire. You can simply create a datalist as follows 
##### PHP
```
Form::text('clients')->useDatalist($clients)

// Or use a Query object, same syntax than fromQuery()
Form::text('projects')->useDatalist(Project::all(), 'name')
```
You can also (if you need to) set a custom id on the created datalist by doing ***Form::text('foo')->list('myId')->useDatalist()***. 

From there it will automatically generate the corresponding ***<datalist>*** and link it by ***id ***to that field. 

The text input will get populated by the values in your array, while still letting people type whatever they would like.
#### Live validation
All modern browsers support instant validation via HTML attributes —there is no need for Javascript nor script nor polyfill. 

There are a few attributes that can allow you to conduct instant validation, ***pattern, required, max/min ***to name a few. 

When you validate your POST data with that little $rules array. You would be able to pass that array to your form and let it transcribe your rules into real-live validation
##### PHP
```
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
The Form will look for fields that match the keys and apply the best it can those rules. 
##### PHP
```
<input name="name"      type="text"   required maxlength="20" pattern="[a-zA-Z]+" />
<input name="age"       type="number" min="18" max="24" />
<input name="email"     type="email" />
<input name="show"      type="text"   pattern="^(batman|spiderman)$" />
<input name="random"    type="text"   pattern="[a-zA-Z]+" />
<input name="birthday"  type="date"   max="1968-12-03" />
<input name="avatar"    type="file"   accept="image/jpeg,image/png,image/gif,image/bmp" />
```
Note that you can always add custom rules the way you'd add any attributes, since the pattern attribute uses a Regex.
##### PHP
```
Form::number('age')->min(18)

Form::text('client_code')->pattern('[a-z]{4}[0-9]{2}')
```
Bootstrap recognizes live validation, if you type something that doesn't match the ***alpha ***pattern in your name field, it will automatically turn red just like when your control group is set to ***error***. 

You can also, mid-course, manually set the state of a control group — that's a feature of course available only if you're using either Bootstrap or Foundation. You can use any of the control group states which include*** success, warning, error ***and ***info***.
#### Files handling
In Form like in Laravel you can create a simple file field with ***Form::file***. What's new, is you can also create a multiple files field by calling ***Form::files*** which which will generate ***<input type="file" name="foo[]" multiple />.*** 

One of the special method is the*** ->accept() ***with which you can do the following
##### PHP
```
// Use a shortcut (image, video or audio)
Form::files('avatar')->accept('image')

// Use an extension which will be converted to MIME by Laravel
Form::files('avatar')->accept('gif', 'jpg')

// Or directly use a MIME
Form::files('avatar')->accept('image/jpeg', 'image/png')
```
You can also set a maximum size easily by using either bits or bytes
##### PHP
```
Form::file('foo')->max(2, 'MB')
Form::file('foo')->max(400, 'Kb')
Form::file('foo')->max(1, 'TB')
```
This will create an hidden ***MAX_FILE_SIZE*** field with the correct value in bytes.
#### Checkboxes and Radios
With Form it's all a little easier to validate all the respective Checkboxes and radios. 
##### PHP
```
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
**Important point** : The form gives you an option to force the pushing of checkboxes. 

That's when your checkboxes still pop up in your POST data even when they're unchecked. 

You can further change what value an unchecked checkbox possesses in the POST array via the unchecked_value option.

When creating checkables via the checkboxes/radios() method, by default for each checkable name attribute it will use the original name you specified and append it a number (here in our exemple it would be <input type="checkbox" name="checkme_2">). It also repopulates it, meaning a checked input will stay checked on submit.
#### Localization helpers
If you would want to work on multilingual projects, the Form is also capable of helping. 

By default, when creating a field, if no label is specified the Form will use the field name by default. It will try and translate it automatically. 

The same applied towards checkboxes labels, help texts and form legends. 

This can be represented through the following
##### PHP
```
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
The Form will first try to translate the string in itself, ie ***my.text*** will return ***__('my.text')*** and if that fails, it will look for it in a fallback place. 

You can further set the Form to look for translations by changing the following variable : ***Form::config('translate_from', [boolean])*** (defaults to ***validation.attributes***). 

This must be an array.
#### Notes on setting field values
All the form classes encounter a problem at one point : 

To populate your field, Form set the following priorities to found values

1. The ***->forceValue()*** method wins over everything – it's a special branch of ***->value()*** created to force a value in a field no matter what happens

2. Next is POST data – if a user just typed something in a field, chances are this is what they want to see in it next time

3. Then any values set via ***Form::text()->populate()***, so you can override any global population on a per-element basis.

4. Then any values set via ***Form::populate()*** – that means that if you're editing something or are repopulating with some value, it can be overwritten with ***forceValue***

5. Finally the classic ***->value()*** gets the least priority – it is created for minimum and default field values and thus gets overwritten by population and POST data
#### Anatomy of Form
Bare with me, there are a lot of classes but it all makes sense once you read what they do :

**Form** : The main class, mostly handles configuration and interactions between the form parts

**FormServiceProvider** : The Service Provider for Laravel 4

**Helpers** : A set of static helpers used troughout the library

**Dispatch**: Catches the call you do the Form facade and dispatches them to the right classes

**LiveValidation**: This Handles live validation of fields and translation of rules into attributes

**Populator**: A value container from which Form gets/sets the fields values

**Traits**
**Checkable**: Common methods and properties to radios and checkboxes

**Field**: Common methods and properties to all fields

**FormObject**: A base object extended by the traits above, handles dynamic attributes and stuff

**Framework**: Common methods and properties to all frameworks

**Interfaces**: Required methods by all fields an frameworks
**FieldInterface**

**FrameworkInterface**

**Framework**: These classes generate the right markup according to the framework in use

**Nude**

**TwitterBootstrap**

**ZurbFoundation**

**Form**: All the classes underneath handle the actual markup and elements of a form

**Actions**: Handles the actions blocks where submit buttons and all are found

**Elements**: Various form elements that are neither fields nor actions (legends, etc)

**Form**: Handles the form opening and closing and the methods that go with it

**Group**: Wraps fields and provide field states, errors fetching and such

**Fields**: The field classes, each handle one kind of field. The names say it all

**Button**

**Checkbox**

**File**

**Hidden**

**Input**

**Radio**

**Select**

**Textarea**

**Uneditable**

**Facades**: Various entry points that smooth out the experience across environments

**Agnostic**: The base facade used by Form outside of any framework

**FormBuilder**: Common methods and building blocks for all facades to use

**Illuminate**: The Laravel 4 facade, hooks up Form to the various components (localization etc)

**LaravelThree**: The Laravel 3 facade, hooks up Form to the various components (localization etc)

**Legacy**: A set of redirector classes that unify the interface between Laravel 3 and 4

**Config**

**Redirector**

**Session**

**Translator** 

#### Description of each method
#### Form\Form
This class is the main class - it is your interface for everything in Form. 

Now of course, Form makes you interact with its sub classes which means you can not only use its methods but the methods of each class used by Form.
#### Options
This is a set of options that can be set to modify Form's behavior.
##### PHP
```
Form::setOption('translate_from', [string]) ('validation.attributes')
```
By default Form tries to translate most labels, legends and help texts. For that it first tries to translate the string passed as is, and then it tries to look in a special place that can be defined with this variable - defaulting to 'validation.attributes.mykey'.
##### PHP
```
Form::setOption('required_class', [string]) ('required')
```
A class to add to fields that are set as required.
##### PHP
```
Form::setOption('default_form_type', [horizontal|vertical|inline|search]) ('horizontal')

```
By default when you call ***Form::open*** a fallback form type gets assigned to the form – this is the option that says which one is that type. It can be any of those values, or null if you want don't want to use Bootstrap's form classes.
##### PHP
```
Form::setOption('fetch_errors', [boolean]) (true)
```
Whenever you call the ***open*** method, if this option is set to true, Form will look into the Session array for a ***Message*** objects that would have been created by the -***>with_errors()*** method of ***Redirect***. Which means that if on failed validation you do ***Redirect::to()->with_errors()***, Form will automatically fetch said errors and display them on the corresponding fields without having to type anything.
##### PHP
```
Form::setOption('automatic_label', [boolean]) (true)
```
Allows the user to turn on or off the automatic labeling feature. When it's on, if you give per example ***foo*** as a field name and no label, Form will assume ***foo*** is also to be used as label. Form will then attempt to translate it, capitalize it, and use it as label. With this option turned off, if no label is specific, no label tag will be printed out.
##### PHP
```
Form::setOption('live_validation', [boolean]) (true)
```
Whether Form should try to apply Laravel's Validator rules as live validation attributes.
##### PHP
```
Form::setOption('push_checkboxes', [boolean]) (true)
```
Whether checkboxes should always be present in the POST data, no matter if you checked them or not
##### PHP
```
Form::setOption('TwitterBootstrap3.labelWidths', array('large' => 2, 'small' => 3)) ?>
```
Set the Bootstrap label width for your form.
#### Helpers
##### PHP
```
Form::populate([array|Eloquent])
```
Populates the fields with an array of values or an Eloquent object.
##### PHP
```
Form::withErrors([Validator])
```
If you have an ***$errors*** variable from a failed validation, the Form will automatically fetch it from Session and set the corresponding fields as incorrect and display the error message right next to them. This is if you're calling ***withErrors*** in your view. If you're in the controller, you can simply pass the ***$validator*** object to Form and it will work just the same.
##### PHP
```
Form::withRules([array])
```
Let you pass an array of Laravel rules to Form, to let it try and apply those rules live with HTML attributes such as ***pattern***, ***maxlength***, etc.
##### PHP
```
Form::framework([TwitterBootstrap3|ZurbFoundation5|null])
```
Select which CSS framework Form should use for its syntax – each gives you more or less access to the available methods. Per example you can't use Bootstrap's ***->blockHelp*** method while using Zurb, etc.
##### PHP
```
$error = Form::getErrors([string])
```
This is equivalent to
##### PHP
```
$error = isset($errors) ? $errors->first('field') : null.
```
#### Form builders
##### PHP
```
Form::legend([string])
```
This opens a Bootstrap ***legend>*** tag in your form – the string passed to Form can be a translation index since Form will attempt to translate it.
##### PHP
```
Form::open()
```
The ***open*** method mirrors Laravel's, which means you can refer to the doc for it on Laravel's website. But it also support the magic methods introduced by Bootstrapper for backward compatibility :

##### PHP
```
Form::horizontal_open()
Form::secure_open()
Form::open_for_files()
Form::secure_vertical_open_for_files()
```
You can mix it up however you want as long as each block remain intact 
##### PHP
```
Form::close()
```
This is pretty straightforward, simply prints out a </form> tag. It contains no argument or anything.
##### PHP
```
Form::actions([string, ...])
```
Creates a ***<div class='form-actions'>*** tag to wrap your submit/reset/back/etc buttons. 

To output multiple buttons simply use multiple arguments
##### PHP
```
// Button class from Bootstrapper
Form::actions( Button::submit('Submit'), Button::reset('Reset') )
```
#### Field Builders
This is the one class you'll be using the two thirds of the time
##### PHP
```
Form::[classes]_[field]
```
This method analyze whatever unknown method you're trying to call and creates a field with it. 

It decomposes it as [classes]_[field] or [field]. As classes you can call all Bootstrap classes working on fields : **span1** to **span12** and **mini** to **xxlarge**
#### Form\Field
This class is what you actually get when you create a field, which means the method listed underneath are only accessible as chained methods after a field was created. 
##### PHP
```
// Here you're using Form
Form::populate($project)

// Here you're actually using the Field class wrapped in Form
Form::text('foo')
```
A Form class stops being a field the second that field is printed out. 
##### PHP
```
$textField = Form::text('foo');
$textField->class('myclass')
```
But can't do this
##### PHP
```
echo Form::text('foo')
Form::class('myclass')
```
#### Interactions with the attributes
##### PHP
```
Form::text('foo')->addClass([string])
```
Adds a class to the current field without overwriting any existing one. 

This does differs front ***->class()*** will — just like any attribute setter — overwrite any existing value
##### PHP
```
Form::text('foo')->forceValue([string])
```
This forces the field value to a particular value, ignoring both POST data and ***Form::populate()*** values.
##### PHP
```
Form::text('foo')->value([string])
```
This sets a default value for a field — a text present in it but that will get overwritten by calls to ***Form::populate*** or POST data.
##### PHP
```
Form::text('foo')->[attribute]([value])
```
This sets the value of any attribute. Attributes containing dashes have to be replaced with an underscore, so that you'd call ***data_foo('bar')*** to set ***data-foo="bar"***.
##### PHP
```
Form::text('text')->setAttribute([string], [string])
```
This can be used as a fallback to the magic methods, if you really want to set an attribute that contains an underscore.
##### PHP
```
Form::text('foo')->setAttributes([associative array])
```
This code allows you to mass-set a couple of attributes with an array. 

The following examples do the exact same thing.
```
Form::text('foo')->class('foo')->foo('bar')

Form::text('foo')->setAttributes(array( 'class' => 'foo', 'foo' => 'bar' ))
```
The second method is not the cleanest per say but it is still useful if you have to set the same attributes for a group of fields. 

You can then just create an array with the attributes you want and assign it to each field in order to stay DRY.
##### PHP
```
Form::text('foo')->label([string])
```
This sets the label of a field to a string. 

If you're using Bootstrap this would have a specific meaning, since the label that will be created will automatically have the class control-label.
##### PHP
```
Form::text('foo')->addGroupClass('bar')
```
This adds specified class attributes to the parent control-group.
#### Helpers
##### PHP
```
$textField = Form::text('foo')->require()
$textField = $textField->isRequired() // Returns "true"
```
This checks if a field has been set to required, be it by yourself or when transposing Laravel rules.
#### Form\Checkable
The Checkable is a subset of Field. 

This means all methods available with the Field are available with Checkable. The latter just offers a few more methods related to radios and checkboxes.
##### PHP
```
Form::radios('foo')->inline()
Form::checkboxes('foo')->stacked()
```
This would set the current radios and checkboxes as inline, or stacked (vertical)
##### PHP
```
Form::checkbox('foo')->text('bar')
```
If you're printing only a single checkbox/radio, 

it's easier to use this method to set the text that will be appended to it.
##### PHP
```
Form::checkbox('foo')->check()
Form::radio('foo')->check()

Form::checkboxes('foo')->checkboxes('foo', 'bar')->check('foo_0')

Form::radios('foo')->checkboxes('foo', 'bar')->check(0)
```
The check method allows you to check a one-off checkable element, or to check one in a group of checkable elements. 

For single elements you can just call check() without arguments – but when you're using a group you would need to specify which element is going to be checked. 

This is done differently if you're creating radios or checkboxes. 

This is because checkboxes can only be differentiated by their field name, while radio elements can only be differentiated by their value. The checkboxes you specify the name of the one you want to see checked, and for radios you specify its value.
#### Form\ControlGroup
Helpers and methods related to Bootstrap's control groups. If you set the ***$useBootstrap*** option to false earlier, this class is not accessible, nor are any of its methods.
##### PHP
```
Form::text('foo')->state([error|warning|info|success])
```
This set the state of a control group to a particular Bootstrap class.
##### PHP
```
Form::text('foo')->inlineHelp([string])
Form::text('foo')->blockHelp([string])
```
This further adds an inline/block help text to the current field. 

Both can be called (meaning you can have both an inline and a block text) but can only be called once (which means if you call inlineHelp twice the latter will overwrite whatever you typed in the first one).
##### PHP
```
Form::text('foo')->append([string, ...])
Form::text('foo')->prepend([string, ...])
```
Prepends one or more icons/text/buttons to the current field. Those functions use ***func_get_args()*** so you can per example 
##### PHP
```
Form::text('foo')->prepend('@', '$')
```
#### Form\Fields\Input
All classes related to Input-type fields.
##### PHP
```
Form::text('foo')->useDatalist([array])

Form::text('foo')->useDatalist([Query], [string], [string (id)])

Creates a ***`<datalist>`*** tag and links it to the field in order to create a select/text mix.
```
#### Form\Fields\Select
All classes related to Select-type fields (select, multiselect, etc)
##### PHP
```
Form::select('foo')->options([array], [selected])
```
Populates a ***`<select>`*** field with an array of options, where key will be the option's value and value will be its text. 
It sounds retarded explained like that but it means the following :
##### PHP
```
Form::select('clients')->options(array(
    1  => 'Max',
    3  => 'Clémence',
    12 => 'Jean Valjean'
));

<select name="foo">
    <option value="1">Max</option>
    <option value="3">Clémence</option>
    <option value="12">Jean Valjean</option>
</select>
```
Which looks really natural : you select a client's name and get it's ID in return. The second parameters allows to preselect one option, per example if in the above example we'd done -***>options($clients, 3)*** then Clémence would have been selected.
##### PHP
```
Form::select('foo')->select(3)
```
Alias for ***->value()***, selects an option.
##### PHP
```
Form::select('foo')->fromQuery([array], [string], [string (id)])
```
Populates a ***`<select>`*** field with the results of a Fluent/Eloquent query. Second argument is the attribute used by Form for the options text, third argument is the attribute used by Form for the options value (defaults to the ***id*** attribute). Second argument defaults to the model's ***__toString()*** method, thirds defaults to the model's ***get_key()*** method.

Alternative use ***fromQuery*** for ***Form::select***
##### PHP
```
Form::select('foo')->fromQuery([array], [callable], [array])
```
Sample
##### PHP
```
Form::select('foo')->fromQuery(
    $collection,
    function($model) {
        return $model->id . $model->name;//option text
    },
    [//array option attributes
        'value' => 'id',
        'data-test' => function($model) {
            return $model->test_field;
        }
    ]
);
```
Possible mixed use.
##### PHP 
```
Form::select('foo')->placeholder('Select one option...')
```
Create a ghost option set as disabled by default to serve as placeholder for the select.

