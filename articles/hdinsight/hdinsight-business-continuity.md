---
title: Azure HDInsight-bedrijfs continuïteit
description: Dit artikel bevat een overzicht van best practices, Beschik baarheid van één regio en optimalisatie opties voor het plannen van Azure HDInsight-bedrijfs continuïteit.
keywords: Hadoop hoge Beschik baarheid
ms.service: hdinsight
ms.topic: conceptual
ms.date: 10/08/2020
ms.openlocfilehash: 74f8bdd26e000b89bfae84102077c241f85abf7e
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/28/2021
ms.locfileid: "98933318"
---
# <a name="azure-hdinsight-business-continuity"></a>Azure HDInsight-bedrijfs continuïteit

Azure HDInsight-clusters zijn afhankelijk van veel Azure-Services, zoals opslag, data bases, Active Directory, Active Directory Domain Services, netwerken en Key Vault. Een goed ontworpen, Maxi maal beschik bare en fout tolerante analytische toepassing moet zijn ontworpen met voldoende redundantie voor een regionale of lokale onderbrekingen in een of meer van deze services. Dit artikel bevat een overzicht van best practices, Beschik baarheid van één regio en optimalisatie opties voor het plannen van bedrijfs continuïteit.

## <a name="general-best-practices"></a>Algemene best practices

In deze sectie worden enkele aanbevolen procedures besproken waarmee u rekening moet houden tijdens de planning van bedrijfs continuïteit.

* Bepaal de minimale bedrijfs functionaliteit die u nodig hebt als er sprake is van een nood geval. U kunt bijvoorbeeld evalueren of u failover-mogelijkheden nodig hebt voor de gegevenstransform-laag (weer gegeven in geel) *en* de laag voor het leveren van gegevens (blauw weer gegeven), of dat u alleen failover nodig hebt voor de gegevenslaag.

   :::image type="content" source="media/hdinsight-business-continuity/data-layers.png" alt-text="gegevens transformatie en gegevens leveren lagen":::

* Segmenteer uw clusters op basis van workload, ontwikkelings levenscyclus en afdelingen. Als u meer clusters hebt, is de kans kleiner dat er één grote fout optreedt in de verschillende bedrijfs processen.

* Maak de secundaire regio's alleen-lezen. Failover-regio's met zowel lees-als schrijf mogelijkheden kunnen leiden tot complexe architecturen.

* Tijdelijke clusters zijn eenvoudiger te beheren wanneer er sprake is van een nood geval. Ontwerp uw workloads zodanig dat clusters kunnen worden gerecycled en dat er geen statussen in clusters worden onderhouden.

* Vaak blijven werk belastingen onvoltooid als er sprake is van een nood geval en moet de computer opnieuw worden opgestart in de nieuwe regio. Ontwerp uw workloads die moeten worden idempotent.

* Gebruik automatisering tijdens cluster implementaties en zorg ervoor dat de cluster configuratie-instellingen zo veel mogelijk worden gescripteerd om snelle en volledig geautomatiseerde implementatie te garanderen als er sprake is van een nood geval.

* Gebruik Azure controle hulpprogramma's op HDInsight om abnormaal gedrag in het cluster te detecteren en bijbehorende waarschuwings meldingen in te stellen. U kunt de vooraf geconfigureerde HDInsight-cluster-specifieke beheer oplossingen implementeren die belang rijke metrische prestatie gegevens van het specifieke cluster type verzamelen. Zie [Azure-bewaking voor HDInsight](./hdinsight-hadoop-oms-log-analytics-tutorial.md)voor meer informatie.  

* Abonneer u op Azure Health Alerts om op de hoogte te worden gesteld van problemen met de service, gepland onderhoud, status-en beveiligings adviezen voor een abonnement, service of regio. Status meldingen die de oorzaak van het probleem en de opgeloste letterlijke ETA bevatten, helpen u bij het uitvoeren van failover en failback. Zie [Azure service Health-documentatie](../service-health/index.yml)voor meer informatie.

## <a name="single-region-availability"></a>Beschik baarheid van één regio

Een standaard HDInsight-systeem heeft de volgende onderdelen. Alle onderdelen hebben hun eigen fout tolerantie mechanismen voor één regio.

* Compute (virtuele machines): Azure HDInsight-cluster
* Meta Store (s): Azure SQL Database
* Opslag: Azure Data Lake Gen2 of Blob Storage
* Verificatie: Azure Active Directory, Azure Active Directory Domain Services Enterprise Security Package
* Domein naam omzetting: Azure DNS

Er zijn andere optionele services die kunnen worden gebruikt, zoals Azure Key Vault en Azure Data Factory.

:::image type="content" source="media/hdinsight-business-continuity/hdinsight-components.png" alt-text="HDInsight-onderdelen":::

### <a name="azure-hdinsight-cluster-compute"></a>Azure HDInsight-cluster (Compute)

HDInsight biedt een SLA voor de beschik baarheid van 99,9%. Om hoge Beschik baarheid in één implementatie te bieden, wordt HDInsight vergezeld van veel services die standaard in de modus voor hoge Beschik baarheid worden uitgevoerd. Mechanismen voor fout tolerantie in HDInsight worden beschikbaar gesteld door de Maxi maal beschik bare services van micro soft en Apache OSS.

De volgende services zijn ontworpen om Maxi maal beschikbaar te zijn:

#### <a name="infrastructure"></a>Infrastructuur

* Actieve en stand-hoofd knooppunten
* Meerdere gateway knooppunten
* Drie Zookeeper-quorum knooppunten
* Worker-knoop punten gedistribueerd door fout-en update domeinen

#### <a name="service"></a>Service

* Apache Ambari-server
* Tijdlijn server van de toepassing voor GARENs
* Taak geschiedenis server voor Hadoop-MapReduce
* Apache Livy
* HDFS
* GARENs van Resource Manager
* HBase Master

Raadpleeg de documentatie over [Services voor hoge Beschik baarheid die worden ondersteund door Azure HDInsight](./hdinsight-high-availability-components.md) voor meer informatie.

Het is niet altijd een onherstelbare gebeurtenis die invloed heeft op de bedrijfs functionaliteit. Service-incidenten in een of meer van de volgende services in één regio kunnen ook leiden tot verlies van de verwachte bedrijfs functionaliteit.

### <a name="hdinsight-metastore"></a>HDInsight-meta Store

HDInsight gebruikt [Azure SQL database](https://azure.microsoft.com/support/legal/sla/sql-database/v1_4/) als meta Store, die een SLA van 99,99% vormt. Drie replica's van gegevens blijven binnen een Data Center met synchrone replicatie. Als er sprake is van een replica verlies, wordt een alternatieve replica probleemloos afgehandeld. [Actieve geo-replicatie](../azure-sql/database/active-geo-replication-overview.md) wordt niet ondersteund in het vak met een maximum van vier data centers. Als er een failover is, hetzij hand matig of Data Center, wordt de eerste replica in de hiërarchie automatisch lees-schrijf mogelijkheden. Zie [Azure SQL database bedrijfs continuïteit](../azure-sql/database/business-continuity-high-availability-disaster-recover-hadr-overview.md)voor meer informatie.

### <a name="hdinsight-storage"></a>HDInsight Storage

HDInsight raadt Azure Data Lake Storage Gen2 aan als de onderliggende opslaglaag. [Azure Storage](https://azure.microsoft.com/support/legal/sla/storage/v1_5/), inclusief Azure data Lake Storage Gen2, bevat een SLA van 99,9%. HDInsight maakt gebruik van de LRS-service waarbij drie replica's van gegevens binnen een Data Center blijven en replicatie synchroon is. Wanneer er sprake is van een replica-verlies, wordt een replica probleemloos afgehandeld.

### <a name="azure-active-directory"></a>Azure Active Directory

[Azure Active Directory](https://azure.microsoft.com/support/legal/sla/active-directory/v1_0/) biedt een SLA van 99,9%. Active Directory is een wereld wijde service met meerdere niveaus van interne redundantie en automatische herstel baarheid. Zie hoe [micro soft de betrouw baarheid van Azure Active Directory voortdurend verbetert](https://azure.microsoft.com/blog/advancing-azure-active-directory-availability/)voor meer informatie.

### <a name="azure-active-directory-domain-services-ad-ds"></a>Azure Active Directory Domain Services (AD DS)

[Azure Active Directory Domain Services](https://azure.microsoft.com/support/legal/sla/active-directory-ds/v1_0/) biedt een SLA van 99,9%. Azure AD DS is een Maxi maal beschik bare service die wordt gehost in wereld wijd gedistribueerde data centers. Replica sets zijn een preview-functie in azure AD DS die geografische nood herstel mogelijk maakt als een Azure-regio offline gaat. Zie voor meer informatie [replica sets-concepten en-functies voor Azure Active Directory Domain Services](../active-directory-domain-services/concepts-replica-sets.md) meer informatie.  

### <a name="azure-dns"></a>Azure DNS

[Azure DNS](https://azure.microsoft.com/support/legal/sla/dns/v1_1/) biedt een SLA van 100%. HDInsight gebruikt Azure DNS op verschillende locaties voor het omzetten van domein namen.

## <a name="multi-region-cost-and-complexity-optimizations"></a>Optimalisaties voor kosten en complexiteit van meerdere regio's

Het verbeteren van de bedrijfs continuïteit met een nood herstel voor hoge Beschik baarheid in meerdere regio's vereist architecturale ontwerpen met een grotere complexiteit en hogere kosten. In de volgende tabellen worden een aantal technische gebieden beschreven die total cost of ownership kunnen verhogen.

### <a name="cost-optimizations"></a>Kosten optimalisaties

|Gebied|Oorzaak van de kosten escalatie|Optimalisatie strategieën|
|----|------------------------|-----------------------|
|Gegevensopslag|Primaire gegevens/tabellen in een secundaire regio dupliceren|Alleen gegevens met curator repliceren|
|Gegevens uitgang|Uitgaande gegevens overdrachten voor meerdere regio's zijn tegen een prijs. Richt lijnen voor bandbreedte prijzen bekijken|Alleen gegevens met een curator repliceren om de regio afzwakken te verminderen|
|Cluster compute|Extra HDInsight-cluster/s in secundaire regio|Gebruik geautomatiseerde scripts voor het implementeren van secundaire Compute na een primaire storing. Gebruik automatisch schalen om de grootte van het secundaire cluster tot een minimum te beperken. Gebruik goedkopere VM-Sku's. Maak secundaire zones in regio's waar VM-Sku's kunnen worden gedisconteerd.|
|Verificatie |Met scenario's voor meerdere gebruikers in de secundaire regio worden extra Azure AD DS-installaties in rekening gebracht|Vermijd het instellen van meerdere gebruikers in een secundaire regio.|

### <a name="complexity-optimizations"></a>Complexiteits optimalisaties

|Gebied|Oorzaak van complexiteits escalatie|Optimalisatie strategieën|
|----|------------------------|-----------------------|
|Schrijf patronen lezen |Vereisen dat zowel primair als secundair Lees-en schrijf bewerkingen zijn ingeschakeld |Het secundaire ontwerp zodanig ontwerpen dat het alleen-lezen is|
|Geen RPO & RTO |Het vereisen van nul gegevens verlies (RPO = 0) en nul downtime (RTO = 0) |Ontwerp RPO en RTO op manieren om het aantal onderdelen te verminderen waarvoor failover moet worden uitgevoerd.|
|Bedrijfs functionaliteit |Volledige bedrijfs functionaliteit van primair in secundair vereisen |Evalueren of u kunt uitvoeren met bare minimale essentiële subset van de bedrijfs functionaliteit in secundair.|
|Connectiviteit |Vereisen dat alle upstream-en downstream-systemen van de primaire verbinding worden gemaakt met de secundaire, ook|Beperk de secundaire connectiviteit met een kritieke minimale subset.|

## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie over de items die in dit artikel worden besproken:

* [Azure HDInsight-architectuur voor bedrijfs continuïteit](./hdinsight-business-continuity-architecture.md)
* [Casestudy Azure HDInsight met hoge Beschik baarheid voor oplossings architectuur](./hdinsight-high-availability-case-study.md)
* [Wat is Apache Hive en HiveQL in azure HDInsight?](./hadoop/hdinsight-use-hive.md)