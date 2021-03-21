---
title: Tekst verschuivingen in de Text Analytics-API
titleSuffix: Azure Cognitive Services
description: Meer informatie over verschuivingen die worden veroorzaakt door meertalige en Emoji-code ringen.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: article
ms.date: 03/09/2020
ms.author: aahi
ms.reviewer: jdesousa
ms.openlocfilehash: f5b63503792b13e089568004ba67e5be8a3d0c7f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98932367"
---
# <a name="text-offsets-in-the-text-analytics-api-output"></a>Tekst verschuivingen in de Text Analytics-API uitvoer

Meertalige en Emoji-ondersteuning heeft geleid tot Unicode-code ringen die meer dan één [code punt](https://wikipedia.org/wiki/Code_point) gebruiken om één weer gegeven teken te vertegenwoordigen, een grapheme genoemd. Bijvoorbeeld, emojis als 🌷 en 👍 kunnen meerdere tekens gebruiken om de vorm samen te stellen met extra tekens voor visuele kenmerken, zoals de weergave kleur. Op dezelfde manier wordt het Hindi woord `अनुच्छेद` als vijf letters gecodeerd en worden drie tekens gecombineerd.

Vanwege de verschillende lengtes van mogelijke meertalige en Emoji-code ringen, kan de Text Analytics-API retour neren in het antwoord.

## <a name="offsets-in-the-api-response"></a>Verschuivingen in de API-reactie. 

Houd rekening met het volgende wanneer verschuivingen het API-antwoord retourneert, zoals [named entity Recognition](../how-tos/text-analytics-how-to-entity-linking.md) of [sentimentanalyse](../how-tos/text-analytics-how-to-sentiment-analysis.md):

* Elementen in het antwoord zijn mogelijk specifiek voor het eind punt dat is aangeroepen. 
* HTTP POST/GET-nettoladingen worden gecodeerd in [UTF-8](https://www.w3schools.com/charsets/ref_html_utf8.asp), die al dan niet de standaard teken codering voor het compileren van de client of het besturings systeem kan zijn.
* Verschuivingen verwijzen naar grapheme counts op basis van de [Unicode 8.0.0](https://unicode.org/versions/Unicode8.0.0) -standaard, niet de teken aantallen.

## <a name="extracting-substrings-from-text-with-offsets"></a>Subtekenreeksen ophalen uit tekst met verschuivingen

Verschuivingen kunnen problemen veroorzaken bij het gebruik van methoden op basis van teken reeksen, bijvoorbeeld de methode .NET [subtekenreeks ()](/dotnet/api/system.string.substring) . Eén probleem is dat een offset kan ertoe leiden dat een subtekenreeks-methode eindigt in het midden van een grapheme-code ring met meerdere tekens in plaats van het einde.

In .NET kunt u de [StringInfo](/dotnet/api/system.globalization.stringinfo) -klasse gebruiken, waarmee u kunt werken met een teken reeks als een reeks tekstuele elementen, in plaats van afzonderlijke teken objecten. U kunt ook zoeken naar grapheme splitter-bibliotheken in uw favoriete software omgeving. 

De Text Analytics-API retourneert ook deze tekstuele elementen, voor het gemak.

## <a name="offsets-in-api-version-31-preview"></a>Verschuivingen in API-versie 3,1-Preview

Vanaf API-versie 3,1-Preview. 1 worden alle Text Analytics-API eind punten die een offset retour neren, ondersteuning voor de `stringIndexType` para meter. Met deze para meter `offset` worden de en- `length` kenmerken in de API-uitvoer aangepast zodat deze overeenkomen met het aangevraagde teken reeks iteratie schema. Op dit moment ondersteunen we drie typen:

1. `textElement_v8` (standaard): herhaalt de graphemes zoals gedefinieerd door de [Unicode 8.0.0](https://unicode.org/versions/Unicode8.0.0) -standaard
2. `unicodeCodePoint`: herhaalt meer dan [Unicode-code punten](http://www.unicode.org/versions/Unicode13.0.0/ch02.pdf#G25564), het standaard schema voor python 3
3. `utf16CodeUnit`: herhaalt over [UTF-16-code-eenheden](https://unicode.org/faq/utf_bom.html#UTF16), het standaard schema voor Java script, Java en .net

Als de `stringIndexType` aanvraag overeenkomt met de programmeer omgeving van Choice, kan subtekenreeks worden geëxtraheerd met behulp van standaard subtekenreeks of segment methoden. 

## <a name="see-also"></a>Zie ook

* [Overzicht van Text Analytics](../overview.md)
* [Sentimentanalyse](../how-tos/text-analytics-how-to-sentiment-analysis.md)
* [Herkenning van entiteiten](../how-tos/text-analytics-how-to-entity-linking.md)
* [Taal detecteren](../how-tos/text-analytics-how-to-keyword-extraction.md)
* [Taalherkenning](../how-tos/text-analytics-how-to-language-detection.md)
