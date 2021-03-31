---
title: Wat is Enterprise State Roaming in Azure Active Directory?
description: Enterprise State Roaming biedt gebruikers een uniforme ervaring op hun Windows-apparaten
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: overview
ms.date: 02/12/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: na
ms.collection: M365-identity-device-management
ms.openlocfilehash: c22baf0a08718883f0c0c9844cc395f607b5b20d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "77194276"
---
# <a name="what-is-enterprise-state-roaming"></a>Wat is Enterprise State Roaming?

Met Windows 10 krijgen gebruikers van [Azure Active Directory (Azure AD)](../fundamentals/active-directory-whatis.md) de mogelijkheid om hun gebruikersinstellingen en toepassingsinstellingen veilig te synchroniseren met de cloud. Enterprise State Roaming biedt gebruikers een uniforme ervaring op hun Windows-apparaten en verkort de benodigde tijd voor het configureren van een nieuw apparaat. Enterprise State Roaming werkt op dezelfde manier als de standaardfunctie [Synchronisatie van de consumenteninstellingen](https://go.microsoft.com/fwlink/?linkid=2015135) die voor het eerst is geïntroduceerd in Windows 8. Enterprise State Roaming biedt daarnaast de volgende opties:

* **Scheiding van bedrijfs-en consumentengegevens**: organisaties hebben controle over hun gegevens en er is geen vermenging van bedrijfsgegevens in een consumentencloudaccount of consumentengegevens in een bedrijfscloudaccount.
* **Verbeterde beveiliging**: gegevens worden automatisch versleuteld voordat deze het Windows 10-apparaat van de gebruiker verlaten met behulp van Azure Rights Management (Azure RMS), en de gegevens blijven at-rest versleuteld in de cloud. Alle inhoud blijft at-rest versleuteld in de cloud, met uitzondering van de naamruimten, zoals namen van instellingen en Windows-apps.  
* **Beter beheer en betere bewaking**: voorziet in het beheer en de zichtbaarheid van door wie en op welke apparaten de instellingen in uw organisatie via de integratie van de Azure AD-portal worden gesynchroniseerd. 

Enterprise State Roaming is beschikbaar in meerdere Azure-regio's. U kunt de bijgewerkte lijst met beschikbare regio‘s vinden op de pagina [Azure-services per regio](https://azure.microsoft.com/regions/#services) onder Azure Active Directory.

| Artikel | Beschrijving |
| --- | --- |
| [Enterprise state roaming inschakelen in Azure Active Directory](enterprise-state-roaming-enable.md) |Enterprise State Roaming is beschikbaar voor elke organisatie met een Premium-abonnement voor Azure Active Directory (Azure AD). Zie de pagina [Azure AD-product](https://azure.microsoft.com/services/active-directory) voor meer informatie over het verkrijgen van een Azure AD-abonnement. |
| [Veelgestelde vragen over instellingen en gegevensroaming](enterprise-state-roaming-faqs.md) |In dit artikel vindt u antwoorden op enkele vragen die IT-beheerders mogelijk hebben over instellingen en synchronisatie van app-gegevens. |
| [Groepsbeleid en MDM-instellingen voor de synchronisatie van instellingen](enterprise-state-roaming-group-policy-settings.md) |Windows 10 biedt groepsbeleid-en MDM-beleidsinstellingen (MDM: Mobile Device Management) om de synchronisatie van instellingen te beperken. |
| [Naslaginformatie over roaminginstellingen voor Windows 10](enterprise-state-roaming-windows-settings-reference.md) |Een lijst met instellingen waarvoor roaming wordt ingesteld en/of waarvan een back-up wordt gemaakt in Windows 10. |
| [Problemen oplossen](enterprise-state-roaming-troubleshooting.md) |Dit artikel behandelt enkele eenvoudige stappen voor het oplossen van problemen en bevat een lijst met bekende problemen. |

## <a name="next-steps"></a>Volgende stappen

Zie [Enterprise State Roaming inschakelen](enterprise-state-roaming-enable.md) voor meer informatie over het inschakelen van de Enterprise State Roaming.
