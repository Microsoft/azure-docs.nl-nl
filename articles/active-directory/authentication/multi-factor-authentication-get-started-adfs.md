---
title: Verificatie in twee stappen Azure AD MFA en ADFS-Azure Active Directory
description: Dit is de Azure AD Multi-Factor Authentication-pagina waarop wordt beschreven hoe u aan de slag gaat met Azure AD MFA en AD FS.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: d0023d40fdc26fa1c42a67ce78a9259643098abb
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/06/2020
ms.locfileid: "96741402"
---
# <a name="getting-started-with-azure-ad-multi-factor-authentication-and-active-directory-federation-services"></a>Aan de slag met Azure AD Multi-Factor Authentication en Active Directory Federation Services

<center>

![Azure AD MFA en ADFS aan de slag](./media/multi-factor-authentication-get-started-adfs/adfs.png)</center>

Als uw organisatie gebruikmaakt van uw on-premises Active Directory met Azure Active Directory met behulp van AD FS, zijn er twee opties voor het gebruik van Azure AD Multi-Factor Authentication.

* Cloud resources beveiligen met Azure AD Multi-Factor Authentication of Active Directory Federation Services
* Cloudresources en on-premises resources beveiligen met behulp van de Azure Multi-Factor Authentication-server

De volgende tabel bevat een overzicht van de verificatie-ervaring tussen het beveiligen van resources met Azure AD Multi-Factor Authentication en AD FS

| Verificatie-ervaring - op browser gebaseerde apps | Verificatie-ervaring - niet op browser gebaseerde apps |
|:--- |:--- |
| Azure AD-resources beveiligen met Azure AD Multi-Factor Authentication |<li>De eerste verificatiestap wordt uitgevoerd on-premises met AD FS.</li> <li>De tweede stap is een telefonische methode die met behulp van cloudverificatie wordt uitgevoerd.</li> |
| Azure AD-resources beveiligen met behulp van Active Directory Federation Services |<li>De eerste verificatiestap wordt uitgevoerd on-premises met AD FS.</li><li>De tweede stap wordt on-premises uitgevoerd door de claim toe te kennen.</li> |

Valkuilen met app-wachtwoorden voor federatieve gebruikers:

* App-wachtwoorden worden gecontroleerd met cloudverificatie en daarom worden federaties omzeild. Federatie wordt alleen actief gebruikt bij het instellen van het app-wachtwoord.
* On-premises instellingen voor toegangsbeheer van client worden niet herkend door app-wachtwoorden.
* U raakt on-premises mogelijkheden voor logboekregistratie bij verificatie voor app-wachtwoorden kwijt.
* Account uitschakelen/verwijderen kan met Directory-synchronisatie tot drie uur duren, waardoor uitschakelen/verwijderen van app-wachtwoorden in de cloudidentiteit wordt vertraagd.

Raadpleeg de volgende artikelen voor meer informatie over het instellen van Azure AD Multi-Factor Authentication of de Azure Multi-Factor Authentication-server met AD FS:

* [Cloud resources beveiligen met Azure AD Multi-Factor Authentication en AD FS](howto-mfa-adfs.md)
* [Cloudresources en on-premises resources beveiligen met behulp van de Azure Multi-Factor Authentication-server met Windows Server 2012 R2 AD FS](howto-mfaserver-adfs-2012.md)
* [Cloudresources en on-premises resources beveiligen met behulp van de Azure Multi-Factor Authentication-server met AD FS 2.0](howto-mfaserver-adfs-2.md)
