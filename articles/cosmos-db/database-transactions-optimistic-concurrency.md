---
title: Database transacties en optimistische gelijktijdigheids beheer in Azure Cosmos DB
description: In dit artikel worden database transacties en optimistische gelijktijdigheids beheer in Azure Cosmos DB beschreven
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 12/04/2019
ms.reviewer: sngun
ms.openlocfilehash: 96652b2a1eb35668bd8a810b309ab31cec5afdb7
ms.sourcegitcommit: 9514d24118135b6f753d8fc312f4b702a2957780
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/07/2021
ms.locfileid: "97967256"
---
# <a name="transactions-and-optimistic-concurrency-control"></a>Transacties en optimistisch beheer van gelijktijdigheid
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Database transacties bieden een veilig en voorspelbaar programmeer model voor het afhandelen van gelijktijdige wijzigingen in de gegevens. Met traditionele relationele data bases, zoals SQL Server, kunt u de bedrijfs logica schrijven met behulp van opgeslagen procedures en/of triggers, deze naar de server verzenden zodat deze rechtstreeks vanuit de data base-engine kan worden uitgevoerd. Met traditionele relationele data bases moet u twee verschillende programmeer talen behandelen: (niet-transactionele) programmeer taal van de toepassing, zoals Java script, Python, C#, Java, enzovoort en de transactionele programmeer taal (zoals T-SQL) die systeem eigen kan worden uitgevoerd door de data base.

De data base-engine in Azure Cosmos DB ondersteunt volwaardige trans acties met volledige zuren (atomiciteit, consistentie, isolatie, duurzaamheid) met snap shot-isolatie. Alle database bewerkingen binnen het bereik van de [logische partitie](partitioning-overview.md) van een container zijn transactioneel uitgevoerd in de data base-engine die wordt gehost door de replica van de partitie. Deze bewerkingen zijn onder andere schrijven (het bijwerken van een of meer items in de logische partitie) en lees bewerkingen. In de volgende tabel ziet u verschillende bewerkingen en transactie typen:

| **Bewerking**  | **Bewerkings type** | **Trans actie met één of meerdere items** |
|---------|---------|---------|
| Invoegen (zonder een pre/post-trigger) | Schrijven | Trans actie met één item |
| Invoegen (met een pre/post-trigger) | Schrijven en lezen | Trans actie met meerdere items |
| Vervangen (zonder een pre/post-trigger) | Schrijven | Trans actie met één item |
| Vervangen (met een pre/post-trigger) | Schrijven en lezen | Trans actie met meerdere items |
| Upsert (zonder een pre/post-trigger) | Schrijven | Trans actie met één item |
| Upsert (met een pre/post-trigger) | Schrijven en lezen | Trans actie met meerdere items |
| Verwijderen (zonder een pre/post-trigger) | Schrijven | Trans actie met één item |
| Verwijderen (met een pre/post-trigger) | Schrijven en lezen | Trans actie met meerdere items |
| Opgeslagen procedure uitvoeren | Schrijven en lezen | Trans actie met meerdere items |
| Het systeem heeft de uitvoering van een samenvoeg procedure gestart | Schrijven | Trans actie met meerdere items |
| Het systeem heeft de uitvoering van items die zijn verwijderd op basis van de verval datum (TTL) van een item gestart | Schrijven | Trans actie met meerdere items |
| Lezen | Lezen | Trans actie met één item |
| Feed wijzigen | Lezen | Trans actie met meerdere items |
| Gepagineerd lezen | Lezen | Trans actie met meerdere items |
| Gepagineerde query | Lezen | Trans actie met meerdere items |
| UDF uitvoeren als onderdeel van de gepagineerde query | Lezen | Trans actie met meerdere items |

## <a name="multi-item-transactions"></a>Trans acties met meerdere items

Met Azure Cosmos DB kunt u [opgeslagen procedures, vooraf/post triggers, door de gebruiker gedefinieerde functies (udf's)](stored-procedures-triggers-udfs.md) en procedures voor samen voegen schrijven in Java script. Azure Cosmos DB ondersteunt Java script-uitvoering in de data base-engine. U kunt opgeslagen procedures, vooraf/post triggers, door de gebruiker gedefinieerde functies (Udf's) en procedures voor het samen voegen van een container registreren en deze later transactioneel uitvoeren in de Azure Cosmos data base-engine. Het schrijven van toepassings logica in Java script maakt natuurlijke expressie van controle stroom, variabele scoping, toewijzing en integratie van primitieve uitzonderings verwerking in de database transacties rechtstreeks in de Java script-taal mogelijk.

De op Java script gebaseerde opgeslagen procedures, triggers, Udf's en samenvoeg procedures worden verpakt in een omgevings zure trans actie met momentopname isolatie voor alle items in de logische partitie. Als tijdens de uitvoering een uitzonde ring wordt gegenereerd door het Java script-programma, wordt de hele trans actie afgebroken en teruggezet. Het resulterende programmeer model is eenvoudig maar krachtig. Java script-ontwikkel aars krijgen een duurzaam programmeer model terwijl ze nog steeds hun vertrouwde taal constructies en bibliotheek primitieven gebruiken.

De mogelijkheid om Java script direct in de data base-engine uit te voeren, biedt prestaties en transactionele uitvoering van database bewerkingen voor de items van een container. Omdat de Azure Cosmos-data base-engine systeem eigen ondersteuning biedt voor JSON en Java script, is er bovendien geen sprake van een niet-belemmerde impedantie tussen het type systemen van een toepassing en de data base.

## <a name="optimistic-concurrency-control"></a>Optimistisch gelijktijdigheidsbeheer

Met optimistische gelijktijdigheids controle kunt u voor komen dat updates en verwijderingen verloren gaan. Gelijktijdige, conflicterende bewerkingen worden onderhevig aan de reguliere pessimistische vergren deling van de data base-engine die wordt gehost door de logische partitie die eigenaar is van het item. Wanneer twee gelijktijdige bewerkingen proberen om de nieuwste versie van een item binnen een logische partitie bij te werken, wordt een van de items gewonnen en mislukt de andere. Als echter een of twee bewerkingen die hetzelfde item gelijktijdig proberen te updaten een oudere waarde van het item hebben gelezen, is de data base niet bekend als de eerder gelezen waarde door ofwel een van beide of beide conflicten inderdaad de laatste waarde van het item waren. Gelukkig kan deze situatie worden gedetecteerd met het **optimistische Gelijktijdigheids beheer (OCC)** voordat de twee bewerkingen de transactie grens in de data base-engine invoeren. OCC beschermt uw gegevens tegen per ongeluk wijzigingen die door anderen zijn aangebracht. Ook wordt voor komen dat anderen uw eigen wijzigingen overschrijven.

De gelijktijdige updates van een item worden onderhevig aan de OCC door de Layer van het communicatie protocol van Azure Cosmos DB. Voor Azure Cosmos-accounts die zijn geconfigureerd voor **schrijf bewerkingen met één regio**, zorgt Azure Cosmos DB ervoor dat de client-side versie van het item dat u bijwerkt (of verwijdert) hetzelfde is als de versie van het item in de Azure Cosmos-container. Dit zorgt ervoor dat uw schrijf bewerkingen worden beschermd tegen het onbedoeld overschrijven van de schrijf bewerkingen door anderen en andersom. In een omgeving met meerdere gebruikers beschermt u met het optimistische gelijktijdigheids beheer dat u per ongeluk de verkeerde versie van een item verwijdert of bijwerkt. Als zodanig worden items beschermd tegen de problemen met de Infamous ' verloren update ' of ' verloren gegane verwijderingen '.

In een Azure Cosmos-account dat is geconfigureerd met **meerdere regio's schrijft**, kunnen gegevens onafhankelijk worden vastgelegd in secundaire regio's als ze `_etag` overeenkomen met die van de gegevens in de lokale regio. Zodra nieuwe gegevens lokaal zijn doorgevoerd in een secundaire regio, wordt deze vervolgens samengevoegd in de hub of in de primaire regio. Als het beleid voor conflict oplossing de nieuwe gegevens in de hub-regio samenvoegt, worden deze gegevens vervolgens globaal gerepliceerd met de nieuwe `_etag` . Als het beleid voor het oplossen van conflicten de nieuwe gegevens weigert, wordt de secundaire regio teruggezet naar de oorspronkelijke gegevens en `_etag` .

Elk item dat in een Azure Cosmos-container is opgeslagen, heeft een door het systeem gedefinieerde `_etag` eigenschap. De waarde van de `_etag` wordt automatisch gegenereerd en bijgewerkt door de server telkens wanneer het item wordt bijgewerkt. `_etag` kan worden gebruikt met de door de client geleverde `if-match` aanvraag header om de server toe te staan om te bepalen of een item voorwaardelijk kan worden bijgewerkt. De waarde van de `if-match` header komt overeen met de waarde van de `_etag` op de server, het item wordt vervolgens bijgewerkt. Als de waarde van de `if-match` aanvraag header niet meer actueel is, weigert de server de bewerking met een respons bericht ' HTTP 412-voor waarde voor fout '. De client kan het item vervolgens opnieuw ophalen voor het verkrijgen van de huidige versie van het item op de server of de versie van het item op de server vervangen door een eigen `_etag` waarde voor het item. Daarnaast `_etag` kan worden gebruikt met de `if-none-match` header om te bepalen of een resource opnieuw moet worden opgehaald.

De waarde van het item `_etag` wordt gewijzigd telkens wanneer het item wordt bijgewerkt. Voor vervanging van items `if-match` moet expliciet worden uitgedrukt als onderdeel van de aanvraag opties. Zie de voorbeeld code in [github](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ItemManagement/Program.cs#L676-L772)voor een voor beeld. `_etag` waarden worden impliciet gecontroleerd op alle schriftelijke items die door de opgeslagen procedure worden beïnvloed. Als er conflicten worden gedetecteerd, wordt de trans actie teruggedraaid door de opgeslagen procedure en wordt er een uitzonde ring gegenereerd. Met deze methode worden alle of geen schrijf bewerkingen binnen de opgeslagen procedure op een Atomic toegepast. Dit is een signaal voor de toepassing om updates opnieuw toe te passen en de oorspronkelijke client aanvraag opnieuw uit te voeren.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over database transacties en optimistisch gelijktijdigheids beheer vindt u in de volgende artikelen:

- [Werken met Azure Cosmos-data bases,-containers en-items](account-databases-containers-items.md)
- [Consistentieniveaus](consistency-levels.md)
- [Conflicttypen en oplossingsbeleid](conflict-resolution-policies.md)
- [TransactionalBatch gebruiken](transactional-batch.md)
- [Opgeslagen procedures, triggers en door de gebruiker gedefinieerde functies](stored-procedures-triggers-udfs.md)
