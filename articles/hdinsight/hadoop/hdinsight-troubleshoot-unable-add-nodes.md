---
title: Kan geen knoop punten toevoegen aan een Azure HDInsight-cluster
description: Problemen oplossen waarom kan geen knoop punten toevoegen aan Apache Hadoop cluster in azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 07/31/2019
ms.openlocfilehash: b11d1edef2f3a6fa0fb39c76d1f25ec05ff15d07
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98944319"
---
# <a name="scenario-unable-to-add-nodes-to-azure-hdinsight-cluster"></a>Scenario: kan geen knoop punten toevoegen aan een Azure HDInsight-cluster

In dit artikel worden de stappen beschreven voor het oplossen van problemen en mogelijke oplossingen voor problemen bij het werken met Azure HDInsight-clusters.

## <a name="issue"></a>Probleem

Kan geen knoop punten toevoegen aan een Azure HDInsight-cluster.

## <a name="cause"></a>Oorzaak

Redenen kunnen variëren.

## <a name="resolution"></a>Oplossing

Gebruik de functie [cluster grootte](../hdinsight-scaling-best-practices.md) om het aantal extra kernen te berekenen dat nodig is voor het cluster. Dit wordt gebaseerd op het totale aantal cores in de nieuwe werkrolknooppunten. Probeer vervolgens een of meer van de volgende stappen:

* Controleer of er kernen beschikbaar zijn op de locatie van het cluster.

* Bekijk het aantal beschikbare cores op andere locaties. Overweeg het cluster opnieuw te maken op een andere locatie met voldoende beschikbare cores.

* Als u het aantal cores voor een specifieke locatie wilt verhogen, maakt u een ondersteuningsticket aan voor verhoging van het aantal HDInsight-cores.

## <a name="next-steps"></a>Volgende stappen

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]