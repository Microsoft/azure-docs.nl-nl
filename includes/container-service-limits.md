---
title: bestand opnemen
description: bestand opnemen
services: container-service
author: dlepow
ms.service: container-service
ms.topic: include
ms.date: 11/22/2019
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 32edd880e5e455393e60c87f4f963d71a3e59a20
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/24/2020
ms.locfileid: "95555982"
---
| Resource | Limiet |
| --- | :--- |
| Maximumaantal clusters per abonnement | 100 |
| Maximumaantal knooppunten per cluster met virtuele-machinebeschikbaarheidssets en basic load balancer-SKU  | 100 |
| Maximumaantal knooppunten per cluster met virtuele-machineschaalsets en [standaard load balancer-SKU][standard-load-balancer] | 1000 (100 knooppunten per [knooppuntpool][node-pool]) |
| Maximumaantal pods per knooppunt: [Basisnetwerken][basic-networking] met Kubenet | 110 |
| Maximumaantal pods per knooppunt: [Geavanceerde netwerk][advanced-networking] met Azure Container Network Interface | Implementatie van Azure CLI: 30<sup>1</sup><br />Azure Resource Manager-sjabloon: 30<sup>1</sup><br />Portal-implementatie: 30 |

<sup>1</sup>Als u een Azure Kubernetes Service-cluster (AKDS) implementeert met behulp van de Azure CLI of een Resource Manager-sjabloon, kan deze waarde worden geconfigureerd tot maximaal 250 pods per knooppunt. U kunt geen maximumaantal pods per node configureren als u het AKS-cluster al hebt geïmplementeerd, of als u een cluster met behulp van de Azure-portal hebt geïmplementeerd.<br />

<!-- LINKS - Internal -->
[basic-networking]: ../articles/aks/concepts-network.md#kubenet-basic-networking
[advanced-networking]: ../articles/aks/concepts-network.md#azure-cni-advanced-networking
[standard-load-balancer]: ../articles/load-balancer/load-balancer-overview.md
[node-pool]: ../articles/aks/use-multiple-node-pools.md

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest