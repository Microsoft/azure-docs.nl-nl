---
title: Apache Hadoop clusters in HDInsight beheren met .NET SDK-Azure
description: Meer informatie over het uitvoeren van beheer taken voor de Apache Hadoop clusters in HDInsight met behulp van HDInsight .NET SDK.
ms.service: hdinsight
ms.custom: hdinsightactive, devx-track-csharp
ms.topic: conceptual
ms.date: 05/14/2018
ms.openlocfilehash: 86e279fef9561397343b3e6cb55ac335a5b39542
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98942844"
---
# <a name="manage-apache-hadoop-clusters-in-hdinsight-by-using-net-sdk"></a>Apache Hadoop-clusters in HDInsight beheren met behulp van de .NET-SDK

[!INCLUDE [selector](../../includes/hdinsight-portal-management-selector.md)]

Meer informatie over het beheren van HDInsight-clusters met behulp van de [HDInsight.NET-SDK](/dotnet/api/overview/azure/hdinsight).

**Vereisten**

Voordat u dit artikel gaat lezen, moet u beschikken over het volgende:

* **Een Azure-abonnement**. Zie [Gratis proefversie van Azure ophalen](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

## <a name="connect-to-azure-hdinsight"></a>Verbinding maken met Azure HDInsight

U hebt de volgende NuGet-pakketten nodig:

```powershell
Install-Package Microsoft.Rest.ClientRuntime.Azure.Authentication -Pre
Install-Package Microsoft.Azure.Management.ResourceManager -Pre
Install-Package Microsoft.Azure.Management.HDInsight
```

In het volgende code voorbeeld ziet u hoe u verbinding kunt maken met Azure voordat u HDInsight-clusters kunt beheren onder uw Azure-abonnement.

```csharp
using System;
using Microsoft.Azure;
using Microsoft.Azure.Management.HDInsight;
using Microsoft.Azure.Management.HDInsight.Models;
using Microsoft.Azure.Management.ResourceManager;
using Microsoft.IdentityModel.Clients.ActiveDirectory;
using Microsoft.Rest;
using Microsoft.Rest.Azure.Authentication;

namespace HDInsightManagement
{
    class Program
    {
        private static HDInsightManagementClient _hdiManagementClient;
        // Replace with your AAD tenant ID if necessary
        private const string TenantId = UserTokenProvider.CommonTenantId; 
        private const string SubscriptionId = "<Your Azure Subscription ID>";
        // This is the GUID for the PowerShell client. Used for interactive logins in this example.
        private const string ClientId = "1950a258-227b-4e31-a9cf-717495945fc2";

        static void Main(string[] args)
        {
            // Authenticate and get a token
            var authToken = Authenticate(TenantId, ClientId, SubscriptionId);
            // Flag subscription for HDInsight, if it isn't already.
            EnableHDInsight(authToken);
            // Get an HDInsight management client
            _hdiManagementClient = new HDInsightManagementClient(authToken);

            // insert code here

            System.Console.WriteLine("Press ENTER to continue");
            System.Console.ReadLine();
        }

        /// <summary>
        /// Authenticate to an Azure subscription and retrieve an authentication token
        /// </summary>
        static TokenCloudCredentials Authenticate(string TenantId, string ClientId, string SubscriptionId)
        {
            var authContext = new AuthenticationContext("https://login.microsoftonline.com/" + TenantId);
            var tokenAuthResult = authContext.AcquireToken("https://management.core.windows.net/", 
                ClientId, 
                new Uri("urn:ietf:wg:oauth:2.0:oob"), 
                PromptBehavior.Always, 
                UserIdentifier.AnyUser);
            return new TokenCloudCredentials(SubscriptionId, tokenAuthResult.AccessToken);
        }
        /// <summary>
        /// Marks your subscription as one that can use HDInsight, if it has not already been marked as such.
        /// </summary>
        /// <remarks>This is essentially a one-time action; if you have already done something with HDInsight
        /// on your subscription, then this isn't needed at all and will do nothing.</remarks>
        /// <param name="authToken">An authentication token for your Azure subscription</param>
        static void EnableHDInsight(TokenCloudCredentials authToken)
        {
            // Create a client for the Resource manager and set the subscription ID
            var resourceManagementClient = new ResourceManagementClient(new TokenCredentials(authToken.Token));
            resourceManagementClient.SubscriptionId = SubscriptionId;
            // Register the HDInsight provider
            var rpResult = resourceManagementClient.Providers.Register("Microsoft.HDInsight");
        }
    }
}
```

Er wordt een prompt weer gegeven wanneer u dit programma uitvoert.  Als u de prompt niet wilt weer geven, raadpleegt u [niet-interactieve verificatie voor .net HDInsight-toepassingen maken](hdinsight-create-non-interactive-authentication-dotnet-applications.md).


## <a name="list-clusters"></a>Clusters weergeven

Het volgende code fragment bevat clusters en enkele eigenschappen:

```csharp
var results = _hdiManagementClient.Clusters.List();
foreach (var name in results.Clusters) {
    Console.WriteLine("Cluster Name: " + name.Name);
    Console.WriteLine("\t Cluster type: " + name.Properties.ClusterDefinition.ClusterType);
    Console.WriteLine("\t Cluster location: " + name.Location);
    Console.WriteLine("\t Cluster version: " + name.Properties.ClusterVersion);
}
```

## <a name="delete-clusters"></a>Clusters verwijderen

Gebruik het volgende code fragment om een cluster synchroon of asynchroon te verwijderen: 

```csharp
_hdiManagementClient.Clusters.Delete("<Resource Group Name>", "<Cluster Name>");
_hdiManagementClient.Clusters.DeleteAsync("<Resource Group Name>", "<Cluster Name>");
```

## <a name="scale-clusters"></a>Clusters schalen

Met de functie voor het schalen van clusters kunt u het aantal worker-knoop punten wijzigen dat wordt gebruikt door een cluster dat wordt uitgevoerd in azure HDInsight zonder dat u het cluster opnieuw hoeft te maken.

> [!NOTE]  
> Alleen clusters met HDInsight-versie 3.1.3 of hoger worden ondersteund. Als u niet zeker bent van de versie van uw cluster, kunt u de pagina eigenschappen controleren.  Zie [clusters weer geven en tonen](hdinsight-administer-use-portal-linux.md#showClusters).

De gevolgen van het wijzigen van het aantal gegevens knooppunten voor elk type cluster dat wordt ondersteund door HDInsight:

* Apache Hadoop
  
    U kunt het aantal worker-knoop punten in een Hadoop-cluster dat wordt uitgevoerd, probleemloos verhogen zonder dat dit van invloed is op wachtende of actieve taken. Nieuwe taken kunnen ook worden verzonden terwijl de bewerking wordt uitgevoerd. Fouten in een schaal bewerking worden op de juiste wijze afgehandeld, zodat het cluster altijd in een functionele status blijft.
  
    Wanneer een Hadoop-cluster omlaag wordt geschaald door het aantal gegevens knooppunten te verminderen, worden enkele van de services in het cluster opnieuw gestart. Dit zorgt ervoor dat alle actieve en in behandeling zijnde taken mislukken wanneer de schaal bewerking is voltooid. U kunt de taken echter opnieuw verzenden nadat de bewerking is voltooid.
* Apache HBase
  
    U kunt tijdens het uitvoeren naadloos knoop punten toevoegen aan of verwijderen uit uw HBase-cluster. Regionale servers worden automatisch gebalanceerd binnen enkele minuten van het volt ooien van de schaal bewerking. U kunt de regionale servers echter ook hand matig verdelen door u aan te melden bij de hoofd knooppunt van het cluster en door de volgende opdrachten uit te voeren vanuit een opdracht prompt venster:
  

    ```bash
    >pushd %HBASE_HOME%\bin
    >hbase shell
    >balancer
    ```

* Apache Storm
  
    U kunt eenvoudig gegevens knooppunten toevoegen aan of verwijderen uit uw Storm-cluster tijdens de uitvoering. Maar nadat de schaal bewerking is voltooid, moet u de topologie opnieuw verdelen.
  
    Herverdeling kan op twee manieren worden uitgevoerd:
  
  * Storm-webinterface
  * Opdracht regel interface (CLI)-hulp programma
    
    Raadpleeg de documentatie van [Apache Storm](https://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html) voor meer informatie.
    
    De gebruikers interface van Storm is beschikbaar op het HDInsight-cluster:
    
    ![Herverdeling van HDInsight Storm-schaal](./media/hdinsight-administer-use-powershell/hdinsight-portal-scale-cluster-storm-rebalance.png)
    
    Hier volgt een voor beeld van het gebruik van de CLI-opdracht voor het opnieuw verdelen van de Storm-topologie:
    

    ```console
    ## Reconfigure the topology "mytopology" to use 5 worker processes,
    ## the spout "blue-spout" to use 3 executors, and
    ## the bolt "yellow-bolt" to use 10 executors
    $ storm rebalance mytopology -n 5 -e blue-spout=3 -e yellow-bolt=10
    ```

Het volgende code fragment laat zien hoe u het formaat van een cluster synchroon of asynchroon kunt wijzigen:

```csharp
_hdiManagementClient.Clusters.Resize("<Resource Group Name>", "<Cluster Name>", <New Size>);   
_hdiManagementClient.Clusters.ResizeAsync("<Resource Group Name>", "<Cluster Name>", <New Size>);   
```

## <a name="grantrevoke-access"></a>Toegang verlenen/intrekken

HDInsight-clusters hebben de volgende HTTP-webservices (al deze services hebben REST-eind punten):

* ODBC
* JDBC
* Apache Ambari
* Apache Oozie
* Apache Templeton

Deze services worden standaard verleend voor toegang. U kunt de toegang intrekken/verlenen. Intrekken:

```csharp
var httpParams = new HttpSettingsParameters
{
    HttpUserEnabled = false,
    HttpUsername = "admin",
    HttpPassword = "*******",
};
_hdiManagementClient.Clusters.ConfigureHttpSettings("<Resource Group Name>, <Cluster Name>, httpParams);
```

Verlenen:

```csharp
var httpParams = new HttpSettingsParameters
{
    HttpUserEnabled = enable,
    HttpUsername = "admin",
    HttpPassword = "*******",
};
_hdiManagementClient.Clusters.ConfigureHttpSettings("<Resource Group Name>, <Cluster Name>, httpParams);
```

> [!NOTE]  
> Door de toegang te verlenen/in te trekken, stelt u de gebruikers naam en het wacht woord van het cluster opnieuw in.

Dit kan ook worden gedaan via de portal. Zie [Apache Hadoop clusters in HDInsight beheren met behulp van de Azure Portal](hdinsight-administer-use-portal-linux.md).

## <a name="update-http-user-credentials"></a>HTTP-gebruikers referenties bijwerken

Het is dezelfde procedure als HTTP-toegang verlenen/intrekken.  Als de HTTP-toegang aan het cluster is verleend, moet u dit eerst intrekken.  En verleen vervolgens de toegang met nieuwe HTTP-gebruikers referenties.

## <a name="find-the-default-storage-account"></a>Het standaard opslag account zoeken

Het volgende code fragment laat zien hoe u de standaard naam van het opslag account en de standaard sleutel voor het opslag account voor een cluster kunt ophalen.

```csharp
var results = _hdiManagementClient.Clusters.GetClusterConfigurations(<Resource Group Name>, <Cluster Name>, "core-site");
foreach (var key in results.Configuration.Keys)
{
    Console.WriteLine(String.Format("{0} => {1}", key, results.Configuration[key]));
}
```

## <a name="submit-jobs"></a>Taken verzenden

**MapReduce-taken verzenden**

Zie [MapReduce-voor beelden uitvoeren in HDInsight](hadoop/apache-hadoop-run-samples-linux.md).

**Apache Hive-taken verzenden** 

Zie [Apache Hive Query's uitvoeren met behulp van .NET SDK](hadoop/apache-hadoop-use-hive-dotnet-sdk.md).

**Apache Sqoop-taken verzenden**

Zie [Apache Sqoop gebruiken met HDInsight](hadoop/apache-hadoop-use-sqoop-dotnet-sdk.md).

**Apache Oozie-taken verzenden**

Zie [Apache Oozie gebruiken met Hadoop om een werk stroom in HDInsight te definiëren en uit te voeren](hdinsight-use-oozie-linux-mac.md).

## <a name="upload-data-to-azure-blob-storage"></a>Gegevens uploaden naar Azure Blob Storage

Zie [Gegevens uploaden naar HDInsight][hdinsight-upload-data].

## <a name="see-also"></a>Zie ook

* [Referentie documentatie voor HDInsight .NET SDK](/dotnet/api/overview/azure/hdinsight)
* [Apache Hadoop clusters in HDInsight beheren door gebruik te maken van de Azure Portal](hdinsight-administer-use-portal-linux.md)
* [HDInsight beheren met een opdracht regel interface][hdinsight-admin-cli]
* [HDInsight-clusters maken][hdinsight-provision]
* [Gegevens uploaden naar HDInsight][hdinsight-upload-data]
* [Aan de slag met Azure HDInsight][hdinsight-get-started]

[azure-purchase-options]: https://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: https://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: https://azure.microsoft.com/pricing/free-trial/

[hdinsight-get-started]:hadoop/apache-hadoop-linux-tutorial-get-started.md
[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-provision-custom-options]: hdinsight-hadoop-provision-linux-clusters.md#configuration
[hdinsight-submit-jobs]:hadoop/submit-apache-hadoop-jobs-programmatically.md

[hdinsight-admin-cli]: hdinsight-administer-use-command-line.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md
[hdinsight-use-hive]:hadoop/hdinsight-use-hive.md
[hdinsight-use-mapreduce]:hadoop/hdinsight-use-mapreduce.md
[hdinsight-upload-data]: hdinsight-upload-data.md
