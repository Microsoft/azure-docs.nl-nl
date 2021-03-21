---
title: Prestatieproblemen van Apache Hbase in Azure HDInsight oplossen
description: Diverse richt lijnen en tips voor het afstemmen van Apache HBase-prestaties voor optimale prestaties in azure HDInsight.
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 09/24/2019
ms.openlocfilehash: 466fac524601e2d569bfa0ccf90179fe9419210d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98942893"
---
# <a name="troubleshoot-apache-hbase-performance-issues-on-azure-hdinsight"></a>Prestatieproblemen van Apache Hbase in Azure HDInsight oplossen

In dit artikel worden verschillende richt lijnen en tips voor het afstemmen van Apache HBase-prestaties beschreven voor optimale prestaties van Azure HDInsight. Veel van deze tips zijn afhankelijk van de specifieke werk belasting en het Lees-en schrijf-en scan patroon. Voordat u configuratie wijzigingen toepast in een productie omgeving, moet u ze zorgvuldig testen.

## <a name="hbase-performance-insights"></a>HBase-prestatie inzichten

De meest voorkomende bottleneck in de meeste HBase-werk belastingen is het Write-Ahead logboek (WAL). Het heeft een grote invloed op de schrijf prestaties. HDInsight HBase heeft een gescheiden model voor opslag berekening. Gegevens worden extern opgeslagen op Azure Storage, zelfs als virtuele machines de regio servers hosten. Tot die tijd werd de WAL ook geschreven naar Azure Storage. In HDInsight versterkt dit gedrag van deze bottleneck. De functie voor [versnelde schrijf bewerkingen](./apache-hbase-accelerated-writes.md) is ontworpen om dit probleem op te lossen. Het schrijft de WAL naar Azure Premium-SSD-Managed disks. Dit is een enorme voor deel van het schrijven van prestaties en het helpt veel problemen te voor komen door enkele van de Write-intensieve workloads.

Gebruik [Premium Block Blob Storage account](https://azure.microsoft.com/blog/azure-premium-block-blob-storage-is-now-generally-available/) als uw externe opslag om aanzienlijke verbeteringen in Lees bewerkingen te verkrijgen. Deze optie is alleen mogelijk als de functie WAL is ingeschakeld.

## <a name="compaction"></a>Compressie

Compressie is een andere mogelijke bottleneck die fundamenteel is overeengekomen in de community. Primaire compressie is standaard uitgeschakeld in HDInsight HBase-clusters. De compressie is uitgeschakeld, omdat het een resource-intensieve proces is, kunnen klanten de volledige flexibiliteit plannen volgens hun werk belastingen. Ze kunnen bijvoorbeeld deze plannen tijdens daluren. Data Locality is ook niet relevant omdat onze opslag extern (ondersteund door Azure Storage) in plaats van naar een lokale Hadoop Distributed File System (HDFS).

Klanten moeten op hun gemak grote compressie plannen. Als ze deze onderhouds werkzaamheden niet uitvoeren, heeft de compressie nadelige invloed op de Lees prestaties van de lange uitvoering.

Voor scan bewerkingen duiden de latentie die veel hoger zijn dan 100 milliseconden een oorzaak van bezorgdheid. Controleer of de grote compressie nauw keurig is gepland.

## <a name="apache-phoenix-workload"></a>Apache Phoenix workload

Als u de volgende vragen beantwoordt, hebt u meer inzicht in uw Apache Phoenix-werk belasting:

* Zijn al uw ' Lees bewerkingen ' vertaald naar scans?
    * Als dat het geval is, wat zijn dan de kenmerken van deze scans?
    * Hebt u uw BREDAS tabel schema geoptimaliseerd voor deze scans, inclusief de juiste indexeringen?
* Hebt u de `EXPLAIN` instructie gebruikt om inzicht te krijgen in de resultaten van het genereren van ' Lees bewerkingen '?
* Zijn uw schrijf opdrachten "upsert-selecteren"?
    * Als dat het geval is, worden er ook scans uitgevoerd. De verwachte latentie voor scans is ongeveer 100 milliseconden, vergeleken met 10 milliseconden voor punt wordt opgehaald in HBase.  

## <a name="test-methodology-and-metrics-monitoring"></a>Bewakings methodiek en metrische gegevens controleren

Als u benchmarks gebruikt zoals Yahoo! Cloud met Bench Mark, JMeter of Pherf om prestaties te testen en af te stemmen, moet u het volgende doen:

- De client computers worden geen knel punt. U doet dit door het CPU-gebruik op client computers te controleren.

- Configuraties aan client zijde, zoals het aantal threads, worden op de juiste wijze afgestemd op de verzadiging van client bandbreedte.

- Test resultaten worden nauw keurig en systematisch vastgelegd.

Als uw query's plotseling worden gestart, kunt u controleren op mogelijke fouten in uw toepassings code. Worden er plotseling grote hoeveel heden ongeldige gegevens gegenereerd? Als dat het geval is, kan dit de lees latentie verhogen.

## <a name="migration-issues"></a>Migratieproblemen

Als u migreert naar Azure HDInsight, moet u ervoor zorgen dat uw migratie systematisch en nauw keurig wordt uitgevoerd, bij voor keur via Automation. Vermijd hand matige migratie. Zorg voor het volgende:

- Tabel kenmerken worden nauw keurig gemigreerd. Kenmerken kunnen bestaan uit als compressie, bloei filters, enzovoort.

- De instellingen voor het zouten in BREDAS tabellen worden op de juiste wijze toegewezen aan de nieuwe cluster grootte. Het aantal zout buckets moet bijvoorbeeld een veelvoud zijn van het aantal worker-knoop punten in het cluster. En u moet een veelvoud gebruiken dat een factor is van de hoeveelheid Hot herkennen.

## <a name="server-side-configuration-tunings"></a>Aanpassing van de configuratie aan server zijde

In HDInsight HBase worden HFiles opgeslagen op externe opslag. Wanneer er een cache ontbreekt, zijn de kosten voor lees bewerkingen hoger dan on-premises systemen, omdat gegevens op on-premises systemen worden ondersteund door Local HDFS. Voor de meeste scenario's is intelligent gebruik van HBase-caches (Block cache en Bucket cache) ontworpen om dit probleem te omzeilen. In gevallen waarin het probleem niet wordt omzeild, kunt u dit probleem mogelijk oplossen met behulp van een account voor een Premium-blok-blob. Het Windows Azure Storage Blob-stuur programma is afhankelijk van bepaalde eigenschappen `fs.azure.read.request.size` , zoals het ophalen van gegevens in blokken op basis van de Lees modus (opeenvolgend versus wille keurig), zodat er mogelijk nog steeds exemplaren van hogere latenties met lees bewerkingen zijn. Door middel van empirische experimenten hebben we vastgesteld dat het instellen van de blokkerings grootte van de Lees aanvraag ( `fs.azure.read.request.size` ) op 512 KB en het afstemmen van de blok grootte van de HBase-tabellen dezelfde grootte hebben als het beste resultaat in de praktijk.

Voor de meeste clusters met een groot aantal knoop punten biedt HDInsight HBase `bucketcache` als een bestand op een lokale Premium-SSD dat is gekoppeld aan de virtuele machine, die wordt uitgevoerd `regionservers` . Het gebruik van een off-heap-cache kan in plaats daarvan enige verbeteringen opleveren. Deze tijdelijke oplossing heeft de beperking van het gebruik van het beschik bare geheugen en mogelijk kleiner dan een op bestanden gebaseerde cache, zodat het niet altijd de beste keuze is.

Hieronder vindt u enkele van de andere specifieke para meters die we hebben afgestemd en die u helpen bij het variëren van graden:

- Verhoog de `memstore` grootte van de standaard waarde van 128 MB tot 256 MB. Normaal gesp roken wordt deze instelling aanbevolen voor zware schrijf scenario's.

- Verhoog het aantal threads dat is gereserveerd voor compressie, van de standaard instelling van **1** tot **4**. Deze instelling is relevant als we veelvuldige kleine compressies bekijken.

- Voorkom dat `memstore` Flush wordt geblokkeerd vanwege een opslag limiet. Als u deze buffer wilt opgeven, verhoogt `Hbase.hstore.blockingStoreFiles` u de instelling in **100**.

- Gebruik de volgende instellingen voor het beheren van leegmaak acties:

    - `Hbase.regionserver.maxlogs`: **140** (leegmaak acties worden voor komen vanwege wal limieten)

    - `Hbase.regionserver.global.memstore.lowerLimit`: **0,55**

    - `Hbase.regionserver.global.memstore.upperLimit`: **0,60**

- Phoenix-specifieke configuraties voor het afstemmen van thread groepen:

    - `Phoenix.query.queuesize`: **10000**

    - `Phoenix.query.threadpoolsize`: **512**

- Andere Phoenix-specifieke configuraties:

    - `Phoenix.rpc.index.handler.count`: **50** (als er grote of veel index lookups zijn)

    - `Phoenix.stats.updateFrequency`: **1 uur**

    - `Phoenix.coprocessor.maxmetadatacachetimetolivems`: **1 uur**

    - `Phoenix.coprocessor.maxmetadatacachesize`: **50 MB**

- RPC-time-outs: **drie minuten**

   - RPC-time-outs zijn onder andere HBase RPC time-out, time-out voor HBase-client scanner en Bredae querytime-out. 
   - Zorg ervoor dat de `hbase.client.scanner.caching` para meter is ingesteld op dezelfde waarde op zowel het server eindpunt als het einde van de client. Als ze niet hetzelfde zijn, leidt deze instelling tot client-end fouten die betrekking hebben op `OutOfOrderScannerException` . Deze instelling moet worden ingesteld op een lage waarde voor grote scans. We stellen deze waarde in op **100**.

## <a name="other-considerations"></a>Andere overwegingen

Hieronder vindt u aanvullende para meters voor het afstemmen van het volgende:

- `Hbase.rs.cacheblocksonwrite` -HDI is deze instelling standaard ingesteld op **True**.

- Instellingen waarmee u de kleine compressie voor later kunt uitstellen.

- Experimentele instellingen, zoals het aanpassen van percentages van wacht rijen die zijn gereserveerd voor lees-en schrijf aanvragen.

## <a name="next-steps"></a>Volgende stappen

Als uw probleem niet is opgelost, gaat u naar een van de volgende kanalen voor meer ondersteuning:

- Krijg antwoorden van Azure-experts via de [ondersteuning van Azure Community](https://azure.microsoft.com/support/community/).

- Verbinding maken met [@AzureSupport](https://twitter.com/azuresupport) . Dit is het officiële Microsoft Azure account voor het verbeteren van de gebruikers ervaring. De Azure-community wordt verbonden met de juiste resources: antwoorden, ondersteuning en experts.

- Als u meer hulp nodig hebt, kunt u een ondersteunings aanvraag indienen via de [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selecteer **ondersteuning** in de menu balk of open de hub **Help en ondersteuning** . Lees [hoe u een ondersteunings aanvraag voor Azure kunt maken](../../azure-portal/supportability/how-to-create-azure-support-request.md)voor meer informatie. Uw Microsoft Azure-abonnement bevat toegang tot abonnements beheer en ondersteuning voor facturering, en technische ondersteuning wordt geleverd via een van de [ondersteunings abonnementen voor Azure](https://azure.microsoft.com/support/plans/).