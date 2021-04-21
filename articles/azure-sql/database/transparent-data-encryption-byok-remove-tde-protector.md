---
title: TDE-beveiliging verwijderen (PowerShell & Azure CLI)
titleSuffix: Azure SQL Database & Azure Synapse Analytics
description: Informatie over hoe u reageert op een mogelijk aangetaste TDE-beveiliging voor Azure SQL Database of Azure Synapse Analytics met behulp van TDE met Bring Your Own Key (BYOK)-ondersteuning.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: seo-lt-2019 sqldbrb=1, devx-track-azurecli
ms.devlang: ''
ms.topic: how-to
author: shohamMSFT
ms.author: shohamd
ms.reviewer: vanto
ms.date: 02/24/2020
ms.openlocfilehash: f98dcdd9c1a479703c82c01b4fd240507ea355de
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107784457"
---
# <a name="remove-a-transparent-data-encryption-tde-protector-using-powershell"></a>Een TDE Transparent Data Encryption beveiliging verwijderen met behulp van PowerShell
[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]


In dit onderwerp wordt beschreven hoe u reageert op een mogelijk aangetaste TDE-bescherming voor Azure SQL Database of Azure Synapse Analytics die TDE gebruikt met door de klant beheerde sleutels in Azure Key Vault- Bring Your Own Key(BYOK)-ondersteuning. Zie de overzichtspagina voor meer informatie over BYOK-ondersteuning [voor](transparent-data-encryption-byok-overview.md)TDE.

> [!CAUTION]
> De procedures die in dit artikel worden beschreven, mogen alleen worden uitgevoerd in extreme gevallen of in testomgevingen. Controleer de stappen zorgvuldig, omdat het verwijderen van actief gebruikte TDE-beveiligingen Azure Key Vault ertoe leidt **dat de database niet meer beschikbaar is.**

Als er ooit wordt vermoed dat een sleutel is aangetast, zodat een service of gebruiker onbevoegde toegang tot de sleutel heeft gehad, kunt u de sleutel het beste verwijderen.

Houd er rekening mee dat zodra de TDE-beveiliging in Key Vault is verwijderd, binnen tien minuten alle versleutelde databases alle verbindingen met [](./transparent-data-encryption-byok-overview.md#inaccessible-tde-protector)het bijbehorende foutbericht weigeren en de status ervan wijzigen in Niet-toegankelijk.

Deze handleiding gaat over twee benaderingen, afhankelijk van het gewenste resultaat na een incidentrespons die is aangetast:

- Als u de databases in Azure SQL Database/Azure Synapse Analytics **ontoegankelijk wilt maken.**
- Als u de databases in Azure SQL Database/Azure Azure Synapse Analytics **ontoegankelijk wilt maken.**

## <a name="prerequisites"></a>Vereisten

- U moet een Azure-abonnement hebben en een beheerder van dat abonnement zijn
- U moet Azure PowerShell geïnstalleerd en uitgevoerd.
- In deze handleiding wordt ervan uitgenomen dat u al een sleutel van Azure Key Vault gebruikt als TDE-beveiliging voor een Azure SQL Database of Azure Synapse. Zie [Transparent Data Encryption met BYOK-ondersteuning voor](transparent-data-encryption-byok-overview.md) meer informatie.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

 Raadpleeg [Azure PowerShell installeren](/powershell/azure/install-az-ps) voor instructies over de installatie van de Az-module. Zie [AzureRM.Sql](/powershell/module/AzureRM.Sql/)voor specifieke cmdlets.

> [!IMPORTANT]
> De Module PowerShell Azure Resource Manager (RM) wordt nog steeds ondersteund, maar alle toekomstige ontwikkeling is voor de Az.Sql-module. De AzureRM-module blijft tot ten minste december 2020 bugfixes ontvangen.  De argumenten voor de opdrachten in de Az-module en in de AzureRm-modules zijn vrijwel identiek. Zie [Introductie van de nieuwe Az-module van Azure PowerShell](/powershell/azure/new-azureps-module-az) voor meer informatie over de compatibiliteit van de argumenten.

# <a name="the-azure-cli"></a>[De Azure CLI](#tab/azure-cli)

Zie Azure CLI installeren [voor de installatie.](/cli/azure/install-azure-cli)

* * *

## <a name="check-tde-protector-thumbprints"></a>Vingerafdruk van TDE-beveiliging controleren

In de volgende stappen wordt beschreven hoe u de vingerafdruk van de TDE-beveiliging controleert die nog steeds wordt gebruikt door Virtual Log Files (VLF) van een bepaalde database.
De vingerafdruk van de huidige TDE-beveiliging van de database en de database-id kunt u vinden door het volgende uit te uitvoeren:

```sql
SELECT [database_id],
       [encryption_state],
       [encryptor_type], /*asymmetric key means AKV, certificate means service-managed keys*/
       [encryptor_thumbprint]
 FROM [sys].[dm_database_encryption_keys]
```

De volgende query retourneert de VLFs en de respectievelijke TDE-beveiliging vingerafdruk in gebruik. Elke verschillende vingerafdruk verwijst naar een andere sleutel in Azure Key Vault (AKV):

```sql
SELECT * FROM sys.dm_db_log_info (database_id)
```

U kunt ook PowerShell of de Azure CLI gebruiken:

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

De **PowerShell-opdracht Get-AzureRmSqlServerKeyVaultKey** biedt de vingerafdruk van de TDE-beveiliging die in de query wordt gebruikt, zodat u kunt zien welke sleutels u wilt behouden en welke sleutels u wilt verwijderen   in AKV. Alleen sleutels die niet meer door de database worden gebruikt, kunnen veilig worden verwijderd uit Azure Key Vault.

# <a name="the-azure-cli"></a>[De Azure CLI](#tab/azure-cli)

De PowerShell-opdracht **az sql server key show** bevat de vingerafdruk van de TDE-beveiliging die in de query wordt gebruikt, zodat u kunt zien welke sleutels u wilt behouden en welke sleutels u   wilt verwijderen in AKV. Alleen sleutels die niet meer door de database worden gebruikt, kunnen veilig worden verwijderd uit Azure Key Vault.

* * *

## <a name="keep-encrypted-resources-accessible"></a>Versleutelde resources toegankelijk houden

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Maak een [nieuwe sleutel in Key Vault](/powershell/module/az.keyvault/add-azkeyvaultkey). Zorg ervoor dat deze nieuwe sleutel is gemaakt in een afzonderlijke sleutelkluis van de mogelijk aangetaste TDE-beveiliging, omdat toegangsbeheer is ingericht op kluisniveau.

2. Voeg de nieuwe sleutel toe aan de server met behulp van de cmdlets [Add-AzSqlServerKeyVaultKey](/powershell/module/az.sql/add-azsqlserverkeyvaultkey) en [Set-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) en werk deze bij als de nieuwe TDE-beveiliging van de server.

   ```powershell
   # add the key from Key Vault to the server  
   Add-AzSqlServerKeyVaultKey -ResourceGroupName <SQLDatabaseResourceGroupName> -ServerName <LogicalServerName> -KeyId <KeyVaultKeyId>

   # set the key as the TDE protector for all resources under the server
   Set-AzSqlServerTransparentDataEncryptionProtector -ResourceGroupName <SQLDatabaseResourceGroupName> `
       -ServerName <LogicalServerName> -Type AzureKeyVault -KeyId <KeyVaultKeyId>
   ```

3. Zorg ervoor dat de server en eventuele replica's zijn bijgewerkt naar de nieuwe TDE-beveiliging met behulp van de cmdlet [Get-AzSqlServerTransparentDataEncryptionProtector.](/powershell/module/az.sql/get-azsqlservertransparentdataencryptionprotector)

   > [!NOTE]
   > Het kan enkele minuten duren voordat de nieuwe TDE-beveiliging is doorgegeven aan alle databases en secundaire databases onder de server.

   ```powershell
   Get-AzSqlServerTransparentDataEncryptionProtector -ServerName <LogicalServerName> -ResourceGroupName <SQLDatabaseResourceGroupName>
   ```

4. Maak een [back-up van de nieuwe](/powershell/module/az.keyvault/backup-azkeyvaultkey) sleutel in Key Vault.

   ```powershell
   # -OutputFile parameter is optional; if removed, a file name is automatically generated.
   Backup-AzKeyVaultKey -VaultName <KeyVaultName> -Name <KeyVaultKeyName> -OutputFile <DesiredBackupFilePath>
   ```

5. Verwijder de aangetaste sleutel uit Key Vault cmdlet [Remove-AzKeyVaultKey.](/powershell/module/az.keyvault/remove-azkeyvaultkey)

   ```powershell
   Remove-AzKeyVaultKey -VaultName <KeyVaultName> -Name <KeyVaultKeyName>
   ```

6. Als u een sleutel wilt herstellen naar Key Vault in de toekomst met behulp van de cmdlet [Restore-AzKeyVaultKey:](/powershell/module/az.keyvault/restore-azkeyvaultkey)

   ```powershell
   Restore-AzKeyVaultKey -VaultName <KeyVaultName> -InputFile <BackupFilePath>
   ```

# <a name="the-azure-cli"></a>[De Azure CLI](#tab/azure-cli)

Zie azure CLI [keyvault voor naslag voor opdrachten.](/cli/azure/keyvault/key)

1. Maak een [nieuwe sleutel in Key Vault](/cli/azure/keyvault/key#az_keyvault_key_create). Zorg ervoor dat deze nieuwe sleutel is gemaakt in een afzonderlijke sleutelkluis van de mogelijk aangetaste TDE-beveiliging, omdat toegangsbeheer is ingericht op kluisniveau.

2. Voeg de nieuwe sleutel toe aan de server en werk deze bij als de nieuwe TDE-beveiliging van de server.

   ```azurecli
   # add the key from Key Vault to the server  
   az sql server key create --kid <KeyVaultKeyId> --resource-group <SQLDatabaseResourceGroupName> --server <LogicalServerName>

   # set the key as the TDE protector for all resources under the server
   az sql server tde-key set --server-key-type AzureKeyVault --kid <KeyVaultKeyId> --resource-group <SQLDatabaseResourceGroupName> --server <LogicalServerName>
   ```

3. Zorg ervoor dat de server en eventuele replica's zijn bijgewerkt naar de nieuwe TDE-beveiliging.

   > [!NOTE]
   > Het kan enkele minuten duren voordat de nieuwe TDE-beveiliging is doorgegeven aan alle databases en secundaire databases onder de server.

   ```azurecli
   az sql server tde-key show --resource-group <SQLDatabaseResourceGroupName> --server <LogicalServerName>
   ```

4. Maak een back-up van de nieuwe sleutel in Key Vault.

   ```azurecli
   # --file parameter is optional; if removed, a file name is automatically generated.
   az keyvault key backup --file <DesiredBackupFilePath> --name <KeyVaultKeyName> --vault-name <KeyVaultName>
   ```

5. Verwijder de aangetaste sleutel uit Key Vault.

   ```azurecli
   az keyvault key delete --name <KeyVaultKeyName> --vault-name <KeyVaultName>
   ```

6. Als u een sleutel wilt herstellen Key Vault in de toekomst.

   ```azurecli
   az keyvault key restore --file <BackupFilePath> --vault-name <KeyVaultName>
   ```

* * *

## <a name="make-encrypted-resources-inaccessible"></a>Versleutelde resources ontoegankelijk maken

1. De databases verwijderen die worden versleuteld door de mogelijk aangetaste sleutel.

   Er wordt automatisch een back-up van de database en logboekbestanden gemaakt, zodat een herstel naar een bepaald tijdstip van de database op elk moment kan worden uitgevoerd (zolang u de sleutel verstrekt). De databases moeten worden verwijderd voordat een actieve TDE-beveiliging wordt verwijderd om mogelijk gegevensverlies van maximaal 10 minuten van de meest recente transacties te voorkomen.

2. Back-up van het sleutelmateriaal van de TDE-beveiliging in Key Vault.
3. Verwijder de sleutel die mogelijk is aangetast uit Key Vault

[!INCLUDE [sql-database-akv-permission-delay](../includes/sql-database-akv-permission-delay.md)]

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het roteren van de TDE-beveiliging van een server om te voldoen aan de beveiligingsvereisten: De Transparent Data Encryption [draaien met behulp van PowerShell](transparent-data-encryption-byok-key-rotation.md)
- Aan de slag Bring Your Own Key ondersteuning voor TDE: TDE in uw eigen sleutel in Key Vault [met behulp van PowerShell](transparent-data-encryption-byok-configure.md)