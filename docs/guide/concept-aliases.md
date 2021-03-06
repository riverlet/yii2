Aliases
=======

Aliases are used to represent file paths or URLs to avoid hard-coding absolute paths or URLs in your code.
An alias must start with a `@` character so that it can be differentiated from file paths and URLs.
For example, the alias `@yii` represents the installation path of the Yii framework, while `@web` represents
the base URL for the currently running Web application.


Defining Aliases <a name="defining-aliases"></a>
----------------

You can call [[Yii::setAlias()]] to define an alias for a given file path or URL. For example,

```php
// an alias of file path
Yii::setAlias('@foo', '/path/to/foo');

// an alias of URL
Yii::setAlias('@bar', 'http://www.example.com');
```

> Note: A file path or URL being aliased may NOT necessarily refer to an existing file or resource.

Given an alias, you may derive a new alias (without the need of calling [[Yii::setAlias()]]) by appending
a slash `/` followed with one or several path segments. We call the aliases defined via [[Yii::setAlias()]]
*root aliases*, while the aliases derived from them *derived aliases*. For example, `@foo` is a root alias,
while `@foo/bar/file.php` is a derived alias.

You can define an alias using another alias (either root alias or derived alias is fine):

```php
Yii::setAlias('@foobar', '@foo/bar');
```

Root aliases are usually defined during the [bootstrapping](runtime-bootstrapping.md) stage.
For example, you may call [[Yii::setAlias()]] in the [entry script](structure-entry-scripts.md).
For convenience, [Application](structure-applications.md) provides a writable property named `aliases`
that you can configure in the application [configuration](concept-configurations.md), like the following,

```php
return [
    // ...
    'aliases' => [
        '@foo' => '/path/to/foo',
        '@bar' => 'http://www.example.com',
    ],
];
```


Resolving Aliases <a name="resolving-aliases"></a>
-----------------

You can call [[Yii::getAlias()]] to resolve a root alias into the file path or URL it is representing.
The same method can also resolve a derived alias into the corresponding file path or URL. For example,

```php
echo Yii::getAlias('@foo');               // displays: /path/to/foo
echo Yii::getAlias('@bar');               // displays: http://www.example.com
echo Yii::getAlias('@foo/bar/file.php');  // displays: /path/to/foo/bar/file.php
```

The path/URL represented by a derived alias is determined by replacing the root alias part with its corresponding
path/URL in the derived alias.

> Note: The [[Yii::getAlias()]] method does not check whether the resulting path/URL refers to an existing file or resource.


A root alias may also contain slash `/` characters. The [[Yii::getAlias()]] method
is intelligent enough to tell which part of an alias is a root alias and thus correctly determines
the corresponding file path or URL. For example,

```php
Yii::setAlias('@foo', '/path/to/foo');
Yii::setAlias('@foo/bar', '/path2/bar');
Yii::getAlias('@foo/test/file.php');  // displays: /path/to/foo/test/file.php
Yii::getAlias('@foo/bar/file.php');   // displays: /path2/bar/file.php
```

If `@foo/bar` is not defined as a root alias, the last statement would display `/path/to/foo/bar/file.php`.


Using Aliases <a name="using-aliases"></a>
-------------

Aliases are recognized in many places in Yii without the need of calling [[Yii::getAlias()]] to convert
them into paths/URLs. For example, [[yii\caching\FileCache::cachePath]] can accept both a file path
and an alias representing a file path, thanks to the `@` prefix which allows it to differentiate a file path
from an alias.

```php
use yii\caching\FileCache;

$cache = new FileCache([
    'cachePath' => '@runtime/cache',
]);
```

Please pay attention to the API documentation to see if a property or method parameter supports aliases.


Predefined Aliases <a name="predefined-aliases"></a>
------------------

Yii predefines a set of aliases to ease the need of referencing commonly used file paths and URLs.
The following is the list of the predefined aliases:

- `@yii`: the directory where the `BaseYii.php` file is located (also called the framework directory).
- `@app`: the [[yii\base\Application::basePath|base path]] of the currently running application.
- `@runtime`: the [[yii\base\Application::runtimePath|runtime path]] of the currently running application.
- `@vendor`: the [[yii\base\Application::vendorPath|Composer vendor directory]].
- `@webroot`: the Web root directory of the currently running Web application.
- `@web`: the base URL of the currently running Web application.

The `@yii` alias is defined when you include the `Yii.php` file in your [entry script](structure-entry-scripts.md),
while the rest of the aliases are defined in the application constructor when applying the application
[configuration](concept-configurations.md).


Extension Aliases <a name="extension-aliases"></a>
-----------------

An alias is automatically defined for each [extension](structure-extensions.md) that is installed via Composer.
The alias is named after the root namespace of the extension as declared in its `composer.json` file, and it
represents the root directory of the package. For example, if you install the `yiisoft/yii2-jui` extension,
you will automatically have the alias `@yii/jui` defined during the [bootstrapping](runtime-bootstrapping.md) stage:

```php
Yii::setAlias('@yii/jui', 'VendorPath/yiisoft/yii2-jui');
```
