---
title: Downstream IoT Edge apparaten verbinden-Azure IoT Edge | Microsoft Docs
description: Een IoT Edge apparaat configureren om verbinding te maken met Azure IoT Edge gateway apparaten.
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
ms.openlocfilehash: 70b3ed53747deb1f3bdc90de8fe71f42f8f7ce13
ms.sourcegitcommit: d63f15674f74d908f4017176f8eddf0283f3fac8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/07/2021
ms.locfileid: "106580496"
---
# <a name="connect-a-downstream-iot-edge-device-to-an-azure-iot-edge-gateway-preview"></a>Een downstream-IoT Edge apparaat verbinden met een Azure IoT Edge gateway (preview-versie)

[!INCLUDE [iot-edge-version-202011](../../includes/iot-edge-version-202011.md)]

Dit artikel bevat instructies voor het tot stand brengen van een vertrouwde verbinding tussen een IoT Edge gateway en een downstream IoT Edge apparaat.

>[!NOTE]
>Voor deze functie is IoT Edge versie 1.2, in openbare preview, met Linux-containers vereist.
>
>Dit artikel heeft betrekking op de nieuwste preview-versie van IoT Edge versie 1,2. Zorg ervoor dat versie [1.2.0-RC4](https://github.com/Azure/azure-iotedge/releases/tag/1.2.0-rc4) of hoger op uw apparaat wordt uitgevoerd. Zie voor stappen voor het verkrijgen van de nieuwste preview-versie op uw apparaat [installeren Azure IOT Edge voor Linux (versie 1,2)](how-to-install-iot-edge.md) of [Update IoT Edge naar versie 1,2](how-to-update-iot-edge.md#special-case-update-from-10-or-11-to-12).

In een gateway scenario kan een IoT Edge apparaat zowel een gateway als een downstream-apparaat zijn. Meerdere IoT Edge gateways kunnen worden gelaagd om een hiërarchie van apparaten te maken. De downstream-(of onderliggende) apparaten kunnen berichten verifiëren en verzenden of ontvangen via hun gateway-(of bovenliggende) apparaat.

Er zijn twee verschillende configuraties voor IoT Edge apparaten in een gateway-hiërarchie, en dit artikel heeft betrekking op beide. De eerste is de **bovenste laag** IOT edge apparaat. Wanneer meerdere IoT Edge apparaten verbinding maken via elkaar, wordt elk apparaat dat geen bovenliggend apparaat heeft maar rechtstreeks verbinding maakt met IoT Hub, gezien in de bovenste laag. Dit apparaat is verantwoordelijk voor het verwerken van aanvragen van alle onderliggende apparaten. De andere configuratie is van toepassing op elk IoT Edge apparaat in een **lagere laag** van de hiërarchie. Deze apparaten zijn mogelijk een gateway voor andere downstream IoT-en IoT Edge-apparaten, maar moeten ook communicatie via hun eigen bovenliggende apparaten routeren.

Sommige netwerk architecturen vereisen dat alleen het bovenste IoT Edge apparaat in een hiërarchie verbinding kan maken met de Cloud. In deze configuratie kunnen alle IoT Edge apparaten in lagere lagen van een hiërarchie alleen communiceren met hun gateway (of bovenliggend) apparaat en alle downstream (of onderliggende) apparaten.

Alle stappen in dit artikel zijn gebaseerd op die in [een IOT edge apparaat configureren om te fungeren als een transparante gateway](how-to-create-transparent-gateway.md), waarmee een IOT edge apparaat wordt ingesteld als gateway voor downstream IOT-apparaten. Dezelfde basis stappen zijn van toepassing op alle gateway scenario's:

* **Verificatie**: Maak IOT hub-identiteiten voor alle apparaten in de hiërarchie van de gateway.
* **Autorisatie**: Stel de bovenliggende/onderliggende relatie in IOT hub in om onderliggende apparaten te autoriseren om verbinding te maken met het bovenliggende apparaat, net zoals ze verbinding zouden maken met IOT hub.
* **Gateway detectie**: Controleer of het onderliggende apparaat het bovenliggende apparaat kan vinden in het lokale netwerk.
* **Beveiligde verbinding**: Stel een beveiligde verbinding in met vertrouwde certificaten die deel uitmaken van dezelfde keten.

## <a name="prerequisites"></a>Vereisten

* Een gratis of standaard IoT-hub.
* Ten minste twee **IOT edge apparaten**, een als het hoogste laag apparaat en een of meer apparaten met een lagere laag. Als IoT Edge apparaten niet beschikbaar zijn, kunt u [Azure IOT Edge uitvoeren op virtuele Ubuntu-machines](how-to-install-iot-edge-ubuntuvm.md).
* Als u de Azure CLI gebruikt om apparaten te maken en te beheren, moet u Azure CLI v 2.3.1 met de Azure IoT-extensie v 0.10.6 of hoger hebben geïnstalleerd.

In dit artikel vindt u gedetailleerde stappen en opties om u te helpen de juiste gateway hiërarchie te maken voor uw scenario. Zie [een hiërarchie van IOT edge apparaten met behulp van gateways maken](tutorial-nested-iot-edge.md)voor een begeleide zelf studie.

## <a name="create-a-gateway-hierarchy"></a>Een gateway hiërarchie maken

U maakt een gateway hiërarchie IoT Edge door bovenliggende/onderliggende relaties te definiëren voor de IoT Edge apparaten in het scenario. U kunt een bovenliggend apparaat instellen wanneer u een nieuwe apparaat-id maakt, of u kunt de bovenliggende en onderliggende items van een bestaande apparaat-id beheren.

Met de stap voor het instellen van bovenliggende/onderliggende relaties worden onderliggende apparaten gemachtigd om verbinding te maken met het bovenliggende apparaat, alsof ze verbinding maken met IoT Hub.

Alleen IoT Edge apparaten kunnen bovenliggende apparaten zijn, maar zowel IoT Edge apparaten als IoT-apparaten kunnen kinderen zijn. Een bovenliggend item kan veel onderliggende items hebben, maar een onderliggend item kan slechts één bovenliggend item hebben. Een gateway hiërarchie wordt gemaakt door bovenliggende/onderliggende sets samen te koppelen, zodat het onderliggende item van één apparaat het bovenliggende item van een ander is.

<!-- TODO: graphic of gateway hierarchy -->

# <a name="portal"></a>[Portal](#tab/azure-portal)

In de Azure Portal kunt u de relatie bovenliggend/onderliggend item beheren wanneer u nieuwe apparaat-id's maakt of door bestaande apparaten te bewerken.

Wanneer u een nieuw IoT Edge apparaat maakt, hebt u de optie om bovenliggende en onderliggende apparaten te kiezen in de lijst met bestaande IoT Edge apparaten in die hub.

1. Navigeer in het [Azure Portal](https://portal.azure.com)naar uw IOT-hub.
1. Selecteer **IOT Edge** in het navigatie menu.
1. Selecteer **een IOT edge apparaat toevoegen**.
1. Naast het instellen van de apparaat-ID en verificatie-instellingen, kunt u **een bovenliggend apparaat instellen** of **onderliggende apparaten kiezen**.
1. Kies het apparaat of de apparaten die u als bovenliggend of onderliggend element wilt instellen.

U kunt ook relaties tussen bovenliggende en onderliggende items maken of beheren voor bestaande apparaten.

1. Navigeer in het [Azure Portal](https://portal.azure.com)naar uw IOT-hub.
1. Selecteer **IOT Edge** in het navigatie menu.
1. Selecteer het apparaat dat u wilt beheren in de lijst met **IOT edge apparaten**.
1. Selecteer **een bovenliggend apparaat instellen** of **onderliggende apparaten beheren**.
1. Voeg alle bovenliggende of onderliggende apparaten toe of verwijder deze.

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

De [Azure IOT-](/cli/azure/ext/azure-iot) extensie voor Azure cli bevat opdrachten voor het beheren van uw IOT-resources. U kunt de bovenliggende/onderliggende relatie van IoT-en IoT Edge-apparaten beheren wanneer u nieuwe apparaat-id's maakt of bestaande apparaten bewerkt.

Met de opdracht [AZ IOT hub Device-Identity](/cli/azure/ext/azure-iot/iot/hub/device-identity) set van opdrachten kunt u de bovenliggende/onderliggende relaties voor een bepaald apparaat beheren.

De `create` opdracht bevat para meters voor het toevoegen van onderliggende apparaten en het instellen van een bovenliggend apparaat op het moment dat het apparaat wordt gemaakt.

Met extra apparaat-id-opdrachten, waaronder `add-children` , `list-children` , en `remove-children` of `get-parent` `set-parent` , kunt u de relaties tussen bovenliggende en onderliggende items beheren voor bestaande apparaten.

---

## <a name="prepare-certificates"></a>Certificaten voorbereiden

Een consistente keten van certificaten moet worden geïnstalleerd op alle apparaten in dezelfde gateway hiërarchie om een beveiligde communicatie tussen zichzelf tot stand te brengen. Elk apparaat in de hiërarchie, of een IoT Edge apparaat of een IoT-Leaf-apparaat, een kopie van hetzelfde basis-CA-certificaat nodig heeft. Elk IoT Edge apparaat in de hiërarchie gebruikt dat basis-CA-certificaat als basis voor het CA-certificaat van het apparaat.

Met deze installatie kan elk downstream-IoT Edge apparaat of IoT-blad apparaat de identiteit van het bovenliggende item verifiëren door te controleren of de edgeHub waarmee ze verbinding maken een server certificaat heeft dat is ondertekend door het gedeelde basis-CA-certificaat.

<!-- TODO: certificate graphic -->

Maak de volgende certificaten:

* Een **basis-CA-certificaat**, dat het bovenste gedeelde certificaat is voor alle apparaten in een bepaalde gateway hiërarchie. Dit certificaat is geïnstalleerd op alle apparaten.
* Alle **tussenliggende certificaten** die u wilt toevoegen aan de basis certificaat keten.
* Een **CA-certificaat** van het apparaat en de bijbehorende **persoonlijke sleutel**, gegenereerd door de basis-en tussenliggende certificaten. U hebt één uniek CA-certificaat voor apparaten nodig voor elk IoT Edge apparaat in de gateway-hiërarchie.

U kunt een zelfondertekende certificerings instantie gebruiken of een certificaat aanschaffen bij een vertrouwde commerciële certificerings instantie, zoals Baltimore, VeriSign, Digicert of GlobalSign.

Als u geen eigen certificaten hebt om te gebruiken, kunt u [demo certificaten maken om IOT Edge apparaatfuncties te testen](how-to-create-test-certificates.md). Volg de stappen in dat artikel om één set basis-en tussen certificaten te maken en vervolgens IoT Edge voor elk apparaat een CA-certificaat voor apparaten te maken.

## <a name="configure-iot-edge-on-devices"></a>IoT Edge op apparaten configureren

De stappen voor het instellen van IoT Edge als een gateway zijn vergelijkbaar met de stappen voor het instellen van IoT Edge als een downstream-apparaat.

Om gateway detectie in te scha kelen, moet elk IoT Edge gateway apparaat worden geconfigureerd met een **hostnaam** die de onderliggende apparaten gebruiken om het te vinden op het lokale netwerk. Elk downstream-IoT Edge apparaat moet worden geconfigureerd met een **parent_hostname** om verbinding te maken. Als één IoT Edge apparaat zowel een bovenliggend als een onderliggend apparaat is, moeten beide para meters worden opgegeven.

Om beveiligde verbindingen in te scha kelen, moet elk IoT Edge apparaat in een gateway scenario worden geconfigureerd met een uniek CA-certificaat voor het apparaat en een kopie van het basis-CA-certificaat dat wordt gedeeld door alle apparaten in de gateway hiërarchie.

U moet IoT Edge al op uw apparaat hebben geïnstalleerd. Als dat niet het geval is, volgt u de stappen voor het [registreren van een IOT edge apparaat in IOT hub](how-to-register-device.md) en installeert u vervolgens [de Azure IOT Edge runtime](how-to-install-iot-edge.md).

De stappen in deze sectie verwijzen naar het **basis-CA-certificaat** en de **certificerings instantie van het apparaat en de persoonlijke sleutel** die eerder in dit artikel zijn besproken. Als u deze certificaten op een ander apparaat hebt gemaakt, moet u deze op dit apparaat beschikbaar hebben. U kunt de bestanden fysiek overdragen, zoals met een USB-station, met een service zoals [Azure Key Vault](../key-vault/general/overview.md), of met een functie zoals een [beveiligde bestands kopie](https://www.ssh.com/ssh/scp/).

Gebruik de volgende stappen om IoT Edge op het apparaat te configureren.

Zorg ervoor dat de gebruiker **iotedge** Lees machtigingen heeft voor de directory met de certificaten en sleutels.

1. Installeer het **basis-CA-certificaat** op dit IOT edge apparaat.

   ```bash
   sudo cp <path>/<root ca certificate>.pem /usr/local/share/ca-certificates/<root ca certificate>.pem.crt
   ```

1. Werk het certificaat archief bij.

   ```bash
   sudo update-ca-certificates
   ```

   Met deze opdracht moet worden uitgevoerd dat er één certificaat is toegevoegd aan/etc/ssl/certs.

1. Open het IoT Edge-configuratiebestand.

   ```bash
   sudo nano /etc/aziot/config.toml
   ```

   >[!TIP]
   >Als het configuratie bestand nog niet op uw apparaat aanwezig is, gebruikt `/etc/aziot/config.toml.edge.template` u als sjabloon om er een te maken.

1. Zoek de sectie **hostname** in het configuratie bestand. Verwijder de opmerking over de regel die de `hostname` para meter bevat en werk de waarde bij om de Fully Qualified Domain Name (FQDN) of het IP-adres van het IOT edge apparaat.

   De waarde van deze para meter is wat downstream-apparaten gebruiken om verbinding te maken met deze gateway. De hostnaam krijgt standaard de computer naam, maar de FQDN of het IP-adres is vereist om downstream-apparaten te verbinden.

   Gebruik een hostnaam die korter is dan 64 tekens. Dit is de teken limiet voor een algemene naam voor een server certificaat.

   Consistent zijn met het hostname-patroon in een gateway-hiërarchie. Gebruik FQDN-of IP-adressen, maar niet beide.

1. *Als dit apparaat een onderliggend apparaat is*, gaat u naar de sectie **bovenliggende hostnaam** . Verwijder de opmerking en werk de `parent_hostname` para meter bij naar de FQDN of het IP-adres van het bovenliggende apparaat, die overeenkomt met wat er is gegeven als de hostnaam in het configuratie bestand van het bovenliggende apparaat.

1. Zoek het gedeelte **Trust bundel CERT** . Verwijder de opmerking en werk de `trust_bundle_cert` para meter bij met de bestands-URI naar het basis-CA-certificaat op uw apparaat.

1. Hoewel deze functie in de open bare preview is, moet u uw IoT Edge-apparaat configureren om de open bare preview-versie van de IoT Edge agent te gebruiken wanneer deze wordt gestart.

   Zoek de sectie **standaard rand agent** en werk de afbeeldings waarde bij naar de open bare voorbeeld installatie kopie:

   ```toml
   [agent.config]
   image: "mcr.microsoft.com/azureiotedge-agent:1.2.0-rc4"
   ```

1. Zoek het gedeelte **CA-certificaat** voor de rand in het configuratie bestand. Verwijder de opmerkingen in de regels in deze sectie en geef de bestands-URI-paden op voor het certificaat en de sleutel bestanden op het IoT Edge-apparaat.

   ```toml
   [edge_ca]
   cert = "file:///<path>/<device CA cert>"
   pk = "file:///<path>/<device CA key>"
   ```

1. Sla `Ctrl+O` het configuratie bestand op () en sluit ( `Ctrl+X` ).

1. Als u eerder andere certificaten voor IoT Edge hebt gebruikt, verwijdert u de bestanden in de volgende twee mappen om er zeker van te zijn dat uw nieuwe certificaten worden toegepast:

   * `/var/lib/aziot/certd/certs`
   * `/var/lib/aziot/keyd/keys`

1. Pas de wijzigingen toe.

   ```bash
   sudo iotedge config apply
   ```

1. Controleer of er fouten zijn opgetreden in de configuratie.

   ```bash
   sudo iotedge check --verbose
   ```

   >[!TIP]
   >Het hulp programma IoT Edge controle maakt gebruik van een container voor het uitvoeren van een deel van de diagnostische controle. Als u dit hulp programma wilt gebruiken op downstream IoT Edge-apparaten, moet u ervoor zorgen dat ze toegang hebben `mcr.microsoft.com/azureiotedge-diagnostics:latest` of de container installatie kopie hebben in het persoonlijke container register.

## <a name="configure-runtime-modules-for-public-preview"></a>Runtime modules voor open bare preview configureren

Hoewel deze functie in de open bare preview is, moet u uw IoT Edge-apparaat configureren voor het gebruik van de open bare Preview-versies van de IoT Edge runtime-modules. De vorige sectie bevat stappen voor het configureren van edgeAgent bij het opstarten. U moet ook de runtime modules configureren in implementaties voor uw apparaat.

1. De edgeHub-module configureren voor het gebruik van de open bare voorbeeld installatie kopie: `mcr.microsoft.com/azureiotedge-hub:1.2.0-rc4` .

1. Configureer de volgende omgevings variabelen voor de module edgeHub:

   | Name | Waarde |
   | - | - |
   | `experimentalFeatures__enabled` | `true` |
   | `experimentalFeatures__nestedEdgeEnabled` | `true` |

1. De edgeAgent-module configureren voor het gebruik van de open bare voorbeeld installatie kopie: `mcr.microsoft.com/azureiotedge-hub:1.2.0-rc4` .

## <a name="network-isolate-downstream-devices"></a>Downstream-apparaten netwerk isoleren

De stappen tot nu toe in dit artikel stellen IoT Edge-apparaten in als een gateway of een downstream-apparaat en maken hiervoor een vertrouwde verbinding. Het gateway apparaat verwerkt de interacties tussen het downstream-apparaat en de IoT Hub, inclusief verificatie en het routeren van berichten. Modules die zijn geïmplementeerd op downstream IoT Edge apparaten kunnen nog steeds hun eigen verbindingen met Cloud Services maken.

Sommige netwerk architecturen, zoals die van de ISA-95-standaard, zoeken om het aantal Internet verbindingen te minimaliseren. In deze scenario's kunt u downstream-IoT Edge apparaten configureren zonder rechtstreekse Internet verbinding. Downstream IoT Edge-apparaten kunnen afhankelijk zijn van de route ring van IoT Hub communicatie via hun gateway apparaat.

Deze netwerk configuratie vereist dat alleen het IoT Edge apparaat in de bovenste laag van een gateway hiërarchie directe verbindingen met de Cloud heeft. IoT Edge apparaten in de lagere lagen kunnen alleen communiceren met hun bovenliggende apparaat of van onderliggende apparaten. Speciale modules op de gateway apparaten maken dit scenario mogelijk, waaronder:

* De **API-proxy module** is vereist op een IOT Edge gateway met een ander IOT edge apparaat daaronder. Dit betekent dat het moet op *elke laag* van een gateway hiërarchie, met uitzonde ring van de onderste laag. Deze module maakt gebruik van een reverse proxy voor [nginx](https://nginx.org) om http-gegevens via netwerk lagen via één poort te routeren. Het kan zeer worden geconfigureerd via de bijbehorende module dubbele en omgevings variabelen, dus kan worden aangepast aan de vereisten van uw Gateway scenario.

* De **docker-register module** kan worden geïmplementeerd op de IOT Edge-gateway in de *bovenste laag* van een gateway-hiërarchie. Deze module is verantwoordelijk voor het ophalen en caching van container installatie kopieën namens alle IoT Edge apparaten in lagere lagen. Het alternatief voor het implementeren van deze module in de bovenste laag is het gebruik van een lokaal REGI ster of het hand matig laden van container installatie kopieën op apparaten, en het pull-beleid van de module instellen op **nooit**.

* De **Azure Blob Storage op IOT Edge** kan worden geïmplementeerd op de IOT Edge gateway in de *bovenste laag* van een gateway-hiërarchie. Deze module is verantwoordelijk voor het uploaden van blobs namens alle IoT Edge apparaten in lagere lagen. De mogelijkheid om blobs te uploaden biedt ook nuttige probleemoplossings functionaliteit voor IoT Edge apparaten in lagere lagen, zoals het uploaden van het module logboek en het uploaden van de ondersteunings bundel.

### <a name="network-configuration"></a>Netwerkconfiguratie

Voor elk gateway apparaat in de bovenste laag moeten netwerk exploitanten het volgende doen:

* Geef een statisch IP-adres of een Fully Qualified Domain Name (FQDN) op.
* Autoriseer uitgaande berichten van dit IP-adres aan uw Azure IoT Hub hostname via de poorten 443 (HTTPS) en 5671 (AMQP).
* Autoriseer uitgaande berichten van dit IP-adres aan uw Azure Container Registry hostnaam via poort 443 (HTTPS).

  De API-proxy module kan alleen verbindingen met één container register tegelijk verwerken. We raden u aan alle container installatie kopieën, inclusief de open bare installatie kopieën die worden geleverd door micro soft Container Registry (mcr.microsoft.com), op te slaan in uw persoonlijke container register.

Voor elk gateway apparaat in een lagere laag moeten netwerk exploitanten het volgende doen:

* Geef een statisch IP-adres op.
* Autoriseer uitgaande berichten van dit IP-adres naar het IP-adres van de bovenliggende gateway via poort 443 (HTTPS) en 5671 (AMQP).

### <a name="deploy-modules-to-top-layer-devices"></a>Modules implementeren op apparaten in de bovenste laag

Het IoT Edge apparaat in de bovenste laag van een gateway hiërarchie heeft een set vereiste modules die erop moeten worden geïmplementeerd, naast eventuele modules die u op het apparaat kunt uitvoeren.

De API-proxy module is ontworpen om te worden aangepast voor het verwerken van de meest voorkomende gateway scenario's. In dit artikel vindt u een voor beeld van het instellen van de modules in een basis configuratie. Raadpleeg [de API-proxy module voor uw gateway hiërarchie scenario configureren](how-to-configure-api-proxy-module.md) voor meer gedetailleerde informatie en voor beelden.

1. Navigeer in het [Azure Portal](https://portal.azure.com)naar uw IOT-hub.
1. Selecteer **IOT Edge** in het navigatie menu.
1. Selecteer het apparaat van de bovenste laag dat u configureert in de lijst met **IOT edge apparaten**.
1. Selecteer **Modules instellen**.
1. Selecteer in de sectie **IOT Edge modules** **toevoegen** en kies vervolgens **Marketplace-module**.
1. Zoek en selecteer de proxy module voor de **IOT Edge-API** .
1. Selecteer de naam van de API-proxy module in de lijst met geïmplementeerde modules en werk de volgende module-instellingen bij:
   1. Werk op het tabblad **omgevings variabelen** de waarde van **NGINX_DEFAULT_PORT** naar `443` .
   1. Werk op het tabblad Opties voor het maken van de **container** de poort bindingen bij naar referentie poort 443.

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

   Met deze wijzigingen wordt de API-proxy module geconfigureerd om te Luis teren op poort 443. Om conflicten met poort bindingen te voor komen, moet u de edgeHub-module zo configureren dat deze niet luistert op poort 443. In plaats daarvan stuurt de API-proxy module elk edgeHub-verkeer op poort 443.

1. Selecteer **runtime-instellingen** en zoek de edgeHub-module maken opties. Verwijder de poort binding voor poort 443 en laat de bindingen voor de poorten 5671 en 8883 staan.

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

1. Selecteer **Opslaan** om uw wijzigingen in de runtime-instellingen op te slaan.
1. Selecteer opnieuw **toevoegen** en kies vervolgens **IOT Edge module**.
1. Geef de volgende waarden op om de docker-register module toe te voegen aan uw-implementatie:
   1. **Module naam IOT Edge**: `registry`
   1. Op het tabblad **module-instellingen** , **afbeeldings-URI**: `registry:latest`
   1. Voeg op het tabblad **omgevings variabelen** de volgende omgevings variabelen toe:

      * **Naam**: `REGISTRY_PROXY_REMOTEURL` **waarde**: de URL voor het container register waarnaar u deze register module wilt toewijzen. Bijvoorbeeld `https://myregistry.azurecr`.

        De register module kan alleen worden toegewezen aan één container register, dus u kunt het beste alle container installatie kopieën in één persoonlijk container register.

      * **Naam**: `REGISTRY_PROXY_USERNAME` **waarde**: gebruikers naam voor verificatie bij het container register.

      * **Name**: `REGISTRY_PROXY_PASSWORD` **Value**: wacht woord voor verificatie bij het container register.

   1. Plak op het tabblad Opties voor het maken van de **container** :

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

1. Selecteer **toevoegen** om de module toe te voegen aan de implementatie.
1. Selecteer **volgende: routes** om naar de volgende stap te gaan.
1. Als u apparaat-naar-Cloud-berichten wilt inschakelen vanaf downstream-apparaten om IoT Hub te bereiken, neemt u een route op waarmee alle berichten worden door gegeven aan IoT Hub. Bijvoorbeeld:
    1. **Naam**: `Route`
    1. **Waarde**: `FROM /messages/* INTO $upstream`
1. Selecteer **controleren + maken** om naar de laatste stap te gaan.
1. Selecteer **maken** om te implementeren op het apparaat.

### <a name="deploy-modules-to-lower-layer-devices"></a>Modules implementeren op apparaten met een lagere laag

IoT Edge apparaten in lagere lagen van een gateway hiërarchie een vereiste module hebben die moet worden geïmplementeerd, naast de werkbelasting modules die u op het apparaat kunt uitvoeren.

#### <a name="route-container-image-pulls"></a>Pull-bewerkingen van container installatie kopie

Voordat u de vereiste proxy module voor IoT Edge apparaten in Gateway-hiërarchieën bespreekt, is het belang rijk om te begrijpen hoe IoT Edge apparaten in lagere lagen hun module-installatie kopieën ophalen.

Als uw lagere laag apparaten geen verbinding kunnen maken met de Cloud, maar u wilt dat ze de module installatie kopieën laten weer geven, moet het apparaat voor de bovenste laag van de gateway hiërarchie worden geconfigureerd om deze aanvragen af te handelen. Het apparaat voor de bovenste laag moet een docker- **register** module uitvoeren die is toegewezen aan uw container register. Configureer vervolgens de API-proxy module zo dat er container aanvragen worden gerouteerd. Deze details worden besproken in de eerdere secties van dit artikel. In deze configuratie moeten de apparaten in de lagere laag niet verwijzen naar de Cloud container registers, maar naar het REGI ster dat wordt uitgevoerd in de bovenste laag.

In plaats van de oproep `mcr.microsoft.com/azureiotedge-api-proxy:latest` kunnen apparaten met een lagere laag bijvoorbeeld worden aangeroepen `$upstream:443/azureiotedge-api-proxy:latest` .

De **$upstream** -para meter verwijst naar het bovenliggende apparaat van een lagere laag, zodat de aanvraag door alle lagen wordt doorgestuurd totdat de bovenste laag die een proxy omgeving heeft, een routerings container aanvragen naar de register module heeft bereikt. De `:443` poort in dit voor beeld moet worden vervangen door de poort waarop de API-proxy module op het bovenliggende apparaat wordt geluisterd.

De API-proxy module kan slechts naar één register module routeren en elke register module kan slechts worden toegewezen aan één container register. Daarom moeten installatie kopieën die lagere laag apparaten ophaalden worden opgeslagen in één container register.

Als u niet wilt dat een module pull-aanvragen maakt via een gateway hiërarchie, is een andere optie het beheren van een lokale register oplossing. Of push de module installatie kopieën naar de apparaten voordat u implementaties maakt en stel de **imagePullPolicy** vervolgens in op **nooit**.

#### <a name="bootstrap-the-iot-edge-agent"></a>Boots trap de IoT Edge-agent

De IoT Edge-agent is het eerste runtime onderdeel dat op een IoT Edge apparaat kan worden gestart. U moet ervoor zorgen dat downstream IoT Edge-apparaten toegang kunnen krijgen tot de installatie kopie van de edgeAgent-module wanneer ze worden opgestart, en ze kunnen vervolgens toegang krijgen tot implementaties en de rest van de module installatie kopieën starten.

Wanneer u op een IoT Edge apparaat naar het configuratie bestand gaat om de verificatie gegevens, certificaten en bovenliggende hostnamen op te geven, moet u ook de edgeAgent-container installatie kopie bijwerken.

Als het gateway apparaat op het hoogste niveau is geconfigureerd voor het afhandelen van container installatie kopie aanvragen, vervangt u door `mcr.microsoft.com` de bovenliggende hostnaam en de API proxy Luister poort. In het implementatie manifest kunt u `$upstream` als een snelkoppeling gebruiken, maar hiervoor moet de module edgeHub de route ring afhandelen en die module op dit moment nog niet is gestart. Bijvoorbeeld:

```toml
[agent]
name = "edgeAgent"
type = "docker"

[agent.config]
image: "{Parent FQDN or IP}:443/azureiotedge-agent:1.2.0-rc4"
```

Als u een lokaal container register gebruikt of de container installatie kopieën hand matig op het apparaat opgeeft, moet u het configuratie bestand dienovereenkomstig bijwerken.

#### <a name="configure-runtime-and-deploy-proxy-module"></a>Runtime configureren en proxy module implementeren

De **API-proxy module** is vereist voor het door sturen van alle communicatie tussen de Cloud en alle downstream-IOT edge apparaten. De module is niet nodig voor een IoT Edge apparaat in de onderste laag van de hiërarchie zonder downstream IoT Edge apparaten.

De API-proxy module is ontworpen om te worden aangepast voor het verwerken van de meest voorkomende gateway scenario's. In dit artikel wordt kort gewaakt over de stappen voor het instellen van de modules in een basis configuratie. Raadpleeg [de API-proxy module voor uw gateway hiërarchie scenario configureren](how-to-configure-api-proxy-module.md) voor meer gedetailleerde informatie en voor beelden.

1. Navigeer in het [Azure Portal](https://portal.azure.com)naar uw IOT-hub.
1. Selecteer **IOT Edge** in het navigatie menu.
1. Selecteer het apparaat met de lagere laag dat u configureert in de lijst met **IOT edge apparaten**.
1. Selecteer **Modules instellen**.
1. Selecteer in de sectie **IOT Edge modules** **toevoegen** en kies vervolgens **Marketplace-module**.
1. Zoek en selecteer de proxy module voor de **IOT Edge-API** .
1. Selecteer de naam van de API-proxy module in de lijst met geïmplementeerde modules en werk de volgende module-instellingen bij:
   1. Werk op het tabblad **module-instellingen** de waarde van de **afbeeldings-URI** bij. Vervang `mcr.microsoft.com` door `$upstream:443`.
   1. Werk op het tabblad **omgevings variabelen** de waarde van **NGINX_DEFAULT_PORT** naar `443` .
   1. Werk op het tabblad Opties voor het maken van de **container** de poort bindingen bij naar referentie poort 443.

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

   Met deze wijzigingen wordt de API-proxy module geconfigureerd om te Luis teren op poort 443. Om conflicten met poort bindingen te voor komen, moet u de edgeHub-module zo configureren dat deze niet luistert op poort 443. In plaats daarvan stuurt de API-proxy module elk edgeHub-verkeer op poort 443.

1. Selecteer **runtime-instellingen**.
1. De instellingen van de edgeHub-module bijwerken:

   1. Vervang in het veld **afbeelding** door `mcr.microsoft.com` `$upstream:443` .
   1. In het veld **Maak opties** verwijdert u de poort binding voor poort 443, waarbij u de bindingen voor poorten 5671 en 8883 verlaat.

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

1. De instellingen van de edgeAgent-module bijwerken:
   1. Vervang in het veld **afbeelding** door `mcr.microsoft.com` `$upstream:443` .

1. Selecteer **Opslaan** om uw wijzigingen in de runtime-instellingen op te slaan.
1. Selecteer **volgende: routes** om naar de volgende stap te gaan.
1. Als u apparaat-naar-Cloud-berichten wilt inschakelen vanaf downstream-apparaten om IoT Hub te bereiken, neemt u een route op waarmee alle berichten worden door gegeven aan `$upstream` . De upstream-para meter verwijst naar het bovenliggende apparaat in het geval van apparaten met een lagere laag. Bijvoorbeeld:
    1. **Naam**: `Route`
    1. **Waarde**: `FROM /messages/* INTO $upstream`
1. Selecteer **controleren + maken** om naar de laatste stap te gaan.
1. Selecteer **maken** om te implementeren op het apparaat.

## <a name="next-steps"></a>Volgende stappen

[Hoe een IoT Edge-apparaat kan worden gebruikt als gateway](iot-edge-as-gateway.md)

[De API-proxy module configureren voor uw gateway hiërarchie scenario](how-to-configure-api-proxy-module.md)