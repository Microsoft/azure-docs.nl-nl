---
title: BLOB-moment opnamen
titleSuffix: Azure Storage
description: Begrijpen hoe BLOB-moment opnamen werken en hoe ze worden gefactureerd.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 02/02/2021
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: debce0a1b4c09bb89cdceb1cd29e59e1976c939a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "99539146"
---
# <a name="blob-snapshots"></a>BLOB-moment opnamen

Een moment opname is een alleen-lezen versie van een blob die op een bepaald moment wordt uitgevoerd.

> [!NOTE]
> BLOB-versie beheer biedt een superieure manier om eerdere versies van een BLOB te onderhouden. Zie voor meer informatie [BLOB-versie beheer](versioning-overview.md).

## <a name="about-blob-snapshots"></a>Over blob-moment opnamen

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

Een moment opname van een blob is identiek aan de basis-blob, behalve dat de BLOB-URI een **datum/tijd** -waarde heeft toegevoegd aan de BLOB-URI om het tijdstip aan te geven waarop de moment opname is gemaakt. Als de URI van een pagina-BLOB bijvoorbeeld is `http://storagesample.core.blob.windows.net/mydrives/myvhd` , is de moment opname-URI vergelijkbaar met `http://storagesample.core.blob.windows.net/mydrives/myvhd?snapshot=2011-03-09T01:42:34.9360000Z` .

> [!NOTE]
> Alle moment opnamen delen de URI van de basis-blob. Het enige verschil tussen de basis-Blob en de moment opname is de toegevoegde **DateTime** -waarde.

Een BLOB kan een wille keurig aantal moment opnamen hebben. Moment opnamen blijven behouden totdat ze expliciet worden verwijderd, hetzij onafhankelijk of als onderdeel van een bewerking voor het verwijderen van een [BLOB](/rest/api/storageservices/delete-blob) voor de basis-blob. U kunt de moment opnamen die zijn gekoppeld aan de basis-BLOB opsommen om uw huidige moment opnamen bij te houden.

Wanneer u een moment opname van een BLOB maakt, worden de systeem eigenschappen van de BLOB gekopieerd naar de moment opname met dezelfde waarden. De meta gegevens van de basis-BLOB worden ook gekopieerd naar de moment opname, tenzij u afzonderlijke meta gegevens voor de moment opname opgeeft wanneer u deze maakt. Nadat u een moment opname hebt gemaakt, kunt u deze lezen, kopiëren of verwijderen, maar u kunt deze niet wijzigen.

Alle leases die zijn gekoppeld aan de basis-blob, hebben geen invloed op de moment opname. U kunt geen lease verkrijgen voor een moment opname.

Een VHD-bestand wordt gebruikt voor het opslaan van de huidige informatie en status voor een VM-schijf. U kunt een schijf loskoppelen van in de VM of de virtuele machine afsluiten en vervolgens een moment opname maken van het VHD-bestand. U kunt dit momentopname bestand later gebruiken om het VHD-bestand op dat moment op te halen en de virtuele machine opnieuw te maken.

## <a name="understand-how-snapshots-accrue-charges"></a>Meer informatie over hoe moment opnamen kosten samen voegen

### <a name="important-billing-considerations"></a>Belang rijke overwegingen met betrekking tot facturering

De volgende lijst bevat belang rijke punten waarmee u rekening moet houden bij het maken van een moment opname.

- In uw opslag account worden kosten in rekening gebracht voor unieke blokken of pagina's, ongeacht of deze zich in de BLOB of in de moment opname bevinden. Voor uw account worden geen extra kosten in rekening gebracht voor moment opnamen die zijn gekoppeld aan een BLOB totdat u de BLOB bijwerkt waarop ze zijn gebaseerd. Nadat u de basis-BLOB hebt bijgewerkt, is deze afwijkend van de moment opnamen. Als dit gebeurt, worden er kosten in rekening gebracht voor de unieke blokken of pagina's in elke BLOB of moment opname.
- Wanneer u een blok in een blok-BLOB vervangt, wordt dat blok vervolgens als een uniek blok in rekening gebracht. Dit geldt ook als het blok dezelfde blok-ID en dezelfde gegevens bevat als in de moment opname. Nadat het blok opnieuw is doorgevoerd, is het afwijkend van de tegen hanger in een moment opname en worden de gegevens in rekening gebracht. Hetzelfde geldt voor een pagina in een pagina-blob die wordt bijgewerkt met identieke gegevens.
- Als u een blok-BLOB bijwerkt door een methode aan te roepen die de volledige inhoud van de BLOB overschrijft, worden alle blokken in de BLOB vervangen. Als u een moment opname hebt die is gekoppeld aan die blob, zijn alle blokken in de basis-Blob en snap shot nu afwijkend en worden er kosten in rekening gebracht voor alle blokken in beide blobs. Dit geldt ook als de gegevens in de basis-Blob en de moment opname identiek blijven.
- De Azure-Blob service heeft geen manier om te bepalen of twee blokken identieke gegevens bevatten. Elk blok dat wordt geüpload en doorgevoerd, wordt behandeld als uniek, zelfs als het dezelfde gegevens en dezelfde blok-ID heeft. Omdat kosten toenemen voor unieke blokken, is het belang rijk om te overwegen dat het bijwerken van een blob met een moment opname resulteert in extra unieke blokken en extra kosten.

### <a name="minimize-costs-with-snapshot-management"></a>Kosten minimaliseren met snap shot Management

We raden u aan uw moment opnamen zorgvuldig te beheren om extra kosten te voor komen. U kunt deze aanbevolen procedures volgen om de kosten te minimaliseren die worden gemaakt door de opslag van uw moment opnamen:

- U kunt moment opnamen die zijn gekoppeld aan een blob, verwijderen en opnieuw maken wanneer u de BLOB bijwerkt, zelfs als u een update uitvoert met identieke gegevens, tenzij uw toepassings ontwerp vereist dat u moment opnamen bijhoudt. Door de moment opnamen van de BLOB te verwijderen en opnieuw te maken, kunt u ervoor zorgen dat de BLOB en de moment opnamen niet afwijken.
- Als u moment opnamen voor een BLOB beheert, vermijdt u het aanroepen van methoden die de hele BLOB overschrijven wanneer u de BLOB bijwerkt. Werk in plaats daarvan het zo weinig mogelijk aantal blokken bij om de kosten te laag te blijven.

### <a name="snapshot-billing-scenarios"></a>Facturerings scenario's voor moment opnamen

De volgende scenario's laten zien hoe kosten toenemen voor een blok-Blob en de bijbehorende moment opnamen.

## <a name="pricing-and-billing"></a>Prijzen en facturering

Het maken van een moment opname, een alleen-lezen kopie van een blob, kan leiden tot extra kosten voor gegevens opslag voor uw account. Bij het ontwerpen van uw toepassing is het belang rijk om te weten hoe deze kosten kunnen toenemen, zodat u de kosten kunt minimaliseren.

BLOB-moment opnamen, zoals BLOB-versies, worden in rekening gebracht tegen hetzelfde tarieven als actieve gegevens. Hoe moment opnamen worden gefactureerd, is afhankelijk van of u de laag expliciet hebt ingesteld voor de basis-BLOB of voor een van de moment opnamen (of versies). Zie [Azure Blob Storage: warme, cool en archief toegangs lagen](storage-blob-storage-tiers.md)voor meer informatie over blob-lagen.

Als u de laag van een BLOB of moment opname niet hebt gewijzigd, worden er kosten in rekening gebracht voor unieke gegevens blokken voor die blob, de bijbehorende moment opnamen en eventuele versies. Zie [Facturering wanneer de BLOB-laag niet expliciet is ingesteld](#billing-when-the-blob-tier-has-not-been-explicitly-set)voor meer informatie.

Als u de laag van een BLOB of moment opname hebt gewijzigd, wordt u gefactureerd voor het hele object, ongeacht of de BLOB en de moment opname zich in dezelfde laag bevinden. Zie [Facturering wanneer de BLOB-laag expliciet is ingesteld](#billing-when-the-blob-tier-has-been-explicitly-set)voor meer informatie.

Zie [BLOB-versie beheer](versioning-overview.md)voor meer informatie over de facturerings gegevens voor BLOB-versies.

### <a name="billing-when-the-blob-tier-has-not-been-explicitly-set"></a>Facturering wanneer de BLOB-laag niet expliciet is ingesteld

Als u de BLOB-laag niet expliciet hebt ingesteld voor een basis-BLOB of een van de moment opnamen daarvan, worden er unieke blokken of pagina's in rekening gebracht voor de blob, de bijbehorende moment opnamen en eventuele versies. Gegevens die worden gedeeld via een BLOB en de bijbehorende moment opnamen worden slechts één keer in rekening gebracht. Wanneer een BLOB wordt bijgewerkt, worden gegevens in een basis-BLOB afwijkt van de gegevens die zijn opgeslagen in de moment opnamen en worden de unieke gegevens per blok of pagina in rekening gebracht.

Wanneer u een blok in een blok-BLOB vervangt, wordt dat blok vervolgens als een uniek blok in rekening gebracht. Dit geldt ook als het blok dezelfde blok-ID en dezelfde gegevens bevat als in de moment opname. Nadat het blok opnieuw is doorgevoerd, is het afwijkend van het equivalent in de moment opname en worden de gegevens in rekening gebracht. Hetzelfde geldt voor een pagina in een pagina-blob die wordt bijgewerkt met identieke gegevens.

Blob-opslag is niet van een manier om te bepalen of twee blokken identieke gegevens bevatten. Elk blok dat wordt geüpload en doorgevoerd, wordt behandeld als uniek, zelfs als het dezelfde gegevens en dezelfde blok-ID heeft. Omdat kosten worden toegerekend voor unieke blokken, is het belang rijk dat u een BLOB bijwerkt wanneer die BLOB moment opnamen of versies heeft, waardoor er meer unieke blokken en extra kosten in rekening worden gebracht.

Wanneer een BLOB moment opnamen bevat, moet u update bewerkingen aanroepen op blok-blobs zodat deze het minst mogelijke aantal blokken bijwerken. De schrijf bewerkingen die nauw keurige controle over blokken toestaan, zijn [blok keren](/rest/api/storageservices/put-block) en [blokkerings lijst plaatsen](/rest/api/storageservices/put-block-list). De [put-BLOB](/rest/api/storageservices/put-blob) -bewerking, daarentegen, vervangt de volledige inhoud van een BLOB en kan dus leiden tot extra kosten.

De volgende scenario's laten zien hoe kosten worden toegerekend voor een blok-Blob en de bijbehorende moment opnamen wanneer de BLOB-laag niet expliciet is ingesteld.

#### <a name="scenario-1"></a>Scenario 1

In scenario 1 is de basis-BLOB niet bijgewerkt nadat de moment opname is gemaakt, dus worden er alleen kosten in rekening gebracht voor unieke blokken 1, 2 en 3.

![Diagram 1 met facturering voor unieke blokken in basis-Blob en moment opname.](./media/snapshots-overview/storage-blob-snapshots-billing-scenario-1.png)

#### <a name="scenario-2"></a>Scenario 2

In scenario 2 is de basis-BLOB bijgewerkt, maar niet de moment opname. Blok 3 is bijgewerkt en hoewel het dezelfde gegevens en dezelfde ID bevat, is dit niet hetzelfde als blok 3 in de moment opname. Als gevolg hiervan wordt het account in rekening gebracht voor vier blokken.

![Diagram 2 toont facturering voor unieke blokken in basis-Blob en moment opname.](./media/snapshots-overview/storage-blob-snapshots-billing-scenario-2.png)

#### <a name="scenario-3"></a>Scenario 3

In scenario 3 is de basis-BLOB bijgewerkt, maar niet de moment opname. Blok 3 is vervangen door blok 4 in de basis-blob, maar de moment opname komt nog steeds overeen met blok 3. Als gevolg hiervan wordt het account in rekening gebracht voor vier blokken.

![Diagram 3 toont facturering voor unieke blokken in basis-Blob en moment opname.](./media/snapshots-overview/storage-blob-snapshots-billing-scenario-3.png)

#### <a name="scenario-4"></a>Scenario 4

In scenario 4 is de basis-BLOB volledig bijgewerkt en bevat deze geen van de oorspronkelijke blokken. Als gevolg hiervan wordt het account in rekening gebracht voor alle acht unieke blokken.

![Diagram 4 wordt weer gegeven voor de facturering van unieke blokken in de basis-Blob en de moment opname.](./media/snapshots-overview/storage-blob-snapshots-billing-scenario-4.png)

> [!TIP]
> Vermijd het aanroepen van methoden waarbij de gehele BLOB wordt overschreven. in plaats daarvan worden afzonderlijke blokken bijgewerkt zodat de kosten laag blijven.

### <a name="billing-when-the-blob-tier-has-been-explicitly-set"></a>Facturering wanneer de BLOB-laag expliciet is ingesteld

Als u de BLOB-laag voor een BLOB of moment opname (of versie) expliciet hebt ingesteld, worden er kosten in rekening gebracht voor de volledige lengte van de inhoud van het object in de nieuwe laag, ongeacht of er blokken worden gedeeld met een object in de oorspronkelijke laag. Ook worden er kosten in rekening gebracht voor de volledige inhouds lengte van de oudste versie in de oorspronkelijke laag. Alle versies of moment opnamen die in de oorspronkelijke laag blijven, worden in rekening gebracht voor unieke blokken die ze kunnen delen, zoals wordt beschreven in [Facturering wanneer de BLOB-laag niet expliciet is ingesteld](#billing-when-the-blob-tier-has-not-been-explicitly-set).

#### <a name="moving-a-blob-to-a-new-tier"></a>Een BLOB verplaatsen naar een nieuwe laag

In de volgende tabel wordt het facturerings gedrag voor een BLOB of moment opname beschreven wanneer het wordt verplaatst naar een nieuwe laag.

| Wanneer de BLOB-laag expliciet is ingesteld... | Vervolgens wordt u gefactureerd voor... |
|-|-|
| Een basis-blob met een moment opname | De basis-Blob in de nieuwe laag en de oudste moment opname in de oorspronkelijke laag, plus eventuele unieke blokken in andere moment opnamen. <sup>1</sup> |
| Een basis-blob met een vorige versie en een moment opname | De basis-Blob in de nieuwe laag, de oudste versie in de oorspronkelijke laag en de oudste moment opname in de oorspronkelijke laag, plus eventuele unieke blokken in andere versies of moment opnamen<sup>1</sup>. |
| Een moment opname | De moment opname in de nieuwe laag en de basis-Blob in de oorspronkelijke laag, plus eventuele unieke blokken in andere moment opnamen. <sup>1</sup> |

<sup>1</sup> Als er andere eerdere versies of moment opnamen zijn die niet zijn verplaatst uit de oorspronkelijke laag, worden deze versies of moment opnamen in rekening gebracht op basis van het aantal unieke blokken dat ze bevatten, zoals wordt beschreven in [Facturering wanneer de BLOB-laag niet expliciet is ingesteld](#billing-when-the-blob-tier-has-not-been-explicitly-set).

In het volgende diagram ziet u hoe objecten worden gefactureerd wanneer een blob met moment opnamen wordt verplaatst naar een andere laag.

:::image type="content" source="media/snapshots-overview/snapshot-billing-tiers.png" alt-text="Diagram waarin wordt getoond hoe objecten worden gefactureerd wanneer een blob met moment opnamen expliciet wordt gelaagd.":::

Het expliciet instellen van de laag voor een blob, versie of moment opname kan niet ongedaan worden gemaakt. Als u een BLOB naar een nieuwe laag verplaatst en vervolgens weer naar de oorspronkelijke laag verplaatst, worden er kosten in rekening gebracht voor de volledige lengte van de inhoud van het object, zelfs als er blokken met andere objecten in de oorspronkelijke laag worden gedeeld.

Bewerkingen die de laag van een blob, versie of moment opname expliciet instellen, zijn onder andere:

- [Set Blob Tier](/rest/api/storageservices/set-blob-tier) (Blob-laag instellen)
- [BLOB](/rest/api/storageservices/put-blob) met opgegeven laag plaatsen
- [Blokkerings lijst](/rest/api/storageservices/put-block-list) met opgegeven laag plaatsen
- [BLOB kopiëren](/rest/api/storageservices/copy-blob) met opgegeven laag

#### <a name="deleting-a-blob-when-soft-delete-is-enabled"></a>Een BLOB verwijderen wanneer de functie voor voorlopig verwijderen is ingeschakeld

Als de functie voor het voorlopig verwijderen van blobs is ingeschakeld, wordt de volledige lengte van de inhoud gefactureerd als u een basis-BLOB verwijdert of overschrijft waarvan de laag expliciet is ingesteld. Zie voor meer informatie over hoe BLOB versie en soft delete samen werken, [BLOB-versie beheer en zacht verwijderen](versioning-overview.md#blob-versioning-and-soft-delete).

In de volgende tabel wordt het facturerings gedrag voor een verwijderde BLOB beschreven, afhankelijk van het feit of versie beheer is ingeschakeld of uitgeschakeld. Wanneer versie beheer is ingeschakeld, wordt een nieuwe versie gemaakt wanneer een BLOB zacht wordt verwijderd. Wanneer versie beheer is uitgeschakeld, wordt door het zacht verwijderen van een BLOB een tijdelijke moment opname verwijderd.

| Wanneer u een basis-BLOB overschrijft waarbij de laag expliciet is ingesteld... | Vervolgens wordt u gefactureerd voor... |
|-|-|
| Als de functie voor het voorlopig verwijderen van blobs en versie beheer beide zijn ingeschakeld | Alle bestaande versies met volledige inhouds lengte, ongeacht de laag. |
| Als de functie voor het voorlopig verwijderen van BLOB is ingeschakeld, maar versie beheer is uitgeschakeld | Alle bestaande tijdelijke moment opnamen met een volledige inhouds lengte, ongeacht de laag, worden verwijderd. |

## <a name="next-steps"></a>Volgende stappen

- [BLOB-versie beheer](versioning-overview.md)
- [Een BLOB-moment opname maken en beheren in .NET](snapshots-manage-dotnet.md)
- [Back-ups maken van onbeheerde VM-schijven met incrementele momentopnames](../../virtual-machines/windows/incremental-snapshots.md)
