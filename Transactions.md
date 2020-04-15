# Transactions

Suppose we have a network consisting of several hundreds or thousands of nodes and edges and your algorithm is changing many of the attributes of those elements, e.g. layout, adding new information, changing visual properties based on some calculations.

If you would update each attribute of each graph element, each time the graphical component of that graph element has to be updated and a `repaint()` call is issued to show the user the updated view.

For large amounts of changes this would slow down the update and drawing process.

To avoid that, VANTED has a transaction API that collects all the changes to the attributes, combines attribute changes and sends out the changes in one block to a transaction listener, which is usually also the view the graph is displayed in.

Also, if you use transactions, VANTED will make sure, that the graphical updates are executed on the AWT thread.

### Creating a transaction

To create a transaction you need to tell a listener manager, that you are going to initiate a new transaction. This manager is always present in the `graph` object, that is made available during the `attach()` lifecycle. (see [[Algorithm Development|DevelopAlgorithm]])

```java
...
graph.getListenerManager().transactionStarted(this);
...
```
The `this` gives the transaction manager a reference to whom started the transaction.

To finish it, just call
```java
...
graph.getListenerManager().transactionFinished(this);
...
```

After this call, all transaction listeners (usually also all views that draw graphs) are called and they handle the actual graphical updates of all graph elements in one go.

#### Example

This example will implement a simple algorithm to move all nodes by 10 pixels in *x* and *y* direction.

We also make use of some helper methods implemented in `AbstractAlgorithm` as well as some API calls from the `AttributeHelper` (see [[API Overview|APIOverviewExamples]])


```java
import org.graffiti.graph.Node;
import org.graffiti.plugin.algorithm.AbstractAlgorithm;
import org.graffiti.plugin.algorithm.PreconditionException;

public class Grid extends AbstractAlgorithm {
	
	@Override
	public String getName() {
		return "My ALgorithm";
	}
	
	@Override
	public void execute() {
		// this helper method will return a list of either all available nodes
		// or only nodes that are selected by the user
		Collection<Node> workNodes = getSelectedOrAllNodes();
		// execute with try-finally to make sure, that the transaction is finished
		// even if the algorithm should fail
		try {
			// start a new transaction
			graph.getListenerManager().transactionStarted(this);
			for(Node node : workNodes) {
				// read current position using API
				Point2D position = AttributeHelper.getPosition(node);
				
				//update position object
				position.setLocation(position.getX() + 10, position.getY() + 10);
				
				//set new position to the current node
				AttributeHelper.setPosition(node, position);
			}
		} finally {
			// finish our transaction
			graph.getListenerManager().transactionFinished(this);
	}
	
	@Override
	public String getDescription() {
		return "My Grid Layout";
	}
	
	@Override
	public void check() throws PreconditionException {
		// we can only work, if the graph is not empty
		if (this.graph.getNodes().isEmpty())
			throw new PreconditionException("Need non-empty graph");
	}
	
	@Override
	public void reset() {
		// reset internal variables
	}
	
	@Override
	public String getCategory() {
		return "Network";
	}

}
```

After executing the algorithm, all nodes will appear further down right.