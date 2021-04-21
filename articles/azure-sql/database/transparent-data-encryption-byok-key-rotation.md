---
title: TDE-beveiliging roteren (PowerShell & Azure CLI)
titleSuffix: Azure SQL Database & Azure Synapse Analytics
description: Leer hoe u de Transparent Data Encryption beveiliging (TDE) roteert voor een server in Azure die wordt gebruikt door Azure SQL Database en Azure Synapse Analytics met behulp van PowerShell en de Azure CLI.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: seo-lt-2019 sqldbrb=1, devx-track-azurecli
ms.devlang: ''
ms.topic: how-to
author: shohamMSFT
ms.author: shohamd
ms.reviewer: vanto
ms.date: 03/12/2019
ms.openlocfilehash: 67bcd8597314530f26481ef840644ffbc056b033
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107777559"
---
# <a name="rotate-the-transparent-data-encryption-tde-protector"></a>De Transparent Data Encryption (TDE) draaien
[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]


In dit artikel wordt sleutelrotatie voor een [server beschreven met](logical-servers.md) behulp van een TDE-beveiliging van Azure Key Vault. Het roteren van de logische TDE-beveiliging voor een server betekent overschakelen naar een nieuwe asymmetrische sleutel die de databases op de server beveiligt. Sleutelrotatie is een onlinebewerking die slechts enkele seconden duurt, omdat hiermee alleen de gegevensversleutelingssleutel van de database wordt ontsleuteld en opnieuw wordt versleuteld, niet de hele database.

In deze handleiding worden twee opties besproken voor het roteren van de TDE-beveiliging op de server.

> [!NOTE]
> Een onderbroken toegewezen SQL-pool in Azure Synapse Analytics moet worden hervat vóór sleutelrotaties.

> [!IMPORTANT]
> Verwijder geen eerdere versies van de sleutel na een rollover. Wanneer sleutels worden overgedraaid, worden sommige gegevens nog steeds versleuteld met de vorige sleutels, zoals oudere databaseback-ups.

## <a name="prerequisites"></a>Vereisten

- In deze handleiding wordt ervan uitgenomen dat u al een sleutel van Azure Key Vault gebruikt als TDE-beveiliging voor Azure SQL Database of Azure Synapse Analytics. Zie [Transparent Data Encryption met BYOK-ondersteuning](transparent-data-encryption-byok-overview.md).
- U moet Azure PowerShell geïnstalleerd en uitgevoerd.
- [Aanbevolen maar optioneel] Maak eerst het sleutelmateriaal voor de TDE-beveiliging in een HSM (Hardware Security Module) of lokaal sleutelopslag en importeer het sleutelmateriaal in Azure Key Vault. Volg de [instructies voor het gebruik van een HSM (Hardware Security Module) en](../../key-vault/general/overview.md) Key Vault meer informatie.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Raadpleeg [Azure PowerShell installeren](/powershell/azure/install-az-ps) voor instructies over de installatie van de Az-module. Zie [AzureRM.Sql](/powershell/module/AzureRM.Sql/)voor specifieke cmdlets.

> [!IMPORTANT]
> De module PowerShell Azure Resource Manager (RM) wordt nog steeds ondersteund, maar alle toekomstige ontwikkeling is voor de Az.Sql-module. De AzureRM-module blijft tot ten minste december 2020 bugfixes ontvangen.  De argumenten voor de opdrachten in de Az-module en in de AzureRm-modules zijn vrijwel identiek. Zie [Introductie van de nieuwe Az-module van Azure PowerShell](/powershell/azure/new-azureps-module-az) voor meer informatie over de compatibiliteit van de argumenten.

# <a name="the-azure-cli"></a>[De Azure CLI](#tab/azure-cli)

Zie Azure CLI installeren [voor de installatie.](/cli/azure/install-azure-cli)

* * *

## <a name="manual-key-rotation"></a>Handmatige sleutelrotatie

Handmatige sleutelrotatie maakt gebruik van de volgende opdrachten om een volledig nieuwe sleutel toe te voegen, die onder een nieuwe sleutelnaam of zelfs een andere sleutelkluis kan vallen. Het gebruik van deze methode ondersteunt het toevoegen van dezelfde sleutel aan verschillende sleutelkluizen ter ondersteuning van scenario's met hoge beschikbaarheid en geo-dr.

> [!NOTE]
> De gecombineerde lengte voor de naam en sleutelnaam van de sleutelkluis mag niet langer zijn dan 94 tekens.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Gebruik de cmdlets [Add-AzKeyVaultKey,](/powershell/module/az.keyvault/Add-AzKeyVaultKey) [Add-AzSqlServerKeyVaultKey](/powershell/module/az.sql/add-azsqlserverkeyvaultkey)en [Set-AzSqlServerTransparentDataEncryptionProtector.](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector)

```powershell
# add a new key to Key Vault
Add-AzKeyVaultKey -VaultName <keyVaultName> -Name <keyVaultKeyName> -Destination <hardwareOrSoftware>

# add the new key from Key Vault to the server
Add-AzSqlServerKeyVaultKey -KeyId <keyVaultKeyId> -ServerName <logicalServerName> -ResourceGroup <SQLDatabaseResourceGroupName>
  
# set the key as the TDE protector for all resources under the server
Set-AzSqlServerTransparentDataEncryptionProtector -Type AzureKeyVault -KeyId <keyVaultKeyId> `
   -ServerName <logicalServerName> -ResourceGroup <SQLDatabaseResourceGroupName>
```

# <a name="the-azure-cli"></a>[De Azure CLI](#tab/azure-cli)

Gebruik de [opdrachten az keyvault key create](/cli/azure/keyvault/key#az_keyvault_key_create), az sql server key [create](/cli/azure/sql/server/key#az_sql_server_key_create)en az sql [server tde-key set.](/cli/azure/sql/server/tde-key#az_sql_server_tde_key_set)

```azurecli
# add a new key to Key Vault
az keyvault key create --name <keyVaultKeyName> --vault-name <keyVaultName> --protection <hsmOrSoftware>

# add the new key from Key Vault to the server
az sql server key create --kid <keyVaultKeyId> --resource-group <SQLDatabaseResourceGroupName> --server <logicalServerName>

# set the key as the TDE protector for all resources under the server
az sql server tde-key set --server-key-type AzureKeyVault --kid <keyVaultKeyId> --resource-group <SQLDatabaseResourceGroupName> --server <logicalServerName>
```

* * *

## <a name="switch-tde-protector-mode"></a>TDE-beveiligingmodus wisselen

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

- Als u de TDE-beveiliging wilt overschakelen van de door Microsoft beheerde naar de BYOK-modus, gebruikt u de cmdlet [Set-AzSqlServerTransparentDataEncryptionProtector.](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector)

   ```powershell
   Set-AzSqlServerTransparentDataEncryptionProtector -Type AzureKeyVault `
       -KeyId <keyVaultKeyId> -ServerName <logicalServerName> -ResourceGroup <SQLDatabaseResourceGroupName>
   ```

- Als u de TDE-beveiliging wilt overschakelen van de BYOK-modus naar door Microsoft beheerd, gebruikt u de cmdlet [Set-AzSqlServerTransparentDataEncryptionProtector.](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector)

   ```powershell
   Set-AzSqlServerTransparentDataEncryptionProtector -Type ServiceManaged `
       -ServerName <logicalServerName> -ResourceGroup <SQLDatabaseResourceGroupName>
   ```

# <a name="the-azure-cli"></a>[De Azure CLI](#tab/azure-cli)

In de volgende voorbeelden wordt [az sql server tde-key set gebruikt.](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector)

- Als u de TDE-beveiliging wilt overschakelen van door Microsoft beheerde naar BYOK-modus,

   ```azurecli
   az sql server tde-key set --server-key-type AzureKeyVault --kid <keyVaultKeyId> --resource-group <SQLDatabaseResourceGroupName> --server <logicalServerName>
   ```

- Als u de TDE-beveiliging wilt overschakelen van de BYOK-modus naar een door Microsoft beheerde modus,

   ```azurecli
   az sql server tde-key set --server-key-type ServiceManaged --resource-group <SQLDatabaseResourceGroupName> --server <logicalServerName>
   ```

* * *

## <a name="next-steps"></a>Volgende stappen

- In het geval van een beveiligingsrisico leert u hoe u een mogelijk aangetaste TDE-beveiliging verwijdert: Een mogelijk [aangetaste sleutel verwijderen.](transparent-data-encryption-byok-remove-tde-protector.md)

- Aan de slag Azure Key Vault integratie en Bring Your Own Key ondersteuning voor [TDE: schakel TDE](transparent-data-encryption-byok-configure.md)in met uw eigen sleutel van Key Vault met behulp van PowerShell.