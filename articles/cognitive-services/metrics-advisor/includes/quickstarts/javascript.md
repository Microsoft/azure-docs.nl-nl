---
title: JavaScript-quickstart voor Metrics Advisor
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: include
ms.date: 11/09/2020
ms.author: mbullwin
ms.openlocfilehash: 121f6a876507b84de7a4613fe2158c1953e2d250
ms.sourcegitcommit: 4bee52a3601b226cfc4e6eac71c1cb3b4b0eafe2
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/11/2020
ms.locfileid: "94523922"
---
[Referentiedocumentatie](https://docs.microsoft.com/javascript/api/overview/azure/ai-metrics-advisor-readme-pre?view=azure-node-preview&preserve-view=true) | [Bibliotheekbroncode](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/metricsadvisor/ai-metrics-advisor/README.md) | [Pakket (npm)](https://www.npmjs.com/package/@azure/ai-metrics-advisor) | [Voorbeelden](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/metricsadvisor/ai-metrics-advisor/samples)

## <a name="prerequisites"></a>Vereisten

* Azure-abonnement: [Krijg een gratis abonnement](https://azure.microsoft.com/free/cognitive-services/)
* De huidige versie van [Node.js](https://nodejs.org/)
* Wanneer u uw Azure-abonnement hebt, kunt u <a href="https://go.microsoft.com/fwlink/?linkid=2142156"  title="Een Metrics Advisor-resource maken"  target="_blank">een Metrics Advisor-resource maken<span class="docon docon-navigate-external x-hidden-focus"></span></a> in de Azure-portal om uw Metrics Advisor-instantie te implementeren.  
* Uw eigen SQL-database met tijdreeksgegevens.
  
> [!TIP]
> * Op [GitHub](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/metricsadvisor/ai-metrics-advisor/samples) vindt u JavaScript-voorbeelden voor Metrics Advisor.
> * Het kan 10 tot 30 minuten duren voordat uw Metrics Advisor-resource een service-instantie heeft geïmplementeerd die u kunt gebruiken. Klik op **Naar de resource gaan** zodra de implementatie is voltooid. Na de implementatie kunt u uw Metrics Advisor-instantie gebruiken met zowel de webportal als de REST API. 
> * U kunt de URL voor de REST API in Azure Portal vinden in het gedeelte **Overzicht** van uw resource. Dit zal er als volgt uitzien:
>    * `https://<instance-name>.cognitiveservices.azure.com/`
    
## <a name="setting-up"></a>Instellen

### <a name="create-a-new-nodejs-application"></a>Een nieuwe Node.js-toepassing maken

Maak in een consolevenster (zoals cmd, PowerShell of Bash) een nieuwe map voor de app, en navigeer naar deze map. 

```console
mkdir myapp && cd myapp
```

Voer de opdracht `npm init` uit om een knooppunttoepassing te maken met een `package.json`-bestand. 

```console
npm init
```

### <a name="install-the-client-library"></a>De clientbibliotheek installeren

Installeer het NPM-pakket `@azure/ai-metrics-advisor`:

```console
npm install @azure/ai-metrics-advisor@1.0.0-beta.2
```

Het `package.json`-bestand van uw app wordt bijgewerkt met de afhankelijkheden.

Maak een bestand met de naam `index.js` en importeer de volgende bibliotheken:

> [!TIP]
> Wilt u het codebestand voor de quickstart in één keer weergeven? Die is te vinden op [GitHub](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/metricsadvisor/ai-metrics-advisor/samples/javascript), waar de codevoorbeelden uit deze quickstart zich bevinden.

Maak variabelen voor het Azure-eindpunt en de Azure-sleutel voor uw resource. 

> [!IMPORTANT]
> Ga naar Azure Portal. Als de Metrics Advisor-resource die u in de sectie **Vereisten** hebt gemaakt, succesvol is geïmplementeerd, klikt u op de knop **Ga naar resource** onder **Volgende stappen**. U vindt uw abonnementssleutels en eindpunt op de pagina **Sleutel en eindpunt** van de resource, onder **Resourcebeheer**. <br><br>Als u uw API-sleutel wilt ophalen, moet u naar [https://metricsadvisor.azurewebsites.net](https://metricsadvisor.azurewebsites.net) gaan. Selecteer de gewenste optie: **Map**, **Abonnementen** en **Werkruimte** voor uw resource en kies **Aan de slag**. U kunt de API-sleutels vervolgens ophalen van [https://metricsadvisor.azurewebsites.net/api-key](https://metricsadvisor.azurewebsites.net/api-key).   
>
> Vergeet niet de sleutel uit uw code te verwijderen wanneer u klaar bent, en plaats deze sleutel nooit in het openbaar. Overweeg om voor productie een veilige manier te gebruiken voor het opslaan en openen van uw referenties. Zie het artikel Cognitive Services [Beveiliging](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-security) voor meer informatie.

```javascript
subscriptionKey = "<paste-your-metrics-advisor-key-here>";
apiKey ="<paste-your-metrics-advisor-api-key-here>";
endpoint = "<paste-your-metrics-advisor-endpoint-here>";
```

## <a name="object-model"></a>Objectmodel

De volgende klassen en interfaces verwerken enkele van de belangrijkste functies van de Metrics Advisor JavaScript-SDK.

|Naam|Beschrijving|
|---|---|
| MetricsAdvisorClient | **Gebruikt voor**: <br> - het weergeven van incidenten <br> - het weergeven van hoofdoorzaken van incidenten <br> - het ophalen van de oorspronkelijke tijdreeksgegevens en tijdreeksgegevens die door de service zijn verrijkt <br> - het weergeven van waarschuwingen <br> - het toevoegen van feedback om uw model af te stemmen |
| MetricsAdvisorAdministrationClient | **Hiermee kunt u:** <br> - gegevensfeeds beheren <br> - configuraties voor anomaliewaarschuwingen maken, configureren, ophalen, weergeven en verwijderen <br> - hooks beheren  |
| DataFeed | **Wat Metrics Advisor van uw gegevensbron opneemt. Een `DataFeed` bevat rijen van:** <br> - tijdstempels <br> - nul of meer dimensies <br> - één of meer metingen  |
| DataFeedMetric | Een `DataFeedMetric` is een kwantificeerbaar metrisch gegeven dat wordt gebruikt om de status van een specifiek bedrijfsproces te controleren en te beoordelen. Dit kan een combinatie zijn van meerdere tijdreekswaarden, onderverdeeld in dimensies. Een metrisch gegeven dat de status van het web aangeeft, kan bijvoorbeeld dimensies bevatten voor het aantal gebruikers en voor het en-us-taalgebied. |

## <a name="code-examples"></a>Codevoorbeelden

Deze codefragmenten laten zien hoe u het volgende kunt uitvoeren met de Metrics Advisor-clientbibliotheek voor JavaScript:

* [De client verifiëren](#authenticate-the-client)
* [Een gegevensfeed toevoegen](#add-a-data-feed)
* [Opnamestatus controleren](#check-ingestion-status)
* [Anomaliedetectie configureren](#configure-anomaly-detection)
* [Een hook maken](#create-a-hook)
* [Een waarschuwingsconfiguratie maken](#create-an-alert-configuration)
* [De waarschuwing opvragen](#query-the-alert)

## <a name="authenticate-the-client"></a>De client verifiëren

Zodra u de twee sleutels en het eindpuntadres hebt, kunt u de klasse MetricsAdvisorKeyCredential gebruiken om de clients als volgt te verifiëren:

```javascript
const {
  MetricsAdvisorKeyCredential,
  MetricsAdvisorClient,
  MetricsAdvisorAdministrationClient
} = require("@azure/ai-metrics-advisor");

const credential = new MetricsAdvisorKeyCredential(subscriptionKey, apiKey);
const client = new MetricsAdvisorClient(endpoint, credential);
const adminClient = new MetricsAdvisorAdministrationClient(endpoint, credential);
```

## <a name="add-a-data-feed"></a>Een gegevensfeed toevoegen

Metrics Advisor biedt ondersteuning voor het met elkaar verbinden van verschillende typen gegevensbronnen. Hier volgt een voorbeeld van het opnemen van gegevens uit SQL Server.

Vervang `sql_server_connection_string` door uw eigen SQL Server-verbindingsreeks en vervang `query` door een query die uw gegevens voor één enkele tijdstempel retourneert. U moet ook de waarden `metric` en `dimension` aanpassen op basis van uw aangepaste gegevens.

> [!IMPORTANT]
> Met de query wordt maximaal één record per dimensiecombinatie geretourneerd voor elke tijdstempel. En alle records die zijn geretourneerd met de query, moeten dezelfde tijdstempels hebben. In Metrics Advisor wordt deze query uitgevoerd voor elke tijdstempel om uw gegevens op te nemen. Raadpleeg de sectie [Veelgestelde vragen over query's](../../faq.md#how-do-i-write-a-valid-query-for-ingesting-my-data) voor meer informatie en voorbeelden. 

```javascript
const {
  MetricsAdvisorKeyCredential,
  MetricsAdvisorAdministrationClient
} = require("@azure/ai-metrics-advisor");

async function main() {
  subscriptionKey = "<paste-your-metrics-advisor-key-here>";
  apiKey ="<paste-your-metrics-advisor-api-key-here>";
  endpoint = "<paste-your-metrics-advisor-endpoint-here>";
  const sqlServerConnectionString ="<sql_server_connection_string>";
  const sqlServerQuery ="<query>";
  const credential = new MetricsAdvisorKeyCredential(subscriptionKey, apiKey);

  const adminClient = new MetricsAdvisorAdministrationClient(endpoint, credential);

  const created = await createDataFeed(adminClient, sqlServerConnectionString, sqlServerQuery);
  console.log(`Data feed created: ${created.id}`);
}

async function createDataFeed(adminClient, sqlServerConnectionString, sqlServerQuery) {
  console.log("Creating Datafeed...");
  const dataFeed = {
    name: "test_datafeed_" + new Date().getTime().toString(),
    source: {
      dataSourceType: "SqlServer",
      dataSourceParameter: {
        connectionString: sqlServerConnectionString,
        query: sqlServerQuery
      }
    },
    granularity: {
      granularityType: "Daily"
    },
    schema: {
      metrics: [
        {
          name: "revenue",
          displayName: "revenue",
          description: "Metric1 description"
        },
        {
          name: "cost",
          displayName: "cost",
          description: "Metric2 description"
        }
      ],
      dimensions: [
        { name: "city", displayName: "city display" },
        { name: "category", displayName: "category display" }
      ],
      timestampColumn: null
    },
    ingestionSettings: {
      ingestionStartTime: new Date(Date.UTC(2020, 5, 1)),
      ingestionStartOffsetInSeconds: 0,
      dataSourceRequestConcurrency: -1,
      ingestionRetryDelayInSeconds: -1,
      stopRetryAfterInSeconds: -1
    },
    rollupSettings: {
      rollupType: "AutoRollup",
      rollupMethod: "Sum",
      rollupIdentificationValue: "__CUSTOM_SUM__"
    },
    missingDataPointFillSettings: {
      fillType: "SmartFilling"
    },
    accessMode: "Private",
    adminEmails: ["xyz@example.com"]
  };
  const result = await adminClient.createDataFeed(dataFeed);

  return result;
}
```

## <a name="check-ingestion-status"></a>Opnamestatus controleren

Nadat de gegevensopname is gestart, kunt u de opnamestatus controleren.

```javascript
const {
  MetricsAdvisorKeyCredential,
  MetricsAdvisorAdministrationClient
} = require("@azure/ai-metrics-advisor");

async function main() {
  // You will need to set these environment variables or edit the following values
  const endpoint = "<service endpoint>";
  const subscriptionKey = "<subscription key>";
  const apiKey = "<api key>";
  const dataFeedId = "<data feed id>";
  const credential = new MetricsAdvisorKeyCredential(subscriptionKey, apiKey);

  const adminClient = new MetricsAdvisorAdministrationClient(endpoint, credential);
  await checkIngestionStatus(
    adminClient,
    dataFeedId,
    new Date(Date.UTC(2020, 8, 1)),
    new Date(Date.UTC(2020, 8, 12))
  );
}

async function checkIngestionStatus(adminClient, datafeedId, startTime, endTime) {
  // This shows how to use for-await-of syntax to list status
  console.log("Checking ingestion status...");
  const iterator = adminClient.listDataFeedIngestionStatus(datafeedId, startTime, endTime);
  for await (const status of iterator) {
    console.log(`  [${status.timestamp}] ${status.status} - ${status.message}`);
  }
}
```

## <a name="configure-anomaly-detection"></a>Anomaliedetectie configureren

Er is een anomaliedetectieconfiguratie vereist om te bepalen of een punt in de tijdreeks een anomalie is. Hoewel er op elk metrisch gegeven automatisch een standaard detectieconfiguratie wordt toegepast, kunt u de detectiemodi die op uw gegevens worden gebruikt, afstemmen door een aangepaste anomaliedetectieconfiguratie te maken.

```javascript
const {
  MetricsAdvisorKeyCredential,
  MetricsAdvisorAdministrationClient
} = require("@azure/ai-metrics-advisor");

async function main() {
  const endpoint = "<service endpoint>";
  const subscriptionKey = "<subscription key>";
  const apiKey = "<api key>";
  const metricId =  "<metric id>";
  const credential = new MetricsAdvisorKeyCredential(subscriptionKey, apiKey);

  const adminClient = new MetricsAdvisorAdministrationClient(endpoint, credential);

  const detectionConfig = await configureAnomalyDetectionConfiguration(adminClient, metricId);
  console.log(`Detection configuration created: ${detectionConfig.id}`);
}

async function configureAnomalyDetectionConfiguration(adminClient, metricId) {
  console.log(`Creating an anomaly detection configuration on metric '${metricId}'...`);
  const detectionConfig = {
    name: "test_detection_configuration" + new Date().getTime().toString(),
    metricId,
    wholeSeriesDetectionCondition: {
      smartDetectionCondition: {
        sensitivity: 100,
        anomalyDetectorDirection: "Both",
        suppressCondition: {
          minNumber: 1,
          minRatio: 1
        }
      }
    },
    description: "Detection configuration description"
  };
  return await adminClient.createDetectionConfig(detectionConfig);
}
```

### <a name="create-a-hook"></a>Een hook maken

Er worden hooks gebruikt om u te abonneren op real-time waarschuwingen. In dit voorbeeld maken we een webhook voor de Metrics Advisor-service waar de waarschuwing moet worden geplaatst.

```javascript

const {
  MetricsAdvisorKeyCredential,
  MetricsAdvisorAdministrationClient
} = require("@azure/ai-metrics-advisor");

async function main() {
  // You will need to set these environment variables or edit the following values
  const endpoint = "<service endpoint>";
  const subscriptionKey = "<subscription key>";
  const apiKey = "<api key>";
  const credential = new MetricsAdvisorKeyCredential(subscriptionKey, apiKey);

  const adminClient = new MetricsAdvisorAdministrationClient(endpoint, credential);
  const hook = await createWebhookHook(adminClient);
  console.log(`Webhook hook created: ${hook.id}`);
}

async function createWebhookHook(adminClient) {
  console.log("Creating a webhook hook");
  const hook = {
    hookType: "Webhook",
    name: "web hook " + new Date().getTime().toFixed(),
    description: "description",
    hookParameter: {
      endpoint: "https://example.com/handleAlerts", // you must enter a valid webhook url to post the alert payload
      username: "username",
      password: "password"
      // certificateKey: "certificate key",
      // certificatePassword: "certificate password"
    }
  };

  return await adminClient.createHook(hook);
}
```

##  <a name="create-an-alert-configuration"></a>Een waarschuwingsconfiguratie maken

In dit voorbeeld ziet u hoe u voorwaarden configureert waardoor een waarschuwing moet worden geactiveerd, en welke hooks als bestemming voor een waarschuwing moeten worden gebruikt.

```javascript
const {
  MetricsAdvisorKeyCredential,
  MetricsAdvisorAdministrationClient
} = require("@azure/ai-metrics-advisor");

async function main() {
  // You will need to set these environment variables or edit the following values
  const endpoint = "<service endpoint>";
  const subscriptionKey = "<subscription key>";
  const apiKey = "<api key>";
  const detectionConfigId = "<detection id>";
  const hookId = "<hook id>";
  const credential = new MetricsAdvisorKeyCredential(subscriptionKey, apiKey);

  const adminClient = new MetricsAdvisorAdministrationClient(endpoint, credential);
  const alertConfig = await configureAlertConfiguration(adminClient, detectionConfigId, [hookId]);
  console.log(`Alert configuration created: ${alertConfig.id}`);
}

async function configureAlertConfiguration(adminClient, detectionConfigId, hookIds) {
  console.log("Creating a new alerting configuration...");
  const anomalyAlertConfig = {
    name: "test_alert_config_" + new Date().getTime().toString(),
    crossMetricsOperator: "AND",
    metricAlertConfigurations: [
      {
        detectionConfigurationId: detectionConfigId,
        alertScope: {
          scopeType: "All"
        },
        alertConditions: {
          severityCondition: { minAlertSeverity: "Medium", maxAlertSeverity: "High" }
        },
        snoozeCondition: {
          autoSnooze: 0,
          snoozeScope: "Metric",
          onlyForSuccessive: true
        }
      }
    ],
    hookIds,
    description: "Alerting config description"
  };
  return await adminClient.createAlertConfig(anomalyAlertConfig);
}
```

## <a name="query-the-alert"></a>De waarschuwing opvragen

```javascript
const { MetricsAdvisorKeyCredential, MetricsAdvisorClient } = require("@azure/ai-metrics-advisor");

async function main() {
  // You will need to set these environment variables or edit the following values
  const endpoint = "<service endpoint>";
  const subscriptionKey = "<subscription key>";
  const apiKey = "<api key>";
  const alertConfigId = "<alert config id>";
  const credential = new MetricsAdvisorKeyCredential(subscriptionKey, apiKey);

  const client = new MetricsAdvisorClient(endpoint, credential);

  const alertIds = await queryAlerts(
    client,
    alertConfigId,
    new Date(Date.UTC(2020, 8, 1)),
    new Date(Date.UTC(2020, 8, 12))
  );

  if (alertIds.length > 1) {
    // query anomalies using an alert id.
    await queryAnomaliesByAlert(client, alertConfigId, alertIds[0]);
  } else {
    console.log("No alerts during the time period");
  }
}

async function queryAlerts(client, alertConfigId, startTime, endTime) {
  let alerts = [];
  const iterator = client.listAlerts(alertConfigId, startTime, endTime, "AnomalyTime");
  for await (const alert of iterator) {
    alerts.push(alert);
  }

  return alerts;
}

async function queryAnomaliesByAlert(client, alert) {
  console.log(
    `Listing anomalies for alert configuration '${alert.alertConfigId}' and alert '${alert.id}'`
  );
  const iterator = client.listAnomalies(alert);
  for await (const anomaly of iterator) {
    console.log(
      `  Anomaly ${anomaly.severity} ${anomaly.status} ${anomaly.seriesKey.dimension} ${anomaly.timestamp}`
    );
  }
}
```

### <a name="run-the-application"></a>De toepassing uitvoeren

Voer de toepassing uit met de opdracht `node` in uw quickstart-bestand.

```console
node index.js
```