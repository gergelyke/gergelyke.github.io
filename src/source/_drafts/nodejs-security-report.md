---
title: Node.js Security Report
description: A look at where we are with Node.js security, and what you can do to keep your customer's data safe
githubIssueId: 3
date: 2017-10-12
tags:
    - 'report'
    - 'node.js'
---

The initial Node.js release happened a little bit more than 8 years ago. Since then Node.js became of the leading platforms for building backend applications. As such, security becomes more and more important. In this first part of this article you will learn where Node.js and npm are where it comes to security, and in the second part, you will learn some security practices which you can immediately apply to your Node.js applications.

## Why security matters?

As a Node.js developer, you most probably work with a huge amount of user data - some of them being very sensitive in nature. Businesses built in the cloud-native era should always treat their data in the top-most respect.

**As the saying goes - your attackers need to get things right only once, while you as an application developer has to be right all the time.**

One of the best examples of this is the website called [have i been pwned?](https://haveibeenpwned.com/) operated by [Troy Hunt](https://twitter.com/troyhunt), which collects security breaches, and makes publicly available breaches searchable, so you can know if one of your accounts may got exploited 💥. Breached websites include LinkedIn, Adobe or MySpace, just to name the bigger ones.

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

As the responsible maintainer and developer of the registry, npm, makes huge steps towards making npm more secure every step down the road, like:

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

## Resources

### Further readings

* [Node.js security checklist](https://blog.risingstack.com/node-js-security-checklist/)
* [OWASP Node.js Security](https://www.owasp.org/images/f/fa/AppSecIL2016_NodeJS-Security_LiranTal.pdf)
* [Node.js Security Handbook](https://www.sqreen.io/resources/nodejs-security-handbook.html)

### Companies helping with Node.js security

* [^Lift Security](https://liftsecurity.io) - penetration testing, application security
* [RisingStack](https://risingstack.com) - code reviews
* [NSP](https://nodesecurity.io/) - continuous security monitoring for your node apps
* [Snyk](https://snyk.io) - continuously find & fix vulnerabilities in your dependencies
* [Sqreen](https://www.sqreen.io/) - web application and user protection
