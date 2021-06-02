# Extending the Experiment loading capabilities

This document will show you how to extend certain parts of the experiment handling code.

It includes examples for new document type readers as well as action buttons, that perform actions after the experiment has been loaded.

## Implementing a new experiment document reader

If you develop a new document reader, you can directly extend the `ExperimentDataDragAndDropHandler` class and implement the only method in there.

```java
class MyExperimentDataDragAndDropHandler

	ExperimentDataPresenter receiver;
	
	public void setExperimentDataReceiver(ExperimentDataPresenter receiver) {
		this.receiver = receiver;
	}

	/*
	 * is called, after 'canProcess' has been called a returned
	 * true, meaning this handler can deal with this file type
	 */
	public boolean process(List<File> files) {
		// your parsing code for the file to create a new
		// experiment
		// if more should happen you have to call the receiver
		
		receiver.processReceivedData(TableData td, String experimentName, ExperimentInterface doc, JComponent gui);
	}
	
	/**
	 * @param f
	 *           Input file to be analyzed for compatibility.
	 * @return True, if this handler might handle the input file. E.g. based on
	 *         the file extension.
	 */
	public boolean canProcess(File f){
		...
	}
	
	/**
	 * @return True, if the handler should be called before other handlers
	 *         are executed, which return False.
	 */
	public boolean hasPriority() {
		return false;
	}
}
```

You then have to register this drag'n drop handler to Vanted by calling the method below. This is usually done in your Addon startup code. (see [[Add-on development|AddonDevelopment]])

```Java
	GravistoMainHelper.addDragAndDropHandler(new MyExperimentDataDragAndDropHandler());
```

## Implementing a new Action Button for read experiment data

Usually after an experiment file has been read and you didn't override or ignore the receiver object, a dialog box is shown to ask the user, what he wants to do with the loaded data.

![](images/ExperimentReceiverDialog.jpg)

You can add your own action button to this list. They are also extending the `Algorithm` interface and get executed, if clicked on.

```Java
class MyAbstractExperimentDataProcessor {
	
	private ExperimentInterface md;
	...
	
	/*
	 * This is called by Vanted to set the experiment data
	 */
	@Override
	public void setExperimentData(ExperimentInterface md) {
		this.md = md;
	}
	/*
	 * This method is called after 'setExperimentData'.
	 * Here you can do whatever you want with the experiment data
	 */
	@Override
	public void processData() {
		...
	}

}
```

Finally, you have to register this processor with VANTED by calling the method below. This is usually done in your Add-on startup code. (see [[Add-on development|AddonDevelopment]])

```Java 			
ExperimentDataProcessingManager.addExperimentDataProcessor(new PutIntoSidePanel());
```

## Loading data from an Excel file with custom layout

VANTED already supports loading and parsing experiment data from Excel files. The usual workflow is as follows:

1. Open a network view
2. Using the "Experiments" sidebar tab....
    1. pick one or several excel files in the shape of some template
    2. (pick data to be mapped)
    3. trigger data mapping procedure. The experiment data is then mapped onto the elements of the currently viewed network.

The entry point for code is the `TabDBE` class, most notably `TabDBE.loadExcelOrBinaryFiles`. The basic scheme is that files are picked using `OpenExcelFileDialogService.getExcelOrBinaryFiles`, which are then handed to `ExperimentLoader.loadFile`. This method takes an `ExperimentDataPresenter` as a parameter, which essentially can be seen as a callback method for handling the data that was read. In other words, the `ExperimentDataPresenter` (presenter) is the adapter between file loading and actually doing something with the file contents.

The next central component, often (but not necessarily) called from a presenter, in the pipeline is the `ExperimentDataProcessingManager` (processing manager, for brevity). The basic idea is that different `ExperimentDataProcessor`s (processors) with different functionality can be written and then registered with the processing manager. For instance, there are two existing processors in VANTED: `DataMapping` and `PutIntoSidePanel`.

The processing manager handles executing registered and applicable processors, and error and progress reporting. It is also capable of triggering a single, specific processor.


### Import from Excel via template

`ExperimentLoader.loadFile` can load an excel file in the shape of the [template](https://www.cls.uni-konstanz.de/software/vanted/input-formats/) and returns, amongst other things, an object of type `ExperimentInterface` which holds the experiment data. Its structure looks as follows:

- Experiment*,* contains several...
- Substances, contains several...
- Conditions
- Samples
- Measurements

Utility methods like e.g. computing an average over all replica measurements are already provided by these classes. 

### Import from Excel with custom layout

**Use case**: We want to exploit given functionality for parsing an Excel file as much as possible. However, we are given an Excel file that does not adhere to the shape of the template and thus want to define our own logic how an `Experiment` object (used within Vanted to represent experiment data) is constructed from our Excel file.

1. Obtain a representation of the excel data as `TableData`. This functionality is already implemented statically and returns a representation of the cells in the excel file.

    ```java
    final TableData **myData** = ExperimentDataFileReader.getExcelTableData(excelOrBinaryFile, null);
    ```

2. Create a subclass of `ExperimentDataFileReader` and implement the `getXMLDataFromExcelTable` 

    ```java
    ExperimentDataFileReader **myReader** = new MyReader();
    ```

    ```java
    public class MyReader extends ExperimentDataFileReader {
    	// ...
    	public ExperimentInterface getXMLDataFromExcelTable(File excelFile, TableData td, BackgroundTaskStatusProviderSupportingExternalCall statusProvider) {
    	  // access the TableData object and construct and return an ExperimentInterface object
    	}
    }
    ```

3. Then trigger the loading routine of `ExperimentLoader` (offers async, error checks, callbacks, ...) and, in the callback, call a receiver a.k.a. `ExperimentDataPresenter` to further handle the data (e.g. perform data mapping).

    ```java
    loadExcelFileWithBackGroundService(**myReader**, **myData**, excelOrBinaryFile,
        new RunnableWithXMLexperimentData() {
          private ExperimentInterface md = null;

          /**
            * <code>setExperimentData</code> will be automatically called before this
            * method is called. This is a two step solution as the loading of the data is
            * done in background.
            */
          public void run() {
            **myReceiver**.processReceivedData(myData, experimentName, md, null);
          }

          public void setExperimenData(ExperimentInterface md) {
            this.md = md;
          }
    });
    ```

4. Note that in the callback, the experiment data in the shape of `ExperimentInterface` is available, *as well as* the table data of the excel file!

### Data Mapping

In VANTED, mapping data from experiment data onto a network is implemented in the `DataMapping` class. The basic scheme is as follows: 

1. Collect the possible matching IDs for each graph element (an element can have more than one identifier)
2. Iterate over substances: 
    1. iterate over matching graph elements
        1. upsert a collection attribute to the graph element. (In VANTED, graph elements can hold a hierarchical structure of attributes which hold label, position, etc. but also experiment data).
        2. add XML data to collection attribute
        3. set node component type to the specified diagram style
