---
title: MapReduce-taken verzenden met HDInsight .NET SDK-Azure
description: Meer informatie over het verzenden van MapReduce-taken naar Azure HDInsight Apache Hadoop met behulp van HDInsight .NET SDK.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive, devx-track-csharp
ms.date: 01/15/2020
ms.openlocfilehash: 8fbcb66be11c7c77a9bfaf0e6ec790622dcbbda7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98932174"
---
# <a name="run-mapreduce-jobs-using-hdinsight-net-sdk"></a>MapReduce-taken uitvoeren met behulp van de HDInsight .NET-SDK

[!INCLUDE [mapreduce-selector](../../../includes/hdinsight-selector-use-mapreduce.md)]

Meer informatie over het verzenden van MapReduce-taken met behulp van HDInsight .NET SDK. HDInsight-clusters worden geleverd met een jar-bestand met enkele MapReduce-voor beelden. Het jar-bestand is `/example/jars/hadoop-mapreduce-examples.jar` .  Een van de voor beelden is **WordCount**. U ontwikkelt een C#-console toepassing om een WordCount-taak te verzenden.  De taak leest het `/example/data/gutenberg/davinci.txt` bestand en voert de resultaten uit naar `/example/data/davinciwordcount` .  Als u de toepassing opnieuw wilt uitvoeren, moet u de uitvoermap opschonen.

> [!NOTE]  
> De stappen in dit artikel moeten worden uitgevoerd vanaf een Windows-client. Voor informatie over het gebruik van een Linux-, OS X-of UNIX-client om met hive te werken, gebruikt u de tabblad kiezer die boven aan het artikel wordt weer gegeven.

## <a name="prerequisites"></a>Vereisten

* Een Apache Hadoop cluster in HDInsight. Zie [Apache Hadoop-clusters maken met behulp van Azure Portal](../hdinsight-hadoop-create-linux-clusters-portal.md).

* [Visual Studio](https://visualstudio.microsoft.com/vs/community/).

## <a name="submit-mapreduce-jobs-using-hdinsight-net-sdk"></a>MapReduce-taken verzenden met behulp van HDInsight .NET SDK

De HDInsight .NET SDK biedt .NET-client Bibliotheken, waarmee u eenvoudiger met HDInsight-clusters kunt werken vanuit .NET.

1. Start Visual Studio en maak een C#-console toepassing.

1. Ga naar **extra** NuGet Package Manager-  >    >  **console** en voer de volgende opdracht in:

    ```   
    Install-Package Microsoft.Azure.Management.HDInsight.Job
    ```

1. Kopieer de onderstaande code naar **programma. cs**. Bewerk de code vervolgens door de waarden in te stellen voor: `existingClusterName` , `existingClusterPassword` , `defaultStorageAccountName` , `defaultStorageAccountKey` en `defaultStorageContainerName` .

    ```csharp
    using System.Collections.Generic;
    using System.IO;
    using System.Text;
    using System.Threading;
    using Microsoft.Azure.Management.HDInsight.Job;
    using Microsoft.Azure.Management.HDInsight.Job.Models;
    using Hyak.Common;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Blob;
    
    namespace SubmitHDInsightJobDotNet
    {
        class Program
        {
            private static HDInsightJobManagementClient _hdiJobManagementClient;
    
            private const string existingClusterName = "<Your HDInsight Cluster Name>";
            private const string existingClusterPassword = "<Cluster User Password>";
            private const string defaultStorageAccountName = "<Default Storage Account Name>"; 
            private const string defaultStorageAccountKey = "<Default Storage Account Key>";
            private const string defaultStorageContainerName = "<Default Blob Container Name>";
    
            private const string existingClusterUsername = "admin";
            private const string existingClusterUri = existingClusterName + ".azurehdinsight.net";
            private const string sourceFile = "/example/data/gutenberg/davinci.txt";
            private const string outputFolder = "/example/data/davinciwordcount";
    
            static void Main(string[] args)
            {
                System.Console.WriteLine("The application is running ...");
    
                var clusterCredentials = new BasicAuthenticationCloudCredentials { Username = existingClusterUsername, Password = existingClusterPassword };
                _hdiJobManagementClient = new HDInsightJobManagementClient(existingClusterUri, clusterCredentials);
    
                SubmitMRJob();
    
                System.Console.WriteLine("Press ENTER to continue ...");
                System.Console.ReadLine();
            }
    
            private static void SubmitMRJob()
            {
                List<string> args = new List<string> { { "/example/data/gutenberg/davinci.txt" }, { "/example/data/davinciwordcount" } };
    
                var paras = new MapReduceJobSubmissionParameters
                {
                    JarFile = @"/example/jars/hadoop-mapreduce-examples.jar",
                    JarClass = "wordcount",
                    Arguments = args
                };
    
                System.Console.WriteLine("Submitting the MR job to the cluster...");
                var jobResponse = _hdiJobManagementClient.JobManagement.SubmitMapReduceJob(paras);
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
                System.Console.WriteLine("Job output is: ");
                var storageAccess = new AzureStorageAccess(defaultStorageAccountName, defaultStorageAccountKey,
                    defaultStorageContainerName);
    
                if (jobDetail.ExitValue == 0)
                {
                    // Create the storage account object
                    CloudStorageAccount storageAccount = CloudStorageAccount.Parse("DefaultEndpointsProtocol=https;AccountName=" +
                        defaultStorageAccountName +
                        ";AccountKey=" + defaultStorageAccountKey);
    
                    // Create the blob client.
                    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
    
                    // Retrieve reference to a previously created container.
                    CloudBlobContainer container = blobClient.GetContainerReference(defaultStorageContainerName);
    
                    CloudBlockBlob blockBlob = container.GetBlockBlobReference(outputFolder.Substring(1) + "/part-r-00000");
    
                    using (var stream = blockBlob.OpenRead())
                    {
                        using (StreamReader reader = new StreamReader(stream))
                        {
                            while (!reader.EndOfStream)
                            {
                                System.Console.WriteLine(reader.ReadLine());
                            }
                        }
                    }
                }
                else
                {
                    // fetch stderr output in case of failure
                    var output = _hdiJobManagementClient.JobManagement.GetJobErrorLogs(jobId, storageAccess);
    
                    using (var reader = new StreamReader(output, Encoding.UTF8))
                    {
                        string value = reader.ReadToEnd();
                        System.Console.WriteLine(value);
                    }
    
                }
            }
        }
    }

    ```

1. Druk op **F5** om de toepassing uit te voeren.

Als u de taak opnieuw wilt uitvoeren, moet u de naam van de map voor de taak uitvoer wijzigen in het voor beeld `/example/data/davinciwordcount` .

Wanneer de taak is voltooid, wordt de inhoud van het uitvoer bestand afgedrukt door de toepassing `part-r-00000` .

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u verschillende manieren geleerd om een HDInsight-cluster te maken. Lees de volgende artikelen voor meer informatie:

* Zie [Apache Hive Query's uitvoeren met HDInsight .NET SDK](apache-hadoop-use-hive-dotnet-sdk.md)voor informatie over het verzenden van een Hive-taak.
* Zie voor het maken van HDInsight [-clusters Linux-gebaseerde Apache Hadoop clusters maken in HDInsight](../hdinsight-hadoop-provision-linux-clusters.md).
* Zie [Apache Hadoop clusters in HDInsight beheren](../hdinsight-administer-use-portal-linux.md)voor het beheren van HDInsight-clusters.
* Zie [referentie voor hdinsight .NET SDK](/dotnet/api/overview/azure/hdinsight)voor meer informatie over de HDINSIGHT .NET SDK.
* Zie [niet-interactieve verificatie van .net HDInsight-toepassingen maken](../hdinsight-create-non-interactive-authentication-dotnet-applications.md)voor niet-interactieve verificatie naar Azure.
