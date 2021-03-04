---
title: Problemen oplossen-Azure IoT Edge | Microsoft Docs
description: In dit artikel vindt u informatie over de standaard diagnostische vaardig heden voor Azure IoT Edge, zoals het ophalen van onderdeel status en-Logboeken
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/12/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: d46ad8238faa42ca657b18b3997407d91a224537
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102045918"
---
# <a name="troubleshoot-your-iot-edge-device"></a>Problemen met uw IoT Edge apparaat oplossen

Als u problemen ondervindt met het uitvoeren van Azure IoT Edge in uw omgeving, gebruikt u dit artikel als richt lijn voor het oplossen van problemen en diagnostische gegevens.

## <a name="run-the-check-command"></a>Voer de opdracht ' check ' uit

De eerste stap bij het oplossen van problemen met IoT Edge moet de `check` opdracht gebruiken, waarmee een verzameling configuratie-en connectiviteits tests voor veelvoorkomende problemen wordt uitgevoerd. De `check` opdracht is beschikbaar in [Release 1.0.7](https://github.com/Azure/azure-iotedge/releases/tag/1.0.7) en hoger.

>[!NOTE]
>Het hulp programma voor probleem oplossing kan geen connectiviteit controles uitvoeren als de IoT Edge apparaat zich achter een proxy server bevindt.

U kunt de `check` opdracht als volgt uitvoeren, of de `--help` vlag opnemen om een volledige lijst met opties weer te geven:

Op Linux:

```bash
sudo iotedge check
```

In Windows:

```powershell
iotedge check
```

Het hulp programma voor probleem oplossing voert veel controles uit die in deze drie categorieën worden gesorteerd:

* Met *configuratie controles* wordt informatie onderzocht die kan verhinderen dat IOT edge-apparaten verbinding maken met de Cloud, met inbegrip van problemen met het configuratie bestand en de container engine.
* *Verbindings controles* Controleer of de IOT Edge runtime toegang kan krijgen tot poorten op het hostapparaat en dat alle IOT Edge onderdelen verbinding kunnen maken met de IOT hub. Met deze set controles worden fouten geretourneerd als het IoT Edge apparaat zich achter een proxy bevindt.
* De *productie gereedheids controles controleren* op aanbevolen productie best practices, zoals de status van certificerings instanties voor certificaten en de configuratie van het logboek bestand van de module.

Het hulp programma IoT Edge controle gebruikt een container om de diagnostische gegevens uit te voeren. De container installatie kopie, `mcr.microsoft.com/azureiotedge-diagnostics:latest` , is beschikbaar via de [micro soft-container Registry](https://github.com/microsoft/containerregistry). Als u een controle wilt uitvoeren op een apparaat zonder directe toegang tot internet, moeten uw apparaten toegang hebben tot de container installatie kopie.

Zie [IOT Edge problemen met controles oplossen](https://github.com/Azure/iotedge/blob/master/doc/troubleshoot-checks.md)voor meer informatie over elk van de diagnostische gegevens die door dit hulp programma worden uitgevoerd, waaronder wat u moet doen als er een fout of waarschuwing wordt weer gegeven.

## <a name="gather-debug-information-with-support-bundle-command"></a>Informatie over fout opsporing verzamelen met de opdracht ' ondersteunings bundel '

Wanneer u logboeken van een IoT Edge apparaat wilt verzamelen, kunt u de opdracht het beste gebruiken `support-bundle` . Met deze opdracht worden standaard module, IoT Edge Security Manager en logboeken van de container-engine, `iotedge check` JSON-uitvoer en andere nuttige informatie over fout opsporing verzameld. Ze worden gecomprimeerd tot één bestand, zodat het eenvoudig kan worden gedeeld. De `support-bundle` opdracht is beschikbaar in [Release 1.0.9](https://github.com/Azure/azure-iotedge/releases/tag/1.0.9) en hoger.

Voer de `support-bundle` opdracht uit met de `--since` vlag om op te geven hoelang van het verleden u logboeken wilt ophalen. U `6h` krijgt bijvoorbeeld Logboeken sinds de laatste zes uur, sinds de laatste zes `6d` dagen `6m` sinds de laatste zes minuten, enzovoort. Neem de `--help` vlag op om een volledige lijst met opties weer te geven.

Op Linux:

```bash
sudo iotedge support-bundle --since 6h
```

In Windows:

```powershell
iotedge support-bundle --since 6h
```

U kunt ook een [directe methode](how-to-retrieve-iot-edge-logs.md#upload-support-bundle-diagnostics) aanroep naar uw apparaat gebruiken om de uitvoer van de opdracht voor de ondersteunings bundel te uploaden naar Azure Blob Storage.

> [!WARNING]
> De uitvoer van de `support-bundle` opdracht kan de namen van host, apparaat en module bevatten, informatie die is vastgelegd door uw modules, enzovoort. Houd rekening met het volgende als u de uitvoer in een openbaar forum deelt.

## <a name="check-your-iot-edge-version"></a>Controleer uw IoT Edge versie

Als u een oudere versie van IoT Edge hebt, kunt u het probleem mogelijk oplossen door een upgrade uit te voeren. Het `iotedge check` hulp programma controleert of de IOT Edge Security daemon de meest recente versie is, maar controleert niet de versies van de modules IOT Edge hub en agent. Als u de versie van de runtime modules op uw apparaat wilt controleren, gebruikt u de opdrachten `iotedge logs edgeAgent` en `iotedge logs edgeHub` . Het versienummer staat in de logboeken vermeld wanneer de module wordt gestart.

Zie [IOT Edge Security daemon en runtime bijwerken](how-to-update-iot-edge.md)voor instructies voor het bijwerken van uw apparaat.

## <a name="verify-the-installation-of-iot-edge-on-your-devices"></a>De installatie van IoT Edge op uw apparaten controleren

U kunt de installatie van IoT Edge op uw apparaten controleren door [de edgeAgent-module te controleren](./how-to-monitor-module-twins.md).

Voer de volgende opdracht uit [Azure Cloud shell](https://shell.azure.com/)om de meest recente edgeAgent-module te verkrijgen:

   ```azurecli-interactive
   az iot hub module-twin show --device-id <edge_device_id> --module-id $edgeAgent --hub-name <iot_hub_name>
   ```

Met deze opdracht worden alle edgeAgent- [gerapporteerde eigenschappen](./module-edgeagent-edgehub.md)uitgevoerd. Hier volgen enkele nuttige items om de status van het apparaat te controleren:

* runtime status
* Start tijd van runtime
* laatste afsluit tijd van runtime
* aantal opnieuw gestart runtime

## <a name="check-the-status-of-the-iot-edge-security-manager-and-its-logs"></a>Controleer de status van de IoT Edge Security Manager en de bijbehorende logboeken

De [IOT Edge Security Manager](iot-edge-security-manager.md) is verantwoordelijk voor bewerkingen zoals het initialiseren van het IOT Edge systeem bij het opstarten en het inrichten van apparaten. Als IoT Edge niet wordt gestart, kunnen de logboeken van de beveiligings beheerder nuttige informatie geven.

Op Linux:

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"

* Bekijk de status van de IoT Edge Security Manager:

   ```bash
   sudo systemctl status iotedge
   ```

* Raadpleeg de logboeken van de IoT Edge Security Manager:

   ```bash
   sudo journalctl -u iotedge -f
   ```

* Meer gedetailleerde logboeken van de IoT Edge Security Manager weer geven:

  1. Bewerk de IoT Edge daemon-instellingen:

     ```bash
     sudo systemctl edit iotedge.service
     ```

  2. Werk de volgende regels bij:

     ```bash
     [Service]
     Environment=IOTEDGE_LOG=edgelet=debug
     ```

  3. Start de IoT Edge-beveiligings-daemon opnieuw op:

     ```bash
     sudo systemctl cat iotedge.service
     sudo systemctl daemon-reload
     sudo systemctl restart iotedge
     ```
<!--end 1.1 -->
:::moniker-end

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"

* Bekijk de status van de IoT Edge-systeem services:

   ```bash
   sudo iotedge system status
   ```

* Bekijk de logboeken van de IoT Edge-systeem services:

   ```bash
   sudo iotedge system logs -- -f
   ```

* Schakel logboeken voor fout opsporing in om meer gedetailleerde logboeken van de IoT Edge-systeem services weer te geven:

  1. Schakel logboeken voor fout opsporing in.

     ```bash
     sudo iotedge system set-log-level debug
     sudo iotedge system restart
     ```

  1. Ga terug naar de standaard logboeken voor info niveau na het opsporen van fouten.

     ```bash
     sudo iotedge system set-log-level info
     sudo iotedge system restart
     ```

<!-- end 1.2 -->
:::moniker-end

In Windows:

* Bekijk de status van de IoT Edge Security Manager:

   ```powershell
   Get-Service iotedge
   ```

* Raadpleeg de logboeken van de IoT Edge Security Manager:

   ```powershell
   . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
   ```

* Alleen de laatste 5 minuten van de logboeken van IoT Edge Security Manager weer geven:

   ```powershell
   . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog -StartTime ([datetime]::Now.AddMinutes(-5))
   ```

* Meer gedetailleerde logboeken van de IoT Edge Security Manager weer geven:

  1. Een omgevings variabele op systeem niveau toevoegen:

     ```powershell
     [Environment]::SetEnvironmentVariable("IOTEDGE_LOG", "debug", [EnvironmentVariableTarget]::Machine)
     ```

  2. Start de IoT Edge-beveiligings-daemon opnieuw op:

     ```powershell
     Restart-Service iotedge
     ```

## <a name="check-container-logs-for-issues"></a>Raadpleeg container logboeken voor problemen

Zodra de IoT Edge Security daemon wordt uitgevoerd, bekijkt u de logboeken van de containers om problemen te detecteren. Begin met uw geïmplementeerde containers en bekijk vervolgens de containers waaruit de IoT Edge runtime: edgeAgent en edgeHub. De logboeken van de IoT Edge-agent bevatten doorgaans informatie over de levens cyclus van elke container. De IoT Edge hub-logboeken bieden informatie over berichten en route ring.

```cmd
iotedge logs <container name>
```

U kunt ook een [directe methode](how-to-retrieve-iot-edge-logs.md#upload-module-logs) aanroep naar een module op uw apparaat gebruiken om de logboeken van die module te uploaden naar Azure Blob Storage.

## <a name="view-the-messages-going-through-the-iot-edge-hub"></a>De berichten weer geven die via de IoT Edge hub worden verzonden

<!--1.1 -->
:::moniker range="iotedge-2018-06"

U kunt de berichten van de IoT Edge hub bekijken en inzichten verzamelen uit uitgebreide logboeken van de runtime-containers. Als u uitgebreide logboeken op deze containers wilt inschakelen, stelt u `RuntimeLogLevel` in het configuratie bestand van uw yaml in. Het bestand openen:

Op Linux:

   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

In Windows:

   ```cmd
   notepad C:\ProgramData\iotedge\config.yaml
   ```

Het `agent` element ziet er standaard uit als in het volgende voor beeld:

   ```yaml
   agent:
     name: edgeAgent
     type: docker
     env: {}
     config:
       image: mcr.microsoft.com/azureiotedge-agent:1.1
       auth: {}
   ```

Vervangen `env: {}` door:

   ```yaml
   env:
     RuntimeLogLevel: debug
   ```

   > [!WARNING]
   > YAML-bestanden kunnen geen tabbladen als inspringing bevatten. Gebruik in plaats daarvan 2 spaties. Items op het hoogste niveau kunnen geen voorloop spaties bevatten.

Sla het bestand op en start het IoT Edge Security Manager opnieuw.

<!-- end 1.1 -->
:::moniker-end

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"

U kunt de berichten bekijken die via de IoT Edge hub worden verzonden en inzichten verzamelen van uitgebreide logboeken van de runtime-containers. Als u uitgebreide logboeken op deze containers wilt inschakelen, stelt u de `RuntimeLogLevel` omgevings variabele in het implementatie manifest in.

Als u berichten wilt weer geven die via de IoT Edge hub worden verzonden, stelt `RuntimeLogLevel` u de omgevings variabele in op `debug` voor de edgeHub-module.

Zowel de edgeHub-als edgeAgent-modules hebben deze omgevings variabele voor runtime-logboeken, waarbij de standaard waarde is ingesteld op `info` . Deze omgevings variabele kan de volgende waarden hebben:

* fatale
* fout
* waarschuwing
* Info
* debug
* verbose

<!-- end 1.2 -->
:::moniker-end

U kunt ook de berichten controleren die worden verzonden tussen IoT Hub en IoT-apparaten. Bekijk deze berichten met behulp van de [Azure IOT hub-extensie voor Visual Studio code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit). Zie [het handige hulp programma bij het ontwikkelen met Azure IOT](https://blogs.msdn.microsoft.com/iotdev/2017/09/01/handy-tool-when-you-develop-with-azure-iot/)voor meer informatie.

## <a name="restart-containers"></a>Containers opnieuw opstarten

Nadat u de logboeken en berichten voor informatie hebt onderzocht, kunt u containers opnieuw starten:

```cmd
iotedge restart <container name>
```

Start de IoT Edge runtime-containers opnieuw op:

```cmd
iotedge restart edgeAgent && iotedge restart edgeHub
```

## <a name="check-your-firewall-and-port-configuration-rules"></a>Controleer uw firewall en poort configuratie regels

Zie [een communicatie protocol kiezen](../iot-hub/iot-hub-devguide-protocols.md)Azure IOT Edge communicatie van een on-premises server naar Azure-Cloud met ondersteunde IOT hub protocollen toestaat. Voor een betere beveiliging worden communicatie kanalen tussen Azure IoT Edge en Azure IoT Hub altijd geconfigureerd als uitgaand verkeer. Deze configuratie is gebaseerd op het [service-ondersteunde communicatie patroon](/archive/blogs/clemensv/service-assisted-communication-for-connected-devices), waarmee de kwets baarheid voor een kwaadwillende entiteit wordt geminimaliseerd. Inkomende communicatie is alleen vereist voor specifieke scenario's waarbij Azure-IoT Hub berichten naar het Azure IoT Edge apparaat moet pushen. Cloud-naar-apparaat-berichten worden beveiligd met behulp van beveiligde TLS-kanalen en kunnen verder worden beveiligd met X. 509-certificaten en TPM-apparaat modules. De Azure IoT Edge Security Manager bepaalt hoe deze communicatie tot stand kan worden gebracht. Zie [IOT Edge Security Manager](../iot-edge/iot-edge-security-manager.md).

Hoewel IoT Edge een verbeterde configuratie biedt voor het beveiligen van Azure IoT Edge runtime en geïmplementeerde modules, is het nog steeds afhankelijk van de onderliggende computer-en netwerk configuratie. Daarom is het van cruciaal belang om ervoor te zorgen dat de juiste netwerk-en firewall regels worden ingesteld voor beveiligde Edge-to-Cloud communicatie. De volgende tabel kan worden gebruikt als richt lijn bij de configuratie van firewall regels voor de onderliggende servers waarop Azure IoT Edge runtime wordt gehost:

|Protocol|Poort|Binnenkomende|Verzendt|Hulp|
|--|--|--|--|--|
|MQTT|8883|GEBLOKKEERD (standaard)|GEBLOKKEERD (standaard)|<ul> <li>Configureer uitgaand (uitgaand) dat moet worden geopend wanneer MQTT wordt gebruikt als communicatie protocol.<li>1883 voor MQTT wordt niet ondersteund door IoT Edge. <li>Binnenkomende (binnenkomende) verbindingen moeten worden geblokkeerd.</ul>|
|AMQP|5671|GEBLOKKEERD (standaard)|OPEN (standaard)|<ul> <li>Het standaard communicatie protocol voor IoT Edge. <li> Moet worden geconfigureerd om te worden geopend als Azure IoT Edge niet is geconfigureerd voor andere ondersteunde protocollen of als AMQP het gewenste communicatie protocol is.<li>5672 voor AMQP wordt niet ondersteund door IoT Edge.<li>Deze poort blok keren wanneer Azure IoT Edge gebruikmaakt van een ander IoT Hub ondersteund protocol.<li>Binnenkomende (binnenkomende) verbindingen moeten worden geblokkeerd.</ul></ul>|
|HTTPS|443|GEBLOKKEERD (standaard)|OPEN (standaard)|<ul> <li>Configureer uitgaand (uitgaand) dat moet worden geopend op 443 voor het inrichten van IoT Edge. Deze configuratie is vereist wanneer u hand matige scripts of een Azure IoT Device Provisioning Service (DPS) gebruikt. <li>Binnenkomende (binnenkomende) verbinding moet alleen worden geopend voor specifieke scenario's: <ul> <li>  Als u een transparante gateway hebt met Leaf-apparaten die methode aanvragen kunnen verzenden. In dit geval hoeft u poort 443 niet te openen voor externe netwerken om verbinding te maken met IoTHub of IoTHub-services te bieden via Azure IoT Edge. Daarom kan de binnenkomende regel worden beperkt tot het openen van binnenkomende (binnenkomende) vanuit het interne netwerk. <li> Voor C2D-scenario's (client to Device).</ul><li>80 voor HTTP wordt niet ondersteund door IoT Edge.<li>Als niet-HTTP-protocollen (bijvoorbeeld AMQP of MQTT) niet kunnen worden geconfigureerd in de onderneming; de berichten kunnen via websockets worden verzonden. Poort 443 wordt in dat geval gebruikt voor WebSocket-communicatie.</ul>|

## <a name="next-steps"></a>Volgende stappen

Denkt u dat u een fout op het IoT Edge-platform hebt gevonden? [Dien een probleem](https://github.com/Azure/iotedge/issues) in zodat we kunnen blijven verbeteren.

Als u meer vragen hebt, maakt u een [ondersteuningsaanvraag](https://portal.azure.com/#create/Microsoft.Support) voor hulp.
