---
author: rothja
ms.service: app-service
ms.topic: include
ms.date: 03/04/2020
ms.author: jroth
ms.openlocfilehash: 6265a7cde8e276570409238cadfd5ebc777b4527
ms.sourcegitcommit: 66479d7e55449b78ee587df14babb6321f7d1757
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/15/2020
ms.locfileid: "97532003"
---
| Resource | Gratis | Gedeeld | Basic | Standard | Premium (v1-v3) | Geïsoleerd </th> |
| --- | --- | --- | --- | --- | --- | --- |
| [Web-, mobiele of API-apps](https://azure.microsoft.com/services/app-service/) per [Azure App Service-abonnement](../articles/app-service/overview-hosting-plans.md)<sup>1</sup> |10 |100 |Onbeperkt<sup>2</sup> |Onbeperkt<sup>2</sup> |Onbeperkt<sup>2</sup> |Onbeperkt<sup>2</sup>|
| [App Service-plan](../articles/app-service/overview-hosting-plans.md) |10 per regio |10 per resourcegroep |100 per resourcegroep |100 per resourcegroep |100 per resourcegroep |100 per resourcegroep|
| Type rekenproces |Gedeeld |Gedeeld |Toegewezen<sup>3</sup> |Toegewezen<sup>3</sup> |Toegewezen<sup>3</sup></p> |Toegewezen<sup>3</sup>|
| [Uitbreiden](../articles/app-service/manage-scale-up.md) (maximum aantal exemplaren) |1 gedeeld |1 gedeeld |3 toegewezen<sup>3</sup> |10 toegewezen<sup>3</sup> | 20 toegewezen voor v1 en v2; 30 toegewezen voor v3.<sup>3</sup>|100 toegewezen<sup>4</sup>|
| Opslag<sup>5</sup> |1 GB<sup>5</sup> |1 GB<sup>5</sup> |10 GB<sup>5</sup> |50 GB<sup>5</sup> |250 GB<sup>5</sup> <br/><br/> Voor meer dan 250 GB moet u een ondersteuningsaanvraag indienen. |1 TB<sup>5</sup> <br/><br/> De maximaal beschikbare opslag is 999 GB. |
| CPU-tijd (5 minuten)<sup>6</sup> |3 minuten |3 minuten |Onbeperkt, tegen [standaardtarieven](https://azure.microsoft.com/pricing/details/app-service/)</a> |Onbeperkt, tegen [standaardtarieven](https://azure.microsoft.com/pricing/details/app-service/)</a> |Onbeperkt, tegen [standaardtarieven](https://azure.microsoft.com/pricing/details/app-service/)</a> |Onbeperkt, tegen [standaardtarieven](https://azure.microsoft.com/pricing/details/app-service/)</a>|
| CPU-tijd (dag)<sup>6</sup> |60 minuten |240 minuten |Onbeperkt, tegen [standaardtarieven](https://azure.microsoft.com/pricing/details/app-service/)</a> |Onbeperkt, tegen [standaardtarieven](https://azure.microsoft.com/pricing/details/app-service/)</a> |Onbeperkt, tegen [standaardtarieven](https://azure.microsoft.com/pricing/details/app-service/)</a> |Onbeperkt, tegen [standaardtarieven](https://azure.microsoft.com/pricing/details/app-service/)</a> |
| Geheugen (1 uur) |1024 MB per App Service-abonnement |1024 MB per app |N.v.t. |N.v.t. |N.v.t. |N.v.t. |
| Bandbreedte |165 MB |Onbeperkt, [gegevensoverdrachttarieven](https://azure.microsoft.com/pricing/details/data-transfers/) van toepassing |Onbeperkt, [gegevensoverdrachttarieven](https://azure.microsoft.com/pricing/details/data-transfers/) van toepassing |Onbeperkt, [gegevensoverdrachttarieven](https://azure.microsoft.com/pricing/details/data-transfers/) van toepassing |Onbeperkt, [gegevensoverdrachttarieven](https://azure.microsoft.com/pricing/details/data-transfers/) van toepassing |Onbeperkt, [gegevensoverdrachttarieven](https://azure.microsoft.com/pricing/details/data-transfers/) van toepassing |
| Toepassingsarchitectuur |32-bits |32-bits |32-bits/64-bits |32-bits/64-bits |32-bits/64-bits |32-bits/64-bits |
| Websockets per exemplaar<sup>7</sup> |5 |35 |350 |Onbeperkt |Onbeperkt |Onbeperkt |
| IP-verbindingen | 600 | 600 | Is afhankelijk van de exemplaargrootte<sup>8</sup> | Is afhankelijk van de exemplaargrootte<sup>8</sup> | Is afhankelijk van de exemplaargrootte<sup>8</sup> | 16.000 |
| Gelijktijdige [verbindingen van foutopsporingsprogramma](../articles/app-service/troubleshoot-dotnet-visual-studio.md) per toepassing |1 |1 |1 |5 |5 |5 |
| App Service-certificaten per abonnement<sup>9</sup>| Niet ondersteund | Niet ondersteund |10 |10 |10 |10 |
| Aangepaste domeinen per app</a> |0 (alleen subdomein azurewebsites.net)|500 |500 |500 |500 |500 |
| Aangepast domein voor [SSL-ondersteuning](../articles/app-service/configure-ssl-certificate.md) |Niet ondersteund, jokertekencertificaat voor \*. azurewebsites.net is standaard beschikbaar|Niet ondersteund, jokertekencertificaat voor \*. azurewebsites.net is standaard beschikbaar|Onbeperkte SNI SSL-verbindingen |Onbeperkte SNI SSL- en 1 IP SSL-verbindingen inbegrepen |Onbeperkte SNI SSL- en 1 IP SSL-verbindingen inbegrepen | Onbeperkte SNI SSL- en 1 IP SSL-verbindingen inbegrepen|
| Hybride verbindingen | | | 5 per abonnement | 25 per abonnement | 200 per app | 200 per app |
| [Integratie van virtueel netwerk](../articles/app-service/web-sites-integrate-with-vnet.md) | | |   |  X |  X  |  X  |
| Geïntegreerde load balancer | |X |X |X |X |X<sup>10</sup> |
| [Toegangsbeperkingen](../articles/app-service/networking-features.md#access-restrictions) | 512 regels per app | 512 regels per app | 512 regels per app | 512 regels per app | 512 regels per app | 512 regels per app |
| [Altijd aan](../articles/app-service/configure-common.md) | | |X |X |X |X |
| [Geplande back-ups](../articles/app-service/manage-backup.md) | | | | Geplande back-ups om de 2 uur, een maximum van 12 back-ups per dag (handmatig + gepland) | Geplande back-ups elk uur, een maximum van 50 back-ups per dag (handmatig + gepland) | Geplande back-ups elk uur, een maximum van 50 back-ups per dag (handmatig + gepland) |
| [Automatisch schalen](../articles/app-service/manage-scale-up.md) | | | |X |X |X |
| [WebJobs](../articles/app-service/webjobs-create.md)<sup>11</sup> |X |X |X |X |X |X |
| [Eindpuntbewaking](../articles/app-service/web-sites-monitor.md) | | |X |X |X |X |
| [Faseringssleuven](../articles/app-service/deploy-staging-slots.md) per app| | | |5 |20 |20 |
| [Testen in productie](../articles/app-service/deploy-staging-slots.md#route-traffic)| | | |X |X |X |
| [Diagnostische logboeken](../articles/app-service/troubleshoot-diagnostic-logs.md) | X | X | X | X | X | X |
| Kudu | X | X | X | X | X | X |
| [Verificatie en autorisatie](../articles/app-service/overview-authentication-authorization.md) | X | X | X | X | X | X |
| [Door App Service beheerde certificaten (openbare preview)](https://azure.microsoft.com/updates/secure-your-custom-domains-at-no-cost-with-app-service-managed-certificates-preview/)<sup>12</sup> | |  | X | X | X | X |
| SLA | |  |99.95%|99.95%|99.95%|99.95%|  

<sup>1</sup>Apps en opslagquota's gelden per App Service-abonnement tenzij anders vermeld.  
<sup>2</sup>Het werkelijke aantal apps dat u kunt hosten op deze machines, is afhankelijk van de activiteit van de apps, de grootte van het machine-exemplaar en het bijbehorende resourcegebruik.  
<sup>3</sup>Toegewezen exemplaren kunnen verschillende groottes hebben. Zie [Prijzen van App Service](https://azure.microsoft.com/pricing/details/app-service/) voor meer informatie.  
<sup>4</sup>Meer zijn op aanvraag toegestaan.  
<sup>5</sup>De opslaglimiet is de totale inhoudsgrootte voor alle apps in hetzelfde App Service-abonnement. De totale inhoudsgrootte van alle apps in alle App Service-abonnementen in één resourcegroep en regio mag niet meer dan 500 GB bedragen.  
<sup>6</sup>Deze resources worden beperkt door fysieke resources op de toegewezen exemplaren (de grootte van het exemplaar en het aantal exemplaren).  
<sup>7</sup>Als u een app in de basislaag naar twee exemplaren wilt schalen, hebt u 350 gelijktijdige verbindingen voor elk van de twee exemplaren. Voor de standaardlaag en hoger zijn er geen theoretische limieten voor het aantal websockets, maar het aantal websockets kan door andere factoren worden beperkt. Bijvoorbeeld maximaal aantal toegestane gelijktijdige aanvragen (gedefinieerd door `maxConcurrentRequestsPerCpu`): 7500 per kleine VM, 15.000 per gemiddelde VM (7500 x 2 kernen) en 75.000 per grote VM (18.750 x 4 kernen).  
<sup>8</sup>Het maximum aantal IP-verbindingen geldt per exemplaar en is afhankelijk van de grootte van het exemplaar: 1,920 per B1/S1/P1V3 instance, 3,968 per B2/S2/P2V3 instance, 8,064 per B3/S3/P3V3 instance.  
<sup>9</sup>De quotumlimiet per abonnement van het App Service-certificaat kan worden verhoogd via een ondersteuningsaanvraag tot een maximumlimiet van 200.  
<sup>10</sup>App Service-geïsoleerde SKU's kunnen intern worden verdeeld (ILB) met Azure Load Balancer, dus er is geen openbare verbinding via internet. Als gevolg hiervan moeten sommige functies van een ILB-geïsoleerde App Service worden gebruikt vanaf machines met rechtstreekse toegang tot het ILB-netwerkeindpunt.  
<sup>11</sup>Voer aangepaste uitvoerbare bestanden en/of scripts op aanvraag, op basis van een schema of continu als een achtergrondtaak binnen uw App Service-exemplaar uit. AlwaysOn is vereist voor de continue uitvoering van webjobs. Er is geen vooraf gedefinieerde limiet voor het aantal webjobs dat kan worden uitgevoerd in een App Service-exemplaar. Er zijn praktische limieten die afhankelijk zijn van wat de toepassingscode probeert te doen.

<sup>12</sup>Domeinen zonder voorvoegsel worden niet ondersteund. Alleen standaardcertificaten verlenen (jokertekencertificaten zijn niet beschikbaar). Beperkt tot slechts één gratis certificaat per aangepast domein.
