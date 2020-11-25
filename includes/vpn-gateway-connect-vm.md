---
title: bestand opnemen
description: bestand opnemen
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 10/23/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 6986651d2b48d82382aa26a0a3947104c7e09981
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96026911"
---
U kunt verbinding maken met een VM die op uw VNet is geïmplementeerd door een verbinding met extern bureaublad te maken voor uw VM. De beste manier om eerst te controleren of u verbinding met uw VM kunt maken is door verbinding te maken met behulp van het privé-IP-adres in plaats van de computernaam. Op die manier test u of u verbinding kunt maken, niet of naamomzetting correct is geconfigureerd.

1. Zoek het privé-IP-adres. U vindt het privé-IP-adres van een VM door naar de eigenschappen voor de VM te kijken in Azure Portal of door PowerShell te gebruiken.

   * Azure Portal: vind uw virtuele machine in Azure Portal. Bekijk de eigenschappen voor de VM. Het privé-IP-adres wordt vermeld.

   * PowerShell: gebruik het voorbeeld om een lijst met VM's en privé-IP-adressen uit uw resourcegroepen weer te geven. U hoeft het voorbeeld niet te wijzigen voordat u het gebruikt.

     ```azurepowershell-interactive
     $VMs = Get-AzVM
     $Nics = Get-AzNetworkInterface | Where VirtualMachine -ne $null

     foreach($Nic in $Nics)
     {
      $VM = $VMs | Where-Object -Property Id -eq $Nic.VirtualMachine.Id
      $Prv = $Nic.IpConfigurations | Select-Object -ExpandProperty PrivateIpAddress
      $Alloc = $Nic.IpConfigurations | Select-Object -ExpandProperty PrivateIpAllocationMethod
      Write-Output "$($VM.Name): $Prv,$Alloc"
     }
     ```

1. Controleer of u met uw VNet bent verbonden met behulp van de punt-naar-site-VPN-verbinding.
1. Open **Verbinding met extern bureaublad door** 'RDP' of 'Verbinding met extern bureaublad' te typen in het zoekvak in de taakbalk en selecteer vervolgens Verbinding met extern bureaublad. U kunt Verbinding met extern bureaublad ook openen met behulp van de opdracht 'mstsc' in PowerShell. 
1. Voer het privé-IP-adres van de VM in Verbinding met extern bureaublad in. U kunt op Opties weergeven klikken om aanvullende instellingen aan te passen en vervolgens verbinding maken.

**Problemen met een verbinding oplossen**

Als u problemen ondervindt bij het verbinding maken met een virtuele machine via de VPN-verbinding, controleert u het volgende:

* Controleer of uw VPN-verbinding tot stand is gebracht.

* Controleer of u verbinding maakt met het privé-IP-adres voor de VM.

* Als u verbinding met de VM kunt maken met behulp van het privé-IP-adres, maar niet met de computernaam, controleert u of DNS correct is geconfigureerd. Zie [Naamomzetting voor VM's](../articles/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md) voor meer informatie over de werking van naamomzetting voor VM's.

* Zie [Problemen met Extern-bureaubladverbindingen met een VM oplossen](../articles/virtual-machines/troubleshooting/troubleshoot-rdp-connection.md) voor meer informatie over Extern-bureaubladverbindingen.