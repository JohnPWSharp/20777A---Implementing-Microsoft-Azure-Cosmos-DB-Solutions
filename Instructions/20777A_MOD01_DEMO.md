# Module 1: Introduction to Azure Cosmos DB

- [Module 1: Introduction to Azure Cosmos DB](#module-1-introduction-to-azure-cosmos-db)
    - [Lesson 1: Review of NoSQL database structures](#lesson-1-review-of-nosql-database-structures)
        - [Demo 1: Creating a Cosmos DB database using the Azure portal](#demo-1-creating-a-cosmos-db-database-using-the-azure-portal)
            - [Preparation](#preparation)
            - [Task 1: Create a SQL API account, database, and collection](#task-1-create-a-sql-api-account-database-and-collection)
            - [Task 2: Add Sample Documents to the Collection](#task-2-add-sample-documents-to-the-collection)
            - [Task 3: Perform sample SQL queries](#task-3-perform-sample-sql-queries)
    - [Lesson 2: Migrating data and applications to Cosmos DB](#lesson-2-migrating-data-and-applications-to-cosmos-db)
        - [Demo 1: Importing data from a MongoDB database](#demo-1-importing-data-from-a-mongodb-database)
            - [Preparation](#preparation)
            - [Task 1: Show the sample app connecting to a local MongoDB database](#task-1-show-the-sample-app-connecting-to-a-local-mongodb-database)
            - [Task 2: Copy the data from MongoDB into a MongoDB API database](#task-2-copy-the-data-from-mongodb-into-a-mongodb-api-database)
            - [Task 3: Copy the data from the MongoDB into a SQL API database](#task-3-copy-the-data-from-the-mongodb-into-a-sql-api-database)
        - [Demo 2: Importing data from Azure Table storage](#demo-2-importing-data-from-azure-table-storage)
            - [Preparation](#preparation)
            - [Task 1: Show the data in Azure Table storage](#task-1-show-the-data-in-azure-table-storage)
            - [Task 2: Move the data from Table storage into a Table API database in Cosmos DB](#task-2-move-the-data-from-table-storage-into-a-table-api-database-in-cosmos-db)
            - [Task 3: Verify the data in Cosmos DB](#task-3-verify-the-data-in-cosmos-db)
        - [Demo 3: Reconfiguring a MongoDB application to use a Cosmos DB](#demo-3-reconfiguring-a-mongodb-application-to-use-a-cosmos-db)
            - [Preparation](#preparation)
            - [Task 1: Show the Sample Application Connecting to MongoDB](#task-1-show-the-sample-application-connecting-to-mongodb)
            - [Task 2: Reconfigure the Application to Connect to Cosmos DB](#task-2-reconfigure-the-application-to-connect-to-cosmos-db)
            - [Task 3: Verify the Data in Cosmos DB](#task-3-verify-the-data-in-cosmos-db)
        - [Demo 4: Reconfiguring an Azure Table storage application for Cosmos DB](#demo-4-reconfiguring-an-azure-table-storage-application-for-cosmos-db)
            - [Preparation](#preparation)
            - [Task 1: Show the sample application connecting to Azure Table storage](#task-1-show-the-sample-application-connecting-to-azure-table-storage)
            - [Task 2: Show the sample application connecting to Cosmos DB](#task-2-show-the-sample-application-connecting-to-cosmos-db)
        - [Demo 5: Using the SQL API to access data](#demo-5-using-the-sql-api-to-access-data)
            - [Preparation](#preparation)
            - [Task 1: Show the sample application that performs queries against the SQL API database](#task-1-show-the-sample-application-that-performs-queries-against-the-sql-api-database)
            - [Task 2: Run the sample application](#task-2-run-the-sample-application)
        - [Demo 6: Comparing BulkExecutor performance](#demo-6-comparing-bulkexecutor-performance)
            - [Preparation](#preparation)
            - [Task 1: Update the App.config file](#task-1-update-the-appconfig-file)
            - [Task 2: Run the application](#task-2-run-the-application)
            - [Task 3: Explain how it works](#task-3-explain-how-it-works)
    - [Lesson 3: Managing data in Cosmos DB](#lesson-3-managing-data-in-cosmos-db)
        - [Demo 1: Replicating data and controlling data lifetime](#demo-1-replicating-data-and-controlling-data-lifetime)
            - [Preparation](#preparation)
            - [Task 1: Configure cross-region replication](#task-1-configure-cross-region-replication)
            - [Task 2: Set the lifetime of documents](#task-2-set-the-lifetime-of-documents)
            - [Task 3: Demonstration clean up](#task-3-demonstration-clean-up)

## Lesson 1: Review of NoSQL database structures

### Demo 1: Creating a Cosmos DB database using the Azure portal

#### Preparation

Before starting this demo:

1.  Ensure that the **MT17B-WS2016-NAT** and **20777A-LON-DEV** virtual
    machines are running, and then log on to **20777A-LON-DEV** as
    **Administrator** with the password **Pa55w.rd**.
2.  On the toolbar, click **Internet Explorer**.
3.  In Internet Explorer, go to **http://portal.azure.com**, and sign in
    using the Microsoft account that is associated with your Azure
    Learning Pass subscription.

#### Task 1: Create a SQL API account, database, and collection

1. In the Azure portal, in the left panel, click **Azure Cosmos DB**,
    and then click **+ Add**.
2. On the **Azure Cosmos DB** blade, under the **Resource Group** box click **Create new**, type **20777aMod1**, and then click **OK**
3. In the **Account name** box, type **20777a-sql-\<your name\>-\<the day\>**, for example, **20777a-sql-john-10**.
4. In the **API** drop-down list, note the options available, and then click **SQL**.
5. In the **Location** drop-down list, click the region closest to your current location, click **Review + create**, and then click **Create**.
6. Wait for the Azure Cosmos DB to be created—this could take a few minutes.    
7. On the **Azure Cosmos DB** blade, click **20777a-sql-\<your
    name\>-\<the day\>**, and then click **Data Explorer**.
8. In the **SQL API** pane, click **New Database**.
9.  On the **New** **Database** blade, in the **Database id** box, type
    **Personnel**, and then click **OK**.
10. In the **SQL API** pane, click **New Collection**.
11. On the **Add Collection** blade, under **Database id**, click **Use
    existing**.
12. In the **Choose an existing database** drop-down list, click
    **Personnel**.
13. In the **Collection Id** box, type **Employees**, and then click
    **OK**.

#### Task 2: Add Sample Documents to the Collection

1.  In the **SQL API** pane, in the left pane, expand **Personnel**,
    expand **Employees**, and then click **Documents**.
2.  On the **Documents** tab, click **New Document**.
3.  Replace the following text:
    ```JSON
    {
        "id": "replace_with_new_document_id"
    }
    ```
    with
    ```JSON
    {
        "id": "1",
        "employeeNumber": "Emp02",
        "name": {
            "firstName": "Kayla",
            "lastName": "Woodcock"
        },
        "email": "kaylaw@wideworldimporters.com"
    }
    ```
5.  On the **Documents** tab, click **Save**.
6.  On the **Documents** tab, click **New Document**.
7.  Replace the following text:

    ```JSON
    {
        "id": "replace_with_new_document_id"
    }
    ```

    with
    ```JSON
    {
        "id": "2",
        "employeeNumber": "Emp08",
        "name": {
            "firstName": "Anthony",
            "lastName": "Grosse"
        },
        "email": "anthonyg@wideworldimporters.com"
    }
    ```
8.  On the **Documents** tab, click **Save**.

#### Task 3: Perform sample SQL queries

1.  In the **SQL API** pane, click **New SQL Query**.
2.  On the **Query 1** tab, replace the following text:
    
    ```SQL
    SELECT * FROM c
    ```
    
    with
    
    ```SQL
    SELECT * FROM Employees e
    ```
3.  On the Query 1 tab, click **Execute Query**.

    > **Note:** there are two results that are returned as a JSON document.
    > Explain the structure of the JSON document, highlighting the extra
    > system properties that are added by Cosmos DB.

4.  In the **SQL API** pane, click **New SQL Query**.
5.  On the **Query 2** tab, replace the following text:
    
    ```SQL
    SELECT * FROM c
    ```

    with
    
    ```SQL
    SELECT e.employeeNumber FROM Employees e
    ```
6.  On the **Query 2** tab, click **Execute Query**.

    > **Note:** only the employeeNumber field has been returned for the two
    > records. Also note that the **Request Charge** is less than the first
    > query.

7.  In the **SQL API** pane, click **New SQL Query**.
8.  On the **Query 3** tab, replace the following text:

    ```SQL
    SELECT * FROM c
    ```

    with
    
    ```SQL
    SELECT e.name FROM Employees e
    ```
9. On the **Query 3** tab, click **Execute Query**.

    > **Note:** the JSON document has returned the child elements of the name
    > from the Employees collection.

10. In the **SQL API** pane, click **New SQL Query**.
11. On the **Query 4** tab, replace the following text:

    ```SQL
    SELECT * FROM c
    ```

    with
    
    ```SQL
    SELECT e.name.firstName FROM Employees e
    ```
12. On the **Query 4** tab, click **Execute Query**.

    > **Note:** you can traverse the JSON document to return the data you need,
    > at any level in the document.

13. In the **SQL API** pane, click **New SQL Query**.
14. On the **Query 5** tab, replace the following text:

    ```SQL
    SELECT * FROM c
    ```

    with
    
    ```SQL
    SELECT e.name.firstname FROM Employees e
    ```
15. On the **Query 5** tab, click **Execute Query**.

    > **Note:** Cosmos DB is a NoSQL database, it has no schema. Therefore, a
    > query can't be syntax checked for column names, as it could if this
    > was an RDBMS. Therefore, the above query doesn't return any error
    > because firstname doesn't exist—the property is actually called
    > firstName.

16. Close all open windows, but keep the Azure portal open for the next
    demo.

## Lesson 2: Migrating data and applications to Cosmos DB

### Demo 1: Importing data from a MongoDB database

#### Preparation

You need to have completed the previous demonstration before following
these steps.

Before starting this demo:

1.  Ensure that the **MT17B-WS2016-NAT** and **20777A-LON-DEV** virtual
    machines are running, and then log on to **20777A-LON-DEV** as
    **Administrator** with the password **Pa55w.rd**.
2.  In Internet Explorer, open a new tab, and then go to
    [**http://downloads.mongodb.org/win32/mongodb-win32-x86\_64-2008plus-ssl-3.6.5-signed.msi**](http://downloads.mongodb.org/win32/mongodb-win32-x86_64-2008plus-ssl-3.6.5-signed.msi).
3.  In the message box, click **Run**.
4.  In the **MongoDB 3.6.5 2008R2Plus SSL (64 bit) Setup** dialog box,
    click **Next**.
5.  On the **End-User License Agreement** page, select the **I accept
    the terms in the License Agreement** check box, and then click
    **Next**.
6.  On the **Choose Setup Type** page, click **Complete**.
7.  On the **Install MongoDB Compass** page, click **Next**.
8.  On the **Ready to install MongoDB 3.6.5 2008R2Plus SSL (64 bit)**
    page, click **Install**.
9.  On the **Completed the MongoDB 3.6.5 2008R2Plus SSL (64 bit) Setup
    Wizard** page, click **Finish**.
10. In MongoDB Compass Community - Connect, on the **License Agreement**
    page, click **Agree**.
11. On the **Welcome to MongoDB Compass Community** page, click
    **Close**.
12. On the **Privacy Settings** page, clear all the check boxes, and
    then click **Start Using Compass**.
13. Close MongoDB Compass Community - Connect.
14. Open a Command Prompt, and then run the following code:

    ```DOS
    "C:\Program Files\MongoDB\Server\3.6\bin\mongod.exe" --dbpath E:\Demofiles\Mod01\MongoDB
    ```
15. Open a new Command Prompt, and then run the following code:

    ```DOS
    "C:\Program Files\MongoDB\Server\3.6\bin\mongo.exe"
    ```
16. At the MongoDB prompt, enter the following code:
    ```
    use admin;
    db.createUser(
        {
            user: "useradmin",
            pwd: "Pa55w.rd",
            roles: [ { role: "userAdminAnyDatabase", db: "admin" } ]
        }
    );
    use DeviceData;
    db.createUser(
        {
            user: "deviceadmin",
            pwd: "Pa55w.rd",
            roles: [ { role: "readWrite", db: "DeviceData" } ]
        }
    );
    use admin;
    db.shutdownServer();
    exit;
    ```
17. In the first Command Prompt window, run the following code:
    
    ```DOS
    "C:\Program Files\MongoDB\Server\3.6\bin\mongod.exe" --auth --dbpath E:\Demofiles\Mod01\MongoDB
    ```
18. In the second Command Prompt window, run the following code:

    ```DOS
    "C:\Program Files\MongoDB\Server\3.6\bin\mongo.exe" -u "deviceadmin" -p "Pa55w.rd" --authenticationDatabase "DeviceData"
    ```
19. At the MongoDB prompt, run the following code:

    ```
    use DeviceData;
    db.testcollection.insert({testdata: 1});
    show collections;
    db.testcollection.find();
    ```
20. Leave the command windows open for the following tasks.

#### Task 1: Show the sample app connecting to a local MongoDB database

**Scenario:** you need to capture temperature data from a series of
devices and store this information as a set of documents in a local
MongoDB.

1.  On the Start menu, click **Visual Studio 2017**.
2.  In Visual Studio 2017, on the **File** menu, point to **Open**, then
    click **Project/Solution**.
3.  In the **Open Project** dialog box, go to
    **E:\\Demofiles\\Mod01\\MongoDeviceDataCapture**, click
    **MongoDeviceDataCapture.sln**, and then click **Open**.
4.  On the **Build** menu, click **Build Solution**.
5.  On the **Debug** menu, click **Start Without Debugging**.
6.  In the **cmd.exe** window, let the code run for a minute to allow
    some data to be written, and then press Enter.
7.  In the **cmd.exe** window, press Enter to close the window.
8.  In the command window with the MongoDB shell, enter the following
    code :
    
    ```
    use DeviceData;
    db.Temperatures.find();
    db.Temperatures.count();
    ```
    > Note the content and number of documents that have been created.

#### Task 2: Copy the data from MongoDB into a MongoDB API database

1.  In Internet Explorer, go to **http://portal.azure.com**, and sign in
    if required.
2.  In the left pane, click **Azure Cosmos DB**, and then click **+
    Add**.
3.  On the **Azure Cosmos DB** blade, in the **Resource Group** drop-down list, click **20777aMod1**.

4.  In the **Account name** box, type
    **20777a-mongo-\<your name\>-\<the day\>**, for example
    **20777a-Mongo-jane-12**.
5.  In the **API** drop-down list, click **MongoDB**.
6.  In the **Location** drop-down list, click the region closest to your
    current location, click **Review * create**, and then click
    **Create**.
7.  Wait for the Azure Cosmos DB to be created—this might take a few
    minutes.    
8.  On the **Azure Cosmos DB** blade, click **20777a-mongo-\<your
    name\>-\<the day\>**, and then click **Data Explorer**.
9.  On the **MONGODB API** blade, click **New Database**.
10. On the **New Database** blade, in the **Database id** box, type
    **DeviceData**, and then click **OK**.
11. On the **MONGODB API** blade, click **New Collection**.
12. On the **Add Collection** blade, under **Database id**, click **Use
    existing**.
13. In the **Choose an existing database** drop-down list, click
    **DeviceData**.
14. In the **Collection Id** box, type **Temperatures**.
15. In the **Throughput** box, change **1000** to **5000**, and then
    click **OK**.
16. In the left pane, under **Settings**, click **Connection String**.
17. Make a note of the following values:

    ```
    HOST
    PORT
    USERNAME
    PRIMARY PASSWORD
    ```
18. On the Start menu, type **cmd**, and then press Enter.
19. At the command prompt, enter the following code:
    
    ```DOS
    E:
    cd \
    "C:\Program Files\MongoDB\Server\3.6\bin\mongodump" --db DeviceData --collection Temperatures --out Data -u "deviceadmin" -p "Pa55w.rd"
    ```
    > **Note:** this command creates a backup of the Temperatures collection
    > (Temperatures.bson) in the E:\\Data\\DeviceData folder.

20. At the command prompt, enter the following code to import the data
    into the MongoDB API database (replace **HOST**, **PORT**,
    **USERNAME**, and **PRIMARYPASSWORD** with the values recorded when
    you created the Cosmos DB
    account):
    
    ```DOS
    "C:\Program Files\MongoDB\Server\3.6\bin\mongorestore" --host HOST --port PORT -u USERNAME -p PRIMARYPASSWORD --db DeviceData --collection Temperatures --ssl --sslAllowInvalidCertificates E:\Data\DeviceData\Temperatures.bson
    ```
21. Wait until the restore is complete.
22. In Internet Explorer, in the Azure portal, in the left pane, click
    **Data Explorer**.
23. On the **MONGODB API** blade, expand **DeviceData**, right-click
    **Temperatures**, and then click **New Shell**.
24. On the **Shell 1** tab, at the `>` prompt, type
    **db.Temperatures.count()**, and then press Enter.

    > **Note:** the shell returns the RUs consumed in performing the query. The
    > number of documents should match the number on the local MongoDB
    > database.

#### Task 3: Copy the data from the MongoDB into a SQL API database

1.  In Internet Explorer, in the Azure portal, in the left pane, click
    **Azure Cosmos DB**, and note the previously created databases.
2.  Click the **20777a-sql-\<your name\>-\<the day\>** database that was
    created in the previous demo.
3.  On the **20777a-sql-\<your name\>-\<the day\>** blade, click **Data Explorer**, and then click
    **New Database**.
4.  On the **New Database** blade, in the **Database id** box, type
    **DeviceData**, and then click **OK**.
5.  In the **SQL API** pane, click **New Collection**.
6.  In the **Add Collection** blade, under **Database id**, click **Use
    existing**.
7.  In the **Choose an existing database** drop-down list, click
    **DeviceData**.
8.  In the **Collection Id** box, type **Temperatures**.
9.  In the **Throughput** box, change **1000** to **5000**, and then
    click **OK**.
10. In the left pane, under **Settings**, click **Keys**.
11. Make a note of the **PRIMARY CONNECTION STRING**.
12. In Internet Explorer, open a new tab, and then go to
    **https://github.com/azure/azure-documentdb-datamigrationtool**.
13. Click **Clone or download**, and then click **Download ZIP**.
14. In the message box, click **Save**.
15. When the file has downloaded, in the message box, click **Open
    folder**.
16. Right-click **azure-documentdb-datamigrationtool-master.zip**, and
    then click **Extract All**.
17. In the **Extract Compressed (Zipped)** **Folders** window, change
    the folder to **E:\\dmt**, and then click **Extract**.
18. When the files have extracted, double-click the
    **azure-documentdb-datamigrationtool-master** folder, and then
    double-click **DataTransfer.sln**.
19. In the **How do you want to open this file?** dialog box, click
    **Visual Studio 2017**, and then click **OK**.
20. In the **Security Warning for Microsoft.DataTransfer.Extensibility**
    window, clear the **Ask me for every project in this solution**
    check box, and then click **OK**.
21. In Solution Explorer, right-click **Solution 'DataTransfer'**, then
    click **Set StartUp Projects**.
22. In the **Solution 'DataTransfer' Property Pages** dialog box, in the
    **Single startup project** drop-down list, click
    **Microsoft.DataTransfer.WpfHost**, and then click **OK**.
23. Press F5 to build the application.
24. In the **DocumentDB Data Migration Tool** window, on the **Welcome**
    page, click **Next**.
25. On the **Source Information** page, in the **Import from** drop-down
    list, click **MongoDB**.
26. In the **Connection String** box, type
    **mongodb://deviceadmin:Pa55w.rd@localhost/DeviceData**.
27. In the **Collection** box, type **Temperatures**, and then click
    **Next**.
28. On the **Target Information** page, ensure the **Export to** list is
    set to **DocumentDB - Sequential record import (partitioned
    collection)**.
29. In the **Connection String** box, paste the **PRIMARY CONNECTION
    STRING** you noted earlier, and then at the end of the string, type
    **database=DeviceData**.
30. In the **Collection** box, type **Temperatures**.
31. In the **Partition Key** box, type **deviceID**.
32. In the **Id Field** box, type **\_id**, and then click **Next**.

    > **Note:** when you specify an Id field, this significantly speeds up the
    > import, because Cosmos DB doesn't have to create a unique key for each
    > document it's creating.

33. On the **Advanced** page, click **Next**.
34. On the **Summary** page, click **Import**.
35. Wait for the import to complete.
36. In Internet Explorer, in the Azure portal, in the
    **20777a-sql-\<your name\>-\<the day\>** database, click **Data
    Explorer**.
37. In the **SQL API** pane, expand **DeviceData**, right-click
    **Temperatures**, and then click **New SQL Query**.
38. On the **Query 1** tab, replace the following text, and then click
    **Execute Query**:

    ```SQL
    SELECT * FROM c
    ```
    with

    ```SQL
    SELECT VALUE COUNT(1) FROM c
    ```
39. On the **Query 1** tab, click **Execute Query**.

    > **Note:** the number of documents returned should match the number in the
    > local MongoDB and the Azure Cosmos DB with the Mongo API.

40. In Visual Studio 2017, in the **DataTransfer** instance, on the
    **Debug** menu, click **Stop Debugging**.
41. Close all instances of Visual Studio 2017.
42. Close all open windows, but keep the Azure portal open for the next
    demo.

### Demo 2: Importing data from Azure Table storage

#### Preparation

You need to have completed the previous demonstration before following
these steps.

Before starting this demo:

1.  In Internet Explorer, go to **http://portal.azure.com**, and sign in
    if required.
2.  In the left pane, click **+ Create a resource**, click **Storage**, and then click **Storage account - blob file, table, queue**.
3.  On the **Create storage account** blade, in the **Resource group** drop-down list, click **20777aMod1**.
4.  In the **Storage account name** box, type **20777a**.
5.  Click **Review + create**, and then click **Create**.
6.  Wait for the Azure storage account to be created; this might take a few minutes.
7.  In the left pane of the Azure portal, click **All resources**, and then click
    the **20777a** storage account.
8.  On the **20777a** blade, on the **Overview** page, under
    **Table service**, click **Tables**, and then click **+ Table**.
9.  In the **Add table** dialog box, in the **Table name** box, type
    **CriminalRecords**, and then click **OK**.
10. In the left pane, under **Settings**, click **Access keys**.
11. Make a note of the **key1** **Connection string**.
12. On the Start menu, click **Visual Studio 2017**.
13. In Visual Studio 2017, on the **File** menu, point to **Open**, and
    then click **Project/Solution**.
14. In the **Open Project** dialog box, go to
    **E:\\Demofiles\\Mod01\\TableStorageOffences**, click
    **TableStorageOffences.sln**, and then click **Open**.
15. In Solution Explorer, under **PopulateOffencesStore**, double-click **App.config**.
16. In the **StorageConnectionString** value setting, paste the **key1**
    **Connection string** you noted earlier, for
    example:
    
    ```XML
    <add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=20777a;AccountKey=B2AYQKUFO+GM/wSozRtBJJ+S8wZ2nm1KwJOvzeZ+j3jjQKy5JArl/thlYgly3rYPC+7z8ZyTesW/nStCKy427w==;EndpointSuffix=core.windows.net" />
    ```
18. In Solution Explorer, under **TableStorageOffencesLib**, double-click **App.config**.
19. In the **StorageConnectionString** value setting, paste the **key1**
    **Connection string** you noted earlier, for
    example:
    
    ```XML
    <add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=20777a;AccountKey=B2AYQKUFO+GM/wSozRtBJJ+S8wZ2nm1KwJOvzeZ+j3jjQKy5JArl/thlYgly3rYPC+7z8ZyTesW/nStCKy427w==;EndpointSuffix=core.windows.net" />
    ```
20. Press F5 to build and run the application.
21. At the **Press Enter to end program** prompt, press Enter.
22. In File Explorer, navigate to **E:\\Demofiles\\Mod01**, right-click
    **Setup.cmd**, and then click **Run as administrator**.

#### Task 1: Show the data in Azure Table storage

**Scenario:** There's a small database containing the details of criminals
and the offenses they have committed. Use Visual Studio to examine some
of the records in the database.

1.  In Visual Studio 2017, on the **View** menu, click **Cloud
    Explorer**.
2.  In the **Cloud Explorer** pane, at the top, click the **Account
    Management** button, and then click **Manage Accounts**.
3.  In the **Sign in to Visual Studio** window, under **All Accounts**,
    click **Add an account**.
4.  Enter your Azure Pass login credentials to sign in, and then click
    **Close**.
5.  In the **Cloud Explorer** pane, click **Apply**.
6.  Expand the **Azure Pass** account, expand **Storage Accounts**,
    expand **20777a**, expand **Tables**, and then double-click
    **CriminalRecords**.

    > **Note:** 100 records are shown in a table view, it might be easier to
    > reorder by RowKey.

#### Task 2: Move the data from Table storage into a Table API database in Cosmos DB

1.  In the Azure portal, in the left pane, click **Azure Cosmos DB**,
    and then click **+ Add**.
2.  On the **Azure Cosmos DB** blade, in the **Resource group** drop-down list, click **20777aMod1**
3.  In the **Account name** box, type
    **20777a-table-api-\<your name\>-\<the day\>**, for example
    **20777a-table-api-james-12**.
4.  In the **API** drop-down list, click **Azure Table**.
5.  In the **Location** dowp-down list, click the region closest to your
    current location, click **Review + create**, and then click
    **Create**.
6.  Wait for the Azure Cosmos DB to be created—this could take a few
    minutes.
7.  On the **Azure Cosmos DB** blade, click **20777a-table-api-\<your
    name\>-\<the day\>**, click **Data Explorer**, and then click **New
    Table**.
8.  On the **Add Table** blade, in the **Table Id** box, type
    **CriminalRecords**.
9.  Change the **Throughput** value to **5000**, and then click **OK**.
10. In the left pane, under **Settings**, click **Connection String**.
11. Make a note of the **PRIMARY CONNECTION STRING** value.
12. On the Start menu, type **cmd**, and then press Enter.
13. At the command prompt, run the following commands, replacing the
    **\<Table Storage Connection string\>** with the **key1**
    **Connection string**, and **\<Cosmos DB PRIMARY CONNECTION
    STRING\>** with the **PRIMARY CONNECTION STRING** values you noted
    earlier:

    ```DOS
    E:
    cd "E:\dmt\bin\dt"
    dt /s:AzureTable /s.ConnectionString:"<Table  Connection string>" /s.Table:CriminalRecords /t:TableAPIBulk /t.ConnectionString:"<Cosmos DB PRIMARY CONNECTION STRING>" /t.TableName:CriminalRecords
    ```
    > **Note:** the tool will report the number of records transferred—this
    > should match the 100 created in the earlier steps.

#### Task 3: Verify the data in Cosmos DB

1.  In Internet Explorer, in the left pane, click **Azure Cosmos DB**,
    click the **20777a-table-api-\<your name\>-\<the day\>** database,
    and then click **Data Explorer**.

2.  On the **AZURE TABLE API** blade, expand **TablesDB**, expand
    **CriminalRecords**, and then click **Entities**.

3.  On the **Entities** tab, browse the list of criminal records, and
    note they total **100**.

4.  Close all open windows, but keep the Azure portal open for the next
    demo.

### Demo 3: Reconfiguring a MongoDB application to use a Cosmos DB

#### Preparation

You need to have completed the previous demonstration before following
these steps.

Before starting this demo:

1.  On the Start menu, type **cmd**, right-click **Command Prompt**, and
    then click **Run as administrator**.
2.  At the command prompt, type the following command, and then press
    Enter:
    
    ```DOS
    "C:\Program Files\MongoDB\Server\3.6\bin\mongod.exe" --dbpath E:\Demofiles\Mod01\MongoDB
    ```
3.  On the Start menu, type **cmd**, right-click **Command Prompt**, and
    then click **Run as administrator**.
4.  At the command prompt, type the following command, and then press
    Enter :

    ```DOS
    "C:\Program Files\MongoDB\Server\3.6\bin\mongo.exe"
    ```
5.  At the MongoDB prompt, enter the following code:

    ```
    use DeviceData;
    db.Temperatures.deleteMany({});
    db.Temperatures.count();
    ```
6.  Leave both command prompts running for the demonstration.

#### Task 1: Show the Sample Application Connecting to MongoDB

1.  On the Start menu, click **Visual Studio 2017**.
2.  In Visual Studio 2017, on the **File** menu, point to **Open**, and
    then click **Project/Solution**.
3.  In the **Open Project** dialog box, go to
    **E:\\Demofiles\\Mod01\\MongoDeviceDataCapture**, click
    **MongoDeviceDataCapture.sln**, and then click **Open**.
4.  In Solution Explorer, double-click the **TemperatureDevice.cs**
    file. This class simulates a device capturing and storing
    temperature data. The code uses the native MongoDB API for C\# (not
    Cosmos DB) to connect to the database and insert documents.
5.  Press F5 to build and run the application.
6.  In the **MongoDeviceDataCapture.exe** window, let the code run for a
    minute to allow some data to be written, and then press Enter.
7.  In the command window with the MongoDB shell, run the following
    code:

    ```
    db.Temperatures.find();
    ```
#### Task 2: Reconfigure the Application to Connect to Cosmos DB

1.  In Internet Explorer, go to **http://portal.azure.com**, and sign in
    if required.
2.  In the left pane, click **Azure Cosmos DB**, and then click the
    Mongo database you created in the previous demonstration named
    **20777a-mongo-\<your name\>-\<the day\>**.
3.  In the left pane, click **Data Explorer**, expand **DeviceData**,
    right-click **Temperatures**, and then click **New Shell**.
4.  On the **Shell 1** tab, at the `>` prompt, run the following code:

    ```
    db.Temperatures.deleteMany({});
    ```
5.  On the **Shell 1** tab, at the `>` prompt, run the following code:

    ```
    db.Temperatures.count();
    ```
6.  In Visual Studio 2017, in Solution Explorer, double-click
    **App.config**.
7.  Highlight all lines from 9 - 12, on the **Edit** menu, point to
    **Advanced**, and then click **Comment Selection** to comment them
    out.
8.  Highlight all lines from 16 - 19, on the **Edit** menu, point to
    **Advanced**, and then click **Uncomment Selection** to uncomment
    them.
9.  In lines 16 - 19, replace the **Address**, **Port**, **Username**,
    and **Password** values with the **HOST**, **PORT**, **USERNAME**,
    and **PRIMARYPASSWORD** value recorded when you created the
    **20777a-mongo-\<your name\>-\<the day\>** Cosmos DB account.
10. In Solution Explorer, double-click **TemperatureDevice.cs**.
11. Comment out line 41 so that matches
    below:
    
    ```C#
    //Credential = MongoCredential.CreateCredential(database, username, password),
    ```
12. Uncomment lines 47 to 52 so that the code is the same as below:

    ```C#
    UseSsl = true,
    SslSettings = new SslSettings
    {
       EnabledSslProtocols = SslProtocols.Tls12
    },
    Credential = new MongoCredential("SCRAM-SHA-1", new MongoInternalIdentity(database, azureLogin.UserName), new PasswordEvidence(azureLogin.SecurePassword))
    ```
13. Press F5 to build and run the application.
14. In the **MongoDeviceDataCapture.exe** window, let the code run for a
    minute to allow for some data to be written, and then press Enter.
 
#### Task 3: Verify the Data in Cosmos DB

1.  In Internet Explorer, on the **MONGODB API** blade, under
    **DeviceData**, expand **Temperatures**, and then click
    **Documents**.
2.  Explore the data by clicking on different documents.
3.  On the **MONGODB API** blade, click **New Query**.
4.  On the **Query 1** tab, type **{}**, and then click **Execute Query**.
5.  Note that the same data is displayed.
6.  Close all open windows, but keep the Azure portal open for the next
    demo.

> **Important:** Once the demo has been completed, at the top of the
> page, click Delete Database to reduce costs. (A 5,000 RU/s collection
> costs $9.60 per day to run)

### Demo 4: Reconfiguring an Azure Table storage application for Cosmos DB

#### Preparation

You need to have completed the previous demonstration before following
these steps. Specifically, the demonstration to create and populate the
CriminalRecords tables in Azure Storage and in the Cosmos DB Azure Table
API database.

#### Task 1: Show the sample application connecting to Azure Table storage

1.  On the Start menu, click **Visual Studio 2017**.
2.  In Visual Studio 2017, on the **File** menu, point to **Open**, and
    then click **Project/Solution**.
3.  In the **Open Project** dialog box, go to
    **E:\\Demofiles\\Mod01\\ListOffences**, click **ListOffences.sln**,
    and then click **Open**.
4.  In Solution Explorer, double-click **Program.cs**.
5.  Explain the **Main** procedure, highlighting the LINQ query to
    filter the results from the Table storage.
6.  In Solution Explorer, double-click **App.config**.
7.  On line 5, replace the **\<enter your connection string here\>**
    with the **key1 Connection String** for the **20777a** storage
    account you created in the previous demonstration.
8.  Press F5 to build and run the application.
9.  In the **ListOffences.exe** window, type **Scar-Face**, and then
    press Enter.
10. The query returns a list of offenses committed.
11. In the **ListOffences.exe** window, type **Snooty**, and then press
    Enter.
12. The query returns a list of offenses committed.
13. In the **ListOffences.exe** window, type **q**, and then press
    Enter.

#### Task 2: Show the sample application connecting to Cosmos DB

1.  In Visual Studio 2017, in the **App.config** file, on line 5,
    replace the **StorageConnectionString** value with the **Primary
    Connection String** for the **20777a-table-api-\<your name\>-\<the
    day\>** database you created in the previous demonstration.
2.  Press F5 to build and run the application.
3.  In the **ListOffences.exe** window, type **Scar-Face**, and then
    press Enter.
4.  The query returns a list of offenses committed.
5.  In the **ListOffences.exe** window, type **Snooty**, and then press
    Enter.
6.  The query returns a list of offenses committed.
7.  In the **ListOffences.exe** window, type **q**, and then press
    Enter.

    > **Note:** that the application works in exactly the same way, the only
    > change to code was the connection string. Data is now being retrieved
    > from your Azure Cosmos DB database with a Table API.

8.  Close all open windows, but keep the Azure portal open for the next
    demo.

### Demo 5: Using the SQL API to access data

#### Preparation

You need to have completed the previous demonstration before following
these steps, as you need to connect to the SQL API Cosmos DB with
DeviceData loaded with temperature data.

1.  In Internet Explorer, go to **http://portal.azure.com**, and sign in
    if required.
2.  In the Azure portal, in the left panel, click **Azure Cosmos DB**,
    and then click the **20777a-sql-\<your name\>-\<the day\>**
    database.
3.  In the left pane, under **Settings**, click **Keys**.
4.  Make a note of the **URI** and **PRIMARY KEY** values for later in
    this demonstration.

#### Task 1: Show the sample application that performs queries against the SQL API database

**Scenario:** Examine a .NET application to connect and read data from a
SQL API database.

1.  On the Start menu, click **Visual Studio 2017**.
2.  In Visual Studio 2017, on the **File** menu, point to **Open**, and
    then click **Project/Solution**.
3.  In the **Open Project** dialog box, go to
    **E:\\Demofiles\\Mod01\\TemperaturesDBDemoCode**, click
    **TemperaturesDBDemoCode.sln**, and then click **Open**.
4.  In Solution Explorer, double-click **App.config**.
5.  Replace the **EndpointUrl** value with the **URI** you noted
    earlier.
6.  Replace the **PrimaryKey** value with the **PRIMARY KEY** you noted
    earlier.
7.  In Solution Explorer, double-click **ThermometerReading.cs**.
8.  Walk through the code, explaining that the class represents the
    schema of temperature data in the database. The next module will
    show how to process data in a Cosmos DB where the schema varies with
    each document.
9.  In Solution Explorer, double-click **Program.cs**.
10. Note that the file has the following directives at the top of the
    program.

    ```C#
    using Microsoft.Azure.Documents;
    using Microsoft.Azure.Documents.Client;
    ```
11. Explain that these namespaces contain the types required to connect
    to a SQL API account and perform queries.
12. On the **Tools** menu, point to **NuGet Package Manager**, and then
    click **Manage NuGet Packages for Solution**.
13. On the **NuGet - Solution** tab, click **Installed**.
14. Point out that the **Microsoft.Azure.DocumentDB.Core** package
    provides the library that implements the client-side API. The
    remaining packages contain the libraries on which this package
    depends (they are located and loaded automatically by the NuGet
    Package Manager).
15. On the **Program.cs** tab, in the **DoWork** task, highlight the
    following statement:

    ```C#
    // Connect to the Cosmos DB account
    this.client = new DocumentClient(new Uri(endpointUrl), primaryKey);
    ```
16. Explain that a **DocumentClient** object represents a connection to
    the Cosmos DB account. All requests to the account have to pass
    through a **DocumentClient** object. The constructor expects the
    **URL** and **PRIMARY KEY** of the Cosmos DB account.

17. Highlight the following block of
    code:

    ```C#
    // Fetch a document by using its unique ID and partition key with ReadDocumentAsync (fastest way to retrieve a document) 
    Console.WriteLine("Enter document ID");
    string id = Console.ReadLine();
    Uri docUri = UriFactory.CreateDocumentUri(this.database, this.collection, id);
    
    Console.WriteLine("Enter partition key (device ID)");
    string deviceID = Console.ReadLine();
    RequestOptions options = new RequestOptions
    {
        PartitionKey = new PartitionKey(deviceID)
    };
    
    var documentResponse = await client.ReadDocumentAsync<ThermometerReading>(docUri, options);
    ```
18. Explain that:
    - The ReadDocumentAsync method provides the fastest way to retrieve a
        document.
    - You create a URI that identifies the document. This URI comprises of
        an encoded reference to the datatabase (DeviceData), collection
        (Temperatures), and the unique Id for the document.
    - Additionally, if the collection is partitioned, you must also
        specify in which partition that data is located (partitioning in
        Cosmos DB is discussed in more detail later).
    - The value returned by the ReadDocumentAsync method is an object
        (specified by the type parameter - ThermometerReading) containing
        the data.

19. Highlight the following block of
    code:

    ```C#
    // Fetch a set of documents by performing a SQL query. This query uses the index over the deviceID field
    Console.WriteLine("Enter device ID");
    deviceID = Console.ReadLine();
    string queryString = $"SELECT c.deviceID, c.temperature, c.time FROM {this.collection} c WHERE c.deviceID = @devID";
    SqlParameterCollection parameters = new SqlParameterCollection()
    {
        new SqlParameter("@devID", deviceID)
    };
    
    SqlQuerySpec querySpec = new SqlQuerySpec()
    {
        QueryText = queryString,
        Parameters = parameters
    };
    
    Uri collectionUri = UriFactory.CreateDocumentCollectionUri(this.database, this.collection);
    var query = this.client.CreateDocumentQuery<ThermometerReading>(collectionUri, querySpec);
    
    // Run the query, and display the list of matching documents
    foreach (var doc in query)
    {
        Console.WriteLine($"{doc.ToString()}");
    }
    ```
20. Explain that:
    - This code uses a SQL statement to find documents that match the
        specified criteria.
    - The deviceID is specified as a parameter to the SQL statement.
    - You specify the collection to search, but you don't need to indicate
        in which partitions that data is likely to be found.
    - Cosmos DB will use an index to find all matching documents.
    - The query (defined by calling the **CreateDocumentQuery** method)
        does not actually run until you start to iterate through the result
        set.

#### Task 2: Run the sample application

1.  In Internet Explorer, go to **http://portal.azure.com**, and sign in
    if required.
2.  In the Azure portal, in the left panel, click **Azure Cosmos DB**,
    click the **20777a-sql-\<your name\>-\<the day\>** database, and
    then click **Data Explorer**.
3.  In the **SQL API** pane, expand **DeviceData**, expand
    **Temperatures**, and then click **Documents**.
4.  On the **Documents** tab, select any document by clicking an id.
5.  From the returned document copy some example **id** values, for
    example **6e8b7b37-a12d-4972-9929-854046a860f0**, and copy some
    **deviceID** values, for example **Device 39**.
6.  In Visual Studio 2017, press F5 to build and run the application.
7.  At the command prompt, paste the copied **id** value, and then press
    Enter.
8.  At the command prompt, paste the copied **deviceID** value, and then
    press Enter.
9.  Note that the specific documents details are returned.
10. At the command prompt, paste the copied **deviceID** value, and then
    press Enter.
11. Note that all the document details for the pasted **devideID** have
    been returned.
12. Press Enter, and then press any key to quit.
13. Close all open windows, but keep the Azure portal open for the next
    demo.

### Demo 6: Comparing BulkExecutor performance

#### Preparation

You need to have completed the first demo in this module and created a
SQL API Cosmos DB database.

1.  In Internet Explorer, go to **http://portal.azure.com**, and sign in
    if required.
2.  In the Azure portal, in the left pane, click **Azure Cosmos DB**,
    and then click the **20777a-sql-\<your name\>-\<the day\>** you
    created in the previous demonstration.
3.  In the left pane, click **Data Explorer**, and then click **New Database**.
4.  On the **New Database** blade, in the **Database id** box, type
    **FlightData**, and then click **OK**.
5.  In left pane, under **Settings**, click **Keys**.
6.  Copy the **URI** and **PRIMARY KEY** values for later in the
    demonstration.

#### Task 1: Update the App.config file

1.  On the Start menu, click **Visual Studio 2017**.
2.  In Visual Studio 2017, on the **File** menu, point to **Open**, and
    then click **Project/Solution**.
3.  In the **Open Project** dialog box, go to
    **E:\\Demofiles\\Mod01\\BulkExectorDemo**, click
    **BulkImportSample.sln**, and then click **Open**.
4.  In Solution Explorer, double-click **App.config**.
5.  Replace the **EndpointUrl** value with the **URI** you noted
    earlier.
6.  Replace the **PrimaryKey** value with the **PRIMARY KEY** you noted
    earlier.
7.  Note the other settings for **NumThreads** and **Throughput**.

#### Task 2: Run the application

1.  Press F5 to build and run the application.
2.  Note that the application loads the csv data, deletes the collection
    that might have existed, and creates a new empty one.
3.  You will then see menu with the following options:

    ```DOS
    A: Import Documents using Parallel Threads
    B: Import Documents using the BulkExecutor Library
    C: Clear the Sample Collection
    X: Exit
    ```
4.  Type **A** to import the data with multiple threads. Note this can
    take around 2 minutes with the default setting of 10 threads. Note
    the time in milliseconds.
5.  Type **C** to clear the collection, and have an empty database for
    the next step.
6.  Type **B** to import the data with BulkExector. This
    should take around 20 seconds with 50,000 RU/s. Note the time to
    compare to the first run.
7.  Type **X** to close the application.
8.  Close all open windows, but keep the Azure portal open for the next
    demo.

> **Important:** When you have finished, use the Azure portal to delete
> the FlightDelays collection, otherwise you will be charged nearly
> $40/day.

#### Task 3: Explain how it works

1.  If you have time, walk through the code.
2.  The multithreaded client approach is implemented by the
    `DelayDataUploadDriver` and `DelayDataUploader` classes. The
    `DelayDataUploadDriver` class creates an instance of the
    `DelayDataUploader` for each thread, and divides the data to
    uploaded amongst these threads. The `DelayDataUploader` class
    uploads each document individually using the `CreateDocumentAsync`
    method of the SQL API.
3.  The method `ImportUsingBulkExecutorLibrary` in the **Program.cs**
    file contains the code that runs the import using the
    **BulkExecutor** library. The processing is performed by a call to
    the `BulkImportAsync` method of the `BulkExecutor` object provided
    by the library.

## Lesson 3: Managing data in Cosmos DB

### Demo 1: Replicating data and controlling data lifetime

#### Preparation

You need to have completed the previous demonstration in this module and
created a SQL API Cosmos DB database with DeviceData and loaded
temperature data into the Temperatures collection.

#### Task 1: Configure cross-region replication

1.  In Internet Explorer, go to **http://portal.azure.com**, and sign in
    if required.
2.  In the Azure portal, in the left pane, click **Azure Cosmos DB**,
    and then click the **20777a-sql-\<your name\>-\<the day\>** you
    created in the previous demonstration.
3.  In the left pane, under **Settings**, click **Replicate data
    globally**.
4.  Note that by default there is only one region, so data isn't being
    replicated to different data centers. However, within the region the
    data is being duplicated across a number of servers as part of the
    resilience of the Cosmos DB platform as a service.
5.  On the map, click two different **+** icons to add two additional
    regions, and then click **Save**.
6.  Note that each region is added as an additional read region.

    > It can take several minutes for the new read regions to become
    > available, especially for large datasets. During the replication
    > process the data is still available from the write region. All writes
    > will still be performed on the write region, with opportunistic
    > updates happening when the new read regions have become available. The
    > current SLA for adding additional regions is a maximum of 30 minutes,
    > but in practice the time is far less than that.

7.  Note that clients can reduce the latency of their queries by
    specifying the region closest to them in their applications
    connection policy.
8.  When the new regions are available, click **Automatic Failover**.
9.  On the **Automatic Failover** blade, click **ON**.
10. Click and drag the lowest region above the highest to demonstrate
    how to change a regions priority. The read region with the highest
    priority will become the write region automatically if the current
    write region fails, and then click **OK**.
11. On the **Replicate data globally** blade, click **Save**, and then
    wait for the changes to be saved.
12. On the **Replicate data globally** blade, click **Manual Failover**.
13. Explain that it is possible to force a manual failover to change the
    read and write responsibilities of a region. Note that Azure
    guarantees no data loss or conflicts if a manual failover is
    triggered.
14. On the **Manual Failover** blade, click the **Close** button.

#### Task 2: Set the lifetime of documents

1.  In the left pane, click **Data Explorer**, expand **DeviceData**,
    expand **Temperatures**, and then click **Scale & Settings**.
2.  On the **Scale & Settings** tab, under **Settings**, under **Time to
    Live**, click **On**.
3.  In the **seconds** box, enter a value of **30**. Explain that you
    can also click **On (no default)** in which case you specify the TTL
    value for each document as they are created.
4.  Under **Throughput (400 - 10,000 RU/s)** set the value to **1000**,
    and then click **Save**.
5.  All the documents in the database will be deleted. Note that this is
    a quick, convenient, and free way to reset a Cosmos DB collection
    for testing purposes.

#### Task 3: Demonstration clean up

1.  In the Azure portal, in the left pane, click **Resource groups**.
2.  On the **Resource groups** blade, right-click **20777aMod1**, and
    then click **Delete resource group**.
3.  On the **Are you sure** **you want to delete "20777aMod1"?** blade,
    in the **TYPE THE RESOURCE GROUP NAME** box, type **20777aMod1**,
    and then click **Delete**.

---
© 2018 Microsoft Corporation. All rights reserved.

The text in this document is available under the [Creative Commons
Attribution 3.0
License](https://creativecommons.org/licenses/by/3.0/legalcode),
additional terms may apply. All other content contained in this document
(including, without limitation, trademarks, logos, images, etc.) are
**not** included within the Creative Commons license grant. This
document does not provide you with any legal rights to any intellectual
property in any Microsoft product. You may copy and use this document
for your internal, reference purposes.

This document is provided "as-is." Information and views expressed in
this document, including URL and other Internet Web site references, may
change without notice. You bear the risk of using it. Some examples are
for illustration only and are fictitious. No real association is
intended or inferred. Microsoft makes no warranties, express or implied,
with respect to the information provided here.
