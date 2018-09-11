# Module 2: Designing and Implementing SQL API Database Applications

- [Module 2: Designing and Implementing SQL API Database Applications](#module-2-designing-and-implementing-sql-api-database-applications)
    - [Lesson 1: Document Models in Cosmos DB](#lesson-1-document-models-in-cosmos-db)
        - [Demo 1: Partitioning a Document Database](#demo-1-partitioning-a-document-database)
            - [Preparation](#preparation)
            - [Task 1: Create a SQL API Account, Database, and Collections](#task-1-create-a-sql-api-account-database-and-collections)
            - [Task 2: Populate the collections](#task-2-populate-the-collections)
            - [Task 3: Test the Partitioning Strategy](#task-3-test-the-partitioning-strategy)
            - [Task 4: Delete the DeviceData database](#task-4-delete-the-devicedata-database)
    - [Lesson 2: Querying Data in SQL API Databases](#lesson-2-querying-data-in-sql-api-databases)
        - [Demo 1: Querying Data in SQL API Databases](#demo-1-querying-data-in-sql-api-databases)
            - [Preparation](#preparation)
            - [Task 1: Create the Person Database and Populate the PersonsData Collection](#task-1-create-the-person-database-and-populate-the-personsdata-collection)
            - [Task 2: Perform Queries using the PersonsData Collection](#task-2-perform-queries-using-the-personsdata-collection)
            - [Task 3: Delete the PersonData database](#task-3-delete-the-persondata-database)
    - [Lesson 3: Querying and Maintaining Data Programmatically](#lesson-3-querying-and-maintaining-data-programmatically)
        - [Demo 1: Indexing and Queries](#demo-1-indexing-and-queries)
            - [Preparation](#preparation)
            - [Task 1: Create and Populate the Temperatures Collection](#task-1-create-and-populate-the-temperatures-collection)
            - [Task 2: Perform a Query that Uses an Index](#task-2-perform-a-query-that-uses-an-index)
            - [Task 3: Remove the Index from the DeviceID Property](#task-3-remove-the-index-from-the-deviceid-property)
            - [Task 4: Enable Scanning in the Query](#task-4-enable-scanning-in-the-query)
            - [Task 5: Reenable the DeviceID Index and Compare the Runtime Statistics](#task-5-reenable-the-deviceid-index-and-compare-the-runtime-statistics)
        - [Demo 2: Querying, Inserting, Modifying, and Deleting Data using the SQL API](#demo-2-querying-inserting-modifying-and-deleting-data-using-the-sql-api)
            - [Preparation](#preparation)
            - [Task 1: Retrieve Documents by ID](#task-1-retrieve-documents-by-id)
            - [Task 2: Create, Delete, and Modify Documents](#task-2-create-delete-and-modify-documents)
            - [Task 3: Read and Write Documents with Attachments](#task-3-read-and-write-documents-with-attachments)

## Lesson 1: Document Models in Cosmos DB

### Demo 1: Partitioning a Document Database

#### Preparation

Before starting this demo:

1.  Ensure that the **MT17B-WS2016-NAT** and **20777A-LON-DEV** virtual machines are running, and then log on to **20777A-LON-DEV** as **Administrator** with the password **Pa55w.rd**.

2.  On the Start menu, type **Internet Explorer**, and then press Enter.

3.  In Internet Explorer, go to http://portal.azure.com, and sign in using the Microsoft account that is associated with your Azure Learning Pass subscription.

4.  On the toolbar, click **File** **Explorer**.

5.  In File Explorer, navigate to **E:\\Demofiles\\Mod02**, right-click **Setup.cmd**, and then click **Run as administrator**.

6.  In File Explorer, navigate to **E:\\Resources**, right-click **build\_data\_migration\_tool.ps1**, and then click **Run with PowerShell**.

7.  Wait for the script to finish, and then press Enter.

#### Task 1: Create a SQL API Account, Database, and Collections

1.  In Internet Explorer, in the left panel click **Azure Cosmos DB**, and then click **+ Add**.

2.  On the **Azure Cosmos DB** blade, in the **ID** box, type **20777a-sql-\<your name\>-\<the day\>**, for example, **20777a-sql-john-31**.

3.  In the **API** drop-down list, note the options available, and then click **SQL**.

4.  In the **Resource Group** box, type **20777aMod2**.

5.  In the **Location** drop-down list, click the region closest to your current location, and then click **Create**.

6.  Wait for the Azure Cosmos DB to be created—this could take a few minutes.

7.  On the **Azure Cosmos DB** blade, click **Refresh**, and then click **20777a-sql-\<your name\>-\<the day\>**.

8.  On the **20777a-sql-\<your name\>-\<the day\>** blade, click **Data Explorer**, and then click **New Database**.

9.  On the **New Database** blade, in the **Database id** box, type **DeviceData**, and then click **OK**.

10. On the **20777a-sql-\<your name\>-\<the day\>** blade, click **New Collection**.

11. On the **Add Collection** blade, click **Use existing**, and then in the drop-down list, click **DeviceData**.

12. In the **Collection Id** box, type **Temperatures**.

13. Under **Storage capacity**, click **Unlimited**.

14. In the **Partition key** box, type **/deviceID**.

    > **Note:** Cosmos DB is case sensitive; the case of the partitioning key must match the case of the property in your documents.

15. In the **Throughput (1,000 - 100,000 RU/s)** box, type **15000**, and then click **OK**.

16. On the **20777a-sql-\<your name\>-\<the day\>** blade, click **New Collection**.

17. On the **Add Collection** blade, click **Use existing**, and then in the drop-down list, click **DeviceData**.

18. In the **Collection Id** box, type **Temperatures2**.

19. Under **Storage capacity**, click **Unlimited**.

20. In the **Partition key** box, type **/date**.

21. In the **Throughput (1,000 - 100,000 RU/s)** box, type **15000**, and then click **OK**.

#### Task 2: Populate the collections

1.  On the **20777a-sql-\<your name\>-\<the day\>** blade, click **Keys**.

2.  Make a note of the **PRIMARY CONNECTION STRING** value.

3.  In File Explorer, navigate to **E:\\Demofiles\\Mod02\\Demo01**, right-click **Demo01-setup.ps1**, and then click **Run with PowerShell**.

4.  Right-click anywhere inside the PowerShell window to paste your primary connection string, and then press Enter.

5.  The script will load the same documents into the **Temperatures** and **Temperatures2** collections, and will take several minutes to complete.

6.  Wait for the script to finish, and then press Enter.

7.  In Internet Explorer, on the **20777a-sql-\<your name\>-\<the day\>** blade, click **Data Explorer**.

8.  In the **SQL API** pane, expand **Temperatures**, and then click **Documents**.

9.  On the **Documents** tab, a list of documents will appear.

10. Repeat steps 8 and 9 for the **Temperatures2** collection.

#### Task 3: Test the Partitioning Strategy

1. In File Explorer, navigate to **E:\\Demofiles\\Mod02\\Demo01\\Partitioning**, and then double-click **Partitioning.sln**.

2. In the **How do you want to open this file?** dialog box, double-click **Visual Studio 2017**.

3. Explain that this application simulates many concurrent users performing queries against temperature data, and calculates the query cost in Resource Units per second (RU/s).

4. In Solution Explorer, double-click **ClientReader.cs**.

5. Scroll down to the **RunQueriesAsync** method. Explain that the details of the Azure SDK are covered in more detail later in this module, but point out the following features:
    
    - The code runs a query to retrieve all the temperature readings for a specified device:

    ```SQL 
    SELECT c.deviceID, c.temperature, c.date FROM {this.collectionName} c WHERE c.deviceID = @devID
    ```
    
    - The `FeedOptions` object used to configure the query options:
    
    ```CSharp     
    // Capture the internal query metrics
    FeedOptions options = new FeedOptions();
    options.PopulateQueryMetrics = true;
    options.MaxItemCount = -1;
    
    // TODO: Only select the PartitionKey property for appropriately partitioned collections
    //options.PartitionKey = new PartitionKey(deviceKey);
    
    // TODO: Enable cross-partition queries if the partition key does not match the predicate
    options.EnableCrossPartitionQuery = true;
    
    // TODO: MaxDegreeOfParallelism is only useful for cross-partition queries
    options.MaxDegreeOfParallelism = this.maxDegreesOfParallelism;
    ```   

    - Queries that calculate aggregated values over all documents for a specified device:
  
    ```SQL       
    SELECT VALUE max(c.temperature) FROM {this.collectionName} c WHERE c.deviceID = @devID
    SELECT VALUE min(c.temperature) FROM {this.collectionName} c WHERE c.deviceID = @devID
    SELECT VALUE avg(c.temperature) FROM {this.collectionName} c WHERE c.deviceID = @devID
    SELECT VALUE count(1) FROM {this.collectionName} c WHERE c.deviceID = @devID
    ```
6. In Solution Explorer, double-click **App.config**.

7. In Internet Explorer, in the Azure Portal, on the **20777a-sql-\<your name\>-\<the day\>** blade, click **Keys**.

8. Make a note of the **URI**, and **PRIMARY KEY** values.

9. In Visual Studio, in the **Value** attribute of the **EndpointURL** key, paste the **URI** value, replacing the text **your\_URI\_here**.

    ```XML
    <add key="EndpointUrl" value="your_URI_here" />
    ```

10. In Visual Studio, in the **Value** attribute of the **PrimaryKey** key, paste the **PRIMARY KEY** value, replacing the text **your\_PK\_here**.

    ```XML
    <add key="PrimaryKey" value="your_PK_here" />
    ```

11. Confirm that the value of the **Database** key is **DeviceData** and that the value of the **Collection** key is **Temperature2**.

12. In ClientReader.cs, in the **RunQueriesAsync** method, confirm that the **EnableCrossPartitionQuery** and **MaxDegreeOfParallelism** options are not commented out, and the **PartitionKey** option is commented out (as in the code shown in step 5 above).

13. Press F5 to run the application. The application will take a couple of minutes to run; it simulates 100 concurrent users, each performing 10 iterations of the workload specified in the **ClientReader** class. When it completes, make a note of the **Processing Throughput (in RU/s)** (query cost) and **Elapsed Time** returned by the application.

    > **Note:** The **Processing Throughput (in RU/s)** figure shows the throughput consumed by the query in RU/s. The higher the **Processing Throughput (in RU/s)** value, the greater the cost of the query.

14. When the application is complete, press any key to close the window.

15. To change the application to use the **Temperatures** collection (that is partitioned by **deviceId**), in Solution Explorer, double-click **App.config**, and edit the values of the **Collection** key so that it reads:

    ```XML 
    <add key="Collection" value="Temperatures" />
    ```

16. In Solution Explorer, double-click **ClientReader.cs**, and amend the **RunQueriesAsync** method to comment out the **EnableCrossPartitionQuery** and **MaxDegreeOfParallelism** options, and un-comment the statement that sets the **PartitionKey** option. When you have finished editing, lines 93 to 100 of **ClientReader.cs** should read:

    ```CSharp 
    // TODO: Only select the PartitionKey property for appropriately partitioned collections
    options.PartitionKey = new PartitionKey(deviceKey);

    // TODO: Enable cross-partition queries if the partition key does not match the predicate
    //options.EnableCrossPartitionQuery = true;

    // TODO: MaxDegreeOfParallelism is only useful for cross-partition queries
    //options.MaxDegreeOfParallelism = this.maxDegreesOfParallelism;
    ```

17. Press F5 to run the application; it will take a couple of minutes to run.

18. When it completes, make a note of the **Processing Throughput (in RU/s)** and **Elapsed Time** returned by the application, and compare them to results of the first run.

> The throughput figure for the first run should be 3 or 4 times more than that of the second run. indicating that the first run had a higher RU/s cost. Both runs aggregate the same results, but partitioning the data by deviceID is much more cost-effective for queries that retrieve data by deviceID.
>
> The elapsed time is likely to be longer for the first run. In the first run, that data is partitioned by the date field and the queries can take advantage of parallelism to fetch the data for a device using concurrent threads (as indicated by the **MaxDegreeOfParallelism** option). In the second run, the data for each query comes from a single partition and cannot be parallelized; however, the first run must do so much more work to aggregrate the results that the second run is faster.

19. In the application window, press any key to close the window.

#### Task 4: Delete the DeviceData database

1.  In Internet Explorer, on the **20777a-sql-\<your name\>-\<the day\>** blade, click **Data Explorer**.

2.  In the **SQL API** pane, right-click **DeviceData**, and then click **Delete Database**.

3.  On the **Delete Database** blade, in the **Confirm by typing the database id** box, type **DeviceData**, and then click **OK**.

4.  Close Visual Studio, but leave Internet Explorer open for the next demonstration.

    > **Note:** You should delete the collections at the end of the demonstration to avoid unexpected charges to you Azure subscription.

## Lesson 2: Querying Data in SQL API Databases

### Demo 1: Querying Data in SQL API Databases

#### Preparation

You need to have completed the previous demonstration before following these steps.

#### Task 1: Create the Person Database and Populate the PersonsData Collection

1.  In Internet Explorer, in the Azure Portal, click **All Resources**, and then click **20777a-sql-\<your name\>-\<the day\>**.

2.  On the **20777a-sql-\<your name\>-\<the day\>** blade, click **Data Explorer**, and then click **New Database**.

3.  On the **New Database** blade, in the **Database id** box, type **PersonData**, and then click **OK**.

4.  On the **20777a-sql-\<your name\>-\<the day\>** blade, click **New Collection**.

5.  On the **Add Collection** blade, click **Use existing**, and then in the drop-down list, click **PersonData**.

6.  In the **Collection Id** box, type **Persons**, and then click **OK**.

7.  On the **20777a-sql-\<your name\>-\<the day\>** blade, click **Keys**.

8.  Make a note of the **PRIMARY CONNECTION STRING** value.

9.  In File Explorer, navigate to **E:\\Demofiles\\Mod02\\Demo02**, right-click **Demo02-setup.ps1**, and then click **Run with PowerShell**.

10. In the PowerShell window, right-click anywhere to paste your primary connection string, and then press Enter.

11. Wait for the script to finish, and then press Enter to close the PowerShell window.

#### Task 2: Perform Queries using the PersonsData Collection

1.  In Internet Explorer, on the **20777a-sql-\<your name\>-\<the day\>** blade, click **Data Explorer**.

2.  In the **SQL API** pane, right-click **Persons**, and then click **New SQL Query**.

3.  On the **Query1** tab, type the following code, and then click **Execute Query**:

    ```SQL 
    SELECT * FROM p
    ```
    
    Verify that three documents are displayed, showing the details and address history of each person.

4.  On the **Query1** tab, type the following code, and then click **Execute Query**:

    ```SQL 
    SELECT p.Title, p.Name FROM p
    ```
    
    This query should display the title and name of each person using projection. Note that the `Name` property is a sub-document that contains two fields; **FirstName** and **LastName**.

5.  On the **Query1** tab, type the following code, and then click **Execute Query**:

    ```SQL 
    SELECT p["Title"], p["Name"] FROM p
    ```
    
    This query should return the same results as the previous query, using property name syntax.

6.  On the **Query1** tab, type the following code, and then click **Execute Query**:

    ```SQL 
    SELECT p.Title, p.Name.FirstName, p.Name.LastName FROM p
    ```
    
    This query explicitly references the fields in the `Name` sub-document: Note that the structure of the JSON document that is returned is different from the earlier example; everything is at the root level.

7.  On the **Query1** tab, type the following code, and then click **Execute Query**:

    ```SQL 
    SELECT p["Title"], p["Name.FirstName"] FROM p
    ```
    
    This query does not return the results that you might expect; the second property is NULL. This is because the condition in the **SELECT** clause **p\["Name.FirstName"\]** is looking for a property with the name **"Name.FirstName"**.

8.  On the **Query1** tab, type the following code, and then click **Execute Query**:

    ```SQL 
    SELECT p["Title"], p["Name"]["FirstName"] FROM p
    ```
    
    This query correctly returns the **Name.FirstName** property using property name syntax.

9.  On the **Query1** tab, type the following code, and then click **Execute Query**:

    ```SQL 
    SELECT {"Forename":p.Name.FirstName, "Surname":p.Name.LastName} AS ContactName
    FROM p
    ```
    
    This query renames the fields in the JSON document that is returned.

10. On the **Query1** tab, type the following code, and then click **Execute Query**:

    ```SQL 
    SELECT * FROM p
    WHERE p.CurrentAddress.State IN ("WA", "NY")
    ```
    
    This query filters the data by State.

11. On the **Query1** tab, type the following code, and then click **Execute Query**:

    ```SQL 
    SELECT * FROM a
    IN p.AddressHistory
    WHERE a.State = "WA"
    ```
    
    This query returns just the matching address information from the AddressHistory sub-documents.

12. On the **Query1** tab, type the following code, and then click **Execute Query**:

    ```SQL 
    SELECT * FROM p
    WHERE IS_DEFINED(p.DateOfBirth)
    ```
    
    This query returns documents that contain the **DateOfBirth** property.

13. On the **Query1** tab, type the following code, and then click **Execute Query**:

    ```SQL 
    SELECT * FROM p
    ORDER BY p.CurrentAddress.ZipCode
    ```
    
    This query reorders the results by the **ZipCode** field of the **CurrentAddress** sub-document.

14. On the **Query1** tab, type the following code, and then click **Execute Query**:

    ```SQL 
    SELECT p.CurrentAddress.City, 
           p.CurrentAddress.State, 
           ST_DISTANCE(p.CurrentAddress.Location, 
               {"type": "Point", "coordinates": [-0.12721, 51.50642]}
            ) / 1000 AS DistanceFromLondonInKM
    FROM p
    ```

    This query uses the spatial coordinates of the current address of each person to calculate their distance from London, England.

15. On the **Query1** tab, type the following code, and then click **Execute Query**:

    ```SQL 
    SELECT p.Name, p.CurrentAddress.City AS CurrentCity, h.City AS EarlierCity
    FROM p
    JOIN h IN p.AddressHistory
    ```
    
    This query performs a join between a Person document and its AddressHistory sub-document to list the current and earlier cities in which each person resided.

16. On the **Query1** tab, type the following code, and then click **Execute Query**:
    
    ```SQL 
    SELECT VALUE COUNT(1)
    FROM p
    WHERE p.CurrentAddress.State = "WA"
    ```
    
    This query returns how many people in the collection currently live in Washington State (just 1 in the demonstration data set).

17. On the **Query1** tab, type the following code, and then click **Execute Query**:
    
    ```SQL 
    SELECT p.Name, ARRAY_LENGTH(p.AddressHistory) AS NumberOfPreviousAddresses
    FROM p
    ```
    
    This query displays the name of each person and the number of previous addresses for that person (if any).

#### Task 3: Delete the PersonData database

1.  In the **SQL API** pane, right-click **PersonData**, and then click **Delete Database**.

2.  On the **Delete Database** blade, in the **Confirm by typing the database id** box, type **PersonData**, and then click **OK**.

3.  Leave Internet Explorer open for the next demonstration.

    > **Note:** You should delete the collection at the end of the demonstration to avoid unexpected charges to you Azure subscription

## Lesson 3: Querying and Maintaining Data Programmatically

### Demo 1: Indexing and Queries

#### Preparation

You need to have completed the previous demonstration before following these steps.

#### Task 1: Create and Populate the Temperatures Collection

1.  In Internet Explorer, in the Azure Portal, click **All Resources**, and then click **20777a-sql-\<your name\>-\<the day\>**.

2.  On the **20777a-sql-\<your name\>-\<the day\>** blade, click **Data Explorer**, and then click **New Database**.

3.  On the **New Database** blade, in the **Database id** box, type **DeviceData**, and then click **OK**.

4.  On the **20777a-sql-\<your name\>-\<the day\>** blade, click **New Collection**.

5.  On the **Add Collection** blade, click **Use existing**, and then in the drop-down list, click **DeviceData**.

6.  In the **Collection Id** box, type **Temperatures**, and then click **OK**.

7.  On the **20777a-sql-\<your name\>-\<the day\>** blade, click **Keys**.

8.  Make a note of the **PRIMARY CONNECTION STRING** value.

9.  In File Explorer, navigate to **E:\\Demofiles\\Mod02\\Demo03**, right-click **Demo03-setup.ps1**, and then click **Run with PowerShell**.

10. In the PowerShell window, right-click anywhere to paste your **PRIMARY CONNECTION STRING**, and then press Enter.

11. Wait for the script to finish, and then press Enter to close the PowerShell window.

#### Task 2: Perform a Query that Uses an Index

1.  In File Explorer, navigate to **E:\\Demofiles\\Mod02\\Demo03\\IndexDemo**, and then double-click **IndexDemo.sln** to open the solution in Visual Studio.

2.  In Solution Explorer, double-click **App.config**.

3.  In Internet Explorer, on the **20777a-sql-\<your name\>-\<the day\>** blade, click **Keys**.

4.  Make a note of the **URI**, and **PRIMARY KEY** values.

5.  In Visual Studio, in the **Value** attribute of the **EndpointUrl** key, paste the **URI** value, replacing the text **your\_URI\_here**.

    ```XML 
        <add key="EndpointUrl" value="your_URI_here" />
    ```

6.  In Visual Studio, in the **Value** attribute of the **PrimaryKey** key, paste the **PRIMARY KEY** value, replacing the text **your\_PK\_here**.
    
    ```XML 
        <add key="PrimaryKey" value="your_PK_here" />
    ```

7.  Confirm that the value of the **Database** key is **DeviceData** and that the value of the **Collection** key is **Temperatures**.

8.  In Solution Explorer, double-click **Program.cs**. Point out that the code runs the following query; this query performs optimally if an index is available on the **deviceID** property:

    ```SQL 
    SELECT c.deviceID, c.temperature, c.time FROM {collection} c WHERE c.deviceID = @devID
    ```
9.  Press F5 to run the application.

10. At the prompt, type **Device 8**, and then press Enter.

11. The application will return a list of temperature readings, followed by some execution statistics.

12. Ignore the statistics for now, and then press a key to close the application.

#### Task 3: Remove the Index from the DeviceID Property

1.  In Internet Explorer, on the **20777a-sql-\<your name\>-\<the day\>** blade, click **Data Explorer**.

2.  In the **SQL API** pane, expand **Temperatures**, and then click **Scale & Settings**.

3.  On the **Scale & Settings** tab, to change the indexing policy to exclude **deviceID** from the list of indexed paths, edit the contents of the **Indexing Policy** box so that it matches the code block below, and then click **Save**:

    ```JSON 
    {
        "indexingMode": "consistent",
        "automatic": true,
        "includedPaths": [
            {
                "path": "/*",
                "indexes": [
                    {
                        "kind": "Range",
                        "dataType": "Number",
                        "precision": -1
                    },
                    {
                        "kind": "Range",
                        "dataType": "String",
                        "precision": -1
                    },
                    {
                        "kind": "Spatial",
                        "dataType": "Point"
                    }
                ]
            }
        ],
        "excludedPaths": [
            {
                "path": "/deviceID/*"
            }
        ]
    }
    ```

4.  In Visual Studio, press F5 to run the application.

5.  At the prompt, type **Device 8**, and then press Enter.

6.  The application will fail with an error message:

> Message: {"Errors":\["An invalid query has been specified with filters against path(s) excluded from indexing. Consider adding allow scan header in the request."\]}

7.  Press a key to close the application.

#### Task 4: Enable Scanning in the Query

1.  In Visual Studio, in the **Program.cs** file, uncomment line 54 so that it reads:

    ```CSharp 
    options.EnableScanInQuery = true;
    ```

2.  In Visual Studio, press F5 to run the application.

3.  At the prompt, type **Device 8**, and then press Enter.

4.  The application will run successfully; the error message is no longer displayed. Make a note of the runtime statistics displayed by the application. They will look like this:
    ```DOS
        Request charge: 1832.24 RUs
        Index LookUp Time: 0 ms
        Index Hit Ratio: 0.833333333333333%
        Document Load Time: 75.38 ms
        Runtime Execution Time: 49.9201 ms
    ```
5.  Press a key to close the application.

#### Task 5: Reenable the DeviceID Index and Compare the Runtime Statistics

1.  In Internet Explorer, on the **Scale & Settings** tab, to remove the exclusion for **deviceID** from the list of indexed paths, edit the contents of the **Indexing Policy** box so that it matches the code block below, and then click **Save**:

    ```JSON 
    {
        "indexingMode": "consistent",
        "automatic": true,
        "includedPaths": [
            {
                "path": "/*",
                "indexes": [
                    {
                        "kind": "Range",
                        "dataType": "Number",
                        "precision": -1
                    },
                    {
                        "kind": "Range",
                        "dataType": "String",
                        "precision": -1
                    },
                    {
                        "kind": "Spatial",
                        "dataType": "Point"
                    }
                ]
            }
        ],
        "excludedPaths": []
    }
    ```

2.  In Visual Studio, press F5 to run the application.

3.  At the prompt, type **Device 8**, and then press Enter.

4.  Make a note of the runtime statistics displayed by the application; they will look similar to this:
    ```DOS
        Request charge: 22.39 RUs
        Index LookUp Time: 0.31 ms
        Index Hit Ratio: 100%
        Document Load Time: 0.5 ms
        Runtime Execution Time: 0.45 ms
    ```
5.  Press a key to close the application.

6.  Compare the runtime statistics that you collected in this task with the statistics that you collected in the previous task. Note that:
    
      - The **Request Charge** is 8 or 9 times lower when the **deviceID** is included in an index. Filtering data with an index is much more efficient than a scan of the entire partition.
    
      - When the index on **deviceID** is enabled, the **Index Hit Ratio** is 100% and the **Index Lookup Time** is non-zero because the data was located using the index.
    
      - The **Runtime Execution Time** is an order of magnitude lower when the index on **deviceID** is enabled.

7.  When you have finished, close Visual Studio.

8.  Leave Internet Explorer open for the next demonstration.

    >**Note:** Do not delete the **Temperatures** collection at the end of this demonstration; it is needed for the next demonstration in this module.

### Demo 2: Querying, Inserting, Modifying, and Deleting Data using the SQL API

#### Preparation

You need to have completed the previous demonstration before following these steps.

> **Note:** This demo requires the **Temperatures** collection created by the previous demonstration.

#### Task 1: Retrieve Documents by ID

1.  In File Explorer, navigate to **E:\\Demofiles\\Mod02\\Demo04\\TemperaturesDBDemoCode**, and then double-click **TemperaturesDBDemoCode.sln** to open the solution in Visual Studio.

2.  In Solution Explorer, double-click **App.config**.

3.  In Internet Explorer, on the **20777a-sql-\<your name\>-\<the day\>** blade, click **Keys**.

4.  Make a note of the **URI**, and **PRIMARY KEY** values.

5.  In Visual Studio, in the **Value** attribute of the **EndpointUrl** key, paste the **URI** value, replacing the text **your\_URI\_here**.

    ``` XML
    <add key="EndpointUrl" value="your_URI_here" />
    ```

6.  In Visual Studio, in the **Value** attribute of the **PrimaryKey** key, paste the **PRIMARY KEY** value, replacing the text **your\_PK\_here**.

    ```XML 
    <add key="PrimaryKey" value="your_PK_here" />
    ```

7.  Confirm that the value of the **Database** key is **DeviceData** and that the value of the **Collection** key is **Temperatures**.

8.  In Solution Explorer, double-click **Program.cs**.

9.  In Program.cs, in the **Worker** class, locate the **FindTemperatureDocumentByID** method (line 121):

    ```csharp 
    private async Task FindTemperatureDocumentByID()
    {
        Console.WriteLine("Enter document ID");
        string id = Console.ReadLine();
        Uri docUri = UriFactory.CreateDocumentUri(this.database, this.collection, id);

        var documentResponse = await client.ReadDocumentAsync<ThermometerReading>(docUri);

        // The Document property of the response contains a typed document
        Console.WriteLine(documentResponse.Document);
        Console.WriteLine();
    }
    ```
    
    Explain that this method implements the simplest and most efficient means of fetching a single document, by constructing a Uri that references the document directly. This method assumes that the document structure matches the fields defined in the **ThermometerReading** class.

10. In Program.cs, in the **Worker** class, locate the **FindUntypedTemperatureDocumentByID** method:

    ```csharp 
    private async Task FindUntypedTemperatureDocumentByID()
    {
        Console.WriteLine("Enter document ID");
        string id = Console.ReadLine();
        Uri docUri = UriFactory.CreateDocumentUri(this.database, this.collection, id);

        // The Document property of the response contains a JSON document
        var documentResponse = await client.ReadDocumentAsync(docUri);
        Console.WriteLine(documentResponse.Resource);
        Console.WriteLine();

        // Parse the JSON document into a Dictionary
        var values = JsonConvert.DeserializeObject<Dictionary<string, string>>(documentResponse.Resource.ToString());
        foreach (var item in values)
        {
            Console.WriteLine($"{item.Key}: {item.Value}");
        }
    }
    ```
    
    Explain that this method uses the same mechanism as the previous one to fetch the data, but makes no assumptions about the structure of that data. Instead, the data is deserialized as a dictionary of property/value pairs.

11. In Internet Explorer, on the **20777a-sql-\<your name\>-\<the day\>** blade, click **Data Explorer**.
12. In the **SQL API** pane, expand **Temperatures**, and then click **Documents**.

13. On the **Documents** tab, click the first document in the document list.

14. In the **id** property, select the value, and then press Ctrl+C to copy the value.

15. In Visual Studio, press F5 to run the application.

16. At the prompt, type **A**.

17. Right-click anywhere inside the application window to paste the document id, and then press Enter.

18. Examine the values returned by the application.

19. In Internet Explorer, verify that the values correspond to the document open in Data Explorer.

20. In the running application, at the command prompt, type **B**.

21. Right-click anywhere inside the application window to paste the document id, and then press Enter.

22. The data for the document will appear as a list of name/value pairs, including the system properties of the document, such as **time**, **\_rid**, and so on.

23. At the prompt, type **X** to close the application.

#### Task 2: Create, Delete, and Modify Documents

1.  In Visual Studio, in Program.cs, in the **Worker** class, locate the **CreateTemperatureDocument** method:

    ```csharp 
    private async Task CreateTemperatureDocument()
    {
        Console.Write("Enter a device ID: ");
        string deviceName = Console.ReadLine();


        // Create a temperature reading
        ThermometerReading reading = new ThermometerReading
        {
            DeviceID = deviceName,
            Temperature = new Random().NextDouble() * 100,
            Time = DateTime.UtcNow.Ticks
        };

        // Write the reading to the database
        Uri collectionUri = UriFactory.CreateDocumentCollectionUri(this.database, this.collection);
        var response = await this.client.CreateDocumentAsync(collectionUri, reading);
        Console.WriteLine($"Document created. Status code is {response.StatusCode}");
        Console.WriteLine($"ID is {response.Resource.Id}");
    }
    ```
    
    This code uses the **CreateDocumentAsync** method to add a document to the specified collection; you provide the Uri of the collection as a parameter. The **StatusCode** property of the response returned by **CreateDocumentAsync** indicates whether the document was added successfully; it should be an HTTP status code in the 2XX (200, 201, 202, etc) range to indicate success.

2.  In the **Worker** class, locate the **DeleteTemperatureDocument** method:

    ```csharp 
    private async Task DeleteTemperatureDocument()
    {
        // Specify the document to be deleted
        Console.WriteLine("Enter document ID");
        string id = Console.ReadLine();
        Uri docUri = UriFactory.CreateDocumentUri(this.database, this.collection, id);

        // Delete the document
        var response = await this.client.DeleteDocumentAsync(docUri);
        Console.WriteLine($"Document deleted. Status code is {response.StatusCode}");
    }
    ```
    
    This method removes a document from a collection. Note that you identify the document to be deleted by creating a URI that specifies the document ID. If you need to delete multiple documents, you remove each one individually using this technique.

3.  In the **Worker** class, locate the **UpdateTemperatureDocument** method:

    ```csharp 
    private async Task UpdateTemperatureDocument()
    {
        // Fetch the doc to be updated
        Document doc = await GetDocument();

        // Change the value of a field in the document
        double temperature = doc.GetPropertyValue<double>("temperature");
        doc.SetPropertyValue("temperature", temperature + 1000);

        // Save the doc
        await SaveDocument(doc);
    }

    private async Task<Document> GetDocument()
    {
        // Fetch the document to be updated
        Console.WriteLine("Enter document ID");
        string id = Console.ReadLine();
        Uri docUri = UriFactory.CreateDocumentUri(this.database, this.collection, id);

        var documentResponse = await client.ReadDocumentAsync(docUri);

        // Display the document just retrieved
        var doc = documentResponse.Resource;
        Console.WriteLine(doc.ToString());
        return doc;
    }

    private async Task SaveDocument(Document doc)
    {
        try
        {

            // Replace the document in the database with the updated doc
            RequestOptions options = new RequestOptions
            {
                AccessCondition = new AccessCondition
                {
                    Condition = doc.ETag,
                    Type = AccessConditionType.IfMatch
                }
            };

            var response = await client.ReplaceDocumentAsync(doc.SelfLink, doc, options);
            Console.WriteLine($"Document replaced. Status code is {response.StatusCode}");
        }
        catch (DocumentClientException dce)
        {
            // If a conflict occured, display a message
            if (dce.StatusCode == HttpStatusCode.PreconditionFailed)
            {
                Console.WriteLine("Update failed - another user already changed this document. Requery and try again");
            }
        }
    }
    ```
    
    Modifying a document is a multi-stage process; retrieve the document, change the property values in the document, write the new document to the database, then replace the old document. The example code uses two helper functions: **GetDocument** that retrieves the document, and **SaveDocument** that replaces the document in the database.
    
    > **Note:** The `GetDocument` method returns a generic `Document` object, that includes the `ETag` property, used by `SaveDocument` to help prevent lost updates from occurring. If another user modifies the same document concurrently, Cosmos DB will throw an exception to indicate a conflict.

4.  In the **Worker** class, locate the **UpdateTemperatureDocumentWithConflict** method. This method simulates two concurrent users modifying the same document and causing a conflict:

    ```csharp 
    private async Task UpdateTemperatureDocumentWithConflict()
    {
        // Fetch the doc to be updated
        Document doc = await GetDocument();

        double temperature = doc.GetPropertyValue<double>("temperature");

        // Run two tasks that change the value of a field in the document and attemptp to save it
        Task t1 = new Task(async () =>
        {
            doc.SetPropertyValue("temperature", temperature + 1000);

                // Save the doc
                await SaveDocument(doc);
        });

        Task t2 = new Task(async () =>
        {
            doc.SetPropertyValue("temperature", temperature - 500);

                // Save the doc
                await SaveDocument(doc);
        });

        t1.Start();
        t2.Start();

        Task.WaitAll(t1, t2);
    }
    ```

5.  In Visual Studio, press F5 to run the application.

6.  At the prompt, type **C** to add a new document.

7.  At the **Enter a device ID:** prompt, type **Device 999**, and then press Enter.

8.  The application will return a line indicating the status code of the operation. If the insert operation was successful, a second line will be returned that begins **ID is**, giving the **id** of the new document.

9.  At the prompt, type **A**, type the **id** of the new document, and then press Enter.

10. Details of the new document will be returned.

11. At the prompt, type **D**, type the **id** of the new document, and then press Enter to delete the document.

12. At the prompt, type **A**, type the **id** of the new document, and then press Enter.

13. Because the document was deleted, the application will return an error message beginning:

```
Message: {"Errors":["Resource Not Found"]}
```

14. In Internet Explorer, in the **SQL API** pane, expand **Temperatures**, and then click **Documents**.

15. On the **Documents** tab, click the first document in the document list.

16. In the **id** property, select the value, and then press Ctrl+C to copy the value.

17. Note the value of the **temperature** property in this document.

18. In the application, at the prompt, type **E**, right-click anywhere in the application window to paste the document id, and then press Enter to update the document.

19. At the prompt, type **A**, right-click anywhere in the application window, and then press Enter to view the updated document.

20. Notice that the value of the **temperature** property is now **1000** higher than the old value.

21. At the prompt, type **F**, right-click anywhere in the application window, and then press Enter to simulate a write conflict.

22. The following messages should be returned as one update succeeds and the other fails:

    ```DOS 
    Document replaced. Status code is OK
    Update failed - another user already changed this document. Requery and try again
    ```
    
    > **Note:** The messages might be mixed up with the re-display of the application menu. This is a side-effect of the threaded execution of the tasks and does not indicate an error.

23. At the command prompt, type **X** to close the application.

#### Task 3: Read and Write Documents with Attachments

1.  In Visual Studio, in Program.cs, in the **Worker** class, locate the **CreateDocWithAttachment** method (line 272):

    ```csharp 
    private async Task CreateDocWithAttachment()
    {
        // Create a generic JSON document
        JObject doc = new JObject();
        doc.Add("Name", "Employee 1");
        doc.Add("Department", "Engineering");

        // Write the document to the database
        Uri collectionUri = UriFactory.CreateDocumentCollectionUri(this.database, this.collection);
        var response = await this.client.CreateDocumentAsync(collectionUri, doc);
        Console.WriteLine($"Document created. Status code is {response.StatusCode}");
        Console.WriteLine($"ID is {response.Resource.Id}");

        string data = "This is the attached data - pretend it is a large binary image!";
        byte[] byteArray = Encoding.ASCII.GetBytes(data);
        MemoryStream stream = new MemoryStream(byteArray);

        // Create the attachment
        var attachmentResponse = await client.CreateAttachmentAsync(response.Resource.AttachmentsLink, stream,
            new MediaOptions
            {
                ContentType = "application/text",
                Slug = Guid.NewGuid().ToString()
            });
        Console.WriteLine($"Attachment created. Status code is {attachmentResponse.StatusCode}");
        Console.WriteLine($"Attachment ID is {attachmentResponse.Resource.Id}");
    }
    ```
    
    This method creates a generic JSON document (using the **JObject** type) that it adds to the database as a document. The code then uses the **CreateAttachmentAsync** method to stream an object into storage, and add a reference to this object in the attachments property of the document.

2.  In the **Worker** class, locate the **RetrieveDocWithAttachments** method:

    ```csharp 
        private async Task RetrieveDocWithAttachments()
        {
            var doc = await GetDocument();
    
            foreach (var attachment in client.CreateAttachmentQuery(doc.SelfLink))
            {
                var data = await client.ReadMediaAsync(attachment.MediaLink);
                StreamReader reader = new StreamReader(data.Media);
                string text = reader.ReadToEnd();
                Console.WriteLine($"Attachment is: {text}");
            }
        }
    ```
    
    This code retrieves a document using the **GetDocument** helper method and then executes a separate attachment query (using the **ReadMediaAsync** method) to read the attachment.

3.  Press F5 to run the application.

4.  At the prompt, type **G** to add a new document with an attachment.

5.  Note that the **id** of the new document is returned by the application, on the line starting **ID is** - note this value. The **id** of the attachment is also returned.

6.  At the prompt, type **H**, type the **id** of the document created in the previous step, and then press Enter to retrieve the document and its attachment.

7.  The application returns details of the document, and the contents of the attachment.

8.  At the prompt, press **X** to close the application.

9.  Close Visual Studio.

10. In Internet Explorer, in the Azure Portal, click **All Resources**, select the **20777a-sql-\<your name\>-\<the day\>** check box, and then click **Delete**.

11. On the **Delete Resources** blade, in the **Confirm delete** box, type **yes**, and then click **Delete**.

12. Close Internet Explorer and any other open windows.

    > **Note:** You should delete the collection at the end of the demonstration to avoid unexpected charges to you Azure subscription.

---
©2018 Microsoft Corporation. All rights reserved.

The text in this document is available under the [Creative Commons Attribution 3.0 License](https://creativecommons.org/licenses/by/3.0/legalcode), additional terms may apply. All other content contained in this document (including, without limitation, trademarks, logos, images, etc.) are **not** included within the Creative Commons license grant. This document does not provide you with any legal rights to any intellectual property in any Microsoft product. You may copy and use this document for your internal, reference purposes.

This document is provided "as-is." Information and views expressed in this document, including URL and other Internet Web site references, may change without notice. You bear the risk of using it. Some examples are for illustration only and are fictitious. No real association is intended or inferred. Microsoft makes no warranties, express or implied, with respect to the information provided here.
