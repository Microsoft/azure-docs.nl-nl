---
title: Diagnose en controle van actors
description: In dit artikel worden de functies voor diagnostische gegevens en prestatie bewaking in de Service Fabric Reliable Actors runtime beschreven, inclusief de gebeurtenissen en prestatie meter items die door de functie worden gegenereerd.
author: abhishekram
ms.topic: conceptual
ms.date: 10/26/2017
ms.author: abhisram
ms.openlocfilehash: ebaedb5369f3b39372262bfde526706e8d069418
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/26/2021
ms.locfileid: "98789613"
---
# <a name="diagnostics-and-performance-monitoring-for-reliable-actors"></a>Diagnose- en prestatiecontrole voor betrouwbare actoren
De Reliable Actors runtime verzendt gebeurtenissen van [Event source](/dotnet/api/system.diagnostics.tracing.eventsource) en [prestatie meter items](/dotnet/api/system.diagnostics.performancecounter). Deze bieden inzicht in hoe de runtime werkt en helpt u bij het oplossen van problemen en het controleren van prestaties.

## <a name="eventsource-events"></a>Event source-gebeurtenissen
De naam van de Event source-provider voor de Reliable Actors runtime is ' micro soft-ServiceFabric-Actors '. Gebeurtenissen van deze gebeurtenis bron worden weer gegeven in het venster [diagnostische gebeurtenissen](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md#view-service-fabric-system-events-in-visual-studio) wanneer de actor-toepassing [in Visual Studio wordt opgespoord](service-fabric-debugging-your-application.md).

Voor beelden van hulpprogram ma's en technologieën die u helpen bij het verzamelen en/of weer geven van Event source-gebeurtenissen zijn [PerfView](https://www.microsoft.com/download/details.aspx?id=28567), [Azure Diagnostics](../cloud-services/cloud-services-dotnet-diagnostics.md), [semantische logboek registratie](/previous-versions/msp-n-p/dn774980(v=pandp.10))en de [TraceEvent-bibliotheek van micro soft](https://www.nuget.org/packages/Microsoft.Diagnostics.Tracing.TraceEvent).

### <a name="keywords"></a>Trefwoorden
Alle gebeurtenissen die deel uitmaken van de Reliable Actors Event source zijn gekoppeld aan een of meer tref woorden. Hiermee wordt gefilterd op gebeurtenissen die worden verzameld. De volgende trefwoord bits zijn gedefinieerd.

| Bitmask | Beschrijving |
| --- | --- |
| 0x1 |Set van belang rijke gebeurtenissen die de werking van de runtime van de Fabric actors samenvatten. |
| 0x2 |Set gebeurtenissen die de actor-methode aanroepen beschrijven. Zie het [inleidende onderwerp over actors](service-fabric-reliable-actors-introduction.md)voor meer informatie. |
| 0x4 |Set gebeurtenissen met betrekking tot de actor status. Zie het onderwerp over [actor State Management](service-fabric-reliable-actors-state-management.md)voor meer informatie. |
| 0x8 |Set gebeurtenissen met betrekking tot gelijktijdigheid op basis van een functie in de actor. Zie het onderwerp over [gelijktijdigheid](service-fabric-reliable-actors-introduction.md#concurrency)voor meer informatie. |

## <a name="performance-counters"></a>Prestatiemeteritems
De Reliable Actors runtime definieert de volgende categorieën voor prestatie meter items.

| Categorie | Beschrijving |
| --- | --- |
| Service Fabric actor |Tellers die specifiek zijn voor Azure Service Fabric actors, zoals het tijdstip van het opslaan van de actor status |
| Service Fabric actor-methode |Tellers die specifiek zijn voor methoden die worden geïmplementeerd door Service Fabric actors, bijvoorbeeld hoe vaak een actor-methode wordt aangeroepen |

Elk van de bovenstaande categorieën heeft een of meer tellers.

De [Windows Performance Monitor](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc749249(v=ws.11)) -toepassing die standaard beschikbaar is in het Windows-besturings systeem, kan worden gebruikt voor het verzamelen en weer geven van gegevens van prestatie meter items. [Azure Diagnostics](../cloud-services/cloud-services-dotnet-diagnostics.md) is een andere optie voor het verzamelen van gegevens over prestatie meter items en het uploaden ervan naar Azure-tabellen.

### <a name="performance-counter-instance-names"></a>Instantie namen van prestatie meter items
Een cluster met een groot aantal actor Services-of actor service-partities krijgt een groot aantal instanties voor prestatie meter items voor actors. De instantie namen van het prestatie meter item kunnen helpen bij het identificeren van de specifieke [partitie](service-fabric-reliable-actors-platform.md#service-fabric-partition-concepts-for-actors) en actor methode (indien van toepassing) waaraan het prestatie meter exemplaar is gekoppeld.

#### <a name="service-fabric-actor-category"></a>Service Fabric actor-categorie
De namen van de tellers voor de categorie `Service Fabric Actor` hebben de volgende indeling:

`ServiceFabricPartitionID_ActorsRuntimeInternalID`

*ServiceFabricPartitionID* is de teken reeks representatie van de service Fabric partitie-id waaraan het prestatie meter exemplaar is gekoppeld. De partitie-ID is een GUID en de teken reeks representatie wordt gegenereerd via de [`Guid.ToString`](/dotnet/api/system.guid.tostring#System_Guid_ToString_System_String_) methode met de indelings specificatie "D".

*ActorRuntimeInternalID* is de teken reeks representatie van een 64-bits geheel getal dat wordt gegenereerd door de Fabric actors-runtime voor intern gebruik. Dit is opgenomen in de exemplaar naam van het prestatie meter item om de uniekheid ervan te garanderen en te voor komen dat er conflicten ontstaan met andere instantie namen van prestatie meter items. Gebruikers moeten dit gedeelte van de exemplaar naam van het prestatie meter item niet interpreteren.

Hier volgt een voor beeld van de naam van een item exemplaar voor een teller die tot de `Service Fabric Actor` categorie behoort:

`2740af29-78aa-44bc-a20b-7e60fb783264_635650083799324046`

In het bovenstaande voor beeld `2740af29-78aa-44bc-a20b-7e60fb783264` is de teken reeks representatie van de service Fabric partitie-id en `635650083799324046` is de 64-bits id die wordt gegenereerd voor het interne gebruik van de runtime.

#### <a name="service-fabric-actor-method-category"></a>Categorie Service Fabric actor-methode
De namen van de tellers voor de categorie `Service Fabric Actor Method` hebben de volgende indeling:

`MethodName_ActorsRuntimeMethodId_ServiceFabricPartitionID_ActorsRuntimeInternalID`

*MethodName* is de naam van de actor-methode waaraan het prestatie meter exemplaar is gekoppeld. De indeling van de methode naam wordt bepaald op basis van een deel van de logica in de runtime van de Fabric actors waarmee de Lees baarheid van de naam wordt gebalanceerd met beperkingen voor de maximum lengte van de instantie namen van het prestatie meter item in Windows.

*ActorsRuntimeMethodId* is de teken reeks representatie van een 32-bits geheel getal dat wordt gegenereerd door de Fabric actors-runtime voor intern gebruik. Dit is opgenomen in de exemplaar naam van het prestatie meter item om de uniekheid ervan te garanderen en te voor komen dat er conflicten ontstaan met andere instantie namen van prestatie meter items. Gebruikers moeten dit gedeelte van de exemplaar naam van het prestatie meter item niet interpreteren.

*ServiceFabricPartitionID* is de teken reeks representatie van de service Fabric partitie-id waaraan het prestatie meter exemplaar is gekoppeld. De partitie-ID is een GUID en de teken reeks representatie wordt gegenereerd via de [`Guid.ToString`](/dotnet/api/system.guid.tostring#System_Guid_ToString_System_String_) methode met de indelings specificatie "D".

*ActorRuntimeInternalID* is de teken reeks representatie van een 64-bits geheel getal dat wordt gegenereerd door de Fabric actors-runtime voor intern gebruik. Dit is opgenomen in de exemplaar naam van het prestatie meter item om de uniekheid ervan te garanderen en te voor komen dat er conflicten ontstaan met andere instantie namen van prestatie meter items. Gebruikers moeten dit gedeelte van de exemplaar naam van het prestatie meter item niet interpreteren.

Hier volgt een voor beeld van de naam van een item exemplaar voor een teller die tot de `Service Fabric Actor Method` categorie behoort:

`ivoicemailboxactor.leavemessageasync_2_89383d32-e57e-4a9b-a6ad-57c6792aa521_635650083804480486`

In het bovenstaande voor beeld is `ivoicemailboxactor.leavemessageasync` de naam van de methode, de `2` 32-bits id die is gegenereerd voor het interne gebruik van de runtime, `89383d32-e57e-4a9b-a6ad-57c6792aa521` is de teken reeks representatie van de service Fabric partitie-id en `635650083804480486` is de 64-bits-id die is gegenereerd voor het interne gebruik van de runtime.

## <a name="list-of-events-and-performance-counters"></a>Lijst met gebeurtenissen en prestatiemeters
### <a name="actor-method-events-and-performance-counters"></a>Actor-methode gebeurtenissen en prestatie meter items
De Reliable Actors runtime verzendt de volgende gebeurtenissen met betrekking tot [actor-methoden](service-fabric-reliable-actors-introduction.md).

| Gebeurtenis naam | Gebeurtenis-id | Niveau | Zoek | Beschrijving |
| --- | --- | --- | --- | --- |
| ActorMethodStart |7 |Uitgebreid |0x2 |Actors-runtime staat op het punt een actor-methode aan te roepen. |
| ActorMethodStop |8 |Uitgebreid |0x2 |Het uitvoeren van een actor-methode is voltooid. Dat wil zeggen dat de asynchrone aanroep van de runtime naar de actor methode heeft geretourneerd en dat de taak die door de actor-methode is geretourneerd, is voltooid. |
| ActorMethodThrewException |9 |Waarschuwing |0x3 |Er is een uitzonde ring opgetreden tijdens het uitvoeren van een actor-methode, hetzij tijdens de asynchrone aanroep van de runtime naar de actor-methode of tijdens de uitvoering van de taak die door de actor-methode is geretourneerd. Deze gebeurtenis geeft aan dat er een fout is opgetreden in de actor code die moet worden onderzocht. |

De Reliable Actors runtime publiceert de volgende prestatie meter items die betrekking hebben op de uitvoering van actor-methoden.

| Category name | Naam van het meteritem | Beschrijving |
| --- | --- | --- |
| Service Fabric actor-methode |Aanroepen per seconde |Aantal keren dat de actor-service methode per seconde wordt aangeroepen |
| Service Fabric actor-methode |Gemiddeld aantal milliseconden per aanroep |Gebruikte tijd in milliseconden voor het uitvoeren van de service methode actor |
| Service Fabric actor-methode |Uitzonde ringen gegenereerd per seconde |Aantal keren dat de actor-service methode een uitzonde ring heeft gegenereerd per seconde |

### <a name="concurrency-events-and-performance-counters"></a>Gelijktijdigheid van gebeurtenissen en prestatie meter items
De Reliable Actors runtime verzendt de volgende gebeurtenissen met betrekking tot [gelijktijdigheid](service-fabric-reliable-actors-introduction.md#concurrency).

| Gebeurtenis naam | Gebeurtenis-id | Niveau | Zoek | Beschrijving |
| --- | --- | --- | --- | --- |
| ActorMethodCallsWaitingForLock |12 |Uitgebreid |0x8 |Deze gebeurtenis is geschreven aan het begin van elke nieuwe functie voor het inschakelen van een actor. Het bevat het aantal in behandeling zijnde actor-aanroepen dat wacht om de vergren deling per actor te verkrijgen waarmee gelijktijdigheid op basis van een bocht wordt afgedwongen. |

De Reliable Actors runtime publiceert de volgende prestatie meters die betrekking hebben op gelijktijdigheid.

| Category name | Naam van het meteritem | Beschrijving |
| --- | --- | --- |
| Service Fabric actor |aantal actor-aanroepen tijdens het wachten op actor vergrendeling |Het aantal in behandeling zijnde actor-aanroepen dat wacht om de vergren deling per actor te verkrijgen waarmee gelijktijdigheid op basis van een hand gave wordt afgedwongen |
| Service Fabric actor |Gemiddeld aantal milliseconden per wachten op vergren deling |Gebruikte tijd (in milliseconden) om de vergren deling per actor te verkrijgen waarmee gelijktijdig gebruik op basis van een bocht wordt afgedwongen |
| Service Fabric actor |Gemiddelde milliseconden actor-vergren deling geblokkeerd |Tijd (in milliseconden) waarvoor de vergren deling per actor wordt gehouden |

### <a name="actor-state-management-events-and-performance-counters"></a>Gebeurtenissen en prestatie meter items voor actor status beheer
De Reliable Actors runtime verzendt de volgende gebeurtenissen met betrekking tot het [beheer van actor status](service-fabric-reliable-actors-state-management.md).

| Gebeurtenis naam | Gebeurtenis-id | Niveau | Zoek | Beschrijving |
| --- | --- | --- | --- | --- |
| ActorSaveStateStart |10 |Uitgebreid |0x4 |Actors-runtime staat op het punt de actor status op te slaan. |
| ActorSaveStateStop |11 |Uitgebreid |0x4 |Actors-runtime heeft het opslaan van de actor status voltooid. |

Met de Reliable Actors runtime worden de volgende prestatie meter items gepubliceerd die betrekking hebben op het beheer van actor status.

| Category name | Naam van het meteritem | Beschrijving |
| --- | --- | --- |
| Service Fabric actor |Gemiddeld aantal milliseconden per opslag status bewerking |Gebruikte tijd in milliseconden voor het opslaan van de actor status |
| Service Fabric actor |Gemiddeld aantal milliseconden per bewerking van de laad status |Gebruikte tijd in milliseconden voor het laden van de actor status |

### <a name="events-related-to-actor-replicas"></a>Gebeurtenissen met betrekking tot actor replica's
De Reliable Actors runtime verzendt de volgende gebeurtenissen met betrekking tot [actor replica's](service-fabric-reliable-actors-platform.md#service-fabric-partition-concepts-for-actors).

| Gebeurtenis naam | Gebeurtenis-id | Niveau | Zoek | Beschrijving |
| --- | --- | --- | --- | --- |
| ReplicaChangeRoleToPrimary |1 |Informatief |0x1 |De rol van de actor replica is gewijzigd in Primary. Dit betekent dat de actors voor deze partitie worden gemaakt in deze replica. |
| ReplicaChangeRoleFromPrimary |2 |Informatief |0x1 |De rol van de actor replica is gewijzigd in niet-primair. Dit betekent dat de actors voor deze partitie niet meer worden gemaakt in deze replica. Er worden geen nieuwe aanvragen bezorgd bij Actors die al zijn gemaakt in deze replica. De actors worden vernietigd nadat alle lopende aanvragen zijn voltooid. |

### <a name="actor-activation-and-deactivation-events-and-performance-counters"></a>Actor-activering en deactivering van gebeurtenissen en prestatie meter items
De Reliable Actors runtime verzendt de volgende gebeurtenissen met betrekking tot de [Activering en deactivering van actor](service-fabric-reliable-actors-lifecycle.md).

| Gebeurtenis naam | Gebeurtenis-id | Niveau | Zoek | Beschrijving |
| --- | --- | --- | --- | --- |
| ActorActivated |5 |Informatief |0x1 |Er is een actor geactiveerd. |
| ActorDeactivated |6 |Informatief |0x1 |Een actor is gedeactiveerd. |

Met de Reliable Actors runtime worden de volgende prestatie meter items gepubliceerd die betrekking hebben op het activeren en deactiveren van actor.

| Category name | Naam van het meteritem | Beschrijving |
| --- | --- | --- |
| Service Fabric actor |Gemiddelde OnActivateAsync milliseconden |Gebruikte tijd in milliseconden voor het uitvoeren van de methode OnActivateAsync |

### <a name="actor-request-processing-performance-counters"></a>Prestatie meter items verwerking actor-aanvraag
Wanneer een client een methode aanroept via een actor-proxy object, resulteert dit in een aanvraag bericht dat via het netwerk wordt verzonden naar de actor-service. De service verwerkt het aanvraag bericht en stuurt een antwoord terug naar de client. Met de Reliable Actors runtime worden de volgende prestatie meter items gepubliceerd die betrekking hebben op de verwerking van actor-aanvragen.

| Category name | Naam van het meteritem | Beschrijving |
| --- | --- | --- |
| Service Fabric actor |aantal openstaande aanvragen |Aantal aanvragen dat wordt verwerkt in de service |
| Service Fabric actor |Gemiddeld aantal milliseconden per aanvraag |Gebruikte tijd (in milliseconden) van de service voor het verwerken van een aanvraag |
| Service Fabric actor |Gemiddeld aantal milliseconden voor het deserialiseren van aanvragen |Gebruikte tijd (in milliseconden) voor het deserialiseren van het bericht actor Request wanneer het wordt ontvangen bij de service |
| Service Fabric actor |Gemiddeld aantal milliseconden voor het serialiseren van antwoorden |Gebruikte tijd (in milliseconden) voor het serialiseren van het respons bericht van de actor bij de service voordat het antwoord naar de client wordt verzonden |

## <a name="next-steps"></a>Volgende stappen
* [Hoe Reliable Actors het Service Fabric platform gebruiken?](service-fabric-reliable-actors-platform.md)
* [Referentie documentatie voor actor-API](/previous-versions/azure/dn971626(v=azure.100))
* [Voorbeeldcode](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [Event source-providers in PerfView](/archive/blogs/vancem/introduction-tutorial-logging-etw-events-in-c-system-diagnostics-tracing-eventsource)
