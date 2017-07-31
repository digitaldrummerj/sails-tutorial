## Routing

As you create your own methods there are times where you want the route to be different from the function name in the controller.  

For example, in our UserController, we created a userIdentity function but having the route be /user/userIdentity is not very REST like.  Instead it would be better if could change the route to be /user/identity

All of the routes are configured in the config\routes.js file.  

### Change userIdentity Route

<h4 class="exercise-start">
    <b>Exercise</b>: Create User Identity Route
</h4>

1. Open the config/routes.js

    ```bash
    routes.js
    ```

1. Within the module.exports.routes, add the following route that will setup a GET call at /user/identity that maps to the UserController userIdentity function.

    ```javascript
    'GET /user/identity': 'UserController.userIdentity'
    ```


### Verify Route Works

1. For the route to take effect you will need to stop the `sails lift` with a ctrl+c and then re-run the `sails lift`
1. Open Postman
1. Make a call to the user login API
1. Set the verb to be GET (*step 1*)
1. Set the url to be http://localhost:1337/user/identity (*step 2*)

    ```bash
    http://localhost:1337/user/identity
    ```

1. Click the Send button (*step 3*)
1. You should now see the user information for the logged in user (*step 4*) and a status code of 200 (*step 5*)

![User Identity Route Output](images/postman-route-user-identity.png)

<div class="exercise-end"></div>
