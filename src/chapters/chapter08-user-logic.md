## User API Logic

In this chapter we are going to add logic to allow a user to login, signup, logout, perform CRUD (**C**reate, **R**ead, **U**pdate, **D**elete) operations on the data that they own.


### Signup


The first thing that we are going to do is override the POST verb with our own create function that will allow a user to signup for our Todo Api.  Within the sign up function we will validate the email address is an actual email address, then encrypt the password before creating a user record in the data store and finally return back the newly created user.

<h4 class="exercise-start">
    <b>Exercise</b>: Create Stub
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

1. In order to test the create function, we have to restart the sails lift.  Stop sails lift using ctrl+c and run sails lift 
1. Now if you run the POST call against the User API, you will see if returns back a user with an encryptedPassword (*step 8*)

    ![Postman user custom create](images/postman-user-custom-create.png)

If you run into issues, here is the full code for the create function

```javascript
var EmailAddresses = require('machinepack-emailaddresses');
var Passwords = require('machinepack-passwords');

module.exports = {
create: function createFn(req, res) {
    var email = req.param('email');

    // validate the email address that is passed in
    EmailAddresses.validate({
        string: email,
    }).exec({
    // called if there is a general error
    error: function (err) {
        return res.serverError(err);
    },

    // called if email is invalid
    invalid: function () {
        return res.badRequest('Does not look like an email address to me!');
    },

    // called if the email validation passed
    success: function () {
        // encrypt the password
        // get password from the body of the request with the req.param call
        Passwords.encryptPassword({
            password: req.param('password'),
        }).exec({
        // if there is an error return a server error 500 status code
        error: function (err) {
            return res.serverError(err);
        },

        // if success then move on to the next step
        success: function (result) {
            // create user with email and encryptedPassword to add to the database
            var user = {
                email: email,
                encryptedPassword: result
            };

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
        }
        })
    }
    });
}
},
```

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
    }
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

1. If we found the user then we want to use the password machine pack that we used earlier to validate that the unecrypted and encrypted passwords match.  The response from the machine pack will be either error, incorrect or success.  If there was an error we will return a server error.  If the password was incorrect we will return a forbidden error.  If it was success then we will add the session user and return the user.   


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


<div class="exercise-end"></div>

### Logout

<h4 class="exercise-start">
    <b>Exercise</b>: 
</h4>

```javascript
logout: function logoutFn(req, res) {
    req.session.user = null;

    return res.ok();
},
```


<div class="exercise-end"></div>

### Get User Info

<h4 class="exercise-start">
    <b>Exercise</b>: 
</h4>

```javascript
find: function findFn(req, res) {
    User.find(
        { id: req.session.user }, 
        function (err, results) {
            if (err) return res.serverError(err);
            if (results.length === 0) return res.notFound();

            return res.ok(results);
        }
    );
},
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


<div class="exercise-end"></div>

### Delete User

<h4 class="exercise-start">
    <b>Exercise</b>: 
</h4>

```javascript

```


<div class="exercise-end"></div>

### Update User

<h4 class="exercise-start">
    <b>Exercise</b>: 
</h4>

```javascript

```


<div class="exercise-end"></div>

### Who is Logged In?

<h4 class="exercise-start">
    <b>Exercise</b>: 
</h4>

```javascript
userIdentity: function (req, res) {
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

<div class="exercise-end"></div>
