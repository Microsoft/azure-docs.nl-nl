---
title: Apps weer geven met behulp van uw Azure Active Directory-Tenant voor identiteits beheer
description: Meer informatie over het weer geven van alle toepassingen met behulp van uw Azure Active Directory-Tenant voor identiteits beheer.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: reference
ms.date: 01/07/2021
ms.author: kenwith
ms.openlocfilehash: 443e8c83ec29f9f0478e3881b9e6d6a8eb0bf403
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/02/2021
ms.locfileid: "99259783"
---
# <a name="viewing-apps-using-your-azure-ad-tenant-for-identity-management"></a>Apps weer geven met uw Azure AD-Tenant voor identiteits beheer
In de Quick Start- [serie op toepassings beheer](view-applications-portal.md) worden de basis principes uitgelegd. Hierin leert u hoe u alle apps kunt weer geven met behulp van uw Azure AD-Tenant voor identiteits beheer. In dit artikel Dives een beetje dieper op de typen apps die u vindt.

## <a name="why-does-a-specific-application-appear-in-my-all-applications-list"></a>Waarom wordt een specifieke toepassing weer gegeven in mijn lijst met alle toepassingen?
Bij het filteren op **alle toepassingen** wordt in de **lijst** **alle toepassingen** elke service-principal-object in uw Tenant weer gegeven. Service-Principal-objecten kunnen op verschillende manieren in deze lijst worden weer gegeven:
- Wanneer u een toepassing toevoegt vanuit de toepassings galerie, waaronder:
   - **Azure AD-zakelijke toepassingen** : apps die zijn toegevoegd aan uw Tenant met behulp van de optie **bedrijfs toepassingen** in de Azure AD-Portal. Meestal worden apps geïntegreerd met de SAML-standaard.
   - **Azure AD-App-registraties** : apps die zijn toegevoegd aan uw Tenant met behulp van de **app-registraties** optie in de Azure AD-Portal. Doorgaans ontwikkelde apps met behulp van de Open-ID Connect-en OAuth-standaarden.
   - **Toepassings proxy toepassingen** : een toepassing die in uw on-premises omgeving wordt uitgevoerd en die u wilt voorzien van een beveiligde eenmalige aanmelding op een externe locatie
- Wanneer u zich aanmeldt voor of zich aanmeldt bij een toepassing van derden die is geïntegreerd met Azure Active Directory. Een voor beeld is [Smart Sheet](https://app.smartsheet.com/b/home) of [DocuSign](https://www.docusign.net/member/MemberLogin.aspx).
- Micro soft-apps, zoals Microsoft 365.
- Wanneer u een nieuwe toepassings registratie toevoegt door een aangepaste toepassing te maken met behulp van het [toepassings register](../develop/quickstart-register-app.md)
- Wanneer u een nieuwe toepassings registratie toevoegt door een aangepaste toepassing te maken met behulp van de [Portal voor toepassings registratie van de v 2.0-toepassing](../develop/quickstart-register-app.md)
- Wanneer u een toepassing toevoegt, ontwikkelt u met behulp van de [ASP.NET-verificatie methoden](https://www.asp.net/visual-studio/overview/2013/creating-web-projects-in-visual-studio#orgauthoptions) of [verbonden services](https://devblogs.microsoft.com/visualstudio/connecting-to-cloud-services/) van Visual Studio
- Wanneer u een Service-Principal-object maakt met behulp van de [Azure AD Power shell-module](/powershell/azure/active-directory/install-adv2)
- Wanneer u [een toepassing](../develop/howto-convert-app-to-be-multi-tenant.md) als beheerder hebt om gegevens in uw Tenant te gebruiken
- Wanneer een [gebruiker een toepassing](../develop/howto-convert-app-to-be-multi-tenant.md) gebruikt om gegevens in uw Tenant te gebruiken
- Wanneer u bepaalde services voor het opslaan van gegevens in uw Tenant inschakelt. Een voor beeld is het opnieuw instellen van een wacht woord, dat wordt gemodelleerd als Service-Principal om het beleid voor wachtwoord herstel veilig op te slaan.

Zie [hoe toepassingen worden toegevoegd aan Azure AD](../develop/active-directory-how-applications-are-added.md)voor meer informatie over hoe en waarom apps worden toegevoegd aan uw Directory.

## <a name="next-steps"></a>Volgende stappen
[Toepassingen beheren met Azure Active Directory](what-is-application-management.md)
