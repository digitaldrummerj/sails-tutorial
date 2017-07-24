## Model Migration Strategy

Right now everytime you start sails lift, you are asked what migration strategy you want to use to keep the model in sync with the data store.  Being asked each time becomes annoying very quickly.  Lets go ahead and look at migration strategy options.

As you are developing your API and changing the models, you need to decide on the strategy to use for keeping your model in sync with your data store.  

### Migration Options

There are 3 options:

| Migration Strategy | Description |
| ---- | ---- |
| safe | never auto-migrate.  you will perform the migration by hand |
| alter | auto-migrate columns/fields but attempt to keep existing data |
| drop | wipe/drop ALL data and rebuild models every time you run sails lift |


### How auto-migrations work

When you lift your Sails app in a development environment, the configured auto-migration strategy will run. 

* If you are using migrate: 'safe', then nothing extra will happen at all. 

* If you are using drop or alter, Sails will load every record in your development database into memory, then drop and recreate the physical layer representation of the data (i.e. tables/collections/sets/etc.) This allows any breaking changes you've made in your model definitions, like removing a uniqueness constraint, to be automatically applied to your development database. 

* As well if you are using alter, Sails will then attempt to re-seed the freshly generated tables/collections/sets with the records it saved earlier.

### Can I use auto-migrations in production?

The drop and alter auto-migration strategies in Sails exist as a feature for your convenience during development, and when running automated tests. They are not designed to be used with data you care about. Please take care to never use drop or alter with a production dataset. 

As a failsafe, any time you lift your app in a production environment, Sails always uses migrate: 'safe', no matter what you have configured.

In many cases, hosting providers automatically set the NODE_ENV environment variable to "production" when they detect a Node.js app. Even so, please don't rely only on that failsafe, and take the usual precautions to keep your users' data safe. 

### Set Migrate Strategy

<h4 class="exercise-start">
    <b>Exercise</b>: Set Migrate Strategy
</h4>

Since we are in development, we are going to use the alter strategy to have Sails keep our model in sync with our data store.  

1. Open the config\models.js file
1. At the bottom of the file uncomment the migrate line

    ```javascript
    migrate: 'alter'
    ```

<div class="exercise-end"></div>
