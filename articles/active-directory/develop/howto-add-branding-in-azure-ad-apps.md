---
title: Richt lijnen voor app-branding | Azure
titleSuffix: Microsoft identity platform
description: Meer informatie over de richt lijnen voor de toepassings huisstijl van micro soft Identity platform.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 08/31/2020
ms.author: ryanwi
ms.reviewer: arielgo, jiml
ms.custom: aaddev, signin_art
ms.openlocfilehash: 236e82ab97244e1428441f83295f6a5d4ed56350
ms.sourcegitcommit: 2817d7e0ab8d9354338d860de878dd6024e93c66
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/05/2021
ms.locfileid: "99581989"
---
# <a name="branding-guidelines-for-applications"></a>Huisstijlrichtlijnen voor apps

Bij het ontwikkelen van toepassingen met het micro soft-identiteits platform moet u uw klanten omleiden wanneer ze hun werk-of school account willen gebruiken (beheerd in azure AD) of hun persoonlijke account voor het aanmelden en aanmelden bij uw toepassing.

In dit artikel gaat u het volgende doen:

- Meer informatie over de twee soorten gebruikersaccounts die worden beheerd door Microsoft en hoe u in uw toepassing naar Azure AD-accounts kunt verwijzen
- Meer informatie over de vereisten voor het gebruik van het micro soft-logo in uw app
- Hoe u de officiële afbeeldingen voor **Aanmelden** of **Aanmelden bij Microsoft** downloadt voor gebruik in uw app
- De do's en don'ts van huisstijl en navigatie

## <a name="personal-accounts-vs-work-or-school-accounts-from-microsoft"></a>Persoonlijke accounts vs. werk- of schoolaccounts van Microsoft

Microsoft beheert twee soorten gebruikersaccounts:

- **Persoonlijke accounts** (voorheen bekend als Windows Live ID). Deze accounts vertegenwoordigen de relatie tussen *afzonderlijke* gebruikers en Microsoft, en worden gebruikt om toegang te verkrijgen tot consumentenapparaten en services van Microsoft. Deze accounts zijn bedoeld voor persoonlijk gebruik.
- **Werk- of schoolaccounts.** Deze accounts worden beheerd door Microsoft namens organisaties die Azure Active Directory gebruiken. Deze accounts worden gebruikt om u aan te melden bij Microsoft 365 en andere zakelijke services van micro soft.

Werk- of schoolaccounts van Microsoft worden meestal toegewezen aan eindgebruikers (werknemers, leerlingen/studenten, overheidspersoneel) door hun organisaties (bedrijf, school, overheidsinstelling). Deze accounts worden rechtstreeks in de Cloud gemastereerd (in het Azure AD-platform) of gesynchroniseerd met Azure AD vanuit een on-premises Directory, zoals Windows Server Active Directory. Microsoft is de *bewaarder* van de werk- of schoolaccounts, maar de accounts zijn eigendom van en worden beheerd door de organisatie.

## <a name="referring-to-azure-ad-accounts-in-your-application"></a>Verwijzen naar Azure AD-accounts in uw app

Microsoft stelt eindgebruikers niet bloot aan de merknaam Azure of Active Directory, en u moet dat ook niet doen.

- Zodra gebruikers zijn aangemeld, moeten ze de naam en het logo van de organisatie zoveel mogelijk zien. Dit is beter dan algemene termen zoals ‘uw organisatie’ te gebruiken.
- Wanneer gebruikers niet zijn aangemeld, moet u naar hun accounts verwijzen als ‘werk- of schoolaccounts’ en het Microsoft-logo gebruiken om te laten zien dat Microsoft deze accounts beheert. Gebruik geen termen zoals ‘ondernemingsaccount’, ‘bedrijfsaccount’ of ‘zakelijk account’, want die kunnen voor verwarring zorgen bij de gebruiker.

## <a name="user-account-pictogram"></a>Pictogram van het gebruikersaccount

In een eerdere versie van deze richtlijnen raadden we aan een pictogram van een ‘blauwe badge’ te gebruiken. Naar aanleiding van feedback van gebruikers en ontwikkelaars raden we nu echter aan het Microsoft-logo te gebruiken. Het micro soft-logo helpt gebruikers te begrijpen dat ze het account dat ze gebruiken met Microsoft 365 of andere micro soft Business Services, opnieuw kunnen gebruiken om zich aan te melden bij uw app.

## <a name="signing-up-and-signing-in-with-azure-ad"></a>Registreren en aanmelden met Azure AD

Uw app heeft misschien aparte paden voor registratie en aanmelding, en de volgende secties bieden visuele begeleiding voor beide scenario’s.

**Als uw app eindgebruikersregistratie ondersteunt (bijvoorbeeld voor een gratis proefversie of freemium-model)**: U kunt een knop **Aanmelden** tonen waarmee gebruikers toegang tot uw app kunnen verkrijgen met hun werkaccount of persoonlijke account. De eerste keer dat ze toegang tot uw app verkrijgen, toont Azure AD een instemmingsprompt.

**Als uw app toestemmingen nodig heeft die alleen beheerders kunnen verlenen, of als uw app organisatielicenties nodig heeft**: Scheid beheerdersdownload van gebruikersaanmelding. Met de knop **Download deze app** worden beheerders naar de aanmeldingspagina geleid, waarna ze worden gevraagd toestemming te verlenen namens gebruikers in hun organisatie. Dit heeft het extra voordeel dat instemmingsprompts voor eindgebruikers naar uw app worden onderdrukt.

## <a name="visual-guidance-for-app-acquisition"></a>Visuele begeleiding voor app-download

Uw koppeling ‘Download de app’ moet de gebruiker naar de Azure AD-pagina voor toegangsverlening (autorisatie) leiden, zodat de beheerder van een organisatie kan autoriseren dat uw app toegang heeft tot de gegevens van die organisatie, die worden gehost door Microsoft. In het artikel [Toepassingen integreren met Azure Active Directory](./quickstart-register-app.md) staat meer informatie over hoe u toegang kunt aanvragen.

Nadat beheerders toestemming hebben gegeven voor uw app, kunnen ze deze toevoegen aan hun Microsoft 365 app-start programma van de gebruiker (toegankelijk via de wafel en vanaf [https://portal.office.com/myapps](https://portal.office.com/myapps) ). Als u deze mogelijkheid wilt adverteren, kunt u termen zoals ‘Voeg deze app aan uw organisatie toe’ gebruiken en een knop tonen zoals in het volgende voorbeeld:

![Knop met het logo van micro soft en de tekst toevoegen aan mijn organisatie](./media/howto-add-branding-in-azure-ad-apps/add-to-my-org.png)

We raden u echter aan verklarende tekst te schrijven in plaats van op knoppen te vertrouwen. Bijvoorbeeld:

> *Als u Microsoft 365 of andere zakelijke services van micro soft al gebruikt, kunt u <your_app_name> toegang verlenen tot de gegevens van uw organisatie. Hiermee kunnen uw gebruikers toegang krijgen tot <your_app_name> met hun bestaande werk accounts.*

Als u het officiële Microsoft-logo wilt downloaden voor gebruik in uw app, klikt u met de rechtermuisknop op het logo dat u wilt gebruiken en slaat u het op naar uw computer.

| Asset                                | PNG-indeling | SVG-indeling |
| ------------------------------------ | ---------- | ---------- |
| Microsoft-logo  | ![Downloadbaar micro soft-logo in PNG-indeling](./media/howto-add-branding-in-azure-ad-apps/ms-symbollockup_mssymbol_19.png) | ![Downloadbaar micro soft-logo in SVG-indeling](./media/howto-add-branding-in-azure-ad-apps/ms-symbollockup_mssymbol_19.svg) |

## <a name="visual-guidance-for-sign-in"></a>Visuele begeleiding voor aanmelding

Uw app moet een aanmeldingsknop tonen waarmee gebruikers naar het aanmeldingseindpunt worden geleid dat overeenkomt met het protocol dat u gebruikt om te integreren met Azure AD. De volgende sectie biedt meer informatie over hoe de knop eruit moet zien.

### <a name="pictogram-and-sign-in-with-microsoft"></a>Pictogram en ‘Aanmelden met Microsoft’

Het is de associatie van het Microsoft-logo en de term ‘Aanmelden met Microsoft’ die Azure AD onderscheidt van andere identiteitsproviders die uw app mogelijk ondersteunt. Als u niet genoeg ruimte hebt voor ‘Aanmelden met Microsoft’, kunt u dit afkorten tot ‘Aanmelden’. Voor de knoppen kunt u een licht of donker kleurenschema gebruiken.

Het volgende diagram toont de door Microsoft aanbevolen rode lijnen wanneer u de assets met uw app gebruikt. De rode lijnen zijn van toepassing op ‘Aanmelden met Microsoft’ en de kortere versie ‘Aanmelden’.

![Hiermee worden de Aanmelden met Microsoft redlines](./media/howto-add-branding-in-azure-ad-apps/sign-in-with-microsoft-redlines.png)

Als u de officiële afbeelding wilt downloaden voor gebruik in uw app, klikt u met de rechtermuisknop op de afbeelding die u wilt gebruiken en slaat u deze op naar uw computer.

| Asset                                | PNG-indeling | SVG-indeling |
| ------------------------------------ | ---------- | ---------- |
| Aanmelden met Microsoft (donker thema)  | ![Download bare knop Aanmelden met Microsoft, donker thema PNG](./media/howto-add-branding-in-azure-ad-apps/ms-symbollockup_signin_dark.png) | ![Download bare knop Aanmelden met Microsoft, donker thema SVG](./media/howto-add-branding-in-azure-ad-apps/ms-symbollockup_signin_dark.svg) |
| Aanmelden met Microsoft (licht thema) | ![Down loadable Aanmelden met Microsoft-knop licht thema PNG](./media/howto-add-branding-in-azure-ad-apps/ms-symbollockup_signin_light.png) | ![Down loadable Aanmelden met Microsoft-knop Light thema SVG](./media/howto-add-branding-in-azure-ad-apps/ms-symbollockup_signin_light.svg) |
| Aanmelden (donker thema)                 | ![Down loadable ' Sign in ' Short button donker Theme PNG](./media/howto-add-branding-in-azure-ad-apps/ms-symbollockup_signin_dark_short.png) | ![Down loadable ' Aanmelden ' korte knop voor het donkere thema SVG](./media/howto-add-branding-in-azure-ad-apps/ms-symbollockup_signin_dark_short.svg) |
| Aanmelden (licht thema)                | ![Down loadable "aanmelden" korte knop licht thema PNG](./media/howto-add-branding-in-azure-ad-apps/ms-symbollockup_signin_light_short.png) | ![Down loadable ' Aanmelden ' SVG-laag thema voor korte knoppen](./media/howto-add-branding-in-azure-ad-apps/ms-symbollockup_signin_light_short.svg) |

## <a name="branding-dos-and-donts"></a>Huisstijl: wel en niet doen

**WEL DOEN** ‘Werk- of schoolaccount’ gebruiken in combinatie met de knop ‘Aanmelden met Microsoft’ om eindgebruikers te helpen zien of ze het kunnen gebruiken. **NIET DOEN** Andere termen zoals ‘ondernemingsaccount’, ‘bedrijfsaccount’ of ‘zakelijk account’ gebruiken.

Gebruik **geen** ' Microsoft 365 id ' of ' Azure id '. Microsoft 365 is ook de naam van een consument die door micro soft wordt aangeboden en die geen gebruik maakt van Azure AD voor verificatie.

**NIET DOEN** Het Microsoft-logo wijzigen.

**NIET DOEN** Eindgebruikers blootstellen aan het merk Azure of Active Directory. U kunt deze termen echter wel gebruiken bij ontwikkelaars, IT-professionals en beheerders.

## <a name="navigation-dos-and-donts"></a>Navigatie: wel en niet doen

**WEL DOEN** Gebruikers een manier bieden om zich af te melden en naar een ander gebruikersaccount te schakelen. Hoewel de meeste mensen één persoonlijk account van Microsoft/Facebook/Google/Twitter hebben, zijn mensen vaak gekoppeld aan meer dan één organisatie. Ondersteuning voor meerdere aangemelde gebruikers zal binnenkort beschikbaar zijn.
