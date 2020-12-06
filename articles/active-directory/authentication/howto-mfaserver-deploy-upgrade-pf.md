---
title: Phone factor upgraden naar Azure MFA-server-Azure Active Directory
description: Ga aan de slag met Azure MFA-server bij het upgraden van de oudere PhoneFactor-agent.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 07/11/2018
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 808109e00decf5c4084be5be550b49e2e7d4628c
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/06/2020
ms.locfileid: "96742354"
---
# <a name="upgrade-the-phonefactor-agent-to-azure-multi-factor-authentication-server"></a>De PhoneFactor-agent upgraden naar Azure Multi-Factor Authentication-server

Als u PhoneFactor-agent versie 5.x of ouder wilt upgraden naar Azure Multi-Factor Authentication-server, verwijdert u eerst de PhoneFactor-agent en alle bijbehorende onderdelen. Vervolgens kunnen de Multi-Factor Authentication-server en de bijbehorende onderdelen worden geïnstalleerd.

> [!IMPORTANT]
> Vanaf 1 juli 2019 biedt micro soft geen MFA-server meer voor nieuwe implementaties. Nieuwe klanten die multi-factor Authentication (MFA) willen vereisen tijdens aanmeldings gebeurtenissen, moeten gebruikmaken van Azure AD-Multi-Factor Authentication in de Cloud.
>
> Zie [zelf studie: aanmeldings gebeurtenissen voor gebruikers beveiligen met Azure AD multi-factor Authentication](tutorial-enable-azure-mfa.md)om aan de slag te gaan met MFA op basis van de Cloud.
>
> Bestaande klanten die MFA-server vóór 1 juli hebben geactiveerd 2019, kunnen de meest recente versie downloaden, toekomstige updates en activerings referenties genereren.

## <a name="uninstall-the-phonefactor-agent"></a>De PhoneFactor-agent verwijderen

1. Maak eerst een back-up van het PhoneFactor-gegevensbestand. De standaardlocatie voor installatie is C:\Program Files\PhoneFactor\Data\Phonefactor.pfdata.

2. Ga als volgt te werk als de gebruikersportal is geïnstalleerd:
   1. Ga naar de installatiemap en maak een back-up van het bestand web.config. De standaardlocatie voor installatie is C:\inetpub\wwwroot\PhoneFactor.

   2. Als u aangepaste thema's aan de portal hebt toegevoegd, maak dan een back-up van uw aangepaste map onder de map C:\inetpub\wwwroot\PhoneFactor\App_Themes.

   3. Verwijder de gebruikersportal via de PhoneFactor-agent (alleen beschikbaar als deze is geïnstalleerd op dezelfde server als de PhoneFactor-agent) of via Windows Programma's en onderdelen.

3. Ga als volgt te werk als de webservice voor mobiele apps is geïnstalleerd:

   1. Ga naar de installatiemap en maak een back-up van het bestand web.config. De standaardlocatie voor installatie is C:\inetpub\wwwroot\PhoneFactorPhoneAppWebService.

   2. Verwijder de webservice voor mobiele apps via Windows Programma's en onderdelen.

4. Als de webservice-SDK is geïnstalleerd, verwijder deze dan via de PhoneFactor-agent of via Windows Programma's en onderdelen.

5. Verwijder de PhoneFactor-agent via Windows Programma's en onderdelen.

## <a name="install-the-multi-factor-authentication-server"></a>Installeer de Multi-Factor Authentication-server

Het installatiepad wordt opgehaald uit het register van de vorige installatie van de PhoneFactor-agent. De installatielocatie is dus dezelfde (bijvoorbeeld C:\Program Files\PhoneFactor). Nieuwe installaties hebben een ander standaardinstallatiepad (bijvoorbeeld C:\Program Files\Multi-Factor Authentication Server). Het gegevensbestand van de vorige PhoneFactor-agent moet tijdens de installatie worden bijgewerkt zodat u uw gebruikers en instellingen niet kwijt bent na de installatie van de nieuwe Multi-Factor Authentication-server.

1. Activeer de Multi-Factor Authentication-server wanneer dit wordt gevraagd en controleer of deze is toegewezen aan de juiste replicatiegroep.

2. Als de webservice-SDK eerder was geïnstalleerd, installeert u de nieuwe webservice-SDK via de gebruikersinterface van Multi-Factor Authentication-server.

   De standaard naam van de virtuele map is nu **MultiFactorAuthWebServiceSdk** in plaats van **PhoneFactorWebServiceSdk**. Als u de oude naam wilt gebruiken, moet u de naam van de virtuele map tijdens de installatie wijzigen. Als u de nieuwe standaardnaam wilt gebruiken, moet u de URL wijzigen in alle toepassingen die naar de webservice-SDK verwijzen, zoals de gebruikersportal en de webservice voor mobiele apps, zodat ze naar de juiste locatie wijzigen.

3. Als de gebruikersportal eerder was geïnstalleerd op de PhoneFactor Agent-server, installeert u de nieuwe Multi-Factor Authentication-gebruikersportal via de gebruikersinterface van de Multi-Factor Authentication-server.

   De standaard naam van de virtuele map is nu **MultiFactorAuth** in plaats van **Phone factor**. Als u de oude naam wilt gebruiken, moet u de naam van de virtuele map tijdens de installatie wijzigen. Als u de nieuwe standaardnaam wilt gebruiken, klikt u op het pictogram Gebruikersportal op de Multi-Factor Authentication-server en werkt u de URL van de gebruikersportal bij op het tabblad Instellingen.

4. Ga als volgt te werk als de gebruikersportal en/of webservice voor mobiele apps eerder op een andere server van de PhoneFactor-agent was geïnstalleerd:

   1. Ga naar de installatielocatie (bijvoorbeeld C:\Program Files\PhoneFactor) en kopieer een of meer installatieprogramma's naar de andere server. Er zijn 32-bits en 64-bits installatieprogramma's voor zowel de gebruikersportal als de webservice voor mobiele apps. Deze heten MultiFactorAuthenticationUserPortalSetupXX.msi en MultiFactorAuthenticationMobileAppWebServiceSetupXX.msi.

   2. Als u de gebruikersportal op de webserver wilt installeren, opent u als beheerder een opdrachtprompt en voert u MultiFactorAuthenticationUserPortalSetupXX.msi uit.

      De standaard naam van de virtuele map is nu **MultiFactorAuth** in plaats van **Phone factor**. Als u de oude naam wilt gebruiken, moet u de naam van de virtuele map tijdens de installatie wijzigen. Als u de nieuwe standaard naam wilt gebruiken, klikt u op het pictogram gebruikers Portal in het Multi-Factor Authentication-server en werkt u de URL van de gebruikers portal bij op het tabblad instellingen. Bestaande gebruikers moeten op de hoogte worden gesteld van de nieuwe URL.

   3. Ga naar de installatielocatie van de gebruikersportal (bijvoorbeeld C:\inetpub\wwwroot\MultiFactorAuth) en bewerk het bestand web.config. Kopieer de waarden in de secties appSettings en applicationSettings van het oorspronkelijke bestand web.config, waarvan u vóór de upgrade een back-up hebt gemaakt, naar het nieuwe bestand web.config. Als tijdens de installatie van de webservice-SDK de nieuwe standaardnaam van de virtuele map is aangehouden, wijzigt u de URL in de sectie applicationSettings om naar de juiste locatie te verwijzen. Als in het vorige bestand web.config nog andere standaardinstellingen zijn gewijzigd, past u dezelfde wijzigingen ook toe op het nieuwe bestand web.config.

> [!NOTE]
> Bij een upgrade van een versie van Azure MFA Server die ouder is dan 8.0 naar 8.0 +, kan de webservice voor mobiele apps worden verwijderd na de upgrade

## <a name="next-steps"></a>Volgende stappen

- [De gebruikersportal installeren](howto-mfaserver-deploy-userportal.md) voor de Azure Multi-Factor Authentication-server.

- [Windows-verificatie configureren](howto-mfaserver-windows.md) voor uw toepassingen. 
