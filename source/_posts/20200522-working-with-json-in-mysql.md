---
title: 【转】Working with JSON in MySQL
date: 2020-05-22 17:17:17
tags:
  - mysql
categories: MySQL
---
SQL databases tend to be rigid.

If you have worked with them, you would agree that database design though it seems easier, is a lot trickier in practice. SQL databases believe in structure, that is why it's called structured query language.

On the other side of the horizon, we have the NoSQL databases, also called schema-less databases that encourage flexibility. In schema-less databases, there is no imposed structural restriction, only data to be saved.

### Table of Contents

- [Why Use JSON](https://scotch.io/tutorials/working-with-json-in-mysql#toc-why-use-json)
- [The Schema](https://scotch.io/tutorials/working-with-json-in-mysql#toc-the-schema)
- [The CRUD Operations](https://scotch.io/tutorials/working-with-json-in-mysql#toc-the-crud-operations)
- [A Primer for Web Applications](https://scotch.io/tutorials/working-with-json-in-mysql#toc-a-primer-for-web-applications)
- [Creating the Migrations](https://scotch.io/tutorials/working-with-json-in-mysql#toc-creating-the-migrations)
- [Creating the Models](https://scotch.io/tutorials/working-with-json-in-mysql#toc-creating-the-models)
- [Resource Operations](https://scotch.io/tutorials/working-with-json-in-mysql#toc-resource-operations)
- [Curtains](https://scotch.io/tutorials/working-with-json-in-mysql#toc-curtains)

Though every tool has it's use case, sometimes things call for a hybrid approach.

What if you could structure some parts of your database and leave others to be flexible?

[MySQL version 5.7.8](https://dev.mysql.com/doc/relnotes/mysql/5.7/en/news-5-7-8.html) introduces a JSON data type that allows you to accomplish that.

In this tutorial, you are going to learn.

1. How to design your database tables using JSON fields.
2. The various JSON based functions available in MYSQL to create, read, update, and delete rows.
3. How to work with JSON fields using the Eloquent ORM in Laravel.

## [Why Use JSON](https://scotch.io/tutorials/working-with-json-in-mysql#toc-why-use-json)

At this moment, you are probably asking yourself why would you want to use JSON when MySQL has been catering to a wide variety of database needs even before it introduced a JSON data type.

The answer lies in the use-cases where you would probably use a make-shift approach.

Let me explain with an example.

Suppose you are building a web application where you have to save a user's configuration/preferences in the database.

Generally, you can create a separate database table with the `id`, `user_id`, `key`, and `value` fields or save it as a formatted string that you can parse at runtime.

However, this works well for a small number of users. If you have about a thousand users and five configuration keys, you are looking at a table with five thousand records that addresses a very small feature of your application.

Or if you are taking the formatted string route, extraneous code that only compounds your server load.

Using a JSON data type field to save a user's configuration in such a scenario can spare you a database table's space and bring down the number of records, which were being saved separately, to be the same as the number of users.

And you get the added benefit of not having to write any JSON parsing code, the ORM or the language runtime takes care of it.

## [The Schema](https://scotch.io/tutorials/working-with-json-in-mysql#toc-the-schema)

Before we dive into using all the cool JSON stuff in MySQL, we are going to need a sample database to play with.

So, let's get our database schema out of the way first.

We are going to consider the use case of an online store that houses multiple brands and a variety of electronics.

Since different electronics have different attributes(compare a Macbook with a Vacuumn Cleaner) that buyers are interested in, typically the [Entity–attribute–value model (EAV)](https://en.wikipedia.org/wiki/Entity–attribute–value_model) pattern is used.

However, since we now have the option to use a JSON data type, we are going to drop EAV.

For a start, our database will be named `e_store` and has three tables only named, `brands`, `categories`, and `products` respectively.

Our `brands` and `categories` tables will be pretty similar, each having an `id` and a `name` field.

```sql
CREATE DATABASE IF NOT EXISTS `e_store`
DEFAULT CHARACTER SET utf8
DEFAULT COLLATE utf8_general_ci;

SET default_storage_engine = INNODB;

CREATE TABLE `e_store`.`brands`(
    `id` INT UNSIGNED NOT NULL auto_increment ,
    `name` VARCHAR(250) NOT NULL ,
    PRIMARY KEY(`id`)
);

CREATE TABLE `e_store`.`categories`(
    `id` INT UNSIGNED NOT NULL auto_increment ,
    `name` VARCHAR(250) NOT NULL ,
    PRIMARY KEY(`id`)
);
```

The objective of these two tables will be to house the product categories and the brands that provide these products.

While we are at it, let us go ahead and seed some data into these tables to use later.

```sql
/* Brands */
INSERT INTO `e_store`.`brands`(`name`)
VALUES
    ('Samsung');

INSERT INTO `e_store`.`brands`(`name`)
VALUES
    ('Nokia');

INSERT INTO `e_store`.`brands`(`name`)
VALUES
    ('Canon');

/* Types of electronic device */
INSERT INTO `e_store`.`categories`(`name`)
VALUES
    ('Television');

INSERT INTO `e_store`.`categories`(`name`)
VALUES
    ('Mobilephone');

INSERT INTO `e_store`.`categories`(`name`)
VALUES
    ('Camera');
```


Next, is the business area of this tutorial.

We are going to create a `products` table with the `id`, `name`, `brand_id`, `category_id`, and `attributes` fields.

```sql
CREATE TABLE `e_store`.`products`(
    `id` INT UNSIGNED NOT NULL AUTO_INCREMENT ,
    `name` VARCHAR(250) NOT NULL ,
    `brand_id` INT UNSIGNED NOT NULL ,
    `category_id` INT UNSIGNED NOT NULL ,
    `attributes` JSON NOT NULL ,
    PRIMARY KEY(`id`) ,
    INDEX `CATEGORY_ID`(`category_id` ASC) ,
    INDEX `BRAND_ID`(`brand_id` ASC) ,
    CONSTRAINT `brand_id` FOREIGN KEY(`brand_id`) REFERENCES `e_store`.`brands`(`id`) ON DELETE RESTRICT ON UPDATE CASCADE ,
    CONSTRAINT `category_id` FOREIGN KEY(`category_id`) REFERENCES `e_store`.`categories`(`id`) ON DELETE RESTRICT ON UPDATE CASCADE
);
```

Our table definition specifies foreign key constraints for the `brand_id` and `category_id` fields, specifying that they reference the `brands` and `categories` table respectively. We have also specified that the referenced rows should not be allowed to delete and if updated, the changes should reflect in the references as well.

The `attributes` field's column type has been declared to be JSON which is the native data type now available in MySQL. This allows us to use the various JSON related constructs in MySQL on our `attributes` field.

Here is an entity relationship diagram of our created database.


> Our database design is not the best in terms of efficiency and accuracy. There is no price column in the `products` table and we could do with putting a product into multiple categories. However, the purpose of this tutorial is not to teach database design but rather how to model objects of different nature in a single table using MySQL's JSON features.

## [The CRUD Operations](https://scotch.io/tutorials/working-with-json-in-mysql#toc-the-crud-operations)

Let us look at how to create, read, update, and delete data in a JSON field.

### Create

Creating a record in the database with a JSON field is pretty simple.

All you need to do is add valid JSON as the field value in your insert statement.

```sql
/* Let's sell some televisions */
INSERT INTO `e_store`.`products`(
    `name` ,
    `brand_id` ,
    `category_id` ,
    `attributes`
)
VALUES(
    'Prime' ,
    '1' ,
    '1' ,
    '{"screen": "50 inch", "resolution": "2048 x 1152 pixels", "ports": {"hdmi": 1, "usb": 3}, "speakers": {"left": "10 watt", "right": "10 watt"}}'
);

INSERT INTO `e_store`.`products`(
    `name` ,
    `brand_id` ,
    `category_id` ,
    `attributes`
)
VALUES(
    'Octoview' ,
    '1' ,
    '1' ,
    '{"screen": "40 inch", "resolution": "1920 x 1080 pixels", "ports": {"hdmi": 1, "usb": 2}, "speakers": {"left": "10 watt", "right": "10 watt"}}'
);

INSERT INTO `e_store`.`products`(
    `name` ,
    `brand_id` ,
    `category_id` ,
    `attributes`
)
VALUES(
    'Dreamer' ,
    '1' ,
    '1' ,
    '{"screen": "30 inch", "resolution": "1600 x 900 pixles", "ports": {"hdmi": 1, "usb": 1}, "speakers": {"left": "10 watt", "right": "10 watt"}}'
);

INSERT INTO `e_store`.`products`(
    `name` ,
    `brand_id` ,
    `category_id` ,
    `attributes`
)
VALUES(
    'Bravia' ,
    '1' ,
    '1' ,
    '{"screen": "25 inch", "resolution": "1366 x 768 pixels", "ports": {"hdmi": 1, "usb": 0}, "speakers": {"left": "5 watt", "right": "5 watt"}}'
);

INSERT INTO `e_store`.`products`(
    `name` ,
    `brand_id` ,
    `category_id` ,
    `attributes`
)
VALUES(
    'Proton' ,
    '1' ,
    '1' ,
    '{"screen": "20 inch", "resolution": "1280 x 720 pixels", "ports": {"hdmi": 0, "usb": 0}, "speakers": {"left": "5 watt", "right": "5 watt"}}'
);
```


Instead of laying out the JSON object yourself, you can also use the built-in `JSON_OBJECT` function.

The `JSON_OBJECT` function accepts a list of key/value pairs in the form `JSON_OBJECT(key1, value1, key2, value2, ... key(n), value(n))` and returns a JSON object.

```sql
/* Let's sell some mobilephones */
INSERT INTO `e_store`.`products`(
    `name` ,
    `brand_id` ,
    `category_id` ,
    `attributes`
)
VALUES(
    'Desire' ,
    '2' ,
    '2' ,
    JSON_OBJECT(
        "network" ,
        JSON_ARRAY("GSM" , "CDMA" , "HSPA" , "EVDO") ,
        "body" ,
        "5.11 x 2.59 x 0.46 inches" ,
        "weight" ,
        "143 grams" ,
        "sim" ,
        "Micro-SIM" ,
        "display" ,
        "4.5 inches" ,
        "resolution" ,
        "720 x 1280 pixels" ,
        "os" ,
        "Android Jellybean v4.3"
    )
);

INSERT INTO `e_store`.`products`(
    `name` ,
    `brand_id` ,
    `category_id` ,
    `attributes`
)
VALUES(
    'Passion' ,
    '2' ,
    '2' ,
    JSON_OBJECT(
        "network" ,
        JSON_ARRAY("GSM" , "CDMA" , "HSPA") ,
        "body" ,
        "6.11 x 3.59 x 0.46 inches" ,
        "weight" ,
        "145 grams" ,
        "sim" ,
        "Micro-SIM" ,
        "display" ,
        "4.5 inches" ,
        "resolution" ,
        "720 x 1280 pixels" ,
        "os" ,
        "Android Jellybean v4.3"
    )
);

INSERT INTO `e_store`.`products`(
    `name` ,
    `brand_id` ,
    `category_id` ,
    `attributes`
)
VALUES(
    'Emotion' ,
    '2' ,
    '2' ,
    JSON_OBJECT(
        "network" ,
        JSON_ARRAY("GSM" , "CDMA" , "EVDO") ,
        "body" ,
        "5.50 x 2.50 x 0.50 inches" ,
        "weight" ,
        "125 grams" ,
        "sim" ,
        "Micro-SIM" ,
        "display" ,
        "5.00 inches" ,
        "resolution" ,
        "720 x 1280 pixels" ,
        "os" ,
        "Android KitKat v4.3"
    )
);

INSERT INTO `e_store`.`products`(
    `name` ,
    `brand_id` ,
    `category_id` ,
    `attributes`
)
VALUES(
    'Sensation' ,
    '2' ,
    '2' ,
    JSON_OBJECT(
        "network" ,
        JSON_ARRAY("GSM" , "HSPA" , "EVDO") ,
        "body" ,
        "4.00 x 2.00 x 0.75 inches" ,
        "weight" ,
        "150 grams" ,
        "sim" ,
        "Micro-SIM" ,
        "display" ,
        "3.5 inches" ,
        "resolution" ,
        "720 x 1280 pixels" ,
        "os" ,
        "Android Lollypop v4.3"
    )
);

INSERT INTO `e_store`.`products`(
    `name` ,
    `brand_id` ,
    `category_id` ,
    `attributes`
)
VALUES(
    'Joy' ,
    '2' ,
    '2' ,
    JSON_OBJECT(
        "network" ,
        JSON_ARRAY("CDMA" , "HSPA" , "EVDO") ,
        "body" ,
        "7.00 x 3.50 x 0.25 inches" ,
        "weight" ,
        "250 grams" ,
        "sim" ,
        "Micro-SIM" ,
        "display" ,
        "6.5 inches" ,
        "resolution" ,
        "1920 x 1080 pixels" ,
        "os" ,
        "Android Marshmallow v4.3"
    )
);
```


Notice the `JSON_ARRAY` function which returns a JSON array when passed a set of values.

If you specify a single key multiple times, only the first key/value pair will be retained. This is called normalizing the JSON in MySQL's terms. Also, as part of normalization, the object keys are sorted and the extra white-space between key/value pairs is removed.

Another function that we can use to create JSON objects is the `JSON_MERGE` function.

The `JSON_MERGE` function takes multiple JSON objects and produces a single, aggregate object.

```sql
/* Let's sell some cameras */
INSERT INTO `e_store`.`products`(
    `name` ,
    `brand_id` ,
    `category_id` ,
    `attributes`
)
VALUES(
    'Explorer' ,
    '3' ,
    '3' ,
    JSON_MERGE(
        '{"sensor_type": "CMOS"}' ,
        '{"processor": "Digic DV III"}' ,
        '{"scanning_system": "progressive"}' ,
        '{"mount_type": "PL"}' ,
        '{"monitor_type": "LCD"}'
    )
);

INSERT INTO `e_store`.`products`(
    `name` ,
    `brand_id` ,
    `category_id` ,
    `attributes`
)
VALUES(
    'Runner' ,
    '3' ,
    '3' ,
    JSON_MERGE(
        JSON_OBJECT("sensor_type" , "CMOS") ,
        JSON_OBJECT("processor" , "Digic DV II") ,
        JSON_OBJECT("scanning_system" , "progressive") ,
        JSON_OBJECT("mount_type" , "PL") ,
        JSON_OBJECT("monitor_type" , "LED")
    )
);

INSERT INTO `e_store`.`products`(
    `name` ,
    `brand_id` ,
    `category_id` ,
    `attributes`
)
VALUES(
    'Traveler' ,
    '3' ,
    '3' ,
    JSON_MERGE(
        JSON_OBJECT("sensor_type" , "CMOS") ,
        '{"processor": "Digic DV II"}' ,
        '{"scanning_system": "progressive"}' ,
        '{"mount_type": "PL"}' ,
        '{"monitor_type": "LCD"}'
    )
);

INSERT INTO `e_store`.`products`(
    `name` ,
    `brand_id` ,
    `category_id` ,
    `attributes`
)
VALUES(
    'Walker' ,
    '3' ,
    '3' ,
    JSON_MERGE(
        '{"sensor_type": "CMOS"}' ,
        '{"processor": "Digic DV I"}' ,
        '{"scanning_system": "progressive"}' ,
        '{"mount_type": "PL"}' ,
        '{"monitor_type": "LED"}'
    )
);

INSERT INTO `e_store`.`products`(
    `name` ,
    `brand_id` ,
    `category_id` ,
    `attributes`
)
VALUES(
    'Jumper' ,
    '3' ,
    '3' ,
    JSON_MERGE(
        '{"sensor_type": "CMOS"}' ,
        '{"processor": "Digic DV I"}' ,
        '{"scanning_system": "progressive"}' ,
        '{"mount_type": "PL"}' ,
        '{"monitor_type": "LCD"}'
    )
);
```


There is a lot happening in these insert statements and it can get a bit confusing. However, it is pretty simple.

We are only passing objects to the `JSON_MERGE` function. Some of them have been constructed using the `JSON_OBJECT` function we saw previously whereas others have been passed as valid JSON strings.

In case of the `JSON_MERGE` function, if a key is repeated multiple times, it's value is retained as an array in the output.

A proof of concept is in order I suppose.

```sql
/* output: {"network": ["GSM", "CDMA", "HSPA", "EVDO"]} */
SELECT JSON_MERGE(
    '{"network": "GSM"}' ,
    '{"network": "CDMA"}' ,
    '{"network": "HSPA"}' ,
    '{"network": "EVDO"}'
);
```

We can confirm all our queries were run successfully using the `JSON_TYPE` function which gives us the field value type.

```sql
/* output: OBJECT */
SELECT JSON_TYPE(attributes) FROM `e_store`.`products`;
```

### Read

Right, we have a few products in our database to work with.

For typical MySQL values that are not of type JSON, a where clause is pretty straight-forward. Just specify the column, an operator, and the values you need to work with.

Heuristically, when working with JSON columns, this does not work.

```sql
/* It's not that simple */
SELECT
    *
FROM
    `e_store`.`products`
WHERE
    attributes = '{"ports": {"usb": 3, "hdmi": 1}, "screen": "50 inch", "speakers": {"left": "10 watt", "right": "10 watt"}, "resolution": "2048 x 1152 pixels"}';
```

When you wish to narrow down rows using a JSON field, you should be familiar with the concept of a path expression.

The most simplest definition of a path expression(think JQuery selectors) is it's used to specify which parts of the JSON document to work with.

The second piece of the puzzle is the `JSON_EXTRACT` function which accepts a path expression to navigate through JSON.

Let us say we are interested in the range of televisions that have atleast a single USB and HDMI port.

```sql
SELECT
    *
FROM
    `e_store`.`products`
WHERE
    `category_id` = 1
AND JSON_EXTRACT(`attributes` , '$.ports.usb') > 0
AND JSON_EXTRACT(`attributes` , '$.ports.hdmi') > 0;
```


The first argument to the `JSON_EXTRACT` function is the JSON to apply the path expression to which is the `attributes` column. The `$` symbol tokenizes the object to work with. The `$.ports.usb` and `$.ports.hdmi` path expressions translate to "take the usb key under ports" and "take the hdmi key under ports" respectively.

Once we have extracted the keys we are interested in, it is pretty simple to use the MySQL operators such as `>` on them.

Also, the `JSON_EXTRACT` function has the alias `->` that you can use to make your queries more readable.

Revising our previous query.

```sql
SELECT
    *
FROM
    `e_store`.`products`
WHERE
    `category_id` = 1
AND `attributes` -> '$.ports.usb' > 0
AND `attributes` -> '$.ports.hdmi' > 0;
```

### Update

In order to update JSON values, we are going to use the `JSON_INSERT`, `JSON_REPLACE`, and `JSON_SET` functions. These functions also require a path expression to specify which parts of the JSON object to modify.

The output of these functions is a valid JSON object with the changes applied.

Let us modify all mobilephones to have a chipset property as well.

```sql
UPDATE `e_store`.`products`
SET `attributes` = JSON_INSERT(
    `attributes` ,
    '$.chipset' ,
    'Qualcomm'
)
WHERE
    `category_id` = 2;
```


The `$.chipset` path expression identifies the position of the `chipset` property to be at the root of the object.

Let us update the `chipset` property to be more descriptive using the `JSON_REPLACE` function.

```sql
UPDATE `e_store`.`products`
SET `attributes` = JSON_REPLACE(
    `attributes` ,
    '$.chipset' ,
    'Qualcomm Snapdragon'
)
WHERE
    `category_id` = 2;
```


Easy peasy!

Lastly, we have the `JSON_SET` function which we will use to specify our televisions are pretty colorful.

```sql
UPDATE `e_store`.`products`
SET `attributes` = JSON_SET(
    `attributes` ,
    '$.body_color' ,
    'red'
)
WHERE
    `category_id` = 1;
```


All of these functions seem identical but there is a difference in the way they behave.

The `JSON_INSERT` function will only add the property to the object if it does not exists already.

The `JSON_REPLACE` function substitutes the property only if it is found.

The `JSON_SET` function will add the property if it is not found else replace it.

### Delete

There are two parts to deleting that we will look at.

The first is to delete a certain key/value from your JSON columns whereas the second is to delete rows using a JSON column.

Let us say we are no longer providing the `mount_type` information for cameras and wish to remove it for all cameras.

We will do it using the `JSON_REMOVE` function which returns the updated JSON after removing the specified key based on the path expression.

```sql
UPDATE `e_store`.`products`
SET `attributes` = JSON_REMOVE(`attributes` , '$.mount_type')
WHERE
    `category_id` = 3;
```


For the second case, we also do not provide mobilephones anymore that have the Jellybean version of the Android OS.

```sql
DELETE FROM `e_store`.`products`
WHERE `category_id` = 2
AND JSON_EXTRACT(`attributes` , '$.os') LIKE '%Jellybean%';
```

As stated previously, working with a specific attribute requires the use of the `JSON_EXTRACT` function so in order to apply the `LIKE` operator, we have first extracted the `os` property of mobilephones(with the help of `category_id`) and deleted all records that contain the string `Jellybean`.

## [A Primer for Web Applications](https://scotch.io/tutorials/working-with-json-in-mysql#toc-a-primer-for-web-applications)

The old days of directly working with a database are way behind us.

These days, frameworks insulate developers from lower-level operations and it almost feels alien for a framework fanatic not to be able to translate his/her database knowledge into an object relational mapper.

For the purpose of not leaving such developers heartbroken and wondering about their existence and purpose in the universe, we are going to look at how to go about the business of JSON columns in the Laravel framework.

> We will only be focusing on the parts that overlap with our subject matter which deals with JSON columns. An in-depth tutorial on the Laravel framework is beyond the scope of this piece.

## [Creating the Migrations](https://scotch.io/tutorials/working-with-json-in-mysql#toc-creating-the-migrations)

Make sure to configure your Laravel application to use a MySQL database.

We are going to create three migrations for `brands`, `categories`, and `products` respectively.

```bash
$ php artisan make:migration create_brands
$ php artisan make:migration create_categories
$ php artisan make:migration create_products
```

The `create_brands` and `create_categories` migrations are pretty similar and and a regulation for Laravel developers.

```php
/* database/migrations/create_brands.php */

<?php

use Illuminate\Support\Facades\Schema;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Database\Migrations\Migration;

class CreateBrands extends Migration
{
    /**
     * Run the migrations.
     *
     * @return void
     */
    public function up()
    {
        Schema::create('brands', function(Blueprint $table){
            $table->engine = 'InnoDB';
            $table->increments('id');
            $table->string('name');
            $table->timestamps();
        });
    }

    /**
     * Reverse the migrations.
     *
     * @return void
     */
    public function down()
    {
        Schema::drop('brands');
    }
}

/* database/migrations/create_categories.php */

<?php

use Illuminate\Support\Facades\Schema;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Database\Migrations\Migration;

class CreateCategories extends Migration
{
    /**
     * Run the migrations.
     *
     * @return void
     */
    public function up()
    {
        Schema::create('categories', function(Blueprint $table){
            $table->engine = 'InnoDB';
            $table->increments('id');
            $table->string('name');
            $table->timestamps();
        });
    }

    /**
     * Reverse the migrations.
     *
     * @return void
     */
    public function down()
    {
        Schema::drop('categories');
    }
}
```

The `create_products` migration will also have the directives for indexes and foreign keys.

```php
/* database/migrations/create_products */

<?php

use Illuminate\Support\Facades\Schema;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Database\Migrations\Migration;

class CreateProducts extends Migration
{
    /**
     * Run the migrations.
     *
     * @return void
     */
    public function up()
    {
        Schema::create('products', function(Blueprint $table){
            $table->engine = 'InnoDB';
            $table->increments('id');
            $table->string('name');
            $table->unsignedInteger('brand_id');
            $table->unsignedInteger('category_id');
            $table->json('attributes');
            $table->timestamps();
            // foreign key constraints
            $table->foreign('brand_id')->references('id')->on('brands')->onDelete('restrict')->onUpdate('cascade');
            $table->foreign('category_id')->references('id')->on('categories')->onDelete('restrict')->onUpdate('cascade');
            // indexes
            $table->index('brand_id');
            $table->index('category_id');
        });
    }

    /**
     * Reverse the migrations.
     *
     * @return void
     */
    public function down()
    {
        Schema::drop('products');
    }
}
```

Pay attention to the `$table->json('attributes');` statement in the migration.

Just like creating any other table field using the appropriate data type named method, we have created a JSON column using the `json` method with the name `attributes`.

Also, this only works for database engines that support the JSON data type.

Engines, such as older versions of MySQL will not be able to carry out these migrations.

## [Creating the Models](https://scotch.io/tutorials/working-with-json-in-mysql#toc-creating-the-models)

Other than associations, there is not much needed to set up our models so let's run through them quickly.

```php
/* app/Brand.php */

<?php

namespace App;

use Illuminate\Database\Eloquent\Model;

class Brand extends Model
{
    // A brand has many products
    public function products(){
        return $this->hasMany('Product')
    }
}

/* app/Category.php */

<?php

namespace App;

use Illuminate\Database\Eloquent\Model;

class Category extends Model
{
    // A category has many products
    public function products(){
        return $this->hasMany('Product')
    }
}

/* app/Product.php */

<?php

namespace App;

use Illuminate\Database\Eloquent\Model;

class Product extends Model
{
    // Cast attributes JSON to array
    protected $casts = [
        'attributes' => 'array'
    ];

    // Each product has a brand
    public function brand(){
        return $this->belongsTo('Brand');
    }

    // Each product has a category
    public function category(){
        return $this->belongsTo('Category');
    }
}
```

Again, our `Product` model needs a special mention.

The `$casts` array which has the key `attributes` set to `array` makes sure whenever a product is fetched from the database, it's `attributes` JSON is converted to an associated array.

We will see later in the tutorial how this facilitates us to update records from our controller actions.

## [Resource Operations](https://scotch.io/tutorials/working-with-json-in-mysql#toc-resource-operations)

### Creating a Product

Speaking of the admin panel, the parameters to create a product maybe coming in through different routes since we have a number of product categories. You may also have different views to create, edit, show, and delete a product.

For example, a form to add a camera requires different input fields than a form to add a mobilephone so they warrant separate views.

Moreover, once you have the user input data, you will most probabaly run it through a request validator, separate for the camera, and the mobilephone each.

The final step would be to create the product through Eloquent.

We will be focusing on the camera resource for the rest of this tutorial. Other products can be addressed using the code produced in a similar manner.

Assuming we are saving a camera and the form fields are named as the respective camera attributes, here is the controller action.

```php
// creates product in database
// using form fields
public function store(Request $request){
    // create object and set properties
    $camera = new \App\Product();
    $camera->name = $request->name;
    $camera->brand_id = $request->brand_id;
    $camera->category_id = $request->category_id;
    $camera->attributes = json_encode([
        'processor' => $request->processor,
        'sensor_type' => $request->sensor_type,
        'monitor_type' => $request->monitor_type,
        'scanning_system' => $request->scanning_system,
    ]);
    // save to database
    $camera->save();
    // show the created camera
    return view('product.camera.show', ['camera' => $camera]);
}
```

### Fetching Products

Recall the `$casts` array we declared earlier in the `Product` model. It will help us read and edit a product by treating attributes as an associative array.

```php
// fetches a single product
// from database
public function show($id){
    $camera = \App\Product::find($id);
    return view('product.camera.show', ['camera' => $camera]);
}
```

Your view would use the `$camera` variable in the following manner.

```html
<table>
    <tr>
        <td>Name</td>
        <td>{{ $camera->name }}</td>
    </tr>
    <tr>
        <td>Brand ID</td>
        <td>{{ $camera->brand_id }}</td>
    </tr>
    <tr>
        <td>Category ID</td>
        <td>{{ $camera->category_id }}</td>
    </tr>
    <tr>
        <td>Processor</td>
        <td>{{ $camera->attributes['processor'] }}</td>
    </tr>
    <tr>
        <td>Sensor Type</td>
        <td>{{ $camera->attributes['sensor_type'] }}</td>
    </tr>
    <tr>
        <td>Monitor Type</td>
        <td>{{ $camera->attributes['monitor_type'] }}</td>
    </tr>
    <tr>
        <td>Scanning System</td>
        <td>{{ $camera->attributes['scanning_system'] }}</td>
    </tr>
</table>
```

### Editing a Product

As shown in the previous section, you can easily fetch a product and pass it to the view, which in this case would be the edit view.

You can use the product variable to pre-populate form fields on the edit page.

Updating the product based on the user input will be pretty similar to the `store` action we saw earlier, only that instead of creating a new product, you will fetch it first from the database before updating it.

### Searching Based on JSON Attributes

The last piece of the puzzle that remains to discuss is querying JSON columns using the Eloquent ORM.

If you have a search page that allows cameras to be searched based on their specifications provided by the user, you can do so with the following code.

```php
// searches cameras by user provided specifications
public function search(Request $request){
    $cameras = \App\Product::where([
        ['attributes->processor', 'like', $request->processor],
        ['attributes->sensor_type', 'like', $request->sensor_type],
        ['attributes->monitor_type', 'like', $request->monitor_type],
        ['attributes->scanning_system', 'like', $request->scanning_system]
    ])->get();
    return view('product.camera.search', ['cameras' => $cameras]);
}
```

The retrived records will now be available to the `product.camera.search` view as a `$cameras` collection.

### Deleting a Product

Using a non-JSON column attribute, you can delete products by specifying a where clause and then calling the `delete` method.

For example, in case of an ID.

```php
\App\Product::where('id', $id)->delete();
```

For JSON columns, specify a where clause using a single or multiple attributes and then call the `delete` method.

```php
// deletes all cameras with the sensor_type attribute as CMOS
\App\Product::where('attributes->sensor_type', 'CMOS')->delete();
}
```

## [Curtains](https://scotch.io/tutorials/working-with-json-in-mysql#toc-curtains)

We have barely scratched the surface when it comes to using JSON columns in MySQL.

Whenever you need to save data as key/value pairs in a separate table or work with flexible attributes for an entity, you should consider using a JSON data type field instead as it can heavily contribute to compressing your database design.

If you are interested in diving deeper, the [MySQL documentation](https://dev.mysql.com/doc/refman/5.7/en/json.html) is a great resource to explore JSON concepts futher.



来源：

[How To Work with JSON in MySQL | DigitalOcean](https://www.digitalocean.com/community/tutorials/working-with-json-in-mysql)
