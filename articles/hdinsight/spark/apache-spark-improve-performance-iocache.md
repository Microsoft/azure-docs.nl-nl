---
title: Prestaties van Apache Spark-Azure HDInsight IO-cache (preview-versie)
description: Meer informatie over de Azure HDInsight i/o-cache en hoe u deze kunt gebruiken om de prestaties van Apache Spark te verbeteren.
ms.service: hdinsight
ms.topic: how-to
ms.date: 12/23/2019
ms.openlocfilehash: 9df585c102e2c7307e949e38b6b69147372c38dd
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/23/2021
ms.locfileid: "104866298"
---
# <a name="improve-performance-of-apache-spark-workloads-using-azure-hdinsight-io-cache"></a>Verbeter de prestaties van Apache Spark werk belastingen met behulp van de Azure HDInsight IO-cache

I/o-cache is een service voor gegevens cache voor Azure HDInsight waarmee de prestaties van Apache Spark taken worden verbeterd. I/o-cache werkt ook met [Apache TEZ](https://tez.apache.org/) en [Apache Hive](https://hive.apache.org/) workloads, die op [Apache Spark](https://spark.apache.org/) -clusters kunnen worden uitgevoerd. I/o-cache maakt gebruik van een open-source cache onderdeel met de naam RubiX. RubiX is een lokale schijf cache voor gebruik met big data Analytics-engines die toegang hebben tot gegevens van opslag systemen in de Cloud. RubiX is uniek in cache systemen, omdat deze gebruikmaakt van Solid-State drives (Ssd's) in plaats van dat het besturings geheugen wordt gereserveerd voor cache doeleinden. De i/o-cache service start en beheert RubiX-meta gegevens servers op elk worker-knoop punt van het cluster. Ook worden alle services van het cluster geconfigureerd voor transparant gebruik van RubiX-cache.

De meeste Ssd's bieden meer dan 1 GByte per seconde band breedte. Deze band breedte, aangevuld met de bestands cache van het besturings systeem, biedt voldoende band breedte om te laden big data engines voor reken verwerking, zoals Apache Spark. Het besturings geheugen is beschikbaar voor Apache Spark voor het verwerken van sterk geheugen afhankelijke taken, zoals wille keurige volg orde. Als u exclusief gebruikt voor het gebruik van het geheugen, kunt u met Apache Spark optimaal gebruik van resources krijgen.  

> [!Note]  
> I/o-cache maakt momenteel gebruik van RubiX als een cache onderdeel, maar dit kan worden gewijzigd in toekomstige versies van de service. Gebruik de i/o-cache interfaces en neem geen afhankelijkheden op de RubiX-implementatie.
>I/o-cache wordt op dit moment alleen ondersteund met Azure BLOB Storage.

## <a name="benefits-of-azure-hdinsight-io-cache"></a>Voor delen van de Azure HDInsight IO-cache

Het gebruik van een i/o-cache biedt een prestatie verhoging voor taken die gegevens van Azure Blob Storage lezen.

U hoeft geen wijzigingen aan te brengen in uw Spark-taken om de prestatie verhogingen te bekijken wanneer u een i/o-cache gebruikt. Als i/o-cache is uitgeschakeld, zou deze Spark-code gegevens op afstand van Azure Blob Storage kunnen lezen: `spark.read.load('wasbs:///myfolder/data.parquet').count()` . Als i/o-cache is geactiveerd, veroorzaakt dezelfde regel code een lees-IO-cache door de cache. Bij de volgende Lees bewerkingen worden de gegevens lokaal van SSD gelezen. Worker-knoop punten in HDInsight-cluster zijn uitgerust met lokaal gekoppelde SSD-stations. De HDInsight IO-cache maakt gebruik van deze lokale Ssd's voor caching, wat een laag latentie niveau biedt en de band breedte maximaliseert.

## <a name="getting-started"></a>Aan de slag

De Azure HDInsight IO-cache is standaard uitgeschakeld in de preview-versie. I/o-cache is beschikbaar in azure HDInsight 3.6 + Spark-clusters, die Apache Spark 2,3 worden uitgevoerd.  Voer de volgende stappen uit om de i/o-cache te activeren op HDInsight 4,0:

1. Navigeer in een webbrowser naar `https://CLUSTERNAME.azurehdinsight.net`, waarbij `CLUSTERNAME` de naam van uw cluster is.

1. Selecteer de **i/o-cache** service aan de linkerkant.

1. Selecteer **acties** (**service acties** in HDI 3,6) en **Activeer**.

    :::image type="content" source="./media/apache-spark-improve-performance-iocache/ambariui-enable-iocache.png " alt-text="De i/o-cache service inschakelen in Ambari" border="true":::

1. Opnieuw opstarten bevestigen van alle betrokken services op het cluster.

> [!NOTE]  
> Hoewel de voortgangs balk geactiveerd wordt weer gegeven, wordt de i/o-cache niet daad werkelijk ingeschakeld totdat u de andere betrokken services opnieuw opstart.

## <a name="troubleshooting"></a>Problemen oplossen
  
Er kunnen schijf ruimte fouten optreden bij het uitvoeren van Spark-taken na het inschakelen van i/o-cache. Deze fouten treden op omdat Spark ook gebruikmaakt van lokale schijf opslag voor het opslaan van gegevens tijdens het volg orde van bewerkingen. Spark kan geen SSD-ruimte meer gebruiken nadat de i/o-cache is ingeschakeld en de ruimte voor Spark-opslag is beperkt. De hoeveelheid ruimte die wordt gebruikt door de i/o-cache is standaard de helft van de totale SSD-ruimte. Het schijfruimte gebruik voor de i/o-cache kan worden geconfigureerd in Ambari. Als u schijf ruimte fouten krijgt, vermindert u de hoeveelheid SSD-ruimte die wordt gebruikt voor de i/o-cache en start u de service opnieuw. Voer de volgende stappen uit om de ingestelde ruimte voor de i/o-cache te wijzigen:

1. Selecteer in Apache Ambari de **HDFS** -service aan de linkerkant.

1. Selecteer de tabbladen **configuratie** en **Geavanceerd** .

    :::image type="content" source="./media/apache-spark-improve-performance-iocache/ambariui-hdfs-service-configs-advanced.png " alt-text="Geavanceerde HDFS-configuratie bewerken" border="true":::

1. Schuif omlaag en vouw het gebied **aangepaste kern site** uit.

1. Zoek de eigenschap **Hadoop. cache. data. vol. percentage**.

1. Wijzig de waarde in het vak.

    :::image type="content" source="./media/apache-spark-improve-performance-iocache/ambariui-cache-data-fullness-percentage-property.png " alt-text="Percentage van de volledige i/o-cache bewerken" border="true":::

1. Selecteer **Opslaan** in de rechter bovenhoek.

1. Selecteer **opnieuw opstarten**  >  **alle beïnvloed**.

    :::image type="content" source="./media/apache-spark-improve-performance-iocache/ambariui-restart-all-affected.png " alt-text="Apache Ambari start alle betrokken" border="true":::

1. Selecteer **Bevestig opnieuw opstarten**.

Als dat niet werkt, schakelt u de i/o-cache uit.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over i/o-cache, inclusief benchmarks voor prestaties in dit blog bericht: [Apache Spark-taken kunnen Maxi maal 9x versnellen met HDINSIGHT io-cache](https://azure.microsoft.com/blog/apache-spark-speedup-with-hdinsight-io-cache/)
