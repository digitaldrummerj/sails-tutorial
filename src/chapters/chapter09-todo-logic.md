## Todo API Logic

So far we have not done anything with the Todo API.  That is about to change though.  We are going to add the following functionality to the Todo API.

* Associate a Todo record to a User Record in a 1:Many setting (e.g. a User will have many Todo records and a Todo Record will have 1 user)
* Get the associated user from the req.session.user 
* Set completed for a new record to false by default
* Make sure that for viewing, updating and deleting the a user can only affect their records

### Associating User to Todo Items

We could just put the User id into the Todo record but that would not actually associate the data.  By creating an actual association we will be able to also include the associated user record with the Todo recrod.

<h4 class="exercise-start">
    <b>Exercise</b>: Associate Models
</h4>

1. Open api\models\Todo.js

    ```bash
    todo.js
    ```

1. In the attributes section add the following attribute to create a field that has an association to the User model.

    ```javascript
    user: {
      model: 'user'
    }
    ```

1. Save the file


We are now going to add the reverse associate so that the user model can get a list of associated Todo records. 

1. Open api\models\User.js

    ```bash
    User.js
    ```

1. To the attributes section, we need to add the following field that says it will be a collection of Todo records and associated through the User field that we created in the previous steps.

    ```javascript
    todoItems: {
      collection: 'todo',
      via: 'user'
    },
    ```

1. Save the file

<div class="exercise-end"></div>

### Create Todo

We are now going to setup the ability to create Todo items that gets the user for the res.session.user and sets completed to false by default.

<h4 class="exercise-start">
    <b>Exercise</b>: Setup Model Field Attributes
</h4>

1. Open api\models\Todo.js

    ```bash
    todo.js
    ```

1.  In the attributes we need to add the completed field.  We are going to make the field required, of type boolean, and defaults to false.

    ```javascript
    completed: {
        required: 'true',
        type: 'boolean',
        defaultsTo: 'false'
    },
    ```

While we are defining fields, lets go ahead and define our item field that will hold the text for our todo item.  The item field will be required and of type string.

In the attributes for the Todo model, add the item field below

```javascript
item: {
    required: 'true',
    type: 'string'
},
```

<div class="exercise-end"></div>

<h4 class="exercise-start">
    <b>Exercise</b>: Create Todo
</h4>

Now that we have our fields defined, we are ready to override the POST verb with out own logic.  We wil get the item text from the body of the request and the user from the req.session.user.  We do not need to include the completed field since it will automatically default to true.

1. Open api/controllers/TodoController.js

    ```bash
    TodoController.js
    ```

1. Add our create method stub to the module.exports

    ```javascript
    create: function createFn(req, res){

    }
    ```

1. Within the create function, we need to create a variable to hold the todo object we will be creating with the item and user fields populated.  The item field we will get from the request params collection and the user field from req.session.user.

    ```javascript
    var todo = {
        item: req.param('item'),
        user: req.session.user
    };
    ```

1. Next we need to call the Todo model's create function and pass in the todo object we just created.  Then we will run our standard callback error check and return statement

    ```javascript
    Todo.create(todo, function (err, results) {
        if (err) res.serverError(err);
        
        return res.ok(results);
    });
    ```

1. Save the TodoController.js file

#### Verify Todo Create

1. Restart sails for the changes to take effect
1. Open Postman
1. Make a call to the user login API
1. Set the REST verb to POST (*step 1*)
1. Set the URL to http://localhost:1337/todo (*step 2*)

    ```bash
    http://localhost:1337/todo
    ```

1. Click on the Body tab (*step 3*)
1. Select Raw (*step 4*)
1. Set the type to JSON (application/json) (*step 5*)
1. Set the body of the request to the following to pass in the email and password fields.  Change the email and password values as needed for the user that you created earlier with the sign up function (*step 6*)

    ```javascript
    {
        "item": "Coding with @digitaldrummerj and SailsJS"
    }
    ```

1.  Click the Send button (*step 7*)
1. If everything worked a new todo record was created and was returned back to you (*step 8*) with a 200 status code (*step 9*).

    ![create response](images/postman-todo-create.png)

1. You can verify that the create actually did work by running a GET against http://localhost:1337/todo

    ```bash
    http://localhost:1337/todo
    ```

<div class="exercise-end"></div>

### Get Todo List

Next we want to be able to get back the Todo records that belong to us.  Right now we can pull all of the Todo records even if we do not own them.  To fix this issue, we are going to override the find function to filter it by the user field. 

<div class="alert alert-danger" role="alert">**Note:** That once we override the find function it will no longer by default return the associated user records.  Instead if will just returned the user Id value.  To fix this you have to make a call to populate and tell it which association to populate.</div>

<h4 class="exercise-start">
    <b>Exercise</b>: Get Todo List
</h4> 

1. Open api\controllers\TodoController.js

    ```bash
    TodoController.js
    ```

1. Within the module.exports, add the following function.  This function is just like we did for the override of the find function in the UserController with the addition of the populate users call.

    ```javascript
    find: function findFn(req, res) {
        Todo.find(
            { 
                user: req.session.user 
            }
        )
        .populate('user')
        .exec(function (err, results) {
            if (err) return res.serverError(err);
            if (results.length === 0) return res.notFound();

            return res.ok(results);
        });
    },
    ```

1. Save the TodoController.js

#### Verify the Find Function

1. You must restart sails lift for the changes to take effect
1. Open Postman
1. Make sure to call the login API
1. Open another tab in Postman 
1. Set the verb to GET (*step 1*)
1. Set the url to http://localhost:1337/todo (*step 2*)

    ```bash
    http://localhost:1337/todo
    ```

1. Click the Send Button (*step 3*)
1. You should only see your user information returned (*step 4*) with a 200 status code (*step 5*)

![Postman Find Override](images/postman-todo-find.png)


<div class="exercise-end"></div>

### Get Single Todo 

Just like with the find all, we can also see any individual todo record regardless if we own it or not.  To fix this we need to override the findOne function.  

<div class="alert alert-danger" role="alert">**Note:** Just like the find function, the automatic population of associations goes away when we override the find function.  To fix this you have to make a call to populate and tell it which association to populate.</div>

<h4 class="exercise-start">
    <b>Exercise</b>: Find Single Todo Item
</h4>

1. Open api\controllers\TodoController.js

    ```bash
    TodoController.js
    ```

1. Add the following function to override the findOne function to make sure that we own the record, populate the user association, and then run our standard error trap/return statement.

    ```javascript
    findOne: function findOneFn(req, res) {
        Todo.findOne(
            { 
                user: req.session.user 
            }
        )
        .populate('user')
        .exec(function (err, result) {
            if (err) return res.serverError(err);
            if (!result) return res.notFound();

            return res.ok(result);
        });
    }
    ```

1. Save the file

#### Verify the Find One Function

1. You must restart sails lift for the changes to take effect
1. Open Postman
1. Make sure to call the login API
1. Open another tab in Postman 
1. Set the verb to GET (*step 1*)
1. Set the url to http://localhost:1337/todo/1 (*step 2*)

    ```bash
    http://localhost:1337/todo/1
    ```

    <div class="alert alert-info">The 1 at the end of the url is the Id (primary key) value of the Todo record that we want to pull.  If the Id field for the Todo record you created is not 1, then change the value in the url.</div>

1. Click the Send Button (*step 3*)
1. You should only see your user information returned (*step 4*) with a 200 status code (*step 5*)

![Postman Find One Override](images/postman-todo-findone.png)

<div class="exercise-end"></div>

### Update Todo

A Todo API wouldn't be complete without the ability to mark off the you completed an item.  However, we need to ensure that the user actually owns the record before we update it.  Up to this point, we have only used 1 parameter in our Waterline calls.  For the Update statement, we are going to use 2 parameters:  todo record id and the user value from req.session.user.

<h4 class="exercise-start">
    <b>Exercise</b>: Update Single Todo
</h4>

1. Open api\controllers\TodoController.js

    ```bash
    TodoController.js
    ```

1. Add the following function stub to the module.exports

    ```javascript
    update: function (req, res) {
    
    },
    ```

1. Within the update function, create a variable called record that will hold the values that we plan on updating.

    ```javascript
    var record = {
        id: req.param('id'),
        completed: req.param('completed'),
        user: req.session.user
    };
    ```

1. Then we need to make a call to the Waterline update function on the Todo object and then run our normal error check and return statements.   If we do not get a record back, then we will assume that we don't own the record and return a not found error.

    ```javascript
    Todo.update(
        {
            user: req.session.user,
            id: req.param('id')
        }, 
        record,
        function (err, results) {
            if (err) res.serverError(err);
            if (results.length === 0) return res.notFound();
            
            return res.ok(results);
        }
    );
    ```

<div class="alert alert-warning">**Note:** The update statement returns back an array of records even though we are updating just one record.  Hence the length check in the callback function to make sure we have records</div>

#### Verify Update Function

1. Restart sails for the changes to take effect
1. Open Postman
1. Make a call to the user login API
1. Set the REST verb to POST (*step 1*)
1. Set the URL to http://localhost:1337/todo/1 (*step 2*)

    ```bash
    http://localhost:1337/todo/1
    ```

1. Click on the Body tab (*step 3*)
1. Select Raw (*step 4*)
1. Set the type to JSON (application/json) (*step 5*)
1. Set the body of the request to the following to pass in the email and password fields.  Change the email and password values as needed for the user that you created earlier with the sign up function (*step 6*)

    ```javascript
    {
        "item": "Verify Coding with @digitaldrummerj and SailsJS",
        "completed": true
    }
    ```

1.  Click the Send button (*step 7*)
1. If everything worked a new todo record was created and was returned back to you (*step 8*) with a 200 status code (*step 9*).

    ![update response](images/postman-todo-update.png)

1. You can verify that the update actually did work by running a GET against http://localhost:1337/todo

    ```bash
    http://localhost:1337/todo
    ```

<div class="exercise-end"></div>

### Delete Todo

The last major thing we need to do it to override the delete function to make sure we can only delete record that we own.

<h4 class="exercise-start">
    <b>Exercise</b>: Delete Single Todo
</h4>

1. Open api\controllers\TodoController.js

    ```bash
    TodoController.js
    ```

1. The delete function is like the other function that we have been overriding.  We are going to take in the Id of the record to delete, make sure we own it and then run our standard error/response check.

    ```javascript
    delete: function (req, res) {
        Todo.delete(
            { 
                user: req.session.user, 
                id: req.param('id'),
            }, 
            function (err, results) {
                if (err) res.serverError(err);
                if (!results) return res.notFound();
                return res.ok(results);
            }
        );
    },
    ```

#### Verify Delete Function

1. Restart sails for the changes to take effect
1. Open Postman
1. Make a call to the user login API
1. Set the REST verb to POST (*step 1*)
1. Set the URL to http://localhost:1337/todo/1 (*step 2*)

    ```bash
    http://localhost:1337/todo/1
    ```

1.  Click the Send button (*step 3*)
1. If everything worked a new todo record was created and was returned back to you (*step 4*) with a 200 status code (*step 5*).

    ![delete response](images/postman-todo-delete.png)

1. You can verify that the delete actually did work by running a GET against http://localhost:1337/todo

    ```bash
    http://localhost:1337/todo
    ```



<div class="exercise-end"></div>
