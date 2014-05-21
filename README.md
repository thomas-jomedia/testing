# COOKBOOK #

## Move an old controller to the new structure ##

### New routes: ###

Since some of the content have the same title and that the title is the unique identifier in our url some content are not accessible. We need to change the url format for everything to be accessible. 

Id will now be in the url so this will be our unique identifier.

before: `games/:gameName`

after: `game/:gameName/:id`

*The "s" have been removed intentionally.*

Examples are made with game but same apply to all medias

- games
- movies
- albums
- books
- software

Add routes in routing

- config/routing.yml

```php
game:
    path: /game/{title}/{id}
    defaults:  { _controller: game.controller:view, _template: game/view.html.twig }
```

### Old controller: ###

We want to get rid of static method.
- old/controllers/game_controller.php

```php
class GamesController extends Ihub_Controller
{
	public function view()
	{
        $restricted_ids = BrowseManager::getRestrictedIds('games'); // static method to get rid of
    }
}
```

### New controller: ###

copy old controller and remove static methods
- src/MemberArea/Content/Game/GameController.php

```php
namespace MemberArea\Content\Game;
class GameController
{
    private $filter;
    public function __construct(FilterInterface $filter) 
    {
        $this->filter = $filter; // dependencies now get injected
    }
    public function view($id)
    {
        $this->filter->getRestrictedIds();  // static method prevented
        $title = 'lorem ipsum';
		return array('id' => $id, 'title' => $title); // return variables to be accessible by the view
	}
}
```

### Old managers: ###

add `@deprecated` in the comment above the method

- old/managers/browse_manager.php

```php
class BrowseManager
{

    /**
     * @deprecated use MemberArea/Content/Game/Filter.php instead
     */
	public static function getRestrictedIds($media_type)
	{
	    // old code
	}
}
```

### New managers: ###
Managers are now seen as services, try to find a meaningfull name. In this example I choose `Filter` class. It will contain domain and geo restricted logic.

Ideally, refactor all methods, but it can take too much time; as a temporary solution and as to prevent code duplication, refer to the old method like so:
- src/MemberArea/Content/Game/Filter.php

```php
namespace MemberArea\Content\Game;

class Filter
{
	public function getRestrictedIds()
	{
		return BrowseManager::getRestrictedIds('games'); // temporary solution
	}
}
```


### Use TWIG for the views ###

This is the new template engine.

the variable {{ id }} has been made accessible by GameController.

- app-emedia-members-v2/views/game/view.html.twig

```php
{% extends 'layouts/default/layout.html.twig' %}

{% block content %}
	the game id is: {{ id }}
{% endblock content %}

```

### Use TWIG for the default layout ###

This is the new template engine.

the variable {{ title }} has been made accessible by GameController.

- app-emedia-members-v2/views/layouts/default.html.twig

```php
<html>
	<body>
	<h1>{{ title }}</h1>
		{% block content %}
		{% endblock content %}
	</body>
<html>
```
