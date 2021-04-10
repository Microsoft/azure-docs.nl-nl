---
title: MapReduce en Power shell gebruiken met Apache Hadoop-Azure HDInsight
description: Meer informatie over hoe u Power shell kunt gebruiken om MapReduce-taken op afstand uit te voeren met Apache Hadoop op HDInsight.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 01/08/2020
ms.openlocfilehash: 16c6c5e317591b70c3a1300453093fc715e213fb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98939672"
---
# <a name="run-mapreduce-jobs-with-apache-hadoop-on-hdinsight-using-powershell"></a>MapReduce-taken uitvoeren met Apache Hadoop op HDInsight met behulp van Power shell

[!INCLUDE [mapreduce-selector](../../../includes/hdinsight-selector-use-mapreduce.md)]

Dit document bevat een voor beeld van het gebruik van Azure PowerShell om een MapReduce-taak uit te voeren in een Hadoop in HDInsight-cluster.

## <a name="prerequisites"></a>Vereisten

* Een Apache Hadoop cluster in HDInsight. Zie [Apache Hadoop-clusters maken met behulp van Azure Portal](../hdinsight-hadoop-create-linux-clusters-portal.md).

* De [Az-module](/powershell/azure/) van PowerShell geïnstalleerd.

## <a name="run-a-mapreduce-job"></a>Een MapReduce-taak uitvoeren

Azure PowerShell biedt *cmdlets* waarmee u op afstand MapReduce-taken kunt uitvoeren op HDInsight. Intern maakt Power shell REST-aanroepen naar [WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat) (voorheen Templeton) die worden uitgevoerd op het HDInsight-cluster.

De volgende cmdlets worden gebruikt voor het uitvoeren van MapReduce-taken in een extern HDInsight-cluster.

|Cmdlet | Beschrijving |
|---|---|
|Connect-AzAccount|Verifieert Azure PowerShell aan uw Azure-abonnement.|
|New-AzHDInsightMapReduceJobDefinition|Hiermee wordt een nieuwe *taak definitie* gemaakt met behulp van de opgegeven MapReduce-gegevens.|
|Start-AzHDInsightJob|De taak definitie wordt verzonden naar HDInsight en de taak wordt gestart. Er wordt een *taak* object geretourneerd.|
|Wait-AzHDInsightJob|Gebruikt het taak object om de status van de taak te controleren. Er wordt gewacht tot de taak is voltooid of de wacht tijd is overschreden.|
|Get-AzHDInsightJobOutput|Wordt gebruikt om de uitvoer van de taak op te halen.|

De volgende stappen laten zien hoe u deze cmdlets kunt gebruiken om een taak uit te voeren in uw HDInsight-cluster.

1. Gebruik een editor om de volgende code op te slaan als **mapreducejob.ps1**.

    [!code-powershell[main](../../../powershell_scripts/hdinsight/use-mapreduce/use-mapreduce.ps1?range=5-69)]

2. Open een nieuwe **Azure PowerShell** opdracht prompt. Wijzig de mappen in de locatie van het **mapreducejob.ps1** bestand en gebruik vervolgens de volgende opdracht om het script uit te voeren:

    ```azurepowershell
    .\mapreducejob.ps1
    ```

    Wanneer u het script uitvoert, wordt u gevraagd de naam van het HDInsight-cluster en de cluster aanmelding op te vragen. U wordt mogelijk ook gevraagd om u aan te melden bij uw Azure-abonnement.

3. Wanneer de taak is voltooid, ontvangt u uitvoer die er ongeveer als volgt uitziet:

    ```output
    Cluster         : CLUSTERNAME
    ExitCode        : 0
    Name            : wordcount
    PercentComplete : map 100% reduce 100%
    Query           :
    State           : Completed
    StatusDirectory : f1ed2028-afe8-402f-a24b-13cc17858097
    SubmissionTime  : 12/5/2014 8:34:09 PM
    JobId           : job_1415949758166_0071
    ```

    Deze uitvoer geeft aan dat de taak is voltooid.

    > [!NOTE]  
    > Zie [probleem oplossing](#troubleshooting)als de **ExitCode** een andere waarde dan 0 heeft.

    In dit voor beeld worden de gedownloade bestanden ook opgeslagen in een **output.txt** -bestand in de map waarin u het script uitvoert.

### <a name="view-output"></a>Uitvoer weer geven

Als u de woorden en aantallen wilt zien die door de taak worden geproduceerd, opent u het **output.txt** bestand in een tekst editor.

> [!NOTE]  
> De uitvoer bestanden van een MapReduce-taak zijn onveranderbaar. Dus als u dit voor beeld opnieuw uitvoert, moet u de naam van het uitvoer bestand wijzigen.

## <a name="troubleshooting"></a>Problemen oplossen

Als er geen informatie wordt geretourneerd wanneer de taak is voltooid, kunt u de fouten voor de taak weer geven. Als u de fout gegevens voor deze taak wilt weer geven, voegt u de volgende opdracht toe aan het einde van het **mapreducejob.ps1** -bestand. Sla het bestand op en voer het script opnieuw uit.

```powershell
# Print the output of the WordCount job.
Write-Host "Display the standard output ..." -ForegroundColor Green
Get-AzHDInsightJobOutput `
        -Clustername $clusterName `
        -JobId $wordCountJob.JobId `
        -HttpCredential $creds `
        -DisplayOutputType StandardError
```

Met deze cmdlet wordt de informatie geretourneerd die naar STDERR is geschreven terwijl de taak wordt uitgevoerd.

## <a name="next-steps"></a>Volgende stappen

Zoals u kunt zien, biedt Azure PowerShell een eenvoudige manier om MapReduce-taken uit te voeren op een HDInsight-cluster, de taak status te controleren en de uitvoer op te halen. Voor informatie over andere manieren om met Hadoop in HDInsight te werken:

* [MapReduce gebruiken in HDInsight Hadoop](hdinsight-use-mapreduce.md)
* [Apache Hive gebruiken met Apache Hadoop op HDInsight](hdinsight-use-hive.md)