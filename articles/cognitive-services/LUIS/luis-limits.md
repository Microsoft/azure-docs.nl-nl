---
title: Limieten - LUIS
description: Dit artikel bevat de bekende limieten van Azure Cognitive Services Language Understanding (LUIS). LUIS heeft verschillende limietgebieden. Modellimiet bepaalt intenties, entiteiten en functies in LUIS. Quotumlimieten op basis van sleuteltype. Met een toetsenbordcombinatie wordt de LUIS-website bestuurd.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 06/04/2020
ms.openlocfilehash: 1f917087eb15d8c77356995299e27dfc1657cb5d
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107497197"
---
# <a name="limits-for-your-luis-model-and-keys"></a>Limieten voor uw LUIS-model en -sleutels
LUIS heeft verschillende limietgebieden. De eerste is de [modellimiet](#model-limits), waarmee intenties, entiteiten en functies in LUIS worden gecontroleerd. Het tweede gebied is [quotumlimieten](#key-limits) op basis van sleuteltype. Een derde gebied van limieten is de [toetsenbordcombinatie voor](#keyboard-controls) het beheren van de LUIS-website. Een vierde gebied is de [wereldregiotoewijzing](luis-reference-regions.md) tussen de LUIS-ontwerpwebsite en de [LUIS-eindpunt-API's.](luis-glossary.md#endpoint)

<a name="model-boundaries"></a>

## <a name="model-limits"></a>Modellimieten

Als uw app de limieten van het LUIS-model overschrijdt, kunt u overwegen om een [LUIS-verzend-app te](luis-concept-enterprise.md#dispatch-tool-and-model) gebruiken of een [LUIS-container te gebruiken.](luis-container-howto.md)

|Gebied|Limiet|
|--|:--|
| [Naam van app][luis-get-started-create-app] | *Maximum aantal standaardtekens |
| Toepassingen| 500 toepassingen per Azure-ontwerpresource |
| [Batchgewijs testen][batch-testing]| 10 gegevenssets, 1000 utterances per gegevensset|
| Expliciete lijst | 50 per toepassing|
| Externe entiteiten | geen limieten |
| [Intenties][intents]|500 per toepassing: 499 aangepaste intenties en de vereiste intentie _Geen._<br>[De toepassing op basis](https://aka.ms/dispatch-tool) van verzending heeft de bijbehorende 500 verzendbronnen.|
| [Entiteiten weergeven](./luis-concept-entity-types.md) | Bovenliggend: 50, onderliggend: 20.000 items. Canonieke naam is *standaard character max. Synoniemwaarden hebben geen lengtebeperking. |
| [machine learning-entiteiten en -rollen:](./luis-concept-entity-types.md)<br> Samengestelde<br>Eenvoudige<br>entiteitsrol|Een limiet van 100 bovenliggende entiteiten of 330 entiteiten, welke limiet de gebruiker het eerst bereikt. Een rol telt als een entiteit voor deze limiet. Een voorbeeld is een samengestelde entiteit met een eenvoudige entiteit, die 2 rollen heeft: 1 samengestelde + 1 eenvoudige + 2 rollen = 4 van de 330 entiteiten.<br>Subentiteiten kunnen maximaal 5 niveaus worden genest, met maximaal 20 kinderen per niveau.|
|Model als een functie| Het maximum aantal modellen dat als functie voor een specifiek model kan worden gebruikt, is 10 modellen. Het maximum aantal woordgroepenlijsten dat als functie voor een specifiek model wordt gebruikt, is 10 woordgroepenlijsten.|
| [Preview - Dynamische lijstentiteiten](./luis-migration-api-v3.md)|2 lijsten met ~1.000 per aanvraag voor het queryvoorspellings-eindpunt|
| [Patronen](luis-concept-patterns.md)|500 patronen per toepassing.<br>De maximale lengte van een patroon is 400 tekens.<br>3 Pattern.any-entiteiten per patroon<br>Maximaal 2 geneste optionele teksten in patroon|
| [Pattern.any](./luis-concept-entity-types.md)|100 per toepassing, 3 pattern.any-entiteiten per patroon |
| [Frasenlijst][phrase-list]|500 woordgroepenlijsten. 10 globale woordgroepenlijsten vanwege het model als een functielimiet. Niet-uitwisselbare frasenlijst heeft maximaal 5000 woordgroepen. Een uitwisselbare frasenlijst heeft maximaal 50.000 woordgroepen. Maximumaantal zinnen per toepassing van 500.000 woordgroepen.|
| [Vooraf gemaakte entiteiten](./howto-add-prebuilt-models.md) | geen limiet|
| [Entiteiten in de vorm van reguliere expressies](./luis-concept-entity-types.md)|20 entiteiten<br>Maximaal 500 tekens. entiteitspatroon per reguliere expressie|
| [Rollen](./luis-concept-entity-types.md)|300 rollen per toepassing. 10 rollen per entiteit|
| [Uiting][utterances] | 500 tekens<br><br>Als u tekst hebt die langer is dan deze tekenlimiet, moet u de uiting segmenteren vóór invoer naar LUIS en ontvangt u afzonderlijke intentiereacties per segment. Er zijn duidelijke onderbrekingen waar u mee kunt werken, zoals leestekens en lange pauzes in spraak.|
| [Voorbeelden van utterances][utterances] | 15.000 per toepassing: er is geen limiet voor het aantal utterances per intentie<br><br>Als u de toepassing wilt trainen met [](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/Dispatch) meer voorbeelden, gebruikt u een dispatch-modelbenadering. U traint afzonderlijke LUIS-apps (ook wel onderliggende apps genoemd naar de bovenliggende verzend-app) met een of meer intenties en traint vervolgens een verzend-app die voorbeelden van uitingen van elke onderliggende LUIS-app gebruikt om de voorspellingsaanvraag naar de juiste onderliggende app te sturen. |
| [Versies](./luis-concept-app-iteration.md)| 100 versies per toepassing |
| [Versienaam][luis-how-to-manage-versions] | 128 tekens |

*Het maximum van het standaardteken is 50 tekens.

<a name="intent-and-entity-naming"></a>

## <a name="name-uniqueness"></a>Uniekheid van naam

Objectnamen moeten uniek zijn in vergelijking met andere objecten van hetzelfde niveau.

|Objecten|Beperkingen|
|--|--|
|Intentie, entiteit|Alle namen van intenties en entiteiten moeten uniek zijn in een versie van een app.|
|ML-entiteitsonderdelen|Alle machine learning-entiteitsonderdelen (onderliggende entiteiten) moeten uniek zijn binnen die entiteit voor onderdelen op hetzelfde niveau.|
|Functies | Alle benoemde functies, zoals woordgroepenlijsten, moeten uniek zijn binnen een versie van een app.|
|Entiteitsrollen|Alle rollen in een entiteit of entiteitsonderdeel moeten uniek zijn wanneer ze zich op hetzelfde entiteitsniveau (bovenliggend, onderliggend, kleinkind, enzovoort) hebben.|

## <a name="object-naming"></a>Objectnaamgeving

Gebruik niet de volgende tekens in de volgende namen.

|Object|Tekens uitsluiten|
|--|--|
|Namen van intenties, entiteiten en rollen|`:`<br>`$` <br> `&`|
|Versienaam|`\`<br> `/`<br> `:`<br> `?`<br> `&`<br> `=`<br> `*`<br> `+`<br> `(`<br> `)`<br> `%`<br> `@`<br> `$`<br> `~`<br> `!`<br> `#`|

## <a name="resource-usage-and-limits"></a>Resourcegebruik en -limieten

Language Understand heeft afzonderlijke resources, één type voor ontwerp en één type voor het uitvoeren van query's op het voorspellings-eindpunt. Zie [Authoring and query prediction endpoint keys in LUIS](luis-how-to-azure-subscription.md)(Eindpuntsleutels voor ontwerp- en queryvoorspelling in LUIS) voor meer informatie over de verschillen tussen sleuteltypen.

<a name="key-limits"></a>

### <a name="authoring-resource-limits"></a>Resourcelimieten voor ontwerp

Gebruik het _type_, `LUIS.Authoring` , bij het filteren van resources in de Azure Portal. LUIS beperkt 500 toepassingen per Azure-ontwerpresource.

|Ontwerpresource|TPS maken|
|--|--|
|Starter|1 miljoen per maand, 5/seconde|
|F0 - Gratis laag |1 miljoen per maand, 5/seconde|

* TPS = transacties per seconde

[Meer informatie over prijzen.][pricing]

### <a name="query-prediction-resource-limits"></a>Resourcelimieten voor queryvoorspelling

Gebruik het _type_, `LUIS` , bij het filteren van resources in de Azure Portal. De LUIS-queryvoorspellings-eindpuntresource, die in de runtime wordt gebruikt, is alleen geldig voor eindpuntquery's.

|Resource voor queryvoorspelling|Query TPS|
|--|--|
|F0 - Gratis laag |10.000.000 per maand, 5/seconde|
|S0 - Standard-laag|50 per seconde|

### <a name="sentiment-analysis"></a>Sentimentanalyse

[Sentimentanalyse-integratie,](luis-how-to-publish-app.md#enable-sentiment-analysis)die sentimentinformatie biedt, wordt aangeboden zonder dat u een andere Azure-resource nodig hebt.

### <a name="speech-integration"></a>Spraakintegratie

[Spraakintegratie biedt](../speech-service/how-to-recognize-intents-from-speech-csharp.md) 1.000 eindpuntaanvragen per eenheidskosten.

[Meer informatie over prijzen.][pricing]

## <a name="keyboard-controls"></a>Toetsenbordbesturingselementen

|Toetsenbordinvoer | Beschrijving |
|--|--|
|Besturingselement + E|schakelt tussen tokens en entiteiten in de lijst met utterances|

## <a name="website-sign-in-time-period"></a>Aanmeldingsperiode van website

Uw aanmeldingstoegang is **60 minuten.** Na deze periode krijgt u deze foutmelding. U moet zich opnieuw aanmelden.

[luis-get-started-create-app]: ./luis-get-started-create-app.md
[batch-testing]: ./luis-concept-test.md#batch-testing
[intents]: ./luis-concept-intent.md
[phrase-list]: ./luis-concept-feature.md
[utterances]: ./luis-concept-utterance.md
[luis-how-to-manage-versions]: ./luis-how-to-manage-versions.md
[pricing]: https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/
<!-- TBD: fix this link -->
[speech-to-intent-pricing]: https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/
