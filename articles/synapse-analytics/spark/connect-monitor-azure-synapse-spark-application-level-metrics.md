---
title: Zelf studie-metrische gegevens van Azure Synapse Spark-toepassings niveau verbinden en bewaken
description: 'Zelf studie: informatie over het integreren van uw bestaande on-premises Prometheus-server met de Azure Synapse-werk ruimte voor bijna realtime Azure Spark-toepassings gegevens met behulp van de Synapse Prometheus-connector.'
services: synapse-analytics
author: jejiang
ms.author: jejiang
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.topic: tutorial
ms.subservice: spark
ms.date: 01/22/2021
ms.openlocfilehash: d22975199eedae353f2dc12588671ae4b54c85ab
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105109315"
---
# <a name="tutorial-connect-and-monitor-azure-synapse-spark-application-level-metrics"></a>Zelf studie: metrische gegevens over het Azure Synapse Spark-toepassings niveau verbinden en bewaken

## <a name="overview"></a>Overzicht

In deze zelf studie leert u hoe u uw bestaande on-premises Prometheus-server kunt integreren met de Azure Synapse-werk ruimte voor bijna realtime Azure Spark-toepassings gegevens met behulp van de Synapse Prometheus-connector. 

In deze zelf studie worden ook de Azure Synapse REST Metrics-Api's geïntroduceerd. U kunt gegevens over Spark-toepassings metrieken ophalen via de REST Api's om uw eigen bewakings-en diagnose Toolkit te bouwen of te integreren met uw bewakings systemen.

## <a name="use-azure-synapse-prometheus-connector-for-your-on-premises-prometheus-servers"></a>Azure Synapse Prometheus-connector gebruiken voor uw on-premises Prometheus-servers

De [Azure Synapse Prometheus-connector](https://github.com/microsoft/azure-synapse-spark-metrics) is een open-source project. De Synapse Prometheus-connector gebruikt een op bestanden gebaseerde service detectie methode waarmee u het volgende kunt doen:
 - Verificatie voor de Synapse-werk ruimte via een AAD-Service-Principal.
 - De lijst met toepassingen voor de werk ruimte ophalen Apache Spark. 
 - Metrische gegevens van Spark-toepassingen ophalen via Prometheus op basis van bestanden. 

### <a name="1-prerequisite"></a>1. vereiste

U moet een Prometheus-server hebben geïmplementeerd op een virtuele Linux-machine.

### <a name="2-create-a-service-principal"></a>2. een service-principal maken

Als u de Azure Synapse Prometheus-connector wilt gebruiken in uw on-premises Prometheus-server, moet u de onderstaande stappen volgen om een service-principal te maken.

#### <a name="21-create-a-service-principal"></a>2,1 een service-principal maken:

```bash
az ad sp create-for-rbac --name <service_principal_name>
```

Het resultaat moet er als volgt uitzien:

```json
{
  "appId": "abcdef...",
  "displayName": "<service_principal_name>",
  "name": "http://<service_principal_name>",
  "password": "abc....",
  "tenant": "<tenant_id>"
}
```

Noteer de appId, het wacht woord en de Tenant-ID.

#### <a name="22-add-corresponding-permissions-to-the-service-principal-created-in-the-above-step"></a>2,2 Voeg de bijbehorende machtigingen toe aan de service-principal die in de bovenstaande stap is gemaakt.

![scherm afbeelding verlenen machtiging Srbac](./media/monitor-azure-synapse-spark-application-level-metrics/screenshot-grant-permission-srbac-new.png)

1. Meld u aan bij uw [Azure Synapse Analytics-werk ruimte](https://web.azuresynapse.net/) als Synapse-beheerder

2. Selecteer in Synapse Studio in het linkerdeel venster de optie **Manage > Access Control**

3. Klik op de knop toevoegen in de linkerbovenhoek om **een roltoewijzing toe te voegen**

4. Voor bereik kiest u **werk ruimte**

5. Kies voor rol **Synapse Compute-operator**

6. Voer voor gebruiker selecteren de **<in service_principal_name>** en klik op uw Service-Principal

7. Klik op **Toep assen** (wacht drie minuten om de machtiging te activeren.)


### <a name="3-download-the-azure-synapse-prometheus-connector"></a>3. down load de Azure Synapse Prometheus-connector

Gebruik de opdrachten om de Azure Synapse Prometheus-connector te installeren.

```bash
git clone https://github.com/microsoft/azure-synapse-spark-metrics.git
cd ./azure-synapse-spark-metrics/synapse-prometheus-connector/src
python pip install -r requirements.txt
```

### <a name="4-create-a-config-file-for-azure-synapse-workspaces"></a>4. Maak een configuratie bestand voor Azure Synapse-werk ruimten

Maak een bestand config. yaml in de map config en vul de volgende velden in: workspace_name, tenant_id, service_principal_name en service_principal_password.
U kunt meerdere werk ruimten toevoegen in de yaml-configuratie.

```yaml
workspaces:
  - workspace_name: <your_workspace_name>
    tenant_id: <tenant_id>
    service_principal_name: <service_principal_app_id>
    service_principal_password: "<service_principal_password>"
```

### <a name="5-update-the-prometheus-config"></a>5. de Prometheus-configuratie bijwerken

Voeg het volgende configuratie gedeelte toe aan uw Prometheus-scrape_config en vervang de <your_workspace_name> naar de naam van uw werk ruimte en de <path_to_synapse_connector> naar uw gekloonde Synapse-Prometheus-connector-map

```yaml
- job_name: synapse-prometheus-connector
  static_configs:
  - labels:
      __metrics_path__: /metrics
      __scheme__: http
    targets:
    - localhost:8000
- job_name: synapse-workspace-<your_workspace_name>
  bearer_token_file: <path_to_synapse_connector>/output/workspace/<your_workspace_name>/bearer_token
  file_sd_configs:
  - files:
    - <path_to_synapse_connector>/output/workspace/<your_workspace_name>/application_discovery.json
    refresh_interval: 10s
  metric_relabel_configs:
  - source_labels: [ __name__ ]
    target_label: __name__
    regex: metrics_application_[0-9]+_[0-9]+_(.+)
    replacement: spark_$1
  - source_labels: [ __name__ ]
    target_label: __name__
    regex: metrics_(.+)
    replacement: spark_$1
```


### <a name="6-start-the-connector-in-the-prometheus-server-vm"></a>6. Start de connector in de Prometheus-Server-VM

Start een connector server als volgt in de Prometheus-Server-VM.

```
python main.py
```

Wacht een paar seconden en de connector moet beginnen te werken. En u ziet de ' Synapse-Prometheus-connector ' op de pagina Prometheus service detectie.

## <a name="use-azure-synapse-prometheus-or-rest-metrics-apis-to-collect-metrics-data"></a>Gebruik Azure Synapse Prometheus of REST Metrics-Api's voor het verzamelen van metrische gegevens

### <a name="1-authentication"></a>1. verificatie
U kunt de client referenties stroom gebruiken om een toegangs token op te halen. Om toegang te krijgen tot de metrische gegevens van de API, moet u een Azure AD-toegangs token voor de Service-Principal verkrijgen, die de juiste machtigingen heeft om toegang te krijgen tot de Api's.

| Parameter     | Vereist | Beschrijving                                                                                                   |
| ------------- | -------- | ------------------------------------------------------------------------------------------------------------- |
| tenant_id     | Waar     | De Tenant-ID van uw Azure-Service-Principal (toepassing)                                                          |
| grant_type    | Waar     | Hiermee geeft u het aangevraagde toekennings type op. In een client referenties toekenning stroom moet de waarde client_credentials zijn. |
| client_id     | Waar     | De toepassing (Service-Principal)-ID van de toepassing die u hebt geregistreerd in Azure Portal of Azure CLI.        |
| client_secret | Waar     | Het geheim dat is gegenereerd voor de toepassing (Service-Principal)                                                  |
| resource      | Waar     | Synapse-resource-URI moet ' https://dev.azuresynapse.net ' zijn                                                  |

```bash
curl -X GET -H 'Content-Type: application/x-www-form-urlencoded' \
  -d 'grant_type=client_credentials&client_id=<service_principal_app_id>&resource=<azure_synapse_resource_id>&client_secret=<service_principal_secret>' \
  https://login.microsoftonline.com/<tenant_id>/oauth2/token
```

Het antwoord ziet er als volgt uit:

```json
{
  "token_type": "Bearer",
  "expires_in": "599",
  "ext_expires_in": "599",
  "expires_on": "1575500666",
  "not_before": "1575499766",
  "resource": "2ff8...f879c1d",
  "access_token": "ABC0eXAiOiJKV1Q......un_f1mSgCHlA"
}
```

### <a name="2-list-running-applications-in-the-azure-synapse-workspace"></a>2. actieve toepassingen weer geven in de Azure Synapse-werk ruimte

Als u een lijst wilt ophalen met Spark-toepassingen voor een Synapse-werk ruimte, kunt u deze document [bewaking volgen-taken lijst van Spark ophalen](/rest/api/synapse/data-plane/monitoring/getsparkjoblist).


### <a name="3-collect-spark-application-metrics-with-the-prometheus-or-rest-apis"></a>3. metrische gegevens van Spark-toepassingen verzamelen met de Prometheus-of REST-Api's


#### <a name="collect-spark-application-metrics-with-the-prometheus-api"></a>Metrische toepassings gegevens verzamelen met de Prometheus-API

De meest recente metrische gegevens ophalen van de opgegeven Spark-toepassing door de Prometheus-API

```
GET https://{endpoint}/livyApi/versions/{livyApiVersion}/sparkpools/{sparkPoolName}/sessions/{sessionId}/applications/{sparkApplicationId}/metrics/executors/prometheus?format=html
```

| Parameter          | Vereist | Beschrijving                                                                               |
| ------------------ | -------- | ----------------------------------------------------------------------------------------- |
| endpoint           | Waar     | Het eind punt voor het ontwikkelen van werk ruimten, bijvoorbeeld https://myworkspace.dev.azuresynapse.net . |
| livyApiVersion     | Waar     | Geldige API-versie voor de aanvraag. Het is momenteel 2019-11-01-preview-versie                    |
| sparkPoolName      | Waar     | De naam van de Spark-pool.                                                                   |
| sessionId          | Waar     | De id voor de sessie.                                                               |
| sparkApplicationId | Waar     | Spark-toepassings-ID                                                                      |

Voorbeeld aanvraag: 

```
GET https://myworkspace.dev.azuresynapse.net/livyApi/versions/2019-11-01-preview/sparkpools/mysparkpool/sessions/1/applications/application_1605509647837_0001/metrics/executors/prometheus?format=html
```

Voorbeeld antwoord:

Status code: 200-antwoord ziet er als volgt uit:

```
metrics_executor_rddBlocks{application_id="application_1605509647837_0001", application_name="mynotebook_mysparkpool_1605509570802", executor_id="driver"} 0
metrics_executor_memoryUsed_bytes{application_id="application_1605509647837_0001", application_name="mynotebook_mysparkpool_1605509570802", executor_id="driver"} 74992
metrics_executor_diskUsed_bytes{application_id="application_1605509647837_0001", application_name="mynotebook_mysparkpool_1605509570802", executor_id="driver"} 0
metrics_executor_totalCores{application_id="application_1605509647837_0001", application_name="mynotebook_mysparkpool_1605509570802", executor_id="driver"} 0
metrics_executor_maxTasks{application_id="application_1605509647837_0001", application_name="mynotebook_mysparkpool_1605509570802", executor_id="driver"} 0
metrics_executor_activeTasks{application_id="application_1605509647837_0001", application_name="mynotebook_mysparkpool_1605509570802", executor_id="driver"} 1
metrics_executor_failedTasks_total{application_id="application_1605509647837_0001", application_name="mynotebook_mysparkpool_1605509570802", executor_id="driver"} 0
metrics_executor_completedTasks_total{application_id="application_1605509647837_0001", application_name="mynotebook_mysparkpool_1605509570802", executor_id="driver"} 2
...

```

#### <a name="collect-spark-application-metrics-with-the-rest-api"></a>Metrische gegevens van Spark-toepassingen verzamelen met de REST API

```
GET https://{endpoint}/livyApi/versions/{livyApiVersion}/sparkpools/{sparkPoolName}/sessions/{sessionId}/applications/{sparkApplicationId}/executors
```

| Parameter          | Vereist | Beschrijving                                                                               |
| ------------------ | -------- | ----------------------------------------------------------------------------------------- |
| endpoint           | Waar     | Het eind punt voor het ontwikkelen van werk ruimten, bijvoorbeeld https://myworkspace.dev.azuresynapse.net . |
| livyApiVersion     | Waar     | Geldige API-versie voor de aanvraag. Het is momenteel 2019-11-01-preview-versie                    |
| sparkPoolName      | Waar     | De naam van de Spark-pool.                                                                   |
| sessionId          | Waar     | De id voor de sessie.                                                               |
| sparkApplicationId | Waar     | Spark-toepassings-ID                                                                      |

Voorbeeldaanvraag

```
GET https://myworkspace.dev.azuresynapse.net/livyApi/versions/2019-11-01-preview/sparkpools/mysparkpool/sessions/1/applications/application_1605509647837_0001/executors
```

Status code voorbeeld antwoord: 200

```json
[
    {
        "id": "driver",
        "hostPort": "f98b8fc2aea84e9095bf2616208eb672007bde57624:45889",
        "isActive": true,
        "rddBlocks": 0,
        "memoryUsed": 75014,
        "diskUsed": 0,
        "totalCores": 0,
        "maxTasks": 0,
        "activeTasks": 0,
        "failedTasks": 0,
        "completedTasks": 0,
        "totalTasks": 0,
        "totalDuration": 0,
        "totalGCTime": 0,
        "totalInputBytes": 0,
        "totalShuffleRead": 0,
        "totalShuffleWrite": 0,
        "isBlacklisted": false,
        "maxMemory": 15845975654,
        "addTime": "2020-11-16T06:55:06.718GMT",
        "executorLogs": {
            "stdout": "http://f98b8fc2aea84e9095bf2616208eb672007bde57624:8042/node/containerlogs/container_1605509647837_0001_01_000001/trusted-service-user/stdout?start=-4096",
            "stderr": "http://f98b8fc2aea84e9095bf2616208eb672007bde57624:8042/node/containerlogs/container_1605509647837_0001_01_000001/trusted-service-user/stderr?start=-4096"
        },
        "memoryMetrics": {
            "usedOnHeapStorageMemory": 75014,
            "usedOffHeapStorageMemory": 0,
            "totalOnHeapStorageMemory": 15845975654,
            "totalOffHeapStorageMemory": 0
        },
        "blacklistedInStages": []
    },
    // ...
]
```


### <a name="4-build-your-own-diagnosis-and-monitoring-tools"></a>4. bouw uw eigen hulpprogram ma's voor diagnose en controle

De Prometheus-API en de REST Api's bieden uitgebreide metrische gegevens over de gegevens van de Spark-toepassing die worden uitgevoerd. U kunt de metrische gegevens van de toepassing verzamelen met behulp van de Prometheus-API en de REST-Api's. En bouw uw eigen hulpprogram ma's voor diagnose en controle die geschikter zijn voor uw behoeften.
