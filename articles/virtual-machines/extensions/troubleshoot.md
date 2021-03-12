---
title: Problemen met Windows VM-extensie fouten oplossen
description: Meer informatie over het oplossen van problemen met Azure Windows VM-extensie fouten
ms.topic: article
ms.service: virtual-machines
ms.subservice: extensions
author: amjads1
ms.author: amjads
ms.collection: windows
ms.date: 03/29/2016
ms.openlocfilehash: 8cc8a0b5ae0a83a152168b14a2c5577f8f7b48ab
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/10/2021
ms.locfileid: "102564795"
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


U kunt ook een nieuwe GoalState naar de virtuele machine activeren door een ' VM reapply ' uit te voeren. VM [opnieuw Toep assen](/rest/api/compute/virtualmachines/reapply) is een API die is geïntroduceerd in 2020 om de status van een virtuele machine opnieuw toe te passen. We raden u aan dit op een bepaald moment te doen wanneer u een korte uitval tijd van de VM kunt verdragen. Als zichzelf opnieuw wordt toegepast, is het niet mogelijk om een VM opnieuw op te starten en de meeste keren dat opnieuw wordt aangeroepen, wordt de VM niet opnieuw opgestart. er is een zeer klein risico dat een andere in behandeling zijnde update voor het VM-model wordt toegepast wanneer het opnieuw Toep assen van triggers een nieuwe doel status heeft en die andere wijziging kan vereisen dat de computer opnieuw wordt opgestart. 

Azure Portal:

Selecteer in de Portal de virtuele machine en klik in het linkerdeel venster onder **ondersteuning en probleem oplossing** op **opnieuw implementeren + opnieuw Toep assen** en selecteer **opnieuw Toep assen**.


Azure PowerShell *(Vervang de naam van RG en de VM-naam door de waarden)*:

```azurepowershell
Set-AzVM -ResourceGroupName <RG Name> -Name <VM Name> -Reapply
```

Azure CLI *(Vervang de naam en de VM-naam van RG door uw waarden)*:

```azurecli
az vm reapply -g <RG Name> -n <VM Name>
```

Als het niet lukt om een VM opnieuw toe te passen, kunt u een nieuwe, lege gegevens schijf toevoegen aan de VM vanuit de Azure-Beheerportal en deze later verwijderen zodra het certificaat weer is toegevoegd.