# Asana [![Build Status][travis-image]][travis-url] [![Dependency Status][depstat-image]][depstat-url]

A node.js client for the 1.0 version of the Asana API. This client is a thin
client for the API which means it tries just to provide proxy methods for the 
API and does no local validation.

## Examples

### Find all incomplete tasks assigned to me for today across my workspaces

```js
var asana = require('./index');
var Promise = require('bluebird');
var util = require('util');

var client = asana.Client.basicAuth(process.env.ASANA_API_KEY);

client.users.me().then(function(user) {
  return Promise.map(user.workspaces, function(workspace) {
    return client.tasks.findAll({
      assignee: user.id,
      workspace: workspace.id,
      opt_fields: 'id,name,assignee_status,completed'
    }).filter(function(task) {
      return task.assignee_status === 'today' && !task.completed;
    });
  });
}).reduce(function(list, tasks) {
  return list.concat(tasks);
}, []).then(function(list) {
  console.log(util.inspect(list, {
    colors: true,
    depth: null
  }));
});
```

## Installation

Install with npm

```sh
npm install asana --save
```

## Documentation

The code is thoroughly documented with JsDoc tags and online documentation can
be found [here](http://pspeter3.com/node-asana).

[travis-url]: http://travis-ci.org/pspeter3/node-asana
[travis-image]: http://img.shields.io/travis/pspeter3/node-asana.svg?style=flat

[depstat-url]: https://gemnasium.com/pspeter3/node-asana
[depstat-image]: http://img.shields.io/gemnasium/pspeter3/node-asana.svg?style=flat