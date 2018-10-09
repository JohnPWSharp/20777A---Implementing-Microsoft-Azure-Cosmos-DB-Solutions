# Module 6: Querying and Analyzing Big Data with Cosmos DB

- [Module 6: Querying and Analyzing Big Data with Cosmos DB](#module-6-querying-and-analyzing-big-data-with-cosmos-db)
    - [Lesson 1: Optimizing Cosmos DB queries with Azure Search](#lesson-1-optimizing-cosmos-db-queries-with-azure-search)
        - [Demo 1: Using Azure Search with a Cosmos DB SQL API database](#demo-1-using-azure-search-with-a-cosmos-db-sql-api-database)
            - [Preparation](#preparation)
            - [Task 1: Create a Cosmos DB account](#task-1-create-a-cosmos-db-account)
            - [Task 2: Create an Azure Search account](#task-2-create-an-azure-search-account)
            - [Task 3: Use Azure Search to index Cosmos DB](#task-3-use-azure-search-to-index-cosmos-db)
            - [Task 4: Run searches with Azure Search](#task-4-run-searches-with-azure-search)
    - [Lesson 2: Analyzing data in a Cosmos DB database using Apache Spark](#lesson-2-analyzing-data-in-a-cosmos-db-database-using-apache-spark)
        - [Demo 1: Performing interactive queries with Spark](#demo-1-performing-interactive-queries-with-spark)
            - [Preparation](#preparation)
            - [Task 1: Create an HDInsight cluster](#task-1-create-an-hdinsight-cluster)
            - [Task 2: Create a Cosmos DB collection](#task-2-create-a-cosmos-db-collection)
            - [Task 3: Connect to the Spark cluster head node](#task-3-connect-to-the-spark-cluster-head-node)
            - [Task 4: Connect spark-shell to Cosmos DB](#task-4-connect-spark-shell-to-cosmos-db)
            - [Task 5: Run interactive queries](#task-5-run-interactive-queries)
        - [Demo 2: Processing data in Cosmos DB with Spark](#demo-2-processing-data-in-cosmos-db-with-spark)
            - [Preparation](#preparation)
            - [Task 1: Create a Spark on HDInsight project](#task-1-create-a-spark-on-hdinsight-project)
            - [Task 2: Upload the connector JAR file to HDInsight](#task-2-upload-the-connector-jar-file-to-hdinsight)
            - [Task 3: Submit the application](#task-3-submit-the-application)
            - [Task 4: Review the outcome of the application](#task-4-review-the-outcome-of-the-application)
    - [Lesson 3: Visualizing data in a Cosmos DB database](#lesson-3-visualizing-data-in-a-cosmos-db-database)
        - [Demo 1: Visualizing data using PySpark](#demo-1-visualizing-data-using-pyspark)
            - [Preparation](#preparation)
            - [Task 1: Create a PySpark Jupyter Notebook](#task-1-create-a-pyspark-jupyter-notebook)
            - [Task 2: Write PySpark code that connects to Cosmos DB](#task-2-write-pyspark-code-that-connects-to-cosmos-db)
            - [Task 3: Run the notebook and analyze the data](#task-3-run-the-notebook-and-analyze-the-data)
        - [Demo 2: Visualizing data with Power BI, ODBC, and Spark](#demo-2-visualizing-data-with-power-bi-odbc-and-spark)
            - [Preparation](#preparation)
            - [Task 1: Configure a Cosmos DB ODBC DSN](#task-1-configure-a-cosmos-db-odbc-dsn)
            - [Task 2: Define formulas and measures](#task-2-define-formulas-and-measures)
            - [Task 3: Visualize delay data](#task-3-visualize-delay-data)
            - [Task 4: Configure Spark for DirectQuery to CosmosDB](#task-4-configure-spark-for-directquery-to-cosmosdb)
            - [Task 5: Connect PowerBI to HDInsight and Spark](#task-5-connect-powerbi-to-hdinsight-and-spark)
            - [Task 6: Show live data updates](#task-6-show-live-data-updates)
        - [Demo 3: Visualizing data using Azure Databricks](#demo-3-visualizing-data-using-azure-databricks)
            - [Preparation](#preparation)
            - [Task 1: Create an Azure Databricks service](#task-1-create-an-azure-databricks-service)
            - [Task 2: Create an Azure Databricks cluster](#task-2-create-an-azure-databricks-cluster)
            - [Task 3: Import the pydocumentdb library and create a PySpark notebook](#task-3-import-the-pydocumentdb-library-and-create-a-pyspark-notebook)
            - [Task 4: Retrieve data from Cosmos DB using PySpark](#task-4-retrieve-data-from-cosmos-db-using-pyspark)
            - [Task 5: Run the notebook and visualize the data](#task-5-run-the-notebook-and-visualize-the-data)
            - [Task 6: Demonstration clean-up](#task-6-demonstration-clean-up)

## Lesson 1: Optimizing Cosmos DB queries with Azure Search

### Demo 1: Using Azure Search with a Cosmos DB SQL API database

#### Preparation

Start the **20777A-LON-DEV** virtual machine.

This demonstration requires a Cosmos DB instance to be created in Azure; this might take several minutes. You might prefer to complete the first stage—Create a Cosmos DB Account—before the module begins.

#### Task 1: Create a Cosmos DB account
1.	Ensure that the **MT17B-WS2016-NAT** and **20777A-LON-DEV** virtual machines are running, and then log on to **20777A-LON-DEV** as **LON-DEV\Administrator** with the password **Pa55w.rd**.
2.	In File Explorer, go to **E:\Demofiles\Mod06**, right-click **Setup.cmd**, and then click **Run as administrator**.
3.	In File Explorer, go to **E:\Resources**, right-click **build_data_migration_tool.ps1**, and then click **Run with PowerShell**.
4.	Wait for the script to finish.
5.	In Internet Explorer, go to **http://portal.azure.com**, and sign in using the Microsoft account that is associated with your Azure Learning Pass subscription.
6.	In the Azure portal, in the left panel, click **Azure Cosmos DB**, and then click **+ Add**.
7.	On the **Azure Cosmos DB** blade, under **Resource Group** click **Create new**, type **20777Mod06**, and then click **OK**
8.  In the **Account name** box, type **20777a-sql-\<your name\>-\<the day\>**, for example, **20777a-sql-john-31**.
9.	In the **API** drop-down list, note the options available, and then click **SQL**.
10.	In the **Location** drop-down list, click the region closest to your current location, click **Review + create**, and then click **Create**.
11.	Wait for the Azure Cosmos DB to be created—this could take a few minutes.
12.	On the **Azure Cosmos DB** blade, click **Refresh**, and then click **20777a-sql-\<your name\>-\<the day\>**.
13.	On the **20777a-sql-\<your name\>-\<the day\>** blade, click **Data Explorer**, and then click **New Database**.
14.	On the **New Database** blade, in the **Database id** box, type **MovieLens**, and then click **OK**.
15.	In the **SQL API** pane, click **New Collection**.
16.	On the **Add Collection** blade, under **Database id**, click **Use existing**, and then in the drop-down list, click **MovieLens**.
17.	In the **Collection Id** box, type **moviedata**.
18.	In the **Throughput (400 - 10,000 RU/s)** box, type **1000**, and then click **OK**.
19.	On the **20777a-sql-\<your name\>-\<the day\>** blade, under **SETTINGS**, click **Keys**.
20.	Make a note of the **PRIMARY CONNECTION STRING** value.
21.	In File Explorer, go to **E:\Demofiles\Mod06\Demo01**, right-click **Demo01-setup.ps1**, and then click **Run with PowerShell**.
22.	Right-click anywhere inside the PowerShell window to paste your primary connection string, and then press Enter.
23.	Wait for the script to finish—it will take a couple of minutes.
24.	When the script is complete, press Enter to close the PowerShell window.
25.	In Internet Explorer, on the **20777a-sql-\<your name\>-\<the day\>** blade, click **Data Explorer**.
26.	In the **SQL API** pane, expand **moviedata**, and then click **Documents**.
27.	In the **Documents** pane, a list of documents will appear.

#### Task 2: Create an Azure Search account
1.	In the left panel, click **+ Create a resource**.
2.	On the **New** blade, in the **Search** box, type **azure search**, and then press Enter.
3.	On the **Everything** blade, click **Azure Search**, and then click **Create**.
4.	On the **New Search Service** blade, in the **URL** box, type **20777a-search-\<your name\>-\<the day\>**.
5.	Under **Resource** group, click **Use existing**, and then in the drop-down list, click **20777Mod06**.
6.	In the **Location** box, select the same location as you selected for your Cosmos DB account in the previous step, or the closest physical location if the location you selected in the last step is not available.
7.	Click **Pricing tier**.
8.	On the **Choose your pricing tier** blade, click **Free**, and then click **Select**.
9.	On the **New Search Service** blade, click **Create**.
10.	Wait for the search account to be created before proceeding.

#### Task 3: Use Azure Search to index Cosmos DB
1.	In the left panel, click **All resources**, and then click **20777a-search-\<your name\>-\<the day\>**.
2.	On the **20777a-search-\<your name\>-\<the day\>** blade, click **Import data**.
3.	On the **Import data** blade, click **Connect to your data**.
4.	On the **Data Source** blade, click **Cosmos DB**.
5.	On the **New data source** blade, in the **Name** box, type **movielens-cosmos**, and then click **Select an account**.
6.	On the **Cosmos DB accounts** blade, click **20777a-sql-\<your name\>-\<the day\>**.
7.	On the **New data source** blade, in the **Database** drop-down list, click **MovieLens**.
8.	In the **Collection** drop-down list, click **moviedata**, and then click **OK**.
9.	On the **Import data** blade, click **Customize target index**.
10.	On the **Index** blade, at the end of the **movieId** row, click the ellipsis (**…**), and then click **Delete**.
11.	At the end of the **rid** row, click the ellipsis (**…**), and then click **Delete**.
12.	On the **title** row, select the **RETRIEVABLE**, **FILTERABLE**, **SORTABLE**, and **SEARCHABLE** check boxes.
13.	On the **year** row, change the **TYPE** to **Edm.Int32**, and then select the **RETRIEVABLE**, **FILTERABLE**, and **SORTABLE** check boxes.
14.	On the **genres**, and **tags** rows, select the **RETRIEVABLE**, **FILTERABLE**, and **SEARCHABLE** check boxes.
15.	Select the **Analyzer** check box, observe that an analyzer is associated with string columns.
16.	On the **title** row, in the **ANALYZER** column, click **English - Microsoft**.
17.	Select the **Suggester** check box.
18.	In the **Suggester name** box, type **sg01**.
19.	In the **Search mode** drop-down list, click **analyzingInfixMatching**.
20.	On the **title**, and **genres** rows, select the **SUGGESTER: SG01** check box, and then click **OK**.
21.	On the **Create an Indexer** blade, in the **Name** box, type **indexer01**.
22.	Ensure the **Schedule** box is set to **Once**, and then click **OK**.
23.	On the **Import data** blade, click **OK**.
24.	After a few seconds, the import process will report that it is complete.


#### Task 4: Run searches with Azure Search
1.	On the **20777a-search-\<your name\>-\<the day\>** blade, click **Search explorer**.
2.	On the **Search explorer** blade, click **Search** to run a search without a filter. Observe that the first 50 results are returned, and that only the retrievable properties (**title**, **genres**, **tags**, **year**, and **id**) are returned in the results. An application could use the **id** to quickly fetch the document from the Cosmos DB database.
3.	In the **Query string** box, type **$top=3**, and then click **Search**. Observe that three results are returned.
4.	Click **Search**, observe that the same three results are returned.
5.	Edit the **Query string** box, so that it reads as follows, and then click **Search**:

    ```
    $top=3&search=horror
    ```

    Observe that three results are returned, ordered by **@search.score**.
6.	Click **Search**, and observe that the same three results are returned, in the same order.
7.	Edit the **Query string** box, so that it reads as follows, and then click **Search**:

    ```
    $top=3&$skip=3&search=horror
    ```
    Observe that three results are returned, ordered by **@search.score**; these are the next three results in the list.
8.	Edit the **Query string** box, so that it reads as follows, and then click **Search**:

    ```
    $top=3&search=title:horror
    ```
    Observe that three results are returned, where the title property contains **horror**.
9.	Edit the **Query string** box, so that it reads as follows, and then click **Search**:

    ```
    $top=5&queryType=full&search=title:greet~1
    ```
    Observe that five results are returned where the title contains a word with a fuzzy match to **greet**.
10.	Edit the **Query string** box, so that it reads as follows, and then click **Search**:

    ```
    $top=5&queryType=full&search=title:greet~1&highlight=title
    ```
    Observe that five results are returned with an additional **@search.highlights** property with an HTML snippet that highlights the matching result.
11.	Edit the **Query string** box, so that it reads as follows, and then click **Search**:

    ```
    queryType=full&search=green&$filter=year lt 1970
    ```
    Observe that three records are returned (even though there is no **top** option in the query) where the search matches and **year** is before **1970**.
12.	Edit the **Query string** box, so that it reads as follows, and then click **Search**:

    ```
    $top=10&queryType=full&search=red
    ```
    Observe that 10 records are returned.
13.	Edit the **Query string** box, so that it reads as follows, and then click **Search**:

    ```
    $top=10&queryType=full&search=red&$filter=tags/any()
    ```
    Observe that only three matching records with **tags** are returned.
14.	Edit the **Query string** box, so that it reads as follows, and then click **Search**:

    ```
    $top=10&queryType=full&search=blue&$filter=genres/any(t: t eq 'Action')
    ```
    Observe that eight matching records are returned that all have the **Action** genre.
15.	In the left panel, click **All resources**, click **20777a-sql-\<your name\>-\<the day\>**, and then click **Data Explorer**.
16.	In the **SQL API** pane, expand **moviedata**, click **Documents**, and then click **New Document**.
17.	On the **Documents** tab, edit the new document definition so that it reads as follows, and then click **Save**:

    ```JSON
    {
        "movieId":  999999,
        "title":  "Missing from my Index",
        "year":  "2018"
    }
    ```
18.	On the **All Resources** blade, click **20777a-search-\<your name\>-\<the day\>**, and then click **Search explorer**. 
19.	On the **Search explorer** blade, in the **Query string** box, type **"missing from my index"** (including quotes), and then click **Search**. Observe that no results are returned; the new value has not been added to the index.
20.	Close the **Search explorer** blade.
21.	On the **20777a-search-\<your name\>-\<the day\>** blade, under **Usage**, click **Indexers**.
22.	On the **Indexers** blade, click **indexer01**.
23.	On the **indexer01** blade, click **Run**.
24.	In the **Run indexer** dialog box, click **Yes**.
25.	The indexer will take about 10 seconds to update the index; the **indexer01** blade should automatically update to show the result of the indexer run—observe that in the **DOCS SUCCEEDED** column, a new row is added to the history, showing **1/1**.
26.	Close the **indexer01**, and **Indexers** blade.
27.	On the **20777a-search-\<your name\>-\<the day\>** blade, click **Search explorer**. 
28.	On the **Search explorer** blade, in the **Query string** box, type **"missing from my index"** (including quotes), and then click **Search**. Observe that the new result is now returned.
29.	In the left panel, click **All resources**, click **20777a-sql-\<your name\>-\<the day\>**, and then click **Data Explorer**.
30.	In the **SQL API** pane, right-click **moviedata**, and then click **Delete collection**.
31.	On the **Delete Collection** blade, in the **Confirm by typing the collection id** box, type **moviedata**, and then click **OK**.

## Lesson 2: Analyzing data in a Cosmos DB database using Apache Spark

### Demo 1: Performing interactive queries with Spark

#### Preparation

Complete the previous demonstration in this module. If you did not complete the previous demonstration, go back and carry out the first stage—**Create a Cosmos DB Account**—from the Using Azure Search with a Cosmos DB SQL API Database demonstration. 

This demonstration requires an **SSH client** such as **PuTTY** to be installed on **20777A-LON-DEV**. You’ll find instructions for installing PuTTY in the Prerequisites topic at the start of this module.

This demonstration requires an **HDInsight cluster** to be created in Azure; this step might take several minutes. You might prefer to complete the first stage—**Create an HDInsight Cluster**—before the module begins.

#### Task 1: Create an HDInsight cluster
1.	Ensure that the **MT17B-WS2016-NAT** and **20777A-LON-DEV** virtual machines are running, and then log on to **20777A-LON-DEV** as **LON-DEV\Administrator** with the password **Pa55w.rd**.
2.	In Internet Explorer, log in to the Azure portal at **https://portal.azure.com**. 
3.	In the Azure portal, in the left panel, click **+ Create a resource**.
4.	On the **New** blade, click **Analytics**, and then click **HDInsight**.
5.	On the **HDInsight** blade, click **Custom (size, settings, apps)**.
6.	On the **Basics** blade, type the following details, and then click **Cluster type**:
    - **Cluster name**: hdi-\<your name\>\<date\>
    - **Subscription**: \<your subscription\>
7.	On the **Cluster configuration** blade, enter the following details, and then click **Select**:
    - **Cluster type**: Spark
    - **Version**: Spark 2.3.0 (HDI 3.6)
8.	On the **Basics** blade, enter the following details, and then click **Next**:
    - **Cluster login username**: sparkadmin
    - **Cluster login password**: Pa55w.rdPa55w.rd
    - **Secure Shell (SSH) username**: sadmin
    - **Use same password as cluster login**: selected
    - **Resource group (use existing)**: 20777Mod06
    - **Location**: Select your region
9.	On the **Storage** blade, under **Select a Storage account**, click **Create new**.
10.	In the **Create a new Storage account** box, type **\<your name\>\<date\>sa**; point out that this name must be globally unique.
11.	In the **Default container** box, replace the suggested name with **hdi-\<your name\>\<date\>**.
12.	Leave all other settings at their defaults, and then click **Next**.
13.	On the **Applications (optional)** blade, click **Next**.
14.	On the **Cluster size** blade, in the **Number of Worker nodes** box, type **1**.
15.	Click **Worker node size**.
16.	On the **Choose your node size** blade, click **View all**, click **D12 V2 Optimized**, and then click **Select**.
17.	Click **Head node size**.
18.	On the **Choose your node size** blade, click **View all**, click **D3 V2 Optimized**, and then click **Select**.
19.	On the **Cluster size** blade, click **Next**.
20.	On the **Advanced settings** blade, click **Next**.
21.	On the **Cluster summary** blade, point out the estimated cost per hour of this cluster. 

    > Remember that, to avoid using up your Azure Pass allowance, it’s important that you remove clusters when you are not using them.

22.	On the **Cluster summary** blade, click **Create**.
23.	Wait for the cluster to be provisioned and the status to show as **Running**. This is likely to take at least 10 minutes.


#### Task 2: Create a Cosmos DB collection
1.	In the left panel, click **All resources**, click **20777a-sql-\<your name\>-\<the day\>**, and then click **Data Explorer**.
2.	In the **SQL API** pane, click **New Database**.
3.	On the **New Database** blade, in the **Database id** box, type **flightdelays**, and then click **OK**.
4.	In the **SQL API** pane, click **New Collection**.
5.	On the **Add Collection** blade, under **Database id**, click **Use existing**, and then in the drop-down list, click **flightdelays**.
6.	In the **Collection Id** box, type **delaydata**.
7.	In the **Throughput (400 - 10,000 RU/s)** box, type **5000**, and then click **OK**.
8.	In the **SQL API** pane, click **New Collection**.
9.	On the **Add Collection** blade, click **Use existing**, and then in the drop-down list, click **flightdelays**.
10.	In the **Collection Id** box, type **delaysbyselectedairline**.
11.	In the **Throughput (400 - 10,000 RU/s)** box, type **1000**, and then click **OK**.
12.	On the **20777a-sql-\<your name\>-\<the day\>** blade, under **SETTINGS**, click **Keys**.
13.	Make a note of the **PRIMARY CONNECTION STRING** value.
14.	In File Explorer, go to **E:\Demofiles\Mod06\Demo02**, right-click **Demo02-setup.ps1**, and then click **Run with PowerShell**.
15.	Right-click anywhere inside the PowerShell window to paste your primary connection string, and then press Enter.
16.	Wait for the script to finish—it will take a couple of minutes.
17.	Press Enter to close the PowerShell window.
18.	On the **20777a-sql-\<your name\>-\<the day\>** blade, click **Data Explorer**.
19.	In the **SQL API** pane, expand **delaydata**, and then click **Documents**.
20.	On the **Documents** tab, observe that documents will appear.

#### Task 3: Connect to the Spark cluster head node
1.	In the left panel, click **All resources**, and then click **hdi-\<your name\>\<date\>**.
2.	On the **hdi-\<your name\>\<date\>** blade, under **SETTINGS**, click **SSH + Cluster login**. 
3.	On the **SSH + Cluster login** blade, in the **Hostname** drop-down list, click the name of your cluster (it should be the only entry), and then click the **Click to copy** button.
4.	In File Explorer, go to the location where you saved **putty.exe** (C:\putty if you followed the instructions in the module prerequisites), and then double-click **putty.exe**.
5.	In the **Open File - Security Warning** dialog box, clear the **Always ask before opening this file** check box, and then click **Run**.
6.	In the **PuTTY Configuration** dialog box, right-click the **Host Name (or IP address)** box, and then click **Paste**.
7.	At the beginning of the host name, edit the pasted value to remove **ssh sadmin@**, and then click **Open**.
8.	In the **PuTTY Security Alert** dialog box, click **Yes**.
9.	In the **PuTTY** window, at the PuTTY prompt, type **sadmin**, and then press Enter.
10.	At the PuTTY prompt, type **Pa55w.rdPa55w.rd**, and then press Enter.
If login is successful, you will be presented with a prompt that starts **sadmin@hn0-**.


#### Task 4: Connect spark-shell to Cosmos DB
> **Note:** The spark-shell commands used in this step can be found in **E:\Demofiles\Mod06\Demo02\spark-shell_commands_01.txt**.

1.	At the PuTTY prompt, to start spark-shell, type the following, and then press Enter:

    ```
    spark-shell --master yarn --packages com.microsoft.azure:azure-cosmosdb-spark_2.3.0_2.11:1.2.0
    ```
    The application will take some time to load; when it’s ready, you will be presented with a scala> prompt.

2.	At the scala> prompt, type the following, and then press Enter:

    ```
    import com.microsoft.azure.cosmosdb.spark.schema._
    import com.microsoft.azure.cosmosdb.spark._
    import com.microsoft.azure.cosmosdb.spark.config._
    ```
3.	At the scala> prompt, type the following, and then press Enter. Replace **~URL~** with the **URL**, and replace **~Key~** with the **PRIMARY KEY** you noted earlier for the **20777a-sql-\<your name\>-\<the day\>** Cosmos DB instance.

    ```
    val flightDatabaseConfig = Config(Map(
        "Endpoint" -> "~URL~",
        "Masterkey" -> "~Key~", 
        "Database" -> "flightdelays",
        "Collection" -> "delaydata"
    ))
    ```
    
4.	At the scala> prompt, type the following, and then press Enter:

    ```
    val collection = spark.sqlContext.read.cosmosDB(flightDatabaseConfig)
    collection.createOrReplaceTempView("c")
    ```

#### Task 5: Run interactive queries

> **Note:** The spark-shell commands used in this step can be found in **E:\Demofiles\Mod06\Demo02\spark-shell_commands_02.txt**.
1.	At the scala> prompt, type the following, and then press Enter:

    ```
    val query = "SELECT c.Year, c.Month, c.DayOfMonth, c.Origin, c.Dest, c.UniqueCarrier, c.ArrDelay, c.DepDelay, c.CarrierDelay, c.WeatherDelay, c.NASDelay, c.SecurityDelay, c.LateAircraftDelay FROM c"
    val data = spark.sql(query)
    ```
2.	At the scala> prompt, type the following, and then press Enter to display a count of rows in the result set:

    ```    
    data.count()
    ```
3.	At the scala> prompt, type the following, and then press Enter to display the data from the result set:

    ```
    data.show()
    ```
4.	At the scala> prompt, type the following, and then press Enter to count the number of records for each airline:

    ```
    spark.sql("select UniqueCarrier, count(1) from c group by UniqueCarrier").show()
    ```

5.	At the scala> prompt, type the following, and then press Enter to show the average departure delay and arrival delay for each airline:

    ```
    spark.sql("select UniqueCarrier, avg(ArrDelay), avg(DepDelay), count(1) from c group by UniqueCarrier").show()
    ```
6.	At the scala> prompt, type the following, and then press Enter to show which destinations have the most cancellations, in descending order:

    ```
    spark.sql("select c.Dest, count(1) FROM c WHERE c.Cancelled = 1 group by c.Dest order by count(1) desc").show()
    ```
7.	At the scala> prompt, type the following, and then press Enter to show the total distance travelled by flights that were not cancelled:

    ```
    spark.sql("select sum(Distance) FROM c WHERE c.Cancelled = 1").show()
    ```
8.	At the scala> prompt, type **:quit**, press Enter, and then press CTRL+C.
9.	Leave your PuTTY session open for use in a later demonstration.

### Demo 2: Processing data in Cosmos DB with Spark

#### Preparation

Complete the previous demonstration in this module.

Downloading and installing the JDK and Eclipse might take some time. You might decide to complete the first step—**Prepare the Development Environment**—before starting the module.

1.	In Internet Explorer, go to **http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html**.
2.	If the **Your Choices Regarding Cookies on this Site** dialog box appears, click **SUBMIT PREFERENCES**.
3.	If the **Preferences Submitted** dialog box appears, click **CLOSE**.
4.	In the **Java SE Development Kit 8u181** section, click **Accept License Agreement**, and then click **jdk-8u181-windows-x64.exe**.
5.	In the message box, click **Run**.
6.	In the **Java SE Development Kit 8 Update 181 (64-bit) - Setup** dialog box, on the **Welcome to the Installation Wizard for Java SE Development Kit 8 Update 181** page, click **Next**.
7.	On the **Select optional features to install from the list below** page, click **Next**.
8.	If the **Change in License Terms** dialog box appears, click **OK**.
9.	On the **Destination Folder** page, click **Next**.
10.	Wait for installation to complete, and then click **Close**.
11.	In Internet Explorer, go to **http://www.eclipse.org/downloads/packages/eclipse-ide-java-developers/oxygen2**.
12.	Under **Download Links**, click **Windows 64-bit**, and then click **Download**.
13.	In the **Internet Explorer** dialog box, click **Save as**.
14.	In the **Save As** dialog box, in the **File Name** box, type **E:\Demofiles\Mod06\eclipse.zip**, and then click **Save**.
15.	Wait for the download to complete, and then click **Open folder**.
16.	In File Explorer, right-click **eclipse.zip**, and then click **Extract All**.
17.	In the **Extract Compressed (Zipped) Folders** dialog box, click **Extract**.
18.	Wait for extraction to complete.
19.	In File Explorer, go to **E:\Demofiles\Mod06\eclipse\eclipse**, and then double-click **eclipse.exe**.
20.	In the **Open File - Security Warning** dialog, clear the **Always ask before opening this file** check box, and then click **Run**.
21.	In the **Eclipse Launcher** dialog box, in the **Workspace** box, type **E:\Demofiles\Mod06\eclipse\workspace**, select the **Use this as the default and do not ask again** check box, and then click **Launch**.
22.	Wait for Eclipse to start.
23.	In Eclipse, on the **Help** menu, click **Install New Software**. 
24.	In the **Available Software** dialog box, in the **Work with** box, type **http://dl.microsoft.com/eclipse/**, and then click **Add**.
25.	In the **Add Repository** dialog box, in the **Name** box, type **azure-toolkit**, and then click **OK**.
26.	Wait for the repository to be added.
27.	In the **Available Software** dialog box, select the **Azure Toolkit for Java** check box.
28.	Clear the **Contact all update sites during install to find required software** check box, and then click **Next**.
29.	On the **Install Remediation Page** page, click **Next**.
30.	On the **Install Details** page, click **Next**.
31.	On the **Review Licenses** page, click **I accept the terms of the license agreements**, and then click **Finish**. The progress of the installation is shown in the bottom-right of the Eclipse window. 
32.	In the **Software Updates** dialog box, click **Restart Now**.
33.	Wait for Eclipse to restart.
34.	In the **Azure HDInsight for Eclipse** dialog box, click **OK**.
35.	In the **Install missing plugin** dialog box, click **OK**.
36.	In the **Eclipse Marketplace** dialog box, click **Confirm**.
37.	On the **Review Licenses** page, click **I accept the terms of the license agreements**, and then click **Finish**. The progress of the installation is shown in the bottom-right of the Eclipse window.
38.	In the **Software Updates** dialog box click **Restart Now**.
39.	Wait for Eclipse to restart.


#### Task 1: Create a Spark on HDInsight project
1. In the Eclipse workspace on the **File** menu, point to **New**, and then click **Project**.
2. In the **New Project** wizard, on the **Select a wizard** page, expand **HDInsight Project**, click **Spark on HDInsight (Scala)**, and then click **Next**.
3. In the **New HDInsight Scala Project** dialog box, type the following details, and then click **Next**:
    - **Project name**: spark-sample
    - **Use default** location: clear
    - **Location**: E:\Demofiles\Mod06\Demo03\spark-sample
    - **Use Maven to configure Spark SDK**: Spark 2.3.0 (Scala 2.11.8)

4. On the **HDInsight Spark Project Library Settings** page, click **Finish**. Wait for the project to be created.
   
5. In Eclipse, close the **Welcome** tab to see Package Explorer.

6. On the **Package Explorer** tab, right-click **spark-sample**, point to **Build Path**, and then click **Configure Build Path**.

7. In the **Properties for spark-sample** dialog box, on the **Libraries** tab, click **Add External JARs**.

8. In the **JAR Selection** dialog box, in the **File name** box, type **E:\\Demofiles\\Mod06\\Demo03\\azure-cosmosdb-spark\_2.3.0\_2.11-1.2.0-uber.jar**, and then click **Open**.

9. In the **Properties for spark-sample** dialog box, click **Apply and Close**.

10. In Package Explorer, expand **spark-sample**, expand **src**, right-click **main**, point to **New**, and then click **Other**.

11. In the **New** wizard, on the **Select a wizard** page, expand **Scala Wizards**, click **Scala Object**, and then click **Next**.

12. On the **Create New File** page, in the **Name** box, type **Options**, and then click **Finish**.

13. On the **File** menu, click **Open File**.

14. In the **Open File** dialog box, in the **File name** box, type **E:\\Demofiles\\Mod06\\Demo03\\Options.txt**, and then click **Open**.

15. On the **Edit** menu, click **Select All**.

16. On the **Edit** menu, click **Copy**.

17. Click the **Options.scala** tab.

18. On the **Edit** menu, click **Select All**.

19. On the **Edit** menu, click **Paste**.

20. Edit the contents of the **Options.scala** file to replace **~URI~** with the **URI**, and replace **~KEY~** with the **PRIMARY KEY** values that you noted earlier for the  **20777a-sql-\<your name\>-\<the day\>** Cosmos DB instance
    
21. In Package Explorer, right-click **main**, point to **New**, and then click **Other**.

22. In the **New** wizard, on the **Select a wizard** page, expand **Scala Wizards**, click **Scala Object**, and then click **Next**.

23. On the **Create New File** page, in the **Name** box, type **Analyzer**, and then click **Finish**.

24. On the **File** menu, click **Open File**.

25. In the **Open File** dialog box, in the **File name** box, type **E:\\Demofiles\\Mod06\\Demo03\\Analyzer.txt**, and then click **Open**.

26. On the **Edit** menu, click **Select All**.

27. On the **Edit** menu, click **Copy**.

28. Click the **Analyzer.scala** tab.

29. On the **Edit** menu, click **Select All**.

30. On the **Edit** menu, click **Paste**.

31. On the **File** menu, click **Save All**.

32. On the **Project** menu, click **Clean**.

33. In the **Clean** dialog box, click **Clean**.

#### Task 2: Upload the connector JAR file to HDInsight

1.  In Internet Explorer, click **All resources**, and then click **hdi-\<your name\>\<date\>**.

2.  On the **hdi-\<your name\>\<date\>** blade, under **PROPERTIES**, click **Storage accounts**.

3.  On the **Storage accounts** blade, click **\<your name\>\<date\>sa**.

4.  On the **\<your name\>\<date\>sa** blade, under **BLOB SERVICE**, click **Blobs**, and then click **hdi-\<your name\>\<date\>**.

5.  On the **hdi-\<your name\>\<date\>** blade, click the **example** folder, click the **jars** folder, and then click **Upload**.

6.  In the **Upload blob** dialog box, click the folder icon.

7.  In the **Choose File to Upload** dialog box, in the **File name** box, type **E:\\Demofiles\\Mod06\\Demo03\\azure-cosmosdb-spark\_2.3.0\_2.11-1.2.0-uber.jar**, and then click **Open**.

8.  In the **Upload blob** dialog box, click **Upload**.

9.  Wait for the upload to finish.

#### Task 3: Submit the application

1.  In Eclipse, on the **Tools** menu, point to **Azure**, and then click **Sign In**.

2.  In the **Azure Sign In** dialog box, click **Sign in**.

3.  In the **Azure Login Dialog** dialog box, on the **Sign in** page, type your Azure account email address, and then click **Next**.

4.  On the **Enter password** page, type your Azure password, and then click **Sign in**.

5.  In the **Your Subscriptions** dialog box, select your Azure Pass subscription, and then click **Select**.

6.  In Package Explorer, right-click **spark-sample**, and then click **Submit Spark Application to HDInsight**.

7.  In the **Spark Submission** dialog box, type the following values, and then click **Submit**:

    - **Cluster Name**: hdi-\<your name\>\<date\> (Spark: 2.3)

    - **Referenced Jars**: wasb:///example/jars/azure-cosmosdb-spark\_2.3.0\_2.11-1.2.0-uber.jar

8.  In the **Spark Submission** pane, you can see the progress of the Spark submission. Wait for the job to complete.

9.  When the job completes, the icons at the left-hand side of the **Spark Submission** pane will turn grey.

#### Task 4: Review the outcome of the application

1.  In Internet Explorer, click **All resources**, click **20777a-sql-\<your name\>-\<the day\>**, and then click **Data Explorer**.

2.  On the **Data Explorer** blade, expand **delaysbyselectedairline**, and then click **Documents**.

3.  On the **Documents** tab, click a few of the documents; observe that the **UniqueCarrier** is **AA**.

4.  In the **SQL API** pane, click **New SQL Query**.

5.  On the **Query 1** tab, edit the query so that it reads as follows, and then click **Execute Query**:

    ```SQL
    SELECT DISTINCT c.UniqueCarrier FROM c
    ```
    Observe that only **AA** is returned.

6.  Right-click **delaysbyselectedairline**, and then click **Delete Collection**.

7.  In the **Delete Collection** pane, in the **Confirm by typing the collection id** box, type **delaysbyselectedairline**, and then click **OK**.

8.  In the left panel, click **All resources**, and click **hdi-\<your name\>\<date\>**.

9.  On the **hdi-\<your name\>\<date\>** blade, in the **Cluster dashboards** section, click **Cluster dashboards**.

10. On the **Cluster Dashboards** blade, click **Spark History Server**.

11. In the **Windows Security** dialog box, in the **User Name** box, type **sparkadmin**, and then in the **Password** box, type **Pa55w.rdPa55w.rd**, and then click **OK**.

12. On the **History Server** page, in the **App ID** list, click the first item—the item name will start **application**.

13. On the **Jobs** tab, you can see the individual jobs that Spark ran.

14. On the **Stages** tab, you can see the job stages.

15. On the **Environment** tab, you can see the details of the environment configuration.

16. Close the Spark history page when you have finished.

17. Close Eclipse, but leave Internet Explorer open for the next demonstration.

## Lesson 3: Visualizing data in a Cosmos DB database

### Demo 1: Visualizing data using PySpark

#### Preparation 

Complete the previous demonstration in this module.

#### Task 1: Create a PySpark Jupyter Notebook

1.  In Internet Explorer, in the Azure portal, click **All resources**, and then click **hdi-\<your name\>\<date\>**.

2.  On the **hdi-\<your name\>\<date\>** blade, under **Quick links**, click **Cluster dashboard**.

3.  On the **Cluster dashboards** blade, click **Jupyter Notebook**.

4.  In the **Windows Security** dialog box, in the **User Name** box, type **sparkadmin**, and then in the **Password** box, type **Pa55w.rdPa55w.rd**, and then click **OK**.

5.  In Internet Explorer, on the **Jupyter** page, click **PySpark**.

6.  In the **New** drop-down list, click **PySpark3**.

7.  Click the **PySpark/** tab, and then click the **Untitled** tab.

8.  In the page heading, click the text **Untitled**.

9.  In the **Rename Notebook** dialog box, in the text box, type **Flight Delays Analysis**, and then click **OK**.

#### Task 2: Write PySpark code that connects to Cosmos DB

> **Note:** The PySpark used in this step can be found in **E:\\Demofiles\\Mod06\\Demo04\\pyspark.txt**.

1.  In the first cell of the notebook, add the following code that configures the Cosmos DB Spark connector:

    ```
    %%configure
    { "name":"Spark-to-Cosmos_DB_Connector", 
    "conf": {
        "spark.jars.packages": "com.microsoft.azure:azure-cosmosdb-spark_2.3.0_2.11:1.2.0",
        "spark.jars.excludes": "org.scala-lang:scala-reflect"
        }
    }
    ```
    > **Note:** Do not insert a comment or a blank line above the **%%configure** magic.

2.  On the **Insert** menu, click **Insert Cell Below**.

3.  In the new cell, add the following code that configures the Cosmos DB Spark connector. Replace **--URI--** with the **URI**, and replace **--KEY--** with the **PRIMARY KEY** values that you noted earlier for the **20777a-sql-\<your name\>-\<the day\>** Cosmos DB account:

    ```
    host = '--URL--'
    primaryKey = '--KEY--'
    ```
4.  On the **Insert** menu, click **Insert Cell Below**.

5.  In the new cell, add the following code that specifies the database and collection to use in the Cosmos DB account:

    ```JSON
    dbConfig = {
        "Endpoint" : host,
        "Masterkey" : primaryKey,
        "Database" : "flightdelays",
        "Collection" : "delaydata"
    }
    ```

6.  On the **Insert** menu, click **Insert Cell Below**.

7.  In the new cell, add the following code that creates a temporary view that Spark uses to query the data:

    ```JAVA
    data = spark.read.format("com.microsoft.azure.cosmosdb.spark").options(**dbConfig).load()
    data.createOrReplaceTempView("flights")
    ```
8.  On the **Insert** menu, click **Insert Cell Below**.

9.  In the new cell, add the following code that runs a SQL query against the temporary table to find all documents for a specified airline:

    ```SQL
    %%sql
    SELECT * FROM flights WHERE UniqueCarrier= 'AA'
    ```
#### Task 3:  Run the notebook and analyze the data

1.  On the **Cell** menu, click **Run All**. Wait while each cell is executed in turn.

2.  Examine the output generated by the **%%sql magic**; it should show a tabular view of the delay data.

3.  In the toolbar above the data table, click **Scatter**.

4.  In the **X axis** drop-down list, click **Distance**.

5.  In the **Y axis** drop-down list, click **ArrDelay**.

6.  In the **Func** drop-down list, click **Avg**.

7.  The scatter chart shows how the average arrival delay varies with the flight distance.

8.  In the **X axis** drop-down list, click **Dest**.

9.  In the toolbar above the data table, click **Bar**.

10. The bar chart depicts the average arrival delay for each destination airport for this airline.

11. In the **Func** drop-down list, click **Count**.

12. In the toolbar above the data table, click **Pie**.

13. The pie chart illustrates how many flights for the airline were subject to arrival delays at each airport, as a percentage of the flights made. If you position the cursor over a segment of the pie chart, you see how many flights each segment represents.

14. In the cell that runs the **%%sql magic**, change the code to find the delays for a different airline:

    ```SQL
    %%sql
    SELECT * FROM flights WHERE UniqueCarrier= 'UA'
    ```
15. On the **Cell** menu, click **Run Cells**. This action causes only the code in the current cell to run.

16. In the toolbar above the data table, click **Scatter**.

17. In the **X axis** drop-down list, click **Origin**.

18. In the **Y axis** drop-down list, click **DepDelay**.

19. In the **Func** drop-down list, click **Count**.

20. The graph that is generated shows the number of flights for the airline that were subject to departure delays at each airport.

21. Close the **Flight Delays Analysis** tab.

### Demo 2: Visualizing data with Power BI, ODBC, and Spark

#### Preparation 

Complete the previous demonstration in this module.

#### Task 1: Configure a Cosmos DB ODBC DSN

1.  In Internet Explorer, go to **https://aka.ms/documentdb-odbc-64x64**.

2.  In the message box, click **Run**.

3.  In the **Microsoft Azure DocumentDB ODBC Driver (x64) - InstallShield Wizard** dialog box, click **Next**.

4.  On the **License Agreement** page, click **I accept the terms in the license agreement**, and then click **Next**.

5.  On the **Customer Information** page, click **Next**.

6.  On the **Ready to Install the Program** page, click **Install**.

7.  On the **InstallShield Wizard Completed** page, click **Finish**.

8.  On the Start menu, type **odbc**, and then click **ODBC Data Sources (64-bit)**.

9.  In the **ODBC Data Source Administrator (64-bit)** dialog box, on the **System DSN** tab, click **Add**.

10. In the **Create New Data Source** dialog box, click **Microsoft DocumentDB ODBC Driver**, and then click **Finish**.

11. In the **DocumentDB ODBC Driver DSN Setup** dialog, type the following values, and then click **Schema Editor**:

    - **Data Source Name**: cosmos

    - **Host**: The **URI** value that you noted earlier for the **20777a-sql-\<your name\>-\<the day\>** Cosmos DB account

    - **Access Key**: The **PRIMARY KEY** value that you noted earlier for the **20777a-sql-\<your name\>-\<the day\>** Cosmos DB account

12.  In the **Schema Editor** window, click **Create New**.
13.  In the **Generate Schema** dialog box, observe that the mapping method is **Collection**.

14. Under **Mapping Definition** click **Edit**.

15. In the **Mapping Definition** dialog box, observe that you can switch between collection mapping and table delimiters sampling, and then click **OK**.

16. In the **Generate Schema** dialog box, click **Sample**.

17. On the **Design View** tab, expand **flightdelays**, expand **delaydata**, and then click **delaydata**.

18. In the right-hand pane, observe that you can view and amend the data types for each property in the source documents.

19. On the **File** menu, click **Save**.

20. In the **Save Schema Map to** dialog box, in the **File name** box, type **E:\\Demofiles\\Mod06\\Demo05\\delaydata_schema**, and then click **Save**.

21. Close the **Schema Editor** window.

22. In the **DocumentDB ODBC Driver DSN Setup** dialog box, click **Advanced Options**.

23. In the **Advanced Options** dialog box, in the **Schema File** box, type **E:\\Demofiles\\Mod06\\Demo05\\delaydata_schema.json**, and then click **OK**.

24. In the **DocumentDB ODBC Driver DSN Setup** dialog box, click **OK**.

25. In the **ODBC Data Source Administrator (64-bit)** dialog box, click **OK**.

26. On the desktop, double-click **Power BI Desktop**.

27. In Power BI Desktop, in the **Welcome to Power BI Desktop** dialog box, either fill in your personal information then click **Done**, or, if you already have a Power BI account, click **Already have a Power BI account? Sign in**, and then sign in using your credentials.

28. In the **Power BI Desktop** window, click **Get data**.

29. In the **Get Data** dialog box, click **Other**, click **ODBC**, and then click **Connect**.

30. In the **From ODBC** dialog box, in the **Data source name (DSN)** drop-down list, click **cosmos**, and then click **OK**.

31. In the **ODBC driver** dialog box, on the **Default or custom** tab, click **Connect**.

32. In the **Navigator** dialog box, expand **flightdelays**, expand **delaydata**, and then click **delaydata**.

33. Select the **delaydata** check box, and then click **Load**.

34. Wait while the data is loaded from Cosmos DB; observe that all rows are retrieved, and the data is loaded into local memory. If there is no data loaded, click **Data** to view the Data pane.

#### Task 2: Define formulas and measures

1.  In Power BI, click **New column**.

2.  In the new column box, edit the value so that it reads the following, and then press Enter:

    ```Excel
    FlightDelay = if ([ArrDelay] <> "NA", [ArrDelay], "0") + if ([DepDelay] <> "NA", [DepDelay], "0")
    ```
3.  Click **New column**.

4.  In the new column box, edit the value so that it reads the following, and then press Enter:

    ```Excel
    TimeInAir = VALUE(if ([AirTime] <> "NA", [AirTime], "0") )
    ```
5.  Click **New measure**.

6.  In the new measure box, edit the value so that it reads the following, and then press Enter:

    ```Excel
    AvgDelay = AVERAGE(delaydata[FlightDelay])
    ```
7.  Click **New measure**.

8.  In the new measure box, edit the value so that it reads the following, and then press Enter:

    ```Excel
    DelayProportion = AVERAGE(delaydata[FlightDelay]) / AVERAGE(delaydata[TimeInAir])
    ```

#### Task 3: Visualize delay data

1.  In Power BI, in the **Navigation** pane, click **Report**, and then click **New Visual**.

2.  In the **VISUALIZATIONS** pane, click the vertical **Clustered Column Chart** icon (the fourth icon on the top row).

3.  In the **FIELDS** pane, click and drag **UniqueCarrier** to the **VISUALIZATIONS** pane, then drop **UniqueCarrier** on the **Axis** data fields list.

4.  In the **FIELDS** pane, select the **AvgDelay** check box—observe that **AvgDelay** appears on the **Value** data fields list in the **VISUALIZATIONS** pane.

5.  Resize the visual to make the graph easier to see.

6.  In Power BI, click **New Visual**.

7.  In the **VISUALIZATIONS** pane, click the vertical **Clustered Column Chart** icon (the fourth icon on the top row).

8.  In the **FIELDS** pane, click and drag **UniqueCarrier** to the **VISUALIZATIONS** pane, then drop **UniqueCarrier** on the **Axis** data fields list.

9.  In the **FIELDS** pane, select the **DelayProportion** check box—observe that **DelayProportion** appears on the **Value** data fields list in the **VISUALIZATIONS** pane.

10. Resize the visual to make the graph easier to see.

11. On the **File** menu, click **Save As**.

12. In the **Save As** dialog box, in the **File name** box, type **E:\\Demofiles\\Mod06\\Demo05\\FlightDelays.pbix**, and then click **Save**.

13. Close **Power BI desktop**.

#### Task 4: Configure Spark for DirectQuery to CosmosDB

1.  In the PuTTY window, type the following, and then press Enter:

    ```bash
    sudo hdfs dfs -get /example/jars/azure-cosmosdb-spark_2.3.0_2.11-1.2.0-uber.jar /usr/hdp/current/spark2-client/jars/
    ```
    > **Note:** If PuTTY is not running, follow the instructions in the *Connect to the Spark Cluster Head Node* section of Demo 1 in Lesson 2 of this module.

2.  At the PuTTY prompt, type the following, and then press Enter:

    ```bash
    hn=`hostname` && hn1="${hn/hn0-/hn1-}" && ssh $hn1
    ```
3.  At the PuTTY prompt, type **yes**, and then press Enter.

4.  At the PuTTY prompt, type **Pa55w.rdPa55w.rd**, and then press Enter.

5.  At the PuTTY prompt, type the following, and then press Enter:

    ```bash
    sudo hdfs dfs -get /example/jars/azure-cosmosdb-spark_2.3.0_2.11-1.2.0-uber.jar /usr/hdp/current/spark2-client/jars/ && exit
    ```
6.  At the PuTTY prompt, type the following, and then press Enter:

    ```bash 
    hn=`hostname` && wn0="${hn/hn0-/wn0-}" && ssh $wn0
    ```

7.  At the PuTTY prompt, type **yes**, and then press Enter.

8.  At the PuTTY prompt, type **Pa55w.rdPa55w.rd**, and then press Enter.

9.  At the PuTTY prompt, type the following, and then press Enter:

    ```bash
    sudo hdfs dfs -get /example/jars/azure-cosmosdb-spark_2.3.0_2.11-1.2.0-uber.jar /usr/hdp/current/spark2-client/jars/ && exit
    ```
10. In Internet Explorer, click **All resources**, and then click **hdi-\<your name\>\<date\>**.

11. On the **hdi-\<your name\>\<date\>** blade, click **Cluster dashboards**.

12. On the **Cluster dashboards** blade, click **Ambari home**.

13. If the **Windows Security** dialog box appears, in the **User name** box, type **sparkadmin**, and then in the **Password** box, type **Pa55w.rdPa55w.rd**, and then then click **OK**.

14. On the **Ambari** page, on the **Services** tab, click **Spark2**.

15. On the **Spark2** page, in the **Service Actions** drop-down list, click **Restart All**.

16. In the **Confirmation** dialog box, click **Confirm Restart All**.

17. Wait for the restart to complete, and then click **OK**.

18. In the PuTTY window, type the following, and then press Enter:

    ```bash
    spark-shell --master yarn
    ```
19. At the **scala\>** prompt, type the following, and then press Enter. Replace **~URI~** with the **URI**, and replace **~KEY~** with the **PRIMARY KEY** values that you noted earlier for the **20777a-sql-\<your name\>-\<the day\>** Cosmos DB instance.

    ```bash
    spark.sql("create table delaydata using com.microsoft.azure.cosmosdb.spark options (endpoint '~URL~', database 'flightdelays', collection 'delaydata', masterkey '~KEY~')")
    ```

20. At the **scala\>** prompt, type the following, and then press Enter to verify that the table definition was created:

    ```bash
    spark.sql("select UniqueCarrier from delaydata limit 10").show()
    ```
    > The results should include 10 values of the **UniqueCarrier** property.

21. At the **scala\>** prompt, type **:quit**, and then press Enter to exit the Spark shell.
    > **Note:**: If this fails to exit, press CTLR+C.

22. At the PuTTY prompt, type **exit**, and then press Enter.

#### Task 5: Connect PowerBI to HDInsight and Spark

1.  On the desktop, double-click **Power BI Desktop**.

2.  In the Power BI Desktop window, click **Get data**.

3.  In the **Get Data** dialog box, click **Azure**, click **Azure HDInsight Spark**, and then click **Connect**.

4.  In the **Azure HDInsight Spark** dialog box, in the **Server** box, type **hdi-\<your name\>\<date\>.azurehdinsight.net**, click **DirectQuery**, and then click **OK**.

5.  In the **Spark** dialog box, in the **User name** box, type **sparkadmin**.

6.  In the **Password** box, type **Pa55w.rdPa55w.rd**, and then click **Connect**.

7.  In the **Navigator** dialog box, select the **delaydata** check box, and then click **Load**.

8.  On the **Home** menu, click **New Visual**.

9.  In the **VISUALIZATIONS** pane, click the vertical **Clustered column chart** icon (the fourth icon on the top row).

10. In the **FIELDS** pane, click and drag **UniqueCarrier** to the **VISUALIZATIONS** pane, then drop **UniqueCarrier** on the **Axis** data field.

11. In the **FIELDS** pane, select the **Cancelled**, and **Diverted** check boxes—observe that in the **VISUALIZATIONS** pane, the **Cancelled** and **Diverted** appear on the **Value** data field.

12. Resize the visual to make the graph easier to see.

#### Task 6: Show live data updates

1.  In Internet Explorer, click **All resources**, click **20777a-sql-\<your name\>-\<the day\>**, and then click **Data Explorer**.

2.  In the **SQL API** pane, right-click **delaydata**, and then click **Delete Collection**.

3.  On the **Delete Collection** blade, in the **Confirm by typing the collection id** box, type **delaydata**, and then click **OK**.

4.  In the **SQL API** pane, click **New Collection**.

5.  On the **Add Collection** blade, click **Use existing**, and then in the drop-down list, click **flightdelays**.

6.  In the **Collection Id** box, type **delaydata**.

7.  In the **Throughput (400 - 10,000 RU/s)** box, type **5000**, and then click **OK**.

8.  In Power BI, click **Refresh**. Observe that the bars disappear from the chart because the Cosmos DB collection is now empty.

9.  In Internet Explorer, on the **20777a-sql-\<your name\>-\<the day\>** blade, under **SETTINGS**, click **Keys**.

10. Make a note of the **PRIMARY CONNECTION STRING** value.

11. In File Explorer, go to **E:\\Demofiles\\Mod06\\Demo02**, right-click **Demo02-setup.ps1**, and then click **Run with PowerShell**.

12. Right-click anywhere inside the PowerShell window to paste your primary connection string, and then press Enter.

13. While the script is running, switch to Power BI and click **Refresh** several times. Notice that the values on the chart change as data is loaded into the collection.

14. On the **File** menu, click **Save As**.

15. In the **Save As** dialog box, in the **File name** box, type **E:\\Demofiles\\Mod06\\Demo05\\FlightDelaysDynamic.pbix**, and then click **Save**.

16. Close **Power BI**.

17. In PowerShell, press Enter.

18. Leave Internet Explorer open for the next demonstration.

### Demo 3: Visualizing data using Azure Databricks

#### Preparation 

Complete the previous demonstration in this module.

#### Task 1: Create an Azure Databricks service

1.  In Internet Explorer, in the Azure portal, click **+ Create a resource**, in the search box, type **Azure Databricks**, and then press Enter.

2.  On the **Everything** blade, click **Azure Databricks**, and then click **Create**.

3.  On the **Azure Databricks Service** blade, in the **Workspace name** box, type **20777a-databricks-\<your name\>-\<the day\>**, for example, **20777a-databricks-john-31**.

4.  In the **Resource group** box, click **Create new**, and then type **DatabricksGroup**.

5.  In the **Location** drop-down list, click **West US 2**.

    > **Note**: Currently, not all regions support the range of VMs used by Azure Databricks to host Spark clusters. West US 2 does.

6.  In the **Pricing Tier** drop-down list, click **Trial**, and then click **Create**.
7.  Wait while the service is deployed.

#### Task 2: Create an Azure Databricks cluster

1.  In the left panel, click **All resources**, and then click **20777a-databricks-\<your name\>-\<the day\>**.

2.  On the **20777a-databricks-\<your name\>-\<the day\>** blade, click **Launch Workspace**.

3.  On the **Azure Databricks** page, under **Common Tasks**, click **New Cluster**.

4.  On the **New Cluster** page, in the **Cluster Name** box, type **\<your name\>-cluster**.

5.  In the **Max Workers** box, type **4**.

6.  Leave all other settings at their default values, and then click **Create Cluster**.

7.  Wait while the cluster is created and started. Verify that its **State** is set to **Running** before continuing.

#### Task 3: Import the pydocumentdb library and create a PySpark notebook

1.  In the left toolbar, click **Azure Databricks**.

2.  Under **Common Tasks**, click **Import Library**.

3.  On the **New Library** page, in the **Source** drop-down list, click **Upload Python Egg or PyPI**.

4.  In the **PyPi Name** box, type **pydocumentdb**, and then click **Install Library**.

5.  On the **pydocumentdb** page, in the **\<your name\>-cluster** row, select the **Attach** check box, and wait until the status changes to **Attached**.

6.  In the toolbar on the left of the blade, click **Azure Databricks**.

7.  Under **Common Tasks**, click **New Notebook**.

8.  In the **Create Notebook** dialog box, in the **Name** box, type **FlightDelaysAnalysis**.

9.  In the **Language** drop-down list, click **Python**, click **\<your name\>-cluster**, and then click **Create**.

#### Task 4: Retrieve data from Cosmos DB using PySpark

> **Note:** The pyspark code used in this step can be found in **E:\\Demofiles\\Mod06\\Demo06\\flightdelays.txt**.

1.  In the first cell of the notebook, enter the following code that specifies the modules that will be used by the notebook:

    ```python
    import pydocumentdb
    from pydocumentdb import document_client
    from pydocumentdb import documents
    import datetime
    ```
2.  In the toolbar at the top right-hand side of the cell, on the **Edit Menu** menu, click **Add Cell Below**.

3.  Add the following code to the new cell. This code uses the pydocumentdb library to create a client object that can connect to your Cosmos DB account. Replace **--URI--** with the **URI**, and replace **--KEY--** with the **PRIMARY KEY** values that you noted earlier for the **20777a-sql-\<your name\>-\<the day\>** Cosmos DB account.

    ```python
    connectionPolicy = documents.ConnectionPolicy()
    connectionPolicy.EnableEndpointDiscovery
    host = '--URL--'
    primaryKey = '--KEY--'
    client = document_client.DocumentClient(host, {'masterKey': primaryKey}, connectionPolicy)
    ```
4.  In the toolbar at the top right-hand side of the cell, on the **Edit Menu** menu, click **Add Cell Below**.

5.  Add the following code to the new cell. This code creates a link that you can use to connect to the **delaydata** collection in the **flightdelays** database.

    ```python
    databaseId = 'flightdelays'
    collectionId = 'delaydata'
    dbLink = 'dbs/' + databaseId
    collLink = dbLink + '/colls/' + collectionId
    ```
6.  In the toolbar at the top right-hand side of the cell, on the **Edit Menu** menu, click **Add Cell Below**.

7.  Add the following code to the new cell. This code defines the query that will be run. The query returns flight delay data for flights that depart Los Angeles (LAX) airport.

    ```python
    querystr = "SELECT c.UniqueCarrier, c.DepDelay, c.DayOfWeek, c.Dest FROM c WHERE c.DepDelay <> 'NA' AND c.ArrDelay <> 'NA' AND c.Origin = 'LAX'"
    ```
8.  In the toolbar at the top right-hand side of the cell, on the **Edit Menu** menu, click **Add Cell Below**.

9.  Add the following code to the new cell. This code runs the query against the collection using the link created earlier.

    ```python
    query = client.QueryDocuments(collLink, querystr, options=None, partition_key=None)
    ```
10. In the toolbar at the top right-hand side of the cell, on the **Edit Menu** menu, click **Add Cell Below**.

11. Add the following code to the new cell. This code constructs a Spark dataframe using the data returned by the query.

    ```python
    df = spark.createDataFrame(list(query))
    ```
12. In the toolbar at the top right-hand side of the cell, on the **Edit Menu** menu, click **Add Cell Below**.

13. Add the following code to the new cell. This code displays the results of the query in the notebook.

    ```python
    display(df)
    ```

#### Task 5: Run the notebook and visualize the data

1.  In the menu bar at the top of the notebook, click **Run All**.

2.  When the notebook has finished running, you should see a table that contains the rows retrieved by the query in the output of the last cell.

3.  Below the table, click the **Visualizations** button (the middle button of the three that appear), and then click **Bar**. The table is replaced by a bar chart.

4.  Click **Plot Options**.

5.  In the **Customize Plot** dialog box, in the **Keys** box, delete **UniqueCarrier**.

6.  Drag **Dest** from the **Keys** box to the **Series** groupings box.

7.  In the **All fields** box, drag **DayOfWeek** to the **Keys** box.

8.  In the **Aggregation** drop-down list, click **COUNT**, and then click **Apply**. Notice that the final cell runs again, using a Spark job to group and aggregate the data before displaying the results.

9.  Resize the graph to make it easier to view. The graph should display the number of outbound flights from LAX that had a departure delay. The data is grouped by day of week for each destination.

10. Close the **FlightDelaysAnalysis** tab.
11. In the **Windows Internet Explorer** dialog box, click **Leave this page**.

#### Task 6: Demonstration clean-up

1.  In the Azure portal, in the left panel, click **Resource groups**.

2.  On the **Resource groups** blade, right-click **20777aMod6**, and then click **Delete resource group**.

3.  On the **Are you sure you want to delete "20777aMod6"?** blade, in the **TYPE THE RESOURCE GROUP NAME** box, type **20777aMod6**, and then click **Delete**.

4.  Repeat steps 1 to 3 for the **Databricks** entries.

---
© 2018 Microsoft Corporation. All rights reserved.

The text in this document is available under the [Creative Commons Attribution 3.0 License](https://creativecommons.org/licenses/by/3.0/legalcode), additional terms may apply. All other content contained in this document (including, without limitation, trademarks, logos, images, etc.) are **not** included within the Creative Commons license grant. This document does not provide you with any legal rights to any intellectual property in any Microsoft product. You may copy and use this document for your internal, reference purposes.

This document is provided “as-is.” Information and views expressed in this document, including URL and other Internet Web site references, may change without notice. You bear the risk of using it. Some examples are for illustration only and are fictitious. No real association is intended or inferred. Microsoft makes no warranties, express or implied, with respect to the information provided here.
