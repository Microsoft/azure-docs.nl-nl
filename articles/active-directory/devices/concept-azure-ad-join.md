---
title: Wat is een toegevoegd Azure AD-apparaat?
description: Meer informatie over apparaten die zijn toegevoegd aan Azure AD en hoe u met apparaat-identiteits beheer apparaten kunt beheren die toegang hebben tot bronnen in uw omgeving.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 07/20/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2b7cc0535549f2dbcdef4ab043ee506527fdbc5f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "90601449"
---
# <a name="azure-ad-joined-devices"></a>Azure AD-gekoppelde apparaten

Deelname aan Azure AD is bedoeld voor organisaties die alleen in de Cloud of alleen in de Cloud moeten worden opgenomen. Elke organisatie kan aan Azure AD gekoppelde apparaten implementeren, ongeacht de omvang of de branche. Azure AD-deelname werkt zelfs in een hybride omgeving en biedt toegang tot zowel Cloud-als on-premises apps en bronnen.

| Azure AD-Join | Description |
| --- | --- |
| **Definition** | Alleen opgenomen in azure AD waarvoor een organisatie account is vereist om zich aan te melden bij het apparaat |
| **Primaire doel groep** | Alleen geschikt voor Cloud-en hybride organisaties. |
|   | Van toepassing op alle gebruikers in een organisatie |
| **Apparaateigendom** | Organisatie |
| **Besturings systemen** | Alle Windows 10-apparaten behalve Windows 10 Home |
|   | [Virtuele Windows Server 2019-machines die worden uitgevoerd in Azure](howto-vm-sign-in-azure-ad-windows.md) (serverkern wordt niet ondersteund) |
| **Inrichten** | Self-service: Windows OOBE of instellingen |
|   | Bulkinschrijving |
|   | Windows Autopilot |
| **Aanmeldings opties voor apparaten** | Organisatie accounts met: |
|   | Wachtwoord |
|   | Windows Hello voor Bedrijven |
|   | FIDO 2.0-beveiligings sleutels (preview-versie) |
| **Apparaatbeheer** | Beheer van mobiele apparaten (voor beeld: Microsoft Intune) |
|   | Co-beheer met Microsoft Intune en micro soft-eind punt Configuration Manager |
| **Belangrijkste mogelijkheden** | Eenmalige aanmelding voor zowel Cloud-als on-premises resources |
|   | Voorwaardelijke toegang via MDM-inschrijving en MDM-nalevings evaluatie |
|   | Selfservice voor wachtwoord herstel en Windows hello pincode opnieuw instellen op vergrendelings scherm |
|   | Enterprise State Roaming op verschillende apparaten |

Aan Azure AD gekoppelde apparaten zijn aangemeld voor het gebruik van een Azure AD-account in de organisatie. Toegang tot resources in de organisatie kan verder worden beperkt op basis van het Azure AD-account en het [beleid voor voorwaardelijke toegang](../conditional-access/howto-conditional-access-policy-compliant-device.md) dat wordt toegepast op de apparaat-id.

Beheerders kunnen apparaten die zijn toegevoegd aan Azure AD beveiligen en verder beheren met de MDM-hulpprogram ma's (Mobile Device Management), zoals Microsoft Intune of in scenario's voor co-beheer met micro soft endpoint Configuration Manager. Deze hulpprogram ma's bieden een manier om organisatie-vereiste configuraties af te dwingen, zoals het vereisen van een versleutelde opslag, wachtwoord complexiteit, software-installatie en software-updates. Beheerders kunnen organisatie toepassingen beschikbaar maken voor apparaten die lid zijn van Azure AD met behulp van Configuration Manager om [apps te beheren via de Microsoft Store voor bedrijven en onderwijs](/configmgr/apps/deploy-use/manage-apps-from-the-windows-store-for-business).

Azure AD-deelname kan worden uitgevoerd met selfservice opties zoals out of Box Experience (OOBE), bulk inschrijving of [Windows auto pilot](/intune/enrollment-autopilot).

Apparaten die aan Azure AD zijn toegevoegd, kunnen nog steeds toegang tot uw on-premises resources hebben bij het gebruik van eenmalige aanmelding wanneer ze zich op het netwerk van de organisatie bevinden. Apparaten die zijn toegevoegd aan Azure AD, kunnen nog steeds worden geverifieerd voor on-premises servers zoals bestanden, printers en andere toepassingen.

## <a name="scenarios"></a>Scenario's

Hoewel Azure AD-koppeling voornamelijk is bedoeld voor organisaties die geen on-premises Windows Server Active Directory-infrastructuur hebben, kunt u dit beslist gebruiken in scenario’s waarbij:

- U wilt overstappen op cloudgebaseerde infrastructuur met behulp van Azure AD en MDM zoals Intune.
- U geen on-premises domeinkoppeling kunt gebruiken, bijvoorbeeld als u mobiele apparaten zoals tablets en telefoons onder controle moet krijgen.
- Uw gebruikers moeten voornamelijk toegang hebben tot Microsoft 365 of andere SaaS-apps die zijn geïntegreerd met Azure AD.
- U een groep gebruikers in Azure AD wilt beheren in plaats van in Active Directory. Dit scenario kan bijvoorbeeld van toepassing zijn op seizoen arbeiders, aannemers of studenten.
- U koppelingsmogelijkheden wilt bieden aan medewerkers in externe filialen met beperkte on-premises infrastructuur.

U kunt apparaten die zijn toegevoegd aan Azure AD configureren voor alle Windows 10-apparaten, met uitzonde ring van Windows 10 Home.

Het doel van Azure AD-gekoppelde apparaten is vereenvoudiging van:

- Windows-implementaties van apparaten die eigendom van het bedrijf zijn
- Toegang tot apps en bronnen van de organisatie vanaf elk Windows-apparaat
- Cloudgebaseerd beheer van apparaten die eigendom van het bedrijf zijn
- Gebruikers om zich aan te melden bij hun apparaten met hun Azure AD-of gesynchroniseerde Active Directory werk-of school account.

![Azure AD-gekoppelde apparaten](./media/concept-azure-ad-join/azure-ad-joined-device.png)

Azure AD-koppeling kan via een van de volgende methoden worden geïmplementeerd:

- [Windows Autopilot](/windows/deployment/windows-autopilot/windows-10-autopilot)
- [Bulkimplementatie](/intune/windows-bulk-enroll)
- [Selfservice-ervaring](azuread-joined-devices-frx.md)

## <a name="next-steps"></a>Volgende stappen

- [Uw implementatie van Azure AD-deelname plannen](azureadjoin-plan.md)
- [De lokale groep Administrators beheren op apparaten die zijn toegevoegd aan Azure AD](assign-local-admin.md)
- [Apparaat-id's beheren in Azure Portal](device-management-azure-portal.md)
- [Langdurig inactieve apparaten beheren in Azure AD](manage-stale-devices.md)
