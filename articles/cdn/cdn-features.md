---
title: Productfuncties van Azure Content Delivery Network (CDN) vergelijken
description: Meer informatie over de functies waarvoor elk product van Azure Content Delivery Network (CDN) ondersteuning biedt.
services: cdn
documentationcenter: ''
author: asudbring
ms.service: azure-cdn
ms.topic: overview
ms.date: 11/15/2019
ms.author: allensu
ms.custom: mvc
ms.openlocfilehash: 8dbf3b5de4b908eb831158b73b2b17472bc4895e
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/21/2020
ms.locfileid: "95020960"
---
# <a name="what-are-the-comparisons-between-azure-cdn-product-features"></a>Wat zijn de vergelijkingen tussen Azure CDN-productfuncties?

Azure Content Delivery Network (CDN) bevat vier producten: 

* **Azure CDN Standard van Microsoft**
* **Azure CDN Standard van Akamai**
* **Azure CDN Standard van Verizon**
* **Azure CDN Premium van Verizon**. 

De volgende tabel vergelijkt de functies die beschikbaar zijn voor elk product.

| **Prestatiefuncties en -optimalisatie** | **Standard Microsoft** | **Standard Akamai** | **Standard Verizon** | **Premium Verizon** |
| --- | --- | --- | --- | --- |
| [Dynamische siteversnelling](./cdn-dynamic-site-acceleration.md)  | Aangeboden via [Azure Front Door Service](../frontdoor/front-door-overview.md) | **&#x2713;**  | **&#x2713;** | **&#x2713;** |
| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[Dynamische siteversnelling - adaptieve afbeeldingscompressie](./cdn-dynamic-site-acceleration.md#adaptive-image-compression-azure-cdn-from-akamai-only)  |  | **&#x2713;**  |  |  |
| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[Dynamische siteversnelling - vooraf ophalen van objecten](./cdn-dynamic-site-acceleration.md#object-prefetch-azure-cdn-from-akamai-only)  |  | **&#x2713;**  |  |  |
| [Optimalisatie voor algemene webweergave](./cdn-optimization-overview.md#general-web-delivery)  | **&#x2713;** | **&#x2713;** , Selecteer dit optimalisatietype als de gemiddelde bestandsgrootte kleiner is dan 10 MB  | **&#x2713;** |  **&#x2713;** |
| [Optimalisatie van videostreaming](./cdn-media-streaming-optimization.md)  | via algemene webweergave | **&#x2713;**  | via algemene webweergave |  via algemene webweergave |
| [Optimalisatie van grote bestanden](./cdn-large-file-optimization.md)  | via algemene webweergave | **&#x2713;** , Selecteer dit optimalisatietype als de gemiddelde bestandsgrootte groter is dan 10 MB   | via algemene webweergave |  via algemene webweergave |
| Optimalisatietype wijzigen | |**&#x2713;** | | |
| Poort van oorsprong |Alle TCP-poorten |[Toegestane poorten van oorsprong](/previous-versions/azure/mt757337(v%3Dazure.100)#allowed-origin-ports) |Alle TCP-poorten |Alle TCP-poorten |
| [Global server load balancing (GSLB)](../traffic-manager/traffic-manager-load-balancing-azure.md)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Snel leegmaken](cdn-purge-endpoint.md)  | **&#x2713;** |**&#x2713;** , Leegmaken en leegmaken met jokertekens worden momenteel niet ondersteund door Azure CDN van Akamai |**&#x2713;** |**&#x2713;** |
| [Vooraf laden van assets](cdn-preload-endpoint.md)  |  | |**&#x2713;** |**&#x2713;** |
| Instellingen voor cache/koptekst (met behulp van [regels voor opslaan in cache](cdn-caching-rules.md))  |**&#x2713;** met behulp van [standaardregelengine](cdn-standard-rules-engine.md)  |**&#x2713;** |**&#x2713;** | |
| Aanpasbare, op regels gebaseerde engine voor contentlevering |**&#x2713;** met behulp van [standaardregelengine](cdn-standard-rules-engine.md)  | | |**&#x2713;** met behulp van [regelengine](./cdn-verizon-premium-rules-engine.md) |
| Cache-/headerinstellingen  |**&#x2713;** met behulp van [standaardregelengine](cdn-standard-rules-engine.md) | | |**&#x2713;** met behulp van [Premium-regelengine](./cdn-verizon-premium-rules-engine.md) |
| URL omleiden/opnieuw schrijven |**&#x2713;** met behulp van [standaardregelengine](cdn-standard-rules-engine.md)  | | |**&#x2713;** met behulp van [Premium-regelengine](./cdn-verizon-premium-rules-engine.md) |
| Regels voor mobiele apparaten  |**&#x2713;** met behulp van [standaardregelengine](cdn-standard-rules-engine.md) | | |**&#x2713;** met behulp van [Premium-regelengine](./cdn-verizon-premium-rules-engine.md) |
| [Queryreeksen opslaan in cache](cdn-query-string.md)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| IPv4/IPv6 dual stack | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Ondersteuning voor HTTP/2](cdn-http2.md)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
||||
 **Beveiliging** | **Standard Microsoft** | **Standard Akamai** | **Standard Verizon** | **Premium Verizon** | 
| HTTPS-ondersteuning met CDN-eindpunt | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [HTTPS voor aangepaste domeinen](cdn-custom-ssl.md)  | **&#x2713;** | **&#x2713;** , Vereist directe CNAME om in te schakelen |**&#x2713;** |**&#x2713;** |
| [Ondersteuning voor aangepaste domeinnamen](cdn-map-content-to-custom-domain.md)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Geo-filteren](cdn-restrict-access-by-country.md)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Tokenverificatie](cdn-token-auth.md)  |  |  |  |**&#x2713;**| 
| [DDOS-beveiliging](https://www.us-cert.gov/ncas/tips/ST04-015)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Uw eigen certificaat gebruiken](cdn-custom-ssl.md?tabs=option-2-enable-https-with-your-own-certificate#tlsssl-certificates) |**&#x2713;** |  | **&#x2713;** | **&#x2713;** |
| Ondersteunde TLS-versies | TLS 1.2, TLS 1.0/1.1 - [configureerbaar](/rest/api/cdn/customdomains/enablecustomhttps#usermanagedhttpsparameters) | TLS 1.2 | TLS 1.2 | TLS 1.2 |
||||
| **Analyse en rapportage** | **Standard Microsoft** | **Standard Akamai** | **Standard Verizon** | **Premium Verizon** | 
| [Diagnostische logboeken in Azure](cdn-azure-diagnostic-logs.md)  | **&#x2713;** | **&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Verizon-kernrapporten](cdn-analyze-usage-patterns.md)  |  | |**&#x2713;** |**&#x2713;** |
| [Aangepaste Verizon-rapporten](cdn-verizon-custom-reports.md)  |  | |**&#x2713;** |**&#x2713;** |
| [Geavanceerde HTTP-rapporten](cdn-advanced-http-reports.md)  |  | | |**&#x2713;** |
| [Realtime statistieken](cdn-real-time-stats.md)  |  | | |**&#x2713;** |
| [Prestaties van edge-knooppunt](cdn-edge-performance.md)  |  | | |**&#x2713;** |
| [Realtime waarschuwingen](cdn-real-time-alerts.md)  |  | | |**&#x2713;** |
||||
| **Gebruiksgemak** | **Standard Microsoft** | **Standard Akamai** | **Standard Verizon** | **Premium Verizon** | 
| Eenvoudige integratie met Azure-services, zoals [Storage](cdn-create-a-storage-account-with-cdn.md), [Web Apps](cdn-add-to-web-app.md) en [Media Services](../media-services/previous/media-services-portal-manage-streaming-endpoints.md)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| Beheer via [REST API](/rest/api/cdn/), [.NET](cdn-app-dev-net.md), [Node.js](cdn-app-dev-node.md) of [PowerShell](cdn-manage-powershell.md)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Compressie-MIME-typen](./cdn-improve-performance.md)  |Alleen standaard |Configureerbaar |Configureerbaar  |Configureerbaar  |
| Compressie-coderingen  |gzip, brotli |gzip |gzip, deflate, bzip2, brotli  |gzip, deflate, bzip2, brotli  |

## <a name="migration"></a>Migratie

Voor informatie over het migreren van een **Azure CDN Standard van Verizon**-profiel naar **Azure CDN Premium van Verizon**, raadpleegt u [Een Azure CDN-profiel migreren van Standard Verizon naar Premium Verizon](cdn-migrate.md). 

> [!NOTE]
> Er bestaat een upgradepad van Standard Verizon naar Premium Verizon, maar er is op dit moment geen omzettingsmechanisme beschikbaar voor andere producten.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [Azure CDN](cdn-overview.md).
