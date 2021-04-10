---
title: Azure front deur Standard/Premium (preview)-logboek registratie
description: In dit artikel wordt uitgelegd hoe logboek registratie werkt in azure front deur Standard/Premium.
services: front-door
author: duongau
ms.service: frontdoor
ms.topic: article
ms.date: 03/15/2021
ms.author: duau
ms.openlocfilehash: 531f4a9c9f535779e451ca316a8a5867f6cdaba5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "103573894"
---
# <a name="azure-front-door-standardpremium-preview-logging"></a>Azure front deur Standard/Premium (preview)-logboek registratie

> [!Note]
> Deze documentatie is voor Azure front deur Standard/Premium (preview). Zoekt u informatie over de voor deur van Azure? [Hier](../front-door-overview.md)weer geven.

De front-deur van Azure biedt verschillende logboek registraties waarmee u uw voor deur kunt volgen, bewaken en opsporen. 

* Toegangs logboeken bevatten gedetailleerde informatie over elke aanvraag die door AFD wordt ontvangen en helpt u bij het analyseren en bewaken van toegangs patronen en het oplossen van problemen. 
* Met activiteiten logboeken krijgt u inzicht in de bewerkingen die worden uitgevoerd op Azure-resources.  
* Health probe-logboeken bieden de logboeken voor elke mislukte test naar uw oorsprong. 
* WAF-Logboeken (Web Application firewall) bieden gedetailleerde informatie over aanvragen die worden geregistreerd via de detectie-of preventie modus van een Azure front-deur eindpunt. Een aangepast domein dat met WAF wordt geconfigureerd, kan ook via deze logboeken worden weer gegeven.

> [!IMPORTANT]
> Azure front deur Standard/Premium (preview) is momenteel beschikbaar als open bare preview.
> Deze preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt.
> Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

Access-logboeken, Health probe-logboeken en WAF-logboeken zijn niet standaard ingeschakeld. Volg de onderstaande stappen om logboek registratie in te scha kelen. Activiteitenlogboekitems worden standaard verzameld en kunnen in de Azure-portal worden bekeken. Logboeken kunnen een paar minuten duren. 

U hebt drie opties voor het opslaan van uw logboeken: 

* **Opslag account:** Opslag accounts worden het beste gebruikt voor scenario's wanneer logboeken worden opgeslagen voor een langere duur en worden gecontroleerd wanneer dit nodig is. 
* **Event hubs:** Event hubs zijn een uitstekende optie om te integreren met andere SIEM-hulpprogram ma's (Security Information and Event Management) of externe gegevens opslag. Bijvoorbeeld: Splunk/DataDog/Sumo. 
* **Azure log Analytics:** Azure Log Analytics in Azure Monitor wordt het meest gebruikt voor algemene realtime-bewaking en analyse van de prestaties van de Azure-voor deur.

## <a name="configure-logs"></a>Logboeken configureren

1. Meld u aan bij [Azure Portal](https://portal.azure.com).

1. Zoek naar de voor deur standaard/Premium van Azure en selecteer het profiel voor de Azure front-deur.

1. Ga in het profiel naar **bewaking**, selecteer **Diagnostische instelling**. Selecteer **Diagnostische instellingen toevoegen**.

   :::image type="content" source="../media/how-to-logging/front-door-logging-1.png" alt-text="Scherm afbeelding van de pagina met de diagnostische instellingen voor het land.":::

1. Voer onder **Diagnostische instellingen** een naam in voor de naam van de  **Diagnostische instellingen**.

1. Selecteer het  **logboek** in **FrontDoorAccessLog**, **FrontDoorHealthProbeLog** en **FrontDoorWebApplicationFirewallLog**.

1. Selecteer de **doel gegevens**. Bestemmings opties zijn: 

    * **Verzenden naar Log Analytics**
        * Selecteer het *abonnement* en de *log Analytics-werk ruimte*.
    * **Archiveren naar een opslag account**
        * Selecteer het *abonnement* en het *opslag account*. en instellen van **bewaren (dagen)**.
    * **Streamen naar een Event Hub**
        * Selecteer het *abonnement, de Event hub-naam ruimte, de naam van de Event hub (optioneel)* en de naam van het *Event hub-beleid*. 

     :::image type="content" source="../media/how-to-logging/front-door-logging-2.png" alt-text="Scherm opname van de pagina Diagnostische instellingen.":::

1. Klik op **Opslaan**.

## <a name="access-log"></a>Access-logboek

Azure front-deur biedt momenteel afzonderlijke API-aanvragen voor elke vermelding met het volgende schema en een geregistreerde JSON-indeling zoals hieronder wordt weer gegeven. 

| Eigenschap | Beschrijving |
|----------|-------------| 
| TrackingReference | De unieke verwijzings reeks die een aanvraag identificeert door AFD, ook als X-Azure-ref-header naar de client verzonden. Vereist voor het zoeken naar details in de logboeken van de toegang voor een specifieke aanvraag. |
| Tijd | De datum en tijd waarop de AFD-rand de aangevraagde inhoud aan de client heeft geleverd (in UTC). |
| HttpMethod | HTTP-methode die wordt gebruikt door de aanvraag: verwijderen, ophalen, HEAD, OPTIONS, PATCH, POST of PUT. |
| HttpVersion | De HTTP-versie die de viewer heeft opgegeven in de aanvraag. |
| RequestUri | De URI van de ontvangen aanvraag. Dit veld is een volledig schema, poort, domein, pad en query teken reeks |
| HostName | De hostnaam in de aanvraag van de client. Als u aangepaste domeinen inschakelt en een Joker teken domein (*. contoso.com) hebt, is de hostnaam a.contoso.com. Als u een Azure front-deur domein (contoso.azurefd.net) gebruikt, wordt de hostnaam contoso.azurefd.net. |
| RequestBytes | De grootte van het HTTP-aanvraag bericht in bytes, met inbegrip van de aanvraag headers en de hoofd tekst van de aanvraag. Het aantal gegevens bytes dat de viewer in de aanvraag heeft opgenomen, inclusief kopteksten. |
| ResponseBytes | Bytes dat als reactie door de back-endserver wordt verzonden. |
| User agent | Het browser type dat door de client wordt gebruikt. |
| ClientIp | Het IP-adres van de client die de oorspronkelijke aanvraag heeft ingediend. Als er een X-doorgestuurd is voor de header in de aanvraag, wordt het client-IP-adres uit hetzelfde gekozen. |
| SocketIp | Het IP-adres van de directe verbinding met de AFD-rand. Als de client een HTTP-proxy of een load balancer gebruikt voor het verzenden van de aanvraag, is de waarde van SocketIp het IP-adres van de proxy of load balancer. |
| Latentie | De tijds duur van de time-out-server voor de AFD-rand ontvangt de aanvraag van een client naar de tijd dat AFD de laatste byte van de reactie op de client verzendt, in milliseconden. In dit veld wordt geen rekening gehouden met netwerk latentie en TCP-buffering. |
| RequestProtocol | Het protocol dat de client heeft opgegeven in de aanvraag: HTTP, HTTPS. |
| Exemplaar | De TLS/SSL-protocol versie die wordt gebruikt door de aanvraag of null als er geen versleuteling is. Mogelijke waarden zijn: SSLv3, TLSv1, TLSv 1.1, TLSv 1.2 |
| SecurityCipher | Als de waarde voor het aanvraag protocol HTTPS is, wordt in dit veld de TLS/SSL-code aangegeven die door de client en AFD wordt overeengekomen voor versleuteling. |
| Eindpunt | De domein naam van het AFD-eind punt, bijvoorbeeld contoso.z01.azurefd.net |
| Http status code | De HTTP-status code die wordt geretourneerd door AFD. |
| Keuzemenu | De rand pop, die op de aanvraag van de gebruiker reageerde.  |
| Cache status | Geeft de status code van de manier waarop de aanvraag wordt verwerkt door de CDN-service wanneer deze in de cache wordt geplaatst. Mogelijke waarden zijn: de HTTP-aanvraag is verzonden vanuit de POP-cache van de AFD-rand. <br> **Mis**: de HTTP-aanvraag is verzonden vanaf de oorsprong. <br/> **PARTIAL_HIT**: een aantal van de bytes van een aanvraag is afkomstig van de pop-cache van de afd Edge, terwijl enkele van de bytes afkomstig zijn van de oorsprong voor scenario's voor het segmenteren van objecten. <br> **CACHE_NOCONFIG**: door sturen van aanvragen zonder caching-instellingen, inclusief het scenario overs Laan. <br/> **PRIVATE_NOSTORE**: er is geen cache geconfigureerd in de cache-instellingen door klanten. <br> **REMOTE_HIT**: de aanvraag is bediend door de cache van het bovenliggende knoop punt. <br/> **N.v.t.**:* *-aanvraag die is geweigerd door de ondertekende URL en de ingestelde regels. |
| MatchedRulesSetName | De namen van de regels die zijn verwerkt. |
| RouteName | De naam van de route die de aanvraag heeft gevonden. |
| ClientPort | De IP-poort van de client die de aanvraag heeft ingediend. |
| Verwijzende site | De URL van de site waarvan de aanvraag afkomstig is. |
| TimetoFirstByte | De tijds duur in milliseconden van AFD ontvangt de aanvraag naar het tijdstip waarop de eerste byte wordt verzonden naar de client, zoals gemeten op Azure front-deur. Met deze eigenschap worden de client gegevens niet gemeten. |
| Info | Dit veld bevat gedetailleerde informatie over het fout token voor elke reactie. <br> **Fout**: geeft aan dat er geen fout is gevonden. <br> **CertificateError**: algemene SSL-certificaat fout. <br> **CertificateNameCheckFailed**: de hostnaam in het SSL-certificaat is ongeldig of komt niet overeen. <br> **ClientDisconnected**: de aanvraag is mislukt vanwege een client netwerk verbinding. <br> **ClientGeoBlocked**: de client is geblokkeerd vanwege de geografische locatie van het IP-adres. <br> **UnspecifiedClientError**: algemene client fout. <br> **InvalidRequest**: ongeldige aanvraag. Dit kan gebeuren vanwege een ongeldige header, hoofd tekst en URL. <br> **DNSFailure**: DNS-fout. <br> **DNSTimeout**: de DNS-query voor het oplossen van de back-end <br> **DNSNameNotResolved**: de server naam of het adres kan niet worden omgezet. <br> **OriginConnectionAborted**: de verbinding met de oorsprong is abnormaal verbroken. <br> **OriginConnectionError**: algemene verbindings fout van oorsprong. <br> **OriginConnectionRefused**: de verbinding met de oorsprong is niet tot stand gebracht. <br> **OriginError**: fout met algemene oorsprong. <br> **OriginInvalidRequest**: er is een ongeldige aanvraag verzonden naar de oorsprong. <br> **ResponseHeaderTooBig**: de oorsprong retourneert een te hoge reactie header. <br> **OriginInvalidResponse**:* * oorsprong heeft een ongeldig of niet-herkend antwoord geretourneerd. <br> **OriginTimeout**: de time-outperiode voor de oorspronkelijke aanvraag is verlopen. <br> **ResponseHeaderTooBig**: de oorsprong retourneert een te hoge reactie header. <br> **RestrictedIP**: de aanvraag is geblokkeerd vanwege een beperkt IP-adres. <br> **SSLHandshakeError**: kan geen verbinding maken met de oorsprong vanwege een SSL-hand Shake fout. <br> **SSLInvalidRootCA**: de RootCA is ongeldig. <br> **SSLInvalidCipher**: de versleuteling is ongeldig waarvoor de HTTPS-verbinding tot stand is gebracht. <br> **OriginConnectionAborted**: de verbinding met de oorsprong is abnormaal verbroken. <br> **OriginConnectionRefused**: de verbinding met de oorsprong is niet tot stand gebracht. <br> **UnspecifiedError**: er is een fout opgetreden die niet overeenkomt met de fouten in de tabel. |
| OriginURL | De volledige URL van de oorsprong waar aanvragen worden verzonden. Samengesteld uit het schema, de host-header, de poort, het pad en de query reeks. <br> **URL herschrijven**: als er een regel voor het herschrijven van een URL in de regel is, verwijst pad naar het herschreven pad. <br> **Cache op Edge-pop** Als het een cache-treffer op de Edge-POP is, is de oorsprong N/A. <br> **Grote aanvraag** Als de aangevraagde inhoud groot is en er meerdere gesegmenteerde aanvragen worden teruggestuurd naar de oorsprong, komt dit veld overeen met de eerste aanvraag voor de oorsprong. Zie object Chunking voor meer informatie. |
| OriginIP | Het IP-adres van de oorsprong dat de aanvraag heeft verzonden. <br> **Cache treffers op Edge-pop** Als het een cache-treffer op de Edge-POP is, is de oorsprong N/A. <br> **Grote aanvraag** Als de aangevraagde inhoud groot is en er meerdere gesegmenteerde aanvragen worden teruggestuurd naar de oorsprong, komt dit veld overeen met de eerste aanvraag voor de oorsprong. Zie object Chunking voor meer informatie. |
| Oorsprong| De volledige DNS-naam (hostnaam in oorspronkelijke URL) naar de oorsprong. <br> **Cache treffers op Edge-pop** Als het een cache-treffer op de Edge-POP is, is de oorsprong N/A. <br> **Grote aanvraag** Als de aangevraagde inhoud groot is en er meerdere gesegmenteerde aanvragen worden teruggestuurd naar de oorsprong, komt dit veld overeen met de eerste aanvraag voor de oorsprong. Zie object Chunking voor meer informatie. |

## <a name="health-probe-log"></a>Status test logboek

Health probe-logboeken bieden logboek registratie voor elke mislukte test om u te helpen bij het vaststellen van uw oorsprong.De logboeken bevatten informatie die u kunt gebruiken om de oorspronkelijke service terug te zetten. Dit logboek kan nuttig zijn voor de volgende scenario's:

* Er is een Azure front-deur verkeer verzonden naar een aantal oorsprong. Bijvoorbeeld slechts drie van de vier oorsprongen die verkeer ontvangen. U wilt weten of de herkomst tests ontvangt en als dit niet het geval is.  

* U hebt vastgesteld dat de status% van de oorsprong lager is dan verwacht en wilt weten welke oorsprong is mislukt en wat de oorzaak van het probleem is.

### <a name="health-probe-log-properties"></a>Eigenschappen van het status test logboek

Elk Health probe-logboek heeft het volgende schema.

| Eigenschap | Beschrijving |
| --- | --- |
| HealthProbeId  | Een unieke ID voor het identificeren van de aanvraag. |
| Tijd | Voltooiings tijd test |
| HttpMethod | HTTP-methode die wordt gebruikt door de Health probe-aanvraag. De waarden zijn GET en HEAD, op basis van de configuraties van Health probe. |
| Resultaat | Status van status test naar herkomst, waarde is inclusief geslaagd en andere fout tekst. |
| Http status code  | De HTTP-status code die is geretourneerd door de oorsprong. |
| ProbeURL (doel) | De volledige URL van de oorsprong waar aanvragen worden verzonden. Samengesteld uit het schema, de host-header, het pad en de query reeks. |
| Oorsprong  | De oorsprong waar aanvragen worden verzonden. Dit veld helpt de oorsprong van belang te vinden als de oorsprong is geconfigureerd voor FDQN. |
| POP | De rand van de pop, die de test aanvraag heeft verzonden. |
| IP-adres van bron | IP van doel oorsprong. Dit veld is handig voor het vinden van belang rijke oorsprong als u de oorsprong configureert met behulp van FDQN. |
| TotolaLatency | De tijd van de AFDX-Edge stuurt de aanvraag naar de oorspronkelijke tijd naar de herkomst van de AFDX. |
| ConnectionLatency| De duur van de tijd die is besteed aan het instellen van de TCP-verbinding voor het verzenden van de HTTP-test aanvraag naar de oorsprong. | 
| DNSResolution-latentie | De duur van de DNS-omzetting als de oorsprong is geconfigureerd als een FDQN in plaats van een IP-adres. N.v.t. als de oorsprong is geconfigureerd voor IP. |

### <a name="health-probe-log-sample-in-json"></a>Voor beeld van status test logboek in JSON

`{ "records": [ { "time": "2021-02-02T07:15:37.3640748Z",
      "resourceId": "/SUBSCRIPTIONS/27CAFCA8-B9A4-4264-B399-45D0C9CCA1AB/RESOURCEGROUPS/AFDXPRIVATEPREVIEW/PROVIDERS/MICROSOFT.CDN/PROFILES/AFDXPRIVATEPREVIEW-JESSIE",
      "category": "FrontDoorHealthProbeLog",
      "operationName": "Microsoft.Cdn/Profiles/FrontDoorHealthProbeLog/Write",
      "properties": { "healthProbeId": "9642AEA07BA64675A0A7AD214ACF746E",
        "POP": "MAA",
        "httpVerb": "HEAD",
        "result": "OriginError",
        "httpStatusCode": "400",
        "probeURL": "http://afdxprivatepreview.blob.core.windows.net:80/",
        "originName": "afdxprivatepreview.blob.core.windows.net",
        "originIP": "52.239.224.228:80",
        "totalLatencyMilliseconds": "141",
        "connectionLatencyMilliseconds": "68",
        "DNSLatencyMicroseconds": "1814" } } ]
} `

## <a name="activity-logs"></a>Activiteitenlogboeken

Activiteiten logboeken bevatten informatie over de bewerkingen die worden uitgevoerd op de standaard/Premium van Azure voor de voor deur. De logboeken bevatten details over wat, wie en wanneer een schrijf bewerking is uitgevoerd op de Azure front-deur. 

> [!NOTE]
> Activiteiten logboeken bevatten geen GET-bewerkingen. Ze bevatten ook geen bewerkingen die u uitvoert met behulp van de Azure Portal of de oorspronkelijke beheer-API. 

U krijgt toegang tot activiteiten Logboeken in uw voor deur of in alle logboeken van uw Azure-resources in Azure Monitor.

Activiteitenlogboeken weergeven:

1. Selecteer uw front deur-profiel.

1. Selecteer **activiteiten logboek.**

1. Kies een filter bereik en selecteer vervolgens **Toep assen**.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [Azure front-deur Standard/Premium-rapporten (preview)](how-to-reports.md).
- Meer informatie over de [metrische gegevens voor de realtime-controle van Azure front deur Standard/Premium (preview)](how-to-monitor-metrics.md).
