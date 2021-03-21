---
title: 'Azure AD Connect: Pass-Through-verificatie: veelgestelde vragen | Microsoft Docs'
description: Antwoorden op veelgestelde vragen over de Pass-Through-verificatie van Azure Active Directory
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
ms.date: 06/09/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 75f797c40a276323cea9983c5340d2d854160c83
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97368474"
---
# <a name="azure-active-directory-pass-through-authentication-frequently-asked-questions"></a>Pass-Through-verificatie Azure Active Directory: veelgestelde vragen

In dit artikel vindt u veelgestelde vragen over de Pass-Through-verificatie van Azure Active Directory (Azure AD). Blijf op de hoogte van bijgewerkte inhoud.

## <a name="which-of-the-methods-to-sign-in-to-azure-ad-pass-through-authentication-password-hash-synchronization-and-active-directory-federation-services-ad-fs-should-i-choose"></a>Welke van de methoden om u aan te melden bij Azure AD, Pass Through-verificatie, wachtwoord hash-synchronisatie en Active Directory Federation Services (AD FS), moet ik kiezen?

Raadpleeg [deze hand leiding](./choose-ad-authn.md) voor een vergelijking van de verschillende aanmeldings methoden van Azure AD en het kiezen van de juiste aanmeldings methode voor uw organisatie.

## <a name="is-pass-through-authentication-a-free-feature"></a>Is de Pass-Through-verificatie een gratis functie?

Pass-Through-verificatie is een gratis functie. U hebt geen betaalde versies van Azure AD nodig om het te gebruiken.

## <a name="does-conditional-access-work-with-pass-through-authentication"></a>Werkt [voorwaardelijke toegang](../conditional-access/overview.md) met Pass-Through-verificatie?

Ja. Alle mogelijkheden voor voorwaardelijke toegang, inclusief Azure AD Multi-Factor Authentication, werken met Pass-Through-verificatie.

## <a name="does-pass-through-authentication-support-alternate-id-as-the-username-instead-of-userprincipalname"></a>Ondersteunt Pass-Through-verificatie "alternatieve ID" als de gebruikers naam, in plaats van "userPrincipalName"?
Ja, aanmelden met een niet-UPN-waarde, zoals een alternatieve e-mail, wordt ondersteund voor Pass-Through-verificatie (PTA) en wacht woord-hash-synchronisatie (PHS). Voor meer informatie over [alternatieve aanmeldings-id](../authentication/howto-authentication-use-email-signin.md).

## <a name="does-password-hash-synchronization-act-as-a-fallback-to-pass-through-authentication"></a>Fungeert wachtwoord hash synchroniseren als terugval voor Pass-Through-verificatie?

Nee. Pass-Through-verificatie _voert niet_ automatisch een failover uit naar een wachtwoord hash-synchronisatie. Als u wilt voor komen dat gebruikers zich aanmelden, moet u Pass-Through-verificatie configureren voor [maximale Beschik baarheid](how-to-connect-pta-quick-start.md#step-4-ensure-high-availability).

## <a name="what-happens-when-i-switch-from-password-hash-synchronization-to-pass-through-authentication"></a>Wat gebeurt er wanneer ik de synchronisatie van wacht woord-hash naar Pass Through-verificatie uitschakel?

Wanneer u Azure AD Connect gebruikt om de aanmeldings methode te wijzigen van de wachtwoord-hash-synchronisatie naar Pass-Through-verificatie, wordt Pass Through-verificatie de primaire aanmeldings methode voor uw gebruikers in beheerde domeinen. Houd er rekening mee dat de wacht woord-hashes van alle gebruikers die eerder zijn gesynchroniseerd door de synchronisatie van wacht woord-hash, in azure AD zijn opgeslagen.

## <a name="can-i-install-an-azure-ad-application-proxy-connector-on-the-same-server-as-a-pass-through-authentication-agent"></a>Kan ik een [Azure AD-toepassingsproxy](../manage-apps/application-proxy.md) -connector installeren op dezelfde server als een Pass-Through-verificatie agent?

Ja. De versies van de Pass-Through-verificatie agent, versie 1.5.193.0 of hoger, ondersteunen deze configuratie.

## <a name="what-versions-of-azure-ad-connect-and-pass-through-authentication-agent-do-you-need"></a>Welke versies van Azure AD Connect en Pass Through-verificatie agent hebt u nodig?

Voor een goede werking van deze functie hebt u versie 1.1.750.0 of hoger nodig voor Azure AD Connect en 1.5.193.0 of hoger voor de Pass-Through-verificatie agent. Installeer alle software op servers met Windows Server 2012 R2 of hoger.

## <a name="what-happens-if-my-users-password-has-expired-and-they-try-to-sign-in-by-using-pass-through-authentication"></a>Wat gebeurt er als mijn gebruikers wachtwoord is verlopen en u zich probeert aan te melden met behulp van Pass-Through-verificatie?

Als u het [terugschrijven van wacht woorden](../authentication/concept-sspr-writeback.md) voor een specifieke gebruiker hebt geconfigureerd en als de gebruiker zich aanmeldt met behulp van Pass-Through-verificatie, kunnen ze hun wacht woord wijzigen of opnieuw instellen. De wacht woorden worden teruggeschreven naar een on-premises Active Directory, zoals verwacht.

Als u het terugschrijven van wacht woorden niet hebt geconfigureerd voor een specifieke gebruiker of als aan de gebruiker geen geldige Azure AD-licentie is toegewezen, kan de gebruiker hun wacht woord niet bijwerken in de Cloud. Ze kunnen hun wacht woord niet bijwerken, zelfs niet als het wacht woord is verlopen. In plaats daarvan ziet de gebruiker het volgende bericht: "uw organisatie staat niet toe dat u uw wacht woord op deze site bijwerkt. Werk het bij volgens de methode die door uw organisatie wordt aanbevolen of vraag uw beheerder om hulp nodig. " De gebruiker of de beheerder moet het wacht woord opnieuw instellen in on-premises Active Directory.

## <a name="how-does-pass-through-authentication-protect-you-against-brute-force-password-attacks"></a>Hoe wordt de Pass-Through-verificatie beschermd tegen aanvallen met felle wacht woorden?

[Lees informatie over slimme vergren delingen](../authentication/howto-password-smart-lockout.md).

## <a name="what-do-pass-through-authentication-agents-communicate-over-ports-80-and-443"></a>Wat doen Pass Through-verificatie agenten communiceren via poorten 80 en 443?

- De verificatie agenten maken HTTPS-aanvragen via poort 443 voor alle onderdeel bewerkingen.
- De verificatie agenten maken HTTP-aanvragen via poort 80 om de TLS/SSL-certificaatintrekkingslijsten (Crl's) te downloaden.

     >[!NOTE]
     >Recente updates hebben het aantal poorten verminderd dat de functie vereist. Als u oudere versies van Azure AD Connect of de verificatie agent hebt, moet u deze poorten ook openen: 5671, 8080, 9090, 9091, 9350, 9352 en 10100-10120.

## <a name="can-the-pass-through-authentication-agents-communicate-over-an-outbound-web-proxy-server"></a>Kunnen de Pass-Through-verificatie agenten communiceren via een uitgaande webproxy-server?

Ja. Als WPAD (Web Proxy Auto-Discovery) is ingeschakeld in uw on-premises omgeving, proberen verificatie agenten automatisch een webproxyserver in het netwerk te zoeken en te gebruiken.

Als u geen WPAD hebt in uw-omgeving, kunt u proxy gegevens toevoegen (zoals hieronder wordt weer gegeven), zodat een Pass-Through-verificatie agent kan communiceren met Azure AD:
- Configureer proxy-informatie in Internet Explorer voordat u de Pass-Through-verificatie agent op de server installeert. Hiermee kunt u de installatie van de verificatie agent volt ooien, maar deze wordt nog steeds weer gegeven als **inactief** in de beheer Portal.
- Ga op de-server naar ' C:\Program Files\Microsoft Azure AD Connect authentication agent '.
- Bewerk het configuratie bestand ' AzureADConnectAuthenticationAgentService ' en voeg de volgende regels toe (Vervang ' http \: //contosoproxy.com:8080 ' door het daad werkelijke proxy adres):

```
   <system.net>
      <defaultProxy enabled="true" useDefaultCredentials="true">
         <proxy
            usesystemdefault="true"
            proxyaddress="http://contosoproxy.com:8080"
            bypassonlocal="true"
         />
     </defaultProxy>
   </system.net>
```

## <a name="can-i-install-two-or-more-pass-through-authentication-agents-on-the-same-server"></a>Kan ik twee of meer Pass-Through-verificatie agenten op dezelfde server installeren?

Nee, u kunt slechts één Pass-Through-verificatie agent op één server installeren. Als u Pass-Through-verificatie voor hoge Beschik baarheid wilt configureren, [volgt u de instructies hier](how-to-connect-pta-quick-start.md#step-4-ensure-high-availability).

## <a name="do-i-have-to-manually-renew-certificates-used-by-pass-through-authentication-agents"></a>Moet ik certificaten die worden gebruikt door Pass Through-verificatie agenten hand matig verlengen?

De communicatie tussen elke pass-through-verificatie agent en Azure AD wordt beveiligd met verificatie op basis van certificaten. Deze [certificaten worden elke paar maanden automatisch door Azure AD verlengd](how-to-connect-pta-security-deep-dive.md#operational-security-of-the-authentication-agents). U hoeft deze certificaten niet hand matig te vernieuwen. U kunt oude verlopen certificaten zo nodig opschonen.

## <a name="how-do-i-remove-a-pass-through-authentication-agent"></a>Een Pass-Through-verificatie agent Hoe kan ik verwijderen?

Zolang er een Pass-Through-verificatie agent wordt uitgevoerd, blijft deze actief en wordt de aanmeldings aanvragen van gebruikers continu verwerkt. Als u de installatie van een verificatie agent wilt ongedaan maken, gaat u naar **configuratie scherm-> Program ma's-> Program ma's en onderdelen** en verwijdert u de **Microsoft Azure AD Connect Authentication Agent** en de Microsoft Azure AD Program ma's voor de verbinding met de **agent bijwerken** .

Als u de Blade Pass-Through-verificatie in het [Azure Active Directory-beheer centrum](https://aad.portal.azure.com) controleert nadat u de vorige stap hebt voltooid, ziet u dat de verificatie agent wordt weer gegeven als **inactief**. Dit wordt _verwacht_. De verificatie agent wordt na 10 dagen automatisch uit de lijst verwijderd.

## <a name="i-already-use-ad-fs-to-sign-in-to-azure-ad-how-do-i-switch-it-to-pass-through-authentication"></a>Ik gebruik AD FS al om je aan te melden bij Azure AD. Hoe kan ik overschakelen naar Pass-Through-verificatie?

Als u migreert van AD FS (of andere Federatie technologieën) naar Pass-Through-verificatie, raden we u ten zeerste aan de gedetailleerde implementatie handleiding te volgen die u [hier](https://github.com/Identity-Deployment-Guides/Identity-Deployment-Guides/blob/master/Authentication/Migrating%20from%20Federated%20Authentication%20to%20Pass-through%20Authentication.docx?raw=true)hebt gepubliceerd.

## <a name="can-i-use-pass-through-authentication-in-a-multi-forest-active-directory-environment"></a>Kan ik Pass-Through-verificatie gebruiken in een omgeving met meerdere forests Active Directory?

Ja. Omgevingen met meerdere forests worden ondersteund als er forest-vertrouwens relaties zijn tussen uw Active Directory bossen en als de route ring van het naam achtervoegsel correct is geconfigureerd.

## <a name="does-pass-through-authentication-provide-load-balancing-across-multiple-authentication-agents"></a>Biedt Pass Through-verificatie taak verdeling over meerdere verificatie agenten?

Nee, het installeren van meerdere Pass Through-verificatie agenten garandeert alleen een [hoge Beschik baarheid](how-to-connect-pta-quick-start.md#step-4-ensure-high-availability). Het biedt geen deterministische taak verdeling tussen de verificatie agenten. Elke verificatie-agent (wille keurig) kan een bepaalde aanmeldings aanvraag van de gebruiker verwerken.

## <a name="how-many-pass-through-authentication-agents-do-i-need-to-install"></a>Hoeveel Pass Through-verificatie agenten moet ik installeren?

Het installeren van meerdere Pass Through-verificatie agenten garandeert een [hoge Beschik baarheid](how-to-connect-pta-quick-start.md#step-4-ensure-high-availability). Maar biedt geen deterministische taak verdeling tussen de verificatie agenten.

Houd rekening met de piek en de gemiddelde belasting van aanmeldings aanvragen die u verwacht te zien in uw Tenant. Als referentie kan één verificatie agent 300 tot 400 authenticaties per seconde afhandelen op een standaard 4-core CPU, 16 GB RAM-server.

Als u het netwerk verkeer wilt schatten, gebruikt u de volgende richt lijnen:
- Elke aanvraag heeft een Payload grootte van (0,5 K + 1K * num_of_agents) bytes; gegevens van Azure AD naar de verificatie agent. Num_of_agents geeft hier het aantal verificatie agenten aan dat is geregistreerd op uw Tenant.
- Elk antwoord heeft een Payload-grootte van 1 KB aan bytes; gegevens van de verificatie-agent naar Azure AD.

Voor de meeste klanten zijn twee of drie verificatie agenten in totaal voldoende voor hoge Beschik baarheid en capaciteit. U moet verificatie agenten voor uw domein controllers sluiten om de aanmeldings latentie te verbeteren.

>[!NOTE]
>Er is een systeem limiet van 40-verificatie agenten per Tenant.

## <a name="why-do-i-need-a-cloud-only-global-administrator-account-to-enable-pass-through-authentication"></a>Waarom heb ik een globale beheerders account voor de Cloud nodig om Pass-Through-verificatie in te scha kelen?

Het is raadzaam Pass-Through-verificatie in of uit te scha kelen met behulp van een alleen-Cloud account voor globale beheerders. Meer informatie over [het toevoegen van een globale beheerders account voor de Cloud](../fundamentals/add-users-azure-active-directory.md). Op deze manier zorgt u ervoor dat uw Tenant niet wordt vergrendeld.

## <a name="how-can-i-disable-pass-through-authentication"></a>Hoe kan ik Pass-Through-verificatie uitschakelen?

Voer de wizard Azure AD Connect opnieuw uit en wijzig de aanmeldings methode voor gebruikers van Pass-Through-verificatie naar een andere methode. Met deze wijziging wordt Pass-Through-verificatie op de Tenant uitgeschakeld en wordt de verificatie agent van de server verwijderd. U moet de verificatie agenten hand matig van de andere servers verwijderen.

## <a name="what-happens-when-i-uninstall-a-pass-through-authentication-agent"></a>Wat gebeurt er wanneer ik een Pass-Through-verificatie agent Verwijder?

Als u een Pass-Through-verificatie agent van een server verwijdert, wordt de server gestopt met het accepteren van aanmeldings aanvragen. Om te voor komen dat gebruikers zich aanmelden bij uw Tenant, moet u ervoor zorgen dat er een andere verificatie agent wordt uitgevoerd voordat u een Pass-Through-verificatie agent verwijdert.

## <a name="i-have-an-older-tenant-that-was-originally-setup-using-ad-fs--we-recently-migrated-to-pta-but-now-are-not-seeing-our-upn-changes-synchronizing-to-azure-ad--why-are-our-upn-changes-not-being-synchronized"></a>Ik heb een oudere Tenant die oorspronkelijk is ingesteld met behulp van AD FS.  Onlangs gemigreerd naar PTA, maar de UPN-wijzigingen worden niet meer in azure AD gesynchroniseerd.  Waarom worden de UPN-wijzigingen niet gesynchroniseerd?

A: in de volgende gevallen worden uw on-premises UPN-wijzigingen mogelijk niet gesynchroniseerd als:

- Uw Azure AD-Tenant is gemaakt vóór 15 juni 2015
- U bent in eerste instantie federatieve met uw Azure AD-Tenant met behulp van AD FS voor verificatie
- U bent overgeschakeld op het hebben van beheerde gebruikers met behulp van PTA als verificatie

Dit komt omdat het standaard gedrag van tenants die vóór 15 juni 2015 zijn gemaakt, de UPN-wijzigingen blokkeert.  Als u de UPN-wijzigingen wilt blok keren, moet u de volgende Power shell-cmdlt uitvoeren:  

`Set-MsolDirSyncFeature -Feature SynchronizeUpnForManagedUsers -Enable $True`

Tenants die zijn gemaakt na 15 juni 2015, hebben het standaard gedrag om UPN-wijzigingen te synchroniseren.   



## <a name="next-steps"></a>Volgende stappen
- [Huidige beperkingen](how-to-connect-pta-current-limitations.md): meer informatie over welke scenario's worden ondersteund en wat niet.
- [Snel starten](how-to-connect-pta-quick-start.md): aan de slag met Azure AD Pass-Through-verificatie.
- [Migratie van AD FS naar Pass-](https://github.com/Identity-Deployment-Guides/Identity-Deployment-Guides/blob/master/Authentication/Migrating%20from%20Federated%20Authentication%20to%20Pass-through%20Authentication.docx?raw=true) through-verificatie: een gedetailleerde hand leiding voor het migreren van AD FS (of andere Federatie technologieën) naar Pass-Through-verificatie.
- [Slimme vergren deling](../authentication/howto-password-smart-lockout.md): informatie over het configureren van de mogelijkheden voor slim vergren delen van uw Tenant voor het beveiligen van gebruikers accounts.
- [Technisch diep gaande](how-to-connect-pta-how-it-works.md): begrijpen hoe de functie Pass-Through-verificatie werkt.
- [Problemen oplossen](tshoot-connect-pass-through-authentication.md): informatie over het oplossen van veelvoorkomende problemen met de functie Pass-Through-verificatie.
- [Grondige beveiliging](how-to-connect-pta-security-deep-dive.md): krijg uitgebreide technische informatie over de functie Pass-Through-verificatie.
- [Naadloze SSO van Azure AD](how-to-connect-sso.md): meer informatie over deze complementaire functie.
- [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect): gebruik het Azure Active Directory-forum om nieuwe functie aanvragen te verwerken.
