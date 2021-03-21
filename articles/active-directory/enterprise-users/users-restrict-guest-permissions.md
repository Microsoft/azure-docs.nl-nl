---
title: Toegangs machtigingen voor gast gebruikers beperken-Azure Active Directory | Microsoft Docs
description: Toegangs machtigingen voor gast gebruikers beperken met behulp van de Azure Portal, Power shell of Microsoft Graph in Azure Active Directory
services: active-directory
author: curtand
ms.author: curtand
manager: daveba
ms.date: 01/14/2020
ms.topic: how-to
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
ms.custom: it-pro
ms.reviewer: krbain
ms.collection: M365-identity-device-management
ms.openlocfilehash: bf2d0d3335468147575eb53a99940866baa18375
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98222518"
---
# <a name="restrict-guest-access-permissions-preview-in-azure-active-directory"></a>Machtigingen voor gast toegang (preview) beperken in Azure Active Directory

Met Azure Active Directory (Azure AD) kunt u beperken wat externe gast gebruikers in hun organisatie kunnen zien in azure AD. Gast gebruikers worden standaard ingesteld op een beperkt machtigings niveau in azure AD, terwijl de standaard instelling is de volledige set standaard gebruikers machtigingen. Dit is een voor beeld van een nieuw machtigings niveau voor gast gebruikers in de externe samenwerkings instellingen van uw Azure AD-organisatie voor nog meer beperkte toegang, zodat uw opties voor gast toegang nu zijn:

Machtigingsniveau         | Toegangsniveau
----------------         | ------------
Hetzelfde als gebruikers van leden     | Gasten hebben dezelfde toegang tot Azure AD-resources als gebruikers van een gebruiker
Beperkte toegang (standaard) | Gasten kunnen lidmaatschap van alle niet-verborgen groepen zien
**Beperkte toegang (nieuw)**  | **Gasten kunnen lidmaatschap van groepen niet zien**

Als gast toegang is beperkt, kunnen gasten alleen hun eigen gebruikers profiel bekijken. Machtiging voor het weer geven van andere gebruikers is niet toegestaan, zelfs niet als de gast zoekt op User Principal name of objectId. Met beperkte toegang kunnen gast gebruikers ook het lidmaatschap van groepen weer geven waarin ze zich bevinden. Zie [Wat zijn de standaard machtigingen voor gebruikers in azure Active Directory?](../fundamentals/users-default-permissions.md)voor meer informatie over de algemene standaard machtigingen voor gebruikers, inclusief gast gebruikers machtigingen.

## <a name="permissions-and-licenses"></a>Machtigingen en licenties

U moet de rol globale beheerder hebben om de instellingen voor externe samen werking te kunnen configureren. Er zijn geen aanvullende licentie vereisten om gast toegang te beperken.

## <a name="update-in-the-azure-portal"></a>Bijwerken in de Azure Portal

Er zijn wijzigingen aangebracht in de bestaande Azure Portal-besturings elementen voor gast gebruikers machtigingen.

1. Meld u aan bij het [Azure AD-beheer centrum](https://aad.portal.azure.com) met globale beheerders machtigingen.
1. Selecteer op de pagina **Azure Active Directory** overzicht voor uw organisatie de optie **gebruikers instellingen**.
1. Onder **externe gebruikers** selecteert u **externe instellingen voor samen werking beheren**.
1. Selecteer op de pagina **externe samenwerkings instellingen** de optie **gast gebruikers toegang is beperkt tot de eigenschappen en lidmaatschappen van hun eigen Directory-objecten** .

    ![Pagina instellingen voor externe samen werking van Azure AD](./media/users-restrict-guest-permissions/external-collaboration-settings.png)

1. Selecteer **Opslaan**. Het kan tot vijf tien minuten duren voordat de wijzigingen zijn doorgevoerd voor gast gebruikers.

## <a name="update-with-the-microsoft-graph-api"></a>Bijwerken met de Microsoft Graph-API

We hebben een nieuwe Microsoft Graph-API toegevoegd om gast machtigingen in uw Azure AD-organisatie te configureren. De volgende API-aanroepen kunnen worden uitgevoerd om een machtigings niveau toe te wijzen. De waarde voor guestUserRoleId die hier wordt gebruikt, is bedoeld ter illustratie van de instelling voor de meeste beperkte gast gebruikers. Zie [authorizationPolicy resource type](/graph/api/resources/authorizationpolicy)voor meer informatie over het gebruik van de Microsoft Graph om gast machtigingen in te stellen.

### <a name="configuring-for-the-first-time"></a>Voor de eerste keer configureren

````PowerShell
POST https://graph.microsoft.com/beta/policies/authorizationPolicy/authorizationPolicy

{
  "guestUserRoleId": "2af84b1e-32c8-42b7-82bc-daa82404023b"
}
````

Het antwoord moet slagen 204.

### <a name="updating-the-existing-value"></a>De bestaande waarde bijwerken

````PowerShell
PATCH https://graph.microsoft.com/beta/policies/authorizationPolicy/authorizationPolicy

{
  "guestUserRoleId": "2af84b1e-32c8-42b7-82bc-daa82404023b"
}
````

Het antwoord moet slagen 204.

### <a name="view-the-current-value"></a>De huidige waarde weer geven

````PowerShell
GET https://graph.microsoft.com/beta/policies/authorizationPolicy/authorizationPolicy
````

Voorbeeld van een reactie:

````PowerShell
{
    "@odata.context": "https://graph.microsoft.com/beta/$metadata#policies/authorizationPolicy/$entity",
    "id": "authorizationPolicy",
    "displayName": "Authorization Policy",
    "description": "Used to manage authorization related settings across the company.",
    "enabledPreviewFeatures": [],
    "guestUserRoleId": "10dae51f-b6af-4016-8d66-8c2a99b929b3",
    "permissionGrantPolicyIdsAssignedToDefaultUserRole": [
        "user-default-legacy"
    ]
}
````

## <a name="update-with-powershell-cmdlets"></a>Bijwerken met Power shell-cmdlets

Met deze functie hebben we de mogelijkheid voor het configureren van de beperkte machtigingen via Power shell v2-cmdlets toegevoegd. Power shell-cmdlets ophalen en instellen zijn gepubliceerd in versie 2.0.2.85.

### <a name="get-command-get-azureadmsauthorizationpolicy"></a>Get-opdracht: Get-AzureADMSAuthorizationPolicy

Voorbeeld:

````PowerShell
PS C:\WINDOWS\system32> Get-AzureADMSAuthorizationPolicy

Id                                                : authorizationPolicy
OdataType                                         :
Description                                       : Used to manage authorization related settings across the company.
DisplayName                                       : Authorization Policy
EnabledPreviewFeatures                            : {}
GuestUserRoleId                                   : 10dae51f-b6af-4016-8d66-8c2a99b929b3
PermissionGrantPolicyIdsAssignedToDefaultUserRole : {user-default-legacy}
````

### <a name="set-command-set-azureadmsauthorizationpolicy"></a>Stel opdracht: Set-AzureADMSAuthorizationPolicy

Voorbeeld:

````PowerShell
PS C:\WINDOWS\system32> Set-AzureADMSAuthorizationPolicy -GuestUserRoleId '2af84b1e-32c8-42b7-82bc-daa82404023b'
````

> [!NOTE]
> U moet authorizationPolicy als de ID opgeven wanneer u hierom wordt gevraagd.

## <a name="supported-microsoft-365-services"></a>Ondersteunde Microsoft 365 Services

### <a name="supported-services"></a>Ondersteunde services

Op basis van ondersteund wordt de ervaring als verwacht beschouwd. Dit is met name hetzelfde als de huidige gast ervaring.

- Teams
- Outlook (OWA)
- SharePoint
- Planner in teams
- Planner-web-app

### <a name="services-currently-not-supported"></a>Services worden momenteel niet ondersteund

Service zonder huidige ondersteuning heeft mogelijk compatibiliteits problemen met de instelling voor nieuwe gast beperkingen.

- Formulieren
- Mobiele app voor planner
- Project
- Yammer

## <a name="frequently-asked-questions-faq"></a>Veelgestelde vragen

Vraag | Antwoord
-------- | ------
Waar zijn deze machtigingen van toepassing? | Deze machtigingen op mapniveau worden afgedwongen voor Azure AD-Services en-portals, met inbegrip van de Microsoft Graph, Power shell v2, de Azure Portal en mijn apps-Portal. Microsoft 365 services die gebruikmaken van Microsoft 365 groepen voor samenwerkings scenario's, worden ook beïnvloed, met name Outlook, micro soft teams en share point.
Wat is de invloed van beperkte machtigingen op welke groepen gasten kunnen worden weer geven? | Gasten kunnen de lijst met groepen of gebruikers niet opsommen, ongeacht de standaard of beperkte machtigingen voor gasten. Gasten kunnen groepen weer geven waarvan ze lid zijn in zowel de Azure Portal als de portal mijn apps, afhankelijk van de machtigingen:<li>**Standaard machtigingen**: als u wilt zoeken naar de groepen waarvan ze lid zijn in de Azure Portal, moet de gast in de lijst **alle gebruikers** zoeken naar hun object-id en vervolgens **groepen** selecteren. Hier ziet u de lijst met groepen waarvan deze deel uitmaken, met inbegrip van alle groeps gegevens, met inbegrip van de naam, het e-mail adres, enzovoort. In de portal mijn apps kunnen ze een lijst zien van de groepen waarvan ze zijn en de groepen waarvan ze lid zijn.</li><li>**Beperkte machtigingen voor gasten**: in de Azure portal kunnen ze nog steeds de lijst met groepen waarvan ze deel uitmaken, vinden door te zoeken naar hun object-id in de lijst alle gebruikers en vervolgens groepen te selecteren. Ze kunnen alleen zeer beperkte Details over de groep weer geven, met name de object-ID. De kolommen naam en e-mail adres zijn leeg en het groeps type wordt niet herkend. In de portal mijn apps hebben ze geen toegang tot de lijst met groepen waarvan ze lid zijn of groepen waarvan ze deel uitmaken.</li><br>Zie [standaard gebruikers machtigingen](../fundamentals/users-default-permissions.md#member-and-guest-users)voor een gedetailleerde vergelijking van de mapmachtigingen die afkomstig zijn van de Graph API.
Welke onderdelen van de portal van mijn apps beïnvloeden deze functie? | De functionaliteit van de groepen in de portal mijn apps voldoet aan deze nieuwe machtigingen. Dit omvat alle paden voor het weer geven van de groepen lijst en groepslid maatschappen in mijn apps. Er zijn geen wijzigingen aangebracht in de beschik baarheid voor groeps tegels. De beschik baarheid van de groeps tegel wordt nog steeds beheerd door de bestaande groeps instelling in de Azure Portal.
Deze machtigingen overschrijven gast instellingen voor share point of micro soft teams? | Nee. Deze bestaande instellingen bepalen nog steeds de ervaring en toegang in deze toepassingen. Als u bijvoorbeeld problemen in share point ziet, controleert u de instellingen voor extern delen.
Wat zijn de bekende compatibiliteits problemen in planner en Yammer? | <li>Als de machtigingen zijn ingesteld op beperkt, hebben gasten die zijn aangemeld bij de mobiele app van de planner geen toegang tot hun plannen of taken.<li>Als de machtigingen zijn ingesteld op beperkt, kunnen gasten die zijn aangemeld bij Yammer de groep niet verlaten.
Worden mijn bestaande gast machtigingen gewijzigd in mijn Tenant? | Er zijn geen wijzigingen aangebracht in de huidige instellingen. We behouden achterwaartse compatibiliteit met uw huidige instellingen. U bepaalt wanneer u wijzigingen wilt aanbrengen.
Worden deze machtigingen standaard ingesteld? | Nee. De bestaande standaard machtigingen blijven ongewijzigd. U kunt de machtigingen eventueel zo instellen dat ze meer beperkend zijn.
Zijn er licentie vereisten voor deze functie? | Nee, er zijn geen nieuwe licentie vereisten voor deze functie.

## <a name="next-steps"></a>Volgende stappen

- Zie [Wat zijn de standaard gebruikers machtigingen in azure Active Directory?](../fundamentals/users-default-permissions.md) voor meer informatie over bestaande gast machtigingen in azure AD?
- Zie [authorizationPolicy resource type](/graph/api/resources/authorizationpolicy) voor een overzicht van de Microsoft Graph API-methoden voor het beperken van toegang tot gasten.
- Als u alle toegang voor een gebruiker wilt intrekken, raadpleegt u [gebruikers toegang intrekken in azure AD](users-revoke-access.md)