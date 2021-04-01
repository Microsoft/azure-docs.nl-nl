---
title: B2B-samen werking voor hybride organisaties-Azure AD
description: Geef partners toegang tot zowel on-premises als cloud resources met Azure AD B2B-samen werking.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 04/26/2018
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: 427f7ad4d6a1b9839b1197ef9f7ca15400ea0f59
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "87908618"
---
# <a name="azure-active-directory-b2b-collaboration-for-hybrid-organizations"></a>Azure Active Directory B2B-samen werking voor hybride organisaties

Azure Active Directory (Azure AD) B2B-samen werking maakt het eenvoudig voor u om uw externe partners toegang te geven tot apps en resources in uw organisatie. Dit geldt zelfs in een hybride configuratie waarbij u zowel on-premises als cloud resources hebt. Het maakt niet uit of u externe partner accounts lokaal beheert in uw on-premises identiteits systeem, of dat u de externe accounts in de Cloud beheert als Azure AD B2B-gebruikers. U kunt deze gebruikers nu toegang verlenen tot bronnen op beide locaties, met dezelfde aanmeldings referenties voor beide omgevingen.

## <a name="grant-b2b-users-in-azure-ad-access-to-your-on-premises-apps"></a>B2B-gebruikers in azure AD toegang verlenen tot uw on-premises apps

Als uw organisatie gebruikmaakt van Azure AD B2B-samenwerkings mogelijkheden om gast gebruikers van partner organisaties uit te nodigen voor uw Azure AD, kunt u deze B2B-gebruikers nu toegang bieden tot on-premises apps.

Voor apps die gebruikmaken van SAML-verificatie kunt u deze apps beschikbaar maken voor B2B-gebruikers via de Azure Portal, met behulp van Azure AD-toepassingsproxy voor authenticatie.

Voor apps die gebruikmaken van geïntegreerde Windows-verificatie (IWA) met Kerberos-beperkte delegering (KCD), kunt u ook Azure AD proxy gebruiken voor verificatie. Om autorisatie te kunnen gebruiken, is echter een gebruikers object vereist in het on-premises Windows Server-Active Directory. Er zijn twee methoden die u kunt gebruiken om lokale gebruikers objecten te maken die uw B2B-gast gebruikers vertegenwoordigen.

- U kunt Microsoft Identity Manager (MIM) 2016 SP1 en de MIM-beheer agent voor Microsoft Graph gebruiken.
- U kunt een Power shell-script gebruiken. (Voor deze oplossing is MIM niet vereist.)

Zie voor meer informatie over het implementeren van deze oplossingen [B2B-gebruikers in azure AD-toegang verlenen aan uw on-premises toepassingen](hybrid-cloud-to-on-premises.md).

## <a name="grant-locally-managed-partner-accounts-access-to-cloud-resources"></a>Lokaal beheerde partner accounts toegang verlenen tot cloud resources

Voor Azure AD hebben organisaties met on-premises identiteits systemen traditioneel beheerde partner accounts in hun on-premises Directory. Als u een dergelijke organisatie bent, wilt u er zeker van zijn dat uw partners toegang blijven houden wanneer u uw apps en andere resources verplaatst naar de Cloud. In het ideale geval wilt u dat deze gebruikers dezelfde set referenties gebruiken om toegang te krijgen tot zowel Cloud-als on-premises resources. 

We bieden nu methoden aan waar u Azure AD Connect kunt gebruiken om deze lokale accounts te synchroniseren met de Cloud als ' gast gebruikers ', waarbij de accounts zich gedragen als Azure AD B2B-gebruikers.

Om uw bedrijfs gegevens te helpen beveiligen, kunt u de toegang tot de juiste resources beheren en verificatie beleidsregels configureren die deze gast gebruikers verschillend van uw werk nemers behandelen.

Zie [lokaal beheerde partner accounts toegang verlenen tot cloud resources met Azure AD B2B Collaboration](hybrid-on-premises-to-cloud.md)voor meer informatie over de implementatie.
 
## <a name="next-steps"></a>Volgende stappen

- [B2B-gebruikers in Azure AD toegang verlenen tot uw on-premises toepassingen](hybrid-cloud-to-on-premises.md)
- [Lokaal beheerde partneraccounts toegang verlenen tot cloudresources met behulp van B2B-samenwerking in Azure AD](hybrid-on-premises-to-cloud.md)


