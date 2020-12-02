---
title: Virtuele Azure-machines verplaatsen naar een nieuw abonnement of een nieuwe resource groep
description: Gebruik Azure Resource Manager om virtuele machines te verplaatsen naar een nieuwe resource groep of een nieuw abonnement.
ms.topic: conceptual
ms.date: 12/01/2020
ms.openlocfilehash: b1032b5a632bcac82cb9ae1f1b3df7b49f5463f5
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/01/2020
ms.locfileid: "96456313"
---
# <a name="move-guidance-for-virtual-machines"></a>Richt lijnen voor het verplaatsen van virtuele machines

In dit artikel worden de scenario's beschreven die momenteel niet worden ondersteund en de stappen voor het verplaatsen van virtuele machines met back-up.

## <a name="scenarios-not-supported"></a>Scenario's worden niet ondersteund

De volgende scenario's worden nog niet ondersteund:

* Virtual Machine Scale Sets met een standaard-SKU Load Balancer of een open bare standaard-SKU kan niet worden verplaatst.
* Virtuele machines in een bestaand virtueel netwerk kunnen niet worden verplaatst naar een nieuw abonnement wanneer u niet alle resources in het virtuele netwerk verplaatst.
* Virtuele machines die zijn gemaakt op basis van Marketplace-resources waarvoor plannen zijn gekoppeld, kunnen niet worden verplaatst naar abonnementen. Zie [virtuele machines met Marketplace-abonnementen](#virtual-machines-with-marketplace-plans)voor een mogelijke tijdelijke oplossing.
* Virtuele machines met lage prioriteit en virtuele-machine schaal sets met lage prioriteit kunnen niet worden verplaatst over resource groepen of-abonnementen.
* Virtuele machines in een beschikbaarheidsset kunnen niet afzonderlijk worden verplaatst.

## <a name="azure-disk-encryption"></a>Azure Disk Encryption

U kunt een virtuele machine die is geïntegreerd met een sleutel kluis, niet verplaatsen om Azure Disk Encryption te implementeren [voor Linux vm's](../../../virtual-machines/linux/disk-encryption-overview.md) of [Azure Disk Encryption voor Windows-vm's](../../../virtual-machines/windows/disk-encryption-overview.md). Als u de virtuele machine wilt verplaatsen, moet u versleuteling uitschakelen.

```azurecli-interactive
az vm encryption disable --resource-group demoRG --name myVm1
```

```azurepowershell-interactive
Disable-AzVMDiskEncryption -ResourceGroupName demoRG -VMName myVm1
```

## <a name="virtual-machines-with-marketplace-plans"></a>Virtuele machines met Marketplace-abonnementen

Virtuele machines die zijn gemaakt op basis van Marketplace-resources waarvoor plannen zijn gekoppeld, kunnen niet worden verplaatst naar abonnementen. U kunt deze beperking omzeilen door de inrichting van de virtuele machine in het huidige abonnement ongedaan te maken en deze opnieuw te implementeren in het nieuwe abonnement. De volgende stappen helpen u bij het opnieuw maken van de virtuele machine in het nieuwe abonnement. Ze werken echter mogelijk niet voor alle scenario's. Als het plan niet meer beschikbaar is op Marketplace, zullen deze stappen niet werken.

1. Informatie over het plan kopiëren.

1. Kloon de besturingssysteem schijf naar het doel abonnement of verplaats de oorspronkelijke schijf nadat u de virtuele machine hebt verwijderd van het bron abonnement.

1. Accepteer in het doel abonnement de Marketplace-voor waarden voor uw abonnement. U kunt de voor waarden accepteren door de volgende Power shell-opdracht uit te voeren:

   ```azurepowershell
   Get-AzMarketplaceTerms -Publisher {publisher} -Product {product/offer} -Name {name/SKU} | Set-AzMarketplaceTerms -Accept
   ```

   U kunt ook een nieuw exemplaar van een virtuele machine met het plan maken via de portal. U kunt de virtuele machine verwijderen nadat u de voor waarden in het nieuwe abonnement hebt geaccepteerd.

1. Maak in het doel abonnement de virtuele machine opnieuw van de gekloonde besturingssysteem schijf met behulp van Power shell, CLI of een Azure Resource Manager sjabloon. Voeg het Marketplace-abonnement toe dat is gekoppeld aan de schijf. De informatie over het plan moet overeenkomen met het abonnement dat u hebt aangeschaft in het nieuwe abonnement.

## <a name="virtual-machines-with-azure-backup"></a>Virtuele machines met Azure Backup

Als u virtuele machines wilt verplaatsen die zijn geconfigureerd met Azure Backup, moet u de herstel punten verwijderen uit de kluis.

Als [voorlopig verwijderen](../../../backup/backup-azure-security-feature-cloud.md) is ingeschakeld voor uw virtuele machine, kunt u de virtuele machine niet verplaatsen terwijl de herstel punten worden bewaard. [Schakel zacht verwijderen uit](../../../backup/backup-azure-security-feature-cloud.md#enabling-and-disabling-soft-delete) of wacht 14 dagen na het verwijderen van de herstel punten.

### <a name="portal"></a>Portal

1. De back-up tijdelijk stoppen en back-upgegevens opslaan.
2. Voer de volgende stappen uit om virtuele machines te verplaatsen die zijn geconfigureerd met Azure Backup:

   1. Zoek de locatie van de virtuele machine.
   2. Een resource groep zoeken met het volgende naamgevings patroon: `AzureBackupRG_<VM location>_1` . De naam heeft bijvoorbeeld de indeling van *AzureBackupRG_westus2_1*.
   3. Selecteer in het Azure Portal de optie **verborgen typen weer geven**.
   4. Zoek de resource met het type **micro soft. Compute/restorePointCollections** die het naamgevings patroon heeft `AzureBackup_<VM name>_###########` .
   5. Deze resource verwijderen. Met deze bewerking worden alleen de directe herstel punten verwijderd, niet de gegevens waarvan een back-up is gemaakt in de kluis.
   6. Nadat de bewerking delete is voltooid, kunt u de virtuele machine verplaatsen.

3. Verplaats de virtuele machine naar de doel resource groep.
4. Hervat de back-up.

### <a name="powershell"></a>PowerShell

1. Zoek de locatie van de virtuele machine.

1. Een resource groep zoeken met het naamgevings patroon- `AzureBackupRG_<VM location>_1` . De naam kan bijvoorbeeld zijn `AzureBackupRG_westus2_1` .

1. Als u slechts één virtuele machine verplaatst, haalt u de herstel punt verzameling voor die virtuele machine op.

   ```azurepowershell-interactive
   $restorePointCollection = Get-AzResource -ResourceGroupName AzureBackupRG_<VM location>_1 -name AzureBackup_<VM name>* -ResourceType Microsoft.Compute/restorePointCollections
   ```

   Deze resource verwijderen. Met deze bewerking worden alleen de directe herstel punten verwijderd, niet de gegevens waarvan een back-up is gemaakt in de kluis.

   ```azurepowershell-interactive
   Remove-AzResource -ResourceId $restorePointCollection.ResourceId -Force
   ```

1. Als u alle virtuele machines met back-ups op deze locatie wilt verplaatsen, haalt u de herstel punt verzamelingen voor die virtuele machines op.

   ```azurepowershell-interactive
   $restorePointCollection = Get-AzResource -ResourceGroupName AzureBackupRG_<VM location>_1 -ResourceType Microsoft.Compute/restorePointCollections
   ```

   Verwijder elke resource. Met deze bewerking worden alleen de directe herstel punten verwijderd, niet de gegevens waarvan een back-up is gemaakt in de kluis.

   ```azurepowershell-interactive
   foreach ($restorePoint in $restorePointCollection)
   {
     Remove-AzResource -ResourceId $restorePoint.ResourceId -Force
   }
   ```

### <a name="azure-cli"></a>Azure CLI

1. Zoek de locatie van de virtuele machine.

1. Een resource groep zoeken met het naamgevings patroon- `AzureBackupRG_<VM location>_1` . De naam kan bijvoorbeeld zijn `AzureBackupRG_westus2_1` .

1. Als u slechts één virtuele machine verplaatst, haalt u de herstel punt verzameling voor die virtuele machine op.

   ```azurecli-interactive
   RESTOREPOINTCOL=$(az resource list -g AzureBackupRG_<VM location>_1 --resource-type Microsoft.Compute/restorePointCollections --query "[?starts_with(name, 'AzureBackup_<VM name>')].id" --output tsv)
   ```

   Deze resource verwijderen. Met deze bewerking worden alleen de directe herstel punten verwijderd, niet de gegevens waarvan een back-up is gemaakt in de kluis.

   ```azurecli-interactive
   az resource delete --ids $RESTOREPOINTCOL
   ```

1. Als u alle virtuele machines met back-ups op deze locatie wilt verplaatsen, haalt u de herstel punt verzamelingen voor die virtuele machines op.

   ```azurecli-interactive
   RESTOREPOINTCOL=$(az resource list -g AzureBackupRG_<VM location>_1 --resource-type Microsoft.Compute/restorePointCollections)
   ```

   Verwijder elke resource. Met deze bewerking worden alleen de directe herstel punten verwijderd, niet de gegevens waarvan een back-up is gemaakt in de kluis.

   ```azurecli-interactive
   az resource delete --ids $RESTOREPOINTCOL
   ```

## <a name="next-steps"></a>Volgende stappen

* Zie [resources verplaatsen naar een nieuwe resource groep of een nieuw abonnement](../move-resource-group-and-subscription.md)voor opdrachten voor het verplaatsen van resources.

* Zie [Recovery Services beperkingen](../../../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json)voor informatie over het verplaatsen van Recovery Services-kluizen voor back-ups.
