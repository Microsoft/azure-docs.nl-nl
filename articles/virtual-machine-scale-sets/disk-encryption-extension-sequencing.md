---
title: Schaal sets voor Azure Disk Encryption en Azure virtual machine scaleset
description: In dit artikel vindt u informatie over het inschakelen van Microsoft Azure schijf versleuteling voor virtuele Linux IaaS-machines.
author: ju-shim
ms.author: jushiman
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: disks
ms.date: 10/10/2019
ms.reviewer: mimckitt
ms.custom: mimckitt
ms.openlocfilehash: 1aff05e51bcbc99f33325efb905ade819ae22e02
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "90988022"
---
# <a name="use-azure-disk-encryption-with-virtual-machine-scale-set-extension-sequencing"></a>Azure Disk Encryption gebruiken met extensiereeksen voor virtuele-machineschaalsets

Extensies, zoals Azure Disk Encryption, kunnen worden toegevoegd aan een schaalset voor virtuele Azure-machines in een opgegeven volg orde. U doet dit door gebruik te maken van [extensie volgorde bepaling](virtual-machine-scale-sets-extension-sequencing.md). 

In het algemeen moet versleuteling worden toegepast op een schijf:

- Na extensies of aangepaste scripts waarmee de schijven of volumes worden voor bereid.
- Vóór extensies of aangepaste scripts die de gegevens op de versleutelde schijven of volumes benaderen of gebruiken.

In beide gevallen geeft de `provisionAfterExtensions` eigenschap aan welke extensie later in de reeks moet worden toegevoegd.

## <a name="sample-azure-templates"></a>Voor beelden van Azure-sjablonen

Als u Azure Disk Encryption na een andere uitbrei ding wilt Toep assen, plaatst u de `provisionAfterExtensions` eigenschap in het AzureDiskEncryption-extensie blok. 

Hier volgt een voor beeld van het gebruik van ' CustomScriptExtension ', een Power shell-script voor het initialiseren en Format teren van een Windows-schijf, gevolgd door ' AzureDiskEncryption ':

```json
"virtualMachineProfile": {
  "extensionProfile": {
    "extensions": [
      {
        "type": "Microsoft.Compute/virtualMachineScaleSets/extensions",
        "name": "CustomScriptExtension",
        "location": "[resourceGroup().location]",
        "properties": {
          "publisher": "Microsoft.Compute",
          "type": "CustomScriptExtension",
          "typeHandlerVersion": "1.9",
          "autoUpgradeMinorVersion": true,
          "forceUpdateTag": "[parameters('forceUpdateTag')]",
          "settings": {
            "fileUris": [
              "https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/ade-vmss/FormatMBRDisk.ps1"
            ]
          },
          "protectedSettings": {
           "commandToExecute": "powershell -ExecutionPolicy Unrestricted -File FormatMBRDisk.ps1"
          }
        }
      },
      {
        "type": "Microsoft.Compute/virtualMachineScaleSets/extensions",
        "name": "AzureDiskEncryption",
        "location": "[resourceGroup().location]",
        "properties": {
          "provisionAfterExtensions": [
            "CustomScriptExtension"
          ],
          "publisher": "Microsoft.Azure.Security",
          "type": "AzureDiskEncryption",
          "typeHandlerVersion": "2.2",
          "autoUpgradeMinorVersion": true,
          "forceUpdateTag": "[parameters('forceUpdateTag')]",
          "settings": {
            "EncryptionOperation": "EnableEncryption",
            "KeyVaultURL": "[reference(variables('keyVaultResourceId'),'2018-02-14-preview').vaultUri]",
            "KeyVaultResourceId": "[variables('keyVaultResourceID')]",
            "KeyEncryptionKeyURL": "[parameters('keyEncryptionKeyURL')]",
            "KekVaultResourceId": "[variables('keyVaultResourceID')]",
            "KeyEncryptionAlgorithm": "[parameters('keyEncryptionAlgorithm')]",
            "VolumeType": "[parameters('volumeType')]",
            "SequenceVersion": "[parameters('sequenceVersion')]"
          }
        }
      },
    ]
  }
}
```

Als u Azure Disk Encryption wilt Toep assen vóór een andere uitbrei ding, plaatst u de `provisionAfterExtensions` eigenschap in het blok van de uitbrei ding die moet worden gevolgd.

Hier volgt een voor beeld van het gebruik van ' AzureDiskEncryption ', gevolgd door ' VMDiagnosticsSettings ', een uitbrei ding die de bewakings-en diagnostische mogelijkheden biedt voor een Azure-VM op basis van Windows:


```json
"virtualMachineProfile": {
  "extensionProfile": {
    "extensions": [
      {
        "name": "AzureDiskEncryption",
        "type": "Microsoft.Compute/virtualMachineScaleSets/extensions",
        "location": "[resourceGroup().location]",
        "properties": {
          "publisher": "Microsoft.Azure.Security",
          "type": "AzureDiskEncryption",
          "typeHandlerVersion": "2.2",
          "autoUpgradeMinorVersion": true,
          "forceUpdateTag": "[parameters('forceUpdateTag')]",
          "settings": {
            "EncryptionOperation": "EnableEncryption",
            "KeyVaultURL": "[reference(variables('keyVaultResourceId'),'2018-02-14-preview').vaultUri]",
            "KeyVaultResourceId": "[variables('keyVaultResourceID')]",
            "KeyEncryptionKeyURL": "[parameters('keyEncryptionKeyURL')]",
            "KekVaultResourceId": "[variables('keyVaultResourceID')]",
            "KeyEncryptionAlgorithm": "[parameters('keyEncryptionAlgorithm')]",
            "VolumeType": "[parameters('volumeType')]",
            "SequenceVersion": "[parameters('sequenceVersion')]"
          }
        }
      },
      { 
        "name": "Microsoft.Insights.VMDiagnosticsSettings", 
        "type": "extensions", 
        "location": "[resourceGroup().location]", 
        "apiVersion": "2016-03-30", 
        "dependsOn": [ 
          "[concat('Microsoft.Compute/virtualMachines/myVM', copyindex())]" 
        ], 
        "properties": { 
          "provisionAfterExtensions": [
            "AzureDiskEncryption"
          ],
        "publisher": "Microsoft.Azure.Diagnostics", 
          "type": "IaaSDiagnostics", 
          "typeHandlerVersion": "1.5", 
          "autoUpgradeMinorVersion": true, 
          "settings": { 
            "xmlCfg": "[base64(concat(variables('wadcfgxstart'), 
            variables('wadmetricsresourceid'), 
            concat('myVM', copyindex()),
            variables('wadcfgxend')))]", 
            "storageAccount": "[variables('storageName')]" 
          }, 
          "protectedSettings": { 
            "storageAccountName": "[variables('storageName')]", 
            "storageAccountKey": "[listkeys(variables('accountid'), 
              '2015-06-15').key1]", 
            "storageAccountEndPoint": "https://core.windows.net" 
          } 
        } 
      },
    ]
  }
}
```

Voor een dieper sjabloon raadpleegt u:
* Pas de Azure Disk Encryption-extensie toe na een aangepast shell-script waarmee de schijf (Linux) wordt geformatteerd: [deploy-extseq-linux-ADE-after-customscript.jsop](https://github.com/Azure-Samples/compute-automation-configurations/blob/master/ade-vmss/deploy-extseq-linux-ADE-after-customscript.json)


## <a name="next-steps"></a>Volgende stappen
- Meer informatie over de volg orde van extensies: [Sequence-inrichting inrichten in virtuele-machine schaal sets](virtual-machine-scale-sets-extension-sequencing.md).
- Meer informatie over de `provisionAfterExtensions` eigenschap: [micro soft. Compute virtualMachineScaleSets/Extensions Temp late Reference](/azure/templates/microsoft.compute/2018-10-01/virtualmachinescalesets/extensions).
- [Azure Disk Encryption voor virtuele-machineschaalsets](disk-encryption-overview.md)
- [ Een virtuele-machineschaalset versleutelen met de Azure CLI](disk-encryption-cli.md)
- [Een virtuele-machineschaalset versleutelen met de Azure PowerShell](disk-encryption-powershell.md)
- [Een sleutelkluis voor Azure Disk Encryption maken en configureren](disk-encryption-key-vault.md)
