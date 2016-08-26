# Express Routing

When we go to a website like Facebook or Twitter, we see different pages for different URLs. In this lesson we are going to learn how add URLs to our Express app. We'll also look at some advanced techniques for how to manage routing once we have lot of URLs.

## Objectives

1. Explain how to declare routes in Express
2. Explain how callbacks are used to handle requests
3. Describe how to modularize an application using `express.Router()`
4. Explain how to mount a router on an Express application

## Structure of URLs

Before building our Express app, let's quickly review the parts of a URL. If we have a url such as `https://flatironschool.com/campuses/online/`, it is made up of the following parts:

* Protocol - `https`
* Host - `flatiron.com`
* Path - `/campuses/online`

In this lesson we will be focusing on the `path` of the url, and how our app can do different things based on what the `path` is.

## Hello Routes

Let's start with our standard Hello World Express app:

```js
var express = require('express');
var app = express();

app.get('/', function(req, res) {
  res.send('Hello World!');
});

app.listen(3000, function() {
  console.log('Express app listening on port 3000!');
});
```

Visiting localhost:3000 in our browser, we should see `Hello World!`. Note that this is because our Express app treats, `localhost:3000` the same as `localhost:3000/`.

Another way to test this route is to run `curl localhost:3000` in your terminal. Try this to make sure you get `Hello World!`.

## How Does Routing Work?

Try running `curl localhost:3000/hello`. You should see `Cannot GET /hello`. Why? Because our app doesn't know to handle the `/hello` path. Each time a request to our app is made, the Express app checks routes that have been registered.

In Express, a route is a path and a callback function. We used `app.get` above to register a route. When a request is made for that path, the callback function that was the second argument to `app.get` gets called. That function is given a request and a response. In our callback function, we've called these arguments `req` (for request) and `res` (for response). Check out the docs to learn more about Express [requests](https://expressjs.com/en/4x/api.html#req) and [responses](https://expressjs.com/en/4x/api.html#res). We use the response argument's `send` method to send data back to the requester.

Let's add a route for `/hello` and try that curl command again. Our code show now look something like this:

```js
var express = require('express');
var app = express();

app.get('/', function(req, res) {
  res.send('Hello World!');
});

app.get('/hello', function(req, res) {
  res.send('Hello hello!');
});

app.listen(3000, function() {
  console.log('Express app listening on port 3000!');
});
```

Running `curl localhost:3000/hello` we should get `Hello hello!`. Nice!

## Catch em All

Many websites, such as Facebook, have many different routes people can go to. For example, Facebook has the URL https://www.facebook.com/FlatironSchool. It would be very challenging for a developer to write a new path for each of Facebook's 1 billion users.

Instead Facebook uses what is called _route parameters_ to let every user have their own separate url. A _route parameter_ captures a segment of the path and stores it in a variable. Let's try this out in our own app.

```js
var express = require('express');
var app = express();

app.get('/', function(req, res) {
  res.send('Hello World!');
});

app.get('/hello', function(req, res) {
  res.send('Hello hello!');
});

// :name is our route parameter. We can access
// it in req.params.name
app.get('/hello/:name', function(req, res) {
  res.send('Hello ' + req.params.name);
});

app.listen(3000, function() {
  console.log('Express app listening on port 3000!');
});
```

The `:` tells the Express router that rather than to match the string exactly, it should store that part of the path inside of the `req.params` object.

When we curl `curl localhost:3000/hello/Ann` we see `Hello Ann`. And when we run `curl localhost:3000/hello/Chuck` the same routing function gets called, but this time we see `Hello Chuck`.

## Routes on Routes

As our app grows, our routes will likely get longer and more complicated. In our three route example above, we already have nested routes: `/hello/:name` and `/hello` share the same root (`/hello`).

We also might have routes that are all related and we want to group them together. Luckily Express helps us out with this by letting us create different routers. Different routers will let us create path and callback function pairs just like we did with `app.get`.

We've just decided that ping pong is going to be the next big thing so we want to add it to our app. Let's create a file called `ping-pong.js` and write the following code:

```js
// ping-pong.js
var express = require('express');
var router = express.Router();

router.get('/ping', function(req, res) {
  res.send('pong!');
})

router.get('/pong', function(req, res) {
  res.send('ping!');
})

module.exports = router;
```

Then in `app.js` we can add these routes like so:

```js
// app.js
var express = require('express');
var app = express();
var pingPongRouter = require('./ping-pong');

app.get('/', function(req, res) {
  res.send('Hello World!');
});

app.get('/hello', function(req, res) {
  res.send('Hello hello!');
});

app.get('/hello/:name', function(req, res) {
  res.send('Hello ' + req.params.name);
});

app.use('/ping-pong', pingPongRouter);

app.listen(3000, function() {
  console.log('Express app listening on port 3000!');
});
```

Now try running `curl localhost:3000/ping-pong/ping` and `curl localhost:3000/ping-pong/pong`.

Why should we get so excited about moving some routes into a separate router? For one it helps keep our files smaller and more contained. Rather than having all of our routes in one file, we can have small files with a a group of routes.

Another reason is now we can easily _mount_ our router on any prefix. Let's say instead of wanting our paths to be `/ping-pong/ping` and `/ping-pong/pong`, we wanted them to be `/superfungame/ping` and `/superfungame/pong`. Great news, we only have to change one small part of our code! See `app.js` below to see the change.

```js
// app.js
var express = require('express');
var app = express();
var pingPongRouter = require('./ping-pong');

app.get('/', function(req, res) {
  res.send('Hello World!');
});

app.get('/hello', function(req, res) {
  res.send('Hello hello!');
});

app.get('/hello/:name', function(req, res) {
  res.send('Hello ' + req.params.name);
});

// All we had to do was change ping-pong to superfungame!
app.use('/superfungame', pingPongRouter);

app.listen(3000, function() {
  console.log('Express app listening on port 3000!');
});
```

If we structure our routes with routers, it will be very easy for us change what paths cause our routing functions to be called.

## Conclusion

In this lesson we learned how to start building an application with many routes. We say how we can capture parts of the path; the same technique that Facebook uses to create a separate url for each of their users. We also saw that we can create a separate router using `express.Router` which we can then mount in into our Express app.

With this routing foundation we can now build lots of useful Express apps!

## Resources

- [Express Router](http://expressjs.com/en/4x/api.html#router)
