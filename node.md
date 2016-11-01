## Node
The model traits can implement functionality. The node in the design can be divided into **Simple Tree** as well as a **Nested Tree**
### Simple Tree
A simple tree model will use the ***parent_id*** column maintain a parent and child relationship between models. To use the simple tree, apply the ***Litepie\Node\Traits\SimpleTree*** trait.
##### PHP
```
<?

class Category extends Model
{
    use \Litepie\Node\Traits\SimpleTree;
}
```
This trait will automatically inject two model relations called **parent** and **children**, it is the equivalent of the following definitions
##### PHP
```
<?

public $belongsTo = [
    'parent'    => ['User', 'key' => 'parent_id'],
];

public $hasMany = [
    'children'    => ['User', 'key' => 'parent_id'],
];
```
You can further go onto modifying the respective key name that has been used to identify the parent by defining the **parent_id** variable:
##### PHP
```
<?

var paretn_id = 'parent_id';
```
Collections of models that use this trait will return the type of **\Litepie\Node\NodeCollection** which adds the **toNested** method. To build an eager loaded tree structure, return the records with the relations eager loaded.
<?

Category::all()->toNested();
### Nested Tree
The nested set model is an advanced technique for maintaining hierachies among models using ***parent_id***, ***nest_left***, ***nest_right***, and ***nest_depth*** columns. To use a nested set model, apply the **\Litepie\Node\Traits\NestedTree*** trait. All of the features of the ***SimpleTree*** trait are inherently available in this model.
##### PHP
```
<?

class Category extends Model
{
    use \Litepie\Node\Traits\NestedNode;
}
```
##Creating a root node##
The default action of the compiler is to create all nodes as root nodes.
##### PHP
```
<?

$root = Category::create(['name' => 'Root category']);
```
Alternatively, you may find yourself in the need of converting an existing node into a root node:
##### PHP
```
<?

$node->makeRoot();
```
You may also nullify it's ***parent_id*** column which works the same as `makeRoot'.
##### PHP
```
<?

$node->parent_id = null;
$node->save();
```
##INSERTING NODES##
You can insert new nodes directly by the relation:
##### PHP
```
<?

$child2 = Category::create(['name' => 'Child 2']);
$child2->makeChildOf($root);
```
##Deleting Nodes##
When a particular node is deleted with the delete method, all descendants of the node will also be deleted. Note that the delete model events will not be fired for the child models.
##### PHP
```
<?

$child1->delete();
```
##Getting the nesting level of a node##

You can identify the level at which the node is embedded. The ***getLevel*** method will return current nesting level, or depth, of a node.
##### PHP
```
<?

// 0 when root
$node->getLevel()
```
##Moving nodes around##
There are several methods for moving nodes around. You could implement them through the following code

***moveLeft()***: Find the left sibling and move to the left of it. 

***moveRight()***: Find the right sibling and move to the right of it. 

***moveBefore($otherNode)***: Move to the node to the left of a defined node 

***moveAfter($otherNode)***: Move to the node to the right of a defined node 

***makeChildOf($otherNode)***: Make the node a child of a defined node

***makeRoot()***: Make current node a root node.
