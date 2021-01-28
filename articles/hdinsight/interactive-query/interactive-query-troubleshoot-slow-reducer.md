---
title: Reductie van Azure HDInsight is langzaam
description: Reductie van Azure HDInsight verloopt traag vanuit mogelijke gegevens scheef trekken
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 07/30/2019
ms.openlocfilehash: f9b6e0d862d17badb1caa672852214cdd86abb49
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/28/2021
ms.locfileid: "98930825"
---
# <a name="scenario-reducer-is-slow-in-azure-hdinsight"></a>Scenario: trager verloopt langzaam in azure HDInsight

In dit artikel worden probleemoplossings stappen en mogelijke oplossingen voor problemen beschreven bij het gebruik van interactieve query onderdelen in azure HDInsight-clusters.

## <a name="issue"></a>Probleem

Bij het uitvoeren van een query, zoals `insert into table1 partition(a,b) select a,b,c from table2` het query plan, wordt een aantal verminderers gestart, maar de gegevens van elke partitie gaan naar één verkleinings proces. Hierdoor wordt de query zo langzaam als de tijd die nodig is voor de maximale partitie.

## <a name="cause"></a>Oorzaak

Open [Beeline](../hadoop/apache-hadoop-use-hive-beeline.md) en controleer de waarde van set `hive.optimize.sort.dynamic.partition` .

De waarde van deze variabele moet worden ingesteld op waar/onwaar, op basis van de aard van de gegevens.

Als de partities in de invoer tabel minder zijn (minder dan 10), en dus het aantal uitvoer partities is, en de variabele is ingesteld op `true` , worden gegevens globaal gesorteerd en geschreven met één verminderr per partitie. Zelfs als het aantal beschik bare verminderingen groter is, kunnen enkele verminderingen achterblijven vanwege het hellen van gegevens en de maximale parallellisme niet kan worden bereikt. Wanneer deze is gewijzigd in `false` , kan er meer dan één verlaager een enkele partitie afhandelen en kunnen meerdere kleinere bestanden worden wegge schreven, wat leidt tot een snellere invoeging. Dit kan van invloed zijn op verdere query's als gevolg van de aanwezigheid van kleinere bestanden.

Een waarde van is `true` zinvol wanneer het aantal partities groter is en de gegevens niet worden scheefgetrokken. In dergelijke gevallen wordt het resultaat van de kaart fase zodanig geschreven dat elke partitie wordt afgehandeld door één lagere vertrager, wat resulteert in betere volgende query prestaties.

## <a name="resolution"></a>Oplossing

1. Probeer de gegevens opnieuw te partitioneren zodat deze in meerdere partities kunnen worden genormaliseerd.

1. Als #1 niet mogelijk is, stelt u de waarde van de configuratie in op False in Beeline-sessie en voert u de query opnieuw uit. `set hive.optimize.sort.dynamic.partition=false`. Het instellen van de waarde op ONWAAR op een cluster niveau wordt niet aanbevolen. De waarde van `true` is optimaal en stelt de para meter zo nodig in op basis van de aard van de gegevens en de query.

## <a name="next-steps"></a>Volgende stappen

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]