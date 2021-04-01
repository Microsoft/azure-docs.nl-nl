---
title: Problemen met Internet Explorer & micro soft Edge (MSAL.js) | Azure
titleSuffix: Microsoft identity platform
description: Meer informatie over problemen met het gebruik van de micro soft-verificatie bibliotheek voor Java script (MSAL.js) met Internet Explorer en micro soft Edge-browsers.
services: active-directory
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: troubleshooting
ms.workload: identity
ms.date: 05/18/2020
ms.author: nacanuma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 2a471504b88791b5bfb6ce6cc7c81d60bfbe5028
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "83772077"
---
# <a name="known-issues-on-internet-explorer-and-microsoft-edge-browsers-msaljs"></a>Bekende problemen met Internet Explorer en micro soft Edge-browsers (MSAL.js)

## <a name="issues-due-to-security-zones"></a>Problemen als gevolg van beveiligings zones
Er zijn meerdere meldingen over problemen met verificatie in IE en micro soft Edge (sinds de update van de *micro soft Edge-browser versie naar 40.15063.0.0*). We volgen deze en hebben het micro soft Edge-team op de hoogte gebracht. Micro soft Edge werkt met een oplossing. Hier volgt een beschrijving van de vaak voorkomende problemen en de mogelijke oplossingen die kunnen worden geïmplementeerd.

### <a name="cause"></a>Oorzaak
De oorzaak van de meeste van deze problemen is als volgt. De sessie opslag en lokale opslag worden gepartitioneerd door beveiligings zones in de micro soft Edge-browser. In deze specifieke versie van micro soft Edge worden de sessie opslag en lokale opslag gewist wanneer de toepassing wordt omgeleid tussen zones. In het bijzonder wordt de sessie opslag gewist in de normale browser navigatie en worden zowel de sessie als de lokale opslag gewist in de InPrivate-modus van de browser. MSAL.js slaat een bepaalde status op in de sessie opslag en is afhankelijk van het controleren van deze status tijdens de verificatie stromen. Wanneer de sessie-opslag is gewist, gaat deze status verloren en resulteert dit dus in een verbroken ervaring.

### <a name="issues"></a>Problemen

- **Oneindige omleidings lussen en pagina-laadt tijdens verificatie**. Wanneer gebruikers zich aanmelden bij de toepassing op micro soft Edge, worden ze teruggeleid van de AAD-aanmeldings pagina en zijn ze vastgelopen in een oneindige omleidings lus, wat resulteert in herhaalde pagina opnieuw laden. Dit wordt meestal vergezeld van een `invalid_state` fout in de sessie opslag.

- **Oneindig tokens voor ophalen en AADSTS50058-fout**. Wanneer een toepassing die wordt uitgevoerd op micro soft Edge een token voor een resource probeert op te halen, kan de toepassing vastlopen in een oneindige lus van de aanvraag voor het ophalen van tokens, samen met de volgende fout van AAD in uw netwerk tracering:

    `Error :login_required; Error description:AADSTS50058: A silent sign-in request was sent but no user is signed in. The cookies used to represent the user's session were not sent in the request to Azure AD. This can happen if the user is using Internet Explorer or Edge, and the web app sending the silent sign-in request is in different IE security zone than the Azure AD endpoint (login.microsoftonline.com)`

- **Pop-upvensters worden niet gesloten of blijven hangen wanneer de aanmelding via de pop-up wordt geverifieerd**. Wanneer de verificatie via pop-upvenster in micro soft Edge of IE (InPrivate) wordt uitgevoerd en u zich hebt aangemeld en u zich aanmeldt, wordt het pop-upvenster niet gesloten, omdat MSAL.js de ingang naar het pop-upvenster verliest.  

### <a name="update-fix-available-in-msaljs-023"></a>Update: Fix beschikbaar in MSAL.js 0.2.3
Oplossingen voor de problemen met de omleiding van verificatie lussen zijn vrijgegeven in [MSAL.js 0.2.3](https://github.com/AzureAD/microsoft-authentication-library-for-js/releases). Schakel de vlag `storeAuthStateInCookie` in de configuratie van MSAL.js in om te profiteren van deze oplossing. Deze vlag is standaard ingesteld op ONWAAR.

Wanneer de `storeAuthStateInCookie` markering is ingeschakeld, gebruikt MSAL.js de browser cookies om de status van de aanvraag op te slaan die vereist is voor de validatie van de verificatie stromen.

> [!NOTE]
> Deze oplossing is nog niet beschikbaar voor de msal-en msal-angularjs-wrappers. Deze oplossing biedt geen oplossing voor het probleem met pop-upvensters.

Gebruik onderstaande tijdelijke oplossingen.

#### <a name="other-workarounds"></a>Andere tijdelijke oplossingen
Zorg ervoor dat u test dat uw probleem alleen optreedt op de specifieke versie van de micro soft Edge-browser en werkt in de andere browsers voordat u deze tijdelijke oplossingen doorvoert.  
1. Als eerste stap om deze problemen op te lossen, moet u ervoor zorgen dat het toepassings domein en alle andere sites die betrokken zijn bij de omleidingen van de verificatie stroom worden toegevoegd als vertrouwde sites in de beveiligings instellingen van de browser, zodat deze deel uitmaken van dezelfde beveiligings zone.
Voer hiervoor de volgende stappen uit:
    - Open **Internet Explorer** en klik in de rechter bovenhoek op het pictogram **instellingen** (tand wiel)
    - Selecteer **Internet opties**
    - Selecteer het tabblad **beveiliging**
    - Klik onder de optie **vertrouwde sites** op de knop **sites** en voeg de url's toe in het dialoog venster dat wordt geopend.

2. Zoals eerder vermeld, kunt u in plaats daarvan de MSAL.js configureren voor het gebruik van de lokale opslag, omdat tijdens de normale navigatie alleen de sessie opslag is gewist. Dit kan worden ingesteld als de `cacheLocation` configuratie parameter tijdens het initialiseren van MSAL.

Opmerking: Hiermee wordt het probleem voor InPrivate-Browsing niet opgelost omdat zowel de sessie als de lokale opslag is gewist.

## <a name="issues-due-to-popup-blockers"></a>Problemen als gevolg van pop-upblokkeringen

Er zijn gevallen waarin pop-ups worden geblokkeerd in IE of micro soft Edge, bijvoorbeeld wanneer een tweede pop-up wordt weer gegeven tijdens [multi-factor Authentication](../authentication/concept-mfa-howitworks.md). U ontvangt een waarschuwing in de browser om de pop-up één keer of altijd toe te staan. Als u ervoor kiest om toe te staan, wordt het pop-upvenster automatisch geopend en wordt er een ingang voor de browser geretourneerd `null` . Als gevolg hiervan heeft de bibliotheek geen ingang voor het venster en is er geen manier om het pop-upvenster te sluiten. Hetzelfde probleem treedt niet op in Chrome wanneer u wordt gevraagd popups toe te staan omdat er niet automatisch een pop-upvenster wordt geopend.

Als **tijdelijke oplossing** moeten ontwikkel aars pop-ups in Internet Explorer en micro soft Edge toestaan voordat ze hun app gaan gebruiken om dit probleem te voor komen.

## <a name="next-steps"></a>Volgende stappen
Meer informatie over het [gebruik van MSAL.js in Internet Explorer](msal-js-use-ie-browser.md).
