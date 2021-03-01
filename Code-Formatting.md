VANTED source code is formatted using a specific formatter, this can be found in the root directory of the vanted repository under the name of _save_action_format.xml_. To set up the formatter, perform the following steps in Eclipse:

## Window &rarr; Preferences &rarr; Java &rarr; Editor &rarr; Save Actions
* Tick "Perform the selected actions on save".
* Tick "Format source code".
* Select "Format all lines".
* Click "Formatter".
* Lastly, import the file _save_action_format.xml_ and make sure the profile VANTED_SAVE_ACTION_FORMAT in "Formatter" is activated.

#### If you have already started development, perform formatting once for all classes in Eclipse:
* Select the package in the Package Explorer.
* Source &rarr; Format.