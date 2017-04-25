# Add-on Development

VANTED is an extendible software. Additional functionality can be provided by Add-ons. These are libraries that can be added during runtime to provide new algorithms, user-interfaces and visual network components.

Add-ons can use the provided APIs to easily access and change attributes, setup and call algorithms, etc.

To start development it is a good idea to download the example-addon code from [here](files/vanted-addon-example.zip).

It is, as the VANTED project itself, already an eclipse project. After you have unzipped the file, copy the directory to the workspace directory, where the VANTED source resides. 

Import the Add-on example project into the VANTED workspace.

The following explains the most important parts of the directory structure, necessary files and some method calls of the VANTED API.

* [Project Structure and Compilation](ProjectStructure.md)

* [Adding new Algorithms, Views, and Visual Graph Components](AddonExtensions.md)