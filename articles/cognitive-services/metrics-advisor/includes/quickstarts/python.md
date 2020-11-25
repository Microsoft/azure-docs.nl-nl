---
title: Python-quickstart voor Metrics Advisor
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: include
ms.date: 11/09/2020
ms.author: mbullwin
ms.openlocfilehash: 2c79773d6697ae9fb62e2b7515da60178243fe40
ms.sourcegitcommit: 4bee52a3601b226cfc4e6eac71c1cb3b4b0eafe2
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/11/2020
ms.locfileid: "94523939"
---
[Referentiedocumentatie](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-ai-metricsadvisor/latest/azure.ai.metricsadvisor.html) | [Broncode bibliotheek](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/metricsadvisor/azure-ai-metricsadvisor/README.md) | [Package (PiPy)](https://pypi.org/project/azure-ai-metricsadvisor/) | [Voorbeelden](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/metricsadvisor/azure-ai-metricsadvisor/samples/README.md)

## <a name="prerequisites"></a>Vereisten

* Azure-abonnement: [Krijg een gratis abonnement](https://azure.microsoft.com/free/cognitive-services/)
* [Python 3.x](https://www.python.org/)
* Wanneer u uw Azure-abonnement hebt, kunt u <a href="https://go.microsoft.com/fwlink/?linkid=2142156"  title="Een Metrics Advisor-resource maken"  target="_blank">een Metrics Advisor-resource maken<span class="docon docon-navigate-external x-hidden-focus"></span></a> in de Azure-portal om uw Metrics Advisor-instantie te implementeren.  
* Uw eigen SQL-database met tijdreeksgegevens.
  
> [!TIP]
> * Op [GitHub](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/metricsadvisor/azure-ai-metricsadvisor/samples) vindt u Python-voorbeelden voor Metrics Advisor.
> * Het kan 10 tot 30 minuten duren voordat uw Metrics Advisor-resource een service-instantie heeft geïmplementeerd die u kunt gebruiken. Klik op **Naar de resource gaan** zodra de implementatie is voltooid. Na de implementatie kunt u uw Metrics Advisor-instantie gebruiken met zowel de webportal als de REST API.
> * U kunt de URL voor de REST API in Azure Portal vinden in het gedeelte **Overzicht** van uw resource. Dit zal er als volgt uitzien:
>    * `https://<instance-name>.cognitiveservices.azure.com/` 
 
## <a name="setting-up"></a>Instellen

### <a name="install-the-client-library"></a>De clientbibliotheek installeren

Na de installatie van Python kunt u de clientbibliotheek installeren met:

```console
pip install azure-ai-metricsadvisor --pre
```

### <a name="create-a-new-python-application"></a>Een nieuwe Python-toepassing maken

Maak een nieuw Python-bestand en importeer de volgende bibliotheken.

```python
import os
import datetime
```

Maak variabelen voor het Azure-eindpunt en de Azure-sleutel voor uw resource.

> [!IMPORTANT]
> Ga naar Azure Portal. Als de Metrics Advisor-resource die u in de sectie **Vereisten** hebt gemaakt, succesvol is geïmplementeerd, klikt u op de knop **Ga naar resource** onder **Volgende stappen**. U vindt uw abonnementssleutels en eindpunt op de pagina **Sleutel en eindpunt** van de resource, onder **Resourcebeheer**. <br><br>Als u uw API-sleutel wilt ophalen, moet u naar [https://metricsadvisor.azurewebsites.net](https://metricsadvisor.azurewebsites.net) gaan. Selecteer de gewenste optie: **Map**, **Abonnementen** en **Werkruimte** voor uw resource en kies **Aan de slag**. U kunt de API-sleutels vervolgens ophalen van [https://metricsadvisor.azurewebsites.net/api-key](https://metricsadvisor.azurewebsites.net/api-key).   
>
> Vergeet niet de sleutel uit uw code te verwijderen wanneer u klaar bent, en plaats deze sleutel nooit in het openbaar. Overweeg om voor productie een veilige manier te gebruiken voor het opslaan en openen van uw referenties. Zie het artikel Cognitive Services [Beveiliging](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-security) voor meer informatie.

```python
subscription_key = "<paste-your-metrics-advisor-subscription-key-here>"
api_key = "<paste-your-metrics-advisor-api-key-here>"
service_endpoint = "<paste-your-metrics-advisor-endpoint-here>"
```

## <a name="object-model"></a>Objectmodel

De volgende klassen worden gebruikt voor enkele van de belangrijkste functies van de Metrics Advisor Python-SDK.

|Naam|Beschrijving|
|---|---|
| [MetricsAdvisorClient](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-ai-metricsadvisor/latest/azure.ai.metricsadvisor.html#azure.ai.metricsadvisor.MetricsAdvisorClient) | **Gebruikt voor**: <br> - het weergeven van incidenten <br> - het weergeven van hoofdoorzaken van incidenten <br> - het ophalen van de oorspronkelijke tijdreeksgegevens en tijdreeksgegevens die door de service zijn verrijkt <br> - het weergeven van waarschuwingen <br> - het toevoegen van feedback om uw model af te stemmen |
| [MetricsAdvisorAdministrationClient](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-ai-metricsadvisor/latest/azure.ai.metricsadvisor.html#azure.ai.metricsadvisor.MetricsAdvisorAdministrationClient) | **Hiermee kunt u:** <br> - gegevensfeeds beheren <br> - configuraties voor anomaliedetectie maken, configureren, ophalen, weergeven en verwijderen <br> - configuraties voor anomaliewaarschuwingen maken, configureren, ophalen, weergeven en verwijderen <br> - hooks beheren  | |
| [DataFeed](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-ai-metricsadvisor/latest/azure.ai.metricsadvisor.models.html?highlight=datafeed#azure.ai.metricsadvisor.models.DataFeed)| **Wat Metrics Advisor van uw gegevensbron opneemt. Een `DataFeed` bevat rijen van:** <br> - tijdstempels <br> - nul of meer dimensies <br> - één of meer metingen  |
| [DataFeedMetric](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-ai-metricsadvisor/latest/azure.ai.metricsadvisor.models.html?highlight=datafeedmetric#azure.ai.metricsadvisor.models.DataFeedMetric) | Een `DataFeedMetric` is een kwantificeerbaar metrisch gegeven dat wordt gebruikt om de status van een specifiek bedrijfsproces te controleren en te beoordelen. Dit kan een combinatie zijn van meerdere tijdreekswaarden, onderverdeeld in dimensies. Een metrisch gegeven dat de status van het web aangeeft, kan bijvoorbeeld dimensies bevatten voor het aantal gebruikers en voor het en-us-taalgebied. |

## <a name="code-examples"></a>Codevoorbeelden

Deze codefragmenten laten zien hoe u de volgende taken kunt uitvoeren met de Metrics Advisor-clientbibliotheek voor Python:

* [De client verifiëren](#authenticate-the-client)
* [Een gegevensfeed toevoegen](#add-a-data-feed)
* [Opnamestatus controleren](#check-the-ingestion-status)
* [Detectieconfiguratie en waarschuwingsconfiguratie instellen](#configure-anomaly-detection)
* [Een waarschuwingsconfiguratie maken](#create-an-alert-configuration)
* [Query's uitvoeren op de anomaliedetectieresultaten](#query-the-alert)

## <a name="authenticate-the-client"></a>De client verifiëren

De client in dit voorbeeld is een `MetricsAdvisorAdministrationClient`-object dat gebruikmaakt van uw eindpunt en een `MetricsAdvisorKeyCredential`-object dat uw sleutels bevat. U hoeft dit codevoorbeeld niet te kopiëren. De-methoden die u later maakt, maken een instantie van de client. De alternatieve client heet `MetricsAdvisorClient`. Meer informatie over deze client vindt u in de [referentiedocumentatie](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-ai-metricsadvisor/latest/azure.ai.metricsadvisor.html#azure.ai.metricsadvisor.MetricsAdvisorClient).

```python
client = MetricsAdvisorAdministrationClient(service_endpoint,
                                MetricsAdvisorKeyCredential(subscription_key, api_key))
```

## <a name="add-a-data-feed"></a>Een gegevensfeed toevoegen

In een nieuwe methode maakt u importinstructies, zoals in het voorbeeld hieronder. Vervang `sql_server_connection_string` door uw eigen SQL Server-verbindingsreeks en vervang `query` door een query die uw gegevens voor één enkele tijdstempel retourneert. U moet ook de waarden `DataFeedmetric` en `DataFeedDimension` aanpassen op basis van uw aangepaste gegevens.

> [!IMPORTANT]
> Met de query wordt maximaal één record per dimensiecombinatie geretourneerd voor elke tijdstempel. En alle records die zijn geretourneerd met de query, moeten dezelfde tijdstempels hebben. In Metrics Advisor wordt deze query uitgevoerd voor elke tijdstempel om uw gegevens op te nemen. Raadpleeg de sectie [Veelgestelde vragen over query's](../../faq.md#how-do-i-write-a-valid-query-for-ingesting-my-data) voor meer informatie en voorbeelden. 

Maak een client met uw sleutels en eindpunt, en gebruik `client.create_data_feed()` om de naam, de bron, de granulariteit en het schema te configureren. U kunt ook de tijd van opname, instellingen voor samentellen en meer instellen.


```python
def sample_create_data_feed():
    from azure.ai.metricsadvisor import MetricsAdvisorKeyCredential, MetricsAdvisorAdministrationClient
    from azure.ai.metricsadvisor.models import (
        SQLServerDataFeed,
        DataFeedSchema,
        DataFeedMetric,
        DataFeedDimension,
        DataFeedOptions,
        DataFeedRollupSettings
    )
    sql_server_connection_string = "<replace-with-your-sql-server-connection-string>"
    query = "<replace-with-metrics-advisor-sql-server-query>"

    client = MetricsAdvisorAdministrationClient(service_endpoint,
                                  MetricsAdvisorKeyCredential(subscription_key, api_key))

    data_feed = client.create_data_feed(
    name="My data feed",
    source=SQLServerDataFeed(
        connection_string=sql_server_connection_string,
        query=query,
    ),
    granularity="Daily",
    schema=DataFeedSchema(
        metrics=[
            DataFeedMetric(name="cost", display_name="Cost"),
            DataFeedMetric(name="revenue", display_name="Revenue")
        ],
        dimensions=[
            DataFeedDimension(name="category", display_name="Category"),
            DataFeedDimension(name="city", display_name="City")
        ],
        timestamp_column="Timestamp"
    ),
    ingestion_settings=datetime.datetime(2019, 10, 1),
    options=DataFeedOptions(
        data_feed_description="cost/revenue data feed",
        rollup_settings=DataFeedRollupSettings(
            rollup_type="AutoRollup",
            rollup_method="Sum",
            rollup_identification_value="__CUSTOM_SUM__"
        ),
        missing_data_point_fill_settings=DataFeedMissingDataPointFillSettings(
            fill_type="SmartFilling"
        ),
        access_mode="Private"
    )
)

return data_feed
sample_create_data_feed()
```

## <a name="check-the-ingestion-status"></a>De opnamestatus controleren

In een nieuwe methode maakt u importinstructies, zoals in het voorbeeld hieronder. Vervang `data_feed_id` door de id voor de gegevensfeed die u hebt gemaakt. Maak een client met uw sleutels en eindpunt en gebruik `client.list_data_feed_ingestion_status()` om de voortgang van de opname op te halen. Geef de details weer, zoals de laatste actieve en geslaagde tijdstempels.


```python
    from azure.ai.metricsadvisor import MetricsAdvisorKeyCredential, MetricsAdvisorAdministrationClient

    data_feed_id = "<replace-with-your-metrics-advisor-data-feed-id>"

   client = MetricsAdvisorAdministrationClient(service_endpoint,
    MetricsAdvisorKeyCredential(subscription_key, api_key)
)

ingestion_status = client.list_data_feed_ingestion_status(
    data_feed_id,
    datetime.datetime(2020, 9, 20),
    datetime.datetime(2020, 9, 25)
)
for status in ingestion_status:
    print("Timestamp: {}".format(status.timestamp))
    print("Status: {}".format(status.status))
    print("Message: {}\n".format(status.message))

```

## <a name="configure-anomaly-detection"></a>Anomaliedetectie configureren

In een nieuwe methode maakt u importinstructies, zoals in het voorbeeld hieronder. Vervang `metric_id` door de id voor het metrische gegeven dat u wilt configureren. Maak een client met uw sleutels en eindpunt en gebruik `client.create_detection_configuration` om een nieuwe detectieconfiguratie te maken. Met de drempelvoorwaarden worden de parameters voor anomaliedetectie opgegeven.

```python
    from azure.ai.metricsadvisor import MetricsAdvisorKeyCredential, MetricsAdvisorAdministrationClient
    from azure.ai.metricsadvisor.models import (
        ChangeThresholdCondition,
        HardThresholdCondition,
        SmartDetectionCondition,
        SuppressCondition,
        MetricDetectionCondition,
    )
    metric_id = "replace-with-your-metric-id"

    
client = MetricsAdvisorAdministrationClient(
    service_endpoint,
    MetricsAdvisorKeyCredential(subscription_key, api_key)
)

change_threshold_condition = ChangeThresholdCondition(
    anomaly_detector_direction="Both",
    change_percentage=20,
    shift_point=10,
    within_range=True,
    suppress_condition=SuppressCondition(
        min_number=5,
        min_ratio=2
    )
)
hard_threshold_condition = HardThresholdCondition(
    anomaly_detector_direction="Up",
    upper_bound=100,
    suppress_condition=SuppressCondition(
        min_number=2,
        min_ratio=2
    )
)
smart_detection_condition = SmartDetectionCondition(
    anomaly_detector_direction="Up",
    sensitivity=10,
    suppress_condition=SuppressCondition(
        min_number=2,
        min_ratio=2
    )
)

detection_config = client.create_detection_configuration(
    name="my_detection_config",
    metric_id=metric_id,
    description="anomaly detection config for metric",
    whole_series_detection_condition=MetricDetectionCondition(
        cross_conditions_operator="OR",
        change_threshold_condition=change_threshold_condition,
        hard_threshold_condition=hard_threshold_condition,
        smart_detection_condition=smart_detection_condition
    )
)
return detection_config
```

## <a name="create-a-hook"></a>Een hook maken

In een nieuwe methode maakt u importinstructies, zoals in het voorbeeld hieronder. Maak een client met uw sleutels en eindpunt en gebruik `client.create_hook()` om een hook te maken. Voer een beschrijving in, een lijst met e-mailadressen waarnaar de waarschuwing moet worden verzonden en een externe koppeling om de waarschuwing mee te kunnen ontvangen.  

```python
def sample_create_hook():

    from azure.ai.metricsadvisor import MetricsAdvisorKeyCredential, MetricsAdvisorAdministrationClient
    from azure.ai.metricsadvisor.models import EmailNotificationHook

    client = MetricsAdvisorAdministrationClient(service_endpoint,
                                  MetricsAdvisorKeyCredential(subscription_key, api_key))

client = MetricsAdvisorAdministrationClient(service_endpoint,
    MetricsAdvisorKeyCredential(subscription_key, api_key))

hook = client.create_hook(
    hook=EmailNotificationHook(
        name="email hook",
        description="my email hook",
        emails_to_alert=["alertme@alertme.com"],
        external_link="https://example.com/handleAlerts", # you must enter a valid webhook url to post the alert payload
    )
)
```

##  <a name="create-an-alert-configuration"></a>Een waarschuwingsconfiguratie maken

In een nieuwe methode maakt u importinstructies, zoals in het voorbeeld hieronder. Vervang `detection_configuration_id` door de ID van uw configuratie van de anomaliedetectie en vervang `hook_id` door de hook die u eerder hebt gemaakt. Maak een client met uw sleutels en eindpunt, en gebruik `client.create_alert_configuration()` om een nieuwe waarschuwingsconfiguratie te maken. 

```python
def sample_create_alert_config():
    from azure.ai.metricsadvisor import MetricsAdvisorKeyCredential, MetricsAdvisorAdministrationClient
    from azure.ai.metricsadvisor.models import (
        MetricAlertConfiguration,
        MetricAnomalyAlertScope,
        TopNGroupScope,
        MetricAnomalyAlertConditions,
        SeverityCondition,
        MetricBoundaryCondition,
        MetricAnomalyAlertSnoozeCondition
    )
    anomaly_detection_configuration_id = "<replace-with-your-detection-configuration-id"
    hook_id = "<replace-with-your-hook-id>"

    client = MetricsAdvisorAdministrationClient(
    service_endpoint,
    MetricsAdvisorKeyCredential(subscription_key, api_key)
)

alert_config = client.create_alert_configuration(
        name="my alert config",
        description="alert config description",
        cross_metrics_operator="AND",
        metric_alert_configurations=[
            MetricAlertConfiguration(
                detection_configuration_id=detection_configuration_id,
                alert_scope=MetricAnomalyAlertScope(
                    scope_type="WholeSeries"
                ),
                alert_conditions=MetricAnomalyAlertConditions(
                    severity_condition=SeverityCondition(
                        min_alert_severity="Low",
                        max_alert_severity="High"
                    )
                )
            ),
            MetricAlertConfiguration(
                detection_configuration_id=detection_configuration_id,
                alert_scope=MetricAnomalyAlertScope(
                    scope_type="TopN",
                    top_n_group_in_scope=TopNGroupScope(
                        top=10,
                        period=5,
                        min_top_count=5
                    )
                ),
                alert_conditions=MetricAnomalyAlertConditions(
                    metric_boundary_condition=MetricBoundaryCondition(
                        direction="Up",
                        upper=50
                    )
                ),
                alert_snooze_condition=MetricAnomalyAlertSnoozeCondition(
                    auto_snooze=2,
                    snooze_scope="Metric",
                    only_for_successive=True
                )
            ),
        ],
        hook_ids=[hook_id]
    )

    return alert_config
```

### <a name="query-the-alert"></a>De waarschuwing opvragen

In een nieuwe methode maakt u importinstructies, zoals in het voorbeeld hieronder. Vervang `alert_id` door de id van uw waarschuwing en vervang `alert_config_id` door de id van de waarschuwingsconfiguratie. Maak een client met uw sleutels en eindpunt, en gebruik `client.list_anomalies` om de anomalieën voor een waarschuwing weer te geven. 

```python
from azure.ai.metricsadvisor import MetricsAdvisorKeyCredential, MetricsAdvisorClient
    
alert_id = "<replace-with-your-alert-id>"
alert_config_id = "<replace-with-your-alert-configuration-id"

client = MetricsAdvisorClient(service_endpoint,
    MetricsAdvisorKeyCredential(subscription_key, api_key)
)

results = client.list_alerts(
    alert_configuration_id=alert_config_id,
    start_time=datetime.datetime(2020, 1, 1),
    end_time=datetime.datetime(2020, 9, 9),
    time_mode="AnomalyTime",
)
for result in results:
    print("Alert id: {}".format(result.id))
    print("Create on: {}".format(result.created_on))

results = client.list_anomalies(
    alert_configuration_id=alert_config_id,
    alert_id=alert_id,
)
for result in results:
    print("Create on: {}".format(result.created_on))
    print("Severity: {}".format(result.severity))
    print("Status: {}".format(result.status))
```

### <a name="run-the-application"></a>De toepassing uitvoeren

Voer de toepassing uit met de opdracht `python` in uw quickstart-bestand.

```console
python quickstart-file.py
```