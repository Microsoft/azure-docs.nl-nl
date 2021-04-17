---
title: Aantal tekens - Translator
titleSuffix: Azure Cognitive Services
description: In dit artikel wordt uitgelegd hoe Azure Cognitive Services Translator tekens telt, zodat u begrijpt hoe inhoud wordt opgenomen.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 05/26/2020
ms.author: lajanuar
ms.openlocfilehash: 53fc22e1dbdac3240f72e8d64fbaee690597950f
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107373924"
---
# <a name="how-the-translator-counts-characters"></a>Hoe de Translator tekens telt

De Translator telt elk unicode-codepunt van invoertekst als een teken. Elke vertaling van een tekst naar een taal telt als een afzonderlijke vertaling, zelfs als de aanvraag is gedaan in één API-aanroep die vertaalt naar meerdere talen. De lengte van het antwoord is niet van belang.

Wat telt is:

* Tekst die wordt doorgegeven aan Translator in de body van de aanvraag
   * `Text` wanneer u de methoden Vertalen, Transliterate en Opzoek in woordenlijst gebruikt
   * `Text` en `Translation` bij gebruik van de methode Dictionary Examples
* Alle markeringen: HTML, XML-tags, enzovoort in het tekstveld van de aanvraagtekst. De JSON-notatie die wordt gebruikt om de aanvraag te maken (bijvoorbeeld 'Text:') wordt niet meegetelde.
* Een afzonderlijke letter
* Interpunctie
* Een spatie, tabblad, opmaak en elk soort witruimteteken
* Elk codepunt dat is gedefinieerd in Unicode
* Een herhaalde vertaling, zelfs als u eerder dezelfde tekst hebt vertaald

Voor scripts op basis van ideogrammen, zoals Chinees en Japans Kanji, telt de Translator-service nog steeds het aantal Unicode-codepunten, één teken per ideogram. Uitzondering: Unicode-surrogaatpersonen tellen als twee tekens.

Het aantal aanvragen, woorden, bytes of zinnen is niet relevant in het aantal tekens.

Aanroepen naar de methoden Detect en BreakSentence worden niet meegetelde in het tekenverbruik. We verwachten echter dat de aanroepen naar de methoden Detect en BreakSentence een redelijk aandeel hebben in het gebruik van andere functies die worden geteld. Als het aantal aanroepen van Detect of BreakSentence het aantal andere getelde methoden 100 keer overschrijdt, behoudt Microsoft zich het recht voor om uw gebruik van de methoden Detect en BreakSentence te beperken.

Elk teken dat wordt verzonden naar de functie translate wordt geteld, zelfs wanneer de inhoud niet is gewijzigd of wanneer de bron- en doeltaal hetzelfde zijn.

Meer informatie over het aantal tekens vindt u in De [veelgestelde vragen over Translator.](https://www.microsoft.com/en-us/translator/faq.aspx)
