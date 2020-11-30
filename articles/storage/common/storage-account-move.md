---
title: Een Azure Storage-account naar een andere regio verplaatsen | Microsoft Docs
description: Laat zien hoe u een Azure Storage account naar een andere regio kunt verplaatsen.
services: storage
author: normesta
ms.service: storage
ms.subservice: common
ms.topic: how-to
ms.date: 05/11/2020
ms.author: normesta
ms.reviewer: dineshm
ms.openlocfilehash: b70beb90fae794eb5512cb8b466524169c4c7b53
ms.sourcegitcommit: ac7029597b54419ca13238f36f48c053a4492cb6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/29/2020
ms.locfileid: "92792987"
---
# <a name="move-an-azure-storage-account-to-another-region"></a>Een Azure Storage-account naar een andere regio verplaatsen

Als u een opslagaccount wilt verplaatsen, maak dan een kopie van uw opslagaccount in een andere regio. Verplaats uw gegevens vervolgens naar het account met behulp van AzCopy of een ander hulp programma van uw keuze.

In dit artikel leert u het volgende:

> [!div class="checklist"]
> 
> * Een sjabloon exporteren.
> * Wijzig de sjabloon door de doel regio en de naam van het opslag account toe te voegen.
> * Implementeer de sjabloon om het nieuwe opslag account te maken.
> * Configureer het nieuwe opslag account.
> * Verplaats gegevens naar het nieuwe opslag account.
> * Verwijder de resources in de bron regio.

## <a name="prerequisites"></a>Vereisten

- Zorg ervoor dat de services en functies die uw account gebruikt, worden ondersteund in de doelregio.

- U kunt preview-functies alleen gebruiken als uw abonnement is goedgekeurd voor de doelregio.

<a id="prepare"></a>

## <a name="prepare"></a>Voorbereiden

Om aan de slag te gaan, een resource manager-sjabloon te exporteren en vervolgens te wijzigen. 

### <a name="export-a-template"></a>Een sjabloon exporteren

Deze sjabloon bevat instellingen die uw opslagaccount beschrijven. 

# <a name="portal"></a>[Portal](#tab/azure-portal)

Een sjabloon exporteren via de Azure-portal:

1. Meld u aan bij [Azure Portal](https://portal.azure.com).

2. Selecteer **alle resources** en selecteer vervolgens uw opslag account.

3. Selecteer > **instellingen**  >  **sjabloon exporteren**.

4. Kies **downloaden** in de Blade **sjabloon exporteren** .

5. Zoek het zip-bestand dat u hebt gedownload van de portal en pak het bestand uit naar een map van uw keuze.

   Dit zip-bestand bevat de. json-bestanden waaruit de sjabloon en scripts bestaan voor het implementeren van de sjabloon.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Een sjabloon exporteren met behulp van Power shell:

1. Meld u aan bij uw Azure-abonnement met de opdracht [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) en volg de instructies op het scherm:

   ```azurepowershell-interactive
   Connect-AzAccount
   ```
2. Als uw identiteit is gekoppeld aan meer dan één abonnement, stelt u uw actieve abonnement in op het abonnement van het opslag account dat u wilt verplaatsen.

   ```azurepowershell-interactive
   $context = Get-AzSubscription -SubscriptionId <subscription-id>
   Set-AzContext $context
   ```

3. Exporteer de sjabloon van uw bron-opslag account. Met deze opdrachten wordt een JSON-sjabloon opgeslagen in de huidige map.

   ```azurepowershell-interactive
   $resource = Get-AzResource `
     -ResourceGroupName <resource-group-name> `
     -ResourceName <storage-account-name> `
     -ResourceType Microsoft.Storage/storageAccounts
   Export-AzResourceGroup `
     -ResourceGroupName <resource-group-name> `
     -Resource $resource.ResourceId
   ```

---

### <a name="modify-the-template"></a>De sjabloon aanpassen 

Wijzig de sjabloon door de naam en regio van het opslagaccount te wijzigen.

# <a name="portal"></a>[Portal](#tab/azure-portal)

De sjabloon implementeren met behulp van Azure Portal:

1. Selecteer in de Azure Portal **een resource maken**.

2. Typ in **de Marketplace zoeken de** **sjabloon implementatie** en druk vervolgens op **Enter**.

3. Selecteer **Sjabloonimlementatie**.

    ![Azure Resource Manager-sjabloonbibliotheek](./media/storage-account-move/azure-resource-manager-template-library.png)

4. Selecteer **Maken**.

5. Selecteer **Bouw uw eigen sjabloon in de editor**.

6. Selecteer **bestand laden** en volg de instructies voor het laden van de **template.jsin** het bestand dat u in de laatste sectie hebt gedownload.

7. Geef in het **template.js** bestand de naam van het doel-opslag account op door de standaard waarde van de naam van het opslag account in te stellen. In dit voor beeld wordt de standaard waarde van de naam van het opslag account ingesteld op `mytargetaccount` .
    
    ```json
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storageAccounts_mysourceaccount_name": {
            "defaultValue": "mytargetaccount",
            "type": "String"
        }
    },
 
8. Edit the **location** property in the **template.json** file to the target region. This example sets the target region to `centralus`.

    ```json
    "resources": [{
         "type": "Microsoft.Storage/storageAccounts",
         "apiVersion": "2019-04-01",
         "name": "[parameters('storageAccounts_mysourceaccount_name')]",
         "location": "centralus"
         }]          
    ```
    Zie [Azure-locaties](https://azure.microsoft.com/global-infrastructure/locations/)voor het verkrijgen van regio-locatie codes.  De code voor een regio is de naam van de regio zonder spaties, **Central VS**-  =  **Midden**.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

De sjabloon implementeren met behulp van Power shell:

1. Geef in het **template.js** bestand de naam van het doel-opslag account op door de standaard waarde van de naam van het opslag account in te stellen. In dit voor beeld wordt de standaard waarde van de naam van het opslag account ingesteld op `mytargetaccount` .
    
    ```json
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storageAccounts_mysourceaccount_name": {
            "defaultValue": "mytargetaccount",
            "type": "String"
        }
    },
    ``` 

2. Bewerk de eigenschap **Location** in de **template.jsop** bestand naar de doel regio. In dit voor beeld wordt de doel regio ingesteld op `eastus` .

    ```json
    "resources": [{
         "type": "Microsoft.Storage/storageAccounts",
         "apiVersion": "2019-04-01",
         "name": "[parameters('storageAccounts_mysourceaccount_name')]",
         "location": "eastus"
         }]          
    ```

    U kunt regio codes verkrijgen door de opdracht [Get-AzLocation](/powershell/module/az.resources/get-azlocation) uit te voeren.

    ```azurepowershell-interactive
    Get-AzLocation | format-table 
    ```
---

<a id="move"></a>

## <a name="move"></a>Verplaatsen

Implementeer de sjabloon om een nieuw opslagaccount te maken in de doelregio. 

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Sla de **template.jsop in** het bestand.

2. Voer de eigenschaps waarden in of Selecteer deze:

- **Abonnement**: Selecteer een Azure-abonnement.

- **Resourcegroep**: selecteer **Nieuwe maken** en geef de resourcegroep een naam.

- **Locatie**: Selecteer een Azure-locatie.

3. Klik op het selectie vakje **Ik ga akkoord met de bovenstaande voor waarden** en klik vervolgens op de knop **aankoop selecteren** .

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Vraag de abonnements-ID op waar u het doel-open bare IP-adres wilt implementeren met [Get-AzSubscription](/powershell/module/az.accounts/get-azsubscription):

   ```azurepowershell-interactive
   Get-AzSubscription
   ```

2. Gebruik de volgende opdrachten om uw sjabloon te implementeren:

   ```azurepowershell-interactive
   $resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
   $location = Read-Host -Prompt "Enter the location (i.e. centralus)"

   New-AzResourceGroup -Name $resourceGroupName -Location "$location"
   New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri "<name of your local template file>"  
   ```
---

### <a name="configure-the-new-storage-account"></a>Het nieuwe opslagaccount configureren

Sommige functies worden niet geëxporteerd naar een sjabloon. U moet deze dus toevoegen aan het nieuwe opslagaccount. 

De volgende tabel bevat een overzicht van deze functies en richtlijnen voor het toevoegen ervan aan uw nieuwe opslagaccount.

| Functie    | Hulp    |
|--------|-----------|
| **Levenscyclus beheer beleid** | [De levenscyclus van Azure Blob-opslag beheren](../blobs/storage-lifecycle-management-concepts.md) |
| **Statische websites** | [Een statische website hosten in Azure Storage](../blobs/storage-blob-static-website-how-to.md) |
| **Gebeurtenisabonnementen** | [Reageren op gebeurtenissen van Blob Storage](../blobs/storage-blob-event-overview.md) |
| **Waarschuwingen** | [Waarschuwingen voor activiteiten logboek maken, weer geven en beheren met behulp van Azure Monitor](../../azure-monitor/platform/alerts-activity-log.md) |
| **Content Delivery Network (CDN)** | [Azure CDN gebruiken om toegang te krijgen tot blobs met aangepaste domeinen via HTTPS](../blobs/storage-https-custom-domain-cdn.md) |

> [!NOTE] 
> Als u een CDN hebt ingesteld voor het bron-opslag account, wijzigt u alleen de oorsprong van uw bestaande CDN in het eind punt van de primaire BLOB-service (of het primaire eind punt van een statische website) van uw nieuwe account. 

### <a name="move-data-to-the-new-storage-account"></a>Gegevens verplaatsen naar het nieuwe opslagaccount

AzCopy is het voorkeurs programma waarmee u uw gegevens kunt verplaatsen. Deze is geoptimaliseerd voor prestaties.  Een van de manieren waarop het sneller gaat, is gegevens rechtstreeks tussen opslagservers kopiëren, zodat AzCopy niet de netwerkbandbreedte van uw computer gebruikt. Gebruik AzCopy bij de opdrachtregel of als onderdeel van een aangepast script. Raadpleeg [Aan de slag met AzCopy](/azure/storage/common/storage-use-azcopy-v10?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

U kunt Azure Data Factory ook gebruiken om uw gegevens te verplaatsen. Het biedt een intuïtieve gebruikers interface. Als u Azure Data Factory wilt gebruiken, raadpleegt u een van de volgende koppelingen:. 

  - [Gegevens kopiëren naar of uit Azure Blob Storage met behulp van Azure Data Factory](/azure/data-factory/connector-azure-blob-storage)
  - [Gegevens kopiëren naar of vanuit Azure Data Lake Storage Gen2 met behulp van Azure Data Factory](/azure/data-factory/connector-azure-data-lake-storage)
  - [Gegevens kopiëren vanuit of naar Azure File Storage met behulp van Azure Data Factory](/azure/data-factory/connector-azure-file-storage)
  - [Gegevens kopiëren van en naar Azure Table Storage met behulp van Azure Data Factory](/azure/data-factory/connector-azure-table-storage)

---

## <a name="discard-or-clean-up"></a>Verwijderen of opschonen

Als u na de implementatie wilt beginnen, kunt u het doel opslag account verwijderen en de stappen herhalen die worden beschreven in de secties [voorbereiden](#prepare) en [verplaatsen](#move) van dit artikel.

Voor het doorvoeren van de wijzigingen en het verplaatsen van een opslagaccount moet u het bronopslagaccount verwijderen.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Een opslagaccount verwijderen via de Azure-portal:

1. Vouw in het Azure Portal het menu aan de linkerkant uit om het menu met services te openen en kies **opslag accounts** om de lijst met uw opslag accounts weer te geven.

2. Zoek het doel-opslag account dat u wilt verwijderen en klik met de rechter muisknop op de knop **meer** (**...**) aan de rechter kant van de vermelding.

3. Selecteer **verwijderen** en bevestigen.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Als u de resource groep en de bijbehorende resources wilt verwijderen, met inbegrip van het nieuwe opslag account, gebruikt u de opdracht [Remove-AzStorageAccount](/powershell/module/az.storage/remove-azstorageaccount) :

```powershell
Remove-AzStorageAccount -ResourceGroupName  $resourceGroup -AccountName $storageAccount
```
---

## <a name="next-steps"></a>Volgende stappen

In deze zelf studie hebt u een Azure Storage-account verplaatst van de ene regio naar een andere en de bron resources opgeschoond.  Raadpleeg voor meer informatie over het verplaatsen van resources tussen regio's en herstel na nood gevallen in Azure:


- [Resources verplaatsen naar een nieuwe resourcegroep of een nieuw abonnement](../../azure-resource-manager/management/move-resource-group-and-subscription.md)
- [Virtuele Azure-machines verplaatsen naar een andere regio](../../site-recovery/azure-to-azure-tutorial-migrate.md)