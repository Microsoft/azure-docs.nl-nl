---
title: Include-bestand
description: bestand opnemen
author: rothja
ms.service: azure-resource-manager
ms.topic: include
ms.date: 05/18/2018
ms.author: jroth
ms.custom: include file
ms.openlocfilehash: 74f9c5304237ec77a629bc914d95c345882b784f
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/24/2020
ms.locfileid: "95561047"
---
| Resource | Standaardlimiet | Maximumaantal |
| --- | --- | --- |
| vCPU's per [abonnement](https://azure.microsoft.com/pricing/)<sup>1</sup> |20 |10.000 |
| [Medebeheerders](../articles/cost-management-billing/manage/add-change-subscription-administrator.md) per abonnement |200 |200 |
| [Opslagaccounts](../articles/storage/common/storage-account-create.md) per abonnement<sup>2</sup> |100 |100 |
| [Cloudservices](../articles/cloud-services/cloud-services-choose-me.md) per abonnement |20 |200 |
| [Lokale netwerken](/previous-versions/azure/reference/jj157100(v=azure.100)) per abonnement |10 |500 |
| DNS-servers per abonnement |9 |100 |
| Gereserveerde IP-adressen per abonnement |20 |100 |
| [Affiniteitsgroepen](/previous-versions/azure/virtual-network/virtual-networks-migrate-to-regional-vnet) per abonnement |256 |256 |
| Lengte van de abonnementsnaam (tekens) | 64 | 64 |

<sup>1</sup>Extra kleine instanties tellen als één vCPU voor de vCPU-limiet, ondanks het gebruik van een gedeeltelijke CPU-kern.

<sup>2</sup>De opslagaccountlimiet omvat zowel Standard als Premium opslagaccounts.