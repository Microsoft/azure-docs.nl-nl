---
title: API-verwijzing-Content Moderator
titleSuffix: Azure Cognitive Services
description: Meer informatie over de verschillende inhouds toezicht-en controle-Api's voor Content Moderator.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: reference
ms.date: 05/29/2019
ms.author: pafarley
ms.openlocfilehash: 7fc46d06b68dca074da060b4866186a6242ffad2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "72757380"
---
# <a name="content-moderator-api-reference"></a>Naslag informatie over Content Moderator-API

U kunt op de volgende manieren aan de slag met Azure Content Moderator-Api's:

- Abonneer u op de [Content moderator-API](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesContentModerator)In de Azure Portal.
- Zie [Content moderator op internet proberen](quick-start.md) om u aan te melden met het [hulp programma content moderator beoordeling](https://contentmoderator.cognitive.microsoft.com/).

## <a name="moderation-apis"></a>Beheer-API's

U kunt de volgende Content Moderator-Api's gebruiken voor het instellen van uw werk stromen na toezicht.

| Description | Referentie |
| -------------------- |-------------|
| **Afbeeldingstoezicht-API**<br /><br />Scan afbeeldingen en Detecteer potentiële inhoud voor volwassenen en ongepaste met behulp van tags, betrouwbaarheids scores en andere geëxtraheerde informatie. <br /><br />Gebruik deze informatie om de inhoud in uw werk stroom na toezicht te publiceren, af te wijzen of te controleren. <br /><br />| [API-verwijzing voor afbeeldings toezicht](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66c "API-verwijzing voor afbeeldings toezicht")   |
| **Teksttoezicht-API**<br /><br />Tekst inhoud scannen. Voor waarden voor scheld woorden en persoonlijke gegevens worden geretourneerd. <br /><br />Gebruik deze informatie om de inhoud in uw werk stroom na toezicht te publiceren, af te wijzen of te controleren.<br /><br /> | [Naslag informatie voor de API voor tekst toezicht](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66f "Naslag informatie voor de API voor tekst toezicht")   |
| **Video toezicht-API**<br /><br />Scan Video's en spoor potentiële inhoud voor volwassenen en ongepaste op. <br /><br />Gebruik deze informatie om de inhoud in uw werk stroom na toezicht te publiceren, af te wijzen of te controleren.<br /><br /> | [Video toezicht API-overzicht](video-moderation-api.md "Video toezicht API-overzicht")   |
| **Lijst beheer-API**<br /><br />Aangepaste uitsluitings-of insluitings lijsten met afbeeldingen en tekst maken en beheren. Als deze functie is ingeschakeld, worden de bewerkingen voor **afbeeldings overeenkomsten** en **tekst schermen** bij benadering vergeleken met de verzonden inhoud op basis van uw aangepaste lijsten. <br /><br />Voor efficiëntie kunt u de op machine learning gebaseerde toezicht stap overs Laan.<br /><br /> | [Naslag informatie voor List Management API](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f675 "Naslag informatie voor List Management API")   |

## <a name="review-apis"></a>Beoordelings-API's

De beoordelings-Api's hebben de volgende onderdelen:

| Description | Referentie |
| -------------------- |-------------|
| **Taken**<br /><br /> Scan-en revisie werk stromen initiëren voor zowel afbeeldings-als tekst inhoud. Een moderator taak scant uw inhoud met behulp van de afbeeldings toezicht-API en de tekst toezicht-API. Toezicht taken maken gebruik van de gedefinieerde en standaard werk stromen om beoordelingen te genereren. <br /><br />Nadat een Human toezicht houder de automatisch toegewezen tags en Voorspellings gegevens heeft gecontroleerd en een beslissing voor inhouds toezicht heeft ingediend, verzendt de beoordelings-API alle informatie naar uw API-eind punt.<br /><br /> | [Taak verwijzing](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c5 "Taak verwijzing")   |
| **Reviews**<br /><br />Gebruik het hulp programma voor controle om rechtstreeks afbeeldings-of tekst beoordelingen te maken voor menselijke moderators.<br /><br /> Nadat een Human toezicht houder de automatisch toegewezen tags en Voorspellings gegevens heeft gecontroleerd en een beslissing voor inhouds toezicht heeft ingediend, verzendt de beoordelings-API alle informatie naar uw API-eind punt.<br /><br /> | [Verwijzing controleren](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c4 "Verwijzing controleren")   |
| **Werkstromen**<br /><br />Maak, werk en ontvang Details over de aangepaste werk stromen die uw team maakt. U definieert werk stromen met behulp van het hulp programma beoordeling. <br /> <br />Werk stromen gebruiken meestal Content Moderator, maar kunnen ook bepaalde andere Api's gebruiken die beschikbaar zijn als connectors in het beoordelings programma.<br /><br /> | [Werk stroom referentie](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/5813b46b3f9b0711b43c4c59 "Werk stroom referentie")   |