---
title: Azure MFA-server en Vpn's van derden-Azure Active Directory
description: Stapsgewijze configuratie handleidingen voor de Azure MFA-server om te integreren met Cisco, Citrix en Juniper.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1ba0d359079a8999b3c4f2a41f4beadb835ccacc
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/18/2020
ms.locfileid: "94838226"
---
# <a name="advanced-scenarios-with-azure-mfa-server-and-third-party-vpn-solutions"></a>Geavanceerde scenario's met Azure MFA-server en VPN-oplossingen van derden

Azure Multi-Factor Authentication-server (Azure MFA-server) kan worden gebruikt om naadloos verbinding te maken met verschillende VPN-oplossingen van derden. Dit artikel richt zich op Cisco &reg; ASA VPN-apparaat, Citrix NetScaler SSL VPN-apparaat en de Juniper netwerken beveiligde toegang/Pulse Secure SSL VPN-apparaat veilig verbinden. We hebben configuratie handleidingen gemaakt om deze drie algemene apparaten te verhelpen. De Azure MFA-server kan ook worden geïntegreerd met de meeste andere systemen die gebruikmaken van RADIUS, LDAP, IIS of op claims gebaseerde verificatie om te AD FS. Meer informatie vindt u in de [Azure MFA-server configuraties](howto-mfaserver-deploy.md#next-steps).

> [!IMPORTANT]
> Vanaf 1 juli 2019 biedt micro soft geen MFA-server meer voor nieuwe implementaties. Nieuwe klanten die multi-factor Authentication (MFA) willen vereisen tijdens aanmeldings gebeurtenissen, moeten gebruikmaken van Azure AD-Multi-Factor Authentication in de Cloud.
>
> Zie [zelf studie: aanmeldings gebeurtenissen voor gebruikers beveiligen met Azure AD multi-factor Authentication](tutorial-enable-azure-mfa.md)om aan de slag te gaan met MFA op basis van de Cloud.
>
> Als u op de cloud gebaseerde MFA gebruikt, raadpleegt u [uw VPN-infra structuur integreren met Azure MFA](howto-mfa-nps-extension-vpn.md).
>
> Bestaande klanten die MFA-server vóór 1 juli hebben geactiveerd 2019, kunnen de meest recente versie downloaden, toekomstige updates en activerings referenties genereren.

## <a name="cisco-asa-vpn-appliance-and-azure-mfa-server"></a>Cisco ASA VPN-apparaat en Azure MFA-server
De Azure MFA-server kan worden geïntegreerd met uw Cisco &reg; ASA VPN-apparaat om extra beveiliging te bieden voor Cisco AnyConnect &reg; VPN-aanmeldingen en toegang tot de portal.  U kunt het LDAP-of RADIUS-protocol gebruiken.  Selecteer een van de volgende stappen om de gedetailleerde stapsgewijze configuratie handleidingen te downloaden.

| Configuratiegids | Beschrijving |
| --- | --- |
| [Cisco ASA met AnyConnect VPN-en Azure MFA-configuratie voor LDAP](https://download.microsoft.com/download/A/2/0/A201567C-C3DE-4227-AF89-4567A470899E/Cisco_ASA_Azure_MFA_LDAP.docx) | Uw Cisco ASA VPN-apparaat integreren met Azure MFA met behulp van LDAP |
| [Cisco ASA met AnyConnect VPN-en Azure MFA-configuratie voor RADIUS](https://download.microsoft.com/download/4/5/7/4579C1CF-35B0-4FBE-8A1A-B49CB2CC0382/Cisco_ASA_Azure_MFA_RADIUS.docx) | Uw Cisco ASA VPN-apparaat integreren met Azure MFA met behulp van RADIUS |

## <a name="citrix-netscaler-ssl-vpn-and-azure-mfa-server"></a>Citrix NetScaler SSL VPN-en Azure MFA-server
De Azure MFA-server kan worden geïntegreerd met uw Citrix NetScaler SSL VPN-apparaat om extra beveiliging te bieden voor Citrix NetScaler SSL VPN-aanmeldingen en toegang tot de portal.  U kunt het LDAP-of RADIUS-protocol gebruiken.  Selecteer een van de volgende stappen om de gedetailleerde stapsgewijze configuratie handleidingen te downloaden.

| Configuratiegids | Beschrijving |
| --- | --- |
| [Citrix NetScaler SSL VPN-en Azure MFA-configuratie voor LDAP](https://download.microsoft.com/download/2/4/E/24E1E722-72DF-471F-A88A-D1338DB1AF83/Citrix_NS_Azure_MFA_LDAP.docx) | Uw Citrix NetScaler SSL VPN integreren met Azure MFA-apparaat met behulp van LDAP |
| [Citrix NetScaler SSL VPN-en Azure MFA-configuratie voor RADIUS](https://download.microsoft.com/download/1/A/4/1A482764-4A63-45C2-A5EC-2B673ACCDD12/Citrix_NS_Azure_MFA_RADIUS.docx) | Uw Citrix NetScaler SSL VPN-apparaat integreren met Azure MFA met RADIUS |

## <a name="juniperpulse-secure-ssl-vpn-appliance-and-azure-mfa-server"></a>Juniper/Pulse Secure SSL VPN-apparaat en Azure MFA-server
De Azure MFA-server kan worden geïntegreerd met uw Juniper/Pulse Secure SSL VPN-apparaat om extra beveiliging te bieden voor Juniper/Pulse Secure SSL VPN-aanmeldingen en toegang tot de portal.  U kunt het LDAP-of RADIUS-protocol gebruiken.  Selecteer een van de volgende stappen om de gedetailleerde stapsgewijze configuratie handleidingen te downloaden.

| Configuratiegids | Beschrijving |
| --- | --- |
| [Juniper/Pulse Secure SSL VPN en Azure MFA-configuratie voor LDAP](https://download.microsoft.com/download/6/5/8/6587B418-75B1-4FCB-84D4-984BC479309E/JuniperPulse_Azure_MFA_LDAP.docx) | Uw Juniper/Pulse Secure SSL VPN integreren met Azure MFA-apparaat met behulp van LDAP |
| [Juniper/Pulse Secure SSL VPN en Azure MFA-configuratie voor RADIUS](https://download.microsoft.com/download/7/9/A/79AB3DAD-4799-4379-B1DA-B95ABDF231DC/JuniperPulse_Azure_MFA_RADIUS.docx) | Uw Juniper/Pulse Secure SSL VPN-apparaat integreren met Azure MFA met behulp van RADIUS |

## <a name="next-steps"></a>Volgende stappen

- [Breid uw bestaande verificatie-infra structuur uit met de NPS-extensie voor Azure Multi-Factor Authentication](howto-mfa-nps-extension.md)

- [Azure Multi-Factor Authentication-instellingen configureren](howto-mfa-mfasettings.md)
