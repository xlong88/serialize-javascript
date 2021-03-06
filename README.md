Serialize JavaScript
====================

Serialize JavaScript to a _superset_ of JSON that includes regular expressions and functions.

[![npm Version][npm-badge]][npm]
[![Dependency Status][david-badge]][david]
[![Build Status][travis-badge]][travis]

## Overview

The code in this package began its life as an internal module to [express-state][]. To expand its usefulness, it now lives as `serialize-javascript` — an independent package on npm.

You're probably wondering: **What about `JSON.stringify()`!?** We've found that sometimes we need to serialize JavaScript **functions** and **regexps**. A great example is a web app that uses client-side URL routing where the route definitions are regexps that need to be shared from the server to the client.

The string returned from this package's single export function is literal JavaScript which can be saved to a `.js` file, or be embedded into an HTML document by making the content of a `<script>` element. **HTML charaters and JavaScript line terminators are escaped automatically.**

## Installation

Install using npm:

```shell
$ npm install serialize-javascript
```

## Usage

```js
var serialize = require('serialize-javascript');

serialize({
    str  : 'string',
    num  : 0,
    obj  : {foo: 'foo'},
    arr  : [1, 2, 3],
    bool : true,
    nil  : null,
    undef: undefined,

    fn: function echo(arg) { return arg; },
    re: /([^\s]+)/g
});
```

The above will produce the following string output:

```js
'{"str":"string","num":0,"obj":{"foo":"foo"},"arr":[1,2,3],"bool":true,"nil":null,"fn":function echo(arg) { return arg; },"re":/([^\\s]+)/g}'
```

Note: to produced a beautified string, you can pass an optional second argument to `serialize()` to define the number of spaces to be used for the indentation.

### Automatic Escaping of HTML Characters

A primary feature of this package is to serialize code to a string of literal JavaScript which can be embedded in an HTML document by adding it as the contents of the `<script>` element. In order to make this safe, HTML characters and JavaScript line terminators are escaped automatically.

```js
serialize({
    haxorXSS: '</script>'
});
```

The above will produce the following string, HTML-escaped output which is safe to put into an HTML document as it will not cause the inline script element to terminate:

```js
'{"haxorXSS":"\\u003C\\u002Fscript\\u003E"}'
```

### Options

The `serialize()` function accepts `options` as its second argument. There are two options, both default to being `undefined`:

#### `options.space`

This option is the same as the `space` argument that can be passed to [`JSON.stringify`][JSON.stringify]. It can be used to add whitespace and indentation to the serialized output to make it more readable.

```js
serialize(obj, {space: 2});
```

#### `options.isJSON`

This option is a signal to `serialize()` that the object being serialized does not contain any function or regexps values. This enables a hot-path that allows serialization to be over 3x faster. If you're serializing a lot of data, and know its pure JSON, then you can enable this option for a speed-up.

**Note:** That when using this option, the output will still be escaped to protect against XSS.

```js
serialize(obj, {isJSON: true});
```

## License

This software is free to use under the Yahoo! Inc. BSD license.
See the [LICENSE file][LICENSE] for license text and copyright information.


[npm]: https://www.npmjs.org/package/serialize-javascript
[npm-badge]: https://img.shields.io/npm/v/serialize-javascript.svg?style=flat-square
[david]: https://david-dm.org/yahoo/serialize-javascript
[david-badge]: https://img.shields.io/david/yahoo/serialize-javascript.svg?style=flat-square
[travis]: https://travis-ci.org/yahoo/serialize-javascript
[travis-badge]: https://img.shields.io/travis/yahoo/serialize-javascript.svg?style=flat-square
[express-state]: https://github.com/yahoo/express-state
[JSON.stringify]: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/JSON/stringify
[LICENSE]: https://github.com/yahoo/serialize-javascript/blob/master/LICENSE
