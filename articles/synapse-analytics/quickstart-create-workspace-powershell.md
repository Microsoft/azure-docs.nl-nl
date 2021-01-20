---
title: 'Quickstart: Een Synapse-werkruimte maken met Azure PowerShell'
description: Maak een Azure Synapse-werkruimte met behulp van Azure PowerShell door de stappen in deze handleiding te volgen.
services: synapse-analytics
author: alehall
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: overview
ms.date: 10/19/2020
ms.author: alehall
ms.reviewer: jrasnick
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 0537d2353d6b372ed19127101c488b872bbc5224
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/14/2021
ms.locfileid: "98218855"
---
# <a name="quickstart-create-an-azure-synapse-workspace-with-azure-powershell"></a>Quickstart: Een Azure Synapse-werkruimte maken met Azure PowerShell

Azure PowerShell is een set cmdlets om Azure-resources rechtstreeks vanuit PowerShell te beheren. U kunt deze gebruiken in uw browser met Azure Cloud Shell. U kunt deze ook installeren op macOS, Linux of Windows.

In deze quickstart leert u hoe u een Synapse-werkruimte maakt met behulp van Azure PowerShell.

Als u nog geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

- [Azure Data Lake Storage Gen2-opslagaccount](../storage/common/storage-account-create.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)

    > [!IMPORTANT]
    > De Azure Synapse-werkruimte moet kunnen lezen uit en schrijven naar het geselecteerde ADLS Gen2-account. Voor elk opslagaccount dat u als primair opslagaccount koppelt moet u **hiërarchische naamruimte** inschakelen bij het maken van het opslagaccount, zoals beschreven in [Een opslagaccount maken](../storage/common/storage-account-create.md?tabs=azure-powershell#create-a-storage-account).

Als u Cloud Shell gebruikt, raadpleegt u [Overzicht van Azure Cloud Shell](../cloud-shell/overview.md) voor meer informatie.

### <a name="install-the-azure-powershell-module-locally"></a>Installeer de Azure PowerShell-module lokaal

Als u PowerShell lokaal wilt gebruiken, moet u voor dit artikel de Az-module van PowerShell installeren en verbinding maken met uw Azure-account met behulp van de cmdlet [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount). Zie [Azure PowerShell installeren](/powershell/azure/install-az-ps) voor meer informatie over het installeren van de Az-module van PowerShell.

Zie [Aanmelden met Azure PowerShell](/powershell/azure/authenticate-azureps) voor meer informatie over verificatie met Azure PowerShell.

### <a name="install-the-azure-synapse-powershell-module"></a>De Azure Synapse PowerShell-module installeren

> [!IMPORTANT]
> Zolang de PowerShell-module **Az.Synapse** in de preview-fase is, moet u deze afzonderlijk installeren met behulp van de cmdlet `Install-Module`. Nadat de PowerShell-module algemeen beschikbaar is geworden, wordt deze onderdeel van toekomstige releases van de Az PowerShell-module en is deze standaard beschikbaar vanuit Azure Cloud Shell.

```azurepowershell-interactive
Install-Module -Name Az.Synapse
```

## <a name="create-an-azure-synapse-workspace-using-azure-powershell"></a>Een Azure Synapse-werkruimte maken met behulp van Azure PowerShell

1. Definieer de benodigde omgevingsvariabelen om resources voor de Azure Synapse-ruimte te maken.

   |        Naam van de variabele        |                                                 Beschrijving                                                 |
   | --------------------------- | ----------------------------------------------------------------------------------------------------------- |
   | StorageAccountName          | Naam van uw bestaande ADLS Gen2-opslagaccount.                                                           |
   | StorageAccountResourceGroup | Naam van de resourcegroep van uw bestaande ADLS Gen2-opslagaccount.                                             |
   | FileShareName               | Naam van uw bestaande opslagbestandssysteem.                                                                  |
   | SynapseResourceGroup        | Kies een nieuwe naam voor uw Azure Synapse-resourcegroep.                                                    |
   | Region                      | Kies een van de [Azure-regio’s](https://azure.microsoft.com/global-infrastructure/geographies/#overview). |
   | SynapseWorkspaceName        | Kies een unieke naam voor uw nieuwe Azure Synapse-werkruimte.                                                  |
   | SqlUser                     | Kies een waarde voor een nieuwe gebruikersnaam.                                                                          |
   | SqlPassword                 | Kies een veilig wachtwoord.                                                                                   |
   | ClientIP                    | Het openbare IP-adres van het systeem waarop PowerShell wordt uitgevoerd.                                             |
   |                             |                                                                                                             |

1. Maak een resourcegroep als container voor uw Azure Synapse-werkruimte:

   ```azurepowershell-interactive
   New-AzResourceGroup -Name $SynapseResourceGroup -Location $Region
   ```

1. Haal de sleutel van het ADLS Gen2-opslagaccount op:

   ```azurepowershell-interactive
   $StorageAccountKey = Get-AzStorageAccountKey -ResourceGroupName $StorageAccountResourceGroup -Name $StorageAccountName |
     Select-Object -First 1 -ExpandProperty Value
    ```

1. Haal de eindpunt-URL van het ADLS Gen2-opslagaccount op:

   ```azurepowershell-interactive
   $StorageEndpointUrl = (Get-AzStorageAccount -ResourceGroupName $StorageAccountResourceGroup -Name $StorageAccountName).PrimaryEndpoints.Dfs
   ```

1. (Optioneel) U kunt altijd controleren wat de sleutel en eindpunt-URL van uw ADLS Gen2-opslagaccount zijn:

   ```azurepowershell-interactive
   Write-Output "Storage Account Key: $StorageAccountKey"
   Write-Output "Storage Endpoint URL: $StorageEndpointUrl"
   ```

1. Maak een Azure Synapse-werkruimte:

   ```azurepowershell-interactive
   $Cred = New-Object -TypeName System.Management.Automation.PSCredential ($SqlUser, (ConvertTo-SecureString $SqlPassword -AsPlainText -Force))

   $WorkspaceParams = @{
     Name = $SynapseWorkspaceName
     ResourceGroupName = $SynapseResourceGroup
     DefaultDataLakeStorageAccountName = $StorageAccountName
     DefaultDataLakeStorageFilesystem = $FileShareName
     SqlAdministratorLoginCredential = $Cred
     Location = $Region
   }
   New-AzSynapseWorkspace @WorkspaceParams
   ```

1. Haal de Web-URL en Dev-URL van de Azure Synapse-werkruimte op:

   ```azurepowershell-interactive
   $WorkspaceWeb = (Get-AzSynapseWorkspace -Name $SynapseWorkspaceName -ResourceGroupName $StorageAccountResourceGroup).ConnectivityEndpoints.Web
   $WorkspaceDev = (Get-AzSynapseWorkspace -Name $SynapseWorkspaceName -ResourceGroupName $StorageAccountResourceGroup).ConnectivityEndpoints.Dev
   ```

1. Maak een firewallregel om vanaf uw computer toegang te krijgen tot de Azure Synapse-werkruimte:

   ```azurepowershell-interactive
   $FirewallParams = @{
     WorkspaceName = $SynapseWorkspaceName
     Name = 'Allow Client IP'
     ResourceGroupName = $StorageAccountResourceGroup
     StartIpAddress = $ClientIP
     EndIpAddress = $ClientIP
   }
   New-AzSynapseFirewallRule @FirewallParams
   ```

1. Open de Web-URL van de Azure Synapse-werkruimte, die in de omgevingsvariabele `WorkspaceWeb` is opgeslagen, om toegang te krijgen tot uw werkruimte:

   ```azurepowershell-interactive
   Start-Process $WorkspaceWeb
   ```

   ![Web-URL van Azure Synapse-werkruimte](media/quickstart-create-synapse-workspace-powershell/create-workspace-powershell-1.png)

## <a name="clean-up-resources"></a>Resources opschonen

Voer de onderstaande stappen uit om de Azure Synapse-werkruimte te verwijderen.

> [!WARNING]
> Als u een Azure Synapse-werkruimte verwijdert, worden de analyse-engines en de gegevens die zijn opgeslagen in de database van de SQL-pools die het bevat en de metagegevens van de werkruimte verwijderd. Er kan geen verbinding meer worden gemaakt met het SQL- of Apache Spark-eindpunt. Alle codeartefacten worden verwijderd (query's, notebooks, taakdefinities en pijplijnen). Als u de werkruimte verwijdert, heeft dit **geen** invloed op de gegevens in het Data Lake Storage Gen2-account dat aan de werkruimte is gekoppeld.

Als de Azure Synapse-werkruimte die u in dit artikel hebt gemaakt, niet nodig is, kunt u deze verwijderen door het volgende voorbeeld uit te voeren.

```azurepowershell-interactive
Remove-AzSynapseWorkspace -Name $SynapseWorkspaceNam -ResourceGroupName $SynapseResourceGroup
```

## <a name="next-steps"></a>Volgende stappen

Vervolgens kunt u [SQL-pools maken](quickstart-create-sql-pool-studio.md) of [Apache Spark-pools maken](quickstart-create-apache-spark-pool-studio.md) zodat u uw gegevens kunt gaan analyseren en verkennen.