---
title: Realtime gegevensvisualisatie van uw IoT Hub-gegevens in een web-app
description: Gebruik een webtoepassing om temperatuur- en vochtigheidsgegevens te visualiseren die van een sensor worden verzameld en naar uw IoT-hub worden verzonden.
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
ms.openlocfilehash: 4f2f0678b421ac6965b2848cc25564b4e95c7c6b
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107567045"
---
# <a name="visualize-real-time-sensor-data-from-your-azure-iot-hub-in-a-web-application"></a>Realtime sensorgegevens uit uw Azure IoT-hub visualiseren in een webtoepassing

![End-to-end-diagram](./media/iot-hub-live-data-visualization-in-web-apps/1_iot-hub-end-to-end-diagram.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

In dit artikel leert u hoe u realtime sensorgegevens kunt visualiseren die uw IoT-hub ontvangt met een node.js-web-app die wordt uitgevoerd op uw lokale computer. Nadat u de web-app lokaal hebt uitgevoerd, kunt u desgewenst de stappen volgen om de web-app te hosten in Azure App Service. Zie Use Power BI to [visualize real-time sensor data from Azure IoT Hub](iot-hub-live-data-visualization-in-power-bi.md)(Power BI gebruiken om realtime sensorgegevens van uw IoT-hub te visualiseren met behulp van Power BI Azure IoT Hub ).

## <a name="prerequisites"></a>Vereisten

* Voltooi de [zelfstudie Raspberry Pi Online Simulator](iot-hub-raspberry-pi-web-simulator-get-started.md) of een van de zelfstudies over apparaten. U kunt bijvoorbeeld naar [Raspberry Pi ](iot-hub-raspberry-pi-kit-node-get-started.md) gaan met node.jsof naar een van de quickstarts [Telemetrie](quickstart-send-telemetry-dotnet.md) verzenden. Deze artikelen hebben betrekking op de volgende vereisten:

  * Een actief Azure-abonnement
  * Een IoT-hub onder uw abonnement
  * Een clienttoepassing die berichten naar uw IoT-hub verzendt

* [Git downloaden](https://www.git-scm.com/downloads)

* Voor de stappen in dit artikel wordt uitgenomen dat u een Windows-ontwikkelmachine gebruikt. U kunt deze stappen echter eenvoudig uitvoeren op een Linux-systeem in de shell van uw voorkeur.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

## <a name="add-a-consumer-group-to-your-iot-hub"></a>Een consumentengroep toevoegen aan uw IoT-hub

[Consumentengroepen](../event-hubs/event-hubs-features.md#event-consumers) bieden onafhankelijke weergaven in de gebeurtenisstroom waarmee apps en Azure-services onafhankelijk gegevens van hetzelfde Event Hub-eindpunt kunnen gebruiken. In deze sectie voegt u een consumentengroep toe aan het ingebouwde eindpunt van uw IoT-hub waar de web-app gegevens uit leest.

Voer de volgende opdracht uit om een consumentengroep toe te voegen aan het ingebouwde eindpunt van uw IoT-hub:

```azurecli-interactive
az iot hub consumer-group create --hub-name YourIoTHubName --name YourConsumerGroupName
```

Noteer de naam die u kiest. U hebt deze later in deze zelfstudie nodig.

## <a name="get-a-service-connection-string-for-your-iot-hub"></a>Een service-connection string voor uw IoT-hub

IoT-hubs worden gemaakt met verschillende standaardtoegangsbeleidsregels. Een dergelijk beleid is het **servicebeleid,** dat voldoende machtigingen biedt voor een service om de eindpunten van de IoT-hub te lezen en schrijven. Voer de volgende opdracht uit om een connection string voor uw IoT-hub op te halen die voldoet aan het servicebeleid:

```azurecli-interactive
az iot hub show-connection-string --hub-name YourIotHub --policy-name service
```

De connection string ziet er ongeveer als volgt uit:

```javascript
"HostName={YourIotHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}"
```

Noteer de service connection string, u hebt deze later in deze zelfstudie nodig.

## <a name="download-the-web-app-from-github"></a>De web-app downloaden van GitHub

Open een opdrachtvenster en voer de volgende opdrachten in om het voorbeeld te downloaden van GitHub en naar de voorbeeldmap te gaan:

```cmd
git clone https://github.com/Azure-Samples/web-apps-node-iot-hub-data-visualization.git
cd web-apps-node-iot-hub-data-visualization
```

## <a name="examine-the-web-app-code"></a>De code van de web-app bekijken

Open in de map web-apps-node-iot-hub-data-visualization de web-app in uw favoriete editor. Hieronder ziet u de bestandsstructuur die wordt bekeken in VS Code:

![Bestandsstructuur van web-app](./media/iot-hub-live-data-visualization-in-web-apps/web-app-files.png)

Neem even de tijd om de volgende bestanden te bekijken:

* **Server.js** is een script aan de servicezijde dat de websocke en de Event Hub-wrapperklasse initialiseert. Het biedt een callback naar de Event Hub-wrapperklasse die de klasse gebruikt om binnenkomende berichten naar de websocke uit te zenden.

* **Event-hub-reader.js** is een script aan de servicezijde dat verbinding maakt met het ingebouwde eindpunt van de IoT-hub met behulp van de opgegeven connection string en consumentengroep. De DeviceId en EnqueuedTimeUtc worden geëxtraheerd uit metagegevens van binnenkomende berichten en het bericht wordt vervolgens doorgegeven met behulp van de callbackmethode die is geregistreerd door server.js.

* **Chart-device-data.js** is een script aan de clientzijde dat luistert op de websocke, elke DeviceId bijhoudt en de laatste 50 punten met inkomende gegevens voor elk apparaat op bewaart. Vervolgens worden de geselecteerde apparaatgegevens aan het grafiekobject binden.

* **Index.html verwerkt** de indeling van de gebruikersinterface voor de webpagina en verwijst naar de benodigde scripts voor logica aan de clientzijde.

## <a name="configure-environment-variables-for-the-web-app"></a>Omgevingsvariabelen configureren voor de web-app

Als u gegevens uit uw IoT-hub wilt lezen, heeft de web-app de connection string van uw IoT-hub en de naam van de consumentengroep nodig die deze moet doorlezen. Deze tekenreeksen worden in de volgende regels in de volgende regels uit de procesomgeving server.js:

```javascript
const iotHubConnectionString = process.env.IotHubConnectionString;
const eventHubConsumerGroup = process.env.EventHubConsumerGroup;
```

Stel de omgevingsvariabelen in het opdrachtvenster in met de volgende opdrachten. Vervang de tijdelijke aanduidingen door de connection string voor uw IoT-hub en de naam van de consumentengroep die u eerder hebt gemaakt. Gebruik de tekenreeksen niet.

```cmd
set IotHubConnectionString=YourIoTHubConnectionString
set EventHubConsumerGroup=YourConsumerGroupName
```

## <a name="run-the-web-app"></a>Voer de web-app uit

1. Zorg ervoor dat uw apparaat wordt uitgevoerd en gegevens verstuurt.

2. Voer in het opdrachtvenster de volgende regels uit om de pakketten waarnaar wordt verwezen te downloaden en te installeren en de website te starten:

   ```cmd
   npm install
   npm start
   ```

3. U ziet uitvoer in de console die aangeeft dat de web-app verbinding heeft met uw IoT-hub en luistert op poort 3000:

   ![Web-app gestart in console](./media/iot-hub-live-data-visualization-in-web-apps/web-app-console-start.png)

## <a name="open-a-web-page-to-see-data-from-your-iot-hub"></a>Open een webpagina om gegevens van uw IoT-hub weer te geven

Open een browser naar `http://localhost:3000` .

Selecteer in **de lijst Een** apparaat selecteren uw apparaat om een grafiek te zien van de laatste 50 temperatuur- en vochtigheidsgegevenspunten die door het apparaat naar uw IoT-hub zijn verzonden.

![Web-app-pagina met realtime temperatuur en vochtigheid](./media/iot-hub-live-data-visualization-in-web-apps/web-page-output.png)

U ziet ook uitvoer in de console met de berichten die uw web-app uitzendt naar de browserclient:  

![Uitvoer van web-app-broadcast op console](./media/iot-hub-live-data-visualization-in-web-apps/web-app-console-broadcast.png)

## <a name="host-the-web-app-in-app-service"></a>De web-app hosten in App Service

De [Web Apps functie van Azure App Service](../app-service/overview.md) biedt een platform as a service (PAAS) voor het hosten van webtoepassingen. Webtoepassingen die worden gehost in Azure App Service kunnen profiteren van krachtige Azure-functies, zoals extra beveiliging, taakverdeling en schaalbaarheid, evenals Azure- en partner DevOps-oplossingen zoals continue implementatie, pakketbeheer, en meer. Azure App Service biedt ondersteuning voor webtoepassingen die zijn ontwikkeld in veel populaire talen en die zijn geïmplementeerd in een Windows- of Linux-infrastructuur.

In deze sectie gaat u een web-app inrichten in App Service en uw code implementeren met behulp van Azure CLI-opdrachten. U vindt de details van de opdrachten die worden gebruikt in de [documentatie van az webapp.](/cli/azure/webapp) Voordat u begint, moet u ervoor zorgen dat u de stappen hebt voltooid om een resourcegroep toe te voegen aan uw [IoT-hub,](#add-a-consumer-group-to-your-iot-hub)een service-connection string voor uw [IoT-hub](#get-a-service-connection-string-for-your-iot-hub)op te halen en de [web-app](#download-the-web-app-from-github)te downloaden van GitHub .

1. Een [App Service definieert](../app-service/overview-hosting-plans.md) een set rekenbronnen voor een app die wordt gehost in App Service uitgevoerd. In deze zelfstudie gebruiken we de developer/gratis laag om de web-app te hosten. Met de gratis laag wordt uw web-app uitgevoerd op gedeelde Windows-resources met andere App Service apps, waaronder apps van andere klanten. Azure biedt ook App Service voor het implementeren van web-apps op Linux-rekenbronnen. U kunt deze stap overslaan als u al een App Service plan hebt dat u wilt gebruiken.

   Voer de volgende opdracht App Service een abonnement te maken met behulp van de gratis Windows-laag. Gebruik dezelfde resourcegroep als de IoT-hub. De naam van uw serviceplan kan hoofdletters, kleine letters, cijfers en afbreekstreeepten bevatten.

   ```azurecli-interactive
   az appservice plan create --name <app service plan name> --resource-group <your resource group name> --sku FREE
   ```

2. U kunt nu een web-app inrichten in App Service abonnement. Met de parameter kan de code van de web-app worden geüpload en geïmplementeerd vanuit een `--deployment-local-git` Git-opslagplaats op uw lokale computer. De naam van uw web-app moet wereldwijd uniek zijn en mag hoofdletters, kleine letters, cijfers en afbreekstreeken bevatten. Zorg ervoor dat u knooppuntversie 10.6 of hoger opgeeft voor de parameter, afhankelijk van de versie van de `--runtime` Node.js runtime die u gebruikt. U kunt de opdracht `az webapp list-runtimes` gebruiken om een lijst met ondersteunde runtimes op te halen.

   ```azurecli-interactive
   az webapp create -n <your web app name> -g <your resource group name> -p <your app service plan name> --runtime "node|10.6" --deployment-local-git
   ```

3. Voeg nu Toepassingsinstellingen toe voor de omgevingsvariabelen die de IoT-hub-connection string en de Event Hub-consumentengroep opgeven. Afzonderlijke instellingen zijn spaties die zijn scheidingstekens in de `-settings` parameter . Gebruik de service-connection string voor uw IoT-hub en de consumentengroep die u eerder in deze zelfstudie hebt gemaakt. Geef de waarden niet op.

   ```azurecli-interactive
   az webapp config appsettings set -n <your web app name> -g <your resource group name> --settings EventHubConsumerGroup=<your consumer group> IotHubConnectionString="<your IoT hub connection string>"
   ```

4. Schakel het protocol Web Sockets voor de web-app in en stel de web-app in op het ontvangen van alleen HTTPS-aanvragen (HTTP-aanvragen worden omgeleid naar HTTPS).

   ```azurecli-interactive
   az webapp config set -n <your web app name> -g <your resource group name> --web-sockets-enabled true
   az webapp update -n <your web app name> -g <your resource group name> --https-only true
   ```

5. Als u de code wilt implementeren App Service, gebruikt u uw [implementatiereferenties op gebruikersniveau.](../app-service/deploy-configure-credentials.md) Uw implementatiereferenties op gebruikersniveau verschillen van uw Azure-referenties en worden gebruikt voor lokale Git- en FTP-implementaties naar een web-app. Zodra deze zijn ingesteld, zijn ze geldig voor al uw App Service-apps in alle abonnementen in uw Azure-account. Als u eerder implementatiereferenties op gebruikersniveau hebt ingesteld, kunt u deze gebruiken.

   Als u nog geen implementatiereferenties op gebruikersniveau hebt ingesteld of als u uw wachtwoord niet meer weet, voer dan de volgende opdracht uit. De gebruikersnaam van uw implementatie moet uniek zijn binnen Azure en mag niet het symbool \@ ' ' voor lokale Git-pushes bevatten. Wanneer u hier om wordt gevraagd, voert u het nieuwe wachtwoord in en bevestigt u het. Het wachtwoord moet ten minste acht tekens lang zijn en minimaal twee van de volgende drie typen elementen bevatten: letters, cijfers en symbolen.

   ```azurecli-interactive
   az webapp deployment user set --user-name <your deployment user name>
   ```

6. Haal de Git-URL op die moet worden gebruikt om uw code naar de App Service.

   ```azurecli-interactive
   az webapp deployment source config-local-git -n <your web app name> -g <your resource group name>
   ```

7. Voeg een externe toe aan uw kloon die verwijst naar de Git-opslagplaats voor de web-app in App Service. Gebruik \<Git clone URL\> voor de URL die in de vorige stap is geretourneerd. Voer de volgende opdracht uit in het opdrachtvenster.

   ```cmd
   git remote add webapp <Git clone URL>
   ```

8. Als u de code wilt implementeren App Service, voert u de volgende opdracht in het opdrachtvenster in. Als u om referenties wordt gevraagd, voert u de implementatiereferenties op gebruikersniveau in die u in stap 5 hebt gemaakt. Zorg ervoor dat u naar de main branch van de App Service pusht.

    ```cmd
    git push webapp main:main
    ```

9. De voortgang van de implementatie wordt bijgewerkt in het opdrachtvenster. Een geslaagde implementatie eindigt met regels die vergelijkbaar zijn met de volgende uitvoer:

    ```cmd
    remote:
    remote: Finished successfully.
    remote: Running post deployment command(s)...
    remote: Deployment successful.
    To https://contoso-web-app-3.scm.azurewebsites.net/contoso-web-app-3.git
    6b132dd..7cbc994  main -> main
    ```

10. Voer de volgende opdracht uit om de status van uw web-app op te vragen en te zorgen dat deze wordt uitgevoerd:

    ```azurecli-interactive
    az webapp show -n <your web app name> -g <your resource group name> --query state
    ```

11. Ga naar `https://<your web app name>.azurewebsites.net` in een browser. Een webpagina die vergelijkbaar is met de webpagina die u hebt gezien toen u de web-app lokaal hebt uitgevoerd, wordt weergegeven. Ervan uitgaande dat uw apparaat wordt uitgevoerd en gegevens verstuurt, ziet u een grafiek van de 50 meest recente temperatuur- en vochtigheidsmetingen die door het apparaat zijn verzonden.

## <a name="troubleshooting"></a>Problemen oplossen

Als u problemen met dit voorbeeld tegenkomt, volgt u de stappen in de volgende secties. Als u nog steeds problemen hebt, kunt u ons onder aan dit onderwerp feedback sturen.

### <a name="client-issues"></a>Clientproblemen

* Als een apparaat niet wordt weergegeven in de lijst of als er geen grafiek wordt getekend, moet u ervoor zorgen dat de apparaatcode op uw apparaat wordt uitgevoerd.

* Open in de browser de ontwikkelhulpprogramma's (in veel browsers wordt de F12-sleutel geopend) en zoek de console. Zoek naar eventuele waarschuwingen of fouten die daar worden afgedrukt.

* U kunt fouten opsporen in het script aan de clientzijde in /js/chat-device-data.js.

### <a name="local-website-issues"></a>Problemen met lokale website

* Bekijk de uitvoer in het venster waarin u het knooppunt hebt gestart voor console-uitvoer.

* Fouten opsporen in de servercode, met name server.js en /scripts/event-hub-reader.js.

### <a name="azure-app-service-issues"></a>Azure App Service problemen

* Ga Azure Portal naar uw web-app. Selecteer **onder Bewaking** in het linkerdeelvenster App Service **logboeken.** Schakel **Application Logging (bestandssysteem)** in, stel **Niveau in** op Fout en selecteer vervolgens **Opslaan.** Open vervolgens **Logboekstream** (onder **Bewaking).**

* Selecteer in uw web-app in Azure Portal onder **Ontwikkelprogramma's**  de optie  **Console** en valideer knooppunt- en npm-versies met en `node -v` `npm -v` .

* Als er een foutbericht wordt weergegeven over het niet vinden van een pakket, hebt u de stappen mogelijk niet in de juiste volgorde uitgevoerd. Wanneer de site wordt geïmplementeerd (met ), wordt de app-service uitgevoerd, die wordt uitgevoerd op basis van de huidige versie van het knooppunt `git push` `npm install` dat is geconfigureerd. Als dit later in de configuratie wordt gewijzigd, moet u een betekenisloze wijziging in de code maken en opnieuw pushen.

## <a name="next-steps"></a>Volgende stappen

U hebt uw web-app gebruikt om realtime sensorgegevens van uw IoT-hub te visualiseren.

Zie Azure IoT Hub Use Power BI to visualize real-time sensor data from your IoT hub (Gegevens uit uw IoT-hub Power BI realtime sensorgegevens visualiseren) voor een andere manier om gegevens uit uw [IoT-hub te visualiseren.](iot-hub-live-data-visualization-in-power-bi.md)

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
