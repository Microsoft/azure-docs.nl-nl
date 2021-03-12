---
title: Downstream-apparaten verifiëren-Azure IoT Edge | Microsoft Docs
description: Downstream-apparaten of-blad apparaten verifiëren voor IoT Hub en de verbinding via Azure IoT Edge gateway apparaten door sturen.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 10/15/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: c799e38092c5983b4ad0e3daea6aae99934c7302
ms.sourcegitcommit: 5f32f03eeb892bf0d023b23bd709e642d1812696
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/12/2021
ms.locfileid: "103200888"
---
# <a name="authenticate-a-downstream-device-to-azure-iot-hub"></a>Een downstream-apparaat verifiëren voor Azure IoT Hub

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

In een transparant Gateway scenario moeten downstream-apparaten (ook wel Leaf-apparaten of onderliggende apparaten genoemd) identiteiten hebben in IoT Hub, zoals elk ander apparaat. In dit artikel worden de opties beschreven voor het verifiëren van een downstream-apparaat bij IoT Hub, en wordt vervolgens gedemonstreerd hoe de gateway verbinding moet worden gedeclareerd.

Er zijn drie algemene stappen voor het instellen van een geslaagde transparante gateway verbinding. In dit artikel wordt de tweede stap behandeld:

1. Configureer het gateway apparaat als een server zodat downstream-apparaten veilig verbinding kunnen maken. Stel de gateway in voor het ontvangen van berichten van downstream-apparaten en stuur ze naar de juiste bestemming. Zie [een IOT edge apparaat configureren om te fungeren als transparante gateway](how-to-create-transparent-gateway.md)voor deze stappen.
2. **Maak een apparaat-id voor het downstream-apparaat, zodat het kan worden geverifieerd met IoT Hub. Configureer het downstream-apparaat om berichten te verzenden via het gateway apparaat.**
3. Verbind het downstream-apparaat met het gateway apparaat en begin met het verzenden van berichten. Zie [een downstream-apparaat verbinden met een Azure IOT Edge-gateway](how-to-connect-downstream-device.md)voor deze stappen.

Downstream-apparaten kunnen met IoT Hub worden geverifieerd met behulp van een van de volgende drie methoden: symmetrische sleutels (ook wel gedeelde toegangs sleutels genoemd), X. 509 zelfondertekende certificaten of X. 509 Certificate Authority (CA) ondertekende certificaten. De verificatie stappen zijn vergelijkbaar met de stappen die worden gebruikt om een niet-IoT-edge-apparaat in te stellen met IoT Hub, met kleine verschillen in het declareren van de gateway relatie.

Het automatisch inrichten van downstream-apparaten met Azure IoT Hub Device Provisioning Service (DPS) wordt niet ondersteund.

## <a name="prerequisites"></a>Vereisten

Voer de stappen in [een IOT edge apparaat configureren om te fungeren als transparante gateway](how-to-create-transparent-gateway.md).

Als u X. 509-verificatie gebruikt, kunt u certificaten voor het downstream-apparaat genereren. Hebben hetzelfde basis-CA-certificaat en het certificaat voor het genereren van certificaten dat u hebt gebruikt voor het transparante gateway artikel dat u opnieuw kunt gebruiken.

Dit artikel verwijst naar de *hostnaam* van de gateway op verschillende punten. De hostnaam van de gateway wordt gedeclareerd in de para meter **hostname** van het configuratie bestand op het IOT Edge gateway-apparaat. In de connection string van het downstream-apparaat waarnaar wordt verwezen. De hostnaam van de gateway moet kunnen worden omgezet in een IP-adres, hetzij met behulp van DNS of een vermelding in een hostbestand op het downstream-apparaat.

## <a name="register-device-with-iot-hub"></a>Apparaat registreren bij IoT Hub

Kies hoe u het downstream-apparaat wilt verifiëren met IoT Hub:

* [Symmetrische-sleutel verificatie](#symmetric-key-authentication): IOT hub maakt een sleutel die u op het downstream-apparaat plaatst. Wanneer het apparaat wordt geverifieerd, wordt door IoT Hub gecontroleerd of de twee sleutels overeenkomen. U hoeft geen extra certificaten te maken voor het gebruik van symmetrische sleutel verificatie.

  Deze methode is sneller om aan de slag te gaan als u gateways in een ontwikkelings-of test scenario test.

* [X. 509 zelfondertekende authenticatie](#x509-self-signed-authentication): ook wel vingerafdruk verificatie genoemd, omdat u de vinger afdruk van het X. 509-certificaat van het apparaat deelt met IOT hub.

  Verificatie van certificaten wordt aanbevolen voor apparaten in productie scenario's.

* [X. 509 CA-ondertekende verificatie](#x509-ca-signed-authentication): upload het basis-CA-certificaat naar IOT hub. Wanneer apparaten het X. 509-certificaat voor verificatie presen teren, IoT Hub controleert of het deel uitmaakt van een vertrouwens keten die is ondertekend door hetzelfde basis-CA-certificaat.

  Verificatie van certificaten wordt aanbevolen voor apparaten in productie scenario's.

### <a name="symmetric-key-authentication"></a>Verificatie met symmetrische sleutel

Verificatie op basis van symmetrische sleutels, of verificatie van de gedeelde toegangs sleutel, is de eenvoudigste manier om te verifiëren met IoT Hub. Bij symmetrische sleutel verificatie wordt een base64-sleutel gekoppeld aan uw IoT-apparaat-ID in IoT Hub. U neemt die sleutel op in uw IoT-toepassingen, zodat uw apparaat deze kan presen teren wanneer er verbinding wordt gemaakt met IoT Hub.

Voeg een nieuw IoT-apparaat toe aan uw IoT-hub met behulp van de Azure Portal, Azure CLI of de IoT-extensie voor Visual Studio code. Houd er rekening mee dat downstream-apparaten moeten worden geïdentificeerd in IoT Hub als gewone IoT-apparaten, niet IoT Edge apparaten.

Wanneer u de nieuwe apparaat-id maakt, geeft u de volgende informatie op:

* Maak een ID voor uw apparaat.

* Selecteer **symmetrische sleutel** als het verificatie type.

* Selecteer **een bovenliggend apparaat instellen** en selecteer het IOT Edge gateway apparaat waarmee dit downstream-apparaat verbinding maakt. U kunt het bovenliggende item later altijd wijzigen.

   ![Apparaat-ID met symmetrische-sleutel verificatie maken in de portal](./media/how-to-authenticate-downstream-device/symmetric-key-portal.png)

   >[!NOTE]
   >Het instellen van het bovenliggende apparaat wordt gebruikt als een optionele stap voor downstream-apparaten die gebruikmaken van symmetrische sleutel verificatie. Vanaf IoT Edge versie 1.1.0 moet elk downstream-apparaat echter worden toegewezen aan een bovenliggend apparaat.
   >
   >U kunt de IoT Edge hub configureren om terug te gaan naar het vorige gedrag door de omgevings variabele **AuthenticationMode** in te stellen op de waarde **CloudAndScope**.

U kunt ook de [IOT-extensie voor Azure cli](https://github.com/Azure/azure-iot-cli-extension) gebruiken om dezelfde bewerking te volt ooien. In het volgende voor beeld wordt de opdracht [AZ IOT hub apparaat-Identity](/cli/azure/ext/azure-iot/iot/hub/device-identity) gebruikt voor het maken van een nieuw IOT-apparaat met symmetrische sleutel verificatie en het toewijzen van een bovenliggend apparaat:

```azurecli
az iot hub device-identity create -n {iothub name} -d {new device ID} --pd {existing gateway device ID}
```

Vervolgens [haalt u de Connection String op en wijzigt](#retrieve-and-modify-connection-string) u zo dat uw apparaat verbinding kan maken via de gateway.

### <a name="x509-self-signed-authentication"></a>X. 509 zelfondertekende authenticatie

Voor X. 509 zelfondertekende verificatie, ook wel vingerafdruk verificatie genoemd, moet u certificaten maken om op uw downstream-apparaat te plaatsen. Deze certificaten bevatten een vinger afdruk die u met IoT Hub voor verificatie kunt delen.

1. Maak met behulp van uw CA-certificaat twee apparaats certificaten (primair en secundair) voor het downstream-apparaat.

   Als u geen certificerings instantie hebt om X. 509-certificaten te maken, kunt u de certificaat scripts van de IoT Edge-demo gebruiken om [stroomafwaartse certificaten](how-to-create-test-certificates.md#create-downstream-device-certificates)voor het apparaat te maken. Volg de stappen voor het maken van zelfondertekende certificaten. Gebruik hetzelfde basis-CA-certificaat dat de certificaten voor uw gateway apparaat heeft gegenereerd.

   Als u uw eigen certificaten maakt, moet u ervoor zorgen dat de onderwerpnaam van het certificaat van het apparaat is ingesteld op de apparaat-ID die u gebruikt bij het registreren van het IoT-apparaat in de Azure IoT Hub. Deze instelling is vereist voor verificatie.

2. Haal de SHA1-vinger afdruk (een vinger afdruk in de IoT Hub Interface) op van elk certificaat, wat een 40 hexadecimale teken reeks is. Gebruik de volgende openssl-opdracht om het certificaat weer te geven en de vinger afdruk te zoeken:

   * Windows:

     ```PowerShell
     openssl x509 -in <path to primary device certificate>.cert.pem -text -fingerprint
     ```

   * Linux:

     ```Bash
     openssl x509 -in <path to primary device certificate>.cert.pem -text -fingerprint | sed 's/[:]//g'
     ```

   Voer deze opdracht twee keer uit en eenmaal voor het primaire certificaat en eenmaal voor het secundaire certificaat. U geeft de vinger afdrukken voor beide certificaten op wanneer u een nieuw IoT-apparaat registreert met zelfondertekende X. 509-certificaten.

3. Navigeer naar uw IoT-hub in het Azure Portal en maak een nieuwe IoT-apparaat-id met de volgende waarden:

   * Geef de **apparaat-id** op die overeenkomt met de onderwerpnaam van de certificaten van uw apparaat.
   * Selecteer **X. 509 zelf ondertekend** als verificatie type.
   * Plak de hexadecimale teken reeksen die u hebt gekopieerd van het primaire en het secundaire certificaat van uw apparaat.
   * Selecteer **een bovenliggend apparaat instellen** en kies het IOT Edge gateway apparaat waarmee dit downstream-apparaat verbinding maakt. U kunt het bovenliggende item later altijd wijzigen.

   ![Apparaat-ID maken met X. 509 zelfondertekende auth in Portal](./media/how-to-authenticate-downstream-device/x509-self-signed-portal.png)

4. Kopieer zowel het primaire als het secundaire certificaat van het apparaat en de sleutels naar een locatie op het downstream-apparaat. Verplaats ook een kopie van het gedeelde basis-CA-certificaat dat het certificaat van de gateway-apparaat en de downstream-apparaatstuurprogramma's heeft gegenereerd.

   U verwijst naar deze certificaat bestanden in alle toepassingen op het downstream-apparaat die verbinding maken met IoT Hub. U kunt een service zoals [Azure Key Vault](../key-vault/index.yml) of een functie zoals [Secure Copy Protocol](https://www.ssh.com/ssh/scp/) gebruiken om de certificaat bestanden te verplaatsen.

5. Bekijk, afhankelijk van de taal van uw voor keur, de voor beelden van hoe X. 509-certificaten kunnen worden verwezen in IoT-toepassingen:

   * C#: [X. 509-beveiliging instellen in uw Azure IOT hub](../iot-hub/iot-hub-security-x509-get-started.md#authenticate-your-x509-device-with-the-x509-certificates)
   * C: [iotedge_downstream_device_sample. c](https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples/iotedge_downstream_device_sample)
   * Node.js: [simple_sample_device_x509.js](https://github.com/Azure/azure-iot-sdk-node/blob/master/device/samples/simple_sample_device_x509.js)
   * Java: [SendEventX509. java](https://github.com/Azure/azure-iot-sdk-java/tree/master/device/iot-device-samples/send-event-x509)
   * Python: [send_message_x509. py](https://github.com/Azure/azure-iot-sdk-python/blob/master/azure-iot-device/samples/async-hub-scenarios/send_message_x509.py)

U kunt ook de [IOT-extensie voor Azure cli](https://github.com/Azure/azure-iot-cli-extension) gebruiken om de bewerking voor het maken van het apparaat te volt ooien. In het volgende voor beeld wordt de opdracht [AZ IOT hub apparaat-Identity](/cli/azure/ext/azure-iot/iot/hub/device-identity) gebruikt voor het maken van een nieuw IOT-apparaat met X. 509 zelfondertekende authenticatie en het toewijzen van een bovenliggend apparaat:

```azurecli
az iot hub device-identity create -n {iothub name} -d {device ID} --pd {gateway device ID} --am x509_thumbprint --ptp {primary thumbprint} --stp {secondary thumbprint}
```

Vervolgens [haalt u de Connection String op en wijzigt](#retrieve-and-modify-connection-string) u zo dat uw apparaat verbinding kan maken via de gateway.

### <a name="x509-ca-signed-authentication"></a>X. 509 CA-ondertekende verificatie

Voor X. 509 certificerings instantie (CA) ondertekende verificatie hebt u een basis-CA-certificaat geregistreerd in IoT Hub dat u gebruikt om certificaten voor uw downstream-apparaat te ondertekenen. Elk apparaat dat een certificaat gebruikt dat problemen heeft met het basis-CA-certificaat of een van de tussenliggende certificaten, mag worden geverifieerd.

Deze sectie is gebaseerd op de instructies in het artikel IoT Hub [X. 509-beveiliging in te stellen in uw Azure IOT hub](../iot-hub/iot-hub-security-x509-get-started.md).

1. Maak met behulp van uw CA-certificaat twee apparaats certificaten (primair en secundair) voor het downstream-apparaat.

   Als u geen certificerings instantie hebt om X. 509-certificaten te maken, kunt u de certificaat scripts van de IoT Edge-demo gebruiken om [stroomafwaartse certificaten](how-to-create-test-certificates.md#create-downstream-device-certificates)voor het apparaat te maken. Volg de stappen voor het maken van CA-ondertekende certificaten. Gebruik hetzelfde basis-CA-certificaat dat de certificaten voor uw gateway apparaat heeft gegenereerd.

2. Volg de instructies in de sectie [509 van CA-certificaten registreren bij uw IOT-hub](../iot-hub/iot-hub-security-x509-get-started.md#register-x509-ca-certificates-to-your-iot-hub) voor het instellen van *X. 509-beveiliging in uw Azure IOT hub*. In deze sectie voert u de volgende stappen uit:

   1. Upload een basis-CA-certificaat. Als u de demo certificaten gebruikt, is de basis-CA **\<path> /certs/Azure-IOT-test-only.root.ca.cert.pem**.

   2. Controleer of u bent eigenaar van het basis-CA-certificaat.

3. Volg de instructies in het gedeelte [een x. 509-apparaat voor uw IOT-hub maken van de](../iot-hub/iot-hub-security-x509-get-started.md#create-an-x509-device-for-your-iot-hub) *X. 509-beveiliging instellen in uw Azure IOT hub*. In deze sectie voert u de volgende stappen uit:

   1. Een nieuw apparaat toevoegen. Geef een kleine naam op voor de **apparaat-id** en kies het verificatie type **X. 509 certificerings instantie ondertekend**.

   2. Stel een bovenliggend apparaat in. Selecteer **een bovenliggend apparaat instellen** en kies het IOT Edge gateway apparaat dat de verbinding met IOT hub zal bieden.

4. Maak een certificaat keten voor het downstream-apparaat. Gebruik hetzelfde basis-CA-certificaat dat u hebt geüpload naar IoT Hub om deze keten te maken. Gebruik dezelfde kleine apparaat-ID die u in de portal hebt opgegeven voor de identiteit van uw apparaat.

5. Kopieer het certificaat en de sleutels van het apparaat naar een wille keurige locatie op het downstream-apparaat. Verplaats ook een kopie van het gedeelde basis-CA-certificaat dat het certificaat van de gateway-apparaat en de downstream-apparaatstuurprogramma's heeft gegenereerd.

   U verwijst naar deze bestanden in alle toepassingen op het downstream-apparaat die verbinding maken met IoT Hub. U kunt een service zoals [Azure Key Vault](../key-vault/index.yml) of een functie zoals [Secure Copy Protocol](https://www.ssh.com/ssh/scp/) gebruiken om de certificaat bestanden te verplaatsen.

6. Bekijk, afhankelijk van de taal van uw voor keur, de voor beelden van hoe X. 509-certificaten kunnen worden verwezen in IoT-toepassingen:

   * C#: [X. 509-beveiliging instellen in uw Azure IOT hub](../iot-hub/iot-hub-security-x509-get-started.md#authenticate-your-x509-device-with-the-x509-certificates)
   * C: [iotedge_downstream_device_sample. c](https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples/iotedge_downstream_device_sample)
   * Node.js: [simple_sample_device_x509.js](https://github.com/Azure/azure-iot-sdk-node/blob/master/device/samples/simple_sample_device_x509.js)
   * Java: [SendEventX509. java](https://github.com/Azure/azure-iot-sdk-java/tree/master/device/iot-device-samples/send-event-x509)
   * Python: [send_message_x509. py](https://github.com/Azure/azure-iot-sdk-python/blob/master/azure-iot-device/samples/async-hub-scenarios/send_message_x509.py)

U kunt ook de [IOT-extensie voor Azure cli](https://github.com/Azure/azure-iot-cli-extension) gebruiken om de bewerking voor het maken van het apparaat te volt ooien. In het volgende voor beeld wordt de opdracht [AZ IOT hub apparaat-Identity](/cli/azure/ext/azure-iot/iot/hub/device-identity) gebruikt voor het maken van een nieuw IOT-apparaat met X. 509 certificerings instantie ondertekend verificatie en wijst een bovenliggend apparaat toe:

```azurecli
az iot hub device-identity create -n {iothub name} -d {device ID} --pd {gateway device ID} --am x509_ca
```

Vervolgens [haalt u de Connection String op en wijzigt](#retrieve-and-modify-connection-string) u zo dat uw apparaat verbinding kan maken via de gateway.

## <a name="retrieve-and-modify-connection-string"></a>connection string ophalen en wijzigen

Nadat u in de portal een IoT-apparaat-id hebt gemaakt, kunt u de primaire of secundaire sleutels ophalen. Een van deze sleutels moet worden opgenomen in de connection string die toepassingen gebruiken om te communiceren met IoT Hub. Voor de verificatie van symmetrische sleutels biedt IoT Hub de volledig opgemaakte connection string in de apparaatgegevens voor uw gemak. U moet extra informatie over het gateway apparaat toevoegen aan de connection string.

Verbindings reeksen voor downstream-apparaten hebben de volgende onderdelen nodig:

* De IoT-hub waarmee het apparaat verbinding maakt: `Hostname={iothub name}.azure-devices.net`
* De apparaat-ID die is geregistreerd bij de hub: `DeviceID={device ID}`
* De verificatie methode, of symmetrische sleutel of X. 509-certificaten
  * Als symmetrische sleutel verificatie wordt gebruikt, geeft u de primaire of secundaire sleutel: `SharedAccessKey={key}`
  * Als u X. 509-certificaat verificatie gebruikt, geeft u een vlag op: `x509=true`
* Het gateway apparaat waarmee het apparaat verbinding maakt. Geef de **hostnaam** -waarde op uit het configuratie bestand van het IOT Edge gateway-apparaat: `GatewayHostName={gateway hostname}`

Alle samen, een volledig connection string ziet er als volgt uit:

```console
HostName=myiothub.azure-devices.net;DeviceId=myDownstreamDevice;SharedAccessKey=xxxyyyzzz;GatewayHostName=myGatewayDevice
```

Of

```console
HostName=myiothub.azure-devices.net;DeviceId=myDownstreamDevice;x509=true;GatewayHostName=myGatewayDevice
```

Dankzij de relatie tussen bovenliggende en onderliggende items kunt u de connection string vereenvoudigen door de gateway rechtstreeks aan te roepen als de host van de verbinding. Bijvoorbeeld:

```console
HostName=myGatewayDevice;DeviceId=myDownstreamDevice;SharedAccessKey=xxxyyyzzz
```

U gebruikt deze gewijzigde connection string in het volgende artikel van de transparante gateway reeks.

## <a name="next-steps"></a>Volgende stappen

U hebt op dit moment een IoT Edge apparaat dat is geregistreerd bij uw IoT-hub en geconfigureerd als een transparante gateway. U hebt ook een downstream-apparaat dat is geregistreerd bij uw IoT-hub en het gateway apparaat wijst.

Vervolgens moet u het downstream-apparaat configureren om het gateway apparaat te vertrouwen en er veilig verbinding mee te maken. Ga verder met het volgende artikel in de transparante gateway-reeks, [sluit een downstream-apparaat aan op een Azure IOT Edge gateway](how-to-connect-downstream-device.md).