## Getting up and running

We will utilize a number of tools in our Sails development to allow us to create, debug, and deploy our application.  We will be utilizing the following tools:

* Platforms: Windows, Mac, or Linux computer
* Software:
    * git command line
    * Node 6.x
    * sails
    * Postman    
    * Visual Studio Code


Below are all of the steps required to install and configure the tools.  

### Windows Showing File Extensions

<div class="alert alert-danger" role="alert">
**Windows Only**
</div>

**Non-Windows users can [skip to next section](#chapter2.4)**

By default Windows is set to not show file extensions for known files which causes files such as .gitconfig and .npmrc to show up as just a period with no file extensions.  To fix this we need to set Windows Explorer to show file extensions.

<h4 class="exercise-start">
    <b>Exercise</b>: Turn On Windows Showing File Extensions
</h4>

1. Open Windows Explorer
1. Click on the View Tab and select Options

    <img src="images/chapter1/windows-explorer-ribbon.png">

1. Click on the View Tab
1. Uncheck the "Hide extensions for known file types"
1. Click Ok

    <img src="images/chapter1/windows-explorer-view-options.png">
  
<div class="exercise-end"></div>

### Node.js

NodeJS is used to power the Sails CLI as well as install all of our dependencies.  The Sails CLI will work with Node 4.x+.  Since I am using Angular for my UI which requires Node version 6.9+, we are going to install the latest LTS version of Node which as of this writing is 6.11.0..

<h4 class="exercise-start">
<b>Exervise</b>: Install Node
</h4>

1.	Download the latest stable version (LTS) of [NodeJS](http://nodejs.org).  
1.	Run the installer and accept all defaults.
1.	To validate that Node installed successfully, open a new command prompt and run the following command:

    ```bash
    node -v
    ```

<div class="exercise-end"></div>

### Sails.js

<h4 class="exercise-start">
    <b>Exercise:</b>: Install Sails Globally
</h4>

1. Open a terminal window if it's not already open
1. Execute the following command to install Sails:

    ```bash
    npm install -g sails
    ```

1. Validate that Sails installed successfully by running the following command:

    ```bash
    sails -v
    ```

    > 0.12.13 as of this writing

<div class="exercise-end"></div>

### Postman

Postman is a useful utility for testing REST apis without having to create a UI.

1. You can download it from [https://www.getpostman.com/apps](https://www.getpostman.com/apps)
1. Once the download finishes, launch the installer and accept all of the defaults

### Visual Studio Code

Visual Studio Code is Microsoft lightweight cross platform IDE.  You could use any editor that you would like to developer our  Sails but only Visual Studio Code has been tested against

1. Download Visual Studio Code at [https://code.visualstudio.com/](https://code.visualstudio.com/)
1. Once the download finishes, launch the installer except all of the defaults

