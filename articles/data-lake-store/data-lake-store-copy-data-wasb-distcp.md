---
title: Gegevens kopiëren van en naar WASB naar Azure Data Lake Storage Gen1 met behulp van DistCp
description: Gebruik het hulp programma DistCp om gegevens te kopiëren van en naar Azure Storage-blobs naar Azure Data Lake Storage Gen1
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 01/03/2020
ms.author: twooley
ms.openlocfilehash: c608f357eb1eff9fd36e583b98d26250a71cb923
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "85515672"
---
# <a name="use-distcp-to-copy-data-between-azure-storage-blobs-and-azure-data-lake-storage-gen1"></a>Gebruik DistCp om gegevens te kopiëren tussen Azure Storage blobs en Azure Data Lake Storage Gen1

> [!div class="op_single_selector"]
> * [DistCp gebruiken](data-lake-store-copy-data-wasb-distcp.md)
> * [AdlCopy gebruiken](data-lake-store-copy-data-azure-storage-blob.md)
>
>

Als u een HDInsight-cluster hebt met toegang tot Azure Data Lake Storage Gen1, kunt u Hadoop ecosysteem-hulpprogram ma's zoals DistCp gebruiken om gegevens te kopiëren van en naar een HDInsight-cluster opslag (WASB) naar een Data Lake Storage Gen1-account. In dit artikel wordt beschreven hoe u het DistCp-hulp programma gebruikt.

## <a name="prerequisites"></a>Vereisten

* **Een Azure-abonnement**. Zie [Gratis proefversie van Azure ophalen](https://azure.microsoft.com/pricing/free-trial/).
* **Een Azure data Lake Storage gen1-account**. Zie [aan de slag met Azure data Lake Storage gen1](data-lake-store-get-started-portal.md)voor instructies over het maken van een account.
* **Azure HDInsight-cluster** met toegang tot een Data Lake Storage gen1-account. Zie [een HDInsight-cluster met data Lake Storage gen1 maken](data-lake-store-hdinsight-hadoop-use-portal.md). Zorg ervoor dat Extern bureaublad voor het cluster is ingeschakeld.

## <a name="use-distcp-from-an-hdinsight-linux-cluster"></a>DistCp gebruiken vanuit een HDInsight Linux-cluster

An HDInsight cluster wordt geleverd met het DistCp-hulp programma dat kan worden gebruikt voor het kopiëren van gegevens uit verschillende bronnen naar een HDInsight-cluster. Als u het HDInsight-cluster hebt geconfigureerd voor het gebruik van Data Lake Storage Gen1 als extra opslag, kunt u DistCp out-of-the-box gebruiken om gegevens te kopiëren van en naar een Data Lake Storage Gen1-account. In deze sectie kijken we naar het gebruik van het DistCp-hulp programma.

1. Gebruik op uw bureau blad SSH om verbinding te maken met het cluster. Zie [verbinding maken met een HDInsight-cluster op basis van Linux](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md). Voer de opdrachten uit vanaf de SSH-prompt.

1. Controleer of u toegang hebt tot de Azure Storage-blobs (WASB). Voer de volgende opdracht uit:

   ```
   hdfs dfs –ls wasb://<container_name>@<storage_account_name>.blob.core.windows.net/
   ```

   De uitvoer bevat een lijst met inhoud in de opslag-blob.

1. U kunt ook controleren of u toegang hebt tot het Data Lake Storage Gen1-account vanuit het cluster. Voer de volgende opdracht uit:

   ```
   hdfs dfs -ls adl://<data_lake_storage_gen1_account>.azuredatalakestore.net:443/
   ```

    De uitvoer bevat een lijst met bestanden en mappen in het Data Lake Storage Gen1-account.

1. Gebruik DistCp om gegevens van WASB te kopiëren naar een Data Lake Storage Gen1-account.

   ```
   hadoop distcp wasb://<container_name>@<storage_account_name>.blob.core.windows.net/example/data/gutenberg adl://<data_lake_storage_gen1_account>.azuredatalakestore.net:443/myfolder
   ```

    De opdracht kopieert de inhoud van de map **/example/data/Gutenberg/** in WASB naar **/myfolder** in het data Lake Storage gen1-account.

1. Op dezelfde manier kunt u DistCp gebruiken om gegevens van een Data Lake Storage Gen1-account naar WASB te kopiëren.

   ```
   hadoop distcp adl://<data_lake_storage_gen1_account>.azuredatalakestore.net:443/myfolder wasb://<container_name>@<storage_account_name>.blob.core.windows.net/example/data/gutenberg
   ```

    De opdracht kopieert de inhoud van **/MyFolder** in de map Data Lake Storage gen1 account naar **/example/data/Gutenberg/** in WASB.

## <a name="performance-considerations-while-using-distcp"></a>Prestatie overwegingen bij het gebruik van DistCp

Omdat de laagste granulatie van het DistCp-hulp programma één bestand is, is het instellen van het maximum aantal gelijktijdige kopieën de belangrijkste para meter om deze te optimaliseren ten opzichte van Data Lake Storage Gen1. U kunt het aantal gelijktijdige kopieën bepalen door de para meter aantal mappers (') in te stellen op de opdracht regel. Met deze para meter geeft u het maximum aantal mappers op dat wordt gebruikt om gegevens te kopiëren. De standaard waarde is 20.

Voorbeeld:

```
 hadoop distcp wasb://<container_name>@<storage_account_name>.blob.core.windows.net/example/data/gutenberg adl://<data_lake_storage_gen1_account>.azuredatalakestore.net:443/myfolder -m 100
```

### <a name="how-to-determine-the-number-of-mappers-to-use"></a>Bepalen welk aantal mapperen moet worden gebruikt

Hier volgen een aantal richtlijnen.

* **Stap 1: het totale aantal garens bepalen** -de eerste stap is het bepalen van het garen geheugen dat beschikbaar is voor het cluster waarop u de DistCp-taak uitvoert. Deze informatie is beschikbaar in de Ambari-portal die aan het cluster is gekoppeld. Navigeer naar GARENs en Bekijk het tabblad **configs** om het garen geheugen weer te geven. Als u het totale aantal GARENs wilt bepalen, vermenigvuldigt u het garen geheugen per knoop punt met het aantal knoop punten dat u in uw cluster hebt.

* **Stap 2: het aantal mappers berekenen** . de waarde van **m** is gelijk aan het QUOTIËNT van het totale garen geheugen gedeeld door de grootte van de garen container. De informatie over de grootte van de garen container is ook beschikbaar in de Ambari-Portal. Navigeer naar GARENs en Bekijk het tabblad **configuratie** . De grootte van de garen container wordt in dit venster weer gegeven. De vergelijking voor het aantal mappers (**m**) is:

   `m = (number of nodes * YARN memory for each node) / YARN container size`

Voorbeeld:

Stel dat u over vier D14v2s-knoop punten in het cluster beschikt en u 10 TB aan gegevens van tien verschillende mappen wilt overdragen. Elk van de mappen bevat verschillende hoeveel heden gegevens en de bestands grootten in elke map verschillen.

* Totaal aantal GARENs: in de Ambari-Portal hebt u vastgesteld dat het garen geheugen 96 GB is voor een D14-knoop punt. Dit betekent dat het totale garen geheugen voor het cluster van vier knoop punten:

   `YARN memory = 4 * 96GB = 384GB`

* Aantal mappers: vanuit de Ambari-Portal hebt u vastgesteld dat de grootte van de garen container 3072 is voor een D14-cluster knooppunt. Het aantal mappers is dus:

   `m = (4 nodes * 96GB) / 3072MB = 128 mappers`

Als andere toepassingen gebruikmaken van geheugen, kunt u ervoor kiezen om alleen een deel van het garen geheugen van uw cluster te gebruiken voor DistCp.

### <a name="copying-large-datasets"></a>Grote gegevens sets kopiëren

Wanneer de grootte van de gegevensset die moet worden verplaatst, groot is (bijvoorbeeld > 1 TB) of als u veel verschillende mappen hebt, kunt u overwegen meerdere DistCp-taken te gebruiken. Er is waarschijnlijk geen prestatie verbetering, maar deze verdeelt de taken zo dat als een taak mislukt, alleen die specifieke taak opnieuw moet worden gestart in plaats van de volledige taak.

### <a name="limitations"></a>Beperkingen

* DistCp probeert toewijzingen te maken die vergelijkbaar zijn met de grootte om de prestaties te optimaliseren. Het verhogen van het aantal mappers leidt mogelijk niet altijd tot betere prestaties.

* DistCp is beperkt tot slechts één Mapper per bestand. Daarom moet u niet meer toewijzingen hebben dan er bestanden zijn. Omdat DistCp slechts één Mapper aan een bestand kan toewijzen, wordt hiermee de hoeveelheid gelijktijdigheid beperkt die kan worden gebruikt voor het kopiëren van grote bestanden.

* Als u een klein aantal grote bestanden hebt, splitst u deze in bestands segmenten van 256 MB om u meer potentiële gelijktijdigheid te geven.

* Als u kopieert vanuit een Azure Blob-opslag account, wordt uw Kopieer taak mogelijk beperkt op basis van de Blob-opslag. Dit vermindert de prestaties van uw Kopieer taak. Zie Azure Storage limieten bij [Azure-abonnement en service limieten](../azure-resource-manager/management/azure-subscription-service-limits.md)voor meer informatie over de limieten van Azure Blob-opslag.

## <a name="see-also"></a>Zie ook

* [Gegevens kopiëren van Azure Storage-blobs naar Data Lake Storage Gen1](data-lake-store-copy-data-azure-storage-blob.md)
* [Gegevens beveiligen in Data Lake Storage Gen1](data-lake-store-secure-data.md)
* [Azure Data Lake Analytics gebruiken met Data Lake Storage Gen1](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Azure HDInsight gebruiken met Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md)
