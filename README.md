[![GitHub license](https://img.shields.io/badge/license-MIT-blue.svg)](https://github.com/esr360/Synergy/blob/master/LICENSE)
[![GitHub license](https://api.travis-ci.org/esr360/Synergy.svg)](https://travis-ci.org/esr360/Synergy)
[![Bower version](https://badge.fury.io/bo/Synergy.svg)](https://badge.fury.io/bo/Synergy)
[![npm version](https://badge.fury.io/js/Synergy.svg)](https://badge.fury.io/js/Synergy)
[![npm version](https://img.shields.io/npm/dm/synergy.svg)](https://badge.fury.io/js/Synergy)

[![Synergy](https://raw.githubusercontent.com/esr360/Synergy/gh-pages/logo-small.png "Synergy Logo")](https://github.com/esr360/Synergy)

#### Wiki Links

* [Overview](#overview)
* [Installation](#installation)
* [Documentation - Sass](#documentation---sass)
* [Documentation - JS](#documentation---js)
* [Creating a Theme](#creating-a-theme)
* [Development](#development)
* [Changelog](#changelog)

> A front-end framework for creating modular, configurable and scalable UI components

[View SassDoc Documentation](http://esr360.github.io/Synergy/docs/sass) | [View JSDoc Documentation](http://esr360.github.io/Synergy/docs/js)

### Module Example

```
|-- modules
|   |-- header
|   |   |-- _header.scss
|   |   |-- header.js
|   |   |-- header.json
|-- app.scss
|-- app.js
```

> For a detailed analysis of this example, checkout the [Example Uncovered](#TODO) wiki page

#### header.json

To start with we'll create all configurable aspects of the module by adding them to `header.json`:

```json
{
    "header": {
        "name": "header",
        "background": "",
        "height": "100px",
        "text-color": "",
        "link-color": "",
        "logo": {
            "image-path": "",
            "height": "50px",
            "width": "200px",
            "padding": "20px 0"
        },
        "dark": {
            "enabled": false,
            "background": "",
            "text-color": "",
            "link-color": ""
        },
        "sticky": {
            "enabled": false,
            "offset": 0
        }
    }
}
```

#### _header.scss

Next, inside `_header.scss` we will create the foundation for the `header` CSS - with the goal being to be able to change anything about the header without ever touching this file again (toucing only the above `header.json` file):

```scss
@import './modules/header/header.json';

@mixin header() {

    $config: $header;

    @include module {

        color: this('text-color');

        a {
            color: this('link-color');
        }

        @include component('logo') {
            @include get-styles(this('logo'));

            background-image: url(this('logo', 'image-path'));
            background-size: cover;
            display: inline-block;
            vertical-align: middle;
        }

        @include modifier('fixed') {
            position: fixed;
            width: 100%;
            top: 0;
        }

        @include option('dark') {
            @include get-styles(this('dark'));

            color: this('dark', 'text-color');

            a {
                color: this('dark', 'link-color');
            }
        }

    }

}
```

> For the compiled CSS result checkout the [Example Uncovered](#TODO) page

#### header.js

```js
import { Synergy } from '../../app';
import config from './header.json';

export function header() {

    Synergy('header', (header, options) => {

        const stickyOffset = options.sticky.offset || header.offsetTop;

        if (options.sticky.enabled || header.modifier('sticky')) {
            window.addEventListener('load', stickyHeaderHandler);
            window.addEventListener('scroll', stickyHeaderHandler);
        }

        function stickyHeaderHandler() {
            const operator = (window.scrollY > stickyOffset) ? 'set' : 'unset';

            header.modifier('fixed', operator);
        }

    }, config);

}
```

#### app.scss

```scss
@import '../node_modules/Synergy/dist/synergy';

@import './modules/header/header';

@include header();
```

#### app.js

```js
export { default as Synergy } from 'Synergy';

import { header } from './modules/header/header';

header();
```

#### HTML Usage

Given the above, we would now be able to use any of the following markup examples:

```html
<header class="header">
    <div class="header_logo"></div>
</header>
```

```html
<!-- This is the equivilent of setting `dark.enabled` in `header.json` to `true` -->
<header class="header-dark">
    <div class="header_logo"></div>
</header>
```

```html
<!-- This is the equivilent of setting `sticky.enabled` in `header.json` to `true` -->
<header class="header-sticky">
    <div class="header_logo"></div>
</header>
```

```html
<!-- This is the equivilent of setting both `dark.enabled` and `sticky.enabled` in `header.json` to `true` -->
<header class="header-dark-sticky">
    <div class="header_logo"></div>
</header>
```

## Changelog

### Version 3.7.0

Released: 30th July 2017

###### Release Notes

* Splitting JS into smaller functions

### Version 3.6.0

Released: 11th June 2017

###### Release Notes

* Synergy JS module rewritten in ES6
* Removing Sass-JSON as dependency, replacing with Sass-JSON-Vars
* Updating Sass-Boost dependency
* Dependences now node modules instead of git submodules
* Improving options mixin
* Adding `value-enabled()` utility function
* Adding `enabled()` utility function
* Allow modules to have default modifiers
* Allow extending of modifiers when including module
* Allow combining of modifiers when including module
* Allow module output to be disabled
* Removing `overwrite()` mixin
* Removing `overwrite-component()` mixin
* `module()` mixin is now nestable
* `component()` mixin is now nestable
* Adding JavaScript unit tests