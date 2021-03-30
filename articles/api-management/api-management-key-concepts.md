---
title: Overzicht en belangrijkste concepten van API Management| Microsoft Docs
description: Meer informatie over API's, producten, rollen, groepen en andere belangrijke concepten van API Management.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: overview
ms.date: 11/15/2017
ms.author: apimpm
ms.custom: mvc
ms.openlocfilehash: 85fa79cdfc7036be5b0ab20e49986a1d075152c5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "86254653"
---
# <a name="about-api-management"></a>Meer informatie over API Management

API Management (APIM) is een manier om consistente en moderne API-gateways voor bestaande back-endservices te maken.

API Management helpt organisaties bij het publiceren van API's naar externe, partner- en interne ontwikkelaars om het potentieel van hun gegevens en services te ontsluiten. Veel bedrijven willen hun activiteiten uitbreiden als een digitaal platform, waarbij nieuwe kanalen worden gemaakt, nieuwe klanten worden gevonden en grotere betrokkenheid met bestaande klanten wordt bereikt. API Management beschikt over de competenties die belangrijk zijn voor een geslaagd API-programma via ontwikkelaarsbetrokkenheid, zakelijke inzichten, analytische gegevens, beveiliging en bescherming. U kunt Azure API Management gebruiken om elke back-end te nemen en een volwaardig API-programma te starten dat hierop is gebaseerd.

Dit artikel bevat een overzicht van veelvoorkomende scenario's met APIM.  Het bevat ook een kort overzicht van de belangrijkste onderdelen van het APIM-systeem. Het artikel biedt vervolgens een meer gedetailleerd overzicht van elk onderdeel.

## <a name="overview"></a>Overzicht

Beheerders maken API's om API Management te gebruiken. Elke API bestaat uit een of meer bewerkingen en elke API kan aan een of meer producten worden toegevoegd. Als ontwikkelaars een API willen gebruiken, abonneren ze zich op een product dat die API bevat en vervolgens kunnen ze de bewerking van de API aanroepen. Deze is onderworpen aan gebruiksbeleidsregels die mogelijk van kracht zijn. Algemene scenario's omvatten de volgende:

* **Mobiele infrastructuur beveiligen** door het beperken van toegang met API-sleutels, het voorkomen van DOS-aanvallen door middel van beperking, of het gebruiken van geavanceerd beveiligingsbeleid zoals validatie van JWT-tokens.
* **Ecosystemen van ISV-partners beschikbaar stellen** door snelle onboarding van partners via de portal voor ontwikkelaars en het bouwen van een API-facade voor het loskoppelen van interne implementaties die niet geschikt zijn voor gebruik door partners.
* **Een intern API-programma uitvoeren** door het aanbieden van een centrale locatie voor de organisatie om te communiceren over de beschikbaarheid en de meest recente wijzigingen in API's, toegang te beperken op basis van organisatieaccounts. Dit alles is op basis van een veilig kanaal tussen de API-gateway en de back-end.

Het systeem bestaat uit de volgende onderdelen:

* De **API-gateway** is het eindpunt waarmee:
  
  * API-aanroepen worden geaccepteerd en naar uw back-ends worden doorgestuurd.
  * API-sleutels, JWT-tokens, certificaten en andere referenties worden geverifieerd.
  * Gebruiksquota en snelheidsbeperkingen worden gehandhaafd.
  * Uw API wordt ter plekke omgezet zonder codewijzigingen.
  * Back-endreacties worden in de cache opgeslagen waar ze zijn ingesteld.
  * Aanroepmetagegevens worden voor analysedoeleinden aan het logboek toegevoegd.
* **Azure Portal** is de beheerinterface waar u uw API-programma instelt. Gebruik deze voor het volgende:
  
  * API-schema definiëren of importeren.
  * API's verpakken in producten.
  * Beleidsregels instellen zoals quota of transformaties voor de API's.
  * Inzicht krijgen van analytische gegevens.
  * Gebruikers beheren.
* De **portal voor ontwikkelaars** fungeert als de belangrijkste informatiebron op het web voor ontwikkelaars. Ze kunnen hier het volgende doen:
  
  * API-documentatie lezen.
  * Probeer een API uit via de interactieve console.
  * Maak een account en abonneer u om API-sleutels op te halen.
  * Analytische gegevens openen over hun eigen gebruik.

Raadpleeg het technische document in PDF-indeling [API Management in de cloud: de mogelijkheden van API's benutten](https://j.mp/ms-apim-whitepaper) voor meer informatie. In dit inleidende technische document over API Management door CITO Research wordt het volgende besproken: 
 
 * Algemene API-vereisten en -uitdagingen
 * Loskoppelen van API's en presenteren van facades
 * Ontwikkelaars snel aan de slag helpen
 * Toegang beveiligen
 * Analytische en metrische gegevens
 * Controle en inzicht verkrijgen met een API Management-platform
 * Cloud- versus on-premises oplossingen gebruiken
 * Azure API Management
 
## <a name="apis-and-operations"></a><a name="apis"> </a>API's en bewerkingen
API's vormen de basis van een service-exemplaar van API Management. Elke API vertegenwoordigt een reeks bewerkingen die beschikbaar zijn voor ontwikkelaars. Elke API bevat een verwijzing naar de back-endservice waarmee de API wordt geïmplementeerd, en de bewerkingen zijn toegewezen aan de bewerkingen die zijn geïmplementeerd door de back-endservice. Bewerkingen in API Management zijn zeer goed te configureren, met controle over URL-toewijzing, query- en padparameters, aanvraag- en antwoordinhoud en het in de cache opslaan van bewerkingsantwoorden. Beleidsregels voor frequentielimiet, quota en IP-restrictie kunnen ook op API-niveau of op het niveau van de afzonderlijke bewerking worden geïmplementeerd.

Zie voor meer informatie [API's maken][How to create APIs] en [Bewerkingen toevoegen aan een API][How to add operations to an API].

## <a name="products"></a><a name="products"> </a> Producten
Producten zijn de manier waarop de API's worden opgehaald voor ontwikkelaars. Producten in API Management hebben een of meer API's en worden geconfigureerd met een titel, beschrijving en gebruiksvoorwaarden. Producten kunnen **open** of **beveiligd** zijn. Voor beveiligde producten is een abonnement nodig voordat ze kunnen worden gebruikt, terwijl open producten zonder abonnement kunnen worden gebruikt. Wanneer een product gereed is voor gebruik door ontwikkelaars, kan het worden gepubliceerd. Zodra het is gepubliceerd, kan het worden bekeken (en in het geval van beveiligde producten kan er een abonnement op worden genomen) door ontwikkelaars. Goedkeuring van abonnementen wordt geconfigureerd op productniveau en er kan beheerdersgoedkeuring voor vereist zijn of abonnementen kunnen automatisch worden goedgekeurd.

Groepen worden gebruikt voor het beheren van de zichtbaarheid van producten voor ontwikkelaars. Voor producten wordt zichtbaarheid aan groepen verleend en ontwikkelaars kunnen de producten bekijken en zich abonneren op de producten die zichtbaar zijn voor de groepen waartoe de ontwikkelaars behoren. 

## <a name="groups"></a><a name="groups"> </a> Groepen
Groepen worden gebruikt voor het beheren van de zichtbaarheid van producten voor ontwikkelaars. API Management heeft de volgende onveranderbare systeemgroepen:

* **Beheerders** - Azure-abonnementbeheerders zijn lid van deze groep. Beheerders beheren service-exemplaren van API Management, door het maken van de API's, bewerkingen en producten die door ontwikkelaars worden gebruikt.
* **Ontwikkelaars** - Geverifieerde gebruikers van de ontwikkelaarsportal vallen in deze groep. Ontwikkelaars zijn de klanten die toepassingen bouwen met uw API's. Ontwikkelaars krijgen toegang tot de ontwikkelaarsportal en bouwen toepassingen waarmee de bewerkingen van een API worden aangeroepen.
* **Gasten** - Niet-geverifieerde gebruikers van de ontwikkelaarsportal, zoals potentiële klanten die de ontwikkelaarsportal van een API Management-exemplaar bezoeken, vallen in deze groep. Ze kunnen bepaalde alleen-lezentoegang krijgen, zoals de mogelijkheid om API's te bekijken maar niet om ze aan te roepen.

Naast deze systeemgroepen kunnen beheerders aangepaste groepen maken of [gebruikmaken van externe groepen in gekoppelde Azure Active Directory-tenants](api-management-howto-aad.md). Aangepaste en externe groepen kunnen naast systeemgroepen worden gebruikt om ontwikkelaars zichtbaarheid van en toegang tot API-producten te geven. U kunt bijvoorbeeld één aangepaste groep maken voor ontwikkelaars die zijn gekoppeld aan een specifieke partnerorganisatie en hen toegang geven tot de API's vanuit een product dat alleen relevante API's bevat. Een gebruiker kan lid zijn van meerdere groepen.

Zie voor meer informatie [Groepen maken en gebruiken][How to create and use groups].

## <a name="developers"></a><a name="developers"> </a> Ontwikkelaars
Ontwikkelaars vertegenwoordigen de gebruikersaccounts in een service-exemplaar van API Management. Ontwikkelaars kunnen worden gemaakt of worden uitgenodigd voor deelname door beheerders, of ze kunnen zich registreren vanuit de [ontwikkelaarsportal][Developer portal]. Elke ontwikkelaar is lid van een of meer groepen en mag zich abonneren op de producten die zichtbaarheid aan deze groepen verlenen.

Als ontwikkelaars zich op een product abonneren, krijgen ze de primaire en secundaire sleutel voor het product. Deze sleutel wordt gebruikt bij het aanroepen van de API's van het product.

Zie voor meer informatie [Ontwikkelaars maken of uitnodigen][How to create or invite developers] en [Groepen koppelen aan ontwikkelaars][How to associate groups with developers].

## <a name="policies"></a><a name="policies"> </a> Beleidsregels
Beleidsregels zijn een krachtige mogelijkheid van API Management waarmee het gedrag van de API via configuratie in Azure Portal kan worden gewijzigd. Beleidsregels zijn een verzameling instructies die sequentieel worden uitgevoerd op de aanvraag of het antwoord van een API. Populaire instructies omvatten indelingsconversie van XML in JSON en beperking van de aanroepfrequentie om het aantal inkomende aanroepen van een ontwikkelaar te beperken. Er zijn nog vele andere beleidsregels beschikbaar.

Beleidsexpressies kunnen worden gebruikt als kenmerkwaarden of tekstwaarden in API Management-beleidsregels, tenzij het beleid iets anders aangeeft. Sommige beleidsregels, zoals de beleidsregels [Stroom controleren](./api-management-advanced-policies.md#choose) en [Variabele instellen](./api-management-advanced-policies.md#set-variable), zijn gebaseerd op beleidsexpressies. Zie [Geavanceerde beleidsregels](./api-management-advanced-policies.md#AdvancedPolicies) en [Beleidsexpressies](./api-management-policy-expressions.md) voor meer informatie.


Zie [Naslaginformatie over beleid][Policy reference] voor een volledige lijst met API Management-beleidsregels. Zie [API Management-beleidsregels][API Management policies]voor meer informatie over het gebruiken en configureren van beleidsregels. Zie [Geavanceerde productinstellingen maken en configureren][How create and configure advanced product settings] voor een zelfstudie over het maken van een product met beleidsregels voor frequentielimiet en quotum.


## <a name="developer-portal"></a><a name="developer-portal"> </a> Ontwikkelaarsportal
De ontwikkelaarsportal is de plek waar ontwikkelaars meer informatie kunnen vinden over uw API's, bewerkingen kunnen bekijken en aanroepen en zich op producten kunnen abonneren. Potentiële klanten kunnen de ontwikkelaarsportal bezoeken, API's en bewerkingen bekijken en zich registreren. De URL voor uw ontwikkelaarsportal bevindt zich in het dashboard in Azure Portal voor uw service-exemplaar van API Management.

U kunt het uiterlijk van uw ontwikkelaarsportal aanpassen door aangepaste inhoud toe te voegen, stijlen aan te passen en uw huisstijl toe te voegen.

## <a name="api-management-and-the-api-economy"></a>API Management en de API-economie

Bekijk de volgende presentatie van de conferentie Microsoft Ignite 2017 voor meer informatie over API Management.

> [!VIDEO https://channel9.msdn.com/Events/Ignite/Microsoft-Ignite-Orlando-2017/BRK2186/player]
> 
> 

## <a name="next-steps"></a>Volgende stappen

Voltooi de volgende Quick Start en ga aan de slag met Azure API Management:

> [!div class="nextstepaction"]
> [Een Azure API Management-exemplaar maken](get-started-create-service-instance.md)

[APIs and operations]: #apis
[Products]: #products
[Groups]: #groups
[Developers]: #developers
[Policies]: #policies
[Developer portal]: #developer-portal

[How to create APIs]: ./import-and-publish.md
[How to add operations to an API]: ./mock-api-responses.md
[How to create and publish a product]: api-management-howto-add-products.md
[How to create and use groups]: api-management-howto-create-groups.md
[How to associate groups with developers]: api-management-howto-create-groups.md#associate-group-developer
[How create and configure advanced product settings]: transform-api.md
[How to create or invite developers]: api-management-howto-create-or-invite-developers.md
[Policy reference]: ./api-management-policies.md
[API Management policies]: api-management-howto-policies.md
[Create an API Management service instance]: get-started-create-service-instance.md
