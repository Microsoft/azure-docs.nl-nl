---
title: VM-extensie Azure Key Vault voor Windows
description: Implementeer een agent voor het automatisch vernieuwen van Key Vault geheimen op virtuele machines met behulp van de extensie van een virtuele machine.
services: virtual-machines
author: msmbaldwin
tags: keyvault
ms.service: virtual-machines
ms.subservice: extensions
ms.collection: windows
ms.topic: article
ms.date: 12/02/2019
ms.author: mbaldwin
ms.openlocfilehash: a984d044134dbd775bacb653f8590ee78724f15b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "102563554"
---
# <a name="key-vault-virtual-machine-extension-for-windows"></a>Extensie van de virtuele machine Key Vault voor Windows

De Key Vault VM-extensie biedt automatisch vernieuwen van certificaten die zijn opgeslagen in een Azure-sleutel kluis. De uitbrei ding bewaakt met name een lijst van waargenomen certificaten die zijn opgeslagen in sleutel kluizen, en bij het detecteren van een wijziging, ophalen en installeren van de bijbehorende certificaten. In dit document vindt u informatie over de ondersteunde platforms, configuraties en implementatie opties voor de Key Vault VM-extensie voor Windows. 

### <a name="operating-system"></a>Besturingssysteem

De Key Vault VM-extensie ondersteunt de volgende versies van Windows:

- Windows Server 2019
- Windows Server 2016
- Windows Server 2012

De extensie van de Key Vault-VM wordt ook ondersteund op een aangepaste lokale virtuele machine die wordt geüpload en geconverteerd naar een gespecialiseerde installatie kopie voor gebruik in azure met behulp van Windows Server 2019 Core-installatie.

### <a name="supported-certificate-content-types"></a>Ondersteunde inhouds typen voor certificaten

- PKCS #12
- PEM

## <a name="prerequisites"></a>Vereisten

  - Key Vault-exemplaar met het certificaat. Zie [een Key Vault maken](../../key-vault/general/quick-create-portal.md)
  - De virtuele machine moet een [beheerde identiteit](../../active-directory/managed-identities-azure-resources/overview.md) hebben toegewezen
  - Het Key Vault toegangs beleid moet zijn ingesteld met geheimen `get` en `list` machtigingen voor de door de virtuele machine/VMSS beheerde identiteit om een geheim gedeelte van het certificaat op te halen. Zie [verifiëren bij Key Vault](../../key-vault/general/authentication.md) en [een Key Vault toegangs beleid toewijzen](../../key-vault/general/assign-access-policy-cli.md).
  -  Virtual Machine Scale Sets moet de volgende identiteits instelling hebben:

  ``` 
  "identity": {
    "type": "UserAssigned",
    "userAssignedIdentities": {
      "[parameters('userAssignedIdentityResourceId')]": {}
    }
  }
  ```
  
  - De Azure-extensie moet deze instelling hebben:

  ```
  "authenticationSettings": {
    "msiEndpoint": "[parameters('userAssignedIdentityEndpoint')]",
    "msiClientId": "[reference(parameters('userAssignedIdentityResourceId'), variables('msiApiVersion')).clientId]"
  }
  ```

## <a name="extension-schema"></a>Extensieschema

De volgende JSON toont het schema voor de extensie van de Key Vault-VM. Voor de extensie zijn geen beveiligde instellingen vereist: alle instellingen ervan worden beschouwd als open bare informatie. De uitbrei ding vereist een lijst met bewaakte certificaten, polling frequentie en het doel certificaat archief. Met name:  

```json
    {
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "name": "KVVMExtensionForWindows",
      "apiVersion": "2019-07-01",
      "location": "<location>",
      "dependsOn": [
          "[concat('Microsoft.Compute/virtualMachines/', <vmName>)]"
      ],
      "properties": {
      "publisher": "Microsoft.Azure.KeyVault",
      "type": "KeyVaultForWindows",
      "typeHandlerVersion": "1.0",
      "autoUpgradeMinorVersion": true,
      "settings": {
        "secretsManagementSettings": {
          "pollingIntervalInS": <polling interval in seconds, e.g: "3600">,
          "certificateStoreName": <certificate store name, e.g.: "MY">,
          "linkOnRenewal": <Only Windows. This feature ensures s-channel binding when certificate renews, without necessitating a re-deployment.  e.g.: false>,
          "certificateStoreLocation": <certificate store location, currently it works locally only e.g.: "LocalMachine">,
          "requireInitialSync": <initial synchronization of certificates e..g: true>,
          "observedCertificates": <list of KeyVault URIs representing monitored certificates, e.g.: "https://myvault.vault.azure.net/secrets/mycertificate"
        },
        "authenticationSettings": {
                "msiEndpoint":  <Optional MSI endpoint e.g.: "http://169.254.169.254/metadata/identity">,
                "msiClientId":  <Optional MSI identity e.g.: "c7373ae5-91c2-4165-8ab6-7381d6e75619">
        }
       }
      }
    }
```

> [!NOTE]
> De Url's van uw waargenomen certificaten moeten van het formulier zijn `https://myVaultName.vault.azure.net/secrets/myCertName` .
> 
> Dit komt doordat het `/secrets` pad het volledige certificaat retourneert, inclusief de persoonlijke sleutel, terwijl het `/certificates` pad niet. Meer informatie over certificaten vindt u hier: [Key Vault certificaten](../../key-vault/general/about-keys-secrets-certificates.md)

> [!IMPORTANT]
> De eigenschap authenticationSettings is alleen **vereist** voor vm's met door de **gebruiker toegewezen identiteiten**.
> Hiermee wordt de identiteit opgegeven die moet worden gebruikt voor de verificatie van Key Vault.


### <a name="property-values"></a>Eigenschaps waarden

| Name | Waarde/voor beeld | Gegevenstype |
| ---- | ---- | ---- |
| apiVersion | 2019-07-01 | date |
| publisher | Microsoft.Azure.KeyVault | tekenreeks |
| type | KeyVaultForWindows | tekenreeks |
| typeHandlerVersion | 1.0 | int |
| pollingIntervalInS | 3600 | tekenreeks |
| Naam certificaat archief | MY | tekenreeks |
| linkOnRenewal | onjuist | booleaans |
| certificateStoreLocation  | LocalMachine of CurrentUser (hoofdletter gevoelig) | tekenreeks |
| requireInitialSync | true | booleaans |
| observedCertificates  | ["https://myvault.vault.azure.net/secrets/mycertificate","https://myvault.vault.azure.net/secrets/mycertificate2"] | teken reeks matrix
| msiEndpoint | http://169.254.169.254/metadata/identity | tekenreeks |
| msiClientId | c7373ae5-91c2-4165-8ab6-7381d6e75619 | tekenreeks |


## <a name="template-deployment"></a>Sjabloonimplementatie

Azure VM-extensies kunnen worden geïmplementeerd met Azure Resource Manager sjablonen. Sjablonen zijn ideaal bij het implementeren van een of meer virtuele machines die na de implementatie van certificaten moeten worden vernieuwd. De uitbrei ding kan worden geïmplementeerd op afzonderlijke Vm's of virtuele-machine schaal sets. Het schema en de configuratie zijn gebruikelijk voor beide sjabloon typen. 

De JSON-configuratie voor een extensie van een virtuele machine moet zijn genest in het resource fragment van de virtuele machine van de sjabloon, met name `"resources": []` object voor de virtuele-machine sjabloon en in het geval van een schaalset voor virtuele machines onder `"virtualMachineProfile":"extensionProfile":{"extensions" :[]` object.

 > [!NOTE]
> Voor de VM-extensie moet een door het systeem of de gebruiker beheerde identiteit worden toegewezen om te verifiëren bij de sleutel kluis.  Zie [verifiëren bij Key Vault en een Key Vault toegangs beleid toewijzen.](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)
> 

```json
    {
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "name": "KeyVaultForWindows",
      "apiVersion": "2019-07-01",
      "location": "<location>",
      "dependsOn": [
          "[concat('Microsoft.Compute/virtualMachines/', <vmName>)]"
      ],
      "properties": {
      "publisher": "Microsoft.Azure.KeyVault",
      "type": "KeyVaultForWindows",
      "typeHandlerVersion": "1.0",
      "autoUpgradeMinorVersion": true,
      "settings": {
        "secretsManagementSettings": {
          "pollingIntervalInS": <polling interval in seconds, e.g: "3600">,
          "certificateStoreName": <certificate store name, e.g.: "MY">,
          "certificateStoreLocation": <certificate store location, currently it works locally only e.g.: "LocalMachine">,
          "observedCertificates": <list of KeyVault URIs representing monitored certificates, e.g.: ["https://myvault.vault.azure.net/secrets/mycertificate", "https://myvault.vault.azure.net/secrets/mycertificate2"]>
        }      
      }
      }
    }
```

### <a name="extension-dependency-ordering"></a>Volg orde van extensie afhankelijkheid
De extensie van de Key Vault-VM ondersteunt de volg orde van de extensie als deze is geconfigureerd. De uitbrei ding rapporteert standaard dat deze is gestart zodra het pollen is gestart. Het kan echter zo worden geconfigureerd dat er wordt gewacht tot de volledige lijst met certificaten is gedownload voordat een geslaagde start wordt gerapporteerd. Als andere uitbrei dingen afhankelijk zijn van de installatie van de volledige set certificaten voordat ze worden gestart, kunt u met deze instelling een afhankelijkheid declareren voor de uitbrei ding van de Key Vault. Hiermee voor komt u dat de uitbrei dingen worden gestart totdat alle certificaten waarvan ze afhankelijk zijn, zijn geïnstalleerd. De uitbrei ding zal de eerste down load voor onbepaalde tijd opnieuw proberen en blijven in een `Transitioning` status.

Als u dit wilt inschakelen, stelt u het volgende in:
```
"secretsManagementSettings": {
    "requireInitialSync": true,
    ...
}
```

> [!Note] 
> Het gebruik van deze functie is niet compatibel met een ARM-sjabloon die een door het systeem toegewezen identiteit maakt en een Key Vault toegangs beleid met die identiteit bijwerkt. Dit leidt tot een deadlock als het toegangs beleid voor de kluis pas kan worden bijgewerkt als alle uitbrei dingen zijn gestart. U moet in plaats daarvan *één door de gebruiker toegewezen MSI-identiteit* gebruiken en uw kluizen vooraf-ACL met die identiteit voordat u implementeert.

## <a name="azure-powershell-deployment"></a>Azure PowerShell-implementatie
> [!WARNING]
> Power shell-clients voegen vaak toe `\` aan `"` in de settings.js, waardoor akvvm_service mislukt met de volgende fout: `[CertificateManagementConfiguration] Failed to parse the configuration settings with:not an object.`

De Azure PowerShell kan worden gebruikt om de Key Vault VM-extensie te implementeren op een bestaande virtuele machine of virtuele-machine schaalset. 

* De uitbrei ding implementeren op een virtuele machine:
    
    ```powershell
        # Build settings
        $settings = '{"secretsManagementSettings": 
        { "pollingIntervalInS": "' + <pollingInterval> + 
        '", "certificateStoreName": "' + <certStoreName> + 
        '", "certificateStoreLocation": "' + <certStoreLoc> + 
        '", "observedCertificates": ["' + <observedCert1> + '","' + <observedCert2> + '"] } }'
        $extName =  "KeyVaultForWindows"
        $extPublisher = "Microsoft.Azure.KeyVault"
        $extType = "KeyVaultForWindows"
       
    
        # Start the deployment
        Set-AzVmExtension -TypeHandlerVersion "1.0" -ResourceGroupName <ResourceGroupName> -Location <Location> -VMName <VMName> -Name $extName -Publisher $extPublisher -Type $extType -SettingString $settings
    
    ```

* De uitbrei ding implementeren op een schaalset voor virtuele machines:

    ```powershell
    
        # Build settings
        $settings = '{"secretsManagementSettings": 
        { "pollingIntervalInS": "' + <pollingInterval> + 
        '", "certificateStoreName": "' + <certStoreName> + 
        '", "certificateStoreLocation": "' + <certStoreLoc> + 
        '", "observedCertificates": ["' + <observedCert1> + '","' + <observedCert2> + '"] } }'
        $extName = "KeyVaultForWindows"
        $extPublisher = "Microsoft.Azure.KeyVault"
        $extType = "KeyVaultForWindows"
        
        # Add Extension to VMSS
        $vmss = Get-AzVmss -ResourceGroupName <ResourceGroupName> -VMScaleSetName <VmssName>
        Add-AzVmssExtension -VirtualMachineScaleSet $vmss  -Name $extName -Publisher $extPublisher -Type $extType -TypeHandlerVersion "1.0" -Setting $settings

        # Start the deployment
        Update-AzVmss -ResourceGroupName <ResourceGroupName> -VMScaleSetName <VmssName> -VirtualMachineScaleSet $vmss 
    
    ```

## <a name="azure-cli-deployment"></a>Implementatie van Azure CLI

De Azure CLI kan worden gebruikt om de Key Vault VM-extensie te implementeren op een bestaande virtuele machine of virtuele-machine schaalset. 
 
* De uitbrei ding implementeren op een virtuele machine:
    
    ```azurecli
       # Start the deployment
         az vm extension set --name "KeyVaultForWindows" `
         --publisher Microsoft.Azure.KeyVault `
         --resource-group "<resourcegroup>" `
         --vm-name "<vmName>" `
         --settings '{\"secretsManagementSettings\": { \"pollingIntervalInS\": \"<pollingInterval>\", \"certificateStoreName\": \"<certStoreName>\", \"certificateStoreLocation\": \"<certStoreLoc>\", \"observedCertificates\": [\" <observedCert1> \", \" <observedCert2> \"] }}'
    ```

* De uitbrei ding implementeren op een schaalset voor virtuele machines:

   ```azurecli
        # Start the deployment
        az vmss extension set -name "KeyVaultForWindows" `
         --publisher Microsoft.Azure.KeyVault `
         -resource-group "<resourcegroup>" `
         --vmss-name "<vmName>" `
         --settings '{\"secretsManagementSettings\": { \"pollingIntervalInS\": \"<pollingInterval>\", \"certificateStoreName\": \"<certStoreName>\", \"certificateStoreLocation\": \"<certStoreLoc>\", \"observedCertificates\": [\" <observedCert1> \", \" <observedCert2> \"] }}'
    ```

Houd rekening met de volgende beperkingen/vereisten:
- Key Vault beperkingen:
  - Deze moet op het moment van de implementatie bestaan 
  - Het Key Vault toegangs beleid moet worden ingesteld voor de virtuele machine-VMSS met behulp van een beheerde identiteit. Zie [verifiëren bij Key Vault](../../key-vault/general/authentication.md) en [een Key Vault toegangs beleid toewijzen](../../key-vault/general/assign-access-policy-cli.md).

## <a name="troubleshoot-and-support"></a>Problemen oplossen en ondersteuning

### <a name="frequently-asked-questions"></a>Veelgestelde vragen

* Is er een limiet voor het aantal observedCertificates dat u kunt instellen?
  Nee, Key Vault VM-extensie heeft geen limiet voor het aantal observedCertificates.

### <a name="troubleshoot"></a>Problemen oplossen

Gegevens over de status van uitbreidings implementaties kunnen worden opgehaald uit de Azure Portal en met behulp van de Azure PowerShell. Als u de implementatie status van extensies voor een bepaalde virtuele machine wilt bekijken, voert u de volgende opdracht uit met behulp van de Azure PowerShell.

**Azure PowerShell**
```powershell
Get-AzVMExtension -VMName <vmName> -ResourceGroupname <resource group name>
```

**Azure-CLI**
```azurecli
 az vm get-instance-view --resource-group <resource group name> --name  <vmName> --query "instanceView.extensions"
```

#### <a name="logs-and-configuration"></a>Logboeken en configuratie

```
%windrive%\WindowsAzure\Logs\Plugins\Microsoft.Azure.KeyVault.KeyVaultForWindows\<version>\akvvm_service_<date>.log
```

### <a name="support"></a>Ondersteuning

Als u op elk moment in dit artikel meer hulp nodig hebt, kunt u contact opnemen met de Azure-experts op [MSDN Azure en stack overflow forums](https://azure.microsoft.com/support/forums/). U kunt ook een ondersteunings incident voor Azure opslaan. Ga naar de [ondersteunings site van Azure](https://azure.microsoft.com/support/options/) en selecteer ondersteuning verkrijgen. Lees de [Veelgestelde vragen over ondersteuning voor Microsoft Azure](https://azure.microsoft.com/support/faq/)voor meer informatie over het gebruik van Azure-ondersteuning.
