---
title: Azure-resourcelogboeken
description: Meer informatie over het streamen van Azure-resource logboeken naar een Log Analytics-werk ruimte in Azure Monitor.
author: bwren
services: azure-monitor
ms.topic: conceptual
ms.date: 07/17/2019
ms.author: bwren
ms.openlocfilehash: 2435e4ed16889d9d4701b6047c0a1f602ee7ae91
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "102558692"
---
# <a name="azure-resource-logs"></a>Azure-resourcelogboeken
Azure-resource logboeken zijn [platform logboeken](../essentials/platform-logs-overview.md) die inzicht bieden in bewerkingen die zijn uitgevoerd in een Azure-resource. De inhoud van bron Logboeken is afhankelijk van de Azure-service en het resource type. Bron logboeken worden niet standaard verzameld. U moet een diagnostische instelling maken voor elke Azure-resource om de resource logboeken te verzenden naar een Log Analytics-werk ruimte die u wilt gebruiken met [Azure monitor-logboeken](../logs/data-platform-logs.md), Azure Event hubs om buiten Azure door te sturen of om te Azure Storage voor archivering.

Zie [Diagnostische instellingen maken om platform logboeken en metrische gegevens naar verschillende bestemmingen te verzenden](../essentials/diagnostic-settings.md) voor meer informatie over het maken van een diagnostische instelling en het [implementeren van Azure monitor op schaal met behulp van Azure Policy](../deploy-scale.md) voor meer informatie over het gebruik van Azure Policy om automatisch een diagnostische instelling te maken voor elke Azure-resource die u maakt.

## <a name="send-to-log-analytics-workspace"></a>Verzenden naar Log Analytics-werkruimte
 Verzend resource logboeken naar een Log Analytics-werk ruimte om de functies van [Azure monitor logboeken](../logs/data-platform-logs.md) in te scha kelen. Dit omvat het volgende:

- Correleer bron logboek gegevens met andere bewakings gegevens die zijn verzameld door Azure Monitor.
- Consolideer logboek vermeldingen van meerdere Azure-resources,-abonnementen en-tenants naar één locatie voor analyse tegelijk.
- Gebruik logboek query's om complexe analyses uit te voeren en grondige inzichten op logboek gegevens te verkrijgen.
- Gebruik logboek waarschuwingen met complexe logica voor waarschuwingen.

[Een diagnostische instelling maken](../essentials/diagnostic-settings.md) om bron logboeken te verzenden naar een log Analytics-werk ruimte. Deze gegevens worden opgeslagen in tabellen zoals beschreven in de [structuur van Azure monitor logboeken](../logs/data-platform-logs.md). De tabellen die door resource logboeken worden gebruikt, zijn afhankelijk van het type verzameling dat de resource gebruikt:

- Azure Diagnostics-alle gegevens die zijn geschreven, worden naar de tabel [AzureDiagnostics](/azure/azure-monitor/reference/tables/azurediagnostics) .
- Resource-specifiek: gegevens worden naar afzonderlijke tabellen geschreven voor elke categorie van de resource.

### <a name="azure-diagnostics-mode"></a>Diagnostische modus van Azure 
In deze modus worden alle gegevens van een diagnostische instelling verzameld in de tabel [AzureDiagnostics](/azure/azure-monitor/reference/tables/azurediagnostics) . Dit is de verouderde methode die momenteel door de meeste Azure-Services wordt gebruikt. Aangezien meerdere bron typen gegevens naar dezelfde tabel verzenden, is het bijbehorende schema de hoofd verzameling van de schema's van alle verschillende gegevens typen die worden verzameld. Zie [AzureDiagnostics-verwijzing](/azure/azure-monitor/reference/tables/azurediagnostics) voor meer informatie over de structuur van deze tabel en hoe deze werkt met een mogelijk groot aantal kolommen.

Bekijk het volgende voor beeld waarin Diagnostische instellingen worden verzameld in dezelfde werk ruimte voor de volgende gegevens typen:

- Audit logboeken van service 1 (met een schema dat bestaat uit de kolommen A, B en C)  
- Fout logboeken van service 1 (met een schema dat bestaat uit kolommen D, E en F)  
- Audit logboeken van Service 2 (met een schema dat bestaat uit de kolommen G, H en I)  

De tabel AzureDiagnostics ziet er als volgt uit:  

| ResourceProvider    | Categorie     | A  | B  | C  | D  | E  | F  | G  | H  | I  |
| -- | -- | -- | -- | -- | -- | -- | -- | -- | -- | -- |
| Micro soft. Service1 | Audit logs bevat    | x1 | Y1 | z1 |    |    |    |    |    |    |
| Micro soft. Service1 | ErrorLogs    |    |    |    | eerste | W1 | E1 |    |    |    |
| Micro soft. Service2 | Audit logs bevat    |    |    |    |    |    |    | j1 | K1 | L1 |
| Micro soft. Service1 | ErrorLogs    |    |    |    | inclusief | Box | E2 |    |    |    |
| Micro soft. Service2 | Audit logs bevat    |    |    |    |    |    |    | j3 | k3 | L3 |
| Micro soft. Service1 | Audit logs bevat    | x5 | y5 waardeveld | z5 |    |    |    |    |    |    |
| ... |

### <a name="resource-specific"></a>Resource-specifiek
In deze modus worden afzonderlijke tabellen in de geselecteerde werk ruimte gemaakt voor elke categorie die in de diagnostische instelling is geselecteerd. Deze methode wordt aanbevolen omdat het veel gemakkelijker is om met de gegevens in logboek query's te werken, een betere detectie van schema's en hun structuur te bieden, de prestaties te verbeteren voor zowel opname latentie als query tijden, en de mogelijkheid om Azure RBAC-rechten te verlenen voor een specifieke tabel. Alle Azure-Services worden uiteindelijk naar de Resource-Specific modus gemigreerd. 

Het bovenstaande voor beeld resulteert in het maken van drie tabellen:
 
- Tabel *Service1AuditLogs* als volgt:

    | Resourceprovider | Categorie | A | B | C |
    | -- | -- | -- | -- | -- |
    | Service1 | Audit logs bevat | x1 | Y1 | z1 |
    | Service1 | Audit logs bevat | x5 | y5 waardeveld | z5 |
    | ... |

- Tabel *Service1ErrorLogs* als volgt:  

    | Resourceprovider | Categorie | D | E | F |
    | -- | -- | -- | -- | -- | 
    | Service1 | ErrorLogs |  eerste | W1 | E1 |
    | Service1 | ErrorLogs |  inclusief | Box | E2 |
    | ... |

- Tabel *Service2AuditLogs* als volgt:  

    | Resourceprovider | Categorie | G | H | I |
    | -- | -- | -- | -- | -- |
    | Service2 | Audit logs bevat | j1 | K1 | L1|
    | Service2 | Audit logs bevat | j3 | k3 | L3|
    | ... |



### <a name="select-the-collection-mode"></a>De verzamelings modus selecteren
Met de meeste Azure-resources worden gegevens naar de werk ruimte geschreven in **Azure Diagnostic** of de **resource-specifieke modus** zonder dat u daarvoor een keuze hoeft te doen. Zie de [documentatie voor elke service](./resource-logs-schema.md) voor meer informatie over de modus die wordt gebruikt. Alle Azure-Services zullen uiteindelijk Resource-Specific modus gebruiken. Als onderdeel van deze overgang kunt u met sommige resources een modus selecteren in de diagnostische instelling. Geef de resource-specifieke modus op voor nieuwe diagnostische instellingen, omdat hierdoor de gegevens eenvoudiger te beheren zijn en u mogelijk complexe migraties op een later tijdstip kunt voor komen.
  
   ![Selector voor de modus Diagnostische instellingen](media/resource-logs/diagnostic-settings-mode-selector.png)

> [!NOTE]
> Zie voor een voor beeld van het instellen van de verzamelings modus met een resource manager-sjabloon [voor beelden van Resource Manager-sjablonen voor Diagnostische instellingen in azure monitor](./resource-manager-diagnostic-settings.md#diagnostic-setting-for-recovery-services-vault).


U kunt een bestaande diagnostische instelling wijzigen in de resource-specifieke modus. In dit geval blijven de gegevens die al zijn verzameld in de tabel _AzureDiagnostics_ totdat deze is verwijderd volgens de Bewaar instelling voor de werk ruimte. Nieuwe gegevens worden verzameld in de toegewezen tabel. Gebruik de operator [Union](/azure/kusto/query/unionoperator) om gegevens op te vragen over beide tabellen.

Ga door met het bekijken van [Azure updates](https://azure.microsoft.com/updates/) blog voor aankondigingen over Azure-services die Resource-Specific modus ondersteunen.


## <a name="send-to-azure-event-hubs"></a>Verzenden naar Azure Event Hubs
Verzend resource logboeken naar een Event Hub om ze buiten Azure te verzenden, bijvoorbeeld naar een SIEM van derden of andere log Analytics-oplossingen. Bron logboeken van Event hubs worden gebruikt in JSON-indeling met een- `records` element dat de records in elke Payload bevat. Het schema is afhankelijk van het resource type zoals beschreven in het [gemeen schappelijke en service-specifieke schema voor Azure-resource logboeken](resource-logs-schema.md).

Hieronder ziet u voor beelden van uitvoer gegevens van Event Hubs voor een bron logboek:

```json
{
    "records": [
        {
            "time": "2019-07-15T18:00:22.6235064Z",
            "workflowId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/JOHNKEMTEST/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/JOHNKEMTESTLA",
            "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/JOHNKEMTEST/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/JOHNKEMTESTLA/RUNS/08587330013509921957/ACTIONS/SEND_EMAIL",
            "category": "WorkflowRuntime",
            "level": "Error",
            "operationName": "Microsoft.Logic/workflows/workflowActionCompleted",
            "properties": {
                "$schema": "2016-04-01-preview",
                "startTime": "2016-07-15T17:58:55.048482Z",
                "endTime": "2016-07-15T18:00:22.4109204Z",
                "status": "Failed",
                "code": "BadGateway",
                "resource": {
                    "subscriptionId": "00000000-0000-0000-0000-000000000000",
                    "resourceGroupName": "JohnKemTest",
                    "workflowId": "243aac67fe904cf195d4a28297803785",
                    "workflowName": "JohnKemTestLA",
                    "runId": "08587330013509921957",
                    "location": "westus",
                    "actionName": "Send_email"
                },
                "correlation": {
                    "actionTrackingId": "29a9862f-969b-4c70-90c4-dfbdc814e413",
                    "clientTrackingId": "08587330013509921958"
                }
            }
        },
        {
            "time": "2019-07-15T18:01:15.7532989Z",
            "workflowId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/JOHNKEMTEST/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/JOHNKEMTESTLA",
            "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/JOHNKEMTEST/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/JOHNKEMTESTLA/RUNS/08587330012106702630/ACTIONS/SEND_EMAIL",
            "category": "WorkflowRuntime",
            "level": "Information",
            "operationName": "Microsoft.Logic/workflows/workflowActionStarted",
            "properties": {
                "$schema": "2016-04-01-preview",
                "startTime": "2016-07-15T18:01:15.5828115Z",
                "status": "Running",
                "resource": {
                    "subscriptionId": "00000000-0000-0000-0000-000000000000",
                    "resourceGroupName": "JohnKemTest",
                    "workflowId": "243aac67fe904cf195d4a28297803785",
                    "workflowName": "JohnKemTestLA",
                    "runId": "08587330012106702630",
                    "location": "westus",
                    "actionName": "Send_email"
                },
                "correlation": {
                    "actionTrackingId": "042fb72c-7bd4-439e-89eb-3cf4409d429e",
                    "clientTrackingId": "08587330012106702632"
                }
            }
        }
    ]
}
```

## <a name="send-to-azure-storage"></a>Verzenden naar Azure Storage
Verzend resource logboeken naar Azure Storage om het archief te bewaren. Zodra u de diagnostische instelling hebt gemaakt, wordt er een opslag container gemaakt in het opslag account zodra er een gebeurtenis optreedt in een van de ingeschakelde logboek categorieën. De blobs in de container gebruiken de volgende naam Conventie:

```
insights-logs-{log category name}/resourceId=/SUBSCRIPTIONS/{subscription ID}/RESOURCEGROUPS/{resource group name}/PROVIDERS/{resource provider name}/{resource type}/{resource name}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
```

De BLOB voor een netwerk beveiligings groep kan bijvoorbeeld een naam hebben die er ongeveer als volgt uitziet:

```
insights-logs-networksecuritygrouprulecounter/resourceId=/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/TESTRESOURCEGROUP/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUP/TESTNSG/y=2016/m=08/d=22/h=18/m=00/PT1H.json
```

Elke PT1H.json-blob bevat een JSON-blob van gebeurtenissen die hebben plaatsgevonden binnen het uur dat is opgegeven in de blob-URL (bijvoorbeeld, h=12). Tijdens het huidige uur worden gebeurtenissen toegevoegd aan het bestand PT1H.json wanneer deze zich voordoen. De minuut waarde (m = 00) is altijd 00, omdat de bron logboek gebeurtenissen worden opgesplitst in afzonderlijke blobs per uur.

In de PT1H.jsop bestand wordt elke gebeurtenis opgeslagen met de volgende indeling. Dit maakt gebruik van een algemeen schema op het hoogste niveau, maar is uniek voor elke Azure-service, zoals beschreven in het [schema voor bron logboeken](./resource-logs-schema.md).

``` JSON
{"time": "2016-07-01T00:00:37.2040000Z","systemId": "46cdbb41-cb9c-4f3d-a5b4-1d458d827ff1","category": "NetworkSecurityGroupRuleCounter","resourceId": "/SUBSCRIPTIONS/s1id1234-5679-0123-4567-890123456789/RESOURCEGROUPS/TESTRESOURCEGROUP/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/TESTNSG","operationName": "NetworkSecurityGroupCounters","properties": {"vnetResourceGuid": "{12345678-9012-3456-7890-123456789012}","subnetPrefix": "10.3.0.0/24","macAddress": "000123456789","ruleName": "/subscriptions/ s1id1234-5679-0123-4567-890123456789/resourceGroups/testresourcegroup/providers/Microsoft.Network/networkSecurityGroups/testnsg/securityRules/default-allow-rdp","direction": "In","type": "allow","matchedConnections": 1988}}
```

> [!NOTE]
> Platform-logboeken worden geschreven naar Blob-opslag met behulp van [JSON-lijnen](http://jsonlines.org/), waarbij elke gebeurtenis een regel is en het teken voor nieuwe regel aangeeft dat er een gebeurtenis is. Deze indeling is geïmplementeerd in november 2018. Vóór deze datum zijn logboeken naar Blob Storage geschreven als een JSON-matrix van records, zoals wordt beschreven in [voor bereiding van indeling wijzigen in azure monitor platform logboeken die zijn gearchiveerd in een opslag account](resource-logs-blob-format.md).


## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over resource logboeken](../essentials/platform-logs-overview.md).
* [Diagnostische instellingen maken voor het verzenden van platform logboeken en-metrische gegevens naar verschillende bestemmingen](../essentials/diagnostic-settings.md).
