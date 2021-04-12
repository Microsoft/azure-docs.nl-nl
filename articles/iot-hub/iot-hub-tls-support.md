---
title: Ondersteuning voor Azure IoT Hub TLS
description: Meer informatie over het gebruik van beveiligde TLS-verbindingen voor apparaten en services die communiceren met IoT Hub
services: iot-hub
author: jlian
ms.service: iot-fundamentals
ms.topic: conceptual
ms.date: 03/31/2021
ms.author: jlian
ms.openlocfilehash: 6a02b97957cc0599e2960cba551b536e83d1a902
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/02/2021
ms.locfileid: "106222552"
---
# <a name="transport-layer-security-tls-support-in-iot-hub"></a>Ondersteuning van Transport Layer Security (TLS) in IoT Hub

IoT Hub gebruikt Transport Layer Security (TLS) om verbindingen van IoT-apparaten en-services te beveiligen. Er worden op dit moment drie versies van het TLS-protocol ondersteund, namelijk versie 1,0, 1,1 en 1,2.

TLS 1,0 en 1,1 worden beschouwd als verouderd en zijn gepland voor afschaffing. Zie [TLS 1,0 en 1,1 voor IOT hub](iot-hub-tls-deprecating-1-0-and-1-1.md)afzien voor meer informatie. Als u toekomstige problemen wilt voor komen, gebruikt u TLS 1,2 als enige TLS-versie wanneer u verbinding maakt met IoT Hub.

## <a name="iot-hubs-server-tls-certificate"></a>TLS-certificaat van IoT Hub server

Tijdens een TLS-Handshake biedt IoT Hub RSA-versleutelings server certificaten om clients te verbinden. De hoofdmap is de basis-CA van de Baltimore Cyber Trust. Onlangs hebben we een wijziging doorgevoerd in ons TLS-server certificaat, zodat dit nu door nieuwe tussenliggende certificerings instanties (ICA) wordt verleend. Zie [IOT hub TLS-certificaat update](https://azure.microsoft.com/updates/iot-hub-tls-certificate-update/)voor meer informatie.

### <a name="4kb-size-limit-on-renewal"></a>limiet voor 4KB-grootte bij vernieuwen

Tijdens de verlenging van IoT Hub certificaten aan de server zijde wordt een controle uitgevoerd aan IoT Hub service zijde om te voor komen dat de `Server Hello` grootte van 4KB overschrijdt. Een client moet mini maal 4KB RAM-geheugen hebben ingesteld voor de maximale inhouds lengte buffer van het binnenkomende TLS-maximum, zodat bestaande apparaten die zijn ingesteld voor 4KB-limiet blijven werken zoals vóór het vernieuwen van het certificaat. Voor beperkte apparaten ondersteunt IoT Hub [TLS-maximale fragment lengte in Preview-fase](#tls-maximum-fragment-length-negotiation-preview). 

### <a name="elliptic-curve-cryptography-ecc-server-tls-certificate-preview"></a>TLS-certificaat (voor elliptische curve) (ECC)-server (preview)

IoT Hub TLS-certificaat van de ECC-server is beschikbaar voor open bare preview. Hoewel een soort gelijke beveiliging biedt voor RSA-certificaten, gebruikt ECC-certificaat validatie (met alleen ECC-coderings suites) tot wel 40% minder reken kracht, geheugen en band breedte. Deze besparingen zijn belang rijk voor IoT-apparaten vanwege hun kleinere profielen en geheugen, en ter ondersteuning van use cases in omgevingen met beperkte netwerk bandbreedte. 

Voor beeld van het ECC-server certificaat van IoT Hub:

1. [Maak een nieuwe IOT-hub met de preview-modus op](iot-hub-preview-mode.md).
1. [Configureer uw client](#tls-configuration-for-sdk-and-iot-edge) zo dat deze *alleen* ECDSA-coderings suites bevat en *sluit* alle RSA uit. Dit zijn de ondersteunde coderings suites voor de open bare preview van ECC-certificaten:
    - `TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256`
    - `TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384`
    - `TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256`
    - `TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384`
1. Verbind uw client met de preview IoT-hub.

## <a name="tls-12-enforcement-available-in-select-regions"></a>TLS 1,2 afdwinging beschikbaar in geselecteerde regio's

Configureer uw IoT-hubs voor extra beveiliging zodat *alleen* client verbindingen met TLS-versie 1,2 worden toegestaan en het gebruik van [coderings suites](#cipher-suites)wordt afgedwongen. Deze functie wordt alleen ondersteund in deze regio's:

* VS - oost
* VS - zuid-centraal
* VS - west 2
* VS (overheid) - Arizona
* US Gov-Virginia (TLS 1.0/1.1-ondersteuning is niet beschikbaar in deze regio-TLS 1,2 Enforcement moet zijn ingeschakeld of het maken van een IoT hub mislukt)

Volg de stappen in [IOT hub maken in azure Portal](iot-hub-create-through-portal.md)om het afdwingen van TLS 1,2 in te scha kelen, behalve

- Kies een **regio** in de bovenstaande lijst.
- Selecteer **1,2** onder **Management-> Advanced-> Transport Layer Security (tls)-> minimale TLS-versie**. Deze instelling wordt alleen weer gegeven voor IoT hub die in een ondersteunde regio is gemaakt.

    :::image type="content" source="media/iot-hub-tls-12-enforcement.png" alt-text="Scherm afbeelding die laat zien hoe u TLS 1,2-afdwinging inschakelt tijdens het maken van IoT hub":::

Als u ARM-sjabloon wilt gebruiken om te maken, dient u een nieuwe IoT Hub in te richten in een van de ondersteunde regio's en de `minTlsVersion` eigenschap in te stellen op `1.2` in de resource specificatie:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "type": "Microsoft.Devices/IotHubs",
            "apiVersion": "2020-01-01",
            "name": "<provide-a-valid-resource-name>",
            "location": "<any-of-supported-regions-below>",
            "properties": {
                "minTlsVersion": "1.2"
            },
            "sku": {
                "name": "<your-hubs-SKU-name>",
                "tier": "<your-hubs-SKU-tier>",
                "capacity": 1
            }
        }
    ]
}
```

Met de gemaakte IoT Hub resource die gebruikmaakt van deze configuratie, worden de apparaat-en service clients die verbinding proberen te maken met behulp van TLS-versies 1,0 en 1,1 geweigerd. Op dezelfde manier wordt de TLS-Handshake geweigerd als `ClientHello` in het bericht geen van de [Aanbevolen code](#cipher-suites)ringen wordt vermeld.

> [!NOTE]
> De `minTlsVersion` eigenschap is alleen-lezen en kan niet worden gewijzigd nadat de IOT hub resource is gemaakt. Daarom is het essentieel dat u op de juiste wijze test en controleert of *al* uw IOT-apparaten en-Services compatibel zijn met TLS 1,2 en de [Aanbevolen coderingen](#cipher-suites) vooraf.
> 
> Bij failovers blijft de `minTlsVersion` eigenschap van uw IOT hub geldig in de geo-paard Region-post-failover.

## <a name="cipher-suites"></a>Coderings suites

IoT-hubs die zijn geconfigureerd om alleen TLS 1,2 te accepteren, afdwingen ook het gebruik van de volgende aanbevolen coderings suites af:

* `TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256`
* `TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384`
* `TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256`
* `TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384`

Voor IoT-hubs die niet zijn geconfigureerd voor TLS 1,2-afdwinging, werkt TLS 1,2 nog steeds met de volgende coderings suites:

* `TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256`
* `TLS_DHE_RSA_WITH_AES_256_GCM_SHA384`
* `TLS_DHE_RSA_WITH_AES_128_GCM_SHA256`
* `TLS_RSA_WITH_AES_256_GCM_SHA384`
* `TLS_RSA_WITH_AES_128_GCM_SHA256`
* `TLS_RSA_WITH_AES_256_CBC_SHA256`
* `TLS_RSA_WITH_AES_128_CBC_SHA256`
* `TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA`
* `TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA`
* `TLS_RSA_WITH_AES_256_CBC_SHA`
* `TLS_RSA_WITH_AES_128_CBC_SHA`
* `TLS_RSA_WITH_3DES_EDE_CBC_SHA`

Een-client kan een lijst met hogere coderings Suites suggereren voor gebruik tijdens `ClientHello` . Sommige hiervan worden mogelijk echter niet ondersteund door IoT Hub (bijvoorbeeld `ECDHE-ECDSA-AES256-GCM-SHA384` ). In dit geval probeert IoT Hub de voor keur van de client te volgen, maar onderhandelt uiteindelijk de coderings Suite met `ServerHello` .

## <a name="tls-configuration-for-sdk-and-iot-edge"></a>TLS-configuratie voor SDK en IoT Edge

Gebruik de onderstaande koppelingen voor het configureren van TLS 1,2 en toegestane code ringen in IoT Hub client-Sdk's.

| Taal | Versies die TLS 1,2 ondersteunen | Documentatie |
|----------|------------------------------------|---------------|
| C        | Tag 2019-12-11 of hoger            | [Koppeling](https://aka.ms/Tls_C_SDK_IoT) |
| Python   | Versie 2.0.0 of nieuwer             | [Koppeling](https://aka.ms/Tls_Python_SDK_IoT) |
| C#       | Versie 1.21.4 of nieuwer            | [Koppeling](https://aka.ms/Tls_CSharp_SDK_IoT) |
| Java     | Versie 1.19.0 of nieuwer            | [Koppeling](https://aka.ms/Tls_Java_SDK_IoT) |
| Node.js   | Versie 1.12.2 of nieuwer            | [Koppeling](https://aka.ms/Tls_Node_SDK_IoT) |

IoT Edge-apparaten kunnen worden geconfigureerd voor het gebruik van TLS 1,2 bij de communicatie met IoT Hub. Gebruik voor dit doel de pagina met de [IOT Edge documentatie](https://github.com/Azure/iotedge/blob/master/edge-modules/edgehub-proxy/README.md).

## <a name="device-authentication"></a>Apparaatverificatie

Na een geslaagde TLS-Handshake kan IoT Hub een apparaat verifiëren met behulp van een symmetrische sleutel of een X. 509-certificaat. Voor verificatie op basis van een certificaat kan dit elk X. 509-certificaat zijn, inclusief ECC. IoT Hub valideert het certificaat met de vinger afdruk of certificerings instantie (CA) die u opgeeft. Zie [ondersteunde X. 509-certificaten](iot-hub-devguide-security.md#supported-x509-certificates)voor meer informatie.

## <a name="tls-maximum-fragment-length-negotiation-preview"></a>Onderhandeling over maximale fragment lengte TLS (preview-versie)

IoT Hub biedt ook ondersteuning voor de maximale lengte van TLS-onderhandeling, die ook wel TLS-frame grootte onderhandeling wordt genoemd. Deze functie is beschikbaar voor openbare preview. 

Gebruik deze functie om de maximale lengte van de Lees bare tekst op te geven in een waarde die kleiner is dan de standaard instelling van 2 ^ 14 bytes. Na onderhandelingen hebben IoT Hub en de client de fragmentatie van berichten gestart om ervoor te zorgen dat alle fragmenten kleiner zijn dan de onderhandelde lengte. Dit gedrag is handig voor het berekenen of beperken van geheugen. Zie de [officiële TLS-extensie specificatie](https://tools.ietf.org/html/rfc6066#section-4)voor meer informatie.

Officiële SDK-ondersteuning voor deze open bare preview-functie is nog niet beschikbaar. Om aan de slag te gaan

1. [Maak een nieuwe IOT-hub met de preview-modus op](iot-hub-preview-mode.md).
1. Bij gebruik van OpenSSL roept u [SSL_CTX_set_tlsext_max_fragment_length](https://manpages.debian.org/testing/libssl-doc/SSL_CTX_set_max_send_fragment.3ssl.en.html) aan om de fragment grootte op te geven.
1. Verbind uw client met de preview-IoT Hub.

## <a name="next-steps"></a>Volgende stappen

- Zie [toegang tot IOT hub beheren](iot-hub-devguide-security.md)voor meer informatie over IOT hub beveiliging en toegangs beheer.
- Zie voor meer informatie over het gebruik van x509-certificaat voor verificatie van apparaten de [verificatie van apparaten met X. 509 CA-certificaten](iot-hub-x509ca-overview.md)
