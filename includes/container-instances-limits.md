---
author: dlepow
ms.service: container-instances
ms.topic: include
ms.date: 07/22/2020
ms.author: danlep
ms.openlocfilehash: 6878180ffedfaa53f25d2bdc6db72dcd7dd8b38b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "87384821"
---
| Resource | Limiet |
| --- | :--- |
| Standaard SKU-containergroepen per regio en per abonnement | 100<sup>1</sup> |
| Toegewezen SKU-containergroepen per regio en per abonnement | 0<sup>1</sup> |
| Aantal containers per containergroep | 60 |
| Aantal volumes per containergroep | 20 |
| Standaard SKU-kernen (CPU's) per regio en per abonnement | 10<sup>1,2</sup> | 
| Standaard SKU-kernen (CPU's) voor K80 GPU per regio en per abonnement | 18<sup>1,2</sup> |
| Standaard SKU-kernen (CPU's) voor P100 of V100 GPU per regio en per abonnement | 0<sup>1,2</sup> |
| Poorten per IP-adres | 5 |
| Logboekgrootte van containerexemplaar - uitgevoerd exemplaar | 4 MB |
| Logboekgrootte van containerexemplaar - gestopt exemplaar | 16 KB of 1000 regels |
| Aangemaakte containers per uur |300<sup>1</sup> |
| Aangemaakte containers per 5 minuten | 100<sup>1</sup> |
| Verwijderde containers per uur | 300<sup>1</sup> |
| Verwijderde containers per 5 minuten | 100<sup>1</sup> |


<sup>1</sup> Stel een [Azure-ondersteuningsaanvraag][azure-support] op om een hogere limiet aan te vragen. Gratis abonnementen, waaronder [gratis Azure-account](https://azure.microsoft.com/offers/ms-azr-0044p/) en [Azure for Students](https://azure.microsoft.com/offers/ms-azr-0170p/) komen niet in aanmerking voor limiet- of quotumverhoging. Als u een gratis abonnement hebt, kunt u [upgraden](../articles/cost-management-billing/manage/upgrade-azure-subscription.md) naar een abonnement met betalen per gebruik.<br />
<sup>2</sup>Standaardlimiet voor abonnement [met betalen per gebruik](https://azure.microsoft.com/offers/ms-azr-0003p/). De limiet kan verschillen voor andere typen categorieën.<br/>

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
