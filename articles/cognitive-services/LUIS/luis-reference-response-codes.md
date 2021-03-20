---
title: API HTTP-antwoord codes-LUIS
titleSuffix: Azure Cognitive Services
description: Meer informatie over de HTTP-respons codes die worden geretourneerd door de LUIS-ontwerpen en-eindpunt-Api's
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 03/04/2019
ms.openlocfilehash: 46a7fff86d3a8c4539b77a0f271179d68e7a26d2
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91541676"
---
# <a name="common-api-response-codes-and-their-meaning"></a>Veelvoorkomende API-respons codes en hun betekenis

De [ontwerpen](https://go.microsoft.com/fwlink/?linkid=2092087) en [endpoint](https://go.microsoft.com/fwlink/?linkid=2092356) api's retour neren http-antwoord codes. Hoewel antwoord berichten informatie bevatten die specifiek is voor een aanvraag, is de status code van het HTTP-antwoord algemeen.

## <a name="common-status-codes"></a>Algemene status codes
De volgende tabel bevat een aantal van de meest voorkomende status codes voor HTTP-antwoorden voor de api's voor [ontwerpen](https://go.microsoft.com/fwlink/?linkid=2092087) en [eind punten](https://go.microsoft.com/fwlink/?linkid=2092356) :

|Code|API|Uitleg|
|:--|--|--|
|400|Ontwerpen, eind punt|de para meters van de aanvraag zijn onjuist, wat betekent dat de vereiste para meters ontbreken, ongeldig of te groot zijn|
|400|Ontwerpen, eind punt|de hoofd tekst van de aanvraag is onjuist, wat betekent dat de JSON ontbreekt, ongeldig is of te groot is|
|401|Ontwerpen|de gebruikte sleutel van het eindpunt abonnement in plaats van de sleutel voor schrijven|
|401|Ontwerpen, eind punt|ongeldige, ongeldige of lege sleutel|
|401|Ontwerpen, eind punt| sleutel komt niet overeen met regio|
|401|Ontwerpen|u bent niet de eigenaar of de samen werker|
|401|Ontwerpen|Ongeldige volg orde van API-aanroepen|
|403|Ontwerpen, eind punt|de totale maandelijkse quotum limiet voor de sleutel is overschreden|
|409|Eindpunt|de toepassing wordt nog steeds geladen|
|410|Eindpunt|de toepassing moet opnieuw worden getraind en opnieuw worden gepubliceerd|
|414|Eindpunt|de query overschrijdt het maximum aantal tekens|
|429|Ontwerpen, eind punt|Frequentie limiet is overschreden (aanvragen/seconde)|

## <a name="next-steps"></a>Volgende stappen

* Documentatie over REST API- [ontwerpen](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c2f) en- [eind punten](https://westus.dev.cognitive.microsoft.com/docs/services/5819c76f40a6350ce09de1ac/operations/5819c77140a63516d81aee78)
