# Koa Static Cache [![Build Status](https://travis-ci.org/koajs/static-cache.png)](https://travis-ci.org/koajs/static-cache)

Static server for koa.

Differences between this library and other libraries such as [static](https://github.com/koajs/static):

- There is no directory or `index.html` support.
- You may optionally store the data in memory - it streams by default.
- Caches the assets on initialization - you need to restart the process to update the assets.
- Uses MD5 hash sum as an ETag.

## Installation

```js
$ npm install koa-static-cache
```

## API

### staticCache(dir [, options] [, files])

```js
var path = require('path')
var staticCache = require('koa-static-cache')

app.use(staticCache(path.join(__dirname, 'public'), {
  maxAge: 365 * 24 * 60 * 60
}))
```

- `dir` (str) - the directory you wish to serve.
- `options.maxAge` (int) - cache control max age for the files, `0` by default.
- `options.cacheControl` (str) - optional cache control header. Overrides `options.maxAge`.
- `options.buffer` (bool) - store the files in memory instead of streaming from the filesystem on each request.
- `options.alias` (obj) - object map of aliases. See below.
- `options.files` (obj) - optional files object. See below.

### Aliases

For example, if you have this alias object:

```js
{
  '/favicon.png': '/favicon-32.png'
}
```

Then requests to `/favicon.png` will actually return `/favicon-32.png` without redirects or anything.
This is particularly important when serving [favicons](https://github.com/audreyr/favicon-cheat-sheet) as you don't want to store duplicate images.

### Files

You can pass in an optional files object.
This allows you to do two things:

#### Combining directories into a single middleware

Instead of doing:

```js
app.use(staticCache('/public/js'))
app.use(staticCache('/public/css'))
```

You can do this:

```js
var files = {}

// Mount the middleware
app.use(staticCache('/public/js', {}, files))

// Add additional files
staticCache('/public/css', {}, files)
```

The benefit is that you'll have one less function added to the stack as well as doing one hash lookup instead of two.

#### Editing the files object

For example, if you want to change the max age of `/package.json`, you can do the following:

```js
var files = {}

app.use(staticCache('/public', {
  maxAge: 60 * 60 * 24 * 365
}, files))

files['/package.json'].maxAge = 60 * 60 * 24 * 30
```

## License

The MIT License (MIT)

Copyright (c) 2013 Jonathan Ong me@jongleberry.com

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in
all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN
THE SOFTWARE.
