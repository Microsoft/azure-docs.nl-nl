---
title: Wat is inter-directory inrichten met Azure Active Directory? | Microsoft Docs
description: Geeft een overzicht van identiteitsinrichting tussen directory’s.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: overview
ms.date: 10/30/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4246bc4c62fd8e5e73ff18e383b8bf115d25ede8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101643829"
---
# <a name="what-is-inter-directory-provisioning"></a>Wat is directory-inrichting?

Een directory is een gedeelde informatie-infrastructuur die wordt gebruikt voor het zoeken, beheren en organiseren van items en netwerkbronnen.  Voorbeelden van toepassingen die gebruikmaken van directoryservices, zijn Microsoft Active Directory en Azure AD.  Identiteiten helpen directory-systemen te bepalen wie er toegang heeft tot wat en wie specifieke resources mag gebruiken.

Inter-directory-inrichten is het inrichten van een identiteit tussen twee verschillende directoryservices-systemen.   Het meest voorkomende scenario voor inter-directory-inrichten is wanneer een gebruiker die al in Active Directory staat wordt ingericht in Azure AD. Deze inrichting kan worden uitgevoerd door agents zoals Azure AD Connect-synchronisatie of Azure AD Connect-cloudinrichting.

Met inter-directory-inrichten kunnen we [hybride identiteits](../hybrid/whatis-hybrid-identity.md)omgevingen maken.


## <a name="what-types-of-inter-directory-provisioning-does-azure-ad-support"></a>Welke typen inter-directory-inrichten ondersteunt Azure AD

Azure AD biedt momenteel ondersteuning voor drie methoden voor het uitvoeren van inter-directory-inrichten. Deze methoden zijn:

- [Azure AD Connect](../hybrid/whatis-azure-ad-connect.md) - het Microsoft-hulpprogramma dat is ontworpen om uw hybride identiteit te realiseren, waaronder inter-directory-inrichten van Active Directory naar Azure AD.

- [Azure AD Connect Cloud-inrichten](../cloud-sync/what-is-cloud-sync.md) - een nieuwe Microsoft-agent die is ontworpen om uw doelstellingen voor een hybride identiteit te realiseren.  Het biedt een lichtgewicht inter-directory-inrichtingservaring tussen Active Directory en Azure AD.

- [Microsoft Identity Manager](/microsoft-identity-manager/microsoft-identity-manager-2016) - de on-premises oplossing voor identiteits- en toegangsbeheer van Microsoft waarmee u de gebruikers, referenties, het beleid en de toegang binnen uw organisatie kunt beheren. Daarnaast biedt MIM geavanceerde mogelijkheden voor inter-directory-inrichting om hybride identiteitsomgevingen te maken voor Active Directory, Azure AD en andere directory's.

### <a name="key-benefits"></a>Belangrijkste voordelen

Deze mogelijkheid inter-directory-inrichting biedt de volgende belangrijke zakelijke voordelen:

- [Wachtwoord-hashsynchronisatie](../hybrid/whatis-phs.md): een aanmeldingsmethode waarmee de hash van een on-premises AD-wachtwoord van een gebruiker wordt gesynchroniseerd met Azure AD.
- [Pass through-verificatie](../hybrid/how-to-connect-pta.md): een aanmeldingsmethode waarmee gebruikers hetzelfde wachtwoord on-premises en in de cloud kunnen gebruiken, zonder dat er een extra infrastructuur van een federatieve omgeving vereist is.
- [Integratie van Federation](../hybrid/how-to-connect-fed-whatis.md) - kan worden gebruikt om een hybride omgeving te configureren met behulp van een on-premises AD FS-infrastructuur. Het onderdeel biedt ook beheermogelijkheden voor AD FS, zoals het verlengen van certificaten en aanvullende implementaties van AD FS-servers.
- [Synchronisatie](../hybrid/how-to-connect-sync-whatis.md): verantwoordelijk voor het maken van gebruikers, groepen en andere objecten.  Het kan ook gebruikt worden om te controleren of de identiteitsinformatie van uw on-premises gebruikers en groepen overeenkomt met de cloud.  Deze synchronisatie bevat ook wachtwoord-hashes.
- [Statuscontrole](../hybrid/whatis-azure-ad-connect.md) - biedt robuuste bewaking en een centrale locatie in de Azure Portal om deze activiteit weer te geven. 


## <a name="next-steps"></a>Volgende stappen 
- [Wat is Beheer van identiteitslevenscycli](what-is-identity-lifecycle-management.md)
- [Wat is inrichting?](what-is-provisioning.md)
- [Wat is inrichting op basis van HR?](what-is-hr-driven-provisioning.md)
- [Wat is app-inrichting?](what-is-app-provisioning.md)