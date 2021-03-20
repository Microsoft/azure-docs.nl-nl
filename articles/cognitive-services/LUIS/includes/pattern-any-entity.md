---
title: bestand opnemen
description: bestand opnemen
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.date: 02/14/2020
ms.topic: include
ms.openlocfilehash: 5eb2517b4b71b61dd70406f345c005d007d9e3ef
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91535951"
---
Met de entiteit pattern.any kunt u vrije gegevens vinden waarbij het lastig is om uit de tekst van de entiteit het einde van de entiteit te bepalen op basis van de rest van de utterance.

Een Human Resources-app helpt medewerkers bij het zoeken van bedrijfsformulieren.

|Utterance|
|--|
|Waar is **HRF-123456**?|
|Wie is de auteur van **HRF 123234**?|
|Is **HRF-456098** gepubliceerd in het Frans?|

Elk formulier heeft echter zowel een opgemaakte naam, gebruikt in de voorgaande tabel, als een beschrijvende naam, zoals `Request relocation from employee new to the company 2018 version 5`.

Utterances met de beschrijvende naam van het formulier zien er als volgt uit:

|Utterance|
|--|
|Waar is de **Aanvraag voor relocatie van nieuwe medewerker in het bedrijf 2018 versie 5**?|
|Wie heeft **Aanvraag voor relocatie van nieuwe medewerker in het bedrijf 2018 versie 5** geschreven?|
|Is **Aanvraag voor relocatie van nieuwe medewerker in het bedrijf 2018 versie 5** gepubliceerd in het Frans?|

De lengte varieert en er zijn woorden die verwarrend kunnen zijn voor LUIS om te bepalen waar de entiteit eindigt. Met behulp van een entiteit Pattern.any in een patroon, kunt u het begin en einde van de naam van het formulier opgeven, zodat de naam van het formulier correct worden geëxtraheerd door LUIS.

|Voorbeeld van sjabloon-utterance|
|--|
|Waar is {FormName}[?]|
|Wie heeft {FormName} geschreven[?]|
|Is {FormName} gepubliceerd in het Frans[?]|

Pattern.any-[referentie](../reference-entity-pattern-any.md)gegevens bekijken