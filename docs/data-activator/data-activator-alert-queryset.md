---
title: Create Data Activator alerts from a KQL Queryset
description: Learn how to create a Data Activator alert from the results of a KQL query in a KQL Queryset in Real-Time Intelligence.
author: yaelschuster
ms.author: yaschust
ms.reviewer: guregini
ms.topic: how-to
ms.date: 09/24/2024
ms.search.form: KQL Queryset
# CustomerIntent: As a customer, I want to learn how to create Data Activator alerts from a KQL Queryset so that I can trigger notifications when conditions are met on data in the query result.
---
# Create Data Activator alerts from a KQL Queryset (preview)

This article explains how to create Data Activator alerts from a KQL Queryset. For more information, see [What is Data Activator](data-activator-introduction.md).
You can use Data Activator on a KQL Queryset to trigger notifications in two modes: when a scheduled KQL query returns results, or when a scheduled KQL query result that contains a visualization meets a defined set of conditions. You can send alert notifications either to yourself, or to others in your organization. Notifications can be sent by email or Microsoft Teams message.

[!INCLUDE [feature-preview-note](../includes/feature-preview-note.md)]

## Sample scenarios

Here are some ways you can use Data Activator alerts with KQL queries:

* Suppose you have a KQL database and are storing application logs.
    * You receive an alert when any records from the last 5 minutes contain the string `authorization error` in the table’s *message* column.
* In a different scenario, you have streaming data for available bicycles in different neighborhoods. A KQL query is created to render a piechart for the number of available bicycles per neighborhood.
    * You receive an alert when the number of available bicycles in any neighborhood falls below an acceptable number.

## Prerequisites

* A [workspace](../get-started/create-workspaces.md) with a Microsoft Fabric-enabled [capacity](../enterprise/licenses.md#capacity)
* A [KQL database](../real-time-intelligence/create-database.md) with data
* A [KQL Queryset](../real-time-intelligence/create-query-set.md) connected to the KQL database. For more information, see [Query data in a KQL queryset](../real-time-intelligence/kusto-query-set.md).

The following steps show you how to create an alert on a query that creates a visualization, or on a query that doesn't create a visualization.

Choose the tab that corresponds to your desired workflow.

## [With visualization](#tab/visualization)

## Set alert on a KQL Queryset

> [!IMPORTANT]
> [Timechart](/kusto/query/visualization-timechart?view=microsoft-fabric&preserve-view=true) visualizations are not supported in this scenario. They are supported in [Create Data Activator alerts from a Real-Time Dashboard](data-activator-get-data-real-time-dashboard.md).

1. Browse to your KQL Queryset.
1. Run a query that returns a visualization.
1. Once the query returns results, select **Set Alert** on the top ribbon.

    For example, the following query is based on the sample *Bicycles* data from the [Real-Time Intelligence tutorial](../real-time-intelligence/tutorial-introduction.md).

    ```kusto
    TutorialTable
    | where Timestamp < ago(5m)
    | summarize NumberOfBikes=sum(No_Bikes) by Neighbourhood
    | render columnchart
    ```

    The query returns a column chart that shows the number of bikes available in each neighborhood, and this chart is used to set alert conditions.

## Define alert conditions

1. Set a time frequency for how often the query is run. The default is 5 minutes.
1. In **Conditions**, specify your alert conditions as follows:
    * If your visualization has no dimensions, you can select the **On each event when** condition to monitor changes in the data stream by choosing a specific field to monitor.
    * If your visualization includes dimensions, you can select the **On each event grouped by** condition to monitor changes in the data stream by selecting a field for grouping, which divides the data into distinct groups
    * In the **When** dropdown, set the value to be evaluated.
    * In the **Condition** dropdown, set the condition to be evaluated. For more information, see [Conditions](data-activator-detection-conditions.md#conditions).
    * In the **Value** field, set the value to compare against.
1. In **Action**, specify whether you want your alert via email or Microsoft Teams. In the side pane, you can configure notifications that are sent to yourself. To send notifications to a different user, see [Optional: Edit your trigger in Data Activator](#optional-edit-your-trigger-in-data-activator).
1. In **Save location**, specify where to save your Data Activator alert. Choose an existing workspace, and save either in an existing Reflex item or a new one.
1. Select **Create** to create your Data Activator trigger.

:::image type="content" source="media/data-activator-alert-queryset/conditions-with-visualization.png" alt-text="Screenshot of the set alert pane in the KQL queryset for creating a Data Activator alert.":::

## [Without visualization](#tab/no-visualization)

## Set alert on a KQL Queryset

1. Browse to your KQL Queryset.
1. Run a query. Data Activator checks the results of this query according to the time frequency set in a later step, and sends an alert for each record returned in the result set. For example, if a scheduled query returns five records, Data Activator sends five alerts.
1. Once the query completes running, select **Set Alert** on the top ribbon.

### Example 1 - Single result when count is greater than threshold

For example, the following query returns an alert if there are more than *threshold* records in the table from the last 5 minutes. The last two lines of the query are key, in which the count of records matching the filters is created, and a result is returned only if the count is greater than the threshold.

```kusto
SampleTable 
| where ingestion_time() > ago (5min)
// Add any other optional filters
| count 
| where Count > threshold
```

### Example 2 - Create a single result with an array of several values

In the following example, the query returns an alert if the number of bicycles in any neighborhood is above the given threshold. In order to get a single alert for all neighborhoods for which the number is above the threshold, the query is built to return a single record (meaning, a single alert). This is done using the [make_list() operator](/kusto/query/make-list-aggregation-function?view=microsoft-fabric&preserve-view=true) To edit the alert to contain the list of the neighborhoods that reached the threshold, see [Optional: Edit your trigger in Data Activator](#optional-edit-your-trigger-in-data-activator).

```kusto
TableForReflex
| where ingestion_time() > ago (5min)
| summarize NeighborhoodCount = count() by Neighbourhood
| where NeighborhoodCount > threshold
| summarize NeighbourhoodList = make_list(Neighbourhood)
```

## Define alert conditions

Next, define your alert conditions. In the **Set Alert** pane that appears, take the following steps:

1. Set a time frequency for how often the query is run. The default is 5 minutes.
    The only condition available in this scenario is **On each event**, meaning that when any record is returned, the condition is met.
1. In **Action**, specify whether you want your alert via email or Microsoft Teams. In the side pane, you can configure notifications that are sent to yourself. To send notifications to a different user, see [Optional: Edit your trigger in Data Activator](#optional-edit-your-trigger-in-data-activator).
1. In **Save location**, specify where to save your Data Activator alert. Choose an existing workspace, and save either in an existing Reflex item or a new one.
1. Select **Create** to create your Data Activator trigger.

:::image type="content" source="media/data-activator-alert-queryset/conditions-without-visualization.png" alt-text="Screenshot of conditions being set on a query that doesn't include a visualization.":::

---

## Optional: Edit your trigger in Data Activator

When your Reflex item is saved, the side pane will display a link to your item. Select the link to further edit in Data Activator. This step can be useful if you want to do one of the following:

* Add other recipients to your alert.
* Change the content of the alert to reflect the specific data that triggered the alert.
* Define a more complex alert condition than is possible in the Set alert pane.

For information on how to edit triggers in Data Activator, see [Create triggers in design mode](data-activator-create-triggers-design-mode.md).

In the Reflex item itself, you can also view the history of the query results and the history of the trigger activations. For more information, see [Create Data Activator triggers in design mode](data-activator-create-triggers-design-mode.md).

<!-- ## Limitations on query result set that returns a time chart with a time axis

If you have a result set with a chart that has a time axis, Data Activator will read the measure value exactly once for each point on the time axis. For more information, see [Limitations on charts with a time axis](data-activator-get-data-real-time-dashboard.md#limitations-on-charts-with-a-time-axis).

To work around this limitation, you can add a line to the query so that the end time of the time filter ends at 'one bin before,' and the last bin does not change. 

```kusto
TableForReflex
| extend ingestionTime = ingestion_time()
| where ingestionTime between (startTime..bin(endTime, 10min))
| summarize count = count() by  bin(ingestionTime, 10min)
```
-->

## Related content

* [Query data in a KQL queryset](../real-time-intelligence/kusto-query-set.md)
* [Get started with Data Activator](data-activator-get-started.md)
* [KQL quick reference guide](/kusto/query/kql-quick-reference)