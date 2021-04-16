---
title: 'Zelfstudie: Meer inzicht in openbare X.509-sleutelcertificaten voor Azure IoT Hub| Microsoft Docs'
description: 'Zelfstudie: Meer inzicht in openbare X.509-sleutelcertificaten voor Azure IoT Hub'
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
ms.openlocfilehash: 5503f9ad57180146c25a01c133a27b34e643496c
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107378343"
---
# <a name="tutorial-understanding-x509-public-key-certificates"></a>Zelfstudie: Informatie over openbare X.509-sleutelcertificaten

X.509-certificaten zijn digitale documenten die een gebruiker, computer, service of apparaat vertegenwoordigen. Ze worden uitgegeven door een certificeringsinstantie (CA), onderliggende CA of registratie-instantie en bevatten de openbare sleutel van het certificaatonderwerp. Ze bevatten geen persoonlijke sleutel van het onderwerp die veilig moet worden opgeslagen. Openbare-sleutelcertificaten worden gedocumenteerd door [RFC 5280.](https://tools.ietf.org/html/rfc5280) Ze zijn digitaal ondertekend en bevatten in het algemeen de volgende informatie:

* Informatie over het certificaatonderwerp
* De openbare sleutel die overeenkomt met de persoonlijke sleutel van het onderwerp
* Informatie over de verlenende CA
* De ondersteunde versleutelings- en/of digitale ondertekeningsalgoritmen
* Informatie om de intrekking en geldigheidsstatus van het certificaat te bepalen

## <a name="certificate-fields"></a>Certificaatvelden

In de afgelopen tijd zijn er drie certificaatversies. Elke versie voegt velden toe aan de eerdere versie. Versie 3 is actueel en bevat naast de velden versie 3 ook velden voor versie 1 en versie 2. Versie 1 definieerde de volgende velden:

* **Versie:** een waarde (1, 2 of 3) die het versienummer van het certificaat identificeert
* **Serienummer:** een uniek nummer voor elk certificaat dat is uitgegeven door een CA
* **CA-handtekeningalgoritme:** naam van het algoritme dat de CA gebruikt om de inhoud van het certificaat te ondertekenen
* **Naam van vergever:** de DN (Distinguished Name) van de verlenende CA van het certificaat
* **Geldigheidsperiode:** de periode waarvoor het certificaat als geldig wordt beschouwd
* **Onderwerpnaam:** naam van de entiteit die wordt vertegenwoordigd door het certificaat
* **Informatie over openbare sleutel onderwerp:** openbare sleutel die eigendom is van het certificaatonderwerp

Versie 2 heeft de volgende velden toegevoegd met informatie over de certificaatverkender. Deze velden worden echter zelden gebruikt.

* **Unieke id van** verificator: een unieke id voor de verlenende CA zoals gedefinieerd door de CA
* **Unieke onderwerp-id:** een unieke id voor het certificaatonderwerp zoals gedefinieerd door de verlenende CA

Met versie 3-certificaten zijn de volgende extensies toegevoegd:

* **Authority Key Identifier:** dit kan een van twee waarden zijn:
  * Het onderwerp van de CA en het serienummer van het CA-certificaat dat dit certificaat heeft uitgegeven
  * Een hash van de openbare sleutel van de CA die dit certificaat heeft uitgegeven
* **Onderwerpsleutel-id:** hash van de openbare sleutel van het huidige certificaat
* **Sleutelgebruik** Hiermee definieert u de service waarvoor een certificaat kan worden gebruikt. Dit kunnen een of meer van de volgende waarden zijn:
  * **Digitale handtekening**
  * **Geen weerlegbaarheid**
  * **Sleutelcodering**
  * **Gegevenscodeering**
  * **Sleutelovereenkomst**
  * **Sleutel-certificaat ondertekenen**
  * **CRL-teken**
  * **Alleen codering**
  * **Alleen ontcijferen**
* **Gebruiksperiode van persoonlijke sleutel:** geldigheidsperiode voor het gedeelte van de persoonlijke sleutel van een sleutelpaar
* **Certificaatbeleid:** beleid dat wordt gebruikt om het certificaatonderwerp te valideren
* **Beleidstoewijzingen:** een beleid in de ene organisatie wordt toegewezen aan beleid in een andere organisatie
* **Alternatieve onderwerpnaam:** lijst met alternatieve namen voor het onderwerp
* **Alternatieve naam van vergever:** lijst met alternatieve namen voor de verlenende CA
* **Kenmerk onderwerp dir:** kenmerken van een X.500- of LDAP-directory
* **Basisbeperkingen:** hiermee kan het certificaat aanwijzen of het is uitgegeven aan een CA of aan een gebruiker, computer, apparaat of service. Deze extensie bevat ook een padlengtebeperking die het aantal onderliggende CERTIFICERINGen beperkt dat kan bestaan.
* **Naambeperkingen:** geeft aan welke naamruimten zijn toegestaan in een door de CA uitgegeven certificaat
* **Beleidsbeperkingen:** kan worden gebruikt om beleidstoewijzingen tussen CAs te verbieden
* **Uitgebreide-sleutelgebruik:** geeft aan hoe de openbare sleutel van een certificaat kan worden gebruikt buiten de doeleinden die zijn geïdentificeerd in de uitbreiding **voor sleutelgebruik**
* **CRL-distributiepunten:** bevat een of meer URL's waar de crl (base certificate revocation list) is gepubliceerd
* **AnyPolicy belemmeren:** voorkomt het gebruik van de **OID** voor alle uitgiftebeleidsregels (2.5.29.32.0) in onderliggende CA-certificaten
* **Meest nieuwe CRL:** bevat een of meer URL's waar de delta-CRL van de verlenende CA wordt gepubliceerd
* **Toegang tot ca-gegevens:** bevat een of meer URL's waar het verlenende CA-certificaat wordt gepubliceerd
* **Toegang tot onderwerpinformatie:** bevat informatie over het ophalen van aanvullende gegevens voor een certificaatonderwerp

## <a name="certificate-formats"></a>Certificaatindelingen

Certificaten kunnen in verschillende indelingen worden opgeslagen. Azure IoT Hub verificatie maakt doorgaans gebruik van de PEM- en PFX-indelingen.

### <a name="binary-certificate"></a>Binair certificaat

Dit bevat een binair certificaat met onbewerkte vorm met DER ASN.1-codering.

### <a name="ascii-pem-format"></a>ASCII PEM-indeling

Een PEM-certificaat (.pem-extensie) bevat een base64-gecodeerd certificaat dat begint met -----BEGIN CERTIFICATE----- en eindigt met -----END CERTIFICATE-----. De PEM-indeling is zeer gebruikelijk en is vereist voor IoT Hub bij het uploaden van bepaalde certificaten.

### <a name="ascii-pem-key"></a>ASCII-sleutel (PEM)

Bevat een met base64 gecodeerde DER-sleutel met mogelijk aanvullende metagegevens over het algoritme dat wordt gebruikt voor wachtwoordbeveiliging.

### <a name="pkcs7-certificate"></a>PKCS #7 certificaat

Een indeling die is ontworpen voor het transport van ondertekende of versleutelde gegevens. Deze wordt gedefinieerd door [RFC 2315.](https://tools.ietf.org/html/rfc2315) Deze kan de volledige certificaatketen bevatten.

### <a name="pkcs8-key"></a>PKCS#8-sleutel

De indeling voor een persoonlijke sleutelopslag die is gedefinieerd door [RFC 5208.](https://tools.ietf.org/html/rfc5208)

### <a name="pkcs12-key-and-certificate"></a>PKCS #12 sleutel en certificaat

Een complexe indeling die een sleutel en de hele certificaatketen kan opslaan en beveiligen. Deze wordt vaak gebruikt met een PFX-extensie. PKCS #12 is synoniem met de PFX-indeling.

## <a name="for-more-information"></a>Voor meer informatie

Zie de volgende onderwerpen voor meer informatie:

* [De handleiding van de layman voor X.509-certificaat-jargon](https://techcommunity.microsoft.com/t5/internet-of-things/the-layman-s-guide-to-x-509-certificate-jargon/ba-p/2203540)
* [Conceptueel begrip van X.509 CA-certificaten in de IoT-branche](https://docs.microsoft.com/azure/iot-hub/iot-hub-x509ca-concept)

## <a name="next-steps"></a>Volgende stappen

Als u testcertificaten wilt genereren die u kunt gebruiken om apparaten te verifiëren bij uw IoT Hub, bekijkt u de volgende onderwerpen:

* [Testcertificaten Microsoft-Supplied scripts maken](tutorial-x509-scripts.md)
* [OpenSSL gebruiken om testcertificaten te maken](tutorial-x509-openssl.md)
* [OpenSSL gebruiken om certificaten Self-Signed maken](tutorial-x509-self-sign.md)

Als u een ca-certificaat (certificeringsinstantie) of een onderliggend CA-certificaat hebt en u dit wilt uploaden naar uw IoT-hub en wilt bewijzen dat u de eigenaar bent, gaat u naar Het bezit van een [CA-certificaat](tutorial-x509-prove-possession.md)bewijzen.
