---
title: Logboeken en metrische gegevens in Azure Spring Cloud | Microsoft Docs
description: Meer informatie over het analyseren van diagnostische gegevens in Azure Spring Cloud
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 01/06/2020
ms.author: brendm
ms.custom: devx-track-java
ms.openlocfilehash: 6472ba7dd055de97a1855211f21fd0c75eea814f
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107874113"
---
# <a name="analyze-logs-and-metrics-with-diagnostics-settings"></a>Logboeken en metrische gegevens analyseren met diagnostische instellingen

**Dit artikel is van toepassing op:** ✔️ Java ✔️ C#

Met de diagnostische functionaliteit van Azure Spring Cloud kunt u logboeken en metrische gegevens analyseren met een van de volgende services:

* Gebruik Azure Log Analytics, waar de gegevens naar de Azure Storage. Er is een vertraging bij het exporteren van logboeken naar Log Analytics.
* Sla logboeken op in een opslagaccount voor controle of handmatige inspectie. U kunt de bewaartijd opgeven (in dagen).
* Stream logboeken naar uw Event Hub voor opname door een service van derden of een aangepaste analyseoplossing.

Kies de logboekcategorie en metrische categorie die u wilt bewaken.

> [!TIP]
> Wilt u alleen uw logboeken streamen? Bekijk deze [Azure CLI-opdracht](/cli/azure/spring-cloud/app#az_spring_cloud_app_logs)!

## <a name="logs"></a>Logboeken

|Logboek | Description |
|----|----|
| **ApplicationConsole** | Consolelogboek van alle klanttoepassingen. |
| **SystemLogs** | Op dit moment [Spring Cloud Config Server](https://cloud.spring.io/spring-cloud-config/reference/html/#_spring_cloud_config_server) alleen logboeken in deze categorie. |

## <a name="metrics"></a>Metrische gegevens

Zie Metrische gegevens voor een volledige [lijst Spring Cloud metrische gegevens.](./spring-cloud-concept-metrics.md#user-metrics-options)

Schakel een van deze services in om de gegevens te ontvangen om aan de slag te gaan. Zie Aan de slag met Log Analytics in Azure Monitor voor meer informatie [over het configureren van Log Analytics.](../azure-monitor/logs/log-analytics-tutorial.md)

## <a name="configure-diagnostics-settings"></a>Diagnostische instellingen configureren

1. Ga in Azure Portal naar uw Azure Spring Cloud-exemplaar.
1. Selecteer **de optie Diagnostische** instellingen en selecteer vervolgens **Diagnostische instelling toevoegen.**
1. Voer een naam in voor de instelling en kies waar u de logboeken wilt verzenden. U kunt een combinatie van de volgende drie opties selecteren:
    * **Archiveren naar een opslagaccount**
    * **Streamen naar een Event Hub**
    * **Verzenden naar Log Analytics**

1. Kies welke logboekcategorie en categorie met metrische gegevens u wilt bewaken en geef vervolgens de bewaartijd op (in dagen). De bewaartijd geldt alleen voor het opslagaccount.
1. Selecteer **Opslaan**.

> [!NOTE]
> 1. Er kan een hiaat van maximaal 15 minuten zijn tussen het moment waarop logboeken of metrische gegevens worden weergegeven in uw opslagaccount, uw Event Hub of Log Analytics.
> 1. Als het Azure Spring Cloud wordt verwijderd of verplaatst, wordt de bewerking niet trapsgevat naar de resources voor **diagnostische instellingen.** De **resources voor diagnostische** instellingen moeten handmatig worden verwijderd vóór de bewerking op het bovenliggende exemplaar, dat wil zeggen de Azure Spring Cloud exemplaar. Als er anders een nieuw Azure Spring Cloud-exemplaar wordt ingericht met dezelfde resource-id als het verwijderde exemplaar, of als het Azure Spring Cloud-exemplaar wordt teruggeschoven, worden deze door de vorige **resources** voor diagnostische instellingen uitgebreid.

## <a name="view-the-logs-and-metrics"></a>De logboeken en metrische gegevens weergeven
Er zijn verschillende methoden om logboeken en metrische gegevens weer te geven, zoals beschreven onder de volgende koppen.

### <a name="use-the-logs-blade"></a>De blade Logboeken gebruiken

1. Ga in Azure Portal naar uw Azure Spring Cloud-exemplaar.
1. Selecteer Logboeken om **het deelvenster Zoeken** in logboeken te **openen.**
1. In het **zoekvak** Tabellen
   * Als u logboeken wilt weergeven, voert u een eenvoudige query in, zoals:

    ```sql
    AppPlatformLogsforSpring
    | limit 50
    ```
   * Als u metrische gegevens wilt weergeven, voert u een eenvoudige query in, zoals:

    ```sql
    AzureMetrics
    | limit 50
    ```
1. Als u het zoekresultaat wilt weergeven, selecteert u **Uitvoeren.**

### <a name="use-log-analytics"></a>Log Analytics gebruiken

1. Selecteer in Azure Portal linkerdeelvenster Log **Analytics.**
1. Selecteer de Log Analytics-werkruimte die u hebt gekozen toen u de diagnostische instellingen hebt toegevoegd.
1. Selecteer Logboeken **om het deelvenster Zoeken** in logboeken te **openen.**
1. In het **zoekvak** Tabellen
   * als u logboeken wilt weergeven, voert u een eenvoudige query in, zoals:

    ```sql
    AppPlatformLogsforSpring
    | limit 50
    ```
    * als u metrische gegevens wilt weergeven, voert u een eenvoudige query in, zoals:

    ```sql
    AzureMetrics
    | limit 50
    ```

1. Als u het zoekresultaat wilt weergeven, selecteert u **Uitvoeren.**
1. U kunt de logboeken van de specifieke toepassing of het specifieke exemplaar doorzoeken door een filtervoorwaarde in te stellen:

    ```sql
    AppPlatformLogsforSpring
    | where ServiceName == "YourServiceName" and AppName == "YourAppName" and InstanceName == "YourInstanceName"
    | limit 50
    ```
> [!NOTE]
> `==` is wel casegevoelig, maar `=~` niet.

Zie logboekquery's voor meer informatie over de querytaal die wordt gebruikt in Log Analytics [Azure Monitor logboekquery's.](/azure/data-explorer/kusto/query/) Als u een query wilt uitvoeren op al uw Log Analytics-logboeken van een gecentraliseerde client, raadpleegt [u Azure Data Explorer](https://docs.microsoft.com/azure/data-explorer/query-monitor-data).

### <a name="use-your-storage-account"></a>Uw opslagaccount gebruiken

1. Zoek in Azure Portal **opslagaccounts** in het linkernavigatievenster of in het zoekvak.
1. Selecteer het opslagaccount dat u hebt gekozen toen u de diagnostische instellingen hebt toegevoegd.
1. Selecteer Blobs om het **deelvenster Blobcontainer** **te openen.**
1. Als u toepassingslogboeken wilt bekijken, zoekt u naar een container met de naam **insights-logs-applicationconsole.**
1. Als u metrische gegevens van toepassingen wilt controleren, zoekt u naar een container met de naam **insights-metrics-pt1m**.

Zie Diagnostische gegevens opslaan en weergeven in een opslagaccount voor meer informatie over het verzenden van diagnostische gegevens [Azure Storage](../storage/common/storage-introduction.md).

### <a name="use-your-event-hub"></a>Uw Event Hub gebruiken

1. Zoek in Azure Portal **de** Event Hubs in het linkernavigatievenster of in het zoekvak.

1. Zoek en selecteer de Event Hub die u hebt gekozen toen u uw diagnostische instellingen hebt toegevoegd.
1. Als u het **deelvenster Event Hub-lijst wilt** openen, **selecteert u Event Hubs**.
1. Als u toepassingslogboeken wilt bekijken, zoekt u naar een Event Hub met de naam **insights-logs-applicationconsole.**
1. Als u metrische gegevens van toepassingen wilt controleren, zoekt u naar een Event Hub met de naam **insights-metrics-pt1m.**

Zie Streaming Azure Diagnostics data in the hot [path by using](../azure-monitor/agents/diagnostics-extension-stream-event-hubs.md)Event Hubs voor meer informatie over het verzenden van diagnostische gegevens naar een Event Hub.

## <a name="analyze-the-logs"></a>De logboeken analyseren

Azure Log Analytics wordt uitgevoerd met een Kusto-engine, zodat u uw logboeken kunt opvragen voor analyse. Lees de Log Analytics-zelfstudie voor een korte inleiding tot het uitvoeren van query's op [logboeken](../azure-monitor/logs/log-analytics-tutorial.md)met behulp van Kusto.

Toepassingslogboeken bevatten essentiële informatie en uitgebreide logboeken over de status, prestaties en meer van uw toepassing. In de volgende secties vindt u enkele eenvoudige query's om inzicht te krijgen in de huidige en eerdere staten van uw toepassing.

### <a name="show-application-logs-from-azure-spring-cloud"></a>Toepassingslogboeken van Azure Spring Cloud

Als u een lijst met toepassingslogboeken van Azure Spring Cloud, gesorteerd op tijd met de meest recente logboeken die eerst worden weergegeven, moet u de volgende query uitvoeren:

```sql
AppPlatformLogsforSpring
| project TimeGenerated , ServiceName , AppName , InstanceName , Log
| sort by TimeGenerated desc
```

### <a name="show-logs-entries-containing-errors-or-exceptions"></a>Logboeken met fouten of uitzonderingen tonen

Voer de volgende query uit om niet-afgeschreven logboekgegevens te controleren die een fout of uitzondering vermelden:

```sql
AppPlatformLogsforSpring
| project TimeGenerated , ServiceName , AppName , InstanceName , Log
| where Log contains "error" or Log contains "exception"
```

Gebruik deze query om fouten te vinden of wijzig de querytermen om specifieke foutcodes of uitzonderingen te vinden.

### <a name="show-the-number-of-errors-and-exceptions-reported-by-your-application-over-the-last-hour"></a>Het aantal fouten en uitzonderingen laten zien dat het afgelopen uur door uw toepassing is gerapporteerd

Als u een cirkeldiagram wilt maken waarin het aantal fouten en uitzonderingen wordt weergegeven dat het afgelopen uur door uw toepassing is geregistreerd, moet u de volgende query uitvoeren:

```sql
AppPlatformLogsforSpring
| where TimeGenerated > ago(1h)
| where Log contains "error" or Log contains "exception"
| summarize count_per_app = count() by AppName
| sort by count_per_app desc
| render piechart
```

### <a name="learn-more-about-querying-application-logs"></a>Meer informatie over het uitvoeren van query's op toepassingslogboeken

Azure Monitor biedt uitgebreide ondersteuning voor het uitvoeren van query's op toepassingslogboeken met behulp van Log Analytics. Zie Aan de slag met logboekquery's in Azure Monitor voor meer informatie [over deze service.](../azure-monitor/logs/get-started-queries.md) Zie Overzicht van logboekquery's in Azure Monitor voor meer informatie over het bouwen [van query's voor het analyseren van uw toepassingslogboeken.](../azure-monitor/logs/log-query-overview.md)

## <a name="frequently-asked-questions-faq"></a>Veelgestelde vragen

### <a name="how-to-convert-multi-line-java-stack-traces-into-a-single-line"></a>Hoe converteert u Java-stack-traceringen van meerdere lijnen naar één regel?

Er is een tijdelijke oplossing om uw stack-traceringen van meerdere lijnen te converteren naar één regel. U kunt de java-logboekuitvoer wijzigen om stack-traceerberichten opnieuw op te maken, zodat nieuwe-lijntekens worden vervangen door een token. Als u de Java Logback-bibliotheek gebruikt, kunt u stack-traceerberichten opnieuw inmaken door het `%replace(%ex){'[\r\n]+', '\\n'}%nopex` volgende toe te voegen:

```xml
<configuration>
    <appender name="CONSOLE" class="ch.qos.logback.core.ConsoleAppender">
        <encoder>
            <pattern>
                level: %level, message: "%logger{36}: %msg", exceptions: "%replace(%ex){'[\r\n]+', '\\n'}%nopex"%n
            </pattern>
        </encoder>
    </appender>
    <root level="INFO">
        <appender-ref ref="CONSOLE"/>
    </root>
</configuration>
```
Vervolgens kunt u het token opnieuw vervangen door nieuwe-lijntekens in Log Analytics, zoals hieronder wordt weergegeven:

```sql
AppPlatformLogsforSpring
| extend Log = array_strcat(split(Log, '\\n'), '\n')
```
Mogelijk kunt u dezelfde strategie gebruiken voor andere Java-logboekbibliotheken.

## <a name="next-steps"></a>Volgende stappen

* [Snelstart: Uw eerste Azure Spring Cloud-toepassing implementeren](spring-cloud-quickstart.md)
