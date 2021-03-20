---
title: Problemen met configuratie van Azure front-deur oplossen
description: In deze zelf studie leert u hoe u zelf een aantal veelvoorkomende problemen kunt oplossen die u voor uw Azure front deur-exemplaar zou kunnen opsporen.
services: frontdoor
documentationcenter: ''
author: duongau
editor: ''
ms.service: frontdoor
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 09/30/2020
ms.author: duau
ms.openlocfilehash: 15cdcefe628a392704e650b560243e2f6a134ec2
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94629985"
---
# <a name="troubleshooting-common-routing-problems"></a>Veelvoorkomende problemen met route ring oplossen

In dit artikel wordt beschreven hoe u veelvoorkomende routerings problemen oplost die u kunt voor de configuratie van uw Azure-voor deur.

## <a name="503-response-from-azure-front-door-after-a-few-seconds"></a>503 reactie van de voor deur van Azure na een paar seconden

### <a name="symptom"></a>Symptoom

* Regel matige aanvragen die worden verzonden naar uw back-end zonder de voor deur van Azure te passeren, slagen. De resultaten van een Azure-deur lopen in 503 fout reacties.
* De uitval van Azure front deur wordt doorgaans na ongeveer 30 seconden weer gegeven.

### <a name="cause"></a>Oorzaak

De oorzaak van dit probleem kan een van de volgende twee dingen zijn:
 
* De back-end duurt langer dan de geconfigureerde time-out (de standaard waarde is 30 seconden) om de aanvraag van de Azure front-deur te ontvangen.
* De tijd die nodig is om een antwoord te verzenden naar de aanvraag van een Azure front deur duurt langer dan de time-outwaarde. 

### <a name="troubleshooting-steps"></a>Stappen voor probleemoplossing

* Verzend de aanvraag rechtstreeks naar uw back-end (zonder de voor deur van Azure te passeren). Bekijk hoe lang uw back-end normaal gesp roken reageert.
* Verzend de aanvraag via Azure front deur en kijk of er 503-antwoorden zijn. Als dat niet het geval is, is het probleem mogelijk geen time-outprobleem. Neem contact op met ondersteuning.
* Als de status van 503 een Azure-voor deur wordt door lopen, configureert u het `sendReceiveTimeout` veld voor de Azure front-deur. U kunt de standaardtime-out tot 4 minuten verlengen (240 seconden). De instelling wordt `backendPoolSettings` genoemd `sendRecvTimeoutSeconds` . 

## <a name="requests-sent-to-the-custom-domain-return-a-400-status-code"></a>Aanvragen die worden verzonden naar het aangepaste domein, retour neren een 400-status code

### <a name="symptom"></a>Symptoom

* U hebt een exemplaar van de Azure front-deur gemaakt, maar een aanvraag aan de domein-of frontend-host retourneert een HTTP 400-status code.
* U hebt een DNS-toewijzing voor een aangepast domein gemaakt aan de frontend-host die u hebt geconfigureerd. Het verzenden van een aanvraag naar de naam van de aangepaste domein-hostnaam retourneert echter een HTTP 400-status code. Het lijkt niet te leiden naar de back-end die u hebt geconfigureerd.

### <a name="cause"></a>Oorzaak

Het probleem treedt op als u geen routerings regel hebt geconfigureerd voor het aangepaste domein dat is toegevoegd als de frontend-host. Een routerings regel moet expliciet worden toegevoegd voor die frontend-host. Dat is waar, zelfs als een regel voor het door sturen van de bewerkings plan al is geconfigureerd voor de frontend-host onder het subdomein van de Azure-front-deur (*. azurefd.net).

### <a name="troubleshooting-steps"></a>Stappen voor probleemoplossing

Voeg een routerings regel voor het aangepaste domein toe om verkeer naar de geselecteerde back-end-groep te sturen.

## <a name="azure-front-door-doesnt-redirect-http-to-https"></a>HTTP wordt niet omgeleid naar HTTPS door Azure front deur

### <a name="symptom"></a>Symptoom

De voor deur van Azure heeft een regel voor het door sturen van HTTP naar HTTPS, maar de toegang tot het domein blijft behouden als het protocol.

### <a name="cause"></a>Oorzaak

Dit probleem kan optreden als u de routerings regels niet correct hebt geconfigureerd voor Azure front-deur. In principe is uw huidige configuratie niet specifiek en kunnen er conflicterende regels zijn.

### <a name="troubleshooting-steps"></a>Stappen voor probleemoplossing

## <a name="request-to-a-frontend-host-name-returns-a-404-status-code"></a>Aanvraag aan een frontend-hostnaam retourneert een 404-status code

### <a name="symptom"></a>Symptoom

U hebt een exemplaar van de Azure-front-deur gemaakt door een frontend-host, een back-end-pool met ten minste één back-end te configureren en een routerings regel die de frontend-host verbindt met de back-end-groep. Uw inhoud is niet beschikbaar wanneer u een aanvraag voor de geconfigureerde frontend-host maakt. Als gevolg hiervan retourneert de aanvraag een HTTP 404-status code.

### <a name="cause"></a>Oorzaak

Er zijn verschillende mogelijke oorzaken voor dit symptoom:

* De back-end is geen open bare back-end en is niet zichtbaar voor de Azure front-deur.
* De back-end is onjuist geconfigureerd, waardoor de verkeerde aanvraag door de Azure-deur wordt verzonden. Met andere woorden, uw back-end accepteert alleen HTTP en u hebt HTTPS niet toegestaan. Daarom probeert de voor deur van Azure HTTPS-aanvragen door te sturen.
* De back-end weigert de host-header die is doorgestuurd met de aanvraag naar de back-end.
* De configuratie voor de back-end is nog niet volledig geïmplementeerd.

### <a name="troubleshooting-steps"></a>Stappen voor probleemoplossing

* Controleer de implementatie tijd:
   * Zorg ervoor dat u ten minste 10 minuten hebt gewacht totdat de configuratie is geïmplementeerd.

* Controleer de back-end-instellingen:
    * Ga naar de back-end-groep waarnaar de aanvraag moet worden doorgestuurd. (Dit is afhankelijk van hoe u de routerings regel hebt geconfigureerd.) Controleer of het type back-end en de hostnaam van de back-end juist zijn. Als de back-end een aangepaste host is, controleert u of u deze correct hebt gespeld. 

    * Controleer uw HTTP-en HTTPS-poorten. In de meeste gevallen zijn 80 en 443 (respectievelijk) juist en er zijn geen wijzigingen vereist. Er is echter een kans dat uw back-end niet op deze manier is geconfigureerd en luistert op een andere poort.

    * Controleer of de back-end-host-header is geconfigureerd voor de back-end waarnaar de frontend-host moet worden gerouteerd. In de meeste gevallen moet deze header hetzelfde zijn als de naam van de back-end. Een onjuiste waarde kan echter verschillende HTTP 4xx-status codes veroorzaken als de back-end iets anders verwacht. Als u het IP-adres van uw back-end opgeeft, moet u mogelijk de host-header van de back-end instellen op de hostnaam van de back-end.

* Controleer de instellingen voor de routerings regel:
    * Ga naar de routerings regel die van de frontend-hostnaam in de betreffende naam moet worden gerouteerd naar een back-end-groep. Zorg ervoor dat de geaccepteerde protocollen correct zijn geconfigureerd wanneer de aanvraag wordt doorgestuurd. Het veld **geaccepteerde protocollen** bepaalt welke aanvragen Azure front-deur moeten accepteren. Het doorstuur protocol bepaalt welk protocol Azure front deur moet gebruiken om de aanvraag door te sturen naar de back-end.
      
      Als de back-end bijvoorbeeld alleen HTTP-aanvragen accepteert, zijn de volgende configuraties geldig:
            
      * Geaccepteerde protocollen zijn HTTP en HTTPS. Het doorstuur protocol is HTTP. Een match-aanvraag werkt niet, omdat HTTPS een toegestaan protocol is. Als een aanvraag is ontvangen als HTTPS, probeert Azure front-deur deze door te sturen met behulp van HTTPS.

      * Het geaccepteerde protocol is HTTP. Het doorstuur protocol is een match-aanvraag of HTTP.
    - Het opnieuw schrijven van de **URL** is standaard uitgeschakeld. Dit veld wordt alleen gebruikt als u het bereik van op back-gehoste bronnen wilt beperken dat u beschikbaar wilt maken. Als dit veld is uitgeschakeld, wordt door Azure front-deur hetzelfde aanvraag traject doorgestuurd dat het ontvangt. Dit veld kan niet worden geconfigureerd. Als Azure front deur een bron van de back-end die niet beschikbaar is, dus retourneert, wordt een HTTP 404-status code geretourneerd.

## <a name="request-to-the-frontend-host-name-returns-a-411-status-code"></a>De aanvraag voor de frontend-hostnaam retourneert een 411-status code

### <a name="symptom"></a>Symptoom

U hebt een exemplaar van de Azure-front-deur gemaakt en een frontend-host geconfigureerd, een back-end-pool met ten minste één back-end en een routerings regel die de frontend-host verbindt met de back-end-groep. Uw inhoud lijkt niet beschikbaar te zijn wanneer een aanvraag naar de geconfigureerde frontend-host gaat, omdat er een HTTP 411-status code wordt geretourneerd.

Antwoorden op deze aanvragen kunnen ook een HTML-fout pagina bevatten in de antwoord tekst die een verklaring bevat. Bijvoorbeeld: `HTTP Error 411. The request must be chunked or have a content length`.

### <a name="cause"></a>Oorzaak

Er zijn verschillende mogelijke oorzaken voor dit symptoom. De algehele reden is dat uw HTTP-aanvraag niet volledig compatibel is met RFC. 

Een voor beeld van een niet-naleving is een `POST` aanvraag die zonder een `Content-Length` -of een `Transfer-Encoding` -header wordt verzonden (bijvoorbeeld met `curl -X POST https://example-front-door.domain.com` ). Deze aanvraag voldoet niet aan de vereisten die zijn opgegeven in [RFC 7230](https://tools.ietf.org/html/rfc7230#section-3.3.2). Azure front-deur blokkeert deze met een HTTP 411-antwoord.

Dit gedrag is gescheiden van de WAF-functionaliteit (Web Application firewall) van Azure front deur. Op dit moment is er geen manier om dit gedrag uit te scha kelen. Alle HTTP-aanvragen moeten voldoen aan de vereisten, zelfs als de WAF-functionaliteit niet wordt gebruikt.

### <a name="troubleshooting-steps"></a>Stappen voor probleemoplossing

- Controleer of uw aanvragen voldoen aan de vereisten in de benodigde Rfc's.

- Noteer de HTML-bericht tekst die wordt geretourneerd als reactie op uw aanvraag. Een bericht tekst legt vaak precies uit *hoe* uw aanvraag niet-compatibel is.
