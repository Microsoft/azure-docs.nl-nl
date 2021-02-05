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
ms.openlocfilehash: 815947b7c1774fb58ca4e3d20a4d1d2b43099cd2
ms.sourcegitcommit: f377ba5ebd431e8c3579445ff588da664b00b36b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/05/2021
ms.locfileid: "99593075"
---
# <a name="quickstart-set-up-a-tenant"></a>Quickstart: Een tenant instellen

Als u apps wilt bouwen die gebruikmaken van het micro soft-identiteits platform voor identiteits-en toegangs beheer, moet u toegang hebben tot een Azure Active Directory Azure AD- *Tenant*. Het bevindt zich in de Azure AD-Tenant die u registreert en beheert uw apps, configureert hun toegang tot gegevens in Microsoft 365 en andere web-Api's en schakelt functies zoals voorwaardelijke toegang in.

Een tenant vertegenwoordigt een organisatie. Het is een toegewezen exemplaar van Azure AD dat een organisatie of app-ontwikkelaar aan het begin van een relatie met micro soft ontvangt. Deze relatie kan bijvoorbeeld beginnen met het aanmelden voor Azure, Microsoft Intune of Microsoft 365.

Elke Azure AD-tenant is uniek en werkt afzonderlijk van andere Azure AD-tenants. Het heeft een eigen representatie van werk-en school identiteiten, identiteiten van consumenten (als het een Azure AD B2C-Tenant is) en app-registraties. Een app-registratie in uw Tenant kan alleen authenticatie toestaan van accounts binnen uw Tenant of van alle tenants.

## <a name="prerequisites"></a>Vereisten

Een Azure-account met een actief abonnement. [Gratis een account maken](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)

## <a name="determining-the-environment-type"></a>Het omgevings type bepalen

U kunt twee soorten omgevingen maken. De omgeving is alleen afhankelijk van de soorten gebruikers die door uw app worden geverifieerd. 

Deze Quick Start biedt een oplossing voor twee scenario's voor het type app dat u wilt bouwen:

* Werk-en school accounts (Azure AD) of micro soft-accounts (zoals Outlook.com en Live.com)
* Sociale en lokale (Azure AD B2C) accounts

## <a name="work-and-school-accounts-or-personal-microsoft-accounts"></a>Werk- en schoolaccounts of persoonlijke Microsoft-accounts

Als u een omgeving wilt bouwen voor werk-en school accounts of persoonlijke micro soft-accounts, kunt u een bestaande Azure AD-Tenant gebruiken of een nieuwe maken.
### <a name="use-an-existing-azure-ad-tenant"></a>Een bestaande Azure AD-tenant gebruiken

Veel ontwikkel aars hebben al tenants via Services of abonnementen die zijn gekoppeld aan Azure AD-tenants, zoals Microsoft 365 of Azure-abonnementen.

De Tenant controleren:

1. Meld u aan bij <a href="https://portal.azure.com/" target="_blank">Azure Portal<span class="docon docon-navigate-external x-hidden-focus"></span></a>. Gebruik het account dat u gebruikt voor het beheren van uw toepassing.
1. Controleer de rechter bovenhoek. Als u een Tenant hebt, wordt u automatisch aangemeld. U ziet de naam van de Tenant rechtstreeks onder uw account naam.
   * Beweeg de muis aanwijzer over de naam van uw account om uw naam, e-mail adres, map of Tenant-ID (GUID) en domein weer te geven.
   * Als uw account is gekoppeld aan meerdere tenants, kunt u de accountnaam selecteren om een menu te openen waarmee u kunt schakelen tussen de tenants. Elke tenant heeft zijn eigen tenant-id.

> [!TIP]
> Als u de Tenant-ID wilt vinden, kunt u het volgende doen:
> * Beweeg de muis aanwijzer over de naam van uw account om de map of Tenant-ID op te halen.
> * Zoek en selecteer **Azure Active Directory**  >  **Eigenschappen**  >  **Tenant-id** in de Azure Portal.

Als u geen Tenant hebt die aan uw account is gekoppeld, ziet u een GUID onder uw account naam. U kunt acties zoals het registreren van apps niet uitvoeren als u een Azure AD-Tenant maakt.

### <a name="create-a-new-azure-ad-tenant"></a>Een nieuwe Azure AD-tenant maken

Als u nog geen Azure AD-Tenant hebt of een nieuwe wilt maken voor ontwikkeling, raadpleegt u [een nieuwe Tenant maken in azure AD](../fundamentals/active-directory-access-create-new-tenant.md). Of gebruik de [ervaring](https://portal.azure.com/#create/Microsoft.AzureActiveDirectory) voor het maken van mappen in de Azure Portal. 

U geeft de volgende informatie op om uw nieuwe Tenant te maken:

- **Naam van de organisatie**
- **Eerste domein** -dit domein maakt deel uit van *. onmicrosoft.com. U kunt het domein later aanpassen.
- **Land of regio**

> [!NOTE]
> Gebruik alfanumerieke tekens als u de tenant een naam geeft. Speciale tekens zijn niet toegestaan. De naam mag maximaal 256 tekens lang zijn.

## <a name="social-and-local-accounts"></a>Socialemedia-accounts en lokale accounts

Maak een Azure AD B2C-Tenant om te beginnen met het bouwen van apps die zich aanmelden bij sociale en lokale accounts. Zie [een Azure AD B2C-Tenant maken](../../active-directory-b2c/tutorial-create-tenant.md)om te beginnen.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Een app registreren](quickstart-register-app.md) om te integreren met Microsoft Identity Platform.
