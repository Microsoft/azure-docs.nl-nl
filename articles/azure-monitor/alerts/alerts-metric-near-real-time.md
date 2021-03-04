---
title: Ondersteunde resources voor metrische waarschuwingen in Azure Monitor
description: Naslag informatie over metrische gegevens en logboeken voor metrische waarschuwingen in Azure Monitor
author: harelbr
ms.author: harelbr
services: monitoring
ms.topic: conceptual
ms.date: 02/10/2021
ms.openlocfilehash: c282e6890d56fe047b319f72e05cdc97de76cfcf
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102038183"
---
# <a name="supported-resources-for-metric-alerts-in-azure-monitor"></a>Ondersteunde resources voor metrische waarschuwingen in Azure Monitor

Azure Monitor ondersteunt nu een [nieuw type metrische waarschuwing](./alerts-overview.md) met aanzienlijke voor delen ten opzichte van de oudere [klassieke metrische waarschuwingen](./alerts-classic.overview.md). Metrische gegevens zijn beschikbaar voor een [grote lijst met Azure-Services](../essentials/metrics-supported.md). De nieuwere waarschuwingen bieden ondersteuning voor een deel verzameling van de resource typen. Dit artikel bevat een lijst met deze subset.

U kunt ook nieuwere metrische waarschuwingen gebruiken voor populaire logboek gegevens die zijn opgeslagen in een Log Analytics werk ruimte geëxtraheerd als metrische waarden. Bekijk [metrische waarschuwingen voor logboeken](./alerts-metric-logs.md)voor meer informatie.

## <a name="portal-powershell-cli-rest-support"></a>Portal, Power shell, CLI, REST-ondersteuning
Op dit moment kunt u alleen nieuwe metrische waarschuwingen maken in de sjablonen Azure Portal, [rest API](/rest/api/monitor/metricalerts/)of [Resource Manager](./alerts-metric-create-templates.md). Ondersteuning voor het configureren van nieuwere waarschuwingen met behulp van Power shell en Azure CLI versie 2,0 en hoger wordt binnenkort beschikbaar.

## <a name="metrics-and-dimensions-supported"></a>Ondersteunde metrische gegevens en dimensies
Nieuwere metrische waarschuwingen ondersteunen waarschuwingen voor metrische gegevens die gebruikmaken van dimensies. U kunt dimensies gebruiken om uw metrische gegevens te filteren op het juiste niveau. Alle ondersteunde metrische gegevens en de toepasselijke dimensies kunnen worden verkend en gevisualiseerd vanaf [Azure monitor-Metrics Explorer](../essentials/metrics-charts.md).

Dit is de volledige lijst met Azure Monitor metrische bronnen die worden ondersteund door de nieuwere waarschuwingen:

|Resourcetype  |Ondersteunde dimensies |Waarschuwingen voor meerdere resources| Beschik bare metrische gegevens|
|---------|---------|-----|----------|
|Micro soft. Aadiam/azureADMetrics | Ja | Nee | |
|Microsoft.ApiManagement/service | Ja | Nee | [API Management](../essentials/metrics-supported.md#microsoftapimanagementservice) |
|Micro soft. AppConfiguration/configurationStores |Ja | Nee | [App-configuratie](../essentials/metrics-supported.md#microsoftappconfigurationconfigurationstores) |
|Micro soft. AppPlatform/lente | Ja | Nee | [Azure Spring Cloud](../essentials/metrics-supported.md#microsoftappplatformspring) |
|Micro soft. Automation/automationAccounts | Ja| Nee | [Automation-accounts](../essentials/metrics-supported.md#microsoftautomationautomationaccounts) |
|Micro soft. AVS/privateClouds | Nee | Nee | [Azure VMware Solution](../essentials/metrics-supported.md#microsoftavsprivateclouds) |
|Microsoft.Bat-CH/batchAccounts | Ja | Nee | [Batchaccounts](../essentials/metrics-supported.md#microsoftbatchbatchaccounts) |
|Microsoft.Cache/Redis | Ja | Ja | [Azure Cache voor Redis](../essentials/metrics-supported.md#microsoftcacheredis) |
|Micro soft. ClassicCompute/domein naam/sleuven/rollen | Nee | Nee | [Klassieke Cloud Services](../essentials/metrics-supported.md#microsoftclassiccomputedomainnamesslotsroles) |
|Micro soft. ClassicCompute/informatie | Nee | Nee | [Klassieke Virtual Machines](../essentials/metrics-supported.md#microsoftclassiccomputevirtualmachines) |
|Micro soft. ClassicStorage/Storage accounts | Ja | Nee | [Opslag accounts (klassiek)](../essentials/metrics-supported.md#microsoftclassicstoragestorageaccounts) |
|Micro soft. ClassicStorage/Storage accounts/blobServices | Ja | Nee | [Opslag accounts (klassiek)-blobs](../essentials/metrics-supported.md#microsoftclassicstoragestorageaccountsblobservices) |
|Micro soft. ClassicStorage/Storage accounts/fileServices | Ja | Nee | [Opslag accounts (klassiek)-bestanden](../essentials/metrics-supported.md#microsoftclassicstoragestorageaccountsfileservices) |
|Micro soft. ClassicStorage/Storage accounts/queueServices | Ja | Nee | [Opslag accounts (klassiek)-wacht rijen](../essentials/metrics-supported.md#microsoftclassicstoragestorageaccountsqueueservices) |
|Micro soft. ClassicStorage/Storage accounts/tableServices | Ja | Nee | [Opslag accounts (klassiek)-tabellen](../essentials/metrics-supported.md#microsoftclassicstoragestorageaccountstableservices) |
|Micro soft. CognitiveServices/accounts | Ja | Nee | [Cognitive Services](../essentials/metrics-supported.md#microsoftcognitiveservicesaccounts) |
|Microsoft.Compute/virtualMachines | Ja | Ja<sup>1</sup> | [Virtual Machines](../essentials/metrics-supported.md#microsoftcomputevirtualmachines) |
|Microsoft.Compute/virtualMachineScaleSets | Ja | Nee |[Schaal sets voor virtuele machines](../essentials/metrics-supported.md#microsoftcomputevirtualmachinescalesets) |
|Micro soft. ContainerInstance/containerGroups | Ja| Nee | [Containergroepen](../essentials/metrics-supported.md#microsoftcontainerinstancecontainergroups) |
|Micro soft. ContainerRegistry/registers | Nee | Nee | [Container registers](../essentials/metrics-supported.md#microsoftcontainerregistryregistries) |
|Micro soft. container service/managedClusters | Ja | Nee | [Beheerde clusters](../essentials/metrics-supported.md#microsoftcontainerservicemanagedclusters) |
|Micro soft. DataBoxEdge/dataBoxEdgeDevices | Ja | Ja | [Data Box](../essentials/metrics-supported.md#microsoftdataboxedgedataboxedgedevices) |
|Micro soft. DataFactory/datafactories| Ja| Nee | [Gegevens fabrieken v1](../essentials/metrics-supported.md#microsoftdatafactorydatafactories) |
|Micro soft. DataFactory/fabrieken |Ja | Nee | [Gegevens fabrieken v2](../essentials/metrics-supported.md#microsoftdatafactoryfactories) |
|Microsoft.DataShare/accounts | Ja | Nee | [Gegevens shares](../essentials/metrics-supported.md#microsoftdatashareaccounts) |
|Microsoft.DBforMariaDB/servers | Nee | Nee | [DB voor MariaDB](../essentials/metrics-supported.md#microsoftdbformariadbservers) |
|Microsoft.DBforMySQL/servers | Nee | Nee |[DB voor MySQL](../essentials/metrics-supported.md#microsoftdbformysqlservers)|
|Microsoft.DBforPostgreSQL/servers | Nee | Nee | [DB voor PostgreSQL](../essentials/metrics-supported.md#microsoftdbforpostgresqlservers)|
|Micro soft. DBforPostgreSQL/serversv2 | Nee | Nee | [DB voor PostgreSQL v2](../essentials/metrics-supported.md#microsoftdbforpostgresqlserversv2)|
|Microsoft.DBforPostgreSQL/flexibleServers | Ja | Nee | [DB voor PostgreSQL (flexibele servers)](../essentials/metrics-supported.md#microsoftdbforpostgresqlflexibleservers)|
|Microsoft.Devices/IotHubs | Ja | Nee |[IoT Hub](../essentials/metrics-supported.md#microsoftdevicesiothubs) |
|Micro soft. devices/provisioningServices| Ja | Nee | [Services voor het inrichten van apparaten](../essentials/metrics-supported.md#microsoftdevicesprovisioningservices) |
|Micro soft. DigitalTwins/digitalTwinsInstances | Ja | Nee | [Digital Twins](../essentials/metrics-supported.md#microsoftdigitaltwinsdigitaltwinsinstances) |
|Microsoft.DocumentDB/databaseAccounts | Ja | Nee | [Cosmos DB](../essentials/metrics-supported.md#microsoftdocumentdbdatabaseaccounts) |
|Micro soft. EventGrid/domeinen | Ja | Nee | [Event Grid-domeinen](../essentials/metrics-supported.md#microsofteventgriddomains) |
|Micro soft. EventGrid/systemTopics | Ja | Nee | [Event Grid systeem onderwerpen](../essentials/metrics-supported.md#microsofteventgridsystemtopics) |
|Micro soft. EventGrid/topics |Ja | Nee | [Event Grid-onderwerpen](../essentials/metrics-supported.md#microsofteventgridtopics) |
|Micro soft. EventHub/clusters |Ja| Nee | [Event Hubs clusters](../essentials/metrics-supported.md#microsofteventhubclusters) |
|Microsoft.EventHub/namespaces |Ja| Nee | [Event Hubs](../essentials/metrics-supported.md#microsofteventhubnamespaces) |
|Micro soft. HDInsight/clusters | Ja | Nee | [HDInsight-clusters](../essentials/metrics-supported.md#microsofthdinsightclusters) |
|Micro soft. Insights/onderdelen | Ja | Nee | [Application Insights](../essentials/metrics-supported.md#microsoftinsightscomponents) |
|Microsoft.KeyVault/vaults | Ja |Ja |[Kluizen](../essentials/metrics-supported.md#microsoftkeyvaultvaults)|
|Micro soft. Kusto/clusters | Ja |Nee |[Data Explorer clusters](../essentials/metrics-supported.md#microsoftkustoclusters)|
|Micro soft. Logic/integrationServiceEnvironments | Ja | Nee |[Integratie service omgevingen](../essentials/metrics-supported.md#microsoftlogicintegrationserviceenvironments) |
|Microsoft.Logic/workflows | Nee | Nee |[Logic Apps](../essentials/metrics-supported.md#microsoftlogicworkflows) |
|Micro soft. MachineLearningServices/werk ruimten | Ja | Nee | [Machine Learning](../essentials/metrics-supported.md#microsoftmachinelearningservicesworkspaces) |
|Micro soft. Maps/accounts | Ja | Nee | [Maps-accounts](../essentials/metrics-supported.md#microsoftmapsaccounts) |
|Micro soft. Media/Media Services | Nee | Nee | [Media Services](../essentials/metrics-supported.md#microsoftmediamediaservices) |
|Micro soft. Media/Media Services/streamingEndpoints | Ja | Nee | [Media Services streaming-eind punten](../essentials/metrics-supported.md#microsoftmediamediaservicesstreamingendpoints) |
|Micro soft. NetApp/netAppAccounts/capacityPools | Ja | Ja | [Azure NetApp-capaciteits Pools](../essentials/metrics-supported.md#microsoftnetappnetappaccountscapacitypools) |
|Micro soft. NetApp/netAppAccounts/capacityPools/volumes | Ja | Ja | [Azure NetApp-volumes](../essentials/metrics-supported.md#microsoftnetappnetappaccountscapacitypoolsvolumes) |
|Micro soft. Network/applicationGateways | Ja | Nee | [Toepassings gateways](../essentials/metrics-supported.md#microsoftnetworkapplicationgateways) |
|Micro soft. Network/azurefirewalls | Ja | Nee | [Firewalls](../essentials/metrics-supported.md#microsoftnetworkazurefirewalls) |
|Microsoft.Network/dnsZones | Nee | Nee | [DNS-zones](../essentials/metrics-supported.md#microsoftnetworkdnszones) |
|Microsoft.Network/expressRouteCircuits | Ja | Nee |[ExpressRoute-circuits](../essentials/metrics-supported.md#microsoftnetworkexpressroutecircuits) |
|Micro soft. Network/expressRoutePorts | Ja | Nee |[ExpressRoute Direct](../essentials/metrics-supported.md#microsoftnetworkexpressrouteports) |
|Micro soft. Network/loadBalancers (alleen voor standaard-Sku's)| Ja| Nee | [Load balancers](../essentials/metrics-supported.md#microsoftnetworkloadbalancers) |
|Micro soft. Network/natGateways| Nee | Nee | [NAT-gateways](../essentials/metrics-supported.md#microsoftnetworknatgateways) |
|Micro soft. Network/privateEndpoints| Nee | Nee | [Privé-eindpunten](../essentials/metrics-supported.md#microsoftnetworkprivateendpoints) |
|Micro soft. Network/privateLinkServices| Nee | Nee | [Services voor persoonlijke koppelingen](../essentials/metrics-supported.md#microsoftnetworkprivatelinkservices) |
|Micro soft. Network/publicipaddresses | Nee | Nee | [Openbare IP-adressen](../essentials/metrics-supported.md#microsoftnetworkpublicipaddresses)|
|Microsoft.Network/trafficManagerProfiles | Ja | Nee | [Traffic Manager-profielen](../essentials/metrics-supported.md#microsoftnetworktrafficmanagerprofiles) |
|Microsoft.OperationalInsights/workspaces| Ja | Nee | [Log Analytics-werkruimten](../essentials/metrics-supported.md#microsoftoperationalinsightsworkspaces)|
|Micro soft. peering/peering | Ja | Nee | [Peerings](../essentials/metrics-supported.md#microsoftpeeringpeerings) |
|Micro soft. peering/peeringServices | Ja | Nee | [Peering Services](../essentials/metrics-supported.md#microsoftpeeringpeeringservices) |
|Micro soft. PowerBIDedicated/capaciteiten | Nee | Nee | [Capaciteiten](../essentials/metrics-supported.md#microsoftpowerbidedicatedcapacities) |
|Micro soft. relay/naam ruimten | Ja | Nee | [Relays](../essentials/metrics-supported.md#microsoftrelaynamespaces) |
|Micro soft. Search/searchServices | Nee | Nee | [Services zoeken](../essentials/metrics-supported.md#microsoftsearchsearchservices) |
|Microsoft.ServiceBus/namespaces | Ja | Nee | [Service Bus](../essentials/metrics-supported.md#microsoftservicebusnamespaces) |
|Micro soft. SQL/managedInstances | Nee | Ja | [Beheerde SQL-exemplaren](../essentials/metrics-supported.md#microsoftsqlmanagedinstances) |
|Microsoft.Sql/servers/databases | Nee | Ja | [SQL Databases](../essentials/metrics-supported.md#microsoftsqlserversdatabases) |
|Micro soft. SQL/servers/elasticPools | Nee | Ja | [Elastische SQL-Pools](../essentials/metrics-supported.md#microsoftsqlserverselasticpools) |
|Microsoft.Storage/storageAccounts |Ja | Nee | [Storage Accounts](../essentials/metrics-supported.md#microsoftstoragestorageaccounts)|
|Micro soft. Storage/Storage accounts/blobServices | Ja| Nee | [Opslag accounts-blobs](../essentials/metrics-supported.md#microsoftstoragestorageaccountsblobservices) |
|Micro soft. Storage/Storage accounts/fileServices | Ja| Nee | [Opslag accounts: bestanden](../essentials/metrics-supported.md#microsoftstoragestorageaccountsfileservices) |
|Micro soft. Storage/Storage accounts/queueServices | Ja| Nee | [Opslag accounts-wacht rijen](../essentials/metrics-supported.md#microsoftstoragestorageaccountsqueueservices) |
|Micro soft. Storage/Storage accounts/tableServices | Ja| Nee | [Opslag accounts-tabellen](../essentials/metrics-supported.md#microsoftstoragestorageaccountstableservices) |
|Micro soft. StorageCache/caches | Ja | Nee | [HPC-caches](../essentials/metrics-supported.md#microsoftstoragecachecaches) |
|Micro soft. StorageSync/storageSyncServices | Ja | Nee | [Opslag synchronisatie Services](../essentials/metrics-supported.md#microsoftstoragesyncstoragesyncservices) |
|Micro soft. StreamAnalytics/streamingjobs | Ja | Nee | [Stream Analytics](../essentials/metrics-supported.md#microsoftstreamanalyticsstreamingjobs) |
|Micro soft. Synapse/werk ruimten | Ja | Nee | [Synapse Analytics](../essentials/metrics-supported.md#microsoftsynapseworkspaces) |
|Micro soft. Synapse/werk ruimten/bigDataPools | Ja | Nee | [Apache Spark Pools voor Synapse Analytics](../essentials/metrics-supported.md#microsoftsynapseworkspacesbigdatapools) |
|Micro soft. Synapse/werk ruimten/sqlPools | Ja | Nee | [Synapse Analytics SQL-groepen](../essentials/metrics-supported.md#microsoftsynapseworkspacessqlpools) |
|Micro soft. VMWareCloudSimple/informatie | Ja | Nee | [Virtuele CloudSimple-machines](../essentials/metrics-supported.md#microsoftvmwarecloudsimplevirtualmachines) |
|Micro soft. Web/hostingEnvironments/multiRolePools | Ja | Nee | [Groepen met meerdere rollen App Service Environment](../essentials/metrics-supported.md#microsoftwebhostingenvironmentsmultirolepools)|
|Micro soft. Web/hostingEnvironments/workerPools | Ja | Nee | [Werk groepen App Service Environment](../essentials/metrics-supported.md#microsoftwebhostingenvironmentsworkerpools)|
|Micro soft. web/server farms | Ja | Nee | [App Service plannen](../essentials/metrics-supported.md#microsoftwebserverfarms)|
|Micro soft. web/sites | Ja | Nee | [App Services en functions](../essentials/metrics-supported.md#microsoftwebsites)|
|Micro soft. web/sites/sleuven | Ja | Nee | [App Service sleuven](../essentials/metrics-supported.md#microsoftwebsitesslots)|

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