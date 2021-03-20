---
title: Wat is inrichten met Azure Active Directory op basis van HR? | Microsoft Docs
description: Geeft een overzicht van identiteitsinrichting op basis van HR.
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
ms.openlocfilehash: 1ffe8c1397525348e472e965a407909fee36152d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96168444"
---
# <a name="what-is-hr-driven-provisioning"></a>Wat is inrichten op basis van HR?

![Inrichting van HR](./media/what-is-hr-driven-provisioning/cloud2a.png)

Inrichten op basis van HR is het proces waarbij digitale identiteiten worden gemaakt op basis van een human resources-systeem.  De HR-systemen worden de leidende bron voor deze nieuwe digitale identiteiten en zijn vaak het beginpunt voor talloze inrichtingsprocessen.  Bijvoorbeeld: bij een indiensttreding binnen uw bedrijf wordt de nieuwe werknemer in het human resource-systeem gemaakt.  Dit activeert het inrichten van een gebruikersaccount in Active Directory. Azure AD Connect richt dit account vervolgens in op Azure AD, enzovoort.

Inrichten op basis van HR kan on-premises of in de cloud.

## <a name="on-premises-based-hr-provisioning"></a>On-premises inrichten op basis van HR
On-premises inrichten op basis van HR wordt uitgevoerd met behulp van een lokaal HR-systeem en een manier om nieuwe digitale identiteiten in te richten.

HR-systemen zijn beschikbaar in verschillende pakketten en softwarebundels. Ze kunnen gebruikmaken van SQL-servers, LDAP-mappen, enzovoort.

Op dit moment maken on-premises HR-inrichtingsoplossingen van Microsoft gebruik van Microsoft Identity Manager om het inrichten te activeren. Dit gebeurt wanneer er in deze HR-systemen een nieuwe identiteit wordt gemaakt.

Met MIM kunt u gebruikers uit uw on-premises HR-systemen inrichten voor Active Directory of Azure AD.

Zie de documentatie voor [Microsoft Identity Manager](/microsoft-identity-manager/microsoft-identity-manager-2016) voor informatie over Microsoft Identity Manager en de systemen die erdoor worden ondersteund.

[!INCLUDE [active-directory-hr-provisioning.md](../../../includes/active-directory-hr-provisioning.md)]



## <a name="next-steps"></a>Volgende stappen 
- [Wat is Beheer van identiteitslevenscycli](what-is-identity-lifecycle-management.md)
- [Wat is inrichting?](what-is-provisioning.md)
- [Wat is app-inrichting?](what-is-app-provisioning.md)
- [Wat is directory-inrichting?](what-is-inter-directory-provisioning.md)