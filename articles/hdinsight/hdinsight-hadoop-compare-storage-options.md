---
title: Opslag opties vergelijken voor gebruik met Azure HDInsight-clusters
description: Biedt een overzicht van opslag typen en hoe deze werken met Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: seoapr2020
ms.date: 04/21/2020
ms.openlocfilehash: 34eeeed2b3c44336cd4aa1219d54b1811c6988f5
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/20/2020
ms.locfileid: "94952315"
---
# <a name="compare-storage-options-for-use-with-azure-hdinsight-clusters"></a>Opslag opties vergelijken voor gebruik met Azure HDInsight-clusters

U kunt kiezen tussen een aantal verschillende Azure Storage-services bij het maken van HDInsight-clusters:

* [Azure Blob-opslag met HDInsight](./overview-azure-storage.md)
* [Azure Data Lake Storage Gen2 met HDInsight](./overview-data-lake-storage-gen2.md)
* [Azure Data Lake Storage Gen1 met HDInsight](./overview-data-lake-storage-gen1.md)

Dit artikel bevat een overzicht van deze opslag typen en de bijbehorende unieke functies.

## <a name="storage-types-and-features"></a>Opslag typen en-functies

De volgende tabel bevat een overzicht van de Azure Storage services die worden ondersteund door verschillende versies van HDInsight:

| Opslag service | Accounttype | Type naam ruimte | Ondersteunde services | Ondersteunde prestatielagen | Ondersteunde toegangslagen | HDInsight-versie | Clustertype |
|---|---|---|---|---|---|---|---|
|Azure Data Lake Storage Gen2| Algemeen v2 | Hiërarchisch (bestands systeem) | Blob | Standard | Dynamisch, statisch en archiefopslag | 3.6 + | Alle behalve Spark 2,1 en 2,2|
|Azure Storage| Algemeen v2 | Object | Blob | Standard | Dynamisch, statisch en archiefopslag | 3.6 + | Alles |
|Azure Storage| Algemeen v1 | Object | Blob | Standard | N.v.t. | Alles | Alles |
|Azure Storage| Blob Storage * * | Object | Blok-BLOB | Standard | Dynamisch, statisch en archiefopslag | Alles | Alles |
|Azure Data Lake Storage Gen1| N.v.t. | Hiërarchisch (bestands systeem) | N.v.t. | N.v.t. | N.v.t. | alleen 3,6 | Alle behalve HBase |
|Azure Storage| Blok-BLOB| Object | Blok-BLOB | Premium | N.v.t.| 3.6 + | Alleen HBase met versnelde schrijf bewerkingen|
|Azure Data Lake Storage Gen2| Blok-BLOB| Hiërarchisch (bestands systeem) | Blok-BLOB | Premium | N.v.t.| 3.6 + | Alleen HBase met versnelde schrijf bewerkingen|

* * Voor HDInsight-clusters kunnen alleen secundaire opslag accounts van het type BlobStorage en page BLOB worden ondersteund.

Zie [overzicht van Azure Storage-accounts](../storage/common/storage-account-overview.md) voor meer informatie over Azure Storage-account typen

Zie [Azure Blob-opslag: Premium (preview), hot, cool en archief opslag lagen](../storage/blobs/storage-blob-storage-tiers.md) voor meer informatie over Azure Storage toegangs lagen.

U kunt clusters maken met behulp van combi Naties van services voor primaire en optionele secundaire opslag. De volgende tabel bevat een overzicht van de configuraties voor cluster opslag die momenteel worden ondersteund in HDInsight:

| HDInsight-versie | Primaire opslag | Secundaire opslag | Ondersteund |
|---|---|---|---|
| 3,6 & 4,0 | Algemeen v1, Algemeen v2 | Algemeen v1, Algemeen v2, BlobStorage (blok-blobs) | Ja |
| 3,6 & 4,0 | Algemeen v1, Algemeen v2 | Data Lake Storage Gen2 | Nee |
| 3,6 & 4,0 | Data Lake Storage Gen2 * | Data Lake Storage Gen2 | Ja |
| 3,6 & 4,0 | Data Lake Storage Gen2 * | Algemeen v1, Algemeen v2, BlobStorage (blok-blobs) | Ja |
| 3,6 & 4,0 | Data Lake Storage Gen2 | Data Lake Storage Gen1 | Nee |
| 3,6 | Data Lake Storage Gen1 | Data Lake Storage Gen1 | Ja |
| 3,6 | Data Lake Storage Gen1 | Algemeen v1, Algemeen v2, BlobStorage (blok-blobs) | Ja |
| 3,6 | Data Lake Storage Gen1 | Data Lake Storage Gen2 | Nee |
| 4,0 | Data Lake Storage Gen1 | Elk | Nee |
| 4,0 | Algemeen v1, Algemeen v2 | Data Lake Storage Gen1 | Nee |

* = Dit kan een of meer Data Lake Storage Gen2 zijn, zolang alle instellingen dezelfde beheerde identiteit voor toegang tot het cluster gebruiken.

> [!NOTE]
> Data Lake Storage Gen2 primaire opslag wordt niet ondersteund voor Spark 2,1-of 2,2-clusters.

## <a name="data-replication"></a>Gegevensreplicatie

Er worden door Azure HDInsight geen klant gegevens opgeslagen. De primaire opslag ruimte voor een cluster zijn gekoppelde opslag accounts. U kunt uw cluster koppelen aan een bestaand opslag account of een nieuw opslag account maken tijdens het maken van het cluster. Als er een nieuw account wordt gemaakt, wordt dit gemaakt als een lokaal redundante opslag account (LRS) en voldoen aan de in-Region gegevens locatie vereisten, waaronder die in het [vertrouwens centrum](https://azuredatacentermap.azurewebsites.net).

U kunt valideren dat HDInsight correct is geconfigureerd voor het opslaan van gegevens in één regio door ervoor te zorgen dat het opslag account dat is gekoppeld aan uw HDInsight LRS of een andere opslag optie vermeld in het [vertrouwens centrum](https://azuredatacentermap.azurewebsites.net).
 
## <a name="next-steps"></a>Volgende stappen

* [Azure Storage overzicht in HDInsight](./overview-azure-storage.md)
* [Azure Data Lake Storage Gen1 overzicht in HDInsight](./overview-data-lake-storage-gen1.md)
* [Azure Data Lake Storage Gen2 overzicht in HDInsight](./overview-data-lake-storage-gen2.md)
* [Inleiding tot Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md)
* [Kennismaking met Azure Storage](../storage/common/storage-introduction.md)
