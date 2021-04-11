---
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 09/18/2019
ms.author: cephalin
ms.openlocfilehash: ed53cb60f6fb3bca93900941a8a6a53bed99d779
ms.sourcegitcommit: d40ffda6ef9463bb75835754cabe84e3da24aab5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/07/2021
ms.locfileid: "107073582"
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
* Paden toewijzen
* Instellingen die eindigen op het achtervoegsel _EXTENSION_VERSION

> [!NOTE]
> Als u deze instellingen wilt verwisselen, voegt u de app-instelling `WEBSITE_OVERRIDE_PRESERVE_DEFAULT_STICKY_SLOT_SETTINGS` in elke sleuf van de app toe en stelt u de waarde in op `0` of `false` . Deze instellingen zijn allemaal verwisselbaar of helemaal niet. U kunt niet alleen enkele instellingen wisselen en niet de andere wijzigen.

> Bepaalde app-instellingen die van toepassing zijn op niet-Verwissel bare instellingen, worden ook niet omgewisseld. Omdat de diagnostische instellingen bijvoorbeeld niet worden gewisseld, worden gerelateerde app-instellingen, zoals `WEBSITE_HTTPLOGGING_RETENTION_DAYS` en `DIAGNOSTICS_AZUREBLOBRETENTIONDAYS` ook niet omgewisseld, zelfs niet weer gegeven als sleuf instellingen.
>
