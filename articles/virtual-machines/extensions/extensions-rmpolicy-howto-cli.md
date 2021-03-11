---
title: Azure Policy gebruiken om de installatie van de VM-extensie te beperken (Linux)
description: Gebruik Azure Policy om implementaties van VM-extensies te beperken.
ms.topic: article
ms.service: virtual-machines
ms.subservice: extensions
author: amjads1
ms.author: amjads
ms.collection: linux
ms.date: 03/23/2018
ms.openlocfilehash: 582f690c00ff8bdd06e4d2867724aa482bcacaf3
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/10/2021
ms.locfileid: "102559967"
---
# <a name="use-azure-policy-to-restrict-extensions-installation-on-linux-vms"></a>Azure Policy gebruiken om de installatie van extensies op Linux-Vm's te beperken

Als u wilt voor komen dat bepaalde uitbrei dingen op uw Linux-Vm's worden gebruikt of geïnstalleerd, kunt u een Azure Policy definitie maken met behulp van de CLI om uitbrei dingen voor virtuele machines in een resource groep te beperken. 

In deze zelfstudie wordt gebruikgemaakt van de CLI in de Azure Cloud Shell, die voortdurend wordt bijgewerkt naar de nieuwste versie. Als u de Azure CLI lokaal wilt uitvoeren, moet u versie 2.0.26 of hoger installeren. Voer `az --version` uit om de versie te bekijken. Zie [Azure CLI installeren]( /cli/azure/install-azure-cli) als u de CLI wilt installeren of een upgrade wilt uitvoeren. 

## <a name="create-a-rules-file"></a>Een regel bestand maken

Als u wilt beperken welke uitbrei dingen kunnen worden geïnstalleerd, moet u een [regel](../../governance/policy/concepts/definition-structure.md#policy-rule) hebben om de logica te bieden voor het identificeren van de uitbrei ding.

In dit voor beeld ziet u hoe u installatie-extensies die zijn gepubliceerd door ' micro soft. OSTCExtensions ', kunt weigeren door een regel bestand te maken in Azure Cloud Shell, maar als u lokaal werkt in CLI, is het mogelijk om ook een lokaal bestand te maken en het pad (~/clouddrive) te vervangen door het pad naar het lokale bestand op uw computer.

In een [bash-Cloud shell](https://shell.azure.com/bash), type:

```bash
vim ~/clouddrive/azurepolicy.rules.json
```

Kopieer en plak de volgende json in het bestand.

```json
{
    "if": {
        "allOf": [
            {
                "field": "type",
                "equals": "Microsoft.OSTCExtensions/virtualMachines/extensions"
            },
            {
                "field": "Microsoft.OSTCExtensions/virtualMachines/extensions/publisher",
                "equals": "Microsoft.OSTCExtensions"
            },
            {
                "field": "Microsoft.OSTCExtensions/virtualMachines/extensions/type",
                "in": "[parameters('notAllowedExtensions')]"
            }
        ]
    },
    "then": {
        "effect": "deny"
    }
}
```

Wanneer u klaar bent, drukt u op de **ESC** -toets en typt u **: wq** om het bestand op te slaan en te sluiten.


## <a name="create-a-parameters-file"></a>Een parameter bestand maken

U hebt ook een [parameter](../../governance/policy/concepts/definition-structure.md#parameters) bestand nodig dat een structuur maakt die u kunt gebruiken voor het door geven van een lijst met de uitbrei dingen die moeten worden geblokkeerd. 

Dit voor beeld laat zien hoe u een parameter bestand voor Linux-Vm's in Cloud Shell maakt, maar als u lokaal werkt in CLI, kunt u ook een lokaal bestand maken en het pad (~/clouddrive) vervangen door het pad naar het lokale bestand op uw computer.

Typ in het [Cloud shell bash](https://shell.azure.com/bash):

```bash
vim ~/clouddrive/azurepolicy.parameters.json
```

Kopieer en plak de volgende json in het bestand.

```json
{
    "notAllowedExtensions": {
        "type": "Array",
        "metadata": {
            "description": "The list of extensions that will be denied. Example: CustomScriptForLinux, VMAccessForLinux etc.",
            "displayName": "Denied extension"
        }
    }
}
```

Wanneer u klaar bent, drukt u op de **ESC** -toets en typt u **: wq** om het bestand op te slaan en te sluiten.

## <a name="create-the-policy"></a>Beleid maken

Een beleids definitie is een object dat wordt gebruikt voor het opslaan van de configuratie die u wilt gebruiken. De beleids definitie gebruikt de regels en parameter bestanden om het beleid te definiëren. Maak de beleids definitie met [AZ Policy Definition Create](/cli/azure/role/assignment).

In dit voor beeld zijn de regels en para meters de bestanden die u hebt gemaakt en opgeslagen als json-bestanden in uw Cloud shell.

```azurecli-interactive
az policy definition create \
   --name 'not-allowed-vmextension-linux' \
   --display-name 'Block VM Extensions' \
   --description 'This policy governs which VM extensions that are blocked.' \
   --rules '~/clouddrive/azurepolicy.rules.json' \
   --params '~/clouddrive/azurepolicy.parameters.json' \
   --mode All
```


## <a name="assign-the-policy"></a>Wijs het beleid toe

In dit voor beeld wordt het beleid toegewezen aan een resource groep met [AZ Policy Assignment Create](/cli/azure/policy/assignment). Elke VM die in de resource groep **myResourceGroup** is gemaakt, kan de Linux-VM-toegang of de aangepaste script extensies voor Linux niet installeren. De resource groep moet bestaan voordat u het beleid kunt toewijzen.

Gebruik [AZ account list](/cli/azure/account) om uw abonnements-id te verkrijgen die u wilt gebruiken in plaats van het voor beeld.


```azurecli-interactive
az policy assignment create \
   --name 'not-allowed-vmextension-linux' \
   --scope /subscriptions/<subscription Id>/resourceGroups/myResourceGroup \
   --policy "not-allowed-vmextension-linux" \
   --params '{
        "notAllowedExtensions": {
            "value": [
                "VMAccessForLinux",
                "CustomScriptForLinux"
            ]
        }
    }'
```

## <a name="test-the-policy"></a>Het beleid testen

Test het beleid door een nieuwe virtuele machine te maken en een nieuwe gebruiker toe te voegen.


```azurecli-interactive
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image UbuntuLTS \
    --generate-ssh-keys
```

Probeer een nieuwe gebruiker met de naam **myNewUser** te maken met behulp van de extensie VM-toegang.

```azurecli-interactive
az vm user update \
  --resource-group myResourceGroup \
  --name myVM \
  --username myNewUser \
  --password 'mynewuserpwd123!'
```



## <a name="remove-the-assignment"></a>De toewijzing verwijderen

```azurecli-interactive
az policy assignment delete --name 'not-allowed-vmextension-linux' --resource-group myResourceGroup
```
## <a name="remove-the-policy"></a>Het beleid verwijderen

```azurecli-interactive
az policy definition delete --name 'not-allowed-vmextension-linux'
```

## <a name="next-steps"></a>Volgende stappen

Zie [Azure Policy](../../governance/policy/overview.md) voor meer informatie.
