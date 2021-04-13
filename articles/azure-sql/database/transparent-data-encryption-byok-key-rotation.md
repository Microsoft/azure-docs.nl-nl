---
title: TDE-beveiliging (Power shell & de Azure CLI) draaien
titleSuffix: Azure SQL Database & Azure Synapse Analytics
description: Meer informatie over het roteren van de Transparent Data Encryption-Protector (TDE) voor een server in azure die wordt gebruikt door Azure SQL Database en Azure Synapse Analytics met behulp van Power shell en de Azure CLI.
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
ms.openlocfilehash: 4bcfc0c7e24c83f5388e49af397d66f2bad7aa9f
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107310934"
---
# <a name="rotate-the-transparent-data-encryption-tde-protector"></a>De Transparent Data Encryption-Protector (TDE) draaien
[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]


In dit artikel wordt de belangrijkste draaiing beschreven voor een [Server](logical-servers.md) met behulp van een TDe-protector van Azure Key Vault. Als u de logische TDE-Protector voor een server roteert, wordt overgeschakeld naar een nieuwe asymmetrische sleutel die de data bases op de server beveiligt. Het draaien van sleutels is een online bewerking die slechts enkele seconden kan duren, omdat hiermee alleen de gegevens versleutelings sleutel van de data base wordt gedecodeerd en opnieuw versleuteld, niet de volledige data base.

In deze hand leiding worden twee opties besproken voor het draaien van de TDE-protector op de-server.

> [!NOTE]
> Een onderbroken, toegewezen SQL-groep in azure Synapse Analytics moet worden hervat voordat de sleutel wordt gedraaid.

> [!IMPORTANT]
> Verwijder geen eerdere versies van de sleutel na een rollover. Wanneer sleutels worden doorgevoerd, worden er nog enkele gegevens versleuteld met de vorige sleutels, zoals oudere back-ups van de data base.

## <a name="prerequisites"></a>Vereisten

- In deze hand leiding wordt ervan uitgegaan dat u al een sleutel gebruikt uit Azure Key Vault als de TDE-Protector voor Azure SQL Database of Azure Synapse Analytics. Zie [transparent Data Encryption met BYOK-ondersteuning](transparent-data-encryption-byok-overview.md).
- Azure PowerShell moet zijn geïnstalleerd en worden uitgevoerd.
- [Aanbevolen, maar optioneel] Maak eerst het sleutel materiaal voor de TDE-Protector in een HSM (Hardware Security module) of een lokaal sleutel archief en importeer het sleutel materiaal in Azure Key Vault. Volg de [instructies voor het gebruik van een Hardware Security module (hsm) en Key Vault](../../key-vault/general/overview.md) voor meer informatie.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Raadpleeg [Azure PowerShell installeren](/powershell/azure/install-az-ps) voor instructies over de installatie van de Az-module. Zie [AzureRM. SQL](/powershell/module/AzureRM.Sql/)voor specifieke cmdlets.

> [!IMPORTANT]
> De module PowerShell Azure Resource Manager (RM) wordt nog steeds ondersteund, maar alle toekomstige ontwikkeling is voor de Az.Sql-module. De AzureRM-module blijft tot ten minste december 2020 bugfixes ontvangen.  De argumenten voor de opdrachten in de Az-module en in de AzureRm-modules zijn vrijwel identiek. Zie [Introductie van de nieuwe Az-module van Azure PowerShell](/powershell/azure/new-azureps-module-az) voor meer informatie over de compatibiliteit van de argumenten.

# <a name="the-azure-cli"></a>[De Azure CLI](#tab/azure-cli)

Zie [de Azure cli installeren](/cli/azure/install-azure-cli)voor installatie.

* * *

## <a name="manual-key-rotation"></a>Hand matige rotatie van de sleutel

Voor hand matige rotatie van sleutels worden de volgende opdrachten gebruikt om een volledig nieuwe sleutel toe te voegen, die onder een nieuwe sleutel naam kan staan of zelfs een andere sleutel kluis. Het gebruik van deze aanpak biedt ondersteuning voor het toevoegen van dezelfde sleutel aan verschillende sleutel kluizen ter ondersteuning van scenario's met hoge Beschik baarheid en geo-Dr.

> [!NOTE]
> De gecombineerde lengte van de naam van de sleutel kluis en de naam van de sleutel mag niet langer zijn dan 94 tekens.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Gebruik de cmdlets [add-AzKeyVaultKey](/powershell/module/az.keyvault/Add-AzKeyVaultKey), [add-AzSqlServerKeyVaultKey](/powershell/module/az.sql/add-azsqlserverkeyvaultkey)en [set-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) .

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

Gebruik de opdracht [AZ sleutel kluis Create](/cli/azure/keyvault/key#az-keyvault-key-create), [AZ SQL Server key Create](/cli/azure/sql/server/key#az-sql-server-key-create)en [AZ SQL Server TDe-key set](/cli/azure/sql/server/tde-key#az-sql-server-tde-key-set) .

```azurecli
# add a new key to Key Vault
az keyvault key create --name <keyVaultKeyName> --vault-name <keyVaultName> --protection <hsmOrSoftware>

# add the new key from Key Vault to the server
az sql server key create --kid <keyVaultKeyId> --resource-group <SQLDatabaseResourceGroupName> --server <logicalServerName>

# set the key as the TDE protector for all resources under the server
az sql server tde-key set --server-key-type AzureKeyVault --kid <keyVaultKeyId> --resource-group <SQLDatabaseResourceGroupName> --server <logicalServerName>
```

* * *

## <a name="switch-tde-protector-mode"></a>TDE Protector modus wijzigen

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

- Gebruik de cmdlet [set-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) om de TDe-Protector te scha kelen van micro soft die wordt beheerd naar de BYOK-modus.

   ```powershell
   Set-AzSqlServerTransparentDataEncryptionProtector -Type AzureKeyVault `
       -KeyId <keyVaultKeyId> -ServerName <logicalServerName> -ResourceGroup <SQLDatabaseResourceGroupName>
   ```

- Als u de TDE-Protector wilt overschakelen van de BYOK-modus naar micro soft-beheerd, gebruikt u de cmdlet [set-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) .

   ```powershell
   Set-AzSqlServerTransparentDataEncryptionProtector -Type ServiceManaged `
       -ServerName <logicalServerName> -ResourceGroup <SQLDatabaseResourceGroupName>
   ```

# <a name="the-azure-cli"></a>[De Azure CLI](#tab/azure-cli)

De volgende voor beelden gebruiken [AZ SQL Server TDe-key set](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector).

- Als u wilt overschakelen van de TDE-Protector van micro soft-beheerd naar de BYOK-modus,

   ```azurecli
   az sql server tde-key set --server-key-type AzureKeyVault --kid <keyVaultKeyId> --resource-group <SQLDatabaseResourceGroupName> --server <logicalServerName>
   ```

- Als u de TDE-Protector wilt overschakelen van de BYOK-modus naar door micro soft beheerd,

   ```azurecli
   az sql server tde-key set --server-key-type ServiceManaged --resource-group <SQLDatabaseResourceGroupName> --server <logicalServerName>
   ```

* * *

## <a name="next-steps"></a>Volgende stappen

- In het geval van een beveiligings risico leert u hoe u een mogelijk aangetast TDE-Protector kunt verwijderen: [Verwijder een mogelijk versleutelde code](transparent-data-encryption-byok-remove-tde-protector.md).

- Aan de slag met Azure Key Vault-integratie en Bring Your Own Key ondersteuning voor TDE: [Schakel TDe met behulp van uw eigen sleutel in Key Vault met behulp van Power shell](transparent-data-encryption-byok-configure.md).