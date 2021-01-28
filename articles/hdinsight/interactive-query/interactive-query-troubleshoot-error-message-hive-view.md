---
title: Fout bericht niet weer gegeven in Apache Hive weer gave-Azure HDInsight
description: De query mislukt in Apache Hive weergave zonder enige details van Azure HDInsight-cluster.
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 07/30/2019
ms.openlocfilehash: c60e06e8f37e7aed0d0a0df661690a2b1f32dbd5
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/28/2021
ms.locfileid: "98931004"
---
# <a name="scenario-query-error-message-not-displayed-in-apache-hive-view-in-azure-hdinsight"></a>Scenario: query-fout bericht wordt niet weer gegeven in Apache Hive weer gave in azure HDInsight

In dit artikel worden probleemoplossings stappen en mogelijke oplossingen voor problemen beschreven bij het gebruik van interactieve query onderdelen in azure HDInsight-clusters.

## <a name="issue"></a>Probleem

Het Apache Hive weergave query fout bericht ziet er ongeveer als volgt uit, zonder verdere informatie:

```
"Failed to execute query. <a href="#/messages/1">(details)</a>"
```

## <a name="cause"></a>Oorzaak

Soms is het fout bericht van een query fout te groot om te worden weer gegeven op de hoofd pagina van de Hive-weer gave.

## <a name="resolution"></a>Oplossing

Ga naar het tabblad meldingen in de rechter bovenhoek van de Hive_view om de volledige stacktrace en het fout bericht te zien.

## <a name="next-steps"></a>Volgende stappen

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]