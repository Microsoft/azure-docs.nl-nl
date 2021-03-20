---
title: Meer informatie over de OAuth2 impliciete toekennings stroom in azure AD | Microsoft Docs
description: Meer informatie over de implementatie van de OAuth2 impliciete toekennings stroom van de Azure Active Directory, en of deze geschikt is voor uw toepassing.
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.topic: conceptual
ms.workload: identity
ms.date: 08/15/2019
ms.author: ryanwi
ms.reviewer: jmprieur
ms.custom: aaddev
ROBOTS: NOINDEX
ms.openlocfilehash: eaa3844bfbbef8cb71dbe8691cab894c921ce00a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "80154505"
---
# <a name="understanding-the-oauth2-implicit-grant-flow-in-azure-active-directory-ad"></a>Uitleg over de OAuth2 impliciete toekennings stroom in Azure Active Directory (AD)

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

De impliciete OAuth2-toekenning wordt Notorious om te worden toegekend aan de langste lijst van beveiligings problemen in de OAuth2-specificatie. En dit is nog de benadering die wordt geïmplementeerd door ADAL JS en de methode die wordt aanbevolen voor het schrijven van SPA-toepassingen. Wat biedt? Het is allemaal een soort afweging: en wanneer deze wordt uitgesteld, is de impliciete toekenning de beste benadering die u kunt gebruiken voor toepassingen die een web-API via Java script in een browser.

## <a name="what-is-the-oauth2-implicit-grant"></a>Wat is de impliciete toekenning van OAuth2?

De Quintessential [OAuth2-autorisatie code subsidie](https://tools.ietf.org/html/rfc6749#section-1.3.1) is de autorisatie toekenning die gebruikmaakt van twee afzonderlijke eind punten. Het autorisatie-eind punt wordt gebruikt voor de interactie fase van de gebruiker, wat resulteert in een autorisatie code. Het token-eind punt wordt vervolgens gebruikt door de client voor het uitwisselen van de code voor een toegangs token en vaak ook een vernieuwings token. Webtoepassingen moeten hun eigen toepassings referenties voor het token-eind punt presen teren, zodat de autorisatie server de client kan verifiëren.

De [impliciete OAuth2-subsidie](https://tools.ietf.org/html/rfc6749#section-1.3.2) is een variant van andere autorisatie subsidies. Hiermee kan een client een toegangs token (en id_token, wanneer [OpenID Connect Connect](https://openid.net/specs/openid-connect-core-1_0.html)) rechtstreeks vanuit het autorisatie-eind punt wordt gebruikt, zonder contact op te nemen met het token-eind punt of de client te verifiëren. Deze variant is ontworpen voor toepassingen op basis van Java script die worden uitgevoerd in een webbrowser: in de oorspronkelijke OAuth2-specificatie worden tokens geretourneerd in een URI-fragment. Hierdoor worden de token-bits beschikbaar voor de Java script-code in de client, maar wordt gegarandeerd dat ze niet worden opgenomen in omleidingen naar de server. In OAuth2 impliciete toekenning verleent het autorisatie-eind punt toegangs tokens rechtstreeks aan de client met behulp van een omleidings-URI die eerder is opgegeven. Het biedt ook het voor deel van het elimineren van eventuele vereisten voor cross-Origin-aanroepen, wat nodig is als de Java script-toepassing vereist is om contact op te nemen met het eind punt van de token.

Een belang rijk kenmerk van de impliciete OAuth2-subsidie is het feit dat dergelijke stromen nooit vernieuwings tokens retour neren naar de client. In de volgende sectie ziet u hoe dit niet nodig is en zou u een beveiligings probleem moeten voordoen.

## <a name="suitable-scenarios-for-the-oauth2-implicit-grant"></a>Geschikte scenario's voor de impliciete toekenning van OAuth2

De OAuth2-specificatie verklaart dat de impliciete toekenning is ontwikkeld voor het inschakelen van toepassingen voor de gebruikers agent, dat wil zeggen java script-toepassingen die worden uitgevoerd in een browser. Het definiëren van kenmerken van dergelijke toepassingen is dat Java script-code wordt gebruikt voor toegang tot Server bronnen (meestal een web-API) en om de gebruikers ervaring van de toepassing dienovereenkomstig bij te werken. Toepassingen zoals Gmail of Outlook Web Access beschouwen: wanneer u een bericht uit uw postvak in selecteert, wordt in alleen het venster visualisatie van berichten de nieuwe selectie weer gegeven, terwijl de rest van de pagina ongewijzigd blijft. Dit kenmerk is in tegens telling tot traditionele op omleiden gebaseerde web-apps, waarbij elke interactie van de gebruiker leidt tot een volledige pagina-terugposting en een volledige pagina-rendering van de nieuwe server reactie.

Toepassingen die gebruikmaken van de Java script-benadering, worden toepassingen met één pagina of SPAs genoemd. Het idee is dat deze toepassingen alleen een eerste HTML-pagina en bijbehorende java script gebruiken, waarbij alle volgende interacties worden aangedreven door Web API-aanroepen via Java script. Hybride benaderingen, waarbij de toepassing voornamelijk wordt teruggestuurd, maar af en toe wordt uitgevoerd, zijn niet ongebruikelijk: de discussie over impliciet stroom gebruik is ook relevant voor die.

Toepassingen op basis van omleiding beveiligen doorgaans hun aanvragen via cookies, maar deze benadering werkt ook niet voor Java script-toepassingen. Cookies werken alleen voor het domein waarvoor ze zijn gegenereerd, terwijl Java script-aanroepen kunnen worden omgeleid naar andere domeinen. Dit is in feite het geval: het aanroepen van Microsoft Graph-API, Office API, Azure API-alles dat zich buiten het domein bevindt waar de toepassing wordt aangeboden. Een groeiende trend voor Java script-toepassingen is om helemaal geen back-end te hebben, met 100% op Web-Api's van derden om hun zakelijke functie te implementeren.

Momenteel is de voorkeurs methode voor het beveiligen van een web-API het gebruik van de OAuth2 Bearer-token benadering, waarbij elke aanroep vergezeld gaat van een OAuth2-toegangs token. De Web-API onderzoekt het token voor inkomende toegang en, als dit de benodigde bereiken vindt, wordt toegang verleend tot de aangevraagde bewerking. De impliciete stroom biedt een handig mechanisme voor Java script-toepassingen voor het verkrijgen van toegangs tokens voor een web-API en biedt talrijke voor delen ten opzichte van cookies:

* Tokens kunnen betrouwbaar worden verkregen zonder dat dit nodig is voor cross-Origin-aanroepen: verplichte registratie van de omleidings-URI waarnaar tokens worden geretourneerd, garandeert dat de tokens niet zijn verplaatst
* Java script-toepassingen kunnen zoveel toegangs tokens verkrijgen als ze nodig hebben, voor net zoveel web-Api's waarop ze zijn gericht, zonder beperkingen voor domeinen
* HTML5-functies, zoals sessie of lokale opslag, hebben volledige controle over het opslaan van tokens en levensduur beheer, terwijl cookies beheer dekkend is voor de app
* Toegangs tokens zijn niet vatbaar voor CSRF-aanvallen (cross-site request vervalsing)

In de impliciete toekennings stroom worden geen vernieuwings tokens uitgegeven, voornamelijk om veiligheids redenen. Een vernieuwings token is niet zo smal als toegangs tokens, waardoor veel meer energie wordt toegewezen, waardoor veel meer schade wordt toegebracht bij het uitvallen van het probleem. In de impliciete stroom worden tokens geleverd in de URL, waardoor het risico van interceptie hoger is dan in de autorisatie code toekenning.

Een Java script-toepassing heeft echter een ander mechanisme voor het vernieuwen van toegangs tokens zonder dat de gebruiker herhaaldelijk om referenties wordt gevraagd. De toepassing kan een verborgen iframe gebruiken om nieuwe token aanvragen uit te voeren op het autorisatie-eind punt van Azure AD: zolang de browser nog steeds een actieve sessie heeft (gelezen: een sessie cookie heeft) op het Azure AD-domein, kan de verificatie aanvraag zonder tussen komst van de gebruiker worden uitgevoerd.

Dit model verleent de Java script-toepassing de mogelijkheid om afzonderlijke toegangs tokens te vernieuwen en kan zelfs nieuwe api's verkrijgen voor een nieuwe API (vooropgesteld dat de gebruiker deze eerder heeft gestemd). Dit voor komt dat de extra belasting van een hoogwaardige artefact, zoals een vernieuwings token, wordt verzorgd, onderhouden en beveiligd. Het artefact dat de Stille vernieuwing mogelijk maakt, wordt de Azure AD-sessie cookie beheerd buiten de toepassing. Een ander voor deel van deze benadering is dat een gebruiker zich afmeldt bij Azure AD, met behulp van een van de toepassingen die zijn aangemeld bij Azure AD en die wordt uitgevoerd op een van de browser tabbladen. Dit resulteert in het verwijderen van de Azure AD-sessie cookie en de Java script-toepassing verliest automatisch de mogelijkheid om tokens te vernieuwen voor de afgemelde gebruiker.

## <a name="is-the-implicit-grant-suitable-for-my-app"></a>Is de impliciete toekenning geschikt voor mijn app?

De impliciete toekenning presenteert meer Risico's dan andere subsidies en de gebieden die u moet best Eden aan de goed gedocumenteerde (bijvoorbeeld [misbruik van toegangs token om de resource-eigenaar in impliciete stroom][OAuth2-Spec-Implicit-Misuse] en [OAuth 2,0 Threat model en beveiligings overwegingen][OAuth2-Threat-Model-And-Security-Implications]te imiteren). Het profiel met een hoger risico is echter grotendeels te wijten aan het feit dat het de bedoeling is dat toepassingen die actieve code uitvoeren, worden geactiveerd door een externe bron in een browser. Als u een beveiligd-wachtwoord verificatie-architectuur plant, geen back-end-onderdelen hebt of een web-API wilt aanroepen via Java script, wordt het gebruik van de impliciete stroom voor het ophalen van tokens aanbevolen.

Als uw toepassing een systeem eigen client is, is de impliciete stroom niet geweldig. Het ontbreken van de Azure AD-sessie cookie in de context van een systeem eigen client zorgt dat uw toepassing een langdurige sessie kan onderhouden. Dit betekent dat uw toepassing herhaaldelijk de gebruiker vraagt wanneer er toegangs tokens voor nieuwe resources worden verkregen.

Als u een webtoepassing ontwikkelt die een back-end bevat en gebruikmaakt van een API van de bijbehorende back-upcode, is de impliciete stroom ook niet geschikt. Andere subsidies bieden u veel meer mogelijkheden. Zo biedt de OAuth2-client referenties toekenning de mogelijkheid om tokens te verkrijgen die overeenkomen met de machtigingen die zijn toegewezen aan de toepassing zelf, in tegens telling tot gebruikers delegaties. Dit betekent dat de client de mogelijkheid heeft om programmatisch toegang tot resources te behouden, zelfs wanneer een gebruiker niet actief in een sessie wordt onderneemt, enzovoort. Niet alleen die subsidies bieden hogere beveiligings garanties. Toegangs tokens worden bijvoorbeeld nooit door Voer via de browser van de gebruiker, maar worden niet opgeslagen in de geschiedenis van de browser, enzovoort. De client toepassing kan ook sterke verificatie uitvoeren bij het aanvragen van een token.

## <a name="next-steps"></a>Volgende stappen

* Bekijk [hoe u een toepassing integreert met Azure AD][ACOM-How-To-Integrate] voor meer informatie over het integratie proces van de toepassing.

<!--Image references-->

<!--Reference style links in use-->
[ACOM-How-And-Why-Apps-Added-To-AAD]: active-directory-how-applications-are-added.md
[ACOM-How-To-Integrate]: ../develop/active-directory-how-to-integrate.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json
[OAuth2-Spec-Implicit-Misuse]: https://tools.ietf.org/html/rfc6749#section-10.16
[OAuth2-Threat-Model-And-Security-Implications]: https://tools.ietf.org/html/rfc6819
