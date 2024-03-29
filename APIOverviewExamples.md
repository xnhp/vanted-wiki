# API Overview and Examples

If you are developing a new algorithm for VANTED (see [[Add-on development|AddonDevelopment]]), you will most likely want to add/change/delete attributes of graph elements.

To help with the access of graph elements, their attributes, and their containing data several helper classes and methods exist to provide an API to the developer.

Since most of the work is done on and with graph elements, the next paragraph will give an overview about general methods retrieving graph elements.

### Access to graph and its elements

Usually, if you are implementing an algorithm, a reference to the graph will be given before the algorithm is executed. See [[Add new Algorithms, Views, and Visual Graph Components|AddonExtensions]] for details.

You can also get access to the graph elements by retrieving a **Session**-object, which is an object containing the reference to the graph, the view that draws the graph, etc.
Every opened document in VANTED has its own session. Only one of those sessions can be active, which is usually the one, the user is currently interacting with.

You can get the graph from the active session by calling a method from the MainFrame class.

```
MainFrame.getInstance().getActiveEditorSession().getGraph()
```
First you retrieve the object instance of the MainFrame class. This object is a singleton class. 

Then you retrieve the active session and then the graph from that session.

To access the node and edge elements of that graph you can simply call the getter of the Graph object
```java
...
import org.graffiti.graph.Graph;
import org.graffiti.graph.Node;
import org.graffiti.graph.Edge;
...

Graph graph = MainFrame.getInstance().getActiveEditorSession().getGraph();
List<Node> nodes = graph.getNodes();
Collection<Edge> edges = graph.getEdges();
```
Make sure that the correct `Node` and `Edge` Classes are imported, since VANTED is not the only one using such names for graph elements.

## Helper Classes

If you're very curious about what helper classes exist, you can list them using Eclipse. They all implement the `org.HelperClass` interface. Open the type hierarchy of that class and you would see all implementing classes.

The following paragraphs will give an overview about the mostly used helper classes.

### AttributeHelper

The `org.AttributeHelper` provides most of the necessary methods to access graph element attributes.

It provides necessary setter and getter methods for the most important graphical attributes of graph elements such as nodes and edges.

To get the label, change it, and set the new label of a node element you can use:

```java
// node is reference to graph node
// defaultReturn is return value, if node has no label

String defaultReturn = null;
String label = AttributeHelper.getLabel(node, defaultReturn);

String newLabel = label + " new";

// set the new label

AttributeHelper.setlabel(node, newLabel);
```

To get or set the location of a node you call
```java

// get location

Point2D position = AttributeHelper.getPosition(node);

// change location

position.setLocation(position.getX() + 10, position.getY() + 10);

// set new location

AttributeHelper.setPosition(node, position);
```

Information on the structure/layout of graph element attributes can be found on [this page](AttributeStructure)

## API calls not found in HelperClass hierarchy
There are useful methods even outside the HelperClass hierarchy. The most important would be loading of graph files and creation of sessions. For the complete reference, see the [Vanted API](http://kim25.wwwdns.kim.uni-konstanz.de/vanted/javadoc/).

To create a new view to display a graph you can call:
```java
File file = <path to graph file>
MainFrame.getInstance().loadGraph(file);
```
This call will take care of loading the graph and creating a session (see above) and creating a view to display the graph. 

Supported file formats are: `GML, GraphML, SBML, SBGN-PD, ...` to name the most important ones.
