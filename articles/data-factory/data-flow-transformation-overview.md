---
title: Overzicht van gegevens stroom transformatie toewijzen
description: Een overzicht van de verschillende trans formaties die beschikbaar zijn in de toewijzing van gegevens stroom
author: dcstwh
ms.author: weetok
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/27/2020
ms.openlocfilehash: bb5021c0125c3140ef44a1ec3304b9d0ac40c30f
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/31/2021
ms.locfileid: "106060224"
---
# <a name="mapping-data-flow-transformation-overview"></a>Overzicht van gegevens stroom transformatie toewijzen

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)] 

Hieronder ziet u een lijst van de trans formaties die momenteel worden ondersteund in de toewijzing van gegevens stroom. Klik op elke trans formaties voor meer informatie over de configuratie gegevens.

| Name | Categorie | Beschrijving |
| ---- | -------- | ----------- |
| [Samenvoegen](data-flow-aggregate.md) | Schema wijziging | Definieer verschillende typen aggregaties, zoals som, MIN, maximum en aantal, gegroepeerd op bestaande of berekende kolommen. | 
| [Rij wijzigen](data-flow-alter-row.md) | Rij-aanpassing | Stel insert-, Delete-, update-en upsert-beleid in op rijen. |
| [Voorwaardelijk splitsen](data-flow-conditional-split.md) | Meerdere invoer/uitvoer | Route rijen met gegevens naar verschillende stromen op basis van overeenkomende voor waarden. |
| [Afgeleide kolom](data-flow-derived-column.md) | Schema wijziging | nieuwe kolommen genereren of bestaande velden wijzigen met behulp van de data flow-expressie taal. | 
| [Exists](data-flow-exists.md) | Meerdere invoer/uitvoer | Controleer of uw gegevens zich in een andere bron of stream bevindt. | 
| [Filter](data-flow-filter.md) | Rij-aanpassing | Een rij filteren op basis van een voor waarde. |
| [Platmaken](data-flow-flatten.md) | Schema wijziging |  Haal matrix waarden op in hiërarchische structuren zoals JSON en maak ze ongedaan in afzonderlijke rijen. |
| [Join](data-flow-join.md) | Meerdere invoer/uitvoer |  Gegevens uit twee bronnen of streams combi neren. |
| [Unmp](data-flow-lookup.md) | Meerdere invoer/uitvoer | Referentie gegevens uit een andere bron. |
| [Nieuwe vertakking](data-flow-new-branch.md) | Meerdere invoer/uitvoer | Meerdere sets bewerkingen en trans formaties Toep assen op dezelfde gegevens stroom. |
| [Parse](data-flow-new-branch.md) | Formatter | Tekst kolommen in uw gegevens stroom parseren die teken reeksen zijn van JSON, tekst met scheidings tekens of tekst met XML-opmaak. |
| [Draaitabel](data-flow-pivot.md) | Schema wijziging | Een aggregatie waarbij een of meer groeperings kolommen de afzonderlijke rijwaarden in afzonderlijke kolommen hebben getransformeerd. |
| [Positie](data-flow-rank.md) | Schema wijziging | Een geordende classificatie genereren op basis van de sorteer voorwaarden |
| [Selecteren](data-flow-select.md) | Schema wijziging | Alias kolommen en stroom namen en kolommen verwijderen of opnieuw rangschikken |
| [Sink](data-flow-sink.md) | - | Een eind bestemming voor uw gegevens |
| [Acties](data-flow-sort.md) | Rij-aanpassing | Binnenkomende rijen op de huidige gegevens stroom sorteren |
| [Bron](data-flow-source.md) | - | Een gegevens bron voor de gegevens stroom |
| [Surrogaatsleutel](data-flow-surrogate-key.md) | Schema wijziging | Een wille keurige sleutel waarde voor niet-zakelijk nummer toevoegen |
| [Union](data-flow-union.md) | Meerdere invoer/uitvoer | Meerdere gegevens stromen verticaal combi neren |
| [Draaitabel opheffen](data-flow-unpivot.md) | Schema wijziging | Kolommen in rijwaarden draaien |
| [Venster](data-flow-window.md) | Schema wijziging |  Definieer op venster gebaseerde aggregaties van kolommen in uw gegevens stromen. |
| [Parse](data-flow-parse.md) | Schema wijziging |  Kolom gegevens parseren naar JSON of tekst met scheidings tekens |
