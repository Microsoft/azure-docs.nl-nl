---
title: BLOB-gegevens worden opnieuw gehydrateerd op basis van de opslaglaag
description: U kunt de blobs uit archief opslag opnieuw gebruiken zodat u toegang hebt tot de BLOB-gegevens. Kopieer een gearchiveerde BLOB naar een online-laag.
services: storage
author: mhopkins-msft
ms.author: mhopkins
ms.date: 03/11/2021
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.reviewer: hux
ms.openlocfilehash: 2f0ddca9cbd7d85909b1d86e68b92fa1d847476d
ms.sourcegitcommit: 94c3c1be6bc17403adbb2bab6bbaf4a717a66009
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/12/2021
ms.locfileid: "103225078"
---
# <a name="rehydrate-blob-data-from-the-archive-tier"></a>BLOB-gegevens worden opnieuw gehydrateerd op basis van de opslaglaag

Terwijl een BLOB zich in de Access-laag Archive bevindt, wordt deze als offline beschouwd en kan deze niet worden gelezen of gewijzigd. De meta gegevens van de BLOB blijven online en beschikbaar, zodat u de BLOB en de bijbehorende eigenschappen kunt weer geven. Het lezen en wijzigen van BLOB-gegevens is alleen beschikbaar voor online lagen, zoals warme of koud. Er zijn twee opties voor het ophalen en openen van gegevens die zijn opgeslagen in de toegangs laag voor het archief.

1. [Een gearchiveerde BLOB opnieuw gehydrateerd naar een online-laag](#rehydrate-an-archived-blob-to-an-online-tier) -een archief-BLOB opnieuw laten worden gehydrateerd op warme of koud door de laag te wijzigen met de bewerking voor het instellen van de [BLOB-laag](/rest/api/storageservices/set-blob-tier) .
2. [Een gearchiveerde BLOB naar een online-laag kopiëren](#copy-an-archived-blob-to-an-online-tier) : Maak een nieuwe kopie van een archief-blob met behulp van de [Kopieer bewerking BLOB](/rest/api/storageservices/copy-blob) . Geef een andere blobnaam en een bestemmings laag op met de naam Hot of cool.

 Zie [Azure Blob Storage: warme, cool en archief toegangs lagen](storage-blob-storage-tiers.md)voor meer informatie over lagen.

## <a name="rehydrate-an-archived-blob-to-an-online-tier"></a>Een gearchiveerde BLOB opnieuw naar een online-laag gehydrateerd

[!INCLUDE [storage-blob-rehydration](../../../includes/storage-blob-rehydrate-include.md)]

### <a name="lifecycle-management"></a>Levenscyclus beheer

Reactiveren een BLOB heeft geen invloed op de `Last-Modified` tijd. Met de functie [levenscyclus beheer](storage-lifecycle-management-concepts.md) kunt u een scenario maken waarbij een BLOB opnieuw wordt gehydrateerd. vervolgens verplaatst een levenscyclus beheer beleid de BLOB terug naar Archive, omdat de `Last-Modified` tijd buiten de drempel waarde voor het beleid ligt. Om dit scenario te voor komen, gebruikt u de methode *[een gearchiveerde BLOB kopiëren naar een online-laag](#copy-an-archived-blob-to-an-online-tier)* . Met de methode Copy wordt een nieuw exemplaar van de BLOB gemaakt met een bijgewerkte `Last-Modified` tijd en wordt het levenscyclus beheer beleid niet geactiveerd.

## <a name="monitor-rehydration-progress"></a>Voortgang van rehydratatie bewaken

Gebruik tijdens rehydratatie de bewerking BLOB eigenschappen ophalen om het kenmerk **Archive status** te controleren en te bevestigen wanneer de laag wijziging is voltooid. De status is "rehydrate-pending-to-hot" of "rehydrate-pending-to-cool", afhankelijk van de doellaag. Na voltooiing wordt de eigenschap archief status verwijderd en de BLOB-eigenschap van de **Access-laag** weerspiegelt de nieuwe hot of cool-laag.

## <a name="copy-an-archived-blob-to-an-online-tier"></a>Een gearchiveerde blob naar een online laag kopiëren

Als u de archief-BLOB niet opnieuw wilt laten worden gehydrateerd, kunt u ervoor kiezen om een [Kopieer-BLOB](/rest/api/storageservices/copy-blob) bewerking uit te voeren. De oorspronkelijke BLOB blijft ongewijzigd in archief terwijl er een nieuwe BLOB wordt gemaakt in de online hot of cool-laag, zodat u kunt werken. In de bewerking **BLOB kopiëren** kunt u ook de optionele *x-MS-autohydrat-Priority-* eigenschap instellen op Standard of High om de prioriteit op te geven waarop u de BLOB-kopie wilt maken.

Het kopiëren van een BLOB uit het archief kan uren duren, afhankelijk van de geselecteerde opnieuw te maken prioriteit. Achter de schermen leest de bewerking **BLOB kopiëren** de bron-blob van het archief om een nieuwe online-Blob in de geselecteerde doellaag te maken. De nieuwe blob is mogelijk zichtbaar wanneer u blobs vermeldte, maar de gegevens zijn pas beschikbaar als de Lees bewerking van de blob van het bron archief is voltooid en de gegevens naar de nieuwe online-doel-BLOB zijn geschreven. De nieuwe blob is een onafhankelijke kopie en een wijziging of verwijdering hiervan heeft geen invloed op de bron archief-blob.

> [!IMPORTANT]
> Verwijder de bron-BLOB pas als de Kopieer bewerking is voltooid op het doel. Als de bron-BLOB wordt verwijderd, wordt de doel-BLOB mogelijk niet volledig gekopieerd en is deze leeg. U kunt de *x-MS-Copy-status* controleren om de status van de Kopieer bewerking te bepalen.

Archief-blobs kunnen alleen worden gekopieerd naar online doel lagen binnen hetzelfde opslag account. Het kopiëren van een archief-BLOB naar een andere archief-BLOB wordt niet ondersteund. In de volgende tabel ziet u de mogelijkheden van een **Kopieer-BLOB** -bewerking.

|                                           | **Bron van warme laag**   | **Bron van de cool-laag** | **Bron van Archive-laag**    |
| ----------------------------------------- | --------------------- | -------------------- | ------------------- |
| **Doel van de warme laag**                  | Ondersteund             | Ondersteund            | Ondersteund binnen hetzelfde account; opnieuw gehydrateerd in behandeling               |
| **Doel van de cool-laag**                 | Ondersteund             | Ondersteund            | Ondersteund binnen hetzelfde account; opnieuw gehydrateerd in behandeling               |
| **Doel van de archief laag**              | Ondersteund             | Ondersteund            | Niet ondersteund         |

## <a name="pricing-and-billing"></a>Prijzen en facturering

Reactiveren-blobs uit het archief in warme of coole lagen worden in rekening gebracht als Lees bewerkingen en gegevens ophalen. Het gebruik van hoge prioriteit heeft hogere kosten voor bewerkingen en het ophalen van gegevens ten opzichte van de standaard prioriteit. Rehydratatie met hoge prioriteit worden weer gegeven als een afzonderlijk regel item op uw factuur. Als een aanvraag met een hoge prioriteit voor het retour neren van een archief-blob van een paar gigabytes meer dan vijf uur duurt, wordt niet het ophaal tarief met hoge prioriteit in rekening gebracht. Standaard tarieven voor het ophalen van zijn echter nog steeds van toepassing omdat de rehydratatie prioriteit heeft gegeven aan andere aanvragen.

Het kopiëren van blobs van Archive in warme of coole lagen wordt in rekening gebracht als Lees bewerkingen en ophalen van gegevens. Voor het maken van de nieuwe BLOB-kopie wordt een schrijf bewerking in rekening gebracht. Kosten voor vroegtijdig verwijderen zijn niet van toepassing wanneer u naar een online-BLOB kopieert, omdat de bron-BLOB ongewijzigd blijft in de laag van het archief. De kosten voor het ophalen van hoge prioriteit zijn van toepassing indien geselecteerd.

Blobs in de archief laag moeten mini maal 180 dagen worden opgeslagen. Als u gearchiveerde blobs verwijdert of reactiveren, worden de kosten 180 voor vroegtijdige verwijdering in rekening gebracht.

> [!NOTE]
> Zie [Azure Storage prijzen](https://azure.microsoft.com/pricing/details/storage/blobs/)voor meer informatie over de prijzen voor blok-blobs en gegevens rehydratatie. Zie [prijs informatie voor gegevens overdracht](https://azure.microsoft.com/pricing/details/data-transfers/)voor meer informatie over de kosten voor uitgaande gegevens overdracht.

## <a name="quickstart-scenarios"></a>Snelstartscenario's

### <a name="rehydrate-an-archive-blob-to-an-online-tier"></a>Een archief-BLOB naar een online-laag opnieuw gehydrateerd
# <a name="portal"></a>[Portal](#tab/azure-portal)
1. Meld u aan bij de [Azure-portal](https://portal.azure.com).

1. Zoek in het Azure Portal **alle resources** en selecteer deze.

1. Selecteer uw opslagaccount.

1. Selecteer uw container en selecteer vervolgens uw blob.

1. Selecteer in de **BLOB**-eigenschappen **laag wijzigen**.

1. Selecteer de laag **Hot** of **cool** Access. 

1. Selecteer een opnieuw gehydrateerde prioriteit van **standaard** of **hoog**.

1. Selecteer onder **Opslaan** onder.

![De status van de ](media/storage-tiers/blob-access-tier.png)
 rehydratie controle van de laag van het opslag account wijzigen ![](media/storage-tiers/rehydrate-status.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Het volgende Power shell-script kan worden gebruikt om de BLOB-laag van een archief-BLOB te wijzigen. De `$rgName` variabele moet worden geïnitialiseerd met de naam van de resource groep. De `$accountName` variabele moet worden geïnitialiseerd met de naam van uw opslag account. De `$containerName` variabele moet worden geïnitialiseerd met de container naam. De `$blobName` variabele moet worden geïnitialiseerd met de naam van de blob. 
```powershell
#Initialize the following with your resource group, storage account, container, and blob names
$rgName = ""
$accountName = ""
$containerName = ""
$blobName = ""

#Select the storage account and get the context
$storageAccount =Get-AzStorageAccount -ResourceGroupName $rgName -Name $accountName
$ctx = $storageAccount.Context

#Select the blob from a container
$blob = Get-AzStorageBlob -Container $containerName -Blob $blobName -Context $ctx

#Change the blob’s access tier to Hot using Standard priority rehydrate
$blob.ICloudBlob.SetStandardBlobTier("Hot", "Standard")
```
---

### <a name="copy-an-archive-blob-to-a-new-blob-with-an-online-tier"></a>Een archief-BLOB kopiëren naar een nieuwe blob met een online-laag
Het volgende Power shell-script kan worden gebruikt om een Archive-BLOB te kopiëren naar een nieuwe BLOB binnen hetzelfde opslag account. De `$rgName` variabele moet worden geïnitialiseerd met de naam van de resource groep. De `$accountName` variabele moet worden geïnitialiseerd met de naam van uw opslag account. De `$srcContainerName` `$destContainerName` variabelen en moeten worden geïnitialiseerd met de container namen. De `$srcBlobName` `$destBlobName` variabelen en moeten worden geïnitialiseerd met de namen van de blobs. 
```powershell
#Initialize the following with your resource group, storage account, container, and blob names
$rgName = ""
$accountName = ""
$srcContainerName = ""
$destContainerName = ""
$srcBlobName = ""
$destBlobName = ""

#Select the storage account and get the context
$storageAccount =Get-AzStorageAccount -ResourceGroupName $rgName -Name $accountName
$ctx = $storageAccount.Context

#Copy source blob to a new destination blob with access tier hot using standard rehydrate priority
Start-AzStorageBlobCopy -SrcContainer $srcContainerName -SrcBlob $srcBlobName -DestContainer $destContainerName -DestBlob $destBlobName -StandardBlobTier Hot -RehydratePriority Standard -Context $ctx
```

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over Blob Storage lagen](storage-blob-storage-tiers.md)
* [Controleer de prijzen voor warme, koude en archief in Blob Storage en GPv2-accounts per regio](https://azure.microsoft.com/pricing/details/storage/)
* [De levenscyclus van Azure Blob-opslag beheren](storage-lifecycle-management-concepts.md)
* [Prijzen voor gegevensoverdracht controleren](https://azure.microsoft.com/pricing/details/data-transfers/)