---
title: Meer Azure Storage accounts toevoegen aan HDInsight
description: Meer informatie over het toevoegen van extra Azure Storage accounts aan een bestaand HDInsight-cluster.
ms.service: hdinsight
ms.topic: how-to
ms.custom: seoapr2020
ms.date: 04/27/2020
ms.openlocfilehash: 32757fef131c5e443350f032c0ac987d7f491396
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/23/2021
ms.locfileid: "104864309"
---
# <a name="add-additional-storage-accounts-to-hdinsight"></a>Extra opslag accounts toevoegen aan HDInsight

Meer informatie over het gebruik van script acties om extra Azure Storage *accounts* toe te voegen aan HDInsight. Met de stappen in dit document voegt u een opslag *account* toe aan een bestaand HDInsight-cluster. Dit artikel is van toepassing op opslag *accounts* (niet op het standaard cluster-opslag account) en niet op extra opslag, zoals [`Azure Data Lake Storage Gen1`](hdinsight-hadoop-use-data-lake-storage-gen1.md) en [`Azure Data Lake Storage Gen2`](hdinsight-hadoop-use-data-lake-storage-gen2.md) .

> [!IMPORTANT]  
> De informatie in dit document is over het toevoegen van extra opslag accounts aan een cluster nadat het is gemaakt. Voor informatie over het toevoegen van opslag accounts tijdens het maken van een cluster, raadpleegt [u clusters in HDInsight instellen met Apache Hadoop, Apache Spark, Apache Kafka en meer](hdinsight-hadoop-provision-linux-clusters.md).

## <a name="prerequisites"></a>Vereisten

* Een Hadoop-cluster in HDInsight. Zie aan de [slag met HDInsight op Linux](./hadoop/apache-hadoop-linux-tutorial-get-started.md).
* Naam en sleutel van het opslag account. Zie [toegangs sleutels voor opslag accounts beheren](../storage/common/storage-account-keys-manage.md).
* Als u Power shell gebruikt, hebt u de AZ-module nodig.  Zie [overzicht van Azure PowerShell](/powershell/azure/).

## <a name="how-it-works"></a>Uitleg

Tijdens de verwerking voert het script de volgende acties uit:

* Als het opslag account al aanwezig is in de core-site.xml configuratie voor het cluster, wordt het script afgesloten en worden er geen verdere acties uitgevoerd.

* Verifieert of het opslag account bestaat en toegankelijk is via de sleutel.

* Hiermee versleutelt u de sleutel met de cluster referentie.

* Voegt het opslag account toe aan het core-site.xml-bestand.

* Stopt en start de Apache Oozie, Apache Hadoop GARENs, Apache Hadoop MapReduce2 en Apache Hadoop HDFS-services opnieuw. Als u deze services stopt en start, kunnen ze het nieuwe opslag account gebruiken.

> [!WARNING]  
> Het gebruik van een opslag account op een andere locatie dan het HDInsight-cluster wordt niet ondersteund.

## <a name="add-storage-account"></a>Opslagaccount toevoegen

Gebruik [script actie](hdinsight-hadoop-customize-cluster-linux.md#script-action-to-a-running-cluster) om de wijzigingen toe te passen met de volgende overwegingen:

|Eigenschap | Waarde |
|---|---|
|Bash-script-URI|`https://hdiconfigactions.blob.core.windows.net/linuxaddstorageaccountv01/add-storage-account-v01.sh`|
|Knooppunttype(n)|Head|
|Parameters|`ACCOUNTNAME``ACCOUNTKEY` `-p` (optioneel)|

* `ACCOUNTNAME` is de naam van het opslag account dat aan het HDInsight-cluster moet worden toegevoegd.
* `ACCOUNTKEY` is de toegangs sleutel voor `ACCOUNTNAME` .
* `-p` is optioneel. Als deze is opgegeven, wordt de sleutel niet versleuteld en wordt deze in het core-site.xml bestand opgeslagen als tekst zonder opmaak.

## <a name="verification"></a>Verificatie

Bij het weer geven van het HDInsight-cluster in de Azure Portal, het selecteren van de vermelding __opslag accounts__ onder __Eigenschappen__ , worden geen opslag accounts weer gegeven die via deze script actie zijn toegevoegd. Het extra opslag account wordt niet weer gegeven in Azure PowerShell en Azure CLI. De opslag informatie wordt niet weer gegeven omdat het script alleen de `core-site.xml` configuratie van het cluster wijzigt. Deze informatie wordt niet gebruikt bij het ophalen van de cluster gegevens met behulp van Azure-beheer-Api's.

Gebruik een van de volgende methoden om de extra opslag ruimte te controleren:

### <a name="powershell"></a>PowerShell

Het script retourneert de namen van de opslag accounts die zijn gekoppeld aan het opgegeven cluster. Vervang door `CLUSTERNAME` de werkelijke naam van het cluster en voer het script uit.

```powershell
# Update values
$clusterName = "CLUSTERNAME"

$creds = Get-Credential -UserName "admin" -Message "Enter the cluster login credentials"

$clusterName = $clusterName.ToLower();

# getting service_config_version
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName`?fields=Clusters/desired_service_config_versions/HDFS" `
    -Credential $creds -UseBasicParsing
$respObj = ConvertFrom-Json $resp.Content

$configVersion=$respObj.Clusters.desired_service_config_versions.HDFS.service_config_version

$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=$configVersion" `
    -Credential $creds
$respObj = ConvertFrom-Json $resp.Content

# extract account names
$value = ($respObj.items.configurations | Where type -EQ "core-site").properties | Get-Member -membertype properties | Where Name -Like "fs.azure.account.key.*"
foreach ($name in $value ) { $name.Name.Split(".")[4]}
```

### <a name="apache-ambari"></a>Apache Ambari

1. Navigeer in een webbrowser naar `https://CLUSTERNAME.azurehdinsight.net`, waarbij `CLUSTERNAME` de naam van uw cluster is.

1. Navigeer naar **HDFS**  >  **configs**  >  **Geavanceerde**  >  **aangepaste kern-site**.

1. Bekijk de sleutels die beginnen met `fs.azure.account.key` . De account naam maakt deel uit van de sleutel, zoals wordt weer gegeven in deze voorbeeld afbeelding:

   :::image type="content" source="./media/hdinsight-hadoop-add-storage/apache-ambari-verification.png" alt-text="verificatie via Apache Ambari":::

## <a name="remove-storage-account"></a>Opslag account verwijderen

1. Navigeer in een webbrowser naar `https://CLUSTERNAME.azurehdinsight.net`, waarbij `CLUSTERNAME` de naam van uw cluster is.

1. Navigeer naar **HDFS**  >  **configs**  >  **Geavanceerde**  >  **aangepaste kern-site**.

1. Verwijder de volgende sleutels:
    * `fs.azure.account.key.<STORAGE_ACCOUNT_NAME>.blob.core.windows.net`
    * `fs.azure.account.keyprovider.<STORAGE_ACCOUNT_NAME>.blob.core.windows.net`

Nadat u deze sleutels hebt verwijderd en de configuratie hebt opgeslagen, moet u Oozie, garens, MapReduce2, HDFS en Hive één voor één opnieuw starten.

## <a name="known-issues"></a>Bekende problemen

### <a name="storage-firewall"></a>Opslag firewall

Als u ervoor kiest om uw opslag account te beveiligen met de **firewalls en beperkingen voor virtuele netwerken** op **geselecteerde netwerken**, moet u de uitzonde ring inschakelen **vertrouwde micro soft-Services toestaan...** zodat HDInsight toegang kan krijgen tot uw opslag account`.`

### <a name="unable-to-access-storage-after-changing-key"></a>Kan geen toegang krijgen tot de opslag na het wijzigen van de sleutel

Als u de sleutel voor een opslag account wijzigt, heeft HDInsight geen toegang meer tot het opslag account. HDInsight gebruikt een kopie van de sleutel in de cache van de core-site.xml voor het cluster. Deze kopie in de cache moet worden bijgewerkt om overeen te komen met de nieuwe sleutel.

Als u de script actie opnieuw uitvoert, wordt de sleutel **niet** bijgewerkt, omdat er wordt gecontroleerd of er al een vermelding voor het opslag account bestaat. Als er al een vermelding bestaat, worden er geen wijzigingen aangebracht.

Om dit probleem te omzeilen:  
1. Verwijder het opslag account.
1. Voeg het opslag account toe.

> [!IMPORTANT]  
> Het draaien van de opslag sleutel voor het primaire opslag account dat aan een cluster is gekoppeld, wordt niet ondersteund.

## <a name="next-steps"></a>Volgende stappen

U hebt geleerd hoe u extra opslag accounts toevoegt aan een bestaand HDInsight-cluster. Zie [HDInsight-clusters op basis van Linux aanpassen met script actie](hdinsight-hadoop-customize-cluster-linux.md) voor meer informatie over script acties