---
title: Chef-extensie voor Azure-Vm's
description: Implementeer de chef-client op een virtuele machine met behulp van de VM-extensie chef.
ms.topic: article
ms.service: virtual-machines
ms.subservice: extensions
ms.author: amjads
author: amjads1
ms.collection: linux
ms.date: 09/21/2018
ms.openlocfilehash: e316bf9763dd7c2cbbab21992086eac52d108912
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102554782"
---
# <a name="chef-vm-extension-for-linux-and-windows"></a>Chef VM-extensie voor Linux en Windows

Chef Software levert een DevOps-automatiseringsplatform voor Linux en Windows. Hiermee kunnen zowel fysieke als virtuele serverconfiguraties worden beheerd. De chef-VM-extensie is een uitbrei ding waarmee chef op virtuele machines worden ingeschakeld.

## <a name="prerequisites"></a>Vereisten

### <a name="operating-system"></a>Besturingssysteem

De chef-VM-extensie wordt ondersteund op alle [besturings systemen](https://support.microsoft.com/help/4078134/azure-extension-supported-operating-systems) die door de extensie worden ondersteund in Azure.

### <a name="internet-connectivity"></a>Internetconnectiviteit

De chef-VM-extensie vereist dat de virtuele doel machine is verbonden met internet om de chef-client Payload op te halen van het Content Delivery Network (CDN).  

## <a name="extension-schema"></a>Extensieschema

In de volgende JSON wordt het schema voor de VM-extensie chef weer gegeven. Voor de uitbrei ding zijn mini maal de URL van de chef-server, de naam van de validatie-client en de validatie sleutel voor de chef-server vereist. deze waarden zijn te vinden in het `knife.rb` bestand in de starter-kit.zip dat wordt gedownload wanneer u [chef Automatiseer](https://azuremarketplace.microsoft.com/marketplace/apps/chef-software.chef-automate) of een zelfstandige [chef-server](https://downloads.chef.io/chef-server)installeert. Omdat de validatie sleutel moet worden behandeld als gevoelige gegevens, moet deze worden geconfigureerd onder het **protectedSettings** -element, wat inhoudt dat deze alleen wordt ontsleuteld op de virtuele doel machine.

```json
{
  "type": "Microsoft.Compute/virtualMachines/extensions",
  "name": "[concat(variables('vmName'),'/', parameters('chef_vm_extension_type'))]",
  "apiVersion": "2017-12-01",
  "location": "[parameters('location')]",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
  ],
  "properties": {
    "publisher": "Chef.Bootstrap.WindowsAzure",
    "type": "[parameters('chef_vm_extension_type')]",
    "typeHandlerVersion": "1210.13",
    "settings": {
      "bootstrap_options": {
        "chef_server_url": "[parameters('chef_server_url')]",
        "validation_client_name": "[parameters('chef_validation_client_name')]"
      },
      "runlist": "[parameters('chef_runlist')]"
    },
    "protectedSettings": {
      "validation_key": "[parameters('chef_validation_key')]"
    }
  }
}  
```

### <a name="core-property-values"></a>Kern eigenschaps waarden

| Name | Waarde/voor beeld | Gegevenstype
| ---- | ---- | ----
| apiVersion | `2017-12-01` | teken reeks (datum) |
| publisher | `Chef.Bootstrap.WindowsAzure` | tekenreeks |
| type | `LinuxChefClient` (Linux), `ChefClient` (Windows) | tekenreeks |
| typeHandlerVersion | `1210.13` | teken reeks (dubbel) |

### <a name="settings"></a>Instellingen

| Name | Waarde/voor beeld | Gegevenstype | Vereist?
| ---- | ---- | ---- | ----
| instellingen/bootstrap_options/chef_server_url | `https://api.chef.io/organizations/myorg` | teken reeks (URL) | J |
| instellingen/bootstrap_options/validation_client_name | `myorg-validator` | tekenreeks | J |
| instellingen/runlist | `recipe[mycookbook::default]` | tekenreeks | J |

### <a name="protected-settings"></a>Beveiligde instellingen

| Name | Voorbeeld | Gegevenstype | Vereist?
| ---- | ---- | ---- | ---- |
| protectedSettings/validation_key | `-----BEGIN RSA PRIVATE KEY-----\nKEYDATA\n-----END RSA PRIVATE KEY-----` | tekenreeks | J |

<!--
### Linux-specific settings

| Name | Value / Example | Data Type |
| ---- | ---- | ---- |

### Windows-specific settings

| Name | Value / Example | Data Type |
| ---- | ---- | ---- |
-->

## <a name="template-deployment"></a>Sjabloonimplementatie

Azure VM-extensies kunnen worden geïmplementeerd met Azure Resource Manager sjablonen. Sjablonen kunnen worden gebruikt om een of meer virtuele machines te implementeren, de chef-client te installeren, verbinding te maken met de chef-server en de eerste configuratie op de server uit te voeren, zoals gedefinieerd in de [lijst Run-List](https://docs.chef.io/run_lists.html)

Een voor beeld van een resource manager-sjabloon met de extensie chef VM vindt u in de [Galerie van Azure Quick](https://github.com/Azure/azure-quickstart-templates/tree/master/chef-json-parameters-linux-vm)start.

De JSON-configuratie voor een extensie van een virtuele machine kan worden genest in de resource van de virtuele machine of worden geplaatst op het hoofd niveau of op de hoogste niveaus van een JSON-sjabloon van Resource Manager. De plaatsing van de JSON-configuratie is van invloed op de waarde van de naam en het type van de resource. Zie voor meer informatie [naam en type voor onderliggende resources instellen](../../azure-resource-manager/templates/child-resource-name-type.md).

## <a name="azure-cli-deployment"></a>Implementatie van Azure CLI

De Azure CLI kan worden gebruikt om de chef-VM-extensie te implementeren op een bestaande virtuele machine. Vervang de **validation_key** door de inhoud van uw validatie sleutel (dit bestand als een `.pem` extensie).  Vervang **validation_client_name**, **chef_server_url** en **run_list** door deze waarden uit het `knife.rb` bestand in uw Start pakket.

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myExistingVM \
  --name LinuxChefClient \
  --publisher Chef.Bootstrap.WindowsAzure \
  --version 1210.13 --protected-settings '{"validation_key": "<validation_key>"}' \
  --settings '{ "bootstrap_options": { "chef_server_url": "<chef_server_url>", "validation_client_name": "<validation_client_name>" }, "runlist": "<run_list>" }'
```

## <a name="troubleshooting-and-support"></a>Probleem oplossing en ondersteuning

Gegevens over de status van uitbreidings implementaties kunnen worden opgehaald uit het Azure Portal en met behulp van de Azure CLI. Als u de implementatie status van uitbrei dingen voor een bepaalde virtuele machine wilt bekijken, voert u de volgende opdracht uit met behulp van de Azure CLI.

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myExistingVM -o table
```

Uitvoer voor uitvoering van extensie wordt vastgelegd in het volgende bestand:

### <a name="linux"></a>Linux

```bash
/var/lib/waagent/Chef.Bootstrap.WindowsAzure.LinuxChefClient
```

### <a name="windows"></a>Windows

```powershell
C:\Packages\Plugins\Chef.Bootstrap.WindowsAzure.ChefClient\
```

### <a name="error-codes-and-their-meanings"></a>Fout codes en hun betekenis

| Foutcode | Betekenis | Mogelijke actie |
| :---: | --- | --- |
| 51 | Deze extensie wordt niet ondersteund op het besturings systeem van de virtuele machine | |

Meer informatie over het oplossen van problemen vindt u in het Leesmij-bestand van de [chef VM-extensie](https://github.com/chef-partners/azure-chef-extension).

> [!NOTE]
> Neem contact op met de [ondersteuning van chef](https://www.chef.io/support/)voor iets anders rechtstreeks met betrekking tot chef.

## <a name="next-steps"></a>Volgende stappen

Als u op elk moment in dit artikel meer hulp nodig hebt, kunt u contact opnemen met de Azure-experts op [MSDN Azure en stack overflow forums](https://azure.microsoft.com/support/forums/). U kunt ook een ondersteunings incident voor Azure opslaan. Ga naar de [ondersteunings site van Azure](https://azure.microsoft.com/support/options/) en selecteer ondersteuning verkrijgen. Lees de [Veelgestelde vragen over ondersteuning voor Microsoft Azure](https://azure.microsoft.com/support/faq/)voor meer informatie over het gebruik van Azure-ondersteuning.
