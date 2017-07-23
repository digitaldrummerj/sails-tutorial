## Generating Api

Now that we have generated our project, before we can use it, we need to generate an Api.  Our Todo API will have two APIs:  User and Todo.  We will use the Sails CLI to generate the APIs.  

When you generate a new api it will create a model and controller for the API.  A model describes the data store fields with attributes such as type, required, unique, and defaultsTo.  The controller will contain our functions that are related to the model.  

Out of the box, Sails wires up all of the REST verbs (GET, POST, PUT, DELETE) for us and saves the data to a schema-less JSON file as the data store.  This means that immediately after running the generate command we can use the API without having to write any code. 

### Generating User API

<h4 class="exercise-start">
  <b>Exercise</b>: Generating User API
</h4>

1. Visual Studio Code has a built-in terminal that we can use to run our commands.  Open up the integrated terminal in Visual Studio Code  by using ctrl+` or view menu -> Integrated Terminal
1. Run the following command to create the user API

    ```bash
    sails generate api user
    ```

1. This will create 2 files for us:

    * api\controllers\UserController.js
    * api\models\User.js

<div class="exercise-end"></div>

### Generating Todo Api

<h4 class="exercise-start">
  <b>Exercise</b>: Generating Todo API
</h4>

1. In the Visual Studio Code integrated terminal (ctrl+` or view menu -> Integrated Terminal), run the  following command to create the user API

    ```bash
    sails generate api todo
    ```

1. This will create 2 files for us:

    * api\controllers\TodoController.js
    * api\models\Todo.js

<div class="exercise-end"></div>

### Running Api

<h4 class="exercise-start">
    <b>Exercise</b>: Starting Api
</h4>

1. In the Visual Studio Code integrated  terminal run the following command to start our API

    ```bash
    sails lift
    ```

    ![sails lift output](images/db-migrate-question.png)

    <div class="alert alert-danger" role="alert">You will notice that you are prompted with a big long question about database migration strategy to use.  Sails does not make any assumptions on how we want it to deal with database migrations as the models are updated.  Out of the box Sails uses a schema-less JSON file as the data store.  This is what allows us to immediately started working with our API.  Until we set the database migration strategy to use, we will be prompted to select one when we run sails lift.</div>

1. When prompted for the migration strategy, type 2 and press enter

    ![sails lift started](images/db-migrate-answer.png)    

    <div class="alert alert-info" role="alert">You will see that a message shows up that says "Temporarily using sails.config.models.migrate="alert"..."</div>

1. The API is now started up and we are ready to do some testing with Postman

<div class="exercise-end"></div>

<h4 class="exercise-start">
    <b>Exercise</b>: Get Data
</h4>


1. Open up Postman
1. Set the VERB to GET (*step 1*)
1. Set the url to http://localhost:1337/user (*step 2*)

    ```bash
    http://localhost:1337/user
    ```

1. Click the Send button (*step 3*)
1. Right now you should have gotten an empty array as we have not added any data yet (*step 4*)
1. You should have also gotten a status code of 200 (*step 5*)

![Postman get](images/postman-user-get-blank.png)

<div class="exercise-end"></div>

<h4 class="exercise-start">
    <b>Exercise</b>: Insert Data
</h4>

We can insert data using the REST verb POST.

1. In Postman, change the VERB to POST (*step 1*)
1. Leave the url set to http://localhost:1337/user (*step 2*)

    ```bash
    http://localhost:1337/user
    ```

1. Click on the Body tab (*step 3*)
1. Click raw radio button (*step 4*)
1. Change the type dropdown from "Text" to "application/json" (*step 5*)
1. Set the request body to the following JSON (*step 6*)

    ```json
    {
	    "email": "foo@foo.com",
	    "password": "123456"
    }
    ```

1. Click the Send button (*step 7*)
1. You will get an output similar to the following. (*step 8 *)

    ```json
    {
        "email": "foo@foo.com",
        "password": "123456",
        "createdAt": "2017-07-23T22:09:29.601Z",
        "updatedAt": "2017-07-23T22:09:29.601Z",
        "id": 1
    }
    ```

1. You should also have a status code of 201 (*step 9*)

![Postman POST](images/postman-user-post.png)

<div class="exercise-end"></div>

<h4 class="exercise-start">
    <b>Exercise</b>: Update Data
</h4>

We can update data using the REST verb PUT.

1. In Postman VERB to PUT (*step 1*)
1. Change the url to http://localhost:1337/user/1 (*step 2*)
1. Click on the Body tab (*step 3*)
1. Click raw radio button (*step 4*)
1. Change the type dropdown from "Text" to "application/json" (*step 5*)
1. Set the request body to the following JSON (*step 6*)

    ```json
    {
        "email": "foo1@foo.com",
        "password": "1234567"
    }
    ```

1. Click the Send button (*step 7*)
1. You will get an output similar to the following. (*step 8*)

    ```json
    {
        "email": "foo1@foo.com",
        "password": "1234567",
        "createdAt": "2017-07-23T22:09:29.601Z",
        "updatedAt": "2017-07-23T22:09:29.601Z",
        "id": 1
    }
    ```

1. You should also have a status code of 201 (*step 9*)


![Postman PUT](images/postman-user-put.png)

<div class="alert alert-info" role="alert">Right now update returns the record that we just deleted.  In the next Sails release it will not return any data.</div>

<div class="exercise-end"></div>

<h4 class="exercise-start">
    <b>Exercise</b>: Delete Data
</h4>

We can update data using the REST verb DELETE.

1. In Postman VERB to DELETE (*step 1*)
1. Change the url to http://localhost:1337/user/1 (*step 2*)
1. Click the Send button (*step 3*)
1. You will get an output similar to the following.  (*step 4*)

    ```json
    {
        "email": "foo1@foo.com",
        "password": "1234567",
        "createdAt": "2017-07-23T22:09:29.601Z",
        "updatedAt": "2017-07-23T22:09:29.601Z",
        "id": 1
    }
    ```

1. You should also have a status code of 200 (*step 5*)

![Postman PUT](images/postman-user-delete.png)

<div class="alert alert-info" role="alert">Right now delete returns the record that we just deleted.  In the next Sails release it will not return any data.</div>

<div class="exercise-end"></div>

