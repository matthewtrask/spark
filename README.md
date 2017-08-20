# Spark 1.0 fork

## Introduction

This is a non-deleted fork of Laravel Spark 1.0, which eventually became a paid product (available at https://spark.laravel.com/).

The latest supported Laravel version is currently 5.2 (compatibility with Laravel 5.3 was added in Spark 2.0).

## Installation

Create a new Laravel 5.2 application and give it a name (`project-name` here):

```
composer create-project laravel/laravel project-name 5.2.*

cd project-name
```

Add the Spark installer and install Spark (answer "no" to all questions it will ask you):

```
composer require "nkkollaw/spark-installer=~1.0"

vendor/nkkollaw/spark-installer/spark install
```

Create a database for your app.

Edit `.env` to add your database info and your URL if different than `http://localhost`. You might want to also set the `AUTHY_KEY`, `STRIPE_KEY`, and `STRIPE_SECRET` environment variables or do it later.

Migrate your database:

```
php artisan migrate
```

Install NPM dependencies:

```
npm install
```

Execute Gulp tasks:

```
gulp
```

It should work.

### Post-install

You may also wish to review the `SparkServiceProvider` class that was installed in your application. This provider is the central location for customizing your Spark installation.

### Notes

Installing Spark should be done while crafting your application. Installing Spark after running commands such as `php artisan app:name MyApp` may result in errors when trying to install.

From now on you're on your own. The rest of this README has not been checked and things may or may not work (although they should).

## Defining Subscription Plans

Subscription plans may be defined in your `app/Providers/SparkServiceProvider.php` file. This file contains a `customizeSubscriptionPlans` method. Within this method, you may define all of your application's subscription plans. There are a few examples in the method to get you started.

When defining a Spark plan, the `plan` method accepts two arguments: the name of the plan and the Stripe ID of the plan. Be sure that the Stripe ID given to the `plan` method corresponds to a plan ID on your Stripe account:
```php
	Spark::plan('Display Name', 'stripe-id')
		->price(10)
		->features([
			//
		]);
```

### Yearly Plans

To define a yearly plan, simply call the `yearly` method on the plan definition:
```php
	Spark::plan('Basic', 'basic-yearly')
		->price(100)
		->yearly()
		->features(
			//
		);
```
### Coupons

To use a coupon, simply create the coupon on Stripe and access the `/register` route with a `coupon` query string variable that matches the ID of the coupon on Stripe.

	    http://stripe.app/register?coupon=code

Site-wide promotions may be run using the `Spark::promotion` method within your `SparkServiceProvider`:
```php
	Spark::promotion('coupon-code');
```
## Teams

To enable teams, simply use the `CanJoinTeams` trait on your `User` model. The trait has already been imported in the top of the file, so you only need to add it to the model itself:
```php
	class User extends Model implements TwoFactorAuthenticatableContract,
	                                    BillableContract,
	                                    CanResetPasswordContract
	{
	    use Billable, CanJoinTeams, CanResetPassword, TwoFactorAuthenticatable;
	}
```
Once teams are enabled, a team name will be required during registration, and a `Teams` tab will be available in the user settings dashboard.

### Roles

Team roles may be defined in the `customizeRoles` method of the `SparkServiceProvider`.

<a name="customizing-spark-views"></a>
## Customizing Spark Views

You may publish Spark's common Blade views by using the `vendor:publish` command:

```
	php artisan vendor:publish --tag=spark-basics
```

All published views will be placed in `resources/views/vendor/spark`.

If you would like to publish every Spark view, you may use the `spark-full` tag:

```
	php artisan vendor:publish --tag=spark-full
```

## Customizing Spark JavaScript

The `resources/assets/js/core/components.js` file contains the statements to load some common Spark Vue components. [Vue](http://vuejs.org) is the JavaScript framework used by the Spark registration and settings screens.

You are free to change any of these require statements to load your own Vue component for a given screen. Most likely, you will want to copy the original component as a starting point for your customization.
