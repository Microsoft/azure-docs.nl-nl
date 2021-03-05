---
title: Virtuele machines in DevTest Labs maken en beheren met Azure CLI
description: Meer informatie over het gebruik van Azure DevTest Labs voor het maken en beheren van virtuele machines met Azure CLI
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 22ee6bf607fe1b66cece0e7ddb25a2da2830258b
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2021
ms.locfileid: "102201461"
---
# <a name="create-and-manage-virtual-machines-with-devtest-labs-using-the-azure-cli"></a>Virtuele machines maken en beheren met DevTest Labs met behulp van Azure CLI
Deze Snelstartgids helpt u bij het maken, starten, aansluiten, bijwerken en opschonen van een ontwikkel machine in uw Lab. 

Voordat u begint:

* Als een Lab niet is gemaakt, kunt u [hier](devtest-lab-create-lab.md)instructies vinden.

* [Installeer de Azure CLI](/cli/azure/install-azure-cli). Als u wilt starten, voert u AZ login uit om een verbinding te maken met Azure. 

## <a name="create-and-verify-the-virtual-machine"></a>De virtuele machine maken en verifiëren 
Voordat u DevTest Labs-gerelateerde opdrachten uitvoert, stelt u de juiste Azure-context in met behulp van de `az account set` opdracht:

```azurecli
az account set --subscription 11111111-1111-1111-1111-111111111111
```

De opdracht voor het maken van een virtuele machine is: `az lab vm create` . De resource groep voor het lab, de naam van het lab en de naam van de virtuele machine zijn allemaal verplicht. De overige argumenten veranderen, afhankelijk van het type virtuele machine.

Met de volgende opdracht maakt u een Windows-installatie kopie op basis van de Azure-markt plaats. De naam van de installatie kopie is hetzelfde als die u ziet bij het maken van een virtuele machine met behulp van de Azure Portal. 

```azurecli
az lab vm create --resource-group DtlResourceGroup --lab-name MyLab --name 'MyTestVm' --image "Visual Studio Community 2017 on Windows Server 2016 (x64)" --image-type gallery --size 'Standard_D2s_v3' --admin-username 'AdminUser' --admin-password 'Password1!'
```

Met de volgende opdracht maakt u een virtuele machine op basis van een aangepaste installatie kopie die beschikbaar is in het Lab:

```azurecli
az lab vm create --resource-group DtlResourceGroup --lab-name MyLab --name 'MyTestVm' --image "My Custom Image" --image-type custom --size 'Standard_D2s_v3' --admin-username 'AdminUser' --admin-password 'Password1!'
```

Het argument **Image-Type** is gewijzigd van de **Galerie** in **aangepast**. De naam van de afbeelding komt overeen met wat u ziet als u de virtuele machine in de Azure Portal hebt gemaakt.

Met de volgende opdracht maakt u een virtuele machine op basis van een Marketplace-installatie kopie met SSH-verificatie:

```azurecli
az lab vm create --lab-name sampleLabName --resource-group sampleLabResourceGroup --name sampleVMName --image "Ubuntu Server 16.04 LTS" --image-type gallery --size Standard_DS1_v2 --authentication-type  ssh --generate-ssh-keys --ip-configuration public 
```

U kunt ook virtuele machines maken op basis van formules door de para meter **Image-Type** in te stellen op **Formula**. Als u voor uw virtuele machine een specifiek virtueel netwerk wilt kiezen, gebruikt u de para meters **vnet-naam** en **subnet** . Zie [AZ Lab VM Create](/cli/azure/lab/vm#az-lab-vm-create)voor meer informatie.

## <a name="verify-that-the-vm-is-available"></a>Controleer of de virtuele machine beschikbaar is.
Gebruik de `az lab vm show` opdracht om te controleren of de virtuele machine beschikbaar is voordat u begint en er verbinding mee maakt. 

```azurecli
az lab vm show --lab-name sampleLabName --name sampleVMName --resource-group sampleResourceGroup --expand 'properties($expand=ComputeVm,NetworkInterface)' --query '{status: computeVm.statuses[0].displayStatus, fqdn: fqdn, ipAddress: networkInterface.publicIpAddress}'
```
```json
{
  "fqdn": "lisalabvm.southcentralus.cloudapp.azure.com",
  "ipAddress": "13.85.228.112",
  "status": "Provisioning succeeded"
}
```

## <a name="start-and-connect-to-the-virtual-machine"></a>Start en maak verbinding met de virtuele machine
Met de volgende voorbeeld opdracht wordt een virtuele machine gestart:

```azurecli
az lab vm start --lab-name sampleLabName --name sampleVMName --resource-group sampleLabResourceGroup
```

Verbinding maken met een virtuele machine: [SSH](../virtual-machines/linux/mac-create-ssh-keys.md) of [extern bureaublad](../virtual-machines/windows/connect-logon.md).
```bash
ssh userName@ipAddressOrfqdn 
```

## <a name="update-the-virtual-machine"></a>De virtuele machine bijwerken
Met de volgende voorbeeld opdracht worden artefacten op een VM toegepast:

```azurecli
az lab vm apply-artifacts --lab-name  sampleLabName --name sampleVMName  --resource-group sampleResourceGroup  --artifacts @/artifacts.json
```

```json
[
  {
    "artifactId": "/artifactSources/public repo/artifacts/linux-java",
    "parameters": []
  },
  {
    "artifactId": "/artifactSources/public repo/artifacts/linux-install-nodejs",
    "parameters": []
  },
  {
    "artifactId": "/artifactSources/public repo/artifacts/linux-apt-package",
    "parameters": [
      {
        "name": "packages",
        "value": "abcd"
      },
      {
        "name": "update",
        "value": "true"
      },
      {
        "name": "options",
        "value": ""
      }
    ]
  } 
]
```

### <a name="list-artifacts-available-in-the-lab"></a>Beschik bare lijst artefacten in het lab

Als u in een Lab beschik bare artefacten wilt weer geven, voert u de volgende opdrachten uit.

**Cloud shell-Power shell**: Let op het gebruik van de apostroffen ( \` ) vóór de $ in $expand (dat wil zeggen $expand):

```azurecli-interactive
az lab vm show --resource-group <resourcegroupname> --lab-name <labname> --name <vmname> --expand "properties(`$expand=artifacts)" --query "artifacts[].{artifactId: artifactId, status: status}"
```

**Cloud shell-bash**: Let op het gebruik van het slash- \\ teken () vóór $ in de opdracht. 

```azurecli-interactive
az lab vm show --resource-group <resourcegroupname> --lab-name <labname> --name <vmname> --expand "properties(\$expand=artifacts)" --query "artifacts[].{artifactId: artifactId, status: status}"
```

Voorbeelduitvoer: 

```json
[
  {
    "artifactId": "/subscriptions/<subscription ID>/resourceGroups/<resource group name>/providers/Microsoft.DevTestLab/labs/<lab name>/artifactSources/public repo/artifacts/windows-7zip",
    "status": "Succeeded"
  }
]
```

## <a name="stop-and-delete-the-virtual-machine"></a>De virtuele machine stoppen en verwijderen    
Met de volgende voorbeeld opdracht wordt een virtuele machine gestopt.

```azurecli
az lab vm stop --lab-name sampleLabName --name sampleVMName --resource-group sampleResourceGroup
```

Een VM verwijderen.
```azurecli
az lab vm delete --lab-name sampleLabName --name sampleVMName --resource-group sampleResourceGroup
```

## <a name="next-steps"></a>Volgende stappen
Zie de volgende inhoud: [Azure cli-documentatie voor Azure DevTest Labs](/cli/azure/lab). 
