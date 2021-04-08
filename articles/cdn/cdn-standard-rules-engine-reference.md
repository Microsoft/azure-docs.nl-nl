---
title: Naslag informatie voor de standaard regels voor Azure CDN | Microsoft Docs
description: Referentie documentatie voor voor waarden en acties in de Standard Rules engine voor Azure Content Delivery Network (Azure CDN).
services: cdn
author: asudbring
ms.service: azure-cdn
ms.topic: article
ms.date: 08/04/2020
ms.author: allensu
ms.openlocfilehash: 1a0f4456f38939632026645500dd48acbf7dbc88
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "93242205"
---
# <a name="standard-rules-engine-reference-for-azure-cdn"></a>Naslaginformatie over standaardregelengine voor Azure CDN

In de [standaard regels-engine](cdn-standard-rules-engine.md) voor Azure Content Delivery Network (Azure CDN) bestaat een regel uit een of meer match-voor waarden en een actie. In dit artikel vindt u gedetailleerde beschrijvingen van de voor waarden en functies die beschikbaar zijn in de standaard regels-engine voor Azure CDN.

De regel engine is ontworpen als de definitieve instantie van hoe specifieke typen aanvragen worden verwerkt door de standaard Azure CDN.

**Veelvoorkomende toepassingen voor de regels**:

- Een aangepast cache beleid overschrijft of definieert.
- Aanvragen omleiden.
- Wijzig HTTP-aanvraag-en antwoord headers.

## <a name="terminology"></a>Terminologie

Als u een regel in de regel Engine wilt definiëren, stelt u de voor waarden en [acties](cdn-standard-rules-engine-actions.md)voor [afstemmen](cdn-standard-rules-engine-match-conditions.md) in:

 ![Structuur van Azure CDN-regels](./media/cdn-standard-rules-engine-reference/cdn-rules-structure.png)

Elke regel kan Maxi maal tien match voorwaarden en vijf acties hebben. Elk Azure CDN-eind punt kan Maxi maal 25 regels bevatten. 

Inbegrepen in deze limiet is een standaard *globale regel*. De algemene regel heeft geen overeenkomende voor waarden; acties die in een globale regel zijn gedefinieerd, worden altijd geactiveerd.

   > [!IMPORTANT]
   > De volg orde waarin meerdere regels worden weer gegeven, is van invloed op hoe regels worden verwerkt. De acties die zijn opgegeven in een regel, kunnen worden overschreven door een volgende regel.

## <a name="limits-and-pricing"></a>Limieten en prijzen 

Elk Azure CDN-eind punt kan Maxi maal 25 regels bevatten. Elke regel kan Maxi maal tien match voorwaarden en vijf acties hebben. De prijs van de engine voor regels volgt de onderstaande dimensies: 
- Regels: $1 per regel per maand 
- Verwerkte aanvragen: $0,60 per miljoen aanvragen
- De eerste 5 regels blijven gratis

## <a name="syntax"></a>Syntax

Hoe speciale tekens worden behandeld in een regel, hangt af van de manier waarop de verschillende match voorwaarden en acties tekst waarden afhandelen. Een match-voor waarde of actie kan tekst op een van de volgende manieren interpreteren:

- [Letterlijke waarden](#literal-values)
- [Joker teken waarden](#wildcard-values)


### <a name="literal-values"></a>Letterlijke waarden

Tekst die wordt geïnterpreteerd als een letterlijke waarde, behandelt alle speciale tekens *, met uitzonde ring van het symbool%* als onderdeel van de waarde die moet worden vergeleken in een regel. Bijvoorbeeld: een letterlijke match-voor waarde die is ingesteld op `'*'` is alleen vervuld als de exacte waarde `'*'` wordt gevonden.

Een procent teken wordt gebruikt om URL-code ring aan te geven (bijvoorbeeld `%20` ).

### <a name="wildcard-values"></a>Joker teken waarden

Momenteel wordt het Joker teken ondersteund in de **UrlPath match-voor waarde** in de standaard regels-engine. Het \* teken is een Joker teken dat bestaat uit een of meer tekens. 

## <a name="next-steps"></a>Volgende stappen

- [Voldoen aan de voor waarden in de standaard regels-engine](cdn-standard-rules-engine-match-conditions.md)
- [Acties in de standaard regels-engine](cdn-standard-rules-engine-actions.md)
- [HTTPS afdwingen met de standaardregelengine](cdn-standard-rules-engine.md)
- [Overzicht van Azure CDN](cdn-overview.md)
