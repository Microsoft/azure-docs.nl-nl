---
title: Problemen met live video Analytics op IoT Edge-Azure oplossen
description: In dit artikel worden de stappen beschreven voor het oplossen van problemen met live video Analytics op IoT Edge.
author: IngridAtMicrosoft
ms.topic: how-to
ms.author: inhenkel
ms.date: 12/04/2020
ms.openlocfilehash: d23294c21d49b1c2ab83c4bf8f110d5d4bc7aafb
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/27/2021
ms.locfileid: "98878287"
---
# <a name="troubleshoot-live-video-analytics-on-iot-edge"></a>Problemen met live video Analytics op IoT Edge oplossen

In dit artikel worden de stappen beschreven voor het oplossen van problemen met live video Analytics (LVA) op Azure IoT Edge.

## <a name="troubleshoot-deployment-issues"></a>Oplossen van implementatieproblemen

### <a name="diagnostics"></a>Diagnostiek

Als onderdeel van uw live video Analytics-implementatie stelt u Azure-resources in zoals IoT Hub en IoT Edge apparaten. Als eerste stap bij het vaststellen van problemen moet u altijd controleren of het apparaat aan de rand juist is ingesteld door de volgende instructies te volgen:

1. [Voer de `check` opdracht uit](../../iot-edge/troubleshoot.md#run-the-check-command).
1. [Controleer uw IOT Edge versie](../../iot-edge/troubleshoot.md#check-your-iot-edge-version).
1. [Controleer de status van de IOT Edge Security Manager en de bijbehorende logboeken](../../iot-edge/troubleshoot.md#check-the-status-of-the-iot-edge-security-manager-and-its-logs).
1. [Bekijk de berichten die door de IOT Edge hub worden verzonden](../../iot-edge/troubleshoot.md#view-the-messages-going-through-the-iot-edge-hub).
1. [Containers opnieuw opstarten](../../iot-edge/troubleshoot.md#restart-containers).
1. [Controleer uw firewall en poort configuratie regels](../../iot-edge/troubleshoot.md#check-your-firewall-and-port-configuration-rules).

### <a name="pre-deployment-issues"></a>Problemen vóór de implementatie

Als de rand infrastructuur nauw keurig is, kunt u problemen met het manifest bestand van de implementatie zoeken. Als u de live video Analytics op IoT Edge module op het IoT Edge-apparaat naast andere IoT-modules wilt implementeren, gebruikt u een implementatie manifest dat de IoT Edge hub, IoT Edge agent en andere modules en de bijbehorende eigenschappen bevat. U kunt de volgende opdracht gebruiken om het manifest bestand te implementeren:

```
az iot edge set-modules --hub-name <iot-hub-name> --device-id lva-sample-device --content <path-to-deployment_manifest.json>
```
Als de JSON-code niet de juiste indeling heeft, wordt mogelijk de volgende fout weer gegeven:   
&nbsp;&nbsp;&nbsp;**Kan JSON niet parseren vanuit bestand: ' <deployment manifest.json> ' voor het argument ' content ' met uitzonde ring: ' extra gegevens: regel 101 kolom 1 (char 5325) '**

Als deze fout optreedt, raden we u aan om de JSON te controleren op ontbrekende vier Kante haken of andere problemen met de structuur van het bestand. Als u de bestands structuur wilt valideren, kunt u een client gebruiken zoals de [invoeg toepassing Notepad + + met JSON Viewer](https://riptutorial.com/notepadplusplus/example/18201/json-viewer) of een online hulp programma, zoals de [json-formatter & validator](https://jsonformatter.curiousconcept.com/).

### <a name="during-deployment-diagnose-with-media-graph-direct-methods"></a>Tijdens de implementatie: problemen vaststellen met media Graph direct-methoden 

Nadat de module live video-analyse op IoT Edge op de juiste wijze is geïmplementeerd op het IoT Edge apparaat, kunt u de media grafiek maken en uitvoeren door [directe methoden](direct-methods.md)aan te roepen.  
>[!NOTE]
>  De directe-methode aanroepen moeten alleen worden gemaakt aan de **`lvaEdge`** module.

U kunt de Azure Portal gebruiken om een diagnose van de media grafiek uit te voeren met behulp van directe methoden:

1. Ga in het Azure Portal naar de IoT-hub die is verbonden met uw IoT Edge apparaat.

1. Zoek naar **automatisch Apparaatbeheer** en selecteer vervolgens **IOT Edge**.  

1. Selecteer in de lijst met rand apparaten het apparaat dat u wilt diagnosticeren.  
         
    ![Scherm afbeelding van de Azure Portal een lijst met Edge-apparaten weer geven](./media/troubleshoot-how-to/lva-sample-device.png)


1. Controleer of de antwoord code *200-OK* is. Andere antwoord codes voor de [IOT Edge runtime](../../iot-edge/iot-edge-runtime.md) zijn onder andere:
    * 400-de implementatie configuratie is onjuist of ongeldig.
    * 417-er is geen implementatie configuratie ingesteld op het apparaat.
    * 412-de schema versie in de implementatie configuratie is ongeldig.
    * 406-het IoT Edge apparaat is offline of stuurt geen status rapporten.
    * 500-er is een fout opgetreden in de IoT Edge-runtime.

    > [!TIP]
    > Als u problemen ondervindt met het uitvoeren van Azure IoT Edge-modules in uw omgeving, gebruikt u **[Azure IOT Edge standaard diagnose stappen](../../iot-edge/troubleshoot.md?preserve-view=true&view=iotedge-2018-06)** als richt lijn voor het oplossen van problemen en diagnostische gegevens.
### <a name="post-deployment-direct-method-error-code"></a>Post-implementatie: fout code directe methode
1. Als u een status krijgt `501 code` , controleert u of de naam van de directe methode nauw keurig is. Als de methode naam en de aanvraag lading nauw keurig zijn, moet u de resultaten ophalen en de succes code = 200. 
1. Als de aanvraag lading onjuist is, krijgt u een status- `400 code` en reactie-nettolading die de fout code en het bericht aangeeft dat de oorzaak van het probleem met de aanroep van de directe methode moet helpen.
    * Door de gerapporteerde en gewenste eigenschappen te controleren, kunt u begrijpen of de module-eigenschappen zijn gesynchroniseerd met de implementatie. Als dat niet het geval is, kunt u uw IoT Edge apparaat opnieuw opstarten. 
    * Gebruik de hand leiding [directe methoden](direct-methods.md) voor het aanroepen van een paar methoden, met name eenvoudige items zoals GraphTopologyList. De hand leiding bevat ook de verwachte nettoladingen voor aanvragen en antwoorden en fout codes. Nadat de eenvoudige directe methoden zijn geslaagd, weet u zeker dat de module live video Analytics IoT Edge functioneel is.
        
       ![Scherm afbeelding van het deel venster directe methode voor de module IoT Edge.](./media/troubleshoot-how-to/direct-method.png) 

1. Als het **opgegeven in** de kolommen implementatie en **gerapporteerd door** de kolom *Ja* aangeeft, kunt u directe methoden aanroepen in de module live video-analyses op IOT Edge. Selecteer de module om naar een pagina te gaan waar u de gewenste en gerapporteerde eigenschappen kunt controleren en direct-methoden aanroept. Houd rekening met het volgende: 

### <a name="post-deployment-diagnose-logs-for-issues-during-the-run"></a>Post-implementatie: diagnose logboeken voor problemen tijdens de uitvoering 

De container logboeken voor uw IoT Edge-module moeten diagnostische gegevens bevatten die u helpen bij het opsporen van fouten in uw problemen tijdens module runtime. U kunt de [container logboeken voor problemen controleren](../../iot-edge/troubleshoot.md#check-container-logs-for-issues) en het probleem zelf vaststellen. 

Als u alle voor gaande controles hebt uitgevoerd en nog steeds problemen ondervindt, kunt u logboeken van het IoT Edge-apparaat verzamelen [met de `support bundle` opdracht](../../iot-edge/troubleshoot.md#gather-debug-information-with-support-bundle-command) voor verdere analyse van het Azure-team. U kunt [contact met ons](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) opnemen voor ondersteuning en de verzamelde logboeken indienen.

## <a name="common-error-resolutions"></a>Oplossingen voor algemene problemen

Live video Analytics wordt geïmplementeerd als een IoT Edge module op het IoT Edge apparaat en werkt samen met de IoT Edge agent-en hub-modules. Enkele veelvoorkomende fouten die u tegen komt bij het implementeren van live video Analytics worden veroorzaakt door problemen met de onderliggende IoT-infra structuur. De fouten zijn onder andere:

* [De IOT Edge-agent stopt na ongeveer een minuut](../../iot-edge/troubleshoot-common-errors.md#iot-edge-agent-stops-after-about-a-minute).
* [De IOT Edge-agent heeft geen toegang tot de installatie kopie van een module (403)](../../iot-edge/troubleshoot-common-errors.md#iot-edge-agent-cant-access-a-modules-image-403).
* [De module IOT Edge agent rapporteert "empty configuratie bestand" en er worden geen modules gestart op het apparaat](../../iot-edge/troubleshoot-common-errors.md#edge-agent-module-reports-empty-config-file-and-no-modules-start-on-the-device).
* [Het starten van de IOT Edge hub mislukt](../../iot-edge/troubleshoot-common-errors.md#iot-edge-hub-fails-to-start).
* [De IOT Edge Security daemon mislukt met een ongeldige hostnaam](../../iot-edge/troubleshoot-common-errors.md#iot-edge-security-daemon-fails-with-an-invalid-hostname).
* [De live video analyse of een andere aangepaste IOT Edge module kan geen bericht verzenden naar de Edge hub met een 404-fout](../../iot-edge/troubleshoot-common-errors.md#iot-edge-module-fails-to-send-a-message-to-edgehub-with-404-error).
* [De module IOT Edge is geïmplementeerd en wordt vervolgens van het apparaat verwijderd](../../iot-edge/troubleshoot-common-errors.md#iot-edge-module-deploys-successfully-then-disappears-from-device).

    > [!TIP]
    > Als u problemen ondervindt met het uitvoeren van Azure IoT Edge-modules in uw omgeving, gebruikt u **[Azure IOT Edge standaard diagnose stappen](../../iot-edge/troubleshoot.md?preserve-view=true&view=iotedge-2018-06)** als richt lijn voor het oplossen van problemen en diagnostische gegevens.
### <a name="live-video-analytics-working-with-external-modules"></a>Live video Analytics met externe modules

Live video-analyses via de media Graph-extensie processors kunnen de media grafiek uitbreiden voor het verzenden en ontvangen van gegevens van andere IoT Edge modules met behulp van HTTP-of gRPC-protocollen. In een [specifiek voor beeld](https://github.com/Azure/live-video-analytics/tree/master/MediaGraph/topologies/httpExtension)kan deze media grafiek video frames als installatie kopieën verzenden naar een externe Afleidings module, zoals Yolo v3, en analyse van op JSON gebaseerde analyses ontvangen met behulp van het HTTP-protocol. In een dergelijke topologie is het doel voor de gebeurtenissen voornamelijk de IoT-hub. In situaties waarin de gebeurtenissen voor afnemen op de hub niet worden weer geven, controleert u het volgende:

* Controleer of de hub waarnaar de media grafiek wordt gepubliceerd en de hub die u bekijkt hetzelfde zijn. Wanneer u meerdere implementaties maakt, kan het zijn dat u met meerdere hubs kunt eindigen en per ongeluk de verkeerde hub kunt controleren op gebeurtenissen.
* Controleer in Azure Portal of de externe module is geïmplementeerd en wordt uitgevoerd. In de voorbeeld afbeelding hier zijn rtspsim, yolov3, tinyyolov3 en logAnalyticsAgent IoT Edge modules die extern worden uitgevoerd op de lvaEdge-module.

    [![Scherm opname van de actieve status van modules in Azure IOT hub. ](./media/troubleshoot-how-to/iot-hub-azure.png)](./media/troubleshoot-how-to/iot-hub-azure.png#lightbox)

* Controleer of u gebeurtenissen naar het juiste URL-eind punt verzendt. De externe AI-container maakt een URL en een poort beschikbaar die deze ontvangt en retourneert de gegevens van POST-aanvragen. Deze URL wordt opgegeven als een `endpoint: url` eigenschap voor de HTTP-extensie processor. Zoals in de [topologie-URL](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/httpExtension/2.0/topology.json)wordt weer gegeven, wordt het eind punt ingesteld op de para meter voor de deactieve URL. Zorg ervoor dat de standaard waarde voor de para meter of de door gegeven waarde nauw keurig is. U kunt controleren of het werkt met behulp van de client-URL (krul).  

    Hier volgt een voor beeld van een Yolo v3-container die wordt uitgevoerd op een lokale computer met het IP-adres 172.17.0.3.  
    
    ```
    curl -X POST http://172.17.0.3/score -H "Content-Type: image/jpeg" --data-binary @<fullpath to jpg>
    ```

    Geretourneerd resultaat:

    ```
    {"inferences": [{"type": "entity", "entity": {"tag": {"value": "car", "confidence": 0.8668569922447205}, "box": {"l": 0.3853073438008626, "t": 0.6063712999658677, "w": 0.04174524943033854, "h": 0.02989496027381675}}}]}
    ```
    > [!TIP]
    > Gebruik de **[opdracht docker inspecte](https://docs.docker.com/engine/reference/commandline/inspect/)** om het IP-adres van de computer te vinden.
    
* Als u een of meer exemplaren uitvoert van een grafiek die gebruikmaakt van de media Graph-extensie processor, moet u het `samplingOptions` veld gebruiken om het aantal frames per seconde (fps) van de videofeed te beheren. 

   * In bepaalde situaties, waarbij de CPU of het geheugen van de Edge-computer zeer intensief wordt gebruikt, kunt u bepaalde gebeurtenissen voor de afleiding verliezen. Om dit probleem op te lossen, stelt u een lage waarde in voor de `maximumSamplesPerSecond` eigenschap van het `samplingOptions` veld. U kunt dit instellen op 0,5 ("maximumSamplesPerSecond": "0,5") voor elk exemplaar van de grafiek en vervolgens het exemplaar opnieuw uitvoeren om te controleren op gebeurtenissen voor inschakeling op de hub.
    
### <a name="multiple-direct-methods-in-parallel--timeout-failure"></a>Meerdere directe methoden parallel: time-out-fout 

Live video Analytics op IoT Edge biedt een directe, op methode gebaseerd programmeer model waarmee u meerdere topologieën en meerdere grafiek exemplaren kunt instellen. Als onderdeel van de topologie en de grafiek instellingen roept u meerdere directe-methode aanroepen aan in de module IoT Edge. Als u deze meerdere methode-aanroepen parallel aanroept, met name de verbindingen die de grafieken starten en stoppen, kan er een time-outfout optreden, zoals de volgende: 

De initialisatie methode voor assembly Microsoft.Media.LiveVideoAnalytics.Test.Feature.Edge.AssemblyInitializer.InitializeAssemblyAsync heeft een uitzonde ring gegenereerd. Micro soft. Azure. devices. common. exceptions. IotHubException: micro soft. Azure. devices. common. exceptions. IotHubException:<br/> `{"Message":"{\"errorCode\":504101,\"trackingId\":\"55b1d7845498428593c2738d94442607-G:32-TimeStamp:05/15/2020 20:43:10-G:10-TimeStamp:05/15/2020 20:43:10\",\"message\":\"Timed out waiting for the response from device.\",\"info\":{},\"timestampUtc\":\"2020-05-15T20:43:10.3899553Z\"}","ExceptionMessage":""}. Aborting test execution. `

U wordt aangeraden directe methoden parallel *niet* te bellen. U kunt ze sequentieel aanroepen (dat wil zeggen, een directe methode aanroepen nadat de vorige is voltooid).

### <a name="collect-logs-for-submitting-a-support-ticket"></a>Logboeken verzamelen voor het indienen van een ondersteunings ticket

Wanneer u het probleem niet kunt oplossen met de zelf-begeleide stappen voor probleem oplossing, gaat u naar Azure Portal en [opent u een ondersteunings ticket](../../azure-portal/supportability/how-to-create-azure-support-request.md).

> [!WARNING]
> De logboeken kunnen persoons gegevens (PII) bevatten, zoals uw IP-adres. Alle lokale kopieën van de logboeken worden verwijderd zodra de beoordeling is voltooid en het ondersteunings ticket wordt gesloten.  

Als u de relevante logboeken wilt verzamelen die aan het ticket moeten worden toegevoegd, volgt u de onderstaande instructies in volg orde en uploadt u de logboek bestanden in het **detail** venster van de ondersteunings aanvraag.  
1. [De live video Analytics-module configureren voor het verzamelen van uitgebreide logboeken](#configure-live-video-analytics-module-to-collect-verbose-logs)
1. [Logboeken voor fout opsporing inschakelen](#live-video-analytics-debug-logs)
1. Reproduceer het probleem
1. Verbinding maken met de virtuele machine via de **IOT hub** pagina in de portal
    1. Zip alle bestanden in de map *debugLogs* .

       > [!NOTE]
       > Deze logboek bestanden zijn niet bedoeld voor zelf-diagnose. Ze zijn bedoeld voor het technische team van Azure om uw problemen te analyseren.

       * Vervang in de volgende opdracht **$DEBUG _LOG_LOCATION_ON_EDGE_DEVICE** door de locatie van de logboeken voor fout opsporing op het apparaat Edge dat u eerder in **stap 2** hebt ingesteld.  

           ```
           sudo apt install zip unzip  
           zip -r debugLogs.zip $DEBUG_LOG_LOCATION_ON_EDGE_DEVICE 
           ```

    1. Koppel het *debugLogs.zip* -bestand aan het ondersteunings ticket.
1. Voer de [opdracht voor de ondersteunings bundel](#use-the-support-bundle-command)uit, verzamel de logboeken en koppel deze aan het ondersteunings ticket.

### <a name="configure-live-video-analytics-module-to-collect-verbose-logs"></a>Live video Analytics-module configureren voor het verzamelen van uitgebreide logboeken
Configureer uw live video Analytics-module om uitgebreide logboeken te verzamelen door de `logLevel` en als volgt in te stellen `logCategories` :
```
"logLevel": "Verbose",
"logCategories": "Application,Events,MediaPipeline",
```

U kunt dit doen in een van de volgende:
* In **Azure Portal**, door de dubbele eigenschappen van de module identiteit van de module module identiteit van live video Analytics te updaten   [ ![ . ](media/troubleshoot-how-to/module-twin.png)](media/troubleshoot-how-to/module-twin.png#lightbox)    
* Of in het **manifest** bestand van de implementatie kunt u deze vermeldingen toevoegen in het knoop punt eigenschappen van de module live video analyse

### <a name="use-the-support-bundle-command"></a>De ondersteunings bundel opdracht gebruiken

Wanneer u logboeken van een IoT Edge apparaat wilt verzamelen, is het de eenvoudigste manier om de `support-bundle` opdracht te gebruiken. Met deze opdracht wordt verzameld:

- Module Logboeken
- IoT Edge Security Manager en container engine-logboeken
- JSON-uitvoer IoT Edge controleren
- Nuttige informatie over fout opsporing

1. Voer de `support-bundle` opdracht uit met de vlag *--sindsdien* om op te geven hoeveel tijd de logboeken moeten omvatten. Zo ontvangt 2H logboeken voor de afgelopen twee uur. U kunt de waarde van deze vlag wijzigen zodat logboeken voor verschillende Peri Oden worden toegevoegd.

    ```
    sudo iotedge support-bundle --since 2h
    ```

   Met deze opdracht maakt u een bestand met de naam *support_bundle.zip* in de map waarin u de opdracht hebt uitgevoerd. 
   
1. Koppel het *support_bundle.zip* -bestand aan het ondersteunings ticket.

### <a name="live-video-analytics-debug-logs"></a>Logboeken voor fout opsporing in live video Analytics

Ga als volgt te werk om de module live video-analyse op IoT Edge te configureren voor het genereren van Logboeken voor fout opsporing:

1. Meld u aan bij de [Azure Portal](https://portal.azure.com)en ga naar uw IOT-hub.
1. Selecteer **IOT Edge** in het linkerdeel venster.
1. Selecteer de ID van het doel apparaat in de lijst met apparaten.
1. Selecteer boven in het deel venster de optie **modules instellen**.

   ![Scherm opname van de knop ' modules instellen ' in de Azure Portal.](media/troubleshoot-how-to/set-modules.png)

1. Zoek in de sectie **IOT Edge modules** naar en selecteer **lvaEdge**.
1. Selecteer de optie voor het **maken van containers**.
1. Voeg in de sectie **bindingen** de volgende opdracht toe:

    `/var/local/mediaservices/logs:/var/lib/azuremediaservices/logs`

    > [!NOTE] 
    > Met deze opdracht worden de logboeken-mappen tussen het rand apparaat en de container gebonden. Als u de logboeken op een andere locatie wilt verzamelen, gebruikt u de volgende opdracht, waarbij u **$LOG _LOCATION_ON_EDGE_DEVICE** vervangt door de locatie die u wilt gebruiken: `/var/$LOG_LOCATION_ON_EDGE_DEVICE:/var/lib/azuremediaservices/logs`

1. Selecteer **Update**.
1. Selecteer **Controleren + maken**. Een geslaagd validatie bericht wordt geplaatst onder een groene banner.
1. Selecteer **Maken**.
1. Update **module-identiteit** , te verwijzen naar de para meter DebugLogsDirectory, die verwijst naar de map waarin de logboeken worden verzameld:

    a. Selecteer in de tabel **modules** de optie **lvaEdge**.  
    b. Selecteer boven in het deel venster **module identiteit, twee**. Een bewerkbaar deel venster wordt geopend.  
    c. Voeg onder **gewenste sleutel** de volgende sleutel/waarde-paar toe:  
    `"DebugLogsDirectory": "/var/lib/azuremediaservices/logs"`

    > [!NOTE] 
    > Met deze opdracht worden de logboeken-mappen tussen het rand apparaat en de container gebonden. Als u de logboeken op een andere locatie op het apparaat wilt verzamelen:
    > 1. Maak een binding voor de locatie van het logboek voor fout opsporing in het gedeelte **bindingen** en vervang de **$DEBUG _LOG_LOCATION_ON_EDGE_DEVICE** en **$debug _LOG_LOCATION** door de gewenste locatie: `/var/$DEBUG_LOG_LOCATION_ON_EDGE_DEVICE:/var/$DEBUG_LOG_LOCATION`
    > 2. Gebruik de volgende opdracht om **$DEBUG _LOG_LOCATION** te vervangen door de locatie die in de vorige stap wordt gebruikt:  
    > `"DebugLogsDirectory": "/var/$DEBUG_LOG_LOCATION"`  
    
    d. Selecteer **Opslaan**.


1. U kunt de logboek verzameling stoppen door de waarde in **module-identiteit** in te stellen op *Null*. Ga terug naar de **dubbele pagina module Identity** en werk de volgende para meter bij als:

    `"DebugLogsDirectory": ""`

### <a name="best-practices-around-logging"></a>Aanbevolen procedures voor logboek registratie

[Bewaking en logboek registratie](monitoring-logging.md) moeten helpen bij het leren van de taxonomie en het genereren van logboeken die helpen bij het oplossen van problemen met LVA. 

Omdat de implementatie van de gRPC-server verschilt tussen talen, is er geen standaard manier om logboek registratie in te voegen in de-server.  

Als u bijvoorbeeld een gRPC-server bouwt met behulp van .NET core, voegt de gRPC-service Logboeken toe onder de categorie **gRPC** . Als u gedetailleerde logboeken van gRPC wilt inschakelen, moet u de Grpc-voor voegsels configureren op het niveau van de fout opsporing in uw appsettings.jsin het bestand door de volgende items toe te voegen aan de sectie LogLevel in logboek registratie: 

```
{ 
  "Logging": { 
    "LogLevel": { 
      "Default": "Debug", 
      "System": "Information", 
      "Microsoft": "Information", 
      "Grpc": "Debug" 
       } 
  } 
} 
``` 

U kunt dit ook configureren in het Startup.cs-bestand met ConfigureLogging: 

```
public static IHostBuilder CreateHostBuilder(string[] args) => 
    Host.CreateDefaultBuilder(args) 
        .ConfigureLogging(logging => 
        { 

           logging.AddFilter("Grpc", LogLevel.Debug); 
        }) 
        .ConfigureWebHostDefaults(webBuilder => 
        { 
            webBuilder.UseStartup<Startup>(); 
        }); 

``` 

[Logboek registratie en diagnostische gegevens in gRPC in .net](/aspnet/core/grpc/diagnostics?preserve-view=true&view=aspnetcore-3.1) bieden enkele richt lijnen voor het verzamelen van Diagnostische logboeken van een gRPC-server. 

### <a name="a-failed-grpc-connection"></a>Een mislukte gRPC-verbinding 

Als een grafiek actief is en streamt vanuit een camera, wordt de verbinding beheerd door live video Analytics. 

### <a name="monitoring-and-balancing-the-load-of-cpu-and-gpu-resources-when-these-resources-become-bottlenecks"></a>Bewaking en verdeling van de belasting van CPU-en GPU-resources wanneer deze bronnen knel punten worden

Live video analyse bewaakt of biedt geen hardware-bron bewaking. Ontwikkel aars moeten de bewakings oplossingen voor hardwarefabrikanten gebruiken. Als u echter Kubernetes-containers gebruikt, kunt u het apparaat bewaken met behulp van het [Kubernetes-dash board](https://kubernetes.io/docs/tasks/access-application-cluster/web-ui-dashboard/). 

gRPC in .NET core-documenten delen ook waardevolle informatie over [Best practices voor prestaties](/aspnet/core/grpc/performance?preserve-view=true&view=aspnetcore-3.1) en [taak verdeling](/aspnet/core/grpc/performance?preserve-view=true&view=aspnetcore-3.1#load-balancing).  

### <a name="troubleshooting-an-inference-server-when-it-does-not-receive-any-frames-and-you-are-receiving-an-unknown-protocol-error"></a>Problemen met een Afleidings Server oplossen wanneer deze geen frames ontvangt en u ontvangt, een ' onbekende ' protocol fout 

Er zijn verschillende dingen die u kunt doen om meer informatie over het probleem te verkrijgen.  

* Neem de **ediaPipeline** -logboek categorie op in de gewenste eigenschappen van de module live video Analytics en zorg ervoor dat het logboek niveau is ingesteld op `Information` .  
* Als u de netwerk verbinding wilt testen, kunt u de volgende opdracht uitvoeren vanaf het apparaat aan de rand. 

   ```
   sudo docker exec lvaEdge /bin/bash -c “apt update; apt install -y telnet; telnet <inference-host> <inference-port>” 
   ```

   Als met de opdracht een korte teken reeks van onsamenhangende tekst wordt uitgevoerd, heeft Telnet een verbinding met de server voor het afwijzen van de interferentie kunnen openen en wordt een binair gRPC-kanaal geopend. Als u dit niet ziet, wordt een netwerk fout door Telnet gerapporteerd. 
* U kunt in de inleidende server extra logboek registratie inschakelen in de gRPC-bibliotheek. Dit kan extra informatie geven over het gRPC-kanaal zelf. Dit is afhankelijk van taal. Dit zijn de instructies voor [C#](/aspnet/core/grpc/diagnostics?preserve-view=true&view=aspnetcore-3.1). 

### <a name="picking-more-images-from-buffer-of-grpc-without-sending-back-result-for-first-buffer"></a>Meerdere installatie kopieën uit de buffer van gRPC verzamelen zonder het terugsturen van het resultaat voor de eerste buffer

Als onderdeel van het gRPC-contract voor gegevens overdracht, moeten alle berichten die live video Analytics verzendt naar de gRPC-server worden bevestigd. Het is niet bevestigd dat de ontvangst van een afbeeldings frame het gegevens contract verbreekt en kan leiden tot ongewenste situaties.  

Als u uw gRPC-server met live video Analytics wilt gebruiken, kan gedeeld geheugen worden gebruikt voor de beste prestaties. Hiervoor moet u de functionaliteit voor gedeeld Linux-geheugen gebruiken die wordt weer gegeven door de programmeer taal/omgeving. 

1. Open de koppeling voor gedeeld Linux-geheugen.
1. Wanneer u een frame ontvangt, krijgt u toegang tot de adres verschuiving binnen het gedeelde geheugen.
1. Bevestig de voltooiing van de kader verwerking zodat het geheugen kan worden vrijgemaakt door live video Analytics.

   > [!NOTE]
   > Als u de ontvangst van de bevestiging van het kader voor een lange periode vertraagt, kan dit ertoe leiden dat het gedeelde geheugen vol is en dat de gegevens verloren gaan.
1. Sla elk frame op in een gegevens structuur van uw keuze (lijst, matrix, enzovoort) op de server voor afleiding.
1. U kunt de verwerkings logica vervolgens uitvoeren wanneer u het gewenste aantal afbeeldings kaders hebt.
1. Als u klaar bent, kunt u terugvallen op live video Analytics.

## <a name="next-steps"></a>Volgende stappen

[Zelfstudie: Video-opname op basis van gebeurtenissen in de cloud en afspelen vanuit de Cloud](event-based-video-recording-tutorial.md)