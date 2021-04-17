---
title: Telemetrie en logboekregistratie voor ruimtelijke analysecontainers
titleSuffix: Azure Cognitive Services
description: Ruimtelijke analyse biedt elke container een algemeen configuratiekader voor inzichten, logboekregistratie en beveiligingsinstellingen.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 01/12/2021
ms.author: aahi
ms.openlocfilehash: 901e857a346b0955726c5755e23595efefbc2ca1
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/17/2021
ms.locfileid: "107589496"
---
# <a name="telemetry-and-troubleshooting"></a>Telemetrie en probleemoplossing

Ruimtelijke analyse bevat een set functies voor het bewaken van de status van het systeem en hulp bij het diagnosticeren van problemen.

## <a name="enable-visualizations"></a>Visualisaties inschakelen

Als u een visualisatie van AI-inzichten gebeurtenissen in een videoframe wilt inschakelen, moet u de versie van een ruimtelijke `.debug` [analysebewerking op](spatial-analysis-operations.md) een desktopcomputer gebruiken. De visualisatie is niet mogelijk op Azure Stack Edge apparaten. Er zijn vier foutopsporingsbewerkingen beschikbaar.

Als uw apparaat geen apparaat is Azure Stack Edge, bewerkt u het distributiemanifestbestand voor [desktopcomputers](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/ComputerVision/spatial-analysis/DeploymentManifest_for_non_ASE_devices.json) om de juiste waarde voor de `DISPLAY` omgevingsvariabele te gebruiken. Deze moet overeenkomen met de `$DISPLAY` variabele op de hostcomputer. Nadat u het implementatiemanifest hebt bijgewerkt, moet u de container opnieuw implementeren.

Nadat de implementatie is voltooid, moet u het bestand mogelijk van de hostcomputer naar de container kopiëren `.Xauthority` en opnieuw opstarten. In het onderstaande voorbeeld `peopleanalytics` is de naam van de container op de hostcomputer.

```bash
sudo docker cp $XAUTHORITY peopleanalytics:/root/.Xauthority
sudo docker stop peopleanalytics
sudo docker start peopleanalytics
xhost +
```


## <a name="collect-system-health-telemetry"></a>Telemetrie over systeemtoestand verzamelen

Telegraf is een open source-afbeelding die werkt met ruimtelijke analyse en beschikbaar is in de Microsoft Container Registry. De volgende invoer wordt gebruikt en naar de Azure Monitor. De telegraf-module kan worden gebouwd met gewenste aangepaste invoer en uitvoer. De configuratie van de telegrafmodule in ruimtelijke analyse maakt deel uit van het implementatiemanifest (hierboven gekoppeld). Deze module is optioneel en kan worden verwijderd uit het manifest als u deze niet nodig hebt. 

Invoer: 
1. Metrische gegevens voor ruimtelijke analyse
2. Metrische schijfgegevens
3. Metrische CPU-gegevens
4. Metrische gegevens van Docker
5. Metrische GPU-gegevens

Uitvoer:
1. Azure Monitor

Met de meegeleverde ruimtelijke analyse-telegraf-module worden alle telemetriegegevens die door de container ruimtelijke analyse zijn ingediend, gepubliceerd Azure Monitor. Zie de [Azure Monitor](../../azure-monitor/overview.md) voor meer informatie over het toevoegen Azure Monitor aan uw abonnement.

Nadat u de Azure Monitor, moet u referenties maken waarmee de module telemetrie kan verzenden. U kunt de opdracht Azure Portal een nieuwe service-principal te maken of de onderstaande Azure CLI-opdracht gebruiken om er een te maken.

> [!NOTE] 
> Voor deze opdracht moet u eigenaarsbevoegdheden voor het abonnement hebben. 

```bash
# Find your Azure IoT Hub resource ID by running this command. The resource ID  should start with something like 
# "/subscriptions/b60d6458-1234-4be4-9885-c7e73af9ced8/resourceGroups/..."
az iot hub list

# Create a Service Principal with `Monitoring Metrics Publisher` role in the IoTHub resource:
# Save the output from this command. The values will be used in the deployment manifest. The password won't be shown again so make sure to write it down
az ad sp create-for-rbac --role="Monitoring Metrics Publisher" --name "<principal name>" --scopes="<resource ID of IoT Hub>"
```

Zoek in het implementatiemanifest voor uw Azure Stack Edge-apparaat, [desktopcomputer](https://go.microsoft.com/fwlink/?linkid=2152270) [of](https://go.microsoft.com/fwlink/?linkid=2142179) [Azure VM](https://go.microsoft.com/fwlink/?linkid=2152189)met GPU naar de *telegraf-module* en vervang de volgende waarden door de service-principalgegevens uit de vorige stap en de implementatie opnieuw.

```json

"telegraf": { 
  "settings": {
  "image":   "mcr.microsoft.com/azure-cognitive-services/vision/spatial-analysis/telegraf:1.0",
  "createOptions":   "{\"HostConfig\":{\"Runtime\":\"nvidia\",\"NetworkMode\":\"azure-iot-edge\",\"Memory\":33554432,\"Binds\":[\"/var/run/docker.sock:/var/run/docker.sock\"]}}"
},
"type": "docker",
"env": {
    "AZURE_TENANT_ID": {
        "value": "<Tenant Id>"
    },
    "AZURE_CLIENT_ID": {
        "value": "Application Id"
    },
    "AZURE_CLIENT_SECRET": {
        "value": "<Password>"
    },
    "region": {
        "value": "<Region>"
    },
    "resource_id": {
        "value": "/subscriptions/{subscriptionId}/resourceGroups/{resoureGroupName}/providers/Microsoft.Devices/IotHubs/{IotHub}"
    },
...
```

Zodra de telegraf-module is geïmplementeerd, zijn de gerapporteerde metrische gegevens toegankelijk via de Azure Monitor-service of door **Bewaking** te selecteren in de IoT Hub op de Azure Portal.

:::image type="content" source="./media/spatial-analysis/iot-hub-telemetry.png" alt-text="Azure Monitor telemetrierapport":::

### <a name="system-health-events"></a>Systeem statusgebeurtenissen

| Gebeurtenisnaam                  | Description    |
|-----------------------------|-------------------------------------------------------------------------------------------|
| archon_exit                 | Verzonden wanneer een gebruiker de status van de module Ruimtelijke analyse wijzigt van *Actief in* *Gestopt.*  |
| archon_error                | Verzonden wanneer een van de processen in de container vast loopt. Dit is een kritieke fout.      |
| InputRate                   | De snelheid waarmee de grafiek video-invoer verwerkt. Elke vijf minuten gerapporteerd.              |
| OutputRate                  | De snelheid waarmee de grafiek AI-inzichten uitvoert. Elke 5 minuten gerapporteerd.                |
| archon_allGraphsStarted     | Verzonden wanneer alle grafieken zijn begonnen.                                           |
| archon_configchange         | Verzonden wanneer een grafiekconfiguratie is gewijzigd.                                              |
| archon_graphCreationFailed  | Verzonden wanneer de grafiek met de `graphId` gerapporteerde niet kan worden starten.                           |
| archon_graphCreationSuccess | Verzonden wanneer de grafiek met de `graphId` gerapporteerde wordt gestart.                      |
| archon_graphCleanup         | Verzonden wanneer de grafiek met de `graphId` gerapporteerde opschoont en wordt afgesloten.                      |
| archon_graphHeartbeat       | Heartbeat die elke minuut wordt verzonden voor elke grafiek van een vaardigheid.                                   |
| archon_apiKeyAuthFail       | Verzonden wanneer Computer Vision resourcesleutel de container langer dan 24 uur niet kan verifiëren. Dit heeft de volgende redenen: Quotum is bereikt, ongeldig, offline. |
| VideoIngesterBeat      | Elk uur verzonden om aan te geven dat de video wordt gestreamd vanuit de videobron, met het aantal fouten in dat uur. Gerapporteerd voor elke grafiek. |
| VideoIngesterState          | Rapporten *gestopt* of *gestart voor* videostreaming. Gerapporteerd voor elke grafiek.              |

##  <a name="troubleshooting-an-iot-edge-device"></a>Problemen met een IoT Edge oplossen

U kunt het `iotedge` opdrachtregelprogramma gebruiken om de status en logboeken van de modules die worden uitgevoerd te controleren. Bijvoorbeeld:
* `iotedge list`: Rapporteert een lijst met modules die worden uitgevoerd. 
  U kunt verder controleren op fouten met `iotedge logs edgeAgent` . Als `iotedge` het vastloopt, kunt u proberen het opnieuw te starten met `iotedge restart edgeAgent`
* `iotedge logs <module-name>`
* `iotedge restart <module-name>` om een specifieke module opnieuw op te starten 

## <a name="collect-log-files-with-the-diagnostics-container"></a>Logboekbestanden verzamelen met de diagnostische container

Ruimtelijke analyse genereert docker-foutenlogboeken die u kunt gebruiken om runtimeproblemen vast te stellen of op te nemen in ondersteuningstickets. De diagnostische module ruimtelijke analyse is beschikbaar in de Microsoft Container Registry u kunt downloaden. Zoek in het manifestimplementatiebestand [Azure Stack Edge apparaat,](https://go.microsoft.com/fwlink/?linkid=2142179) [desktopcomputer](https://go.microsoft.com/fwlink/?linkid=2152270)of [Azure VM](https://go.microsoft.com/fwlink/?linkid=2152189) met GPU naar de *diagnostische* module.

Voeg in de sectie env de volgende configuratie toe:

```json
"diagnostics": {  
  "settings": {
  "image":   "mcr.microsoft.com/azure-cognitive-services/vision/spatial-analysis/diagnostics:1.0",
  "createOptions":   "{\"HostConfig\":{\"Mounts\":[{\"Target\":\"/usr/bin/docker\",\"Source\":\"/home/data/docker\",\"Type\":\"bind\"},{\"Target\":\"/var/run\",\"Source\":\"/run\",\"Type\":\"bind\"}],\"LogConfig\":{\"Config\":{\"max-size\":\"500m\"}}}}"
  }
```    

Als u logboeken wilt optimaliseren die zijn geüpload naar een extern eindpunt, zoals Azure Blob Storage, raden we u aan een kleine bestandsgrootte te onderhouden. Zie het onderstaande voorbeeld voor de aanbevolen configuratie van Docker-logboeken.

```json
{
    "HostConfig": {
        "LogConfig": {
            "Config": {
                "max-size": "500m",
                "max-file": "1000"
            }
        }
    }
}
```

### <a name="configure-the-log-level"></a>Het logboekniveau configureren

Met configuratie op logboekniveau kunt u de complexiteit van de gegenereerde logboeken bepalen. Ondersteunde logboekniveaus zijn: `none` , , , en `verbose` `info` `warning` `error` . Het standaard uitgebreide logboekniveau voor zowel knooppunten als platform is `info` . 

Logboekniveaus kunnen globaal worden gewijzigd door de `ARCHON_LOG_LEVEL` omgevingsvariabele in te stellen op een van de toegestane waarden.
Het kan ook worden ingesteld via het document IoT Edge Module Twin, voor alle geïmplementeerde vaardigheden of voor elke specifieke vaardigheid, door de waarden voor en in te stellen, zoals hieronder `platformLogLevel` `nodesLogLevel` wordt weergegeven.

```json
{
    "version": 1,
    "properties": {
        "desired": {
            "globalSettings": {
                "platformLogLevel": "verbose"
            },
            "graphs": {
                "samplegraph": {
                    "nodesLogLevel": "verbose",
                    "platformLogLevel": "verbose"
                }
            }
        }
    }
}
```

### <a name="collecting-logs"></a>Logboeken verzamelen

> [!NOTE]
> De module heeft geen invloed op de logboekinhoud, maar helpt alleen bij het verzamelen, filteren en `diagnostics` uploaden van bestaande logboeken.
> U moet Docker API versie 1.40 of hoger hebben om deze module te kunnen gebruiken.

Het voorbeeld van een distributiemanifestbestand voor uw [Azure Stack Edge,](https://go.microsoft.com/fwlink/?linkid=2142179) [desktopcomputer](https://go.microsoft.com/fwlink/?linkid=2152270)of [Azure VM](https://go.microsoft.com/fwlink/?linkid=2152189) met GPU bevat een module met de naam die logboeken verzamelt `diagnostics` en uploadt. Deze module is standaard uitgeschakeld en moet worden ingeschakeld via de configuratie van IoT Edge module wanneer u toegang nodig hebt tot logboeken. 

De verzameling wordt op aanvraag beheerd via een IoT Edge directe methode en kan logboeken `diagnostics` naar een Azure Blob Storage.

### <a name="configure-diagnostics-upload-targets"></a>Uploaddoelen voor diagnostische gegevens configureren

Selecteer in IoT Edge portal uw apparaat en vervolgens de **diagnostische** module. Zoek in het voorbeeldbestand [](https://go.microsoft.com/fwlink/?linkid=2142179)Implementatiemanifest voor uw Azure Stack Edge-apparaat, [desktopcomputers](https://go.microsoft.com/fwlink/?linkid=2152270)of [Azure VM](https://go.microsoft.com/fwlink/?linkid=2152189) met GPU naar de sectie **Omgevingsvariabelen** voor diagnostische gegevens, met de naam , en voeg de `env` volgende informatie toe:

**Uploaden naar Azure Blob Storage**

1. Maak uw eigen Azure Blob Storage account, als u dat nog niet hebt gedaan.
2. Haal de **verbindingsreeks** voor uw opslagaccount op uit Azure Portal. Deze bevindt zich in **toegangssleutels.**
3. Logboeken voor ruimtelijke analyse worden automatisch geüpload naar een Blob Storage container met de naam *rtcvlogs* met de volgende bestandsnaamindeling: `{CONTAINER_NAME}/{START_TIME}-{END_TIME}-{QUERY_TIME}.log` .

```json
"env":{
    "IOTEDGE_WORKLOADURI":"fd://iotedge.socket",
    "AZURE_STORAGE_CONNECTION_STRING":"XXXXXX",   //from the Azure Blob Storage account
    "ARCHON_LOG_LEVEL":"info"
}
```

### <a name="uploading-spatial-analysis-logs"></a>Logboeken voor ruimtelijke analyse uploaden

Logboeken worden op aanvraag geüpload met `getRTCVLogs` de IoT Edge methode in de `diagnostics` module. 


1. Ga naar de IoT Hub portalpagina, selecteer **Edge-apparaten** en selecteer vervolgens uw apparaat en uw diagnostische module. 
2. Ga naar de detailpagina van de module en klik op het ***tabblad Directe*** methode.
3. Typ `getRTCVLogs` bij Methodenaam en een tekenreeks in json-indeling in nettolading. U kunt `{}` invoeren. Dit is een lege nettolading. 
4. Stel de time-outs voor de verbinding en methode in en klik op **Methode aanroepen.**
5. Selecteer uw doelcontainer en bouw een json-tekenreeks voor de nettolading met behulp van de parameters die worden beschreven in de **sectie Syntaxis voor logboekregistratie.** Klik **op Methode aanroepen** om de aanvraag uit te voeren.

>[!NOTE]
> Als u de methode `getRTCVLogs` aanroept met een lege nettolading, wordt een lijst met alle containers die op het apparaat zijn geïmplementeerd, weergegeven. De naam van de methode is casegevoelig. U krijgt een 501-fout als er een onjuiste methodenaam wordt opgegeven.

:::image type="content" source="./media/spatial-analysis/direct-log-collection.png" alt-text="De methode getRTCVLogs aanroepen ":::
![pagina met directe methode getRTCVLogs](./media/spatial-analysis/direct-log-collection.png)

 
### <a name="logging-syntax"></a>Syntaxis voor logboekregistratie

De onderstaande tabel bevat de parameters die u kunt gebruiken bij het uitvoeren van query's op logboeken.

| Trefwoord | Beschrijving | Standaardwaarde |
|--|--|--|
| StartTime | Gewenste begintijd van logboeken, in milliseconden UTC. | `-1`, het begin van de runtime van de container. Wanneer `[-1.-1]` wordt gebruikt als een tijdsbereik, retourneert de API logboeken van het afgelopen uur.|
| EndTime | Gewenste eindtijd van logboeken, in milliseconden UTC. | `-1`, de huidige tijd. Wanneer `[-1.-1]` het tijdsbereik wordt gebruikt, retourneert de API logboeken van het afgelopen uur. |
| ContainerId | Doelcontainer voor het ophalen van logboeken.| `null`, wanneer er geen container-id is. De API retourneert alle beschikbare gegevens van containers met -ID's.|
| DoPost | Voer de uploadbewerking uit. Als deze is ingesteld op , wordt de aangevraagde bewerking uitgevoerd en wordt de uploadgrootte zonder `false` de upload uitgevoerd. Als deze optie is `true` ingesteld op , wordt de asynchrone upload van de geselecteerde logboeken gestart | `false`, niet uploaden.|
| Vertragen | Geef aan hoeveel regels logboeken per batch moeten worden geüpload | `1000`, gebruik deze parameter om de postsnelheid aan te passen. |
| Filters | Filtert logboeken die moeten worden geüpload | `null`, kunnen filters worden opgegeven als sleutel-waardeparen op basis van de structuur van de logboeken voor ruimtelijke analyse: `[UTC, LocalTime, LOGLEVEL,PID, CLASS, DATA]` . Bijvoorbeeld: `{"TimeFilter":[-1,1573255761112]}, {"TimeFilter":[-1,1573255761112]}, {"CLASS":["myNode"]`|

De volgende tabel bevat de kenmerken in het query-antwoord.

| Trefwoord | Description|
|--|--|
|DoPost| Waar  of *onwaar.* Geeft aan of logboeken zijn geüpload of niet. Wanneer u ervoor kiest geen logboeken te uploaden, retourneert de API informatie ***synchroon** _. Wanneer u ervoor kiest om logboeken te uploaden, retourneert de API 200 als de aanvraag geldig is en begint met het uploaden van logboeken _*_asynchroon_**.|
|TimeFilter| Tijdfilter toegepast op de logboeken.|
|ValueFilters| Trefwoordenfilters die zijn toegepast op de logboeken. |
|Tijdstempel| Begintijd van uitvoering van methode. |
|ContainerId| Doelcontainer-id. |
|FetchCounter| Totaal aantal logboekregels. |
|FetchSizeInByte| Totale hoeveelheid logboekgegevens in bytes. |
|MatchCounter| Geldig aantal logboekregels. |
|MatchSizeInByte| Geldige hoeveelheid logboekgegevens in bytes. |
|FilterCount| Totaal aantal logboekregels na het toepassen van het filter. |
|FilterSizeInByte| Totale hoeveelheid logboekgegevens in bytes na het toepassen van het filter. |
|FetchLogsDurationInMiliSec| Duur van op te halen bewerking. |
|PaseLogsDurationInMiliSec| Duur filterbewerking. |
|PostLogsDurationInMiliSec| Duur na bewerking. |

#### <a name="example-request"></a>Voorbeeldaanvraag 

```json
{
    "StartTime": -1,
    "EndTime": -1,
    "ContainerId": "5fa17e4d8056e8d16a5a998318716a77becc01b36fde25b3de9fde98a64bf29b",
    "DoPost": false,
    "Filters": null
}
```

#### <a name="example-response"></a>Voorbeeld van een antwoord 

```json
{
    "status": 200,
    "payload": {
        "DoPost": false,
        "TimeFilter": [-1, 1581310339411],
        "ValueFilters": {},
        "Metas": {
            "TimeStamp": "2020-02-10T04:52:19.4365389+00:00",
            "ContainerId": "5fa17e4d8056e8d16a5a998318716a77becc01b36fde25b3de9fde98a64bf29b",
            "FetchCounter": 61,
            "FetchSizeInByte": 20470,
            "MatchCounter": 61,
            "MatchSizeInByte": 20470,
            "FilterCount": 61,
            "FilterSizeInByte": 20470,
            "FetchLogsDurationInMiliSec": 0,
            "PaseLogsDurationInMiliSec": 0,
            "PostLogsDurationInMiliSec": 0
        }
    }
}
```

Controleer de regels, tijden en grootten van het logboek ophalen. Als deze instellingen er goed uitzien, vervangt u ***DoPost*** naar en die de logboeken met dezelfde filters naar `true` bestemmingen pusht. 

U kunt logboeken vanuit de Azure Blob Storage bij het oplossen van problemen. 

## <a name="common-issues"></a>Algemene problemen

Als u het volgende bericht in de modulelogboeken ziet, kan dit betekenen dat uw Azure-abonnement moet worden goedgekeurd: 

'Container heeft geen geldige status. De validatie van het abonnement is mislukt met de status 'Komt niet overeen'. Api-sleutel is niet bedoeld voor het opgegeven containertype.

Zie Goedkeuring aanvragen om de [container uit te voeren voor meer informatie.](spatial-analysis-container.md#request-approval-to-run-the-container)

## <a name="troubleshooting-the-azure-stack-edge-device"></a>Problemen met Azure Stack Edge apparaat oplossen

De volgende sectie is bedoeld voor hulp bij het debuggen en verifiëren van de status van Azure Stack Edge apparaat.

### <a name="access-the-kubernetes-api-endpoint"></a>Toegang tot het Kubernetes API-eindpunt. 

1. Ga in de lokale gebruikersinterface van uw apparaat naar de **pagina** Apparaten. 
2. Kopieer **onder Apparaat-eindpunten** het service-eindpunt van de Kubernetes-API. Dit eindpunt is een tekenreeks met de volgende indeling: `https://compute..[device-IP-address]`.
3. Sla de tekenreeks met het eindpunt op. U gebruikt deze later bij het configureren voor `kubectl` toegang tot het Kubernetes-cluster.

### <a name="connect-to-powershell-interface"></a>Verbinding maken met de PowerShell-interface

Maak op afstand verbinding vanaf een Windows-client. Nadat het Kubernetes-cluster is gemaakt, kunt u de toepassingen beheren via dit cluster. U moet verbinding maken met de PowerShell-interface van het apparaat. Afhankelijk van het besturingssysteem van de client kunnen de procedures voor het extern verbinden met het apparaat verschillen. De volgende stappen zijn voor een Windows-client met PowerShell.

> [!TIP]
> * Voordat u begint, moet u ervoor zorgen dat uw Windows-client wordt uitgevoerd Windows PowerShell 5.0 of hoger.
> * PowerShell is ook [beschikbaar op Linux](/powershell/scripting/install/installing-powershell-core-on-linux).

1. Voer een Windows PowerShell uit als beheerder. 
    1. Zorg ervoor dat de Windows Remote Management-service wordt uitgevoerd op uw client. Typ bij de `winrm quickconfig` opdrachtprompt.

2. Wijs een variabele toe voor het IP-adres van het apparaat. Bijvoorbeeld `$ip = "<device-ip-address>"`.

3. Gebruik de volgende opdracht om het IP-adres van uw apparaat toe te voegen aan de lijst met vertrouwde hosts van de client. 

    ```powershell
    Set-Item WSMan:\localhost\Client\TrustedHosts $ip -Concatenate -Force
    ```
 
4. Start een Windows PowerShell op het apparaat. 

    ```powershell
    Enter-PSSession -ComputerName $ip -Credential $ip\EdgeUser -ConfigurationName Minishell
    ```

5. Geef het wachtwoord op wanneer u hier om wordt gevraagd. Gebruik hetzelfde wachtwoord dat wordt gebruikt om u aan te melden bij de lokale webinterface. Het standaardwachtwoord voor de lokale webinterface is `Password1` . 

### <a name="access-the-kubernetes-cluster"></a>Toegang tot het Kubernetes-cluster

Nadat het Kubernetes-cluster is gemaakt, kunt u het `kubectl` opdrachtregelprogramma gebruiken om toegang te krijgen tot het cluster.

1. Maak een nieuwe naamruimte. 

    ```powershell
    New-HcsKubernetesNamespace -Namespace
    ```

2. Maak een gebruiker en haal een configuratiebestand op. Met deze opdracht worden configuratiegegevens voor het Kubernetes-cluster uitgevoerd. Kopieer deze informatie en sla deze op in een bestand met de *naam config*. Sla het bestand niet op als bestandsextensie.
    
    ```powershell
    New-HcsKubernetesUser -UserName
    ```

3. Voeg het *configuratiebestand toe* aan de *map .kube* in uw gebruikersprofiel op de lokale computer.    

4. Koppel de naamruimte aan de gebruiker die u hebt gemaakt.

    ```powershell
    Grant-HcsKubernetesNamespaceAccess -Namespace -UserName
    ```

5. Installeer `kubectl` op uw Windows-client met behulp van de volgende opdracht:
    
    ```powershell
    curl https://storage.googleapis.com/kubernetesrelease/release/v1.15.2/bin/windows/amd64/kubectl.exe -O kubectl.exe
    ```

6. Voeg een DNS-vermelding toe aan het hosts-bestand op uw systeem. 
    1. Voer Kladblok uit als beheerder en open het *hosts-bestand* dat zich bevindt op `C:\windows\system32\drivers\etc\hosts` . 
    2. Maak een vermelding in het hosts-bestand met het IP-adres van het apparaat en het DNS-domein dat u hebt ontvangen van de **pagina Apparaat** in de lokale gebruikersinterface. Het eindpunt dat u moet gebruiken, ziet er ongeveer als de volgende uit: `https://compute.asedevice.microsoftdatabox.com/10.100.10.10` .

7. Controleer of u verbinding kunt maken met de Kubernetes-pods.

    ```powershell
    kubectl get pods -n "iotedge"
    ```

Voer de volgende opdracht uit om containerlogboeken op te halen:

```powershell
kubectl logs <pod-name> -n <namespace> --all-containers
```

### <a name="useful-commands"></a>Nuttige opdrachten

|Opdracht  |Beschrijving  |
|---------|---------|
|`Get-HcsKubernetesUserConfig -AseUser`     | Genereert een Kubernetes-configuratiebestand. Wanneer u de opdracht gebruikt, kopieert u de informatie naar een bestand met de *naam config*. Sla het bestand niet op met een bestandsextensie.        |
| `Get-HcsApplianceInfo` | Retourneert informatie over uw apparaat. |
| `Enable-HcsSupportAccess` | Genereert toegangsreferenties om een ondersteuningssessie te starten. |


## <a name="how-to-file-a-support-ticket-for-spatial-analysis"></a>Een ondersteuningsticket indienen voor ruimtelijke analyse 

Als u meer ondersteuning nodig hebt bij het vinden van een oplossing voor een probleem met de ruimtelijke analysecontainer, volgt u deze stappen om een ondersteuningsticket in te vullen en in te dienen. Ons team neemt contact met u op met aanvullende richtlijnen. 

### <a name="fill-out-the-basics"></a>De basisbeginselen invullen 
Maak een nieuw ondersteuningsticket op de [pagina Nieuwe ondersteuningsaanvraag.](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) Volg de aanwijzingen om de volgende parameters in te vullen:

![Basisbeginselen van ondersteuning](./media/support-ticket-page-1-final.png)

1. Stel **Probleemtype in** op `Technical` .
2. Selecteer het abonnement dat u gebruikt om de ruimtelijke analysecontainer te implementeren.
3. Selecteer `My services` en selecteer als de `Cognitive Services` service.
4. Selecteer de resource die u gebruikt om de container ruimtelijke analyse te implementeren.
5. Schrijf een korte beschrijving van het probleem waarmee u te maken hebt. 
6. Selecteer `Spatial Analysis` als uw probleemtype.
7. Selecteer het juiste subtype in de vervolgkeuzekeuze.
8. Selecteer **Volgende: Oplossingen om** door te gaan naar de volgende pagina.

### <a name="recommended-solutions"></a>Aanbevolen oplossingen
De volgende fase biedt aanbevolen oplossingen voor het probleemtype dat u hebt geselecteerd. Met deze oplossingen worden de meest voorkomende problemen opgelost, maar als dit niet nuttig is voor uw oplossing, selecteert u **Volgende: Details** om naar de volgende stap te gaan.

### <a name="details"></a>Details
Voeg op deze pagina aanvullende informatie toe over het probleem waarmee u te maken hebt. Zorg ervoor dat u zo veel mogelijk details op neemt, omdat dit onze technici helpt het probleem beter te beperken. Neem de gewenste contactmethode en de ernst van het probleem op, zodat we op de juiste wijze contact met u kunnen opnemen en selecteer **Volgende:** Beoordelen en maken om door te gaan naar de volgende stap. 

### <a name="review-and-create"></a>Controleren en maken 
Bekijk de details van uw ondersteuningsaanvraag om er zeker van te zijn dat alles klopt en het probleem effectief vertegenwoordigt. Wanneer u klaar bent, selecteert **u Maken om** het ticket naar ons team te verzenden. U ontvangt een e-mailbevestiging zodra uw ticket is ontvangen. Ons team zal er dan aan werken om zo snel mogelijk contact met u op te nemen. U kunt de status van uw ticket in de Azure Portal.

## <a name="next-steps"></a>Volgende stappen

* [Een webtoepassing voor het tellen van personen implementeren](spatial-analysis-web-app.md)
* [Bewerkingen voor ruimtelijke analyse configureren](./spatial-analysis-operations.md)
* [Handleiding voor het plaatsen van camera's](spatial-analysis-camera-placement.md)
* [Handleiding voor zone- en regelplaatsing](spatial-analysis-zone-line-placement.md)
