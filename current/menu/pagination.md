---
title: Pagination
layout: default
nav_order: 100
---

<h1>Pagination</h1>

- [Implementing pagination](#implementing-pagination)
  - [Eloquent Pagination (Recommended)](#eloquent-pagination-recommended)

## Implementing pagination

Rejoice allows you to quickly create a paginable menu. The recommend way to implement a paginable menu is by using the Eloquent ORM. But you can also create the same menu using directly SQL queries.

This is a typical scenario:

- We want the user to select his country.
- We assume the user has selected his continent in the previous menu (named `SelectContinent`).

This is how we will implement such a menu with the help of the eloquent paginator trait.

### Eloquent Pagination (Recommended)

Create the menu class by using:

```php
php smile make:menu SelectCountry -p
```

This command will generate a paginable menu in the `Menus` directory:

```php

namespace App\Menus;

use App\Menus\Menu;
use Rejoice\Menu\EloquentPaginator as Paginator;

class SelectCountry extends Menu
{
    use Paginator;

    protected $perPage = 4;

    /**
     * Menu message.
     *
     * @return string|array
     */
    public function message()
    {
        if (!$this->itemsExist()) {
            return 'No item found';
        }

        return 'Select an option';
    }

    /**
     * Items to paginate.
     *
     * Do not use '->get()' or '->first()' on the query.
     * Rejoice will perform it at the appropriate time.
     *
     * @return Rejoice\Database\QueryBuilder
     */
    public function paginate()
    {
        // return Model::where([]);
    }

    /**
     * Will be called for each item.
     *
     * @param array|Rejoice\Support\Collection $row
     * @param string $trigger
     * @return array
     */
    public function itemAction($row, $trigger)
    {
        return [
            $trigger => [
                'display'   => $row['name'],
                'next_menu' => 'menu_name',
                'save_as'   => $row['id'],
            ],
        ];
    }
}
```

The `paginate` method will be called to retrieve the items from the database. It must return the query to select the items from the database or a logic returning the an array of the current items.

The `itemAction` defines the action to perform for each item.

We just now need to modify the paginate method and the `itemAction`.

Assuming we have a `Country` model, let's modify our `paginate` method:

```php
public function paginate()
{
    $continent = $this->previousResponses('SelectContinent');

    return Country::where('continent_id', '=', $continent);
}
```

Assuming a country has a name and a code and we rather want to save the country's code instead of the ID and the next menu is `InputName`, let's modify the `itemAction` function like this:

```php
public function itemAction($country, $trigger)
{
    return [
        $trigger => [
            'display'   => $country['name'],
            'next_menu' => 'InputName',
            'save_as'   => $country['code'],
        ],
    ];
}
```

That's all! We have our paginable menu:

```php

namespace App\Menus;

use App\Menus\Menu;
use App\Models\Country;
use Rejoice\Menu\EloquentPaginator as Paginator;

class SelectCountry extends Menu
{
    use Paginator;

    protected $perPage = 4;

    public function message()
    {
        if (!$this->itemsExist()) {
            return 'No item found';
        }

        return 'Select an option';
    }

    public function paginate()
    {
        $continent = $this->previousResponses('SelectContinent');

        return Country::where('continent_id', '=', $continent);
    }

    public function itemAction($country, $trigger)
    {
        return [
            $trigger => [
                'display'   => $country['name'],
                'next_menu' => 'InputName',
                'save_as'   => $country['code'],
            ],
        ];
    }
}
```

We can also customize the `message` method to display different message based on the page on which we are by using one or both of the methods `isPaginationFirstPage` or `isPaginationLastPage`:

```php
public function message()
{
    if (!$this->itemsExist()) {
        return 'No item found';
    } elseif ($this->isPaginationFirstPage()) {
        return 'Select an option';
    } elseif ($this->isPaginationFirstPage()) {
        return 'Last page';
    }

    return '';
}
```

The paginator trait makes available some methods for our use:

- `perPage` The maximum number of items that can be showed on the pagination screen; this method will return the `perPage` property of the menu if it's configured, else it will return a default value. The default value is `4`.
- `currentItemsCount` The actual number of items showed on the current screen (the number of items can be less the `perPage`, on the last page.);
- `isPaginationFirstPage` Check if the current screen is the first screen of the pagination;
- `isPaginationLastPage` Check if the current screen is the last screen of the pagination;
- `lastRetrievedId` Get the id of the last fetched row - the next query to the database to fetch, will begin at that index;
- `paginationSave` Saves a data for pagination purpose;
- `paginationGet` Get a pagination data;
- `paginationHas` Check if a pagination data exists;

<div class="note note-warning">When using the pagination, always prefer:

```php
$this->previousResponses('the_name_of_the_previous_menu');
```

to retrieve the response of the previous menu, over

```php
$this->userResponse();
//or
$this->userSavedResponse();
```

</div>
