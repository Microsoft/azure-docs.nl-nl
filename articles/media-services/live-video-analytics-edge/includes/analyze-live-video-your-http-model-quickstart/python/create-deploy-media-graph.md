---
ms.openlocfilehash: 1b5dd2fb4ef8cb3f6fd169477d9ee82e912c146e
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/28/2021
ms.locfileid: "98956266"
---
### <a name="examine-and-edit-the-sample-files"></a>De voorbeeld bestanden bekijken en bewerken

Als onderdeel van de vereisten hebt u de voorbeeldcode naar een map gedownload. Volg deze stappen om de voorbeeldbestanden te bekijken en te bewerken.

1. Ga in Visual Studio Code naar *src/edge*. U ziet het bestand *.env* en enkele implementatiesjabloonbestanden.

    De implementatiesjabloon verwijst naar het implementatiemanifest voor het edge-apparaat. Deze bevat enkele tijdelijke waarden. Het *.env*-bestand bevat de waarden voor die variabelen.

1. Ga naar de map *src/cloud-to-device-console-app*. Hier ziet u het bestand *appsettings.json* en enkele andere bestanden:

    * ***c2d-console-app.csproj** _: het projectbestand voor Visual Studio Code.
    _ ***operations.json** _: een lijst met de bewerkingen die u het programma wilt laten uitvoeren.
    _ ***Program.cs** _: de voorbeeldprogrammacode. Deze code:

        _ laadt de app-instellingen.
        * Roept directe methoden aan die worden weergegeven door de module Live Video Analytics in IoT Edge. U kunt de module gebruiken om livevideostreams te analyseren door de bijbehorende [directe methoden](../../../direct-methods.md) aan te roepen.
        * Pauzeert, zodat u de uitvoer van het programma kunt controleren in het **TERMINAL**-venster en de gebeurtenissen die zijn gegenereerd door de module kunt controleren in het **UITVOER**-venster.
        * Roept directe methoden aan voor het opschonen van resources.


1. Bewerk het bestand *operations.json*:
    * Wijzig de link naar de graaftopologie:

        `"topologyUrl" : "https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/httpExtension/2.0/topology.json"`

    * Bewerk onder `GraphInstanceSet` de naam van de graaftopologie zodat deze overeenkomt met de waarde in de voorgaande link:

      `"topologyName" : "InferencingWithHttpExtension"`

    * Bewerk de naam onder `GraphTopologyDelete`:

      `"name": "InferencingWithHttpExtension"`

### <a name="generate-and-deploy-the-iot-edge-deployment-manifest"></a>Het IoT Edge-implementatiemanifest genereren en implementeren

1. Klik met de rechtermuisknop op het bestand *src/edge/ deployment.yolov3.template.json* en klik op **IoT Edge-implementatiemanifest genereren**.

    ![IoT Edge-implementatiemanifest genereren](../../../media/quickstarts/generate-iot-edge-deployment-manifest-yolov3.png)  

    Het manifestbestand *deployment.yolov3.amd64.json* wordt gemaakt in de map *src/edge/config*.

1. Als u de quickstart [Beweging detecteren en gebeurtenissen verzenden](../../../detect-motion-emit-events-quickstart.md) hebt voltooid, kunt u deze stap overslaan. 

    Als dat niet het geval is, selecteert u in het deelvenster **AZURE IOT HUB** in de linkerbenedenhoek het pictogram **Meer acties** en selecteert u vervolgens **IoT Hub-verbindingsreeks instellen**. U kunt de tekenreeks uit het bestand *appsettings.json* kopiëren. Als u er zeker van wilt zijn dat u de juiste IoT-hub in Visual Studio Code hebt geconfigureerd, gebruikt u de [opdracht IoT Hub selecteren](https://github.com/Microsoft/vscode-azure-iot-toolkit/wiki/Select-IoT-Hub).
    
    ![IoT Hub-verbindingsreeks instellen](../../../media/quickstarts/set-iotconnection-string.png)

> [!NOTE]
> Mogelijk wordt u gevraagd om ingebouwde eindpunt gegevens voor de IoT Hub op te geven. Als u deze informatie wilt ophalen, gaat u in Azure Portal naar uw IoT Hub en zoekt u naar de optie **ingebouwde eind punten** in het navigatie deel venster links. Klik op deze en zoek naar het **eind punt Event hub** onder **Event hub-compatibel eind punt** . Kopieer en gebruik de tekst in het vak. Het eind punt ziet er ongeveer als volgt uit:  
    ```
    Endpoint=sb://iothub-ns-xxx.servicebus.windows.net/;SharedAccessKeyName=iothubowner;SharedAccessKey=XXX;EntityPath=<IoT Hub name>
    ```

1. Klik met de rechtermuisknop op *src/edge/config/ deployment.yolov3.amd64.json* en selecteer **Implementatie voor één apparaat maken**. 

    ![Implementatie voor één apparaat maken](../../../media/quickstarts/create-deployment-single-device.png)

1. Wanneer u wordt gevraagd om een IoT Hub-apparaat te selecteren, selecteert u **lva-sample-device**.
1. Vernieuw Azure IoT Hub na ongeveer 30 seconden in de linkerbenedenhoek van het venster. Op het edge-apparaat worden nu de volgende geïmplementeerde modules weergegeven:

    * De Live Video Analytics-module met de naam **lvaEdge**
    * De **rtspsim**-module, die een RTSP-server simuleert en fungeert als de bron van een livevideofeed
        > [!NOTE]
        > Bij de bovenstaande stappen wordt ervan uitgegaan dat u gebruikmaakt van de virtuele machine die is gemaakt door het installatie script. Als u in plaats daarvan uw eigen edge-apparaat gebruikt, gaat u naar uw edge-apparaat en voert u de volgende opdrachten uit met **beheerders rechten** om het voor beeld-video bestand dat voor deze Quick Start wordt gebruikt, op te halen en op te slaan:  
        
        ```
        mkdir /home/lvaadmin/samples
        mkdir /home/lvaadmin/samples/input    
        curl https://lvamedia.blob.core.windows.net/public/camera-300s.mkv > /home/lvaadmin/samples/input/camera-300s.mkv  
        chown -R lvaadmin /home/lvaadmin/samples/  
        ```
    * De **yolov3**-module, het YOLOv3-objectdetectiemodel dat computer vision toepast op de afbeeldingen en meerdere klassen objecttypen retourneert
 
      ![Modules die zijn geïmplementeerd op het edge-apparaat](../../../media/quickstarts/yolov3.png)

### <a name="prepare-to-monitor-events"></a>Het bewaken van gebeurtenissen voorbereiden

1. Open in Visual Studio Code het tabblad **Extensies** (of druk op Ctrl+Shift+X) en zoek naar Azure IoT Hub.
1. Klik met de rechtermuisknop en selecteer **Extensie-instellingen**.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="../../../media/run-program/extensions-tab.png" alt-text="Extensie-instellingen":::
1. Zoek 'Uitgebreid bericht tonen' en schakel deze optie in.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="../../../media/run-program/show-verbose-message.png" alt-text="Uitgebreid bericht tonen":::
1. Klik met de rechtermuisknop op het Live Video Analytics-apparaat en selecteer **Bewaking van ingebouwd gebeurteniseindpunt starten**. Deze stap is nodig om de IoT Hub-gebeurtenissen te controleren in het venster **UITVOER** van Visual Studio Code. 

   ![Bewaking starten](../../../media/quickstarts/start-monitoring-iothub-events.png) 

### <a name="run-the-sample-program"></a>Het voorbeeldprogramma uitvoeren

1. Selecteer de toets F5 om een foutopsporingssessie te starten. U ziet de berichten die worden afgedrukt in het venster **TERMINAL**.
1. De code *operations.json* wordt gestart met aanroepen naar de directe methoden `GraphTopologyList` en `GraphInstanceList`. Als u na het voltooien van vorige quickstarts resources hebt opgeschoond, worden er lege lijsten geretourneerd en wordt het proces gepauzeerd. Selecteer de Enter-toets om door te gaan.

   ```
   --------------------------------------------------------------------------
   Executing operation GraphTopologyList
   -----------------------  Request: GraphTopologyList  --------------------------------------------------
   {
   "@apiVersion": "2.0"
   }
   ---------------  Response: GraphTopologyList - Status: 200  ---------------
   {
   "value": []
   }
   --------------------------------------------------------------------------
   Executing operation WaitForInput
   Press Enter to continue
   ```

    In het **TERMINAL**-venster wordt de volgende set aanroepen van directe methoden weergegeven:

     * Een aanroep van `GraphTopologySet` waarin gebruik wordt gemaakt van de voorgaande `topologyUrl`
     * Een aanroep van `GraphInstanceSet` waarin gebruik wordt gemaakt van de volgende hoofdtekst:

         ```
         {
           "@apiVersion": "2.0",
           "name": "Sample-Graph-1",
           "properties": {
             "topologyName": "InferencingWithHttpExtension",
             "description": "Sample graph description",
             "parameters": [
               {
                 "name": "rtspUrl",
                 "value": "rtsp://rtspsim:554/media/camera-300s.mkv"
               },
               {
                 "name": "rtspUserName",
                 "value": "testuser"
               },
               {
                 "name": "rtspPassword",
                 "value": "testpassword"
               }
             ]
           }
         }
         ```

     * Een aanroep van `GraphInstanceActivate` waarmee het graafexemplaar en de videostroom worden gestart
     * Een tweede aanroep van `GraphInstanceList` waarmee wordt weergegeven dat het graafexemplaar wordt uitgevoerd
1. De uitvoer in het **TERMINAL**-venster wordt gepauzeerd bij een `Press Enter to continue`-prompt. Selecteer Enter nog niet. Schuif omhoog om de nettoladingen voor het JSON-antwoord te zien voor de directe methoden die u hebt aangeroepen.
1. Schakel naar het **UITVOER**-venster in Visual Studio Code. Er zijn berichten te zien die door de module Live Video Analytics in IoT Edge worden verzonden naar de IoT-hub. In de volgende sectie van deze quickstart worden deze berichten besproken.
1. Het uitvoeren van de mediagraaf wordt vervolgd en de resultaten worden afgedrukt. Via de RTSP-simulator wordt de bronvideo continu herhaald. Als u de mediagraaf wilt stoppen, keert u terug naar het **TERMINAL**-venster en selecteert u Enter. 

    Met de volgende reeks aanroepen worden resources opgeschoond:
      * Met een aanroep van `GraphInstanceDeactivate` wordt het graafexemplaar gedeactiveerd.
      * Met een aanroep van `GraphInstanceDelete` wordt het exemplaar verwijderd.
      * Met een aanroep van `GraphTopologyDelete` wordt de topologie verwijderd.
      * Met een aanroep van `GraphTopologyList` wordt ten slotte aangegeven dat de lijst leeg is.
