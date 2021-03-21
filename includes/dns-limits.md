---
author: rothja
ms.service: azure-resource-manager
ms.topic: include
ms.date: 11/05/2020
ms.author: rohink
ms.openlocfilehash: 7011b92485c56187021c9043ba84bc85e448a98f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94329555"
---
**Openbare DNS-zones**

| Resource | Limiet |
| --- | --- |
| Openbare DNS-zones per abonnement |250 <sup>1</sup> |
| Recordsets per openbare DNS-zone |10.000 <sup>1</sup> |
| Records per recordset in openbare DNS-zone |20 |
| Aantal Alias-records voor een enkele Azure-resource |20|

<sup>1</sup>als u deze limieten wilt verhogen, neemt u contact op met ondersteuning voor Azure.

**Privé-DNS-zones**

| Resource | Limiet |
| --- | --- |
| Privé-DNS-zones per abonnement |1000|
| Recordsets per privé-DNS-zone |25.000|
| Records per recordset voor privé-DNS-zones |20|
| Virtual Network-koppelingen per privé-DNS-zone |1000|
| Virtual Network-koppelingen per privé-DNS-zones met automatische registratie ingeschakeld |100|
| Aantal privé-DNS-zones waaraan een virtueel netwerk kan worden gekoppeld met automatische registratie ingeschakeld |1|
| Aantal privé-DNS-zones in een virtueel netwerk dat kan worden gekoppeld |1000|
| Aantal DNS-query's dat een virtuele machine naar een Azure DNS-omzetter kan sturen, per seconde |1000 <sup>1</sup> |
| Maximumaantal DNS-query's in wachtrij (reactie in behandeling) per virtuele machine |200 <sup>1</sup> |

<sup>1</sup>Deze limieten worden toegepast op elke afzonderlijke virtuele machine en niet op het niveau van het virtuele netwerk. DNS-query's die deze limieten overschrijden, worden verwijderd.
