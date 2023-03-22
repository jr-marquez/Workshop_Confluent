### Description
To use Stream Designer for creating pipelines, the following prerequisites must be in place.

* A Schema Registry cluster
* A ksqlDB cluster
* Sufficient permissions to create pipelines

### Step 1: Create a pipeline project

A Stream Designer pipeline project defines all the components that are deployed for an application

1. Log in to the Confluent Cloud Console and open the Cluster Overview page for the cluster you want to use for creating pipelines.

2. In the navigation menu, click **Stream Designer.**

3. Click **Create pipeline**, and in the Create new pipeline dialog, enter **agg-pipe** for the pipeline name. The engine icon

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

8. In the Select output record value format section, click **JSON_SR,** and in the Select a template section, click **Clickstream.**

9. Click **Continue** to open the **Sizing** page.

10. In the Connector sizing section, leave the minimum number of tasks at **1.**

11. Click **Continue** to open the Review and launch page.

12. In the Connector name textbox, enter “Datagen_clickstream” and click **Continue.**

The Datagen source connector is configured and appears on the canvas with a corresponding topic component. The topic component is configured with the name you provided during connector configuration.

### Step 3: Register a stream on the topic

Stream Designer enables registering a stream on an underlying topic.

1. In the Stream component, click **Configure** to open the stream configuration dialog.

2. In the Name textbox, enter "CLICKSTREAM".

3. In the **Value Format** dropdown, select **JSON_SR.**

4. Click **Save.**

The Topic component updates with the stream name.

### Step 4: Create a filter query definition

Stream Designer enables defining queries on your streams. In this step, you create the definition for a filter component and specify its behavior by using a simple SQL statement. The filter produces a stream of click events that have a status code greater than 400.

1. Hover over the **CLICKSTREAM** component and click **+.**

A context menu appears showing the components that accept a stream as input.

2. In the context menu, click **Filter.**

A Query component appears

3. In the **Query** component, click **Configure** and name the query “error detection query”. Click **Save.**

4. In the **Filter** component, click **Configure.**

5. In the **Configuration** dialog, name the filter “status filter”.

6. In the **Filter Expression** field, enter the following SQL:

```bash
(CAST(CLICKSTREAM.STATUS AS INT) > 400)
```

7. Click **Save** to create the filter definition.

The query component has an error state because it needs a sink topic or another query for its output, which you provide in the next step.

### Step 5: Create a GROUP BY query definition

The next stage of the pipeline groups click messages by their status code, within a 60-second hopping window.

1. Hover over the **status filter** component and click **+.**

A context menu appears showing the components that accept a stream as input.

2. In the context menu, click **Group By.**

A Group By component appears in the query.

3. In the Group By component, click **Configure.**

4. In the **Name** textbox, enter “group by status”.

5. In the **Group by Expression** textbox, enter “CLICKSTREAM.STATUS”

6. Click the **Window** dropdown and select HOPPING.

Fields for the hopping window parameters appear.

7. Configure the window with the following values:
  * Window Size Duration: 60
  * Window Size Unit: seconds
  * Advance By Duration: 20
  * Advance By Unit: seconds

8. Click **Save.**

### Step 6: Create a HAVING query definition

The next stage of the pipeline extracts clicks from an aggregation that has more than 5 of the same type of error within the time window.

1. Hover over the **Group By** component and click +.

A context menu appears showing the components that accept grouped input.

2. In the context menu, click **Having.**

A Having component appears in the query.

3. In the Having component, click **Configure.**

4. In the **Name** textbox, enter “having more than 5”.

5. In the **Having Expression** textbox, enter the following SQL:

```bash
(COUNT(*) > 5) AND (COUNT(*) IS NOT NULL)
```

6. Click **Save.**

### Step 7: Create a mapping definition

In this step, you define a projection that maps columns to an output table.

1. Hover over the **Having** component and click **+.**

A context menu appears showing the components that accept HAVING input.

2. In the context menu, click **Project.**

A **Project** component appears in the query.

3. In the **Project** component, click **Configure.**

4. In the **Name** textbox, enter “error mappings”.

5. In the **Projection Expression** textbox, enter the following SQL, which defines the output table’s key column to be the status code.

```bash
STATUS AS K1
```
6. Click **Add Projection Expression** and in the textbox, enter the following SQL:

```bash
AS_VALUE(STATUS) AS STATUS
```
7. Click **Add Projection Expression** and in the textbox, enter the following SQL:

```bash
COUNT(*) AS ERRORS
```

8. Click **Add Projection Expression** and in the textbox, enter the following SQL:

```bash
WINDOWSTART AS EVENT_TS
```
9. Click **Save.**

The query is definition is complete. The query component shows an error state because it needs a sink topic for its output, which you provide in the next step.

### Step 8: Create an error summary table definition

The last step is to create a table from the projected columns that reports the error counts within the window.

1. Hover over the **Project** component and click +.

A context menu appears showing the components that accept mapping input.

2. In the context menu, click **Table.**

A Table component appears on the canvas.

3. In the **Topic** component, click **Configure.**

4. In the **Name** textbox, enter “errors_per_minute_alert” and click **Save.**

5. In the **Table** component, click **Configure.**

6. In the **Name** textbox, enter “errors_per_minute_alert” and click Save.

The aggregation pipeline is ready to activate.

### Step 9: Activate the pipeline

1. Click **Activate** to deploy the pipeline components.

After a few seconds, the state of each component goes from Activating to Activated.

< If the filter component reports an error like Did not find any value schema for the topic, wait for the Datagen source connector to provision completely and activate the pipeline again.>

2. When all components show the **Activated** state, click the **errors_per_minute** topic, and in the details view, click **Messages.**

The aggregated status messages appear, showing the number of errors and the timestamp of the window in which the messages were aggregated.


End of lab

[go back to Agenda](https://github.com/jr-marquez/Workshop_Confluent/blob/main/README.md#confluent-hands-on-workshop)


