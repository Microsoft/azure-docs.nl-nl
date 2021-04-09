---
title: Limieten-LUIS
description: Dit artikel bevat de bekende limieten van Azure Cognitive Services Language Understanding (LUIS). LUIS heeft verschillende limiet gebieden. De limiet voor het aantal besturings elementen voor modellen is intenties, entiteiten en functies in LUIS. Quotum limieten op basis van het sleutel type. Met toetscombinaties bepaalt u de LUIS-website.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 06/04/2020
ms.openlocfilehash: 41423ce34a62dfdbd5b9a60f683a2366a94d1bfd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "97976789"
---
# <a name="limits-for-your-luis-model-and-keys"></a>Limieten voor uw LUIS-model en-sleutels
LUIS heeft verschillende limiet gebieden. De eerste is de [model limiet](#model-limits), waarmee de intenties, entiteiten en onderdelen in Luis worden beheerd. Het tweede gebied is [quotum limieten](#key-limits) op basis van het sleutel type. Een derde gebied met limieten is de [toetscombinatie voor het beheren van de Luis](#keyboard-controls) -website. Een vierde gebied is de [wereld wijde toewijzing](luis-reference-regions.md) tussen de Luis-ontwerp website en de Luis- [eindpunt](luis-glossary.md#endpoint) api's.

<a name="model-boundaries"></a>

## <a name="model-limits"></a>Model limieten

Als uw app de limieten voor het LUIS-model overschrijdt, kunt u overwegen een [Luis-verzend](luis-concept-enterprise.md#dispatch-tool-and-model) -app te gebruiken of een [Luis-container](luis-container-howto.md)te gebruiken.

|Gebied|Limiet|
|--|:--|
| [Naam van app][luis-get-started-create-app] | * Standaard teken Max |
| Toepassingen| 500-toepassingen per Azure-ontwerp bron |
| [Batchgewijs testen][batch-testing]| 10 gegevens sets, 1000 uitingen per gegevensset|
| Expliciete lijst | 50 per toepassing|
| Externe entiteiten | geen limieten |
| [Intents][intents]|500 per toepassing: 499 aangepaste intenties en de vereiste _geen_ intentie.<br>[Op verzen ding gebaseerde](https://aka.ms/dispatch-tool) toepassing heeft corresponderende 500-verzend bronnen.|
| [Entiteiten weergeven](./luis-concept-entity-types.md) | Bovenliggend item: 50, onderliggend: 20.000 items. Canonieke naam is * standaard teken Max. Synoniemen waarden hebben geen beperking voor lengte. |
| [machine learning-entiteiten + rollen](./luis-concept-entity-types.md):<br> werken<br>Simple<br>entiteits rol|Een limiet van 100 bovenliggende entiteiten of 330 entiteiten, afhankelijk van het aantal gebruikers dat het eerst voor komt. Een rol telt als een entiteit voor het doel van deze limiet. Een voor beeld is een samen stelling met een eenvoudige entiteit, die twee rollen heeft: 1 composiet + 1 eenvoudige + 2 rollen = 4 van de 330 entiteiten.<br>Subentiteiten kunnen tot vijf niveaus worden genest, met een maximum van 10 onderliggende items per niveau.|
|Model als onderdeel| Het maximum aantal modellen dat kan worden gebruikt als onderdeel van een specifiek model om 10 modellen te zijn. Het maximum aantal woordgroepen lijsten dat als een functie voor een specifiek model wordt gebruikt, is 10 woordgroepen lijsten.|
| [Preview-dynamische lijst entiteiten](./luis-migration-api-v3.md)|2 lijsten met een eindpunt aanvraag van ongeveer 1 KB per query|
| [Patronen](luis-concept-patterns.md)|500 patronen per toepassing.<br>De maximale lengte van het patroon is 400 tekens.<br>3 patroon. alle entiteiten per patroon<br>Maxi maal 2 geneste optionele teksten in het patroon|
| [Patroon. alle](./luis-concept-entity-types.md)|100 per toepassing, 3 patroon. alle entiteiten per patroon |
| [Woordgroepen lijst][phrase-list]|500 woordgroepen lijsten. 10 globale woordgroepen lijsten vanwege het model als een functie limiet. De lijst met niet-verwisselbaar woordgroepen bevat een maximum van 5.000 zinsdelen. Lijst met verwisselbaar woordgroepen bevat een maximum van 50.000 zinsdelen. Maximum aantal woord groepen per toepassing van 500.000-zinsdelen.|
| [Vooraf gemaakte entiteiten](./howto-add-prebuilt-models.md) | geen limiet|
| [Entiteiten in de vorm van reguliere expressies](./luis-concept-entity-types.md)|20 entiteiten<br>Maxi maal 500 tekens. entiteits patroon per reguliere expressie|
| [Rollen](./luis-concept-entity-types.md)|300 rollen per toepassing. 10 rollen per entiteit|
| [Uiting][utterances] | 500 tekens<br><br>Als u tekst langer hebt dan deze limiet, moet u de utterance vóór de invoer in LUIS segmenteren en krijgt u afzonderlijke intentie reacties per segment. Er zijn duidelijke onderbrekingen waarmee u kunt werken, zoals Lees tekens en lange pauzes in spraak.|
| [Utterance-voor beelden][utterances] | 15.000 per toepassing: er is geen limiet voor het aantal uitingen per intentie<br><br>Als u de toepassing wilt trainen met meer voor beelden, gebruikt u een methode voor het [verzendings](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/Dispatch) model. U traint afzonderlijke LUIS-apps (ook wel onderliggende apps genoemd in de bovenliggende verzend-app) met een of meer intenties en traint vervolgens een Dispatch-app die steek proeven van elke onderliggende LUIS-app uitingen om de Voorspellings aanvraag naar de juiste onderliggende app te sturen. |
| [Versies](./luis-concept-app-iteration.md)| 100 versies per toepassing |
| [Versie naam][luis-how-to-manage-versions] | 128 tekens |

* Standaard teken Max is 50 tekens.

<a name="intent-and-entity-naming"></a>

## <a name="name-uniqueness"></a>Unieke naam

Object namen moeten uniek zijn in vergelijking met andere objecten van hetzelfde niveau.

|Objecten|Beperkingen|
|--|--|
|Intentie, entiteit|Alle intentie-en entiteits namen moeten uniek zijn in een versie van een app.|
|ML-entiteit onderdelen|Alle onderdelen van de machine learning-entiteit (onderliggende entiteiten) moeten binnen die entiteit uniek zijn voor onderdelen op hetzelfde niveau.|
|Functies | Alle benoemde functies, zoals woordgroepen lijsten, moeten uniek zijn in een versie van een app.|
|Entiteitsrollen|Alle rollen op een entiteits-of entiteits onderdeel moeten uniek zijn wanneer ze zich op hetzelfde niveau van de entiteit bevinden (bovenliggend, onderliggend, grandchild, enzovoort).|

## <a name="object-naming"></a>Naamgeving van objecten

Gebruik niet de volgende tekens in de volgende namen.

|Object|Tekens uitsluiten|
|--|--|
|De namen van intentie, entiteit en rollen|`:`<br>`$` <br> `&`|
|Versie naam|`\`<br> `/`<br> `:`<br> `?`<br> `&`<br> `=`<br> `*`<br> `+`<br> `(`<br> `)`<br> `%`<br> `@`<br> `$`<br> `~`<br> `!`<br> `#`|

## <a name="resource-usage-and-limits"></a>Resource gebruik en limieten

Taal kennis heeft afzonderlijke resources, een type voor ontwerpen en één type voor het uitvoeren van query's op het Voorspellings eindpunt. Zie voor meer informatie over de verschillen tussen sleutel typen [ontwerpen en query prediction-eindpunt sleutels in Luis](luis-how-to-azure-subscription.md).

<a name="key-limits"></a>

### <a name="authoring-resource-limits"></a>Resource limieten ontwerpen

Gebruik de _soort_, `LUIS.Authoring` , wanneer u resources in de Azure Portal filtert. LUIS beperkt 500-toepassingen per Azure-ontwerp bron.

|Resource ontwerpen|TPS ontwerpen|
|--|--|
|Starter|1 miljoen/maand, 5/seconde|
|F0-gratis laag |1 miljoen/maand, 5/seconde|

* TPS = trans acties per seconde

[Meer informatie over prijzen.][pricing]

### <a name="query-prediction-resource-limits"></a>Resource limieten voor voor spelling query's

Gebruik de _soort_, `LUIS` , wanneer u resources in de Azure Portal filtert. De LUIS-query Voorspellings eindpunt resource, die in de runtime wordt gebruikt, is alleen geldig voor eindpunt query's.

|Query Voorspellings resource|Query TPS|
|--|--|
|F0-gratis laag |10 duizend/maand, 5/seconde|
|S0-Standard-laag|50 per seconde|

### <a name="sentiment-analysis"></a>Sentimentanalyse

[Sentiment-analyse integratie](luis-how-to-publish-app.md#enable-sentiment-analysis), die sentiment-informatie bevat, wordt geleverd zonder dat er een andere Azure-resource nodig is.

### <a name="speech-integration"></a>Spraak integratie

[Spraak integratie](../speech-service/how-to-recognize-intents-from-speech-csharp.md) biedt 1000 eindpunt aanvragen per eenheids kosten.

[Meer informatie over prijzen.][pricing]

## <a name="keyboard-controls"></a>Toetsenbord besturings elementen

|Toetsenbord invoer | Description |
|--|--|
|Control + E|scha kelen tussen tokens en entiteiten in de lijst uitingen|

## <a name="website-sign-in-time-period"></a>Tijds periode website aanmelding

Uw aanmeldings toegang is **60 minuten**. Na deze periode wordt deze fout weer geven. U moet zich opnieuw aanmelden.

[luis-get-started-create-app]: ./luis-get-started-create-app.md
[batch-testing]: ./luis-concept-test.md#batch-testing
[intents]: ./luis-concept-intent.md
[phrase-list]: ./luis-concept-feature.md
[utterances]: ./luis-concept-utterance.md
[luis-how-to-manage-versions]: ./luis-how-to-manage-versions.md
[pricing]: https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/
<!-- TBD: fix this link -->
[speech-to-intent-pricing]: https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/
