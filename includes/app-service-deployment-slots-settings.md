---
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 09/18/2019
ms.author: cephalin
ms.openlocfilehash: 70ab0b5c70e94c4784a7ab260b3304107bcb1175
ms.sourcegitcommit: 5db975ced62cd095be587d99da01949222fc69a3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/10/2020
ms.locfileid: "97096296"
---
Wanneer u de configuratie kloont vanuit een andere implementatie site, kan de gekloonde configuratie bewerkbaar zijn. Sommige configuratie-elementen volgen de inhoud in een wissel (niet sleuf specifiek), terwijl andere configuratie-elementen zich in dezelfde sleuf bevinden na een wissel (sleuf specifiek). In de volgende lijsten ziet u de instellingen die veranderen wanneer u sleuven verwisselt.

**Instellingen die worden omgewisseld**:

* Algemene instellingen, zoals Framework versie, 32/64 bits, Web Sockets
* App-instellingen (kan worden geconfigureerd om naar een sleuf te worden gestickt)
* Verbindings reeksen (kan zodanig worden geconfigureerd dat ze naar een sleuf worden gestickt)
* Handlertoewijzing
* Openbare certificaten
* Inhoud van webjobs
* Hybride verbindingen *
* Service-eind punten *
* Azure Content Delivery Network *

Functies die zijn gemarkeerd met een sterretje (*) zijn gepland om ongewisseld te worden. 

**Instellingen die niet zijn gewisseld**:

* Eind punten publiceren
* Aangepaste domeinnamen
* Niet-open bare certificaten en TLS/SSL-instellingen
* Schaal instellingen
* Webjobs-planners
* IP-beperkingen
* AlwaysOn
* Diagnostische instellingen
* CORS (Cross-Origin Resource Sharing, cross-origin-resource delen)
* Integratie van virtueel netwerk

> [!NOTE]
> Bepaalde app-instellingen die van toepassing zijn op niet-Verwissel bare instellingen, worden ook niet omgewisseld. Omdat de diagnostische instellingen bijvoorbeeld niet worden gewisseld, worden gerelateerde app-instellingen, zoals `WEBSITE_HTTPLOGGING_RETENTION_DAYS` en `DIAGNOSTICS_AZUREBLOBRETENTIONDAYS` ook niet omgewisseld, zelfs niet weer gegeven als sleuf instellingen.
>
