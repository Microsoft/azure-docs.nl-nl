---
title: Hoe een toepassings gateway werkt
description: Dit artikel bevat informatie over hoe een toepassings gateway binnenkomende aanvragen accepteert en naar de back-end stuurt.
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: conceptual
ms.date: 11/16/2019
ms.author: absha
ms.openlocfilehash: d2055bf812c3dc986a907d4358fa0e74e8af20fa
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104599660"
---
# <a name="how-an-application-gateway-works"></a>Hoe een toepassings gateway werkt

In dit artikel wordt uitgelegd hoe een toepassings gateway binnenkomende aanvragen accepteert en naar de back-end stuurt.

![Hoe een toepassings gateway een aanvraag accepteert](./media/how-application-gateway-works/how-application-gateway-works.png)

## <a name="how-an-application-gateway-accepts-a-request"></a>Hoe een toepassings gateway een aanvraag accepteert

1. Voordat een client een aanvraag naar een toepassings Gateway verzendt, wordt de domein naam van de toepassings gateway omgezet met behulp van een Domain Name System (DNS)-server. Azure beheert de DNS-vermelding omdat alle toepassings gateways zich in het azure.com-domein bevinden.

2. De Azure DNS retourneert het IP-adres naar de client. Dit is het front-end-IP-adres van de toepassings gateway.

3. De toepassings gateway accepteert binnenkomend verkeer op een of meer listeners. Een listener is een logische entiteit die controleert op verbindings aanvragen. Het is geconfigureerd met een frontend-IP-adres,-protocol en-poort nummer voor verbindingen van clients naar de toepassings gateway.

4. Als een Web Application Firewall (WAF) wordt gebruikt, controleert de toepassings gateway de aanvraag headers en de hoofd tekst, indien aanwezig, tegen WAF-regels. Met deze actie wordt bepaald of de aanvraag een geldige aanvraag of een beveiligings risico is. Als de aanvraag geldig is, wordt deze doorgestuurd naar de back-end. Als de aanvraag niet geldig is en WAF zich in preventie modus bevindt, wordt deze als een beveiligings risico geblokkeerd. Als het zich in de detectie modus bevindt, wordt de aanvraag geëvalueerd en geregistreerd, maar wordt deze wel doorgestuurd naar de back-endserver.

Azure-toepassing gateway kan worden gebruikt als een interne toepassings load balancer of als een load balancer voor Internet gerichte toepassingen. Een Internet gerichte toepassings gateway maakt gebruik van open bare IP-adressen. De DNS-naam van een Internet gerichte toepassings gateway kan openbaar worden omgezet naar het open bare IP-adres. Als gevolg hiervan kunnen Internet gerichte toepassings gateways client aanvragen van het Internet routeren.

Interne toepassings gateways gebruiken alleen particuliere IP-adressen. Als u een aangepaste of [privé-DNS zone](../dns/private-dns-overview.md)gebruikt, moet de domein naam intern worden omgezet naar het privé-IP-adres van de Application Gateway. Daarom kunnen interne load balancers alleen aanvragen van clients met toegang tot een virtueel netwerk voor de toepassings gateway routeren.

## <a name="how-an-application-gateway-routes-a-request"></a>Hoe een toepassings gateway een aanvraag routert

Als een aanvraag geldig is en niet wordt geblokkeerd door WAF, evalueert de toepassings gateway de routerings regel voor aanvragen die is gekoppeld aan de listener. Met deze actie wordt bepaald aan welke back-end-groep de aanvraag moet worden door gestuurd.

Op basis van de regel voor aanvraag routering, bepaalt de toepassings gateway of alle aanvragen op de listener naar een specifieke back-end-groep moeten worden gerouteerd, worden aanvragen naar verschillende back-Pools gerouteerd op basis van het URL-pad of aanvragen omleiden naar een andere poort of externe site.
>[!NOTE]
>Regels worden verwerkt in de volg orde waarin ze worden weer gegeven in de portal voor v1-SKU. 

Wanneer de toepassings gateway de back-end-pool selecteert, wordt de aanvraag verzonden naar een van de gezonde back-endservers in de pool (y. y. y. y). De status van de server wordt bepaald door een status test. Als de back-endadresgroep meerdere servers bevat, gebruikt de toepassings gateway een Round Robin-algoritme om de aanvragen tussen servers in orde te routeren. Met deze taak worden de aanvragen op de servers gebalanceerd.

Nadat de toepassings gateway de back-endserver heeft vastgesteld, opent deze een nieuwe TCP-sessie met de back-endserver op basis van HTTP-instellingen. HTTP-instellingen geven het Protocol, de poort en andere routerings instellingen op die nodig zijn om een nieuwe sessie met de back-endserver tot stand te brengen.

De poort en het protocol dat wordt gebruikt in HTTP-instellingen bepalen of het verkeer tussen de toepassings gateway en de back-endservers is versleuteld (waardoor end-to-end TLS wordt uitgevoerd) of niet versleuteld is.

Wanneer een toepassings gateway de oorspronkelijke aanvraag naar de back-endserver verzendt, wordt er een aangepaste configuratie in de HTTP-instellingen met betrekking tot het overschrijven van de hostnaam, het pad en het Protocol, in rekening gebracht. Deze actie houdt sessie affiniteit op basis van cookies, verwerkings verwerkings stop, het selecteren van een host-naam van de back-end, enzovoort.

 >[!NOTE]
>Als de back-end-groep:
> - **Is een openbaar eind punt**, de toepassings gateway maakt gebruik van het front-end open bare IP-adres van de server. Als er geen openbaar frontend-IP-adres is, wordt er een toegewezen voor de uitgaande externe verbinding.
> - **Bevat een intern omgezette FQDN of een privé-IP-adres**. de toepassings gateway stuurt de aanvraag naar de back-endserver door de privé-IP-adressen van het exemplaar te gebruiken.
> - **Bevat een extern eind punt of een extern omgezette FQDN**-naam, de toepassings gateway stuurt de aanvraag naar de back-endserver via het open bare frontend-IP-adres. De DNS-omzetting is gebaseerd op een privé-DNS-zone of een aangepaste DNS-server, als deze is geconfigureerd of gebruikmaakt van de standaard-DNS van Azure. Als er geen openbaar frontend-IP-adres is, wordt er een toegewezen voor de uitgaande externe verbinding.

### <a name="modifications-to-the-request"></a>Wijzigingen in de aanvraag

Een toepassings Gateway voegt vier extra headers toe aan alle aanvragen voordat de aanvragen worden doorgestuurd naar de back-end. Deze headers worden x-doorgestuurd: voor x-doorgestuurde proto, x-doorgestuurde poorten en x-original-host. De notatie voor x-doorgestuurde header is een door komma's gescheiden lijst met IP-adressen: poort.

De geldige waarden voor x-doorgestuurde-proto zijn HTTP of HTTPS. X-doorgestuurd: poort Hiermee geeft u de poort op waarop de aanvraag de toepassings gateway heeft bereikt. X-oorspronkelijke host-header bevat de oorspronkelijke hostheader waarmee de aanvraag is aangekomen. Deze header is handig in de integratie van Azure-websites, waarbij de inkomende host-header wordt gewijzigd voordat het verkeer naar de back-end wordt doorgestuurd. Als sessie affiniteit als een optie is ingeschakeld, wordt er een door gateway beheerde affiniteits cookie toegevoegd.

U kunt de toepassings gateway configureren voor het wijzigen van de aanvraag-en antwoord headers en de URL door gebruik te maken van [HTTP-headers en URL herschrijven](rewrite-http-headers-url.md) of het pad naar de URI te wijzigen met behulp van een pad-onderdrukkings instelling. Tenzij geconfigureerd om dit te doen, worden alle binnenkomende aanvragen echter doorgestuurd naar de back-end.

## <a name="next-steps"></a>Volgende stappen

[Meer informatie over Application Gateway-onderdelen](application-gateway-components.md)
