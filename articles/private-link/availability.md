---
title: Azure Private Link beschikbaarheid
description: In dit artikel leert u welke Azure-services ondersteuning bieden Private Link.
author: asudbring
ms.author: allensu
ms.service: private-link
ms.topic: conceptual
ms.date: 3/15/2021
ms.custom: template-concept,references_regions
ms.openlocfilehash: 866eb9feb152c0094cd5281fe4820ccc4589386f
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107778315"
---
# <a name="azure-private-link-availability"></a>Azure Private Link beschikbaarheid

Met Azure Private Link hebt u via een [privé-eindpunt](private-endpoint-overview.md) in uw virtuele netwerk toegang tot Azure PaaS-services (bijvoorbeeld Azure Storage en SQL Database) en in Azure gehoste services van klanten of partners. 

> [!IMPORTANT]
> Azure Private Link is nu algemeen beschikbaar. Zowel privé-eindpunten als de Private Link-service (service achter standaard load balancer) zijn algemeen beschikbaar. Met verschillende Azure PaaS wordt de Azure Private Link volgens verschillende planningen uitgevoerd. Zie [Privé-eindpunt](private-endpoint-overview.md#limitations) en [Private Link-service](private-link-service-overview.md#limitations) voor bekende beperkingen. 

## <a name="service-availability"></a>Beschikbaarheid van de service

In de volgende tabellen worden de Private Link services en de regio's waar ze beschikbaar zijn weergegeven. 

### <a name="ai--machine-learning"></a>AI en Machine Learning

|Ondersteunde services  |Beschikbare regio's | Andere overwegingen | Status  |
|:-------------------|:-----------------|:----------------|:--------|
|Azure Machine Learning | Alle openbare regio's    |  | Algemene beschikbaarheid   <br/> [Meer informatie over het maken van een privé-eindpunt voor Azure Machine Learning.](../machine-learning/how-to-configure-private-link.md)   |

### <a name="analytics"></a>Analyse

|Ondersteunde services  |Beschikbare regio's | Andere overwegingen | Status  |
|:-------------------|:-----------------|:----------------|:--------|
|Azure Synapse Analytics| Alle openbare regio's <br/> Alle Government-regio's |  Ondersteund voor [proxy-verbindingsbeleid](../azure-sql/database/connectivity-architecture.md#connection-policy) |Algemene beschikbaarheid <br/> [Meer informatie over het maken van een privé-eindpunt voor Azure Synapse Analytics.](../azure-sql/database/private-endpoint-overview.md)|
|Azure Event Hub | Alle openbare regio's<br/>Alle Government-regio's      |   | Algemene beschikbaarheid   <br/> [Meer informatie over het maken van een privé-eindpunt voor Azure Event Hub.](../event-hubs/private-link-service.md)  |
| Azure Monitor <br/>(Log Analytics en Application Insights) | Alle openbare regio's      |  | Algemene beschikbaarheid   <br/> [Meer informatie over het maken van een privé-eindpunt voor Azure Monitor.](../azure-monitor/logs/private-link-security.md)   |
|Azure Data Factory | Alle openbare regio's<br/> Alle Government-regio's<br/>Alle Chinese regio's    | Referenties moeten worden bewaard in een Azure-sleutelkluis| Algemene beschikbaarheid   <br/> [Meer informatie over het maken van een privé-eindpunt voor Azure Data Factory.](../data-factory/data-factory-private-link.md)   |

### <a name="compute"></a>Compute

|Ondersteunde services  |Beschikbare regio's | Andere overwegingen | Status  |
|:-------------------|:-----------------|:----------------|:--------|
|Azure App Configuration | Alle openbare regio's      |  | Preview  </br> [Meer informatie over het maken van een privé-eindpunt voor Azure App Configuration](../azure-app-configuration/concept-private-endpoint.md) |
|Door Azure beheerde schijven | Alle openbare regio's<br/> Alle Government-regio's<br/>Alle Chinese regio's    | [Selecteer voor bekende beperkingen](../virtual-machines/disks-enable-private-links-for-import-export-portal.md#limitations) | Algemene beschikbaarheid   <br/> [Meer informatie over het maken van een privé-eindpunt voor Azure Managed Disks.](../virtual-machines/disks-enable-private-links-for-import-export-portal.md)   |

### <a name="containers"></a>Containers

|Ondersteunde services  |Beschikbare regio's | Andere overwegingen | Status  |
|:-------------------|:-----------------|:----------------|:--------|
|Azure Container Registry | Alle openbare regio's<br/> Alle Government-regio's    | Ondersteund met de Premium-laag van Container Registry. [Selecteren voor lagen](../container-registry/container-registry-skus.md)| Algemene beschikbaarheid   <br/> [Meer informatie over het maken van een privé-eindpunt voor Azure Container Registry.](../container-registry/container-registry-private-link.md)   |
|Azure Kubernetes Service - Kubernetes API | Alle openbare regio's      |  | Algemene beschikbaarheid   <br/> [Meer informatie over het maken van een privé-eindpunt voor Azure Kubernetes Service.](../aks/private-clusters.md)   |

### <a name="databases"></a>Databases

|Ondersteunde services  |Beschikbare regio's | Andere overwegingen | Status  |
|:-------------------|:-----------------|:----------------|:--------|
|  Azure SQL Database         | Alle openbare regio's <br/> Alle Government-regio's<br/>Alle Chinese regio's      |  Ondersteund voor [proxy-verbindingsbeleid](../azure-sql/database/connectivity-architecture.md#connection-policy) | Algemene beschikbaarheid <br/> [Meer informatie over het maken van een privé-eindpunt voor Azure SQL](create-private-endpoint-portal.md)      |
|Azure Cosmos DB|  Alle openbare regio's<br/> Alle Government-regio's</br> Alle Chinese regio's | |Algemene beschikbaarheid <br/> [Meer informatie over het maken van een privé-eindpunt voor Cosmos DB.](./tutorial-private-endpoint-cosmosdb-portal.md)|
|  Azure Database for PostgreSQL - één server         | Alle openbare regio's <br/> Alle Government-regio's<br/>Alle Chinese regio's     | Prijscategorieën Ondersteund voor algemeen gebruik en Geoptimaliseerd voor geheugen | Algemene beschikbaarheid <br/> [Meer informatie over het maken van een privé-eindpunt voor Azure Database for PostgreSQL.](../postgresql/concepts-data-access-and-security-private-link.md)      |
|  Azure Database for MySQL         | Alle openbare regio's<br/> Alle Government-regio's<br/>Alle Chinese regio's      |  | Algemene beschikbaarheid <br/> [Meer informatie over het maken van een privé-eindpunt voor Azure Database for MySQL.](../mysql/concepts-data-access-security-private-link.md)     |
|  Azure Database for MariaDB         | Alle openbare regio's<br/> Alle Government-regio's<br/>Alle Chinese regio's     |  | Algemene beschikbaarheid <br/> [Meer informatie over het maken van een privé-eindpunt voor Azure Database for MariaDB.](../mariadb/concepts-data-access-security-private-link.md)      |

### <a name="integration"></a>Integratie

|Ondersteunde services  |Beschikbare regio's | Andere overwegingen | Status  |
|:-------------------|:-----------------|:----------------|:--------|
|Azure Event Grid| Alle openbare regio's<br/> Alle Government-regio's       |  | Algemene beschikbaarheid   <br/> [Meer informatie over het maken van een privé-eindpunt voor Azure Event Grid.](../event-grid/network-security.md) |
|Azure Service Bus | Alle openbare regio's<br/>Alle Government-regio's  | Ondersteund met de Premium-laag van Azure Service Bus. [Selecteren voor lagen](../service-bus-messaging/service-bus-premium-messaging.md) | Algemene beschikbaarheid   <br/> [Meer informatie over het maken van een privé-eindpunt voor Azure Service Bus.](../service-bus-messaging/private-link-service.md)    |

### <a name="internet-of-things-iot"></a>Internet der dingen (IoT)

|Ondersteunde services  |Beschikbare regio's | Andere overwegingen | Status  |
|:-------------------|:-----------------|:----------------|:--------|
| Azure IoT Hub | Alle openbare regio's    |  | Algemene beschikbaarheid   <br/> [Meer informatie over het maken van een privé-eindpunt voor Azure IoT Hub.](../iot-hub/virtual-network-support.md) |
|  Azure Digital Twins         | Alle openbare regio's die worden ondersteund door Azure Digital Twins     |  | Preview <br/> [Meer informatie over het maken van een privé-eindpunt voor Azure Digital Twins.](../digital-twins/how-to-enable-private-link-portal.md)      |

### <a name="management-and-governance"></a>Beheer en governance

| Ondersteunde services | Beschikbare regio's | Andere overwegingen | Status  |
| ------------ | ----------------| ------------| ----------------|
| Azure Automation  | Alle openbare regio's<br/> Alle Government-regio's |  | Preview </br> [Meer informatie over het maken van een privé-eindpunt voor Azure Automation.](../automation/how-to/private-link-security.md)|
|Azure Backup | Alle openbare regio's<br/> Alle Government-regio's   |  | Algemene beschikbaarheid <br/> [Meer informatie over het maken van een privé-eindpunt voor Azure Backup.](../backup/private-endpoints.md)   |

### <a name="security"></a>Beveiliging

|Ondersteunde services  |Beschikbare regio's | Andere overwegingen | Status  |
|:-------------------|:-----------------|:----------------|:--------|
|  Azure Key Vault         | Alle openbare regio's<br/> Alle Government-regio's      |  | Algemene beschikbaarheid   <br/> [Meer informatie over het maken van een privé-eindpunt voor Azure Key Vault.](../key-vault/general/private-link-service.md)   |

### <a name="storage"></a>Storage
|Ondersteunde services  |Beschikbare regio's | Andere overwegingen | Status  |
|:-------------------|:-----------------|:----------------|:--------|
| Azure Blob Storage (inclusief Data Lake Storage Gen2)       |  Alle openbare regio's<br/> Alle Government-regio's       |  Ondersteund op Account Kind General Purpose V2 | Algemene beschikbaarheid <br/> [Meer informatie over het maken van een privé-eindpunt voor Blob Storage.](tutorial-private-endpoint-storage-portal.md)  |
| Azure Files | Alle openbare regio's<br/> Alle Government-regio's      | |   Algemene beschikbaarheid <br/> [Meer informatie over het maken van Azure Files-netwerkeindpunten.](../storage/files/storage-files-networking-endpoints.md)   |
| Azure File Sync | Alle openbare regio's      | |   Algemene beschikbaarheid <br/> [Meer informatie over het maken van Azure Files-netwerkeindpunten.](../storage/file-sync/file-sync-networking-endpoints.md)   |
| Azure Queue Storage       |  Alle openbare regio's<br/> Alle Government-regio's       |  Ondersteund op Account Kind General Purpose V2 | Algemene beschikbaarheid <br/> [Meer informatie over het maken van een privé-eindpunt voor Queue Storage.](tutorial-private-endpoint-storage-portal.md) |
| Azure Table Storage       |  Alle openbare regio's<br/> Alle Government-regio's       |  Ondersteund op Account Kind General Purpose V2 | Algemene beschikbaarheid <br/> [Meer informatie over het maken van een privé-eindpunt voor Table Storage.](tutorial-private-endpoint-storage-portal.md)  |
| Azure Batch | Alle openbare regio's, behalve: Duitsland CENTRAAL, Duitsland NOORDOOST <br/> Alle Government-regio's  | | Algemene beschikbaarheid <br/> [Meer informatie over het maken van een privé-eindpunt voor Azure Batch.](../batch/private-connectivity.md) |

### <a name="web"></a>Web
|Ondersteunde services  |Beschikbare regio's | Andere overwegingen | Status  |
|:-------------------|:-----------------|:----------------|:--------|
| Azure SignalR | VS - OOST, VS - ZUID-CENTRAAL,<br/>VS - WEST 2, alle Chinese regio's      |  | Preview   <br/> [Meer informatie over het maken van een privé-eindpunt voor Azure SignalR.](../azure-signalr/howto-private-endpoints.md)   |
|Azure Web Apps | Alle openbare regio's<br/> China - noord 2 & East 2    | Ondersteund met een PremiumV2-, PremiumV3- of Function Premium-abonnement  | Algemene beschikbaarheid   <br/> [Meer informatie over het maken van een privé-eindpunt voor Azure Web Apps.](./tutorial-private-endpoint-webapp-portal.md)   |
|Azure Search | Alle openbare regio's <br/> Alle Government-regio's | Ondersteund met service in de privémodus | Algemene beschikbaarheid   <br/> [Meer informatie over het maken van een privé-eindpunt voor Azure Search.](../search/service-create-private-endpoint.md)    |
|Azure Relay | Alle openbare regio's      |  | Preview <br/> [Meer informatie over het maken van een privé-eindpunt voor Azure Relay.](../azure-relay/private-link-service.md)  |

### <a name="private-link-service-with-a-standard-load-balancer"></a>Private Link service met een load balancer

|Ondersteunde services  |Beschikbare regio's | Andere overwegingen | Status  |
|:-------------------|:-----------------|:----------------|:--------|
|Private Link-services achter standaard Azure Load Balancer | Alle openbare regio's<br/> Alle Government-regio's<br/>Alle Chinese regio's  | Ondersteund op Standard Load Balancer | Algemene beschikbaarheid <br/> [Meer informatie over het maken van een Private Link-service.](create-private-link-service-portal.md) |

## <a name="next-steps"></a>Volgende stappen

Meer informatie over Azure Private Link service:
- [Wat is Azure Private Link?](private-link-overview.md)
- [Een privé-eindpunt maken met behulp van de Azure-portal](create-private-endpoint-portal.md)
