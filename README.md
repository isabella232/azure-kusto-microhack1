## Microhack 1: Cluster Creation and Data Ingestion (Preview)

This Microhack is organised into the following 4 challenges:
- Challenge 1: Create ADX cluster
- Challenge 2: Create integration with Azure services (Event Hub and Storage Account)
- Challenge 3: Explore and transform data
- Challenge 4: Check stats and key metrics of the cluster

#### Challenge 1: Create ADX cluster
To use Azure Data Explorer (ADX), you first have to create an ADX cluster, and create one or more databases in that cluster. Each database has tables. Then you can ingest data into a database so that you can run queries against it.

In this challenge, you will design an ADX based architecture, create an ADX cluster and database.
In addition, you will get familiarized with two tools that enable you to connect to your Azure Data Explorer and run queries.

**Expected Learning Outcomes:**
- Deploy ADX cluster from Azure Portal
- Use ADX clients such as Kusto Web Explorer and Kusto Explorer
- The initial configuration of the cluster at creation time

##### Task 1: Create an ADX cluster resource
Sign in to the Azure portal, select the + Create a resource button in the upper-left corner of the portal’s main page.

<img src="/assets/images/Challenge1-Task1-Pic1.png" width="400">
  
- Search for Azure Data Explorer. Under Azure Data Explorer, select Create.

<img src="/assets/images/Challenge1-Task1-Pic2.png" width="450">
<img src="/assets/images/Challenge1-Task1-Pic3.png" width="450">

- Fill out the basic cluster details with the following information.

![Screen capture 1](/assets/images/Challenge1-Task1-Pic4.png)

- Subscription: Use your own subscription
- Resource Group: It's recommended to create a new resource group for the microhack's resources. Call it: <youralias>-microhack-RG
- Cluster name: Must be unique for each participant. Call it: <youralias>microhackadx (cluster name must begin with a letter and contain lowercase alphanumeric characters.)
- Region: France Central
-	Enable performance update (EngineV3): keep the default (enabled)
-	Compute specification: For a production system, select the specification that best meets your needs (storage optimized or compute optimized). For this Microhack we can use ‘Compute optimized’, Extra small (2 cores), Standard_D11_v2.
  With various compute SKU options to choose from, you can optimize costs for the performance and hot-cache requirements for your scenario. If you need the most optimal performance for a high query volume, the ideal SKU should be compute-optimized. If you need to query large volumes of data with relatively lower query load, the storage-optimized SKU can help reduce costs and still provide excellent performance. You can read more about ADX’s SKU types [here](https://docs.microsoft.com/en-us/azure/data-explorer/manage-cluster-choose-sku)
- Availability zones: keep the default.
Move to the next tab (“Scale”). Choose how to scale your resource. Select the “Optimized autoscale” option. It’s always recommended to use this option. Optimized Autoscale is a built-in feature that helps clusters perform their best when demand changes. Optimized Autoscale enables your cluster to be performant and cost effective by adding and removing instances based on demand. For this microhack keep the default values (Minimum instance count == 2, Maximum instance count == 3)

You can keep all the other configurations with the default values. 
Select Review + create to review your cluster details. Then, select Create to provision the cluster. Provisioning typically takes about 10 minutes.
Creating an ADX cluster takes in average 10-15 minutes.

When the deployment is complete, select Go to resource. You will be redirected to the ADX cluster resource page. On the top of the Overview page, you can see the basic details of the cluster, like: the Subscription, the state (running) and the URI.

##### Task 2: Create a Database
- You're now ready for the second step in the process: database creation.
- On the Overview tab, select Create database. Alternatively, you can go to the “Databases” blade.

  ![Screen capture 1](/assets/images/Challenge1-Task2-Pic1.png)

- Fill out the form with the following information.
  
  ![Screen capture 1](/assets/images/Challenge1-Task2-Pic2.png)
  
  | Setting       | Suggested Value   | Field Description                                                             |
  | ------------- | ----------------- | ----------------------------------------------------------------------------- |
  | Admin         | Default selected  | The admin field is disabled. New admins can be added after database creation. |
  | Database Name | TelemetryDatabase | The database name must be unique within the cluster.                          |
  | Retention period | 365	| The time span (in days) for which it's guaranteed that the data is kept available to query. The time span is measured from the time that data is ingested. This is the longer-term storage (in reliable storage) retention. |
  | Cache period	| 31	| The time span (in days) for which to keep frequently queried data available in SSD storage or RAM of the cluster’s VM, rather than in longer-term storage. Azure Data Explorer stores all its ingested data in reliable storage (most commonly Azure Blob Storage), away from its actual processing (such as Azure Compute) nodes. To speed up queries on that data, Azure Data Explorer caches it, or parts of it, on its processing nodes, SSD, or even in RAM. The best query performance is achieved when all ingested data is cached. Sometimes, certain data doesn't justify the cost of keeping it "warm" in local SSD storage. For example, many teams consider that rarely accessed older log records are of lesser importance. They prefer to have reduced performance when querying this data, rather than pay to keep it warm all the time. By increasing the cache policy, more VMs will be required to store data on their SSD/RAM. For Azure Data Explorer cluster, compute cost (VMs) is the most significant part of cluster cost as compared to storage and networking. |

- Select Create to create the database. Creation typically takes less than a minute. When the process is complete, you're back on the cluster Overview blade. You can see the database that you have created from on the Databases blade.

  ![Screen capture 1](/assets/images/Challenge1-Task2-Pic3.png)
  
##### Task 3: Write your first Kusto Query Language (KQL) query
  What is a Kusto query?
  Azure Data Explorer provides a web experience that enables you to connect to your Azure Data Explorer clusters and write and run Kusto Query Language queries. The web experience is available in the Azure portal and as a stand-alone web application, the Azure Data Explorer Web UI, that we will use later.
  Kusto query is a read-only request to process data and return results. The request is stated in plain text that's easy to read. A Kusto query has one or more query statements and returns data in a tabular or graph format.
  In the next challenges, we'll ingest data to the cluster, and then learn the most important concepts in KQL and write interesting queries. In this task, you will write a few basic queries to get an understanding of the environment.
  To start, go to the “Query” blade. In this example, you'll use the Azure Data Explorer web interface as a query editor (Kusto Query Language can also be used in Azure Monitor Logs, Azure Sentinel, and other services that are built on-top of Azure Data Explorer.)
  
  ![Screen capture 1](/assets/images/Challenge1-Task3-Pic1.png)
  
  We can see our cluster and the database that we created.
To tun KQL queries, we must select the database that the query will run on (the scope). 
To select the data base, just click on the database name.
Now – you can write a simple QKL query: print ("hello world"),
and hit the “Run” button. The query will be executed and its result can be seen in the result grid on the bottom of the page. 
  
  ![Screen capture 1](/assets/images/Challenge1-Task3-Pic2.png)
  
  You can also download [Kusto Explorer](https://docs.microsoft.com/en-us/azure/data-explorer/kusto/tools/kusto-explorer), the desktop client to run queries and benefit from some advanced features available in the client.

##### Task 4: Enable Diagnostic logs
  Azure Monitor diagnostic logs provide monitoring data about the operation of Azure resources. ADX uses diagnostic logs for insights on ingestion, commands, queries, and tables usage. You can export operation logs to Azure Storage, Event Hub, or Log Analytics.
  Diagnostic logs are disabled by default. To enable diagnostic logs, go to your cluster page in the portal. Under Monitoring, select Diagnostic settings. 

  ![Screen capture 1](/assets/images/Challenge1-Task4-Pic1.png)
  
  Select Add diagnostic setting. In the Diagnostic settings window. Enter a Diagnostic setting name as per your preference. 
Select all the log categories and metrics (SucceededIngestion, FailedIngestion, IngestionBatching, Command, or Query, TableUsageStatistics, TableDetails and Journal).
For this microhack, select the Destination details to be a Log Analytics workspace and select your own workspace or create a new one.
Save the new diagnostic logs settings and metrics.

  
#### Challenge 2: Create integration with Azure services (Event Hub and Storage Account)
  
  Data ingestion to ADX is the process used to load data records from one or more sources into a table in your ADX cluster. Once ingested, the data becomes available for query.

  ADX supports several ingestion methods. [These methods include ingestion tools, connectors and plugins, managed pipelines, programmatic ingestion using SDKs, and direct access to ingestion.]

  **Expected Learning Outcomes:**
  - Create continuous ingestion from Azure Event Hub (a managed pipeline) 
  - Create one-time ingestion from Azure Blob Storage (direct access) to your ADX cluster.

  ##### Task 1: Use the “One-click” UI (User Interfaces) Create a data connection to Event Hub
For the best user experience, we will use the Azure Data Explorer Web UI (aka: Kusto web Explorer/KWE). To open it, click on the “Open in Web UI” or just go to [Kusto Web Explorer](https://dataexplorer.azure.com)
  
  ![Screen capture 1](/assets/images/Challenge2-Task1-Pic1.png)
  
  The web UI opens. The messages are in a JSON format and they are being sent to an event hub named "adx-microhac-eh-2". This is how a sample message looks like:
  ```
  {
  "messageProperties": {
    "iothub-creation-time-utc": "2021-12-22T11:16:58.668Z"
  },
  "enrichments": {},
  "applicationId": "1b2f5f29-a78b-4012-bf31-2016473cadf6",
  "deviceId": "13n5b9yiael",
  "messageSource": "telemetry",
  "telemetry": {
    "Status": "Online",
    "BatteryLife": 52,
    "Light": 62602.66864777621,
    "Tilt": 44.70275959833819,
    "Shock": -6.381560743718394,
    "ActiveTags": 164,
    "Location": {
      "lon": -68.4585,
      "lat": 40.9633,
      "alt": 1069.4617
    },
    "TransportationMode": "Train",
    "LostTags": 5,
    "Temp": 13.178830710467722,
    "Humidity": 91.17280445807984,
    "Pressure": 1033.3527307505506,
    "TotalTags": 187
  },
  "schema": "default@v1",
  "enqueuedTime": "2021-12-22T11:16:58.753Z",
  "templateId": "dtmi:ltifbs50b:mecybcwqm"
}
```

KWE lets us easily connect to Azure Event Hub and build a table which is schema based on an event sample data. Go to the “Data” blade. The name of this capability is “One-click ingestion” and it allows you to quicky ingest data.
  
  ![Screen capture 1](/assets/images/Challenge2-Task1-Pic2.png)
  
  ##### Task 2: Configure the Event hub data connection
  
  The ’Ingest new data’ wizard opens. 
  
  Destination tab:
The Cluster and Database fields are auto-populated. Select the ADX cluster and the Database that you created in challenge #1. We haven’t created a table, so use the “Create new table” option. 
  
  Source tab:
Set the Source type to “Event Hub”, and specify the event hub details:
- Subscription: Kusto_PM_Experiments
- Event hub namepsace: adx-microhack-eh-ns
- Event hub: adx-microhac-eh-2
- Data connection name: we used ‘Database1-adx-microhack-eh’. Data connection connects ADX database to Event hub (or to storage account through Event Grid notifications)
- Consumer group: use your respective consumer group
- Compression: None
  Event system properties: leave empty. For this Microhack, we are not going to use them. (System properties store properties (meta data) that are set by the Event Hubs service, at the time the event is enqueued. ADX can embed the selected properties into a new column in your destination table.)
  
  ![Screen capture 1](/assets/images/Challenge2-Task2-Pic1.png)

  Click on “Next: Schema” 
  
  Schema tab:
  
  Sample data will be read from the Event Hub and you will see data preview. The default format is TXT, but our Event hub sends JSON data.
Change the Data format field to JSON. Keep the nested levels as 1. As you can see, ADX inferred the column names and the data type according to the JSON’s data.
Among the types you can find GUID, string, datetime, and dynamic.</br>
You can think of dynamic column as a JSON-like type. The dynamic data type can take on any value of other scalar data types like: bool, datetime, guid, int, long, real, string, timespan, an array of dynamic values, and a property bag (dictionary) that maps unique string values to dynamic values.</br>
Although the dynamic type appears JSON-like, it can hold values that the JSON model does not represent because they don't exist in JSON (e.g., long, real, datetime, timespan, and guid).

The 'Nested levels' field expands levels of nested data in dynamic type columns into separate columns. Although the raw event’s JSON format has nestedness of 2 levels, for this microhack we will use 1 level and see later how to leverage the powerful update policy capability of ADX to break these dynamic columns.

This is an example of the telemetry JSON (that is part of a bigger JSON that is being sent from the evet hub):
```
{"Location":{"alt":"252.71910000000003","lon":-93.2176,"lat":41.7911},"LostTags":4,"Light":"49115.368835522917","Temp":"32.93780098864795","TotalTags":186,"Status":"Offline","TransportationMode":"Land","BatteryLife":-5,"Tilt":"-52.64112596209344","Humidity":"74.018336518734131","Shock":"6.696957328744805","Pressure":"603.69265616418761","ActiveTags":170}
```
  ![Screen capture 1](/assets/images/Challenge2-Task2-Pic2.png)
  
  If you set the Nested levels to 2, ADX will “break” the JSON to independent fields.
  
  ![Screen capture 1](/assets/images/Challenge2-Task2-Pic2-2.png)
  
  For this microhack, we want to learn how ADX deals with dynamic fields, so we will keep the Nested levels as 1.
  
  ![Screen capture 1](/assets/images/Challenge2-Task2-Pic3.png)
  
  ![Screen capture 1](/assets/images/Challenge2-Task2-Pic4.png)
  
  Open the command viewer. You can see the control commands that were automatically generated.
  In contrast to Kusto queries, control commands are requests to Kusto to process or modify data or metadata. Control commands are distinguished from queries by having the first character in the text of the command be the dot (.) character (which can't start a query). Not all control commands modify data or metadata. The large class of commands that start with .show, are used to display metadata or data. For example, the .show tables command returns a list of all tables in the current database.
  
  Review the control commands that were generated by One-Click. 
  
  ![Screen capture 1](/assets/images/Challenge2-Task2-Pic5.png)
  
  You can see the:
  '.create table table_name command', which creates a new empty table.
 '.alter table table_name policy ingestionbatching', which alters the batching policy of the specified table. During the ingestion process, ADX optimizes for throughput by batching small ingress data chunks together before ingestion. Batching reduces the resources consumed by the ingestion process. The batching policy defines when to seal a batch and send it for the next stage of the ingestion (once the first condition is met):
  
  | Parameter | Value | Description |
  | --------- | ----- | ----------- |
  | Size | 1G | Batch size limit reached or exceeded the configured size |
  | Count | 1000 files | Batch file (blob) number limit reached the configured count |
  | Time | 5 minutes | The configured batching time has expired (maximum delay time per batch) (One click sets the batching time to 30 seconds) |

The table mapping (data mapping) command: Data mappings are used during ingestion to map incoming data to columns inside tables. In our case, the incoming data arrives from the event hub in JSON format. The table mapping maps the JSON fields (by describing the path to elements in a JSON document) to our table’s columns.
The desired result:

![Screen capture 1](/assets/images/Challenge2-Task2-Pic6.png)
  
  Click on ”Next: Summary” to create this data connection.

  The Azure portal opens on the Event Hubs Instance page, so you can monitor the Event hub’s outgoing messages. In addition, in the One click’s **Continuous ingestion from Event Hub established** window, all steps should be marked with green check marks when establishment finishes successfully. The cards below these steps give you options to explore your data with Quick queries or Monitor the Event Hub connections and data. 
  
  Use the “Take 10” link (under “Quick queries”). Review the query and the data.
  
  Notice that the query begins with a reference to the data table. This data is piped into the first and only operator in our query (take) and returns a specific number of arbitrary rows.
  Run the query by either selecting the Run button above the query window or selecting Shift+Enter on the keyboard.
  Use the “Manage Data Connection” link (under the “Monitor” section) to go to the portal and review your data connection. The data connection is saved under the Database.
  
  ![Screen capture 1](/assets/images/Challenge2-Task2-Pic7.png)

  ##### Task 3: Use the “One-click” UI (User Interfaces) to create a data connection to blob storage
  
  This time, we want to ingest data from an Azure Storage account. This storage account has the data on device’s telemetry history from 30th December 2021, device property changes and the commands executed on the device. 
  
  Go again to the “Data management” tab, and select the **Ingest from blob container** option under **Continuous ingestion**
  
  ![Screen capture 1](/assets/images/Challenge2-Task3-Pic1.png)
  
  Make sure the cluster and the Database fields are correct. Select **Create new table**
  
  ![Screen capture 1](/assets/images/Challenge2-Task3-Pic2.png)
  
  In the **Link to source**, paste the SAS URL of the blob storage (the proctors will provide this information). Then select one of the **Schema defining file** (all the files in that blob storage have the same schema) and click **Next**
  
  ![Screen capture 1](/assets/images/Challenge2-Task3-Pic3.png)
  
  Make sure you use the **JSON Data format**
  
  ![Screen capture 1](/assets/images/Challenge2-Task3-Pic4.png)
  
  Once the message **“Queueing blobs is complete”** appears, you can close this page. Then, you can click on the **Event Grid** link under **Continuous Ingestion**.

  ![Screen capture 1](/assets/images/Challenge2-Task3-Pic5.png)
  
  ![Screen capture 1](/assets/images/Challenge2-Task3-Pic6.png)
  
  ![Screen capture 1](/assets/images/Challenge2-Task3-Pic7.png)
  
  Repeat the above steps for ingesting data from the following blob containers: (the proctors will provide this information)
  - logistics-connectivity-commands
  - logistics-telemetry
  - logistics-properties

  Check the table
  ```
  LogisticsLifecycle
  | count 
  ```

  **Relevant docs for this challenge:**
  - [Azure Data Explorer data ingestion overview | Microsoft Docs](https://docs.microsoft.com/en-us/azure/data-explorer/ingest-data-overview)
  - [Use one-click ingestion to ingest data into Azure Data Explorer | Microsoft Docs](https://docs.microsoft.com/en-us/azure/data-explorer/ingest-data-one-click)
   
  
#### Challenge 3: Explore and transform data
  
**Expected Learning Outcomes:**
- Write simple queries using Kusto Query Language
- Create update policies to transform the data at ingestion
  
  
##### Task 1: Explore the data
  
  Kusto queries can be used to filter data and return specific information. Recall that you've looked at arbitrary rows of data to get a sense of its structure. Now, you'll learn how to choose specific rows of the data. The where operator filters results that satisfy a certain condition.

  ```
LogisticsTelemetry
| where deviceId startswith "x"
| take 10
  ```

Similarly, you can filter where the time of an event occurred more than a certain number of years/days/minutes ago. For example, run the following query, where 2m means 2 minutes:

  ```
LogisticsTelemetry
| where enqueuedTime > ago(2m)
| take 10 
  ```

Find out how many records are in the table

  ```
LogisticsTelemetry
| summarize count() // or: count
  ```

Find out how many records have enqueuedTime bigger than the last 10 minutes.

  ```
LogisticsTelemetry
| where enqueuedTime > ago(10m)
| summarize count()
 ``` 
 
Find out how many records startswith "x" 

```
LogisticsTelemetry
| where deviceId startswith "x"
| summarize count()
```
  
Find out how many records start with "x" , per device ID (aggregate by device ID)

```
LogisticsTelemetry
| where deviceId startswith "x"
| summarize count() by deviceId
```
  
Find out how many records startswith "x" , per device ID (aggregate by device ID). Render a timechart

```
LogisticsTelemetry
| where deviceId startswith "x"
| summarize count() by deviceId
| render piechart 
```


```
LogisticsTelemetry
| where deviceId startswith "x"
| summarize count() by deviceId
| render piechart 
```

```
LogisticsTelemetry
| extend h = telemetry.Humidity
| summarize avg(toint(h)) by bin(enqueuedTime, 1m)
| render timechart 
```


  ##### Task 2: Create an update policy
  
  By taking 10 records, we can see that the telemetry column has a JSON structure. In this task we will use update policy to manipulate the raw data in the LogisticsTelemetry table (the source table) and transform the JSON data into separate columns in a new table that we’ll create (“target table”).
We want to create a new table, with a calculated column (we will call it: NumOfTagsCalculated) that contains the following value: telemetry.TotalTags + telemetry.TotalTags - telemetry.LostTags.
The schema of the new table would be:
```
  ( deviceId:string, enqueuedTime:datetime, NumOfTagsCalculated:int, Temp:real)
```
  
  ![Screen capture 1](/assets/images/Challenge3-Task2-Pic1.png)
  
  Example (note that the order of the keys may be different):
  
  ```
{
    "BatteryLife": 73,
    "Light": "70720.236143472212",
    "Tilt": "18.608539012789223",
    "Humidity": "60.178854625386215",
    "Shock": "-4.6141182265359628",
    "Pressure": "529.61165751122712",
    "ActiveTags": 165,
    "TransportationMode": "Ocean",
    "Status": "Online",
    "LostTags": 9,
    "Temp": "7.5054504554744765",
    "TotalTags": 185,
    "Location": {
        "alt": 1361.0469,
        "lon": -107.7473,
        "lat": 36.0845
    }
}
```
  
  **Build the target table**
  
  **Create a function for the update policy**
    
  **Create the update policy**


  
**Relevant docs for this challenge:**
  - [Kusto update policy - Azure Data Explorer | Microsoft Docs](https://docs.microsoft.com/en-us/azure/data-explorer/kusto/management/updatepolicy)
    
#### Challenge 4: Check stats and key metrics of the cluster
    
  ##### Task 1:  #####
  Go to the Insights blade in the portal (in the ADX cluster page, under monitoring). This blade provides comprehensive monitoring of your clusters by delivering a unified view of your cluster performance, operations, usage, and ingestion operations.
  
  ##### Task 2: #####
  Go to the Overview tab: It provides metrics tiles that highlight the availability and overall status of the cluster for quick health assessment. A summary of active Azure Advisor recommendations and resource health status. Charts that show the top CPU and memory consumers and the number of unique users over time.
  
  ##### Task 3: #####
  Go to the Key Metrics tab: It shows a unified view of some of the cluster's metrics. They're grouped into general metrics, query-related metrics, ingestion-related metrics, and streaming ingestion-related metrics.
  
  ##### Task 4: #####
  Go to The Ingestion tab: It provides details about the ingestion operations, including the result of your ingestion attempts (per DB of per table), the latency of the ingestion process, and more.
  
  ##### Task 5: #####
  Go to the Overview tab: You can stop the cluster to save compute costs. You will not lose any data. ADX persists data on blob storage. When you restart your cluster, it will take few minutes to startup and warm up the cache before you can start writing the queries. When the cluster has been stopped, no continuous ingestion will be performed.
  
**Relevant docs for this challenge:**
  - [Monitor Azure Data Explorer performance, health & usage with metrics | Microsoft Docs](https://docs.microsoft.com/en-us/azure/data-explorer/using-metrics)

## Contributing

This project welcomes contributions and suggestions.  Most contributions require you to agree to a
Contributor License Agreement (CLA) declaring that you have the right to, and actually do, grant us
the rights to use your contribution. For details, visit https://cla.opensource.microsoft.com.

When you submit a pull request, a CLA bot will automatically determine whether you need to provide
a CLA and decorate the PR appropriately (e.g., status check, comment). Simply follow the instructions
provided by the bot. You will only need to do this once across all repos using our CLA.

This project has adopted the [Microsoft Open Source Code of Conduct](https://opensource.microsoft.com/codeofconduct/).
For more information see the [Code of Conduct FAQ](https://opensource.microsoft.com/codeofconduct/faq/) or
contact [opencode@microsoft.com](mailto:opencode@microsoft.com) with any additional questions or comments.

## Trademarks

This project may contain trademarks or logos for projects, products, or services. Authorized use of Microsoft 
trademarks or logos is subject to and must follow 
[Microsoft's Trademark & Brand Guidelines](https://www.microsoft.com/en-us/legal/intellectualproperty/trademarks/usage/general).
Use of Microsoft trademarks or logos in modified versions of this project must not cause confusion or imply Microsoft sponsorship.
Any use of third-party trademarks or logos are subject to those third-party's policies.
