# rooter-router

This is a super simple javascript router implementation.

It handles multiple levels of resources and dynamic segments, and can handle method-specific routes.

NOTE: If you elect to use method specific routers, you cannot also use general routes for that endpoint.

Splats: Rooter-router now has a basic splats implementation. Note that splats are treated as less specific, which has the following repercussions:

Given:
```testRouter.add('/public/*', function (req, res, helper) {
      res.end(helper.routeMatched)
    })
  .add('/public/resource/:test', function (req, res, helper) {
      res.end(helper.dynamics.test)
    })```

the test:

```request(app)
      .get('/public/resource/foobar')
      .expect('foobar’)```

passes.

Usage looks something like this:

```
var http = require('http'),
Rooter = require('rooter-router')

router = new Rooter

router.add('/foo', function(req, res, url){
    res.end('Index for ' + url.resources[0])
})
router.add('/foo/:id', function(req, res, url){
  res.end('Got an instance of a foo: ' + url.dynamics.id)
})
router.add('/foo/new', function(req, res, url){
  res.end('This must have been a get request')
},'GET')
router.add('/foo/:bar/baz', function(req, res, url){
  res.end( url.resources[0] + '/' + url.dynamics.bar + '/' + url.resources[1])
})

http.createServer(function (req, res) {
  router.handle(req, res)
}).listen(9001)
```

Note that the URL object available to your callback includes a standard url.parse object, in addition to elements such as:
* resources (an array)
* dynamics (an object, where the key name is the dynamic segment)
* verb (method)
* matchedRoute

Testing requires supertest (for testing only). Tests cover essential functions.
