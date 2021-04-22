---
title: Apparaatverbindingsgebeurtenissen bestellen Azure IoT Hub w/Azure Cosmos DB
description: In dit artikel wordt beschreven hoe u verbindingsgebeurtenissen van apparaten van Azure IoT Hub en Azure Cosmos DB om de meest recente verbindingstoestand te onderhouden
services: iot-hub
ms.service: iot-hub
author: ash2017
ms.topic: conceptual
ms.date: 04/11/2019
ms.author: asrastog
ms.custom: devx-track-azurecli
ms.openlocfilehash: bcbfc0941e3c97e96ebc3746b946553e67a10f93
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107878538"
---
# <a name="order-device-connection-events-from-azure-iot-hub-using-azure-cosmos-db"></a>Verbindingsgebeurtenissen voor het apparaat aanvragen bij Azure IoT Hub met behulp van Azure Cosmos DB

Azure Event Grid helpt u bij het bouwen van op gebeurtenissen gebaseerde toepassingen en het eenvoudig integreren van IoT-gebeurtenissen in uw bedrijfsoplossingen. In dit artikel wordt een installatie beschreven die kan worden gebruikt om de meest recente verbindingstoestand van het apparaat bij te houden en op te slaan in Cosmos DB. We gebruiken het volgnummer dat beschikbaar is in de gebeurtenissen Apparaat verbonden en Apparaat is verbroken en slaan de meest recente status op in Cosmos DB. We gaan een opgeslagen procedure gebruiken. Dit is een toepassingslogica die wordt uitgevoerd op een verzameling in Cosmos DB.

Het volgnummer is een tekenreeksweergave van een hexadecimaal getal. U kunt string compare gebruiken om het grotere getal te identificeren. Als u de tekenreeks converteert naar hexx, is het getal een 256-bits getal. Het volgnummer neemt strikt toe en de meest recente gebeurtenis heeft een hoger aantal dan andere gebeurtenissen. Dit is handig als u regelmatig verbinding met het apparaat maakt en de verbinding verbreekt, en u er zeker van wilt zijn dat alleen de meest recente gebeurtenis wordt gebruikt om een downstreamactie te activeren, omdat Azure Event Grid geen ondersteuning biedt voor het orden van gebeurtenissen.

## <a name="prerequisites"></a>Vereisten

* Een actief Azure-account. Als u nog geen account hebt, kunt u [een gratis account maken.](https://azure.microsoft.com/pricing/free-trial/)

* Een actief Azure Cosmos DB SQL API-account. Zie Een [databaseaccount](../cosmos-db/create-sql-api-java.md#create-a-database-account) maken voor een overzicht als u er nog geen hebt gemaakt.

* Een verzameling in uw database. Zie [Een verzameling toevoegen](../cosmos-db/create-sql-api-java.md#add-a-container) voor een overzicht. Wanneer u uw verzameling maakt, gebruikt u `/id` voor de partitiesleutel.

* Een IoT Hub in Azure. Als u nog geen hub hebt gemaakt, leest u [Get started with IoT Hub](./quickstart-send-telemetry-dotnet.md) (Aan de slag met IoT Hub) voor stapsgewijze instructies.

## <a name="create-a-stored-procedure"></a>Een opgeslagen procedure maken

Maak eerst een opgeslagen procedure en stel deze in om een logica uit te voeren die het aantal binnenkomende gebeurtenissen vergelijkt en de meest recente gebeurtenis per apparaat in de database registreert.

1. Selecteer in Cosmos DB SQL-API **Data Explorer**  >  **Items**  >  **Nieuwe opgeslagen procedure.**

   ![Opgeslagen procedure maken](./media/iot-hub-how-to-order-connection-state-events/create-stored-procedure.png)

2. Voer **LatestDeviceConnectionState** in als de opgeslagen procedure-id en plak het volgende in de **body van de opgeslagen procedure.** Houd er rekening mee dat deze code alle bestaande code in de opgeslagen procedure-body moet vervangen. Deze code houdt één rij bij per apparaat-id en registreert de meest recente verbindingstoestand van die apparaat-id door het hoogste reeksnummer te identificeren.

    ```javascript
    // SAMPLE STORED PROCEDURE
    function UpdateDevice(deviceId, moduleId, hubName, connectionState, connectionStateUpdatedTime, sequenceNumber) {
      var collection = getContext().getCollection();
      var response = {};

      var docLink = getDocumentLink(deviceId, moduleId);

      var isAccepted = collection.readDocument(docLink, function(err, doc) {
        if (err) {
          console.log('Cannot find device ' + docLink + ' - ');
          createDocument();
        } else {
          console.log('Document Found - ');
          replaceDocument(doc);
        }
      });

      function replaceDocument(document) {
        console.log(
          'Old Seq :' +
            document.sequenceNumber +
            ' New Seq: ' +
            sequenceNumber +
            ' - '
        );
        if (sequenceNumber > document.sequenceNumber) {
          document.connectionState = connectionState;
          document.connectionStateUpdatedTime = connectionStateUpdatedTime;
          document.sequenceNumber = sequenceNumber;

          console.log('replace doc - ');

          isAccepted = collection.replaceDocument(docLink, document, function(
            err,
            updated
          ) {
            if (err) {
              getContext()
                .getResponse()
                .setBody(err);
            } else {
              getContext()
                .getResponse()
                .setBody(updated);
            }
          });
        } else {
          getContext()
            .getResponse()
            .setBody('Old Event - current: ' + document.sequenceNumber + ' Incoming: ' + sequenceNumber);
        }
      }
      function createDocument() {
        document = {
          id: deviceId + '-' + moduleId,
          deviceId: deviceId,
          moduleId: moduleId,
          hubName: hubName,
          connectionState: connectionState,
          connectionStateUpdatedTime: connectionStateUpdatedTime,
          sequenceNumber: sequenceNumber
        };
        console.log('Add new device - ' + collection.getAltLink());
        isAccepted = collection.createDocument(
          collection.getAltLink(),
          document,
          function(err, doc) {
            if (err) {
              getContext()
                .getResponse()
                .setBody(err);
            } else {
              getContext()
                .getResponse()
                .setBody(doc);
            }
          }
        );
      }

      function getDocumentLink(deviceId, moduleId) {
        return collection.getAltLink() + '/docs/' + deviceId + '-' + moduleId;
      }
    }
    ```

3. Sla de opgeslagen procedure op:

    ![opgeslagen procedure opslaan](./media/iot-hub-how-to-order-connection-state-events/save-stored-procedure.png)

## <a name="create-a-logic-app"></a>Een logische app maken

U gaat eerst een logische app maken en een trigger voor Event Grid toevoegen die de resourcegroep voor uw virtuele machine bewaakt.

### <a name="create-a-logic-app-resource"></a>Een logische app maken

1. Selecteer in [Azure Portal](https://portal.azure.com)+ **Een resource maken,** selecteer **Integratie** en vervolgens **Logische app.**

   ![Logische app maken](./media/iot-hub-how-to-order-connection-state-events/select-logic-app.png)

2. Geef een naam op voor de logische app die uniek is in uw abonnement en selecteer vervolgens het abonnement, de resourcegroep en de locatie van uw IoT-hub.

   ![Nieuwe logische app](./media/iot-hub-how-to-order-connection-state-events/new-logic-app.png)

3. Selecteer **Maken** om de logische app te maken.

   U hebt nu een Azure-resource voor uw logische app gemaakt. Nadat de logische app is geïmplementeerd, toont de ontwerper van logische apps sjablonen voor algemene patronen, zodat u sneller aan de slag kunt.

   > [!NOTE]
   > Als u de logische app opnieuw wilt zoeken en openen, selecteert u **Resourcegroepen** en selecteert u de resourcegroep die u gebruikt voor deze zelf-app. Selecteer vervolgens uw nieuwe logische app. Hiermee opent u de ontwerpfunctie voor logische apps.

4. Schuif in de ontwerpfunctie voor logische apps naar rechts totdat u algemene triggers ziet. Kies **onder Sjablonen** **de optie Lege logische app,** zodat u uw logische app helemaal opnieuw kunt bouwen.

### <a name="select-a-trigger"></a>Een trigger selecteren

Een trigger is een specifieke gebeurtenis waarmee uw logische app wordt gestart. Voor deze zelfstudie is de trigger voor het activeren van de werkstroom het ontvangen van een aanvraag via HTTP.

1. Typ **HTTP** in de zoekbalk voor connectors en triggers en druk op Enter.

2. Selecteer **Aanvraag - Wanneer een HTTP-aanvraag is ontvangen** als de trigger.

   ![Selecteer de trigger Aanvraag - Wanneer een HTTP-aanvraag is ontvangen](./media/iot-hub-how-to-order-connection-state-events/http-request-trigger.png)

3. Selecteer **Voorbeeldnettolading om een schema te genereren**.

   ![Voorbeeldnlading gebruiken om een schema te genereren](./media/iot-hub-how-to-order-connection-state-events/sample-payload.png)

4. Plak de volgende voorbeeldcode van JSON in het tekstvak en selecteer vervolgens **Gereed**:

   ```json
   [{
    "id": "fbfd8ee1-cf78-74c6-dbcf-e1c58638ccbd",
    "topic":
      "/SUBSCRIPTIONS/DEMO5CDD-8DAB-4CF4-9B2F-C22E8A755472/RESOURCEGROUPS/EGTESTRG/PROVIDERS/MICROSOFT.DEVICES/IOTHUBS/MYIOTHUB",
    "subject": "devices/Demo-Device-1",
    "eventType": "Microsoft.Devices.DeviceConnected",
    "eventTime": "2018-07-03T23:20:11.6921933+00:00",
    "data": {
      "deviceConnectionStateEventInfo": {
        "sequenceNumber":
          "000000000000000001D4132452F67CE200000002000000000000000000000001"
      },
      "hubName": "MYIOTHUB",
      "deviceId": "48e44e11-1437-4907-83b1-4a8d7e89859e",
      "moduleId": ""
    },
    "dataVersion": "1",
    "metadataVersion": "1"
   }]
   ```

   ![Voorbeeld van JSON-nettolading plakken](./media/iot-hub-how-to-order-connection-state-events/paste-sample-payload.png)

5. Er kan een melding worden weergegeven dat u niet moet vergeten **in uw aanvraag een header Content-type op te nemen die is ingesteld op application/json**. U kunt deze suggestie zonder problemen negeren en verdergaan met de volgende sectie.

### <a name="create-a-condition"></a>Een voorwaarde maken

In de werkstroom van uw logische app kunnen voorwaarden specifieke acties uitvoeren nadat deze specifieke voorwaarde is doorstaat. Zodra aan de voorwaarde is voldaan, kan een gewenste actie worden gedefinieerd. Voor deze zelfstudie is de voorwaarde om te controleren of eventType is verbonden met het apparaat of dat de verbinding met het apparaat is verbroken. De actie is het uitvoeren van de opgeslagen procedure in uw database.

1. Selecteer **+ Nieuwe stap** en vervolgens **Ingebouwd**, zoek en selecteer **Voorwaarde**. Klik op **Een waarde kiezen.** Er verschijnt een vak met de dynamische inhoud, de velden die kunnen worden geselecteerd. Vul de velden in zoals hieronder wordt weergegeven om dit alleen uit te voeren voor gebeurtenissen Apparaat verbonden en Apparaat is verbroken:

   * Kies een waarde: **eventType.** Selecteer dit in de velden in de dynamische inhoud die worden weergegeven wanneer u op dit veld klikt.
   * Wijzig 'is gelijk aan' om **te eindigen op**.
   * Kies een waarde: **nected**.

     ![Opvulvoorwaarde](./media/iot-hub-how-to-order-connection-state-events/condition-detail.png)

2. Klik in **het dialoogvenster indien** waar op Een actie **toevoegen.**
  
   ![Actie toevoegen indien waar](./media/iot-hub-how-to-order-connection-state-events/action-if-true.png)

3. Zoek naar Cosmos DB en selecteer **Azure Cosmos DB - Opgeslagen procedure uitvoeren**

   ![Zoeken naar CosmosDB](./media/iot-hub-how-to-order-connection-state-events/cosmosDB-search.png)

4. Vul **cosmosdb-connection in als** **verbindingsnaam,** selecteer de vermelding in de tabel en selecteer **vervolgens Maken.** U ziet het **deelvenster Opgeslagen procedure** uitvoeren. Voer de waarden voor de velden in:

   **Database-id:** ToDoList

   **Verzamelings-id:** items

   **Sproc-id:** LatestDeviceConnectionState

5. Selecteer **Nieuwe parameter toevoegen.** Selecteer in de vervolgkeuzelijst die wordt  weergegeven de selectievakjes naast Partitiesleutel en Parameters voor de opgeslagen **procedure** en klik vervolgens op een andere locatie op het scherm; Er wordt een veld toegevoegd voor de partitiesleutelwaarde en een veld voor parameters voor de opgeslagen procedure.

   ![Schermopname toont het item Opgeslagen procedure uitvoeren met Nieuwe parameter toevoegen geselecteerd.](./media/iot-hub-how-to-order-connection-state-events/logicapp-stored-procedure.png)

6. Voer nu de partitiesleutelwaarde en parameters in, zoals hieronder wordt weergegeven. Zorg ervoor dat u de haakjes en dubbele aanhalingstekens tussen de haakjes zet, zoals wordt weergegeven. Mogelijk moet u op Dynamische **inhoud toevoegen klikken om** de geldige waarden op te halen die u hier kunt gebruiken.

   ![Schermopname van het item Opgeslagen procedure uitvoeren met ingevoerde parameters.](./media/iot-hub-how-to-order-connection-state-events/logicapp-stored-procedure-2.png)

7. Boven in het deelvenster met de tekst **For Each**, onder Selecteer een **uitvoer** uit de vorige stappen, moet u ervoor zorgen dat  **Hoofd tekst** is geselecteerd.

   ![logische app voor elke in te vullen](./media/iot-hub-how-to-order-connection-state-events/logicapp-foreach-body.png)

8. Sla uw logische app op.

### <a name="copy-the-http-url"></a>HTTP-URL kopiëren

Voordat u de Logic Apps Designer verlaat, kopieert u de URL waar uw logische app naar luistert voor een trigger. U gebruikt deze URL voor het configureren van Event Grid.

1. Vouw het configuratievak **Wanneer een HTTP-aanvraag is ontvangen** uit door erop te klikken.

2. Kopieer de waarde van **URL voor HTTP POST** door de knop URL kopiëren ernaast te selecteren.

   ![De URL voor HTTP POST kopiëren](./media/iot-hub-how-to-order-connection-state-events/copy-url.png)

3. Sla deze URL op zodat u ernaar kunt verwijzen in de volgende sectie.

## <a name="configure-subscription-for-iot-hub-events"></a>Abonnement voor IoT Hub-gebeurtenissen configureren

In deze sectie configureert u de IoT-hub voor het publiceren van gebeurtenissen op het moment dat deze optreden.

1. Ga in Azure Portal naar uw IoT-hub.

2. Selecteer **Gebeurtenissen**.

   ![Details van gebeurtenisraster weergeven](./media/iot-hub-how-to-order-connection-state-events/event-grid.png)

3. Selecteer **+ Gebeurtenisabonnement.**

   ![Nieuw gebeurtenisabonnement maken](./media/iot-hub-how-to-order-connection-state-events/event-subscription.png)

4. Details van **gebeurtenisabonnement invullen:** geef een beschrijvende naam op en selecteer **Event Grid Schema.**

5. Vul de velden **Gebeurtenistypen** in. Selecteer in de vervolgkeuzelijst alleen **Apparaat verbonden** en **Apparaat is verbroken** in het menu. Klik ergens anders op het scherm om de lijst te sluiten en uw selecties op te slaan.

   ![Gebeurtenistypen instellen om naar te zoeken](./media/iot-hub-how-to-order-connection-state-events/set-event-types.png)

6. Bij **Eindpuntdetails** selecteert u Eindpunttype als **Web hook,** klikt u op Eindpunt selecteren, plakt u de URL die u hebt gekopieerd uit uw logische app en bevestigt u de selectie.

   ![Eindpunt-URL selecteren](./media/iot-hub-how-to-order-connection-state-events/endpoint-select.png)

7. Het formulier moet er nu uitzien zoals in het volgende voorbeeld:

   ![Voorbeeld van formulier voor gebeurtenisabonnement](./media/iot-hub-how-to-order-connection-state-events/subscription-form.png)

   Selecteer **Maken** om het gebeurtenisabonnement op te slaan.

## <a name="observe-events"></a>Gebeurtenissen observeren

Nu uw gebeurtenisabonnement is ingesteld, gaan we testen door een apparaat te verbinden.

### <a name="register-a-device-in-iot-hub"></a>Een apparaat registreren in IoT Hub

1. Selecteer **IoT-apparaten** in de IoT-hub.

2. Selecteer **+Toevoegen** bovenaan het deelvenster.

3. Voor **Apparaat-id**, voert u `Demo-Device-1` in.

4. Selecteer **Opslaan**.

5. U kunt meerdere apparaten met verschillende apparaat-ID's toevoegen.

   ![Apparaten toegevoegd aan hub](./media/iot-hub-how-to-order-connection-state-events/AddIoTDevice.png)

6. Klik opnieuw op het apparaat; nu worden de verbindingsreeksen en sleutels ingevuld. Kopieer de **verbindingsreeks -- primaire sleutel** voor later gebruik.

   ![ConnectionString voor apparaat](./media/iot-hub-how-to-order-connection-state-events/DeviceConnString.png)

### <a name="start-raspberry-pi-simulator"></a>Raspberry Pi-simulator starten

We gaan de Raspberry Pi-websimulator gebruiken om de apparaatverbinding te simuleren.

[Raspberry Pi-simulator starten](https://azure-samples.github.io/raspberry-pi-web-simulator/#Getstarted)

### <a name="run-a-sample-application-on-the-raspberry-pi-web-simulator"></a>Een voorbeeldtoepassing uitvoeren op de Raspberry Pi-websimulator

Hiermee activeert u een gebeurtenis die is verbonden met het apparaat.

1. Vervang in het coderingsgebied de tijdelijke aanduiding in regel 15 door de Azure IoT Hub-connection string die u aan het einde van de vorige sectie hebt opgeslagen.

   ![Apparaat-connection string](./media/iot-hub-how-to-order-connection-state-events/raspconnstring.png)

2. Voer de toepassing uit door **Uitvoeren te selecteren.**

U ziet iets dat lijkt op de volgende uitvoer met de sensorgegevens en de berichten die naar uw IoT-hub worden verzonden.

   ![De toepassing uitvoeren](./media/iot-hub-how-to-order-connection-state-events/raspmsg.png)

   Klik **op Stoppen** om de simulator te stoppen en een gebeurtenis Apparaat is **verbroken te** activeren.

U hebt nu een voorbeeldtoepassing uitgevoerd om sensorgegevens te verzamelen en naar uw IoT-hub te verzenden.

### <a name="observe-events-in-cosmos-db"></a>Gebeurtenissen in de Cosmos DB

U kunt de resultaten van de uitgevoerde opgeslagen procedure bekijken in Cosmos DB document. Hier ziet u hoe dat eruit ziet. Elke rij bevat de meest recente apparaatverbindingstoestand per apparaat.

   ![Resultaat](./media/iot-hub-how-to-order-connection-state-events/cosmosDB-outcome.png)

## <a name="use-the-azure-cli"></a>Azure CLI gebruiken

In plaats van de [Azure Portal,](https://portal.azure.com)kunt u de IoT Hub uitvoeren met behulp van de Azure CLI. Zie de Azure CLI-pagina's voor het [maken van een gebeurtenisabonnement](/cli/azure/eventgrid/event-subscription) en het [maken van een IoT-apparaat](/cli/azure/iot/hub/device-identity#az_iot_hub_device_identity_create) voor meer informatie.

## <a name="clean-up-resources"></a>Resources opschonen

In deze zelfstudie zijn resources gebruikt die kosten voor uw Azure-abonnement met zich meebrengen. Wanneer u klaar bent met de zelfstudie en het testen van de resultaten, moet u daarom de resources uitschakelen of verwijderen die u niet wilt behouden.

Als u het werk aan uw logische app wilt behouden, kunt u de app uitschakelen in plaats van verwijderen.

1. Ga naar uw logische app.

2. Selecteer verwijderen **of**  uitschakelen op de blade **Overzicht.**

    Elk abonnement biedt toegang tot één gratis IoT-hub. Als u een gratis hub hebt gemaakt voor deze zelfstudie, hoeft u deze niet te verwijderen om te voorkomen dat er kosten in rekening worden gebracht.

3. Ga naar uw IoT-hub.

4. Selecteer verwijderen **op de** blade **Overzicht.**

    Zelfs als u uw IoT-hub wilt behouden, kunt u het gebeurtenisabonnement verwijderen dat u hebt gemaakt.

5. Selecteer hiervoor **Event Grid** in uw IoT-hub.

6. Selecteer het gebeurtenisabonnement dat u wilt verwijderen.

7. Selecteer **Verwijderen**.

Als u een Azure Cosmos DB account wilt verwijderen uit Azure Portal, klikt u met de rechtermuisknop op de accountnaam en klikt u **op Account verwijderen.** Zie gedetailleerde instructies voor [het verwijderen van een Azure Cosmos DB account](../cosmos-db/how-to-manage-database-account.md).

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [reageren op IoT Hub gebeurtenissen met behulp van Event Grid acties te activeren](../iot-hub/iot-hub-event-grid.md)

* [Probeer de zelfstudie IoT Hub gebeurtenissen](../event-grid/publish-iot-hub-events-to-logic-apps.md)

* Meer informatie over wat u nog meer kunt doen [met Event Grid](../event-grid/overview.md)