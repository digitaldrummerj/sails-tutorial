## Overriding Built-In REST Verbs

Up to this point we have been using all of the built-in REST function.  However, we are going to want to create our own function with our own business logic.  

Here is a short list of some of the business logic we will be creating:

* Validate that the email address is an actual email address
* Encrypted the password
* Remove the encrypted password from the returned json
* Only allow a user to get, update and delete data that they own
* Allow a user to signup 
* Allow a user to login
* Allow a user to sign out
Be able to query to the current logged in user
* Associate a user to a todo item
* Store the logged in user in session state

### Function Map
To override the built-in get, post, put, and delete, we will be creating methods in the controller.  Below is the mapping of REST verbs to function names.

| VERB | Method Name |Description |
| ---- | ---- | ---- |
| POST | create | Insert new record
| GET | find | Get all records |
| GET/:id | findOne | Get record by ID |
| PUT  | update  | Update record |
| DELETE |  destroy | Remove record | 

### Function Format

The standard format of the sails controller function looks like the following with replacing *METHOD_NAME* with the method name from above.   

<h4 class="exercise-start">
Function Format Example
</h4>

```javascript
METHOD_NAME: function METHOD_NAMEFn(req, res) {
     // get body of the request
     var body = req.body; 
     
     // get id from either querystring or body
     var id = req.params.id; 
}
```

This format for the function is the same for the custom functions that we will be creating for logging in and logging out. 

<div class="exercise-end"></div> 

