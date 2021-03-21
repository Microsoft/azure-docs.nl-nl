---
title: Problemen met de configuratie van Azure front deur, standaard/Premium oplossen
description: In deze zelf studie leert u hoe u een aantal veelvoorkomende problemen kunt oplossen voor uw Azure front deur Standard/Premium-exemplaar.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: how-to
ms.date: 02/18/2021
ms.author: qixwang
ms.openlocfilehash: 4690a513494d794377ee0c2e8cfb101e8fd66a0f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "101099256"
---
# <a name="troubleshooting-common-routing-problems-with-azure-front-door-standardpremium"></a>Problemen oplossen met veelvoorkomende routerings problemen met Azure front deur Standard/Premium

In dit artikel wordt beschreven hoe u veelvoorkomende routerings problemen oplost die u kunt voor de configuratie van uw Azure-voor deur.

## <a name="503-response-from-azure-front-door-after-a-few-seconds"></a>503 reactie van de voor deur van Azure na een paar seconden

### <a name="symptom"></a>Symptoom

* Regel matige aanvragen die worden verzonden naar uw back-end zonder de voor deur van Azure te passeren, slagen. De resultaten van een Azure-deur lopen in 503 fout reacties.
* De uitval van Azure front deur wordt doorgaans na ongeveer 30 seconden weer gegeven.

### <a name="cause"></a>Oorzaak

De oorzaak van dit probleem kan een van de volgende twee dingen zijn:
 
* Uw oorsprong duurt langer dan de geconfigureerde time-out (de standaard waarde is 30 seconden) om de aanvraag van de Azure front-deur te ontvangen.
* De tijd die nodig is om een antwoord te verzenden naar de aanvraag van een Azure front deur duurt langer dan de time-outwaarde. 

### <a name="troubleshooting-steps"></a>Stappen voor probleemoplossing

* Verzend de aanvraag rechtstreeks naar uw back-end (zonder de voor deur van Azure te passeren). Bekijk hoe lang uw back-end normaal gesp roken reageert.
* Verzend de aanvraag via Azure front deur en kijk of er 503-antwoorden zijn. Als dat niet het geval is, is het probleem mogelijk geen time-outprobleem. Neem contact op met ondersteuning.
* Als de status van 503 een Azure-voor deur wordt door lopen, configureert u het `sendReceiveTimeout` veld voor de Azure front-deur. U kunt de standaardtime-out tot 4 minuten verlengen (240 seconden). De instelling wordt `Endpoint Setting` genoemd `Origin response timeout` . 

## <a name="requests-sent-to-the-custom-domain-return-a-400-status-code"></a>Aanvragen die worden verzonden naar het aangepaste domein, retour neren een 400-status code

### <a name="symptom"></a>Symptoom

* U hebt een exemplaar van de Azure front-deur gemaakt, maar een aanvraag aan de domein-of frontend-host retourneert een HTTP 400-status code.
* U hebt een DNS-toewijzing voor een aangepast domein gemaakt aan de frontend-host die u hebt geconfigureerd. Het verzenden van een aanvraag naar de naam van de aangepaste domein-hostnaam retourneert echter een HTTP 400-status code. Het lijkt niet te leiden naar de back-end die u hebt geconfigureerd.

### <a name="cause"></a>Oorzaak

Het probleem treedt op als u geen routerings regel hebt geconfigureerd voor het aangepaste domein dat is toegevoegd als de frontend-host. Een routerings regel moet expliciet worden toegevoegd voor die frontend-host. Dat is waar, zelfs als een regel voor het door sturen van de bewerkings plan al is geconfigureerd voor de frontend-host onder het subdomein van de Azure-front-deur (*. azurefd.net).

### <a name="troubleshooting-steps"></a>Stappen voor probleemoplossing

Voeg een routerings regel voor het aangepaste domein toe om verkeer naar de geselecteerde oorspronkelijke groep te sturen.

## <a name="azure-front-door-doesnt-redirect-http-to-https"></a>HTTP wordt niet omgeleid naar HTTPS door Azure front deur

### <a name="symptom"></a>Symptoom

De voor deur van Azure heeft een regel voor het door sturen van HTTP naar HTTPS, maar de toegang tot het domein blijft behouden als het protocol.

### <a name="cause"></a>Oorzaak

Dit probleem kan optreden als u de routerings regels niet correct hebt geconfigureerd voor Azure front-deur. In principe is uw huidige configuratie niet specifiek en kunnen er conflicterende regels zijn.

### <a name="troubleshooting-steps"></a>Stappen voor probleemoplossing


## <a name="request-to-the-frontend-host-name-returns-a-411-status-code"></a>De aanvraag voor de frontend-hostnaam retourneert een 411-status code

### <a name="symptom"></a>Symptoom

U hebt een Azure front deur Standard/Premium-exemplaar gemaakt en een frontend-host, een oorspronkelijke groep met ten minste één oorsprong, en een routerings regel die de frontend-host verbindt met de oorspronkelijke groep. Uw inhoud lijkt niet beschikbaar te zijn wanneer een aanvraag naar de geconfigureerde frontend-host gaat, omdat er een HTTP 411-status code wordt geretourneerd.

Antwoorden op deze aanvragen kunnen ook een HTML-fout pagina bevatten in de antwoord tekst die een verklaring bevat. Bijvoorbeeld: `HTTP Error 411. The request must be chunked or have a content length`.

### <a name="cause"></a>Oorzaak

Er zijn verschillende mogelijke oorzaken voor dit symptoom. De algehele reden is dat uw HTTP-aanvraag niet volledig compatibel is met RFC. 

Een voor beeld van een niet-naleving is een `POST` aanvraag die zonder een `Content-Length` -of een `Transfer-Encoding` -header wordt verzonden (bijvoorbeeld met `curl -X POST https://example-front-door.domain.com` ). Deze aanvraag voldoet niet aan de vereisten die zijn opgegeven in [RFC 7230](https://tools.ietf.org/html/rfc7230#section-3.3.2). Azure front-deur blokkeert deze met een HTTP 411-antwoord.

Dit gedrag is gescheiden van de WAF-functionaliteit (Web Application firewall) van Azure front deur. Op dit moment is er geen manier om dit gedrag uit te scha kelen. Alle HTTP-aanvragen moeten voldoen aan de vereisten, zelfs als de WAF-functionaliteit niet wordt gebruikt.

### <a name="troubleshooting-steps"></a>Stappen voor probleemoplossing

- Controleer of uw aanvragen voldoen aan de vereisten in de benodigde Rfc's.

- Noteer de HTML-bericht tekst die wordt geretourneerd als reactie op uw aanvraag. Een bericht tekst legt vaak precies uit *hoe* uw aanvraag niet-compatibel is.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het [maken van een voor deur standaard/Premium](create-front-door-portal.md).
