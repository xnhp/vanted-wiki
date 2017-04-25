## Create a new Tab for user interaction

Tabs are places, where you can add a custom user interface that provides visible custom information or actions of your Add-on.

Each tab has to be instantiated and added to the tab-array field in the entry-class (see above).

Each tab must extend the `org.graffiti.plugin.inspector.InspectorTab` class.

The following example shows the minimum implementation
```java
import org.graffiti.plugin.inspector.InspectorTab;
import org.graffiti.plugin.view.View;

public class MyTab extends InspectorTab {
	/**
	 * You can selectively show your tabs for compatible views
	 * If this tab should always appear just return true
	 * Else check the class type of the view 
	 */
	@Override
	public boolean visibleForView(View v) {
		return true;
	}

	/**
	 * The title of your tab. 
	 * This will appear as string in the tab header
	 */
	@Override
	public String getTitle() {
		return super.getTitle();
	}

	/**
	 * A '.'-separated string giving the parent-tab hierarchy,
	 * where this tab will appear
	 * E.g. this tab provides some interface for networks
	 * return "Network"
	 */
	@Override
	public String getTabParentPath() {
		return super.getTabParentPath();
	}
}
```

Since the `InspectorTab` is also an extension of `JPanel`, you can just create the UI-content in a separate method or in the constructor.

If you want to show information regarding the selection of graph elements or any changes in the session state (e.g. selection of a different graph frame), you have to implement the appropriate listeners.

The most important listeners would be

* `org.graffiti.selection.SelectionListener` to listen to selection events.
* `org.graffiti.session.SessionListener` to listen to session change events.

If you implement the `SelectionListener` you have two methods, where the first is the more useful.

```java
...
	@Override
	public void selectionChanged(SelectionEvent e) {
		// the SelectionEvent e contains the selection 
		Selection selection = e.getSelection();
		// And the selection contains the selected graph elements
		selection.getNodes();
		selection.getEdges();
	}

	@Override
	public void selectionListChanged(SelectionEvent e) {	}
...
```
The first method is always called when a selection change happens.

If you implement the `SessionListener`, you can change the content of the tab according to the new graph being displayed in the active session context.

```java
...
	@Override
	public void sessionChanged(Session s) {
		// get the new graph from the new active session
		Graph graph = s.getGraph();
	}

	@Override
	public void sessionDataChanged(Session s) {
	}
...
```