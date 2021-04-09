---
title: Schijf versleuteling inschakelen voor knoop punten van Service Fabric Managed cluster (preview)
description: Meer informatie over het inschakelen van schijf versleuteling voor Azure Service Fabric beheerde cluster knooppunten in Windows met behulp van een ARM-sjabloon.
ms.topic: how-to
ms.date: 02/15/2021
ms.openlocfilehash: b7e56ff8db9f94b8c6681a1a7d69a4751b3f43a5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100642405"
---
# <a name="enable-disk-encryption-for-service-fabric-managed-cluster-preview-nodes"></a>Schijf versleuteling inschakelen voor knoop punten van Service Fabric Managed cluster (preview)

In deze hand leiding vindt u informatie over het inschakelen van schijf versleuteling op Service Fabric beheerde cluster knooppunten in Windows met behulp van de [Azure Disk Encryption](../virtual-machines/windows/disk-encryption-overview.md) mogelijkheid voor [virtuele-machine schaal sets](../virtual-machine-scale-sets/disk-encryption-azure-resource-manager.md) via Azure Resource Manager-sjablonen (arm).

> [!IMPORTANT]
> De preview-versie van de schijf versleuteling van de virtuele machine biedt nog geen ondersteuning voor het bijwerken van de installatie kopie of de installatie kopie. Gebruik niet als u een upgrade van uw installatie kopie van het besturings systeem moet uitvoeren.

## <a name="register-for-azure-disk-encryption"></a>Registreren voor Azure Disk Encryption

Voor de preview-versie van schijf versleuteling voor de schaalset voor virtuele machines is zelf registratie vereist. Voer de volgende opdracht uit:

```powershell
Register-AzProviderFeature -FeatureName "UnifiedDiskEncryption" -ProviderNamespace "Microsoft.Compute"
```

De status van de registratie controleren door uit te voeren:

```powershell
Get-AzProviderFeature -ProviderNamespace "Microsoft.Compute" -FeatureName "UnifiedDiskEncryption"
```

Zodra de status is gewijzigd in *geregistreerd*, bent u klaar om door te gaan.

## <a name="provision-a-key-vault-for-disk-encryption"></a>Een Key Vault inrichten voor schijf versleuteling

Voor Azure Disk Encryption is een Azure Key Vault vereist om sleutels en geheimen voor schijf versleuteling te beheren en te beheren. Uw Key Vault en Service Fabric beheerd cluster moeten zich in dezelfde Azure-regio en hetzelfde abonnement bevinden. Zolang aan deze vereisten wordt voldaan, kunt u een nieuwe of bestaande Key Vault gebruiken door deze in te scha kelen voor schijf versleuteling.

### <a name="create-key-vault-with-disk-encryption-enabled"></a>Key Vault maken met schijf versleuteling ingeschakeld

Voer de volgende opdrachten uit om een nieuwe Key Vault voor schijf versleuteling te maken. Zorg ervoor dat de regio voor uw Key Vault wordt [ondersteund voor service Fabric beheerde clusters](faq-managed-cluster.md#what-regions-are-supported-in-the-preview) en zich in dezelfde regio bevindt als uw cluster.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```powershell
$resourceGroupName = "<rg-name>" 
$keyvaultName = "<kv-name>" 

New-AzResourceGroup -Name $resourceGroupName -Location eastus2 
New-AzKeyVault -ResourceGroupName $resourceGroupName -Name $keyvaultName -Location eastus2 -EnabledForDiskEncryption
```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

```azurecli
$resourceGroupName = "<rg-name>" 
$keyvaultName = "<kv-name>" 

az keyvault create --resource-group $resourceGroupName --name $keyvaultName --enabled-for-disk-encryption
```

---

### <a name="update-existing-key-vault-to-enable-disk-encryption"></a>Bestaande Key Vault bijwerken om schijf versleuteling in te scha kelen

Voer de volgende opdrachten uit om schijf versleuteling in te scha kelen voor een bestaande Key Vault.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```powershell
# ps 

Set-AzKeyVaultAccessPolicy -ResourceGroupName $resourceGroupName -VaultName $keyvaultName -EnabledForDiskEncryption
```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

```azurecli
az keyvault update --name keyvaultName --enabled-for-disk-encryption 
```

---

## <a name="update-the-template-and-parameters-files-for-disk-encryption"></a>De sjabloon-en parameter bestanden bijwerken voor schijf versleuteling

De volgende stap begeleidt u stapsgewijs door de vereiste sjabloon wijzigingen om schijf versleuteling in te scha kelen op een [bestaand beheerd cluster](tutorial-managed-cluster-deploy.md). U kunt ook een nieuw Service Fabric beheerd cluster implementeren waarvoor schijf versleuteling is ingeschakeld met deze sjabloon: https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/SF-Managed-Standard-SKU-1-NT-DiskEncryption

1. Voeg de volgende para meters aan de sjabloon toe, waarbij u uw eigen abonnement, de naam van de resource groep en de naam van de kluis vervangt onder `keyVaultResourceId` :

```json
"parameters": { 
…
    "keyVaultResourceId": { 
        "type": "string", 
        "defaultValue": "/subscriptions/########-####-####-####-############/resourceGroups/<rg-name>/providers/Microsoft.KeyVault/vaults/<kv-name>", 
        "metadata": { 
            "description": "Full resource id of the Key Vault used for disk encryption." 
        } 
    },
    "volumeType": { 
        "type": "string", 
        "defaultValue": "All", 
        "metadata": { 
            "description": "Type of the volume OS or Data to perform encryption operation" 
        }
    }
}, 
```

2. Voeg vervolgens de `AzureDiskEncryption` VM-extensie toe aan de knoop punten van het beheerde cluster knooppunt in de sjabloon:

```json
"properties": { 
    "vmExtensions": [ 
        { 
            "name": "AzureDiskEncryption", 
            "properties": { 
                "publisher": "Microsoft.Azure.Security", 
                "type": "AzureDiskEncryption", 
                "typeHandlerVersion": "2.1", 
                "autoUpgradeMinorVersion": true, 
                "settings": {                     
                    "EncryptionOperation": "EnableEncryption", 
                    "KeyVaultURL": "[reference(parameters('keyVaultResourceId'),'2016-10-01').vaultUri]", 
                    "KeyVaultResourceId": "[parameters('keyVaultResourceID')]",
                    "VolumeType": "[parameters('volumeType')]" 
                } 
            } 
        } 
    ] 
} 
```

3. Werk tot slot het parameter bestand bij, vervang uw eigen abonnement, de resource groep en de naam van de sleutel kluis in *keyVaultResourceId*:

```json
"parameters": { 
    … 
    "keyVaultResourceId": { 
        "value": "/subscriptions/########-####-####-####-############/resourceGroups/<rg-name>/providers/Microsoft.KeyVault/vaults/<kv-name>" 
    },   
    "volumeType": { 
        "value": "All" 
    }    
} 
```

## <a name="deploy-and-verify-the-changes"></a>De wijzigingen implementeren en verifiëren

Zodra u klaar bent, implementeert u de wijzigingen om schijf versleuteling in te scha kelen op uw beheerde cluster.

```powershell
$clusterName = "<clustername>" 

New-AzResourceGroupDeployment -Name $resourceGroupName -ResourceGroupName $resourceGroupName -TemplateFile .\template_diskEncryption.json -TemplateParameterFile \.parameters_diskEncryption.json -Debug -Verbose 
```

U kunt de schijf versleutelings status van de onderliggende schaalset van een knooppunt type controleren met behulp van de `Get-AzVmssDiskEncryption` opdracht. Eerst moet u de naam van de ondersteunende resource groep van uw beheerde cluster zoeken (met daarin het onderliggende virtuele netwerk, load balancer, open bare IP, NSG, schaal sets en opslag accounts). Wijzig `VmssName` de naam van het cluster knooppunt type dat u wilt controleren (zoals opgegeven in de implementatie sjabloon).

```powershell
$VmssName = "NT1"
$supportResourceGroupName = "SFC_" + (Get-AzServiceFabricManagedCluster -ResourceGroupName $resourceGroupName).ClusterId
Get-AzVmssDiskEncryption -ResourceGroupName $supportResourceGroupName -VMScaleSetName $VmssName
```

De uitvoer moet er ongeveer als volgt uitzien:

```console
ResourceGroupName            : SFC_########-####-####-####-############
VmScaleSetName               : NT1
EncryptionEnabled            : True
EncryptionExtensionInstalled : True
```

## <a name="next-steps"></a>Volgende stappen

[Voor beeld: Standard SKU Service Fabric beheerd cluster, 1 knooppunt type met schijf versleuteling ingeschakeld](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/SF-Managed-Standard-SKU-1-NT-DiskEncryption)

[Azure Disk Encryption voor Windows-Vm's](../virtual-machines/windows/disk-encryption-overview.md)

[Schaalsets voor virtuele machines versleutelen met Azure Resource Manager](../virtual-machine-scale-sets/disk-encryption-azure-resource-manager.md)
