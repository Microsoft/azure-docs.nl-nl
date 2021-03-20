---
title: Uw Azure Service Fabric-cluster sluiten
description: Een inleiding tot het verdelen van uw cluster met de Service Fabric cluster resource manager.
author: masnider
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 192aca589c3b1e660667dbe8377afe7802b56f17
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "93146191"
---
# <a name="balancing-your-service-fabric-cluster"></a>Balancing your service Fabric-cluster
De Service Fabric cluster resource manager ondersteunt dynamische belasting wijzigingen, waarbij wordt gecommuniceerd op toevoegingen of verwijderingen van knoop punten of services. Hiermee worden ook de schendingen van beperkingen automatisch gecorrigeerd en wordt het cluster proactief herbalanceerd. Maar hoe vaak worden er acties uitgevoerd en wat wordt geactiveerd?

Er zijn drie verschillende werk categorieën die door cluster resource manager worden uitgevoerd. Dit zijn:

1. Plaatsing: dit stadium behandelt het plaatsen van stateful replica's of stateless instanties die ontbreken. Plaatsing omvat zowel nieuwe services als het verwerken van stateful replica's of stateless instanties die zijn mislukt. Het verwijderen en neerzetten van replica's of exemplaren wordt hier behandeld.
2. Controle van beperkingen: deze fase controleert de schendingen van de verschillende plaatsings beperkingen (regels) binnen het systeem en corrigeert deze. Voor beelden van regels zijn zaken als het garanderen dat knoop punten niet meer capaciteit hebben en dat aan de plaatsings beperkingen van een service wordt voldaan.
3. Balancing: in deze fase wordt gecontroleerd of herverdeling nood zakelijk is op basis van het geconfigureerde saldo niveau voor verschillende metrische gegevens. Als dit het geval is, wordt geprobeerd een indeling in het cluster te vinden die meer evenwichtig is.

## <a name="configuring-cluster-resource-manager-timers"></a>Timers voor cluster resource managers configureren
De eerste set besturings elementen rond Balancing is een set timers. Deze timers bepalen hoe vaak de cluster resource manager het cluster onderzoekt en corrigerende maat regelen neemt.

Elk van de verschillende typen verbeteringen die de cluster resource manager kan maken, wordt bepaald door een andere timer die de frequentie bepaalt. Wanneer elke timer wordt geactiveerd, wordt de taak gepland. Standaard de Resource Manager:

* Hiermee wordt de status gescand en worden updates toegepast (zoals de opname dat een knoop punt niet beschikbaar is) elke 1/tiende van een seconde
* Hiermee wordt elke seconde de vlag voor de plaatsings controle ingesteld
* Hiermee stelt u de controle vlag beperking elke seconde in
* Hiermee stelt u de Balancing-vlag elke vijf seconden in

Hieronder vindt u voor beelden van de configuratie met betrekking tot deze timers:

ClusterManifest.xml:

``` xml
        <Section Name="PlacementAndLoadBalancing">
            <Parameter Name="PLBRefreshGap" Value="0.1" />
            <Parameter Name="MinPlacementInterval" Value="1.0" />
            <Parameter Name="MinConstraintCheckInterval" Value="1.0" />
            <Parameter Name="MinLoadBalancingInterval" Value="5.0" />
        </Section>
```

via ClusterConfig.jsop voor zelfstandige implementaties of Template.jsop voor door Azure gehoste clusters:

```json
"fabricSettings": [
  {
    "name": "PlacementAndLoadBalancing",
    "parameters": [
      {
          "name": "PLBRefreshGap",
          "value": "0.10"
      },
      {
          "name": "MinPlacementInterval",
          "value": "1.0"
      },
      {
          "name": "MinConstraintCheckInterval",
          "value": "1.0"
      },
      {
          "name": "MinLoadBalancingInterval",
          "value": "5.0"
      }
    ]
  }
]
```

Vandaag voert de cluster resource manager slechts een van deze acties tegelijk uit. Daarom verwijzen we naar deze timers als ' minimale intervallen ' en de acties die worden uitgevoerd wanneer de timers worden ingesteld als ' vlaggen instellen '. De cluster resource manager zorgt er bijvoorbeeld voor dat in behandeling zijnde aanvragen voor het maken van services worden uitgevoerd voordat het cluster wordt gebalanceerd. Zoals u kunt zien op basis van de standaard tijds intervallen die u hebt opgegeven, wordt door cluster resource manager gescand wat er vaak nodig is. Dit betekent normaal gesp roken dat de set wijzigingen die tijdens elke stap zijn aangebracht, klein is. Als u kleine wijzigingen regel matig maakt, kan cluster bron beheer reageren wanneer er iets gebeurt in het cluster. De standaardtimers bieden enige batch verwerking omdat veel van de verschillende typen gebeurtenissen tegelijkertijd tegelijkertijd worden uitgevoerd. 

Als knoop punten bijvoorbeeld mislukken, kunnen ze ervoor zorgen dat alle fout domeinen tegelijk worden uitgevoerd. Al deze fouten worden vastgelegd tijdens de volgende status update na de *PLBRefreshGap*. De correcties worden bepaald tijdens de volgende plaatsing, controle van de beperking en taak verdeling. Standaard wordt de cluster resource manager niet gescand door de uren aan wijzigingen in het cluster en wordt geprobeerd om alle wijzigingen in één keer op te lossen. Dit zou leiden tot bursts van het verloop.

De cluster resource manager heeft ook aanvullende informatie nodig om te bepalen of het cluster niet in evenwicht is. Er zijn twee andere configuratie onderdelen: *BalancingThresholds* en *ActivityThresholds*.

## <a name="balancing-thresholds"></a>Drempel waarden voor Balancing
Een drempel waarde voor Balancing is het belangrijkste besturings element voor het activeren van herverdeling. De drempel waarde voor de balans van een metriek is een _verhouding_. Als de belasting voor een metriek op het meest geladen knoop punt gedeeld door de hoeveelheid belasting op het minst geladen knoop punt groter is dan de *BalancingThreshold* van de metriek, wordt het cluster niet in balans gebracht. Als gevolg van het afbalanceren wordt de volgende keer dat de cluster resource Manager controleert, geactiveerd. De timer *MinLoadBalancingInterval* definieert hoe vaak de cluster resource manager moet controleren of herverdeling nood zakelijk is. Controleren betekent niet dat er iets gebeurt. 

Drempel waarden voor de verdeling worden per metriek gedefinieerd als onderdeel van de cluster definitie. Raadpleeg [dit artikel](service-fabric-cluster-resource-manager-metrics.md)voor meer informatie over metrische gegevens.

ClusterManifest.xml

```xml
    <Section Name="MetricBalancingThresholds">
      <Parameter Name="MetricName1" Value="2"/>
      <Parameter Name="MetricName2" Value="3.5"/>
    </Section>
```

via ClusterConfig.jsop voor zelfstandige implementaties of Template.jsop voor door Azure gehoste clusters:

```json
"fabricSettings": [
  {
    "name": "MetricBalancingThresholds",
    "parameters": [
      {
          "name": "MetricName1",
          "value": "2"
      },
      {
          "name": "MetricName2",
          "value": "3.5"
      }
    ]
  }
]
```

<center>

![Voor beeld van een Balancing drempel][Image1]
</center>

In dit voor beeld verbruikt elke service één eenheid van een bepaalde metriek. In het bovenste voor beeld is de maximale belasting op een knoop punt vijf en de minimum waarde is twee. Stel dat de drempel waarde voor de Balancing voor deze metriek drie is. Omdat de verhouding in het cluster 5/2 = 2,5 is en kleiner is dan de opgegeven taakverdelings drempel van drie, wordt het cluster evenwichtig. Er wordt geen verdeling geactiveerd wanneer de cluster resource Manager controleert.

In het onderste voor beeld is de maximale belasting op een knoop punt 10, terwijl de minimum waarde twee, wat resulteert in een verhouding van vijf. Vijf is groter dan de ingestelde drempel waarde voor de verdeling van drie voor die metriek. Als gevolg hiervan wordt een herverdelings uitvoering gepland wanneer de taak verdeling de volgende keer wordt geactiveerd. In een situatie als deze belasting wordt doorgaans gedistribueerd naar Knooppunt3. Omdat de Service Fabric cluster resource manager geen Greedy-benadering gebruikt, kan een bepaalde belasting ook worden gedistribueerd naar Knooppunt2. 

<center>

![Voorbeeld acties voor drempel waarden voor Balancing][Image2]
</center>

> [!NOTE]
> "Balancing" behandelt twee verschillende strategieën voor het beheren van de belasting in uw cluster. De standaard strategie die door cluster resource manager wordt gebruikt, is het verdelen van de belasting over de knoop punten in het cluster. De andere strategie is [Defragmentatie](service-fabric-cluster-resource-manager-defragmentation-metrics.md). Defragmentatie wordt uitgevoerd tijdens de uitvoering van dezelfde verdeling. De Balancing-en defragmentatie strategieën kunnen worden gebruikt voor verschillende metrische gegevens binnen hetzelfde cluster. Een service kan metrische gegevens over de verdeling en defragmentatie hebben. Voor metrische gegevens van defragmentatie wordt de verhouding van de belasting in het cluster geactiveerd wanneer de verdeling _lager is dan_ de drempel waarde voor de balans. 
>

Onder de drempel waarde voor de balans is geen expliciet doel. Drempel waarden voor balances zijn slechts een *trigger*. Bij het uitvoeren van Balancing wordt door cluster resource manager bepaald welke verbeteringen er kunnen worden aangebracht, indien van toepassing. Maar omdat een gebalanceerde zoek opdracht niet wordt gestart, betekent dat niets dat wordt verplaatst. Soms is het cluster niet in balans, maar is het ook te beperkt om te corrigeren. Het is ook mogelijk dat de verbeteringen verplaatsingen hebben die te [kostbaar](service-fabric-cluster-resource-manager-movement-cost.md)zijn.

## <a name="activity-thresholds"></a>Drempel waarden voor activiteit
Soms zijn knoop punten relatief niet in balans, maar is de *totale* hoeveelheid belasting in het cluster laag. Het ontbreken van een belasting kan een tijdelijke dip zijn of omdat het cluster nieuw is en alleen Boots trapt. In beide gevallen is het mogelijk dat u niet wilt dat de tijd verdeling van het cluster afneemt, omdat er weinig te worden gedaan. Als het cluster is gebalanceerd, kunt u netwerk-en reken bronnen uitgeven om dingen te verplaatsen zonder een grote *absolute* verschillen te maken. Om te voor komen dat er onnodig wordt verplaatst, is er een ander besturings element bekend als de drempel waarden voor de activiteit. Met de drempel waarde voor activiteiten kunt u een absolute ondergrens voor activiteit opgeven. Als er geen knoop punt meer is dan deze drempel waarde, wordt de taak verdeling niet geactiveerd, zelfs niet als aan de drempel waarde voor de balans wordt voldaan.

Stel dat we onze drempel waarde voor de balans van drie voor deze metrische waarde behouden. We hebben ook een drempel waarde voor de activiteit van 1536. In het eerste geval, terwijl het cluster niet in balans wordt gebracht volgens de drempel waarde, is er geen knoop punt dat voldoet aan de drempel waarde voor de activiteit, dus er gebeurt niets. In het onderste voor beeld bevindt Knooppunt1 zich boven de drempel waarde voor de activiteit. Omdat de drempel waarde voor de balans en de drempel waarde voor de activiteit voor de metriek worden overschreden, wordt de taak verdeling gepland. Laten we bijvoorbeeld eens kijken naar het volgende diagram: 

<center>

![Voor beeld van een drempel waarde voor activiteit][Image3]
</center>

Net als bij het verdelen van de drempel waarden, worden de drempel waarden voor de activiteit per metriek gedefinieerd via de cluster definitie:

ClusterManifest.xml

``` xml
    <Section Name="MetricActivityThresholds">
      <Parameter Name="Memory" Value="1536"/>
    </Section>
```

via ClusterConfig.jsop voor zelfstandige implementaties of Template.jsop voor door Azure gehoste clusters:

```json
"fabricSettings": [
  {
    "name": "MetricActivityThresholds",
    "parameters": [
      {
          "name": "Memory",
          "value": "1536"
      }
    ]
  }
]
```

Balancing-en activiteit drempels zijn beide gebonden aan een specifieke metrische taak verdeling worden alleen geactiveerd als zowel de drempel waarde voor de balans als de drempel voor de activiteit voor dezelfde metriek wordt overschreden.

> [!NOTE]
> Als deze para meter niet wordt opgegeven, is de drempel waarde voor de Balancing van een metriek 1 en is de drempel waarde voor de activiteit 0. Dit betekent dat cluster resource manager probeert te blijven dat de metrische gegevens voor een bepaalde belasting perfect worden verdeeld. Als u aangepaste metrische gegevens gebruikt, is het raadzaam om expliciet uw eigen Balancing-en activiteiten drempels voor uw metrische gegevens te definiëren. 
>

## <a name="balancing-services-together"></a>Balancing Services samen
Of het cluster niet is gebalanceerd of niet is een beslissing voor het hele cluster. De manier waarop we het probleem gaan verhelpen, is echter het verplaatsen van afzonderlijke service replica's en exemplaren. Dit is zinvol, rechts? Als geheugen op één knoop punt is gestapeld, kunnen er meerdere replica's of instanties aan worden bijgedragen. Het oplossen van het evenwicht kan ertoe leiden dat een van de stateful replica's of stateless instanties die gebruikmaken van de niet-gebalanceerde metrische gegevens worden verplaatst.

In sommige gevallen wordt een service die niet is gebalanceerd, verplaatst (Onthoud de bespreking van lokaal en wereld wijde gewichten eerder). Waarom wordt een service verplaatst wanneer alle metrische gegevens van de service zijn gesaldeerd? Laten we een voor beeld zien:

- Stel dat er vier Services, Service1, Service2, Service3 en Service4 zijn. 
- Service1 rapporteert metrische gegevens Metric1 en Metric2. 
- Service2 rapporteert metrische gegevens Metric2 en Metric3. 
- Service3 rapporteert metrische gegevens Metric3 en Metric4.
- Service4 rapporteert metrische Metric99. 

Surely u kunt zien waar we hier naartoe gaan: er is een keten. We hebben niet echt vier onafhankelijke services, we hebben drie services die verwant zijn en één die eigen zijn.

<center>

![Diagram dat laat zien hoe services met elkaar worden gebalanceerd.][Image4]
</center>

Vanwege deze keten is het mogelijk dat een onevenwicht in de metrische gegevens 1-4 kan leiden tot replica's of instanties die tot Services 1-3 behoren. We weten ook dat een onevenwicht in de metrische gegevens 1, 2 of 3 geen bewegingen kan veroorzaken in Service4. Er zou geen punt zijn omdat het verplaatsen van de replica's of instanties die tot Service4 leiden, absoluut niets kan doen met invloed op het saldo van metrische gegevens 1-3.

Cluster resource manager heeft automatisch een resultaat van de gerelateerde services. Het toevoegen, verwijderen of wijzigen van de metrische gegevens voor Services kan van invloed zijn op hun relaties. Zo kunnen bijvoorbeeld tussen twee uitvoeringen van de Balanced service2 zijn bijgewerkt om Metric2 te verwijderen. Hiermee wordt de keten onderbroken tussen Service1 en Service2. In plaats van twee groepen gerelateerde services zijn er drie:

<center>

![Diagram dat laat zien dat cluster resource Manager bepaalt welke services gerelateerd zijn.][Image5]
</center>

## <a name="next-steps"></a>Volgende stappen
* Metrische gegevens zijn de manier waarop de Service Fabric cluster resource manager het verbruik en de capaciteit in het cluster beheert. Raadpleeg [dit artikel](service-fabric-cluster-resource-manager-metrics.md) voor meer informatie over metrische gegevens en hoe u deze kunt configureren.
* De verplaatsings kosten zijn één manier om aan te geven dat de cluster resource manager bepaalde services duurder kan verplaatsen dan andere. Raadpleeg [dit artikel](service-fabric-cluster-resource-manager-movement-cost.md) voor meer informatie over de verplaatsings kosten
* Cluster resource manager heeft verschillende beperkingen die u kunt configureren om het verloop in het cluster te vertragen. Ze zijn normaal gesp roken niet nodig, maar als u ze nodig hebt, kunt u [hier](service-fabric-cluster-resource-manager-advanced-throttling.md) meer informatie hierover vinden
* Cluster resource manager kan subclustering herkennen en verwerken (een situatie die soms optreedt wanneer u placement constraints en Balancing gebruikt). Zie [hier](cluster-resource-manager-subclustering.md) voor meer informatie over hoe clusters kunnen worden beïnvloed en hoe u deze kunt afhandelen.

[Image1]:./media/service-fabric-cluster-resource-manager-balancing/cluster-resrouce-manager-balancing-thresholds.png
[Image2]:./media/service-fabric-cluster-resource-manager-balancing/cluster-resource-manager-balancing-threshold-triggered-results.png
[Image3]:./media/service-fabric-cluster-resource-manager-balancing/cluster-resource-manager-activity-thresholds.png
[Image4]:./media/service-fabric-cluster-resource-manager-balancing/cluster-resource-manager-balancing-services-together1.png
[Image5]:./media/service-fabric-cluster-resource-manager-balancing/cluster-resource-manager-balancing-services-together2.png
