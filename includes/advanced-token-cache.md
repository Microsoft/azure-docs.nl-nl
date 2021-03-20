---
title: bestand opnemen
description: bestand opnemen
services: active-directory
author: kalyankrishna1
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: include
ms.date: 11/05/2020
ms.author: kkrishna
ms.openlocfilehash: 174946667885debc348370ef2c6f93206890e9c1
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94628001"
---
U kunt de implementatie van de token cache van MSAL gebruiken om achtergrond-apps, Api's en services toe te staan de toegangs token cache te gebruiken om namens gebruikers in hun afwezigheid te blijven handelen. Dit is vooral nuttig als de achtergrond-apps en-services moeten blijven werken namens de gebruiker nadat de gebruiker de front-end-web-app heeft afgesloten.

De meeste achtergrond processen gebruiken nu [toepassings machtigingen](/graph/auth/auth-concepts#microsoft-graph-permissions) wanneer ze met de gegevens van een gebruiker moeten werken zonder dat ze zich hoeven te verifiëren of opnieuw te verifiëren. Omdat voor toepassings machtigingen vaak een beheerders toestemming vereist is, waarvoor uitbrei ding van bevoegdheden nodig is, wordt onnodig frictie aangetroffen, omdat de ontwikkelaar geen toestemming heeft gekregen te verkrijgen dan de gebruiker die oorspronkelijk heeft gestemd met de app.

Dit code voorbeeld in GitHub laat zien hoe u deze niet-vereiste wrijving kunt voor komen door de token cache van MSAL te openen vanaf de achtergrond-apps:

 [De token cache van de aangemelde gebruiker openen vanaf de achtergrond-apps, Api's en services](https://github.com/Azure-Samples/ms-identity-dotnet-advanced-token-cache)