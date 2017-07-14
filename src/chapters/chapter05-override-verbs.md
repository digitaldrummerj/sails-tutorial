## Overriding Built-In REST Verbs

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
        body.wwid = req.session.isso.raw.id; //get wwid from isso object.  if isso is implemented
}
```

In the locking down routes chapter, we will override all of the built-in verbs.
