---
title: Wat is een hybride lid van Azure AD dat is gekoppeld aan het apparaat?
description: Meer informatie over hoe het beheer van apparaat-id's u kan helpen bij het beheren van apparaten die toegang hebben tot resources in uw omgeving.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 06/27/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 259a1324c412dad40d32a8b8e026d84e6f5aa066
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "85554919"
---
# <a name="hybrid-azure-ad-joined-devices"></a>Hybride Azure AD-gekoppelde apparaten

Al meer dan tien jaar gebruiken vele organisaties de domeinkoppeling naar hun on-premises Active Directory om:

- IT-afdelingen vanaf een centrale locatie apparaten te laten beheren die eigendom van het bedrijf zijn.
- Gebruikers zich bij hun apparaat te laten aanmelden met hun Active Directory-werkaccount of -schoolaccount.

Organisaties met een on-premises footprint zijn doorgaans afhankelijk van de Imaging-methoden om apparaten in te richten en vaak gebruiken ze **Configuration Manager** of **groeps beleid (GP)** om ze te beheren.

Als uw omgeving een on-premises AD-voetafdruk heeft en u ook wilt profiteren van de mogelijkheden die Azure Active Directory biedt, kunt u hybride Azure AD-gekoppelde apparaten implementeren. Deze apparaten, zijn apparaten die zijn gekoppeld aan uw on-premises Active Directory en zijn geregistreerd bij uw Azure Active Directory.

| Hybride Azure AD-deelname | Description |
| --- | --- |
| **Definition** | Lid van on-premises AD en Azure AD waarvoor een organisatie account is vereist om zich aan te melden bij het apparaat |
| **Primaire doel groep** | Geschikt voor hybride organisaties met een bestaande on-premises AD-infra structuur |
|   | Van toepassing op alle gebruikers in een organisatie |
| **Apparaateigendom** | Organisatie |
| **Besturings systemen** | Windows 10, 8,1 en 7 |
|   | Windows Server 2008/R2, 2012/R2, 2016 en 2019 |
| **Inrichten** | Windows 10, Windows Server 2016/2019 |
|   | Domein deelname door IT en autolid via Azure AD Connect of ADFS-configuratie |
|   | Domein deelname door Windows auto pilot en autojoin's via Azure AD Connect of ADFS-configuratie |
|   | Windows 8,1, Windows 7, Windows Server 2012 R2, Windows Server 2012 en Windows Server 2008 R2-MSI vereist |
| **Aanmeldings opties voor apparaten** | Organisatie accounts met: |
|   | Wachtwoord |
|   | Windows hello voor bedrijven voor Win10 |
| **Apparaatbeheer** | Groepsbeleid |
|   | Configuration Manager zelfstandig of gezamenlijk beheer met Microsoft Intune |
| **Belangrijkste mogelijkheden** | Eenmalige aanmelding voor zowel Cloud-als on-premises resources |
|   | Voorwaardelijke toegang via domein deelname of intune als gezamenlijk beheerd |
|   | Selfservice voor wachtwoord herstel en Windows hello pincode opnieuw instellen op vergrendelings scherm |
|   | Enterprise State Roaming op verschillende apparaten |

![Hybride Azure AD-gekoppelde apparaten](./media/concept-azure-ad-join-hybrid/azure-ad-hybrid-joined-device.png)

## <a name="scenarios"></a>Scenario's

Gebruik Azure AD Hybrid joind devices als:

- U Win32-apps op deze apparaten hebt geïmplementeerd die afhankelijk zijn van Active Directory-machineverificatie.
- U wilt door gaan met het gebruik van groepsbeleid voor het beheren van de apparaatconfiguratie.
- U wilt door gaan met het gebruik van bestaande imaging-oplossingen voor het implementeren en configureren van apparaten.
- U moet naast Windows 10 Windows 7-en 8,1-apparaten op lagere niveaus ondersteunen

## <a name="next-steps"></a>Volgende stappen

- [Uw implementatie van hybride Azure AD-deelname plannen](hybrid-azuread-join-plan.md)
- [Apparaat-id's beheren in Azure Portal](device-management-azure-portal.md)
- [Langdurig inactieve apparaten beheren in Azure AD](manage-stale-devices.md)
