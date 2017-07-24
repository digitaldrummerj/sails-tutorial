## User API Logic

In this chapter we are going to add logic to allow a user to login, signup, logout, perform CRUD (**C**reate, **R**ead, **U**pdate, **D**elete) operations on the data that they own.


### Signup


We are going to override the POST verb with our own create function that will validate that the email address is an actual email address, encrypt the password and create a user record in the data store.

<h4 class="exercise-start">
    <b>Exercise</b>: Allow a User to Signup
</h4>

The first thing we need to do is create our method stub.

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

1. By creating the above method, Sails will automatically use this method for the POST verb without us having to do any route configurations.

<div class="exercise-end"></div>


<h4 class="exercise-start">
    <b>Exercise</b>: Validate Email Address
</h4>

The first thing we want to do when a user signs up is to validate that their email address is an actual email address.  To validate the email address, we are going to use Node Machine Packs.

Node Machine Packs are single purpose npm libraries.  For example the Email Address machine pack only has 1 function: validate instead of having a whole slew of helper function.  This makes the machine packs very lightweight.  

The only downside to using the machine packs is that you will have to use callbacks instead of promises within the Sails functions that we create that uses a machine pack.

Before we can use a Machine Pack we need to install the pack.  They are installed using NPM.  

1. Open the Visual Studio Code Integrated Terminal
1. Run the following command to install the machinepack-emailaddresses that will be used to validate the email address

    ```bash
    npm install --save machinepack-emailaddresses
    ```

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
        if (err) res.serverError(err);
        
        // add user id to session state
        req.session.user = createdResult.id;

        // return back created user with a status code of 200
        // see api\responses\ok.js for what the ok response is actually doing
        return res.ok(createdResult);
    });
    ```

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
                if (err) res.serverError(err);

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

It is an improvement that we are now encrypting the password but it is still passing the encrypted hash across the wire.  This is not a good security practice.  There is no reason to pass the encrypted password back from the API.  

<h4 class="exercise-start">
    <b>Exercise</b>: Stripping the encryptedPassword from JSON
</h4>

1. Open the api\models\User.js

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

1. Now if you run the GET call for the user, you will see that the encryptedPassword field is no longer returned back from the API

    ![User Get without EncryptedPassword](images/postman-user-without-encryptedPassword.png)

<div class="exercise-end"></div>

### Make sure Email is unique

<h4 class="exercise-start">
    <b>Exercise</b>: Defining Fields
</h4>

```javascript
email: {
    required: 'true',
    type: 'string',
    unique: true,
},
encryptedPassword: {
    required: 'true',
    type: 'string',
},
```

<div class="exercise-end"></div>

### Login


<h4 class="exercise-start">
    <b>Exercise</b>: 
</h4>

```javascript
 login: function loginFn(req, res) {
    User.findOne(
        { email: req.param('email') }, 
        function (err, result) {
            if (err) return res.serverError(err);
            if (!result) return res.notFound();

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
        }
    );
  },
```


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
