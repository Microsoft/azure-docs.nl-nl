---
title: Intelligente traceer beveiliging (ITP) in Safari afhandelen | Azure
titleSuffix: Microsoft identity platform
description: Verificatie van een app met één pagina (SPA) wanneer cookies van derden niet meer zijn toegestaan.
services: active-directory
author: hpsin
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: how-to
ms.date: 05/19/2020
ms.author: hirsin
ms.reviewer: kkrishna
ms.custom: aaddev
ms.openlocfilehash: eed4e919684575bb2c63170d91517b661fac4acf
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98753974"
---
# <a name="handle-itp-in-safari-and-other-browsers-where-third-party-cookies-are-blocked"></a>ITP in Safari en andere browsers waar cookies van derden worden geblokkeerd, afhandelen

In veel browsers worden cookies van derden geblokkeerd op aanvragen voor domeinen die niet hetzelfde zijn als de browser balk die wordt weer gegeven in de webbrowser. Hiermee wordt de impliciete stroom onderbroken en zijn nieuwe authenticatie patronen vereist om gebruikers te kunnen aanmelden. In het micro soft-identiteits platform gebruiken we de autorisatie stroom met PKCE en tokens voor het vernieuwen om gebruikers aangemeld te laten wanneer cookies van derden worden geblokkeerd.

## <a name="what-is-intelligent-tracking-protection-itp"></a>Wat is intelligente traceer beveiliging (ITP)?

Apple Safari heeft een standaard functie voor privacy-beveiliging, die [intelligente traceer beveiliging](https://webkit.org/tracking-prevention-policy/)of *ITP* wordt genoemd. ITP blokkeert "cookies van derden" cookies op aanvragen die tussen domeinen.

Een veelvoorkomende vorm van gebruikers tracering wordt uitgevoerd door een IFRAME te laden naar een site van derden op de achtergrond en cookies te gebruiken om de gebruiker via internet te correleren. Helaas is dit patroon ook de standaard methode voor het implementeren van de [impliciete stroom](v2-oauth2-implicit-grant-flow.md) in apps met één pagina (Spas). Wanneer een browser cookies van derden blokkeert om het volgen van gebruikers te voor komen, worden SPAs ook verbroken.

Safari is niet alleen aanwezig in het blok keren van cookies van derden om de privacy van gebruikers te verbeteren. Brave heeft standaard cookies van derden geblokkeerd en chroom (het platform achter Google Chrome en micro soft Edge) heeft gewaarschuwd dat ze in de toekomst ook geen cookies van derden meer ondersteunen.

De oplossing die in dit artikel wordt beschreven, werkt in al deze browsers, of elders cookies van derden worden geblokkeerd.

## <a name="overview-of-the-solution"></a>Overzicht van de oplossing

Om gebruikers in SPAs te blijven verifiëren, moeten app-ontwikkel aars de [autorisatie code stroom](v2-oauth2-auth-code-flow.md)gebruiken. In de auth-code stroom geeft de ID-provider een code uit en de beveiligd-wachtwoord verificatie wordt de code voor een toegangs token en een vernieuwings token ingewisseld. Wanneer de app aanvullende tokens vereist, kan deze de [token stroom vernieuwen](v2-oauth2-auth-code-flow.md#refresh-the-access-token) gebruiken om nieuwe tokens op te halen. MSAL.js 2,0, de micro soft Identity platform-bibliotheek voor SPAs, implementeert de autorisatie code stroom voor SPAs en, met kleine updates, is een vervanging voor MSAL.js 1. x.

Voor de micro soft Identity platform, SPAs en native-clients volgen vergelijk bare protocol richtlijnen:

* Gebruik van een [PKCE-code vraag](https://tools.ietf.org/html/rfc7636)
    * PKCE is *vereist* voor Spas op het micro soft Identity-platform. PKCE wordt *Aanbevolen* voor systeem eigen en vertrouwelijke clients.
* Geen gebruik van een client geheim

SPAs hebben twee extra beperkingen:

* [De omleidings-URI moet worden `spa` gemarkeerd als type](v2-oauth2-auth-code-flow.md#redirect-uri-setup-required-for-single-page-apps) CORS inschakelen voor eind punten van de aanmelding.
* Vernieuwings tokens die zijn uitgegeven via de autorisatie code stroom naar `spa` omleidings-uri's, hebben een levens duur van 24 uur in plaats van een levens duur van 90 dagen.

:::image type="content" source="media/v2-oauth-auth-code-spa/active-directory-oauth-code-spa.svg" alt-text="Diagram van de OAuth 2-autorisatie code stroom tussen een app met één pagina en het eind punt van het beveiligings token service." border="false":::

## <a name="performance-and-ux-implications"></a>Prestaties en UX-implicaties

Sommige toepassingen die gebruikmaken van de impliciete stroom poging, aanmelden zonder omleiding door een aanmelding iframe te openen met `prompt=none` . In de meeste browsers reageert deze aanvraag met tokens voor de momenteel aangemelde gebruiker (ervan uitgaande dat toestemming al is verleend). In dit patroon zijn toepassingen die in het geding zijn, niet nodig om de gebruiker in te schrijven, de prestaties en gebruikers ervaring te verbeteren. de gebruiker bezoekt de webpagina en is al aangemeld. Omdat `prompt=none` in een IFRAME geen optie meer is wanneer cookies van derden worden geblokkeerd, moeten toepassingen de aanmeldings pagina in een frame op het hoogste niveau bezoeken om een autorisatie code te kunnen uitgeven.

Er zijn twee manieren om u aan te melden:

* **Volledige pagina omleidingen**
    * Op de eerste keer dat het beveiligd-wachtwoord verificatie wordt geladen, wordt de gebruiker omgeleid naar de aanmeldings pagina als er al een sessie bestaat (of als de sessie is verlopen). De browser van de gebruiker gaat naar de aanmeldings pagina, presenteert de cookies die de gebruikers sessie bevatten en stuurt vervolgens terug naar de toepassing met de code en tokens in een fragment.
    * De omleiding heeft tot gevolg dat de beveiligd-wachtwoord verificatie twee keer wordt geladen. Volg de aanbevolen procedures voor het opslaan in cache van SPAs, zodat de app niet twee maal is gedownload.
    * Overweeg het gebruik van een vooraf te laden reeks in de app die controleert op een aanmeldings sessie en omleidt naar de aanmeldings pagina voordat de app de volledige versie van de Java script-nettolading uitpakt en uitvoert.
* **Pop**
    * Als de gebruikers ervaring (UX) van een volledige pagina omleiding niet werkt voor de toepassing, kunt u een pop-upvenster gebruiken om verificatie te verwerken.
    * Wanneer de pop-up wordt omgeleid naar de toepassing na verificatie, worden in code in de handler voor omleiding de code en tokens in de lokale opslag opgeslagen zodat de toepassing deze kan gebruiken. MSAL.js ondersteunt pop-ups voor verificatie, zoals de meeste bibliotheken.
    * Browsers verlaagt de ondersteuning voor pop-ups, zodat ze mogelijk niet de meest betrouw bare optie zijn. Gebruikers interactie met het beveiligd-wachtwoord verificatie alvorens het pop-upvenster te maken, kan nodig zijn om te voldoen aan de browser vereisten.

>[!NOTE]
> Apple [beschrijft een pop-upmethode](https://webkit.org/blog/8311/intelligent-tracking-prevention-2-0/) als tijdelijke oplossing om het oorspronkelijke venster toegang te geven tot cookies van derden. Hoewel Apple deze overdracht van machtigingen in de toekomst kan verwijderen, is dit niet van invloed op de richt lijnen. Hier wordt de pop-up gebruikt als eerste navigatie naar de aanmeldings pagina, zodat een sessie wordt gevonden en er een verificatie code kan worden gegeven. Dit moet in de toekomst blijven werken.

### <a name="a-note-on-iframe-apps"></a>Een opmerking over IFRAME-apps

Een algemeen patroon in web-apps is het gebruik van een iframe om één app binnen een andere te sluiten. Het frame op het hoogste niveau zorgt voor de verificatie van de gebruiker, en de toepassing die wordt gehost in het iframe kan erop vertrouwen dat de gebruiker is aangemeld, tokens op de achtergrond ophalen met behulp van de impliciete stroom. Het op de achtergrond ophalen van tokens werkt niet meer wanneer cookies van derden worden geblokkeerd: de toepassing die is inge sloten in de iframe moet overschakelen naar pop-ups om toegang te krijgen tot de sessie van de gebruiker, omdat deze niet kan navigeren naar de aanmeldings pagina.

## <a name="security-implications-of-refresh-tokens-in-the-browser"></a>Beveiligings implicaties van vernieuwings tokens in de browser

Het verlenen van vernieuwings tokens aan de browser wordt beschouwd als een beveiligings probleem. Aanvallen met cross-site scripting (XSS) of met gemanipuleerd JS-pakketten kunnen het vernieuwings token stelen en extern gebruiken totdat het verloopt of wordt ingetrokken. Om het risico van gestolen vernieuwings tokens te minimaliseren, worden SPAs tokens uitgegeven die slechts 24 uur geldig zijn. Na 24 uur moet de app een nieuwe autorisatie code verkrijgen via een frame op het hoogste niveau Ga naar de aanmeldings pagina.

Dit vernieuwings token patroon voor beperkte levens duur is gekozen als een evenwicht tussen beveiliging en gedegradeerde UX. Zonder vernieuwings tokens of cookies van derden wordt de autorisatie code stroom (zoals aanbevolen door het concept van de [Best practices voor OAuth-beveiliging](https://tools.ietf.org/html/draft-ietf-oauth-security-topics-14)) verlieslatend als nieuwe of extra tokens zijn vereist. Voor elke token is een omleiding met volledige pagina's of pop-up vereist, telkens wanneer een token verloopt (elk uur meestal voor de tokens van micro soft Identity platform).

## <a name="next-steps"></a>Volgende stappen

Meer informatie over de [autorisatie code stroom](v2-oauth2-auth-code-flow.md).

Probeer de autorisatie code stroom uit met de [ Snelstartgids vanMSAL.js 2,0](quickstart-v2-javascript-auth-code.md).
