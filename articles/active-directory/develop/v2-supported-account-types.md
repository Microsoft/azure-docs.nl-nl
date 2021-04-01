---
title: Ondersteunde account typen | Azure
titleSuffix: Microsoft identity platform
description: Conceptuele documentatie over doel groepen en ondersteunde account typen in toepassingen
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/14/2020
ms.author: jmprieur
ms.reviewer: saeeda
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: d6c184e2983a072dec4b3021a1b58a61cd206dba
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98755994"
---
# <a name="supported-account-types"></a>Ondersteunde accounttypen

In dit artikel wordt uitgelegd welke typen accounts (ook wel *doel groepen* worden genoemd) worden ondersteund in de micro soft Identity platform-toepassingen.

<!-- This section can be in an include for many of the scenarios (SPA, web app signing-in users, protecting a web API, Desktop (depending on the flows), Mobile -->

## <a name="account-types-in-the-public-cloud"></a>Account typen in de open bare Cloud

In de Microsoft Azure open bare Cloud kunnen de meeste typen apps zich aanmelden met een wille keurige doel groep:

- Als u een LOB-toepassing (line-of-Business) schrijft, kunt u zich aanmelden bij gebruikers in uw eigen organisatie. Een dergelijke toepassing wordt ook wel *één Tenant* genoemd.
- Als u een ISV bent, kunt u een toepassing schrijven die zich aanmeldt bij gebruikers:

  - In elke organisatie. Een dergelijke toepassing wordt een *multi tenant* -webtoepassing genoemd. Soms leest u dat deze in gebruikers met hun werk-of school account wordt ondertekend.
  - Met hun werk-of school account of persoonlijke micro soft-accounts.
  - Alleen met persoonlijke micro soft-accounts.
    
- Als u een Business-to-consumer toepassing schrijft, kunt u zich ook aanmelden bij gebruikers met hun sociale identiteit, met behulp van Azure Active Directory B2C (Azure AD B2C).

## <a name="account-type-support-in-authentication-flows"></a>Ondersteuning voor account typen in verificatie stromen

Sommige account typen kunnen niet worden gebruikt met bepaalde verificatie stromen. Bijvoorbeeld in Desktop-, UWP-of daemon-toepassingen:

- Daemon-toepassingen kunnen alleen worden gebruikt met Azure AD-organisaties. Het is niet verstandig om daemon-toepassingen te gebruiken voor het bewerken van persoonlijke micro soft-accounts. De toestemming van de beheerder wordt nooit verleend.
- U kunt de geïntegreerde Windows-verificatie stroom alleen gebruiken met werk-of school accounts (in uw organisatie of een organisatie). Geïntegreerde Windows-verificatie werkt met domein accounts en de computers moeten lid zijn van een domein of lid zijn van Azure AD. Deze stroom is niet zinvol voor persoonlijke micro soft-accounts.
- De [referenties van het wacht woord](./v2-oauth-ropc.md) voor de resource-eigenaar (gebruikers naam en wacht woord) kunnen niet worden gebruikt met persoonlijke micro soft-accounts. Persoonlijke micro soft-accounts vereisen dat de gebruiker zich bij elke aanmeldings sessie voor toegang tot persoonlijke bronnen heeft verzonden. Daarom is dit gedrag niet compatibel met niet-interactieve stromen.

## <a name="account-types-in-national-clouds"></a>Account typen in nationale Clouds

Apps kunnen zich ook aanmelden bij gebruikers in [nationale Clouds](authentication-national-cloud.md). Persoonlijke micro soft-accounts worden echter niet ondersteund in deze Clouds. Daarom zijn de ondersteunde account typen voor deze Clouds, naar uw organisatie (één Tenant) of voor alle organisaties (multi tenant-toepassingen), gereduceerd.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [pacht in azure Active Directory](./single-and-multi-tenant-apps.md).
- Meer informatie over [nationale Clouds](./authentication-national-cloud.md).
