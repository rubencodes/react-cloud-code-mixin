# react-cloud-code-mixin

[![npm version](https://badge.fury.io/js/react-cloud-code-mixin.svg)](https://badge.fury.io/js/react-cloud-code-mixin)

Mixin for React components to easily listen for changes and re-render from Parse Cloud Code function calls. The format here is very heavily inspired from Parse-React's Mixin.


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
- `propDeps`: an array with the names of each `prop` whose value change should cause a reload.
- `stateDeps`: an array with the names of each `state` whose value change should cause a reload.
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
        propDeps: ['userAge']
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

## Install

```
npm install react-cloud-code-mixin
```

## Depencies
None! Well, except Parse. But that's a given.

## License

MIT
