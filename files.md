# Filer

Package for handling file upload and display.

```php
    use Litepie\Database\Model;
    use Litepie\Filer\Traits\Filer;

    ....

    class YourModelClass extends Model
    {
        use Filer ...;
```

### Configuration
Each model has a `config` file and the developer can specify its uploading folder and file upload type using this configuration. The upload type is mainly divided into two, single and multiple for example in a products table the main image can be defined as single and gallery images can be defined as multiple. 

```php
    // Model variables for file upload.
    'model'     => [
        ........
        'upload_folder' => '/package/modue',

        'uploads'      => [
            'image'  => [
                'count' => 1,
                'type'  => 'image', // image or file
            ],
            'images' => [
                'count' => 10,
                'type'  => 'image',
            ],
        ],
```

### Views
For displaying, editing or uploading files on a view can be easy accoomplised using the below code snippets.
If you want to customize the view publish the files and edit. Other views can be specifyed using `$model->files('field_name'))->view()`

Display uploader (dropzone)
```php
{!!
  $model->files('field_name'))
    ->url($model->getUploadURL('field_name')))
    ->dropzone(); 
!!}
```
Other functions available are uploader() & input().

Show files (Grid view)
```php
{!!
  $model->files('field_name'))
    ->show();
!!}
```

Editor files
```php
{!!
  $model->files('field_name'))
    ->editor();
!!}
```
In addition to the default parameters you can pass additional details form the view using 
 like `mine()` `url()` `view()` `count()` to the file views.

`$model->getUploadURL('field_name'))` generate the full upload link of a model.

### Storage
Files for each record of the table is stored on individual folders and corresponding details of the files are stored on the table in JSON format. Folder for uploading the files are stored in the each record upload_folder attribute. And the JSON data of each file contain details such as  Folder Name, File Name, Caption, Date & Time and fullpath relative to the main upload folder. 

Example JSON file formats for multiple file field.

```php
    [  
       {  
          "folder":"\/2017\/10\/07\/050447617\/images\/",
          "file":"file.jpg",
          "caption":"File",
          "time":"2017-05-11 04:46:48"
          "path":"package/\module\/2017\/10\/07\/050447617\/images\/file.jpg"
       },
       {  
          "folder":"\/2017\/10\/07\/050447617\/images\/",
          "file":"file-2.png",
          "caption":"File 2",
          "time":"2017-05-11 04:46:49"
          "path":"package/\module\/2017\/10\/07\/050447617\/images\/file.jpg"
       }
    ]
```

Don't forget to specify the upload folder in the `.env` file. By default it is `UPLOAD_FOLDER=storage/uploads`

## Displaying Images & Files
Lavalite used [Intervension](http://image.intervention.io/) for resizing and displayng images. You can find its documentaion in their website.

Default image filters included in lavalite is `xl` `lg` `md` `sm` `xs` and it can be configure through filer configration  `filer.size`.

The url for image resize will be like `http://yourhost.com/{route-name}/{filter-name}/{file-name}`
For more details [click here](http://image.intervention.io/use/url)
