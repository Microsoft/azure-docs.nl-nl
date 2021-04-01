---
title: Desired state Configuration gebruiken met Virtual Machine Scale Sets
description: Met behulp van Virtual Machine Scale Sets met de Azure desired state Configuration-extensie voor het configureren van virtuele machines.
author: ju-shim
ms.author: jushiman
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: extensions
ms.date: 6/25/2020
ms.reviewer: mimckitt
ms.custom: mimckitt
ms.openlocfilehash: 20e5bff87d5cd0d6e0a35a558462bb5598bfe3f4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "87080485"
---
# <a name="using-virtual-machine-scale-sets-with-the-azure-dsc-extension"></a>Virtual Machine Scale Sets gebruiken met de Azure DSC-uitbrei ding
[Virtual Machine Scale sets](./overview.md) kan worden gebruikt met de extensie [-handler Azure desired state Configuration (DSC)](../virtual-machines/extensions/dsc-overview.md?toc=/azure/virtual-machines/windows/toc.json) . Virtuele-machine schaal sets bieden een manier om grote aantallen virtuele machines te implementeren en te beheren, en kunnen in-en uitgroeien in antwoord op belasting. DSC wordt gebruikt voor het configureren van de virtuele machines zodra deze online zijn, zodat ze de productie software uitvoeren.

## <a name="differences-between-deploying-to-virtual-machines-and-virtual-machine-scale-sets"></a>Verschillen tussen de implementatie van Virtual Machines en Virtual Machine Scale Sets
De onderliggende sjabloon structuur voor een schaalset voor virtuele machines wijkt enigszins af van één virtuele machine. In het bijzonder implementeert één VM uitbrei dingen onder het knoop punt ' informatie '. Er is een invoer van het type ' uitbrei dingen ' waar DSC aan de sjabloon wordt toegevoegd

```
"resources": [
          {
              "name": "Microsoft.Powershell.DSC",
              "type": "extensions",
              "location": "[resourceGroup().location]",
              "apiVersion": "2015-06-15",
              "dependsOn": [
                  "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
              ],
              "tags": {
                  "displayName": "dscExtension"
              },
              "properties": {
                  "publisher": "Microsoft.Powershell",
                  "type": "DSC",
                  "typeHandlerVersion": "2.20",
                  "autoUpgradeMinorVersion": false,
                  "forceUpdateTag": "[parameters('dscExtensionUpdateTagVersion')]",
                  "settings": {
                      "configuration": {
                          "url": "[concat(parameters('_artifactsLocation'), '/', variables('dscExtensionArchiveFolder'), '/', variables('dscExtensionArchiveFileName'))]",
                          "script": "DscExtension.ps1",
                          "function": "Main"
                      },
                      "configurationArguments": {
                          "nodeName": "[variables('vmName')]"
                      }
                  },
                  "protectedSettings": {
                      "configurationUrlSasToken": "[parameters('_artifactsLocationSasToken')]"
                  }
              }
          }
      ]
```

Een knoop punt van een virtuele-machine schaalset heeft een sectie ' Eigenschappen ' met het kenmerk ' VirtualMachineProfile ', ' extensionProfile '. DSC wordt toegevoegd onder ' uitbrei dingen '

```
"extensionProfile": {
            "extensions": [
                {
                    "name": "Microsoft.Powershell.DSC",
                    "properties": {
                        "publisher": "Microsoft.Powershell",
                        "type": "DSC",
                        "typeHandlerVersion": "2.20",
                        "autoUpgradeMinorVersion": false,
                        "forceUpdateTag": "[parameters('DscExtensionUpdateTagVersion')]",
                        "settings": {
                            "configuration": {
                                "url": "[concat(parameters('_artifactsLocation'), '/', variables('DscExtensionArchiveFolder'), '/', variables('DscExtensionArchiveFileName'))]",
                                "script": "DscExtension.ps1",
                                "function": "Main"
                            },
                            "configurationArguments": {
                                "nodeName": "localhost"
                            }
                        },
                        "protectedSettings": {
                            "configurationUrlSasToken": "[parameters('_artifactsLocationSasToken')]"
                        }
                    }
                }
            ]
```

## <a name="behavior-for-a-virtual-machine-scale-set"></a>Gedrag voor een Schaalset voor virtuele machines
Het gedrag voor een schaalset voor virtuele machines is identiek aan het gedrag voor één virtuele machine. Wanneer een nieuwe virtuele machine wordt gemaakt, wordt deze automatisch ingericht met de DSC-extensie. Als er een nieuwere versie van de WMF wordt vereist door de extensie, wordt de VM opnieuw opgestart voordat deze online is. Zodra deze online is, worden de DSC-configuratie. zip gedownload en op de VM ingericht. Meer informatie vindt u in [het overzicht van Azure DSC-extensies](../virtual-machines/extensions/dsc-overview.md?toc=/azure/virtual-machines/windows/toc.json).

## <a name="next-steps"></a>Volgende stappen
Bekijk de [Azure Resource Manager-sjabloon voor de DSC-extensie](../virtual-machines/extensions/dsc-template.md?toc=/azure/virtual-machines/windows/toc.json).

Meer informatie over hoe de [DSC-extensie veilig referenties afhandelt](../virtual-machines/extensions/dsc-credentials.md?toc=/azure/virtual-machines/windows/toc.json). 

Zie [Introduction to the Azure desired state Configuration extension handler](../virtual-machines/extensions/dsc-overview.md?toc=/azure/virtual-machines/windows/toc.json)(Engelstalig) voor meer informatie over de Azure DSC-extensie-handler. 

[Ga naar het Power shell-documentatie centrum](/powershell/scripting/dsc/overview/overview)voor meer informatie over Power shell DSC. 
