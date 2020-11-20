---
title: Windows-extensie voor VM-moment opname voor Azure Backup
description: Toepassings consistente back-up van de virtuele machine maken van Azure Backup met behulp van de extensie van de VM-moment opname
services: backup, virtual-machines-windows
documentationcenter: ''
author: trinadhkotturu
manager: gwallace
ms.service: virtual-machines-windows
ms.subservice: extensions
ms.topic: article
ms.date: 10/15/2020
ms.author: trinadhk
ms.openlocfilehash: 1d1342cffc6bc8743c34cb4c028c0790de91dc50
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/20/2020
ms.locfileid: "94965864"
---
# <a name="vm-snapshot-windows-extension-for-azure-backup"></a>Windows-extensie voor VM-moment opname voor Azure Backup

Azure Backup biedt ondersteuning voor het maken van back-ups van werk belastingen van on-premises naar de Cloud en het maken van back-ups van Cloud bronnen naar Recovery Services kluis. Azure Backup maakt gebruik van VM snap shot extension om een toepassings consistente back-up te maken van de virtuele machine van Azure, zonder dat u de VM hoeft te afsluiten. De extensie van de VM-moment opname wordt gepubliceerd en ondersteund door micro soft als onderdeel van Azure Backup service. Azure Backup wordt de uitbrei ding geïnstalleerd als onderdeel van de eerste geplande back-up geactiveerd na het inschakelen van back-ups. In dit document vindt u informatie over de ondersteunde platforms, configuraties en implementatie opties voor de VM-momentopname extensie.

De uitbrei ding VMSnapshot wordt alleen weer gegeven in de Azure Portal voor niet-beheerde Vm's.

## <a name="prerequisites"></a>Vereisten

### <a name="operating-system"></a>Besturingssysteem
Zie [besturings systemen die worden ondersteund door Azure backup](../../backup/backup-azure-arm-vms-prepare.md#before-you-start) voor een lijst met ondersteunde besturings systemen

## <a name="extension-schema"></a>Extensieschema

De volgende JSON toont het schema voor de extensie van de VM-moment opname. Voor de uitbrei ding is de taak-ID vereist. Hiermee wordt de back-uptaak geïdentificeerd die de moment opname op de virtuele machine heeft geactiveerd, de URI van de status-blob: waar de status van de momentopname bewerking is geschreven, de geplande begin tijd van de moment opname, logboeken van de BLOB-URI: de logboeken die overeenkomen met de momentopname taak worden geschreven, objstr  Omdat deze instellingen moeten worden behandeld als gevoelige gegevens, moet deze worden opgeslagen in een configuratie met een beveiligde instelling. De beveiligde instellings gegevens voor de Azure VM-extensie zijn versleuteld en worden alleen ontsleuteld op de virtuele doel machine. Houd er rekening mee dat deze instellingen worden aanbevolen van Azure Backup Service alleen worden door gegeven als onderdeel van de back-uptaak.

```json
{
  "type": "extensions",
  "name": "VMSnapshot",
  "location":"<myLocation>",
  "properties": {
    "publisher": "Microsoft.Azure.RecoveryServices",
    "type": "VMSnapshot",
    "typeHandlerVersion": "1.9",
    "autoUpgradeMinorVersion": true,
    "settings": {
      "locale":"<location>",
      "taskId":"<taskId used by Azure Backup service to communicate with extension>",
      "commandToExecute": "snapshot",
      "commandStartTimeUTCTicks": "<scheduled start time of the snapshot task>",
      "vmType": "microsoft.compute/virtualmachines"
    },
    "protectedSettings": {
      "objectStr": "<blob SAS uri representation of VM sent by Azure Backup service to extension>",
      "logsBlobUri": "<blob uri where logs of command execution by extension are written to>",
      "statusBlobUri": "<blob uri where status of the command executed by extension is written>"
    }
  }
}
```

### <a name="property-values"></a>Eigenschaps waarden

| Name | Waarde/voor beeld | Gegevenstype |
| ---- | ---- | ---- |
| apiVersion | 2015-06-15 | datum |
| taskId | e07354cf-041e-4370-929f-25a319ce8933_1 | tekenreeks |
| commandStartTimeUTCTicks | 6.36458 e + 17 | tekenreeks |
| landinstelling | nl-nl | tekenreeks |
| objectStr | Encoding of sas uri array- "blobSASUri": ["https: \/ \/ sopattna5365.blob.core.windows.net \/ vhds \/ vmwin1404ltsc201652903941.vhd?sv=2014-02-14&sr=b&sig=TywkROXL1zvhXcLujtCut8g3jTpgbE6JpSWRLZxAdtA%3D&st=2017-11-09T14%3A23%3A28Z&se=2017-11-09T17%3A38%3A28Z&sp=rw", "https: \/ \/ sopattna8461.blob.core.windows.net \/ vhds \/ vmwin1404ltsc-20160629-122418.vhd?sv=2014-02-14&sr=b&sig=5S0A6YDWvVwqPAkzWXVy%2BS%2FqMwzFMbamT5upwx05v8Q%3D&st=2017-11-09T14%3A23%3A28Z&se=2017-11-09T17%3A38%3A28Z&sp=rw", "https: \/ \/ sopattna8461.blob.core.windows.net \/ bootdiagnostics-vmwintu1-deb58392-ed5e-48be-9228-ff681b0cd3ee \/ vmubuntu1404ltsc-20160629-122541.vhd?sv=2014-02-14&sr=b&sig=X0Me2djByksBBMVXMGIUrcycvhQSfjYvqKLeRA7nBD4%3D&st=2017-11-09T14%3A23%3A28Z&se=2017-11-09T17%3A38%3A28Z&sp=rw", "https: \/ \/ sopattna5365.blob.core.windows.net \/ vhds \/ vmwin1404ltsc-20160701-163922.vhd?sv=2014-02-14&sr=b&sig=oXvtK2IXCNqWv7fpjc7TAzFDpc1GoXtT7r%2BC%2BNIAork%3D&st=2017-11-09T14%3A23%3A28Z&se=2017-11-09T17%3A38%3A28Z&sp=rw", "https: \/ \/ sopattna5365.blob.core.windows.net \/ vhds \/ vmwin1404ltsc-20170705-124311.vhd?sv=2014-02-14&sr=b&sig=ZUM9d28Mvvm%2FfrhJ71TFZh0Ni90m38bBs3zMl%2FQ9rs0%3D&st=2017-11-09T14%3A23%3A28Z&se=2017-11-09T17%3A38%3A28Z&sp=rw"] | tekenreeks |
| logsBlobUri | https://seapod01coord1exsapk732.blob.core.windows.net/bcdrextensionlogs-d45d8a1c-281e-4bc8-9d30-3b25176f68ea/sopattna-vmubuntu1404ltsc.v2.Logs.txt?sv=2014-02-14&sr=b&sig=DbwYhwfeAC5YJzISgxoKk%2FEWQq2AO1vS1E0rDW%2FlsBw%3D&st=2017-11-09T14%3A33%3A29Z&se=2017-11-09T17%3A38%3A29Z&sp=rw | tekenreeks |
| statusBlobUri | https://seapod01coord1exsapk732.blob.core.windows.net/bcdrextensionlogs-d45d8a1c-281e-4bc8-9d30-3b25176f68ea/sopattna-vmubuntu1404ltsc.v2.Status.txt?sv=2014-02-14&sr=b&sig=96RZBpTKCjmV7QFeXm5IduB%2FILktwGbLwbWg6Ih96Ao%3D&st=2017-11-09T14%3A33%3A29Z&se=2017-11-09T17%3A38%3A29Z&sp=rw | tekenreeks |



## <a name="template-deployment"></a>Sjabloonimplementatie

Azure VM-extensies kunnen worden geïmplementeerd met Azure Resource Manager sjablonen. De aanbevolen manier om een VM-momentopname extensie toe te voegen aan een virtuele machine is echter door back-up op de virtuele machine in te scha kelen. Dit kan worden bereikt via een resource manager-sjabloon.  Een voor beeld van een resource manager-sjabloon waarmee back-ups op een virtuele machine kunnen worden gemaakt, vindt u in de [Azure Quick Start-galerie](https://azure.microsoft.com/resources/templates/101-recovery-services-backup-vms/).


## <a name="azure-cli-deployment"></a>Implementatie van Azure CLI

De Azure CLI kan worden gebruikt om back-ups op een virtuele machine in te scha kelen. Post enable backup, eerste geplande back-uptaak installeert de VM snap shot-extensie op de VM.

```azurecli
az backup protection enable-for-vm \
    --resource-group myResourceGroup \
    --vault-name myRecoveryServicesVault \
    --vm myVM \
    --policy-name DefaultPolicy
```

## <a name="troubleshoot-and-support"></a>Problemen oplossen en ondersteuning

### <a name="troubleshoot"></a>Problemen oplossen

Gegevens over de status van uitbreidings implementaties kunnen worden opgehaald uit het Azure Portal en met behulp van de Azure CLI. Als u de implementatie status van uitbrei dingen voor een bepaalde virtuele machine wilt bekijken, voert u de volgende opdracht uit met behulp van de Azure CLI.

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

Uitvoer voor uitvoering van extensie wordt vastgelegd in het volgende bestand:

```
C:\Packages\Plugins\Microsoft.Azure.RecoveryServices.VMSnapshot
```

### <a name="error-codes-and-their-meanings"></a>Fout codes en hun betekenis

Informatie over het oplossen van problemen vindt u in de [hand leiding voor het oplossen van back-ups van Azure VM](../../backup/backup-azure-vms-troubleshoot.md).

### <a name="support"></a>Ondersteuning

Als u op elk moment in dit artikel meer hulp nodig hebt, kunt u contact opnemen met de Azure-experts op [MSDN Azure en stack overflow forums](https://azure.microsoft.com/support/forums/). U kunt ook een ondersteunings incident voor Azure opslaan. Ga naar de [ondersteunings site van Azure](https://azure.microsoft.com/support/options/) en selecteer ondersteuning verkrijgen. Lees de [Veelgestelde vragen over ondersteuning voor Microsoft Azure](https://azure.microsoft.com/support/faq/)voor meer informatie over het gebruik van Azure-ondersteuning.
