---
title: SizeSelector UI-element
description: Hierin wordt het UI-element micro soft. compute. SizeSelector voor Azure Portal beschreven. Gebruiken voor het selecteren van de grootte van een virtuele machine.
author: tfitzmac
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: tomfitz
ms.openlocfilehash: d6408f8c08694ae681d302ae35f5778894091733
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "87063615"
---
# <a name="microsoftcomputesizeselector-ui-element"></a>Micro soft. compute. SizeSelector UI-element

Een besturings element voor het selecteren van een grootte voor een of meer exemplaren van virtuele machines.

## <a name="ui-sample"></a>UI-voor beeld

De gebruiker ziet een selector met de standaard waarden van de element definitie.

![Microsoft.Compute.SizeSelector](./media/managed-application-elements/microsoft-compute-sizeselector.png)

Nadat het besturings element is geselecteerd, ziet de gebruiker een uitgebreid overzicht van de beschik bare grootten.

![Micro soft. compute. SizeSelector uitgebreid](./media/managed-application-elements/microsoft-compute-sizeselector-expanded.png)

## <a name="schema"></a>Schema

```json
{
  "name": "element1",
  "type": "Microsoft.Compute.SizeSelector",
  "label": "Size",
  "toolTip": "",
  "recommendedSizes": [
    "Standard_D1",
    "Standard_D2",
    "Standard_D3"
  ],
  "constraints": {
    "allowedSizes": [],
    "excludedSizes": [],
    "numAvailabilityZonesRequired": 3,
    "zone": "3"
  },
  "options": {
    "hideDiskTypeFilter": false
  },
  "osPlatform": "Windows",
  "imageReference": {
    "publisher": "MicrosoftWindowsServer",
    "offer": "WindowsServer",
    "sku": "2012-R2-Datacenter"
  },
  "count": 2,
  "visible": true
}
```

## <a name="sample-output"></a>Voorbeelduitvoer

```json
"Standard_D1"
```

## <a name="remarks"></a>Opmerkingen

- `recommendedSizes` moet ten minste één grootte hebben. De eerste aanbevolen grootte wordt als de standaard waarde gebruikt. De lijst met beschik bare grootten is niet gesorteerd op de aanbevolen status. De gebruiker kan die kolom selecteren om deze op de aanbevolen status te sorteren.
- Als er geen aanbevolen grootte beschikbaar is op de geselecteerde locatie, wordt de grootte automatisch overgeslagen. In plaats daarvan wordt de volgende aanbevolen grootte gebruikt.
- `constraints.allowedSizes` en `constraints.excludedSizes` zijn beide optioneel, maar kunnen niet tegelijkertijd worden gebruikt. De lijst met beschik bare grootten kan worden bepaald door het aanroepen [van een lijst met beschik bare virtuele machine grootten voor een abonnement](/rest/api/compute/virtualmachines/virtualmachines-list-sizes-region). Elke grootte die niet is opgegeven in de `constraints.allowedSizes` is verborgen en een grootte die niet `constraints.excludedSizes` is opgegeven in wordt weer gegeven.
- `osPlatform` moet worden opgegeven, en kan **Windows** of **Linux** zijn. Dit wordt gebruikt om de hardwarekosten van de virtuele machines te bepalen.
- `imageReference` wordt wegge laten voor installatie kopieën van de eerste partij, maar is opgegeven voor installatie kopieën van derden. De oplossing wordt gebruikt om de software kosten van de virtuele machines te bepalen.
- `count` wordt gebruikt om de juiste vermenigvuldiger voor het element in te stellen. Het ondersteunt een statische waarde, zoals **2**, of een dynamische waarde van een ander element, zoals `[steps('step1').vmCount]` . De standaard waarde is **1**.
- De `numAvailabilityZonesRequired` kan 1, 2 of 3 zijn.
- Standaard `hideDiskTypeFilter` is **False**. Met het filter schijf type kan de gebruiker alle schijf typen of alleen SSD bekijken.

## <a name="next-steps"></a>Volgende stappen

* Zie aan de slag [met CreateUiDefinition](create-uidefinition-overview.md)voor een inleiding tot het maken van UI-definities.
* Zie [CreateUiDefinition-elementen](create-uidefinition-elements.md)voor een beschrijving van algemene eigenschappen in UI-elementen.
