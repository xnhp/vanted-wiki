# Get the code
To start developing you have to checkout two git repositories. The VANTED code base and a library repository that contains all libraries used by VANTED.

To start, create a new directory, where your VANTED code and eclipse workspace will reside.

Change to that directory and clone the code from BitBucket.
For this guide all *git* commands are executed on the command line. Please, refer to the SourceTree tutorials on how to checkout code using the graphical interface.

```
# Clone the VANTED code base:
$ git clone https://github.com/LSI-UniKonstanz/vanted

# Clone the VANTED library repository:
$ git clone https://github.com/LSI-UniKonstanz/vanted-libraries
```

Main stable branch is `master`.

Main development is happening on the `develop`-branch. This is not always a stable branch.

Now start Eclipse and select a workspace. This would be the directory you have created before.

## Setup Eclipse
Now it is time for the final tweaks in Eclipse.
### Import Projects
You should see an empty workspace. Now you need to import the two projects, that reside in the directory, but are not part of your workspace-projects yet.
To do so:

* Select `File -> Import` from the Menu.
* select `Existing Projects` into Workspace.
* select the root directory which is the directory you created before.
* if not already selected, selected `vanted` and `vanted-libraries` from the list.

### Check Encoding
If you are working on Windows, please make sure, that the character encoding is set to UTF-8. You can set the encoding in the Eclipse preferences under `General -> Encoding`. Make sure the Text file encoding is set to UTF-8. After changing the encoding Eclipse will build the project again.
### Start Program
Open the Java file `Main.java` in the package `ipk_gatersleben.ag_nw.graffiti.plugins.gui.webstart`.
Now right-click on the file in the Package Explorer and Debug/Run as Java Application.

You should see now VANTED starting.

To _enable debugging_ messages on the console, open the Run/Debug configuration Eclipse just created for you. It should be called  `Main`.
Now open the  `Arguments` tab and enter the following into the  `VM arguments:` field:
`-Dvanted.debug="true"`.

You should see now a log message, telling you that logging is enabled:
```
Main:* - If you can read this: Vanted is displaying debug messages
```

That's it. You're good to go and start developing.