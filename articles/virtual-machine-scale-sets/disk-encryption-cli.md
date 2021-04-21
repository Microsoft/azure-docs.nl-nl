---
title: Schijven voor Azure-schaalsets versleutelen met Azure CLI
description: Meer informatie over het gebruik van Azure CLI voor het versleutelen van VM-exemplaren en gekoppelde schijven in een Windows-schaalset voor virtuele machines
author: ju-shim
ms.author: jushiman
ms.topic: tutorial
ms.service: virtual-machine-scale-sets
ms.subservice: disks
ms.date: 10/15/2019
ms.reviewer: mimckitt
ms.custom: mimckitt, devx-track-azurecli
ms.openlocfilehash: d347be4e6727cdda659620befe20824678160020
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107792431"
---
# <a name="encrypt-os-and-attached-data-disks-in-a-virtual-machine-scale-set-with-the-azure-cli"></a>OS en gekoppelde gegevensschijven versleutelen in een virtuele-machineschaalset met Azure CLI

De Azure CLI wordt gebruikt voor het maken en beheren van Azure-resources vanaf de opdrachtregel of in scripts. In deze quickstart wordt beschreven hoe u de Azure CLI gebruikt om een virtuele-machineschaalset te maken en te versleutelen. Zie [Azure Disk Encryption voor een virtuele-machineschaalset](disk-encryption-overview.md)voor meer informatie over het toepassen van Azure Disk Encryption voor een virtuele-machineschaalset.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Voor dit artikel is versie 2.0.31 of hoger van Azure CLI vereist. Als u Azure Cloud Shell gebruikt, is de nieuwste versie al geïnstalleerd.

## <a name="create-a-scale-set"></a>Een schaalset maken

Voordat u een schaalset kunt maken, moet u eerst een resourcegroep maken met [az group create](/cli/azure/group). In het volgende voorbeeld wordt een resourcegroep met de naam *myResourceGroup* gemaakt op de locatie *eastus*:

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Maak nu een virtuele-machineschaalset met [az vmss create](/cli/azure/vmss). In het volgende voorbeeld wordt een schaalset met de naam *myScaleSet* gemaakt die automatisch wordt bijgewerkt als er wijzigingen zijn. Daarnaast worden er SSH-sleutels gegenereerd als deze niet bestaan in *~/.ssh/id_rsa*. Een gegevensschijf van 32 GB is gekoppeld aan elk VM-exemplaar en de [Custom Script Extension](../virtual-machines/extensions/custom-script-linux.md) van Azure wordt gebruikt om de gegevensschijven voor te bereiden met [az vmss extension set](/cli/azure/vmss/extension):

```azurecli-interactive
# Create a scale set with attached data disk
az vmss create \
  --resource-group myResourceGroup \
  --name myScaleSet \
  --image UbuntuLTS \
  --upgrade-policy-mode automatic \
  --admin-username azureuser \
  --generate-ssh-keys \
  --data-disk-sizes-gb 32

# Prepare the data disk for use with the Custom Script Extension
az vmss extension set \
  --publisher Microsoft.Azure.Extensions \
  --version 2.0 \
  --name CustomScript \
  --resource-group myResourceGroup \
  --vmss-name myScaleSet \
  --settings '{"fileUris":["https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/prepare_vm_disks.sh"],"commandToExecute":"./prepare_vm_disks.sh"}'
```

Het duurt enkele minuten om alle schaalsetresources en VM's te maken en te configureren.

## <a name="create-an-azure-key-vault-enabled-for-disk-encryption"></a>Een Azure-sleutelkluis maken die is ingeschakeld voor schijfversleuteling

Azure Key Vault kan sleutels, geheimen of wachtwoorden opslaan waarmee u deze veilig kunt implementeren in uw toepassingen en services. Cryptografische sleutels worden opgeslagen in Azure Key Vault met behulp van softwarebeveiliging, of u kunt uw sleutels in Hardware Security Modules (HMS's), die zijn gecertificeerd voor FIPS 140-2 level 2-standaarden, importeren of genereren. Deze cryptografische sleutels worden gebruikt voor het versleutelen en ontsleutelen van virtuele schijven die zijn gekoppeld aan uw VM. U behoudt de controle over deze cryptografische sleutels en kunt het gebruik controleren.

Definieer uw eigen unieke *keyvault_name*. Maak vervolgens een sleutelkluis met [az sleutelkluis maken](/cli/azure/keyvault#ext-keyvault-preview-az-keyvault-create) in hetzelfde abonnement en dezelfde regio als de schaalset en stel het toegangsbeleid *--ingeschakeld-voor-schijfversleuteling* in.

```azurecli-interactive
# Provide your own unique Key Vault name
keyvault_name=myuniquekeyvaultname

# Create Key Vault
az keyvault create --resource-group myResourceGroup --name $keyvault_name --enabled-for-disk-encryption
```

### <a name="use-an-existing-key-vault"></a>Een bestaande sleutelkluis gebruiken

Deze stap is alleen vereist als u een bestaande sleutelkluis hebt die u wilt gebruiken met schijfversleuteling. Sla deze stap over als u in de vorige sectie een sleutelkluis hebt gemaakt.

Definieer uw eigen unieke *keyvault_name*. Update vervolgens uw sleutelklus met [az sleutelkluis updaten](/cli/azure/keyvault#ext-keyvault-preview-az-keyvault-update) en stel het toegangsbeleid *--enabled-for-disk-encryption* in.

```azurecli-interactive
# Provide your own unique Key Vault name
keyvault_name=myuniquekeyvaultname

# Create Key Vault
az keyvault update --name $keyvault_name --enabled-for-disk-encryption
```

## <a name="enable-encryption"></a>Versleuteling inschakelen

Als u VM-exemplaren in een schaalset wilt versleutelen, moet u eerst bepaalde informatie over de Resource-ID van de sleutelkluis ophalen met [az keyvault show](/cli/azure/keyvault#ext-keyvault-preview-az-keyvault-show). Deze variabelen worden gebruikt om het versleutelingsproces vervolgens te starten met [az vmss encryption enable](/cli/azure/vmss/encryption#az_vmss_encryption_enable):

```azurecli-interactive
# Get the resource ID of the Key Vault
vaultResourceId=$(az keyvault show --resource-group myResourceGroup --name $keyvault_name --query id -o tsv)

# Enable encryption of the data disks in a scale set
az vmss encryption enable \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --disk-encryption-keyvault $vaultResourceId \
    --volume-type DATA
```

Het kan een paar minuten duren voordat het versleutelingsproces wordt gestart.

Als de schaalset een upgradebeleid heeft voor de schaalset die in een eerdere stap is gemaakt en op *automatisch* is ingesteld, wordt het versleutelingsproces automatisch gestart door de VM-exemplaren. Op schaalsets waarbij het upgradebeleid is ingesteld op handmatig, start u het versleutelingsbeleid voor de VM-exemplaren met [AZ vmss update-instances](/cli/azure/vmss#az_vmss_update_instances).

### <a name="enable-encryption-using-kek-to-wrap-the-key"></a>Versleuteling inschakelen met KEK om de sleutel in te pakken

U kunt ook een sleutelversleutelingssleutel voor extra beveiliging gebruiken bij het versleutelen van de schaalset voor virtuele machines.

```azurecli-interactive
# Get the resource ID of the Key Vault
vaultResourceId=$(az keyvault show --resource-group myResourceGroup --name $keyvault_name --query id -o tsv)

# Enable encryption of the data disks in a scale set
az vmss encryption enable \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --disk-encryption-keyvault $vaultResourceId \
    --key-encryption-key myKEK \
    --key-encryption-keyvault $vaultResourceId \
    --volume-type DATA
```

> [!NOTE]
>  De syntaxis voor de waarde van de parameter voor disk-encryption-keyvault is de volledige id-tekenreeks:</br>
/subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]</br></br>
> De syntaxis voor de waarde van de parameter sleutel-versleuteling-sleutel is de volledige URI naar de KEK, zoals in:</br>
https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id]

## <a name="check-encryption-progress"></a>Voortgang van de versleuteling controleren

Als u de status van de schijfversleuteling wilt controleren, gebruikt u [az vmss encryption show](/cli/azure/vmss/encryption#az_vmss_encryption_show):

```azurecli-interactive
az vmss encryption show --resource-group myResourceGroup --name myScaleSet
```

Wanneer VM-exemplaren worden versleuteld, wordt in de statuscode gerapporteerd *EncryptionState/encrypted*, zoals wordt weergegeven in de volgende voorbeelduitvoer:

```console
[
  {
    "disks": [
      {
        "encryptionSettings": null,
        "name": "myScaleSet_myScaleSet_0_disk2_3f39c2019b174218b98b3dfae3424e69",
        "statuses": [
          {
            "additionalProperties": {},
            "code": "EncryptionState/encrypted",
            "displayStatus": "Encryption is enabled on disk",
            "level": "Info",
            "message": null,
            "time": null
          }
        ]
      }
    ],
    "id": "/subscriptions/guid/resourceGroups/MYRESOURCEGROUP/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/virtualMachines/0",
    "resourceGroup": "MYRESOURCEGROUP"
  }
]
```

## <a name="disable-encryption"></a>Versleuteling uitschakelen

Als u geen schijven van versleutelde VM-exemplaren meer wilt gebruiken, kunt u versleuteling als volgt uitschakelen met [AZ vmss encryption disable](/cli/azure/vmss/encryption#az_vmss_encryption_disable):

```azurecli-interactive
az vmss encryption disable --resource-group myResourceGroup --name myScaleSet
```

## <a name="next-steps"></a>Volgende stappen

- In dit artikel hebt u de Azure CLI gebruikt om een virtuele-machineschaalset te versleutelen. U kunt ook [Azure Powershell](disk-encryption-powershell.md) gebruiken, of [sjablonen van Azure Resource Manager](disk-encryption-azure-resource-manager.md).
- Als u Azure Disk Encryption wilt toepassen nadat een andere extensie is ingericht, kunt u [extensiereeksen](virtual-machine-scale-sets-extension-sequencing.md) gebruiken. 
- Een end-to-end batch-voorbeeldbestand voor het versleutelen van gegevensschijven van de Linux-schaalset vindt u [hier](https://gist.githubusercontent.com/ejarvi/7766dad1475d5f7078544ffbb449f29b/raw/03e5d990b798f62cf188706221ba6c0c7c2efb3f/enable-linux-vmss.bat). In dit voorbeeld wordt een resourcegroep gemaakt, een Linux-schaal ingesteld, een gegevensschijf van 5 GB gekoppeld en de schaalset voor virtuele machines versleuteld.
