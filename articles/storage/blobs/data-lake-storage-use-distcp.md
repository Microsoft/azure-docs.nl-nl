---
title: Gegevens naar Azure Data Lake Storage Gen2 kopiëren met behulp van DistCp | Microsoft Docs
description: Gegevens kopiëren van en naar Azure Data Lake Storage Gen2 met behulp van het Apache Hadoop Distributed copy tool (DistCp).
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: how-to
ms.date: 12/06/2018
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: e69a97a86a357fb36dde572f292b5cac7963d14a
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "95912481"
---
# <a name="use-distcp-to-copy-data-between-azure-storage-blobs-and-azure-data-lake-storage-gen2"></a>Gebruik DistCp om gegevens te kopiëren tussen Azure Storage blobs en Azure Data Lake Storage Gen2

U kunt [DistCp](https://hadoop.apache.org/docs/stable/hadoop-distcp/DistCp.html) gebruiken om gegevens te kopiëren tussen een algemeen doel-v2-opslag account en een opslag account voor algemeen gebruik v2 met een hiërarchische naam ruimte ingeschakeld. In dit artikel vindt u instructies voor het gebruik van het hulp programma DistCp.

DistCp biedt diverse opdracht regel parameters en we raden u ten zeerste aan om dit artikel te lezen, zodat u het gebruik ervan kunt optimaliseren. Dit artikel bevat de basis functionaliteit en is gericht op het gebruik voor het kopiëren van gegevens naar een hiërarchische naam ruimte met ingeschakeld account.

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. Zie [Gratis proefversie van Azure ophalen](https://azure.microsoft.com/pricing/free-trial/).
* Een bestaand Azure Storage-account zonder Data Lake Storage Gen2 mogelijkheden (hiërarchische naam ruimte) ingeschakeld.
* Een Azure Storage account met Data Lake Storage Gen2 mogelijkheden (hiërarchische naam ruimte) ingeschakeld. Zie [een Azure Storage-account maken](../common/storage-account-create.md) voor meer informatie over het maken van deze.
* Een container die is gemaakt in het opslag account waarvoor een hiërarchische naam ruimte is ingeschakeld.
* Een Azure HDInsight-cluster met toegang tot een opslag account waarvoor de functie voor hiërarchische naam ruimte is ingeschakeld. Zie [Azure Data Lake Storage Gen2 gebruiken met Azure HDInsight-clusters](../../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json). Zorg ervoor dat Extern bureaublad voor het cluster is ingeschakeld.

## <a name="use-distcp-from-an-hdinsight-linux-cluster"></a>DistCp gebruiken vanuit een HDInsight Linux-cluster

An HDInsight cluster wordt geleverd met het hulp programma DistCp, dat kan worden gebruikt voor het kopiëren van gegevens uit verschillende bronnen naar een HDInsight-cluster. Als u het HDInsight-cluster hebt geconfigureerd voor het gebruik van Azure Blob Storage en Azure Data Lake Storage samen, kan het hulp programma DistCp out-of-the-box worden gebruikt voor het kopiëren van gegevens. In deze sectie kijken we naar het gebruik van het DistCp-hulp programma.

1. Maak een SSH-sessie met uw HDI-cluster. Zie [verbinding maken met een HDInsight-cluster op basis van Linux](../../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md).

2. Controleer of u toegang hebt tot uw bestaande algemeen gebruik v2-account (zonder hiërarchische naam ruimte ingeschakeld).

    ```bash
    hdfs dfs –ls wasbs://<container-name>@<storage-account-name>.blob.core.windows.net/
    ```

   De uitvoer moet een lijst met inhoud in de container bevatten.

3. U kunt ook controleren of u toegang hebt tot het opslag account met een hiërarchische naam ruimte die is ingeschakeld in het cluster. Voer de volgende opdracht uit:

    ```bash
    hdfs dfs -ls abfss://<container-name>@<storage-account-name>.dfs.core.windows.net/
    ```

    De uitvoer moet een lijst met bestanden/mappen in het opslag account van Data Lake bevatten.

4. Gebruik DistCp om gegevens van WASB te kopiëren naar een Data Lake Storage-account.

    ```bash
    hadoop distcp wasbs://<container-name>@<storage-account-name>.blob.core.windows.net/example/data/gutenberg abfss://<container-name>@<storage-account-name>.dfs.core.windows.net/myfolder
    ```

    Met de opdracht wordt de inhoud van de map **/example/data/Gutenberg/** in Blob Storage gekopieerd naar **/myfolder** in het data Lake Storage-account.

5. Gebruik DistCp om gegevens te kopiëren van Data Lake Storage account naar Blob Storage (WASB).

    ```bash
    hadoop distcp abfss://<container-name>@<storage-account-name>.dfs.core.windows.net/myfolder wasbs://<container-name>@<storage-account-name>.blob.core.windows.net/example/data/gutenberg
    ```

    De opdracht kopieert de inhoud van **/MyFolder** in de map Data Lake Store account naar **/example/data/Gutenberg/** in WASB.

## <a name="performance-considerations-while-using-distcp"></a>Prestatie overwegingen bij het gebruik van DistCp

Omdat de laagste granulatie van DistCp één bestand is, is het instellen van het maximum aantal gelijktijdige kopieën de belangrijkste para meter om deze te optimaliseren ten opzichte van Data Lake Storage. Het aantal gelijktijdige kopieën is gelijk aan de para meter aantal mappers (**m**) op de opdracht regel. Met deze para meter geeft u het maximum aantal mappers op dat wordt gebruikt om gegevens te kopiëren. De standaard waarde is 20.

**Voorbeeld**

```bash
hadoop distcp -m 100 wasbs://<container-name>@<storage-account-name>.blob.core.windows.net/example/data/gutenberg abfss://<container-name>@<storage-account-name>.dfs.core.windows.net/myfolder
```

### <a name="how-do-i-determine-the-number-of-mappers-to-use"></a>Hoe kan ik het aantal mappers bepalen dat moet worden gebruikt?

Hier volgen een aantal richtlijnen.

* **Stap 1: Bepaal het totale geheugen dat beschikbaar is voor de ' standaard '** coactieve app-wachtrij: de eerste stap is het bepalen van het geheugen dat beschikbaar is voor de standaard-app-wachtrij voor garens. Deze informatie is beschikbaar in de Ambari-portal die aan het cluster is gekoppeld. Navigeer naar GARENs en Bekijk het tabblad Configuratie om het garen geheugen weer te geven dat beschikbaar is voor de standaard-app-wachtrij. Dit is het totale beschik bare geheugen voor uw DistCp-taak (dit is eigenlijk een MapReduce-taak).

* **Stap 2: het aantal mappers berekenen** . de waarde van **m** is gelijk aan het QUOTIËNT van het totale garen geheugen gedeeld door de grootte van de garen container. De informatie over de grootte van de garen container is ook beschikbaar in de Ambari-Portal. Navigeer naar GARENs en Bekijk het tabblad Configuratie. De grootte van de garen container wordt in dit venster weer gegeven. De vergelijking voor het aantal mappers (**m**) is

    m = (aantal knoop punten * garen geheugen voor elk knoop punt)/garen container grootte

**Voorbeeld**

We gaan ervan uit dat u een 4x D14v2s-cluster hebt en u probeert 10 TB aan gegevens over te brengen van tien verschillende mappen. Elk van de mappen bevat verschillende hoeveel heden gegevens en de bestands grootten in elke map verschillen.

* **Totaal aantal garens**: vanuit de Ambari-Portal hebt u vastgesteld dat het garen geheugen 96 GB is voor een D14-knoop punt. Dit betekent dat het totale garen geheugen voor het cluster van vier knoop punten: 

    GAREN geheugen = 4 * 96GB = 384GB

* **Aantal mappers**: vanuit de Ambari-Portal hebt u vastgesteld dat de grootte van de garen container 3.072 MB is voor een D14-cluster knooppunt. Het aantal mappers is dus:

    m = (4 knoop punten * 96GB)/3072MB = 128 mappers

Als andere toepassingen geheugen gebruiken, kunt u ervoor kiezen om alleen een deel van het garen geheugen van uw cluster te gebruiken voor DistCp.

### <a name="copying-large-datasets"></a>Grote gegevens sets kopiëren

Wanneer de grootte van de gegevensset die moet worden verplaatst, groot is (bijvoorbeeld >1 TB) of als u veel verschillende mappen hebt, kunt u overwegen meerdere DistCp-taken te gebruiken. Er is waarschijnlijk geen prestatie verbetering, maar deze verdeelt de taken, zodat als een taak mislukt, u de specifieke taak alleen opnieuw hoeft te starten in plaats van de volledige taak.

### <a name="limitations"></a>Beperkingen

* DistCp probeert toewijzingen te maken die vergelijkbaar zijn met de grootte om de prestaties te optimaliseren. Het verhogen van het aantal mappers leidt mogelijk niet altijd tot betere prestaties.

* DistCp is beperkt tot slechts één Mapper per bestand. Daarom moet u niet meer toewijzingen hebben dan er bestanden zijn. Aangezien DistCp slechts één Mapper aan een bestand kan toewijzen, wordt hiermee de hoeveelheid gelijktijdigheid beperkt die kan worden gebruikt voor het kopiëren van grote bestanden.

* Als u een klein aantal grote bestanden hebt, moet u deze opsplitsen in bestands segmenten van 256 MB om u meer potentiële gelijktijdigheid te geven.