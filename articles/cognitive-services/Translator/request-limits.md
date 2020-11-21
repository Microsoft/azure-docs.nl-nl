---
title: Aanvraag limieten-Translator
titleSuffix: Azure Cognitive Services
description: In dit artikel vindt u de aanvraag limieten voor het conversie programma. De kosten worden berekend op basis van het aantal tekens, niet de aanvraag frequentie met een limiet van 5.000 tekens per aanvraag. De teken limieten zijn gebaseerd op abonnementen, met F0 tot 2.000.000 tekens per uur.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 08/06/2020
ms.author: swmachan
ms.openlocfilehash: 8841c55d8f276f048db53a531bd2e9218e498b34
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/21/2020
ms.locfileid: "95016493"
---
# <a name="request-limits-for-translator"></a>Aanvraag limieten voor Translator

Dit artikel bevat beperkings limieten voor het conversie programma. Services omvatten omzetting, vele, detectie van de lengte van de zinnen, taal detectie en alternatieve vertalingen.

## <a name="character-and-array-limits-per-request"></a>Limieten voor tekens en matrices per aanvraag

Elke Vertaal aanvraag is beperkt tot 10.000 tekens, in alle doel talen waarnaar u vertaalt. Als u bijvoorbeeld een Vertaal aanvraag van 3.000 tekens verzendt om te vertalen naar drie verschillende talen, resulteert dit in een aanvraag grootte van 3000x3 = 9.000 tekens, die voldoet aan de aanvraag limiet. U betaalt per teken, niet op het aantal aanvragen. Het wordt aanbevolen om kortere aanvragen te verzenden.

De volgende tabel bevat een overzicht van matrix elementen en teken limieten voor elke bewerking van de vertaler.

| Bewerking | Maximale grootte van matrix element |    Maximum aantal matrix elementen |    Maximale aanvraag grootte (tekens) |
|:----|:----|:----|:----|
| Translator | 10.000    | 100   | 10.000 |
| Transcriberen | 5\.000 | 10    | 5\.000 |
| Detecteren | 50,000 | 100 |   50,000 |
| BreakSentence | 50,000    | 100 | 50,000 |
| Opzoeken in woordenlijst| 100 |  10  | 1000 |
| Voorbeelden in woordenlijst | 100 voor tekst en 100 voor vertaling (200 in totaal)| 10|   2.000 |

## <a name="character-limits-per-hour"></a>Maximum aantal tekens per uur

De teken limiet per uur is gebaseerd op uw Vertaal handelslaag. 

Het quotum per uur moet gelijkmatig over het hele uur worden verbruikt. Bijvoorbeeld: bij de limiet van F0 van 2.000.000 tekens per uur moeten tekens worden verbruikt die niet sneller zijn dan ongeveer 33.300 tekens per minuut sliding window (2.000.000 tekens gedeeld door 60 minuten).

Als u deze limieten bereikt of overschrijdt, of als u een deel van het quotum te groot verzendt, wordt er waarschijnlijk een out-of-quota reactie weer gegeven. Er zijn geen limieten voor gelijktijdige aanvragen.

| Laag | Teken limiet |
|------|-----------------|
| F0 | 2.000.000 tekens per uur |
| S1 | 40.000.000 tekens per uur |
| S2/C2 | 40.000.000 tekens per uur |
| S3/C3 | 120.000.000 tekens per uur |
| S4/C4 | 200.000.000 tekens per uur |

De limieten voor [meerdere service abonnementen](./reference/v3-0-reference.md#authentication) zijn gelijk aan die van de S1-laag.

Deze limieten zijn beperkt tot de standaard Vertaal modellen van micro soft. Aangepaste Vertaal modellen die gebruikmaken van aangepaste vertalers, zijn beperkt tot 1.800 tekens per seconde.

## <a name="latency"></a>Latentie

Het conversie programma heeft een maximale latentie van 15 seconden met standaard modellen en 120 seconden wanneer aangepaste modellen worden gebruikt. Normaal gesp roken worden antwoorden op *tekst binnen 100 tekens* geretourneerd in 150 milliseconden tot 300 milliseconden. De aangepaste Translator-modellen hebben vergelijk bare latentie kenmerken voor een continue aanvraag frequentie en kunnen een hogere latentie hebben wanneer uw aanvraag frequentie loopt. Reactie tijden variëren op basis van de grootte van de aanvraag en het taal paar. Als u binnen deze tijds Panne geen vertaling of een [fout](./reference/v3-0-reference.md#errors) bericht ontvangt, controleert u uw code, uw netwerk verbinding en probeer het opnieuw. 

## <a name="sentence-length-limits"></a>Maximale lengte van zin

Wanneer u de functie [BreakSentence](./reference/v3-0-break-sentence.md) gebruikt, is de lengte van de zin beperkt tot 275 tekens. Er zijn uitzonde ringen voor deze talen:

| Taal | Code | Teken limiet |
|----------|------|-----------------|
| Chinees | zh | 166 |
| Duits | de | 800 |
| Italiaans | it | 800 |
| Japans | ja | 166 |
| Portugees | pt | 800 |
| Spaans | es | 800 |
| Thai | th | 180 |

> [!NOTE]
> Deze limiet is niet van toepassing op vertalingen.

## <a name="next-steps"></a>Volgende stappen

* [Prijzen](https://azure.microsoft.com/pricing/details/cognitive-services/translator-text-api/)
* [Regionale Beschik baarheid](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services)
* [v3 Translator-naslaginformatie](./reference/v3-0-reference.md)