## Sails Project Layout

### API Controllers

> api/controllers

Controllers are defined in the api/controllers/ folder. You can put any files you like in that folder, but in order for them to be loaded by Sails as controllers, a file must end in Controller.js. 

Controllers (the C in MVC) are the principal objects in your Sails application that are responsible for responding to requests from a web browser, mobile application or any other system capable of communicating with a server. For many applications, the controllers will contain the bulk of your project’s business logic.

Controllers are comprised of a set of methods called actions. Actions are bound to routes, so that when a client requests the route, the action is executed to perform some business logic and send a response. 

Using the command line you can generate a controller and model by using the sails generate command api

```bash
sails generate api <API Name>
```

Get more details on Controllers at [http://sailsjs.org/documentation/concepts/controllers](http://sailsjs.org/documentation/concepts/controllers)

### API Models

>directory: api/models

Models are defined in the api/models/ folder.

A model represents a collection of structured data, usually corresponding to a single table or collection in a database. 

 There are many built-in methods available on models, the most important of which are the query methods: [find](http://sailsjs.org/documentation/reference/waterline/models/find.html), [create](http://sailsjs.org/documentation/reference/waterline/models/create.html), [update](http://sailsjs.org/documentation/reference/waterline/models/update.html), and [destroy](http://sailsjs.org/documentation/reference/waterline/models/destroy.html). These methods are asynchronous - under the covers, Waterline has to send a query to the database and wait for a response.

Get more details on Models at [http://sailsjs.org/documentation/concepts/models-and-orm/models](http://sailsjs.org/documentation/concepts/models-and-orm/models)

### API Policies

>directory: api/policies

Policies in Sails are used for authorization and access control.  Basically they let you allow or deny access to your controllers down to a fine level of granularity. 

Policies can be used for anything: HTTP BasicAuth, 3rd party single-sign-on, OAuth 2.0, or your own custom authorization/authentication scheme.

Policies are files defined in the api/policies folder in your Sails app. Each policy file should contain a single function and ideally should just check one thing.

Policies are really just Connect/Express middleware functions which run before your controllers. They are designed to be chained together.  

See [Policies Docs](http://sailsjs.com/documentation/concepts/policies)

### API Responses

>directory: api/responses

Sails allows for customizable server responses. Sails comes with a handful of the most common response types by default that are found in the /api/responses directory of our project. To customize these, simply edit the appropriate .js file.

Any .js script saved in the /api/responses folder will be executed by calling res.[responseName] in your controller. For example, /api/responses/serverError.js can be executed with a call to res.serverError(errors). The request and response objects are available inside the response script as this.req and this.res; this allows the actual response function to take arbitrary parameters (like serverError's errors parameter).

See [Response Docs](http://sailsjs.com/documentation/concepts/custom-responses)

### API Services

>directory: api/services

Services are defined in the api\services folder.

Services are stateless libraries of functions (called helpers) that you can use from anywhere in your Sails app.  They are globalized and you don't have to use require() to access them.  

See [Services Docs](http://sailsjs.org/documentation/concepts/services)

### Configuration Overview

Configuration files are defined in the config folder.

While Sails dutifully adheres to the philosophy of convention-over-configuration, it is important to understand how to customize those handy defaults from time to time. For almost every convention in Sails, there is an accompanying set of configuration options that allow you to adjust or override things to fit your needs.

Sails apps can be configured programmatically, by specifying environment variables or command-line arguments, by changing the local or global .sailsrc files, or (most commonly) using the boilerplate configuration files conventionally located in the config/ folder of new projects. The authoritative, merged-together configuration used in your app is available at runtime on the sails global as sails.config.

Get more details on Configuration at [http://sailsjs.org/documentation/concepts/configuration](http://sailsjs.org/documentation/concepts/configuration)

### Configurations Environment

>file: config/env/development.js and config/env/production.js

### Configurations Connections

>file: config/connections.js

Storage connections are configured in the connections.js file. You can also specify connections in your config/local.js or environment-specific config files and reference them in the connections.js file.

See [Docs](http://sailsjs.com/documentation/reference/configuration/sails-config-connections)

### Configurations CORS

>file: config/cors.js

CORS specifies how HTTP requests to your app originating from foreign domains should be treated. It is primarily used to allow third-party sites to make AJAX requests to your app, which are normally blocked by browsers following the same-origin policy.

See [Docs](http://sailsjs.com/documentation/reference/configuration/sails-config-cors)

### Configurations Locals

>file: config/local.js

The config/local.js file is used to configure a Sails app for your local environment. The settings in this file take precedence over all other config files except .sailsrc. Since they're intended only for local use, **they should not be put under version control** . Use local.js to store local database settings, change the port used when lifting an app on your computer, etc.


### Configurations Models 

>file: config/models.js

Your default project-wide model settings. We will be using it to store your migrate strategy and the default connection to use for your models.  

You can also override the settings on a per-model basis.

See [Docs](http://sailsjs.com/documentation/reference/configuration/sails-config-models)

### Configurations Policies

>file: config/policies.js

Your applications ACL (access control list) to apply to each controller and route.  Policies can be chained together using the array syntax.

See [Docs](http://sailsjs.com/documentation/reference/configuration/sails-config-policies)

### Configurations Routes

>file: config/routes.js

Configuration for your routes.  For a given route you will configure: REST verb, url, controller and function that a route maps to.

See [Docs](http://sailsjs.com/documentation/reference/configuration/sails-config-routes)

### Configurations Session

>file: config/session.js

Configuration for Sails built-in session store.  You can add/get information from session by using req.session.  

See [Docs](http://sailsjs.com/documentation/reference/configuration/sails-config-session)
