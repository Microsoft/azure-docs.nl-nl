---
title: Naslag informatie over patroon syntaxis-LUIS
description: Maak entiteiten voor het extra heren van belang rijke gegevens van gebruikers uitingen in apps van Language Understanding (LUIS). Geëxtraheerde gegevens worden gebruikt door de client toepassing.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 04/14/2020
ms.openlocfilehash: 3caccd6766226ce68b371856b081b052c1033f71
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91542186"
---
# <a name="pattern-syntax"></a>Patroonsyntaxis

De syntaxis van het patroon is een sjabloon voor een utterance. De sjabloon moet woorden en entiteiten bevatten die u wilt laten overeenkomen, evenals woorden en [interpunctie](luis-reference-application-settings.md#punctuation-normalization) die u wilt negeren. Het is **geen** reguliere expressie.

> [!CAUTION]
> Patronen zijn alleen van toepassing op machine learning-entiteit, niet voor subentiteiten.

Entiteiten in patronen worden omgeven door accolades, `{}` . Patronen kunnen entiteiten en entiteiten met rollen bevatten. [Patroon. any](luis-concept-entity-types.md#patternany-entity) is een entiteit die alleen in patronen wordt gebruikt.

De syntaxis van het patroon ondersteunt de volgende syntaxis:

|Functie|Syntax|Niveau nesten|Voorbeeld|
|--|--|--|--|
|vennootschap| {} -accolades|2|Waar is formulier {entity-name}?|
|optioneel|[]-vier Kante haken<BR><BR>Er is een limiet van 3 op geneste niveaus van een combi natie van optioneel en groepering |2|Het vraag teken is optioneel [?]|
|shapes|()-haakjes|2|is (a \| b)|
|of| \| -verticale streep (pipe)<br><br>Er is een limiet van 2 op de verticale balken (of) in één groep |-|Waar is formulier ({Form-name-Short} &#x7c; {Form-name-Long} &#x7c; {Form-Number})|
|begin en/of einde van utterance|^-caret|-|^ utterance starten<br>de utterance is voltooid.<br>^ strikte letterlijke overeenkomst van de volledige utterance met {Number} entiteit ^|

## <a name="nesting-syntax-in-patterns"></a>Syntaxis nesten in patronen

De **optionele** syntaxis met vier Kante haken kan twee niveaus genest zijn. Bijvoorbeeld: `[[this]is] a new form`. In dit voor beeld worden de volgende uitingen toegestaan:

|Genest optioneel utterance-voor beeld|Uitleg|
|--|--|
|Dit is een nieuw formulier|komt overeen met alle woorden in het patroon|
|is een nieuw formulier|komt overeen met buitenste optionele woord en niet-optionele woorden in een patroon|
|een nieuw formulier|komt overeen met alleen de vereiste woorden|

De **groeperings** syntaxis met haakjes kan twee niveaus genest zijn. Bijvoorbeeld: `(({Entity1.RoleName1} | {Entity1.RoleName2} ) | {Entity2} )`. Met deze functie kunnen de drie entiteiten overeenkomen.

Als Entity1 een locatie is met functies zoals origin (Seattle) en doel (Cairo) en entiteit 2 is een bekend gebouw naam van een lijst entiteit (RedWest-C), zou de volgende uitingen worden toegewezen aan dit patroon:

|Voor beeld van geneste groeperings utterance|Uitleg|
|--|--|
|RedWest-C|komt overeen met buitenste groeperings entiteit|
|Seattle|komt overeen met een van de binnenste groeperings entiteiten|
|Cairo|komt overeen met een van de binnenste groeperings entiteiten|

## <a name="nesting-limits-for-groups-with-optional-syntax"></a>Limieten voor groepen met optionele syntaxis nesten

Een combi natie van **groepering** met **optionele** syntaxis heeft een limiet van 3 geneste niveaus.

|Toegestaan|Voorbeeld|
|--|--|
|Ja|([(test1 &#x7c; Test2)] &#x7c; Test3)|
|Nee|([([test1] &#x7c; Test2)] &#x7c; Test3)|

## <a name="nesting-limits-for-groups-with-or-ing-syntax"></a>Limieten voor het nesten van groepen met de syntaxis

Een combi natie van **groepering** met **of-ING-** syntaxis heeft een limiet van 2 verticale balken.

|Toegestaan|Voorbeeld|
|--|--|
|Ja|(test1 &#x7c; Test2 &#x7c; (Test3 &#x7c; test4))|
|Nee|(test1 &#x7c; Test2 &#x7c; Test3 &#x7c; (test4 &#x7c; test5)) |

## <a name="syntax-to-add-an-entity-to-a-pattern-template"></a>Syntaxis om een entiteit toe te voegen aan een patroon sjabloon
Als u een entiteit wilt toevoegen aan de patroon sjabloon, plaatst u de naam van de entiteit tussen accolades, zoals `Who does {Employee} manage?` .

|Patroon met entiteit|
|--|
|`Who does {Employee} manage?`|

## <a name="syntax-to-add-an-entity-and-role-to-a-pattern-template"></a>Syntaxis voor het toevoegen van een entiteit en rol aan een patroon sjabloon
Een entiteits functie wordt aangeduid `{entity:role}` met de naam van de entiteit, gevolgd door een dubbele punt, en vervolgens de naam van de rol. Als u een entiteit met een rol wilt toevoegen aan de patroon sjabloon, plaatst u de naam van de entiteit en de naam van de rol door accolades, zoals `Book a ticket from {Location:Origin} to {Location:Destination}` .

|Patroon met entiteits rollen|
|--|
|`Book a ticket from {Location:Origin} to {Location:Destination}`|

## <a name="syntax-to-add-a-patternany-to-pattern-template"></a>Syntaxis voor het toevoegen van een patroon. elke sjabloon voor een patroon
Het patroon. elke entiteit kunt u een entiteit van een lengte van variërend aan het patroon toevoegen. Zolang de patroon sjabloon wordt gevolgd, is het patroon. elk kan elk wille keurig lengte zijn.

Als u een **patroon wilt toevoegen. een wille keurige** entiteit in de patroon sjabloon, rondom het patroon. elke entiteit met de accolades, zoals `How much does {Booktitle} cost and what format is it available in?` .

|Patroon met patroon. elke entiteit|
|--|
|`How much does {Booktitle} cost and what format is it available in?`|

|Boek titels in het patroon|
|--|
|Wat zijn de kosten van **Dit boek** , maar wat is er beschikbaar in?|
|Wat zijn de kosten voor de **vraag** en welke indeling is er beschikbaar in?|
|Wat gebeurt er met **het nieuws gierige incidenten van de hond in de nacht** kosten en in welke indeling deze beschikbaar is?|

De woorden van de boek titel zijn niet verwarrend voor LUIS, omdat LUIS weet waar de titel van het boek eindigt, op basis van het patroon. elke entiteit.

## <a name="explicit-lists"></a>Expliciete lijsten

Maak een [expliciete lijst](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5ade550bd5b81c209ce2e5a8) via de ontwerp-API om de uitzonde ring toe te staan wanneer:

* Uw patroon bevat een [patroon.](luis-concept-entity-types.md#patternany-entity)
* En met deze patroon syntaxis kan een onjuiste entiteits extractie worden uitgevoerd op basis van de utterance.

Stel dat u een patroon hebt met zowel optionele syntaxis, als de `[]` syntaxis van de entiteit, `{}` , gecombineerd op een manier om gegevens onjuist op te halen.

Overweeg het patroon [Find] e-mail over {subject} [van {person}].

In de volgende uitingen worden het **onderwerp** en de **persoons** entiteit op de juiste wijze en onjuist opgehaald:

|Uiting|Entiteit|Juiste extractie|
|--|--|:--:|
|e-mail over honden van Chris|onderwerp = honden<br>persoon = Chris|✔|
|e-mail over de man van La Mancha|onderwerp = de man<br>persoon = La Mancha|X|

In de voor gaande tabel moet het onderwerp `the man from La Mancha` (de titel van een boek) zijn, maar omdat het onderwerp het optionele woord bevat `from` , is de titel onjuist voor speld.

Als u deze uitzonde ring wilt herstellen naar het patroon, voegt u `the man from la mancha` als een expliciete lijst overeenkomst toe voor de entiteit {subject} met behulp [van de API voor het maken van expliciete lijsten](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5ade550bd5b81c209ce2e5a8).

## <a name="syntax-to-mark-optional-text-in-a-template-utterance"></a>Syntaxis voor het markeren van optionele tekst in een sjabloon utterance
Markeer optionele tekst in de utterance met de reguliere expressie vier Kante haakjes syntaxis `[]` . Met de optionele tekst kunnen vier Kante haken Maxi maal twee haken worden genest.

|Patroon met optionele tekst|Betekenis|
|--|--|
|`[find] email about {subject} [from {person}]`|`find` en `from {person}` zijn optioneel|
|' Kan ik me helpen [?]|Het lees teken is optioneel|

Lees tekens ( `?` , `!` , `.` ) moeten worden genegeerd en u moet ze negeren met de syntaxis van het vier Kante haakje in patronen.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over patronen:

* [Patronen toevoegen](luis-how-to-model-intent-pattern.md)
* [Een patroon toevoegen. elke entiteit](luis-how-to-add-entities.md#add-a-patternany-entity)
* [Patronen concepten](luis-concept-patterns.md)

Begrijp hoe [sentiment](luis-reference-prebuilt-sentiment.md) wordt geretourneerd in het. json-antwoord.