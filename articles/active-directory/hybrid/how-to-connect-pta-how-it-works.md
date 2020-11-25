---
title: 'Azure AD Connect: Pass-Through-verificatie: Hoe werkt het? Microsoft Docs'
description: In dit artikel wordt beschreven hoe Azure Active Directory Pass-Through-verificatie werkt
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
ms.date: 07/19/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: fe92f761ac0b16da7c3cc3c69c1fa4b00f4e7579
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "95996590"
---
# <a name="azure-active-directory-pass-through-authentication-technical-deep-dive"></a>Pass-Through-verificatie Azure Active Directory: technische grondige kennis
Dit artikel bevat een overzicht van de werking van de Pass-Through-verificatie van Azure Active Directory (Azure AD). Zie het artikel over de [grondige beveiliging](how-to-connect-pta-security-deep-dive.md) voor meer informatie.

## <a name="how-does-azure-active-directory-pass-through-authentication-work"></a>Hoe werkt Azure Active Directory Pass Through-verificatie?

>[!NOTE]
>Als een vereiste om Pass-Through-verificatie te kunnen gebruiken, moeten gebruikers in azure AD worden ingericht vanuit on-premises Active Directory met behulp van Azure AD Connect. Pass Through-verificatie is niet van toepassing op gebruikers met alleen Clouds.

Wanneer een gebruiker zich probeert aan te melden bij een toepassing die is beveiligd door Azure AD, en als Pass-Through-verificatie is ingeschakeld voor de Tenant, worden de volgende stappen uitgevoerd:

1. De gebruiker probeert toegang te krijgen tot een toepassing, bijvoorbeeld [Outlook Web app](https://outlook.office365.com/owa/).
2. Als de gebruiker nog niet is aangemeld, wordt de gebruiker omgeleid naar de aanmeldings pagina van de **gebruiker van** Azure AD.
3. De gebruiker voert de gebruikers naam in op de aanmeldings pagina van Azure AD en selecteert vervolgens de knop **volgende** .
4. De gebruiker voert zijn wacht woord in op de aanmeldings pagina van Azure AD en selecteert vervolgens de knop **Aanmelden** .
5. Azure AD: de gebruikers naam en het wacht woord (versleutelen met behulp van de open bare sleutel van de verificatie agenten) worden in een wachtrij geplaatst, wanneer de aanvraag wordt ontvangen om zich aan te melden.
6. Een on-premises verificatie agent haalt de gebruikers naam en het versleutelde wacht woord op uit de wachtrij. Houd er rekening mee dat de agent geen regel matig pollt voor aanvragen van de wachtrij, maar aanvragen ophaalt via een vooraf ingestelde permanente verbinding.
7. De agent ontsleutelt het wacht woord met behulp van de bijbehorende persoonlijke sleutel.
8. De agent valideert de gebruikers naam en het wacht woord voor Active Directory door gebruik te maken van standaard Windows-Api's. Dit is een vergelijkbaar mechanisme voor wat Active Directory Federation Services (AD FS) gebruikt. De gebruikers naam kan de on-premises standaard gebruikersnaam zijn, meestal `userPrincipalName` of een ander kenmerk dat is geconfigureerd in azure AD Connect (ook wel bekend als `Alternate ID` ).
9. De on-premises Active Directory domein controller (DC) evalueert de aanvraag en retourneert de juiste reactie (geslaagd, mislukt, wacht woord verlopen of gebruiker vergrendeld) bij de agent.
10. De verificatie agent retourneert op zijn beurt deze reactie terug naar Azure AD.
11. Azure AD evalueert het antwoord en reageert indien nodig op de gebruiker. Azure AD ondertekent bijvoorbeeld de gebruiker onmiddellijk of aanvragen voor Azure AD-Multi-Factor Authentication.
12. Als de gebruiker zich aanmeldt, heeft de gebruiker toegang tot de toepassing.

In het volgende diagram ziet u alle onderdelen en de daarbij behorende stappen:

![Pass-through-verificatie](./media/how-to-connect-pta-how-it-works/pta2.png)

## <a name="next-steps"></a>Volgende stappen
- [Huidige beperkingen](how-to-connect-pta-current-limitations.md): meer informatie over welke scenario's worden ondersteund en wat niet.
- [Quick Start](how-to-connect-pta-quick-start.md): Ga aan de slag met Azure AD Pass-Through-verificatie.
- [Migratie van AD FS naar Pass-](https://aka.ms/adfstoPTADP) through-verificatie: een gedetailleerde hand leiding voor het migreren van AD FS (of andere Federatie technologieën) naar Pass-Through-verificatie.
- [Slimme vergren deling](../authentication/howto-password-smart-lockout.md): Configureer de mogelijkheden voor slim vergren delen op uw Tenant voor het beveiligen van gebruikers accounts.
- [Veelgestelde vragen](how-to-connect-pta-faq.md): Hier vindt u antwoorden op veelgestelde vragen.
- [Problemen oplossen](tshoot-connect-pass-through-authentication.md): informatie over het oplossen van veelvoorkomende problemen met de functie Pass-Through-verificatie.
- [Grondige beveiliging](how-to-connect-pta-security-deep-dive.md): krijg uitgebreide technische informatie over de functie Pass-Through-verificatie.
- [Naadloze SSO van Azure AD](how-to-connect-sso.md): meer informatie over deze complementaire functie.
- [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect): gebruik het Azure Active Directory-forum om nieuwe functie aanvragen te verwerken.

