### Revision
The Lavalite model is capable of recording the history of changes in values by storing revisions. 

The revisions can be stored for the model, you could further apply the ***Litepie\Rivision\Traits\Revision*** trait and declare a ***$revision*** property with an array containing the attributes to monitor for changes. 

You also need to define a ***$morphMany*** model relation called revision_history that refers to the ***Litepie\Revision\Models\Revision*** class with the name ***revision***, this is where the revision history data is stored.
##### PHP
```
<?

class User extends Model
{
    use \Litepie\Rivision\Traits\Revision;

    /**
     * @var array Monitor these attributes for changes.
     */
    protected $revision = ['name', 'email'];

    /**
     * @var array Relations
     */
    public $morphMany = [
        'revision_history' => ['Litepie\Revision\Models\Revision', 'name' => 'revision']
    ];
}
```
By default 500 records will be kept, however this limit can be further modified by declaring a ***$revisionableLimit*** property on the model with a new limit value.
##### PHP
```
<?

/**
 * @var int Maximum number of revision records to keep.
 */
public $revisionLimit = 50;
```
The revision history can be accessed like any other relation:
##### PHP
```
<?

$history = User::find(1)->revision_history;

foreach ($history as $record) {
    echo $record->field . ' updated ';
    echo 'from ' . $record->old_value;
    echo 'to ' . $record->new_value;
}
```
