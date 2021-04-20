---
title: 'Zelfstudie: een hiërarchie van IoT Edge-apparaten maken - Azure IoT Edge'
description: In deze zelfstudie ziet u hoe u een hiërarchische structuur van IoT Edge-apparaten met behulp van gateways maakt.
author: v-tcassi
manager: philmea
ms.author: v-tcassi
ms.date: 2/26/2021
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
monikerRange: '>=iotedge-2020-11'
ms.openlocfilehash: 44fe6bb3787e1fe0df7ccf83200497b46c473568
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107728496"
---
# <a name="tutorial-create-a-hierarchy-of-iot-edge-devices"></a>Zelfstudie: Een hiërarchie van IoT Edge maken

[!INCLUDE [iot-edge-version-202011](../../includes/iot-edge-version-202011.md)]

Implementeer Azure IoT Edge-knooppunten in netwerken die zijn georganiseerd in hiërarchische lagen. Elke laag in een hiërarchie is een gateway-apparaat dat berichten en aanvragen verwerkt van apparaten in de onderliggende laag.

U kunt een hiërarchie van apparaten zo structureren dat alleen de bovenste laag verbinding heeft met de cloud en de onderliggende lagen alleen kunnen communiceren met aangrenzende noord- en zuidlagen. Deze vorming van netwerklagen vormt de basis van de meeste industriële netwerken, die de [ISA-95-standaard](https://en.wikipedia.org/wiki/ANSI/ISA-95) volgen.

Het doel van deze zelfstudie is het maken van een hiërarchie van IoT Edge apparaten die een vereenvoudigde productieomgeving simuleren. Aan het einde implementeert u de [gesimuleerde temperatuursensormodule](https://azuremarketplace.microsoft.com/marketplace/apps/azure-iot.simulated-temperature-sensor) op een apparaat met een lagere laag zonder internettoegang door containerinstallatiekopieën te downloaden via de hiërarchie.

Om dit doel te bereiken, leidt deze zelfstudie u door het maken van een hiërarchie van IoT Edge-apparaten, het implementeren van IoT Edge-runtime-containers op uw apparaten en het lokaal configureren van uw apparaten. In deze zelfstudie gebruikt u een geautomatiseerd configuratiehulpprogramma voor het volgende:

> [!div class="checklist"]
>
> * De relaties in een hiërarchie van IoT Edge-apparaten maken en definiëren.
> * De IoT Edge-runtime op de apparaten in uw hiërarchie configureren.
> * Consistente certificaten in de hiërarchie van uw apparaat installeren.
> * Workloads toevoegen aan de apparaten in uw hiërarchie.
> * Gebruik de [IoT Edge API Proxy-module](https://azuremarketplace.microsoft.com/marketplace/apps/azure-iot.azureiotedge-api-proxy?tab=Overview) om HTTP-verkeer veilig via één poort te sturen vanaf uw apparaten met een lagere laag.

>[!TIP]
>Deze zelfstudie bevat een combinatie van handmatige en geautomatiseerde stappen om een presentatie te geven van geneste IoT Edge functies.
>
>Als u een volledig geautomatiseerd overzicht wilt van het instellen van een hiërarchie van IoT Edge-apparaten, kunt u de gescripte Azure IoT Edge voor industriële [IoT-voorbeeld volgen.](https://aka.ms/iotedge-nested-sample) In dit scriptscenario worden virtuele Azure-machines geïmplementeerd als vooraf geconfigureerde apparaten om een fabrieksomgeving te simuleren.
>
>Als u de handmatige stappen voor het maken en beheren van een hiërarchie van IoT Edge-apparaten uitgebreid wilt bekijken, bekijkt u de handleiding voor [IoT Edge-apparaatgatewayhiërarchieën.](how-to-connect-downstream-iot-edge-device.md)

In deze zelfstudie worden de volgende netwerklagen gedefinieerd:

* **Bovenste laag**: IoT Edge-apparaten van deze laag kunnen rechtstreeks verbinding maken met de cloud.

* **Lagere lagen:** IoT Edge apparaten in lagen onder de bovenste laag kunnen niet rechtstreeks verbinding maken met de cloud. Ze moeten via een of meer tussenliggende IoT Edge-apparaten gaan om gegevens te verzenden en te ontvangen.

In deze zelfstudie wordt voor het gemak gebruikgemaakt van een twee apparaathiërarchie, zoals hieronder wordt weergegeven. Eén apparaat, het **bovenste laagapparaat,** vertegenwoordigt een apparaat in de bovenste laag van de hiërarchie, dat rechtstreeks verbinding kan maken met de cloud. Dit apparaat wordt ook wel het **bovenliggende apparaat** genoemd. Het andere apparaat, het **apparaat met** de lagere laag, vertegenwoordigt een apparaat op de onderste laag van de hiërarchie, dat niet rechtstreeks verbinding kan maken met de cloud. U kunt meer apparaten met een lagere laag toevoegen om uw productieomgeving naar behoefte weer te geven. Apparaten op lagere lagen worden ook wel **onderliggende apparaten genoemd.**

![Structuur van de hiërarchie van de zelfstudie, met twee apparaten: het apparaat in de bovenste laag en het apparaat met de onderste laag](./media/tutorial-nested-iot-edge/tutorial-hierarchy-diagram.png)

## <a name="prerequisites"></a>Vereisten

Als u een hiërarchie van IoT Edge-apparaten wilt maken, hebt u het volgende nodig:

* Een computer (Windows of Linux) met internetverbinding.
* Een Azure-account met een geldig abonnement. Als u geen [Azure-abonnement](../guides/developer/azure-developer-guide.md#understanding-accounts-subscriptions-and-billing) hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.
* Een gratis of standaard [IoT-hub](../iot-hub/iot-hub-create-through-portal.md)-laag in Azure.
* Een Bash-shell in Azure Cloud Shell Azure CLI v2.3.1 met de Azure IoT-extensie v0.10.6 of hoger geïnstalleerd. Deze zelfstudie maakt gebruik van de [Azure Cloud Shell](../cloud-shell/overview.md). Als u niet bekend bent met de Azure Cloud Shell kunt u [een Snelstartgids bekijken voor meer informatie](./quickstart-linux.md#prerequisites).
  * Voer az version uit om uw huidige versies van de Azure CLI-modules en -extensies [te bekijken.](/cli/azure/reference-index?#az_version)
* Een Linux-apparaat om te configureren als IoT Edge apparaat voor elk apparaat in uw hiërarchie. In deze zelfstudie worden twee apparaten gebruikt. Als u geen apparaten beschikbaar hebt, kunt u voor elk apparaat in uw hiërarchie virtuele Azure-machines maken met behulp van de onderstaande opdracht.

   Vervang de tekst van de tijdelijke aanduiding in de volgende opdracht en voer deze twee keer uit, één keer voor elke virtuele machine. Elke virtuele machine heeft een uniek DNS-voorvoegsel nodig, dat ook als naam dient. Het DNS-voorvoegsel moet voldoen aan de volgende reguliere expressie: `[a-z][a-z0-9-]{1,61}[a-z0-9]` .

   ```bash
   az deployment group create \
    --resource-group <REPLACE_WITH_YOUR_RESOURCE_GROUP> \
    --template-uri "https://raw.githubusercontent.com/Azure/iotedge-vm-deploy/1.2.0/edgeDeploy.json" \
    --parameters dnsLabelPrefix='<REPLACE_WITH_UNIQUE_DNS_FOR_VIRTUAL_MACHINE>' \
    --parameters adminUsername='azureuser' \
    --parameters authenticationType='sshPublicKey' \
    --parameters adminPasswordOrKey="$(< ~/.ssh/id_rsa.pub)" \
    --query "properties.outputs.[publicFQDN.value, publicSSH.value]" -o tsv
   ```

   De virtuele machine maakt gebruik van SSH-sleutels voor het authenticeren van gebruikers. Als u niet bekend bent met het maken en gebruiken van SSH-sleutels, kunt u de instructies voor [openbare-persoonlijke SSH-sleutelparen voor Linux-VM's in Azure volgen.](https://docs.microsoft.com/azure/virtual-machines/linux/mac-create-ssh-keys)

   IoT Edge versie 1.2 vooraf is geïnstalleerd met deze ARM-sjabloon, hoeft u de assets niet handmatig op de virtuele machines te installeren. Zie Install [Azure IoT Edge for Linux (versie 1.2)](how-to-install-iot-edge.md) of Update IoT Edge to version 1.2 (IoT Edge naar versie [1.2)](how-to-update-iot-edge.md#special-case-update-from-10-or-11-to-12)als u een IoT Edge installeert op uw eigen apparaten. IoT Edge

   Als u een virtuele machine hebt gemaakt met behulp van deze ARM-sjabloon, worden de greep van uw virtuele machine en de volledig gekwalificeerde domeinnaam `SSH` () `FQDN` uitgevoerd. U gebruikt de SSH-handle en de FQDN of het IP-adres van elke virtuele machine voor configuratie in latere stappen, dus houd deze informatie bij. Hieronder ziet u een voorbeelduitvoer.

   >[!TIP]
   >U vindt het IP-adres en de FQDN ook op de Azure Portal. Navigeer voor het IP-adres naar uw lijst met virtuele machines en noteer het **veld Openbaar IP-adres.** Ga voor de FQDN naar de overzichtspagina van elke virtuele machine en zoek het **veld DNS-naam.**

   ![De virtuele machine geeft een JSON weer bij het maken, die de SSH-handle bevat](./media/tutorial-nested-iot-edge/virtual-machine-outputs.png)

* Zorg ervoor dat de volgende poorten zijn geopend voor alle apparaten, met uitzondering van het apparaat met de laagste laag: 8000, 443, 5671, 8883:
  * 8000: Wordt gebruikt voor het ophalen van Docker-containerinstallatie kopieën via de API-proxy.
  * 443: Wordt gebruikt tussen bovenliggende en onderliggende Edge-hubs voor REST API-aanroepen.
  * 5671, 8883: Wordt gebruikt voor AMQP en MQTT.

  Zie Poorten openen voor een virtuele machine met de Azure Portal voor [meer Azure Portal.](../virtual-machines/windows/nsg-quickstart-portal.md)

## <a name="configure-your-iot-edge-device-hierarchy"></a>Uw IoT Edge-apparaathiërarchie configureren

### <a name="create-a-hierarchy-of-iot-edge-devices"></a>Een hiërarchie van IoT Edge-apparaten maken

IoT Edge apparaten zijn de lagen van uw hiërarchie. In deze zelfstudie maakt u een hiërarchie van twee IoT Edge apparaten: het **apparaat** in de bovenste laag en het onderliggende apparaat, het **apparaat met de lagere laag.** U kunt naar behoefte extra onderliggende apparaten maken.

Als u uw hiërarchie van IoT Edge apparaten wilt maken en configureren, gebruikt u het `iotedge-config` hulpprogramma . Dit hulpprogramma vereenvoudigt de configuratie van de hiërarchie door verschillende stappen te automatiseren en samen te zetten in twee:

1. Het instellen van de cloudconfiguratie en het voorbereiden van elke apparaatconfiguratie, waaronder:

   * Apparaten maken in uw IoT Hub
   * De bovenliggende/onderliggende relaties zo instellen dat communicatie tussen apparaten wordt geautoriseerd
   * Het genereren van een keten van certificaten voor elk apparaat om beveiligde communicatie tussen deze apparaten tot stand te brengen
   * Configuratiebestanden genereren voor elk apparaat

1. Het installeren van elke apparaatconfiguratie, waaronder:

   * Certificaten installeren op elk apparaat
   * De configuratiebestanden voor elk apparaat toepassen

Het `iotedge-config` hulpprogramma maakt de module-implementaties ook automatisch IoT Edge uw apparaat.

Als u het hulpprogramma wilt gebruiken om uw hiërarchie te maken en te `iotedge-config` configureren, volgt u de onderstaande stappen in de Azure CLI:

1. Maak in [Azure Cloud Shell](https://shell.azure.com/)een map voor de resources van uw zelfstudie:

   ```bash
   mkdir nestedIotEdgeTutorial
   ```

1. Download de release van het configuratieprogramma en de configuratiesjablonen:

   ```bash
   cd ~/nestedIotEdgeTutorial
   wget -O iotedge_config.tar "https://github.com/Azure-Samples/iotedge_config_cli/releases/download/latest/iotedge_config_cli.tar.gz"
   tar -xvf iotedge_config.tar
   ```

   Hiermee maakt u de map `iotedge_config_cli_release` in uw zelfstudiemap.

   Het sjabloonbestand dat wordt gebruikt om uw apparaathiërarchie te maken, is `iotedge_config.yaml` het bestand dat is gevonden in `~/nestedIotEdgeTutorial/iotedge_config_cli_release/templates/tutorial` . In dezelfde map is een JSON-implementatiebestand met instructies voor `deploymentLowerLayer.json` welke modules moeten worden geïmplementeerd op uw apparaat met een lagere **laag.** Het `deploymentTopLayer.json` bestand is hetzelfde, maar voor uw bovenste **laag-apparaat**, omdat de modules die op elk apparaat zijn geïmplementeerd, niet hetzelfde zijn. Het bestand is een sjabloon voor IoT Edge-apparaatconfiguraties en wordt gebruikt om automatisch de configuratiebundels voor de apparaten in uw `device_config.toml` hiërarchie te genereren.

   Als u de broncode en scripts voor het hulpprogramma wilt bekijken, raadpleegt u de `iotedge-config` [Azure-Samples-opslagplaats op GitHub.](https://github.com/Azure-Samples/iotedge_config_cli)

1. Open de configuratiesjabloon voor de zelfstudie en bewerk deze met uw gegevens:

   ```bash
   code ~/nestedIotEdgeTutorial/iotedge_config_cli_release/templates/tutorial/iotedge_config.yaml
   ```

   Vul in **de sectie iothub** de velden `iothub_hostname` en in met uw `iothub_name` gegevens. Deze informatie vindt u op de overzichtspagina van uw IoT Hub op Azure Portal.

   In de **sectie Optionele certificaten** kunt u de velden vullen met de absolute paden naar uw certificaat en sleutel. Als u deze velden leeg laat, genereert het script automatisch zelf-ondertekende testcertificaten voor uw gebruik. Als u niet bekend bent met de manier waarop certificaten worden gebruikt in een gatewayscenario, raadpleegt u de sectie certificaat [van de](how-to-connect-downstream-iot-edge-device.md#prepare-certificates)handleiding.

   In de **configuratiesectie** is het pad naar de sjabloon die wordt gebruikt `template_config_path` om uw `device_config.toml` apparaatconfiguraties te maken. Het veld bepaalt welke apparaten met een lagere laag van de `default_edge_agent` Edge Agent-afbeelding worden pullt en van waar naar toe.

   In de **sectie edgedevices** kunt u voor een productiescenario de hiërarchiestructuur bewerken om de gewenste structuur weer te geven. Voor deze zelfstudie accepteert u de standaardstructuur. Voor elk apparaat is er een `device_id` veld waarin u uw apparaten een naam kunt geven. Er is ook het `deployment` veld , waarmee het pad naar de implementatie-JSON voor dat apparaat wordt opgegeven.

   U kunt de apparaten in uw IoT Edge ook handmatig registreren IoT Hub de Azure Portal of Azure Cloud Shell. Zie de handleiding over het registreren van een IoT Edge [apparaat voor meer informatie.](how-to-register-device.md)

   U kunt de bovenliggende en onderliggende relaties ook handmatig definiëren. Zie de [sectie Een gatewayhiërarchie](how-to-connect-downstream-iot-edge-device.md#create-a-gateway-hierarchy) maken van de handleiding voor meer informatie.

   ![In de sectie edgedevices van het configuratiebestand kunt u uw hiërarchie definiëren](./media/tutorial-nested-iot-edge/hierarchy-config-sample.png)

1. Sla het bestand op en sluit het:

   `CTRL + S`, `CTRL + Q`

1. Maak een map outputs voor de configuratiebundels in de map resources van uw zelfstudie:

   ```bash
   mkdir ~/nestedIotEdgeTutorial/iotedge_config_cli_release/outputs
   ```

1. Navigeer naar `iotedge_config_cli_release` uw map en voer het hulpprogramma uit om uw hiërarchie van IoT Edge maken:

   ```bash
   cd ~/nestedIotEdgeTutorial/iotedge_config_cli_release
   ./iotedge_config --config ~/nestedIotEdgeTutorial/iotedge_config_cli_release/templates/tutorial/iotedge_config.yaml --output ~/nestedIotEdgeTutorial/iotedge_config_cli_release/outputs -f
   ```

   Met de `--output` vlag maakt het hulpprogramma de apparaatcertificaten, certificaatbundels en een logboekbestand in een map naar keuze. Als de vlag is ingesteld, wordt met het hulpprogramma automatisch naar bestaande IoT Edge-apparaten in uw IoT Hub en verwijderd, om fouten te voorkomen en uw hub schoon `-f` te houden.

   Het configuratiehulpprogramma maakt uw IoT Edge apparaten en stelt de relaties tussen boven- en onderliggende apparaten in. Optioneel kunt u certificaten maken die door uw apparaten kunnen worden gebruikt. Als paden naar JSON's voor implementatie worden opgegeven, maakt het hulpprogramma deze implementaties automatisch op uw apparaten, maar dit is niet vereist. Ten slotte genereert het hulpprogramma de configuratiebundels voor uw apparaten en zet deze in de uitvoermap. Zie het logboekbestand in de uitvoermap voor een uitgebreid overzicht van de stappen die door het configuratiehulpprogramma worden uitgevoerd.

   ![Het script geeft een topologie van uw hiërarchie weer bij uitvoering](./media/tutorial-nested-iot-edge/successful-setup-tool-run.png)

Controleer of de topologie-uitvoer van het script er correct uitziet. Zodra u tevreden bent met de juiste structuur van uw hiërarchie, bent u klaar om door te gaan.

### <a name="configure-the-iot-edge-runtime"></a>De IoT Edge-runtime configureren.

Naast het inrichten van uw apparaten brengen de configuratiestappen vertrouwde communicatie tussen de apparaten in uw hiërarchie tot stand met behulp van de certificaten die u eerder hebt gemaakt. Met de stappen wordt ook de netwerkstructuur van uw hiërarchie tot stand brengen. Het apparaat in de bovenste laag behoudt de internetverbinding, zodat het afbeeldingen voor de runtime van de cloud kan halen, terwijl apparaten met een lagere laag via het bovenste laag-apparaat worden gerouteerd om toegang te krijgen tot deze afbeeldingen.

Als u de IoT Edge runtime wilt configureren, moet u de configuratiebundels die door het installatiescript zijn gemaakt, toepassen op uw apparaten. De configuraties verschillen enigszins tussen het apparaat in de **bovenste** laag en een apparaat met een lagere **laag.** Let dus goed op het configuratiebestand van het apparaat dat u op elk apparaat wilt toepassen.

1. Elk apparaat heeft de bijbehorende configuratiebundel nodig. U kunt een USB-station of beveiligde [bestandskopie gebruiken om](https://www.ssh.com/ssh/scp/) de configuratiebundels naar elk apparaat te verplaatsen.

   Zorg ervoor dat u de juiste configuratiebundel naar elk apparaat verzendt.

   ```bash
   scp <PATH_TO_CONFIGURATION_BUNDLE> <USER>@<VM_IP_OR_FQDN>:~
   ```

   Dit betekent dat de configuratiemap in de basismap op de `:~` virtuele machine wordt geplaatst.

1. Meld u aan bij uw virtuele machine om de configuratiebundel toe te passen op het apparaat:

   ```bash
   ssh <USER>@<VM_IP_OR_FQDN>
   ```

1. Op elk apparaat moet u de configuratiebundel uitsetsen. U moet eerst zip installeren:

   ```bash
   sudo apt install zip
   unzip ~/<PATH_TO_CONFIGURATION_BUNDLE>/<CONFIGURATION_BUNDLE>.zip
   ```

1. Pas op elk apparaat de configuratiebundel toe op het apparaat:

   ```bash
   sudo ./install.sh
   ```

   Op het **apparaat in de bovenste laag** ontvangt u een prompt om de hostnaam in te voeren. Op het **apparaat met de lagere laag** wordt om de hostnaam en de hostnaam van het bovenliggende apparaat gevraagd. Voor elke prompt moet u het juiste IP- of FQDN-adres gebruiken. U kunt beide gebruiken, maar consistent zijn in uw keuze op alle apparaten. De uitvoer van het installatiescript wordt hieronder weergegeven.

   Zie de sectie configure IoT Edge on devices (IoT Edge configureren op apparaten) van de handleiding voor meer informatie over de wijzigingen die worden aangebracht in het [configuratiebestand van uw apparaat.](how-to-connect-downstream-iot-edge-device.md#configure-iot-edge-on-devices)

  ![Als u de configuratiebundels installeert, worden de config.toml-bestanden op uw apparaat bijgewerkt en worden alle IoT Edge automatisch opnieuw opgestart](./media/tutorial-nested-iot-edge/configuration-install-output.png)

Als u de bovenstaande stappen correct hebt uitgevoerd, kunt u controleren of uw apparaten juist zijn geconfigureerd.

Voer de configuratie- en connectiviteitscontroles uit op uw apparaten. Voor het **apparaat in de bovenste laag:**

   ```bash
   sudo iotedge check
   ```

Voor het **apparaat met de lagere laag** moet de diagnostische afbeelding handmatig worden doorgegeven in de opdracht :

   ```bash
   sudo iotedge check --diagnostics-image-name <parent_device_fqdn_or_ip>:8000/azureiotedge-diagnostics:1.2
   ```

Op uw **apparaat in de bovenste laag** verwacht u uitvoer met verschillende door te geven evaluaties. Mogelijk ziet u enkele waarschuwingen over logboekbeleid en, afhankelijk van uw netwerk, DNS-beleid.

<!-- Add pic after GA -->
<!-- KEEP! A sample output of the `iotedge check` is shown below: -->

<!-- KEEP! ![Sample configuration and connectivity results](./media/tutorial-nested-iot-edge/configuration-and-connectivity-check-results.png) -->

Zodra u tevreden bent met de juiste configuraties op elk apparaat, bent u klaar om door te gaan.

## <a name="deploy-modules-to-your-devices"></a>Modules implementeren op uw apparaten

De module-implementaties op uw apparaten werden automatisch gegenereerd toen de apparaten werden gemaakt. Het `iotedge-config-cli` hulpprogramma heeft implementatie-JSON's ingevoerd voor de apparaten in de **bovenste en onderste laag** nadat ze zijn gemaakt. De module-implementatie was in behandeling tijdens het configureren van de IoT Edge runtime op elk apparaat. Nadat u de runtime hebt geconfigureerd, zijn de implementaties naar het **apparaat in de bovenste laag** gestart. Nadat deze implementaties zijn voltooid, kan **het apparaat met de** lagere laag de module IoT Edge API **Proxy** gebruiken om de benodigde afbeeldingen op te halen.

In de [Azure Cloud Shell](https://shell.azure.com/)kunt u de implementatie-JSON van het bovenste laagapparaat bekijken om te begrijpen welke modules op uw apparaat zijn geïmplementeerd: 

   ```bash
   cat ~/nestedIotEdgeTutorial/iotedge_config_cli_release/templates/tutorial/deploymentTopLayer.json
   ```

Naast de runtimemodules **IoT Edge Agent** en **IoT Edge Hub,** ontvangt het bovenste laagapparaat de **Docker-registermodule** en **IoT Edge API Proxy-module.** 

De **Docker-registermodule** wijst naar een bestaande Azure Container Registry. In dit geval `REGISTRY_PROXY_REMOTEURL` wijst naar de Microsoft Container Registry. In de `createOptions` ziet u dat deze communiceert op poort 5000.

De **IoT Edge API Proxy-module** routeer HTTP-aanvragen naar andere modules, zodat apparaten met een lagere laag containerafbeeldingen kunnen pullen of blobs naar de opslag kunnen pushen. In deze zelfstudie communiceert het op poort 8000 en is het geconfigureerd voor het verzenden van de route voor pull-aanvragen voor Docker-container-aanvragen naar uw **Docker-registermodule** op poort 5000. Bovendien worden aanvragen voor het uploaden van blob-opslag doorgeleid naar de module AzureBlobStorageonIoTEdge op poort 11002. Zie de handleiding van de module voor meer IoT Edge **api-proxymodule** en hoe u deze [kunt configureren.](how-to-configure-api-proxy-module.md)

Als u wilt zien hoe u een implementatie als deze kunt maken via de Azure Portal [of](how-to-connect-downstream-iot-edge-device.md#deploy-modules-to-top-layer-devices)Azure Cloud Shell, bekijkt u de sectie Apparaat in de bovenste laag van de handleiding.

In de [Azure Cloud Shell](https://shell.azure.com/)kunt u de JSON  van de implementatie van het apparaat in de lagere laag bekijken om te begrijpen welke modules op uw apparaat zijn geïmplementeerd:

   ```bash
   cat ~/nestedIotEdgeTutorial/iotedge_config_cli_release/templates/tutorial/deploymentLowerLayer.json
   ```

U kunt zien dat de runtimemodules van het apparaat met de lagere laag zijn ingesteld op pull van , in plaats van , zoals het apparaat op `systemModules` de bovenste laag  `$upstream:8000` `mcr.microsoft.com` **deed.** Het **apparaat met de** lagere laag verzendt Docker-afbeeldingsaanvragen naar de IoT Edge API **Proxy-module** op poort 8000, omdat deze de afbeeldingen niet rechtstreeks uit de cloud kan halen. De andere module die is geïmplementeerd op het apparaat met **een** lagere laag, de module **Gesimuleerde** temperatuursensor, doet ook de aanvraag voor de afbeelding naar `$upstream:8000` .

Als u wilt zien hoe u een implementatie als deze maakt via de Azure Portal of Azure Cloud Shell, bekijkt u de sectie Apparaat met lagere laag van de handleiding [.](how-to-connect-downstream-iot-edge-device.md#deploy-modules-to-lower-layer-devices)

U kunt de status van uw modules weergeven met behulp van de opdracht :

   ```bash
   az iot hub module-twin show --device-id <edge_device_id> --module-id '$edgeAgent' --hub-name <iot_hub_name> --query "properties.reported.[systemModules, modules]"
   ```

   Met deze opdracht worden alle gerapporteerde eigenschappen edgeAgent uitgevoerd. Hier volgen enkele nuttige informatie voor het bewaken van de status van het apparaat: *runtimestatus,* *runtime-begintijd,* laatste tijd van *runtime,* aantal opnieuw *opstarten van runtime.*

U kunt ook de status van uw modules bekijken op de [Azure Portal.](https://ms.portal.azure.com/) Navigeer **naar IoT Edge** sectie van uw IoT Hub om uw apparaten en modules te bekijken.

Zodra u tevreden bent met uw module-implementaties, bent u klaar om door te gaan.

## <a name="view-generated-data"></a>Gegenereerde gegevens weergeven

De **gesimuleerde temperatuursensormodule** die u hebt gepusht, genereert samples van omgevingsgegevens. De module verzendt berichten met informatie over omgevingstemperatuur, luchtvochtigheid, machinetemperatuur en druk, evenals een tijdstempel.

U kunt deze berichten ook bekijken via de [Azure Cloud Shell](https://shell.azure.com/):

   ```azurecli-interactive
   az iot hub monitor-events -n <iothub_name> -d <lower-layer-device-name>
   ```

## <a name="troubleshooting"></a>Problemen oplossen

Voer de opdracht `iotedge check` uit om de configuratie te controleren en fouten op te lossen.

U kunt uitvoeren `iotedge check` in een geneste hiërarchie, zelfs als de onderliggende machines geen directe internettoegang hebben.

Wanneer u vanaf de onderste laag wordt uitgevoerd, probeert het programma de afbeelding van het bovenliggende via `iotedge check` poort 443 op te halen.

In deze zelfstudie gebruiken we poort 8000, dus moeten we deze opgeven:

```bash
sudo iotedge check --diagnostics-image-name $upstream:8000/azureiotedge-diagnostics:1.2
```

De `azureiotedge-diagnostics`-waarde wordt opgehaald uit het containerregister dat aan de registermodule is gekoppeld. In deze zelfstudie is deze waarde standaard ingesteld op https://mcr.microsoft.com:

| Naam | Waarde |
| - | - |
| `REGISTRY_PROXY_REMOTEURL` | `https://mcr.microsoft.com` |

Als u een privécontainerregister gebruikt, moet u ervoor zorgen dat alle afbeeldingen (IoTEdgeAPIProxy, edgeAgent, edgeHub, Simulated Temperature Sensor en diagnostics) aanwezig zijn in het containerregister.

## <a name="clean-up-resources"></a>Resources opschonen

U kunt de lokale configuraties en Azure-resources die u in dit artikel hebt gemaakt, verwijderen om kosten te voorkomen.

Om de resources te verwijderen:

1. Meld u aan bij [Azure Portal](https://portal.azure.com) en selecteer **Resourcegroepen**.

2. Selecteer de naam van de resourcegroep die uw IoT Edge-testresources bevat. 

3. Bekijk de lijst met resources die zich bevinden in de resourcegroep. Als u alle mappen wilt verwijderen, kunt u **Resourcegroep verwijderen** selecteren. Als u slechts een deel ervan wilt verwijderen, kunt u in elke resource afzonderlijk klikken om ze te verwijderen. 

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u twee IoT Edge-apparaten geconfigureerd als gateways, en het ene ingesteld als bovenliggend apparaat van het andere. Vervolgens hebt u gedemonstreerd hoe u een containerafbeelding naar het onderliggende apparaat haalt via een gateway met behulp van IoT Edge API Proxy-module. Zie [de handleiding over het gebruik van de proxymodule](how-to-configure-api-proxy-module.md) als u meer wilt weten.

Voor meer informatie over het gebruik van gateways voor het maken van hiërarchische lagen van IoT Edge-apparaten, zie de handleiding voor het verbinden van [downstreamapparaten IoT Edge downstreamapparaten.](how-to-connect-downstream-iot-edge-device.md)

Ga verder met de andere zelfstudies om te zien hoe u met Azure IoT Edge meer oplossingen voor uw bedrijf kunt maken.

> [!div class="nextstepaction"]
> [Een Azure Machine Learning-model implementeren als een module](tutorial-deploy-machine-learning.md)
