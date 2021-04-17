---
title: 'Zelfstudie: OpenSSL gebruiken om zelf ondertekende certificaten te maken voor Azure IoT Hub | Microsoft Docs'
description: 'Zelfstudie: OpenSSL gebruiken om zelf-ondertekende X.509-certificaten te maken voor Azure IoT Hub'
author: v-gpettibone
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 02/26/2021
ms.author: robinsh
ms.custom:
- mvc
- 'Role: Cloud Development'
- 'Role: Data Analytics'
ms.openlocfilehash: 982e402946cbd71d946bc1e316cef99621c536a3
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107378190"
---
# <a name="tutorial-using-openssl-to-create-self-signed-certificates"></a>Zelfstudie: OpenSSL gebruiken om zelf ondertekende certificaten te maken

U kunt een apparaat bij uw IoT Hub met behulp van twee zelf-ondertekende apparaatcertificaten. Dit wordt ook wel vingerafdrukverificatie genoemd omdat de certificaten vingerafdruk (hash-waarden) bevatten die u naar de IoT-hub verstuurt. In de volgende stappen ziet u hoe u twee zelf-ondertekende certificaten maakt.

## <a name="step-1---create-a-key-for-the-first-certificate"></a>Stap 1: een sleutel voor het eerste certificaat maken

```bash
openssl genpkey -out device1.key -algorithm RSA -pkeyopt rsa_keygen_bits:2048
```

## <a name="step-2---create-a-csr-for-the-first-certificate"></a>Stap 2: een CSR maken voor het eerste certificaat

Zorg ervoor dat u de apparaat-id opgeeft wanneer u hier om wordt gevraagd.

```bash
openssl req -new -key device1.key -out device1.csr

Country Name (2 letter code) [XX]:.
State or Province Name (full name) []:.
Locality Name (eg, city) [Default City]:.
Organization Name (eg, company) [Default Company Ltd]:.
Organizational Unit Name (eg, section) []:.
Common Name (eg, your name or your server hostname) []:{your-device-id}
Email Address []:

```

## <a name="step-3---check-the-csr"></a>Stap 3: de CSR controleren

```bash
openssl req -text -in device1.csr -noout
```

## <a name="step-4---self-sign-certificate-1"></a>Stap 4: zelf-ondertekenen certificaat 1

```bash
openssl x509 -req -days 365 -in device1.csr -signkey device1.key -out device.crt
```

## <a name="step-5---create-a-key-for-certificate-2"></a>Stap 5: een sleutel voor certificaat 2 maken

Geef des te meer de apparaat-id op die u hebt gebruikt voor certificaat 1.

```bash
openssl req -new -key device2.key -out device2.csr

Country Name (2 letter code) [XX]:.
State or Province Name (full name) []:.
Locality Name (eg, city) [Default City]:.
Organization Name (eg, company) [Default Company Ltd]:.
Organizational Unit Name (eg, section) []:.
Common Name (eg, your name or your server hostname) []:{your-device-id}
Email Address []:

```

## <a name="step-6---self-sign-certificate-2"></a>Stap 6: zelf-ondertekenen certificaat 2

```bash
openssl x509 -req -days 365 -in device2.csr -signkey device2.key -out device2.crt
```

## <a name="step-7---retrieve-the-thumbprint-for-certificate-1"></a>Stap 7: de vingerafdruk voor certificaat 1 ophalen

```bash
openssl x509 -in device.crt -text -fingerprint
```

## <a name="step-8---retrieve-the-thumbprint-for-certificate-2"></a>Stap 8: de vingerafdruk voor certificaat 2 ophalen

```bash
openssl x509 -in device2.crt -text -fingerprint
```

## <a name="step-9---create-a-new-iot-device"></a>Stap 9: een nieuw IoT-apparaat maken

Navigeer naar IoT Hub in de Azure Portal en maak een nieuwe IoT-apparaat-id met de volgende kenmerken:

* Geef de **apparaat-id** op die overeenkomt met de onderwerpnaam van uw twee certificaten.
* Selecteer het **zelf-ondertekende verificatietype X.509.**
* Plak de vingerafdruk van de hex-tekenreeks die u hebt gekopieerd uit de primaire en secundaire certificaten van uw apparaat. Zorg ervoor dat de hexe-tekenreeksen geen dubbele punt-scheidingstekens hebben.

## <a name="next-steps"></a>Volgende stappen

Ga naar [Test Certificate Authentication om](tutorial-x509-test-certificate.md) te bepalen of uw certificaat uw apparaat kan verifiëren bij uw IoT Hub.
