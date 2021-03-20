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
ms.openlocfilehash: ed2b13077ff5809899097254dde0fa8406e1db3b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "85601378"
---
U kunt versleuteling uitschakelen met behulp van Azure PowerShell, de Azure CLI of met een resource manager-sjabloon. 

>[!IMPORTANT]
>Het uitschakelen van versleuteling met Azure Disk Encryption op Linux Vm's wordt alleen ondersteund voor gegevens volumes. Het wordt niet ondersteund op gegevens of besturingssysteem volumes als het volume van het besturings systeem is versleuteld.  

- **Schijf versleuteling uitschakelen met Azure PowerShell:** Als u de versleuteling wilt uitschakelen, gebruikt u de cmdlet [Disable-AzVMDiskEncryption](/powershell/module/az.compute/disable-azvmdiskencryption) . 
     ```azurepowershell-interactive
     Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM' [-VolumeType DATA]
     ```

- **Versleuteling uitschakelen met Azure cli:** Als u versleuteling wilt uitschakelen, gebruikt u de opdracht [AZ VM Encryption Disable](/cli/azure/vm/encryption#az-vm-encryption-disable) . 
     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type DATA
     ```
- **Versleuteling uitschakelen met een resource manager-sjabloon:** Gebruik de sjabloon [versleuteling uitschakelen in een actieve Linux-VM](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-linux-vm-without-aad) om versleuteling uit te scha kelen.
     1. Klik op **Implementeren in Azure**.
     2. Selecteer het abonnement, de resource groep, de locatie, de virtuele machine, de juridische voor waarden en de overeenkomst.
