---
title: Hulp krijgen bij de portal mijn apps-Azure Active Directory | Microsoft Docs
description: U kunt hulp krijgen bij het aanmelden bij en het uitvoeren van algemene taken in de portal mijn apps.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: end-user-help
ms.date: 01/19/2021
ms.author: curtand
ms.reviewer: kasimpso
ms.custom: user-help, seo-update-azuread-jan
ms.openlocfilehash: 4377ed76de971f78336ea9024b59dafc5d513487
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100094964"
---
# <a name="troubleshoot-problems-with-the-my-apps-portal"></a>Problemen met de portal mijn apps oplossen

Als u problemen ondervindt met het aanmelden bij of het gebruik van de portal **mijn apps** , kunt u deze tips voor probleem oplossing proberen voordat u contact opneemt met de Help Desk of uw beheerder voor hulp.

## <a name="im-having-trouble-installing-the-my-apps-secure-sign-in-extension"></a>Ik ondervind problemen bij het installeren van de beveiligde aanmeldings extensie voor mijn apps

Als u problemen ondervindt met de installatie van de uitbrei ding voor beveiligde aanmelding van mijn apps:

- Zorg ervoor dat u een ondersteunde browser gebruikt, met inbegrip van:

    - **Micro soft Edge.** Wordt uitgevoerd op Windows 10 jubileum Edition of hoger.

    - **Google Chrome.** Wordt uitgevoerd op Windows 7 of hoger en op macOS X of hoger.

    - **Mozilla Firefox 26,0 of hoger.** Wordt uitgevoerd op Windows XP SP2 of hoger en op Mac OS X 10,6 of hoger.

    - **Internet Explorer 11.** Wordt uitgevoerd op Windows 7 of hoger (beperkte ondersteuning).

- Zorg ervoor dat de instellingen voor de browser uitbreiding zijn ingeschakeld.

- Probeer de browser opnieuw te starten en u opnieuw aan te melden bij de portal **mijn apps** .

- Probeer de cookies van uw browser te wissen en vervolgens opnieuw te starten en u opnieuw aan te melden bij de portal **mijn apps** .

## <a name="i-cant-sign-in-to-the-my-apps-portal"></a>Ik kan me niet aanmelden bij de portal **mijn apps**

Als u problemen ondervindt bij het aanmelden bij de portal **mijn apps** , kunt u het volgende proberen:

- Als er een fout optreedt bij het aanmelden met een persoonlijke Microsoft-account, kunt u zich nog steeds aanmelden met de domein naam voor uw organisatie (zoals contoso.com) of de **Tenant-id** van uw organisatie van uw beheerder in een van de volgende url's:

   - https://myapplications.microsoft.com?tenantId=*your_domain_name*
   - https://myapplications.microsoft.com?tenant=*your_tenant_ID*

- Zorg ervoor dat u de juiste URL gebruikt. Dit moet https://myapps.microsoft.com een aangepaste pagina voor uw organisatie zijn, zoals https://myapps.microsoft.com/contoso.com .

- Zorg ervoor dat uw wacht woord juist is en niet is verlopen. Zie [uw werk-of school wachtwoord opnieuw instellen](active-directory-passwords-update-your-own-password.md)voor meer informatie.

- Zorg ervoor dat uw verificatie gegevens actueel en nauw keurig zijn. Zie [wat betekent Azure AD multi-factor Authentication voor mij?](./multi-factor-authentication-end-user-first-time.md) u kunt ook [de methoden en informatie voor uw beveiligings gegevens wijzigen](./security-info-setup-auth-app.md).

- Voeg de URL van **mijn app** -portal toe aan de **Internet eigenschappen > beveiliging > instellingen voor vertrouwde sites** .

- Wis de cache van uw browser en probeer u opnieuw aan te melden.

## <a name="my-password-isnt-working"></a>Mijn wacht woord werkt niet

Als u uw wacht woord bent verg eten, u het nooit hebt ontvangen van uw organisatie, uw account hebt vergrendeld of uw wacht woord wilt wijzigen, raadpleegt u [Help, ik ben mijn wacht woord voor Azure AD verg eten](active-directory-passwords-update-your-own-password.md).

## <a name="i-want-to-be-able-to-reset-my-own-password"></a>Ik wil mijn eigen wacht woord opnieuw instellen

Als u uw eigen wacht woord opnieuw wilt instellen, moet uw beheerder eerst de functie voor uw organisatie inschakelen en vervolgens uw vereiste verificatie methoden bijwerken en verifiëren. Zie [registreren voor Self-service voor wachtwoord herstel](active-directory-passwords-reset-register.md)voor meer informatie over het bijwerken van uw verificatie methoden.

## <a name="im-getting-an-access-denied-message-when-i-start-an-app"></a>Ik krijg een bericht dat de toegang wordt geweigerd wanneer ik een app start

Als u een bericht ontvangt dat de **toegang is geweigerd** nadat u een app hebt gestart vanuit de **mijn app** -Portal, kunt u het volgende proberen:

- Zorg ervoor dat u de [beveiligde aanmeldings extensie voor mijn apps](my-apps-portal-end-user-access.md#download-and-install-the-my-apps-secure-sign-in-extension) hebt geïnstalleerd en dat u een [ondersteunde browser](my-apps-portal-end-user-access.md#supported-browsers)gebruikt.

- Zorg ervoor dat u de juiste URL voor de app gebruikt en dat de URL zich op uw Internet-eigenschappen bevindt > lijst met **vertrouwde sites voor beveiliging >** .

- Zorg ervoor dat uw wacht woord juist is en niet is verlopen. Zie [uw werk-of school wachtwoord opnieuw instellen](active-directory-passwords-update-your-own-password.md)voor meer informatie.

- Zorg ervoor dat uw verificatie gegevens actueel en nauw keurig zijn. Zie [wat betekent Azure AD multi-factor Authentication voor mij?](./multi-factor-authentication-end-user-first-time.md) u kunt ook [de methoden en informatie voor uw beveiligings gegevens wijzigen](./security-info-setup-auth-app.md).

- Wis de cache van uw browser en probeer u opnieuw aan te melden.

Als u na het uitvoeren van deze dingen nog steeds geen toegang hebt tot uw app, neemt u contact op met de Help Desk van uw organisatie voor hulp.

## <a name="next-steps"></a>Volgende stappen

Nadat u zich hebt aangemeld bij de portal **mijn apps** , kunt u uw profiel-en account gegevens, uw groeps gegevens en toegangs beoordelings gegevens bijwerken (als u hiervoor toestemming hebt).

- [Apps openen en gebruiken in de portal voor mijn apps](my-apps-portal-end-user-access.md).

- [Wijzig de profiel gegevens](./my-account-portal-settings.md).

- [Gegevens over groepen weer geven en bijwerken](my-apps-portal-end-user-groups.md).

- [Voer uw eigen toegangs beoordelingen uit](my-apps-portal-end-user-access-reviews.md).
