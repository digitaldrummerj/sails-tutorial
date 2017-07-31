## Locking Down the Api

Update to this point you have had to remember to call the Login or Signup routes before you can see or update Todo items.  It would be much better if we directed users with errors messages that tell them that they need to perform an authentication call before trying to make a call.  

In order to do the authentication checks, Sails uses policies to lock down routes.  A policy is essential a function that returns a true/false value for whatever check you need to do.  

Policies are stored in the api\policies directory.  By creating a file in  the policies directory, they are automatically ready to be used to lock down a route.  

We will be creating 2 policies:  

1. Check if you are logged in  
1. Check if you are logged out

### Policy to Check if Logged In

<h4 class="exercise-start">
    <b>Exercise</b>: Create Is Logged In Policy
</h4>

1. In the api\policies folder, create a new JavaScript file called isLoggedIn.js

    ```bash
    isLoggedIn.js
    ```

1. The function format for Sails policies as follows.  It takes in a request, response, and next option.  

    ```javascript
    module.exports = function(req, res, next) {
        
    }
    ```

1. Within the policy function, we will use the req.session.user to determine if the user has logged in yet or not.  If the user has logged in, we need call next to go to the next policy in the chain.  If the user has not logged in, we will return the forbidden response.

    ```javascript
    if (req.session.user) {
        return next();
    }

    return res.forbidden('Please Login First.');
    ```

<div class="exercise-end"></div>

### Policy to Check if Logged Out

<h4 class="exercise-start">
    <b>Exercise</b>: Create Is Logged Out Policy
</h4>

1. In the api\policies folder, create a new JavaScript file called isLoggedOut.js

    ```bash
    isLoggedOut.js
    ```

1. Remember that the function format for Sails policies as follows.  It takes in a request, response, and next option.  

    ```javascript
    module.exports = function(req, res, next) {
        
    }
    ```

1. Within the policy function, we will use the req.session.user to determine if the user has logged in yet or not.  If the user has not logged in, we will call to go to the next policy in the chain. If the user has logged in, we will return the forbidden response.  

    ```javascript
    if (!req.session.user) {
        return next();
    }

    return res.forbidden('Please Logout First');
    ```

<div class="exercise-end"></div>

### Locking Down Routes

Now that we have both of our policies created, we need to configure which routes will use which policy.  

| Controller | Function | Policy | Description |
| ---- | ---- | ---- | ---- |
| UserController | login | true | Let everyone make a call to login |
| UserController | create | ['isLoggedOut'] | Make sure that you are logged out before trying to login again |
| UserController | * | ['isLoggedIn'] | All routes that aren't already configured will use this policy chain |
| TodoController | * | ['isLoggedIn'] | All routes in Todo controller will use this policy |

<h4 class="exercise-start">
    <b>Exercise</b>: Locking Down Routes
</h4>

1. Open config\policies.js

    ```bash
    policies.js
    ```

1. Within the module.exports.policies, add the following to the list of models that are locked down

    ```JavaScript
    UserController: {
        'login': true,
        'create': ['isLoggedOut'],
        '*': ['isLoggedIn']
    },
    TodoController: {
        '*': ['isLoggedIn']
    }
    ```

<div class="exercise-end"></div>

### Verify The Policies Are Working

We are now ready to test that our security is working.

<h4 class="exercise-start">
    <b>Exercise</b>: Testing policy
</h4>

1. Restart sails lift.  If you already have sails lift running, you need to ctrl+c to stop running it and restart it.  Policies do not automatically up themselves as you change the code until sails lift is restarted.

1. For the User routes, if you try to do any call besides a POST, you will get a message that you need to login first

    ![Not Logged In User Message](images/postman-policy-login.png) 

1. For the Todo routes, if you try to do any call you will get a message that you need to login first.  Even though we have not created any custom routes all of the standards REST verbs are already wired up

    ![Not Logged In User Message](images/postman-policy-login.png) 

1. Once you call the login route you will be able to do the following:

    * For User routes, you will be able to do call all of the routes except the create new user.  
    * For Todo routes, you will be able to call all routes

1. After you have logged in, if you try to create a user you will get an error message that you need to logout first. 

    ![Must Logout First Message](images/postman-policy-logout.png)
    
<div class="exercise-end"></div>
