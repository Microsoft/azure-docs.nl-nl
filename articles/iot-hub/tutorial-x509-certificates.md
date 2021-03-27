---
title: Zelf studie-meer informatie over X. 509 open bare sleutel certificaten voor Azure IoT Hub | Microsoft Docs
description: Zelf studie-meer informatie over X. 509 open bare sleutel certificaten voor Azure IoT Hub
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
ms.openlocfilehash: cdc5b261abe91c31d31827aeab03c9e8838b2a91
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2021
ms.locfileid: "105630701"
---
# <a name="tutorial-understanding-x509-public-key-certificates"></a>Zelf studie: informatie over de open bare-sleutel certificaten van X. 509

X. 509-certificaten zijn digitale documenten die een gebruiker, computer, service of apparaat vertegenwoordigen. Ze worden uitgegeven door een certificerings instantie (CA), een onderliggende CERTIFICERINGs instantie of een registratie-instantie en bevatten de open bare sleutel van het certificaat onderwerp. Ze bevatten geen persoonlijke sleutel van het onderwerp die veilig moet worden opgeslagen. Certificaten met een open bare sleutel worden gedocumenteerd door [RFC 5280](https://tools.ietf.org/html/rfc5280). Ze zijn digitaal ondertekend en bevatten in het algemeen de volgende informatie:

* Informatie over het certificaat onderwerp
* De open bare sleutel die overeenkomt met de persoonlijke sleutel van het onderwerp
* Informatie over de verlenende CA
* De ondersteunde versleuteling en/of digitale ondertekening algoritmen
* Informatie voor het bepalen van de intrekking en geldigheids status van het certificaat

## <a name="certificate-fields"></a>Certificaat velden

Er zijn in de loop van de tijd drie certificaat versies. Elke versie voegt velden toe aan de eerste. Versie 3 is actueel en bevat naast de velden van versie 3 ook velden van versie 1 en versie 2. Versie 1 heeft de volgende velden gedefinieerd:

* **Versie**: een waarde (1, 2 of 3) die het versie nummer van het certificaat aangeeft
* **Serie nummer**: een uniek nummer voor elk certificaat dat is uitgegeven door een certificerings instantie
* **CA-handtekening algoritme**: naam van het algoritme dat door de CA wordt gebruikt voor het ondertekenen van de certificaat inhoud
* **Naam** van de verlener: de DN-naam van de verlenende CA van het certificaat
* **Geldigheids periode**: de periode waarvoor het certificaat als geldig wordt beschouwd
* **Onderwerpnaam**: naam van de entiteit die wordt vertegenwoordigd door het certificaat
* **Info over open bare sleutel van onderwerp**: open bare sleutel die eigendom is van het certificaat onderwerp

Versie 2 heeft de volgende velden toegevoegd met informatie over de uitgever van het certificaat. Deze velden worden echter zelden gebruikt.

* **Unieke id** van de verlener: een unieke id voor de verlenende CA, zoals gedefinieerd door de CA
* **Unieke id van onderwerp**: een unieke id voor het certificaat onderwerp zoals gedefinieerd door de VERLENENde ca

Certificaten van versie 3 hebben de volgende extensies toegevoegd:

* **Authority Key Identifier**: dit kan een van de volgende twee waarden zijn:
  * Het onderwerp van de CA en het serie nummer van het CA-certificaat dat dit certificaat heeft uitgegeven
  * Een hash van de open bare sleutel van de certificerings instantie die dit certificaat heeft uitgegeven
* **Id van de onderwerps sleutel**: hash van de open bare sleutel van het huidige certificaat
* **Sleutel gebruik** Hiermee definieert u de service waarvoor een certificaat kan worden gebruikt. Dit kan een of meer van de volgende waarden zijn:
  * **Digitale handtekening**
  * **Geen weerlegbaarheid**
  * **Sleutelcodering**
  * **Gegevens codering**
  * **Sleutel overeenkomst**
  * **Sleutel certificaat ondertekenen**
  * **CRL-teken**
  * **Alleen ontsleutelen**
  * **Alleen ontcijferen**
* **Gebruiks periode van privé sleutel**: geldigheids periode voor het gedeelte met de persoonlijke sleutel van een sleutel paar
* **Certificaat beleid**: beleid dat wordt gebruikt om het certificaat onderwerp te valideren
* **Beleids koppelingen**: Hiermee wordt een beleid in de ene organisatie toegewezen aan een ander beleid
* **Alternatieve naam voor onderwerp**: lijst met alternatieve namen voor het onderwerp
* **Alternatieve naam voor verlener**: lijst met alternatieve namen voor de VERLENENde ca
* **Onderwerp DIR kenmerk**: kenmerken van een X. 500-of LDAP-adres lijst
* **Basis beperkingen**: Hiermee staat u toe dat het certificaat wordt toegewezen aan een certificerings instantie of aan een gebruiker, computer, apparaat of service. Deze extensie bevat ook een beperking voor padlengte die het aantal onderliggende Ca's beperkt dat kan bestaan.
* **Naam beperkingen**: Hiermee wordt aangegeven welke naam ruimten zijn toegestaan in een certificaat dat is uitgegeven door een certificerings instantie
* **Beleids beperkingen**: kan worden gebruikt om beleids koppelingen tussen ca's te verbieden
* **Uitgebreide-sleutel gebruik**: Hiermee wordt aangegeven hoe de open bare sleutel van een certificaat kan worden gebruikt buiten de doel stellingen die worden vermeld in de uitbrei ding voor **sleutel gebruik** .
* **CRL-distributie punten**: bevat een of meer url's waarin de basis lijst met ingetrokken certificaten (CRL) is gepubliceerd
* **AnyPolicy uitschakelen**: het gebruik van de OID van **alle beleids regels voor uitgifte** (2.5.29.32.0) in onderliggende CA-certificaten wordt belemmerd
* Meest **recente CRL**: bevat een of meer url's waarin de Delta-CRL van de uitgevende CA is gepubliceerd
* **Toegang tot CA-gegevens**: bevat een of meer url's waarin het certificaat van de uitgevende CA is gepubliceerd
* **Toegang tot Onderwerpgegevens**: bevat informatie over het ophalen van aanvullende Details voor een certificaat onderwerp

## <a name="certificate-formats"></a>Certificaat indelingen

Certificaten kunnen worden opgeslagen in verschillende indelingen. Azure IoT Hub-verificatie maakt gewoonlijk gebruik van de PEM-en PFX-indeling.

### <a name="binary-certificate"></a>Binair certificaat

Dit bevat een binair certificaat voor onbewerkte tekst met DER ASN. 1-code ring.

### <a name="ascii-pem-format"></a>ASCII-PEM-indeling

Een PEM-certificaat (. pem-extensie) bevat een met base64 gecodeerd certificaat dat begint met-----BEGIN van CERTIFICATE-----en eindigt met------eind certificaat-----. De PEM-indeling is zeer gebruikelijk en is vereist voor IoT Hub bij het uploaden van bepaalde certificaten.

### <a name="ascii-pem-key"></a>ASCII-sleutel (PEM)

Bevat een met base64 gecodeerde DER-sleutel met mogelijk aanvullende meta gegevens over de algoritme die wordt gebruikt voor wachtwoord beveiliging.

### <a name="pkcs7-certificate"></a>PKCS # 7-certificaat

Een indeling die is ontworpen voor het transporteren van ondertekende of versleutelde gegevens. Het wordt gedefinieerd door [RFC 2315](https://tools.ietf.org/html/rfc2315). Het kan de volledige certificaat keten bevatten.

### <a name="pkcs8-key"></a>PKCS # 8-sleutel

De indeling voor een persoonlijk sleutel archief dat is gedefinieerd door [RFC 5208](https://tools.ietf.org/html/rfc5208).

### <a name="pkcs12-key-and-certificate"></a>PKCS # 12-sleutel en-certificaat

Een complexe indeling die een sleutel en de volledige certificaat keten kan opslaan en beveiligen. Dit wordt vaak gebruikt met de extensie. pfx. PKCS # 12 is synoniem met de PFX-indeling.

## <a name="next-steps"></a>Volgende stappen

Zie de volgende onderwerpen als u test certificaten wilt genereren die u kunt gebruiken om apparaten te verifiëren op uw IoT Hub:

* [Microsoft-Supplied-scripts gebruiken om test certificaten te maken](tutorial-x509-scripts.md)
* [OpenSSL gebruiken om test certificaten te maken](tutorial-x509-openssl.md)
* [Self-Signed test certificaten maken met behulp van OpenSSL](tutorial-x509-self-sign.md)

Als u een certificaat van een certificerings instantie of een onderliggend CA-certificaat hebt en u het wilt uploaden naar uw IoT-hub en wilt bewijzen dat u de eigenaar bent, raadpleegt u het [bewijs van een CA-certificaat](tutorial-x509-prove-possession.md).
