---
title: Apps weergeven met behulp van Azure Active Directory tenant voor identiteitsbeheer
description: Meer informatie over het weergeven van alle toepassingen met uw Azure Active Directory tenant voor identiteitsbeheer.
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: reference
ms.date: 01/07/2021
ms.author: iangithinji
ms.openlocfilehash: b11f2dab97b3c45502c6050e06f0f39312f8c3c0
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107378070"
---
# <a name="viewing-apps-using-your-azure-ad-tenant-for-identity-management"></a>Apps weergeven met uw Azure AD-tenant voor identiteitsbeheer
De [Quickstart-reeks over toepassingsbeheer](view-applications-portal.md) laat u de basisbeginselen zien. In deze zelf-app leert u hoe u alle apps kunt weergeven met behulp van uw Azure AD-tenant voor identiteitsbeheer. In dit artikel gaan we dieper in op de typen apps die u kunt vinden.

## <a name="why-does-a-specific-application-appear-in-my-all-applications-list"></a>Waarom wordt een specifieke toepassing weergegeven in de lijst met alle toepassingen?
Wanneer u filtert **op Alle toepassingen,** wordt in **de lijst Alle** toepassingen elk Service Principal-object in uw tenant weergegeven.  Service-principalobjecten kunnen op verschillende manieren in deze lijst worden weergegeven:
- Wanneer u een toepassing uit de toepassingsgalerie toevoegt, met inbegrip van:
   - **Azure AD - Bedrijfstoepassingen:** apps die zijn toegevoegd aan uw tenant met behulp van de optie **Bedrijfstoepassingen** in de Azure AD-portal. Meestal apps die zijn geïntegreerd met behulp van de SAML-standaard.
   - **Azure AD - App-registraties:** apps toegevoegd aan uw tenant met behulp van **de App-registraties-optie** in de Azure AD-portal. Meestal aangepaste ontwikkelde apps die gebruikmaken van de Open ID Connect- en OAuth-standaarden.
   - **toepassingsproxy Toepassingen:** een toepassing die wordt uitgevoerd in uw on-premises omgeving en die u extern beveiligde een aanmelding wilt bieden
- Wanneer u zich aanmeldt voor of zich aanmeldt bij een toepassing van derden die is geïntegreerd met Azure Active Directory. Een voorbeeld is [Smartsheet](https://app.smartsheet.com/b/home) of [DocuSign.](https://www.docusign.net/member/MemberLogin.aspx)
- Microsoft-apps zoals Microsoft 365.
- Wanneer u een nieuwe toepassingsregistratie toevoegt door een aangepaste toepassing te maken met behulp van [het toepassingsregister](../develop/quickstart-register-app.md)
- Wanneer u een nieuwe toepassingsregistratie toevoegt door een aangepaste toepassing te maken met behulp van de [V2.0-portal voor toepassingsregistratie](../develop/quickstart-register-app.md)
- Wanneer u een toepassing toevoegt, ontwikkelt u met behulp van de Visual Studio van [ASP.NET-verificatiemethoden](https://www.asp.net/visual-studio/overview/2013/creating-web-projects-in-visual-studio#orgauthoptions) of [verbonden services](https://devblogs.microsoft.com/visualstudio/connecting-to-cloud-services/)
- Wanneer u een service-principalobject maakt met behulp van de [Azure AD PowerShell-module](/powershell/azure/active-directory/install-adv2)
- Wanneer u [een toepassing toestemming geeft als](../develop/howto-convert-app-to-be-multi-tenant.md) beheerder om gegevens in uw tenant te gebruiken
- Wanneer een [gebruiker toestemming geeft aan een toepassing om](../develop/howto-convert-app-to-be-multi-tenant.md) gegevens in uw tenant te gebruiken
- Wanneer u bepaalde services inschakelen die gegevens opslaan in uw tenant. Een voorbeeld hiervan is Wachtwoord opnieuw instellen, dat is gemodelleerd als een service-principal om uw beleid voor wachtwoord opnieuw instellen veilig op te slaan.

Zie Hoe toepassingen worden toegevoegd aan Azure AD voor meer informatie over hoe en waarom apps worden toegevoegd [aan uw directory.](../develop/active-directory-how-applications-are-added.md)

## <a name="next-steps"></a>Volgende stappen
[Toepassingen beheren met Azure Active Directory](what-is-application-management.md)
