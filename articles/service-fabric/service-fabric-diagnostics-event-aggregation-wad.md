---
title: Gebeurtenissen samen voegen met Windows Azure Diagnostics
description: Meer informatie over het samen voegen en verzamelen van gebeurtenissen met WAD voor bewaking en diagnose van Azure Service Fabric-clusters.
ms.topic: conceptual
ms.date: 04/03/2018
ms.openlocfilehash: bbc8efcb2600e1832ad8a37560ab231a4a7f3185
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2021
ms.locfileid: "105626725"
---
# <a name="event-aggregation-and-collection-using-windows-azure-diagnostics"></a>Gebeurtenis aggregatie en verzameling met behulp van Windows Azure Diagnostics
> [!div class="op_single_selector"]
> * [Windows](service-fabric-diagnostics-event-aggregation-wad.md)
> * [Linux](service-fabric-diagnostics-event-aggregation-lad.md)
>
>

Wanneer u een Azure Service Fabric-cluster uitvoert, is het een goed idee om de logboeken te verzamelen van alle knoop punten op een centrale locatie. Met de logboeken op een centrale locatie kunt u problemen in uw cluster analyseren en oplossen, of problemen in de toepassingen en services die in dat cluster worden uitgevoerd.

Een manier om logboeken te uploaden en te verzamelen, is door gebruik te maken van de extensie Windows Azure Diagnostics (WAD), waarmee logboeken naar Azure Storage worden geüpload en ook de optie is voor het verzenden van logboeken naar Azure-toepassing inzichten of Event Hubs. U kunt ook een extern proces gebruiken om de gebeurtenissen uit de opslag te lezen en deze te plaatsen in een analyse platform product, zoals [Azure monitor logboeken](./service-fabric-diagnostics-oms-setup.md) of een andere oplossing voor het parseren van Logboeken.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Vereisten
De volgende hulpprogram ma's worden gebruikt in dit artikel:

* [Azure Resource Manager](../azure-resource-manager/management/overview.md)
* [Azure PowerShell](/powershell/azure/)
* [Azure Resource Manager-sjabloon](../virtual-machines/extensions/diagnostics-template.md?toc=/azure/virtual-machines/windows/toc.json)

## <a name="service-fabric-platform-events"></a>Service Fabric-platform gebeurtenissen
Service Fabric stelt u in met enkele kant-en- [klare logboek kanalen](service-fabric-diagnostics-event-generation-infra.md), waarvan de volgende kanalen vooraf zijn geconfigureerd met de extensie voor het verzenden van bewakings-en diagnostische gegevens naar een opslag tabel of elders:
  * [Operationele gebeurtenissen](service-fabric-diagnostics-event-generation-operational.md): bewerkingen op een hoger niveau die het service Fabric-platform uitvoert. Voor beelden zijn onder andere het maken van toepassingen en services, wijziging van knooppunt status en upgrade-informatie. Deze worden verzonden als Event Tracing for Windows-logboeken (ETW)
  * [Reliable Actors-programmeermodelgebeurtenissen](service-fabric-reliable-actors-diagnostics.md)
  * [Reliable Services-programmeermodelgebeurtenissen](service-fabric-reliable-services-diagnostics.md)

## <a name="deploy-the-diagnostics-extension-through-the-portal"></a>De diagnostische extensie implementeren via de portal
De eerste stap bij het verzamelen van Logboeken is het implementeren van de diagnostische uitbrei ding voor de knoop punten van de virtuele-machine schaalset in het Service Fabric cluster. De diagnostische uitbrei ding verzamelt logboeken op elke virtuele machine en uploadt deze naar het opslag account dat u opgeeft. In de volgende stappen wordt beschreven hoe u dit kunt doen voor nieuwe en bestaande clusters via de Azure Portal-en Azure Resource Manager sjablonen.

### <a name="deploy-the-diagnostics-extension-as-part-of-cluster-creation-through-azure-portal"></a>Implementeer de diagnostische extensie als onderdeel van het maken van een cluster via Azure Portal
Wanneer u uw cluster maakt, vouwt u in de stap cluster configuratie de optionele instellingen uit en zorgt u ervoor dat diagnostische gegevens is ingesteld **op aan** (de standaard instelling).

![Azure Diagnostics instellingen in de portal voor het maken van clusters](media/service-fabric-diagnostics-event-aggregation-wad/azure-enable-diagnostics-new.png)

We raden u ten zeerste aan de sjabloon te downloaden **voordat u op maken klikt** in de laatste stap. Zie [een service Fabric cluster instellen met behulp van een Azure Resource Manager sjabloon](service-fabric-cluster-creation-via-arm.md)voor meer informatie. U hebt de sjabloon nodig om wijzigingen in te voeren op welke kanalen (zie hierboven) gegevens moeten worden verzameld.

![Cluster sjabloon](media/service-fabric-diagnostics-event-aggregation-wad/download-cluster-template.png)

Nu u gebeurtenissen in Azure Storage samenvoegt, [stelt u Azure monitor logboeken](service-fabric-diagnostics-oms-setup.md) in om inzicht te krijgen en query's uit te geven in de Azure monitor logboeken Portal

>[!NOTE]
>Er is momenteel geen manier om de gebeurtenissen te filteren of op te schonen die naar de tabellen worden verzonden. Als u geen proces implementeert om gebeurtenissen uit de tabel te verwijderen, blijft de tabel groeien (de standaard limiet is 50 GB). Instructies voor het wijzigen hiervan vindt u [verderop in dit artikel](service-fabric-diagnostics-event-aggregation-wad.md#update-storage-quota). Daarnaast is er een voor beeld van een service voor het opschonen van gegevens die wordt uitgevoerd in het watchdog-voor [beeld](https://github.com/Azure-Samples/service-fabric-watchdog-service)en wordt aanbevolen dat u er een voor uzelf schrijft, tenzij u een goede reden hebt om logboeken te bewaren buiten een periode van 30 of 90 dagen.



## <a name="deploy-the-diagnostics-extension-through-azure-resource-manager"></a>De diagnostische uitbrei ding implementeren via Azure Resource Manager

### <a name="create-a-cluster-with-the-diagnostics-extension"></a>Een cluster maken met de uitbrei ding voor diagnostische gegevens
Als u een cluster wilt maken met behulp van Resource Manager, moet u de JSON van de diagnostische configuratie toevoegen aan de volledige Resource Manager-sjabloon. We bieden een voor beeld van vijf VM-cluster resource manager-sjabloon met diagnostische configuratie die hieraan is toegevoegd als onderdeel van de voor beelden van de Resource Manager-sjabloon. U ziet het op deze locatie in de galerie met Azure-voor beelden: [cluster met vijf knoop punten met diagnostische Resource Manager-sjabloon voor beeld](https://azure.microsoft.com/resources/templates/service-fabric-secure-cluster-5-node-1-nodetype/).

Als u de instelling diagnostische gegevens in de Resource Manager-sjabloon wilt zien, opent u de azuredeploy.jsin het bestand en zoekt u naar **IaaSDiagnostics**. Als u een cluster wilt maken met behulp van deze sjabloon, selecteert u de knop **implementeren in azure die u kunt** vinden op de vorige koppeling.

U kunt ook het Resource Manager-voor beeld downloaden, wijzigingen aanbrengen en een cluster maken met de gewijzigde sjabloon met behulp van de `New-AzResourceGroupDeployment` opdracht in een Azure PowerShell-venster. Raadpleeg de volgende code voor de para meters die u doorgeeft aan de opdracht. Voor gedetailleerde informatie over het implementeren van een resource groep met behulp van Power shell raadpleegt u het artikel [een resource groep implementeren met de sjabloon Azure Resource Manager](../azure-resource-manager/templates/deploy-powershell.md).

### <a name="add-the-diagnostics-extension-to-an-existing-cluster"></a>De diagnostische extensie toevoegen aan een bestaand cluster
Als u een bestaand cluster hebt waarvoor geen diagnostische gegevens zijn geïmplementeerd, kunt u het toevoegen of bijwerken via de clustersjabloon. Wijzig de Resource Manager-sjabloon die wordt gebruikt voor het maken van het bestaande cluster of down load de sjabloon uit de portal zoals eerder beschreven. Wijzig het bestand template.json door de volgende taken uit te voeren:

Voeg een nieuwe opslag resource toe aan de sjabloon door toe te voegen aan de sectie resources.

```json
{
    "apiVersion": "2018-07-01",
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[parameters('applicationDiagnosticsStorageAccountName')]",
    "location": "[parameters('computeLocation')]",
    "sku": {
    "name": "[parameters('applicationDiagnosticsStorageAccountType')]"
    "tier": "standard"
  },
    "tags": {
    "resourceType": "Service Fabric",
    "clusterName": "[parameters('clusterName')]"
  }
},
```

 Voeg vervolgens toe aan de sectie para meters vlak na de definities van het opslag account `supportLogStorageAccountName` . Vervang de naam van het *opslag account* voor tijdelijke aanduidingen hier de naam van het opslag account dat u wilt.

```json
    "applicationDiagnosticsStorageAccountType": {
      "type": "string",
      "allowedValues": [
        "Standard_LRS",
        "Standard_GRS"
      ],
      "defaultValue": "Standard_LRS",
      "metadata": {
        "description": "Replication option for the application diagnostics storage account"
      }
    },
    "applicationDiagnosticsStorageAccountName": {
      "type": "string",
      "defaultValue": "**STORAGE ACCOUNT NAME GOES HERE**",
      "metadata": {
        "description": "Name for the storage account that contains application diagnostics data from the cluster"
      }
    },
```
Werk vervolgens de `VirtualMachineProfile` sectie van de template.jsin bestand bij door de volgende code toe te voegen in de matrix extensies. Zorg ervoor dat u aan het begin of het einde een komma toevoegt, afhankelijk van waar deze is ingevoegd.

```json
{
    "name": "[concat(parameters('vmNodeType0Name'),'_Microsoft.Insights.VMDiagnosticsSettings')]",
    "properties": {
        "type": "IaaSDiagnostics",
        "autoUpgradeMinorVersion": true,
        "protectedSettings": {
        "storageAccountName": "[parameters('applicationDiagnosticsStorageAccountName')]",
        "storageAccountKey": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('applicationDiagnosticsStorageAccountName')),'2015-05-01-preview').key1]",
        "storageAccountEndPoint": "https://core.windows.net/"
        },
        "publisher": "Microsoft.Azure.Diagnostics",
        "settings": {
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
                },
                {
                    "provider": "02d06793-efeb-48c8-8f7f-09713309a810",
                    "scheduledTransferLogLevelFilter": "Information",
                    "scheduledTransferKeywordFilter": "4611686018427387904",
                    "scheduledTransferPeriod": "PT5M",
                    "DefaultEvents": {
                    "eventDestination": "ServiceFabricSystemEventTable"
                    }
                }
                ]
            }
            }
        },
        "StorageAccount": "[parameters('applicationDiagnosticsStorageAccountName')]"
        },
        "typeHandlerVersion": "1.5"
    }
}
```

Nadat u de template.jsin het bestand hebt gewijzigd zoals beschreven, publiceert u de Resource Manager-sjabloon opnieuw. Als de sjabloon is geëxporteerd, publiceert het uitvoeren van het deploy.ps1-bestand de sjabloon opnieuw. Nadat u hebt geïmplementeerd, moet u ervoor zorgen dat **ProvisioningState** is **voltooid**.

> [!TIP]
> Als u containers naar uw cluster wilt implementeren, schakelt u WAD in om docker-statistieken op te halen door deze toe te voegen aan uw **WadCfg > sectie DiagnosticMonitorConfiguration** .

```json
"DockerSources": {
    "Stats": {
        "enabled": true,
        "sampleRate": "PT1M"
    }
},
```

### <a name="update-storage-quota"></a>Opslag quotum bijwerken

Omdat de tabellen die worden gevuld door de uitbrei ding groeien tot het quotum wordt bereikt, kunt u overwegen de quotum grootte te verlagen. De standaard waarde is 50 GB en kan worden geconfigureerd in de sjabloon onder het `overallQuotaInMB` veld onder `DiagnosticMonitorConfiguration`

```json
"overallQuotaInMB": "50000",
```

## <a name="log-collection-configurations"></a>Configuraties van logboek verzameling
Logboeken van extra kanalen zijn ook beschikbaar voor de verzameling. Hier volgen enkele van de meest voorkomende configuraties die u in de sjabloon kunt maken voor clusters die worden uitgevoerd in Azure.

* Operationeel kanaal-basis: deze optie is standaard ingeschakeld voor bewerkingen op hoog niveau die worden uitgevoerd door Service Fabric en het cluster, met inbegrip van gebeurtenissen voor een knoop punt, een nieuwe toepassing die wordt geïmplementeerd of een upgrade wordt teruggedraaid, enzovoort. Raadpleeg [operationele kanaal gebeurtenissen](./service-fabric-diagnostics-event-generation-operational.md)voor een lijst met gebeurtenissen.
  
```json
      scheduledTransferKeywordFilter: "4611686018427387904"
  ```
* Operationeel kanaal-gedetailleerd: Dit omvat status rapporten en taak verdelings beslissingen, plus alles in het basis-operationele kanaal. Deze gebeurtenissen worden gegenereerd door het systeem of door uw code met behulp van de status-of belasting rapportage-Api's zoals [ReportPartitionHealth](/previous-versions/azure/reference/mt645153(v=azure.100)) of [ReportLoad](/previous-versions/azure/reference/mt161491(v=azure.100)). Als u deze gebeurtenissen wilt weer geven in de diagnostische Logboeken van Visual Studio, voegt u ' micro soft-ServiceFabric: 4:0x4000000000000008 ' toe aan de lijst met ETW-providers.

```json
      scheduledTransferKeywordFilter: "4611686018427387912"
  ```

* Gegevens-en Messa ging Channel-Base: essentiële logboeken en gebeurtenissen die zijn gegenereerd in de Messa ging (momenteel alleen de ReverseProxy) en het gegevenspad, evenals gedetailleerde operationele kanaal Logboeken. Deze gebeurtenissen zijn fouten bij het verwerken van aanvragen en andere kritieke problemen in de ReverseProxy, evenals de verwerkte aanvragen. **Dit is onze aanbeveling voor uitgebreide logboek registratie**. Als u deze gebeurtenissen wilt weer geven in de diagnostische Logboeken van Visual Studio, voegt u ' micro soft-ServiceFabric: 4:0x4000000000000010 ' toe aan de lijst met ETW-providers.

```json
      scheduledTransferKeywordFilter: "4611686018427387928"
  ```

* Data & Message Channel: gedetailleerd kanaal dat alle niet-kritieke Logboeken bevat van gegevens en berichten in het cluster en het gedetailleerde operationele kanaal. Raadpleeg de [reverse proxy Diagnostics Guide (Engelstalig](service-fabric-reverse-proxy-diagnostics.md)) voor meer informatie over het oplossen van problemen met alle omgekeerde proxy gebeurtenissen.  Als u deze gebeurtenissen wilt weer geven in de diagnostische logboeken van Visual Studio, voegt u ' micro soft-ServiceFabric: 4:0x4000000000000020 ' toe aan de lijst met ETW-providers.

```json
      scheduledTransferKeywordFilter: "4611686018427387944"
  ```

>[!NOTE]
>Dit kanaal heeft een zeer groot aantal gebeurtenissen, waardoor het verzamelen van gebeurtenissen uit dit gedetailleerde kanaal resulteert in een groot aantal traceringen die snel worden gegenereerd en opslag capaciteit kan verbruiken. Schakel deze optie alleen in als dit absoluut nood zakelijk is.


Als u de **basis van het operationele kanaal** wilt inschakelen voor uitgebreide logboek registratie met de minste mate van ruis, `EtwManifestProviderConfiguration` zou de in `WadCfg` uw sjabloon er als volgt uitzien:

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
              },
              {
                "provider": "02d06793-efeb-48c8-8f7f-09713309a810",
                "scheduledTransferLogLevelFilter": "Information",
                "scheduledTransferKeywordFilter": "4611686018427387904",
                "scheduledTransferPeriod": "PT5M",
                "DefaultEvents": {
                "eventDestination": "ServiceFabricSystemEventTable"
                }
              }
            ]
          }
        }
      },
```

## <a name="collect-from-new-eventsource-channels"></a>Verzamelen uit nieuwe event source-kanalen

Als u Diagnostische gegevens wilt bijwerken om logboeken te verzamelen van nieuwe event source-kanalen die een nieuwe toepassing vertegenwoordigen die u op het punt staat te implementeren, voert u dezelfde stappen uit als eerder beschreven voor het instellen van diagnostische gegevens voor een bestaand cluster.

Werk de `EtwEventSourceProviderConfiguration` sectie in het template.jsbestand om vermeldingen toe te voegen voor de nieuwe event source-kanalen voordat u de configuratie-update toepast met behulp van de `New-AzResourceGroupDeployment` Power shell-opdracht. De naam van de gebeurtenis bron wordt gedefinieerd als onderdeel van uw code in het Visual Studio-gegenereerde bestand ServiceEventSource. cs.

Als uw gebeurtenis bron bijvoorbeeld de naam mijn-Event source heeft, voegt u de volgende code toe om de gebeurtenissen van My-Eventsource te plaatsen in een tabel met de naam MyDestinationTableName.

```json
        {
            "provider": "My-Eventsource",
            "scheduledTransferPeriod": "PT5M",
            "DefaultEvents": {
            "eventDestination": "MyDestinationTableName"
            }
        }
```

Als u prestatie meter items of gebeurtenis logboeken wilt verzamelen, wijzigt u de Resource Manager-sjabloon met behulp van de voor beelden in [een virtuele Windows-machine maken met bewaking en diagnose met behulp van een Azure Resource Manager sjabloon](../virtual-machines/extensions/diagnostics-template.md?toc=/azure/virtual-machines/windows/toc.json). Publiceer vervolgens de Resource Manager-sjabloon opnieuw.

## <a name="collect-performance-counters"></a>Prestatie meter items verzamelen

Als u prestatie gegevens wilt verzamelen uit uw cluster, voegt u de prestatie meter items toe aan uw ' WadCfg > DiagnosticMonitorConfiguration ' in de Resource Manager-sjabloon voor uw cluster. Zie [prestatie bewaking met wad](service-fabric-diagnostics-perf-wad.md) voor de stappen voor het aanpassen van uw `WadCfg` voor het verzamelen van specifieke prestatie meter items. Referentie [service Fabric prestatie meter items](service-fabric-diagnostics-event-generation-perf.md) voor een lijst met prestatie meter items die u kunt verzamelen.
  
Als u een Application Insights-Sink gebruikt, zoals beschreven in de sectie hieronder, en u wilt dat deze metrische gegevens worden weer gegeven in Application Insights, moet u de naam van de Sink toevoegen in de sectie ' sinks ', zoals hierboven wordt weer gegeven. Hiermee worden automatisch de prestatie meter items verzonden die afzonderlijk zijn geconfigureerd voor uw Application Insights-resource.


## <a name="send-logs-to-application-insights"></a>Logboeken naar Application Insights verzenden

### <a name="configuring-application-insights-with-wad"></a>Application Insights configureren met WAD

>[!NOTE]
>Dit is op dit moment alleen van toepassing op Windows-clusters.

Er zijn twee primaire manieren om gegevens te verzenden van WAD naar Azure-toepassing Insights. dit wordt bereikt door een Application Insights-Sink toe te voegen aan de WAD-configuratie, via de Azure Portal of via een Azure Resource Manager sjabloon.

#### <a name="add-an-application-insights-instrumentation-key-when-creating-a-cluster-in-azure-portal"></a>Een Application Insights instrumentatie sleutel toevoegen bij het maken van een cluster in Azure Portal

![Een AIKey toevoegen](media/service-fabric-diagnostics-event-analysis-appinsights/azure-enable-diagnostics.png)

Bij het maken van een cluster, als de diagnostische gegevens zijn ingeschakeld, een optioneel veld voor het invoeren van een Application Insights instrumentatie sleutel wordt weer gegeven. Als u uw Application Insights sleutel hier plakt, wordt de Application Insights-Sink automatisch voor u geconfigureerd in de Resource Manager-sjabloon die wordt gebruikt voor het implementeren van uw cluster.

#### <a name="add-the-application-insights-sink-to-the-resource-manager-template"></a>De Application Insights-Sink toevoegen aan de Resource Manager-sjabloon

Voeg in het ' WadCfg ' van de Resource Manager-sjabloon een ' sink ' toe door de volgende twee wijzigingen op te nemen:

1. Voeg de Sink-configuratie direct toe nadat de `DiagnosticMonitorConfiguration` is voltooid:

    ```json
    "SinksConfig": {
        "Sink": [
            {
                "name": "applicationInsights",
                "ApplicationInsights": "***ADD INSTRUMENTATION KEY HERE***"
            }
        ]
    }

    ```

2. Neem de Sink op in het `DiagnosticMonitorConfiguration` door de volgende regel toe te voegen aan de `DiagnosticMonitorConfiguration` van de `WadCfg` (rechts voor de `EtwProviders` worden gedeclareerd):

    ```json
    "sinks": "applicationInsights"
    ```

In zowel de voor gaande code fragmenten is de naam ' applicationInsights ' gebruikt om de sink te beschrijven. Dit is geen vereiste en zolang de naam van de sink is opgenomen in ' sinks ', kunt u de naam op een wille keurige teken reeks instellen.

Op dit moment worden logboeken van het cluster weer gegeven als **traceringen** in de logboek weergave van Application Insights. Omdat de meeste traceringen van het platform van het niveau ' informatief ' zijn, kunt u ook overwegen de Sink-configuratie te wijzigen zodat alleen logboeken worden verzonden met het type waarschuwing of fout. U kunt dit doen door ' channels ' toe te voegen aan uw Sink, zoals wordt geïllustreerd in [dit artikel](../azure-monitor/agents/diagnostics-extension-to-application-insights.md).

>[!NOTE]
>Als u een onjuiste Application Insights sleutel in de portal of in uw Resource Manager-sjabloon gebruikt, moet u de sleutel hand matig wijzigen en het cluster bijwerken of opnieuw implementeren.

## <a name="next-steps"></a>Volgende stappen

Zodra u Azure Diagnostics correct hebt geconfigureerd, ziet u de gegevens in uw opslag tabellen in de logboeken ETW en Event source. Als u Azure Monitor-logboeken, Kibana of een ander gegevens analyse-en visualisatie platform wilt gebruiken dat niet rechtstreeks is geconfigureerd in de Resource Manager-sjabloon, moet u ervoor zorgen dat u het platform van uw keuze instelt voor het lezen van de gegevens uit deze opslag tabellen. Dit wordt gedaan voor Azure Monitor Logboeken is relatief redelijk en wordt uitgelegd in [gebeurtenis-en logboek analyse](service-fabric-diagnostics-event-analysis-oms.md). Application Insights is in deze zin een bit van een speciaal geval, omdat deze kan worden geconfigureerd als onderdeel van de configuratie van de diagnostische gegevens, dus Raadpleeg het [desbetreffende artikel](service-fabric-diagnostics-event-analysis-appinsights.md) als u ervoor kiest om AI te gebruiken.

>[!NOTE]
>Er is momenteel geen manier om de gebeurtenissen te filteren of op te schonen die naar de tabel worden verzonden. Als u geen proces implementeert om gebeurtenissen uit de tabel te verwijderen, blijft de tabel groeien. Op dit moment is er een voor beeld van een service voor het opschonen van gegevens die wordt uitgevoerd in het watchdog-voor [beeld](https://github.com/Azure-Samples/service-fabric-watchdog-service)en wordt aanbevolen dat u er een voor uzelf schrijft, tenzij u een goede reden hebt om logboeken te bewaren buiten een periode van 30 of 90 dagen.

* [Meer informatie over het verzamelen van prestatie meter items of Logboeken met behulp van de diagnostische extensie](../virtual-machines/extensions/diagnostics-template.md?toc=/azure/virtual-machines/windows/toc.json)
* [Gebeurtenis analyse en visualisatie met Application Insights](service-fabric-diagnostics-event-analysis-appinsights.md)
* [Gebeurtenis analyse en visualisatie met Azure Monitor-logboeken](service-fabric-diagnostics-event-analysis-oms.md)
* [Gebeurtenis analyse en visualisatie met Application Insights](service-fabric-diagnostics-event-analysis-appinsights.md)
* [Gebeurtenis analyse en visualisatie met Azure Monitor-logboeken](service-fabric-diagnostics-event-analysis-oms.md)
