---
title: Apache Spark langzaam wanneer Azure HDInsight-opslag veel bestanden bevat
description: Apache Spark taak wordt traag uitgevoerd wanneer de Azure storage-container veel bestanden in azure HDInsight bevat
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 08/21/2019
ms.openlocfilehash: c26baec66248ca00ef212acf3d773c2566b3aea9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98946364"
---
# <a name="apache-spark-job-run-slowly-when-the-azure-storage-container-contains-many-files-in-azure-hdinsight"></a>Apache Spark-taken worden langzaam uitgevoerd wanneer de Azure-opslagcontainer veel bestanden bevat in Azure HDInsight

In dit artikel worden probleemoplossings stappen en mogelijke oplossingen voor problemen beschreven bij het gebruik van Apache Spark-onderdelen in azure HDInsight-clusters.

## <a name="issue"></a>Probleem

Bij het uitvoeren van een HDInsight-cluster wordt de Apache Spark-taak die naar de Azure storage-container schrijft, traag wanneer er veel bestanden/submappen zijn. Het duurt bijvoorbeeld 20 seconden wanneer u naar een nieuwe container schrijft, maar ongeveer 2 minuten wanneer u naar een container met van persoonlijkheden-bestanden schrijft.

## <a name="cause"></a>Oorzaak

Dit is een bekend Spark-probleem. De vertraging is afkomstig van de- `ListBlob` en- `GetBlobProperties` bewerkingen tijdens de uitvoering van Spark-taken.

Voor het bijhouden van partities moet Spark een met `FileStatusCache` informatie over de directory-structuur onderhouden. Met deze cache kunnen Spark de paden parseren en op de hoogte zijn van de beschik bare partities. Het voor deel van het bijhouden van partities is dat Spark alleen de benodigde bestanden aanraakt wanneer u gegevens leest. Om deze informatie up-to-date te houden wanneer u nieuwe gegevens schrijft, moet Spark alle bestanden in de map weer geven en deze cache bijwerken.

In Spark 2,1, terwijl de cache na elke schrijf bewerking niet hoeft te worden bijgewerkt, controleert Spark of een bestaande partitie kolom overeenkomt met de voor waarde die wordt voorgesteld in de huidige schrijf aanvraag, zodat deze ook aan het begin van elke schrijf bewerking wordt weer gegeven.

In Spark 2,2, bij het schrijven van gegevens met de toevoeg modus, moet dit prestatie probleem worden opgelost.

## <a name="resolution"></a>Oplossing

Wanneer u een gegevensgestuurde gegevensset maakt, is het belang rijk dat u een partitie schema gebruikt waarmee het aantal bestanden wordt beperkt dat door Spark moet worden weer gegeven om de te kunnen bijwerken `FileStatusCache` .

Voor elke ne micro batch waarbij N %100 = = 0 (100 is een voor beeld), verplaatst u de bestaande gegevens naar een andere map, die door Spark kan worden geladen.

## <a name="next-steps"></a>Volgende stappen

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]