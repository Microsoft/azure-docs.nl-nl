---
title: Azure Storage overzicht in HDInsight
description: Overzicht van Azure Storage in HDInsight.
ms.service: hdinsight
ms.topic: conceptual
ms.custom: seoapr2020
ms.date: 04/21/2020
ms.openlocfilehash: 419b4939c19ee03651a406db44244897e17087f2
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104865465"
---
# <a name="azure-storage-overview-in-hdinsight"></a>Azure Storage overzicht in HDInsight

Azure Storage is een robuuste, algemene opslag oplossing die naadloos kan worden geïntegreerd met HDInsight. HDInsight kan een blobcontainer in Azure Storage gebruiken als het standaardbestandssysteem voor het cluster. Via een HDFS-interface kan de volledige set onderdelen in HDInsight rechtstreeks worden toegepast op gestructureerde of ongestructureerde gegevens die zijn opgeslagen als blobs.

We raden u aan om afzonderlijke opslag containers te gebruiken voor uw standaard cluster opslag en uw bedrijfs gegevens. De schei ding is het isoleren van de HDInsight-logboeken en tijdelijke bestanden van uw eigen zakelijke gegevens. We raden u ook aan de standaard-BLOB-container te verwijderen, die toepassings-en systeem Logboeken bevat, na elk gebruik om de opslag kosten te verlagen. Breng de logboeken over naar een andere locatie voordat u de container verwijdert.

Als u ervoor kiest om uw opslag account te beveiligen met de **firewalls en beperkingen voor virtuele netwerken** op **geselecteerde netwerken**, moet u de uitzonde ring inschakelen **vertrouwde micro soft-Services toestaan...**. De uitzonde ring hierop is dat HDInsight toegang kan krijgen tot uw opslag account.

## <a name="hdinsight-storage-architecture"></a>HDInsight-opslagarchitectuur

Het volgende diagram biedt een abstracte weer gave van de HDInsight-architectuur van Azure Storage:

:::image type="content" source="./media/overview-azure-storage/storage-architecture.png" alt-text="HDInsight Storage architectuur" border="false":::

HDInsight biedt toegang tot het Distributed File System dat lokaal wordt gekoppeld aan de rekenknooppunten. Dit bestandssysteem is toegankelijk via de volledig gekwalificeerde URI, bijvoorbeeld:

`hdfs://<namenodehost>/<path>`

Via HDInsight kunt u ook toegang krijgen tot gegevens in Azure Storage. De syntaxis is als volgt:

`wasb://<containername>@<accountname>.blob.core.windows.net/<path>`

Houd rekening met de volgende principes wanneer u een Azure Storage-account gebruikt met HDInsight-clusters:

* **Containers in de opslagaccounts die zijn verbonden met een cluster:** omdat de accountnaam en de sleutel tijdens het maken worden gekoppeld aan het cluster, hebt u volledige toegang tot de blobs in deze containers.

* **Open bare containers of open bare blobs in opslag accounts die niet zijn verbonden met een cluster:** U hebt alleen-lezen-machtiging voor de blobs in de containers.
  
  > [!NOTE]
  > Open bare containers bieden u de mogelijkheid een lijst op te halen met alle blobs die beschikbaar zijn in die container en om meta gegevens van de container op te halen. Openbare blobs zijn alleen toegankelijk als u de exacte URL weet. Zie [Anonieme leestoegang tot containers en blobs beheren](../storage/blobs/anonymous-read-access-configure.md) voor meer informatie.

* **Persoonlijke containers in opslag accounts die niet zijn verbonden met een cluster:** U hebt geen toegang tot de blobs in de containers, tenzij u het opslag account definieert wanneer u de WebHCat-taken verzendt.

De opslagaccounts die worden gedefinieerd tijdens het creatieproces en de bijbehorende sleutels worden opgeslagen in %HADOOP_HOME%/conf/core-site.xml op de clusterknooppunten. HDInsight maakt standaard gebruik van de opslag accounts die in het core-site.xml bestand zijn gedefinieerd. U kunt deze instelling wijzigen met behulp van [Apache Ambari](./hdinsight-hadoop-manage-ambari.md).

Meerdere WebHCat-taken, met inbegrip van Apache Hive. En MapReduce, Apache Hadoop streaming en Apache varken, bevatten een beschrijving van opslag accounts en meta gegevens. (Dit aspect is momenteel waar voor varkens met opslag accounts, maar niet voor meta gegevens.) Zie [een HDInsight-cluster gebruiken met alternatieve opslag accounts en meta Stores](https://social.technet.microsoft.com/wiki/contents/articles/23256.using-an-hdinsight-cluster-with-alternate-storage-accounts-and-metastores.aspx)voor meer informatie.

Blobs kunnen worden gebruikt voor gestructureerde en ongestructureerde gegevens. BLOB-containers slaan gegevens op als sleutel/waarde-paren en hebben geen Directory-hiërarchie. De naam van de sleutel kan echter een slash (/) bevatten om deze weer te geven als een bestand wordt opgeslagen in een mapstructuur. De sleutel van een BLOB kan bijvoorbeeld zijn `input/log1.txt` . Er bestaat geen daad werkelijke `input` map, maar vanwege het slash-teken in de naam van de sleutel ziet de sleutel eruit als een bestandspad.

## <a name="benefits-of-azure-storage"></a>Voordelen van Azure Storage

Reken clusters en opslag resources die niet naast zichzelf zijn ondergebracht, hebben impliciete prestatie kosten. Deze kosten worden verholpen door de manier waarop de berekenings clusters worden gemaakt dicht bij de resources van het opslag account in de Azure-regio. In deze regio kunnen de reken knooppunten efficiënt toegang krijgen tot de gegevens via het netwerk met hoge snelheid in Azure Storage.

Wanneer u de gegevens opslaat in Azure Storage in plaats van HDFS, profiteert u van verschillende voor delen:

* **Hergebruik en delen van gegevens:** de gegevens in HDFS bevinden zich in het rekencluster. Alleen de toepassingen die toegang tot het rekencluster hebben, kunnen de gegevens met HDFS API's gebruiken. De gegevens in Azure Storage daarentegen kunnen worden geopend via de HDFS-Api's of de Blob Storage REST-Api's. Vanwege deze indeling kan een grotere set toepassingen (met inbegrip van andere HDInsight-clusters) en hulpprogram ma's worden gebruikt om de gegevens te produceren en te gebruiken.

* **Gegevens archivering:** Wanneer gegevens worden opgeslagen in Azure Storage, kunnen de HDInsight-clusters die worden gebruikt voor berekeningen, veilig worden verwijderd zonder dat er gebruikers gegevens verloren gaan.

* **Kosten voor gegevens opslag:** Het opslaan van gegevens in DFS voor lange termijn is kostbaarer dan het opslaan van de gegevens in Azure Storage. Omdat de kosten van een reken cluster hoger zijn dan de kosten van Azure Storage. Omdat de gegevens voor elke generatie van het berekenings cluster niet opnieuw hoeven te worden geladen, bespaart u ook de kosten voor het laden van gegevens.

* **Elastisch uitbreiden:** hoewel HDFS u een uitgebreid bestandssysteem biedt, wordt de schaal bepaald door het aantal knooppunten dat u voor het cluster maakt. Het wijzigen van de schaal kan ingewik kelder zijn dan de mogelijkheden voor elastisch schalen die u automatisch in Azure Storage krijgt.

* **Geo-replicatie:** Uw Azure Storage kan geografisch worden gerepliceerd. Hoewel geo-replicatie een geografisch herstel en gegevens redundantie biedt, is een failover naar de geo-gerepliceerde locatie ernstig van invloed op de prestaties en kunnen er extra kosten in rekening worden gebracht. Kies dus geo-replicatie voorzichtig en alleen als de waarde van de gegevens de extra kosten rechtvaardigt.

Bepaalde MapReduce-taken en-pakketten kunnen tussenliggende resultaten maken die u niet in Azure Storage wilt opslaan. In dat geval kunt u ervoor kiezen om de gegevens op te slaan in de lokale HDFS. HDInsight gebruikt DFS voor verschillende van deze tussenliggende resultaten in Hive-taken en andere processen.

> [!NOTE]  
> De meeste HDFS-opdrachten (bijvoorbeeld `ls` , `copyFromLocal` en `mkdir` ) werken zoals verwacht in azure Storage. Alleen de opdrachten die specifiek zijn voor de systeem eigen HDFS-implementatie (aangeduid als DFS), zoals `fschk` en `dfsadmin` , tonen een ander gedrag in azure Storage.

## <a name="next-steps"></a>Volgende stappen

* [Inleiding tot Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md)
* [Kennismaking met Azure Storage](../storage/common/storage-introduction.md)
* [Azure Data Lake Storage Gen1-overzicht](./overview-data-lake-storage-gen1.md)
