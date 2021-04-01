---
title: Extensie-app in Azure Active Directory B2C | Microsoft Docs
description: De B2C-extensies-app worden hersteld.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/06/2017
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: dc536fa4292d794e8d89a2564ad10a3c10dd0a3d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "94560851"
---
# <a name="azure-ad-b2c-extensions-app"></a>Azure AD B2C: extensies-app

Wanneer een Azure AD B2C Directory wordt gemaakt, wordt er automatisch een app met `b2c-extensions-app. Do not modify. Used by AADB2C for storing user data.` de naam gemaakt in de nieuwe map. Deze app, waarnaar wordt verwezen als de **B2C-Extensions-app**, is zichtbaar in *app-registraties*. Deze wordt door de Azure AD B2C-service gebruikt om informatie over gebruikers en aangepaste kenmerken op te slaan. Als de app wordt verwijderd, functioneert Azure AD B2C niet op de juiste manier en wordt uw productieomgeving beïnvloed.

> [!IMPORTANT]
> Verwijder de B2C-uitbrei dingen-app alleen als u van plan bent om uw Tenant onmiddellijk te verwijderen. Als de app langer dan 30 dagen verwijderd blijft, zullen de gebruikers gegevens definitief verloren gaan.

## <a name="verifying-that-the-extensions-app-is-present"></a>Controleren of de App Extensions aanwezig is

Controleren of de B2C-extensies-app aanwezig is:

1. Klik in de Azure AD B2C-Tenant op **alle services** in het navigatie menu aan de linkerkant.
1. **App-registraties** zoeken en openen.
1. Zoeken naar een app die begint met **B2C-Extensions-app**

## <a name="recover-the-extensions-app"></a>De extensie-app herstellen

Als u de B2C-uitbrei dingen per ongeluk hebt verwijderd, hebt u 30 dagen om de app te herstellen. U kunt de app herstellen met behulp van de Graph API:

1. Blader naar [https://graphexplorer.azurewebsites.net/](https://graphexplorer.azurewebsites.net/).
1. Meld u aan bij de site als globale beheerder voor de Azure AD B2C directory waarvan u de verwijderde app wilt herstellen. Deze globale beheerder moet een e-mail adres hebben dat lijkt op het volgende: `username@{yourTenant}.onmicrosoft.com` .
1. Geef een HTTP-GET op voor de URL `https://graph.windows.net/myorganization/deletedApplications` met API-Version = 1.6. Met deze bewerking worden alle toepassingen weer geven die in de afgelopen 30 dagen zijn verwijderd.
1. Zoek de toepassing in de lijst waarin de naam begint met ' B2C-Extensions-app ' en kopieer de `objectid` eigenschaps waarde.
1. Geef een HTTP-POST op voor de URL `https://graph.windows.net/myorganization/deletedApplications/{OBJECTID}/restore` . Vervang het `{OBJECTID}` gedeelte van de URL door de `objectid` van de vorige stap.

U zou nu [de herstelde app kunnen zien](#verifying-that-the-extensions-app-is-present) in de Azure Portal.

> [!NOTE]
> Een toepassing kan alleen worden hersteld als deze in de afgelopen 30 dagen is verwijderd. Als er meer dan 30 dagen zijn, gaan de gegevens permanent verloren. Voor meer hulp moet u een ondersteunings ticket indienen.
