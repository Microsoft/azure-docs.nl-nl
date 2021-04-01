---
title: Verizon-specifieke HTTP-headers voor de engine van Azure CDN-regels | Microsoft Docs
description: In dit artikel wordt beschreven hoe u Verizon-specifieke HTTP-headers gebruikt met Azure CDN regels-engine.
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/16/2018
ms.author: allensu
ms.openlocfilehash: e20f6ce9540d357b61ae2cfdf0e8f96d127dc6c0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "84343214"
---
# <a name="verizon-specific-http-headers-for-azure-cdn-rules-engine"></a>Verizon-specifieke HTTP-headers voor de engine van Azure CDN-regels

Wanneer een HTTP-aanvraag wordt verzonden naar de bron server, kan de POP-server (Point-of-Presence) een of meer gereserveerde headers (of proxy Special headers) toevoegen aan de POP voor **Azure CDN Premium van Verizon** -producten. Deze headers zijn naast de standaard-doorstuur headers ontvangen. Zie [aanvraag velden](https://en.wikipedia.org/wiki/List_of_HTTP_header_fields#Request_fields)voor informatie over standaard aanvraag headers.

Als u wilt voor komen dat een van deze gereserveerde headers wordt toegevoegd aan de POP-aanvraag Azure CDN (Content Delivery Network) op de bron server, moet u een regel maken met de [functie speciale headers proxy](https://docs.vdms.com/cdn/Content/HRE/F/Proxy-Special-Headers.htm) in de regel engine. In deze regel sluit u de koptekst die u wilt verwijderen uit in de standaard lijst met kopteksten in het veld headers. Als u de [functie debug cache response headers](https://docs.vdms.com/cdn/Content/HRE/F/Debug-Cache-Response-Headers.htm)hebt ingeschakeld, moet u de vereiste headers toevoegen `X-EC-Debug` . 

Als u bijvoorbeeld de header wilt verwijderen `Via` , moet het veld headers van de regel de volgende lijst met headers bevatten: *x-doorgestuurd-voor, x-doorgestuurde proto, x-host, x-Midgress, x-gateway-lijst, x-EG-naam, host*. 

![Proxy-regel voor speciale headers](./media/cdn-http-headers/cdn-proxy-special-header-rule.png)

In de volgende tabel worden de headers beschreven die kunnen worden toegevoegd door de Verizon CDN POP in de aanvraag:

Aanvraagheader | Beschrijving | Voorbeeld
---------------|-------------|--------
[Kopen](#via-request-header) | Hiermee wordt de POP-server geïdentificeerd die de aanvraag via een proxy naar een originele server heeft doorgestuurd. | HTTP/1.1 ECS (DCA/1A2B)
X-doorgestuurd-voor | Hiermee wordt het IP-adres van de aanvrager aangegeven.| 10.10.10.10
X-doorgestuurd-proto | Geeft het Protocol van de aanvraag aan. | http
X-host | Geeft de hostnaam van de aanvraag aan. | cdn.mydomain.com
X-Midgress | Geeft aan of de aanvraag via een extra CDN-server via een proxy is verzonden. Een voor beeld: een POP server-to-Origin Shield-Server of een POP-server-naar-ADN-Gateway server. <br />Deze header wordt alleen toegevoegd aan de aanvraag wanneer midgress verkeer plaatsvindt. In dit geval wordt de header ingesteld op 1 om aan te geven dat de aanvraag via een extra CDN-server via een proxy is verzonden.| 1
[Host](#host-request-header) | Identificeert de host en de poort waarop de aangevraagde inhoud kan worden gevonden. | marketing.mydomain.com:80
[X-gateway-lijst](#x-gateway-list-request-header) | ADN: identificeert de failoverlijst van ADN Gateway servers die zijn toegewezen aan de oorsprong van een klant. <br />Schild van oorsprong: geeft de set van originele afschermings servers aan die zijn toegewezen aan de oorsprong van een klant. | `icn1,hhp1,hnd1`
X-EG-_&lt; naam &gt;_ | Aanvraag headers die beginnen met *x-EG* (bijvoorbeeld x-EG-tag, [x-EG-debug](cdn-http-debug-headers.md)) zijn gereserveerd voor gebruik door het CDN.| WAF-productie

## <a name="via-request-header"></a>Via aanvraag header
De indeling waarmee de `Via` aanvraag header een pop-server identificeert, wordt opgegeven met de volgende syntaxis:

`Via: Protocol from Platform (POP/ID)` 

De termen die in de syntaxis worden gebruikt, zijn als volgt gedefinieerd:
- Protocol: geeft de versie van het protocol aan (bijvoorbeeld HTTP/1.1) dat is gebruikt om de aanvraag te proxy. 

- Platform: geeft het platform aan waarop de inhoud is aangevraagd. De volgende codes zijn geldig voor dit veld: 

    Code | Platform
    -----|---------
    ECAcc | HTTP-groot
    ECS   | HTTP-klein
    ECD   | Application Delivery Network (ADN)

- POP: Hiermee wordt de [pop](cdn-pop-abbreviations.md) aangegeven waarmee de aanvraag is verwerkt. 

- ID: alleen voor intern gebruik.

### <a name="example-via-request-header"></a>Voor beeld via aanvraag header

`Via: HTTP/1.1 ECD (dca/1A2B)`

## <a name="host-request-header"></a>Header van host-aanvraag
De POP-servers overschrijven de `Host` header wanneer aan de volgende voor waarden wordt voldaan:
- De bron voor de aangevraagde inhoud is een server van de oorsprong van de klant.
- De optie voor de HTTP-host-header van de bijbehorende klant is niet leeg.

De `Host` aanvraag header wordt overschreven om de waarde weer te geven die is gedefinieerd in de optie HTTP-host-header.
Als de optie voor de HTTP-host-header van de klant is ingesteld op leeg, wordt de `Host` aanvraag header die wordt verzonden door de aanvrager doorgestuurd naar de oorspronkelijke server van de klant.

## <a name="x-gateway-list-request-header"></a>X-gateway-aanvraag header
Een POP-server voegt de aanvraag ' X-gateway-List ' toe of overschreven wanneer aan een van de volgende voor waarden wordt voldaan:
- De aanvraag wijst naar het ADN-platform.
- De aanvraag wordt doorgestuurd naar een bron server van de klant die wordt beveiligd door de functie voor het afschermen van de oorsprong.

