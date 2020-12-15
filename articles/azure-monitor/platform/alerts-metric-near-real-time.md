---
title: Ondersteunde resources voor metrische waarschuwingen in Azure Monitor
description: Naslag informatie over metrische gegevens en logboeken voor metrische waarschuwingen in Azure Monitor
author: harelbr
ms.author: harelbr
services: monitoring
ms.topic: conceptual
ms.date: 12/15/2020
ms.subservice: alerts
ms.openlocfilehash: 8f59f3488f6c8f5b35ec68d93db656447f882a92
ms.sourcegitcommit: 63d0621404375d4ac64055f1df4177dfad3d6de6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/15/2020
ms.locfileid: "97510679"
---
# <a name="supported-resources-for-metric-alerts-in-azure-monitor"></a>Ondersteunde resources voor metrische waarschuwingen in Azure Monitor

Azure Monitor ondersteunt nu een [nieuw type metrische waarschuwing](./alerts-overview.md) met aanzienlijke voor delen ten opzichte van de oudere [klassieke metrische waarschuwingen](./alerts-classic.overview.md). Metrische gegevens zijn beschikbaar voor een [grote lijst met Azure-Services](./metrics-supported.md). De nieuwere waarschuwingen bieden ondersteuning voor een deel verzameling van de resource typen. Dit artikel bevat een lijst met deze subset.

U kunt ook nieuwere metrische waarschuwingen gebruiken voor populaire logboek gegevens die zijn opgeslagen in een Log Analytics werk ruimte geëxtraheerd als metrische waarden. Bekijk [metrische waarschuwingen voor logboeken](./alerts-metric-logs.md)voor meer informatie.

## <a name="portal-powershell-cli-rest-support"></a>Portal, Power shell, CLI, REST-ondersteuning
Op dit moment kunt u alleen nieuwe metrische waarschuwingen maken in de sjablonen Azure Portal, [rest API](/rest/api/monitor/metricalerts/)of [Resource Manager](./alerts-metric-create-templates.md). Ondersteuning voor het configureren van nieuwere waarschuwingen met behulp van Power shell en Azure CLI versie 2,0 en hoger wordt binnenkort beschikbaar.

## <a name="metrics-and-dimensions-supported"></a>Ondersteunde metrische gegevens en dimensies
Nieuwere metrische waarschuwingen ondersteunen waarschuwingen voor metrische gegevens die gebruikmaken van dimensies. U kunt dimensies gebruiken om uw metrische gegevens te filteren op het juiste niveau. Alle ondersteunde metrische gegevens en de toepasselijke dimensies kunnen worden verkend en gevisualiseerd vanaf [Azure monitor-Metrics Explorer](./metrics-charts.md).

Dit is de volledige lijst met Azure Monitor metrische bronnen die worden ondersteund door de nieuwere waarschuwingen:

|Resourcetype  |Ondersteunde dimensies |Waarschuwingen voor meerdere resources| Beschik bare metrische gegevens|
|---------|---------|-----|----------|
|Micro soft. Aadiam/azureADMetrics | Ja | Nee | |
|Microsoft.ApiManagement/service | Ja | Nee | [API Management](./metrics-supported.md#microsoftapimanagementservice) |
|Micro soft. AppConfiguration/configurationStores |Ja | Nee | [App-configuratie](./metrics-supported.md#microsoftappconfigurationconfigurationstores) |
|Micro soft. AppPlatform/lente | Ja | Nee | [Azure Spring Cloud](./metrics-supported.md#microsoftappplatformspring) |
|Micro soft. Automation/automationAccounts | Ja| Nee | [Automation-accounts](./metrics-supported.md#microsoftautomationautomationaccounts) |
|Micro soft. AVS/privateClouds | Nee | Nee | |
|Microsoft.Bat-CH/batchAccounts | Ja | Nee | [Batchaccounts](./metrics-supported.md#microsoftbatchbatchaccounts) |
|Microsoft.Cache/Redis | Ja | Ja | [Azure Cache voor Redis](./metrics-supported.md#microsoftcacheredis) |
|Micro soft. ClassicCompute/domein naam/sleuven/rollen | Nee | Nee | [Klassieke Cloud Services](./metrics-supported.md#microsoftclassiccomputedomainnamesslotsroles) |
|Micro soft. ClassicCompute/informatie | Nee | Nee | [Klassieke Virtual Machines](./metrics-supported.md#microsoftclassiccomputevirtualmachines) |
|Micro soft. ClassicStorage/Storage accounts | Ja | Nee | [Opslag accounts (klassiek)](./metrics-supported.md#microsoftclassicstoragestorageaccounts) |
|Micro soft. ClassicStorage/Storage accounts/blobServices | Ja | Nee | [Opslag accounts (klassiek)-blobs](./metrics-supported.md#microsoftclassicstoragestorageaccountsblobservices) |
|Micro soft. ClassicStorage/Storage accounts/fileServices | Ja | Nee | [Opslag accounts (klassiek)-bestanden](./metrics-supported.md#microsoftclassicstoragestorageaccountsfileservices) |
|Micro soft. ClassicStorage/Storage accounts/queueServices | Ja | Nee | [Opslag accounts (klassiek)-wacht rijen](./metrics-supported.md#microsoftclassicstoragestorageaccountsqueueservices) |
|Micro soft. ClassicStorage/Storage accounts/tableServices | Ja | Nee | [Opslag accounts (klassiek)-tabellen](./metrics-supported.md#microsoftclassicstoragestorageaccountstableservices) |
|Micro soft. CognitiveServices/accounts | Ja | Nee | [Cognitive Services](./metrics-supported.md#microsoftcognitiveservicesaccounts) |
|Microsoft.Compute/virtualMachines | Yes | Ja<sup>1</sup> | [Virtual Machines](./metrics-supported.md#microsoftcomputevirtualmachines) |
|Microsoft.Compute/virtualMachineScaleSets | Ja | Nee |[Schaal sets voor virtuele machines](./metrics-supported.md#microsoftcomputevirtualmachinescalesets) |
|Micro soft. ContainerInstance/containerGroups | Ja| Nee | [Containergroepen](./metrics-supported.md#microsoftcontainerinstancecontainergroups) |
|Micro soft. ContainerRegistry/registers | Nee | Nee | [Container registers](./metrics-supported.md#microsoftcontainerregistryregistries) |
|Micro soft. container service/managedClusters | Ja | Nee | [Beheerde clusters](./metrics-supported.md#microsoftcontainerservicemanagedclusters) |
|Micro soft. DataBoxEdge/dataBoxEdgeDevices | Ja | Ja | [Data Box](./metrics-supported.md#microsoftdataboxedgedataboxedgedevices) |
|Micro soft. DataFactory/datafactories| Ja| Nee | [Gegevens fabrieken v1](./metrics-supported.md#microsoftdatafactorydatafactories) |
|Micro soft. DataFactory/fabrieken |Ja | Nee | [Gegevens fabrieken v2](./metrics-supported.md#microsoftdatafactoryfactories) |
|Microsoft.DataShare/accounts | Ja | Nee | |
|Microsoft.DBforMariaDB/servers | Nee | Nee | [DB voor MariaDB](./metrics-supported.md#microsoftdbformariadbservers) |
|Microsoft.DBforMySQL/servers | Nee | Nee |[DB voor MySQL](./metrics-supported.md#microsoftdbformysqlservers)|
|Microsoft.DBforPostgreSQL/servers | Nee | Nee | [DB voor PostgreSQL](./metrics-supported.md#microsoftdbforpostgresqlservers)|
|Micro soft. DBforPostgreSQL/serversv2 | Nee | Nee | [DB voor PostgreSQL v2](./metrics-supported.md#microsoftdbforpostgresqlserversv2)|
|Microsoft.DBforPostgreSQL/flexibleServers | Ja | Nee | [DB voor PostgreSQL (flexibele servers)](./metrics-supported.md#microsoftdbforpostgresqlflexibleservers)|
|Microsoft.Devices/IotHubs | Ja | Nee |[IoT Hub](./metrics-supported.md#microsoftdevicesiothubs) |
|Micro soft. devices/provisioningServices| Ja | Nee | [Services voor het inrichten van apparaten](./metrics-supported.md#microsoftdevicesprovisioningservices) |
|Micro soft. DigitalTwins/digitalTwinsInstances | Ja | Nee | |
|Microsoft.DocumentDB/databaseAccounts | Ja | Nee | [Cosmos DB](./metrics-supported.md#microsoftdocumentdbdatabaseaccounts) |
|Micro soft. EventGrid/domeinen | Ja | Nee | [Event Grid-domeinen](./metrics-supported.md#microsofteventgriddomains) |
|Micro soft. EventGrid/systemTopics | Ja | Nee | [Event Grid systeem onderwerpen](./metrics-supported.md#microsofteventgridsystemtopics) |
|Micro soft. EventGrid/topics |Ja | Nee | [Event Grid-onderwerpen](./metrics-supported.md#microsofteventgridtopics) |
|Micro soft. EventHub/clusters |Ja| Nee | [Event Hubs clusters](./metrics-supported.md#microsofteventhubclusters) |
|Microsoft.EventHub/namespaces |Ja| Nee | [Event Hubs](./metrics-supported.md#microsofteventhubnamespaces) |
|Micro soft. HDInsight/clusters | Ja | Nee | [HDInsight-clusters](./metrics-supported.md#microsofthdinsightclusters) |
|Micro soft. Insights/onderdelen | Ja | Nee | [Application Insights](./metrics-supported.md#microsoftinsightscomponents) |
|Microsoft.KeyVault/vaults | Ja |Ja |[Kluizen](./metrics-supported.md#microsoftkeyvaultvaults)|
|Micro soft. Kusto/clusters | Ja |Nee |[Data Explorer clusters](./metrics-supported.md#microsoftkustoclusters)|
|Micro soft. Logic/integrationServiceEnvironments | Ja | Nee |[Integratie service omgevingen](./metrics-supported.md#microsoftlogicintegrationserviceenvironments) |
|Microsoft.Logic/workflows | Nee | Nee |[Logic Apps](./metrics-supported.md#microsoftlogicworkflows) |
|Micro soft. MachineLearningServices/werk ruimten | Ja | Nee | [Machine Learning](./metrics-supported.md#microsoftmachinelearningservicesworkspaces) |
|Micro soft. Maps/accounts | Ja | Nee | [Maps-accounts](./metrics-supported.md#microsoftmapsaccounts) |
|Micro soft. Media/Media Services | Nee | Nee | [Media Services](./metrics-supported.md#microsoftmediamediaservices) |
|Micro soft. Media/Media Services/streamingEndpoints | Ja | Nee | [Media Services streaming-eind punten](./metrics-supported.md#microsoftmediamediaservicesstreamingendpoints) |
|Micro soft. NetApp/netAppAccounts/capacityPools | Ja | Ja | [Azure NetApp-capaciteits Pools](./metrics-supported.md#microsoftnetappnetappaccountscapacitypools) |
|Micro soft. NetApp/netAppAccounts/capacityPools/volumes | Ja | Ja | [Azure NetApp-volumes](./metrics-supported.md#microsoftnetappnetappaccountscapacitypoolsvolumes) |
|Micro soft. Network/applicationGateways | Ja | Nee | [Toepassings gateways](./metrics-supported.md#microsoftnetworkapplicationgateways) |
|Micro soft. Network/azurefirewalls | Ja | Nee | [Firewalls](./metrics-supported.md#microsoftnetworkazurefirewalls) |
|Microsoft.Network/dnsZones | Nee | Nee | [DNS-zones](./metrics-supported.md#microsoftnetworkdnszones) |
|Microsoft.Network/expressRouteCircuits | Ja | Nee |[ExpressRoute-circuits](./metrics-supported.md#microsoftnetworkexpressroutecircuits) |
|Micro soft. Network/expressRoutePorts | Ja | Nee |[ExpressRoute Direct](./metrics-supported.md#microsoftnetworkexpressrouteports) |
|Micro soft. Network/loadBalancers (alleen voor standaard-Sku's)| Ja| Nee | [Load balancers](./metrics-supported.md#microsoftnetworkloadbalancers) |
|Micro soft. Network/natGateways| Nee | Nee | |
|Micro soft. Network/privateEndpoints| Nee | Nee | |
|Micro soft. Network/privateLinkServices| Nee | Nee |
|Micro soft. Network/publicipaddresses | Nee | Nee |[Openbare IP-adressen](./metrics-supported.md#microsoftnetworkpublicipaddresses)|
|Microsoft.Network/trafficManagerProfiles | Ja | Nee | [Traffic Manager-profielen](./metrics-supported.md#microsoftnetworktrafficmanagerprofiles) |
|Microsoft.OperationalInsights/workspaces| Ja | Nee | [Log Analytics-werkruimten](./metrics-supported.md#microsoftoperationalinsightsworkspaces)|
|Micro soft. peering/peering | Ja | Nee | [Peerings](./metrics-supported.md#microsoftpeeringpeerings) |
|Micro soft. peering/peeringServices | Ja | Nee | [Peering Services](./metrics-supported.md#microsoftpeeringpeeringservices) |
|Micro soft. PowerBIDedicated/capaciteiten | Nee | Nee | [Capaciteiten](./metrics-supported.md#microsoftpowerbidedicatedcapacities) |
|Micro soft. relay/naam ruimten | Ja | Nee | [Relays](./metrics-supported.md#microsoftrelaynamespaces) |
|Micro soft. Search/searchServices | Nee | Nee | [Services zoeken](./metrics-supported.md#microsoftsearchsearchservices) |
|Microsoft.ServiceBus/namespaces | Ja | Nee | [Service Bus](./metrics-supported.md#microsoftservicebusnamespaces) |
|Micro soft. SQL/managedInstances | Nee | Ja | [Beheerde SQL-exemplaren](./metrics-supported.md#microsoftsqlmanagedinstances) |
|Microsoft.Sql/servers/databases | Nee | Ja | [SQL Databases](./metrics-supported.md#microsoftsqlserversdatabases) |
|Micro soft. SQL/servers/elasticPools | Nee | Ja | [Elastische SQL-Pools](./metrics-supported.md#microsoftsqlserverselasticpools) |
|Microsoft.Storage/storageAccounts |Ja | Nee | [Storage Accounts](./metrics-supported.md#microsoftstoragestorageaccounts)|
|Micro soft. Storage/Storage accounts/blobServices | Ja| Nee | [Opslag accounts-blobs](./metrics-supported.md#microsoftstoragestorageaccountsblobservices) |
|Micro soft. Storage/Storage accounts/fileServices | Ja| Nee | [Opslag accounts: bestanden](./metrics-supported.md#microsoftstoragestorageaccountsfileservices) |
|Micro soft. Storage/Storage accounts/queueServices | Ja| Nee | [Opslag accounts-wacht rijen](./metrics-supported.md#microsoftstoragestorageaccountsqueueservices) |
|Micro soft. Storage/Storage accounts/tableServices | Ja| Nee | [Opslag accounts-tabellen](./metrics-supported.md#microsoftstoragestorageaccountstableservices) |
|Micro soft. StorageCache/caches | Ja | Nee | |
|Micro soft. StorageSync/storageSyncServices | Ja | Nee | [Opslag synchronisatie Services](./metrics-supported.md#microsoftstoragesyncstoragesyncservices) |
|Micro soft. StreamAnalytics/streamingjobs | Ja | Nee | [Stream Analytics](./metrics-supported.md#microsoftstreamanalyticsstreamingjobs) |
|Micro soft. Synapse/werk ruimten | Ja | Nee | [Synapse Analytics](./metrics-supported.md#microsoftsynapseworkspaces) |
|Micro soft. Synapse/werk ruimten/bigDataPools | Ja | Nee | [Apache Spark Pools voor Synapse Analytics](./metrics-supported.md#microsoftsynapseworkspacesbigdatapools) |
|Micro soft. Synapse/werk ruimten/sqlPools | Ja | Nee | [Synapse Analytics SQL-groepen](./metrics-supported.md#microsoftsynapseworkspacessqlpools) |
|Micro soft. VMWareCloudSimple/informatie | Ja | Nee | [Virtuele CloudSimple-machines](./metrics-supported.md#microsoftvmwarecloudsimplevirtualmachines) |
|Micro soft. Web/hostingEnvironments/multiRolePools | Ja | Nee | [Groepen met meerdere rollen App Service Environment](./metrics-supported.md#microsoftwebhostingenvironmentsmultirolepools)|
|Micro soft. Web/hostingEnvironments/workerPools | Ja | Nee | [Werk groepen App Service Environment](./metrics-supported.md#microsoftwebhostingenvironmentsworkerpools)|
|Micro soft. web/server farms | Ja | Nee | [App Service plannen](./metrics-supported.md#microsoftwebserverfarms)|
|Micro soft. web/sites | Ja | Nee | [App Services en functions](./metrics-supported.md#microsoftwebsites)|
|Micro soft. web/sites/sleuven | Ja | Nee | [App Service sleuven](./metrics-supported.md#microsoftwebsitesslots)|

<sup>1</sup> wordt niet ondersteund voor metrische gegevens van het netwerk van de virtuele machine (netwerk in totaal, totale netwerk uitgaand, inkomende stromen, uitgaande stromen, maximum aanmaak frequentie voor inkomende stromen, maximum aanmaak snelheden voor uitgaande stromen) en aangepaste metrische gegevens.

## <a name="payload-schema"></a>Payload-schema

> [!NOTE]
> U kunt ook het [schema common alert](./alerts-common-schema.md)gebruiken. Dit biedt het voor deel van het gebruik van een enkele uitbreid bare en Unified payload van waarschuwingen voor alle waarschuwings services in azure monitor voor uw webhook-integraties. [Meer informatie over de algemene schema definities voor waarschuwingen.](./alerts-common-schema-definitions.md)


De POST-bewerking bevat de volgende JSON-nettolading en het schema voor alle nabije nieuwere metrische waarschuwingen wanneer een passende geconfigureerde [actie groep](./action-groups.md) wordt gebruikt:

```json
{
  "schemaId": "AzureMonitorMetricAlert",
  "data": {
    "version": "2.0",
    "status": "Activated",
    "context": {
      "timestamp": "2018-02-28T10:44:10.1714014Z",
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Contoso/providers/microsoft.insights/metricAlerts/StorageCheck",
      "name": "StorageCheck",
      "description": "",
      "conditionType": "SingleResourceMultipleMetricCriteria",
      "severity":"3",
      "condition": {
        "windowSize": "PT5M",
        "allOf": [
          {
            "metricName": "Transactions",
            "metricNamespace":"microsoft.storage/storageAccounts",
            "dimensions": [
              {
                "name": "AccountResourceId",
                "value": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Contoso/providers/Microsoft.Storage/storageAccounts/diag500"
              },
              {
                "name": "GeoType",
                "value": "Primary"
              }
            ],
            "operator": "GreaterThan",
            "threshold": "0",
            "timeAggregation": "PT5M",
            "metricValue": 1
          }
        ]
      },
      "subscriptionId": "00000000-0000-0000-0000-000000000000",
      "resourceGroupName": "Contoso",
      "resourceName": "diag500",
      "resourceType": "Microsoft.Storage/storageAccounts",
      "resourceId": "/subscriptions/1e3ff1c0-771a-4119-a03b-be82a51e232d/resourceGroups/Contoso/providers/Microsoft.Storage/storageAccounts/diag500",
      "portalLink": "https://portal.azure.com/#resource//subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Contoso/providers/Microsoft.Storage/storageAccounts/diag500"
    },
    "properties": {
      "key1": "value1",
      "key2": "value2"
    }
  }
}
```

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over de nieuwe [meldings ervaring](./alerts-overview.md).
* Meer informatie over [logboek waarschuwingen in azure](./alerts-unified-log.md).
* Meer informatie over [waarschuwingen in azure](./alerts-overview.md).
