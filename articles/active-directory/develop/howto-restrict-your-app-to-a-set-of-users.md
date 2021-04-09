---
title: Azure AD-App beperken tot een set gebruikers | Azure
titleSuffix: Microsoft identity platform
description: Meer informatie over het beperken van de toegang tot uw apps die zijn geregistreerd in azure AD voor een geselecteerde groep gebruikers.
services: active-directory
author: kalyankrishna1
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: how-to
ms.date: 09/24/2018
ms.author: kkrishna
ms.reviewer: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 71cfe08da42b8eec9ddbd0e4246ba1b72f895414
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "103199588"
---
# <a name="how-to-restrict-your-azure-ad-app-to-a-set-of-users-in-an-azure-ad-tenant"></a>Procedure: uw Azure AD-App beperken tot een set gebruikers in een Azure AD-Tenant

Toepassingen die zijn geregistreerd in een Azure Active Directory-Tenant (Azure AD), zijn standaard beschikbaar voor alle gebruikers van de Tenant die zijn geverifieerd.

In het geval van een [multi tenant](howto-convert-app-to-be-multi-tenant.md) -app kunnen alle gebruikers in de Azure AD-Tenant waar deze app is ingericht, toegang krijgen tot deze toepassing zodra ze zijn geverifieerd in hun respectieve Tenant.

Tenant beheerders en ontwikkel aars hebben vaak vereisten waarbij een app moet worden beperkt tot een bepaalde groep gebruikers. Ontwikkel aars kunnen hetzelfde doen met behulp van populaire verificatie patronen zoals Azure RBAC (op rollen gebaseerd toegangs beheer), maar deze benadering vereist een aanzienlijke hoeveelheid werk voor een deel van de ontwikkelaar.

Tenant beheerders en ontwikkel aars kunnen een app beperken tot een specifieke set gebruikers of beveiligings groepen in de Tenant door deze ingebouwde functie van Azure AD ook te gebruiken.

## <a name="supported-app-configurations"></a>Ondersteunde app-configuraties

De optie om een app te beperken tot een specifieke set gebruikers of beveiligings groepen in een Tenant werkt met de volgende typen toepassingen:

- Toepassingen die zijn geconfigureerd voor federatieve eenmalige aanmelding met op SAML gebaseerde verificatie.
- Toepassings proxy toepassingen die gebruikmaken van pre-authenticatie van Azure AD.
- Toepassingen die rechtstreeks zijn gebouwd op het Azure AD-toepassings platform die gebruikmaken van OAuth 2.0/OpenID Connect Connect-verificatie nadat een gebruiker of beheerder heeft ingestemd met die toepassing.

     > [!NOTE]
     > Deze functie is alleen beschikbaar voor web-app/Web-API en bedrijfs toepassingen. Apps die zijn geregistreerd als [systeem eigen](./quickstart-register-app.md) , kunnen niet worden beperkt tot een set gebruikers of beveiligings groepen in de Tenant.

## <a name="update-the-app-to-enable-user-assignment"></a>De app bijwerken om de gebruikers toewijzing in te scha kelen

Er zijn twee manieren om een toepassing met ingeschakelde gebruikers toewijzing te maken. Voor één is de rol **globale beheerder** vereist, de tweede niet.

### <a name="enterprise-applications-requires-the-global-administrator-role"></a>Bedrijfs toepassingen (vereist de rol globale beheerder)

1. Meld u aan bij de <a href="https://portal.azure.com/" target="_blank">Azure Portal</a> als **globale beheerder**.
1. Als u toegang hebt tot meerdere tenants, gebruikt u het filter **Directory + abonnement** :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: in het bovenste menu om de tenant te selecteren waarin u een toepassing wilt registreren.
1. Zoek en selecteer de optie **Azure Active Directory**.
1. Selecteer onder **beheren** de optie **bedrijfs toepassingen**  >  **alle toepassingen**.
1. Selecteer in de lijst de toepassing waaraan u een gebruiker of een beveiligings groep wilt toewijzen. 
    Gebruik de filters bovenaan het venster om te zoeken naar een specifieke toepassing.
1. Op de **overzichts** pagina van de toepassing, onder **beheren**, selecteert u **Eigenschappen**.
1. Zoek de instelling **gebruikers toewijzing vereist?** en stel deze in op **Ja**. Als deze optie is ingesteld op **Ja**, moeten gebruikers in de Tenant eerst worden toegewezen aan deze toepassing of kunnen ze zich niet aanmelden bij deze toepassing.
1. Selecteer **Opslaan**.

### <a name="app-registration"></a>App-registratie

1. Meld u aan bij de <a href="https://portal.azure.com/" target="_blank">Azure-portal</a>.
1. Als u toegang hebt tot meerdere tenants, gebruikt u het filter **Directory + abonnement** :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: in het bovenste menu om de tenant te selecteren waarin u een toepassing wilt registreren.
1. Zoek en selecteer de optie **Azure Active Directory**.
1. Selecteer **App-registraties** onder **Beheren**.
1. Maak of selecteer de app die u wilt beheren. U moet de **eigenaar** van deze toepassing zijn.
1. Selecteer op de **overzichts** pagina van de toepassing de koppeling **beheerde toepassing in lokale map** in de sectie **Essentials** .
1. Selecteer **Eigenschappen** onder **Beheren**.
1. Zoek de instelling **gebruikers toewijzing vereist?** en stel deze in op **Ja**. Als deze optie is ingesteld op **Ja**, moeten gebruikers in de Tenant eerst worden toegewezen aan deze toepassing of kunnen ze zich niet aanmelden bij deze toepassing.
1. Selecteer **Opslaan**.

## <a name="assign-users-and-groups-to-the-app"></a>Gebruikers en groepen toewijzen aan de app

Zodra u uw app hebt geconfigureerd om gebruikers toewijzing in te scha kelen, kunt u gebruikers en groepen toewijzen aan de app.

1. Onder **beheren** selecteert u de **gebruikers en groepen**  >  **Toevoegen gebruiker/groep** .
1. Selecteer de **gebruikers** kiezer. 

     Er wordt een lijst met gebruikers en beveiligings groepen weer gegeven samen met een tekstvak om te zoeken en een bepaalde gebruiker of groep te zoeken. In dit scherm kunt u meerdere gebruikers en groepen tegelijk selecteren.

1. Wanneer u klaar bent met het selecteren van de gebruikers en groepen, selecteert u **selecteren**.
1. Beschrijving Als u app-functies hebt gedefinieerd in uw toepassing, kunt u de optie **rol selecteren** gebruiken om de geselecteerde gebruikers en groepen toe te wijzen aan een van de rollen van de toepassing. 
1. Selecteer **toewijzen** om de toewijzingen van gebruikers en groepen aan de app te volt ooien. 
1. Controleer of de gebruikers en groepen die u hebt toegevoegd, worden weer gegeven in de lijst met bijgewerkte **gebruikers en groepen** .

## <a name="more-information"></a>Meer informatie

- [Procedure: app-functies toevoegen in uw toepassing](./howto-add-app-roles-in-azure-ad-apps.md)
- [Autorisatie toevoegen met behulp van app-rollen & rollen claims aan een ASP.NET Core-web-app](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/5-WebApp-AuthZ/5-1-Roles)
- [Beveiligings groepen en toepassings rollen gebruiken in uw apps (video)](https://www.youtube.com/watch?v=LRoc-na27l0)
- [Azure Active Directory, nu met groeps claims en toepassings rollen](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-Active-Directory-now-with-Group-Claims-and-Application/ba-p/243862)
- [Azure Active Directory-app-manifest](./reference-app-manifest.md)
