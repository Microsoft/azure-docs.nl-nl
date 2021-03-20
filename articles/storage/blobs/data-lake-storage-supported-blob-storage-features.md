---
title: Blob-opslag functies die beschikbaar zijn in Azure Data Lake Storage Gen2 | Microsoft Docs
description: Meer informatie over de functies van Blob Storage die u kunt gebruiken met Azure Data Lake Storage Gen2
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 02/11/2021
ms.author: normesta
ms.openlocfilehash: 769163dd09ec29c2c206d0273e71de27732fb9db
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100516275"
---
# <a name="blob-storage-features-available-in-azure-data-lake-storage-gen2"></a>Blob Storage-functies die beschikbaar zijn in Azure Data Lake Storage Gen2

Blob Storage functies als [Diagnostische logboek registratie](../common/storage-analytics-logging.md), [toegangs lagen](storage-blob-storage-tiers.md)en [Blob Storage levenscyclus beheer beleid](storage-lifecycle-management-concepts.md) werken nu met accounts die een hiërarchische naam ruimte hebben. U kunt dus hiërarchische naamruimten inschakelen op uw Blob Storage-accounts zonder toegang tot deze functies te verliezen.

Deze tabel geeft een lijst van de functies voor Blob-opslag die u kunt gebruiken met Azure Data Lake Storage Gen2. De items die in deze tabellen worden weer gegeven, worden in de loop van de tijd gewijzigd, omdat de ondersteuning blijft toenemen. Zie het artikel [bekende problemen](data-lake-storage-known-issues.md) voor meer informatie over specifieke problemen met betrekking tot de preview-status van een functie.

## <a name="supported-blob-storage-features"></a>Ondersteunde Blob Storage-functies

De volgende tabel laat zien hoe elke Blob Storage-functie wordt ondersteund met Data Lake Storage Gen2. Er is een kolom voor de prestatie lagen Standard en [Premium](premium-tier-for-data-lake-storage.md) . 

|Blob Storage functie |Standard |Premium |Verwante artikelen: |
|---------------|-------------------|---|
|Dynamische-toegangslaag|Algemeen beschikbaar|Niet ondersteund|[Azure Blob-opslag: dynamische en statische toegangslagen, en archieftoegangslaag](storage-blob-storage-tiers.md)|
|Statische-toegangslaag|Algemeen beschikbaar|Niet ondersteund|[Azure Blob-opslag: dynamische en statische toegangslagen, en archieftoegangslaag](storage-blob-storage-tiers.md)|
|Gebeurtenissen|Algemeen beschikbaar|Algemeen beschikbaar|[Reageren op gebeurtenissen van Blob Storage](storage-blob-event-overview.md)|
|Metrische gegevens (klassiek)|Algemeen beschikbaar|Algemeen beschikbaar|[Azure Storage Analytics-metrische gegevens (klassiek)](../common/storage-analytics-metrics.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Metrische gegevens in Azure Monitor|Algemeen beschikbaar|Preview|[Metrische gegevens van Azure Storage in Azure Monitor](./monitor-blob-storage.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Power shell-opdrachten voor Blob-opslag|Algemeen beschikbaar|Algemeen beschikbaar|[Quickstart: Blobs uploaden, downloaden en vermelden met PowerShell](storage-quickstart-blobs-powershell.md)|
|Azure CLI-opdrachten voor Blob Storage|Algemeen beschikbaar|Algemeen beschikbaar|[Quickstart: Blobs maken, downloaden, uploaden en weergeven met Azure CLI](storage-quickstart-blobs-cli.md)|
|Api's voor Blob-opslag|Algemeen beschikbaar|Algemeen beschikbaar|[Snelstart: De Azure Blob Storage-clientbibliotheek v12 voor .NET](storage-quickstart-blobs-dotnet.md)<br>[Quickstart: Blobs beheren met Java v12 SDK](storage-quickstart-blobs-java.md)<br>[Quickstart: Blobs beheren met Python v12 SDK](storage-quickstart-blobs-python.md)<br>[Quickstart: Blobs beheren met JavaScript v12 SDK in Node.js](storage-quickstart-blobs-nodejs.md)|
|Diagnostische logboeken|Algemeen beschikbaar|Preview |[Azure Storage-analyselogboeken](../common/storage-analytics-logging.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Access-laag archiveren|Algemeen beschikbaar|Niet ondersteund|[Azure Blob-opslag: dynamische en statische toegangslagen, en archieftoegangslaag](storage-blob-storage-tiers.md)|
|Levenscyclus beheer beleid (lagen)|Algemeen beschikbaar|Nog niet ondersteund|[De levenscyclus van Azure Blob-opslag beheren](storage-lifecycle-management-concepts.md)|
|Levenscyclus beheer beleid (BLOB verwijderen)|Algemeen beschikbaar|Algemeen beschikbaar|[De levenscyclus van Azure Blob-opslag beheren](storage-lifecycle-management-concepts.md)|
|Aanmelden Azure Monitor|Preview |Preview|[Bewakings Azure Storage](./monitor-blob-storage.md)|
|Momentopnamen|Preview<div role="complementary" aria-labelledby="preview-form"><sup>1</sup></div>|Preview<div role="complementary" aria-labelledby="preview-form"><sup>1</sup></div>|[BLOB-moment opnamen](snapshots-overview.md)|
|Statische websites|Preview<div role="complementary" aria-labelledby="preview-form"><sup>1</sup></div>|Preview<div role="complementary" aria-labelledby="preview-form"><sup>1</sup></div>|[Een statische website hosten in Azure Storage](storage-blob-static-website.md)|
|Onveranderbare opslag|Preview<div role="complementary" aria-labelledby="preview-form"><sup>1</sup></div>|Preview<div role="complementary" aria-labelledby="preview-form"><sup>1</sup></div>|[Bedrijfskritieke blobgegevens opslaan met onveranderlijke opslag](storage-blob-immutable-storage.md)|
|Container zacht verwijderen|Preview|Preview|[Voorlopig verwijderen voor containers (preview-versie)](soft-delete-container-overview.md)|
|Azure Storage voorraad|Preview|Preview|[Azure Storage-inventaris gebruiken voor het beheren van BLOB-gegevens (preview)](blob-inventory.md)|
|Aangepaste domeinen|Preview<div role="complementary" aria-labelledby="preview-form-2"><sup>2</sup></div>|Preview<div role="complementary" aria-labelledby="preview-form-2"><sup>2</sup></div>|[Een aangepast domein toewijzen aan een Azure Blob Storage-eind punt](storage-custom-domain-name.md)|
|BLOB zacht verwijderen|Nog niet ondersteund|Nog niet ondersteund|[Blobs voorlopig verwijderen](./soft-delete-blob-overview.md)|
|Blobfuse|Algemeen beschikbaar|Algemeen beschikbaar|[Blob-opslag koppelen als een bestands systeem met blobfuse](storage-how-to-mount-container-linux.md)|
|Anonieme toegang voor iedereen |Algemeen beschikbaar|Algemeen beschikbaar| Zie [anonieme open bare Lees toegang configureren voor containers en blobs](anonymous-read-access-configure.md).|
|Door de klant beheerde account-failover|Nog niet ondersteund|Nog niet ondersteund|[Herstel na nood gevallen en failover van accounts](../common/storage-disaster-recovery-guidance.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Door de klant verschafte sleutels|Nog niet ondersteund|Nog niet ondersteund|[Geef een versleutelings sleutel op voor een aanvraag voor Blob-opslag](encryption-customer-provided-keys.md)|
|Versleutelingsbereiken|Nog niet ondersteund|Nog niet ondersteund|[Versleutelings bereiken maken en beheren (preview-versie)](encryption-scope-manage.md)|
|Feed wijzigen|Nog niet ondersteund|Nog niet ondersteund|[Ondersteuning voor feed wijzigen in Azure Blob-opslag](storage-blob-change-feed.md)|
|Objectreplicatie|Nog niet ondersteund|Nog niet ondersteund|[Object replicatie configureren voor blok-blobs](object-replication-configure.md)|
|BLOB-versie beheer|Nog niet ondersteund|Nog niet ondersteund|[BLOB-versie beheer inschakelen en beheren](versioning-enable.md)|

<div id="preview-form"><sup>1</sup> Als u moment opnamen, onveranderlijke opslag of statische websites met Data Lake Storage Gen2 wilt gebruiken, moet u dit <a href=https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR2EUNXd_ZNJCq_eDwZGaF5VUOUc3NTNQSUdOTjgzVUlVT1pDTzU4WlRKRy4u>formulier</a>invullen in de preview-versie.  </div>
<div id="preview-form-2"><sup>2</sup> Een aangepaste domein naam kan alleen worden toegewezen aan het eind punt van de BLOB-service of een statisch website. Het Data Lake Storage-eind punt wordt niet </a> ondersteund.  </div>

## <a name="see-also"></a>Zie ook

- [Bekende problemen met Azure Data Lake Storage Gen2](data-lake-storage-known-issues.md)
- [Azure-Services die ondersteuning bieden voor Azure Data Lake Storage Gen2](data-lake-storage-supported-azure-services.md)
- [Open-source platforms die ondersteuning bieden voor Azure Data Lake Storage Gen2](data-lake-storage-supported-open-source-platforms.md)
- [Toegang met meerdere protocollen in Azure Data Lake Storage](data-lake-storage-multi-protocol-access.md)