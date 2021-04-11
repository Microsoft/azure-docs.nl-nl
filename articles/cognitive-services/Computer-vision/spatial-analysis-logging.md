---
title: Telemetrie en logboek registratie voor containers voor ruimtelijke analyse
titleSuffix: Azure Cognitive Services
description: Ruimtelijke analyse biedt elke container een veelvoorkomende configuratie raamwerk, logboek registratie en beveiligings instellingen.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 01/12/2021
ms.author: aahi
ms.openlocfilehash: bd35062ca1a5b3218a9af1fbd28fe8e7d7073c07
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/31/2021
ms.locfileid: "106077615"
---
# <a name="telemetry-and-troubleshooting"></a>Telemetrie en probleem oplossing

Ruimtelijke analyse bevat een aantal functies voor het controleren van de status van het systeem en hulp bij het diagnosticeren van problemen.

## <a name="enable-visualizations"></a>Visualisaties inschakelen

Als u een visualisatie van AI Insights-gebeurtenissen in een video frame wilt maken, moet u de `.debug` versie van een [ruimtelijke analyse bewerking](spatial-analysis-operations.md) op een desktop computer gebruiken. De visualisatie is niet mogelijk op Azure Stack edge-apparaten. Er zijn vier debug-bewerkingen beschikbaar.

Als uw apparaat geen Azure Stack edge-apparaat is, bewerkt u het manifest bestand van de implementatie voor [desktop computers](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/ComputerVision/spatial-analysis/DeploymentManifest_for_non_ASE_devices.json) om de juiste waarde voor de `DISPLAY` omgevings variabele te gebruiken. De waarde moet overeenkomen met de `$DISPLAY` variabele op de hostcomputer. Nadat u het implementatie manifest hebt bijgewerkt, implementeert u de container opnieuw.

Nadat de implementatie is voltooid, moet u het `.Xauthority` bestand mogelijk van de hostcomputer naar de container kopiëren en opnieuw starten. In het onderstaande voor beeld `peopleanalytics` is de naam van de container op de hostcomputer.

```bash
sudo docker cp $XAUTHORITY peopleanalytics:/root/.Xauthority
sudo docker stop peopleanalytics
sudo docker start peopleanalytics
xhost +
```


## <a name="collect-system-health-telemetry"></a>Telemetrie van systeem status verzamelen

Telegrafie is een open-source installatie kopie die werkt met ruimtelijke analyse en is beschikbaar in de micro soft-Container Registry. Het heeft de volgende invoer en verzendt deze naar Azure Monitor. De telegrafie module kan worden gebouwd met de gewenste aangepaste invoer en uitvoer. De configuratie van de telegrafie module in ruimtelijke analyse maakt deel uit van het implementatie manifest (gekoppeld aan de bovenstaande). Deze module is optioneel en kan worden verwijderd uit het manifest als u deze niet nodig hebt. 

Invoer: 
1. Metrische gegevens over ruimtelijke analyse
2. Metrische schijfgegevens
3. CPU-metrische gegevens
4. Basis gegevens docker
5. Metrische gegevens GPU

Uitvoer:
1. Azure Monitor

De opgegeven ruimtelijke analyse-telegrafie module publiceert alle telemetriegegevens die zijn verzonden door de container voor ruimtelijke analyse naar Azure Monitor. Raadpleeg de [Azure monitor](../../azure-monitor/overview.md) voor meer informatie over het toevoegen van Azure monitor aan uw abonnement.

Nadat u Azure Monitor hebt ingesteld, moet u referenties maken waarmee de module telemetrie kan verzenden. U kunt de Azure Portal gebruiken om een nieuwe service-principal te maken of de Azure CLI-opdracht hieronder gebruiken om er een te maken.

> [!NOTE] 
> Voor deze opdracht moet u eigenaars bevoegdheden voor het abonnement hebben. 

```bash
# Find your Azure IoT Hub resource ID by running this command. The resource ID  should start with something like 
# "/subscriptions/b60d6458-1234-4be4-9885-c7e73af9ced8/resourceGroups/..."
az iot hub list

# Create a Service Principal with `Monitoring Metrics Publisher` role in the IoTHub resource:
# Save the output from this command. The values will be used in the deployment manifest. The password won't be shown again so make sure to write it down
az ad sp create-for-rbac --role="Monitoring Metrics Publisher" --name "<principal name>" --scopes="<resource ID of IoT Hub>"
```

Zoek in het implementatie manifest voor uw [Azure stack edge-apparaat](https://go.microsoft.com/fwlink/?linkid=2142179), [desktop machine](https://go.microsoft.com/fwlink/?linkid=2152270)of [Azure VM met GPU](https://go.microsoft.com/fwlink/?linkid=2152189)naar de *telegrafie* module en vervang de volgende waarden door de Service-Principal-informatie uit de vorige stap en implementeer opnieuw.

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

Zodra de telegrafie module is geïmplementeerd, zijn de gerapporteerde metrische gegevens toegankelijk via de Azure Monitor-service of door **bewaking** te selecteren in de IOT hub op de Azure Portal.

:::image type="content" source="./media/spatial-analysis/iot-hub-telemetry.png" alt-text="Azure Monitor telemetrie-rapport":::

### <a name="system-health-events"></a>Systeem status gebeurtenissen

| Gebeurtenisnaam                  | Description    |
|-----------------------------|-------------------------------------------------------------------------------------------|
| archon_exit                 | Verzonden wanneer een gebruiker de status van de ruimtelijke analyse module wijzigt van *actief* in *gestopt*.  |
| archon_error                | Wordt verzonden wanneer een van de processen in de container vastloopt. Dit is een kritieke fout.      |
| InputRate                   | De snelheid waarmee de video-invoer door de grafiek wordt verwerkt. Elke 5 minuten gerapporteerd.              |
| OutputRate                  | De snelheid waarmee de grafiek AI-inzichten uitvoert. Elke 5 minuten gerapporteerd.                |
| archon_allGraphsStarted     | Wordt verzonden wanneer het opstarten van alle grafieken is voltooid.                                           |
| archon_configchange         | Verzonden wanneer de configuratie van een grafiek is gewijzigd.                                              |
| archon_graphCreationFailed  | Verzonden wanneer de grafiek met de gemelde `graphId` fout niet kan worden gestart.                           |
| archon_graphCreationSuccess | Verzonden wanneer de grafiek met de gemelde `graphId` Start.                      |
| archon_graphCleanup         | Verzonden wanneer de grafiek met de gerapporteerde `graphId` opschoont en afsluit.                      |
| archon_graphHeartbeat       | Heartbeat elke minuut verzonden voor elke grafiek van een vaardigheid.                                   |
| archon_apiKeyAuthFail       | Dit bericht wordt verzonden wanneer de container niet langer dan 24 uur door de Computer Vision bron sleutel kan worden geverifieerd vanwege de volgende redenen: buiten het quotum, ongeldig, offline. |
| VideoIngesterHeartbeat      | Wordt elk uur verzonden om aan te geven dat de video wordt gestreamd vanuit de video bron, met het aantal fouten in dat uur. Gerapporteerd voor elke grafiek. |
| VideoIngesterState          | Rapporten *gestopt* of *gestart* voor video-streaming. Gerapporteerd voor elke grafiek.              |

##  <a name="troubleshooting-an-iot-edge-device"></a>Problemen met een IoT Edge apparaat oplossen

U kunt `iotedge` het opdracht regel programma gebruiken om de status en logboeken van de actieve modules te controleren. Bijvoorbeeld:
* `iotedge list`: Hiermee wordt een lijst met actieve modules gerapporteerd. 
  U kunt verder controleren op fouten met `iotedge logs edgeAgent` . Als `iotedge` het vastloopt, kunt u het opnieuw proberen met `iotedge restart edgeAgent`
* `iotedge logs <module-name>`
* `iotedge restart <module-name>` een specifieke module opnieuw opstarten 

## <a name="collect-log-files-with-the-diagnostics-container"></a>Logboek bestanden met de diagnostische container verzamelen

Met ruimtelijke analyse worden logboeken voor fout opsporing in docker gegenereerd die u kunt gebruiken om runtime problemen vast te stellen of in ondersteunings tickets op te vragen. De module voor de diagnostische gegevens over ruimtelijke analyse is beschikbaar in de micro soft-Container Registry die u kunt downloaden. In het manifest bestand voor het implementeren van uw [Azure stack edge-apparaat](https://go.microsoft.com/fwlink/?linkid=2142179), [desktop machine](https://go.microsoft.com/fwlink/?linkid=2152270)of [Azure VM met GPU](https://go.microsoft.com/fwlink/?linkid=2152189) zoekt u de *Diagnostische* module.

Voeg in de sectie ' env ' de volgende configuratie toe:

```json
"diagnostics": {  
  "settings": {
  "image":   "mcr.microsoft.com/azure-cognitive-services/vision/spatial-analysis/diagnostics:1.0",
  "createOptions":   "{\"HostConfig\":{\"Mounts\":[{\"Target\":\"/usr/bin/docker\",\"Source\":\"/home/data/docker\",\"Type\":\"bind\"},{\"Target\":\"/var/run\",\"Source\":\"/run\",\"Type\":\"bind\"}],\"LogConfig\":{\"Config\":{\"max-size\":\"500m\"}}}}"
  }
```    

Voor het optimaliseren van logboeken die zijn geüpload naar een extern eind punt, zoals Azure Blob Storage, raden we u aan een kleine bestands grootte te behouden. Zie het onderstaande voor beeld voor de aanbevolen configuratie van docker-Logboeken.

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

### <a name="configure-the-log-level"></a>Het logboek niveau configureren

Met de configuratie van het logboek niveau kunt u de uitgebreide versie van de gegenereerde logboeken beheren. De ondersteunde logboek niveaus zijn: `none` ,, `verbose` , en `info` `warning` `error` . Het standaard niveau voor logboek registratie van zowel knoop punten als platform is `info` . 

De logboek niveaus kunnen globaal worden gewijzigd door de `ARCHON_LOG_LEVEL` omgevings variabele in te stellen op een van de toegestane waarden.
Het kan ook worden ingesteld via het dubbele document van de IoT Edge module, voor alle geïmplementeerde vaardig heden, of voor elke specifieke vaardigheid door de waarden in te stellen voor `platformLogLevel` en `nodeLogLevel` zoals hieronder wordt weer gegeven.

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
                    "nodeLogLevel": "verbose",
                    "platformLogLevel": "verbose"
                }
            }
        }
    }
}
```

### <a name="collecting-logs"></a>Logboeken verzamelen

> [!NOTE]
> De `diagnostics` module heeft geen invloed op de inhoud van het logboek. het is alleen handig bij het verzamelen, filteren en uploaden van bestaande logboeken.
> U moet docker API versie 1,40 of hoger hebben om deze module te kunnen gebruiken.

Het manifest bestand van de voorbeeld implementatie voor uw [Azure stack edge-apparaat](https://go.microsoft.com/fwlink/?linkid=2142179), [desktop machine](https://go.microsoft.com/fwlink/?linkid=2152270)of [Azure VM met GPU](https://go.microsoft.com/fwlink/?linkid=2152189) bevat een module met de naam `diagnostics` die logboeken verzamelt en uploadt. Deze module is standaard uitgeschakeld en moet worden ingeschakeld via de configuratie van de IoT Edge-module als u logboeken wilt openen. 

De `diagnostics` verzameling is op aanvraag en wordt beheerd via een IOT Edge directe methode en kan Logboeken verzenden naar een Azure-Blob Storage.

### <a name="configure-diagnostics-upload-targets"></a>Upload doelen voor diagnostische gegevens configureren

Selecteer uw apparaat in de IoT Edge Portal en vervolgens de module **diagnostiek** . In het manifest bestand voor beeld implementatie voor uw [Azure stack edge-apparaat](https://go.microsoft.com/fwlink/?linkid=2142179), [desktop computers](https://go.microsoft.com/fwlink/?linkid=2152270)of [Azure VM met GPU](https://go.microsoft.com/fwlink/?linkid=2152189) zoekt u naar de sectie **omgevings variabelen** voor diagnostische gegevens, met de naam `env` en voegt u de volgende informatie toe:

**Upload naar Azure Blob Storage configureren**

1. Maak uw eigen Azure Blob Storage-account, als u dat nog niet hebt gedaan.
2. Haal de **verbindings reeks** voor uw opslag account op uit het Azure Portal. Deze vindt u in **toegangs sleutels**.
3. Ruimtelijke analyse logboeken worden automatisch geüpload naar een Blob Storage-container met de naam *rtcvlogs* met de volgende indeling voor bestands namen: `{CONTAINER_NAME}/{START_TIME}-{END_TIME}-{QUERY_TIME}.log` .

```json
"env":{
    "IOTEDGE_WORKLOADURI":"fd://iotedge.socket",
    "AZURE_STORAGE_CONNECTION_STRING":"XXXXXX",   //from the Azure Blob Storage account
    "ARCHON_LOG_LEVEL":"info"
}
```

### <a name="uploading-spatial-analysis-logs"></a>Logboeken met ruimtelijke analyse uploaden

Logboeken worden op aanvraag geüpload met de `getRTCVLogs` methode IOT Edge in de `diagnostics` module. 


1. Ga naar de pagina IoT Hub Portal, selecteer **edge-apparaten** en selecteer vervolgens uw apparaat en de module diagnostiek. 
2. Ga naar de pagina Details van de module en klik op het tabblad ***directe methode*** .
3. Typ `getRTCVLogs` de naam van de methode en een JSON-indelings teken reeks in payload. U kunt `{}` een lege Payload opgeven. 
4. Stel de time-out voor de verbinding en de methode in en klik op **methode aanroepen**.
5. Selecteer uw doel container en bouw een JSON-teken reeks voor de nettolading op met de para meters die worden beschreven in de sectie **syntaxis van logboek registratie** . Klik op **methode aanroepen** om de aanvraag uit te voeren.

>[!NOTE]
> `getRTCVLogs`Als de methode wordt aangeroepen met een lege Payload, wordt een lijst geretourneerd met alle containers die op het apparaat zijn geïmplementeerd. De naam van de methode is hoofdletter gevoelig. U krijgt een 501-fout als er een onjuiste methode naam wordt opgegeven.

:::image type="content" source="./media/spatial-analysis/direct-log-collection.png" alt-text="Aanroepen van de methode getRTCVLogs ":::
![pagina directe methode getRTCVLogs](./media/spatial-analysis/direct-log-collection.png)

 
### <a name="logging-syntax"></a>Syntaxis van logboek registratie

De onderstaande tabel geeft een lijst van de para meters die u kunt gebruiken bij het uitvoeren van query's op Logboeken.

| Zoek | Beschrijving | Standaardwaarde |
|--|--|--|
| StartTime | Gewenste begin tijd van Logboeken, in milliseconden UTC. | `-1`, het begin van de runtime van de container. Wanneer `[-1.-1]` wordt gebruikt als een tijds bereik, retourneert de API logboeken van de laatste één uur.|
| EndTime | Eind tijd van de gewenste Logboeken in milliseconden UTC. | `-1`, de huidige tijd. Wanneer `[-1.-1]` het tijds bereik wordt gebruikt, retourneert de API logboeken van de laatste één uur. |
| ContainerId | Doel container voor het ophalen van Logboeken.| `null`Als er geen container-ID is. De API retourneert alle beschik bare containers informatie met Id's.|
| DoPost | Voer de upload bewerking uit. Wanneer dit is ingesteld op `false` , wordt de aangevraagde bewerking uitgevoerd en wordt de upload grootte geretourneerd zonder dat de upload wordt uitgevoerd. Als deze instelling is ingesteld op `true` , wordt de asynchrone upload van de geselecteerde logboeken gestart | `false`, niet uploaden.|
| Vertragen | Aangeven hoeveel regels logboeken per batch moeten worden geüpload | `1000`, Gebruikt u deze para meter om de post snelheid aan te passen. |
| Filters | Filtert de logboeken die moeten worden geüpload | `null`filters kunnen worden opgegeven als sleutel-waardeparen op basis van de structuur van de ruimtelijke analyse logboeken: `[UTC, LocalTime, LOGLEVEL,PID, CLASS, DATA]` . Bijvoorbeeld: `{"TimeFilter":[-1,1573255761112]}, {"TimeFilter":[-1,1573255761112]}, {"CLASS":["myNode"]`|

De volgende tabel bevat de kenmerken in de query-antwoord.

| Zoek | Description|
|--|--|
|DoPost| *Waar* of *Onwaar*. Hiermee wordt aangegeven of Logboeken zijn geüpload of niet. Wanneer u ervoor kiest geen logboeken te uploaden, retourneert de API informatie ***synchroon** _. Wanneer u ervoor kiest om logboeken te uploaden, retourneert de API 200, als de aanvraag geldig is en het uploaden van Logboeken _ *_asynchroon_* * wordt gestart.|
|TimeFilter| Tijd filter toegepast op de logboeken.|
|ValueFilters| Tref woorden filters die worden toegepast op de logboeken. |
|Neem| Start tijd van methode-uitvoering. |
|ContainerId| Doel container-ID. |
|FetchCounter| Totaal aantal logboek regels. |
|FetchSizeInByte| Totale hoeveelheid logboek gegevens in bytes. |
|MatchCounter| Geldig aantal logboek regels. |
|MatchSizeInByte| Geldig aantal logboek gegevens in bytes. |
|FilterCount| Het totale aantal logboek regels na het Toep assen van het filter. |
|FilterSizeInByte| De totale hoeveelheid logboek gegevens in bytes na het Toep assen van het filter. |
|FetchLogsDurationInMiliSec| Haal de duur van de bewerking op. |
|PaseLogsDurationInMiliSec| De duur van de filter bewerking. |
|PostLogsDurationInMiliSec| Duur van de post bewerking. |

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

Controleer de regels voor het ophaal logboek, de tijden en de grootte als deze instellingen goed worden vervangen ***DoPost*** naar `true` en dat de logboeken met dezelfde filters naar bestemmingen pusht. 

U kunt Logboeken exporteren vanuit de Azure-Blob Storage bij het oplossen van problemen. 

## <a name="common-issues"></a>Algemene problemen

Als het volgende bericht wordt weer gegeven in de module Logboeken, kan het zijn dat uw Azure-abonnement moet worden goedgekeurd: 

Container heeft geen geldige status. De validatie van het abonnement is mislukt met de status komt niet overeen. De API-sleutel is niet bedoeld voor het opgegeven container type. "

Zie [goed keuring aanvragen voor het uitvoeren van de container](spatial-analysis-container.md#request-approval-to-run-the-container)voor meer informatie.

## <a name="troubleshooting-the-azure-stack-edge-device"></a>Problemen met het Azure Stack edge-apparaat oplossen

In de volgende sectie vindt u informatie over fout opsporing en verificatie van de status van uw Azure Stack edge-apparaat.

### <a name="access-the-kubernetes-api-endpoint"></a>Toegang tot het Kubernetes API-eind punt. 

1. Ga in de lokale gebruikers interface van het apparaat naar de pagina **apparaten** . 
2. Kopieer het eind punt van de Kubernetes API-service onder de **eind punten** van het apparaat. Dit eindpunt is een tekenreeks met de volgende indeling: `https://compute..[device-IP-address]`.
3. Sla de tekenreeks met het eindpunt op. U gebruikt dit later bij het configureren `kubectl` voor toegang tot het Kubernetes-cluster.

### <a name="connect-to-powershell-interface"></a>Verbinding maken met de Power shell-interface

Op afstand verbinding maken vanaf een Windows-client. Nadat het Kubernetes-cluster is gemaakt, kunt u de toepassingen beheren via dit cluster. U moet verbinding maken met de Power shell-interface van het apparaat. Afhankelijk van het besturings systeem van de client, kunnen de procedures voor het extern maken van een verbinding met het apparaat afwijken. De volgende stappen gelden voor een Windows-client met Power shell.

> [!TIP]
> * Voordat u begint, moet u ervoor zorgen dat Windows Power shell 5,0 of hoger wordt uitgevoerd op de Windows-client.
> * Power shell is ook [beschikbaar in Linux](/powershell/scripting/install/installing-powershell-core-on-linux).

1. Een Windows Power shell-sessie uitvoeren als beheerder. 
    1. Zorg ervoor dat de Windows Remote Management-service wordt uitgevoerd op de client. Typ bij de opdracht prompt `winrm quickconfig` .

2. Wijs een variabele toe aan het IP-adres van het apparaat. Bijvoorbeeld `$ip = "<device-ip-address>"`.

3. Gebruik de volgende opdracht om het IP-adres van uw apparaat toe te voegen aan de lijst met vertrouwde hosts van de client. 

    ```powershell
    Set-Item WSMan:\localhost\Client\TrustedHosts $ip -Concatenate -Force
    ```
 
4. Start een Windows Power shell-sessie op het apparaat. 

    ```powershell
    Enter-PSSession -ComputerName $ip -Credential $ip\EdgeUser -ConfigurationName Minishell
    ```

5. Geef het wacht woord op wanneer u hierom wordt gevraagd. Gebruik hetzelfde wacht woord dat wordt gebruikt om u aan te melden bij de lokale webinterface. Het standaard wacht woord voor de lokale webinterface is `Password1` . 

### <a name="access-the-kubernetes-cluster"></a>Toegang tot het Kubernetes-cluster

Nadat het Kubernetes-cluster is gemaakt, kunt u het `kubectl` opdracht regel programma gebruiken om toegang te krijgen tot het cluster.

1. Maak een nieuwe naam ruimte. 

    ```powershell
    New-HcsKubernetesNamespace -Namespace
    ```

2. Maak een gebruiker en haal een configuratie bestand op. Met deze opdracht worden de configuratie gegevens voor het Kubernetes-cluster uitgevoerd. Kopieer deze gegevens en sla deze op in een bestand met de naam *config*. Sla het bestand niet op als bestands extensie.
    
    ```powershell
    New-HcsKubernetesUser -UserName
    ```

3. Voeg het *configuratie* bestand toe aan de map *. uitvoeren* in uw gebruikers profiel op de lokale computer.    

4. Koppel de naam ruimte aan de gebruiker die u hebt gemaakt.

    ```powershell
    Grant-HcsKubernetesNamespaceAccess -Namespace -UserName
    ```

5. Installeer `kubectl` op uw Windows-client met behulp van de volgende opdracht:
    
    ```powershell
    curl https://storage.googleapis.com/kubernetesrelease/release/v1.15.2/bin/windows/amd64/kubectl.exe -O kubectl.exe
    ```

6. Voeg een DNS-vermelding toe aan het hosts-bestand op uw systeem. 
    1. Voer Klad blok als Administrator uit en open het *hosts* -bestand dat zich bevindt in `C:\windows\system32\drivers\etc\hosts` . 
    2. Maak een vermelding in het hosts-bestand met het IP-adres van het apparaat en het DNS-domein dat u hebt gekregen op de pagina **apparaat** in de lokale gebruikers interface. Het eind punt dat u moet gebruiken, ziet er ongeveer als volgt uit: `https://compute.asedevice.microsoftdatabox.com/10.100.10.10` .

7. Controleer of u verbinding kunt maken met het Kubernetes.

    ```powershell
    kubectl get pods -n "iotedge"
    ```

Als u container logboeken wilt ophalen, voert u de volgende opdracht uit:

```powershell
kubectl logs <pod-name> -n <namespace> --all-containers
```

### <a name="useful-commands"></a>Handige opdrachten

|Opdracht  |Beschrijving  |
|---------|---------|
|`Get-HcsKubernetesUserConfig -AseUser`     | Hiermee wordt een Kubernetes-configuratie bestand gegenereerd. Wanneer u de opdracht gebruikt, kopieert u de gegevens naar een bestand met de naam *config*. Sla het bestand niet op met een bestands extensie.        |
| `Get-HcsApplianceInfo` | Retourneert informatie over uw apparaat. |
| `Enable-HcsSupportAccess` | Hiermee worden toegangs referenties gegenereerd om een ondersteunings sessie te starten. |


## <a name="how-to-file-a-support-ticket-for-spatial-analysis"></a>Een ondersteunings ticket indienen voor ruimtelijke analyse 

Als u meer ondersteuning nodig hebt bij het vinden van een oplossing voor een probleem dat u hebt met de container voor ruimtelijke analyse, voert u de volgende stappen uit om een ondersteunings ticket in te vullen en in te dienen. Ons team krijgt een extra richt lijn. 

### <a name="fill-out-the-basics"></a>Vul de basis beginselen in 
Maak een nieuw ondersteunings ticket op de pagina [nieuwe ondersteunings aanvraag](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) . Volg de aanwijzingen om de volgende para meters in te vullen:

![Ondersteunings basis](./media/support-ticket-page-1-final.png)

1. Stel het **probleem type** in op `Technical` .
2. Selecteer het abonnement dat u gebruikt voor het implementeren van de container voor ruimtelijke analyse.
3. Selecteer `My services` en selecteer `Cognitive Services` als de service.
4. Selecteer de resource die u wilt gebruiken voor het implementeren van de container voor ruimtelijke analyse.
5. Schrijf een korte beschrijving van het probleem dat u hebt. 
6. Selecteer `Spatial Analysis` als het type probleem.
7. Selecteer de gewenste subtype in de vervolg keuzelijst.
8. Selecteer **volgende: oplossingen** om naar de volgende pagina te gaan.

### <a name="recommended-solutions"></a>Aanbevolen oplossingen
In de volgende fase worden aanbevolen oplossingen aangeboden voor het probleem type dat u hebt geselecteerd. Met deze oplossingen worden de meest voorkomende problemen opgelost, maar als het niet nuttig is voor uw oplossing, selecteert u **volgende: Details** om naar de volgende stap te gaan.

### <a name="details"></a>Details
Op deze pagina kunt u extra informatie over het probleem dat u hebt gericht toevoegen. Zorg ervoor dat u zo veel mogelijk details opneemt, omdat de technici het probleem beter kunnen verfijnen. Neem de contact wijze van uw voor keur en de ernst van het probleem op zodat we u op de juiste wijze kunnen bereiken, en selecteer **volgende: controleren + maken** om naar de volgende stap te gaan. 

### <a name="review-and-create"></a>Controleren en maken 
Bekijk de details van uw ondersteunings aanvraag om ervoor te zorgen dat alles nauw keurig is en het probleem effectief weergeeft. Zodra u klaar bent, selecteert u **maken** om het ticket naar ons team te verzenden. U ontvangt een e-mail bevestiging zodra uw ticket is ontvangen en ons team zal zo snel mogelijk aan de slag gaan. U kunt de status van uw ticket bekijken in het Azure Portal.

## <a name="next-steps"></a>Volgende stappen

* [Een web-app voor het tellen van personen implementeren](spatial-analysis-web-app.md)
* [Ruimtelijke-analyse bewerkingen configureren](./spatial-analysis-operations.md)
* [Gids voor camera plaatsing](spatial-analysis-camera-placement.md)
* [Hand leiding voor zone-en lijn plaatsing](spatial-analysis-zone-line-placement.md)
