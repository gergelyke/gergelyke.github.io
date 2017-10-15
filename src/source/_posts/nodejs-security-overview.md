---
title: Node.js Security Overview
description: A look at where we are with Node.js security, and what you can do to keep your customer's data safe
githubIssueId: 3
date: 2017-10-12
tags:
    - 'report'
    - 'node.js'
---

The initial Node.js release happened a little bit more than 8 years ago. Since then Node.js became of the leading platforms for building backend applications. As such, security becomes more and more important. 

In the first part of this article you will learn where Node.js and npm are where it comes to security, and in the second part, you will learn some best security practices which you can immediately apply to your Node.js applications.

## Why security matters?

As a Node.js developer, you most probably work with a huge amount of user data - some of them being very sensitive in nature. Businesses built in the cloud-native era should always treat their data in the top-most respect.

**As the saying goes - your attackers need to get things right only once, while you as an application developer has to be right all the time.**

One of the best examples of this is the website called [have i been pwned?](https://haveibeenpwned.com/) operated by [Troy Hunt](https://twitter.com/troyhunt), which collects security breaches, and makes publicly available breaches searchable, so you can know if one of your accounts may got exploited 💥. Breached websites include LinkedIn, Adobe or MySpace, just to name the bigger ones.

**But what about Node.js?**

## Security in the Node.js core

As of today, Node.js and its core contributors maintain many different channels to address the security of the Node.js project and the security of its' users.

In 2016, at Node.js Interactive in Austin, the Security Working Group was formed, addressing the need for a working group focusing on security. It is mainly responsible for defining and maintaining security policies and procedures for the core Node.js project and other projects maintained by the Node.js Foundation.

Also, it helps with:

* the Node.js Security project to bring vulnerability data into the Foundation,
* review and recommend processes for handling of security reports,
* recommend security improvements for the core Node.js project,
* facilitate and promote the expansion of a healthy security service and product provider ecosystem vulnerabilities.

For more information, check out the [Security working group](https://github.com/nodejs/security-wg).

### Responsible disclosure of potential security issues

If you feel like you found a security bug in Node.js, you should report it to security@node.js.org. Once you do that, your email will be acknowledged within 24 hours and if needed, the security team may ask you for more details in the next 48 hours.

*To better understand the process, and read the full disclosure policy, check out the official [Node.js security](https://nodejs.org/en/security/) page.*

## Security in the npm ecosystem

npm is by far the biggest package ecosystem in the world, with more than half a million packages, growing with 500 new modules daily. We download these packages more than 3 billion times every week. At this scale, the security is the ecosystem is of the top-most priorities.

As the responsible maintainer and developer of the registry, npm, make important steps towards making npm more secure every step down the road, like:

* [moving the whole traffic of the registry to https](http://blog.npmjs.org/post/142077474335/npm-registry-is-now-fully-https),
* [working with ^Lift Security to do security audits](http://blog.npmjs.org/post/163676667652/securing-the-npm-registry),
* [fighting malwares](http://blog.npmjs.org/post/163723642530/crossenv-malware-on-the-npm-registry),
* and most recently, adding [two-factor authentication and read-only tokens](http://blog.npmjs.org/post/166039777883/protect-your-npm-account-with-two-factor).

---

<center>**⚠️ Before continuing reading the article, take a minute to [enable Two-Factor Authentication for your npm account now](https://docs.npmjs.com/getting-started/using-two-factor-authentication). ⚠️**</center>

---

Even if npm does everything in its' power to secure the registry, it can still happen that modules have security vulnerabilities. As most applications can easily end up depending on hundreds if not thousands of modules, auditing each one of them before you start using them is not something you have the time to do.

Luckily, the Node.js Security Project, as well as Snyk maintains security advisories, with both companies offering paid subscription plans to run their tools in CI/CD environments:

* [Security advisories](https://nodesecurity.io/) by the Node Security Project
* [Vulnerability DB](https://snyk.io/vuln?type=npm) by Snyk


## Securing your Node.js applications

It is time to get hands on, and make your application more secure! If you follow and implement the next items carefully, you are already making great progress towards a more secure Node.js application.

### Using the Helmet module

Helmet helps you secure your Express applications by setting various HTTP headers, like:

* [X-Frame-Options](https://helmetjs.github.io/docs/frameguard/) to mitigates clickjacking attacks,
* [Strict-Transport-Security](https://helmetjs.github.io/docs/hsts/) to keep your users on HTTPS,
* [X-XSS-Protection](https://helmetjs.github.io/docs/xss-filter/) to prevent reflected XSS attacks,
* [X-DNS-Prefetch-Control](https://helmetjs.github.io/docs/dns-prefetch-control/) to disable browsers’ DNS prefetching.

Helmet supports a lot more headers, to get the full list, visit the [official documentation of helmet](https://helmetjs.github.io/docs/).

Adding `helmet` to your applications is just a few lines of codes away:

```javascript
const express = require('express')
const helmet = require('helmet')

const app = express()

app.use(helmet())
```

### Validating user input

Validating user input is one of the most important things to do when it comes to the security of your application. Failing to do it correctly can open up your application and users to a wide range of attacks, including [command injection](https://www.owasp.org/index.php/Command_Injection), [SQL injection](https://www.owasp.org/index.php/SQL_Injection) or [stored cross-site scripting](https://www.veracode.com/security/sql-injection).

To validate user input, one of the best libraries you can pick is [joi](https://www.npmjs.com/package/joi). Joi io an object schema description language and validator for JavaScript objects. To get an idea what it can do for you, take a look at the following example:

```javascript
const Joi = require('joi');
 
const schema = Joi.object().keys({
    username: Joi.string().alphanum().min(3).max(30).required(),
    password: Joi.string().regex(/^[a-zA-Z0-9]{3,30}$/),
    access_token: [Joi.string(), Joi.number()],
    birthyear: Joi.number().integer().min(1900).max(2013),
    email: Joi.string().email()
}).with('username', 'birthyear').without('password', 'access_token')
 
// Return result
const result = Joi.validate({ 
    username: 'abc', 
    birthyear: 1994 
}, schema)
// result.error === null -> valid
```

When it comes to validating user input passed into SQL queries, you should always escape any user provided data before using it in a SQL query in combination with validating user input. If you are using the `mysql` module, it looks something like this:

```javascript
connection.query('UPDATE users SET foo = ?, bar = ?, baz = ? WHERE id = ?', ['a', 'b', 'c', userId], function (error, results, fields) {
  // ...
})
```

If you are using an ORM like [sequelize](http://docs.sequelizejs.com/), it comes with the concept of *Prepared Statements* (also sometimes referred as *Parameterized Statements* too). Just like the `mysql` module, it understands `?` and will encode it properly.

### Securing your Regular Expressions

Regular Expressions are a great way to manipulate texts and get the parts that you need from them. However, there is an attack vector called [Regular Expression Denial of Service](https://www.owasp.org/index.php/Regular_expression_Denial_of_Service_-_ReDoS) attack, which exposes the fact that most Regular Expression implementations may reach extreme situations for specially crafted input, that cause them to work extremely slowly.

Combined with the fact that Node.js is single-threaded by nature, with a single user input a whole server can be taken down. The Regular Expressions that can do such a thing are commonly referred as **Evil Regexes**. These expressions contain:

* grouping with repetition,
* inside the repeated group:
  * repetition, or
  * alternation with overlapping

**Examples of Evil Regular Expressions patterns:**

* `(a+)+`
* `([a-zA-Z]+)*`
* `(a|aa)+`

To detect these regular expressions, you can use the [safe-regex](https://www.npmjs.com/package/safe-regex) module.

### Security.txt

Security.txt defines a standard to help organizations define the process for security researchers to securely disclose security vulnerabilities. Currently, it is in a draft stage, with the following intended purpose:

> When security risks in web services are discovered by independent security researchers who understand the severity of the risk, they often lack the channels to properly disclose them. As a result, security issues may be left unreported. Security.txt defines a standard to help organizations define the process for security researchers to securely disclose security vulnerabilities. - *from the [security.txt draft](https://tools.ietf.org/html/draft-foudil-securitytxt-00)*

In case you'd like to add it to your application to help researchers report security bugs to you, I've just published a Node.js module that makes it super easy, called [express-security.txt](https://github.com/gergelyke/express-security.txt). Adding it to your app is as easy as:

```javascript
const express = require('express')
const securityTxt = require('express-security.txt')

const app = express()

app.get('/security.txt', securityTxt({
  // your security address
  contact: 'email@example.com',
  // your pgp key
  encryption: 'encryption',
  // if you have a hall of fame for securty resourcers, include the link here
  acknowledgements: 'http://acknowledgements.example.com'
}))
```

## Outro

I hope this article helped you better understand how security in the Node.js ecosystem works, and what steps you can make to make your Node.js applications more secure. Did I miss something important? Please let me know in the comments sections below! 🗯

## Resources

### Further readings

* [Node.js security checklist](https://blog.risingstack.com/node-js-security-checklist/)
* [OWASP Node.js Security](https://www.owasp.org/images/f/fa/AppSecIL2016_NodeJS-Security_LiranTal.pdf)
* [Node.js Security Handbook](https://www.sqreen.io/resources/nodejs-security-handbook.html)

### Companies helping with Node.js security

* [^Lift Security](https://liftsecurity.io) - penetration testing, application security
* [RisingStack](https://risingstack.com) - code reviews, application security
* [NSP](https://nodesecurity.io/) - continuous security monitoring for your node apps
* [Snyk](https://snyk.io) - continuously find & fix vulnerabilities in your dependencies
* [Sqreen](https://www.sqreen.io/) - web application and user protection
