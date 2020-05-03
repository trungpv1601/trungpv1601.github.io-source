---
title: 'Laravel Nova: Setting a default sort order support multi columns'
date: 2020-04-14 16:20:45
tags:
  - Laravel
  - Laravel Nova
  - TIL
---

## 1. Trait

```
<?php

namespace App\Traits;

use Laravel\Nova\Http\Requests\NovaRequest;

trait NovaDefaultSort
{
  /**
   * Default Sort Columns
   * @return array
   */
  public static function getDefaultSort()
  {
    return [
      'id' => 'asc'
    ];
  }

  /**
   * Build an "index" query for the given resource.
   *
   * @param  \Laravel\Nova\Http\Requests\NovaRequest  $request
   * @param  \Illuminate\Database\Eloquent\Builder  $query
   * @return \Illuminate\Database\Eloquent\Builder
   */
  public static function indexQuery(NovaRequest $request, $query)
  {
    if (static::getDefaultSort() && empty($request->get('orderBy'))) {
      $query->getQuery()->orders = [];
      foreach (static::getDefaultSort() as $field => $order) {
        $query->orderBy($field, $order);
      }
    }
    return $query;
  }
}

```

## 2. Usage

- Put the code bellow into Laravel Nova resource class

- We are override `getDefaultSort` method of Resource parrent class.

```
  use NovaDefaultSort;

  /**
   * Default Sort Columns
   * @return array
   */
  public static function getDefaultSort()
  {
    return [
      'id' => 'asc',
      'name' => 'desc'
    ];
  }
```
