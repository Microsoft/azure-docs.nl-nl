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
ms.openlocfilehash: 3f997b577fb473e30fbafec08c4e68547a641fa3
ms.sourcegitcommit: 5f32f03eeb892bf0d023b23bd709e642d1812696
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/12/2021
ms.locfileid: "103200103"
---
# <a name="tutorial-create-a-hierarchy-of-iot-edge-devices-preview"></a>Zelfstudie: Een hiërarchie van IoT Edge-apparaten maken (preview)

Implementeer Azure IoT Edge-knooppunten in netwerken die zijn georganiseerd in hiërarchische lagen. Elke laag in een hiërarchie is een gateway-apparaat dat berichten en aanvragen verwerkt van apparaten in de onderliggende laag.

>[!NOTE]
>Voor deze functie is IoT Edge versie 1.2, in openbare preview, met Linux-containers vereist.

U kunt een hiërarchie van apparaten zo structureren dat alleen de bovenste laag verbinding heeft met de cloud en de onderliggende lagen alleen kunnen communiceren met aangrenzende noord- en zuidlagen. Deze vorming van netwerklagen vormt de basis van de meeste industriële netwerken, die de [ISA-95-standaard](https://en.wikipedia.org/wiki/ANSI/ISA-95) volgen.

Het doel van deze zelfstudie is het maken van een hiërarchie van IoT Edge-apparaten die een productieomgeving simuleert. Aan het einde implementeert u de [gesimuleerde temperatuursensormodule](https://azuremarketplace.microsoft.com/marketplace/apps/azure-iot.simulated-temperature-sensor) op een apparaat met een lagere laag zonder internettoegang door containerinstallatiekopieën te downloaden via de hiërarchie.

Om dit doel te bereiken, leidt deze zelfstudie u door het maken van een hiërarchie van IoT Edge-apparaten, het implementeren van IoT Edge-runtime-containers op uw apparaten en het lokaal configureren van uw apparaten. In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
>
> * De relaties in een hiërarchie van IoT Edge-apparaten maken en definiëren.
> * De IoT Edge-runtime op de apparaten in uw hiërarchie configureren.
> * Consistente certificaten in de hiërarchie van uw apparaat installeren.
> * Workloads toevoegen aan de apparaten in uw hiërarchie.
> * Gebruik de [proxy module IOT Edge API](https://azuremarketplace.microsoft.com/marketplace/apps/azure-iot.azureiotedge-api-proxy?tab=Overview) om http-verkeer veilig te routeren via één poort van uw lagere laag apparaten.

In deze zelfstudie worden de volgende netwerklagen gedefinieerd:

* **Bovenste laag**: IoT Edge-apparaten van deze laag kunnen rechtstreeks verbinding maken met de cloud.

* **Lagere lagen**: IOT edge apparaten in lagen onder de bovenste laag kunnen niet rechtstreeks verbinding maken met de Cloud. Ze moeten via een of meer tussenliggende IoT Edge-apparaten gaan om gegevens te verzenden en te ontvangen.

In deze zelf studie wordt een hiërarchie van twee apparaten gebruikt voor eenvoud. Hieronder ziet u een afbeelding. Eén apparaat, het **apparaat van de bovenste laag**, vertegenwoordigt een apparaat in de bovenste laag van de hiërarchie, waarmee rechtstreeks verbinding kan worden gemaakt met de Cloud. Dit apparaat wordt ook wel het **bovenliggende apparaat** genoemd. Het andere apparaat, het **apparaat met een lagere laag**, vertegenwoordigt een apparaat in de laag van de hiërarchie, waarmee geen rechtstreekse verbinding kan worden gemaakt met de Cloud. U kunt zo nodig extra apparaten met een lagere laag toevoegen om uw productie omgeving weer te geven. Apparaten op lagere lagen worden ook wel **onderliggende apparaten** genoemd. De configuratie van alle extra lagere laag apparaten wordt gevolgd door de configuratie van het **lagere laag apparaat**.

![Structuur van de zelfstudie hiërarchie met twee apparaten: het bovenste laag apparaat en het onderste laag apparaat](./media/tutorial-nested-iot-edge/tutorial-hierarchy-diagram.png)

## <a name="prerequisites"></a>Vereisten

Als u een hiërarchie van IoT Edge-apparaten wilt maken, hebt u het volgende nodig:

* Een computer (Windows of Linux) met internetverbinding.
* Een Azure-account met een geldig abonnement. Als u geen [Azure-abonnement](../guides/developer/azure-developer-guide.md#understanding-accounts-subscriptions-and-billing) hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.
* Een gratis of standaard [IoT-hub](../iot-hub/iot-hub-create-through-portal.md)-laag in Azure.
* Azure CLI v2.3.1 met de Azure IoT-uitbreiding v0.10.6 of hoger geïnstalleerd. Deze zelfstudie maakt gebruik van de [Azure Cloud Shell](../cloud-shell/overview.md). Als u niet bekend bent met de Azure Cloud Shell kunt u [een Snelstartgids bekijken voor meer informatie](./quickstart-linux.md#prerequisites).
  * Voer [AZ version](/cli/azure/reference-index?#az_version)uit om uw huidige versies van de Azure cli-modules en-extensies weer te geven.
* Een Linux-apparaat dat moet worden geconfigureerd als een IoT Edge apparaat voor elk apparaat in uw hiërarchie. In deze zelf studie wordt gebruikgemaakt van twee apparaten. Als u geen apparaten beschikbaar hebt, kunt u virtuele Azure-machines maken voor elk apparaat in uw hiërarchie door de tekst van de tijdelijke aanduiding te vervangen door de volgende opdracht en deze uit te voeren:

   ```azurecli-interactive
   az vm create \
    --resource-group <REPLACE_WITH_RESOURCE_GROUP> \
    --name <REPLACE_WITH_UNIQUE_NAMES_FOR_EACH_VM> \
    --image UbuntuLTS \
    --admin-username azureuser \
    --admin-password <REPLACE_WITH_PASSWORD>
   ```

* Zorg ervoor dat de volgende poorten geopend zijn voor inkomend verkeer: 8000, 443, 5671, 8883:
  * 8000: Wordt gebruikt voor het ophalen van Docker-containerinstallatie kopieën via de API-proxy.
  * 443: Wordt gebruikt tussen bovenliggende en onderliggende Edge-hubs voor REST API-aanroepen.
  * 5671, 8883: Wordt gebruikt voor AMQP en MQTT.

  Zie voor meer informatie [Het openen van poorten op een virtuele machine met Azure Portal](../virtual-machines/windows/nsg-quickstart-portal.md).

>[!TIP]
>Als u een geautomatiseerd uiterlijk wilt instellen om een hiërarchie van IoT Edge apparaten in te stellen, kunt u de gescripteerde [Azure IOT Edge voor industrieel IOT-voor beeld](https://aka.ms/iotedge-nested-sample)volgen. Met dit script scenario worden virtuele machines van Azure geïmplementeerd als vooraf geconfigureerde apparaten voor het simuleren van een fabrieks omgeving.
>
>Als u wilt door gaan met het maken van de voor beeld-hiërarchie stapsgewijs, gaat u verder met de stappen in de onderstaande zelf studie.

## <a name="configure-your-iot-edge-device-hierarchy"></a>Uw IoT Edge-apparaathiërarchie configureren

### <a name="create-a-hierarchy-of-iot-edge-devices"></a>Een hiërarchie van IoT Edge-apparaten maken

IoT Edge apparaten vormen de lagen van uw hiërarchie. In deze zelf studie maakt u een hiërarchie van twee IoT Edge apparaten: het **bovenste laag apparaat** en het onderliggende apparaat, de **lagere laag**. U kunt, indien nodig, extra onderliggende apparaten maken.

Als u uw IoT Edge-apparaten wilt maken, kunt u de Azure Portal of Azure CLI gebruiken.

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Ga in [Azure Portal](https://ms.portal.azure.com/) naar uw IoT Hub.

1. Selecteer in het menu in het linkerdeelvenster, onder **Automatisch apparaatbeheer**, de optie **IoT Edge**.

1. Selecteer **+ Een IoT Edge-apparaat toevoegen**. Dit apparaat wordt het apparaat van de bovenste laag, dus voer een geschikte unieke apparaat-id in. Selecteer **Opslaan**.

1. Selecteer nogmaals **+ Een IoT Edge-apparaat toevoegen**. Dit apparaat is een apparaat met een lagere laag. Geef dus een geschikte unieke apparaat-ID op.

1. Kies **Een bovenliggend apparaat instellen**, kies uw bovenste laag-apparaat in de lijst met apparaten en selecteer **Ok**. Selecteer **Opslaan**.

   ![Bovenliggend apparaat instellen voor apparaat van onderliggende laag](./media/tutorial-nested-iot-edge/set-parent-device.png)

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

1. Voer in [Azure Cloud Shell](https://shell.azure.com/) de volgende opdracht in om een IoT-​​apparaat in uw hub te maken. Dit apparaat wordt het apparaat van de bovenste laag, dus voer een geschikte unieke apparaat-id in:

   ```azurecli-interactive
   az iot hub device-identity create --device-id {top_layer_device_id} --edge-enabled --hub-name {hub_name}
   ```

   Bij het maken van een apparaat wordt de JSON-configuratie van het apparaat uitgevoerd.

   ![JSON-uitvoer van een geslaagde apparaat maken](./media/tutorial-nested-iot-edge/json-success-output.png)

1. Voer de volgende opdracht in om een lagere laag IoT Edge apparaat te maken. U kunt meer dan één apparaat met een lagere laag maken als u meer laag in uw hiërarchie wilt. Zorg ervoor dat u unieke apparaat-id's opgeeft.

   ```azurecli-interactive
   az iot hub device-identity create --device-id {lower_layer_device_id} --edge-enabled --hub-name {hub_name}
   ```

1. Voer de volgende opdracht in om elke bovenliggende/onderliggende relatie te definiëren tussen het apparaat van de **bovenste laag** en elk apparaat met een **lagere laag**. Zorg ervoor dat u deze opdracht uitvoert voor elk apparaat met een lagere laag in uw hiërarchie.

   ```azurecli-interactive
   az iot hub device-identity parent set --device-id {lower_layer_device_id} --parent-device-id {top_layer_device_id} --hub-name {hub_name}
   ```

   Deze opdracht heeft geen expliciete uitvoer.

---

Noteer vervolgens de connection string van elk IoT Edge apparaat. Deze worden later gebruikt.

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Ga in [Azure Portal](https://ms.portal.azure.com/)naar het gedeelte **IoT Edge** van uw IoT Hub.

1. Klik in de lijst met apparaten op de apparaat-id van een van de apparaten.

1. Selecteer **Kopiëren** in het veld **Primaire verbindingsreeks** en noteer deze op een door u gewenste plaats.

1. Herhaal dit voor alle andere apparaten.

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

1. Voer in de [Azure Cloud Shell](https://shell.azure.com/) voor elk apparaat de volgende opdracht in om de verbindingsreeks van uw apparaat op te halen en op een plaats van uw keuze op te nemen:

   ```azurecli-interactive
   az iot hub device-identity connection-string show --device-id {device_id} --hub-name {hub_name}
   ```

---

Als u de bovenstaande stappen correct hebt uitgevoerd, kunt u de volgende stappen gebruiken om te controleren of de relaties tussen de bovenliggende en onderliggende items juist zijn in de Azure Portal of Azure CLI.

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Ga in [Azure Portal](https://ms.portal.azure.com/)naar het gedeelte **IoT Edge** van uw IoT Hub.

1. Klik op de apparaat-ID van een **lager apparaat** in de lijst met apparaten.

1. Op de pagina Details van het apparaat wordt de identiteit van uw **hoogste laag apparaat** weer gegeven naast het veld **bovenliggend apparaat** .

   [Bovenliggend apparaat bevestigd door onderliggend apparaat](./media/tutorial-nested-iot-edge/lower-layer-device-parent.png)

U kunt ook onderliggend van uw hiërarchie relatie via uw apparaat voor de **bovenste laag**.

1. Klik op de apparaat-ID van een **bovenste laag apparaat** in de lijst met apparaten.

1. Selecteer boven aan het tabblad **onderliggende apparaten beheren** .

1. Onder de lijst met apparaten ziet u het apparaat met een **lagere laag**.

   [Onderliggend apparaat bevestigd door bovenliggend apparaat](./media/tutorial-nested-iot-edge/top-layer-device-child.png)

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

1. In de [Azure Cloud shell](https://shell.azure.com/) kunt u controleren of een van de onderliggende apparaten zijn relatie met het bovenliggende apparaat heeft gemaakt door de identiteit van het bevestigde bovenliggende apparaat van het onderliggende apparaat op te halen. Hiermee wordt de JSON-configuratie van het bovenliggende apparaat uitgevoerd. bovenstaande afbeelding:

   ```azurecli-interactive
   az iot hub device-identity parent show --device-id {lower_layer_device_id} --hub-name {hub_name}
   ```

U kunt ook onderliggend niveau van uw hiërarchie relatie via query's uitvoeren op het apparaat van de **bovenste laag**.

1. De onderliggende apparaten weer geven die het bovenliggende apparaat kent:

    ```azurecli-interactive
    az iot hub device-identity children list --device-id {top_layer_device_id} --hub-name {hub_name}
    ```

---

Zodra u tevreden bent over de juiste structuur van uw hiërarchie, kunt u door gaan.

### <a name="create-certificates"></a>Certificaten maken

Alle apparaten in een [gateway-scenario](iot-edge-as-gateway.md) hebben een gedeeld certificaat nodig om beveiligde verbindingen tussen deze apparaten in te stellen. Gebruik de volgende stappen om in dit scenario democertificaten voor beide apparaten te maken.

Als u democertificaten wilt maken op een Linux-apparaat, moet u de generatiescripts klonen en deze instellen om lokaal in bash te worden uitgevoerd.

1. Kloon de IoT Edge git-opslagplaats, die scripts bevat om democertificaten te genereren.

   ```bash
   git clone https://github.com/Azure/iotedge.git
   ```

1. Navigeer naar de map waarin u wilt werken. Alle certificaat- en sleutelbestanden worden in deze map gemaakt.

1. Kopieer de config- en scriptbestanden van de gekloonde IoT Edge-opslagplaats in uw werkmap.

   ```bash
   cp <path>/iotedge/tools/CACertificates/*.cnf .
   cp <path>/iotedge/tools/CACertificates/certGen.sh .
   ```

1. Maak het basis-CA-certificaat en één tussenliggend certificaat.

   ```bash
   ./certGen.sh create_root_and_intermediate
   ```

   Met deze scriptopdracht worden verschillende certificaat- en sleutelbestanden gemaakt, maar we gebruiken het volgende bestand als het **basis-CA-certificaat** voor de gatewayhiërarchie:

   * `<WRKDIR>/certs/azure-iot-test-only.root.ca.cert.pem`  

1. Maak twee sets CA-certificaten van IoT Edge-apparaten en privésleutels met de volgende opdracht: één set voor het apparaat van de bovenste laag en één set voor het apparaat van de onderliggende laag. Geef herkenbare namen op voor de CA-certificaten om ze van elkaar te onderscheiden.

   ```bash
   ./certGen.sh create_edge_device_ca_certificate "{top_layer_certificate_name}"
   ./certGen.sh create_edge_device_ca_certificate "{lower_layer_certificate_name}"
   ```

   Met deze script opdracht worden verschillende certificaat-en sleutel bestanden gemaakt, maar we gebruiken het volgende certificaat en sleutel paar op elk IoT Edge apparaat en waarnaar wordt verwezen in het configuratie bestand:

   * `<WRKDIR>/certs/iot-edge-device-<CA cert name>-full-chain.cert.pem`
   * `<WRKDIR>/private/iot-edge-device-<CA cert name>.key.pem`

Elk apparaat heeft een kopie van het basis-CA-certificaat en een kopie van zijn eigen CA-certificaat en persoonlijke sleutel nodig. U kunt een USB-station of [beveiligde bestandskopie](https://www.ssh.com/ssh/scp/) gebruiken om de certificaten naar elk apparaat te verplaatsen.

1. Nadat de certificaten zijn overgedragen, installeert u de basis-CA voor elk apparaat.

   ```bash
   sudo cp <path>/azure-iot-test-only.root.ca.cert.pem /usr/local/share/ca-certificates/azure-iot-test-only.root.ca.cert.pem.crt
   sudo update-ca-certificates
   ```

   Met deze opdracht moet worden uitgevoerd dat er één certificaat is toegevoegd in `/etc/ssl/certs` .

   [Bericht voor installatie van certificaat geslaagd](./media/tutorial-nested-iot-edge/certificates-success-output.png)

Als u de bovenstaande stappen correct hebt voltooid, kunt u controleren of uw certificaten zijn geïnstalleerd `/etc/ssl/certs` door naar de map te navigeren en te zoeken naar uw geïnstalleerde certificaten.

1. Ga naar `/etc/ssl/certs` :

   ```bash
   cd /etc/ssl/certs
   ```

1. Vermeld de geïnstalleerde certificaten en `grep` voor `azure` :

   ```bash
   ll | grep azure
   ```

   Er wordt een certificaat-hash weer geven die is gekoppeld aan het basis-CA-certificaat en het basis-CA-certificaat dat is gekoppeld aan de kopie in uw `usr/local/share` Directory.

   [Resultaten van zoeken naar Azure-certificaten](./media/tutorial-nested-iot-edge/certificate-list-results.png)

Zodra u hebt nagegaan of uw certificaten op elk apparaat zijn geïnstalleerd, bent u klaar om door te gaan.

### <a name="install-iot-edge-on-the-devices"></a>IoT Edge installeren op de apparaten

Als u de IoT Edge versie 1,2 runtime-installatie kopieën installeert, hebt u toegang tot de functies die nodig zijn om als een hiërarchie van apparaten te kunnen fungeren.

Als u IoT Edge wilt installeren, moet u de juiste opslagplaats configuratie installeren, vereiste onderdelen installeren en de benodigde release-assets installeren.

1. Installeer de configuratie van de opslagplaats die overeenkomt met het besturingssysteem van uw apparaat.

   * **Ubuntu Server 18.04**:

     ```bash
     curl https://packages.microsoft.com/config/ubuntu/18.04/multiarch/prod.list > ./microsoft-prod.list
     ```

   * **Raspberry Pi OS Stretch**:

     ```bash
     curl https://packages.microsoft.com/config/debian/stretch/multiarch/prod.list > ./microsoft-prod.list
     ```

1. Kopieer de gegenereerde lijst naar de map sources.list.d.

   ```bash
   sudo cp ./microsoft-prod.list /etc/apt/sources.list.d/
   ```

1. Installeer de openbare Microsoft GPG-sleutel.

   ```bash
   curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
   sudo cp ./microsoft.gpg /etc/apt/trusted.gpg.d/
   ```

1. Pakketlijsten bijwerken op uw apparaat.

   ```bash
   sudo apt-get update
   ```

1. Installeer de Moby-engine.

   ```bash
   sudo apt-get install moby-engine
   ```

1. Installeer IoT Edge en de IoT-identiteits service.

   ```bash
   sudo apt-get install aziot-edge
   ```

Als u de bovenstaande stappen correct hebt uitgevoerd, hebt u het banner bericht Azure IoT Edge u hebt gezien dat u het Azure IoT Edge configuratie bestand hebt bijgewerkt, `/etc/aziot/config.toml` op elk apparaat in uw hiërarchie. Als dat het geval is, kunt u door gaan.

### <a name="configure-the-iot-edge-runtime"></a>De IoT Edge-runtime configureren.

Naast het inrichten van uw apparaten, wordt met de configuratie stappen een vertrouwde communicatie tussen de apparaten in uw hiërarchie tot stand gebracht met behulp van de certificaten die u eerder hebt gemaakt. De stappen worden ook gestart om de netwerk structuur van uw hiërarchie te bepalen. Het apparaat van de bovenste laag houdt Internet connectiviteit in stand, zodat het installatie kopieën kan ophalen voor de runtime van de Cloud, terwijl apparaten met een lagere laag worden doorgestuurd via het apparaat van de bovenste laag voor toegang tot deze installatie kopieën.

Als u de IoT Edge runtime wilt configureren, moet u verschillende onderdelen van het configuratie bestand wijzigen. De configuraties zijn iets anders tussen het apparaat voor de **bovenste laag** en een apparaat met een **lagere laag**, dus mindful het configuratie bestand van het apparaat dat u voor elke stap bewerkt. Het configureren van de IoT Edge-runtime voor uw apparaten bestaat uit vier stappen, die allemaal worden uitgevoerd door het IoT Edge-configuratiebestand te bewerken:

* Richt elk apparaat handmatig in door de verbindingsreeks van dat apparaat toe te voegen aan het configuratiebestand.

* Werk de certificaten van uw apparaat bij door het configuratiebestand aan te wijzen op het CA-certificaat van het apparaat, de persoonlijke CA-sleutel van het apparaat en het basis-CA-certificaat.

* Bootstrap het systeem met behulp van de IoT Edge-agent.

* Werk de parameter **hostnaam** bij voor uw apparaat in de **bovenste laag** en werk zowel de parameter **hostnaam** als de parameter **parent_hostname** bij voor uw apparaten in de **onderliggende laag**.

Voltooi deze stappen en start de IoT Edge-service opnieuw om uw apparaten te configureren.

>[!TIP]
>Bij het navigeren in het configuratie bestand in nano kunt u met **CTRL + W** zoeken naar tref woorden in het bestand.

1. Op elk apparaat maakt u een configuratie bestand op basis van de opgenomen sjabloon.

   ```bash
   sudo cp /etc/aziot/config.toml.edge.template /etc/aziot/config.toml

1. On each device, open the IoT Edge configuration file.

   ```bash
   sudo nano /etc/aziot/config.toml
   ```

1. Zoek de sectie **hostname** op voor het apparaat van de **bovenste laag** . Verwijder de opmerking bij de regel met de `hostname` para meter en werk de waarde bij naar de Fully Qualified Domain Name (FQDN) of het IP-adres van het IOT edge apparaat. Gebruik de waarde die u consistent kiest op de apparaten in uw hiërarchie.

   ```toml
   hostname = "<device fqdn or IP>"
   ```

   >[!TIP]
   >Als u de inhoud van het klembord in Nano wilt plakken gebruikt u `Shift+Right Click` of drukt u op `Shift+Insert`.

1. Zoek het gedeelte **bovenliggende hostname** voor een IOT edge apparaat in **lagere lagen**. Verwijder de opmerking bij de regel met de `parent_hostname` para meter en werk de waarde bij om naar de FQDN of het IP-adres van het bovenliggende apparaat te verwijzen. Gebruik de exacte waarde die u in het veld **hostnaam** van het bovenliggende apparaat plaatst. Voor het IoT Edge apparaat in de **bovenste laag**, houdt u deze para meter uit.

   ```toml
   parent_hostname = "<parent device fqdn or IP>"
   ```

   > [!NOTE]
   > Voor hiërarchieën met meer dan één onderliggende laag werkt u het veld *parent_hostname* bij met de FQDN van het apparaat in de laag direct erboven.

1. Zoek het gedeelte **Trust bundel CERT** van het bestand. Verwijder de opmerking bij de regel met de `trust_bundle_cert` para meter en werk de waarde bij met het pad naar de bestands-URI naar het basis-CA-certificaat dat wordt gedeeld door alle apparaten in de gateway hiërarchie.

   ```toml
   trust_bundle_cert = "<root CA certificate>"
   ```

1. De **inrichtings** sectie van het bestand zoeken. Verwijder de opmerkingen bij de regels voor **hand matige inrichting met Connection String**. Werk voor elk apparaat in uw hiërarchie de waarde van **connection_string** met de Connection String van uw IOT edge apparaat.

   ```toml
   # Manual provisioning with connection string
   [provisioning]
   source = "manual"
   connection_string: "<Device connection string>"
   ```

1. De sectie **standaard rand agent** zoeken.

   * Werk de edgeAgent-installatie kopie waarde voor het apparaat van de **bovenste laag** bij naar de open bare preview-versie van de module in azure container Registry.
   
     ```toml
     [agent.config]
     image = "mcr.microsoft.com/azureiotedge-agent:1.2.0-rc4"
     ```

   * Werk voor elk IoT Edge apparaat in **lagere lagen** de edgeAgent-installatie kopie zodanig bij dat deze naar het bovenliggende apparaat wijst, gevolgd door de poort waarop de API-proxy luistert. U implementeert de API-proxy module naar het bovenliggende apparaat in de volgende sectie.
   
     ```toml
     [agent.config]
     image = "<parent hostname value>:8000/azureiotedge-agent:1.2.0-rc4"
     ```

1. Zoek de sectie met het **Edge CA-certificaat** . Verwijder de opmerking bij de eerste drie regels van deze sectie. Werk vervolgens de twee para meters bij zodat deze verwijzen naar het CA-certificaat van het apparaat en de persoonlijke sleutel bestanden van de apparaat-CA die u hebt gemaakt in de vorige sectie en verplaatst naar het IoT Edge-apparaat. Geef de bestands-URI-paden op, `file:///<path>/<filename>` zoals `file:///certs/iot-edge-device-ca-top-layer-device.key.pem` .

   ```yml
   [edge_ca]
   cert = "<File URI path to the device full chain CA certificate unique to this device.>"
   pk = "<File URI path to the device CA private key unique to this device.>"
   ```

   >[!NOTE]
   >Zorg ervoor dat u de **volledige** CA-certificaat paden en bestands naam van de certificerings instantie gebruikt om het veld in te vullen `device_ca_cert` .

1. Sla het bestand op en sluit het.

   `CTRL + X`, `Y`, `Enter`

1. Nadat u de inrichtings gegevens hebt ingevoerd in het configuratie bestand, moet u de wijzigingen Toep assen:

   ```bash
   sudo iotedge config apply
   ```

Voordat u doorgaat, moet u ervoor zorgen dat u het configuratie bestand van elk apparaat in de hiërarchie hebt bijgewerkt. Afhankelijk van uw hiërarchie structuur hebt u één apparaat met de **hoogste laag** en een of meer **apparaten met een lagere laag** geconfigureerd.

Als u de bovenstaande stappen correct hebt voltooid, kunt u controleren of uw apparaten correct zijn geconfigureerd.

1. Voer de configuratie-en connectiviteits controles op uw apparaten uit:

   ```bash
   sudo iotedge check
   ```

In het **bovenste laag apparaat** verwacht u een uitvoer te zien met verschillende evaluaties en ten minste één waarschuwing. Met de controle op de `latest security daemon` wordt u gewaarschuwd dat een andere IOT Edge versie de laatste stabiele versie is, omdat IOT Edge versie 1,2 in de open bare preview is. Mogelijk ziet u aanvullende waarschuwingen over beleids regels voor logboeken en, afhankelijk van uw netwerk, DNS-beleid.

Op een **apparaat met een lagere laag** wordt verwacht een uitvoer te zien die vergelijkbaar is met het apparaat van de bovenste laag, maar met een extra waarschuwing dat de module EdgeAgent niet uit de upstream kan worden gehaald. Dit is acceptabel, omdat de proxy module IoT Edge API en de module docker Container Registry, waarbij lagere laag apparaten installatie kopieën gaan maken, nog niet zijn geïmplementeerd op het apparaat voor de **bovenste laag**.

Hieronder ziet u een voor beeld van een uitvoer van `iotedge check` :

[Voorbeeld configuratie en connectiviteits resultaten](./media/tutorial-nested-iot-edge/configuration-and-connectivity-check-results.png)

Zodra u tevreden bent over de configuraties op elk apparaat, bent u klaar om door te gaan.

## <a name="deploy-modules-to-the-top-layer-device"></a>Modules implementeren op het apparaat in de bovenste laag

Modules dienen om de implementatie en de IoT Edge runtime op uw apparaten te volt ooien en de structuur van uw hiërarchie te bepalen. Met de proxy module IoT Edge-API kunt u HTTP-verkeer veilig Routs via één poort van uw lagere laag apparaten. De docker-REGI ster-module biedt een opslag plaats van docker-installatie kopieën die door uw lagere laag apparaten kunnen worden geopend door het door sturen van installatie kopieën naar het apparaat voor de bovenste laag.

U kunt de Azure Portal of Azure CLI gebruiken om modules te implementeren op uw apparaat op de hoogste laag.

>[!NOTE]
>De resterende stappen voor het volt ooien van de configuratie van de IoT Edge runtime en het implementeren van werk belastingen worden niet uitgevoerd op uw IoT Edge apparaten.

# <a name="portal"></a>[Portal](#tab/azure-portal)

[In Azure Portal](https://ms.portal.azure.com/):

1. Ga naar uw IoT Hub.

1. Selecteer in het menu in het linkerdeelvenster, onder **Automatisch apparaatbeheer**, de optie **IoT Edge**.

1. Klik op de apparaat-id van uw edge-apparaat in de **bovenste laag** in de lijst met apparaten.

1. Selecteer op de bovenste balk **Modules instellen**.

1. Selecteer **runtime-instellingen** naast het tandwielpictogram.

1. Voer onder **Edge Hub** in het installatiekopieveld `mcr.microsoft.com/azureiotedge-hub:1.2.0-rc4` in.

   ![De installatiekopie van de Edge Hub bewerken](./media/tutorial-nested-iot-edge/edge-hub-image.png)

1. Voeg de volgende omgevingsvariabelen toe aan uw Edge Hub-module:

    | Name | Waarde |
    | - | - |
    | `experimentalFeatures__enabled` | `true` |
    | `experimentalFeatures__nestedEdgeEnabled` | `true` |

   ![De omgevingsvariabelen van de Edge Hub bewerken](./media/tutorial-nested-iot-edge/edge-hub-environment-variables.png)

1. Voer onder **Edge Agent** in het installatiekopieveld `mcr.microsoft.com/azureiotedge-agent:1.2.0-rc4` in. Selecteer **Opslaan**.

1. Voeg de Docker-registermodule toe aan uw apparaat in de bovenste laag. Selecteer **+ Toevoegen** en kies **IoT Edge module** in de vervolgkeuzelijst. Geef de naam `registry` op voor uw Docker-registermodule en voer `registry:latest` in voor de installatiekopie-URI. Voeg vervolgens omgevingsvariabelen toe en maak opties om uw lokale registermodule te plaatsen in het Microsoft-containerregister om containerinstallatiekopieën te downloaden en om deze installatiekopieën in registry:5000 uit te voeren.

1. Voer onder het tabblad met omgevingsvariabelen het volgende naam-waardepaar voor omgevingsvariabelen in:

    | Name | Waarde |
    | - | - |
    | `REGISTRY_PROXY_REMOTEURL` | `https://mcr.microsoft.com` |

1. Voer op het tabblad Opties voor het maken van containers de volgende JSON in:

   ```json
   {
    "HostConfig": {
        "PortBindings": {
            "5000/tcp": [
                {
                    "HostPort": "5000"
                }
            ]
         }
      }
   }
   ```

1. Voeg vervolgens de API-proxymodule toe aan uw apparaat in de bovenste laag. Selecteer **+ Toevoegen** en kies **Marketplace Module** in de vervolgkeuzelijst. Zoek naar `IoT Edge API Proxy` en selecteer de module. De IoT Edge API-proxy maakt gebruik van poort 8000 en is zodanig geconfigureerd dat deze standaard uw registermodule met de naam `registry` standaard op poort 5000 gebruikt.

1. Selecteer **Beoordelen + maken** en vervolgens **Maken** om de implementatie te voltooien. Met de IoT Edge-runtime van uw apparaat in de bovenste laag, dat toegang heeft tot internet, worden de **openbare preview**-configuraties voor IoT Edge-hub en IoT Edge-agent opgehaald en uitgevoerd.

   ![Volledige implementatie met Edge Hub, Edge Agent, Registry Module en API Proxy Module](./media/tutorial-nested-iot-edge/complete-top-layer-deployment.png)

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

1. Voer in de [Azure Cloud Shell](https://shell.azure.com/) de volgende opdracht in om een deployment.json-bestand te maken:

   ```azurecli-interactive
   code deploymentTopLayer.json
   ```

1. Kopieer de inhoud van de JSON hieronder in uw deployment.json, sla het bestand op en sluit het.

   ```json
   {
       "modulesContent": {
           "$edgeAgent": {
               "properties.desired": {
                   "modules": {
                       "registry": {
                           "settings": {
                               "image": "registry:latest",
                               "createOptions": "{\"HostConfig\":{\"PortBindings\":{\"5000/tcp\":[{\"HostPort\":\"5000\"}]}}}"
                           },
                           "type": "docker",
                           "version": "1.0",
                           "env": {
                               "REGISTRY_PROXY_REMOTEURL": {
                                   "value": "https://mcr.microsoft.com"
                               } 
                           },
                           "status": "running",
                           "restartPolicy": "always"
                       },
                       "IoTEdgeAPIProxy": {
                           "settings": {
                               "image": "mcr.microsoft.com/azureiotedge-api-proxy",
                               "createOptions": "{\"HostConfig\": {\"PortBindings\": {\"8000/tcp\": [{\"HostPort\":\"8000\"}]}}}"
                           },
                           "type": "docker",
                           "env": {
                               "NGINX_DEFAULT_PORT": {
                                   "value": "8000"
                               },
                               "DOCKER_REQUEST_ROUTE_ADDRESS": {
                                   "value": "registry:5000"
                               },
                               "BLOB_UPLOAD_ROUTE_ADDRESS": {
                                   "value": "AzureBlobStorageonIoTEdge:11002"
                               }
                           },
                           "status": "running",
                           "restartPolicy": "always",
                           "version": "1.0"
                       }
                   },
                   "runtime": {
                       "settings": {
                           "minDockerVersion": "v1.25"
                       },
                       "type": "docker"
                   },
                   "schemaVersion": "1.1",
                   "systemModules": {
                       "edgeAgent": {
                           "settings": {
                               "image": "mcr.microsoft.com/azureiotedge-agent:1.2.0-rc4",
                               "createOptions": ""
                           },
                           "type": "docker"
                       },
                       "edgeHub": {
                           "settings": {
                               "image": "mcr.microsoft.com/azureiotedge-hub:1.2.0-rc4",
                               "createOptions": "{\"HostConfig\":{\"PortBindings\":{\"443/tcp\":[{\"HostPort\":\"443\"}],\"5671/tcp\":[{\"HostPort\":\"5671\"}],\"8883/tcp\":[{\"HostPort\":\"8883\"}]}}}"
                           },
                           "type": "docker",
                           "env": {
                               "experimentalFeatures__enabled": {
                                   "value": "true"
                               },
                               "experimentalFeatures__nestedEdgeEnabled": {
                                   "value": "true"
                               }
                           },
                           "status": "running",
                           "restartPolicy": "always"
                       }
                   }
               }
           },
           "$edgeHub": {
               "properties.desired": {
                   "routes": {
                       "route": "FROM /messages/* INTO $upstream"
                   },
                   "schemaVersion": "1.1",
                   "storeAndForwardConfiguration": {
                       "timeToLiveSecs": 7200
                   }
               }
           }
       }
   }
   ```

1. Voer de volgende opdracht in om een implementatie te maken voor uw edge-apparaat van de bovenste laag:

   ```azurecli-interactive
   az iot edge set-modules --device-id <top_layer_device_id> --hub-name <iot_hub_name> --content ./deploymentTopLayer.json
   ```

---

Als u de bovenstaande stappen correct hebt uitgevoerd, moet uw apparaat op de **bovenste laag** de vier modules rapporteren: de IOT Edge API-proxy module, de docker container Registry module en de systeem modules, zoals **opgegeven in de implementatie**. Het kan enkele minuten duren voordat het apparaat de nieuwe implementatie ontvangt en de modules start. Vernieuw de pagina totdat de module temperatuur sensor wordt weer gegeven als **gerapporteerd door apparaat**. Zodra de modules zijn gerapporteerd door het apparaat, kunt u door gaan.

## <a name="deploy-modules-to-the-lower-layer-device"></a>Modules implementeren op het apparaat in de onderliggende laag

Modules fungeren ook als werk belasting van uw lagere laag apparaten. Met de gesimuleerde temperatuur sensor module worden voor beelden van telemetriegegevens gemaakt om een functionele stroom van gegevens te bieden via uw-hiërarchie van apparaten.

Als u modules naar uw lagere laag apparaten wilt implementeren, kunt u de Azure Portal of Azure CLI gebruiken.

# <a name="portal"></a>[Portal](#tab/azure-portal)

[In Azure Portal](https://ms.portal.azure.com/):

1. Ga naar uw IoT Hub.

1. Selecteer in het menu in het linkerdeelvenster, onder **Automatisch apparaatbeheer**, de optie **IoT Edge**.

1. Klik op de apparaat-id van uw apparaat in de onderliggende laag in de lijst met IoT Edge-apparaten.

1. Selecteer op de bovenste balk **Modules instellen**.

1. Selecteer **runtime-instellingen** naast het tandwielpictogram.

1. Voer onder **Edge Hub** in het installatiekopieveld `$upstream:8000/azureiotedge-hub:1.2.0-rc4` in.

1. Voeg de volgende omgevingsvariabelen toe aan uw Edge Hub-module:

    | Name | Waarde |
    | - | - |
    | `experimentalFeatures__enabled` | `true` |
    | `experimentalFeatures__nestedEdgeEnabled` | `true` |

1. Voer onder **Edge Agent** in het installatiekopieveld `$upstream:8000/azureiotedge-agent:1.2.0-rc4` in. Selecteer **Opslaan**.

1. Voeg de temperatuursensormodule toe. Selecteer **+ Toevoegen** en kies **Marketplace Module** in de vervolgkeuzelijst. Zoek naar `Simulated Temperature Sensor` en selecteer de module.

1. Selecteer onder **IoT Edge Modules** de `Simulated Temperature Sensor` module die u zojuist hebt toegevoegd en werk de installatiekopie-URI bij zodat deze naar `$upstream:8000/azureiotedge-simulated-temperature-sensor:1.0` verwijst.

1. Selecteer **Opslaan**, **Beoordelen + maken** en vervolgens **Maken** om de implementatie te voltooien.

   ![Volledige implementatie met Edge Hub, Edge Agent en gesimuleerde temperatuursensor](./media/tutorial-nested-iot-edge/complete-lower-layer-deployment.png)

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

1. Voer in de [Azure Cloud Shell](https://shell.azure.com/) de volgende opdracht in om een deployment.json-bestand te maken:

   ```azurecli-interactive
   code deploymentLowerLayer.json
   ```

1. Kopieer de inhoud van de JSON hieronder in uw deployment.json, sla het bestand op en sluit het.

   ```json
   {
       "modulesContent": {
           "$edgeAgent": {
               "properties.desired": {
                   "modules": {
                       "simulatedTemperatureSensor": {
                           "settings": {
                               "image": "$upstream:8000/azureiotedge-simulated-temperature-sensor:1.0",
                               "createOptions": ""
                           },
                           "type": "docker",
                           "status": "running",
                           "restartPolicy": "always",
                           "version": "1.0"
                       }
                   },
                   "runtime": {
                       "settings": {
                           "minDockerVersion": "v1.25"
                       },
                       "type": "docker"
                   },
                   "schemaVersion": "1.1",
                   "systemModules": {
                       "edgeAgent": {
                           "settings": {
                               "image": "$upstream:8000/azureiotedge-agent:1.2.0-rc4",
                               "createOptions": ""
                           },
                           "type": "docker"
                       },
                       "edgeHub": {
                           "settings": {
                               "image": "$upstream:8000/azureiotedge-hub:1.2.0-rc4",
                               "createOptions": "{\"HostConfig\":{\"PortBindings\":{\"443/tcp\":[{\"HostPort\":\"443\"}],\"5671/tcp\":[{\"HostPort\":\"5671\"}],\"8883/tcp\":[{\"HostPort\":\"8883\"}]}}}"
                           },
                           "type": "docker",
                           "env": {
                               "experimentalFeatures__enabled": {
                                   "value": "true"
                               },
                               "experimentalFeatures__nestedEdgeEnabled": {
                                   "value": "true"
                               }
                           },
                           "status": "running",
                           "restartPolicy": "always"
                       }
                   }
               }
           },
           "$edgeHub": {
               "properties.desired": {
                   "routes": {
                       "route": "FROM /messages/* INTO $upstream"
                   },
                   "schemaVersion": "1.1",
                   "storeAndForwardConfiguration": {
                       "timeToLiveSecs": 7200
                   }
               }
           }
       }
   }
   ```

1. Voer de volgende opdracht in om een setmodule-implementatie te maken voor uw edge-apparaat van de onderliggende laag:

   ```azurecli-interactive
   az iot edge set-modules --device-id <lower_layer_device_id> --hub-name <iot_hub_name> --content ./deploymentLowerLayer.json

---

Notice that the image URI that we used for the simulated temperature sensor module pointed to `$upstream:8000` instead of to a container registry. We configured this device to not have direct connections to the cloud, because it's in a lower layer. To pull container images, this device requests the image from its parent device instead. At the top layer, the API proxy module routes this container request to the registry module, which handles the image pull.

If you completed the above steps correctly, your **lower layer device** should report three modules: the temperature sensor module and the system modules, as **Specified in Deployment**. It may take a few minutes for the device to receive its new deployment, request the container image, and start the module. Refresh the page until you see the temperature sensor module listed as **Reported by Device**. Once the modules are reported by the device, you are ready to continue.

## Troubleshooting

Run the `iotedge check` command to verify the configuration and to troubleshoot errors.

You can run `iotedge check` in a nested hierarchy, even if the child machines don't have direct internet access.

When you run `iotedge check` from the lower layer, the program tries to pull the image from the parent through port 443.

In this tutorial, we use port 8000, so we need to specify it:

```bash
sudo iotedge check --diagnostics-image-name <parent_device_fqdn_or_ip>:8000/azureiotedge-diagnostics:1.2.0-rc4
```

De `azureiotedge-diagnostics`-waarde wordt opgehaald uit het containerregister dat aan de registermodule is gekoppeld. In deze zelfstudie is deze waarde standaard ingesteld op https://mcr.microsoft.com:

| Naam | Waarde |
| - | - |
| `REGISTRY_PROXY_REMOTEURL` | `https://mcr.microsoft.com` |

Als u een persoonlijk containerregister gebruikt, moet u ervoor zorgen dat alle installatiekopieën (bijvoorbeeld IoTEdgeAPIProxy, edgeAgent, edgeHub en diagnose) in het containerregister aanwezig zijn.

## <a name="view-generated-data"></a>Gegenereerde gegevens weergeven

De **gesimuleerde temperatuursensormodule** die u hebt gepusht, genereert samples van omgevingsgegevens. De module verzendt berichten met informatie over omgevingstemperatuur, luchtvochtigheid, machinetemperatuur en druk, evenals een tijdstempel.

U kunt de berichten zien binnenkomen bij uw IoT Hub door de [Azure IoT Hub-extensie voor Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit) te gebruiken.

U kunt deze berichten ook bekijken via de [Azure Cloud Shell](https://shell.azure.com/):

   ```azurecli-interactive
   az iot hub monitor-events -n <iothub_name> -d <lower-layer-device-name>
   ```

## <a name="clean-up-resources"></a>Resources opschonen

U kunt de lokale configuraties en Azure-resources die u in dit artikel hebt gemaakt, verwijderen om kosten te voorkomen.

Om de resources te verwijderen:

1. Meld u aan bij [Azure Portal](https://portal.azure.com) en selecteer **Resourcegroepen**.

2. Selecteer de naam van de resourcegroep die uw IoT Edge-testresources bevat. 

3. Bekijk de lijst met resources die zich bevinden in de resourcegroep. Als u alle mappen wilt verwijderen, kunt u **Resourcegroep verwijderen** selecteren. Als u slechts een deel ervan wilt verwijderen, kunt u in elke resource afzonderlijk klikken om ze te verwijderen. 

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u twee IoT Edge-apparaten geconfigureerd als gateways, en het ene ingesteld als bovenliggend apparaat van het andere. Vervolgens hebt u een containerinstallatiekopie naar het onderliggende apparaat gebracht via een gateway.

Ga verder met de andere zelfstudies om te zien hoe u met Azure IoT Edge meer oplossingen voor uw bedrijf kunt maken.

> [!div class="nextstepaction"]
> [Een Azure Machine Learning-model implementeren als een module](tutorial-deploy-machine-learning.md)
