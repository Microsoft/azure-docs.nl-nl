---
title: Dynamische Service Fabric azure-knooppunttags
description: Met Azure Service Fabric kunt u dynamisch knooppunttags toevoegen en verwijderen.
author: yu-supersonic
ms.topic: conceptual
ms.date: 04/05/2021
ms.author: branim
ms.custom: devx-track-csharp
ms.openlocfilehash: 712e6422060619e5567a74d6335320eff9ed8e66
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107741745"
---
# <a name="introduction-to-dynamic-node-tags"></a>Inleiding tot dynamische knooppunttags
Met knooppunttags kunt u dynamisch tags toevoegen aan en verwijderen uit knooppunten om de plaatsing van services te beïnvloeden. Knooppunttagging is zeer flexibel en maakt wijzigingen in serviceplaatsing mogelijk zonder toepassings- of clusterupgrades. Tags kunnen op elk moment worden toegevoegd aan of verwijderd uit knooppunten en services kunnen vereisten voor bepaalde tags opgeven wanneer ze worden gemaakt. De tagvereisten voor een service kunnen ook dynamisch worden bijgewerkt terwijl deze wordt uitgevoerd.

Knooppunttagging is vergelijkbaar met [plaatsingsbeperkingen](service-fabric-cluster-resource-manager-configure-services.md) en wordt meestal gebruikt om te bepalen op welke knooppunten een service wordt uitgevoerd. Elke Service Fabric-service kan worden geconfigureerd om te vereisen dat een tag wordt geplaatst of actief blijft.

Knooppunttagging wordt ondersteund voor alle Service Fabric gehoste servicetypen (Reliable Services, uitvoerbare gast-bestanden en containers). Als u knooppunttagging wilt gebruiken, moet u versie 8.0 of hoger van de Service Fabric uitvoeren.

In de rest van dit artikel worden manieren beschreven om het taggen van knooppunt in of uit te schakelen en worden voorbeelden gegeven van het gebruik van deze functie.


## <a name="describing-dynamic-node-tags"></a>Dynamische knooppunttags beschrijven
Services kunnen de tags opgeven die ze nodig hebben. Er zijn twee typen tags:
* **Tags die vereist zijn voor plaatsing** beschrijven een set tags, die alleen vereist zijn voor serviceplaatsing. Zodra de replica is geplaatst, kunnen deze tags worden verwijderd zonder de service te onderbreken. Als een van deze tags van het knooppunt wordt verwijderd, blijft de servicereplica werken en Service Fabric de service niet verwijderen

* **Tags die nodig zijn om uit** te voeren, beschrijven een set tags die vereist zijn voor zowel de plaatsing als het uitvoeren van de service. Als een van de vereiste tags wordt verwijderd, Service Fabric de service naar een ander knooppunt waar deze tags zijn opgegeven.

Voorbeeld: Vereist voor plaatsingstags kan worden gebruikt wanneer u een soort containeractivtorservice gebruikt en u die service nodig hebt om uw container te plaatsen. Zodra de container wordt geactiveerd, hebt u geen activator meer nodig en kunt u de tag die aan de container is gekoppeld, verwijderen, maar de container moet actief blijven.
Vereist voor het uitvoeren van tags kan worden gebruikt wanneer u een factureringsservice hebt, wat handig is om samen te werken met de gebruikers gerichte service. Wanneer de factureringsservice mislukt op het knooppunt, verwijdert u de tag die aan het knooppunt is gekoppeld en wordt de gebruikers gerichte service verplaatst naar een ander knooppunt met de factureringsservice en de bijbehorende tag.

Een tag of set tags kan worden toegevoegd, bijgewerkt of verwijderd uit één knooppunt met behulp van standaard Service Fabric-interfacemechanismen zoals C#-API's, REST API's of PowerShell-opdrachten.

> [!NOTE]
> Service Fabric onderhoudt geen UD-/FD-distributies bij het gebruik van knooppunttags. Beheer knooppunttags op de juiste wijze, zodat u geen FD-/UD-schendingen krijgt vanwege een slechte distributie van tags over domeinen.

## <a name="enabling-dynamic-node-tags"></a>Dynamische knooppunttags inschakelen
Als u wilt dat deze functie werkt, moet u NodeTaggingEnabled-configuratie inschakelen in de sectie PlacementAndLoadBalancing van het clustermanifest met behulp van XML of JSON:

``` xml
<Section Name="PlacementAndLoadBalancing">
     <Parameter Name="NodeTaggingEnabled" Value="true" />
</Section>
```

via ClusterConfig.jsop voor zelfstandige implementaties of Template.jsvoor in Azure gehoste clusters:

```json
"fabricSettings": [
  {
    "name": "PlacementAndLoadBalancing",
    "parameters": [
      {
          "name": " NodeTaggingEnabled ",
          "value": "true"
      }
    ]
  }
]
```

## <a name="setting-dynamic-node-tags"></a>Dynamische knooppunttags instellen

### <a name="using-powershell"></a>PowerShell gebruiken

Knooppunttags toevoegen aan het knooppunt:

```posh
Add-ServiceFabricNodeTags -NodeName "DB.1" -NodeTags @("SampleTag1", "SampleTag2")
```
Met deze opdracht worden de tags SampleTag1 en SampleTag2 toegevoegd aan knooppunt DB.1.

Verwijder knooppunttags uit het knooppunt:

```posh
Remove-ServiceFabricNodeTags -NodeName "DB.1" -NodeTags @("SampleTag1", "SampleTag2")
```
Met deze opdracht worden de tags SampleTag1 en SampleTag2 in knooppunt DB.1 verwijderd.

### <a name="using-c-apis"></a>C#-API's gebruiken

Knooppunttags toevoegen aan het knooppunt:

```csharp
FabricClient fabricClient = new FabricClient();
List<string> nodeTagsList = new List<string>();
nodeTagsList.Add("SampleTag1");
nodeTagsList.Add("SampleTag2");
await fabricClient.ClusterManager.AddNodeTagsAsync("DB.1", nodeTagsList);
```

Verwijder knooppunttags uit het knooppunt:

```csharp
FabricClient fabricClient = new FabricClient();
List<string> nodeTagsList = new List<string>();
nodeTagsList.Add("SampleTag1");
nodeTagsList.Add("SampleTag2");
await fabricClient.ClusterManager.RemoveNodeTagsAsync("DB.1", nodeTagsList);
```

## <a name="setting-required-tags-for-services"></a>Vereiste tags instellen voor services

### <a name="using-powershell"></a>PowerShell gebruiken

Nieuwe service maken:

```posh
New-ServiceFabricService -ApplicationName fabric:/HelloWorld -ServiceName fabric:/HelloWorld/svc1 -ServiceTypeName HelloWorldStateful -Stateful -PartitionSchemeSingleton -TargetReplicaSetSize 5 -MinReplicaSetSize 3 -TagsRequiredToRun @("SampleTag1") - TagsRequiredToPlace @("SampleTag2")
```
Met deze opdracht maakt u een service waarvoor 'SampleTag2' aanwezig moet zijn op een knooppunt om de service daar te kunnen plaatsen, en 'SampleTag1' om de service op dat knooppunt te kunnen blijven uitvoeren.

Bestaande service bijwerken:

```posh
Update-ServiceFabricService -Stateful -ServiceName fabric:/myapp/test -TagsRequiredToRun @("SampleTag1") -TagsRequiredToPlace @("SampleTag2")
```
Met deze opdracht wordt een service bijgewerkt waarvoor 'SampleTag2' op een knooppunt aanwezig moet zijn om de service daar te kunnen plaatsen, en 'SampleTag1' om de service op dat knooppunt te kunnen blijven uitvoeren.

### <a name="using-c-apis"></a>C#-API's gebruiken

Nieuwe service maken:

```csharp
FabricClient fabricClient = new FabricClient();
StatefulServiceDescription serviceDescription = new StatefulServiceDescription();
//set up the rest of the ServiceDescription
ServiceTags serviceTags = new ServiceTags();
serviceTags.TagsRequiredToPlace.Add("SampleTag1");
serviceTags.TagsRequiredToRun.Add("SampleTag2");
serviceDescription.ServiceTags = serviceTags;
await fabricClient.ServiceManager.CreateServiceAsync(serviceDescription);
```

Bestaande service bijwerken:

```csharp
FabricClient fabricClient = new FabricClient();
StatefulServiceUpdateDescription serviceUpdate = new StatefulServiceUpdateDescription();
ServiceTags serviceTags = new ServiceTags();
serviceTags.TagsRequiredToPlace.Add("SampleTag1");
serviceTags.TagsRequiredToRun.Add("SampleTag2");
serviceUpdate.ServiceTags = serviceTags;
await fabricClient.ServiceManager.UpdateServiceAsync(new Uri("fabric:/AppName/ServiceName"), serviceUpdate);
```

Al deze opdrachten zijn van toepassing op staatloze services.

## <a name="next-steps"></a>Volgende stappen
Meer informatie over [plaatsingsbeperkingen](service-fabric-cluster-resource-manager-configure-services.md)
