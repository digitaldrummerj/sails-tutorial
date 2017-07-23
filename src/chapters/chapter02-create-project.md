## Creating Sails Api

We will utilize the Sails CLI to generate a project for us to developer our API with.  By default Sails comes with a view engine unless you tell it otherwise.  When we create our project we will only  be creating an API project and will not include the view engine as part of the project. 

### Create Project

<h4 class="exercise-start">
    <b>Exercise</b>: Create Sails Project
</h4>

To generate the project we need to run the intc gen-ui command.

1. Open a command prompt or terminal
1. Navigate to where you store your project code (I store mine at c:\projects)
1. Run the following command to generate the project.  

        sails new sails-tutorial --no-linker --no-frontend
    <div class="alert alert-info" role="alert">This will create a new Sails project called sails-tutorial and install the npm dependencies</div>

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

###  Navigating around Visual Studio Code 

Being able to effectively use your editor is key to being a super productive developer.  With Visual Studio Code, there are several shortcut keys that will help you out.

| Purpose | Key |
| ---- | ---- |
| Integrated Terminal | ctrl+` |
| Open File | ctrl+p |
| Switch Between Files | ctrl+tab |
| Switch Between Files Reverse | ctrl+shift+tab |
| Hide Side Menu | ctrl+b |
| Toggle Word Wrap for File  | alt+z |
| Format Document | ctrl+alt+f | 

<div class="alert alert-info" role="alert"> I also split my screen in half with the tutorial on one side and the editor on the other side.  On Windows you can do this by using the win key + left arrow for the one you want on the left and then select the other for the right when windows prompts you.  You can also use win key + right arrow to make a Windows use half the screen on the right.</div>

### Visual Studio Settings

There are 2 types of Settings in Visual Studio Code:  User and Workspace.  User settings apply to all instances of Visual Studio Code.  Workspace settings apply to the currently folder that you have open and are stored in a .vscode directory within the folder.

There are a few settings that I like to change within Visual Studio Code.  

1. Turn off the mini map in the upper right corder that I find distracting
1. Turn off telemetry and crash reporter
1. Turn on word wrap
1. Turn on indent guides

<h4 class="exercise-start">
  <b>Exercise</b>: Updating Settings
</h4>

1. Go under the Preferences -> Settings (File -> Preferences -> Settings on Windows) and add the following setting to the "User Settings" tab

```bash
{
    "telemetry.enableTelemetry": false,
    "telemetry.enableCrashReporter": false,
    "editor.minimap.enabled": false,
    "editor.wordWrap": "on",
    "editor.wrappingIndent": "same",
    "editor.renderIndentGuides": true,
}
```

<div class="exercise-end"></div>


