---
author: vhorne
ms.service: application-gateway
ms.topic: include
ms.date: 03/04/2020
ms.author: victorh
ms.openlocfilehash: 4c7c68bf690097004f2f3310a8d89ce954d7f87f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98612896"
---
| Resource | Limiet | Opmerking |
| --- | --- | --- |
| Azure Application Gateway |1000 per abonnement | |
| Frontend-IP-configuraties |2 |1 openbaar en 1 privé |
| Front-endpoorten |100<sup>1</sup> | |
| Back-endadresgroepen |100<sup>1</sup> | |
| Back-endservers per groep |1200 | |
| HTTP-listeners |200<sup>1</sup> |Beperkt tot 100 actieve listeners die verkeer routeren. Actieve listeners = totaalaantal listeners - inactieve listeners.<br>Als een standaardconfiguratie in een routeringsregel is ingesteld op het instellen om verkeer te routeren (als een listener bijvoorbeeld een back-end-pool en HTTP-instellingen heeft) dan telt dat ook als listener.|
| HTTP-taakverdelingsregels |100<sup>1</sup> | |
| Back-end-HTTP-instellingen |100<sup>1</sup> | |
| Exemplaren per gateway |V1 SKU - 32<br>V2 SKU - 125 | |
| SSL-certificaten |100<sup>1</sup> |1 per HTTP-listener |
| Maximale grootte van SSL-certificaat |V1 SKU - 10 KB<br>V2 SKU - 16 KB| |
| Certificaten voor verificatie |100 | |
| Vertrouwde basiscertificaten |100 | |
| Time-outminimum aanvragen |1 seconde | |
| Time-outmaximum aanvragen |24 uur | |
| Aantal sites |100<sup>1</sup> |1 per HTTP-listener |
| URL-kaarten per listener |1 | |
| Maximumaantal op paden gebaseerde regels per URL-kaart|100||
| Configuraties omleiden |100<sup>1</sup>| |
| Aantal herschrijfregelsets |400| |
| Aantal header- of URL-configuraties per herschrijfregelset|40| |
| Aantal voorwaarden per herschrijfregelset|40| |
| Gelijktijdige WebSocket-verbindingen |Gemiddelde gateways 20.000<br> Grote gateways 50.000| |
| Maximale URL-lengte|32 KB| |
| Maximale header-grootte voor HTTP/2 |4 KB| |
| Maximumgrootte voor bestandsupload, Standard |2 GB | |
| Maximumgrootte voor bestandsupload, WAF |V1 Medium WAF-gateways, 100 MB<br>V1 Large WAF-gateways, 500 MB<br>V2 WAF, 750 MB| |
| Maximale grootte van WAF, zonder bestanden|128 kB||
| Maximumaantal aangepaste WAF-regels|100||
| Maximale WAF-uitsluitingen per Application Gateway|40||

<sup>1</sup> In het geval van SKU's met WAF ingeschakeld moet u het aantal resources beperken tot 40.
