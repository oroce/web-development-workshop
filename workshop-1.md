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
##  A basic api using koa

---
class: middle
# The stack

* [koa](http://koajs.com/)
* [babel](https://babeljs.io/)
* [npm](https://npmjs.org)

---

class: middle
# It's not MEAN

## MongoDB - Express - Angular - nodejs

---
class: center, middle
## mean is good for development but you need to understand the basics.

---
class: impact

# Let's get this started
---
class: impact
# npm
???

what commands do you know?
--

`npm install [pkg]` - `npm uninstall [pkg]`

--

`npm install --save-dev [pkg]` - `npm uninstall --save-dev [pkg]`

--

`npm start` - `npm test`

--

`npm run [script-command]`

--

`npm init`

--

`npm view [pkg]`

--

`npm --help` or the [docs.npmjs.com](https://docs.npmjs.com)

---
class: center, middle
## Let's open a terminal

```
mkdir koa-api
npm init
```
---

# `npm install --save babel-cli`

---
class: center,middle
# wait babel?
???
Who has ever heard of babel?
Who has ever used babel?
Okay, let's see what babel is
---
class: center, middle
# Babel is a JavaScript compiler.
## Use next generation JavaScript, today.

[http://babeljs.io/](http://babeljs.io/)
???
Let's play with it.
Show object spread operator
---
# babel itself isn't enough, let's install some plugins

```
npm i --save babel-preset-es2015 \
             babel-plugin-transform-object-rest-spread \
             babel-plugin-transform-es2015-modules-commonjs
```

---

`cat .babelrc`

```
{
  "presets": ["es2015"],
  "plugins": [
    "transform-object-rest-spread",
    "transform-es2015-modules-commonjs"
  ]
}
```

---

# Let's create a basic http server using the next version of javascript
## use `import`, spread operator

---

# A basic http server

```
var http = require('http');
var server = http.createServer(function(req, res) {
  res.write('Hello Dogfish');
  res.end();
});

server.listen(5000, function(err) {
  if (err) {
    console.error(err);
    process.exit(1);
  }
  console.log('Server has been started on port: %s', server.address().port);
});
```

---

# Let's try it out

`node app.js`

Navigate to `http://localhost:5000`
???
Protip: open `http://localhost:5000` from terminal
---

# A http server es.next

```
import { createServer } from 'http';
const server = createServer((req, res) => {
  res.write('Hello Dogfish');
  res.end();
});
server.listen(5000, err => {
  if (err) {
    console.error(err);
    process.exit(1);
  }
  console.log('Server has been started on port: %s', server.address().port);
});
```
---

`node app.js`

---

```
% node app.js
app.js:3
import { createServer } from 'http';
^^^^^^
SyntaxError: Unexpected token import
    at Object.exports.runInThisContext (vm.js:53:16)
    at Module._compile (module.js:513:28)
    at Object.Module._extensions..js (module.js:550:10)
    at Module.load (module.js:458:32)
    at tryModuleLoad (module.js:417:12)
    at Function.Module._load (module.js:409:3)
    at Function.Module.runMain (module.js:575:10)
    at startup (node.js:160:18)
    at node.js:456:3
```
---
# We need to run it with the `babel-node` instead of `node`

```
babel-node app.js
zsh: command not found: babel-node
```
---
## `babel-node` sits in `node_modules/.bin/babel-node`

--

## luckily we have `npm bin`, so we can use `$(npm bin)/babel-node`

--

## or just add to npm script as `start`

---
# koa

* from the authors of expressjs
* uses es.next
* less spaghetti
* more robust

---

```
npm i --save koa
```
---

Basic koa server:

```
import Koa from 'koa';
import { createServer } from 'http';
const app = new Koa();

app.use(ctx => {
  ctx.body = 'Hello Dogfish';
});
const server = createServer(app.callback());
server.listen(3000, err => {
  if (err) {
    console.error(err);
    process.exit(1);
  }
  console.log('Server has been started on port: %s', server.address().port);
});
```
---

Bored of keep restarting the app?

```
npm install --save-dev nodemon
```

---

```
  "scripts": {
+   "start:dev": "nodemon --exec 'npm start'",
    "start": "babel-node app.js"
  }
```
---

Koa's wiki is highly recommended: [https://github.com/koajs/koa/wiki](https://github.com/koajs/koa/wiki)

---
# Let's add

* a router
* a (json) bodyparser

???a
router: https://github.com/koajs/route
body parser: https://github.com/dlau/koa-body

---

# Let's make sure that

* `curl -X POST -H 'Content-Type: application/json' -d'{"type": "node"}' localhost:3000/submit` should respond with 400
* `curl -X POST -H 'Content-Type: application/json' -d'{"type": "npm"}' localhost:3000/submit` should respond with 400
* `curl -X POST -H 'Content-Type: application/json' -d'{"type": "dogfish"}' localhost:3000/submit` should respond with 200
---

# Let's create a small react app
## npm i -g create-react-app
