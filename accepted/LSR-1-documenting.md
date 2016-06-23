# LSR-1 Documenting

## Abstract

For checking if the codebase complies with the standards we make use of the `Syntax` command which comes with the [QA plugin for li3](https://github.com/UnionOfRAD/li3_qa). Rules which have a corresponding check in li3 QA are marked with the `◌` symbol.

## General

All documentation must be written in English and must follow the basic rules of punctuation. **Sentences usually end with a period.** Sentences are separated by one space from each other. The first letter of a word must be capitalized if it's the beginning of a sentence, following a period or exclamation mark. Paragraphs are in general separated by one empty line. No indentation of the first paragraph line must occur.

Markdown syntax can be used in all docblocks. As a rule of thumb you should wrap in single backticks if you're talking about code. Please note that `null`, `false` and `true` must always be wrapped in single backticks.

The **maximum line length of 100 characters** which applies to code, of course applies to documentation, too. 

**Do not use tabs within dockblocks.** Use spaces instead.

## Short and Long Description

All properties and methods **must at least have a short description**.
The short description must not span more than 3 lines and may be followed by a more extensive description, which is separated by one empty line from the short description.

```
/**
 * The required short description. 
 *
 * The optional long description dictum ante quis enim 
 * luctus eget auctor leo sagittis.
[...]
```

## Examples

### Usage

Usage examples can be given as part of a long description but should be as short and to the point as possible. They should start with `Usage:`,  `For example:` or something comparable followed by the code block which is wrapped in three braces (`{` and `}`). Several example blocks can be used to group examples logically and must be separated by a blank line.

```
[...]
 * Usage:
 * ```
 * Foo::bar();
 * // returns ['a', 'b']
 * 
 * Foo::out('xyz'); // echoes xyz
 * ```
 *  
 * Extended usage:
 * ```
 * Foo::run('fast'); // returns true
 * ```
[..]
```

By convention an example consists of the code calling i.e. the method and the result of that call. Results are always commented with two slashes and can either be returned (`returns`), echoed (`echoes`) or one or the other.

### Addresses

For all example URL and mail addresses use _example.org_, for example:

 * Email: _someone@example.org_
 * WWW: _http://example.org_
 * FTP: _ftp://ftp.example.org_

## Parameters et. al.

Each tag should have a description. If that description spans multiple lines the **wrapped lines must be indented using 4 spaces**. 

```
[...]
 * @return array Nunc rutrum luctus velit, eu mattis quam aliquet quis. Morbi pulvinar 
 *         posuere neque, eget consectetur nisi tristique nec. Cras in tellus orci, 
 *         ac venenatis magna.
[...]
```

The _options_ parameter is a special case and should be described by listing all valid keys it may take. Most often the description starts with _Valid options are:_ but may also start with a more extensive description.

```
[...]
 * @param array $options This array may contain groups of settings used to override
 *        the default loader initialization for both the framework and the application.
 *        For details on allowed settings, see `Libraries::add()`. Valid options are:
 *        - `'lithium'`: Overrides base path and class load settings for core framework.
 *        - `'app'`: Overrides base path and class load settings for application.
[...]
```

If you need to specify multiple types use the pipe symbol to separate up to 2 different types (i.e. `boolean|array`), if there are more than 2 two types possible use mixed.

Don't use `@access` and `@static` tags as they are not necessary.

Don't use inline tags. Put links and references on their own lines i.e. below the return tag.

The `@author` tag must not be used in core code and usage in i.e. plugins is discouraged. An `AUTHORS.txt` file is a much better place to collect the names of the contributers as it's less "code territory carving".

### Basic

 * [@var](http://manual.phpdoc.org/HTMLSmartyConverter/HandS/phpDocumentor/tutorial_tags.var.pkg.html)
  * Syntax: `@var type Description.`
 * [@param](http://manual.phpdoc.org/HTMLSmartyConverter/HandS/phpDocumentor/tutorial_tags.param.pkg.html)
  * Syntax: `@param type $parameter Description.`
  * If the parameter accepts null as a value use void as type.
 * [@return](http://manual.phpdoc.org/HTMLSmartyConverter/HandS/phpDocumentor/tutorial_tags.return.pkg.html) 
  * Syntax: `@return type Description.`
  * If the method don't return anything specify void as type.
  * If the documented object is a method you must use this tag. 
 * @throws
  * Syntax: `@throws \qualified\Class Description.`

### Other

 * [@link](http://manual.phpdoc.org/HTMLframesConverter/phpdoc.de/phpDocumentor/tutorial_tags.link.pkg.html) 
  * For *external* links.
 * [@see](http://manual.phpdoc.org/HTMLframesConverter/phpdoc.de/phpDocumentor/tutorial_tags.see.pkg.html)
  * For *internal* links.
 * @filter
  * Flag indicating that a method is filterable.
  * Use only in docblocks for methods.
 * [@deprecated](http://manual.phpdoc.org/HTMLSmartyConverter/HandS/phpDocumentor/tutorial_tags.deprecated.pkg.html)
  * Flag indicating that a method or property is deprecated.
 * [@todo](http://manual.phpdoc.org/HTMLSmartyConverter/HandS/phpDocumentor/tutorial_tags.todo.pkg.html)
  * Used very sparsely or during early development stages.
  * Tag itself is always lowercased.
  * Is used instead of the `@fixme` tag.
 * [@method](http://manual.phpdoc.org/HTMLSmartyConverter/HandS/phpDocumentor/tutorial_tags.method.pkg.html)
  * Used in the docblock of a class to show it's magic methods
  * Usage: `@method returntype function_name(type $param1[ = ][, type $param2[ = ]...]) [description]`
 * [@property](http://manual.phpdoc.org/HTMLSmartyConverter/PHP/phpDocumentor/tutorial_tags.property.pkg.html)
  * Used in the docblock of a class to show it's magic properties
  * Usage: `@property type $varname [description]`

### Variable Types

Variable types which are used in DocBlocks. These types (with a few exceptions) are also used for typecasting.

 * `integer` Integer type variable (non-floating-point positive or negative number).
 * `float` Float type (point number).
 * `boolean` Logical type (true or false).
 * `string` String type (any value in &quot;&quot; or &#039; &#039;).
 * `array` Array type.
 * `object` Object type.
 * `resource` Resource type (for example, the result of mysql_connect()).
 * `callback` Used for anonymous functions, or objects that implement `__invoke()`, or array style callbacks.
 * `mixed` Pseudo type with undefined (or multiple) types.
 * `void` Pseudo type (i.e. null, a function returns nothing)

Remember that when you specify the type as `mixed`, you should indicate whether it is unknown, or what the possible types are.
 
If a function returns nothing or explicitly `null`, `void` is used to indicate this.

For more information on pseudo types see the [PHP documentation](http://de2.php.net/manual/en/language.pseudo-types.php).

### Order of Tags

The rule of thumb is that code related tags should be located close to the actual code and documentation related tags close to the documenting text.

Tags should be ordered according to the following list.<sup>◌</sup>

 * `@link`
 * `@see`
 * `@params`
 * `@return`

The discussion about the order of tags can be reviewed [here](http://li3.me/bot/view/li3-core/2010-01-08).

## Referring

When referring to methods always use the parentheses i.e. `run()`. Variables should be prepended with the dollar symbol i.e. `$enabled`. 

When referring to a class or a parameter of the documented object use unqualified notation and wrap in backticks. When referring to classes, methods or properties in a _see_ tag use the fully qualified notation omitting the leading namespace separator.

```
[...]
 * You better use the `Cache` class.
 *
 * The `$bubbles` parameter of this method does some crazy things.
[...]
 * @see lithium\util\Inflector::pluralize()
[...]
```

## Formatting of Comments

The comment block of a DocBlock must begin with `/**`  and close with `*/`. All lines between the first and the last begin with an asterisk. The following example shows how the asterisk must be aligned.

```
/**
 * First line.
 *
 * @return void Last line.
 */
```

**The following section is under discussion:**

Non-DocBlock comment blocks begin with `/*`and are otherwise formatted the same way as DocBlocks. The very first line (the one with the `/*`) must be left empty.

```
/*
 * First line.
 */
```

