---
title: 'PowerShell-script: kluis voor opslagaccount vinden'
description: Lees hoe u met een Azure PowerShell-script de Recovery Services-kluis kunt vinden waarbij uw opslagaccount is geregistreerd.
ms.topic: sample
ms.date: 1/28/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 40859c1ea05210d27fcdcf33ba9d4f961965ea22
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "89075695"
---
# <a name="powershell-script-to-find-the-recovery-services-vault-where-a-storage-account-is-registered"></a>PowerShell-script voor het vinden van de Recovery Services-kluis waarbij uw opslagaccount is geregistreerd

Dit script helpt om de Recovery Services-kluis te vinden waarbij uw opslagaccount is geregistreerd.

## <a name="sample-script"></a>Voorbeeldscript

```powershell
Param(
        [Parameter(Mandatory=$True)][System.String] $ResourceGroupName,
        [Parameter(Mandatory=$True)][System.String] $StorageAccountName,
        [Parameter(Mandatory=$True)][System.String] $SubscriptionId
    )

Connect-AzAccount
Select-AzSubscription -Subscription $SubscriptionId
$vaults = Get-AzRecoveryServicesVault
$found = $false
foreach($vault in $vaults)
{
  Write-Verbose "Checking vault: $($vault.Id)" -Verbose
  
  $containers = Get-AzRecoveryServicesBackupContainer -ContainerType AzureStorage -FriendlyName $StorageAccountName -ResourceGroupName $ResourceGroupName -VaultId $vault.Id -Status Registered
  
  if($containers -ne $null)
  {
    $found = $True
    Write-Information "Found Storage account $StorageAccountName registered in vault: $($vault.Id)" -InformationAction Continue
    break;
  }
}

if(!$found)
{
     Write-Information "Storage account: $StorageAccountName is not registered in any vault of this subscription" -InformationAction Continue
}
```

## <a name="how-to-execute-the-script"></a>Het script uitvoeren

1. Sla het bovenstaande script op de computer op met een zelfgekozen naam. In dit voorbeeld hebben we het script opgeslagen als *FindRegisteredStorageAccount.ps1*.
2. Voer het script uit door de volgende parameters op te geven:

    * **-ResourceGroupName**: de resourcegroep van het opslagaccount
    * **-StorageAccountName**: de naam van opslagaccount
    * **-SubscriptionID**: de id van het abonnement waarin het opslagaccount zich bevindt

In het volgende voorbeeld wordt gezocht naar de Recovery Services-kluis waarbij het opslagaccount *afsaccount* is geregistreerd:

```powershell
.\FindRegisteredStorageAccount.ps1 -ResourceGroupName AzureFiles -StorageAccountName afsaccount -SubscriptionId ef4ad5a7-c2c0-4304-af80-af49f49af3d1
```

## <a name="output"></a>Uitvoer

De uitvoer bestaat uit het volledige pad van de Recovery Services-kluis waarbij het opslagaccount is geregistreerd. Hier volgt een voorbeeld van uitvoer:

```output
Found Storage account afsaccount registered in vault: /subscriptions/ ef4ad5a7-c2c0-4304-af80-af49f49af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault123
```

## <a name="next-steps"></a>Volgende stappen

Lees meer over het [maken van back-ups van Azure-bestandsshares via de Azure-portal](../backup-afs.md).
