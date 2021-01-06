---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 05/06/2020
ms.author: glenga
ms.openlocfilehash: 3c9679f3d66d58c7a6c847b54c84438c979ecd39
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/06/2021
ms.locfileid: "97936856"
---
[Azure Private Endpoint](../articles/private-link/private-endpoint-overview.md) is een netwerkinterface die u privé en veilig verbindt met een service die wordt ondersteund door Azure Private Link.  Private Endpoint maakt gebruik van een privé-IP-adres van uw virtuele netwerk, waarbij de service effectief in uw virtuele netwerk wordt geplaatst.

U kunt een privé-eind punt gebruiken voor uw functies die worden gehost in de [Premium](../articles/azure-functions/functions-premium-plan.md) -en [app service](../articles/azure-functions/dedicated-plan.md) -abonnementen.

Bij het maken van een verbinding voor een inkomend particulier eind punt voor functions, hebt u ook een DNS-record nodig om het privé adres op te lossen.  Er wordt standaard een privé-DNS-record voor u gemaakt bij het maken van een persoonlijk eind punt met behulp van de Azure Portal.

Zie voor meer informatie over het [gebruik van privé-eind punten voor web apps](../articles/app-service/networking/private-endpoint.md).