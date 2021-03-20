---
title: De Ambari TEZ-weer gave van Apache wordt langzaam in azure HDInsight geladen
description: De Apache Ambari TEZ-weer gave kan langzaam worden geladen of helemaal niet worden geladen in azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 07/30/2019
ms.openlocfilehash: 4fe66b3104be0351a9b0e1df6b6545f71ff276ab
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98930771"
---
# <a name="scenario-apache-ambari-tez-view-loads-slowly-in-azure-hdinsight"></a>Scenario: de Ambari-weer gave van Apache TEZ wordt langzaam in azure HDInsight geladen

In dit artikel worden probleemoplossings stappen en mogelijke oplossingen voor problemen beschreven bij het gebruik van interactieve query onderdelen in azure HDInsight-clusters.

## <a name="issue"></a>Probleem

De Apache Ambari TEZ-weer gave kan langzaam worden geladen of helemaal niet worden geladen. Wanneer u de weer gave Ambari TEZ laadt, ziet u mogelijk processen op hoofd knooppunten niet meer reageert.

## <a name="cause"></a>Oorzaak

Het openen van garen-Api's van coonderdelen kan soms slechte prestaties hebben voor clusters die zijn gemaakt vóór okt 2017 als het cluster een groot aantal Hive-taken uitvoert.

## <a name="resolution"></a>Oplossing

Dit is een probleem dat is opgelost in OCT 2017. Als u het cluster opnieuw maakt, wordt het niet opnieuw uitgevoerd in dit probleem.

## <a name="next-steps"></a>Volgende stappen

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]