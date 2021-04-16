---
title: Microsoft Graph API
description: De Microsoft Graph API is een RESTful-web-API waarmee u toegang krijgt tot Microsoft Cloud-servicebronnen.
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
ms.openlocfilehash: 2e689e620a5aeb7c5028f1a1b30dd6def8e447ab
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107529987"
---
# <a name="microsoft-graph-api"></a>Microsoft Graph API

De Microsoft Graph API is een RESTful-web-API waarmee u toegang krijgt tot Microsoft Cloud-servicebronnen. Nadat u uw app hebt geregistreerd en verificatietokens voor een gebruiker of service hebt ontvangen, kunt u aanvragen indienen bij de Microsoft Graph API. Zie Overzicht van Microsoft Graph voor [meer informatie.](/graph/overview)

Microsoft Graph stelt REST API's en clientbibliotheken beschikbaar voor toegang tot gegevens op de volgende Microsoft 365 services:
- Microsoft 365 services: Delve, Excel, Microsoft Bookings, Microsoft Teams, OneDrive, OneNote, Outlook/Exchange, Planner en SharePoint
- Enterprise Mobility and Security-services: Advanced Threat Analytics, Advanced Threat Protection, Azure Active Directory, Identity Manager en Intune
- Windows 10 services: activiteiten, apparaten, meldingen
- Dynamics 365 Business Central

## <a name="versions"></a>Versies

Microsoft Graph ondersteunt momenteel twee versies: v1.0 en bètaversies. De versie v1.0 bevat algemeen beschikbare API's. Gebruik de versie v1.0 voor alle productie-apps. De bètaversie bevat API's die momenteel in preview zijn. Omdat we belangrijke wijzigingen kunnen introduceren in onze bèta-API's, raden we u aan de bètaversie alleen te gebruiken om apps te testen die in ontwikkeling zijn; gebruik geen bèta-API's in uw productie-apps. Zie Voor meer informatie [Versiebeleid, ondersteuning en](/graph/versioning-and-support)wijzigingsbeleid voor het Microsoft Graph.

Als u de bèta-API's wilt gaan gebruiken, zie [Microsoft Graph bèta-eindpuntreferentie](/graph/api/overview?view=graph-rest-beta&preserve-view=true)

Als u de v1.0-API's wilt gaan gebruiken, Microsoft Graph REST API [v1.0-referentie](/graph/api/overview?view=graph-rest-1.0&preserve-view=true)

## <a name="get-started"></a>Aan de slag

Als u wilt lezen van of schrijven naar een resource, zoals een gebruiker of een e-mailbericht, maakt u een aanvraag die er als volgt uitziet:

`{HTTP method} https://graph.microsoft.com/{version}/{resource}?{query-parameters}`

Zie Use the Microsoft Graph API (De api voor de Microsoft Graph gebruiken) voor meer informatie [Microsoft Graph de samengestelde aanvraag](/graph/use-the-api)

Quickstart-voorbeelden zijn beschikbaar om u te laten zien hoe u toegang krijgt tot de kracht van Microsoft Graph API. De voorbeelden die beschikbaar zijn, hebben toegang tot twee services met één verificatie: Microsoft-account en Outlook. Elke quickstart heeft toegang tot informatie Microsoft-account profielen van gebruikers en geeft gebeurtenissen uit hun agenda weer.
De quickstarts bestaan uit vier stappen:
- Uw platform selecteren
- Uw app-id (client-id) op te halen
- Het voorbeeld bouwen
- Aanmelden en gebeurtenissen in uw agenda weergeven

Wanneer u de quickstart hebt voltooid, hebt u een app die gereed is om te worden uitgevoerd. Zie de veelgestelde vragen over de [snelstart Microsoft Graph voor meer informatie.](/graph/quick-start-faq) Zie snelstart om aan de slag [te Microsoft Graph met de voorbeelden.](https://developer.microsoft.com/graph/quick-start)

## <a name="tools"></a>Hulpprogramma's

Microsoft Graph Explorer is een webhulpprogramma dat u kunt gebruiken om aanvragen te bouwen en te testen met behulp Microsoft Graph API's. U hebt toegang tot Microsoft Graph Explorer op: `https://developer.microsoft.com/graph/graph-explorer` .

Postman is een hulpprogramma dat u ook kunt gebruiken om aanvragen te bouwen en te testen met behulp van Microsoft Graph API's. U kunt Postman downloaden op: `https://www.getpostman.com/` . Voor interactie met Microsoft Graph in Postman gebruikt u de verzameling Microsoft Graph in Postman. Zie Postman gebruiken met [de api Microsoft Graph voor meer informatie.](/graph/use-postman)
