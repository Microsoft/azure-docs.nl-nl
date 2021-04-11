---
title: bestand opnemen
description: bestand opnemen
services: virtual-machines
author: rishabv90
ms.service: virtual-machines
ms.topic: include
ms.date: 11/05/2020
ms.author: risverma
ms.custom: include file
ms.openlocfilehash: 83a19dea56693a1caff2c982b9f772543fe1cf2e
ms.sourcegitcommit: d40ffda6ef9463bb75835754cabe84e3da24aab5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/07/2021
ms.locfileid: "107073663"
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
* Standard_DC8_v2


> [!NOTE]
> Geïsoleerde VM-grootten hebben een beperkte levens duur van de hardware. Zie hieronder voor meer informatie

## <a name="deprecation-of-isolated-vm-sizes"></a>Afschaffing van geïsoleerde VM-grootten

Geïsoleerde VM-grootten hebben een beperkte levens duur van de hardware. Azure zal 12 maanden vóór de officiële afschaffing datum van de grootten een melding doen, en biedt een bijgewerkte, geïsoleerde aanbieding voor uw rekening.

| Grootte | Aftredings datum van isolatie | 
| --- | --- |
| Standard_DS15_v2 | 15 mei 2021 |
| Standard_D15_v2  | 15 mei 2021 |
| Standard_G5  | 15 februari 2022 |
| Standard_GS5  | 15 februari 2022 |
| Standard_E64i_v3  | 15 februari 2022 |
| Standard_E64is_v3  | 15 februari 2022 |
| Standard_DC8_v2 | 15 februari 2022 |


## <a name="faq"></a>Veelgestelde vragen
### <a name="q-is-the-size-going-to-get-retired-or-only-its-isolation-feature"></a>V: is de grootte van het buiten gebruik gesteld of alleen de ' isolatie functie '?
**A**: op dit moment wordt alleen de isolatie functie van de VM-grootten buiten gebruik gesteld. De afgeschafte geïsoleerde grootten blijven bestaan in een niet-geïsoleerde status. Als er geen isolatie nodig is, wordt er geen actie ondernomen en blijft de virtuele machine werken zoals verwacht.

### <a name="q-is-there-a-downtime-when-my-vm-lands-on-a-non-isolated-hardware"></a>V: is er sprake van uitval tijd wanneer mijn VM-land op een niet-geïsoleerde hardware wordt gehouden?
**A**: als er geen isolatie nodig is, hoeft u geen actie te ondernemen en treedt er geen uitval tijd op. In tegens telling tot de isolatie is vereist, bevat onze aankondiging de aanbevolen vervangende grootte. Als u de vervangings grootte selecteert, moeten onze klanten hun Vm's groter of kleiner maken.  

### <a name="q-is-there-any-cost-delta-for-moving-to-a-non-isolated-virtual-machine"></a>V: is er sprake van een kosten verschil voor de overstap naar een niet-geïsoleerde virtuele machine?
**A**: Nee

### <a name="q-when-are-the-other-isolated-sizes-going-to-retire"></a>V: wanneer zijn de andere geïsoleerde grootten buiten gebruik gesteld?
**A**: de officiële afschaffing van de geïsoleerde omvang is een periode van 12 maanden van tevoren. Onze meest recente aankondiging omvat de isolatie functie buiten gebruik stellen van Standard_G5, Standard_GS5, Standard_E64i_v3 en Standard_E64i_v3.  

### <a name="q-im-an-azure-service-fabric-customer-relying-on-the-silver-or-gold-durability-tiers-does-this-change-impact-me"></a>V: Ik ben een Azure Service Fabric klant die afhankelijk is van de laag met Silver of Gold-duurzaamheid. Is dit van invloed op mijn wijzigingen?
**A**: Nee. De garanties die worden geboden door de [duurzaamheids lagen](../articles/service-fabric/service-fabric-cluster-capacity.md#durability-characteristics-of-the-cluster) van service Fabric blijven werken, zelfs na deze wijziging. Als u voor andere redenen fysieke hardware-isolatie nodig hebt, moet u mogelijk nog een van de hierboven beschreven acties uitvoeren. 
 
### <a name="q-what-are-the-milestones-for-d15_v2-or-ds15_v2-isolation-retirement"></a>V: wat zijn de mijl palen voor D15_v2 of DS15_v2 buiten gebruik stellen? 
**A**: 
 
| Datum | Bewerking |
|---|---| 
| 15 mei 2020<sup>1</sup> | Aankondiging van D/DS15_v2-isolatie buiten gebruik stellen| 
| 15 mei 2021 | D/DS15_v2-isolatie garantie verwijderd| 

<sup>1</sup> bestaande klant die deze grootten gebruikt, ontvangt een aankondigings-e-mail met gedetailleerde instructies voor de volgende stappen.  

### <a name="q-what-are-the-milestones-for-g5-gs5-e64i_v3-and-e64is_v3-isolation-retirement"></a>V: wat zijn de mijl palen voor G5, Gs5, E64i_v3 en E64is_v3 buiten gebruik stellen? 
**A**: 
 
| Datum | Bewerking |
|---|---|
| 15 februari 2021<sup>1</sup> | G5/GS5/E64i_v3/E64is_v3 isolatie buiten gebruik stellen |
| 28 februari 2022 | G5/GS5/E64i_v3/E64is_v3-isolatie garantie verwijderd |

<sup>1</sup> bestaande klant die deze grootten gebruikt, ontvangt een aankondigings-e-mail met gedetailleerde instructies voor de volgende stappen.  

## <a name="next-steps"></a>Volgende stappen

Klanten kunnen er ook voor kiezen om de resources van deze geïsoleerde virtuele machines verder te onderverdelen met behulp [van Azure-ondersteuning voor geneste virtuele machines](https://azure.microsoft.com/blog/nested-virtualization-in-azure/).
