---
title: 'Test baarheid: Service communicatie'
description: Service-to-service-communicatie is een essentieel integratie punt van een Service Fabric-toepassing. In dit artikel worden ontwerp overwegingen en test technieken besproken.
ms.topic: conceptual
ms.date: 11/02/2017
ms.openlocfilehash: fbf889422d69d031d777acd80cf3becfb3184568
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96575921"
---
# <a name="service-fabric-testability-scenarios-service-communication"></a>Service Fabric scenario's voor testen: Service communicatie
Micro Services en service-georiënteerde architecturale modellen worden op natuurlijke wijze in azure Service Fabric. In deze typen gedistribueerde architecturen zijn onderdelen van micro service-toepassingen meestal samengesteld uit meerdere services die met elkaar moeten worden gecommuniceerd. In de eenvoudigste gevallen beschikt u over het algemeen over ten minste een stateless webservice en een stateful gegevens opslag service die moet communiceren.

Service-to-service-communicatie is een essentieel integratie punt van een toepassing, omdat elke service een externe API voor andere services beschikbaar maakt. Bij het werken met een reeks API-grenzen waarvoor I/O's geldt, is een goede hoeveelheid tests en validatie vereist.

Er zijn talrijke overwegingen die moeten worden gemaakt wanneer deze service grenzen samen in een gedistribueerd systeem worden bekabeld:

* *Transport Protocol*. Gebruikt u HTTP voor een verhoogde interoperabiliteit of een aangepast binair protocol voor maximale door Voer?
* *Fout afhandeling*. Hoe worden permanente en tijdelijke fouten afgehandeld? Wat gebeurt er wanneer een service naar een ander knoop punt wordt verplaatst?
* *Time-outs en latentie*. Hoe zorgt elke service slaag in toepassingen met meerdere lagen tot een latentie door de stack en naar de gebruiker?

Of u een van de ingebouwde service communicatie onderdelen gebruikt die door Service Fabric worden meegeleverd of u uw eigen functies bouwt, het testen van de interacties tussen uw services is essentieel om de flexibiliteit in uw toepassing te garanderen.

## <a name="prepare-for-services-to-move"></a>Voorbereiden op het verplaatsen van services
Service-exemplaren kunnen na verloop van tijd worden verplaatst. Dit geldt met name wanneer ze zijn geconfigureerd met metrische gegevens over het laden van een op maat gemaakte, optimale resource verdeling. Service Fabric verplaatst uw service-exemplaren om hun Beschik baarheid te maximaliseren, zelfs tijdens upgrades, failovers, uitschalen en andere situaties die zich voordoen gedurende de levens duur van een gedistribueerd systeem.

Naarmate Services in het cluster worden verplaatst, moeten uw clients en andere services worden voor bereid voor het afhandelen van twee scenario's wanneer ze met een service communiceren:

* Het service-exemplaar of de partitie replica is verplaatst sinds de laatste keer dat u deze hebt aangesp roken. Dit is een normaal onderdeel van een levens cyclus van een service en moet naar verwachting plaatsvinden tijdens de levens duur van uw toepassing.
* Het service-exemplaar of de partitie replica is bezig met verplaatsen. Hoewel failover van een service van het ene naar het andere knoop punt zeer snel plaatsvindt in Service Fabric, kan er een vertraging in de beschik baarheid optreden als het communicatie onderdeel van uw service traag wordt gestart.

Het afhandelen van deze scenario's is van belang voor een systeem met soepele uitvoering. Denk hierbij aan het volgende:

* Elke service waarmee verbinding kan worden gemaakt, heeft een *adres* waarop het luistert (bijvoorbeeld http of websockets). Wanneer een service-exemplaar of partitie wordt verplaatst, wordt het adres van het eind punt gewijzigd. (Het wordt verplaatst naar een ander knoop punt met een ander IP-adres.) Als u de ingebouwde communicatie onderdelen gebruikt, worden de service adressen voor u opnieuw voor u afgehandeld.
* Er is mogelijk een tijdelijke toename van de service latentie, omdat het service-exemplaar opnieuw de listener start. Dit is afhankelijk van hoe snel de service de listener opent nadat het service-exemplaar is verplaatst.
* Bestaande verbindingen moeten worden gesloten en opnieuw worden geopend nadat de service op een nieuw knoop punt is geopend. Wanneer een knoop punt wordt afgesloten of opnieuw wordt opgestart, kan de bestaande verbindingen op een correcte manier worden afgesloten.

### <a name="test-it-move-service-instances"></a>Testen: service-exemplaren verplaatsen
Met behulp van de hulpprogram ma's voor test baarheid van Service Fabric kunt u een test scenario ontwerpen om deze situaties op verschillende manieren te testen:

1. De primaire replica van een stateful service verplaatsen.
   
    De primaire replica van een stateful service partitie kan om verschillende redenen worden verplaatst. Gebruik deze om de primaire replica van een specifieke partitie te richten om te zien hoe uw services op een zeer gecontroleerde manier reageren op de verplaatsing.
   
    ```powershell
   
    PS > Move-ServiceFabricPrimaryReplica -PartitionId 6faa4ffa-521a-44e9-8351-dfca0f7e0466 -ServiceName fabric:/MyApplication/MyService
   
    ```
2. Een knoop punt stoppen.
   
    Wanneer een knoop punt wordt gestopt, verplaatst Service Fabric alle service-exemplaren of partities die zich op dat knoop punt bevonden, naar een van de andere beschik bare knoop punten in het cluster. Gebruik deze om een situatie te testen waarbij een knoop punt uit uw cluster verloren gaat en alle service-exemplaren en replica's op dat knoop punt moeten worden verplaatst.
   
    U kunt een knoop punt stoppen met behulp van de Power shell **-cmdlet stop-ServiceFabricNode** :
   
    ```powershell
   
    PS > Stop-ServiceFabricNode -NodeName Node_1
   
    ```

## <a name="maintain-service-availability"></a>Beschik baarheid van service onderhouden
Als platform is Service Fabric ontworpen om een hoge Beschik baarheid van uw services te bieden. Maar in uitzonderlijke gevallen kunnen onderliggende problemen met de infra structuur nog steeds niet-beschik baarheid veroorzaken. Het is belang rijk om te testen op deze scenario's.

Stateful Services gebruiken een op een quorum gebaseerd systeem om de status voor hoge Beschik baarheid te repliceren. Dit betekent dat er een quorum van replica's beschikbaar moet zijn om schrijf bewerkingen uit te voeren. In zeldzame gevallen, zoals een verbreide hardwarestoring, is een quorum van replica's mogelijk niet beschikbaar. In dergelijke gevallen is het niet mogelijk om schrijf bewerkingen uit te voeren, maar u kunt wel lees bewerkingen uitvoeren.

### <a name="test-it-write-operation-unavailability"></a>Testen: Schrijf bewerking is niet beschikbaar
Door gebruik te maken van de hulpprogram ma's voor test baarheid in Service Fabric kunt u een fout injecteren die quorum verlies als test veroorzaakt. Hoewel een dergelijk scenario zeldzaam is, is het belang rijk dat clients en services die afhankelijk zijn van een stateful service, worden voor bereid om situaties te verwerken waarin ze geen schrijf aanvragen kunnen maken. Het is ook belang rijk dat de stateful service zichzelf op de hoogte is van deze mogelijkheid en kan worden gecommuniceerd naar bellers.

U kunt quorum verlies voor komen met behulp van de Power shell **-cmdlet invoke-ServiceFabricPartitionQuorumLoss** :

```powershell

PS > Invoke-ServiceFabricPartitionQuorumLoss -ServiceName fabric:/Myapplication/MyService -QuorumLossMode QuorumReplicas -QuorumLossDurationInSeconds 20

```

In dit voor beeld stellen we `QuorumLossMode` in `QuorumReplicas` om aan te geven dat we quorum verlies willen maken zonder alle replica's uit te voeren. Op deze manier kunnen Lees bewerkingen nog steeds worden uitgevoerd. Als u een scenario wilt testen waarbij een volledige partitie niet beschikbaar is, kunt u deze schakel optie instellen op `AllReplicas` .

## <a name="next-steps"></a>Volgende stappen
[Meer informatie over test acties](service-fabric-testability-actions.md)

[Meer informatie over scenario's voor testen](service-fabric-testability-scenarios.md)

