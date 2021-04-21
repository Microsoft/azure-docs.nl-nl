---
title: Een Azure-bestandsshare maken
titleSuffix: Azure Files
description: Een Azure-bestands share maken met behulp van Azure Portal, PowerShell of de Azure CLI.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 04/05/2021
ms.author: rogarana
ms.subservice: files
ms.custom: devx-track-azurecli, references_regions
ms.openlocfilehash: 9caabb8dc7f09e4ef3852d9269d178c086744779
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107789803"
---
# <a name="create-an-azure-file-share"></a>Een Azure-bestandsshare maken
Als u een Azure-bestands share wilt maken, moet u drie vragen beantwoorden over hoe u deze gaat gebruiken:

- **Wat zijn de prestatievereisten voor uw Azure-bestands share?**  
    Azure Files biedt standaardbestands shares die worden gehost op hardware op basis van harde schijven (HDD) en Premium-bestands shares, die worden gehost op SSD-hardware (solid-state disk-based).

- **Wat zijn uw redundantievereisten voor uw Azure-bestands share?**  
    Standaardbestands shares bieden lokaal redundante (LRS), zone-redundante (ZRS), geografisch redundante (GRS) of geografisch zone-redundante opslag (GZRS), maar de functie voor grote bestands delen wordt alleen ondersteund op lokaal redundante en zone-redundante bestands shares. Premium-bestands shares bieden geen ondersteuning voor een vorm van geo-redundantie.

    Premium-bestands shares zijn beschikbaar met lokale redundantie en zone-redundantie in een subset van regio's. Als u wilt weten of Premium-bestands shares momenteel beschikbaar zijn in uw regio, gaat u naar de pagina Beschikbare [producten per regio](https://azure.microsoft.com/global-infrastructure/services/?products=storage) voor Azure. Zie redundantie voor meer informatie over [regio's Azure Storage ondersteuning voor ZRS.](../common/storage-redundancy.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)

- **Welke grootte bestands share hebt u nodig?**  
    In lokale en zone-redundante opslagaccounts kunnen Azure-bestands shares maximaal 100 TiB bespannen, maar in geografisch en geografisch zone-redundante opslagaccounts kunnen Azure-bestands shares slechts 5 TiB bespannen. 

Zie Planning for an Azure Files deployment (Planning voor een implementatie van een Azure Files [voor meer informatie over deze drie opties.](storage-files-planning.md)

## <a name="prerequisites"></a>Vereisten
- In dit artikel wordt ervan uitgegaan dat u al een Azure-abonnement hebt gemaakt. Als u nog geen abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.
- Als u van plan bent om Azure PowerShell te gebruiken, [installeert u de nieuwste versie](/powershell/azure/install-az-ps).
- Als u van plan bent om de Artikel CLI te gebruiken, [installeert u de nieuwste versie](/cli/azure/install-azure-cli).

## <a name="create-a-storage-account"></a>Een opslagaccount maken
Azure-bestandsshares worden geïmplementeerd in *opslagaccounts*. Dat zijn objecten op het hoogste niveau die een gedeelde opslagpool vertegenwoordigen. Deze opslaggroep kan worden gebruikt om meerdere bestands shares te implementeren. 

Azure ondersteunt meerdere typen opslagaccounts voor verschillende opslagscenario's die klanten mogelijk hebben, maar er zijn twee hoofdtypen opslagaccounts voor Azure Files. Welk type opslagaccount u moet maken, is afhankelijk van of u een standaardbestands share of een Premium-bestands share wilt maken: 

- **GPv2-opslagaccounts (versie twee voor algemeen gebruik)** : Met GPv2-opslagaccounts kunt u Azure-bestandsshares implementeren op (HDD-)hardware met een standaard/harde schijf. Naast het opslaan van Azure-bestandsshares kunnen GPv2-opslagaccounts andere opslagresources, zoals blobcontainers, wachtrijen of tabellen, opslaan. Bestands shares kunnen worden geïmplementeerd in de voor transacties geoptimaliseerde (standaard),hot- of cool-laag.

- **FileStorage-opslagaccounts**: Met FileStorage-opslagaccounts kunt u Azure-bestandsshares implementeren op (SSD-)hardware met een premium/solid-state schijf. FileStorage-accounts kunnen alleen worden gebruikt voor het opslaan van Azure-bestandsshares. Er kunnen geen andere opslagresources (blobcontainers, wachtrijen, tabellen enz.) worden geïmplementeerd in een FileStorage-account.

# <a name="portal"></a>[Portal](#tab/azure-portal)
Als u een opslagaccount wilt maken via Azure Portal selecteert **u + Een resource maken** vanuit het dashboard. Zoek in het Azure Marketplace zoekvenster naar **het opslagaccount** en selecteer het resulterende zoekresultaat. Dit leidt tot een overzichtspagina voor opslagaccounts; Selecteer **Maken om** door te gaan met de wizard voor het maken van het opslagaccount.

![Een schermopname van de optie voor het snel maken van een opslagaccount in een browser](media/storage-how-to-create-file-share/create-storage-account-0.png)

#### <a name="basics"></a>Basisbeginselen
De eerste sectie die moet worden voltooid om een opslagaccount te maken, heeft het label **Basisbeginselen.** Dit bevat alle vereiste velden om een opslagaccount te maken. Als u een GPv2-opslagaccount wilt maken, moet u ervoor zorgen dat het radioknop Prestaties is ingesteld op *Standard* en dat de vervolgkeuzelijst **Soort** account is geselecteerd op  *StorageV2 (algemeen gebruik v2).*

:::image type="content" source="media/storage-how-to-create-file-share/files-create-smb-share-performance-standard.png" alt-text="Een schermopname van het prestatierondje met standaard geselecteerd en soort account met storagev2 geselecteerd.":::

Als u een FileStorage-opslagaccount wilt maken, moet u ervoor zorgen dat het radioknop Prestaties is ingesteld op *Premium* en **dat Bestandsshares** is geselecteerd in de vervolgkeuzelijst **Type Premium-account.** 

:::image type="content" source="media/storage-how-to-create-file-share/files-create-smb-share-performance-premium.png" alt-text="Een schermopname van het prestatierondje met Premium geselecteerd en soort account met filestorage geselecteerd.":::

De andere basisvelden zijn onafhankelijk van de keuze van het opslagaccount:
- **Naam van opslagaccount:** de naam van de opslagaccountresource die moet worden gemaakt. Deze naam moet wereldwijd uniek zijn, maar anders kan elke wenste naam worden gebruikt. De naam van het opslagaccount wordt gebruikt als servernaam wanneer u een Azure-bestands share via SMB aan een share kunt toevoegen.
- **Locatie:** de regio waar het opslagaccount in moet worden geïmplementeerd. Dit kan de regio zijn die is gekoppeld aan de resourcegroep of een andere beschikbare regio.
- **Replicatie:** hoewel dit replicatie wordt gelabeld, betekent dit veld in feite **redundantie**; dit is het gewenste redundantieniveau: lokale redundantie (LRS), zone-redundantie (ZRS), geo-redundantie (GRS) en geo-zone-redundantie (GZRS). Deze vervolgkeuzelijst bevat ook geografisch redundantie met leestoegang (RA-GRS) en redundantie van de geografische zone met leestoegang (RA-GZRS), die niet van toepassing zijn op Azure-bestands shares; elke bestands share die is gemaakt in een opslagaccount met deze geselecteerde, is in feite respectievelijk geografisch redundant of geografisch zone-redundant. 

#### <a name="networking"></a>Netwerken
In de sectie Netwerken kunt u netwerkopties configureren. Deze instellingen zijn optioneel voor het maken van het opslagaccount en kunnen desgewenst later worden geconfigureerd. Zie netwerkoverwegingen voor Azure Files [informatie over deze opties.](storage-files-networking-overview.md)

#### <a name="data-protection"></a>Gegevensbeveiliging
In de sectie gegevensbeveiliging kunt u het beleid voor het soft-delete-beleid voor Azure-bestands shares in uw opslagaccount configureren. Andere instellingen met betrekking tot soft-delete voor blobs, containers, herstel naar een bepaald tijdstip voor containers, versie-versies en de wijzigingsfeed zijn alleen van toepassing op Azure Blob Storage.

#### <a name="advanced"></a>Geavanceerd
De sectie geavanceerd bevat verschillende belangrijke instellingen voor Azure-bestands shares:

- **Veilige overdracht vereist:** dit veld geeft aan of voor het opslagaccount versleuteling in transit is vereist voor communicatie met het opslagaccount. Als u ondersteuning voor SMB 2.1 nodig hebt, moet u dit uitschakelen.

    :::image type="content" source="media/storage-how-to-create-file-share/files-create-smb-share-secure-transfer.png" alt-text="Een schermopname van beveiligde overdracht die is ingeschakeld in de geavanceerde instellingen voor het opslagaccount.":::

- **Grote bestands shares:** met dit veld schakelt u het opslagaccount in voor bestands shares die maximaal 100 TiB beslaat. Als u deze functie inschakelen, wordt uw opslagaccount beperkt tot alleen lokaal redundante en zone-redundante opslagopties. Zodra een GPv2-opslagaccount is ingeschakeld voor grote bestands shares, kunt u de mogelijkheid voor grote bestands delen niet uitschakelen. FileStorage-opslagaccounts (opslagaccounts voor Premium-bestands shares) hebben deze optie niet, omdat alle Premium-bestands shares tot 100 TiB kunnen worden geschaald. 

    :::image type="content" source="media/storage-how-to-create-file-share/files-create-smb-share-large-file-shares.png" alt-text="Een schermopname van de instelling voor grote bestands delen op de blade Geavanceerd van het opslagaccount.":::

De andere instellingen die beschikbaar zijn op het tabblad Geavanceerd (hiërarchische naamruimte voor Azure Data Lake Storage Gen 2, standaard bloblaag, NFSv3 voor blobopslag, enzovoort), zijn niet van toepassing op Azure Files.

> [!Important]  
> Het selecteren van de blobtoegangslaag heeft geen invloed op de laag van de bestands share.

#### <a name="tags"></a>Tags
Tags zijn naam/waarde-paren waarmee u resources kunt categoriseren en geconsolideerde facturering kunt weergeven door dezelfde tag toe te passen op meerdere resources en resourcegroepen. Deze zijn optioneel en kunnen worden toegepast nadat het opslagaccount is gemaakt.

#### <a name="review--create"></a>Controleren en maken
De laatste stap voor het maken van het opslagaccount bestaat uit het selecteren **van de knop Maken** op het tabblad Beoordelen **en** maken. Deze knop is niet beschikbaar als niet alle vereiste velden voor een opslagaccount zijn ingevuld.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Voor het maken van een opslagaccount met Behulp van PowerShell gebruiken we de `New-AzStorageAccount` cmdlet . Deze cmdlet heeft veel opties; alleen de vereiste opties worden weergegeven. Zie de [ `New-AzStorageAccount` cmdlet-documentatie](/powershell/module/az.storage/new-azstorageaccount)voor meer informatie over geavanceerde opties.

Om het maken van het opslagaccount en de volgende bestands share te vereenvoudigen, slaan we verschillende parameters op in variabelen. U kunt de inhoud van de variabele vervangen door de waarden die u wilt, maar houd er rekening mee dat de naam van het opslagaccount globaal uniek moet zijn.

```powershell
$resourceGroupName = "myResourceGroup"
$storageAccountName = "mystorageacct$(Get-Random)"
$region = "westus2"
```

Voor het maken van een opslagaccount waarmee standaard Azure-bestands shares kunnen worden opgeslagen, gebruiken we de volgende opdracht. De parameter heeft betrekking op het gewenste type redundantie. Als u een geografisch redundant of geografisch zone-redundant opslagaccount wilt, moet u ook `-SkuName` de `-EnableLargeFileShare` parameter verwijderen.

```powershell
$storAcct = New-AzStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $storageAccountName `
    -SkuName Standard_LRS `
    -Location $region `
    -Kind StorageV2 `
    -EnableLargeFileShare
```

Voor het maken van een opslagaccount waarmee Premium Azure-bestands shares kunnen worden opgeslagen, gebruiken we de volgende opdracht. Houd er rekening mee `-SkuName` dat de parameter is gewijzigd zodat zowel als het `Premium` gewenste redundantieniveau van lokaal redundant ( ) worden `LRS` opgeslagen. De `-Kind` parameter is in plaats van omdat `FileStorage` `StorageV2` Premium-bestands shares moeten worden gemaakt in een FileStorage-opslagaccount in plaats van een GPv2-opslagaccount.

```powershell
$storAcct = New-AzStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $storageAccountName `
    -SkuName Premium_LRS `
    -Location $region `
    -Kind FileStorage 
```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)
Als u een opslagaccount wilt maken met behulp van de Azure CLI, gebruikt u de opdracht az storage account create. Deze opdracht heeft veel opties; alleen de vereiste opties worden weergegeven. Zie de opdrachtdocumentatie voor meer informatie over [ `az storage account create` de geavanceerde opties.](/cli/azure/storage/account)

Om het maken van het opslagaccount en de volgende bestands share te vereenvoudigen, slaan we verschillende parameters op in variabelen. U kunt de inhoud van de variabele vervangen door de waarden die u wilt, maar houd er rekening mee dat de naam van het opslagaccount globaal uniek moet zijn.

```azurecli
resourceGroupName="myResourceGroup"
storageAccountName="mystorageacct$RANDOM"
region="westus2"
```

Voor het maken van een opslagaccount waarmee standaard Azure-bestands shares kunnen worden opgeslagen, gebruiken we de volgende opdracht. De parameter heeft betrekking op het gewenste type redundantie. Als u een geografisch redundant of geografisch zone-redundant opslagaccount wilt, moet u ook `--sku` de `--enable-large-file-share` parameter verwijderen.

```azurecli
az storage account create \
    --resource-group $resourceGroupName \
    --name $storageAccountName \
    --kind StorageV2 \
    --sku Standard_ZRS \
    --enable-large-file-share \
    --output none
```

Voor het maken van een opslagaccount waarmee Premium Azure-bestands shares kunnen worden opgeslagen, gebruiken we de volgende opdracht. Houd er rekening mee `--sku` dat de parameter is gewijzigd zodat zowel als het `Premium` gewenste redundantieniveau van lokaal redundant ( ) worden `LRS` opgeslagen. De `--kind` parameter is in plaats van omdat `FileStorage` `StorageV2` Premium-bestands shares moeten worden gemaakt in een FileStorage-opslagaccount in plaats van een GPv2-opslagaccount.

```azurecli
az storage account create \
    --resource-group $resourceGroupName \
    --name $storageAccountName \
    --kind FileStorage \
    --sku Premium_LRS \
    --output none
```

---

## <a name="create-a-file-share"></a>Een bestandsshare maken
Nadat u uw opslagaccount hebt gemaakt, kunt u alleen nog de bestands share maken. Dit proces is grotendeels hetzelfde, ongeacht of u een Premium-bestands share of een standaardbestands share gebruikt. Houd rekening met de volgende verschillen.

Standaardbestands shares kunnen worden geïmplementeerd in een van de standaardlagen: geoptimaliseerd voor transacties (standaard), hot of cool. Dit is een laag per bestands  share die niet wordt beïnvloed door de blob-toegangslaag van het opslagaccount (deze eigenschap heeft alleen betrekking op Azure Blob Storage- deze heeft helemaal geen betrekking op Azure Files). U kunt de laag van de share op elk moment wijzigen nadat deze is geïmplementeerd. Premium-bestands shares kunnen niet rechtstreeks worden geconverteerd naar een Standard-laag.

> [!Important]  
> U kunt bestandsshares verplaatsen tussen lagen binnen GPv2-opslagaccounttypen (geoptimaliseerd voor transacties, dynamisch en statisch). Als u shares tussen lagen verplaatst, worden er kosten in rekening gebracht: als u van een dynamischere laag verplaatst naar een statischere laag, worden er schrijfkosten in rekeningen gebracht in de statische laag voor elk bestand in de share, terwijl er van de statischere laag naar de dynamischere laag leeskosten in rekening worden gebracht voor de statische laag voor elk bestand in de share.

De **quotum-eigenschap** betekent iets anders tussen Premium- en Standard-bestands shares:

- Voor standaardbestands shares is dit een bovengrens van de Azure-bestands share, waaruit eindgebruikers niet kunnen gaan. Als er geen quotum is opgegeven, kan de standaardbestands share maximaal 100 TiB bespannen (of 5 TiB als de eigenschap voor grote bestands shares niet is ingesteld voor een opslagaccount).

- Voor Premium-bestands shares betekent quotum **de inrichtende grootte**. De inrichtende grootte is de hoeveelheid die wordt gefactureerd, ongeacht het werkelijke gebruik. Zie Premium-bestands shares inrichten voor meer informatie over het plannen [van een Premium-bestands share.](understanding-billing.md#provisioned-model)

# <a name="portal"></a>[Portal](#tab/azure-portal)
Als u zojuist uw opslagaccount hebt gemaakt, kunt u er naartoe navigeren vanuit het implementatiescherm door **Ga naar resource te selecteren.** Selecteer in het opslagaccount de **bestands shares** in de inhoudsopgave voor het opslagaccount.

In de lijst met bestands share ziet u alle bestands shares die u eerder hebt gemaakt in dit opslagaccount; een lege tabel als er nog geen bestands shares zijn gemaakt. Selecteer **+ Bestands share** om een nieuwe bestands share te maken.

De blade nieuwe bestands delen wordt weergegeven op het scherm. Vul de velden in de blade nieuwe bestands share in om een bestands share te maken:

- **Naam:** de naam van de bestands share die moet worden gemaakt.
- **Quotum:** het quotum van de bestands share voor standaardbestands shares; de inrichtende grootte van de bestands share voor Premium-bestands shares.
- **Lagen:** de geselecteerde laag voor een bestands share. Dit veld is alleen beschikbaar in een **GPv2-opslagaccount (algemeen gebruik).** U kunt kiezen voor geoptimaliseerde transacties, 'hot' of 'cool'. De laag van de share kan op elk moment worden gewijzigd. We raden u aan om tijdens een migratie de meest populaire laag te kiezen, om de transactiekosten te minimaliseren en desgewenst over te schakelen naar een lagere laag nadat de migratie is voltooid.

Selecteer **Maken om** het maken van de nieuwe share te beëindigen.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
U kunt een Azure-bestands share maken met de [`New-AzRmStorageShare`](/powershell/module/az.storage/New-AzRmStorageShare) cmdlet . Bij de volgende PowerShell-opdrachten wordt ervan uitgenomen dat u de variabelen hebt ingesteld en zoals hierboven is gedefinieerd in de sectie Een opslagaccount `$resourceGroupName` `$storageAccountName` maken met Azure PowerShell. 

In het volgende voorbeeld ziet u hoe u een bestands share maakt met een expliciete laag met behulp van de `-AccessTier` parameter . Hiervoor moet u de preview-module Az.Storage gebruiken, zoals aangegeven in het voorbeeld. Als een laag niet is opgegeven, omdat u de AZ.Storage-module ga gebruikt of omdat u deze opdracht niet hebt toegevoegd, wordt de standaardlaag voor standaardbestands shares geoptimaliseerd voor transacties.

> [!Important]  
> Voor Premium-bestands shares `-QuotaGiB` verwijst de parameter naar de inrichtende grootte van de bestands share. De inrichten grootte van de bestands share is het bedrag dat u in rekening wordt gefactureerd, ongeacht het gebruik. Standaardbestands shares worden gefactureerd op basis van gebruik in plaats van de ingerichte grootte.

```powershell
# Assuming $resourceGroupName and $storageAccountName from earlier in this document have already
# been populated. The access tier parameter may be TransactionOptimized, Hot, or Cool for GPv2 
# storage accounts. Standard tiers are only available in standard storage accounts. 
$shareName = "myshare"

New-AzRmStorageShare `
        -ResourceGroupName $resourceGroupName `
        -StorageAccountName $storageAccountName `
        -Name $shareName `
        -AccessTier TransactionOptimized `
        -QuotaGiB 1024 | `
    Out-Null
```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)
U kunt een Azure-bestands share maken met de [`az storage share-rm create`](/cli/azure/storage/share-rm#az_storage_share_rm_create) opdracht . Bij de volgende Azure CLI-opdrachten wordt ervan uitgenomen dat u de variabelen hebt ingesteld en zoals hierboven is gedefinieerd in de sectie Een `$resourceGroupName` `$storageAccountName` opslagaccount maken met Azure CLI.

> [!Important]  
> Voor Premium-bestands shares `--quota` verwijst de parameter naar de inrichtende grootte van de bestands share. De inrichten grootte van de bestands share is het bedrag dat u in rekening wordt gefactureerd, ongeacht het gebruik. Standaardbestands shares worden gefactureerd op basis van gebruik in plaats van de ingerichte grootte.

```azurecli
shareName="myshare"

az storage share-rm create \
    --resource-group $resourceGroupName \
    --storage-account $storageAccountName \
    --name $shareName \
    --access-tier "TransactionOptimized" \
    --quota 1024 \
    --output none
```

---

> [!Note]  
> De naam van de bestandsshare mag alleen uit kleine letters bestaan. Zie [Naming and referencing shares, directories, files, and metadata](/rest/api/storageservices/Naming-and-Referencing-Shares--Directories--Files--and-Metadata) (Shares, mappen, bestanden en metagegevens een naam geven en hiernaar verwijzen) voor meer informatie over de naamgeving van bestandsshares en bestanden.

### <a name="change-the-tier-of-an-azure-file-share"></a>De laag van een Azure-bestands share wijzigen
Bestands shares die zijn geïmplementeerd in het opslagaccount voor algemeen gebruik **v2 (GPv2),** kunnen zich in de voor transacties geoptimaliseerde, 'hot' of 'cool' lagen. U kunt de laag van de Azure-bestands share op elk moment wijzigen, afhankelijk van transactiekosten zoals hierboven beschreven.

# <a name="portal"></a>[Portal](#tab/azure-portal)
Selecteer op de hoofdpagina  van het opslagaccount bestands shares de tegel met het label Bestands shares **(u** kunt ook naar Bestands shares **navigeren** via de inhoudsopgave voor het opslagaccount).

:::image type="content" source="media/storage-files-quick-create-use-windows/click-files.png" alt-text="Schermopname van de blade Opslagaccount, bestands shares geselecteerd.":::

Selecteer in de tabellijst met bestands shares de bestands share waarvoor u de laag wilt wijzigen. Selecteer op de overzichtspagina van de bestands share **de optie Laag** wijzigen in het menu.

![Een schermopname van de overzichtspagina van de bestands share met de knop Laag wijzigen gemarkeerd](media/storage-how-to-create-file-share/change-tier-0.png)

Selecteer in het resulterende dialoogvenster de gewenste laag: geoptimaliseerde transactie, hot of cool.

![Een schermopname van het dialoogvenster Laag wijzigen](media/storage-how-to-create-file-share/change-tier-1.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
De volgende PowerShell-cmdlet gaat ervan uit dat u de variabelen , en hebt ingesteld zoals beschreven in de eerdere `$resourceGroupName` `$storageAccountName` `$shareName` secties van dit document.

```PowerShell
Update-AzRmStorageShare `
    -ResourceGroupName $resourceGroupName `
    -StorageAccountName $storageAccountName `
    -Name $shareName `
    -AccessTier Cool
```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)
De volgende Azure CLI-opdracht gaat ervan uit dat u de variabelen , en hebt ingesteld zoals beschreven in de eerdere `$resourceGroupName` `$storageAccountName` `$shareName` secties van dit document.

```azurecli
az storage share-rm update \
    --resource-group $resourceGroupName \
    --storage-account $storageAccountName \
    --name $shareName \
    --access-tier "Cool"
```

---

## <a name="next-steps"></a>Volgende stappen
- [Plan een implementatie van Azure Files of](storage-files-planning.md) Plan voor een implementatie van [Azure File Sync](../file-sync/file-sync-planning.md). 
- [Netwerkoverzicht.](storage-files-networking-overview.md)
- Maak verbinding met een bestands share en koppel [deze op Windows](storage-how-to-use-files-windows.md), [macOS](storage-how-to-use-files-mac.md)en [Linux.](storage-how-to-use-files-linux.md)