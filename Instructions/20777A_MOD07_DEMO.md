# Module 7: Implementing Stream Processing with Cosmos DB

- [Module 7: Implementing Stream Processing with Cosmos DB](#module-7-implementing-stream-processing-with-cosmos-db)
    - [Lesson 1: Working with the Cosmos DB Change Feed](#lesson-1-working-with-the-cosmos-db-change-feed)
        - [Demo 1: Using the Change Feed Processor Library to Manage a Materialized View](#demo-1-using-the-change-feed-processor-library-to-manage-a-materialized-view)
            - [Task 1: Create a Cosmos DB account](#task-1-create-a-cosmos-db-account)
            - [Task 2: Generate dummy stock data](#task-2-generate-dummy-stock-data)
            - [Task 3: Consume the change feed](#task-3-consume-the-change-feed)
            - [Task 4: Consume the change feed with the Change Feed Processor Library](#task-4-consume-the-change-feed-with-the-change-feed-processor-library)
        - [Demo 2: Processing Data with Azure Functions](#demo-2-processing-data-with-azure-functions)
            - [Preparation](#preparation)
            - [Task 1: Create Cosmos DB Emulator collections](#task-1-create-cosmos-db-emulator-collections)
            - [Task 2: Consume the change feed using local emulators](#task-2-consume-the-change-feed-using-local-emulators)
            - [Task 3: Create an Azure Functions account](#task-3-create-an-azure-functions-account)
            - [Task 4: Create a CosmosDB collection and debug locally](#task-4-create-a-cosmosdb-collection-and-debug-locally)
            - [Task 5: Consume the change feed in Azure](#task-5-consume-the-change-feed-in-azure)
            - [Task 6: Clear down the demonstration environment](#task-6-clear-down-the-demonstration-environment)
    - [Lesson 2: Integrating Cosmos DB into streaming solutions](#lesson-2-integrating-cosmos-db-into-streaming-solutions)
        - [Demo 1: Capturing Kafka messages](#demo-1-capturing-kafka-messages)
            - [Task 1: Create a graph collection](#task-1-create-a-graph-collection)
            - [Task 2: Install IDEA, Graph Explorer, Zookeeper, and Kafka](#task-2-install-idea-graph-explorer-zookeeper-and-kafka)
            - [Task 3: Generate email log records in Kafka](#task-3-generate-email-log-records-in-kafka)
            - [Task 4: Retrieve email log records and construct a graph in CosmosDB](#task-4-retrieve-email-log-records-and-construct-a-graph-in-cosmosdb)
            - [Task 5: View the results](#task-5-view-the-results)
        - [Demo 2: Streaming Data from Cosmos DB to Spark](#demo-2-streaming-data-from-cosmos-db-to-spark)
            - [Task 1: Create the DeviceData Database and Temperatures Collection](#task-1-create-the-devicedata-database-and-temperatures-collection)
            - [Task 2: Configure and Run the Data Capture Application](#task-2-configure-and-run-the-data-capture-application)
            - [Task 3: Create an Azure Databricks Spark Notebook](#task-3-create-an-azure-databricks-spark-notebook)
            - [Task 4: Stream Data from the Cosmos DB Change Feed using Spark](#task-4-stream-data-from-the-cosmos-db-change-feed-using-spark)
        - [Demo 3: Combining Cosmos DB with Azure Stream Analytics](#demo-3-combining-cosmos-db-with-azure-stream-analytics)
            - [Task 1: Create an Azure IoT Hub and a Stream Analytics Job](#task-1-create-an-azure-event-hub-and-a-stream-analytics-job)
            - [Task 2: Connect the IoT Hub, Stream Analytics Job, and Cosmos DB Database](#task-2-connect-the-event-hub-stream-analytics-job-and-cosmos-db-database)
            - [Task 3: Stream Temperature Readings to Cosmos DB](#task-3-stream-temperature-readings-to-cosmos-db)
            - [Task 4: Clean Up Resources](#task-4-clean-up-resources)

## Lesson 1: Working with the Cosmos DB Change Feed

### Demo 1: Using the Change Feed Processor Library to Manage a Materialized View

**Scenario:** In this demonstration, you will use the change feed to capture and process changes made to the price of items in the stock market. Each item is identified by a four-character ticker. Each time the market price changes, it is recorded in a Cosmos DB database. You want to maintain a summary for each stock market item, showing the highest and lowest prices, the current price, and the volatility of the stock (how many times had the price changed). 

#### Task 1: Create a Cosmos DB account

1. Ensure that the **MT17B-WS2016-NAT** and **20777A-LON-DEV** virtual machines are running, and then log on to **20777A-LON-DEV** as **LON-DEV\\Administrator** with the password **Pa55w.rd**.
2. In File Explorer, go to **E:\\Demofiles\\Mod07**, right-click **Setup.cmd**, and then click **Run as administrator**.
3. In Internet Explorer, go to **http://portal.azure.com**, and sign in using the Microsoft account that is associated with your Azure Learning pass subscription.
4. In the Azure portal, in the left panel, click **Azure Cosmos DB**, and then click **+ Add**.
5. On the **Azure Cosmos DB** blade, under the **Resource Group** box, click **Create new**, type **20777Mod07**, and then click **OK**.
6. In the **Account name** box, type **20777-sql-&lt;your name&gt;-&lt;the day&gt;**, for example, **20777-sql-john-31**.
7. Click the **API** drop-down, note the options available, and then click **SQL**.
8. In the **Location** drop-down list, click the region closest to your current location, click **Review + create**, and then click **Create**. Wait for the Azure Cosmos DB to be created—this could take a few minutes.
9.  In the Azure portal, in the left pane, click **All resources**, and then click **20777-sql-&lt;your name&gt;-&lt;the day&gt;**.
10. On the **20777-sql-&lt;your name&gt;-&lt;the day&gt;** blade, click **Data Explorer**, and then click **New Database**.
11. On the **New Database** blade, in the **Database id** box, type **StockMarket**, and then click **OK**.
12. In the **SQL API** pane, click **New Collection**.
13. On the **Add Collection** blade, click **Use existing**, and then in the drop-down list, click **StockMarket**.
14. In the **Collection Id** box, type **StockPrices**.
15. Under **Storage capacity**, click **Unlimited**.
16. In the **Partition key** box, type **/Ticker**.
17. In the **Throughput (1,000 - 100,000 RU/s)** box, type **2000**, and then click **OK**.
18. In the **SQL API** pane, click **New Collection**.
19. On the **Add Collection** blade, click **Use existing**, and then in the drop-down list, click **StockMarket**.
20. In the **Collection Id** box, type **StockPricesSummary**.
21. In the **Throughput (400 - 10,000 RU/s)** box, type **2000**, and then click **OK**.
22. On the **20777-sql-&lt;your name&gt;-&lt;the day&gt;** blade, under **Settings**, click **Keys**.
23. Make a note of the **URI**, and **PRIMARY KEY** values.

#### Task 2: Generate dummy stock data

The next task in this demonstration is to generate some simulated stock price data into the **StockPrice** collection.

1. On the Windows Start menu, click **Visual Studio 2017**.
2. On the **File** menu, point to **Open**, and then click **Project/Solution**.
3. In the **Open Project** dialog box, go to **E:\\Demofiles\\Mod07\\Demo01\\StockMarketSimulation**, and then double-click **StockMarketSimulation.sln**.
4. In Solution Explorer, click **StockPriceChangeEvent.cs**.
5. On the **StockPriceChangeEvent.cs** tab, review the contents of the file, and observe that the **StockPriceChangeEvent** class represents a stock price quote for a point in time.
6. In Solution Explorer, click **StockItem.cs**.
7. On the **StockItem.cs** tab, review the contents of the file. The **GeneratePriceAlerts** method generates a new **StockPriceChangeEvent** for a stock ticker code every 500-5,000 milliseconds and writes it to the Cosmos DB database. The stock price and quote time are randomly generated based on previous values, using the **getPrice** and **getQuoteTime** methods.
8. In Solution Explorer, click **StockMarketDriver.cs**.
9. On the **StockMarketDriver.cs** tab, review the contents of the file. The **Run** method creates 100 instances of the **StockItem** class, generating a random ticker value for each one. The program’s Main method starts an instance of the **StockMarketDriver** class when the application runs.
10. In Solution Explorer, click **App.config**.
11. On the **App.config** tab, replace **\~URI\~** with the **URI**, and replace **\~KEY\~** with the **PRIMARY KEY** values that you noted earlier.
12. Press F5 to run the application.
13. Allow the application to run for approximately 60 seconds, and then press Enter to stop it.
14. In Internet Explorer, on the **20777-sql-&lt;your name&gt;-&lt;the day&gt;** blade, click **Data Explorer**.
15. In the **SQL API** pane, expand **StockMarket**, expand **StockPrices**, and then click **Documents**.
16. On the **Documents** tab, click a document and examine its contents, it should include the **Ticker**, **Price**, and **QuoteTime**. There will be one document for each price change.
17. Make a note of the **Ticker** for the document you selected.
18. Click **New SQL Query**, and enter the following query. Replace **&lt;Ticker&gt;** with the ticker you just noted:

    ```SQL
    SELECT * FROM c WHERE c.Ticker = '<Ticker>'
    ```

19. Click **Execute Query**, and scroll through the list of price changes recorded for this ticker.

#### Task 3: Consume the change feed

1. In Visual Studio, on the **File** menu, point to **Open**, and then click **Project/Solution**.
2. In the **Open Project** dialog box, go to **E:\\Demofiles\\Mod07\\Demo01\\StockAnalyzer** folder, and then double-click **StockAnalyzer.sln**. This solution contains two versions of an application to take the change feed from the stock data that you just generated and produce a highest price/lowest price analysis for each ticker value.
3. In Solution Explorer, under **StockAnalyzerLib**, click **StockPriceDocument.cs**. **StockAnalyzerLib** contains classes shared by both implementations of the application.
4. On the **StockPriceDocument.cs** tab, observe the definition of the **StockPriceDocument** class that represents a document from the change feed.
5. In Solution Explorer, under **StockAnalyzerLib**, click **StockPriceSummaryDocument.cs**. The **StockPriceSummaryDocument** class will hold the summary information generated by this application. Instances of this class are written out to another Cosmos DB collection.
6. In Solution Explorer, under **StockAnalyzer**, click **Worker.cs**.
7. On the **Worker.cs** tab, review the contents of the file. The **Run** method is the entry point for the class. In the **Run** method, the application connects to the source database, retrieves a list of partition key ranges, then passes each partition key range to an instance of the **ProcessChangesInPartitionAsync** method, creating one task for each partition key range.
8. Locate the **ProcessChangesInPartitionAsync** method. This method connects to the source partition change feed with the **CreateDocumentChangeFeedQuery** method, and then enters a loop, reading documents from the change feed and updating the target collection with the most recent data.
9. Locate the code that creates the **ChangeFeedOptions** object (line 123). The change feed query is configured to read the change feed from the start— **StartFromBeginning = true**. Notice the commented-out option to start the change feed from **DateTime.Now**.
10. In Solution Explorer, under **StockAnalyzer**, click **App.config**.
11. On the **App.config** tab, replace **\~URI\~** with the **URI**, and replace **\~KEY\~** with the **PRIMARY KEY** values that you noted earlier.
12. Press F5 to run the application.
13. Allow the application to run for approximately 60 seconds, and then press Enter to stop it. While the application runs, it displays the output of the ticker values that it’s updating.
14. In Internet Explorer, on the **Data Explorer** blade, in the **SQL API** pane, under **StockMarket**, expand **StockPricesSummary**, and then click **Documents**.
15. On the **Documents** tab, observe that there are only **100** documents in the collection, one for each ticker value.
16. Click any of the documents to review the contents. Its contents should include the highest and lowest prices recorded for the ticker, together with the current price (and quote time). The volatility index is a count of how many times the price has changed.
17. In the **SQL API** pane, right-click **StockPricesSummary**, and then click **Delete Collection**.
18. On the **Delete Collection** blade, in the **Confirm by typing the collection id** box, type **StockPricesSummary**, and then click **OK**.

#### Task 4: Consume the change feed with the Change Feed Processor Library

1. In Visual Studio, in Solution Explorer, under **StockAnalyzerUsingChangeFeedProcessorLibrary**, click **ChangeFeedObserver.cs**. The application performs the same aggregation as the **StockAnalyzer** project, but uses the **Change Feed Processor Library** to manage connections to the change feed.
2. On the **ChangeFeedObserver.cs** tab, review the contents of the file. The **StockPriceObserver** class implements the **IChangeFeedObserver** interface.
3. Locate the **ProcessChangesAsync** method (line 60). This is the method that consumes items from the change feed—the signature of this method is defined by the **IChangeFeedObserver** interface. Observe that documents from the change feed are delivered as an instance of **IReadOnlyList&lt;Document&gt;**. The application uses much of the same logic as the **StockAnalyzer** application to detect and update summary documents. Observe that there is no code in this class to deal directly with connecting to partition key ranges of the source database; this is handled by the **Change Feed Processor Library**.
4. Locate the **StockPricesObserverFactory** class and the **CreateObserver** method (line 113). This class is used to create new instances of the observer.
5. In Solution Explorer, under **StockAnalyzerUsingChangeFeedProcessorLibrary**, click
 **Program.cs**.
6. On the **Program.cs** tab, locate the **ChangeFeedOptions** instance in the **Main** method ( line 32). Observe that the **StartFromBeginning** property is set **true**.
7. Locate the **// Create the observer factory and event host for handling change feed events** comment  (line 76). The code creates a new instance of the **StockPricesObserverFactory** class, and then uses this to start an observer that is allowed to run for 60 seconds.
8. In Solution Explorer, under **StockAnalyzerUsingChangeFeedProcessorLibrary**, click **App.config**.
9. On the **App.config** tab, notice that the application requires an additional collection to store lease information. Replace **\~URI\~** with the **URI**, and replace **\~KEY\~** with the **PRIMARY KEY** values that you noted earlier.
10. In Solution Explorer, right-click **StockAnalyzerUsingChangeFeedProcessorLibrary**, and then click **Set as Startup Project**.
11. Press F5 to run the application.
12. Allow the application to run to completion (it will execute for approximately 60 seconds). As before, while the application runs, it displays output of the ticker values that it’s updating. Depending on the number of records in your StockPrice collection change feed, the application might stop processing after a few seconds because it has consumed the entire change feed.
13. Close Visual Studio.
14. In Internet Explorer, on the **Data Explorer** blade, in the **SQL API** pane, click the refresh button. Notice that the application created a new **StockPricesSummary** collection, and a **StockPricesLease** collection.
15. Expand **StockPricesSummary**, and then click **Documents**.
16. On the **Documents** tab, click any of the documents to review the contents.
17. In the **SQL API** pane, right-click **StockPricesSummary**, and then click **Delete Collection**.
18. On the **Delete Collection** blade, in the **Confirm by typing the collection id** box, type **StockPricesSummary**, and then click **OK**.
19. Leave Internet Explorer open for the next demonstration.

### Demo 2: Processing Data with Azure Functions

#### Preparation

1. In Internet Explorer, open a new tab, and go to **https://www.microsoft.com/en-us/download/details.aspx?id=56115** to download .NET Framework 4.7.1.
2. Select your language, and then click **Download**.
3. In the message box, click **Run** to start the installer.
4. In the **Microsoft .NET Framework** dialog box, on the **.NET Framework 4.7.1 Setup** page, select the **I have read and accept the license terms** check box, and then click **Install**.
5. On the **Do you want Setup to close your programs?** page, click **Yes**.
6. On the **Installation Is Complete** page, click **Finish**.
7. In the **Microsoft .NET Framework** dialog box, click **Restart Now**.
8. Log on to **20777A-LON-DEV** as **LON-DEV\\Administrator** with the password **Pa55w.rd**.
9. On the Start menu, click **Visual Studio 2017**.
10. In Visual Studio, on the **Tools** menu, click **Extensions and Updates**.
11. In the **Extensions and Updates** dialog box, click **Updates**.
12. In the Updates list, click **Azure Functions and Web Jobs Tools**, and then click **Update**.
13. Wait for the download to complete.
14. In the **Extensions and Updates** window, click **Close**, and then close Visual Studio. When Visual Studio closes, the VSIX Installer will start automatically.
15. In the **VSIX Installer** dialog box, click **Modify**.
16. When the installation completes, click **Close**.
17. In File Explorer, go to **E:\Resources**, right-click **get_cosmos_db_emulator.ps1**, and then click **Run with PowerShell**.
18. If an **Execution Policy Change** message is displayed, press **Y**.
19. Wait while the script downloads and installs the Cosmos DB local emulator if it’s not already present.

#### Task 1: Create Cosmos DB Emulator collections

In this task, you will configure the Cosmos DB Emulator with collections that correspond to the collections on your Cosmos DB account in Azure.

1. On the Start menu, click **Azure Cosmos DB Emulator**, and then click **Azure Cosmos DB Emulator**. When the emulator starts, it will automatically open a new tab in Internet Explorer.
2. In Internet Explorer, on the **Azure Cosmos DB Emulator** page, click **Explorer**.
3. On the **Explorer** page, click **New Collection**.
4. On the **Add Collection** blade, in the **Database id** box, type **StockMarket**.
5. In the **Collection Id** box, type **StockPrices**.
6. Under **Storage Capacity**, click **Unlimited**.
7. In the **Partition key** box, type **/Ticker**, and then click **OK**.
8. In the **SQL API** pane, right-click **StockMarket**, and then click **New Collection**.
9.  On the **Add Collection** blade, in the **Collection Id** box, type **StockPricesSummary**, and then click **OK**.
10. In the **SQL API** pane, right-click **StockMarket**, and then click **New Collection**.
11. On the **Add Collection** blade, in the **Collection Id** box, type **StockLeases**, and then click **OK**.

#### Task 2: Consume the change feed using local emulators

1. On the Start menu, click **Visual Studio 2017**.
2. In Visual Studio 2017, on the **File** menu, point to **Open**, and then click **Project\Solution**.
3. In the **Open Project** dialog box, go to **E:\Demofiles\Mod07\Demo02\StockPricesTriggerFunctionApp**, and then double-click **StockPricesTriggerFunctionApp.sln**.
4. In Solution Explorer, click **StockPriceDocument.cs**.
5. On the **StockPriceDocument.cs** tab, review the contents of the file. This is the same stock price class as the one that you used in the application in the previous demonstration.
6. In Solution Explorer, click **StockPriceSummaryDocument.cs**.
7. On the **StockPriceSummaryDocument.cs** tab, review the contents of the file. This is the same stock price summary class that you used in the application in the previous demonstration.
8. In Solution Explorer, click **StockPricesTrigger.cs**.
9. On the **StockPricesTrigger.cs** tab, review the contents of the file. This file contains the Azure Function definition, in the **StockPricesTrigger** class. The **Run** method of the class contains the processing logic. The parameters passed to the method define the Cosmos DB connections used by the function; note that the method takes a **LeaseCollectionName** parameter (the function uses the Change Feed Processor Library). The logic of the Run method is very similar to that used in the application in the previous demonstration:
     - A list of one or more change feed documents is read from the Cosmos DB change feed; this arrives in the variable called **input**.
     - For each document received, any existing summary document for the corresponding Ticker value is read from the summary collection using an input binding.
     - The summary document is updated with the values from the document received from the change feed.
     - The summary document is written back to the summary collection using an output binding.
10. In Solution Explorer, click **local.settings.json**.
11. On the **local.settings.json** tab, review the contents of the file. This file configures the connection strings for the Azure Storage account, Azure Web Jobs dashboard, and Cosmos DB connection string used by the function. Note that these properties are already configured to use the local emulators.
12. Press F5 to run the function in the Azure Functions emulator. There will be a short delay while Visual Studio downloads the correct version of the Azure Functions CLI tools, and starts the Azure Storage emulator. The Azure Functions emulator runs in a command prompt window.
13. In the command prompt window, when you see the **Job host started** message, it’s running and ready to process.
14. In Internet Explorer, on the **Azure Cosmos DB Emulator** page, expand **StockPrices**, and then click **Documents**.
15. On the **Documents** tab, click **New Document**.
16. Edit the new document definition as shown below, and then click **Save**:

    ```JSON
    {
      "Ticker": "XXXX",
      "Price": 100,
      "QuoteTime": "2018-07-18T00:00:00Z"
    }
    ```

17. In the Azure Functions emulator command prompt window, after a short delay, you should see a sequence of messages, indicating that the StockPricesTrigger function executed. The final message should begin:

    ```Text
    Executed 'StockPricesTrigger' (Succeeded, Id=...)
    ```

18. In Internet Explorer, on the **Azure Cosmos DB Emulator** page, expand **StockPricesSummary**, and then click **Documents**.
19. On the **Documents** tab, click the first document in the list (there should be only one). Observe that the summary information for the Ticker value **XXXX** reflects the document that you added to the **StockPrices** collection.
20. Under **StockPrices**, and then click **Documents**.
21. On the **Documents** tab, click **New Document**.
22. Edit the new document definition as shown below, and then click **Save**:

    ```JSON
    {
      "Ticker": "XXXX",
      "Price": 110,
      "QuoteTime": "2018-07-18T01:00:00Z"
    }
    ```

23. Wait five seconds for the function to run, under **StockPricesSummary**, click **Documents**.
24. On the **Documents** tab, click the first document in the list (there should still only be one). Notice that the summary information for the Ticker value **XXXX** now reflects the document that you added to the **StockPrices** collection.
25. Close the Azure Cosmos DB Emulator page.
26. In the Azure Functions emulator command prompt window, to stop the emulator, press Ctrl+C.
27. If the command prompt window does not close by itself after 30 seconds, close the window manually (using the **X** in the upper right corner).
28. On the Windows toolbar, right-click the **Azure Storage emulator** icon (the Windows symbol), and then click **Exit**. If the icon is not visible, in the bottom-right, click the **Show hidden icons** button (**^**).
29. In the **Microsoft Azure Emulator** dialog box, click **OK**.
30. On the Windows toolbar, right-click the **Azure Cosmos DB Emulator** icon, and then click **Exit**. If the icon is not visible, in the bottom-right, click the **Show hidden icons** button (**^**).

#### Task 3: Create an Azure Functions account

1. In Internet Explorer, in the Azure portal, click **+ Create a resource**.
2. On the **New** blade, in the search box, type **Function App**, and then press Enter.
3. On the **Everything** blade, click **Function App**, and then click **Create**.
4. In the **App name** box, type **20777-func-&lt;your name&gt;-&lt;the day&gt;**, for example, **20777-func-john-31**.
5. Under **Resource Group**, click **Use existing**, and then in the drop-down list, click **20777Mod07**.
6. In the **Location** box, select a location near you; you should use the same location that you used for your Cosmos DB account if it appears in the list.
7. Make a note of the **Storage** value.
8. Under **Application Insights**, click **Off**, and then click **Create**.
9. When deployment is complete, click **All resources**.
10. On the **All resources** blade, click the name of the **Storage account** that you noted in step 7.
11. On the storage account blade, under **Settings**, click **Access keys**.
12. Under **key1**, make a note of the **Connection string** value.
13. In Visual Studio, on the **local.settings.json** tab, edit the values of the **AzureWebJobsDashboard** and **AzureWebJobsStorage** properties to match the **Connection string** that you noted in the previous step.

#### Task 4: Create a CosmosDB collection and debug locally

1. In Internet Explorer, in the Azure portal, click **All resources**.
2. On the **All resources** blade, click **20777-sql-&lt;your name&gt;-&lt;the day&gt;**, click **Data Explorer**, and then click **New Collection**.
3. On the **Add Collection** blade, click **Use existing**, and then in the drop-down list, click **StockMarket**.
4. In the **Collection Id** box, type **StockPricesSummary**.
5. In the **Throughput (400 - 10,000 RU/s)** box, type **2000**, and then click **OK**.
6. On the **20777-sql-&lt;your name&gt;-&lt;the day&gt;** blade, under **Settings**, click **Keys**.
7. Make a note of the **PRIMARY CONNECTION STRING** value.
8.  In Visual Studio, on the **local.settings.json** tab, edit the value of the **StockPricesTrigger_ConnectionString** property to match the **PRIMARY CONNECTION STRING** value that you recorded in the previous step. Note that the local.settings.json file only affects the behavior of the Azure Functions emulator; values in this file are not published to Azure.
9. Press F5 to run the application.
10. When the Azure Functions emulator has started, in Internet Explorer, in the Azure portal, click **All resources**.
11. On the **All resources** blade, click **20777-sql-&lt;your name&gt;-&lt;the day&gt;**, and then click **Data Explorer**.
12. In the **SQL API** pane, expand **StockPrices**, and then click **Documents**.
13. On the **Documents** tab, click **New Document**.
14. Edit the new document definition as shown below, and then click **Save**:

    ```JSON
    {
      "Ticker": "XXXX",
      "Price": 100,
      "QuoteTime": "2018-07-18T00:00:00Z"
    }
    ```

15. In the Azure Functions emulator command prompt window, after a short delay, you should see a sequence of messages indicating that the StockPricesTrigger function executed. The final message should begin:

    ```Text
    Executed 'StockPricesTrigger' (Succeeded, Id=...)
    ```

16. In Internet Explorer, in the **SQL API** pane, expand **StockPricesSummary**, and then click **Documents**.
17. On the **Documents** tab, click the first document in the list (there should be only one). Observe that the summary information for the Ticker value **XXXX** reflects the document that you added to the **StockPrices** collection.
18. Under **StockPrices**, click **Documents**.
19. On the **Documents** tab, click **New Document**.
20. Edit the new document definition as shown below, and then click **Save**:

    ```JSON
    {
      "Ticker": "XXXX",
      "Price": 110,
      "QuoteTime": "2018-07-18T01:00:00Z"
    }
    ```

21. Wait five seconds for the function to run, under **StockPricesSummary**, and then click **Documents**.
22. On the **Documents** tab, click the first document in the list (there should still be only one). The summary information for the Ticker value **XXXX** should now reflect the document that you added to the **StockPrices** collection.
23. In the Azure Functions emulator command prompt window, to stop the emulator, press Ctrl+C.
24. If the command prompt window does not close by itself after 30 seconds, close the window manually (using the **X** in the upper-right corner).

#### Task 5: Consume the change feed in Azure

1. In Internet Explorer, in the Azure portal, click **All resources**.
2. On the **All resources** blade, click **20777-func-&lt;your name&gt;-&lt;the day&gt;**.
3. On the **20777-func-&lt;your name&gt;-&lt;the day&gt;** blade, on the **Overview** tab, click **Stop**.
4. In the **Function Apps** pane, click the **Refresh** button by the **20777-func-&lt;your name&gt;-&lt;the day&gt;**
5. On the **Overview** tab, under **Configured features**, click **Application settings**.
6. On the **Application settings** tab, under **Application settings**, click **+ Add new setting**.
7. In the **Enter a name** box, type **StockPricesTrigger_ConnectionString**.
8. In the **Enter a value** box, type the **PRIMARY CONNECTION STRING** value that you noted earlier then, and then click **Save**. If you do not add this application setting, the function will not be able to connect to your Cosmos DB account, and it will fail silently.
9. In Visual Studio, on the **Build** menu, click **Publish StockPricesTriggerFunctionApp** to deploy the function.
10. On the **StockPricesTriggerFunctionApp** tab, click **New Profile**.
11. In the **Pick a publish target** dialog box, click **Select Existing**, and then click **Publish**.
12. In the **App Service** dialog box, click **Sign In**.
13. In the **Sign in to your account** dialog box, sign in to Azure with you Azure pass credentials.
14. In the **App Service** dialog box, under **Search**, expand **20777Mod07**, click **20777-func-&lt;your name&gt;-&lt;the day&gt;**, and then click **OK**.
15. Publication begins immediately; publication is complete when the **Output** pane displays the **Publish completed** message.
16. In Internet Explorer, in the Azure portal, click **All resources**, and then click **20777-func-&lt;your name&gt;-&lt;the day&gt;**.
17. On the **20777-func-&lt;your name&gt;-&lt;the day&gt;** blade, on the **Overview** tab, click **Start**.
18. In the **Function Apps** pane, expand **Functions (Read Only)**, click **StockPricesTrigger**. The **function.json** description of the function is displayed, but that it’s grayed out, and a message notifies you that the app is in read-only mode because it was published from Visual Studio.
19. Review the contents of function.json. Note that the function definition includes a **cosmosDBTrigger** binding, and that the binding has the **name** property with the value **input**. This is the name of the variable in the C\# code that contains the documents received from the change feed.
20. Click **All resources**, click **20777-sql-&lt;your name&gt;-&lt;the day&gt;**, and then click **Data Explorer**.
21. In the **SQL API** pane, expand **StockPrices**, and then click **Documents**.
22. On the **Documents** tab, click **New Document**.
23. Edit the new document definition as shown below, and then click **Save**:

    ```JSON
    {
      "Ticker": "ABCD",
      "Price": 100,
      "QuoteTime": "2018-07-18T00:00:00Z"
    }
    ```

24. Wait five seconds, expand **StockPricesSummary**, and then click **Documents**.
25. On the **Documents** tab, click the second document in the list (there should now be two of them). Observe that the summary information for the Ticker value **ABCD** reflects the document that you added to the **StockPrices** collection.
26. Under **StockPrices**, click **Documents**.
27. On the **Documents** tab, click **New Document**.
28. Edit the new document definition as shown below, and then click **Save**:

    ```JSON
    {
      "Ticker": "ABCD",
      "Price": 110,
      "QuoteTime": "2018-07-18T01:00:00Z"
    }
    ```

29. Wait five seconds for the function to run, and then, under **StockPricesSummary**, click **Documents**.
30. On the **Documents** tab, click the second document in the list. The summary information for the Ticker value **ABCD** now reflects the changes caused by the document that you added to the **StockPrices** collection.

#### Task 6: Clear down the demonstration environment

1. In the **SQL API** pane, right-click **StockMarket**, and then click **Delete Database**.
2. On the **Delete Database** blade, in the **Confirm by typing the database id** box, type **StockMarket**, and then click **OK**.
3. Close Visual Studio. Leave Internet Explorer open for the next demonstration.

## Lesson 2: Integrating Cosmos DB into streaming solutions

### Demo 1: Capturing Kafka messages

**Scenario:** This demonstration simulates logging email traffic sent internally inside an organization. The data is captured by usig Kafka. A Kafka client then reads this data and uses it to construct a graph in Cosmos DB showing who has emailled who.

#### Task 1: Create a graph collection

1. In the Azure portal, in the left panel, click **Azure Cosmos DB**, and then click **+ Add**.
2. On the **Azure Cosmos DB** blade, in the **Resource Group** drop-down list, click **20777Mod07**.
3. In the **Account name** box, type **20777-graph-&lt;your name&gt;-&lt;the day&gt;**, for example, **20777-graph-john-31**.
4. In the **API** drop-down list, click **Gremlin (graph)**.
5. In the **Location** drop-down list, click the region closest to your current location, click **Review + create**, and then click **Create**.
6. Wait for the Azure Cosmos DB to be created. This could take a few minutes.
7. In the Azure portal, in the left panel, click **20777-graph-&lt;your name&gt;-&lt;the day&gt;**.
8. On the **20777-graph-&lt;your name&gt;-&lt;the day&gt;** blade, click **Overview**.
9. On the **Overview** blade, make a note of the **Gremlin Endpoint** value, and then click **Data Explorer**.
10. In the **GREMLIN API** pane, click **New Database**.
11. On the **New Database** blade, in the **Database id** box, type **emaildatabase**, and then click **OK**.
12. In the **GREMLIN API** pane, click **New Graph**.
13. On the **Add Graph** blade, under **Database id**, click **Use existing**, and then in the drop-down list, click **emaildatabase**.
14. In the **Graph Id** box, type **emailgraph**.
15. In the **Throughput (400 - 10,000 RU/s)** box, type **10000**, and then click **OK**.
16. On the **20777-graph-&lt;your name&gt;-&lt;the day&gt;** blade, under **SETTINGS**, click **Keys**.
17. Make a note of the **URI**, and **PRIMARY KEY** values.

#### Task 2: Install IDEA, Graph Explorer, Zookeeper, and Kafka

> **Note:** Kafka is available as a service in HDInsight, but to keep costs down this demonstration installs and runs a local version of Kafka on your virtual machine. Kafka relies on Java, Node.js, and Apache Zookeeper. If you installed the Java Development Kit in an earlier module in this course, you can skip step 1 in this task and start at step 2.

1. To install the **Java Development Kit (JDK)** on 20777A-LON-DEV, complete the following steps:
   1. In Internet Explorer, open a new tab , and then go to **http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html**.
   2. If the **Your Choices Regarding Cookies on this Site** dialog box appears, where applicable click **NO**, and then click **SUBMIT PREFERENCES**.
   3. If the **Preferences Submitted** dialog box appears, click **CLOSE**. 
   4. In the **Java SE Development Kit 8u181** section, click **Accept License Agreement**, and then click **jdk-8u181-windows-x64.exe**. 
   5. In the message box, click **Run**. 
   6. In the **Java SE Development Kit 8 Update 181 (64-bit) - Setup** dialog box, on the **Welcome to the Installation Wizard for Java SE Development Kit 8 Update 181** page, click **Next**.
   7. On the **Select optional features to install from the list below** page, click **Next**.
   8. If the **Change in License Terms** dialog box appears, click **OK**.
   9. On the **Destination Folder** page, click **Next**.
   10. Wait for installation to complete, and then click **Close**.
   11. In Internet Explorer, close the current tab.
2. In File Explorer, go to **E:\Resources**, right-click **get_node.js.ps1** then click **Run with PowerShell**.
3. If an **Execution Policy Change** message is displayed, press **Y**. The script downloads and installs Node.js if it is not already present.
4. Right-click **get_7z_zookeeper_kafka.ps1**, and then click **Run with PowerShell**. The script downloads and extracts Zookeeper and Kafka to folders on the **E:** drive and configures some environment variables.
5. Right-click **get_IDEA.ps1**, and then click **Run with PowerShell**. The script downloads and extracts **IntelliJ IDEA** to **E:\IDEA**; the size of installer is about 600 MB, so this step will take some time to complete. You can continue with other steps in this stage whilst the script is running.
6. In File Explorer, go to the **E:\Zookeeper\conf** folder, right-click **zoo_sample.cfg**, and then click **Rename**.
7. Type **zoo**, and then press Enter.
8. Right-click **zoo.cfg**, and then click **Open with**.
9. In the **Windows can't open this type of file (.cfg)** dialog box, click **Try an app on this PC**, click **Microsoft Visual Studio Version Selector**, and then click **OK**.
10. In Visual Studio 2017, on the **zoo.cfg** tab, edit line 12 (that begins **dataDir=**) as shown below:

    ```Text
    dataDir=E:/zookeeper/data
    ```

11. On the **File** menu, click **Save zoo.cfg**.
12. Close Visual Studio.
13. On the Start menu, type **cmd**, and then press Enter.
14. At the command prompt, type the following command, and then press Enter to start the Zookeeper service:

    ```Cmd
    E:\Zookeeper\bin\zkServer.cmd
    ```

    Zookeeper should start; the final message on the console should finish with the message shown below. Leave the command prompt window open (if you close it, Zookeeper will stop):

    ```Text
    binding to port 0.0.0.0/0.0.0.0:2181
    ```

15. In File Explorer, go to **E:\Kafka\config**, right-click **server.properties**, and then click **Open with**.
16. In the **Windows can't open this type of file (.properties)** dialog box, click **Try an app on this PC**, click **Microsoft Visual Studio Version Selector**, and then click **OK**.
17. In Visual Studio 2017, on the **server.properties** tab, edit line 60 (that begins **log.dirs=**) as shown below:

    ```Text
    log.dirs=E:/Kafka/kafka-logs
    ```

18.  On the **File** menu, click **Save server.properties**.
19. Close Visual Studio.
20. On the Start menu, type **cmd**, and then press Enter to start another command prompt.
21. At the command prompt, type the following command, and then press Enter to start the Kafka service running:

    ```Cmd
    E:
    cd Kafka
    .\bin\windows\kafka-server-start.bat .\config\server.properties
    ```

    The final message on the console should end:

    ```Text
    [KafkaServer id=0] started (kafka.server.KafkaServer)
    ```

22. On the Start menu, type **cmd**, and then press Enter to start a further command prompt.
23. At the command prompt, type the following command, and then press Enter to create a Kafka topic named **emailtopic**:

    ```Cmd
    E:
    cd Kafka
    .\bin\windows\kafka-topics --create --zookeeper localhost:2181 --topic emailtopic --partitions 5 --replication-factor 1
    ```

24. The response **Created topic "emailtopic".** should be displayed.
25. At the command prompt, type **exit**, and then press Enter to close this instance of the command prompt (leave the other command prompt windows running).

#### Task 3: Generate email log records in Kafka

> **Note:** Wait for the **get_IDEA.ps1** PowerShell script to complete before continuing with this task.

1. In File Explorer, go to **E:\IDEA\bin**, and then double-click **idea.exe**.
2. In the **Complete Installation** dialog box, accept the default values, and then click **OK**.
3. In the **JetBrains Privacy Policy** dialog box, review the policy, and then click **Accept**.
4. In the **Data Sharing** dialog box, click **Don’t send**.
5. In the **Customize IntelliJ IDEA** dialog box, click **Skip Remaining and Set Defaults**.
6. In the **Welcome to IntelliJ IDEA** dialog box, click **Open**.
7. In the **Open File or Project** dialog box, in the text box, type **E:\Demofiles\Mod07\Demo03\KafkaMessageProducer**, and then click **OK**.
8. If the **Tip of the Day** dialog box appears, clear the **Show tips on startup** check box, and then click **Close**.
9. This project is a Java application that simulates the capture of an organization’s internal email traffic for logging purposes.
10. In the Project tree, under **KafkaMessageProducer**, under **src**, under **main**, under **java**, double-click **EmailMessage**.
11. On the **EmailMessage.java** tab, if the message **Project SDK is not defined** is displayed, click **Setup SDK**, and then complete the following steps:
    1. In the **Select Project SDK** dialog box, click **Configure**.
    2. In the **Configure SDK** dialog box, click **+**, and then click **JDK**.
    3.  In the **Select Home Directory for JDK** dialog box, in the text box, type **C:\Program Files\Java\jdk1.8.0_181**, and then click **OK**.
    4.  In the **Configure SDK** dialog box, click **OK**.
    5.  In the **Select Project SDK** dialog box, click **OK**.
12. On the **EmailMessage.java** tab, review the contents of the **EmailMessage** class. This class represents email messages. It includes the address of the sender, and a list of recipients. The other fields specify the subject of the email, the date sent, and the text comprising the body of the email message.
13. In the Project tree, under **KafkaMessageProducer**, under **src**, under **main**, under **java**, double-click **EmailMessageProducer**.
14. On the **EmailMessageProducer** tab, review the contents of the **EmailMessageProducer** class. This class simulates users sending email message which have been captured by using Kafka. This class generates **EmailMessage** objects containing random email addresses with random subject lines and random text (using English words and names). Messages will be posted to the Kafka **emailtopic** topic that you created in the previous stage, for processing by a Kafka receiver.
15. On the **Run** menu, click **Edit Configurations**.
16. In the **Run/Debug Configurations** dialog box, in the **EmailMessageProducer** configuration, review the value of the **Program arguments** setting (it specifies that the application will connect to Kafka running on the local machine, and post messaged to the emailtopic topic), and then click **OK**.
17. On the **Run** menu, click **Run ‘EmailMessageProducer’**.
18. Allow the application to run for about 30 seconds; you should see messages starting **Message sent to topic emailtopic** in the application log.
19. On the **Run** menu, click **Stop ‘EmailMessageProducer’**.

#### Task 4: Retrieve email log records and construct a graph in CosmosDB

1. In IDEA, on the **File** menu, click **Open**.
2. In the **Open File or Project** dialog box, in the text box, type **E:\Demofiles\Mod07\Demo03\KafkaMessageConsumer**, and then click **OK**.
3. In the **Open Project** dialog box, click **This Window**.
4. The application in this project subscribes to the Kafka topic containing the captured email messages, **emailtopic**. The messages are used to construct a graph of email communication.
5. In the Project tree, expand **KafkaMessageConsumer**, expand **src**, expand **main**, expand **java**, and then double-click **EmailMessage**.
6. On the **EmailMessage.java** tab, observe that this is the same message class that’s used in the producer application.
7. In the Project tree, under **KafkaMessageComsumer**, under **src**, under **main**, under **java**, double-click **EmailMessageConsumer**.
8. On the **EmailMessageConsumer.java** tab, observe that this class connects to the Kafka topic, deserializes each message into an **EmailMessage** object, and then calls the **addMsgToGraph** method to construct a graph with the following nodes:

   - The email message.
   - The sender.
   - The recipients.
   - The words in the subject line.

9. Locate the **addMsgToGraph** method (line 76). This method uses the information received from Kafka to add nodes and edges to a Cosmos DB graph database using Gremlin queries. Point out the Gremlin queries used by this method.
10. Locate the **performQuery** method (line 136). This method sends queries to the Cosmos DB database using the Java API.
11. In the Project tree, double-click **params.yaml**.
12. On the **params.yaml** tab, edit the parameters to allow the application to connect to your Cosmos DB Gremlin API instance:
    - Replace **\~Gremlin Endpoint\~** with the **Gremlin** **Endpoint** value that you noted earlier, discarding the leading **https://** and the trailing **:443/**—the value should be **20777-graph-&lt;your name&gt;-&lt;the day&gt;.gremlin.cosmosdb.azure.com**.
    - Replace **\~KEY\~** with the **PRIMARY KEY** value that you noted earlier.
13. On the **Run** menu, click **Run ‘EmailMessageConsumer’**.
14. Allow the application to run for about 30 seconds. Observe that log messages are shown as records that are consumed from Kafka and added to Cosmos DB.
15. On the **Run** menu, click **Stop ‘EmailMessageConsumer’**.
16. Close IDEA.
17. In the **Confirm Exit** dialog box, click **Exit**.

#### Task 5: View the results

1. In File Explorer, go to **E:\Demofiles\Mod07\Demo03**, and then double-click **run_npm_install.cmd**.
2. On the Start menu, click **Visual Studio 2017**.
3. On the **File** menu, point to **Open**, and then click **Project/Solution**.
4. In the **Open Project** dialog box, go to **E:\Demofiles\Mod07\Demo03\GraphExplorer**, and then double-click **GraphExplorer.sln**. This application is a copy of the Graph Explorer utility used in earlier modules.
5. In Visual Studio, in Solution Explorer, click **appsettings.json**.
6. On the **appsettings.json** tab, edit the **DocumentDBConfig** section of the file as shown below:
    - Replace **\~URI\~** with the **URI** value that you noted earlier.
    - Replace **\~GREMLIN URI\~** with the **Gremlin Endpoint** value that you noted earlier, omitting the leading **https://**. The value should be **20777-graph-&lt;your name&gt;-&lt;the day&gt;.gremlin.cosmosdb.azure.com:443/**.
    - Replace **\~KEY\~** with the **PRIMARY KEY** value that you noted earlier:

    ```JSON
    "DocumentDBConfig": {
      "endpoint": "~URI~",
      "gremlinURI": "~GREMLIN URI~",
      "authKey": "~KEY~",
      "database": "emaildatabase",
      "collection": "emailgraph"
    }
    ```

7. Press F5 to run the application.
8. In Internet Explorer, when the Graph Explorer web application starts, click **Execute** to run the default query **g.V(); g.E()**.This query displays all nodes and edges, and might take a few seconds to appear.
9. Zoom in to the graph visualization, and click any **participant** node. Make a note of the **sender/recipient** property—the values will be an email address.
10. To find details of all the emails sent to this participant, in the query box, type the following, replacing **\~participant\~** with the **sender/recipient** value that you noted in the previous step:

    ```Gremlin
    g.V().hasLabel('participant').has('sender/recipient', '~participant~').inE('received by').outV()
    ```

11. Click **Execute**.
12. Click one of the **email_message** nodes returned by the query, and note one of the words in the value of the **subject** property.
13. To find the names of all the participants that this participant has communicated with as the sender of one or more emails, in the query box, type the following, replacing **\~participant\~** with the **sender/recipient** value that you noted in an earlier step:

    ```Gremlin
    g.V().hasLabel('participant').has('sender/recipient', '~participant~').outE ('sent').inV().outE('received by').inV().values('sender/recipient')
    ```

14. Click **Execute**. Note that it’s possible that the participant did not send any emails, in which case this query will not return any results; if this happens, go back to step 8 and choose another participant.
15. To find all the messages with a given word in the subject, in the query box, type the following, replacing **\~word\~** with the **subject** value that you noted in a previous step:

    ```Gremlin
    g.V().hasLabel('subject_line').has('subject_line_word', '~word~').outE('applies to').inV()
    ```

16. Click **Execute**. Review the results, and then close the web application.
17. Close Visual Studio.
18. In the Azure portal, on the **20777-graph-&lt;your name&gt;-&lt;the day&gt;** blade, click **Data Explorer**.
19. On the **Data Explorer** blade, right-click **emailgraph**, and then click **Delete Graph**.
20. In the **Delete Graph** pane, in the **Confirm by typing the graph id** box, type **emailgraph**, and then click **OK**.
21. In the command prompt window running Kafka, press Ctrl+C.
22. At the **Terminate batch job (Y/N)?** message, press **Y**, and then press Enter.
23. Close the command prompt window.
24. In the command prompt window running Zookeeper, press Ctrl+C.
25. At the **Terminate batch job (Y/N)?** message, press **Y**, and then press Enter.
26. Close the command prompt window.
27. Leave Internet Explorer open for the next demonstration.

### Demo 2: Streaming Data from Cosmos DB to Spark

**Scenario:** This demonstration shows how to use the Cosmos DB Connector for Spark to stream data from the change feed for a collection. The collection holds documents that record the temperatures reported by a series of devices. A seperate application acts as data feed that writes to this collection. You will use a Spark Notebook in Azure DataBricks to process the data as it arrives in the Cosmos DB database, and display a graph that shows the maximum and minimum temperatures for each device, together with the number of readings taken. The data will be processed at 15 second intervals.

#### Task 1: Create the DeviceData Database and Temperatures Collection

1. In Internet Explorer, in the Azure portal, click **All resources**.
2. On the **All resources** blade, click **20777-sql-&lt;your name&gt;-&lt;the day&gt;**, and then click **Data Explorer**.
3. In the **SQL API** pane, click **New Database**.
4. On the **New Database** blade, in the **Database id** box, type **DeviceData**, and then click **OK**.
5. In the **SQL API** pane, click **New Collection**.
6. On the **Add Collection** blade, click **Use existing**, and then in the drop-down list, click **DeviceData**.
7. In the **Collection Id** box, type **Temperatures**.
8. Under **Storage capacity**, click **Unlimited**.
9. In the **Partition key** box, type **/deviceid**.
10. In the **Throughput (1,000 - 100,000 RU/s)** box, type **2500**, and then click **OK**.
11. On the **20777-sql-&lt;your name&gt;-&lt;the day&gt;** blade, under **SETTINGS**, click **Keys**.
12. Make a note of the **URI**, and **PRIMARY KEY** values.

#### Task 2: Configure and Run the Data Capture Application

1. On the Start menu, click **Visual Studio 2017**.
2. On the **File** menu, point to **Open**, and then click **Project/Solution**.
3. In the **Open Project** dialog box, go to **E:\Demofiles\Mod07\Demo04\CosmosDBDeviceDataCapture**, and then double-click **CosmosDBDeviceDataCapture**. This is a copy of the same application that was used in the demonstration in Module 1. The application generates a series of documents following the structure shown by the class in the **ThermometerReading.cs** file:

    ```CSharp
    public class ThermometerReading
    {
      [JsonProperty("id")]
      public string ID { get; set; }

      [JsonProperty("deviceid")]
      public string DeviceID { get; set; }

      [JsonProperty("temperature")]
      public double Temperature { get; set; }

      [JsonProperty("time")]
      public long Time { get; set; }

      ...
    }
    ```

4. In Solution Explorer, click **App.config**.
5. On the **App.config** tab, replace **\~URI\~** with the **URI**, and replace **\~KEY\~** with the **PRIMARY KEY** values that you noted earlier for your **20777-sql-&lt;your name&gt;-&lt;the day&gt;** Cosmos DB account.
6. Press F5 to build and run the application.
7. Allow it to run for just a few seconds, and then press Enter to stop it.
8. In Internet Explorer, on the **20777-sql-&lt;your name&gt;-&lt;the day&gt;** blade, click **Data Explorer**. 
9. In the **SQL API** pane, expand **DeviceData**, expand **Temperatures**, and then click **Documents**, verify that a number of documents that look like the following example have been created:

    ```JSON
    {
        "id": "6820cccc-81dd-4ac8-8cd7-56cbc006b494",
        "deviceid": "Device 74",
        "temperature": 28.95798144347872,
        "time": 636697755141504100,
        "_rid": "Ja1aAP1n64UGAAAAAAAAAA==",
        "_self": "dbs/Ja1aAA==/colls/Ja1aAP1n64U=/docs/Ja1aAP1n64UGAAAAAAAAAA==/",
        "_etag": "0100b85f-0000-0000-0000-5b71b59c0000",
        "_attachments": "attachments/",
        "_ts": 1534178716
    }
    ```

#### Task 3: Create an Azure Databricks Spark Notebook

1. In the Azure portal, click **+ Create a resource**, in the search box, type **Azure Databricks**, and then press Enter.
2. On the **Everything** blade, click **Azure Databricks**, and then click **Create**.
3. On the **Azure Databricks Service** blade, in the **Workspace name** box, type **20777a-databricks-&lt;your name\>-&lt;the day\>**, for example, **20777a-databricks-john-31**.
4. Under **Resource group**, click **Create new**, and then type **DatabricksGroup**.
5. In the **Location** drop-down list, click **West US 2**.

    >Currently, not all regions support the range of VMs used by Azure Databricks to host Spark clusters. West US 2 does.

6. In the **Pricing Tier** drop-down list, click **Trial (Premium - 14-Days Free DBUs)**, and then click **Create**.
7. Wait while the service is deployed.
8. In the left panel, click **All resources**, and then click **20777a-databricks-&lt;your name\>-&lt;the day\>**.
9. On the **20777a-databricks-&lt;your name\>-&lt;the day\>** blade, click **Launch Workspace**.
10. On the **Azure Databricks** page, under **Common Tasks**, click **New Cluster**.
11. On the **New Cluster** page, in the **Cluster Name** box, type **&lt;your name\>-cluster**.
12. In the **Max Workers** box, type **4**.
13. Leave all other settings at their default values, and then click **Create Cluster**.
14. Wait while the cluster is created and started. Verify that its **State** is set to **Running** before continuing.
15. In the left toolbar, click **Azure Databricks**.
16. Under **Common Tasks**, click **Import Library**.
17. On the **New Library** page, in the **Source** drop-down list, click **Upload Java/Scala JAR**.
18. In the **Library Name** box, type **Cosmos DB Connector**.
19. Click the text **Drop library JARA here to upload**.
20. In the **Choose File to Upload** box, go to the **E:\Demofiles\Mod07\Demo04** folder, click **azure-cosmosdb-spark_2.3.0_2.11-1.2.0-uber.jar**, and then click **Open**.
21. When the JAR file has been uploaded, click **Create Library**.
22. On the **Cosms DB Connector** page, in the **&lt;your name\>-cluster** row, select the **Attach** check box, and wait until the status changes to **Attached**.
23. In the left toolbar, click **Azure Databricks**.
24. Under **Common Tasks**, click **New Notebook**.
25. In the **Create Notebook** dialog box, in the **Name** box, type **StreamTemperatures**.
26. In the **Language** drop-down list, click **Scala**.
27. In the **Cluster** drop-down list, ensure **&lt;your name\>-cluster** is selected, and then click **Create**.

#### Task 4: Stream Data from the Cosmos DB Change Feed using Spark

> The Scala code used in this task can be found in the file **E:\\Demofiles\\Mod07\\Demo04\\StreamTemperatures.txt**.

1. In the first cell of the notebook, enter the following code that specifies the modules that will be used by the notebook:

    ```Scala
    import org.apache.spark.SparkConf
    import org.apache.spark.SparkContext
    import com.microsoft.azure.cosmosdb.spark.config.Config
    import com.microsoft.azure.cosmosdb.spark.schema._
    import com.microsoft.azure.cosmosdb.spark.streaming._
    import org.apache.spark.sql.functions._
    import java.sql.Timestamp
    ```

2. In the toolbar at the top right-hand side of the cell, on the **Edit Menu** menu, click **Add Cell Below**.
3. Add the following code to the new cell. This code uses the specifies the configuration settings for connecting to the Cosmos DB database change feed for the Temperatues collection from Spark. Replace the **--URI--** with the **URI**, and replace **--KEY--** with the **PRIMARY KEY** of your **20777-sql-&lt;your name\>-&lt;the day\>** Cosmos DB account. The key parameters required for streaming from the change feed are:
    - **readchangefeed**. This should be set to **true** to stream data from the change feed.
    - **changefeedqueryname**. This is a text identifier that can be used for tracking streaming operations.
    - **changefeedstartfromthebeginning**. This setting indicates whether the change feed should be read from the first change recorded for the collection (**true**), or where previous reads have left off (**false**). To avoid repeatedly reading the same changes, you should set this parameter to **false**.
    - **changefeedcheckpointlocation**. This is a folder name on the Spark server where the connector can store checkpoint information about each read. This must be an absolute path name, but other than that you can specify almost any name; the folder will be created automatically when the notebook runs. Note that if you don't specify a value for this parameter, the change feed will not be read correctly.


    ```Scala
    val temperatureDatabaseConfig = Map(
        "endpoint" -> "--URI--",
        "masterkey" -> "--KEY--",
        "database" -> "DeviceData",
        "collection" -> "Temperatures",
        "readchangefeed" -> "true",
        "changefeedqueryname" -> "temperaturesquery",
        "changefeedstartfromthebeginning" -> "false",
        "changefeedcheckpointlocation" -> "/checkpointlocation"
    )
    ```

4. In the toolbar at the top right-hand side of the cell, on the **Edit Menu** menu, click **Add Cell Below**.
5. Add the following code to the new cell. This code creates a streaming dataframe that connects to the change feed of the Temperatures collection.

    ```Scala
    var temperatureStream = spark.readStream.format(classOf[CosmosDBSourceProvider].getName).options(temperatureDatabaseConfig).load()
    ```

6. In the toolbar at the top right-hand side of the cell, on the **Edit Menu** menu, click **Add Cell Below**.
7. Add the following code to the new cell. This code adds a column named **deviceNum** to the dataframe. The **deviceid** column contains a value of the form **device 99**. The **deviceNum** column contains only the numeric part of the device id (99). This column is used for displaying the data in order of device number later.

    ```Scala
    temperatureStream = temperatureStream.withColumn("deviceNum", $"deviceid".substr(8,2) + 0)
    ```

8. In the toolbar at the top right-hand side of the cell, on the **Edit Menu** menu, click **Add Cell Below**.
9. Add the following code to the new cell. This code creates a Spark UDF named **ticksToTimeStamp** that converts a datetime value specified in ticks into a Spark timestamp. A Spark UDF is similar to a Cosmos DB UDF - it can be applied to a column in a dataframe to convert the data in that column in every row in the dataframe.

    ```Scala
    val ticksToTimeStamp = udf[Timestamp, Long](ticks => {
            /* Convert ticks into milliseconds (1 tick = 100 nanoseconds) */
            val millisecondTicks = ticks / 10000;

            /* Adjust the time - ticks are measured from 1st January 0001, whereas Scala dates are measured from 1st January 1970
            So the value in tickDateTime is 1970 years fast! */
            val ticksBetweenYear1And1970 = 62135596800000L;
            val adjustedTicks = millisecondTicks - ticksBetweenYear1And1970;

            /* Check the result is not negative. 
            If it is, then the input parameter represents a date before January 1st 1970, which is not supported by this UDF */

            if (adjustedTicks < 0)
                throw new Error("Parameter must represent a date on or after 1st January 1970");
        
            /* Return a Date object using the number of milliseconds */
            new Timestamp(adjustedTicks);
        }
    )
    ```

10. In the toolbar at the top right-hand side of the cell, on the **Edit Menu** menu, click **Add Cell Below**.
11. Add the following code to the new cell. This specifies the transformations that will be applied to the data as it is streamed by Spark. In this example, that data is grouped by deviceid and deviceNum, and then further grouped into *windows* that contain that last 15 seconds worth of data. The maximum, minimum, and count of the number of values for each device in the window are calculated.
    ```Scala
    val streamingDF = temperatureStream.groupBy($"deviceid", $"deviceNum", window(ticksToTimeStamp($"time"), "15 seconds")).agg(max($"temperature").as("highest"), min($"temperature").as("lowest"), count($"deviceid").as("number of readings"))
    ```

12. In the toolbar at the top right-hand side of the cell, on the **Edit Menu** menu, click **Add Cell Below**.
13. Add the following code to the new cell. This is the code that actually starts performing the streaming operations. As the data for a window is read and processed, it is stored in an in-memory table named **stats**. Note that you should only use in-memory tables for operations that generate relatively small ammount of data. Additionally, an in-memory table is transient; when you stop the Spark job, it will be lost. If you need to persist the results of the streaming operations, you can specify a more permanent sink.

    ```Scala
    val query = streamingDF
        .writeStream
        .format("memory")        // memory = store data in-memory in a table (only do this if the results of the streaming aggregation are low-volume)
        .queryName("stats")      // stats = name of the in-memory table
        .outputMode("complete")  // complete = every count, max, and min should be in the table
        .start()
    ```

14. In the toolbar at the top right-hand side of the cell, on the **Edit Menu** menu, click **Add Cell Below**.
15. Add the following code to the new cell. This is an SQL statement (as indicated by the **%sql** *magic* prefix). This statement retrieves the data for the most recent time window from the **stats** in-memory table and sorts it by the **deviceNum** column.

    ```Scala
    %sql 
    select *, date_format(window.end, "HH:mm:ss") from stats where date_format(window.end, "HH:mm:ss") = (select max(date_format(window.end, "HH:mm:ss")) from stats) order by deviceNum
    ```

16. In the toolbar at the top of the notebook, click **Run All**. Wait while the steps in the notebook complete. Note that the final step will not display any data initially, because there is no new data in the change feed for the **Temperatures** collection.
17. Leave the notebook running.
18. In Visual Studio 2017, press F5 to run the **CosmosDBDeviceDataCapture** again, but this time allow it to remain running.
19. In the notebook, in the toolbar at the top right-hand side of the final cell, on the **Run Cell** menu, click **Run Cell**. This time you should see some data.
20. At the bottom of the cell, click the graph drop-down list icon, and then click **Bar**.
21. Click **Plot Options**.
22. In the **Customize Plot** dialog box, in the **Keys** box, delete both entries.
23. In the **All fields** box, drag **deviceid** into the **Keys** box.
24. In the **All fields** box, drag **highest** and **lowest** into the **Values** box.
25. Leave **number of readings** in the **Values** box
26. In the **Aggregation** drop-down list, click **SUM**, and then click **Apply**.
27. Resize the graph to make it easier to view.
28. In the toolbar at the top right-hand side of the final cell, on the **Run Cell** menu, click **Run Cell**. You should see the graph change to reflect the data for the latest 15 second window.
29. Wait 15 seconds, and then on the **Run Cell** menu, click **Run Cell**. The graph should be updated.
30. In the penultimate cell (the cell above the code that generates the graph), click **Cancel** to stop the Spark streaming job.
31. In the **Are you sure you want to cancel this query** dialog box, click **Yes** to confirm that you want to cancel the query.
32. In Visual Studio 2017, press Enter to stop the **CosmosDBDeviceDataCapture** application.

### Demo 3: Combining Cosmos DB with Azure Stream Analytics

**Scenario:** This demonstration extends the previous demo by showing how to capture data streamed into Azure Streaming Analytics and store it in a Cosmos DB database. The incoming data comprises temperature readings reported by a series of devices. Each device sends data to an Azure IoT Hub. Azure Stream Analytics takes this data and creates documents that it appends it to the Temperature collection in the DeviceData Cosmos DB database.

#### Task 1: Create an Azure IoT Hub and a Stream Analytics Job

1. In Internet Explorer, in the Azure portal, in the left panel, click **+ Create a resource**.
2. On the **New** blade, in the **Search the Marketplace** box, type **IoT**, and then press Enter.
3. On the **Everything** blade, click **IoT Hub**, and then click **Create**.
4. On the **IoT hub** blade, on the **Basics** tab, in the **Resource Group** drop-down list, click **20777Mod07**.
5. In the **Region** drop-down list, click the region closest to your current location.
6. In the **IoT Hub Name** box, type **20777a-iothub-&lt;your name\>-&lt;the day\>**, for example, **20777a-iothub-john-31**.
7. On the **Size and Scale** tab, in the **Pricing and scale tier** drop-down list, click **B1: Basic tier**.
8. Leave the remaining settings at their default values.
9. On the **Review + create** tab, click **Create**.
10. Wait while the IoT Hub is created and deployed.
11. In the left panel, click **All resources**, and then click **20777a-iothub-&lt;your name\>-&lt;the day\>**.
12. On the **20777a-iothub-&lt;your name\>-&lt;the day\>** blade, under **Settings**, click **Shared access policies**, and then click **device**.
13. Make a note of the **Connection string-primary key** value.
14. Close the **device** blade.
15. On the **20777a-iothub-&lt;your name\>-&lt;the day\>** blade, under **Explorers**, click **IoT devices**.
16. On the **IoT devices** blade, click **+ Add**.
17. On the **Add Device** blade, in the **Device ID** box, type **Thermometer**.
18. Leave the other settings as their default values, and then click **Save**.
19. In the left panel, click **+ Create a resource**.
20. On the **New** blade, in the **Search the Marketplace** box, type **Stream**, and then press Enter.
21. On the **Everything** blade, click **Stream Analytics job**, and then click **Create**.
22. On the **New Stream Analytics job** blade, in the **Job name** box, type **20777a-job-&lt;your name\>-&lt;the day\>**, for example, **20777a-job-john-31**.
23. Under **Resource group**, click **Use existing**, and then in the drop-down list, click **20777Mod07**.
24. In the **Location** drop-down list, click the location closest to your current location.
25. In the **Streaming units** box, type **6**, and then click **Create**.
26. Wait while the stream analytics job is created.

#### Task 2: Connect the IoT Hub, Stream Analytics Job, and Cosmos DB Database

1. In the left panel, click **All resources**, click **20777a-sql-&lt;your name\>-&lt;the day\>**, and then click **Data Explorer**.
2. On the **Data Explorer** blade, expand **DeviceData**, right-click **Temperatures**, and then click **Delete Collection**.
3. On the **Delete Collection** blade, in the **Confirm by typing the collection id** box, type **Temperatures**, and then click **OK**.
4. In the **SQL API** pane, right-click **DeviceData**, and then click **New Collection**.
5. On the **Add Collection** blade, in the **Collection Id** box, type **Temperatures**.
6. Under **Storage Capacity**, click **Unlimited**.
7. In the **Partition key** box, type **/deviceid**, and then click **OK**.
8. On the **20777a-sql-&lt;your name\>-&lt;the day\>** blade, under **Settings**, click **Keys**.
9. Make a note of the **PRIMARY KEY** value.
10. In the left panel, click **All resources**, and then click **20777a-job-&lt;your name\>-&lt;the day\>**.
11. On the **20777a-job-&lt;your name\>-&lt;the day\>** blade, under **Job topology**, click **Inputs**.
12. Click **+ Add stream input**, and then click **IoT Hub**.
13. On the **IoT Hub** blade, in the **Input alias** box, type **TemperatureDevicesHub**.
14. In the **Shared access policy name** drop-down list, click **service**, and then click **Save**.
15. On the **20777a-job-&lt;your name\>-&lt;the day\>** blade, under **Job topology**, click **Outputs**.
16. Click **+ Add**, and then click **Cosmos DB**.
17. On the **Cosmos DB** blade, in the **Output alias** box, type **TemperatureDatabase**.
18.  Click **Provide Cosmos DB settings manually**.
19.  In the **Account id** box, type **20777a-sql-&lt;your name\>-&lt;the day\>**.
20.  In the **Account key** box, enter the **PRIMARY KEY** for your Cosmos DB account as noted earlier.
21.  In the **Database** box, type **DeviceData**.
22.  In the **Collection name pattern** box, type **Temperatures**.
23.  In the **Document id** box, type **id**, and then click **Save**.
24. Wait while the connection to the output is verified and validated.
25. On the **20777a-job-&lt;your name\>-&lt;the day\>** blade, under **Job topology**, click **Query**.
26. Specify the following query, and then click **Save**.

    ```SQL
    SELECT
      id, deviceid, temperature, time
    INTO
      TemperatureDatabase
    FROM
      TemperatureDevicesHub
    ```

27. In the **Save** dialog box, click **Yes**.
28. On the **20777a-job-&lt;your name\>-&lt;the day\>** blade, click **Overview**, and then click **Start**.
29. On the **Start job** blade, click **Start**.
30. Wait for the stream analytics job to start before continuing.
  
#### Task 3: Stream Temperature Readings to Cosmos DB

1. In Visual Studio, on the **File** menu, point to **Open**, and then click **Project/Solution**.
2. In the **Open Project** dialog box, go to **E:\Demofiles\Mod07\Demo05\CosmosDBDeviceDataCapture**, and then double-click **CosmosDBDeviceDataCapture.sln**. This project is a variation on that used in the previous demonstration; it generates temperature readings, but rather than storing them directly in Cosmos DB it sends them as messages to an IoT hub.
3. In Solution Explorer, click **App.config**.
4. On the **App.config** tab, replace **\~CONNECTION STRING\~** with the **Connection string-primary key** value you noted earlier from your IoT hub.
5. In Solution Explorer, click **TemperatureDevice.cs**.
6. On the **TemperatureDevice.cs** tab, in the **TemperatureDevice** class, locate the **RecordTemperatures** method. In the previous version of the application, this class wrote the data to Cosmos DB. In this version, it uses the IoT hub client API to convert the data to a JSON string and then send it to the IoT hub, as follows:

    ``` CSharp
    // Create a temperature readings
    ThermometerReading reading = new ThermometerReading
    {
        ID = Guid.NewGuid().ToString(),
        DeviceID = this.deviceName,
        Temperature = rnd.NextDouble() * 100,
        Time = DateTime.UtcNow.Ticks
    };

    Trace.TraceInformation($"Recording: {reading.ToString()}");

    // Send the reading to the IoT hub
    var messageString = JsonConvert.SerializeObject(reading);
    var message = new Message(Encoding.ASCII.GetBytes(messageString));
    await this.client.SendEventAsync(message);
    ```

7. Press F5 to build and run the application.
8. In the Azure portal, click **All resources**, and then click **20777a-sql-&lt;your name\>-&lt;the day\>**.
9. On the **20777a-sql-&lt;your name\>-&lt;the day\>** blade, click **Data Explorer**.
10. On the **Data Explorer** blade, expand **DeviceData**, expand **Temperatures**, and then click **Documents**. Verify that the collection contains a set of documents that look similar to that shown below. Because you deleted and recreated the **Temperatures** collection, these documents must have been created by the Stream Analytics job:
   
    ```JSON
    {
      "id": "84b111f3-3281-4ebd-a758-75544f5813ba",
      "deviceid": "Device 48",
      "temperature": 82.02927060519777,
      "time": 636698565652062500,
      "_rid": "Ja1aAKIuTLoBAAAAAAAAAA==",
      "_self": "dbs/Ja1aAA==/colls/Ja1aAKIuTLo=/docs/Ja1aAKIuTLoBAAAAAAAAAA==/",
      "_etag": "\"0200b896-0000-0000-0000-5b72f2440000\"",
      "_attachments": "attachments/",
      "_ts": 1534259780
    }
    ```

11. In the Azure portal, click **All resources**, and then click **20777a-databricks-&lt;your name\>-&lt;the day\>**.
12. On the **20777a-databricks-&lt;your name\>-&lt;the day\>** blade, click **Launch Workspace**.
13. On the **Azure Databricks** page, under **Recents**, click **StreamTemperatures**. This is the Spark notebook that you created in the previous demonstration.
14. In the second cell of the notebook, change the **changefeedcheckpointlocation** value to **/checkpointlocation2**. This step as necessary as sometimes the checkpoint file from a previous run is left in place and can't be overwritten, resulting in errors when the notebook executes.
15. On the toolbar, on the **Clear** menu, click **Clear State & Results**.
16. In the **Are you sure you want to clear the notebook state and all results?** dialog box, click **Confirm**.
17. Click **Run All**, and wait for the notebook to run.
18. In the toolbar at the top right-hand side of the final cell, on the **Run Cell** menu, click **Run Cell**. You should see the graph showing the maximum and miniumum temperatures recorded by each device. Wait 15 seconds before continuing.
> **Note:** It might take 30 seconds or so for the data to appear the first time, while the initial data is propogated through the Stream Analytics job.    
19. In the toolbar at the top right-hand side of the final cell, click **Run Cell**. The graph should be updated.
20. Click **Stop Execution**, and then close the StreamTemperatures tab.
21. In the **Windows internet Explorer** dialog box, click **Leave this page**.


#### Task 4: Clean Up Resources

1. In Visual Studio 2017, press Enter to stop the CosmosDBDeviceDataCapture application, and the close Visual Studio 2017.
2. In the Azure portal, in the left panel, click **Resource groups**.
3. On the **Resource groups** blade, right-click **20777Mod07**, and then click **Delete resource group**.
4. On the **Are you sure you want to delete "20777Mod07"?**
 blade , in the **TYPE THE RESOURCE GROUP NAME** box, type **20777Mod07**, and then click **Delete**.
5. On the **Resource groups** blade, right-click **DatabricksGroup**, and then click **Delete resource group**.
6. On the **Are you sure you want to delete "20777Mod07"?**
 blade , in the **TYPE THE RESOURCE GROUP NAME** box, type **DatabricksGroup**, and then click **Delete**.
7. Close Internet Explorer.

---
©2018 Microsoft Corporation. All rights reserved.

The text in this document is available under the [Creative Commons Attribution 3.0 License](https://creativecommons.org/licenses/by/3.0/legalcode), additional terms may apply. All other content contained in this document (including, without limitation, trademarks, logos, images, etc.) are **not** included within the Creative Commons license grant. This document does not provide you with any legal rights to any intellectual property in any Microsoft product. You may copy and use this document for your internal, reference purposes.

This document is provided "as-is." Information and views expressed in this document, including URL and other Internet Web site references, may change without notice. You bear the risk of using it. Some examples are for illustration only and are fictitious. No real association is intended or inferred. Microsoft makes no warranties, express or implied, with respect to the information provided here.
