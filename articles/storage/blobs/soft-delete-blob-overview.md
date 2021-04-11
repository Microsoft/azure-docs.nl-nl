---
title: Blobs voorlopig verwijderen
titleSuffix: Azure Storage
description: Met zacht verwijderen voor blobs worden uw gegevens beschermd, zodat u uw gegevens eenvoudiger kunt herstellen wanneer deze foutief worden gewijzigd of verwijderd door een toepassing of door een andere gebruiker van het opslag account.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 03/27/2021
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 29d9dd7757319e59fc12b42d89c2ce16dec71b8b
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/07/2021
ms.locfileid: "106551064"
---
# <a name="soft-delete-for-blobs"></a>Blobs voorlopig verwijderen

Met de opdracht zacht verwijderen wordt een afzonderlijke blob, moment opname of versie van onopzettelijke verwijderingen of overschrijvingen beschermd door de verwijderde gegevens in het systeem gedurende een bepaalde periode te behouden. Tijdens de retentie periode kunt u een voorlopig verwijderd object herstellen naar de status op het moment dat het werd verwijderd. Wanneer de Bewaar periode is verlopen, wordt het object definitief verwijderd.

## <a name="recommended-data-protection-configuration"></a>Aanbevolen configuratie voor gegevens beveiliging

Het dynamisch verwijderen van blobs maakt deel uit van een uitgebreide strategie voor gegevens beveiliging voor BLOB-gegevens. Micro soft raadt aan om de volgende functies voor gegevens bescherming in te scha kelen voor optimale beveiliging van uw BLOB-gegevens:

- Container soft delete, om een verwijderde container te herstellen. Zie voor meer informatie over het inschakelen van de container Soft soft delete [voor containers, voorlopig verwijderen inschakelen en beheren](soft-delete-container-enable.md).
- BLOB-versie beheer om eerdere versies van een blob automatisch te onderhouden. Wanneer BLOB-versie beheer is ingeschakeld, kunt u een eerdere versie van een BLOB herstellen om uw gegevens te herstellen als deze ten onrechte zijn gewijzigd of verwijderd. Zie [BLOB-versie beheer inschakelen en beheren](versioning-enable.md)voor meer informatie over het inschakelen van BLOB-versies.
- Zacht verwijderen van BLOB, voor het herstellen van een blob, moment opname of versie die is verwijderd. Zie voor het inschakelen van het voorlopig verwijderen van blobs de optie [voorlopig verwijderen inschakelen en beheren voor blobs](soft-delete-blob-enable.md).

Zie [overzicht van gegevens beveiliging](data-protection-overview.md)voor meer informatie over de aanbevelingen van micro soft voor gegevens bescherming.

[!INCLUDE [storage-data-lake-gen2-support](../../../includes/storage-data-lake-gen2-support.md)]

## <a name="how-blob-soft-delete-works"></a>Hoe BLOB zacht verwijderen werkt

Wanneer u de optie voor het voorlopig verwijderen van blobs inschakelt voor een opslag account, geeft u een Bewaar periode op voor verwijderde objecten van 1 tot 365 dagen. De retentie periode geeft aan hoe lang de gegevens beschikbaar blijven nadat deze zijn verwijderd of worden overschreven. Zodra een object wordt verwijderd of overschreven, wordt de klok gestart op de Bewaar periode.

Hoewel de Bewaar periode actief is, kunt u een verwijderde blob, samen met de bijbehorende moment opnamen of een verwijderde versie, herstellen door de bewerking voor het verwijderen van een [BLOB](/rest/api/storageservices/undelete-blob) aan te roepen. In het volgende diagram ziet u hoe een verwijderd object kan worden hersteld wanneer de functie voor het voorlopig verwijderen van BLOB is ingeschakeld:

:::image type="content" source="media/soft-delete-blob-overview/blob-soft-delete-diagram.png" alt-text="Diagram waarin wordt getoond hoe een met Soft verwijderde Blob kan worden hersteld":::

U kunt de tijdelijke Bewaar periode voor verwijderen op elk gewenst moment wijzigen. Een bijgewerkte Bewaar periode geldt alleen voor gegevens die zijn verwijderd nadat de Bewaar periode is gewijzigd. Alle gegevens die zijn verwijderd voordat de Bewaar periode werd gewijzigd, zijn afhankelijk van de retentie periode die van kracht was op het moment dat deze werd verwijderd.

Het verwijderen van een voorlopig verwijderd object heeft geen invloed op de verloop tijd.

Als u de optie voor het voorlopig verwijderen van blobs uitschakelt, kunt u door gaan met het herstellen van met voorlopig verwijderde objecten in uw opslag account totdat de Bewaar periode voor de tijdelijke verwijdering is verstreken.

BLOB-versie beheer is beschikbaar voor algemeen gebruik v2-, blok-Blob-en Blob Storage-accounts. Opslag accounts met een hiërarchische naam ruimte die is ingeschakeld voor gebruik met Azure Data Lake Storage Gen2 worden momenteel niet ondersteund.

Versie 2017-07-29 en hoger van de Azure Storage REST API ondersteuning voor het voorlopig verwijderen van blobs.

> [!IMPORTANT]
> U kunt voor het voorlopig verwijderen van blobs alleen een afzonderlijke blob, een moment opname of een versie herstellen. Als u een container en de inhoud ervan wilt herstellen, moet u de container soft delete ook inschakelen voor het opslag account. Micro soft raadt u aan om voorlopig verwijderen van containers en BLOB-versie beheer samen met Blob soft delete in te scha kelen om ervoor te zorgen dat BLOB-gegevens volledig worden beveiligd. Zie [overzicht van gegevens beveiliging](data-protection-overview.md)voor meer informatie.
>
> Zacht verwijderen van BLOB biedt geen bescherming tegen het verwijderen van een opslag account. Als u een opslag account wilt beveiligen tegen verwijderen, configureert u een vergren deling voor de bron van het opslag account. Zie een [Azure Resource Manager Lock Toep assen op een opslag account](../common/lock-account-resource.md)voor meer informatie over het vergren delen van een opslag account.

### <a name="how-deletions-are-handled-when-soft-delete-is-enabled"></a>Hoe verwijderingen worden verwerkt wanneer zacht verwijderen is ingeschakeld

Wanneer de functie voor het voorlopig verwijderen van BLOB is ingeschakeld, verwijdert u de blobs die als zacht worden verwijderd. Er wordt geen moment opname gemaakt. Wanneer de Bewaar periode is verlopen, wordt de zachte verwijderde BLOB permanent verwijderd.

Als een BLOB moment opnamen bevat, kan de BLOB alleen worden verwijderd als de moment opnamen ook worden verwijderd. Wanneer u een BLOB en de bijbehorende moment opnamen verwijdert, worden zowel de BLOB als de moment opnamen gemarkeerd als zacht verwijderd. Er worden geen nieuwe moment opnamen gemaakt.

U kunt ook een of meer actieve moment opnamen verwijderen zonder de basis-BLOB te verwijderen. In dit geval wordt de moment opname zacht verwijderd.

Voorlopig verwijderde objecten zijn onzichtbaar, tenzij ze expliciet worden weer gegeven of weer gegeven. Zie voor meer informatie over het weer geven van zachte verwijderde objecten [beheren en herstellen van soft verwijderde blobs](soft-delete-blob-manage.md).

### <a name="how-overwrites-are-handled-when-soft-delete-is-enabled"></a>Hoe overschrijvingen worden verwerkt wanneer zacht verwijderen is ingeschakeld

Het aanroepen van een bewerking, zoals het plaatsen van een [BLOB](/rest/api/storageservices/put-blob), het plaatsen van een [blok lijst](/rest/api/storageservices/put-block-list)of het kopiëren van een [BLOB](/rest/api/storageservices/copy-blob) , overschrijft de gegevens in een blob. Wanneer het zacht verwijderen van de blob is ingeschakeld, maakt het overschrijven van een blob automatisch een voorlopig verwijderde moment opname van de status van de BLOB voordat de schrijf bewerking wordt uitgevoerd. Wanneer de Bewaar periode is verlopen, wordt de voorlopig verwijderde moment opname definitief verwijderd.

Tijdelijke verwijderde moment opnamen zijn onzichtbaar, tenzij tijdelijke verwijderde objecten expliciet worden weer gegeven of worden weer gegeven. Zie voor meer informatie over het weer geven van zachte verwijderde objecten [beheren en herstellen van soft verwijderde blobs](soft-delete-blob-manage.md).

Als u een Kopieer bewerking wilt beveiligen, moet u de functie voor het voorlopig verwijderen van BLOB inschakelen voor het doel opslag account.

Zacht verwijderen van BLOB biedt geen bescherming tegen bewerkingen om BLOB-meta gegevens of-eigenschappen te schrijven. Er wordt geen voorlopig verwijderde moment opname gemaakt wanneer meta gegevens of eigenschappen van een BLOB worden bijgewerkt.

Voor het voorlopig verwijderen van blobs wordt de beveiliging van blobs in de opslaglaag niet overschreven. Als een BLOB in de Archive-laag wordt overschreven door een nieuwe Blob in een wille keurige laag, wordt de overschreven BLOB definitief verwijderd.

Voor Premium-opslag accounts tellen tijdelijke verwijderde moment opnamen niet mee voor de limiet van 100-moment opnamen per blob.

### <a name="restoring-soft-deleted-objects"></a>Voorlopig verwijderde objecten herstellen

U kunt dynamische verwijderde blobs herstellen door de bewerking [undelete BLOB](/rest/api/storageservices/undelete-blob) in de Bewaar periode aan te roepen. Met de bewerking voor het verwijderen van een **BLOB** worden een BLOB en alle voorlopig verwijderde moment opnamen die eraan zijn gekoppeld, hersteld. Alle moment opnamen die zijn verwijderd tijdens de Bewaar periode, worden hersteld.

Het aanroepen van **dedelete BLOB** op een blob die niet zacht is verwijderd, herstelt alle voorlopig verwijderde moment opnamen die zijn gekoppeld aan de blob. Als de BLOB geen moment opnamen heeft en niet zacht is verwijderd, heeft het aanroepen van **undelete BLOB** geen effect.

Als u een voorlopig verwijderde moment opname wilt promo veren naar de basis-blob, roept u de **verwijderen van BLOB** op de basis-BLOB aan om de BLOB en de bijbehorende moment opnamen te herstellen. Kopieer vervolgens de gewenste moment opname via de basis-blob. U kunt de moment opname ook kopiëren naar een nieuwe blob.

Gegevens in een zachte verwijderde BLOB of moment opname kunnen niet worden gelezen totdat het object is hersteld.

Zie voor meer informatie over het herstellen van tijdelijke verwijderde objecten [beheren en herstellen van soft verwijderde blobs](soft-delete-blob-manage.md).

## <a name="blob-soft-delete-and-versioning"></a>Zacht verwijderen en versie beheer van BLOB

Als blob-versie beheer en dynamisch verwijderen van BLOB zijn ingeschakeld voor een opslag account, wordt door het overschrijven van een blob automatisch een nieuwe versie gemaakt. De nieuwe versie wordt niet zacht verwijderd en wordt niet verwijderd wanneer de tijdelijke Bewaar periode voor het verwijderen is verlopen. Er zijn geen voorlopig verwijderde moment opnamen gemaakt. Wanneer u een BLOB verwijdert, wordt de huidige versie van de BLOB een vorige versie en wordt de huidige versie verwijderd. Er wordt geen nieuwe versie gemaakt en er worden geen tijdelijke verwijderde moment opnamen gemaakt.

Als u de functie voor voorlopig verwijderen en versie beheer inschakelt, worden de BLOB-versies van verwijdering beveiligd. Als zacht verwijderen is ingeschakeld, wordt door het verwijderen van een versie een voorlopig verwijderde versie gemaakt. U kunt de bewerking **BLOB verwijderen** gebruiken om een door een soft verwijderde versie te herstellen, zolang er een actuele versie van de blob is. Als er geen huidige versie is, moet u een vorige versie kopiëren naar de huidige versie voordat u de bewerking voor het verwijderen van een **BLOB** aanroept.

> [!NOTE]
> Het aanroepen van de bewerking voor het **ongedaan** maken van de BLOB op een verwijderde BLOB wanneer versie beheer is ingeschakeld, worden de door soft verwijderde versies of moment opnamen hersteld, maar wordt de basis-BLOB niet hersteld. Als u de basis-BLOB wilt herstellen, moet u een eerdere versie verhogen door deze te kopiëren naar de basis-blob.

Micro soft raadt u aan om de functie voor het voorlopig verwijderen van versies en blobs voor uw opslag accounts in te scha kelen voor optimale gegevens bescherming. Zie voor meer informatie over het samen voegen van BLOB-versie en zacht verwijderen, [BLOB-versie beheer en soft verwijderen](versioning-overview.md#blob-versioning-and-soft-delete).

## <a name="blob-soft-delete-protection-by-operation"></a>Zacht verwijderen BLOB-beveiliging op bewerking

In de volgende tabel wordt het verwachte gedrag voor Delete-en Write-bewerkingen beschreven wanneer het dynamisch verwijderen van een blob is ingeschakeld, met of zonder BLOB-versie beheer:

| REST API bewerkingen | Voorlopig verwijderen ingeschakeld | Voorlopig verwijderen en versie beheer ingeschakeld |
|--|--|--|
| [Opslag account verwijderen](/rest/api/storagerp/storageaccounts/delete) | Geen verandering. Containers en blobs in het verwijderde account kunnen niet worden hersteld. | Geen verandering. Containers en blobs in het verwijderde account kunnen niet worden hersteld. |
| [Container verwijderen](/rest/api/storageservices/delete-container) | Geen verandering. Blobs in de verwijderde container kunnen niet worden hersteld. | Geen verandering. Blobs in de verwijderde container kunnen niet worden hersteld. |
| [Blob verwijderen](/rest/api/storageservices/delete-blob) | Als deze wordt gebruikt om een BLOB te verwijderen, wordt die BLOB gemarkeerd als zacht verwijderd. <br /><br /> Als u gebruikt om een BLOB-moment opname te verwijderen, wordt de moment opname gemarkeerd als zacht verwijderd. | Als u gebruikt om een BLOB te verwijderen, wordt de huidige versie een eerdere versie en wordt de huidige versie verwijderd. Er wordt geen nieuwe versie gemaakt en er worden geen tijdelijke verwijderde moment opnamen gemaakt.<br /><br /> Als u gebruikt om een BLOB-versie te verwijderen, wordt de versie als zacht verwijderd gemarkeerd. |
| [BLOB verwijderen](/rest/api/storageservices/delete-blob) | Hiermee herstelt u een BLOB en alle moment opnamen die zijn verwijderd binnen de Bewaar periode. | Hiermee herstelt u een BLOB en alle versies die zijn verwijderd binnen de Bewaar periode. |
| [Blob plaatsen](/rest/api/storageservices/put-blob)<br />[Blokkerings lijst plaatsen](/rest/api/storageservices/put-block-list)<br />[Blob kopiëren](/rest/api/storageservices/copy-blob)<br />[BLOB kopiëren van URL](/rest/api/storageservices/copy-blob) | Als u een actieve BLOB aanroept, wordt er automatisch een moment opname van de status van de BLOB vóór de bewerking gegenereerd. <br /><br /> Als deze wordt aangeroepen op een voorlopig verwijderde blob, wordt een moment opname van de voor gaande status van de BLOB alleen gegenereerd als deze wordt vervangen door een BLOB van hetzelfde type. Als de blob van een ander type is, worden alle bestaande voorlopig verwijderde gegevens definitief verwijderd. | Er wordt automatisch een nieuwe versie gegenereerd die de status van de BLOB vastlegt voordat de bewerking wordt uitgevoerd. |
| [Blok keren](/rest/api/storageservices/put-block) | Als deze wordt gebruikt om een blok door te voeren in een actieve blob, is er geen wijziging.<br /><br />Als deze wordt gebruikt om een blok door te voeren naar een blob die zacht wordt verwijderd, wordt een nieuwe BLOB gemaakt en wordt automatisch een moment opname gegenereerd om de status van de zachte verwijderde BLOB vast te leggen. | Geen verandering. |
| [Pagina plaatsen](/rest/api/storageservices/put-page)<br />[Pagina van URL plaatsen](/rest/api/storageservices/put-page-from-url) | Geen verandering. Pagina-BLOB-gegevens die worden overschreven of gewist met deze bewerking, worden niet opgeslagen en kunnen niet worden hersteld. | Geen verandering. Pagina-BLOB-gegevens die worden overschreven of gewist met deze bewerking, worden niet opgeslagen en kunnen niet worden hersteld. |
| [Blok toevoegen](/rest/api/storageservices/append-block)<br />[Blok van URL toevoegen](/rest/api/storageservices/append-block-from-url) | Geen verandering. | Geen verandering. |
| [BLOB-eigenschappen instellen](/rest/api/storageservices/set-blob-properties) | Geen verandering. Overschreven BLOB-eigenschappen kunnen niet worden hersteld. | Geen verandering. Overschreven BLOB-eigenschappen kunnen niet worden hersteld. |
| [BLOB-meta gegevens instellen](/rest/api/storageservices/set-blob-metadata) | Geen verandering. Overschreven BLOB-meta gegevens kunnen niet worden hersteld. | Er wordt automatisch een nieuwe versie gegenereerd die de status van de BLOB vastlegt voordat de bewerking wordt uitgevoerd. |
| [Set Blob Tier](/rest/api/storageservices/set-blob-tier) (Blob-laag instellen) | De basis-BLOB wordt verplaatst naar de nieuwe laag. Actieve of voorlopig verwijderde moment opnamen blijven in de oorspronkelijke laag. Er is geen voorlopig verwijderde moment opname gemaakt. | De basis-BLOB wordt verplaatst naar de nieuwe laag. Actieve of voorlopig verwijderde versies blijven in de oorspronkelijke laag. Er wordt geen nieuwe versie gemaakt. |

## <a name="pricing-and-billing"></a>Prijzen en facturering

Alle voorlopig verwijderde gegevens worden gefactureerd tegen hetzelfde aantal als actieve gegevens. Er worden geen kosten in rekening gebracht voor gegevens die permanent worden verwijderd nadat de Bewaar periode is verstreken.

Wanneer u voorlopig verwijderen inschakelt, raadt micro soft aan een korte Bewaar periode te gebruiken om beter te begrijpen hoe de functie uw factuur beïnvloedt. De minimale aanbevolen Bewaar periode is zeven dagen.

Het inschakelen van zacht verwijderen voor vaak overschreven gegevens kan leiden tot hogere kosten voor opslag capaciteit en een grotere latentie bij het weer geven van blobs. U kunt deze extra kosten en latentie verminderen door de vaak overschreven gegevens op te slaan in een afzonderlijk opslag account, waar zacht verwijderen is uitgeschakeld.

Er worden geen kosten in rekening gebracht voor trans acties die betrekking hebben op het automatisch genereren van moment opnamen of versies wanneer een BLOB wordt overschreven of verwijderd. U wordt gefactureerd voor aanroepen van de bewerking **BLOB verwijderen** tegen de transactie snelheid voor schrijf bewerkingen.

Zie de pagina met [Blob Storage prijzen](https://azure.microsoft.com/pricing/details/storage/blobs/) voor meer informatie over de prijzen voor Blob Storage.

## <a name="blob-soft-delete-and-virtual-machine-disks"></a>Voorlopig verwijderen van blobs en virtuele-machine schijven  

Het voorlopig verwijderen van blobs is beschikbaar voor zowel Premium als standaard niet-beheerde schijven. Dit zijn pagina-blobs onder de kaften. Met de functie voor voorlopig verwijderen kunt u gegevens herstellen die worden verwijderd of overschreven door de **BLOB verwijderen**, **BLOB** maken, **blokkerings lijst plaatsen** en BLOB-bewerkingen **kopiëren** .

Gegevens die worden overschreven door een aanroep naar de **put-pagina** , kunnen niet worden hersteld. Een virtuele machine van Azure schrijft naar een niet-beheerde schijf met behulp van aanroepen om de **pagina te plaatsen**. met de opdracht voorlopig verwijderen kunt u schrijf bewerkingen naar een niet-beheerde schijf van een Azure-VM ongedaan maken.

## <a name="next-steps"></a>Volgende stappen

- [Voorlopig verwijderen inschakelen voor blobs](./soft-delete-blob-enable.md)
- [Tijdelijke verwijderde blobs beheren en herstellen](soft-delete-blob-manage.md)
- [BLOB-versie beheer](versioning-overview.md)