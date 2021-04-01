---
title: 'Azure AD Connect: Pass-Through-verificatie: huidige beperkingen | Microsoft Docs'
description: In dit artikel worden de huidige beperkingen van de Pass-Through-verificatie van Azure Active Directory (Azure AD) beschreven
services: active-directory
keywords: Azure AD Connect Pass-Through-verificatie, installeren van Active Directory, vereiste onderdelen voor Azure AD, SSO, eenmalige aanmelding
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 09/04/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3e9c4489f59f72e4d0b5c7a0b911da188eb0828c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "89280193"
---
# <a name="azure-active-directory-pass-through-authentication-current-limitations"></a>Pass-Through-verificatie Azure Active Directory: huidige beperkingen

>[!IMPORTANT]
>De Pass-Through-verificatie van Azure Active Directory (Azure AD) is een gratis functie en u hebt geen betaalde versies van Azure AD nodig om deze te gebruiken. Pass-Through-verificatie is alleen beschikbaar in het World Wide instance of Azure AD en niet op de [Microsoft Azure Duitsland Cloud](https://www.microsoft.de/cloud-deutschland) of de [Microsoft Azure Government Cloud](https://azure.microsoft.com/features/gov/).

## <a name="supported-scenarios"></a>Ondersteunde scenario's

De volgende scenario's worden ondersteund:

- Gebruikers aanmeldingen voor toepassingen op basis van een webbrowser.
- Aanmeldingen van gebruikers met Outlook-clients met verouderde protocollen zoals Exchange ActiveSync, EAS, SMTP, POP en IMAP.
- Aanmeldingen van gebruikers voor oudere Office-client toepassingen en Office-toepassingen die ondersteuning bieden voor [moderne verificatie](https://www.microsoft.com/en-us/microsoft-365/blog/2015/11/19/updated-office-365-modern-authentication-public-preview): Office 2013 en 2016.
- Aanmeldingen van gebruikers naar verouderde protocol toepassingen, zoals Power shell versie 1,0 en anderen.
- Azure AD-deelname voor Windows 10-apparaten.
- App-wacht woorden voor Multi-Factor Authentication.

## <a name="unsupported-scenarios"></a>Niet-ondersteunde scenario's

De volgende scenario's worden _niet_ ondersteund:

- Detectie van gebruikers met [gelekte referenties](../identity-protection/overview-identity-protection.md).
- Azure AD Domain Services wachtwoord hash-synchronisatie moet zijn ingeschakeld voor de Tenant. Tenants die gebruikmaken van Pass-Through-verificatie, werken daarom _alleen_ voor scenario's die Azure AD Domain Services nodig hebben.
- Pass-Through-verificatie is niet geïntegreerd met [Azure AD Connect Health](./whatis-azure-ad-connect.md).

> [!IMPORTANT]
> Als tijdelijke oplossing voor _niet-ondersteunde scenario's (_ met uitzonde ring van Azure AD Connect Health-integratie) schakelt u de synchronisatie van wacht woord-hashes in op de pagina [optionele functies](how-to-connect-install-custom.md#optional-features) van de wizard Azure AD Connect.
> 
> [!NOTE]
> Wanneer u wachtwoord-hash synchronisatie inschakelt, hebt u de mogelijkheid om failover-verificatie uit te scha kelen als uw on-premises infra structuur wordt onderbroken. Deze failover van Pass-Through-verificatie naar wachtwoord-hash-synchronisatie is niet automatisch. U moet de aanmeldings methode hand matig overschakelen met Azure AD Connect. Als de server met Azure AD Connect uitvalt, hebt u hulp nodig van Microsoft Ondersteuning om Pass-Through-verificatie uit te scha kelen.

## <a name="next-steps"></a>Volgende stappen
- [Snel starten](how-to-connect-pta-quick-start.md): aan de slag met Azure AD Pass-Through-verificatie.
- [Migratie van AD FS naar Pass-](https://aka.ms/ADFSTOPTADPDownload) through-verificatie: een gedetailleerde hand leiding voor het migreren van AD FS (of andere Federatie technologieën) naar Pass-Through-verificatie.
- [Slimme vergren deling](../authentication/howto-password-smart-lockout.md): informatie over het configureren van de mogelijkheden voor slim vergren delen van uw Tenant voor het beveiligen van gebruikers accounts.
- [Technisch diep gaande](how-to-connect-pta-how-it-works.md): begrijpen hoe de functie Pass-Through-verificatie werkt.
- [Veelgestelde vragen](how-to-connect-pta-faq.md): Hier vindt u antwoorden op veelgestelde vragen over de functie Pass-Through-verificatie.
- [Problemen oplossen](tshoot-connect-pass-through-authentication.md): informatie over het oplossen van veelvoorkomende problemen met de functie Pass-Through-verificatie.
- [Grondige beveiliging](how-to-connect-pta-security-deep-dive.md): krijg uitgebreide technische informatie over de functie Pass-Through-verificatie.
- [Naadloze SSO van Azure AD](how-to-connect-sso.md): meer informatie over deze complementaire functie.
- [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect): gebruik het Azure Active Directory-forum om nieuwe functie aanvragen te verwerken.
