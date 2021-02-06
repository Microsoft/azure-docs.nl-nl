---
title: Bewaking en diagnose in azure Service Fabric mesh-apps
description: Meer informatie over het bewaken en diagnosticeren van toepassingen in Service Fabric net op Azure.
author: srrengar
ms.topic: conceptual
ms.date: 03/19/2019
ms.author: srrengar
ms.custom: mvc, devcenter, devx-track-azurecli
ms.openlocfilehash: 02de8ea5dd5c53192d2b8c7beba8bc36143beac6
ms.sourcegitcommit: 59cfed657839f41c36ccdf7dc2bee4535c920dd4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/06/2021
ms.locfileid: "99626991"
---
# <a name="monitoring-and-diagnostics"></a>Controle en diagnose

> [!IMPORTANT]
> De preview-versie van Azure Service Fabric Mesh is buiten gebruik gesteld. Nieuwe implementaties zijn niet langer toegestaan via de API van Service Fabric net. Ondersteuning voor bestaande implementaties gaat door tot 28 april 2021.
> 
> Zie [Azure service Fabric Netpreview buiten](https://azure.microsoft.com/updates/azure-service-fabric-mesh-preview-retirement/)gebruik stellen voor meer informatie.

Azure Service Fabric Mesh is een volledig beheerde service waarmee ontwikkelaars microservices-toepassingen kunnen implementeren zonder virtuele machines, opslag of netwerken hoeven te beheren. Bewaking en diagnose van Service Fabric net zijn onderverdeeld in drie hoofd typen diagnostische gegevens:

- Toepassings logboeken: deze worden gedefinieerd als de logboeken van uw toepassingen met containers, op basis van hoe u uw toepassing hebt geinstrumenteerd (bijvoorbeeld docker-Logboeken)
- Platform gebeurtenissen: gebeurtenissen van het net platform die relevant zijn voor uw container bewerking, met inbegrip van het activeren, deactiveren en beëindigen van de container.
- Metrische gegevens van containers-metrische gegevens over bron gebruik en prestaties voor uw containers (docker-statistieken)

In dit artikel worden de bewakings-en diagnostische opties voor de nieuwste preview-versie beschreven.

## <a name="application-logs"></a>Toepassings logboeken

U kunt uw docker-logboeken vanuit uw geïmplementeerde containers weer geven op basis van per container. In het Service Fabric-net-toepassings model is elke container een code pakket in uw toepassing. Als u de bijbehorende logboeken wilt weer geven met een code pakket, gebruikt u de volgende opdracht:

```azurecli
az mesh code-package-log get --resource-group <nameOfRG> --app-name <nameOfApp> --service-name <nameOfService> --replica-name <nameOfReplica> --code-package-name <nameOfCodePackage>
```

> [!NOTE]
> U kunt de opdracht AZ mesh service-replica gebruiken om de replica naam op te halen. De namen van replica's verhogen de gehele getallen van 0.

Hier ziet u de logboeken van de VotingWeb. code-container van de stem toepassing:

```azurecli
az mesh code-package-log get --resource-group <nameOfRG> --application-name SbzVoting --service-name VotingWeb --replica-name 0 --code-package-name VotingWeb.Code
```

## <a name="container-metrics"></a>Metrische container gegevens 

In de mesh-omgeving wordt een aantal metrische gegevens weer gegeven die aangeven hoe uw containers worden uitgevoerd. De volgende metrische gegevens zijn beschikbaar via de Azure Portal en Azure monitor CLI:

| Metrisch | Beschrijving | Eenheden|
|----|----|----|
| CpuUtilization | ActualCpu/AllocatedCpu als percentage | % |
| MemoryUtilization | ActualMem/AllocatedMem als percentage | % |
| AllocatedCpu | CPU toegewezen als per Azure Resource Manager sjabloon | Millicores |
| AllocatedMemory | Geheugen toegewezen volgens Azure Resource Manager sjabloon | MB |
| ActualCpu | CPU-gebruik | Millicores |
| ActualMemory | Geheugengebruik | MB |
| Container status | 0-ongeldig: de container status is onbekend <br> 1-in behandeling: de container is gepland om te starten <br> 2-starten: de container wordt gestart <br> 3-gestart: de container is gestart <br> 4-stoppen: de container wordt gestopt <br> 5-gestopt: de container is gestopt | N.v.t. |
| ApplicationStatus | 0-onbekend: de status kan niet worden opgehaald <br> 1-gereed: de toepassing wordt uitgevoerd <br> 2-bijwerken: er wordt een upgrade uitgevoerd <br> 3-maken: de toepassing wordt gemaakt <br> 4-verwijderen: de toepassing wordt verwijderd <br> 5-mislukt: de toepassing is niet geïmplementeerd | N.v.t. |
| ServiceStatus | 0-ongeldig: de service heeft momenteel geen integriteits status <br> 1-OK: de service is in orde  <br> 2-waarschuwing: er is mogelijk iets verkeerd vereist onderzoek <br> 3-fout: er is iets verkeerd gegaan dat moet worden onderzocht <br> 4-onbekend: de status kan niet worden opgehaald | N.v.t. |
| ServiceReplicaStatus | 0-ongeldig: de replica heeft momenteel geen integriteits status <br> 1-OK: de service is in orde  <br> 2-waarschuwing: er is mogelijk iets verkeerd vereist onderzoek <br> 3-fout: er is iets verkeerd gegaan dat moet worden onderzocht <br> 4-onbekend: de status kan niet worden opgehaald | N.v.t. | 
| RestartCount | Aantal herstarts van de container | N.v.t. |

> [!NOTE]
> De waarden ServiceStatus en ServiceReplicaStatus zijn hetzelfde als die van de [HealthState](/dotnet/api/system.fabric.health.healthstate) in service Fabric.

Elke metriek is beschikbaar op verschillende dimensies, zodat u aggregaties op verschillende niveaus kunt zien. De huidige lijst met dimensies is als volgt:

* ApplicationName
* ServiceName
* ServiceReplicaName
* CodePackageName

> [!NOTE]
> De dimensie CodePackageName is niet beschikbaar voor Linux-toepassingen. 

Elke dimensie komt overeen met verschillende onderdelen van het [service Fabric-toepassings model](service-fabric-mesh-service-fabric-resources.md#applications-and-services)

### <a name="azure-monitor-cli"></a>Azure Monitor CLI

Er is een volledige lijst met opdrachten beschikbaar in de [Azure monitor cli-documenten](/cli/azure/monitor/metrics#az-monitor-metrics-list) , maar we hebben een paar handige voor beelden opgenomen 

In elk voor beeld volgt de resource-ID dit patroon

`"/subscriptions/<your sub ID>/resourcegroups/<your RG>/providers/Microsoft.ServiceFabricMesh/applications/<your App name>"`


* CPU-gebruik van de containers in een toepassing

```azurecli
    az monitor metrics list --resource <resourceId> --metric "CpuUtilization"
```
* Geheugen gebruik voor elke service replica
```azurecli
    az monitor metrics list --resource <resourceId> --metric "MemoryUtilization" --dimension "ServiceReplicaName"
``` 

* Wordt opnieuw gestart voor elke container in een venster van één uur 
```azurecli
    az monitor metrics list --resource <resourceId> --metric "RestartCount" --start-time 2019-02-01T00:00:00Z --end-time 2019-02-01T01:00:00Z
``` 

* Gemiddeld CPU-gebruik voor alle services met de naam ' VotingWeb ' in een venster van één uur
```azurecli
    az monitor metrics list --resource <resourceId> --metric "CpuUtilization" --start-time 2019-02-01T00:00:00Z --end-time 2019-02-01T01:00:00Z --aggregation "Average" --filter "ServiceName eq 'VotingWeb'"
``` 

### <a name="metrics-explorer"></a>Metrics-explorer

Metrics Explorer is een Blade in de portal waarin u alle metrische gegevens voor uw net-toepassing kunt visualiseren. Deze Blade is toegankelijk op de pagina van de toepassing in de portal en de Blade Azure monitor, waarmee u de metrische gegevens voor al uw Azure-resources kunt weer geven die Azure Monitor ondersteunen. 

![Metrics Explorer](./media/service-fabric-mesh-monitoring-diagnostics/metricsexplorer.png)


<!--
### Container Insights

In addition to the metrics explorer, we also have a dashboard available out of the box that shows sample metrics over time under the Insights blade in the application's page in the portal. 

![Container Insights](./media/service-fabric-mesh-monitoring-diagnostics/containerinsights.png)
-->

## <a name="next-steps"></a>Volgende stappen
* Lees [Wat is Service Fabric Mesh?](service-fabric-mesh-overview.md) voor meer informatie over Service Fabric Mesh.
* Raadpleeg de [Azure monitor cli-documenten](/cli/azure/monitor/metrics#az-monitor-metrics-list)voor meer informatie over de Azure monitor metrische opdrachten.
