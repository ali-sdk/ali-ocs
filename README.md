# ali-mc

[![NPM version][npm-image]][npm-url]
[![build status][travis-image]][travis-url]
[![Test coverage][codecov-image]][codecov-url]
[![David deps][david-image]][david-url]
[![Known Vulnerabilities][snyk-image]][snyk-url]
[![npm download][download-image]][download-url]

[npm-image]: https://img.shields.io/npm/v/ali-mc.svg?style=flat-square
[npm-url]: https://npmjs.org/package/ali-mc
[travis-image]: https://img.shields.io/travis/ali-sdk/ali-mc.svg?style=flat-square
[travis-url]: https://travis-ci.org/ali-sdk/ali-mc
[codecov-image]: https://codecov.io/gh/ali-sdk/ali-mc/branch/master/graph/badge.svg
[codecov-url]: https://codecov.io/gh/ali-sdk/ali-mc
[david-image]: https://img.shields.io/david/ali-sdk/ali-mc.svg?style=flat-square
[david-url]: https://david-dm.org/ali-sdk/ali-mc
[snyk-image]: https://snyk.io/test/npm/ali-mc/badge.svg?style=flat-square
[snyk-url]: https://snyk.io/test/npm/ali-mc
[download-image]: https://img.shields.io/npm/dm/ali-mc.svg?style=flat-square
[download-url]: https://npmjs.org/package/ali-mc

AliCloudDB for Memcache client.
A standard implemetation of memcached binary protocol,
and is compatible with spymemcached-2.12.0.jar.

## Install

```bash
npm install ali-mc --save
```

## Usage

- [new MCClient(options)](#new-mcclientoptions)
- [.get(key[, callback])](#getkey-callback)
- [.set(key, value[, expired, callback])](#setkey-value-expired-callback)
- [.add(key, value[, expired, callback])](#addkey-value-expired-callback)
- [.replace(key, value[, expired, callback])](#replacekey-value-expired-callback)
- [.delete(key[, callback])](#deletekey-callback)
- [.increment(key[, step, callback])](#incrementkey-step-callback)
- [.decrement(key[, step, callback])](#decrementkey-step-callback)
- [.flush([expired, callback])](#flushexpired-callback)
- [.version([callback])](#versioncallback)
- [.append(key, value[, callback])](#appendkey-value-callback)
- [.prepend(key, value[, callback])](#prependkey-value-callback)
- [.touch(key[, expired, callback])](#touchkey-expired-callback)
- [.gat(key[, expired, callback])](#gatkey-expired-callback)
- [.close()](#close)
- [Event: 'error'](#event-error)
- [Event: 'close'](#event-close)

### new MCClient(options)

Create a mc client instance.

Parameters:

- {object} options
  - {String} host
  - {Integer} port
  - {String} [username]
  - {String} [password]
  - {String} [protocol]

Example:

```js
const MemcacheClient = require('ali-mc');

const client = new MemcacheClient({
  host: ${host},
  port: ${port},
  username: ${username},
  password: ${password},
  // protocol: ${protocol}, // default is `binary`, also can use `text`
  // responseTimeout: 50,
  // logger: console,
});
```

### .ready(callback)

If the client is ready for services, callback function will be called.

Otherwise all method will return `The server is not available!`;

### .get(key[, callback])

Get the item with specified key.

Parameters:

- {String} key
- {function(err, value)} [callback]

Return:

- {mc|thunk} returns the mc instance if the callback is passed in, or return the thunk.

Example:

```
// call with callback
mc.get(${someKey}, function(err, value) {
  if (err) {
    return console.log(err);
  }
  console(value);
});

// call without callback
const co = require('co');
co(function *() {
  try {
    let value = yield mc.get(${someKey});
    console.log(value);
  } catch(err) {
    console.log(err);
  }
});
```

### .set(key, value[, expired, callback])
### .add(key, value[, expired, callback])
### .replace(key, value[, expired, callback])

Set should store the data unconditionally if the item exists or not.
Add MUST fail if the item already exist.
Replace MUST fail if the item doesn't exist.

Parameters:

- {String} key
- {object} value - anything except for function, null and undefined
- {UnsignedInteger} [expired = 0] - in seconds
- {function(err)} [callback]

Return:

- {mc|thunk} returns the mc instance if the callback is passed in, or return the thunk.


### .delete(key[, callback])

Delete the item with specified key.

Parameters:

- {String} key
- {function(err)} [callback]

Return:

- {mc|thunk} returns the mc instance if the callback is passed in, or return the thunk.


### .increment(key[, step, callback])
### .decrement(key[, step, callback])

Add or remove the specified amount to the requested counter.

Increment will cause the counter to wrap if it is up to the max value of 64 bit unsigned int.

Decrement will never result in a "negative value" (or cause the counter to "wrap")

Parameters:

- {String} key
- {PostiveInteger|UnsignedLong|object} [step = 1] - amount to add or remove. An object could be passed in to specify more details:
  - {UnsignedInteger|UnsignedLong} [step = 1]
  - {UnsignedInteger|UnsignedLong} [initial = 1] - initial value
  - {UnsignedInteger} [expired = 0] - in seconds
- {function(err, value)} [callback] - the returned value is an instance of unsigned Long. If the item doesn't exist, the server will respond with the initial value.

Return:

- {mc|thunk} returns the mc instance if the callback is passed in, or return the thunk.

Example:

```
mc.increment(${someKey}, function(err, value) { ... });

// above is equal to
mc.increment(${someKey}, 1, function(err, value) { ... });

// also is equal to
mc.increment(${someKey}, {
  step: 1,
}, function(err, value) { ... });
```

### .flush([expired, callback])

Flush the items in the cache now or some time in the future as
specified by the "expired" field.

Parameters:

- {UnsignedInteger} [expired = 0] - in seconds
- {function(err)} [callback]

Return:

- {mc|thunk} returns the mc instance if the callback is passed in, or return the thunk.


### .version([callback])

Request the server version.

Parameters:

- {function(err, value)} [callback] - the value is a version string

Return:

- {mc|thunk} returns the mc instance if the callback is passed in, or return the thunk.

### .append(key, value[, callback])
### .prepend(key, value[, callback])

Append or prepend the specified value to
   the requested key.

Parameters:

- {String} key
- {String|Buffer} value
- {function(err)} [callback]

Return:

- {mc|thunk} returns the mc instance if the callback is passed in, or return the thunk.

Example:

```
// appends "!" to the "Hello" key
mc.append('Hello', '!', function(err) { ... });
```

### .touch(key[, expired, callback])

Change the item's expiration.

Parameters:

- {String} key
- {UnsignedInteger} [expired = 0] - in seconds
- {function(err)} [callback]

Return:

- {mc|thunk} returns the mc instance if the callback is passed in, or return the thunk.

### .gat(key[, expired, callback])

All the same with `.touch()` except this it will return the value of the spcified key. `Text protocol not support`

### .close()

Close the client, you should listen the 'close' event to confirm that the client closes.

### Event: 'error'

Emitted when an error occurs.

Parameters:

- {Error} err - error object

### Event: 'close'

Emitted when the client closes.

## References

- [memcached.org](http://memcached.org/)
- [memecached binary protocol](https://cloud.github.com/downloads/memcached/memcached/protocol-binary.txt)

## License

```
The MIT License (MIT)
Copyright (c) 2016 fool2fish <fool2fish@gmail.com> and other contributors

Permission is hereby granted, free of charge, to any person obtaining
a copy of this software and associated documentation files (the "Software"),
to deal in the Software without restriction, including without limitation
the rights to use, copy, modify, merge, publish, distribute, sublicense,
and/or sell copies of the Software, and to permit persons to whom the
Software is furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included
in all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL
THE AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR
OTHER LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE,
ARISING FROM, OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR
OTHER DEALINGS IN THE SOFTWARE.
```
