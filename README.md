
# tape-run

A [tape](https://github.com/substack/tape) test runner that runs your tests in
a (headless) browser and returns 0/1 as exit code, so you can use it as your
`npm test` script.

[![build status](https://secure.travis-ci.org/juliangruber/tape-run.svg)](http://travis-ci.org/juliangruber/tape-run)
[![downloads](https://img.shields.io/npm/dm/tape-run.svg)](https://www.npmjs.org/package/tape-run)

## Usage

First write a test utilizing [tape](https://github.com/substack/tape) and save
it to `test/test.js`:

```js
var test = require('tape');

test('a test', function (t) {
  t.ok(true);
  t.end();
});
```

Then run this command using tape-run and
[browserify](https://github.com/substack/browserify) and watch the magic happen
as the TAP results stream in from a browser (default: electron):

```bash
$ browserify test/*.js | tape-run
TAP version 13
# one
ok 1 true

1..1
# tests 1
# pass  1

# ok

$ echo $?
0
```

## API

You can use tape-run from JavaScript too:

```js
var run = require('tape-run');
var browserify = require('browserify');

browserify(__dirname + '/test/test.js')
  .bundle()
  .pipe(run())
  .on('results', console.log)
  .pipe(process.stdout);
```

And run it:

```bash
$ node example/api.js
TAP version 13
# one
ok 1 true

1..1
# tests 1
# pass  1

# ok
{ ok: true,
  asserts: [ { ok: true, number: 1, name: 'true' } ],
  pass: [ { ok: true, number: 1, name: 'true' } ],
  fail: [],
  errors: [],
  plan: { start: 1, end: 1 } }
```

### run([opts])

`opts` can be:

* `wait (Number) [Default: 1000]`: Make `tap-finished` wait longer for results.
Increase this value if tests finish without all tests being run.
* `port (Number)`: If you specify a port it will wait for you to open a browser
on `http://localhost:<port>` and tests will be run there.
* `browser (String)`: Browser to use. Defaults to `electron`. Usage `--browser=chrome`. Available if installed:
  * `chrome`
  * `firefox`
  * `ie`
  * `phantom`
  * `safari`
* `browser (Object)`: Object with browser.name and browser.options fields:
  * `browser.name (String)`: same as `browser (String)`. Usage `--browser.name=chrome`.
  * `browser.options (Object)`: Browser options, for supported browsers (currently [electron only](https://github.com/atom/electron/blob/master/docs/api/browser-window.md#new-browserwindowoptions)). Usage `--browser.name=electron --browser.options.width=800 --browser.options.height=500 --browser.options.webPreferences.webSecurity=false`

 

The **CLI** takes the same arguments, plus `--render` (see blow):

```bash
$ tape-run --help
Pipe a browserify stream into this.
browserify [opts] [files] | tape-run [opts]

Options:
  --wait     Timeout for tap-finished                                                                                
  --port     Wait to be opened by a browser on that port                                                             
  --browser       [Object|String]. Object with browser.name and browser.options fields. Or string to pass browser.name only.
    Always available: electron.
    Options available: electron only.
    Available if installed: chrome, firefox, ie, phantom, safari
    Usage:
      --browser=chrome - set browser name with no options passed.
      --browser.name=electron --browser.options.width=800 --browser.options.height=500 --browser.options.webPreferences.webSecurity=false - set browser and specify browser options.
      
  --render   Command to pipe tap output to for custom rendering
  --help     Print usage instructions                                                                                
```

## Custom Rendering

In order to apply custom transformations to tap output without sacrificing the proper exit code, pass `--render` with a command like [tap-spec](https://npmjs.org/package/tap-spec):

```bash
$ browserify test.js | tape-run --render="tap-spec"

  one

    ✔ true

```

## Headless testing

To use the default electron browser on travis, add this to your travis.yml:

```yml
addons:
  apt:
    packages:
      - xvfb
install:
  - export DISPLAY=':99.0'
  - Xvfb :99 -screen 0 1024x768x24 > /dev/null 2>&1 &
  - npm install
```

[Source](https://github.com/rhysd/Shiba/blob/055a11a0a2b4f727577fe61371a88d8db9277de5/.travis.yml).

For gnu/linux installations without a graphical environment:

```bash
$ sudo apt-get install xvfb # or equivalent
$ export DISPLAY=':99.0'
$ Xvfb :99 -screen 0 1024x768x24 > /dev/null 2>&1 &
$ browser-run ...
```

There is also an example docker machine [here](https://github.com/fraserxu/docker-tape-run).

## Installation

With [npm](http://npmjs.org) do

```bash
$ npm install tape-run -g # for cli
$ npm install tape-run    # for api
```

## License

(MIT)

Copyright (c) 2013 Julian Gruber &lt;julian@juliangruber.com&gt;

Permission is hereby granted, free of charge, to any person obtaining a copy of
this software and associated documentation files (the "Software"), to deal in
the Software without restriction, including without limitation the rights to
use, copy, modify, merge, publish, distribute, sublicense, and/or sell copies
of the Software, and to permit persons to whom the Software is furnished to do
so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.
