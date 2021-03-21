---
title: Identiteits gegevens opslag voor Europese klanten-Azure AD
description: Meer informatie over waar Azure Active Directory identiteits gegevens voor haar Europese klanten opslaat.
services: active-directory
author: ajburnle
manager: daveba
ms.author: ajburnle
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 09/15/2020
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7a8b013723707c4a3a087a90674227c3d41c5108
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94836934"
---
# <a name="identity-data-storage-for-european-customers-in-azure-active-directory"></a>Identiteits gegevens opslag voor Europese klanten in Azure Active Directory
Identiteits gegevens worden opgeslagen door Azure AD op een geografische locatie op basis van het adres van uw organisatie bij het abonneren op een micro soft online service, zoals Microsoft 365 en Azure. Voor informatie over waar uw identiteits gegevens worden opgeslagen, kunt u de sectie [waar bevinden zich uw gegevens?](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located) in het micro soft vertrouwens centrum gebruiken.

Voor klanten die een adres in Europa hebben geleverd, houdt Azure AD de meeste identiteits gegevens binnen de Europese data centers. Dit document bevat informatie over alle gegevens die buiten Europa door Azure AD Services zijn opgeslagen.

## <a name="microsoft-azure-ad-multi-factor-authentication"></a>Microsoft Azure AD Multi-Factor Authentication

Voor Azure AD-Multi-Factor Authentication in de Cloud, is verificatie voltooid in het dichtstbijzijnde Data Center voor de gebruiker. Data centers voor Azure AD Multi-Factor Authentication bestaan in Noord-Amerika, Europa en Azië en Stille Oceaan.

* Multi-factor Authentication met telefoon gesprekken afkomstig van Amerikaanse data centers en wordt gerouteerd door wereld wijde providers.
* Multi-factor Authentication met behulp van SMS wordt gerouteerd door globale providers.
* Multi-factor Authentication-aanvragen met behulp van de Microsoft Authenticator app-push meldingen die afkomstig zijn van EU-data centers worden verwerkt in EU-data centers.
    * Leverancierspecifieke services van apparaten, zoals Apple Push meldingen, kunnen buiten Europa zijn.
* Multi-factor Authentication-aanvragen met OATH-codes die afkomstig zijn van EU-data centers, worden gevalideerd in de EU.

Zie [azure multi-factor Authentication User Data Collection](../authentication/howto-mfa-reporting-datacollection.md)(Engelstalig) voor meer informatie over welke gebruikers gegevens worden verzameld door Azure multi-factor Authentication-Server (MFA-server) en Azure AD MFA in de Cloud.

## <a name="password-based-single-sign-on-for-enterprise-applications"></a>Enkele Sign-On op basis van wacht woorden voor bedrijfs toepassingen
 
Als een klant een nieuwe bedrijfs toepassing maakt (of het gaat om een Azure AD-galerie of niet-galerie) en op wacht woord gebaseerde SSO maakt, worden de aanmeldings-URL van de toepassing en aangepaste aanmeldings aanmeld velden opgeslagen in de Verenigde Staten. Zie [eenmalige aanmelding op basis van wacht woorden configureren](../manage-apps/configure-password-single-sign-on-non-gallery-applications.md) voor meer informatie over deze functie

## <a name="microsoft-azure-active-directory-b2c-azure-ad-b2c"></a>Microsoft Azure Active Directory B2C (Azure AD B2C)

Azure AD B2C-beleids configuratie gegevens en sleutel containers worden opgeslagen in de data centers van de VS. Deze bevatten geen persoonlijke gegevens van de gebruiker. Zie voor meer informatie over configuraties voor beleid het artikel [Azure Active Directory B2C: ingebouwde beleidsregels](../../active-directory-b2c/user-flow-overview.md).

## <a name="microsoft-azure-active-directory-b2b-azure-ad-b2b"></a>Microsoft Azure Active Directory B2B (Azure AD B2B) 
    
Azure AD B2B slaat uitnodigingen met Verwissel-en omleidings-URL-informatie op in de VS-data centers. Daarnaast worden e-mail adressen van gebruikers die zich afmelden voor het ontvangen van B2B-uitnodigingen ook opgeslagen in de data centers in de VS.

## <a name="microsoft-azure-active-directory-domain-services-azure-ad-ds"></a>Microsoft Azure Active Directory Domain Services (Azure AD DS)

Azure Active Directory Domain Services slaat gebruikersgegevens op dezelfde locatie op als het door de klant geselecteerde Azure Virtual Network. Dus als het netwerk zich buiten Europa bevindt, worden de gegevens gerepliceerd en opgeslagen buiten Europa.

## <a name="federation-in-microsoft-exchange-server-2013"></a>Federatie in micro soft Exchange Server 2013
    
- Toepassings-id (AppID): een uniek nummer dat wordt gegenereerd door het Azure Active Directory-verificatie systeem om Exchange-organisaties te identificeren.
- Lijst met goedgekeurde federatieve domeinen voor toepassing
- Open bare sleutel voor token ondertekening van toepassing 

Zie het artikel [Federation: Exchange 2013 Help](/exchange/federation-exchange-2013-help) voor meer informatie over Federatie in micro soft Exchange Server.


## <a name="other-considerations"></a>Andere overwegingen

Services en toepassingen die met Azure AD worden geïntegreerd, hebben toegang tot identiteits gegevens. Evalueer elke service en toepassing die u gebruikt om te bepalen hoe identiteits gegevens worden verwerkt door die specifieke service en toepassing en of ze voldoen aan de vereisten voor gegevens opslag van uw bedrijf.

Zie de sectie [Waar bevinden uw gegevens zich?](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located) van het Microsoft Trust Center voor meer informatie over de gegevenslocatie van Microsoft-services.

## <a name="next-steps"></a>Volgende stappen
Zie de volgende artikelen voor meer informatie over de functies en functionaliteit die hierboven worden beschreven:
- [Wat is Multi-Factor Authentication?](../authentication/concept-mfa-howitworks.md)

- [Self-service voor wachtwoord herstel van Azure AD](../authentication/concept-sspr-howitworks.md)

- [Wat is Azure Active Directory B2C?](../../active-directory-b2c/overview.md)

- [Wat is Azure AD B2B-samenwerking?](../external-identities/what-is-b2b.md)

- [Azure Active Directory (AD) Domain Services](../../active-directory-domain-services/overview.md)