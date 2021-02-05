---
title: Lokale partner accounts synchroniseren met de Cloud als B2B-gebruikers-Azure AD
description: Lokaal beheerde externe partners toegang geven tot zowel lokale als cloud resources met dezelfde referenties met Azure AD B2B-samen werking.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 11/03/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 29aeca30e1fbdd28d3f69597d902a9b714056cd5
ms.sourcegitcommit: 1f1d29378424057338b246af1975643c2875e64d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/05/2021
ms.locfileid: "99575920"
---
# <a name="grant-locally-managed-partner-accounts-access-to-cloud-resources-using-azure-ad-b2b-collaboration"></a>Lokaal beheerde partneraccounts toegang verlenen tot cloudresources met behulp van B2B-samenwerking in Azure AD

Voor Azure Active Directory (Azure AD) hebben organisaties met on-premises identiteits systemen traditioneel beheerde partner accounts in hun on-premises Directory. Wanneer u in een dergelijke organisatie apps gaat verplaatsen naar Azure AD, wilt u er zeker van zijn dat uw partners toegang hebben tot de resources die ze nodig hebben. Het is niet belang rijk of de bronnen on-premises of in de Cloud zijn. Daarnaast wilt u dat uw partner gebruikers dezelfde aanmeldings referenties kunnen gebruiken voor zowel on-premises als Azure AD-resources. 

Als u accounts voor uw externe partners in uw on-premises Directory maakt (u maakt bijvoorbeeld een account met de aanmeldings naam ' wmoran ' voor een externe gebruiker met de naam Wendy Moran in uw partners.contoso.com-domein), kunt u deze accounts nu synchroniseren met de Cloud. Met name kunt u Azure AD Connect gebruiken om de partner accounts te synchroniseren met de Cloud, waarmee een gebruikers account wordt gemaakt met User type = Guest. Hierdoor kunnen uw partner gebruikers toegang krijgen tot Cloud bronnen met dezelfde referenties als hun lokale accounts, zonder dat ze meer toegang hebben dan ze nodig hebben.

> [!NOTE]
> Zie ook hoe u [interne gebruikers uitnodigt voor B2B-samen werking](invite-internal-users.md). Met deze functie kunt u interne gast gebruikers uitnodigen om B2B-samen werking te gebruiken, ongeacht of u de accounts van uw on-premises Directory hebt gesynchroniseerd met de Cloud. Zodra de gebruiker de uitnodiging voor het gebruik van B2B-samen werking heeft geaccepteerd, kunnen ze hun eigen identiteiten en referenties gebruiken om zich aan te melden bij de resources waartoe ze toegang willen hebben. U hoeft geen wacht woorden te onderhouden of de levens cyclus van het account te beheren.

## <a name="identify-unique-attributes-for-usertype"></a>Unieke kenmerken voor User type identificeren

Voordat u synchronisatie van het kenmerk User type inschakelt, moet u eerst bepalen hoe u het kenmerk User type van on-premises Active Directory afleiden. Met andere woorden, welke para meters in uw on-premises omgeving zijn uniek voor uw externe deel nemers? Bepaal een para meter die deze externe deel nemers onderscheidt van leden van uw eigen organisatie.

Er zijn twee veelvoorkomende benaderingen voor:

- Geef een niet-gebruikt on-premises Active Directory kenmerk (bijvoorbeeld extensionAttribute1) op om te gebruiken als bron kenmerk. 
- U kunt ook de waarde voor het kenmerk User type afleiden uit andere eigenschappen. U wilt bijvoorbeeld alle gebruikers synchroniseren als gast als hun on-premises Active Directory UserPrincipalName kenmerk eindigt met het domein *\@ partners.contoso.com*.
 
Zie [synchronisatie van User type inschakelen](../hybrid/how-to-connect-sync-change-the-configuration.md#enable-synchronization-of-usertype)voor gedetailleerde kenmerk vereisten. 

## <a name="configure-azure-ad-connect-to-sync-users-to-the-cloud"></a>Azure AD Connect configureren voor het synchroniseren van gebruikers naar de Cloud

Nadat u het unieke kenmerk hebt geïdentificeerd, kunt u Azure AD Connect zodanig configureren dat deze gebruikers naar de cloud worden gesynchroniseerd. Hiermee maakt u een gebruikers account met User type = gast. Vanuit een autorisatie punt van de weer gave zijn deze gebruikers niet te onderscheiden van B2B-gebruikers die zijn gemaakt via het uitnodigings proces van Azure AD B2B-samen werking.

Zie [synchronisatie van User type inschakelen](../hybrid/how-to-connect-sync-change-the-configuration.md#enable-synchronization-of-usertype)voor instructies voor de implementatie.

## <a name="next-steps"></a>Volgende stappen

- [Azure Active Directory B2B-samen werking voor hybride organisaties](hybrid-organizations.md)
- [B2B-gebruikers in Azure AD toegang verlenen tot uw on-premises toepassingen](hybrid-cloud-to-on-premises.md)
- Zie [uw on-premises Directory's integreren met Azure Active Directory](../hybrid/whatis-hybrid-identity.md)voor een overzicht van Azure AD Connect.

