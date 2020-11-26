---
title: 'Zelfstudie: Java-functies gebruiken met Azure Cosmos DB en Event Hubs'
description: In deze zelfstudie ontdekt u hoe u gebeurtenissen van Event Hubs gebruikt om updates uit te voeren in Azure Cosmos DB met behulp van een functie geschreven in Java.
author: KarlErickson
ms.topic: tutorial
ms.date: 11/04/2019
ms.author: karler
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: c5510a66f48007d629d23a96d17205b489ab6a5c
ms.sourcegitcommit: 6a770fc07237f02bea8cc463f3d8cc5c246d7c65
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/24/2020
ms.locfileid: "95999124"
---
# <a name="tutorial-create-a-function-in-java-with-an-event-hub-trigger-and-an-azure-cosmos-db-output-binding"></a>Zelfstudie: Een functie maken in Java met een Event Hub-trigger en een Azure Cosmos DB-uitvoerbinding

In deze zelfstudie ontdekt u hoe u Azure Functions gebruikt om een Java-functie te maken die een doorlopende stroom van temperatuur- en drukgegevens analyseert. Event Hub-gebeurtenissen die sensorwaarden vertegenwoordigen activeren de functie. De functie verwerkt de gebeurtenisgegevens en voegt vervolgens statusvermeldingen toe aan een Azure Cosmos DB.

In deze zelfstudie gaat u:

> [!div class="checklist"]
> * Azure-resources maken en beheren met behulp van de Azure CLI.
> * Java-functies maken en testen die communiceren met deze resources.
> * Uw functies naar Azure implementeren en ze bewaken met Application Insights.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Vereisten

Om deze zelfstudie te voltooien moet het volgende geïnstalleerd zijn:

* [Java Developer Kit](/azure/developer/java/fundamentals/java-jdk-long-term-support), versie 8
* [Apache Maven](https://maven.apache.org), versie 3.0 of hoger
* [Azure CLI](/cli/azure/install-azure-cli) als u liever Cloud Shell niet gebruikt
* [Azure Functions Core Tools](https://www.npmjs.com/package/azure-functions-core-tools), versie 2.6.666 of hoger

> [!IMPORTANT]
> De omgevingsvariabele `JAVA_HOME` moet zijn ingesteld op de installatielocatie van de JDK om deze zelfstudie te kunnen voltooien.

Als u de code voor deze zelfstudie direct wilt gebruiken, raadpleegt u de voorbeeldopslagplaats [java-functions-eventhub-cosmosdb](https://github.com/Azure-Samples/java-functions-eventhub-cosmosdb).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-azure-resources"></a>Azure-resources maken

In deze zelfstudie hebt u de volgende resources nodig:

* Een resourcegroep die de andere resources bevat
* Een Event Hubs-naamruimte, event hub en autorisatieregel
* Een account, database en verzameling in Cosmos DB
* Een functie-app en een opslagaccount om deze te hosten

In de volgende onderdelen ziet u hoe u deze resources maakt met behulp van de Azure CLI.

### <a name="log-in-to-azure"></a>Meld u aan bij Azure.

Als u Cloud Shell niet gebruikt, dan moet de de Azure CLI lokaal gebruiken om toegang te krijgen tot uw account. Gebruik de opdracht `az login` uit de bash-prompt om de browser-gebaseerde aanmeldingservaring te openen. Als u toegang tot meer dan één Azure-abonnement hebt, stelt u de standaard in met `az account set --subscription` gevolgd door het abonnements-id.

### <a name="set-environment-variables"></a>Omgevingsvariabelen instellen

Maak vervolgens enkele omgevingsvariabelen voor de namen en de locatie van de resources die u gaat maken. Gebruik de volgende opdrachten om de tijdelijke aanduidingen `<value>` te vervangen door uw gekozen waarden. Waarden moeten voldoen aan de [naamgevingsregels en -beperkingen voor Azure-resources](/azure/architecture/best-practices/resource-naming). Gebruik voor de variabele `LOCATION` een van de waarden die door de opdracht `az functionapp list-consumption-locations` worden geproduceerd.

```azurecli-interactive
RESOURCE_GROUP=<value>
EVENT_HUB_NAMESPACE=<value>
EVENT_HUB_NAME=<value>
EVENT_HUB_AUTHORIZATION_RULE=<value>
COSMOS_DB_ACCOUNT=<value>
STORAGE_ACCOUNT=<value>
FUNCTION_APP=<value>
LOCATION=<value>
```

In de rest van deze zelfstudie worden deze variabelen gebruikt. Houd er rekening mee dat deze variabelen alleen behouden blijven tijdens uw huidige sessie in Azure CLI of Cloud Shell. U moet deze opdrachten opnieuw uitvoeren als u een ander lokaal terminalvenster gebruikt, of als er een time-out optreedt voor de Cloud Shell-sessie.

### <a name="create-a-resource-group"></a>Een resourcegroep maken

Azure gebruikt resourcegroepen om alle gerelateerde resources in uw account te verzamelen. Op die manier kunt u ze bekijken als eenheid en ze met één opdracht verwijderen wanneer u klaar bent.

Gebruik de volgende opdracht om een resourcegroep te maken:

```azurecli-interactive
az group create \
    --name $RESOURCE_GROUP \
    --location $LOCATION
```

### <a name="create-an-event-hub"></a>Een Event Hub maken

Maak vervolgens een Azure Event Hubs-naamruimte, event hub en autorisatieregel met behulp van de volgende opdrachten:

```azurecli-interactive
az eventhubs namespace create \
    --resource-group $RESOURCE_GROUP \
    --name $EVENT_HUB_NAMESPACE
az eventhubs eventhub create \
    --resource-group $RESOURCE_GROUP \
    --name $EVENT_HUB_NAME \
    --namespace-name $EVENT_HUB_NAMESPACE \
    --message-retention 1
az eventhubs eventhub authorization-rule create \
    --resource-group $RESOURCE_GROUP \
    --name $EVENT_HUB_AUTHORIZATION_RULE \
    --eventhub-name $EVENT_HUB_NAME \
    --namespace-name $EVENT_HUB_NAMESPACE \
    --rights Listen Send
```

De Event Hubs-naamruimte bevat de daadwerkelijke event hub en de autorisatieregel. De autorisatieregel stelt uw functies in staat om berichten te sturen naar de hub en te luisteren naar de overeenkomstige gebeurtenissen. De ene functie verstuurt berichten die telemetriegegevens vertegenwoordigen. Een andere functie luistert naar gebeurtenissen, analyseert de gebeurtenisgegevens en slaat de resultaten op in Azure Cosmos DB.

### <a name="create-an-azure-cosmos-db"></a>Een Azure Cosmos DB maken

Maak vervolgens een Azure Cosmos DB-account, -database en -verzameling met behulp van de volgende opdrachten:

```azurecli-interactive
az cosmosdb create \
    --resource-group $RESOURCE_GROUP \
    --name $COSMOS_DB_ACCOUNT
az cosmosdb database create \
    --resource-group-name $RESOURCE_GROUP \
    --name $COSMOS_DB_ACCOUNT \
    --db-name TelemetryDb
az cosmosdb collection create \
    --resource-group-name $RESOURCE_GROUP \
    --name $COSMOS_DB_ACCOUNT \
    --collection-name TelemetryInfo \
    --db-name TelemetryDb \
    --partition-key-path '/temperatureStatus'
```

De waarde `partition-key-path` partitioneert uw gegevens op basis van de `temperatureStatus`-waarde voor elk item. De partitiesleutel stelt Cosmos DB in staat om de prestaties te verbeteren door uw gegevens op te delen in aparte subsets die onafhankelijk van elkaar toegankelijk zijn.

### <a name="create-a-storage-account-and-function-app"></a>Maak een opslagaccount en functie-app

Maak vervolgens een Azure Storage-account, wat vereist is voor Azure Functions, en maak vervolgens de functie-app. Gebruik de volgende opdrachten:

```azurecli-interactive
az storage account create \
    --resource-group $RESOURCE_GROUP \
    --name $STORAGE_ACCOUNT \
    --sku Standard_LRS
az functionapp create \
    --resource-group $RESOURCE_GROUP \
    --name $FUNCTION_APP \
    --storage-account $STORAGE_ACCOUNT \
    --consumption-plan-location $LOCATION \
    --runtime java
```

Wanneer de `az functionapp create`-opdracht uw functie-app maakt, wordt er ook een Application Insights-resource met dezelfde naam gemaakt. De functie-app wordt automatisch geconfigureerd met een instelling met de naam `APPINSIGHTS_INSTRUMENTATIONKEY` die deze verbindt met Application Insights. U kunt de app-telemetrie bekijken nadat u uw functies hebt geïmplementeerd in Azure, zoals verderop in deze zelfstudie beschreven wordt.

## <a name="configure-your-function-app"></a>Uw functie-app configureren

De functie-app moet toegang hebben tot de andere resources om goed te kunnen werken. In de volgende onderdelen ontdekt u hoe u uw functie-app zodanig configureert dat deze op uw lokale machine kan worden uitgevoerd.

### <a name="retrieve-resource-connection-strings"></a>Verbindingsreeken voor een resource ophalen

Gebruik de volgende opdrachten om de opslag-, event hub- en Cosmos DB-verbindingsreeksen op te halen en op te slaan in omgevingsvariabelen:

```azurecli-interactive
AZURE_WEB_JOBS_STORAGE=$( \
    az storage account show-connection-string \
        --name $STORAGE_ACCOUNT \
        --query connectionString \
        --output tsv)
echo $AZURE_WEB_JOBS_STORAGE
EVENT_HUB_CONNECTION_STRING=$( \
    az eventhubs eventhub authorization-rule keys list \
        --resource-group $RESOURCE_GROUP \
        --name $EVENT_HUB_AUTHORIZATION_RULE \
        --eventhub-name $EVENT_HUB_NAME \
        --namespace-name $EVENT_HUB_NAMESPACE \
        --query primaryConnectionString \
        --output tsv)
echo $EVENT_HUB_CONNECTION_STRING
COSMOS_DB_CONNECTION_STRING=$( \
    az cosmosdb keys list \
        --resource-group $RESOURCE_GROUP \
        --name $COSMOS_DB_ACCOUNT \
        --type connection-strings \
        --query 'connectionStrings[0].connectionString' \
        --output tsv)
echo $COSMOS_DB_CONNECTION_STRING
```

Deze variabelen worden ingesteld op waarden die worden opgehaald vanuit Azure CLI-opdrachten. Elke opdracht maakt gebruik van een JMESPath-query om de verbindingsreeks op te halen uit de geretourneerde JSON-payload. De verbindingsreeksen worden ook weergegeven met `echo` zodat u kunt controleren of ze met succes zijn opgehaald.

### <a name="update-your-function-app-settings"></a>De instellingen van uw functie-app bijwerken

Gebruik vervolgens de volgende opdracht om de waarden van de verbindingsreeks over te zetten naar de app-instellingen in uw Azure Functions-account:

```azurecli-interactive
az functionapp config appsettings set \
    --resource-group $RESOURCE_GROUP \
    --name $FUNCTION_APP \
    --settings \
        AzureWebJobsStorage=$AZURE_WEB_JOBS_STORAGE \
        EventHubConnectionString=$EVENT_HUB_CONNECTION_STRING \
        CosmosDBConnectionString=$COSMOS_DB_CONNECTION_STRING
```

Uw Azure-resources zijn nu aangemaakt en geconfigureerd om goed te kunnen samen werken.

## <a name="create-and-test-your-functions"></a>Uw functies maken en testen

Nu gaat u een project maken op uw lokale machine, Java-code toevoegen en deze testen. U gebruikt opdrachten die werken met de Azure Functions Plugin voor Maven en de Azure Functions Core Tools. Uw functies worden lokaal uitgevoerd, maar gebruiken de cloudresources die u hebt gemaakt. Wanneer uw functies lokaal werken, kunt u Maven gebruiken om ze te implementeren naar de cloud en te kijken hoe uw gegevens en analyses verzqmeld worden.

Als u Cloud Shell hebt gebruikt om uw resources aan te maken, wordt u niet lokaal verbonden met Azure. Gebruik in dat geval de opdracht `az login` om het browsergebaseerde aanmeldingsproces te starten. Stel vervolgens, indien nodig, het standaardabonnement in met `az account set --subscription`, gevolgd door de abonnements-id. Voer ten slotte de volgende opdrachten uit om enkele omgevingsvariabelen opnieuw te maken op uw lokale machine. Vervang de tijdelijke aanduidingen `<value>` door de waarden die u eerder hebt gebruikt.

```bash
RESOURCE_GROUP=<value>
FUNCTION_APP=<value>
```

### <a name="create-a-local-functions-project"></a>Een lokaal Functions-project maken

Gebruik de volgende Maven-opdracht om een Functions-project te maken en de nodige afhankelijkheden toe te voegen.

```bash
mvn archetype:generate --batch-mode \
    -DarchetypeGroupId=com.microsoft.azure \
    -DarchetypeArtifactId=azure-functions-archetype \
    -DappName=$FUNCTION_APP \
    -DresourceGroup=$RESOURCE_GROUP \
    -DgroupId=com.example \
    -DartifactId=telemetry-functions
```

Deze opdracht genereert verschillende bestanden in een `telemetry-functions`-map:

* Een `pom.xml`-bestand dat kan worden gebruikt met Maven
* Een `local.settings.json`-bestand om app-instellingen te bewaren voor lokale tests
* Een `host.json`-bestand dat de Azure Functions Extension Bundle inschakelt, die vereist is voor Cosmos DB-uitvoerbinding in uw functie voor gegevensanalyse
* Een `Function.java`-bestand dat een standaard functie-implementatie bevat
* Een aantal testbestanden die niet nodig zijn voor deze zelfstudie

Verwijder de testbestanden om compilatiefouten te voorkomen Voer de volgende opdrachten uit om naar de nieuwe projectmap te navigeren en verwijder de testmap:

```bash
cd telemetry-functions
rm -r src/test
```

### <a name="retrieve-your-function-app-settings-for-local-use"></a>Haal de instellingen van uw functie-app op voor lokaal gebruik

Voor lokale tests heeft uw functieproject de verbindingsreeksen nodig die u eerder in deze zelfstudie hebt toegevoegd aan uw functie-app in Azure. Gebruik de volgende Azure Functions Core Tools-opdracht, die alle instellingen van de functie-app die zijn opgeslagen in de cloud ophaalt en ze toevoegt aan uw `local.settings.json`-bestand:

```bash
func azure functionapp fetch-app-settings $FUNCTION_APP
```

### <a name="add-java-code"></a>Java-code toevoegen

Open vervolgens het `Function.java`-bestand en vervang de inhoud door de volgende code.

```java
package com.example;

import com.example.TelemetryItem.status;
import com.microsoft.azure.functions.annotation.Cardinality;
import com.microsoft.azure.functions.annotation.CosmosDBOutput;
import com.microsoft.azure.functions.annotation.EventHubOutput;
import com.microsoft.azure.functions.annotation.EventHubTrigger;
import com.microsoft.azure.functions.annotation.FunctionName;
import com.microsoft.azure.functions.annotation.TimerTrigger;
import com.microsoft.azure.functions.ExecutionContext;
import com.microsoft.azure.functions.OutputBinding;

public class Function {

    @FunctionName("generateSensorData")
    @EventHubOutput(
        name = "event",
        eventHubName = "", // blank because the value is included in the connection string
        connection = "EventHubConnectionString")
    public TelemetryItem generateSensorData(
        @TimerTrigger(
            name = "timerInfo",
            schedule = "*/10 * * * * *") // every 10 seconds
            String timerInfo,
        final ExecutionContext context) {

        context.getLogger().info("Java Timer trigger function executed at: "
            + java.time.LocalDateTime.now());
        double temperature = Math.random() * 100;
        double pressure = Math.random() * 50;
        return new TelemetryItem(temperature, pressure);
    }

    @FunctionName("processSensorData")
    public void processSensorData(
        @EventHubTrigger(
            name = "msg",
            eventHubName = "", // blank because the value is included in the connection string
            cardinality = Cardinality.ONE,
            connection = "EventHubConnectionString")
            TelemetryItem item,
        @CosmosDBOutput(
            name = "databaseOutput",
            databaseName = "TelemetryDb",
            collectionName = "TelemetryInfo",
            connectionStringSetting = "CosmosDBConnectionString")
            OutputBinding<TelemetryItem> document,
        final ExecutionContext context) {

        context.getLogger().info("Event hub message received: " + item.toString());

        if (item.getPressure() > 30) {
            item.setNormalPressure(false);
        } else {
            item.setNormalPressure(true);
        }

        if (item.getTemperature() < 40) {
            item.setTemperatureStatus(status.COOL);
        } else if (item.getTemperature() > 90) {
            item.setTemperatureStatus(status.HOT);
        } else {
            item.setTemperatureStatus(status.WARM);
        }

        document.setValue(item);
    }
}
```

Zoals u kunt zien, bevat dit bestand twee functies, `generateSensorData` en `processSensorData`. De functie `generateSensorData` simuleert een sensor die de temperatuur- en drukwaarden doorstuurt naar de event hub. Een tijdstrigger voert de functie elke 10 seconden uit en een Event Hub-uitvoerbinding verzendt de retourwaarde naar de event hub.

Wanneer de event hub het bericht ontvangt, genereert het een gebeurtenis. De functie `processSensorData` wordt uitgevoerd wanneer het de gebeurtenis ontvangt. Vervolgens verwerkt het de gebeurtenisgegevens en gebruikt het een Azure Cosmos DB-uitvoerbinding om de resultaten naar Azure Cosmos DB te sturen.

De gegevens die door deze functies worden gebruikt, worden opgeslagen met behulp van een klasse met de naam `TelemetryItem`, die u moet implementeren. Maak een nieuw bestand met de naam `TelemetryItem.java` op dezelfde locatie als `Function.java` en voeg de volgende code toe:

```java
package com.example;

public class TelemetryItem {

    private String id;
    private double temperature;
    private double pressure;
    private boolean isNormalPressure;
    private status temperatureStatus;
    static enum status {
        COOL,
        WARM,
        HOT
    }

    public TelemetryItem(double temperature, double pressure) {
        this.temperature = temperature;
        this.pressure = pressure;
    }

    public String getId() {
        return id;
    }

    public double getTemperature() {
        return temperature;
    }

    public double getPressure() {
        return pressure;
    }

    @Override
    public String toString() {
        return "TelemetryItem={id=" + id + ",temperature="
            + temperature + ",pressure=" + pressure + "}";
    }

    public boolean isNormalPressure() {
        return isNormalPressure;
    }

    public void setNormalPressure(boolean isNormal) {
        this.isNormalPressure = isNormal;
    }

    public status getTemperatureStatus() {
        return temperatureStatus;
    }

    public void setTemperatureStatus(status temperatureStatus) {
        this.temperatureStatus = temperatureStatus;
    }
}
```

### <a name="run-locally"></a>Lokaal uitvoeren

U kunt nu de functies lokaal bouwen en uitvoeren en gegevens weergeven in uw Azure Cosmos DB.

Gebruik de volgende Maven-opdrachten om de functies te bouwen en uitvoeren:

```bash
mvn clean package
mvn azure-functions:run
```

Na sommige build- en opstartberichten ziet u uitvoer die lijkt op het volgende voorbeeld telkens de functie wordt uitgevoerd:

```output
[10/22/19 4:01:30 AM] Executing 'Functions.generateSensorData' (Reason='Timer fired at 2019-10-21T21:01:30.0016769-07:00', Id=c1927c7f-4f70-4a78-83eb-bc077d838410)
[10/22/19 4:01:30 AM] Java Timer trigger function executed at: 2019-10-21T21:01:30.015
[10/22/19 4:01:30 AM] Function "generateSensorData" (Id: c1927c7f-4f70-4a78-83eb-bc077d838410) invoked by Java Worker
[10/22/19 4:01:30 AM] Executed 'Functions.generateSensorData' (Succeeded, Id=c1927c7f-4f70-4a78-83eb-bc077d838410)
[10/22/19 4:01:30 AM] Executing 'Functions.processSensorData' (Reason='', Id=f4c3b4d7-9576-45d0-9c6e-85646bb52122)
[10/22/19 4:01:30 AM] Event hub message received: TelemetryItem={id=null,temperature=32.728691307527015,pressure=10.122563042388165}
[10/22/19 4:01:30 AM] Function "processSensorData" (Id: f4c3b4d7-9576-45d0-9c6e-85646bb52122) invoked by Java Worker
[10/22/19 4:01:38 AM] Executed 'Functions.processSensorData' (Succeeded, Id=1cf0382b-0c98-4cc8-9240-ee2a2f71800d)
```

U kunt vervolgens naar het [Azure-portal](https://portal.azure.com) gaan en naar uw Azure Cosmos DB-account navigeren. Selecteer **Data Explorer**, vouw **TelemetryInfo** uit en selecteer **items** om uw gegevens weer te geven wanneer deze aankomen.

![Cosmos DB Data Explorer](media/functions-event-hub-cosmos-db/data-explorer.png)

## <a name="deploy-to-azure-and-view-app-telemetry"></a>Implementeren naar Azure en app-telemetrie weergeven

Ten slotte kunt u uw app implementeren in Azure en controleren of deze blijft werken zoals hij dat lokaal deed.

Implementeer uw project naar Azure met behulp van de volgende opdracht:

```bash
mvn azure-functions:deploy
```

Uw functies worden nu uitgevoerd in Azure, en blijven gegevens verzamelen in uw Azure Cosmos DB. U kunt uw geïmplementeerde functie-app weergeven in de Azure-portal en de app-telemetrie weergeven via de verbonden Application Insights-resource, zoals te zien is in de volgende schermopnames:

**Live Metrics Stream:**

![Live Metrics Stream van Application Insights](media/functions-event-hub-cosmos-db/application-insights-live-metrics-stream.png)

**Prestaties:**

![Prestatieblade van Application Insights](media/functions-event-hub-cosmos-db/application-insights-performance.png)

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u klaar bent met de Azure-resources die u in deze zelfstudie hebt gemaakt, kunt u deze verwijderen met de volgende opdracht:

```azurecli-interactive
az group delete --name $RESOURCE_GROUP
```

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie heeft u geleerd om een Azure Function te maken die Event Hub-gebeurtenissen verwerkt en een Cosmos DB bijwerkt. Zie de [Azure Functions Java-ontwikkelaarshandleiding](./functions-reference-java.md) voor meer informatie. Zie de referentie [com.microsoft.azure.functions.annotation](/java/api/com.microsoft.azure.functions.annotation) voor meer informatie over de gebruikte aantekeningen.

Deze zelfstudie heeft omgevingsvariabelen en toepassingsinstellingen gebruikt om geheimen zoals verbindingsreeksen op te slaan. Zie voor informatie over het opslaan van deze geheimen in Azure Key Vault [Key Vault-referenties gebruiken voor App Service en Azure Functions](../app-service/app-service-key-vault-references.md).

Vervolgens leert u Azure Pipelines CI/CD gebruiken voor geautomatiseerde implementatie:

> [!div class="nextstepaction"]
> [Java maken en implementeren naar Azure Functions](/azure/devops/pipelines/ecosystems/java-function)
