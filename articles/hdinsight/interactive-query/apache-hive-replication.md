---
title: Apache Hive replicatie gebruiken in azure HDInsight-clusters
description: Meer informatie over het gebruik van Hive-replicatie in HDInsight-clusters voor het repliceren van de Hive-metastore en de Azure Data Lake Storage gen 2 data Lake.
ms.service: hdinsight
ms.topic: conceptual
ms.date: 10/08/2020
ms.openlocfilehash: 2e57b7d8121ac0bd6fc9cb693750c41a0ef6a5bc
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101733054"
---
# <a name="how-to-use-apache-hive-replication-in-azure-hdinsight-clusters"></a>Apache Hive replicatie gebruiken in azure HDInsight-clusters

In de context van data bases en magazijnen, is replicatie het proces van het dupliceren van entiteiten van het ene naar het andere magazijn. Duplicatie kan worden toegepast op een gehele data base of op een kleiner niveau, zoals een tabel of partitie. Het doel is om een replica te hebben die verandert wanneer de basis entiteit wordt gewijzigd. Replicatie op Apache Hive richt zich op herstel na nood geval en biedt replicatie op basis van één of meer primaire kopieën. In HDInsight-clusters kan Hive-replicatie worden gebruikt om de Hive-metastore en de bijbehorende onderliggende data Lake op Azure Data Lake Storage Gen2 ontoegankelijk te repliceren.  

Hive-replicatie is in de loop van de jaren ontwikkeld met nieuwere versies, waardoor er betere functionaliteit is en sneller en minder bronnen worden intensief. In dit artikel bespreken we Hive Replication (Replv2), die wordt ondersteund in cluster typen van zowel HDInsight 3,6 als HDInsight 4,0.

## <a name="advantages-of-replv2"></a>Voor delen van replv2

[Hive ReplicationV2](https://cwiki.apache.org/confluence/display/Hive/HiveReplicationv2Development) (ook wel Replv2 genoemd) heeft de volgende voor delen ten opzichte van de eerste versie van Hive-replicatie waarvoor Hive [import-export](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+ImportExport)is gebruikt:

- Incrementele replicatie op basis van gebeurtenissen
- Punt-in-time-replicatie  
- Beperkte bandbreedte vereisten  
- Vermindering van het aantal tussenliggende kopieën  
- De replicatie status wordt behouden
- Beperkte replicatie  
- Ondersteuning voor een hub-en spoke-model  
- Ondersteuning voor ACID-tabellen (in HDInsight 4,0)

## <a name="replication-phases"></a>Replicatie fasen

Replicatie op basis van Hive-gebeurtenissen is geconfigureerd tussen de primaire en secundaire clusters. Deze replicatie bestaat uit twee afzonderlijke fasen: Boots trap ping en incrementele uitvoeringen.

### <a name="bootstrapping"></a>Oftewel opnieuw opstarten

Boots Trapping is bedoeld om één keer te worden uitgevoerd om de basis status van de data bases van primair naar secundair te repliceren. U kunt indien nodig Boots trap ping configureren om een subset van de tabellen in de doel database op te maken waarvoor replicatie moet worden ingeschakeld.

### <a name="incremental-runs"></a>Incrementele uitvoeringen

Na het Boots trappen worden incrementele uitvoeringen geautomatiseerd op het primaire cluster en worden de gebeurtenissen die tijdens deze incrementele uitvoeringen worden gegenereerd, afgespeeld op het secundaire cluster. Wanneer het secundaire cluster met het primaire cluster ingaat, wordt de secundaire in overeenstemming met de gebeurtenissen van de primaire.

## <a name="replication-commands"></a>Replicatie opdrachten

Hive biedt een reeks REPL-opdrachten: `DUMP` , `LOAD` en `STATUS` -om de stroom van gebeurtenissen te organiseren. `DUMP`Met de opdracht wordt een lokaal logboek van alle DDL/DML-gebeurtenissen op het primaire cluster gegenereerd. De `LOAD` opdracht is een methode voor het kopiëren van meta gegevens en gegevens die zijn vastgelegd in de uitgepakte replicatie dump uitvoer en wordt uitgevoerd op het doel cluster. De `STATUS` opdracht wordt uitgevoerd vanaf het doel cluster om de nieuwste gebeurtenis-id te verstrekken die de meest recente replicatie belasting heeft gerepliceerd.

### <a name="set-replication-source"></a>Replicatie bron instellen

Voordat u met replicatie begint, moet u ervoor zorgen dat de data base die moet worden gerepliceerd, is ingesteld als de replicatie bron. U kunt de `DESC DATABASE EXTENDED <db_name>` opdracht gebruiken om te bepalen of de para meter `repl.source.for` is ingesteld met de naam van het beleid.

Als het beleid is gepland en de `repl.source.for` para meter niet is ingesteld, moet u deze para meter eerst instellen met behulp van `ALTER DATABASE <db_name> SET DBPROPERTIES ('repl.source.for'='<policy_name>')` .

```sql
ALTER DATABASE tpcds_orc SET DBPROPERTIES ('repl.source.for'='replpolicy1') 
```

### <a name="dump-metadata-to-the-data-lake"></a>Meta gegevens aan data Lake dumpen

De `REPL DUMP [database name]. => location / event_id` opdracht wordt gebruikt in de Boots trap-fase om relevante meta gegevens te dumpen voor Azure data Lake Storage Gen2. `event_id`Hiermee geeft u de minimale gebeurtenis op waarnaar de relevante meta gegevens in azure data Lake Storage Gen2 zijn geplaatst. 
 
```sql
repl dump tpcds_orc; 
```
Voorbeelduitvoer:

| dump_dir|last_repl_id
|-|-|
|/tmp/hive/repl/38896729-67d5-41b2-90dc-46eeed4c5dd0|2925|

### <a name="load-data-to-the-target-cluster"></a>Gegevens laden naar het doel cluster

De `REPL LOAD [database name] FROM [ location ] { WITH ( ‘key1’=‘value1’{, ‘key2’=‘value2’} ) }` opdracht wordt gebruikt om gegevens in het doel cluster te laden voor zowel de Boots trap als de incrementele fase van de replicatie. De `[database name]` kan hetzelfde zijn als de bron of een andere naam op het doel cluster. De `[location]` vertegenwoordigt de locatie uit de uitvoer van een eerdere `REPL DUMP` opdracht. Dit betekent dat het doel cluster moet kunnen communiceren met het bron cluster. De `WITH` component is primair toegevoegd om te voor komen dat het doel cluster opnieuw wordt opgestart, waardoor replicatie mogelijk is.

```sql
repl load tpcds_orc from '/tmp/hive/repl/38896729-67d5-41b2-90dc-46eeed4c5dd0'; 
```

### <a name="output-the-last-replicated-event-id"></a>Uitvoer van de laatste gerepliceerde gebeurtenis-ID

De `REPL STATUS [database name]` opdracht wordt uitgevoerd op doel clusters en voert de laatste replicatie uit `event_id` . Met deze opdracht kunnen gebruikers ook weten in welke staat hun doel cluster is gerepliceerd. U kunt de uitvoer van deze opdracht gebruiken om de volgende `REPL DUMP` opdracht voor incrementele replicatie te maken.

```sql
repl status tpcds_orc;
```

Voorbeelduitvoer:

|last_repl_id|
|-|
|2925|

### <a name="dump-relevant-data-and-metadata-to-the-data-lake"></a>Relevante gegevens en meta gegevens naar de data Lake dumpen

De `REPL DUMP [database name] FROM [event-id] { TO [event-id] } { LIMIT [number of events] }` opdracht wordt gebruikt om relevante meta gegevens en gegevens te dumpen voor Azure data Lake Storage. Deze opdracht wordt gebruikt in de incrementele fase en wordt uitgevoerd op het bron magazijn. De `FROM [event-id]` is vereist voor de incrementele fase en de waarde van `event-id` kan worden afgeleid door de `REPL STATUS [database name]` opdracht uit te voeren in het doel magazijn.

```sql
repl dump tpcds_orc from 2925;
```

Voorbeelduitvoer:

|dump_dir|last_repl_id|
|-|-|
| /tmp/hive/repl/38896729-67d5-41b2-90dc-466466agadd0 | 2960|

## <a name="hive-replication-process"></a>Hive-replicatie proces

De volgende stappen zijn de sequentiële gebeurtenissen die plaatsvinden tijdens het Hive-replicatie proces.

1. Zorg ervoor dat de gerepliceerde tabellen zijn ingesteld als de replicatie bron voor een bepaald beleid.

1. De `REPL_DUMP` opdracht wordt verleend aan het primaire cluster met de bijbehorende beperkingen, zoals de database naam, het gebeurtenis-ID-bereik en de opslag-URL van Azure data Lake Storage Gen2.

1. Het systeem serialeert een dump van alle bijgehouden gebeurtenissen van de meta Store naar de meest recente. Deze dump wordt opgeslagen in het opslag account van Azure Data Lake Storage Gen2 op het primaire cluster op de URL die is opgegeven door de `REPL_DUMP` .  

1. De primaire cluster blijft de meta gegevens van de replicatie naar de Azure Data Lake Storage Gen2 opslag van het primaire cluster. Het pad kan worden geconfigureerd in de UI-gebruikers interface van Hive in Ambari. Het proces biedt het pad waar de meta gegevens worden opgeslagen en de ID van de laatste getraceerde DML/DDL-gebeurtenis.

1. De `REPL_LOAD` opdracht wordt verleend vanuit het secundaire cluster. De opdracht verwijst naar het pad dat is geconfigureerd in stap 3.

1. Het secundaire cluster leest het meta gegevensbestand met bijgehouden gebeurtenissen dat is gemaakt in stap 3. Zorg ervoor dat het secundaire cluster netwerk verbinding heeft met de Azure Data Lake Storage Gen2 opslag van het primaire cluster waar de bijgehouden gebeurtenissen van `REPL_DUMP` worden opgeslagen.  

1. Het secundaire cluster berekent gedistribueerde kopie ( `DistCP` ) compute.

1. Het secundaire cluster kopieert gegevens uit de opslag van het primaire cluster.  

1. De meta Store op het secundaire cluster wordt bijgewerkt.  

1. De laatste getraceerde gebeurtenis-ID wordt opgeslagen in de primaire meta Store.

Incrementele replicatie volgt hetzelfde proces en vereist de laatste gerepliceerde gebeurtenis-ID als invoer. Dit leidt tot een incrementele kopie sinds de laatste replicatie gebeurtenis. Incrementele replicaties worden normaal gesp roken geautomatiseerd met een vooraf bepaalde frequentie om vereiste herstel punt doelstellingen (RPO) te bereiken.

:::image type="content" source="media/apache-hive-replication/hive-replication-diagram.png" alt-text="Hive-replicatie diagram":::

## <a name="replication-patterns"></a>Replicatie patronen  

Replicatie wordt normaal gesp roken geconfigureerd op een manier tussen de primaire en secundaire locatie, waarbij de primaire keuken aanvragen voor lezen en schrijven. De secundaire cluster-keukens om alleen aanvragen te lezen. Schrijf bewerkingen zijn toegestaan op het secundaire als er sprake is van een nood geval, maar omgekeerde replicatie moet worden geconfigureerd op de primaire.

:::image type="content" source="media/apache-hive-replication/replication-pattern.png" alt-text="Replicatie patroon Hive":::

Er zijn veel patronen die geschikt zijn voor Hive-replicatie, waaronder Primary: Secondary, hub en spoke en relay.

In HDInsight actief primair – stand-by secundair is een gemeen schappelijk BCDR-patroon (bedrijfs continuïteit en herstel na nood gevallen) en HiveReplicationV2 kan dit patroon gebruiken met door regio's gescheiden HDInsight Hadoop-clusters met VNet-peering. Een algemene virtuele machine die is gekoppeld aan beide clusters kan worden gebruikt voor het hosten van de replicatie automatiserings scripts. Raadpleeg de [documentatie voor hdinsight-bedrijfs continuïteit](../hdinsight-business-continuity.md)voor meer informatie over mogelijke hdinsight BCDR-patronen.  

### <a name="hive-replication-with-enterprise-security-package"></a>Hive-replicatie met Enterprise Security Package  

In gevallen waarin Hive-replicatie wordt gepland op HDInsight Hadoop-clusters met Enterprise Security Package, moet u rekening houden met de replicatie mechanismen voor Zwerver meta Store en Azure Active Directory Domain Services (AD DS).  

Gebruik de functie replica sets van AD DS Azure om meer dan één Azure AD-AD DS replicaset te maken op basis van meerdere regio's. Elke afzonderlijke replicaset moet worden gekoppeld aan HDInsight-VNets in hun respectieve regio's. In deze configuratie worden wijzigingen in azure AD DS, waaronder configuratie, gebruikers-id en referenties, groepen, groeps beleidsobjecten, computer objecten en andere wijzigingen, toegepast op alle replica sets in het beheerde domein met behulp van Azure AD DS-replicatie.
  
In het zwerver-beleid kunt u regel matig een back-up maken van de primaire naar de secundaire en deze repliceren met behulp van Zwerver Import-Export functionaliteit. U kunt ervoor kiezen om alle of een subset van Zwerver-beleids regels te repliceren, afhankelijk van het niveau van autorisaties dat u op het secundaire cluster wilt implementeren.  

## <a name="sample-code"></a>Voorbeeldcode  

De volgende code reeks biedt een voor beeld van hoe Boots trap en incrementele replicatie kunnen worden geïmplementeerd in een voorbeeld tabel met de naam `tpcds_orc` .  

1. Stel de tabel in als de bron voor een replicatie beleid.

   ```sql
   ALTER DATABASE tpcds_orc SET DBPROPERTIES ('repl.source.   for'='replpolicy1');
   ```

1. Boots trap dump op het primaire cluster.

   ```sql
   repl dump tpcds_orc with ('hive.repl.rootdir'='/tmpag/hiveag/replag'); 
   ```
   
   Voorbeelduitvoer:
   
   |dump_dir|last_repl_id|
   |-|-|
   |/tmpag/hiveag/replag/675d1bea-2361-4cad-bcbf-8680d305a27a|2925|
 
1. Boots trap laden op het secundaire cluster. 

   ```sql
   repl load tpcds_orc from '/tmpag/hiveag/replag 675d1bea-2361-4cad-bcbf-8680d305a27a'; 
   ```

1. Controleer de `REPL` status op het secundaire cluster.

   ```sql
   repl status tpcds_orc; 
   ```
 
   |last_repl_id|
   |-|
   |2925|

1. Incrementele dump op het primaire cluster.

   ```sql
   repl dump tpcds_orc from 2925 with ('hive.repl.rootdir'='/tmpag/hiveag/ replag');
   ```

   Voorbeelduitvoer:
   
   |dump_dir | last_repl_id|
   |-|-|
   |/tmpag/hiveag/replag/31177ff7-a40f-4f67-a613-3b64ebe3bb31|2960|

1. Incrementele belasting op het secundaire cluster.  

   ```sql
   repl load tpcds_orc from '/tmpag/hiveag/replag/31177ff7-a40f-4f67-a613-3b64ebe3bb31';
   ```

1. Controleer de `REPL` status op het secundaire cluster.

   ```sql
   repl status tpcds_orc;
   ```

   |last_repl_id|
   |-|
   |2960|

## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie over de items die in dit artikel worden besproken:

- [Azure HDInsight-bedrijfs continuïteit](../hdinsight-business-continuity.md)
- [Azure HDInsight-architectuur voor bedrijfs continuïteit](../hdinsight-business-continuity-architecture.md)
- [Casestudy Azure HDInsight met hoge Beschik baarheid voor oplossings architectuur](../hdinsight-high-availability-case-study.md)
- [Wat is Apache Hive en HiveQL in azure HDInsight?](../hadoop/hdinsight-use-hive.md)