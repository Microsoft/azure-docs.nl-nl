---
author: dominicbetts
ms.author: dobett
ms.service: iot-pnp
ms.topic: include
ms.date: 03/31/2021
ms.openlocfilehash: 6c97ee01dd1ad5b669142d74a02bada010525e81
ms.sourcegitcommit: bfa7d6ac93afe5f039d68c0ac389f06257223b42
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/06/2021
ms.locfileid: "106491070"
---
## <a name="prerequisites"></a>Vereisten

U hebt de volgende resources nodig om de stappen in dit artikel uit te voeren:

* Een Azure IoT Central-toepassing gemaakt met behulp van de sjabloon **Aangepaste toepassing**. Zie voor meer informatie de [snelstart over het maken van een toepassing](../articles/iot-central/core/quick-deploy-iot-central.md). De toepassing moet op of na 14 juli 2020 zijn gemaakt.
* Een ontwikkelmachine waarop [Node.js](https://nodejs.org/) versie 6 of hoger is geïnstalleerd. Voer `node --version` uit op de opdrachtregel om uw versie te controleren. In de instructies in deze zelfstudie wordt ervan uitgegaan dat u de opdracht **node** uitvoert vanaf de Windows-opdrachtprompt. U kunt Node.js echter gebruiken met verschillende andere besturingssystemen.
* Een lokale kopie van de [Microsoft Azure IoT-SDK voor Node.js](https://github.com/Azure/azure-iot-sdk-node) GitHub-opslagplaats die de voorbeeldcode bevat. Gebruik deze koppeling om een kopie van de opslagplaats te downloaden: [ZIP-bestand downloaden](https://github.com/Azure/azure-iot-sdk-node/archive/master.zip). Pak het bestand vervolgens uit op een geschikte locatie op uw lokale computer.

## <a name="review-the-code"></a>De code bekijken

Open in de kopie van de Microsoft Azure IoT SDK voor Node.js die u eerder hebt gedownload het bestand *Azure-IOT-SDK-node/apparaat/samples/PnP/pnpTemperatureController.js* in een tekst editor.

Wanneer u het voorbeeld uitvoert om verbinding te maken met IoT Central, wordt daarvoor gebruikgemaakt van de Device Provisioning Service (DPS) om het apparaat te registreren en een verbindingsreeks te genereren. Het voorbeeld haalt de DPS-verbindingsgegevens die nodig zijn op uit de opdrachtregelomgeving.

De methode `main`:

* Hiermee wordt een `client`-object gemaakt en wordt de id van het `dtmi:com:example:TemperatureController;2`-model ingesteld voordat de verbinding wordt geopend. IoT Central gebruikt de model-id om de apparaatsjabloon voor dit apparaat te identificeren of te genereren. Zie [Een apparaat koppelen met een apparaatsjabloon](../articles/iot-central/core/concepts-get-connected.md#associate-a-device-with-a-device-template) voor meer informatie.
* Maakt opdracht-handlers voor drie opdrachten.
* Start een lus voor elk Thermo staat-onderdeel om elke vijf seconden een temperatuur telemetrie te verzenden.
* Start een lus voor het standaard onderdeel om elke 6 seconden een telemetrie-grootte te sturen.
* Hiermee wordt de `maxTempSinceLastReboot` eigenschap voor elk onderdeel van de Thermo staat verzonden.
* Hiermee worden de eigenschappen van de apparaatgegevens verzonden.
* Hiermee worden schrijf bare eigenschappen voor de drie onderdelen gemaakt.

```javascript
async function main() {
  // ...

  // fromConnectionString must specify a transport, coming from any transport package.
  const client = Client.fromConnectionString(deviceConnectionString, Protocol);
  console.log('Connecting using connection string: ' + deviceConnectionString);
  let resultTwin;

  try {
    // Add the modelId here
    await client.setOptions(modelIdObject);
    await client.open();
    console.log('Enabling the commands on the client');
    client.onDeviceMethod(commandNameGetMaxMinReport1, commandHandler);
    client.onDeviceMethod(commandNameGetMaxMinReport2, commandHandler);
    client.onDeviceMethod(commandNameReboot, commandHandler);

    // Send Telemetry after some interval
    let index1 = 0;
    let index2 = 0;
    let index3 = 0;
    intervalToken1 = setInterval(() => {
      const data = JSON.stringify(thermostat1.updateSensor().getCurrentTemperatureObject());
      sendTelemetry(client, data, index1, thermostat1ComponentName).catch((err) => console.log('error ', err.toString()));
      index1 += 1;
    }, 5000);

    intervalToken2 = setInterval(() => {
      const data = JSON.stringify(thermostat2.updateSensor().getCurrentTemperatureObject());
      sendTelemetry(client, data, index2, thermostat2ComponentName).catch((err) => console.log('error ', err.toString()));
      index2 += 1;
    }, 5500);


    intervalToken3 = setInterval(() => {
      const data = JSON.stringify({ workingset: 1 + (Math.random() * 90) });
      sendTelemetry(client, data, index3, null).catch((err) => console.log('error ', err.toString()));
      index3 += 1;
    }, 6000);

    // attach a standard input exit listener
    exitListener(client);

    try {
      resultTwin = await client.getTwin();
      // Only report readable properties
      const patchRoot = helperCreateReportedPropertiesPatch({ serialNumber: serialNumber }, null);
      const patchThermostat1Info = helperCreateReportedPropertiesPatch({
        maxTempSinceLastReboot: thermostat1.getMaxTemperatureValue(),
      }, thermostat1ComponentName);

      const patchThermostat2Info = helperCreateReportedPropertiesPatch({
        maxTempSinceLastReboot: thermostat2.getMaxTemperatureValue(),
      }, thermostat2ComponentName);

      const patchDeviceInfo = helperCreateReportedPropertiesPatch({
        manufacturer: 'Contoso Device Corporation',
        model: 'Contoso 47-turbo',
        swVersion: '10.89',
        osName: 'Contoso_OS',
        processorArchitecture: 'Contoso_x86',
        processorManufacturer: 'Contoso Industries',
        totalStorage: 65000,
        totalMemory: 640,
      }, deviceInfoComponentName);

      // the below things can only happen once the twin is there
      updateComponentReportedProperties(resultTwin, patchRoot, null);
      updateComponentReportedProperties(resultTwin, patchThermostat1Info, thermostat1ComponentName);
      updateComponentReportedProperties(resultTwin, patchThermostat2Info, thermostat2ComponentName);
      updateComponentReportedProperties(resultTwin, patchDeviceInfo, deviceInfoComponentName);
      desiredPropertyPatchListener(resultTwin, [thermostat1ComponentName, thermostat2ComponentName, deviceInfoComponentName]);
    } catch (err) {
      console.error('could not retrieve twin or report twin properties\n' + err.toString());
    }
  } catch (err) {
    console.error('could not connect Plug and Play client or could not attach interval function for telemetry\n' + err.toString());
  }
}
```

De functie `provisionDevice` laat zien hoe het apparaat DPS gebruikt om te registreren en verbinding te maken met IoT Central. De payload omvat de model-id die in IoT Central wordt gebruikt om [het apparaat te koppelen aan een apparaatsjabloon](../articles/iot-central/core/concepts-get-connected.md#associate-a-device-with-a-device-template):

```javascript
async function provisionDevice(payload) {
  var provSecurityClient = new SymmetricKeySecurityClient(registrationId, symmetricKey);
  var provisioningClient = ProvisioningDeviceClient.create(provisioningHost, idScope, new ProvProtocol(), provSecurityClient);

  if (!!(payload)) {
    provisioningClient.setProvisioningPayload(payload);
  }

  try {
    let result = await provisioningClient.register();
    deviceConnectionString = 'HostName=' + result.assignedHub + ';DeviceId=' + result.deviceId + ';SharedAccessKey=' + symmetricKey;
    console.log('registration succeeded');
    console.log('assigned hub=' + result.assignedHub);
    console.log('deviceId=' + result.deviceId);
    console.log('payload=' + JSON.stringify(result.payload));
  } catch (err) {
    console.error("error registering device: " + err.toString());
  }
}
```

De functie `sendTelemetry` laat zien hoe het apparaat de temperatuurtelemetriegegevens verzendt naar IoT Central. Voor telemetrie van-onderdelen voegt het een eigenschap toe `$.sub` met de naam van de component:

```javascript
async function sendTelemetry(deviceClient, data, index, componentName) {
  if (!!(componentName)) {
    console.log('Sending telemetry message %d from component: %s ', index, componentName);
  } else {
    console.log('Sending telemetry message %d from root interface', index);
  }
  const msg = new Message(data);
  if (!!(componentName)) {
    msg.properties.add(messageSubjectProperty, componentName);
  }
  msg.contentType = 'application/json';
  msg.contentEncoding = 'utf-8';
  await deviceClient.sendEvent(msg);
}
```

De `main` methode maakt gebruik van een hulp methode met de naam `helperCreateReportedPropertiesPatch` om update-berichten van eigenschappen te maken. Deze methode heeft een optionele para meter voor het opgeven van het onderdeel dat de eigenschap verzendt.:

```javascript
const helperCreateReportedPropertiesPatch = (propertiesToReport, componentName) => {
  let patch;
  if (!!(componentName)) {
    patch = { };
    propertiesToReport.__t = 'c';
    patch[componentName] = propertiesToReport;
  } else {
    patch = { };
    patch = propertiesToReport;
  }
  if (!!(componentName)) {
    console.log('The following properties will be updated for component: ' + componentName);
  } else {
    console.log('The following properties will be updated for root interface.');
  }
  console.log(patch);
  return patch;
};
```

De `main` methode maakt gebruik van de volgende methode voor het afhandelen van updates van Beschrijf bare eigenschappen van IOT Central. U ziet hoe de methode het antwoord bouwt met de versie en de status code:

```javascript
const desiredPropertyPatchListener = (deviceTwin, componentNames) => {
  deviceTwin.on('properties.desired', (delta) => {
    console.log('Received an update for device with value: ' + JSON.stringify(delta));
    Object.entries(delta).forEach(([key, values]) => {
      const version = delta.$version;
      if (!!(componentNames) && componentNames.includes(key)) { // then it is a component we are expecting
        const componentName = key;
        const patchForComponents = { [componentName]: {} };
        Object.entries(values).forEach(([propertyName, propertyValue]) => {
          if (propertyName !== '__t' && propertyName !== '$version') {
            console.log('Will update property: ' + propertyName + ' to value: ' + propertyValue + ' of component: ' + componentName);
            const propertyContent = { value: propertyValue };
            propertyContent.ac = 200;
            propertyContent.ad = 'Successfully executed patch';
            propertyContent.av = version;
            patchForComponents[componentName][propertyName] = propertyContent;
          }
        });
        updateComponentReportedProperties(deviceTwin, patchForComponents, componentName);
      }
      else if  (key !== '$version') { // individual property for root
        const patchForRoot = { };
        console.log('Will update property: ' + key + ' to value: ' + values + ' for root');
        const propertyContent = { value: values };
        propertyContent.ac = 200;
        propertyContent.ad = 'Successfully executed patch';
        propertyContent.av = version;
        patchForRoot[key] = propertyContent;
        updateComponentReportedProperties(deviceTwin, patchForRoot, null);
      }
    });
  });
};
```

De- `main` methode maakt gebruik van de volgende methoden voor het afhandelen van opdrachten van IOT Central:

```javascript
const commandHandler = async (request, response) => {
  helperLogCommandRequest(request);
  switch (request.methodName) {
  case commandNameGetMaxMinReport1: {
    await sendCommandResponse(request, response, 200, thermostat1.getMaxMinReportObject());
    break;
  }
  case commandNameGetMaxMinReport2: {
    await sendCommandResponse(request, response, 200, thermostat2.getMaxMinReportObject());
    break;
  }
  case commandNameReboot: {
    await sendCommandResponse(request, response, 200, 'reboot response');
    break;
  }
  default:
    await sendCommandResponse(request, response, 404, 'unknown method');
    break;
  }
};

const sendCommandResponse = async (request, response, status, payload) => {
  try {
    await response.send(status, payload);
    console.log('Response to method: ' + request.methodName + ' sent successfully.' );
  } catch (err) {
    console.error('An error ocurred when sending a method response:\n' + err.toString());
  }
};
```

## <a name="get-connection-information"></a>Verbindingsgegevens ophalen

[!INCLUDE [iot-central-connection-configuration](iot-central-connection-configuration.md)]

## <a name="run-the-code"></a>De code uitvoeren

Als u de voorbeeld toepassing wilt uitvoeren, opent u een opdracht regel omgeving en navigeert u naar de map *Azure-IOT-SDK-node/apparaat/samples/PnP* die het *pnpTemperatureController.js* voorbeeld bestand bevat.

[!INCLUDE [iot-central-connection-environment](iot-central-connection-environment.md)]

Installeer de vereiste pakketten:

```cmd/sh
npm install
```

Voer het voorbeeld uit:

```cmd/sh
node pnpTemperatureController.js
```

In de volgende uitvoer ziet u hoe het apparaat zich registreert bij IoT Central en er verbinding mee maakt. Het voor beeld verzendt vervolgens de `maxTempSinceLastReboot` eigenschap van de twee Thermo staat-onderdelen voordat telemetrie wordt verzonden:

```output
registration succeeded
assigned hub=iotc-....azure-devices.net
deviceId=sample-device-01
payload=undefined
Connecting using connection string: HostName=iotc-....azure-devices.net;DeviceId=sample-device-01;SharedAccessKey=qdv...IpAo=
Enabling the commands on the client
Please enter q or Q to exit sample.
The following properties will be updated for root interface.
{ serialNumber: 'alwinexlepaho8329' }
The following properties will be updated for component: thermostat1
{ thermostat1: { maxTempSinceLastReboot: 1.5902294191855972, __t: 'c' } }
The following properties will be updated for component: thermostat2
{ thermostat2: { maxTempSinceLastReboot: 16.181771928614545, __t: 'c' } }
The following properties will be updated for component: deviceInformation
{ deviceInformation:
   { manufacturer: 'Contoso Device Corporation',
     model: 'Contoso 47-turbo',
     swVersion: '10.89',
     osName: 'Contoso_OS',
     processorArchitecture: 'Contoso_x86',
     processorManufacturer: 'Contoso Industries',
     totalStorage: 65000,
     totalMemory: 640,
     __t: 'c' } }
executed sample
Received an update for device with value: {"$version":1}
Properties have been reported for component: thermostat1
Properties have been reported for component: thermostat2
Properties have been reported for component: deviceInformation
Properties have been reported for root interface.
Sending telemetry message 0 from component: thermostat1 
Sending telemetry message 0 from component: thermostat2 
Sending telemetry message 0 from root interface
```

[!INCLUDE [iot-central-monitor-thermostat](iot-central-monitor-thermostat.md)]

U kunt zien hoe het apparaat reageert op opdrachten en updates van eigenschappen. De `getMaxMinReport` opdracht bevindt zich in het `thermostat2` onderdeel `reboot` . de opdracht bevindt zich in het standaard onderdeel. De `targetTemperature` Beschrijf bare eigenschap is ingesteld voor het onderdeel ' thermostat2 ':

```output
Received command request for command name: thermostat2*getMaxMinReport
The command request payload is:
2021-03-26T06:00:00.000Z
Response to method: thermostat2*getMaxMinReport sent successfully.

...

Received command request for command name: reboot
The command request payload is:
10
Response to method: reboot sent successfully.

...

Received an update for device with value: {"thermostat2":{"targetTemperature":76,"__t":"c"},"$version":2}
Will update property: targetTemperature to value: 76 of component: thermostat2
Properties have been reported for component: thermostat2
```
