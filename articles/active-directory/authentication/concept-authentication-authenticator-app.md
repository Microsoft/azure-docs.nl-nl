---
title: Microsoft Authenticator app-verificatiemethode - Azure Active Directory
description: Meer informatie over het gebruik Microsoft Authenticator-app in Azure Active Directory om aanmeldingsgebeurtenissen te verbeteren en beveiligen
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 02/22/2021
ms.author: justinha
author: justinha
manager: daveba
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3175b1292a7e69506b9193d1182e184e257ebda3
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107530505"
---
# <a name="authentication-methods-in-azure-active-directory---microsoft-authenticator-app"></a>Verificatiemethoden in Azure Active Directory - Microsoft Authenticator app

De Microsoft Authenticator-app biedt een extra beveiligingsniveau voor uw Azure AD-werk- of schoolaccount of uw Microsoft-account en is beschikbaar voor [Android](https://go.microsoft.com/fwlink/?linkid=866594) en [iOS.](https://go.microsoft.com/fwlink/?linkid=866594) Met de Microsoft Authenticator-app kunnen gebruikers zich op een wachtwoordloze manier verifiëren tijdens het aanmelden of als een extra verificatieoptie tijdens selfservice voor wachtwoord opnieuw instellen (SSPR) of Azure AD Multi-Factor Authentication-gebeurtenissen.

Gebruikers kunnen via de mobiele app een melding ontvangen om goed te keuren of te weigeren, of de Authenticator-app gebruiken om een OAUTH-verificatiecode te genereren die kan worden ingevoerd in een aanmeldingsinterface. Als u zowel een meldings- als verificatiecode inschakelen, kunnen gebruikers die de Authenticator-app registreren, een van beide methoden gebruiken om hun identiteit te verifiëren.

Als u de Authenticator-app wilt gebruiken bij een aanmeldingsprompt in plaats van een combinatie van gebruikersnaam en wachtwoord, gaat u naar Aanmelden zonder wachtwoord inschakelen met de [Microsoft Authenticator app.](howto-authentication-passwordless-phone.md)

> [!NOTE]
> Gebruikers kunnen hun mobiele app niet registreren wanneer ze SSPR inschakelen. In plaats daarvan kunnen gebruikers hun mobiele app registreren op of als onderdeel van de gecombineerde registratie van [https://aka.ms/mfasetup](https://aka.ms/mfasetup) beveiligingsgegevens op [https://aka.ms/setupsecurityinfo](https://aka.ms/setupsecurityinfo) .

## <a name="passwordless-sign-in"></a>Aanmelden zonder wachtwoord

In plaats van een prompt voor een wachtwoord te zien na het invoeren van een gebruikersnaam, ziet een gebruiker die zich via de telefoon heeft aanmelden vanuit de Microsoft Authenticator-app een bericht om op een nummer in de app te tikken. Wanneer het juiste nummer is geselecteerd, is het aanmeldingsproces voltooid.

![Voorbeeld van een aanmelding in een browser waarin de gebruiker wordt gevraagd om de aanmelding goed te keuren](./media/howto-authentication-passwordless-phone/phone-sign-in-microsoft-authenticator-app.png)

Deze verificatiemethode biedt een hoog beveiligingsniveau en zorgt dat de gebruiker geen wachtwoord hoeft op te geven bij het aanmelden. 

Zie Aanmelden zonder wachtwoord inschakelen met de app Microsoft Authenticator om aan de slag [te gaan met aanmelden zonder wachtwoord.](howto-authentication-passwordless-phone.md)

## <a name="notification-through-mobile-app"></a>Melding via mobiele app

De Authenticator-app kan helpen onbevoegde toegang tot accounts te voorkomen en frauduleuze transacties te stoppen door een melding naar uw smartphone of tablet te pushen. Gebruikers bekijken de melding en als deze legitiem zijn, selecteert u **Verifiëren.** Anders kunnen ze **Weigeren selecteren.**

![Schermopname van een voorbeeld van een webbrowserprompt voor de authenticator-app-melding om het aanmeldingsproces te voltooien](media/tutorial-enable-azure-mfa/azure-multi-factor-authentication-browser-prompt.png)

> [!NOTE]
> Als uw organisatie medewerkers heeft die in China werken of naar China reizen, werkt de melding via de methode voor mobiele *apps* op Android-apparaten niet in dat land of die regio, omdat Google Play Services (inclusief pushmeldingen) worden geblokkeerd in de regio. iOS-meldingen werken echter wel. Voor Android-apparaten moeten alternatieve verificatiemethoden beschikbaar worden gesteld voor deze gebruikers.

## <a name="verification-code-from-mobile-app"></a>Verificatiecode van mobiele app

De Authenticator-app kan worden gebruikt als een software-token om een OATH-verificatiecode te genereren. Nadat u uw gebruikersnaam en wachtwoord hebt opgegeven, voert u de code van de Authenticator-app in de aanmeldingsinterface in. De verificatiecode biedt een tweede vorm van verificatie.

Gebruikers kunnen een combinatie van maximaal vijf OATH-hardwaretokens of authenticatortoepassingen hebben, zoals de Microsoft Authenticator-app, die op elk moment is geconfigureerd voor gebruik.

> [!WARNING]
> Om het hoogste beveiligingsniveau voor selfservice voor wachtwoord opnieuw instellen te garanderen wanneer er slechts één methode is vereist voor het opnieuw instellen van wachtwoorden, is een verificatiecode de enige optie die beschikbaar is voor gebruikers.
>
> Wanneer twee methoden vereist zijn, kunnen gebruikers opnieuw instellen met behulp van een meldings- of verificatiecode naast andere ingeschakelde methoden.

## <a name="next-steps"></a>Volgende stappen

Zie Aanmelden zonder wachtwoord inschakelen met de app Microsoft Authenticator om aan de slag [te gaan met aanmelden zonder wachtwoord.](howto-authentication-passwordless-phone.md)

Meer informatie over het configureren van verificatiemethoden met behulp [van Microsoft Graph REST API](/graph/api/resources/authenticationmethods-overview).
