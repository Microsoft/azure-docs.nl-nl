---
title: Prestatie bewaking met Windows Azure Diagnostics
description: Gebruik Windows Azure Diagnostics om prestatie meter items voor uw Azure Service Fabric-clusters te verzamelen.
ms.topic: conceptual
ms.date: 11/21/2018
ms.openlocfilehash: 6803494d29bf97336e30128f9f5ad20ec73ce202
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2021
ms.locfileid: "105627401"
---
# <a name="performance-monitoring-with-the-windows-azure-diagnostics-extension"></a>Prestatie bewaking met de uitbrei ding voor Windows-Azure Diagnostics

In dit document worden de stappen beschreven die nodig zijn voor het instellen van het verzamelen van prestatie meter items via de Windows Azure Diagnostics (WAD)-extensie voor Windows-clusters. Voor Linux-clusters stelt u de [log Analytics-agent](service-fabric-diagnostics-oms-agent.md) in voor het verzamelen van prestatie meter items voor uw knoop punten. 

 > [!NOTE]
> De WAD-extensie moet in uw cluster worden geïmplementeerd om deze stappen te kunnen gebruiken. Als deze niet is ingesteld, gaat u naar [gebeurtenis aggregatie en verzameling met behulp van Windows Azure Diagnostics](service-fabric-diagnostics-event-aggregation-wad.md).  


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="collect-performance-counters-via-the-wadcfg"></a>Prestatie meter items verzamelen via de WadCfg

Als u prestatie meter items wilt verzamelen via WAD, moet u de configuratie op de juiste manier aanpassen in de Resource Manager-sjabloon van uw cluster. Volg deze stappen om een prestatie meter item toe te voegen aan uw sjabloon en een resource manager-resource-upgrade uit te voeren.

1. Zoek de WAD-configuratie in de sjabloon van uw cluster `WadCfg` . U voegt prestatie meter items toe om te verzamelen onder de `DiagnosticMonitorConfiguration` .

2. Stel uw configuratie in voor het verzamelen van prestatie meter items door de volgende sectie toe te voegen aan uw `DiagnosticMonitorConfiguration` . 

    ```json
    "PerformanceCounters": {
        "scheduledTransferPeriod": "PT1M",
        "PerformanceCounterConfiguration": []
    }
    ```

    De `scheduledTransferPeriod` definieert hoe vaak de waarden van de prestatie meter items die worden verzameld, worden overgebracht naar uw Azure Storage-tabel en naar alle geconfigureerde Sinks. 

3. Voeg de prestatie meter items toe die u wilt verzamelen voor de gegevens `PerformanceCounterConfiguration` die in de vorige stap zijn gedeclareerd. Elk item dat u wilt verzamelen, is gedefinieerd met een `counterSpecifier` , `sampleRate` , `unit` , `annotation` en een relevant `sinks` .

Hier volgt een voor beeld van een configuratie met de teller voor de *totale processor tijd* (de hoeveelheid tijd die de CPU gebruikt voor het verwerken van bewerkingen) en *service Fabric actor-methode aanroepen per seconde*, een van de service Fabric aangepaste prestatie meter items. Raadpleeg [betrouw bare prestatie meter items voor actors](service-fabric-reliable-actors-diagnostics.md#list-of-events-and-performance-counters) en [betrouw bare service prestatie meter items](service-fabric-reliable-serviceremoting-diagnostics.md#list-of-performance-counters) voor een volledige lijst met Service Fabric aangepaste prestatie meter items.

 ```json
 "WadCfg": {
         "DiagnosticMonitorConfiguration": {
           "overallQuotaInMB": "50000",
           "EtwProviders": {
             "EtwEventSourceProviderConfiguration": [
               {
                 "provider": "Microsoft-ServiceFabric-Actors",
                 "scheduledTransferKeywordFilter": "1",
                 "scheduledTransferPeriod": "PT5M",
                 "DefaultEvents": {
                   "eventDestination": "ServiceFabricReliableActorEventTable"
                 }
               },
               {
                 "provider": "Microsoft-ServiceFabric-Services",
                 "scheduledTransferPeriod": "PT5M",
                 "DefaultEvents": {
                   "eventDestination": "ServiceFabricReliableServiceEventTable"
                 }
               }
             ],
             "EtwManifestProviderConfiguration": [
               {
                 "provider": "cbd93bc2-71e5-4566-b3a7-595d8eeca6e8",
                 "scheduledTransferLogLevelFilter": "Information",
                 "scheduledTransferKeywordFilter": "4611686018427387904",
                 "scheduledTransferPeriod": "PT5M",
                 "DefaultEvents": {
                   "eventDestination": "ServiceFabricSystemEventTable"
                 }
               }
             ]
           },
           "PerformanceCounters": {
                 "scheduledTransferPeriod": "PT1M",
                 "PerformanceCounterConfiguration": [
                     {
                         "counterSpecifier": "\\Processor(_Total)\\% Processor Time",
                         "sampleRate": "PT1M",
                         "unit": "Percent",
                         "annotation": [
                         ],
                         "sinks": ""
                     },
                     {
                         "counterSpecifier": "\\Service Fabric Actor Method(*)\\Invocations/Sec",
                         "sampleRate": "PT1M",
                     }
                 ]
             }
         }
       },
  ```

 De sampling frequentie voor het prestatie meter item kan worden gewijzigd volgens uw behoeften. De indeling hiervoor is `PT<time><unit>` , dus als u wilt dat de teller elke seconde wordt verzameld, moet u de configureren `"sampleRate": "PT15S"` .

 U kunt ook variabelen in uw ARM-sjabloon gebruiken om een matrix met prestatie meter items te verzamelen. Dit kan handig zijn wanneer u prestatie meter items per proces verzamelt. In het onderstaande voor beeld verzamelen we de processor tijd en garbage collector tijd per proces en twee prestatie meter items op de knoop punten zelf, allemaal met behulp van variabelen. 

 ```json
"variables": {
  "copy": [
      {
        "name": "processorTimeCounters",
        "count": "[length(parameters('monitoredProcesses'))]",
        "input": {
          "counterSpecifier": "\\Process([parameters('monitoredProcesses')[copyIndex('processorTimeCounters')]])\\% Processor Time",
          "sampleRate": "PT1M",
          "unit": "Percent",
          "sinks": "applicationInsights",
          "annotation": [
            {
              "displayName": "[concat(parameters('monitoredProcesses')[copyIndex('processorTimeCounters')],' Processor Time')]",
              "locale": "en-us"
            }
          ]
        }
      },
      {
        "name": "gcTimeCounters",
        "count": "[length(parameters('monitoredProcesses'))]",
        "input": {
          "counterSpecifier": "\\.NET CLR Memory([parameters('monitoredProcesses')[copyIndex('gcTimeCounters')]])\\% Time in GC",
          "sampleRate": "PT1M",
          "unit": "Percent",
          "sinks": "applicationInsights",
          "annotation": [
            {
              "displayName": "[concat(parameters('monitoredProcesses')[copyIndex('gcTimeCounters')],' Time in GC')]",
              "locale": "en-us"
            }
          ]
        }
      }
    ],
    "machineCounters": [
      {
        "counterSpecifier": "\\Memory\\Available Bytes",
        "sampleRate": "PT1M",
        "unit": "KB",
        "sinks": "applicationInsights",
        "annotation": [
          {
            "displayName": "Memory Available Kb",
            "locale": "en-us"
          }
        ]
      },
      {
        "counterSpecifier": "\\Memory\\% Committed Bytes In Use",
        "sampleRate": "PT15S",
        "unit": "percent",
        "annotation": [
          {
            "displayName": "Memory usage",
            "locale": "en-us"
          }
        ]
      }
    ]
  }
....
"WadCfg": {
    "DiagnosticMonitorConfiguration": {
      "overallQuotaInMB": "50000",
      "Metrics": {
        "metricAggregation": [
          {
            "scheduledTransferPeriod": "PT1M"
          }
        ],
        "resourceId": "[resourceId('Microsoft.Compute/virtualMachineScaleSets', variables('vmNodeTypeApp2Name'))]"
      },
      "PerformanceCounters": {
        "scheduledTransferPeriod": "PT1M",
        "PerformanceCounterConfiguration": "[concat(variables ('processorTimeCounters'), variables('gcTimeCounters'),  variables('machineCounters'))]"
      },
....
```

1. Wanneer u de juiste prestatie meter items hebt toegevoegd die moeten worden verzameld, moet u de cluster bron upgraden zodat deze wijzigingen worden weer gegeven in het actieve cluster. Sla uw gewijzigde op `template.json` en open Power shell. U kunt uw cluster bijwerken met `New-AzResourceGroupDeployment` . Voor de aanroep zijn de naam van de resource groep, het bijgewerkte sjabloon bestand en het parameter bestand vereist, en wordt u gevraagd om de benodigde wijzigingen aan te brengen in de resources die u hebt bijgewerkt. Wanneer u bent aangemeld bij uw account en zich in het juiste abonnement bevindt, gebruikt u de volgende opdracht om de upgrade uit te voeren:

    ```sh
    New-AzResourceGroupDeployment -ResourceGroupName <ResourceGroup> -TemplateFile <PathToTemplateFile> -TemplateParameterFile <PathToParametersFile> -Verbose
    ```

1. Zodra de upgrade is voltooid (tussen 15-45 minuten), afhankelijk van of het de eerste implementatie en de grootte van uw resource groep is, moeten WAD de prestatie meter items verzamelen en deze naar de tabel met de naam WADPerformanceCountersTable verzenden in het opslag account dat is gekoppeld aan uw cluster. Bekijk de prestatie meter items in Application Insights door [de AI-Sink toe te voegen aan de Resource Manager-sjabloon](service-fabric-diagnostics-event-aggregation-wad.md#add-the-application-insights-sink-to-the-resource-manager-template).

## <a name="next-steps"></a>Volgende stappen
* Verzamelen van meer prestatie meter items voor uw cluster. Bekijk [metrische prestatie gegevens](service-fabric-diagnostics-event-generation-perf.md) voor een lijst met prestatie meter items die u moet verzamelen.
* [Gebruik bewaking en diagnostische gegevens met een Windows-VM en Azure Resource Manager sjablonen](../virtual-machines/extensions/diagnostics-template.md) om uw wijzigingen aan te brengen `WadCfg` , inclusief het configureren van extra opslag accounts voor het verzenden van diagnostische gegevens naar.
* Ga naar de [WadCfg Builder](https://azure.github.io/azure-diagnostics-tools/config-builder/) om een volledig nieuwe sjabloon te maken en zorg ervoor dat de syntaxis juist is. ( https://azure.github.io/azure-diagnostics-tools/config-builder/) Als u een volledig nieuwe sjabloon wilt maken, moet u ervoor zorgen dat de syntaxis juist is.
