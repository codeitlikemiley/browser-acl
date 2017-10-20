# browser-acl

[![build status](http://img.shields.io/travis/mblarsen/browser-acl.svg)](http://travis-ci.org/mblarsen/browser-acl)
[![Coverage Status](https://coveralls.io/repos/github/mblarsen/browser-acl/badge.svg?branch=master)](https://coveralls.io/github/mblarsen/browser-acl?branch=master)
[![Known Vulnerabilities](https://snyk.io/test/github/mblarsen/browser-acl/badge.svg)](https://snyk.io/test/github/mblarsen/browser-acl)
[![NPM version](http://img.shields.io/npm/v/browser-acl.svg)](https://www.npmjs.com/package/browser-acl/) [![](https://img.shields.io/npm/dm/browser-acl.svg)](https://www.npmjs.com/package/browser-acl/)

> Simple ACL library for the browser inspired by Laravel's guards and policies.

# Install

```
yarn add browser-acl
```

# Usage

```javascript
import Acl from 'browser-acl'
const acl = new Acl()

// Attach acl function to user class/constructor
// Adds: user.can() function
acl.mixin(User)

acl.rule('view', Post)
acl.rule(['edit', 'delete'], Post, (user, post) => post.userId === user.id)

if (user.can('edit', post)) {
  // code for when user has permission
}
```

Policies are also supported:

```javascript
acl.policy({
    view: () => true,
    edit: (user, post) => post.id === user.id),
}, Post)

if (user.can('edit', post)) {
  // code for when user has permission
}
```
Note: policies takes precedence over rules.

# API

<!-- Generated by documentation.js. Update this documentation by updating the source code. -->

### Table of Contents

-   [Acl](#acl)
    -   [rule](#rule)
    -   [policy](#policy)
    -   [register](#register)
    -   [can](#can)
    -   [some](#some)
    -   [every](#every)
    -   [mixin](#mixin)
    -   [subjectMapper](#subjectmapper)

### rule

You add rules by providing a verb, a subject and an optional
test (that otherwise defaults to true).

If the test is a function it will be evaluated with the params:
user, subject, and subjectName. The test value is ultimately evaluated
for thruthiness.

**Parameters**

-   `verbs` **([Array](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array)&lt;[string](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String)> | [string](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String))**
-   `subject` **([Function](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/function) \| [Object](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object) \| [string](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String))**
-   `test` **[Boolean](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Boolean)** =true (optional, default `true`)

Returns **[Acl](#acl)**

### policy

You can group related rules into policies for a subject. The policies
properties are verbs and they can plain values or functions.

If the policy is a function it will be new'ed up before use.

```javascript
  class Post {
    constructor() {
      this.view = true       // no need for a functon

      this.delete = false    // not really necessary since an abscent
                             // verb has the same result
    },
    edit(user, subject) {
      return subject.id === user.id
    }
  }
```

Policies are useful for grouping rules and adding more comples logic.

**Parameters**

-   `policy` **[Object](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object)** A policy with properties that are verbs
-   `subject` **([Function](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/function) \| [Object](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object) \| [string](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String))**

Returns **[Acl](#acl)**

### register

Explicitly map a class or constructor function to a name.

You would want to do this in case your code is heavily
minified in which case the default mapper cannot use the
simple "reflection" to resolve the subject name.

Note: If you override the subjectMapper this is not used,
bud it can be used manually through `this.registry`.

**Parameters**

-   `object` **[Function](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/function)** A class or constructor function
-   `subjectName` **[string](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String)** 

### can

Performs a test if a user can perform action on subject.

The action is a verb and the subject can be anything the
subjectMapper can map to a subject name.

E.g. if you can to test if a user can delete a post you would
pass the actual post. Where as if you are testing us a user
can create a post you would pass the class function or a
string.

```javascript
  acl->can(user, 'create', Post)
  acl->can(user, 'edit', post)
```

Note that these are also available on the user if you've used
the mixin:

```javascript
  user->can('create', Post)
  user->can('edit', post)
```

**Parameters**

-   `user` **[Object](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object)**
-   `verb` **[string](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String)**
-   `subject` **([Function](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/function) \| [Object](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object) \| [string](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String))**
-   `args` **...any** Any other param is passed into rule

Returns **any** Boolean

### some

Like can but subject is an array where only some has to be
true for the rule to match.

Note the subjects do not need to be of the same kind.

**Parameters**

-   `user` **[Object](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object)**
-   `verb`
-   `subjects` **[Array](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array)&lt;([Function](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/function) \| [Object](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object) \| [string](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String))>**
-   `args` **...any** Any other param is passed into rule

Returns **any** Boolean

### every

Like can but subject is an array where all has to be
true for the rule to match.

Note the subjects do not need to be of the same kind.

**Parameters**

-   `user` **[Object](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object)**
-   `verb`
-   `subjects` **[Array](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array)&lt;([Function](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/function) \| [Object](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object) \| [string](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String))>**
-   `args` **...any** Any other param is passed into rule

Returns **any** Boolean

### mixin

Mix in augments your user class with a `can` function object. This
is optional and you can always call `can` directly on your
Acl instance.

    user.can()
    user.can.some()
    user.can.every()

**Parameters**

-   `User` **[Function](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/function)** A user class or contructor function

### subjectMapper

Rules are grouped by subjects and this default mapper tries to
map any non falsy input to a subject name.

This is important when you want to try a verb against a rule
passing in an instance of a class.

-   strings becomes subjects
-   function's names are used for subject
-   objects's constructor name is used for subject

Override this function if your models do not match this approach.

E.g. say that you are using plain data objects with a type property
to indicate the "class" of the object.

```javascript
  acl.subjectMapper = s => typeof s === 'string' ? s : s.type
```

`can` will now use this function when you pass in your objects.

See [register()](#register) for how to manually map
classes to subject name.

**Parameters**

-   `subject` **([Function](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/function) \| [Object](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object) \| [string](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String))**

Returns **[string](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String)** A subject
