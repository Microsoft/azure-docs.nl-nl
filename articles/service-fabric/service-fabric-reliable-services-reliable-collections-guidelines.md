---
title: Richt lijnen voor betrouw bare verzamelingen
description: Richt lijnen en aanbevelingen voor het gebruik van Service Fabric betrouw bare verzamelingen in een Azure Service Fabric-toepassing.
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: f12db76f324d07c178b49150d4e574476e7d9929
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98784322"
---
# <a name="guidelines-and-recommendations-for-reliable-collections-in-azure-service-fabric"></a>Richt lijnen en aanbevelingen voor betrouw bare verzamelingen in azure Service Fabric
Deze sectie bevat richt lijnen voor het gebruik van betrouw bare status Manager en betrouw bare verzamelingen. Het doel is om gebruikers te helpen veelvoorkomende Valk uilen te voor komen.

De richt lijnen zijn ingedeeld als eenvoudige aanbevelingen met betrekking tot de voor *waarden.* *Dit* *kan van* pas *komen* .

* Wijzig geen object van het aangepaste type dat wordt geretourneerd door Lees bewerkingen (bijvoorbeeld `TryPeekAsync` of `TryGetValueAsync` ). Betrouw bare verzamelingen, net als gelijktijdige verzamelingen, retour neren een verwijzing naar de objecten en niet naar een kopie.
* Kopieer het geretourneerde object van een aangepast type grondig voordat u het wijzigt. Aangezien structs en ingebouwde typen pass-by-value zijn, hoeft u geen diep gaande kopie te maken, tenzij deze velden of eigenschappen bevatten die u wilt wijzigen.
* Gebruik deze niet `TimeSpan.MaxValue` voor time-outs. Time-outs moeten worden gebruikt voor het detecteren van deadlocks.
* Gebruik geen trans actie nadat deze is vastgelegd, afgebroken of verwijderd.
* Gebruik geen inventarisatie buiten het transactie bereik waarin het is gemaakt.
* Maak geen trans actie in een andere trans actie- `using` instructie, omdat deze impasses kan veroorzaken.
* Maak geen betrouw bare status met `IReliableStateManager.GetOrAddAsync` en gebruik de betrouw bare status in dezelfde trans actie. Dit resulteert in een InvalidOperationException.
* Zorg ervoor dat uw `IComparable<TKey>` implementatie juist is. Het systeem maakt afhankelijk van het `IComparable<TKey>` samen voegen van controle punten en rijen.
* Gebruik de update vergrendeling bij het lezen van een item met de bedoeling om het bij te werken om een bepaalde klasse van deadlocks te voor komen.
* Overweeg het aantal betrouw bare verzamelingen per partitie kleiner dan 1000 te houden. Geniet de voor keur aan betrouw bare verzamelingen met meer items voor betrouwbaardere verzamelingen met minder items.
* Overweeg om uw items te bewaren (bijvoorbeeld TKey + TValue voor betrouw bare woorden lijst) onder 80 KB: smaller. Dit reduceert de hoeveelheid Large Object heap en de vereisten voor schijf-en netwerk-i/o. Vaak vermindert het het repliceren van dubbele gegevens wanneer er slechts één klein deel van de waarde wordt bijgewerkt. Gebruikelijke manier om dit in een betrouw bare woorden lijst te krijgen, is het opdelen van rijen in meerdere rijen.
* U kunt de functionaliteit voor back-up en herstel gebruiken om herstel na nood geval te hebben.
* Vermijd het combi neren van bewerkingen met één entiteit en bewerkingen met meerdere entiteiten (bijvoorbeeld `GetCountAsync` `CreateEnumerableAsync` ) in dezelfde trans actie als gevolg van de verschillende isolatie niveaus.
* Do InvalidOperationException. Gebruikers transacties kunnen om verschillende redenen door het systeem worden afgebroken. Bijvoorbeeld wanneer de betrouw bare status Manager de rol van de hoofd beheerder wijzigt of wanneer een langlopende trans actie de afkap ping van het transactionele logboek blokkeert. In dergelijke gevallen kan de gebruiker InvalidOperationException ontvangen die aangeeft dat de trans actie al is beëindigd. Ervan uitgaande dat de gebruiker de trans actie niet heeft beëindigd en de beste manier om deze uitzonde ring te verwerken is om de trans actie te verwijderen, Controleer of het annulerings token is gesignaleerd (of de rol van de replica is gewijzigd), en als er geen nieuwe trans actie wordt gemaakt en opnieuw wordt uitgevoerd.  

Hier volgen enkele dingen die u moet onthouden:

* De standaard time-out is vier seconden voor alle betrouw bare verzamelings-Api's. De meeste gebruikers moeten de standaard time-out gebruiken.
* Het standaard annulerings token bevindt zich `CancellationToken.None` in alle betrouw bare verzamelingen-api's.
* De sleutel type parameter (*TKey*) voor een betrouw bare woorden lijst moet correct worden geïmplementeerd `GetHashCode()` en `Equals()` . Sleutels moeten onveranderbaar zijn.
* Voor een hoge Beschik baarheid voor de betrouw bare verzamelingen moet elke service ten minste beschikken over een doel en minimale grootte van de replicaset van 3.
* Bij Lees bewerkingen op de secundaire versie kunnen versies worden gelezen die niet in het quorum zijn vastgelegd.
  Dit betekent dat een versie van gegevens die is gelezen uit één secundair mogelijk onwaar is voor de voortgang.
  Lees bewerkingen van de primaire zijn altijd stabiel: kan nooit ONWAAR worden uitgevoerd.
* De beveiliging/privacy van de gegevens die door uw toepassing worden bewaard in een betrouw bare verzameling is uw beslissing en is onderworpen aan de beveiligingen van uw opslag beheer. dat wil zeggen. Versleuteling van de schijf van het besturings systeem kan worden gebruikt om uw gegevens in rust te beveiligen.
* `ReliableDictionary` opsomming maakt gebruik van een gesorteerde gegevens structuur, gesorteerd op sleutel. Om opsommings efficiënt te maken, worden door voeren toegevoegd aan een tijdelijke hashtabel en later verplaatst naar het hoofd controlepunt van de primaire gesorteerde gegevens structuur. Toevoegingen/updates/verwijderen hebben de best mogelijke runtime van O (1) en het ergste geval van een runtime van O (log n), in het geval van validatie controles op de aanwezigheid van de sleutel. Kan worden opgehaald als O (1) of O (logboek n), afhankelijk van het feit of u een recente door Voer of een oudere commit-bewerking leest.

## <a name="volatile-reliable-collections"></a>Vluchtige betrouw bare verzamelingen
Houd rekening met het volgende als u wilt bepalen of u vluchtige betrouw bare verzamelingen wilt gebruiken:

* ```ReliableDictionary``` heeft vluchtige ondersteuning
* ```ReliableQueue``` heeft vluchtige ondersteuning
* ```ReliableConcurrentQueue``` heeft geen vluchtige ondersteuning
* Blijvende Services kunnen niet vluchtig worden gemaakt. ```HasPersistedState```Als u de markering wilt wijzigen, ```false``` moet u de volledige service helemaal opnieuw maken
* Tijdelijke Services kunnen niet blijvend worden gemaakt. ```HasPersistedState```Als u de markering wilt wijzigen, ```true``` moet u de volledige service helemaal opnieuw maken
* ```HasPersistedState``` is een serviceniveau configuratie. Dit betekent dat **alle** verzamelingen persistent of vluchtig zijn. U kunt geen vluchtige en permanente verzamelingen combi neren
* Quorum verlies van een vluchtige partitie resulteert in volledig verlies van gegevens
* Backup en Restore is niet beschikbaar voor vluchtige Services

## <a name="next-steps"></a>Volgende stappen
* [Werken met betrouwbare verzamelingen](service-fabric-work-with-reliable-collections.md)
* [Trans acties en vergren delingen](service-fabric-reliable-services-reliable-collections-transactions-locks.md)
* Gegevens beheren
  * [Back-up en herstel](service-fabric-reliable-services-backup-restore.md)
  * [Meldingen](service-fabric-reliable-services-notifications.md)
  * [Serialisatie en upgrade](service-fabric-application-upgrade-data-serialization.md)
  * [Configuratie van betrouw bare status Manager](service-fabric-reliable-services-configuration.md)
* Andere
  * [Snelstartgids Reliable Services](service-fabric-reliable-services-quick-start.md)
  * [Naslag informatie voor ontwikkel aars voor betrouw bare verzamelingen](/dotnet/api/microsoft.servicefabric.data.collections#microsoft_servicefabric_data_collections)
