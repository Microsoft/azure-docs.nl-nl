---
title: Verbindings gebeurtenissen voor order apparaten fr Azure IoT Hub met Azure Cosmos DB
description: In dit artikel wordt beschreven hoe u apparaat verbindings gebeurtenissen kunt ordenen en vastleggen vanuit Azure IoT Hub met behulp van Azure Cosmos DB om de nieuwste verbindings status te onderhouden
services: iot-hub
ms.service: iot-hub
author: ash2017
ms.topic: conceptual
ms.date: 04/11/2019
ms.author: asrastog
ms.custom: devx-track-azurecli
ms.openlocfilehash: 90b7b6aebfce1c37bef76d371d829048d755e39e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92147276"
---
# <a name="order-device-connection-events-from-azure-iot-hub-using-azure-cosmos-db"></a>Verbindingsgebeurtenissen voor het apparaat aanvragen bij Azure IoT Hub met behulp van Azure Cosmos DB

Azure Event Grid helpt u bij het bouwen van toepassingen op basis van gebeurtenissen en het integreren van IoT-gebeurtenissen in uw bedrijfs oplossingen. In dit artikel wordt stapsgewijs uitgelegd hoe u een configuratie kunt gebruiken voor het bijhouden en opslaan van de laatste verbindings status van een apparaat in Cosmos DB. We gebruiken het Volg nummer dat beschikbaar is op de gebeurtenissen die zijn verbonden met het apparaat en de verbinding met het apparaat is verbroken. de meest recente status wordt opgeslagen in Cosmos DB. We gaan een opgeslagen procedure gebruiken. Dit is een toepassings logica die wordt uitgevoerd op basis van een verzameling in Cosmos DB.

Het Volg nummer is een teken reeks representatie van een hexadecimaal getal. U kunt de teken reeks Compare gebruiken om het grotere getal te identificeren. Als u de teken reeks converteert naar hex, wordt het getal een 256-bits getal. Het Volg nummer wordt strikt verhoogd en de laatste gebeurtenis heeft een hoger getal dan andere gebeurtenissen. Dit is handig als u regel matig apparaten verbindt en de verbinding verbreekt, en u zeker wilt zijn dat alleen de nieuwste gebeurtenis wordt gebruikt voor het activeren van een stroomafwaartse actie, omdat Azure Event Grid de volg orde van gebeurtenissen niet ondersteunt.

## <a name="prerequisites"></a>Vereisten

* Een actief Azure-account. Als u er nog geen hebt, kunt u [een gratis account maken](https://azure.microsoft.com/pricing/free-trial/).

* Een Active Azure Cosmos DB SQL-API-account. Zie [een database account](../cosmos-db/create-sql-api-java.md#create-a-database-account) voor een overzicht maken als u nog geen hebt gemaakt.

* Een verzameling in uw data base. Zie [een verzameling toevoegen](../cosmos-db/create-sql-api-java.md#add-a-container) voor een overzicht. Wanneer u uw verzameling maakt, gebruikt u `/id` voor de partitie sleutel.

* Een IoT Hub in Azure. Als u nog geen hub hebt gemaakt, leest u [Get started with IoT Hub](./quickstart-send-telemetry-dotnet.md) (Aan de slag met IoT Hub) voor stapsgewijze instructies.

## <a name="create-a-stored-procedure"></a>Een opgeslagen procedure maken

Maak eerst een opgeslagen procedure en stel deze in om een logica uit te voeren waarmee de Volg nummers van binnenkomende gebeurtenissen worden vergeleken en de laatste gebeurtenis per apparaat in de data base wordt geregistreerd.

1. Selecteer in uw Cosmos DB SQL-API **Data Explorer**  >  **items**  >  **nieuwe opgeslagen procedure**.

   ![Opgeslagen procedure maken](./media/iot-hub-how-to-order-connection-state-events/create-stored-procedure.png)

2. Voer **LatestDeviceConnectionState** in voor de opgeslagen procedure-id en plak het volgende in de **hoofd tekst van de opgeslagen procedure**. Houd er rekening mee dat deze code alle bestaande code in de hoofd tekst van de opgeslagen procedure moet vervangen. Deze code houdt één rij per apparaat-ID bij en registreert de nieuwste verbindings status van die apparaat-ID door het hoogste Volg nummer te identificeren.

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

1. Selecteer in het [Azure Portal](https://portal.azure.com) **+ een resource maken**, selecteer **integratie** en vervolgens **logische app**.

   ![Logische app maken](./media/iot-hub-how-to-order-connection-state-events/select-logic-app.png)

2. Geef een naam op voor de logische app die uniek is in uw abonnement en selecteer vervolgens het abonnement, de resourcegroep en de locatie van uw IoT-hub.

   ![Nieuwe logische app](./media/iot-hub-how-to-order-connection-state-events/new-logic-app.png)

3. Selecteer **Maken** om de logische app te maken.

   U hebt nu een Azure-resource voor uw logische app gemaakt. Nadat de logische app is geïmplementeerd, toont de ontwerper van logische apps sjablonen voor algemene patronen, zodat u sneller aan de slag kunt.

   > [!NOTE]
   > Als u de logische app opnieuw wilt zoeken en openen, selecteert u **resource groepen** en selecteert u de resource groep die u gebruikt voor deze instructies. Selecteer vervolgens uw nieuwe logische app. Hiermee opent u de ontwerp functie voor logische apps.

4. Schuif in de ontwerp functie van de logische app naar rechts totdat u algemene triggers ziet. Kies onder **sjablonen** de optie **lege logische app** , zodat u uw logische app helemaal zelf kunt bouwen.

### <a name="select-a-trigger"></a>Een trigger selecteren

Een trigger is een specifieke gebeurtenis waarmee uw logische app wordt gestart. Voor deze zelfstudie is de trigger voor het activeren van de werkstroom het ontvangen van een aanvraag via HTTP.

1. Typ **http** en druk op ENTER op de zoek balk connectors en triggers.

2. Selecteer **Aanvraag - Wanneer een HTTP-aanvraag is ontvangen** als de trigger.

   ![Selecteer de trigger Aanvraag - Wanneer een HTTP-aanvraag is ontvangen](./media/iot-hub-how-to-order-connection-state-events/http-request-trigger.png)

3. Selecteer **Voorbeeldnettolading om een schema te genereren**.

   ![Voor beeld-Payload gebruiken om een schema te genereren](./media/iot-hub-how-to-order-connection-state-events/sample-payload.png)

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

   ![Voor beeld van JSON-nettolading plakken](./media/iot-hub-how-to-order-connection-state-events/paste-sample-payload.png)

5. Er kan een melding worden weergegeven dat u niet moet vergeten **in uw aanvraag een header Content-type op te nemen die is ingesteld op application/json**. U kunt deze suggestie zonder problemen negeren en verdergaan met de volgende sectie.

### <a name="create-a-condition"></a>Een voor waarde maken

In de werk stroom van de logische app kunnen voor waarden specifieke acties worden uitgevoerd nadat deze specifieke voor waarde is door gegeven. Als aan de voor waarde wordt voldaan, kan een gewenste actie worden gedefinieerd. Voor deze zelf studie is de voor waarde om te controleren of event type is aangesloten op een apparaat of de verbinding met het apparaat is verbroken. De actie is het uitvoeren van de opgeslagen procedure in uw data base.

1. Selecteer **+ nieuwe stap** en vervolgens **ingebouwd**, en klik vervolgens op **voor waarde** zoeken en selecteren. Klik op **een waarde kiezen** en een vak wordt weer gegeven met de dynamische inhoud: de velden die kunnen worden geselecteerd. Vul de velden in zoals hieronder wordt weer gegeven, zodat deze alleen worden uitgevoerd voor gebeurtenissen die zijn verbonden met een apparaat en die zijn losgekoppeld van het apparaat:

   * Kies een waarde: **Event** type--Selecteer deze in de velden in de dynamische inhoud die worden weer gegeven wanneer u op dit veld klikt.
   * De wijziging is gelijk aan om **eindigt met**.
   * Kies een waarde: **nected**.

     ![Voor waarde voor opvulling](./media/iot-hub-how-to-order-connection-state-events/condition-detail.png)

2. Klik in het dialoog venster **Indien waar** op **een actie toevoegen**.
  
   ![Actie toevoegen indien waar](./media/iot-hub-how-to-order-connection-state-events/action-if-true.png)

3. Zoek naar Cosmos DB en selecteer **Azure Cosmos DB-opgeslagen procedure uitvoeren**

   ![Zoeken naar CosmosDB](./media/iot-hub-how-to-order-connection-state-events/cosmosDB-search.png)

4. Vul de **cosmosdb-verbinding** in voor de naam van de **verbinding** en selecteer de vermelding in de tabel en selecteer vervolgens **maken**. U ziet het paneel **opgeslagen procedure uitvoeren** . Voer de waarden voor de velden in:

   **Data Base-id**: ToDoList

   **Verzamelings-id**: items

   **Sproc-id**: LatestDeviceConnectionState

5. Selecteer **nieuwe para meter toevoegen**. Schakel in de vervolg keuzelijst die wordt weer gegeven de selectie vakjes naast **partitie sleutel** en **para meters voor de opgeslagen procedure** in en klik vervolgens op wille keurig ergens anders op het scherm. Er wordt een veld voor de partitie sleutel waarde en een veld voor de para meters voor de opgeslagen procedure toegevoegd.

   ![Scherm afbeelding toont een item voor het uitvoeren van een opgeslagen procedure waarvoor nieuwe para meter toevoegen is geselecteerd.](./media/iot-hub-how-to-order-connection-state-events/logicapp-stored-procedure.png)

6. Voer nu de partitie sleutel waarde en para meters in zoals hieronder wordt weer gegeven. Zorg ervoor dat u de accolades en dubbele aanhalings tekens plaatst, zoals wordt weer gegeven. Mogelijk moet u klikken op **dynamische inhoud toevoegen** om de geldige waarden te verkrijgen die u hier kunt gebruiken.

   ![Scherm afbeelding toont een uitgevoerde, opgeslagen procedure-item met opgegeven para meters.](./media/iot-hub-how-to-order-connection-state-events/logicapp-stored-procedure-2.png)

7. Zorg ervoor dat boven aan het deel venster waarin wordt vermeld, onder **Selecteer een uitvoer van de vorige stappen**, of de **hoofd tekst** is geselecteerd.

   ![logische app voor-elk invullen](./media/iot-hub-how-to-order-connection-state-events/logicapp-foreach-body.png)

8. Sla uw logische app op.

### <a name="copy-the-http-url"></a>HTTP-URL kopiëren

Voordat u de Logic Apps Designer verlaat, kopieert u de URL waarnaar de logische app luistert naar een trigger. U gebruikt deze URL voor het configureren van Event Grid.

1. Vouw het configuratievak **Wanneer een HTTP-aanvraag is ontvangen** uit door erop te klikken.

2. Kopieer de waarde van **URL voor HTTP POST** door de knop URL kopiëren ernaast te selecteren.

   ![De URL voor HTTP POST kopiëren](./media/iot-hub-how-to-order-connection-state-events/copy-url.png)

3. Sla deze URL op zodat u ernaar kunt verwijzen in de volgende sectie.

## <a name="configure-subscription-for-iot-hub-events"></a>Abonnement voor IoT Hub-gebeurtenissen configureren

In deze sectie configureert u de IoT-hub voor het publiceren van gebeurtenissen op het moment dat deze optreden.

1. Ga in Azure Portal naar uw IoT-hub.

2. Selecteer **Gebeurtenissen**.

   ![Details van gebeurtenisraster weergeven](./media/iot-hub-how-to-order-connection-state-events/event-grid.png)

3. Selecteer **+ gebeurtenis abonnement**.

   ![Nieuw gebeurtenisabonnement maken](./media/iot-hub-how-to-order-connection-state-events/event-subscription.png)

4. Details van **gebeurtenis abonnementen** invullen: Geef een beschrijvende naam op en selecteer **Event grid schema**.

5. Vul de velden voor **gebeurtenis typen** in. Selecteer in de vervolg keuzelijst alleen het **apparaat is verbonden** en het **apparaat is losgekoppeld** van het menu. Klik ergens anders op het scherm om de lijst te sluiten en uw selecties op te slaan.

   ![Gebeurtenis typen instellen die moeten worden gezocht](./media/iot-hub-how-to-order-connection-state-events/set-event-types.png)

6. Selecteer voor **eindpunt Details** het eindpunt type als **webhook** en klik op eind punt selecteren en plak de URL die u hebt gekopieerd uit uw logische app en bevestig de selectie.

   ![Eind punt-URL selecteren](./media/iot-hub-how-to-order-connection-state-events/endpoint-select.png)

7. Het formulier moet er nu uitzien als in het volgende voor beeld:

   ![Voorbeeld van formulier voor gebeurtenisabonnement](./media/iot-hub-how-to-order-connection-state-events/subscription-form.png)

   Selecteer **Maken** om het gebeurtenisabonnement op te slaan.

## <a name="observe-events"></a>Gebeurtenissen observeren

Nu uw gebeurtenis abonnement is ingesteld, kunt u testen door verbinding te maken met een apparaat.

### <a name="register-a-device-in-iot-hub"></a>Een apparaat registreren bij IoT Hub

1. Selecteer **IoT-apparaten** in de IoT-hub.

2. Selecteer **+ toevoegen** boven aan het deel venster.

3. Voor **Apparaat-id**, voert u `Demo-Device-1` in.

4. Selecteer **Opslaan**.

5. U kunt meerdere apparaten met verschillende apparaat-Id's toevoegen.

   ![Apparaten die zijn toegevoegd aan de hub](./media/iot-hub-how-to-order-connection-state-events/AddIoTDevice.png)

6. Klik nogmaals op het apparaat. nu worden de verbindings reeksen en-sleutels ingevuld. Kopieer de **verbindings reeks--primaire sleutel** voor later gebruik.

   ![Connections Tring voor apparaat](./media/iot-hub-how-to-order-connection-state-events/DeviceConnString.png)

### <a name="start-raspberry-pi-simulator"></a>Raspberry Pi Simulator starten

We gebruiken de Raspberry Pi Web Simulator om de verbinding met het apparaat te simuleren.

[Raspberry Pi Simulator starten](https://azure-samples.github.io/raspberry-pi-web-simulator/#Getstarted)

### <a name="run-a-sample-application-on-the-raspberry-pi-web-simulator"></a>Een voorbeeld toepassing uitvoeren op de Raspberry Pi Web Simulator

Hiermee wordt een gebeurtenis die is verbonden met het apparaat geactiveerd.

1. Vervang in het gedeelte code ring de tijdelijke aanduiding in regel 15 door uw Azure IoT Hub-apparaat connection string dat u aan het einde van de vorige sectie hebt opgeslagen.

   ![connection string in apparaat plakken](./media/iot-hub-how-to-order-connection-state-events/raspconnstring.png)

2. Voer de toepassing uit door **uitvoeren** te selecteren.

Er wordt een bericht weer gegeven dat vergelijkbaar is met de volgende uitvoer met de sensor gegevens en de berichten die worden verzonden naar uw IoT-hub.

   ![De toepassing uitvoeren](./media/iot-hub-how-to-order-connection-state-events/raspmsg.png)

   Klik op **stoppen** om de Simulator te stoppen en een gebeurtenis die is verbroken bij het **apparaat** te activeren.

U hebt nu een voorbeeld toepassing uitgevoerd voor het verzamelen van sensor gegevens en deze naar uw IoT-hub te verzenden.

### <a name="observe-events-in-cosmos-db"></a>Gebeurtenissen in Cosmos DB observeren

U kunt de resultaten van de uitgevoerde opgeslagen procedure bekijken in uw Cosmos DB-document. Hier ziet u hoe dat eruit ziet. Elke rij bevat de laatste verbindings status van het apparaat per apparaat.

   ![Resultaten](./media/iot-hub-how-to-order-connection-state-events/cosmosDB-outcome.png)

## <a name="use-the-azure-cli"></a>Azure CLI gebruiken

In plaats van de [Azure Portal](https://portal.azure.com)te gebruiken, kunt u de IOT hub stappen volt ooien met behulp van de Azure cli. Zie de Azure CLI-pagina's voor het [maken van een gebeurtenisabonnement](/cli/azure/eventgrid/event-subscription) en het [maken van een IoT-apparaat](/cli/azure/ext/azure-iot/iot/hub/device-identity#ext-azure-iot-az-iot-hub-device-identity-create) voor meer informatie.

## <a name="clean-up-resources"></a>Resources opschonen

In deze zelfstudie zijn resources gebruikt die kosten voor uw Azure-abonnement met zich meebrengen. Wanneer u klaar bent met de zelfstudie en het testen van de resultaten, moet u daarom de resources uitschakelen of verwijderen die u niet wilt behouden.

Als u het werk aan uw logische app wilt behouden, kunt u de app uitschakelen in plaats van verwijderen.

1. Ga naar uw logische app.

2. Selecteer op de Blade **overzicht** de optie **verwijderen** of **uitschakelen**.

    Elk abonnement biedt toegang tot één gratis IoT-hub. Als u een gratis hub hebt gemaakt voor deze zelfstudie, hoeft u deze niet te verwijderen om te voorkomen dat er kosten in rekening worden gebracht.

3. Ga naar uw IoT-hub.

4. Selecteer op de Blade **overzicht** de optie **verwijderen**.

    Zelfs als u uw IoT-hub wilt behouden, kunt u het gebeurtenisabonnement verwijderen dat u hebt gemaakt.

5. Selecteer hiervoor **Event Grid** in uw IoT-hub.

6. Selecteer het gebeurtenisabonnement dat u wilt verwijderen.

7. Selecteer **Verwijderen**.

Als u een Azure Cosmos DB account uit de Azure Portal wilt verwijderen, klikt u met de rechter muisknop op de naam van het account en klikt u op **account verwijderen**. Zie de gedetailleerde instructies voor [het verwijderen van een Azure Cosmos DB-account](../cosmos-db/how-to-manage-database-account.md).

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [het reageren op IOT hub-gebeurtenissen met behulp van Event grid om acties te activeren](../iot-hub/iot-hub-event-grid.md)

* [De zelf studie voor de IoT Hub-gebeurtenissen proberen](../event-grid/publish-iot-hub-events-to-logic-apps.md)

* Meer informatie over wat u kunt doen met [Event grid](../event-grid/overview.md)