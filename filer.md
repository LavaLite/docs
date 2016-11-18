## Filer
The package allows the user to upload and download images through means of multiple and single entries into the website.

The basic model for the package can be implemented through the following code. 

    use Litepie\Database\Model;
    use Litepie\Filer\Traits\Filer;

    ....

    class YourModelClass extends Model
    {
        use Filer ...;


### Implementing the File Upload Function
The model has a **config** file.

The code that must be implemented in order implement the file upload function is as follows.

The code can be divided as an single and multiple, as decided by the user. 

Eg. Single can be used to implement a profile picture, while multiple selection can be implemented for a photo gallery. 

    // Model variables for module variable.
    'model'     => [
        ........
        'uploadfolder' => '/uploads/page',

        'uploads'      => [
            'single'   => ['single_file_field_1', 'single_file_field_2'],
            'multiple' => ['multiple_file_field_1', 'multiple_file_field_2'],
        ],



### File Upload Interface
The file upload interface can be generated on the user side, implementing the following code. The would be open to allowing the user to upload all types of files.

A further iteration on choosing a particular type of files (Eg. Images, Word, Excel) can be done ahead. 
##### PHP
 ```
 <?

{!! Filer::uploader('multiple_file_field_1', $page->getUploadURL('multiple_file_field_1')) !!}

 ```
 ### Single VS Multiple Upload
 The following code can be found once the file has been uploaded in a JSON format online. 

The code for the multiple upload would be as follows. 

The data would consist of Folder Name, File Name, Caption, Time & EFolder. 
##### PHP
```

Multiple
[  
   {  
      "folder":"\/uploads\/pages\/2016\/05\/07\/050447617\/images\/",
      "file":"file.jpg",
      "caption":"File",
      "time":"2016-05-11 04:46:48",
      "efolder":"pages\/066zCXfgcnxBl0\/images"
   },
   {  
      "folder":"\/uploads\/pages\/2016\/05\/07\/050447617\/images\/",
      "file":"file-2.png",
      "caption":"File 2",
      "time":"2016-05-11 04:46:49",
      "efolder":"pages\/066zCXfgcnxBl0\/images"
   }
]
```
### The following lines of code can be found for a single uploaded file.
##### PHP
```

Single
{  
  "folder":"\/uploads\/pages\/2016\/05\/07\/050447617\/images\/",
  "file":"file-2.png",
  "caption":"File 2",
  "time":"2016-05-11 04:46:49",
  "efolder":"pages\/066zCXfgcnxBl0\/images"
}
```
The following can be implemented in order to return the URL required for uploading the respective files.
##### PHP
```
<?

$page->getUploadURL('field_name'))
```
