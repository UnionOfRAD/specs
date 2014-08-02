## Abstract

Globalization (a term for the combination of internationalization and localization and abbreviated to _g11n_) makes it possible to adapt software to different languages, scripts and regional differences. This document describes the approach Lithium takes towards globalizing software.

## Introduction

G11n takes advantage of the updated structures and paradigms in Lithium. It's built-in right from the start and the whole framework is developed with it's requirement in mind. G11n consists of related data, translation of static messages and dynamic content, formatting of numbers, dates and currencies, specific validation rules, setting and parsing locale identifiers. The functionality required to allow for full g11n are kept in dedicated namespaces and classes.

### Terms

#### Globalization

This specification proposes the term globalization (g11n) as the combination of internationalization and localization. This eases the communication and doesn't require someone to learn two different definitions without any pragmatic use. Please also see the [article on g11n at Wikipedia](https://secure.wikimedia.org/wikipedia/en/wiki/G11n).

#### Locale

The locale (here: _locale identifier_) is used to distinguish among different sets of common preferences. The identifier used by Lithium is based in it's structure upon [Unicode's language identifier]((http://www.unicode.org/reports/tr35/tr35-12.html#Identifiers) and is compliant to [BCP 47](http://www.rfc-editor.org/rfc/bcp/bcp47.txt). A list of all valid locales can be found at the [BCP47 Registry](http://www.iana.org/assignments/language-subtag-registry).

`language[_Script][_TERRITORY][_VARIANT]`

 - `language`
The spoken language, here represented by an ISO 639-1 code, where not available ISO 639-3 and ISO 639-5 codes are allowed too) tag. The tag should be lowercased and is required.

 - `Script`
The tag should have it's first character capitalized, all others lowercased. The tag is optional.

 - `TERRITORY`
A geographical area, here represented by an ISO 3166-1 code. Should be all uppercased and is optional.

 - `VARIANT`
Should be all uppercased and is optional.

Tags are joined together by underscores.

#### Time Zone

Time zones are represented by time zone codes. See http://php.net/timezones for list of all valid time zones codes. Where multiple codes for one time zone exist (i.e. `UTC` and `Etc/UTC`) the most specific one is used.

#### Currency

n/a

## Identifying, Setting and Passing Locale Settings

The settings for the current locale, time zone and currency are kept as environment settings. This allows for _centrally_ switching, _transparently_ setting and retrieving g11n related settings. This way it is always clear what the effective locale is. Globalized output is _consistent_.

The environment settings are:

 - `g11n.locale` The effective locale. Defaults to `en`.
 - `g11n.acceptedLocales` Locales accepted by the client. Defaults to `array('en')`.
 - `g11n.availableLocales` Application locales available. Defaults to `array('en')`.
 - `g11n.timezone` The effective time zone. Defaults to `Etc/UTC`.
 - `g11n.currency` The effective currency. Defaults to `USD`.

The _effective locale_ (which ends up in `g11n.locale`) depends on the availability of globalized application content and the locale preferred by the client. This is how it is negotiated: If the preferred locale is available, the effective locale has been determined. If it's not, the next locale accepted by the client is being tried to match against the available ones. If none of the accepted locales matches, the default locale is being used.

But how do you identify the preferred locale(s)? You can either retrieve the locale from the name of the resource (i.e. the url) or from information provided by the client. In order to keep the identification process _as transparent as possible_ it is recommended to first try identification by resource name and than fall back to identification by client supported information.

**Note**: _Brad Fults has [a post](http://h3h.net/2007/01/designing-urls-for-multilingual-web-sites/)  on all the different approaches and their pros and cons. See also [this related RFC ticket](http://code.cakephp.org/tickets/view/100)._

Resource identifiers for content globalized for different locales should reflect that difference. It is therefore recommended to indicate this by adding the locale to the respective resource identifiers (i.e. urls). These are 5 different approaches for embedding the locale into the resource:

 - Modified directory structure aka Prefixed route: `http://example.org/fr/posts/add`
 - Locale in querystring `http://example.org/posts/add?l=fr`
 - Locale as named parameter `http://example.org/posts/add/locale:fr`
 - Country-specific TLD: `http://example.fr/posts/add`
 - Locale-specific sub-domains: `http://fr.example.org/posts/add`

Sometimes it's necessary to retrieve the preferred locale from the client directly. This could be used in the case other techniques to determine the effective locale fail.

If we've got a HTTP request information can be easily parsed from the `Accept-Language` header. It's also possible to use a database resolving IP addresses to locales or using - if existent - the locale provided in a profile. For console requests the preferred locale can be retrieved by means of environments settings.

#### Use Cases

1. A site is made available for different locales. Search engines should be able to index content for each locale easily.
2. Gerald gives Emily an url, Emily expects the same content Gerald gets when pointing her browser to that url.
3. A client without a profile (therefore without any explicit preference) uses an url which does not contain any locale information but expects the content to be globalized.
4. A client issues a console request and wants to get globalized console output.

## Data

G11n data is not just translated messages, it's validation rules, formats and a lot more, too. Data is grouped into 4 different kinds of categories: _inflection_, validation_, _message_ and _list_.

Generally speaking is the g11n catalog class allowing us to retrieve and store globalized data, providing low-level functionality to other classes. It's interface is similar to classes like Session or Cache and like those extensible through adapters.

We need to deal with different kinds of sources for this data, but we don't want differing results depending on the adapter in use. This is why results are kept in a neutral interchangeable format. You can rely on getting the same format of obtained results independent from the adapter they're coming from.

The class is able to **aggregate** data from different sources which allows to complement sparse data. Not all categories must be supported by an individual adapter.

```
// Configures the runtime source.
Catalog::config(array('runtime' => array('adapter' => 'Memory')));

$data = '/[0-9]{5}/'
Catalog::write('validation.postalCode', 'de_DE', $data, array('name' => 'runtime'));

// Reads from all configured sources
Catalog::read('validation.postalCode', 'en_US');

// Reads from just the runtime source.
Catalog::read('validation.postalCode', 'en_US', array('name' => 'runtime'));
```

Several adapters are available:

The **gettext adapter** reads and writes PO and MO files. The current implementation of gettext is not thread safe. This PHP only implementation is thread safe and allows reading binary MO files independent of the machine's endian it was created on. Both 32bit and 64bit systems are supported. Also see the [this post to the mailinglist](http://groups.google.com/group/cake-php/msg/926e556c5c751a5c) for more information.

**Note**: Full context support is coming in POEdit 1.5 according to [this ticket](http://www.poedit.net/trac/ticket/148).

The **cldr adapter** reads from the [Common Locale Data Repository](http://cldr.unicode.org/) maintained by the Unicode Consortium. Writing and deleting is not supported. The data is not shipped with the framework. The data is licensed under the [CLDR License](http://unicode.org/copyright.html#Exhibit1).

The **memory adapter** allows for writing and reading runtime data and is also good for testing.

The **code adapter** reads (extracts) message ids for creating message catalog templates from source code. PHP is supported through a parser using the built-in tokenizer but future support for other formats (i.e. JavaScript) is possible too.

Nearly all adapters require a path to a directory containing globalized data. Data is - by convention - being stored in the following locations:

- `libraries/lithium/g11n/resources[/{cldr,po}]`
- `app/resources/g11n[/{cldr,po}]`
- `app/plugins/PLUGIN/resources/g11n[/{cldr,po}]`

Data can either be stored directly below `resources/g11n` or in a subdirectory where adapters expect a `cldr` subdirectory to contain data for the CLDR and `po` to contain files for usage with gettext.

#### Use Cases

1. Norman is migrating code. He wants to keep the format i.e. translated messages are stored in because the translation team is used to this format.
2. In order to cut down time needed for supporting a set of locales, data available through the CLDR should be used.
3. The catalog class needs to be tested.
4. A plugin provides g11n data.
5. One application has with multiple, different sources of data. The PO format is used for translation whereas other data is available through the CLDR.
6. Other classes in a framework need for i.e. rules for a specific locale.
7. John wrote a small application and thinks keeping translation in an extra format would be too much.

## Translation of Static Messages

Marking messages as translatable is done by using `$t()` and `$tn()` which are short-hands to `Message::translate()` with the addition of setting a default fallback message.

```
$t('Look!');
$tn('book', 'books', array('count' => 3));
```

The functions can be retrieved through `Message::shortHands()`. To make them available in all templates you can use a filter to add those as content filters.

```
extract(Message::shortHands());
$bar = $t('Look!');
```

### Best Practices

Since the marked messages will later be translated by others it is important to keep a few best practices in mind.

1. Use entire English sentences (as it gives context).
2. Split paragraphs into multiple messages.
3. Instead of string concatenation utilize `String::insert()`-style format strings.
4. Avoid to embed markup into the messages.
5. Do not escape i.e. quotation marks where possible.

### Placeholders

When marking messages you can use standard `String::insert()`-style replacement.

```
$t('Look! A {:color} bird!', array('color' => $t('red')));
```

### Plurals

n/a

### Scope

n/a

### Ambiguous Meaning

Especially very short messages have an ambiguous meaning. To aid translators determining the correct translation for a message there should be enough context given for the message. The easiest way is - if possible - to embed the context into the message itself or you can use the _context_ option.

```
$t('manual'); // bad
$t('Read the manual.'); // good
$t('manual', array('context' => 'the installation manual')); // good alternative
```

**Note**: The context option is not yet implemented.

### No Operation

Passing the `'noop'` option to `Message::translate()` will result in the default being returned. Since the short-hand translation functions use `translate()` internally you can use the option to  just mark a string for translation without it being translated during runtime.

```
$t('foo', array('noop' => true));
$tn('foo', 'bar', array('noop' => true)); // we don't need to pass `'count'` in this case
```

```
// File: a.php
$t('foo', array('noop' => true)); // the extractor picks up `foo`

// File: b.php
$section = 'foo';
$t($section);
```

### Knock Out

If you're using templates which use the short-hand translation functions, but don't want to do any globalization you can disable the lookup of translations by using a filter. Keep in mind that you still need to include the functions as you otherwise would get missing function errors.

```
Message::applyFilter('_translated', function($self, $params, $chain) {
	return null;
});
```

### Templates, Extraction

Marked messages are extracted by using the g11n command. This allows for extracting messages and comments from source files, creating and updating files containing message templates, creating and updating files containing translated messages and compilation of those.

#### Use Cases

1. John is working alone and is ready to deploy a small application. The last thing to do is checking if all translation are correct. He finds one incorrect message id after the other and needs to reextract and update the message template very often.

2. A message has multiple translations depending on it's context.

## Translation of Dynamic Content

n/a

#### Use Cases

n/a

## Formatting of Numbers, Dates and Currencies

#### Implementation Details

As with PHP 5.3 the [intl extension] ships with PHP (but need to be enabled due to dependencies on ICU) formatting is going to be implemented utilizing parts of functionality the extension exposes.

It is assumed that if a user is in the need of such a specific functionality she/he is willing to install the ext. Installing the extension is not considered to be a barrier for this group of users.

Core formatting is going to be supported through two new classes within the `lihtium\g11n` namespace: `Number`
 and `Date` class.

The main purpose of the formatters is that on the one hand being able to transform numbers and dates into a locale specific format and on the other hand normalizing such numbers and dates (i.e. for storing them in a database).

While the formatting would be logically be put into a helper (within the view layer), the normalization part would take place within the model layer. This needs some sort of discussion.

Recent experiments with the intl extension have proven differing results across systems. Keeping this in mind the following roadmap is suggested:

1. `Number` class with unit tests.
2. `Number` integration tests.
3. Discussion: Are the inconsistencies neglectable?
4. `Date` class with unit tests.
5. `Date` integration tests.
6. Refine `Number` and `Date` APIs
7. Discussion: How do formatting classes integrate with the framework?
8. Discussion: On which layer should formatting/normalization occur?

Iterate/Rewrite/throw away code as necessary.

For the `Number` class a skeleton alongside a handful of tests already exists. These should be seen as starting point on how the API could look like. The API of the class takes cues from the existing [Locale class](http://lithify.me/docs/lithium/g11n/Locale) which itself also provides symmetric methods (i.e. compose/decompose).

[Skeleton Number](http://pastium.org/view/51256ecfad542a34b663c8ce17bb6377)
[Skeleton NumberTest](http://pastium.org/view/1b5a202e09a041d79d5eaa8843de1bd1)

[Intl's NumberFormatter](http://php.net/manual/en/class.numberformatter.php)
[Intl's DateFormatter](http://php.net/manual/en/class.intldateformatter.php)


#### Use Cases

**Emily** has an images table with a title column and width and height columns where the length of each image should be stored in centimeter with a precision of 2. She now wants to display a nice table in the index view the decimal values formatted according to the users locale.

```
foreach ($this->data['Image'] as $item) {
        echo $number->format($item['width'], array('places' => 2)) . ' cm';
}
```

Emily wants the users of the site to be able to add new images. She creates a form in the add view.

```
echo $form->input('title');
echo $form->input('width', array('after' => 'cm');
```

Now Horst from Germany starts to fill out the form.  He enters the title `Grosses Bild` and the width for the image `1,20`. Now Tom from the United States starts to fill out the form. He enters the title `Large Image` and the width `1.20`. Both expect the form to submit successfully and that the value `1.2` is stored in the db.

**Jonathan** just finished the blog tutorial and wants the created time for his blog post to be displayed according to the currently selected language.

```
echo $time->nice($this->data['Post']['created']);
```

He may also choose to disable automatic formatting because his blog is in english only.

```
echo $time->nice($this->data['Post']['created'], array('language' => 'en'));
```


