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
ms.openlocfilehash: 82318a94a6a095016fe1177ee486f035d101589c
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107776780"
---
U kunt versleuteling uitschakelen Azure PowerShell, de Azure CLI of met een Resource Manager sjabloon. Het uitschakelen van gegevensschijfversleuteling voor de Windows-VM wanneer zowel de besturingssysteem als gegevensschijven zijn versleuteld, werkt niet zoals verwacht. Schakel in plaats daarvan versleuteling op alle schijven uit.

- **Schijfversleuteling uitschakelen met Azure PowerShell:** Als u de versleuteling wilt uitschakelen, gebruikt u de cmdlet [Disable-AzVMDiskEncryption.](/powershell/module/az.compute/disable-azvmdiskencryption) 
     ```azurepowershell-interactive
     Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM' -VolumeType "all"
     ```

- **Versleuteling uitschakelen met de Azure CLI:** Als u versleuteling wilt uitschakelen, gebruikt [u de opdracht az vm encryption disable.](/cli/azure/vm/encryption#az_vm_encryption_disable) 
     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type "all"
     ```
- **Versleuteling uitschakelen met een Resource Manager sjabloon:** 

    1. Klik **op Implementeren in Azure in** de sjabloon [Schijfversleuteling uitschakelen bij het uitvoeren van een Windows-VM.](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-windows-vm-without-aad)
    2. Selecteer het abonnement, de resourcegroep, de locatie, de VM, het volumetype, de juridische voorwaarden en de overeenkomst.
    3.  Klik **op Kopen om** schijfversleuteling uit te schakelen op een windows-VM die wordt uitgevoerd. 
