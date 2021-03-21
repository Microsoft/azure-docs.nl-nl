---
title: Realtime gegevens visualisatie van uw IoT hub-gegevens in een web-app
description: Gebruik een webtoepassing voor het visualiseren van gegevens over de Tempe ratuur en vochtigheid die worden verzameld van een sensor en die naar uw IOT-hub worden verzonden.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 05/31/2019
ms.author: robinsh
ms.custom:
- 'Role: Cloud Development'
- 'Role: Data Analytics'
- devx-track-azurecli
ms.openlocfilehash: 53b5add7526b0c20487e8fe3adb0b8ebe207a2ce
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102200041"
---
# <a name="visualize-real-time-sensor-data-from-your-azure-iot-hub-in-a-web-application"></a>Real-time sensor gegevens visualiseren vanuit uw Azure IoT hub in een webtoepassing

![End-to-end-diagram](./media/iot-hub-live-data-visualization-in-web-apps/1_iot-hub-end-to-end-diagram.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

## <a name="what-you-learn"></a>Wat u leert

In deze zelf studie leert u hoe u real-time sensor gegevens visualiseren die uw IoT-hub ontvangt met een node.js web-app die wordt uitgevoerd op uw lokale computer. Nadat u de web-app lokaal hebt uitgevoerd, kunt u eventueel stappen volgen om de web-app in Azure App Service te hosten. Als u de gegevens in uw IoT-hub wilt visualiseren met behulp van Power BI, raadpleegt u [Power BI voor het visualiseren van realtime-sensor gegevens uit Azure IOT hub](iot-hub-live-data-visualization-in-power-bi.md).

## <a name="what-you-do"></a>Wat u doet

* Een Consumer groep toevoegen aan uw IoT-hub die door de webtoepassing wordt gebruikt om sensor gegevens te lezen
* De web-app-code downloaden van GitHub
* De web-app-code onderzoeken
* Omgevings variabelen configureren voor de IoT Hub artefacten die nodig zijn voor uw web-app
* De web-app op uw ontwikkel computer uitvoeren
* Een webpagina openen om de gegevens van de realtime-Tempe ratuur en de vochtigheid van uw IoT-hub te bekijken
* Beschrijving Azure CLI gebruiken voor het hosten van uw web-app in Azure App Service

## <a name="what-you-need"></a>Wat u nodig hebt

* Voltooi de zelf studie [Raspberry Pi online Simulator](iot-hub-raspberry-pi-web-simulator-get-started.md) of een van de zelf studies van het apparaat. bijvoorbeeld [Raspberry Pi met node.js](iot-hub-raspberry-pi-kit-node-get-started.md). Deze voldoen aan de volgende vereisten:

  * Een actief Azure-abonnement
  * Een IOT-hub onder uw abonnement
  * Een client toepassing die berichten verzendt naar uw IOT-hub

* [Git downloaden](https://www.git-scm.com/downloads)

* In de stappen in dit artikel wordt ervan uitgegaan dat u een Windows-ontwikkel computer hebt. u kunt deze stappen echter eenvoudig uitvoeren op een Linux-systeem in uw voorkeurs shell.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

## <a name="add-a-consumer-group-to-your-iot-hub"></a>Een Consumer groep toevoegen aan uw IoT-hub

[Consumenten groepen](../event-hubs/event-hubs-features.md#event-consumers) bieden onafhankelijke weer gaven in de gebeurtenis stroom die apps en Azure-Services in staat stellen gegevens onafhankelijk van hetzelfde Event hub-eind punt te gebruiken. In deze sectie voegt u een Consumer groep toe aan het ingebouwde eind punt van uw IoT-hub die door de web-app wordt gebruikt om gegevens te lezen.

Voer de volgende opdracht uit om een Consumer groep toe te voegen aan het ingebouwde eind punt van uw IoT-hub:

```azurecli-interactive
az iot hub consumer-group create --hub-name YourIoTHubName --name YourConsumerGroupName
```

Noteer de naam die u kiest. u hebt deze later in deze zelf studie nodig.

## <a name="get-a-service-connection-string-for-your-iot-hub"></a>Een service connection string voor uw IoT-hub ophalen

IoT-hubs worden gemaakt met verschillende standaard beleids regels voor toegang. Een dergelijk beleid is het **service** beleid dat voldoende machtigingen biedt voor een service om de eind punten van de IOT-hub te lezen en te schrijven. Voer de volgende opdracht uit om een connection string voor uw IoT-hub op te halen die voldoet aan het service beleid:

```azurecli-interactive
az iot hub show-connection-string --hub-name YourIotHub --policy-name service
```

De connection string moet er ongeveer als volgt uitzien:

```javascript
"HostName={YourIotHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}"
```

Noteer de service connection string. u hebt deze later in deze zelf studie nodig.

## <a name="download-the-web-app-from-github"></a>De web-app downloaden van GitHub

Open een opdracht venster en voer de volgende opdrachten in om het voor beeld van GitHub te downloaden en de voor beeld-map te wijzigen:

```cmd
git clone https://github.com/Azure-Samples/web-apps-node-iot-hub-data-visualization.git
cd web-apps-node-iot-hub-data-visualization
```

## <a name="examine-the-web-app-code"></a>De web-app-code onderzoeken

Open in de map web-apps-node-IOT-hub-data-visualisatie de web-app in uw favoriete editor. Hieronder ziet u de bestands structuur die in VS code wordt weer gegeven:

![Bestands structuur van web-app](./media/iot-hub-live-data-visualization-in-web-apps/web-app-files.png)

Neem even de tijd om de volgende bestanden te controleren:

* **Server.js** is een script aan de service zijde waarmee de Web-socket en de Event hub-wrapper-klasse worden geïnitialiseerd. Het biedt een call back naar de Event hub wrapper-klasse die de klasse gebruikt om inkomende berichten te verzenden naar de Web-socket.

* **Event-hub-reader.js** is een service script dat verbinding maakt met het ingebouwde eind punt van de IOT-hub met behulp van de opgegeven Connection String en Consumer groep. Hiermee worden de DeviceId-en EnqueuedTimeUtc opgehaald uit meta gegevens voor inkomende berichten en wordt het bericht vervolgens doorgestuurd met de call back methode die is geregistreerd door server.js.

* **Chart-device-data.js** is een script aan de client zijde dat luistert op de Web-socket, houdt elke DeviceID bij en slaat de laatste 50 punten van de inkomende gegevens voor elk apparaat op. Vervolgens worden de geselecteerde apparaatgegevens gebonden aan het grafiek object.

* **Index.html** verwerkt de indeling van de gebruikers interface voor de webpagina en verwijst naar de benodigde scripts voor logica aan client zijde.

## <a name="configure-environment-variables-for-the-web-app"></a>Omgevings variabelen configureren voor de web-app

Als u gegevens van uw IoT-hub wilt lezen, moeten de connection string van uw IoT-hub en de naam van de Consumer-groep die door de web-app moet worden gelezen. Deze teken reeksen worden opgehaald uit de proces omgeving in de volgende regels in server.js:

```javascript
const iotHubConnectionString = process.env.IotHubConnectionString;
const eventHubConsumerGroup = process.env.EventHubConsumerGroup;
```

Stel de omgevings variabelen in het opdracht venster in met de volgende opdrachten. Vervang de waarden van de tijdelijke aanduiding door de service connection string voor uw IoT-hub en de naam van de Consumer-groep die u eerder hebt gemaakt. De teken reeksen worden niet geciteerd.

```cmd
set IotHubConnectionString=YourIoTHubConnectionString
set EventHubConsumerGroup=YourConsumerGroupName
```

## <a name="run-the-web-app"></a>Voer de web-app uit

1. Zorg ervoor dat het apparaat wordt uitgevoerd en dat er gegevens worden verzonden.

2. Voer in het opdracht venster de volgende regels uit om verwijzende pakketten te downloaden en te installeren en de website te starten:

   ```cmd
   npm install
   npm start
   ```

3. U ziet de uitvoer in de-console, waarmee wordt aangegeven dat de web-app is verbonden met uw IoT-hub en luistert op poort 3000:

   ![Web-app gestart op console](./media/iot-hub-live-data-visualization-in-web-apps/web-app-console-start.png)

## <a name="open-a-web-page-to-see-data-from-your-iot-hub"></a>Een webpagina openen om gegevens van uw IoT-hub te bekijken

Open een browser naar `http://localhost:3000` .

Selecteer uw apparaat in de lijst **een apparaat selecteren** om een actieve grafiek te zien van de laatste gegevens punten van 50-Tempe ratuur en vochtigheid die door het apparaat worden verzonden naar uw IOT-hub.

![Pagina Web-app met realtime temperatuur en lucht vochtigheid](./media/iot-hub-live-data-visualization-in-web-apps/web-page-output.png)

U ziet ook de uitvoer in de-console waarin de berichten worden weer gegeven die uw web-app uitzendt naar de browser-client:  

![Uitvoer van web-app-broadcasts op console](./media/iot-hub-live-data-visualization-in-web-apps/web-app-console-broadcast.png)

## <a name="host-the-web-app-in-app-service"></a>De web-app in App Service hosten

De [functie Web apps van Azure app service](../app-service/overview.md) biedt een platform as a Service (PaaS) voor het hosten van webtoepassingen. Webtoepassingen die worden gehost in Azure App Service, kunnen profiteren van krachtige functies van Azure, zoals extra beveiliging, taak verdeling en schaal baarheid, evenals Azure-en partner DevOps oplossingen zoals continue implementatie, pakket beheer, enzovoort. Azure App Service ondersteunt webtoepassingen die in veel populaire talen zijn ontwikkeld en die zijn geïmplementeerd in de Windows-of Linux-infra structuur.

In deze sectie maakt u een web-app in App Service en implementeert u uw code hiervoor met behulp van Azure CLI-opdrachten. U vindt informatie over de opdrachten die worden gebruikt in de [AZ webapp](/cli/azure/webapp) -documentatie. Voordat u begint, moet u ervoor zorgen dat u de stappen hebt voltooid om [een resource groep toe te voegen aan uw IOT-hub](#add-a-consumer-group-to-your-iot-hub), [een service connection string te krijgen voor uw IOT-hub](#get-a-service-connection-string-for-your-iot-hub)en [de web-app te downloaden van github](#download-the-web-app-from-github).

1. Een [app service plan](../app-service/overview-hosting-plans.md) definieert een set reken resources voor een app die wordt gehost in app service om uit te voeren. In deze zelf studie gebruiken we de laag voor ontwikkel aars/gratis om de web-app te hosten. Met de gratis laag wordt uw web-app uitgevoerd op gedeelde Windows-resources met andere App Service-apps, waaronder apps van andere klanten. Azure biedt ook App Service plannen voor het implementeren van web-apps op Linux-reken resources. U kunt deze stap overs Laan als u al een App Service plan hebt dat u wilt gebruiken.

   Voer de volgende opdracht uit om een App Service plan te maken met behulp van de gratis laag van Windows. Gebruik dezelfde resource groep als uw IoT-hub. De naam van uw service abonnement kan bestaan uit hoofd letters, cijfers en afbreek streepjes.

   ```azurecli-interactive
   az appservice plan create --name <app service plan name> --resource-group <your resource group name> --sku FREE
   ```

2. Richt nu een web-app in uw App Service-abonnement in. `--deployment-local-git`Met de para meter kan de web-app-code worden geüpload en geïmplementeerd vanuit een Git-opslag plaats op uw lokale computer. De naam van uw web-app moet globaal uniek zijn en mag alleen bestaan uit hoofd letters, cijfers en afbreek streepjes. Zorg ervoor dat u het knoop punt versie 10,6 of hoger opgeeft voor de `--runtime` para meter, afhankelijk van de versie van de Node.js runtime die u gebruikt. U kunt de `az webapp list-runtimes` opdracht gebruiken om een lijst met ondersteunde Runtimes weer te geven.

   ```azurecli-interactive
   az webapp create -n <your web app name> -g <your resource group name> -p <your app service plan name> --runtime "node|10.6" --deployment-local-git
   ```

3. Voeg nu toepassings instellingen toe voor de omgevings variabelen waarmee de IoT hub-connection string en de Event hub-Consumer groep worden opgegeven. Afzonderlijke instellingen zijn ruimte gescheiden in de `-settings` para meter. Gebruik de service connection string voor uw IoT-hub en de Consumer-groep die u eerder in deze zelf studie hebt gemaakt. De waarden worden niet geciteerd.

   ```azurecli-interactive
   az webapp config appsettings set -n <your web app name> -g <your resource group name> --settings EventHubConsumerGroup=<your consumer group> IotHubConnectionString="<your IoT hub connection string>"
   ```

4. Schakel het web sockets-protocol voor de web-app in en stel de web-app zo in dat alleen HTTPS-aanvragen worden ontvangen (HTTP-aanvragen worden omgeleid naar HTTPS).

   ```azurecli-interactive
   az webapp config set -n <your web app name> -g <your resource group name> --web-sockets-enabled true
   az webapp update -n <your web app name> -g <your resource group name> --https-only true
   ```

5. Als u de code wilt implementeren in App Service, gebruikt u de [referenties voor implementatie op gebruikers niveau](../app-service/deploy-configure-credentials.md). De referenties voor implementatie op gebruikers niveau verschillen van uw Azure-referenties en worden gebruikt voor git lokale en FTP-implementaties naar een web-app. Eenmaal ingesteld zijn ze geldig voor al uw App Service-apps in alle abonnementen in uw Azure-account. Als u eerder implementatie referenties op gebruikers niveau hebt ingesteld, kunt u deze gebruiken.

   Als u geen implementatie referenties op gebruikers niveau hebt ingesteld of als u het wacht woord niet meer weet, voert u de volgende opdracht uit. De gebruikers naam van uw implementatie moet uniek zijn binnen Azure en mag niet het symbool ' \@ ' bevatten voor lokale Git-pushes. Voer uw nieuwe wacht woord in en bevestig dit wanneer u hierom wordt gevraagd. Het wachtwoord moet ten minste acht tekens lang zijn en minimaal twee van de volgende drie typen elementen bevatten: letters, cijfers en symbolen.

   ```azurecli-interactive
   az webapp deployment user set --user-name <your deployment user name>
   ```

6. Haal de Git-URL op die u wilt gebruiken om uw code naar App Service te pushen.

   ```azurecli-interactive
   az webapp deployment source config-local-git -n <your web app name> -g <your resource group name>
   ```

7. Voeg een extern aan uw kloon toe die verwijst naar de Git-opslag plaats voor de web-app in App Service. Voor \<Git clone URL\> gebruikt u de URL die u in de vorige stap hebt geretourneerd. Voer de volgende opdracht uit in het opdracht venster.

   ```cmd
   git remote add webapp <Git clone URL>
   ```

8. Als u de code wilt implementeren in App Service, voert u de volgende opdracht in het opdracht venster in. Als u wordt gevraagd om referenties, voert u de referenties voor implementatie op gebruikers niveau in die u in stap 5 hebt gemaakt. Zorg ervoor dat u pusht naar de hoofd vertakking van de App Service extern.

    ```cmd
    git push webapp main:main
    ```

9. De voortgang van de implementatie wordt bijgewerkt in uw opdracht venster. Een geslaagde implementatie eindigt met regels die vergelijkbaar zijn met de volgende uitvoer:

    ```cmd
    remote:
    remote: Finished successfully.
    remote: Running post deployment command(s)...
    remote: Deployment successful.
    To https://contoso-web-app-3.scm.azurewebsites.net/contoso-web-app-3.git
    6b132dd..7cbc994  main -> main
    ```

10. Voer de volgende opdracht uit om een query uit te voeren op de status van uw web-app en controleer of deze wordt uitgevoerd:

    ```azurecli-interactive
    az webapp show -n <your web app name> -g <your resource group name> --query state
    ```

11. Ga naar `https://<your web app name>.azurewebsites.net` in een browser. Een webpagina die lijkt op de pagina die u hebt gezien wanneer u de web-app lokaal weergeeft. Ervan uitgaande dat uw apparaat wordt uitgevoerd en dat er gegevens worden verzonden, ziet u een actieve plot van de 50 meest recente Tempe ratuur en vochtigheids aflezingen die door het apparaat worden verzonden.

## <a name="troubleshooting"></a>Problemen oplossen

Als u problemen ondervindt met dit voor beeld, probeert u de stappen in de volgende secties. Als u nog steeds problemen ondervindt, kunt u ons feedback sturen onder aan dit onderwerp.

### <a name="client-issues"></a>Client problemen

* Als een apparaat niet wordt weer gegeven in de lijst of als er geen grafiek wordt getekend, controleert u of de apparaatcode op het apparaat wordt uitgevoerd.

* Open in de browser de hulpprogram ma's voor ontwikkel aars (in veel browsers wordt de F12-sleutel geopend) en zoek de console. Zoek naar eventuele waarschuwingen of fouten die daar worden afgedrukt.

* U kunt fouten opsporen in een script aan de client zijde in/JS/chat-device-data.js.

### <a name="local-website-issues"></a>Problemen met de lokale website

* Bekijk de uitvoer in het venster waarin u het knoop punt hebt gestart voor console-uitvoer.

* Fout opsporing voor de server code, met name server.js en/scripts/event-hub-reader.js.

### <a name="azure-app-service-issues"></a>Azure App Service problemen

* Ga in Azure Portal naar uw web-app. Selecteer onder **bewaking** in het linkerdeel venster de optie **app service logboeken**. Schakel **toepassings Logboeken (bestands systeem)** in op, stel **niveau** in op fout en selecteer vervolgens **Opslaan**. Open vervolgens **logboek stroom** (onder **bewaking**).

* Vanuit uw web-app in Azure Portal selecteert u onder **ontwikkelingsprogram Ma's**  **console** en valideert u node-en NPM-versies met `node -v` en `npm -v` .

* Als er een fout bericht wordt weer geven over het niet vinden van een pakket, kunt u de stappen in de juiste volg orde uitvoeren. Wanneer de site wordt geïmplementeerd (met `git push` ), wordt de app service uitgevoerd `npm install` , die wordt uitgevoerd op basis van de huidige versie van het knoop punt dat is geconfigureerd. Als dat later in de configuratie wordt gewijzigd, moet u een betekenisloze wijziging aanbrengen in de code en de push bewerking opnieuw uitvoeren.

## <a name="next-steps"></a>Volgende stappen

U hebt uw web-app gebruikt voor het visualiseren van real-time sensor gegevens van uw IoT-hub.

Voor een andere manier om gegevens van Azure IoT Hub te visualiseren, raadpleegt u [Power bi gebruiken voor het visualiseren van real-time sensor gegevens van uw IOT-hub](iot-hub-live-data-visualization-in-power-bi.md).

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
