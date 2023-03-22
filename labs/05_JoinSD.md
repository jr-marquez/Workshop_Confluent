### Description
To use Stream Designer for creating pipelines, the following prerequisites must be in place.

* A Schema Registry cluster
* A ksqlDB cluster
* Sufficient permissions to create pipelines

**IMPORTANT** YOU CAN GO TO STEP 4 IF YOU ALREADY DID THE PREVIOUS EXERCISE

### Step 1: Create a pipeline project

A Stream Designer pipeline project defines all the components that are deployed for an application

1. Log in to the Confluent Cloud Console and open the Cluster Overview page for the cluster you want to use for creating pipelines.

2. In the navigation menu, click **Stream Designer.**

3. Click **Create pipeline**, and in the Create new pipeline dialog, enter **join-pipe** for the pipeline name. The engine icon

4. In the ksqlDB Cluster dropdown, select the ksqlDB cluster to use for your pipeline logic. (please create a ksqldb app)

5. Click **Create pipeline.**

6. Click **Grant pipeline privileges.**

7. In the Grant activation privileges dialog, type “confirm” and click Confirm.
  * The Activate pipeline and Deactivate pipeline buttons appear.

### Step 2: Create a connector definition

Your pipeline starts with data produced by the Datagen source connector. In this step, you create a pipeline definition for a connector that produces mock pageviews data to a Kafka topic

Click **Start with connector** and **Start building.**

The Stream Designer canvas opens with a Source Connector component.

1. In the **Source Connector** component, click **Configure.**

2. In the search box, enter “datagen”.

3. Click the Datagen Source tile to open the Configuration page.

4. In the **Topic** textbox, type “pageviews_topic”.

5. Click **Continue** to open Kafka credentials page.

6. Ensure that the the Global access tile is selected and click Generate a Kafka API key & secret to create the API key for the Datagen connector.

7. Click **Continue** to configure the connector’s output.

8. In the Select output record value format section, click **JSON_SR,** and in the Select a template section, click **Pageviews.**

9. Click **Continue** to open the Sizing page.

10. In the Connector sizing section, leave the minimum number of tasks at **1.**

11. Click **Continue** to open the Review and launch page.

12. In the Connector name textbox, enter “Datagen_pageviews” and click **Continue.**

The Datagen source connector is configured and appears on the canvas with a corresponding topic component. The topic component is configured with the name you provided during connector configuration.

### Step 3: Register a stream on the topic

Stream Designer enables registering a stream on an underlying topic.

1. In the Stream component, click **Configure** to open the stream configuration dialog.

2. In the Name textbox, enter “pageviews_stream”.

3. In the **Value Format** dropdown, select **JSON_SR.**

4. Click **Save.**

The Topic component updates with the stream name.

### Step 4: Create a users connector definition

In this step, you create a pipeline definition for a connector that produces mock user data to a Kafka topic

In the Components menu, click **Source Connector.**

1. In the **Source Connector** component, click **Configure.**

The Source **Connector** page opens.

2. In the search box, enter “datagen”.

3. Click the **Datagen Source** tile to open the **Configuration** page.

4. In the **Topic** textbox, type “users_topic”.

5. Click **Continue.**

The Kafka credentials page opens.

6. Ensure that the the **Global access** tile is selected and click **Generate a Kafka API key & secret** to create the API key for the Datagen connector.

7. Click **Continue.**

8. In the **Select output record value format** section, click **JSON**, and in the **Select a template** section, click **Users.**

9. Click **Continue.**

10. In the Connector sizing section, leave the minimum number of tasks at **1** and click Continue.

11. In the **Connector name** textbox, enter “Datagen_users” and click **Continue.**

The Datagen source connector is configured and appears on the canvas with a corresponding topic component. The topic component is configured with the name you provided during connector configuration.

### Step 5: Register a table on the users topic

Stream Designer enables registering a table on an underlying topic. In this step, you create a table named “users_table” that corresponds with “users_topic”.

1. Right-click the **Stream** component and click **Remove.**

2. Hover over the **Topic** component and click the **+** icon that appears near the center.

A context menu opens.

3. In the context menu, click **Table.**

A Table component appears within the Topic component.

4. In the **Table** component, click **Configure.**

The Table Configuration dialog opens.

5. In the **Name** textbox, enter “users_table”.

6. In the **Columns for the table** textbox, enter the following SQL:

```bash
id VARCHAR PRIMARY KEY
```

7. Click **Add Columns for the table** and in the textbox, enter the following SQL

```bash
REGISTERTIME BIGINT
```
8. Repeat the previous step for the following column definitions:

```bash
USERID STRING
REGIONID STRING
GENDER STRING
```
9. Click **Save.**
The Topic component updates with the table name.

### Step 6: Join the stream and table

With pageviews_stream and users_table defined, you can join them to produce an enriched stream that has pageviews per user messages.

1. In the Components menu, click **Join.**
A Join query appears on the canvas.

2. In the query component, click **Configure** and name the query “user-pageviews join”.

3. From users_table, drag an arrow to the **Join** component.

4. From pageviews_stream, drag an arrow to the **Join** component.

5. In the **Join** component, click **Configure.**
The Join Configuration dialog opens.

6. In the Name textbox, enter “user pageviews”.

7. In the **Reference to the left input source** dropdown, Select pageviews_stream.

8. In the **Alias of the left input source field**, enter “p”.

9. In the **Reference to the input source** dropdown, select users_table.

10. In the **Alias of the input source field**, enter “u”.

11. In the **Join Type** dropdown, select LEFT OUTER.

12. In the **Join on clause** textbox, enter the following SQL:

```bash
p.userid = u.id
```
13. Click **Save**

The query component displays a red error triangle because it requires a stream, table, or another query component for its output. In the next step, you add a sink topic with a corresponding stream for the filter outpu

### Step 7: Define the join topic

The join query requires a sink topic for the joined messages. In this step, you define a user_pageviews topic for the query results.

1. Hover over the Join component and click **+**.

2. In the context menu, click **Stream.**
A Topic component appears.

3. In the **Topic** component, click **Configure** and name the topic “user_pageviews_topic”. Click **Save.**

4. In the **Stream** component, click **Configure** and name the stream “user_pageviews”. Click **Save.**

### Step 8: Activate the pipeline

1. Click **Activate** to deploy the pipeline components.

After a few seconds, the state of each component goes from Activating to Activated.

< If the filter component reports an error like Did not find any value schema for the topic, wait for the Datagen source connector to provision completely and activate the pipeline again.>

When all components are activated, your pipeline is deployed.

End of lab

[go back to Agenda](https://github.com/jr-marquez/Workshop_Confluent/blob/main/README.md#confluent-hands-on-workshop)


