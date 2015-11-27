# Write a new algorithm

Algorithm extend the functionality of VANTED. They can be called be clicking on a menu-item or on a button in the button-bar or by manually executing them in the frame of the side-tab UI.

Using parameters algorithms can be configured to match the task in hand. 

Before an algorithm is executed, a check can be performed to see if the algorithm is able to perform on the given graph or a given selection of graph elements. If it cannot execute it can throw an exception to inform the user.

Algorithms must either implement the `org.graffiti.plugin.algorithm.Algorithm` interface or simply extend `org.graffiti.plugin.algorithm.AbstractEditorAlgorithm` which implements already most of the methods in the interface. They are primarily supposed to work with a graph, who's reference is given to the algorithm before it is executed.

You should however override or implement at least the following methods.

* `public String getName()` to give the algorithm a name in VANTED
* `public void execute()` which gets called, when the algorithm is called in VANTED 
* `public String getDescription()` a textual (or HTML) description of the algorithm
* `public void check() throws PreconditionException` a check method, if the algorithm will work with the given graph
* `public void reset()` to reset the algorithm to a defined initial state.
* `public String getCategory()` the Menu category. A '.'-seperated string giving the menu-hierarchy this algorithm will be embedded in.

and if you need support for parameters

* `public Parameter[] getParameters();`
* `public void setParameters(Parameter[] params);`

### A simple algorithm

An example of such an algorithm would be:
```java
import org.graffiti.graph.Node;
import org.graffiti.plugin.algorithm.AbstractAlgorithm;
import org.graffiti.plugin.algorithm.PreconditionException;

public class MyAlgorithm extends AbstractAlgorithm {
	
	@Override
	public String getName() {
		return "My ALgorithm";
	}
	
	@Override
	public void execute() {
		// if there is a selection, work only on selected graph elements
		// which can be nodes and edges
		// if there is no selection, work on all graph elements
		Collection<GraphElement> selectedOrAllGraphElements = getSelectedOrAllGraphElements();
		
		// if there is a selection, work only on selected graph nodes
		// which can be nodes only 
		// if there is no selection, work on all graph nodes
		Collection<Node> selectedOrAllNodes = getSelectedOrAllNodes();
	}
	
	@Override
	public String getDescription() {
		return "Some description of the algorithm";
	}
	
	@Override
	public void check() throws PreconditionException {
		if (this.graph.getNodes().isEmpty())
			throw new PreconditionException("Need non-empty graph");
	}
	
	@Override
	public void reset() {
		// reset internal variables
	}
	
	@Override
	public String getCategory() {
		return null;
	}

}
```

Algorithms are usually executed using the API call `GravistoService.getInstance().runAlgorithm(myAlgo, myActionEvent)`. Usually an action event is not needed and can be null.

If an algorithm is executed using the above API call it lives through a small lifecycle, which means certain methods are called in a defined order

1. attach()
	* with that call, the current active graph and a possible selection containig graph elements (nodes, edges) are provided to the algorithm
2. setActionEvent()
	* a possible action event is provided
3. getParameters()
	* an array of 'Parameter' objects can be returned by the algorithm, if it is configurable
4. setParameters()
	* an array of the given Parameters is returned in the same order they were given with probably new values set
5. check()
	* this is the place where the algorithm can check all the conditions to run or fail
6. execute()
	* finally the algorithm is executed

### Add the algorithm to the menu

To add an algorithm to the menu and make it directly accessible to the user, you can set a category your algorithm will fit into.

This category will reflect the hierarchy as seen in the menus. E.g. your algorithm does something on the network its cateogory would be `"Network"`. If it is not only network but also something special e.g. hierarchy, then the category would be `"Network.Hierarchy"`

This category is only the level your algorithm will reside in. So if your algorithm is named "My Hierarchy Algorithm" it will appear in the menu under 'Network' -> 'Hierarchy' as entry 'My Hierarchy Algorithm'.

To tell VANTED where to put your algorithm simple return your category as a period separated string in the method `public String getCategory()`

```java	
...
	@Override
	public String getCategory() {
		return "Network.Hierarchy";
	}
...
```
	
### Notes

#### Background Execution

When using the API call to execute the algorithm,  is executed in the current thread. Which means it is usually executed from the AWT thread. If you implement a computational demanding algorithm use the provided [Background thread API](BackGroundAPI.md)

#### Transactions

If an algorithm changes attributes of nodes and edges as a result of some computation, it should use the [Transaction API](Transactions.md) 

See [API Overview and Examples](APIOverviewExamples.md) to get more information of how to access graph elements and data that this algorithm could work on.
