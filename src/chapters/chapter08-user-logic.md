## User API Logic

In this chapter we are going to add logic to allow a user to login, signup, logout, perform CRUD (**C**reate, **R**ead, **U**pdate, **D**elete) operations on the data that they own.


### Sign up


The first thing that we are going to do is override the POST verb with our own create function that will allow a user to signup for our Todo API.  Within the sign up function we will validate the email address is an actual email address, then encrypt the password before creating a user record in the data store and finally return back the newly created user.

<h4 class="exercise-start">
    <b>Exercise</b>: Create Sign up Stub
</h4>

For the sign up since we will be overriding the POST verb, we will be creating a function called create.

1. Open the api\controllers\UserController.js file

    ```bash
    usercontroller.js
    ```

    <div class="alert alert-info" role="alert">In Visual Studio Code, use Ctrl+P to bring up the find file dialog, paste in the file name, and either click on the file or arrow down to it and press enter</div>

1. Within the module.exports add the following function stub to override the POST verb

    ```javascript
    create: function createFn(req, res){

    }
    ```

    <div class="alert alert-info" role="alert">The function takes in 2 parameters:  req is the request and res is the response.</div>

1. By creating the above method, Sails will automatically use this method for the POST verb without us having to do any route configurations.  It is using convention over configuration.

<div class="exercise-end"></div>


<h4 class="exercise-start">
    <b>Exercise</b>: Validate Email Address
</h4>

The first thing we want to do when a user signs up is to validate that their email address is an actual email address.  It is good practice to re-validate the data that is coming into the API even if the UI is also doing validation.  To validate the email address, we are going to use [Node Machines](http://node-machine.org/). Each Node Machine has a single and clear purpose.

The only downside to using the machine packs is that you will have to use callbacks instead of promises within the Sails functions that we create that uses a machine pack.

Before we can use a Node Machine Pack we need to install the pack we want to use.  They are installed using NPM.    For email addresses, we are going to use the machinepack-emailaddresses.  

1. Open the Visual Studio Code Integrated Terminal

    <div class="alert alert-info" role="alert">ctrl+` to show the integrated terminal</div>

1. Run the following command to install the machinepack-emailaddresses that will be used to validate the email address

    ```bash
    npm install --save machinepack-emailaddresses
    ```

1. You can do a ctrl+` to toggle off the integrated terminal

1. In the api\controllers\UserController.js file at the top outside of the module.exports, we need to import the machinepack-emailaddresses

    ```javascript
    var EmailAddresses = require('machinepack-emailaddresses');
    ```

Within the create function we are ready to start adding in our logic.    

1. Create a variable that gets the email address from the req parameters (body)

    ```javascript
    var email = req.param('email');
    ```

1.  Call the EmailAddress.validate function to validate the email address and then check the results.  The Validate function has 3 possible returns:  error, invalid, and success. If there is an error we will return a server error (500 status code).  If the email is not valid then we will return a bad request (400 status code).  If the email is valid, then we will move on to encrypting the password. 

    ```javascript
    // validate the email address that is passed in
    EmailAddresses.validate({
      string: email,
    }).exec({
        // called if there is a general error
        error: function(err){
            return res.serverError(err);
        },

        // called if email is invalid
        invalid: function(){
            return res.badRequest('Does not look like an email address to me!');
        },

        // called if the email validation passed
        success: function(){
        }
    });
    ```

Within the success callback for the email validate we are ready to encrypt the password.  We will be using the machinepack-passwords to do the encryption and validating that the encrypted password and unencrypted passwords match.

1. Open the Visual Studio Code Integrated Terminal

    <div class="alert alert-info" role="alert">ctrl+` to show the integrated terminal</div>

1. Install the machinepack-passwords

    ```bash
    npm install --save machinepack-passwords
    ```

1. At the top of the UserController, we need to add a require statement for the machinepack-passwords

    ```javascript
    var Passwords = require('machinepack-passwords');
    ```

1. Within the success callback for the email validate we need to call the Passwords.encryptPassword function and pass it the password from the request parameters (body).  The Passwords.encryptPassword function returns back 2 responses:  error or success.  If there is an error, we will return a server error (500 status code).  If it is successful, we will create the user in our data store.

    ```javascript
    // encrypt the password
    // get password from the body of the request with the req.param call
    Passwords.encryptPassword({
        password: req.param('password'),
    }).exec({
        // if there is an error return a server error 500 status code
        error: function(err){
            return res.serverError(err);
        },

        // if success then move on to the next step
        success: function(result){
        }
    })
    ```

Now we are ready to create our user in the data store and add the user to the request session.  Within the success callback add the following code to create a user with email and encryptedPassword fields.  We will get the email from the local variable that we created earlier and the encryptedPassword will come from the results of the Password.encryptPassword call.  To add the record to the data store, we are using the Waterline ORM and calling User.create.  Waterline allows us to write data store agnostic code.

1.  Within the success callback add the following code to create a local user variable

    ```javascript
    // create user with email and encryptedPassword to add to the database
    var user = {
        email: email,
        encryptedPassword: result
    };
    ```

1. After the user variable declaration, call the Waterline ORM create function to create a new record based on the user variable that we created in the previous step 

    ```javascript
    // User waterline to create a new user by calling .create and passing in the local user variable
    User.create(user, function (err, createdResult) {
        // check for errors
        if (err) return res.serverError(err);
        
        // add user id to session state
        req.session.user = createdResult.id;

        // return back created user with a status code of 200
        // see api\responses\ok.js for what the ok response is actually doing
        return res.ok(createdResult);
    });
    ```

    * **Note:** When we set the req.session.user was are only setting the id. This is so that the encryptedPassword hash does not end up in the in-memory session store.

#### Verify Sign up API

1. In order to test the create function, we have to restart the sails lift.  Stop sails lift using ctrl+c and run sails lift 
1. Now if you run the POST call against the User API, you will see if returns back a user with an encryptedPassword (*step 8*)

    ![Postman user custom create](images/postman-user-custom-create.png)

<div class="exercise-end"></div>

### Remove Password from JSON

We now have users saved with an encrypted password but we are passing the encrypted hash as part of the returned user.  This is not a good security practice.  There is no reason to pass the encrypted password back from the API.  Luckily it is really easy to global remove this field anytime the user is serialized to JSON.

<h4 class="exercise-start">
    <b>Exercise</b>: Stripping the encryptedPassword from JSON
</h4>

1. Open api\models\User.js

    ```bash
    user.js
    ```

1. Within the attributes we are going to create a function called toJSON

    ```javascript
    toJSON: function (){

    }
    ```

1. Within the toJSON function we need to get a reference to the current record by calling this.toObject() and setting the output a variable

    ```javascript
    var obj = this.toObject();    
    ```

1. Now that we have a reference to the current record (object), we can call delete to remove the encryptedPassword field from the JSON results

    ```javascript
    delete obj.encryptedPassword;
    ```

1. The last thing left to do is return back the object

    ```javascript
    return obj;
    ```

#### Verify toJSON Works

1. You must restart sails lift for the changes to take effect
1. Now if you run the GET call for the user, you will see that the encryptedPassword field is no longer returned back from the API

    ![User Get without EncryptedPassword](images/postman-user-without-encryptedPassword.png)

<div class="exercise-end"></div>

### Make sure Email is unique

We want to make sure that each user is unique and has not signed up already.  To enable this constraint we need to define the email field and add the unique attribute to the field.

Fields and attributes are defined within the model file in the attributes section.  Some of the common attributes are: required, type, unique, and defaultsTo.

<h4 class="exercise-start">
    <b>Exercise</b>: Defining Email Field
</h4>

1. Within the already open api\models\user.js add the following attribute to the attributes section.  This will tell Sails that the email field is required, of type string, and needs to be unique.  For the unique attribute, it is actually run within the data store and not within the API. 

    ```javascript
    email: {
        required: 'true',
        type: 'string',
        unique: true,
    },
    ```

#### Verify Unique Attribute

1. You must restart sails lift for the changes to take effect
1. If you now try to do a POST again for the user with the same email you will get an error that unique constraint was violated.  

    ![Unique Error](images/email-unique-error.png)


<div class="exercise-end"></div>

<h4 class="exercise-start">
    <b>Exercise</b>: Create a Custom Response
</h4>

The default unique error is really ugly.  Instead we can check for the unique error and return our own response that contains an easier message to parse.  

1. We need to replace the current error check in the User.create callback with the following code

    ```javascript
    if (err) {
        if (err.invalidAttributes
            && err.invalidAttributes.email
            && err.invalidAttributes.email[0]
            && err.invalidAttributes.email[0].rule === 'unique') {
            return res.alreadyInUse(err);
        }

        return res.serverError(err);
    }
    ```

In our error trapping we are not returning res.alreadyInUse when the unique constraint is fired.  We need to create this response before our code will work.

1. On the api\responses folder, right-click on the folder, pick New File and name the file alreadyInUse.js

    ```bash
    alreadyInUse.js
    ```

1. The alreadyInUse response will return a response with a 409 status code and message of "Email address is already taken by another user, please try again."   

    ```javascript
    module.exports = function alreadyInUse(err){
        // Get access to `res`
        // (since the arguments are up to us)
        var res = this.res;

        if (err.invalidAttributes.email) {
            return res.send(409, 'Email address is already taken by another user, please try again.');
        }
    };
    ```

1. By create the file in the response folder, the response is automatically wired up and available to use.

#### Verify Custom Response

1. You must restart sails lift for the changes to take effect
1. If you now try to do a POST again for the user with the same email you will get an error that looks much nicer than before and has a status code of 409.  You could also change the response message to be a standard format that the whole API will use instead of just plain text if you wanted to

    ![already in use response](images/already-in-use.png)


<div class="exercise-end"></div>

<h4 class="exercise-start">
    <b>Exercise</b>: Encrypted Password Field Definition
</h4>

In the api\model\User.js file we are also going to define the encryptedPassword as well so that we have it later when we change over to a schema based data store like Postgres or MySql.  For the encryptedPassword field we are going to set it to be required and of type string. 

1. Open the api\models\User.js

    ```bash
    user.js
    ```

1. To the attributes add the encryptedPassword field

    ```javascript
    encryptedPassword: {
        required: 'true',
        type: 'string',
    },
    ```

#### Verify Encryped Password Field Attributes

1. You must restart sails lift for the changes to take effect
1. Now you have to input a password when calling the sign up API and it must be a string.

<div class="exercise-end"></div>

### Login

You can't have a sign up without having a login to go with it.  The login function will pull the user from the data based on the email address, check if a record was found and if it was then verify that the password matched the encrypted password in the database.

<h4 class="exercise-start">
    <b>Exercise</b>: Create the Login Function Stub
</h4>

1. Open the api\controllers\UserController.js

    ```bash
    UserController.js
    ```

1. We are going to add a function called login with the standard Sails controller function definition

    ```javascript
    login: function loginFn(req, res) {

    },
    ```

1. The first thing we are going to do is attempt to pull the user out of the database based on the email address.  We will be using the Waterline findOne function to ensure that we only pull back the 1 record.  We will get the email from req.param 

    ```javascript
    User.findOne(
        { 
            email: req.param('email') 
        }, 
        function (err, result) {

        }
    )
    ```

1. In the callback function, we need to add our standard error check that will return back a server error if an error did occur

    ```javascript
    if (err) return res.serverError(err);
    ```

1. The next thing we want to check for is if the user was found or not.  If the user was not found then we want to return a 404 not found error

    ```javascript
    if (!result) return res.notFound();
    ```

1. If we found the user then we want to use the Node Machine password pack that we used earlier to validate that the unencrypted and encrypted passwords match.  The response from the machine pack will be either error, incorrect or success.  If there was an error we will return a server error.  If the password was incorrect we will return a forbidden error.  If it was success then we will add the session user and return the user.   

    ```javascript
    Passwords.checkPassword({
        passwordAttempt: req.param('password'),
        encryptedPassword: result.encryptedPassword
    }).exec({
        error: function (err) {
            return res.serverError(err);
        },

        incorrect: function () {
            return res.forbidden('Invalid Login, Please Try Again!');
        },

        success: function () {
            req.session.user = result.id;

            return res.ok(result);
        }
    })
    ```

    * **Note:** When we set the req.session.user was are only setting the id. This is so that the encryptedPassword hash does not end up in the in-memory session store.


#### Verify Login API

1. You must restart sails lift for the changes to take effect
1. Open Postman
1. Set the REST verb to POST (*step 1*)
1. Set the URL to http://localhost:1337/user/login (*step 2*)

    ```bash
    http://localhost:1337/user/login
    ```

1. Click on the Body tab (*step 3*)
1. Select Raw (*step 4*)
1. Set the type to JSON (application/json) (*step 5*)
1. Set the body of the request to the following to pass in the email and password fields.  Change the email and password values as needed for the user that you created earlier with the sign up function (*step 6*)

    ```javascript
    {
        "email": "foo@foo.com",
        "password": "123456"
    }
    ```

1.  Click the Send button (*step 7*)
1. If everything worked you should now be logged and your user information was returned back to you (*step 8*) with a 200 status code (*step 9*).

![already in use response](images/postman-user-login.png)

<div class="exercise-end"></div>

### Logout

Now that we can either sign up or login to the API, we need to be able to log out of the API.  Logging out of the API in our case is really simple.  We are going to clear the req user session and return back a response ok (200 status code)

<h4 class="exercise-start">
    <b>Exercise</b>: Implement Logout Function
</h4>

1. Open the api\controllers\UserController.js
1. Add the logout function below to the existing functions

    ```javascript
    logout: function logoutFn(req, res) {
        req.session.user = null;

        return res.ok();
    },
    ```

#### Verify Logout API

1. You must restart sails lift for the changes to take effect
1. Open Postman
1. Set the verb to GET (*step 1*)
1. Set the url to http://localhost:1337/user/logout (*step 2*)

    ```bash
    http://localhost:1337/user/logout
    ```

1. Click the send button (*step 4*)
1. You should get an empty response with a status code of 200 (*step 4*)

![Postman logout](images/postman-user-logout.png)

<div class="exercise-end"></div>

### Who is Logged In?

<h4 class="exercise-start">
    <b>Exercise</b>: Get Logged in User Info
</h4>

1. Open the api\controllers\UserController.js 

    ```bash
    UserController.js
    ```

1. We are going to add a function called userIdentity

    ```javascript
    userIdentity: function (req, res) {
    },
    ```

1. Within the userIdentity function we are going to use the Waterline ORM find a single record in the data store by running findOne

    * We will query by id againt the req.session.user
    * We will do the standard error trapping looking for errors and missing records.
    * If user was found then return an ok response

    ```javascript
    User.findOne(
        { id: req.session.user }, 
        function (err, result) {
            if (err) return res.serverError(err);
            if (!result) return res.notFound();

            return res.ok(result);      
        }
    );
    ```


#### Verify User Identity API

We can test the userIdentity API by making a GET call to http://localhost:1337/user/userIdentity after we have called the login API

1. You must restart sails lift for the changes to take effect
1. Open Postman
1. Make a call to the login API like we did before so that we have a logged in user 
1. Open another tab in Postman 
1. Set the verb to GET (*step 1*)
1. Set the url to http://localhost:1337/user/userIdentity (*step 2*)

    ```bash
    http://localhost:1337/user/userIdentity
    ```

1. Click the Send Button (*step 3*)
1. You should see your user information returned (*step 4*) with a 200 status code (*step 5*)

![Postman User Identity](images/postman-user-identity.png)

<div class="exercise-end"></div>

### Get User Info

Right now if you sign up a 2nd user and then do a GET on the user API,  you will be able to see both records.  This is not great from a privacy standpoint.  You should only be able to see your user information.  

![Postman Show All Users](images/postman-user-show-all.png)

In order to restrict the data, we are going to override the find (get all) and findOne (get 1 record by id) functions to only return back the information for the logged in user.

Even though we will only ever be returning 1 record back, we want to use the find function which returns an array instead of the findOne that returns a single record.  This way we keep the expected return type of a GET all REST call which is an array of records.

<h4 class="exercise-start">
    <b>Exercise</b>: Override Find Function
</h4>

1. Open api/controllers/UserController.js

    ```bash
    usercontroller.js
    ```

1. Add the following function stub to override the find function

    ```javascript
    find: function findFn(req, res) {

    },
    ```

1. Within the find function, we want to use Waterline to call the find function on the User object, filter by the user we have stored in session, and then have our standard callback to process the results

    ```javascript
    User.find(
        { id: req.session.user }, 
        function (err, results) {

        }
    );

    ```

1. Within the callback the first thing we want to do is check for errors

    ```javascript
    if (err) return res.serverError(err);
    ```

1. Then we want to check if the user was found or not.  Since the find method returns an array we can look at the length to determine if the record was returned.   If the record is not found, we will return a response notFound.

    ```javascript
    if (results.length === 0) return res.notFound();
    ```    

1.  The final thing we are going to do is return the user record that was found with a 200 status code.  

    ```javascript
    return res.ok(results);
    ```

#### Verify Find Override

1. You must restart sails lift for the changes to take effect
1. Open Postman
1.  If you have not created a 2nd user, make a call the sign up API and create a 2nd user
1. If you have already created the 2nd user, then call the login API to login    
1. Open another tab in Postman 
1. Set the verb to GET (*step 1*)
1. Set the url to http://localhost:1337/user (*step 2*)

    ```bash
    http://localhost:1337/user
    ```

1. Click the Send Button (*step 3*)
1. You should only see your user information returned (*step 4*) with a 200 status code (*step 5*)

![Postman Find Override](images/postman-user-find.png)


<div class="exercise-end"></div>

<h4 class="exercise-start">
    <b>Exercise</b>: Find by Id Override
</h4>

To override the find by get (http://localhost:1337/user/2) we need to override the findOne function.  The findOne is similar to the find that we just created except that we will only be returning a single record back instead of an array with 1 record.

In the api/user/usercontroller.js add the following function that will call the Waterline findOne function on the User object, filtering by the id stored in request session and then running our standard error checks before return back the user record.

```javascript
findOne: function findOneFn(req, res) {
    User.findOne(
        { id: req.session.user }, 
        function (err, result) {
            if (err) return res.serverError(err);
            if (!result) return res.notFound();

            return res.ok(result);
        }
    );
},
```

#### Verify Find by Id Override

1. You must restart sails lift for the changes to take effect
1. Open Postman
1. Call the login API to login    
1. Open another tab in Postman 
1. Set the verb to GET (*step 1*)
1. Set the url to http://localhost:1337/user/2 (*step 2*)

    ```bash
    http://localhost:1337/user/2
    ```

    * The 2 at the end of the url is the Id field value for the user record.
    
    <div class="alert alert-info">**Note** You can technically use any value as long as it is not a route name since we are getting the Id from the req.session.user and not from the querystring.</div>

1. Click the Send Button (*step 3*)
1. You should only see your user information returned (*step 4*) with a 200 status code (*step 5*)

![Postman Find Override](images/postman-user-findOne.png)

<div class="exercise-end"></div>

### Update User

The next thing we are going to do is override the update function so that we can only update our own user.

We will allow the user to change their email and password.  We need to make sure to valid that email address and encrypt the password.

<h4 class="exercise-start">
    <b>Exercise</b>: Create Update Function
</h4>

1. Open the api/models/UserController.js

    ```bash
    UserController.js
    ```

1. Create the update function stub

    ```javascript
    update: function updateFn(req, res){

    },
    ```

1. Within the update function we want to use the Node Machine email pack to validate the email address like we did in the create function

    ```javascript
    EmailAddresses.validate({
      string: req.param('email'),
    }).exec({
      error: function (err) {
        return res.serverError(err);
      },

      invalid: function () {
        return res.badRequest('Does not look like an email address to me!');
      },

      success: function () {

      },
    });
    ```

1. If the email address is validate we want to encrypt the password like we did before with the Node Machine pack passwords

    ```javascript
    Passwords.encryptPassword({
        password: req.param('password'),
    }).exec({
        error: function(err){
        return res.serverError(err);
        },
    
        success: function(result){
        
        },
    });
```

1. Lastly, in the success of the encrypt response, we want to update the user record in the data store and return back the results.

    ```javascript
    var user = {
        email: req.param('email'),
        encryptedPassword: result,
    }
    User.update(
        { id: req.session.user },
        user,
        function (err, result) {
        if (err) return res.serverError(err);
        if (results.length === 0) return res.notFound();

        return res.ok(result);
        }
    );
    ```

    <div class="alert alert-info" role="alert">**Note:** The Waterline update call returns an array of records.</div>

#### Verify Update Override

1. You must restart sails lift for the changes to take effect
1. Open Postman
1. Call the login API to login    
1. Open another tab in Postman 
1. Set the verb to PUT (*step 1*)

    <div class="alert alert-info" role="alert">PUT is the REST verb for doing an update</div>

1. Set the url to http://localhost:1337/user/2 (*step 2*)

    ```bash
    http://localhost:1337/user/2
    ```

    * The 2 at the end of the url is the Id field value for the user record.
    
    <div class="alert alert-info">**Note** You can technically use any value as long as it is not a route name since we are getting the Id from the req.session.user and not from the querystring.</div>

1. Click on the Body tab (*step 3*)
1. Select Raw (*step 4*)
1. Set the type to JSON (application/json) (*step 5*)
1. Set the body of the request to the following to pass in the email and password fields.  Change the email and password values (*step 6*)

    ```javascript
    {
        "email": "foo2@foo.com",
        "password": "1234567"
    }
    ```

1.  Click the Send button (*step 7*)
1. If everything worked you should now be logged and your user information was returned back to you (*step 8*) with a 200 status code (*step 9*).

![Postman Find Override](images/postman-user-update.png)

<div class="alert alert-danger" role="alert">If you try to login in with the original email, you should get a 404 not found error</div>
<div class="alert alert-danger" role="alert">If you try to login with the original password, you should get a 403 Forbidden status with an "Invalid Login, Please Try Again!" message
</div>

<div class="exercise-end"></div>

### Delete User

The last thing that we are going to do is to override the delete function to ensure that we can only delete our own user.

The user must be logged in before they can be deleted.  

<h4 class="exercise-start">
    <b>Exercise</b>: 
</h4>

We will call the Waterline delete function on the User object and make sure to filter it by the user id that is stored in req.session.user.  We will then do our standard error traping and return back the result.  We will also clear out the req.session.user since we are deleting the user and we want to make sure that they are actually logged out of the API.

Even though we are deleting the user we will return back the record that we just deleted.  This way we keep with the way the Sails does it out of the box.

1. Open the api/controllers/UserController.js

    ```bash
    UserController.js
    ```

1. Add the following function for the delete call

    ```javascript
    delete: function deleteFn(req, res) {
        User.delete(
            {
            id: req.session.user
            }
        ), function (err, result) {
            if (err) return res.serverError(err);
            if (!result) return res.notFound();

            req.session.user = null;
            return res.ok(result);
        }
    }
    ```

#### Verify Delete Override

1. You must restart sails lift for the changes to take effect
1. Open Postman
1. Call the login API to login    
1. Open another tab in Postman 
1. Set the verb to DELETE (*step 1*)

    <div class="alert alert-info" role="alert">PUT is the REST verb for doing an update</div>

1. Set the url to http://localhost:1337/user/2 (*step 2*)

    ```bash
    http://localhost:1337/user/2
    ```

    * The 2 at the end of the url is the Id field value for the user record.
    
    <div class="alert alert-info">**Note** You can technically use any value as long as it is not a route name since we are getting the Id from the req.session.user and not from the querystring.</div>

1.  Click the Send button (*step 3*)
1. If everything worked you should now be logged and your user information was returned back to you (*step 4*) with a 200 status code (*step 5*).

![Postman Find Override](images/postman-user-delete.png)

<div class="exercise-end"></div>

We have now completed the initial functionality for our User API.  Next we will configure our security policies so that we do not have to remember to login before running any of the function.
