---
title: bestand opnemen
description: bestand opnemen
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: include
ms.date: 02/18/2021
ms.author: duau
ms.custom: include file
ms.openlocfilehash: 53d837883daefddd5fa3f0f543eae1d116a5e86a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "101102934"
---
| Resource | Limiet |
| --- | --- |
| Azure Front Door-resources per abonnement | 100 |
| Front-end hosts, inclusief aangepaste domeinen per resource | 500 |
| Regels voor doorsturen per resource | 500 |
| Back-end pools per resource | 50 |
| Back-ends per back-end pool | 100 |
| Te vergelijken padpatronen voor een regel voor doorsturen | 25 |
| URL's in één aanroep voor het opschonen van een cache | 100 |
| Aangepaste regels voor Web Application Firewall per beleid | 100 |
| Web Application Firewall-beleid per abonnement | 100 |
| Match-voorwaarden per aangepaste regel voor Web Application Firewall | 10 |
| IP-adresbereiken per overeenkomstvoorwaarde voor Web Application Firewall | 600 |
| Tekenreeksovereenkomstwaarden per match-voorwaarde voor Web Application Firewall | 10 |
| Lengte tekenreeksovereenkomstwaarde voor Web Application Firewall | 256 |
| Naamlengte POST-hoofdtekstparameter voor Web Application Firewall | 256 |
| Naamlengte HTTP-header voor Web Application Firewall | 256 |
| Cookienaamlengte voor Web Application Firewall | 256 |
| Geïnspecteerde grootte HTTP-aanvraag voor Web Application Firewall | 128 kB |
| Lengte van aangepaste antwoordtekst voor Web Application Firewall | 2 KB |

### <a name="azure-front-door-standardpremium-preview-service-limits"></a>Azure front deur Standard/Premium-Service limieten (preview)

*** *Maxi maal **500** totaal aantal standaard-en Premium-profielen per abonnement.*

| Resource | Standaard SKU-limiet | Premium-SKU-limiet |
| --- | --- | --- |
| Maximum eindpunt per profiel  | 10 | 25 |
| Maxi maal aangepast domein per profiel | 100 | 200 |
| Maximale oorspronkelijke groep per profiel | 100 | 200 |
| Maximum aantal geheimen per profiel | 100 | 200 |
| Maximum beveiligings beleid per profiel | 100 | 200 |
| Maximum aantal regelset per profiel | 100 | 200 |
| Maximum regels per regel reeks | 100 | 100 |
| Maximum herkomst per oorsprongs groep | 50 | 50 |
| Maximum aantal routes per eind punt | 100 | 200 |
| Match-voorwaarden per aangepaste regel voor Web Application Firewall | 10 | 10 |
| IP-adresbereiken per overeenkomstvoorwaarde voor Web Application Firewall | 600 | 600 |
| Tekenreeksovereenkomstwaarden per match-voorwaarde voor Web Application Firewall | 10 | 10 |
| Lengte tekenreeksovereenkomstwaarde voor Web Application Firewall | 256 | 256 |
| Naamlengte POST-hoofdtekstparameter voor Web Application Firewall | 256 | 256 |
| Naamlengte HTTP-header voor Web Application Firewall | 256 | 256 |
| Cookienaamlengte voor Web Application Firewall | 256 | 256|
| Geïnspecteerde grootte HTTP-aanvraag voor Web Application Firewall | 128 kB | 128 kB |
| Lengte van aangepaste antwoordtekst voor Web Application Firewall | 2 KB | 2 KB |

### <a name="timeout-values"></a>Time-outwaarden
#### <a name="client-to-front-door"></a>Client naar Front Door
* Front Door heeft een time-out van 61 seconden voor inactieve TCP-verbindingen.

#### <a name="front-door-to-application-back-end"></a>Front Door naar back-end toepassing
* Als het antwoord een chunk-antwoord is, wordt een 200 geretourneerd als of wanneer de eerste chunk wordt ontvangen.
* Nadat de HTTP-aanvraag naar de back-end is doorgestuurd, wacht Front Door 30 seconden op het eerste pakket van de back-end. Vervolgens wordt er een 503-fout naar de client geretourneerd. Deze waarde kan worden geconfigureerd via het veld sendRecvTimeoutSeconds in de API.
    * Voor cache-scenario's is deze time-out niet configureerbaar, en dus wordt er een 504-fout geretourneerd naar de client als een aanvraag wordt gecachet en het meer dan 30 seconden duurt voor het eerste pakket van Front Door of van de back-end. 
* Nadat het eerste pakket is ontvangen van de back-end, wacht Front Door 30 seconden in een time-out voor inactiviteit. Vervolgens wordt er een 503-fout naar de client geretourneerd. Deze time-outwaarde kan niet worden geconfigureerd.
* De time-out van Front Door naar de back-end-TCP-sessie is 90 seconden.

### <a name="upload-and-download-data-limit"></a>Gegevenslimiet voor uploaden en downloaden

|  | Met chunk-overdrachtcodering (CTE) | Zonder HTTP-chunking |
| ---- | ------- | ------- |
| **Downloaden** | Er is geen limiet voor de downloadgrootte. | Er is geen limiet voor de downloadgrootte. |
| **Uploaden** |    Er is geen limiet zolang elke CTE-upload kleiner is dan 2 GB. | De grootte mag maximaal 2 GB zijn. |

### <a name="other-limits"></a>Andere limieten
* Maximale URL-grootte: 8.192 bytes. Dit is de maximumlengte van de onbewerkte URL (schema + hostnaam + poort + pad + querytekenreeks van de URL)
* Maximale grootte van de querytekenreeks: 4.096 bytes. Dit is de maximumlengte van de querytekenreeks in bytes.
* Maximale grootte van de HTTP-antwoordheader van de statustest-URL: 4.096 bytes. Dit is de maximale lengte van alle antwoord-headers van statuscontroles. 
