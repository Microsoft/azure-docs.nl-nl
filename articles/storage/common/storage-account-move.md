---
title: Een account Azure Storage naar een andere regio | Microsoft Docs
description: Laat zien hoe u een account Azure Storage verplaatsen naar een andere regio.
services: storage
author: normesta
ms.service: storage
ms.subservice: common
ms.topic: how-to
ms.date: 05/11/2020
ms.author: normesta
ms.reviewer: dineshm
ms.openlocfilehash: 1900326bf03c6a32f25c7a019d8bd1e460735bd6
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107505595"
---
# <a name="move-an-azure-storage-account-to-another-region"></a>Een account Azure Storage verplaatsen naar een andere regio

Als u een opslagaccount wilt verplaatsen, maak dan een kopie van uw opslagaccount in een andere regio. Verplaats vervolgens uw gegevens naar dat account met behulp van AzCopy of een ander hulpprogramma naar keuze.

In dit artikel leert u het volgende:

> [!div class="checklist"]
> 
> * Een sjabloon exporteren.
> * Wijzig de sjabloon door de naam van de doelregio en het opslagaccount toe te voegen.
> * Implementeer de sjabloon om het nieuwe opslagaccount te maken.
> * Configureer het nieuwe opslagaccount.
> * Gegevens verplaatsen naar het nieuwe opslagaccount.
> * Verwijder de resources in de bronregio.

## <a name="prerequisites"></a>Vereisten

- Zorg ervoor dat de services en functies die uw account gebruikt, worden ondersteund in de doelregio.

- Zorg ervoor dat uw abonnement is toegestaan voor de doelregio voor preview-functies.

<a id="prepare"></a>

## <a name="prepare"></a>Voorbereiden

Om aan de slag te gaan, exporteert en wijzigt u vervolgens een Resource Manager sjabloon. 

### <a name="export-a-template"></a>Een sjabloon exporteren

Deze sjabloon bevat instellingen die uw opslagaccount beschrijven. 

# <a name="portal"></a>[Portal](#tab/azure-portal)

Een sjabloon exporteren via de Azure-portal:

1. Meld u aan bij [Azure Portal](https://portal.azure.com).

2. Selecteer **Alle resources** en selecteer vervolgens uw opslagaccount.

3. Selecteer >   >  **Automation-exportsjabloon**.

4. Kies **Downloaden** op de **blade Sjabloon** exporteren.

5. Zoek het ZIP-bestand dat u hebt gedownload uit de portal en open het bestand in een map naar keuze.

   Dit zip-bestand bevat de .json-bestanden die de sjabloon en scripts vormen voor het implementeren van de sjabloon.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Een sjabloon exporteren met behulp van PowerShell:

1. Meld u aan bij uw [Azure-abonnement met de opdracht Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) en volg de instructies op het scherm:

   ```azurepowershell-interactive
   Connect-AzAccount
   ```
2. Als uw identiteit is gekoppeld aan meer dan één abonnement, stelt u uw actieve abonnement in op het abonnement van het opslagaccount dat u wilt verplaatsen.

   ```azurepowershell-interactive
   $context = Get-AzSubscription -SubscriptionId <subscription-id>
   Set-AzContext $context
   ```

3. Exporteert de sjabloon van uw bronopslagaccount. Met deze opdrachten wordt een json-sjabloon opgeslagen in uw huidige map.

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

1. Selecteer in Azure Portal de optie **Een resource maken.**

2. In **Marketplace doorzoeken typt** u **sjabloonimplementatie** en drukt u op **ENTER.**

3. Selecteer **Sjabloonimlementatie**.

    ![Azure Resource Manager-sjabloonbibliotheek](./media/storage-account-move/azure-resource-manager-template-library.png)

4. Selecteer **Maken**.

5. Selecteer **Bouw uw eigen sjabloon in de editor**.

6. Selecteer **Bestand laden** en volg de instructies voor het laden van **template.js** bestand dat u in de laatste sectie hebt gedownload.

7. In het **template.jsbestand, noemt** u het doelopslagaccount door de standaardwaarde van de naam van het opslagaccount in te stellen. In dit voorbeeld wordt de standaardwaarde van de naam van het opslagaccount ingesteld op `mytargetaccount` .
    
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
    Zie Azure-locaties voor het verkrijgen van [regiolocatiecodes.](https://azure.microsoft.com/global-infrastructure/locations/)  De code voor een regio is de regionaam zonder spaties, **VS -**  =  **centraalus**.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

De sjabloon implementeren met behulp van PowerShell:

1. In het **template.jsbestand, noemt** u het doelopslagaccount door de standaardwaarde van de naam van het opslagaccount in te stellen. In dit voorbeeld wordt de standaardwaarde van de naam van het opslagaccount ingesteld op `mytargetaccount` .
    
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

2. Bewerk **de eigenschap location** intemplate.js **on** file naar de doelregio. In dit voorbeeld wordt de doelregio op `eastus` .

    ```json
    "resources": [{
         "type": "Microsoft.Storage/storageAccounts",
         "apiVersion": "2019-04-01",
         "name": "[parameters('storageAccounts_mysourceaccount_name')]",
         "location": "eastus"
         }]          
    ```

    U kunt regiocodes verkrijgen door de opdracht [Get-AzLocation uit te](/powershell/module/az.resources/get-azlocation) voeren.

    ```azurepowershell-interactive
    Get-AzLocation | format-table 
    ```
---

<a id="move"></a>

## <a name="move"></a>Verplaatsen

Implementeer de sjabloon om een nieuw opslagaccount te maken in de doelregio. 

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Sla de **template.jsop in het** bestand.

2. Voer de eigenschapswaarden in of selecteer deze:

- **Abonnement**: Selecteer een Azure-abonnement.

- **Resourcegroep**: selecteer **Nieuwe maken** en geef de resourcegroep een naam.

- **Locatie:** selecteer een Azure-locatie.

3. Klik op het selectievakje Ik ga akkoord met de **bovenstaande** voorwaarden en klik vervolgens op de **knop Aankoop** selecteren.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Haal de abonnements-id op waar u het openbare IP-adres van het doel wilt implementeren met [Get-AzSubscription](/powershell/module/az.accounts/get-azsubscription):

   ```azurepowershell-interactive
   Get-AzSubscription
   ```

2. Gebruik deze opdrachten om uw sjabloon te implementeren:

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
| **Beleid voor levenscyclusbeheer** | [De levenscyclus van Azure Blob-opslag beheren](../blobs/storage-lifecycle-management-concepts.md) |
| **Statische websites** | [Een statische website hosten in Azure Storage](../blobs/storage-blob-static-website-how-to.md) |
| **Gebeurtenisabonnementen** | [Reageren op gebeurtenissen van Blob Storage](../blobs/storage-blob-event-overview.md) |
| **Waarschuwingen** | [Waarschuwingen voor activiteitenlogboek maken, weergeven en beheren met behulp van Azure Monitor](../../azure-monitor/alerts/alerts-activity-log.md) |
| **Content Delivery Network (CDN)** | [Gebruik Azure CDN voor toegang tot blobs met aangepaste domeinen via HTTPS](../blobs/storage-https-custom-domain-cdn.md) |

> [!NOTE] 
> Als u een CDN voor het bronopslagaccount hebt ingesteld, wijzigt u de oorsprong van uw bestaande CDN in het primaire blobservice-eindpunt (of het primaire statische website-eindpunt) van uw nieuwe account. 

### <a name="move-data-to-the-new-storage-account"></a>Gegevens verplaatsen naar het nieuwe opslagaccount

AzCopy is het voorkeurshulpprogramma om uw gegevens over te verplaatsen. Deze is geoptimaliseerd voor prestaties.  Een van de manieren waarop het sneller gaat, is gegevens rechtstreeks tussen opslagservers kopiëren, zodat AzCopy niet de netwerkbandbreedte van uw computer gebruikt. Gebruik AzCopy bij de opdrachtregel of als onderdeel van een aangepast script. Raadpleeg [Aan de slag met AzCopy](/azure/storage/common/storage-use-azcopy-v10?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

U kunt ook Azure Data Factory om uw gegevens te verplaatsen. Het biedt een intuïtieve gebruikersinterface. Als u Azure Data Factory wilt gebruiken, bekijkt u een van deze koppelingen:. 

  - [Gegevens kopiëren naar of uit Azure Blob Storage met behulp van Azure Data Factory](/azure/data-factory/connector-azure-blob-storage)
  - [Gegevens kopiëren naar of vanuit Azure Data Lake Storage Gen2 met behulp van Azure Data Factory](/azure/data-factory/connector-azure-data-lake-storage)
  - [Gegevens kopiëren vanuit of naar Azure File Storage met behulp van Azure Data Factory](/azure/data-factory/connector-azure-file-storage)
  - [Gegevens kopiëren van en naar Azure Table Storage met behulp van Azure Data Factory](/azure/data-factory/connector-azure-table-storage)

---

## <a name="discard-or-clean-up"></a>Verwijderen of opschonen

Als u na de implementatie opnieuw wilt beginnen, kunt u het doelopslagaccount [](#prepare) verwijderen [](#move) en de stappen herhalen die worden beschreven in de secties Voorbereiden en Verplaatsen van dit artikel.

Voor het doorvoeren van de wijzigingen en het verplaatsen van een opslagaccount moet u het bronopslagaccount verwijderen.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Een opslagaccount verwijderen via de Azure-portal:

1. Vouw in Azure Portal het menu aan de linkerkant uit om het menu met services te openen en kies Opslagaccounts **om** de lijst met opslagaccounts weer te geven.

2. Zoek het doelopslagaccount dat u wilt verwijderen en klik met de rechtermuisknop op de knop **Meer** (**...**) aan de rechterkant van de vermelding.

3. Selecteer **Verwijderen** en bevestig dit.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Als u de resourcegroep en de bijbehorende resources wilt verwijderen, met inbegrip van het nieuwe opslagaccount, gebruikt u de [opdracht Remove-AzStorageAccount:](/powershell/module/az.storage/remove-azstorageaccount)

```powershell
Remove-AzStorageAccount -ResourceGroupName  $resourceGroup -AccountName $storageAccount
```
---

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u een Azure-opslagaccount van de ene regio naar de andere verplaatst en de bronbronnen opgeschoond.  Raadpleeg voor meer informatie over het verplaatsen van resources tussen regio's en herstel na noodherstel in Azure:


- [Resources verplaatsen naar een nieuwe resourcegroep of een nieuw abonnement](../../azure-resource-manager/management/move-resource-group-and-subscription.md)
- [Virtuele Azure-machines verplaatsen naar een andere regio](../../site-recovery/azure-to-azure-tutorial-migrate.md)