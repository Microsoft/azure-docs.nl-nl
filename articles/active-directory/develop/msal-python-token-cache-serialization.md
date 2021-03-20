---
title: Aangepaste token cache-serialisatie (MSAL python) | Azure
titleSuffix: Microsoft identity platform
description: Meer informatie over het serialiseren van de token cache voor MSAL voor python
services: active-directory
author: rayluo
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 11/13/2019
ms.author: rayluo
ms.reviewer: nacanuma
ms.custom: aaddev, devx-track-python
ms.openlocfilehash: 7aaf59377949101ba8dd5c9454b89229e925e859
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "87846187"
---
# <a name="custom-token-cache-serialization-in-msal-for-python"></a>Serialisatie van aangepaste token cache in MSAL voor python

In MSAL python wordt een token cache in het geheugen die persistent is voor de duur van de app-sessie, standaard gegeven wanneer u een instantie van [ClientApplication](https://msal-python.readthedocs.io/en/latest/#confidentialclientapplication)maakt.

Serialisatie van de token cache, zodat verschillende sessies van uw app toegang hebben, is niet in het vak opgenomen. Dat komt doordat MSAL python kan worden gebruikt in app-typen die geen toegang hebben tot het bestands systeem, zoals web apps. Als u een permanente token cache wilt hebben in een MSAL python-app, moet u aangepaste serialisatie van de token cache opgeven.

De strategieën voor het serialiseren van de token cache variëren, afhankelijk van of u een open bare client toepassing (Desktop) schrijft of een vertrouwelijke client toepassing (Web-app, Web-API of daemon-app).

## <a name="token-cache-for-a-public-client-application"></a>Token cache voor een open bare client toepassing

Open bare client toepassingen worden uitgevoerd op het apparaat van een gebruiker en voor het beheren van tokens voor één gebruiker. In dit geval kunt u de volledige cache serialiseren in een bestand. Vergeet niet om bestands vergrendeling te bieden als uw app of een andere app gelijktijdig toegang heeft tot de cache. Zie het voor beeld in de [SerializableTokenCache](https://msal-python.readthedocs.io/en/latest/#msal.SerializableTokenCache) -referentie documentatie voor een eenvoudig voor beeld van het serialiseren van een token cache naar een bestand zonder te vergren delen.

## <a name="token-cache-for-a-web-app-confidential-client-application"></a>Token cache voor een web-app (vertrouwelijke client toepassing)

Voor web-apps of Web-Api's kunt u de sessie of een redis-cache of een Data Base gebruiken om de token cache op te slaan. Er moet één token cache per gebruiker (per account) zijn, dus zorg ervoor dat u de token cache per account serialiseren.

## <a name="next-steps"></a>Volgende stappen

Zie [MS-Identity-python-webapp](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/master/app.py#L64-L72) voor een voor beeld van het gebruik van de token cache voor een Windows-of Linux-web-app of Web-API. Het voor beeld is voor een web-app die de Microsoft Graph-API aanroept.
