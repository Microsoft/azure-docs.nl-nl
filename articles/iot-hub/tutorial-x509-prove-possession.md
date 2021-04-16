---
title: 'Zelfstudie: Eigendom van CA-certificaten bewijzen in Azure IoT Hub | Microsoft Docs'
description: 'Zelfstudie: bewijzen dat u eigenaar bent van een CA-certificaat voor Azure IoT Hub'
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
ms.openlocfilehash: b7740fa1f6a54dcfcc1181dddedcdd5fdb50402c
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107378224"
---
# <a name="tutorial-proving-possession-of-a-ca-certificate"></a>Zelfstudie: Bezit van een CA-certificaat bewijzen

Nadat u uw basiscertificeringsinstantiecertificaat (CA) of onderliggend CA-certificaat hebt geüpload naar uw IoT-hub, moet u bewijzen dat u eigenaar bent van het certificaat:

1. Navigeer Azure Portal naar uw IoTHub en selecteer **Instellingen > certificaten.**

2. Selecteer **Toevoegen om** een nieuw CA-certificaat toe te voegen.

3. Voer een weergavenaam in het **veld Certificaatnaam** in en selecteer het PEM-certificaat dat u wilt toevoegen.

4. Selecteer **Opslaan**. Uw certificaat wordt weergegeven in de lijst met certificaten met de status **Niet geverifieerd.** Dit verificatieproces zal bewijzen dat u het certificaat in bezit hebt.

5. Selecteer het certificaat om het dialoogvenster **Certificaatdetails weer te** geven.

6. Selecteer **Verificatiecode genereren** in het dialoogvenster.

  :::image type="content" source="media/tutorial-x509-prove-possession/certificate-details.png" alt-text="Dialoogvenster {Certificaatdetails}":::

7. Kopieer de verificatiecode naar het klembord. U moet de verificatiecode instellen als het certificaatonderwerp. Als de verificatiecode bijvoorbeeld 75B86466DA34D2B04C0C4C9557A119687ADAE7D4732BDDB3 is, voegt u dat toe als onderwerp van uw certificaat, zoals wordt weergegeven in de volgende stap.

8. Er zijn drie manieren om een verificatiecertificaat te genereren:

    * Als u het PowerShell-script gebruikt dat door Microsoft is geleverd, voer dan uit om `New-CACertsVerificationCert "75B86466DA34D2B04C0C4C9557A119687ADAE7D4732BDDB3"` een certificaat met de naam te `VerifyCert4.cer` maken. Zie Using [Microsoft-supplied Scripts (Door Microsoft geleverde scripts gebruiken) voor meer informatie.](tutorial-x509-scripts.md)

    * Als u het Bash-script gebruikt dat door Microsoft is geleverd, voer dan uit om `./certGen.sh create_verification_certificate "75B86466DA34D2B04C0C4C9557A119687ADAE7D4732BDDB3"` een certificaat met de naam te `verification-code.cert.pem` maken. Zie Using [Microsoft-supplied Scripts (Door Microsoft geleverde scripts gebruiken) voor meer informatie.](tutorial-x509-scripts.md)

    * Als u OpenSSL gebruikt om uw certificaten te genereren, moet u eerst een persoonlijke sleutel genereren en vervolgens een aanvraag voor certificaat-ondertekening (CSR):

      ```bash
      $ openssl genpkey -out pop.key -algorithm RSA -pkeyopt rsa_keygen_bits:2048

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

      Maak vervolgens een certificaat met behulp van het basis-CA-configuratiebestand (zie hieronder) of het onderliggende CA-configuratiebestand en de CSR.

      ```bash
      openssl ca -config rootca.conf -in pop.csr -out pop.crt -extensions client_ext

      ```

    Zie OpenSSL gebruiken om [testcertificaten te maken voor meer informatie.](tutorial-x509-openssl.md)

10. Selecteer het nieuwe certificaat in de weergave **Certificaatdetails.**

11. Nadat het certificaat is geüpload, selecteert **u Verifiëren.** De ca-certificaatstatus moet worden gewijzigd **in Geverifieerd.**
