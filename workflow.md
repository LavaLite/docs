# Workflow

Use the Symfony Workflow component in Lavalite

### Installation

    composer require litepie/workflow

#### For laravel <= 5.4

Add a ServiceProvider to your providers array in `config/app.php`:

```php
<?php

'providers' => [
    ...
    Litepie\Workflow\WorkflowServiceProvider::class,

]
```

Add the `Workflow` facade to your facades array:

```php
<?php
    ...
    'Workflow' => Litepie\Workflow\Facades\WorkflowFacade::class,
```

### Definitions 

Here is the sample definition for the work-flow

```php

return [
    
    'workflows' => [
    	// Single state workflow
        'singlestatesample' => [
            'type'          => 'state_machine',
            'marking_store' => [
                'type'      => 'single_state',
                'arguments' => ['status'],
            ],
            'supports'      => [App\Models\Posts::class],
            'places'        => [
                'draft',
                'completed',
                'verified',
                'published'
            ],
            'transitions'   => [
                'complete'  => [
                    'from' => 'draft',
                    'to'   => 'completed',
                ],
                'verify'    => [
                    'from' => 'completed',
                    'to'   => 'verified',
                ],
                'publish'   => [
                    'from' => 'approved',
                    'to'   => 'published',
                ]
            ],
        ],
        
        
    	// Mutilple state workflow
        'multiplestatesample'  [
            'type'          => 'workflow',
            'marking_store' => [
                'type'      => 'multiple_state',
                'arguments' => ['status'],
            ],
            'supports'      => [App\Models\Posts::class],
            'places'        => [
                'draft',
                'wait_for_spellchecker',
                'wait_for_journalist',
                'published'
            ],
            'transitions'   => [
                'complete'  => [
                    'from' => 'draft',
                    'to'   => ['wait_for_spellchecker', 'wait_for_journalist'],
                ],
                'spellchecker_approval'    => [
                    'from' => 'wait_for_spellchecker',
                    'to'   => 'published',
                ],
                'journalist_approval'   => [
                    'from' => 'wait_for_journalist',
                    'to'   => 'published',
                ]
            ],

        ],
    ],
];
```

## Service provider

You should bing the configuration using the workflow service provide for each packages.


```php

namespace App\Providers;

use App\Workflow\Providers\WorkflowServiceProvider as ServiceProvider;

class WrokflowServiceProvider extends ServiceProvider
{

    /**
     * Register any package authentication / authorization services.
     *
     * @param \Illuminate\Contracts\Auth\Access\Gate $gate
     *
     * @return void
     */
    public function boot()
    {
        $this->workflow = config('path.to.workflows');
        parent::registerWorkflows();
    }
}

```


## Trait

Use the `WorkflowModalTrait` inside supported classes

```php

namespace App;

use Illuminate\Database\Eloquent\Model;
use Litepie\Workflow\Traits\WorkflowModalTrait;

class BlogPost extends Model
{
  use WorkflowModalTrait;

}
```

### Usage

#### Using Facades

```php
$post = BlogPost::find(1);
$workflow = Workflow::get($post);
// if more than one workflow is defined for the BlogPost class
$workflow = Workflow::get($post, $workflowName);

$workflow->can($post, 'publish'); // False
$workflow->can($post, 'to_review'); // True
$transitions = $workflow->getEnabledTransitions($post);

// Apply a transition
$workflow->apply($post, 'to_review');
$post->save(); // Don't forget to persist the state
```

#### Using the WorkflowModalTrait

```php
$post->workflowCan('publish'); // True
$post->workflowCan('to_review'); // False

// Get the post transitions
foreach ($post->workflowTransitions() as $transition) {
    echo $transition->getName();
}

// Apply a transition
$post->workflowApply('publish');
```

### Use the events

This package provides a list of events fired during a transition

```php
    Litepie\Workflow\Events\Guard
    Litepie\Workflow\Events\Leave
    Litepie\Workflow\Events\Transition
    Litepie\Workflow\Events\Enter
    Litepie\Workflow\Events\Entered
```

You can subscribe to an event

```php
namespace App\Listeners;

class BlogPostWorkflowSubscriber
{
    /**
     * Handle workflow guard events.
     */
    public function onGuard(GuardEvent $event) {
        /** Symfony\Component\Workflow\Event\GuardEvent */
        $originalEvent = $event->getOriginalEvent();

        /** @var App\BlogPost $post */
        $post = $originalEvent->getSubject();
        $title = $post->title;

        if (empty($title)) {
            // Posts with no title should not be allowed
            $originalEvent->setBlocked(true);
        }
    }

    /**
     * Handle workflow leave event.
     */
    public function onLeave($event) {}

    /**
     * Handle workflow transition event.
     */
    public function onTransition($event) {}

    /**
     * Handle workflow enter event.
     */
    public function onEnter($event) {}

    /**
     * Handle workflow entered event.
     */
    public function onEntered($event) {}

    /**
     * Register the listeners for the subscriber.
     *
     * @param  Illuminate\Events\Dispatcher  $events
     */
    public function subscribe($events)
    {
        $events->listen(
            'Litepie\Workflow\Events\GuardEvent',
            'App\Listeners\BlogPostWorkflowSubscriber@onGuard'
        );

        $events->listen(
            'Litepie\Workflow\Events\LeaveEvent',
            'App\Listeners\BlogPostWorkflowSubscriber@onLeave'
        );

        $events->listen(
            'Litepie\Workflow\Events\TransitionEvent',
            'App\Listeners\BlogPostWorkflowSubscriber@onTransition'
        );

        $events->listen(
            'Litepie\Workflow\Events\EnterEvent',
            'App\Listeners\BlogPostWorkflowSubscriber@onEnter'
        );

        $events->listen(
            'Litepie\Workflow\Events\EnteredEvent',
            'App\Listeners\BlogPostWorkflowSubscriber@onEntered'
        );
    }

}
```

### Dump Workflows
Symfony workflow uses GraphvizDumper to create the workflow image. You may need to install the `dot` command of [Graphviz](http://www.graphviz.org/)

    php artisan workflow:dump workflow_name

You can change the image format with the `--format` option. By default the format is png.

    php artisan workflow:dump workflow_name --format=jpg
