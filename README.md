# Laravel Sub Query

![PHP Composer](https://github.com/Alexmg86/laravel-sub-query/workflows/PHP%20Composer/badge.svg)
[![Latest Stable Version](https://poser.pugx.org/alexmg86/laravel-sub-query/v/stable)](https://packagist.org/packages/alexmg86/laravel-sub-query)
[![License](https://poser.pugx.org/alexmg86/laravel-sub-query/license)](https://packagist.org/packages/alexmg86/laravel-sub-query)

## Why is this method needed?

Now, when you trying to summarize a column in a related model, you get 2 queries in the database. With this method, it all turns into 1 query to the database and there is no need to load extra data.
I often use this in my work and I hope it will be useful to you!

## Installation

Install via composer
```bash
composer require alexmg86/laravel-sub-query
```
Use LaravelSubQueryTrait trait in your model.
```php
use Alexmg86\LaravelSubQuery\Traits\LaravelSubQueryTrait;
use Illuminate\Database\Eloquent\Model;

class Invoice extends Model
{
    use LaravelSubQueryTrait;
```

## Usage

If you want to get results from a relationship without actually loading them and by one request to the database you may use the these methods, which will place a new columns on your resulting models. For example:
```php
$invoices = Invoice::withSum('items:price,price2')->get();
echo $invoices[0]->items_price_sum;
$invoices = Invoice::withMin('items:price,price2')->get();
echo $invoices[0]->items_price_min;
```
The following methods apply to all methods.!!!
You may add the "sum" for multiple relations as well as add constraints to the queries:
```php
use Illuminate\Database\Eloquent\Builder;

$invoices = Invoice::withSum(['items:price', 'goods:price,price2' => function (Builder $query) {
    $query->where('price','>',6);
}])->get();

echo $invoices[0]->items_price_sum;
echo $invoices[0]->goods_price_sum;
echo $invoices[0]->goods_price2_sum;
```
If you're combining `withSum` with a `select` statement, ensure that you call `withSum` after the `select` method:
```php
$invoices = Invoice::select(['id'])->withSum('items:price')->get();

echo $invoices[0]->id;
echo $invoices[0]->items_price_sum;
```
In addition, using the `loadSum` method, you may load a relationship sum columns after the parent model has already been retrieved:
```php
$invoice = Invoice::first();
$invoice->loadSum('items:price');
```
If you need to set additional query constraints on the eager loading query, you may pass an array keyed by the relationships you wish to load. The array values should be Closure instances which receive the query builder instance:
```php
$invoice = Invoice::first();
$invoice->loadSum(['items:price' => function ($query) {
    $query->where('price', '>', 5);
}]);
```
And of course it is all compatible with scopes in models.

## Security

If you discover any security related issues, please email instead of using the issue tracker.

## Credits

- [All contributors](https://github.com/alexmg86/laravel-sub-query/graphs/contributors)

