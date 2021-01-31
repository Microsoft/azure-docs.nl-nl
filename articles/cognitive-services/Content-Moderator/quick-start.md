---
title: 'Snelstart: Content Moderator op internet proberen'
titleSuffix: Azure Cognitive Services
description: In deze quickstart gebruikt u het online Content Moderator-controlehulpprogramma om de basisfunctionaliteit van Content Moderator te testen zonder dat u code hoeft te schrijven.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: quickstart
ms.date: 01/29/2021
ms.author: pafarley
ms.custom: cog-serv-seo-aug-2020
keywords: content moderator, inhoudsbeheer
ms.openlocfilehash: f8ad9c135fea4e582e6ba47764d0401936f8c295
ms.sourcegitcommit: 54e1d4cdff28c2fd88eca949c2190da1b09dca91
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/31/2021
ms.locfileid: "99221173"
---
# <a name="quickstart-try-content-moderator-on-the-web"></a>Snelstart: Content Moderator op internet proberen

In deze quickstart gebruikt u het online Content Moderator-controlehulpprogramma om de basisfunctionaliteit van Content Moderator te testen zonder dat u code hoeft te schrijven. Als u deze service sneller wilt integreren in uw inhoudsbeheer-app, raadpleegt u de andere quickstarts in de sectie [Volgende stappen](#next-steps).

## <a name="prerequisites"></a>Vereisten

- Een webbrowser

## <a name="set-up-the-review-tool"></a>Het controlehulpprogramma instellen
Het Content Moderator beoordeling is een webhulpprogramma waarmee menselijke revisoren de cognitieve service kunnen helpen bij het nemen van beslissingen. In deze handleiding doorloopt u het korte proces voor het instellen van het controlehulpprogramma, zodat u kunt zien hoe de Content Moderator-service werkt. Ga naar de website van het [Content Moderator-controlehulpprogramma](https://contentmoderator.cognitive.microsoft.com/) en meld u aan.

![Startpagina van Content Moderator](images/homepage.PNG)

## <a name="create-a-review-team"></a>Een beoordelingsteam maken

Maak vervolgens een beoordelingsteam. In een werkscenario is dit de groep personen die handmatig de beheerbeslissingen van de service controleert. Als u een team wilt maken, moet u een **regio** selecteren en een **teamnaam** en een **team-id** opgeven. Als u collega's wilt uitnodigen voor het team, kunt u dit doen door hier hun e-mailadressen in te voeren.

> [!NOTE]
> **Teamnaam** is een beschrijvende naam voor uw beoordelingsteam. Dit is de naam die wordt weergegeven in Azure Portal. De **team-id** wordt gebruikt ter identificatie van uw beoordelingsteam.

> [!div class="mx-imgBorder"]
> ![Teamlid uitnodigen](images/create-team.png).

Als u ervoor kiest om gegevens te versleutelen met behulp van een door klant beheerde sleutel (CMK), wordt u gevraagd om de **resource-id** voor uw Content Moderator-resource in de prijs categorie E0. De resource die u opgeeft, moet uniek zijn voor dit team. 

> [!div class="mx-imgBorder"]
> ![Teamlid uitnodigen met CMK](images/create-team-cmk.png)

## <a name="upload-sample-content"></a>Voorbeeldinhoud uploaden

U bent nu klaar om voorbeeldinhoud te uploaden. Selecteer **Try > Image**, **Try > Text** of **Try > Video**.

> [!div class="mx-imgBorder"]
> ![Beheer van afbeeldingen of tekst uitproberen](images/tryimagesortext.png)

Verzend de inhoud die u wilt beheren. U kunt de volgende voorbeeldtekstinhoud gebruiken:

```
Is this a grabage email abcdef@abcd.com, phone: 4255550111, IP: 255.255.255.255, 1234 Main Boulevard, Panapolis WA 96555.
Crap is the profanity here. Is this information PII? phone 4255550111
```

Intern roept het controlehulpprogramma de beheer-API's aan om uw inhoud te scannen. Zodra het scannen is voltooid, ziet u een bericht waarin wordt gemeld dat er resultaten zijn die wachten op uw beoordeling.

> [!div class="mx-imgBorder"]
> ![Bestanden beheren](images/submitted.png)

## <a name="review-moderation-tags"></a>Beheertags controleren

Controleer de toegepaste beheertags. U kunt de tags die zijn toegepast op uw inhoud en de score in elke categorie bekijken. Zie de artikelen over het beheer van [afbeeldingen](image-moderation-api.md), [teksten](text-moderation-api.md) en [video's](video-moderation-api.md) voor meer informatie over de betekenis van de verschillende inhoudstags.

<!-- ![Review results](images/reviewresults_text.png) -->

In een project kunt u of uw beoordelingsteam deze tags wijzigen of meer tags toevoegen, indien nodig. U verzendt deze wijzigingen met de knop **Volgende**. Als uw zakelijke toepassing de Moderator-API's aanroept, wordt de getagde inhoud hier in de wachtrij geplaatst, zodat deze kan worden gecontroleerd door de leden van de beoordelingsteams. Met deze aanpak kunt u snel grote hoeveelheden inhoud bekijken.

Op dit moment hebt u het Content Moderator-controlehulpprogramma gebruikt om voorbeelden te bekijken van wat de Content Moderator-service kan doen. Daarna kunt u meer te weten komen over het controlehulpprogramma en hoe u dit integreert in een softwareproject met behulp van de controle-API's, of u kunt doorgaan naar de sectie [Volgende stappen](#next-steps) voor informatie over het gebruik van de beheer-API's zelf in uw app.

## <a name="learn-more-about-the-review-tool"></a>Meer informatie over het controlehulpprogramma

Als u meer wilt weten over het gebruik van het Content Moderator-controlehulpprogramma, raadpleegt u de handleiding voor het [controlehulpprogramma](Review-Tool-User-Guide/human-in-the-loop.md) Guide en bekijkt u de API's van het controlehulpprogramma voor meer informatie over het afstemmen van de procedure voor menselijke beoordeling:
- De [taak-API](try-review-api-job.md) scant uw inhoud met behulp van de beheer-API's en genereert beoordelingen in het controlehulpprogramma. 
- De [controle-API](try-review-api-review.md) maakt direct beoordelingen van afbeeldingen, teksten of video's voor menselijke revisoren zonder eerst de inhoud te scannen. 
- Met de [werkstroom-API](try-review-api-workflow.md) worden details over de aangepaste werkstromen die uw team maakt, gemaakt, bijgewerkt en opgehaald.

U kunt ook doorgaan met de volgende stappen om aan de slag te gaan met de beheer-API's in uw code.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het gebruik van de beheer-API's in uw app.
- Beheer van afbeeldingen implementeren. Gebruik de [API-console](try-image-api.md) of volg een [Snelstartgids](client-libraries.md) om afbeeldingen te scannen en potentiële inhoud van de volwassene en ongepaste te detecteren met behulp van tags, betrouwbaarheids scores en andere geëxtraheerde informatie.
- Beheer van tekst implementeren. Gebruik de [API-console](try-text-api.md) of volg een [Quick](client-libraries.md) start om de tekst inhoud te scannen op mogelijke Gods baarheid, persoonlijke gegevens en andere ongewenste tekst.
- Beheer van video's implementeren. Volg de [instructiegids voor het beheer van video's voor C#](video-moderation-api.md) om video's te scannen en potentiële inhoud voor volwassenen en ongepast inhoud te detecteren. 
