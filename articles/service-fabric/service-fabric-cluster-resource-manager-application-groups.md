---
title: Cluster resource Manager Service Fabric-toepassings groepen
description: Overzicht van de functionaliteit van de toepassings groep in de Service Fabric cluster resource manager
author: masnider
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 988c7ce52125800c16aa785d5b1458604a927ecd
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "75452155"
---
# <a name="introduction-to-application-groups"></a>Inleiding tot toepassings groepen
Service Fabric cluster resource manager beheert cluster bronnen doorgaans door de belasting gelijkmatig te verdelen (vertegenwoordigd door [metrische gegevens](service-fabric-cluster-resource-manager-metrics.md)) in het hele cluster. Service Fabric beheert de capaciteit van de knoop punten in het cluster en het cluster als geheel via [capaciteit](service-fabric-cluster-resource-manager-cluster-description.md). Metrische gegevens en capaciteit werken prima voor veel werk belastingen, maar patronen die een zwarer gebruik maken van verschillende Service Fabric toepassings exemplaren, kunnen soms extra vereisten in beslag nemen. U kunt bijvoorbeeld het volgende doen:

- Reserveer enige capaciteit op de knoop punten in het cluster voor de services binnen sommige benoemde toepassings exemplaren
- Beperk het totale aantal knoop punten waarop de services binnen een benoemd toepassings exemplaar worden uitgevoerd (in plaats van ze te spreiden over het hele cluster)
- Definieer de capaciteit van het benoemde toepassings exemplaar zelf om het aantal services of het totale Resource verbruik van de services erin te beperken

Om aan deze vereisten te voldoen, ondersteunt de Service Fabric cluster resource manager een functie genaamd toepassings groepen.

## <a name="limiting-the-maximum-number-of-nodes"></a>Het maximum aantal knoop punten beperken
Het eenvoudigste gebruiks scenario voor toepassings capaciteit is wanneer een toepassings exemplaar moet worden beperkt tot een bepaald maximum aantal knoop punten. Hiermee worden alle services binnen het toepassings exemplaar geconsolideerd op een ingesteld aantal machines. Consolidatie is handig als u wilt voors pellen of het gebruik van fysieke resources door de services binnen dat toepassings exemplaar. 

De volgende afbeelding toont een toepassings exemplaar met en zonder een maximum aantal knoop punten dat is gedefinieerd:

<center>

![Instantie van toepassing die het maximum aantal knoop punten definieert][Image1]
</center>

In het linkerdeel voor beeld heeft de toepassing niet het maximum aantal knoop punten dat is gedefinieerd en zijn er drie services. Cluster resource manager heeft alle replica's verdeeld over zes beschik bare knoop punten om het beste evenwicht in het cluster te krijgen (het standaard gedrag). In het juiste voor beeld zien we dat dezelfde toepassing is beperkt tot drie knoop punten.

De para meter die dit gedrag bepaalt, wordt MaximumNodes genoemd. Deze para meter kan worden ingesteld tijdens het maken van een toepassing of worden bijgewerkt voor een instantie van een toepassing die al wordt uitgevoerd.

PowerShell

``` posh
New-ServiceFabricApplication -ApplicationName fabric:/AppName -ApplicationTypeName AppType1 -ApplicationTypeVersion 1.0.0.0 -MaximumNodes 3
Update-ServiceFabricApplication –ApplicationName fabric:/AppName –MaximumNodes 5
```

C#

``` csharp
ApplicationDescription ad = new ApplicationDescription();
ad.ApplicationName = new Uri("fabric:/AppName");
ad.ApplicationTypeName = "AppType1";
ad.ApplicationTypeVersion = "1.0.0.0";
ad.MaximumNodes = 3;
await fc.ApplicationManager.CreateApplicationAsync(ad);

ApplicationUpdateDescription adUpdate = new ApplicationUpdateDescription(new Uri("fabric:/AppName"));
adUpdate.MaximumNodes = 5;
await fc.ApplicationManager.UpdateApplicationAsync(adUpdate);

```

Binnen de set knoop punten garandeert het cluster resource manager niet welke service objecten tegelijk worden geplaatst of welke knoop punten worden gebruikt.

## <a name="application-metrics-load-and-capacity"></a>Metrische gegevens van toepassingen, laden en capaciteit
Met toepassings groepen kunt u ook metrische gegevens definiëren die zijn gekoppeld aan een opgegeven benoemd toepassings exemplaar en de capaciteit van die toepassings instantie voor deze metrische gegevens. Met metrische gegevens van toepassingen kunt u het Resource verbruik van de services in dat toepassings exemplaar bijhouden, reserveren en beperken.

Voor elke toepassings metriek zijn er twee waarden die kunnen worden ingesteld:

- **Totale toepassings capaciteit** : deze instelling vertegenwoordigt de totale capaciteit van de toepassing voor een bepaalde metriek. Het maken van nieuwe services binnen deze toepassings instantie door cluster resource manager wordt niet toegestaan, waardoor de totale belasting deze waarde kan overschrijden. Stel bijvoorbeeld dat het toepassings exemplaar een capaciteit van 10 had en al vijf belasting had. Het maken van een service met een totale standaard belasting van 10 zou niet zijn toegestaan.
- **Maximale knooppunt capaciteit** : deze instelling bepaalt de maximale totale belasting voor de toepassing op één knoop punt. Als de belasting deze capaciteit overschrijdt, verplaatst de cluster resource manager replica's naar andere knoop punten zodat de belasting afneemt.


Zo

``` posh
New-ServiceFabricApplication -ApplicationName fabric:/AppName -ApplicationTypeName AppType1 -ApplicationTypeVersion 1.0.0.0 -Metrics @("MetricName:Metric1,MaximumNodeCapacity:100,MaximumApplicationCapacity:1000")
```

C#

``` csharp
ApplicationDescription ad = new ApplicationDescription();
ad.ApplicationName = new Uri("fabric:/AppName");
ad.ApplicationTypeName = "AppType1";
ad.ApplicationTypeVersion = "1.0.0.0";

var appMetric = new ApplicationMetricDescription();
appMetric.Name = "Metric1";
appMetric.TotalApplicationCapacity = 1000;
appMetric.MaximumNodeCapacity = 100;
ad.Metrics.Add(appMetric);
await fc.ApplicationManager.CreateApplicationAsync(ad);
```

## <a name="reserving-capacity"></a>Capaciteit reserveren
Een ander algemeen gebruik voor toepassings groepen is ervoor te zorgen dat resources binnen het cluster worden gereserveerd voor een bepaald toepassings exemplaar. De ruimte is altijd gereserveerd wanneer het toepassings exemplaar wordt gemaakt.

Het reserveren van ruimte in het cluster voor de toepassing gebeurt onmiddellijk zelfs wanneer:
- het exemplaar van de toepassing is gemaakt, maar heeft nog geen services
- het aantal services binnen het toepassings exemplaar wordt elke keer gewijzigd 
- de services bestaan, maar verbruiken geen gebruik van de resources 

Voor het reserveren van resources voor een toepassings exemplaar moet u twee extra para meters opgeven: *MinimumNodes* en *NodeReservationCapacity*

- **MinimumNodes** : Hiermee definieert u het minimum aantal knoop punten waarop het toepassings exemplaar moet worden uitgevoerd.  
- **NodeReservationCapacity** : deze instelling is per metriek voor de toepassing. De waarde is de hoeveelheid gegevens die voor de toepassing is gereserveerd op een wille keurig knoop punt waar de services in die toepassing worden uitgevoerd.

Het combi neren van **MinimumNodes** en **NodeReservationCapacity** garandeert een minimale laad reservering voor de toepassing binnen het cluster. Als er minder resterende capaciteit is in het cluster dan de totale reserve ring vereist, mislukt het maken van de toepassing. 

We kijken naar een voor beeld van capaciteits reservering:

<center>

![Toepassings exemplaren die gereserveerde capaciteit definiëren][Image2]
</center>

In het linkerdeel voor beeld is er geen toepassings capaciteit gedefinieerd voor toepassingen. Het cluster resource manager verdeelt alles op basis van normale regels.

In het voor beeld aan de rechter kant laten we zeggen dat Application1 is gemaakt met de volgende instellingen:

- MinimumNodes ingesteld op twee
- Een toepassings metriek die is gedefinieerd met
  - NodeReservationCapacity van 20

PowerShell

 ``` posh
 New-ServiceFabricApplication -ApplicationName fabric:/AppName -ApplicationTypeName AppType1 -ApplicationTypeVersion 1.0.0.0 -MinimumNodes 2 -Metrics @("MetricName:Metric1,NodeReservationCapacity:20")
 ```

C#

 ``` csharp
ApplicationDescription ad = new ApplicationDescription();
ad.ApplicationName = new Uri("fabric:/AppName");
ad.ApplicationTypeName = "AppType1";
ad.ApplicationTypeVersion = "1.0.0.0";
ad.MinimumNodes = 2;

var appMetric = new ApplicationMetricDescription();
appMetric.Name = "Metric1";
appMetric.NodeReservationCapacity = 20;

ad.Metrics.Add(appMetric);

await fc.ApplicationManager.CreateApplicationAsync(ad);
```

Service Fabric reserveert capaciteit op twee knoop punten voor Application1 en staat services van Application2 niet toe dat deze capaciteit wordt gebruikt, zelfs niet als er op het moment geen belasting door de services binnen Application1 wordt verbruikt. Deze gereserveerde toepassings capaciteit wordt beschouwd als verbruikt en telt op basis van de resterende capaciteit van het knoop punt en binnen het cluster.  De reserve ring wordt onmiddellijk in mindering gebracht op de resterende cluster capaciteit. het gereserveerde verbruik wordt echter alleen van de capaciteit van een specifiek knoop punt in mindering gebracht wanneer er ten minste één service object wordt geplaatst. Deze latere reserve ring biedt flexibiliteit en beter bron gebruik, aangezien bronnen alleen worden gereserveerd op knoop punten wanneer dat nodig is.

## <a name="obtaining-the-application-load-information"></a>Informatie over het laden van de toepassing verkrijgen
Voor elke toepassing waarvoor een toepassings capaciteit is gedefinieerd voor een of meer metrische gegevens, kunt u de informatie verkrijgen over de cumulatieve belasting die wordt gerapporteerd door replica's van de bijbehorende services.

Zo

``` posh
Get-ServiceFabricApplicationLoadInformation –ApplicationName fabric:/MyApplication1
```

C#

``` csharp
var v = await fc.QueryManager.GetApplicationLoadInformationAsync("fabric:/MyApplication1");
var metrics = v.ApplicationLoadMetricInformation;
foreach (ApplicationLoadMetricInformation metric in metrics)
{
    Console.WriteLine(metric.ApplicationCapacity);  //total capacity for this metric in this application instance
    Console.WriteLine(metric.ReservationCapacity);  //reserved capacity for this metric in this application instance
    Console.WriteLine(metric.ApplicationLoad);  //current load for this metric in this application instance
}
```

De ApplicationLoad-query retourneert de basis informatie over de toepassings capaciteit die voor de toepassing is opgegeven. Deze informatie omvat het minimale knoop punt en het maximum aantal knoop punten en het nummer dat de toepassing momenteel bezet is. Het bevat ook informatie over de metrische gegevens van de belasting van de toepassing, waaronder:

* Metrische naam: naam van de metriek.
* Reserverings capaciteit: cluster capaciteit die is gereserveerd in het cluster voor deze toepassing.
* Laden van toepassingen: totale belasting van de onderliggende replica's van deze toepassing.
* Toepassings capaciteit: Maxi maal toegestane waarde voor het laden van toepassingen.

## <a name="removing-application-capacity"></a>Toepassings capaciteit verwijderen
Zodra de para meters voor de toepassings capaciteit zijn ingesteld voor een toepassing, kunnen ze worden verwijderd met behulp van Application Api's of Power shell-cmdlets. Bijvoorbeeld:

``` posh
Update-ServiceFabricApplication –Name fabric:/MyApplication1 –RemoveApplicationCapacity

```

Met deze opdracht worden alle beheer parameters voor toepassings capaciteit verwijderd uit het toepassings exemplaar. Dit omvat MinimumNodes, MaximumNodes en de metrische gegevens van de toepassing, indien aanwezig. Het effect van de opdracht is direct. Wanneer deze opdracht is voltooid, gebruikt cluster resource manager het standaard gedrag voor het beheren van toepassingen. De para meters voor de toepassings capaciteit kunnen opnieuw worden opgegeven via `Update-ServiceFabricApplication` / `System.Fabric.FabricClient.ApplicationManagementClient.UpdateApplicationAsync()` .

### <a name="restrictions-on-application-capacity"></a>Beperkingen voor de toepassings capaciteit
Er zijn verschillende beperkingen voor de para meters van de toepassings capaciteit die moeten worden gerespecteerd. Als er validatie fouten zijn, worden er geen wijzigingen doorgevoerd.

- Alle para meters voor geheel getal moeten niet-negatieve getallen zijn.
- MinimumNodes mag nooit groter zijn dan MaximumNodes.
- Als er capaciteit voor een metrische belasting worden gedefinieerd, moeten ze de volgende regels volgen:
  - De capaciteit van de knooppunt reservering mag niet groter zijn dan de maximale knooppunt capaciteit. U kunt bijvoorbeeld de capaciteit voor de metrische gegevens ' CPU ' op het knoop punt niet naar twee eenheden beperken en proberen drie eenheden op elk knoop punt te reserveren.
  - Als MaximumNodes is opgegeven, mag het product van MaximumNodes en de maximale knooppunt capaciteit niet groter zijn dan de totale toepassings capaciteit. Stel bijvoorbeeld dat de maximum capaciteit van het knoop punt voor belasting gegevens ' CPU ' is ingesteld op acht. U kunt er ook voor zorgen dat u het maximum aantal knoop punten instelt op 10. In dit geval moet de totale capaciteit van de toepassing groter zijn dan 80 voor deze belasting metriek.

De beperkingen worden beide afgedwongen tijdens het maken en bijwerken van de toepassing.

## <a name="how-not-to-use-application-capacity"></a>Hoe kan ik toepassings capaciteit niet gebruiken?
- Probeer niet de functies van de toepassings groep te gebruiken om de toepassing te beperken tot een _specifieke_ subset van knoop punten. Met andere woorden, u kunt opgeven dat de toepassing wordt uitgevoerd op Maxi maal vijf knoop punten, maar niet de specifieke vijf knoop punten in het cluster. Het beperken van een toepassing voor specifieke knoop punten kan worden bereikt met behulp van plaatsings beperkingen voor services.
- Probeer niet de capaciteit van de toepassing te gebruiken om ervoor te zorgen dat twee services van dezelfde toepassing op dezelfde knoop punten worden geplaatst. Gebruik in plaats daarvan [affiniteits](service-fabric-cluster-resource-manager-advanced-placement-rules-affinity.md) -of [plaatsings beperkingen](service-fabric-cluster-resource-manager-cluster-description.md#node-properties-and-placement-constraints).

## <a name="next-steps"></a>Volgende stappen
- Meer informatie over het configureren van Services vindt u op het [configureren van services](service-fabric-cluster-resource-manager-configure-services.md)
- Raadpleeg het artikel over het [verdelen](service-fabric-cluster-resource-manager-balancing.md) van de taken in het cluster voor meer informatie over hoe de cluster resource manager de belasting beheert en balanceert.
- Begin vanaf het begin en [krijg een inleiding tot de service Fabric cluster resource manager](service-fabric-cluster-resource-manager-introduction.md)
- Voor meer informatie over hoe metrische gegevens in het algemeen werken, lees dan [service Fabric metrische gegevens over belasting](service-fabric-cluster-resource-manager-metrics.md)
- Cluster resource manager heeft veel opties voor het beschrijven van het cluster. Lees dit artikel over [het beschrijven van een service Fabric cluster](service-fabric-cluster-resource-manager-cluster-description.md) voor meer informatie.

[Image1]:./media/service-fabric-cluster-resource-manager-application-groups/application-groups-max-nodes.png
[Image2]:./media/service-fabric-cluster-resource-manager-application-groups/application-groups-reserved-capacity.png
