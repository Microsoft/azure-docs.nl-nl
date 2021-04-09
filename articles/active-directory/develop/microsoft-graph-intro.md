---
title: Microsoft Graph API
description: De Microsoft Graph-API is een REST Web-API waarmee u toegang hebt tot Microsoft Cloud-service bronnen.
author: davidmu1
services: active-directory
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 02/13/2020
ms.author: davidmu
ms.custom: aaddev
ms.openlocfilehash: a799d7c32ad632303931adf838ca8e2e715b2adc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96860181"
---
# <a name="microsoft-graph-api"></a>Microsoft Graph API

De Microsoft Graph-API is een REST Web-API waarmee u toegang hebt tot Microsoft Cloud-service bronnen. Nadat u uw app hebt geregistreerd en verificatie tokens voor een gebruiker of service hebt opgehaald, kunt u aanvragen indienen bij de Microsoft Graph-API. Zie [overzicht van Microsoft Graph](/graph/overview)voor meer informatie.

Microsoft Graph maakt REST-Api's en client bibliotheken beschikbaar voor toegang tot gegevens op de volgende Microsoft 365-Services:
- Microsoft 365 Services: Delve, Excel, micro soft-boekingen, micro soft teams, OneDrive, OneNote, Outlook/Exchange, planner en share point
- Enter prise Mobility and Security Services: Advanced Threat Analytics, Advanced Threat Protection, Azure Active Directory, Identity Manager en intune
- Windows 10-Services: activiteiten, apparaten, meldingen
- Dynamics 365 Business Central

## <a name="versions"></a>Versies

Microsoft Graph ondersteunt momenteel twee versies: v 1.0 en bèta. De versie v 1.0 bevat algemeen beschik bare Api's. De v 1.0-versie gebruiken voor alle productie-apps. De bèta versie bevat Api's die momenteel als preview-versie beschikbaar zijn. We raden u aan de bèta versie alleen te gebruiken voor het testen van apps die in ontwikkeling zijn. Gebruik geen bèta-Api's in uw productie-apps. Zie voor meer informatie [versie beheer, ondersteuning en beleid voor het wijzigen van wijzigingen voor Microsoft Graph](/graph/versioning-and-support).

Zie [Microsoft Graph bèta-eindpunt referentie](/graph/api/overview?view=graph-rest-beta) voor meer informatie over het gebruik van de bèta-api's.

Zie [Microsoft Graph referentie rest API v 1.0](/graph/api/overview) als u de api's van v 1.0 wilt gaan gebruiken

## <a name="get-started"></a>Aan de slag

Als u wilt lezen uit of schrijven naar een resource zoals een gebruiker of een e-mail bericht, maakt u een aanvraag die er als volgt uitziet:

`{HTTP method} https://graph.microsoft.com/{version}/{resource}?{query-parameters}`

Zie [de Microsoft Graph-API gebruiken](/graph/use-the-api) voor meer informatie over de elementen van de geconstrueerde aanvraag

Quick start-voor beelden zijn beschikbaar om u te laten zien hoe u toegang krijgt tot de kracht van de Microsoft Graph-API. De voor beelden die beschikbaar zijn, hebben toegang tot twee services met één verificatie: Microsoft-account en Outlook. Met elke Snelstartgids krijgt u toegang tot gegevens uit de profielen van Microsoft-account gebruikers en worden gebeurtenissen uit hun agenda weer gegeven.
De Quick starts bestaan uit vier stappen:
- Uw platform selecteren
- Uw app-ID ophalen (client-ID)
- Het voor beeld maken
- Meld u aan en Bekijk gebeurtenissen in uw agenda

Wanneer u de Snelstartgids hebt voltooid, hebt u een app die kan worden uitgevoerd. Zie de [Veelgestelde vragen over Microsoft Graph Quick](/graph/quick-start-faq)start voor meer informatie. Zie [Microsoft Graph Snelstartgids](https://developer.microsoft.com/graph/quick-start)om aan de slag te gaan met de voor beelden.

## <a name="tools"></a>Hulpprogramma's

Microsoft Graph Explorer is een webgebaseerd hulp programma dat u kunt gebruiken om aanvragen te bouwen en te testen met behulp van Microsoft Graph-Api's. U hebt toegang tot Microsoft Graph Explorer op: `https://developer.microsoft.com/graph/graph-explorer` .

Postman is een hulp programma dat u ook kunt gebruiken om aanvragen te maken en te testen met behulp van de Microsoft Graph-Api's. U kunt een bericht downloaden naar: `https://www.getpostman.com/` . Als u wilt werken met Microsoft Graph in Postman, gebruikt u de Microsoft Graph verzameling in postman. Zie voor meer informatie [postman gebruiken met de Microsoft Graph-API](/graph/use-postman?context=graph%2Fapi%2Fbeta&view=graph-rest-beta).
