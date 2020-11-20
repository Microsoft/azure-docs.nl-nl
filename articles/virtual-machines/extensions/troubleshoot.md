---
title: Problemen met Windows VM-extensie fouten oplossen
description: Meer informatie over het oplossen van problemen met Azure Windows VM-extensie fouten
services: virtual-machines-windows
documentationcenter: ''
author: kundanap
manager: gwallace
editor: ''
tags: top-support-issue,azure-resource-manager
ms.assetid: 878ab9b6-c3e6-40be-82d4-d77fecd5030f
ms.service: virtual-machines-windows
ms.subservice: extensions
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 03/29/2016
ms.author: kundanap
ms.openlocfilehash: bca826cda8dfe47c341886faaf4a0d66f09d37d2
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/20/2020
ms.locfileid: "94966340"
---
# <a name="troubleshooting-azure-windows-vm-extension-failures"></a>Problemen met extensie fouten van Azure Windows VM oplossen
[!INCLUDE [virtual-machines-common-extensions-troubleshoot](../../../includes/virtual-machines-common-extensions-troubleshoot.md)]

## <a name="viewing-extension-status"></a>Uitbrei ding status weer geven
Azure Resource Manager sjablonen kunnen vanuit Azure PowerShell worden uitgevoerd. Zodra de sjabloon is uitgevoerd, kunt u de status van de uitbrei ding bekijken vanuit Azure Resource Explorer of de opdracht regel Programma's.

Hier volgt een voorbeeld:

Azure PowerShell:

```azurepowershell
Get-AzVM -ResourceGroupName $RGName -Name $vmName -Status
```

Hier volgt een voorbeeld van uitvoer:

```output
Extensions:  {
  "ExtensionType": "Microsoft.Compute.CustomScriptExtension",
  "Name": "myCustomScriptExtension",
  "SubStatuses": [
    {
      "Code": "ComponentStatus/StdOut/succeeded",
      "DisplayStatus": "Provisioning succeeded",
      "Level": "Info",
      "Message": "    Directory: C:\\temp\\n\\n\\nMode                LastWriteTime     Length Name
          \\n----                -------------     ------ ----                              \\n-a---          9/1/2015   2:03 AM         11
          test.txt                          \\n\\n",
                  "Time": null
      },
    {
      "Code": "ComponentStatus/StdErr/succeeded",
      "DisplayStatus": "Provisioning succeeded",
      "Level": "Info",
      "Message": "",
      "Time": null
    }
  ]
}
```

## <a name="troubleshooting-extension-failures"></a>Problemen met extensies oplossen

### <a name="rerun-the-extension-on-the-vm"></a>De uitbrei ding op de VM opnieuw uitvoeren
Als u scripts uitvoert op de VM met behulp van aangepaste script extensie, zou u soms een fout kunnen ondervinden waarbij de VM is gemaakt, maar het script is mislukt. Onder deze omstandigheden wordt de aanbevolen manier om deze fout te herstellen, de uitbrei ding te verwijderen en de sjabloon opnieuw uit te voeren.
Opmerking: in de toekomst zou deze functionaliteit worden uitgebreid om de installatie van de uitbrei ding te verwijderen.

#### <a name="remove-the-extension-from-azure-powershell"></a>De uitbrei ding verwijderen uit Azure PowerShell
```azurepowershell
Remove-AzVMExtension -ResourceGroupName $RGName -VMName $vmName -Name "myCustomScriptExtension"
```

Zodra de extensie is verwijderd, kan de sjabloon opnieuw worden uitgevoerd om de scripts op de virtuele machine uit te voeren.

### <a name="trigger-a-new-goalstate-to-the-vm"></a>Een nieuwe GoalState naar de virtuele machine activeren
U zult merken dat een uitbrei ding niet is uitgevoerd of niet kan worden uitgevoerd vanwege een ontbrekende ' Windows Azure CRP-certificaat Generator ' (dat certificaat wordt gebruikt voor het beveiligen van het Trans Port van de beveiligde instellingen van de extensie).
Het certificaat wordt automatisch opnieuw gegenereerd door de Windows-gast agent opnieuw op te starten in de virtuele machine:
- Open taak beheer
- Ga naar het tabblad Details
- Zoek het WindowsAzureGuestAgent.exe proces
- Klik met de rechter muisknop en selecteer taak beëindigen. Het proces wordt automatisch opnieuw gestart


U kunt ook een nieuwe GoalState naar de virtuele machine activeren door een lege update uit te voeren:

Azure PowerShell:

```azurepowershell
$vm = Get-AzureRMVM -ResourceGroupName <RGName> -Name <VMName>  
Update-AzureRmVM -ResourceGroupName <RGName> -VM $vm  
```

Azure CLI:

```azurecli
az vm update -g <rgname> -n <vmname>
```

Als een lege update niet werkt, kunt u een nieuwe, lege gegevens schijf toevoegen aan de VM vanuit de Azure Beheerportal en deze later verwijderen zodra het certificaat weer is toegevoegd.
