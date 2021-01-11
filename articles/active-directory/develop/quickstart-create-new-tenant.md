---
title: 'Quickstart: Een Azure Active Directory-tenant maken'
titleSuffix: Microsoft identity platform
description: In deze quickstart leert u hoe u een Azure Active Directory-Tenant kunt maken voor het ontwikkelen van toepassingen die gebruikmaken van het Microsoft Identity-platform voor verificatie en autorisatie.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: quickstart
ms.date: 03/12/2020
ms.author: ryanwi
ms.reviewer: jmprieur
ms.custom: aaddev, identityplatformtop40, fasttrack-edit
ms.openlocfilehash: f43cb80a148f5dadb409f506f237cb1210a2000a
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/06/2021
ms.locfileid: "97933996"
---
# <a name="quickstart-set-up-a-tenant"></a>Quickstart: Een tenant instellen

Met het Microsoft Identity Platform kunnen ontwikkelaars apps bouwen voor een breed scala van aangepaste Microsoft 365-omgevingen en -identiteiten. Om aan de slag te gaan met het Microsoft Identity Platform hebt u toegang nodig tot een omgeving, ook wel een Azure AD-tenant genoemd, waarmee u apps kunt registreren en beheren. Daarnaast hebt u toegang nodig tot Microsoft 365-gegevens en moet u aangepaste beperkingen voor voorwaardelijke toegang en de tenant implementeren.

Een tenant vertegenwoordigt een organisatie. Een tenant is een toegewezen exemplaar van Azure AD dat een organisatie of app-ontwikkelaar ontvangt wanneer deze een relatie start met Microsoft, door zich bijvoorbeeld aan te melden voor Azure, Microsoft Intune of Microsoft 365.

Elke Azure AD-tenant is verschillend en gescheiden van andere Azure AD-tenants. Elke tenant heeft een eigen weergave van werk- en schoolidentiteiten, consumentidentiteiten (als het een Azure AD B2C-tenant betreft) en app-registraties. Met een app-registratie binnen uw tenant kunt u verificatie toestaan vanaf accounts alleen binnen uw tenants of alle tenants.

## <a name="prerequisites"></a>Vereisten

- Een Azure-account met een actief abonnement. [Gratis een account maken](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)

## <a name="determining-environment-type"></a>Omgevingstype bepalen

Er zijn twee soorten omgevingen die u kunt maken. Welke u nodig hebt is uitsluitend gebaseerd op de typen gebruikers die u moet verifiëren met uw app.

* Werk en school (Azure AD-accounts) of Microsoft-accounts (zoals outlook.com en live.com)
* Socialemedia-accounts en lokale accounts (Azure AD B2C)

De snelstart is verdeeld in twee scenario’s, afhankelijk van het type app dat u bouwt.

## <a name="work-and-school-accounts-or-personal-microsoft-accounts"></a>Werk- en schoolaccounts of persoonlijke Microsoft-accounts

### <a name="use-an-existing-tenant"></a>Een bestaande tenant gebruiken

Veel ontwikkelaars hebben al tenants via services of abonnementen die zijn gekoppeld aan Azure AD-tenants, zoals via Microsoft 365- of Azure-abonnementen.

1. Om te controleren of u al een tenant hebt, meld u zich aan bij [Azure Portal](https://portal.azure.com) met het account dat u wilt gebruiken voor het beheren van uw toepassing.
1. Kijk in de rechterbovenhoek. Als u een tenant hebt, wordt u automatisch aangemeld en ziet u de naam van de tenant direct onder de accountnaam.
   * Houd de muisaanwijzer op uw accountnaam rechtsboven in Azure Portal om uw naam, e-mailadres, directory en tenant-id (een GUID), en uw domein te zien.
   * Als uw account is gekoppeld aan meerdere tenants, kunt u de accountnaam selecteren om een menu te openen waarmee u kunt schakelen tussen de tenants. Elke tenant heeft zijn eigen tenant-id.

> [!TIP]
> Als u de tenant-id wilt zoeken, kunt u:
> * De muisaanwijzer op uw accountnaam houden om de directory/tenant-ID te zien, of
> * Zoek en selecteer **Azure Active Directory > Eigenschappen > Tenant-id** in de Azure-portal

Als er geen bestaande tenant is gekoppeld aan uw account, ziet u een GUID onder de accountnaam. U moet dan eerst een nieuwe tenant maken voordat u bewerkingen kunt uitvoeren zoals het registreren van apps. Daarvoor volgt u de stappen in het volgende gedeelte.

### <a name="create-a-new-azure-ad-tenant"></a>Een nieuwe Azure AD-tenant maken

Als u nog geen Azure AD-tenant hebt of een nieuwe wilt maken voor ontwikkeling. raadpleegt u de [quickstart](../fundamentals/active-directory-access-create-new-tenant.md) of volgt u de [instructies voor het maken van een directory](https://portal.azure.com/#create/Microsoft.AzureActiveDirectory). U moet de volgende informatie opgeven voor het maken van de nieuwe tenant:

- **Naam van de organisatie**
- **Eerste domein**: dit wordt onderdeel van *. onmicrosoft.com. U kunt het domein later nog aanpassen.
- **Land of regio**

> [!NOTE]
> Gebruik alfanumerieke tekens als u de tenant een naam geeft. Speciale tekens zijn niet toegestaan. De naam mag maximaal 256 tekens lang zijn.

## <a name="social-and-local-accounts"></a>Socialemedia-accounts en lokale accounts

Om te beginnen met het bouwen van apps waarmee socialemedia-accounts en lokale accounts zich aanmelden, moet u een Azure AD B2C-tenant maken. Daarvoor volgt u de stappen in [een Azure AD B2C-tenant maken](../../active-directory-b2c/tutorial-create-tenant.md).

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Een app registreren](quickstart-register-app.md) om te integreren met Microsoft Identity Platform.
