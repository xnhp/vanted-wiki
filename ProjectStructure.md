# Project structure and compilation

The following project structure shows the the minimum mandatory files to create an Addon. The downloaded example contains a lot more files but most of them are not mandatory.

```
|   .classpath
|   .project
|   createAdd-on.xml
|
+---src
|   \---main
|       +---java
|       |   \---org
|       |       \---vanted
|       |           \---addons
|       |               \---exampleaddon
|       |                   |   ExemplaryAddon.java
|       |                   |   StartVantedWithAddon.java
|       |
|       \---resources
|           |   Add-on-Example.xml
|
+---target
    \---classes
|
\---lib	

```
The main structure is taken from Maven. 

* Java source files should be created in `src/main/java`
* additional resources such as images, xml files, etc. should be put under `src/main/resources` 
* Java compiled classes end up in `target/classes`
* additional JAR libraries reside in the `lib` directory

The root directory contains the following files

* The `.classpath` and `.project` files are Eclipse project files.
* `createAdd-on.xml` is an Ant script to create the distributable JAR file, that can be loaded in VANTED

The following files are mandatory for an Add-on

* `ExemplaryAddon.java` is the entry point for VANTED for the Add-on. This class must extend `AddonAdapter` to be recognized. The name of the class is not important. It will be defined in the XML-descriptor file below
* `StartVantedWithAddon.java` is a helper class to start VANTED from the IDE with the Add-on, without having the JAR installed.
* `Add-on-Example.xml` is the descriptor file for the Add-on. Details below.

## Descriptor file

As shown above, the `Add-on-Example.xml` is the descriptor file of the Add-on. It must have the same name as the distributed JAR file _and_ must reside in the root of the resource directory.

E.g. you have an Add-on that you distribute as `MyAddon.jar`, you have to have a `MyAddon.xml` in the resource directory.

The descriptor file is an XML document having the following structure.
```
<?xml version="1.0" encoding="ISO-8859-1" ?>

<!DOCTYPE plugin
  PUBLIC "-//GRAFFITI/DTD Plugin Descriptin 1.0//EN"
 "http://www.graffiti.org/dtd/1.0/plugin.dtd">

<plugin>
    <author>Your Name</author>
    <description>Put here your description of the Add-on</description>
    <plugindesc>
        <name>Name of your Addon</name>
        <main>org.vanted.addons.exampleaddon.ExemplaryAddon</main>
        <version>2.0</version>
        <feedname>Example-Add-on Changelog</feedname>
        <feedurl>https://immersive-analytics.infotech.monash.edu/vanted/addons/example/rss-changelog.xml</feedurl>
        <compatibility>2.5.0</compatibility>
        <available>https://bitbucket.org/vanted-dev/vanted-addon-example/</available>
    </plugindesc>
</plugin>
```

* `<author>` contains the names of the authors of the Add-on
* `<description>` contains a description of the functionality of the Add-on
* `<name>` is the actual name of the Add-on, which can contain spaces
* `<main>` point to the Java class that extends `AddonAdapter` - the entry point (start-up class)
* `<version>` the version of the Add-on
* `<feedname>` (optional) The name of the entry in the feed reader
* `<feedurl>` (optional) The location of the feed
* `<compatibility>` contains the version of VANTED this Add-on is compatible with. 
* `<available>` (optional) Website location of the source or wiki of the Add-on

## Start the Add-on in VANTED

To execute VANTED with the Add-on, without installing the distributable JAR, execute the helper class
`StartVantedWithAddon.java`, which looks like this.

```
package org.vanted.addons.exampleaddon;

import de.ipk_gatersleben.ag_nw.graffiti.plugins.gui.webstart.Main;

public class StartVantedWithAddon {

	public static void main(String[] args) {
		System.out.println("Starting VANTED with Add-on " + getAddonName()
							+ " for development...");
		Main.startVanted(args, "Add-on-Example.xml");
	}
}
```

To start your Add-on just replace `"Add-on-Example.xml"` with the name of your descriptor file.

## Create distributable JAR

To create the distributable JAR you use the `createAdd-on.xml` Ant script in the root directory of the project.
If executed it will create a fat-JAR containing the Java classes, resources and all additional JAR files, that were found in the `lib` directory.

As a standard configuration the Ant script will also include the Java source code.

This shows the minimal script for creating an Add-on

```
<?xml version="1.0" encoding="UTF-8" standalone="no"?>
<project default="create_run_jar" name="Create Examplary Addon for VANTED">
    <target name="create_run_jar">
      <jar destfile="Add-on-Example.jar" filesetmanifest="mergewithoutmain">
      	<fileset dir="target/classes"/>
	      <fileset dir="src/main/java">
         	<include name="**/*.java"/>
         	<include name="**/*.xml"/>
         </fileset>
      	<!-- dont forget to include your libs using this command -->
      	<zipfileset excludes="META-INF/*.SF" src="./lib/*.jar"/>
       </jar>
    </target>
</project>
```

You have to edit the Ant script to match the resulting JAR file name to the descriptor file name by changing this part of the script:
```
<jar destfile="Add-on-Example.jar" ...
```




