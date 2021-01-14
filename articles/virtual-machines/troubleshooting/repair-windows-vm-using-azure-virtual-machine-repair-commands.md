---
title: Een Windows-VM herstellen met behulp van de opdrachten voor het herstellen van virtuele Azure-machines | Microsoft Docs
description: In dit artikel wordt beschreven hoe u de herstel opdrachten van Azure VM kunt gebruiken om de schijf te verbinden met een andere Windows-VM om eventuele fouten op te lossen en vervolgens de oorspronkelijke VM opnieuw op te bouwen.
services: virtual-machines-windows
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: virtual-machines
ms.service: virtual-machines
ms.topic: troubleshooting
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: azurecli
ms.date: 09/10/2019
ms.author: v-miegge
ms.openlocfilehash: 7763c1d856a09acf2523a7073c3d300be6b70b37
ms.sourcegitcommit: 2bd0a039be8126c969a795cea3b60ce8e4ce64fc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/14/2021
ms.locfileid: "98200699"
---
# <a name="repair-a-windows-vm-by-using-the-azure-virtual-machine-repair-commands"></a>Een Windows-VM herstellen met de reparatieopdrachten van Azure Virtual Machine

Als op uw virtuele Windows-machine (VM) in azure een opstart-of schijf fout optreedt, moet u mogelijk de schijf zelf beperken. Een veelvoorkomend voor beeld hiervan is een mislukte toepassings update waarmee wordt voor komen dat de virtuele machine kan worden opgestart. In dit artikel wordt beschreven hoe u de herstel opdrachten van Azure VM kunt gebruiken om de schijf te verbinden met een andere Windows-VM om eventuele fouten op te lossen en vervolgens de oorspronkelijke VM opnieuw op te bouwen.

> [!IMPORTANT]
> * De scripts in dit artikel zijn alleen van toepassing op de virtuele machines die gebruikmaken van [Azure Resource Manager](../../azure-resource-manager/management/overview.md).
> * De uitgaande verbinding van de virtuele machine (poort 443) is vereist om het script uit te voeren.
> * Er kan slechts één script tegelijk worden uitgevoerd.
> * Een actief script kan niet worden geannuleerd.
> * De maximale tijd dat een script kan worden uitgevoerd, is 90 minuten, waarna er een time-out optreedt.
> * Voor Vm's met Azure Disk Encryption worden alleen beheerde schijven ondersteund die zijn versleuteld met eenmalige versleuteling (met of zonder KEK).


## <a name="repair-process-overview"></a>Overzicht van het reparatie proces

U kunt nu de reparatie opdrachten van Azure VM gebruiken om de besturingssysteem schijf voor een virtuele machine te wijzigen en u hoeft de virtuele machine niet meer te verwijderen en opnieuw te maken.

Volg deze stappen om het VM-probleem op te lossen:

1. Azure Cloud Shell starten
2. Voer AZ extension add/update uit.
3. Voer AZ VM Repair Create.
4. Voer AZ VM Repair run uit of voer beperkings stappen uit.
5. Voer AZ VM Repair Restore.

Zie [AZ VM Repair](/cli/azure/ext/vm-repair/vm/repair)(Engelstalig) voor aanvullende documentatie en instructies.

## <a name="repair-process-example"></a>Voor beeld van reparatie proces

1. Azure Cloud Shell starten

   Azure Cloud Shell is een gratis interactieve shell waarmee u de stappen in dit artikel kunt uitvoeren. Het bevat algemene Azure-hulpprogram ma's die vooraf zijn geïnstalleerd en geconfigureerd voor gebruik met uw account.

   Als u de Cloud Shell wilt openen, selecteert u **deze** in de rechter bovenhoek van een code blok. U kunt Cloud Shell ook openen op een afzonderlijk browser tabblad door te bezoeken [https://shell.azure.com](https://shell.azure.com) .

   Selecteer **kopiëren** om de blokken code te kopiëren en plak de code in het Cloud shell en selecteer **Enter** om het programma uit te voeren.

   Als u ervoor kiest om de CLI lokaal te installeren en te gebruiken, hebt u voor deze snelstart versie 2.0.30 of hoger van Azure CLI nodig. Voer ``az --version`` uit om de versie te bekijken. Als u Azure CLI wilt installeren of upgraden, raadpleegt u [Azure cli installeren](/cli/azure/install-azure-cli).
   
   Als u zich moet aanmelden bij Cloud Shell met een ander account dan u momenteel bent aangemeld bij de Azure Portal met kunt u referenties voor ``az login`` [Aanmelden](/cli/azure/reference-index#az-login&preserve-view=true)gebruiken.  Als u wilt scha kelen tussen de abonnementen die zijn gekoppeld aan uw account, kunt u ``az account set --subscription`` [AZ account set reference](/cli/azure/account#az-account-set&preserve-view=true)gebruiken.

2. Als dit de eerste keer is dat u de `az vm repair` opdrachten gebruikt, voegt u de extensie VM-herstel cli toe.

   ```azurecli-interactive
   az extension add -n vm-repair
   ```

   Als u de opdrachten eerder hebt gebruikt `az vm repair` , moet u alle updates Toep assen op de extensie voor VM-herstel.

   ```azurecli-interactive
   az extension update -n vm-repair
   ```

3. Voer `az vm repair create` uit. Met deze opdracht maakt u een kopie van de besturingssysteem schijf voor de niet-functionele VM, maakt u een herstel-VM in een nieuwe resource groep en koppelt u de kopie van de besturingssysteem schijf.  De herstel-VM heeft dezelfde grootte en regio als de niet-functionele VM die is opgegeven. De resource groep en de VM-naam die in alle stappen worden gebruikt, zijn voor de niet-functionele VM. Als uw virtuele machine wordt gebruikt Azure Disk Encryption wordt met de opdracht geprobeerd de versleutelde schijf te ontgrendelen, zodat deze toegankelijk is wanneer deze wordt gekoppeld aan de reparatie-VM.

   ```azurecli-interactive
   az vm repair create -g MyResourceGroup -n myVM --repair-username username --repair-password 'password!234' --verbose
   ```

4. Voer `az vm repair run` uit. Met deze opdracht wordt het opgegeven herstel script op de gekoppelde schijf uitgevoerd via de reparatie-VM. Als in de hand leiding voor probleem oplossing die u gebruikt, een run-id wordt gebruikt, kunt u deze hier gebruiken `az vm repair list-scripts` om beschik bare herstel scripts te bekijken. De resource groep en de VM-naam die hier worden gebruikt voor de niet-functionele VM die u in stap 3 gebruikt. Meer informatie over de herstel scripts vindt u in de [herstel script bibliotheek](https://github.com/Azure/repair-script-library).

   ```azurecli-interactive
   az vm repair run -g MyResourceGroup -n MyVM --run-on-repair --run-id win-hello-world --verbose
   ```
   
   U kunt eventueel benodigde hand matige stappen voor het oplossen van problemen uitvoeren met de reparatie-VM en vervolgens door gaan naar stap 5.

5. Voer `az vm repair restore` uit. Met deze opdracht wordt de gerepareerde besturingssysteem schijf vervangen door de oorspronkelijke besturingssysteem schijf van de virtuele machine. De resource groep en de VM-naam die hier worden gebruikt voor de niet-functionele VM die u in stap 3 gebruikt.

   ```azurecli-interactive
   az vm repair restore -g MyResourceGroup -n MyVM --verbose
   ```

## <a name="verify-and-enable-boot-diagnostics"></a>Diagnostische gegevens over opstarten controleren en inschakelen

In het volgende voor beeld wordt de diagnostische uitbrei ding ingeschakeld op de virtuele machine ``myVMDeployed`` met de naam in de resource groep met de naam ``myResourceGroup`` :

Azure CLI

```azurecli-interactive
az vm boot-diagnostics enable --name myVMDeployed --resource-group myResourceGroup --storage https://mystor.blob.core.windows.net/
```

## <a name="next-steps"></a>Volgende stappen

* Zie [problemen met RDP-verbindingen met een Azure VM oplossen](./troubleshoot-rdp-connection.md)als u problemen ondervindt bij het maken van verbinding met uw virtuele machine.
* Zie problemen met [toepassings connectiviteit oplossen op virtuele machines in azure](./troubleshoot-app-connection.md)voor problemen met het openen van toepassingen die op uw virtuele machine worden uitgevoerd.
* Zie [Azure Resource Manager Overview](../../azure-resource-manager/management/overview.md)voor meer informatie over het gebruik van Resource Manager.
