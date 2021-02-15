---
title: bestand opnemen
description: bestand opnemen
services: virtual-machines
author: styli365
ms.service: virtual-machines
ms.topic: include
ms.date: 11/05/2020
ms.author: sttsinar
ms.custom: include file
ms.openlocfilehash: e22c2b7cb561e30e84ea5ede5481fbdc35be8cdf
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100515015"
---
Azure Compute biedt virtuele machine grootten die zijn geïsoleerd voor een specifiek hardwaretype en die zijn toegewezen aan één klant. De geïsoleerde grootten Live en worden gebruikt voor het genereren van specifieke hardware en worden afgeschaft wanneer het genereren van de hardware buiten gebruik wordt gesteld.

Geïsoleerde virtuele-machine grootten zijn het meest geschikt voor werk belastingen die een hoge mate van isolatie van andere werk belastingen vereisen om redenen die voldoen aan de vereisten voor naleving en regelgeving.  Het gebruik van een geïsoleerde grootte garandeert dat uw virtuele machine de enige is die op dat specifieke Server exemplaar wordt uitgevoerd. 


Daarnaast kunnen klanten er ook voor kiezen om de resources van deze Vm's te onderverdelen met behulp [van Azure-ondersteuning voor geneste virtuele machines](https://azure.microsoft.com/blog/nested-virtualization-in-azure/), omdat de virtuele machines van de geïsoleerde grootte groot zijn.

De huidige, geïsoleerde virtuele-machine-aanbiedingen omvatten:
* Standard_E80ids_v4
* Standard_E80is_v4
* Standard_F72s_v2
* Standard_E64is_v3
* Standard_E64i_v3
* Standard_M128ms
* Standard_GS5
* Standard_G5


> [!NOTE]
> Geïsoleerde VM-grootten hebben een beperkte levens duur van de hardware. Zie hieronder voor meer informatie

## <a name="deprecation-of-isolated-vm-sizes"></a>Afschaffing van geïsoleerde VM-grootten

Geïsoleerde VM-grootten hebben een beperkte levens duur van de hardware. Azure zal 12 maanden vóór de officiële afschaffing datum van de grootten een melding doen, en biedt een bijgewerkte, geïsoleerde aanbieding voor uw rekening.

| Grootte | Aftredings datum van isolatie | 
| --- | --- |
| Standard_DS15_v2<sup>1</sup> | 15 mei 2020 |
| Standard_D15_v2<sup>1</sup>  | 15 mei 2020 |

<sup>1</sup>  Zie Veelgestelde vragen voor meer informatie over Standard_DS15_v2 en Standard_D15_v2 buiten gebruik gesteld programma voor isolatie


## <a name="faq"></a>Veelgestelde vragen
### <a name="q-is-the-size-going-to-get-retired-or-only-its-isolation-feature"></a>V: is de grootte van het buiten gebruik gesteld of alleen de ' isolatie functie '?
**A**: als de grootte van de virtuele machine niet het subscript ' i ' heeft, wordt alleen de ' isolatie functie ' buiten gebruik gesteld. Als er geen isolatie nodig is, wordt er geen actie ondernomen en blijft de virtuele machine werken zoals verwacht. Voor beelden zijn Standard_DS15_v2, Standard_D15_v2, Standard_M128ms enzovoort. Als de grootte van de virtuele machine ' i ' bevat, wordt de grootte buiten gebruik gesteld.

### <a name="q-is-there-a-downtime-when-my-vm-lands-on-a-non-isolated-hardware"></a>V: is er sprake van uitval tijd wanneer mijn VM-land op een niet-geïsoleerde hardware wordt gehouden?
**A**: als er geen isolatie nodig is, hoeft u geen actie te ondernemen en treedt er geen uitval tijd op.

### <a name="q-is-there-any-cost-delta-for-moving-to-a-non-isolated-virtual-machine"></a>V: is er sprake van een kosten verschil voor de overstap naar een niet-geïsoleerde virtuele machine?
**A**: Nee

### <a name="q-when-are-the-other-isolated-sizes-going-to-retire"></a>V: wanneer zijn de andere geïsoleerde grootten buiten gebruik gesteld?
**A**: de officiële afschaffing van de geïsoleerde omvang is een periode van 12 maanden van tevoren.

### <a name="q-im-an-azure-service-fabric-customer-relying-on-the-silver-or-gold-durability-tiers-does-this-change-impact-me"></a>V: Ik ben een Azure Service Fabric klant die afhankelijk is van de laag met Silver of Gold-duurzaamheid. Is dit van invloed op mijn wijzigingen?
**A**: Nee. De garanties die worden geboden door de [duurzaamheids lagen](../articles/service-fabric/service-fabric-cluster-capacity.md#durability-characteristics-of-the-cluster) van service Fabric blijven werken, zelfs na deze wijziging. Als u voor andere redenen fysieke hardware-isolatie nodig hebt, moet u mogelijk nog een van de hierboven beschreven acties uitvoeren. 
 
### <a name="q-what-are-the-milestones-for-d15_v2-or-ds15_v2-isolation-retirement"></a>V: wat zijn de mijl palen voor D15_v2 of DS15_v2 buiten gebruik stellen? 
**A**: 
 
| Datum | Bewerking |
|---|---| 
| 18 november 2019 | Beschik baarheid van D/DS15i_v2 (PAYG, 1-jarige RI) | 
| 14 mei 2020 | Laatste dag tot aankoop D/DS15i_v2 1 jaar RI | 
| 15 mei 2020 | D/DS15_v2-isolatie garantie verwijderd | 
| 15 mei 2021 | D/DS15i_v2 buiten gebruik stellen (alle klanten, met uitzonde ring van wie de 3 jaar RI van D/DS15_v2 heeft gekocht vóór 18 november 2019)| 
| 17 november 2022 | D/DS15i_v2 buiten gebruik stellen als er 3 jaar een RIs is uitgevoerd (voor klanten die 3-jarig RI van D/DS15_v2 hebben gekocht vóór 18 november 2019) |

## <a name="next-steps"></a>Volgende stappen

Klanten kunnen er ook voor kiezen om de resources van deze geïsoleerde virtuele machines verder te onderverdelen met behulp [van Azure-ondersteuning voor geneste virtuele machines](https://azure.microsoft.com/blog/nested-virtualization-in-azure/).
