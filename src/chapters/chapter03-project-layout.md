## Sails Layout

### Controllers

Controllers are defined in the api/controllers/ folder. You can put any files you like in that folder, but in order for them to be loaded by Sails as controllers, a file must end in Controller.js. 

Controllers (the C in MVC) are the principal objects in your Sails application that are responsible for responding to requests from a web browser, mobile application or any other system capable of communicating with a server. For many applications, the controllers will contain the bulk of your project’s business logic.

Controllers are comprised of a set of methods called actions. Actions are bound to routes, so that when a client requests the route, the action is executed to perform some business logic and send a response. 

Using the command line you can generate a controller and model by using the sails generate command api

```bash
sails generate api <API Name>
```

Get more details on Controllers at [http://sailsjs.org/documentation/concepts/controllers](http://sailsjs.org/documentation/concepts/controllers)

### Models

Models are defined in the api/models/ folder.

A model represents a collection of structured data, usually corresponding to a single table or collection in a database. 

 There are many built-in methods available on models, the most important of which are the query methods: [find](http://sailsjs.org/documentation/reference/waterline/models/find.html), [create](http://sailsjs.org/documentation/reference/waterline/models/create.html), [update](http://sailsjs.org/documentation/reference/waterline/models/update.html), and [destroy](http://sailsjs.org/documentation/reference/waterline/models/destroy.html). These methods are asynchronous - under the covers, Waterline has to send a query to the database and wait for a response.

Get more details on Models at [http://sailsjs.org/documentation/concepts/models-and-orm/models](http://sailsjs.org/documentation/concepts/models-and-orm/models)

### Configuration

Configuration files are defined in the config folder.

While Sails dutifully adheres to the philosophy of convention-over-configuration, it is important to understand how to customize those handy defaults from time to time. For almost every convention in Sails, there is an accompanying set of configuration options that allow you to adjust or override things to fit your needs.

Sails apps can be configured programmatically, by specifying environment variables or command-line arguments, by changing the local or global .sailsrc files, or (most commonly) using the boilerplate configuration files conventionally located in the config/ folder of new projects. The authoritative, merged-together configuration used in your app is available at runtime on the sails global as sails.config.

**config/local.js**

The config/local.js file is used to configure a Sails app for your local environment. The settings in this file take precedence over all other config files except .sailsrc. Since they're intended only for local use, **they should not be put under version control** . Use local.js to store local database settings, change the port used when lifting an app on your computer, etc.


Get more details on Configuration at [http://sailsjs.org/documentation/concepts/configuration](http://sailsjs.org/documentation/concepts/configuration)


### Services

Services are defined in the api\services folder.

Services are stateless libraries of functions (called helpers) that you can use from anywhere in your Sails app.  They are globalized and you don't have to use require() to access them.  

See [Creating a Service Documentation](http://sailsjs.org/documentation/concepts/services/creating-a-service)
