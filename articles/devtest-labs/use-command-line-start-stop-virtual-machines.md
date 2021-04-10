---
title: Gebruik opdracht regel Programma's om Vm's te starten en te stoppen Azure DevTest Labs
description: Meer informatie over het gebruik van opdracht regel Programma's voor het starten en stoppen van virtuele machines in Azure DevTest Labs.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 2ddc1620cf86fa203b2f0e31359f9fd262df8916
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "102499540"
---
# <a name="use-command-line-tools-to-start-and-stop-azure-devtest-labs-virtual-machines"></a>Opdracht regel Programma's gebruiken om Azure DevTest Labs virtuele machines te starten en te stoppen
In dit artikel wordt beschreven hoe u Azure PowerShell of Azure CLI gebruikt om virtuele machines in een lab in Azure DevTest Labs te starten of te stoppen. U kunt Power shell/CLI-scripts maken om deze bewerkingen te automatiseren. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="overview"></a>Overzicht
Azure DevTest Labs is een manier om snelle, eenvoudige en flexibele ontwikkel-en test omgevingen te maken. Zo kunt u kosten beheren, virtuele machines snel inrichten en afval minimaliseren.  Er zijn ingebouwde functies in de Azure Portal waarmee u Vm's in een lab kunt configureren om automatisch te starten en te stoppen op specifieke tijdstippen. 

In sommige gevallen wilt u echter mogelijk het starten en stoppen van Vm's vanuit Power shell/CLI-scripts automatiseren. Het biedt u de mogelijkheid om op elk gewenst moment afzonderlijke machines te starten en te stoppen, in plaats van op specifieke tijdstippen. Hier volgen enkele van de situaties waarin het uitvoeren van deze taken met behulp van scripts handig is.

- Wanneer u een 3-tier-toepassing gebruikt als onderdeel van een test omgeving, moeten de lagen in een reeks worden gestart. 
- Een virtuele machine uitschakelen wanneer aan een aangepast criterium wordt voldaan om geld te besparen. 
- Gebruik het als een taak binnen een CI/CD-werk stroom om te beginnen bij het begin van de stroom, gebruik de Vm's als Build-machines, test machines of infra structuur en Stop vervolgens de virtuele machines wanneer het proces is voltooid. Een voor beeld hiervan is de aangepaste installatie kopie fabriek met Azure DevTest Labs.  

## <a name="azure-powershell"></a>Azure PowerShell

> [!NOTE]
> Het volgende script maakt gebruik van de Azure PowerShell AZ-module. 

Met het volgende Power shell-script wordt een virtuele machine in een Lab gestart. [Invoke-AzResourceAction](/powershell/module/az.resources/invoke-azresourceaction) is de primaire focus voor dit script. De **ResourceID** para meter is de volledig gekwalificeerde resource-id voor de virtuele machine in het lab. Met de **actie** parameter worden de opties voor **starten** of **stoppen** ingesteld, afhankelijk van wat er nodig is.

```powershell
# The id of the subscription
$subscriptionId = "111111-11111-11111-1111111"

# The name of the lab
$devTestLabName = "yourlabname"

# The name of the virtual machine to be started
$vMToStart = "vmname"

# The action on the virtual machine (Start or Stop)
$vmAction = "Start"

# Select the Azure subscription
Select-AzSubscription -SubscriptionId $subscriptionId

# Get the lab information
$devTestLab = Get-AzResource -ResourceType 'Microsoft.DevTestLab/labs' -ResourceName $devTestLabName

# Start the VM and return a succeeded or failed status
$returnStatus = Invoke-AzResourceAction `
                    -ResourceId "$($devTestLab.ResourceId)/virtualmachines/$vMToStart" `
                    -Action $vmAction `
                    -Force

if ($returnStatus.Status -eq 'Succeeded') {
    Write-Output "##[section] Successfully updated DTL machine: $vMToStart, Action: $vmAction"
}
else {
    Write-Error "##[error]Failed to update DTL machine: $vMToStart, Action: $vmAction"
}
```


## <a name="azure-cli"></a>Azure CLI
De [Azure cli](/cli/azure/get-started-with-azure-cli) is een andere manier om het starten en stoppen van DevTest Labs-vm's te automatiseren. Azure CLI kan worden [geïnstalleerd](/cli/azure/install-azure-cli) op verschillende besturings systemen. Met het volgende script krijgt u opdrachten voor het starten en stoppen van een virtuele machine in een lab. 

```azurecli
# Sign in to Azure
az login 

## Get the name of the resource group that contains the lab
az resource list --resource-type "Microsoft.DevTestLab/labs" --name "yourlabname" --query "[0].resourceGroup" 

## Start the VM
az lab vm start --lab-name yourlabname --name vmname --resource-group labResourceGroupName

## Stop the VM
az lab vm stop --lab-name yourlabname --name vmname --resource-group labResourceGroupName
```


## <a name="next-steps"></a>Volgende stappen
Raadpleeg het volgende artikel voor het gebruik van de Azure Portal om deze bewerkingen uit te voeren: [Start een virtuele machine opnieuw](devtest-lab-restart-vm.md)op.
