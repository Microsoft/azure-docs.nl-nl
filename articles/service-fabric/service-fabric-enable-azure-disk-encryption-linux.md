---
title: Schijf versleuteling voor Linux-clusters inschakelen
description: In dit artikel wordt beschreven hoe u schijf versleuteling inschakelt voor Azure Service Fabric cluster knooppunten in Linux met behulp van Azure Resource Manager en Azure Key Vault.
ms.topic: article
ms.date: 03/22/2019
ms.openlocfilehash: c600d822d20b0e5a0ca613935b1dfa4be838fcec
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "78252819"
---
# <a name="enable-disk-encryption-for-azure-service-fabric-cluster-nodes-in-linux"></a>Schijf versleuteling inschakelen voor Azure Service Fabric cluster knooppunten in Linux 
> [!div class="op_single_selector"]
> * [Schijf versleuteling voor Linux](service-fabric-enable-azure-disk-encryption-linux.md)
> * [Schijf versleuteling voor Windows](service-fabric-enable-azure-disk-encryption-windows.md)
>
>

In deze zelf studie leert u hoe u schijf versleuteling kunt inschakelen op Azure Service Fabric cluster knooppunten in Linux. U moet deze stappen volgen voor elk van de knooppunt typen en virtuele-machine schaal sets. Voor het versleutelen van de knoop punten gebruiken we de Azure Disk Encryption capaciteit van virtuele-machine schaal sets.

De hand leiding bevat de volgende onderwerpen:

* Belang rijke concepten waarvan u rekening moet houden bij het inschakelen van schijf versleuteling op Service Fabric schaal sets voor virtuele cluster machines in Linux.
* De stappen die moeten worden gevolgd voordat u schijf versleuteling inschakelt op Service Fabric cluster knooppunten in Linux.
* De stappen die moeten worden gevolgd om schijf versleuteling in te scha kelen op Service Fabric cluster knooppunten in Linux.



[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Vereisten

 **Zelf registratie**

Voor de preview-versie van schijf versleuteling voor de schaalset voor virtuele machines is zelf registratie vereist. Voer de volgende stappen uit:

1. Voer de volgende opdracht uit: 
    ```powershell
    Register-AzProviderFeature -ProviderNamespace Microsoft.Compute -FeatureName "UnifiedDiskEncryption"
    ```
2. Wacht ongeveer 10 minuten totdat de status is *geregistreerd*. U kunt de status controleren door de volgende opdracht uit te voeren:
    ```powershell
    Get-AzProviderFeature -ProviderNamespace "Microsoft.Compute" -FeatureName "UnifiedDiskEncryption"
    Register-AzResourceProvider -ProviderNamespace Microsoft.Compute
    ```
**Azure Key Vault**

1. Maak een sleutel kluis in hetzelfde abonnement en dezelfde regio als de schaalset. Selecteer vervolgens het toegangs beleid voor **EnabledForDiskEncryption** op de sleutel kluis met behulp van de Power shell-cmdlet. U kunt het beleid ook instellen met behulp van de Key Vault gebruikers interface in de Azure Portal met de volgende opdracht:
    ```powershell
    Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -EnabledForDiskEncryption
    ```
2. Installeer de nieuwste versie van de [Azure cli](/cli/azure/install-azure-cli), die de nieuwe versleutelings opdrachten heeft.

3. Installeer de nieuwste versie van de [Azure SDK vanuit Azure PowerShell](https://github.com/Azure/azure-powershell/releases) release. Hieronder vindt u de virtuele-machine schaalset Azure Disk Encryption-cmdlets voor het inschakelen ([instellen](/powershell/module/az.compute/set-azvmssdiskencryptionextension)) van versleuteling, ophalen ([Get](/powershell/module/az.compute/get-azvmssvmdiskencryption)) versleutelings status en verwijderen ([uitschakelen](/powershell/module/az.compute/disable-azvmssdiskencryption)) voor het exemplaar van de schaalset.


| Opdracht | Versie |  Bron  |
| ------------- |-------------| ------------|
| Get-AzVmssDiskEncryptionStatus   | 1.0.0 of hoger | Az.Compute |
| Get-AzVmssVMDiskEncryptionStatus   | 1.0.0 of hoger | Az.Compute |
| Disable-AzVmssDiskEncryption   | 1.0.0 of hoger | Az.Compute |
| Get-AzVmssDiskEncryption   | 1.0.0 of hoger | Az.Compute |
| Get-AzVmssVMDiskEncryption   | 1.0.0 of hoger | Az.Compute |
| Set-AzVmssDiskEncryptionExtension   | 1.0.0 of hoger | Az.Compute |


## <a name="supported-scenarios-for-disk-encryption"></a>Ondersteunde scenario's voor schijf versleuteling
* Versleuteling voor schaal sets voor virtuele machines wordt alleen ondersteund voor schaal sets die zijn gemaakt met Managed disks. Het wordt niet ondersteund voor een systeem eigen (of niet-beheerde) schijf schaal sets.
* Versleuteling en uitschakeling uitschakelen worden ondersteund voor besturings systeem-en gegevens volumes in virtuele-machine schaal sets in Linux. 
* Installatie kopieën van virtuele machines (VM) en upgrades voor schaal sets voor virtuele machines worden niet ondersteund in de huidige preview-versie.


## <a name="create-a-new-cluster-and-enable-disk-encryption"></a>Een nieuw cluster maken en schijf versleuteling inschakelen

Gebruik de volgende opdrachten om een cluster te maken en schijf versleuteling in te scha kelen met behulp van een Azure Resource Manager sjabloon en een zelfondertekend certificaat.

### <a name="sign-in-to-azure"></a>Aanmelden bij Azure  

Meld u aan met de volgende opdrachten:

```powershell

Login-AzAccount
Set-AzContext -SubscriptionId <guid>

```

```azurecli

azure login
az account set --subscription $subscriptionId

```

### <a name="use-the-custom-template-that-you-already-have"></a>De aangepaste sjabloon gebruiken die u al hebt 

Als u een aangepaste sjabloon moet maken, raden we u ten zeerste aan een van de sjablonen te gebruiken op de pagina met voor beelden voor het [maken van Azure service Fabric-cluster sjabloon](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master) . 

Als u al een aangepaste sjabloon hebt, controleert u of alle drie certificaat-gerelateerde para meters in de sjabloon en het parameter bestand als volgt worden genoemd. Zorg er ook voor dat de waarden als volgt zijn:

```Json
   "certificateThumbprint": {
      "value": ""
    },
    "sourceVaultValue": {
      "value": ""
    },
    "certificateUrlValue": {
      "value": ""
    },
```

Omdat alleen versleuteling van de gegevens schijf wordt ondersteund voor virtuele-machine schaal sets in Linux, moet u een gegevens schijf toevoegen met behulp van een resource manager-sjabloon. Werk de sjabloon voor het inrichten van gegevens schijven als volgt bij:

```Json
   
   "storageProfile": { 
            "imageReference": { 
              "publisher": "[parameters('vmImagePublisher')]", 
              "offer": "[parameters('vmImageOffer')]", 
              "sku": "[parameters('vmImageSku')]", 
              "version": "[parameters('vmImageVersion')]" 
            }, 
            "osDisk": { 
              "caching": "ReadOnly", 
              "createOption": "FromImage", 
              "managedDisk": { 
                "storageAccountType": "[parameters('storageAccountType')]" 
              } 
           }, 
                "dataDisks": [ 
                { 
                    "diskSizeGB": 1023, 
                    "lun": 0, 
                    "createOption": "Empty" 
   
```
 

```powershell
$resourceGroupLocation="westus"
$resourceGroupName="mycluster"
$CertSubjectName="mycluster.westus.cloudapp.azure.com"
$certPassword="Password!1" | ConvertTo-SecureString -AsPlainText -Force 
$certOutputFolder="c:\certificates"

$parameterFilePath="c:\templates\templateparam.json"
$templateFilePath="c:\templates\template.json"


New-AzServiceFabricCluster -ResourceGroupName $resourceGroupName -CertificateOutputFolder $certOutputFolder -CertificatePassword $certpassword -CertificateSubjectName $CertSubjectName -TemplateFile $templateFilePath -ParameterFile $parameterFilePath 

```

Dit is de equivalente CLI-opdracht. Wijzig de waarden in de Declare-instructies in de juiste waarden. De CLI ondersteunt alle andere para meters die de voor gaande Power shell-opdracht ondersteunt.

```azurecli
declare certPassword=""
declare resourceGroupLocation="westus"
declare resourceGroupName="mylinux"
declare certSubjectName="mylinuxsecure.westus.cloudapp.azure.com"
declare parameterFilePath="c:\mytemplates\linuxtemplateparm.json"
declare templateFilePath="c:\mytemplates\linuxtemplate.json"
declare certOutputFolder="c:\certificates"


az sf cluster create --resource-group $resourceGroupName --location $resourceGroupLocation  \
    --certificate-output-folder $certOutputFolder --certificate-password $certPassword  \
    --certificate-subject-name $certSubjectName \
    --template-file $templateFilePath --parameter-file $parametersFilePath

```

### <a name="mount-a-data-disk-to-a-linux-instance"></a>Een gegevens schijf koppelen aan een Linux-exemplaar
Voordat u doorgaat met het versleutelen van een schaalset voor virtuele machines, moet u ervoor zorgen dat de toegevoegde gegevens schijf correct is gekoppeld. Meld u aan bij de virtuele Linux-cluster-VM en voer de opdracht **LSBLK** uit. In de uitvoer moet worden aangegeven dat de gegevens schijf is toegevoegd in de kolom **koppel punt** .


### <a name="deploy-application-to-a-service-fabric-cluster-in-linux"></a>Toepassing implementeren in een Service Fabric-cluster in Linux
Als u een toepassing in uw cluster wilt implementeren, volgt u de stappen en richt lijnen op [Quick Start: Linux-containers implementeren op service Fabric](service-fabric-quickstart-containers-linux.md).


### <a name="enable-disk-encryption-for-the-virtual-machine-scale-sets-created-previously"></a>Schijf versleuteling inschakelen voor de virtuele-machine schaal sets die eerder zijn gemaakt
Voer de volgende opdrachten uit om schijf versleuteling in te scha kelen voor de schaal sets voor virtuele machines die u tijdens de vorige stappen hebt gemaakt:
 
```powershell
$VmssName = "nt1vm"
$vaultName = "mykeyvault"
$resourceGroupName = "mycluster"
$KeyVault = Get-AzKeyVault -VaultName $vaultName -ResourceGroupName $rgName
$DiskEncryptionKeyVaultUrl = $KeyVault.VaultUri
$KeyVaultResourceId = $KeyVault.ResourceId

Set-AzVmssDiskEncryptionExtension -ResourceGroupName $resourceGroupName -VMScaleSetName $VmssName -DiskEncryptionKeyVaultUrl $DiskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -VolumeType All

```

```azurecli

az vmss encryption enable -g <resourceGroupName> -n <VMSS name> --disk-encryption-keyvault <KeyVaultResourceId>

```

### <a name="validate-if-disk-encryption-is-enabled-for-a-virtual-machine-scale-set-in-linux"></a>Valideren of schijf versleuteling is ingeschakeld voor een schaalset voor virtuele machines in Linux
Voer de volgende opdrachten uit om de status van een volledige schaalset voor virtuele machines of een wille keurig exemplaar in een schaalset op te halen.
Daarnaast kunt u zich aanmelden bij de virtuele Linux-cluster-VM en de opdracht **LSBLK** uitvoeren. In de uitvoer moet de toegevoegde gegevens schijf worden weer gegeven in de kolom **koppel punt** , en de kolom **type** moet *crypt* lezen.

```powershell

$VmssName = "nt1vm"
$resourceGroupName = "mycluster"
Get-AzVmssDiskEncryption -ResourceGroupName $resourceGroupName -VMScaleSetName $VmssName

Get-AzVmssVMDiskEncryption -ResourceGroupName $resourceGroupName -VMScaleSetName $VmssName -InstanceId "0"

```

```azurecli
az vmss encryption show -g <resourceGroupName> -n <VMSS name>

```

### <a name="disable-disk-encryption-for-a-virtual-machine-scale-set-in-a-service-fabric-cluster"></a>Schijf versleuteling uitschakelen voor een schaalset voor virtuele machines in een Service Fabric cluster
Schakel de schijf versleuteling voor een schaalset voor virtuele machines uit door de volgende opdrachten uit te voeren. Houd er rekening mee dat het uitschakelen van schijf versleuteling van toepassing is op de gehele schaalset voor virtuele machines en niet op een afzonderlijk exemplaar.

```powershell
$VmssName = "nt1vm"
$resourceGroupName = "mycluster"
Disable-AzVmssDiskEncryption -ResourceGroupName $rgName -VMScaleSetName $VmssName

```

```azurecli
az vmss encryption disable -g <resourceGroupName> -n <VMSS name>

```


## <a name="next-steps"></a>Volgende stappen
Op dit moment moet u een beveiligd cluster hebben en kunt u schijf versleuteling voor Service Fabric cluster knooppunten en virtuele-machine schaal sets in-en uitschakelen. Zie [schijf versleuteling voor Windows](service-fabric-enable-azure-disk-encryption-windows.md)voor vergelijk bare richt lijnen op service Fabric cluster knooppunten in Linux. 
