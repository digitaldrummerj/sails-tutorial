## Overriding Built-In REST Verbs

Up to this point we have been using all of the built-in REST function.  However, we are going to want to create our own function with our own business logic.  

To override the built-in get, post, put, and delete, you need to create methods in the controller.  Below is the mapping of REST verbs to function names.

* POST => create 
* GET => find
* GET/:id => findOne 
* PUT => update 
* DELETE => destory 

The standard format of the sails controller function looks like: 

```javascript
METHOD_NAME: function method_nameFn(req, res) {
        var body = req.body; // get body
        var id = req.params.id; // get querystring parameter id
        body.id = req.session.isso.raw.id; 
}
```

In the locking down routes chapter, we will override all of the built-in verbs.
