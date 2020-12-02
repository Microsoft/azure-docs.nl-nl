---
title: Logboeken en metrische gegevens analyseren in azure lente-Cloud | Microsoft Docs
description: Meer informatie over het analyseren van diagnostische gegevens in azure lente Cloud
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 01/06/2020
ms.author: brendm
ms.custom: devx-track-java
ms.openlocfilehash: 11f5fcd74b228fa2d57658f5c268e3bebc3c7e93
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/02/2020
ms.locfileid: "96499523"
---
# <a name="analyze-logs-and-metrics-with-diagnostics-settings"></a>Logboeken en metrische gegevens analyseren met Diagnostische instellingen

**Dit artikel is van toepassing op:** ✔️ Java ✔️ C#

Met de diagnostische functionaliteit van Azure lente-Cloud kunt u Logboeken en metrische gegevens analyseren met een van de volgende services:

* Gebruik Azure Log Analytics, waarbij de gegevens naar Azure Storage worden geschreven. Er is een vertraging bij het exporteren van logboeken naar Log Analytics.
* Sla logboeken op in een opslag account voor controle of hand matige inspectie. U kunt de Bewaar periode (in dagen) opgeven.
* Stream-logboeken naar uw Event Hub voor opname door een service van derden of een aangepaste analyse oplossing.

Kies de categorie van het logboek en de metrische gegevens die u wilt bewaken.

> [!TIP]
> Wilt u uw logboeken gewoon streamen? Bekijk deze [Azure cli-opdracht](/cli/azure/ext/spring-cloud/spring-cloud/app?preserve-view=true&view=azure-cli-latest#ext-spring-cloud-az-spring-cloud-app-logs).

## <a name="logs"></a>Logboeken

|Logboek | Beschrijving |
|----|----|
| **ApplicationConsole** | Console logboek van alle toepassingen van klanten. |
| **SystemLogs** | Op dit moment worden alleen logboeken met [lente-Cloud configuratie servers](https://cloud.spring.io/spring-cloud-config/reference/html/#_spring_cloud_config_server) in deze categorie vastgelegd. |

## <a name="metrics"></a>Metrische gegevens

Zie voor een volledige lijst met metrische gegevens [lente-Cloud metrieken](./spring-cloud-concept-metrics.md#user-metrics-options).

Als u aan de slag wilt gaan, schakelt u een van deze services in om de gegevens te ontvangen. Zie aan de [slag met log Analytics in azure monitor](../azure-monitor/log-query/log-analytics-tutorial.md)voor meer informatie over het configureren van log Analytics.

## <a name="configure-diagnostics-settings"></a>Diagnostische instellingen configureren

1. Ga in het Azure Portal naar uw Azure veer Cloud-exemplaar.
1. Selecteer de optie **Diagnostische instellingen** en selecteer vervolgens **Diagnostische gegevens toevoegen**.
1. Voer een naam in voor de instelling en kies vervolgens waar u de logboeken wilt verzenden. U kunt een combi natie van de volgende drie opties selecteren:
    * **Archiveren naar een opslag account**
    * **Streamen naar een Event Hub**
    * **Verzenden naar Log Analytics**

1. Kies welke logboek categorie en metrische categorie u wilt bewaken en geef vervolgens de Bewaar tijd op (in dagen). De Bewaar tijd is alleen van toepassing op het opslag account.
1. Selecteer **Opslaan**.

> [!NOTE]
> 1. Er kan een onderbreking van Maxi maal 15 minuten zijn tussen het moment dat Logboeken of metrische gegevens worden verzonden en wanneer ze worden weer gegeven in uw opslag account, uw Event Hub of Log Analytics.
> 1. Als het Azure veer Cloud-exemplaar wordt verwijderd of verplaatst, worden de resources met **Diagnostische instellingen** niet gecascaded. De resources voor **Diagnostische instellingen** moeten hand matig worden verwijderd voordat de bewerking wordt uitgevoerd op het bovenliggende item, D.w.z. het Azure veer Cloud-exemplaar. Als er anders een nieuwe Azure-Cloud instantie wordt ingericht met dezelfde resource-ID als de verwijderde, of als het Azure veer Cloud-exemplaar weer wordt verplaatst, worden de eerdere bronnen voor **Diagnostische instellingen** door lopen.

## <a name="view-the-logs-and-metrics"></a>De logboeken en metrische gegevens weer geven
Er zijn verschillende methoden om logboeken en metrische gegevens weer te geven, zoals wordt beschreven in de volgende koppen.

### <a name="use-the-logs-blade"></a>De Blade Logboeken gebruiken

1. Ga in het Azure Portal naar uw Azure veer Cloud-exemplaar.
1. Selecteer **Logboeken** om het deel venster **Zoeken in Logboeken** te openen.
1. In het zoekvak **tabellen**
   * Als u logboeken wilt weer geven, voert u een eenvoudige query in, zoals:

    ```sql
    AppPlatformLogsforSpring
    | limit 50
    ```
   * Als u metrische gegevens wilt weer geven, voert u een eenvoudige query in, zoals:

    ```sql
    AzureMetrics
    | limit 50
    ```
1. Als u het Zoek resultaat wilt weer geven, selecteert u **uitvoeren**.

### <a name="use-log-analytics"></a>Log Analytics gebruiken

1. Selecteer **log Analytics** in het linkerdeel venster van de Azure Portal.
1. Selecteer de Log Analytics werk ruimte die u hebt gekozen toen u de diagnostische instellingen hebt toegevoegd.
1. Selecteer **Logboeken** om het deel venster **Zoeken in Logboeken** te openen.
1. In het zoekvak **tabellen** ,
   * Als u logboeken wilt weer geven, voert u een eenvoudige query in, zoals:

    ```sql
    AppPlatformLogsforSpring
    | limit 50
    ```
    * Als u metrische gegevens wilt weer geven, voert u een eenvoudige query in, zoals:

    ```sql
    AzureMetrics
    | limit 50
    ```

1. Als u het Zoek resultaat wilt weer geven, selecteert u **uitvoeren**.
1. U kunt de logboeken van de specifieke toepassing of het exemplaar doorzoeken door een filter voorwaarde in te stellen:

    ```sql
    AppPlatformLogsforSpring
    | where ServiceName == "YourServiceName" and AppName == "YourAppName" and InstanceName == "YourInstanceName"
    | limit 50
    ```
> [!NOTE]
> `==` is hoofdletter gevoelig, maar `=~` niet.

Zie [Azure monitor-logboek query's](/azure/data-explorer/kusto/query/)voor meer informatie over de query taal die wordt gebruikt in log Analytics.

### <a name="use-your-storage-account"></a>Uw opslag account gebruiken

1. Zoek in het Azure Portal **opslag accounts** in het navigatie deel venster links of in het zoekvak.
1. Selecteer het opslag account dat u hebt gekozen toen u de diagnostische instellingen hebt toegevoegd.
1. Als u het deel venster **BLOB container** wilt openen, selecteert u **blobs**.
1. Als u toepassings logboeken wilt bekijken, zoekt u naar een container met de naam **Insights-logs-applicationconsole**.
1. Als u metrische gegevens van de toepassing wilt controleren, zoekt u naar een container met de naam **Insights-metrische gegevens-pt1m**.

Zie voor meer informatie over het verzenden van diagnostische gegevens naar een opslag account [Diagnostische gegevens opslaan en weer geven in azure Storage](../storage/common/storage-introduction.md).

### <a name="use-your-event-hub"></a>Uw Event Hub gebruiken

1. Zoek in de Azure Portal **Event hubs** in het navigatie deel venster links of in het zoekvak.

1. Zoek en selecteer de Event Hub die u hebt gekozen toen u de diagnostische instellingen hebt toegevoegd.
1. Selecteer **Event hubs** om het deel venster **Event hub-lijst** te openen.
1. Als u toepassings logboeken wilt bekijken, zoekt u naar een Event Hub genaamd **Insights-logs-applicationconsole**.
1. Als u metrische gegevens van de toepassing wilt bekijken, zoekt u naar een Event Hub met de naam **Insights-metrische gegevens-pt1m**.

Voor meer informatie over het verzenden van diagnostische gegevens naar een Event Hub raadpleegt u [Azure Diagnostics gegevens streamen in het warme pad met behulp van Event hubs](../azure-monitor/platform/diagnostics-extension-stream-event-hubs.md).

## <a name="analyze-the-logs"></a>De logboeken analyseren

Azure Log Analytics wordt uitgevoerd met een Kusto-engine, zodat u een query kunt uitvoeren op uw logboeken voor analyse. Raadpleeg de [log Analytics zelf studie](../azure-monitor/log-query/log-analytics-tutorial.md)voor een snelle inleiding in het uitvoeren van query's op Logboeken met behulp van Kusto.

Toepassings logboeken bieden essentiële informatie en uitgebreide logboeken over de status, prestaties en meer van de toepassing. In de volgende secties vindt u enkele eenvoudige query's waarmee u inzicht krijgt in de huidige en eerdere status van uw toepassing.

### <a name="show-application-logs-from-azure-spring-cloud"></a>Toepassings logboeken van Azure lente-Cloud weer geven

Voer de volgende query uit om een lijst met toepassings logboeken te bekijken vanuit Azure lente-Cloud, gesorteerd op tijd met de meest recente logboeken die het eerst worden weer gegeven:

```sql
AppPlatformLogsforSpring
| project TimeGenerated , ServiceName , AppName , InstanceName , Log
| sort by TimeGenerated desc
```

### <a name="show-logs-entries-containing-errors-or-exceptions"></a>Logboek vermeldingen weer geven die fouten of uitzonde ringen bevatten

Voer de volgende query uit om ongesorteerde logboek vermeldingen te controleren waarin een fout of uitzonde ring wordt vermeld:

```sql
AppPlatformLogsforSpring
| project TimeGenerated , ServiceName , AppName , InstanceName , Log
| where Log contains "error" or Log contains "exception"
```

Gebruik deze query om fouten te vinden of wijzig de query termen om specifieke fout codes of uitzonde ringen op te sporen.

### <a name="show-the-number-of-errors-and-exceptions-reported-by-your-application-over-the-last-hour"></a>Het aantal fouten en uitzonde ringen weer geven dat in het afgelopen uur is gerapporteerd door uw toepassing

Voer de volgende query uit om een cirkel diagram te maken waarin het aantal fouten en uitzonde ringen wordt weer gegeven dat door uw toepassing in het afgelopen uur is geregistreerd:

```sql
AppPlatformLogsforSpring
| where TimeGenerated > ago(1h)
| where Log contains "error" or Log contains "exception"
| summarize count_per_app = count() by AppName
| sort by count_per_app desc
| render piechart
```

### <a name="learn-more-about-querying-application-logs"></a>Meer informatie over het uitvoeren van query's in toepassings logboeken

Azure Monitor biedt uitgebreide ondersteuning voor het uitvoeren van query's in toepassings logboeken met behulp van Log Analytics. Zie aan de [slag met logboek query's in azure monitor](../azure-monitor/log-query/get-started-queries.md)voor meer informatie over deze service. Zie [overzicht van logboek query's in azure monitor](../azure-monitor/log-query/log-query-overview.md)voor meer informatie over het bouwen van query's voor het analyseren van uw toepassings Logboeken.

## <a name="frequently-asked-questions-faq"></a>Veelgestelde vragen (FAQ's)

### <a name="how-to-convert-multi-line-java-stack-traces-into-a-single-line"></a>Hoe kan ik meerdere regels Java stack-traceringen omzetten in één regel?

Er is een tijdelijke oplossing voor het converteren van uw stack traceringen met meerdere regels naar één regel. U kunt de uitvoer van het Java-logboek wijzigen om stack-tracerings berichten opnieuw in te delen, waarbij de nieuwe regels opnieuw worden vervangen door een token. Als u de Java logback-bibliotheek gebruikt, kunt u stack trace-berichten opnieuw opmaken door het volgende toe te voegen `%replace(%ex){'[\r\n]+', '\\n'}%nopex` :

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
Daarna kunt u het token met nieuwe regels opnieuw in Log Analytics, zoals hieronder:

```sql
AppPlatformLogsforSpring
| extend Log = array_strcat(split(Log, '\\n'), '\n')
```
Mogelijk kunt u dezelfde strategie gebruiken voor andere Java-logboek bibliotheken.

## <a name="next-steps"></a>Volgende stappen

* [Snelstart: Uw eerste Azure Spring Cloud-toepassing implementeren](spring-cloud-quickstart.md)