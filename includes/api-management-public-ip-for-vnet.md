---
author: dlepow
ms.service: api-management
ms.topic: include
ms.date: 04/12/2021
ms.author: danlep
ms.openlocfilehash: 6ed30ed2333393e9d8c0222500aaf3f17da6d33d
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107531639"
---
* **Een openbaar [IPv4-adres](../articles/virtual-network/public-ip-addresses.md#standard)** van de Standard-SKU als uw client API-versie 2021-01-01-preview of hoger gebruikt. De resource voor het openbare IP-adres is vereist bij het instellen van het virtuele netwerk voor externe of interne toegang. Met een intern virtueel netwerk wordt het openbare IP-adres alleen gebruikt voor beheerbewerkingen. Meer informatie over [IP-adressen van API Management.](../articles/api-management/api-management-howto-ip-addresses.md)

  * Het IP-adres moet zich in dezelfde regio en hetzelfde abonnement als het API Management-exemplaar en het virtuele netwerk.

  * De waarde van het IP-adres wordt toegewezen als het virtuele openbare IPv4-adres van het API Management in die regio. 

  * Bij het wijzigen van een extern naar intern virtueel netwerk (of omgekeerd), het wijzigen van subnetten in het netwerk of het bijwerken van beschikbaarheidszones voor het API Management-exemplaar, moet u een ander openbaar IP-adres configureren. 

  > [!IMPORTANT]
  > Momenteel gebruikt de Azure Portal API-versie 2021-01-01 preview bij het maken of bijwerken van een API Management-exemplaar. U kunt deze API-versie opgeven met behulp van Azure Resource Manager sjabloon of de API Management REST API. De Azure CLI en Azure PowerShell ondersteunen momenteel API-versie 2020-12-01.
