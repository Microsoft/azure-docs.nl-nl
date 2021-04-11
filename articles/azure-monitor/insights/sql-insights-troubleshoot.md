---
title: Problemen met SQL Insights oplossen (preview-versie)
description: Problemen met SQL Insights in Azure Monitor oplossen
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/04/2021
ms.openlocfilehash: 85a3505dd347b96036c28c85c089afa04e3e3bd5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104609374"
---
# <a name="troubleshooting-sql-insights-preview"></a>Problemen met SQL Insights oplossen (preview-versie)
Als u problemen met het verzamelen van gegevens in SQL Insights wilt oplossen, controleert u de status van de bewakings machine op het tabblad **profiel beheren** . Dit heeft een van de volgende statussen:

- Verzamelen 
- Niet verzamelen 
- Verzamelen met fouten 
 
Klik op de **status** om in te zoomen op Logboeken en meer details, waarmee u het probleem mogelijk kunt oplossen. 

:::image type="content" source="media/sql-insights-enable/monitoring-machine-status.png" alt-text="Status van bewakings machine":::

## <a name="not-collecting-state"></a>Status wordt niet verzameld 
De bewakings computer heeft de status *niet verzameld* als er in de afgelopen tien minuten geen gegevens in *InsightsMetrics* voor SQL zijn. 

SQL Insights maakt gebruik van de volgende query om deze informatie op te halen:

```
InsightsMetrics 
    | extend Tags = todynamic(Tags) 
    | extend SqlInstance = tostring(Tags.sql_instance) 
    | where TimeGenerated > ago(10m) and isnotempty(SqlInstance) and Namespace == 'sqlserver_server_properties' and Name == 'uptime' 
```

Controleer of er logboeken van het telegrafie zijn waarmee de hoofd oorzaak van het probleem kan worden geïdentificeerd. Als er logboek vermeldingen zijn, kunt u klikken op *niet verzamelen* en raadpleegt u de logboeken en informatie over probleem oplossing voor veelvoorkomende problemen. 


Als er geen logboeken zijn, moet u de logboeken op de virtuele machine voor bewaking controleren op de volgende services die zijn geïnstalleerd door twee virtuele-machine uitbreidingen:

- Micro soft. Azure. monitor. AzureMonitorLinuxAgent 
  - Service: mdsd 
- Micro soft. Azure. monitor. workloads. workload. WLILinuxExtension 
  - Service: WLI 
  - Service: MS-Telegraf 
  - Service: TD-agent-bit-WLI 
  - Extensie logboek om installatie fouten te controleren:/var/log/azure/Microsoft.Azure.Monitor.Workloads.Workload.WLILinuxExtension/wlilogs.log 



### <a name="wli-service-logs"></a>WLI-service logboeken 

Service logboeken: `/var/log/wli.log`

Recente logboeken bekijken: `tail -n 100 -f /var/log/wli.log`
 

Als het volgende fouten logboek wordt weer gegeven, duidt dit op een probleem met de **mdsd** -service.

```
2021-01-27T06:09:28Z [Error] Failed to get config data. Error message: dial unix /var/run/mdsd/default_fluent.socket: connect: no such file or directory 
```


### <a name="telegraf-service-logs"></a>Logboeken voor telegrafie service 

Service logboeken: `/var/log/ms-telegraf/telegraf.log`

Recente logboeken bekijken: `tail -n 100 -f /var/log/ms-telegraf/telegraf.log` voor een overzicht van recente fout-en waarschuwings logboeken: `tail -n 1000 /var/log/ms-telegraf/telegraf.log | grep "E\!\|W!"`

 De configuratie die door Telegraf wordt gebruikt, wordt gegenereerd door de WLI-service en geplaatst in: `/etc/ms-telegraf/telegraf.d/wli`
 
Als er een onjuiste configuratie wordt gegenereerd, kan de MS-telegrafie service mogelijk niet worden gestart. Controleer of de MS-telegrafie service wordt uitgevoerd met de opdracht: `service ms-telegraf status`

Als u fout berichten van de telegrafie service wilt bekijken, voert u deze hand matig uit met de volgende opdracht: 

```
/usr/bin/ms-telegraf --config /etc/ms-telegraf/telegraf.conf --config-directory /etc/ms-telegraf/telegraf.d/wli --test 
```

### <a name="mdsd-service-logs"></a>mdsd-service logboeken 

Controleer de [huidige beperkingen](../agents/azure-monitor-agent-overview.md#current-limitations) voor de Azure monitor-agent. 


Service logboeken:  
- `/var/log/mdsd.err`
- `/var/log/mdsd.warn`
- `/var/log/mdsd.info`

Recente fouten bekijken: `tail -n 100 -f /var/log/mdsd.err`

 Als u contact moet opnemen met de ondersteuning, verzamelt u de volgende informatie: 

- Meldt zich aan `/var/log/azure/Microsoft.Azure.Monitor.AzureMonitorLinuxAgent/` 
- Aanmelden `/var/log/waagent.log` 
- Meldt zich aan `/var/log/mdsd*`
- Bestanden in `/etc/mdsd.d/`
- Profiler `/etc/default/mdsd`

### <a name="invalid-monitoring-virtual-machine-configuration"></a>Ongeldige configuratie van de virtuele machine controleren

Een oorzaak van de *niet-verzamelde* status is wanneer u een ongeldige bewakings configuratie voor virtuele machines hebt.  Dit is de standaard configuratie:

```json
{
    "version": 1,
    "secrets": {
        "telegrafPassword": {
            "keyvault": "https://mykeyvault.vault.azure.net/",
            "name": "sqlPassword"
        }
    },
    "parameters": {
        "sqlAzureConnections": [
            "Server=mysqlserver.database.windows.net;Port=1433;Database=mydatabase;User Id=telegraf;Password=$telegrafPassword;"
        ],
        "sqlVmConnections": [
        ],
        "sqlManagedInstanceConnections": [
        ]
    }
}
```

Met deze configuratie geeft u de vervangings tokens op die moeten worden gebruikt in de profiel configuratie op uw virtuele bewakings machine. U kunt ook verwijzen naar geheimen van Azure Key Vault, zodat u geen geheime waarden in een configuratie hoeft te gebruiken. dit wordt ten zeerste aanbevolen.

#### <a name="secrets"></a>Geheimen
Geheimen zijn tokens waarvan de waarden worden opgehaald tijdens de uitvoering van een Azure Key Vault. Een geheim wordt gedefinieerd door een combi natie van een Key Vault referentie en geheime naam. Hiermee kan Azure Monitor de dynamische waarde van het geheim ophalen en deze gebruiken als downstream-configuratie verwijzingen.

U kunt zoveel geheimen definiëren als nodig is in de configuratie, inclusief geheimen die zijn opgeslagen in afzonderlijke sleutel kluizen.

```json
   "secrets": {
        "<secret-token-name-1>": {
            "keyvault": "<key-vault-uri>",
            "name": "<key-vault-secret-name>"
        },
        "<secret-token-name-2>": {
            "keyvault": "<key-vault-uri-2>",
            "name": "<key-vault-secret-name-2>"
        }
    }
```

De machtigingen voor toegang tot de Key Vault worden door gegeven aan een Managed Service Identity op de virtuele machine voor bewaking. Azure Monitor verwacht dat de Key Vault om ten minste geheimen toestemming te geven voor de virtuele machine. U kunt dit inschakelen vanuit de sjabloon Azure Portal, Power shell, CLI of Resource Manager.

#### <a name="parameters"></a>Parameters
Para meters zijn tokens waarnaar kan worden verwezen in de profiel configuratie via JSON sjabloon. Para meters hebben een naam en een waarde. Waarden kunnen elk JSON-type met objecten en matrices zijn. Naar een para meter wordt verwezen in de profiel configuratie met de naam in deze Conventie `.Parameters.<name>` .

Para meters kunnen verwijzen naar geheimen in Key Vault met dezelfde Conventie. `sqlAzureConnections`Verwijst bijvoorbeeld naar het geheim `telegrafPassword` met behulp van de Conventie `$telegrafPassword` .

Tijdens runtime worden alle para meters en geheimen omgezet en samengevoegd met de profiel configuratie om de daad werkelijke configuratie te maken die op de computer moet worden gebruikt.

> [!NOTE]
> De parameter namen van `sqlAzureConnections` , `sqlVmConnections` en `sqlManagedInstanceConnections` zijn allemaal vereist in de configuratie, zelfs als u geen verbindings reeksen hebt die u voor sommige van de para meters opgeeft.


## <a name="collecting-with-errors-state"></a>Status van verzamelen met fouten
De status van de bewakings machine wordt *verzameld met fouten* als er ten minste één *InsightsMetrics* -logboek is, maar er zijn ook fouten in de *bewerkings* tabel.

SQL Insights maakt gebruik van de volgende query's om deze informatie op te halen:

```
InsightsMetrics 
    | extend Tags = todynamic(Tags) 
    | extend SqlInstance = tostring(Tags.sql_instance) 
    | where TimeGenerated > ago(240m) and isnotempty(SqlInstance) and Namespace == 'sqlserver_server_properties' and Name == 'uptime' 
```

```
Operation 
 | where OperationCategory == "WorkloadInsights" 
 | summarize Errors = countif(OperationStatus == 'Error') 
```

Voor veelvoorkomende gevallen bieden we de kennis van het oplossen van problemen in de logboeken weergave: 

:::image type="content" source="media/sql-insights-enable/troubleshooting-logs-view.png" alt-text="De weer gave logboeken voor probleem oplossing.":::



## <a name="next-steps"></a>Volgende stappen

- Krijg meer informatie over het [inschakelen van SQL Insights](sql-insights-enable.md).
