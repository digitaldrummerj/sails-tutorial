## Routing

### Controlling the Api Route Url

In the Api project, you can also change how to route to the custom method by setting your own route in the config\routes.js file.  

<h4 class="exercise-start">
    <b>Exercise</b>: Create Todo
</h4>

1. Open the config/routes.js

    ```javascript
    'GET /user/identity': 'UserController.userIdentity'
    ```

1. For the route to take effect you will need to stop the `sails lift` with a ctrl+c and then re-run the `sails lift`

<div class="exercise-end"></div>
