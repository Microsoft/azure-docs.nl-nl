---
title: 'Zelfstudie: een hiërarchie van IoT Edge-apparaten maken - Azure IoT Edge'
description: In deze zelfstudie ziet u hoe u een hiërarchische structuur van IoT Edge-apparaten met behulp van gateways maakt.
author: v-tcassi
manager: philmea
ms.author: v-tcassi
ms.date: 11/10/2020
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
monikerRange: '>=iotedge-2020-11'
ms.openlocfilehash: a7f82ec5a4ef918b1bc7ab0fd6813199c0a1d772
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100366389"
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
> * Een API-proxymodule gebruiken om HTTP-verkeer veilig via een enkele poort vanaf uw apparaten op een lagere laag te routeren.

In deze zelfstudie worden de volgende netwerklagen gedefinieerd:

* **Bovenste laag**: IoT Edge-apparaten van deze laag kunnen rechtstreeks verbinding maken met de cloud.

* **Onderliggende laag**: IoT Edge-apparaten van deze laag kunnen niet rechtstreeks verbinding maken met de cloud. Ze moeten via een of meer tussenliggende IoT Edge-apparaten gaan om gegevens te verzenden en te ontvangen.

In deze zelfstudie wordt voor het gemak een hiërarchie van twee apparaten gebruikt. Eén apparaat, **topLayerDevice**, vertegenwoordigt een apparaat in de bovenste laag van de hiërarchie, w3aar rechtstreeks verbinding kan worden gemaakt met de cloud. Dit apparaat wordt ook wel het **bovenliggende apparaat** genoemd. Het andere apparaat, **lowerLayerDevice**, vertegenwoordigt een apparaat in de onderliggende laag van de hiërarchie, waar niet rechtstreeks verbinding kan worden gemaakt met de cloud. Dit apparaat wordt ook wel het **onderliggende apparaat** genoemd. U kunt extra apparaten met een lagere laag toevoegen om uw productieomgeving weer te geven. De configuratie van eventuele extra apparaten in onderliggende lagen volgt de configuratie van **lowerLayerDevice**.

## <a name="prerequisites"></a>Vereisten

Als u een hiërarchie van IoT Edge-apparaten wilt maken, hebt u het volgende nodig:

* Een computer (Windows of Linux) met internetverbinding.
* Een Azure-account met een geldig abonnement. Als u geen [Azure-abonnement](../guides/developer/azure-developer-guide.md#understanding-accounts-subscriptions-and-billing) hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.
* Een gratis of standaard [IoT-hub](../iot-hub/iot-hub-create-through-portal.md)-laag in Azure.
* Azure CLI v2.3.1 met de Azure IoT-uitbreiding v0.10.6 of hoger geïnstalleerd. Deze zelfstudie maakt gebruik van de [Azure Cloud Shell](../cloud-shell/overview.md). Als u niet bekend bent met de Azure Cloud Shell kunt u [een Snelstartgids bekijken voor meer informatie](./quickstart-linux.md#prerequisites).
* Twee Linux-apparaten om te configureren als IoT Edge-apparaten. Als u geen apparaten beschikbaar hebt, kunt u twee virtuele Azure-machines maken door de tijdelijke aanduiding voor tekst in de volgende opdracht te vervangen en twee keer uit te voeren:

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

U kunt dit scenario ook proberen door het script [Azure IoT Edge voor Industrial IoT-voorbeeld](https://aka.ms/iotedge-nested-sample) te volgen, waarmee virtuele Azure-machines worden geïmplementeerd als vooraf geconfigureerde apparaten om een ​​fabrieksomgeving te simuleren.

## <a name="configure-your-iot-edge-device-hierarchy"></a>Uw IoT Edge-apparaathiërarchie configureren

### <a name="create-a-hierarchy-of-iot-edge-devices"></a>Een hiërarchie van IoT Edge-apparaten maken

De eerste stap, het maken van uw IoT Edge-apparaten, kan worden gedaan via Azure Portal of Azure CLI. In deze zelfstudie maakt u een hiërarchie van twee IoT Edge-apparaten: **topLayerDevice** en de bijbehorende onderliggende **lowerLayerDevice**.

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Ga in [Azure Portal](https://ms.portal.azure.com/) naar uw IoT Hub.

1. Selecteer in het menu in het linkerdeelvenster, onder **Automatisch apparaatbeheer**, de optie **IoT Edge**.

1. Selecteer **+ Een IoT Edge-apparaat toevoegen**. Dit apparaat wordt het apparaat van de bovenste laag, dus voer een geschikte unieke apparaat-id in. Selecteer **Opslaan**.

1. Selecteer nogmaals **+ Een IoT Edge-apparaat toevoegen**. Dit apparaat wordt het apparaat van de onderliggende laag, dus voer een geschikte unieke apparaat-id in.

1. Kies **Een bovenliggend apparaat instellen**, kies uw bovenste laag-apparaat in de lijst met apparaten en selecteer **Ok**. Selecteer **Opslaan**.

   ![Bovenliggend apparaat instellen voor apparaat van onderliggende laag](./media/tutorial-nested-iot-edge/set-parent-device.png)

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

1. Voer in [Azure Cloud Shell](https://shell.azure.com/) de volgende opdracht in om een IoT-​​apparaat in uw hub te maken. Dit apparaat wordt het apparaat van de bovenste laag, dus voer een geschikte unieke apparaat-id in:

   ```azurecli-interactive
   az iot hub device-identity create --device-id {top_layer_device_id} --edge-enabled --hub-name {hub_name}
   ```

1. Voer de volgende opdracht in om uw onderliggend IoT Edge-apparaat te maken en de relatie bovenliggend-onderliggend tussen apparaten te maken:

    ```azurecli-interactive
    az iot hub device-identity create --device-id {lower_layer_device_id} --edge-enabled --pd {top_layer_device_id} --hub-name {iothub_name}
    ```

---

Noteer de verbindingstekenreeks van elk IoT Edge-apparaat. Deze worden later gebruikt.

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
   ./certGen.sh create_edge_device_ca_certificate "top-layer-device"
   ./certGen.sh create_edge_device_ca_certificate "lower-layer-device"
   ```

   Met deze scriptopdracht worden verschillende certificaat- en sleutelbestanden gemaakt, maar we gebruiken het volgende certificaat en sleutelpaar op elk IoT Edge-apparaat en er kan naar worden verwezen in het bestand config.yaml:

   * `<WRKDIR>/certs/iot-edge-device-<CA cert name>-full-chain.cert.pem`
   * `<WRKDIR>/private/iot-edge-device-<CA cert name>.key.pem`

Elk apparaat heeft een kopie van het basis-CA-certificaat en een kopie van zijn eigen CA-certificaat en persoonlijke sleutel nodig. U kunt een USB-station of [beveiligde bestandskopie](https://www.ssh.com/ssh/scp/) gebruiken om de certificaten naar elk apparaat te verplaatsen.

1. Nadat de certificaten zijn overgedragen, installeert u de basis-CA voor elk apparaat.

   ```bash
   sudo cp <path>/azure-iot-test-only.root.ca.cert.pem /usr/local/share/ca-certificates/azure-iot-test-only.root.ca.cert.pem.crt
   sudo update-ca-certificates
   ```

   Met deze opdracht moet worden aangeven dat er één certificaat is toegevoegd in /etc/ssl/certs.

### <a name="install-iot-edge-on-the-devices"></a>IoT Edge installeren op de apparaten

Installeer IoT Edge door deze stappen op beide apparaten te volgen.

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

1. Installeer de hsmlib- en IoT Edge-daemon. Als u de activa voor andere Linux-distributies wilt bekijken, [gaat u naar de GitHub-release](https://github.com/Azure/azure-iotedge/releases/tag/1.2.0-rc1). <!-- Update with proper image links on release -->

   ```bash
   curl -L https://github.com/Azure/azure-iotedge/releases/download/1.2.0-rc1/libiothsm-std_1.2.0_rc1-1-1_debian9_amd64.deb -o libiothsm-std.deb
   curl -L https://github.com/Azure/azure-iotedge/releases/download/1.2.0-rc1/iotedge_1.2.0_rc1-1_debian9_amd64.deb -o iotedge.deb
   sudo dpkg -i ./libiothsm-std.deb
   sudo dpkg -i ./iotedge.deb
   ```

### <a name="configure-the-iot-edge-runtime"></a>De IoT Edge-runtime configureren.

Configureer de IoT Edge-runtime door deze stappen op beide apparaten uit te voeren. Het configureren van de IoT Edge-runtime voor uw apparaten bestaat uit vier stappen, die allemaal worden uitgevoerd door het IoT Edge-configuratiebestand te bewerken:

1. Richt elk apparaat handmatig in door de verbindingsreeks van dat apparaat toe te voegen aan het configuratiebestand.

1. Werk de certificaten van uw apparaat bij door het configuratiebestand aan te wijzen op het CA-certificaat van het apparaat, de persoonlijke CA-sleutel van het apparaat en het basis-CA-certificaat.

1. Bootstrap het systeem met behulp van de IoT Edge-agent.

1. Werk de parameter **hostnaam** bij voor uw apparaat in de **bovenste laag** en werk zowel de parameter **hostnaam** als de parameter **parent_hostname** bij voor uw apparaten in de **onderliggende laag**.

Voltooi deze stappen en start de IoT Edge-service opnieuw om uw apparaten te configureren.

1. Open het IoT Edge-configuratiebestand op elk apparaat.

   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

1. Zoek de inrichtingsconfiguraties van het bestand en verwijder het commentaar in de sectie **Handmatige configuratie met behulp van een verbindingsreeks**.

1. Werk de waarde van **device_connection_string** bij met de verbindingsreeks van uw IoT Edge-apparaat. Zorg ervoor dat alle andere inrichtingssecties vrij zijn van commentaar. Zorg ervoor dat de regel **inrichting:** geen voorafgaande witruimte heeft en dat geneste items door twee spaties worden ingesprongen.

   ```yml
   # Manual provisioning configuration using a connection string
   provisioning:
     source: "manual"
     device_connection_string: "<ADD DEVICE CONNECTION STRING HERE>"
     dynamic_reprovisioning: false
   ```

   >[!TIP]
   >Als u de inhoud van het klembord in Nano wilt plakken gebruikt u `Shift+Right Click` of drukt u op `Shift+Insert`.

1. Zoek de sectie **certificaten**. Verwijder het commentaar en werk de drie certificaatvelden bij om te verwijzen naar de certificaten die u in de vorige sectie hebt gemaakt en naar het IoT Edge-apparaat hebt verplaatst. Geef de bestands-URI-paden op, die de indeling `file:///<path>/<filename>` hebben.

   ```yml
   certificates:
     device_ca_cert: "<File URI path to the device CA certificate unique to this device.>"
     device_ca_pk: "<File URI path to the device CA private key unique to this device.>"
     trusted_ca_certs: "<File URI path to the root CA certificate shared by all devices in the gateway hierarchy.>"
   ```

1. Zoek voor uw apparaat in de **bovenste laag** de parameter **hostname**. Werk de waarde bij naar de volledig gekwalificeerde domeinnaam (FQDN) of het IP-adres van het IoT Edge-apparaat. Gebruik de waarde die u consistent kiest op de apparaten in uw hiërarchie.

   ```yml
   hostname: <device fqdn or IP>
   ```

1. Voor IoT Edge-apparaten in de **onderliggende lagen** werkt u het config-bestand bij om naar de FQDN of IP van het bovenliggende apparaat te wijzen, waarbij wordt afgestemd op wat in het veld **hostname** van het bovenliggende apparaat staat. Voor IoT Edge-apparaten in de **bovenste laag** laat u deze parameter zonder commentaar.

   ```yml
   parent_hostname: <parent device fqdn or IP>
   ```

   > [!NOTE]
   > Voor hiërarchieën met meer dan één onderliggende laag werkt u het veld *parent_hostname* bij met de FQDN van het apparaat in de laag direct erboven.

1. Zoek voor uw apparaat in de **bovenste laag** de yaml-sectie **agent** en werk de installatiekopiewaarde bij naar de juiste versie van de IoT Edge-agent. In dit geval verwijzen we de IoT Edge-agent van de bovenste laag naar de Azure Container Registry met de openbare preview-versie van de IoT Edge-agent-installatiekopie beschikbaar.

   ```yml
   agent:
     name: "edgeAgent"
     type: "docker"
     env: {}
     config:
       image: "mcr.microsoft.com/azureiotedge-agent:1.2.0-rc2"
       auth: {}
   ```

1. Voor IoT Edge-apparaten in de **onderste lagen** werkt u de domeinnaam van de installatiekopiewaarde bij zodat deze verwijst naar de FQDN of IP van het bovenliggende apparaat, gevolgd door de API-proxypoort, 8000. In de volgende sectie gaat u de API-proxy module toevoegen.

   ```yml
   agent:
     name: "edgeAgent"
     type: "docker"
     env: {}
     config:
       image: "<parent_device_fqdn_or_ip>:8000/azureiotedge-agent:1.2.0-rc2"
       auth: {}
   ```

1. Sla het bestand op en sluit het.

   `CTRL + X`, `Y`, `Enter`

1. Nadat u de inrichtingsgegevens in het configuratiebestand hebt ingevoerd, start u de daemon opnieuw op:

   ```bash
   sudo systemctl restart iotedge
   ```

## <a name="deploy-modules-to-the-top-layer-device"></a>Modules implementeren op het apparaat in de bovenste laag

De resterende stappen om de configuratie van de IoT Edge-runtime te voltooien en workloads te implementeren, worden vanuit de cloud uitgevoerd via Azure Portal of de Azure CLI.

# <a name="portal"></a>[Portal](#tab/azure-portal)

[In Azure Portal](https://ms.portal.azure.com/):

1. Ga naar uw IoT Hub.

1. Selecteer in het menu in het linkerdeelvenster, onder **Automatisch apparaatbeheer**, de optie **IoT Edge**.

1. Klik op de apparaat-id van uw edge-apparaat in de **bovenste laag** in de lijst met apparaten.

1. Selecteer op de bovenste balk **Modules instellen**.

1. Selecteer **runtime-instellingen** naast het tandwielpictogram.

1. Voer onder **Edge Hub** in het installatiekopieveld `mcr.microsoft.com/azureiotedge-hub:1.2.0-rc2` in.

   ![De installatiekopie van de Edge Hub bewerken](./media/tutorial-nested-iot-edge/edge-hub-image.png)

1. Voeg de volgende omgevingsvariabelen toe aan uw Edge Hub-module:

    | Name | Waarde |
    | - | - |
    | `experimentalFeatures__enabled` | `true` |
    | `experimentalFeatures__nestedEdgeEnabled` | `true` |

   ![De omgevingsvariabelen van de Edge Hub bewerken](./media/tutorial-nested-iot-edge/edge-hub-environment-variables.png)

1. Voer onder **Edge Agent** in het installatiekopieveld `mcr.microsoft.com/azureiotedge-agent:1.2.0-rc2` in. Selecteer **Opslaan**.

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
                       "dockerContainerRegistry": {
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
                               "image": "mcr.microsoft.com/azureiotedge-agent:1.2.0-rc2",
                               "createOptions": ""
                           },
                           "type": "docker"
                       },
                       "edgeHub": {
                           "settings": {
                               "image": "mcr.microsoft.com/azureiotedge-hub:1.2.0-rc2",
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

## <a name="deploy-modules-to-the-lower-layer-device"></a>Modules implementeren op het apparaat in de onderliggende laag

U kunt zowel Azure Portal als de Azure CLI gebruiken om workloads van de cloud naar uw apparaten in de **onderliggende laag** te implementeren.

# <a name="portal"></a>[Portal](#tab/azure-portal)

[In Azure Portal](https://ms.portal.azure.com/):

1. Ga naar uw IoT Hub.

1. Selecteer in het menu in het linkerdeelvenster, onder **Automatisch apparaatbeheer**, de optie **IoT Edge**.

1. Klik op de apparaat-id van uw apparaat in de onderliggende laag in de lijst met IoT Edge-apparaten.

1. Selecteer op de bovenste balk **Modules instellen**.

1. Selecteer **runtime-instellingen** naast het tandwielpictogram.

1. Voer onder **Edge Hub** in het installatiekopieveld `$upstream:8000/azureiotedge-hub:1.2.0-rc2` in.

1. Voeg de volgende omgevingsvariabelen toe aan uw Edge Hub-module:

    | Name | Waarde |
    | - | - |
    | `experimentalFeatures__enabled` | `true` |
    | `experimentalFeatures__nestedEdgeEnabled` | `true` |

1. Voer onder **Edge Agent** in het installatiekopieveld `$upstream:8000/azureiotedge-agent:1.2.0-rc2` in. Selecteer **Opslaan**.

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
                               "image": "$upstream:8000/azureiotedge-agent:1.2.0-rc2",
                               "createOptions": ""
                           },
                           "type": "docker"
                       },
                       "edgeHub": {
                           "settings": {
                               "image": "$upstream:8000/azureiotedge-hub:1.2.0-rc2",
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

On the device details page for your lower layer IoT Edge device, you should now see the temperature sensor module listed along the system modules as **Specified in deployment**. It may take a few minutes for the device to receive its new deployment, request the container image, and start the module. Refresh the page until you see the temperature sensor module listed as **Reported by device**.

## IotEdge check

Run the `iotedge check` command to verify the configuration and to troubleshoot errors.

You can run `iotedge check` in a nested hierarchy, even if the child machines don't have direct internet access.

When you run `iotedge check` from the lower layer, the program tries to pull the image from the parent through port 443.

In this tutorial, we use port 8000, so we need to specify it:

```bash
sudo iotedge check --diagnostics-image-name <parent_device_fqdn_or_ip>:8000/azureiotedge-diagnostics:1.2.0-rc2
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
