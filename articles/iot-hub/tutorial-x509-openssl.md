---
title: Zelf studie-OpenSSL gebruiken om X. 509-test certificaten te maken voor Azure IoT Hub | Microsoft Docs
description: Zelf studie-OpenSSL gebruiken voor het maken van CA-en apparaat-certificaten voor Azure IoT hub
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
ms.openlocfilehash: 0d083d856138d7895a6e03f4d290ef3c4ddebd05
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2021
ms.locfileid: "105630679"
---
# <a name="tutorial-using-openssl-to-create-test-certificates"></a>Zelf studie: OpenSSL gebruiken om test certificaten te maken

Hoewel u X. 509-certificaten kunt kopen bij een vertrouwde certificerings instantie, het maken van uw eigen test certificaat hiërarchie of het gebruik van zelfondertekende certificaten is voldoende voor het testen van de verificatie van IoT Hub-apparaten. In het volgende voor beeld worden [openssl](https://www.openssl.org/) en de [openssl-Cookbook](https://www.feistyduck.com/library/openssl-cookbook/online/ch-openssl.html) gebruikt voor het maken van een certificerings instantie (CA), een onderliggende certificerings instantie en een certificaat voor een apparaat. Het voor beeld ondertekent de onderliggende CA en het certificaat van het apparaat in een certificaat hiërarchie. Dit wordt alleen voor beeld doeleinden gepresenteerd.

## <a name="step-1---create-the-root-ca-directory-structure"></a>Stap 1: de mapstructuur van de basis-CA maken

Maak een mapstructuur voor de certificerings instantie.

* In **de certificaten** Directory worden nieuwe certificaten opgeslagen.
* De map **db** wordt gebruikt voor de certificaat database.
* De **persoonlijke map bevat de persoonlijke** sleutel van de ca.

```bash
  mkdir rootca
  cd rootca
  mkdir certs db private
  touch db/index
  openssl rand -hex 16 > db/serial
  echo 1001 > db/crlnumber
```

## <a name="step-2---create-a-root-ca-configuration-file"></a>Stap 2: een basis-CA-configuratie bestand maken

Voordat u een certificerings instantie maakt, maakt u een configuratie bestand en slaat u deze `rootca.conf` op als in de rootca-map.

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

```

## <a name="step-3---create-a-root-ca"></a>Stap 3: een basis-CA maken

Genereer eerst de sleutel en de aanvraag voor certificaat ondertekening (CSR) in de rootca-map.

```bash
  openssl req -new -config rootca.conf -out rootca.csr -keyout private/rootca.key
```

Maak vervolgens een zelfondertekend CA-certificaat. Zelf ondertekening is geschikt voor test doeleinden. Geef de extensies voor het ca_ext-configuratie bestand op de opdracht regel op. Deze geven aan dat het certificaat voor een basis-CA is en kan worden gebruikt voor het ondertekenen van certificaten en certificaatintrekkingslijsten (Crl's). Het certificaat ondertekenen en het door voeren in de-data base.

```bash
  openssl ca -selfsign -config rootca.conf -in rootca.csr -out rootca.crt -extensions ca_ext
```

## <a name="step-4---create-the-subordinate-ca-directory-structure"></a>Stap 4: de mapstructuur van de onderliggende CA maken

Maak een mapstructuur voor de onderliggende certificerings instantie.

```bash
  mkdir subca
  cd subca
  mkdir certs db private
  touch db/index
  openssl rand -hex 16 > db/serial
  echo 1001 > db/crlnumber
```

## <a name="step-5---create-a-subordinate-ca-configuration-file"></a>Stap 5: een onderliggend CA-configuratie bestand maken

Maak een configuratie bestand en sla het op als subca. conf in de `subca` Directory.

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

Maak een nieuw serie nummer in het `rootca/db/serial` bestand voor het onderliggende CA-certificaat.

```bash
  openssl rand -hex 16 > db/serial
```

>[!IMPORTANT]
>U moet een nieuw serie nummer maken voor elk certificaat van een onderliggende CA en elk apparaat dat u maakt. Verschillende certificaten kunnen niet hetzelfde serie nummer hebben.

In dit voor beeld ziet u hoe u een ondergeschikte of registratie-CA maakt. Omdat u de basis-CA kunt gebruiken om certificaten te ondertekenen, is het maken van een onderliggende CA niet strikt nood zakelijk. Als er een onderliggende CA wordt gebruikt, wordt er echter gesimuleerd dat de basis-CA wordt gebruikt voor de certificerings hiërarchie van het certificaat.

Gebruik het configuratie bestand om een sleutel en een aanvraag voor certificaat ondertekening (CSR) te genereren.

```bash
  openssl req -new -config subca.conf -out subca.csr -keyout private/subca.key
```

De CSR verzenden naar de basis-CA en de basis-CA gebruiken om het onderliggende CA-certificaat te verlenen en te ondertekenen. Geef sub_ca_ext op voor de switch extensies op de opdracht regel. De uitbrei dingen geven aan dat het certificaat voor een certificerings instantie is waarmee certificaten en certificaatintrekkingslijsten (Crl's) kunnen worden ondertekend. Onderteken het certificaat wanneer daarom wordt gevraagd en sla het op in de data base.

```bash
  openssl ca -config ../rootca/rootca.conf -in subca.csr -out subca.crt -extensions sub_ca_ext
```

## <a name="step-7---demonstrate-proof-of-possession"></a>Stap 7-bewijzen van bezit tonen

U hebt nu zowel een basis-CA-certificaat als een onderliggend CA-certificaat. U kunt een van beide gebruiken om apparaat-certificaten te ondertekenen. De naam die u kiest, moet naar uw IoT Hub worden geüpload. Bij de volgende stappen wordt ervan uitgegaan dat u het onderliggende CA-certificaat gebruikt. Uw onderliggende CA-certificaat uploaden en registreren bij uw IoT Hub:

1. Ga in het Azure Portal naar uw IoTHub en selecteer **instellingen > certificaten**.

1. Selecteer **toevoegen** om uw nieuwe onderliggende CA-certificaat toe te voegen.

1. Voer een weergave naam in het veld **certificaat naam** in en selecteer het PEM-certificaat bestand dat u eerder hebt gemaakt.

1. Selecteer **Opslaan**. Uw certificaat wordt weer gegeven in de lijst certificaten met de status niet **geverifieerd**. Het verificatie proces bewijst dat u eigenaar van het certificaat is.

   
1. Selecteer het certificaat om het dialoog venster **certificaat Details** weer te geven.

1. Selecteer **verificatie code genereren**. Zie [bewijzen over een CA-certificaat](tutorial-x509-prove-possession.md)voor meer informatie.

1. Kopieer de verificatiecode naar het klembord. U moet de verificatie code instellen als het certificaat onderwerp. Als de verificatie code bijvoorbeeld BB0C656E69AF75E3FB3C8D922C1760C58C1DA5B05AAA9D0A is, voegt u deze toe als het onderwerp van het certificaat, zoals in de volgende stap wordt weer gegeven.

1. Genereer een persoonlijke sleutel.

  ```bash
    $ openssl req -new -key pop.key -out pop.csr

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

9. Maak een certificaat met behulp van het configuratie bestand van de basis-CA en de CSR.

  ```bash
    openssl ca -config rootca.conf -in pop.csr -out pop.crt -extensions client_ext

  ```

10. Selecteer het nieuwe certificaat in de weer gave **certificaat Details**

11. Nadat het certificaat is geüpload, selecteert u **verifiëren**. De status van het CA-certificaat moet worden gewijzigd in **gecontroleerd**.

## <a name="step-8---create-a-device-in-your-iot-hub"></a>Stap 8: een apparaat maken in uw IoT Hub

Navigeer naar uw IoT Hub in de Azure Portal en maak een nieuwe IoT-apparaat-id met de volgende waarden:

1. Geef de **apparaat-id** op die overeenkomt met de onderwerpnaam van de certificaten van uw apparaat.

1. Selecteer het verificatie type **X. 509 certificerings instantie ondertekend** .

1. Selecteer **Opslaan**.

## <a name="step-9---create-a-client-device-certificate"></a>Stap 9: een certificaat voor client apparaten maken

Als u een client certificaat wilt genereren, moet u eerst een persoonlijke sleutel genereren. De volgende opdracht laat zien hoe u OpenSSL kunt gebruiken om een persoonlijke sleutel te maken. Maak de sleutel in de subca-map.

```bash
openssl genpkey -out device.key -algorithm RSA -pkeyopt rsa_keygen_bits:2048
```

Maak een aanvraag voor certificaat ondertekening (CSR) voor de sleutel. U hoeft geen vraag wachtwoord of een optionele bedrijfs naam op te geven. U moet echter de apparaat-ID in het veld algemene naam invoeren.

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

Verzend de CSR naar de onderliggende certificerings instantie om u aan te melden bij de certificaat hiërarchie. Geef `client_ext` in de `-extensions` Switch op. Merk op dat de `Basic Constraints` in het uitgegeven certificaat aangeeft dat dit certificaat niet voor een certificerings instantie is. Als u meerdere certificaten ondertekent, moet u ervoor zorgen dat u het serie nummer bijwerkt voordat u elk certificaat genereert met behulp van de openssl- `rand -hex 16 > db/serial` opdracht.

```bash
openssl ca -config subca.conf -in device.csr -out device.crt -extensions client_ext
```

## <a name="next-steps"></a>Volgende stappen

Ga naar [verificatie van certificaat testen](tutorial-x509-test-certificate.md) om te bepalen of uw apparaat kan worden geverifieerd op uw IOT hub.
