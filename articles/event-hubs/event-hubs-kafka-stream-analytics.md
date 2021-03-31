---
title: 'Azure Event Hubs: gebeurtenissen van Apache Kafka verwerken'
description: 'Zelfstudie: In dit artikel wordt beschreven hoe u Kafka-gebeurtenissen verwerkt die via Event Hubs worden opgenomen met behulp van Azure Stream Analytics'
ms.topic: tutorial
ms.date: 06/23/2020
ms.openlocfilehash: 328537eebd05391fc4c8138395a9c10f0a5d072f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92319378"
---
# <a name="tutorial-process-apache-kafka-for-event-hubs-events-using-stream-analytics"></a>Zelfstudie: Apache Kafka voor Event Hubs-gebeurtenissen verwerken met behulp van Stream Analytics 
In dit artikel wordt beschreven hoe u gegevens streamt naar Event Hubs en hoe u deze verwerkt met Azure Stream Analytics. U wordt door de volgende stappen geleid: 

1. Maak een Event Hubs-naamruimte.
2. Maak een Kafka-client die berichten naar de Event Hub verzendt.
3. Maak een Stream Analytics-taak waarmee gegevens van de Event Hub naar een Azure Blob-opslag worden gekopieerd. 

U hoeft uw protocolclients niet te wijzigen of uw eigen clusters uit te voeren wanneer u het Kafka-eindpunt gebruikt dat door een Event Hub wordt weergegeven. Azure Event Hubs ondersteunt [Apache Kafka versie 1.0.](https://kafka.apache.org/10/documentation.html) en hoger. 


## <a name="prerequisites"></a>Vereisten

Zorg ervoor dat u aan de volgende vereisten voldoet om deze snelstart uit te voeren:

* Een Azure-abonnement. Als u nog geen account hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) voordat u begint.
* [Java Development Kit (JDK) 1.7+](/azure/developer/java/fundamentals/java-jdk-long-term-support).
* [Download](https://maven.apache.org/download.cgi) en [installeer](https://maven.apache.org/install.html) een binair Maven-archief.
* [Git](https://www.git-scm.com/)
* **Een Azure Storage-account**. Als u er nog geen hebt, [maakt u er een](../storage/common/storage-account-create.md) voordat u verder gaat. Met de Stream Analytics-taak in dit overzicht worden de uitvoergegevens opgeslagen in een Azure Blob-opslag. 


## <a name="create-an-event-hubs-namespace"></a>Een Event Hubs-naamruimte maken
Wanneer u een Event Hubs-naamruimte van het type **Standaard** maakt, wordt het Kafka-eindpunt voor de naamruimte automatisch ingeschakeld. U kunt gebeurtenissen vanaf uw toepassingen, waarbij gebruikgemaakt wordt van het Kafka-protocol, naar Event Hubs van het type Standaard streamen. Volg de stapsgewijze instructies in [Een Event Hub maken met behulp van Azure Portal](event-hubs-create.md) om een Event Hubs-naamruimte van het type **Standaard** te maken. 

> [!NOTE]
> Voor Kafka zijn alleen Event Hubs van het type **Standaard** en **Toegewezen** beschikbaar. Het type **Basis** biedt geen ondersteuning voor Kafka op Event Hubs.

## <a name="send-messages-with-kafka-in-event-hubs"></a>Berichten verzenden met Kafka in Event Hubs

1. Kloon de [Azure Event Hubs for Kafka-opslagplaats](https://github.com/Azure/azure-event-hubs-for-kafka) naar uw machine.
2. Navigeer naar de map `azure-event-hubs-for-kafka/quickstart/java/producer`. 
4. Werk de configuratiegegevens voor de producer bij in `src/main/resources/producer.config`. Geef de **naam** en **verbindingsreeks** op voor de **Event Hub-naamruimte**. 

    ```xml
    bootstrap.servers={EVENT HUB NAMESPACE}.servicebus.windows.net:9093
    security.protocol=SASL_SSL
    sasl.mechanism=PLAIN
    sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{CONNECTION STRING for EVENT HUB NAMESPACE}";
    ```

5. Navigeer naar `azure-event-hubs-for-kafka/quickstart/java/producer/src/main/java/` en open het bestand **TestDataReporter.java** in een editor naar keuze. 
6. Voeg commentaar toe aan de volgende regel code:

    ```java
                //final ProducerRecord<Long, String> record = new ProducerRecord<Long, String>(TOPIC, time, "Test Data " + i);
    ```
3. Voeg de volgende regel code toe in plaats van de code met commentaar: 

    ```java
                final ProducerRecord<Long, String> record = new ProducerRecord<Long, String>(TOPIC, time, "{ \"eventData\": \"Test Data " + i + "\" }");            
    ```

    Deze code verzendt de gebeurtenisgegevens in **JSON**-indeling. Wanneer u invoer voor een Stream Analytics-taak configureert, geeft u JSON als de indeling voor de invoergegevens op. 
7. **Voer de producer** uit en stream naar Event Hubs. Schakel op een Windows-computer, wanneer u gebruikmaakt van een **Node.js-opdrachtprompt**, over naar de map `azure-event-hubs-for-kafka/quickstart/java/producer` voordat u deze opdrachten uitvoert. 
   
    ```shell
    mvn clean package
    mvn exec:java -Dexec.mainClass="TestProducer"                                    
    ```

## <a name="verify-that-event-hub-receives-the-data"></a>Controleren of Event Hub de gegevens ontvangt

1. Selecteer **Event Hubs** onder **ENTITEITEN**. Controleer of er een Event Hub met de naam **test** wordt weergegeven. 

    ![Event hub - test](./media/event-hubs-kafka-stream-analytics/test-event-hub.png)
2. Controleer of er berichten worden weergegeven in de Event Hub. 

    ![Event Hub - berichten](./media/event-hubs-kafka-stream-analytics/confirm-event-hub-messages.png)

## <a name="process-event-data-using-a-stream-analytics-job"></a>Gebeurtenisgegevens verwerken met een Stream Analytics-taak
In dit gedeelte maakt u een Azure Stream Analytics-taak. De Kafka-client verzendt gebeurtenissen naar de Event Hub. U maakt een Stream Analytics-taak waarmee gebeurtenisgegevens worden ingevoerd als invoer en naar een Azure Blob-opslag worden uitgevoerd. Als u geen **Azure Storage-account** hebt, [maakt u er een](../storage/common/storage-account-create.md).

De query in de Stream Analytics-taak geeft de gegevens door zonder analyses uit te voeren. U kunt een query maken die de invoergegevens transformeert om uitvoergegevens te produceren in een andere indeling of met verworven inzichten.  

### <a name="create-a-stream-analytics-job"></a>Een Stream Analytics-taak maken 

1. Selecteer **Een resource maken** in de [Azure-portal](https://portal.azure.com).
2. Selecteer **Analytics** in het menu **Azure Marketplace** en selecteer **Stream Analytics-taak**. 
3. Voer op de pagina **Nieuwe Stream Analytics** de volgende acties uit: 
    1. Voer een **naam** voor de taak in. 
    2. Selecteer uw **abonnement**.
    3. Selecteer **Nieuwe maken** voor de **resourcegroep** en voer de naam in. U kunt ook een **bestaande** resourcegroep gebruiken. 
    4. Selecteer een **locatie** voor de taak.
    5. Selecteer **Maken** om de taak te maken. 

        ![Nieuwe Stream Analytics-taak](./media/event-hubs-kafka-stream-analytics/new-stream-analytics-job.png)

### <a name="configure-job-input"></a>Taakinvoer configureren

1. Selecteer in het meldingsbericht **Ga naar resource** om de pagina **Stream Analytics-taak** te bekijken. 
2. Selecteer **Invoer** in het gedeelte **TAAKTOPOLOGIE** in het menu aan de linkerkant.
3. Selecteer **Stroominvoer toevoegen** en selecteer vervolgens **Event Hub**. 

    ![Event Hub toevoegen als invoer](./media/event-hubs-kafka-stream-analytics/select-event-hub-input.png)
4. Voer de volgende handelingen uit op de configuratiepagina **Event Hub-invoer**: 

    1. Geef een **alias** op voor de invoer. 
    2. Selecteer uw **Azure-abonnement**.
    3. Selecteer de **Event Hub-naamruimte** die u eerder hebt gemaakt. 
    4. Selecteer **test** voor de **Event Hub**. 
    5. Selecteer **Opslaan**. 

        ![Configuratie van Event Hub-invoer](./media/event-hubs-kafka-stream-analytics/event-hub-input-configuration.png)

### <a name="configure-job-output"></a>Taakuitvoer configureren 

1. Selecteer **Uitvoer** in het gedeelte **TAAKTOPOLOGIE** in het menu. 
2. Selecteer **+ Toevoegen** op de werkbalk en selecteer **Blob-opslag**
3. Voer de volgende handelingen uit op de pagina met uitvoerinstellingen voor de Blob-opslag: 
    1. Geef een **alias** op voor de uitvoer. 
    2. Selecteer uw Azure-**abonnement**. 
    3. Selecteer uw **Azure Storage-account**. 
    4. Voer een **naam in voor de container** waarin de uitvoergegevens van de Stream Analytics-query worden opgeslagen.
    5. Selecteer **Opslaan**.

        ![Configuratie van Blob Storage-uitvoer](./media/event-hubs-kafka-stream-analytics/output-blob-settings.png)
 

### <a name="define-a-query"></a>Een query definiëren
Nadat u een Stream Analytics-taak zodanig hebt ingesteld dat een binnenkomende gegevensstroom kan worden gelezen, is de volgende stap het maken van een transformatie waarmee gegevens in real-time worden geanalyseerd. U definieert de transformatie-query met [Stream Analytics Query Language](/stream-analytics-query/stream-analytics-query-language-reference). In dit overzicht definieert u een query die de gegevens doorgeeft zonder transformatie uit te voeren.

1. Selecteer **Query**.
2. Vervang in het queryvenster `[YourOutputAlias]` door de uitvoeralias die u eerder hebt gemaakt.
3. Vervang `[YourInputAlias]` door de invoeralias die u eerder hebt gemaakt. 
4. Selecteer **Opslaan** op de werkbalk. 

    ![Schermopname van het queryvenster met waarden voor invoer- en uitvoervariabelen.](./media/event-hubs-kafka-stream-analytics/query.png)


### <a name="run-the-stream-analytics-job"></a>De Stream Analytics-taak uitvoeren

1. Selecteer **Overzicht** in het menu aan de linkerkant. 
2. Selecteer **Starten**. 

    ![Startmenu](./media/event-hubs-kafka-stream-analytics/start-menu.png)
1. Selecteer op de pagina **Taak starten** de optie **Starten**. 

    ![De pagina Taak starten](./media/event-hubs-kafka-stream-analytics/start-job-page.png)
1. Wacht tot de status van de taak is gewijzigd van **Wordt gestart** in **Wordt uitgevoerd**. 

    ![Taakstatus - wordt uitgevoerd](./media/event-hubs-kafka-stream-analytics/running.png)

## <a name="test-the-scenario"></a>Het scenario testen
1. Voer de **Kafka-producer** opnieuw uit om gebeurtenissen naar de Event Hub te verzenden. 

    ```shell
    mvn exec:java -Dexec.mainClass="TestProducer"                                    
    ```
1. Controleer of **uitvoergegevens** zijn gegenereerd in de **Azure Blob-opslag**. U ziet een JSON-bestand in de container met 100 rijen die er uitzien als de volgende voorbeeldrijen: 

    ```
    {"eventData":"Test Data 0","EventProcessedUtcTime":"2018-08-30T03:27:23.1592910Z","PartitionId":0,"EventEnqueuedUtcTime":"2018-08-30T03:27:22.9220000Z"}
    {"eventData":"Test Data 1","EventProcessedUtcTime":"2018-08-30T03:27:23.3936511Z","PartitionId":0,"EventEnqueuedUtcTime":"2018-08-30T03:27:22.9220000Z"}
    {"eventData":"Test Data 2","EventProcessedUtcTime":"2018-08-30T03:27:23.3936511Z","PartitionId":0,"EventEnqueuedUtcTime":"2018-08-30T03:27:22.9220000Z"}
    ```

    In dit scenario heeft de Azure Stream Analytics-taak invoergegevens ontvangen van de Event Hub en opgeslagen in de Azure Blob-opslag. 



## <a name="next-steps"></a>Volgende stappen
In dit artikel hebt u geleerd u hoe u kunt streamen naar Event Hubs zonder uw protocolclients te wijzigen of uw eigen clusters uit te voeren. Zie [Ontwikkelaarshandleiding van Apache Kafka voor Azure Event Hubs](apache-kafka-developer-guide.md) voor meer informatie over Event Hubs voor Apache Kafka.