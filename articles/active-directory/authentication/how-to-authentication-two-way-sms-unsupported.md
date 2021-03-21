---
title: Twee richtings-SMS-Azure Active Directory niet meer ondersteund
description: Hierin wordt uitgelegd hoe u een andere methode inschakelt voor gebruikers die nog steeds twee richtings-SMS gebruiken.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 03/02/2021
ms.author: justinha
author: rhicock
manager: daveba
ms.reviewer: dawoo
ms.collection: M365-identity-device-management
ms.openlocfilehash: d25ed1e46823ec6d820addf3944c96c97fcabcb8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101689025"
---
# <a name="two-way-sms-unsupported"></a>Twee richtings-SMS niet ondersteund

Twee richtings-SMS-server voor Azure AD Multi-Factor Authentication (MFA) is in 2018 oorspronkelijk afgeschaft en wordt na 24 februari 2021 niet meer ondersteund. Beheerders moeten een andere methode inschakelen voor gebruikers die nog steeds twee richtings-SMS gebruiken.

E-mail meldingen en Azure Portal Service Health meldingen (Portal-pop-upmeldingen) zijn verzonden naar betrokken beheerders op 8 december 2020 en 28 januari 2021. De waarschuwingen zijn naar de RBAC-rollen van de eigenaar, mede-eigenaar, beheerder en service beheerder gepaard die zijn gekoppeld aan de abonnementen. Als u de volgende stappen al hebt uitgevoerd, hoeft u geen actie te ondernemen.

## <a name="required-actions"></a>Vereiste acties

1. Schakel de mobiele app voor uw gebruikers in, als u dit nog niet hebt gedaan. Zie [verificatie van mobiele apps met MFA-server inschakelen](howto-mfaserver-deploy-mobileapp.md)voor meer informatie.
1. Stuur uw eind gebruikers hiervan op de hoogte om de [gebruikers Portal](howto-mfaserver-deploy-userportal.md) van de MFA-server te bezoeken om de mobiele app te activeren. De [Microsoft Authenticator-app](https://www.microsoft.com/en-us/account/authenticator) is de aanbevolen verificatie optie omdat deze veiliger is dan twee richtings-SMS-toepassingen. Zie voor meer informatie [de tijd voor het ophangen van de telefoon transporten voor authenticatie](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/it-s-time-to-hang-up-on-phone-transports-for-authentication/ba-p/1751752).
1. Wijzig de gebruikers instellingen van tekst bericht in twee richtingen als de standaard methode.

## <a name="faq"></a>Veelgestelde vragen

### <a name="what-if-i-dont-change-the-default-method-from-two-way-sms-to-the-mobile-app"></a>Wat moet ik doen als ik de standaard methode niet Wijzig vanuit een twee richtings-SMS naar de mobiele app?
Twee richtings-SMS mislukt na 24 februari 2021. Gebruikers krijgen een fout bericht te zien wanneer ze zich proberen aan te melden en MFA te geven.

### <a name="how-do-i-change-the-user-settings-from-two-way-text-message-to-mobile-app"></a>Hoe kan ik de gebruikers instellingen van tekst bericht in twee richtingen wijzigen in mobiele app?

U moet de gebruikers instellingen wijzigen door de volgende stappen uit te voeren:

1. Filter in MFA-server de lijst met gebruikers voor tekst bericht in twee richtingen.
1. Selecteer alle gebruikers.
1. Open het dialoog venster gebruikers bewerken.
1. Wijzig gebruikers van een tekst bericht naar een mobiele app.

   ![Scherm afbeelding van eind gebruikers](media/how-to-authentication-two-way-sms-unsupported/end-users.png)

### <a name="do-my-users-need-to-take-any-action-if-yes-how"></a>Moeten mijn gebruikers actie ondernemen? Zo ja, hoe?
Ja. Uw eind gebruikers moeten uw specifieke gebruikers portal voor de MFA-Server bezoeken om de mobiele app te activeren, als deze nog niet zijn gemaakt. Nadat u stap 3 hebt uitgevoerd, kunnen alle gebruikers die de gebruikers Portal niet bezoeken om de mobiele app in te stellen, zich aanmelden voordat ze de gebruikers Portal bezoeken om zich opnieuw te registreren.

### <a name="what-if-my-users-cant-install-the-mobile-app-what-other-options-do-they-have"></a>Wat gebeurt er als mijn gebruikers de mobiele app niet kunnen installeren? Welke andere opties hebben ze?
Het alternatief voor SMS of de mobiele app is een telefoon gesprek. De Microsoft Authenticator-app is echter de aanbevolen verificatie methode.

### <a name="will-one-way-sms-be-deprecated-as-well"></a>Worden er ook eenmalige SM'S afgeschaft?
Nee, alleen de twee richtings-SMS wordt afgeschaft. Voor MFA-server werkt de eenrichtings-SMS voor een subset van scenario's:

- AD FS adapter
- IIS-verificatie (vereist gebruikers Portal en-configuratie)
- RADIUS (vereist dat RADIUS-clients toegangs Challenge ondersteunen en dat het PAP-protocol wordt gebruikt)

Er gelden beperkingen wanneer SMS kan worden gebruikt om een beter alternatief te maken voor de mobiele app, omdat hiervoor geen verificatie code wordt gevraagd.
Als u in sommige scenario's nog steeds eenrichtings-SMS-berichten wilt gebruiken, kunt u deze selectief laten, maar de sectie **bedrijfs instellingen** wijzigen, standaard instellingen voor het tabblad **Algemeen** **tekst bericht** in plaats van **twee** **richtingen.** Ten slotte, als u adreslijst synchronisatie gebruikt die standaard is ingesteld op SMS, moet u deze wijzigen in One-Way in plaats van twee richtings waarden.

### <a name="how-can-i-check-which-users-are-still-using-two-way-sms"></a>Hoe kan ik controleren welke gebruikers nog steeds een twee richtings-SMS gebruiken?
Als u deze gebruikers wilt weer geven, start u **MFA server**, selecteert u de sectie **gebruikers** , klikt u op **gebruikers lijst filteren** en filtert u op **tekst bericht in twee richtingen**.

### <a name="how-do-we-hide-two-way-sms-as-an-option-in-the-mfa-portal-to-prevent-users-from-selecting-it-in-the-future"></a>Hoe kunnen we twee richtings-SMS verbergen als een optie in de MFA-Portal om te voor komen dat gebruikers deze in de toekomst selecteren?
In de gebruikers portal van de MFA-server klikt u op **instellingen**. u kunt **tekst berichten** wissen zodat deze niet beschikbaar zijn. Dit geldt ook voor de sectie **AD FS** als u AD FS gebruikt voor gebruikers registratie.

