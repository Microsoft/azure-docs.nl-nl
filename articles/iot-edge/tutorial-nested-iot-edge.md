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
ms.openlocfilehash: bfecc88dc0c504cee615f1a3d35f9208aeb724f8
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107309186"
---
# <a name="tutorial-create-a-hierarchy-of-iot-edge-devices"></a>Zelf studie: een hiërarchie van IoT Edge apparaten maken

[!INCLUDE [iot-edge-version-202011](../../includes/iot-edge-version-202011.md)]

Implementeer Azure IoT Edge-knooppunten in netwerken die zijn georganiseerd in hiërarchische lagen. Elke laag in een hiërarchie is een gateway-apparaat dat berichten en aanvragen verwerkt van apparaten in de onderliggende laag.

U kunt een hiërarchie van apparaten zo structureren dat alleen de bovenste laag verbinding heeft met de cloud en de onderliggende lagen alleen kunnen communiceren met aangrenzende noord- en zuidlagen. Deze vorming van netwerklagen vormt de basis van de meeste industriële netwerken, die de [ISA-95-standaard](https://en.wikipedia.org/wiki/ANSI/ISA-95) volgen.

Het doel van deze zelf studie is het maken van een hiërarchie van IoT Edge apparaten die een vereenvoudigde productie omgeving simuleert. Aan het einde implementeert u de [gesimuleerde temperatuursensormodule](https://azuremarketplace.microsoft.com/marketplace/apps/azure-iot.simulated-temperature-sensor) op een apparaat met een lagere laag zonder internettoegang door containerinstallatiekopieën te downloaden via de hiërarchie.

Om dit doel te bereiken, leidt deze zelfstudie u door het maken van een hiërarchie van IoT Edge-apparaten, het implementeren van IoT Edge-runtime-containers op uw apparaten en het lokaal configureren van uw apparaten. In deze zelf studie gebruikt u een geautomatiseerd configuratie programma voor het volgende:

> [!div class="checklist"]
>
> * De relaties in een hiërarchie van IoT Edge-apparaten maken en definiëren.
> * De IoT Edge-runtime op de apparaten in uw hiërarchie configureren.
> * Consistente certificaten in de hiërarchie van uw apparaat installeren.
> * Workloads toevoegen aan de apparaten in uw hiërarchie.
> * Gebruik de [proxy module IOT Edge API](https://azuremarketplace.microsoft.com/marketplace/apps/azure-iot.azureiotedge-api-proxy?tab=Overview) om http-verkeer veilig te routeren via één poort van uw lagere laag apparaten.

>[!TIP]
>Deze zelf studie bevat een mengeling van hand matige en geautomatiseerde stappen om een etalage van geneste IoT Edge functies te bieden.
>
>Als u een volledig geautomatiseerd uiterlijk wilt geven bij het instellen van een hiërarchie van IoT Edge apparaten, kunt u de gescripteerde [Azure IOT Edge voor industrieel IOT-voor beeld](https://aka.ms/iotedge-nested-sample)volgen. Met dit script scenario worden virtuele machines van Azure geïmplementeerd als vooraf geconfigureerde apparaten voor het simuleren van een fabrieks omgeving.
>
>Als u een uitgebreid overzicht wilt zien van de hand matige stappen voor het maken en beheren van een hiërarchie van IoT Edge apparaten, raadpleegt u [de hand leiding voor IOT Edge gateway-hiërarchieën](how-to-connect-downstream-iot-edge-device.md).

In deze zelfstudie worden de volgende netwerklagen gedefinieerd:

* **Bovenste laag**: IoT Edge-apparaten van deze laag kunnen rechtstreeks verbinding maken met de cloud.

* **Lagere lagen**: IOT edge apparaten in lagen onder de bovenste laag kunnen niet rechtstreeks verbinding maken met de Cloud. Ze moeten via een of meer tussenliggende IoT Edge-apparaten gaan om gegevens te verzenden en te ontvangen.

In deze zelf studie wordt een hiërarchie van twee apparaten gebruikt voor eenvoud. Hieronder ziet u een afbeelding. Eén apparaat, het **apparaat van de bovenste laag**, vertegenwoordigt een apparaat in de bovenste laag van de hiërarchie, waarmee rechtstreeks verbinding kan worden gemaakt met de Cloud. Dit apparaat wordt ook wel het **bovenliggende apparaat** genoemd. Het andere apparaat, het **apparaat met een lagere laag**, vertegenwoordigt een apparaat in de laag van de hiërarchie, waarmee geen rechtstreekse verbinding kan worden gemaakt met de Cloud. U kunt zo nodig meer apparaten met een lagere laag toevoegen om uw productie omgeving weer te geven. Apparaten op lagere lagen worden ook wel **onderliggende apparaten** genoemd.

![Structuur van de zelfstudie hiërarchie met twee apparaten: het bovenste laag apparaat en het onderste laag apparaat](./media/tutorial-nested-iot-edge/tutorial-hierarchy-diagram.png)

## <a name="prerequisites"></a>Vereisten

Als u een hiërarchie van IoT Edge-apparaten wilt maken, hebt u het volgende nodig:

* Een computer (Windows of Linux) met internetverbinding.
* Een Azure-account met een geldig abonnement. Als u geen [Azure-abonnement](../guides/developer/azure-developer-guide.md#understanding-accounts-subscriptions-and-billing) hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.
* Een gratis of standaard [IoT-hub](../iot-hub/iot-hub-create-through-portal.md)-laag in Azure.
* Een bash-shell in Azure Cloud Shell met behulp van Azure CLI v 2.3.1 met de Azure IoT-extensie v 0.10.6 of hoger geïnstalleerd. Deze zelfstudie maakt gebruik van de [Azure Cloud Shell](../cloud-shell/overview.md). Als u niet bekend bent met de Azure Cloud Shell kunt u [een Snelstartgids bekijken voor meer informatie](./quickstart-linux.md#prerequisites).
  * Voer [AZ version](/cli/azure/reference-index?#az_version)uit om uw huidige versies van de Azure cli-modules en-extensies weer te geven.
* Een Linux-apparaat dat moet worden geconfigureerd als een IoT Edge apparaat voor elk apparaat in uw hiërarchie. In deze zelf studie wordt gebruikgemaakt van twee apparaten. Als u geen apparaten beschikbaar hebt, kunt u voor elk apparaat in uw hiërarchie virtuele Azure-machines maken met behulp van de onderstaande opdracht.

   Vervang de tijdelijke aanduiding voor tekst in de volgende opdracht en voer deze twee keer uit en eenmaal voor elke virtuele machine. Elke virtuele machine moet beschikken over een uniek DNS-voor voegsel dat ook als naam wordt gebruikt. Het DNS-voor voegsel moet voldoen aan de volgende reguliere expressie: `[a-z][a-z0-9-]{1,61}[a-z0-9]` .

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

   De virtuele machine maakt gebruik van SSH-sleutels voor de verificatie van gebruikers. Als u niet bekend bent met het maken en gebruiken van SSH-sleutels, kunt u [de instructies voor het combi natie van open bare sleutels van SSH voor Linux-vm's in azure](https://docs.microsoft.com/azure/virtual-machines/linux/mac-create-ssh-keys)volgen.

   IoT Edge versie 1,2 is vooraf geïnstalleerd met deze ARM-sjabloon, waardoor de nood zaak om de assets hand matig te installeren op de virtuele machines, wordt opgeslagen. Als u IoT Edge op uw eigen apparaten installeert, raadpleegt u [Azure IOT Edge voor Linux (versie 1,2) installeren](how-to-install-iot-edge.md) of [IOT Edge bijwerken naar versie 1,2](how-to-update-iot-edge.md#special-case-update-from-10-or-11-to-12).

   Als u een virtuele machine met deze ARM-sjabloon hebt gemaakt, worden de ingangen van de virtuele machine `SSH` en de FQDN-naam (Fully Qualified Domain name `FQDN` ) uitgevoerd. U gebruikt de SSH-ingang en de FQDN of het IP-adres van elke virtuele machine voor configuratie in latere stappen. Houd deze informatie bij. Hieronder ziet u een voor beeld van de uitvoer.

   >[!TIP]
   >U kunt ook het IP-adres en de FQDN-namen op de Azure Portal vinden. Voor het IP-adres navigeert u naar de lijst met virtuele machines en noteert u het **veld openbaar IP-adres**. Voor de FQDN gaat u naar de overzichts pagina van elke virtuele machine en zoekt u naar het veld **DNS-naam** .

   ![Tijdens het maken van de virtuele machine wordt een JSON uitgevoerd, die de SSH-ingang bevat](./media/tutorial-nested-iot-edge/virtual-machine-outputs.png)

* Zorg ervoor dat de volgende poorten zijn geopend voor inkomend verkeer voor alle apparaten behalve het laagste laag apparaat: 8000, 443, 5671, 8883:
  * 8000: Wordt gebruikt voor het ophalen van Docker-containerinstallatie kopieën via de API-proxy.
  * 443: Wordt gebruikt tussen bovenliggende en onderliggende Edge-hubs voor REST API-aanroepen.
  * 5671, 8883: Wordt gebruikt voor AMQP en MQTT.

  Zie [poorten openen voor een virtuele machine met de Azure Portal](../virtual-machines/windows/nsg-quickstart-portal.md)voor meer informatie.

## <a name="configure-your-iot-edge-device-hierarchy"></a>Uw IoT Edge-apparaathiërarchie configureren

### <a name="create-a-hierarchy-of-iot-edge-devices"></a>Een hiërarchie van IoT Edge-apparaten maken

IoT Edge apparaten vormen de lagen van uw hiërarchie. In deze zelf studie maakt u een hiërarchie van twee IoT Edge apparaten: het **bovenste laag apparaat** en het onderliggende apparaat, de **lagere laag**. U kunt indien nodig extra onderliggende apparaten maken.

Als u uw-hiërarchie van IoT Edge apparaten wilt maken en configureren, gebruikt u het `iotedge-config` hulp programma. Dit hulp programma vereenvoudigt de configuratie van de hiërarchie door het automatiseren en verkorten van verschillende stappen in twee:

1. Instellen van de configuratie van de Cloud en de voor bereiding van elke apparaatconfiguratie, waaronder:

   * Apparaten maken in uw IoT Hub
   * De relaties tussen bovenliggende en onderliggende items instellen om communicatie tussen apparaten te machtigen
   * Genereren van een keten van certificaten voor elk apparaat om beveiligde communicatie tussen de apparaten tot stand te brengen
   * Configuratie bestanden voor elk apparaat genereren

1. Het installeren van elke apparaatconfiguratie, waaronder:

   * Certificaten installeren op elk apparaat
   * De configuratie bestanden voor elk apparaat Toep assen

Het `iotedge-config` hulp programma maakt ook de module-implementaties automatisch op uw IOT edge apparaat.

Als u het `iotedge-config` hulp programma voor het maken en configureren van uw hiërarchie wilt gebruiken, volgt u de onderstaande stappen in de Azure cli:

1. Maak in de [Azure Cloud shell](https://shell.azure.com/)een map voor de resources van uw zelf studie:

   ```bash
   mkdir nestedIotEdgeTutorial
   ```

1. De release van het configuratie hulpprogramma en configuratie sjablonen downloaden:

   ```bash
   cd ~/nestedIotEdgeTutorial
   wget -O iotedge_config.tar "https://github.com/Azure-Samples/iotedge_config_cli/releases/download/latest/iotedge_config_cli.tar.gz"
   tar -xvf iotedge_config.tar
   ```

   Hiermee maakt u de `iotedge_config_cli_release` map in uw zelf studie Directory.

   Het sjabloon bestand dat wordt gebruikt voor het maken van de hiërarchie van het apparaat is het `iotedge_config.yaml` bestand in `~/nestedIotEdgeTutorial/iotedge_config_cli_release/templates/tutorial` . In dezelfde map `deploymentLowerLayer.json` bevindt zich een JSON-implementatie bestand met instructies voor de modules die moeten worden geïmplementeerd op het apparaat met een **lagere laag**. Het `deploymentTopLayer.json` bestand is hetzelfde, maar voor uw apparaat voor de **hoogste laag**, omdat de modules die op elk apparaat zijn geïmplementeerd, niet hetzelfde zijn. Het `device_config.toml` bestand is een sjabloon voor IOT Edge configuraties van apparaten en wordt gebruikt om automatisch de configuratie bundels voor de apparaten in uw hiërarchie te genereren.

   Als u de bron code en scripts voor het `iotedge-config` hulp programma wilt bekijken, raadpleegt u [de Azure-Samples opslagplaats op github](https://github.com/Azure-Samples/iotedge_config_cli).

1. Open de sjabloon zelf studie configuratie en bewerk deze met uw gegevens:

   ```bash
   code ~/nestedIotEdgeTutorial/iotedge_config_cli_release/templates/tutorial/iotedge_config.yaml
   ```

   Vul in de sectie **iothub** de `iothub_hostname` velden en in `iothub_name` met uw gegevens. Deze informatie vindt u op de pagina overzicht van uw IoT Hub op de Azure Portal.

   In het gedeelte optionele **certificaten** kunt u de velden met de absolute paden naar uw certificaat en sleutel vullen. Als u deze velden leeg laat, genereert het script automatisch zelfondertekende test certificaten voor uw gebruik. Als u niet bekend bent met het gebruik van certificaten in een gateway scenario, raadpleegt u [de sectie met het certificaat van de hand leiding](how-to-connect-downstream-iot-edge-device.md#prepare-certificates).

   In de sectie **configuratie** is het `template_config_path` het pad naar de `device_config.toml` sjabloon die wordt gebruikt voor het maken van de configuraties van uw apparaten. Het `default_edge_agent` veld bepaalt welke installatie kopie van de rand agent apparaten ondergaat en van waar.

   In de **edgedevices** sectie voor een productie scenario kunt u de hiërarchie structuur bewerken om de gewenste structuur weer te geven. Voor de doel einden van deze zelf studie accepteert u de standaard structuur. Voor elk apparaat is er een `device_id` veld waarin u uw apparaten kunt benoemen. Het veld bevat ook het `deployment` pad naar de JSON van de implementatie voor dat apparaat.

   U kunt IoT Edge apparaten ook hand matig registreren in uw IoT Hub via de Azure Portal of het Azure Cloud Shell. Zie [de hand leiding voor het registreren van een IOT edge apparaat](how-to-register-device.md)voor meer informatie.

   U kunt ook de relaties tussen bovenliggende en onderliggende items hand matig definiëren. Zie de sectie [een gateway-hiërarchie maken](how-to-connect-downstream-iot-edge-device.md#create-a-gateway-hierarchy) in de hand leiding voor meer informatie.

   ![In het gedeelte edgedevices van het configuratie bestand kunt u uw hiërarchie definiëren](./media/tutorial-nested-iot-edge/hierarchy-config-sample.png)

1. Sla het bestand op en sluit het:

   `CTRL + S`, `CTRL + Q`

1. Een uitvoer Directory maken voor de configuratie bundels in de map met resources voor zelf studie:

   ```bash
   mkdir ~/nestedIotEdgeTutorial/iotedge_config_cli_release/outputs
   ```

1. Ga naar de `iotedge_config_cli_release` map en voer het hulp programma uit om uw hiërarchie van IOT edge apparaten te maken:

   ```bash
   cd ~/nestedIotEdgeTutorial/iotedge_config_cli_release
   ./iotedge_config --config ~/nestedIotEdgeTutorial/iotedge_config_cli_release/templates/tutorial/iotedge_config.yaml --output ~/nestedIotEdgeTutorial/iotedge_config_cli_release/outputs -f
   ```

   Met de `--output` vlag maakt het hulp programma de certificaten, de certificaat bundels en een logboek bestand in een map van uw keuze. Als de `-f` vlag is ingesteld, zoekt het hulp programma automatisch naar bestaande IOT edge-apparaten in uw IOT hub en verwijdert u deze, om fouten te voor komen en uw hub schoon te laten blijven.

   Het configuratie hulpprogramma maakt uw IoT Edge-apparaten en stelt de relaties tussen bovenliggende en onderliggende items in. U kunt er ook voor kiezen om certificaten te maken die uw apparaten gebruiken. Als er paden naar implementatie-JSONes worden opgegeven, maakt het hulp programma automatisch deze implementaties op uw apparaten, maar dit is niet vereist. Ten slotte genereert het hulp programma de configuratie bundels voor uw apparaten en plaatst ze in de uitvoermap. Zie het logboek bestand in de uitvoermap voor een uitgebreid overzicht van de stappen die door het configuratie hulpprogramma worden uitgevoerd.

   ![Tijdens de uitvoering wordt een topologie van uw hiërarchie weer gegeven door het script](./media/tutorial-nested-iot-edge/successful-setup-tool-run.png)

Controleer of de topologie-uitvoer van het script correct lijkt. Zodra u tevreden bent over de juiste structuur van uw hiërarchie, kunt u door gaan.

### <a name="configure-the-iot-edge-runtime"></a>De IoT Edge-runtime configureren.

Naast het inrichten van uw apparaten, wordt met de configuratie stappen een vertrouwde communicatie tussen de apparaten in uw hiërarchie tot stand gebracht met behulp van de certificaten die u eerder hebt gemaakt. De stappen worden ook gestart om de netwerk structuur van uw hiërarchie te bepalen. Het apparaat van de bovenste laag houdt Internet connectiviteit in stand, zodat het installatie kopieën kan ophalen voor de runtime van de Cloud, terwijl apparaten met een lagere laag worden doorgestuurd via het apparaat van de bovenste laag voor toegang tot deze installatie kopieën.

Als u de IoT Edge runtime wilt configureren, moet u de configuratie bundels die zijn gemaakt door het installatie script, Toep assen op uw apparaten. De configuraties enigszins verschillen tussen het **apparaat van de bovenste laag** en een apparaat met een **lagere laag**, dus mindful het configuratie bestand van het apparaat dat u op elk apparaat toepast.

1. Elk apparaat heeft de bijbehorende configuratie bundel nodig. U kunt een USB-station of een [beveiligde bestands kopie](https://www.ssh.com/ssh/scp/) gebruiken om de configuratie bundels naar elk apparaat te verplaatsen.

   Zorg ervoor dat u de juiste configuratie bundel naar elk apparaat verzendt.

   ```bash
   scp <PATH_TO_CONFIGURATION_BUNDLE> <USER>@<VM_IP_OR_FQDN>:~
   ```

   Het `:~` betekent dat de configuratiemap op de virtuele machine wordt geplaatst in de basismap.

1. Meld u aan bij de virtuele machine om de configuratie bundel toe te passen op het apparaat:

   ```bash
   ssh <USER>@<VM_IP_OR_FQDN>
   ```

1. Pak de configuratie bundel uit op elk apparaat. U moet eerst zip installeren:

   ```bash
   sudo apt install zip
   unzip ~/<PATH_TO_CONFIGURATION_BUNDLE>/<CONFIGURATION_BUNDLE>.zip
   ```

1. Op elk apparaat past u de configuratie bundel toe op het apparaat:

   ```bash
   sudo ./install.sh
   ```

   Op het **bovenste laag apparaat** wordt u gevraagd om de hostnaam in te voeren. Op het **lagere laag apparaat** wordt u gevraagd om de hostnaam en de hostnaam van het bovenliggende item. Geef voor elke prompt de juiste IP-adres of FQDN op. U kunt ofwel gebruiken, maar dit is consistent in uw keuze tussen apparaten. De uitvoer van het installatie script vindt u hieronder.

   Als u meer wilt weten over de wijzigingen die worden aangebracht in het configuratie bestand van uw apparaat, raadpleegt u [de sectie IOT Edge op apparaten configureren van de hand leiding](how-to-connect-downstream-iot-edge-device.md#configure-iot-edge-on-devices).

  ![Als u de configuratie bundels installeert, worden de config. toml-bestanden op uw apparaat bijgewerkt en worden alle IoT Edge services automatisch opnieuw gestart](./media/tutorial-nested-iot-edge/configuration-install-output.png)

Als u de bovenstaande stappen correct hebt voltooid, kunt u controleren of uw apparaten correct zijn geconfigureerd.

Voer de configuratie-en connectiviteits controles op uw apparaten uit. Voor het **bovenste laag apparaat**:

   ```bash
   sudo iotedge check
   ```

Voor het **lagere laag apparaat** moet de diagnostische installatie kopie hand matig worden door gegeven in de opdracht:

   ```bash
   sudo iotedge check --diagnostics-image-name <parent_device_fqdn_or_ip>:8000/azureiotedge-diagnostics:1.2
   ```

Op het **apparaat op de bovenste laag** verwacht u dat er een uitvoer wordt weer gegeven met verschillende evaluaties. Mogelijk ziet u enkele waarschuwingen over beleids regels voor logboeken en, afhankelijk van uw netwerk, DNS-beleid.

<!-- Add pic after GA -->
<!-- KEEP! A sample output of the `iotedge check` is shown below: -->

<!-- KEEP! ![Sample configuration and connectivity results](./media/tutorial-nested-iot-edge/configuration-and-connectivity-check-results.png) -->

Zodra u tevreden bent over de configuraties op elk apparaat, bent u klaar om door te gaan.

## <a name="deploy-modules-to-your-devices"></a>Modules implementeren op uw apparaten

De module-implementaties voor uw apparaten zijn automatisch gegenereerd toen de apparaten werden gemaakt. Het `iotedge-config-cli` hulp programma implementeert implementatie-jsonen voor de **apparaten boven en laag,** nadat deze zijn gemaakt. De module-implementatie is in behandeling tijdens het configureren van de IoT Edge runtime op elk apparaat. Zodra u de runtime hebt geconfigureerd, is de implementaties naar het **bovenste laag apparaat** begonnen. Nadat deze implementaties zijn voltooid, kan het apparaat met een **lagere laag** het **IOT Edge API-proxy** module gebruiken om de vereiste installatie kopieën te halen.

In de [Azure Cloud shell](https://shell.azure.com/)kunt u de JSON **van de toplaag van het apparaat** bekijken om te begrijpen welke modules op uw apparaat zijn geïmplementeerd:

   ```bash
   cat ~/nestedIotEdgeTutorial/iotedge_config_cli_release/templates/tutorial/deploymentTopLayer.json
   ```

Naast de runtime modules **IOT Edge agent** en **IOT Edge hub** ontvangt het apparaat van de **bovenste laag** de **docker-register** module en de **IOT Edge API-proxy** module.

De **docker-REGI ster** -module verwijst naar een bestaande Azure container Registry. In dit geval `REGISTRY_PROXY_REMOTEURL` verwijst naar de micro soft-container Registry. In de `createOptions` kunt u zien dat het communiceert op poort 5000.

De **proxy module IOT Edge API** stuurt HTTP-aanvragen naar andere modules, waardoor lagere laag apparaten container installatie kopieën of push-blobs naar opslag kunnen halen. In deze zelf studie communiceert de IT op poort 8000 en is geconfigureerd om docker-container installatie kopie pull-aanvragen te verzenden naar de **docker-register** module op poort 5000. Daarnaast worden alle Blob Storage-upload aanvragen gerouteerd naar module AzureBlobStorageonIoTEdge op poort 11002. Zie de [hand leiding](how-to-configure-api-proxy-module.md)voor de module voor meer informatie over de **proxy module IOT Edge API** en hoe u deze kunt configureren.

Als u wilt weten hoe u een implementatie kunt maken, zoals in de Azure Portal of Azure Cloud Shell, raadpleegt u het [gedeelte apparaat in de bovenste laag van de hand leiding](how-to-connect-downstream-iot-edge-device.md#deploy-modules-to-top-layer-devices).

In de [Azure Cloud shell](https://shell.azure.com/)kunt u de JSON van de implementatie **van het lagere laag-apparaat** bekijken om te begrijpen welke modules op uw apparaat zijn geïmplementeerd:

   ```bash
   cat ~/nestedIotEdgeTutorial/iotedge_config_cli_release/templates/tutorial/deploymentLowerLayer.json
   ```

U kunt zien `systemModules` dat de runtime modules **van het lagere laag-apparaat** zijn ingesteld op ophalen uit `$upstream:8000` , in plaats van `mcr.microsoft.com` , omdat het apparaat op de **bovenste laag** is. Het **apparaat met een lagere laag** verzendt docker-installatie kopie vraagt de **proxy module IOT Edge API** op poort 8000, omdat de installatie kopieën niet rechtstreeks vanuit de Cloud kunnen worden opgehaald. De andere module die is geïmplementeerd op het **lagere laag apparaat**, de **gesimuleerde temperatuur sensor** module, neemt ook de afbeeldings aanvraag door aan `$upstream:8000` .

Als u wilt weten hoe u een implementatie met behulp van de Azure Portal of Azure Cloud Shell kunt maken, raadpleegt u het [gedeelte van het onderste laag apparaat in de hand leiding](how-to-connect-downstream-iot-edge-device.md#deploy-modules-to-lower-layer-devices).

U kunt de status van uw modules weer geven met behulp van de opdracht:

   ```bash
   az iot hub module-twin show --device-id <edge_device_id> --module-id '$edgeAgent' --hub-name <iot_hub_name> --query "properties.reported.[systemModules, modules]"
   ```

   Met deze opdracht worden alle edgeAgent-gerapporteerde eigenschappen uitgevoerd. Hier volgen enkele nuttige informatie voor het bewaken van de status van het apparaat: *runtime status*, *Start tijd van runtime*, *laatste afsluit tijd* van runtime, *aantal keer opnieuw starten van runtime*.

U kunt ook de status van uw modules op het [Azure Portal](https://ms.portal.azure.com/)bekijken. Navigeer naar het gedeelte **IOT Edge** van uw IOT hub om uw apparaten en modules weer te geven.

Zodra u tevreden bent met uw module-implementaties, bent u klaar om door te gaan.

## <a name="view-generated-data"></a>Gegenereerde gegevens weergeven

De **gesimuleerde temperatuursensormodule** die u hebt gepusht, genereert samples van omgevingsgegevens. De module verzendt berichten met informatie over omgevingstemperatuur, luchtvochtigheid, machinetemperatuur en druk, evenals een tijdstempel.

U kunt deze berichten ook bekijken via de [Azure Cloud Shell](https://shell.azure.com/):

   ```azurecli-interactive
   az iot hub monitor-events -n <iothub_name> -d <lower-layer-device-name>
   ```

## <a name="troubleshooting"></a>Problemen oplossen

Voer de `iotedge check` opdracht uit om de configuratie te controleren en fouten op te lossen.

U kunt uitvoeren `iotedge check` in een geneste hiërarchie, zelfs als de onderliggende computers geen directe toegang tot internet hebben.

Wanneer u `iotedge check` vanuit de lagere laag uitvoert, probeert het programma de installatie kopie vanuit het bovenliggende item te halen via poort 443.

In deze zelf studie gebruiken we poort 8000, dus we moeten deze opgeven:

```bash
sudo iotedge check --diagnostics-image-name $upstream:8000/azureiotedge-diagnostics:1.2.0-rc4
```

De `azureiotedge-diagnostics`-waarde wordt opgehaald uit het containerregister dat aan de registermodule is gekoppeld. In deze zelfstudie is deze waarde standaard ingesteld op https://mcr.microsoft.com:

| Naam | Waarde |
| - | - |
| `REGISTRY_PROXY_REMOTEURL` | `https://mcr.microsoft.com` |

Als u een persoonlijk container register gebruikt, moet u ervoor zorgen dat alle installatie kopieën (IoTEdgeAPIProxy, edgeAgent, edgeHub, gesimuleerde temperatuur sensor en diagnose) aanwezig zijn in het container register.

## <a name="clean-up-resources"></a>Resources opschonen

U kunt de lokale configuraties en Azure-resources die u in dit artikel hebt gemaakt, verwijderen om kosten te voorkomen.

Om de resources te verwijderen:

1. Meld u aan bij [Azure Portal](https://portal.azure.com) en selecteer **Resourcegroepen**.

2. Selecteer de naam van de resourcegroep die uw IoT Edge-testresources bevat. 

3. Bekijk de lijst met resources die zich bevinden in de resourcegroep. Als u alle mappen wilt verwijderen, kunt u **Resourcegroep verwijderen** selecteren. Als u slechts een deel ervan wilt verwijderen, kunt u in elke resource afzonderlijk klikken om ze te verwijderen. 

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u twee IoT Edge-apparaten geconfigureerd als gateways, en het ene ingesteld als bovenliggend apparaat van het andere. Vervolgens hebt u een container installatie kopie met behulp van de proxy module IoT Edge API via een gateway op het onderliggende apparaat gedemonstreerd. Raadpleeg [de hand leiding voor het gebruik van de proxy module](how-to-configure-api-proxy-module.md) als u meer wilt weten.

Voor meer informatie over het gebruik van gateways om hiërarchische lagen van IoT Edge apparaten te maken, raadpleegt u [de hand leiding voor het verbinden van downstream IOT edge-apparaten](how-to-connect-downstream-iot-edge-device.md).

Ga verder met de andere zelfstudies om te zien hoe u met Azure IoT Edge meer oplossingen voor uw bedrijf kunt maken.

> [!div class="nextstepaction"]
> [Een Azure Machine Learning-model implementeren als een module](tutorial-deploy-machine-learning.md)
