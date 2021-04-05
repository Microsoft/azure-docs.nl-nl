---
title: 'Zelfstudie: Azure Logic Apps triggeren met IoT Hub-gebeurtenissen'
description: In deze zelfstudie leert u hoe u de functie voor het routeren van gebeurtenissen van Azure Event Grid gebruikt voor het maken van geautomatiseerde processen om acties van Azure Logic Apps uit te voeren op basis van IoT Hub-gebeurtenissen.
services: iot-hub, event-grid
author: philmea
ms.service: iot-hub
ms.topic: tutorial
ms.date: 09/14/2020
ms.author: philmea
ms.custom: devx-track-azurecli
ms.openlocfilehash: 3772988e9bf4f733323adae6c0527c8b811c4d04
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98624453"
---
# <a name="tutorial-send-email-notifications-about-azure-iot-hub-events-using-event-grid-and-logic-apps"></a>Zelfstudie: E-mailmeldingen over gebeurtenissen van Azure IoT Hub verzenden met Event Grid en Logic Apps

Azure Event Grid maakt het mogelijk om te reageren op gebeurtenissen in IoT Hub door acties in zakelijke toepassingen verderop in de werkstroom te activeren.

In dit artikel wordt u stapsgewijs begeleid bij het maken van een voorbeeldconfiguratie waarin IoT Hub en Event Grid worden gebruikt. Aan het einde van het artikel beschikt u over een logische Azure-app die een e-mailmelding verstuurt zodra er een apparaat verbinding maakt met uw IoT-hub of deze verbinding wordt verbroken. Event Grid kan worden gebruikt om tijdig meldingen te ontvangen over kritieke apparaten die de verbinding verbreken. Het kan enkele minuten (dat wil zeggen 20 of meer, maar we willen niet er geen waarde aan hangen) duren voordat metrische gegevens en diagnostische worden weergegeven in logboeken/waarschuwingen. Dat is mogelijk onaanvaardbaar voor kritieke infrastructuur.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Vereisten

* Een e-mailaccount van een e-mailprovider die door Azure Logic Apps wordt ondersteund, bijvoorbeeld Office 365 Outlook of Outlook.com. Dit e-mailaccount wordt gebruikt voor het verzenden van de gebeurtenismeldingen.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

## <a name="create-an-iot-hub"></a>Een IoT-hub maken

U kunt snel een nieuwe IoT-hub maken met behulp van de Azure Cloud Shell-terminal in de portal.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com). 

1. Klik in de rechterbovenhoek van de pagina en selecteer de knop Cloud Shell.

   ![Knop Cloud Shell](./media/publish-iot-hub-events-to-logic-apps/portal-cloud-shell.png)

1. Voer de volgende opdracht uit om een nieuwe resourcegroep te maken:

   ```azurecli
   az group create --name {your resource group name} --location westus
   ```
    
1. Voer de volgende opdracht uit om een IoT-hub te maken:

   ```azurecli
   az iot hub create --name {your iot hub name} --resource-group {your resource group name} --sku S1 
   ```

1. Minimaliseer de Cloud Shell-terminal. Verderop in de zelfstudie gaat u terug naar de shell.

## <a name="create-a-logic-app"></a>Een logische app maken

Maak vervolgens een logische app en voeg een HTTP Event grid-trigger toe waarmee aanvragen van de IoT-hub worden verwerkt. 

### <a name="create-a-logic-app-resource"></a>Een logische app maken

1. Selecteer in de [Azure Portal](https://portal.azure.com) **Een resource maken**, typ ' logische app ' in het zoekvak en selecteer Enter. Selecteer **Logische app** in de resultaten.

   ![Logische app maken](./media/publish-iot-hub-events-to-logic-apps/select-logic-app.png)

1. Selecteer **Maken** in het volgende scherm. 

1. Geef een naam op voor de logische app die uniek is in uw abonnement en selecteer vervolgens het abonnement, de resourcegroep en de locatie van uw IoT-hub. 

   ![Velden voor het maken van logische apps](./media/publish-iot-hub-events-to-logic-apps/create-logic-app-fields.png)

1. Selecteer **Controleren + maken**.

1. Controleer de instellingen en selecteer vervolgens **Maken**.

1. Selecteer **Ga naar resource** zodra de resource is gemaakt. 

1. Blader omlaag in de Ontwerper van logische apps naar **Sjablonen**. Kies **Lege logische app**, zodat u de logische app helemaal zelf kunt ontwerpen.

### <a name="select-a-trigger"></a>Een trigger selecteren

Een trigger is een specifieke gebeurtenis waarmee uw logische app wordt gestart. Voor deze zelfstudie is de trigger voor het activeren van de werkstroom het ontvangen van een aanvraag via HTTP.  

1. Typ **HTTP** in de zoekbalk voor connectors en triggers.

1. Scroll door de resultaten en selecteer **Aanvraag - Wanneer een HTTP-aanvraag is ontvangen** als de trigger. 

   ![Selecteer de trigger Aanvraag - Wanneer een HTTP-aanvraag is ontvangen](./media/publish-iot-hub-events-to-logic-apps/http-request-trigger.png)

1. Selecteer **Voorbeeldnettolading om een schema te genereren**. 

   ![Voorbeeldbelasting gebruiken](./media/publish-iot-hub-events-to-logic-apps/sample-payload.png)

1. Plak de JSON-code *Apparaat verbonden-gebeurtenisschema* in het tekstvak en selecteer vervolgens **Gereed**:

   ```json
     [{  
      "id": "f6bbf8f4-d365-520d-a878-17bf7238abd8",
      "topic": "/SUBSCRIPTIONS/<subscription ID>/RESOURCEGROUPS/<resource group name>/PROVIDERS/MICROSOFT.DEVICES/IOTHUBS/<hub name>",
      "subject": "devices/LogicAppTestDevice",
      "eventType": "Microsoft.Devices.DeviceConnected",
      "eventTime": "2018-06-02T19:17:44.4383997Z",
      "data": {
          "deviceConnectionStateEventInfo": {
            "sequenceNumber":
              "000000000000000001D4132452F67CE200000002000000000000000000000001"
          },
        "hubName": "egtesthub1",
        "deviceId": "LogicAppTestDevice",
        "moduleId" : "DeviceModuleID"
      }, 
      "dataVersion": "1",
      "metadataVersion": "1"
    }]
   ```

   Deze gebeurtenis wordt gepubliceerd wanneer een apparaat is verbonden met een IoT-hub.

> [!NOTE]
> Er kan een melding worden weergegeven dat u niet moet vergeten **in uw aanvraag een header Content-type op te nemen die is ingesteld op application/json**. U kunt deze suggestie zonder problemen negeren en verdergaan met de volgende sectie. 

### <a name="create-an-action"></a>Een actie maken

Acties zijn stappen die worden uitgevoerd nadat de trigger de werkstroom van de logische app heeft gestart. Voor deze zelfstudie is de actie het verzenden van een e-mailmelding via uw e-mailprovider. 

1. Selecteer **Nieuwe stap**. **Kies een actie** in het venster dat wordt geopend.

1. Zoek naar **Outlook**.

1. Zoek en selecteer de bijbehorende connector op basis van uw e-mailprovider. In deze zelfstudie wordt **Outlook.com** gebruikt. De stappen voor andere e-mailproviders zijn vergelijkbaar. 

   ![Connector voor e-mailprovider selecteren](./media/publish-iot-hub-events-to-logic-apps/outlook-step.png)

1. Selecteer de actie **Een e-mail verzenden (V2)** . 

1. Selecteer **Aanmelden** en meld u aan bij uw e-mailaccount. Selecteer **Ja** om de app toegang te geven tot uw gegevens.

1. Bouw uw e-mailsjabloon. 

   * **Aan**: Voer het e-mailadres in om de e-mailmelding te ontvangen. Voor deze zelfstudie gebruikt u een e-mailaccount dat u kunt gebruiken voor het testen. 

   * **Onderwerp**: Vul de tekst voor het onderwerp in. Wanneer u op het tekstvak Onderwerp klikt, kunt u dynamische inhoud selecteren die u wilt toevoegen. In deze zelfstudie wordt bijvoorbeeld `IoT Hub alert: {eventType}` gebruikt. Als u geen dynamische inhoud kunt zien, selecteert u de hyperlink **Dynamische inhoud toevoegen**, waarmee u deze optie in- en uitschakelt.

   * **Hoofdtekst**: Typ hier het onderwerp en de tekst voor uw e-mail. Selecteer JSON-eigenschappen in het selectiehulpmiddel om dynamische inhoud op te nemen op basis van gegevens van gebeurtenissen. Als u de dynamische inhoud niet kunt zien, selecteert u de hyperlink **Dynamische inhoud toevoegen** onder het tekstvak **Hoofdtekst**. Als de velden die u wilt gebruiken niet worden weergegeven, klikt u op *Meer* in het scherm Dynamische inhoud om de velden van de vorige actie op te nemen.

   Uw e-mailsjabloon ziet er nu misschien uit als in dit voorbeeld:

   ![Informatie voor e-mail invullen](./media/publish-iot-hub-events-to-logic-apps/email-content.png)

1. Kies **Ja** in de Logic Apps Designer.  

### <a name="copy-the-http-url"></a>HTTP-URL kopiëren

Voordat u de functie Ontwerper van logische apps verlaat, kopieert u de URL waarnaar uw logische app luistert voor een trigger. U gebruikt deze URL voor het configureren van Event Grid. 

1. Vouw het configuratievak **Wanneer een HTTP-aanvraag is ontvangen** uit door erop te klikken. 

1. Kopieer de waarde van **URL voor HTTP POST** door de knop URL kopiëren ernaast te selecteren. 

   ![De URL voor HTTP POST kopiëren](./media/publish-iot-hub-events-to-logic-apps/copy-url.png)

1. Sla deze URL op zodat u ernaar kunt verwijzen in de volgende sectie. 

## <a name="configure-subscription-for-iot-hub-events"></a>Abonnement voor IoT Hub-gebeurtenissen configureren

In deze sectie configureert u de IoT-hub voor het publiceren van gebeurtenissen op het moment dat deze optreden. 

1. Ga in Azure Portal naar uw IoT-hub. U kunt dit doen door **Resourcegroepen** te selecteren en vervolgens de resourcegroep voor deze zelfstudie te selecteren. Selecteer vervolgens uw IoT-hub in de lijst met resources.

1. Selecteer **Gebeurtenissen**.

   ![Details van gebeurtenisraster weergeven](./media/publish-iot-hub-events-to-logic-apps/event-grid.png)

1. Selecteer **Gebeurtenisabonnement**. 

   ![Nieuw gebeurtenisabonnement maken](./media/publish-iot-hub-events-to-logic-apps/event-subscription.png)

1. Maak het gebeurtenisabonnement met de volgende waarden: 

   1. Ga als volgt te werk in de sectie **GEBEURTENISABONNEMENTDETAILS**:
      1. Geef een **naam** op voor het gebeurtenisabonnement. 
      2. Selecteer **Gebeurtenisrasterschema** voor **Gebeurtenisschema**. 
   2. Ga als volgt te werk in de sectie **ONDERWERPDETAILS**:
      1. Controleer of het **Type onderwerp** is ingesteld op **IoT Hub**. 
      2. Controleer of de naam van de IoT-hub is ingesteld als de waarde voor het veld **Bronresource**. 
      3. Voer een naam in voor het **systeemonderwerp** dat voor u wordt gemaakt. Zie [Overzicht van systeemonderwerpen](system-topics.md) voor meer informatie over systeemonderwerpen.
   3. Ga als volgt te werk in de sectie **GEBEURTENISTYPEN**:
      1. Selecteer de vervolgkeuzelijst **Filteren op gebeurtenistypen**.
      1. Schakel de selectievakjes voor **Apparaat gemaakt** en **Apparaat verwijderd** uit, zodat alleen de selectievakjes **Apparaat verbonden** en **Verbinding met apparaat verbroken** zijn geselecteerd.

         ![abonnementgebeurtenistypen selecteren](./media/publish-iot-hub-events-to-logic-apps/subscription-event-types.png)
   
   4. Ga als volgt te werk in de sectie **EINDPUNTDETAILS**: 
       1. Selecteer **Eindpunttype** als **Webhook**.
       2. Klik op **Eindpunt selecteren**, plak de URL die u hebt gekopieerd vanuit uw logische app en bevestig uw selectie.

         ![eindpunt-URL selecteren](./media/publish-iot-hub-events-to-logic-apps/endpoint-webhook.png)

         Als u klaar bent, moet het deelvenster er als volgt uitzien: 

         ![Voorbeeld van formulier voor gebeurtenisabonnement](./media/publish-iot-hub-events-to-logic-apps/subscription-form.png)

1.  Selecteer **Maken**.

## <a name="simulate-a-new-device-connecting-and-sending-telemetry"></a>Een nieuw apparaat simuleren voor het verbinden en verzenden van telemetrie

Test uw logische app door snel een apparaatverbinding te simuleren met de Azure CLI. 

1. Selecteer de knop Cloud Shell om de terminal opnieuw te openen.

1. Voer de volgende opdracht uit om een identiteit van een gesimuleerd apparaat te maken:
    
     ```azurecli 
    az iot hub device-identity create --device-id simDevice --hub-name {YourIoTHubName}
    ```

1. Voer de volgende opdracht uit om het verbinden van uw apparaat met IoT Hub en het verzenden van telemetrie te simuleren:

    ```azurecli
    az iot device simulate -d simDevice -n {YourIoTHubName}
    ```

1. Wanneer het gesimuleerde apparaat verbinding maakt met IoT Hub, ontvangt u een e-mailbericht met de melding 'DeviceConnected'.

1. Wanneer de simulatie is voltooid, ontvangt u een e-mailbericht met de melding 'DeviceDisconnected'. 

    ![Voorbeeld van waarschuwingse-mail](./media/publish-iot-hub-events-to-logic-apps/alert-mail.png)

## <a name="clean-up-resources"></a>Resources opschonen

In deze zelfstudie zijn resources gebruikt die kosten voor uw Azure-abonnement met zich meebrengen. Wanneer u klaar bent met de zelfstudie en het testen van de resultaten, moet u daarom de resources uitschakelen of verwijderen die u niet wilt behouden. 

Als u alle resources wilt verwijderen die u in deze zelfstudie hebt gemaakt, verwijdert u de resourcegroep. 

1. Selecteer **Resourcegroepen** en selecteer vervolgens de resourcegroep die u hebt gemaakt voor deze zelfstudie.

2. Selecteer **Resourcegroep verwijderen** op het deelvenster Resourcegroep. U wordt gevraagd de naam van de resourcegroep op te geven, waarna u deze kunt verwijderen. Alle resources in de resourcegroep worden ook verwijderd.

## <a name="next-steps"></a>Volgende stappen

* Ga voor meer informatie naar [Reageren op gebeurtenissen van IoT Hub door met behulp van Event Grid acties te triggeren - Preview](../iot-hub/iot-hub-event-grid.md).
* [Informatie over het rangschikken van gebeurtenissen ‘apparaat verbonden’ en ‘verbinding met apparaat verbroken’.](../iot-hub/iot-hub-how-to-order-connection-state-events.md)
* Lees hier meer over wat u kunt doen met [Event Grid](overview.md).

Zie voor een volledige lijst met ondersteunde logische app-connectors de 

> [!div class="nextstepaction"]
> [Overzicht van connectors](/connectors/).