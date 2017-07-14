## Locking Down the Api

Update to this point our Api has not had any user information stored in it.  This means that everyone that uses the Api will get the same information and not their own todo list.  Instead it would be better to associate a user to each todo list item and only allow you to interact with your own todo list items.  



### Locking down sails routes

Sails uses policies to lock down routes.  A policy is essential a bit of code that does you authorization checks.  In the App Generator sails template that we used there is a sample policy called sessionAuth.  However, since it is sample code it does not completely work out of the box.  We need to make a quick change to it and then we can tell our routes to use it.

<h4 class="exercise-start">
    <b>Exercise</b>: Locking Down Routes
</h4>

1. Open config\policies.js
1. Add the following to the list of models that are locked down

    ```JavaScript
    TodoController: {
        '*': ['sessionAuth']
    }
    ```

<div class="exercise-end"></div>


### Testing out policy

We are now ready to test that our security is working.

<h4 class="exercise-start">
    <b>Exercise</b>: Testing policy
</h4>

1. Start up the api.  If you already had the api running, you need to ctrl+c to stop running it and restart it.  Policies do not automatically up themselves as you change the code until the api is restarted.

    ```bash
    sails lift
    ```

Now we are ready to use the user information in our todo list data store.

<div class="exercise-end"></div>

### Using Login results

Up to this point we have mainly been using the built-in REST api functionality.  Now we are going to override the built-in REST api method and create our own.

To override the built-in get, post, put, and delete, you need to create methods in the controller.  Below is the mapping of REST verbs to function names.

* POST => create 
* GET => find
* GET/:id => findOne 
* PUT => update 
* DELETE => destory 

Now lets create our functions for all of the REST verbs so that all our of data is associated to a user.  

<h4 class="exercise-start">
    <b>Exercise</b>: Create Todo
</h4>

The first thing we are going to do is create a method that allows us to create a todo item and along with a user field.

Open the api\TodoController.js file.  Within the module.exports add the create function.


```javascript
 create: function (req, res) {
    var body = req.body;
    body.user  = req.session.user;

    Todo.create(body)
        .then(function (result) {
            res.json(200, result);
        })
        .catch(function (error) {
            sails.log.error('TodoController.create', error);
            res.json(500, error);
        });
},
```

Test with Postman.   This is the same POST call that we did earlier.  The only different is that the output now has a user field.

![Postman POST Results](images/postman-post-isso.png)

<div class="exercise-end"></div>

<h4 class="exercise-start">
    <b>Exercise</b>: Get Todo List
</h4>

Below the create function in the api\TodoController.js add a find function.  In the Todo.find call, we tell it to filter the list to only ones where the user  field matches your user.  

```javascript
find: function (req, res) {
    
    Todo.find({ user: req.session.user })
        .then(function (result) {
            res.json(200, result);
        })
        .catch(function (error) {
            sails.log.error('TodoController.find', error);
            res.json(500, error);
        });
},
```

Test with Postman.  This is the same GET call that we did earlier.  The only difference is that now it will only show todo items that are associated to your user.

![Postman Get ](images/postman-get-isso.png)

<div class="exercise-end"></div>

<h4 class="exercise-start">
    <b>Exercise</b>: Find Single Todo Item
</h4>

Even though we do not have the functionality within our UI to view a single todo item, we need to implement the functionality to make sure that we do not unintentionally leak data.

Below the find function in the api\TodoController.js add a findOne function.  In the Todo.findOne call, we tell it to return an ID as long as the user for the todo item is associated to the authenticated user.

```javascript
findOne: function (req, res) {
    var id = req.params.id;
    if (!id) {
        return res.json(400, 'id is a required parameter');
    }

    

    Todo.findOne({ id: id, user: req.session.user })
        .then(function (findOneResult) {
            return res.json(200, findOneResult);
        })
        .catch(function (error) {
            sails.log.error('TodoController.findOne', error);
            return res.json(500, error);
        });
},
```

Test with Postman.  The url requires that an id parameter at the end of it with the id field value from the todo item you want to return.  This call will return a single record if the id and your user matches.

![Postman Get One with](images/postman-get-one-isso.png)

<div class="exercise-end"></div>

<h4 class="exercise-start">
    <b>Exercise</b>: Update Single Todo
</h4>

Below the find function in the api\TodoController.js add a update function.   In the Todo.update call, we tell it to only update when the id and user match the record.

```javascript
update: function (req, res) {
    var id = req.params.id;
    if (!id) {
        return res.json(400, 'id is a required parameter');
    }

    var body = req.body;

    Todo.update({ id: id, user: req.session.user }, body)
        .then(function (updateResults) {
            return res.json(200, updateResults);
        })
        .catch(function (error) {
            sails.log.error('TodoController.update', error);
            return res.json(500, error);
        });
},
```

Test with Postman.  The url requires that an id parameter at the end of it with the id field value from the todo item you want to update.  This call will update the record if the id and your user matches.

![Postman PUT with](images/postman-put-isso.png)


<div class="exercise-end"></div>

<h4 class="exercise-start">
    <b>Exercise</b>: Delete Single Todo
</h4>

Below the update function in the api\TodoController.js add a delete function.  In the Todo.delete call, we tell it to only update when the id and user match the record.

```javascript
destroy: function (req, res) {
    var id = req.params.id;
    if (!id) {
        return res.json(400, 'id is a required parameter');
    }

    var body = req.body;

    Todo.destroy({ id: id, user: req.session.user })
        .then(function (deleteResults) {
            return res.json(200, deleteResults);
        })
        .catch(function (error) {
            sails.log.error('TodoController.destory', error);
            return res.json(500, error);
        });
},
```

Test with Postman.  The url requires that an id parameter at the end of it with the id field value from the todo item you want to delete.  This call will delete the record if the id and your user matches.

![Postman DELETE with](images/postman-delete-isso.png)

<div class="exercise-end"></div>


<h4 class="exercise-start">
    <b>Exercise</b>: Update the Find Only Open
</h4>

When we implemented the findOnlyOpen function earlier we did not yet have user authentication integrated so it is pulling all of the open todo items regardless of user.  We can to change this to also filter by user. 

```javascript
findOnlyOpen: function (req, res) {

    Todo.find({ user: req.session.user, completed: false })
        .then(function (findModelResults) {
        res.json(200, findModelResults);
        })
        .catch(function (findModelError) {
        res.json(500, findModelError);
        });
},
```

![Postman open](images/postman-get-findOnlyOpen-isso.png)

<div class="exercise-end"></div>
