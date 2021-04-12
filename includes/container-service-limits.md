---
title: bestand opnemen
description: bestand opnemen
services: container-service
author: mlearned
ms.service: container-service
ms.topic: include
ms.date: 11/22/2019
ms.author: mlearned
ms.custom: include file
ms.openlocfilehash: 1c2dec106ae72ddead7bda54792fa74e38eb6660
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/31/2021
ms.locfileid: "106081067"
---
| Resource                                                                                                           | Limiet                                                                                                                                                                                                       |
| ------------------------------------------------------------------------------------------------------------------ | :---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Maximumaantal clusters per abonnement                                                                                  | 1000                                                                                                                                                                                                        |
| Maximumaantal knooppunten per cluster met virtuele-machinebeschikbaarheidssets en basic load balancer-SKU                       | 100                                                                                                                                                                                                         |
| Maximumaantal knooppunten per cluster met virtuele-machineschaalsets en [standaard load balancer-SKU][standard-load-balancer] | 1000 (100 knooppunten per [knooppuntpool][node-pool])                                                                                                                                                                 |
| Maximumaantal pods per knooppunt: [Basisnetwerken][basic-networking] met Kubenet                                           | 110                                                                                                                                                                                                         |
| Maximumaantal pods per knooppunt: [Geavanceerde netwerk][advanced-networking] met Azure Container Network Interface        | Implementatie van Azure CLI: 30<sup>1</sup><br />Azure Resource Manager-sjabloon: 30<sup>1</sup><br />Portal-implementatie: 30                                                                                        |
| Preview-versie van OSM (Service Mesh) openen AKS                                                                          | Kubernetes-cluster versie: 1.19 +<sup>2</sup><br />OSM-controllers per cluster: 1<sup>2</sup><br />Peul per OSM-controller: 500<sup>2</sup><br />Kubernetes-service accounts die worden beheerd door OSM: 50<sup>2</sup> |

<sup>1</sup>Als u een Azure Kubernetes Service-cluster (AKDS) implementeert met behulp van de Azure CLI of een Resource Manager-sjabloon, kan deze waarde worden geconfigureerd tot maximaal 250 pods per knooppunt. U kunt geen maximumaantal pods per node configureren als u het AKS-cluster al hebt geïmplementeerd, of als u een cluster met behulp van de Azure-portal hebt geïmplementeerd.<br />

<sup>2</sup> De OSM-invoeg toepassing voor AKS bevindt zich in een preview-status en zal extra verbeteringen ondergaan vóór algemene Beschik baarheid (GA). Tijdens de preview-fase wordt aanbevolen de weer gegeven limieten niet te overschrijden.<br />

<!-- LINKS - Internal -->

[basic-networking]: ../articles/aks/concepts-network.md#kubenet-basic-networking
[advanced-networking]: ../articles/aks/concepts-network.md#azure-cni-advanced-networking
[standard-load-balancer]: ../articles/load-balancer/load-balancer-overview.md
[node-pool]: ../articles/aks/use-multiple-node-pools.md

<!-- LINKS - External -->

[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
