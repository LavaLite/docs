# Node

The model traits can implement functionality. The node in the design can be divided into `Simple Tree` as well as a `Nested Tree`

## Simple Tree
A simple tree model will use the `parent_id` column maintain a parent and child relationship between models. To use the simple tree, apply the `Litepie\Node\Traits\SimpleTree` trait.

```php
	class Category extends Model
	{
	    use \Litepie\Node\Traits\SimpleTree;
	}
```

This trait will automatically inject two model relations called `parent` and `children**`, it is the equivalent of the following definitions.


```php
	public $belongsTo = [
	    'parent'    => ['User', 'key' => 'parent_id'],
	];

	public $hasMany = [
	    'children'    => ['User', 'key' => 'parent_id'],
	];
```

You can further go onto modifying the respective key name that has been used to identify the parent by defining the **parent_id** variable:

	var paretn_id = 'parent_id';

Collections of models that use this trait will return the type of `\Litepie\Node\NodeCollection` which adds the `toNested` method. To build an eager loaded tree structure, return the records with the relations eager loaded.

	Category::all()->toNested();

## Nested Tree
The nested set model is an advanced technique for maintaining hierachies among models using `parent_id`, `nest_left`, `nest_right`, and `nest_depth` columns. To use a nested set model, apply the `\Litepie\Node\Traits\NestedTree` trait. All of the features of the `SimpleTree` trait are inherently available in this model.

```php
	class Category extends Model
	{
	    use \Litepie\Node\Traits\NestedNode;
	}
```

##Creating a root node##
The default action of the compiler is to create all nodes as root nodes.

	$root = Category::create(['name' => 'Root category']);

Alternatively, you may find yourself in the need of converting an existing node into a root node:

	$node->makeRoot();

You may also nullify it's `parent_id` column which works the same as `makeRoot`.

```php
	$node->parent_id = null;
	$node->save();
```

##INSERTING NODES##
You can insert new nodes directly by the relation:

```php
	$child2 = Category::create(['name' => 'Child 2']);
	$child2->makeChildOf($root);
```
##Deleting Nodes##
When a particular node is deleted with the delete method, all descendants of the node will also be deleted. Note that the delete model events will not be fired for the child models.

	$child1->delete();

##Getting the nesting level of a node##

You can identify the level at which the node is embedded. The `getLevel` method will return current nesting level, or depth, of a node.


```php
	// 0 when root
	$node->getLevel()
```

##Moving nodes around
There are several methods for moving nodes around. You could implement them through the following code

`moveLeft()`: Find the left sibling and move to the left of it. 

`moveRight()`: Find the right sibling and move to the right of it. 

`moveBefore($otherNode)`: Move to the node to the left of a defined node 

`moveAfter($otherNode)`: Move to the node to the right of a defined node 

`makeChildOf($otherNode)`: Make the node a child of a defined node

`makeRoot()`: Make current node a root node.

## Nested set model trait.

Model table must have parent_id, nest_left, nest_right and nest_depth table columns.

```
$table->integer('parent_id')->nullable();

$table->integer('nest_left')->nullable();

$table->integer('nest_right')->nullable();

$table->integer('nest_depth')->nullable();

```
You can change the column names used by declaring:

```
const PARENT_ID = 'my_parent_column';
const NEST_LEFT = 'my_left_column';
const NEST_RIGHT = 'my_right_column';
const NEST_DEPTH = 'my_depth_column';
```
### General access methods

`$model->getRoot();` // Returns the highest parent of a node.

`$model->getRootList();` // Returns an indented array of key and value columns from root.

`$model->getParent();` // The direct parent node.

`$model->getParents();` // Returns all parents up the tree.

`$model->getParentsAndSelf();` // Returns all parents up the tree and self.

`$model->getChildren();` // Set of all direct child nodes.

`$model->getSiblings();` // Return all siblings (parent's children).

`$model->getSiblingsAndSelf();` // Return all siblings and self.

`$model->getLeaves();` // Returns all final nodes without children.

`$model->getDepth();` // Returns the depth of a current node.

`$model->getChildCount();` // Returns number of all children.


###Query builder methods

`$query->withoutNode();` 	// Filters a specific node from the results.

`$query->withoutSelf();` 	// Filters current node from the results.

`$query->withoutRoot();` 	// Filters root from the results.

`$query->children();` 	// Filters as direct children down the tree.

`$query->allChildren();` 	// Filters as all children down the tree.

`$query->parent();` 	// Filters as direct parent up the tree.

`$query->parents();` 	// Filters as all parents up the tree.

`$query->siblings();` 	// Filters as all siblings (parent's children).

`$query->leaves();` 	// Filters as all final nodes without children.

`$query->getNested();` 	// Returns an eager loaded collection of results.

`$query->listsNested();` 	// Returns an indented array of key and value columns.

### Flat result access methods

`$model->getAll();` // Returns everything in correct order.

`$model->getAllRoot();` // Returns all root nodes.

`$model->getAllChildren();` // Returns all children down the tree.

`$model->getAllChildrenAndSelf();` // Returns all children and self.

###Eager loaded access methods

`$model->getEagerRoot();` // Returns a list of all root nodes, with ->children eager loaded.

`$model->getEagerChildren();` // Returns direct child nodes, with ->children eager loaded.

