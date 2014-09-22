# Documentation

## Document Status

This document is a draft.

## Abstract

This describes the various forms that the framework's documentation will take.  The goals are (1) to always keep documentation in sync with the current codebase and (2) keep documentation as low-overhead and easy to write as possible.

The mechanism to be used for generating docs will be similar to how [http://github.com/qrush/gitready/](http://github.com/qrush/gitready/) works. Git Ready uses the [http://github.com/mojombo/jekyll](http://github.com/mojombo/jekyll) engine, which after due thought will be used as inspiration for a similar lithium-based generation script - Entirely pages-controller-based documentation?

## API

The main reference point for all documentation will be the API.  API documentation should always be written in full sentences with proper punctuation.  Minimum API documentation requirements will be enforced through documentation coverage and analysis tools provided by the Docs plugin, which will act as a live documentation browser when installed.  This documentation browser will act as a virtual table of contents for all code within an application, from the framework core, to the app itself, to any plugins installed (and potentially any other vendor libraries as well).

## Tutorials

Tutorials will be short, step-by-step explanations, with code, of how to accomplish a particular task using the framework.  For example, 'how to authenticate users with OpenID', or 'how to send email notifications on a database error'.  Each tutorial will be its own li3 project with its own Git repository, and will have a "readme.wiki" file in the root directory. Markdown files will be extended with syntax for embedding content from files in the repository. Tutorials will be paired with unit and integration tests that verify the functionality presented in the tutorial; this will ensure that tutorials stay in-sync with the current code.

## Guides

As opposed to tutorials, guides are more core-technology-oriented.  They give broad, functional overviews of specific parts of the framework, with links to relevant tutorials or API docs.  For example, the filters system, or content handlers in the template system. Like tutorials, each guide will be composed of a li3 project in a Git repository, with a "readme.wiki" file, and will contain unit and integration tests.

## 'Book Learning

A few things to note from my experience on the CakePHP project:

* Releasing any undocumented code is a bad idea.
* Localization is great, but it needs userland ownership to succeed. There are more than a dozen versions of the manual, but only a few are complete. Many have been abandoned.
* Manuals should not attempt to cover the API in any way
* Too much code in the manual causes more problems than it solves. The point of a manual is to teach, not act as a snippet repository.
* I don't personally think that comments add value to a manual.
* Barrier to entry needs to be overcome. DocXML or similarly complex methods don't work for contribution.
* Docs submissions are tough to moderate and edit, and credit for work becomes fuzzy very quickly.
* A writing guide is a must. People tend to want to cover every possible problem that might come up (I'm using the code with Lighty on Dreamhost and I'm seeing an issue where...), and basic grammar, branding, and code formatting guides must be enforced.
