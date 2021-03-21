---
title: Vereiste poorten en protocollen voor hybride identiteit-Azure | Microsoft Docs
description: Deze pagina is een technische naslag pagina voor poorten die moeten worden geopend voor Azure AD Connect
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: curtand
ms.assetid: de97b225-ae06-4afc-b2ef-a72a3643255b
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 03/04/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 681d92ed162feff0510067556d7eeaa47b12d988
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102520027"
---
# <a name="hybrid-identity-required-ports-and-protocols"></a>Voor hybride identiteit benodigde poorten en protocollen
Het volgende document bevat technische Naslag informatie over de vereiste poorten en protocollen voor het implementeren van een hybride identiteits oplossing. Gebruik de volgende afbeelding en Raadpleeg de bijbehorende tabel.

![Wat is Azure AD Connect?](./media/reference-connect-ports/required3.png)

## <a name="table-1---azure-ad-connect-and-on-premises-ad"></a>Tabel 1-Azure AD Connect en on-premises AD
In deze tabel worden de poorten en protocollen beschreven die vereist zijn voor de communicatie tussen de Azure AD Connect server en on-premises AD.

| Protocol | Poorten | Beschrijving |
| --- | --- | --- |
| DNS |53 (TCP/UDP) |DNS-zoek acties op het doelforest. |
| Kerberos |88 (TCP/UDP) |Kerberos-verificatie voor het AD-forest. |
| MS-RPC |135 (TCP) |Wordt gebruikt tijdens de eerste configuratie van de wizard Azure AD Connect wanneer deze verbinding maakt met het AD-forest, en tijdens wachtwoord synchronisatie. |
| LDAP |389 (TCP/UDP) |Wordt gebruikt voor het importeren van gegevens uit AD. Gegevens zijn versleuteld met het Kerberos-teken & zegel. |
| SMB | 445 (TCP) |Wordt gebruikt door naadloze SSO voor het maken van een computer account in het AD-forest. |
| LDAP/SSL |636 (TCP/UDP) |Wordt gebruikt voor het importeren van gegevens uit AD. De gegevens overdracht is ondertekend en versleuteld. Wordt alleen gebruikt als u TLS gebruikt. |
| RPC |49152-65535 (wille keurige hoge RPC-poort) (TCP) |Wordt gebruikt tijdens de eerste configuratie van Azure AD Connect wanneer deze verbinding maakt met de AD-forests en tijdens wachtwoord synchronisatie. Als de dynamische poort is gewijzigd, moet u die poort openen. Zie [KB929851](https://support.microsoft.com/kb/929851), [KB832017](https://support.microsoft.com/kb/832017)en [KB224196](https://support.microsoft.com/kb/224196) voor meer informatie. |
|WinRM  | 5985 (TCP) |Wordt alleen gebruikt als u AD FS installeert met behulp van de wizard gMSA door Azure AD Connect|
|AD DS-webservices | 9389 (TCP) |Wordt alleen gebruikt als u AD FS installeert met behulp van de wizard gMSA door Azure AD Connect |

## <a name="table-2---azure-ad-connect-and-azure-ad"></a>Tabel 2-Azure AD Connect en Azure AD
In deze tabel worden de poorten en protocollen beschreven die vereist zijn voor de communicatie tussen de Azure AD Connect-server en Azure AD.

| Protocol | Poorten | Beschrijving |
| --- | --- | --- |
| HTTP |80 (TCP) |Wordt gebruikt voor het downloaden van Crl's (certificaatintrekkingslijsten) om TLS/SSL-certificaten te verifiëren. |
| HTTPS |443 (TCP) |Wordt gebruikt om te synchroniseren met Azure AD. |

Voor een lijst met Url's en IP-adressen die u moet openen in uw firewall, raadpleegt u [Office 365-url's en IP-](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2) adresbereiken en [problemen met Azure AD Connect connectiviteit oplossen](tshoot-connect-connectivity.md#troubleshoot-connectivity-issues-in-the-installation-wizard).

## <a name="table-3---azure-ad-connect-and-ad-fs-federation-serverswap"></a>Tabel 3-Azure AD Connect en AD FS federatie servers/WAP
In deze tabel worden de poorten en protocollen beschreven die vereist zijn voor de communicatie tussen de Azure AD Connect-server en AD FS federatie/WAP-servers.  

| Protocol | Poorten | Beschrijving |
| --- | --- | --- |
| HTTP |80 (TCP) |Wordt gebruikt voor het downloaden van Crl's (certificaatintrekkingslijsten) om TLS/SSL-certificaten te verifiëren. |
| HTTPS |443 (TCP) |Wordt gebruikt om te synchroniseren met Azure AD. |
| WinRM |5985 |WinRM-listener |

## <a name="table-4---wap-and-federation-servers"></a>Tabel 4: WAP-en Federatie servers
In deze tabel worden de poorten en protocollen beschreven die vereist zijn voor de communicatie tussen de Federatie servers en de WAP-servers.

| Protocol | Poorten | Beschrijving |
| --- | --- | --- |
| HTTPS |443 (TCP) |Wordt gebruikt voor verificatie. |

## <a name="table-5---wap-and-users"></a>Tabel 5-WAP en gebruikers
In deze tabel worden de poorten en protocollen beschreven die vereist zijn voor communicatie tussen gebruikers en de WAP-servers.

| Protocol | Poorten | Beschrijving |
| --- | --- | --- |
| HTTPS |443 (TCP) |Wordt gebruikt voor authenticatie van het apparaat. |
| TCP |49443 (TCP) |Wordt gebruikt voor verificatie via certificaat. |

## <a name="table-6a--6b---pass-through-authentication-with-single-sign-on-sso-and-password-hash-sync-with-single-sign-on-sso"></a>Tabel 6a & 6b-Pass Through-verificatie met eenmalige aanmelding (SSO) en wachtwoord hash-synchronisatie met eenmalige aanmelding (SSO)
In de volgende tabellen worden de poorten en protocollen beschreven die vereist zijn voor de communicatie tussen de Azure AD Connect en Azure AD.

### <a name="table-6a---pass-through-authentication-with-sso"></a>Tabel 6a-Pass Through-verificatie met SSO
|Protocol|Poortnummer|Beschrijving
| --- | --- | ---
|HTTP|80|Uitgaand HTTP-verkeer voor beveiligings validatie, zoals SSL, inschakelen. Het is ook nodig dat de functie voor het automatisch bijwerken van de connector goed werkt.
|HTTPS|443| Schakel uitgaande HTTPS-verkeer in voor bewerkingen, zoals het in-en uitschakelen van de functie, het registreren van connectors, downloaden van connector updates en het afhandelen van alle aanmeldings aanvragen van gebruikers.

Daarnaast moeten Azure AD Connect directe IP-verbindingen kunnen maken met de [IP-adresbereiken van Azure Data Center](https://www.microsoft.com/download/details.aspx?id=41653).

### <a name="table-6b---password-hash-sync-with-sso"></a>Tabel 6b-wacht woord-hash synchroniseren met SSO

|Protocol|Poortnummer|Beschrijving
| --- | --- | ---
|HTTPS|443| SSO-registratie inschakelen (alleen vereist voor het registratie proces voor eenmalige aanmelding).

Daarnaast moeten Azure AD Connect directe IP-verbindingen kunnen maken met de [IP-adresbereiken van Azure Data Center](https://www.microsoft.com/download/details.aspx?id=41653). Dit is alleen vereist voor het registratie proces voor eenmalige aanmelding.

## <a name="table-7a--7b---azure-ad-connect-health-agent-for-ad-fssync-and-azure-ad"></a>Tabel 7A & 7b-Azure AD Connect Health Agent voor (AD FS/Sync) en Azure AD
In de volgende tabellen worden de eind punten, poorten en protocollen beschreven die vereist zijn voor de communicatie tussen Azure AD Connect Health Agents en Azure AD

### <a name="table-7a---ports-and-protocols-for-azure-ad-connect-health-agent-for-ad-fssync-and-azure-ad"></a>Tabel 7A-poorten en protocollen voor Azure AD Connect Health Agent voor (AD FS/Sync) en Azure AD
In deze tabel worden de volgende uitgaande poorten en protocollen beschreven die vereist zijn voor de communicatie tussen de Azure AD Connect Health Agents en Azure AD.  

| Protocol | Poorten | Beschrijving |
| --- | --- | --- |
| HTTPS |443 (TCP) |Uitgaand |
| Azure Service Bus |5671 (TCP) |Uitgaand |

Azure Service Bus poort 5671 is niet meer vereist voor de nieuwste versie van de agent. De nieuwste versie van Azure AD Connect Health Agent is alleen poort 443 vereist.

### <a name="7b---endpoints-for-azure-ad-connect-health-agent-for-ad-fssync-and-azure-ad"></a>7b-eind punten voor Azure AD Connect Health Agent voor (AD FS/Sync) en Azure AD
Zie [de sectie vereisten voor de Azure AD Connect Health-Agent](how-to-connect-health-agent-install.md#requirements)voor een lijst met eind punten.

