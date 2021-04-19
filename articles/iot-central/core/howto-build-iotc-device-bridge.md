---
title: De Azure IoT Central-apparaatbrug | Microsoft Docs
description: Implementeer de IoT Central apparaatbrug om andere IoT-clouds te verbinden met uw IoT Central app. Andere IoT-clouds zijn Sigfox, Particle Device Cloud en The Things Network.
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 04/19/2021
ms.topic: how-to
ms.openlocfilehash: 6b535ecb80fae9f55eb6ab11751c26e0c6d0e9e5
ms.sourcegitcommit: 3ed0f0b1b66a741399dc59df2285546c66d1df38
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107713708"
---
# <a name="use-the-iot-central-device-bridge-to-connect-other-iot-clouds-to-iot-central"></a>Gebruik de IoT Central apparaatbrug om andere IoT-clouds te verbinden met IoT Central

*Dit artikel is van toepassing op beheerders.*

## <a name="azure-iot-central-device-bridge"></a>Azure IoT Central apparaatbrug

De IoT Central is een opensource-oplossing die andere IoT-clouds verbindt met uw IoT Central toepassing. Voorbeelden van andere IoT-clouds zijn [Sigfox,](https://www.sigfox.com/) [Particle Device Cloud](https://www.particle.io/)en The Things [Network.](https://www.thethingsnetwork.org/) De apparaatbrug werkt door gegevens van apparaten die zijn verbonden met andere IoT-clouds door te geven aan uw IoT Central toepassing. De apparaatbrug stuurt alleen gegevens door naar IoT Central, er worden geen opdrachten of updates van eigenschappen van IoT Central naar de apparaten.

Met de apparaatbrug kunt u de kracht van IoT Central combineren met apparaten zoals apparaten voor het bijhouden van activa die zijn verbonden met het low-power wide area network van Sigfox, de bewakingsapparaten voor de luchtkwaliteit op de deeltjesapparaatwolk of apparaten voor de bewaking van de bodemvochtigheid in het The Things Network. U kunt IoT Central toepassingen gebruiken, zoals regels en analyses voor de gegevens, werkstromen maken in Power Automate en Azure Logic-apps of de gegevens exporteren.

De oplossing voor apparaatbrug biedt verschillende Azure-resources in uw Azure-abonnement die samenwerken om apparaatberichten te transformeren en door te sturen naar IoT Central.

## <a name="prerequisites"></a>Vereisten

Als u de stappen in deze handleiding wilt uitvoeren, hebt u een actief Azure-abonnement nodig.

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

Voltooi de quickstart [Een Azure IoT Central-toepassing maken](./quick-deploy-iot-central.md) om een IoT Central-toepassing te maken met behulp van de toepassing **Aangepaste app > Aangepaste sjabloon**.

## <a name="overview"></a>Overzicht

De IoT Central device bridge is een opensource-oplossing in GitHub. Er wordt gebruikgemaakt van Azure Resource Manager aangepaste sjabloon om verschillende resources te implementeren in uw Azure-abonnement, waaronder een Azure-functie-app.

De functie-app is het belangrijkste onderdeel van de apparaatbrug. Het ontvangt HTTP POST-aanvragen van andere IoT-platforms via een eenvoudige webhook. De [Azure IoT Central Device Bridge bevat voorbeelden](https://github.com/Azure/iotc-device-bridge) die laten zien hoe u Sigfox-, Deeltje- en The Things-netwerkwolken verbindt. U kunt deze oplossing uitbreiden om verbinding te maken met uw aangepaste IoT-cloud als uw platform HTTP POST-aanvragen naar uw functie-app kan verzenden.

De functie-app transformeert de gegevens naar een indeling die wordt geaccepteerd door IoT Central en doorgestuurd met behulp van de device provisioning service en client-API's voor apparaten:

:::image type="content" source="media/howto-build-iotc-device-bridge/azure-function.png" alt-text="Schermopname van de schermopname van Azure Functions.":::

Als uw IoT Central de apparaat-id in het doorgestuurde bericht herkent, wordt de telemetrie van het apparaat weergegeven in IoT Central. Als de apparaat-id niet wordt herkend door uw IoT Central toepassing, probeert de functie-app een nieuw apparaat met de apparaat-id te registreren. Het nieuwe apparaat wordt weergegeven als een **niet-verbonden** apparaat op de pagina Apparaten in IoT Central toepassing.  Op de **pagina** Apparaten kunt u het nieuwe apparaat koppelen aan een apparaatsjabloon en vervolgens de telemetrie bekijken.

## <a name="deploy-the-device-bridge"></a>De apparaatbrug implementeren

De apparaatbrug implementeren in uw abonnement:

1. Ga in IoT Central toepassing naar de **pagina > Apparaatverbinding.**

    1. Noteer het **Id-bereik**. U gebruikt deze waarde wanneer u de apparaatbrug implementeert.

    1. Open op dezelfde pagina de **registratiegroep SAS-IoT-Devices.** Kopieer op **de groepspagina SAS-IoT-Devices** de **primaire sleutel**. U gebruikt deze waarde wanneer u de apparaatbrug implementeert.

1. Gebruik de **knop Implementeren in Azure** hieronder om de aangepaste Resource Manager te openen waarmee de functie-app in uw abonnement wordt geïmplementeerd. Gebruik het **id-bereik** en **de primaire sleutel uit** de vorige stap:

    [![Implementeren in Azure](http://azuredeploy.net/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fiotc-device-bridge%2Fmaster%2Fazuredeploy.json)

Nadat de implementatie is voltooid, moet u de NPM-pakketten installeren die de functie vereist:

1. Open in Azure Portal de functie-app die in uw abonnement is geïmplementeerd. Navigeer vervolgens **naar Ontwikkelhulpprogramma'> Console**. Voer in de -console de volgende opdrachten uit om de pakketten te installeren:

    ```bash
    cd IoTCIntegration
    npm install
    ```

    Het uitvoeren van deze opdrachten kan enkele minuten duren. U kunt waarschuwingsberichten veilig negeren.

1. Nadat de installatie van het pakket is voltooien, selecteert u **Opnieuw** opstarten op **de pagina Overzicht** van de functie-app:

    :::image type="content" source="media/howto-build-iotc-device-bridge/restart.png" alt-text="Schermopname van Opnieuw opstarten.":::

1. De functie is nu klaar voor gebruik. Externe systemen kunnen HTTP POST-aanvragen gebruiken om apparaatgegevens via de apparaatbrug naar uw IoT Central verzenden. Om de functie-URL op te halen, gaat u naar **Functions > IoTCIntegration > Code + Test > Functie-URL op te halen:**

    :::image type="content" source="media/howto-build-iotc-device-bridge/get-function-url.png" alt-text="Schermopname van Functie-URL op halen.":::

Berichten die naar de apparaatbrug worden verzonden, moeten de volgende indeling hebben:

```json
"device": {
  "deviceId": "my-cloud-device"
},
"measurements": {
  "temp": 20.31,
  "pressure": 50,
  "humidity": 8.5,
  "ledColor": "blue"
}
```

Elke sleutel in het object moet overeenkomen met de naam van een telemetrietype in de apparaatsjabloon `measurements` in IoT Central toepassing. Deze oplossing biedt geen ondersteuning voor het opgeven van de interface-id in de bericht body. Dus als twee verschillende interfaces een telemetrietype met dezelfde naam hebben, wordt de meting weergegeven in beide telemetriestromen in uw IoT Central toepassing.

U kunt een veld `timestamp` in de body opnemen om de UTC-datum en -tijd van het bericht op te geven. Dit veld moet de ISO 8601-indeling hebben. Bijvoorbeeld `2020-06-08T20:16:54.602Z`. Als u geen tijdstempel op bevat, worden de huidige datum en tijd gebruikt.

U kunt een veld `modelId` in de body opnemen. Gebruik dit veld om het apparaat tijdens het inrichten te koppelen aan een apparaatsjabloon. Deze functionaliteit wordt alleen ondersteund door [V3-toepassingen.](howto-get-app-info.md)

De `deviceId` moet alfanumeriek zijn, kleine letters bevatten en kan afbreekstreelopen bevatten.

Als u het veld niet op neemt of als IoT Central de model-id niet herkent, wordt met een bericht met een niet-herkende id een nieuw niet-verbonden apparaat `modelId` `deviceId` in IoT Central.  Een operator kan het apparaat handmatig migreren naar de juiste apparaatsjabloon. Zie Apparaten beheren in uw Azure IoT Central toepassing voor [> migreren van apparaten naar een sjabloon voor meer informatie.](howto-manage-devices.md)

In [V2-toepassingen](howto-get-app-info.md)wordt het nieuwe apparaat weergegeven op **Device Explorer > pagina Niet-verbonden** apparaten. Selecteer **Koppelen** en kies een apparaatsjabloon om binnenkomende telemetrie van het apparaat te ontvangen.

> [!NOTE]
> Totdat het apparaat is gekoppeld aan een sjabloon, retourneren alle HTTP-aanroepen naar de functie de foutstatus 403.

Als u logboekregistratie voor de functie-app wilt inschakelen met Application Insights, gaat u naar **Bewaking > logboeken** in uw functie-app in de Azure Portal. Selecteer **In Application Insights.**

## <a name="provisioned-resources"></a>Inrichtende resources

De Resource Manager voor de volgende resources in uw Azure-abonnement:

* Functie-app
* App Service-plan
* Storage-account
* Key Vault

De sleutelkluis slaat de SAS-groepssleutel voor uw IoT Central op.

De functie-app wordt uitgevoerd met een [verbruiksplan](https://azure.microsoft.com/pricing/details/functions/). Hoewel deze optie geen toegewezen rekenresources biedt, kan de apparaatbrug honderden apparaatberichten per minuut verwerken, wat geschikt is voor kleinere groepen apparaten of apparaten die minder vaak berichten verzenden. Als uw toepassing afhankelijk is van het streamen van een groot aantal apparaatberichten, vervangt u het verbruiksplan door een speciaal [App Service-plan](https://azure.microsoft.com/pricing/details/app-service/windows/). Dit plan biedt toegewezen rekenbronnen, waardoor de reactietijden van de server sneller zijn. Met een standaard App Service plan waren de maximaal waargenomen prestaties van de Azure-functie in deze opslagplaats ongeveer 1500 apparaatberichten per minuut. Zie Hostingopties voor [Azure-functies voor meer informatie.](../../azure-functions/functions-scale.md)

Als u een toegewezen abonnement App Service in plaats van een verbruiksplan, bewerkt u de aangepaste sjabloon voordat u implementeert. Selecteer **Sjabloon bewerken.**

:::image type="content" source="media/howto-build-iotc-device-bridge/edit-template.png" alt-text="Schermopname van Sjabloon bewerken.":::

Vervang het volgende segment:

```json
{
  "type": "Microsoft.Web/serverfarms",
  "apiVersion": "2015-04-01",
  "name": "[variables('planName')]",
  "location": "[resourceGroup().location]",
  "properties": {
    "name": "[variables('planName')]",
    "computeMode": "Dynamic",
    "sku": "Dynamic"
  }
},
```

wordt uitgevoerd met

```json
{
  "type": "Microsoft.Web/serverfarms",
  "sku": {
      "name": "S1",
      "tier": "Standard",
      "size": "S1",
      "family": "S",
      "capacity": 1
  },
  "kind": "app",
  "name": "[variables('planName')]",
  "apiVersion": "2016-09-01",
  "location": "[resourceGroup().location]",
  "tags": {
      "iotCentral": "device-bridge",
      "iotCentralDeviceBridge": "app-service-plan"
  },
  "properties": {
      "name": "[variables('planName')]"
  }
},
```

Bewerk vervolgens de sjabloon om deze op te nemen in de configuratie voor de `"alwaysOn": true` `functionapp` resource onder De `"properties": {"SiteConfig": {...}}` [alwaysOn-configuratie](https://github.com/Azure/Azure-Functions/wiki/Enable-Always-On-when-running-on-dedicated-App-Service-Plan) zorgt ervoor dat de functie-app altijd wordt uitgevoerd.

## <a name="examples"></a>Voorbeelden

In de volgende voorbeelden wordt beschreven hoe u de apparaatbrug configureert voor verschillende IoT-clouds:

### <a name="example-1-connecting-particle-devices-through-the-device-bridge"></a>Voorbeeld 1: Apparaten met deeltjes verbinden via de apparaatbrug

Als u een deeltjeapparaat via de apparaatbrug wilt verbinden met IoT Central, gaat u naar de Console voor deeltjes en maakt u een nieuwe webhookintegratie. Stel de **Aanvraagindeling in** op **JSON**.  Gebruik **onder Geavanceerde instellingen** de volgende aangepaste indeling voor de body:

```json
{
  "device": {
    "deviceId": "{{{PARTICLE_DEVICE_ID}}}"
  },
  "measurements": {
    "{{{PARTICLE_EVENT_NAME}}}": {{{PARTICLE_EVENT_VALUE}}}
  }
}
```

Plak de **functie-URL van** uw Azure-functie-app en u ziet dat Deeltjesapparaten worden weergegeven als niet-verbonden apparaten in IoT Central. Zie de blogpost [Hier kunt](https://blog.particle.io/2019/09/26/integrate-particle-with-azure-iot-central/) u uw door deeltjes aangedreven projecten integreren met Azure IoT Central meer informatie.

### <a name="example-2-connecting-sigfox-devices-through-the-device-bridge"></a>Voorbeeld 2: Sigfox-apparaten verbinden via de apparaatbrug

Op sommige platforms kunt u mogelijk niet de indeling opgeven van apparaatberichten die via een webhook worden verzonden. Voor dergelijke systemen moet u de nettolading van het bericht converteren naar de verwachte indeling van de body voordat de apparaatbrug deze verwerkt. U kunt de conversie uitvoeren in dezelfde Azure-functie die de apparaatbrug voert.

In deze sectie ziet u hoe u de nettolading van een Sigfox-webhook-integratie converteert naar de indeling van de body die wordt verwacht door de apparaatbrug. De Sigfox-cloud verzendt apparaatgegevens in een hexadecimale tekenreeksindeling. Voor het gemak bevat de apparaatbrug een conversiefunctie voor deze indeling, die een subset accepteert van de mogelijke veldtypen in de nettolading van een Sigfox-apparaat: en van `int` `uint` 8, 16, 32 of 64 bits, `float` van 32 bits of 64 bits; little-endian en big-endian. Als u berichten van een Sigfox-webhookintegratie wilt verwerken, moet u de volgende wijzigingen aanbrengen in het _bestand IoTCIntegration/index.js_ in de functie-app.

Als u de nettolading van het bericht wilt converteren, voegt u de volgende code toe vóór de aanroep van op regel 21, en vervangt u door de definitie van uw `handleMessage` `payloadDefinition` Sigfox-nettolading:

```javascript
const payloadDefinition = 'gforce::uint:8 lat::uint:8 lon::uint:16'; // Replace this with your payload definition

req.body = {
    device: {
        deviceId: req.body.device
    },
    measurements: require('./converters/sigfox')(payloadDefinition, req.body.data)
};
```

Sigfox-apparaten verwachten een `204` antwoordcode. Voeg de volgende code toe na de aanroep `handleMessage` van in regel 21:

```javascript
context.res = {
    status: 204
};
```

### <a name="example-3-connecting-devices-from-the-things-network-through-the-device-bridge"></a>Voorbeeld 3: Apparaten verbinden vanuit het Things-netwerk via de apparaatbrug

The Things Network-apparaten verbinden met IoT Central:

* Voeg een nieuwe HTTP-integratie toe aan uw toepassing in The Things Network: **Application > Integrations > integration > HTTP Integration**.
* Zorg ervoor dat uw toepassing een decoderfunctie bevat die de nettolading van uw apparaatberichten automatisch converteert naar JSON voordat deze wordt verzonden naar de Azure-functie: **Application > Payload Functions > decoder**.

In het volgende voorbeeld ziet u een JavaScript-decoderfunctie die u kunt gebruiken voor het decoderen van algemene numerieke typen uit binaire gegevens:

```javascript
function Decoder(bytes, port) {
  function bytesToFloat(bytes, decimalPlaces) {
    var bits = (bytes[3] << 24) | (bytes[2] << 16) | (bytes[1] << 8) | bytes[0];
    var sign = (bits >>> 31 === 0) ? 1.0 : -1.0;
    var e = bits >>> 23 & 0xff;
    var m = (e === 0) ? (bits & 0x7fffff) << 1 : (bits & 0x7fffff) | 0x800000;
    var f = Math.round((sign * m * Math.pow(2, e - 150)) * Math.pow(10, decimalPlaces)) / Math.pow(10, decimalPlaces);
    return f;
  }

  function bytesToInt32(bytes, signed) {
    var bits = bytes[0] | (bytes[1] << 8) | (bytes[2] << 16) | (bytes[3] << 24);
    var sign = 1;

    if (signed && bits >>> 31 === 1) {
      sign = -1;
      bits = bits & 0x7FFFFFFF;
    }

    return bits * sign;
  }

  function bytesToShort(bytes, signed) {
    var bits = bytes[0] | (bytes[1] << 8);
    var sign = 1;

    if (signed && bits >>> 15 === 1) {
      sign = -1;
      bits = bits & 0x7FFF;
    }

    return bits * sign;
  }

  return {
    temperature: bytesToFloat(bytes.slice(0, 4), 2),
    presscounter: bytesToInt32(bytes.slice(4, 8), true),
    blueLux: bytesToShort(bytes.slice(8, 10), false)
  };
}
```

Nadat u de integratie hebt definieert, voegt u de volgende code toe vóór de aanroep van in regel 21 van het `handleMessage` *bestand IoTCIntegration/index.js* van uw Azure-functie-app. Met deze code wordt de hoofdcode van uw HTTP-integratie omgezet in de verwachte indeling.

```javascript
device: {
    deviceId: req.body.hardware_serial.toLowerCase()
},
measurements: req.body.payload_fields
};
```

## <a name="limitations"></a>Beperkingen

De apparaatbrug stuurt alleen berichten door naar IoT Central en verzendt geen berichten terug naar apparaten. Daarom werken eigenschappen en opdrachten niet voor apparaten die verbinding maken met IoT Central via deze apparaatbrug. Omdat apparaattweebewerkingen niet worden ondersteund, is het niet mogelijk om apparaateigenschappen bij te werken via de apparaatbrug. Als u deze functies wilt gebruiken, moet een apparaat rechtstreeks verbinding maken met IoT Central met behulp van een van de [Azure IoT-apparaat-SDK's.](../../iot-hub/iot-hub-devguide-sdks.md)

## <a name="next-steps"></a>Volgende stappen
Nu u hebt geleerd hoe u de apparaatbrug IoT Central implementeren, is dit de voorgestelde volgende stap:

> [!div class="nextstepaction"]
> [Uw apparaten beheren](howto-manage-devices.md)
