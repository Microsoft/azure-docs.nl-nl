---
title: bestand opnemen
description: bestand opnemen
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 06/19/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 37571a82ca73342b8edfc4702686ccd9091887c4
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107771497"
---
U kunt versleuteling uitschakelen Azure PowerShell, de Azure CLI of met een Resource Manager sjabloon. 

>[!IMPORTANT]
>Het uitschakelen van versleuteling Azure Disk Encryption linux-VM's wordt alleen ondersteund voor gegevensvolumes. Het wordt niet ondersteund op gegevens- of besturingssysteemvolumes als het besturingssysteemvolume is versleuteld.  

- **Schijfversleuteling uitschakelen met Azure PowerShell:** Als u de versleuteling wilt uitschakelen, gebruikt u de cmdlet [Disable-AzVMDiskEncryption.](/powershell/module/az.compute/disable-azvmdiskencryption) 
     ```azurepowershell-interactive
     Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM' [-VolumeType DATA]
     ```

- **Versleuteling uitschakelen met de Azure CLI:** Als u versleuteling wilt uitschakelen, gebruikt [u de opdracht az vm encryption disable.](/cli/azure/vm/encryption#az_vm_encryption_disable) 
     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type DATA
     ```
- **Versleuteling uitschakelen met een Resource Manager sjabloon:** Gebruik de [sjabloon Versleuteling uitschakelen op een linux-VM-sjabloon](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-linux-vm-without-aad) die wordt uitgevoerd om versleuteling uit te schakelen.
     1. Klik op **Implementeren in Azure**.
     2. Selecteer het abonnement, de resourcegroep, de locatie, de VM, de juridische voorwaarden en de overeenkomst.
