---
title: Uitbrei ding van Azure Linux-agent Stackify opnieuw traceren
description: Implementeer de Stackify-Linux-agent op een virtuele Linux-machine.
ms.topic: article
ms.service: virtual-machines
ms.subservice: extensions
author: amjads1
ms.author: amjads
ms.collection: linux
ms.date: 04/12/2018
ms.openlocfilehash: 1fc437637fde524da125af9191bf9de79a2e9c37
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/10/2021
ms.locfileid: "102558998"
---
# <a name="stackify-retrace-linux-agent-extension"></a>Stackify-extensie voor Linux-agent opnieuw traceren

## <a name="overview"></a>Overzicht

Stackify biedt producten die gegevens over uw toepassing volgen om snel problemen op te sporen en op te lossen. Voor ontwikkel teams is het opnieuw traceren van een volledig geïntegreerde app-prestatie toepassing met meerdere omgevingen. Het combineert diverse hulp middelen die elk ontwikkel team nodig heeft.

Retrace is het enige hulp programma dat alle van de volgende mogelijkheden in alle omgevingen in één platform levert.

* Beheer van toepassings prestaties (APM)
* Logboek registratie van toepassingen en server
* Fout opsporing en-bewaking
* Server-, toepassings-en aangepaste metrische gegevens

**Over de Stackify Linux agent-extensie**

Deze uitbrei ding biedt een installatiepad voor de Linux-agent voor opnieuw traceren. 

## <a name="prerequisites"></a>Vereisten

### <a name="operating-system"></a>Besturingssysteem 

De retrace-agent kan worden uitgevoerd met deze Linux-distributies

| Distributie | Versie |
|---|---|
| Ubuntu | 16,04 LTS, 14,04 LTS, 16,10 en 17,04 |
| Debian | 7,9 + en 8.2 +, 9 |
| Red Hat | 6,7 +, 7.1 + |
| CentOS | 6.3 +, 7.0 + |

### <a name="internet-connectivity"></a>Internetconnectiviteit

De Stackify-agent extensie voor Linux vereist dat de virtuele doel machine is verbonden met internet. 

Mogelijk moet u uw netwerk configuratie aanpassen om verbindingen met Stackify toe te staan. Zie voor meer informatie https://support.stackify.com/hc/en-us/articles/207891903-Adding-Exceptions-to-a-Firewall . 


## <a name="extension-schema"></a>Extensieschema

---

In de volgende JSON wordt het schema voor de uitbrei ding Stackify retrace agent weer gegeven. Voor de uitbrei ding is de `environment` and vereist `activationKey` .

```json
    {
      "type": "extensions",
      "name": "StackifyExtension",
      "apiVersion": "[variables('apiVersion')]",
      "location": "[resourceGroup().location]",
      "dependsOn": [
        "[resourceId('Microsoft.Compute/virtualMachines',variables('vmName'))]"
      ],
      "properties": {
        "publisher": "Stackify.LinuxAgent.Extension",
        "type": "StackifyLinuxAgentExtension",
        "typeHandlerVersion": "1.0",
        "autoUpgradeMinorVersion": true,
        "settings": {
          "environment": "myEnvironment"
        },
        "protectedSettings": {
          "activationKey": "myActivationKey"
        }
      }
    }      
```

## <a name="template-deployment"></a>Sjabloonimplementatie 

Azure VM-extensies kunnen worden geïmplementeerd met Azure Resource Manager sjablonen. Het JSON-schema dat in de vorige sectie wordt beschreven, kan worden gebruikt in een Azure Resource Manager sjabloon voor het uitvoeren van de Stackify-extensie voor het opnieuw traceren van de Linux-agent tijdens de implementatie van een Azure Resource Manager sjabloon.  

De JSON voor een extensie van een virtuele machine kan worden genest in de resource van de virtuele machine of worden geplaatst op het hoofd niveau of op de hoogste niveaus van een JSON-sjabloon van Resource Manager. De plaatsing van de JSON is van invloed op de waarde van de naam en het type van de resource. Zie voor meer informatie naam en type voor onderliggende resources instellen.

In het volgende voor beeld wordt ervan uitgegaan dat de Linux-uitbrei ding Stackify retrace is genest in de resource van de virtuele machine. Bij het nesten van de extensie bron wordt de JSON in het object ' resources ': [] van de virtuele machine geplaatst.

Voor de uitbrei ding is de `environment` and vereist `activationKey` .

```json
    {
      "type": "extensions",
      "name": "StackifyExtension",
      "apiVersion": "[variables('apiVersion')]",
      "location": "[resourceGroup().location]",
      "dependsOn": [
        "[resourceId('Microsoft.Compute/virtualMachines',variables('vmName'))]"
      ],
      "properties": {
        "publisher": "Stackify.LinuxAgent.Extension",
        "type": "StackifyLinuxAgentExtension",
        "typeHandlerVersion": "1.0",
        "autoUpgradeMinorVersion": true,
        "settings": {
          "environment": "myEnvironment"
        },
        "protectedSettings": {
          "activationKey": "myActivationKey"
        }
      }
    }      
```

Bij het plaatsen van de JSON van de extensie in de hoofdmap van de sjabloon, bevat de resource naam een verwijzing naar de bovenliggende virtuele machine en het type weerspiegelt de geneste configuratie.

```json
    {
        "type": "Microsoft.Compute/virtualMachines/extensions",
        "name": "<parentVmResource>/StackifyExtension",
        "apiVersion": "[variables('apiVersion')]",
        "location": "[resourceGroup().location]",
        "dependsOn": [
            "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
        ],
        "properties": {
            "publisher": "Stackify.LinuxAgent.Extension",
            "type": "StackifyLinuxAgentExtension",
            "typeHandlerVersion": "1.0",
            "autoUpgradeMinorVersion": true,
            "settings": {
              "environment": "myEnvironment"
            },
            "protectedSettings": {
              "activationKey": "myActivationKey"
            }
        }
    }
```


## <a name="powershell-deployment"></a>PowerShell-implementatie

De `Set-AzVMExtension` opdracht kan worden gebruikt voor het implementeren van de extensie van de virtuele machine van de Linux-agent Stackify naar een bestaande virtuele machine. Voordat u de opdracht uitvoert, moeten de open bare en persoonlijke configuraties worden opgeslagen in een Power shell-Hash-tabel.

Voor de uitbrei ding is de `environment` and vereist `activationKey` .

```powershell
$PublicSettings = @{"environment" = "myEnvironment"}
$ProtectedSettings = @{"activationKey" = "myActivationKey"}

Set-AzVMExtension -ExtensionName "Stackify.LinuxAgent.Extension" `
    -ResourceGroupName "myResourceGroup" `
    -VMName "myVM" `
    -Publisher "Stackify.LinuxAgent.Extension" `
    -ExtensionType "StackifyLinuxAgentExtension" `
    -TypeHandlerVersion 1.0 `
    -Settings $PublicSettings `
    -ProtectedSettings $ProtectedSettings `
    -Location WestUS `
```

## <a name="azure-cli-deployment"></a>Implementatie van Azure CLI 

Het Azure CLI-hulp programma kan worden gebruikt voor het implementeren van de extensie van de virtuele machine van de Linux-agent Stackify naar een bestaande virtuele machine.  

Voor de uitbrei ding is de `environment` and vereist `activationKey` .

```azurecli
az vm extension set --publisher 'Stackify.LinuxAgent.Extension' --version 1.0 --name 'StackifyLinuxAgentExtension' --protected-settings '{"activationKey":"myActivationKey"}' --settings '{"environment":"myEnvironment"}'  --resource-group 'myResourceGroup' --vm-name 'myVmName'
```

## <a name="troubleshoot-and-support"></a>Problemen oplossen en ondersteuning

### <a name="error-codes"></a>Foutcodes

| Foutcode | Betekenis | Mogelijke actie |
| :---: | --- | --- |
| 10 | Installatie fout | wget is vereist |
| 20 | Installatie fout | python is vereist |
| 30 | Installatie fout | sudo is vereist |
| 40 | Installatie fout | activationKey is vereist |
| 51 | Installatie fout | OS distributie wordt niet ondersteund |
| 60 | Installatie fout | omgeving is vereist |
| 70 | Installatie fout | Onbekend |
| 80 | Fout inschakelen | Installatie van de service is mislukt |
| 90 | Fout inschakelen | Het starten van de service is mislukt |
| 100 | Fout uitschakelen | Stoppen van service mislukt |
| 110 | Fout uitschakelen | Service verwijderen is mislukt |
| 120 | Fout bij verwijderen | Stoppen van service mislukt |

Als u meer hulp nodig hebt, kunt u contact opnemen met de Stackify-ondersteuning in https://support.stackify.com .