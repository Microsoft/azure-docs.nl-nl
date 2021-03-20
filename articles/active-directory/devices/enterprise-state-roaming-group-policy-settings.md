---
title: Groepsbeleid-en MDM-instellingen voor ESR-Azure Active Directory
description: Beheer instellingen voor Enterprise State Roaming
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: reference
ms.date: 02/12/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: na
ms.collection: M365-identity-device-management
ms.openlocfilehash: ab24b3113f9dc69b8f3907037e228ba212a03106
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "85252930"
---
# <a name="group-policy-and-mdm-settings"></a>groepsbeleid-en MDM-instellingen

Gebruik de volgende instellingen voor groeps beleid en de Mobile Device Management (MDM) alleen voor apparaten in bedrijfs eigendom omdat deze beleids regels worden toegepast op het hele apparaat van de gebruiker. Het Toep assen van een MDM-beleid om instellingen synchronisatie uit te scha kelen voor een persoonlijk apparaat, wordt een negatieve invloed op het gebruik van het apparaat. Daarnaast worden ook andere gebruikers accounts op het apparaat door het beleid beïnvloed.

Ondernemingen die roaming voor persoonlijke (onbeheerde) apparaten willen beheren, kunnen de Azure Portal gebruiken om roaming in of uit te scha kelen, in plaats van groepsbeleid of MDM te gebruiken.
In de volgende tabellen worden de beschik bare beleids instellingen beschreven.

> [!NOTE]
> Dit artikel is van toepassing op de op HTML gebaseerde browser van micro soft Edge die is geïntroduceerd in Windows 10 in juli 2015. Het artikel is niet van toepassing op de nieuwe micro soft Edge-browser op basis van chroom, uitgebracht op 15 januari 2020. Zie het artikel [micro soft Edge Sync](/deployedge/microsoft-edge-enterprise-sync)(Engelstalig) voor meer informatie over het synchronisatie gedrag voor de nieuwe micro soft Edge.

## <a name="mdm-settings"></a>MDM-instellingen

De MDM-beleids instellingen zijn van toepassing op Windows 10 en Windows 10 Mobile.  Ondersteuning voor Windows 10 Mobile bestaat alleen voor zwerven op basis van Microsoft-account via het OneDrive-account van de gebruiker. Raadpleeg [apparaten en eind punten](enterprise-state-roaming-windows-settings-reference.md) voor meer informatie over welke apparaten worden ondersteund voor synchronisatie op basis van Azure AD.

| Naam | Beschrijving |
| --- | --- |
| Verbinding met micro soft-account toestaan |Hiermee kunnen gebruikers verifiëren met behulp van een Microsoft-account op het apparaat |
| Synchronisatie van mijn instellingen toestaan |Hiermee kunnen gebruikers Windows-instellingen en app-gegevens roamen; Als u dit beleid uitschakelt, worden de synchronisatie en back-ups op mobiele apparaten uitgeschakeld |

## <a name="group-policy-settings"></a>Instellingen voor groepsbeleid

De instellingen voor groeps beleid zijn van toepassing op Windows 10-apparaten die lid zijn van een Active Directory domein. De tabel bevat ook verouderde instellingen die zouden moeten worden gebruikt voor het beheren van synchronisatie-instellingen, maar die niet werken voor Enterprise State Roaming voor Windows 10, die worden vermeld bij ' niet gebruiken ' in de beschrijving.

Deze instellingen bevinden zich in: `Computer Configuration > Administrative Templates > Windows Components > Sync your settings` 

| Naam | Beschrijving |
| --- | --- |
| Accounts: micro soft-accounts blok keren |Met deze beleids instelling wordt voor komen dat gebruikers nieuwe micro soft-accounts toevoegen op deze computer |
| Niet synchroniseren |Hiermee voor komt u dat gebruikers Windows-instellingen en app-gegevens kunnen roamen |
| Geen persoonlijke instellingen synchroniseren |Hiermee schakelt u de synchronisatie van de groep Thema's uit |
| Geen browser instellingen synchroniseren |Hiermee schakelt u het synchroniseren van de Internet Explorer-groep uit |
| Geen wacht woorden synchroniseren |Hiermee schakelt u het synchroniseren van de groep wacht woorden uit |
| Andere Windows-instellingen niet synchroniseren |Hiermee schakelt u de synchronisatie van een andere Windows-instellingen groep uit |
| Geen persoonlijke bureau bladen synchroniseren |Niet gebruiken; heeft geen effect |
| Niet synchroniseren via verbindingen met data limiet |Hiermee schakelt u roaming uit op verbindingen met data limiet, zoals mobiele 3G |
| Apps niet synchroniseren |Niet gebruiken; heeft geen effect |
| App-instellingen niet synchroniseren |Hiermee wordt roaming van app-gegevens uitgeschakeld |
| Start instellingen niet synchroniseren |Niet gebruiken; heeft geen effect |

## <a name="next-steps"></a>Volgende stappen

Zie [overzicht van ENTER prise State roaming](enterprise-state-roaming-overview.md)voor een overzicht.
