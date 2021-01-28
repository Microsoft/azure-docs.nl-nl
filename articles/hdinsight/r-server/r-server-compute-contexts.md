---
title: Opties voor Compute-context voor MILLILITERs Services in HDInsight-Azure
description: Meer informatie over de verschillende berekenings context opties die beschikbaar zijn voor gebruikers met ML Services in HDInsight
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 01/02/2020
ms.openlocfilehash: 71ce0d87faa33bd7d533242edfcf3b131c8f7e47
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/28/2021
ms.locfileid: "98943950"
---
# <a name="compute-context-options-for-ml-services-on-hdinsight"></a>Opties voor compute-context voor ML Services in HDInsight

De services van ML van Azure HDInsight bepalen hoe aanroepen worden uitgevoerd door de compute-context in te stellen. In dit artikel vindt u een overzicht van de opties die beschikbaar zijn om aan te geven of en hoe uitvoering wordt geevenwijdigd verdeeld over kernen van het Edge-knoop punt of HDInsight-cluster.

Het edge-knooppunt van een cluster biedt een handige plaats om verbinding te maken met het cluster en om uw R-scripts uit te voeren. Met een edge-knooppunt hebt u de mogelijkheid om de geparallelliseerde gedistribueerde functies van RevoScaleR uit te voeren op de kernen van de server van het edge-knooppunt. U kunt ze ook uitvoeren op de knooppunten van het cluster door compute-contexten van Hadoop Map Reduce of Apache Spark van RevoScaleR te gebruiken.

## <a name="ml-services-on-azure-hdinsight"></a>ML Services in azure HDInsight

[Ml Services in azure HDInsight](r-server-overview.md) biedt de nieuwste mogelijkheden voor R-gebaseerde analyses. Het kan gebruikmaken van gegevens die zijn opgeslagen in een Apache Hadoop HDFS-container in uw [Azure Blob](../../storage/common/storage-introduction.md "Azure Blob Storage") -opslag account, een Data Lake Store of het lokale Linux-bestands systeem. Omdat de services van ML zijn gebouwd op open source R, kunnen de op R gebaseerde toepassingen die u bouwt, een van de 8000 + open source R-pakketten Toep assen. Ze kunnen ook gebruikmaken van de routines in [RevoScaleR](/machine-learning-server/r-reference/revoscaler/revoscaler), het Big Data Analytics-pakket van micro soft dat deel uitmaakt van ml Services.  

## <a name="compute-contexts-for-an-edge-node"></a>Contexten berekenen voor een Edge-knoop punt

In het algemeen wordt een R-script dat wordt uitgevoerd in het cluster met MILLILITER Services op het Edge-knoop punt uitgevoerd in de R-interpreter op dat knoop punt. De uitzonde ringen zijn de stappen die de functie RevoScaleR aanroepen. De RevoScaleR-aanroepen worden uitgevoerd in een compute-omgeving die wordt bepaald door de manier waarop u de RevoScaleR-Compute-context instelt.  Wanneer u uw R-script uitvoert vanuit een Edge-knoop punt, zijn de mogelijke waarden van de compute-context:

- lokaal sequentieel (*lokaal*)
- lokaal parallel (*localpar*)
- Toewijzing verminderen
- Spark

De opties *lokaal* en *localpar* zijn alleen van invloed op de manier waarop **rxExec** -aanroepen worden uitgevoerd. Ze voeren beide andere RX-functie aanroepen op parallelle wijze uit voor alle beschik bare kernen, tenzij anders is aangegeven met behulp van de RevoScaleR **numCoresToUse** optie `rxOptions(numCoresToUse=6)` . Opties voor parallelle uitvoering bieden optimale prestaties.

De volgende tabel bevat een overzicht van de verschillende opties voor de compute-context om in te stellen hoe aanroepen worden uitgevoerd:

| Compute-context  | Hoe instellen                      | Context voor uitvoering                        |
| ---------------- | ------------------------------- | ---------------------------------------- |
| Lokaal sequentieel | rxSetComputeContext (' lokaal ')    | Parallelle uitvoering in de kernen van de Edge-knooppunt server, met uitzonde ring van rxExec-aanroepen, die serieel worden uitgevoerd |
| Lokaal parallel   | rxSetComputeContext('localpar') | Parallelle uitvoering in de kernen van de Edge-knooppunt server |
| Spark            | RxSpark ()                       | Gesplitste gedistribueerde uitvoering via Spark over de knoop punten van het HDI-cluster |
| Toewijzing verminderen       | RxHadoopMR()                    | Gesplitste gedistribueerde uitvoering via kaart reductie over de knoop punten van het HDI-cluster |

## <a name="guidelines-for-deciding-on-a-compute-context"></a>Richt lijnen voor het kiezen van een compute-context

Welke van de drie opties die u kiest voor parallelle uitvoering, is afhankelijk van de aard van uw analyse werkzaamheden, de grootte en de locatie van uw gegevens. Er is geen eenvoudige formule die aangeeft welke reken context u moet gebruiken. Er zijn echter enkele leidende principes die u kunnen helpen bij het kiezen van de juiste keuze, of om uw keuzes te beperken voordat u een bench Mark uitvoert. Deze richt lijnen zijn onder andere:

- Het lokale Linux-bestands systeem is sneller dan HDFS.
- Herhaalde analyses zijn sneller als de gegevens lokaal zijn en als deze zich in XDF bevinden.
- Het is raadzaam om kleine hoeveel heden gegevens uit een tekst gegevens bron te streamen. Als de hoeveelheid gegevens groter is, converteert u deze naar XDF vóór analyse.
- De overhead voor het kopiëren of streamen van gegevens naar het Edge-knoop punt voor analyse wordt onbeheerbaar voor zeer grote hoeveel heden gegevens.
- ApacheSpark is sneller dan toewijzings reductie voor analyse in Hadoop.

Op basis van deze principes bieden de volgende secties enkele algemene vuist regels voor het selecteren van een compute-context.

### <a name="local"></a>Lokaal

- Als de hoeveelheid gegevens die moet worden geanalyseerd klein is en er geen herhaalde analyse nodig is, kunt u deze rechtstreeks naar de analyse routine streamen met behulp van *lokale* of *localpar*.
- Als de hoeveelheid gegevens die moet worden geanalyseerd, klein of middel groot is en herhaalde analyse vereist is, kopieert u deze naar het lokale bestands systeem, importeert u het naar XDF en analyseert u deze via *lokale* of *localpar*.

### <a name="apache-spark"></a>Apache Spark

- Als de hoeveelheid gegevens die moet worden geanalyseerd groot is, kunt u deze in een Spark-data frame importeren met **RxHiveData** of **RxParquetData**, of naar XDF in HDFS (tenzij opslag een probleem is) en deze analyseren met behulp van de Spark-Compute-context.

### <a name="apache-hadoop-map-reduce"></a>Apache Hadoop toewijzing verminderen

- Gebruik de alleen Compute-context van de kaart als u een insurmountable-probleem ondervindt met de Spark Compute-context, omdat deze in het algemeen langzamer is.  

## <a name="inline-help-on-rxsetcomputecontext"></a>Inline-Help op rxSetComputeContext
Voor meer informatie en voor beelden van RevoScaleR Compute-contexten, zie de inline-Help in R in de rxSetComputeContext-methode, bijvoorbeeld:

```console
> ?rxSetComputeContext
```

U kunt ook het overzicht van [gedistribueerde computers](/machine-learning-server/r/how-to-revoscaler-distributed-computing) in [machine learning Server documentatie](/machine-learning-server/)raadplegen.

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u geleerd over de beschik bare opties om op te geven of en hoe uitvoering wordt geevenwijdigd verdeeld over kernen van het Edge-knoop punt of HDInsight-cluster. Zie de volgende onderwerpen voor meer informatie over het gebruik van ML-Services met HDInsight-clusters:

- [Overzicht van ML Services voor Apache Hadoop](r-server-overview.md)
- [Azure Storage opties voor de services van MILLILITERs in HDInsight](r-server-storage.md)