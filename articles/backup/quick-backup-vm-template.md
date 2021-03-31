---
title: Quickstart - Resource Manager-sjabloon voor back-ups van VM
description: Lees hoe u een back-up van virtuele machines maakt via Azure Resource Manager-sjabloon
ms.devlang: azurecli
ms.topic: quickstart
ms.date: 05/14/2019
ms.custom: mvc,subject-armqs
ms.openlocfilehash: e64aa10d8ddadd367d04b2b480770a99f3ece1dc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "88826528"
---
# <a name="quickstart-back-up-a-virtual-machine-in-azure-with-an-arm-template"></a>Quickstart: Een back-up van een virtuele machine maken in Azure met een ARM-sjabloon

[Azure Backup](backup-overview.md) maakt een back-up van zowel on-premises machines en apps als Azure-VM's. In dit artikel ontdekt u hoe u een back-up kunt maken van een Azure VM met een Azure Resource Manager-sjabloon (ARM-sjabloon) en Azure PowerShell. Deze quickstart is gericht op het implementeren van een ARM-sjabloon om een Recovery Services-kluis te maken. Voor meer informatie over het ontwikkelen van ARM-sjablonen raadpleegt u de [documentatie voor Azure Resource Manager](../azure-resource-manager/index.yml) en de [sjabloonverwijzing](/azure/templates/microsoft.recoveryservices/allversions).

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

[Een Recovery Services-kluis](backup-azure-recovery-services-vault-overview.md) is een logische container waarin de back-upgegevens voor beveiligde resources worden opgeslagen, zoals virtuele Azure-machines. Wanneer en back-uptaak wordt uitgevoerd, wordt er binnen de Recovery Services-kluis een herstelpunt gemaakt. U kunt vervolgens een van deze herstelpunten gebruiken om gegevens voor dat tijdstip te herstellen. U kunt ook een back-up maken van een VM met behulp van [Azure PowerShell](./quick-backup-vm-powershell.md), de [Azure CLI](quick-backup-vm-cli.md) of in het [Azure-portal](quick-backup-vm-portal.md).

Als uw omgeving voldoet aan de vereisten en u benkend bent met het gebruik van ARM-sjablonen, selecteert u de knop **Implementeren naar Azure**. De sjabloon wordt in Azure Portal geopend.

[![Implementeren in Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-recovery-services-create-vm-and-configure-backup%2Fazuredeploy.json)

## <a name="review-the-template"></a>De sjabloon controleren

De sjabloon die in deze quickstart wordt gebruikt, komt uit [Azure-quickstartsjablonen](https://azure.microsoft.com/resources/templates/101-recovery-services-create-vm-and-configure-backup/). Met deze sjabloon kunt u een eenvoudige Windows-VM en Recovery Services-kluis implementeren waarvoor de DefaultPolicy is geconfigureerd ter bescherming.

:::code language="json" source="~/quickstart-templates/101-recovery-services-create-vm-and-configure-backup/azuredeploy.json":::

De resources die in het sjabloon worden gedefinieerd zij:

- [**Microsoft.Storage/storageAccounts**](/azure/templates/microsoft.storage/storageaccounts)
- [**Microsoft.Network/publicIPAddresses**](/azure/templates/microsoft.network/publicipaddresses)
- [**Microsoft.Network/networkSecurityGroups**](/azure/templates/microsoft.network/networksecuritygroups)
- [**Microsoft.Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks)
- [**Microsoft.Network/networkInterfaces**](/azure/templates/microsoft.network/networkinterfaces)
- [**Microsoft.Compute/virtualMachines**](/azure/templates/microsoft.compute/virtualmachines)
- [**Microsoft.RecoveryServices/vaults**](/azure/templates/microsoft.recoveryservices/2016-06-01/vaults)
- [**Microsoft.RecoveryServices/vaults/backupFabrics/protectionContainers/protectedItems**](/azure/templates/microsoft.recoveryservices/vaults/backupfabrics/protectioncontainers/protecteditems)

## <a name="deploy-the-template"></a>De sjabloon implementeren

Als u het sjabloon wilt implementeren, selecteert u **Probeer het** om de Azure Cloud-shell te openen en plakt u het volgende PowerShell-script in het shell-venster. Als u de code wilt plakken, klikt u met de rechtermuisknop op het shell-venster en selecteert u **Plakken**.

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter a project name (limited to eight characters) that is used to generate Azure resource names"
$location = Read-Host -Prompt "Enter the location (for example, centralus)"
$adminUsername = Read-Host -Prompt "Enter the administrator username for the virtual machine"
$adminPassword = Read-Host -Prompt "Enter the administrator password for the virtual machine" -AsSecureString
$dnsPrefix = Read-Host -Prompt "Enter the unique DNS Name for the Public IP used to access the virtual machine"

$resourceGroupName = "${projectName}rg"
$templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-recovery-services-create-vm-and-configure-backup/azuredeploy.json"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri -projectName $projectName -adminUsername $adminUsername -adminPassword $adminPassword -dnsLabelPrefix $dnsPrefix
```

Azure PowerShell wordt gebruikt om het ARM-sjabloon in deze quickstart te implementeren. Het [Azure-portal](../azure-resource-manager/templates/deploy-portal.md), de [Azure CLI](../azure-resource-manager/templates/deploy-cli.md)en de [Rest API](../azure-resource-manager/templates/deploy-rest.md) kunnen ook worden gebruikt om sjablonen te implementeren.

## <a name="validate-the-deployment"></a>De implementatie valideren

### <a name="start-a-backup-job"></a>Een back-uptaak starten

Het sjabloon maakt een VM en maakt back-up op de VM mogelijk. Nadat u de sjabloon hebt geïmplementeerd, moet u een back-uptaak starten. Zie [Een back-uptaak starten](./quick-backup-vm-powershell.md#start-a-backup-job)voor meer informatie.

### <a name="monitor-the-backup-job"></a>Uitvoering van back-uptaak volgen

Zie [De back-uptaak controleren](./quick-backup-vm-powershell.md#monitor-the-backup-job)om de back-uptaak te controleren.

## <a name="clean-up-resources"></a>Resources opschonen

Als u geen back-up meer nodig heeft voor de virtuele machine, dan kunt u deze opschonen.

- Als u de virtuele machine wilt proberen herstellen, sla het opschonen dan over.
- Als u een bestaande VM hebt gebruikt, kunt u de laatste cmdlet ([Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup)) overslaan om de resourcegroep en VM te behouden.

Schakel bescherming uit en verwijderen de herstelpunten en kluis. Verwijder vervolgens de resourcegroep en de bijbehorende VM-resources als volgt:

```powershell
Disable-AzRecoveryServicesBackupProtection -Item $item -RemoveRecoveryPoints
$vault = Get-AzRecoveryServicesVault -Name "myRecoveryServicesVault"
Remove-AzRecoveryServicesVault -Vault $vault
Remove-AzResourceGroup -Name "myResourceGroup"
```

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u een Recovery Services-kluis gemaakt, de beveiliging op een VM ingeschakeld en het eerste herstelpunt gemaakt.

- [Meer informatie](tutorial-backup-vm-at-scale.md) over hoe u back-ups kunt maken van VM's in het Azure-portal.
- [Meer informatie](tutorial-restore-disk.md) over hoe u snel een VM kunt herstellen
- [Meer informatie](../azure-resource-manager/templates/template-tutorial-create-first-template.md) over hoe u ARM-sjablonen kunt maken.
