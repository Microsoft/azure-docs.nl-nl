---
title: De hostnamen van Azure HDInsight-cluster knooppunten ophalen
description: Meer informatie over het ophalen van hostnamen en FQDN-namen van Azure HDInsight-cluster knooppunten.
ms.service: hdinsight
ms.topic: how-to
author: yanancai
ms.author: yanacai
ms.date: 03/23/2021
ms.openlocfilehash: 676b4ccd52e8a6f1f378756a255ad55b74f18ebe
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105111441"
---
# <a name="find-the-host-names-of-cluster-nodes"></a>De hostnamen van de cluster knooppunten zoeken

HDInsight-cluster wordt gemaakt met open bare DNS-clustername.azurehdinsight.net. Wanneer u SSHeert naar afzonderlijke knoop punten of verbinding met cluster knooppunten met in hetzelfde aangepaste virtuele netwerk hebt ingesteld, moet u de hostnaam of volledig gekwalificeerde domein namen (FQDN) van de cluster knooppunten gebruiken.

In dit artikel leert u hoe u de hostnamen van cluster knooppunten kunt ophalen. U kunt dit hand matig doen via Ambari Web UI of automatisch via Ambari REST API.

> [!WARNING]
> Gebruik de volgende aanbevolen benaderingen om de hostnamen van cluster knooppunten op te halen. De getallen in de naam van de host zijn niet gegarandeerd in de volg orde en HDInsight kunnen de indeling van de hostnaam wijzigen zodat deze wordt uitgelijnd met virtuele machines met vrijgave vernieuwing. Maak geen afhankelijkheid van een bepaalde naam Conventie die vandaag nog bestaat. 
>

U kunt de hostnamen ophalen via Ambari UI of Ambari REST API. 

## <a name="get-the-host-names-from-ambari-web-ui"></a>De hostnamen ophalen uit de Ambari-webgebruikersinterface
U kunt de Ambari-webgebruikersinterface gebruiken om de hostnamen op te halen wanneer u SSHt naar het knoop punt. De weer gave hosts van de Ambari-webgebruikersinterface is beschikbaar op uw HDInsight-cluster op `https://CLUSTERNAME.azurehdinsight.net/#/main/hosts` , waar `CLUSTERNAME` de naam van uw cluster is.

![Get-host-namen-in-Ambari-UI](.\media\find-host-name\find-host-name-in-ambari-ui.png)

## <a name="get-the-host-names-from-ambari-rest-api"></a>Haal de hostnamen op uit Ambari REST API
Wanneer u automatiserings scripts bouwt, kunt u de Ambari-REST API gebruiken om de hostnamen op te halen voordat u verbinding maakt met hosts. De getallen in de hostnaam zijn niet gegarandeerd in volg orde en HDInsight kunnen de indeling van de hostnaam wijzigen zodat deze kan worden uitgelijnd met virtuele machines met vrijgave vernieuwing. Maak geen afhankelijkheid van een bepaalde naam Conventie die vandaag nog bestaat. 

Hier volgen enkele voor beelden van het ophalen van de FQDN-naam voor de knoop punten in het cluster. Zie [HDInsight-clusters beheren met behulp van de Apache Ambari rest API](.\hdinsight-hadoop-manage-ambari-rest-api.md) voor meer informatie over Ambari rest API

In het volgende voor beeld wordt [JQ](https://stedolan.github.io/jq/) of [ConvertFrom-JSON](/powershell/module/microsoft.powershell.utility/convertfrom-json) gebruikt om het JSON-respons document te parseren en alleen de hostnamen weer te geven.

```bash
export password=''
export clusterName=''
curl -u admin:$password -sS -G "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/hosts" \
| jq -r '.items[].Hosts.host_name'
```  

```powershell
$clusterName=''
$creds = Get-Credential -UserName "admin" -Message "Enter the HDInsight login"
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/hosts" `
    -Credential $creds -UseBasicParsing
$respObj = ConvertFrom-Json $resp.Content
$respObj.items.Hosts.host_name
```