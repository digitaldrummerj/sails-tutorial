## Sails Custom Functions

You already saw in a previous chapter that you could override the built-in REST api methods.  You can also create your own custom methods to provide additional functionality.  To demonstrate this we are going to add in the feature to return only the open todo list items.  

### Add Only Open Api Function

<h4 class="exercise-start">
    <b>Exercise</b>: Add Find Only Open Api Function
</h4>

In the api\TodoController.js file we need to add the following function.  The function will return results where the completed field is set to false.

```javascript
findOnlyOpen: function (req, res) {
    Todo.find({ completed: false })
        .then(function (findModelResults) {
            res.json(200, findModelResults);
        })
        .catch(function (findModelError) {
            res.json(500, findModelError);
        });
}
```
<div class="exercise-end"></div>



### Controlling the Api Route Url

In the Api project, you can also change how to route to the custom method by setting your own route in the config\routes.js file.  There is examples of doing this for the App Generator ExampleController. Right now the url is http://localhost:1337/v1/todos/findOnlyOpen .  To be more restful you might change it to something like http://localhost:1337/v1/todos/open.

1. Open the config/routes.js
1. To the routes object, add the following to tell Sails to make the findOnlyOpen function available as a GET at /v1/todos/open

    ```json
    'get /v1/todos/open': {
        controller: 'TodoController',
        action: 'findOnlyOpen'
    },
    ```

1. For the route to take effect you will need to stop the `sails lift` with a ctrl+c and then re-run the `sails lift`
1. You can then test it with Postman as a GET to url http://localhost:1337/v1/todos/open

    ![Postman get findOnlyOpen](images/postman-get-findOnlyOpen.png)    
    