---
title: 'Zelfstudie: Microsoft-scripts gebruiken om x.509-testcertificaten te maken voor Azure IoT Hub | Microsoft Docs'
description: 'Zelfstudie: aangepaste scripts gebruiken om CA- en apparaatcertificaten voor Azure IoT Hub'
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
ms.openlocfilehash: ff4b63f49a87dd9ca6b0ef458bdcf1c285a34a18
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107378207"
---
# <a name="tutorial-using-microsoft-supplied-scripts-to-create-test-certificates"></a>Zelfstudie: Door Microsoft geleverde scripts gebruiken om testcertificaten te maken

Microsoft biedt PowerShell- en Bash-scripts om u te helpen begrijpen hoe u uw eigen X.509-certificaten maakt en deze verifieert bij een IoT Hub. De scripts bevinden zich in een [GitHub-opslagplaats.](https://github.com/Azure/azure-iot-sdk-c/tree/master/tools/CACertificates) Ze worden alleen ter demonstratie aangeboden. Certificaten die door hen zijn gemaakt, mogen niet worden gebruikt voor productie. De certificaten bevatten in code gecodeerde wachtwoorden ('1234') en verlopen na 30 dagen. Voor een productieomgeving moet u uw eigen best practices gebruiken voor het maken van certificaten en levensduurbeheer.

## <a name="powershell-scripts"></a>PowerShell-scripts

### <a name="step-1---setup"></a>Stap 1: instellen

OpenSSL voor Windows. Zie <https://www.openssl.org/docs/faq.html#MISC4> voor locaties om het te downloaden of te bouwen op basis van de <https://www.openssl.org/source/> bron. Voer vervolgens de voorlopige scripts uit:

1. Kopieer de scripts uit [](https://github.com/Azure/azure-iot-sdk-c/tree/master/tools/CACertificates) deze GitHub-opslagplaats naar de lokale map waarin u wilt werken. Alle bestanden worden gemaakt als kinderen van deze map.

1. Start PowerShell als beheerder.

1. Ga naar de map waarin u de scripts hebt geladen.

1. Stel op de opdrachtregel de omgevingsvariabele in op de map waarin het `$ENV:OPENSSL_CONF` openssl-configuratiebestand (openssl.cnf) zich bevindt.

1. Voer `Set-ExecutionPolicy -ExecutionPolicy Unrestricted` uit zodat PowerShell de scripts kan uitvoeren.

1. Voer `. .\ca-certs.ps1` uit. Hiermee worden de functies van het script in de globale PowerShell-naamruimte gebruikt.

1. Voer `Test-CACertsPrerequisites` uit. PowerShell maakt gebruik van het Windows-certificaatopslag voor het beheren van certificaten. Met deze opdracht wordt gecontroleerd of er later geen naamsbotsingen meer zijn met bestaande certificaten en of OpenSSL juist is ingesteld.

### <a name="step-2---create-certificates"></a>Stap 2: certificaten maken

Voer `New-CACertsCertChain [ecc|rsa]` uit. ECC wordt aanbevolen voor CA-certificaten, maar niet vereist. Met dit script worden uw directory en Windows-certificaatopslag bijgewerkt met de volgende CA- en tussencertificaten:

* intermediate1.pem
* intermediate2.pem
* intermediate3.pem
* RootCA.cer
* RootCA.pem

Nadat het script is uitgevoerd, voegt u het nieuwe CA-certificaat (RootCA.pem) toe aan uw IoT Hub:

1. Ga naar uw IoT Hub en navigeer naar Certificaten.

1. Selecteer **Toevoegen**.

1. Voer een weergavenaam in voor het CA-certificaat.

1. Upload het CA-certificaat.

1. Selecteer **Opslaan**.

### <a name="step-3---prove-possession"></a>Stap 3: bezit bewijzen

Nu u uw CA-certificaat hebt geüpload naar uw IoT Hub, moet u bewijzen dat u de eigenaar ervan bent:

1. Selecteer het nieuwe CA-certificaat.

1. Selecteer **Verificatiecode genereren** in het **dialoogvenster Certificaatdetails.** Zie Voor meer informatie Bewijs [bezit van een CA-certificaat.](tutorial-x509-prove-possession.md)

1. Maak een certificaat dat de verificatiecode bevat. Als de verificatiecode bijvoorbeeld is, voer dan het volgende uit om een nieuw certificaat te maken in uw adreslijst `"106A5SD242AF512B3498BD6098C4941E66R34H268DDB3288"` met het onderwerp `CN = 106A5SD242AF512B3498BD6098C4941E66R34H268DDB3288` . Het script maakt een certificaat met de naam `VerifyCert4.cer` .

    `New-CACertsVerificationCert "106A5SD242AF512B3498BD609C4941E66R34H268DDB3288"`

1. Upload `VerifyCert4.cer` naar uw IoT Hub in het dialoogvenster **Certificaatdetails.**

1. Selecteer **Verifiëren**.

### <a name="step-4---create-a-new-device"></a>Stap 4: een nieuw apparaat maken

Maak een apparaat voor uw IoT Hub:

1. Ga in IoT Hub naar de **sectie IoT-apparaten.**

1. Voeg een nieuw apparaat toe met id `mydevice` .

1. Kies voor verificatie **X.509 CA ondertekend.**

1. Voer `New-CACertsDevice mydevice` uit om een nieuw apparaatcertificaat te maken. Hiermee maakt u de volgende bestanden in uw werkmap:

   * `mydevice.pfx`
   * `mydevice-all.pem`
   * `mydevice-private.pem`
   * `mydevice-public.pem`

### <a name="step-5---test-your-device-certificate"></a>Stap 5: uw apparaatcertificaat testen

Ga naar [Test Certificate Authentication om](tutorial-x509-test-certificate.md) te bepalen of uw apparaatcertificaat kan worden geverifieerd bij uw IoT Hub. U hebt de PFX-versie van uw certificaat nodig, `mydevice.pfx` .

### <a name="step-6---cleanup"></a>Stap 6: opschonen

Open in het menu Start **Computercertificaten beheren en navigeer** naar  **Certificaten - Lokale computer > persoonlijke**. Verwijder certificaten die zijn uitgegeven door 'Azure IoT CA TestOnly*'. Verwijder op dezelfde manier de juiste certificaten uit>vertrouwde basiscertificeringsinstantie >-certificaten en >tussenliggende **certificeringsinstanties**> certificaten.

## <a name="bash-scripts"></a>Bash-scripts

### <a name="step-1---setup"></a>Stap 1: Instellen

1. Start Bash.

1. Ga naar de map waarin u wilt werken. Alle bestanden worden in deze map gemaakt.

1. Kopieer `*.cnf` en naar uw `*.sh` werkmap.

### <a name="step-2---create-certificates"></a>Stap 2: certificaten maken

1. Voer `./certGen.sh create_root_and_intermediate` uit. Hiermee maakt u de volgende bestanden in de **map certificaten:**

    * azure-iot-test-only.chain.ca.cert.pem
    * azure-iot-test-only.intermediate.cert.pem
    * azure-iot-test-only.root.ca.cert.pem

1. Ga naar uw IoT Hub en navigeer naar **Certificaten.**

1. Selecteer **Toevoegen**.

1. Voer een weergavenaam in voor het CA-certificaat.

1. Upload alleen het CA-certificaat naar uw IoT Hub. De naam van het certificaat is `./certs/azure-iot-test-only.root.ca.cert.pem.`

1. Selecteer **Opslaan**.

### <a name="step-3---prove-possession"></a>Stap 3: bezit bewijzen

1. Selecteer het nieuwe CA-certificaat dat u in de vorige stap hebt gemaakt.

1. Selecteer **Verificatiecode genereren** in het **dialoogvenster Certificaatdetails.** Zie Voor meer informatie Bewijs [bezit van een CA-certificaat.](tutorial-x509-prove-possession.md)

1. Maak een certificaat dat de verificatiecode bevat. Als de verificatiecode bijvoorbeeld is, voer dan het volgende uit om een nieuw certificaat te maken in uw werkmap met de naam `"106A5SD242AF512B3498BD6098C4941E66R34H268DDB3288"` `verification-code.cert.pem` die het onderwerp `CN = 106A5SD242AF512B3498BD6098C4941E66R34H268DDB3288` bevat.

    `./certGen.sh create_verification_certificate "106A5SD242AF512B3498BD6098C4941E66R34H268DDB3288"`

1. Upload het certificaat naar uw IoT-hub in het **dialoogvenster Certificaatdetails.**

1. Selecteer **Verifiëren**.

### <a name="step-4---create-a-new-device"></a>Stap 4: een nieuw apparaat maken

Maak een apparaat voor uw IoT-hub:

1. Ga in IoT Hub naar de sectie IoT-apparaten.

1. Voeg een nieuw apparaat toe met id `mydevice` .

1. Kies voor verificatie **X.509 CA ondertekend.**

1. Voer `./certGen.sh create_device_certificate mydevice` uit om een nieuw apparaatcertificaat te maken. Hiermee maakt u twee bestanden met `new-device.cert.pem` de naam en bestanden in uw `new-device.cert.pfx` werkmap.

### <a name="step-5---test-your-device-certificate"></a>Stap 5: uw apparaatcertificaat testen

Ga naar [Test Certificate Authentication om](tutorial-x509-test-certificate.md) te bepalen of uw apparaatcertificaat kan worden geverifieerd bij uw IoT Hub. U hebt de PFX-versie van uw certificaat nodig, `new-device.cert.pfx` .

### <a name="step-6---cleanup"></a>Stap 6: opschonen

Omdat met het bash-script gewoon certificaten in uw werkmap worden gemaakt, verwijdert u ze gewoon wanneer u klaar bent met testen.

## <a name="next-steps"></a>Volgende stappen

Als u uw certificaat wilt testen, gaat u naar [Certificaatverificatie](tutorial-x509-test-certificate.md) testen om te bepalen of uw apparaat kan worden geverifieerd bij uw IoT Hub.
