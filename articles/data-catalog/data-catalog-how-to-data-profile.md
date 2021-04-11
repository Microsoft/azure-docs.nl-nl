---
title: Gegevens bronnen voor data Profiler in Azure Data Catalog gebruiken
description: In dit artikel wordt uitgelegd hoe u gegevens profielen op tabel-en kolom niveau kunt opnemen bij het registreren van gegevens bronnen in Azure Data Catalog en hoe u gegevens profielen gebruikt om gegevens bronnen te begrijpen.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: how-to
ms.date: 08/01/2019
ms.openlocfilehash: 473ba69fa30542dfa41dfc5f19002ac36b56b482
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "104674951"
---
# <a name="how-to-data-profile-data-sources-in-azure-data-catalog"></a>Hoe gegevens bronnen voor gegevens profielen in Azure Data Catalog

[!INCLUDE [Azure Purview redirect](../../includes/data-catalog-use-purview.md)]

## <a name="introduction"></a>Introductie

**Microsoft Azure Data Catalog** is een volledig beheerde Cloud service die fungeert als registratie systeem en detectie systeem voor zakelijke gegevens bronnen. Met andere woorden, het **Azure Data Catalog** is alles wat helpt mensen bij het detecteren, begrijpen en gebruiken van gegevens bronnen, en helpt organisaties bij het verkrijgen van meer waarde dan hun bestaande gegevens. Wanneer een gegevens bron wordt geregistreerd bij **Azure Data Catalog**, worden de meta gegevens gekopieerd en geïndexeerd door de service, maar het artikel eindigt daar niet.

Met de functie **gegevens Profiler** van **Azure Data Catalog** worden de gegevens van ondersteunde gegevens bronnen in uw catalogus onderzocht en worden statistieken en informatie over die gegevens verzameld. Het is eenvoudig om een profiel van uw gegevensassets toe te voegen. Wanneer u een gegevens Asset registreert, kiest u **gegevens profiel opnemen** in het hulp programma voor registratie van gegevens bronnen.

## <a name="what-is-data-profiling"></a>Wat is gegevens profilering?

Gegevens Profiler onderzoekt de gegevens in de gegevens bron die worden geregistreerd en verzamelt statistieken en informatie over die gegevens. Tijdens de detectie van gegevens bronnen kan deze statistieken u helpen bij het bepalen van de geschiktheid van de gegevens om het bedrijfs probleem op te lossen.

<!-- In [How to discover data sources](data-catalog-how-to-discover.md), you learn about **Azure Data Catalog's** extensive search capabilities including searching for data assets that have a profile. See [How to include a data profile when registering a data source](#howto). -->

De volgende gegevens bronnen ondersteunen gegevens profilering:

* SQL Server (inclusief Azure SQL DB-en Azure Synapse Analytics)-tabellen en-weer gaven
* Oracle-tabellen en-weer gaven
* Teradata-tabellen en-weer gaven
* Hive-tabellen

Met inbegrip van gegevens profielen bij het registreren van gegevensassets helpt gebruikers bij het beantwoorden van vragen over gegevens bronnen, waaronder:

* Kan worden gebruikt om mijn bedrijfs probleem op te lossen?
* Voldoen de gegevens aan bepaalde normen of patronen?
* Wat zijn de afwijkingen van de gegevens bron?
* Wat zijn de mogelijke uitdagingen van het integreren van deze gegevens in mijn toepassing?

> [!NOTE]
> U kunt ook documentatie toevoegen aan een Asset om te beschrijven hoe gegevens in een toepassing kunnen worden geïntegreerd. Zie [gegevens bronnen documenteren](data-catalog-how-to-documentation.md).
>

<a name="howto"></a>

## <a name="how-to-include-a-data-profile-when-registering-a-data-source"></a>Een gegevens profiel opnemen bij het registreren van een gegevens bron

U kunt eenvoudig een profiel van uw gegevens bron toevoegen. Wanneer u een gegevens bron registreert, kiest u in het deel venster **te registreren objecten** van het hulp programma voor registratie van gegevens bronnen de optie **gegevens profiel opnemen**.

![Selectie vakje gegevens profiel insluiten](media/data-catalog-data-profile/data-catalog-register-profile.png)

Zie [gegevens bronnen registreren](data-catalog-how-to-register.md) en aan de [slag met Azure Data Catalog](data-catalog-get-started.md)voor meer informatie over het registreren van gegevens bronnen.

## <a name="filtering-on-data-assets-that-include-data-profiles"></a>Filteren op gegevens assets met gegevens profielen

Als u gegevensassets wilt detecteren die een gegevens profiel bevatten, kunt u `has:tableDataProfiles` `has:columnsDataProfiles` een van de zoek termen toevoegen.

> [!NOTE]
> Als u **gegevens profiel opnemen** selecteert in het hulp programma voor registratie van gegevens bronnen, worden profiel gegevens op tabel en kolom niveau weer gegeven. Met de Data Catalog-API kunnen gegevens assets echter worden geregistreerd met slechts één set profiel gegevens.
>

## <a name="viewing-data-profile-information"></a>Gegevens profiel gegevens weer geven

Wanneer u een geschikte gegevens bron met een profiel hebt gevonden, kunt u de details van het gegevens profiel bekijken. Als u het gegevens profiel wilt weer geven, selecteert u een gegevens activum en kiest u **gegevens profiel** in het venster Data Catalog-Portal.

![Tabblad Gegevens Profiel](media/data-catalog-data-profile/data-catalog-view.png)

Een gegevens profiel in **Azure Data Catalog** bevat tabel-en kolom profiel informatie, waaronder:

### <a name="object-data-profile"></a>Object gegevens profiel

* Aantal rijen
* Tabelgrootte
* Wanneer het object voor het laatst is bijgewerkt

### <a name="column-data-profile"></a>Kolom gegevens profiel

* Kolom gegevens type
* Aantal afzonderlijke waarden
* Aantal rijen met NULL-waarden
* Minimum, maximum, gemiddelde en standaard afwijking voor kolom waarden

## <a name="summary"></a>Samenvatting

Gegevens Profiler biedt statistieken en informatie over geregistreerde gegevensassets waarmee u de geschiktheid van de gegevens kunt bepalen om zakelijke problemen op te lossen. Naast het aantekeningen maken en gegevens bronnen documenteren, kunnen gegevens profielen gebruikers een dieper inzicht krijgen in uw gegevens.

## <a name="see-also"></a>Zie ook

* [Gegevensbronnen registreren](data-catalog-how-to-register.md)
* [Aan de slag met Azure Data Catalog](data-catalog-get-started.md)
