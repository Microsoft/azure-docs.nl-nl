---
title: Active Directory vergelijken met Azure Active Directory
description: Dit document vergelijkt Active Directory Domain Services (toevoegen) aan Azure Active Directory (AD). Het bevat een overzicht van de belangrijkste concepten in identiteits oplossingen en legt uit hoe het verschillend of vergelijkbaar is.
services: active-directory
author: martincoetzer
manager: daveba
tags: azuread
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: fundamentals
ms.date: 02/26/2020
ms.author: martinco
ms.openlocfilehash: 64a8dabaedc3922ebd8d163b1ea162b7d1584de2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92371916"
---
# <a name="compare-active-directory-to-azure-active-directory"></a>Active Directory vergelijken met Azure Active Directory

Azure Active Directory is de volgende evolutie van oplossingen voor identiteits-en toegangs beheer voor de Cloud. Micro soft heeft Active Directory Domain Services geïntroduceerd in Windows 2000 om organisaties in staat te stellen meerdere on-premises infrastructuur onderdelen en systemen te beheren met één identiteit per gebruiker.

Azure AD neemt deze benadering op het volgende niveau door organisaties te voorzien van een IDaaS-oplossing (Identity as a Service) voor alle apps in de Cloud en on-premises.

De meeste IT-beheerders zijn bekend met Active Directory Domain Services-concepten. De volgende tabel bevat een overzicht van de verschillen en overeenkomsten tussen Active Directory concepten en Azure Active Directory.

|Concept|Active Directory (AD)|Azure Active Directory |
|:-|:-|:-|
|**Gebruikers**|||
|Inrichting: gebruikers | Organisaties maken hand matig interne gebruikers of gebruiken een intern of geautomatiseerd inrichtings systeem, zoals de Microsoft Identity Manager, om te integreren met een HR-systeem.|Bestaande AD-organisaties gebruiken [Azure AD Connect](../hybrid/how-to-connect-sync-whatis.md) om identiteiten te synchroniseren met de Cloud.</br> Azure AD voegt ondersteuning toe om automatisch gebruikers te maken op basis van de computers in de [Cloud](../saas-apps/workday-tutorial.md). </br>Azure AD kan identiteiten inrichten in [scim ingeschakelde](../app-provisioning/use-scim-to-provision-users-and-groups.md) SaaS-apps om automatisch apps te voorzien van de benodigde gegevens om toegang voor gebruikers toe te staan. |
|Inrichting: externe identiteiten| Organisaties maken externe gebruikers hand matig als gewone gebruikers in een toegewezen extern AD-forest, wat leidt tot overhead van beheer voor het beheer van de levens cyclus van externe identiteiten (gast gebruikers)| Azure AD biedt een speciale identiteits klasse ter ondersteuning van externe identiteiten. [Azure AD B2B](/azure/active-directory/b2b/) beheert de koppeling naar de identiteit van de externe gebruiker om te controleren of ze geldig zijn. |
| Beheer van rechten en groepen| Beheerders maken gebruikers lid van groepen. App-en resource-eigen aren geven groepen toegang tot apps of resources.| [Groepen](./active-directory-groups-create-azure-portal.md) zijn ook beschikbaar in azure AD en beheerders kunnen ook groepen gebruiken om machtigingen toe te kennen aan resources. In azure AD kunnen beheerders het lidmaatschap hand matig toewijzen aan groepen of een query gebruiken om gebruikers dynamisch toe te voegen aan een groep. </br> Beheerders kunnen het [rechten beheer](../governance/entitlement-management-overview.md) in azure AD gebruiken om gebruikers toegang te geven tot een verzameling apps en resources met behulp van werk stromen en, indien nodig, op tijd gebaseerde criteria. |
| Beheerders beheer|Organisaties gebruiken een combi natie van domeinen, organisatie-eenheden en groepen in AD om beheer rechten te delegeren voor het beheren van de map en de resources die IT-besturings elementen.| Azure AD biedt [ingebouwde rollen](./active-directory-users-assign-role-azure-portal.md) met het op rollen gebaseerde toegangs beheer (Azure AD RBAC)-systeem van Azure AD, met beperkte ondersteuning voor het [maken van aangepaste rollen](../roles/custom-overview.md) voor het delegeren van bevoegde toegang tot het identiteits systeem, de apps en de resources die IT-besturings elementen.</br>Het beheren van rollen kan worden uitgebreid met [privileged Identity Management (PIM)](../privileged-identity-management/pim-configure.md) voor Just-in-time, beperkte en op werk stroom gebaseerde toegang tot geprivilegieerde rollen. |
| Referentiebeheer| Referenties in Active Directory zijn gebaseerd op wacht woorden, verificatie van certificaten en smartcard verificatie. Wacht woorden worden beheerd met wachtwoord beleid dat is gebaseerd op de lengte van het wacht woord, de verval datum en de complexiteit.|Azure AD maakt gebruik van intelligente [wachtwoord beveiliging](../authentication/concept-password-ban-bad.md) voor de Cloud en on-premises. Beveiliging omvat slimme vergren deling plus het blok keren van veelvoorkomende en aangepaste wachtwoord zinnen en vervangingen. </br>Azure AD stimuleert de beveiliging aanzienlijk [door middel van multi-factor Authentication](../authentication/concept-mfa-howitworks.md) en technologieën met een [wacht woord](../authentication/concept-authentication-passwordless.md) , zoals FIDO2. </br>Azure AD vermindert de ondersteunings kosten door gebruikers een [self-service voor het opnieuw instellen van een wacht woord](../authentication/concept-sspr-howitworks.md) te bieden. |
| **Apps**|||
| Infra structuur-apps|Active Directory vormt de basis voor een groot aantal on-premises infra structuur-onderdelen, zoals DNS, DHCP, IPSec, WiFi, NPS en VPN-toegang|Azure AD in een nieuwe Cloud wereld is het nieuwe besturings element voor toegang tot apps en het vertrouwen op netwerk controles. Wanneer gebruikers zich verifiëren, wordt met de[voorwaardelijke toegang (CA)](../conditional-access/overview.md)bepaald welke gebruikers toegang hebben tot de apps onder de vereiste voor waarden.|
| Traditionele en verouderde apps| De meeste on-premises apps gebruiken LDAP, Windows-Integrated-verificatie (NTLM en Kerberos) of op headers gebaseerde verificatie om de toegang tot gebruikers te beheren.| Azure AD kan toegang bieden tot deze typen on-premises apps met behulp van [Azure AD-toepassings proxy](../manage-apps/application-proxy.md) -agents die on-premises worden uitgevoerd. Met deze methode kan Azure AD Active Directory gebruikers on-premises verifiëren met behulp van Kerberos terwijl u migreert of als u wilt samen werken met verouderde apps. |
| SaaS-apps|Active Directory ondersteunt geen standaard SaaS-apps en hiervoor is een Federatie systeem vereist, zoals AD FS.|SaaS-apps die OAuth2, SAML en WS- \* Authentication ondersteunen, kunnen worden geïntegreerd om Azure ad te gebruiken voor verificatie. |
| LOB-apps (line-of-Business) met moderne verificatie|Organisaties kunnen AD FS met Active Directory gebruiken ter ondersteuning van LOB-apps waarvoor moderne verificatie is vereist.| LOB-apps waarvoor moderne authenticatie is vereist, kunnen worden geconfigureerd om Azure AD te gebruiken voor verificatie. |
| Mid-tier/daemon-services|Services die in on-premises omgevingen worden uitgevoerd, gebruiken normaal gesp roken AD-service accounts of door groepen beheerde service accounts (gMSA) om uit te voeren. Deze apps nemen vervolgens de machtigingen over van het service account.| Azure AD biedt [beheerde identiteiten](../managed-identities-azure-resources/index.yml) voor het uitvoeren van andere werk belastingen in de Cloud. De levens cyclus van deze identiteiten wordt beheerd door Azure AD en is gekoppeld aan de resource provider en kan niet worden gebruikt voor andere doel einden om toegang tot de back-uptaken te krijgen.|
| **Apparaten**|||
| Mobiel|Active Directory biedt geen systeem eigen ondersteuning voor mobiele apparaten zonder oplossingen van derden.| De Mobile Device Management-oplossing van micro soft, Microsoft Intune, is geïntegreerd met Azure AD. Microsoft Intune biedt informatie over de status van de apparaten aan het identiteits systeem dat tijdens de verificatie wordt geëvalueerd. |
| Windows-Bureau bladen|Active Directory biedt de mogelijkheid om domein aan Windows-apparaten toe te voegen om ze te beheren met behulp van groepsbeleid, System Center Configuration Manager of andere oplossingen van derden.|Windows-apparaten kunnen worden [gekoppeld aan Azure AD](../devices/index.yml). Voorwaardelijke toegang kan controleren of een apparaat is toegevoegd aan Azure AD als onderdeel van het verificatie proces. Windows-apparaten kunnen ook worden beheerd met [Microsoft intune](/intune/what-is-intune). In dit geval kunt u met voorwaardelijke toegang bepalen of een apparaat compatibel is (bijvoorbeeld bijgewerkte beveiligings patches en virus handtekeningen) voordat toegang tot de apps wordt toegestaan.|
| Windows-servers| Active Directory biedt krachtige beheer mogelijkheden voor on-premises Windows-servers met behulp van groepsbeleid of andere beheer oplossingen.| Virtuele Windows Server-machines in azure kunnen worden beheerd met [Azure AD Domain Services](../../active-directory-domain-services/index.yml). [Beheerde identiteiten](../managed-identities-azure-resources/index.yml) kunnen worden gebruikt wanneer vm's toegang nodig hebben tot de identiteits systeem Directory of bronnen.|
| Linux/Unix-workloads|Active Directory biedt geen systeem eigen ondersteuning voor niet-Windows zonder oplossingen van derden, hoewel Linux-machines kunnen worden geconfigureerd om te worden geverifieerd met Active Directory als een Kerberos-realm.|Linux/Unix-Vm's kunnen [beheerde identiteiten](../managed-identities-azure-resources/index.yml) gebruiken om toegang te krijgen tot het identiteits systeem of de resources. Sommige organisaties migreren deze workloads naar Cloud container technologieën, die ook beheerde identiteiten kunnen gebruiken.|

## <a name="next-steps"></a>Volgende stappen

- [Wat is Azure Active Directory?](./active-directory-whatis.md)
- [Zelfbeheerde Active Directory Domain Services, Azure Active Directory en beheerde Azure Active Directory Domain Services vergelijken](../../active-directory-domain-services/compare-identity-solutions.md)
- [Veelgestelde vragen over Azure Active Directory](./active-directory-faq.md)
- [Wat is er nieuw in Azure Active Directory?](./whats-new.md)
