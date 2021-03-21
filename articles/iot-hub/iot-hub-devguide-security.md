---
title: Meer informatie over Azure IoT Hub Security | Microsoft Docs
description: 'Ontwikkelaars handleiding: de toegang tot IoT Hub beheren voor apparaat-apps en back-end-apps. Bevat informatie over beveiligings tokens en ondersteuning voor X. 509-certificaten.'
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/18/2018
ms.custom:
- amqp
- mqtt
- 'Role: Cloud Development'
- 'Role: IoT Device'
- 'Role: Operations'
- devx-track-js
- devx-track-csharp
ms.openlocfilehash: 3ddc8c78bac47ed85266037341328585e3c7cb1c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97825124"
---
# <a name="control-access-to-iot-hub"></a>Toegang tot IoT Hub regelen

In dit artikel worden de opties voor het beveiligen van uw IoT-hub beschreven. IoT Hub maakt gebruik van *machtigingen* om toegang te verlenen aan elk-eind punt van de IOT-hub. Machtigingen beperken de toegang tot een IoT-hub op basis van de functionaliteit.

Dit artikel bevat het volgende:

* De verschillende machtigingen die u kunt verlenen aan een apparaat of back-end-app om toegang te krijgen tot uw IoT-hub.
* Het verificatie proces en de tokens die worden gebruikt om machtigingen te controleren.
* Het bereik van referenties om de toegang tot specifieke resources te beperken.
* IoT Hub ondersteuning voor X. 509-certificaten.
* Aangepaste mechanismen voor verificatie van apparaten die gebruikmaken van bestaande identiteits registers of verificatie schema's.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

U moet de juiste machtigingen hebben voor toegang tot een van de IoT Hub-eind punten. Een apparaat moet bijvoorbeeld een token bevatten met beveiligings referenties, samen met elk bericht dat wordt verzonden naar IoT Hub.

## <a name="access-control-and-permissions"></a>Toegangs beheer en machtigingen

U kunt op de volgende manieren [machtigingen](#iot-hub-permissions) verlenen:

* **Beleid voor gedeelde toegang tot IOT hub-niveau**. Beleid voor gedeelde toegang kan elke combi natie van [machtigingen](#iot-hub-permissions)verlenen. U kunt beleids regels definiëren in de [Azure Portal](https://portal.azure.com), programmatisch met behulp van de [IOT hub resource rest-api's](/rest/api/iothub/iothubresource)of met de [AZ IOT hub Policy](/cli/azure/iot/hub/policy) cli. Een nieuw gemaakte IoT-hub heeft het volgende standaard beleid:
  
  | Beleid voor gedeelde toegang | Machtigingen |
  | -------------------- | ----------- |
  | iothubowner | Machtiging alle |
  | service | **ServiceConnect** -machtigingen |
  | apparaat | **DeviceConnect** -machtigingen |
  | registryRead | **RegistryRead** -machtigingen |
  | registryReadWrite | **RegistryRead** -en **RegistryWrite** -machtigingen |

* **Beveiligings referenties per apparaat**. Elke IoT Hub bevat een [id-REGI ster](iot-hub-devguide-identity-registry.md) voor elk apparaat in dit id-REGI ster, kunt u beveiligings referenties configureren waarmee **DeviceConnect** -machtigingen worden toegewezen aan de bijbehorende eind punten van het apparaat.

Bijvoorbeeld in een typische IoT-oplossing:

* Het onderdeel Apparaatbeheer maakt gebruik van het *registryReadWrite* -beleid.
* Het onderdeel gebeurtenis processor maakt gebruik van het *service* beleid.
* Het onderdeel van de bedrijfs logica voor run-time apparaten maakt gebruik van het *service* beleid.
* Afzonderlijke apparaten maken verbinding met de referenties die zijn opgeslagen in het id-REGI ster van de IoT-hub.

> [!NOTE]
> Zie [machtigingen](#iot-hub-permissions) voor gedetailleerde informatie.

## <a name="authentication"></a>Verificatie

In Azure IoT Hub wordt toegang verleend aan eindpunten door een token te verifiëren op basis van de gedeelde toegangsbeleidsregels en beveiligingsreferenties van het identiteitsregister.

Beveiligings referenties, zoals symmetrische sleutels, worden nooit via de kabel verzonden.

> [!NOTE]
> De resource provider van Azure IoT Hub wordt beveiligd via uw Azure-abonnement, evenals alle providers in de [Azure Resource Manager](../azure-resource-manager/management/overview.md).

Zie [IOT hub-beveiligings tokens](iot-hub-devguide-security.md#security-tokens)voor meer informatie over het maken en gebruiken van beveiligings tokens.

### <a name="protocol-specifics"></a>Protocol specificaties

Elk ondersteund protocol, zoals MQTT, AMQP en HTTPS, transporteert tokens op verschillende manieren.

Wanneer u MQTT gebruikt, heeft het CONNECT-pakket de deviceId als ClientId, `{iothubhostname}/{deviceId}` in het veld username en een SAS-token in het veld wacht woord. `{iothubhostname}` moet de volledige CName zijn van de IoT hub (bijvoorbeeld contoso.azure-devices.net).

Wanneer u [AMQP](https://www.amqp.org/)gebruikt, ondersteunt IOT hub [sasl Plain](https://tools.ietf.org/html/rfc4616) en [AMQP op claims gebaseerde beveiliging](https://www.oasis-open.org/committees/download.php/50506/amqp-cbs-v1%200-wd02%202013-08-12.doc).

Als u AMQP op claims gebaseerde beveiliging gebruikt, bepaalt de standaard hoe deze tokens moeten worden verzonden.

Voor SASL PLAIN kan de **gebruikers naam** de volgende zijn:

* `{policyName}@sas.root.{iothubName}` Als u IoT hub-tokens gebruikt.
* `{deviceId}@sas.{iothubname}` Als tokens met het bereik van het apparaat worden gebruikt.

In beide gevallen bevat het wachtwoord veld het token, zoals beschreven in [IOT hub beveiligings tokens](iot-hub-devguide-security.md#security-tokens).

HTTPS implementeert verificatie door een geldig token op te nemen in de header van de **autorisatie** aanvraag.

#### <a name="example"></a>Voorbeeld

Gebruikers naam (DeviceId is hoofdletter gevoelig): `iothubname.azure-devices.net/DeviceId`

Wacht woord (u kunt een SAS-token genereren met de CLI [-extensie opdracht AZ IOT hub generate-SAS-token](/cli/azure/ext/azure-iot/iot/hub#ext-azure-iot-az-iot-hub-generate-sas-token)of de [Azure IOT-Hulpprogram Ma's voor Visual Studio code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)):

`SharedAccessSignature sr=iothubname.azure-devices.net%2fdevices%2fDeviceId&sig=kPszxZZZZZZZZZZZZZZZZZAhLT%2bV7o%3d&se=1487709501`

> [!NOTE]
> De [Azure IOT sdk's](iot-hub-devguide-sdks.md) genereren automatisch tokens wanneer er verbinding wordt gemaakt met de service. In sommige gevallen biedt de Azure IoT Sdk's geen ondersteuning voor alle protocollen of alle verificatie methoden.

### <a name="special-considerations-for-sasl-plain"></a>Speciale overwegingen voor SASL PLAIN

Bij het gebruik van SASL PLAIN met AMQP kan een client die verbinding maakt met een IoT-hub één token gebruiken voor elke TCP-verbinding. Wanneer het token verloopt, wordt de verbinding met de TCP-verbinding met de service verbroken en wordt een nieuwe verbinding geactiveerd. Dit gedrag, terwijl er geen problemen zijn met een back-end-app, is voor de volgende redenen schadelijk voor een apparaat-app:

* Gateways maken meestal verbinding namens een groot aantal apparaten. Wanneer u SASL PLAIN gebruikt, moet u een afzonderlijke TCP-verbinding maken voor elk apparaat dat verbinding maakt met een IoT-hub. In dit scenario wordt het verbruik van energie-en netwerk bronnen aanzienlijk verhoogd en wordt de latentie van elke verbinding met het apparaat verhoogd.

* Bron-beperkte apparaten worden nadelig beïnvloed door het verhoogde gebruik van bronnen om opnieuw verbinding te maken na elke verval datum van het token.

## <a name="scope-iot-hub-level-credentials"></a>Scope IoT-referenties voor hub-niveau

U kunt het beveiligings beleid voor IoT hub-niveau bereiken door tokens met een beperkte resource-URI te maken. Bijvoorbeeld, het eind punt voor het verzenden van apparaat-naar-Cloud-berichten van een apparaat is **/devices/{deviceId}/messages/events**. U kunt ook een beleid voor gedeelde toegang op IoT-niveau gebruiken met **DeviceConnect** -machtigingen voor het ondertekenen van een token waarvan de resourceURI **/devices/{deviceId}** is. Deze benadering maakt een token dat alleen kan worden gebruikt voor het verzenden van berichten namens de **deviceId** van het apparaat.

Dit mechanisme is vergelijkbaar met het [beleid](https://code.msdn.microsoft.com/Service-Bus-Event-Hub-99ce67ab)van de Event hubs-Uitgever en biedt u de mogelijkheid om aangepaste verificatie methoden te implementeren.

## <a name="security-tokens"></a>Beveiligingstokens

IoT Hub maakt gebruik van beveiligings tokens om apparaten en services te verifiëren om te voor komen dat sleutels op de kabel worden verzonden. Daarnaast worden beveiligings tokens beperkt in de geldigheid van de tijd en het bereik. [Azure IOT sdk's](iot-hub-devguide-sdks.md) genereren automatisch tokens zonder dat hiervoor speciale configuratie is vereist. Voor sommige scenario's moet u beveiligings tokens rechtstreeks genereren en gebruiken. Deze scenario's zijn onder andere:

* Het directe gebruik van de MQTT-, AMQP-of HTTPS-Opper vlakken.

* De implementatie van het token service patroon, zoals wordt uitgelegd in [aangepaste verificatie van apparaten](iot-hub-devguide-security.md#custom-device-and-module-authentication).

IoT Hub kunnen ook apparaten verifiëren met IoT Hub met [X. 509-certificaten](iot-hub-devguide-security.md#supported-x509-certificates).

### <a name="security-token-structure"></a>Structuur van beveiligings token

U gebruikt beveiligings tokens om tijdgebonden toegang tot apparaten en services te verlenen aan specifieke functionaliteit in IoT Hub. Als u de autorisatie wilt gebruiken om verbinding te maken met IoT Hub, moeten apparaten en services beveiligings tokens verzenden die zijn ondertekend met een gedeelde toegang of symmetrische sleutel. Deze sleutels worden opgeslagen met een apparaat-id in het identiteits register.

Een token dat is ondertekend met een gedeelde toegangs sleutel, verleent toegang tot alle functies die zijn gekoppeld aan de machtigingen voor het gedeelde toegangs beleid. Een token dat is ondertekend met de symmetrische sleutel van een apparaat-id, krijgt alleen de **DeviceConnect** -machtiging voor de bijbehorende apparaat-id.

Het beveiligings token heeft de volgende indeling:

`SharedAccessSignature sig={signature-string}&se={expiry}&skn={policyName}&sr={URL-encoded-resourceURI}`

Dit zijn de verwachte waarden:

| Waarde | Beschrijving |
| --- | --- |
| ondertekening |Een HMAC-SHA256-handtekening teken reeks van het formulier: `{URL-encoded-resourceURI} + "\n" + expiry` . **Belang rijk**: de sleutel wordt gedecodeerd op basis van base64 en gebruikt als sleutel voor het uitvoeren van de HMAC-sha256-berekening. |
| ResourceURI |URI-voor voegsel (per segment) van de eind punten die toegankelijk zijn met dit token, beginnend met de hostnaam van de IoT hub (geen Protocol). Bijvoorbeeld: `myHub.azure-devices.net/devices/device1` |
| verloop |UTF8-teken reeksen voor het aantal seconden sinds de epoche 00:00:00 UTC op 1 januari 1970. |
| {URL-encoded-resourceURI} |Kleine letter-URL-code ring van de resource-URI voor kleine letters |
| PolicyName |De naam van het gedeelde toegangs beleid waarnaar dit token verwijst. Niet aanwezig als het token naar de register referenties van het apparaat verwijst. |

**Opmerking op voor voegsel**: het URI-voor voegsel wordt berekend door een segment en niet op basis van het teken. Bijvoorbeeld `/a/b` een voor voegsel voor, `/a/b/c` maar niet voor `/a/bc` .

Het volgende Node.js fragment bevat een functie met de naam **generateSasToken** die het token van de invoer berekent `resourceUri, signingKey, policyName, expiresInMins` . In de volgende secties wordt beschreven hoe u de verschillende invoer gegevens voor de verschillende token-use cases initialiseert.

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

Als vergelijking wordt de equivalente python-code voor het genereren van een beveiligings token:

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

De functionaliteit in C# voor het genereren van een beveiligings token is:

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
> Omdat de geldigheids duur van het token op IoT Hub machines is gevalideerd, moet de drift op de klok van de computer die het token genereert, mini maal zijn.

### <a name="use-sas-tokens-in-a-device-app"></a>SAS-tokens in een apparaat-app gebruiken

Er zijn twee manieren om **DeviceConnect** -machtigingen te verkrijgen met IOT hub met beveiligings tokens: gebruik een [symmetrische sleutel in het identiteits register](#use-a-symmetric-key-in-the-identity-registry)of gebruik een [gedeelde toegangs sleutel](#use-a-shared-access-policy).

Houd er rekening mee dat alle functies die toegankelijk zijn vanaf apparaten, worden weer gegeven in het ontwerp van eind punten met het voor voegsel `/devices/{deviceId}` .

> [!IMPORTANT]
> De enige manier waarop IoT Hub een specifiek apparaat verifieert, gebruikt de symmetrische sleutel van de apparaat-id. Als er een gedeeld toegangs beleid wordt gebruikt voor toegang tot de functionaliteit van het apparaat, moet de oplossing rekening houden met het onderdeel dat het beveiligings token afgeeft als een vertrouwd subonderdeel.

De op het apparaat gerichte eind punten zijn (ongeacht het Protocol):

| Eindpunt | Functionaliteit |
| --- | --- |
| `{iot hub host name}/devices/{deviceId}/messages/events` |Apparaat-naar-Cloud-berichten verzenden. |
| `{iot hub host name}/devices/{deviceId}/messages/devicebound` |Cloud-naar-apparaat-berichten ontvangen. |

### <a name="use-a-symmetric-key-in-the-identity-registry"></a>Een symmetrische sleutel gebruiken in het identiteits register

Wanneer u de symmetrische sleutel van een apparaat-id gebruikt om een token te genereren, `skn` wordt het argument ElementName () van het token wegge laten.

Een token dat is gemaakt voor toegang tot alle functies van het apparaat moet bijvoorbeeld de volgende para meters hebben:

* resource-URI: `{IoT hub name}.azure-devices.net/devices/{device id}` ,
* handtekening sleutel: elke symmetrische sleutel voor de `{device id}` identiteit,
* geen beleids naam,
* een verloop tijd.

Een voor beeld van het gebruik van de voor gaande Node.js functie is:

```javascript
var endpoint ="myhub.azure-devices.net/devices/device1";
var deviceKey ="...";

var token = generateSasToken(endpoint, deviceKey, null, 60);
```

Het resultaat, waarmee toegang tot alle functionaliteit voor device1 wordt verleend, zou zijn:

`SharedAccessSignature sr=myhub.azure-devices.net%2fdevices%2fdevice1&sig=13y8ejUk2z7PLmvtwR5RqlGBOVwiq7rQR3WZ5xZX3N4%3D&se=1456971697`

> [!NOTE]
> Het is mogelijk om een SAS-token te genereren met de CLI [-extensie opdracht AZ IOT hub generate-SAS-token](/cli/azure/ext/azure-iot/iot/hub#ext-azure-iot-az-iot-hub-generate-sas-token)of de [Azure IOT-Hulpprogram Ma's voor Visual Studio code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools).

### <a name="use-a-shared-access-policy"></a>Een beleid voor gedeelde toegang gebruiken

Wanneer u een token maakt op basis van een gedeeld toegangs beleid, stelt u het `skn` veld in op de naam van het beleid. Dit beleid moet de machtiging **DeviceConnect** verlenen.

De twee belangrijkste scenario's voor het gebruik van gedeelde toegangs beleid voor de functionaliteit van het apparaat zijn:

* [Cloud protocol gateways](iot-hub-devguide-endpoints.md),
* [token Services](iot-hub-devguide-security.md#custom-device-and-module-authentication) die worden gebruikt voor het implementeren van aangepaste verificatie schema's.

Omdat het gedeelde toegangs beleid mogelijk toegang kan verlenen om verbinding te maken als elk apparaat, is het belang rijk om de juiste resource-URI te gebruiken bij het maken van beveiligings tokens. Deze instelling is met name belang rijk voor token Services, die het token met behulp van de resource-URI op een specifiek apparaat moeten kunnen bereiken. Dit punt is minder relevant voor protocol gateways omdat het verkeer voor alle apparaten al is doorgevoerd.

Een token service die het vooraf gemaakte Shared Access-beleid met de naam **apparaat** gebruikt, zou bijvoorbeeld een token met de volgende para meters maken:

* resource-URI: `{IoT hub name}.azure-devices.net/devices/{device id}` ,
* handtekening sleutel: een van de sleutels van het `device` beleid,
* beleids naam: `device` ,
* een verloop tijd.

Een voor beeld van het gebruik van de voor gaande Node.js functie is:

```javascript
var endpoint ="myhub.azure-devices.net/devices/device1";
var policyName = 'device';
var policyKey = '...';

var token = generateSasToken(endpoint, policyKey, policyName, 60);
```

Het resultaat, waarmee toegang tot alle functionaliteit voor device1 wordt verleend, zou zijn:

`SharedAccessSignature sr=myhub.azure-devices.net%2fdevices%2fdevice1&sig=13y8ejUk2z7PLmvtwR5RqlGBOVwiq7rQR3WZ5xZX3N4%3D&se=1456971697&skn=device`

Een protocol gateway kan hetzelfde token voor alle apparaten gebruiken om eenvoudigweg de bron-URI in te stellen op `myhub.azure-devices.net/devices` .

### <a name="use-security-tokens-from-service-components"></a>Beveiligings tokens van service onderdelen gebruiken

Service onderdelen kunnen alleen beveiligings tokens genereren met behulp van Shared Access policies die de juiste machtigingen verlenen, zoals eerder is uitgelegd.

Hier volgen de service functies die beschikbaar zijn op de eind punten:

| Eindpunt | Functionaliteit |
| --- | --- |
| `{iot hub host name}/devices` |Apparaat-id's maken, bijwerken, ophalen en verwijderen. |
| `{iot hub host name}/messages/events` |Apparaat-naar-Cloud-berichten ontvangen. |
| `{iot hub host name}/servicebound/feedback` |Feedback ontvangen voor Cloud-naar-apparaat-berichten. |
| `{iot hub host name}/devicebound` |Cloud-naar-apparaat-berichten verzenden. |

Een voor beeld: een service die wordt gegenereerd met het vooraf gemaakte gedeelde toegangs beleid met de naam **registryRead** , maakt een token met de volgende para meters:

* resource-URI: `{IoT hub name}.azure-devices.net/devices` ,
* handtekening sleutel: een van de sleutels van het `registryRead` beleid,
* beleids naam: `registryRead` ,
* een verloop tijd.

```javascript
var endpoint ="myhub.azure-devices.net/devices";
var policyName = 'registryRead';
var policyKey = '...';

var token = generateSasToken(endpoint, policyKey, policyName, 60);
```

Het resultaat, waarmee toegang wordt verleend om alle apparaat-id's te lezen, zou zijn:

`SharedAccessSignature sr=myhub.azure-devices.net%2fdevices&sig=JdyscqTpXdEJs49elIUCcohw2DlFDR3zfH5KqGJo4r4%3D&se=1456973447&skn=registryRead`

## <a name="supported-x509-certificates"></a>Ondersteunde X. 509-certificaten

U kunt elk X. 509-certificaat gebruiken om een apparaat te verifiëren met IoT Hub door een certificaat vingerafdruk of een certificerings instantie (CA) te uploaden naar Azure IoT Hub. Verificatie met behulp van certificaat vingerafdrukten controleert of de gepresenteerde vinger afdruk overeenkomt met de geconfigureerde vinger afdruk. Verificatie met behulp van certificerings instantie valideert de certificaat keten. In beide gevallen vereist TLS-Handshake dat het apparaat een geldig certificaat en een geldige persoonlijke sleutel heeft. Raadpleeg de TLS-specificatie voor meer informatie, bijvoorbeeld: [RFC 5246-de Transport Layer Security (TLS) Protocol versie 1,2](https://tools.ietf.org/html/rfc5246/).

Ondersteunde certificaten zijn onder andere:

* **Een bestaand X. 509-certificaat**. Er is mogelijk al een X. 509-certificaat gekoppeld aan een apparaat. Het apparaat kan dit certificaat gebruiken om te verifiëren met IoT Hub. Werkt met een vinger afdruk of CA-verificatie. 

* **CA-ondertekend X. 509-certificaat**. Als u een apparaat wilt identificeren en dit wilt verifiëren met IoT Hub, kunt u een X. 509-certificaat gebruiken dat is gegenereerd en ondertekend door een certificerings instantie (CA). Werkt met een vinger afdruk of CA-verificatie.

* **Een zelf gegenereerd en zelfondertekend X-509-certificaat**. Een fabrikant van het apparaat of de interne implementeerder kan deze certificaten genereren en de bijbehorende persoonlijke sleutel (en het certificaat) opslaan op het apparaat. Voor dit doel kunt u hulpprogram ma's zoals [openssl](https://www.openssl.org/) en [Windows SelfSignedCertificate](/powershell/module/pkiclient/new-selfsignedcertificate) Utility gebruiken. Werkt alleen met vingerafdruk verificatie.

Een apparaat kan een X. 509-certificaat of een beveiligings token voor verificatie gebruiken, maar niet beide. Met X. 509-certificaat verificatie moet u een strategie hebben voor het afhandelen van de certificaat overschakeling wanneer een bestaand certificaat verloopt.

De volgende functionaliteit wordt niet ondersteund voor apparaten die gebruikmaken van X. 509 CA-verificatie:

* HTTPS, MQTT over websockets en AMQP over websockets-protocollen.
* Uploads van bestanden (alle protocollen).

Zie voor meer informatie over verificatie met behulp van certificerings instantie [verificatie van apparaten met behulp van X. 509 CA-certificaten](iot-hub-x509ca-overview.md). Voor informatie over het uploaden en verifiëren van een certificerings instantie met uw IoT-hub raadpleegt u [X. 509-beveiliging instellen in uw Azure IOT hub](iot-hub-security-x509-get-started.md).

### <a name="register-an-x509-certificate-for-a-device"></a>Een X. 509-certificaat voor een apparaat registreren

De [Azure IOT Service SDK voor C#](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/iothub/service) (versie 1.0.8 +) ondersteunt het registreren van een apparaat dat gebruikmaakt van een X. 509-certificaat voor verificatie. Andere Api's, zoals import/export van apparaten ondersteunen ook X. 509-certificaten.

U kunt ook de CLI-extensie opdracht [AZ IOT hub apparaat-Identity](/cli/azure/ext/azure-iot/iot/hub/device-identity) gebruiken om X. 509-certificaten voor apparaten te configureren.

### <a name="c-support"></a>C- \# ondersteuning

De **RegistryManager** -klasse biedt een programmatische manier om een apparaat te registreren. Met name de methoden **AddDeviceAsync** en **UpdateDeviceAsync** bieden u de mogelijkheid om een apparaat te registreren en bij te werken in het IOT hub identiteits register. Deze twee methoden maken een **apparaatinstantie** als invoer. De **apparaatklasse** bevat een **verificatie** -eigenschap waarmee u de primaire en secundaire X. 509-certificaat vingerafdrukken kunt opgeven. De vinger afdruk vertegenwoordigt een SHA256-hash van het X. 509-certificaat (opgeslagen met binaire en andere code ring). U kunt een primaire vinger afdruk of een secundaire vinger afdruk of beide opgeven. Primaire en secundaire vinger afdrukken worden ondersteund voor het afhandelen van certificaat overschakel scenario's.

Hier volgt een voor beeld van een C- \# code fragment om een apparaat te registreren met behulp van een X. 509-certificaat vingerafdruk:

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

### <a name="use-an-x509-certificate-during-run-time-operations"></a>Een X. 509-certificaat gebruiken tijdens runtime-bewerkingen

De [Azure IOT Device SDK voor .net](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/iothub/device) (versie 1.0.11 +) ondersteunt het gebruik van X. 509-certificaten.

### <a name="c-support"></a>C- \# ondersteuning

De klasse **DeviceAuthenticationWithX509Certificate** ondersteunt het maken van **DeviceClient** -instanties met behulp van een X. 509-certificaat. Het X. 509-certificaat moet zijn opgenomen in de PFX-indeling (ook wel PKCS #12 genoemd) die de persoonlijke sleutel bevat.

Hier volgt een voor beeld van een code fragment:

```csharp
var authMethod = new DeviceAuthenticationWithX509Certificate("<device id>", x509Certificate);

var deviceClient = DeviceClient.Create("<IotHub DNS HostName>", authMethod);
```

## <a name="custom-device-and-module-authentication"></a>Aangepaste apparaat-en module verificatie

U kunt het [REGI ster](iot-hub-devguide-identity-registry.md) van de IOT hub-id gebruiken voor het configureren van beveiligings referenties per apparaat/module en toegangs beheer met behulp van [tokens](iot-hub-devguide-security.md#security-tokens). Als een IoT-oplossing al een aangepast identiteits register en/of verificatie schema heeft, kunt u overwegen een *token service* te maken voor de integratie van deze infra structuur met IOT hub. Op deze manier kunt u andere IoT-functies in uw oplossing gebruiken.

Een token service is een aangepaste Cloud service. Er wordt gebruikgemaakt van een IoT Hub *beleid voor gedeelde toegang* met **DeviceConnect** -of **ModuleConnect** -machtigingen voor het maken van tokens in *het bereik* van een apparaat of *module-bereik* . Met deze tokens kunnen apparaten en modules verbinding maken met uw IoT-hub.

![Stappen van het patroon van de token service](./media/iot-hub-devguide-security/tokenservice.png)

Hier volgen de belangrijkste stappen van het patroon van de token service:

1. Maak een IoT Hub beleid voor gedeelde toegang met **DeviceConnect** -of **ModuleConnect** -machtigingen voor uw IOT-hub. U kunt dit beleid in de [Azure Portal](https://portal.azure.com) of via een programma maken. De token service gebruikt dit beleid om de tokens te ondertekenen die het maakt.

2. Wanneer een apparaat/module toegang heeft tot uw IoT-hub, vraagt deze een ondertekend token aan bij uw token service. Het apparaat kan worden geverifieerd met uw aangepaste identiteits register/verificatie schema om te bepalen welke apparaat-of module-id er door de token service wordt gebruikt om het token te maken.

3. De token service retourneert een token. Het token wordt gemaakt met `/devices/{deviceId}` of `/devices/{deviceId}/module/{moduleId}` als `resourceURI` , met `deviceId` als het apparaat wordt geverifieerd of `moduleId` als de module wordt geverifieerd. De token service maakt gebruik van het beleid voor gedeelde toegang om het token te maken.

4. Het apparaat/de module gebruikt het token rechtstreeks met de IoT-hub.

> [!NOTE]
> U kunt de .NET-klasse [SharedAccessSignatureBuilder](/dotnet/api/microsoft.azure.devices.common.security.sharedaccesssignaturebuilder) of de Java-klasse [IotHubServiceSasToken](/java/api/com.microsoft.azure.sdk.iot.service.auth.iothubservicesastoken) gebruiken om een token in uw token service te maken.

De token service kan de verval datum van het token naar wens instellen. Wanneer het token verloopt, wordt de verbinding met het apparaat/de module door de IoT-hub. Vervolgens moet het apparaat/de module een nieuw token aanvragen bij de token service. Een korte verloop tijd verhoogt de belasting van het apparaat/de module en de token service.

Als u een apparaat/module wilt gebruiken om verbinding te maken met uw hub, moet u deze nog steeds toevoegen aan het IoT Hub-identiteits register, zelfs al gebruikt u een token en niet een sleutel om verbinding te maken. Daarom kunt u het toegangs beheer per apparaat/per module blijven gebruiken door identiteiten van apparaten/modules in of uit te scha kelen in het [identiteits register](iot-hub-devguide-identity-registry.md). Deze aanpak vermindert de Risico's van het gebruik van tokens met lange verloop tijden.

### <a name="comparison-with-a-custom-gateway"></a>Vergelijking met een aangepaste gateway

Het token service patroon is de aanbevolen manier om een aangepast schema voor identiteits verificatie/authenticatie te implementeren met IoT Hub. Dit patroon wordt aanbevolen omdat IoT Hub het grootste deel van het oplossings verkeer blijft afhandelen. Als het aangepaste verificatie schema echter zo is Intertwined met het Protocol, hebt u mogelijk een *aangepaste gateway* nodig om al het verkeer te verwerken. Een voor beeld van een dergelijk scenario is het gebruik van [Transport Layer Security (TLS) en vooraf gedeelde sleutels (PSKs)](https://tools.ietf.org/html/rfc4279). Zie het artikel over [protocol gateway](iot-hub-protocol-gateway.md) voor meer informatie.

## <a name="reference-topics"></a>Naslag onderwerpen:

In de volgende onderwerpen vindt u meer informatie over het beheren van de toegang tot uw IoT-hub.

## <a name="iot-hub-permissions"></a>IoT Hub machtigingen

De volgende tabel bevat de machtigingen die u kunt gebruiken om de toegang tot uw IoT-hub te beheren.

| Machtiging | Notities |
| --- | --- |
| **RegistryRead** |Hiermee wordt lees toegang verleend aan het identiteits register. Zie [identiteits register](iot-hub-devguide-identity-registry.md)voor meer informatie. <br/>Deze machtiging wordt gebruikt door de Cloud Services van de back-end. |
| **RegistryReadWrite** |Hiermee wordt lees-en schrijf toegang verleend aan het identiteits register. Zie [identiteits register](iot-hub-devguide-identity-registry.md)voor meer informatie. <br/>Deze machtiging wordt gebruikt door de Cloud Services van de back-end. |
| **ServiceConnect** |Verleent toegang tot Cloud service gerichte communicatie en bewakings eindpunten. <br/>Verleent toestemming om apparaat-naar-Cloud-berichten te ontvangen, Cloud-naar-apparaat-berichten te verzenden en de bijbehorende bezorgings bevestigingen op te halen. <br/>Hiermee wordt een machtiging verleend voor het ophalen van ontvangst bevestigingen voor het uploaden van bestanden. <br/>Geeft toestemming voor toegang tot apparaatdubbels om labels en gewenste eigenschappen bij te werken, gerapporteerde eigenschappen op te halen en query's uit te voeren. <br/>Deze machtiging wordt gebruikt door de Cloud Services van de back-end. |
| **DeviceConnect** |Hiermee wordt toegang verleend aan aan het apparaat gerichte eind punten. <br/>Hiermee wordt toestemming verleend om apparaat-naar-Cloud-berichten te verzenden en Cloud-naar-apparaat-berichten te ontvangen. <br/>Geeft toestemming om het uploaden van bestanden vanaf een apparaat uit te voeren. <br/>Verleent toestemming voor het ontvangen van dubbele gewenste eigenschaps meldingen en het bijwerken van de dubbele gerapporteerde eigenschappen van het apparaat. <br/>Verleent machtigingen voor het uitvoeren van Bestands uploads. <br/>Deze machtiging wordt gebruikt door apparaten. |

## <a name="additional-reference-material"></a>Extra referentie materiaal

Andere naslag onderwerpen in de IoT Hub ontwikkelaars handleiding zijn:

* [IOT hub-eind punten](iot-hub-devguide-endpoints.md) beschrijven de verschillende eind punten die elke IOT-hub beschikbaar maakt voor runtime-en beheer bewerkingen.

* Met [beperking en quota's](iot-hub-devguide-quotas-throttling.md) worden de quota's en beperkings gedrag beschreven die van toepassing zijn op de IOT hub-service.

* Met de [sdk's van Azure IOT-apparaat en-service](iot-hub-devguide-sdks.md) worden de diverse sdk's voor de taal weer gegeven die u kunt gebruiken bij het ontwikkelen van zowel apparaat-als service-apps die communiceren met IOT hub.

* [IOT hub query taal](iot-hub-devguide-query-language.md) beschrijft de query taal die u kunt gebruiken om informatie op te halen van IOT hub over de apparaatdubbels en taken van uw apparaat.

* [IOT hub MQTT-ondersteuning](iot-hub-mqtt-support.md) biedt meer informatie over IOT hub ondersteuning voor het MQTT-protocol.

* [RFC 5246-de Transport Layer Security (TLS)-Protocol versie 1,2](https://tools.ietf.org/html/rfc5246/) biedt meer informatie over TLS-verificatie.

## <a name="next-steps"></a>Volgende stappen

Nu u hebt geleerd hoe u de toegangs IoT Hub kunt beheren, bent u mogelijk geïnteresseerd in de volgende IoT Hub onderwerpen over de ontwikkelaars handleiding:

* [Apparaatdubbels gebruiken om status en configuraties te synchroniseren](iot-hub-devguide-device-twins.md)
* [Een rechtstreekse methode aanroepen op een apparaat](iot-hub-devguide-direct-methods.md)
* [Taken op meerdere apparaten plannen](iot-hub-devguide-jobs.md)

Als u een aantal van de concepten wilt uitproberen die in dit artikel worden beschreven, raadpleegt u de volgende IoT Hub zelf studies:

* [Aan de slag met Azure IoT Hub](quickstart-send-telemetry-node.md)
* [Cloud-naar-apparaat-berichten verzenden met IoT Hub](iot-hub-csharp-csharp-c2d.md)
* [IoT Hub apparaat-naar-Cloud-berichten verwerken](tutorial-routing.md)
