---
title: Exemplaar-Id's voor virtuele machines van Azure VM-schaal sets begrijpen
description: Exemplaar-Id's voor Azure VM-schaal sets virtuele machines en de verschillende manieren waarop ze zich bevinden.
author: mimckitt
ms.author: mimckitt
ms.topic: conceptual
ms.service: virtual-machine-scale-sets
ms.subservice: management
ms.date: 02/22/2018
ms.reviewer: jushiman
ms.custom: mimckitt, devx-track-azurecli
ms.openlocfilehash: a32a5a04c5c71cc06d60f3d2f21946f5361a2afd
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/18/2020
ms.locfileid: "94843244"
---
# <a name="understand-instance-ids-for-azure-vm-scale-set-vms"></a>Exemplaar-Id's voor virtuele machines van Azure VM-schaal sets begrijpen
In dit artikel worden exemplaar-Id's beschreven voor schaal sets en de verschillende manieren waarop ze zich bevinden.

## <a name="scale-set-instance-ids"></a>Instantie-Id's van schaal sets

Elke virtuele machine in een schaalset haalt een exemplaar-ID op die een unieke identificatie heeft. Deze exemplaar-ID wordt in de schaalset-Api's gebruikt om bewerkingen uit te voeren op een specifieke virtuele machine in de schaalset. U kunt bijvoorbeeld een specifieke exemplaar-ID opgeven voor het opnieuw instellen van de installatie kopie bij gebruik van de Reimage-API:

REST API: `POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}/virtualmachines/{instanceId}/reimage?api-version={apiVersion}` (Zie de [rest API documentatie](/rest/api/compute/virtualmachinescalesetvms/reimage)voor meer informatie)

Power shell: `Set-AzVmssVM -ResourceGroupName {resourceGroupName} -VMScaleSetName {vmScaleSetName} -InstanceId {instanceId} -Reimage` (Zie de [Power shell-documentatie](/powershell/module/az.compute/set-azvmssvm)voor meer informatie)

CLI: `az vmss reimage -g {resourceGroupName} -n {vmScaleSetName} --instance-id {instanceId}` (Zie de [cli-documentatie](/cli/azure/vmss?view=azure-cli-latest)voor meer informatie).

U kunt de lijst met exemplaar-Id's ophalen door alle exemplaren in een schaalset weer te geven:

REST API: `GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}/virtualMachines?api-version={apiVersion}` (Zie de [rest API documentatie](/rest/api/compute/virtualmachinescalesetvms/list)voor meer informatie)

Power shell: `Get-AzVmssVM -ResourceGroupName {resourceGroupName} -VMScaleSetName {vmScaleSetName}` (Zie de [Power shell-documentatie](/powershell/module/az.compute/get-azvmssvm)voor meer informatie)

CLI: `az vmss list-instances -g {resourceGroupName} -n {vmScaleSetName}` (Zie de [cli-documentatie](/cli/azure/vmss?view=azure-cli-latest)voor meer informatie).

U kunt ook [resources.Azure.com](https://resources.azure.com) of de [Azure-sdk's](https://azure.microsoft.com/downloads/) gebruiken om de virtuele machines in een schaalset weer te geven.

De exacte presentatie van de uitvoer is afhankelijk van de opties die u opgeeft voor de opdracht, maar hier volgt een voor beeld van de uitvoer van de CLI:

```azurecli
az vmss show -g {resourceGroupName} -n {vmScaleSetName}
```

```output
[
  {
    "instanceId": "85",
    "latestModelApplied": true,
    "location": "westus",
    "name": "nsgvmss_85",
    .
    .
    .
```

Zoals u ziet, is de eigenschap instanceId slechts een decimaal getal. De exemplaar-Id's kunnen opnieuw worden gebruikt voor nieuwe instanties wanneer de oude instanties worden verwijderd.

>[!NOTE]
> Er is **geen garantie** voor de manier waarop exemplaar-id's worden toegewezen aan de virtuele machines in de schaalset. Ze zijn mogelijk opeenvolgend in de tijd oplopend, maar dit is niet altijd het geval. Maak geen afhankelijkheid op de specifieke manier waarmee exemplaar-Id's worden toegewezen aan de Vm's.

## <a name="scale-set-vm-names"></a>VM-namen van schaal sets

In de voorbeeld uitvoer hierboven bevindt zich ook een ' naam ' voor de VM. Deze naam heeft de notatie {Scale-set-name} _ {exemplaar-id}. Dit is de naam die u ziet in de Azure Portal wanneer u instanties in een schaalset vermeldt:

![Scherm afbeelding met een lijst met exemplaren in een schaalset voor virtuele machines in de Azure Portal.](./media/virtual-machine-scale-sets-instance-ids/vmssInstances.png)

Het gedeelte {instance-id} van de naam is hetzelfde decimaal getal als de eigenschap instanceId die eerder is besproken.

## <a name="instance-metadata-vm-name"></a>VM-naam van instantie-meta gegevens

Als u de [meta gegevens](../virtual-machines/windows/instance-metadata-service.md) van het exemplaar van een schaalset-VM opvraagt, ziet u de naam in de uitvoer:

```output
{
  "compute": {
    "location": "westus",
    "name": "nsgvmss_85",
    .
    .
    .
```

Deze naam is hetzelfde als de eerder besproken naam.

## <a name="scale-set-vm-computer-name"></a>Computer naam van de VM-schaalset

Voor elke VM in een schaalset wordt ook een computer naam toegewezen. Deze computer naam is de hostnaam van de virtuele machine in de door [Azure aangelegde DNS-naam omzetting binnen het virtuele netwerk](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md). Deze computer naam heeft de notatie {computer-name-prefix} {base-36-instance-id}.

De {base-36-instance-id} bevindt zich in het [basis-36](https://en.wikipedia.org/wiki/Base36) en is altijd zes cijfers lang. Als de basis 36 weer gave van het aantal minder dan zes cijfers duurt, wordt {base-36-instance-id} opgevuld met nullen om de lengte zes cijfers te maken. Bijvoorbeeld: een exemplaar met {computer-name-prefix} "nsgvmss" en exemplaar-ID 85 heeft computer naam "nsgvmss00002D".

>[!NOTE]
> Het voor voegsel van de computer naam is een eigenschap van het model voor de schaalset dat u kunt instellen, zodat deze kan verschillen van de naam van de schaalset zelf.
