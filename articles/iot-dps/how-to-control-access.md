---
title: Beveiligings eindpunten in IoT Device Provisioning Service | Microsoft Docs
description: 'Concepten: toegang tot IoT Device Provisioning Service (DPS) beheren voor back-end-apps. Bevat informatie over beveiligings tokens.'
author: wesmc7777
manager: philmea
ms.service: iot-dps
services: iot-dps
ms.topic: conceptual
ms.date: 04/09/2019
ms.author: wesmc
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: 024dbf6518748a4048873de4eb54a53f9d9a6362
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94954321"
---
# <a name="control-access-to-azure-iot-hub-device-provisioning-service"></a>Toegang tot Azure IoT Hub Device Provisioning Service beheren

In dit artikel worden de opties beschreven voor het beveiligen van uw IoT Device Provisioning-Service. De inrichtings service maakt gebruik van *machtigingen* om toegang te verlenen aan elk eind punt. Machtigingen beperken de toegang tot een service-exemplaar op basis van de functionaliteit.

In dit artikel wordt het volgende beschreven:

* De verschillende machtigingen die u kunt verlenen aan een back-end-app om toegang te krijgen tot uw inrichtings service.
* Het verificatie proces en de tokens die worden gebruikt om machtigingen te controleren.

### <a name="when-to-use"></a>Wanneer gebruikt u dit?

U moet de juiste machtigingen hebben voor toegang tot een van de inrichtings service-eind punten. Een back-end-app moet bijvoorbeeld een token bevatten met beveiligings referenties, samen met elk bericht dat wordt verzonden naar de service.

## <a name="access-control-and-permissions"></a>Toegangs beheer en machtigingen

U kunt op de volgende manieren [machtigingen](#device-provisioning-service-permissions) verlenen:

* **Verificatie beleid voor gedeelde toegang**. Beleid voor gedeelde toegang kan elke combi natie van [machtigingen](#device-provisioning-service-permissions)verlenen. U kunt beleids regels definiëren in de [Azure Portal][lnk-management-portal], of programmatisch door gebruik te maken van de [Device Provisioning Service-rest api's][lnk-resource-provider-apis]. Een nieuw gemaakte inrichtings service heeft het volgende standaard beleid:

* **provisioningserviceowner**: beleid met alle machtigingen.

> [!NOTE]
> Zie [machtigingen](#device-provisioning-service-permissions) voor gedetailleerde informatie.

## <a name="authentication"></a>Verificatie

Azure IoT Hub Device Provisioning Service verleent toegang tot eind punten door een token te controleren op basis van het beleid voor gedeelde toegang. Beveiligings referenties, zoals symmetrische sleutels, worden nooit via de kabel verzonden.

> [!NOTE]
> De resource provider van de Device Provisioning Service wordt beveiligd met uw Azure-abonnement, evenals alle providers in de [Azure Resource Manager][lnk-azure-resource-manager].

Zie de volgende sectie voor meer informatie over het maken en gebruiken van beveiligings tokens.

HTTP is het enige ondersteunde protocol en implementeert verificatie door een geldig token op te nemen in de header van de **autorisatie** aanvraag.

#### <a name="example"></a>Voorbeeld
```csharp
SharedAccessSignature sr = 
   mydps.azure-devices-provisioning.net&sig=kPszxZZZZZZZZZZZZZZZZZAhLT%2bV7o%3d&se=1487709501&skn=provisioningserviceowner`\
```

> [!NOTE]
> De [sdk's van Azure IOT Device Provisioning Service][lnk-sdks] genereren automatisch tokens wanneer er verbinding wordt gemaakt met de service.

## <a name="security-tokens"></a>Beveiligingstokens

De Device Provisioning Service maakt gebruik van beveiligings tokens voor het verifiëren van services om te voor komen dat sleutels op de kabel worden verzonden. Daarnaast worden beveiligings tokens beperkt in de geldigheid van de tijd en het bereik. De [sdk's van Azure IOT Device Provisioning Service][lnk-sdks] genereren automatisch tokens zonder dat hiervoor speciale configuratie is vereist. Voor sommige scenario's moet u beveiligings tokens rechtstreeks genereren en gebruiken. Dit scenario omvat het directe gebruik van het HTTP-Opper vlak.

### <a name="security-token-structure"></a>Structuur van beveiligings token

U gebruikt beveiligings tokens om tijdgebonden toegang te verlenen aan services aan specifieke functionaliteit in IoT Device Provisioning Service. Om autorisatie te krijgen om verbinding te maken met de inrichtings service, moeten de services beveiligings tokens verzenden die zijn ondertekend met een gedeelde toegang of symmetrische sleutel.

Een token dat is ondertekend met een gedeelde toegangs sleutel, verleent toegang tot alle functies die zijn gekoppeld aan de machtigingen voor het gedeelde toegangs beleid. 

Het beveiligings token heeft de volgende indeling:

`SharedAccessSignature sig={signature}&se={expiry}&skn={policyName}&sr={URL-encoded-resourceURI}`

Dit zijn de verwachte waarden:

| Waarde | Beschrijving |
| --- | --- |
| ondertekening |Een HMAC-SHA256-handtekening teken reeks van het formulier: `{URL-encoded-resourceURI} + "\n" + expiry` . **Belang rijk**: de sleutel wordt gedecodeerd op basis van base64 en gebruikt als sleutel voor het uitvoeren van de HMAC-sha256-berekening.|
| verloop |UTF8-teken reeksen voor het aantal seconden sinds de epoche 00:00:00 UTC op 1 januari 1970. |
| {URL-encoded-resourceURI} | Kleine letter-URL-code ring van de resource-URI voor kleine letters. URI-voor voegsel (per segment) van de eind punten die toegankelijk zijn met dit token, te beginnen met de hostnaam van de IoT Device Provisioning Service (geen Protocol). Bijvoorbeeld `mydps.azure-devices-provisioning.net`. |
| PolicyName |De naam van het gedeelde toegangs beleid waarnaar dit token verwijst. |

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
    var hmac = crypto.createHmac('sha256', new Buffer(signingKey, 'base64'));
    hmac.update(toSign);
    var base64UriEncoded = encodeURIComponent(hmac.digest('base64'));

    // Construct authorization string
    var token = "SharedAccessSignature sr=" + resourceUri + "&sig="
    + base64UriEncoded + "&se=" + expires + "&skn="+ policyName;
    return token;
};
```

Als vergelijking wordt de equivalente python-code voor het genereren van een beveiligings token:

```python
from base64 import b64encode, b64decode
from hashlib import sha256
from time import time
from urllib import quote_plus, urlencode
from hmac import HMAC

def generate_sas_token(uri, key, policy_name, expiry=3600):
    ttl = time() + expiry
    sign_key = "%s\n%d" % ((quote_plus(uri)), int(ttl))
    print sign_key
    signature = b64encode(HMAC(b64decode(key), sign_key, sha256).digest())

    rawtoken = {
        'sr' :  uri,
        'sig': signature,
        'se' : str(int(ttl)),
        'skn' : policy_name
    }

    return 'SharedAccessSignature ' + urlencode(rawtoken)
```

> [!NOTE]
> Omdat de geldigheids duur van het token wordt gevalideerd op IoT Device Provisioning Service-machines, moet de drift op de klok van de computer die het token genereert, mini maal zijn.

### <a name="use-security-tokens-from-service-components"></a>Beveiligings tokens van service onderdelen gebruiken

Service onderdelen kunnen alleen beveiligings tokens genereren met behulp van Shared Access policies die de juiste machtigingen verlenen, zoals eerder is uitgelegd.

Hier volgen de service functies die beschikbaar zijn op de eind punten:

| Eindpunt | Functionaliteit |
| --- | --- |
| `{your-service}.azure-devices-provisioning.net/enrollments` |Biedt registratie bewerkingen voor apparaten met de Device Provisioning Service. |
| `{your-service}.azure-devices-provisioning.net/enrollmentGroups` |Biedt bewerkingen voor het beheren van registratie groepen voor apparaten. |
| `{your-service}.azure-devices-provisioning.net/registrations/{id}` |Biedt bewerkingen voor het ophalen en beheren van de status van apparaatregistratie. |


Een voor beeld: een service die wordt gegenereerd met een vooraf gemaakt Shared Access-beleid met de naam **enrollmentread** , maakt een token met de volgende para meters:

* resource-URI: `{mydps}.azure-devices-provisioning.net` ,
* handtekening sleutel: een van de sleutels van het `enrollmentread` beleid,
* beleids naam: `enrollmentread` ,
* elke verloop tijd. backn

![Een gedeeld toegangs beleid voor uw Device Provisioning service-exemplaar maken in de portal][img-add-shared-access-policy]

```javascript
var endpoint ="mydps.azure-devices-provisioning.net";
var policyName = 'enrollmentread'; 
var policyKey = '...';

var token = generateSasToken(endpoint, policyKey, policyName, 60);
```

Het resultaat, dat toegang zou verlenen om alle inschrijvings records te lezen, zou zijn:

`SharedAccessSignature sr=mydps.azure-devices-provisioning.net&sig=JdyscqTpXdEJs49elIUCcohw2DlFDR3zfH5KqGJo4r4%3D&se=1456973447&skn=enrollmentread`

## <a name="reference-topics"></a>Naslag onderwerpen:

In de volgende onderwerpen vindt u meer informatie over het beheren van de toegang tot uw IoT Device Provisioning-Service.

### <a name="device-provisioning-service-permissions"></a>Machtigingen voor Device Provisioning Service

De volgende tabel geeft een lijst van de machtigingen die u kunt gebruiken voor het beheren van de toegang tot uw IoT Device Provisioning-Service.

| Machtiging | Notities |
| --- | --- |
| **ServiceConfig** |Hiermee wordt toegang verleend om de service configuraties te wijzigen. <br/>Deze machtiging wordt gebruikt door de back-end-Cloud Services. |
| **EnrollmentRead** |Hiermee wordt lees toegang verleend aan de registraties en inschrijvings groepen van het apparaat. <br/>Deze machtiging wordt gebruikt door de back-end-Cloud Services. |
| **EnrollmentWrite** |Hiermee wordt schrijf toegang verleend aan de registraties en inschrijvings groepen van het apparaat. <br/>Deze machtiging wordt gebruikt door de back-end-Cloud Services. |
| **RegistrationStatusRead** |Hiermee wordt lees toegang verleend aan de registratie status van het apparaat. <br/>Deze machtiging wordt gebruikt door de back-end-Cloud Services. |
| **RegistrationStatusWrite**  |Hiermee wordt het verwijderen van toegang verleend aan de registratie status van het apparaat. <br/>Deze machtiging wordt gebruikt door de back-end-Cloud Services. |

<!-- links and images -->

[img-add-shared-access-policy]: ./media/how-to-control-access/how-to-add-shared-access-policy.PNG
[lnk-sdks]: ../iot-hub/iot-hub-devguide-sdks.md
[lnk-management-portal]: https://portal.azure.com
[lnk-azure-resource-manager]: ../azure-resource-manager/management/overview.md
[lnk-resource-provider-apis]: /rest/api/iot-dps/