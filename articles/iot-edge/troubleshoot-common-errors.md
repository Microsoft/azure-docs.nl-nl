---
title: Veelvoorkomende fouten-Azure IoT Edge | Microsoft Docs
description: Gebruik dit artikel om veelvoorkomende problemen op te lossen die optreden bij het implementeren van een IoT Edge oplossing
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 03/01/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: 0d36a51865f3ed4a093998b16aaa174432c5308a
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/03/2021
ms.locfileid: "106275648"
---
# <a name="common-issues-and-resolutions-for-azure-iot-edge"></a>Veelvoorkomende problemen en oplossingen voor Azure IoT Edge

[!INCLUDE [iot-edge-version-201806-or-202011](../../includes/iot-edge-version-201806-or-202011.md)]

In dit artikel vindt u de stappen voor het oplossen van veelvoorkomende problemen die zich kunnen voordoen bij het implementeren van IoT Edge oplossingen. Als u wilt weten hoe u Logboeken en fouten van uw IoT Edge apparaat kunt vinden, raadpleegt u [problemen met uw IOT edge apparaat oplossen](troubleshoot.md).

## <a name="iot-edge-agent-stops-after-about-a-minute"></a>IoT Edge agent stopt na ongeveer een minuut

**Waargenomen gedrag:**

De edgeAgent-module wordt ongeveer een minuut gestart en uitgevoerd en vervolgens gestopt. In de logboeken wordt aangegeven dat de IoT Edge-agent probeert verbinding te maken met IoT Hub via AMQP en vervolgens probeert verbinding te maken via AMQP via WebSocket. Als dit mislukt, wordt de IoT Edge-agent afgesloten.

Voor beeld van edgeAgent-logboeken:

```output
2017-11-28 18:46:19 [INF] - Starting module management agent.
2017-11-28 18:46:19 [INF] - Version - 1.0.7516610 (03c94f85d0833a861a43c669842f0817924911d5)
2017-11-28 18:46:19 [INF] - Edge agent attempting to connect to IoT Hub via AMQP...
2017-11-28 18:46:49 [INF] - Edge agent attempting to connect to IoT Hub via AMQP over WebSocket...
```

**Hoofd oorzaak:**

Een netwerk configuratie op het doelnet werk verhindert dat de IoT Edge agent het netwerk bereikt. De agent probeert eerst verbinding maken via AMQP (poort 5671). Als de verbinding is mislukt, probeert de websockets (poort 443).

De IoT Edge-runtime stelt een netwerk in voor elk van de modules waarmee moet worden gecommuniceerd. In Linux is dit netwerk een brugnetwerk. In Windows wordt NAT gebruikt. Dit probleem komt vaker voor op Windows-apparaten die gebruikmaken van Windows-containers die het NAT-netwerk gebruiken.

**Oplossing:**

Zorg ervoor dat er een route naar internet is voor de IP-adressen die aan deze brug/dit NAT-netwerk zijn toegewezen. Soms heeft een VPN-configuratie op de host voorrang op het IoT Edge-netwerk.

## <a name="iot-edge-agent-cant-access-a-modules-image-403"></a>IoT Edge agent heeft geen toegang tot de installatie kopie van een module (403)

**Waargenomen gedrag:**

Een container kan niet worden uitgevoerd, en in de edgeAgent-Logboeken wordt een 403-fout weer gegeven.

**Hoofd oorzaak:**

De IoT Edge-agent heeft geen machtigingen voor toegang tot de installatie kopie van een module.

**Oplossing:**

Zorg ervoor dat uw register referenties correct zijn opgegeven in het implementatie manifest.

## <a name="edge-agent-module-reports-empty-config-file-and-no-modules-start-on-the-device"></a>De module Edge agent rapporteert een leeg configuratie bestand en er worden geen modules gestart op het apparaat

**Waargenomen gedrag:**

Het apparaat heeft geen problemen met het starten van modules die in de implementatie zijn gedefinieerd. Alleen de edgeAgent wordt uitgevoerd, maar doorlopend een leeg configuratie bestand...

**Hoofd oorzaak:**

Standaard worden modules IoT Edge in hun eigen geïsoleerde container netwerk gestart. Het apparaat heeft mogelijk problemen met de DNS-naam omzetting binnen dit particuliere netwerk.

**Oplossing:**

**Optie 1: DNS-server instellen in container Engine-instellingen**

Geef de DNS-server voor uw omgeving op in de instellingen van de container-engine, die van toepassing is op alle container modules die zijn gestart door de engine. Maak een bestand met `daemon.json` de naam en geef de DNS-server op die moet worden gebruikt. Bijvoorbeeld:

```json
{
    "dns": ["1.1.1.1"]
}
```

In het bovenstaande voor beeld wordt de DNS-server ingesteld op een openbaar toegankelijke DNS-service. Als het edge-apparaat geen toegang kan krijgen tot dit IP-adres in de omgeving, vervangt u het door de DNS-server die toegankelijk is.

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"
Plaats `daemon.json` op de juiste locatie voor uw platform:

| Platform | Locatie |
| --------- | -------- |
| Linux | `/etc/docker` |
| Windows-host met Windows-containers | `C:\ProgramData\iotedge-moby\config` |

Als de locatie al een `daemon.json` bestand bevat, voegt u de **DNS-** sleutel hieraan toe en slaat u het bestand op.

Start de container-engine opnieuw op om de updates van kracht te laten worden.

| Platform | Opdracht |
| --------- | -------- |
| Linux | `sudo systemctl restart docker` |
| Windows (admin Power shell) | `Restart-Service iotedge-moby -Force` |

:::moniker-end
<!-- end 1.1 -->

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"
Plaats `daemon.json` in de `/etc/docker` map op het apparaat.

Als de locatie al een `daemon.json` bestand bevat, voegt u de **DNS-** sleutel hieraan toe en slaat u het bestand op.

Start de container-engine opnieuw op om de updates van kracht te laten worden.

```bash
sudo systemctl restart docker
```

:::moniker-end
<!-- end 1.2 -->

**Optie 2: DNS-server instellen in IoT Edge-implementatie per module**

U kunt de DNS-server voor de *createOptions* van elke module instellen in de implementatie van IOT Edge. Bijvoorbeeld:

```json
"createOptions": {
  "HostConfig": {
    "Dns": [
      "x.x.x.x"
    ]
  }
}
```

Zorg ervoor dat u deze configuratie ook instelt voor de *edgeAgent* -en *edgeHub* -modules.

## <a name="iot-edge-hub-fails-to-start"></a>IoT Edge hub kan niet worden gestart

**Waargenomen gedrag:**

De edgeHub-module kan niet worden gestart. Mogelijk wordt een bericht weer gegeven als een van de volgende fouten in de logboeken:

```output
One or more errors occurred.
(Docker API responded with status code=InternalServerError, response=
{\"message\":\"driver failed programming external connectivity on endpoint edgeHub (6a82e5e994bab5187939049684fb64efe07606d2bb8a4cc5655b2a9bad5f8c80):
Error starting userland proxy: Bind for 0.0.0.0:443 failed: port is already allocated\"}\n)
```

of

```output
info: edgelet_docker::runtime -- Starting module edgeHub...
warn: edgelet_utils::logging -- Could not start module edgeHub
warn: edgelet_utils::logging --     caused by: failed to create endpoint edgeHub on network nat: hnsCall failed in Win32:  
        The process cannot access the file because it is being used by another process. (0x20)
```

**Hoofd oorzaak:**

Een ander proces op de hostcomputer heeft een poort gebonden aan het koppelen van de edgeHub-module. De IoT Edge hub wijst poorten 443, 5671 en 8883 toe voor gebruik in Gateway scenario's. De module kan niet worden gestart als een ander proces al een van deze poorten heeft gebonden.

**Oplossing:**

U kunt dit probleem op twee manieren oplossen:

Als het IoT Edge-apparaat als een gateway apparaat fungeert, moet u het proces dat poort 443, 5671 of 8883 gebruikt, vinden en stoppen. Een fout voor poort 443 betekent meestal dat het andere proces een webserver is.

Als u het IoT Edge apparaat niet als gateway hoeft te gebruiken, kunt u de poort bindingen verwijderen uit de module voor het maken van edgeHub. U kunt de opties voor het maken van wijzigingen in de Azure Portal of rechtstreeks in de deployment.jsin het bestand wijzigen.

In Azure Portal:

1. Navigeer naar uw IoT-hub en selecteer **IOT Edge**.

2. Selecteer het IoT Edge apparaat dat u wilt bijwerken.

3. Selecteer **modules instellen**.

4. Selecteer **runtime-instellingen**.

5. Verwijder in de instellingen van de **Edge hub** -module alles uit het tekstvak **maken opties** .

6. Sla de wijzigingen op en maak de implementatie.

In het bestand deployment.js:

1. Open de deployment.jsop het bestand dat u op uw IoT Edge apparaat hebt toegepast.

2. Zoek de `edgeHub` instellingen in de sectie gewenste eigenschappen edgeAgent:

   ```json
   "edgeHub": {
       "settings": {
           "image": "mcr.microsoft.com/azureiotedge-hub:1.1",
           "createOptions": "{\"HostConfig\":{\"PortBindings\":{\"8883/tcp\":[{\"HostPort\":\"8883\"}],\"443/tcp\":[{\"HostPort\":\"443\"}]}}}"
       },
       "type": "docker",
       "status": "running",
       "restartPolicy": "always"
   }
   ```

3. Verwijder de `createOptions` lijn en de afsluitende komma aan het einde van de `image` regel voor de volgende:

   ```json
   "edgeHub": {
       "settings": {
           "image": "mcr.microsoft.com/azureiotedge-hub:1.1"
       },
       "type": "docker",
       "status": "running",
       "restartPolicy": "always"
   }
   ```

4. Sla het bestand op en pas het opnieuw toe op uw IoT Edge apparaat.

## <a name="iot-edge-security-daemon-fails-with-an-invalid-hostname"></a>IoT Edge Security daemon mislukt met een ongeldige hostnaam

**Waargenomen gedrag:**

Er wordt geprobeerd [de IOT Edge Security Manager-logboeken te controleren](troubleshoot.md#check-the-status-of-the-iot-edge-security-manager-and-its-logs) en het volgende bericht wordt afgedrukt:

```output
Error parsing user input data: invalid hostname. Hostname cannot be empty or greater than 64 characters
```

**Hoofd oorzaak:**

De runtime van IoT Edge kan alleen hostnamen ondersteunen die korter zijn dan 64 tekens. Fysieke machines hebben doorgaans geen lange hostnamen, maar het probleem komt vaker voor op een virtuele machine. De automatisch gegenereerde hostnamen voor virtuele Windows-machines die worden gehost in azure, met name meestal lang.

**Oplossing:**

Wanneer u deze fout ziet, kunt u deze oplossen door de DNS-naam van de virtuele machine te configureren en vervolgens de DNS-naam als hostnaam in te stellen in de installatie opdracht.

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"

1. Ga in het Azure Portal naar de overzichts pagina van de virtuele machine.
2. Selecteer **configureren** onder DNS-naam. Als er al een DNS-naam is geconfigureerd op de virtuele machine, hoeft u geen nieuwe te configureren.

   ![De DNS-naam van de virtuele machine configureren](./media/troubleshoot/configure-dns.png)

3. Geef een waarde op voor het label voor de **DNS-naam** en selecteer **Opslaan**.
4. Kopieer de nieuwe DNS-naam, die de indeling moet hebben **\<DNSnamelabel\> . \<vmlocation\> .. cloudapp.azure.com**.
5. Gebruik in de virtuele machine de volgende opdracht om de IoT Edge runtime in te stellen met uw DNS-naam:

   * Op Linux:

      ```bash
      sudo nano /etc/iotedge/config.yaml
      ```

   * In Windows:

      ```cmd
      notepad C:\ProgramData\iotedge\config.yaml
      ```

:::moniker-end
<!-- end 1.1 -->

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"

1. Ga in het Azure Portal naar de overzichts pagina van de virtuele machine.

2. Selecteer **configureren** onder DNS-naam. Als er al een DNS-naam is geconfigureerd op de virtuele machine, hoeft u geen nieuwe te configureren.

   ![De DNS-naam van de virtuele machine configureren](./media/troubleshoot/configure-dns.png)

3. Geef een waarde op voor het label voor de **DNS-naam** en selecteer **Opslaan**.

4. Kopieer de nieuwe DNS-naam, die de indeling moet hebben **\<DNSnamelabel\> . \<vmlocation\> .. cloudapp.azure.com**.

5. Open het configuratie bestand op het IoT Edge apparaat.

   ```bash
   sudo nano /etc/aziot/config.toml
   ```

6. Vervang de waarde van `hostname` door uw DNS-naam.

7. Sla het bestand op en sluit het en pas vervolgens de wijzigingen toe op IoT Edge.

   ```bash
   sudo iotedge config apply
   ```

:::moniker-end
<!-- end 1.2 -->

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"

## <a name="cant-get-the-iot-edge-daemon-logs-on-windows"></a>Kan de IoT Edge daemon-logboeken niet ophalen in Windows

**Waargenomen gedrag:**

U krijgt een EventLogException wanneer u `Get-WinEvent` op Windows gebruikt.

**Hoofd oorzaak:**

De `Get-WinEvent` Power shell-opdracht is afhankelijk van een register vermelding die aanwezig moet zijn om logboeken te zoeken op basis van een specifieke `ProviderName` .

**Oplossing:**

Stel een register vermelding voor de IoT Edge-daemon in. Maak een **iotedge. reg** -bestand met de volgende inhoud en importeer het in het Windows-REGI ster door erop te dubbel klikken of door de opdracht te gebruiken `reg import iotedge.reg` :

```reg
Windows Registry Editor Version 5.00

[HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\EventLog\Application\iotedged]
"CustomSource"=dword:00000001
"EventMessageFile"="C:\\ProgramData\\iotedge\\iotedged.exe"
"TypesSupported"=dword:00000007
```

:::moniker-end
<!-- end 1.1 -->

## <a name="stability-issues-on-smaller-devices"></a>Stabiliteits problemen op kleinere apparaten

**Waargenomen gedrag:**

U kunt stabiliteits problemen ondervinden op apparaten die zijn beperkt tot een resource, zoals de Raspberry Pi, met name wanneer deze wordt gebruikt als een gateway. Voor beelden van problemen zijn uitzonde ringen in de IoT Edge-hub, downstream-apparaten die niet kunnen worden verbonden of het apparaat kan geen telemetriegegevens verzenden na een paar uur.

**Hoofd oorzaak:**

De IoT Edge hub, die deel uitmaakt van de IoT Edge runtime, is standaard geoptimaliseerd voor prestaties en probeert grote delen van geheugen toe te wijzen. Deze optimalisatie is niet ideaal voor beperkte edge-apparaten en kan stabiliteits problemen veroorzaken.

**Oplossing:**

Stel voor de IoT Edge hub een omgevings variabele **OptimizeForPerformance** in op **False**. Er zijn twee manieren om omgevings variabelen in te stellen:

In Azure Portal:

Selecteer in uw IOT hub uw IOT edge-apparaat en op de pagina Details van apparaat en selecteer runtime-instellingen voor **modules instellen**  >  . Maak een omgevings variabele voor de IoT Edge hub-module met de naam *OptimizeForPerformance* die is ingesteld op *False*.

![OptimizeForPerformance ingesteld op False](./media/troubleshoot/optimizeforperformance-false.png)

In het implementatie manifest:

```json
"edgeHub": {
  "type": "docker",
  "settings": {
    "image": "mcr.microsoft.com/azureiotedge-hub:1.1",
    "createOptions": <snipped>
  },
  "env": {
    "OptimizeForPerformance": {
      "value": "false"
    }
  },
```

## <a name="iot-edge-module-fails-to-send-a-message-to-edgehub-with-404-error"></a>IoT Edge-module kan geen bericht verzenden naar edgeHub met een fout van 404

**Waargenomen gedrag:**

Een aangepaste IoT Edge module kan geen bericht verzenden naar de IoT Edge hub met een 404- `Module not found` fout. De IoT Edge-daemon drukt het volgende bericht in de logboeken af:

```output
Error: Time:Thu Jun  4 19:44:58 2018 File:/usr/sdk/src/c/provisioning_client/adapters/hsm_client_http_edge.c Func:on_edge_hsm_http_recv Line:364 executing HTTP request fails, status=404, response_buffer={"message":"Module not found"}u, 04 )
```

**Hoofd oorzaak:**

De IoT Edge-daemon dwingt proces identificatie af voor alle modules die om veiligheids redenen verbinding maken met de edgeHub. Hiermee wordt gecontroleerd of alle berichten die worden verzonden door een module afkomstig zijn van de hoofd proces-ID van de module. Als een bericht wordt verzonden door een module van een andere proces-ID dan oorspronkelijk tot stand is gebracht, wordt het bericht met een 404-fout bericht geweigerd.

**Oplossing:**

Vanaf versie 1.0.7 zijn alle module processen gemachtigd om verbinding te maken. Zie [1.0.7 release wijzigingen logboek](https://github.com/Azure/iotedge/blob/master/CHANGELOG.md#iotedged-1)voor meer informatie.

Als u een upgrade naar 1.0.7 niet mogelijk hebt, voert u de volgende stappen uit. Zorg ervoor dat dezelfde proces-ID altijd wordt gebruikt door de aangepaste IoT Edge module om berichten naar de edgeHub te verzenden. Zorg er bijvoorbeeld voor dat u in `ENTRYPOINT` plaats van `CMD` de opdracht in het docker-bestand. De `CMD` opdracht leidt naar één proces-id voor de module en een andere proces-id voor de opdracht bash, die het hoofd programma uitvoert, maar `ENTRYPOINT` leidt tot één proces-id.

## <a name="iot-edge-module-deploys-successfully-then-disappears-from-device"></a>IoT Edge module is geïmplementeerd, verdwijnt vervolgens van apparaat

**Waargenomen gedrag:**

Nadat u modules voor een IoT Edge apparaat hebt ingesteld, worden de modules geïmplementeerd, maar na een paar minuten worden ze verwijderd van het apparaat en van de details van het apparaat in de Azure Portal. Andere modules dan die zijn gedefinieerd, kunnen ook op het apparaat worden weer gegeven.

**Hoofd oorzaak:**

Als een automatische implementatie gericht is op een apparaat, heeft dit prioriteit boven het hand matig instellen van de modules voor één apparaat. De functionaliteit voor het **instellen van modules** in azure portal of het **maken van implementatie voor** de functionaliteit van één apparaat in Visual Studio code zal even duren. U ziet de modules die u hebt gedefinieerd, gestart op het apparaat. Vervolgens wordt de prioriteit van de automatische implementatie in en worden de gewenste eigenschappen van het apparaat overschreven.

**Oplossing:**

Gebruik slechts één type implementatie mechanisme per apparaat, hetzij een automatische implementatie als implementatie van een afzonderlijk apparaat. Als u meerdere automatische implementaties hebt die gericht zijn op een apparaat, kunt u prioriteits-of doel beschrijvingen wijzigen om ervoor te zorgen dat de juiste van toepassing is op een bepaald apparaat. U kunt het apparaat ook bijwerken tussen en niet langer overeenkomen met de doel beschrijving van de automatische implementatie.

Zie [informatie over IOT Edge automatische implementaties voor één apparaat of op schaal](module-deployment-monitoring.md)voor meer informatie.

<!-- <1.2> -->
::: moniker range=">=iotedge-2020-11"

## <a name="iot-edge-behind-a-gateway-cannot-perform-http-requests-and-start-edgeagent-module"></a>IoT Edge achter een gateway kan geen HTTP-aanvragen uitvoeren en de edgeAgent-module starten

**Waargenomen gedrag:**

De IoT Edge-daemon is actief met een geldig configuratie bestand, maar de edgeAgent-module kan niet worden gestart. De opdracht `iotedge list` retourneert een lege lijst. Het IoT Edge daemon-logboeken rapport `Could not perform HTTP request` .

**Hoofd oorzaak:**

IoT Edge apparaten achter een gateway halen hun module-installatie kopieën van het bovenliggende IoT Edge apparaat dat is opgegeven in het `parent_hostname` veld van het configuratie bestand. De `Could not perform HTTP request` fout betekent dat het onderliggende apparaat het bovenliggende apparaat niet via http kan bereiken.

**Oplossing:**

Zorg ervoor dat het bovenliggende IoT Edge apparaat binnenkomende aanvragen kan ontvangen van het onderliggende IoT Edge-apparaat. Open netwerk verkeer op de poorten 443 en 6617 voor aanvragen die afkomstig zijn van het onderliggende apparaat.

:::moniker-end
<!-- end 1.2 -->

## <a name="next-steps"></a>Volgende stappen

Denkt u dat u een fout op het IoT Edge-platform hebt gevonden? [Dien een probleem](https://github.com/Azure/iotedge/issues) in zodat we kunnen blijven verbeteren.

Als u meer vragen hebt, maakt u een [ondersteuningsaanvraag](https://portal.azure.com/#create/Microsoft.Support) voor hulp.
