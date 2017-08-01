## Connecting to A Data Store

In production, you will want to connect to a real data store instead of the default JSON file data store.

Sails has more than 30 data stores connectors available.

All of the connections are stored in config/connection.js and the default connection to use is stored in config/models.js

### Assumptions

It is assume that you already have a database created.  If you do not have a database, you can create a free MongoDb and Postgres on Heroku or a free MySQL database on Azure.

### Mongo 

<h4 class="exercise-start">
    <b>Exercise</b>: Mongo Db
</h4>

1. Open integrated terminal
1. Run the following npm install command

    ```bash
    npm install sails-mongo --save
    ```

1. Open config\connections.js

    ```bash
    connections.js
    ```

1. Find the mongo db section
1. Uncomment the someMongodbServer connection 
1. Change the name from someMongodbServer to something more useful to you
1. Update the host, port, user, password, and database for your Mongo database
1. Save the file

<div class="exercise-end"></div>

### MySQL

<h4 class="exercise-start">
    <b>Exercise</b>: MySQL
</h4>

1. Open integrated terminal
1. Run the following npm install command

    ```bash
    npm install sails-mysql --save
    ```


1. Open config\connections.js

    ```bash
    connections.js
    ```

1. Find the MySQL section
1. Uncomment the someMysqlServer connection 
1. Change the name from someMysqlServer to something more useful to you
1. Update the host, user, password, and database for your MySQL database
1. Save the file

<div class="exercise-end"></div>

### Postgres 

<h4 class="exercise-start">
    <b>Exercise</b>: Postgres
</h4>

1. Open integrated terminal
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
1. Change the name from somePostgresqlServer to something more useful to you
1. Update the host, user, password, and database for your Postgres database
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

1. Open config\models.js

    ```bash
    models.js
    ```

1. Uncomment the connection on line 20 
1. Change the value of the connection to the name of the connection that you want to use from the connections.js file
1. Save the file
1. Restart sails lift and it will connect to your data store.  The first time will be slower as it migrates your models (e.g. creates tables and columns)

<div class="alert alert-warning" role="alert">**Warning**: If you connect to your data store for the first time with the NODE_ENV set to production or run sails lift with the --prod flag, the migrate configuration will automatically be set to safe and your models will not be created in the data store.  To workaround this, you can connect to your data store from your local machine to get the models migrated.  Just becareful doing this since it could potentially impact data and/or performance during the migrate operation</div>

<div class="exercise-end"></div>