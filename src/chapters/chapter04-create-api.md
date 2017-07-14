## Add Todo Api

Now that we have our api project created, we need to add the actual todo api.  Sails comes with a generate command to create new apis.  When you create a new api it will create a model and controller.  A model describes the fields that are part of the api and any attributes such as type, required, etc.  The controller hold custom logic for your api.  

By default, Sails comes with a built-in REST api and uses an in memory json data store.  This means that we can immediately start using the Api without having to write any code. 

<h4 class="exercise-start">
    <b>Exercise</b>: Add  Todo Api
</h4>

1. In the open command prompt navigate into the sails-tutorial that we created when you generate the api project
1. Run the following command to generate the a new api called todo  

        sails generate api todo

<div class="exercise-end"></div>

<h4 class="exercise-start">
    <b>Exercise</b>: Get Data
</h4>

1. Open up Postman
1. Set the VERB to GET and the url to 

    ![Postman get](images/postman-get.png)

<div class="exercise-end"></div>

<h4 class="exercise-start">
    <b>Exercise</b>: Insert Data
</h4>

1. We can test POST (insert) by changing the Postman VERB to POST, setting the body to type Raw as application/json and making the body text the following JSON 

    ```json
    {
	    "todo": "get something done",
	    "completed": false
    }
    ```

    ![Postman POST](images/postman-post.png)

1. You will get an output similar to the following.  You will also get the same output if you re-run the GET command from above.

    ```json
    {
        "todo": "get something done",
        "completed": false,
        "createdAt": "2017-02-20T21:29:44.501Z",
        "updatedAt": "2017-02-20T21:29:44.501Z",
        "id": 1
    }    
    ```


<div class="exercise-end"></div>

<h4 class="exercise-start">
    <b>Exercise</b>: Update Data
</h4>

1. We can test PUT (update) by changing the Postman VERB to PUT, change the url to http://localhost:1337/v1/todos/1, setting the body to type Raw as application/json and making the body text the following JSON 

     ```json
    {
        "todo": "updated to done",
        "completed": true,
        "id": 1
    }    
    ```

    ![Postman PUT](images/postman-put.png)

1. You will get an output similar to the following.  You will also get the same output if you re-run the GET command from above.

    ```json
   {
        "todo": "updated to done",
        "completed": true,
        "createdAt": "2017-02-20T21:29:44.501Z",
        "updatedAt": "2017-02-20T21:35:42.743Z",
        "id": 1
    }
    ```

<div class="exercise-end"></div>

<h4 class="exercise-start">
    <b>Exercise</b>: Delete Data
</h4>


1. We can test Delete by changing the Postman VERB to DELETE, change the url to http://localhost:1337/v1/todos/1 .  For the delete command you do not need a body json to accompany it.

    ![Postman DELETE](images/postman-delete.png)

1. You will get an output similar to the following.  You will also get the same output if you re-run the GET command from above.

    ```json
   {
        "todo": "updated to done",
        "completed": true,
        "createdAt": "2017-02-20T21:29:44.501Z",
        "updatedAt": "2017-02-20T21:35:42.743Z",
        "id": 1
    }
    ```


<div class="exercise-end"></div>

