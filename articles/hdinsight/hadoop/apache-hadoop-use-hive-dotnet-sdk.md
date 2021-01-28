---
title: Apache Hive query's uitvoeren met HDInsight .NET SDK-Azure
description: Meer informatie over het verzenden van Apache Hadoop taken naar Azure HDInsight Apache Hadoop met behulp van HDInsight .NET SDK.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive, devx-track-csharp
ms.date: 12/24/2019
ms.openlocfilehash: 17264fc2a7d0a51de545e329ccf12ad45ad49d46
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/28/2021
ms.locfileid: "98927238"
---
# <a name="run-apache-hive-queries-using-hdinsight-net-sdk"></a>Apache Hive query's uitvoeren met HDInsight .NET SDK

[!INCLUDE [hive-selector](../../../includes/hdinsight-selector-use-hive.md)]

Meer informatie over het verzenden van Apache Hive query's met behulp van HDInsight .NET SDK. U schrijft een C#-programma voor het indienen van een Hive-query voor het weer geven van Hive-tabellen en de resultaten.

> [!NOTE]  
> De stappen in dit artikel moeten worden uitgevoerd vanaf een Windows-client. Voor informatie over het gebruik van een Linux-, OS X-of UNIX-client om met hive te werken, gebruikt u de tabblad kiezer die boven aan het artikel wordt weer gegeven.

## <a name="prerequisites"></a>Vereisten

Voordat u met dit artikel begint, moet u beschikken over de volgende items:

* Een Apache Hadoop cluster in HDInsight. Zie [aan de slag met Hadoop op basis van Linux in HDInsight](apache-hadoop-linux-tutorial-get-started.md).

    > [!IMPORTANT]  
    > Vanaf 15 september 2017 ondersteunt de HDInsight .NET SDK alleen het retour neren van Hive-query resultaten van Azure Storage accounts. Als u dit voor beeld gebruikt met een HDInsight-cluster dat gebruikmaakt van Azure Data Lake Storage als primaire opslag, kunt u geen Zoek resultaten ophalen met behulp van de .NET SDK.

* [Visual Studio](https://visualstudio.microsoft.com/vs/community/) 2013 en hoger. U moet ten minste **.net desktop Development** voor het werk belasting installeren.

## <a name="run-a-hive-query"></a>Een Hive-query uitvoeren

De HDInsight .NET SDK biedt .NET-client Bibliotheken, waardoor het gemakkelijker is om met HDInsight-clusters van .NET te werken.

1. Maak een C#-console toepassing in Visual Studio.

1. Voer in de Nuget Package Manager-console de volgende opdracht uit:

    ```console
    Install-Package Microsoft.Azure.Management.HDInsight.Job
    ```

1. Bewerk de onderstaande code om de waarden voor variabelen te initialiseren: `ExistingClusterName, ExistingClusterUsername, ExistingClusterPassword,DefaultStorageAccountName,DefaultStorageAccountKey,DefaultStorageContainerName` . Gebruik vervolgens de gewijzigde code als de volledige inhoud van **Program.cs** in Visual Studio.

    ```csharp
    using System.Collections.Generic;
    using System.IO;
    using System.Text;
    using System.Threading;
    using Microsoft.Azure.Management.HDInsight.Job;
    using Microsoft.Azure.Management.HDInsight.Job.Models;
    using Hyak.Common;

    namespace SubmitHDInsightJobDotNet
    {
        class Program
        {
            private static HDInsightJobManagementClient _hdiJobManagementClient;

            private const string ExistingClusterName = "<Your HDInsight Cluster Name>";
            private const string ExistingClusterUsername = "<Cluster Username>";
            private const string ExistingClusterPassword = "<Cluster User Password>";

            // Only Azure Storage accounts are supported by the SDK
            private const string DefaultStorageAccountName = "<Default Storage Account Name>";
            private const string DefaultStorageAccountKey = "<Default Storage Account Key>";
            private const string DefaultStorageContainerName = "<Default Blob Container Name>";

            private const string ExistingClusterUri = ExistingClusterName + ".azurehdinsight.net";

            static void Main(string[] args)
            {
                System.Console.WriteLine("The application is running ...");

                var clusterCredentials = new BasicAuthenticationCloudCredentials { Username = ExistingClusterUsername, Password = ExistingClusterPassword };
                _hdiJobManagementClient = new HDInsightJobManagementClient(ExistingClusterUri, clusterCredentials);

                SubmitHiveJob();

                System.Console.WriteLine("Press ENTER to continue ...");
                System.Console.ReadLine();
            }

            private static void SubmitHiveJob()
            {
                Dictionary<string, string> defines = new Dictionary<string, string> { { "hive.execution.engine", "tez" }, { "hive.exec.reducers.max", "1" } };
                List<string> args = new List<string> { { "argA" }, { "argB" } };
                var parameters = new HiveJobSubmissionParameters
                {
                    Query = "SHOW TABLES",
                    Defines = defines,
                    Arguments = args
                };

                System.Console.WriteLine("Submitting the Hive job to the cluster...");
                var jobResponse = _hdiJobManagementClient.JobManagement.SubmitHiveJob(parameters);
                var jobId = jobResponse.JobSubmissionJsonResponse.Id;
                System.Console.WriteLine("Response status code is " + jobResponse.StatusCode);
                System.Console.WriteLine("JobId is " + jobId);

                System.Console.WriteLine("Waiting for the job completion ...");

                // Wait for job completion
                var jobDetail = _hdiJobManagementClient.JobManagement.GetJob(jobId).JobDetail;
                while (!jobDetail.Status.JobComplete)
                {
                    Thread.Sleep(1000);
                    jobDetail = _hdiJobManagementClient.JobManagement.GetJob(jobId).JobDetail;
                }

                // Get job output
                var storageAccess = new AzureStorageAccess(DefaultStorageAccountName, DefaultStorageAccountKey,
                    DefaultStorageContainerName);
                var output = (jobDetail.ExitValue == 0)
                    ? _hdiJobManagementClient.JobManagement.GetJobOutput(jobId, storageAccess) // fetch stdout output in case of success
                    : _hdiJobManagementClient.JobManagement.GetJobErrorLogs(jobId, storageAccess); // fetch stderr output in case of failure

                System.Console.WriteLine("Job output is: ");

                using (var reader = new StreamReader(output, Encoding.UTF8))
                {
                    string value = reader.ReadToEnd();
                    System.Console.WriteLine(value);
                }
            }
        }
    }
    ```

1. Druk op **F5** om de toepassing uit te voeren.

De uitvoer van de toepassing moet er ongeveer als volgt uitzien:

![Uitvoer van HDInsight Hadoop-Hive-taak](./media/apache-hadoop-use-hive-dotnet-sdk/hdinsight-hadoop-use-hive-net-sdk-output.png)

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u geleerd hoe u Apache Hive query's kunt verzenden met behulp van HDInsight .NET SDK. Lees de volgende artikelen voor meer informatie:

* [Aan de slag met Azure HDInsight](apache-hadoop-linux-tutorial-get-started.md)
* [Apache Hadoop clusters maken in HDInsight](../hdinsight-hadoop-provision-linux-clusters.md)
* [Referentie voor HDInsight .NET SDK](/dotnet/api/overview/azure/hdinsight)
* [Apache Sqoop gebruiken met HDInsight](apache-hadoop-use-sqoop-mac-linux.md)
* [Toepassingen zonder interactieve verificatie voor .NET HDInsight maken](../hdinsight-create-non-interactive-authentication-dotnet-applications.md)
