---
title: Wat is federatie met Azure AD? | Microsoft Docs
description: Geeft een beschrijving van federatie met Azure AD.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 11/28/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: eb1c3bc9f89db3f4b694803a63293a5537d4b98b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "89278749"
---
# <a name="what-is-federation-with-azure-ad"></a>Wat is federatie met Azure AD?

Federatie is een verzameling van domeinen waarmee een vertrouwensrelatie is ingesteld. Het niveau van de vertrouwensrelatie kan verschillen, maar omvat gewoonlijk verificatie en bijna altijd autorisatie. Een typische federatie kan een aantal organisaties omvatten waarmee een vertrouwensrelatie is ingesteld voor gedeelde toegang tot een reeks resources.

U kunt uw on-premises-omgeving met Azure AD federeren en deze federatie gebruiken voor verificatie en autorisatie.  Deze aanmeldingsmethode zorgt ervoor dat alle gebruikersverificatie on-premises wordt uitgevoerd.  Via deze methode kunnen beheerders strengere niveaus van toegangsbeheer implementeren. Federatie met AD FS en PingFederate is beschikbaar.

![Federatieve identiteit](./media/whatis-hybrid-identity/federated-identity.png)


> [!TIP]
> Als u besluit federatie met Active Directory Federation Services (AD FS) te gebruiken, kunt u optioneel als back-up wachtwoordhashsynchronisatie instellen in geval de AD FS-infrastructuur mislukt.


## <a name="next-steps"></a>Volgende stappen

- [Wat is hybride identiteit?](./whatis-hybrid-identity.md)
- [Wat zijn Azure AD Connect en Connect Health?](whatis-azure-ad-connect.md)
- [Wat is synchronisatie van wachtwoord-hashes?](whatis-phs.md)
- [Wat is federatie?](whatis-fed.md)
- [Wat is eenmalige aanmelding?](how-to-connect-sso.md)
- [Hoe federatie werkt](how-to-connect-fed-whatis.md)
- [Federatie met PingFederate](how-to-connect-install-custom.md#configuring-federation-with-pingfederate)