---
title: Downstream-apparaten verbinden-Azure IoT Edge | Microsoft Docs
description: Stroomafwaartse of Leaf-apparaten configureren om verbinding te maken met Azure IoT Edge gateway apparaten.
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
- devx-track-js
ms.openlocfilehash: 78600b7b57a7c30fc609434a700f13fa21e079ce
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/17/2020
ms.locfileid: "94659638"
---
# <a name="connect-a-downstream-device-to-an-azure-iot-edge-gateway"></a>Een downstreamapparaat verbinden met een Azure IoT Edge-gateway

Dit artikel bevat instructies voor het tot stand brengen van een vertrouwde verbinding tussen downstream-apparaten en IoT Edge transparante gateways. In een transparant Gateway scenario kunnen een of meer apparaten hun berichten door geven via één gateway apparaat waarmee de verbinding met IoT Hub wordt onderhouden.

Er zijn drie algemene stappen voor het instellen van een geslaagde transparante gateway verbinding. In dit artikel wordt de derde stap behandeld:

1. Configureer het gateway apparaat als een server zodat downstream-apparaten veilig verbinding kunnen maken. Stel de gateway in voor het ontvangen van berichten van downstream-apparaten en stuur ze naar de juiste bestemming. Zie [een IOT edge apparaat configureren om te fungeren als transparante gateway](how-to-create-transparent-gateway.md)voor deze stappen.
2. Maak een apparaat-id voor het downstream-apparaat, zodat het kan worden geverifieerd met IoT Hub. Configureer het downstream-apparaat om berichten te verzenden via het gateway apparaat. Zie [een downstream-apparaat verifiëren voor Azure IOT hub](how-to-authenticate-downstream-device.md)voor deze stappen.
3. **Verbind het downstream-apparaat met het gateway apparaat en begin met het verzenden van berichten.**

In dit artikel worden de basis concepten voor downstream-apparaatnamen beschreven en wordt u begeleid bij het instellen van uw downstream-apparaten:

* Uitleg van TLS (trans port Layer Security) en basis beginselen van certificaten.
* Uitleg over de werking van TLS-bibliotheken op verschillende besturings systemen en over de manier waarop elk besturings systeem met certificaten werkt.
* Bekijk Azure IoT-voor beelden in verschillende talen om u op weg te helpen.

In dit artikel verwijzen de voor waarden *Gateway* en *IOT Edge gateway* naar een IOT edge apparaat geconfigureerd als een transparante gateway.

## <a name="prerequisites"></a>Vereisten

* Hebben het basis-CA-certificaat bestand dat is gebruikt voor het genereren van het CA-certificaat van het apparaat in [een IOT edge apparaat configureren om te fungeren als een transparante gateway die](how-to-create-transparent-gateway.md) beschikbaar is op uw downstream-apparaat. Het downstream-apparaat gebruikt dit certificaat om de identiteit van het gateway-apparaat te valideren. Als u de demo certificaten hebt gebruikt, wordt het basis-CA-certificaat de naam **Azure-IOT-test-only. root. ca. cert. pem**.
* Beschikken over de gewijzigde connection string die naar het gateway apparaat verwijzen, zoals wordt uitgelegd in [een downstream-apparaat verifiëren op Azure IOT hub](how-to-authenticate-downstream-device.md).

## <a name="prepare-a-downstream-device"></a>Een downstream-apparaat voorbereiden

Een downstream-apparaat kan een toepassing of platform zijn met een identiteit die is gemaakt met de Azure IoT Hub-Cloud service. In veel gevallen gebruiken deze toepassingen de [Azure IOT Device SDK](../iot-hub/iot-hub-devguide-sdks.md). Een downstream-apparaat kan zelfs een toepassing zijn die wordt uitgevoerd op het IoT Edge gateway-apparaat zelf. Een andere IoT Edge apparaat kan echter niet worden downstream van een IoT Edge gateway.

>[!NOTE]
>IoT-apparaten die zijn geregistreerd met IoT Hub kunnen [module apparaatdubbels](../iot-hub/iot-hub-devguide-module-twins.md) gebruiken om verschillende processen, hardware of functies op één apparaat te isoleren. IoT Edge gateways ondersteunen downstream-module verbindingen met behulp van symmetrische sleutel verificatie, maar niet X. 509-certificaat verificatie.

Als u een downstream-apparaat wilt verbinden met een IoT Edge gateway, hebt u twee dingen nodig:

* Een apparaat of toepassing dat is geconfigureerd met een IoT Hub apparaat connection string toegevoegd met informatie om deze te verbinden met de gateway.

    Deze stap is voltooid in het vorige artikel, [een downstream-apparaat verifiëren bij Azure IOT hub](how-to-authenticate-downstream-device.md#retrieve-and-modify-connection-string).

* Het apparaat of de toepassing moet het **basis-CA-certificaat** van de gateway vertrouwen om de TLS-verbindingen (trans port Layer Security) te valideren met het gateway apparaat.

    Deze stap wordt gedetailleerd beschreven in de rest van dit artikel. Deze stap kan op twee manieren worden uitgevoerd: door het CA-certificaat in het certificaat archief van het besturings systeem te installeren of (voor bepaalde talen) door te verwijzen naar het certificaat in toepassingen met behulp van de Azure IoT Sdk's.

## <a name="tls-and-certificate-fundamentals"></a>Basis beginselen van TLS en certificaten

De uitdaging van het veilig verbinden van downstream-apparaten met IoT Edge is net hetzelfde als elke andere beveiligde client/server-communicatie die plaatsvindt via internet. Een client en een server communiceren veilig via internet via [trans port Layer Security (TLS)](https://en.wikipedia.org/wiki/Transport_Layer_Security). TLS is gebouwd met behulp van standaard-constructies met een [open bare-sleutel infrastructuur (PKI)](https://en.wikipedia.org/wiki/Public_key_infrastructure) met de naam certificaten. TLS is een redelijk gepaarde specificatie en behandelt een breed scala aan onderwerpen met betrekking tot het beveiligen van twee eind punten. In deze sectie vindt u een overzicht van de concepten die relevant zijn voor u om apparaten veilig te verbinden met een IoT Edge gateway.

Wanneer een client verbinding maakt met een server, presenteert de server een keten van certificaten, die de *server certificaat keten* wordt genoemd. Een certificaat keten omvat doorgaans een basis certificaat voor certificerings instanties (CA), een of meer tussenliggende CA-certificaten en uiteindelijk het server certificaat zelf. Een-client brengt een vertrouwens relatie met een server tot stand door de hele server certificaat keten te controleren. Deze client validatie van de server certificaat keten wordt de *validatie van de server keten* genoemd. De-client vraagt de server om te bewijzen dat de persoonlijke sleutel die is gekoppeld aan het server certificaat in een proces *wordt genoemd.* De combi natie van validatie van de server keten en het bewijs van bezit wordt *Server verificatie* genoemd. Voor het valideren van een server certificaat keten heeft een client een kopie nodig van het basis-CA-certificaat dat is gebruikt voor het maken (of uitgeven) van het server certificaat. Normaal gesp roken wordt bij het maken van verbinding met websites een browser vooraf geconfigureerd met veelgebruikte CA-certificaten, zodat de client naadloos proces heeft.

Wanneer een apparaat verbinding maakt met Azure IoT Hub, is het apparaat de client en is de IoT Hub-Cloud service de server. De IoT Hub Cloud service wordt ondersteund door een basis-CA-certificaat met de naam **Baltimore Cyber Trust Root**, dat openbaar beschikbaar is en veel wordt gebruikt. Omdat de IoT Hub CA-certificaat al op de meeste apparaten is geïnstalleerd, gebruiken veel TLS-implementaties (OpenSSL, Schannel, LibreSSL) deze automatisch tijdens de validatie van het server certificaat. Een apparaat dat verbinding maakt met IoT Hub kan problemen ondervinden met het proberen verbinding te maken met een IoT Edge gateway.

Wanneer een apparaat verbinding maakt met een IoT Edge gateway, is het downstream-apparaat de client en is het gateway apparaat de-server. Met Azure IoT Edge kunt u Gateway certificaat ketens maken, maar ze kunnen er ook aan voldoen. U kunt ervoor kiezen om een openbaar CA-certificaat te gebruiken, zoals Baltimore, of een zelf-ondertekend (of intern) basis-CA-certificaat gebruiken. Voor open bare CA-certificaten gelden vaak kosten. deze worden doorgaans gebruikt in productie scenario's. Zelfondertekende CA-certificaten worden aanbevolen voor ontwikkeling en testen. Als u de demo certificaten gebruikt, zijn dit zelfondertekende basis-CA-certificaten.

Wanneer u een zelfondertekend basis-CA-certificaat voor een IoT Edge gateway gebruikt, moet het worden geïnstalleerd op of worden voorzien van alle downstream-apparaten die verbinding proberen te maken met de gateway.

![Installatie van Gateway certificaat](./media/how-to-create-transparent-gateway/gateway-setup.png)

Zie [IOT Edge certificaat gebruik Details](iot-edge-certs.md)voor meer informatie over IOT Edge certificaten en enkele gevolgen voor de productie.

## <a name="provide-the-root-ca-certificate"></a>Het basis-CA-certificaat opgeven

Het downstream-apparaat heeft een eigen kopie van het basis-CA-certificaat nodig om de certificaten van het gateway-apparaat te controleren. Als u de scripts in de IoT Edge Git-opslag plaats hebt gebruikt om test certificaten te maken, wordt het basis-CA-certificaat de naam **Azure-IOT-test-only. root. ca. cert. pem**. Verplaats dit certificaat bestand naar een map op het downstream-apparaat als u dat nog niet hebt gedaan. U kunt een service zoals [Azure Key Vault](../key-vault/index.yml) of een functie zoals [Secure Copy Protocol](https://www.ssh.com/ssh/scp/) gebruiken om het certificaat bestand te verplaatsen.

## <a name="install-certificates-in-the-os"></a>Certificaten in het besturings systeem installeren

Wanneer het basis-CA-certificaat zich op het downstream-apparaat bevindt, moet u ervoor zorgen dat de toepassingen die verbinding maken met de gateway toegang tot het certificaat hebben.

Als u het basis-CA-certificaat in het certificaat archief van het besturings systeem installeert, kunnen de meeste toepassingen het basis-CA-certificaat gebruiken. Er zijn enkele uitzonde ringen, zoals NodeJS-toepassingen die geen gebruikmaken van het certificaat archief van het besturings systeem, maar het interne certificaat archief van de node runtime gebruiken. Als u het certificaat niet op het niveau van het besturings systeem kunt installeren, gaat u verder met het [gebruik van certificaten met Azure IOT sdk's](#use-certificates-with-azure-iot-sdks).

### <a name="ubuntu"></a>Ubuntu

De volgende opdrachten zijn een voor beeld van het installeren van een CA-certificaat op een Ubuntu-host. In dit voor beeld wordt ervan uitgegaan dat u het certificaat **Azure-IOT-test-only. root. ca. cert. pem** gebruikt uit de artikelen van de vereisten en dat u het certificaat hebt gekopieerd naar een locatie op het downstream-apparaat.

```bash
sudo cp <path>/azure-iot-test-only.root.ca.cert.pem /usr/local/share/ca-certificates/azure-iot-test-only.root.ca.cert.pem.crt
sudo update-ca-certificates
```

Er wordt een bericht weer gegeven met de tekst ' certificaten bijwerken in/etc/ssl/certs... 1 toegevoegd, 0 verwijderd; gereed. "

### <a name="windows"></a>Windows

De volgende stappen zijn een voor beeld van het installeren van een CA-certificaat op een Windows-host. In dit voor beeld wordt ervan uitgegaan dat u het certificaat **Azure-IOT-test-only. root. ca. cert. pem** gebruikt uit de artikelen van de vereisten en dat u het certificaat hebt gekopieerd naar een locatie op het downstream-apparaat.

U kunt certificaten installeren met behulp van het [import-certificaat](/powershell/module/pkiclient/import-certificate?view=win10-ps) van Power shell als beheerder:

```powershell
import-certificate  <file path>\azure-iot-test-only.root.ca.cert.pem -certstorelocation cert:\LocalMachine\root
```

U kunt certificaten ook installeren met het hulp programma **certlm** :

1. Zoek in het menu Start naar en selecteer **computer certificaten beheren**. Een hulp programma met de naam **certlm** wordt geopend.
2. Navigeer naar **certificaten-**  >  **vertrouwde basis certificerings instanties** op de lokale computer.
3. Klik met de rechter muisknop op **certificaten** en selecteer **alle taken**  >  **importeren**. De wizard Certificaat importeren moet worden gestart.
4. Volg de stappen als gericht en importeer het certificaat bestand `<path>/azure-iot-test-only.root.ca.cert.pem` . Als u klaar bent, ziet u het bericht ' is geïmporteerd '.

U kunt certificaten ook programmatisch installeren met behulp van .NET Api's, zoals wordt weer gegeven in .NET-voor beeld verderop in dit artikel.

Doorgaans gebruiken toepassingen de door Windows meegeleverde TLS-stack [Schannel](/windows/desktop/com/schannel) om veilig verbinding te maken via TLS. Voor *Schannel moeten* alle certificaten in het Windows-certificaat archief worden geïnstalleerd voordat er wordt geprobeerd een TLS-verbinding tot stand te brengen.

## <a name="use-certificates-with-azure-iot-sdks"></a>Certificaten gebruiken met Azure IoT Sdk's

In deze sectie wordt beschreven hoe de Azure IoT Sdk's verbinding maken met een IoT Edge apparaat met behulp van eenvoudige voorbeeld toepassingen. Het doel van alle voor beelden is het verbinden van de apparaatclient en het verzenden van telemetrie-berichten naar de gateway, en sluit vervolgens de verbinding en sluit af.

Hebben twee dingen die klaar zijn voordat de voor beelden op toepassings niveau worden gebruikt:

* De IoT Hub van uw downstream-apparaat connection string zodanig gewijzigd dat het naar het gateway apparaat wijst, en eventuele certificaten die nodig zijn om uw downstream-apparaat te verifiëren voor IoT Hub. Zie [een downstream-apparaat verifiëren voor Azure IOT hub](how-to-authenticate-downstream-device.md)voor meer informatie.

* Het volledige pad naar het basis-CA-certificaat dat u hebt gekopieerd en opgeslagen ergens op het downstream-apparaat.

    Bijvoorbeeld `<path>/azure-iot-test-only.root.ca.cert.pem`.

### <a name="nodejs"></a>Node.js

Deze sectie bevat een voorbeeld toepassing om een Azure IoT NodeJS Device-client te verbinden met een IoT Edge-gateway. Voor NodeJS-toepassingen moet u het basis-CA-certificaat installeren op het niveau van de toepassing, zoals hier wordt weer gegeven. NodeJS-toepassingen gebruiken het certificaat archief van het systeem niet.

1. Haal het voor beeld voor **edge_downstream_device.js** van de [Azure IOT Device SDK voor Node.js samples opslag plaats](https://github.com/Azure/azure-iot-sdk-node/tree/master/device/samples).
2. Zorg ervoor dat u beschikt over alle vereisten om het voor beeld uit te voeren door het **README.MD** -bestand te controleren.
3. Werk in het edge_downstream_device.js-bestand de **Connections Tring** -en **edge_ca_cert_path** -variabelen bij.
4. Raadpleeg de SDK-documentatie voor instructies over het uitvoeren van het voor beeld op het apparaat.

Het volgende code fragment is de manier waarop de client-SDK het certificaat bestand leest en gebruikt om een beveiligde TLS-verbinding tot stand te brengen om te begrijpen welk voor beeld wordt uitgevoerd:

```javascript
// Provide the Azure IoT device client via setOptions with the X509
// Edge root CA certificate that was used to setup the Edge runtime
var options = {
    ca : fs.readFileSync(edge_ca_cert_path, 'utf-8'),
};
```

### <a name="net"></a>.NET

In deze sectie wordt een voorbeeld toepassing geïntroduceerd om een Azure IoT .NET-apparaatclient te verbinden met een IoT Edge-gateway. .NET-toepassingen kunnen echter automatisch alle geïnstalleerde certificaten in het certificaat archief van het systeem op zowel Linux-als Windows-hosts gebruiken.

1. Haal het voor beeld voor **EdgeDownstreamDevice** op uit de [map met IOT Edge .net](https://github.com/Azure/iotedge/tree/master/samples/dotnet/EdgeDownstreamDevice)-voor beelden.
2. Zorg ervoor dat u beschikt over alle vereisten om het voor beeld uit te voeren door het **README.MD** -bestand te controleren.
3. Werk de **DEVICE_CONNECTION_STRING** en de **CA_CERTIFICATE_PATH** variabelen bij in de **Eigenschappen/launchSettings.jsin** het bestand. Als u het certificaat wilt gebruiken dat is geïnstalleerd in het vertrouwde certificaat archief op het hostsysteem, laat u deze variabele leeg.
4. Raadpleeg de SDK-documentatie voor instructies over het uitvoeren van het voor beeld op het apparaat.

Raadpleeg de functie **InstallCACert ()** in het **EdgeDownstreamDevice/Program.cs-** bestand om programmatisch een vertrouwd certificaat in het certificaat archief te installeren via een .NET-toepassing. Deze bewerking is idempotent. kan daarom meerdere keren worden uitgevoerd met dezelfde waarden zonder extra effect.

### <a name="c"></a>C

In deze sectie wordt een voorbeeld toepassing geïntroduceerd om een Azure IoT C-apparaatclient te verbinden met een IoT Edge gateway. De C SDK kan worden gebruikt met veel TLS-Bibliotheken, waaronder OpenSSL, WolfSSL en Schannel. Zie de [Azure IOT C-SDK](https://github.com/Azure/azure-iot-sdk-c)voor meer informatie.

1. Haal de **iotedge_downstream_device_sample** toepassing op uit de [Azure IOT Device SDK voor C-voor beelden](https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples).
2. Zorg ervoor dat u beschikt over alle vereisten om het voor beeld uit te voeren door het **README.MD** -bestand te controleren.
3. Werk in het bestand iotedge_downstream_device_sample. c de variabelen **Connections Tring** en **edge_ca_cert_path** bij.
4. Raadpleeg de SDK-documentatie voor instructies over het uitvoeren van het voor beeld op het apparaat.


De Azure IoT Device SDK voor C biedt een optie voor het registreren van een CA-certificaat bij het instellen van de client. Met deze bewerking wordt het certificaat nergens geïnstalleerd, maar wordt in plaats daarvan een teken reeks indeling gebruikt van het certificaat in het geheugen. Het opgeslagen certificaat wordt aan de onderliggende TLS-stack gegeven tijdens het tot stand brengen van een verbinding.

```C
(void)IoTHubDeviceClient_SetOption(device_handle, OPTION_TRUSTED_CERT, cert_string);
```

>[!NOTE]
> De methode voor het registreren van een CA-certificaat bij het instellen van de client kan worden gewijzigd als een [beheerd](https://github.com/Azure/azure-iot-sdk-c#packages-and-libraries) pakket of bibliotheek wordt gebruikt. De [ARDUINO IDE-gebaseerde bibliotheek](https://github.com/azure/azure-iot-arduino) vereist bijvoorbeeld dat het CA-certificaat wordt toegevoegd aan een certificaten matrix die is gedefinieerd in een globaal [certificaat. c](https://github.com/Azure/azure-iot-sdk-c/blob/master/certs/certs.c) -bestand, in plaats van de bewerking te gebruiken `IoTHubDeviceClient_LL_SetOption` .  

Als u op Windows-hosts geen OpenSSL of een andere TLS-bibliotheek gebruikt, wordt de SDK standaard gebruikt voor het gebruik van Schannel. Voor het werken met Schannel moet het IoT Edge basis-CA-certificaat in het Windows-certificaat archief worden geïnstalleerd en niet worden ingesteld met behulp van de `IoTHubDeviceClient_SetOption` bewerking.

### <a name="java"></a>Java

In deze sectie wordt een voorbeeld toepassing geïntroduceerd om een Azure IoT Java-apparaatclient te verbinden met een IoT Edge gateway.

1. Het voor beeld voor **Send-Event** ophalen uit de [Azure IOT Device SDK voor Java-voor beelden](https://github.com/Azure/azure-iot-sdk-java/tree/master/device/iot-device-samples).
2. Zorg ervoor dat u beschikt over alle vereisten om het voor beeld uit te voeren door het **README.MD** -bestand te controleren.
3. Raadpleeg de SDK-documentatie voor instructies over het uitvoeren van het voor beeld op het apparaat.

### <a name="python"></a>Python

In deze sectie wordt een voorbeeld toepassing geïntroduceerd om een Azure IoT python-apparaatclient te verbinden met een IoT Edge gateway.

1. Bekijk het voor beeld voor **send_message_downstream** van de [Azure IOT Device SDK voor python-voor beelden](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples/async-edge-scenarios).
2. Stel de `IOTHUB_DEVICE_CONNECTION_STRING` `IOTEDGE_ROOT_CA_CERT_PATH` omgevings variabelen in zoals opgegeven in de python-script opmerkingen.
3. Raadpleeg de SDK-documentatie voor aanvullende instructies over het uitvoeren van het voor beeld op het apparaat.

## <a name="test-the-gateway-connection"></a>De gateway verbinding testen

Gebruik deze voorbeeld opdracht op het downstream-apparaat om te testen of er verbinding kan worden gemaakt met het gateway apparaat:

```cmd/sh
openssl s_client -connect mygateway.contoso.com:8883 -CAfile <CERTDIR>/certs/azure-iot-test-only.root.ca.cert.pem -showcerts
```

Met deze opdracht worden verbindingen getest via MQTTS (poort 8883). Als u een ander protocol gebruikt, past u de opdracht indien nodig aan voor AMQPS (5671) of HTTPS (433)

De uitvoer van deze opdracht kan lang zijn, inclusief informatie over alle certificaten in de keten. Als uw verbinding is geslaagd, ziet u een regel zoals `Verification: OK` of `Verify return code: 0 (ok)` .

![Gateway verbinding controleren](./media/how-to-connect-downstream-device/verification-ok.png)

## <a name="troubleshoot-the-gateway-connection"></a>Problemen met de gateway verbinding oplossen

Als uw blad apparaat een terugkerende verbinding met het gateway apparaat heeft, voert u de volgende stappen uit voor het oplossen van problemen.

1. Is de hostnaam van de gateway in de connection string hetzelfde als de hostnaam-waarde in het bestand IoT Edge config. yaml op het gateway apparaat?
2. Is de hostnaam van de gateway oplosbaar voor een IP-adres? U kunt onregelmatige verbindingen oplossen door DNS te gebruiken of door een vermelding voor een hostbestand toe te voegen op het blad apparaat.
3. Zijn er communicatie poorten in de firewall geopend? Communicatie op basis van het gebruikte protocol (MQTTS: 8883/AMQPS: 5671/HTTPS: 433) moet mogelijk zijn tussen het downstream-apparaat en de transparante IoT Edge.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over hoe IoT Edge [offline mogelijkheden](offline-capabilities.md) kunt uitbreiden naar downstream-apparaten.