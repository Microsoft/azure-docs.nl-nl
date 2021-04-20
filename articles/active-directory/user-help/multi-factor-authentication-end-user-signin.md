---
title: Aanmelden met verificatie met een werk- of schoolaccount - Azure AD
description: Leer hoe u zich kunt aanmelden bij uw werk- of schoolaccount met behulp van de verschillende methoden voor verificatie in twee stappen.
services: active-directory
author: curtand
manager: daveba
ms.assetid: b310b762-471b-4b26-887a-a321c9e81d46
ms.workload: identity
ms.service: active-directory
ms.subservice: user-help
ms.topic: end-user-help
ms.date: 04/02/2017
ms.author: curtand
ms.reviewer: librown
ms.custom: end-user, seo-update-azuread-jan
ms.openlocfilehash: b373288eb5cd47f99bdbb25f961e1330a708d7d1
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107739184"
---
# <a name="sign-in-to-your-work-or-school-account-using-your-two-factor-verification-method"></a>Meld u aan bij uw werk- of schoolaccount met behulp van uw verificatiemethode in twee stappen

> [!NOTE]
> Het doel van dit artikel is het doorloop van een typische aanmeldingservaring. Zie Problemen met [Azure AD Multi-Factor Authentication](multi-factor-authentication-end-user-troubleshoot.md)voor hulp bij het aanmelden of voor het oplossen van problemen.

## <a name="what-will-your-sign-in-experience-be"></a>Wat wordt uw aanmeldingservaring?
Uw aanmeldingservaring is afhankelijk van wat u als tweede factor wilt gebruiken: een telefoongesprek, een verificatie-app of sms-berichten. Kies de optie die het beste beschrijft wat u doet:

| Hoe meld u zich aan? |
| --- |
| [Met een telefoongesprek naar mijn mobiele telefoon of kantoortelefoon](#signing-in-with-a-phone-call) |
| [Met een sms naar mijn mobiele telefoon](#signing-in-with-a-text-message)
| [Met meldingen van de Microsoft Authenticator app](#to-sign-in-with-a-notification-from-the-microsoft-authenticator-app) |
| Met verificatiecodes van de Microsoft Authenticator app |
| [Met een alternatieve methode, omdat ik op dit moment mijn voorkeursmethode niet kan gebruiken](#signing-in-with-an-alternate-method) |

## <a name="signing-in-with-a-phone-call"></a>Aanmelden met een telefoongesprek
De volgende informatie beschrijft de verificatie-ervaring in twee stappen met een oproep naar uw mobiele telefoon of kantoortelefoon.

1. Meld u aan bij een toepassing of service zoals Microsoft 365 gebruikersnaam en wachtwoord.  
2. Microsoft roept u aan.  
3. Beantwoord de telefoon en druk op de #-toets.  

## <a name="signing-in-with-a-text-message"></a>Aanmelden met een sms-bericht
De volgende informatie beschrijft de verificatie in twee stappen met een sms-bericht op uw mobiele telefoon:

1. Meld u aan bij een toepassing of service zoals Microsoft 365 met uw gebruikersnaam en wachtwoord.
2. Microsoft stuurt u een sms-bericht met een nummercode.
3. Voer de code in het vak op de aanmeldingspagina in.

## <a name="signing-in-with-the-microsoft-authenticator-app"></a>Aanmelden met de Microsoft Authenticator app
De volgende informatie beschrijft de ervaring van het gebruik van Microsoft Authenticator app voor verificaties in twee stappen. Er zijn twee verschillende manieren om de app te gebruiken. U kunt pushmeldingen ontvangen op uw apparaat of u kunt de app openen om een verificatiecode op te halen.

### <a name="to-sign-in-with-a-notification-from-the-microsoft-authenticator-app"></a>Aanmelden met een melding van de Microsoft Authenticator app
1. Meld u aan bij een toepassing of service zoals Microsoft 365 met uw gebruikersnaam en wachtwoord.
2. Microsoft verzendt een melding naar de Microsoft Authenticator app op uw apparaat.

   ![Microsoft verzendt een melding](./media/multi-factor-authentication-end-user-signin/notify.png)

3. Open de melding op uw telefoon en selecteer de **toets** Verifiëren. Als uw bedrijf een pincode vereist, voert u deze hier in.
4. U bent nu aangemeld.

### <a name="to-sign-in-using-a-verification-code-with-the-microsoft-authenticator-app"></a>Aanmelden met behulp van een verificatiecode met de Microsoft Authenticator app

Als u de app Microsoft Authenticator om verificatiecodes op te halen, ziet u bij het openen van de app een nummer onder uw accountnaam. Dit getal wordt elke 30 seconden gewijzigd, zodat u hetzelfde getal niet twee keer gebruikt. Wanneer u om een verificatiecode wordt gevraagd, opent u de app en gebruikt u het nummer dat momenteel wordt weergegeven.

1. Meld u aan bij een toepassing of service zoals Microsoft 365 met uw gebruikersnaam en wachtwoord.
2. Microsoft vraagt u om een verificatiecode.

   ![Verificatiecode invoeren](./media/multi-factor-authentication-end-user-signin/verify3.png)

3. Open de Microsoft Authenticator-app op uw telefoon en voer de code in het vak waarin u zich aanmeldt.

## <a name="signing-in-with-an-alternate-method"></a>Aanmelden met een alternatieve methode
Soms hebt u niet de telefoon of het apparaat dat u hebt ingesteld als verificatiemethode van uw voorkeur. Daarom raden we u aan om back-upmethoden in te stellen voor uw account. In de volgende sectie ziet u hoe u zich kunt aanmelden met een alternatieve methode wanneer uw primaire methode mogelijk niet beschikbaar is.

1. Meld u aan bij een toepassing of service zoals Microsoft 365 gebruikersnaam en wachtwoord.
2. Selecteer **Een andere verificatieoptie gebruiken.** U ziet verschillende verificatieopties op basis van het aantal instellingen dat u hebt ingesteld.
3. Kies een alternatieve methode en meld u aan.

   ![Alternatieve methode gebruiken](./media/multi-factor-authentication-end-user-signin/alt.png)

## <a name="next-steps"></a>Volgende stappen
- Als u problemen hebt met aanmelden met verificatie in twee stappen, kunt u meer informatie vinden in [Problemen met Azure AD Multi-Factor Authentication.](multi-factor-authentication-end-user-troubleshoot.md)

- Meer informatie over het [beheren van uw verificatie-instellingen in twee stappen.](multi-factor-authentication-end-user-manage-settings.md)

- Ontdek hoe u aan de slag gaat [met de Microsoft Authenticator app,](user-help-auth-app-download-install.md) zodat u meldingen kunt gebruiken om u aan te melden in plaats van sms-berichten en telefoongesprekken.
