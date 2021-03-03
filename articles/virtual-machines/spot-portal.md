---
title: De portal gebruiken om Azure Spot Virtual Machines te implementeren
description: Informatie over het gebruik van Azure PowerShell voor het implementeren van Spot Virtual Machines om kosten op te slaan.
author: cynthn
ms.service: virtual-machines
ms.subservice: spot
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 09/14/2020
ms.author: cynthn
ms.reviewer: jagaveer
ms.openlocfilehash: 42f2078e9781e50712344778a33ce8735b4ce11b
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/02/2021
ms.locfileid: "101677349"
---
# <a name="deploy-azure-spot-virtual-machines-using-the-azure-portal"></a>Azure Spot Virtual Machines implementeren met behulp van de Azure Portal

Met behulp van [Azure Spot Virtual Machines](spot-vms.md) kunt u profiteren van onze ongebruikte capaciteit tegen een aanzienlijke kosten besparing. Op elk moment dat Azure de capaciteit nodig heeft, verwijdert de Azure-infra structuur Azure Spot Virtual Machines. Daarom zijn Azure Spot Virtual Machines geweldig voor workloads die onderbrekingen kunnen afhandelen, zoals batch verwerkings taken, ontwikkel-en test omgevingen, grootschalige werk belastingen en meer.

Prijzen voor Azure Spot Virtual Machines is variabel, op basis van de regio en de SKU. Zie prijzen voor VM'S voor [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) en [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/)voor meer informatie. Zie [Azure Spot Virtual Machines-prijzen](spot-vms.md#pricing)voor meer informatie over het instellen van de maximum prijs.

U hebt de mogelijkheid om een maximum prijs voor de virtuele machine in te stellen die u wilt betalen, per uur. U kunt de maximale prijs voor een virtuele machine van Azure spot instellen in Amerikaanse dollars (USD), met Maxi maal vijf decimalen. De waarde `0.05701` is bijvoorbeeld een maximum prijs van $0,05701 USD per uur. Als u de maximale prijs instelt op `-1` , wordt de VM niet verwijderd op basis van de prijs. De prijs voor de virtuele machine is de huidige prijs voor steun of de prijs voor een standaard-VM, die ooit kleiner is, zolang er capaciteit en quota beschikbaar zijn.

Wanneer de virtuele machine wordt verwijderd, hebt u de mogelijkheid om de virtuele machine en de onderliggende schijf te verwijderen of de toewijzing van de virtuele machine ongedaan te maken zodat deze later opnieuw kan worden gestart.


## <a name="create-the-vm"></a>De VM maken

Wanneer u een virtuele machine implementeert, kunt u ervoor kiezen om een Azure spot-instantie te gebruiken.


Op het tabblad **basis beginselen** , in de sectie **Details van exemplaar** , is **Nee** de standaard waarde voor het gebruik van een Azure spot-instantie.

![Scherm opname voor het kiezen van Nee, gebruik geen Azure spot-instantie](./media/spot-portal/no.png)

Als u **Ja** selecteert, wordt de sectie uitgebreid en kunt u het [verwijderings type en verwijderings beleid](spot-vms.md#eviction-policy)kiezen. 

![Scherm opname voor het kiezen van Ja, een Azure spot-instantie gebruiken](./media/spot-portal/yes.png)

U kunt de prijs-en verwijderings tarieven ook vergelijken met andere vergelijk bare regio's door de **prijs geschiedenis weer geven te selecteren en de prijzen in nabijgelegen regio's te vergelijken**.

In dit voor beeld is de regio van de Canada-centraal minder duur en heeft deze een lagere verwijderings frequentie dan de regio VS-Oost.

:::image type="content" source="./media/spot-portal/pricing.png" alt-text="Scherm opname van de regio opties met het verschil in prijs-en verwijderings tarieven.":::

U kunt de regio wijzigen door de keuze te selecteren die het beste bij u past en vervolgens **OK** te selecteren.

## <a name="simulate-an-eviction"></a>Een verwijdering simuleren

U kunt een virtuele machine van Azure spot [simuleren](/rest/api/compute/virtualmachines/simulateeviction) om te testen hoe goed uw toepassing reageert op een plotselinge verwijdering. 

Vervang het volgende door uw gegevens: 

- `subscriptionId`
- `resourceGroupName`
- `vmName`


```http
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{vmName}/simulateEviction?api-version=2020-06-01
```

## <a name="next-steps"></a>Volgende stappen

U kunt ook Azure Spot-Virtual Machines maken met behulp van [Power shell](./windows/spot-powershell.md), [cli](./linux/spot-cli.md)of een [sjabloon](./linux/spot-template.md).
