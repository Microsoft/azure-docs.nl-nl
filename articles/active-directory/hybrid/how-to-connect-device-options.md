---
title: 'Azure AD Connect: opties voor apparaten | Microsoft Docs'
description: Dit document bevat informatie over de apparaten die beschikbaar zijn in Azure AD Connect
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: billmath
ms.assetid: c0ff679c-7ed5-4d6e-ac6c-b2b6392e7892
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 09/13/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 90d8cc39174f6392b0c532948305fa713535f0c9
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "89279224"
---
# <a name="azure-ad-connect-device-options"></a>Azure AD Connect: opties voor apparaten

De volgende documentatie bevat informatie over de verschillende beschik bare opties voor apparaten in Azure AD Connect. U kunt Azure AD Connect gebruiken om de volgende twee bewerkingen te configureren: 
* **Hybride Azure AD join**: als uw omgeving een on-premises AD-footprint heeft en u de voor delen van Azure ad wilt bieden, kunt u aan hybride Azure AD gekoppelde apparaten implementeren. Deze apparaten zijn gekoppeld aan uw on-premises Active Directory en uw Azure Active Directory.
* **Write-back van apparaat**: apparaat write-back wordt gebruikt voor het inschakelen van voorwaardelijke toegang op basis van apparaten die zijn AD FS (2012 R2 of hoger) beveiligde apparaten

## <a name="configure-device-options-in-azure-ad-connect"></a>Opties voor apparaten configureren in Azure AD Connect

1.  Voer Azure AD Connect uit. Selecteer op de pagina **extra taken** de optie **device-opties configureren**.  Klik op **Volgende**.
    ![Opties voor apparaten configureren](./media/how-to-connect-device-options/deviceoptions.png) 

    Op de pagina **overzicht** worden de details weer gegeven.
    ![Overzicht](./media/how-to-connect-device-options/deviceoverview.png)

    >[!NOTE]
    > De nieuwe opties voor het configureren van apparaten zijn alleen beschikbaar in versie 1.1.819.0 en nieuwer.

2.  Nadat u de referenties voor Azure AD hebt opgegeven, kunt u de bewerking die moet worden uitgevoerd op de pagina opties voor het apparaat kiezen.
    ![Bewerkingen voor apparaten](./media/how-to-connect-device-options/deviceoptionsselection.png)

## <a name="next-steps"></a>Volgende stappen

* [Hybride Azure AD-deelname configureren](../devices/hybrid-azuread-join-plan.md)
* [Write-back van apparaat configureren/uitschakelen](how-to-connect-device-writeback.md)