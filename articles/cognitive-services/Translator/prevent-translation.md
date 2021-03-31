---
title: Vertaling van inhoud voor komen-Translator
titleSuffix: Azure Cognitive Services
description: Omzetting van inhoud met het conversie programma voor komen. Met de vertaler kunt u inhoud labelen zodat deze niet wordt vertaald.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 05/26/2020
ms.author: lajanuar
ms.openlocfilehash: e89ff3f4c1f54d9e137d3dd51e325b908c826b03
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "98897473"
---
# <a name="how-to-prevent-translation-of-content-with-the-translator"></a>Omzetting van inhoud met de vertaler voor komen

Met de vertaler kunt u inhoud labelen zodat deze niet wordt vertaald. U wilt bijvoorbeeld een code, een merknaam of een woord/zin labelen die niet goed kan worden vertaald.

## <a name="methods-for-preventing-translation"></a>Methoden voor het voor komen van vertalingen

1. Voorzie uw inhoud van met `notranslate` . Het ontwerp dat dit alleen werkt als de invoer textType is ingesteld als HTML

   Voorbeeld:

   ```html
   <span class="notranslate">This will not be translated.</span>
   <span>This will be translated. </span>
   ```
   
   ```html
   <div class="notranslate">This will not be translated.</div>
   <div>This will be translated. </div>
   ```

2. Voorzie uw inhoud van met `translate="no"` . Dit werkt alleen wanneer de invoer textType is ingesteld als HTML

   Voorbeeld:

   ```html
   <span translate="no">This will not be translated.</span>
   <span>This will be translated. </span>
   ```
   
   ```html
   <div translate="no">This will not be translated.</div>
   <div>This will be translated. </div>
   ```
   
3. Gebruik de [dynamische woorden lijst](dynamic-dictionary.md) om een specifieke vertaling te bepalen.

4. Geef de teken reeks niet door aan de vertaler voor vertaling.

5. Aangepaste vertaler: gebruik een [woorden lijst in het aangepaste conversie programma](custom-translator/what-is-dictionary.md) om de vertaling van een woord groep met een waarschijnlijkheid van 100% te bepalen.


## <a name="next-steps"></a>Volgende stappen
> [!div class="nextstepaction"]
> [De Vertaal bewerking gebruiken om tekst te vertalen](reference/v3-0-translate.md)
