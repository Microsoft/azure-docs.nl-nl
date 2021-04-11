---
title: 'Zelf studie: eigendom bewijzen van CA-certificaten in azure IoT Hub | Microsoft Docs'
description: 'Zelf studie: bewijs dat u eigenaar bent van een CA-certificaat voor Azure IoT Hub'
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
ms.openlocfilehash: 0eb91754c3c70a7b477d456158454f707a874207
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105630678"
---
# <a name="tutorial-proving-possession-of-a-ca-certificate"></a>Zelf studie: het bezit zijn van een CA-certificaat

Nadat u uw CA-certificaat (basis certificerings instantie) of een onderliggend CA-certificaat hebt geüpload naar uw IoT-hub, moet u bewijzen dat u eigenaar bent van het certificaat:

1. Ga in het Azure Portal naar uw IoTHub en selecteer **instellingen > certificaten**.

2. Selecteer **toevoegen** om een nieuw CA-certificaat toe te voegen.

3. Voer een weergave naam in het veld **certificaat naam** in en selecteer het PEM-certificaat dat u wilt toevoegen.

4. Selecteer **Opslaan**. Uw certificaat wordt weer gegeven in de lijst certificaten met de status niet **geverifieerd**. Dit verificatie proces bewijst dat u het certificaat hebt.

5. Selecteer het certificaat om het dialoog venster **certificaat Details** weer te geven.

6. Selecteer **verificatie code genereren** in het dialoog venster.

  :::image type="content" source="media/tutorial-x509-prove-possession/certificate-details.png" alt-text="{Dialoog venster certificaat Details}":::

7. Kopieer de verificatiecode naar het klembord. U moet de verificatie code instellen als het certificaat onderwerp. Als de verificatie code bijvoorbeeld 75B86466DA34D2B04C0C4C9557A119687ADAE7D4732BDDB3 is, voegt u deze toe als het onderwerp van het certificaat, zoals in de volgende stap wordt weer gegeven.

8. Er zijn drie manieren om een verificatie certificaat te genereren:

    * Als u het Power shell-script gebruikt dat door micro soft wordt geleverd, voert u uit `New-CACertsVerificationCert "75B86466DA34D2B04C0C4C9557A119687ADAE7D4732BDDB3"` om een certificaat met de naam te maken `VerifyCert4.cer` . Zie [gebruikmaken van door micro soft geleverde scripts](tutorial-x509-scripts.md)voor meer informatie.

    * Als u het bash-script gebruikt dat door micro soft wordt geleverd, voert u uit `./certGen.sh create_verification_certificate "75B86466DA34D2B04C0C4C9557A119687ADAE7D4732BDDB3"` om een certificaat met de naam te maken `verification-code.cert.pem` . Zie [gebruikmaken van door micro soft geleverde scripts](tutorial-x509-scripts.md)voor meer informatie.

    * Als u OpenSSL gebruikt om uw certificaten te genereren, moet u eerst een persoonlijke sleutel en een aanvraag voor certificaat ondertekening (CSR) genereren:

      ```bash
      $ openssl req -new -key pop.key -out pop.csr

      -----
      Country Name (2 letter code) [XX]:.
      State or Province Name (full name) []:.
      Locality Name (eg, city) [Default City]:.
      Organization Name (eg, company) [Default Company Ltd]:.
      Organizational Unit Name (eg, section) []:.
      Common Name (eg, your name or your server hostname) []:75B86466DA34D2B04C0C4C9557A119687ADAE7D4732BDDB3
      Email Address []:

      Please enter the following 'extra' attributes
      to be sent with your certificate request
      A challenge password []:
      An optional company name []:
 
      ```

      Maak vervolgens een certificaat met behulp van het configuratie bestand van de basis-CA (hieronder weer gegeven) of het configuratie bestand van de onderliggende CA en de CSR.

      ```bash
      openssl ca -config rootca.conf -in pop.csr -out pop.crt -extensions client_ext

      ```

    Zie [openssl gebruiken om test certificaten te maken](tutorial-x509-openssl.md)voor meer informatie.

10. Selecteer in de weer gave **certificaat Details** het nieuwe certificaat.

11. Nadat het certificaat is geüpload, selecteert u **verifiëren**. De status van het CA-certificaat moet worden gewijzigd in **gecontroleerd**.
