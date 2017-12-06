# Filer

Package for handling file upload and display.

    use Litepie\Database\Model;
    use Litepie\Filer\Traits\Filer;

    ....

    class YourModelClass extends Model
    {
        use Filer ...;


### Configuration
Each model has a `config` file and the developer can specify its root uploading folder and file upload type using this configuration. The upload type is mainly divided into two, single and multiple for example in a products table the main image can be defined as single and gallery images can be defined as multiple. 

    // Model variables for file upload.
    'model'     => [
        ........
        'upload_folder' => '/package/modue',

        'uploads'      => [
            'single'   => ['image''],
            'multiple' => ['images''],
        ],

### View
For displaying, editing or uploading files on a view can be easy accoomplised using the below code snippets.

    {!! $model->fileUpload('field_name')!!} // to display file uploader (dropzone)
    {!! $model->fileEdit('field_name')!!} // to display file editor 
    {!! $model->fileShow('field_name')!!} // to display files 


### Storage
Files for each record of the table is stored on individual folders and corresponding details of the files are stored on the table in JSON format. Folder for uploading the files are stored in the each record upload_folder attribute. And the JSON data of each file contain details such as  Folder Name, File Name, Caption, Date & Time. 

Example JSON file formats for multiple file field.

    [  
       {  
          "folder":"\/2016\/05\/07\/050447617\/images\/",
          "file":"file.jpg",
          "caption":"File",
          "time":"2016-05-11 04:46:48"
       },
       {  
          "folder":"\/2016\/05\/07\/050447617\/images\/",
          "file":"file-2.png",
          "caption":"File 2",
          "time":"2016-05-11 04:46:49"
       }
    ]

Example JSON file formats for single file field


    {  
      "folder":"\/2016\/05\/07\/050447617\/images\/",
      "file":"file-2.png",
      "caption":"File 2",
      "time":"2016-05-11 04:46:49"
    }
In addition to the default details you can pass additional details form the view.

The following can be used for getting  URL required for uploading the files.


    $model->getUploadURL('field_name'))
