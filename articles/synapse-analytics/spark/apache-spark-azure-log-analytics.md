---
title: Azure Log Analytics gebruiken voor het verzamelen en visualiseren van metrische gegevens en Logboeken (preview)
description: Meer informatie over het inschakelen van de Synapse ingebouwde Azure Log Analytics-connector voor het verzamelen en verzenden van de metrische gegevens van de Apache Spark toepassing en logboeken naar uw Azure Log Analytics-werk ruimte.
services: synapse-analytics
author: jejiang
ms.author: jejiang
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.topic: tutorial
ms.subservice: spark
ms.date: 03/25/2021
ms.custom: references_regions
ms.openlocfilehash: 243618192593d93bba9d5229e7becfb2af62ce32
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/08/2021
ms.locfileid: "107108549"
---
# <a name="tutorial-use-azure-log-analytics-to-collect-and-visualize-metrics-and-logs-preview"></a>Zelf studie: met Azure Log Analytics metrische gegevens en logboeken verzamelen en visualiseren (preview)

In deze zelf studie leert u hoe u de ingebouwde Synapse-connector voor Azure Log Analytics kunt inschakelen voor het verzamelen en verzenden van de metrische gegevens en logboeken van de Apache Spark toepassing naar uw [Azure log Analytics-werk ruimte](/azure/azure-monitor/logs/quick-create-workspace). Vervolgens kunt u gebruikmaken van een Azure monitor-werkmap om de metrische gegevens en logboeken te visualiseren.

## <a name="configure-azure-log-analytics-workspace-information-in-synapse-studio"></a>Informatie over Azure Log Analytics-werk ruimte configureren in Synapse Studio

### <a name="step-1-create-an-azure-log-analytics-workspace"></a>Stap 1: een Azure Log Analytics-werk ruimte maken

U kunt onderstaande documenten volgen om een Log Analytics-werk ruimte te maken:
- [Een Log Analytics-werkruimte in Azure Portal maken](https://docs.microsoft.com/azure/azure-monitor/logs/quick-create-workspace)
- [Een Log Analytics-werk ruimte maken met Azure CLI](https://docs.microsoft.com/azure/azure-monitor/logs/quick-create-workspace-cli)
- [Een Log Analytics-werk ruimte maken en configureren in Azure Monitor met behulp van Power shell](https://docs.microsoft.com/azure/azure-monitor/logs/powershell-workspace-configuration)

### <a name="step-2-prepare-a-spark-configuration-file"></a>Stap 2: een Spark-configuratie bestand voorbereiden

#### <a name="option-1-configure-with-azure-log-analytics-workspace-id-and-key"></a>Optie 1. Configureren met Azure Log Analytics werk ruimte-ID en-sleutel 

Kopieer de volgende Spark-configuratie, sla deze op als **' spark_loganalytics_conf.txt '** en vul de para meters in:

   - `<LOG_ANALYTICS_WORKSPACE_ID>`: Azure Log Analytics werk ruimte-ID.
   - `<LOG_ANALYTICS_WORKSPACE_KEY>`: Azure Log Analytics Key: **Azure Portal > Azure log Analytics werkruimte > agents beheer > primaire sleutel**

```properties
spark.synapse.logAnalytics.enabled true
spark.synapse.logAnalytics.workspaceId <LOG_ANALYTICS_WORKSPACE_ID>
spark.synapse.logAnalytics.secret <LOG_ANALYTICS_WORKSPACE_KEY>
```

#### <a name="option-2-configure-with-an-azure-key-vault"></a>Optie 2. Configureren met een Azure Key Vault

> [!NOTE]
>
> U moet een lees geheime machtiging verlenen aan de gebruikers die Spark-toepassingen zullen indienen. Zie [toegang tot Key Vault sleutels, certificaten en geheimen bieden met behulp van een toegangs beheer op basis van rollen in azure](https://docs.microsoft.com/azure/key-vault/general/rbac-guide)

Volg de stappen voor het configureren van een Azure Key Vault om de werkruimte sleutel op te slaan:

1. Maak en navigeer naar uw sleutel kluis in de Azure Portal
2. Selecteer op de Key Vault-instellingspagina **Geheimen**.
3. Klik op **Genereren/importeren**.
4. Kies in het scherm **Een geheim maken** de volgende waarden:
   - **Naam**: Typ een naam voor het geheim, typ `"SparkLogAnalyticsSecret"` als standaard waarde.
   - **Waarde**: typ het **<LOG_ANALYTICS_WORKSPACE_KEY>** voor het geheim.
   - Houd voor de overige waarden de standaardwaarden aan. Klik op **Create**.
5. Kopieer de volgende Spark-configuratie, sla deze op als **' spark_loganalytics_conf.txt '** en vul de para meters in:

   - `<LOG_ANALYTICS_WORKSPACE_ID>`: Azure Log Analytics werk ruimte-ID.
   - `<AZURE_KEY_VAULT_NAME>`: De Azure Key Vault naam die u hebt geconfigureerd.
   - `<AZURE_KEY_VAULT_SECRET_KEY_NAME>` (Optioneel): de geheime naam in de Azure Key Vault voor de werkruimte sleutel, standaard: "SparkLogAnalyticsSecret".

```properties
spark.synapse.logAnalytics.enabled true
spark.synapse.logAnalytics.workspaceId <LOG_ANALYTICS_WORKSPACE_ID>
spark.synapse.logAnalytics.keyVault.name <AZURE_KEY_VAULT_NAME>
spark.synapse.logAnalytics.keyVault.key.secret <AZURE_KEY_VAULT_SECRET_KEY_NAME>
```

> [!NOTE]
>
> U kunt de Log Analytics werk ruimte-id ook opslaan in azure Key kluis. Raadpleeg de bovenstaande stappen en sla de werk ruimte-id op met geheime naam `"SparkLogAnalyticsWorkspaceId"` . Of gebruik de configuratie `spark.synapse.logAnalytics.keyVault.key.workspaceId` om de geheime naam van de werk ruimte-id op te geven in de Azure-sleutel kluis.

#### <a name="option-3-configure-with-an-azure-key-vault-linked-service"></a>Optie 3. Configureren met een Azure Key Vault gekoppelde service

> [!NOTE]
>
> U moet de machtiging Lees geheim verlenen aan de Synapse-werk ruimte. Zie [toegang tot Key Vault sleutels, certificaten en geheimen bieden met behulp van een toegangs beheer op basis van rollen in azure](https://docs.microsoft.com/azure/key-vault/general/rbac-guide)

Volg de stappen voor het configureren van een Azure Key Vault gekoppelde service in Synapse Studio voor het opslaan van de werkruimte sleutel:

1. Volg alle stappen in de `Option 2. Configure with an Azure Key Vault` sectie.
2. Een gekoppelde Azure Key kluis-service maken in Synapse studio:

    a. Navigeer naar **Synapse Studio > > gekoppelde services te beheren** en klik op de knop **Nieuw** .

    b. Zoek **Azure Key Vault** in het zoekvak.

    c. Typ een naam voor de gekoppelde service.

    d. Kies uw Azure-sleutel kluis. Klik op **Create**.

3. Een `spark.synapse.logAnalytics.keyVault.linkedServiceName` item toevoegen aan Spark-configuratie.

```properties
spark.synapse.logAnalytics.enabled true
spark.synapse.logAnalytics.workspaceId <LOG_ANALYTICS_WORKSPACE_ID>
spark.synapse.logAnalytics.keyVault.name <AZURE_KEY_VAULT_NAME>
spark.synapse.logAnalytics.keyVault.key.secret <AZURE_KEY_VAULT_SECRET_KEY_NAME>
spark.synapse.logAnalytics.keyVault.linkedServiceName <LINKED_SERVICE_NAME>
```

#### <a name="available-spark-configuration"></a>Beschik bare Spark-configuratie

| Configuratie naam                                  | Standaardwaarde                | Beschrijving                                                                                                                                                                                                |
| --------------------------------------------------- | ---------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Spark. Synapse. logAnalytics. ingeschakeld                  | onjuist                        | Als u de Azure Log Analytics-Sink wilt inschakelen voor de Spark-toepassingen, waar. Anders false.                                                                                                                  |
| Spark. Synapse. logAnalytics. workspaceId              | -                            | De doel-ID van de Azure-Log Analytics werk ruimte                                                                                                                                                          |
| Spark. Synapse. logAnalytics. Secret                   | -                            | De doel-Azure-Log Analytics werkruimte geheim.                                                                                                                                                      |
| Spark. Synapse. logAnalytics. de sleutel kluis. linkedServiceName   | -                            | De naam van de gekoppelde Azure Key kluis-service voor de Azure Log Analytics werk ruimte-ID en-sleutel                                                                                                                       |
| spark.synapse.logAnalytics.keyVault.name            | -                            | Naam van de Azure-sleutel kluis voor de Azure Log Analytics-ID en-sleutel                                                                                                                                                |
| Spark. Synapse. logAnalytics. de sleutel kluis. key. workspaceId | SparkLogAnalyticsWorkspaceId | Geheime naam van de Azure-sleutel kluis voor de Azure Log Analytics werk ruimte-ID                                                                                                                                       |
| Spark. Synapse. logAnalytics. de sleutel kluis. key. Secret      | SparkLogAnalyticsSecret      | Geheime naam van de Azure-sleutel kluis voor de sleutel van de Azure Log Analytics-werk ruimte                                                                                                                                      |
| Spark. Synapse. logAnalytics. de sleutel kluis. uriSuffix       | ods.opinsights.azure.com     | Het [URI-achtervoegsel][uri_suffix]van de doel-Azure log Analytics-werk ruimte. Als uw Azure Log Analytics-werk ruimte zich niet in azure Global bevindt, moet u het URI-achtervoegsel bijwerken volgens de respectieve Cloud. |

> [!NOTE]  
> - Voor Clouds van Azure China moet de para meter "Spark. Synapse. logAnalytics. de sleutel kluis. uriSuffix" "ods.opinsights.azure.cn" zijn. 
> - Voor Azure gov-Clouds moet de para meter "Spark. Synapse. logAnalytics. de sleutel kluis. uriSuffix" "ods.opinsights.azure.us" zijn. 

[uri_suffix]: https://docs.microsoft.com/azure/azure-monitor/logs/data-collector-api#request-uri


### <a name="step-3-upload-your-spark-configuration-to-a-spark-pool"></a>Stap 3: uw Spark-configuratie uploaden naar een Spark-groep
U kunt het configuratie bestand uploaden naar uw Synapse Spark-groep in Synapse Studio.

   1. Navigeer naar uw Apache Spark-groep in azure Synapse Studio (manage-> Apache Spark-groepen)
   2. Klik op de knop **'... '** rechts van de Apache Spark pool
   3. Apache Spark configuratie selecteren 
   4. Klik op **uploaden** en kies het **' spark_loganalytics_conf.txt '** gemaakt.
   5. Klik op **uploaden** en **Toep assen**.

      > [!div class="mx-imgBorder"]
      > ![configuratie van Spark-groep](./media/apache-spark-azure-log-analytics/spark-pool-configuration.png)

> [!NOTE] 
>
> Alle Spark-toepassingen die zijn ingediend bij de bovenstaande Spark-groep, gebruiken de configuratie-instelling om de metrische gegevens en logboeken van de Spark-toepassing naar de opgegeven Azure Log Analytics-werk ruimte te pushen.

## <a name="submit-a-spark-application-and-view-the-logs-and-metrics-in-azure-log-analytics"></a>Een Spark-toepassing verzenden en de logboeken en metrische gegevens in azure Log Analytics weer geven

 1. U kunt een Spark-toepassing verzenden naar de Spark-groep die in de vorige stap is geconfigureerd, met een van de volgende manieren:
    - Voer een Synapse Studio notebook uit. 
    - Een Synapse Apache Spark batch taak verzenden via een Spark-taak definitie.
    - Voer een pijp lijn uit die Spark-activiteit bevat.

 2. Ga naar de opgegeven Azure Log Analytics-werk ruimte, Bekijk de metrische gegevens en logboeken van de toepassing wanneer de Spark-toepassing wordt uitgevoerd.

## <a name="use-the-sample-azure-log-analytics-workbook-to-visualize-the-metrics-and-logs"></a>De voor beeld-Azure Log Analytics werkmap gebruiken om de metrische gegevens en logboeken te visualiseren

1. [Down load hier de werkmap](https://aka.ms/SynapseSparkLogAnalyticsWorkbook) .
2. De inhoud van het werkmap bestand openen en **kopiëren** .
3. Ga naar Azure Log Analytics werkmap ([Azure Portal](https://portal.azure.com/) > Log Analytics werkruimte > werkmappen)
4. Open de **lege** Azure log Analytics-werkmap in de **Geavanceerde editor** -modus (druk op het pictogram </>).
5. **Plak** een JSON-bestand dat bestaat.
6. Druk vervolgens op **Toep assen** en vervolgens op **bewerken**.

    > [!div class="mx-imgBorder"]
    > ![nieuwe werkmap](./media/apache-spark-azure-log-analytics/new-workbook.png)

    > [!div class="mx-imgBorder"]
    > ![werkmap importeren](./media/apache-spark-azure-log-analytics/import-workbook.png)

Dien vervolgens uw Apache Spark-toepassing in voor de geconfigureerde Spark-groep. Nadat de toepassing de status actief heeft, kiest u de actieve toepassing in de vervolg keuzelijst van de werkmap.

> [!div class="mx-imgBorder"]
> ![werkmap imange](./media/apache-spark-azure-log-analytics/workbook.png)

En u kunt de werkmap aanpassen met behulp van Kusto-query en waarschuwingen configureren.

> [!div class="mx-imgBorder"]
> ![kusto-query en-waarschuwingen](./media/apache-spark-azure-log-analytics/kusto-query-and-alerts.png)

## <a name="sample-kusto-queries"></a>Voor beeld van Kusto-query's

1. Query-voor beeld Spark-gebeurtenissen.

   ```kusto
   SparkListenerEvent_CL
   | where workspaceName_s == "{SynapseWorkspace}" and clusterName_s == "{SparkPool}" and livyId_s == "{LivyId}"
   | order by TimeGenerated desc
   | limit 100 
   ```

2. Query naar Logboeken van Spark-toepassings Stuur Programma's en-voors registraties.

   ```kusto
   SparkLoggingEvent_CL
   | where workspaceName_s == "{SynapseWorkspace}" and clusterName_s == "{SparkPool}" and livyId_s == "{LivyId}"
   | order by TimeGenerated desc
   | limit 100
   ```

3. Query voor beeld van Spark-metrische gegevens.

   ```kusto
   SparkMetrics_CL
   | where workspaceName_s == "{SynapseWorkspace}" and clusterName_s == "{SparkPool}" and livyId_s == "{LivyId}"
   | where name_s endswith "jvm.total.used"
   | summarize max(value_d) by bin(TimeGenerated, 30s), executorId_s
   | order by TimeGenerated asc
   ```

## <a name="create-and-manage-alerts-using-azure-log-analytics"></a>Waarschuwingen maken en beheren met Azure Log Analytics

Met Azure Monitor waarschuwingen kunnen gebruikers een Log Analytics query gebruiken voor het evalueren van metrische gegevens en logboeken elke ingestelde frequentie, en een waarschuwing activeren op basis van de resultaten.

Zie [logboek waarschuwingen maken, weer geven en beheren met behulp van Azure monitor](https://docs.microsoft.com/azure/azure-monitor/alerts/alerts-log)voor meer informatie.

## <a name="limitation"></a>Beperking

 - De Azure Synapse Analytics-werk ruimte waarvoor het [beheerde virtuele netwerk](https://docs.microsoft.com/azure/synapse-analytics/security/synapse-workspace-managed-vnet) is ingeschakeld, wordt niet ondersteund.
 - De volgende regio's worden momenteel niet ondersteund:
   - VS - oost 2
   - Noorwegen - oost
   - VAE - noord

## <a name="next-steps"></a>Volgende stappen

 - Meer informatie over het [gebruik van serverloze Apache Spark pool in Synapse Studio](https://docs.microsoft.com/azure/synapse-analytics/quickstart-create-apache-spark-pool-studio).
 - Meer informatie over het [uitvoeren van een Spark-toepassing in notebook](https://docs.microsoft.com/azure/synapse-analytics/spark/apache-spark-development-using-notebooks).
 - Meer informatie over het [maken van Apache Spark-taak definitie in Synapse Studio](https://docs.microsoft.com/azure/synapse-analytics/spark/apache-spark-job-definitions).