## CORS Configuration

Before we can use our API against a UI or allow others to call the API we need to configure CORS.

CORS allows us to configure which routes, domains, if cookies are passed in, which REST verbs are allowed, and what header values are allowed.  

Without configuring, no one would be able to make calls to our API.

### CORS Routes

<h4 class="exercise-start">
    <b>Exercise</b>: Routes
</h4>

The 1st configuration is the routes that are allowed configuration.  You do not have to allow all Routes and can configure CORS on a per Route basis if you would like.  If I am doing an API that is meant to be called by anyone, I typically set allRoutes to true.

For the purpose of this tutorial, we are going to set it to allow all routes.

1. Open config\cors.js

    ```bash
    cors.js
    ```

1. Find the commented out allRoutes, uncomment it and set it to true

    ```javascript
    allRoutes: true,
    ```

1. Save the file

<div class="exercise-end"></div>

### CORS URLS to Allow

You need to specify which URLs are allowed make calls to our API.  It is a comma delimited list of host names beginning with either http:// or https://.  You can also use * to say to allow all domains.  

For the purpose of this tutorial, we are going to allow all domains to make API calls.  This means that any could make a Todo UI and use our API as the backend.

<h4 class="exercise-start">
    <b>Exercise</b>: Urls to Allow
</h4>

1. Still within the config\cors.js

    ```bash
    cors.js
    ```


1. Find the comment our origin and uncomment it.  

    ```javascript
    origin: '*',
    ```

1. Save the file

<div class="exercise-end"></div>

### CORS Cookie Sharing

The ability to share cookies with the API is very important when dealing with in-memory Sails sessions.  By default Sails uses in-memory sessions and sends a cookie with the session hash to the client.  If the client does not send the cookie back with each request then Sails treats the session as a new session which means that for our API, the user would never be treated as being logged in.

<h4 class="exercise-start">
    <b>Exercise</b>: Allow Cookies
</h4>

1. Within config\cors.js

    ```bash
    cors.js
    ```

1. Find the commented out credentials and uncomment it

    ```javascript
    credentials
    ```

1. Save the file


<div class="exercise-end"></div>

### CORS Allowed REST verbs

As part of the CORS pre-flight checks, it determines if the REST verb is allowed or not.  We are going to define that all REST verbs, options and head are allowed. 

<h4 class="exercise-start">
    <b>Exercise</b>: REST Verbs Allowed
</h4>

1. Within config\cors.js

    ```bash
    cors.js
    ```

1. Find the methods list that is commented out and uncomment it.

    ```javascript
    methods: 'GET, POST, PUT, DELETE, OPTIONS, HEAD',
    ```

1. Save the file

<div class="exercise-end"></div>

### CORS Headers

<h4 class="exercise-start">
    <b>Exercise</b>: Headers
</h4>

1. Within the config\cors.js

    ```bash
    cors.js
    ```

1. Find the commented out headers and uncomment it

    ```javascript
    headers: 'content-type'
    ```

1. Save the file

<div class="exercise-end"></div>

### Verifying CORS

Before when you tried to query the API, you got a PreFlight error.  Now if you try to query the API, you will be able to see any machine can query the API.  
