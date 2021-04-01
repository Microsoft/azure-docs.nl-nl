---
title: Vereisten voor het Bing Zoeken-API's gebruiken en weer geven
titleSuffix: Azure Cognitive Services
description: De vereisten voor het weer geven van zoek resultaten van de Bing Zoeken-API's in uw toepassingen.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 01/31/2019
ms.author: aahi
ms.openlocfilehash: 4300860039b76bd076f19acfc252f59ef10d462b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "96351841"
---
# <a name="bing-search-api-use-and-display-requirements"></a>Vereisten voor gebruik en weergave van Bing Zoeken-API's

> [!WARNING]
> Bing Search-API's worden van Cognitive Services naar Bing Search Services verplaatst. Vanaf **30 oktober 2020** moeten nieuwe instanties van Bing Search worden ingericht overeenkomstig het proces dat [hier](/bing/search-apis/bing-web-search/create-bing-search-service-resource) is beschreven.
> Bing Search-API's die zijn ingericht met Cognitive Services, worden voor de komende drie jaar of tot het einde van uw Enterprise Agreement ondersteund, afhankelijk van wat het eerst afloopt.
> Raadpleeg [Bing Search Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource) voor migratie-instructies.

Deze gebruiks-en weergave vereisten zijn van toepassing op elke implementatie van de inhoud en de bijbehorende informatie uit de volgende Bing Zoeken-API's, inclusief relaties, meta gegevens en andere signalen.

- Bing Aangepaste zoekopdrachten
- Bing Entiteiten zoeken
- Bing Afbeeldingen zoeken
- Zoeken in Bing Nieuws
- Bing Video's zoeken
- Bing Visuele zoekopdrachten
- Bing Web Search
- Bing Spellingcontrole
- Bing Automatische suggesties

## <a name="definitions"></a>Definities


|Termijn  |Beschrijving  |
|---------|---------|
|Antwoord     | Een categorie met resultaten die in een antwoord wordt geretourneerd. Een reactie van de Bing Webzoekopdrachten-API kan bijvoorbeeld antwoorden bevatten in de categorieën resultaten van webpagina's, afbeelding, video, visueel element en nieuws. |
|Antwoord     | Alle antwoorden en bijbehorende gegevens die zijn ontvangen als reactie op één aanroep van een zoek-API. |
|Resultaat    | Een item met informatie in een antwoord. De set met gegevens die zijn gekoppeld aan één nieuws artikel is bijvoorbeeld een antwoord op een nieuws. |
|Search-API's    | gezamenlijk, de Bing Aangepaste zoekopdrachten, Entiteiten zoeken, Afbeeldingen zoeken, Nieuws zoeken, Video's zoeken, Visual Search, lokale zakelijke zoek opdracht en Webzoekopdrachten-Api's. |

## <a name="bing-spell-check-and-bing-autosuggest-api-restrictions"></a>Beperkingen voor Bing Spellingcontrole en Automatische suggestie-API voor Bing

Niet doen:

- U kunt alle gegevens die u ontvangt van de Bing Spellingcontrole-of Bing Automatische suggesties-Api's kopiëren, opslaan of in de cache plaatsen.
- Gebruik gegevens die u van Bing Spellingcontrole of Bing Automatische suggesties Api's ontvangt als onderdeel van een machine learning of vergelijk bare algoritme activiteit. Gebruik deze gegevens niet om nieuwe of bestaande services die u of derden kunnen aanbieden, te trainen, evalueren of verbeteren.

## <a name="bing-search-apis"></a>Bing Zoeken-API's

> [!NOTE]
> De vereisten in deze sectie zijn alleen van toepassing op de zoek-Api's, die geen Bing Spellingcontrole of Bing Automatische suggesties bevatten. 

### <a name="internet-search-experience-requirements"></a>Vereisten voor Internet-Zoek ervaring

Alle gegevens die in reacties worden geretourneerd, mogen alleen worden gebruikt in Internet zoek functies. Een Internet-zoek opdracht betekent dat de inhoud wordt weer gegeven: 

- Is relevant en reageert op de directe query van de eind gebruiker, of op een andere vermelding van de zoek interesse en het doel (bijvoorbeeld een door de gebruiker aangegeven zoek opdracht). 

- Helpt gebruikers bij het zoeken naar en navigeren naar de gegevens bronnen van de reactie. U kunt bijvoorbeeld klikken op koppelingen van Hyper links in het antwoord.

- Bevat meerdere resultaten waarmee de gebruiker kan selecteren. 

- Bevinden zich in een plaatsing waarmee gebruikers kunnen zoeken.

- Bevat een zicht bare indicatie dat de inhoud een Zoek resultaat van Internet is. Bijvoorbeeld een instructie die de inhoud van het web is.

- Bevat alle andere passende maat regelen om ervoor te zorgen dat uw Bing Search-API-gegevens geen enkele toepasselijke wetgeving of rechten van derden schenden. Neem contact op met uw juridische adviseur om te bepalen welke maat regelen mogelijk geschikt zijn.

De enige uitzonde ring op deze vereisten voor Internet-Zoek ervaring is voor URL-detectie, zoals verderop in dit artikel wordt beschreven. 

### <a name="restrictions"></a>Beperkingen

Niet doen:

- Gegevens uit antwoorden kopiëren, opslaan of in de cache bewaren (behalve Bewaar periode voor zover dit is toegestaan door de [continuïteit van de service](#continuity-of-service). 

- Gebruik gegevens die zijn ontvangen van de zoek-Api's als onderdeel van een machine learning of vergelijk bare algoritme activiteit. Gebruik deze gegevens niet om nieuwe of bestaande services die u of derden kunnen aanbieden, te trainen, evalueren of verbeteren.

- Wijzig de inhoud van de resultaten (met uitzonde ring van het opnieuw Format teren van de gegevens op een manier die niet in strijd is met een andere vereiste), tenzij wettelijk vereist of door micro soft zijn overeengekomen. 

- Weglaat toewijzings informatie en Url's die zijn gekoppeld aan resultaten inhoud.

- Volg orde, met inbegrip van weglating, de resultaten die in een antwoord worden weer gegeven wanneer een bestelling of rang orde wordt opgegeven, tenzij wettelijk vereist of door micro soft zijn overeengekomen. 

    > [!NOTE]
    > Deze vereiste geldt niet voor het opnieuw ordenen van de implementatie via de portal voor de Bing Custom Search-API.

- Andere inhoud weer geven binnen een deel van een antwoord op een manier die een gebruiker zou kunnen vermoeden dat de andere inhoud deel uitmaakt van het antwoord. 

- Advertenties weer geven die niet door micro soft zijn verschaft op een pagina waarop een deel van een antwoord wordt weer gegeven. 

- Advertenties weer geven op pagina's met reacties:
    - Van de Bing-installatie kopie, Nieuws zoeken, Video's zoeken of Visual Search-Api's
    - Die primair worden gefilterd of beperkt, voornamelijk (of alleen) voor afbeeldings-, nieuws-en/of visuele Zoek resultaten.

### <a name="notices-and-branding"></a>Kennisgevingen en huismerk 
Uitvoeren

- Neem prominent een functionele Hyper link op naar de [privacyverklaring van micro soft](https://go.microsoft.com/fwlink/?LinkId=521839), bijna elk punt in de gebruikers ervaring (UX) die een gebruiker de mogelijkheid biedt om een zoek opdracht in te voeren. Voorzie de Hyper Link van de **privacyverklaring van micro soft**.

- Geef een prominente Bing-huis stijl weer, in overeenstemming met de richt lijnen voor het gebruik van het [Bing-handels merk](https://go.microsoft.com/fwlink/?linkid=833278), waarbij een gebruiker de mogelijkheid biedt om een zoek opdracht in te voeren. Een dergelijke branding moet de gebruiker die micro soft in staat is om de Internet zoekmachine te controleren, een duidelijke status hebben.

- U kunt elk antwoord (of gedeelte van een antwoord) dat wordt weer gegeven in de Bing Web Search, Afbeeldingen zoeken, Nieuws zoeken, Video's zoeken en Visual Search Api's voor micro soft, tenzij micro soft u op een andere manier schrijft voor uw gebruik. Dit wordt beschreven in de [richt lijnen](https://go.microsoft.com/fwlink/?linkid=833278)voor het gebruik van de Bing-handelsmerk. 

Niet doen:

- Kenmerk Reacties (of delen van antwoorden) die worden weer gegeven van de Bing Custom Search-API naar micro soft, tenzij micro soft schrijft dat er voor uw specifieke gebruik iets anders wordt geschreven.

### <a name="transferring-responses"></a>Reacties overdragen

Als u een gebruiker in staat stelt om een reactie over te dragen van een zoek-API naar een andere gebruiker, zoals via een bericht-app of een boeking van de sociale media, is het volgende van toepassing: 

- Verzonden reacties moeten:
  - Bestaan uit inhoud die niet is gewijzigd ten opzichte van de inhoud van de antwoorden die worden weer gegeven aan de gebruiker die de overdracht doet. Opmaak wijzigingen zijn toegestaan.
  - Geen gegevens in het formulier meta gegevens bevatten.
  - Voor antwoorden van de Bing Web-, afbeeldings-, nieuws-, video-en Visual-Api's is de weergave taal die het antwoord aangeeft, verkregen via een Internet Zoek ervaring die is ingeschakeld door Bing. U kunt bijvoorbeeld een taal weer geven zoals ' aangedreven door Bing ' of ' meer informatie over deze installatie kopie op Bing ', of u kunt het Bing-logo gebruiken.
  - Voor reacties van de Bing Custom Search-API is de weergave taal die het antwoord aangeeft, verkregen via een Internet-Zoek ervaring. U kunt bijvoorbeeld een taal weer geven zoals ' meer informatie over dit Zoek resultaat '.
  - De volledige query die wordt gebruikt voor het genereren van het antwoord prominent weer geven.
  - Neem een prominente koppeling of soort gelijke toewijzing aan de onderliggende bron van het antwoord op, hetzij rechtstreeks, hetzij via de zoek machine (bing.com, m.bing.com of uw aangepaste zoek service, indien van toepassing).
- U kunt de overdracht van antwoorden niet automatiseren. Een overdracht moet duidelijk worden geïnitieerd door een actie van een gebruiker om een evidencing te kunnen overdragen.
- U kunt alleen een gebruiker in staat stellen reacties over te dragen die worden weer gegeven als reactie op de query van de overdrachts gebruiker.

### <a name="continuity-of-service"></a>Continuïteit van de service 

Geen gegevens uit de zoek-API-antwoorden kopiëren, opslaan of in de cache plaatsen. Om continuïteit van service toegang en gegevens rendering mogelijk te maken, kunt u de resultaten echter alleen onder de volgende omstandigheden behouden:

#### <a name="device"></a>Apparaat

U kunt een gebruiker in staat stellen om de resultaten te bewaren op een apparaat met een lagere (i) 24 uur vanaf het moment van de query, of (II) totdat een gebruiker een andere query voor bijgewerkte resultaten indient, mits de Inge houden resultaten alleen mogen worden gebruikt:

- De gebruiker in staat stellen om toegang te krijgen tot resultaten die eerder naar die gebruiker zijn geretourneerd op dat apparaat (bijvoorbeeld in het geval van een onderbreking van de service).
- Voor het opslaan van resultaten die zijn geretourneerd voor uw proactieve query, is het aan te raden om de behoeften van de gebruiker op basis van de signalen van die gebruiker (bijvoorbeeld in het geval van een verwachte service onderbreking).

#### <a name="server"></a>Server

U kunt de resultaten voor één gebruiker veilig bewaren op een server die u beheert en alleen de behouden resultaten weer geven:

- De gebruiker in staat stellen om toegang te krijgen tot een historisch rapport met resultaten die eerder in uw oplossing zijn geretourneerd naar die gebruiker. De resultaten mogen niet meer dan 21 dagen na het tijdstip van de eerste query van de eind gebruiker worden bewaard, en (II) worden weer gegeven als reactie op de nieuwe of herhaalde query van een gebruiker.
- Om de resultaten op te slaan die zijn geretourneerd voor uw proactieve query, op basis van de signalen van de gebruiker, die zijn gepersonaliseerd in afwachting van de behoeften van de gebruiker. U kunt deze resultaten voor minder dan (i) 24 uur na het tijdstip van de query of (II) opslaan totdat een gebruiker een andere query verzendt voor bijgewerkte resultaten.

Indien bewaard, kunnen de resultaten voor een specifieke gebruiker niet worden commingled met resultaten voor een andere gebruiker. Dat wil zeggen dat de resultaten van elke gebruiker moeten worden bewaard en afzonderlijk moeten worden geleverd.

### <a name="general"></a>Algemeen 

Voor alle presentatie van behouden resultaten:

- Neem een duidelijke, zicht bare kennisgeving op van het tijdstip waarop de query is verzonden.
- Presenteer de gebruiker met een knop of een vergelijk bare manier om bijgewerkte resultaten opnieuw op te vragen en te verkrijgen. 
- Bewaar de Bing-huis stijl in de presentatie van de resultaten.
- De opgeslagen resultaten binnen de opgegeven tijds bestek verwijderen (en zo nodig vernieuwen met een nieuwe query).

### <a name="non-display-url-discovery"></a>Detectie van niet-weer gave-URL 

U kunt Zoek reacties alleen in een niet-Internet zoek opdracht gebruiken voor het detecteren van Url's van gegevens bronnen die reageren op een query van uw gebruiker of klant. U kunt dergelijke Url's kopiëren in een rapport of een soortgelijk antwoord dat u verstrekt:

- Alleen voor die gebruiker of klant, als reactie op de query.
- Alleen als het belang rijkere extra waardevolle inhoud bevat die relevant is voor de query.

De vorige secties van de vereisten voor het gebruik en de weer gave van zoek-Api's zijn niet van toepassing op dit niet-weer gave-gebruik, met uitzonde ring van het volgende: 

- Sla geen gegevens of inhoud op in de cache, kopieer of bewaar deze van, of afgeleid van, het antwoord op de zoek opdracht dan de beperkte URL die eerder is beschreven.
- Zorg ervoor dat uw gebruik van gegevens (inclusief de Url's) die zijn ontvangen van de zoek-Api's geen inbreuk maakt op toepasselijke wetten of rechten van derden.
- Gebruik niet de gegevens (inclusief Url's) die zijn ontvangen van de zoek-Api's als onderdeel van een zoek index of machine learning of vergelijk bare algoritme activiteit. Gebruik deze gegevens niet voor het maken van Train-, evaluatie-of Verbeter services die u of derden kunnen aanbieden.

## <a name="gdpr-compliance"></a>AVG-naleving  

Met betrekking tot persoonlijke gegevens die zijn onderworpen aan de Europese Unie algemene verordening gegevensbescherming (AVG) en die worden verwerkt in verband met het aanroepen van de zoek-Api's, Bing Spellingcontrole-API of Automatische suggestie-API voor Bing, weet u dat u en micro soft onafhankelijke gegevens controllers zijn onder de AVG. U bent onafhankelijk verantwoordelijk voor uw naleving met de AVG.