---
title: 'Cluster resource Manager Service Fabric: verplaatsings kosten'
description: Meer informatie over de verplaatsings kosten voor Service Fabric Services en hoe deze kunnen worden opgegeven om te voldoen aan de behoeften van de architectuur, inclusief dynamische configuratie.
author: masnider
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: masnider
ms.custom: devx-track-csharp
ms.openlocfilehash: 0fdcfb02851d56ed996ae4bf32671ab545782733
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "89005340"
---
# <a name="service-movement-cost"></a>Kosten voor service verplaatsing
Een factor die de Service Fabric cluster resource manager beschouwt wanneer wordt geprobeerd te bepalen welke wijzigingen aan een cluster moeten worden aangebracht, zijn de kosten van die wijzigingen. Het principe van ' kosten ' wordt verhandeld op basis van de mate waarin het cluster kan worden verbeterd. Kosten worden in rekening gehouden bij het verplaatsen van services voor Balancing, defragmentatie en andere vereisten. Het doel is om te voldoen aan de vereisten in de minst verstorende of dure manier.

Het verplaatsen van Services kost CPU-tijd en netwerk bandbreedte mini maal. Voor stateful Services moet de status van die services worden gekopieerd, waardoor er extra geheugen en schijf ruimte in beslag neemt. Het minimaliseren van de kosten van oplossingen die door Azure Service Fabric cluster resource manager worden geleverd, helpt ervoor te zorgen dat de resources van het cluster onnodig niet worden gebruikt. U wilt echter ook geen oplossingen negeren waarmee de toewijzing van resources in het cluster aanzienlijk wordt verbeterd.

Cluster resource manager heeft twee manieren om kosten te berekenen en te beperken tijdens het beheren van het cluster. Het eerste mechanisme telt gewoon elke verplaatsing die het zou doen. Als er twee oplossingen worden gegenereerd met ongeveer hetzelfde saldo (Score), geeft de cluster resource manager de voor keur aan het abonnement met de laagste kosten (het totale aantal verplaatsingen).

Deze strategie werkt goed. Maar net als bij standaard of statische belasting, is het onwaarschijnlijk in een complex systeem dat alle verplaatsingen gelijk zijn. Sommige zijn waarschijnlijk veel duurder.

## <a name="setting-move-costs"></a>Kosten voor verplaatsen instellen 
U kunt de standaard kosten voor het verplaatsen van een service opgeven wanneer deze wordt gemaakt:

PowerShell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 3 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -DefaultMoveCost Medium
```

C# 

```csharp
FabricClient fabricClient = new FabricClient();
StatefulServiceDescription serviceDescription = new StatefulServiceDescription();
//set up the rest of the ServiceDescription
serviceDescription.DefaultMoveCost = MoveCost.Medium;
await fabricClient.ServiceManager.CreateServiceAsync(serviceDescription);
```

U kunt MoveCost ook dynamisch opgeven of bijwerken voor een service nadat de service is gemaakt: 

PowerShell: 

```posh
Update-ServiceFabricService -Stateful -ServiceName "fabric:/AppName/ServiceName" -DefaultMoveCost High
```

C#

```csharp
StatefulServiceUpdateDescription updateDescription = new StatefulServiceUpdateDescription();
updateDescription.DefaultMoveCost = MoveCost.High;
await fabricClient.ServiceManager.UpdateServiceAsync(new Uri("fabric:/AppName/ServiceName"), updateDescription);
```

## <a name="dynamically-specifying-move-cost-on-a-per-replica-basis"></a>Dynamisch opgeven van kosten voor verplaatsing per replica

De voor gaande code fragmenten zijn allemaal voor het opgeven van MoveCost voor een hele service van buiten de service zelf. De verplaatsings kosten zijn echter vooral nuttig wanneer de kosten voor het verplaatsen van een specifiek Service object worden gewijzigd gedurende de levens duur. Omdat de services zich waarschijnlijk het beste idee hebben van hoe kostbaar ze een bepaalde tijd moeten verplaatsen, is er een API voor services om hun eigen afzonderlijke verplaatsings kosten te rapporteren tijdens runtime. 

C#

```csharp
this.Partition.ReportMoveCost(MoveCost.Medium);
```

## <a name="impact-of-move-cost"></a>Impact van de kosten voor verplaatsen
MoveCost heeft vijf niveaus: nul, laag, gemiddeld, hoog en VeryHigh. De volgende regels zijn van toepassing:

* MoveCosts zijn relatief ten opzichte van elkaar, met uitzonde ring van nul en VeryHigh. 
* De kosten voor het verplaatsen van nul betekent dat de verplaatsing gratis is en niet mag worden geteld op basis van de Score van de oplossing.
* Bij het instellen van uw kosten voor verplaatsen naar hoog of VeryHigh wordt *niet* gegarandeerd dat de replica *nooit* wordt verplaatst.
* Replica's met VeryHigh-verplaatsings kosten worden alleen verplaatst als er sprake is van een schending van een beperking in het cluster die op geen enkele andere manier kan worden opgelost (zelfs als er veel andere replica's moeten worden verplaatst om de schending op te lossen)



<center>

![Kosten verplaatsen als factor bij het selecteren van replica's voor verplaatsing][Image1]
</center>

MoveCost helpt u bij het vinden van de oplossingen die de minst onderbrekingen in het algemeen veroorzaken en gemakkelijk te belopen zijn, terwijl er nog steeds een gelijkwaardig saldo wordt bereikt. De kosten van een service kunnen relatief zijn ten opzichte van een groot aantal zaken. De meest voorkomende factoren bij het berekenen van de verplaatsings kosten zijn:

- De hoeveelheid status of gegevens die de service moet verplaatsen.
- De kosten voor het verbreken van de verbinding van clients. Het verplaatsen van een primaire replica is doorgaans kostbaarer dan de kosten voor het verplaatsen van een secundaire replica.
- De kosten voor het onderbreken van een in-Flight-bewerking. Bepaalde bewerkingen op het niveau van de gegevens opslag of de bewerkingen die worden uitgevoerd als reactie op een client aanroep, zijn kostbaar. Na een bepaald punt wilt u ze niet meer stoppen als dat niet het geval is. Wanneer de bewerking wordt uitgevoerd, verhoogt u de verplaatsings kosten van dit service object om de kans te verkleinen dat deze wordt verplaatst. Wanneer de bewerking is voltooid, stelt u de kosten weer in op normaal.

> [!IMPORTANT]
> Het gebruik van de VeryHigh-verplaatsings kosten moet zorgvuldig worden overwogen, omdat het de mogelijkheid van cluster resource manager aanzienlijk beperkt om een wereld wijd optimale plaatsings oplossing in het cluster te vinden. Replica's met VeryHigh-verplaatsings kosten worden alleen verplaatst als er sprake is van een schending van een beperking in het cluster die op geen enkele andere manier kan worden opgelost (zelfs als er veel andere replica's moeten worden verplaatst om de schending op te lossen)

## <a name="enabling-move-cost-in-your-cluster"></a>Kosten voor verplaatsen in uw cluster inschakelen
Voor een nauw keurigere MoveCosts moet MoveCost in uw cluster worden ingeschakeld... Zonder deze instelling wordt de standaard methode voor het tellen van aantallen gebruikt voor het berekenen van MoveCost en worden MoveCost-rapporten genegeerd.


ClusterManifest.xml:

``` xml
        <Section Name="PlacementAndLoadBalancing">
            <Parameter Name="UseMoveCostReports" Value="true" />
        </Section>
```

via ClusterConfig.jsop voor zelfstandige implementaties of Template.jsop voor door Azure gehoste clusters:

```json
"fabricSettings": [
  {
    "name": "PlacementAndLoadBalancing",
    "parameters": [
      {
          "name": "UseMoveCostReports",
          "value": "true"
      }
    ]
  }
]
```

## <a name="next-steps"></a>Volgende stappen
- Service Fabric cluster resource manager maakt gebruik van metrische gegevens voor het beheren van het verbruik en de capaciteit in het cluster. Voor meer informatie over metrische gegevens en hoe u deze kunt configureren, kunt u het [verbruik van resources beheren en laden in service fabric met metrische gegevens](service-fabric-cluster-resource-manager-metrics.md).
- Voor meer informatie over hoe de cluster resource manager de belasting in het cluster beheert en balanceert, kunt u [het service Fabric-cluster uitbalanceren](service-fabric-cluster-resource-manager-balancing.md).

[Image1]:./media/service-fabric-cluster-resource-manager-movement-cost/service-most-cost-example.png
