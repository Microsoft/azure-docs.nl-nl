---
title: Cluster resource Manager Service Fabric-affiniteit
description: Overzicht van service affiniteit voor Azure Service Fabric Services en richt lijnen voor configuratie van service affiniteit.
services: service-fabric
documentationcenter: .net
author: masnider
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: masnider
ms.custom: devx-track-csharp
ms.openlocfilehash: 9c141cd96877fd140b858d0aaed9197f2de80eca
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "89005731"
---
# <a name="configuring-and-using-service-affinity-in-service-fabric"></a>Service affiniteit in Service Fabric configureren en gebruiken
Affiniteit is een besturings element dat voornamelijk wordt verschaft om de overgang van grotere monolithische-toepassingen naar de Cloud en micro Services-wereld te vergemakkelijken. Het wordt ook gebruikt als een Optima Lise ring voor het verbeteren van de prestaties van services, hoewel dit wel neven effecten kan hebben.

Stel dat u een grotere app brengt of een die zojuist niet is ontworpen met micro Services in het geService Fabric (of een gedistribueerde omgeving). Dit type overgang is gebruikelijk. U begint met het opheffen van de volledige app in de omgeving, verpakt deze en zorgt ervoor dat deze probleemloos wordt uitgevoerd. Vervolgens begint u met het opsplitsen van het bestand in verschillende kleinere services die allemaal met elkaar communiceren.

Uiteindelijk kunt u zien dat er problemen zijn met de toepassing. De problemen vallen doorgaans in een van deze categorieën:

1. Sommige onderdeel X in de monolithische-app heeft een niet-gedocumenteerde afhankelijkheid van Component Y en u hebt deze onderdelen zojuist in afzonderlijke services ingeschakeld. Omdat deze services nu worden uitgevoerd op verschillende knoop punten in het cluster, zijn ze verbroken.
2. Deze onderdelen communiceren via (lokale named pipes | gedeeld geheugen | bestanden op schijf) en ze moeten echt kunnen schrijven naar een gedeelde lokale bron om prestatie redenen. De harde afhankelijkheid wordt later verwijderd.
3. Alles is goed, maar het is belang rijk dat deze twee onderdelen werkelijk intensieve/prestatie gevoelig zijn. Wanneer ze ze naar afzonderlijke services hebben verplaatst, wordt de totale toepassings prestatie getankd of de latentie verhoogd. Als gevolg hiervan is de algehele toepassing niet aan verwachtingen voldoen.

In deze gevallen is het niet mogelijk om de herstructureel werk te verliezen en wilt u niet teruggaan naar het op. De laatste voor waarde kan zelfs gewenst zijn als een gewone optimalisatie. Echter, totdat we de onderdelen zo kunnen ontwerpen dat ze op natuurlijke wijze werken als Services (of totdat we de prestatie verwachtingen op een andere manier kunnen oplossen), hebben we een deel van de lokale locatie nodig.

Wat u moet doen? Het is ook mogelijk om de affiniteit in te scha kelen.

## <a name="how-to-configure-affinity"></a>Affiniteit configureren
Als u affiniteit wilt instellen, definieert u een affiniteits relatie tussen twee verschillende services. U kunt de affiniteit beschouwen als ' wijzend ' één service op een andere en zegt ' deze service kan alleen worden uitgevoerd wanneer de service wordt uitgevoerd '. Soms verwijzen we naar affiniteit als een bovenliggende/onderliggende relatie (waarbij u het onderliggende item op het bovenliggende item plaatst). Affiniteit zorgt ervoor dat de replica's of exemplaren van één service worden geplaatst op dezelfde knoop punten als die van een andere service.

```csharp
ServiceCorrelationDescription affinityDescription = new ServiceCorrelationDescription();
affinityDescription.Scheme = ServiceCorrelationScheme.Affinity;
affinityDescription.ServiceName = new Uri("fabric:/otherApplication/parentService");
serviceDescription.Correlations.Add(affinityDescription);
await fabricClient.ServiceManager.CreateServiceAsync(serviceDescription);
```

> [!NOTE]
> Een onderliggende service kan alleen deel uitmaken van één affiniteits relatie. Als u wilt dat de onderliggende stelt teamlid aan twee bovenliggende services tegelijk wordt uitgevoerd, hebt u de volgende opties:
> - De relaties omkeren (hebben parentService1-en parentService2-punt op de huidige onderliggende service) of
> - Wijs een van de bovenliggende items aan als hub per Conventie en laat alle services-punten voor die service. 
>
> Het resulterende plaatsings gedrag in het cluster moet hetzelfde zijn.
>

## <a name="different-affinity-options"></a>Verschillende opties voor affiniteit
Affiniteit wordt vertegenwoordigd via een van de verschillende correlatie schema's en heeft twee verschillende modi. De meest voorkomende affiniteits modus is wat we NonAlignedAffinity aanroepen. In NonAlignedAffinity worden de replica's of exemplaren van de verschillende services op dezelfde knoop punten geplaatst. De andere modus is AlignedAffinity. Uitgelijnde affiniteit is alleen nuttig met stateful Services. Als u twee stateful Services configureert om een uitgelijnde affiniteit te hebben, zorgt u ervoor dat de Primaries van die services op dezelfde knoop punten als elkaar worden geplaatst. Het zorgt er ook voor dat elk paar secundaire zones voor die services op dezelfde knoop punten worden geplaatst. Het is ook mogelijk (maar minder algemeen) om NonAlignedAffinity te configureren voor stateful Services. De verschillende replica's van de twee stateful services worden voor NonAlignedAffinity uitgevoerd op dezelfde knoop punten, maar hun Primaries kan uiteindelijk op verschillende knoop punten worden gestopt.

<center>

![Affiniteits modi en hun effecten][Image1]
</center>

### <a name="best-effort-desired-state"></a>Gewenste status Best effort
Een affiniteits relatie is het beste. Het biedt niet dezelfde garanties als plaatsing of betrouw baarheid die in hetzelfde uitvoer bare proces worden uitgevoerd. De services in een affiniteits relatie zijn fundamenteel verschillende entiteiten die kunnen mislukken en onafhankelijk worden verplaatst. Een affiniteits relatie kan ook worden verbroken, hoewel deze onderbrekingen tijdelijk zijn. Zo kunnen capaciteits beperkingen betekenen dat slechts enkele van de service objecten in de affiniteits relatie op een bepaald knoop punt passen. In deze gevallen, zelfs als er sprake is van een affiniteits relatie, kan deze niet worden afgedwongen als gevolg van de andere beperkingen. Als dit mogelijk is, wordt de schending automatisch later gecorrigeerd.

### <a name="chains-vs-stars"></a>Ketens versus sterren
De huidige cluster resource manager kan geen ketens van affiniteits relaties model leren. Dit betekent dat een service die een onderliggend item is in één affiniteits relatie geen bovenliggend item kan zijn in een andere affiniteits relatie. Als u dit type relatie wilt model leren, moet u het effectief model leren als een ster in plaats van een keten. Als u wilt overstappen van een keten naar een ster, zou het onderste onderliggende item in plaats daarvan worden geparent naar het bovenliggende Parent van het eerste onderliggende item. Afhankelijk van de rang schikking van uw services moet u dit mogelijk meerdere keren doen. Als er geen natuurlijke bovenliggende service is, moet u er mogelijk een maken die fungeert als tijdelijke aanduiding. Afhankelijk van uw vereisten kunt u ook zoeken in [toepassings groepen](service-fabric-cluster-resource-manager-application-groups.md).

<center>

![Ketens vergeleken met sterren in de context van Affiniteits relaties][Image2]
</center>

Een andere manier om te noteren over affiniteits relaties, is dat ze standaard worden genoteerd. Dit betekent dat de affiniteits regel alleen afdwingt dat het onderliggende element met het bovenliggende item is geplaatst. Er wordt niet gegarandeerd dat het bovenliggende item zich bevindt bij de onderliggende. Als er een affiniteits fout is opgetreden en de schending om een bepaalde reden niet haalbaar is, is het niet mogelijk om het onderliggende knoop punt te verplaatsen naar het knooppunt van het bovenliggende item, zelfs als het verplaatsen van het bovenliggende item naar het knoop punt van het kind de schending heeft gecorrigeerd. het bovenliggende item wordt niet verplaatst naar het knoop punt van de onderliggende. Als u de configuratie [MoveParentToFixAffinityViolation](service-fabric-cluster-fabric-settings.md) instelt op True, wordt de directionality verwijderd. Het is ook belang rijk te weten dat de affiniteits relatie niet perfect of onmiddellijk kan worden afgedwongen omdat verschillende services met verschillende levens cycli hebben en kunnen mislukken en onafhankelijk worden verplaatst. Stel bijvoorbeeld dat het bovenliggende object plotseling een failover voor een ander knoop punt heeft uitgevoerd, omdat het is vastgelopen. De cluster resource manager en Failover Manager verwerken de failover eerst, omdat het actief, consistent en beschikbaar houden van de services de prioriteit is. Zodra de failover is voltooid, wordt de affiniteits relatie verbroken, maar de cluster resource manager denkt dat alles goed is totdat het onderliggende item zich niet bij het bovenliggende item bevindt. Deze sorteringen worden periodiek uitgevoerd. Meer informatie over de wijze waarop de cluster resource manager beperkingen evalueert, is beschikbaar in [dit artikel](service-fabric-cluster-resource-manager-management-integration.md#constraint-types), en dit wordt uitgelegd in [een](service-fabric-cluster-resource-manager-balancing.md) beschrijving van het configureren van de uitgebracht waarvoor deze beperkingen worden geëvalueerd.   


### <a name="partitioning-support"></a>Ondersteuning voor partitionering
Het laatste wat u moet weten over affiniteit is dat affiniteits relaties niet worden ondersteund wanneer de bovenliggende partitie is gepartitioneerd. Gepartitioneerde bovenliggende Services kunnen uiteindelijk worden ondersteund, maar dit is niet toegestaan.

## <a name="next-steps"></a>Volgende stappen
- Meer informatie over het configureren van Services vindt u op het [configureren van services](service-fabric-cluster-resource-manager-configure-services.md)
- Voor het beperken van services naar een kleine set machines of het samen voegen van de belasting van services, gebruikt u [toepassings groepen](service-fabric-cluster-resource-manager-application-groups.md)

[Image1]:./media/service-fabric-cluster-resource-manager-advanced-placement-rules-affinity/cluster-resrouce-manager-affinity-modes.png
[Image2]:./media/service-fabric-cluster-resource-manager-advanced-placement-rules-affinity/cluster-resource-manager-chains-vs-stars.png