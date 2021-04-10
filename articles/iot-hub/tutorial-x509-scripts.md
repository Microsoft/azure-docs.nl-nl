---
title: 'Zelf studie: micro soft-scripts gebruiken voor het maken van x. 509-test certificaten voor Azure IoT Hub | Microsoft Docs'
description: 'Zelf studie: aangepaste scripts gebruiken voor het maken van CA-en apparaat certificaten voor Azure IoT Hub'
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
ms.openlocfilehash: fc3717436619468e2db0bf4b408059112dae24cc
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/05/2021
ms.locfileid: "106384150"
---
# <a name="tutorial-using-microsoft-supplied-scripts-to-create-test-certificates"></a>Zelf studie: door micro soft geleverde scripts gebruiken voor het maken van test certificaten

Micro soft biedt Power shell-en bash-scripts waarmee u leert hoe u uw eigen X. 509-certificaten maakt en deze verifieert bij een IoT Hub. De scripts bevinden zich in een GitHub- [opslag plaats](https://github.com/Azure/azure-iot-sdk-c/tree/master/tools/CACertificates). Ze zijn alleen bedoeld voor demonstratie doeleinden. De door hen gemaakte certificaten mogen niet worden gebruikt voor productie. De certificaten bevatten hardcoded wacht woorden ("1234") en verlopen na 30 dagen. Voor een productie omgeving moet u uw eigen aanbevolen procedures gebruiken voor het maken van certificaten en het beheer van de levens duur.

## <a name="powershell-scripts"></a>PowerShell-scripts

### <a name="step-1---setup"></a>Stap 1-Setup

OpenSSL voor Windows ophalen. Zie <https://www.openssl.org/docs/faq.html#MISC4> voor plaatsen om het te downloaden of <https://www.openssl.org/source/> om te bouwen vanuit de bron. Voer vervolgens de volgende voorbereidende scripts uit:

1. Kopieer de scripts van deze GitHub- [opslag plaats](https://github.com/Azure/azure-iot-sdk-c/tree/master/tools/CACertificates) naar de lokale map waarin u wilt werken. Alle bestanden worden gemaakt als onderliggende items van deze map.

1. Start Power shell als beheerder.

1. Ga naar de map waarin u de scripts hebt geladen.

1. Stel op de opdracht regel de omgevings variabele in `$ENV:OPENSSL_CONF` op de map waarin het openssl-configuratie bestand (openssl. cnf) zich bevindt.

1. Voer uit `Set-ExecutionPolicy -ExecutionPolicy Unrestricted` zodat Power shell de scripts kan uitvoeren.

1. Voer `. .\ca-certs.ps1` uit. Hiermee worden de functies van het script omgezet in de globale naam ruimte van Power shell.

1. Voer `Test-CACertsPrerequisites` uit. Power shell gebruikt het Windows-certificaat archief om certificaten te beheren. Met deze opdracht wordt gecontroleerd of er later geen naam conflict is met bestaande certificaten en dat OpenSSL correct wordt ingesteld.

### <a name="step-2---create-certificates"></a>Stap 2: certificaten maken

Voer `New-CACertsCertChain [ecc|rsa]` uit. ECC wordt aanbevolen voor CA-certificaten, maar niet vereist. Met dit script wordt uw directory-en Windows-certificaat archief bijgewerkt met de volgende certificerings instantie en tussenliggende certificaten:

* intermediate1. pem
* intermediate2. pem
* intermediate3. pem
* RootCA. CER
* RootCA. pem

Nadat u het script hebt uitgevoerd, voegt u het nieuwe CA-certificaat (RootCA. pem) toe aan uw IoT Hub:

1. Ga naar uw IoT Hub en navigeer naar certificaten.

1. Selecteer **Toevoegen**.

1. Voer een weergave naam in voor het CA-certificaat.

1. Upload het CA-certificaat.

1. Selecteer **Opslaan**.

### <a name="step-3---prove-possession"></a>Stap 3: bezit bewijzen

Nu u uw CA-certificaat hebt geüpload naar uw IoT Hub, moet u bewijzen dat u er echt eigenaar van bent:

1. Selecteer het nieuwe CA-certificaat.

1. Selecteer **verificatie code genereren** in het dialoog venster **certificaat Details** . Zie [bewijzen over een CA-certificaat](tutorial-x509-prove-possession.md)voor meer informatie.

1. Maak een certificaat dat de verificatie code bevat. Als de verificatie code bijvoorbeeld is `"106A5SD242AF512B3498BD6098C4941E66R34H268DDB3288"` , voert u de volgende stappen uit om een nieuw certificaat te maken in uw werkmap met daarin het onderwerp `CN = 106A5SD242AF512B3498BD6098C4941E66R34H268DDB3288` . Het script maakt een certificaat met de naam `VerifyCert4.cer` .

    `New-CACertsVerificationCert "106A5SD242AF512B3498BD609C4941E66R34H268DDB3288"`

1. Upload `VerifyCert4.cer` naar uw IOT hub in het dialoog venster **certificaat Details** .

1. Selecteer **Verifiëren**.

### <a name="step-4---create-a-new-device"></a>Stap 4: een nieuw apparaat maken

Een apparaat maken voor uw IoT Hub:

1. Navigeer in uw IoT Hub naar het gedeelte **IOT-apparaten** .

1. Voeg een nieuw apparaat toe met de ID `mydevice` .

1. Kies voor verificatie de optie **X. 509 ca ondertekend**.

1. Voer uit `New-CACertsDevice mydevice` om een nieuw certificaat voor het apparaat te maken. Hiermee maakt u de volgende bestanden in uw werkmap:

   * `mydevice.pfx`
   * `mydevice-all.pem`
   * `mydevice-private.pem`
   * `mydevice-public.pem`

### <a name="step-5---test-your-device-certificate"></a>Stap 5: het certificaat van uw apparaat testen

Ga naar [verificatie van certificaat testen](tutorial-x509-test-certificate.md) om te bepalen of het certificaat van uw apparaat kan worden geverifieerd bij uw IOT hub. U hebt de PFX-versie van uw certificaat nodig `mydevice.pfx` .

### <a name="step-6---cleanup"></a>Stap 6: opschonen

Open in het menu Start de optie **computer certificaten beheren** en navigeer naar  **certificaten-lokale computer > persoonlijk**. Certificaten verwijderen die zijn uitgegeven door Azure IoT CA TestOnly *. Verwijder ook de juiste certificaten van **>vertrouwde basis certificerings instantie > certificaten en >tussenliggende certificerings instanties > certificaten**.

## <a name="bash-scripts"></a>Bash-scripts

### <a name="step-1---setup"></a>Stap 1-Setup

1. Bash starten.

1. Ga naar de map waarin u wilt werken. Alle bestanden worden in deze map gemaakt.

1. Kopieer `*.cnf` en `*.sh` naar uw werkmap.

### <a name="step-2---create-certificates"></a>Stap 2: certificaten maken

1. Voer `./certGen.sh create_root_and_intermediate` uit. Hiermee maakt u de volgende bestanden in de map **certificaten** :

    * Azure-IOT-test-only. keten. ca. cert. pem
    * Azure-IOT-test-only. Intermediate. cert. pem
    * Azure-IOT-test-only. root. ca. cert. pem

1. Ga naar uw IoT Hub en navigeer naar **certificaten**.

1. Selecteer **Toevoegen**.

1. Voer een weergave naam in voor het CA-certificaat.

1. Upload alleen het CA-certificaat naar uw IoT Hub. De naam van het certificaat is `./certs/azure-iot-test-only.root.ca.cert.pem.`

1. Selecteer **Opslaan**.

### <a name="step-3---prove-possession"></a>Stap 3: bezit bewijzen

1. Selecteer het nieuwe CA-certificaat dat u in de vorige stap hebt gemaakt.

1. Selecteer **verificatie code genereren** in het dialoog venster **certificaat Details** . Zie [bewijzen over een CA-certificaat](tutorial-x509-prove-possession.md)voor meer informatie.

1. Maak een certificaat dat de verificatie code bevat. Als de verificatie code bijvoorbeeld is `"106A5SD242AF512B3498BD6098C4941E66R34H268DDB3288"` , voert u de volgende stappen uit om een nieuw certificaat te maken in de werkmap met `verification-code.cert.pem` de naam die het onderwerp bevat `CN = 106A5SD242AF512B3498BD6098C4941E66R34H268DDB3288` .

    `./certGen.sh create_verification_certificate "106A5SD242AF512B3498BD6098C4941E66R34H268DDB3288"`

1. Upload het certificaat naar uw IoT-hub in het dialoog venster **certificaat Details** .

1. Selecteer **Verifiëren**.

### <a name="step-4---create-a-new-device"></a>Stap 4: een nieuw apparaat maken

Een apparaat maken voor uw IoT-hub:

1. Navigeer in uw IoT Hub naar het gedeelte IoT-apparaten.

1. Voeg een nieuw apparaat toe met de ID `mydevice` .

1. Kies voor verificatie de optie **X. 509 ca ondertekend**.

1. Voer uit `./certGen.sh create_device_certificate mydevice` om een nieuw certificaat voor het apparaat te maken. Hiermee maakt u twee bestanden `new-device.cert.pem` `new-device.cert.pfx` met de naam en bestanden in uw werkmap.

### <a name="step-5---test-your-device-certificate"></a>Stap 5: het certificaat van uw apparaat testen

Ga naar [verificatie van certificaat testen](tutorial-x509-test-certificate.md) om te bepalen of het certificaat van uw apparaat kan worden geverifieerd bij uw IOT hub. U hebt de PFX-versie van uw certificaat nodig `new-device.cert.pfx` .

### <a name="step-6---cleanup"></a>Stap 6: opschonen

Omdat het bash-script eenvoudigweg certificaten in uw werkmap maakt, moet u deze gewoon verwijderen wanneer u klaar bent met testen.

## <a name="next-steps"></a>Volgende stappen

Als u uw certificaat wilt testen, gaat u naar [verificatie van certificaat testen](tutorial-x509-test-certificate.md) om te bepalen of uw apparaat kan worden geverifieerd op uw IOT hub.
