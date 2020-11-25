---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 05/04/2020
ms.author: glenga
ms.openlocfilehash: 826fccad043b067ce86d5f56eaebc6ee48b532d1
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96027517"
---
| Resource |[Verbruiksabonnement](../articles/azure-functions/functions-scale.md#consumption-plan)|[Premium-abonnement](../articles/azure-functions/functions-scale.md#premium-plan)|[Toegewezen abonnement](../articles/azure-functions/functions-scale.md#app-service-plan)|[ASE](../articles/app-service/environment/intro.md)| [Kubernetes](../articles/aks/quotas-skus-regions.md) |
| --- | --- | --- | --- | --- | --- |
|Standaard [time-outduur](../articles/azure-functions/functions-scale.md#timeout) (min.) |5 | 30 |30<sup>1</sup> | 30 | 30 |
|Maximale [time-outduur](../articles/azure-functions/functions-scale.md#timeout) (min.) |10 | niet-gebonden<sup>7</sup> | niet-gebonden<sup>2</sup> | niet-gebonden | niet-gebonden |
| Maximumaantal uitgaande verbindingen (per exemplaar) | 600 actief (1.200 totaal) | niet-gebonden | niet-gebonden | niet-gebonden | niet-gebonden |
| Maximale aanvraaggrootte (MB)<sup>3</sup> | 100 | 100 | 100 | 100 | Afhankelijk van cluster |
| Maximale tekenreekslengte voor query's<sup>3</sup> | 4096 | 4096 | 4096 | 4096 | Afhankelijk van cluster |
| Maximale lengte voor aanvraag-URL's<sup>3</sup> | 8192 | 8192 | 8192 | 8192 | Afhankelijk van cluster |
|[ACU](../articles/virtual-machines/acu.md) per exemplaar | 100 | 210-840 | 100-840 | 210-250<sup>8</sup> | [AKS-prijzen](https://azure.microsoft.com/pricing/details/container-service/) |
| Maximaal geheugen (GB per exemplaar) | 1.5 | 3,5-14 | 1,75-14 | 3,5 - 14 | Elk knooppunt wordt ondersteund |
| Functie-apps per abonnement |100 |100 |niet-gebonden<sup>4</sup> | niet-gebonden | niet-gebonden |
| [App Service-abonnementen](../articles/app-service/overview-hosting-plans.md) | 100 per [regio](https://azure.microsoft.com/global-infrastructure/regions/) |100 per resourcegroep |100 per resourcegroep | - | - |
| Opslag<sup>5</sup> |5 TB |250 GB |50-1000 GB | 1 TB | n.v.t. |
| Aangepaste domeinen per app</a> |500<sup>6</sup> |500 |500 | 500 | n.v.t. |
| Aangepast domein voor [SSL-ondersteuning](../articles/app-service/configure-ssl-bindings.md) |niet-gebonden SNI SSL-verbinding inbegrepen | niet-gebonden SNI SSL- en 1 IP SSL-verbindingen inbegrepen |niet-gebonden SNI SSL- en 1 IP SSL-verbindingen inbegrepen | niet-gebonden SNI SSL- en 1 IP SSL-verbindingen inbegrepen | n.v.t. |

<sup>1</sup> De time-out voor de Functions 1.x-runtime in een App Service-abonnement is standaard niet-gebonden.  
<sup>2</sup> Het App Service-abonnement moet zijn ingesteld op [Altijd aan](../articles/azure-functions/functions-scale.md#always-on). Tegen [standaardtarieven](https://azure.microsoft.com/pricing/details/app-service/).  
<sup>3</sup> Deze limieten worden [ingesteld in de host](https://github.com/Azure/azure-functions-host/blob/dev/src/WebJobs.Script.WebHost/web.config).  
<sup>4</sup> Het werkelijke aantal functie-apps dat u kunt hosten, is afhankelijk van de activiteit van de apps, de grootte van het machine-exemplaar en het bijbehorende resourcegebruik.  
<sup>5</sup> De opslaglimiet is de totale inhoudsgrootte in tijdelijke opslag voor alle apps in hetzelfde App Service-abonnement. Verbruiksabonnement maakt gebruik van Azure Files voor tijdelijke opslag.  
<sup>6</sup> Wanneer uw functie-app in een [verbruiksabonnement](../articles/azure-functions/functions-scale.md#consumption-plan) wordt gehost, wordt alleen de optie CNAME ondersteund. Voor functie-apps in een [Premium-abonnement](../articles/azure-functions/functions-scale.md#premium-plan) of een [App Service-abonnement](../articles/azure-functions/functions-scale.md#app-service-plan) kunt u een aangepast domein toewijzen met een CNAME- of A-record.  
<sup>7</sup> Gegarandeerd voor maximaal 60 minuten.  
<sup>8</sup> Werkrollen zijn rollen die klanten-apps hosten. Werkrollen zijn beschikbaar in drie vaste grootten: Eén vCPU/3,5 GB RAM; Twee vCPU/7 GB RAM; Vier vCPU/14 GB RAM.