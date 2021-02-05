---
title: Bekende problemen met Azure Data Lake Storage Gen2 | Microsoft Docs
description: Meer informatie over beperkingen en bekende problemen met Azure Data Lake Storage Gen2.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 02/04/2021
ms.author: normesta
ms.reviewer: jamesbak
ms.openlocfilehash: db55a29e53c53e26fbb04d96ddf1864946bafbfd
ms.sourcegitcommit: 2817d7e0ab8d9354338d860de878dd6024e93c66
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/05/2021
ms.locfileid: "99581275"
---
# <a name="known-issues-with-azure-data-lake-storage-gen2"></a>Bekende problemen met Azure Data Lake Storage Gen2

In dit artikel worden beperkingen en bekende problemen met Azure Data Lake Storage Gen2 beschreven.

## <a name="supported-blob-storage-features"></a>Ondersteunde Blob Storage-functies

Een toenemend aantal functies voor Blob Storage werkt nu met accounts die een hiërarchische naam ruimte hebben. Zie [Blob Storage-functies die beschikbaar zijn in azure data Lake Storage Gen2](data-lake-storage-supported-blob-storage-features.md)voor een volledige lijst.

## <a name="supported-azure-service-integrations"></a>Ondersteunde integraties met Azure-services

Azure Data Lake Storage Gen2 ondersteunt verschillende Azure-Services die u kunt gebruiken om gegevens op te nemen, analyses uit te voeren en visuele weer gaven te maken. Zie [Azure-services die ondersteuning bieden voor Azure Data Lake Storage Gen2](data-lake-storage-supported-azure-services.md) voor een lijst met ondersteunde Azure-services.

Zie [Azure-Services die Azure data Lake Storage Gen2 ondersteunen](data-lake-storage-supported-azure-services.md).

## <a name="supported-open-source-platforms"></a>Ondersteunde open source-platforms

Verschillende open source-platformen ondersteunen Data Lake Storage Gen2. Zie [Open source-platformen die ondersteuning bieden voor Azure Data Lake Storage Gen2](data-lake-storage-supported-open-source-platforms.md) voor een volledig overzicht.

Zie [open-source platforms die Azure data Lake Storage Gen2 ondersteunen](data-lake-storage-supported-open-source-platforms.md).

## <a name="blob-storage-apis"></a>Api's voor Blob-opslag

BLOB-Api's en Data Lake Storage Gen2-Api's kunnen op dezelfde gegevens worden gebruikt.

In deze sectie worden de problemen en beperkingen beschreven met het gebruik van BLOB-Api's en Data Lake Storage Gen2 Api's om op dezelfde gegevens te werken.

* U kunt geen blob-API en Data Lake Storage-Api's gebruiken om naar hetzelfde exemplaar van een bestand te schrijven. Als u naar een bestand schrijft met behulp van Data Lake Storage Gen2 Api's, zijn de blokken van dat bestand niet zichtbaar voor aanroepen naar de BLOB-API voor [blok keren ophalen](/rest/api/storageservices/get-block-list) . De enige uitzonde ring hierop is wanneer u overschrijft. U kunt een bestand/BLOB overschrijven met behulp van de API.

* Wanneer u de bewerking [lijst-blobs](/rest/api/storageservices/list-blobs) gebruikt zonder een scheidings teken op te geven, bevatten de resultaten zowel directory's als blobs. Als u ervoor kiest om een scheidings teken te gebruiken, gebruikt u alleen een slash ( `/` ). Dit is het enige ondersteunde scheidings teken.

* Als u de [Delete BLOB](/rest/api/storageservices/delete-blob) API gebruikt om een map te verwijderen, wordt die map alleen verwijderd als deze leeg is. Dit betekent dat u de BLOB-API niet recursief kunt gebruiken.

Deze BLOB REST Api's worden niet ondersteund:

* [BLOB plaatsen (pagina)](/rest/api/storageservices/put-blob)
* [Pagina plaatsen](/rest/api/storageservices/put-page)
* [Paginabereiken ophalen](/rest/api/storageservices/get-page-ranges)
* [BLOB voor incrementele kopie](/rest/api/storageservices/incremental-copy-blob)
* [Pagina van URL plaatsen](/rest/api/storageservices/put-page-from-url)

Niet-beheerde VM-schijven worden niet ondersteund in accounts met een hiërarchische naam ruimte. Als u een hiërarchische naam ruimte wilt inschakelen op een opslag account, plaatst u onbeheerde VM-schijven in een opslag account waarvoor de functie hiërarchische naam ruimte niet is ingeschakeld.

<a id="api-scope-data-lake-client-library"></a>

## <a name="support-for-setting-access-control-lists-acls-recursively"></a>Ondersteuning voor het recursief instellen van toegangs beheer lijsten (Acl's)

De mogelijkheid om ACL-wijzigingen van de bovenliggende map toe te passen op onderliggende items is algemeen beschikbaar. In de huidige versie van deze mogelijkheid kunt u ACL'S-wijzigingen Toep assen met behulp van Power shell, Azure CLI en de .NET-, Java-en python-SDK. Ondersteuning is nog niet beschikbaar voor de Azure Portal, of Azure Storage Explorer.

<a id="known-issues-tools"></a>

## <a name="azcopy"></a>AzCopy

Gebruik alleen de meest recente versie van AzCopy ([AzCopy V10 toevoegen](../common/storage-use-azcopy-v10.md?toc=%2fazure%2fstorage%2ftables%2ftoc.json)). Eerdere versies van AzCopy, zoals AzCopy v 8.1, worden niet ondersteund.

<a id="storage-explorer"></a>

## <a name="azure-storage-explorer"></a>Azure Storage Explorer

Gebruik alleen versies `1.6.0` of hoger.

<a id="explorer-in-portal"></a>

## <a name="storage-explorer-in-the-azure-portal"></a>Storage Explorer in de Azure Portal

Acl's worden nog niet ondersteund.

<a id="third-party-apps"></a>

## <a name="third-party-applications"></a>Toepassingen van derden

Toepassingen van derden die gebruikmaken van REST-Api's voor werken, blijven werken als u ze gebruikt met Data Lake Storage Gen2 toepassingen die BLOB-Api's aanroepen, waarschijnlijk zullen werken.

## <a name="access-control-lists-acl-and-anonymous-read-access"></a>Toegangs beheer lijsten (ACL) en anonieme lees toegang

Als [anonieme lees toegang](./anonymous-read-access-configure.md) is verleend aan een container, hebben acl's geen invloed op die container of de bestanden in die container.

## <a name="diagnostic-logs"></a>Diagnostische logboeken

De instelling voor Bewaar dagen wordt nog niet ondersteund, maar u kunt Logboeken hand matig verwijderen met behulp van een ondersteund hulp programma zoals Azure Storage Explorer, REST of een SDK.

## <a name="lifecycle-management-policies-with-premium-tier-for-azure-data-lake-storage"></a>Levenscyclus beheer beleid met de Premium-laag voor Azure Data Lake Storage

Het is niet mogelijk om gegevens die zijn opgeslagen in de Premium-laag te verplaatsen tussen de lagen hot, cool en Archive. U kunt echter gegevens van de Premium-laag naar de warme Access-laag kopiëren in een ander account.

## <a name="dremio-support-with-premium-performance-blockblobstorage-storage-accounts"></a>Dremio-ondersteuning met Premium-performance BlockBlobStorage-opslag accounts

Dremio maakt nog geen verbinding met een BlockBlobStorage-account waarop de functie voor hiërarchische naam ruimte is ingeschakeld. 

## <a name="windows-azure-storage-blob-wasb-driver-unsupported-with-data-lake-storage-gen2"></a>Stuur programma voor Windows Azure Storage Blob (WASB) (niet ondersteund met Data Lake Storage Gen2)

Op dit moment is het WASB-stuur programma, dat is ontworpen voor gebruik met alleen de BLOB API, problemen ondervindt in enkele veelvoorkomende scenario's. In het bijzonder, wanneer het een client voor een hiërarchisch opslag account met naam ruimte is. Toegang tot meerdere protocollen op Data Lake Storage kan deze problemen niet oplossen. 

Voor de tijd (en de meest waarschijnlijke toekomst) ondersteunen we klanten die het WASB-stuur programma gebruiken niet als een client voor een hiërarchisch opslag account met naam ruimte. In plaats daarvan raden we u aan om het [ABFS-stuur programma (Azure Blob File System)](data-lake-storage-abfs-driver.md) in uw Hadoop-omgeving te gebruiken. Als u probeert te migreren van een on-premises Hadoop-omgeving met een eerdere versie dan Hadoop Branch-3, opent u een ondersteunings ticket voor Azure zodat we in contact kunnen komen met u op het juiste pad voor u en uw organisatie.