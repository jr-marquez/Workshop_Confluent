### Description
To use Stream Designer for creating pipelines, the following prerequisites must be in place.

* A Schema Registry cluster
* A ksqlDB cluster
* Sufficient permissions to create pipelines

### Step 1: Create a pipeline project

A Stream Designer pipeline project defines all the components that are deployed for an application

1. Log in to the Confluent Cloud Console and open the Cluster Overview page for the cluster you want to use for creating pipelines.

2. In the navigation menu, click Stream Designer.

3. Click Create pipeline, and in the Create new pipeline dialog, enter my-pipeline for the pipeline name.

4. In the ksqlDB Cluster dropdown, select the ksqlDB cluster to use for your pipeline logic. (please create a ksqldb app)

5. Click Create pipeline.

6. Click Grant pipeline privileges.

7. In the Grant activation privileges dialog, type “confirm” and click Confirm.
  * The Activate pipeline and Deactivate pipeline buttons appear.

### Step 2: Create a connector definition

Your pipeline starts with data produced by the Datagen source connector. In this step, you create a pipeline definition for a connector that produces mock pageviews data to a Kafka topic

Click **Start with connector** and **Start building.**

The Stream Designer canvas opens with a Source Connector component.

1. In the Source Connector component, click Configure.

2. In the search box, enter “datagen”.

3. Click the Datagen Source tile to open the Configuration page.

4. In the Topic textbox, type “pageviews_topic”.

5. Click Continue to open Kafka credentials page.

6. Ensure that the the Global access tile is selected and click Generate a Kafka API key & secret to create the API key for the Datagen connector.

7. Click Continue to configure the connector’s output.

8. In the Select output record value format section, click JSON_SR, and in the Select a template section, click Pageviews.

9. Click Continue to open the Sizing page.

10. In the Connector sizing section, leave the minimum number of tasks at 1.

11. Click Continue to open the Review and launch page.

12. In the Connector name textbox, enter “Datagen_pageviews” and click Continue.

The Datagen source connector is configured and appears on the canvas with a corresponding topic component. The topic component is configured with the name you provided during connector configuration.

### Step 3: Register a stream on the topic

Stream Designer enables registering a stream on an underlying topic.

1. In the Stream component, click Configure to open the stream configuration dialog.

2. In the Name textbox, enter “pageviews_stream”.

3. In the Value Format dropdown, select JSON_SR.

4. Click Save.

The Topic component updates with the stream name.

### Step 4: Create a transformation definition

Stream Designer enables defining transformations on your streams. In this step, you create the definition for a filter component and specify its behavior by using a simple SQL statement.

1. Hover over the pageviews_stream component and click +.

A context menu appears showing the components that accept a stream as input.

2. In the context menu, click Filter

A Query component appears.

3. In the Query component, click Configure and name the query “user 9 filter”. Click Save.

4. In the Filter component, click Configure.

5. In the Configuration dialog, name the filter “user 9”.

6. In the Filter Expression field, enter the following SQL:

```bash
userid = 'User_9'
```

7. Click Save to create the filter definition.

The query component displays a red error triangle because it requires a stream, table, or another query component for its output. In the next step, you add a sink topic with a corresponding stream for the filter output.

### Step 5: Create a filtered stream definition

The filter requires a stream, table, or another component for its output. The following steps show how to direct the filter’s output to a stream.

1. Hover over the user 9 filter and click +.

A context menu appears showing the components that accept a stream as input.

2. In the context menu, click Stream.

A Topic component appears on the canvas.

3. In the Topic component, click Configure. Name the topic “filtered_pageviews_topic” and click Save.

4. In the Stream component, click Configure

5. In the Configuration dialog, name the stream “filtered_pageviews” and click Save.

Your pipeline is ready for activation. The design canvas should resemble the following.

### Step 6: Activate the pipeline

1. Click Activate to deploy the pipeline components.

After a few seconds, the state of each component goes from Activating to Activated.

< If the filter component reports an error like Did not find any value schema for the topic, wait for the Datagen source connector to provision completely and activate the pipeline again.>

When all components are activated, your pipeline is deployed.

### Step 7: Inspect the pipeline

In this step, you confirm that messages are flowing from the source connector through all of the components to the sink topic

1. Click pageviews_topic, and in the details page, click Messages.

2. Close the pageviews_topic page.

3. Click the filtered_pageviews topic, and in the details page, click Messages.


