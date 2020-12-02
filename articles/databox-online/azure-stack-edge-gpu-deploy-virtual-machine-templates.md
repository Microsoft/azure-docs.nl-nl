---
title: Vm's op uw Azure Stack Edge Pro-apparaat implementeren via sjablonen
description: Hierin wordt beschreven hoe u virtuele machines (Vm's) maakt en beheert op een Azure Stack Edge Pro-apparaat met behulp van sjablonen.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 11/16/2020
ms.author: alkohli
ms.openlocfilehash: 93df80cd6fcd6f5553ea509a4778a155299bb057
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/01/2020
ms.locfileid: "96449045"
---
# <a name="deploy-vms-on-your-azure-stack-edge-pro-gpu-device-via-templates"></a>Vm's op uw Azure Stack Edge Pro GPU-apparaat implementeren via sjablonen

In deze zelf studie wordt beschreven hoe u een virtuele machine op uw Azure Stack Edge Pro-apparaat maakt en beheert met behulp van sjablonen. Deze sjablonen zijn JavaScript Object Notation (JSON)-bestanden waarmee de infra structuur en configuratie voor uw virtuele machine worden gedefinieerd. In deze sjablonen geeft u de resources op die u wilt implementeren en de eigenschappen voor deze resources.

Sjablonen zijn flexibel in verschillende omgevingen, omdat deze para meters als invoer kunnen worden ingevoerd tijdens runtime vanuit een bestand. De standaard naamgevings structuur is `TemplateName.json` voor de sjabloon en `TemplateName.parameters.json` voor het parameter bestand. Ga voor meer informatie over ARM-sjablonen naar [Wat zijn Azure Resource Manager sjablonen?](../azure-resource-manager/templates/overview.md).

In deze zelf studie gebruiken we vooraf geschreven voorbeeld sjablonen voor het maken van resources. U hoeft het sjabloon bestand niet te bewerken en u kunt alleen de `.parameters.json` bestanden wijzigen om de implementatie aan uw computer aan te passen. 

## <a name="vm-deployment-workflow"></a>VM-implementatiewerkstroom

Als u Azure Stack Edge Pro-Vm's wilt implementeren op een groot aantal apparaten, kunt u één Sysprep-VHD gebruiken voor uw volledige vloot, dezelfde sjabloon voor implementatie en hoeft u alleen kleine wijzigingen aan te brengen in de para meters voor die sjabloon voor elke implementatie locatie (deze wijzigingen kunnen hand matig worden uitgevoerd, zoals we hier of programmatisch doen.) 

Het overzicht op hoog niveau van de implementatie werk stroom met behulp van sjablonen is als volgt:

1. **Vereisten configureren** : er zijn drie soorten vereisten; apparaat, client en voor de virtuele machine.

    1. **Vereisten voor apparaten**

        1. Verbinding maken met Azure Resource Manager op het apparaat.
        2. Schakel Compute in via de lokale gebruikers interface.

    2. **Client vereisten**

        1. Down load de VM-sjablonen en de bijbehorende bestanden op de client.
        1. Optioneel TLS 1,2 configureren op de client.
        1. [Down load en installeer Microsoft Azure Storage Explorer](https://go.microsoft.com/fwlink/?LinkId=708343&clcid=0x409) op uw client.

    3. **VM-vereisten**

        1. Maak een resource groep op de locatie van het apparaat die alle VM-resources zal bevatten.
        1. Maak een opslag account om de VHD te uploaden die wordt gebruikt om een VM-installatie kopie te maken.
        1. De URI van het lokale opslag account toevoegen aan een DNS-of hosts-bestand op de client die toegang heeft tot uw apparaat.
        1. Installeer het Blob Storage-certificaat op het apparaat en op de lokale client die toegang heeft tot uw apparaat. Installeer eventueel het Blob Storage-certificaat op de Storage Explorer.
        1. Maak een VHD en upload deze naar het opslag account dat u eerder hebt gemaakt.

2. **VM maken op basis van sjablonen**

    1. Maak een VM-installatie kopie en een VNet met behulp van de `CreateImageAndVnet.parameters.json` para meters bestand en `CreateImageAndVnet.json` implementatie sjabloon.
    1. Maak een virtuele machine met eerder gemaakte resources met behulp van `CreateVM.parameters.json` para meters bestand en  `CreateVM.json` implementatie sjabloon.

## <a name="device-prerequisites"></a>Vereisten voor apparaten

Configureer deze vereisten op uw Azure Stack Edge Pro-apparaat.

[!INCLUDE [azure-stack-edge-gateway-deploy-virtual-machine-prerequisites](../../includes/azure-stack-edge-gateway-deploy-virtual-machine-prerequisites.md)]

## <a name="client-prerequisites"></a>Client vereisten

Configureer deze vereisten op uw client die worden gebruikt voor toegang tot het Azure Stack Edge Pro-apparaat.

1. [Down load Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) als u het gebruikt om een VHD te uploaden. U kunt ook AzCopy downloaden om een VHD te uploaden. Mogelijk moet u TLS 1,2 op uw client computer configureren als u oudere versies van AzCopy uitvoert. 
1. [Down load de VM-sjablonen en parameter bestanden](https://aka.ms/ase-vm-templates) naar uw client computer. Pak het bestand uit in een map die u wilt gebruiken als werkmap.


## <a name="vm-prerequisites"></a>VM-vereisten

Configureer deze vereisten voor het maken van resources die nodig zijn voor het maken van een VM. 

    
### <a name="create-a-resource-group"></a>Een resourcegroep maken

Maak een Azure-resourcegroep met behulp van de opdracht [New-AzureRmResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Een resource groep is een logische container waarin de Azure-resources, zoals opslag account, schijf, beheerde schijf, worden geïmplementeerd en beheerd.

> [!IMPORTANT]
> Alle resources worden gemaakt op dezelfde locatie als die van het apparaat en de locatie is ingesteld op **DBELocal**.

```powershell
New-AzureRmResourceGroup -Name <Resource group name> -Location DBELocal
```

Hieronder ziet u een voorbeeld van de uitvoer.

```powershell
PS C:\windows\system32> New-AzureRmResourceGroup -Name myasegpurgvm -Location DBELocal

ResourceGroupName : myasegpurgvm
Location          : dbelocal
ProvisioningState : Succeeded
Tags              :
ResourceId        : /subscriptions/DDF9FC44-E990-42F8-9A91-5A6A5CC472DB/resourceGroups/myasegpurgvm

PS C:\windows\system32>
```

### <a name="create-a-storage-account"></a>Create a storage account

Maak een nieuw opslag account met behulp van de resource groep die u in de vorige stap hebt gemaakt. Dit is een **lokaal opslag account** dat wordt gebruikt voor het uploaden van de installatie kopie van de virtuele schijf voor de VM.

```powershell
New-AzureRmStorageAccount -Name <Storage account name> -ResourceGroupName <Resource group name> -Location DBELocal -SkuName Standard_LRS
```

> [!NOTE]
> Alleen de lokale opslag accounts, zoals lokaal redundante opslag (Standard_LRS of Premium_LRS), kunnen worden gemaakt via Azure Resource Manager. Als u gelaagde opslag accounts wilt maken, raadpleegt u de stappen in [toevoegen, verbinding maken met opslag accounts op uw Azure stack Edge Pro](azure-stack-edge-j-series-deploy-add-storage-accounts.md).

Hieronder ziet u een voorbeeld van de uitvoer.

```powershell
PS C:\windows\system32> New-AzureRmStorageAccount -Name myasegpusavm -ResourceGroupName myasegpurgvm -Location DBELocal -SkuName Standard_LRS

StorageAccountName ResourceGroupName Location SkuName     Kind    AccessTier CreationTime
------------------ ----------------- -------- -------     ----    ---------- ------------
myasegpusavm       myasegpurgvm      DBELocal StandardLRS Storage            7/29/2020 10:11:16 PM

PS C:\windows\system32>
```

Voer de opdracht uit om de sleutel van het opslag account op te halen `Get-AzureRmStorageAccountKey` . Hier wordt een voor beeld van een uitvoer van deze opdracht weer gegeven.

```powershell
PS C:\windows\system32> Get-AzureRmStorageAccountKey

cmdlet Get-AzureRmStorageAccountKey at command pipeline position 1
Supply values for the following parameters:
(Type !? for Help.)
ResourceGroupName: myasegpurgvm
Name: myasegpusavm

KeyName    Value                                                  Permissions   
-------     -----                                                   --
key1 GsCm7QriXurqfqx211oKdfQ1C9Hyu5ZutP6Xl0dqlNNhxLxDesDej591M8y7ykSPN4fY9vmVpgc4ftkwAO7KQ== 11 
key2 7vnVMJUwJXlxkXXOyVO4NfqbW5e/5hZ+VOs+C/h/ReeoszeV+qoyuBitgnWjiDPNdH4+lSm1/ZjvoBWsQ1klqQ== ll
```

### <a name="add-blob-uri-to-hosts-file"></a>Blob-URI toevoegen aan het hosts-bestand

Zorg ervoor dat u de BLOB-URI in het hosts-bestand al hebt toegevoegd voor de client die u gebruikt om verbinding te maken met de Blob-opslag. **Voer Klad blok als Administrator uit** en voeg de volgende vermelding toe voor de BLOB-URI in de `C:\windows\system32\drivers\etc\hosts` :

`<Device IP> <storage account name>.blob.<Device name>.<DNS domain>`

In een typische omgeving zou uw DNS zo zijn geconfigureerd dat alle opslag accounts verwijzen naar het Azure Stack Edge Pro-apparaat met een `*.blob.devicename.domainname.com` vermelding.

### <a name="optional-install-certificates"></a>Beschrijving Certificaten installeren

Sla deze stap over als u verbinding wilt maken via Storage Explorer met behulp van *http*. Als u *https* gebruikt, moet u de juiste certificaten installeren in Storage Explorer. In dit geval installeert u het BLOB-eindpunt certificaat. Zie certificaten maken en uploaden in [certificaten beheren](azure-stack-edge-j-series-manage-certificates.md)voor meer informatie. 

### <a name="create-and-upload-a-vhd"></a>Een VHD maken en uploaden

Zorg ervoor dat u een installatie kopie van een virtuele schijf hebt die u in de volgende stap kunt uploaden. Volg de stappen in [een VM-installatie kopie maken](azure-stack-edge-j-series-create-virtual-machine-image.md). 

Kopieer de schijf installatie kopieën die moeten worden gebruikt in pagina-blobs in het lokale opslag account dat u in de vorige stappen hebt gemaakt. U kunt een hulp programma zoals [Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) of [AzCopy gebruiken om de VHD te uploaden naar het opslag account](azure-stack-edge-j-series-deploy-virtual-machine-powershell.md#upload-a-vhd) dat u in de vorige stappen hebt gemaakt. 

### <a name="use-storage-explorer-for-upload"></a>Storage Explorer gebruiken voor uploaden

1. Open Storage Explorer. Ga naar **bewerken** en zorg ervoor dat de toepassing is ingesteld op **target Azure stack-api's**.

    ![Doel instellen op Azure Stack Api's](media/azure-stack-edge-gpu-deploy-virtual-machine-templates/set-target-apis-1.png)

2. Installeer het client certificaat in de PEM-indeling. Ga naar **bewerken > SSL-certificaten > certificaten importeren**. Ga naar het client certificaat.

    ![Eindpunt certificaat van Blob-opslag importeren](media/azure-stack-edge-gpu-deploy-virtual-machine-templates/import-blob-storage-endpoint-certificate-1.png)

    - Als u door apparaat gegenereerde certificaten gebruikt, downloadt en converteert u het eindpunt certificaat voor het Blob-opslag `.cer` naar een `.pem` indeling. Voer de volgende opdracht uit. 
    
        ```powershell
        PS C:\windows\system32> Certutil -encode 'C:\myasegpu1_Blob storage (1).cer' .\blobstoragecert.pem
        Input Length = 1380
        Output Length = 1954
        CertUtil: -encode command completed successfully.
        ```
    - Als u uw eigen certificaat wilt maken, gebruikt u het basis certificaat voor de handtekening keten in de `.pem` indeling.

3. Nadat u het certificaat hebt geïmporteerd, start u Storage Explorer opnieuw op om de wijzigingen van kracht te laten worden.

    ![Opnieuw opstarten Storage Explorer](media/azure-stack-edge-gpu-deploy-virtual-machine-templates/restart-storage-explorer-1.png)

4. Klik in het linkerdeel venster met de rechter muisknop op **opslag accounts** en selecteer **verbinding maken met Azure Storage**. 

    ![Verbinding maken met Azure Storage 1](media/azure-stack-edge-gpu-deploy-virtual-machine-templates/connect-azure-storage-1.png)

5. Selecteer **De naam en sleutel van een opslagaccount gebruiken**. Selecteer **Next**.

    ![Verbinding maken met Azure Storage 2](media/azure-stack-edge-gpu-deploy-virtual-machine-templates/connect-azure-storage-2.png)

6. Geef in de **naam en sleutel verbinding maken** de **weergave naam**, de **naam van het opslag account**, Azure Storage **account sleutel** op. Selecteer een **ander** opslag domein en geef vervolgens de `<device name>.<DNS domain>` Connection String op. Als u een certificaat niet in Storage Explorer hebt geïnstalleerd, controleert u de optie **http gebruiken** . Selecteer **Next**.

    ![Verbinding maken met naam en sleutel](media/azure-stack-edge-gpu-deploy-virtual-machine-templates/connect-name-key-1.png)

7. Controleer het **samen vatting** van de verbinding en selecteer **verbinding maken**.

8. Het opslag account wordt weer gegeven in het linkerdeel venster. Selecteer en vouw het opslag account uit. Selecteer **BLOB-containers**, klik met de rechter muisknop en selecteer **BLOB-container maken**. Geef een naam op voor de BLOB-container.

9. Selecteer de container die u zojuist hebt gemaakt en selecteer in het rechterdeel venster de optie **upload > bestanden uploaden**. 

    ![VHD-bestand 1 uploaden](media/azure-stack-edge-gpu-deploy-virtual-machine-templates/upload-vhd-file-1.png)

10. Blader en wijs de VHD aan die u wilt uploaden in de **geselecteerde bestanden**. Selecteer **BLOB-type** als **pagina-BLOB** en selecteer **uploaden**.

    ![VHD-bestand 2 uploaden](media/azure-stack-edge-gpu-deploy-virtual-machine-templates/upload-vhd-file-2.png)

11. Zodra de VHD naar de BLOB-container is geladen, selecteert u de VHD, klikt u met de rechter muisknop en selecteert u vervolgens **Eigenschappen**. 

    ![VHD-bestand uploaden 3](media/azure-stack-edge-gpu-deploy-virtual-machine-templates/upload-vhd-file-3.png)

12. Kopieer de **URI** en sla deze op zoals u deze in de latere stappen gaat gebruiken.

    ![URI kopiëren](media/azure-stack-edge-gpu-deploy-virtual-machine-templates/copy-uri-1.png)

<!--### Use AzCopy for upload

Before you use AzCopy, make sure that the [AzCopy is configured correctly](#configure-azcopy) for use with the blob storage REST API version that you are using with your Azure Stack Edge Pro device.


```powershell
AzCopy /Source:<sourceDirectoryForVHD> /Dest:<blobContainerUri> /DestKey:<storageAccountKey> /Y /S /V /NC:32  /BlobType:page /destType:blob 
```

> ![NOTE]
> Set `BlobType` to page for creating a managed disk out of VHD. Set `BlobType` to block when writing to tiered storage accounts using AzCopy.

You can download the disk images from the marketplace. For detailed steps, go to [Get the virtual disk image from Azure marketplace](azure-stack-edge-j-series-create-virtual-machine-image.md).

A sample output using AzCopy 7.3 is shown below. For more information on this command, go to [Upload VHD file to storage account using AzCopy](../devtest-labs/devtest-lab-upload-vhd-using-azcopy.md).


```powershell
AzCopy /Source:\\hcsfs\scratch\vm_vhds\linux\ /Dest:http://sa191113014333.blob.dbe-1dcmhq2.microsoftdatabox.com/vmimages /DestKey:gJKoyX2Amg0Zytd1ogA1kQ2xqudMHn7ljcDtkJRHwMZbMK== /Y /S /V /NC:32 /BlobType:page /destType:blob /z:2e7d7d27-c983-410c-b4aa-b0aa668af0c6
```-->

## <a name="create-image-and-vnet-for-your-vm"></a>Een installatie kopie en een VNet maken voor uw virtuele machine

Als u een installatie kopie en een virtueel netwerk voor uw virtuele machine wilt maken, moet u het parameter `CreateImageAndVnet.parameters.json` bestand bewerken en vervolgens de sjabloon implementeren `CreateImageAndVnet.json` die gebruikmaakt van dit parameter bestand.


### <a name="edit-parameters-file"></a>Parameter bestand bewerken

Het bestand `CreateImageAndVnet.parameters.json` heeft de volgende para meters: 

```json
"parameters": {
        "osType": {
              "value": "<Operating system corresponding to the VHD you upload can be Windows or Linux>"
        },
        "imageName": {
            "value": "<Name for the VM image>"
        },
        "imageUri": {
              "value": "<Path to the VHD that you uploaded in the Storage account>"
        },
        "vnetName": {
            "value": "<Name for the virtual network where you will deploy the VM>"
        },
        "subnetName": {
            "value": "<Name for the subnet for the VNet>"
        },
        "addressPrefix": {
            "value": "<Address prefix for the virtual network>"
        },
        "subnetPrefix": {
            "value": "<Subnet prefix for the subnet for the Vnet>"
        }
    }
```

Bewerk het bestand `CreateImageAndVnet.parameters.json` om het volgende toe te voegen aan uw Azure stack Edge Pro-apparaat:

1. Geef het type besturings systeem op dat overeenkomt met de VHD die u wilt uploaden. Het type besturings systeem kan Windows of Linux zijn.

    ```json
    "parameters": {
            "osType": {
              "value": "Windows"
            },
    ```

2. Wijzig de afbeeldings-URI in de URI van de afbeelding die u in de vorige stap hebt geüpload:

    ```json
    "imageUri": {
        "value": "https://myasegpusavm.blob.myasegpu1.wdshcsso.com/windows/WindowsServer2016Datacenter.vhd"
        },
    ```
    Als u *http* gebruikt met Storage Explorer, wijzigt u dit in een *https* -URI.

3. Wijzig de `addressPrefix` en `subnetPrefix` . Ga in de lokale gebruikers interface van het apparaat naar de pagina **netwerk** . Zoek de poort die u hebt ingeschakeld voor compute. Haal het IP-adres van het basis netwerk op en voeg het subnetmasker toe om de CIDR-notatie te maken. Als u een standaard 255.255.255.0-subnet hebt, moet u dit doen door het laatste nummer van het IP-adres te vervangen door 0 en toe te voegen/24 aan het einde. 10.126.68.0 met een subnetmasker voor 255.255.255.0 wordt dus 10.126.68.0/24. 
    
    ```json
    "addressPrefix": {
                "value": "10.126.68.0/24"
            },
            "subnetPrefix": {
                "value": "10.126.68.0/24"
            }
    ```  

4. Geef de unieke naam van de installatie kopie, de VNet-naam en de naam van het subnet op voor de para meters.

    Hier volgt een voor beeld van een JSON dat in dit artikel wordt gebruikt.

    ```json
    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
        "contentVersion": "1.0.0.0",
      "parameters": {
        "osType": {
          "value": "Windows"
        },
        "imageName": {
          "value": "image1"
        },
        "imageUri": {
          "value": "https://myasegpusavm.blob.myasegpu1.wdshcsso.com/windows/WindowsServer2016Datacenter.vhd"
        },
        "vnetName": {
          "value": "vnet1"
        },
        "subnetName": {
          "value": "subnet1"
        },
        "addressPrefix": {
          "value": "10.126.68.0/24"
        },
        "subnetPrefix": {
          "value": "10.126.68.0/24"
        }
      }
    }
    ```
5. Sla het parameter bestand op.


### <a name="deploy-template"></a>Sjabloon implementeren 

Implementeer de sjabloon `CreateImageAndVnet.json` . Met deze sjabloon implementeert u de VNet-en installatie kopie bronnen die worden gebruikt voor het maken van Vm's in de volgende stap.

> [!NOTE]
> Wanneer u de sjabloon implementeert als u een verificatie fout krijgt, zijn uw Azure-referenties voor deze sessie mogelijk verlopen. Voer de `login-AzureRM` opdracht opnieuw uit om verbinding te maken met Azure Resource Manager op uw Azure stack Edge Pro-apparaat opnieuw.

1. Voer de volgende opdracht uit: 
    
    ```powershell
    $templateFile = "Path to CreateImageAndVnet.json"
    $templateParameterFile = "Path to CreateImageAndVnet.parameters.json"
    $RGName = "<Name of your resource group>"
    New-AzureRmResourceGroupDeployment `
        -ResourceGroupName $RGName `
        -TemplateFile $templateFile `
        -TemplateParameterFile $templateParameterFile `
        -Name "<Name for your deployment>"
    ```

2. Controleer of de installatie kopie en de VNet-resources zijn ingericht. Hier volgt een voor beeld van de uitvoer van een geslaagde installatie kopie en VNet.
    
    ```powershell
    PS C:\07-30-2020> login-AzureRMAccount -EnvironmentName aztest1 -TenantId c0257de7-538f-415c-993a-1b87a031879d
    
    Account               SubscriptionName              TenantId                             Environment
    -------               ----------------              --------                             -----------
    EdgeArmUser@localhost Default Provider Subscription c0257de7-538f-415c-993a-1b87a031879d aztest1
    
    PS C:\07-30-2020> $templateFile = "C:\07-30-2020\CreateImageAndVnet.json"
    PS C:\07-30-2020> $templateParameterFile = "C:\07-30-2020\CreateImageAndVnet.parameters.json"
    PS C:\07-30-2020> $RGName = "myasegpurgvm"
    PS C:\07-30-2020> New-AzureRmResourceGroupDeployment `
    >>     -ResourceGroupName $RGName `
    >>     -TemplateFile $templateFile `
    >>     -TemplateParameterFile $templateParameterFile `
    >>     -Name "Deployment1"
    
    DeploymentName          : Deployment1
    ResourceGroupName       : myasegpurgvm
    ProvisioningState       : Succeeded
    Timestamp               : 7/30/2020 5:53:32 PM
    Mode                    : Incremental
    TemplateLink            :
    Parameters              :
                              Name             Type                       Value
                              ===============  =========================  ==========
                              osType           String                     Windows
                              imageName        String                     image1
                              imageUri         String
                              https://myasegpusavm.blob.myasegpu1.wdshcsso.com/windows/WindowsServer2016Datacenter.vhd
                              vnetName         String                     vnet1
                              subnetName       String                     subnet1
                              addressPrefix    String                     10.126.68.0/24
                              subnetPrefix     String                     10.126.68.0/24
    
    Outputs                 :
    DeploymentDebugLogLevel :
    
    PS C:\07-30-2020>
    ```
    
## <a name="create-vm"></a>VM maken

### <a name="edit-parameters-file-to-create-vm"></a>Parameter bestand bewerken voor het maken van een VM
 
Als u een VM wilt maken, gebruikt u het parameterbestand `CreateVM.parameters.json`. Hierbij worden de volgende para meters gebruikt.
    
```json
"vmName": {
            "value": "<Name for your VM>"
        },
        "adminUsername": {
            "value": "<Username to log into the VM>"
        },
        "Password": {
            "value": "<Password to log into the VM>"
        },
        "imageName": {
            "value": "<Name for your image>"
        },
        "vmSize": {
            "value": "<A supported size for your VM>"
        },
        "vnetName": {
            "value": "<Name for the virtual network you created earlier>"
        },
        "subnetName": {
            "value": "<Name for the subnet you created earlier>"
        },
        "nicName": {
            "value": "<Name for the network interface>"
        },
        "privateIPAddress": {
            "value": "<Private IP address, enter a static IP in the subnet created earlier or leave empty to assign DHCP>"
        },
        "IPConfigName": {
            "value": "<Name for the ipconfig associated with the network interface>"
        }
```    

Wijs de juiste para meters toe aan `CreateVM.parameters.json` voor uw Azure stack Edge Pro-apparaat.

1. Geef een unieke naam, de naam van de netwerk interface en de naam van de ipconfig op. 
1. Voer een gebruikers naam, wacht woord en een ondersteunde VM-grootte in.
1. Geef dezelfde naam op voor **VnetName**, **subnetmasker** en **installatie kopie** die is opgegeven in de para meters voor `CreateImageAndVnet.parameters.json` . Als u bijvoorbeeld VnetName, subnetvoorvoegsel en Imagenaam hebt opgegeven als **vnet1**, **subnet1** en **afbeelding1**, moet u deze waarden ook voor de para meters in deze sjabloon gebruiken.
1. U hebt nu een statisch IP-adres nodig om toe te wijzen aan de virtuele machine in het hierboven gedefinieerde subnet-netwerk. Vervang **PrivateIPAddress** door dit adres in het parameter bestand. Als u wilt dat de virtuele machine een IP-adres van uw lokale DHCP-server krijgt, laat u de `privateIPAddress` waarde leeg.  
    
    ```json
    "privateIPAddress": {
                "value": "5.5.153.200"
            },
    ```
    
4. Sla het parameter bestand op.

    Hier volgt een voor beeld van een JSON dat in dit artikel wordt gebruikt.
    
    ```json
    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "vmName": {
                "value": "mywindowsvm"
            },
            "adminUsername": {
                "value": "Administrator"
            },
            "Password": {
                "value": "Password1"
            },
            "imageName": {
                "value": "image1"
            },
            "vmSize": {
                "value": "Standard_D1_v2"
            },
            "vnetName": {
                "value": "vnet1"
            },
            "subnetName": {
                "value": "subnet1"
            },
            "nicName": {
                "value": "nic1"
            },
            "privateIPAddress": {
                "value": "10.126.68.186"
            },
            "IPConfigName": {
                "value": "ipconfig1"
            }
        }
    }
    ```      

### <a name="deploy-template-to-create-vm"></a>Sjabloon implementeren voor het maken van een VM

Implementeer de sjabloon voor het maken van de VM `CreateVM.json` . Met deze sjabloon maakt u een netwerk interface van het bestaande VNet en maakt u een virtuele machine op basis van de geïmplementeerde installatie kopie.

1. Voer de volgende opdracht uit: 
    
    ```powershell
    Command:
        
        $templateFile = "<Path to CreateVM.json>"
        $templateParameterFile = "<Path to CreateVM.parameters.json>"
        $RGName = "<Resource group name>"
             
        New-AzureRmResourceGroupDeployment `
            -ResourceGroupName $RGName `
            -TemplateFile $templateFile `
            -TemplateParameterFile $templateParameterFile `
            -Name "<DeploymentName>"
    ```   

    Het maken van de VM duurt 15-20 minuten. Hier volgt een voor beeld van de uitvoer van een geslaagde VM.
    
    ```powershell
    PS C:\07-30-2020> $templateFile = "C:\07-30-2020\CreateWindowsVM.json"
        PS C:\07-30-2020> $templateParameterFile = "C:\07-30-2020\CreateWindowsVM.parameters.json"
        PS C:\07-30-2020> $RGName = "myasegpurgvm"
        PS C:\07-30-2020> New-AzureRmResourceGroupDeployment `
        >>     -ResourceGroupName $RGName `
        >>     -TemplateFile $templateFile `
        >>     -TemplateParameterFile $templateParameterFile `
        >>     -Name "Deployment2"    
        
        DeploymentName          : Deployment2
        ResourceGroupName       : myasegpurgvm
        ProvisioningState       : Succeeded
        Timestamp               : 7/30/2020 6:21:09 PM
        Mode                    : Incremental
        TemplateLink            :
        Parameters              :
                                  Name             Type                       Value
                                  ===============  =========================  ==========
                                  vmName           String                     MyWindowsVM
                                  adminUsername    String                     Administrator
                                  password         String                     Password1
                                  imageName        String                     image1
                                  vmSize           String                     Standard_D1_v2
                                  vnetName         String                     vnet1
                                  subnetName       String                     subnet1
                                  nicName          String                     Nic1
                                  ipConfigName     String                     ipconfig1
                                  privateIPAddress  String                    10.126.68.186
        
        Outputs                 :
        DeploymentDebugLogLevel :    
        
        PS C:\07-30-2020>
    ```   

    U kunt de opdracht ook `New-AzureRmResourceGroupDeployment` asynchroon uitvoeren met `–AsJob` para meter. Hier volgt een voor beeld van uitvoer wanneer de cmdlet op de achtergrond wordt uitgevoerd. U kunt vervolgens de status van de taak die is gemaakt met behulp van de cmdlet, opvragen `Get-Job` .

    ```powershell   
    PS C:\WINDOWS\system32> New-AzureRmResourceGroupDeployment `
    >>     -ResourceGroupName $RGName `
    >>     -TemplateFile $templateFile `
    >>     -TemplateParameterFile $templateParameterFile `
    >>     -Name "Deployment2" `
    >>     -AsJob
     
    Id     Name            PSJobTypeName   State         HasMoreData     Location             Command
    --     ----            -------------   -----         -----------     --------             -------
    2      Long Running... AzureLongRun... Running       True            localhost            New-AzureRmResourceGro...
     
    PS C:\WINDOWS\system32> Get-Job -Id 2
     
    Id     Name            PSJobTypeName   State         HasMoreData     Location             Command
    --     ----            -------------   -----         -----------     --------             -------
    ```

7. Controleer of de virtuele machine is ingericht. Voer de volgende opdracht uit:

    `Get-AzureRmVm`


## <a name="connect-to-a-vm"></a>Verbinding maken met een virtuele machine

Afhankelijk van of u een Windows-of een Linux-VM hebt gemaakt, kunnen de stappen om verbinding te maken verschillend zijn.

### <a name="connect-to-windows-vm"></a>Verbinding maken met Windows VM

Volg deze stappen om verbinding te maken met een virtuele Windows-machine.

[!INCLUDE [azure-stack-edge-gateway-connect-vm](../../includes/azure-stack-edge-gateway-connect-virtual-machine-windows.md)]

### <a name="connect-to-linux-vm"></a>Verbinding maken met een virtuele Linux-machine

Volg deze stappen om verbinding te maken met een virtuele Linux-machine.

[!INCLUDE [azure-stack-edge-gateway-connect-vm](../../includes/azure-stack-edge-gateway-connect-virtual-machine-linux.md)]

<!--## Manage VM

The following section describes some of the common operations around the VM that you will create on your Azure Stack Edge Pro device.

[!INCLUDE [azure-stack-edge-gateway-manage-vm](../../includes/azure-stack-edge-gateway-manage-vm.md)]-->


## <a name="supported-vm-sizes"></a>Ondersteunde VM-grootten

[!INCLUDE [azure-stack-edge-gateway-supported-vm-sizes](../../includes/azure-stack-edge-gateway-supported-vm-sizes.md)]

## <a name="unsupported-vm-operations-and-cmdlets"></a>Niet-ondersteunde VM-bewerkingen en-cmdlets

Extensies, schaal sets, beschikbaarheids sets, moment opnamen worden niet ondersteund.

<!--## Configure AzCopy

When you install the latest version of AzCopy, you will need to configure AzCopy to ensure that it matches the blob storage REST API version of your Azure Stack Edge Pro device.

On the client used to access your Azure Stack Edge Pro device, set up a global variable to match the blob storage REST API version.

### On Windows client 

`$Env:AZCOPY_DEFAULT_SERVICE_API_VERSION = "2017-11-09"`

### On Linux client

`export AZCOPY_DEFAULT_SERVICE_API_VERSION=2017-11-09`

To verify if the environment variable for AzCopy was set correctly, take the following steps:

1. Run "azcopy env".
2. Find `AZCOPY_DEFAULT_SERVICE_API_VERSION` parameter. This should have the value you set in the preceding steps.-->


## <a name="next-steps"></a>Volgende stappen

[Azure Resource Manager-cmdlets](/powershell/module/azurerm.resources/?view=azurermps-6.13.0)