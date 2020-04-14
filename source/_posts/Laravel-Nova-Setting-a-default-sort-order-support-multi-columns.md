---
title: 'Laravel Nova: Setting a default sort order support multi columns'
date: 2020-04-14 16:20:45
tags:
  - Laravel
  - Laravel Nova
  - TIL
---

# Laravel Nova: Setup a default sort order for resource which support multi columns

- Put the code bellow into Laravel Nova resource class

- We are override `indexQuery` method of Resource parrent class.

```
  /**
   * Default Sort Columns variable
   *
   * @var array
   */
  public static $defaultSort = [
      'id' => 'asc',
      'name' => 'desc'
  ];

  /**
   * Build an "index" query for the given resource.
   *
   * @param  \Laravel\Nova\Http\Requests\NovaRequest  $request
   * @param  \Illuminate\Database\Eloquent\Builder  $query
   * @return \Illuminate\Database\Eloquent\Builder
   */
  public static function indexQuery(NovaRequest $request, $query)
  {
      if (static::$defaultSort && empty($request->get('orderBy'))) {
          $query->getQuery()->orders = [];
          foreach (static::$defaultSort as $field => $order) {
              $query->orderBy($field, $order);
          }
      }
      return $query;
  }
```
