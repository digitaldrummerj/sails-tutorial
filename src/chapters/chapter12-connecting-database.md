## Connecting to A Data Store

In production, you will want to connect to a real data store instead of the default JSON file data store.

Sails has more than 30 data stores connectors available.

All of the connections are stored in config/connection.js and the default connection to use is stored in config/models.js

For this tutorial, we are going to use a free Postgresql database hosted on Heroku.

### Postgres 

<h4 class="exercise-start">
    <b>Exercise</b>: Postgres
</h4>

1. Open integrated terminal
1. Login to Heroku

    ```bash
    heroku login
    ```

1. To add a free Postgresql database to our Heroku App run the following command.  The command will associate the database with the app sails-ws and name the add-on sails-ws-pg

    ```bash
    heroku addons:create heroku-postgresql:hobby-dev --app sails-ws --name sails-ws-pg --wait 
    ```

1. Get the database url from Heroku by running

    ```bash
    heroku config -s
    ```

Now we need to install the Sails Postgres connector

1. Run the following npm install command

    ```bash
    npm install sails-postgresql --save
    ```

1. Open config\connections.js

    ```bash
    connections.js
    ```

1. Find the Postgres section
1. Uncomment the somePostgresqlServer connection 
1. Change the name from somePostgresqlServer to sailsTutorialPostgres
1. Remove the host, user, password, and database for your Postgres database configuration
1. Add url set the value to the output of the `heroku config -s` command and set ssl to true 

    ```javascript
    sailsTutorialPostgres: {
        adapter: 'sails-postgresql',
        url: herokuConfigOutputForDatabaseUrl,
        ssl: true
    }
    ```
1. Open the config/models.js

    ```bash
    models.js
    ```

1. Uncomment the connection on line 20 
1. Make sure that line 20 is set to `sailsTutorialPostgres`

    ```javascript
    sailsTutorialPostgres
    ```

1. Save the file

1. Restart sails lift and it will connect to your data store.  The first time will be slower as it migrates your models (e.g. creates tables and columns)

<div class="alert alert-warning" role="alert">**Warning**: If you connect to your data store for the first time with the NODE_ENV set to production or run sails lift with the --prod flag, the migrate configuration will automatically be set to safe and your models will not be created in the data store.  To workaround this, you can connect to your data store from your local machine to get the models migrated.  Just becareful doing this since it could potentially impact data and/or performance during the migrate operation.  </div>

1. Once `sails lift` start, you can stop it since we won't be using it.

Now that we have our models in our Postgres data store, we need to configure it to use an environment variable to get the connection string.

1. In the models.js file, change the connection to localDiskDb

    ```bash
    localDiskDb
    ```

1. Save the model.js file
1. Open the config/connections.js file

    ```bash
    connections.js
    ```

1. Find the Postgres section and change the url to process.env.DATABASE_url 

    ```javascript
    url: process.env.DATABASE_URL,
    ```

1. Save the file

<div class="exercise-end"></div>

### Configure Models to Use Connection 

You have 2 options for configuring the data store connection that models use:

1. Global configuration in config\models.js
1. Configuration in each model

Since we are only using 1 data store for this tutorial, we are going to go with option 1 and create a global configuration.

<h4 class="exercise-start">
    <b>Exercise</b>: Configure Model Connection
</h4>

We want to set the production environment variables to make it use the Postgresql connection in production.

We have 2 environment files that allow us to have different settings between development and production:

1. config\env\development.js -> used for development when NODE_ENV is not set to production
1. config\env\production.js -> used for when NODE_ENV is set to production

1. Open config\env\production.js

    ```bash
    production.js
    ```

1. Configure the models to use the sailsTutorialPostgres connection that we just configured

    ```javascript
    models: {
        connection: 'sailsTutorialPostgres'
    },
    ```

1. Save the file

<div class="exercise-end"></div>

<h4 class="exercise-start">
    <b>Exercise</b>: Deploy Changes to Heroku
</h4>

We are now ready to deploy our updates to Heroku.

1. Open the integrated terminal
1. Add our changes to git

    ```bash
    git add .
    ```

1. Commit our changes

    ```bash
    git commit -m "configured postgres"
    ```

1. Push our changes to Heroku

    ```bash
    git push heroku master
    ```

1. You can now access the API at [https://sails-ws.herokuapp.com/](https://sails-ws.herokuapp.com/)
    * The first thing you will want to do is run a post against [https://sails-ws.herokuapp.com/user](https://sails-ws.herokuapp.com/user) to create our new user.  
    * Then you can make calls against the User and Todo Api
    
<div class="exercise-end"></div>
