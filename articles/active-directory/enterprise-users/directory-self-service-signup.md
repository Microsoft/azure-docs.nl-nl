---
title: Selfservice voor aanmelden voor via e-mail geverifieerde gebruikers - Azure AD | Microsoft Docs
description: Gebruik de selfservice voor aanmelden in een Azure Active Directory-organisatie (Azure AD)
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: overview
ms.workload: identity
ms.date: 11/15/2020
ms.author: curtand
ms.reviewer: elkuzmen
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 846059c5c20255f9bddba59673458e12e1f354d3
ms.sourcegitcommit: b8eba4e733ace4eb6d33cc2c59456f550218b234
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/23/2020
ms.locfileid: "95503575"
---
# <a name="what-is-self-service-sign-up-for-azure-active-directory"></a>Wat is de selfservice voor aanmelden voor Azure Active Directory?

In dit artikel wordt uitgelegd hoe u de selfservice voor aanmelden gebruikt voor het invullen van gegevens van een organisatie in Azure Active Directory (Azure AD). Zie [Een niet-beheerde map overnemen als beheerder](domains-admin-takeover.md) als u een domeinnaam van een niet beheerde Azure AD-organisatie wilt overnemen.

## <a name="why-use-self-service-sign-up"></a>Wat is de selfservice voor registreren?

* Klanten sneller bij de services die ze willen, krijgen
* Op e-mail gebaseerde aanbiedingen voor een service maken
* Op e-mail gebaseerde aanmeldingsstromen maken waardoor gebruikers snel identiteiten kunnen maken met behulp van hun eenvoudig te onthouden zakelijke e-mailaliassen
* Een via de selfservice gemaakte Azure AD-map kan worden omgezet in een beheerde map die voor andere services kan worden gebruikt

## <a name="terms-and-definitions"></a>Termen en definities

* **Selfservice voor registreren**: Dit is de methode waarmee gebruikers zich voor een cloudservice aanmelden en waardoor automatisch een identiteit voor hen wordt gemaakt in Azure AD op basis van hun e-maildomein.
* **Niet-beheerde Azure AD-map**: Dit is de map waarin die identiteit is gemaakt. Een niet-beheerde map is een map zonder globale beheerder.
* **Via e-mail geverifieerde gebruiker**: Dit is een type gebruikersaccount in Azure AD. Een gebruiker voor wie automatisch een identiteit wordt gemaakt na aanmelding voor een selfservice-aanbieding wordt ook wel een via e-mail geverifieerde gebruiker genoemd. Een via e-mail geverifieerde gebruiker is een gewoon lid van een map waaraan de tag creationmethod=EmailVerified is toegevoegd.

## <a name="how-do-i-control-self-service-settings"></a>Hoe beheer ik de instellingen voor de selfservice?

Beheerders beschikken over twee besturingselementen voor selfservice. Ze kunnen bepalen of:

* gebruikers via e-mail mogen deelnemen aan de map; en of
* gebruikers zichzelf kunnen licentiëren voor toepassingen en services

### <a name="how-can-i-control-these-capabilities"></a>Hoe beheer ik deze mogelijkheden?

Een beheerder kan deze mogelijkheden configureren met behulp van de volgende Set-MsolCompanySettings-parameters van de Azure AD-cmdlet:

* Met **AllowEmailVerifiedUsers** beheert u of een gebruiker een map kan maken of samenvoegen. Als u die parameter instelt op $false, kunnen via e-mail geverifieerde gebruikers de map niet samenvoegen.
* Met **AllowAdHocSubscriptions** beheert u de mogelijkheid voor gebruikers om de selfservice voor aanmelden uit te voeren. Als u die parameter instelt op $false, kunnen gebruikers de selfservice voor aanmelden niet uitvoeren.
  
AllowEmailVerifiedUsers en AllowAdHocSubscriptions zijn instellingen die voor de hele map gelden en die kunnen worden toegepast op een beheerde of niet-beheerde map. Hier ziet u voorbeeld waarin:

* u een map met een geverifieerd domein zoals contoso.com beheert;
* u B2B-samenwerking van een andere map gebruikt om een gebruiker uit te nodigen die niet al bestaat (userdoesnotexist@contoso.com) in de startmap van contoso.com;
* AllowEmailVerifiedUsers in de startmap is ingeschakeld.

Als de bovenstaande voorwaarden true zijn, wordt vervolgens een lid-gebruiker gemaakt in de startmap en een wordt een B2B-gastgebruiker gemaakt in de map van waaruit de uitnodiging afkomstig is.

Zie voor meer informatie over Flow en PowerApps-proefabonnementen de volgende artikelen:

* [Hoe kan ik voorkomen dat mijn bestaande gebruikers Power BI gaan gebruiken?](https://support.office.com/article/Power-BI-in-your-Organization-d7941332-8aec-4e5e-87e8-92073ce73dc5#bkmk_preventjoining)
* [Veelgestelde vragen over Flow in uw organisatie](/flow/organization-q-and-a)

### <a name="how-do-the-controls-work-together"></a>Hoe werken de besturingselementen samen?
Deze twee parameters kunnen samen worden gebruikt om nauwkeuriger beheer over de selfservice voor aanmelden te definiëren. Met de volgende opdracht staat u bijvoorbeeld toe dat gebruikers de selfservice voor aanmelden kunnen uitvoeren, maar alleen als die gebruikers al een account in Azure AD hebben (met andere woorden: gebruikers waarvoor eerst een via e-mail geverifieerd account moet worden gemaakt, kunnen de selfservice voor aanmelden niet uitvoeren):

```powershell
    Set-MsolCompanySettings -AllowEmailVerifiedUsers $false -AllowAdHocSubscriptions $true
```

In het volgende stroomdiagram worden de verschillende combinaties voor deze parameters en de daaruit volgende voorwaarden voor de map en de selfservice voor aanmelden uitgelegd.

![Stroomdiagram met de besturingselementen voor de selfservice voor aanmelden](./media/directory-self-service-signup/SelfServiceSignUpControls.png)

De details van deze instelling kunnen worden opgehaald met de volgende PowerShell-cmdlet Get-MsolCompanyInformation. Zie [Get-MsolCompanyInformation](/powershell/module/msonline/get-msolcompanyinformation?view=azureadps-1.0) voor meer informatie

```powershell
    Get-MsolCompanyInformation | Select AllowEmailVerifiedUsers, AllowAdHocSubscriptions
```

Zie [Set-MsolCompanySettings](/powershell/module/msonline/set-msolcompanysettings?view=azureadps-1.0) voor meer informatie en voorbeelden van het gebruik van deze parameters.

## <a name="next-steps"></a>Volgende stappen

* [Een aangepaste domeinnaam toevoegen in Azure AD](../fundamentals/add-custom-domain.md)
* [Azure PowerShell installeren en configureren](/powershell/azure/)
* [Azure PowerShell](/powershell/azure/)
* [Azure-cmdlet-naslaginformatie](/powershell/azure/get-started-azureps)
* [Set-MsolCompanySettings](/powershell/module/msonline/set-msolcompanysettings?view=azureadps-1.0)
* [Uw werk- of schoolaccount in een niet-beheerde map sluiten](users-close-account.md)
