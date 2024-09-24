---
title: Add Apache Kafka as source in Real-Time hub
description: This article describes how to add Apache Kafka as an event source in Fabric Real-Time hub.
author: ahartoon
ms.author: anboisve
ms.topic: how-to
ms.custom:
  - references_regions
ms.date: 09/15/2024
---

# Add Apache Kafka as source in Real-Time hub (preview)
This article describes how to add Apache Kafka as an event source in Fabric Real-Time hub. 

[!INCLUDE [preview-note](./includes/preview-note.md)]

## Prerequisites 

- Get access to the Fabric **premium** workspace with **Contributor** or above permissions. 
- An Apache Kafka cluster running. 
- Your Apache Kafka must be publicly accessible and not be behind a firewall or secured in a virtual network.  

[!INCLUDE [launch-get-events-experience](./includes/launch-get-events-experience.md)]

## Add Apache Kafka as a source

[!INCLUDE [apache-kafka-connector](../real-time-intelligence/event-streams/includes/apache-kafka-source-connector.md)]

## View data stream details

1. On the **Review and create** page, if you select **Open eventstream**, the wizard opens the eventstream that it created for you with the selected Apache Kafka source. To close the wizard, select **Close** at the bottom of the page. 
1. In Real-Time hub, switch to the **Data streams** tab of Real-Time hub. Refresh the page. You should see the data stream created for you.

    For detailed steps, see [View details of data streams in Fabric Real-Time hub](view-data-stream-details.md).
 
## Related content
To learn about consuming data streams, see the following articles:

- [Process data streams](process-data-streams-using-transformations.md)
- [Analyze data streams](analyze-data-streams-using-kql-table-queries.md)
- [Set alerts on data streams](set-alerts-data-streams.md)