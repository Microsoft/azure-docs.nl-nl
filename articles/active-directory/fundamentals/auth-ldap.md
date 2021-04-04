---
title: LDAP-verificatie met Azure Active Directory
description: Architectuur richtlijnen voor het bereiken van LDAP-verificatie met Azure Active Directory.
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 10/10/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: f209d394e1a0c2c4ddde9cbf8df2704647e2822a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96168709"
---
# <a name="ldap-authentication-with-azure-active-directory"></a>LDAP-verificatie met Azure Active Directory

Lightweight Directory Access Protocol (LDAP) is een toepassings protocol voor het werken met verschillende Directory Services. Directory Services, zoals Active Directory, [opslaan van gebruikers-en account gegevens](https://www.dnsstuff.com/active-directory-service-accounts)en beveiligings informatie, zoals wacht woorden. Met de service kan de informatie vervolgens worden gedeeld met andere apparaten in het netwerk. Zakelijke toepassingen zoals e-mail, Customer Relationship managers (CRMs) en HR-software (Human Resources) kunnen gebruikmaken van LDAP om informatie te verifiëren, te openen en te zoeken. 

Azure Active Directory (Azure AD) ondersteunt dit patroon via Azure AD Domain Services (AD DS). Hiermee kunnen organisaties die een strategie voor Cloud-eerste gebruiken, hun omgeving moderniseren door hun on-premises LDAP-bronnen naar de cloud te verplaatsen. De onmiddellijk voor delen zijn: 

* Geïntegreerd met Azure AD. Toevoegingen van gebruikers en groepen, of kenmerk wijzigingen aan hun objecten worden automatisch gesynchroniseerd van uw Azure AD-Tenant naar AD DS. Wijzigingen in objecten in on-premises Active Directory worden gesynchroniseerd met Azure AD en vervolgens naar AD DS.

* Vereenvoudig bewerkingen. Hiermee vermindert u de nood zaak om on-premises infra structuur hand matig te blijven gebruiken en te patchen. 

* Betrouwbaar. U krijgt beheerde, Maxi maal beschik bare Services 

## <a name="use-when"></a>Wanneer gebruiken

U moet voor een toepassing of service gebruikmaken van LDAP-verificatie.

![Diagram van architectuur](./media/authentication-patterns/ldap-auth.png)

## <a name="components-of-system"></a>Onderdelen van systeem

* **Gebruiker**: toegang tot toepassingen die afhankelijk zijn van LDAP via een browser.

* **Webbrowser**: de interface waarmee de gebruiker communiceert om toegang te krijgen tot de externe URL van de toepassing.

* **Virtual Network**: een particulier netwerk in azure waarmee de oudere toepassing LDAP-services kan verbruiken. 

* **Oudere toepassingen**: toepassingen of server werkbelastingen waarvoor LDAP is geïmplementeerd in een virtueel netwerk in azure, of die zicht hebben op de AD DS instantie ip's via netwerk routes. 

* **Azure AD**: synchroniseert identiteits gegevens van de on-premises Directory van de organisatie via Azure AD Connect.

* **Azure AD Domain Services (AD DS)**: voert een eenrichtings synchronisatie uit vanuit Azure AD om toegang te bieden tot een centrale set gebruikers, groepen en referenties. Het AD DS-exemplaar wordt toegewezen aan een virtueel netwerk. Toepassingen, services en virtuele machines in azure die verbinding maken met het virtuele netwerk dat is toegewezen aan AD DS, kunnen gebruikmaken van algemene AD DS functies zoals LDAP, domein deelname, groeps beleid, Kerberos en NTLM-verificatie.
   > [!NOTE]
   >  In omgevingen waar de organisatie geen wacht woord-hashes kan synchroniseren of gebruikers zich aanmelden met Smart Cards, raden we u aan een bron-forest te gebruiken in AD DS. 

* **Azure AD Connect**: een hulp programma voor het synchroniseren van on-premises identiteits gegevens naar Microsoft Azure AD. De implementatie wizard en de begeleide ervaringen helpen u bij het configureren van vereisten en onderdelen die vereist zijn voor de verbinding, waaronder synchronisatie en aanmelding van Active Directory naar Azure AD. 

* **Active Directory**: Directory service die [on-premises identiteits gegevens opslaat, zoals gebruikers-en account gegevens](https://www.dnsstuff.com/active-directory-service-accounts), en beveiligings informatie, zoals wacht woorden.

## <a name="implement-ldap-authentication-with-azure-ad"></a>LDAP-verificatie implementeren met Azure AD

* [Een Azure AD DS-exemplaar maken en configureren](../../active-directory-domain-services/tutorial-create-instance.md) 

* [Virtuele netwerken configureren voor een Azure AD DS-exemplaar](../../active-directory-domain-services/tutorial-configure-networking.md) 

* [Secure LDAP configureren voor een beheerd domein in azure AD DS](../../active-directory-domain-services/tutorial-configure-ldaps.md) 

* [Een uitgaande forest-vertrouwens relatie maken met een on-premises domein in azure AD DS](../../active-directory-domain-services/tutorial-create-forest-trust.md)

