---
title: Downstreamapparaten IoT Edge verbinden - Azure IoT Edge | Microsoft Docs
description: Een apparaat configureren IoT Edge verbinding te maken met Azure IoT Edge gatewayapparaten.
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
monikerRange: '>=iotedge-2020-11'
ms.openlocfilehash: 500833d1bb4fc492942c08239bd488c2d2c16d30
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107484316"
---
# <a name="connect-a-downstream-iot-edge-device-to-an-azure-iot-edge-gateway"></a>Een downstreamapparaat IoT Edge verbinden met een Azure IoT Edge gateway

[!INCLUDE [iot-edge-version-202011](../../includes/iot-edge-version-202011.md)]

Dit artikel bevat instructies voor het tot stand brengen van een vertrouwde verbinding tussen een IoT Edge-gateway en een downstream IoT Edge apparaat.

In een gatewayscenario kan een IoT Edge zowel een gateway als een downstreamapparaat zijn. Meerdere IoT Edge-gateways kunnen gelaagd worden om een hiërarchie van apparaten te maken. De downstreamapparaten (of onderliggende) apparaten kunnen berichten verifiëren en verzenden of ontvangen via hun gatewayapparaat (of bovenliggend apparaat).

Er zijn twee verschillende configuraties voor IoT Edge in een gatewayhiërarchie en dit artikel heeft beide. De eerste is de **bovenste laag IoT Edge** apparaat. Wanneer meerdere IoT Edge apparaten via elkaar verbinding maken, wordt elk apparaat dat geen bovenliggend apparaat heeft maar rechtstreeks verbinding maakt met IoT Hub beschouwd als een apparaat in de bovenste laag. Dit apparaat is verantwoordelijk voor het verwerken van aanvragen van alle apparaten eronder. De andere configuratie is van toepassing op IoT Edge apparaat in een **lagere laag** van de hiërarchie. Deze apparaten kunnen een gateway zijn voor andere downstream-IoT- en IoT Edge-apparaten, maar moeten ook communicatie via hun eigen bovenliggende apparaten door sturen.

Voor sommige netwerkarchitectieën moet alleen het IoT Edge apparaat in een hiërarchie verbinding kunnen maken met de cloud. In deze configuratie kunnen alle IoT Edge in lagere lagen van een hiërarchie alleen communiceren met hun gatewayapparaat (of bovenliggend) apparaat en downstreamapparaten (of onderliggende) apparaten.

Alle stappen in dit artikel zijn gebaseerd op de stappen in Een IoT Edge-apparaat configureren om te fungeren als een transparante [gateway,](how-to-create-transparent-gateway.md)waarmee een IoT Edge-apparaat wordt ingesteld als gateway voor downstream-IoT-apparaten. Dezelfde basisstappen zijn van toepassing op alle gatewayscenario's:

* **Verificatie:** maak IoT Hub voor alle apparaten in de gatewayhiërarchie.
* **Autorisatie:** stel de bovenliggende/onderliggende relatie in IoT Hub om onderliggende apparaten toestemming te geven verbinding te maken met hun bovenliggende apparaat, zoals ze verbinding zouden maken met IoT Hub.
* **Gatewaydetectie:** zorg ervoor dat het onderliggende apparaat het bovenliggende apparaat kan vinden in het lokale netwerk.
* **Beveiligde verbinding:** stel een beveiligde verbinding tot stand met vertrouwde certificaten die deel uitmaken van dezelfde keten.

## <a name="prerequisites"></a>Vereisten

* Een gratis of standaard IoT-hub.
* Ten minste twee **IoT Edge apparaten**, één voor het apparaat in de bovenste laag en een of meer apparaten met een lagere laag. Als u geen beschikbare IoT Edge hebt, kunt u de Azure IoT Edge uitvoeren [op virtuele Ubuntu-machines.](how-to-install-iot-edge-ubuntuvm.md)
* Als u de Azure CLI gebruikt om apparaten te maken en beheren, moet u Azure CLI v2.3.1 hebben met de Azure IoT-extensie v0.10.6 of hoger geïnstalleerd.

Dit artikel bevat gedetailleerde stappen en opties voor het maken van de juiste gatewayhiërarchie voor uw scenario. Zie Create a [hierarchy of IoT Edge devices using gateways (Een hiërarchie van IoT Edge maken met behulp van gateways) voor een begeleide zelfstudie.](tutorial-nested-iot-edge.md)

## <a name="create-a-gateway-hierarchy"></a>Een gatewayhiërarchie maken

U maakt een IoT Edge gatewayhiërarchie door bovenliggende/onderliggende relaties te definiëren voor de IoT Edge apparaten in het scenario. U kunt een bovenliggend apparaat instellen wanneer u een nieuwe apparaat-id maakt of u kunt de bovenliggende en de kinderen van een bestaande apparaat-id beheren.

Bij de stap voor het instellen van bovenliggende/onderliggende relaties kunnen onderliggende apparaten verbinding maken met hun bovenliggende apparaat, net zoals ze verbinding zouden maken met IoT Hub.

Alleen IoT Edge kunnen bovenliggende apparaten zijn, maar IoT Edge apparaten en IoT-apparaten kunnen kinderen zijn. Een bovenliggend kan veel onderliggende kinderen hebben, maar een onderliggende kan slechts één bovenliggend onderliggende hebben. Een gatewayhiërarchie wordt gemaakt door bovenliggende/onderliggende sets aan elkaar te ketenen, zodat het onderliggende apparaat van het ene apparaat het bovenliggende van een ander apparaat is.

<!-- TODO: graphic of gateway hierarchy -->

# <a name="portal"></a>[Portal](#tab/azure-portal)

In de Azure Portal kunt u de bovenliggende/onderliggende relatie beheren wanneer u nieuwe apparaat-id's maakt of door bestaande apparaten te bewerken.

Wanneer u een nieuw IoT Edge maakt, hebt u de mogelijkheid om bovenliggende en onderliggende apparaten te kiezen uit de lijst met bestaande IoT Edge apparaten in die hub.

1. [Navigeer in Azure Portal](https://portal.azure.com)naar uw IoT-hub.
1. Selecteer **IoT Edge** in het navigatiemenu.
1. Selecteer **Een apparaat IoT Edge toevoegen.**
1. Naast het instellen van de apparaat-id en verificatie-instellingen kunt u een bovenliggend **apparaat instellen** of **Onderliggende apparaten kiezen.**
1. Kies het apparaat of de apparaten die u als bovenliggend of onderliggend apparaat wilt gebruiken.

U kunt ook bovenliggende/onderliggende relaties voor bestaande apparaten maken of beheren.

1. [Navigeer in Azure Portal](https://portal.azure.com)naar uw IoT-hub.
1. Selecteer **IoT Edge** in het navigatiemenu.
1. Selecteer het apparaat dat u wilt beheren in de lijst met **IoT Edge apparaten.**
1. Selecteer **Een bovenliggend apparaat instellen** of **Onderliggende apparaten beheren.**
1. Bovenliggende of onderliggende apparaten toevoegen of verwijderen.

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

De [azure-iot-extensie](/cli/azure/iot) voor de Azure CLI biedt opdrachten voor het beheren van uw IoT-resources. U kunt de bovenliggende/onderliggende relatie van IoT- en IoT Edge beheren wanneer u nieuwe apparaatidentiteiten maakt of door bestaande apparaten te bewerken.

Met [de set opdrachten az iot hub device-identity](/cli/azure/iot/hub/device-identity) kunt u de bovenliggende/onderliggende relaties voor een bepaald apparaat beheren.

De opdracht bevat parameters voor het toevoegen van apparaten met kinderen en het instellen van een bovenliggend `create` apparaat op het moment dat het apparaat wordt gemaakt.

Met aanvullende apparaat-id-opdrachten, waaronder , en of en , kunt u de `add-children` `list-children` `remove-children` `get-parent` `set-parent` bovenliggende/onderliggende relaties voor bestaande apparaten beheren.

---

## <a name="prepare-certificates"></a>Certificaten voorbereiden

Er moet een consistente keten van certificaten worden geïnstalleerd op alle apparaten in dezelfde gatewayhiërarchie om een veilige communicatie tussen elkaar tot stand te brengen. Elk apparaat in de hiërarchie, of IoT Edge of een IoT Leaf-apparaat, heeft een kopie van hetzelfde basis-CA-certificaat nodig. Elk IoT Edge in de hiërarchie gebruikt vervolgens dat basis-CA-certificaat als basis voor het CA-certificaat van het apparaat.

Met deze installatie kan elk downstream IoT Edge-apparaat of IoT Leaf-apparaat de identiteit van het bovenliggende apparaat verifiëren door te controleren of de edgeHub waar ze verbinding mee maken, een servercertificaat heeft dat is ondertekend door het gedeelde basis-CA-certificaat.

<!-- TODO: certificate graphic -->

Maak de volgende certificaten:

* Een **basis-CA-certificaat,** het bovenste gedeelde certificaat voor alle apparaten in een bepaalde gatewayhiërarchie. Dit certificaat is geïnstalleerd op alle apparaten.
* Eventuele **tussenliggende certificaten die** u wilt opnemen in de basiscertificaatketen.
* Een **CA-certificaat voor het** apparaat en de persoonlijke **sleutel**, gegenereerd door de basiscertificaten en tussenliggende certificaten. U hebt één uniek CA-certificaat voor elk apparaat IoT Edge in de gatewayhiërarchie.

U kunt een zelfondertekende certificeringsinstantie gebruiken of er een aanschaffen bij een vertrouwde commerciële certificeringsinstantie zoals Baltimore, Verisign, Digicert of GlobalSign.

Als u geen eigen certificaten hebt om te gebruiken, kunt u democertificaten maken om de functies van [IoT Edge te testen.](how-to-create-test-certificates.md) Volg de stappen in dat artikel om één set basis- en tussencertificaten te maken en vervolgens ca-certificaten voor IoT Edge apparaat te maken voor elk van uw apparaten.

## <a name="configure-iot-edge-on-devices"></a>Apparaten IoT Edge configureren

De stappen voor het instellen IoT Edge gateway zijn vergelijkbaar met de stappen voor het instellen IoT Edge als een downstreamapparaat.

Als u gatewaydetectie wilt inschakelen, moet elk IoT Edge gatewayapparaat worden geconfigureerd met een **hostnaam** die door de onderliggende apparaten wordt gebruikt om het te vinden in het lokale netwerk. Elk downstream IoT Edge moet worden geconfigureerd met een parent_hostname **verbinding** te maken. Als één IoT Edge apparaat zowel een bovenliggend als een onderliggend apparaat is, zijn beide parameters nodig.

Als u beveiligde verbindingen wilt inschakelen, moet elk IoT Edge-apparaat in een gatewayscenario worden geconfigureerd met een uniek CA-certificaat voor apparaten en een kopie van het basis-CA-certificaat dat wordt gedeeld door alle apparaten in de gatewayhiërarchie.

Als het goed is, IoT Edge al geïnstalleerd op uw apparaat. Als dat niet het beste is, volgt u de stappen om een [IoT Edge-apparaat in](how-to-register-device.md) IoT Hub registreren en vervolgens Azure IoT Edge [runtime installeren.](how-to-install-iot-edge.md)

De stappen in deze sectie verwijzen naar het **basis-CA-certificaat,** het **CA-certificaat** van het apparaat en de persoonlijke sleutel die eerder in dit artikel zijn besproken. Als u deze certificaten op een ander apparaat hebt gemaakt, moet u deze beschikbaar hebben op dit apparaat. U kunt de bestanden fysiek overdragen, zoals met een USB-station, met een service zoals Azure Key Vault , [of](../key-vault/general/overview.md)met een functie zoals [Beveiligde bestandskopie.](https://www.ssh.com/ssh/scp/)

Gebruik de volgende stappen om de IoT Edge op uw apparaat te configureren.

Zorg ervoor dat de gebruiker **iotedge** leesmachtigingen heeft voor de map met de certificaten en sleutels.

1. Installeer het **basis-CA-certificaat** op IoT Edge apparaat.

   ```bash
   sudo cp <path>/<root ca certificate>.pem /usr/local/share/ca-certificates/<root ca certificate>.pem.crt
   ```

1. Werk het certificaatopslag bij.

   ```bash
   sudo update-ca-certificates
   ```

   Met deze opdracht moet worden uitgevoerd dat er één certificaat is toegevoegd aan /etc/ssl/certs.

1. Open het IoT Edge-configuratiebestand.

   ```bash
   sudo nano /etc/aziot/config.toml
   ```

   >[!TIP]
   >Als het configuratiebestand nog niet op uw apparaat bestaat, gebruikt u `/etc/aziot/config.toml.edge.template` als sjabloon om er een te maken.

1. Zoek de **sectie Hostnaam** in het configuratiebestand. Wijs de regel met de parameter uit en werk de waarde bij naar de `hostname` FQDN (Fully Qualified Domain Name) of het IP-adres van het IoT Edge apparaat.

   De waarde van deze parameter is welke downstreamapparaten gebruiken om verbinding te maken met deze gateway. De hostnaam heeft standaard de computernaam, maar de FQDN of het IP-adres is vereist om downstreamapparaten te verbinden.

   Gebruik een hostnaam die korter is dan 64 tekens. Dit is de tekenlimiet voor de algemene naam van een servercertificaat.

   Wees consistent met het hostnaampatroon in een gatewayhiërarchie. Gebruik FQDN's of IP-adressen, maar niet beide.

1. *Als dit apparaat een onderliggend apparaat is, gaat* u naar de sectie **Bovenliggende hostnaam.** Uitcommenter en werk de parameter bij als de FQDN of het IP-adres van het bovenliggende apparaat, overeenkomstig wat is opgegeven als hostnaam in het configuratiebestand van het `parent_hostname` bovenliggende apparaat.

1. Zoek de **sectie Bundelcert vertrouwen.** Uitcommenter de parameter en `trust_bundle_cert` werk deze bij met de bestands-URI naar het basis-CA-certificaat op uw apparaat.

1. Controleer of IoT Edge apparaat de juiste versie van de IoT Edge gebruikt wanneer het wordt gestart.

   Zoek de **sectie Standaard Edge-agent** en controleer of de waarde van de IoT Edge versie 1.2 is. Zo niet, werk deze dan bij:

   ```toml
   [agent.config]
   image: "mcr.microsoft.com/azureiotedge-agent:1.2"
   ```

1. Zoek de **sectie Edge CA-certificaat** in het configuratiebestand. Decomment met de regels in deze sectie en geef de bestands-URI-paden voor het certificaat en de sleutelbestanden op de IoT Edge apparaat.

   ```toml
   [edge_ca]
   cert = "file:///<path>/<device CA cert>"
   pk = "file:///<path>/<device CA key>"
   ```

1. Sla ( `Ctrl+O` ) op en sluit ( ) het `Ctrl+X` configuratiebestand.

1. Als u al eerder andere certificaten voor IoT Edge hebt gebruikt, verwijdert u de bestanden in de volgende twee mappen om ervoor te zorgen dat uw nieuwe certificaten worden toegepast:

   * `/var/lib/aziot/certd/certs`
   * `/var/lib/aziot/keyd/keys`

1. Pas de wijzigingen toe.

   ```bash
   sudo iotedge config apply
   ```

1. Controleer op fouten in de configuratie.

   ```bash
   sudo iotedge check --verbose
   ```

   >[!TIP]
   >Het IoT Edge gebruikt een container om een deel van de diagnostische controle uit te voeren. Als u dit hulpprogramma wilt gebruiken op downstream-IoT Edge apparaten, moet u ervoor zorgen dat ze toegang hebben tot of de containerafbeelding in uw `mcr.microsoft.com/azureiotedge-diagnostics:latest` privécontainerregister hebben.

## <a name="network-isolate-downstream-devices"></a>Downstreamapparaten isoleren in het netwerk

Met de stappen die tot nu toe in dit artikel zijn IoT Edge ingesteld als een gateway of een downstreamapparaat, en wordt er een vertrouwde verbinding tussen gemaakt. Het gatewayapparaat verwerkt interacties tussen het downstreamapparaat en IoT Hub, waaronder verificatie en berichtroutering. Modules die zijn geïmplementeerd op IoT Edge kunnen nog steeds hun eigen verbindingen met cloudservices maken.

Sommige netwerkarchitecten, zoals de architecturen die voldoen aan de ISA-95-standaard, proberen het aantal internetverbindingen te minimaliseren. In deze scenario's kunt u downstreamapparaten IoT Edge zonder directe internetverbinding. Naast routering IoT Hub communicatie via hun gatewayapparaat, kunnen downstream IoT Edge apparaten voor alle cloudverbindingen afhankelijk zijn van het gatewayapparaat.

Deze netwerkconfiguratie vereist dat alleen IoT Edge apparaat in de bovenste laag van een gatewayhiërarchie directe verbindingen met de cloud heeft. IoT Edge apparaten in de lagere lagen kunnen alleen communiceren met hun bovenliggende apparaat of met alle apparaten met kinderen. Speciale modules op de gatewayapparaten maken dit scenario mogelijk, waaronder:

* De **API-proxymodule** is vereist op elke IoT Edge gateway met een IoT Edge apparaat eronder. Dit betekent dat deze zich op elke *laag van een* gatewayhiërarchie moet, behalve de onderste laag. In deze module wordt een [omgekeerde nginx-proxy](https://nginx.org) gebruikt om HTTP-gegevens via netwerklagen via één poort te routeer. Het is zeer goed te configureren via de module-dubbel en omgevingsvariabelen, dus kan worden aangepast aan de vereisten voor uw gatewayscenario.

* De **Docker-registermodule** kan worden geïmplementeerd op de IoT Edge gateway in de *bovenste laag* van een gatewayhiërarchie. Deze module is verantwoordelijk voor het ophalen en in de caching van containerafbeeldingen namens alle IoT Edge in lagere lagen. Het alternatief voor het implementeren van deze module in de bovenste laag is het gebruik van een lokaal register, of het handmatig laden van containerafbeeldingen op apparaten en het pull-beleid van de module instellen op **nooit.**

* De **Azure Blob Storage op IoT Edge** kunnen worden geïmplementeerd op de IoT Edge-gateway in de bovenste *laag* van een gatewayhiërarchie. Deze module is verantwoordelijk voor het uploaden van blobs namens alle IoT Edge apparaten in lagere lagen. De mogelijkheid om blobs te uploaden maakt ook nuttige functionaliteit voor probleemoplossing mogelijk voor IoT Edge-apparaten in lagere lagen, zoals het uploaden van modulelogboek en het ondersteunen van bundelupload.

### <a name="network-configuration"></a>Netwerkconfiguratie

Voor elk gatewayapparaat in de bovenste laag moeten netwerkoperators het volgende doen:

* Geef een statisch IP-adres of een FQDN (Fully Qualified Domain Name) op.
* Sta uitgaande communicatie van dit IP-adres toe aan Azure IoT Hub hostnaam via de poorten 443 (HTTPS) en 5671 (AMQP).
* Geef uitgaande communicatie van dit IP-adres aan uw Azure Container Registry hostnaam via poort 443 (HTTPS).

  De API-proxymodule kan alleen verbindingen met één containerregister tegelijk verwerken. We raden u aan om alle containerafbeeldingen, inclusief de openbare afbeeldingen van Microsoft Container Registry (mcr.microsoft.com), op te slaan in uw persoonlijke containerregister.

Voor elk gatewayapparaat in een lagere laag moeten netwerkoperators het volgende doen:

* Geef een statisch IP-adres op.
* Sta uitgaande communicatie van dit IP-adres toe aan het IP-adres van de bovenliggende gateway via de poorten 443 (HTTPS) en 5671 (AMQP).

### <a name="deploy-modules-to-top-layer-devices"></a>Modules implementeren op apparaten in de bovenste laag

Het IoT Edge-apparaat in de bovenste laag van een gatewayhiërarchie bevat een set vereiste modules die erop moeten worden geïmplementeerd, naast de workloadmodules die u op het apparaat kunt uitvoeren.

De API-proxymodule is ontworpen om te worden aangepast voor het afhandelen van de meest voorkomende gatewayscenario's. In dit artikel vindt u een voorbeeld van het instellen van de modules in een basisconfiguratie. Raadpleeg Configure [the API proxy module for your gateway hierarchy scenario (De API-proxymodule configureren voor uw gatewayhiërarchiescenario)](how-to-configure-api-proxy-module.md) voor meer gedetailleerde informatie en voorbeelden.

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. [Navigeer in Azure Portal](https://portal.azure.com)naar uw IoT-hub.
1. Selecteer **IoT Edge** in het navigatiemenu.
1. Selecteer het apparaat in de bovenste laag dat u configureert in de lijst **met IoT Edge apparaten.**
1. Selecteer **Modules instellen**.
1. Selecteer in **IoT Edge module** de optie **Toevoegen** en kies vervolgens **Marketplace-module**.
1. Zoek en selecteer de module **IoT Edge API Proxy.**
1. Selecteer de naam van de API-proxymodule in de lijst met geïmplementeerde modules en werk de volgende module-instellingen bij:
   1. Werk op **het tabblad Omgevingsvariabelen** de waarde **van** NGINX_DEFAULT_PORT bij naar `443` .
   1. Werk op **het tabblad Opties voor container** maken de poortbindingen bij om te verwijzen naar poort 443.

      ```json
      {
        "HostConfig": {
          "PortBindings": {
            "443/tcp": [
              {
                "HostPort": "443"
              }
            ]
          }
        }
      }
      ```

   Deze wijzigingen configureren de API-proxymodule om te luisteren op poort 443. Als u poortbindingsbots wilt voorkomen, moet u de edgeHub-module zo configureren dat deze niet luistert op poort 443. In plaats daarvan routeert de API-proxymodule elk edgeHub-verkeer op poort 443.

1. Selecteer **Runtime-instellingen** en zoek de opties voor het maken van de edgeHub-module. Verwijder de poortbinding voor poort 443 en laat de bindingen voor de poorten 5671 en 8883 staan.

   ```json
   {
     "HostConfig": {
       "PortBindings": {
         "5671/tcp": [
           {
             "HostPort": "5671"
           }
         ],
         "8883/tcp": [
           {
             "HostPort": "8883"
           }
         ]
       }
     }
   }
   ```

1. Selecteer **Opslaan om** uw wijzigingen in de runtime-instellingen op te slaan.
1. Selecteer **opnieuw** Toevoegen en kies vervolgens **IoT Edge module**.
1. Geef de volgende waarden op om de Docker-registermodule toe te voegen aan uw implementatie:
   1. **IoT Edge modulenaam**: `registry`
   1. Op het **tabblad Module-instellingen,** **Afbeeldings-URI:**`registry:latest`
   1. Voeg op **het tabblad Omgevingsvariabelen** de volgende omgevingsvariabelen toe:

      * **Naam:** `REGISTRY_PROXY_REMOTEURL` **waarde:** de URL voor het containerregister aan wie u deze registermodule wilt toevoegen. Bijvoorbeeld `https://myregistry.azurecr`.

        De registermodule kan slechts aan één containerregister worden toe te staan. Daarom raden we u aan om alle containerafbeeldingen in één privécontainerregister te hebben.

      * **Naam:** `REGISTRY_PROXY_USERNAME` **waarde:** gebruikersnaam voor verificatie bij het containerregister.

      * **Naam:** `REGISTRY_PROXY_PASSWORD` **waarde:** wachtwoord voor verificatie bij het containerregister.

   1. Plak het **volgende op het tabblad Opties** voor container maken:

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

1. Selecteer **Toevoegen om** de module toe te voegen aan de implementatie.
1. Selecteer **Volgende: Routes om** naar de volgende stap te gaan.
1. Als u wilt dat apparaat-naar-cloud-berichten van downstreamapparaten de IoT Hub bereiken, moet u een route opnemen die alle berichten door geeft aan IoT Hub. Bijvoorbeeld:
    1. **Naam**: `Route`
    1. **Waarde**: `FROM /messages/* INTO $upstream`
1. Selecteer **Beoordelen en maken om** naar de laatste stap te gaan.
1. Selecteer **Maken om** op uw apparaat te implementeren.

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

1. Maak in [Azure Cloud Shell](https://shell.azure.com/)een JSON-implementatiebestand. Bijvoorbeeld:

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
                                   "value": "The URL for the container registry you want this registry module to map to. For example, https://myregistry.azurecr"
                               },
                               "REGISTRY_PROXY_USERNAME": {
                                   "value": "Username to authenticate to the container registry."
                               },
                               "REGISTRY_PROXY_PASSWORD": {
                                   "value": "Password to authenticate to the container registry."
                               }
                           },
                           "status": "running",
                           "restartPolicy": "always"
                       },
                       "IoTEdgeAPIProxy": {
                           "settings": {
                               "image": "mcr.microsoft.com/azureiotedge-api-proxy:1.0",
                               "createOptions": "{\"HostConfig\": {\"PortBindings\": {\"443/tcp\": [{\"HostPort\":\"443\"}]}}}"
                           },
                           "type": "docker",
                           "env": {
                               "NGINX_DEFAULT_PORT": {
                                   "value": "443"
                               },
                               "DOCKER_REQUEST_ROUTE_ADDRESS": {
                                   "value": "registry:5000"
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
                               "image": "mcr.microsoft.com/azureiotedge-agent:1.2",
                               "createOptions": ""
                           },
                           "type": "docker"
                       },
                       "edgeHub": {
                           "settings": {
                               "image": "mcr.microsoft.com/azureiotedge-hub:1.2",
                               "createOptions": "{\"HostConfig\":{\"PortBindings\":{\"5671/tcp\":[{\"HostPort\":\"5671\"}],\"8883/tcp\":[{\"HostPort\":\"8883\"}]}}}"
                           },
                           "type": "docker",
                           "env": {},
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

   Dit implementatiebestand configureert de API-proxymodule om te luisteren op poort 443. Om poortbindingsbots te voorkomen, configureert het bestand de edgeHub-module zodanig dat deze niet luistert op poort 443. In plaats daarvan routeert de API-proxymodule elk edgeHub-verkeer op poort 443.

1. Voer de volgende opdracht in om een implementatie op een IoT Edge maken:

   ```bash
   az iot edge set-modules --device-id <device_id> --hub-name <iot_hub_name> --content ./<deployment_file_name>.json
   ```

---

### <a name="deploy-modules-to-lower-layer-devices"></a>Modules implementeren op apparaten met een lagere laag

IoT Edge apparaten in lagere lagen van een gatewayhiërarchie hebben één vereiste module die erop moet worden geïmplementeerd, naast de workloadmodules die u op het apparaat kunt uitvoeren.

#### <a name="route-container-image-pulls"></a>Pulls van containerafbeeldingen door een route

Voordat u de vereiste proxymodule voor IoT Edge-apparaten in gatewayhiërarchieën bespreekt, is het belangrijk om te begrijpen hoe IoT Edge apparaten in lagere lagen hun module-afbeeldingen kunnen krijgen.

Als uw apparaten met een lagere laag geen verbinding kunnen maken met de cloud, maar u wilt dat ze zoals gebruikelijk module-afbeeldingen opvragen, moet het apparaat op de bovenste laag van de gatewayhiërarchie worden geconfigureerd voor het afhandelen van deze aanvragen. Op het apparaat op de bovenste laag moet een Docker-registermodule **worden** uitgevoerd die is toe te staan aan uw containerregister. Configureer vervolgens de API-proxymodule om containeraanvragen naar de module te sturen. Deze details worden besproken in de eerdere secties van dit artikel. In deze configuratie moeten de apparaten met een lagere laag niet naar cloudcontainerregisters wijzen, maar naar het register dat wordt uitgevoerd in de bovenste laag.

In plaats van aan te roepen, moeten `mcr.microsoft.com/azureiotedge-api-proxy:1.0` apparaten met een lagere laag bijvoorbeeld aanroepen. `$upstream:443/azureiotedge-api-proxy:1.0`

De **parameter $upstream** wijst naar het bovenliggende apparaat van een apparaat met een lagere laag, zodat de aanvraag door alle lagen wordt geroutering totdat de bovenste laag wordt bereikt. Deze heeft een proxyomgeving die containeraanvragen naar de registermodule routert. De poort in dit voorbeeld moet worden vervangen door de poort waarop de `:443` API-proxymodule op het bovenliggende apparaat luistert.

De API-proxymodule kan slechts naar één registermodule worden gerouteerd en elke registermodule kan slechts aan één containerregister worden toe te kennen. Daarom moeten alle afbeeldingen die apparaten met een lagere laag moeten pullen, worden opgeslagen in één containerregister.

Als u niet wilt dat apparaten met een lagere laag pull-aanvragen voor modules maken via een gatewayhiërarchie, kunt u ook een lokale registeroplossing beheren. Of push de module-installatie afbeeldingen naar de apparaten voordat u implementaties maakt en stel **imagePullPolicy in** op **nooit.**

#### <a name="bootstrap-the-iot-edge-agent"></a>Bootstrap van de IoT Edge agent

De IoT Edge-agent is het eerste runtimeonderdeel dat op elk apparaat IoT Edge starten. U moet ervoor zorgen dat downstreamapparaten IoT Edge toegang hebben tot de edgeAgent-moduleafbeelding wanneer ze worden opstart. Vervolgens hebben ze toegang tot implementaties en kunnen ze de rest van de moduleafbeeldingen starten.

Wanneer u naar het configuratiebestand op een IoT Edge apparaat gaat om de verificatiegegevens, certificaten en bovenliggende hostnaam op te geven, moet u ook de containerafbeelding edgeAgent bijwerken.

Als het gatewayapparaat op het hoogste niveau is geconfigureerd voor het verwerken van aanvragen voor containerafbeeldingen, vervangt u door de bovenliggende hostnaam en `mcr.microsoft.com` api-proxy die naar de poort luisteren. In het implementatiemanifest kunt u gebruiken als snelkoppeling, maar hiervoor is de edgeHub-module vereist om routering af te handelen en is die `$upstream` module nog niet gestart. Bijvoorbeeld:

```toml
[agent]
name = "edgeAgent"
type = "docker"

[agent.config]
image: "{Parent FQDN or IP}:443/azureiotedge-agent:1.2"
```

Als u een lokaal containerregister gebruikt of de containerafbeeldingen handmatig op het apparaat op geeft, moet u het configuratiebestand dienovereenkomstig bijwerken.

#### <a name="configure-runtime-and-deploy-proxy-module"></a>Runtime configureren en proxymodule implementeren

De **API-proxymodule** is vereist voor het routeren van alle communicatie tussen de cloud en alle downstreamapparaten IoT Edge apparaten. Een IoT Edge apparaat in de onderste laag van de hiërarchie, zonder downstream IoT Edge apparaten, heeft deze module niet nodig.

De API-proxymodule is ontworpen om te worden aangepast voor het afhandelen van de meest voorkomende gatewayscenario's. In dit artikel worden de stappen voor het instellen van de modules in een basisconfiguratie kort beschreven. Raadpleeg De [API-proxymodule configureren voor uw gatewayhiërarchiescenario](how-to-configure-api-proxy-module.md) voor meer gedetailleerde informatie en voorbeelden.

1. [Navigeer in Azure Portal](https://portal.azure.com)naar uw IoT-hub.
1. Selecteer **IoT Edge** in het navigatiemenu.
1. Selecteer het apparaat met de lagere laag dat u configureert in de lijst **met IoT Edge apparaten.**
1. Selecteer **Modules instellen**.
1. Selecteer in **IoT Edge module** de optie **Toevoegen** en kies vervolgens **Marketplace-module**.
1. Zoek en selecteer de module **IoT Edge API Proxy.**
1. Selecteer de naam van de API-proxymodule in de lijst met geïmplementeerde modules en werk de volgende module-instellingen bij:
   1. Werk op **het tabblad Module-instellingen** de waarde van **Afbeeldings-URI bij.** Vervang `mcr.microsoft.com` door `$upstream:443`.
   1. Werk op **het tabblad Omgevingsvariabelen** de waarde **van** NGINX_DEFAULT_PORT bij naar `443` .
   1. Werk op **het tabblad Opties voor container** maken de poortbindingen bij om te verwijzen naar poort 443.

      ```json
      {
        "HostConfig": {
          "PortBindings": {
            "443/tcp": [
              {
                "HostPort": "443"
              }
            ]
          }
        }
      }
      ```

   Deze wijzigingen configureren de API-proxymodule om te luisteren op poort 443. Als u poortbindingsbots wilt voorkomen, moet u de edgeHub-module zo configureren dat deze niet luistert op poort 443. In plaats daarvan routeert de API-proxymodule elk edgeHub-verkeer op poort 443.

1. Selecteer **Runtime-instellingen.**
1. Werk de instellingen van de edgeHub-module bij:

   1. Vervang in **het** veld Afbeelding `mcr.microsoft.com` door `$upstream:443` .
   1. Verwijder in **het veld Opties** maken de poortbinding voor poort 443 en laat de bindingen voor de poorten 5671 en 8883 staan.

   ```json
   {
     "HostConfig": {
       "PortBindings": {
         "5671/tcp": [
           {
             "HostPort": "5671"
           }
         ],
         "8883/tcp": [
           {
             "HostPort": "8883"
           }
         ]
       }
     }
   }
   ```

1. Werk de instellingen van de edgeAgent-module bij:
   1. Vervang in **het** veld Afbeelding `mcr.microsoft.com` door `$upstream:443` .

1. Selecteer **Opslaan om** uw wijzigingen in de runtime-instellingen op te slaan.
1. Selecteer **Volgende: Routes** om naar de volgende stap te gaan.
1. Als u wilt dat apparaat-naar-cloud-berichten van downstreamapparaten de IoT Hub bereiken, moet u een route opnemen die alle berichten door geeft aan `$upstream` . De upstream-parameter wijst naar het bovenliggende apparaat in het geval van apparaten met een lagere laag. Bijvoorbeeld:
    1. **Naam**: `Route`
    1. **Waarde**: `FROM /messages/* INTO $upstream`
1. Selecteer **Beoordelen en maken om** naar de laatste stap te gaan.
1. Selecteer **Maken om** op uw apparaat te implementeren.

## <a name="next-steps"></a>Volgende stappen

[Hoe een IoT Edge-apparaat kan worden gebruikt als gateway](iot-edge-as-gateway.md)

[De API-proxymodule configureren voor uw gatewayhiërarchiescenario](how-to-configure-api-proxy-module.md)