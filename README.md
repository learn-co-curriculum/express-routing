# Express Routing

## Objectives

1. Explain how to declare routes in Express
2. Explain how callbacks are used to handle requests
3. Describe how to modularize an application using `express.Router()`
4. Explain how to mount a router on an Express application

## Introduction

Let's say that we have streets, and every street has buildings

Streets are numbered consecutively

```
/streets[/{1-n}]
```

As are buildings:

```
/streets/{1-n}/buildings[/{1-n}]
```

Following standard REST conventions

We want to build an express application that exposes streets and buildings to users in this way. So we might have an app like

```js
// routes/streets.js

const express = require('express');
const router = express.Router();

const streets = [{
  name: 'Main',
  id: 1
}, {
  name: 'West',
  id: 2
}];

exports.router = function(app) {
  app.get('/', (req, res) => {
    res.json(streets);
  });

  app.get('/:id', (req, res) => {
    res.json(streets.find(({ id }) => id === req.params.id));
  });
};

exports.streets = streets;
```

```js
// routes/buildings.js

const express = require('express');
const router = express.Router();

const streets = require('./streets').streets

const buildings = [{
  name: 'Flatiron',
  id: 1,
  streetId: 1
}, {
  name: 'City Hall',
  id: 2,
  streetId: 1
}];

exports.router = function(app) {  
  app.get('/streets/:streetId/buildings', (req, res) => {
    res.json(buildings.filter(({ streetId }) => 
      streetId === req.params.streetId));
  });

  app.get('/streets/:streetId/buildings/:id', (req, res) => {
    const buildingsOnStreet = buildings.filter(({ streetId }) => 
      streetId === req.params.streetId);
    res.json(buildingsOnStreet.find(({ id }) => id === req.params.id));
  });
};
```

```js
// app.js

const express = require('express');
const streetRoutes = require('./routes/streets').router;
const buildingRoutes = require('./routes/buildings').router;

// boilerplate ...

streetRoutes(app);
buildingRoutes(app);
```

(This is a janky, untested example — there's a better way to organize this stuff.)

But the point is we should walk students through this sort of thing.

## Resources

- [Express Router](http://expressjs.com/en/4x/api.html#router)
