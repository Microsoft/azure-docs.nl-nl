---
title: Aanmelden met behulp van verificatie met een werk-of school account-Azure AD
description: Meer informatie over hoe u zich kunt aanmelden bij uw werk-of school account met behulp van de verschillende twee ledige verificatie methoden.
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
ms.openlocfilehash: 2f27dd7daf310e425b09db7905ad2f7c37fcff81
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/18/2020
ms.locfileid: "94834163"
---
# <a name="sign-in-to-your-work-or-school-account-using-your-two-factor-verification-method"></a>Meld u aan bij uw werk-of school account met behulp van de twee ledige verificatie methode

> [!NOTE]
> Het doel van dit artikel is een typische aanmeldings ervaring te door lopen. Zie [problemen met Azure AD multi-factor Authentication](multi-factor-authentication-end-user-troubleshoot.md)voor hulp bij het aanmelden of oplossen van problemen.

## <a name="what-will-your-sign-in-experience-be"></a>Wat is uw aanmeldings ervaring?
Uw aanmeldings ervaring verschilt afhankelijk van wat u wilt gebruiken als uw tweede factor: een telefoon gesprek, een verificatie-app of tekst. Kies de optie die het beste beschrijft wat u doet:

| Hoe meld ik me aan? |
| --- |
| [Met een telefoon gesprek naar mijn mobiele of zakelijke telefoon](#signing-in-with-a-phone-call) |
| [Met een tekst naar mijn mobiele telefoon](#signing-in-with-a-text-message)
| [Met meldingen van de Microsoft Authenticator-app](#to-sign-in-with-a-notification-from-the-microsoft-authenticator-app) |
| Met verificatie codes uit de Microsoft Authenticator-app |
| [Met een alternatieve methode, omdat ik mijn voorkeurs methode nu niet kan gebruiken](#signing-in-with-an-alternate-method) |

## <a name="signing-in-with-a-phone-call"></a>Aanmelden met een telefoon gesprek
In de volgende informatie wordt de verificatie in twee stappen beschreven met een oproep naar uw mobiele telefoon of kantoor.

1. Meld u aan bij een toepassing of service, zoals Microsoft 365 met uw gebruikers naam en wacht woord.  
2. Micro soft belt u.  
3. Beantwoord de telefoon en druk op de #-toets.  

## <a name="signing-in-with-a-text-message"></a>Aanmelden met een SMS-bericht
In de volgende informatie wordt de verificatie in twee stappen beschreven met een SMS-bericht naar uw mobiele telefoon:

1. Meld u aan bij een toepassing of service, zoals Microsoft 365 met uw gebruikers naam en wacht woord.
2. Micro soft stuurt u een SMS-bericht dat een nummer code bevat.
3. Voer de code in het vak op de aanmeldings pagina in.

## <a name="signing-in-with-the-microsoft-authenticator-app"></a>Aanmelden met de app Microsoft Authenticator
De volgende informatie beschrijft de ervaring van het gebruik van de Microsoft Authenticator-app voor verificatie in twee stappen. Er zijn twee verschillende manieren om de app te gebruiken. U kunt push meldingen ontvangen op uw apparaat of u kunt de app openen om een verificatie code op te halen.

### <a name="to-sign-in-with-a-notification-from-the-microsoft-authenticator-app"></a>Aanmelden met een melding van de app Microsoft Authenticator
1. Meld u aan bij een toepassing of service, zoals Microsoft 365 met uw gebruikers naam en wacht woord.
2. Micro soft stuurt een melding naar de Microsoft Authenticator-app op uw apparaat.

   ![Micro soft verzendt een melding](./media/multi-factor-authentication-end-user-signin/notify.png)

3. Open de melding op uw telefoon en selecteer de toets **controleren** . Als uw bedrijf een pincode vereist, voert u dit hier in.
4. U bent nu aangemeld.

### <a name="to-sign-in-using-a-verification-code-with-the-microsoft-authenticator-app"></a>Aanmelden met behulp van een verificatie code met de app Microsoft Authenticator

Als u de app Microsoft Authenticator gebruikt om verificatie codes op te halen, kunt u wanneer u de app opent, een nummer weer geven onder uw account naam. Dit getal wordt elke 30 seconden gewijzigd, zodat u niet twee keer hetzelfde nummer gebruikt. Wanneer u wordt gevraagd om een verificatie code, opent u de app en gebruikt u een wille keurig nummer dat wordt weer gegeven.

1. Meld u aan bij een toepassing of service, zoals Microsoft 365 met uw gebruikers naam en wacht woord.
2. U wordt gevraagd om een verificatie code.

   ![Verificatie code invoeren](./media/multi-factor-authentication-end-user-signin/verify3.png)

3. Open de Microsoft Authenticator-app op uw telefoon en voer de code in het vak in waarin u zich aanmeldt.

## <a name="signing-in-with-an-alternate-method"></a>Aanmelden met een alternatieve methode
Soms hebt u niet de telefoon of het apparaat dat u instelt als uw voorkeurs verificatie methode. Daarom wordt u aangeraden om back-upmethoden voor uw account in te stellen. In de volgende sectie wordt beschreven hoe u zich aanmeldt met een alternatieve methode wanneer uw primaire methode niet beschikbaar is.

1. Meld u aan bij een toepassing of service, zoals Microsoft 365 met uw gebruikers naam en wacht woord.
2. Selecteer **een andere verificatie optie gebruiken**. U ziet verschillende verificatie opties op basis van het aantal instellingen dat u hebt ingesteld.
3. Kies een andere methode en meld u aan.

   ![Alternatieve methode gebruiken](./media/multi-factor-authentication-end-user-signin/alt.png)

## <a name="next-steps"></a>Volgende stappen
- Als u problemen ondervindt bij het aanmelden met verificatie in twee stappen, kunt u meer informatie krijgen over [problemen met Azure AD multi-factor Authentication](multi-factor-authentication-end-user-troubleshoot.md).

- Meer informatie over het [beheren van de verificatie-instellingen in twee stappen](multi-factor-authentication-end-user-manage-settings.md).

- Ontdek hoe u aan [de slag gaat met de app Microsoft Authenticator](user-help-auth-app-download-install.md) , zodat u meldingen kunt gebruiken om u aan te melden in plaats van teksten en telefoon gesprekken.
