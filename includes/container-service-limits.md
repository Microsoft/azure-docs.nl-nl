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
ms.openlocfilehash: da22991b9a1c4b69d3a3d6eb6f76b0925a6ad3d4
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107800197"
---
| Resource                                                                                                           | Limiet                                                                                                                                                                                                       |
| ------------------------------------------------------------------------------------------------------------------ | :---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Maximumaantal clusters per abonnement                                                                                  | 5000                                                                                                                                                                                                        |
| Maximumaantal knooppunten per cluster met virtuele-machinebeschikbaarheidssets en basic load balancer-SKU                       | 100                                                                                                                                                                                                         |
| Maximumaantal knooppunten per cluster met virtuele-machineschaalsets en [standaard load balancer-SKU][standard-load-balancer] | 1000 (voor alle [knooppuntgroepen)][node-pool]                                            |
| Maximumaantal knooppuntgroepen per cluster                                                                                     | 100                                                                                  |
| Maximumaantal pods per knooppunt: [Basisnetwerken][basic-networking] met Kubenet                                           | Maximum: 250 <br /> Standaardinstelling voor Azure CLI: 110 <br /> Azure Resource Manager sjabloon standaard: 110 <br /> Azure Portal implementatie standaard: 30          |
| Maximumaantal pods per knooppunt: [Geavanceerde netwerk][advanced-networking] met Azure Container Network Interface        | Maximum: 250 <br /> Standaardinstelling: 30                                                      |
| Preview van AKS-invoegversie van Service Mesh (OSM) openen                                                                          | Kubernetes-clusterversie: 1.19+<sup>1</sup><br />OSM-controllers per cluster: 1<sup>1</sup><br />Pods per OSM-controller: 500<sup>1</sup><br />Kubernetes-serviceaccounts die worden beheerd door OSM: 50<sup>1</sup> |

<sup>1</sup> De OSM-invoegversie voor AKS heeft een preview-status en zal aanvullende verbeteringen ondergaan voordat algemene beschikbaarheid (GA) wordt bereikt. Tijdens de preview-fase is het raadzaam om de weergegeven limieten niet te overschrijden.<br />

<!-- LINKS - Internal -->

[basic-networking]: ../articles/aks/concepts-network.md#kubenet-basic-networking
[advanced-networking]: ../articles/aks/concepts-network.md#azure-cni-advanced-networking
[standard-load-balancer]: ../articles/load-balancer/load-balancer-overview.md
[node-pool]: ../articles/aks/use-multiple-node-pools.md

<!-- LINKS - External -->

[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
