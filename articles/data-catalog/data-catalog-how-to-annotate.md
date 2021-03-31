---
title: Aantekeningen toevoegen aan gegevens bronnen in Azure Data Catalog
description: Een artikel met instructies voor het markeren van aantekeningen aan gegevensassets in Azure Data Catalog, inclusief beschrijvende namen, tags, beschrijvingen en experts.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: how-to
ms.date: 08/01/2019
ms.openlocfilehash: 89fa1b542c54fc962f8c12a2142884c677afb259
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "104674645"
---
# <a name="how-to-annotate-data-sources-in-azure-data-catalog"></a>Aantekeningen toevoegen aan gegevens bronnen in Azure Data Catalog

[!INCLUDE [Azure Purview redirect](../../includes/data-catalog-use-purview.md)]

## <a name="introduction"></a>Introductie

**Microsoft Azure Data Catalog** is een volledig beheerde Cloud service die fungeert als registratie systeem en detectie systeem voor zakelijke gegevens bronnen. Met andere woorden, het Data Catalog is alles wat helpt mensen bij het detecteren, begrijpen en gebruiken van gegevens bronnen, en helpt organisaties bij het verkrijgen van meer waarde dan hun bestaande gegevens. Wanneer een gegevens bron wordt geregistreerd bij Data Catalog, worden de meta gegevens gekopieerd en geïndexeerd door de service, maar het artikel eindigt daar niet. Met Data Catalog kunnen gebruikers hun eigen beschrijvende meta gegevens, zoals beschrijvingen en tags, opgeven om de meta gegevens die zijn geëxtraheerd uit de gegevens bron op te vullen en de gegevens bron beter te begrijpen voor meer mensen.

## <a name="annotation-and-crowdsourcing"></a>Aantekening en crowdsourcing
Iedereen heeft een mening. En dit is goed.
Data Catalog erkent dat verschillende gebruikers verschillende perspectieven hebben voor gegevens bronnen in de onderneming en dat elk van deze perspectieven waardevol kan zijn. Denkt u zich het volgende scenario eens in:

* De systeem beheerder kent de service level agreement voor de servers of services die de gegevens bron hosten.
* De database beheerder kent het back-upschema voor elke Data Base en de toegestane vensters van de ETL-verwerking.
* De systeem eigenaar weet het proces voor gebruikers om toegang tot de gegevens bron aan te vragen.
* De data steward weet hoe de assets en kenmerken in de gegevens bron worden toegewezen aan het ondernemings gegevens model.
* De analist weet hoe de gegevens worden gebruikt in de context van de bedrijfs processen die ze ondersteunen.

Elk van deze perspectieven is waardevol en Data Catalog maakt gebruik van een crowdsourcing-benadering van meta gegevens waarmee elk van deze mogelijkheden kan worden vastgelegd en wordt gebruikt om een volledige afbeelding van geregistreerde gegevens bronnen te leveren. Met behulp van de Data Catalog Portal kan elke gebruiker hun eigen aantekeningen toevoegen en bewerken, terwijl de aantekeningen van andere gebruikers kunnen worden weer gegeven.

## <a name="different-types-of-annotations"></a>Verschillende typen aantekeningen
Data Catalog ondersteunt de volgende typen aantekeningen:

| Aantekening | Notities |
| --- | --- |
| Beschrijvende naam |Er kunnen beschrijvende namen worden opgegeven op het niveau van de gegevens Asset, zodat de gegevens activa eenvoudiger kunnen worden begrepen. Beschrijvende namen zijn het handigst wanneer de onderliggende object naam cryptisch, afgekort of anderszins niet relevant is voor gebruikers. |
| Description |Beschrijvingen kunnen worden opgegeven op het niveau van de gegevens Asset en het kenmerk/kolom. Beschrijvingen zijn vrije tekst aantekeningen met een korte vorm die het perspectief van de gebruiker voor de gegevens Asset of het gebruik beschrijven. |
| Tags (gebruikers codes) |Labels kunnen worden opgegeven op het niveau van de gegevens Asset en het kenmerk/kolom. Gebruikers codes zijn door de gebruiker gedefinieerde labels die kunnen worden gebruikt om gegevensassets of kenmerken te categoriseren. |
| Tags (woordenlijst Tags) |Labels kunnen worden opgegeven op het niveau van de gegevens Asset en het kenmerk/kolom. Woordenlijst Tags zijn centraal gedefinieerde woorden lijst termen die kunnen worden gebruikt om gegevensassets of kenmerken te categoriseren met behulp van een algemene bedrijfs taxonomie. Zie voor meer informatie  [de zakelijke woorden lijst instellen voor het bepalen van geregeld labelen](data-catalog-how-to-business-glossary.md) |
| Degenen |Experts kunnen worden verstrekt op het niveau van de gegevens Asset. Experts identificeren gebruikers of groepen met deskundig perspectieven voor de gegevens en kunnen dienen als contact punten voor gebruikers die de geregistreerde gegevens bronnen detecteren en vragen hebben die niet door de bestaande aantekeningen worden beantwoord. |
| Toegang aanvragen |Informatie over de aanvraag toegang kan worden opgegeven op het niveau van de gegevens Asset. Deze informatie is voor gebruikers die een gegevens bron detecteren waarvoor ze nog geen toegangs rechten hebben. Gebruikers kunnen het e-mail adres invoeren van de gebruiker of groep die toegang verleent, de URL van het proces of hulp programma die gebruikers nodig hebben om toegang te krijgen of het proces zelf als tekst in te voeren. |
| Documentatie |Documentatie kan worden verstrekt op het niveau van de gegevens Asset. Asset-documentatie is informatie over RTF-tekst die koppelingen en installatie kopieën kan bevatten en die informatie kan geven die niet wordt overgebracht via beschrijvingen en tags. |

## <a name="annotating-multiple-assets"></a>Aantekeningen maken op meerdere assets
Wanneer u meerdere gegevensassets in de Data Catalog Portal selecteert, kunnen gebruikers in één bewerking aantekeningen toevoegen aan alle geselecteerde assets. Aantekeningen zijn van toepassing op alle geselecteerde assets, waardoor het eenvoudig is om een consistente beschrijving en sets van tags en experts voor gerelateerde gegevensassets te selecteren en op te geven.

> [!NOTE]
> Tags en experts kunnen ook worden gegeven bij het registreren van gegevensassets met behulp van het registratie hulpprogramma voor Data Catalog gegevens bron.
>
>

Wanneer u meerdere tabellen en weer gaven selecteert, worden alleen de kolommen die alle geselecteerde gegevensassets gemeen hebben, weer gegeven in de Data Catalog Portal. Hiermee kunnen gebruikers Tags en beschrijvingen opgeven voor alle kolommen met dezelfde naam voor alle geselecteerde assets.

## <a name="annotations-and-discovery"></a>Aantekeningen en detectie
Net zoals de meta gegevens die zijn geëxtraheerd uit de gegevens bron tijdens de registratie, worden toegevoegd aan de Data Catalog zoek index, worden door de gebruiker opgegeven meta gegevens ook geïndexeerd. Dit betekent dat gebruikers niet alleen aantekeningen kunnen maken op de gegevens die ze hebben gedetecteerd, maar maakt het gemakkelijker voor gebruikers om de geannoteerde gegevensgestuurde gegevensassets te detecteren door te zoeken met de termen die voor hen van belang zijn.

## <a name="summary"></a>Samenvatting
Als u een gegevens bron registreert met Data Catalog, kunnen de gegevens worden gedetecteerd door structurele en beschrijvende meta gegevens te kopiëren van de gegevens bron naar de catalogus service. Nadat een gegevens bron is geregistreerd, kunnen gebruikers aantekeningen opgeven zodat ze gemakkelijker te ontdekken en inzicht hebben in de Data Catalog Portal.

## <a name="see-also"></a>Zie ook
* [Aan de slag met Azure Data Catalog](data-catalog-get-started.md) -zelf studie voor stapsgewijze Details over het aantekeningen maken van gegevens bronnen.
