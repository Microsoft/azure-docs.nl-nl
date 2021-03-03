---
title: Verificatie methode voor Microsoft Authenticator-app-Azure Active Directory
description: Meer informatie over het gebruik van de Microsoft Authenticator-app in Azure Active Directory om aanmeldings gebeurtenissen te verbeteren en te beveiligen
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 02/22/2021
ms.author: justinha
author: justinha
manager: daveba
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7fafeae02bce001d473b0ed916624046a559a795
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/02/2021
ms.locfileid: "101648795"
---
# <a name="authentication-methods-in-azure-active-directory---microsoft-authenticator-app"></a>Verificatie methoden in de Azure Active Directory-Microsoft Authenticator-app

De Microsoft Authenticator-app biedt een extra beveiligings niveau voor uw Azure AD-werk-of school account of uw Microsoft-account en is beschikbaar voor [Android](https://go.microsoft.com/fwlink/?linkid=866594) en [IOS](https://go.microsoft.com/fwlink/?linkid=866594). Met de Microsoft Authenticator-app kunnen gebruikers tijdens de aanmelding of als extra verificatie optie worden geverifieerd tijdens de selfservice voor het opnieuw instellen van een wacht woord (SSPR) of Azure AD-Multi-Factor Authentication gebeurtenissen.

Gebruikers kunnen een melding ontvangen via de mobiele app om ze goed te keuren of te weigeren, of de verificator-app gebruiken voor het genereren van een OATH-verificatie code die kan worden ingevoerd in een aanmeldings interface. Als u zowel een melding als een verificatie code inschakelt, kunnen gebruikers die de verificator-app registreren, een van beide methoden gebruiken om hun identiteit te verifiëren.

Als u de verificator-app bij een aanmeldings prompt wilt gebruiken in plaats van een combi natie van gebruikers naam en wacht woord, raadpleegt u [aanmelden zonder wacht woord inschakelen met de app Microsoft Authenticator](howto-authentication-passwordless-phone.md).

> [!NOTE]
> Gebruikers hebben geen optie om hun mobiele app te registreren wanneer ze SSPR inschakelen. In plaats daarvan kunnen gebruikers hun mobiele app registreren op [https://aka.ms/mfasetup](https://aka.ms/mfasetup) of als onderdeel van de registratie van gecombineerde beveiligings gegevens op [https://aka.ms/setupsecurityinfo](https://aka.ms/setupsecurityinfo) .

## <a name="passwordless-sign-in"></a>Aanmelding zonder wacht woord

In plaats van een prompt weer te geven voor een wacht woord na het invoeren van een gebruikers naam, ziet een gebruiker die aanmelding via de telefoon heeft ingeschakeld vanuit de app Microsoft Authenticator een bericht met een nummer in de app. Wanneer het juiste nummer is geselecteerd, is het aanmeldings proces voltooid.

![Voor beeld van een browser aanmelding waarin de gebruiker wordt gevraagd om de aanmelding goed te keuren](./media/howto-authentication-passwordless-phone/phone-sign-in-microsoft-authenticator-app.png)

Deze verificatie methode biedt een hoog beveiligings niveau en de nood zaak van de gebruiker om bij het aanmelden een wacht woord op te geven. 

Als u aan de slag wilt met aanmelden zonder wacht woord, raadpleegt u [aanmelden zonder wacht woord inschakelen met de app Microsoft Authenticator](howto-authentication-passwordless-phone.md).

## <a name="notification-through-mobile-app"></a>Melding via mobiele app

De verificator-app kan helpen voor komen dat onbevoegde toegang tot accounts en frauduleuze trans acties wordt afgebroken door een melding naar uw smartphone of Tablet te pushen. Gebruikers zien de melding en als deze legitiem is, selecteert u **verifiëren**. Als dat niet het geval is, kunnen ze **weigeren** selecteren.

![Scherm afbeelding van voor beeld webbrowser prompt voor verificator-app-melding voor het volt ooien van het aanmeldings proces](media/tutorial-enable-azure-mfa/azure-multi-factor-authentication-browser-prompt.png)

> [!NOTE]
> Als uw organisatie mede werkers heeft die in China werken of onderweg zijn, werkt de *melding via de mobiele app* -methode op Android-apparaten niet in dat land/deze regio als Google Play-Services (inclusief push meldingen) in de regio geblokkeerd. IOS-meldingen zijn echter wel werk. Voor Android-apparaten moeten alternatieve verificatie methoden beschikbaar worden gesteld voor deze gebruikers.

## <a name="verification-code-from-mobile-app"></a>Verificatie code uit de mobiele app

De verificator-app kan worden gebruikt als een software token voor het genereren van een OATH-verificatie code. Nadat u uw gebruikers naam en wacht woord hebt ingevoerd, voert u de code in die is opgegeven door de verificator-app in de aanmeldings interface. De verificatiecode biedt een tweede vorm van verificatie.

Gebruikers kunnen een combi natie hebben van Maxi maal vijf OATH-hardware-tokens of verificator-toepassingen, zoals de app Microsoft Authenticator, die op elk gewenst moment worden geconfigureerd voor gebruik.

> [!WARNING]
> Een verificatie code is de enige optie die beschikbaar is voor gebruikers, om ervoor te zorgen dat het hoogste beveiligings niveau voor de selfservice voor het opnieuw instellen van wacht woorden wanneer er slechts één methode vereist is voor opnieuw instellen.
>
> Wanneer twee methoden zijn vereist, kunnen gebruikers een melding of verificatie code gebruiken naast andere ingeschakelde methoden.

## <a name="next-steps"></a>Volgende stappen

Als u aan de slag wilt met aanmelden zonder wacht woord, raadpleegt u [aanmelden zonder wacht woord inschakelen met de app Microsoft Authenticator](howto-authentication-passwordless-phone.md).

Meer informatie over het configureren van verificatie methoden met behulp van de [Microsoft Graph rest API bèta](/graph/api/resources/authenticationmethods-overview?view=graph-rest-beta).
