---
title: 'Azure AD Connect: problemen met Pass Through-verificatie oplossen | Microsoft Docs'
description: In dit artikel wordt beschreven hoe u Pass-Through-verificatie Azure Active Directory (Azure AD) kunt oplossen.
services: active-directory
keywords: Problemen met Azure AD Connect Pass-Through-verificatie oplossen, Active Directory installeren, vereiste onderdelen voor Azure AD, SSO, eenmalige aanmelding
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 01/25/2021
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9a014bd5c8f1edbfb00019b8541cef552271d65b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98762846"
---
# <a name="troubleshoot-azure-active-directory-pass-through-authentication"></a>Problemen met Pass Through-verificatie in Azure Active Directory oplossen

Dit artikel helpt u bij het vinden van probleemoplossings informatie over veelvoorkomende problemen met Azure AD Pass-Through-verificatie.

>[!IMPORTANT]
>Als u problemen ondervindt bij het aanmelden van gebruikers met Pass-Through-verificatie, schakelt u de functie niet uit of verwijdert u Pass-Through-verificatie agenten zonder alleen een globale beheerders account voor de cloud om terug te vallen. Meer informatie over [het toevoegen van een globale beheerders account voor de Cloud](../fundamentals/add-users-azure-active-directory.md). Deze stap is kritiek en zorgt ervoor dat u uw Tenant niet hebt vergrendeld.

## <a name="general-issues"></a>Algemene problemen

### <a name="check-status-of-the-feature-and-authentication-agents"></a>De status van de functie en verificatie agenten controleren

Zorg ervoor dat de functie Pass-Through-verificatie nog steeds is **ingeschakeld** voor uw Tenant en de status van verificatie agenten **actief** is en niet **inactief**. U kunt de status controleren door naar de Blade **Azure AD Connect** in het [Azure Active Directory beheer centrum](https://aad.portal.azure.com/)te gaan.

![Azure Active Directory-beheer centrum-Azure AD Connect Blade](./media/tshoot-connect-pass-through-authentication/pta7.png)

![Azure Active Directory beheer centrum: Pass Through-verificatie Blade](./media/tshoot-connect-pass-through-authentication/pta11.png)

### <a name="user-facing-sign-in-error-messages"></a>Fout berichten voor gebruikers gerichte aanmelding

Als de gebruiker zich niet kan aanmelden met behulp van Pass-Through-verificatie, zien ze mogelijk een van de volgende gebruikers fouten in het aanmeldings scherm van Azure AD: 

|Fout|Beschrijving|Oplossing
| --- | --- | ---
|AADSTS80001|Kan geen verbinding maken met Active Directory|Zorg ervoor dat de agent servers lid zijn van hetzelfde AD-forest als de gebruikers waarvan de wacht woorden moeten worden gevalideerd en dat ze verbinding kunnen maken met Active Directory.  
|AADSTS8002|Er is een time-out opgetreden tijdens het verbinden met Active Directory|Controleer of Active Directory beschikbaar is en reageert op aanvragen van de agents.
|AADSTS80004|De gebruikers naam die aan de agent is door gegeven, is niet geldig|Zorg ervoor dat de gebruiker zich probeert aan te melden met de juiste gebruikers naam.
|AADSTS80005|Validatie heeft onvoorspelbare Web-except aangetroffen|Een tijdelijke fout. Voer de aanvraag opnieuw uit. Neem contact op met micro soft ondersteuning als dit probleem blijft optreden.
|AADSTS80007|Er is een fout opgetreden tijdens het communiceren met Active Directory|Raadpleeg de logboeken van de agent voor meer informatie en controleer of Active Directory werkt zoals verwacht.

### <a name="users-get-invalid-usernamepassword-error"></a>Fout bij het ophalen van ongeldige gebruikers naam/wacht woord 

Dit kan gebeuren wanneer de on-premises UserPrincipalName van een gebruiker (UPN) afwijkt van de Cloud-UPN van de gebruiker.

U kunt controleren of dit het probleem is door eerst te testen of de Pass-Through-verificatie agent correct werkt:


1. Een test account maken.  
2. Importeer de Power shell-module op de agent computer:

 ```powershell
 Import-Module "C:\Program Files\Microsoft Azure AD Connect Authentication Agent\Modules\PassthroughAuthPSModule\PassthroughAuthPSModule.psd1"
 ```
3. Voer de opdracht voor het aanroepen van Power shell uit: 

 ```powershell
 Invoke-PassthroughAuthOnPremLogonTroubleshooter 
 ``` 
4. Wanneer u wordt gevraagd om referenties in te voeren, voert u dezelfde gebruikers naam en hetzelfde wacht woord in als voor aanmelding bij ( https://login.microsoftonline.com) .

Als u dezelfde gebruikers naam en hetzelfde wacht woord krijgt, betekent dit dat de Pass-Through-verificatie agent correct werkt en het probleem mogelijk is dat de on-premises UPN niet routeerbaar is. Zie de [alternatieve aanmeldings-id configureren](/windows-server/identity/ad-fs/operations/configuring-alternate-login-id)voor meer informatie.

> [!IMPORTANT]
> Als de Azure AD Connect server geen lid is van een domein, wordt een vereiste in [Azure AD Connect:](./how-to-connect-install-prerequisites.md#installation-prerequisites)vereisten, het ongeldige probleem met de gebruikers naam en het wacht woord.

### <a name="sign-in-failure-reasons-on-the-azure-active-directory-admin-center-needs-premium-license"></a>Redenen voor aanmeldings fout op het Azure Active Directory-beheer centrum (vereiste Premium-licentie)

Als aan uw Tenant een Azure AD Premium-licentie is gekoppeld, kunt u ook het rapport voor de [aanmeldings activiteit](../reports-monitoring/concept-sign-ins.md) in het [Azure Active Directory beheer centrum](https://aad.portal.azure.com/)bekijken.

![Azure Active Directory beheer centrum-aanmeld rapport](./media/tshoot-connect-pass-through-authentication/pta4.png)

Navigeer naar **Azure Active Directory**  ->  **-aanmeldingen** in het [Azure Active Directory beheer centrum](https://aad.portal.azure.com/) en klik op de aanmeldings activiteit van een specifieke gebruiker. Zoek het veld **met de fout code** voor de aanmelding. Wijs de waarde van dat veld toe aan een fout reden en oplossing met behulp van de volgende tabel:

|Fout code voor aanmelden|Reden voor aanmeldings fout|Oplossing
| --- | --- | ---
| 50144 | Het Active Directory-wachtwoord van de gebruiker is verlopen. | Het wacht woord van de gebruiker opnieuw instellen in uw on-premises Active Directory.
| 80001 | Er is geen verificatieagent beschikbaar. | Installeer en Registreer een verificatie agent.
| 80002 | Er is een time-out opgetreden bij de wachtwoordvalidatie voor de verificatieagent. | Controleer of uw Active Directory bereikbaar is vanaf de verificatie agent.
| 80003 | Ongeldig antwoord ontvangen door de verificatieagent. | Als het probleem voortdurend kan worden gereproduceerd voor meerdere gebruikers, controleert u de configuratie van Active Directory.
| 80004 | Onjuiste UPN (user principal name) gebruikt voor aanmeldingsaanvraag. | Vraag de gebruiker zich aan te melden met de juiste gebruikers naam.
| 80005 | Verificatieagent: er is een fout opgetreden. | Tijdelijke fout. Probeer het later opnieuw.
| 80007 | Verificatieagent kan geen verbinding maken met Active Directory. | Controleer of uw Active Directory bereikbaar is vanaf de verificatie agent.
| 80010 | Verificatieagent kan wachtwoord niet ontsleutelen. | Als het probleem zich voortdurend reproduceert, installeert en registreert u een nieuwe verificatie agent. En verwijder de huidige versie. 
| 80011 | Verificatieagent kan ontsleutelingssleutel hier ophalen. | Als het probleem zich voortdurend reproduceert, installeert en registreert u een nieuwe verificatie agent. En verwijder de huidige versie.
| 80014 | Validatie aanvraag heeft gereageerd na overschrijding van de maximale verstreken tijd. | Time-out van verificatie agent. Open een ondersteunings ticket met de fout code, de correlatie-ID en de tijds tempel om meer informatie te krijgen over deze fout

>[!IMPORTANT]
>Pass-Through-verificatie agenten verifiëren Azure AD-gebruikers door hun gebruikers namen en wacht woorden te valideren op Active Directory door de [Win32 LogonUser-API](/windows/win32/api/winbase/nf-winbase-logonusera)aan te roepen. Als u dus de instelling aanmelden op in Active Directory hebt ingesteld om de toegang tot het werk station te beperken, moet u servers die fungeren als host voor Pass-Through-verificatie agenten ook toevoegen aan de lijst met servers waarop zich moet aanmelden. Als u dit niet doet, worden uw gebruikers geblokkeerd om zich aan te melden bij Azure AD.

## <a name="authentication-agent-installation-issues"></a>Installatie problemen met verificatie agent

### <a name="an-unexpected-error-occurred"></a>Er is een onverwachte fout opgetreden

[Verzamel Agent logboeken](#collecting-pass-through-authentication-agent-logs) van de server en neem contact op met Microsoft ondersteuning met uw probleem.

## <a name="authentication-agent-registration-issues"></a>Registratie problemen met verificatie agent

### <a name="registration-of-the-authentication-agent-failed-due-to-blocked-ports"></a>Registratie van de verificatie agent is mislukt vanwege geblokkeerde poorten

Zorg ervoor dat de server waarop de verificatie agent is geïnstalleerd, kan communiceren met onze service-Url's en-poorten die [hier](how-to-connect-pta-quick-start.md#step-1-check-the-prerequisites)worden vermeld.

### <a name="registration-of-the-authentication-agent-failed-due-to-token-or-account-authorization-errors"></a>De registratie van de verificatie agent is mislukt vanwege token-of account autorisatie fouten

Zorg ervoor dat u een globale beheerders account voor de Cloud gebruikt voor alle Azure AD Connect of installatie van de zelfstandige verificatie agent en registratie bewerkingen. Er is een bekend probleem met accounts voor globale beheerders met MFA. Schakel MFA tijdelijk uit (alleen om de bewerkingen te volt ooien) als tijdelijke oplossing.

### <a name="an-unexpected-error-occurred"></a>Er is een onverwachte fout opgetreden

[Verzamel Agent logboeken](#collecting-pass-through-authentication-agent-logs) van de server en neem contact op met Microsoft ondersteuning met uw probleem.

## <a name="authentication-agent-uninstallation-issues"></a>Problemen met de installatie van verificatie-agent

### <a name="warning-message-when-uninstalling-azure-ad-connect"></a>Waarschuwings bericht bij het verwijderen van Azure AD Connect

Als u Pass-Through-verificatie hebt ingeschakeld in uw Tenant en u probeert Azure AD Connect te verwijderen, wordt het volgende waarschuwings bericht weer gegeven: ' gebruikers kunnen zich niet aanmelden bij Azure AD, tenzij u andere Pass-Through-verificatie agenten op andere servers hebt geïnstalleerd. '

Zorg ervoor dat uw installatie [Maxi maal beschikbaar](how-to-connect-pta-quick-start.md#step-4-ensure-high-availability) is voordat u Azure AD Connect verwijdert om te voor komen dat gebruikers zich aanmelden.

## <a name="issues-with-enabling-the-feature"></a>Problemen met het inschakelen van de functie

### <a name="enabling-the-feature-failed-because-there-were-no-authentication-agents-available"></a>Het inschakelen van de functie is mislukt omdat er geen verificatie agenten beschikbaar zijn

U moet ten minste één actieve verificatie agent hebben om Pass-Through-verificatie in te scha kelen voor uw Tenant. U kunt een verificatie agent installeren door Azure AD Connect of een zelfstandige verificatie agent te installeren.

### <a name="enabling-the-feature-failed-due-to-blocked-ports"></a>Het inschakelen van de functie is mislukt vanwege geblokkeerde poorten

Zorg ervoor dat de server waarop Azure AD Connect is geïnstalleerd, kan communiceren met de service-Url's en-poorten die [hier](how-to-connect-pta-quick-start.md#step-1-check-the-prerequisites)worden vermeld.

### <a name="enabling-the-feature-failed-due-to-token-or-account-authorization-errors"></a>Het inschakelen van de functie is mislukt vanwege token-of account autorisatie fouten

Zorg ervoor dat u een globale beheerders account voor de Cloud gebruikt wanneer u de functie inschakelt. Er is een bekend probleem met globale beheerders accounts voor multi-factor Authentication (MFA). Schakel MFA tijdelijk uit (alleen om de bewerking te volt ooien) als tijdelijke oplossing.

## <a name="collecting-pass-through-authentication-agent-logs"></a>Logboeken voor Pass Through-verificatie agent verzamelen

Afhankelijk van het type probleem dat u mogelijk hebt, moet u de logboeken van de Pass-Through-verificatie agent zoeken op verschillende locaties.

### <a name="azure-ad-connect-logs"></a>Azure AD Connect logboeken

Raadpleeg de Azure AD Connect-Logboeken in **%ProgramData%\AADConnect\trace- \* . log** voor fouten met betrekking tot de installatie.

### <a name="authentication-agent-event-logs"></a>Gebeurtenis logboeken voor verificatie agent

Voor fouten met betrekking tot de verificatie agent opent u de Logboeken-toepassing op de server en controleert u onder **toepassings-en service-Logs\Microsoft\AzureAdConnect\AuthenticationAgent\Admin**.

Schakel voor gedetailleerde analyse het logboek van de sessie in (Klik met de rechter muisknop in de Logboeken toepassing om deze optie te vinden). De verificatie agent niet uitvoeren wanneer dit logboek is ingeschakeld tijdens normale bewerkingen; alleen gebruiken voor probleem oplossing. De inhoud van het logboek is alleen zichtbaar nadat het logboek is uitgeschakeld.



### <a name="detailed-trace-logs"></a>Gedetailleerde traceer logboeken

Zoek naar traceer logboeken op **%PROGRAMDATA%\MICROSOFT\AZURE AD Connect- \\ verificatie Agent\Trace** om problemen met de gebruikers aanmelding op te lossen. In deze logboeken worden de redenen vermeld waarom een specifieke gebruiker zich aanmeldt met de functie Pass-Through-verificatie. Deze fouten worden ook toegewezen aan de oorzaken van de aanmeldings fout die worden weer gegeven in de voor gaande tabel met mislukte aanmeldings redenen. Hier volgt een voor beeld van een logboek vermelding:

```
    AzureADConnectAuthenticationAgentService.exe Error: 0 : Passthrough Authentication request failed. RequestId: 'df63f4a4-68b9-44ae-8d81-6ad2d844d84e'. Reason: '1328'.
        ThreadId=5
        DateTime=xxxx-xx-xxTxx:xx:xx.xxxxxxZ
```

U kunt een beschrijving van de fout (' 1328 ' in het voor gaande voor beeld) verkrijgen door de opdracht prompt te openen en de volgende opdracht uit te voeren (Opmerking: Vervang ' 1328 ' door het werkelijke fout nummer dat u in uw logboeken ziet):

`Net helpmsg 1328`

![Pass-through-verificatie](./media/tshoot-connect-pass-through-authentication/pta3.png)

### <a name="domain-controller-logs"></a>Domein controller logboeken

Als controle logboek registratie is ingeschakeld, kunt u aanvullende informatie vinden in de beveiligings logboeken van uw domein controllers. Een eenvoudige manier om aanmeldings aanvragen te doorzoeken die door Pass Through-verificatie agenten worden verzonden, is als volgt:

```
    <QueryList>
    <Query Id="0" Path="Security">
    <Select Path="Security">*[EventData[Data[@Name='ProcessName'] and (Data='C:\Program Files\Microsoft Azure AD Connect Authentication Agent\AzureADConnectAuthenticationAgentService.exe')]]</Select>
    </Query>
    </QueryList>
```

## <a name="performance-monitor-counters"></a>Prestatie meter items

Een andere manier om verificatie agenten te controleren is het bijhouden van specifieke prestatie meter items op elke server waarop de verificatie agent is geïnstalleerd. Gebruik de volgende globale tellers (**# PTA-verificaties**, **#PTA mislukte verificaties** en **#PTA geslaagde authenticaties**) en fout tellers (**# PTA-verificatie fouten**):

![Prestatie meter items Pass-Through-verificatie](./media/tshoot-connect-pass-through-authentication/pta12.png)

>[!IMPORTANT]
>Pass-Through-verificatie biedt hoge Beschik baarheid met meerdere verificatie agenten en _geen_ taak verdeling. Afhankelijk van uw configuratie worden _niet_ alle verificatie agenten ongeveer _gelijk aan_ het aantal aanvragen ontvangen. Het is mogelijk dat een specifieke verificatie agent helemaal geen verkeer ontvangt.
