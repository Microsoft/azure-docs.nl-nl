---
title: Transparant gateway apparaat maken-Azure IoT Edge | Microsoft Docs
description: Een Azure IoT Edge apparaat gebruiken als een transparante gateway die informatie kan verwerken vanaf downstream-apparaten
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 10/15/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: 9ecb1c50fe99cc93417a37e892049e03585945a5
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100370424"
---
# <a name="configure-an-iot-edge-device-to-act-as-a-transparent-gateway"></a>Een IoT Edge apparaat configureren om te fungeren als transparante gateway

In dit artikel vindt u gedetailleerde instructies voor het configureren van een IoT Edge apparaat om te werken als een transparante gateway zodat andere apparaten kunnen communiceren met IoT Hub. In dit artikel wordt de term *IOT Edge gateway* gebruikt om te verwijzen naar een IOT edge apparaat dat is geconfigureerd als een transparante gateway. Zie [How a IOT edge-apparaten kunnen worden gebruikt als een gateway](./iot-edge-as-gateway.md)voor meer informatie.

<!-- 1.1 -->
::: moniker range="iotedge-2018-06"

>[!NOTE]
>Dat
>
> * Apparaten met rand mogelijkheden kunnen geen verbinding maken met IoT Edge gateways.
> * Downstream-apparaten kunnen het uploaden van bestanden niet gebruiken.

::: moniker-end

<!-- 1.2.0 -->
::: moniker range=">=iotedge-2020-11"

>[!NOTE]
>Dat
>
> * Downstream-apparaten kunnen het uploaden van bestanden niet gebruiken.

::: moniker-end

Er zijn drie algemene stappen voor het instellen van een geslaagde transparante gateway verbinding. In dit artikel wordt de eerste stap behandeld:

1. **Configureer het gateway apparaat als een server zodat downstream-apparaten veilig verbinding kunnen maken. Stel de gateway in voor het ontvangen van berichten van downstream-apparaten en stuur ze naar de juiste bestemming.**
2. Maak een apparaat-id voor het downstream-apparaat, zodat het kan worden geverifieerd met IoT Hub. Configureer het downstream-apparaat om berichten te verzenden via het gateway apparaat. Zie [een downstream-apparaat verifiëren voor Azure IOT hub](how-to-authenticate-downstream-device.md)voor deze stappen.
3. Verbind het downstream-apparaat met het gateway apparaat en begin met het verzenden van berichten. Zie [een downstream-apparaat verbinden met een Azure IOT Edge-gateway](how-to-connect-downstream-device.md)voor deze stappen.

Een apparaat kan alleen als gateway fungeren als het een veilige verbinding met de downstream-apparaten heeft. Met Azure IoT Edge kunt u een PKI (Public Key Infrastructure) gebruiken om beveiligde verbindingen tussen apparaten in te stellen. In dit geval kunnen we een downstream-apparaat verbinding laten maken met een IoT Edge apparaat dat als transparante gateway fungeert. Om redelijke beveiliging te behouden, moet het downstream-apparaat de identiteit van het gateway-apparaat bevestigen. Met deze identiteits controle voor komt u dat uw apparaten verbinding maken met mogelijk schadelijke gateways.

Een downstream-apparaat kan een toepassing of platform zijn met een identiteit die is gemaakt met de [Azure IOT hub](../iot-hub/index.yml) -Cloud service. In deze toepassingen wordt vaak gebruikgemaakt van de [Azure IOT Device SDK](../iot-hub/iot-hub-devguide-sdks.md). Een downstream-apparaat kan zelfs een toepassing zijn die wordt uitgevoerd op het IoT Edge gateway-apparaat zelf. Een IoT Edge apparaat kan echter niet worden downstream van een IoT Edge gateway.

U kunt een certificaat infrastructuur maken waarmee de vertrouwens relatie die is vereist voor de gateway topologie van uw apparaat wordt ingeschakeld. In dit artikel wordt ervan uitgegaan dat u dezelfde certificaat instelling gebruikt voor het inschakelen van [x. 509 ca-beveiliging](../iot-hub/iot-hub-x509ca-overview.md) in IOT hub, waarbij een x. 509 CA-certificaat is gekoppeld aan een specifieke IOT-hub (de basis-CA van de IOT-hub), een reeks certificaten die zijn ondertekend met deze certificerings instantie en een certificerings instantie voor het IOT edge apparaat.

>[!NOTE]
>Het basis-CA-certificaat dat in deze artikelen wordt gebruikt, verwijst naar het open bare certificaat van de bovenste *certificerings* instantie van de PKI-certificaat keten en niet noodzakelijkerwijs de hoofdmap van het certificaat van een extern gepubliceerde certificerings instantie. In veel gevallen is het eigenlijk een openbaar certificaat van een tussenliggend CA.

De volgende stappen leiden u door het proces van het maken van de certificaten en het installeren ervan op de juiste plaatsen op de gateway. U kunt elke computer gebruiken om de certificaten te genereren en deze vervolgens naar uw IoT Edge apparaat kopiëren.

## <a name="prerequisites"></a>Vereisten

Een Linux-of Windows-apparaat met IoT Edge geïnstalleerd.

Als u nog geen apparaat hebt, kunt u er een maken in een virtuele Azure-machine. Volg de stappen in [uw eerste IOT Edge-module implementeren op een virtueel Linux-apparaat](quickstart-linux.md) om een IOT hub te maken, een virtuele machine te maken en de IOT Edge runtime te configureren. 

## <a name="set-up-the-device-ca-certificate"></a>Het CA-certificaat van het apparaat instellen

Voor alle IoT Edge gateways moet een CA-certificaat van een apparaat zijn geïnstalleerd. De IoT Edge Security daemon maakt gebruik van het CA-certificaat van IoT Edge apparaat om een CA-certificaat van een werk belasting te ondertekenen, dat op zijn beurt een server certificaat voor IoT Edge hub ondertekent. De gateway presenteert het server certificaat voor het downstream-apparaat tijdens de start van de verbinding. Het downstream-apparaat controleert of het server certificaat deel uitmaakt van een certificaat keten die naar het basis-CA-certificaat rolt. Met dit proces kan het downstream-apparaat controleren of de gateway afkomstig is van een vertrouwde bron. Zie [begrijpen hoe Azure IOT Edge certificaten gebruikt](iot-edge-certs.md)voor meer informatie.

![Installatie van Gateway certificaat](./media/how-to-create-transparent-gateway/gateway-setup.png)

Het basis-CA-certificaat en het CA-certificaat (met de persoonlijke sleutel) moeten aanwezig zijn op het IoT Edge gateway-apparaat en worden geconfigureerd in het bestand IoT Edge config. yaml. Houd er rekening mee dat in dit geval het *basis-CA-certificaat* de bovenste certificerings instantie voor dit IOT Edge scenario aangeeft. Het CA-certificaat van de gateway-apparaat en de certificaten van het downstream-apparaat moeten worden getotaliseerd op hetzelfde basis-CA-certificaat.

>[!TIP]
>Het proces voor het installeren van het basis-CA-certificaat en het CA-certificaat van het apparaat op een IoT Edge apparaat wordt ook gedetailleerd beschreven in [certificaten op een IOT edge apparaat beheren](how-to-manage-device-certificates.md).

De volgende bestanden zijn gereed:

* Basis-CA-certificaat
* CA-certificaat van apparaat
* Persoonlijke sleutel van de apparaat-CA

Voor productie scenario's moet u deze bestanden met uw eigen certificerings instantie genereren. Voor ontwikkelings-en test scenario's kunt u demo certificaten gebruiken.

1. Als u demo certificaten gebruikt, volgt u de instructies in [demo certificaten maken om IOT Edge apparaatfuncties te testen](how-to-create-test-certificates.md) om uw bestanden te maken. Op die pagina moet u de volgende stappen uitvoeren:

   1. Stel de scripts voor het genereren van certificaten op het apparaat in om te starten.
   2. Maak een basis-CA-certificaat. Aan het einde van deze instructies hebt u een basis-CA-certificaat bestand:
      * `<path>/certs/azure-iot-test-only.root.ca.cert.pem`.
   3. IoT Edge CA-certificaten voor apparaten maken. Aan het einde van deze instructies hebt u een CA-certificaat van het apparaat en de bijbehorende persoonlijke sleutel:
      * `<path>/certs/iot-edge-device-<cert name>-full-chain.cert.pem` maar
      * `<path>/private/iot-edge-device-<cert name>.key.pem`

2. Als u de certificaten op een andere computer hebt gemaakt, kopieert u deze naar uw IoT Edge-apparaat.

3. Open het configuratie bestand van de beveiligings-daemon op uw IoT Edge-apparaat.
   * Windows: `C:\ProgramData\iotedge\config.yaml`
   * Linux: `/etc/iotedge/config.yaml`

4. Zoek de sectie **certificaat instellingen** van het bestand. Verwijder opmerkingen bij de vier regels die beginnen met **certificaten:** en geef de bestands-uri's op uw drie bestanden op als waarden voor de volgende eigenschappen:
   * **device_ca_cert**: CA-certificaat van apparaat
   * **device_ca_pk**: privé sleutel van de apparaat-ca
   * **trusted_ca_certs**: basis-CA-certificaat

   Zorg ervoor dat er geen voor gaande witruimte op de **certificaten:** regel is en dat de andere regels worden inge sprongen met twee spaties.

5. Sla het bestand op en sluit het.

6. Start IoT Edge opnieuw.
   * Windows: `Restart-Service iotedge`
   * Linux: `sudo systemctl restart iotedge`

## <a name="deploy-edgehub-and-route-messages"></a>EdgeHub en router berichten implementeren

Downstream-apparaten verzenden telemetrie en berichten naar het gateway apparaat, waarbij de IoT Edge hub-module verantwoordelijk is voor het door sturen van gegevens naar andere modules of naar IoT Hub. Als u uw gateway-apparaat voor deze functie wilt voorbereiden, controleert u het volgende:

* De module IoT Edge hub wordt geïmplementeerd op het apparaat.

  Wanneer u IoT Edge voor het eerst op een apparaat installeert, wordt slechts één systeem module automatisch gestart: de IoT Edge agent. Wanneer u de eerste implementatie voor een apparaat hebt gemaakt, wordt de tweede systeem module, de IoT Edge hub, ook gestart. Als de **edgeHub** -module niet op uw apparaat wordt uitgevoerd, maakt u een implementatie voor uw apparaat.

* De module IoT Edge hub bevat routes die zijn ingesteld voor het verwerken van inkomende berichten van downstream-apparaten.

  Het gateway apparaat moet een route hebben om berichten van downstream-apparaten te kunnen afhandelen of anders worden deze berichten niet verwerkt. U kunt de berichten verzenden naar modules op het gateway apparaat of rechtstreeks naar IoT Hub.

Voer de volgende stappen uit om de IoT Edge hub-module te implementeren en te configureren met routes voor het afhandelen van binnenkomende berichten van downstream-apparaten:

1. Ga in Azure Portal naar uw IoT-hub.

2. Ga naar **IOT Edge** en selecteer het IOT edge apparaat dat u als gateway wilt gebruiken.

3. Selecteer **modules instellen**.

4. Op de pagina **modules** kunt u alle modules toevoegen die u wilt implementeren op het gateway apparaat. Voor de doel einden van dit artikel zijn we gericht op het configureren en implementeren van de edgeHub-module, die niet expliciet hoeft te worden ingesteld op deze pagina.

5. Selecteer **volgende: routes**.

6. Zorg er op de pagina **routes** voor dat er een route is voor het afhandelen van berichten die afkomstig zijn van downstream-apparaten. Bijvoorbeeld:

   * Een route waarmee alle berichten, of van een module of van een downstream-apparaat, worden verzonden naar IoT Hub:
       * **Naam**: `allMessagesToHub`
       * **Waarde**: `FROM /messages/* INTO $upstream`

   * Een route waarmee alle berichten van alle downstream-apparaten naar IoT Hub worden verzonden:
      * **Naam**: `allDownstreamToHub`
      * **Waarde**: `FROM /messages/* WHERE NOT IS_DEFINED ($connectionModuleId) INTO $upstream`

      Deze route werkt omdat, in tegens telling tot berichten uit IoT Edge modules, er geen module-ID aan de berichten van downstream-apparaten is gekoppeld. Door gebruik te maken van de component **where** van de route kunnen we berichten met die systeem eigenschap filteren.

      Zie [modules implementeren en routes instellen](./module-composition.md#declare-routes)voor meer informatie over het routeren van berichten.

7. Zodra uw route of routes zijn gemaakt, selecteert u **controleren + maken**.

8. Selecteer op de pagina **controleren en maken** de optie **maken**.

## <a name="open-ports-on-gateway-device"></a>Poorten op gateway apparaat openen

Standard IoT Edge-apparaten hebben geen binnenkomende verbinding nodig om te kunnen werken, omdat alle communicatie met IoT Hub geschiedt via uitgaande verbindingen. Gateway apparaten wijken af omdat ze berichten van hun downstream-apparaten moeten ontvangen. Als er een firewall is tussen de downstream-apparaten en het gateway apparaat, moet communicatie ook mogelijk zijn via de firewall.

Een gateway scenario werkt alleen als ten minste een van de ondersteunde protocollen van de IoT Edge hub open is voor inkomend verkeer van downstream-apparaten. De ondersteunde protocollen zijn MQTT, AMQP, HTTPS, MQTT over websockets en AMQP over websockets.

| Poort | Protocol |
| ---- | -------- |
| 8883 | MQTT |
| 5671 | AMQP |
| 443 | HTTPS <br> MQTT + WS <br> AMQP + WS |

## <a name="next-steps"></a>Volgende stappen

Nu u een IoT Edge apparaat hebt ingesteld als een transparante gateway, moet u uw downstream-apparaten configureren om de gateway te vertrouwen en er berichten naar toe te sturen. Ga door met het [verifiëren van een downstream-apparaat bij Azure IOT hub](how-to-authenticate-downstream-device.md) voor de volgende stappen in het instellen van uw transparante Gateway scenario.