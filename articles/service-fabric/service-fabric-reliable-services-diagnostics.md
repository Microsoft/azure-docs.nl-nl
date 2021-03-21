---
title: Diagnostische gegevens over Azure Service Fabric stateful Reliable Services
description: Diagnostische functionaliteit voor stateful Reliable Services in azure Service Fabric
ms.topic: conceptual
ms.date: 8/24/2018
ms.openlocfilehash: 774a771d0c9701076a5d6c070963bf6224a571dd
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98789327"
---
# <a name="diagnostic-functionality-for-stateful-reliable-services"></a>Diagnosefunctionaliteit voor Stateful Reliable Services
De Azure Service Fabric stateful Reliable Services StatefulServiceBase-klasse verzendt [Event source](/dotnet/api/system.diagnostics.tracing.eventsource) -gebeurtenissen die kunnen worden gebruikt om fouten in de service op te sporen, inzicht te krijgen in hoe de runtime werkt en om te helpen bij het oplossen van problemen.

## <a name="eventsource-events"></a>Event source-gebeurtenissen
De naam van de Event source voor de stateful Reliable Services klasse StatefulServiceBase is ' micro soft-ServiceFabric-Services '. Gebeurtenissen van deze gebeurtenis bron worden weer gegeven in het venster [diagnostische gebeurtenissen](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md#view-service-fabric-system-events-in-visual-studio) wanneer de service [fouten in Visual Studio worden opgespoord](service-fabric-debugging-your-application.md).

Voor beelden van hulpprogram ma's en technologieën die u helpen bij het verzamelen en/of weer geven van Event source-gebeurtenissen zijn [PerfView](https://www.microsoft.com/download/details.aspx?id=28567), [Azure Diagnostics](../cloud-services/cloud-services-dotnet-diagnostics.md)en de [TraceEvent-bibliotheek van micro soft](https://www.nuget.org/packages/Microsoft.Diagnostics.Tracing.TraceEvent).

## <a name="events"></a>Gebeurtenissen
| Gebeurtenis naam | Gebeurtenis-id | Niveau | Gebeurtenisbeschrijving |
| --- | --- | --- | --- |
| StatefulRunAsyncInvocation |1 |Informatief |Verzonden wanneer de service RunAsync taak wordt gestart |
| StatefulRunAsyncCancellation |2 |Informatief |Verzonden wanneer de service RunAsync taak wordt geannuleerd |
| StatefulRunAsyncCompletion |3 |Informatief |Verzonden wanneer de service RunAsync taak is voltooid |
| StatefulRunAsyncSlowCancellation |4 |Waarschuwing |Verzonden wanneer de service RunAsync te lang duurt om de annulering te volt ooien |
| StatefulRunAsyncFailure |5 |Fout |Verzonden wanneer de service RunAsync-taak een uitzonde ring genereert |

## <a name="interpret-events"></a>Gebeurtenissen interpreteren
StatefulRunAsyncInvocation-, StatefulRunAsyncCompletion-en StatefulRunAsyncCancellation-gebeurtenissen zijn nuttig voor de service schrijver om inzicht te krijgen in de levens cyclus van een service, evenals de timing voor wanneer een service wordt gestart, geannuleerd of voltooid. Deze informatie kan nuttig zijn bij het opsporen van problemen met de service of de levens cyclus van de service.

Service schrijvers moeten de StatefulRunAsyncSlowCancellation-en StatefulRunAsyncFailure-gebeurtenissen zorgvuldig afhandelen omdat ze problemen met de service aangeven.

StatefulRunAsyncFailure wordt verzonden wanneer de service RunAsync () taak een uitzonde ring genereert. Normaal gesp roken duidt een uitzonde ring aan dat er een fout is opgetreden in de service. De uitzonde ring zorgt er ook voor dat de service mislukt, zodat deze naar een ander knoop punt wordt verplaatst. Deze bewerking kan duur zijn en kan binnenkomende aanvragen vertragen wanneer de service wordt verplaatst. Service schrijvers moeten de oorzaak van de uitzonde ring bepalen en, indien mogelijk, beperken.

StatefulRunAsyncSlowCancellation wordt verzonden wanneer een annulerings aanvraag voor de RunAsync-taak langer dan vier seconden duurt. Wanneer een service te lang duurt om de annulering te volt ooien, is dit van invloed op de mogelijkheid van de service om snel opnieuw op een ander knoop punt te worden gestart. Dit scenario kan van invloed zijn op de algehele Beschik baarheid van de service.

## <a name="performance-counters"></a>Prestatiemeteritems
De Reliable Services runtime definieert de volgende categorieën voor prestatie meter items:

| Categorie | Beschrijving |
| --- | --- |
| Transactionele Replicator Service Fabric |Tellers die specifiek zijn voor de Service Fabric transactionele Replicator van Azure |
| Service Fabric TStore |Tellers die specifiek zijn voor de Azure Service Fabric TStore |

De Service Fabric transactionele replicator wordt gebruikt door de [betrouw bare status beheerder](./service-fabric-reliable-services-introduction.md) om trans acties binnen een bepaalde set [replica's](service-fabric-concepts-replica-lifecycle.md)te repliceren.

De Service Fabric TStore is een onderdeel dat in [betrouw bare verzamelingen](./service-fabric-reliable-services-introduction.md) wordt gebruikt voor het opslaan en ophalen van sleutel-waardeparen.

De [Windows Performance Monitor](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc749249(v=ws.11)) -toepassing die standaard beschikbaar is in het Windows-besturings systeem, kan worden gebruikt voor het verzamelen en weer geven van gegevens van prestatie meter items. [Azure Diagnostics](../cloud-services/cloud-services-dotnet-diagnostics.md) is een andere optie voor het verzamelen van gegevens over prestatie meter items en het uploaden ervan naar Azure-tabellen.

### <a name="performance-counter-instance-names"></a>Instantie namen van prestatie meter items
Een cluster met een groot aantal betrouw bare Services of betrouw bare service partities beschikt over een groot aantal instanties voor transactionele Replicator-prestatie meter items. Dit is ook het geval voor prestatie meter items voor TStore, maar wordt ook vermenigvuldigd met het aantal betrouw bare woorden lijsten en de gebruikte betrouw bare wacht rijen. De instantie namen van het prestatie meter item kunnen helpen bij het identificeren van de specifieke [partitie](service-fabric-concepts-partitioning.md), service replica en State-provider in het geval van TStore, waaraan het prestatie meter exemplaar is gekoppeld.

#### <a name="service-fabric-transactional-replicator-category"></a>Categorie transactionele Replicator Service Fabric
De namen van de tellers voor de categorie `Service Fabric Transactional Replicator` hebben de volgende indeling:

`ServiceFabricPartitionId:ServiceFabricReplicaId`

*ServiceFabricPartitionId* is de teken reeks representatie van de service Fabric partitie-id waaraan het prestatie meter exemplaar is gekoppeld. De partitie-ID is een GUID en de teken reeks weergave wordt gegenereerd via de [`Guid.ToString`](/dotnet/api/system.guid.tostring#System_Guid_ToString_System_String_) indelings specificatie "D".

*ServiceFabricReplicaId* is de id die is gekoppeld aan een bepaalde replica van een betrouw bare service. De replica-ID is opgenomen in de exemplaar naam van het prestatie meter item om de uniekheid ervan te garanderen en te voor komen dat er conflicten ontstaan met andere prestatie meter exemplaren die worden gegenereerd door dezelfde partitie. Meer informatie over replica's en hun rol in reliable Services vindt u [hier](service-fabric-concepts-replica-lifecycle.md).

De naam van het volgende item exemplaar is gebruikelijk voor een teller onder de `Service Fabric Transactional Replicator` Categorie:

`00d0126d-3e36-4d68-98da-cc4f7195d85e:131652217797162571`

In het vorige voor beeld `00d0126d-3e36-4d68-98da-cc4f7195d85e` is de teken reeks representatie van de service Fabric partitie-id en `131652217797162571` de replica-id.

#### <a name="service-fabric-tstore-category"></a>Service Fabric categorie TStore
De namen van de tellers voor de categorie `Service Fabric TStore` hebben de volgende indeling:

`ServiceFabricPartitionId:ServiceFabricReplicaId:StateProviderId_PerformanceCounterInstanceDifferentiator_StateProviderName`

*ServiceFabricPartitionId* is de teken reeks representatie van de service Fabric partitie-id waaraan het prestatie meter exemplaar is gekoppeld. De partitie-ID is een GUID en de teken reeks weergave wordt gegenereerd via de [`Guid.ToString`](/dotnet/api/system.guid.tostring#System_Guid_ToString_System_String_) indelings specificatie "D".

*ServiceFabricReplicaId* is de id die is gekoppeld aan een bepaalde replica van een betrouw bare service. De replica-ID is opgenomen in de exemplaar naam van het prestatie meter item om de uniekheid ervan te garanderen en te voor komen dat er conflicten ontstaan met andere prestatie meter exemplaren die worden gegenereerd door dezelfde partitie. Meer informatie over replica's en hun rol in reliable Services vindt u [hier](service-fabric-concepts-replica-lifecycle.md).

*StateProviderId* is de id die aan een State-provider is gekoppeld binnen een betrouw bare service. De State-provider-ID is opgenomen in de exemplaar naam van het prestatie meter item om een TStore van een andere te onderscheiden.

*PerformanceCounterInstanceDifferentiator* is een ONDERSCHEIDINGS-id die is gekoppeld aan een prestatie meter exemplaar binnen een State-provider. Deze onderscheid is opgenomen in de exemplaar naam van het prestatie meter item om de uniekheid ervan te garanderen en te voor komen dat er conflicten optreden met andere instanties van prestatie meter items die zijn gegenereerd door dezelfde status provider.

*StateProviderName* is de naam die is gekoppeld aan een State-provider binnen een betrouw bare service. De naam van de State-provider is opgenomen in de exemplaar naam van het prestatie meter item waarmee gebruikers eenvoudig kunnen worden geïdentificeerd welke status het biedt.

De naam van het volgende item exemplaar is gebruikelijk voor een teller onder de `Service Fabric TStore` Categorie:

`00d0126d-3e36-4d68-98da-cc4f7195d85e:131652217797162571:142652217797162571_1337_urn:MyReliableDictionary/dataStore`

In het vorige voor beeld `00d0126d-3e36-4d68-98da-cc4f7195d85e` is de teken reeks representatie van de service Fabric partitie-id, `131652217797162571` de replica-id, `142652217797162571` de status provider-id en `1337` de instantie van het prestatie meter item onderscheid. `urn:MyReliableDictionary/dataStore` is de naam van de State-provider die gegevens opslaat voor de verzameling met de naam `urn:MyReliableDictionary` .

### <a name="transactional-replicator-performance-counters"></a>Prestatie meter items voor transactionele Replicator

De Reliable Services runtime verzendt de volgende gebeurtenissen onder de `Service Fabric Transactional Replicator` categorie

 Naam van het meteritem | Beschrijving |
| --- | --- |
| Begin trans actie bewerkingen per seconde | Het aantal nieuwe schrijf transacties dat per seconde wordt gemaakt.|
| Trans actie bewerkingen per seconde | Het aantal bewerkingen voor toevoegen/bijwerken/verwijderen dat is uitgevoerd op betrouw bare verzamelingen per seconde.|
| Bytes per seconde in logboek leegmaken | Het aantal bytes dat naar de schijf wordt leeg gemaakt door de transactionele Replicator per seconde |
| Vertraagde bewerkingen per seconde | Het aantal bewerkingen dat elke seconde door de transactionele Replicator is geweigerd vanwege beperking. |
| Gem. trans actie MS/commit | Gemiddelde doorvoer latentie per trans actie in milliseconden |
| Gem. latentie bij leegmaken (MS) | Gemiddelde duur van het wissen van schijven, geïnitieerd door de transactionele replicator in milliseconden |

### <a name="tstore-performance-counters"></a>Prestatie meter items TStore

De Reliable Services runtime verzendt de volgende gebeurtenissen onder de `Service Fabric TStore` categorie

 Naam van het meteritem | Beschrijving |
| --- | --- |
| Aantal items | Het aantal items in de Store.|
| Schijfgrootte | De totale schijf grootte, in bytes, van controlepunt bestanden voor de Store.|
| Schrijf bare bytes van het controle bestand per seconde | Het aantal geschreven bytes per seconde voor het meest recente controlepunt bestand.|
| Bytes per seconde kopiëren naar schijf overdracht | Het aantal gelezen schijf bytes (op de primaire replica) of geschreven (op een secundaire replica) per seconde tijdens een archief kopie.|

## <a name="next-steps"></a>Volgende stappen
[Event source-providers in PerfView](/archive/blogs/vancem/introduction-tutorial-logging-etw-events-in-c-system-diagnostics-tracing-eventsource)
