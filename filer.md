## Filer
Filer is used for managing files on the website / application. 

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
        'uploads'       =>[
                        'Field_name' =>  [
                        'count' => 10,
                        'type'  => 'image',
          		                         ],
       		              ],
        'casts'        => [
                        'field_name' => 'array',
                          ]
                   


### View
For displaying, editing or uploading files on a view can be easy accoomplised using the below code snippets.

    // to upload files(dropzone)
    {!! $model->files('field_name')
    ->url($model->getUploadURL('field_name'))
    ->uploader()!!}
                              
    // to edit files
    {!! $model->files('field_name')
    ->editor() !!}
                               
    // to display files
    {!! $model->files('field_name') !!}    
    

### Storage
Files for each record of the table is stored on individual folders and corresponding details of the files are stored on the table in JSON format. Folder for uploading the files are stored in the each record upload_folder attribute. And the JSON data of each file contain details such as  Folder Name, File Name, Caption, Date & Time. 

Example JSON file formats for multiple file field.

    [0] => Array
    (
    [caption] => 0 neu d3 5e34ea5ff3f5d1e13e91a97f292505e1
    [folder] => 2017/04/18/062918175/images
    [time] => 2017-04-18 06:29:55
    [file] => 505e1.jpg
    [url] => http://192.168.1.199/lavalite/cms/5.2/public/download/litecms.blog/blog/9DD3CZF0F4q6vJ/images/0-neu.jpg
    )


In addition to the default details you can pass additional details form the view.

The following can be used for getting  URL required for uploading the files.


    {!! $model->files('field_name')
     ->url($model->getUploadUrl('field_name'))
     ->dropzone()!!}
