---
title: Downstreamapparaten verifiëren - Azure IoT Edge | Microsoft Docs
description: Downstreamapparaten of leaf-apparaten verifiëren voor IoT Hub en hun verbinding via Azure IoT Edge gatewayapparaten.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 10/15/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 4702682dcd6af68242fd5a34d1fb2e0a9273da36
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107482021"
---
# <a name="authenticate-a-downstream-device-to-azure-iot-hub"></a>Een downstream-apparaat verifiëren voor Azure IoT Hub

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

In een transparant gatewayscenario hebben downstreamapparaten (ook wel leaf-apparaten of onderliggende apparaten genoemd) identiteiten nodig in IoT Hub zoals elk ander apparaat. In dit artikel worden de opties voor het authenticeren van een downstreamapparaat voor IoT Hub beschreven en wordt vervolgens gedemonstreerd hoe u de gatewayverbinding kunt declareren.

Er zijn drie algemene stappen voor het instellen van een geslaagde transparante gatewayverbinding. In dit artikel wordt de tweede stap beschreven:

1. Configureer het gatewayapparaat als een server, zodat downstreamapparaten er veilig verbinding mee kunnen maken. Stel de gateway in voor het ontvangen van berichten van downstreamapparaten en routeer deze naar de juiste bestemming. Zie Configure [an IoT Edge device to act as a transparent gateway (Een](how-to-create-transparent-gateway.md)transparant gateway configureren) voor deze stappen.
2. **Maak een apparaat-id voor het downstreamapparaat zodat het kan worden geverifieerd met IoT Hub. Configureer het downstreamapparaat voor het verzenden van berichten via het gatewayapparaat.**
3. Verbind het downstreamapparaat met het gatewayapparaat en begin met het verzenden van berichten. Zie Connect [a downstream device to an Azure IoT Edge gateway (Een downstreamapparaat verbinden met een Azure IoT Edge gateway) voor deze stappen.](how-to-connect-downstream-device.md)

Downstreamapparaten kunnen worden geverifieerd met IoT Hub met behulp van een van de volgende drie methoden: symmetrische sleutels (ook wel gedeelde toegangssleutels genoemd), zelf-ondertekende X.509-certificaten of door de X.509-certificeringsinstantie (CA) ondertekende certificaten. De verificatiestappen zijn vergelijkbaar met de stappen voor het instellen van een niet-IoT Edge-apparaat met IoT Hub, met kleine verschillen om de gatewayrelatie te declareeren.

Het automatisch inrichten van downstreamapparaten met Azure IoT Hub Device Provisioning Service (DPS) wordt niet ondersteund.

## <a name="prerequisites"></a>Vereisten

Voltooi de stappen in Configure an IoT Edge device to act as a transparent gateway (Een transparant [gatewayapparaat configureren om te fungeren als een transparante gateway).](how-to-create-transparent-gateway.md)

Als u X.509-verificatie gebruikt, genereert u certificaten voor uw downstreamapparaat. U hebt hetzelfde basis-CA-certificaat en het script voor het genereren van het certificaat dat u hebt gebruikt voor het transparante gateway-artikel dat u opnieuw kunt gebruiken.

In dit artikel wordt op verschillende punten naar *de hostnaam van de gateway* verwezen. De hostnaam van de gateway wordt gedeclareerd in de **hostnaamparameter** van het configuratiebestand op IoT Edge gatewayapparaat. Er wordt naar verwezen in de connection string van het downstreamapparaat. De hostnaam van de gateway moet kunnen worden omgeslagen in een IP-adres, met behulp van DNS of een hostbestandsinvoer op het downstreamapparaat.

## <a name="register-device-with-iot-hub"></a>Apparaat registreren bij IoT Hub

Kies hoe u wilt dat uw downstreamapparaat wordt geverifieerd met IoT Hub:

* [Verificatie met symmetrische sleutel:](#symmetric-key-authentication)IoT Hub maakt u een sleutel die u op het downstreamapparaat zet. Wanneer het apparaat wordt geverifieerd, controleert IoT Hub of de twee sleutels overeenkomen. U hoeft geen extra certificaten te maken om symmetrische sleutelverificatie te gebruiken.

  Deze methode is sneller om aan de slag te gaan als u gateways test in een ontwikkelings- of testscenario.

* [Zelf-ondertekende X.509-verificatie:](#x509-self-signed-authentication)dit wordt ook wel vingerafdrukverificatie genoemd, omdat u de vingerafdruk van het X.509-certificaat van het apparaat deelt met IoT Hub.

  Certificaatverificatie wordt aanbevolen voor apparaten in productiescenario's.

* [X.509 CA-ondertekende verificatie:](#x509-ca-signed-authentication)upload het basis-CA-certificaat naar IoT Hub. Wanneer apparaten hun X.509-certificaat voor verificatie presenteren, controleert IoT Hub of het tot een vertrouwensketen behoort die is ondertekend door hetzelfde basis-CA-certificaat.

  Certificaatverificatie wordt aanbevolen voor apparaten in productiescenario's.

### <a name="symmetric-key-authentication"></a>Verificatie met symmetrische sleutel

Verificatie met symmetrische sleutels, of verificatie met een gedeelde toegangssleutel, is de eenvoudigste manier om te verifiëren met IoT Hub. Bij verificatie met symmetrische sleutels wordt een base64-sleutel gekoppeld aan uw IoT-apparaat-id in IoT Hub. U kunt deze sleutel opnemen in uw IoT-toepassingen, zodat uw apparaat deze kan presenteren wanneer het verbinding maakt met IoT Hub.

Voeg een nieuw IoT-apparaat toe aan uw IoT-hub met behulp van de Azure Portal, Azure CLI of de IoT-extensie voor Visual Studio Code. Houd er wel voor dat downstreamapparaten in een IoT Hub worden geïdentificeerd als gewone IoT-apparaten, niet IoT Edge apparaten.

Wanneer u de nieuwe apparaat-id maakt, geeft u de volgende informatie op:

* Maak een id voor uw apparaat.

* Selecteer **Symmetrische sleutel** als verificatietype.

* Selecteer **Een bovenliggend apparaat instellen** en selecteer IoT Edge gatewayapparaat dat door dit downstreamapparaat wordt verbonden. U kunt het bovenliggende later altijd wijzigen.

   ![Apparaat-id met symmetrische sleutelversleuteling maken in de portal](./media/how-to-authenticate-downstream-device/symmetric-key-portal.png)

   >[!NOTE]
   >Het bovenliggende apparaat instellen was vroeger een optionele stap voor downstreamapparaten die gebruikmaken van symmetrische-sleutelverificatie. Vanaf versie IoT Edge versie 1.1.0 moet elk downstreamapparaat echter worden toegewezen aan een bovenliggend apparaat.
   >
   >U kunt de hub IoT Edge terug te gaan naar het vorige gedrag door de omgevingsvariabele **AuthenticationMode** in te stellen op de waarde **CloudAndScope.**

U kunt ook de [IoT-extensie voor Azure CLI gebruiken](https://github.com/Azure/azure-iot-cli-extension) om dezelfde bewerking te voltooien. In het volgende voorbeeld wordt de [opdracht az iot hub device-identity](/cli/azure/iot/hub/device-identity) gebruikt om een nieuw IoT-apparaat met symmetrische sleutelverificatie te maken en een bovenliggend apparaat toe te wijzen:

```azurecli
az iot hub device-identity create -n {iothub name} -d {new device ID} --pd {existing gateway device ID}
```

Vervolgens haalt [u de gegevens op en connection string](#retrieve-and-modify-connection-string) zodat uw apparaat weet dat er verbinding moet worden via de gateway.

### <a name="x509-self-signed-authentication"></a>Zelf-ondertekende X.509-verificatie

Voor zelf-ondertekende X.509-verificatie, ook wel vingerafdrukverificatie genoemd, moet u certificaten maken die u op uw downstreamapparaat wilt plaatsen. Deze certificaten hebben een vingerafdruk die u deelt met IoT Hub voor verificatie.

1. Maak met behulp van uw CA-certificaat twee apparaatcertificaten (primair en secundair) voor het downstreamapparaat.

   Als u geen certificeringsinstantie hebt om X.509-certificaten te maken, kunt u de democertificaatscripts van IoT Edge gebruiken om [downstreamapparaatcertificaten te maken.](how-to-create-test-certificates.md#create-downstream-device-certificates) Volg de stappen voor het maken van zelf-ondertekende certificaten. Gebruik hetzelfde basis-CA-certificaat dat de certificaten voor uw gatewayapparaat heeft gegenereerd.

   Als u uw eigen certificaten maakt, moet u ervoor zorgen dat de onderwerpnaam van het apparaatcertificaat is ingesteld op de apparaat-id die u gebruikt bij het registreren van het IoT-apparaat in de Azure IoT Hub. Deze instelling is vereist voor verificatie.

2. Haal de SHA1-vingerafdruk (een vingerafdruk genoemd in de IoT Hub-interface) op uit elk certificaat. Dit is een tekenreeks van 40 hexadecimale tekens. Gebruik de volgende openssl-opdracht om het certificaat weer te geven en de vingerafdruk te vinden:

   * Windows:

     ```PowerShell
     openssl x509 -in <path to primary device certificate>.cert.pem -text -fingerprint
     ```

   * Linux:

     ```Bash
     openssl x509 -in <path to primary device certificate>.cert.pem -text -fingerprint | sed 's/[:]//g'
     ```

   Voer deze opdracht twee keer uit, één keer voor het primaire certificaat en één keer voor het secundaire certificaat. U geeft vingerafdrukken op voor beide certificaten wanneer u een nieuw IoT-apparaat registreert met behulp van zelf-ondertekende X.509-certificaten.

3. Navigeer naar uw IoT-hub in Azure Portal en maak een nieuwe IoT-apparaat-id met de volgende waarden:

   * Geef de **apparaat-id** op die overeenkomt met de onderwerpnaam van uw apparaatcertificaten.
   * Selecteer **X.509 Self-Signed** als verificatietype.
   * Plak de hexadecimale tekenreeksen die u hebt gekopieerd uit de primaire en secundaire certificaten van uw apparaat.
   * Selecteer **Een bovenliggend apparaat instellen** en kies het IoT Edge gatewayapparaat dat door dit downstreamapparaat wordt verbonden. U kunt de bovenliggende later altijd wijzigen.

   ![Een apparaat-id maken met X.509-zelf-ondertekende auth in de portal](./media/how-to-authenticate-downstream-device/x509-self-signed-portal.png)

4. Kopieer zowel de certificaten van het primaire als het secundaire apparaat en de sleutels naar een locatie op het downstreamapparaat. Verplaats ook een kopie van het gedeelde basis-CA-certificaat dat zowel het gatewayapparaatcertificaat als de downstreamapparaatcertificaten heeft gegenereerd.

   U verwijst naar deze certificaatbestanden in alle toepassingen op het downstreamapparaat die verbinding maken met IoT Hub. U kunt een service zoals [Azure Key Vault](../key-vault/index.yml) of een functie zoals secure copy protocol gebruiken [om](https://www.ssh.com/ssh/scp/) de certificaatbestanden te verplaatsen.

5. Bekijk, afhankelijk van de taal van uw voorkeur, voorbeelden van hoe naar X.509-certificaten kan worden verwezen in IoT-toepassingen:

   * C#: [X.509-beveiliging instellen in uw Azure IoT-hub](../iot-hub/iot-hub-security-x509-get-started.md#authenticate-your-x509-device-with-the-x509-certificates)
   * C: [iotedge_downstream_device_sample.c](https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples/iotedge_downstream_device_sample)
   * Node.js: [simple_sample_device_x509.js](https://github.com/Azure/azure-iot-sdk-node/blob/master/device/samples/simple_sample_device_x509.js)
   * Java: [SendEventX509.java](https://github.com/Azure/azure-iot-sdk-java/tree/master/device/iot-device-samples/send-event-x509)
   * Python: [send_message_x509.py](https://github.com/Azure/azure-iot-sdk-python/blob/master/azure-iot-device/samples/async-hub-scenarios/send_message_x509.py)

U kunt ook de [IoT-extensie voor Azure CLI gebruiken](https://github.com/Azure/azure-iot-cli-extension) om dezelfde bewerking voor het maken van apparaten te voltooien. In het volgende voorbeeld wordt de opdracht [az iot hub device-identity](/cli/azure/iot/hub/device-identity) gebruikt om een nieuw IoT-apparaat te maken met zelf-ondertekende X.509-verificatie en wordt een bovenliggend apparaat toegewezen:

```azurecli
az iot hub device-identity create -n {iothub name} -d {device ID} --pd {gateway device ID} --am x509_thumbprint --ptp {primary thumbprint} --stp {secondary thumbprint}
```

Vervolgens haalt [u de gegevens op en connection string](#retrieve-and-modify-connection-string) zodat uw apparaat weet dat er verbinding moet worden via de gateway.

### <a name="x509-ca-signed-authentication"></a>X.509 CA-ondertekende verificatie

Voor ondertekende verificatie van de X.509-certificeringsinstantie (CA) hebt u een basis-CA-certificaat nodig dat is geregistreerd in IoT Hub dat u gebruikt om certificaten voor uw downstreamapparaat te ondertekenen. Elk apparaat dat gebruik maakt van een certificaat dat is problemen met het basis-CA-certificaat of een van de tussenliggende certificaten, mag worden geverifieerd.

Deze sectie is gebaseerd op de instructies die worden beschreven in IoT Hub artikel [X.509-beveiliging instellen in uw Azure IoT-hub.](../iot-hub/iot-hub-security-x509-get-started.md)

1. Maak met behulp van uw CA-certificaat twee apparaatcertificaten (primair en secundair) voor het downstreamapparaat.

   Als u geen certificeringsinstantie hebt om X.509-certificaten te maken, kunt u de democertificaatscripts van IoT Edge gebruiken om [downstreamapparaatcertificaten te maken.](how-to-create-test-certificates.md#create-downstream-device-certificates) Volg de stappen voor het maken van ca-ondertekende certificaten. Gebruik hetzelfde basis-CA-certificaat dat de certificaten voor uw gatewayapparaat heeft gegenereerd.

2. Volg de instructies in de sectie [X.509 CA-certificaten](../iot-hub/iot-hub-security-x509-get-started.md#register-x509-ca-certificates-to-your-iot-hub) registreren bij uw IoT-hub van *X.509-beveiliging instellen in uw Azure IoT-hub.* In deze sectie voert u de volgende stappen uit:

   1. Upload een basis-CA-certificaat. Als u de democertificaten gebruikt, is de basis-CA **\<path> /certs/azure-iot-test-only.root.ca.cert.pem.**

   2. Controleer of u de eigenaar bent van dat basis-CA-certificaat.

3. Volg de instructies in de sectie Een [X.509-apparaat voor uw IoT-hub](../iot-hub/iot-hub-security-x509-get-started.md#create-an-x509-device-for-your-iot-hub) maken van *X.509-beveiliging instellen in uw Azure IoT-hub.* In deze sectie voert u de volgende stappen uit:

   1. Voeg een nieuw apparaat toe. Geef een kleine naam op voor **de apparaat-id** en kies het verificatietype **X.509 CA ondertekend.**

   2. Stel een bovenliggend apparaat in. Selecteer **Een bovenliggend apparaat instellen** en kies het IoT Edge gatewayapparaat dat de verbinding met het apparaat IoT Hub.

4. Maak een certificaatketen voor uw downstreamapparaat. Gebruik hetzelfde basis-CA-certificaat dat u hebt geüpload naar IoT Hub deze keten te maken. Gebruik dezelfde apparaat-id in kleine letters die u in de portal aan uw apparaat-id hebt gegeven.

5. Kopieer het apparaatcertificaat en de sleutels naar een locatie op het downstreamapparaat. Verplaats ook een kopie van het gedeelde basis-CA-certificaat dat zowel het gatewayapparaatcertificaat als de downstreamapparaatcertificaten heeft gegenereerd.

   U verwijst naar deze bestanden in alle toepassingen op het downstreamapparaat die verbinding maken met IoT Hub. U kunt een service zoals [Azure Key Vault](../key-vault/index.yml) of een functie zoals secure copy protocol gebruiken [om](https://www.ssh.com/ssh/scp/) de certificaatbestanden te verplaatsen.

6. Bekijk, afhankelijk van de taal van uw voorkeur, voorbeelden van hoe naar X.509-certificaten kan worden verwezen in IoT-toepassingen:

   * C#: [X.509-beveiliging instellen in uw Azure IoT-hub](../iot-hub/iot-hub-security-x509-get-started.md#authenticate-your-x509-device-with-the-x509-certificates)
   * C: [iotedge_downstream_device_sample.c](https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples/iotedge_downstream_device_sample)
   * Node.js: [simple_sample_device_x509.js](https://github.com/Azure/azure-iot-sdk-node/blob/master/device/samples/simple_sample_device_x509.js)
   * Java: [SendEventX509.java](https://github.com/Azure/azure-iot-sdk-java/tree/master/device/iot-device-samples/send-event-x509)
   * Python: [send_message_x509.py](https://github.com/Azure/azure-iot-sdk-python/blob/master/azure-iot-device/samples/async-hub-scenarios/send_message_x509.py)

U kunt ook de [IoT-extensie voor Azure CLI gebruiken](https://github.com/Azure/azure-iot-cli-extension) om dezelfde bewerking voor het maken van apparaten uit te voeren. In het volgende voorbeeld wordt de [opdracht az iot hub device-identity](/cli/azure/iot/hub/device-identity) gebruikt om een nieuw IoT-apparaat te maken met X.509 CA-ondertekende verificatie en een bovenliggend apparaat toe te wijzen:

```azurecli
az iot hub device-identity create -n {iothub name} -d {device ID} --pd {gateway device ID} --am x509_ca
```

Vervolgens haalt [u de gegevens op en connection string](#retrieve-and-modify-connection-string) zodat uw apparaat weet dat er verbinding moet worden via de gateway.

## <a name="retrieve-and-modify-connection-string"></a>Gegevens ophalen en connection string

Nadat u een IoT-apparaat-id in de portal hebt aanmaken, kunt u de primaire of secundaire sleutels ophalen. Een van deze sleutels moet worden opgenomen in de connection string toepassingen gebruiken om te communiceren met IoT Hub. Voor symmetrische sleutelverificatie biedt IoT Hub voor uw gemak de volledige connection string in de apparaatdetails. U moet extra informatie over het gatewayapparaat toevoegen aan de connection string.

Verbindingsreeksen voor downstreamapparaten hebben de volgende onderdelen nodig:

* De IoT-hub waar het apparaat verbinding mee maakt: `Hostname={iothub name}.azure-devices.net`
* De apparaat-id die is geregistreerd bij de hub: `DeviceID={device ID}`
* De verificatiemethode, ongeacht of het gaat om symmetrische sleutels of X.509-certificaten
  * Als u verificatie met een symmetrische sleutel gebruikt, geeft u de primaire of secundaire sleutel op: `SharedAccessKey={key}`
  * Als u X.509-certificaatverificatie gebruikt, geeft u een vlag op: `x509=true`
* Het gatewayapparaat dat het apparaat gebruikt om verbinding mee te maken. Geef de **hostnaamwaarde** op uit IoT Edge configuratiebestand van het gatewayapparaat: `GatewayHostName={gateway hostname}`

Alles bij elkaar ziet een volledig connection string er als volgende uit:

```console
HostName=myiothub.azure-devices.net;DeviceId=myDownstreamDevice;SharedAccessKey=xxxyyyzzz;GatewayHostName=myGatewayDevice
```

Of:

```console
HostName=myiothub.azure-devices.net;DeviceId=myDownstreamDevice;x509=true;GatewayHostName=myGatewayDevice
```

Dankzij de bovenliggende/onderliggende relatie kunt u de verbinding connection string door de gateway rechtstreeks aan te roepen als de verbindingshost. Bijvoorbeeld:

```console
HostName=myGatewayDevice;DeviceId=myDownstreamDevice;SharedAccessKey=xxxyyyzzz
```

U gebruikt deze gewijzigde connection string in het volgende artikel van de transparante gatewayreeks.

## <a name="next-steps"></a>Volgende stappen

Op dit moment hebt u een IoT Edge dat is geregistreerd bij uw IoT-hub en is geconfigureerd als een transparante gateway. U hebt ook een downstreamapparaat dat is geregistreerd bij uw IoT-hub en verwijst naar het gatewayapparaat.

Vervolgens moet u uw downstreamapparaat configureren om het gatewayapparaat te vertrouwen en er veilig verbinding mee te maken. Ga verder met het volgende artikel in de transparante gatewayreeks: [Een downstreamapparaat verbinden met een Azure IoT Edge gateway](how-to-connect-downstream-device.md).