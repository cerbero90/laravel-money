# Laravel Money

[![Latest Stable Version](https://poser.pugx.org/cknow/laravel-money/version)](https://packagist.org/packages/cknow/laravel-money)
[![Total Downloads](https://poser.pugx.org/cknow/laravel-money/downloads)](https://packagist.org/packages/cknow/laravel-money)
[![tests](https://github.com/cknow/laravel-money/workflows/tests/badge.svg)](https://github.com/cknow/laravel-money/actions)
[![StyleCI](https://styleci.io/repos/40018123/shield?style=flat)](https://styleci.io/repos/40018123)
[![codecov](https://codecov.io/gh/cknow/laravel-money/branch/master/graph/badge.svg)](https://codecov.io/gh/cknow/laravel-money)
[![License](https://poser.pugx.org/cknow/laravel-money/license)](https://packagist.org/packages/cknow/laravel-money)

> **Note:** This project abstracts [MoneyPHP](http://moneyphp.org/)

## Installation

Run the following command from you terminal:

```bash
composer require cknow/laravel-money
```

or add this to require section in your composer.json file:

```bash
"cknow/laravel-money": "~4.0"
```

then run ```composer update```

## Usage

```php
use Cknow\Money\Money;

echo Money::USD(500); // $5.00
```

## Configuration

The defaults are set in `config/money.php`. Copy this file to your own config directory to modify the values. You can publish the config using this command:

```bash
php artisan vendor:publish --provider="Cknow\Money\MoneyServiceProvider"
```

This is the contents of the published file:

```php
return [
    /*
     |--------------------------------------------------------------------------
     | Laravel money
     |--------------------------------------------------------------------------
     */
    'locale' => config('app.locale', 'en_US'),
    'currency' => config('app.currency', 'USD'),
];
```

## Advanced Usage

> See [MoneyPHP](http://moneyphp.org/) for more information

```php
use Cknow\Money\Money;

Money::USD(500)->add(Money::USD(500)); // $10.00
Money::USD(500)->add(Money::USD(500), Money::USD(500)); // $15.00
Money::USD(500)->subtract(Money::USD(400)); // $1.00
Money::USD(500)->subtract(Money::USD(200), Money::USD(100)); // $2.00
Money::USD(500)->multiply(2); // $10.00
Money::USD(1000)->divide(2); // $5.00
Money::USD(830)->mod(Money::USD(300)); // $2.30 -> Money::USD(230)
Money::USD(-500)->absolute(); // $5.00
Money::USD(500)->negative(); // $-5.00
Money::USD(30)->ratioOf(Money::USD(2)); // 15
Money::USD(500)->isSameCurrency(Money::USD(100)); // true
Money::USD(500)->equals(Money::USD(500)); // true
Money::USD(500)->greaterThan(Money::USD(100)); // true
Money::USD(500)->greaterThanOrEqual(Money::USD(500)); // true
Money::USD(500)->lessThan(Money::USD(1000)); // true
Money::USD(500)->lessThanOrEqual(Money::USD(500)); // true
Money::USD(500)->isZero(); // false
Money::USD(500)->isPositive(); // true
Money::USD(500)->isNegative(); // false
Money::USD(500)->getMoney(); // Instance of \Money\Money

// Aggregation
Money::min(Money::USD(100), Money::USD(200), Money::USD(300)); // Money::USD(100)
Money::max(Money::USD(100), Money::USD(200), Money::USD(300)); // Money::USD(300)
Money::avg(Money::USD(100), Money::USD(200), Money::USD(300)); // Money::USD(200)
Money::sum(Money::USD(100), Money::USD(200), Money::USD(300)); // Money::USD(600)

// Formatters
Money::USD(500)->format(); // $5.00
Money::USD(199)->format(null, null, \NumberFormatter::DECIMAL); // 1,99
Money::XBT(41000000)->formatByBitcoin(); // \xC9\x830.41
Money::USD(500)->formatByDecimal(); // 5.00
Money::USD(500)->formatByIntl(); // $5.00
Money::USD(199)->formatByIntl(null, null, \NumberFormatter::DECIMAL); // 1,99
Money::USD(500)->formatByIntlLocalizedDecimal(); // $5.00
Money::USD(199)->formatByIntlLocalizedDecimal(null, null, \NumberFormatter::DECIMAL) // 1.99

// Parsers
Money::parse('$1.00'); // Money::USD(100)
Money::parseByBitcoin("\xC9\x830.41"); // Money::XBT(41000000)
Money::parseByDecimal('1.00', 'USD'); // Money::USD(100)
Money::parseByIntl('$1.00'); // Money::USD(100)
Money::parseByIntlLocalizedDecimal('1.00', 'USD'); // Money::USD(100)
```

### Create your formatter

```php
class MyFormatter implements \Money\MoneyFormatter
{
    public function format(\Money\Money $money)
    {
        return 'My Formatter';
    }
}

Money::USD(500)->formatByFormatter(new MyFormatter()); // My Formatter
```

## Helpers

```php
currency('USD');
money(500); // To use default currency present in `config/money.php`
money(500, 'USD');

// Aggregation
money_min(money(100, 'USD'), money(200, 'USD'), money(300, 'USD')); // Money::USD(100)
money_max(money(100, 'USD'), money(200, 'USD'), money(300, 'USD')); // Money::USD(300)
money_avg(money(100, 'USD'), money(200, 'USD'), money(300, 'USD')); // Money::USD(200)
money_sum(money(100, 'USD'), money(200, 'USD'), money(300, 'USD')); // Money::USD(600)

// Parsers
money_parse('$5.00'); // Money::USD(100)
money_parse_by_bitcoin("\xC9\x830.41"); // Money::XBT(41000000)
money_parse_by_decimal('1.00', 'USD'); // Money::USD(100)
money_parse_by_intl('$1.00'); // Money::USD(100)
money_parse_by_intl_localized_decimal('1.00', 'USD'); // Money::USD(100)
```

## Blade Extensions

```php
@currency('USD')
@money(500) // To use default currency present in `config/money.php`
@money(500, 'USD')

// Aggregation
@money_min(@money(100, 'USD'), @money(200, 'USD'), @money(300, 'USD')) // Money::USD(100)
@money_max(@money(100, 'USD'), @money(200, 'USD'), @money(300, 'USD')) // Money::USD(300)
@money_avg(@money(100, 'USD'), @money(200, 'USD'), @money(300, 'USD')) // Money::USD(200)
@money_sum(@money(100, 'USD'), @money(200, 'USD'), @money(300, 'USD')) // Money::USD(600)

// Parsers
@money_parse('$5.00') // Money::USD(100)
@money_parse_by_bitcoin("\xC9\x830.41") // Money::XBT(41000000)
@money_parse_by_decimal('1.00', 'USD') // Money::USD(100)
@money_parse_by_intl('$1.00') // Money::USD(100)
@money_parse_by_intl_localized_decimal('1.00', 'USD') // Money::USD(100)
```
