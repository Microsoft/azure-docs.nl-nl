---
title: bestand opnemen
description: bestand opnemen
services: container-service
author: mlearned
ms.service: container-service
ms.topic: include
ms.date: 04/06/2021
ms.author: mlearned
ms.custom: include file
ms.openlocfilehash: 15e91e6f275c3a6ebe44690441404a38e8f61394
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107732518"
---
| Resource                                                                                                           | Limiet                                                                                                                                                                                                       |
| ------------------------------------------------------------------------------------------------------------------ | :---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Maximumaantal clusters per abonnement                                                                                  | 1000                                                                                                                                                                                                        |
| Maximumaantal knooppunten per cluster met virtuele-machinebeschikbaarheidssets en basic load balancer-SKU                       | 100                                                                                                                                                                                                         |
| Maximumaantal knooppunten per cluster met virtuele-machineschaalsets en [standaard load balancer-SKU][standard-load-balancer] | 1000 (voor alle [knooppuntgroepen)][node-pool]                                            |
| Maximum aantal knooppuntgroepen per cluster                                                                                     | 100                                                                                  |
| Maximumaantal pods per knooppunt: [Basisnetwerken][basic-networking] met Kubenet                                           | Maximum: 250 <br /> Azure CLI-standaardinstelling: 110 <br /> Azure Resource Manager sjabloon standaard: 110 <br /> Azure Portal implementatie standaard: 30          |
| Maximumaantal pods per knooppunt: [Geavanceerde netwerk][advanced-networking] met Azure Container Network Interface        | Maximum: 250 <br /> Standaardinstelling: 30                                                      |
| AKS-invoegversie van Service Mesh (OSM) openen                                                                          | Kubernetes-clusterversie: 1.19+<sup>1</sup><br />OSM-controllers per cluster: 1<sup>1</sup><br />Pods per OSM-controller: 500<sup>1</sup><br />Kubernetes-serviceaccounts die worden beheerd door OSM: 50<sup>1</sup> |

<sup>1</sup> De OSM-invoegversie voor AKS heeft een preview-status en ondergaat extra verbeteringen voordat algemene beschikbaarheid (GA) wordt bereikt. Tijdens de preview-fase is het raadzaam om de weergegeven limieten niet te overschrijden.<br />

<!-- LINKS - Internal -->

[basic-networking]: ../articles/aks/concepts-network.md#kubenet-basic-networking
[advanced-networking]: ../articles/aks/concepts-network.md#azure-cni-advanced-networking
[standard-load-balancer]: ../articles/load-balancer/load-balancer-overview.md
[node-pool]: ../articles/aks/use-multiple-node-pools.md

<!-- LINKS - External -->

[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
