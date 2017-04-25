# Executing background threads

If you execute an algorithm or any kind of task that takes longer and can block the user interface, it is better to move this code to an extra thread.

VANTED supports those tasks by providing the `BackgroundTaskHelper`.

It not only executes the time-consuming task on an extra thread, but can also execute code, that updates the user interface on the AWT thread, after finishing the calculations. It also supports a callback interface, where continuous updates (progress bar, status messages) during algorithm execution can be messaged and displayed to the user. 

The `BackgroundTaskHelper` has multiple different methods to call, depending on the complexity of the task you want to execute.

### Executing simple [background-task / ui-update-task] pair

The most common method would be `public static void issueSimpleTask(String taskName, String progressText, Runnable backgroundTask1, Runnable finishSwingTask)` where a background task, a task that executes after the background task is finished, as well as some information about this task are given.


```java
BackgroundTaskHelper.issueSimpleTask(
		"My Task", 
		"My progress text", 
		new Runnable() {
			
			@Override
			public void run() {
				// Task with a lot of work to do
			}
		}, new Runnable() {
			
			@Override
			public void run() {
				// Updates of UI elements (tables, lists, graphview)
			}
		});
```

And that's it. The tasks will be executed instantly and if the task takes longer than 100ms a status message box will appear in the lower right corner to inform the user that a task is executing in the background.

### Executing simple background-task / ui-update-task pair with progress

If your algorithm can provide updates during its long running task, it can implement the interface `BackgroundTaskStatusProvider`, which contains several methods that are queried periodically to show update messages or the value for the progress bar.

The `BackgroundTaskStatusProvider` interface also enables the user to send a *cancel* event to the background task. It is the responsibility of the task to check for an abort call and act accordingly.

If you have just a custom background task and want to show some progress, you can use the `BackgroundTaskStatusProviderSupportingExternalCall` as shown in the example below

```java
final BackgroundTaskStatusProviderSupportingExternalCall status = 
		new BackgroundTaskStatusProviderSupportingExternalCallImpl(
					"short Text", "long text");

Runnable myLongRunningTask = new Runnable() {
	public void run() {
		System.out.println(0 + " seconds computed");
		status.setCurrentStatusValue(0);
		for (int i = 1; i < 6; i++) {
			try {
				// some long running task
				Thread.sleep(1000);
				System.out.println(i + " seconds computed");
				
				// update the status value.
				// this will be seen as progress by the user
				status.setCurrentStatusValue(20 * i);

				// if the user presses the stop-button you can react on
				// that (here we just stop working)
				if (status.wantsToStop())
					return;
			} catch (InterruptedException e) {
				e.printStackTrace();
			}
		}
	}
};

// Start the background task
BackgroundTaskHelper.issueSimpleTask(
		"My Task", 
		"My progress text", 
		myLongRunningTask, 
		new Runnable() {
			
			@Override
			public void run() {
				// Updates of UI elements (tables, lists, graphview)
			}
		});
```

If there are no user interface updates afterwards, the second `Runnable` can also be null

```java
// Start the background task without updating the user interface afterwards
BackgroundTaskHelper.issueSimpleTask(
		"My Task", 
		"My progress text", 
		myLongRunningTask, 
		null
);
```