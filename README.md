# react-cloud-code-mixin

[![npm version](https://badge.fury.io/js/react-cloud-code-mixin.svg)](https://badge.fury.io/js/react-cloud-code-mixin)

Mixin for React components to easily listen for changes and re-render from Parse Cloud Code function calls. The format here is very heavily inspired from [ParseReact's Mixin](https://github.com/ParsePlatform/ParseReact/blob/master/docs/api/Mixin.md).


## How to Use

The first step is to include the mixin in your component like so:

```javascript
var MyComponent = React.createClass({
  mixins: [ ParseCCMixin ],
  /*...*/
});
```

This mixin requires you to define a `loadData` function (analagous to ParseReact's observe function). It returns an object where the keys are the name of the data key where the results will be stored, and the value is an object containing data for running the Cloud Code function (details below). For example:

```javascript
var MyComponent = React.createClass({
  mixins: [ ParseCCMixin ],
  loadData: function(props, state) {
    return {
      MyCloudCodeData: {
        name: "getMyCloudCodeData",
        params: {
          myFirstParam: "foo",
          mySecondParam: "bar"
        }
      }
    };
  },
  /*...*/
  render: function() {
    //example render function
    return (
      <h1>{this.data.MyCloudCodeData.join(", ")}</h1>
    );
  }
});
```
Whenever the component mounts, it will issue a function call to the `/getMyCloudCodeData` endpoint and the results will be stored in `this.data.MyCloudCodeData`. Any time props or state change, the function will be called again (unless otherwise configured—details below).

The objects associated with the data keys can take the following parameters:

- `name`: the name of the Parse Cloud Code function you wish to run.
- `params`: the parameters passed to the cloud code function.
- `propDeps`: an array with the names of each `prop` whose value change should cause a reload (e.g. the component will only pull data and re-render if one of these changed).*
- `stateDeps`: an array with the names of each `state` whose value change should cause a reload (e.g. the component will only pull data and re-render if one of these changed).*
- `defaultValue`: the default value assigned to the data key on mount.

Example:
```javascript
var MyComponent = React.createClass({
  mixins: [ ParseCCMixin ],
  loadData: function(props, state) {
    return {
      Users: {
        name: "getUsers",
        params: {
          age: this.props.userAge
        },
        propDeps: ['userAge'],
        defaultValue: []
      }
    };
  },
  /*...*/
  render: function() {
    //example render function
    return (
      <h1>{this.data.Users.join(", ")}</h1>
    );
  }
});
```

*Array and Object values not yet supported.

## Additional Component Methods
###`this.pendingQueries()` 
Returns an array containing the names of queries that are currently waiting for results.
###`this.queryErrors()` 
Returns a map of query names to the error they encountered on the last request, if there was one.
###`this.reloadData()` 
Forces a full refresh of all cloud code calls. 

**Coming Soon**: Will optionally take an array of function names as a param and reload only those functions.

## Install

```
npm install react-cloud-code-mixin
```

Then, in your React project:

```javascript
var ParseCCMixin = require('react-cloud-code-mixin');
```

Or, if you're using ES6/ECMA2015 syntax:
```javascript
import ParseCCMixin from 'react-cloud-code-mixin'
```

## Depencies
None! Well, except Parse. But that's a given.

## F.A.Q.
**Does this replace ParseReact Mixin?**

It can, but it doesn't have to. This project started when my company switched from doing Parse queries client-side to making API calls in order to reduce client-side logic and network usage. We use this in our app now instead of ParseReact, but the two can operate side by side (as long as you don't name two data params the same).

**Is this production ready?**

Like with most open-source software, I can't make any guarantees, but I can tell you it's stable enough that the company I work at is using actively it in production.

**How can I contribute?**

Feel free to fork this project and create a pull request! I'll merge in anything useful. Thanks!

## License

MIT
