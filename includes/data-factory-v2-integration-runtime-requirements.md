---
author: linda33wj
ms.service: data-factory
ms.topic: include
ms.date: 07/13/2020
ms.author: jingwang
ms.openlocfilehash: fbde8bc28f8fc34b7a6a6443950b8733c6dcff45
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "96023170"
---
<!--
    Separate the generic requirement on Self-hosted Integration Runtime set-up from connector articles.
-->
Als uw gegevensarchief zich in een on-premises netwerk, een virtueel Azure-netwerk of een virtuele particuliere cloud van Amazon bevindt, moet u een [zelf-hostende Integration Runtime](../articles/data-factory/create-self-hosted-integration-runtime.md) configureren om er verbinding mee te maken.

Als uw gegevensarchief een beheerde cloudgegevensservice is, kunt u ook Azure Integration Runtime gebruiken. Als de toegang is beperkt tot IP-adressen op de goedgekeurde lijst in de firewallregels, kunt u de [IP-adressen van Azure Integration Runtime](../articles/data-factory/azure-integration-runtime-ip-addresses.md) toevoegen aan de acceptatielijst. 

Zie [Strategieën voor gegevenstoegang](../articles/data-factory/data-access-strategies.md) voor meer informatie over de netwerkbeveiligingsmechanismen en -opties die door Data Factory worden ondersteund.
