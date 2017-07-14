## Getting up and running

To get started with Angular and Sails, we need to install and configure some software first.

* Windows, Mac, or Linux computer
* git command line
* Node 6.x
* npm modules:
    * intc-cli
    * grunt-cli
    * sails
* Postman    
* Visual Studio Code

Sails development at Intel leverages a number of open source tools and frameworks to install, develop, test, debug, execute and deploy your application. Follow the sequence of steps below to install all required open source tools and frameworks to support Sails development on your system. 

Below are all of the steps to install and configure the software.  

### Windows Showing File Extensions

<h4 class="exercise-start">
    <b>Exercise</b>: Turn On Windows Showing File Extensions
  </h4>

<div class="alert alert-danger" role="alert">
**Windows Only**
</div>

By default Windows is set to not show file extensions for known files which causes files such as .gitconfig and .npmrc to show up as just a period with no file extension.  To fix this we need to turn set Windows Explorer to show file extensions.

1. Open Windows Explorer
1. Click on the View Tab and select Options

    <img src="images/chapter1/windows-explorer-ribbon.png" style="height:147px;width:759px;margin-left: 10px">

Once the "Folder Options" dialog is open: 

1. Click on the View Tab
1. Uncheck the "Hide extensions for known file types"
1. Click Ok

    <img src="images/chapter1/windows-explorer-view-options.png" style="height:475px;width:382px;margin-left: 10px">
  
<div class="exercise-end"></div>

### Git

<div class="alert alert-danger" role="alert">
**Windows Only**
</div>

1. Download the latest version of [Git](http://git-scm.com/download).
1. Run the installer. 
1. Click Next 

    <img src="images/chapter1/git-setup-screen-1.png" style="height:490px;width:598px;margin-left: 10px;">

1. Check all except for the last checkbox and click Next

    <img src="images/chapter1/git-setup-screen-2.png" style="height:490px;width:598px;margin-left: 10px;">

1. Select the "Use Git from the Windows Command Prompt" and click Next

    <img src="images/chapter1/git-setup-screen-3.png" style="height:484px;width:598px;margin-left: 10px;">

1. Select the "Checkout Windows-style, commit Unix-style line endings" and Click Next

    <img src="images/chapter1/git-setup-screen-4.png" style="height:484px;width:598px;margin-left: 10px;">

1. Select the "Use MinTTY (the default terminal of MSYS2)    

    <img src="images/chapter1/git-setup-screen-5.png" style="height:484px;width:598px;margin-left: 10px;">

1. Check "Enable File system caching" and "Enable Git Credential Manager" then click Install

    <img src="images/chapter1/git-setup-screen-6.png" style="height:484px;width:598px;margin-left: 10px;">

1. After the install has completed, open a command prompt and run:

    <h4 class="exercise-start">
    Verify Git Version
    </h4>

    ```
    git --version
    ```

    <div class="exercise-end"></div>


#### Git Credential Helper

<div class="alert alert-danger" role="alert">
**Windows Only**
</div>

<div class="alert alert-info" role="alert">
    Note: This should be already installed as part of the Git install
</div>


Credential helper alleviates the need for you to continually enter your idsid and password when interacting with github.intel.com from the command line.

1.	Download the latest installer of [Git Credential Manager](https://github.com/Microsoft/Git-Credential-Manager-for-Windows).
1.	Run the installer. This will only take a few seconds and you'll see a command editor window briefly appear.
1.	The first time you try to interact with Github from the command prompt you'll be prompted for your idsid and password which Credential Helper will store.

#### Git Profile configuration

The last item we need to do is to setup your Git profile. In the terminal window execute the following sets of commands. 

The first two commands setup your user profile which is important for checking in of code to be associated to you.  The next two commands setup proxy information for http and https.  Then we need to tell  Git to use 'https' instead of the git protocol since the git protocol is blocked by the Intel firewalls.  The final command is for Windows machines to ignore the 260 character path limit and allow longer file paths when doing a git clone.

<h4 class="exercise-start">
Git Config Commands
</h4>


```bash
git config --global user.name <Your name>
```

```bash
git config --global user.email <Your email address>
```

```bash
git config --global --add core.longpaths true
```
<div class="exercise-end"></div>



<h4 class="exercise-start">
Validate your Git configurations
</h4>

```bash
git config --global --list
```

<div class="exercise-end"></div>

Your output should look similar to:

```bash
credential.helper=manager
url.https://github.com.insteadof=git://github.com
user.email=foo@foo.com
user.name=Foo Bar
core.longpaths=true
```


### Node.js

1.	Download the latest stable version (LTS) of [NodeJS](http://nodejs.org) which as of this writing is 6.11.0.  

1.	Run the installer and accept all defaults.
1.	Launch a command prompt or terminal window and run: 

    <h4 class="exercise-start">
        Verify Node Version Installed
    </h4>

    ```bash
    node -v
    ```
    <div class="exercise-end"></div>



### Sails.js

Open a terminal window if it's not already open and execute the command:

<h4 class="exercise-start">
    Install Sails Globally
</h4>

```bash
npm install -g sails
```
<div class="exercise-end"></div>

<div class="exercise-start">
<h4>Check Sails version installed</h4>
</div>

```
sails -v
```

> 0.12.13 as of this writing

<div class="exercise-end"></div>


### Visual Studio Code

You could use any editor that you would like for Sails but I recommend using Visual Studio Code.

You can download Visual Studio Code at [https://code.visualstudio.com/](https://code.visualstudio.com/)

Once the download finishes, launch the installer and accept all of the defaults.

### Postman

Postman is a useful utility for excersing a REST apis without having to create a UI.

You can download it from [https://www.getpostman.com/apps](https://www.getpostman.com/apps).  

Once the download finishes, launch the installer and accept all of the defaults.