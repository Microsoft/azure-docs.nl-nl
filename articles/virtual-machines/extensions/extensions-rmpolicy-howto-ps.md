---
title: Azure Policy gebruiken om de installatie van VM-extensies te beperken (Windows)
description: Gebruik Azure Policy om uitbreidings implementaties te beperken.
services: virtual-machines-linux
documentationcenter: ''
author: axayjo
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.subservice: extensions
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/23/2018
ms.author: akjosh
ms.reviewer: cynthn
ms.openlocfilehash: 558b23b4a89fd161b9e97265f6e1ed82624e8254
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/20/2020
ms.locfileid: "94955596"
---
# <a name="use-azure-policy-to-restrict-extensions-installation-on-windows-vms"></a>Azure Policy gebruiken om de installatie van extensies op Windows-Vm's te beperken

Als u het gebruik of de installatie van bepaalde extensies op uw Windows-Vm's wilt voor komen, kunt u een Azure Policy definitie maken met behulp van Power shell om uitbrei dingen voor virtuele machines in een resource groep te beperken. 

In deze zelf studie wordt gebruikgemaakt van Azure PowerShell in het Cloud Shell, dat voortdurend wordt bijgewerkt naar de nieuwste versie. 

 

## <a name="create-a-rules-file"></a>Een regel bestand maken

Als u wilt beperken welke uitbrei dingen kunnen worden geïnstalleerd, moet u een [regel](../../governance/policy/concepts/definition-structure.md#policy-rule) hebben om de logica te bieden voor het identificeren van de uitbrei ding.

In dit voor beeld ziet u hoe u door micro soft. Compute uitbrei dingen die zijn gepubliceerd, kunt weigeren door een regel bestand te maken in Azure Cloud Shell, maar als u lokaal werkt in Power shell, is het mogelijk om ook een lokaal bestand te maken en het pad ($home/clouddrive) te vervangen door het pad naar het lokale bestand op uw computer.

Typ het volgende in een [Cloud shell](https://shell.azure.com/powershell):

```azurepowershell-interactive
nano $home/clouddrive/rules.json
```

Kopieer en plak de volgende json in het bestand.

```json
{
    "if": {
        "allOf": [
            {
                "field": "type",
                "equals": "Microsoft.Compute/virtualMachines/extensions"
            },
            {
                "field": "Microsoft.Compute/virtualMachines/extensions/publisher",
                "equals": "Microsoft.Compute"
            },
            {
                "field": "Microsoft.Compute/virtualMachines/extensions/type",
                "in": "[parameters('notAllowedExtensions')]"
            }
        ]
    },
    "then": {
        "effect": "deny"
    }
}
```

Wanneer u klaar bent, drukt u op **CTRL + O** en vervolgens op **Enter** om het bestand op te slaan. Druk op **CTRL + X** om het bestand te sluiten en te sluiten.

## <a name="create-a-parameters-file"></a>Een parameter bestand maken

U hebt ook een [parameter](../../governance/policy/concepts/definition-structure.md#parameters) bestand nodig dat een structuur maakt die u kunt gebruiken voor het door geven van een lijst met de uitbrei dingen die moeten worden geblokkeerd. 

Dit voor beeld laat zien hoe u een parameter bestand voor virtuele machines in Cloud Shell maakt, maar als u lokaal in Power shell werkt, kunt u ook een lokaal bestand maken en het pad ($home/clouddrive) vervangen door het pad naar het lokale bestand op uw computer.

Typ in [Cloud shell](https://shell.azure.com/powershell):

```azurepowershell-interactive
nano $home/clouddrive/parameters.json
```

Kopieer en plak de volgende json in het bestand.

```json
{
    "notAllowedExtensions": {
        "type": "Array",
        "metadata": {
            "description": "The list of extensions that will be denied.",
            "displayName": "Denied extension"
        }
    }
}
```

Wanneer u klaar bent, drukt u op **CTRL + O** en vervolgens op **Enter** om het bestand op te slaan. Druk op **CTRL + X** om het bestand te sluiten en te sluiten.

## <a name="create-the-policy"></a>Beleid maken

Een beleids definitie is een object dat wordt gebruikt voor het opslaan van de configuratie die u wilt gebruiken. De beleids definitie gebruikt de regels en parameter bestanden om het beleid te definiëren. Een beleids definitie maken met de cmdlet [New-AzPolicyDefinition](/powershell/module/az.resources/new-azpolicydefinition) .

 De beleids regels en para meters zijn de bestanden die u hebt gemaakt en opgeslagen als json-bestanden in uw Cloud shell.


```azurepowershell-interactive
$definition = New-AzPolicyDefinition `
   -Name "not-allowed-vmextension-windows" `
   -DisplayName "Not allowed VM Extensions" `
   -description "This policy governs which VM extensions that are explicitly denied."   `
   -Policy 'C:\Users\ContainerAdministrator\clouddrive\rules.json' `
   -Parameter 'C:\Users\ContainerAdministrator\clouddrive\parameters.json'
```




## <a name="assign-the-policy"></a>Wijs het beleid toe

In dit voor beeld wordt het beleid toegewezen aan een resource groep met behulp van [New-AzPolicyAssignment](/powershell/module/az.resources/new-azpolicyassignment). Elke VM die in de resource groep **myResourceGroup** is gemaakt, kan de VM-toegangs agent of aangepaste script extensies niet installeren. 

De [Get-AzSubscription gebruiken | Maak](/powershell/module/az.accounts/get-azsubscription) de cmdlet Table om uw abonnements-id op te halen die u wilt gebruiken in plaats van het voor beeld.

```azurepowershell-interactive
$scope = "/subscriptions/<subscription id>/resourceGroups/myResourceGroup"
$assignment = New-AzPolicyAssignment `
   -Name "not-allowed-vmextension-windows" `
   -Scope $scope `
   -PolicyDefinition $definition `
   -PolicyParameter '{
    "notAllowedExtensions": {
        "value": [
            "VMAccessAgent",
            "CustomScriptExtension"
        ]
    }
}'
$assignment
```

## <a name="test-the-policy"></a>Het beleid testen

Als u het beleid wilt testen, probeert u de extensie VM-toegang te gebruiken. Het volgende moet mislukken met het bericht ' set-AzVMAccessExtension: resource ' myVMAccess ' is niet toegestaan door beleid '.

```azurepowershell-interactive
Set-AzVMAccessExtension `
   -ResourceGroupName "myResourceGroup" `
   -VMName "myVM" `
   -Name "myVMAccess" `
   -Location EastUS 
```

In de portal moet de wijziging van het wacht woord mislukken met de implementatie van de sjabloon is mislukt vanwege een overtreding van het beleid. .

## <a name="remove-the-assignment"></a>De toewijzing verwijderen

```azurepowershell-interactive
Remove-AzPolicyAssignment -Name not-allowed-vmextension-windows -Scope $scope
```

## <a name="remove-the-policy"></a>Het beleid verwijderen

```azurepowershell-interactive
Remove-AzPolicyDefinition -Name not-allowed-vmextension-windows
```
    
## <a name="next-steps"></a>Volgende stappen
Zie [Azure Policy](../../governance/policy/overview.md) voor meer informatie.
