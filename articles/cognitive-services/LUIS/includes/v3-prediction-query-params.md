---
title: Para meters voor de V3 API-query teken reeks
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: include
ms.date: 06/30/2020
ms.openlocfilehash: 14380dd781fc1eebfe7edb0a816ff8af0f2f17b4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "91309422"
---
Para meters voor de V3 API-query reeks zijn:

|Queryparameter|Naam van LUIS-Portal|Type|Versie|Standaard|Doel|
|--|--|--|--|--|--|
|`log`|Logboeken opslaan|booleaans|V2 & V3|onjuist|Sla de query op in het logboek bestand. De standaard waarde is False.|
|`query`|-|tekenreeks|Alleen v3|Geen standaard-dit is vereist in de GET-aanvraag|**In v2** bevindt de utterance die moet worden voor speld in de `q` para meter. <br><br>**In v3** wordt de functionaliteit door gegeven in de `query` para meter.|
|`show-all-intents`|Scores voor alle intenties toevoegen|booleaans|Alleen v3|onjuist|Alle intenties retour neren met de bijbehorende Score in het object voor **spellingen** . Intenties worden geretourneerd als objecten in een bovenliggend `intents` object. Zo kunt u programmatisch toegang krijgen zonder dat u de bedoeling in een matrix hoeft te vinden: `prediction.intents.give` . In v2 zijn deze geretourneerd in een matrix. |
|`verbose`|Meer details van entiteiten toevoegen|booleaans|V2 & V3|onjuist|**In v2** zijn alle voorspelde intenten geretourneerd als deze zijn ingesteld op True. Als u alle voorspelde intenties nodig hebt, gebruikt u de V3-para meter van `show-all-intents` .<br><br>**In v3** biedt deze para meter alleen details van entiteits-meta gegevens van de voor spelling van de entiteit.  |
|`timezoneOffset`|-|tekenreeks|V2|-|De tijd zone die wordt toegepast op datetimeV2-entiteiten.|
|`datetimeReference`|-|tekenreeks|V3|-|De [tijd zone](../luis-concept-data-alteration.md#change-time-zone-of-prebuilt-datetimev2-entity) die wordt toegepast op datetimeV2-entiteiten. Vervangt door `timezoneOffset` v2.|