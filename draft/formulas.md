# Package Formulas

## Introduction

A `formula` defines the meta-data of a plugin which is distributed through the li3_lab plugin (and therefore through http://lab.li3.me).

It is stored in a JSON file inside the `li3_plugin/config/` directory (where `li3_plugin` is the name of your plugin). The lab will search for this file to read various information, including its dependencies. The lab also assists you in generating it (both online and from the command line).

## Specification
This is a example formula:

	{
		"name": "li3_example",
		"version": "1.0",
		"summary": "a li3 plugin example",
		"homepage": "http://github.com/gwoo/foo",
		"bugs": "http://github.com/gwoo/foo/issues",
		"maintainers":[
			{ "name": "gwoo", "email": "gwoo@rad-dev.org", "website": "li3.rad-dev.org" }
		],
		"sources": [
			"git": [ "git://rad-dev.org/li3_example.git" ],
			"phar": [ "http://downloads.rad-dev.org/li3_example.phar.gz" ]
		],
		"requires": {
			"li3_lab": { "version": ">= 1.0" }
		},
		"suggests": {},
		"tags": [ "lithium", "li3", "example", "awesomeness" ],
		"commands": {
			"install": ["install_command"], "update": ["update_command"], "remove": ["remove_command"]
		}
	}

The following document discusses each of the fields in more detail.

### Field Descriptions
__note that this is currently a work in progress, so feel free to propose modifications.__

#### name
- Attribute: "name"
- Type: String
- Required: Yes
- Examples: "li3_foobar", "foobar", "a_cool_name"
- Description: The name of the plugin. Most of the plugins prefix their names with `li3_`, but this is not required. Please use only lower letters and underscores, but this is also not enforced.

#### version
- Attribute: "version"
- Type: String
- Required: Yes
- Examples: "0.1", "1.0", "1.1-alpha"
- Description: This field defines the current version of the plugin. __more info needs to be added on what version strings are allowed and how they are used.__

#### summary
- Attribute: "summary"
- Type: String
- Required: Yes
- Examples: "A short description of this plugin."
- Description: Make sure to add a descriptive and short explanation of your plugin here. Ideally, it is one sentence long and describes the main purpose of the plugin.

#### homepage
- Attribute: "homepage"
- Type: String
- Required: No
- Examples: "http://example.com"
- Description: The URL to your plugin website, if there is one. You can also place the GitHub url in here if you like.

#### bugs
- Attribute: "bugs"
- Type: String
- Required: No
- Examples: "http://example.com/bugs"
- Description: The URL to your plugin bugtracker, if there is one. You can also place the GitHub Issues url in here if you like.

#### maintainers
- Attribute: "maintainers"
- Type: Hash in Array
- Required: Yes
- Examples: { "name": "gwoo", "email": "gwoo@rad-dev.org", "website": "li3.rad-dev.org" }
- Description: Define one or more maintainers inside the array as a hash. Inside the hash, make at least sure that there is a name and a email field. The website is optional.
