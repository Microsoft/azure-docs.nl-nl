---
title: Apparaten configureren voor netwerkproxy's-Azure IoT Edge | Microsoft Docs
description: De Azure IoT Edge runtime en alle modules op Internet gericht IoT Edge configureren om te communiceren via een proxy server.
author: kgremban
ms.author: kgremban
ms.date: 09/03/2020
ms.topic: how-to
ms.service: iot-edge
services: iot-edge
ms.custom:
- amqp
- contperf-fy21q1
ms.openlocfilehash: 888761bb976b9d7a87211a77cb6504a44f108bbd
ms.sourcegitcommit: 5f32f03eeb892bf0d023b23bd709e642d1812696
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/12/2021
ms.locfileid: "103200046"
---
# <a name="configure-an-iot-edge-device-to-communicate-through-a-proxy-server"></a>Een IoT Edge-apparaat configureren om te communiceren via een proxyserver

[!INCLUDE [iot-edge-version-201806-or-202011](../../includes/iot-edge-version-201806-or-202011.md)]

IoT Edge-apparaten verzenden HTTPS-aanvragen om te communiceren met IoT Hub. Als uw apparaat is verbonden met een netwerk dat gebruikmaakt van een proxy server, moet u de IoT Edge runtime configureren om te communiceren via de-server. Proxy servers kunnen ook van invloed zijn op afzonderlijke IoT Edge modules als ze HTTP-of HTTPS-aanvragen maken die niet via de IoT Edge hub worden gerouteerd.

In dit artikel worden de volgende vier stappen door lopen om een IoT Edge apparaat te configureren en vervolgens te beheren achter een proxy server:

1. [**Installeer de IoT Edge runtime op uw apparaat**](#install-iot-edge-through-a-proxy)

   Met de IoT Edge-installatie scripts worden pakketten en bestanden van het internet opgehaald, zodat uw apparaat moet communiceren via de proxy server om die aanvragen te kunnen uitvoeren. Voor Windows-apparaten biedt het installatie script ook een offline-installatie optie.

   Deze stap is een eenmalig proces om het IoT Edge-apparaat te configureren wanneer u het voor het eerst instelt. Dezelfde verbindingen zijn ook vereist wanneer u de IoT Edge runtime bijwerkt.

2. [**IoT Edge en de container runtime op uw apparaat configureren**](#configure-iot-edge-and-moby)

   IoT Edge is verantwoordelijk voor communicatie met IoT Hub. De container runtime is verantwoordelijk voor container beheer en communiceert daarom met container registers. Beide onderdelen moeten webaanvragen indienen via de proxy server.

   Deze stap is een eenmalig proces om het IoT Edge-apparaat te configureren wanneer u het voor het eerst instelt.

3. [**De eigenschappen van de IoT Edge agent configureren in het configuratie bestand op uw apparaat**](#configure-the-iot-edge-agent)

   De edgeAgent-module wordt in eerste instantie door de IoT Edge daemon gestart. Vervolgens haalt de edgeAgent-module het implementatie manifest op uit IoT Hub en worden alle andere modules gestart. Configureer de edgeAgent-module omgevings variabelen hand matig op het apparaat zelf om de IoT Edge-agent in te stellen voor de eerste verbinding met IoT Hub. Na de eerste verbinding kunt u de edgeAgent-module op afstand configureren.

   Deze stap is een eenmalig proces om het IoT Edge-apparaat te configureren wanneer u het voor het eerst instelt.

4. [**Voor alle toekomstige module-implementaties stelt u omgevings variabelen in voor elke module die communiceert via de proxy**](#configure-deployment-manifests)

   Nadat uw IoT Edge apparaat is ingesteld en via de proxy server is verbonden met IoT Hub, moet u de verbinding in alle toekomstige module-implementaties onderhouden.

   Deze stap is een doorlopend proces dat op afstand wordt uitgevoerd, zodat elke nieuwe module of implementatie-update de mogelijkheid houdt om te communiceren via de proxy server.

## <a name="know-your-proxy-url"></a>Ken uw proxy-URL

Voordat u aan de slag gaat met een van de stappen in dit artikel, moet u weten wat uw proxy-URL is.

Proxy-Url's hebben de volgende indeling: **protocol**://**proxy_host**:**proxy_port**.

* Het **protocol** is http of https. De docker-daemon kan gebruikmaken van een van beide protocollen, afhankelijk van de Register instellingen van uw container, maar de IoT Edge-daemon-en runtime-containers moeten altijd HTTP gebruiken om verbinding te maken met de proxy.

* De **proxy_host** is een adres voor de proxy server. Als voor uw proxy server verificatie is vereist, kunt u uw referenties opgeven als onderdeel van de proxy-host met de volgende indeling: **gebruiker**:**wacht woord** \@ **proxy_host**.

* De **proxy_port** is de netwerk poort waarop de proxy reageert op netwerk verkeer.

## <a name="install-iot-edge-through-a-proxy"></a>IoT Edge installeren via een proxy

Of uw IoT Edge-apparaat wordt uitgevoerd op Windows of Linux, u hebt toegang tot de installatie pakketten via de proxy server. Afhankelijk van uw besturings systeem voert u de stappen uit om de IoT Edge-runtime te installeren via een proxy server.

### <a name="linux-devices"></a>Linux-apparaten

Als u de IoT Edge runtime op een Linux-apparaat installeert, configureert u pakket beheer om door te gaan met de proxy server om toegang te krijgen tot het installatie pakket. [Stel bijvoorbeeld apt-get in voor het gebruik van een http-proxy](https://help.ubuntu.com/community/AptGet/Howto/#Setting_up_apt-get_to_use_a_http-proxy). Als uw pakket beheer is geconfigureerd, volgt u de instructies in [Install Azure IOT Edge runtime](how-to-install-iot-edge.md) zoals gebruikelijk.

### <a name="windows-devices"></a>Windows-apparaten

Als u de IoT Edge runtime op een Windows-apparaat installeert, moet u de proxy server twee keer door lopen. De eerste verbinding downloadt het installatie script bestand en de tweede verbinding tijdens de installatie om de benodigde onderdelen te downloaden. U kunt proxy gegevens in Windows-instellingen configureren of uw proxy gegevens rechtstreeks in de Power shell-opdrachten toevoegen.

In de volgende stappen ziet u een voor beeld van een Windows-installatie met behulp van het `-proxy` argument:

1. De Invoke-WebRequest-opdracht heeft proxy gegevens nodig om toegang te krijgen tot het installatie script. De Deploy-IoTEdge-opdracht heeft de proxy gegevens nodig om de installatie bestanden te downloaden.

   ```powershell
   . {Invoke-WebRequest -proxy <proxy URL> -useb aka.ms/iotedge-win} | Invoke-Expression; Deploy-IoTEdge -proxy <proxy URL>
   ```

2. De Initialize-IoTEdge opdracht hoeft de proxy server niet te door lopen, dus voor de tweede stap is alleen proxy-informatie vereist voor invoke-WebRequest.

   ```powershell
   . {Invoke-WebRequest -proxy <proxy URL> -useb aka.ms/iotedge-win} | Invoke-Expression; Initialize-IoTEdge
   ```

Als u gecompliceerde referenties hebt voor de proxy server die niet kan worden opgenomen in de URL, gebruikt u de `-ProxyCredential` para meter in `-InvokeWebRequestParameters` . Bijvoorbeeld:

```powershell
$proxyCredential = (Get-Credential).GetNetworkCredential()
. {Invoke-WebRequest -proxy <proxy URL> -ProxyCredential $proxyCredential -useb aka.ms/iotedge-win} | Invoke-Expression; `
Deploy-IoTEdge -InvokeWebRequestParameters @{ '-Proxy' = '<proxy URL>'; '-ProxyCredential' = $proxyCredential }
```

Zie [invoke-WebRequest](/powershell/module/microsoft.powershell.utility/invoke-webrequest)voor meer informatie over proxy parameters. Zie [Power shell-scripts voor IOT Edge in Windows](reference-windows-scripts.md)voor meer informatie over para meters voor de installatie van Windows.

## <a name="configure-iot-edge-and-moby"></a>IoT Edge en Moby configureren

IoT Edge is afhankelijk van twee daemons die op het IoT Edge apparaat worden uitgevoerd. De Moby-daemon maakt webaanvragen voor het ophalen van container installatie kopieën vanuit container registers. Met de IoT Edge-daemon kunnen webaanvragen communiceren met IoT Hub.

Zowel de Moby-als de IoT Edge-daemons moeten worden geconfigureerd voor het gebruik van de proxy server voor doorlopende functionaliteit van het apparaat. Deze stap vindt plaats op het IoT Edge-apparaat tijdens de eerste installatie van het apparaat.

### <a name="moby-daemon"></a>Moby-daemon

Omdat Moby is gebouwd op docker, raadpleegt u de docker-documentatie voor het configureren van de Moby-daemon met omgevings variabelen. De meeste container registers (met inbegrip van DockerHub en Azure-container registers) ondersteunen HTTPS-aanvragen, dus de para meter die u moet instellen is **HTTPS_PROXY**. Als u installatie kopieën wilt halen uit een REGI ster dat TLS (trans port Layer Security) niet ondersteunt, moet u de para meter **HTTP_PROXY** instellen.

Kies het artikel dat van toepassing is op uw IoT Edge besturings systeem van het apparaat:

* [Docker-daemon configureren op Linux](https://docs.docker.com/config/daemon/systemd/#httphttps-proxy) De Moby-daemon op Linux-apparaten behoudt de naam docker.
* [Docker-daemon configureren in Windows](/virtualization/windowscontainers/manage-docker/configure-docker-daemon#proxy-configuration) De Moby-daemon op Windows-apparaten heet iotedge-Moby. De namen verschillen, omdat het mogelijk is om zowel docker Desktop als Moby parallel op een Windows-apparaat uit te voeren.

### <a name="iot-edge-daemon"></a>IoT Edge-daemon

De IoT Edge-daemon is op een vergelijk bare manier geconfigureerd als de Moby-daemon. Gebruik de volgende stappen om een omgevings variabele voor de service in te stellen op basis van uw besturings systeem.

De IoT Edge-daemon maakt altijd gebruik van HTTPS voor het verzenden van aanvragen naar IoT Hub.

#### <a name="linux"></a>Linux

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"

Open een editor in de terminal om de IoT Edge daemon te configureren.

```bash
sudo systemctl edit iotedge
```

Voer de volgende tekst in en vervang **\<proxy URL>** door het adres en de poort van uw proxy server. Sla het bestand vervolgens op en sluit het af.

```ini
[Service]
Environment="https_proxy=<proxy URL>"
```

Vernieuw de Service Manager om de nieuwe configuratie voor IoT Edge op te halen.

```bash
sudo systemctl daemon-reload
```

Start IoT Edge opnieuw om de wijzigingen van kracht te laten worden.

```bash
sudo systemctl restart iotedge
```

Controleer of uw omgevings variabele is gemaakt en de nieuwe configuratie is geladen.

```bash
systemctl show --property=Environment iotedge
```
:::moniker-end
<!--end 1.1-->

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"

Open een editor in de terminal om de IoT Edge daemon te configureren.

```bash
sudo systemctl edit aziot-edged
```

Voer de volgende tekst in en vervang **\<proxy URL>** door het adres en de poort van uw proxy server. Sla het bestand vervolgens op en sluit het af.

```ini
[Service]
Environment="https_proxy=<proxy URL>"
```

Vanaf versie 1,2 maakt IoT Edge gebruik van de IoT-identiteits service om het inrichten van apparaten met IoT Hub of IoT Hub Device Provisioning Service af te handelen. Open een editor in de terminal om de IoT Identity service-daemon te configureren.

```bash
sudo systemctl edit aziot-identityd
```

Voer de volgende tekst in en vervang **\<proxy URL>** door het adres en de poort van uw proxy server. Sla het bestand vervolgens op en sluit het af.

```ini
[Service]
Environment="https_proxy=<proxy URL>"
```

Vernieuw de Service Manager om de nieuwe configuraties op te halen.

```bash
sudo systemctl daemon-reload
```

Start de IoT Edge-systeem services opnieuw op om de wijzigingen van beide daemons van kracht te laten worden.

```bash
sudo iotedge system restart
```

Controleer of uw omgevings variabelen zijn gemaakt en de nieuwe configuratie is geladen.

```bash
systemctl show --property=Environment aziot-edged
systemctl show --property=Environment aziot-identityd
```
:::moniker-end
<!--end 1.2-->

#### <a name="windows"></a>Windows

Open een Power shell-venster als beheerder en voer de volgende opdracht uit om het REGI ster te bewerken met de nieuwe omgevings variabele. Vervang door **\<proxy url>** het adres en de poort van uw proxy server.

```powershell
reg add HKLM\SYSTEM\CurrentControlSet\Services\iotedge /v Environment /t REG_MULTI_SZ /d https_proxy=<proxy URL>
```

Start IoT Edge opnieuw om de wijzigingen van kracht te laten worden.

```powershell
Restart-Service iotedge
```

## <a name="configure-the-iot-edge-agent"></a>De IoT Edge-agent configureren

De IoT Edge-agent is de eerste module die op een IoT Edge apparaat kan worden gestart. Het wordt voor de eerste keer gestart op basis van de informatie in het IoT Edge configuratie bestand. De IoT Edge agent maakt vervolgens verbinding met IoT Hub om implementatie manifesten op te halen die declareren welke andere modules op het apparaat moeten worden geïmplementeerd.

Deze stap vindt eenmaal plaats op het IoT Edge apparaat tijdens de eerste installatie van het apparaat.

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"

1. Open het bestand config. yaml op uw IoT Edge-apparaat. Op Linux-systemen bevindt dit bestand zich op **/etc/iotedge/config.yaml**. Op Windows-systemen bevindt dit bestand zich op **C:\ProgramData\iotedge\config.yaml**. Het configuratie bestand is beveiligd, dus u hebt beheerders bevoegdheden nodig om het te openen. Op Linux-systemen gebruikt u de `sudo` opdracht voordat u het bestand opent in de gewenste tekst editor. Open in Windows een tekst editor zoals Klad blok als beheerder en open vervolgens het bestand.

2. Zoek in het bestand config. yaml de sectie met de **rand Agent module specificatie** . De definitie van de IoT Edge-agent bevat een **env** -para meter waar u omgevings variabelen kunt toevoegen.

3. Verwijder de accolades die tijdelijke aanduidingen voor de env para meter zijn en voeg de nieuwe variabele toe aan een nieuwe regel. Houd er rekening mee dat inspringingen in YAML twee spaties bevatten.

   ```yaml
   https_proxy: "<proxy URL>"
   ```

4. De IoT Edge runtime maakt standaard gebruik van AMQP om te praten met IoT Hub. Sommige proxy servers blok keren AMQP-poorten. Als dat het geval is, moet u ook edgeAgent configureren voor het gebruik van AMQP over websockets. Voeg een tweede omgevings variabele toe.

   ```yaml
   UpstreamProtocol: "AmqpWs"
   ```

   ![edgeAgent definitie met omgevings variabelen](./media/how-to-configure-proxy-support/edgeagent-edited.png)

5. Sla de wijzigingen op in config. yaml en sluit de editor. Start IoT Edge opnieuw om de wijzigingen van kracht te laten worden.

   * Linux:

      ```bash
      sudo systemctl restart iotedge
      ```

   * Windows:

      ```powershell
      Restart-Service iotedge
      ```

:::moniker-end
<!-- end 1.1 -->

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"

1. Open het configuratie bestand op uw IoT Edge-apparaat: `/etc/aziot/config.toml` . Het configuratie bestand is beveiligd, dus u hebt beheerders bevoegdheden nodig om het te openen. Op Linux-systemen gebruikt u de `sudo` opdracht voordat u het bestand opent in de gewenste tekst editor.

2. Zoek in het configuratie bestand de `[agent]` sectie, die alle configuratie-informatie bevat voor de edgeAgent-module die moet worden gebruikt bij het opstarten. De definitie van de IoT Edge-agent bevat een `[agent.env]` Subsectie waarin u omgevings variabelen kunt toevoegen.

3. Voeg de para meter **https_proxy** toe aan de sectie omgevings variabelen en stel uw proxy-URL in als waarde.

   ```toml
   [agent.env]
   # "RuntimeLogLevel" = "debug"
   # "UpstreamProtocol" = "AmqpWs"
   "https_proxy" = "<proxy URL>"
   ```

4. De IoT Edge runtime maakt standaard gebruik van AMQP om te praten met IoT Hub. Sommige proxy servers blok keren AMQP-poorten. Als dat het geval is, moet u ook edgeAgent configureren voor het gebruik van AMQP over websockets. Verwijder de opmerking over de `UpstreamProtocol` para meter.

   ```toml
   [agent.env]
   # "RuntimeLogLevel" = "debug"
   "UpstreamProtocol" = "AmqpWs"
   "https_proxy" = "<proxy URL>"
   ```

5. Sla de wijzigingen op en sluit de editor. Pas uw meest recente wijzigingen toe.

   ```bash
   sudo iotedge config apply
   ```

:::moniker-end
<!-- end 1.2 -->

## <a name="configure-deployment-manifests"></a>Implementatie manifesten configureren  

Zodra uw IoT Edge apparaat is geconfigureerd voor gebruik met uw proxy server, moet u de omgevings variabele HTTPS_PROXY in toekomstige implementatie manifesten blijven declareren. U kunt implementatie manifesten bewerken met behulp van de wizard Azure Portal of door een JSON-bestand van het implementatie manifest te bewerken.

De twee runtime modules, edgeAgent en edgeHub, altijd configureren om te communiceren via de proxy server zodat ze een verbinding met IoT Hub kunnen onderhouden. Als u de proxy gegevens uit de edgeAgent-module verwijdert, is de enige manier om de verbinding te herstellen door het configuratie bestand op het apparaat te bewerken, zoals beschreven in de vorige sectie.

Naast de edgeAgent-en edgeHub-modules, hebben andere modules mogelijk de proxy configuratie nodig. Voor modules die toegang nodig hebben tot Azure-resources naast IoT Hub, zoals Blob Storage, moet de HTTPS_PROXY variabele zijn opgegeven in het manifest bestand van de implementatie.

De volgende procedure is van toepassing gedurende de levens duur van het IoT Edge apparaat.

### <a name="azure-portal"></a>Azure Portal

Wanneer u de wizard **module instellen** gebruikt om implementaties voor IOT edge apparaten te maken, heeft elke module een **omgevings variabelen** sectie waarin u proxyserver verbindingen kunt configureren.

Als u de IoT Edge agent en IoT Edge hub-modules wilt configureren, selecteert u **runtime-instellingen** in de eerste stap van de wizard.

![Geavanceerde runtime-instellingen voor Edge configureren](./media/how-to-configure-proxy-support/configure-runtime.png)

Voeg de **https_proxy** -omgevings variabele toe aan de definities van de IOT Edge agent en IOT Edge hub-module. Als u de omgevings variabele **UpstreamProtocol** in het configuratie bestand op uw IOT edge-apparaat hebt opgenomen, moet u die ook toevoegen aan de module definitie van de IOT Edge agent.

![Https_proxy omgevings variabele instellen](./media/how-to-configure-proxy-support/edgehub-environmentvar.png)

Alle andere modules die u aan een implementatie manifest toevoegt, volgen hetzelfde patroon.

### <a name="json-deployment-manifest-files"></a>Manifest bestanden voor JSON-implementaties

Als u implementaties maakt voor IoT Edge apparaten met behulp van de sjablonen in Visual Studio code of door hand matig JSON-bestanden te maken, kunt u de omgevings variabelen rechtstreeks aan elke module definitie toevoegen.

Gebruik de volgende JSON-indeling:

```json
"env": {
    "https_proxy": {
        "value": "<proxy URL>"
    }
}
```

Als de omgevings variabelen zijn opgenomen, moet de module definitie eruitzien zoals in het volgende edgeHub-voor beeld:

```json
"edgeHub": {
    "type": "docker",
    "settings": {
        "image": "mcr.microsoft.com/azureiotedge-hub:1.1",
        "createOptions": ""
    },
    "env": {
        "https_proxy": {
            "value": "http://proxy.example.com:3128"
        }
    },
    "status": "running",
    "restartPolicy": "always"
}
```

Als u de omgevings variabele **UpstreamProtocol** in het bestand config. yaml op uw IOT edge-apparaat hebt opgenomen, moet u die ook toevoegen aan de module definitie van de IOT Edge agent.

```json
"env": {
    "https_proxy": {
        "value": "<proxy URL>"
    },
    "UpstreamProtocol": {
        "value": "AmqpWs"
    }
}
```

## <a name="working-with-traffic-inspecting-proxies"></a>Werken met verkeer-proxy's inspecteren

Als de proxy die u probeert te gebruiken, verkeer inspectie uitvoert op met TLS beveiligde verbindingen, is het belang rijk te weten dat verificatie met X. 509-certificaten niet werkt. IoT Edge een TLS-kanaal met de versleutelde end-to-end-verbinding tot stand brengen met het certificaat en de sleutel. Als dat kanaal is verbroken voor verkeers inspectie, kan de proxy het kanaal niet opnieuw instellen met de juiste referenties en IoT Hub en de IoT Hub Device Provisioning Service een `Unauthorized` fout retour neren.

Als u een proxy wilt gebruiken die verkeer inspectie uitvoert, moet u verificatie van de Shared Access-hand tekening gebruiken of IoT Hub hebben en de IoT Hub Device Provisioning Service is toegevoegd aan een lijst met toegestane apparaten om inspectie te voor komen.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over de rollen van de [IOT Edge runtime](iot-edge-runtime.md).

Problemen met installatie-en configuratie fouten oplossen met [veelvoorkomende problemen en oplossingen voor Azure IOT Edge](troubleshoot.md)
