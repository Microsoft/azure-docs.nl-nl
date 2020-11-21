---
title: Preconstrueerde modellen-LUIS
titleSuffix: Azure Cognitive Services
description: Preconstrueerde modellen bieden domeinen, intenties, uitingen en entiteiten. U kunt uw app starten met een vooraf gebouwd domein of later een relevant domein aan uw app toevoegen.
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/10/2019
ms.openlocfilehash: 6642e59c2957b298d54bc587853752b9fce74686
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/21/2020
ms.locfileid: "95019140"
---
# <a name="prebuilt-models"></a>Vooraf gemaakte modellen

Preconstrueerde modellen bieden domeinen, intenties, uitingen en entiteiten. U kunt uw app starten met een vooraf gebouwd model of later een relevant model aan uw app toevoegen. 

## <a name="types-of-prebuilt-models"></a>Typen vooraf gebouwde modellen

LUIS biedt drie soorten vooraf ontwikkelde modellen. Elk model kan op elk gewenst moment aan uw app worden toegevoegd. 

|Model type|Includes|
|--|--|
|[Domein](luis-reference-prebuilt-domains.md)|Intenties, uitingen, entiteiten|
|Intents|Intenties, uitingen|
|[Entiteiten](luis-reference-prebuilt-entities.md)|Alleen entiteiten| 

## <a name="prebuilt-domains"></a>Vooraf gemaakte domeinen

Language Understanding (LUIS) biedt kant-en- *klare domeinen*. Dit zijn vooraf getrainde modellen van [intenties](luis-how-to-add-intents.md) en [entiteiten](luis-concept-entity-types.md) die samen werken voor domeinen of algemene categorieën van client toepassingen. 

De vooraf gemaakte domeinen zijn getraind en klaar om toe te voegen aan uw LUIS-app. De intenties en entiteiten van een vooraf gebouwd domein kunnen volledig worden aangepast nadat u ze aan uw app hebt toegevoegd. 

> [!TIP]
> De intenties en entiteiten in een vooraf gebouwd domein werken het beste samen. Het is beter om de intenties en entiteiten uit hetzelfde domein, indien mogelijk, te combi neren.
> Het vooraf ontwikkelde hulp programma voor Hulpprogram Ma's heeft intenties die u kunt aanpassen voor gebruik in elk domein. U kunt bijvoorbeeld toevoegen `Utilities.Repeat` aan uw app en traint dat alle acties die gebruikers mogelijk willen herhalen in uw toepassing worden herkend. 

### <a name="changing-the-behavior-of-a-prebuilt-domain-intent"></a>Het gedrag van een vooraf gebouwd domein opzet wijzigen

Het is mogelijk dat een vooraf gebouwd domein een intentie bevat die vergelijkbaar is met een intentie die u in uw LUIS-app wilt hebben, maar u wilt dat deze anders werkt. **Het voor** bereide domein biedt bijvoorbeeld een `MakeReservation` intentie voor het maken van een restaurant, maar u wilt dat uw app die intentie gebruikt om hotel reserveringen te maken. In dat geval kunt u het gedrag van die intentie wijzigen door bijvoorbeeld uitingen toe te voegen aan het doel om hotel reserveringen te maken en de app vervolgens opnieuw te trainen. 

U vindt een volledige lijst met vooraf gemaakte domeinen in de [Naslag Gids met vooraf gedefinieerde domeinen](./luis-reference-prebuilt-domains.md).

## <a name="prebuilt-intents"></a>Preconstrueerde intenties

LUIS biedt vooraf ontwikkelde intenten en hun uitingen voor elk van de vooraf gemaakte domeinen. Intenties kunnen worden toegevoegd zonder het hele domein toe te voegen. Het toevoegen van een intentie is het proces van het toevoegen van een intentie en de uitingen van uw app. Zowel de naam van de doel groep als de utterance-lijst kan worden gewijzigd.  

## <a name="prebuilt-entities"></a>Vooraf gemaakte entiteiten

LUIS bevat een reeks vooraf gemaakte entiteiten voor het herkennen van veelvoorkomende typen informatie, zoals datums, tijden, getallen, metingen en valuta. Vooraf ontwikkelde entiteits ondersteuning is afhankelijk van de cultuur van uw LUIS-app. Zie de [vooraf samengestelde entiteits referentie](./luis-reference-prebuilt-entities.md)voor een volledige lijst van de vooraf gemaakte entiteiten die door Luis worden ondersteund, inclusief ondersteuning door de cultuur.

Wanneer een vooraf samengestelde entiteit is opgenomen in uw toepassing, zijn de voor spellingen opgenomen in de gepubliceerde toepassing. Het gedrag van vooraf gemaakte entiteiten is vooropgeleid en **kan niet** worden gewijzigd. 

> [!NOTE]
> **Builtin. datetime** is afgeschaft. Het wordt vervangen door [**Builtin. datetimeV2**](luis-reference-prebuilt-datetimev2.md), waarmee u de datum-en tijds bereik herkenning kunt herkennen, evenals een verbeterde herkenning van onduidelijke datum en tijd.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het [toevoegen van vooraf gemaakte entiteiten](./howto-add-prebuilt-models.md) aan uw app.