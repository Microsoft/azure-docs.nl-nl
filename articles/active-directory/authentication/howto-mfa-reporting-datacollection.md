---
title: Azure AD MFA-verzameling voor gebruikers gegevens-Azure Active Directory
description: Welke informatie wordt gebruikt om gebruikers te helpen bij het verifiëren van Azure AD Multi-Factor Authentication?
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 11/21/2019
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: c2124ca9fae76d87a695a2c453f7b222e7a08794
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96742790"
---
# <a name="azure-ad-multi-factor-authentication-user-data-collection"></a>Azure AD-Multi-Factor Authentication voor het verzamelen van gebruikers gegevens

In dit document wordt uitgelegd hoe u gebruikers gegevens kunt vinden die zijn verzameld door Azure Multi-Factor Authentication-server (MFA server) en Azure AD MFA (op basis van de Cloud) in de gebeurtenis die u wilt verwijderen.

[!INCLUDE [gdpr-hybrid-note](../../../includes/gdpr-hybrid-note.md)]

## <a name="information-collected"></a>Verzamelde gegevens

MFA-server, de NPS-extensie en de Windows Server 2016 Azure AD MFA-adapter AD FS de volgende informatie te verzamelen en op te slaan voor 90 dagen.

Verificatie pogingen (gebruikt voor rapportage en probleem oplossing):

- Tijdstempel
- Gebruikersnaam
- Voornaam
- Achternaam
- E-mailadres
- Gebruikers groep
- Verificatie methode (telefoon oproep, tekst bericht, mobiele app, OATH-token)
- Modus telefoon oproep (standaard, pincode)
- Tekstbericht richting (in twee richtingen)
- Modus voor tekst berichten (OTP, OTP + pincode)
- Modus voor mobiele app (standaard, pincode)
- Modus OATH-token (standaard, pincode)
- Verificatietype
- Naam van de toepassing
- Land code primaire oproep
- Telefoon nummer van primaire oproep
- Primaire oproep uitbreiding
- Primair gesprek geverifieerd
- Primair oproep resultaat
- Land code back-upoproep
- Telefoon nummer voor back-upoproep
- Uitbrei ding back-upaanroep
- Back-upaanroep geverifieerd
- Resultaat van back-upaanroep
- Algemeen geverifieerd
- Algemeen resultaat
- Resultaten
- Geverifieerd
- Resultaat
- IP-adres wordt gestart
- Apparaten
- Token van apparaat
- Apparaattype
- Versie van mobiele app
- Versie van het besturingssysteem
- Resultaat
- Gebruikte controle voor melding

Activeringen (probeert een account te activeren in de mobiele app Microsoft Authenticator):
- Gebruikersnaam
- Accountnaam
- Tijdstempel
- Resultaat van activerings code ophalen
- Activeren geslaagd
- Fout activeren
- Resultaat van activerings status
- Apparaatnaam
- Apparaattype
- App-versie
- OATH-token ingeschakeld

Blokken (gebruikt voor het bepalen van de geblokkeerde status en voor rapportage):

- Tijds tempel blok keren
- Blok keren op gebruikers naam
- Gebruikersnaam
- Landnummer
- Telefoonnummer
- Telefoon nummer geformatteerd
- Toestelnummer
- Extensie wissen
- Geblokkeerd
- Reden voor blok kering
- Tijds tempel van voltooiing
- Voltooiings reden
- Accountvergrendeling
- Fraude waarschuwing
- Fraude waarschuwing niet geblokkeerd
- Taal

Bypass (gebruikt voor rapportage):

- Tijds tempel overs Laan
- Bypass-seconden
- Overs laan op gebruikers naam
- Gebruikersnaam
- Landnummer
- Telefoonnummer
- Telefoon nummer geformatteerd
- Toestelnummer
- Extensie wissen
- Reden voor bypass
- Tijds tempel van voltooiing
- Voltooiings reden
- Gebruik overs Laan

Wijzigingen (gebruikt voor het synchroniseren van gebruikers wijzigingen naar de MFA-server of Azure AD):

- Tijds tempel wijzigen
- Gebruikersnaam
- Nieuwe land code
- Nieuw telefoon nummer
- Nieuwe extensie
- Nieuwe land code voor back-up
- Nieuw telefoon nummer voor back-up
- Nieuwe back-upextensie
- Nieuwe pincode
- PINCODE wijziging vereist
- Verouderd apparaat-token
- Nieuw apparaat-token

## <a name="gather-data-from-mfa-server"></a>Gegevens verzamelen van MFA-server

Voor MFA Server versie 8,0 of hoger kunt u met het volgende proces alle gegevens voor gebruikers exporteren:

- Meld u aan bij uw MFA-server, navigeer naar het tabblad **gebruikers** , selecteer de gebruiker in kwestie en klik op de knop **bewerken** . Maak scherm opnamen (Alt-PrtScn) van elk tabblad om de gebruiker de huidige MFA-instellingen te geven.
- Voer vanaf de opdracht regel van de MFA-server de volgende opdracht uit om het pad te wijzigen volgens uw installatie `C:\Program Files\Multi-Factor Authentication Server\MultiFactorAuthGdpr.exe export <username>` om een JSON-bestand met indeling te maken.
- Beheerders kunnen ook de GetUserGdpr-bewerking van de webservice-SDK gebruiken als optie voor het exporteren van alle gegevens van de MFA-Cloud service die zijn verzameld voor een bepaalde gebruiker of die zijn opgenomen in een grotere rapportage oplossing.
- Zoeken `C:\Program Files\Multi-Factor Authentication Server\Logs\MultiFactorAuthSvc.log` en eventuele back-ups voor " \<username> " (de aanhalings tekens in de zoek opdracht opnemen) om alle exemplaren te vinden van de gebruikers record die wordt toegevoegd of gewijzigd.
   - Deze records kunnen worden beperkt (maar niet geëlimineerd) door de selectie van **' gebruikers wijzigingen vastleggen '** in de sectie MFA server UX, logboek registratie op het tabblad Logboek bestanden uit te schakelen.
   - Als syslog is geconfigureerd en **' wijzigingen in het logboek vastleggen van de gebruiker '** is ingeschakeld in de sectie MFA server UX, logboek registratie, tabblad syslog, kunnen de logboek vermeldingen worden verzameld van syslog.
- Andere instanties van de gebruikers naam in MultiFactorAuthSvc. log en andere logboek bestanden van de MFA-server die betrekking hebben op verificatie pogingen, worden beschouwd als operationeel en dubbel naar de informatie die wordt verstrekt met MultiFactorAuthGdpr.exe export-of Web Service SDK GetUserGdpr.

## <a name="delete-data-from-mfa-server"></a>Gegevens verwijderen van de MFA-server

Voer vanaf de opdracht regel van de MFA-server de volgende opdracht uit, waarbij u het pad wijzigt volgens uw installatie `C:\Program Files\Multi-Factor Authentication Server\MultiFactorAuthGdpr.exe delete <username>` om alle gegevens van de MFA-Cloud service die voor deze gebruiker zijn verzameld, te verwijderen.

- Gegevens die in de export zijn opgenomen, worden in realtime verwijderd, maar het kan tot 30 dagen duren voordat operationele of dubbele gegevens volledig worden verwijderd.
- Beheerders kunnen ook de DeleteUserGdpr-bewerking van de webservice-SDK gebruiken als optie voor het verwijderen van alle gegevens van de MFA-Cloud service die voor een bepaalde gebruiker zijn verzameld of die zijn opgenomen in een grotere rapportage oplossing.

## <a name="gather-data-from-nps-extension"></a>Gegevens verzamelen uit de NPS-extensie

Gebruik de [micro soft-privacy-Portal](https://portal.azure.com/#blade/Microsoft_Azure_Policy/UserPrivacyMenuBlade/Overview) om een aanvraag voor het exporteren te maken.

- MFA-informatie is opgenomen in de export, waardoor uren of dagen kunnen worden voltooid.
- Instanties van de gebruikers naam in de gebeurtenis logboeken AzureMfa/authn/AuthNOptCh, AzureMfa/AuthZ/AuthZAdminCh en AzureMfa/AuthZ/AuthZOptCh worden beschouwd als operationeel en dubbel naar de gegevens die in de export worden verstrekt.

## <a name="delete-data-from-nps-extension"></a>Gegevens verwijderen uit de NPS-extensie

Gebruik de [micro soft-privacyverklaring](https://portal.azure.com/#blade/Microsoft_Azure_Policy/UserPrivacyMenuBlade/Overview) om een aanvraag voor het sluiten van accounts te maken voor het verwijderen van alle gegevens van de MFA-Cloud service die voor deze gebruiker zijn verzameld.

- Het kan tot 30 dagen duren voordat gegevens volledig worden verwijderd.

## <a name="gather-data-from-windows-server-2016-azure-ad-mfa-ad-fs-adapter"></a>Gegevens verzamelen van Windows Server 2016 Azure AD MFA AD FS-adapter

Gebruik de [micro soft-privacy-Portal](https://portal.azure.com/#blade/Microsoft_Azure_Policy/UserPrivacyMenuBlade/Overview) om een aanvraag voor het exporteren te maken. 

- MFA-informatie is opgenomen in de export, waardoor uren of dagen kunnen worden voltooid.
- Instanties van de gebruikers naam in de gebeurtenis logboeken van AD FS tracering/fout opsporing (indien ingeschakeld) worden beschouwd als operationeel en dubbel naar de gegevens die worden verstrekt in de export.

## <a name="delete-data-from-windows-server-2016-azure-ad-mfa-ad-fs-adapter"></a>Gegevens verwijderen van Windows Server 2016 Azure AD MFA AD FS-adapter

Gebruik de [micro soft-privacyverklaring](https://portal.azure.com/#blade/Microsoft_Azure_Policy/UserPrivacyMenuBlade/Overview) om een aanvraag voor het sluiten van accounts te maken voor het verwijderen van alle gegevens van de MFA-Cloud service die voor deze gebruiker zijn verzameld.

- Het kan tot 30 dagen duren voordat gegevens volledig worden verwijderd.

## <a name="gather-data-for-azure-ad-mfa"></a>Gegevens verzamelen voor Azure AD MFA

Gebruik de [micro soft-privacy-Portal](https://portal.azure.com/#blade/Microsoft_Azure_Policy/UserPrivacyMenuBlade/Overview) om een aanvraag voor het exporteren te maken.

- MFA-informatie is opgenomen in de export, waardoor uren of dagen kunnen worden voltooid.

## <a name="delete-data-for-azure-ad-mfa"></a>Gegevens voor Azure AD MFA verwijderen

Gebruik de [micro soft-privacyverklaring](https://portal.azure.com/#blade/Microsoft_Azure_Policy/UserPrivacyMenuBlade/Overview) om een aanvraag voor het sluiten van accounts te maken voor het verwijderen van alle gegevens van de MFA-Cloud service die voor deze gebruiker zijn verzameld.

- Het kan tot 30 dagen duren voordat gegevens volledig worden verwijderd.

## <a name="next-steps"></a>Volgende stappen

[MFA-server rapportage](howto-mfa-reporting.md)
