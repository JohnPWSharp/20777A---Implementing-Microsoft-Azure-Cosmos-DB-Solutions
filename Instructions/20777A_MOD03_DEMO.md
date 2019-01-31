# Module 3: Implementing Server-Side Operations

- [Module 3: Implementing Server-Side Operations](#module-3-implementing-server-side-operations)
  - [Lesson 1: Server-Side Programming with Cosmos DB](#lesson-1-server-side-programming-with-cosmos-db)
    - [Demo 1: Creating and Testing a User-Defined Function](#demo-1-creating-and-testing-a-user-defined-function)
      - [Preparation](#preparation)
      - [Task 1: Create a User-Defined Function](#task-1-create-a-user-defined-function)
      - [Task 2: Test the User Defined Function](#task-2-test-the-user-defined-function)
      - [Task 3: Delete the Temperatures collection](#task-3-delete-the-temperatures-collection)
  - [Lesson 2: Creating and Using Stored Procedures](#lesson-2-creating-and-using-stored-procedures)
    - [Demo 2: Creating and Running a Stored Procedure](#demo-2-creating-and-running-a-stored-procedure)
      - [Preparation](#preparation-1)
      - [Task 1: Create a Stored Procedure](#task-1-create-a-stored-procedure)
      - [Task 2: Test the Stored Procedure using the Azure Portal](#task-2-test-the-stored-procedure-using-the-azure-portal)
      - [Task 3: Run the Stored Procedure From a SQL API Application](#task-3-run-the-stored-procedure-from-a-sql-api-application)
  - [Lesson 3: Using Triggers to Maintain Data Integrity](#lesson-3-using-triggers-to-maintain-data-integrity)
    - [Demo 3: Using Triggers to Verify Data Integrity](#demo-3-using-triggers-to-verify-data-integrity)
      - [Preparation](#preparation-2)
      - [Task 1: Create a Pre-Trigger](#task-1-create-a-pre-trigger)
      - [Task 2: Test the Trigger using Azure DocumentDB Studio](#task-2-test-the-trigger-using-azure-documentdb-studio)
      - [Task 3: Invoke the Trigger From a SQL API Application](#task-3-invoke-the-trigger-from-a-sql-api-application)
      - [Task 4: Delete the Azure resources](#task-4-delete-the-azure-resources)
  
## Lesson 1: Server-Side Programming with Cosmos DB

### Demo 1: Creating and Testing a User-Defined Function

#### Preparation

Before starting this demo:

1.  Ensure that the **MT17B-WS2016-NAT** and **20777A-LON-DEV** virtual machines are running, and then log on to **20777A-LON-DEV** as **LON-DEV\\Administrator** with the password **Pa55w.rd**.

2.  On the toolbar, click **File Explorer**.

3.  In File Explorer, navigate to **E:\\Demofiles\\Mod03**, right-click **Setup.cmd**, and then click **Run as administrator**.

4.  On the toolbar, click **Internet Explorer**.

5.  In Internet Explorer, go to **http://portal.azure.com**, and sign in using the Microsoft account that is associated with your Azure Learning Pass subscription.

6.  In the Azure portal, in the left panel, click **Azure Cosmos DB**, and then click **+ Add**.

7.  On the **Azure Cosmos DB** blade, under the **Resource Group** box, click **Create new**, type **20777aMod3**, and then click **OK**.

8. In the **Account name** box, type **20777a-sql-\<*your name\>-\<the day*\>**, for example, **20777a-sql-john-31**.

9.  In the **API** drop-down list, note the options available, and then click **Core (SQL)**.

10. In the **Location** drop-down list, click the region closest to your current location, click **Review + create**, and then click **Create**.

11. Wait for the Azure Cosmos DB to be created—this could take a few minutes.

12. Click **Go to resource**.

13. On the **20777a-sql-\<*your name\>-\<the day*\>** blade, click **Data Explorer**, and then click **New Database**.

14. On the **New Database** blade, in the **Database id** box, type **DeviceData**, and then click **OK**.

15. In the **SQL API** pane, click **New Collection**.

16. On the **Add Collection** blade, click **Use existing**, and then in the drop-down list, click **DeviceData**.

17. In the **Collection Id** box, type **Temperatures**.

18. In the **Partition Key** box, type **/id**.

19. In the **Throughput (400 - 1,000,000 RU/s)** box change **400** to **5000**, and then click **OK**.

    > **Note**: Preparation steps 20 to 22 download and build the latest version of the Cosmos DB data migration tool. You do not need to carry out these steps if you completed it in an earlier module (and already have a **E:\\dmt\\bin** folder on **20777A-LON-DEV**); if you already completed these steps, skip ahead to step 22.

20. In File Explorer, navigate to **E:\\Resources**, right-click **build\_data\_migration\_tool.ps1**, and then click **Run with PowerShell**.

21. If the **Open File - Security Warning** dialog box appears, click **Open**.

22. Wait for the script to finish.

    > **Note**: preparation steps 23 to 27 load the collection you created with data, using the data migration tool.

23. In Internet Explorer, on the **20777a-sql-\<*your name\>-\<the day*\>** blade, under **Settings**, click **Keys**.

24. Make a note of the **PRIMARY CONNECTION STRING** value.

25. In File Explorer, navigate to **E:\\Demofiles\\Mod03\\Demo01**, right-click **Demo01-setup.ps1**, and then click **Run with PowerShell**.

26. In the PowerShell window, enter the **PRIMARY CONNECTION STRING** value, and then press Enter. The script loads data into the **Temperatures** collection, and will take several minutes to complete.

27. Wait for the script to finish, and then press Enter to close the PowerShell window.

#### Task 1: Create a User-Defined Function

**Scenario:** The documents in the Temperatures collection capture the time at which the reading was recorded in the **time** property, for maximum accuracy (1 tick = 100 nanoseconds). This is not very user-friendly, so you wish to provide a UDF that will convert ticks into a formatted date and time string.

1.  In Internet Explorer, on the **20777a-sql-\<*your name\>-\<the day*\>** blade, click **Data Explorer**.

2.  In the **SQL API** pane, right-click **Temperatures**, and then click **New UDF**.

3.  On the **New UDF 1** tab, in the **User Defined Function Id** box, type **getDateTime**.

4.  In the **User Defined Function Body** box, edit the contents so that it reads:

    ```javascript
    function getDateTime(ticks){
        /* Validate the input - must be a non-negative number */
        if(typeof ticks != 'number')
            throw new Error("Parameter must be numeric");

        if (ticks < 0)
            throw new Error("Parameter must be non-negative");

        /* Convert ticks into milliseconds (1 tick = 100 nanoseconds) */
        var millisecondTicks = ticks / 10000;

        /* Adjust the time - ticks are measured from 1st January 0001, whereas JavaScript Dates are measured from 1st January 1970
           So the value in tickDateTime is 1970 years fast! */
        const ticksBetweenYear1And1970 = 62135596800000;
        var adjustedTicks = millisecondTicks - ticksBetweenYear1And1970;

        /* Check the result is not negative. 
           If it is, then the input parameter represents a date before January 1st 1970, which is not supported by this UDF */

        if (adjustedTicks < 0)
            throw new Error("Parameter must represent a date on or after 1st January 1970");

        /* Create a Date object using the number of milliseconds */
        var tickDateTime = new Date(adjustedTicks);

        /* Return the Date as a readble string, formatted using the current locale */
        return tickDateTime.toLocaleString();
    }
    ```

    > **Note**: You can find the function definition in **E:\\Demofiles\\Mod03\\Demo01\\getDateTime.txt**.

5.  On the **New UDF 1** tab, click **Save**.

6.  Review the function code, which operates as follows, and then close the **getDateTime** tab:

    - The function accepts a single parameter, **ticks**; the parameter is validated to confirm that it is a positive number.

    - **ticks** is converted into milliseconds (division by 10000).

    - The milliseconds value is adjusted to compensate for the difference between the epoch for .Net dates (January 1, 0001) and the epoch for JavaScript dates (January 1, 1970). The resulting value is checked to confirm that it is greater than zero.

    - The milliseconds value is cast to a **JavaScript Date** object, and returned as a string formatted on the current locale.

#### Task 2: Test the User Defined Function

1.  On the **20777a-sql-\<*your name\>-\<the day*\>** blade, click **New SQL Query**.

2.  On the **Query 1** tab, delete the existing code, type the following code, and then click **Execute Query**:

    ```SQL
    SELECT c.time, udf.getDateTime(c.time) as DateTime FROM c
    ```

3.  Verify that the results look similar to the following extract (your date and time values will vary):

    ```JSON
    [
        {
            "time": 636645730015878700,
            "DateTime": "‎6‎/‎14‎/‎2018‎ ‎11‎:‎36‎:‎41‎ ‎AM"
        },
        {
            "time": 636645730015868700,
            "DateTime": "‎6‎/‎14‎/‎2018‎ ‎11‎:‎36‎:‎41‎ ‎AM"
        },
        {
            "time": 636645730015898600,
            "DateTime": "‎6‎/‎14‎/‎2018‎ ‎11‎:‎36‎:‎41‎ ‎AM"
        },
        {
            "time": 636645730015868700,
            "DateTime": "‎6‎/‎14‎/‎2018‎ ‎11‎:‎36‎:‎41‎ ‎AM"
        },
        {
            "time": 636645730015648600,
            "DateTime": "‎6‎/‎14‎/‎2018‎ ‎11‎:‎36‎:‎41‎ ‎AM"
        },
        {
            "time": 636645730015878700,
            "DateTime": "‎6‎/‎14‎/‎2018‎ ‎11‎:‎36‎:‎41‎ ‎AM"
        },
        ...
    ]
    ```

#### Task 3: Delete the Temperatures collection

1.  In the SQL API pane, right-click **Temperatures**, and then click **Delete Collection**.

2.  On the **Delete Collection** blade, in the **Confirm by typing the database id** box, type **Temperatures**, and then click **OK**.

3.  Leave Internet Explorer open for the next demonstration.

## Lesson 2: Creating and Using Stored Procedures

### Demo 2: Creating and Running a Stored Procedure

#### Preparation

This demonstration assumes that you have completed the previous demonstrations in this module.

1.  On the **20777a-sql-\<*your name\>-\<the day*\>** blade, in the **SQL API** pane, click **New Collection**.

2.  On the **Add Collection** blade, under **Database id**, click **Use existing**, and then in the drop-down box, click **DeviceData**.

3.  In the **Collection Id** box, type **Temperatures**.

4.  In the **Partition key** box, type **/deviceID**.

5.  In the **Throughput (400 - 50,000 RU/s)** box, type **10000**, and then click **OK**.

    > **Note**: Cosmos DB is case sensitive; the case of the partitioning key must match the case of the property in your documents.
    >
    > Preparation steps 6 and 7 download and build the latest version of the Cosmos DB data migration tool. You do not need to carry out these steps if you completed it in an earlier module (and already have a **E:\\dmt\\bin** folder on **20777A-LON-DEV**); if you already completed these steps, skip ahead to step 8.

6.  In File Explorer, navigate to **E:\\Resources**, right-click **build\_data\_migration\_tool.ps1**, and then click **Run with PowerShell**.

7.  Wait for the script to finish, and then press Enter.

    > **Note**: Preparation steps 8 to 15 load the collection you created with data, using the data migration tool.

8.  In Internet Explorer, on the **20777a-sql-\<*your name\>-\<the day*\>** blade, under **Settings**, click **Keys**.

9.  Make a note of the **PRIMARY CONNECTION STRING** value.

10. In File Explorer, navigate to **E:\\Demofiles\\Mod03\\Demo02**, right-click **Demo02-setup.ps1**, and then click **Run with PowerShell**.

11. In the PowerShell window, enter the **PRIMARY CONNECTION STRING** value, and then press Enter. The script loads data into the **Temperatures** collection, and will take several minutes to complete.

12. Wait for the script to finish, and then press Enter to close the PowerShell window.

13. In Internet Explorer, on the **20777a-sql-\<*your name\>-\<the day*\>** blade, click **Data Explorer**.

14. In the **SQL API** pane, expand **Temperatures**, and then click **Scale & Settings**.

15. On the **Scale & Settings** tab, in the **Throughput (400 - unlimited RU/s)** box, type **1500**, and then click **Save**.

#### Task 1: Create a Stored Procedure

**Scenario:** The documents in the **Temperatures** collection require some housekeeping; documents that have been processed should be removed. Cosmos DB does not provide a simple way to remove more than one document at a time. You have decided to write a stored procedure that will delete documents for a specified device that have a timestamp that precedes that of a user-specified date. If there are a large number of matching documents, the delete operation could exceed the runtime limits of a single request, so you must write the stored procedure in such a way that it can be gracefully halted if the available runtime is nearly over, and be restarted from its current position by a subsequent request.

1.  In the **SQL API** pane, right-click **Temperatures**, and then click **New Stored Procedure**.

2.  On the **New Stored Procedure 1** tab, in the **Stored Procedure Id** box, type **deleteOldDocs**.

3.  In the **Stored Procedure Body** box, edit the contents of box so that it reads:

    ```javascript
    function deleteOldDocs(deviceID, deleteBeforeDate) {
        var collection = getContext().getCollection();
        var collectionLink = collection.getSelfLink();
        var response = getContext().getResponse();

        //Convert the "Delete Before" date into the corresponding number of ticks
        var ticks = Date.parse(deleteBeforeDate);

        // Adjust for 1st Jan 1970
        const ticksBetweenYear1And1970 = 62135596800000;
        ticks += ticksBetweenYear1And1970

        // Convert ticks from ms to 100 ns
        ticks *= 10000;

        if (isNaN(ticks))
            throw Error('Invalid "delete before" date');

        // Find matching docs
        var docQuery = {
            query: "SELECT * FROM c WHERE c.deviceID = @deviceID AND c.time < @time",
            parameters: [
                { name: "@deviceID", value: deviceID },
                { name: "@time", value: ticks}
            ]
        };

        // Keep a running total of the number of documents removed
        var numDocsDeleted = 0;

        // Run the query to find and remove all matching docs
        // The query might take a long time to run (if there are a lot of docs), and the results could be paged (using a continuation token)
        tryFind();
    ```

    > **Note**: this code is incomplete—you will complete it in later steps in this demonstration. You can find the complete stored procedure definition in **E:\\Demofiles\\Mod03\\Demo02\\deleteOldDocs.txt**.

4.  Review the code that you added in the last step:

    - The stored procedure takes two parameters: **deviceID** (which identifies the partition to use), and **deleteBeforeDate**. All temperatures that have a date that precedes the **deleteBeforeDate** date for the specified **deviceID** will be removed.

    - Internally, the date recorded for each document is stored as a number of ticks, so the code has to convert **deleteBeforeDate** date into ticks and validate the result.

    - The stored procedure runs a query that finds all matching documents, and will then delete them one by one (using the **tryFind** function, defined in the next step). It keeps track of how many documents it has removed.

5.  In the **Stored Procedure Body** box, at the end of the stored procedure definition, add the following code:

    ```javascript
        function tryFind(continuationToken) {
            var options = { continuation: continuationToken }; // If a continuation taken was passed in, use it to continue paging through the query results

            // Run the query, and invoke the callback with the results
            var isAccepted = collection.queryDocuments(collectionLink, docQuery, options, tryFindCallback);

            // If time is up, return a response body that includes the count of documents deleted so far and a continuation token
            if (!isAccepted) {
                var responseBody = { deleted: numDocsDeleted, continuation: true};
                response.setBody(responseBody);
            }
        }
    ```

6.  Review the code that you added in the last step:

    - The **tryFind** function runs the query defined earlier, and invokes the **tryFindCallback** function (see next step) to handle the results and delete the documents that it finds. Note that the **tryFind** function checks that there is sufficient runtime left; if not, it returns a response containing the number of documents deleted so far, together with a continuation token containing a Boolean value (**true**). The client should read this token and understand that the operation was not completed.

7.  In the **Stored Procedure Body** box, at the end of the stored procedure definition, add the following code:

    ```javascript
        // Callback that runs when the query to find matching docs completes
        function tryFindCallback(err, docs, options) {

            // Handle any errors that might have occurred while running the query
            if (err)
                throw Error(err);

            // If there are still documents to delete (the query returned an array of one or more docs), then try and remove them
            else if (docs.length > 0)
                tryDelete(docs);

            // If the docs array is empty, but we have a continuation token, then continue querying to retrieve the next page of docs
            else if (options.continuation)
                tryFind(options.continuation);

            // If there are no more documents to delete, and no continuation token, then we are done
            else {
                var responseBody = { deleted: numDocsDeleted, continuation: false};
                response.setBody(responseBody);
            }
        }
    ```

8.  Review the code that you added in the last step:

    - The **tryFindCallback** function runs when the query performed by the **tryFind** function completes. The **tryFindCallback** function accepts an array containing the matching documents in the **docs** parameter.

    - If the **docs** array is not empty, it calls the **tryDelete** method to remove them (see next step). If the **docs** array is empty, the stored procedure hasn't necessarily finished; if there are a large number of documents, the query will return them in blocks (pages), and the response will contain a continuation token indicating that there is another page of results to come. In this case, the **tryFindCallback** function calls **tryFind** again, passing it the continuation token.

9.  In the **Stored Procedure Body** box, at the end of the stored procedure definition, add the following code:

    ```javascript
        // Function that deletes all documents listed in the array passed in as the parameter
        function tryDelete(docs) {

            // Attempt to delete the docs
            docs.forEach(doDelete);

            // Query and get the next page of docs to delete
            tryFind();
        }

        // Function that attempts to delete a doc, checking for runtime availability
        function doDelete(doc) {

            var isAccepted = collection.deleteDocument(doc._self, {}, doDeleteCallback);

            // If there is insufficient runtime available, pass back a continuation containing the number of docs deleted to the client
            if (!isAccepted) {
                var responseBody = { deleted: numDocsDeleted, continuation: true};
                response.setBody(responseBody);
            }
        }

        // When a document has been deleted, check for errors, and then increment numDocsDeleted
        function doDeleteCallback(err) {
            if (err)
                throw Error(err);

            numDocsDeleted++;
        }
    }
    ```

10. On the **New Stored Procedure 1** tab, click **Save**.

11. Review the code that you added in the last step:

    - The **tryDelete** function iterates through the array of documents and calls the **doDelete** function to remove each one. It then calls **tryFind** to establish whether there are any more matching documents.

    - The **doDelete** function removes a document if there is sufficient runtime available, or returns a response indicating the number of documents removed and the **true** continuation token back to the client of not.

    - The **doDeleteCallback** function, which runs after a document has been deleted, increments the count of the number of documents removed.

#### Task 2: Test the Stored Procedure using the Azure Portal

1.  On the **deleteOldDocs** tab, click **Execute**.

2.  On the **Input parameters** blade, in the **Partition key value** box, type **Device 1**. This value specifies the partition against which Cosmos DB will run the stored procedure.

3.  To specify a value for the first stored procedure parameter (**deviceID**), in the parameter box, type **Device 1**.

    > **Note**: The partition key and the first procedure parameter have the same value; this is a feature of the design of this demonstration; this situation will not apply to every stored procedure.

4.  To specify a value for the second stored procedure parameter (**deleteBeforeDate**), click **+ Add New Param**, in the new parameter box, type **20 June 2018**, and then click **Execute**. All documents for **Device 1** that have a date earlier than this value will be removed from the database.

5.  In the **Result** pane, the result returned by the stored procedure should look like:

    ```JSON
    {
        "deleted": 1400,
        "continuation": true
    }
    ```

    The value of the **deleted** property—that indicates the number of documents deleted—might be different from the example. The continuation token (**true**) indicates that the operation did not complete before it ran out of time, and there are more documents to remove.

6.  On the **deleteOldDocs** tab, click **Execute** to repeat the operation using the same parameters.

7.  On the **Input parameters** blade, click **Execute**. Wait for the stored procedure execution to complete, then review the results.

8.  Repeat steps 6 and 7 until the value of the **continuation** token in the response is **false** (meaning that all the documents meeting the search criteria have been removed, and no more remain to process).

#### Task 3: Run the Stored Procedure From a SQL API Application

1.  In File Explorer, navigate to **E:\\Demofiles\\Mod03\\Demo02\\DeleteDocs**, and then double-click **DeleteDocs.sln**.

2.  In the **How do you want to open this file?** dialog box, click **Visual Studio 2017**, and then click **OK**. Explain that this is a WPF app that enables the user to specify a device ID and date, and then remove matching documents from the collection.

3.  In Solution Explorer, double-click **App.config**.

4.  In Internet Explorer, on the **20777a-sql-\<*your name\>-\<the day*\>** blade, under **Settings**, click **Keys**.

5.  Make a note of the **URI**, and **PRIMARY KEY** values.

6.  In Visual Studio, in the **value** attribute of the **EndpointURL** key, paste the **URI** value replacing the text **your\_URI\_here**.

    ```XML
    <add key="EndpointUrl" value="your_URI_here" />
    ```

7.  In the **Value** attribute of the **PrimaryKey** key, paste the **PRIMARY KEY** value replacing the text **your\_PK\_here**.

    ```XML
    <add key="PrimaryKey" value="your_PK_here" />
    ```

8.  Confirm that the value of the **Database** key is **DeviceData** and that the value of the **Collection** key is **Temperatures**.

9.  Press F5 to run the application, and then wait for the application to start.

10. In the **Delete Documents** window, in the **Device** box, type **Device 2**.

11. In the **Select a date** box, type **20-Jun-18**, and then click **Delete**. The **Delete** button runs the stored procedure and displays the number of documents deleted. If the stored procedure returns a **true** continuation token, the **Delete More** button is enabled.

12. Click **Delete More** to delete the next block of documents.

13. Repeat step 12 until the number **Delete More** button is disabled.

14. Click **Delete**, and verify that no more documents are deleted. Note that the app also displays the number of RUs consumed by each invocation of the stored procedure.

15. When you have finished, close the **Delete Documents** window.

16. In Solution Explorer, expand **MainWindow.xaml**, and then double-click **MainWindow.xaml.cs**.

17. In MainWindow.xaml.cs, in the **Button\_Click** method (line 55), point out the following code:

    ```CSharp
    // Get the URI of the deleteOldDocs stored procedure
    Uri storedProcUri = UriFactory.CreateStoredProcedureUri(this.database, this.collection, this.storedProc);

    // Run the stored procedure - specify the partition identified by the deviceID value
    var options = new RequestOptions
    {
        PartitionKey = new PartitionKey(deviceID)
    };

    var storedProcResults = await this.client.ExecuteStoredProcedureAsync<DeleteOldDocsResponse>(storedProcUri, options, deviceID, date.Value.ToString("dd MMM yyyy"));
    ```

    This is the code that runs when the user clicks the **Delete** and **Delete More** buttons in the application.

    Explain that this is the code that runs the stored procedure. The response containing the number of documents deleted and the continuation token are returned in the **storedProcResults** variable. This variable is an object based on the **DeleteOldDocsResponse** class:

    ```CSharp
    class DeleteOldDocsResponse
    {
    [JsonProperty("deleted")]
    public int Deleted { get; set; }

    [JsonProperty("continuation")]
    public bool Continuation { get; set; }

    }
    ```

18. In the **Button\_Click** method, point out the remaining code:

    ```CSharp
    // Display the number of docs deleted
    this.numDeleted.Text = $"Documents Deleted: {storedProcResults.Response.Deleted}";

    // Display the cost (RUs) of running the stored procedure
    this.cost.Text = $"Cost: {storedProcResults.RequestCharge} RUs";

    // Check for a continuation token, and enable the "Delete More" button if necessary
    if (storedProcResults.Response.Continuation)
    {
        this.deleteMore.IsEnabled = true;
    }
    else
    {
        // Finished: Disable the "Delete More" button
        this.deleteMore.IsEnabled = false;
    }
    ```

    Explain that this code examines the response from the stored procedure. The **Deleted** property of the **Response** object contains the number of documents deleted, and the **Continuation** property is a Boolean value indicating whether there are more documents to be deleted, in which case the **Delete More** button is enabled (the **Delete More** button runs the same **Button\_Click** method as the **Delete** button). The cost of the operation is retrieved from the **RequestCharge** property of the response object and displayed.

19. When you have finished, close Visual Studio and leave Internet Explorer open for the next demonstration. You will use the **Temperatures** collection in the next demonstration.

## Lesson 3: Using Triggers to Maintain Data Integrity

### Demo 3: Using Triggers to Verify Data Integrity

#### Preparation

This demonstration assumes that you have completed the previous demonstrations in this module.

1.  In File Explorer, navigate to **E:\\Resources**, right-click **get\_document\_db\_studio.ps1**, and then click **Run with PowerShell**.

2.  If the **Open File - Security Warning** dialog box appears, click **Open**.

3.  Wait for the script to finish.

    > **Note**: This script downloads the open source Azure DocumentDB Studio application from GitHub, and extracts it to **E:\\DocumentDBStudio**.

#### Task 1: Create a Pre-Trigger

**Scenario:** You need to validate the **time** property (indicating the date and time expressed in .NET Framework Ticks—100 nanosecond intervals—since 1 January, 0001) for documents in the **Temperatures** collection as they are created or replaced. A valid **time** is one that is not ore than an hour old, or more than 10 seconds into the future (to allow for some clock skew) when compared to the server time. Additionally, if a document does not contain a **time** property, you need to add one with a default value of the current time. You decide to write a pre-trigger to perform this validation.

1.  In Internet Explorer, on the **20777a-sql-\<*your name\>-\<the day*\>** blade, click **Data Explorer**.

2.  In the **SQL API** pane, expand **DeviceData**, right-click **Temperatures**, and then click **New Trigger**.

3.  On the **New Trigger 1** tab, in the **Trigger Id** box, type **validateTime**.

4.  In the **Trigger Type** list, click **Pre**.

5.  In the **Trigger Operation** list, click **All**.

6.  In the **Trigger Body** box, edit the value so that it reads:

    ```javascript
    function validateTime() { 
        var context = getContext();  
        var request = context.getRequest();  
        var documentBeingCreated = request.getBody();

        // If the operation being performed is not a create or replace, then ignore this trigger
        if (request.getOperationType() == "Delete") {
            return;
        }
    ```

    This code retrieves the document that is being added (as a JSON object) from the incoming request. Before proceeding any further, the trigger examines the request to determine whether the user is deleting the document, in which case no validation is required.

    > **Note**: this code is incomplete—you will complete it in later steps in this demonstration. You can find the complete trigger definition in **E:\\Demofiles\\Mod03\\Demo03\\validateTime.txt**.

7.  In the **Trigger Body** box, at the end of the code, add the following code:

    ```javascript
        // Get the current time
        var now = new Date().getTime();

        // Adjust for 1st Jan 1970
        const ticksBetweenYear1And1970 = 62135596800000;
        now += ticksBetweenYear1And1970

        // Convert the value in now from ms to 100 ns
        now *= 10000;

        // Verify that the document contains a ticks property
        if (!("ticks" in documentBeingCreated)) {
            // If there is no ticks property, add it and set it to the current time
            documentBeingCreated["ticks"] = now;
            request.setBody(documentBeingCreated);
    ```

    This block of code obtains the current time and adjusts it to match a .NET Framework Ticks value. The code then examines the incoming document to establish whether it contains the **ticks** property. If not, then the property is added, with the current time as its value.

8.  In the **Trigger Body** box, at the end of the code, add the following code:

    ```javascript
        } else {
            // Read the value of ticks from the new document being added
            var ticks = documentBeingCreated["ticks"];

            // Validate ticks - it must not be more than an hour old, or more than 10 seconds into the future (to allow for a little bit of clock skew)
            var hourOld = now -  3600000000;
            if (ticks < hourOld) {
                throw new Error("Time is too old");
            }

            var tenSecondsInFuture = now + 100000000;
            if (ticks > tenSecondsInFuture) {
                throw new Error("Time is in the future");
            }
        }
    }
    ```

    This code runs if the document contains a **ticks** property. It verifies that the **ticks** property is within the acceptable range of values; if not, the code throws an error. If the code throws an error, the trigger aborts and the associate change to a document is discarded.

9.  On the **New Trigger 1** tab, click **Save**.

#### Task 2: Test the Trigger using Azure DocumentDB Studio

> **Note**: Triggers are not executed automatically; you must specify the pre-trigger and/or post-trigger in the request options when you submit an insert, update, or delete request. You cannot test triggers from the Azure Portal Data Explorer because it does not provide the option to specify trigger names them when inserting, updating, or deleting documents. In this demonstration, you will use Azure Document DB Studio to execute some queries; Azure Document DB Studio allows you to specify trigger names.

1.  In File Explorer, navigate to **E:\\DocumentDBStudio**, and then double-click **AzureDocumentDBStudio.exe**.

2.  In Azure DocumentDB Studio, on the **File** menu, click **Add Account**.

3.  In Internet Explorer, on the **20777a-sql-\<*your name\>-\<the day*\>** blade,under **Settings**, click **Keys**.

4.  Make a note of the **URI**, and **PRIMARY KEY** values.

5.  In Azure DocumentDB Studio, in the **Account Settings** dialog box, in the **AccountEndpoint** box, enter the **URI** value.

6.  In the **AccountSecret** box, enter the **PRIMARY KEY** value.

7.  In the **Connection** section, click **Direct TCP**, and then click **OK**.

8.  In the left pane, expand the URL for your account, expand **DeviceData**, right-click **Temperatures**, and then click **Create document with prefilled id**.

9.  On the **Create Document** tab, edit the document definition to add the following properties:

    ```JSON
      "temperature": 993,
      "deviceID": "Device 1"
    ```

    When you have finished, the document should look like this (but with a different **id** value):

    ```JSON
    {
      "id": "7f2890fe-9027-42f3-90d4-2deb6be47ee4",
      "temperature": 993,
      "deviceID": "Device 1"
    }
    ```

10. Notice that the new document lacks a **ticks** property. The pre-trigger that you created in the last task will be used to populate the **ticks** property with the current time.

11. On the **RequestOptions** tab, clear the **Use default** check box.

12. In the **PreTrigger** box, type **validateTime**.

13. In the **PartitionKey** box, type **Device 1**, and then click **Execute**. The request response is returned in the lower right-hand pane. Notice that the new document includes a **ticks** property:

    ```JSON
    {
    "id": "7f2890fe-9027-42f3-90d4-2deb6be47ee4",
    "temperature" :  993,
    "deviceID" :  "Device 1",
    "ticks" :  636649317180190000
    }
    ```

14. To test that the trigger correctly rejects **ticks** values more than one hour in the past, in the left pane, right-click **Temperatures**, and then click **Create document with prefilled id**.

15. In the **Create Document** pane, edit the document definition to add the following properties:

    ```JSON
      "temperature": 993,
        "deviceID": "Device 1",
        "ticks": 100
    ```

    When you have finished, the document should look like this (but with a different **id** value):

    ```JSON
    {
    "id": "7f2890fe-9027-42f3-90d4-2deb6be47ee4",
      "temperature" :  993,
        "deviceID" :  "Device 1",
        "ticks" :  100
    }
    ```

16. In the toolbar, click **Execute**. A lengthy error message—including the error "Time is too old"—should be returned. The error message should begin:

    > Microsoft.Azure.Documents.BadRequestException: Message: {"Errors":["Encountered exception while executing Javascript. Exception = Error: Time is too old\r\nStack trace: Error: Time is too old\n   at validateTime (validateTime.js:33:25)\n   at __docDbMain (validateTime.js:44:5)\n   at Global code (validateTime.js:1:2)"]}

17. To test that the trigger correctly rejects **ticks** values more than 10 seconds in the future, edit the document definition in the **Create Document** pane, and set the **ticks** value to **736649317180190000**. When you have finished, the document should look like this (but with a different **id** value):

      ```JSON
      {
        "id": "7f2890fe-9027-42f3-90d4-2deb6be47ee4",
        "temperature" :  993,
        "deviceID" :  "Device 1",
        "ticks" :  736649317180190000
      }
      ```

18. In the toolbar, click **Execute**. A lengthy error message—including the error "Time is in the future"—should be returned. The error message should begin:

    > Microsoft.Azure.Documents.BadRequestException: Message: {"Errors":["Encountered exception while executing Javascript. Exception = Error: Time is in the future\r\nStack trace: Error: Time is in the future\n   at validateTime (validateTime.js:38:25)\n   at __docDbMain (validateTime.js:44:5)\n   at Global code (validateTime.js:1:2)"]}

19. When you have finished, close Azure DocumentDB Studio.

#### Task 3: Invoke the Trigger From a SQL API Application

1.  In File Explorer, navigate to **E:\\Demofiles\\Mod03\\Demo03\\TestTrigger**, and then double-click **TestTrigger.sln** to open the solution in Visual Studio. Explain that this is a WPF application that enables the user to create a new **Temperatures** document and fire the **validateTime** pre-trigger.

2.  In Solution Explorer, double-click **App.config**.

3.  In Internet Explorer, on the **20777a-sql-\<*your name\>-\<the day*\>** blade, under **Settings**, click **Keys**.

4.  Make a note of the **URI**, and **PRIMARY KEY** values.

5.  In Visual Studio, in the **value** attribute of the **EndpointURL** key, paste the **URI** value, replacing the text **your\_URI\_here**.

    ```XML
    <add key="EndpointUrl" value="your_URI_here" />
    ```

6.  In the **value** attribute of the **PrimaryKey** key, paste the **PRIMARY KEY** value, replacing the text **your\_PK\_here**.

    ```XML
    <add key="PrimaryKey" value="your_PK_here" />
    ```

7.  Confirm that the value of the **Database** key is **DeviceData**, that the value of the **Collection** key is **Temperatures**, and the value of the **Trigger** key is **validateTime**.

8.  Press F5 to run the application, and wait for the application to start.

9.  In the **Test Triggers** window, in the **Document** box, edit the document definition to add the following properties:

    ```JSON
    "temperature": 999,
    "deviceID": "Device d3"
    ```

    When you have finished, the document should look like this (but with a different **id** value):

    ```JSON
    {
    "id": "7f2890fe-9027-42f3-90d4-2deb6be47ee4",
    "temperature": 999,
    "deviceID": "Device d3"
    }
    ```

10. Click **Create**. Notice that the value of the **Document** box is updated to reflect the inserted record, and that a value has been added for the **ticks** property.

11. In Internet Explorer, in the **SQL API** pane, expand **Temperatures**, and then click **Documents**.

12. In the **Documents** pane, click **Edit filter**.

13. In the filter box, type **WHERE c.deviceID = "Device d3"**, and then click **Apply Filter**.

14. One document should be returned; the value of the **id** property should match the value of the **id** property shown in the **Document** box in the Test Trigger application.

15. In the **Test Triggers** application, to test future date detection, change the first character of the **id** value to **f** (if it is already **f**, change it to **e**), then change the first digit of the **ticks** property to **7** (this puts the date into the far future). When you have finished, the document should look like this (but with a different **id** value):

    ```JSON
    {
    "id": "ff2890fe-9027-42f3-90d4-2deb6be47ee4",
    "temperature": 999,
    "deviceID": "Device d3",
    "ticks": "736649940582340000",
    }
    ```

16. Click **Create**. An error should be reported:

    > Error: Time is in the future

17. In Internet Explorer, on the **Documents** tab, click the refresh button.

18. Only one document is returned; the document with the **ticks** value in the future was not added.

19. In the **Test Triggers** application, to test past date detection, change the value of the **ticks** property to **100** (this puts the date into the distant past). When you have finished, the document should look like this (but with a different **id** value):

    ```JSON
    {
    "id": "ff2890fe-9027-42f3-90d4-2deb6be47ee4",
    "temperature": 999,
    "deviceID": "Device d3",
    "ticks": "100",
    }
    ```

20. Click **Create**. An error should be reported:

    > Error: Time is too old

21. Close the Test Triggers application and return to Visual Studio.

22. In Visual Studio, in Solution Explorer, expand **MainWindow.xaml**, and then double-click **MainWindow.xaml.cs**.

23. In MainWindow.xaml.cs, in the **Button\_Click** method (line 52). This is the code that runs when the user clicks the **Create** button. Point out the following code:

    ```CSharp
    // Get the Uri of the Temperatures collection
    Uri documentCollectionUri = UriFactory.CreateDocumentCollectionUri(this.database, this.collection);

    // Specify that the create document API call should invoke the Pre trigger
    var options = new RequestOptions
    {
        PreTriggerInclude = new List<string> { this.trigger }
    };

    // Create the document and get the response
    var reply = await this.client.CreateDocumentAsync(documentCollectionUri, document, options);
    ```

    Explain that this is the code that creates the document using the data provided in the text box. The important point to note is the **RequestOptions** object, which specifies that the pre-trigger should be run (there is also a *PostTriggerInclude* option). Also note, that although the **PreTriggerInclude** option accepts a list of triggers, at the time of writing only one pre trigger can be fired when the create operation occurs.

24. In MainWindow.xaml.cs, point out the following code (line 92):

    ```CSharp
    catch (DocumentClientException dce)
    {
        Regex errMsgPattern = new Regex(@".*(?<error>Error:.*)\\r\\nStack.*");
        Match errMsgMatch = errMsgPattern.Match(dce.Message);
        if (errMsgMatch.Success)
        {
          message.Text = errMsgMatch.Groups["error"].Value;
        }
        else
        {
          message.Text = $"{dce.Message}";
        }
    }
    ```

    Explain that if the trigger throws an error, it is manifested as a **DocumentClientException** to the caller. This **catch** handler searches through the rather lengthy error message returned by the JavaScript runtime that invokes the trigger and extracts the message thrown by the trigger for display.

25. When you have finished, close Visual Studio.

#### Task 4: Delete the Azure resources

1.  In Internet Explorer, click **Resource groups**, right-click **20777aMod3**, and then click **Delete resource group**.

2.  On the **Are you sure you want to delete "20777aMod3"?** blade, in the **TYPE THE RESOURCE GROUP NAME** box, type **20777aMod3**, and then click **Delete**.

3.  Close Internet Explorer, and any other open windows.

---

©2019 Microsoft Corporation. All rights reserved.

The text in this document is available under the [Creative Commons Attribution 3.0 License](https://creativecommons.org/licenses/by/3.0/legalcode), additional terms may apply. All other content contained in this document (including, without limitation, trademarks, logos, images, etc.) are **not** included within the Creative Commons license grant. This document does not provide you with any legal rights to any intellectual property in any Microsoft product. You may copy and use this document for your internal, reference purposes.

This document is provided "as-is." Information and views expressed in this document, including URL and other Internet Web site references, may change without notice. You bear the risk of using it. Some examples are for illustration only and are fictitious. No real association is intended or inferred. Microsoft makes no warranties, express or implied, with respect to the information provided here.
