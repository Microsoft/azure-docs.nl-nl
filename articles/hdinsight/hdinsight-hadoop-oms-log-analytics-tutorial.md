---
title: Logboeken Azure Monitor voor het bewaken van Azure HDInsight clusters
description: Meer informatie over het gebruik Azure Monitor logboeken voor het bewaken van taken die worden uitgevoerd in een HDInsight-cluster.
ms.service: hdinsight
ms.topic: how-to
ms.custom: seoapr2020, devx-track-azurecli, devx-track-azurepowershell
ms.date: 05/13/2020
ms.openlocfilehash: 2a81b25b08708a878fc8ff83cf19c643036b8f90
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107770323"
---
# <a name="use-azure-monitor-logs-to-monitor-hdinsight-clusters"></a>Azure Monitor-logboeken gebruiken om HDInsight-clusters te bewaken

Meer informatie over het inschakelen Azure Monitor voor het bewaken van Hadoop-clusterbewerkingen in HDInsight. En hoe u een HDInsight-bewakingsoplossing toevoegt.

[Azure Monitor logboeken](../azure-monitor/logs/log-query-overview.md) is een Azure Monitor service die uw cloud- en on-premises omgevingen bewaakt. De bewaking is om de beschikbaarheid en prestaties te behouden. Het verzamelt gegevens die worden gegenereerd door resources in uw cloud, on-premises omgevingen en andere bewakingshulpprogramma's. De gegevens worden gebruikt voor analyse in meerdere bronnen.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

Als u geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

* Een Log Analytics-werkruimte. U kunt deze werkruimte zien als een unieke Azure Monitor logboekomgeving met een eigen gegevensopslagplaats, gegevensbronnen en oplossingen. Zie Een [Log Analytics-werkruimte maken voor de instructies.](../azure-monitor/vm/quick-collect-azurevm.md#create-a-workspace)

* Een Azure HDInsight-cluster. Op dit moment kunt u Azure Monitor met de volgende HDInsight-clustertypen:

  * Hadoop
  * HBase
  * Interactive Query
  * Kafka
  * Spark
  * Storm

  Zie Aan de slag met Azure HDInsight voor instructies over het maken van [een HDInsight-cluster.](hadoop/apache-hadoop-linux-tutorial-get-started.md)  

* Als u PowerShell gebruikt, hebt u de [Az-module nodig.](/powershell/azure/) Zorg ervoor dat u de nieuwste versie hebt. Voer indien nodig `Update-Module -Name Az` uit.

* Zie Azure CLI installeren als u Azure CLI wilt gebruiken en u deze nog niet [hebt geïnstalleerd.](/cli/azure/install-azure-cli)

> [!NOTE]  
> Het is raadzaam om zowel het HDInsight-cluster als de Log Analytics-werkruimte in dezelfde regio te plaatsen voor betere prestaties. Azure Monitor logboeken zijn niet beschikbaar in alle Azure-regio's.

## <a name="enable-azure-monitor-using-the-portal"></a>Toegang Azure Monitor via de portal

In deze sectie configureert u een bestaand HDInsight Hadoop-cluster om een Azure Log Analytics-werkruimte te gebruiken voor het bewaken van taken, logboeken voor foutopsporing, en meer.

1. Selecteer in [Azure Portal](https://portal.azure.com/)het cluster. Het cluster wordt geopend op een nieuwe portalpagina.

1. Selecteer aan de linkerkant **onder Bewaking** de **optie Azure Monitor.**

1. Selecteer in de hoofdweergave **onder Azure Monitor Integratie** de optie **Inschakelen.**

1. Selecteer in **de vervolgkeuzelijst** Een werkruimte selecteren een bestaande Log Analytics-werkruimte.

1. Selecteer **Opslaan**.  Het duurt even om de instelling op te slaan.

    :::image type="content" source="./media/hdinsight-hadoop-oms-log-analytics-tutorial/azure-portal-monitoring.png" alt-text="Bewaking inschakelen voor HDInsight-clusters":::

## <a name="enable-azure-monitor-using-azure-powershell"></a>Schakel Azure Monitor in met Azure PowerShell

U kunt logboeken Azure Monitor met behulp van Azure PowerShell cmdlet [Enable-AzHDInsightMonitoring](/powershell/module/az.hdinsight/enable-azhdinsightmonitoring) van de Az-module.

```powershell
# Enter user information
$resourceGroup = "<your-resource-group>"
$cluster = "<your-cluster>"
$LAW = "<your-Log-Analytics-workspace>"
# End of user input

# obtain workspace id for defined Log Analytics workspace
$WorkspaceId = (Get-AzOperationalInsightsWorkspace `
                    -ResourceGroupName $resourceGroup `
                    -Name $LAW).CustomerId

# obtain primary key for defined Log Analytics workspace
$PrimaryKey = (Get-AzOperationalInsightsWorkspace `
                    -ResourceGroupName $resourceGroup `
                    -Name $LAW | Get-AzOperationalInsightsWorkspaceSharedKeys).PrimarySharedKey

# Enables monitoring and relevant logs will be sent to the specified workspace.
Enable-AzHDInsightMonitoring `
    -ResourceGroupName $resourceGroup `
    -Name $cluster `
    -WorkspaceId $WorkspaceId `
    -PrimaryKey $PrimaryKey

# Gets the status of monitoring installation on the cluster.
Get-AzHDInsightMonitoring `
    -ResourceGroupName $resourceGroup `
    -Name $cluster
```

Als u wilt uitschakelen, gebruikt u de cmdlet [Disable-AzHDInsightMonitoring:](/powershell/module/az.hdinsight/disable-azhdinsightmonitoring)

```powershell
Disable-AzHDInsightMonitoring -Name "<your-cluster>"
```

## <a name="enable-azure-monitor-using-azure-cli"></a>Een Azure Monitor azure CLI inschakelen

U kunt logboeken Azure Monitor met behulp van de opdracht Azure CLI `[az hdinsight monitor enable` ](/cli/azure/hdinsight/monitor#az_hdinsight_monitor_enable).

```azurecli
# set variables
export resourceGroup=RESOURCEGROUPNAME
export cluster=CLUSTERNAME
export LAW=LOGANALYTICSWORKSPACENAME

# Enable the Azure Monitor logs integration on an HDInsight cluster.
az hdinsight monitor enable --name $cluster --resource-group $resourceGroup --workspace $LAW

# Get the status of Azure Monitor logs integration on an HDInsight cluster.
az hdinsight monitor show --name $cluster --resource-group $resourceGroup
```

Als u wilt uitschakelen, gebruikt u de [`az hdinsight monitor disable`](/cli/azure/hdinsight/monitor#az_hdinsight_monitor_disable) opdracht .

```azurecli
az hdinsight monitor disable --name $cluster --resource-group $resourceGroup
```

## <a name="install-hdinsight-cluster-management-solutions"></a>HDInsight-clusterbeheeroplossingen installeren

HDInsight biedt clusterspecifieke beheeroplossingen die u kunt toevoegen voor Azure Monitor logboeken. [Beheeroplossingen voegen](../azure-monitor/insights/solutions.md) functionaliteit toe aan Azure Monitor logboeken en bieden extra hulpprogramma's voor gegevens en analyse. Deze oplossingen verzamelen belangrijke metrische prestatiegegevens van uw HDInsight-clusters. En geef de hulpprogramma's op om de metrische gegevens te doorzoeken. Deze oplossingen bieden ook visualisaties en dashboards voor de meeste clustertypen die worden ondersteund in HDInsight. Met behulp van de metrische gegevens die u met de oplossing verzamelt, kunt u aangepaste bewakingsregels en waarschuwingen maken.

Beschikbare HDInsight-oplossingen:

* HDInsight Hadoop-bewaking
* HDInsight HBase-bewaking
* HdInsight-Interactive Query bewaking
* HdInsight Kafka-bewaking
* HDInsight Spark-bewaking
* HDInsight Storm Monitoring

Zie Beheeroplossingen in Azure voor instructies voor [beheeroplossingen.](../azure-monitor/insights/solutions.md#install-a-monitoring-solution) Als u wilt experimenteren, installeert u een HDInsight Hadoop-bewakingsoplossing. Wanneer dit is gebeurd, ziet u een **HDInsightHadoop-tegel** onder **Samenvatting**. Selecteer de **tegel HDInsightHadoop.** De HDInsightHadoop-oplossing ziet er als volgende uit:

:::image type="content" source="media/hdinsight-hadoop-oms-log-analytics-tutorial/hdinsight-oms-hdinsight-hadoop-monitoring-solution.png" alt-text="Oplossingsweergave voor HDInsight-bewaking":::

Omdat het cluster een geheel nieuw cluster is, worden in het rapport geen activiteiten weer geven.

## <a name="configuring-performance-counters"></a>Prestatiemeters configureren

Azure Monitor ondersteunt het verzamelen en analyseren van metrische prestatiegegevens voor de knooppunten in uw cluster. Zie Gegevensbronnen voor [Linux-prestaties in](../azure-monitor/agents/data-sources-performance-counters.md#linux-performance-counters)Azure Monitor.

## <a name="cluster-auditing"></a>Clustercontrole

HDInsight biedt ondersteuning voor clustercontrole Azure Monitor logboeken, door de volgende typen logboeken te importeren:

* `log_gateway_audit_CL` - Deze tabel bevat auditlogboeken van clustergatewayknooppunten die geslaagde en mislukte aanmeldingspogingen tonen.
* `log_auth_CL` : deze tabel bevat SSH-logboeken met geslaagde en mislukte aanmeldingspogingen.
* `log_ambari_audit_CL` : deze tabel bevat auditlogboeken van Ambari.
* `log_ranger_audti_CL` : deze tabel bevat auditlogboeken van Apache Ranger op ESP-clusters.

## <a name="next-steps"></a>Volgende stappen

* [Query'Azure Monitor voor het bewaken van HDInsight-clusters](hdinsight-hadoop-oms-log-analytics-use-queries.md)
* [De beschikbaarheid van clusters bewaken met Apache Ambari- en Azure Monitor logboeken](./hdinsight-cluster-availability.md)