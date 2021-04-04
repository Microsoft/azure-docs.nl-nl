---
title: Het laden van Azure Service Fabric-apps beheren met metrische gegevens
description: Meer informatie over het configureren en gebruiken van metrische gegevens in Service Fabric voor het beheren van het gebruik van service bronnen.
author: masnider
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: masnider
ms.custom: devx-track-csharp
ms.openlocfilehash: 2a7dedea2937c9cafb4216da3628aa1360ad6993
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92173009"
---
# <a name="managing-resource-consumption-and-load-in-service-fabric-with-metrics"></a>Resource gebruik beheren en laden in Service Fabric met metrische gegevens
*Metrische gegevens* zijn de resources die door uw services worden aangehouden en die worden geboden door de knoop punten in het cluster. Een metriek is alles wat u wilt beheren om de prestaties van uw services te verbeteren of te controleren. U kunt bijvoorbeeld het geheugen verbruik bekijken om te weten of uw service overbelast is. Een ander gebruik is om te bepalen of de service elders kan worden verplaatst, waarbij geheugen minder beperkt is om betere prestaties te krijgen.

Dingen zoals geheugen, schijf en CPU-gebruik zijn voor beelden van metrische gegevens. Deze metrische gegevens zijn fysieke metrische gegevens, resources die overeenkomen met fysieke resources op het knoop punt dat moet worden beheerd. Metrische gegevens kunnen ook worden (en meestal) logische metrische gegevens. Logische metrische gegevens zijn dingen zoals "MyWorkQueueDepth" of "MessagesToProcess" of "TotalRecords". Logische metrische gegevens worden door de toepassing gedefinieerd en komen indirect overeen met een fysiek Resource verbruik. Logische metrische gegevens zijn gebruikelijk omdat het lastig is om het verbruik van fysieke resources per service te meten en te rapporteren. De complexiteit van het meten en rapporteren van uw eigen fysieke metrische gegevens is ook waarom Service Fabric een aantal standaard metrische gegevens biedt.

## <a name="default-metrics"></a>Standaard waarden
Stel dat u aan de slag wilt gaan met het schrijven en implementeren van uw service. Op dit moment weet u niet welke fysieke of logische resources er worden gebruikt. Dat is prima. De Service Fabric cluster resource manager maakt gebruik van enkele standaard waarden wanneer er geen andere metrieken zijn opgegeven. Dit zijn:

  - PrimaryCount: aantal primaire replica's op het knoop punt 
  - ReplicaCount-telling van de totale stateful replica's op het knoop punt
  - Aantal-aantal service objecten (stateless en stateful) op het knoop punt

| Metrisch | Stateless instantie laden | Stateful secundaire belasting | Stateful primaire belasting | Gewicht |
| --- | --- | --- | --- | --- |
| PrimaryCount |0 |0 |1 |Hoog |
| ReplicaCount |0 |1 |1 |Normaal |
| Count |1 |1 |1 |Beperkt |


Voor basis werkbelastingen bieden de standaard metrische gegevens een goede verdeling van het werk in het cluster. In het volgende voor beeld zien we wat er gebeurt wanneer we twee services maken en afhankelijk zijn van de standaard waarden voor de taak verdeling. De eerste service is een stateful service met drie partities en de grootte van de doel replica is ingesteld op drie. De tweede service is een stateless service met één partitie en een aantal exemplaren van drie.

Dit is het resultaat:

<center>

![Cluster indeling met standaard metrische gegevens][Image1]
</center>

Let op het volgende:
  - Primaire replica's voor de stateful service worden gedistribueerd over meerdere knoop punten
  - Replica's voor dezelfde partitie bevinden zich op verschillende knoop punten
  - Het totale aantal Primaries en de secundaire zones wordt gedistribueerd in het cluster
  - Het totale aantal service objecten is gelijkmatig toegewezen op elk knoop punt

Groot!

De standaard waarden werken prima als een start. De standaard gegevens worden echter tot nu toe getransporteerd. Bijvoorbeeld: wat is de kans dat het gekozen partitie schema resulteert in perfect zelfs gebruik door alle partities? Wat is de kans dat de belasting voor een bepaalde service constant is, of zelfs op dezelfde manier als in meerdere partities?

U kunt uitvoeren met alleen de standaard waarden. Dit betekent echter meestal dat uw cluster gebruik lager en groter is dan u wilt. Dit komt doordat de standaard waarden niet adaptief zijn en alles vermoeden dat alles gelijk is. Bijvoorbeeld een primair die bezet is en een die niet beide bijdragen aan de PrimaryCount-metriek. In het ergste geval kan het gebruik van alleen de standaard metrieken ook leiden tot overbelasting van knoop punten die prestatie problemen veroorzaken. Als u geïnteresseerd bent in het meest uit uw cluster halen en prestatie problemen wilt voor komen, moet u aangepaste metrische gegevens en dynamische belasting rapportage gebruiken.

## <a name="custom-metrics"></a>Aangepaste metrische gegevens
Metrische gegevens worden geconfigureerd op basis van de service-instance bij het maken van de service.

Elke metriek heeft enkele eigenschappen die deze beschrijven: een naam, een gewicht en een standaard belasting.

* Metrische naam: de naam van de metriek. De naam van de metriek is een unieke id voor de metriek in het cluster van het perspectief van de Resource Manager.
* Gewicht: metrisch gewicht bepaalt hoe belang rijk deze metriek is ten opzichte van de andere metrische gegevens voor deze service.
* Standaard belasting: de standaard belasting wordt anders weer gegeven, afhankelijk van het feit of de service stateless of stateful is.
  * Voor stateless Services heeft elke metriek een enkele eigenschap met de naam DefaultLoad
  * Voor stateful services die u definieert:
    * PrimaryDefaultLoad: de standaard waarde van deze metrische gegevens verbruikt deze service wanneer het een primaire
    * SecondaryDefaultLoad: de standaard waarde van deze metrische gegevens verbruikt deze service wanneer het een secundaire

> [!NOTE]
> Als u aangepaste metrische gegevens definieert en u _ook_ de standaard metrieken wilt gebruiken, moet u de standaard metrieken _expliciet_ toevoegen en gewichten en waarden voor hen definiëren. Dit komt doordat u de relatie tussen de standaard metrische gegevens en uw aangepaste metrische gegevens moet definiëren. Misschien bent u bijvoorbeeld ConnectionCount of WorkQueueDepth meer dan primaire distributie. Het gewicht van de PrimaryCount-meet waarde is standaard hoog, dus u wilt het gemiddelde verminderen wanneer u uw andere metrische gegevens toevoegt om ervoor te zorgen dat ze voor rang hebben.
>

### <a name="defining-metrics-for-your-service---an-example"></a>Metrische gegevens voor uw service definiëren: een voor beeld
Stel dat u de volgende configuratie wilt:

  - Uw service rapporteert een metriek met de naam ' ConnectionCount '
  - U wilt ook de standaard metrische gegevens gebruiken 
  - U hebt enkele metingen uitgevoerd en weet dat normaal gesp roken een primaire replica van de service 20 eenheden van ' ConnectionCount ' in beslag neemt.
  - Secundaire zones gebruiken 5 eenheden van ' ConnectionCount '
  - U weet dat "ConnectionCount" de belangrijkste meet waarde is voor het beheren van de prestaties van deze specifieke service
  - U wilt nog steeds primaire replica's in evenwicht houden. De verdeling van primaire replica's is doorgaans een goed idee. Dit helpt voor komen dat een bepaald knoop punt of fout domein van invloed is op een meerderheid van de primaire replica's. 
  - Anders zijn de standaard waarden nauw keurig

Hier ziet u de code die u zou schrijven om een service met die metrische configuratie te maken:

Code:

```csharp
StatefulServiceDescription serviceDescription = new StatefulServiceDescription();
StatefulServiceLoadMetricDescription connectionMetric = new StatefulServiceLoadMetricDescription();
connectionMetric.Name = "ConnectionCount";
connectionMetric.PrimaryDefaultLoad = 20;
connectionMetric.SecondaryDefaultLoad = 5;
connectionMetric.Weight = ServiceLoadMetricWeight.High;

StatefulServiceLoadMetricDescription primaryCountMetric = new StatefulServiceLoadMetricDescription();
primaryCountMetric.Name = "PrimaryCount";
primaryCountMetric.PrimaryDefaultLoad = 1;
primaryCountMetric.SecondaryDefaultLoad = 0;
primaryCountMetric.Weight = ServiceLoadMetricWeight.Medium;

StatefulServiceLoadMetricDescription replicaCountMetric = new StatefulServiceLoadMetricDescription();
replicaCountMetric.Name = "ReplicaCount";
replicaCountMetric.PrimaryDefaultLoad = 1;
replicaCountMetric.SecondaryDefaultLoad = 1;
replicaCountMetric.Weight = ServiceLoadMetricWeight.Low;

StatefulServiceLoadMetricDescription totalCountMetric = new StatefulServiceLoadMetricDescription();
totalCountMetric.Name = "Count";
totalCountMetric.PrimaryDefaultLoad = 1;
totalCountMetric.SecondaryDefaultLoad = 1;
totalCountMetric.Weight = ServiceLoadMetricWeight.Low;

serviceDescription.Metrics.Add(connectionMetric);
serviceDescription.Metrics.Add(primaryCountMetric);
serviceDescription.Metrics.Add(replicaCountMetric);
serviceDescription.Metrics.Add(totalCountMetric);

await fabricClient.ServiceManager.CreateServiceAsync(serviceDescription);
```

Zo

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 3 -TargetReplicaSetSize 3 -PartitionSchemeSingleton –Metric @("ConnectionCount,High,20,5”,"PrimaryCount,Medium,1,0”,"ReplicaCount,Low,1,1”,"Count,Low,1,1”)
```

> [!NOTE]
> De bovenstaande voor beelden en de rest van dit document beschrijven het beheer van metrische gegevens op basis van een service. Het is ook mogelijk om metrische gegevens te definiëren voor uw services op het niveau van het service _type_ . Dit wordt bereikt door deze op te geven in uw service manifesten. Het definiëren van metrische gegevens op type niveau wordt niet aanbevolen om verschillende redenen. De eerste reden is dat metrische namen vaak specifiek voor een specifieke omgeving zijn. Tenzij er een vast contract aanwezig is, kunt u niet zeker weten dat de metrische gegevens "kernen" in de ene omgeving niet "MiliCores" of "kernen" in andere omgevingen zijn. Als uw metrische gegevens in het manifest zijn gedefinieerd, moet u nieuwe manifesten per omgeving maken. Dit leidt doorgaans tot een verspreiding van verschillende manifesten met alleen kleine verschillen, wat kan leiden tot beheer problemen.  
>
> Metrische belasting wordt doorgaans toegewezen op basis van een service-exemplaar. Stel bijvoorbeeld dat u één exemplaar van de service maakt voor KlantA die deze alleen maar lichter wil gebruiken. U kunt ook een andere maken voor CustomerB die een grotere werk belasting hebben. In dit geval wilt u waarschijnlijk de standaard belasting voor die services aanpassen. Als u metrische gegevens en belasting hebt gedefinieerd via manifesten en u dit scenario wilt ondersteunen, zijn voor elke klant verschillende toepassings-en service typen vereist. De waarden die zijn gedefinieerd bij het maken van de service, overschrijven die in het manifest, zodat u deze kunt gebruiken om de specifieke standaard waarden in te stellen. Dit leidt er echter toe dat de waarden die in de manifesten worden gedeclareerd, niet overeenkomen met die waarmee de service daad werkelijk wordt uitgevoerd. Dit kan leiden tot Verwar ring. 
>

Als herinnering: als u alleen de standaard metrische gegevens wilt gebruiken, hoeft u de verzameling metrische gegevens niet te aanraken of iets speciaal te doen bij het maken van uw service. De standaard metrische gegevens worden automatisch gebruikt wanneer er geen andere worden gedefinieerd. 

Nu gaan we elk van deze instellingen uitvoeriger bekijken en praten over het gedrag dat het beïnvloedt.

## <a name="load"></a>Laden
Het hele punt voor het definiëren van metrische gegevens is dat sommige werk belasting aangeeft. *Laden* is het gedeelte van een bepaalde metriek dat door een service-exemplaar of replica op een bepaald knoop punt wordt verbruikt. De belasting kan op bijna elk gewenst moment worden geconfigureerd. Bijvoorbeeld:

  - De belasting kan worden gedefinieerd wanneer een service wordt gemaakt. Dit type belasting configuratie heet _standaard belasting_.
  - De metrische gegevens, met inbegrip van de standaard belasting, voor een service kunnen worden bijgewerkt nadat de service is gemaakt. Deze metrische update wordt uitgevoerd door _een service_ bij te werken.
  - De belasting voor een bepaalde partitie kan opnieuw worden ingesteld op de standaard waarden voor die service. Deze metrische update heet het _opnieuw instellen_ van het laden van de partitie.
  - De belasting kan worden gerapporteerd per service object, op dynamische wijze tijdens runtime. Deze metrische update wordt _rapportage belasting_ genoemd.
  - Het laden van replica's of instanties van partities kan ook worden bijgewerkt door belasting waarden te rapporteren via een infra structuur-API-aanroep. Deze metrische update wordt _rapportage belasting genoemd voor een partitie_.

Al deze strategieën kunnen gedurende de levens duur worden gebruikt binnen dezelfde service.

## <a name="default-load"></a>Standaard belasting
*Standaard belasting* is de mate waarin elk Service object (stateless exemplaar of stateful replica) van deze service wordt gebruikt. Het cluster resource manager gebruikt dit nummer voor de belasting van het Service object totdat het andere informatie ontvangt, zoals een dynamisch laad rapport. Voor eenvoudigere Services is de standaard belasting een statische definitie. De standaard belasting wordt nooit bijgewerkt en wordt gebruikt voor de levens duur van de service. Standaard belastingen zijn geschikt voor scenario's met eenvoudige capaciteits planning waarbij bepaalde hoeveel heden resources zijn toegewezen aan verschillende werk belastingen en niet worden gewijzigd.

> [!NOTE]
> Raadpleeg [dit artikel](service-fabric-cluster-resource-manager-cluster-description.md#capacity)voor meer informatie over capaciteits beheer en het definiëren van capaciteit voor de knoop punten in uw cluster.
> 

Met cluster resource manager kunnen stateful Services een andere standaard belasting opgeven voor hun Primaries en secundaire zones. Stateless Services kunnen slechts één waarde opgeven die van toepassing is op alle exemplaren. Voor stateful Services zijn de standaard belasting voor primaire en secundaire replica's meestal anders, omdat replica's verschillende soorten werk in elke rol doen. Zo fungeren Primaries doorgaans zowel lees-als schrijf bewerkingen, en verwerken ze de meeste reken kracht, terwijl de secundaire zones niet. Normaal gesp roken is de standaard belasting voor een primaire replica hoger dan de standaard belasting voor secundaire replica's. De reële getallen moeten afhankelijk zijn van uw eigen metingen.

## <a name="dynamic-load"></a>Dynamische belasting
Stel dat u uw service al enige tijd hebt uitgevoerd. Bij sommige bewaking hebt u het volgende gezien:

1. Voor sommige partities of exemplaren van een bepaalde service worden meer resources gebruikt dan andere.
2. Sommige services hebben belasting die gedurende een bepaalde periode varieert.

Er zijn veel dingen die deze typen belasting schommelingen kunnen veroorzaken. Verschillende services of partities worden bijvoorbeeld gekoppeld aan verschillende klanten met verschillende vereisten. Loading kan ook worden gewijzigd omdat de hoeveelheid werk die de service in de loop van de dag in beslag neemt, varieert. Ongeacht de reden is er meestal geen enkel nummer dat u voor standaard kunt gebruiken. Dit geldt met name als u het meeste gebruik wilt maken van het cluster. Elke waarde die u kiest voor de standaard belasting is een deel van de tijd. Onjuiste standaard belasting resulteert in de cluster resource manager over of onder het toewijzen van resources. Als gevolg hiervan hebt u knoop punten die zich boven of onder gebruik bevinden, zelfs als de cluster resource manager denkt dat het cluster in evenwicht is. Standaard loads zijn nog steeds goed, omdat ze informatie geven over de eerste plaatsing, maar ze geen volledig verhaal hebben op echte workloads. Voor een nauw keurige vastleg ging van resource vereisten, kan de cluster resource manager voor elk Service object zijn eigen belasting bijwerken tijdens runtime. Dit wordt dynamische belasting rapportage genoemd.

Met dynamische laad rapporten kunnen replica's of instanties hun toewijzing/gemelde belasting van metrische gegevens gedurende hun levens duur aanpassen. Normaal gesp roken rapporteert een service replica of-exemplaar dat koud is en geen werk heeft, meestal dat er een lage hoeveelheid van een bepaalde metriek werd gebruikt. Een bezette replica of instantie zou rapporteren dat ze meer gebruiken.

Met de rapportage belasting per replica of exemplaar kan cluster bron beheer de afzonderlijke service objecten in het cluster opnieuw indelen. Door de services opnieuw in te delen, kunt u ervoor zorgen dat ze de resources ophalen die ze nodig hebben. Services die bezet zijn, komen in feite het ' vrijmaken ' van resources van andere replica's of instanties die momenteel koud zijn of minder werk doen.

Binnen Reliable Services ziet de code voor het dynamisch rapporteren van de belasting er als volgt uit:

Code:

```csharp
this.Partition.ReportLoad(new List<LoadMetric> { new LoadMetric("CurrentConnectionCount", 1234), new LoadMetric("metric1", 42) });
```

Een service kan rapporteren over de metrische gegevens die tijdens de aanmaak zijn gedefinieerd. Als een service rapporten laadt voor een metriek die niet is geconfigureerd voor gebruik, Service Fabric dat rapport negeren. Als er andere metrische gegevens worden gerapporteerd op hetzelfde moment dat geldig is, worden deze rapporten geaccepteerd. Met Service code kunnen alle metrische gegevens worden gemeten en gerapporteerd, en kunnen Opera tors de metrische configuratie opgeven die moet worden gebruikt zonder dat de service code hoeft te worden gewijzigd. 

## <a name="reporting-load-for-a-partition"></a>Rapportage belasting voor een partitie
In de vorige sectie wordt beschreven hoe service replica's of exemplaren het rapport zelf laden. Er is een extra optie om de belasting dynamisch te rapporteren met FabricClient. Wanneer u de belasting voor een partitie rapporteert, kunt u voor meerdere partities tegelijk rapporteren.

Deze rapporten worden op dezelfde manier gebruikt als laad rapporten die afkomstig zijn van de replica's of instanties zelf. De gerapporteerde waarden zijn geldig totdat nieuwe laad waarden worden gerapporteerd, hetzij door de replica of het exemplaar, hetzij door een nieuwe laad waarde voor een partitie te rapporteren.

Met deze API zijn er meerdere manieren om de belasting in het cluster bij te werken:

  - Een stateful service-partitie kan de primaire replica taak bijwerken.
  - Zowel stateless als stateful Services kunnen de belasting van alle secundaire replica's of exemplaren bijwerken.
  - Zowel stateless als stateful Services kunnen de belasting van een specifieke replica of exemplaar op een knoop punt bijwerken.

Het is ook mogelijk om elk van deze updates per partitie tegelijkertijd te combi neren.

Het bijwerken van belasting voor meerdere partities is mogelijk met één API-aanroep, in dat geval bevat de uitvoer een antwoord per partitie. Als partitie-update niet wordt toegepast om een of andere reden, worden updates voor die partitie overgeslagen en wordt de bijbehorende fout code voor een doel partitie gegeven:

  - PartitionNotFound-de opgegeven partitie-ID bestaat niet.
  - ReconfigurationPending: de partitie wordt momenteel opnieuw geconfigureerd.
  - InvalidForStatelessServices-er is een poging gedaan om de belasting van een primaire replica te wijzigen voor een partitie die deel uitmaakt van een stateless service.
  - ReplicaDoesNotExist: de secundaire replica of het exemplaar bestaat niet op een opgegeven knoop punt.
  - InvalidOperation: het bijwerken van de belasting voor een partitie die deel uitmaakt van de systeem toepassing of het bijwerken van de voorspelde belasting is niet ingeschakeld.

Als sommige van deze fouten worden geretourneerd, kunt u de invoer voor een specifieke partitie bijwerken en de update opnieuw proberen voor een specifieke partitie.

Code:

```csharp
Guid partitionId = Guid.Parse("53df3d7f-5471-403b-b736-bde6ad584f42");
string metricName0 = "CustomMetricName0";
List<MetricLoadDescription> newPrimaryReplicaLoads = new List<MetricLoadDescription>()
{
    new MetricLoadDescription(metricName0, 100)
};

string nodeName0 = "NodeName0";
List<MetricLoadDescription> newSpecificSecondaryReplicaLoads = new List<MetricLoadDescription>()
{
    new MetricLoadDescription(metricName0, 200)
};

OperationResult<UpdatePartitionLoadResultList> updatePartitionLoadResults =
    await this.FabricClient.UpdatePartitionLoadAsync(
        new UpdatePartitionLoadQueryDescription
        {
            PartitionMetricLoadDescriptionList = new List<PartitionMetricLoadDescription>()
            {
                new PartitionMetricLoadDescription(
                    partitionId,
                    newPrimaryReplicaLoads,
                    new List<MetricLoadDescription>(),
                    new List<ReplicaMetricLoadDescription>()
                    {
                        new ReplicaMetricLoadDescription(nodeName0, newSpecificSecondaryReplicaLoads)
                    })
            }
        },
        this.Timeout,
        cancellationToken);
```

In dit voor beeld voert u een update uit van de laatst gemelde belasting voor een partitie _53df3d7f-5471-403b-b736-bde6ad584f42_. De primaire replica belasting voor een metrische _CustomMetricName0_ wordt bijgewerkt met de waarde 100. Tegelijkertijd wordt de belasting voor dezelfde metriek voor een specifieke secundaire replica op het knoop punt _NodeName0_ bijgewerkt met de waarde 200.

### <a name="updating-a-services-metric-configuration"></a>De metrische configuratie van een service bijwerken
De lijst met metrische gegevens die aan de service zijn gekoppeld en de eigenschappen van deze metrische gegevens kunnen dynamisch worden bijgewerkt terwijl de service actief is. Dit maakt experimenteren en flexibiliteit mogelijk. Enkele voor beelden van wanneer dit nuttig is:

  - een metriek uitschakelen met een rapport met fout opsporing voor een bepaalde service
  - het gewicht van metrische gegevens opnieuw configureren op basis van het gewenste gedrag
  - het inschakelen van een nieuwe metriek pas nadat de code al is geïmplementeerd en gevalideerd via andere mechanismen
  - de standaard belasting voor een service wijzigen op basis van waargenomen gedrag en verbruik

De belangrijkste Api's voor het wijzigen van de metrische configuratie zijn `FabricClient.ServiceManagementClient.UpdateServiceAsync` in C# en `Update-ServiceFabricService` in Power shell. Welke gegevens u met deze Api's opgeeft, vervangt de bestaande metrische gegevens voor de service onmiddellijk. 

## <a name="mixing-default-load-values-and-dynamic-load-reports"></a>Standaard waarden voor laden en dynamische laad rapporten mengen
Standaard belasting en dynamische belasting kunnen voor dezelfde service worden gebruikt. Wanneer een service zowel standaard loads als dynamische laad rapporten gebruikt, fungeert standaard load als een schatting totdat dynamische rapporten worden weer gegeven. De standaard belasting is goed, omdat het cluster resource manager er iets mee moet doen om mee te werken. Met de standaard belasting kan cluster bron beheer de service objecten op goede locaties plaatsen wanneer ze worden gemaakt. Als er geen standaard laad informatie wordt verstrekt, wordt de plaatsing van services in feite wille keurig. Wanneer het laden van rapporten later afneemt, is de eerste wille keurige plaatsing vaak onjuist en de cluster resource manager moet Services verplaatsen.

We gaan het vorige voor beeld bekijken en zien wat er gebeurt wanneer we aangepaste metrische gegevens en dynamische belasting rapportage toevoegen. In dit voor beeld gebruiken we ' MemoryInMb ' als voorbeeld metriek.

> [!NOTE]
> Geheugen is een van de systeem gegevens die Service Fabric resources kan [beheren](service-fabric-resource-governance.md)en die zelf moeilijk te rapporteren is. We verwachten niet dat u rapporteert over het geheugen verbruik; Geheugen wordt hier gebruikt als hulp middel bij het leren over de mogelijkheden van cluster resource manager.
>

We gaan ervan uit dat we de stateful service voor het eerst hebben gemaakt met de volgende opdracht:

Zo

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 3 -TargetReplicaSetSize 3 -PartitionSchemeSingleton –Metric @("MemoryInMb,High,21,11”,"PrimaryCount,Medium,1,0”,"ReplicaCount,Low,1,1”,"Count,Low,1,1”)
```

Als herinnering is deze syntaxis ("metrische, MetricWeight, PrimaryDefaultLoad, SecondaryDefaultLoad").

Laten we eens kijken wat een mogelijke cluster indeling zou kunnen zien:

<center>

![Cluster evenwichtig met standaard-en aangepaste metrische gegevens][Image2]
</center>

Een aantal dingen die u waardeert:

* Secundaire replica's binnen een partitie kunnen elk hun eigen belasting hebben
* In totaal worden de metrische gegevens evenwichtig weer gegeven. Voor geheugen is de verhouding tussen de maximale en minimale belasting 1,75 (het knoop punt met de meeste belasting is N3, de minst N2 en 28/16 = 1,75).

Er zijn enkele dingen die we nog moeten uitleggen:

* Wat is bepaald of een ratio van 1,75 redelijk is of niet? Hoe weet het cluster resource manager of het goed genoeg is of dat er meer werk is om te doen?
* Wanneer gebeurt de taak verdeling?
* Wat betekent het dat geheugen ' hoog ' is?

## <a name="metric-weights"></a>Metrische gewichten
Het volgen van dezelfde metrische gegevens in verschillende services is belang rijk. In deze globale weer gave is het mogelijk om het gebruik van cluster resource manager te traceren in het cluster, het verbruik tussen knoop punten te verdelen en ervoor te zorgen dat knoop punten niet over capaciteit beschikken. Services kunnen echter verschillende weer gaven hebben die het belang van dezelfde metriek hebben. In een cluster met veel metrische gegevens en veel services kunnen er mogelijk niet-gebalanceerde oplossingen bestaan voor alle metrische gegevens. Hoe moet de cluster resource manager deze situaties afhandelen?

Met metrische gewichten kan cluster resource manager bepalen hoe het cluster moet worden gebalanceerd als er geen perfecte antwoord is. Met metrische gewichten kan cluster resource manager ook specifieke services op een andere manier op elkaar afstemmen. Metrische gegevens kunnen vier verschillende gewichts niveaus hebben: nul, laag, gemiddeld en hoog. Een metriek met een gewicht van nul draagt niets toe wanneer wordt overwogen of dingen evenwichtig zijn of niet. De belasting blijft echter wel bijdragen aan het capaciteits beheer. Metrische gegevens met een gewicht van nul zijn nog steeds nuttig en worden vaak gebruikt als onderdeel van service gedrag en prestatie bewaking. In [dit artikel](service-fabric-diagnostics-event-generation-infra.md) vindt u meer informatie over het gebruik van metrische gegevens voor het bewaken en diagnosticeren van uw services. 

De werkelijke impact van verschillende metrische gewichten in het cluster is dat cluster resource manager verschillende oplossingen genereert. Met metrische gewichten wordt de cluster resource manager verteld dat bepaalde metrische gegevens belang rijker zijn dan andere. Als er geen perfecte oplossing is, kan cluster resource manager de voor keur geven aan oplossingen die de hogere gewogen metrische gegevens beter in balans brengen. Als een service denkt dat een bepaalde metriek niet belang rijk is, kan het gebruik van de metrische gegevens onevenwichtig worden. Hierdoor kan een andere service een gelijkmatige verdeling krijgen van een belang rijk deel van de metriek.

We kijken naar een voor beeld van sommige laad rapporten en hoe verschillende metrische meet waarden resulteren in verschillende toewijzingen in het cluster. In dit voor beeld ziet u dat door het overschakelen van het relatieve gewicht van de metrische gegevens de cluster resource manager verschillende services-eenheden maakt.

<center>

![Voor beeld van metrieke gewicht en de invloed op Balancing-oplossingen][Image3]
</center>

In dit voor beeld zijn er vier verschillende services, waarbij alle verschillende waarden worden gerapporteerd voor twee verschillende metrische gegevens, metrische gegevens en MetricB. In één geval is het belang rijk dat alle services metrische gegevens definiëren (gewicht = hoog) en MetricB als niet belang rijk (gewicht = laag). Als gevolg hiervan zien we dat de cluster resource manager de Services plaatst, zodat Metrica beter is gebalanceerd dan MetricB. "Beter evenwichtig" betekent dat metrische gegevens een lagere standaard afwijking hebben dan MetricB. In het tweede geval worden de metrische gewichten omgedraaid. Als gevolg hiervan wisselen de cluster resource manager Services A en B uit om een toewijzing te maken waarbij MetricB beter is evenwichtig dan Metrieka.

> [!NOTE]
> Metrische gewichten bepalen hoe cluster resource manager moet worden gebalanceerd, maar niet wanneer de taak verdeling moet plaatsvinden. Raadpleeg [dit artikel](service-fabric-cluster-resource-manager-balancing.md) voor meer informatie over de taak verdeling
>

### <a name="global-metric-weights"></a>Global metrisch gewichten
Stel dat Servicea metrische gegevens definieert als gewicht hoog en ServiceB het gewicht voor metrische gegevens instelt op laag of nul. Wat is het werkelijke gewicht dat wordt gebruikt om te gebruiken?

Er zijn meerdere gewichten die worden bijgehouden voor elke metriek. Het eerste gewicht is de waarde die is gedefinieerd voor de metriek wanneer de service wordt gemaakt. Het andere gewicht is een globaal gewicht dat automatisch wordt berekend. Cluster resource manager maakt gebruik van beide gewichten bij het scoren van oplossingen. Het maken van beide gewichten is belang rijk. Hierdoor kan cluster resource manager elke service verdelen op basis van eigen prioriteiten, en moet u er ook voor zorgen dat het cluster als geheel correct wordt toegewezen.

Wat gebeurt er als het cluster bron beheer niet heeft gewaakt over het globale en lokale saldo? Daarnaast is het eenvoudig om oplossingen te bouwen die wereld wijd zijn verdeeld, maar wat resulteert in een slecht resource saldo voor afzonderlijke services. In het volgende voor beeld bekijken we een service die is geconfigureerd met alleen de standaard metrische gegevens en ziet u wat er gebeurt als er alleen een globaal saldo wordt overwogen:

<center>

![De impact van een alleen wereld wijde oplossing][Image4]
</center>

In het bovenste voor beeld op basis van het algemene saldo is het cluster als geheel gebalanceerd. Alle knoop punten hebben hetzelfde aantal Primaries en hetzelfde aantal replica's. Als u echter de werkelijke impact van deze toewijzing bekijkt, is dit niet zo goed mogelijk: het verlies van een knoop punt heeft gevolgen voor een bepaalde werk belasting, omdat het alle Primaries ervan afneemt. Als het eerste knoop punt bijvoorbeeld niet voldoet aan de drie Primaries voor de drie verschillende partities van de Circle-service, gaan allemaal verloren. Daarentegen hebben de drie hoek-en zeshoeken-Services een replica verloren. Dit veroorzaakt geen onderbreking, anders dan het herstellen van de replica omlaag.

In het onderste voor beeld heeft cluster resource manager de replica's gedistribueerd op basis van het algemene en het service-saldo. Bij het berekenen van de Score van de oplossing geeft het grootste deel van het gewicht aan de globale oplossing en een (configureerbaar) gedeelte van afzonderlijke services. Het globale saldo voor een metriek wordt berekend op basis van het gemiddelde van de metrische gewichten van elke service. Voor elke service wordt uitgebalanceerd volgens het eigen gedefinieerde metrische gewicht. Dit zorgt ervoor dat de services binnen hun eigen behoeften worden gesaldeerd. Als het resultaat van hetzelfde eerste knoop punt mislukt, wordt de fout gedistribueerd over alle partities van alle services. De impact op elk is hetzelfde.

## <a name="next-steps"></a>Volgende stappen
- Meer informatie over het configureren van Services vindt u in het [onderwerp over het configureren van services](service-fabric-cluster-resource-manager-configure-services.md)(Service-Fabric-cluster-resource-manager-Configure-Services.MD)
- Het definiëren van metrische gegevens over defragmentatie is een manier om de belasting op knoop punten te consolideren in plaats van deze te spreiden. Raadpleeg [dit artikel](service-fabric-cluster-resource-manager-defragmentation-metrics.md) voor meer informatie over het configureren van defragmentatie.
- Raadpleeg het artikel over het [verdelen](service-fabric-cluster-resource-manager-balancing.md) van de taken in het cluster voor meer informatie over hoe de cluster resource manager de belasting beheert en balanceert.
- Begin vanaf het begin en [krijg een inleiding tot de service Fabric cluster resource manager](service-fabric-cluster-resource-manager-introduction.md)
- De verplaatsings kosten zijn één manier om aan te geven dat de cluster resource manager bepaalde services duurder kan verplaatsen dan andere. Raadpleeg [dit artikel](service-fabric-cluster-resource-manager-movement-cost.md) voor meer informatie over de verplaatsings kosten

[Image1]:./media/service-fabric-cluster-resource-manager-metrics/cluster-resource-manager-cluster-layout-with-default-metrics.png
[Image2]:./media/service-fabric-cluster-resource-manager-metrics/Service-Fabric-Resource-Manager-Dynamic-Load-Reports.png
[Image3]:./media/service-fabric-cluster-resource-manager-metrics/cluster-resource-manager-metric-weights-impact.png
[Image4]:./media/service-fabric-cluster-resource-manager-metrics/cluster-resource-manager-global-vs-local-balancing.png
