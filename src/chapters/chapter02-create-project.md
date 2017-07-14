## Creating Sails Api

Now that we have our UI showing and adding data, we are going to move from hard coded data to using an Api we are going to create to interat with a data store.  We wll be using SailsJs as our Api framework.  

### Create Project

<h4 class="exercise-start">
    <b>Exercise</b>: Create Sails Project
</h4>

To generate the project we need to run the intc gen-ui command.

1. Open a command prompt or terminal
1. Navigate to where you store your project code (I store mine at c:\projects)
1. Run the following command to generate the project.  

        sails new sails-tutorial --no-linker --no-frontend

    This will create a new Sails project called sails-tutorial that is just the API without the ejs view engine or Grunt.  It will also install the npm dependencies as part of the project creation.

<div class="exercise-end"></div>

### Opening Project in Visual Studio Code

<h4 class="exercise-start">
  <b>Exercise</b>: Open Project
</h4>

1. Open Visual Studio Code
1. Click File -> Open Folder...
1. Navigate to where you created the sails-tutorial folder and click Select Folder 
1. Your project should now be opened in Visual Studio Code

<div class="exercise-end"></div>

### Generating Api

<h4 class="exercise-start">
  <b>Exercise</b>: Generating User and Todo Api
</h4>

```bash
sails generate api user
```

```bash
sails generate api todo
```

<div class="exercise-end"></div>

### Running Api

<h4 class="exercise-start">
    <b>Exercise</b>: Starting Api
</h4>

1. Visual Studio Code has a built-in terminal that we can use to run our commands.  On Windows, this is a powershell prompt.  To open the Integrated Terminal go under the View Menu and click on the Integrate Terminal or press Ctrl+`
    * You are free to use the regular command prompt outside of Visual Studio Code if you would like
1. Run

    ```bash
    sails lift
    ```

    ![sails lift output](images/sails-lift.png)

1. ???? ADD POSTMAN INFO ????

    

<div class="exercise-end"></div>

