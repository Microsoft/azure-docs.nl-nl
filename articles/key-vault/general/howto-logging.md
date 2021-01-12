---
title: Azure Key Vault logboek registratie inschakelen
description: Logboek registratie inschakelen voor Azure Key Vault, waarmee gegevens worden opgeslagen in een Azure Storage-account dat u opgeeft.
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 10/01/2020
ms.author: mbaldwin
ms.openlocfilehash: 0e1ce841f6da8f15bd977437bca6b835a7b0d745
ms.sourcegitcommit: 48e5379c373f8bd98bc6de439482248cd07ae883
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/12/2021
ms.locfileid: "98108735"
---
# <a name="how-to-enable-key-vault-logging"></a>Key Vault-logboekregistratie inschakelen

Nadat u een of meer sleutelkluizen hebt gemaakt, wilt u wellicht controleren hoe en wanneer uw sleutelkluizen toegankelijk zijn en voor wie. Zie [Key Vault logboek registratie](logging.md)voor volledige informatie over de functie.

## <a name="prerequisites"></a>Vereisten

Voor het voltooien van deze zelfstudie hebt u het volgende nodig:

* Een bestaande sleutelkluis die u hebt gebruikt.  
* [Azure Cloud shell](https://shell.azure.com) -bash omgeving
* Voldoende opslagruimte op Azure voor uw Sleutelkluis-logboeken.

Deze gids opdrachten zijn opgemaakt voor [Cloud shell](https://shell.azure.com) met bash als een omgeving.

## <a name="connect-to-your-key-vault-subscription"></a>Verbinding maken met uw Key Vault-abonnement

De eerste stap bij het instellen van sleutel registratie is het maken van verbinding met een abonnement met uw sleutel kluis. Dit is vooral belang rijk als u meerdere abonnementen hebt die aan uw account zijn gekoppeld.

Met de Azure CLI kunt u al uw abonnementen weer geven met de opdracht [AZ account list](/cli/azure/account?view=azure-cli-latest#az_account_list) en vervolgens verbinding maken met een van de accounts met behulp van [AZ account set](/cli/azure/account?view=azure-cli-latest#az_account_set):

```azurecli-interactive
az account list

az account set --subscription "<subscriptionID>"
```

Met Azure PowerShell kunt u uw abonnementen eerst weer geven met behulp van de cmdlet [Get-AzSubscription](/powershell/module/az.accounts/get-azsubscription?view=azps-4.7.0) en vervolgens verbinding maken met één met behulp van de cmdlet [set-AzContext](/powershell/module/az.accounts/set-azcontext?view=azps-4.7.0) : 

```powershell-interactive
Get-AzSubscription

Set-AzContext -SubscriptionId "<subscriptionID>"
```

## <a name="create-a-storage-account-for-your-logs"></a>Een opslagaccount voor uw logboeken maken

Hoewel u een bestaand opslag account voor uw logboeken kunt gebruiken, maken we een nieuw opslag account dat is toegewezen aan Key Vault Logboeken. 

En om het ons nog gemakkelijker te maken, gebruiken we de resourcegroep die de sleutelkluis bevat. In de [Snelstartgids van Azure cli](quick-create-cli.md) en [Azure PowerShell Quick](quick-create-powershell.md)start is deze resource groep de naam **myResourceGroup** en is de locatie *Oost*-out. Vervang deze waarden door uw eigen waarde, zoals van toepassing. 

U moet ook een naam voor het opslag account opgeven. Namen van opslag accounts moeten uniek zijn, tussen 3 en 24 tekens lang zijn en mogen alleen cijfers en kleine letters bevatten.  Tot slot wordt er een opslag account van de SKU ' Standard_LRS ' gemaakt.

Met de Azure CLI, gebruikt u de opdracht [AZ Storage account create](/cli/azure/storage/account?view=azure-cli-latest#az_storage_account_create) .

```azurecli-interactive
az storage account create --name "<your-unique-storage-account-name>" -g "myResourceGroup" --sku "Standard_LRS"
```

Gebruik met Azure PowerShell de cmdlet [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount?view=azps-4.7.0) . U moet de locatie opgeven die overeenkomt met de resource groep.

```powershell
 New-AzStorageAccount -ResourceGroupName myResourceGroup -Name "<your-unique-storage-account-name>" -Type "Standard_LRS" -Location "eastus"
```

In beide gevallen noteert u de id van het opslag account. Met de Azure CLI-bewerking wordt de ' id ' in de uitvoer geretourneerd. Als u de id wilt ophalen met Azure PowerShell, gebruikt u [Get-AzStorageAccount](/powershell/module/az.storage/get-azstorageaccount?view=azps-4.7.0) en wijst u de uitvoer toe aan een variabele $sa. Daarna kunt u het opslag account met $sa. id weer geven. (De $sa. De eigenschap context wordt ook gebruikt, verderop in dit artikel.)

```powershell-interactive
$sa = Get-AzStorageAccount -Name "<your-unique-storage-account-name>" -ResourceGroup "myResourceGroup"
$sa.id
```

De ' id ' van het opslag account heeft de indeling '/Subscriptions/<your-abonnement-ID>/resourceGroups/myResourceGroup/providers/Microsoft.Storage/storageAccounts/<uw-uniek-Storage-account name>.

> [!NOTE]
> Als u een bestaand opslag account wilt gebruiken, moet het gebruikmaken van hetzelfde abonnement als uw sleutel kluis en moet het Azure Resource Manager implementatie model worden gebruikt in plaats van het klassieke implementatie model.

## <a name="obtain-your-key-vault-resource-id"></a>De resource-ID van uw sleutel kluis ophalen

In de [cli-Snelstartgids](quick-create-cli.md) en [Power shell Quick](quick-create-powershell.md)start hebt u een sleutel met een unieke naam gemaakt.  Gebruik die naam opnieuw in de volgende stappen.  Als u de naam van uw sleutel kluis niet meer weet, kunt u de Azure CLI [AZ-lijst](/cli/azure/keyvault?view=azure-cli-latest#az_keyvault_list) opdracht van de hoofd kluis of de Azure PowerShell [Get-AzKeyVault-](/powershell/module/az.keyvault/get-azkeyvault?view=azps-4.7.0) cmdlet gebruiken om ze weer te geven.

Gebruik de naam van de sleutel kluis om de bijbehorende resource-ID te vinden.  Met Azure CLI gebruikt u de opdracht AZ-sleutel [kluis weer geven](/cli/azure/keyvault?view=azure-cli-latest#az_keyvault_show) .

```azurecli-interactive
az keyvault show --name "<your-unique-keyvault-name>"
```

Met Azure PowerShell gebruikt u de cmdlet [Get-AzKeyVault](/powershell/module/az.keyvault/get-azkeyvault?view=azps-4.7.0) .

```powershell-interactive
Get-AzKeyVault -VaultName "<your-unique-keyvault-name>"
```

De resource-ID voor uw sleutel kluis heeft de indeling '/Subscriptions/<your-abonnement-ID>/resourceGroups/myResourceGroup/providers/Microsoft.KeyVault/vaults/<your-unique-sleutel kluis-name>). Noteer het voor de volgende stap.

## <a name="enable-logging-using-azure-powershell"></a>Logboekregistratie inschakelen met Azure PowerShell

Als u logboek registratie voor Key Vault wilt inschakelen, gebruiken we de opdracht diagnostische gegevens van de Azure CLI [AZ monitor Diagnostic-Settings](/cli/azure/monitor/diagnostic-settings?view=azure-cli-latest) of de cmdlet [set-AzDiagnosticSetting](/powershell/module/az.monitor/set-azdiagnosticsetting?view=azps-4.7.0) , samen met de id van het opslag account en de resource-id van de sleutel kluis.

```azurecli-interactive
az monitor diagnostic-settings create --storage-account "<storage-account-id>" --resource "<key-vault-resource-id>" --name "Key vault logs" --logs '[{"category": "AuditEvent","enabled": true}]' --metrics '[{"category": "AllMetrics","enabled": true}]'
```

Met Azure PowerShell gebruiken we de cmdlet [set-AzDiagnosticSetting](/powershell/module/az.monitor/set-azdiagnosticsetting?view=azps-4.7.0) , waarbij de vlag **-enabled** is ingesteld op **$True** en de categorie ingesteld op `AuditEvent` (de enige categorie voor Key Vault logboek registratie):

```powershell-interactive
Set-AzDiagnosticSetting -ResourceId "<key-vault-resource-id>" -StorageAccountId $sa.id -Enabled $true -Category "AuditEvent"
```

U kunt desgewenst een Bewaar beleid instellen voor uw logboeken, zodat oudere logboeken automatisch worden verwijderd na een bepaalde tijd. U kunt bijvoorbeeld ingesteld Bewaar beleid instellen waarmee logboeken die ouder zijn dan 90 dagen automatisch worden verwijderd.

<!-- With the Azure CLI, use the [az monitor diagnostic-settings update](/cli/azure/monitor/diagnostic-settings?view=azure-cli-latest#az_monitor_diagnostic_settings_update) command. 

```azurecli-interactive
az monitor diagnostic-settings update 
```
-->

Gebruik met Azure PowerShell de cmdlet [set-AzDiagnosticSetting](/powershell/module/az.monitor/set-azdiagnosticsetting?view=azps-4.7.0) . 

```powershell-interactive
Set-AzDiagnosticSetting "<key-vault-resource-id>" -StorageAccountId $sa.id -Enabled $true -Category AuditEvent -RetentionEnabled $true -RetentionInDays 90
```

Wat wordt er vastgelegd:

* Alle geverifieerde REST-API-aanvragen, ook mislukte aanvragen als gevolg van toegangsmachtigingen, systeemfouten of ongeldige aanvragen.
* Bewerkingen voor de sleutelkluis zelf, zoals het maken, verwijderen en instellen van het toegangsbeleid voor sleutelkluizen, en het bijwerken van de kenmerken van sleutelkluizen, zoals tags.
* Bewerkingen van sleutels en geheimen in de sleutelkluis, waaronder:
  * Het maken, wijzigen of verwijderen van die sleutels of geheimen.
  * Het ondertekenen, verifiëren, versleutelen, ontsleutelen, verpakken en uitpakken van sleutels, het ophalen van geheimen en het vermelden van sleutels en geheimen (en hun versies).
* Niet-geverifieerde aanvragen die in een 401-respons resulteren. Voorbeelden daarvan zijn aanvragen die geen Bearer-token hebben, die ongeldig of verlopen zijn, of die een ongeldig token hebben.  
* Meldingsgebeurtenissen van Event Grid voor bijna verlopen, verlopen en toegangsbeleid van kluis gewijzigd (gebeurtenis voor nieuwe versie wordt niet vastgelegd). Gebeurtenissen worden vastgelegd ongeacht of er een gebeurtenisabonnement is gemaakt voor de sleutelkluis. Zie [Azure Event Grid-gebeurtenisschema voor Key Vault](../../event-grid/event-schema-key-vault.md) voor meer informatie.

## <a name="access-your-logs"></a>Toegang tot uw logboeken

Key Vault-logboeken worden opgeslagen in de container ' Insights-logboeken-audit event ' in het opslag account dat u hebt ingevoerd. Als u de logboeken wilt bekijken, moet u blobs downloaden.

Vermeld eerst alle blobs in de container.  Met de Azure CLI, gebruikt u de opdracht [AZ Storage BLOB List](/cli/azure/storage/blob?view=azure-cli-latest#az_storage_blob_list) .

```azurecli-interactive
az storage blob list --account-name "<your-unique-storage-account-name>" --container-name "insights-logs-auditevent"
```

Gebruik Azure PowerShell de lijst [Get-AzStorageBlob](/powershell/module/az.storage/get-azstorageblob?view=azps-4.7.0) alle blobs in deze container en voer het volgende in:

```powershell
Get-AzStorageBlob -Container "insights-logs-auditevent" -Context $sa.Context
```

Zoals u ziet vanuit de uitvoer van de Azure CLI-opdracht of de cmdlet Azure PowerShell, heeft de naam van de blobs de indeling `resourceId=<ARM resource ID>/y=<year>/m=<month>/d=<day of month>/h=<hour>/m=<minute>/filename.json` . De datum- en tijdwaarden maken gebruik van UTC.

Aangezien u hetzelfde opslagaccount kunt gebruiken voor het verzamelen van logboeken voor meerdere resources, is de volledige resource-id in de blobnaam handig voor toegang tot of het downloaden van alleen de blobs die u nodig hebt. Maar eerst laten we zien hoe u alle blobs kunt downloaden.

Met de Azure CLI, gebruikt u de opdracht [AZ Storage BLOB down load](/cli/azure/storage/blob?view=azure-cli-latest#az_storage_blob_download) , geeft u de namen van de blobs en het pad naar het bestand op waar u de resultaten wilt opslaan.

```azurecli-interactive
az storage blob download --container-name "insights-logs-auditevent" --file <path-to-file> --name "<blob-name>" --account-name "<your-unique-storage-account-name>"
```

Gebruik Azure PowerShell met de [gt-AzStorageBlobs](/powershell/module/az.storage/get-azstorageblob?view=azps-4.7.0) -cmdlet om een lijst van de blobs op te halen. vervolgens pipet u naar de cmdlet [Get-AzStorageBlobContent](/powershell/module/az.storage/get-azstorageblobcontent?view=azps-4.7.0) om de logboeken te downloaden naar het gekozen pad.

```powershell-interactive
$blobs = Get-AzStorageBlob -Container "insights-logs-auditevent" -Context $sa.Context | Get-AzStorageBlobContent -Destination "<path-to-file>"
```

Wanneer u deze tweede cmdlet in Power shell uitvoert, **/** maakt het scheidings teken in de naam van de BLOB een volledige mapstructuur onder de doelmap. U gebruikt deze structuur voor het downloaden en opslaan van de blobs als bestanden.

Als u alleen specifieke blobs wilt downloaden, moet u jokertekens gebruiken. Bijvoorbeeld:

* Als u meerdere sleutelkluizen hebt en het logboek voor slechts één sleutelkluis wilt downloaden met de naam CONTOSOKEYVAULT3:

  ```powershell
  Get-AzStorageBlob -Container "insights-logs-auditevent" -Context $sa.Context -Blob '*/VAULTS/CONTOSOKEYVAULT3
  ```

* Als u meerdere resourcegroepen hebt en logboeken voor slechts één resourcegroep wilt downloaden, gebruikt u `-Blob '*/RESOURCEGROUPS/<resource group name>/*'`:

  ```powershell
  Get-AzStorageBlob -Container "insights-logs-auditevent" -Context $sa.Context -Blob '*/RESOURCEGROUPS/CONTOSORESOURCEGROUP3/*'
  ```

* Als u alle logboeken voor de maand januari 2019 wilt downloaden, gebruikt u `-Blob '*/year=2019/m=01/*'`:

  ```powershell
  Get-AzStorageBlob -Container "insights-logs-auditevent" -Context $sa.Context -Blob '*/year=2016/m=01/*'
  ```

We gaan zo kijken wat er precies in de logboeken staat. Maar eerst moet u nog twee opdrachten kennen:

Zie [Key Vault logboek registratie: interpreteer uw Key Vault-logboeken](logging.md#interpret-your-key-vault-logs) voor meer informatie over het lezen van de logboeken.

## <a name="use-azure-monitor-logs"></a>Azure Monitor-logboeken gebruiken

Met de Key Vault-oplossing in Azure Monitor-logboeken kunt u de `AuditEvent`-logboeken van Key Vault controleren. In Azure Monitor-logboeken kunt u logboekquery’s gebruiken om gegevens te analyseren en de informatie op te halen die u nodig hebt.

Zie [Azure Key Vault in Azure Monitor](../../azure-monitor/insights/key-vault-insights-overview.md) voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

- Zie [Key Vault logging](logging.md) (Engelstalig) voor conceptuele informatie, inclusief het interpreteren van Key Vault logboeken
- Zie [Azure Key Vault in een webtoepassing gebruiken](tutorial-net-create-vault-azure-web-app.md) voor een zelfstudie over het gebruik van Azure Key Vault in een .NET-webtoepassing.
- Zie de [Ontwikkelaarshandleiding voor Azure Key Vault](developers-guide.md) voor het programmeren van verwijzingen.
