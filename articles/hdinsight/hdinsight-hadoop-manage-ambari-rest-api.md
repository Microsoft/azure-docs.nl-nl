---
title: Hadoop controleren en beheren met Ambari REST API-Azure HDInsight
description: Meer informatie over het gebruik van Ambari voor het bewaken en beheren van Hadoop-clusters in azure HDInsight. In dit document leert u hoe u het Ambari gebruikt REST API dat deel uitmaakt van HDInsight-clusters.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,seoapr2020
ms.date: 04/29/2020
ms.openlocfilehash: 1d4e6f0d6a0242cda919364965a61e4314927d87
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98945579"
---
# <a name="manage-hdinsight-clusters-by-using-the-apache-ambari-rest-api"></a>HDInsight-clusters beheren met behulp van de Apache Ambari REST API

[!INCLUDE [ambari-selector](../../includes/hdinsight-ambari-selector.md)]

Meer informatie over het gebruik van de Apache Ambari REST API voor het beheren en controleren van Apache Hadoop clusters in azure HDInsight.

## <a name="what-is-apache-ambari"></a>Wat is Apache Ambari

Apache Ambari vereenvoudigt het beheer en de bewaking van Hadoop-clusters door een gebruiks vriendelijke webinterface te bieden die wordt ondersteund door de [rest api's](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md).  Ambari wordt standaard meegeleverd met HDInsight-clusters op basis van Linux.

## <a name="prerequisites"></a>Vereisten

* Een Hadoop-cluster in HDInsight. Zie aan de [slag met HDInsight op Linux](hadoop/apache-hadoop-linux-tutorial-get-started.md).

* Bash op Ubuntu in Windows 10.  In de voorbeelden in dit artikel wordt gebruikgemaakt van de Bash-shell in Windows 10. Zie [Installatiehandleiding voor Windows 10 voor Windows-subsysteem voor Linux](/windows/wsl/install-win10) voor installatiestappen.  Andere [UNIX-shells](https://www.gnu.org/software/bash/) kunnen ook worden gebruikt.  De voor beelden, met een kleine wijziging, kunnen werken aan een Windows-opdracht prompt.  U kunt ook Windows Power shell gebruiken.

* jq, een opdrachtregel-JSON-processor.  Zie [https://stedolan.github.io/jq/](https://stedolan.github.io/jq/).

* Windows PowerShell.  U kunt ook bash gebruiken.

## <a name="base-uniform-resource-identifier-for-ambari-rest-api"></a>Base-uniform resource-id voor Ambari rest API

 De basis-URI (Uniform Resource Identifier) voor de Ambari REST API in HDInsight is `https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME` , waarbij `CLUSTERNAME` de naam van uw cluster is.  Cluster namen in Uri's zijn **hoofdletter gevoelig**.  De naam van het cluster in het Fully Qualified Domain Name (FQDN) van de URI ( `CLUSTERNAME.azurehdinsight.net` ) is hoofdletter gevoelig en andere exemplaren in de URI zijn hoofdletter gevoelig.

## <a name="authentication"></a>Verificatie

Voor het maken van verbinding met Ambari op HDInsight is HTTPS vereist. Gebruik de naam van het beheerders account (de standaard **beheerder**) en het wacht woord dat u hebt opgegeven tijdens het maken van het cluster.

Voor Enterprise Security Package clusters gebruikt u in plaats van `admin` een volledig gekwalificeerde gebruikers naam zoals `username@domain.onmicrosoft.com` .

## <a name="examples"></a>Voorbeelden

### <a name="setup-preserve-credentials"></a>Setup (referenties behouden)

Bewaar uw referenties om te voor komen dat ze opnieuw worden ingevoerd voor elk voor beeld.  De naam van het cluster wordt in een afzonderlijke stap bewaard.

**A. bash**  
Bewerk het onderstaande script door `PASSWORD` uw eigen wacht woord te vervangen.  Voer vervolgens de opdracht in.

```bash
export password='PASSWORD'
```  

**B. Power shell**  

```powershell
$creds = Get-Credential -UserName "admin" -Message "Enter the HDInsight login"
```

### <a name="identify-correctly-cased-cluster-name"></a>Cluster naam met een onjuiste naam identificeren

De daad werkelijke behuizing van de cluster naam kan anders zijn dan verwacht.  In de stappen hier wordt het werkelijke hoofdletter gebruik weer gegeven en opgeslagen in een variabele voor alle latere voor beelden.

Bewerk de onderstaande scripts om deze te vervangen `CLUSTERNAME` door de naam van uw cluster. Voer vervolgens de opdracht in. (De cluster naam voor de FQDN is niet hoofdletter gevoelig.)

```bash
export clusterName=$(curl -u admin:$password -sS -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters" | jq -r '.items[].Clusters.cluster_name')
echo $clusterName
```  

```powershell
# Identify properly cased cluster name
$resp = Invoke-WebRequest -Uri "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters" `
    -Credential $creds -UseBasicParsing
$clusterName = (ConvertFrom-Json $resp.Content).items.Clusters.cluster_name;

# Show cluster name
$clusterName
```

### <a name="parsing-json-data"></a>JSON-gegevens parseren

In het volgende voor beeld wordt [JQ](https://stedolan.github.io/jq/) of [ConvertFrom-JSON](/powershell/module/microsoft.powershell.utility/convertfrom-json) gebruikt om het JSON-respons document te parseren en alleen de `health_report` informatie van de resultaten weer te geven.

```bash
curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName" \
| jq '.Clusters.health_report'
```  

```powershell
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName" `
    -Credential $creds -UseBasicParsing
$respObj = ConvertFrom-Json $resp.Content
$respObj.Clusters.health_report
```

### <a name="get-the-fqdn-of-cluster-nodes"></a>De FQDN-naam van cluster knooppunten ophalen

Mogelijk moet u de Fully Qualified Domain Name (FQDN) van een cluster knooppunt weten. U kunt de FQDN voor de verschillende knoop punten in het cluster eenvoudig ophalen met behulp van de volgende voor beelden:

**Alle knoop punten**  

```bash
curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/hosts" \
| jq -r '.items[].Hosts.host_name'
```  

```powershell
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/hosts" `
    -Credential $creds -UseBasicParsing
$respObj = ConvertFrom-Json $resp.Content
$respObj.items.Hosts.host_name
```

**Hoofd knooppunten**  

```bash
curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/HDFS/components/NAMENODE" \
| jq -r '.host_components[].HostRoles.host_name'
```

```powershell
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/HDFS/components/NAMENODE" `
    -Credential $creds -UseBasicParsing
$respObj = ConvertFrom-Json $resp.Content
$respObj.host_components.HostRoles.host_name
```

**Worker-knoop punten**  

```bash
curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/HDFS/components/DATANODE" \
| jq -r '.host_components[].HostRoles.host_name'
```

```powershell
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/HDFS/components/DATANODE" `
    -Credential $creds -UseBasicParsing
$respObj = ConvertFrom-Json $resp.Content
$respObj.host_components.HostRoles.host_name
```

**Zookeeper-knoop punten**

```bash
curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/ZOOKEEPER/components/ZOOKEEPER_SERVER" \
| jq -r ".host_components[].HostRoles.host_name"
```

```powershell
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/ZOOKEEPER/components/ZOOKEEPER_SERVER" `
    -Credential $creds -UseBasicParsing
$respObj = ConvertFrom-Json $resp.Content
$respObj.host_components.HostRoles.host_name
```

### <a name="get-the-internal-ip-address-of-cluster-nodes"></a>Het interne IP-adres van de cluster knooppunten ophalen

De IP-adressen die worden geretourneerd door de voor beelden in deze sectie zijn niet rechtstreeks toegankelijk via internet. Ze zijn alleen toegankelijk in de Azure-Virtual Network die het HDInsight-cluster bevatten.

Zie [een virtueel netwerk plannen voor hdinsight](hdinsight-plan-virtual-network-deployment.md)voor meer informatie over het werken met hdinsight en virtuele netwerken.

Als u het IP-adres wilt vinden, moet u de interne Fully Qualified Domain Name (FQDN) van de cluster knooppunten kennen. Zodra u de FQDN hebt, kunt u het IP-adres van de host ophalen. In de volgende voor beelden wordt eerst de Ambari voor de FQDN-naam van alle host knooppunten opgevraagd. Voert vervolgens een query uit op Ambari voor het IP-adres van elke host.

```bash
for HOSTNAME in $(curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/hosts" | jq -r '.items[].Hosts.host_name')
do
    IP=$(curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/hosts/$HOSTNAME" | jq -r '.Hosts.ip')
  echo "$HOSTNAME <--> $IP"
done
```  

```powershell
$uri = "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/hosts" 
$resp = Invoke-WebRequest -Uri $uri -Credential $creds -UseBasicParsing
$respObj = ConvertFrom-Json $resp.Content
foreach($item in $respObj.items) {
    $hostName = [string]$item.Hosts.host_name
    $hostInfoResp = Invoke-WebRequest -Uri "$uri/$hostName" `
        -Credential $creds -UseBasicParsing
    $hostInfoObj = ConvertFrom-Json $hostInfoResp
    $hostIp = $hostInfoObj.Hosts.ip
    "$hostName <--> $hostIp"
}
```

### <a name="get-the-default-storage"></a>De standaard opslag ophalen

HDInsight-clusters moeten een Azure Storage account of Data Lake Storage als standaard opslag gebruiken. U kunt Ambari gebruiken om deze gegevens op te halen nadat het cluster is gemaakt. Bijvoorbeeld als u gegevens wilt lezen/schrijven naar de container buiten HDInsight.

In de volgende voor beelden wordt de standaard opslag configuratie van het cluster opgehaald:

```bash
curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=1" \
| jq -r '.items[].configurations[].properties["fs.defaultFS"] | select(. != null)'
```

```powershell
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=1" `
    -Credential $creds -UseBasicParsing
$respObj = ConvertFrom-Json $resp.Content
$respObj.items.configurations.properties.'fs.defaultFS'
```

> [!IMPORTANT]  
> In deze voor beelden wordt de eerste configuratie geretourneerd die wordt toegepast op de server ( `service_config_version=1` ) die deze informatie bevat. Als u een waarde ophaalt die is gewijzigd na het maken van het cluster, moet u mogelijk de configuratie versies weer geven en de laatste ophalen.

De geretourneerde waarde is vergelijkbaar met een van de volgende voor beelden:

* `wasbs://CONTAINER@ACCOUNTNAME.blob.core.windows.net` -Deze waarde geeft aan dat het cluster een Azure Storage-account gebruikt voor de standaard opslag. De `ACCOUNTNAME` waarde is de naam van het opslag account. Het `CONTAINER` gedeelte is de naam van de BLOB-container in het opslag account. De container is de hoofdmap van de HDFS-compatibele opslag voor het cluster.

* `abfs://CONTAINER@ACCOUNTNAME.dfs.core.windows.net` -Deze waarde geeft aan dat het cluster Azure Data Lake Storage Gen2 gebruikt voor de standaard opslag. De `ACCOUNTNAME` `CONTAINER` waarden en hebben dezelfde betekenis als voor Azure Storage eerder beschreven.

* `adl://home` -Deze waarde geeft aan dat het cluster Azure Data Lake Storage Gen1 gebruikt voor de standaard opslag.

    Als u de naam van het Data Lake Storage account wilt vinden, gebruikt u de volgende voor beelden:

    ```bash
    curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=1" \
    | jq -r '.items[].configurations[].properties["dfs.adls.home.hostname"] | select(. != null)'
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=1" `
        -Credential $creds -UseBasicParsing
    $respObj = ConvertFrom-Json $resp.Content
    $respObj.items.configurations.properties.'dfs.adls.home.hostname'
    ```

    De geretourneerde waarde is vergelijkbaar met `ACCOUNTNAME.azuredatalakestore.net` , waarbij de `ACCOUNTNAME` naam is van het data Lake Storage-account.

    Gebruik de volgende voor beelden om de map te vinden in Data Lake Storage die de opslag voor het cluster bevat:

    ```bash
    curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=1" \
    | jq -r '.items[].configurations[].properties["dfs.adls.home.mountpoint"] | select(. != null)'
    ```  

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=1" `
        -Credential $creds -UseBasicParsing
    $respObj = ConvertFrom-Json $resp.Content
    $respObj.items.configurations.properties.'dfs.adls.home.mountpoint'
    ```

    De geretourneerde waarde is vergelijkbaar met `/clusters/CLUSTERNAME/` . Deze waarde is een pad binnen het Data Lake Storage-account. Dit pad is de hoofdmap van het HDFS-compatibele bestands systeem voor het cluster.  

> [!NOTE]  
> Met de cmdlet [Get-AzHDInsightCluster](/powershell/module/az.hdinsight/get-azhdinsightcluster) die door [Azure PowerShell](/powershell/azure/) wordt verstrekt, worden ook de opslag gegevens voor het cluster geretourneerd.

### <a name="get-all-configurations"></a>Alle configuraties ophalen

Haal de configuraties op die beschikbaar zijn voor uw cluster.

```bash
curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName?fields=Clusters/desired_configs"
```

```powershell
$respObj = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName`?fields=Clusters/desired_configs" `
    -Credential $creds -UseBasicParsing
$respObj.Content
```

In dit voor beeld wordt een JSON-document geretourneerd met de huidige configuratie voor geïnstalleerde onderdelen. Bekijk de waarde van het *Label* . Het volgende voor beeld is een fragment van de gegevens die worden geretourneerd door een Spark-cluster type.

```json
"jupyter-site" : {
  "tag" : "INITIAL",
  "version" : 1
},
"livy2-client-conf" : {
  "tag" : "INITIAL",
  "version" : 1
},
"livy2-conf" : {
  "tag" : "INITIAL",
  "version" : 1
},
```

### <a name="get-configuration-for-specific-component"></a>Configuratie voor specifiek onderdeel ophalen

Haal de configuratie op voor het onderdeel waarin u bent geïnteresseerd. Vervang in het volgende voor beeld door `INITIAL` de tag-waarde die is geretourneerd door de vorige aanvraag.

```bash
curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations?type=livy2-conf&tag=INITIAL"
```

```powershell
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations?type=livy2-conf&tag=INITIAL" `
    -Credential $creds -UseBasicParsing
$resp.Content
```

In dit voor beeld wordt een JSON-document geretourneerd met de huidige configuratie voor het `livy2-conf` onderdeel.

### <a name="update-configuration"></a>Configuratie bijwerken

1. Maken `newconfig.json` .  
   Wijzig en voer de volgende opdrachten in:

   * Vervang door `livy2-conf` het nieuwe onderdeel.
   * Vervangen `INITIAL` door werkelijke waarde opgehaald voor `tag` van [alle configuraties ophalen](#get-all-configurations).

     **A. bash**

     ```bash
     curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations?type=livy2-conf&tag=INITIAL" \
     | jq --arg newtag $(echo version$(date +%s%N)) '.items[] | del(.href, .version, .Config) | .tag |= $newtag | {"Clusters": {"desired_config": .}}' > newconfig.json
     ```

     **B. Power shell**  
     Het Power shell-script maakt gebruik van [JQ](https://stedolan.github.io/jq/).  Bewerk `C:\HD\jq\jq-win64` hieronder om uw eigen pad en de huidige versie van [JQ](https://stedolan.github.io/jq/)weer te geven.

     ```powershell
     $epoch = Get-Date -Year 1970 -Month 1 -Day 1 -Hour 0 -Minute 0 -Second 0
     $now = Get-Date
     $unixTimeStamp = [math]::truncate($now.ToUniversalTime().Subtract($epoch).TotalMilliSeconds)
     $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations?type=livy2-conf&tag=INITIAL" `
       -Credential $creds -UseBasicParsing
     $resp.Content | C:\HD\jq\jq-win64 --arg newtag "version$unixTimeStamp" '.items[] | del(.href, .version, .Config) | .tag |= $newtag | {"Clusters": {"desired_config": .}}' > newconfig.json
     ```

     JQ wordt gebruikt om de gegevens die worden opgehaald van HDInsight, in te scha kelen in een nieuwe configuratie sjabloon. Deze voor beelden doen met name de volgende acties:

   * Hiermee maakt u een unieke waarde met de teken reeks "version" en de datum, die wordt opgeslagen in `newtag` .

   * Hiermee maakt u een hoofd document voor de nieuwe configuratie.

   * Hiermee haalt u de inhoud van de `.items[]` matrix en voegt u deze toe onder het element **desired_config** .

   * Hiermee worden `href` de `version` elementen, en, verwijderd `Config` , omdat deze elementen niet nodig zijn om een nieuwe configuratie in te dienen.

   * Hiermee wordt een `tag` element met de waarde van toegevoegd `version#################` . Het numerieke gedeelte is gebaseerd op de huidige datum. Elke configuratie moet een unieke tag hebben.

     Ten slotte worden de gegevens opgeslagen in het `newconfig.json` document. De document structuur moet er ongeveer uitzien als in het volgende voor beeld:

     ```json
     {
       "Clusters": {
         "desired_config": {
           "tag": "version1552064778014",
           "type": "livy2-conf",
           "properties": {
             "livy.environment": "production",
             "livy.impersonation.enabled": "true",
             "livy.repl.enableHiveContext": "true",
             "livy.server.csrf_protection.enabled": "true",
               ....
           },
         },
       }
     }
     ```

2. Bewerken `newconfig.json` .  
   Open het `newconfig.json` document en wijzig/voeg waarden toe aan het `properties` object. In het volgende voor beeld wordt de waarde van van gewijzigd in `"livy.server.csrf_protection.enabled"` `"true"` `"false"` .

    ```json
    "livy.server.csrf_protection.enabled": "false",
    ```

    Sla het bestand op wanneer u klaar bent met het maken van wijzigingen.

3. Verzenden `newconfig.json` .  
   Gebruik de volgende opdrachten om de bijgewerkte configuratie naar Ambari te verzenden.

    ```bash
    curl -u admin:$password -sS -H "X-Requested-By: ambari" -X PUT -d @newconfig.json "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName"
    ```

    ```powershell
    $newConfig = Get-Content .\newconfig.json
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName" `
        -Credential $creds -UseBasicParsing `
        -Method PUT `
        -Headers @{"X-Requested-By" = "ambari"} `
        -Body $newConfig
    $resp.Content
    ```  

    Met deze opdrachten wordt de inhoud van het **newconfig.js** bestand naar het cluster verzonden als de nieuwe configuratie. De aanvraag retourneert een JSON-document. Het **versionTag** -element in dit document moet overeenkomen met de versie die u hebt verzonden en het **configuratie** object bevat de configuratie wijzigingen die u hebt aangevraagd.

### <a name="restart-a-service-component"></a>Een service onderdeel opnieuw starten

Op dat moment geeft de Ambari-webgebruikersinterface aan dat de Spark-service opnieuw moet worden gestart voordat de nieuwe configuratie van kracht kan worden. Gebruik de volgende stappen om de service opnieuw te starten.

1. Gebruik het volgende om de onderhouds modus in te scha kelen voor de Spark2-service:

    ```bash
    curl -u admin:$password -sS -H "X-Requested-By: ambari" \
    -X PUT -d '{"RequestInfo": {"context": "turning on maintenance mode for SPARK2"},"Body": {"ServiceInfo": {"maintenance_state":"ON"}}}' \
    "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK2"
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK2" `
        -Credential $creds -UseBasicParsing `
        -Method PUT `
        -Headers @{"X-Requested-By" = "ambari"} `
        -Body '{"RequestInfo": {"context": "turning on maintenance mode for SPARK2"},"Body": {"ServiceInfo": {"maintenance_state":"ON"}}}'
    ```

2. Onderhouds modus controleren  

    Met deze opdrachten wordt een JSON-document verzonden naar de server die de onderhouds modus inschakelt. U kunt controleren of de service zich nu in de onderhouds modus bevindt met de volgende aanvraag:

    ```bash
    curl -u admin:$password -sS -H "X-Requested-By: ambari" \
    "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK2" \
    | jq .ServiceInfo.maintenance_state
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK2" `
        -Credential $creds -UseBasicParsing
    $respObj = ConvertFrom-Json $resp.Content
    $respObj.ServiceInfo.maintenance_state
    ```

    De geretourneerde waarde is `ON` .

3. Gebruik vervolgens de volgende om de Spark2-service uit te scha kelen:

    ```bash
    curl -u admin:$password -sS -H "X-Requested-By: ambari" \
    -X PUT -d '{"RequestInfo":{"context":"_PARSE_.STOP.SPARK2","operation_level":{"level":"SERVICE","cluster_name":"CLUSTERNAME","service_name":"SPARK"}},"Body":{"ServiceInfo":{"state":"INSTALLED"}}}' \
    "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK2"
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK2" `
        -Credential $creds -UseBasicParsing `
        -Method PUT `
        -Headers @{"X-Requested-By" = "ambari"} `
        -Body '{"RequestInfo":{"context":"_PARSE_.STOP.SPARK2","operation_level":{"level":"SERVICE","cluster_name":"CLUSTERNAME","service_name":"SPARK"}},"Body":{"ServiceInfo":{"state":"INSTALLED"}}}'
    $resp.Content
    ```

    Het antwoord is vergelijkbaar met het volgende voorbeeld:

    ```json
    {
        "href" : "http://10.0.0.18:8080/api/v1/clusters/CLUSTERNAME/requests/29",
        "Requests" : {
            "id" : 29,
            "status" : "Accepted"
        }
    }
    ```

    > [!IMPORTANT]  
    > De `href` waarde die door deze URI wordt geretourneerd, maakt gebruik van het interne IP-adres van het cluster knooppunt. Als u het van buiten het cluster wilt gebruiken, vervangt u het `10.0.0.18:8080` gedeelte door de FQDN van het cluster.  

4. Controleer de aanvraag.  
    Bewerk de onderstaande opdracht door de werkelijke waarde te vervangen door de `29` `id` vorige stap.  Met de volgende opdrachten wordt de status van de aanvraag opgehaald:

    ```bash
    curl -u admin:$password -sS -H "X-Requested-By: ambari" \
    "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/requests/29" \
    | jq .Requests.request_status
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/requests/29" `
        -Credential $creds -UseBasicParsing
    $respObj = ConvertFrom-Json $resp.Content
    $respObj.Requests.request_status
    ```

    Een reactie van `COMPLETED` geeft aan dat de aanvraag is voltooid.

5. Als de vorige aanvraag is voltooid, gebruikt u de volgende opdracht om de Spark2-service te starten.

    ```bash
    curl -u admin:$password -sS -H "X-Requested-By: ambari" \
    -X PUT -d '{"RequestInfo":{"context":"_PARSE_.START.SPARK2","operation_level":{"level":"SERVICE","cluster_name":"CLUSTERNAME","service_name":"SPARK"}},"Body":{"ServiceInfo":{"state":"STARTED"}}}' \
    "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK2"
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK2" `
        -Credential $creds -UseBasicParsing `
        -Method PUT `
        -Headers @{"X-Requested-By" = "ambari"} `
        -Body '{"RequestInfo":{"context":"_PARSE_.START.SPARK2","operation_level":{"level":"SERVICE","cluster_name":"CLUSTERNAME","service_name":"SPARK"}},"Body":{"ServiceInfo":{"state":"STARTED"}}}'
    $resp.Content
    ```

    De service gebruikt nu de nieuwe configuratie.

6. Gebruik tot slot de volgende optie om de onderhouds modus uit te scha kelen.

    ```bash
    curl -u admin:$password -sS -H "X-Requested-By: ambari" \
    -X PUT -d '{"RequestInfo": {"context": "turning off maintenance mode for SPARK2"},"Body": {"ServiceInfo": {"maintenance_state":"OFF"}}}' \
    "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK2"
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/SPARK2" `
        -Credential $creds -UseBasicParsing `
        -Method PUT `
        -Headers @{"X-Requested-By" = "ambari"} `
        -Body '{"RequestInfo": {"context": "turning off maintenance mode for SPARK2"},"Body": {"ServiceInfo": {"maintenance_state":"OFF"}}}'
    ```

## <a name="next-steps"></a>Volgende stappen

Zie [Apache AMBARI API Reference v1](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md)(Engelstalig) voor een volledige referentie van de rest API.  Zie ook [gebruikers machtigen voor Apache Ambari-weer gaven](./hdinsight-authorize-users-to-ambari.md)