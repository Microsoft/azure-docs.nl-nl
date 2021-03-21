---
title: Het defragmenteren van metrische gegevens in azure Service Fabric
description: Meer informatie over het gebruik van defragmentatie, of verpakken, als een strategie voor metrische gegevens in Service Fabric. Deze techniek is handig voor zeer grote Services.
author: masnider
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 6dee9a845df305b06153d99c2bafe6d74524a139
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102449200"
---
# <a name="defragmentation-of-metrics-and-load-in-service-fabric"></a>Gegevens defragmenteren en laden in Service Fabric
De standaard strategie voor het Service Fabric cluster resource manager voor het beheren van metrische gegevens voor belasting in het cluster is om de belasting te verdelen. Ervoor zorgen dat knoop punten gelijkmatig worden gebruikt voor komen van warme en koude vlekken die leiden tot zowel de verwerkings capaciteit als de hoeveelheid verspilde resources. Het distribueren van werk belastingen in het cluster is ook de veiligste op het gebied van verouderde storingen omdat het ervoor zorgt dat een storing een groot percentage van een bepaalde werk belasting niet in beslag neemt. 

De Service Fabric cluster resource manager ondersteunt een andere strategie voor het beheren van de belasting, die wordt gedefragmenteerd. Defragmenteren betekent dat in plaats van het gebruik van een metriek over het cluster te verdelen, het wordt geconsolideerd. Consolidatie is slechts een inversie van de standaard taakverdelings strategie. in plaats van de gemiddelde standaard afwijking van metrische belasting te minimaliseren, probeert cluster resource manager deze te verg Roten.

## <a name="when-to-use-defragmentation"></a>Wanneer gebruikt u Defragmentatie?
Bij het distribueren van de belasting in het cluster worden een aantal resources op elk knoop punt gebruikt. Sommige werk belastingen maken services die uitzonderlijk groot zijn en de meeste of alle knoop punten gebruiken. In dergelijke gevallen is het mogelijk dat wanneer er grote werk belastingen worden gemaakt, er op geen enkel knoop punt voldoende ruimte is om deze uit te voeren. Grote werk belastingen zijn geen probleem in Service Fabric. in deze gevallen bepaalt het cluster resource manager of het cluster opnieuw moet worden ingedeeld om ruimte te maken voor deze grote werk belasting. In de tussen tijd moet de werk belasting echter wachten om te worden gepland in het cluster.

Als er veel services en status zijn om door te gaan, kan het lang duren voordat de grote werk belasting in het cluster is geplaatst. Dit is waarschijnlijker als andere workloads in het cluster ook groot zijn, waardoor het langer duurt om het opnieuw in te delen. De gemeten duur van het Service Fabric team in simulaties van dit scenario. We hebben geconstateerd dat het maken van grote Services veel langer duurde zodra het gebruik van clusters hoger is dan 30% en 50%. Om dit scenario te verwerken, hebben we defragmentatie geïntroduceerd als een evenwichtige strategie. We hebben vastgesteld dat voor grote werk belastingen, in het bijzonder wanneer de aanmaak tijd belang rijk was, de defragmentatie werkelijk heeft bijgedragen dat nieuwe workloads in het cluster worden gepland.

U kunt metrische gegevens voor defragmentatie configureren zodat de cluster resource manager proactief de belasting van de services in minder knoop punten kan proberen te verbreden. Dit helpt ervoor te zorgen dat er bijna altijd ruimte is voor grote Services zonder het cluster opnieuw te organiseren. U hoeft het cluster niet opnieuw in te delen, zodat u snel grote werk belastingen kunt maken.

De meeste mensen hebben geen defragmentatie nodig. Services zijn meestal klein, dus het is niet moeilijk om ruimte te vinden in het cluster. Als er een andere organisatie kan worden uitgevoerd, wordt deze snel weer, omdat de meeste services klein zijn en snel en parallel kunnen worden verplaatst. Als u echter over grote Services beschikt en deze snel moet worden gemaakt, is de strategie voor defragmentatie voor u. We bespreken de compromissen van het gebruik van defragmentatie volgende. 

## <a name="defragmentation-tradeoffs"></a>Compromissen van defragmentatie
Met defragmentatie kunnen impactfulness fouten toenemen, omdat er meer services worden uitgevoerd op knoop punten die niet werken. Defragmentatie kan ook kosten verhogen, omdat resources in het cluster moeten worden bewaard in de reserve ring, zodat grote werk belastingen kunnen worden gemaakt.

Het volgende diagram geeft een visuele weer gave van twee clusters, een die is gedefragmenteerd en een die niet. 

<center>

![Evenwichtige en gefragmenteerde clusters vergelijken][Image1]
</center>

In het gebalanceerde geval moet u rekening houden met het aantal verplaatsingen dat nodig zou zijn om een van de grootste service objecten te plaatsen. In het gedefragmenteerde cluster kan de grote werk belasting op knoop punten vier of vijf worden geplaatst zonder te hoeven wachten tot andere services zijn verplaatst.

## <a name="defragmentation-pros-and-cons"></a>Voor delen en nadelen van defragmentatie
Wat zijn de andere conceptuele afwegingen? Hieronder vindt u een tabel met tips voor het volgende:

| Defragmentatie-professionals | Nadelen van defragmentatie |
| --- | --- |
| Maakt het sneller om grote services te maken |Concentreert de belasting op minder knoop punten, waardoor de conflicten worden verkleind |
| Maakt lagere gegevens verplaatsing mogelijk tijdens het maken |Storingen kunnen van invloed zijn op meer services en leiden tot meer verloop |
| Biedt uitgebreide beschrijving van vereisten en het vrijmaken van ruimte |Complexere algemene configuratie van bron beheer |

U kunt gefragmenteerde en normale metrische gegevens in hetzelfde cluster combi neren. De cluster resource manager probeert de gegevens over de defragmentatie zo veel mogelijk samen te voegen tijdens het uitspreiden van de andere. De resultaten van het combi neren van defragmentatie en onderverdelings strategieën zijn afhankelijk van verschillende factoren, waaronder:
  - het aantal metrische gegevens over de verdeling versus het aantal metrische gegevens over de defragmentatie
  - Of een service beide soorten metrische gegevens gebruikt 
  - het metrische gewicht
  - huidige metrische belasting
  
Experimenteren is vereist om de exacte configuratie te bepalen die nodig is. We raden u aan om uw workloads grondig te meten voordat u metrische gegevens voor defragmentatie in productie inschakelt. Dit geldt met name bij het combi neren van defragmentatie en evenwichtige metrische gegevens binnen dezelfde service. 

## <a name="configuring-defragmentation-metrics"></a>Metrische gegevens voor defragmentatie configureren
Het configureren van metrische gegevens voor defragmentatie is een wereld wijd besluit in het cluster en afzonderlijke meet waarden kunnen worden geselecteerd voor defragmentatie. De volgende configuratie fragmenten laten zien hoe u metrische gegevens voor defragmentatie kunt configureren. In dit geval wordt "Metric1" geconfigureerd als een gegevensverdelings metriek, terwijl "Metric2" normaal gesp roken blijft. 

ClusterManifest.xml:

```xml
<Section Name="DefragmentationMetrics">
    <Parameter Name="Metric1" Value="true" />
    <Parameter Name="Metric2" Value="false" />
</Section>
```

via ClusterConfig.jsop voor zelfstandige implementaties of Template.jsop voor door Azure gehoste clusters:

```json
"fabricSettings": [
  {
    "name": "DefragmentationMetrics",
    "parameters": [
      {
          "name": "Metric1",
          "value": "true"
      },
      {
          "name": "Metric2",
          "value": "false"
      }
    ]
  }
]
```


## <a name="next-steps"></a>Volgende stappen
- Cluster resource manager heeft veel opties voor het beschrijven van het cluster. Lees dit artikel over [het beschrijven van een service Fabric cluster](service-fabric-cluster-resource-manager-cluster-description.md) voor meer informatie.
- Metrische gegevens zijn de manier waarop de Service Fabric cluster resource manager het verbruik en de capaciteit in het cluster beheert. Raadpleeg [dit artikel](service-fabric-cluster-resource-manager-metrics.md) voor meer informatie over metrische gegevens en hoe u deze kunt configureren.

[Image1]:./media/service-fabric-cluster-resource-manager-defragmentation-metrics/balancing-defrag-compared.png
