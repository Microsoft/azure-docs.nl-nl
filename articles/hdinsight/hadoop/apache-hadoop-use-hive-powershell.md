---
title: Apache Hive gebruiken met Power shell in HDInsight-Azure
description: Power shell gebruiken voor het uitvoeren van Apache Hive query's in Apache Hadoop in azure HDInsight
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 12/24/2019
ms.openlocfilehash: 43cac81717027b39210d6cd9cb9ad0baa596f50d
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/28/2021
ms.locfileid: "98943180"
---
# <a name="run-apache-hive-queries-using-powershell"></a>Apache Hive query's uitvoeren met Power shell

[!INCLUDE [hive-selector](../../../includes/hdinsight-selector-use-hive.md)]

Dit document bevat een voor beeld van het gebruik van Azure PowerShell om Apache Hive query's uit te voeren in een Apache Hadoop in HDInsight-cluster.

> [!NOTE]  
> Dit document biedt geen gedetailleerde beschrijving van de HiveQL-instructies die worden gebruikt in de voor beelden. Zie [Apache Hive gebruiken met Apache Hadoop op HDInsight](hdinsight-use-hive.md)voor meer informatie over de HiveQL die in dit voor beeld wordt gebruikt.

## <a name="prerequisites"></a>Vereisten

* Een Apache Hadoop cluster in HDInsight. Zie aan de [slag met HDInsight op Linux](./apache-hadoop-linux-tutorial-get-started.md).

* De [Az-module](/powershell/azure/) van PowerShell geïnstalleerd.

## <a name="run-a-hive-query"></a>Een Hive-query uitvoeren

Azure PowerShell biedt *cmdlets* waarmee u Hive-Query's op HDInsight op afstand kunt uitvoeren. Intern maken de cmdlets REST-aanroepen naar [WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat) op het HDInsight-cluster.

De volgende cmdlets worden gebruikt bij het uitvoeren van Hive-query's in een extern HDInsight-cluster:

* `Connect-AzAccount`: Verifieert Azure PowerShell aan uw Azure-abonnement.
* `New-AzHDInsightHiveJobDefinition`: Hiermee wordt een *taak definitie* gemaakt met behulp van de opgegeven HiveQL-instructies.
* `Start-AzHDInsightJob`: De taak definitie wordt naar HDInsight verzonden en de taak wordt gestart. Er wordt een *taak* object geretourneerd.
* `Wait-AzHDInsightJob`: Het taak object wordt gebruikt om de status van de taak te controleren. Er wordt gewacht tot de taak is voltooid of de wacht tijd is overschreden.
* `Get-AzHDInsightJobOutput`: Wordt gebruikt om de uitvoer van de taak op te halen.
* `Invoke-AzHDInsightHiveJob`: Wordt gebruikt om HiveQL-instructies uit te voeren. Deze cmdlet blokkeert de query en retourneert vervolgens de resultaten.
* `Use-AzHDInsightCluster`: Hiermee stelt u in dat het huidige cluster moet worden gebruikt voor de `Invoke-AzHDInsightHiveJob` opdracht.

De volgende stappen laten zien hoe u deze cmdlets kunt gebruiken om een taak uit te voeren in uw HDInsight-cluster:

1. Gebruik een editor om de volgende code op te slaan als `hivejob.ps1` .

    [!code-powershell[main](../../../powershell_scripts/hdinsight/use-hive/use-hive.ps1?range=5-42)]

2. Open een nieuwe **Azure PowerShell** opdracht prompt. Wijzig de mappen in de locatie van het `hivejob.ps1` bestand en gebruik vervolgens de volgende opdracht om het script uit te voeren:

    ```azurepowershell
    .\hivejob.ps1
    ```

    Wanneer het script wordt uitgevoerd, wordt u gevraagd om de cluster naam en de referenties van het HTTPS/Cluster-beheer account in te voeren. U wordt mogelijk ook gevraagd om u aan te melden bij uw Azure-abonnement.

3. Wanneer de taak is voltooid, wordt er informatie weer gegeven die vergelijkbaar is met de volgende tekst:

    ```output
    Display the standard output...
    2012-02-03      18:35:34        SampleClass0    [ERROR] incorrect       id
    2012-02-03      18:55:54        SampleClass1    [ERROR] incorrect       id
    2012-02-03      19:25:27        SampleClass4    [ERROR] incorrect       id
    ```

4. Zoals eerder vermeld, `Invoke-Hive` kan worden gebruikt om een query uit te voeren en te wachten op de reactie. Gebruik het volgende script om te zien hoe Invoke-Hive werkt:

    [!code-powershell[main](../../../powershell_scripts/hdinsight/use-hive/use-hive.ps1?range=50-71)]

    De uitvoer ziet er als volgt uit:

    ```output
    2012-02-03    18:35:34    SampleClass0    [ERROR]    incorrect    id
    2012-02-03    18:55:54    SampleClass1    [ERROR]    incorrect    id
    2012-02-03    19:25:27    SampleClass4    [ERROR]    incorrect    id
    ```

   > [!NOTE]  
   > Voor langere HiveQL query's kunt u de script bestanden Azure PowerShell **hier-strings** of HiveQL gebruiken. Het volgende code fragment laat zien hoe u de cmdlet kunt gebruiken `Invoke-Hive` om een HiveQL-script bestand uit te voeren. Het HiveQL-script bestand moet worden geüpload naar wasbs://.
   >
   > `Invoke-AzHDInsightHiveJob -File "wasbs://<ContainerName>@<StorageAccountName>/<Path>/query.hql"`
   >
   > Zie [hier](/powershell/module/microsoft.powershell.core/about/about_quoting_rules#here-strings)voor meer informatie over de **teken reeksen**.

## <a name="troubleshooting"></a>Problemen oplossen

Als er geen informatie wordt geretourneerd wanneer de taak is voltooid, raadpleegt u de fouten Logboeken. Als u de fout gegevens voor deze taak wilt weer geven, voegt u het volgende toe aan het einde van het `hivejob.ps1` bestand, slaat u het op en voert u het vervolgens opnieuw uit.

```powershell
# Print the output of the Hive job.
Get-AzHDInsightJobOutput `
        -Clustername $clusterName `
        -JobId $job.JobId `
        -HttpCredential $creds `
        -DisplayOutputType StandardError
```

Met deze cmdlet wordt de informatie geretourneerd die wordt geschreven naar STDERR tijdens taak verwerking.

## <a name="summary"></a>Samenvatting

Zoals u kunt zien, biedt Azure PowerShell een eenvoudige manier om Hive-query's uit te voeren in een HDInsight-cluster, de taak status te controleren en de uitvoer op te halen.

## <a name="next-steps"></a>Volgende stappen

Voor algemene informatie over Hive in HDInsight:

* [Apache Hive gebruiken met Apache Hadoop op HDInsight](hdinsight-use-hive.md)

Voor informatie over andere manieren om met Hadoop in HDInsight te werken:

* [MapReduce gebruiken met Apache Hadoop op HDInsight](hdinsight-use-mapreduce.md)
