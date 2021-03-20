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
ms.openlocfilehash: 1e3cdfccc36549d2aa127636b15b698a4e19ccf0
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "85610558"
---
U kunt versleuteling uitschakelen met behulp van Azure PowerShell, de Azure CLI of met een resource manager-sjabloon. Het uitschakelen van gegevensschijfversleuteling voor de Windows-VM wanneer zowel de besturingssysteem als gegevensschijven zijn versleuteld, werkt niet zoals verwacht. Schakel versleuteling uit op alle schijven.

- **Schijf versleuteling uitschakelen met Azure PowerShell:** Als u de versleuteling wilt uitschakelen, gebruikt u de cmdlet [Disable-AzVMDiskEncryption](/powershell/module/az.compute/disable-azvmdiskencryption) . 
     ```azurepowershell-interactive
     Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM' -VolumeType "all"
     ```

- **Versleuteling uitschakelen met Azure cli:** Als u versleuteling wilt uitschakelen, gebruikt u de opdracht [AZ VM Encryption Disable](/cli/azure/vm/encryption#az-vm-encryption-disable) . 
     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type "all"
     ```
- **Versleuteling uitschakelen met een resource manager-sjabloon:** 

    1. Klik op **implementeren naar Azure** van de sjabloon [voor het uitvoeren van schijf versleuteling op een Windows-VM](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-windows-vm-without-aad) .
    2. Selecteer het abonnement, de resource groep, de locatie, de VM, het volume type, de juridische voor waarden en de overeenkomst.
    3.  Klik op **aanschaffen** om schijf versleuteling uit te scha kelen op een actieve Windows-VM. 
