---
title: Gegevens opslag-LUIS
titleSuffix: Azure Cognitive Services
description: LUIS slaat gegevens op die zijn versleuteld in een Azure-gegevens opslag die overeenkomt met de regio die is opgegeven door de sleutel.
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 12/07/2020
ms.openlocfilehash: a58bcff4e39c4a4a907cd8567b47b074ff299bd5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "97008449"
---
# <a name="data-storage-and-removal-in-language-understanding-luis-cognitive-services"></a>Gegevens opslag en verwijderen in Language Understanding (LUIS) Cognitive Services

LUIS slaat gegevens op die zijn versleuteld in een Azure-gegevens opslag die overeenkomt met [de regio](luis-reference-regions.md) die is opgegeven door de sleutel. 

* Gegevens die worden gebruikt om het model te trainen, zoals entiteiten, intenties en uitingen, worden opgeslagen in LUIS voor de levens duur van de toepassing. Als een eigenaar of Inzender de app verwijdert, worden deze gegevens verwijderd. Als een toepassing in 90 dagen niet is gebruikt, wordt deze verwijderd. 

* Ontwerpers van toepassingen kunnen ervoor kiezen om [logboek registratie in te scha kelen](luis-how-to-review-endpoint-utterances.md#log-user-queries-to-enable-active-learning) op de uitingen die worden verzonden naar een gepubliceerde toepassing. Als deze functie is ingeschakeld, worden uitingen 30 dagen opgeslagen en kunnen ze worden weer gegeven door de auteur van de toepassing. Als logboek registratie niet is ingeschakeld wanneer de toepassing wordt gepubliceerd, worden deze gegevens niet opgeslagen.

## <a name="export-and-delete-app"></a>App exporteren en verwijderen
Gebruikers hebben volledige controle over het [exporteren](luis-how-to-start-new-app.md#export-app) en [verwijderen](luis-how-to-start-new-app.md#delete-app) van de app. 

## <a name="utterances"></a>Uitingen

Uitingen kan op twee verschillende plaatsen worden opgeslagen. 

* Tijdens **het ontwerp proces** worden uitingen gemaakt en opgeslagen in de bedoeling. Uitingen in intenties zijn vereist voor een geslaagde LUIS-app. Zodra de app is gepubliceerd en er query's worden ontvangen op het eind punt, wordt de query reeks van het eindpunt verzoek, `log=false` , bepaalt of het eind punt utterance is opgeslagen. Als het eind punt wordt opgeslagen, maakt het deel uit van het actieve Learning uitingen dat wordt weer gegeven in de sectie **Build** van de portal, in de sectie beoordeling van het **eind punt uitingen** . 
* Wanneer u **endpoint uitingen bekijkt** en een utterance aan een intentie toevoegt, wordt de utterance niet meer opgeslagen als onderdeel van het eind punt uitingen dat moet worden gecontroleerd. Deze wordt toegevoegd aan de intenties van de app. 

<a name="utterances-in-an-intent"></a>

### <a name="delete-example-utterances-from-an-intent"></a>Voor beeld-uitingen verwijderen uit een intentie

Voor beeld-uitingen gebruikt voor training [Luis](luis-reference-regions.md). Als u een voorbeeld utterance uit uw LUIS-app verwijdert, wordt deze verwijderd uit de LUIS-webservice en is deze niet beschikbaar voor export.

<a name="utterances-in-review"></a>

### <a name="delete-utterances-in-review-from-active-learning"></a>Uitingen verwijderen uit actief leren

U kunt uitingen verwijderen uit de lijst met gebruikers uitingen die LUIS worden voorgesteld op de **[pagina eind punt controleren uitingen](luis-how-to-review-endpoint-utterances.md)**. Als u uitingen uit deze lijst verwijdert, kunnen ze niet worden voorgesteld, maar worden ze niet verwijderd uit de logboeken.

Als u geen actieve Learning uitingen wilt, kunt u [actief leren uitschakelen](luis-how-to-review-endpoint-utterances.md#disable-active-learning). Als u actief leren uitschakelt, wordt logboek registratie ook uitgeschakeld.

### <a name="disable-logging-utterances"></a>Logboek registratie uitingen uitschakelen
Als u [actief leren uitschakelt](luis-how-to-review-endpoint-utterances.md#disable-active-learning) , wordt logboek registratie uitgeschakeld.


<a name="accounts"></a>

## <a name="delete-an-account"></a>Een account verwijderen
Als u niet bent gemigreerd, kunt u uw account verwijderen. alle apps worden samen met hun voor beeld-uitingen en-logboeken verwijderd. De gegevens worden 90 dagen bewaard voordat het account en de gegevens permanent worden verwijderd.

Het verwijderen van het account is beschikbaar op de pagina **instellingen** . Selecteer uw account naam in de rechter bovenhoek om naar de pagina **instellingen** te gaan.

## <a name="delete-an-authoring-resource"></a>Een ontwerp bron verwijderen
Als u hebt [gemigreerd naar een](./luis-migration-authoring.md)bewerkings resource, verwijdert u de resource zelf uit het Azure Portal worden alle toepassingen die zijn gekoppeld aan die resource verwijderd, samen met hun voorbeeld uitingen en Logboeken. De gegevens worden 90 dagen bewaard voordat deze permanent worden verwijderd.    

Als u uw resource wilt verwijderen, gaat u naar de [Azure Portal](https://ms.portal.azure.com/#home) en selecteert u uw Luis-ontwerp bron. Ga naar het tabblad **overzicht** en klik op de knop **verwijderen** boven aan de pagina. Bevestig dat uw resource is verwijderd. 

## <a name="data-inactivity-as-an-expired-subscription"></a>Gegevens inactiviteit als verlopen abonnement
Voor het bewaren en verwijderen van gegevens kan een inactieve LUIS-app op _micro soft_ worden behandeld als een verlopen abonnement. Een app wordt als inactief beschouwd als deze voldoet aan de volgende criteria voor de afgelopen 90 dagen: 

* Er zijn **geen** aanroepen naar verzonden.
* Is niet gewijzigd.
* Er is geen huidige sleutel toegewezen aan de app.
* Heeft geen gebruiker aangemeld.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Meer informatie over het exporteren en verwijderen van een app](luis-how-to-start-new-app.md)