---
title: Migreren naar Azure Resource Manager-hulpprogram ma's voor HDInsight
description: Migreren naar Azure Resource Manager ontwikkelingsprogram ma's voor HDInsight-clusters
ms.service: hdinsight
ms.custom: hdinsightactive, devx-track-azurecli
ms.topic: how-to
ms.date: 02/21/2018
ms.openlocfilehash: a8f808cd43f96f26db0de28e8059d02d9488320a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102434658"
---
# <a name="migrating-to-azure-resource-manager-based-development-tools-for-hdinsight-clusters"></a>Migreren naar op Azure Resource Manager gebaseerde ontwikkel hulpprogramma's voor HDInsight-clusters

HDInsight reafschaffing van Azure Service Manager (ASM)-hulpprogram ma's voor HDInsight. Als u Azure PowerShell, klassieke CLI van Azure of de HDInsight .NET SDK gebruikt om te werken met HDInsight-clusters, wordt u aangeraden de Azure Resource Manager versies van Power shell, CLI en .NET SDK vooruit te gebruiken. In dit artikel vindt u verwijzingen naar de nieuwe methode op basis van Resource Manager. In dit document worden alle verschillen tussen de ASM-en Resource Manager-benaderingen voor HDInsight gemarkeerd.

> [!IMPORTANT]  
> De ondersteuning voor op ASM gebaseerde Power shell, CLI en .NET SDK wordt beëindigd op **1 januari 2017**.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="migrating-azure-classic-cli-to-azure-resource-manager"></a>Klassieke Azure-CLI migreren naar Azure Resource Manager

> [!IMPORTANT]  
> Azure CLI biedt geen ondersteuning voor het werken met HDInsight-clusters. U kunt nog steeds Azure Classic CLI met HDInsight gebruiken, maar de klassieke Azure-CLI is afgeschaft.

Hieronder vindt u de basis opdrachten voor het werken met HDInsight via de klassieke Azure-CLI:

* `azure hdinsight cluster create` -maakt een nieuw HDInsight-cluster
* `azure hdinsight cluster delete` -een bestaand HDInsight-cluster verwijderen
* `azure hdinsight cluster show` -informatie over een bestaand cluster weer geven
* `azure hdinsight cluster list` -HDInsight-clusters voor uw Azure-abonnement weer geven

Gebruik de `-h` Schakel optie om de para meters en switches te controleren die beschikbaar zijn voor elke opdracht.

### <a name="new-commands"></a>Nieuwe opdrachten
Er zijn nieuwe opdrachten beschikbaar met Azure Resource Manager:

* `azure hdinsight cluster resize` -Hiermee wordt het aantal worker-knoop punten in het cluster dynamisch gewijzigd
* `azure hdinsight cluster enable-http-access` -Hiermee schakelt u HTTPs-toegang tot het cluster in (standaard ingeschakeld)
* `azure hdinsight cluster disable-http-access` -de HTTPs-toegang tot het cluster wordt uitgeschakeld
* `azure hdinsight script-action` -bevat opdrachten voor het maken/beheren van script acties op een cluster
* `azure hdinsight config` -bevat opdrachten voor het maken van een configuratie bestand dat kan worden gebruikt met de `hdinsight cluster create` opdracht om configuratie gegevens op te geven.

### <a name="deprecated-commands"></a>Afgeschafte opdrachten
Als u de `azure hdinsight job` opdrachten gebruikt om taken te verzenden naar uw HDInsight-cluster, zijn deze opdrachten niet beschikbaar via de Resource Manager-opdrachten. Als u vanuit scripts programmatisch taken wilt verzenden naar HDInsight, moet u in plaats daarvan de REST Api's gebruiken die door HDInsight worden verstrekt. Raadpleeg de volgende documenten voor meer informatie over het verzenden van taken met behulp van REST-Api's.

* [MapReduce-taken uitvoeren met Hadoop op HDInsight met behulp van krul](hadoop/apache-hadoop-use-mapreduce-curl.md)
* [Apache Hive query's uitvoeren met Apache Hadoop op HDInsight met behulp van krul](hadoop/apache-hadoop-use-hive-curl.md)


Zie [MapReduce gebruiken met Hadoop in hdinsight](hadoop/hdinsight-use-mapreduce.md)voor informatie over andere manieren om Apache Hadoop MapReduce, Apache Hive en Apache varken interactief uit te voeren, [gebruik Apache Hive met de Apache Hadoop in](hadoop/hdinsight-use-hive.md)Hdinsight en [gebruik Apache Pig met Apache Hadoop op hdinsight](./index.yml).

### <a name="examples"></a>Voorbeelden
**Een cluster maken**

* Oude opdracht (ASM)- `azure hdinsight cluster create myhdicluster --location northeurope --osType linux --storageAccountName mystorage --storageAccountKey <storagekey> --storageContainer mycontainer --userName admin --password mypassword --sshUserName sshuser --sshPassword mypassword`
* Nieuwe opdracht- `azure hdinsight cluster create myhdicluster -g myresourcegroup --location northeurope --osType linux --clusterType hadoop --defaultStorageAccountName mystorage --defaultStorageAccountKey <storagekey> --defaultStorageContainer mycontainer --userName admin -password mypassword --sshUserName sshuser --sshPassword mypassword`

**Een cluster verwijderen**

* Oude opdracht (ASM)- `azure hdinsight cluster delete myhdicluster`
* Nieuwe opdracht- `azure hdinsight cluster delete mycluster -g myresourcegroup`

**Clusters weergeven**

* Oude opdracht (ASM)- `azure hdinsight cluster list`
* Nieuwe opdracht- `azure hdinsight cluster list`

> [!NOTE]  
> Als u voor de lijst opdracht de resource groep opgeeft, `-g` worden alleen de clusters in de opgegeven resource groep geretourneerd.

**Cluster gegevens weer geven**

* Oude opdracht (ASM)- `azure hdinsight cluster show myhdicluster`
* Nieuwe opdracht- `azure hdinsight cluster show myhdicluster -g myresourcegroup`

## <a name="migrating-azure-powershell-to-azure-resource-manager"></a>Azure PowerShell migreren naar Azure Resource Manager
De algemene informatie over Azure PowerShell in de Azure Resource Manager modus vindt [u in azure PowerShell gebruiken met Azure Resource Manager](../azure-resource-manager/management/manage-resources-powershell.md).

De Azure PowerShell Resource Manager-cmdlets kunnen naast de ASM-cmdlets worden geïnstalleerd. De cmdlets uit de twee modi kunnen worden onderscheiden met hun namen.  De Resource Manager-modus heeft *AzHDInsight* in de namen van de cmdlets vergeleken met *AzureHDInsight* in de oudere Azure Service Management-modus.  Bijvoorbeeld: *New-AzHDInsightCluster* versus *New-AzureHDInsightCluster*. Para meters en Schakel opties hebben mogelijk nieuws namen en er zijn veel nieuwe para meters beschikbaar wanneer u Resource Manager gebruikt.  Verschillende cmdlets vereisen bijvoorbeeld een nieuwe switch met de naam *-ResourceGroupName*.

Voordat u de HDInsight-cmdlets kunt gebruiken, moet u verbinding maken met uw Azure-account en een nieuwe resource groep maken:

* [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount)
* [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup)

### <a name="renamed-cmdlets"></a>De naam van cmdlets is gewijzigd
De HDInsight ASM-cmdlets in de Windows Power shell-console weer geven:

```powershell
help *azurehdinsight*
```

De volgende tabel bevat de ASM-cmdlets en hun namen in de Resource Manager-modus:

| ASM-cmdlets | Resource Manager-cmdlets |
| --- | --- |
| Add-AzureHDInsightConfigValue |[Add-AzHDInsightConfigValue](/powershell/module/az.hdinsight/add-azhdinsightconfigvalue) |
| Add-AzureHDInsightMetastore |[Add-AzHDInsightMetastore](/powershell/module/az.hdinsight/add-azhdinsightmetastore) |
| Add-AzureHDInsightScriptAction |[Add-AzHDInsightScriptAction](/powershell/module/az.hdinsight/add-azhdinsightscriptaction) |
| Add-AzureHDInsightStorage |[Add-AzHDInsightStorage](/powershell/module/az.hdinsight/add-azhdinsightstorage) |
| Get-AzureHDInsightCluster |[Get-AzHDInsightCluster](/powershell/module/az.hdinsight/get-azhdinsightcluster) |
| Get-AzureHDInsightJob |[Get-AzHDInsightJob](/powershell/module/az.hdinsight/get-azhdinsightjob) |
| Get-AzureHDInsightJobOutput |[Get-AzHDInsightJobOutput](/powershell/module/az.hdinsight/get-azhdinsightjoboutput) |
| Get-AzureHDInsightProperty |[Get-AzHDInsightProperty](/powershell/module/az.hdinsight/get-azhdinsightproperty) |
| Grant-AzureHDInsightHttpServicesAccess |[Grant-AzureRmHDInsightHttpServicesAccess](/powershell/module/azurerm.hdinsight/grant-azurermhdinsighthttpservicesaccess) |
| Grant-AzureHdinsightRdpAccess |[Grant-AzHDInsightRdpServicesAccess](/powershell/module/az.hdinsight/grant-azhdinsightrdpservicesaccess) |
| Invoke-AzureHDInsightHiveJob |[Invoke-AzHDInsightHiveJob](/powershell/module/az.hdinsight/invoke-azhdinsighthivejob) |
| New-AzureHDInsightCluster |[New-AzHDInsightCluster](/powershell/module/az.hdinsight/new-azhdinsightcluster) |
| New-AzureHDInsightClusterConfig |[New-AzHDInsightClusterConfig](/powershell/module/az.hdinsight/new-azhdinsightclusterconfig) |
| New-AzureHDInsightHiveJobDefinition |[New-AzHDInsightHiveJobDefinition](/powershell/module/az.hdinsight/new-azhdinsighthivejobdefinition) |
| New-AzureHDInsightMapReduceJobDefinition |[New-AzHDInsightMapReduceJobDefinition](/powershell/module/az.hdinsight/new-azhdinsightmapreducejobdefinition) |
| New-AzureHDInsightPigJobDefinition |[New-AzHDInsightPigJobDefinition](/powershell/module/az.hdinsight/new-azhdinsightpigjobdefinition) |
| New-AzureHDInsightSqoopJobDefinition |[New-AzHDInsightSqoopJobDefinition](/powershell/module/az.hdinsight/new-azhdinsightsqoopjobdefinition) |
| New-AzureHDInsightStreamingMapReduceJobDefinition |[New-AzHDInsightStreamingMapReduceJobDefinition](/powershell/module/az.hdinsight/new-azhdinsightstreamingmapreducejobdefinition) |
| Remove-AzureHDInsightCluster |[Remove-AzHDInsightCluster](/powershell/module/az.hdinsight/remove-azhdinsightcluster) |
| Revoke-AzureHDInsightHttpServicesAccess |[Revoke-AzHDInsightHttpServicesAccess](/powershell/module/azurerm.hdinsight/revoke-azurermhdinsighthttpservicesaccess) |
| Revoke-AzureHdinsightRdpAccess |[Revoke-AzHDInsightRdpServicesAccess](/powershell/module/az.hdinsight/revoke-azhdinsightrdpservicesaccess) |
| Set-AzureHDInsightClusterSize |[Set-AzHDInsightClusterSize](/powershell/module/az.hdinsight/set-azhdinsightclustersize) |
| Set-AzureHDInsightDefaultStorage |[Set-AzHDInsightDefaultStorage](/powershell/module/az.hdinsight/set-azhdinsightdefaultstorage) |
| Start-AzureHDInsightJob |[Start-AzHDInsightJob](/powershell/module/az.hdinsight/start-azhdinsightjob) |
| Stop-AzureHDInsightJob |[Stoppen-AzHDInsightJob](/powershell/module/az.hdinsight/stop-azhdinsightjob) |
| Use-AzureHDInsightCluster |[Use-AzHDInsightCluster](/powershell/module/az.hdinsight/use-azhdinsightcluster) |
| Wait-AzureHDInsightJob |[Wait-AzHDInsightJob](/powershell/module/az.hdinsight/wait-azhdinsightjob) |

### <a name="new-cmdlets"></a>Nieuwe cmdLets
Hieronder vindt u de nieuwe cmdlets die alleen beschikbaar zijn in de Resource Manager-modus. 

**Script actie-gerelateerde cmdlets:**

* **Get-AzHDInsightPersistedScriptAction**: Hiermee worden de persistente script acties voor een cluster opgehaald en in chronologische volg orde vermeld of worden details opgehaald voor een opgegeven persistente script actie. 
* **Get-AzHDInsightScriptActionHistory**: Hiermee wordt de geschiedenis van de script actie voor een cluster opgehaald en in omgekeerde chronologische volg orde weer gegeven, of worden details van een eerder uitgevoerde script actie opgehaald. 
* **Remove-AzHDInsightPersistedScriptAction**: Hiermee wordt een persistente script actie uit een HDInsight-cluster verwijderd.
* **Set-AzHDInsightPersistedScriptAction**: stelt een eerder uitgevoerde script actie in op een persistente script actie.
* **Submit-AzHDInsightScriptAction**: Hiermee wordt een nieuwe script actie verzonden naar een Azure HDInsight-cluster. 

Zie [HDInsight-clusters op basis van Linux aanpassen met behulp van script acties](hdinsight-hadoop-customize-cluster-linux.md)voor meer informatie over het gebruik van.

**Met de cluster-id gerelateerde cmdlets:**

* **Add-AzHDInsightClusterIdentity**: voegt een cluster identiteit toe aan een cluster configuratie object, zodat het HDInsight-cluster toegang kan krijgen tot Azure data Lake Storage. Zie [een HDInsight-cluster met data Lake Storage maken met behulp van Azure PowerShell](../data-lake-store/data-lake-store-hdinsight-hadoop-use-powershell.md).

### <a name="examples"></a>Voorbeelden
**Cluster maken**

Oude opdracht (ASM): 

```azurepowershell
New-AzureHDInsightCluster `
    -Name $clusterName `
    -Location $location `
    -DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" `
    -DefaultStorageAccountKey $storageAccountKey `
    -DefaultStorageContainerName $containerName `
    -ClusterSizeInNodes 2 `
    -ClusterType Hadoop `
    -OSType Linux `
    -Version "3.2" `
    -Credential $httpCredential `
    -SshCredential $sshCredential
```

Nieuwe opdracht:

```azurepowershell
New-AzHDInsightCluster `
    -ClusterName $clusterName `
    -ResourceGroupName $resourceGroupName `
    -Location $location `
    -DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" `
    -DefaultStorageAccountKey $storageAccountKey `
    -DefaultStorageContainer $containerName  `
    -ClusterSizeInNodes 2 `
    -ClusterType Hadoop `
    -OSType Linux `
    -Version "3.2" `
    -HttpCredential $httpcredentials `
    -SshCredential $sshCredentials
```

**Cluster verwijderen**

Oude opdracht (ASM):

```azurepowershell
Remove-AzureHDInsightCluster -name $clusterName 
```

Nieuwe opdracht:

```azurepowershell
Remove-AzHDInsightCluster -ResourceGroupName $resourceGroupName -ClusterName $clusterName
```

**Cluster weer geven**

Oude opdracht (ASM):

```azurepowershell
Get-AzureHDInsightCluster
```

Nieuwe opdracht:

```azurepowershell
Get-AzHDInsightCluster
```

**Cluster weer geven**

Oude opdracht (ASM):

```azurepowershell
Get-AzureHDInsightCluster -Name $clusterName
```

Nieuwe opdracht:

```azurepowershell
Get-AzHDInsightCluster -ResourceGroupName $resourceGroupName -clusterName $clusterName
```

#### <a name="other-samples"></a>Andere voor beelden
* [HDInsight-clusters maken](hdinsight-hadoop-create-linux-clusters-azure-powershell.md)
* [Apache Hive-taken verzenden](hadoop/apache-hadoop-use-hive-powershell.md)
* [Apache Sqoop-taken verzenden](hadoop/apache-hadoop-use-sqoop-powershell.md)

## <a name="migrating-to-the-new-hdinsight-net-sdk"></a>Migreren naar de nieuwe HDInsight .NET SDK
De op Azure Service Management gebaseerde [(ASM) HDInsight .NET SDK](/previous-versions/azure/reference/mt416619(v=azure.100)) is nu afgeschaft. U wordt aangeraden de op [Resource Manager gebaseerde HDInsight .NET SDK](/dotnet/api/overview/azure/hdinsight)op basis van Azure resource management te gebruiken. De volgende HDInsight-pakketten op basis van ASM worden afgeschaft.

* `Microsoft.WindowsAzure.Management.HDInsight`
* `Microsoft.Hadoop.Client`

In deze sectie vindt u meer informatie over het uitvoeren van bepaalde taken met de SDK op basis van Resource Manager.

| Procedures... de op Resource Manager gebaseerde HDInsight SDK gebruiken | Koppelingen |
| --- | --- |
| Azure HDInsight-SDK voor .NET|Zie de [Azure HDInsight-SDK voor .net](/dotnet/api/overview/azure/hdinsight) |
| Toepassingen interactief verifiëren met behulp van Azure Active Directory met .NET SDK |Zie [Apache Hive Query's uitvoeren met behulp van .NET SDK](hadoop/apache-hadoop-use-hive-dotnet-sdk.md). In het code fragment in dit artikel wordt gebruikgemaakt van de interactieve verificatie methode. |
| Toepassingen niet-interactief verifiëren met behulp van Azure Active Directory met .NET SDK |Zie [niet-interactieve toepassingen voor HDInsight maken](hdinsight-create-non-interactive-authentication-dotnet-applications.md) |
| Een Apache Hive-taak verzenden met behulp van .NET SDK |Zie [Apache Hive taken verzenden](hadoop/apache-hadoop-use-hive-dotnet-sdk.md) |
| Een Apache Sqoop-taak verzenden met behulp van .NET SDK |Zie [Apache Sqoop-taken verzenden](hadoop/apache-hadoop-use-sqoop-dotnet-sdk.md) |
| HDInsight-clusters vermelden met behulp van .NET SDK |Zie [HDInsight-clusters](hdinsight-administer-use-dotnet-sdk.md#list-clusters) weer geven |
| HDInsight-clusters schalen met behulp van .NET SDK |Zie [HDInsight-clusters schalen](hdinsight-administer-use-dotnet-sdk.md#scale-clusters) |
| Toegang verlenen/intrekken voor HDInsight-clusters met behulp van .NET SDK |Zie [toegang verlenen/intrekken voor HDInsight-clusters](hdinsight-administer-use-dotnet-sdk.md#grantrevoke-access) |
| HTTP-gebruikers referenties voor HDInsight-clusters bijwerken met behulp van .NET SDK |Zie [HTTP-gebruikers referenties voor HDInsight-clusters bijwerken](hdinsight-administer-use-dotnet-sdk.md#update-http-user-credentials) |
| Het standaard opslag account voor HDInsight-clusters zoeken met behulp van .NET SDK |Zie [het standaard opslag account voor HDInsight-clusters zoeken](hdinsight-administer-use-dotnet-sdk.md#find-the-default-storage-account) |
| HDInsight-clusters verwijderen met behulp van .NET SDK |Zie [HDInsight-clusters verwijderen](hdinsight-administer-use-dotnet-sdk.md#delete-clusters) |

### <a name="examples"></a>Voorbeelden
Hieronder volgen enkele voor beelden van de manier waarop een bewerking wordt uitgevoerd met behulp van de op ASM gebaseerde SDK en het equivalente code fragment voor de op Resource Manager gebaseerde SDK.

**Een ruwe cluster-client maken**

* Oude opdracht (ASM)

  ```azurecli
  //Certificate auth
  //This logs the application in using a subscription administration certificate, which is not offered in Azure Resource Manager
  
  const string subid = "454467d4-60ca-4dfd-a556-216eeeeeeee1";
  var cred = new HDInsightCertificateCredential(new Guid(subid), new X509Certificate2(@"path\to\certificate.cer"));
  var client = HDInsightClient.Connect(cred);
  ```
* Nieuwe opdracht (Service Principal Authorization)

  ```azurecli
  //Service principal auth
  //This will log the application in as itself, rather than on behalf of a specific user.
  //For details, including how to set up the application, see:
  //   https://azure.microsoft.com/documentation/articles/hdinsight-create-non-interactive-authentication-dotnet-applications/
  
  var authFactory = new AuthenticationFactory();
  
  var account = new AzureAccount { Type = AzureAccount.AccountType.ServicePrincipal, Id = clientId };
  
  var env = AzureEnvironment.PublicEnvironments[EnvironmentName.AzureCloud];
  
  var accessToken = authFactory.Authenticate(account, env, tenantId, secretKey, ShowDialog.Never).AccessToken;
  
  var creds = new TokenCloudCredentials(subId.ToString(), accessToken);
  
  _hdiManagementClient = new HDInsightManagementClient(creds);
  ```

* Nieuwe opdracht (gebruikers autorisatie)

  ```azurecli
  //User auth
  //This will log the application in on behalf of the user.
  //The end-user will see a login popup.
  
  var authFactory = new AuthenticationFactory();
  
  var account = new AzureAccount { Type = AzureAccount.AccountType.User, Id = username };
  
  var env = AzureEnvironment.PublicEnvironments[EnvironmentName.AzureCloud];
  
  var accessToken = authFactory.Authenticate(account, env, AuthenticationFactory.CommonAdTenant, password, ShowDialog.Auto).AccessToken;
  
  var creds = new TokenCloudCredentials(subId.ToString(), accessToken);
  
  _hdiManagementClient = new HDInsightManagementClient(creds);
  ```

**Een cluster maken**

* Oude opdracht (ASM)

  ```azurecli
  var clusterInfo = new ClusterCreateParameters
              {
                  Name = dnsName,
                  DefaultStorageAccountKey = key,
                  DefaultStorageContainer = defaultStorageContainer,
                  DefaultStorageAccountName = storageAccountDnsName,
                  ClusterSizeInNodes = 1,
                  ClusterType = type,
                  Location = "West US",
                  UserName = "admin",
                  Password = "*******",
                  Version = version,
                  HeadNodeSize = NodeVMSize.Large,
              };
  clusterInfo.CoreConfiguration.Add(new KeyValuePair<string, string>("config1", "value1"));
  client.CreateCluster(clusterInfo);
  ```

* Nieuwe opdracht

  ```azurecli
  var clusterCreateParameters = new ClusterCreateParameters
      {
          Location = "West US",
          ClusterType = "Hadoop",
          Version = "3.1",
          OSType = OSType.Linux,
          DefaultStorageAccountName = "mystorage.blob.core.windows.net",
          DefaultStorageAccountKey =
              "O9EQvp3A3AjXq/W27rst1GQfLllhp0gUeiUUn2D8zX2lU3taiXSSfqkZlcPv+nQcYUxYw==",
          UserName = "hadoopuser",
          Password = "*******",
          HeadNodeSize = "ExtraLarge",
          RdpUsername = "hdirp",
          RdpPassword = ""*******",
          RdpAccessExpiry = new DateTime(2025, 3, 1),
          ClusterSizeInNodes = 5
      };
  var coreConfigs = new Dictionary<string, string> {{"config1", "value1"}};
  clusterCreateParameters.Configurations.Add(ConfigurationKey.CoreSite, coreConfigs);
  ```

**HTTP-toegang inschakelen**

* Oude opdracht (ASM)

  ```azurecli
  client.EnableHttp(dnsName, "West US", "admin", "*******");
  ```

* Nieuwe opdracht
  
  ```azurecli
  var httpParams = new HttpSettingsParameters
  {
         HttpUserEnabled = true,
         HttpUsername = "admin",
         HttpPassword = "*******",
  };
  client.Clusters.ConfigureHttpSettings(resourceGroup, dnsname, httpParams);
  ```

**Een cluster verwijderen**

* Oude opdracht (ASM)

  ```azurecli
  client.DeleteCluster(dnsName);
  ```

* Nieuwe opdracht

  ```azurecli
  client.Clusters.Delete(resourceGroup, dnsname);
  ```
