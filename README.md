# LinQ

## Language-Integrated Query (**LINQ**) for **PHP** language

## Installation

```shell
composer require penobit/linq
```

## Usage

You can start using this package right away by importing your JSON data from a file:

```php
use Penobit\Linq\Linq;
$linq = new Linq($data);
```

Importing data from a PHP array or json string:

```php
$linq->collect(['id'=>1, 'name'=>'Penobit']);
```

Or using short handed function

```php
linq($data);
```

## Example JSON Data

Let's have a quick example:

```json
//data.json
{
   "name": "Penobit Linq for PHP",
   "description": "This is an example for php linq by Penobit.com",
   "vendor":{
      "name": "Penobit",
      "email": "info@penobit.com",
      "website":"www.penobit.com"
   },
   "users":[
      {"id":1, "name":"R8", "location": "Tehran"},
      {"id":2, "name":"Amin Goodarzi", "location": "Ohio"},
      {"id":3, "name":"Amir Jahangiri", "location": "Louisiana"},
      {"id":4, "name":"Jack Smith", "location": "New York"},
      {"id":5, "name":"John Doe", "location": "Manchester"},
      {"id":6, "name":"Sara Savari", "location": "Tehran", "visits": [
         {"name": "Home page", "year": 2020},
         {"name": "Client area", "year": 2021},
         {"name": "Admin panel", "year": 2019}
      ]}
   ],
   "products": [
      {"id":1, "userID": 2, "city": "TEH", "name":"iPhone", "cat":1, "price": 80000},
      {"id":2, "userID": 2, "city": null, "name":"macbook pro", "cat": 2, "price": 150000},
      {"id":3, "userID": 2, "city": "NY", "name":"iWatch", "cat": 1, "price": 12000},
      {"id":4, "userID": 1, "city": null, "name":"iMac", "cat":1, "price": 15000},
      {"id":5, "userID": 1, "city": "TEH", "name":"macbook air", "cat": 2, "price": 110000},
      {"id":6, "userID": 2, "city": null, "name":"macbook air 1", "cat": 2, "price": 81000}
   ]
}
```

```php
use Penobit\JsonQ\Linq;

$q = new Linq($data);
$res = $q->from('products')
    ->where('cat', '=', 2)
    ->get('name', 'price');

// or you could use $q->select('name', 'price') or $q->fetch('name', 'price')  instead of getch

var_dump($res->toArray());

//This will print
/*
array:3 [▼
  1 => {#7 ▼
    +"name": "macbook pro"
    +"price": 150000
  }
  4 => {#8 ▼
    +"name": "macbook air"
    +"price": 110000
  }
  5 => {#9 ▼
    +"name": "macbook air 1"
    +"price": 81000
  }
]
*/
```

Let's say we want to get the Summation of _price_ of the Queried result. We can do it easily by calling the **sum()** method instead of **get()**:

```php
$result = $linq->from('products')
        ->where('cat', '=', 2)
        ->sum('price');

var_dump($result);

//It will print:
/*
365000
*/
```

That was easy, wasn't it?

## API

>Here is the list of available methods for querying.

Following API examples are shown based on the sample JSON data given [here](#example-json-data)

**List of API:**

- [LinQ](#linq)
  - [Language-Integrated Query (**LINQ**) for **PHP** language](#language-integrated-query-linq-for-php-language)
  - [Installation](#installation)
  - [Usage](#usage)
  - [Example JSON Data](#example-json-data)
  - [API](#api)
    - [`fetch()`](#fetch)
    - [`find(path)`](#findpath)
    - [`from(path)`](#frompath)
    - [`at(path)`](#atpath)
    - [`where(key, condition, val)`](#wherekey-condition-val)
    - [`where(key, op, val)`](#wherekey-op-val)
    - [`callableWhere(fn)`](#callablewherefn)
    - [`orCallableWhere(fn)`](#orcallablewherefn)
    - [`orWhere(key, op, val)`](#orwherekey-op-val)
    - [`whereIn(key, val)`](#whereinkey-val)
    - [`whereNotIn(key, val)`](#wherenotinkey-val)
    - [`whereNull(key)`](#wherenullkey)
    - [`whereNotNull(key)`](#wherenotnullkey)
    - [`whereStartsWith(key, val)`](#wherestartswithkey-val)
    - [`whereEndsWith(key, val)`](#whereendswithkey-val)
    - [`whereContains(key, val)`](#wherecontainskey-val)
    - [`sum(column)`](#sumcolumn)
    - [`count()`](#count)
    - [`size()`](#size)
    - [`max(column)`](#maxcolumn)
    - [`min(column)`](#mincolumn)
    - [`avg(column)`](#avgcolumn)
    - [`first()`](#first)
    - [`last()`](#last)
    - [`nth(index)`](#nthindex)
    - [`exists()`](#exists)
    - [`groupBy(column)`](#groupbycolumn)
    - [`sort(order)`](#sortorder)
    - [`sortBy(column, order)`](#sortbycolumn-order)
    - [`reset(data)`](#resetdata)
    - [`copy()`](#copy)
  - [Example Files](#example-files)

### `fetch()`

This method will execute queries and will return the resulted data. You need to call it finally after using some query methods. Details can be found in other API examples like `get()`.

### `find(path)`

- `path` -- the path hierarchy of the data you want to find.

You don't need to call `fetch()` method after this. Because this method will fetch and return the data by itself.

**caveat:** You can't chain further query methods after it. If you need that, you should use `at()` or `from()` method.

**example:**

Let's say you want to get the value of _'cities'_ property of your Json Data. You can do it like this:

```php
$q = new Linq($data);
echo $q->find('vendor.name');
```

If you want to traverse to more deep in hierarchy, you can do it like:

```php
$q = new Linq($data);
echo $q->find('vendor.name');
```

<!-- See a detail example [here](examples/find.js). -->

### `from(path)`

- `path` (optional) -- the path hierarchy of the data you want to start query from.

By default, query would be started from the root of the JSON Data you've given. If you want to first move to a nested path hierarchy of the data from where you want to start your query, you would use this method. Skipping the `path` parameter or giving **'.'** as parameter will also start query from the root Data.

Difference between this method and `find()` is that, `find()` method will return the data from the given path hierarchy. On the other hand, this method will return the Object instance, so that you can further chain query methods after it.

**example:**

Let's say you want to start query over the values of _'vendor.name'_ property of your JSON Data. You can do it like this:

```php
$q = new Linq($data);
echo $q->from('vendor.name')->get();
```

If you want to traverse to more deep in hierarchy, you can do it like:

```php
$q = new Linq($data);
echo $q->from('users.5.visits')->get();
```

<!-- See a detail example [here](examples/from.php). -->

### `at(path)`

This is an alias method of `from()` and will behave exactly like that.
 <!-- See example [here](examples/from.php). -->

### `where(key, condition, val)`

- `key` -- the property name of the data. Or you can pass a Function here to group multiple query inside it. See details in [example](examples/where.js)
- `val` -- value to be matched with. It can be a _int_, _string_, _bool_ or even _Function_ - depending on the `op`.
- `op` -- operand to be used for matching. The following operands are available to use:

- `=` : For weak equality matching
- `eq` : Same as `=`
- `!=` : For weak not equality matching
- `neq` : Same as `!=`
- `==` : For strict equality matching
- `seq` : Same as `==`
- `!==` : For strict not equality matching
- `sneq` : Same as `!==`
- `>` : Check if value of given **key** in data is Greater than **val**
- `gt` : Same as `>`
- `<` : Check if value of given **key** in data is Less than **val**
- `lt` : Same as `<`
- `>=` : Check if value of given **key** in data is Greater than or Equal of **val**
- `gte` : Same as `>=`
- `<=` : Check if value of given **key** in data is Less than or Equal of **val**
- `lte` : Same as `<=`
- `null` : Check if the value of given **key** in data is **null** (`val` parameter in `where()` can be omitted for this `op`)
- `notnull` : Check if the value of given **key** in data is **not null** (`val` parameter in `where()` can be omitted for this `op`)
- `in` : Check if the value of given **key** in data is exists in given **val**. **val** should be a plain _Array_.
- `notin` : Check if the value of given **key** in data is not exists in given **val**. **val** should be a plain _Array_.
- `startswith` : Check if the value of given **key** in data starts with (has a prefix of) the given **val**. This would only works for _String_ type data.
- `endswith` : Check if the value of given **key** in data ends with (has a suffix of) the given **val**. This would only works for _String_ type data.
- `contains` : Check if the value of given **key** in data has a substring of given **val**. This would only works for _String_ type data.
- `match` : Check if the value of given **key** in data has a Regular Expression match with the given **val**. The `val` parameter should be a **RegExp** for this `op`.
- `macro` : It would try to match the value of given **key** in data executing the given `val`. The `val` parameter should be a **Function** for this `op`. This function should have a matching logic inside it and return **true** or **false** based on that.

**example:**

### `where(key, op, val)`

Let's say you want to find the _'users'_ who has _`id`_ of `1`. You can do it like this:

```php
$q = new Linq($data);
$res = $q->from('users')->where('id', '=', 1)->get();
// Or
$res = $q->from('users')->where('id', 1)->get();
```

You can add multiple _where_ conditions. It'll give the result by AND-ing between these multiple where conditions.

```php
$q = new Linq($data);
$res = $q->from('users')
    ->where('id', '=', 1)
    ->where('location', '=', 'Tehran')
    ->get();
```

<!-- See a detail example [here](examples/where.php). -->

### `callableWhere(fn)`

Parameter of `callableWhere()` is callable function that should return a `bool` (true/false)

```php
$q = new Linq($data);
$res = $q->from('users')
    ->callableWhere(function($item) {
        return $item['price'] > 3000;
    })
    ->toArray();
```

### `orCallableWhere(fn)`

Parameter and behaviour of `orCallableWhere()` is the same as `callableWhere()`

```php
$res = $q->from('users')
    ->where('id', 1)
    ->callableWhere(function($item) {
        return $item['location'] == 'Tehran' &&  substr($item['name'], 0, 3) == 'mac';
    })
    ->orCallableWhere(function($item) {
        return $item['price'] > 3000;
    })
    ->toArray();
```

### `orWhere(key, op, val)`

Parameters of `orWhere()` are the same as `where()`. The only difference between `where()` and `orWhere()` is: condition given by the `orWhere()` method will OR-ed the result with other conditions.

For example, if you want to find the users with _id_ of `1` or `2`, you can do it like this:

```php
$q = new Linq($data);
$res = $q->from('users')
    ->where('id', '=', 1)
    ->orWhere('id', '=', 2)
    ->get();
// Or
$res = $q->from('users')
    ->where('id', 1)
    ->orWhere('id', 2)
    ->get();
```

<!-- See detail example [here](examples/or-where.php). -->

### `whereIn(key, val)`

- `key` -- the property name of the data
- `val` -- it should be an **Array**

This method will behave like `where(key, 'in', val)` method call.

### `whereNotIn(key, val)`

- `key` -- the property name of the data
- `val` -- it should be an **Array**

This method will behave like `where(key, 'notin', val)` method call.

### `whereNull(key)`

- `key` -- the property name of the data

This method will behave like `where(key, 'null')` or `where(key, '=', null)` method call.

### `whereNotNull(key)`

- `key` -- the property name of the data

This method will behave like `where(key, 'notnull')` or `where(key, '!=', null)` method call.

### `whereStartsWith(key, val)`

- `key` -- the property name of the data
- `val` -- it should be a String

This method will behave like `where(key, 'startswith', val)` method call.

### `whereEndsWith(key, val)`

- `key` -- the property name of the data
- `val` -- it should be a String

This method will behave like `where(key, 'endswith', val)` method call.

### `whereContains(key, val)`

- `key` -- the property name of the data
- `val` -- it should be a String

This method will behave like `where(key, 'contains', val)` method call.

### `sum(column)`

- `column` -- the property name of the data

**example:**

Let's say you want to find the sum of the _'price'_ of the _'products'_. You can do it like this:

```php
$q = new Linq($data);
$res = $q->from('products')
    ->where('cat', '=', 1)
    ->sum('price');
```

If the data you are aggregating is plain array, you don't need to pass the 'column' parameter.
<!-- See detail example [here](examples/sum.php) -->

### `count()`

It will return the number of elements in the collection.

**example:**

Let's say you want to find how many elements are in the _'products'_ property. You can do it like:

```php
$q = new Linq($data);
$res = $q->from('products')
    ->where('cat', '=', 1)
    ->count();
```

See detail example [here](examples/count.php).

### `size()`

This is an alias method of `count()`.

### `max(column)`

- `column` -- the property name of the data

**example:**

Let's say you want to find the maximum of the _'price'_ of the _'products'_. You can do it like this:

```php
$q = new Linq($data);
$res = $q->from('products')
    ->where('cat', '=', 1)
    ->max('price);
```

If the data you are querying is plain array, you don't need to pass the 'column' parameter.
<!-- See detail example [here](examples/max.php) -->

### `min(column)`

- `column` -- the property name of the data

**example:**

Let's say you want to find the minimum of the _'price'_ of the _'products'_. You can do it like this:

```php
$q = new Linq($data);
$res = $q->from('products')
    ->where('cat', '=', 1)
    ->min('price');
```

If the data you are querying is plain array, you don't need to pass the 'property' parameter.
<!-- See detail example [here](examples/min.php) -->

### `avg(column)`

- `column` -- the property name of the data

**example:**

Let's say you want to find the average of the _'price'_ of the _'products'_. You can do it like this:

```php
$q = new Linq($data);
$res = $q->from('products')
    ->where('cat', '=', 1)
    ->avg('price');
```

If the data you are querying is plain array, you don't need to pass the 'column' parameter.
<!-- See detail example [here](examples/avg.php) -->

### `first()`

It will return the first element of the collection.

**example:**

```php
$q = new linq('data.json');
$res = $q->from('products')
    ->where('cat', '=', 1)
    ->first();
```

<!-- See detail example [here](examples/first.php). -->

### `last()`

It will return the last element of the collection.

**example:**

```php
$q = new Linq($data);
$res = $q->from('products')
    ->where('cat', '=', 1)
    ->last();
```

<!-- See detail example [here](examples/last.php). -->

### `nth(index)`

- `index` -- index of the element to be returned.

It will return the nth element of the collection. If the given index is a **positive** value, it will return the nth element from the beginning. If the given index is a **negative** value, it will return the nth element from the end.

**example:**

```php
$q = new Linq($data);
$res = $q->from('products')
  ->where('cat', '=', 1)
  ->nth(2);
```

<!-- See detail example [here](examples/nth.php). -->

### `exists()`

It will return **true** if the element is not **empty** or not **null** or not an **empty array** or not an **empty object**.

**example:**

Let's say you want to find how many elements are in the _'products'_ property. You can do it like:

```php
$q = new Linq($data);
$res = $q->from('products')
    ->where('cat', '=', 1)
    ->exists();
```

<!-- See detail example [here](examples/exists.php). -->

### `groupBy(column)`

- `column` -- The property by which you want to group the collection.

**example:**

Let's say you want to group the _'users'_ data based on the _'location'_ property. You can do it like:

```php
$q = new Linq($data);
$res = $q->from('users')
    ->groupBy('location')
    ->get();
```

See detail example [here](examples/group-by.php).

### `sort(order)`

- `order` -- If you skip the _'order'_ property the data will be by default ordered as **ascending**. You need to pass **'desc'** as the _'order'_ parameter to sort the data in **descending** order. Also, you can pass a compare function in _'order'_ parameter to define your own logic to order the data.

**Note:** This method should be used for plain Array. If you want to sort an Array of Objects you should use the **sortBy()** method described later.

**example:**

Let's say you want to sort the _'arr'_ data. You can do it like:

```php
$q = new Linq();
$res = $q->collect([7, 5, 9, 1, 3])
    ->sort();
```

See detail example [here](examples/sort.php).

### `sortBy(column, order)`

- `column` -- You need to pass the column name on which the sorting will be done.
- `order` -- If you skip the _'order'_ property the data will be by default ordered as **ascending**. You need to pass **'desc'** as the _'order'_ parameter to sort the data in **descending** order. Also, you can pass a compare function in _'order'_ parameter to define your own logic to order the data.

**Note:** This method should be used for Array of Objects. If you want to sort a plain Array you should use the **sort()** method described earlier.

**example:**

Let's say you want to sort the _'price'_ data of _'products'_. You can do it like:

```php
$q = new Linq($data);
$res = $q->from('products')
    ->where('cat', '=', 1)
    ->sortBy('price', 'desc');
```

<!-- See detail example [here](examples/sort-by.php). -->

### `reset(data)`

- `data` -- can be a JSON string or a PHP array. If no data passed in the `data` parameter, the `Linq` Object instance will be reset to previously initialized data.

At any point, you might want to reset the Object instance to a completely different set of data and then query over it. You can use this method in that case.

<!-- See a detail example [here](examples/reset.php). -->

### `copy()`

It will return a complete clone of the Object instance.

<!-- See a detail example [here](examples/copy.php). -->

<!-- ## Others Platform

This package has also different language support.

- [JavaScript JsonQ](https://github.com/me-shaon/js-linq) developed by [Ahmed shamim](https://github.com/me-shaon)
- [Python JsonQ](https://github.com/s1s1ty/py-linq) developed by [Shaon Shaonty](https://github.com/s1s1ty)
- [Go JsonQ](https://github.com/thedevsaddam/golinq) developed by [Saddam H](https://github.com/thedevsaddam) - Upcoming -->

## Example Files

> There will be an example file for any of above methods soon.
