---
title: Een Azure-bestandsshare maken
titleSuffix: Azure Files
description: Een Azure-bestands share maken met behulp van de Azure Portal, Power shell of de Azure CLI.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 1/20/2021
ms.author: rogarana
ms.subservice: files
ms.custom: devx-track-azurecli, references_regions
ms.openlocfilehash: 5a27f38e92955c0aa240f6be394aacd187c3a8b8
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100370900"
---
# <a name="create-an-azure-file-share"></a>Een Azure-bestandsshare maken
Als u een Azure-bestands share wilt maken, moet u drie vragen beantwoorden over hoe u deze gaat gebruiken:

- **Wat zijn de prestatie vereisten voor uw Azure-bestands share?**  
    Azure Files biedt standaard bestands shares die worden gehost op harde schijven (op basis van HDD), en Premium-bestands shares, die worden gehost op op schijf gebaseerde (op SSD gebaseerde) hardware.

- **Wat zijn uw redundantie vereisten voor uw Azure-bestands share?**  
    Standaard bestands shares bieden lokaal redundante (LRS), zone redundante (ZRS), geo-redundante (GRS) of geo-zone-redundante opslag (GZRS), maar de functie voor grote bestands shares wordt alleen ondersteund op lokaal redundante en zone redundante bestands shares. Premium-bestands shares bieden geen ondersteuning voor enige vorm van geo-redundantie.

    Premium-bestands shares zijn beschikbaar met lokale redundantie en zone redundantie in een subset van regio's. Zie de pagina [producten beschikbaar per regio](https://azure.microsoft.com/global-infrastructure/services/?products=storage) voor Azure als u wilt weten of Premium-bestands shares op dit moment beschikbaar zijn in uw regio. Zie [Azure Storage redundantie](../common/storage-redundancy.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)voor informatie over REGIO'S die ZRS ondersteunen.

- **Welk formaat bestands share hebt u nodig?**  
    In lokale en zone redundante opslag accounts kan Azure-bestands shares Maxi maal 100 TiB beslaan, maar in geo-en geo-zone-redundante opslag accounts kunnen Azure-bestands shares slechts tot 5 TiB beslaan. 

Zie [planning voor een Azure files-implementatie](storage-files-planning.md)voor meer informatie over deze drie opties.

## <a name="prerequisites"></a>Vereisten
- In dit artikel wordt ervan uitgegaan dat u al een Azure-abonnement hebt gemaakt. Als u nog geen abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.
- Als u van plan bent om Azure PowerShell te gebruiken, [installeert u de nieuwste versie](/powershell/azure/install-az-ps).
- Als u van plan bent om de Artikel CLI te gebruiken, [installeert u de nieuwste versie](/cli/azure/install-azure-cli?preserve-view=true&view=azure-cli-latest).

## <a name="create-a-storage-account"></a>Een opslagaccount maken
Azure-bestandsshares worden geïmplementeerd in *opslagaccounts*. Dat zijn objecten op het hoogste niveau die een gedeelde opslagpool vertegenwoordigen. Deze opslag groep kan worden gebruikt voor het implementeren van meerdere bestands shares. 

Azure ondersteunt meerdere typen opslag accounts voor verschillende opslag scenario's die klanten kunnen hebben, maar er zijn twee hoofd typen opslag accounts voor Azure Files. Welk type opslag account u moet maken, is afhankelijk van of u een standaard bestands share of een Premium-bestands share wilt maken: 

- **GPv2-opslagaccounts (versie twee voor algemeen gebruik)** : Met GPv2-opslagaccounts kunt u Azure-bestandsshares implementeren op (HDD-)hardware met een standaard/harde schijf. Naast het opslaan van Azure-bestandsshares kunnen GPv2-opslagaccounts andere opslagresources, zoals blobcontainers, wachtrijen of tabellen, opslaan. Bestands shares kunnen worden geïmplementeerd in de door de trans actie geoptimaliseerde (standaard), warme of koud lagen.

- **FileStorage-opslagaccounts**: Met FileStorage-opslagaccounts kunt u Azure-bestandsshares implementeren op (SSD-)hardware met een premium/solid-state schijf. FileStorage-accounts kunnen alleen worden gebruikt voor het opslaan van Azure-bestandsshares. Er kunnen geen andere opslagresources (blobcontainers, wachtrijen, tabellen enz.) worden geïmplementeerd in een FileStorage-account.

# <a name="portal"></a>[Portal](#tab/azure-portal)
Als u een opslag account wilt maken via de Azure Portal, selecteert u **+ een resource maken** in het dash board. Zoek in het resulterende Azure Marketplace-Zoek venster naar **Storage-account** en selecteer het resulterende Zoek resultaat. Dit leidt tot een overzichts pagina voor opslag accounts. Selecteer **maken** om door te gaan met de wizard voor het maken van het opslag account.

![Een scherm afbeelding van de optie snel maken van het opslag account in een browser](media/storage-how-to-create-file-share/create-storage-account-0.png)

#### <a name="basics"></a>Basisbeginselen
De eerste sectie die u moet volt ooien om een opslag account te maken, is voorzien van een **label.** Dit bevat alle vereiste velden voor het maken van een opslag account. Als u een GPv2-opslag account wilt maken, moet u ervoor zorgen dat het keuze rondje **prestaties** is ingesteld op *standaard* en dat de vervolg keuzelijst **account type** is geselecteerd voor *StorageV2 (algemeen gebruik v2)*.

![Een scherm afbeelding van het keuze rondje prestaties met de standaard geselecteerd en het soort account waarvoor StorageV2 is geselecteerd](media/storage-how-to-create-file-share/create-storage-account-1.png)

Als u een FileStorage-opslag account wilt maken, moet u ervoor zorgen dat het keuze rondje **prestaties** is ingesteld op *Premium* en dat de vervolg keuzelijst **account type** is geselecteerd voor *FileStorage*.

![Een scherm afbeelding van het keuze rondje prestaties met de selectie vakjes geselecteerd en account type waarvoor FileStorage is geselecteerd](media/storage-how-to-create-file-share/create-storage-account-2.png)

De andere basis velden zijn onafhankelijk van de keuze van het opslag account:
- **Naam van het opslag account**: de naam van de bron van het opslag account die moet worden gemaakt. Deze naam moet globaal uniek zijn, maar kan ook een wille keurige naam hebben. De naam van het opslag account wordt gebruikt als de server naam wanneer u een Azure-bestands share koppelt via SMB.
- **Locatie**: de regio voor het opslag account dat moet worden geïmplementeerd. Dit kan de regio zijn die is gekoppeld aan de resource groep of een andere beschik bare regio.
- **Replicatie**: Hoewel dit een gelabelde replicatie is, betekent dit veld in feite **Redundantie**; Dit is het gewenste redundantie niveau: lokale redundantie (LRS), zone redundantie (ZRS), geo-redundantie (GRS) en geo-zone-redundantie (GZRS). Deze vervolg keuzelijst bevat ook geo-redundantie met lees toegang (RA-GRS) en de geo-zone redundantie met lees toegang (RA-GZRS), die niet van toepassing zijn op Azure-bestands shares. een bestands share die is gemaakt in een opslag account waarvoor deze optie is geselecteerd, is respectievelijk geo-redundant of geo-zone-redundant. 

#### <a name="networking"></a>Netwerken
In het gedeelte netwerken kunt u netwerk opties configureren. Deze instellingen zijn optioneel voor het maken van het opslag account en kunnen later indien gewenst worden geconfigureerd. Zie [Azure files Networking-overwegingen](storage-files-networking-overview.md)voor meer informatie over deze opties.

#### <a name="data-protection"></a>Gegevensbeveiliging
In de sectie gegevens beveiliging kunt u het beleid voor voorlopig verwijderen voor Azure-bestands shares configureren in uw opslag account. Andere instellingen met betrekking tot het voorlopig verwijderen van blobs, containers, herstel naar een bepaald tijdstip voor containers, versie beheer en wijzigings feed zijn alleen van toepassing op Azure Blob-opslag.

#### <a name="advanced"></a>Geavanceerd
De sectie Geavanceerd bevat verschillende belang rijke instellingen voor Azure-bestands shares:

- **Beveiligde overdracht vereist**: dit veld geeft aan of voor het opslag account versleuteling moet worden uitgevoerd voor communicatie naar het opslag account. Als u ondersteuning voor SMB 2,1 nodig hebt, moet u dit uitschakelen.
- **Grote bestands shares**: dit veld schakelt het opslag account in voor bestands shares van maxi maal 100 Tib. Als u deze functie inschakelt, wordt uw opslag account beperkt tot alleen lokaal redundante opslag en zone-redundante Storage-opties. Zodra een GPv2-opslag account is ingeschakeld voor grote bestands shares, kunt u de mogelijkheid voor grote bestands shares niet uitschakelen. FileStorage-opslag accounts (opslag accounts voor Premium-bestands shares) beschikken niet over deze optie, omdat alle Premium-bestands shares kunnen worden geschaald tot 100 TiB. 

![Een scherm opname van de belang rijke geavanceerde instellingen die van toepassing zijn op Azure Files](media/storage-how-to-create-file-share/create-storage-account-3.png)

De overige instellingen die beschikbaar zijn op het tabblad Geavanceerd (hiërarchische naam ruimte voor Azure Data Lake Storage gen 2, standaardblob-laag, NFSv3 voor Blob Storage, enzovoort) zijn niet van toepassing op Azure Files.

> [!Important]  
> Als u de BLOB-toegangs laag selecteert, heeft dit geen invloed op de laag van de bestands share.

#### <a name="tags"></a>Tags
Tags zijn naam/waarde-paren waarmee u resources kunt categoriseren en een geconsolideerde factuur kunt weer geven door hetzelfde label op meerdere resources en resource groepen toe te passen. Deze zijn optioneel en kunnen worden toegepast nadat het maken van een opslag account is gemaakt.

#### <a name="review--create"></a>Controleren en maken
De laatste stap voor het maken van het opslag account is door de knop **maken** te selecteren op het tabblad **controleren + maken** . Deze knop is niet beschikbaar als alle vereiste velden voor een opslag account niet zijn gevuld.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Als u een opslag account wilt maken met behulp van Power shell, zullen we de `New-AzStorageAccount` cmdlet gebruiken. Deze cmdlet heeft veel opties. alleen de vereiste opties worden weer gegeven. Raadpleeg de [ `New-AzStorageAccount` cmdlet-documentatie](/powershell/module/az.storage/new-azstorageaccount)voor meer informatie over geavanceerde opties.

Om het maken van het opslag account en de volgende bestands share te vereenvoudigen, worden in variabelen verschillende para meters opgeslagen. U kunt de variabele inhoud vervangen door de waarden die u wilt, maar houd er rekening mee dat de naam van het opslag account globaal uniek moet zijn.

```powershell
$resourceGroupName = "myResourceGroup"
$storageAccountName = "mystorageacct$(Get-Random)"
$region = "westus2"
```

Als u een opslag account wilt maken dat standaard Azure-bestands shares kan opslaan, wordt de volgende opdracht gebruikt. De `-SkuName` para meter is gekoppeld aan het type redundantie gewenst. Als u een geografisch redundant of geo-zone-redundant opslag account wilt, moet u ook de `-EnableLargeFileShare` para meter verwijderen.

```powershell
$storAcct = New-AzStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $storageAccountName `
    -SkuName Standard_LRS `
    -Location $region `
    -Kind StorageV2 `
    -EnableLargeFileShare
```

Als u een opslag account wilt maken waarmee Premium Azure-bestands shares kunnen worden opgeslagen, wordt de volgende opdracht gebruikt. Houd er rekening mee dat de `-SkuName` para meter is gewijzigd om zowel `Premium` het gewenste redundantie niveau van lokaal redundante () op te geven `LRS` . De `-Kind` para meter is `FileStorage` in plaats van `StorageV2` omdat Premium-bestands shares moeten worden gemaakt in een FileStorage-opslag account in plaats van een GPv2-opslag account.

```powershell
$storAcct = New-AzStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $storageAccountName `
    -SkuName Premium_LRS `
    -Location $region `
    -Kind FileStorage 
```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)
Als u een opslag account wilt maken met behulp van de Azure CLI, gebruikt u de opdracht AZ Storage account create. Deze opdracht heeft veel opties; alleen de vereiste opties worden weer gegeven. Zie de [ `az storage account create` opdracht documentatie](/cli/azure/storage/account)voor meer informatie over de geavanceerde opties.

Om het maken van het opslag account en de volgende bestands share te vereenvoudigen, worden in variabelen verschillende para meters opgeslagen. U kunt de variabele inhoud vervangen door de waarden die u wilt, maar houd er rekening mee dat de naam van het opslag account globaal uniek moet zijn.

```azurecli
resourceGroupName="myResourceGroup"
storageAccountName="mystorageacct$RANDOM"
region="westus2"
```

Als u een opslag account wilt maken dat standaard Azure-bestands shares kan opslaan, wordt de volgende opdracht gebruikt. De `--sku` para meter is gekoppeld aan het type redundantie gewenst. Als u een geografisch redundant of geo-zone-redundant opslag account wilt, moet u ook de `--enable-large-file-share` para meter verwijderen.

```azurecli
az storage account create \
    --resource-group $resourceGroupName \
    --name $storageAccountName \
    --kind StorageV2 \
    --sku Standard_ZRS \
    --enable-large-file-share \
    --output none
```

Als u een opslag account wilt maken waarmee Premium Azure-bestands shares kunnen worden opgeslagen, wordt de volgende opdracht gebruikt. Houd er rekening mee dat de `--sku` para meter is gewijzigd om zowel `Premium` het gewenste redundantie niveau van lokaal redundante () op te geven `LRS` . De `--kind` para meter is `FileStorage` in plaats van `StorageV2` omdat Premium-bestands shares moeten worden gemaakt in een FileStorage-opslag account in plaats van een GPv2-opslag account.

```azurecli
az storage account create \
    --resource-group $resourceGroupName \
    --name $storageAccountName \
    --kind FileStorage \
    --sku Premium_LRS \
    --output none
```

---

## <a name="create-file-share"></a>Bestandsshare maken
Wanneer u uw opslag account hebt gemaakt, hoeft u alleen nog maar uw bestands share te maken. Dit proces is grotendeels hetzelfde, ongeacht of u een Premium-bestands share of een standaard bestands share gebruikt. U moet rekening houden met de volgende verschillen.

Standaard bestands shares kunnen worden geïmplementeerd in een van de volgende standaard lagen: trans actie geoptimaliseerd (standaard), Hot of cool. Dit is een per bestands share-laag die niet wordt beïnvloed door de **BLOB Access-laag** van het opslag account (deze eigenschap is alleen gekoppeld aan Azure Blob Storage, maar heeft geen betrekking op Azure files). U kunt de laag van de share op elk gewenst moment wijzigen nadat deze is geïmplementeerd. Premium-bestands shares kunnen niet rechtstreeks worden geconverteerd naar een Standard-laag.

> [!Important]  
> U kunt bestandsshares verplaatsen tussen lagen binnen GPv2-opslagaccounttypen (geoptimaliseerd voor transacties, dynamisch en statisch). Als u shares tussen lagen verplaatst, worden er kosten in rekening gebracht: als u van een dynamischere laag verplaatst naar een statischere laag, worden er schrijfkosten in rekeningen gebracht in de statische laag voor elk bestand in de share, terwijl er van de statischere laag naar de dynamischere laag leeskosten in rekening worden gebracht voor de statische laag voor elk bestand in de share.

De eigenschap **quota** houdt in dat iets verschilt tussen Premium-en standaard bestands shares:

- Voor standaard bestands shares is dit een bovengrens van de Azure-bestands share, waardoor eind gebruikers deze niet kunnen starten. Als er geen quotum is opgegeven, kan de standaard bestands share tot 100 TiB (of 5 TiB als de eigenschap grote bestands shares niet is ingesteld voor een opslag account).

- Voor Premium-bestands shares betekent quota de **ingerichte grootte**. De ingerichte grootte is het bedrag waarvoor u wordt gefactureerd, ongeacht het werkelijke gebruik. Zie [Premium-bestands shares inrichten](understanding-billing.md#provisioned-model)voor meer informatie over het plannen van een Premium-bestands share.

# <a name="portal"></a>[Portal](#tab/azure-portal)
Als u uw opslag account zojuist hebt gemaakt, kunt u ernaar navigeren vanuit het scherm implementatie door **naar resource te gaan**. Selecteer in het opslag account de tegel gelabelde **Bestands shares** (u kunt ook naar **Bestands shares** gaan via de inhouds opgave van het opslag account).

![Een scherm opname van de tegel bestands shares](media/storage-how-to-create-file-share/create-file-share-1.png)

In de vermelding van de bestands share ziet u alle bestands shares die u eerder hebt gemaakt in dit opslag account. een lege tabel als er nog geen bestands shares zijn gemaakt. Selecteer **+ Bestands share** om een nieuwe bestands share te maken.

De Blade nieuwe bestands share moet op het scherm worden weer gegeven. Vul de velden op de Blade nieuwe bestands share in om een bestands share te maken:

- **Naam**: de naam van de bestands share die u wilt maken.
- **Quotum**: het quotum van de bestands share voor standaard bestands shares; de ingerichte grootte van de bestands share voor Premium-bestands shares.
- **Lagen**: de geselecteerde laag voor een bestands share. Dit veld is alleen beschikbaar in een **opslag account voor algemeen gebruik (GPv2)**. U kunt kiezen voor een trans actie geoptimaliseerd, Hot of cool. De laag van de share kan op elk gewenst moment worden gewijzigd. We raden u aan om tijdens een migratie de nieuwste laag te kiezen, om transactie kosten te minimaliseren en vervolgens naar een lagere laag te scha kelen wanneer de migratie is voltooid.

Selecteer **maken** om de nieuwe share te volt ooien.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
U kunt een Azure-bestands share maken met de [`New-AzRmStorageShare`](/powershell/module/az.storage/New-AzRmStorageShare) cmdlet. Bij de volgende Power shell-opdrachten wordt ervan uitgegaan dat u de variabelen `$resourceGroupName` en `$storageAccountName` zoals hierboven gedefinieerd hebt ingesteld in het gedeelte een opslag account maken met Azure PowerShell. 

In het volgende voor beeld ziet u hoe u een bestands share met een expliciete laag maakt met behulp van de `-AccessTier` para meter. Hiervoor moet de preview AZ. Storage-module worden gebruikt, zoals aangegeven in het voor beeld. Als er geen laag is opgegeven, hetzij omdat u de module GA AZ. Storage gebruikt, of omdat u deze opdracht niet hebt toegevoegd, wordt de standaard-laag voor standaard bestands shares geoptimaliseerd voor trans acties.

> [!Important]  
> Voor Premium-bestands shares `-QuotaGiB` verwijst de para meter naar de ingerichte grootte van de bestands share. De ingerichte grootte van de bestands share is de hoeveelheid die wordt gefactureerd, ongeacht het gebruik. Standaard bestands shares worden gefactureerd op basis van het gebruik in plaats van de ingerichte grootte.

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
U kunt een Azure-bestands share maken met behulp van de [`az storage share-rm create`](/cli/azure/storage/share-rm?preserve-view=true&view=azure-cli-latest#az_storage_share_rm_create) opdracht. Bij de volgende Azure CLI-opdrachten wordt ervan uitgegaan dat u de variabelen `$resourceGroupName` en `$storageAccountName` zoals hierboven gedefinieerd hebt ingesteld in de sectie een opslag account maken met Azure cli.

> [!Important]  
> Voor Premium-bestands shares `--quota` verwijst de para meter naar de ingerichte grootte van de bestands share. De ingerichte grootte van de bestands share is de hoeveelheid die wordt gefactureerd, ongeacht het gebruik. Standaard bestands shares worden gefactureerd op basis van het gebruik in plaats van de ingerichte grootte.

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

### <a name="changing-the-tier-of-an-azure-file-share"></a>De laag van een Azure-bestands share wijzigen
Bestands shares die zijn geïmplementeerd in het **opslag account voor algemeen gebruik v2 (GPv2)** , kunnen de trans actie geoptimaliseerd, warm of koud hebben. U kunt de laag van de Azure-bestands share op elk gewenst moment wijzigen, afhankelijk van de transactie kosten zoals hierboven wordt beschreven.

# <a name="portal"></a>[Portal](#tab/azure-portal)
Selecteer op de pagina hoofd opslag account de optie **Bestands shares**  de tegel **Bestands shares** selecteren (u kunt ook naar **Bestands shares** gaan via de inhouds opgave van het opslag account).

![Een scherm opname van de tegel bestands shares](media/storage-how-to-create-file-share/create-file-share-1.png)

Selecteer in de tabel lijst met bestands shares de bestands share waarvoor u de laag wilt wijzigen. Op de pagina overzicht van bestands shares selecteert u **laag wijzigen** in het menu.

![Een scherm afbeelding van de overzichts pagina van de bestands share waarop de knop laag wijzigen is gemarkeerd](media/storage-how-to-create-file-share/change-tier-0.png)

Selecteer in het dialoog venster dat wordt gegenereerd de gewenste laag: trans actie geoptimaliseerd, Hot of cool.

![Een scherm afbeelding van het dialoog venster laag wijzigen](media/storage-how-to-create-file-share/change-tier-1.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
De volgende Power shell-cmdlet gaat ervan uit dat u `$resourceGroupName` de `$storageAccountName` variabelen,,, `$shareName` zoals beschreven in de vorige secties van dit document hebt ingesteld.

```PowerShell
Update-AzRmStorageShare `
    -ResourceGroupName $resourceGroupName `
    -StorageAccountName $storageAccountName `
    -Name $shareName `
    -AccessTier Cool
```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)
De volgende Azure CLI-opdracht gaat ervan uit dat u de `$resourceGroupName` -, `$storageAccountName` -en-variabelen hebt ingesteld `$shareName` zoals beschreven in de vorige secties van dit document.

```azurecli
az storage share-rm update \
    --resource-group $resourceGroupName \
    --storage-account $storageAccountName \
    --name $shareName \
    --access-tier "Cool"
```

---

## <a name="next-steps"></a>Volgende stappen
- [Plan voor een implementatie van Azure files](storage-files-planning.md) of [plan een implementatie van Azure file sync](storage-sync-files-planning.md). 
- [Overzicht van netwerken](storage-files-networking-overview.md).
- Verbinding maken en koppelen van een bestands share in [Windows](storage-how-to-use-files-windows.md), [macOS](storage-how-to-use-files-mac.md)en [Linux](storage-how-to-use-files-linux.md).