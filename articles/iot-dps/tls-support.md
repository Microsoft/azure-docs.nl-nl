---
title: TLS-ondersteuning voor Azure IoT Device Provisioning Service (DPS)
description: Aanbevolen procedures voor het gebruik van beveiligde TLS-verbindingen voor apparaten en services die communiceren met de IoT Device Provisioning Service (DPS)
services: iot-dps
author: wesmc7777
ms.service: iot-dps
ms.topic: conceptual
ms.date: 09/14/2020
ms.author: wesmc
ms.openlocfilehash: 3a8910cf0e81bd041d74ef95f45220f1c1e0b34c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91761291"
---
# <a name="tls-support-in-azure-iot-hub-device-provisioning-service-dps"></a>TLS-ondersteuning in azure IoT Hub Device Provisioning Service (DPS)

DPS gebruikt [Transport Layer Security (TLS)](http://wikipedia.org/wiki/Transport_Layer_Security) voor het beveiligen van verbindingen van IOT-apparaten. 

De huidige TLS-protocol versies die worden ondersteund door DPS zijn: 
* TLS 1.2

TLS 1,0 en 1,1 worden beschouwd als verouderd en zijn gepland voor afschaffing. Zie [TLS 1,0 en 1,1 voor IOT hub](../iot-hub/iot-hub-tls-deprecating-1-0-and-1-1.md)afzien voor meer informatie. 

## <a name="restrict-connections-to-tls-12"></a>Verbindingen beperken tot TLS 1,2

Voor extra beveiliging wordt u aangeraden uw DPS-exemplaren zo te configureren dat *alleen* client verbindingen van het apparaat met TLS-versie 1,2 worden toegestaan en het gebruik van [Aanbevolen code](#recommended-ciphers)ringen wordt afgedwongen.

U doet dit door een nieuwe DPS-resource in te richten, waarbij `minTlsVersion` u de eigenschap instelt op `1.2` in de DPS-resource specificatie van uw Azure Resource Manager-sjabloon. Met de volgende voorbeeld sjabloon JSON geeft u de `minTlsVersion` eigenschap op voor een nieuw DPS-exemplaar.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "type": "Microsoft.Devices/ProvisioningServices",
            "apiVersion": "2020-01-01",
            "name": "<provide-a-valid-DPS-resource-name>",
            "location": "<any-region>",
            "properties": {
                "minTlsVersion": "1.2"
            },
            "sku": {
                "name": "S1",
                "capacity": 1
            },
        }     
    ]
}
```

U kunt de sjabloon implementeren met de volgende Azure CLI-opdracht. 

```azurecli
az deployment group create -g <your resource group name> --template-file template.json
```

Zie [DPS met een Azure Resource Manager-sjabloon instellen](quick-setup-auto-provision-rm.md)voor meer informatie over het maken van DPS-resources met Resource Manager-sjablonen.

De DPS-resource die is gemaakt met deze configuratie, weigert apparaten die verbinding proberen te maken met behulp van TLS-versie 1,0 en 1,1. Op dezelfde manier wordt de TLS-Handshake geweigerd als het HELLO-bericht van de apparaatclient geen van de [Aanbevolen code](#recommended-ciphers)ringen vermeldt.

> [!NOTE]
> De `minTlsVersion` eigenschap is alleen-lezen en kan niet worden gewijzigd nadat uw DPS-resource is gemaakt. Daarom is het essentieel dat u op de juiste wijze test en controleert of *al* uw IOT-apparaten compatibel zijn met TLS 1,2 en de [Aanbevolen coderingen](#recommended-ciphers) vooraf.


> [!NOTE]
> Bij failovers blijft het `minTlsVersion` eigendom van uw DPS geldig in de geo-paard Region na failover.

## <a name="recommended-ciphers"></a>Aanbevolen code ringen

In DPS-instanties die zijn geconfigureerd om alleen TLS 1,2 te accepteren, wordt ook het gebruik van de volgende coderings suites afgedwongen:


| Aanbevolen TLS 1,2-coderings suites |
| :--- |
| `TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384`<br>`TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256`<br>`TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384`<br>`TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256` |


### <a name="legacy-cipher-suites"></a>Verouderde coderings suites 

Deze coderings suites worden momenteel nog steeds ondersteund door DPS, maar worden afgeschreven. Gebruik indien mogelijk de aanbevolen coderings suites.

| Optie #1 (betere beveiliging) |
| :--- |
| `TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA_P384   (uses SHA-1)`<br>`TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA_P256   (uses SHA-1)`<br>`TLS_RSA_WITH_AES_256_GCM_SHA384           (lack of Perfect Forward Secrecy)`<br>`TLS_RSA_WITH_AES_128_GCM_SHA256           (lack of Perfect Forward Secrecy)`<br>`TLS_RSA_WITH_AES_256_CBC_SHA256           (lack of Perfect Forward Secrecy)`<br>`TLS_RSA_WITH_AES_128_CBC_SHA256           (lack of Perfect Forward Secrecy)`<br>`TLS_RSA_WITH_AES_256_CBC_SHA              (uses SHA-1, lack of Perfect Forward Secrecy)`<br>`TLS_RSA_WITH_AES_128_CBC_SHA              (uses SHA-1, lack of Perfect Forward Secrecy)` |

| Optie #2 (betere prestaties) |
| :--- |
| `TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA_P256   (uses SHA-1)`<br>`TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA_P384   (uses SHA-1)`<br>`TLS_RSA_WITH_AES_128_GCM_SHA256           (lack of Perfect Forward Secrecy)`<br>`TLS_RSA_WITH_AES_256_GCM_SHA384           (lack of Perfect Forward Secrecy)`<br>`TLS_RSA_WITH_AES_128_CBC_SHA256           (lack of Perfect Forward Secrecy)`<br>`TLS_RSA_WITH_AES_256_CBC_SHA256           (lack of Perfect Forward Secrecy)`<br>`TLS_RSA_WITH_AES_128_CBC_SHA              (uses SHA-1, lack of Perfect Forward Secrecy)`<br>`TLS_RSA_WITH_AES_256_CBC_SHA              (uses SHA-1, lack of Perfect Forward Secrecy)` |


## <a name="use-tls-12-in-the-iot-sdks"></a>TLS 1,2 gebruiken in de IoT Sdk's

Gebruik de onderstaande koppelingen om TLS 1,2 en toegestane code ringen te configureren in de Azure IoT client-Sdk's.

| Taal | Versies die TLS 1,2 ondersteunen | Documentatie |
|----------|------------------------------------|---------------|
| C        | Tag 2019-12-11 of hoger            | [Koppeling](https://aka.ms/Tls_C_SDK_IoT) |
| Python   | Versie 2.0.0 of nieuwer             | [Koppeling](https://aka.ms/Tls_Python_SDK_IoT) |
| C#       | Versie 1.21.4 of nieuwer            | [Koppeling](https://aka.ms/Tls_CSharp_SDK_IoT) |
| Java     | Versie 1.19.0 of nieuwer            | [Koppeling](https://aka.ms/Tls_Java_SDK_IoT) |
| Node.js   | Versie 1.12.2 of nieuwer            | [Koppeling](https://aka.ms/Tls_Node_SDK_IoT) |

## <a name="use-tls-12-with-iot-hub"></a>TLS 1,2 gebruiken met IoT Hub

IoT Hub kunnen worden geconfigureerd voor het gebruik van TLS 1,2 bij het communiceren met apparaten. Zie [TLS 1,0 en 1,1 voor IOT hub](../iot-hub/iot-hub-tls-deprecating-1-0-and-1-1.md)afzien voor meer informatie.

## <a name="use-tls-12-with-iot-edge"></a>TLS 1,2 gebruiken met IoT Edge

IoT Edge-apparaten kunnen worden geconfigureerd voor het gebruik van TLS 1,2 bij de communicatie met IoT Hub en DPS. Zie de [IOT Edge-documentatie pagina](https://github.com/Azure/iotedge/blob/master/edge-modules/edgehub-proxy/README.md)voor meer informatie.
