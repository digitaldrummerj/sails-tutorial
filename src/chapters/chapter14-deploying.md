## Deploying

### Preparing for Production

http://sailsjs.com/documentation/concepts/deployment


### Deploy to Heroku

Prerequisites:

* [Git](http://git-scm.com/)
* [Heroku account](https://signup.heroku.com/login)
* [Heroku Toolbelt installed](https://toolbelt.heroku.com/) (CLI tool for creating and managing Heroku apps)

<h4 class="exercise-start">
    <b>Exercise</b>: Create Heroku App
</h4>

1. Stop sails lift
1. Open a command prompt or Windows terminal outside of Visual Studio Code
1. Navigate to our Sails app
1. Login to your Heroku account

    ```bash
    heroku login
    ```

    <div class="alert alert-warning">If heroku can't be found, you will need to hit the trash in the upper right of your integrated shell and then open the integrated terminal up again<div>

1. Create a new heroku app

    ```bash
    heroku create your-app-name
    ```

1. Next we need to create a ProcFile in the root of our project to tell Heroku how to run our Sails app

    ```bash
    Procfile
    ```

1. In the Procfile, add the following line

    ```bash
    web: node app.js
    ```

1. We need create a Git repository for our Sails ap

    ```bash
    git init
    ```

1. We are going to use Git on Heroku to do a deploy to Heroku

    ```bash
    heroku git:remote -a your-app-name
    ```

1. Add the Procfile to Git

    ```bash
    git add .
    ```

1. Now we need to commit the Procfile

    ```bash
    git commit -m "Add Procfile"
    ```

1. We need to push to heroku

    ```bash
    git push heroku master
    ```

1. It will take a bit to deploy and install the dependencies on Heroku.  Once the deploy is completed, you will be able to view your application at [https://your-app-name-heroku.herokuapp.com](https://your-app-name-heroku.herokuapp.com)

Anytime you need to redeploy the app run

```bash
git add .
git commit -m "new changes"
git push heroku master
```

<div class="exercise-end"></div>

### Deploy to Azure

Microsoft has a great article on their Docs site at [https://docs.microsoft.com/en-us/azure/app-service-web/app-service-web-nodejs-sails]
(https://docs.microsoft.com/en-us/azure/app-service-web/app-service-web-nodejs-sails)

