# Add-on Development

VANTED is an extendible software. Additional functionality can be provided by Add-ons. These are libraries that can be added during runtime to provide new algorithms, user-interfaces and visual network components.

Add-ons can use the provided APIs to easily access and change attributes, setup and call algorithms, etc.

To start development it is a good idea to download the example-addon code from [here](https://github.com/LSI-UniKonstanz/vanted-addon-example/releases/tag/v2.0.1).

The following explains the most important parts of the directory structure, necessary files and some method calls of the VANTED API.

* [[Project Structure and Compilation|ProjectStructure]]

* [[Adding new Algorithms, Views, and Visual Graph Components|AddonExtensions.md]]

## Using Eclipse

* It is, as the VANTED project itself, already an eclipse project. After you have unzipped the file, copy the directory to the workspace directory, where the VANTED source resides. 

* Import the Add-on example project into the VANTED workspace.

## Using IntelliJ IDEA

1. Get the `vanted` and `vanted-lib-repository` repos (as of Oct. 2020, they can be found [on GitHub](https://github.com/LSI-UniKonstanz/))
2. Create a new IntelliJ project
3. Go to File → Project Structure, remove the default module that was created with the project
4. Set up a module for Vanted: Click `+` → Import Module, pick the `vanted` directory
    - If asked to use existing Eclipse project information, do that. Proceed with defaults.
5. Set up a module for the libraries
    1. Go to the Libraries tab (left sidebar)
    2. Click `+` → Java, pick the `vanted-lib-repository` directory
    3. In Modules → vanted → Dependencies, make sure that
        - `vanted-lib-repository` is listed and
        - that "Export" is checked

Notes:

- Tests can be included likewise
- On MacOS with SDK version 11, the class `PanScrollZoomDemo` had to be excluded from compilation because a required dependency could not be found (probably Windows-specific?) — In build error dialog, right-click and chose "Exclude from compile"
- After setup, the run configuration that is created automatically when launching a `main` method via the right-click menu should work. There might be issues with some packages but the auto-suggested fix (`—add-exports`) should work. This will modify `.idea/compiler.xml`.
- Any instance of Vanted (particularly these invoked through an IDE) will look for addon jars in `%appdata%\Roaming\VANTED\addons` and use these — don't get confused.

### Plugin Development in IntelliJ

- Plugins can be included as modules, like above (even multiple ones) — In Project Structure → Modules → [Addon Module] → Dependencies, add the `vanted` module to be a dependency for your plugin. Set the scope of the depency (rightmost column) to "Provided".

#### Loading multiple Plugins

1. Create a new module (e.g. "dev-utilities"). 
2. Add the modules of your plugins as depencies of this new module (as described above).
3. Copy/link the addon xml files to the `resources` directory.
4. Put the below code snippet somewhere

    ```java
    import de.ipk_gatersleben.ag_nw.graffiti.plugins.gui.webstart.Main;

    public class StartWithBothAddons {
        public static void main(String[] args) {
            Main.startVantedExt(args, new String[] {
                    "first-add-on.xml",
                    "second-add-on.xml"
            });
        }
    }
    ```

    Right-click into the method and select "Run..."

    Likewise, you can create a `main` method that runs VANTED with no addons (e.g. for testing your packaged JARs).

    ```java
    import de.ipk_gatersleben.ag_nw.graffiti.plugins.gui.webstart.Main;

    public class StartWithNoAddons {
        public static void main(String[] args) {
            Main.startVantedExt(args, new String[0]);
        }
    }
    ```

#### Building a JAR distributable

The way to go is 

1. Project Structure
2. Artifacts
3. Plus icon ( `+`) 
4. Jar 
5. From Module with Dependencies. You can leave everything to defaults. You do not need to set a Main Class in the subsequent dialog.

Take care that the name of the resulting jar file is the same as that of the plugin XML file. You can change the name of the resulting jar by right-clicking on the corresponding entry in the tab "Output layout".

To install the plugin, you can drag&drop it into the VANTED main window. This will copy the jar file to `%appdata%\Roaming\VANTED\addons` and then load that file. Don't get confused about which is used.
