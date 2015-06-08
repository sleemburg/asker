# Asker

Asker is an insecure (I can't stress this enough) PHP script to create web based dialogs with a user to execute actions. The main drive behind the script was to create a web based interface for a helpdesk to allow them to execute tasks that normally would have to be done by system administrators.

This is **BETA** grade software at best!

Asker tries to make it easy to design screens and is therby restricted to some basic types of dialogs that are supported:

- plain text display
- free form input fields
- dropdown select boxes
- checkboxes
- buttons

Input values come either form the dialogs or from an executed command. The values are assigned to variables that can be used in the dialog.

The configs directory contains a test configuration which tries to demo some of the features.

## Installation

Asker has only been tested on Debian/Linux at this moment. Currently it has been tested with the following components:

- Apache webserver
- PHP 5
- Firefox webbrowser (recent release)

Asker will try and check if the connection to the webserver is encrypted and you have basic authentication running. If you don't it will complain. You can overrule this behaviour in the asker.php file. Just edit the file and look at the top of the file for:

```
$OVERRULE_SSL=false;
$OVERRULE_AUTH=false;
```

Change them to true if you want to overrule either or both checks.

Asker will also do some basic sanity checking of the permissions of the `config` directory, to make sure there aren't any too obvious security issue's.

After putting the files from the repository somewhere in the webserver document
root you should be able to start asker with the test configuration:

http://yourserver/installdir/asker.php?action=test

If you no longer require the test configuration, I would urge you to remove the `test.sh` shellscript that is used in the testcase.

# Concept

The basic thought behind asker is to create a sequence of stateless web based screens, which can transition to eachother. You can go from screen to screen, showing information and requesting input from the user. The communication between screens is done by variables. Input from the user, or output from commands is assigned to variables that can be used in either the screen output or running commands.

# Configuration

The configuration uses the standard ini file format (because it was easy to implement :) ). The idea is that for each specific set of dialogs there is a seperate configuration file. When starting asker you need to specify which configuration file it needs to process through the action parameter. The value of the action parameter is the name of the ini configuration file without the extension.

## Variables

Except for the global part of the configuration, variables can be used when defining screens. Variables are identified bij % marks around the capitalised names. So for example:

`%VAR%`

is a variable. Variables can be choosen to be any letter/number combination, except for the variable `%OUTPUT%`. This variable is automatically defined when a screen is defined with a command to run. The variable `%OUTPUT%` contains the output of the command.

## Global configuration

Each configuration file has a global configuration part. which is called

```[start]```

The following configuration items are available in this section:

### name (mandatory)

Name is the name of the configuration. This name is used to display in the generated screens.

Example: name = "Test case"

### begin (mandatory)

Begin refers to the defenition of the screen at which the configuration should start.

Example: name = main

### css (optional)

This refers to a file with a cascading style sheet to change the design of the pages.

Example: css = asker.css

## Screen configuration

There can be loads of screens in a single configuration file (maybe it is better to split them out over more configuration files, but it is possible).

A screen starts at the defenition of a new section heading (with the exception of the start section as discussed previously).

Within and between screens variables can be used.

Each screen can have the following configuration items

### title (mandatory)

This is a description of the specific screen and is used to display on the scren.

Example: title = "This is the first screen"

### action (optional)

An action defines a command that needs to be executed. There can only be one action per screen.

The command will always run in the background. When running an action the webbrowser will be served with a bit of javascript which periodically will poll the server to see if the task has already completed. This works around webserver timeout problems with long running tasks.

An action is split into two fields, seperated by a comma:

#### Output type

The output type is the way the output of the command is processed. 

- normal: With the task running in the background you can configure the webbrowser to only show a time counter indicating the time that the command has been running.
- follow: With the task running in the brackground the webbrowser will show the output of the command in the browser as it is running.

#### Command

This can be any command which the user running the webserver can execute.

Examples
Generate a process list and display the amount of time that has elapsed:
```action = normal,ps -ef```
Create a directory listing and sleep 5 seconds with the output showing on screen:
```action = follow,ls -al;sleep 5```

### item[] (optional)

An item is something which is displayed in the browser. To display anything you will need at least one item[] in the configuration. You can have many of these items per screen. They will be processed in the order that they are defined.

The value of the item[] defines what it will do. There are different kinds of output that can be generated with items.  Each type of item can have arguments, which are seperated by a comma. What each argument does depends on the type of item.

Below follows a list of different types of items.

#### text

Text is a very basic item. It will display the text following it on the screen.

Arguments:
- Text to display

Example: item[] = text,"This is a test"

#### input

Input will show a free form input field in which the user can fill out text. The text will be assigned to a variable



#### Command

# Screen design

The design of the screens is something that is not done through asker. There is a configuration setting in each configuration file which you can use to reference a cascading stylesheet. There is an example in the repository which does at least some formatting.
