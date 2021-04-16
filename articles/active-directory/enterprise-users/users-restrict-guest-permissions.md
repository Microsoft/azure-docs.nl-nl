---
title: Toegangsmachtigingen voor gastgebruikers beperken - Azure Active Directory | Microsoft Docs
description: Toegangsmachtigingen voor gastgebruikers beperken met Azure Portal, PowerShell of Microsoft Graph in Azure Active Directory
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
ms.openlocfilehash: df4cb32720d80dd23289be7e760c9934e9a8db8a
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107501498"
---
# <a name="restrict-guest-access-permissions-preview-in-azure-active-directory"></a>Machtigingen voor gasttoegang beperken (preview) in Azure Active Directory

Azure Active Directory (Azure AD) kunt u beperken wat externe gastgebruikers in hun organisatie kunnen zien in Azure AD. Gastgebruikers worden standaard ingesteld op een beperkt machtigingsniveau in Azure AD, terwijl de standaardinstelling voor lidgebruikers de volledige set standaardgebruikersmachtigingen is. Dit is een preview van een nieuw machtigingsniveau voor gastgebruikers in de instellingen voor externe samenwerking van uw Azure AD-organisatie voor nog beperktere toegang. Uw opties voor gasttoegang zijn nu dus:

Machtigingsniveau         | Toegangsniveau | Waarde
----------------         | ------------ | -----
Hetzelfde als lidgebruikers     | Gasten hebben dezelfde toegang tot Azure AD-resources als lidgebruikers | a0b1b346-4d3e-4e8b-98f8-753987be4970
Beperkte toegang (standaard) | Gasten kunnen het lidmaatschap van alle niet-verborgen groepen zien | 10dae51f-b6af-4016-8d66-8c2a99b929b3
**Beperkte toegang (nieuw)**  | **Gasten kunnen geen lidmaatschap van groepen zien** | **2af84b1e-32c8-42b7-82bc-daa82404023b**

Wanneer gasttoegang is beperkt, kunnen gasten alleen hun eigen gebruikersprofiel bekijken. Machtiging om andere gebruikers weer te geven is niet toegestaan, zelfs niet als de gast zoekt op User Principal Name of objectId. Met beperkte toegang kunnen gastgebruikers ook geen lidmaatschap zien van de groepen waarin ze zich hebben. Zie What are the [default user permissions in Azure Active Directory?](../fundamentals/users-default-permissions.md)(Wat zijn de standaardgebruikersmachtigingen in Azure Active Directory? voor meer informatie over de algemene standaardgebruikersmachtigingen, waaronder machtigingen voor gastgebruikers.

## <a name="permissions-and-licenses"></a>Machtigingen en licenties

U moet de rol globale beheerder hebben om de instellingen voor externe samenwerking te configureren. Er zijn geen aanvullende licentievereisten om gasttoegang te beperken.

## <a name="update-in-the-azure-portal"></a>Werk in de Azure Portal

We hebben wijzigingen aangebracht in de bestaande besturingselementen Azure Portal voor gastgebruikersmachtigingen.

1. Meld u aan bij [het Azure AD-beheercentrum](https://aad.portal.azure.com) met Globale beheerder machtigingen.
1. Selecteer op **Azure Active Directory** overzichtspagina voor uw organisatie de **optie Gebruikersinstellingen.**
1. Selecteer **onder Externe gebruikers** de optie Instellingen voor externe samenwerking **beheren.**
1. Selecteer op **de pagina Instellingen voor** externe samenwerking de optie Gastgebruikerstoegang is beperkt tot eigenschappen en **lidmaatschappen van hun eigen directoryobjecten.**

    ![Pagina instellingen voor externe samenwerking van Azure AD](./media/users-restrict-guest-permissions/external-collaboration-settings.png)

1. Selecteer **Opslaan**. Het kan tot 15 minuten duren voordat de wijzigingen zijn doorgevoerd voor gastgebruikers.

## <a name="update-with-the-microsoft-graph-api"></a>Bijwerken met de Microsoft Graph-API

We hebben een nieuwe API voor Microsoft Graph toegevoegd om gastmachtigingen in uw Azure AD-organisatie te configureren. De volgende API-aanroepen kunnen worden gedaan om elk machtigingsniveau toe te wijzen. De waarde voor guestUserRoleId die hier wordt gebruikt, is ter illustratie van de meest beperkte gastgebruikersinstelling. Zie [authorizationPolicy resource type](/graph/api/resources/authorizationpolicy)voor meer Microsoft Graph het instellen van gastmachtigingen.

### <a name="configuring-for-the-first-time"></a>Voor de eerste keer configureren

````PowerShell
POST https://graph.microsoft.com/beta/policies/authorizationPolicy/authorizationPolicy

{
  "guestUserRoleId": "2af84b1e-32c8-42b7-82bc-daa82404023b"
}
````

Het antwoord moet Geslaagd 204 zijn.

### <a name="updating-the-existing-value"></a>De bestaande waarde bijwerken

````PowerShell
PATCH https://graph.microsoft.com/beta/policies/authorizationPolicy/authorizationPolicy

{
  "guestUserRoleId": "2af84b1e-32c8-42b7-82bc-daa82404023b"
}
````

Het antwoord moet Geslaagd 204 zijn.

### <a name="view-the-current-value"></a>De huidige waarde weergeven

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

## <a name="update-with-powershell-cmdlets"></a>Bijwerken met PowerShell-cmdlets

Met deze functie hebben we de mogelijkheid toegevoegd om de beperkte machtigingen te configureren via PowerShell v2-cmdlets. Get- en Set PowerShell-cmdlets zijn gepubliceerd in versie 2.0.2.85.

### <a name="get-command-get-azureadmsauthorizationpolicy"></a>Opdracht get: Get-AzureADMSAuthorizationPolicy

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

### <a name="set-command-set-azureadmsauthorizationpolicy"></a>Opdracht instellen: Set-AzureADMSAuthorizationPolicy

Voorbeeld:

````PowerShell
PS C:\WINDOWS\system32> Set-AzureADMSAuthorizationPolicy -GuestUserRoleId '2af84b1e-32c8-42b7-82bc-daa82404023b'
````

> [!NOTE]
> Wanneer u hierom wordt gevraagd, moet u authorizationPolicy als id invoeren.

## <a name="supported-microsoft-365-services"></a>Ondersteunde Microsoft 365 services

### <a name="supported-services"></a>Ondersteunde services

Met ondersteund bedoelen we dat de ervaring is zoals verwacht; om precies te zijn dat dit hetzelfde is als de huidige gastervaring.

- Teams
- Outlook (OWA)
- SharePoint
- Planner in Teams
- Planner-web-app

### <a name="services-currently-not-supported"></a>Services worden momenteel niet ondersteund

Service zonder huidige ondersteuning kan compatibiliteitsproblemen hebben met de nieuwe gastbeperkingsinstelling.

- Formulieren
- Mobiele planner-app
- Project
- Yammer

## <a name="frequently-asked-questions-faq"></a>Veelgestelde vragen

Vraag | Antwoord
-------- | ------
Waar zijn deze machtigingen van toepassing? | Deze machtigingen op mapniveau worden afgedwongen in Azure AD-services en -portals, waaronder de Microsoft Graph, PowerShell v2, de Azure Portal en Mijn apps-portal. Microsoft 365 services die gebruikmaken van Microsoft 365 voor samenwerkingsscenario's worden ook beïnvloed, met name Outlook, Microsoft Teams en SharePoint.
Hoe beïnvloeden beperkte machtigingen welke groepen gasten kunnen zien? | Ongeacht de standaard- of beperkte gastmachtigingen kunnen gasten de lijst met groepen of gebruikers niet opsnoemen. Gasten kunnen groepen zien waar ze lid van zijn in zowel de Azure Portal als de Mijn apps portal, afhankelijk van de machtigingen:<li>**Standaardmachtigingen:** om de groepen te vinden waar ze lid van zijn in de  Azure Portal, moet de gast zoeken naar de object-id in de lijst Alle gebruikers en vervolgens **Groepen selecteren.** Hier kunnen ze de lijst met groepen zien waarvan ze lid zijn, met inbegrip van alle groepsdetails, inclusief naam, e-mailadres, en meer. In de Mijn apps-portal kunnen ze een lijst zien met de groepen waar ze eigenaar van zijn en de groepen waar ze lid van zijn.</li><li>**Beperkte gastmachtigingen:** in de Azure Portal kunnen ze nog steeds de lijst met groepen vinden waar ze lid van zijn door te zoeken naar hun object-id in de lijst Alle gebruikers en vervolgens Groepen te selecteren. Ze kunnen slechts zeer beperkte details over de groep zien, met name de object-id. De kolommen Naam en E-mail zijn in het ontwerp leeg en Groepstype is Niet herkend. In de Mijn apps hebben ze geen toegang tot de lijst met groepen waar ze eigenaar van zijn of groepen waar ze lid van zijn.</li><br>Zie Standaardgebruikersmachtigingen voor een gedetailleerde vergelijking van de directorymachtigingen die afkomstig zijn van de [Graph API.](../fundamentals/users-default-permissions.md#member-and-guest-users)
Op welke onderdelen van Mijn apps portal is deze functie van invloed? | De functionaliteit van groepen in de Mijn apps portal zal deze nieuwe machtigingen in eren. Dit omvat alle paden voor het weergeven van de lijst met groepen en groepslidmaatschap in Mijn apps. Er zijn geen wijzigingen aangebracht in de beschikbaarheid van de groepstegel. De beschikbaarheid van de groepstegel wordt nog steeds bepaald door de bestaande groepsinstelling in Azure Portal.
Overschrijven deze machtigingen sharepoint- of Microsoft Teams-gastinstellingen? | Nee. Deze bestaande instellingen bepalen nog steeds de ervaring en toegang in deze toepassingen. Als u bijvoorbeeld problemen in SharePoint ziet, controleert u uw instellingen voor extern delen.
Wat zijn de bekende compatibiliteitsproblemen in Planner en Yammer? | <li>Als de machtigingen zijn ingesteld op 'beperkt', hebben gasten die zijn aangemeld bij de mobiele Planner-app geen toegang tot hun plannen of taken.<li>Als de machtigingen zijn ingesteld op 'beperkt', kunnen gasten die zijn aangemeld bij Yammer de groep niet verlaten.
Worden mijn bestaande gastmachtigingen gewijzigd in mijn tenant? | Er zijn geen wijzigingen aangebracht in uw huidige instellingen. We blijven achterwaarts compatibel met uw bestaande instellingen. U bepaalt wanneer u wijzigingen wilt aanbrengen.
Worden deze machtigingen standaard ingesteld? | Nee. De bestaande standaardmachtigingen blijven ongewijzigd. U kunt eventueel instellen dat de machtigingen beperkter zijn.
Zijn er licentievereisten voor deze functie? | Nee, er zijn geen nieuwe licentievereisten met deze functie.

## <a name="next-steps"></a>Volgende stappen

- Zie Wat zijn de standaardgebruikersmachtigingen in azure ad voor meer informatie over [bestaande gastmachtigingen in Azure Active Directory?](../fundamentals/users-default-permissions.md)
- Zie [authorizationPolicy resource type](/graph/api/resources/authorizationpolicy) Microsoft Graph de API-methoden voor het beperken van gasttoegang
- Zie Gebruikerstoegang intrekken in Azure AD als u alle toegang voor een gebruiker [wilt intrekken](users-revoke-access.md)
