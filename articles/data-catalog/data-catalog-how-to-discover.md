---
title: Gegevens bronnen in Azure Data Catalog ontdekken
description: In dit artikel wordt uitgelegd hoe u geregistreerde gegevensassets kunt detecteren met Azure Data Catalog, met inbegrip van zoeken en filteren en de mogelijkheden voor het markeren van treffers van de Azure Data Catalog Portal.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: how-to
ms.date: 08/01/2019
ms.openlocfilehash: 4eb689b17df8236a00b5914912d1927804944f25
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96009412"
---
# <a name="how-to-discover-data-sources-in-azure-data-catalog"></a>Gegevens bronnen in Azure Data Catalog ontdekken

## <a name="introduction"></a>Introductie

Azure Data Catalog is een volledig beheerde Cloud service die fungeert als een systeem van registratie en detectie voor zakelijke gegevens bronnen. Met andere woorden, Data Catalog helpt mensen bij het ontdekken, begrijpen en gebruiken van gegevens bronnen. Het helpt organisaties meer waarde te verkrijgen dan hun bestaande gegevens. Nadat een gegevens bron is geregistreerd met Data Catalog, worden de meta gegevens ervan geïndexeerd door de service, zodat u gemakkelijk kunt zoeken naar de gegevens die u nodig hebt.

## <a name="searching-and-filtering"></a>Zoeken en filteren

Detectie in Data Catalog gebruikt twee primaire mechanismen: zoeken en filteren.

Zoeken is zowel intuïtief als krachtig. Standaard worden de zoektermen vergeleken met een eigenschap in de catalogus, met inbegrip van door de gebruiker gemaakte aantekeningen.

Filteren is bedoeld als aanvulling op het zoeken. U kunt specifieke kenmerken selecteren, zoals deskundigen, gegevens bron type, object type en tags. U kunt alleen overeenkomende gegevensassets bekijken en de zoek resultaten beperken tot overeenkomende activa.

Door gebruik te maken van een combi natie van zoeken en filteren kunt u snel navigeren naar de gegevens bronnen die zijn geregistreerd bij Data Catalog om de gegevens bronnen te vinden die u nodig hebt.

## <a name="search-syntax"></a>Zoek syntaxis

Hoewel de standaard zoek functie voor vrije tekst eenvoudig en intuïtief is, kunt u ook Data Catalog Zoek syntaxis gebruiken voor meer controle over de zoek resultaten. Data Catalog zoeken ondersteunt de volgende technieken:

| Techniek | Gebruik | Voorbeeld |
| --- | --- | --- |
| Eenvoudige zoek opdracht |Basis zoekopdracht die gebruikmaakt van een of meer zoek termen. Resultaten zijn alle assets die overeenkomen met een eigenschap met een of meer van de opgegeven voor waarden. |`sales data` |
| Bereik van eigenschap |Alleen gegevens bronnen retour neren waarbij de zoek term overeenkomt met de opgegeven eigenschap. |`name:finance` |
| Booleaanse operatoren |Een zoek opdracht uitbreiden of beperken met behulp van Boole-bewerkingen. |`finance NOT corporate` |
| Groeperen met haakjes |Gebruik haakjes om delen van de query te groeperen voor logische isolatie, met name in combi natie met Booleaanse Opera tors. |`name:finance AND (tags:Q1 OR tags:Q2)` |
| Vergelijkingsoperatoren |Gebruik andere vergelijkingen dan gelijkheid voor eigenschappen die de gegevens typen numeriek en datum hebben. |`modifiedTime > "11/05/2014"` |

Zie het [Azure Data Catalog](/rest/api/datacatalog/#search-syntax-reference) artikel voor meer informatie over het zoeken naar Data Catalog.

## <a name="hit-highlighting"></a>Markeren

Wanneer u zoek resultaten bekijkt, worden alle weer gegeven eigenschappen die overeenkomen met de opgegeven zoek termen (zoals de gegevensassetnaam, beschrijving en Tags), gemarkeerd om het gemakkelijker te maken om te identificeren waarom een bepaalde gegevensasset door een bepaalde zoek opdracht is geretourneerd.

> [!NOTE]
> Als u het markeren van treffers wilt uitschakelen, gebruikt u de schakel optie **markeren** in de Data Catalog Portal.

Wanneer u de zoek resultaten bekijkt, is het mogelijk niet altijd duidelijk waarom een gegevensasset is opgenomen, zelfs als het markeren van treffers is ingeschakeld. Omdat alle eigenschappen standaard worden doorzocht, kan een gegevensasset worden geretourneerd vanwege een overeenkomst met een eigenschap op kolom niveau. En omdat meerdere gebruikers aantekeningen kunnen maken op geregistreerde gegevensassets met hun eigen Tags en beschrijvingen, worden niet alle meta gegevens weer gegeven in de lijst met zoek resultaten.

In de standaard tegel weergave bevat elke tegel die wordt weer gegeven in de zoek resultaten, het pictogram **Zoek term voor weer gave komt overeen** , zodat u snel het aantal overeenkomsten en de locatie ervan kunt weer geven, en naar de plaats wilt gaan.

 ![Treffers markeren en zoeken naar overeenkomsten in de Azure Data Catalog Portal](./media/data-catalog-how-to-discover/search-matches.png)

## <a name="summary"></a>Samenvatting

Omdat het registreren van een gegevens bron met Data Catalog structurele en beschrijvende meta gegevens van de gegevens bron naar de catalogus service kopieert, wordt de gegevens bron eenvoudiger te detecteren en te begrijpen. Nadat u een gegevens bron hebt geregistreerd, kunt u deze detecteren met behulp van filteren en zoeken in de Data Catalog Portal.

## <a name="next-steps"></a>Volgende stappen

* Zie [aan de slag met Azure Data Catalog](data-catalog-get-started.md)voor stapsgewijze informatie over het detecteren van gegevens bronnen.
