title: Web development best practices
class: animation-fade
layout: true

<!-- This slide will serve as the base layout for all your slides -->
.bottom-bar[
  {{title}}
]

---

class: impact

# {{title}}
## Testing, QA, CI

---

Topics:
* linting
* writing unit test
* integration test
* e2e test
* sending a pull request
* bitrise meets a web app

---

# A simple snippet

```
var foo = "foobar"
if (foo == 'bar') {
  // true
}
```
???
What issues can we spot?
* using `var` for constant
* not using semicolon
* using both single quote and double quote

---
class: center, middle

# ~~jshint~~ eslint
---
class: center,middle
You can create your own collection of rules, like [we do](https://github.com/purposeindustries/eslint-config-pi)
---
class: center,middle
or implement someone else's: semistandard.
???
Funny story: the original version of [semistandard](https://github.com/Flet/semistandard) was [standard](https://github.com/standard/standard) and someone did a version for fun because standard didn't include semicolons.

---
class: center,middle
`npm init --yes`

--

`npm i --save-dev semistandard`

---
class: center,middle
`$(npm bin)/semistandard`
???
We should see errors

---
# It can even fix (some of) those

```
$(npm bin)/semistandard --fix
```
---

# Let's add the `semistandard` command to `npm scripts`

```
  "scripts": {
+   "test": "semistandard"
  }
```
---
class: middle, center

# Let's push it to bitbucket

---
class: middle, center

# Let's add to bitrise

---

`bitrise.yml`

```
---
format_version: '3'
default_step_lib_source: https://github.com/bitrise-io/bitrise-steplib.git
project_type: other
trigger_map:
- push_branch: "*"
  workflow: primary
- pull_request_source_branch: "*"
  workflow: primary
workflows:
  primary:
    steps:
    - activate-ssh-key@3.1.1:
        run_if: '{{getenv "SSH_RSA_PRIVATE_KEY" | ne ""}}'
    - git-clone@3.4.3: {}
    - npm@0.9.0:
        inputs:
        - command: install
    - npm@0.9.0:
        inputs:
        - command: test
```

---

# It failed, let's fix it.
???
Do not commit after the fix.
---

# A commit guideline by the angular authors: [link]( https://github.com/angular/angular.js/blob/master/CONTRIBUTING.md#commit)

```
<type>(<scope>): <subject>
```

---

# Type

Must be one of the following:

*feat*: A new feature

*fix*: A bug fix

*docs*: Documentation only changes

*style*: Changes that do not affect the meaning of the code (whitespace, formatting, missing semi-colons, etc)

*refactor*: A code change that neither fixes a bug nor adds a feature
*perf*: A code change that improves performance
*test*: Adding missing or correcting existing tests

*chore*: Changes to the build process or auxiliary tools and libraries such as documentation generation

---

# Scope

describes which part of the project we touched, eg:

* http module
* cache manager
* core
* about view

---

# An example

```
fix (core): fixed missing semicolons which caused #43, #45 in the minified js
```

The branch's name would be:

```
fix/missing-semicolon
```
---
class: center,middle
# Yay, the build should pass

---
class: center,middle
# But we should prevent committing failing tests or lint issues

---
class: center,middle
`npm install pre-commit -D`

???
`-D` means `--save-dev`

---

`package.json`

```
+  "pre-commit": [
+     "test"
+  ]
```

---
class: center,middle
To bypass it commit with `--no-verify` or `-n`
(`git commit -m "my message" -n`)

---
# My favourite `package.json`

```
  "scripts": {
    "lint": "semistandard",
    "test": "mocha '**/*.spec.js'"
  }
```

---
# Let's write a koa app which downloads a json file and emits its content.

## Let's pick a datasource from [awesome-json-datasets](https://github.com/jdorfman/awesome-json-datasets#nasa)
???
Pick eg how many astronauts are in the space

---

```
import Koa from 'koa';
import { get } from 'koa-route';
import request from 'request-promise-native';
const app = new Koa();
app.use(get('/', async (ctx) => {
  ctx.body = await request({
    url: 'http://api.open-notify.org/astros.json',
    json: true
  });
}));
app.listen(3000);
```

---

# Integration test

`app.spec.js`

```
import app from './app'
import supertest from 'supertest'

const request = supertest.agent(app.listen())

describe('Test /', function () {
  it('should return the astronauts', function (done) {
    request
      .get('/')
      .expect(200)
      .end((err, res) => {
        res.body.people.should.be.an.Array();
      });
  })
})
```

???
`npm i --save-dev mocha should supertest`
`mocha -r should '**/*.spec.js'`

---

# Unit test
> The goal of unit testing is to isolate each part of the program and show that the individual parts are correct (wikipedia - unit testing)

---

##We need to reorganize the repository to be able to fulfill the definition.

## https://github.com/oroce/koa-api

---

class: center,middle
# E2E test
## end to end testing
---

# A simple html page

```
<html>
<head>
  <title>test page</title>
</head>
<body>
  <button id="btn-incr">click me</button>
  <button id="btn-clear">clear</button>
  <div class="content">0</div>
  <script type="text/javascript">
    var btnIncr = document.querySelector('#btn-incr');
    var btnClear = document.querySelector('#btn-clear');
    var content = document.querySelector('.content');
    btnIncr.addEventListener('click', function() {
      content.innerText = +content.innerText + 1;
    }, false);
    btnClear.addEventListener('click', function() {
      content.innerText = 0;
    }, false);
  </script>
</body>
</html>
```

---

# We need:

* a chrome
* `karma` module
* `http-server`
---

```
npm i --save-dev karma \
                 karma-chrome-launcher \
                 karma-ng-scenario \
                 http-server
```

---

`karma.conf.js`

```
module.exports = function(config) {
  config.set({
    frameworks: ['ng-scenario'],
    files: [
      './test/*.js'
    ],
    proxies: {
      '/test-page': 'http://localhost:8080/index.html'
    },
    browsers: ['Chrome'],
    reporters: ['dots'],
    singleRun: true,
  })
}
```

---

`test/test.js`

```
describe('buttons', function () {
  it('increase should increase', function () {
    browser().navigateTo('/test-page');
    element('#btn-incr').click();
    element('#btn-incr').click();
    expect(element('.content').text()).toBe('2');
  });

  it('clear should clear', function () {
    browser().navigateTo('/test-page');
    element('.content').text('na');
    element('#btn-clear').click();
    expect(element('.content').text()).toBe('0');
  });
});
```

---

```
  "scripts": {
+   "test": "karma start karma.config.js"
  }
```

---
class: middle, center

# npm test

---
class: center,middle
# Let's grab a saucelabs account

---
class: center, middle
# An example: [oroce/bitrise-saucelabs](https://github.com/oroce/bitrise-saucelabs)

---

# Being secured!

## security headers
## csrf
## session
## validation
## password hashing

---

# Security headers

## Use [koa-helmet](https://github.com/venables/koa-helmet)!

* Content Security Policy
* DNS prefetch control
* frame guard
* remove powered by header
* HTTP Public Key Pinning
* HTTP Strict Transport Security
* IE No Open
* no cache
* no sniffing mime type
* hide the referrer
* X-XSS header

---

# CSRF

## Cross-site request forgery

## CSRF module: [koajs/csrf](https://github.com/koajs/csrf)


On login pages it's a must have. Also highly recommended on forms.

???
[wikipedia](https://en.wikipedia.org/wiki/Cross-site_request_forgery)

Like using html snippet in a forum signature, controlling youtube account (it happened).

---

# Sessions

## Using JWT [JSON Web Token](https://en.wikipedia.org/wiki/JSON_Web_Token)

* identity change between the server and client
* encrypted on the server
* uses json format

???
Read more: https://dev.to/neilmadden/7-best-practices-for-json-web-tokens

---

# Validation

## I know you can write an email validation regex but I recommend to use a module for that, like [`validator.js`](https://github.com/chriso/validator.js)

---

# Password hashing

## `MD5(password)` is not password hashing

Use something more reliable: [credential](https://github.com/ericelliott/credential)

---

# Further thougts

* hashicorp's vault
* jest from facebook
* postcss-lint
* danger ci
