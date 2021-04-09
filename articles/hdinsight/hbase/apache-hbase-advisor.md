---
title: Aanbevelingen voor cluster Advisor optimaliseren
titleSuffix: Azure HDInsight
description: Optimaliseer HBase voor cluster Advisor-aanbevelingen in azure HDInsight.
author: ramkrish86
ms.author: ramvasu
ms.service: hdinsight
ms.topic: conceptual
ms.date: 01/03/2021
ms.openlocfilehash: acb7a6aeb4084949be3b0ad40e770a414a13ab6d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98943009"
---
# <a name="apache-hbase-advisories-in-azure-hdinsight"></a>Apache HBase-advies in azure HDInsight

In dit artikel worden verschillende adviezen beschreven om u te helpen bij het optimaliseren van de Apache HBase-prestaties in azure HDInsight. 

## <a name="optimize-hbase-to-read-most-recently-written-data"></a>HBase optimaliseren om meest recent geschreven gegevens te lezen

Als uw usecase de meest recent geschreven gegevens van HBase moet lezen, kan dit advies u helpen. Voor hoge prestaties is het optimaal dat HBase Lees bewerkingen worden uitgevoerd vanaf geheugen opslag, in plaats van de externe opslag.

Het query advies geeft aan dat voor een bepaalde kolom familie in een tabel > 75% Lees bewerkingen wordt uitgevoerd vanuit geheugen opslag. Deze indicator geeft aan dat zelfs als er een leegmaak bewerking plaatsvindt op de geheugen opslag, het recente bestand moet worden geopend en dat in de cache moet worden opgeslagen. De gegevens worden eerst geschreven om te geheugen opslag het systeem toegang krijgt tot de recente gegevens. Er is een kans dat de interne HBase Flusher-threads detecteren dat een bepaalde regio 128M (standaard) grootte heeft bereikt en een flush kan activeren. Dit scenario treedt op als zelfs de meest recente gegevens die zijn geschreven toen de geheugen opslag zich in de grootte bevond. Daarom kan een later gelezen van die recente records een bestand lezen in plaats van geheugen opslag. Daarom is het raadzaam om te optimaliseren dat zelfs recente gegevens die onlangs zijn leeg gemaakt, zich in de cache kunnen bevinden.

Als u de recente gegevens in de cache wilt optimaliseren, kunt u de volgende configuratie-instellingen overwegen:

1. Ingesteld `hbase.rs.cacheblocksonwrite` op `true` . Deze standaard configuratie in HDInsight HBase is `true` , dus controleer of deze niet opnieuw wordt ingesteld op `false` .

2. Verhoog de `hbase.hstore.compactionThreshold` waarde zodat u kunt voor komen dat de compressie wordt gestart. Standaard is deze waarde ingesteld op `3`. U kunt het verhogen naar een hogere waarde, zoals `10` .

3. Als u stap 2 en compactionThreshold hebt ingesteld, gaat u `hbase.hstore.compaction.max` naar een hogere waarde bijvoorbeeld `100` en verhoogt u ook de waarde voor de configuratie `hbase.hstore.blockingStoreFiles` naar een hogere waarde bijvoorbeeld `300` .

4. Als u zeker weet dat u alleen de recente gegevens moet lezen, stelt u de `hbase.rs.cachecompactedblocksonwrite` configuratie in **op aan**. Deze configuratie vertelt het systeem, zelfs als er een compressie plaatsvindt, blijven de gegevens in de cache. De configuraties kunnen ook op familie niveau worden ingesteld. 

   Voer in de HBase-shell de volgende opdracht uit om configuratie in te stellen `hbase.rs.cachecompactedblocksonwrite` :
   
   ```
   alter '<TableName>', {NAME => '<FamilyName>', CONFIGURATION => {'hbase.hstore.blockingStoreFiles' => '300'}}
   ```

5. Blok cache kan worden uitgeschakeld voor een bepaalde familie in een tabel. Zorg ervoor dat deze is **ingeschakeld voor** families met de meest recente gegevens Lees bewerkingen. Blok cache is standaard ingeschakeld voor alle families in een tabel. Als u de blok cache voor een familie hebt uitgeschakeld en deze wilt inschakelen, gebruikt u de opdracht ALTER vanuit de hbase-shell.

   Deze configuraties helpen ervoor te zorgen dat de gegevens in de cache beschikbaar zijn en dat de recente gegevens geen compressie ondergaan. Als er in uw scenario een TTL mogelijk is, kunt u overwegen om een op tijd gelaagde compressie te gebruiken. Zie voor meer informatie [Apache HBase-referentie gids: datum gelaagde compressie](https://hbase.apache.org/book.html#ops.date.tiered)  

## <a name="optimize-the-flush-queue"></a>De flush wachtrij optimaliseren

Dit advies geeft aan dat HBase-leegmaak acties mogelijk moeten worden afgestemd. De huidige configuratie voor flush handlers is mogelijk niet hoog genoeg om te verwerken met schrijf verkeer dat kan leiden tot vertraging van leegmaak acties.

In de gebruikers interface van de regio server ziet u of de wachtrij voor leegmaken groter wordt dan 100. Deze drempel geeft aan dat de leegmaak acties langzaam zijn en dat u de configuratie mogelijk moet afstemmen   `hbase.hstore.flusher.count` . Standaard is de waarde 2. Zorg ervoor dat de maximale Flusher-threads niet groter worden dan 6.

Kijk ook of u een aanbeveling hebt voor het afstemmen van regio's. Als we dit bevestigen, wordt u geadviseerd om de regio te verfijnen om te zien of deze sneller kan worden leeg gemaakt. Anders is het mogelijk om de Flusher-threads te verfijnen.

## <a name="region-count-tuning"></a>Aantal regio's afstemmen

In het advies voor het afstemmen van regio's tellen wordt aangegeven dat HBase updates heeft geblokkeerd en dat het aantal regio's mogelijk groter is dan de optimale ondersteunde Heap-grootte. U kunt de grootte van de heap, de grootte van geheugen opslag en het aantal regio's afstemmen.

Als een voorbeeld scenario:

- Ga ervan uit dat de Heap-grootte voor de regio Server 10 GB is. De is standaard `hbase.hregion.memstore.flush.size` `128M` . De standaard waarde voor `hbase.regionserver.global.memstore.size` is `0.4` . Dit betekent dat de 10 GB 4 GB wordt toegewezen voor geheugen opslag (wereld wijd).

- Stel dat er een gelijkmatige verdeling is van de schrijf belasting voor alle regio's en aangenomen dat elke regio groter is dan 128 MB en dat het maximum aantal regio's in deze installatie `32` regio's is. Als een bepaalde regio server is geconfigureerd met 32 regio's, voor komt het systeem het blok keren van updates te voor komen.

- Als deze instellingen zijn geïmplementeerd, is het aantal regio's 100. De 4-GB Global geheugen opslag is nu gesplitst over 100 regio's. Daarom krijgt elke regio in feite 40 MB voor geheugen opslag. Wanneer de schrijf bewerkingen uniform zijn, wordt het systeem regel matig leeg gemaakt en wordt de kleinste grootte van de volg orde < 40 MB. Het kan zijn dat veel Flusher-threads de flush snelheid kunnen verhogen `hbase.hstore.flusher.count` .

Het advies houdt in dat het een goed idee is om het aantal regio's per server, de Heap-grootte en de algemene configuratie van de geheugen opslag-grootte te controleren, samen met het afstemmen van flush-threads om te voor komen dat updates worden geblokkeerd.

## <a name="compaction-queue-tuning"></a>Afstemming van de verdichtings wachtrij

Als de HBase-compressie wachtrij groter wordt dan 2000 en regel matig wordt uitgevoerd, kunt u de compressie-threads verhogen naar een grotere waarde.

Wanneer er een uitzonderlijk groot aantal bestanden is voor compressie, kan dit leiden tot meer heap-gebruik dat betrekking heeft op de manier waarop de bestanden met het Azure-bestands systeem communiceren. Het is dus beter om de compressie zo snel mogelijk uit te voeren. Sommige keren in oudere clusters kunnen de verdichtings configuraties met betrekking tot het beperken van het compressie proces leiden tot een langzamere verdichtings snelheid.

Controleer de configuraties `hbase.hstore.compaction.throughput.lower.bound` en `hbase.hstore.compaction.throughput.higher.bound` . Als ze al zijn ingesteld op 50 miljoen en 100 miljoen, moet u ze laten staan. Als u deze instellingen echter hebt geconfigureerd op een lagere waarde (wat het geval was met oudere clusters), wijzigt u de limieten in respectievelijk 50 miljoen en 100 miljoen.

De configuraties zijn `hbase.regionserver.thread.compaction.small` en `hbase.regionserver.thread.compaction.large` (de standaard waarden zijn 1 elke).
Cap de maximum waarde voor deze configuratie moet kleiner zijn dan 3.

## <a name="full-table-scan"></a>Volledige tabel scan

De volledige tabel scan advies geeft aan dat meer dan 75% van de scans zijn voltooid. U kunt de scans bekijken om de query prestaties te verbeteren. Houd rekening met de volgende procedures:

* Stel de juiste start-en stop-rij in voor elke scan.

* Gebruik de **MultiRowRangeFilter** -API zodat u verschillende bereiken in één scan aanroep kunt opvragen. Zie de [MULTIROWRANGEFILTER API-documentatie](https://hbase.apache.org/2.1/apidocs/org/apache/hadoop/hbase/filter/MultiRowRangeFilter.html)voor meer informatie.

* Als u een volledige tabel-of regio scan nodig hebt, controleert u of er een mogelijkheid is om cache gebruik voor die query's te vermijden, zodat andere query's die gebruikmaken van de cache de blokken die warm zijn, mogelijk niet worden verwijderd. Om ervoor te zorgen dat de scans geen gebruik maken van cache, gebruikt u de **Scan** -API met de optie **setCaching (false)** in uw code: 

   ```
   scan#setCaching(false)
   ```
   
## <a name="next-steps"></a>Volgende stappen

[Apache HBase optimaliseren met Ambari](../optimize-hbase-ambari.md)
