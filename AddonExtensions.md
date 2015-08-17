# Add new Algorithms, Views, and Visual Graph Components

Add-on can extend the functionality of VANTED by providing new algorithms (Graphical, Computational, etc), views (graphical display of graph or other information), custom visual components for graph drawing, tools and more.

All those extensions extend certain classes to be recognized by VANTED as such.

To extend the functionality, you add the extension to provided array fields, that are present in the main entry class of the Add-on. (see [Project Structure](ProjectStructure.md) for details.
This entry class must extend `de.ipk_gatersleben.ag_nw.graffiti.plugins.addons.AddonAdapter` to have the necessary fields.

The entry class could look like this:

```
public class MyAddon extends AddonAdapter {
	
	/**
	 * This class will automatically start all implemented Algorithms, views and
	 * other extensions written in your Add-on. 
	 */
	@Override
	protected void initializeAddon() {
		// registers user interface tabs in the sidepanel, on which you may add
		// any Jcomponent you want to
		this.tabs = new InspectorTab[] { 
			new MyTab() 
		};
		
		// registers a number of algorithms which can manipulate graphs (and
		// views, if they are editoralgorithms)
		this.algorithms = new Algorithm[] {
			new MyAlgorithm()
		};
		
		// registers a number of views, which can be created from code or via
		// File->New View (you can also decide not to register, but just to create
		// such views from code.
		this.views = new String[] { "org.vanted.addon.exampleaddon.MyFirstView" };
		
		...
		
	}
	...
}
```

## Write a new algorithm

Algorithms must either implement the `org.graffiti.plugin.algorithm.Algorithm` interface or simply extend `org.graffiti.plugin.algorithm.AbstractEditorAlgorithm` which implements already most of the methods in the interface. They are primarily supposed to work with a graph, who's reference is given to the algorithm before it is executed.

You should however override or implement at least the following methods.

* `public String getName()` to give the algorithm a name in VANTED
* `public void execute()` which gets called, when the algorithm is called in VANTED 
* `public String getDescription()` a textual (or HTML) description of the algorithm
* `public void check() throws PreconditionException` a check method, if the algorithm will work with the given graph
* `public void reset()` to reset the algorithm to a defined initial state.
* `public String getCategory()` the Menu category. A '.'-seperated string giving the menu-hierarchy this algorithm will be embedded in.

An example of such an algorithm would be:
```

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
		return "Network.Special Algorithms";
	}

}
```

See [API](API.md) to get more information of how to access graph elements and data that this algorithm could work on.