---
title: Bewaking en diagnose in Azure Service Fabric Mesh apps
description: Meer informatie over het controleren en diagnosticeren van toepassingen in Service Fabric Mesh in Azure.
author: srrengar
ms.topic: conceptual
ms.date: 03/19/2019
ms.author: srrengar
ms.custom: mvc, devcenter, devx-track-azurecli
ms.openlocfilehash: d8859293b4853cbfa8c3b3dd0e7d1bfe4f75fc40
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107766165"
---
# <a name="monitoring-and-diagnostics"></a>Controle en diagnose

> [!IMPORTANT]
> De preview van Azure Service Fabric Mesh is niet meer beschikbaar. Nieuwe implementaties zijn niet langer toegestaan via de Service Fabric Mesh API. Ondersteuning voor bestaande implementaties wordt voortgezet tot en met 28 april 2021.
> 
> Zie preview-Azure Service Fabric Mesh [voor meer informatie.](https://azure.microsoft.com/updates/azure-service-fabric-mesh-preview-retirement/)

Azure Service Fabric Mesh is een volledig beheerde service waarmee ontwikkelaars microservices-toepassingen kunnen implementeren zonder virtuele machines, opslag of netwerken hoeven te beheren. Bewaking en diagnose voor Service Fabric Mesh worden onderverdeeld in drie hoofdtypen diagnostische gegevens:

- Toepassingslogboeken: deze worden gedefinieerd als de logboeken van uw toepassingen in containers, op basis van hoe u uw toepassing hebt instrumenteerd (bijvoorbeeld docker-logboeken)
- Platformgebeurtenissen: gebeurtenissen van het Mesh-platform die relevant zijn voor uw containerbewerking, waaronder het activeren, deactiveren en beëindigen van containers.
- Metrische gegevens voor containers: metrische gegevens over resourcegebruik en prestaties voor uw containers (Docker-statistieken)

In dit artikel worden de opties voor bewaking en diagnostische gegevens voor de nieuwste preview-versie besproken.

## <a name="application-logs"></a>Toepassingslogboeken

U kunt uw Docker-logboeken per container bekijken vanuit uw geïmplementeerde containers. In het Service Fabric Mesh toepassingsmodel is elke container een codepakket in uw toepassing. Gebruik de volgende opdracht om de bijbehorende logboeken met een codepakket te bekijken:

```azurecli
az mesh code-package-log get --resource-group <nameOfRG> --app-name <nameOfApp> --service-name <nameOfService> --replica-name <nameOfReplica> --code-package-name <nameOfCodePackage>
```

> [!NOTE]
> U kunt de opdracht az mesh service-replica gebruiken om de replicanaam op te halen. Replicanamen verhogen gehele getallen van 0.

Hier ziet u hoe dit eruit ziet voor het bekijken van de logboeken van de container VotingWeb.Code van de stemtoepassing:

```azurecli
az mesh code-package-log get --resource-group <nameOfRG> --application-name SbzVoting --service-name VotingWeb --replica-name 0 --code-package-name VotingWeb.Code
```

## <a name="container-metrics"></a>Metrische containergegevens 

De Mesh-omgeving toont een aantal metrische gegevens die aangeven hoe uw containers presteren. De volgende metrische gegevens zijn beschikbaar via de Azure Portal en Azure Monitor CLI:

| Metrisch | Beschrijving | Eenheden|
|----|----|----|
| Cpu-utilisatie | ActualCpu/AllocatedCpu als een percentage | % |
| MemoryUtilization | ActualMem/AllocatedMem als percentage | % |
| ToegewezenCpu | Cpu toegewezen volgens Azure Resource Manager sjabloon | Cores |
| AllocatedMemory | Toegewezen geheugen volgens Azure Resource Manager sjabloon | MB |
| ActualCpu | CPU-gebruik | Cores |
| ActualMemory | Geheugengebruik | MB |
| ContainerStatus | 0 - Ongeldig: de containerstatus is onbekend <br> 1 - In behandeling: de container is gepland om te starten <br> 2 - Starten: De container is bezig met starten <br> 3 - Gestart: De container is gestart <br> 4 - Stoppen: De container wordt gestopt <br> 5 - Gestopt: De container is gestopt | N.v.t. |
| ApplicationStatus | 0 - Onbekend: de status kan niet worden opgehaald <br> 1 - Gereed: de toepassing wordt uitgevoerd <br> 2 - Upgraden: Er wordt een upgrade uitgevoerd <br> 3 - Maken: De toepassing wordt gemaakt <br> 4 - Verwijderen: De toepassing wordt verwijderd <br> 5 - Mislukt: de implementatie van de toepassing is mislukt | N.v.t. |
| ServiceStatus | 0 - Ongeldig: De service heeft momenteel geen status <br> 1 - OK: De service is in orde  <br> 2 - Waarschuwing: Er is mogelijk iets mis waarvoor onderzoek is vereist <br> 3 - Fout: Er is iets mis dat moet worden onderzocht <br> 4 - Onbekend: de status kan niet worden opgehaald | N.v.t. |
| ServiceReplicaStatus | 0 - Ongeldig: De replica heeft momenteel geen status <br> 1 - OK: De service is in orde  <br> 2 - Waarschuwing: Er is mogelijk iets mis waarvoor onderzoek is vereist <br> 3 - Fout: Er is iets mis dat moet worden onderzocht <br> 4 - Onbekend: de status kan niet worden opgehaald | N.v.t. | 
| RestartCount | Aantal keer dat de container opnieuw wordt opgestart | N.v.t. |

> [!NOTE]
> De waarden voor ServiceStatus en ServiceReplicaStatus zijn hetzelfde als de [HealthState](/dotnet/api/system.fabric.health.healthstate) in Service Fabric.

Elk metrisch gegeven is beschikbaar voor verschillende dimensies, zodat u aggregaten op verschillende niveaus kunt zien. De huidige lijst met dimensies is als volgt:

* ApplicationName
* ServiceName
* ServiceReplicaName
* CodePackageName

> [!NOTE]
> De dimensie CodePackageName is niet beschikbaar voor Linux-toepassingen. 

Elke dimensie komt overeen met verschillende onderdelen van het [Service Fabric Application-model](service-fabric-mesh-service-fabric-resources.md#applications-and-services)

### <a name="azure-monitor-cli"></a>Azure Monitor CLI

Een volledige lijst met opdrachten is beschikbaar in [Azure Monitor CLI-documenten,](/cli/azure/monitor/metrics#az_monitor_metrics_list) maar hieronder vindt u enkele nuttige voorbeelden 

In elk voorbeeld volgt de resource-id dit patroon

`"/subscriptions/<your sub ID>/resourcegroups/<your RG>/providers/Microsoft.ServiceFabricMesh/applications/<your App name>"`


* CPU-gebruik van de containers in een toepassing

```azurecli
    az monitor metrics list --resource <resourceId> --metric "CpuUtilization"
```
* Geheugengebruik voor elke servicereplica
```azurecli
    az monitor metrics list --resource <resourceId> --metric "MemoryUtilization" --dimension "ServiceReplicaName"
``` 

* Start opnieuw op voor elke container in een tijdvenster van één uur 
```azurecli
    az monitor metrics list --resource <resourceId> --metric "RestartCount" --start-time 2019-02-01T00:00:00Z --end-time 2019-02-01T01:00:00Z
``` 

* Gemiddeld CPU-gebruik in services met de naam VotingWeb in een venster van 1 uur
```azurecli
    az monitor metrics list --resource <resourceId> --metric "CpuUtilization" --start-time 2019-02-01T00:00:00Z --end-time 2019-02-01T01:00:00Z --aggregation "Average" --filter "ServiceName eq 'VotingWeb'"
``` 

### <a name="metrics-explorer"></a>Metrics-explorer

Metrics Explorer is een blade in de portal waarin u alle metrische gegevens voor uw Mesh-toepassing kunt visualiseren. Deze blade is toegankelijk op de pagina van de toepassing in de portal en op de azure-monitorblade, waarvan u de blade kunt gebruiken om metrische gegevens weer te geven voor al uw Azure-resources die ondersteuning bieden voor Azure Monitor. 

![Metrics Explorer](./media/service-fabric-mesh-monitoring-diagnostics/metricsexplorer.png)


<!--
### Container Insights

In addition to the metrics explorer, we also have a dashboard available out of the box that shows sample metrics over time under the Insights blade in the application's page in the portal. 

![Container Insights](./media/service-fabric-mesh-monitoring-diagnostics/containerinsights.png)
-->

## <a name="next-steps"></a>Volgende stappen
* Lees [Wat is Service Fabric Mesh?](service-fabric-mesh-overview.md) voor meer informatie over Service Fabric Mesh.
* Raadpleeg de cli-documenten Azure Monitor meer informatie over de opdrachten [Azure Monitor metrische gegevens.](/cli/azure/monitor/metrics#az_monitor_metrics_list)
