---
title: Toegangs lagen voor Azure Blob Storage-hot, cool en Archive
description: Meer informatie over de toegangs lagen hot, cool en Archive voor Azure Blob Storage. Bekijk opslag accounts die ondersteuning bieden voor lagen. Vergelijkings opties voor blok-Blob-opslag.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 10/29/2020
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.reviewer: clausjor
ms.openlocfilehash: 87106cce018a2b2663de2a9abbb43b31ab58c125
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/24/2020
ms.locfileid: "96007321"
---
# <a name="access-tiers-for-azure-blob-storage---hot-cool-and-archive"></a>Toegangs lagen voor Azure Blob Storage-hot, cool en Archive

Azure Storage biedt verschillende toegangs lagen, waarmee u gegevens van blob-objecten op de meest rendabele manier kunt opslaan. De beschikbare toegangslagen zijn onder andere:

- **Dynamisch** geoptimaliseerd voor het opslaan van gegevens die regel matig worden geopend.
- **Koud** geoptimaliseerd voor het opslaan van gegevens die niet regel matig worden geopend en die gedurende ten minste 30 dagen worden opgeslagen.
- **Archief** -geoptimaliseerd voor het opslaan van gegevens die zelden worden gebruikt en die gedurende ten minste 180 dagen worden opgeslagen met flexibele latentie vereisten (op volg orde van uur).

De volgende overwegingen zijn van toepassing op de verschillende toegangslagen:

- Alleen de statische- en dynamische-toegangslagen kunnen op accountniveau worden ingesteld. De archieftoegangslaag is niet beschikbaar op accountniveau.
- Dynamische- en statische-toegangslagen en archieftoegangslagen kunnen tijdens of na het uploaden op blob-niveau worden ingesteld.
- Gegevens in de statische-toegangslaag verdragen een iets lagere beschikbaarheid, maar vereisen wel een hoge duurzaamheid, een ophaallatentie en doorvoerkenmerken die vergelijkbaar zijn met dynamische gegevens. Voor statische gegevens zijn een iets lagere SLA (Service Level Agreement) voor beschikbaarheid en hogere toegangskosten in vergelijking met dynamische gegevens acceptabel bij lagere opslagkosten.
- Bij archiefopslag worden gegevens offline opgeslagen. De opslagkosten hiervan zijn het laagst, maar de kosten voor hydratatie van gegevens en toegang tot gegevens zijn het hoogst.

Gegevens die zijn opgeslagen in de Cloud, groeien in een exponentieel tempo. Voor een effectief beheer van de kosten voor uw groeiende opslagbehoeften is het vanwege kostenoptimalisatie een goed idee om de gegevens te ordenen op basis van kenmerken als toegangsfrequentie en geplande bewaarperiode. Gegevens die in de cloud zijn opgeslagen, kunnen verschillen vertonen naar gelang de manier waarop ze gedurende hun levensduur worden gegenereerd, verwerkt en geopend. Sommige gegevens worden tijdens hun hele levensduur actief geopend en gewijzigd. Andere gegevens worden in het begin van hun levensduur regelmatig geopend, terwijl dit naarmate de tijd verstrijkt, aanzienlijk minder vaak gebeurt. Sommige gegevens verblijven inactief in de cloud en worden zelden of nooit geopend nadat ze zijn opgeslagen.

Elk van deze scenario's voor gegevens toegang is voor deel van een andere toegangs laag die is geoptimaliseerd voor een specifiek toegangs patroon. Met de toegangs lagen hot, cool en Archive kunnen Azure Blob Storage deze behoefte voor gedifferentieerde toegangs lagen met afzonderlijke prijs modellen.

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="storage-accounts-that-support-tiering"></a>Storage-accounts die ondersteuning bieden voor opslaglagen

De gegevenslaag van object opslag tussen hot, cool en Archive wordt alleen ondersteund in Blob Storage-en Algemeen v2-accounts (GPv2). Algemeen v1 (GPv1)-accounts bieden geen ondersteuning voor lagen. Klanten kunnen hun bestaande GPv1-of Blob Storage accounts eenvoudig converteren naar GPv2-accounts via de Azure Portal. GPv2 biedt nieuwe prijzen en functies voor blobs, bestanden en wacht rijen. Sommige functies en prijs delen worden alleen aangeboden in GPv2-accounts. Evalueer het gebruik van GPv2-accounts na een grondige beoordeling van de prijzen. Sommige werk belastingen kunnen duurder zijn voor GPv2 dan GPv1. Zie [Overzicht van Azure-opslagaccount](../common/storage-account-overview.md) voor meer informatie.

Blob Storage-en GPv2-accounts bieden het kenmerk **toegangs** niveau op account niveau. Met dit kenmerk kunt u de standaardlaag voor toegang voor een BLOB opgeven die niet expliciet is ingesteld op object niveau. Voor objecten met de laag die op object niveau is ingesteld, is de laag van de account niet van toepassing. De archief laag kan alleen op object niveau worden toegepast. U kunt op elk gewenst moment scha kelen tussen deze toegangs lagen.

## <a name="hot-access-tier"></a>Dynamische-toegangslaag

De dynamische-toegangslaag heeft hogere opslagkosten dan de statische-toegangslaag en de archieftoegangslaag, maar de laagste toegangskosten. Voorbeelden van gebruiksscenario's voor de dynamische-toegangslaag:

- Gegevens die actief worden gebruikt of waarvan wordt verwacht dat ze regelmatig worden geopend (lees- en schrijfbewerkingen).
- Gegevens die worden voorbereid voor verwerking en uiteindelijk migratie naar de statische-toegangslaag.

## <a name="cool-access-tier"></a>Statische-toegangslaag

De statische-toegangslaag heeft lagere opslagkosten en hogere toegangskosten in vergelijking met dynamische opslag. Deze laag is bedoeld voor gegevens die ten minste dertig dagen in de Cold Storage verblijven. Voorbeelden van gebruiksscenario's voor de statische-toegangslaag:

- Gegevenssets waarvan voor de korte termijn een back-up is gemaakt en die na een noodgeval zijn hersteld.
- Oudere media-inhoud die niet meer regelmatig wordt bekeken, maar onmiddellijk beschikbaar moet zijn wanneer deze wordt geopend.
- Grote gegevenssets die voordelig moeten worden opgeslagen, terwijl er meer gegevens worden verzameld voor toekomstige verwerking. (*Bijvoorbeeld*: langetermijnopslag van wetenschappelijke gegevens, onbewerkte telemetriegegevens van een productiefaciliteit)

## <a name="archive-access-tier"></a>Archieftoegangslaag

De toegangs laag voor het archief heeft de laagste opslag kosten. Maar het heeft een hogere kosten voor het ophalen van gegevens vergeleken met de warme en coole lagen. Gegevens moeten gedurende ten minste 180 dagen in de archief laag blijven of onderhevig zijn aan de kosten voor een vroege verwijdering. Het ophalen van gegevens in de archief laag kan enkele uren duren, afhankelijk van de prioriteit van de rehydratatie. Voor kleine objecten kan een met een hoge prioriteit gehydrateerd object binnen één uur worden opgehaald uit het archief. Zie [BLOB-gegevens opnieuw inbreken van de archief laag](storage-blob-rehydration.md) voor meer informatie.

Hoewel een BLOB zich in archief opslag bevindt, zijn de BLOB-gegevens offline en kunnen ze niet worden gelezen, overschreven of gewijzigd. Als u een BLOB in Archive wilt lezen of downloaden, moet u deze eerst opnieuw laten worden gehydrateerd tot een online-laag. U kunt geen moment opnamen maken van een BLOB in archief opslag. De BLOB-meta gegevens blijven echter online en beschikbaar, zodat u de blob, de eigenschappen, de meta gegevens en de index Tags van de BLOB kunt weer geven. Het is niet toegestaan om de BLOB-meta gegevens in te stellen of te wijzigen in archief. u kunt echter de index Tags van de BLOB instellen en wijzigen. Voor blobs in archief zijn de enige geldige bewerkingen GetBlobProperties, GetBlobMetadata, SetBlobTags, GetBlobTags, FindBlobsByTags, ListBlobs, SetBlobTier, CopyBlob en DeleteBlob.

Voor beelden van gebruiks scenario's voor de Access-laag voor archiveren zijn:

- Langetermijnback-up, secundaire back-up en gegevenssets voor archivering
- Oorspronkelijke (onbewerkte) gegevens die moeten worden bewaard, zelfs nadat deze zijn verwerkt tot hun uiteindelijke, bruikbare vorm.
- Compliance- en archiveringsgegevens die gedurende lange tijd moeten worden opgeslagen en bijna nooit worden geopend.

> [!NOTE]
> De Archive-laag wordt momenteel niet ondersteund voor ZRS-, GZRS-of RA-GZRS-accounts.

## <a name="account-level-tiering"></a>Lagen op account niveau

Blobs in alle drie de toegangs lagen kunnen naast elkaar worden gebruikt in hetzelfde account. Een blob die geen expliciet toegewezen laag heeft, leidt de laag af van de instelling van de toegangs laag voor het account. Als de toegangs laag afkomstig is van het account, ziet u de eigenschap voor de **uitstelde** blob van de toegangs laag die is ingesteld op ' True ', en de BLOB-eigenschap van de **Access-laag** overeenkomt met de account-laag. In de Azure Portal wordt de eigenschap voor de _toegang tot de laag_ weer gegeven met de BLOB-toegangs laag als **Hot (uitgesteld)** of **koud (uitgesteld)**.

Het wijzigen van de toegangs laag voor het account is van toepassing op alle objecten in de _toegangs laag_ , die zijn opgeslagen in het account waarvoor geen expliciete laag is ingesteld. Als u de laag van dynamisch naar koud wisselt, wordt u in rekening gebracht voor schrijf bewerkingen (per 10.000) voor alle blobs zonder een set-laag in GPv2-accounts. Er worden geen kosten in rekening gebracht voor deze wijziging in Blob Storage accounts. Er worden kosten in rekening gebracht voor zowel lees bewerkingen (per 10.000) als voor het ophalen van gegevens (per GB) als u van koud naar warm schakelt Blob Storage-of GPv2-accounts.

## <a name="blob-level-tiering"></a>Laaginstelling op blobniveau

Met lagen op blobniveau kunt u gegevens uploaden naar de toegangs laag van uw keuze met behulp van de bewerkingen [put-BLOB](/rest/api/storageservices/put-blob) of [put list](/rest/api/storageservices/put-block-list) en wijzigt u de laag van uw gegevens op object niveau met behulp van de functie voor het instellen van een [BLOB-laag](/rest/api/storageservices/set-blob-tier) of [levenscyclus beheer](#blob-lifecycle-management) . U kunt gegevens uploaden naar uw vereiste Access-laag en de BLOB Access-laag eenvoudig wijzigen onder de dynamische, koude of archief lagen als gebruiks patronen veranderen, zonder dat u gegevens tussen accounts hoeft te verplaatsen. Alle laag wijzigings aanvragen gebeuren onmiddellijk en laag wijzigingen tussen warme en koelen zijn direct. Het reactiveren van een BLOB uit het archief kan echter enkele uren duren.

Het tijdstip waarop de laatste wijziging aan de blob-laag heeft plaatsgevonden, wordt weergegeven via de blob-eigenschap **Access Tier Change Time**. Bij het overschrijven van een BLOB in de warme of koude laag, neemt de zojuist gemaakte BLOB de laag over van de blob die is overschreven, tenzij de nieuwe BLOB-toegangs laag expliciet is ingesteld bij het maken. Als een BLOB zich in de laag Archive bevindt, kan deze niet worden overschreven, dus het uploaden van dezelfde blob is niet toegestaan in dit scenario. 

> [!NOTE]
> Archiefopslag en laaginstelling op blobniveau ondersteunen alleen blok-blobs.

### <a name="blob-lifecycle-management"></a>Beheer van de BLOB levenscyclus

Blob Storage levenscyclus beheer biedt een uitgebreid beleid op basis van regels dat u kunt gebruiken om uw gegevens te overzetten naar de laag van de beste toegang en om gegevens aan het einde van de levens cyclus te laten verlopen. Zie [de Azure Blob Storage levenscyclus beheren](storage-lifecycle-management-concepts.md) voor meer informatie.  

> [!NOTE]
> Gegevens die zijn opgeslagen in een blok-Blob-opslag account (Premium-prestaties), kunnen momenteel niet worden getierd naar warme, koude of archief met behulp van [set BLOB](/rest/api/storageservices/set-blob-tier) of Azure Blob Storage levenscyclus beheer.
> Als u gegevens wilt verplaatsen, moet u blobs synchroon kopiëren van het blok-Blob-opslag account naar de hete Access-laag in een ander account met behulp [van de put-block from URL API](/rest/api/storageservices/put-block-from-url) of een versie van AzCopy die deze API ondersteunt.
> Met de put-functie *van URL* -API worden gegevens synchroon gekopieerd op de server, wat betekent dat de aanroep alleen wordt voltooid wanneer alle gegevens van de oorspronkelijke server locatie naar de doel locatie worden verplaatst.

### <a name="blob-level-tiering-billing"></a>Facturering van laaginstelling op blobniveau

Wanneer een BLOB wordt geüpload of verplaatst naar de warme, koude of archief laag, wordt het bijbehorende tarief direct bij het wijzigen van de laag in rekening gebracht.

Wanneer een BLOB wordt verplaatst naar een koele laag (Hot->cool, Hot->Archive of koele->Archive), wordt de bewerking gefactureerd als een schrijf bewerking naar de doellaag, waar de kosten voor de schrijf bewerking (per 10.000) en het schrijven van gegevens (per GB) van de doellaag van toepassing zijn.

Wanneer een BLOB wordt verplaatst naar een warmere laag (archief->cool, Archive->Hot of cool->Hot), wordt de bewerking gefactureerd als een lees bewerking vanuit de bronlaag, waarbij de kosten voor lees bewerkingen (per 10.000) en het ophalen van gegevens (per GB) van de bronlaag van toepassing zijn. Kosten voor vroegtijdige verwijdering van een blob die is verplaatst uit de opslaglaag Cool of Archive kunnen ook van toepassing zijn. Voor [reactiveren gegevens uit het archief](storage-blob-rehydration.md) worden tijd en gegevens in rekening gebracht totdat de gegevens online worden teruggezet en de BLOB-laag wordt gewijzigd in warme of koud. De volgende tabel bevat een overzicht van de manier waarop laag wijzigingen worden gefactureerd:

| | **Schrijf kosten (bewerking + toegang)** | **Lees kosten (bewerking + toegang)**
| ---- | ----- | ----- |
| **SetBlobTier richting** | warm >cool,<br> Archief met Hot >,<br> Archief met koele > | Archief->cool,<br> Archief->hot,<br> koud >warm

### <a name="cool-and-archive-early-deletion"></a>Vroege verwijdering voor Koud en Archief

Elke blob die wordt verplaatst naar de cool-laag (alleen GPv2-accounts), is onderhevig aan een leuke verwijderings periode van 30 dagen. Elke blob die wordt verplaatst naar de laag van het archief, is onderhevig aan een vroegtijdige verwijderings periode van 180 dagen van een archief. Deze kosten zijn evenredig verdeeld. Als een BLOB bijvoorbeeld wordt verplaatst naar Archive en vervolgens na 45 dagen wordt verwijderd of verplaatst naar de warme laag, worden de kosten voor vroegtijdige verwijdering in rekening gebracht die gelijk zijn aan 135 (180 min 45) dagen waarin de blob is opgeslagen in archief.

U kunt de vroege verwijdering berekenen met behulp van de eigenschap blob, **Laatst gewijzigd**, als er geen wijzigingen zijn aangebracht in de laag. Anders kunt u gebruiken wanneer de Access-laag voor het laatst is gewijzigd in Cool of Archive door de BLOB-eigenschap te bekijken: **toegangs lagen-wijzigings tijd**. Zie [Eigenschappen van BLOB ophalen](/rest/api/storageservices/get-blob-properties)voor meer informatie over blob-eigenschappen.

## <a name="comparing-block-blob-storage-options"></a>Vergelijkings opties voor blok-Blob-opslag

In de volgende tabel ziet u een vergelijking van de toegangs lagen voor het blok keren van Premium-prestaties en de dynamische, koude en archief opslag.

|                                           | **Premium-prestaties**   | **Warme laag** | **Cool-laag**       | **Laag van archief**  |
| ----------------------------------------- | ------------------------- | ------------ | ------------------- | ----------------- |
| **Beschikbaarheid**                          | 99,9%                     | 99,9%        | 99%                 | Offline           |
| **Beschikbaarheid** <br> **(RA-GRS-leesbewerkingen)**  | N.v.t.                       | 99,99%       | 99,9%               | Offline           |
| **Gebruiks kosten**                         | Hogere opslag kosten, lagere toegang en transactie kosten | Hogere opslag kosten, lagere toegang en transactie kosten | Lagere opslag kosten, hogere toegang en transactie kosten | Laagste opslag kosten, hoogste toegang en transactie kosten |
| **Minimale objectgrootte**                   | N.v.t.                       | N.v.t.          | N.v.t.                 | N.v.t.               |
| **Minimale opslagduur**              | N.v.t.                       | N.v.t.          | 30 dagen<sup>1</sup> | 180 dagen
| **Latentie** <br> **(Tijd tot eerste byte)** | Milliseconden met één cijfer | milliseconden | milliseconden        | uur<sup>2</sup> |

<sup>1</sup> objecten in de cool-laag op GPv2-accounts hebben een minimale Bewaar periode van 30 dagen. Blob Storage accounts hebben geen minimale Bewaar duur voor de cool-laag.

<sup>2</sup> Archive Storage ondersteunt momenteel 2 herstel prioriteiten, hoog en standaard, die verschillende latenties voor het ophalen bieden. Zie BLOB-gegevens opnieuw [inbreken vanuit de laag archief](storage-blob-rehydration.md)voor meer informatie.

> [!NOTE]
> Blob Storage-accounts ondersteunen dezelfde prestatie-en schaalbaarheids doelen als voor algemeen gebruik v2-opslag accounts. Zie [schaalbaarheids-en prestatie doelen voor Blob Storage](scalability-targets.md)voor meer informatie.

## <a name="quickstart-scenarios"></a>Snelstartscenario's

In deze sectie worden de volgende scenario's geïllustreerd met behulp van de Azure Portal en Power shell:

- Het wijzigen van de toegangslaag van het standaardaccount van een GPv2- of Blob Storage-account.
- Het wijzigen van de opslaglaag van een blob in een GPv2- of Blob Storage-account.

### <a name="change-the-default-account-access-tier-of-a-gpv2-or-blob-storage-account"></a>De toegangs laag van het standaard account van een GPv2-of Blob Storage-account wijzigen

# <a name="portal"></a>[Portal](#tab/azure-portal)
1. Meld u aan bij de [Azure-portal](https://portal.azure.com).

1. Zoek in het Azure Portal **alle resources** en selecteer deze.

1. Selecteer uw opslagaccount.

1. Selecteer in **instellingen** de optie **configuratie** om de account configuratie te bekijken en te wijzigen.

1. Selecteer de juiste toegangs laag voor uw behoeften: Stel de **toegangs laag** in op **koud** of **dynamisch**.

1. Klik bovenaan op **Opslaan** .

![De standaardlaag van de account wijzigen in Azure Portal](media/storage-tiers/account-tier.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Het volgende Power shell-script kan worden gebruikt om de account tier te wijzigen. De `$rgName` variabele moet worden geïnitialiseerd met de naam van de resource groep. De `$accountName` variabele moet worden geïnitialiseerd met de naam van uw opslag account. 
```powershell
#Initialize the following with your resource group and storage account names
$rgName = ""
$accountName = ""

#Change the storage account tier to hot
Set-AzStorageAccount -ResourceGroupName $rgName -Name $accountName -AccessTier Hot
```
---

### <a name="change-the-tier-of-a-blob-in-a-gpv2-or-blob-storage-account"></a>De laag van een BLOB in een GPv2-of Blob Storage-account wijzigen
# <a name="portal"></a>[Portal](#tab/azure-portal)
1. Meld u aan bij de [Azure-portal](https://portal.azure.com).

1. Zoek in het Azure Portal **alle resources** en selecteer deze.

1. Selecteer uw opslagaccount.

1. Selecteer uw container en selecteer vervolgens uw blob.

1. Selecteer in de **BLOB**-eigenschappen **laag wijzigen**.

1. Selecteer de Access-laag **Hot**, **cool** of **Archive** . Als uw BLOB zich momenteel in het archief bevindt en u wilt opnieuw worden gehydrateerd naar een online-laag, kunt u ook een onhydrate prioriteit van **Standard** of **High** selecteren.

1. Selecteer onder **Opslaan** onder.

![BLOB-laag wijzigen in Azure Portal](media/storage-tiers/blob-access-tier.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Het volgende Power shell-script kan worden gebruikt om de BLOB-laag te wijzigen. De `$rgName` variabele moet worden geïnitialiseerd met de naam van de resource groep. De `$accountName` variabele moet worden geïnitialiseerd met de naam van uw opslag account. De `$containerName` variabele moet worden geïnitialiseerd met de container naam. De `$blobName` variabele moet worden geïnitialiseerd met de naam van de blob. 
```powershell
#Initialize the following with your resource group, storage account, container, and blob names
$rgName = ""
$accountName = ""
$containerName = ""
$blobName == ""

#Select the storage account and get the context
$storageAccount =Get-AzStorageAccount -ResourceGroupName $rgName -Name $accountName
$ctx = $storageAccount.Context

#Select the blob from a container
$blob = Get-AzStorageBlob -Container $containerName -Blob $blobName -Context $ctx

#Change the blob’s access tier to archive
$blob.ICloudBlob.SetStandardBlobTier("Archive")
```
---

## <a name="pricing-and-billing"></a>Prijzen en facturering

Alle opslag accounts gebruiken een prijs model voor blok-Blob-opslag op basis van de laag van elke blob. Houd rekening met de volgende overwegingen met betrekking tot facturering:

- **Opslagkosten**: de kosten voor het opslaan van gegevens hangen niet alleen af van de hoeveelheid opgeslagen gegevens, maar ook van de gebruikte toegangslaag. De kosten per GB nemen af als de laag minder dynamisch ('cooler') wordt.
- **Kosten van gegevenstoegang**: de kosten voor gegevenstoegang nemen toe als de laag minder dynamisch ('cooler') wordt. Voor gegevens in de coole en archief-toegangs laag, worden er kosten in rekening gebracht voor gegevens toegang per gigabyte voor lees bewerkingen.
- **Transactie kosten**: er zijn kosten per trans actie voor alle lagen die toenemen naarmate de laag koeler wordt.
- **Kosten voor gegevensoverdracht met geo-replicatie**: deze kosten zijn alleen van toepassing op accounts waarvoor geo-replicatie is geconfigureerd, inclusief GRS en RA-GRS. Kosten voor gegevensoverdracht met geo-replicatie worden in rekening gebracht per GB.
- **Kosten voor uitgaande gegevensoverdracht**: uitgaande gegevensoverdracht (gegevens die buiten een Azure-regio worden overgedragen) worden gefactureerd voor bandbreedtegebruik per GB, net zoals bij opslagaccounts voor algemeen gebruik.
- **De toegangs laag wijzigen**: als u de toegangs laag van het account wijzigt, worden de kosten voor het wijzigen van de laag voor niet- _verstelde_ blobs in het account waarvoor geen expliciete laag is ingesteld. Raadpleeg voor meer informatie over het wijzigen van de toegangs laag voor één BLOB de [facturering op BLOB-niveau](#blob-level-tiering-billing).

    Het wijzigen van de toegangs laag voor een BLOB wanneer versie beheer is ingeschakeld, of als de BLOB moment opnamen bevat, kunnen er extra kosten in rekening worden gebracht. Zie [prijzen en facturering](versioning-overview.md#pricing-and-billing) in de documentatie voor BLOB-versie beheer voor meer informatie over hoe u wordt gefactureerd wanneer BLOB-versie beheer is ingeschakeld en u de laag van een BLOB expliciet wijzigt. Zie [prijzen en facturering](snapshots-overview.md#pricing-and-billing) in de documentatie voor BLOB-moment opnamen voor meer informatie over hoe u wordt gefactureerd wanneer een BLOB moment opnamen heeft en u de laag van de BLOB expliciet wijzigt.

> [!NOTE]
> Zie Azure Storage-pagina met [prijzen](https://azure.microsoft.com/pricing/details/storage/blobs/) voor meer informatie over de prijzen voor blok-blobs. Zie de pagina [Prijsinformatie voor bandbreedte](https://azure.microsoft.com/pricing/details/data-transfers/) voor meer informatie over de kosten voor uitgaande gegevensoverdracht.

## <a name="faq"></a>Veelgestelde vragen

**Moet ik Blob Storage-of GPv2-accounts gebruiken als ik mijn gegevens trapsgewijs wil?**

Wij raden u aan GPv2 te gebruiken in plaats van Blob Storage-accounts voor opslaglagen. GPv2-accounts ondersteunen alle functies die Blob Storage-accounts ondersteunen, plus nog veel meer. Prijzen van Blob Storage en GPv2 zijn bijna identiek, maar sommige nieuwe functies en prijsverlagingen zijn alleen beschikbaar bij GPv2-accounts. GPv1-accounts bieden geen ondersteuning voor lagen.

De prijsstructuur tussen GPv1- en GPv2-accounts is verschillend en klanten moeten beide zorgvuldig evalueren alvorens te besluiten GPv2-accounts te gebruiken. U kunt een bestaand Blob Storage- of GPv1-account eenvoudig met één muisklik omzetten naar GPv2. Zie [Overzicht van Azure-opslagaccount](../common/storage-account-overview.md) voor meer informatie.

**Kan ik objecten opslaan in alle drie de toegangs lagen (hot, cool en Archive) in hetzelfde account?**

Ja. Het kenmerk van de **toegangs laag** die op account niveau is ingesteld, is de standaardlaag die van toepassing is op alle objecten in dat account zonder een expliciet ingestelde laag. Met lagen op blobniveau kunt u de toegangs laag op object niveau instellen, ongeacht de instelling van de toegangs laag voor het account. Blobs in een van de drie toegangs lagen (hot, cool of Archive) kunnen binnen hetzelfde account bestaan.

**Kan ik de standaard Access-laag van mijn BLOB of GPv2 Storage-account wijzigen?**

Ja, u kunt de standaardlaag voor het account wijzigen door het kenmerk **toegangs niveau** in te stellen voor het opslag account. Het wijzigen van de servicelaag is van toepassing op alle objecten die zijn opgeslagen in het account waarvoor geen expliciete laag is ingesteld (bijvoorbeeld **Hot (instel)** of **cool (uitgesteld)**). Het in-of uitschakelen van de laag van dynamisch naar koeling-schrijf bewerkingen (per 10.000) voor alle blobs zonder een set-laag in GPv2-accounts en het omschakelen van koud naar warm keer de kosten voor zowel lees bewerkingen (per 10.000) als voor het ophalen van gegevens (per GB) voor alle blobs in Blob Storage-en GPv2-accounts.

**Kan ik de toegangslaag van mijn standaardaccount instellen op Archive?**

Nee. Alleen de lagen hot en cool kunnen worden ingesteld als de standaard account toegang. Archive kan alleen op objectniveau worden ingesteld. Bij het uploaden van blobs geeft u de toegangs laag op waarvan uw keuze moet worden ingesteld op warme, koud of archief, ongeacht de standaardlaag. Met deze functie kunt u gegevens rechtstreeks naar de archief laag schrijven om de kosten besparingen te bedenken vanaf het moment dat u gegevens in Blob Storage maakt.

**In welke regio's zijn de dynamische, coole en archief toegangs lagen beschikbaar in?**

De warme en coolbar-toegangs lagen en lagen op BLOB-niveau zijn in alle regio's beschikbaar. Archive-opslag is in eerste instantie alleen beschikbaar in bepaalde regio's. Zie voor een volledige lijst [Azure-producten beschikbaar per regio](https://azure.microsoft.com/regions/services/).

**Welke redundantie opties worden ondersteund voor de dynamische toegangs lagen hot, cool en Archive?**

De warme en cool-laag ondersteunen alle redundantie opties. De archief laag ondersteunt alleen LRS, GRS en RA-GRS. ZRS, GZRS en RA-GZRS worden niet ondersteund voor de archief laag.

**Werken de blobs in de laag voor coolbar-toegang anders dan die in de warme Access-laag?**

Blobs in de laag Hot Access hebben dezelfde latentie als blobs in GPv1-, GPv2-en Blob Storage-accounts. Blobs in de laag voor een coolbar hebben een vergelijk bare latentie (in milliseconden) als blobs in GPv1-, GPv2-en Blob Storage-accounts. Blobs in de toegangs laag voor het archief hebben verschillende uren latentie in GPv1-, GPv2-en Blob Storage-accounts.

Blobs in de laag voor coolbar hebben een service niveau met een iets lagere Beschik baarheid dan de blobs die zijn opgeslagen in de laag Hot Access. Zie [Dienstovereenkomst voor Storage](https://azure.microsoft.com/support/legal/sla/storage/v1_5/) voor meer informatie.

**Zijn de bewerkingen tussen de dynamische-, statische- en archiefopslaglagen hetzelfde?**

Alle bewerkingen tussen de dynamische en statische laag zijn 100% consistent. Alle geldige archief bewerkingen, waaronder GetBlobProperties, GetBlobMetadata, SetBlobTags, GetBlobTags, FindBlobsByTags, ListBlobs, SetBlobTier en DeleteBlob, zijn 100% consistent met warm en cool. BLOB-gegevens kunnen niet worden gelezen of gewijzigd in de laag van het archief voordat ze opnieuw worden gehydrateerd. alleen lees bewerkingen voor BLOB-meta gegevens worden ondersteund in het archief. BLOB index Tags kunnen echter in archief worden gelezen, ingesteld of gewijzigd.

**Als ik een blob reactiveer vanuit de archiefopslaglaag naar de dynamische- of statische-opslaglaag, hoe weet ik dan wanneer de reactivering is voltooid?**

Tijdens rehydratatie kunt u de bewerking BLOB-eigenschappen ophalen gebruiken om het kenmerk **Archive status** te controleren en te bevestigen wanneer de laag wijziging is voltooid. De status is "rehydrate-pending-to-hot" of "rehydrate-pending-to-cool", afhankelijk van de doellaag. Na voltooiing wordt de eigenschap archief status verwijderd en de BLOB-eigenschap van de **Access-laag** weerspiegelt de nieuwe hot of cool-laag. Zie [BLOB-gegevens opnieuw inbreken van de archief laag](storage-blob-rehydration.md) voor meer informatie.

**Na het instellen van de opslaglaag van een blob, wanneer wordt er dan gefactureerd tegen het juiste tarief?**

Elke BLOB wordt altijd gefactureerd op basis van de laag die wordt aangegeven door de eigenschap **Access tier** van de blob. Wanneer u een nieuwe online-laag instelt voor een blob, weerspiegelt de eigenschap **Access tier** direct de nieuwe laag voor alle overgangen. Het kan echter enkele uren duren voordat een BLOB van de offline-reactiveren naar een warme of koud laag kan worden geschaald. In dit geval wordt u gefactureerd tegen archief tarieven totdat rehydratatie is voltooid, waarbij de eigenschap **Access tier** de nieuwe laag weerspiegelt. Zodra u de online laag opnieuw hebt gehydrateerd, wordt deze in rekening gebracht tegen de warme of gekoelde snelheid.

**Hoe kan ik bepalen of er kosten voor de eerste keer dat ik een BLOB uit de cool-of opslaglaag-laag Verwijder of verplaats?**

Voor elke blob die binnen 30 dagen of 180 dagen wordt verwijderd of verplaatst uit respectievelijk de statische (alleen GPv2-accounts) of archiefopslaglaag, wordt een vooraf vastgesteld bedrag voor vroegtijdige verwijdering in rekening gebracht. U kunt bepalen hoe lang een BLOB zich in de koele of archiefmap bevindt door de BLOB-eigenschap **wijziging tijd van de toegangs laag** te controleren. deze geeft een stempel van de laatste wijziging van de laag aan. Als de laag van de BLOB nooit is gewijzigd, kunt u de **laatste gewijzigde** BLOB-eigenschap controleren. Zie voor meer informatie [cool en archief vroegtijdig verwijderen](#cool-and-archive-early-deletion).

**Welke Azure Tools en SDK's ondersteunen laaginstelling op blobniveau en archiefopslag?**

Azure Portal, PowerShell en CLI Tools en .NET, Java, Python en Node.js-clientbibliotheken bieden ondersteuning voor laaginstelling op blobniveau en archiefopslag.  

**Hoeveel gegevens kan ik opslaan in de dynamische-, statische- en archiefopslaglaag?**

Gegevens opslag en andere limieten worden ingesteld op account niveau en niet per toegangs laag. U kunt ervoor kiezen om al uw limieten in één laag of in alle drie de lagen te gebruiken. Zie [schaalbaarheids-en prestatie doelen voor standaard opslag accounts](../common/scalability-targets-standard-account.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

Evalueren van warme, koude en archief in GPv2-en Blob Storage-accounts

- [De beschikbaarheid controleren van de dynamische, statische en archieflaag per regio](https://azure.microsoft.com/regions/#services)
- [Azure Blob Storage levenscyclus beheren](storage-lifecycle-management-concepts.md)
- [Meer informatie over reactiveren BLOB-gegevens uit de laag archief](storage-blob-rehydration.md)
- [Bepalen of Premium-prestaties uw app zouden voor u hebben](storage-blob-performance-tiers.md)
- [Gebruik van de huidige opslagaccounts evalueren door metrische gegevens voor Azure Storage in te schakelen](./monitor-blob-storage.md)
- [Prijzen voor de dynamische, statische en archieflaag controleren in Blob Storage- en GPv2-accounts per regio](https://azure.microsoft.com/pricing/details/storage/)
- [Prijzen voor gegevensoverdracht controleren](https://azure.microsoft.com/pricing/details/data-transfers/)