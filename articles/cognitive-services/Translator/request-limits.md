---
title: Limieten aanvragen - Translator
titleSuffix: Azure Cognitive Services
description: In dit artikel vindt u een lijst met aanvraaglimieten voor Translator. Kosten worden gemaakt op basis van het aantal tekens, niet de aanvraagfrequentie met een limiet van 5000 tekens per aanvraag. Tekenlimieten zijn gebaseerd op een abonnement, met F0 beperkt tot 2 miljoen tekens per uur.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 04/19/2021
ms.author: lajanuar
ms.openlocfilehash: b5beb222ec20b1e7941f9438c0aacf98879a567a
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107727938"
---
# <a name="request-limits-for-translator"></a>Aanvraaglimieten voor Translator

Dit artikel bevat beperkingslimieten voor translator-vertaling, transliteratie, zinlengtedetectie, taaldetectie en alternatieve vertalingen.

## <a name="character-and-array-limits-per-request"></a>Teken- en matrixlimieten per aanvraag

Elke vertaalaanvraag is beperkt tot 10.000 tekens in alle doeltalen waar u naar vertaalt. Het verzenden van een vertaalaanvraag van 3000 tekens om te vertalen naar drie verschillende talen resulteert bijvoorbeeld in een aanvraaggrootte van 3000x3 = 9000 tekens, wat voldoet aan de aanvraaglimiet. Er worden kosten in rekening gebracht per teken, niet per aantal aanvragen. Het is raadzaam om kortere aanvragen te verzenden.

De volgende tabel bevat matrixelement- en tekenlimieten voor elke bewerking van Translator.

| Bewerking | Maximale grootte van matrixelement |    Maximum aantal matrixelementen |    Maximale aanvraaggrootte (tekens) |
|:----|:----|:----|:----|
| Translator | 10.000| 100| 10.000 |
| Transcriberen | 5\.000| 10| 5\.000 |
| Detecteren | 50,000 |100 |50,000 |
| BreakSentence | 50,000| 100 |50,000 |
| Opzoeken in woordenlijst| 100 |10| 1000 |
| Voorbeelden in woordenlijst | 100 voor tekst en 100 voor vertaling (totaal 200)| 10|2.000 |

## <a name="character-limits-per-hour"></a>Tekenlimieten per uur

Uw tekenlimiet per uur is gebaseerd op uw Translator-abonnementslaag.

Het quotum per uur moet gelijkmatig gedurende het hele uur worden verbruikt. Bij de limiet van de F0-laag van 2 miljoen tekens per uur mogen tekens bijvoorbeeld niet sneller worden gebruikt dan ongeveer 33.300 tekens per minuut sliding window (2 miljoen tekens gedeeld door 60 minuten).

Als u deze limieten bereikt of overschrijdt, of als u in korte tijd een te groot deel van het quotum verzendt, ontvangt u waarschijnlijk een antwoord dat het quotum te hoog is. Er zijn geen limieten voor gelijktijdige aanvragen.

| Laag | Tekenlimiet |
|------|-----------------|
| F0 | 2 miljoen tekens per uur |
| S1 | 40 miljoen tekens per uur |
| S2 /C2 | 40 miljoen tekens per uur |
| S3 / C3 | 120 miljoen tekens per uur |
| S4 /C4 | 200 miljoen tekens per uur |

Limieten [voor abonnementen met meerdere service](./reference/v3-0-reference.md#authentication) zijn hetzelfde als de S1-laag.

Deze limieten zijn beperkt tot de standaard vertaalmodellen van Microsoft. Aangepaste vertaalmodellen die gebruikmaken Custom Translator zijn beperkt tot 1800 tekens per seconde per model.

## <a name="latency"></a>Latentie

Translator heeft een maximale latentie van 15 seconden met standaardmodellen en 120 seconden bij het gebruik van aangepaste modellen. Normaal gesproken worden antwoorden voor tekst binnen *100 tekens* geretourneerd in 150 milliseconden tot 300 milliseconden. De custom translator-modellen hebben vergelijkbare latentiekenmerken op basis van een langdurige aanvraagsnelheid en hebben mogelijk een hogere latentie wanneer uw aanvraagfrequentie af en toe is. De reactietijden variëren op basis van de grootte van de aanvraag en het taalpaar. Als u binnen dat tijdsbestek [](./reference/v3-0-reference.md#errors) geen vertaling of foutbericht ontvangt, controleert u uw code, uw netwerkverbinding en doet u het opnieuw.

## <a name="sentence-length-limits"></a>Limieten voor zinlengte

Wanneer u de [functie BreakSentence](./reference/v3-0-break-sentence.md) gebruikt, is de zinlengte beperkt tot 275 tekens. Er zijn uitzonderingen voor deze talen:

| Taal | Code | Tekenlimiet |
|----------|------|-----------------|
| Chinees | zh | 166 |
| Duits | de | 800 |
| Italiaans | it | 800 |
| Japans | ja | 166 |
| Portugees | pt | 800 |
| Spaans | es | 800 |
| Thai | th | 180 |

> [!NOTE]
> Deze limiet geldt niet voor vertalingen.

## <a name="next-steps"></a>Volgende stappen

* [Prijzen](https://azure.microsoft.com/pricing/details/cognitive-services/translator-text-api/)
* [Regionale beschikbaarheid](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services)
* [v3 Translator-naslaginformatie](./reference/v3-0-reference.md)