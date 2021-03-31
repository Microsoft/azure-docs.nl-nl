---
title: TLS 1,2 Enforcement-Azure Active Directory registratie service
description: Ondersteuning voor TLS 1,0 en 1,1 voor de Azure AD Device Registration service verwijderen
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: reference
ms.date: 07/10/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: spunukol
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2bb8c6c64e0a68f5176c4eb0c0177c5220394695
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "89268754"
---
# <a name="enforce-tls-12-for-the-azure-ad-registration-service"></a>TLS 1,2 afdwingen voor de Azure AD-registratie service

De Device Registration service Azure Active Directory (Azure AD) wordt gebruikt om apparaten te verbinden met de Cloud met een apparaat-id. De Azure AD Device Registration-service ondersteunt momenteel het gebruik van Transport Layer Security (TLS) 1,2 voor communicatie met Azure. Micro soft raadt aan om TLS 1,0 en 1,1 in te scha kelen om de beveiliging en de beste versleuteling van de klasse te garanderen. Dit document bevat informatie over het controleren van computers die worden gebruikt om de registratie te volt ooien en te communiceren met de Azure AD Device Registration service TLS 1,2 gebruiken.

De TLS-protocol versie 1,2 is een crypto grafie-protocol dat is ontworpen om beveiligde communicatie mogelijk te maken. Het TLS-protocol is voornamelijk gericht op privacy-en gegevens integriteit. TLS heeft een groot aantal iteraties door lopen met versie 1,2 die is gedefinieerd in [RFC 5246 (externe koppeling)](https://tools.ietf.org/html/rfc5246).

Huidige analyse van verbindingen toont weinig TLS 1,1-en 1,0-gebruik, maar we bieden deze informatie zodat u eventuele betrokken clients of servers zo nodig kunt bijwerken voordat ondersteuning voor TLS 1,1 en 1,0 eindigt. Als u een on-premises infra structuur voor hybride scenario's of Active Directory Federation Services (AD FS) gebruikt, moet u ervoor zorgen dat de infra structuur zowel binnenkomende als uitgaande verbindingen kan ondersteunen die gebruikmaken van TLS 1,2.

## <a name="update-windows-servers"></a>Windows-servers bijwerken

Voor Windows-servers die gebruikmaken van de Azure AD Device Registration service of optreden als proxy's, moet u de volgende stappen uitvoeren om ervoor te zorgen dat TLS 1,2 is ingeschakeld:

> [!IMPORTANT]
> Nadat u het REGI ster hebt bijgewerkt, moet u de Windows-Server opnieuw opstarten om de wijzigingen van kracht te laten worden.

### <a name="enable-tls-12"></a>TLS 1.2 inschakelen

Zorg ervoor dat de volgende register teken reeksen zijn geconfigureerd:

- HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2 \ client
  - "DisabledByDefault" = dword: 00000000
  - "Ingeschakeld" = dword: 00000001
- HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2-server
  - "DisabledByDefault" = dword: 00000000
  - "Ingeschakeld" = dword: 00000001
- HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\. NETFramework\v4.0.30319
  - "Schusestrongcrypto toe" = dword: 00000001

## <a name="update-non-windows-proxies"></a>Niet-Windows-proxy's bijwerken

Computers die fungeren als proxy's tussen apparaten en de Azure AD Device Registration-service moeten ervoor zorgen dat TLS 1,2 is ingeschakeld. Volg de richt lijnen van uw leverancier om ondersteuning te bieden.

## <a name="update-ad-fs-servers"></a>AD FS-servers bijwerken

Alle AD FS servers die worden gebruikt voor de communicatie met de Azure AD Device Registration-service moeten ervoor zorgen dat TLS 1,2 is ingeschakeld. Zie [SSL/TLS-protocollen en coderings suites voor AD FS beheren](/windows-server/identity/ad-fs/operations/manage-ssl-protocols-in-ad-fs) voor meer informatie over het in-of uitschakelen van deze configuratie.

## <a name="client-updates"></a>Clientupdates

Aangezien alle combi Naties van client-server en browser-server TLS 1,2 moeten gebruiken om verbinding te maken met de Azure AD Device Registration-service, moet u deze apparaten mogelijk bijwerken.

De volgende clients kunnen TLS 1,2 niet ondersteunen. Werk uw clients bij om een ononderbroken toegang te garanderen.

- Android-versie 4,3 en lager
- Firefox versie 5,0 en lager
- Versie 8-10 van Internet Explorer in Windows 7 en eerdere versies
- Internet Explorer 10 op Windows Phone 8,0
- Safari-versie 6.0.4 op OS X 10.8.4 en eerder

## <a name="next-steps"></a>Volgende stappen

[Overzicht van TLS/SSL (Schannel SSP)](/windows-server/security/tls/tls-ssl-schannel-ssp-overview)