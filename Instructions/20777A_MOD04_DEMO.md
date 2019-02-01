# Module 4: Optimizing and Monitoring Performance

- [Module 4: Optimizing and Monitoring Performance](#module-4-optimizing-and-monitoring-performance)
  - [Lesson 1: Optimizing database performance](#lesson-1-optimizing-database-performance)
    - [Demo 1: Trade-Offs with Consistency Levels](#demo-1-trade-offs-with-consistency-levels)
      - [Preparation](#preparation)
      - [Task 1: Configure the Cosmos DB Account and Database](#task-1-configure-the-cosmos-db-account-and-database)
      - [Task 2: Test Session Consistency in a Single Client](#task-2-test-session-consistency-in-a-single-client)
      - [Task 3: Test Eventual Consistency in a Single Client](#task-3-test-eventual-consistency-in-a-single-client)
      - [Task 4: Test Consistency Across Multiple Clients](#task-4-test-consistency-across-multiple-clients)
      - [Task 5: Drop the TestDatabase database (to save costs).](#task-5-drop-the-testdatabase-database-to-save-costs)
    - [Demo 2: Understanding the Impact of Partitioning and Index Policy](#demo-2-understanding-the-impact-of-partitioning-and-index-policy)
      - [Preparation](#preparation-1)
      - [Task 1: Test the Effects of Index Policy on Insert Operations](#task-1-test-the-effects-of-index-policy-on-insert-operations)
      - [Task 2: Create Collections with Different Partitioning Strategies](#task-2-create-collections-with-different-partitioning-strategies)
      - [Task 3: Compare the Performance of Different Partitioning Strategies](#task-3-compare-the-performance-of-different-partitioning-strategies)
      - [Task 4: Examine the Physical to Logical Mapping of Partitions](#task-4-examine-the-physical-to-logical-mapping-of-partitions)
  - [Lesson 2: Monitoring the performance of a database](#lesson-2-monitoring-the-performance-of-a-database)
    - [Demo 1: Monitoring a document database](#demo-1-monitoring-a-document-database)
      - [Preparation](#preparation-2)
      - [Task 1: Examine How Consistency Affects Throughput](#task-1-examine-how-consistency-affects-throughput)
      - [Task 2: Examine the Physical to Logical Mapping of Partitions](#task-2-examine-the-physical-to-logical-mapping-of-partitions)

## Lesson 1: Optimizing database performance

### Demo 1: Trade-Offs with Consistency Levels

#### Preparation

Before starting this demo:

1. Ensure that the **MT17B-WS2016-NAT** and **20777A-LON-DEV** virtual machines are running, and then log on to **20777A-LON-DEV** as **LON-DEV\Administrator** with the password **Pa55w.rd**.

2. On the toolbar, click **File Explorer**.

3. In File Explorer, navigate to **E:\\Demofiles\\Mod04**, right-click **Setup.cmd**, and then click **Run as administrator**.

4. On the toolbar, click **Internet Explorer**.

5. In Internet Explorer, go to **http://portal.azure.com**, and sign in using the Microsoft account that is associated with your Azure Learning Pass subscription.

    > **Note**: preparation steps 6 to 18 create a Cosmos DB account, a database, and a collection.

6. In the Azure portal, in the left panel, click **Azure Cosmos DB**, and then click **+ Add**.

7. On the **Azure Cosmos DB** blade, under the **Resource Group** box, click **Create new**, type **20777aMod4**, and then click **OK**.

8. In the **Account name** box, type **20777a-sql-\<*your name\>-\<the day*\>**, for example, **20777a-sql-john-31**.

9. In the **API** drop-down list, click **Core (SQL)**.

10. In the **Location** drop-down list, click **UK West**, click **Review + create**, and then click **Create**.

11. Wait for the Azure Cosmos DB to be created—this could take a few minutes.

12. Click **Go to resource**.

13. On the **20777a-sql-\<*your name\>-\<the day*\>** blade, under **Settings**, click **Replicate data globally**, and then click **Add region**.

14. On the **Replicate data globally** blade, in the **Search for a region** drop-down list, click a region that is on a different continent, and then click **OK**.

15. On the **Replicate data globally** blade, click **Save**. Wait until the process is complete, this can take upto 5 minutes.  

16. On the **20777a-sql-\<*your name\>-\<the day*\>** blade, click **Data Explorer**, and then click **New Database**.

17. On the **New Database** blade, in the **Database id** box, type **DeviceData**, and then click **OK**.

18. In the **SQL API** pane, click **New Collection**.

19. On the **Add Collection** blade, under **Database id**, click **Use existing**, and then in the drop-down list, click **DeviceData**.

20. In the **Collection Id** box, type **Temperatures**.

21. In the **Partition key** box, type **/deviceID**, and then click **OK**.

    > **Note**: preparation steps 22 to 25 download and build the latest version of the Cosmos DB data migration tool. You do not need to carry out these step if you completed it in an earlier module (and already have a **E:\\dmt** folder on **20777A-LON-DEV**); if you already completed these steps, skip ahead to step 26.

22. On the toolbar, click **File Explorer**.

23. In File Explorer, navigate to **E:\\Resources**, right-click **build\_data\_migration\_tool.ps1**, and then click **Run with PowerShell**.

24. If the **Open File - Security Warning** dialog box appears, click **Open**.

25. Wait for the script to finish, and then press Enter.

26. In Internet Explorer, on the **20777a-sql-\<*your name\>-\<the day*\>** blade, under **Settings**, click **Keys**.

27. Make a note of the **URI**, and **PRIMARY KEY** values.

#### Task 1: Configure the Cosmos DB Account and Database

1. In Internet Explorer, on the **20777a-sql-\<*your name\>-\<the day*\>** blade, under **Settings**, click **Default consistency**.

2. Verify that the default consistency for the account is **SESSION**.

3. On the **20777a-sql-\<*your name\>-\<the day*\>** blade, click **Data Explorer**, and then click **New Database**.

4. On the **New Database** blade, in the **Database id** box, type **TestDatabase**, and then click **OK**.

#### Task 2: Test Session Consistency in a Single Client

1. On the Start menu, click **Visual Studio 2017**.

2. On the **File** menu, point to **Open**, and then click **Project/Solution**.

3. In the **Open Project** dialog box, navigate to **E:\\Demofiles\\Mod04\\ConsistencyTester**, click **ConsistencyTester.sln**, and then click **Open**.

4. In Solution Explorer, click **Writer.cs**.

5. On the **Writer.cs** tab, notice the following:
    - The **Writer** constructor, which performs some initialization and creates a **client** object for connecting to the database.

    - The **StartWritingAsync** method wich tests the consistency of the database and collection (described below).

    - The **CreateDataAsync** method which initializes the collection and test documents used by the **StartWritingAsync** method. The structure of the documents used is very simple, consisting two fields named **docid** and **value**, as shown in the example below (which also includes the system fields):

        ```JSON
        {
        "docid": "doc0",
        "value": 0,
        "id": "9415ba5a-5917-4b91-91d1-b40438f6f30a",
        "_rid": "d51LAORzMwABAAAAAAAAAA==",
        "_self": "dbs/d51LAA==/colls/d51LAORzMwA=/docs/d51LAORzMwABAAAAAAAAAA==/",
        "_etag": "\"00005f06-0000-0000-0000-5b5098810000\"",
        "_attachments": "attachments/",
        "_ts": 1532008577
        }
        ```

    - The **WaitForCollection** method which waits for the newly created collection to be replicated across regions.


6. Examine the **StartWritingAsync** method in more detail. This method queries the docs in the collection at random, and increments the number in the **value** field in the selected doc before writing it back to the collection. This process is performed repeatedly (see the **NumIterations** setting described below). For each iteration, the method repeatedly retrieves the same doc until the data in the **value** field is the same for two successive reads, before performing the increment. The results of each read for each iteration are written to a CSV file. In a system configured to use eventual consistency, there is the possibility that the write for a previous iteration (or two) might not be immediately visible. Repeating the reads until the same value is read twice in succession captures how the data changes as it is saved between replicas (and database nodes, even in a non-replicated environment). Note that this approach is not foolproof, and it is possible that some updates might still be lost (if you wait long enough, updates will always appear, but how long is **long enough**?), but this demo is intended simply to illustrate the effects of (in)consistency\!

7. In Solution Explorer, click **App.config**.

8. On the **App.config** tab, enter the values for the following settings:
    - **EndpointUrl**. Replace **\~URI\~** with the **URI** you noted earlier from the **20777a-sql-\<*your name\>-\<the day*\>** Cosmos DB account.

    - **PrimaryKey**. Replace **\~PRIMARY KEY\~** with the **PRIMARY KEY** you noted earlier from the**20777a-sql-\<*your name\>-\<the day*\>** Cosmos DB account.

    - **Database**. Leave this set to **TestDatabase**, unless you created a database with a different name in Task 1.

    - **Collection**. This is the name of the test collection that will be created. Leave it set to **TestDocs**.

    - **WriterReadRegion**. This is the name of the region that the client will connect to for reading data (all writes will be directed to the primary write region for the Cosmos DB account, but the read region can be any region holding a replica). For the time being, set this region to the same as the primary write region (you can experiment with the effects of connecting to replicas for reading data in your own time).

    - **ConsistencyLevel**. This is the consistency level that the app will use. Change it to **Session**.

    - **NumIterations**. This is the number of times that the **StartWritingAsync** method in the **Writer** class will perform read and write operations. Leave it set to **200**.

    - **NumDocs**. This is the number of documents to create in the test collection. The effects of different isolation levels are most easily demonstrated using a single doc, but you can experiment with creating multiple docs to see the effects.

    - **CreateDocs**. Leave this set to **true** to drop and recreate the test collection each time you run the app.

9. On the **Build** menu, click **Build Solution**, and wait until the build has succeeded.

10. On the **Debug** menu, click **Start Debugging**, and wait for it to complete.

11. In File Explorer, navigate to **E:\\Demofiles\\Mod04**, right-click **Writer.csv**, and then click **Edit**.

12. In Notepad, the contents should look similar to that shown below. Each line represents a single iteration. The **New Value** is the data stored in the **value** field after incrementing it and saving the doc. The **Old Values** data is the data that was retrieved before the **value** was incremented. In a completely consistent system, there will be single old value, that is the same as the new value in the line above, and after 200 iterations the data displayed for the **value** field should be 200:

    ```Text
    Time,Iteration,Name,Writer Read Region,DocID,New Value,Old Values
    636676075124668243,0,Writer,UK West,doc0,1,0
    636676075125868232,1,Writer,UK West,doc0,2,1
    636676075127168377,2,Writer,UK West,doc0,3,2
    636676075128288050,3,Writer,UK West,doc0,4,3
    636676075129388085,4,Writer,UK West,doc0,5,4
    636676075130508098,5,Writer,UK West,doc0,6,5
    636676075131658270,6,Writer,UK West,doc0,7,6
    636676075132788086,7,Writer,UK West,doc0,8,7
    636676075133878891,8,Writer,UK West,doc0,9,8
    636676075134978809,9,Writer,UK West,doc0,10,9
    636676075136198925,10,Writer,UK West,doc0,11,10
    636676075137278868,11,Writer,UK West,doc0,12,11
    ...
    636676075345484458,199,Writer,UK West,doc0,200,199
    ```
13. Close Notepad.

#### Task 3: Test Eventual Consistency in a Single Client

1. In Visual Studio, on the **App.config** tab, change the **ConsistencyLevel** to **Eventual**.

2. On the **Build** menu, click **Build Solution**, and wait until the build has succeeded.

3. On the **Debug** menu, click **Start Debugging**, and wait for it to complete.

4. In File Explorer, navigate to **E:\\Demofiles\\Mod04**, right-click **Writer.csv**, and then click **Edit**.

5. In Notepad, as you scroll down, you will often see rows where an iteration has had to perform repeated reads before getting the same result twice in succession. In the second block shown below (iterations 40-44), you can see that the reads eventually settled on the value written by the previous iteration. However, in some cases, it would actually require more than two successive reads returning the same value to actually retrieve the most up-to-date value (see iterations 64-66). In this case, iteration 64 read the value 61, and wrote the value 62. The next iteration read the value 62, followed by 61, and then another 61 (the results of the second read if consecutive values are consistent are not output to the CSV file), resulting in 61 being the accepted value and 62 being written again. Iteration 66 reads the values 61, followed by 62, followed by another 62, so it writes the value 63. These lost updates account for why, after performing 200 iterations, the final value was less than 200.

    ```Text
    Time,Iteration,Name,Writer Read Region,DocID,New Value,Old Values
    636676078609167640,0,Writer,UK West,doc0,1,0
    636676078610228251,1,Writer,UK West,doc0,2,1
    ...
    636676078651406314,40,Writer,UK West,doc0,41,40
    636676078653776271,41,Writer,UK West,doc0,42,41,40,41,40,41
    636676078656196093,42,Writer,UK West,doc0,43,42,41,42,41,42
    636676078657914654,43,Writer,UK West,doc0,44,43,42,43
    636676078658924556,44,Writer,UK West,doc0,45,44
    ...
    636676078684454377,64,Writer,UK West,doc0,62,61
    636676078685796644,65,Writer,UK West,doc0,62,62,61
    636676078687176408,66,Writer,UK West,doc0,63,61,62
    ...
    ```

    > **Note:** These results are caused by using **Eventual** consistency in a single region (the read and write regions are the same), and are due to the way in which Cosmos DB is implemented using multiple nodes in each region. When using **Eventual** consistency, your app can retrieve data from any node, even if the most recent change has yet to be propagated to that node. When using **Session** consistency, your app will only see data that has been committed by a quorum of the nodes.

6. Close Notepad.

#### Task 4: Test Consistency Across Multiple Clients

1. In Visual Studio, on the **File** menu, point to **Open**, and then click **Project/Solution**.

2. In the **Open Project** dialog box, navigate to **E:\\Demofiles\\Mod04\\ConsistencyTester with Multiple Readers**, click **ConsistencyTester.sln**, and then click **Open**. This app is an extension of that used in tasks 2 and 3; it supports multiple concurrent readers as well as the writer.

3. In Solution Explorer, click **Writer.cs**.

4. On the **Writer.cs** tab, notice that the upsert functionality now includes the following code. This code caches the session token generated by the Cosmos DB request when the data is written to the database:

    ```Csharp
    // If the app is using Session consistency, record the session token. A reader using the same session token should receive the value just written ("read your own writes")
    if (Globals.GetConsistencyLevel() == ConsistencyLevel.Session)
    {
        Globals.sessionToken = upsertResponse.SessionToken;
    }
    ```

5. In Solution Explorer, click **Reader.cs**.

6. On the **Reader.cs** tab, notice this file defines the **Reader** class. The **StartReadingAsync** method performs a configurable number of iterations that selects a document from the collection, retrieves it, and writes the contents to a file, together with other information such as the current time (in ticks), and an identifier for the reader. The reader includes the session token written by the writer in the **FeedOptions** object that is specified when the query is performed. If the app is configured to use Session consistency, this will ensure that the reader always reads the most recent value written by the writer:

    ```Csharp
    var options = new FeedOptions
    {
        ConsistencyLevel = Globals.GetConsistencyLevel(),
        SessionToken = Globals.sessionToken
    };

    var query = this.client.CreateDocumentQuery(Globals.collectionUri, queryString, options).AsDocumentQuery();
    ```

7. In Solution Explorer, click **App.config**.

8. On the **App.config** tab, enter the values for the following settings:

    - **EndpointUrl**. Replace **\~URI\~** with the **URI** you noted earlier from the **20777a-sql-\<*your name\>-\<the day*\>** Cosmos DB account.

    - **PrimaryKey**. Replace **\~PRIMARY KEY\~** with the **PRIMARY KEY** you noted earlier from the**20777a-sql-\<*your name\>-\<the day*\>** Cosmos DB account.

    - **Database**. Leave this set to **TestDatabase**.

    - **Collection**. Leave this set to **TestDocs**.

    - **WriterReadRegion**. Set this region to the same as the primary write region.

    - **ConsistencyLevel**. Set this to **Eventual**.

    - **NumIterations**. This is the number of times that the **StartReadingAsync** method in the **Reader** class will perform read operations. Leave it set to **200**.

    - **NumDocs**. Leave this set to **1**.

    - **CreateDocs**. Leave this set to **true**.

    - **NumReaders**. Leave this set to **5**.

    - **ReaderRegion**. Set this region to the same as the primary reader region.

9. On the **Build** menu, click **Build Solution**, and wait until the build has succeeded.

10. On the **Debug** menu, click **Start Debugging**, and wait for it to complete.

11. On the Start menu, type **powershell**, and then click **Windows PowerShell**.

12. At the PowerShell prompt, type **cd E:\\Demofiles\\Mod04**, and then press Enter.

13. At the PowerShell prompt, type the following command, and then press Enter:

    ```Powershell
    Get-Content *.csv | Sort-Object | Out-File Results.csv
    ```

14. In File Explorer, navigate to **E:\\Demofiles\\Mod04**, right-click **Results.csv**, and then click **Edit**.

15. In Notepad, examine the contents of the file. Initially the file should contain a number of lines for the writer, while the readers are getting started. The output should be similar to that shown before, and sometimes the writer may have to perform multiple reads to wait for the data to settle (this run of the app is using Eventual consistency).
    - Scroll through the file, and look for the output of the readers. You should see lines that resemble the following. In the fragment below, the writer has written the value 48 to the doc. Reader 2 and Reader 0 both read this latest value, but Reader 3 and Reader 4 see an earlier value, 46. Additionally, while Reader 1 sees the latest value initially, a subsequent request by the same reader fetches an earlier value, 47. You should see this phenomena occur regularly throughout the file.

    ```Text
    636679496897053516,48,Writer,UK West,doc0,48,47
    636679496897063528,5,Reader 2,UK West,doc0,48
    636679496897114146,5,Reader 0,UK West,doc0,48
    636679496897273626,5,Reader 3,UK West,doc0,46
    636679496897293512,6,Reader 4,UK West,doc0,46
    636679496897343528,6,Reader 1,UK West,doc0,48
    636679496897363489,6,Reader 2,UK West,doc0,48
    636679496897403513,6,Reader 0,UK West,doc0,46
    636679496897563501,7,Reader 4,UK West,doc0,46
    636679496897593514,6,Reader 3,UK West,doc0,48
    636679496897645188,7,Reader 1,UK West,doc0,47
    636679496897653480,7,Reader 2,UK West,doc0,48
    636679496897703484,7,Reader 0,UK West,doc0,48
    636679496897833484,8,Reader 4,UK West,doc0,48
    636679496897913513,7,Reader 3,UK West,doc0,48
    636679496897933493,8,Reader 1,UK West,doc0,48
    636679496897953496,8,Reader 2,UK West,doc0,48
    636679496898005103,8,Reader 0,UK West,doc0,47
    636679496898063522,49,Writer,UK West,doc0,49,48
    636679496898103525,9,Reader 4,UK West,doc0,49
    636679496898243492,9,Reader 1,UK West,doc0,47
    636679496898243492,9,Reader 2,UK West,doc0,49
    636679496898253549,8,Reader 3,UK West,doc0,47
    636679496898313469,9,Reader 0,UK West,doc0,47
    636679496898363900,10,Reader 4,UK West,doc0,49
    636679496898553509,10,Reader 1,UK West,doc0,49
    636679496898563518,10,Reader 2,UK West,doc0,49
    636679496898603475,10,Reader 0,UK West,doc0,49
    636679496898633483,11,Reader 4,UK West,doc0,49
    636679496898633483,9,Reader 3,UK West,doc0,49
    636679496898853539,11,Reader 1,UK West,doc0,48
    636679496898893712,11,Reader 0,UK West,doc0,49
    636679496898893712,11,Reader 2,UK West,doc0,49
    636679496898923528,12,Reader 4,UK West,doc0,49
    636679496898943493,10,Reader 3,UK West,doc0,49
    ```
16. Close Notepad.

17. In File Explorer, delete **E:\\Demofiles\\Mod04\\Results.csv**.

18. In Visual Studio, on the **App.config** tab, change the **ConsistencyLevel** setting to **Session**.

19. On the **Build** menu, click **Build Solution**, and wait until the build has succeeded.

20. On the **Debug** menu, click **Start Debugging**, and wait for it to complete.

21. In PowerShell, at the PowerShell prompt, type the following, and then press Enter:

    ```Powershell
    Get-Content *.csv | Sort-Object | Out-File Results.csv
    ```
22. In File Explorer, navigate to **E:\\Demofiles\\Mod04**, right-click **Results.csv**, and then click **Edit**.

23. In Notepad, examine the data. This time the app is using Session consistency, so the writer will always see the data it last wrote and there should not be any rows showing repeated read operations. Additionally, as the readers all share the same session token as the most recent write operation, they should all see the same, most recent value of the data, as shown in the example output below:

    ```Text
    636679518974718640,54,Writer,UK West,doc0,55,54
    636679518975088942,0,Reader 2,UK West,doc0,55
    636679518975088942,0,Reader 3,UK West,doc0,55
    636679518975098741,0,Reader 4,UK West,doc0,55
    636679518975128701,0,Reader 0,UK West,doc0,55
    636679518975138655,0,Reader 1,UK West,doc0,55
    636679518975388644,1,Reader 2,UK West,doc0,55
    636679518975438675,1,Reader 3,UK West,doc0,55
    636679518975448641,1,Reader 1,UK West,doc0,55
    636679518975458635,1,Reader 0,UK West,doc0,55
    636679518975478677,1,Reader 4,UK West,doc0,55
    636679518975528655,55,Writer,UK West,doc0,56,55
    636679518975658656,2,Reader 2,UK West,doc0,56
    636679518975698637,2,Reader 3,UK West,doc0,56
    636679518975748642,2,Reader 0,UK West,doc0,56
    636679518975768695,2,Reader 1,UK West,doc0,56
    636679518975788689,2,Reader 4,UK West,doc0,56
    636679518975928931,3,Reader 2,UK West,doc0,56
    636679518975968654,3,Reader 3,UK West,doc0,56
    636679518976038651,3,Reader 0,UK West,doc0,56
    636679518976058681,3,Reader 1,UK West,doc0,56
    636679518976078693,3,Reader 4,UK West,doc0,56
    636679518976188727,4,Reader 2,UK West,doc0,56
    636679518976248734,4,Reader 3,UK West,doc0,56
    636679518976348673,4,Reader 0,UK West,doc0,56
    636679518976348673,4,Reader 4,UK West,doc0,56
    636679518976358715,4,Reader 1,UK West,doc0,56
    ```

24. Close Notepad, close PowerShell, and then close Visual Studio.

#### Task 5: Drop the TestDatabase database (to save costs).

1. In Internet Explorer, on the **20777a-sql-\<*your name\>-\<the day*\>** blade, click **Data Explorer**.

2. In the **SQL API** pane, right-click **TestDatabase**, and then click **Delete Database**.

3. On the **Delete Database** blade, in the **Confirm by typing the database id** box, type **TestDatabase**, and then click **OK**.

### Demo 2: Understanding the Impact of Partitioning and Index Policy

**Scenario:** You have a dataset stored as a JSON file that contains ball-by-ball summaries of international cricket matches. Each document in the dataset has the following format.

The **info** subdocument contains the overall information about the match, such as the teams, location, match type (T20, ODI, Test), the umpires, the toss (the winner chooses whether to bat or bowl first), and the match result.

The **innings** subdocument is a ball-by-ball summary of an innings, describing who the bowler was, the batsman facing the delivery, and the outcome of the delivery (were any runs scored, was it a no-ball or wide, was the batsman out and if so, how, and so on).

For a T20 and an ODI match, there will be one innings per team. A T20 match has 20 overs per side (an over is 6 deliveries) in an innings (120 deliveries plus any extras), and an ODI has 50 overs per side (300 deliveries plus extras). A Test match has up to two innings per team, and each innings can vary in length (a team bats until all its players are out or they declare).

You want to store this information in a document database, but want to assess the most optimal way to index the data, and the affects that different partitioning strategies will have on the performance of some common queries, such as:

- Find the details of matches of a particular type (T20, ODI, Test) where team XYZ was playing at home.

- Find the details of matches of a particular type where team XYZ was playing away.

- Find the details of matches of a particular type where team XYZ was playing team ABC.

- Find the details of matches of a particular type played in a specified city.

- Find the details of all matches of a particular type.

#### Preparation

Before starting this demo:

1. In Internet Explorer, on the **20777a-sql-\<*your name\>-\<the day*\>** blade, under **Settings**, click **Default consistency**, and verify that the default consistency for the account is **SESSION**.

2. On the **20777a-sql-\<*your name\>-\<the day*\>** blade, click **Data Explorer**, and then click **New Database**.

3. On the **New Database** blade, in the **Database id** box, type **Cricket**, and then click **OK**.

4. On the **20777a-sql-\<*your name\>-\<the day*\>** blade, under **Settings**, click **Keys**.

5. Make a note of the **PRIMARY CONNECTION STRING** value.

    > **Note**: preparation steps 6 to 8 download and build the latest version of the Cosmos DB data migration tool. You do not need to carry out these step if you completed it in an earlier module (and already have a **E:\\dmt** folder on **20777A-LON-DEV**); if you already completed these steps, skip ahead to the next task.

6. On the toolbar, click **File Explorer**.

7. In File Explorer, navigate to **E:\\Resources**, right-click **build\_data\_migration\_tool.ps1**, and then click **Run with PowerShell**.

8. Wait for the script to finish, and then press Enter.

#### Task 1: Test the Effects of Index Policy on Insert Operations

1. In File Explorer, navigate to **E:\\dmt\\bin\\dtui**, and then double-click **dtui.exe**.

2. In the **DocumentDB Data Migration Tool** window, on the **Welcome** page, click **Next**.

3. On the **Source Information** page, in the **Import from** drop-down list, click **JSON files(s)**, and then click **Add Files**.

4. In the **Open** dialog box, navigate to the **E:\\Demofiles\\Mod04\\Data** folder, click **MatchesData.json**, and then click **Open**.

5. On the **Source Information** page, click **Next**.

6. On the **Target Information** page, in the **Export to** drop-down list, click **DocumentDB - Sequential record import (partitioned collection)**.

    > **Note:** The collection created by this load operation is not actually going to be partitioned, but selecting the **Sequential record import** option will give you a fair comparison of the impact of indexing with subsequent load operations that import data into a partitioned collection.

7. In the **Connection String** box, enter the **PRIMARY CONNECTION STRING** for the **20777a-sql-\<*your name\>-\<the day*\>** Cosmos DB account.

8. At the end of the string, append the text **database=Cricket**.

9. In the **Collection** box, type **Matches**.

10. Leave the **Partition Key** box empty.

11. Leave the **Collection Throughput** box set to **1000** (the default).

12. In the **Id Field** box, type **id**.

13. Expand **Advanced Options**, select the **Disable Automatic Id Generation** check box, and then click **Next**.

14. On the **Advanced** page, click **Next**.

15. On the **Summary** page, click **Import**.

    > **Note:** While the import is running, observe that the load rate is very slow. This is because of the index applied to the **innings** subdocument. This subdocument contains many fields, and constructing the index for this subdocument can take a considerable time.

16. Leave the import running.

17. In Internet Explorer, on the **20777a-sql-\<*your name\>-\<the day*\>** blade, click **Data Explorer**.

18. In the **SQL API** pane, expand **Cricket**, expand **Matches**, and then click **Scale & Settings**.

19. On the **Scale & Settings** tab, change the **Throughput** to **10000**, and then click **Save**.

20. In DocumentDB Data Migration Tool, notice that the rate of import improves significantly. However, you are now incurring ten-times the financial cost.

21. Wait for the import operation to complete (there are 3939 documents).

22. In Internet Explorer, on the **Scale & Settings** tab, change the **Throughput** to **1000** (to save costs), and then click **Save**.

23. In the DocumentDB Data Migration Tool, click **New Import**.

24. In the **New Import** dialog box, click **No** to reuse the settings from the previous run.

25. On the **Source Information** page, ensure that the JSON file **E:\\Demofiles\\Mod04\\Data\\MatchesData.json** is specified, and then click **Next**.

26. On the **Target Information** page, ensure the connection string is the same as before.

27. In the **Collection** box, type **MatchesInningsNotIndexed**.

28. Leave the **Partition Key** empty.

29. Leave the **Collection Throughput** set to **1000** (the default).

30. Leave the **Id Field** set to **id**.

31. In the **Advanced Options** section, leave the **Disable Automatic Id Generation** check box selected.

32. In the **Enter Indexing Policy** box, type the following code to specify the following policy. This policy prevents the fields in the **innings** subdocument from being indexed (it is highly unlikely that you would want to search this subdocument for information about a specific delivery, rather you are more likely to process this data sequentially, so an index is superfluous):

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
                        "kind": "Hash",
                        "dataType": "String",
                        "precision": 3
                    }
                ]
            }
        ],
        "excludedPaths": [
            {
                "path": "/innings/*"
            },
            {
                "path": "/\"_etag\"/?"
            }
        ]
    }
    ```

33. On the **Target Information** page, click **Next**.

34. On the **Advanced** page, click **Next**.

35. On the **Summary** page, click **Import**. Notice that the import proceeds far more quickly than before without the need to increase the **Throughput** for the database.

36. Wait for the operation to finish, and verify that it imports 3939 documents.

    > **Note:** If you decide later that you do require an index over the **innings** data, you can modify the index policy for the collection using the Azure portal.

#### Task 2: Create Collections with Different Partitioning Strategies

1. In DocumentDB Data Migration Tool, click **New Import**.

2. In the **New Import** dialog box, click **No** to reuse the settings from the previous run.

3. On the **Source Information** page, specify the JSON file **E:\\Demofiles\\Mod04\\Data\\MatchesData.json**, and then click **Next**.

4. On the **Target Information** page, ensure the connection string is the same as before.

5. In the **Collection** box, type **MatchesPartitionedByHomeTeam**.

6. In the **Partition Key** box, type **/info/hometeam**.

7. Leave the remaining fields at their current settings, and then click **Next**.

8. On the **Advanced** page, click **Next**.

9. On the **Summary** page, click **Import**. Notice that the import proceeds far more quickly than before without the need to increase the **Throughput** for the database.

10. Wait for the operation to finish.

11. Repeat steps 1 to 10 to import the data into a **Collection** named **MatchesPartitionedByAwayTeam**, and set the **Partition Key** to **/info/awayteam**.

12. Repeat steps 1 to 10 to import the data into a **Collection** named **MatchesPartitionedByMatchType**, and set the **Partition Key** to **/info/match\_type**.

13. Repeat steps 1 to 10 to import the data into a **Collection** named **MatchesPartitionedByCity**, and set the **Partition Key** to **/info/city**.

14. Close the DocumentDB Data Migration Tool.

#### Task 3: Compare the Performance of Different Partitioning Strategies

1. On the Start menu, click **Visual Studio 2017**.

2. On the **File** menu, point to **Open**, and then click **Project/Solution**.

3. In the **Open Project** dialog box, navigate to the **E:\\Demofiles\\Mod04\\CricketQuery**, click **CricketQuery.sln**, and then click **Open**.

4. In Solution Explorer, click **Queries.cs**.

5. On the **Queries.cs** tab, examine the contents of the file.
  
6. The **Queries** class contains a number of methods that implement the queries specified at the start of this demonstration. Notice that the **queryOptions** field defined near the start of the class specifies the **FeedOptions** that are used by all of the queries; these options allow cross partition queries, scanning for non-indexed data, parallelization of partitioned queries, and cause queries to return query metrics:

    ```Csharp
    private static FeedOptions queryOptions = new FeedOptions { MaxItemCount = -1, EnableCrossPartitionQuery = true, MaxDegreeOfParallelism = -1, PopulateQueryMetrics = true, EnableScanInQuery = true };
    ```

7. In Solution Explorer, click **Program.cs**.

8. On the **Program.cs** tab, scroll down to the **Worker** class. The **DoWork** method runs each of the queries using each of the collections in turn, and calls the **Process** method to handle the results. The queries are run twice over each collection, to reduce the effects of caching by Cosmos DB (the first execution might have to retrieve data from disk storage, whereas the second run could use cached data). The **Process** method iterates through the data returned by a query and displays the performance information. This performance information is either the elapsed time taken to run the query (if the **mode** parameter is ‘2’), or the detailed query metrics (if the **mode** parameter is ‘1’). The boolean **print** parameter specifies whether the contents of the documents should be output, for debugging purposes.

9. In Solution Explorer, click **App.config**.

10. On the **App.config** tab, enter the values for the following settings:
  
    - **EndpointUrl**. Replace **\~URI\~** with the **URI** you noted earlier from the **20777a-sql-\<*your name\>-\<the day*\>** Cosmos DB account.

    - **PrimaryKey**. Replace **\~PRIMARY KEY\~** with the **PRIMARY KEY** you noted earlier from the**20777a-sql-\<*your name\>-\<the day*\>** Cosmos DB account.

    - **Database**. Leave this set to **Cricket**.

    - **Collections**. This is the set of collections you created in the database. Leave this set to the default list.

    - **NumWorkers**. Leave this set to **1**.

    - **TraceFolder**. The output of the **Process** method is written to files in this folder. Leave it set to **E:\\Demofiles\\Mod04\\**.

    - **PrintMode**. This setting specified whether the **Process** method should output each document retrieved. We are only interested in the metrics, so leave this set to **false**.

11. On the **Build** menu, click **Build Solution**, and wait until the build has succeeded.

12. On the **Debug** menu, click **Start Debugging**.

13. At the **Which mode?** prompt, type **2**, and wait for the app to finish.

14. In File Explorer, navigate to **E:\\Demofiles\\Mod04**, and then double-click **Matches.txt**. This file contains a copy of the elapsed timings for the queries using the **Matches** non-partitioned and fully indexed collection. It should look similar to the following. **Ignore the first elapsed time for each query**. The second time is a base benchmark you can use for comparison with the queries run using the other collections:

    ```Text
    Performance stats using collection Matches
    =====================================================================


    Query: Find T20 games for England at home, Elapsed Time: 710 ms
    Query: Find T20 games for England at home, Elapsed Time: 56 ms
    Query: Find T20 games for England away, Elapsed Time: 49 ms
    Query: Find T20 games for England away, Elapsed Time: 50 ms
    Query: Find ODIs played in England between England and Australia, Elapsed Time: 66 ms
    Query: Find ODIs played in England between England and Australia, Elapsed Time: 52 ms
    Query: Find all games played in Nottingham, Elapsed Time: 78 ms
    Query: Find all games played in Nottingham, Elapsed Time: 54 ms
    Query: Find all T20 games, Elapsed Time: 400 ms
    Query: Find all T20 games, Elapsed Time: 360 ms
    ```

15. In File Explorer, double-click **MatchesInningsNotIndexed.txt**. This file contains the statistics for the queries run using the non-partitioned collection without the **innings** data indexed. The timings should be similar to those of the fully indexed partition; none of the queries actually retrieve **innings** data:

    ```Text
    Performance stats using collection MatchesInningsNotIndexed
    =====================================================================


    Query: Find T20 games for England at home, Elapsed Time: 83 ms
    Query: Find T20 games for England at home, Elapsed Time: 57 ms
    Query: Find T20 games for England away, Elapsed Time: 49 ms
    Query: Find T20 games for England away, Elapsed Time: 48 ms
    Query: Find ODIs played in England between England and Australia, Elapsed Time: 52 ms
    Query: Find ODIs played in England between England and Australia, Elapsed Time: 105 ms
    Query: Find all games played in Nottingham, Elapsed Time: 52 ms
    Query: Find all games played in Nottingham, Elapsed Time: 75 ms
    Query: Find all T20 games, Elapsed Time: 425 ms
    Query: Find all T20 games, Elapsed Time: 347 ms
    ```

16. In File Explorer, double-click **MatchesPartitionedByHomeTeam.txt**. This file contains the statistics for the queries run using the collection partitioned by the home team. Note that the query that searches for data for the away team takes significantly longer than searching by the home team. Also, the query that searches for games played between two teams can also take advantage of the partitioning structure; one of the teams is the home team:

    ```Text
    Performance stats using collection MatchesPartitionedByHomeTeam
    =====================================================================

    Query: Find T20 games for England at home, Elapsed Time: 72 ms
    Query: Find T20 games for England at home, Elapsed Time: 59 ms
    Query: Find T20 games for England away, Elapsed Time: 321 ms
    Query: Find T20 games for England away, Elapsed Time: 113 ms
    Query: Find ODIs played in England between England and Australia, Elapsed Time: 51 ms
    Query: Find ODIs played in England between England and Australia, Elapsed Time: 54 ms
    Query: Find all games played in Nottingham, Elapsed Time: 92 ms
    Query: Find all games played in Nottingham, Elapsed Time: 126 ms
    Query: Find all T20 games, Elapsed Time: 446 ms
    Query: Find all T20 games, Elapsed Time: 490 ms
    ```

17. In File Explorer, double-click **MatchesPartitionedByAwayTeam.txt**. This file contains the statistics for the queries run using the collection partitioned by the away team. This time, the query that fetches data using the away team runs faster than the query that specifies the home team. Again, the query that searches for games played between two teams can take advantage of the partitioning structure as one of the teams is the away team:

    ```Text
    Performance stats using collection MatchesPartitionedByAwayTeam
    =====================================================================


    Query: Find T20 games for England at home, Elapsed Time: 123 ms
    Query: Find T20 games for England at home, Elapsed Time: 125 ms
    Query: Find T20 games for England away, Elapsed Time: 86 ms
    Query: Find T20 games for England away, Elapsed Time: 87 ms
    Query: Find ODIs played in England between England and Australia, Elapsed Time: 50 ms
    Query: Find ODIs played in England between England and Australia, Elapsed Time: 50 ms
    Query: Find all games played in Nottingham, Elapsed Time: 84 ms
    Query: Find all games played in Nottingham, Elapsed Time: 88 ms
    Query: Find all T20 games, Elapsed Time: 527 ms
    Query: Find all T20 games, Elapsed Time: 594 ms
    ```

18. In File Explorer, double-click **MatchesPartitionedByMatchType.txt**. This file contains the statistics for the queries run using the collection partitioned by the match type. The performance of the query that finds matches by city does not appear to have improved much. The reason for this is that there are only three match types (T20, ODI, and Test). Partitioning across a field that has a small number of distinct values is not as beneficial as partitioning across a field that has a large number of values:

    ```Text
    Performance stats using collection MatchesPartitionedByMatchType
    =====================================================================


    Query: Find T20 games for England at home, Elapsed Time: 73 ms
    Query: Find T20 games for England at home, Elapsed Time: 58 ms
    Query: Find T20 games for England away, Elapsed Time: 99 ms
    Query: Find T20 games for England away, Elapsed Time: 48 ms
    Query: Find ODIs played in England between England and Australia, Elapsed Time: 52 ms
    Query: Find ODIs played in England between England and Australia, Elapsed Time: 51 ms
    Query: Find all games played in Nottingham, Elapsed Time: 163 ms
    Query: Find all games played in Nottingham, Elapsed Time: 148 ms
    Query: Find all T20 games, Elapsed Time: 457 ms
    Query: Find all T20 games, Elapsed Time: 475 ms
    ```
19. Close all open instances of Notepad.

20. In File Explorer, delete all the text files in the **E:\\Demofiles\\Mod04** folder.

21. In Visual Studio 2017, on the **Debug** menu, click **Start Debugging**.

22. At the **Which mode?** prompt, type **1**, and wait for the app to finish. This mode generates comprehensive query metrics rather than the elapsed timings gathered previously. The elapsed times were a rather crude measure that includes many variables, such as the time taken to pass data across the network, and the performance of the client. They are useful as a guide, but the detailed query metrics provide a better view of how a query is run by Cosmos DB.

23. In File Explorer, double-click **Matches.txt**. This file should now contain the detailed metrics for the queries using the **Matches** non-partitioned and fully indexed collection. It should look similar to the following. As before, you can ignore the first set of stats for each query and use the second set as the base benchmark for comparison purposes. The key figures to look at are the **Request Charge** and the **Run Time (ms)** value in the **Scheduling Metrics** table:

    ```Text
    Performance stats using collection Matches
    =====================================================================


    Query: Find T20 games for England at home
    ---------------------------------------------------------------

    [0, Retrieved Document Count                 :              87
    Retrieved Document Size                  :       2,763,363 bytes
    Output Document Count                    :              87
    Output Document Size                     :               0 bytes
    Index Utilization                        :          100.00 %
    Total Query Execution Time               :           12.31 milliseconds
      Query Preparation Times
        Query Compilation Time               :            0.12 milliseconds
        Logical Plan Build Time              :            0.04 milliseconds
        Physical Plan Build Time             :            0.06 milliseconds
        Query Optimization Time              :            0.01 milliseconds
      Index Lookup Time                      :            0.15 milliseconds
      Document Load Time                     :           10.67 milliseconds
      Runtime Execution Times
        Query Engine Execution Time          :            0.85 milliseconds
        System Function Execution Time       :            0.00 milliseconds
        User-defined Function Execution Time :            0.00 milliseconds
      Document Write Time                    :            0.16 milliseconds
      Client Side Metrics
        Retry Count                          :               0
        Request Charge                       :           44.12 RUs

      Partition Execution Timeline
      ┌────────────┬────────────────┬───────────────┬───────────────────┬───────────┐
      │Partition Id│Start Time (UTC)│End Time (UTC) │Number of Documents│Retry Count│
      ├────────────┼────────────────┼───────────────┼───────────────────┼───────────┤
      │           0│ 01:27:57.470579│01:27:57.618499│                 87│          0│
      └────────────┴────────────────┴───────────────┴───────────────────┴───────────┘

      Scheduling Metrics
      ┌────────────┬────────────────────┬────────────────────┬────────────────────┬────────────────────┬─────────────────────┐
      │Partition Id│Response Time (ms)  │Run Time (ms)       │Wait Time (ms)      │Turnaround Time (ms)│Number of Preemptions│
      ├────────────┼────────────────────┼────────────────────┼────────────────────┼────────────────────┼─────────────────────┤
      │           0│               15.10│              147.51│               17.03│              164.54│                    1│
      └────────────┴────────────────────┴────────────────────┴────────────────────┴────────────────────┴─────────────────────┘
    ]


    Query: Find T20 games for England at home
    ---------------------------------------------------------------

    [0, Retrieved Document Count                 :              87
    Retrieved Document Size                  :       2,763,363 bytes
    Output Document Count                    :              87
    Output Document Size                     :               0 bytes
    Index Utilization                        :          100.00 %
    Total Query Execution Time               :           12.07 milliseconds
      Query Preparation Times
        Query Compilation Time               :            0.12 milliseconds
        Logical Plan Build Time              :            0.04 milliseconds
        Physical Plan Build Time             :            0.06 milliseconds
        Query Optimization Time              :            0.01 milliseconds
      Index Lookup Time                      :            0.19 milliseconds
      Document Load Time                     :           10.31 milliseconds
      Runtime Execution Times
        Query Engine Execution Time          :            0.98 milliseconds
        System Function Execution Time       :            0.00 milliseconds
        User-defined Function Execution Time :            0.00 milliseconds
      Document Write Time                    :            0.14 milliseconds
      Client Side Metrics
        Retry Count                          :               0
        Request Charge                       :           44.12 RUs

      Partition Execution Timeline
      ┌────────────┬────────────────┬───────────────┬───────────────────┬───────────┐
      │Partition Id│Start Time (UTC)│End Time (UTC) │Number of Documents│Retry Count│
      ├────────────┼────────────────┼───────────────┼───────────────────┼───────────┤
      │           0│ 01:27:57.688399│01:27:57.773990│                 87│          0│
      └────────────┴────────────────┴───────────────┴───────────────────┴───────────┘

      Scheduling Metrics
      ┌────────────┬────────────────────┬────────────────────┬────────────────────┬────────────────────┬─────────────────────┐
      │Partition Id│Response Time (ms)  │Run Time (ms)       │Wait Time (ms)      │Turnaround Time (ms)│Number of Preemptions│
      ├────────────┼────────────────────┼────────────────────┼────────────────────┼────────────────────┼─────────────────────┤
      │           0│                0.02│               85.59│                0.04│               85.63│                    1│
      └────────────┴────────────────────┴────────────────────┴────────────────────┴────────────────────┴─────────────────────┘
    ]


    Query: Find T20 games for England away
    ---------------------------------------------------------------

    [0, Retrieved Document Count                 :              40
    Retrieved Document Size                  :       1,278,504 bytes
    Output Document Count                    :              40
    Output Document Size                     :               0 bytes
    Index Utilization                        :          100.00 %
    Total Query Execution Time               :            5.84 milliseconds
      Query Preparation Times
        Query Compilation Time               :            0.16 milliseconds
        Logical Plan Build Time              :            0.04 milliseconds
        Physical Plan Build Time             :            0.06 milliseconds
        Query Optimization Time              :            0.01 milliseconds
      Index Lookup Time                      :            0.29 milliseconds
      Document Load Time                     :            4.53 milliseconds
      Runtime Execution Times
        Query Engine Execution Time          :            0.36 milliseconds
        System Function Execution Time       :            0.00 milliseconds
        User-defined Function Execution Time :            0.00 milliseconds
      Document Write Time                    :            0.05 milliseconds
      Client Side Metrics
        Retry Count                          :               0
        Request Charge                       :           22.07 RUs

      Partition Execution Timeline
      ┌────────────┬────────────────┬───────────────┬───────────────────┬───────────┐
      │Partition Id│Start Time (UTC)│End Time (UTC) │Number of Documents│Retry Count│
      ├────────────┼────────────────┼───────────────┼───────────────────┼───────────┤
      │           0│ 01:27:57.798422│01:27:57.887827│                 40│          0│
      └────────────┴────────────────┴───────────────┴───────────────────┴───────────┘

      Scheduling Metrics
      ┌────────────┬────────────────────┬────────────────────┬────────────────────┬────────────────────┬─────────────────────┐
      │Partition Id│Response Time (ms)  │Run Time (ms)       │Wait Time (ms)      │Turnaround Time (ms)│Number of Preemptions│
      ├────────────┼────────────────────┼────────────────────┼────────────────────┼────────────────────┼─────────────────────┤
      │           0│                0.02│               89.40│                0.05│               89.45│                    1│
      └────────────┴────────────────────┴────────────────────┴────────────────────┴────────────────────┴─────────────────────┘
    ]


    Query: Find T20 games for England away
    ---------------------------------------------------------------

    [0, Retrieved Document Count                 :              40
    Retrieved Document Size                  :       1,278,504 bytes
    Output Document Count                    :              40
    Output Document Size                     :               0 bytes
    Index Utilization                        :          100.00 %
    Total Query Execution Time               :            5.67 milliseconds
      Query Preparation Times
        Query Compilation Time               :            0.12 milliseconds
        Logical Plan Build Time              :            0.04 milliseconds
        Physical Plan Build Time             :            0.12 milliseconds
        Query Optimization Time              :            0.01 milliseconds
      Index Lookup Time                      :            0.15 milliseconds
      Document Load Time                     :            4.61 milliseconds
      Runtime Execution Times
        Query Engine Execution Time          :            0.34 milliseconds
        System Function Execution Time       :            0.00 milliseconds
        User-defined Function Execution Time :            0.00 milliseconds
      Document Write Time                    :            0.05 milliseconds
      Client Side Metrics
        Retry Count                          :               0
        Request Charge                       :           22.07 RUs

      Partition Execution Timeline
      ┌────────────┬────────────────┬───────────────┬───────────────────┬───────────┐
      │Partition Id│Start Time (UTC)│End Time (UTC) │Number of Documents│Retry Count│
      ├────────────┼────────────────┼───────────────┼───────────────────┼───────────┤
      │           0│ 01:27:57.977398│01:27:58.031464│                 40│          0│
      └────────────┴────────────────┴───────────────┴───────────────────┴───────────┘

      Scheduling Metrics
      ┌────────────┬────────────────────┬────────────────────┬────────────────────┬────────────────────┬─────────────────────┐
      │Partition Id│Response Time (ms)  │Run Time (ms)       │Wait Time (ms)      │Turnaround Time (ms)│Number of Preemptions│
      ├────────────┼────────────────────┼────────────────────┼────────────────────┼────────────────────┼─────────────────────┤
      │           0│                0.02│               54.06│                0.04│               54.10│                    1│
      └────────────┴────────────────────┴────────────────────┴────────────────────┴────────────────────┴─────────────────────┘
    ]


    Query: Find ODIs played in England between England and Australia
    ---------------------------------------------------------------

    [0, Retrieved Document Count                 :              35
    Retrieved Document Size                  :       2,558,870 bytes
    Output Document Count                    :              35
    Output Document Size                     :               0 bytes
    Index Utilization                        :          100.00 %
    Total Query Execution Time               :            8.41 milliseconds
      Query Preparation Times
        Query Compilation Time               :            0.16 milliseconds
        Logical Plan Build Time              :            0.04 milliseconds
        Physical Plan Build Time             :            0.12 milliseconds
        Query Optimization Time              :            0.01 milliseconds
      Index Lookup Time                      :            0.20 milliseconds
      Document Load Time                     :            7.15 milliseconds
      Runtime Execution Times
        Query Engine Execution Time          :            0.36 milliseconds
        System Function Execution Time       :            0.00 milliseconds
        User-defined Function Execution Time :            0.00 milliseconds
      Document Write Time                    :            0.06 milliseconds
      Client Side Metrics
        Retry Count                          :               0
        Request Charge                       :           30.86 RUs

      Partition Execution Timeline
      ┌────────────┬────────────────┬───────────────┬───────────────────┬───────────┐
      │Partition Id│Start Time (UTC)│End Time (UTC) │Number of Documents│Retry Count│
      ├────────────┼────────────────┼───────────────┼───────────────────┼───────────┤
      │           0│ 01:27:58.088415│01:27:58.138720│                 35│          0│
      └────────────┴────────────────┴───────────────┴───────────────────┴───────────┘

      Scheduling Metrics
      ┌────────────┬────────────────────┬────────────────────┬────────────────────┬────────────────────┬─────────────────────┐
      │Partition Id│Response Time (ms)  │Run Time (ms)       │Wait Time (ms)      │Turnaround Time (ms)│Number of Preemptions│
      ├────────────┼────────────────────┼────────────────────┼────────────────────┼────────────────────┼─────────────────────┤
      │           0│                0.03│               50.30│                0.06│               50.36│                    1│
      └────────────┴────────────────────┴────────────────────┴────────────────────┴────────────────────┴─────────────────────┘
    ]


    Query: Find ODIs played in England between England and Australia
    ---------------------------------------------------------------

    [0, Retrieved Document Count                 :              35
    Retrieved Document Size                  :       2,558,870 bytes
    Output Document Count                    :              35
    Output Document Size                     :               0 bytes
    Index Utilization                        :          100.00 %
    Total Query Execution Time               :            8.62 milliseconds
      Query Preparation Times
        Query Compilation Time               :            0.11 milliseconds
        Logical Plan Build Time              :            0.09 milliseconds
        Physical Plan Build Time             :            0.09 milliseconds
        Query Optimization Time              :            0.01 milliseconds
      Index Lookup Time                      :            0.17 milliseconds
      Document Load Time                     :            7.40 milliseconds
      Runtime Execution Times
        Query Engine Execution Time          :            0.42 milliseconds
        System Function Execution Time       :            0.00 milliseconds
        User-defined Function Execution Time :            0.00 milliseconds
      Document Write Time                    :            0.05 milliseconds
      Client Side Metrics
        Retry Count                          :               0
        Request Charge                       :           30.86 RUs

      Partition Execution Timeline
      ┌────────────┬────────────────┬───────────────┬───────────────────┬───────────┐
      │Partition Id│Start Time (UTC)│End Time (UTC) │Number of Documents│Retry Count│
      ├────────────┼────────────────┼───────────────┼───────────────────┼───────────┤
      │           0│ 01:27:58.198436│01:27:58.250340│                 35│          0│
      └────────────┴────────────────┴───────────────┴───────────────────┴───────────┘

      Scheduling Metrics
      ┌────────────┬────────────────────┬────────────────────┬────────────────────┬────────────────────┬─────────────────────┐
      │Partition Id│Response Time (ms)  │Run Time (ms)       │Wait Time (ms)      │Turnaround Time (ms)│Number of Preemptions│
      ├────────────┼────────────────────┼────────────────────┼────────────────────┼────────────────────┼─────────────────────┤
      │           0│                0.04│               51.90│                0.07│               51.97│                    1│
      └────────────┴────────────────────┴────────────────────┴────────────────────┴────────────────────┴─────────────────────┘
    ]


    Query: Find all games played in Nottingham
    ---------------------------------------------------------------

    [0, Retrieved Document Count                 :              38
    Retrieved Document Size                  :       3,230,737 bytes
    Output Document Count                    :              38
    Output Document Size                     :               0 bytes
    Index Utilization                        :          100.00 %
    Total Query Execution Time               :           13.76 milliseconds
      Query Preparation Times
        Query Compilation Time               :            0.08 milliseconds
        Logical Plan Build Time              :            0.02 milliseconds
        Physical Plan Build Time             :            0.04 milliseconds
        Query Optimization Time              :            0.00 milliseconds
      Index Lookup Time                      :            0.06 milliseconds
      Document Load Time                     :           12.67 milliseconds
      Runtime Execution Times
        Query Engine Execution Time          :            0.63 milliseconds
        System Function Execution Time       :            0.00 milliseconds
        User-defined Function Execution Time :            0.00 milliseconds
      Document Write Time                    :            0.05 milliseconds
      Client Side Metrics
        Retry Count                          :               0
        Request Charge                       :           35.35 RUs

      Partition Execution Timeline
      ┌────────────┬────────────────┬───────────────┬───────────────────┬───────────┐
      │Partition Id│Start Time (UTC)│End Time (UTC) │Number of Documents│Retry Count│
      ├────────────┼────────────────┼───────────────┼───────────────────┼───────────┤
      │           0│ 01:27:58.301464│01:27:58.358922│                 38│          0│
      └────────────┴────────────────┴───────────────┴───────────────────┴───────────┘

      Scheduling Metrics
      ┌────────────┬────────────────────┬────────────────────┬────────────────────┬────────────────────┬─────────────────────┐
      │Partition Id│Response Time (ms)  │Run Time (ms)       │Wait Time (ms)      │Turnaround Time (ms)│Number of Preemptions│
      ├────────────┼────────────────────┼────────────────────┼────────────────────┼────────────────────┼─────────────────────┤
      │           0│                0.03│               57.45│                0.06│               57.51│                    1│
      └────────────┴────────────────────┴────────────────────┴────────────────────┴────────────────────┴─────────────────────┘
    ]


    Query: Find all games played in Nottingham
    ---------------------------------------------------------------

    [0, Retrieved Document Count                 :              38
    Retrieved Document Size                  :       3,230,737 bytes
    Output Document Count                    :              38
    Output Document Size                     :               0 bytes
    Index Utilization                        :          100.00 %
    Total Query Execution Time               :           14.13 milliseconds
      Query Preparation Times
        Query Compilation Time               :            0.18 milliseconds
        Logical Plan Build Time              :            0.03 milliseconds
        Physical Plan Build Time             :            0.05 milliseconds
        Query Optimization Time              :            0.00 milliseconds
      Index Lookup Time                      :            0.13 milliseconds
      Document Load Time                     :           12.49 milliseconds
      Runtime Execution Times
        Query Engine Execution Time          :            0.89 milliseconds
        System Function Execution Time       :            0.00 milliseconds
        User-defined Function Execution Time :            0.00 milliseconds
      Document Write Time                    :            0.06 milliseconds
      Client Side Metrics
        Retry Count                          :               0
        Request Charge                       :           35.35 RUs

      Partition Execution Timeline
      ┌────────────┬────────────────┬───────────────┬───────────────────┬───────────┐
      │Partition Id│Start Time (UTC)│End Time (UTC) │Number of Documents│Retry Count│
      ├────────────┼────────────────┼───────────────┼───────────────────┼───────────┤
      │           0│ 01:27:58.423408│01:27:58.519234│                 38│          0│
      └────────────┴────────────────┴───────────────┴───────────────────┴───────────┘

      Scheduling Metrics
      ┌────────────┬────────────────────┬────────────────────┬────────────────────┬────────────────────┬─────────────────────┐
      │Partition Id│Response Time (ms)  │Run Time (ms)       │Wait Time (ms)      │Turnaround Time (ms)│Number of Preemptions│
      ├────────────┼────────────────────┼────────────────────┼────────────────────┼────────────────────┼─────────────────────┤
      │           0│                0.02│               95.83│                0.03│               95.85│                    1│
      └────────────┴────────────────────┴────────────────────┴────────────────────┴────────────────────┴─────────────────────┘
    ]


    Query: Find all T20 games
    ---------------------------------------------------------------

    [0, Retrieved Document Count                 :           1,661
    Retrieved Document Size                  :      53,600,914 bytes
    Output Document Count                    :           1,661
    Output Document Size                     :               0 bytes
    Index Utilization                        :          100.00 %
    Total Query Execution Time               :          184.17 milliseconds
      Query Preparation Times
        Query Compilation Time               :            0.10 milliseconds
        Logical Plan Build Time              :            0.03 milliseconds
        Physical Plan Build Time             :            0.08 milliseconds
        Query Optimization Time              :            0.00 milliseconds
      Index Lookup Time                      :            0.10 milliseconds
      Document Load Time                     :          170.40 milliseconds
      Runtime Execution Times
        Query Engine Execution Time          :           11.04 milliseconds
        System Function Execution Time       :            0.00 milliseconds
        User-defined Function Execution Time :            0.00 milliseconds
      Document Write Time                    :            1.94 milliseconds
      Client Side Metrics
        Retry Count                          :               0
        Request Charge                       :          792.47 RUs

      Partition Execution Timeline
      ┌────────────┬────────────────┬───────────────┬───────────────────┬───────────┐
      │Partition Id│Start Time (UTC)│End Time (UTC) │Number of Documents│Retry Count│
      ├────────────┼────────────────┼───────────────┼───────────────────┼───────────┤
      │           0│ 01:27:58.528383│01:27:59.267309│               1661│          0│
      └────────────┴────────────────┴───────────────┴───────────────────┴───────────┘

      Scheduling Metrics
      ┌────────────┬────────────────────┬────────────────────┬────────────────────┬────────────────────┬─────────────────────┐
      │Partition Id│Response Time (ms)  │Run Time (ms)       │Wait Time (ms)      │Turnaround Time (ms)│Number of Preemptions│
      ├────────────┼────────────────────┼────────────────────┼────────────────────┼────────────────────┼─────────────────────┤
      │           0│                0.01│              738.92│                0.02│              738.94│                    1│
      └────────────┴────────────────────┴────────────────────┴────────────────────┴────────────────────┴─────────────────────┘
    ]


    Query: Find all T20 games
    ---------------------------------------------------------------

    [0, Retrieved Document Count                 :           1,661
    Retrieved Document Size                  :      53,600,914 bytes
    Output Document Count                    :           1,661
    Output Document Size                     :               0 bytes
    Index Utilization                        :          100.00 %
    Total Query Execution Time               :          185.51 milliseconds
      Query Preparation Times
        Query Compilation Time               :            0.10 milliseconds
        Logical Plan Build Time              :            0.03 milliseconds
        Physical Plan Build Time             :            0.04 milliseconds
        Query Optimization Time              :            0.00 milliseconds
      Index Lookup Time                      :            0.07 milliseconds
      Document Load Time                     :          170.14 milliseconds
      Runtime Execution Times
        Query Engine Execution Time          :           12.76 milliseconds
        System Function Execution Time       :            0.00 milliseconds
        User-defined Function Execution Time :            0.00 milliseconds
      Document Write Time                    :            1.89 milliseconds
      Client Side Metrics
        Retry Count                          :               0
        Request Charge                       :          792.47 RUs

      Partition Execution Timeline
      ┌────────────┬────────────────┬───────────────┬───────────────────┬───────────┐
      │Partition Id│Start Time (UTC)│End Time (UTC) │Number of Documents│Retry Count│
      ├────────────┼────────────────┼───────────────┼───────────────────┼───────────┤
      │           0│ 01:27:59.284387│01:28:00.926713│               1661│          1│
      └────────────┴────────────────┴───────────────┴───────────────────┴───────────┘

      Scheduling Metrics
      ┌────────────┬────────────────────┬────────────────────┬────────────────────┬────────────────────┬─────────────────────┐
      │Partition Id│Response Time (ms)  │Run Time (ms)       │Wait Time (ms)      │Turnaround Time (ms)│Number of Preemptions│
      ├────────────┼────────────────────┼────────────────────┼────────────────────┼────────────────────┼─────────────────────┤
      │           0│                0.01│             1642.32│                0.02│             1642.35│                    1│
      └────────────┴────────────────────┴────────────────────┴────────────────────┴────────────────────┴─────────────────────┘
    ]
    ```
24. In File Explorer, double-click **MatchesPartitionedByHomeTeam.txt**. Compare the values of the **Run Time (ms)** for each query of those in the **Matches.txt** file. For the queries **Find T20 games for England at home** and **Find ODIs played in England between England and Australia**, the timings should be faster due to the way in which the data is partitioned

25. In File Explorer, double-click **MatchesPartitionedByAwayTeam.txt**. The partitioning should have decreased the run time required for the query **Find T20 games for England away**, although the queries **Find T20 games for England at home** and **Find ODIs played in England between England and Australia** should have slowed down.

    > **Note:** Remember that when you specify a partition key, you define a **logical** partition. Internally, Cosmos DB can choose to store several logical partitions in the same **physical** partition, depending on the size and number of logical partitions. The set of logical partitions in a physical partition is referred to as the **partition key range**. When the data was partitioned by home team or away team, it was all stored in the same physical partition. As these logical partitions grow, and new logical partitions are added, then Cosmos DB may choose to add more physical partitions and reorganize the data. There are a larger number of cities than international cricketing countries, so partitioning by city created a larger number of logical partitions, and Cosmos DB decided to distribute these logical partitions across multiple physical partitions. Partitioning by city improves the response time of the query that finds all matches played in Nottingham.

26. In File Explorer, double-click **MatchesPartitionedByMatchType.txt**. In this case, the data is all stored in the same physical partition. Look at the metrics for the query **Find all T20 games**. As described earlier, partitioning by match type does not offer much advantage in performance due to the low number of distinct values.

27. Close all open instances of Notepad.

28. Close Visual Studio 2017.

#### Task 4: Examine the Physical to Logical Mapping of Partitions

1. In Internet Explorer, on the **20777a-sql-\<*your name\>-\<the day*\>** blade, under **Monitoring** click **Metrics**.

2. On the **Metrics** blade, on the **Storage** tab, in the **Database(s)** drop-down list, click **Cricket**.

3. In the **Collection(s)** drop-down list, click **MatchesPartitionedByHomeTeam**.

4. In the **Data + Index storage consumed per partition key range** pane, click the bar for the only partition that is displayed. This partition should be named **partition 0**.

5. In the **Showing partition keys for partition 0** pane, you should see the top three partition keys defining the logical partitions in partition 0. These should be labelled **England**, **Australia**, and **India**.

## Lesson 2: Monitoring the performance of a database

### Demo 1: Monitoring a document database

#### Preparation

Before starting this demo:

1. Complete demo 2 to create and populate the **Cricket** database and the various collections required for this demo.

2. In Internet Explorer, on the **20777a-sql-\<*your name\>-\<the day*\>** blade, under **Settings**, click **Replicate data globally**.

3. If the **Message from webpage** dialog box appears, click **OK**.

4. On the **Replicate data globally** blade, under **READ REGIONS**, click the trash can icon to delete the failover, and then click **Save**. This can take upto 5 minutes to save.

5. On the **20777a-sql-\<*your name\>-\<the day*\>** blade, under **Settings**, click **Default consistency**. 

6. On the **Default consistency** blade, click **STRONG**, and then click **Save**.

7. On the **20777a-sql-\<*your name\>-\<the day*\>** blade, under **Settings**, click **Keys**.

8. Make a note of the **PRIMARY CONNECTION STRING** value.

#### Task 1: Examine How Consistency Affects Throughput

1. On the Start menu, click **Visual Studio 2017**.

2. On the **File** menu, point to **Open**, and then click **Project/Solution**.

3. In the **Open Project** dialog box, navigate to the **E:\\Demofiles\\Mod04\\CricketQueryPerformanceTester** folder, click **CricketQuery.sln,** and then click **Open**. This app is a modified version of the app used in demo 2. This version of the app starts instances of the **Worker** class at regular intervals (governed by an auto-repeating timer). The **Worker** performs one pass through the queries using the data held in a specified collection, gathers the execution metrics, and uses this information to assess the RU/s required to perform each query.

4. In Solution Explorer, click **Program.cs**.

5. On the **Program.cs** tab, in the **Program** class, examine the **RunWorker** method. This method starts instances of the **Worker** class. It is triggered by a timer. After the last instance has been started, the method allows up to 60 seconds for the workers to complete before exiting the app. The **numErrors** variable tracks the number of errors that have occurred across all the workers:

    ```Csharp
    private static void RunWorker(Object source, ElapsedEventArgs e)
    {
        if (++workerNum > maxWorkers)
        {
            timer.Enabled = false;
            Task.Delay(60000).Wait();
            Trace.WriteLine($"Error count {numErrors}");
            Environment.Exit(0);
        }
        else
        {
            Worker worker = new Worker(workerNum);
            worker.DoWork(printMode);
        }
    }
    ```

6. In the **Worker** class, examine the **DoWork** method. This method performs a set of queries and calls the **Process** method to handle the results for each query. As in the previous demo, each query is run twice, to counter the effects of caching by Cosmos DB etc. If an exception occurs, the details are output and the **numErrors** variable is incremented:

    ```CSharp
    internal void DoWork(bool printMode)
    {
        IDocumentQuery<dynamic> data = null;

        try
        {
            data = Queries.FindByHomeTeam("England", "T20");
            Process(data, printMode, $"Worker {this.workerNum}: Find T20 games for England at home");
            data = Queries.FindByHomeTeam("England", "T20");
            Process(data, printMode, $"Worker {this.workerNum}: Find T20 games for England at home");

            data = Queries.FindByAwayTeam("England", "T20");
            Process(data, printMode, $"Worker {this.workerNum}: Find T20 games for England away");
            data = Queries.FindByAwayTeam("England", "T20");
            Process(data, printMode, $"Worker {this.workerNum}: Find T20 games for England away");

            data = Queries.FindByTeams("England", "Australia", "ODI");
            Process(data, printMode, $"Worker {this.workerNum}: Find ODIs played in England between England and Australia");
            data = Queries.FindByTeams("England", "Australia", "ODI");
            Process(data, printMode, $"Worker {this.workerNum}: Find ODIs played in England between England and Australia");

            data = Queries.FindByCity("Nottingham");
            Process(data, printMode, $"Worker {this.workerNum}: Find all games played in Nottingham");
            data = Queries.FindByCity("Nottingham");
            Process(data, printMode, $"Worker {this.workerNum}: Find all games played in Nottingham");

            data = Queries.FindByMatchType("T20");
            Process(data, printMode, $"Worker {this.workerNum}: Find all T20 games");
            data = Queries.FindByMatchType("T20");
            Process(data, printMode, $"Worker {this.workerNum}: Find all T20 games");
        }

        catch (AggregateException e)
        {
            foreach (var ex in e.Flatten().InnerExceptions)
            {
                Trace.WriteLine($"{ex.Message}");
                if (ex.InnerException != null)
                {
                    Trace.WriteLine($"{ex.InnerException.Message}");
                }
            }
            Program.numErrors++;
        }
        catch (Exception ex)
        {
            Trace.WriteLine(ex.Message);
            Program.numErrors++;
        }

        this.outputStream.Close();
    }
    ```

7. Examine the **Process** method. This method iterates through the results of a query, retrieves the time taken to run the query together with the number of RUs required and other stats, and uses this information to calculate the throughput in RU/s. This figure is referred to as the **Burst Throughput** as it is only momentary, while the query is executed and the data retrieved. The **Process** method also calculates the client-side response time of the query; this will include the time taken to retry the query by the SQL API library if it fails:

    ```Csharp
    private void Process(IDocumentQuery<dynamic> data, bool print, string message)
    {
        var executionTimeMS = 0.0;
        var charge = 0.0;
        var numDocs = 0L;
        var docsSize = 0L;

        Stopwatch stopwatch = new Stopwatch();
        while (data.HasMoreResults)
        {
            stopwatch.Start();
            var records = data.ExecuteNextAsync().Result;
            stopwatch.Stop();
            if (print)
            {
                foreach (var record in records)
                {
                    Trace.WriteLine($"{record}\n");
                }
            }

            charge += records.RequestCharge;
            var metrics = records.QueryMetrics;

            foreach (var metric in metrics)
            {
                executionTimeMS += metric.Value.TotalTime.TotalMilliseconds;

                numDocs += metric.Value.RetrievedDocumentCount;
                docsSize += metric.Value.RetrievedDocumentSize;
            }
        }

        Trace.WriteLine($"Query: {message}, Execution Time: {executionTimeMS} ms, Response Time: {stopwatch.ElapsedMilliseconds} ms, Number of Docs: {numDocs}, Total Size: {docsSize}, Charge: {charge} RU, Burst Throughput: {charge/executionTimeMS*1000} RU/s");
        this.outputStream.WriteLine($"{executionTimeMS}\t\t{stopwatch.ElapsedMilliseconds}\t\t{numDocs}\t\t{docsSize}\t\t{charge}\t{charge / executionTimeMS * 1000}\t{message}");
    }
    ```

8. In Solution Explorer, click **App.config**.

9. On the **App.config** tab, enter the values for the following settings: 
    - **EndpointUrl**. Replace **\~URI\~** with the **URI** you noted earlier from the **20777a-sql-\<*your name\>-\<the day*\>** Cosmos DB account.

    - **PrimaryKey**. Replace **\~PRIMARY KEY\~** with the **PRIMARY KEY** you noted earlier from the**20777a-sql-\<*your name\>-\<the day*\>** Cosmos DB account.

    - **Database**. Leave this set to **Cricket**.

    - **Collection**. Set this to **Matches**, the non-partitioned collection.
  
    - **TimerInterval**. This is the interval (in ms) at which new **Worker** instances are created. Leave it set to **10**. This will create overlapping workloads that gradually increase the stress on the system

    - **MaxWorkers**. Leave this set to **25**.

    - **ConsistencyLevel**. This is the consistency level to be utilized by the queries in the workload. Set it to **Strong**.

    - **MaxQueryRetries**. This is the number of times the functions in the SQL API library will attempt to perform an operation (such as a query) until it succeeds. Set it to **10**.

    - **MaxQueryRetryWaitTime**. Leave this set to **30**.

    - **PrintMode**. This setting specified whether the **Process** method should output each document retrieved. We are only interested in the metrics, so leave this set to **false**.

10. On the **Build** menu, click **Build Solution**, and wait until the build has succeeded.

11. On the **Debug** menu, click **Start Debugging**. Remember that the app may pause for up to 60 seconds at the end to allow the workers to complete before terminating.

12. In File Explorer, navigate to **E:**\\**Demofiles\\Mod04**, and then double-click any of the **Matches-Strong-worker.txt** files. These files contains a copy of the elapsed timings for the queries using the **Matches** non-partitioned and fully indexed collection. It should look similar to the following. **Ignore the first elapsed time for each query** and focus on the second. The file should look similar to this (you might need to realign some of the tabbed data):

    ```Text
    Execution Time  Response Time   Number of Docs  Total Size  Charge  Burst Throughput    Query
    9.1             5020            87              2763363     88.24   9696.7032967033     Worker 6: Find T20 games for England at home
    9.33            1093            87              2763363     88.24   9457.66345123258    Worker 6: Find T20 games for England at home
    4.32            889             40              1278504     44.14   10217.5925925926    Worker 6: Find T20 games for England away
    5.59            1088            40              1278504     44.14   7896.24329159213    Worker 6: Find T20 games for England away
    7.9             1937            35              2558870     61.72   7812.6582278481     Worker 6: Find ODIs played in England between England and Australia
    7.8             1022            35              2558870     61.72   7912.82051282051    Worker 6: Find ODIs played in England between England and Australia
    13.74           961             38              3230737     70.7    5145.56040756914    Worker 6: Find all games played in Nottingham
    20.7399         1555            38              3230737     70.7    3408.88818171737    Worker 6: Find all games played in Nottingham
    190.77          5106            1661            53600914    792.85  4156.05179011375    Worker 6: Find all T20 games
    176.01          9801            1661            53600914    792.85  4504.57360377251    Worker 6: Find all T20 games
    ```
13. Observe the **Response Time**, **Charge**, and the **Burst Throughput** value for each query. The response time gives a measure of how long the user would have to wait for the results, and the charge illustrates the resources consumed by the query. The burst throughput shows how many RU/s were required while the query was run.

    > **Note:** The response time includes the time taken by any attempts that the client made to repeat a failed query. Naturally, the more contention that occurs between clients the more often queries fail, therefore the more attempts are required, and the greater the response time.

14. Close Notepad.

15. In Internet Explorer, on the **20777a-sql-\<*your name\>-\<the day*\>** blade, under **Monitoring**, click **Metrics**.

16. On the **Metrics** blade, on the **Throughput** tab, in the **Database(s)** drop-down list, click **Cricket**.

17. Ensure that the **1 hour** interval is selected.

18. Examine the **Number of requests exceeded capacity (aggregated over 1 minute interval)** graph. This graph shows how many requests failed due to **Request rate is large** Http 429 errors (you may need to wait for up to 5 minutes to see the data). This should exceed the number of errors reported by the app, possibly by an order or two of magnitude. This is because the app is configured to retry queries up to 10 times before reporting an exception. This graph gives a true measure of how many requests actually failed.

19. In the **Collection(s)** drop-down list, click **Matches**.

20. Examine the **Max consumed RU/s per partition key range** graph. In this graph, you should see that the throughput in RU/s consumed by the app frequently peaks above the provisioned level (1000). These peaks should reflect the figures captured by the statistics in the text file you examined using Notepad (the figures in Notepad might be higher if a peak occurs during the sampling interval used by the Azure metrics.)

21. In Visual Studio 2017, on the **App.config** tab, change the **ConsistencyLevel** to **Eventual**. Leave all other settings the same.

22. On the **Build** menu, click **Build Solution**, and wait until the build has succeeded.

23. On the **Debug** menu, click **Start Debugging**, and wait for it to complete.

24. In File Explorer, navigate **E:\\Demofiles\\Mod04**, double-click any of the **Matches-Eventual-worker.txt** files. It should resemble the following:

    ```Text
    Execution Time  Response Time   Number of Docs  Total Size  Charge  Burst Throughput    Query
    11.2            5025            87              2763363     44.12   3939.28571428571    Worker 1: Find T20 games for England at home
    9.35            1175            87              2763363     44.12   4718.71657754011    Worker 1: Find T20 games for England at home
    4.88            2148            40              1278504     22.07   4522.54098360656    Worker 1: Find T20 games for England away
    3.88            982             40              1278504     22.07   5688.14432989691    Worker 1: Find T20 games for England away
    7.54            926             35              2558870     30.86   4092.83819628647    Worker 1: Find ODIs played in England between England and Australia
    9.05            1518            35              2558870     30.86   3409.94475138122    Worker 1: Find ODIs played in England between England and Australia
    12.63           509             38              3230737     35.35   2798.89152810768    Worker 1: Find all games played in Nottingham
    11.66           1948            38              3230737     35.35   3031.73241852487    Worker 1: Find all games played in Nottingham
    172.86          4024            1661            53600914    792.47  4584.46141386093    Worker 1: Find all T20 games
    171.71          5040            1661            53600914    792.47  4615.16510395434    Worker 1: Find all T20 games
    ```

    - The **Response Times** should have reduced slightly from before, but the **Charge** and **Burst Throughput** figures should be significantly lower; the charge is likely to be half that of the run that used strong consistency.

25. Close Notepad.

26. In Internet Explorer, on the **20777a-sql-\<*your name\>-\<the day*\>** blade, click **Data Explorer**.

27. If the **Message from webpage** dialog box appears, click **OK**.

28. In the **SQL API** pane, expand **Cricket**, expand **Matches**, and then click **Scale & Settings**.

29. On the **Scale & Settings** tab, increase the **Throughput** for the collection from **1000** to **5000**, and then click **Save**.

30. In Visual Studio, on the **App.config** tab, change the **ConsistencyLevel** to **Strong**. Leave all other settings the same.

31. On the **Build** menu, click **Build Solution**, and wait until the build has succeeded.

32. On the **Debug** menu, click **Start Debugging**, and wait for it to complete. Note that the app overwrites the **Matches-Strong-worker.txt** files created previously. The number of errors that occurred should be lower than the previous run that used strong consistency. This is because more resources are now available to run each query, and contention for these resources should be reduced.

33. On the **App.config** tab, change the **MaxQueryRetries** to **0**. Leave all other settings the same. This setting will cause the SQL API library to fail immediately if a query is unable to run, giving a true picture of the effectiveness of the resources allocated.

34. On the **Build** menu, click **Build Solution**, and wait until the build has succeeded.

35. On the **Debug** menu, click **Start Debugging**, and wait for it to complete. Note the number of errors that occur might be more than before. Ideally you should increase the throughput of the collection to reduce this number to as close to zero as you can, but this might not always be feasible.

36. On the **App.config** tab, change the **ConsistencyLevel** to **Eventual**, leave **MaxQueryRetries** set to **0**, and leave all other settings the same.

37. On the **Build** menu, click **Build Solution**, and wait until the build has succeeded.

38. On the **Debug** menu, click **Start Debugging**, and wait for it to complete. You will be far fewer errors due to the decreased resource requirements of eventual consistency.

#### Task 2: Examine the Physical to Logical Mapping of Partitions

1. In Internet Explorer, on the **20777a-sql-\<*your name\>-\<the day*\>** blade, under **Monitoring**, click **Metrics**.

2. On the **Metrics** blade, on the **Storage** tab, in **Database(s)** drop-down list, click **Cricket**.

3. In the **Collection(s)** drop-down list, click **MatchesPartitionedByHomeTeam**.

4. In the **Data + Index storage consumed per partition key range** graph, click the bar for the only partition that is displayed. This partition should be named partition 0.

5. In the **Showing partition keys for partition 0** pane, you should see the top three partition keys defining the logical partitions in partition 0. These should be labelled **England**, **Australia**, and **India**.

6. In the **Collection(s)** drop-down list, click **MatchesPartitionedByMatchType**.

7. In the **Data + Index storage consumed per partition key range** graph, click the bar for the only partition that is displayed. This partition should be named partition 0.

8. In the **Showing partition keys for partition 0** pane, you should see the top three partition keys, these should be **T20**, **ODI**, and **Test**.

**Task 3: Demonstration clean up**

1. In the Azure portal, in the left pane, click **Resource groups**.

2. If the **Message from webpage** dialog box appears, click **OK**.

3. On the **Resource groups** blade, right-click **20777aMod4**, and then click **Delete resource group**.

4. On the **Are you sure you want to delete "20777aMod4"?** blade, in the **TYPE THE RESOURCE GROUP NAME** box, type **20777aMod4**, and then click **Delete**.

5. Close all open windows.

---
© 2019 Microsoft Corporation. All rights reserved.

The text in this document is available under the [Creative Commons Attribution 3.0 License](https://creativecommons.org/licenses/by/3.0/legalcode), additional terms may apply. All other content contained in this document (including, without limitation, trademarks, logos, images, etc.) are **not** included within the Creative Commons license grant. This document does not provide you with any legal rights to any intellectual property in any Microsoft product. You may copy and use this document for your internal, reference purposes.

This document is provided "as-is." Information and views expressed in this document, including URL and other Internet Web site references, may change without notice. You bear the risk of using it. Some examples are for illustration only and are fictitious. No real association is intended or inferred. Microsoft makes no warranties, express or implied, with respect to the information provided here.
