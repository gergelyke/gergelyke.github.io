---
title: What's new in Node.js 9?
description: Node.js 8 became the LTS version on 31 October 2017, and Node.js 9 became the current Node.js version
githubIssueId: TODO
date: 2017-11-01
tags:
    - 'node.js'
    - 'release'
---

Node.js 8.9.0 just became the LTS (Long Term Support) version of Node.js under the codename  Carbon, and it will be maintained till December 31, 2019 to align wit the scheduled end-of-life of OpenSSL-1.0.2.

To read more on Node.js releases, read the official [Node.js Releases](https://github.com/nodejs/Release) repository.

# What's new in Node.js 9?

## HTTP/2 without the `--expose-http2` flag

HTTP/2 landed in Node.js 8.4 behind a flag. With Node.js 9, it became always available. If you are
new to  HTTP/2, I recommend checking out the following resources:

* [HTTP/2 by EITF](https://http2.github.io/)
* [Introduction to HTTP/2](https://developers.google.com/web/fundamentals/performance/http2/)
* [Rules of Thumb for HTTP/2 Push](https://docs.google.com/document/d/1K0NykTXBbbbTlv60t5MyJvXjqKGsCVNYHyLEXIxYMv0/edit)

In Node.js, you can start using HTTP/2 using the `http2` core module:

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

> To run the example above, you have to generate a private key and a certificate for your server. To do so, run this command: `openssl req -newkey rsa:2048 -new -nodes -x509 -days 3650 -keyout key.pem -out cert.pem`. When it asks for the common name, make sure to enter `localhost`.

To read more on HTTP/2, check out the official [Node.js HTTP/2 documentation](https://nodejs.org/api/http2.html).

## Deep strict equal helper exposed in `util`

The [assert]() core module had the `assert.deepStrictEqual(actual, expected[, message])` method since version 1.2.0. However, as the purpose of that function is to be used in tests, it throws if the values don't match.

Since a lot of userland modules needed this functionality, the Node.js core decided to expose this function, which was already used internally by the Node.js core.

To compare any values, from now on you can use the `util.isDeepStrictEqual(value1, value2)` method.

```javascript
const { isDeepStrictEqual } = require('util')

const isEq = isDeepStrictEqual({
  a: '1'
}, {
  a: 1
})
```

## Callbackify added to `util`

https://github.com/nodejs/node/pull/12712
`