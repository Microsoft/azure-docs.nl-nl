---
title: Azure AD-toestemmings raamwerk
titleSuffix: Microsoft identity platform
description: Meer informatie over het toestemming raamwerk in Azure Active Directory en hoe u hiermee eenvoudig multi tenant-webtoepassingen en native client toepassingen kunt ontwikkelen.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/21/2020
ms.author: ryanwi
ms.reviewer: zachowd, lenalepa, jesakowi
ms.openlocfilehash: 74321bc75fa760727e7896f47cdfc5b2929047e5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92366085"
---
# <a name="azure-active-directory-consent-framework"></a>Raamwerk Azure Active Directory-toestemming

Met het toestemming raamwerk van Azure Active Directory (Azure AD) kunt u eenvoudig multi tenant-webtoepassingen en native client toepassingen ontwikkelen. Met deze toepassingen kunt u zich aanmelden via gebruikers accounts van een Azure AD-Tenant die afwijkt van het item waarin de toepassing is geregistreerd. Daarnaast moeten ze ook toegang hebben tot Web-Api's, zoals de Microsoft Graph-API (om toegang te krijgen tot Azure AD, intune en services in Microsoft 365) en andere Api's van micro soft-Services, naast uw eigen web-Api's.

Het Framework is gebaseerd op een gebruiker of een beheerder die toestemming geeft voor een toepassing die wordt gevraagd om te worden geregistreerd in hun Directory, die mogelijk toegang tot Directory gegevens kan hebben. Als een webclient toepassing bijvoorbeeld agenda gegevens van Microsoft 365 moet lezen, moet die gebruiker eerst toestemming geven voor de client toepassing. Nadat toestemming is gegeven, kan de client toepassing de Microsoft Graph-API namens de gebruiker aanroepen en de agenda gegevens naar behoefte gebruiken. De [Microsoft Graph-API](https://developer.microsoft.com/graph) biedt toegang tot gegevens in Microsoft 365 (zoals agenda's en berichten van Exchange, sites en lijsten vanuit share point, documenten uit OneDrive, notitie blokken van OneNote, taken uit planner en werkmappen vanuit Excel), evenals gebruikers en groepen uit Azure AD en andere gegevens objecten uit meer micro soft-Cloud Services.

Het toestemming raamwerk is gebaseerd op OAuth 2,0 en de verschillende stromen, zoals autorisatie code toekenning en client referenties verlenen, met behulp van open bare of vertrouwelijke clients. Met behulp van OAuth 2,0 kunt u met Azure AD veel verschillende soorten client toepassingen bouwen, zoals op een telefoon, Tablet, server of webtoepassing, en toegang krijgen tot de vereiste bronnen.

Zie [toegang tot webtoepassingen met oauth 2,0-en Azure AD](v2-oauth2-auth-code-flow.md) -en [verificatie SCENARIO'S voor Azure AD](./authentication-vs-authorization.md)machtigen voor meer informatie over het gebruik van het toestemming raamwerk met OAuth 2.0-autorisatie subsidies. Zie [app-verificatie met Microsoft Graph](/graph/)voor informatie over het verkrijgen van geautoriseerde toegang tot Microsoft 365 via Microsoft Graph.

## <a name="consent-experience---an-example"></a>Bestemmings ervaring-een voor beeld

In de volgende stappen ziet u hoe de bestemmings ervaring werkt voor zowel de ontwikkelaar van de toepassing als de gebruiker.

1. Stel dat u een webclient-toepassing hebt waarvoor specifieke machtigingen moeten worden aangevraagd om toegang te krijgen tot een resource/API. In de volgende sectie leert u hoe u deze configuratie kunt uitvoeren, maar in feite wordt de Azure Portal gebruikt voor het declareren van machtigings aanvragen op de configuratie tijd. Net als andere configuratie-instellingen worden ze onderdeel van de Azure AD-registratie van de toepassing:

    ![Machtigingen voor andere toepassingen](./media/consent-framework/permissions.png)

1. Houd er rekening mee dat de machtigingen van uw toepassing zijn bijgewerkt, dat de toepassing wordt uitgevoerd en dat een gebruiker deze voor de eerste keer gebruikt. Ten eerste moet de toepassing een autorisatie code verkrijgen van het eind punt van Azure AD `/authorize` . De autorisatiecode kan vervolgens worden gebruikt bij het verkrijgen van een nieuw token voor toegang en vernieuwen.

1. Als de gebruiker nog niet is geverifieerd, wordt de gebruiker door het eind punt van Azure AD `/authorize` gevraagd zich aan te melden.

    ![Gebruiker of beheerder meldt zich aan bij Azure AD](./media/consent-framework/usersignin.png)

1. Nadat de gebruiker zich heeft aangemeld, wordt door Azure AD bepaald of de gebruiker een toestemming pagina moet worden weer gegeven. Hierbij wordt gecontroleerd of de gebruiker (of de beheerder in de organisatie) de toepassing al toestemming heeft gegeven. Als er nog geen toestemming is verleend, wordt de gebruiker door Azure AD om toestemming gevraagd en worden de vereiste machtigingen weer gegeven die moeten worden gebruikt. De set machtigingen die worden weer gegeven in het dialoog venster voor toestemming, komt overeen met de opties die zijn geselecteerd in de **gedelegeerde machtigingen** in de Azure Portal.

    ![Toont een voor beeld van machtigingen die worden weer gegeven in het dialoog venster voor toestemming](./media/consent-framework/consent.png)

1. Nadat de gebruiker toestemming verleent, wordt er een autorisatie code geretourneerd naar uw toepassing, die wordt ingewisseld om een toegangs token en een vernieuwings token te verkrijgen. Zie [OAuth 2,0 Authorization code flow](v2-oauth2-auth-code-flow.md)(Engelstalig) voor meer informatie over deze stroom.

1. Beheerders kunnen toestemming geven voor de gedelegeerde machtigingen van een toepassing voor alle gebruikers in de tenant. Toestemming van de beheerder voor komt dat het dialoog venster voor toestemming wordt weer gegeven voor elke gebruiker in de Tenant en kan worden uitgevoerd in de [Azure Portal](https://portal.azure.com) door gebruikers met de rol Administrator. Zie [Administrator role permissions in azure AD](../roles/permissions-reference.md)(Engelstalig) voor meer informatie over welke beheerders rollen toestemming kunnen geven voor gedelegeerde machtigingen.

    **Toestemming geven aan de gedelegeerde machtigingen van een app**

   1. Ga naar de pagina **API-machtigingen** voor uw toepassing
   1. Klik op de knop **toestemming beheerder verlenen** .

      ![Machtigingen verlenen voor expliciete beheerders toestemming](./media/consent-framework/grant-consent.png)

   > [!IMPORTANT]
   > Het verlenen van expliciete toestemming met de knop **machtigingen verlenen** is momenteel vereist voor toepassingen met één pagina (Spa) die gebruikmaken van ADAL.js. Als dit niet gebeurt, treedt er een fout op in de toepassing wanneer het toegangstoken wordt aangevraagd.

## <a name="next-steps"></a>Volgende stappen

Zie [een app converteren naar multi tenant](howto-convert-app-to-be-multi-tenant.md)
