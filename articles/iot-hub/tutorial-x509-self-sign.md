---
title: Zelf studie-OpenSSL gebruiken voor het maken van zelfondertekende certificaten voor Azure IoT Hub | Microsoft Docs
description: 'Zelf studie: OpenSSL gebruiken voor het maken van zelfondertekende X. 509-certificaten voor Azure IoT Hub'
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
- devx-track-azurecli
ms.openlocfilehash: 82ef2e39d5d04914e1086e0b25ccbc8e5c7c762e
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105630676"
---
# <a name="tutorial-using-openssl-to-create-self-signed-certificates"></a>Zelf studie: OpenSSL gebruiken voor het maken van zelfondertekende certificaten

U kunt een apparaat verifiëren op uw IoT Hub met twee zelfondertekende apparaat certificaten. Dit wordt ook wel vingerafdruk verificatie genoemd, omdat de certificaten vinger afdrukken (hash-waarden) bevatten die u naar de IoT hub verzendt. De volgende stappen laten zien hoe u twee zelfondertekende certificaten kunt maken.

## <a name="step-1---create-a-key-for-the-first-certificate"></a>Stap 1: een sleutel voor het eerste certificaat maken

```bash
openssl genpkey -out device1.key -algorithm RSA -pkeyopt rsa_keygen_bits:2048
```

## <a name="step-2---create-a-csr-for-the-first-certificate"></a>Stap 2: een CSR voor het eerste certificaat maken

Zorg ervoor dat u de apparaat-ID opgeeft wanneer u hierom wordt gevraagd.

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

## <a name="step-4---self-sign-certificate-1"></a>Stap 4-zelf-ondertekend certificaat 1

```bash
openssl x509 -req -days 365 -in device1.csr -signkey device1.key -out device.crt
```

## <a name="step-5---create-a-key-for-certificate-2"></a>Stap 5: een sleutel maken voor certificaat 2

Geef, wanneer u hierom wordt gevraagd, de apparaat-ID op die u hebt gebruikt voor certificaat 1.

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

## <a name="step-6---self-sign-certificate-2"></a>Stap 6-zelfondertekend certificaat 2

```bash
openssl x509 -req -days 365 -in device2.csr -signkey device2.key -out device2.crt
```

## <a name="step-7---retrieve-the-thumbprint-for-certificate-1"></a>Stap 7: de vinger afdruk voor certificaat 1 ophalen

```bash
openssl x509 -in device.crt -text -fingerprint
```

## <a name="step-8---retrieve-the-thumbprint-for-certificate-2"></a>Stap 8: de vinger afdruk voor certificaat 2 ophalen

```bash
openssl x509 -in device2.crt -text -fingerprint
```

## <a name="step-9---create-a-new-iot-device"></a>Stap 9: een nieuw IoT-apparaat maken

Navigeer naar uw IoT Hub in de Azure Portal en maak een nieuwe IoT-apparaat-id met de volgende kenmerken:

* Geef de **apparaat-id** op die overeenkomt met de onderwerpnaam van uw twee certificaten.
* Selecteer het **zelfondertekende X. 509-** verificatie type.
* Plak de hex-teken reeks-vinger afdrukken die u hebt gekopieerd van het apparaat primaire en secundaire certificaten. Zorg ervoor dat de hexadecimale teken reeksen geen scheidings tekens voor dubbele punten bevatten.

## <a name="next-steps"></a>Volgende stappen

Ga naar [verificatie van certificaat testen](tutorial-x509-test-certificate.md) om te bepalen of uw apparaat kan worden geverifieerd op uw IOT hub.
