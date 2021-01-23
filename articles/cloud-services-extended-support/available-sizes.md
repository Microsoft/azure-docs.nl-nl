---
title: Beschik bare grootten voor Azure Cloud Services (uitgebreide ondersteuning)
description: Beschik bare grootten voor Azure Cloud Services-implementaties (uitgebreide ondersteuning)
ms.topic: article
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: ''
ms.openlocfilehash: c3515b559ef647b9a043a04282b4edfc6e7fa9be
ms.sourcegitcommit: 6272bc01d8bdb833d43c56375bab1841a9c380a5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/23/2021
ms.locfileid: "98744271"
---
# <a name="available-sizes-for-azure-cloud-services-extended-support"></a>Beschik bare grootten voor Azure Cloud Services (uitgebreide ondersteuning)

In dit artikel worden de beschik bare grootten van virtuele machines voor Cloud Services-exemplaren (Extended support) beschreven.   

| SKU-familie |  ACU/core | 
|---|---|
| [A5-7](https://docs.microsoft.com/azure/virtual-machines/sizes-previous-gen?toc=/azure/virtual-machines/linux/toc.json&bc=/azure/virtual-machines/linux/breadcrumb/toc.json#a-series)| 100 |
|[A8-A11](https://docs.microsoft.com/azure/virtual-machines/sizes-previous-gen?toc=/azure/virtual-machines/linux/toc.json&bc=/azure/virtual-machines/linux/breadcrumb/toc.json#a-series---compute-intensive-instances) | 225* |
|[Av2](https://docs.microsoft.com/azure/virtual-machines/av2-series) | 100 | 
|[!](https://docs.microsoft.com/azure/virtual-machines/sizes-previous-gen?toc=/azure/virtual-machines/linux/toc.json&bc=/azure/virtual-machines/linux/breadcrumb/toc.json#d-series) | 160 | 
|[Dv2](https://docs.microsoft.com/azure/virtual-machines/dv2-dsv2-series) | 160-190 * |
|[Dv3](https://docs.microsoft.com/azure/virtual-machines/dv3-dsv3-series) | 160-190 * |
|[Ev3](https://docs.microsoft.com/azure/virtual-machines/ev3-esv3-series) | 160-190 *
|[Kg](https://docs.microsoft.com/azure/virtual-machines/sizes-previous-gen?toc=/azure/virtual-machines/linux/toc.json&bc=/azure/virtual-machines/linux/breadcrumb/toc.json#g-series) | 180-240 * |
|[HxBxD](https://docs.microsoft.com/azure/virtual-machines/h-series) | 290 - 300* | 

>[!NOTE]
> ACU's die met een * zijn gemarkeerd, maken gebruik van Intel® Turbo-technologie om de CPU-frequentie te verhogen en nóg betere prestaties te leveren. Hoe groot die extra prestaties zijn, is afhankelijk van de VM-grootte, de workload en de andere workloads die op dezelfde host worden uitgevoerd.

## <a name="configure-sizes-for-cloud-services-extended-support"></a>Groottes voor Cloud Services configureren (uitgebreide ondersteuning)

U kunt de grootte van de virtuele machine van een rolinstantie opgeven als onderdeel van het service model in het service definitie bestand. De grootte van de rol bepaalt het aantal CPU-kernen, de geheugen capaciteit en de grootte van het lokale bestands systeem.

Stel bijvoorbeeld de grootte van het webrole-exemplaar in op `Standard_D2` : 

```xml
<WorkerRole name="Worker1" vmsize="Standard_D2"> 
</WorkerRole> 
```

## <a name="change-the-size-of-an-existing-role"></a>De grootte van een bestaande rol wijzigen

Als u de grootte van een bestaande functie wilt wijzigen, wijzigt u de grootte van de virtuele machine in het service definitie bestand (csdef), verpakken u de Cloud service opnieuw en implementeert u deze opnieuw. 

## <a name="get-a-list-of-available-sizes"></a>Een lijst met beschik bare grootten ophalen 

Als u een lijst met beschik bare grootten wilt ophalen, raadpleegt u [resource sku's-List](https://docs.microsoft.com/rest/api/compute/resourceskus/list) en past u de volgende filters toe:


`ResourceType = virtualMachines ` <br>
`VMDeploymentTypes = PaaS `


## <a name="next-steps"></a>Volgende stappen 
- Controleer de [vereisten voor implementatie](deploy-prerequisite.md) voor Cloud Services (uitgebreide ondersteuning).
- Bekijk [Veelgestelde vragen](faq.md) over Cloud Services (uitgebreide ondersteuning).
- Implementeer een Cloud service (uitgebreide ondersteuning) met behulp van de [Azure Portal](deploy-portal.md), [Power shell](deploy-powershell.md), [sjabloon](deploy-template.md) of [Visual Studio](deploy-visual-studio.md).
