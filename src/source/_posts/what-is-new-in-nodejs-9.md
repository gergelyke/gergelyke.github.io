---
title: What's new in Node.js 9?
description: Node.js 8 became the LTS version on 31 October 2017, and Node.js 9 became the current Node.js version
githubIssueId: 7
date: 2017-11-02
tags:
    - 'node.js'
    - 'release'
---

Node.js 8.9.0 just became the LTS (Long Term Support) version of Node.js under the codename  Carbon, and it will be maintained till December 31, 2019, to align with the scheduled end-of-life of OpenSSL-1.0.2.

After that, Node.js 9 will serve as the base for the next LTS release, Node.js 10, which will become the active LTS version in October 2018. To read more on Node.js releases, read the official [Node.js Releases](https://github.com/nodejs/Release) repository.

This article is dedicated to the new features and fixes that ship with Node.js 9, and I found most interesting and useful. Did I miss something important? Please let me know in the comments section! 😊

# What's new in Node.js 9?

## HTTP/2 without the `--expose-http2` flag

HTTP/2 landed in Node.js 8.4 behind a flag. With Node.js 9, it became always available. If you are
new to  HTTP/2, I recommend checking out the following resources:

* [HTTP/2 by EITF](https://http2.github.io/)
* [Introduction to HTTP/2](https://developers.google.com/web/fundamentals/performance/http2/)
* [Rules of Thumb for HTTP/2 Push](https://docs.google.com/document/d/1K0NykTXBbbbTlv60t5MyJvXjqKGsCVNYHyLEXIxYMv0/edit)

You can start using HTTP/2 with the http2 core module:

```javascript
const http2 = require('http2')
const fs = require('fs')

const server = http2.createSecureServer({
  key: fs.readFileSync('key.pem'),
  cert: fs.readFileSync('cert.pem')
})

server.on('error', (err) => console.error(err))
server.on('socketError', (err) => console.error(err))

server.on('stream', (stream, headers) => {
  // stream is a Duplex
  stream.respond({
    'content-type': 'text/html',
    ':status': 200
  })
  stream.end('<h1>Hello World</h1>')
})

server.listen(8443)
```

> To run the example above, you have to generate a private key and a certificate for your server. To do so, run this command: `openssl req -newkey rsa:2048 -new -nodes -x509 -days 3650 -keyout key.pem -out cert.pem`. When it asks for the common name, make sure to enter `localhost`. You need this, as HTTP/2 only works using TLS to improve security.

To read more on HTTP/2, check out the official [Node.js HTTP/2 documentation](https://nodejs.org/api/http2.html).

## Deep strict equal helper exposed in `util`

The [assert](https://nodejs.org/api/assert.html) core module had the `assert.deepStrictEqual(actual, expected[, message])` method since version 1.2.0. However, as the purpose of that function is to be used in tests, it throws if the values don't match.

Since a lot of userland modules needed this functionality, the Node.js core decided to expose this function, that it was already using internally.

From now on, you can use the `util.isDeepStrictEqual(value1, value2)` method to compare any values.

```javascript
const { isDeepStrictEqual } = require('util')

const isEq = isDeepStrictEqual({
  a: '1'
}, {
  a: 1
})
```

> <center>*Interested in working on Node.js with me at GoDaddy? Drop me a DM on [Twitter](https://twitter.com/nthgergo)!* 🤗</center>

## Callbackify added to `util`

`util.promisify` was added with Node.js 8. However, because of compatibility reasons, there was a need for the opposite - a method that can convert Promises to an error-first, callback taking function.

```javascript
const { callbackify } = require('util')

async function main () {
  await Promise.resolve()
}

callbackify(main)(function (err) {
  if (err) {
    return console.error(err)
  }

  console.log('finsihed without an error')
})
```


## More static error codes

During the summer, static error codes started to appear in the Node.js core to solve the problem of string comparisons when errors were thrown. You might have seen a lot of error handling logic like this previously:

```javascript
if (err.message === 'Can\'t set headers after they are sent.') {
  //do something with the error
}
```

The problem with this approach is that even a typo fix would cause a SemVer major change. And it gets even worse when you want to fix a misleading error message. To solve the issue, the Node.js core started to assign static error messages to errors. You can check them out on the [errors docs page](https://nodejs.org/api/errors.html#errors_node_js_error_codes).

The previous example can be rewritten the following way:

```javascript
if (err.code === 'ERR_HTTP_HEADERS_SENT') {
  //do something with the error
}
```

## Two-Factor Authentication for npm

Node.js 9.0.0 ships with npm version 5.5.1 - which includes two-factor authentication. If you are publishing, this is a must have - even if you don't update to Node.js 9, you should update your npm version and enable two-factor authentication.

## Updating to Node.js 9?

Updating to odd-numbered major releases are generally not recommended for production environments, only if you'd like to test things out, as breaking changes can be added with each release. There will be no long-term support for these releases, and development on them will be stopped immediately once an even-numbered LTS version is cut.
