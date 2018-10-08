# Module 5: Designing and implementing a graph database

- [Module 5: Designing and implementing a graph database](#module-5-designing-and-implementing-a-graph-database)
    - [Lesson 1: Graph database models in Cosmos DB](#lesson-1-graph-database-models-in-cosmos-db)
        - [Demo 1: Creating a graph database](#demo-1-creating-a-graph-database)
            - [Preparation](#preparation)
            - [Task 1: Create an Azure Cosmos DB account](#task-1-create-an-azure-cosmos-db-account)
            - [Task 2: Create a graph database](#task-2-create-a-graph-database)
            - [Task 3: Create a graph](#task-3-create-a-graph)
        - [Demo 2: Import and query graph data](#demo-2-import-and-query-graph-data)
            - [Preparation](#preparation)
            - [Task 1: Copy the connection strings](#task-1-copy-the-connection-strings)
            - [Task 2: Configure the connection](#task-2-configure-the-connection)
            - [Task 3: Examine the code](#task-3-examine-the-code)
            - [Task 4: Query the imported graph](#task-4-query-the-imported-graph)
    - [Lesson 2: Designing graph database models for efficient operations](#lesson-2-designing-graph-database-models-for-efficient-operations)
        - [Demo 1: Designing efficient Gremlin queries](#demo-1-designing-efficient-gremlin-queries)
            - [Preparation](#preparation)
            - [Task 1: Show how to download the graph explorer from the Azure Portal](#task-1-show-how-to-download-the-graph-explorer-from-the-azure-portal)
            - [Task 2: Try to compile and run the getting started graph explorer](#task-2-try-to-compile-and-run-the-getting-started-graph-explorer)
            - [Task 3: Setup the Gremlin.NET version of the graph explorer](#task-3-setup-the-gremlinnet-version-of-the-graph-explorer)
            - [Task 4: Examine the loaded graph](#task-4-examine-the-loaded-graph)
            - [Task 5: Execute complex Gremlin queries](#task-5-execute-complex-gremlin-queries)
            - [Task 6: Demonstration clean up](#task-6-demonstration-clean-up)

## Lesson 1: Graph database models in Cosmos DB

### Demo 1: Creating a graph database

#### Preparation

Before starting this demo:

1. Ensure that the **MT17B-WS2016-NAT** and **20777A-LON-DEV** virtual machines are running, and then log on to **20777A-LON-DEV** as **Administrator** with the password **Pa55w.rd**.

2. On the toolbar, click **Internet Explorer**.

3. In Internet Explorer, go to **http://portal.azure.com**, and sign in using the Microsoft account that is associated with your Azure Learning Pass subscription.

#### Task 1: Create an Azure Cosmos DB account

1.  In the Azure portal, in the left panel, click **Azure Cosmos DB**, and then click **+ Add**.

2.  On the **Azure Cosmos DB** blade, under **Resource group**, click **Create new**, type **2077aMod05**, and then click **OK**.

3.  In the **Account name** box, type **20777a-mod5-graph-\<your name\>-\<the day\>**, for example, **20777a-mod5-graph-john-10**.

4.  In the **API** drop-down list, note the options available, and then click **Gremlin (graph)**.

5.  In the **Location** drop-down list, click the region closest to your current location, click **Review + create**, and then click **Create**.

6.  Wait for the **Azure Cosmos DB** to be created—this could take a few minutes.

7.  On the **Azure Cosmos DB** blade, click **Refresh**, and then click **20777a-mod5-graph-\<your name\>-\<the day\>**.

#### Task 2: Create a graph database

1.  On the **20777a-mod5-graph-\<your name\>-\<the day\> - Quick start** blade, click **Data Explorer**, and then click **New Database**.

2.  On the **New Database** blade, in the **Database id** box, type **GraphDB**.

3.  Select the **Provision throughput** check box, and note the throughput values go up to 1,000,000 RU/s.

4.  Next to **Provision throughput**, position the cursor on the information icon and discuss how the selected value would be distributed across all the graphs created in this database.

5.  Clear the **Provision throughput** check box, and the click **OK**.

#### Task 3: Create a graph

1.  In the **GREMLIN API** pane, right-click **GraphDB**, and then click **New Graph**.

2.  On the **Add Graph** blade, in the **Graph Id** box, type **Movies**, and then click **OK**.

### Demo 2: Import and query graph data

#### Preparation

You must have completed the previous demonstration in the lesson.

Before starting this demo:

1. In File Explorer, go to **E:\\Demofiles\\Mod05**, right-click **Setup.cmd**, and then click **Run as administrator**.

2. Wait until the script has completed.

#### Task 1: Copy the connection strings

1.  In Internet Explorer, on the **20777a-mod5-graph-\<your name\>-\<the day\>** blade, under **SETTINGS**, click **Keys**.

2.  Make a note of the **URI** and **PRIMARY KEY** values for use in the next steps.

#### Task 2: Configure the connection

1.  On the Start menu, click **Visual Studio 2017**.

2.  In Visual Studio 2017, on the **File** menu, point to **Open**, and then click **Project/Solution**.

3.  In the **Open Project** dialog box, go to **E:\\Demofiles\\Mod05\\GraphBulkExecutor**, click **GraphBulkExecutor.sln**, and then click **Open**.

4.  In Solution Explorer, double-click **App.config**.

5.  In App.config, in the **value** attribute of the **EndpointUrl** key, paste the **URI** value you noted earlier, replacing the text **enter URI here**.

6.  In the **value** attribute of the **AuthorizationKey** key, paste the **Primary Key** value you noted earlier, replacing the text **enter Primary Key here**.

#### Task 3: Examine the code

1.  In Solution Explorer, double-click **Program.cs**.

2.  In Program.cs, note the use of the **Microsoft.Azure.CosmosDB.BulkExecutor.Graph**.

3.  Walk through the **RunBulkImportAsync** method focusing on the **graphbulkExecutor.BulkImportAsync** method calls.

4.  Highlight the disabling of id generation, and the concurrency settings.

5.  In Solution Explorer, expand the **Data** folder, and look at the contents of the **csv** files.

6.  Press F5 to build and run the application.

7.  Once completed, press Enter to close the application.

8.  Close Visual Studio without saving any changes.

#### Task 4: Query the imported graph

1.  In Internet Explorer, on the **20777a-mod5-graph-\<your name\>-\<the day\>** blade, click **Data Explorer**.

2.  In the **GREMLIN API** pane, expand **GraphDB**, expand **MovieGraph**, and then click **Graph**.

3.  On the **Graph** tab, click **Execute Gremlin Query**.

4.  Note that the **g.V()** query returns all the vertices in the graph.

5.  In the **Results** column, click on a different result and show the links between the people and movies.

6.  Replace **g.V()** with **g.E()**, and then click **Execute Gremlin Query**.

7.  Note that a graph isn’t drawn for these results, however GraphSON containing all the edges in the graph is returned.

## Lesson 2: Designing graph database models for efficient operations

### Demo 1: Designing efficient Gremlin queries

#### Preparation

You must have completed the previous demonstration, this should have created a movie graph database.

#### Task 1: Show how to download the graph explorer from the Azure Portal

1.  In Internet Explorer, on the **20777a-mod5-graph-\<your name\>-\<the day\>** blade, click **Quick start**.

2.  On the **Quick start** blade, on the **Guided Gremlin Tour** tab, in the **Add a graph** section, click  
    **Create ‘SalesGraph’ collection**.

3.  In the **Download and run your GuidedGremlinTour app** section, click **Download**.

4.  In the download message box, click the **Save** drop-down arrow, and then click **Save as**.

5.  In the **Save As** dialog box, in the **Filename** box, type **E:\\Demofiles\\Mod05\\GraphExplorer.zip**, and then click **Save**.

6.  After the file has downloaded, click **Open**.

7.  In File Explorer, on the **Extract** menu, click **Extract all**.

8.  In the **Extract Compressed (Zipped) Folders** dialog box, in the **Files will be extracted to this folder** box, type **E:\\Demofiles\\Mod05\\GraphExplorer - Downloaded**, and then click **Extract**.

9.  In File Explorer, navigate to **E:\\Demofiles\\Mod05\\GraphExplorer - Downloaded\\quickstart-guidedgremlintour\\Web**, and then double-click **GraphExplorer.sln**.

10. In the **How do you want to open this file?** dialog box, click **OK**.

11. If the **Security Warning for GraphExplorer** dialog box appears, clear the **Ask me for every project in this solution** check box, and then click **OK**.

#### Task 2: Try to compile and run the getting started graph explorer

1.  Press F5 to build and run the application.

2.  Note that the build fails and there are numerous errors. This code is supposed to be just a starting point for development, and it requires some updates to get fully working.

3.  In the **Microsoft Visual Studio** dialog box, click **No**.

4.  In Solution Explorer, expand **GraphExplorer**, expand **Controllers**, expand **Api**, and then double-click **GremlinController.cs**.

5.  In GremlinController.cs, note that this class is using **Microsoft.Azure.Graphs**, and that current best practice is to use the open source Gremlin.NET libraries to query a Cosmos DB graph.

6.  On the **File** menu, point to **Open**, and then click **Project/Solution**.

7.  In the **Open Project** dialog box, go to **E:\\Demofiles\\Mod05\\GraphExplorer**, click **GraphExplorer.sln**, and then click **Open**.

8.  In Solution Explorer, expand **GraphExplorer**, expand **Controllers**, expand **Api**, and then double-click **GremlinController.cs**.

9.  Note that this version is using the **Gremlin.Net.Driver** library.

#### Task 3: Setup the Gremlin.NET version of the graph explorer

1.  In Solution Explorer, double-click **appsettings.json**.

2.  In appsettings.json, in the **endpoint** attribute, paste the **URI** value you noted earlier, replacing the text **enter URI here**.

3.  In the **authKey** attribute, paste the **Primary Key** value you noted earlier, replacing the text **enter Primary Key here**.

4.  In Internet Explorer, on the **20777a-mod5-graph-\<your name\>-\<the day\>** blade, click **Overview**.

5.  On the **Overview** blade, at the top right, copy the **Gremlin Endpoint**.

6.  In Visual Studio 2017, in the **gremlinURI** attribute, paste the **Gremlin Endpoint**, replacing the text **enter Gremlin Endpoint here**.

7.  In the **Gremlin Endpoint**, at the beginning of the string, delete the **https://**.

8.  In the **Gremlin Endpoint**, at the end of the string, delete the **:443/**.

9.  Press F5 to build and run the application.

10. In the **Microsoft Visual Studio** dialog box, click **Yes**.

11. In the **Security Warning** dialog box, click **Yes**.

12. Wait for the application to compile, and Internet Explorer to open.

#### Task 4: Examine the loaded graph

1.  In Internet Explorer, click **Execute**.

2.  This will take sometime, whilst the query is rendering, explain how the code is executing two queries against the graph. One to return all the vertices, and one to return all the edges. The application then builds the diagram from those two results. It is the building of the diagram (which is a node.js application) that is taking the time.

3.  Once the graph has been drawn, zoom in with the mouse wheel, and click on a **person**.

4.  On the right, the properties for this vertex are shown.

5.  In the **Set label property for Node Type** list, click **name**, and then click **Icon**.

6.  In the **Change icon** dialog box, click the person icon.

7.  On the graph, click a **movie**.

8.  In the **Set label property for Node Type** list, click **title**, and then click **Icon**.

9.  In the **Change icon** dialog box, click the movie icon.

10. Click the black box.

11. In the **Change color** dialog box, click a red color.

12. Zoom out to show the entire graph, then choose an area to zoom in.

13. Click an actors name, then follow the links to see the movies they acted in.

#### Task 5: Execute complex Gremlin queries

1.  In the text box, replace **g.V(); g.E()** with **g.V().hasLabel('person').as('director').out('Directed').as('movie').select('director').out('Acted in').where(eq('movie')).select('director','movie').by('name').by('title')**, and then click **Execute**.

2.  Note a graph hasn’t been drawn, the results are in GraphSON and are shown in a console dropdown:

    ```JSON  
    [
        {"director":"William Shatner","movie":"Star Trek V: The Final Frontier"},      
        {"director":"Mel Gibson","movie":"Braveheart"},      
        {"director":"Leonard Nimoy","movie":"Star Trek III: The Search For Spock"},      
        {"director":"Leonard Nimoy","movie":"Star Trek IV: The Voyage Home"}
    ]
    ```
3.  On the toolbar, click **Internet Explorer** that contains the Azure portal.

4.  On **20777a-mod5-graph-\<your name\>-\<the day\>** blade, click **Data Explorer**.

5.  On the **Data Explorer** blade, expand **GraphDB**, expand **MovieGraph**, and then click **Scale & Settings**.

6.  On the **Scale & Settings** tab, change the **Throughput (1,000 - 50,000 RU/s)** box to **3000**, and the click **Save**.

7.  Return to the Graph explorer window, and then click **Execute**.

8.  The same results should be returned.

9.  In the text box, replace the query with the following code, and then click **Execute**:

    ``` 
    g.V().hasLabel('movie').as('movie').in('Directed').as('director').select('movie').in('Acted in').where(eq('director')).select('director','movie').by('name').by('title')
    ```

10. Note that an error has occurred, scroll through the log message until you find the following string:

    ```JSON 
    {\"Errors\":[\"Request rate is large\"]}
    ```

11. On the toolbar, click **Internet Explorer** that contains the Azure portal.

12. On the **Scale & Settings** tab, change the **Throughput (1,000 - 50,000 RU/s)** box to **6000**, and then click **Save**.

13. In Visual Studio 2017, on the **Debug** menu, click **Continue**.

14. In the Graph explorer window, click **Execute**.

15. The query should return the same actor / directors. Ask your students why the query failed at the lower throughput rate.

16. Also make note that even though the results are the same they are in a different order.

> This is the example in the topic **Optimizing Gremlin queries**. The first query starts the traversal at the person nodes and follows the **OUT** edges. Cosmos DB stores the out edges and their vertices in the same partition so that they are optimized for queries. The second query starts at movies, all their edges are **IN** and therefore are not stored in the same partitions. This makes the second query more expensive in terms of processing.

17. Choose two actors in the graph, and write a query that will find how they are related. Ensure you choose two actors who are in the graph.

18. For example, to find the relationship between **Kevin** and **Christopher**, use the following code, and then click **Execute**:

    ``` 
    g.V().has('name','Kevin Bacon').repeat(bothE().bothV().simplePath()).until(has('name','Christopher Walken')).path().limit(1).unfold()
    ```

19. In the bottom right, click **Configure Graph**.

20. In the **Configure graph** dialog box, select the **Show Edge Labels** check box, and note how both actors and directors have been used in the graph traversal.

21. Close Visual Studio 2017.

#### Task 6: Demonstration clean up

1.  In the Azure portal, in the left pane, click **Resource groups**.

2.  On the **Resource groups** blade, right-click **20777aMod5**, and then click **Delete resource group**.

3.  On the **Are you sure you want to delete "20777aMod5"?** blade, in the **TYPE THE RESOURCE GROUP NAME** box, type **20777aMod5**, and then click **Delete**.

---
© 2018 Microsoft Corporation. All rights reserved.

The text in this document is available under the [Creative Commons Attribution 3.0 License](https://creativecommons.org/licenses/by/3.0/legalcode), additional terms may apply. All other content contained in this document (including, without limitation, trademarks, logos, images, etc.) are **not** included within the Creative Commons license grant. This document does not provide you with any legal rights to any intellectual property in any Microsoft product. You may copy and use this document for your internal, reference purposes.

This document is provided “as-is.” Information and views expressed in this document, including URL and other Internet Web site references, may change without notice. You bear the risk of using it. Some examples are for illustration only and are fictitious. No real association is intended or inferred. Microsoft makes no warranties, express or implied, with respect to the information provided here.
