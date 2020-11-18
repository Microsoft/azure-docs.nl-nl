---
title: Overzicht van Azure AD-Multi-Factor Authentication
description: Meer informatie over hoe Azure AD Multi-Factor Authentication u helpt de toegang tot gegevens en toepassingen te beschermen terwijl u aan de vraag van de gebruiker voor een eenvoudig aanmeldings proces voldoet.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/14/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9563ed283229eb6f43d036629cfe8b84fcde25fc
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/18/2020
ms.locfileid: "94839876"
---
# <a name="how-it-works-azure-ad-multi-factor-authentication"></a>Hoe werkt het? Azure AD-Multi-Factor Authentication

Meervoudige verificatie is een proces waarbij gebruikers tijdens het aanmeldproces om een aanvullende vorm van identificatie wordt gevraagd, zoals het invoeren van een code op hun mobiele telefoon of het uitvoeren van een vingerafdrukscan.

Als u alleen een wachtwoord gebruikt om gebruikers te verifiëren, is dit een onveilige aanvalsvector. Als het wachtwoord zwak is of elders is weergegeven, is het dan wel echt de gebruiker die zich met de gebruikersnaam en het wachtwoord aanmeldt, of is het een aanvaller? Wanneer u een tweede vorm van verificatie vereist, neemt de beveiliging toe omdat deze aanvullende factor niet eenvoudig door een aanvaller kan worden verkregen of gedupliceerd.

![Conceptafbeelding van de verschillende vormen van meervoudige verificatie](./media/concept-mfa-howitworks/methods.png)

Azure AD Multi-Factor Authentication werkt door twee of meer van de volgende verificatie methoden te vereisen:

* Iets dat u weet, zoals een wachtwoord.
* Iets dat u hebt, zoals een vertrouwd apparaat dat niet eenvoudig kan worden gedupliceerd, zoals een telefoon of hardwaresleutel.
* Iets dat u bent: biometrische gegevens zoals een vingerafdruk of gezichtsscan.

Gebruikers kunnen zich registreren voor selfservice voor wachtwoord herstel en Azure AD Multi-Factor Authentication in één stap om de on-board ervaring te vereenvoudigen. Beheerders kunnen definiëren welke vormen van secundaire verificatie kunnen worden gebruikt. Azure AD Multi-Factor Authentication kan ook vereist zijn wanneer gebruikers een selfservice voor wachtwoord herstel moeten uitvoeren om dat proces verder te beveiligen.

![Gebruikte verificatiemethoden op het aanmeldscherm](media/concept-authentication-methods/overview-login.png)

Met Azure AD Multi-Factor Authentication kunt u de toegang tot gegevens en toepassingen beschermen terwijl u eenvoudiger bent voor gebruikers. Het biedt extra beveiliging door een tweede vorm van verificatie te vereisen en sterke verificatie te bieden met behulp van een bereik aan gebruiks vriendelijke [verificatie methoden](concept-authentication-methods.md). Gebruikers kunnen of kunnen niet worden aangevraagd voor MFA op basis van configuratie beslissingen die een beheerder doet.

Uw toepassingen of services hoeven geen wijzigingen aan te brengen voor het gebruik van Azure AD-Multi-Factor Authentication. De verificatie prompts maken deel uit van de Azure AD-aanmeldings gebeurtenis, die de MFA-Challenge automatisch aanvraagt en verwerkt wanneer dat nodig is.

## <a name="available-verification-methods"></a>Beschik bare verificatie methoden

Wanneer een gebruiker zich aanmeldt bij een toepassing of service en een MFA-prompt ontvangt, kan hij of zij kiezen uit een van de geregistreerde formulieren van aanvullende verificatie. Een beheerder kan registratie van deze Azure AD Multi-Factor Authentication-verificatie methoden vereisen of de gebruiker heeft toegang tot hun eigen [profiel](https://myprofile.microsoft.com) om verificatie methoden te bewerken of toe te voegen.

De volgende aanvullende vormen van verificatie kunnen worden gebruikt met Azure AD Multi-Factor Authentication:

* Microsoft Authenticator-app
* OATH-hardware-token
* Sms
* Spraakoproep

## <a name="how-to-enable-and-use-azure-ad-multi-factor-authentication"></a>Azure AD Multi-Factor Authentication inschakelen en gebruiken

Gebruikers en groepen kunnen worden ingeschakeld voor Azure AD-Multi-Factor Authentication om te vragen om aanvullende verificatie tijdens de aanmeldings gebeurtenis. De [standaard instellingen voor beveiliging](../fundamentals/concept-fundamentals-security-defaults.md) zijn beschikbaar voor alle Azure AD-tenants om het gebruik van de Microsoft Authenticator-app snel in te scha kelen voor alle gebruikers.

Voor uitgebreidere besturings elementen kunnen beleids regels voor [voorwaardelijke toegang](../conditional-access/overview.md) worden gebruikt voor het definiëren van gebeurtenissen of toepassingen waarvoor MFA is vereist. Met deze beleids regels kunt u regel matige aanmeldings gebeurtenissen toestaan wanneer de gebruiker zich in het bedrijfs netwerk of een geregistreerd apparaat bevindt, maar u wordt gevraagd om aanvullende verificatie factoren wanneer het externe apparaat of op een persoon is.

![Overzichtsdiagram van de werking van voorwaardelijke toegang om het aanmeldingsproces te beveiligen](media/tutorial-enable-azure-mfa/conditional-access-overview.png)

## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie over licentie verlening de [functies en licenties voor Azure AD multi-factor Authentication](concept-mfa-licensing.md).

Als u MFA in actie wilt zien, schakelt u Azure AD-Multi-Factor Authentication in voor een set test gebruikers in de volgende zelf studie:

> [!div class="nextstepaction"]
> [Azure AD-Multi-Factor Authentication inschakelen](./tutorial-enable-azure-mfa.md)