---
title: Voor beeld van Azure CLI voor het beheer van virtuele-machine schaal sets
description: In dit voor beeld ziet u hoe u schijven toevoegt aan een schaalset voor virtuele machines. U kunt schijven upgraden en uw virtuele machines toevoegen aan Azure AD-verificatie.
author: mimckitt
ms.author: mimckitt
ms.date: 02/04/2021
ms.topic: sample
ms.service: virtual-machine-scale-sets
ms.devlang: azurecli
ms.custom: devx-track-azurecli
ms.openlocfilehash: 1abdf7ae15753d78ac8728f57e9b0cd5dcd9165e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101672599"
---
# <a name="create-and-manage-virtual-machine-scale-set"></a>Een schaalset voor virtuele machines maken en beheren

Gebruik deze voorbeeld opdrachten voor het prototypen van een schaalset voor virtuele machines met behulp van Azure CLI.

Deze voorbeeld opdrachten demonstreren de volgende bewerkingen:

* Maak een virtuele-machineschaalset.
* Nieuwe of bestaande schijven toevoegen en upgraden naar een schaalset of naar een exemplaar van de set.
* Schaalset toevoegen aan Azure Active Directory-verificatie (Azure AD).

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

## <a name="sample-commands"></a>Voorbeeld opdrachten

```azurecli
# Create a resource group
az group create --name MyResourceGroup --location eastus

# Create virtual machine scale set
az vmss create --resource-group MyResourceGroup --name myScaleSet --instance-count 2 \
    --image UbuntuLTS --upgrade-policy-mode automatic --admin-username azureuser \
    --generate-ssh-keys

# Attach a new managed disk to your scale set
az vmss disk attach --resource-group MyResourceGroup --vmss-name myScaleSet --size-gb 50
```

Nadat u een nieuwe gegevens schijf hebt toegevoegd, formatteert u de schijf en koppelt u deze. Zie [een beheerde gegevens schijf koppelen aan een Windows-VM met behulp van de Azure Portal](../../virtual-machines/windows/attach-managed-disk-portal.md)voor virtuele Windows-machines. Zie [een schijf toevoegen aan een virtuele Linux-machine](../../virtual-machines/linux/add-disk.md)voor Linux virtual machines.

```azurecli
# Attach an existing managed disk to a virtual machine instance in your scale set
az vmss disk attach --resource-group MyResourceGroup --disk myDataDisk \
    --vmss-name myScaleSet --instance-id 0

# See the instances in your virtual machine scale set
az vmss list-instances --resource-group MyResourceGroup --name myScaleSet --output table

# See the disks for your virtual machine
az disk list --resource-group MyResourceGroup \
    --query "[*].{Name:name,Gb:diskSizeGb,Tier:accountType}" --output table

# Deallocate the virtual machine
az vmss deallocate --resource-group MyResourceGroup --name myScaleSet --instance-ids 0 

# Resize the disk
az disk update --resource-group MyResourceGroup --name myDataDisk --size-gb 200

# Restart the disk
az vmss restart --resource-group MyResourceGroup --name myScaleSet --instance-ids 0
```

Als u de uitgevouwen schijf wilt gebruiken, vouwt u de onderliggende partitie uit. Zie [een schijf partitie en bestands systeem uitbreiden](../../virtual-machines/linux/expand-disks.md#expand-a-disk-partition-and-filesystem)voor meer informatie.

In dit voor beeld wordt de grootte van een gegevens schijf gewijzigd. U kunt dezelfde procedure gebruiken om een besturingssysteem schijf bij te werken. Zie [het station van het besturings systeem van een virtuele machine uitbreiden](../../virtual-machines/windows/expand-os-disk.md)voor meer informatie over een virtuele Windows-machine. Zie voor meer informatie over virtuele Linux-machines [virtuele harde schijven op een Linux-VM uitbreiden met de Azure cli](../../virtual-machines/linux/expand-disks.md).

```azurecli
# Enable managed service identity on your scale set. This is required to authenticate and interact with other Azure services using bearer tokens.
az vmss identity assign --resource-group MyResourceGroup --name myScaleSet --role Owner \
    --scope /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/MyResourceGroup

# Connect to Azure AD authentication
az vmss extension set --resource-group MyResourceGroup --name AADLoginForWindows \
    --publisher Microsoft.Azure.ActiveDirectory --vmss-name myScaleSet

# Upgrade one instance of a scale set virtual machine
az vmss update-instances --resource-group MyResourceGroup --name myScaleSet --instance-ids 0 

# Remove a managed disk from the scale set
az vmss disk detach --resource-group MyResourceGroup --vmss-name myScaleSet --lun 0

# Remove a managed disk from an instance
az vmss disk detach --resource-group MyResourceGroup --vmss-name myScaleSet --instance-id 1 --lun 0

# Delete the pre-existing disk
az disk delete --resource-group MyResourceGroup --disk myDataDisk
```

## <a name="clean-up-resources"></a>Resources opschonen

Nadat u deze opdrachten hebt gebruikt, voert u de volgende opdracht uit om de resource groep en alle bijbehorende resources te verwijderen.

```azurecli
az group delete --name MyResourceGroup
```

## <a name="azure-cli-references-used-in-this-article"></a>Azure CLI-verwijzingen die in dit artikel worden gebruikt

* [AZ Disk Delete](/cli/azure/disk#az_disk_delete)
* [AZ Disk List](/cli/azure/disk#az_disk_list)
* [AZ Disk Update](/cli/azure/disk#az_disk_update)
* [az group create](/cli/azure/group#az_group_create)
* [az vmss create](/cli/azure/vmss#az_vmss_create)
* [de toewijzing van de virtuele-machine schaalset AZ ongedaan maken](/cli/azure/vmss#az_vmss_deallocate)
* [az vmss disk attach](/cli/azure/vmss/disk#az_vmss_disk_attach)
* [AZ vmss Disk Detach](/cli/azure/vmss/disk#az_vmss_disk_detach)
* [az vmss extension set](/cli/azure/vmss/extension#az_vmss_extension_set)
* [AZ vmss Identity Assign](/cli/azure/vmss/identity#az_vmss_identity_assign)
* [AZ vmss list-instances](/cli/azure/vmss#az_vmss_list_instances)
* [AZ vmss restart](/cli/azure/vmss#az_vmss_restart)
* [az vmss update-instances](/cli/azure/vmss#az_vmss_update_instances)