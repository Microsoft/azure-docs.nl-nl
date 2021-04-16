---
title: Inzicht Azure IoT Hub beveiligingsbeleid | Microsoft Docs
description: 'Ontwikkelaarshandleiding: hoe u de toegang tot IoT Hub voor apparaat-apps en back-end-apps kunt beheren. Bevat informatie over beveiligingstokens en ondersteuning voor X.509-certificaten.'
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/15/2021
ms.custom:
- amqp
- mqtt
- 'Role: Cloud Development'
- 'Role: IoT Device'
- 'Role: Operations'
- devx-track-js
- devx-track-csharp
ms.openlocfilehash: e72af412f61f2084fb78907c15a92a22b9e3bc99
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107567176"
---
# <a name="control-access-to-iot-hub"></a>Toegang tot IoT Hub regelen

In dit artikel worden de opties beschreven voor het beveiligen van uw IoT-hub. IoT Hub gebruikt *machtigingen om* toegang te verlenen tot elk IoT-hub-eindpunt. Machtigingen beperken de toegang tot een IoT-hub op basis van functionaliteit.

In dit artikel wordt het volgende beschreven:

* De verschillende machtigingen die u aan een apparaat of back-end-app kunt verlenen voor toegang tot uw IoT-hub.
* Het verificatieproces en de tokens die worden gebruikt om machtigingen te verifiëren.
* Het bereik van referenties om de toegang tot specifieke resources te beperken.
* IoT Hub ondersteuning voor X.509-certificaten.
* Aangepaste apparaatverificatiemechanismen die gebruikmaken van bestaande apparaat-id-registers of verificatieschema's.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

U moet over de juiste machtigingen voor toegang tot een van de IoT Hub eindpunten. Een apparaat moet bijvoorbeeld een token bevatten met beveiligingsreferenties samen met elk bericht dat naar het apparaat wordt IoT Hub.

## <a name="access-control-and-permissions"></a>Toegangsbeheer en machtigingen

U kunt [machtigingen op](#iot-hub-permissions) de volgende manieren verlenen:

* **Beleid voor gedeelde toegang op IoT-hubniveau.** Beleid voor gedeelde toegang kan elke combinatie van [machtigingen verlenen.](#iot-hub-permissions) U kunt beleidsregels definiëren in [de Azure Portal,](https://portal.azure.com)programmatisch met behulp van [de IoT Hub Resource REST API's](/rest/api/iothub/iothubresource)of met behulp van de CLI [az iot hub policy.](/cli/azure/iot/hub/policy) Een zojuist gemaakte IoT-hub heeft de volgende standaardbeleidsregels:
  
  | Beleid voor gedeelde toegang | Machtigingen |
  | -------------------- | ----------- |
  | iothubowner | Alle machtigingen |
  | service | **ServiceConnect-machtigingen** |
  | apparaat | **DeviceConnect-machtigingen** |
  | registryRead | **RegistryRead-machtigingen** |
  | registryReadWrite | **RegistryRead-** **en RegistryWrite-machtigingen** |

* **Beveiligingsreferenties per apparaat.** Elke IoT Hub bevat een [](iot-hub-devguide-identity-registry.md) identiteitsregister Voor elk apparaat in dit identiteitsregister kunt u beveiligingsreferenties configureren die **DeviceConnect-machtigingen** verlenen voor de bijbehorende apparaat-eindpunten.

Bijvoorbeeld in een typische IoT-oplossing:

* Het onderdeel voor apparaatbeheer maakt gebruik *van het registryReadWrite-beleid.*
* Het onderdeel gebeurtenisprocessor maakt gebruik van *het servicebeleid.*
* Het bedrijfslogicaonderdeel voor run time-apparaten maakt gebruik van *het servicebeleid.*
* Afzonderlijke apparaten maken verbinding met behulp van referenties die zijn opgeslagen in het id-register van de IoT-hub.

> [!NOTE]
> Zie [Machtigingen](#iot-hub-permissions) voor gedetailleerde informatie.

## <a name="authentication"></a>Verificatie

In Azure IoT Hub wordt toegang verleend aan eindpunten door een token te verifiëren op basis van de gedeelde toegangsbeleidsregels en beveiligingsreferenties van het identiteitsregister.

Beveiligingsreferenties, zoals symmetrische sleutels, worden nooit via de kabel verzonden.

> [!NOTE]
> De Azure IoT Hub resourceprovider wordt beveiligd via uw Azure-abonnement, net als alle providers in [de Azure Resource Manager](../azure-resource-manager/management/overview.md).

Zie beveiligingstokens voor meer informatie over het maken en gebruiken [IoT Hub beveiligingstokens.](iot-hub-devguide-security.md#security-tokens)

### <a name="protocol-specifics"></a>Protocolspecifieke informatie

Elk ondersteund protocol, zoals MQTT, AMQP en HTTPS, transporteert tokens op verschillende manieren.

Wanneer u MQTT gebruikt, heeft het CONNECT-pakket de deviceId als de ClientId, in het veld Gebruikersnaam en een `{iothubhostname}/{deviceId}` SAS-token in het veld Wachtwoord. `{iothubhostname}` moet de volledige CName van de IoT-hub zijn (bijvoorbeeld contoso.azure-devices.net).

Wanneer u [AMQP gebruikt,](https://www.amqp.org/)IoT Hub ondersteuning [SASL PLAIN](https://tools.ietf.org/html/rfc4616) en [AMQP Claims-Based-Security](https://www.oasis-open.org/committees/download.php/50506/amqp-cbs-v1%200-wd02%202013-08-12.doc).

Als u beveiliging op basis van AMQP-claims gebruikt, geeft de standaard aan hoe deze tokens moeten worden verzenden.

Voor SASL PLAIN kan de **gebruikersnaam zijn:**

* `{policyName}@sas.root.{iothubName}` als u tokens op IoT-hubniveau gebruikt.
* `{deviceId}@sas.{iothubname}` als u tokens binnen apparaatbereik gebruikt.

In beide gevallen bevat het wachtwoordveld het token, zoals beschreven in [IoT Hub beveiligingstokens](iot-hub-devguide-security.md#security-tokens).

HTTPS implementeert verificatie door een geldig token op te geven in de **aanvraagheader voor** autorisatie.

#### <a name="example"></a>Voorbeeld

Gebruikersnaam (DeviceId is casegevoelig): `iothubname.azure-devices.net/DeviceId`

Wachtwoord (u kunt een SAS-token genereren met de CLI-extensieopdracht [az iot hub generate-sas-token](/cli/azure/ext/azure-iot/iot/hub#ext-azure-iot-az-iot-hub-generate-sas-token)of de [Azure IoT Tools voor Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)):

`SharedAccessSignature sr=iothubname.azure-devices.net%2fdevices%2fDeviceId&sig=kPszxZZZZZZZZZZZZZZZZZAhLT%2bV7o%3d&se=1487709501`

> [!NOTE]
> De [Azure IoT-SDK's](iot-hub-devguide-sdks.md) genereren automatisch tokens wanneer ze verbinding maken met de service. In sommige gevallen bieden de Azure IoT SDK's geen ondersteuning voor alle protocollen of alle verificatiemethoden.

### <a name="special-considerations-for-sasl-plain"></a>Speciale overwegingen voor SASL PLAIN

Wanneer u SASL PLAIN met AMQP gebruikt, kan een client die verbinding maakt met een IoT-hub, één token gebruiken voor elke TCP-verbinding. Wanneer het token verloopt, wordt de verbinding met de TCP-verbinding met de service verbroken en wordt er een nieuwe verbinding tot stand gebracht. Dit gedrag is, hoewel dit niet problematisch is voor een back-end-app, nadelig voor een apparaat-app om de volgende redenen:

* Gateways maken meestal verbinding namens veel apparaten. Wanneer u SASL PLAIN, moeten ze een afzonderlijke TCP-verbinding maken voor elk apparaat dat verbinding maakt met een IoT-hub. Dit scenario verhoogt het verbruik van energie- en netwerkbronnen aanzienlijk en verhoogt de latentie van elke apparaatverbinding.

* Resource-beperkte apparaten worden negatief beïnvloed door het toegenomen gebruik van resources om opnieuw verbinding te maken nadat elke token is verlopen.

## <a name="scope-iot-hub-level-credentials"></a>Referenties op IoT-hubniveau bereik

U kunt het bereik van beveiligingsbeleid op IoT-hubniveau beperken door tokens te maken met een beperkte resource-URI. Het eindpunt voor het verzenden van apparaat-naar-cloud-berichten vanaf een apparaat is **bijvoorbeeld /devices/{deviceId}/messages/events.** U kunt ook een gedeeld toegangsbeleid op IoT-hubniveau gebruiken met **DeviceConnect-machtigingen** om een token te ondertekenen waarvan de resourceURI **/devices/{deviceId} is.** Met deze aanpak wordt een token gemaakt dat alleen kan worden gebruikt om berichten te verzenden namens apparaat **deviceId.**

Dit mechanisme is vergelijkbaar met het [Event Hubs uitgeversbeleid](https://code.msdn.microsoft.com/Service-Bus-Event-Hub-99ce67ab)en stelt u in staat om aangepaste verificatiemethoden te implementeren.

## <a name="security-tokens"></a>Beveiligingstokens

IoT Hub gebruikt beveiligingstokens om apparaten en services te verifiëren om te voorkomen dat sleutels via de kabel worden verzonden. Beveiligingstokens hebben bovendien een beperkte geldigheidsduur en een beperkt bereik. [Azure IoT SDK's](iot-hub-devguide-sdks.md) genereren automatisch tokens zonder speciale configuratie. Voor sommige scenario's moet u beveiligingstokens rechtstreeks genereren en gebruiken. Dergelijke scenario's zijn onder andere:

* Het directe gebruik van de MQTT-, AMQP- of HTTPS-surfaces.

* De implementatie van het tokenservicepatroon, zoals uitgelegd in [Aangepaste apparaatverificatie.](iot-hub-devguide-security.md#custom-device-and-module-authentication)

IoT Hub kunnen apparaten ook worden geverifieerd met IoT Hub [X.509-certificaten.](iot-hub-devguide-security.md#supported-x509-certificates)

### <a name="security-token-structure"></a>Beveiliging tokenstructuur

U gebruikt beveiligingstokens om tijdgebonden toegang tot apparaten en services te verlenen aan specifieke functionaliteit in IoT Hub. Als u autorisatie wilt verkrijgen om verbinding IoT Hub maken, moeten apparaten en services beveiligingstokens verzenden die zijn ondertekend met een gedeelde of symmetrische sleutel. Deze sleutels worden opgeslagen met een apparaat-id in het identiteitsregister.

Een token dat is ondertekend met een gedeelde toegangssleutel verleent toegang tot alle functionaliteit die is gekoppeld aan de machtigingen voor het beleid voor gedeelde toegang. Een token dat is ondertekend met de symmetrische sleutel van een apparaat-id verleent alleen de **deviceConnect-machtiging** voor de bijbehorende apparaat-id.

Het beveiliging token heeft de volgende indeling:

`SharedAccessSignature sig={signature-string}&se={expiry}&skn={policyName}&sr={URL-encoded-resourceURI}`

Dit zijn de verwachte waarden:

| Waarde | Beschrijving |
| --- | --- |
| {signature} |Een handtekeningreeks van het formulier HMAC-SHA256: `{URL-encoded-resourceURI} + "\n" + expiry` . **Belangrijk:** de sleutel wordt gedecodeerd uit base64 en gebruikt als sleutel om de HMAC-SHA256-berekening uit te voeren. |
| {resourceURI} |URI-voorvoegsel (per segment) van de eindpunten die toegankelijk zijn met dit token, beginnend met de hostnaam van de IoT-hub (geen protocol). Bijvoorbeeld: `myHub.azure-devices.net/devices/device1` |
| {expiry} |UTF8-tekenreeksen voor het aantal seconden sinds het tijdvak 00:00:00 UTC op 1 januari 1970. |
| {URL-encoded-resourceURI} |URL-codering in kleine gevallen van de URI van de kleine resource |
| {policyName} |De naam van het beleid voor gedeelde toegang waar dit token naar verwijst. Ontbreekt als het token verwijst naar de referenties van het apparaatregister. |

**Opmerking bij voorvoegsel:** het URI-voorvoegsel wordt berekend per segment en niet per teken. Is bijvoorbeeld `/a/b` een voorvoegsel voor `/a/b/c` , maar niet voor `/a/bc` .

Het volgende Node.js een functie met de naam **generateSasToken** die het token van de invoer `resourceUri, signingKey, policyName, expiresInMins` berekent. In de volgende secties wordt gedetailleerd beschreven hoe u de verschillende invoer voor de verschillende tokengebruiksgevallen initialiseert.

```javascript
var generateSasToken = function(resourceUri, signingKey, policyName, expiresInMins) {
    resourceUri = encodeURIComponent(resourceUri);

    // Set expiration in seconds
    var expires = (Date.now() / 1000) + expiresInMins * 60;
    expires = Math.ceil(expires);
    var toSign = resourceUri + '\n' + expires;

    // Use crypto
    var hmac = crypto.createHmac('sha256', Buffer.from(signingKey, 'base64'));
    hmac.update(toSign);
    var base64UriEncoded = encodeURIComponent(hmac.digest('base64'));

    // Construct authorization string
    var token = "SharedAccessSignature sr=" + resourceUri + "&sig="
    + base64UriEncoded + "&se=" + expires;
    if (policyName) token += "&skn="+policyName;
    return token;
};
```

Ter vergelijking: de equivalente Python-code voor het genereren van een beveiliging token is:

```python
from base64 import b64encode, b64decode
from hashlib import sha256
from time import time
from urllib import parse
from hmac import HMAC

def generate_sas_token(uri, key, policy_name, expiry=3600):
    ttl = time() + expiry
    sign_key = "%s\n%d" % ((parse.quote_plus(uri)), int(ttl))
    print(sign_key)
    signature = b64encode(HMAC(b64decode(key), sign_key.encode('utf-8'), sha256).digest())

    rawtoken = {
        'sr' :  uri,
        'sig': signature,
        'se' : str(int(ttl))
    }

    if policy_name is not None:
        rawtoken['skn'] = policy_name

    return 'SharedAccessSignature ' + parse.urlencode(rawtoken)
```

De functionaliteit in C# voor het genereren van een beveiliging token is:

```csharp
using System;
using System.Globalization;
using System.Net;
using System.Net.Http;
using System.Security.Cryptography;
using System.Text;

public static string generateSasToken(string resourceUri, string key, string policyName, int expiryInSeconds = 3600)
{
    TimeSpan fromEpochStart = DateTime.UtcNow - new DateTime(1970, 1, 1);
    string expiry = Convert.ToString((int)fromEpochStart.TotalSeconds + expiryInSeconds);

    string stringToSign = WebUtility.UrlEncode(resourceUri) + "\n" + expiry;

    HMACSHA256 hmac = new HMACSHA256(Convert.FromBase64String(key));
    string signature = Convert.ToBase64String(hmac.ComputeHash(Encoding.UTF8.GetBytes(stringToSign)));

    string token = String.Format(CultureInfo.InvariantCulture, "SharedAccessSignature sr={0}&sig={1}&se={2}", WebUtility.UrlEncode(resourceUri), WebUtility.UrlEncode(signature), expiry);

    if (!String.IsNullOrEmpty(policyName))
    {
        token += "&skn=" + policyName;
    }

    return token;
}
```

Voor Java:
```java
    public static String generateSasToken(String resourceUri, String key) throws Exception {
        // Token will expire in one hour
        var expiry = Instant.now().getEpochSecond() + 3600;

        String stringToSign = URLEncoder.encode(resourceUri, StandardCharsets.UTF_8) + "\n" + expiry;
        byte[] decodedKey = Base64.getDecoder().decode(key);

        Mac sha256HMAC = Mac.getInstance("HmacSHA256");
        SecretKeySpec secretKey = new SecretKeySpec(decodedKey, "HmacSHA256");
        sha256HMAC.init(secretKey);
        Base64.Encoder encoder = Base64.getEncoder();

        String signature = new String(encoder.encode(
            sha256HMAC.doFinal(stringToSign.getBytes(StandardCharsets.UTF_8))), StandardCharsets.UTF_8);

        String token = "SharedAccessSignature sr=" + URLEncoder.encode(resourceUri, StandardCharsets.UTF_8)
                + "&sig=" + URLEncoder.encode(signature, StandardCharsets.UTF_8.name()) + "&se=" + expiry;
            
        return token;
    }
```


> [!NOTE]
> Omdat de geldigheidsduur van het token wordt gevalideerd op IoT Hub machines, moet de afwijking op de klok van de machine die het token genereert, minimaal zijn.

### <a name="use-sas-tokens-in-a-device-app"></a>SAS-tokens gebruiken in een apparaat-app

Er zijn twee manieren om **DeviceConnect-machtigingen** te verkrijgen met IoT Hub met beveiligingstokens: gebruik een symmetrische apparaatsleutel uit het identiteitsregister [of](#use-a-symmetric-key-in-the-identity-registry)gebruik een [gedeelde toegangssleutel.](#use-a-shared-access-policy)

Houd er wel voor dat alle functionaliteit die toegankelijk is vanaf apparaten, ontwerpbaar is op eindpunten met voorvoegsel `/devices/{deviceId}` .

> [!IMPORTANT]
> De enige manier waarop IoT Hub een specifiek apparaat verifieert, is door de symmetrische sleutel van de apparaat-id te gebruiken. In gevallen waarin een beleid voor gedeelde toegang wordt gebruikt voor toegang tot de functionaliteit van apparaten, moet de oplossing het onderdeel dat het beveiliging token uit geeft, beschouwen als een vertrouwd subonderdeel.

De apparaat-gerichte eindpunten zijn (ongeacht het protocol):

| Eindpunt | Functionaliteit |
| --- | --- |
| `{iot hub host name}/devices/{deviceId}/messages/events` |Apparaat-naar-cloud-berichten verzenden. |
| `{iot hub host name}/devices/{deviceId}/messages/devicebound` |Cloud-naar-apparaat-berichten ontvangen. |

### <a name="use-a-symmetric-key-in-the-identity-registry"></a>Een symmetrische sleutel gebruiken in het identiteitsregister

Wanneer u de symmetrische sleutel van een apparaat-id gebruikt om een token te genereren, wordt het element policyName ( ) van het `skn` token weggelaten.

Een token dat is gemaakt voor toegang tot alle apparaatfunctionaliteit moet bijvoorbeeld de volgende parameters hebben:

* resource-URI: `{IoT hub name}.azure-devices.net/devices/{device id}` ,
* ondertekeningssleutel: een symmetrische sleutel voor `{device id}` de identiteit,
* geen beleidsnaam,
* een verlooptijd.

Een voorbeeld van het gebruik van de Node.js functie is:

```javascript
var endpoint ="myhub.azure-devices.net/devices/device1";
var deviceKey ="...";

var token = generateSasToken(endpoint, deviceKey, null, 60);
```

Het resultaat, dat toegang verleent tot alle functionaliteit voor device1, is:

`SharedAccessSignature sr=myhub.azure-devices.net%2fdevices%2fdevice1&sig=13y8ejUk2z7PLmvtwR5RqlGBOVwiq7rQR3WZ5xZX3N4%3D&se=1456971697`

> [!NOTE]
> Het is mogelijk om een SAS-token te genereren met de CLI-extensieopdracht [az iot hub generate-sas-token](/cli/azure/ext/azure-iot/iot/hub#ext-azure-iot-az-iot-hub-generate-sas-token)of de [Azure IoT Tools voor Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools).

### <a name="use-a-shared-access-policy"></a>Beleid voor gedeelde toegang gebruiken

Wanneer u een token van een beleid voor gedeelde toegang maakt, stelt u het veld in `skn` op de naam van het beleid. Dit beleid moet de machtiging **DeviceConnect** verlenen.

De twee belangrijkste scenario's voor het gebruik van beleid voor gedeelde toegang voor toegang tot apparaatfunctionaliteit zijn:

* [cloudprotocolgateways](iot-hub-devguide-endpoints.md),
* [tokenservices die](iot-hub-devguide-security.md#custom-device-and-module-authentication) worden gebruikt voor het implementeren van aangepaste verificatieschema's.

Omdat het beleid voor gedeelde toegang mogelijk toegang kan verlenen om verbinding te maken als elk apparaat, is het belangrijk om de juiste resource-URI te gebruiken bij het maken van beveiligingstokens. Deze instelling is vooral belangrijk voor tokenservices, die het token moeten instellen op een specifiek apparaat met behulp van de resource-URI. Dit punt is minder relevant voor protocolgateways omdat ze al verkeer voor alle apparaten mediagen.

Een voorbeeld: een tokenservice die gebruik maakt van het vooraf gemaakte beleid voor gedeelde toegang met de naam **apparaat,** maakt een token met de volgende parameters:

* resource-URI: `{IoT hub name}.azure-devices.net/devices/{device id}` ,
* ondertekeningssleutel: een van de sleutels van het `device` beleid,
* beleidsnaam: `device` ,
* een verlooptijd.

Een voorbeeld van het gebruik van de Node.js functie is:

```javascript
var endpoint ="myhub.azure-devices.net/devices/device1";
var policyName = 'device';
var policyKey = '...';

var token = generateSasToken(endpoint, policyKey, policyName, 60);
```

Het resultaat, dat toegang verleent tot alle functionaliteit voor device1, is:

`SharedAccessSignature sr=myhub.azure-devices.net%2fdevices%2fdevice1&sig=13y8ejUk2z7PLmvtwR5RqlGBOVwiq7rQR3WZ5xZX3N4%3D&se=1456971697&skn=device`

Een protocolgateway kan hetzelfde token gebruiken voor alle apparaten door de resource-URI in te stellen op `myhub.azure-devices.net/devices` .

### <a name="use-security-tokens-from-service-components"></a>Beveiligingstokens van serviceonderdelen gebruiken

Serviceonderdelen kunnen alleen beveiligingstokens genereren met behulp van beleid voor gedeelde toegang waarbij de juiste machtigingen worden verleend, zoals eerder is uitgelegd.

Dit zijn de servicefuncties die op de eindpunten worden getoond:

| Eindpunt | Functionaliteit |
| --- | --- |
| `{iot hub host name}/devices` |Apparaat-id's maken, bijwerken, ophalen en verwijderen. |
| `{iot hub host name}/messages/events` |Apparaat-naar-cloud-berichten ontvangen. |
| `{iot hub host name}/servicebound/feedback` |Feedback ontvangen voor cloud-naar-apparaat-berichten. |
| `{iot hub host name}/devicebound` |Cloud-naar-apparaat-berichten verzenden. |

Een voorbeeld: een service die genereert met behulp van het vooraf gemaakte beleid voor gedeelde toegang met de naam **registryRead,** maakt een token met de volgende parameters:

* resource-URI: `{IoT hub name}.azure-devices.net/devices` ,
* ondertekeningssleutel: een van de sleutels van het `registryRead` beleid,
* beleidsnaam: `registryRead` ,
* een verlooptijd.

```javascript
var endpoint ="myhub.azure-devices.net/devices";
var policyName = 'registryRead';
var policyKey = '...';

var token = generateSasToken(endpoint, policyKey, policyName, 60);
```

Het resultaat, dat toegang verleent om alle apparaat-id's te lezen, is:

`SharedAccessSignature sr=myhub.azure-devices.net%2fdevices&sig=JdyscqTpXdEJs49elIUCcohw2DlFDR3zfH5KqGJo4r4%3D&se=1456973447&skn=registryRead`

## <a name="supported-x509-certificates"></a>Ondersteunde X.509-certificaten

U kunt elk X.509-certificaat gebruiken om een apparaat te verifiëren met IoT Hub door een vingerafdruk van het certificaat of een certificeringsinstantie (CA) te uploaden naar Azure IoT Hub. Verificatie met behulp van certificaatvingerafdrukken controleert of de weergegeven vingerafdruk overeenkomt met de geconfigureerde vingerafdruk. Verificatie met behulp van de certificeringsinstantie valideert de certificaatketen. Hoe dan ook, TLS-handshake vereist dat het apparaat een geldig certificaat en een geldige persoonlijke sleutel heeft. Raadpleeg de TLS-specificatie voor meer informatie, bijvoorbeeld: [RFC 5246 - The Transport Layer Security (TLS) Protocol Version 1.2](https://tools.ietf.org/html/rfc5246/).

Ondersteunde certificaten zijn onder andere:

* **Een bestaand X.509-certificaat.** Aan een apparaat is mogelijk al een X.509-certificaat gekoppeld. Het apparaat kan dit certificaat gebruiken om te verifiëren met IoT Hub. Werkt met vingerafdruk- of CA-verificatie. 

* **Door CA ondertekend X.509-certificaat**. Als u een apparaat wilt identificeren en verifiëren met IoT Hub, kunt u een X.509-certificaat gebruiken dat is gegenereerd en ondertekend door een certificeringsinstantie (CA). Werkt met vingerafdruk- of CA-verificatie.

* **Een zelf gegenereerd en zelf-ondertekend X-509-certificaat.** Een apparaatfabrikant of in-house deployer kan deze certificaten genereren en de bijbehorende persoonlijke sleutel (en het bijbehorende certificaat) opslaan op het apparaat. U kunt hulpprogramma's zoals [OpenSSL](https://www.openssl.org/) en [Windows SelfSignedCertificate](/powershell/module/pkiclient/new-selfsignedcertificate) gebruiken voor dit doel. Werkt alleen met vingerafdrukverificatie.

Een apparaat kan een X.509-certificaat of een beveiliging token gebruiken voor verificatie, maar niet beide. Zorg er met X.509-certificaatverificatie voor dat u een strategie hebt om de rollover van certificaten af te handelen wanneer een bestaand certificaat verloopt.

De volgende functionaliteit voor apparaten die X.509-verificatie van de certificeringsinstantie (CA) gebruiken, is nog niet algemeen beschikbaar en de [preview-modus moet zijn ingeschakeld:](iot-hub-preview-mode.md)

* HTTPS, MQTT via WebSockets en AMQP via WebSockets-protocollen.
* Bestandsuploads (alle protocollen).

Zie Apparaatverificatie met [X.509 CA-certificaten](iot-hub-x509ca-overview.md)voor meer informatie over verificatie met behulp van de certificeringsinstantie. Zie Set [up X.509 security in your Azure IoT hub (X.509-beveiliging instellen in uw Azure IoT-hub)](iot-hub-security-x509-get-started.md)voor meer informatie over het uploaden en verifiëren van een certificeringsinstantie met uw IoT-hub.

### <a name="register-an-x509-certificate-for-a-device"></a>Een X.509-certificaat voor een apparaat registreren

De [Azure IoT Service SDK voor C#](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/iothub/service) (versie 1.0.8+) ondersteunt het registreren van een apparaat dat gebruikmaakt van een X.509-certificaat voor verificatie. Andere API's, zoals het importeren/exporteren van apparaten, ondersteunen ook X.509-certificaten.

U kunt ook de CLI-extensieopdracht [az iot hub device-identity](/cli/azure/ext/azure-iot/iot/hub/device-identity) gebruiken om X.509-certificaten voor apparaten te configureren.

### <a name="c-support"></a>\#C-ondersteuning

De **klasse RegistryManager** biedt een programmatische manier om een apparaat te registreren. Met name met de **methoden AddDeviceAsync** en **UpdateDeviceAsync** kunt u een apparaat registreren en bijwerken in het IoT Hub identiteitsregister. Deze twee methoden gebruiken een **apparaatinvoer** als invoer. De **klasse Device** bevat een **verificatie-eigenschap** waarmee u primaire en secundaire X.509-certificaatvingerafdrukken kunt opgeven. De vingerafdruk vertegenwoordigt een SHA256-hash van het X.509-certificaat (opgeslagen met binaire DER-codering). U kunt een primaire vingerafdruk of een secundaire vingerafdruk of beide opgeven. Primaire en secundaire vingerafdruk worden ondersteund voor het afhandelen van scenario's voor het overdraaien van certificaten.

Hier is een voorbeeld van een C-codefragment voor het registreren van een apparaat met een vingerafdruk van een \# X.509-certificaat:

```csharp
var device = new Device(deviceId)
{
  Authentication = new AuthenticationMechanism()
  {
    X509Thumbprint = new X509Thumbprint()
    {
      PrimaryThumbprint = "B4172AB44C28F3B9E117648C6F7294978A00CDCBA34A46A1B8588B3F7D82C4F1"
    }
  }
};
RegistryManager registryManager = RegistryManager.CreateFromConnectionString(deviceGatewayConnectionString);
await registryManager.AddDeviceAsync(device);
```

### <a name="use-an-x509-certificate-during-run-time-operations"></a>Een X.509-certificaat gebruiken tijdens run time-bewerkingen

De [Apparaat-SDK van Azure IoT](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/iothub/device) voor .NET (versie 1.0.11+) ondersteunt het gebruik van X.509-certificaten.

### <a name="c-support"></a>\#C-ondersteuning

De klasse **DeviceAuthenticationWithX509Certificate** ondersteunt het maken van **DeviceClient-exemplaren** met behulp van een X.509-certificaat. Het X.509-certificaat moet de PFX-indeling (ook wel PKCS #12 genoemd) hebben die de persoonlijke sleutel bevat.

Hier is een voorbeeldcodefragment:

```csharp
var authMethod = new DeviceAuthenticationWithX509Certificate("<device id>", x509Certificate);

var deviceClient = DeviceClient.Create("<IotHub DNS HostName>", authMethod);
```

## <a name="custom-device-and-module-authentication"></a>Aangepaste apparaat- en moduleverificatie

U kunt het IoT Hub [identiteitsregister gebruiken om](iot-hub-devguide-identity-registry.md) beveiligingsreferenties per apparaat/module en toegangsbeheer te configureren met behulp van [tokens.](iot-hub-devguide-security.md#security-tokens) Als een IoT-oplossing al een aangepast identiteitsregister en/of verificatieschema heeft, kunt u overwegen om een *tokenservice* te maken om deze infrastructuur te integreren met IoT Hub. Op deze manier kunt u andere IoT-functies in uw oplossing gebruiken.

Een tokenservice is een aangepaste cloudservice. Er wordt gebruikgemaakt *IoT Hub* beleid voor gedeelde toegang met **DeviceConnect-** of **ModuleConnect-machtigingen** om tokens binnen apparaatbereik of *modulebereik te* maken.  Met deze tokens kunnen een apparaat en module verbinding maken met uw IoT-hub.

![Stappen van het tokenservicepatroon](./media/iot-hub-devguide-security/tokenservice.png)

Hier volgen de belangrijkste stappen van het tokenservicepatroon:

1. Maak een IoT Hub voor gedeelde toegang met **de machtigingen DeviceConnect** of **ModuleConnect** voor uw IoT-hub. U kunt dit beleid maken in [Azure Portal](https://portal.azure.com) programmatisch. De tokenservice gebruikt dit beleid om de tokens te ondertekenen die worden gemaakt.

2. Wanneer een apparaat/module toegang moet hebben tot uw IoT-hub, wordt een ondertekend token van uw tokenservice gevraagd. Het apparaat kan worden geverifieerd met uw aangepaste identiteitsregister/verificatieschema om de apparaat-/module-id te bepalen die de tokenservice gebruikt om het token te maken.

3. De tokenservice retourneert een token. Het token wordt gemaakt met of als , met als het apparaat dat `/devices/{deviceId}` wordt geverifieerd of als de module die wordt `/devices/{deviceId}/module/{moduleId}` `resourceURI` `deviceId` `moduleId` geverifieerd. De tokenservice maakt gebruik van het beleid voor gedeelde toegang om het token te maken.

4. Het apparaat/de module gebruikt het token rechtstreeks met de IoT-hub.

> [!NOTE]
> U kunt de .NET-klasse [SharedAccessSignatureBuilder](/dotnet/api/microsoft.azure.devices.common.security.sharedaccesssignaturebuilder) of de Java-klasse [IotHubServiceSasToken](/java/api/com.microsoft.azure.sdk.iot.service.auth.iothubservicesastoken) gebruiken om een token in uw tokenservice te maken.

De tokenservice kan de vervaldatum van het token naar wens instellen. Wanneer het token is verlopen, wordt de verbinding met het apparaat/de module door de IoT-hub verknoopt. Vervolgens moet het apparaat/de module een nieuw token aanvragen bij de tokenservice. Een korte verlooptijd verhoogt de belasting van zowel het apparaat/de module als de tokenservice.

Als u wilt dat een apparaat/module verbinding maakt met uw hub, moet u het nog steeds toevoegen aan het IoT Hub-identiteitsregister, ook al wordt er een token gebruikt en geen sleutel om verbinding te maken. Daarom kunt u toegangsbeheer per apparaat/per module blijven gebruiken door apparaat-/module-id's in het identiteitsregister in of uit [te stellen.](iot-hub-devguide-identity-registry.md) Deze aanpak vermindert de risico's van het gebruik van tokens met lange verlooptijden.

### <a name="comparison-with-a-custom-gateway"></a>Vergelijking met een aangepaste gateway

Het tokenservicepatroon is de aanbevolen manier om een aangepast identiteitsregister/verificatieschema te implementeren met IoT Hub. Dit patroon wordt aanbevolen omdat IoT Hub het meeste verkeer van de oplossing blijft verwerken. Als het aangepaste verificatieschema echter zo is verbonden met het protocol, hebt u mogelijk een aangepaste *gateway* nodig om al het verkeer te verwerken. Een voorbeeld van een dergelijk scenario is het [gebruik Transport Layer Security (TLS) en vooraf gedeelde sleutels (PSK's).](https://tools.ietf.org/html/rfc4279) Zie het artikel [protocolgateway](iot-hub-protocol-gateway.md) voor meer informatie.

## <a name="reference-topics"></a>Naslagonderwerpen:

In de volgende naslagonderwerpen vindt u meer informatie over het beheren van de toegang tot uw IoT-hub.

## <a name="iot-hub-permissions"></a>IoT Hub machtigingen

De volgende tabel bevat de machtigingen die u kunt gebruiken om de toegang tot uw IoT-hub te bepalen.

| Machtiging | Notities |
| --- | --- |
| **RegistryRead** |Verleent leestoegang tot het identiteitsregister. Zie Identiteitsregister [voor meer informatie.](iot-hub-devguide-identity-registry.md) <br/>Deze machtiging wordt gebruikt door back-endcloudservices. |
| **RegistryReadWrite** |Verleent lees- en schrijftoegang tot het identiteitsregister. Zie Identiteitsregister [voor meer informatie.](iot-hub-devguide-identity-registry.md) <br/>Deze machtiging wordt gebruikt door back-endcloudservices. |
| **ServiceConnect** |Verleent toegang tot cloudservice-gerichte communicatie en bewakings-eindpunten. <br/>Verleent toestemming om apparaat-naar-cloud-berichten te ontvangen, cloud-naar-apparaat-berichten te verzenden en de bijbehorende leveringsbevestigingen op te halen. <br/>Verleent toestemming voor het ophalen van leveringsbevestigingen voor bestanduploads. <br/>Verleent machtigingen voor toegang tot tweelingen om tags en gewenste eigenschappen bij te werken, gerapporteerde eigenschappen op te halen en query's uit te voeren. <br/>Deze machtiging wordt gebruikt door back-endcloudservices. |
| **DeviceConnect** |Verleent toegang tot apparaat-gerichte eindpunten. <br/>Verleent toestemming om apparaat-naar-cloud-berichten te verzenden en cloud-naar-apparaat-berichten te ontvangen. <br/>Verleent machtigingen voor het uploaden van bestanden vanaf een apparaat. <br/>Verleent machtigingen voor het ontvangen van meldingen over gewenste eigenschappen van apparaattwee en het bijwerken van gerapporteerde eigenschappen van de apparaattweeling. <br/>Verleent toestemming om bestandsuploads uit te voeren. <br/>Deze machtiging wordt gebruikt door apparaten. |

## <a name="additional-reference-material"></a>Aanvullend referentiemateriaal

Andere naslagonderwerpen in IoT Hub ontwikkelaarshandleiding zijn:

* [IoT Hub eindpunten worden](iot-hub-devguide-endpoints.md) de verschillende eindpunten beschreven die elke IoT-hub blootstelt voor run-time- en beheerbewerkingen.

* [Beperking en quota beschrijven](iot-hub-devguide-quotas-throttling.md) de quota en beperkingsgedrag die van toepassing zijn op de IoT Hub service.

* [Azure IoT-apparaat- en service-SDK's](iot-hub-devguide-sdks.md) bevat de verschillende taal-SDK's die u kunt gebruiken bij het ontwikkelen van apparaat- en service-apps die communiceren met IoT Hub.

* [IoT Hub querytaal beschrijft](iot-hub-devguide-query-language.md) de querytaal die u kunt gebruiken om informatie op te halen uit IoT Hub over uw apparaattweelingen en taken.

* [IoT Hub ondersteuning voor MQTT](iot-hub-mqtt-support.md) biedt meer informatie over IoT Hub ondersteuning voor het MQTT-protocol.

* [RFC 5246- The Transport Layer Security (TLS) Protocol versie 1.2](https://tools.ietf.org/html/rfc5246/) biedt meer informatie over TLS-verificatie.

## <a name="next-steps"></a>Volgende stappen

Nu u hebt geleerd hoe u toegangsbeheer IoT Hub, bent u mogelijk geïnteresseerd in de volgende onderwerpen IoT Hub ontwikkelaarshandleiding:

* [Apparaattweeling gebruiken om de status en configuraties te synchroniseren](iot-hub-devguide-device-twins.md)
* [Een directe methode op een apparaat aanroepen](iot-hub-devguide-direct-methods.md)
* [Taken op meerdere apparaten plannen](iot-hub-devguide-jobs.md)

Als u een aantal concepten wilt uitproberen die in dit artikel worden beschreven, bekijkt u de volgende IoT Hub zelfstudies:

* [Aan de slag met Azure IoT Hub](quickstart-send-telemetry-node.md)
* [Cloud-naar-apparaat-berichten verzenden met IoT Hub](iot-hub-csharp-csharp-c2d.md)
* [Apparaat-IoT Hub-naar-cloud-berichten verwerken](tutorial-routing.md)
