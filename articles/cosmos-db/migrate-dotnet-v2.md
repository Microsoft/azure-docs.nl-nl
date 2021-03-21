---
title: Migreer uw toepassing voor het gebruik van de Azure Cosmos DB .NET SDK 2,0 (micro soft. Azure. Cosmos)
description: Meer informatie over het bijwerken van uw bestaande .NET-toepassing van de V1 SDK naar .NET SDK v2 voor Core-API (SQL).
author: stefArroyo
ms.author: esarroyo
ms.service: cosmos-db
ms.topic: how-to
ms.date: 10/15/2020
ms.openlocfilehash: 88c40452da72ed4ab43d2d7613636136a5b78cfe
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94550062"
---
# <a name="migrate-your-application-to-use-the-azure-cosmos-db-net-sdk-v2"></a>Uw toepassing migreren om de Azure Cosmos DB .NET SDK v2 te gebruiken

> [!IMPORTANT]
> Het is belang rijk te weten dat de V3 van de .NET SDK [momenteel beschikbaar is](migrate-dotnet-v3.md)en dat er een migratie plan van v2 naar v3 beschikbaar is. Voor meer informatie over de Azure Cosmos DB .NET SDK v2 raadpleegt u de [release opmerkingen](sql-api-sdk-dotnet.md), de [.net github-opslag plaats](https://github.com/Azure/azure-cosmos-dotnet-v2), de .NET SDK v2- [prestatie tips](performance-tips.md)en de [hand leiding](troubleshoot-dot-net-sdk.md)voor het oplossen van problemen.
>

In dit artikel worden enkele van de overwegingen beschreven voor het upgraden van uw bestaande v1 .NET-toepassing naar Azure Cosmos DB .NET SDK v2 voor Core-API (SQL). Azure Cosmos DB .NET SDK v2 komt overeen met de `Microsoft.Azure.DocumentDB` naam ruimte. U kunt de informatie in dit document gebruiken als u uw toepassing migreert van een van de volgende Azure Cosmos DB .NET-platforms voor het gebruik van de v2 SDK `Microsoft.Azure.Cosmos` :

* Azure Cosmos DB .NET Framework v1 SDK voor SQL API
* Azure Cosmos DB .NET Core SDK v1 voor SQL-API

## <a name="whats-available-in-the-net-v2-sdk"></a>Wat is er beschikbaar in de .NET v2-SDK

De v2-SDK bevat een groot aantal verbeteringen op het gebied van bruikbaarheid en prestaties, waaronder:

* Ondersteuning voor de TCP direct-modus voor niet-Windows-clients
* Ondersteuning voor meerdere regio's schrijven
* Verbeteringen op het gebied van query prestaties
* Ondersteuning voor georuimtelijke/geometrie verzamelingen en indexering
* Verbeterde verbeteringen voor diagnostische gegevens over direct/TCP-Trans Port
* Updates op directe TCP-transport stack om het aantal ingestelde verbindingen te verminderen
* Verbeteringen in latentie reductie in het RequestTimeout

De meeste pogings logica en lagere niveaus van de SDK blijven grotendeels ongewijzigd.

## <a name="why-migrate-to-the-net-v2-sdk"></a>Waarom migreren naar de .NET v2-SDK

Naast de talrijke verbeteringen in de prestaties, worden nieuwe functie investeringen die zijn gemaakt in de nieuwste SDK, niet teruggezet naar oudere versies.

Daarnaast worden de oudere Sdk's vervangen door nieuwere versies en de V1 SDK gaat in de [onderhouds modus](sql-api-sdk-dotnet.md). Voor de beste ontwikkel ervaring raden we u aan uw toepassing te migreren naar een nieuwere versie van de SDK.

## <a name="major-changes-from-v1-sdk-to-v2-sdk"></a>Belang rijke wijzigingen van v1 SDK naar v2 SDK

### <a name="direct-mode--tcp"></a>Directe modus en TCP

De .NET v2 SDK ondersteunt nu zowel de modus direct als de gateway. De directe modus ondersteunt de connectiviteit via een TCP-protocol en biedt betere prestaties omdat deze rechtstreeks verbinding maakt met de back-end-replica's met minder netwerk-hops.

Lees voor meer informatie de [Azure Cosmos DB hand leiding voor de SQL SDK-connectiviteits modi](sql-sdk-connection-modes.md).

### <a name="session-token-formatting"></a>Sessie token opmaak

De v2 SDK maakt geen gebruik meer van de indeling van het *eenvoudige* sessie token zoals gebruikt in v1, maar in plaats daarvan gebruikt de SDK *Vector* -opmaak. De indeling moet worden geconverteerd wanneer aan de client toepassing met verschillende versies wordt door gegeven, omdat de indelingen niet uitwisselbaar zijn.

Zie [conversie van sessie token indelingen in de .NET SDK](how-to-convert-session-token.md)voor meer informatie.

### <a name="using-the-net-change-feed-processor-sdk"></a>De SDK van de .NET Change feed-processor gebruiken

De .NET Change feed-processor bibliotheek 2.1. x vereist `Microsoft.Azure.DocumentDB` 2,0 of hoger.

De 2.1. x-bibliotheek heeft de volgende belang rijke wijzigingen:

* Verbeteringen in stabiliteit en diagnose
* Verbeterde verwerking van fouten en uitzonde ringen
* Aanvullende ondersteuning voor gepartitioneerde lease verzamelingen
* Geavanceerde extensies voor het implementeren `ChangeFeedDocument` van de interface en klasse voor aanvullende fout afhandeling en tracering
* Er is ondersteuning toegevoegd voor het gebruik van een aangepast Archief voor het persistent maken van vervolg tokens per partitie

Zie de opmerkingen bij de [release](sql-api-sdk-dotnet-changefeed.md)van de processor bibliotheek wijzigen voor meer informatie.

### <a name="using-the-bulk-executor-library"></a>De bulk-uitvoerder bibliotheek gebruiken

De bibliotheek voor bulk-uitvoerder v2 heeft momenteel een afhankelijkheid van de Azure Cosmos DB .NET SDK 2.5.1 of hoger.

Zie voor meer informatie het overzicht van de [Azure Cosmos DB bulk](bulk-executor-overview.md) -uitvoerder en de opmerkingen bij de [release](sql-api-sdk-bulk-executor-dot-net.md)van de .net-bibliotheek voor bulksgewijs uit te voeren.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [aanvullende tips voor betere prestaties](sql-api-get-started.md) met behulp van Azure Cosmos DB voor SQL API versie 2 voor het optimaliseren van uw toepassing voor maximale prestaties
* Meer informatie over [wat u kunt doen met de v2-SDK](sql-api-dotnet-samples.md)