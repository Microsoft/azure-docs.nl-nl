---
title: Virtuele machines maken en beheren in DevTest Labs met Azure CLI
description: Meer informatie over het gebruik Azure DevTest Labs voor het maken en beheren van virtuele machines met Azure CLI
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 95e0add8ce14e47c609b1ae951673c261316293f
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107763537"
---
# <a name="create-and-manage-virtual-machines-with-devtest-labs-using-the-azure-cli"></a>Virtuele machines maken en beheren met DevTest Labs met behulp van de Azure CLI
In deze quickstart wordt u begeleid bij het maken, starten, verbinden, bijwerken en ops manier opsn manier van ontwikkelen in uw lab. 

Voordat u begint:

* Als er geen lab is gemaakt, vindt u hier [instructies.](devtest-lab-create-lab.md)

* [Installeer de Azure CLI](/cli/azure/install-azure-cli). Voer eerst az login uit om een verbinding met Azure te maken. 

## <a name="create-and-verify-the-virtual-machine"></a>De virtuele machine maken en controleren 
Voordat u aan DevTest Labs gerelateerde opdrachten uitvoert, stelt u de juiste Azure-context in met behulp van de `az account set` opdracht :

```azurecli
az account set --subscription 11111111-1111-1111-1111-111111111111
```

De opdracht voor het maken van een virtuele machine is: `az lab vm create` . De resourcegroep voor het lab, de labnaam en de naam van de virtuele machine zijn allemaal vereist. De rest van de argumenten wordt gewijzigd, afhankelijk van het type virtuele machine.

Met de volgende opdracht maakt u een op Windows gebaseerde afbeelding van Azure Market Place. De naam van de afbeelding is dezelfde als u zou zien bij het maken van een virtuele machine met behulp van de Azure Portal. 

```azurecli
az lab vm create --resource-group DtlResourceGroup --lab-name MyLab --name 'MyTestVm' --image "Visual Studio Community 2017 on Windows Server 2016 (x64)" --image-type gallery --size 'Standard_D2s_v3' --admin-username 'AdminUser' --admin-password 'Password1!'
```

Met de volgende opdracht maakt u een virtuele machine op basis van een aangepaste afbeelding die beschikbaar is in het lab:

```azurecli
az lab vm create --resource-group DtlResourceGroup --lab-name MyLab --name 'MyTestVm' --image "My Custom Image" --image-type custom --size 'Standard_D2s_v3' --admin-username 'AdminUser' --admin-password 'Password1!'
```

Het **argument image-type** is gewijzigd van **galerie in** **aangepast.** De naam van de afbeelding komt overeen met wat u ziet als u de virtuele machine in de Azure Portal.

Met de volgende opdracht maakt u een VM op basis van een Marketplace-afbeelding met ssh-verificatie:

```azurecli
az lab vm create --lab-name sampleLabName --resource-group sampleLabResourceGroup --name sampleVMName --image "Ubuntu Server 16.04 LTS" --image-type gallery --size Standard_DS1_v2 --authentication-type  ssh --generate-ssh-keys --ip-configuration public 
```

U kunt ook virtuele machines maken op basis van formules door de parameter **image-type** in te stellen op **formule**. Als u een specifiek virtueel netwerk voor uw virtuele machine moet kiezen, gebruikt u de **parameters vnet-name** en **subnet.** Zie az [lab vm create](/cli/azure/lab/vm#az_lab_vm_create)voor meer informatie.

## <a name="verify-that-the-vm-is-available"></a>Controleer of de VM beschikbaar is.
Gebruik de `az lab vm show` opdracht om te controleren of de VM beschikbaar is voordat u begint en er verbinding mee maakt. 

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

## <a name="start-and-connect-to-the-virtual-machine"></a>De virtuele machine starten en er verbinding mee maken
Met de volgende voorbeeldopdracht wordt een VM gestart:

```azurecli
az lab vm start --lab-name sampleLabName --name sampleVMName --resource-group sampleLabResourceGroup
```

Verbinding maken met een VM: [SSH](../virtual-machines/linux/mac-create-ssh-keys.md) [of Extern bureaublad.](../virtual-machines/windows/connect-logon.md)
```bash
ssh userName@ipAddressOrfqdn 
```

## <a name="update-the-virtual-machine"></a>De virtuele machine bijwerken
Met de volgende voorbeeldopdracht worden artefacten toegepast op een VM:

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

### <a name="list-artifacts-available-in-the-lab"></a>Lijst met artefacten die beschikbaar zijn in het lab

Voer de volgende opdrachten uit om artefacten weer te geven die beschikbaar zijn in een VM in een lab.

**Cloud Shell - PowerShell:** let op het gebruik van de backtick ( ) vóór de $ in $expand (dat wil zeggen \` '$expand):

```azurecli-interactive
az lab vm show --resource-group <resourcegroupname> --lab-name <labname> --name <vmname> --expand "properties(`$expand=artifacts)" --query "artifacts[].{artifactId: artifactId, status: status}"
```

**Cloud Shell - Bash:** let op het gebruik van het slash \\ -teken () vóór $ in de opdracht . 

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
Met de volgende voorbeeldopdracht wordt een VM gestopt.

```azurecli
az lab vm stop --lab-name sampleLabName --name sampleVMName --resource-group sampleResourceGroup
```

Een VM verwijderen.
```azurecli
az lab vm delete --lab-name sampleLabName --name sampleVMName --resource-group sampleResourceGroup
```

## <a name="next-steps"></a>Volgende stappen
Zie de volgende inhoud: [Azure CLI-documentatie voor Azure DevTest Labs.](/cli/azure/lab) 
