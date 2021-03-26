---
title: Vm's op uw Azure Stack Edge Pro-apparaat implementeren via sjablonen
description: Hierin wordt beschreven hoe u virtuele machines (Vm's) maakt en beheert op een Azure Stack Edge Pro-apparaat met behulp van sjablonen.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 02/22/2021
ms.author: alkohli
ms.openlocfilehash: cf236396f080af9676f211c42178ddda6a794420
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2021
ms.locfileid: "105568338"
---
# <a name="deploy-vms-on-your-azure-stack-edge-pro-gpu-device-via-templates"></a>Vm's op uw Azure Stack Edge Pro GPU-apparaat implementeren via sjablonen

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

In deze zelf studie wordt beschreven hoe u een virtuele machine op uw Azure Stack Edge Pro-apparaat maakt en beheert met behulp van sjablonen. Deze sjablonen zijn JavaScript Object Notation (JSON)-bestanden waarmee de infra structuur en configuratie voor uw virtuele machine worden gedefinieerd. In deze sjablonen geeft u de resources op die u wilt implementeren en de eigenschappen voor deze resources.

Sjablonen zijn flexibel in verschillende omgevingen, omdat deze para meters als invoer kunnen worden ingevoerd tijdens runtime vanuit een bestand. De standaard naamgevings structuur is `TemplateName.json` voor de sjabloon en `TemplateName.parameters.json` voor het parameter bestand. Ga voor meer informatie over ARM-sjablonen naar [Wat zijn Azure Resource Manager sjablonen?](../azure-resource-manager/templates/overview.md).

In deze zelf studie gebruiken we vooraf geschreven voorbeeld sjablonen voor het maken van resources. U hoeft het sjabloon bestand niet te bewerken en u kunt alleen de `.parameters.json` bestanden wijzigen om de implementatie aan uw computer aan te passen. 

## <a name="vm-deployment-workflow"></a>VM-implementatiewerkstroom

Als u Azure Stack Edge Pro-Vm's wilt implementeren op een groot aantal apparaten, kunt u één Sysprep-VHD gebruiken voor uw volledige vloot, dezelfde sjabloon voor implementatie en hoeft u alleen kleine wijzigingen aan te brengen in de para meters voor die sjabloon voor elke implementatie locatie (deze wijzigingen kunnen hand matig worden uitgevoerd, zoals we hier of programmatisch doen.) 

Het overzicht op hoog niveau van de implementatie werk stroom met behulp van sjablonen is als volgt:

1. **Vereisten configureren** -er zijn drie soorten vereisten: apparaat, client en voor de virtuele machine.

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

    1. Een VM-installatie kopie maken met behulp van `CreateImage.parameters.json` para meters bestand en `CreateImage.json` implementatie sjabloon.
    1. Maak een virtuele machine met eerder gemaakte resources met behulp van `CreateVM.parameters.json` para meters bestand en  `CreateVM.json` implementatie sjabloon.

## <a name="device-prerequisites"></a>Vereisten voor apparaten

Configureer deze vereisten op uw Azure Stack Edge Pro-apparaat.

[!INCLUDE [azure-stack-edge-gateway-deploy-virtual-machine-prerequisites](../../includes/azure-stack-edge-gateway-deploy-virtual-machine-prerequisites.md)]

## <a name="client-prerequisites"></a>Client vereisten

Configureer deze vereisten op uw client die worden gebruikt voor toegang tot het Azure Stack Edge Pro-apparaat.

1. [Down load Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) als u het gebruikt om een VHD te uploaden. U kunt ook AzCopy downloaden om een VHD te uploaden. Mogelijk moet u TLS 1,2 op uw client computer configureren als u oudere versies van AzCopy uitvoert. 
1. [Down load de VM-sjablonen en parameter bestanden](https://aka.ms/ase-vm-templates) naar uw client computer. Pak het bestand uit in een map die u wilt gebruiken als werkmap.


## <a name="vm-prerequisites"></a>VM-vereisten

Deze vereisten configureren om de resources te maken die nodig zijn voor het maken van VM'S. 

    
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

### <a name="create-a-storage-account"></a>Een opslagaccount maken

Maak een nieuw opslag account met behulp van de resource groep die u in de vorige stap hebt gemaakt. Dit account is een **lokaal opslag account** dat wordt gebruikt voor het uploaden van de installatie kopie van de virtuele schijf voor de VM.

```powershell
New-AzureRmStorageAccount -Name <Storage account name> -ResourceGroupName <Resource group name> -Location DBELocal -SkuName Standard_LRS
```

> [!NOTE]
> Alleen de lokale opslag accounts, zoals lokaal redundante opslag (Standard_LRS of Premium_LRS), kunnen worden gemaakt via Azure Resource Manager. Als u gelaagde opslag accounts wilt maken, raadpleegt u de stappen in [toevoegen, verbinding maken met opslag accounts op uw Azure stack Edge Pro](./azure-stack-edge-gpu-deploy-add-storage-accounts.md).

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

Sla deze stap over als u verbinding wilt maken via Storage Explorer met behulp van *http*. Als u *https* gebruikt, moet u de juiste certificaten installeren in Storage Explorer. In dit geval installeert u het BLOB-eindpunt certificaat. Zie certificaten maken en uploaden in [certificaten beheren](azure-stack-edge-gpu-manage-certificates.md)voor meer informatie. 

### <a name="create-and-upload-a-vhd"></a>Een VHD maken en uploaden

Zorg ervoor dat u een installatie kopie van een virtuele schijf hebt die u in de volgende stap kunt uploaden. Volg de stappen in [een VM-installatie kopie maken](azure-stack-edge-gpu-create-virtual-machine-image.md). 

Kopieer de schijf installatie kopieën die moeten worden gebruikt in pagina-blobs in het lokale opslag account dat u in de vorige stappen hebt gemaakt. U kunt een hulp programma zoals [Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) of [AzCopy gebruiken om de VHD te uploaden naar het opslag account](azure-stack-edge-gpu-deploy-virtual-machine-powershell.md#upload-a-vhd) dat u in de vorige stappen hebt gemaakt. 

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

12. Kopieer de **URI** en sla deze op die u in latere stappen gaat gebruiken.

    ![URI kopiëren](media/azure-stack-edge-gpu-deploy-virtual-machine-templates/copy-uri-1.png)


## <a name="create-image-for-your-vm"></a>Een installatie kopie maken voor uw virtuele machine

Als u een installatie kopie voor uw virtuele machine wilt maken, bewerkt u het `CreateImage.parameters.json` parameter bestand en implementeert u vervolgens de sjabloon `CreateImage.json` die gebruikmaakt van dit parameter bestand.


### <a name="edit-parameters-file"></a>Parameter bestand bewerken

Het bestand `CreateImage.parameters.json` heeft de volgende para meters: 

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
    }
```

Bewerk het bestand `CreateImage.parameters.json` en voeg de volgende waarden toe voor uw Azure stack Edge Pro-apparaat:

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

   Als u *http* gebruikt met Storage Explorer, wijzigt u de URI in een *http-* URI.

3. Geef een unieke naam op voor de installatie kopie. Deze installatie kopie wordt gebruikt om een virtuele machine te maken in de volgende stappen. 

   Hier volgt een voor beeld van een JSON dat in dit artikel wordt gebruikt.

    ```json
    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
        "contentVersion": "1.0.0.0",
      "parameters": {
        "osType": {
          "value": "Linux"
        },
        "imageName": {
          "value": "myaselinuximg"
        },
        "imageUri": {
          "value": "https://sa2.blob.myasegpuvm.wdshcsso.com/con1/ubuntu18.04waagent.vhd"
        }
      }
    }
    ```

5. Sla het parameter bestand op.


### <a name="deploy-template"></a>Sjabloon implementeren 

Implementeer de sjabloon `CreateImage.json` . Met deze sjabloon implementeert u de installatie kopie bronnen die worden gebruikt voor het maken van Vm's in de volgende stap.

> [!NOTE]
> Wanneer u de sjabloon implementeert als u een verificatie fout krijgt, zijn uw Azure-referenties voor deze sessie mogelijk verlopen. Voer de `login-AzureRM` opdracht opnieuw uit om verbinding te maken met Azure Resource Manager op uw Azure stack Edge Pro-apparaat opnieuw.

1. Voer de volgende opdracht uit: 
    
    ```powershell
    $templateFile = "Path to CreateImage.json"
    $templateParameterFile = "Path to CreateImage.parameters.json"
    $RGName = "<Name of your resource group>"
    New-AzureRmResourceGroupDeployment `
        -ResourceGroupName $RGName `
        -TemplateFile $templateFile `
        -TemplateParameterFile $templateParameterFile `
        -Name "<Name for your deployment>"
    ```
    Met deze opdracht wordt een afbeeldings bron geïmplementeerd. Voer de volgende opdracht uit om een query uit te voeren op de bron:

    ```powershell
    Get-AzureRmImage -ResourceGroupName <Resource Group Name> -name <Image Name>
    ``` 
    Hier volgt een voor beeld van de uitvoer van een geslaagde installatie kopie.
    
    ```powershell
    PS C:\WINDOWS\system32> login-AzureRMAccount -EnvironmentName aztest -TenantId c0257de7-538f-415c-993a-1b87a031879d
    
    Account               SubscriptionName              TenantId                             Environment
    -------               ----------------              --------                             -----------
    EdgeArmUser@localhost Default Provider Subscription c0257de7-538f-415c-993a-1b87a031879d aztest
    
   PS C:\WINDOWS\system32> $templateFile = "C:\12-09-2020\CreateImage\CreateImage.json"
    PS C:\WINDOWS\system32> $templateParameterFile = "C:\12-09-2020\CreateImage\CreateImage.parameters.json"
    PS C:\WINDOWS\system32> $RGName = "rg2"
    PS C:\WINDOWS\system32> New-AzureRmResourceGroupDeployment -ResourceGroupName $RGName -TemplateFile $templateFile -TemplateParameterFile $templateParameterFile -Name "deployment4"
        
    DeploymentName          : deployment4
    ResourceGroupName       : rg2
    ProvisioningState       : Succeeded
    Timestamp               : 12/10/2020 7:06:57 PM
    Mode                    : Incremental
    TemplateLink            :
    Parameters              :
                              Name             Type                       Value
                              ===============  =========================  ==========
                              osType           String                     Linux
                              imageName        String                     myaselinuximg
                              imageUri         String
                              https://sa2.blob.myasegpuvm.wdshcsso.com/con1/ubuntu18.04waagent.vhd
    
    Outputs                 :
    DeploymentDebugLogLevel :    
    PS C:\WINDOWS\system32>
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
            "value": "<Name for the virtual network, use ASEVNET>"
        },
        "subnetName": {
            "value": "<Name for the subnet, use ASEVNETsubNet>"
        },
        "vnetRG": {
            "value": "<Resource group for Vnet, use ASERG>"
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
1. Wanneer u de netwerk interface voor Compute hebt ingeschakeld, is er automatisch een virtuele switch en een virtueel netwerk op die netwerk interface gemaakt. U kunt een query uitvoeren op het bestaande virtuele netwerk om de Vnet-naam, de Subnetnaam en de naam van de Vnet-resource groep op te halen.

    Voer de volgende opdracht uit:

    ```powershell
    Get-AzureRmVirtualNetwork
    ```
    Hier volgt een voorbeeld van uitvoer:
    
    ```powershell
    
    PS C:\WINDOWS\system32> Get-AzureRmVirtualNetwork
    
    Name                   : ASEVNET
    ResourceGroupName      : ASERG
    Location               : dbelocal
    Id                     : /subscriptions/947b3cfd-7a1b-4a90-7cc5-e52caf221332/resourceGroups/ASERG/providers/Microsoft
                             .Network/virtualNetworks/ASEVNET
    Etag                   : W/"990b306d-18b6-41ea-a456-b275efe21105"
    ResourceGuid           : f8309d81-19e9-42fc-b4ed-d573f00e61ed
    ProvisioningState      : Succeeded
    Tags                   :
    AddressSpace           : {
                               "AddressPrefixes": [
                                 "10.57.48.0/21"
                               ]
                             }
    DhcpOptions            : null
    Subnets                : [
                               {
                                 "Name": "ASEVNETsubNet",
                                 "Etag": "W/\"990b306d-18b6-41ea-a456-b275efe21105\"",
                                 "Id": "/subscriptions/947b3cfd-7a1b-4a90-7cc5-e52caf221332/resourceGroups/ASERG/provider
                             s/Microsoft.Network/virtualNetworks/ASEVNET/subnets/ASEVNETsubNet",
                                 "AddressPrefix": "10.57.48.0/21",
                                 "IpConfigurations": [],
                                 "ResourceNavigationLinks": [],
                                 "ServiceEndpoints": [],
                                 "ProvisioningState": "Succeeded"
                               }
                             ]
    VirtualNetworkPeerings : []
    EnableDDoSProtection   : false
    EnableVmProtection     : false
    
    PS C:\WINDOWS\system32>
    ```

    Gebruik ASEVNET voor Vnet-naam, ASEVNETsubNet voor subnet naam en ASERG voor Vnet-resource groeps naam.
    
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
              "value": "VM1"
          },
          "adminUsername": {
              "value": "Administrator"
          },
          "Password": {
              "value": "Password1"
          },
        "imageName": {
          "value": "myaselinuximg"
        },
        "vmSize": {
          "value": "Standard_NC4as_T4_v3"
        },
        "vnetName": {
          "value": "ASEVNET"
        },
        "subnetName": {
          "value": "ASEVNETsubNet"
        },
        "vnetRG": {
          "value": "aserg"
        },
        "nicName": {
          "value": "nic5"
        },
        "privateIPAddress": {
          "value": ""
        },
        "IPConfigName": {
          "value": "ipconfig5"
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
    PS C:\WINDOWS\system32> $templateFile = "C:\12-09-2020\CreateVM\CreateVM.json"
    PS C:\WINDOWS\system32> $templateParameterFile = "C:\12-09-2020\CreateVM\CreateVM.parameters.json"
    PS C:\WINDOWS\system32> $RGName = "rg2"
    PS C:\WINDOWS\system32> New-AzureRmResourceGroupDeployment -ResourceGroupName $RGName -TemplateFile $templateFile -TemplateParameterFile $templateParameterFile -Name "Deployment6"
       
    DeploymentName          : Deployment6
    ResourceGroupName       : rg2
    ProvisioningState       : Succeeded
    Timestamp               : 12/10/2020 7:51:28 PM
    Mode                    : Incremental
    TemplateLink            :
    Parameters              :
                              Name             Type                       Value
                              ===============  =========================  ==========
                              vmName           String                     VM1
                              adminUsername    String                     Administrator
                              password         String                     Password1
                              imageName        String                     myaselinuximg
                              vmSize           String                     Standard_NC4as_T4_v3
                              vnetName         String                     ASEVNET
                              vnetRG           String                     aserg
                              subnetName       String                     ASEVNETsubNet
                              nicName          String                     nic5
                              ipConfigName     String                     ipconfig5
                              privateIPAddress  String
    
    Outputs                 :
    DeploymentDebugLogLevel :
    
    PS C:\WINDOWS\system32
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



## <a name="next-steps"></a>Volgende stappen

[Azure Resource Manager-cmdlets](/powershell/module/azurerm.resources/?view=azurermps-6.13.0&preserve-view=true)