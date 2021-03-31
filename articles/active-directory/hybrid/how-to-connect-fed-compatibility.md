---
title: Azure AD-federation compatibiliteitslijst
description: Deze pagina heeft id-providers die niet van micro soft zijn en die kunnen worden gebruikt voor het implementeren van eenmalige aanmelding.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: curtand
ms.assetid: 22c8693e-8915-446d-b383-27e9587988ec
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 08/23/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 895e7347feea3190ba9bdc273200d2985dfaa0d1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "89661833"
---
# <a name="azure-ad-federation-compatibility-list"></a>Azure AD-federation compatibiliteitslijst
Azure Active Directory biedt een eenmalige aanmelding en verbeterde beveiliging van toepassings toegang voor Microsoft 365 en andere online services van micro soft voor hybride en Cloud implementaties zonder enige oplossing van derden. Microsoft 365, zoals het meren deel van de online services van micro soft, is geïntegreerd met Azure Active Directory voor adreslijst Services, verificatie en autorisatie. Azure Active Directory biedt ook eenmalige aanmelding bij duizenden SaaS-toepassingen en on-premises webtoepassingen. Zie de Azure Active Directory- [toepassings galerie](https://azuremarketplace.microsoft.com/marketplace/apps/category/azure-active-directory-apps) voor ondersteunde SaaS-toepassingen. 

## <a name="idp-validation"></a>IDP validatie
Als uw organisatie gebruikmaakt van een Federatie oplossing van derden, kunt u eenmalige aanmelding configureren voor uw on-premises Active Directory gebruikers met micro soft Online Services, zoals Microsoft 365, mits de Federatie oplossing van derden compatibel is met Azure Active Directory.  Neem contact op met uw ID-provider voor vragen met betrekking tot compatibiliteit.  Als u een lijst wilt weer geven met id-providers die eerder zijn getest op compatibiliteit met Azure AD, raadpleegt u micro soft [Azure AD Identity provider Compatibility docs](https://www.microsoft.com/download/details.aspx?id=56843). 

>[!NOTE]
>Micro soft biedt geen validatie tests meer voor onafhankelijke ID-providers voor compatibiliteit met Azure Active Directory. Als u uw product wilt testen op interoperabiliteit, raadpleegt u deze [richt lijnen](https://www.microsoft.com/download/details.aspx?id=56843). 

## <a name="next-steps"></a>Volgende stappen

- [Uw on-premises directory's integreren met Azure Active Directory](whatis-hybrid-identity.md)
- [Azure AD Connect en federatie](how-to-connect-fed-whatis.md)
