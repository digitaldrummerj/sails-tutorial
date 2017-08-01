## Hooking Up a UI

As part of my [Angular Workshop](http://digitaldrummerj.me/angular-tutorial), you build a Todo UI that utilizes the Sails Api that we just built.  The completed code for the UI is available at [https://github.com/digitaldrummerj/angular-tutorial-code].

### Getting the Code 

<h4 class="exercise-start">
    <b>Exercise</b>: Getting the Code
</h4>

1. Download the Zip file for the completed code
1. Unzip the download zip file 

<div class="exercise-end"></div>

### Using the Code

You need to install the Angular CLI in order to use the code.

<h4 class="exercise-start">
    <b>Exercise</b>: Angular CLI Install
</h4>

<div class="alert alert-info" role="alert">If you already have the latest Angular CLI installed, you can skip this step</div>

1. Open a command prompt or terminal
1. Run the following npm command

    ```bash
    npm install -g @angular/cli
    ```

    <div class="alert alert-warning" role="alert">**Warning**: If you are on a Mac, you may need to run `sudo npm install -g @angular/cli`</div>

1. Navigate to the directory you unzipped the code into and go into the Angular project directory
1. Install the npm dependencies

    ```bash
    npm install
    ```

1. Open the directory for the Angular code in Visual Studio Code
1. Open the src/environments/environment.ts file

    ```bash
    environment.ts
    ```

1. Change the API base url to your Sails url.  Either your locally running instance or your deployed instance.
1. Save the file
1. Start the Angular web server

    ```bash
    ng serve
    ```

1. Navigate to [http://localhost:4200](http://localhost:4200) to view the UI
1. You can now login to the account that you created using Postman or create a new account.
1. Once you create/login, you will be able to see your todo list on the home page
1. On the home page, you can add new Todo items, completed an item or delete an item.

