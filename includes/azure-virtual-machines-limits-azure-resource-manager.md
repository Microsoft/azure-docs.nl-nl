---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 02/10/2020
ms.author: cynthn
ms.openlocfilehash: 96c476d1724f9475eb28675fc24e21192935e883
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104719907"
---
| Resource | Limiet |
| --- | --- |
| Virtuele machines [per abonnement](https://azure.microsoft.com/pricing/) |25.000<sup>1</sup> per regio. |
| Totaal aantal VM-cores per [abonnement](https://azure.microsoft.com/pricing/) |20<sup>1</sup> per regio. Neem contact op met de ondersteuning om de limiet te verhogen. |
| Totaal aantal cores voor spot-VM's van Azure per [abonnement](https://azure.microsoft.com/pricing/) |20<sup>1</sup> per regio. Neem contact op met de ondersteuning om de limiet te verhogen. |
| VM's per reeks, bijvoorbeeld Dv2 en F, cores per [abonnement](https://azure.microsoft.com/pricing/) |20<sup>1</sup> per regio. Neem contact op met de ondersteuning om de limiet te verhogen. |
| [Beschikbaarheidssets](../articles/virtual-machines/availability-set-overview.md) per abonnement |2500 per regio. |
| Beschikbaarheidsset per virtuele machine | 200 |
| [Nabijheidsplaatsingsgroep ](../articles/virtual-machines/windows/proximity-placement-groups-portal.md) per [resourcegroup](../articles/azure-resource-manager/management/overview.md#resource-groups) | 800 |
| Certificaten per beschikbaarheidsset | 199<sup>2</sup> |
| Certificaten per abonnement |Onbeperkt<sup>3</sup> |

<sup>1</sup> Standaardlimieten variëren per categorietype van de aanbieding, zoals Gratis proefversie en Betalen per gebruik, en per serie, zoals Dv2, F en G. De standaardinstelling voor Enterprise Agreement abonnementen is 350. Voor beveiliging worden abonnementen standaard ingesteld op 20 kernen om te voor komen dat grote kernen worden geïmplementeerd. Als u meer kernen nodig hebt, moet u een ondersteunings ticket indienen.

<sup>2</sup> Eigenschappen, zoals openbare SSH-sleutels, worden ook gepusht als certificaten en tellen mee voor deze limiet. Als u deze limiet wilt omzeilen, gebruikt u de [Azure Key Vault-extensie voor Windows](../articles/virtual-machines/extensions/key-vault-windows.md) of de [Azure Key Vault-extensie voor Linux](../articles/virtual-machines/extensions/key-vault-linux.md) om certificaten te installeren.

<sup>3</sup> Met Azure Resource Manager worden certificaten opgeslagen in Azure Key Vault. Het aantal certificaten is onbeperkt voor een abonnement. Er is een limiet van 1 MB voor certificaten per implementatie, die bestaat uit één virtuele machine of een beschikbaarheidsset.

> [!NOTE]
> De kerngeheugens van de virtuele machine hebben een regionale limiet. Er geldt ook een limiet voor regionale reeksen per grootte, zoals Dv2 en F. Deze limieten worden afzonderlijk afgedwongen. Neem bijvoorbeeld een abonnement met een limiet van 30 VM-cores voor US - oost, een limiet van 30 cores voor de A-serie en een limiet van 30 cores voor de D-serie. Met dit abonnement zouden 30 A1-VM's of 30 D1-VM's kunnen worden geïmplementeerd, of een combinatie van deze twee typen dat het totaal van 30 cores niet overschrijdt. Een voorbeeld van een combinatie is 10 A1-VM's en 20 D1 VM's.
> <!-- -->
>
