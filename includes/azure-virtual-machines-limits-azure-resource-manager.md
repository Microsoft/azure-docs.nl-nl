---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 02/10/2020
ms.author: cynthn
ms.openlocfilehash: a9c830025b9ce6c93bf106a8348b0f9dc6521582
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/24/2020
ms.locfileid: "95554282"
---
| Resource | Limiet |
| --- | --- |
| Virtuele machines [per abonnement](https://azure.microsoft.com/pricing/) |25.000<sup>1</sup> per regio. |
| Totaal aantal VM-cores per [abonnement](https://azure.microsoft.com/pricing/) |20<sup>1</sup> per regio. Neem contact op met de ondersteuning om de limiet te verhogen. |
| Totaal aantal cores voor spot-VM's van Azure per [abonnement](https://azure.microsoft.com/pricing/) |20<sup>1</sup> per regio. Neem contact op met de ondersteuning om de limiet te verhogen. |
| VM's per reeks, bijvoorbeeld Dv2 en F, cores per [abonnement](https://azure.microsoft.com/pricing/) |20<sup>1</sup> per regio. Neem contact op met de ondersteuning om de limiet te verhogen. |
| [Beschikbaarheidssets](../articles/virtual-machines/manage-availability.md#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy) per abonnement |2500 per regio. |
| Beschikbaarheidsset per virtuele machine | 200 |
| Certificaten per abonnement |Onbeperkt<sup>2</sup> |

<sup>1</sup>Standaardlimieten variëren per categorietype van de aanbieding, zoals Gratis proefversie en Betalen per gebruik, en per serie, zoals Dv2, F en G. De standaardinstelling voor Enterprise Agreement abonnementen is 350.

<sup>2</sup>Met Azure Resource Manager worden certificaten opgeslagen in Azure Key Vault. Het aantal certificaten is onbeperkt voor een abonnement. Er is een limiet van 1 MB voor certificaten per implementatie, die bestaat uit één virtuele machine of een beschikbaarheidsset.

> [!NOTE]
> De kerngeheugens van de virtuele machine hebben een regionale limiet. Er geldt ook een limiet voor regionale reeksen per grootte, zoals Dv2 en F. Deze limieten worden afzonderlijk afgedwongen. Neem bijvoorbeeld een abonnement met een limiet van 30 VM-cores voor US - oost, een limiet van 30 cores voor de A-serie en een limiet van 30 cores voor de D-serie. Met dit abonnement zouden 30 A1-VM's of 30 D1-VM's kunnen worden geïmplementeerd, of een combinatie van deze twee typen dat het totaal van 30 cores niet overschrijdt. Een voorbeeld van een combinatie is 10 A1-VM's en 20 D1 VM's.  
> <!-- -->
>