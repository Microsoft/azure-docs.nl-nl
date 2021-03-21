---
author: cherylmc
ms.date: 12/13/2019
ms.service: expressroute
ms.topic: include
ms.author: cherylmc
ms.openlocfilehash: 7aee201b0419b65c7ea8ddcb6f2d42ffaff4711e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101750009"
---
|  | **Persoonlijke peering** | **Microsoft-peering** |  **Openbare peering** (afgeschaft voor nieuwe circuits) |
| --- | --- | --- | --- |
| **Maximumaantal voorvoegsels ondersteund per peering** |4000 standaard, 10.000 met ExpressRoute Premium |200 |200 |
| **Ondersteunde IP-adresbereiken** |Elk geldig IP-adres in uw WAN. |Openbare IP-adressen waarvan u of uw connectiviteitsprovider eigenaar is. |Openbare IP-adressen waarvan u of uw connectiviteitsprovider eigenaar is. |
| **Vereisten voor AS-nummers** |Persoonlijke en openbare AS-nummers. Als u ervoor kiest een openbaar AS-nummer te gebruiken, moet u er eigenaar van zijn. |Persoonlijke en openbare AS-nummers. U moet echter bewijzen dat u eigenaar bent van openbare IP-adressen. |Persoonlijke en openbare AS-nummers. U moet echter bewijzen dat u eigenaar bent van openbare IP-adressen. |
| **Ondersteunde IP-protocollen**| IPv4, IPv6 (preview-versie) |  IPv4, IPv6 | IPv4 |
| **IP-adressen met routeringsinterface** |RFC1918- en openbare IP-adressen |Openbare IP-adressen die bij u zijn geregistreerd in routeringsregisters. |Openbare IP-adressen die bij u zijn geregistreerd in routeringsregisters. |
| **Ondersteuning voor MD5-hashes** |Ja |Ja |Ja |