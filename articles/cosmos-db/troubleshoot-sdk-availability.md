---
title: De beschik baarheid van Azure Cosmos-Sdk's in multiregionale omgevingen vaststellen en oplossen
description: Meer informatie over de beschik baarheid van Azure Cosmos SDK als u in meerdere regionale omgevingen werkt.
author: ealsur
ms.service: cosmos-db
ms.date: 02/16/2021
ms.author: maquaran
ms.subservice: cosmosdb-sql
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: 641b7d44407f8f3760c673f45d69dcfdc8b363b8
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/18/2021
ms.locfileid: "100650980"
---
# <a name="diagnose-and-troubleshoot-the-availability-of-azure-cosmos-sdks-in-multiregional-environments"></a>De beschik baarheid van Azure Cosmos-Sdk's in multiregionale omgevingen vaststellen en oplossen
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

In dit artikel wordt het gedrag van de nieuwste versie van Azure Cosmos Sdk's beschreven wanneer u een connectiviteits probleem met een bepaalde regio ziet of wanneer er een failover optreedt voor een regio.

Alle Azure Cosmos Sdk's bieden u een optie om de regionale voor keur aan te passen. De volgende eigenschappen worden gebruikt in verschillende Sdk's:

* De eigenschap [Connection Policy. PreferredLocations](/dotnet/api/microsoft.azure.documents.client.connectionpolicy.preferredlocations) in de .NET v2-SDK.
* De eigenschappen [CosmosClientOptions. ApplicationRegion](/dotnet/api/microsoft.azure.cosmos.cosmosclientoptions.applicationregion) of [CosmosClientOptions. ApplicationPreferredRegions](/dotnet/api/microsoft.azure.cosmos.cosmosclientoptions.applicationpreferredregions) in de .net v3-SDK.
* De methode [CosmosClientBuilder. preferredRegions](/java/api/com.azure.cosmos.cosmosclientbuilder.preferredregions) in Java v4 SDK.
* De [CosmosClient.preferred_locations](/python/api/azure-cosmos/azure.cosmos.cosmos_client.cosmosclient) -para meter in de PYTHON-SDK.
* De [CosmosClientOptions. Connection Policy. preferredLocations](/javascript/api/@azure/cosmos/connectionpolicy#preferredlocations) -para meter in de JS-SDK.

Wanneer u de regionale voor keur instelt, zal de client verbinding maken met een regio zoals vermeld in de volgende tabel:

|Accounttype |Leesbewerkingen |Schrijfbewerkingen |
|------------------------|--|--|
| Enkele schrijf regio | Voorkeursregio | Primaire regio  |
| Meerdere schrijf regio's | Voorkeursregio | Voorkeursregio  |

Als u **geen voorkeurs regio instelt**, wordt de SDK-client standaard ingesteld op de primaire regio:

|Accounttype |Leesbewerkingen |Schrijfbewerkingen |
|------------------------|--|--|
| Enkele schrijf regio | Primaire regio | Primaire regio |
| Meerdere schrijf regio's | Primaire regio  | Primaire regio  |

> [!NOTE]
> Primaire regio verwijst naar de eerste regio in de [lijst met Azure Cosmos-account regio's](distribute-data-globally.md).
> Als de waarden die zijn opgegeven als regionale voor keur niet overeenkomen met een van de bestaande Azure-regio's, worden ze genegeerd. Als ze overeenkomen met een bestaande regio, maar het account niet naar dit gebied wordt gerepliceerd, zal de client verbinding maken met de volgende voorkeurs regio die overeenkomt met of aan de primaire regio.

> [!WARNING]
> Het uitschakelen van de herdetectie van het eind punt (dat wil zeggen instellen op false) bij de client configuratie zorgt voor het uitschakelen van alle failover-en beschikbaarheids logica die in dit document wordt beschreven.
> Deze configuratie kan worden gebruikt door de volgende para meters in elke Azure Cosmos-SDK:
>
> * De eigenschap [Connection Policy. EnableEndpointRediscovery](/dotnet/api/microsoft.azure.documents.client.connectionpolicy.enableendpointdiscovery) in de .NET v2-SDK.
> * De methode [CosmosClientBuilder. endpointDiscoveryEnabled](/java/api/com.azure.cosmos.cosmosclientbuilder.endpointdiscoveryenabled) in Java v4 SDK.
> * De [CosmosClient.enable_endpoint_discovery](/python/api/azure-cosmos/azure.cosmos.cosmos_client.cosmosclient) -para meter in de PYTHON-SDK.
> * De [CosmosClientOptions. Connection Policy. enableEndpointDiscovery](/javascript/api/@azure/cosmos/connectionpolicy#enableEndpointDiscovery) -para meter in de JS-SDK.

Onder normale omstandigheden zal de SDK-client verbinding maken met de voorkeurs regio (als een regionale voor keur is ingesteld) of aan de primaire regio (als er geen voor keur is ingesteld), en de bewerkingen worden beperkt tot die regio, tenzij een van de onderstaande scenario's zich voordoet.

In dergelijke gevallen worden logboeken door de client met de Azure Cosmos SDK beschikbaar gemaakt en worden de gegevens voor nieuwe pogingen opgenomen als onderdeel van de **Diagnostische gegevens** van de bewerking:

* De eigenschap *RequestDiagnosticsString* op antwoorden in de .NET v2-SDK.
* De *Diagnostische* eigenschap voor antwoorden en uitzonde ringen in de .net v3 SDK.
* De methode *getDiagnostics ()* voor antwoorden en uitzonderingen in Java V4 SDK.

Bij het bepalen van de volgende regio in de volg orde van voor keur, gebruikt de SDK-client de lijst met account regio's, waarbij de prioriteit van de gewenste regio's (indien van toepassing) kan worden bepaald.

Zie de [sla's voor Beschik baarheid](high-availability.md#slas-for-availability)voor uitgebreide informatie over sla-garanties tijdens deze gebeurtenissen.

## <a name="removing-a-region-from-the-account"></a><a id="remove-region"></a>Een regio uit het account verwijderen

Wanneer u een regio uit een Azure Cosmos-account verwijdert, detecteert elke SDK-client die actief gebruikmaakt van het account, het verwijderen van de regio via een back-end-respons code. De client markeert vervolgens het regionale eind punt als niet-beschikbaar. De client probeert de huidige bewerking opnieuw en alle toekomstige bewerkingen worden permanent doorgestuurd naar de volgende regio in de volg orde van voor keur. Als de lijst met voor keuren slechts één vermelding had (of leeg was), maar het account andere regio's beschikbaar heeft, wordt de route naar de volgende regio in de lijst met accounts doorgestuurd.

## <a name="adding-a-region-to-an-account"></a>Een regio toevoegen aan een account

De Azure Cosmos SDK-client leest elke vijf minuten de account configuratie en vernieuwt de regio's waarvan het op de hoogte is.

Als u een regio verwijdert en later weer toevoegt aan het account en de toegevoegde regio een hogere regionale voorkeurs volgorde heeft in de SDK-configuratie dan de huidige verbonden regio, zal de SDK overschakelen naar het permanent gebruik van deze regio. Nadat de toegevoegde regio is gedetecteerd, worden alle toekomstige aanvragen ernaar doorgeleid.

Als u de client zo configureert dat bij voor keur verbinding wordt gemaakt met een regio die het Azure Cosmos-account niet heeft, wordt de voorkeurs regio genegeerd. Als u die regio later toevoegt, detecteert de client deze en gaat deze over naar die regio.

## <a name="fail-over-the-write-region-in-a-single-write-region-account"></a><a id="manual-failover-single-region"></a>Failover uitvoeren voor de schrijf regio in één schrijf regio account

Als u een failover van de huidige schrijf regio initieert, mislukt de volgende schrijf aanvraag met een bekend back-end-antwoord. Wanneer dit antwoord wordt gedetecteerd, voert de client een query uit op het account om te zien wat de nieuwe schrijf regio is en gaat het om de huidige bewerking opnieuw uit te voeren en alle toekomstige schrijf bewerkingen permanent naar de nieuwe regio te routeren.

## <a name="regional-outage"></a>Regionale storing

Als het account een enkele schrijf regio is en de regionale storing optreedt tijdens een schrijf bewerking, is het gedrag vergelijkbaar met een [hand matige failover](#manual-failover-single-region). Voor lees aanvragen of meerdere accounts voor het schrijven van regio's is het gedrag vergelijkbaar met het [verwijderen van een regio](#remove-region).

## <a name="session-consistency-guarantees"></a>Garantie voor sessie consistentie

Bij het gebruik van [sessie consistentie](consistency-levels.md#guarantees-associated-with-consistency-levels)moet de client garanderen dat het eigen schrijf bewerkingen kan lezen. In accounts met een enkele schrijf regio waarbij de voor keur voor de Lees regio afwijkt van de schrijf regio, kunnen er gevallen zijn waarin de gebruiker een schrijf actie uitvoert en wanneer een lees bewerking wordt uitgevoerd vanuit een lokale regio, de lokale regio de gegevens replicatie nog niet heeft ontvangen (snelheid van de Light beperking). In dergelijke gevallen detecteert de SDK de specifieke fout voor de Lees bewerking en wordt opnieuw geprobeerd het lezen op de primaire regio uit te proberen om consistentie van de sessie te garanderen.

## <a name="transient-connectivity-issues-on-tcp-protocol"></a>Problemen met de tijdelijke verbinding met het TCP-protocol

In scenario's waarin de Azure Cosmos SDK-client is geconfigureerd voor het gebruik van het TCP-protocol voor een bepaalde aanvraag, kunnen er situaties zijn waarin de netwerk omstandigheden de communicatie met een bepaald eind punt tijdelijk beïnvloeden. Deze tijdelijke netwerk omstandigheden kunnen worden geoppereerd als TCP-time-outs en service niet beschikbaar (HTTP 503-fouten). De client probeert de aanvraag gedurende enkele seconden lokaal uit te voeren op hetzelfde eind punt voordat de fout wordt halen.

Als de gebruiker een lijst met voorkeurs regio's heeft geconfigureerd met meer dan één regio en het Azure Cosmos-account meerdere schrijf regio's of een enkele schrijf regio is en de bewerking een lees aanvraag is, detecteert de client de lokale fout en probeert die ene bewerking in de volgende regio uit de voorkeurs lijst uit te voeren.

## <a name="next-steps"></a>Volgende stappen

* Controleer de [Beschik baarheid van sla's](high-availability.md#slas-for-availability).
* De nieuwste [.NET SDK](sql-api-sdk-dotnet-standard.md) gebruiken
* De meest recente [Java-SDK](sql-api-sdk-java-v4.md) gebruiken
* De meest recente [python-SDK](sql-api-sdk-python.md) gebruiken
* De nieuwste [knoop punt-SDK](sql-api-sdk-node.md) gebruiken
