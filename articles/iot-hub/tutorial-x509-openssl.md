---
title: 'Zelfstudie: OpenSSL gebruiken om X.509-testcertificaten te maken voor Azure IoT Hub| Microsoft Docs'
description: 'Zelfstudie: OpenSSL gebruiken om CA- en apparaatcertificaten te maken voor Azure IoT Hub'
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
ms.openlocfilehash: 0843e5d3a5e91cb4acdf18ad6bdf6f4f0c214f72
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107378292"
---
# <a name="tutorial-using-openssl-to-create-test-certificates"></a>Zelfstudie: OpenSSL gebruiken om testcertificaten te maken

Hoewel u X.509-certificaten kunt aanschaffen bij een vertrouwde certificeringsinstantie, is het maken van uw eigen testcertificaathiërarchie of het gebruik van zelf-ondertekende certificaten voldoende om de verificatie van IoT Hub-apparaten te testen. In het volgende voorbeeld worden [OpenSSL en](https://www.openssl.org/) [het OpenSSL Cookbook](https://www.feistyduck.com/library/openssl-cookbook/online/ch-openssl.html) gebruikt om een certificeringsinstantie (CA), een onderliggende CA en een apparaatcertificaat te maken. In het voorbeeld worden vervolgens de onderliggende CA en het apparaatcertificaat in een certificaathiërarchie geplaatst. Dit wordt alleen voor voorbeelddoeleinden weergegeven.

## <a name="step-1---create-the-root-ca-directory-structure"></a>Stap 1: de hoofdmapstructuur van de CA maken

Maak een mapstructuur voor de certificeringsinstantie.

* De **map certificaten slaat** nieuwe certificaten op.
* De **map db** wordt gebruikt voor de certificaatdatabase.
* In **de privémap** wordt de persoonlijke sleutel van de CA op opslag.

```bash
  mkdir rootca
  cd rootca
  mkdir certs db private
  touch db/index
  openssl rand -hex 16 > db/serial
  echo 1001 > db/crlnumber
```

## <a name="step-2---create-a-root-ca-configuration-file"></a>Stap 2: een basis-CA-configuratiebestand maken

Voordat u een CA maakt, maakt u een configuratiebestand en sla u dit op `rootca.conf` als in de rootca-map.

```xml
[default]
name                     = rootca
domain_suffix            = example.com
aia_url                  = http://$name.$domain_suffix/$name.crt
crl_url                  = http://$name.$domain_suffix/$name.crl
default_ca               = ca_default
name_opt                 = utf8,esc_ctrl,multiline,lname,align

[ca_dn]
commonName               = "Test Root CA"

[ca_default]
home                     = ../rootca 
database                 = $home/db/index
serial                   = $home/db/serial
crlnumber                = $home/db/crlnumber
certificate              = $home/$name.crt
private_key              = $home/private/$name.key
RANDFILE                 = $home/private/random
new_certs_dir            = $home/certs
unique_subject           = no
copy_extensions          = none
default_days             = 3650
default_crl_days         = 365
default_md               = sha256
policy                   = policy_c_o_match

[policy_c_o_match]
countryName              = optional
stateOrProvinceName      = optional
organizationName         = optional
organizationalUnitName   = optional
commonName               = supplied
emailAddress             = optional

[req]
default_bits             = 2048
encrypt_key              = yes
default_md               = sha256
utf8                     = yes
string_mask              = utf8only
prompt                   = no
distinguished_name       = ca_dn
req_extensions           = ca_ext

[ca_ext]
basicConstraints         = critical,CA:true
keyUsage                 = critical,keyCertSign,cRLSign
subjectKeyIdentifier     = hash

[sub_ca_ext]
authorityKeyIdentifier   = keyid:always
basicConstraints         = critical,CA:true,pathlen:0
extendedKeyUsage         = clientAuth,serverAuth
keyUsage                 = critical,keyCertSign,cRLSign
subjectKeyIdentifier     = hash

[client_ext]
authorityKeyIdentifier   = keyid:always
basicConstraints         = critical,CA:false
extendedKeyUsage         = clientAuth
keyUsage                 = critical,digitalSignature
subjectKeyIdentifier     = hash

```

## <a name="step-3---create-a-root-ca"></a>Stap 3: een basis-CA maken

Genereer eerst de sleutel en de aanvraag voor certificaat-ondertekening (CSR) in de rootca-map.

```bash
  openssl req -new -config rootca.conf -out rootca.csr -keyout private/rootca.key
```

Maak vervolgens een zelf-ondertekend CA-certificaat. Zelf-ondertekening is geschikt voor testdoeleinden. Geef de ca_ext configuratiebestandsextensies op de opdrachtregel op. Deze geven aan dat het certificaat voor een basis-CA is en kan worden gebruikt voor het ondertekenen van certificaten en certificaat intrekken van lijsten (CRL's). Onderteken het certificaat en geef het door aan de database.

```bash
  openssl ca -selfsign -config rootca.conf -in rootca.csr -out rootca.crt -extensions ca_ext
```

## <a name="step-4---create-the-subordinate-ca-directory-structure"></a>Stap 4: de onderliggende CA-mapstructuur maken

Maak een mapstructuur voor de onderliggende CA.

```bash
  mkdir subca
  cd subca
  mkdir certs db private
  touch db/index
  openssl rand -hex 16 > db/serial
  echo 1001 > db/crlnumber
```

## <a name="step-5---create-a-subordinate-ca-configuration-file"></a>Stap 5: een onderliggend CA-configuratiebestand maken

Maak een configuratiebestand en sla het op als subca.conf in de `subca` map .

```bash
[default]
name                     = subca
domain_suffix            = example.com
aia_url                  = http://$name.$domain_suffix/$name.crt
crl_url                  = http://$name.$domain_suffix/$name.crl
default_ca               = ca_default
name_opt                 = utf8,esc_ctrl,multiline,lname,align

[ca_dn]
commonName               = "Test Subordinate CA"

[ca_default]
home                     = . 
database                 = $home/db/index
serial                   = $home/db/serial
crlnumber                = $home/db/crlnumber
certificate              = $home/$name.crt
private_key              = $home/private/$name.key
RANDFILE                 = $home/private/random
new_certs_dir            = $home/certs
unique_subject           = no
copy_extensions          = copy 
default_days           
default_crl_days         = 90 
default_md               = sha256
policy                   = policy_c_o_match

[policy_c_o_match]
countryName              = optional
stateOrProvinceName      = optional
organizationName         = optional
organizationalUnitName   = optional
commonName               = supplied
emailAddress             = optional

[req]
default_bits             = 2048
encrypt_key              = yes
default_md               = sha256
utf8                     = yes
string_mask              = utf8only
prompt                   = no
distinguished_name       = ca_dn
req_extensions           = ca_ext

[ca_ext]
basicConstraints         = critical,CA:true
keyUsage                 = critical,keyCertSign,cRLSign
subjectKeyIdentifier     = hash

[sub_ca_ext]
authorityKeyIdentifier   = keyid:always
basicConstraints         = critical,CA:true,pathlen:0
extendedKeyUsage         = clientAuth,serverAuth
keyUsage                 = critical,keyCertSign,cRLSign
subjectKeyIdentifier     = hash

[client_ext]
authorityKeyIdentifier   = keyid:always
basicConstraints         = critical,CA:false
extendedKeyUsage         = clientAuth
keyUsage                 = critical,digitalSignature
subjectKeyIdentifier     = hash
```

## <a name="step-6---create-a-subordinate-ca"></a>Stap 6: een onderliggende CA maken

Maak een nieuw serienummer in het bestand `rootca/db/serial` voor het onderliggende CA-certificaat.

```bash
  openssl rand -hex 16 > db/serial
```

>[!IMPORTANT]
>U moet een nieuw serienummer maken voor elk onderliggend CA-certificaat en elk apparaatcertificaat dat u maakt. Verschillende certificaten kunnen niet hetzelfde serienummer hebben.

In dit voorbeeld ziet u hoe u een onderliggende ca of registratie-CA maakt. Omdat u de basis-CA kunt gebruiken om certificaten te ondertekenen, is het maken van een onderliggende CA niet strikt noodzakelijk. Een onderliggende CA imiteert echter de werkelijke certificaathiërarchieën waarin de basis-CA offline wordt gehouden en onderliggende CA's clientcertificaten uitgeven.

Gebruik het configuratiebestand om een sleutel en een aanvraag voor certificaat-ondertekening (CSR) te genereren.

```bash
  openssl req -new -config subca.conf -out subca.csr -keyout private/subca.key
```

Dien de CSR in bij de basis-CA en gebruik de basis-CA om het onderliggende CA-certificaat uit te geven en te ondertekenen. Geef sub_ca_ext op voor de uitbreidingsschakelaar op de opdrachtregel. De extensies geven aan dat het certificaat is voor een CA die certificaten en certificaat intrekken lijsten (CRL's) kan ondertekenen. Wanneer u hier om wordt gevraagd, ondertekent u het certificaat en legt u het door aan de database.

```bash
  openssl ca -config ../rootca/rootca.conf -in subca.csr -out subca.crt -extensions sub_ca_ext
```

## <a name="step-7---demonstrate-proof-of-possession"></a>Stap 7: bewijs van eigendom demonstreren

U hebt nu zowel een basis-CA-certificaat als een onderliggend CA-certificaat. U kunt beide gebruiken om apparaatcertificaten te ondertekenen. Het bestand dat u kiest, moet worden geüpload naar uw IoT Hub. In de volgende stappen wordt ervan uitgenomen dat u het onderliggende CA-certificaat gebruikt. Uw onderliggende CA-certificaat uploaden en registreren bij uw IoT Hub:

1. Ga in Azure Portal naar uw IoTHub en selecteer **Instellingen > certificaten.**

1. Selecteer **Toevoegen om** uw nieuwe onderliggende CA-certificaat toe te voegen.

1. Voer een weergavenaam in het **veld Certificaatnaam** in en selecteer het PEM-certificaatbestand dat u eerder hebt gemaakt.

1. Selecteer **Opslaan**. Uw certificaat wordt weergegeven in de lijst met certificaten met de status **Niet-geverifieerd.** Tijdens het verificatieproces wordt bewezen dat u eigenaar bent van het certificaat.

   
1. Selecteer het certificaat om het dialoogvenster **Certificaatdetails weer te** geven.

1. Selecteer **Verificatiecode genereren.** Zie Voor meer informatie [Bewijs bezit van een CA-certificaat.](tutorial-x509-prove-possession.md)

1. Kopieer de verificatiecode naar het klembord. U moet de verificatiecode instellen als het certificaatonderwerp. Als de verificatiecode bijvoorbeeld BB0C656E69AF75E3FB3C8D922C1760C58C1DA5B05AAA9D0A is, voegt u dat toe als onderwerp van uw certificaat, zoals wordt weergegeven in stap 9.

1. Genereer een persoonlijke sleutel.

  ```bash
    $ openssl genpkey -out pop.key -algorithm RSA -pkeyopt rsa_keygen_bits:2048
  ```

9. Genereer een aanvraag voor certificaat-ondertekening (CSR) op basis van de persoonlijke sleutel. Voeg de verificatiecode toe als het onderwerp van uw certificaat.

  ```bash
  openssl req -new -key pop.key -out pop.csr
  
    -----
    Country Name (2 letter code) [XX]:.
    State or Province Name (full name) []:.
    Locality Name (eg, city) [Default City]:.
    Organization Name (eg, company) [Default Company Ltd]:.
    Organizational Unit Name (eg, section) []:.
    Common Name (eg, your name or your server hostname) []:BB0C656E69AF75E3FB3C8D922C1760C58C1DA5B05AAA9D0A
    Email Address []:

    Please enter the following 'extra' attributes
    to be sent with your certificate request
    A challenge password []:
    An optional company name []:
 
  ```

10. Maak een certificaat met behulp van het basis-CA-configuratiebestand en de CSR voor het bewijs van eigendom-certificaat.

  ```bash
    openssl ca -config rootca.conf -in pop.csr -out pop.crt -extensions client_ext

  ```

11. Selecteer het nieuwe certificaat in de weergave **Certificaatdetails.** Als u het PEM-bestand wilt zoeken, gaat u naar de map certs.

12. Nadat het certificaat is geüpload, selecteert u **Verifiëren.** De ca-certificaatstatus moet worden gewijzigd **in Geverifieerd.**

## <a name="step-8---create-a-device-in-your-iot-hub"></a>Stap 8: maak een apparaat in uw IoT Hub

Navigeer naar IoT Hub in de Azure Portal en maak een nieuwe IoT-apparaat-id met de volgende waarden:

1. Geef de **apparaat-id** op die overeenkomt met de onderwerpnaam van uw apparaatcertificaten.

1. Selecteer het **verificatietype X.509 CA ondertekend.**

1. Selecteer **Opslaan**.

## <a name="step-9---create-a-client-device-certificate"></a>Stap 9: een clientapparaatcertificaat maken

Als u een clientcertificaat wilt genereren, moet u eerst een persoonlijke sleutel genereren. De volgende opdracht laat zien hoe u OpenSSL gebruikt om een persoonlijke sleutel te maken. Maak de sleutel in de subcamap.

```bash
openssl genpkey -out device.key -algorithm RSA -pkeyopt rsa_keygen_bits:2048
```

Maak een aanvraag voor certificaat ondertekening (CSR) voor de sleutel. U hoeft geen wachtwoord voor een uitdaging of een optionele bedrijfsnaam in te voeren. U moet echter de apparaat-id invoeren in het veld Algemene naam.

```bash
openssl req -new -key device.key -out device.csr

-----
Country Name (2 letter code) [XX]:.
State or Province Name (full name) []:.
Locality Name (eg, city) [Default City]:.
Organization Name (eg, company) [Default Company Ltd]:.
Organizational Unit Name (eg, section) []:
Common Name (eg, your name or your server hostname) []:`<your device ID>`
Email Address []:

Please enter the following 'extra' attributes
to be sent with your certificate request
A challenge password []:
An optional company name []:

```

Controleer of de CSR is wat u verwacht.

```bash
openssl req -text -in device.csr -noout
```

Verzend de CSR naar de onderliggende CA om u aan te melden bij de certificaathiërarchie. Geef `client_ext` op in de `-extensions` switch. U ziet dat `Basic Constraints` de in het uitgegeven certificaat aangeeft dat dit certificaat niet voor een CA is. Als u meerdere certificaten ondertekent, moet u het serienummer bijwerken voordat u elk certificaat genereert met behulp van de `rand -hex 16 > db/serial` openssl-opdracht.

```bash
openssl ca -config subca.conf -in device.csr -out device.crt -extensions client_ext
```

## <a name="next-steps"></a>Volgende stappen

Ga naar [Test Certificate Authentication om](tutorial-x509-test-certificate.md) te bepalen of uw certificaat uw apparaat kan verifiëren bij uw IoT Hub.
