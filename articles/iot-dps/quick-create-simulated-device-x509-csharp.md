---
title: Snelstart - Een gesimuleerd X.509-apparaat inrichten voor Azure IoT Hub met behulp van C#
description: Snelstart - Een gesimuleerd X.509-apparaat met de SDK voor C# maken en inrichten voor Azure IoT Hub Device Provisioning Service (DPS). In deze snelstart wordt gebruikgemaakt van afzonderlijke inschrijvingen.
author: wesmc7777
ms.author: wesmc
ms.date: 02/01/2021
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
ms.devlang: csharp
ms.custom: mvc
ms.openlocfilehash: a6e859a39cbcf867e3c0a21bb59c6154cbd47412
ms.sourcegitcommit: eb546f78c31dfa65937b3a1be134fb5f153447d6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/02/2021
ms.locfileid: "99430579"
---
# <a name="quickstart-create-and-provision-a-simulated-x509-device-using-c-device-sdk-for-iot-hub-device-provisioning-service"></a>Snelstart: Een gesimuleerd X.509-apparaat met de SDK voor C# maken en inrichten voor IoT Hub Device Provisioning Service

[!INCLUDE [iot-dps-selector-quick-create-simulated-device-x509](../../includes/iot-dps-selector-quick-create-simulated-device-x509.md)]

Deze stappen laten u zien hoe u de [Azure IoT-voorbeelden voor C#](https://github.com/Azure-Samples/azure-iot-samples-csharp) gebruikt om een X.509-apparaat te simuleren op een ontwikkelcomputer met het Windows-besturingssysteem. In het voorbeeld wordt het gesimuleerde apparaat ook verbonden met een IoT Hub met behulp van de Device Provisioning Service.

Raadpleeg het overzicht [Inrichten](about-iot-dps.md#provisioning-process) als u niet bekend bent met het proces van automatisch inrichten. Controleer ook of u de stappen in [IoT Hub Device Provisioning Service instellen met Azure Portal](./quick-setup-auto-provision.md) hebt voltooid voordat u verdergaat. 

Azure IoT Device Provisioning Service ondersteunt twee typen inschrijvingen:
- [Inschrijvingsgroepen](concepts-service.md#enrollment-group): Wordt gebruikt om meerdere gerelateerde apparaten in te schrijven.
- [Afzonderlijke inschrijvingen](concepts-service.md#individual-enrollment): Wordt gebruikt om één apparaat in te schrijven.

In dit artikel worden afzonderlijke inschrijvingen gedemonstreerd.

[!INCLUDE [IoT Device Provisioning Service basic](../../includes/iot-dps-basic.md)]

<a id="setupdevbox"></a>
## <a name="prepare-the-development-environment"></a>De ontwikkelomgeving voorbereiden 

1. Zorg ervoor dat `git` op de computer wordt geïnstalleerd en toegevoegd aan de omgevingsvariabelen die voor het opdrachtvenster toegankelijk zijn. Zie [Software Freedom Conservancy's Git client tools](https://git-scm.com/download/) (Git-clienthulpprogramma's van Software Freedom Conservancy) om de nieuwste versie van `git`-hulpprogramma's te installeren, waaronder **Git Bash**, de opdrachtregel-app die u kunt gebruiken voor interactie met de lokale Git-opslagplaats. 

1. Open een opdrachtprompt of Git Bash. Kloon de Azure IoT-voorbeelden GitHub-opslagplaats voor C#:
    
    ```bash
    git clone https://github.com/Azure-Samples/azure-iot-samples-csharp.git
    ```

1. Zorg ervoor dat u de [.net Core 3.0.0 SDK of hoger](https://www.microsoft.com/net/download/windows) op uw computer hebt geïnstalleerd. U kunt de volgende opdracht gebruiken om uw versie te controleren.

    ```bash
    dotnet --info
    ```



## <a name="create-a-self-signed-x509-device-certificate"></a>Een zelfondertekend X.509-apparaatcertificaat maken

In deze sectie maakt u een zelfondertekend X. 509-test certificaat dat wordt gebruikt `iothubx509device1` als de algemene naam van het onderwerp. Het is belang rijk dat u rekening houdt met het volgende:

* Zelfondertekende certificaten zijn alleen voor testdoeleinden en moeten niet in productieomgevingen worden gebruikt.
* De standaardvervaltermijn voor een zelfondertekend certificaat is één jaar.
* De apparaat-ID van het IoT-apparaat wordt de algemene naam van het onderwerp op het certificaat. Zorg ervoor dat u een onderwerpnaam gebruikt die voldoet aan de vereisten voor de [teken reeks voor de apparaat-id](../iot-hub/iot-hub-devguide-identity-registry.md#device-identity-properties).

U gaat voorbeeldcode uit [Provisioning Device Client Sample - X.509 Attestation](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/provisioning/Samples/device/X509Sample) gebruiken om het certificaat te maken dat moet worden gebruikt met de afzonderlijke inschrijvingsvermelding voor het gesimuleerde apparaat.


1. In een Power shell-prompt wijzigt u de directory's in de projectmap voor het X. 509 Device Provisioning-voor beeld.

    ```powershell
    cd .\azure-iot-samples-csharp\provisioning\Samples\device\X509Sample
    ```

2. De voorbeeldcode is ingesteld voor het gebruik van X.509-certificaten die zijn opgeslagen in een met PKCS12 opgemaakt bestand (certificate.pfx) met wachtwoordbeveiliging. Bovendien hebt u een certificaatbestand met een openbare sleutel (certificate.cer) nodig om later in deze snelstart een afzonderlijke registratie te maken. Als u het zelfondertekende certificaat en de bijbehorende CER-en pfx-bestanden wilt genereren, voert u de volgende opdracht uit:

    ```powershell
    PS D:\azure-iot-samples-csharp\provisioning\Samples\device\X509Sample> .\GenerateTestCertificate.ps1 iothubx509device1
    ```

3. Het script vraagt u om een PFX-wachtwoord. Onthoud dit wacht woord, u moet het later opnieuw gebruiken wanneer u het voor beeld uitvoert. U kunt uitvoeren `certutil` om het certificaat te dumpen en de onderwerpnaam te verifiëren.

    ```powershell
    PS D:\azure-iot-samples-csharp\provisioning\Samples\device\X509Sample> certutil .\certificate.pfx
    Enter PFX password:
    ================ Certificate 0 ================
    ================ Begin Nesting Level 1 ================
    Element 0:
    Serial Number: 7b4a0e2af6f40eae4d91b3b7ff05a4ce
    Issuer: CN=iothubx509device1, O=TEST, C=US
     NotBefore: 2/1/2021 6:18 PM
     NotAfter: 2/1/2022 6:28 PM
    Subject: CN=iothubx509device1, O=TEST, C=US
    Signature matches Public Key
    Root Certificate: Subject matches Issuer
    Cert Hash(sha1): e3eb7b7cc1e2b601486bf8a733887a54cdab8ed6
    ----------------  End Nesting Level 1  ----------------
      Provider = Microsoft Strong Cryptographic Provider
    Signature test passed
    CertUtil: -dump command completed successfully.    
    ```

 ## <a name="create-an-individual-enrollment-entry-for-the-device"></a>Een afzonderlijke inschrijvings vermelding voor het apparaat maken


1. Meld u aan bij Azure Portal, selecteer in het linkermenu de knop **Alle resources** en open uw Provisioning-service.

2. Selecteer **Inschrijvingen beheren** in het Device Provisioning Service-menu. Selecteer het tabblad **Individuele inschrijvingen** en selecteer vervolgens de knop **Individuele inschrijving toevoegen** bovenaan. 

3. Voer in het deelvenster **Inschrijving toevoegen** de volgende gegevens in:
   - Selecteer **X.509** als *mechanisme* voor identiteitscontrole.
   - Kies onder het *PEM- of CER-bestand van het primaire certificaat* voor *Selecteer een bestand* om het certificaatbestand **certificate.cer** te selecteren dat in de vorige stappen is gemaakt.
   - Laat **Apparaat-id** leeg. Uw apparaat wordt ingericht met de apparaat-id ingesteld op de algemene naam (CN) in het X.509-certificaat, **iothubx509device1**. Dit is ook de naam die wordt gebruikt voor de registratie-id voor de vermelding voor de afzonderlijke registratie. 
   - Desgewenst kunt u de volgende informatie verstrekken:
       - Selecteer een IoT-hub die is gekoppeld aan uw inrichtingsservice.
       - Werk de **initiële status van de apparaatdubbel** bij met de gewenste beginconfiguratie voor het apparaat.
   - Klik op de knop **Opslaan** als u klaar bent. 

     [![Afzonderlijke inschrijving voor X.509-attestation toevoegen in de portal](./media/quick-create-simulated-device-x509-csharp/device-enrollment.png)](./media/quick-create-simulated-device-x509-csharp/device-enrollment.png#lightbox)
    
   Als het apparaat is ingeschreven, wordt het X.509-apparaat weergegeven als **iothubx509device1** onder de kolom *Registratie-id* op het tabblad *Afzonderlijke registraties*. 



## <a name="provision-the-simulated-device"></a>Het gesimuleerde apparaat inrichten

1. Ga naar de blade **Overzicht** voor de provisioning-service en noteer de waarde van **_Id-bereik_**.

    ![Device Provisioning Service-eindpuntgegevens uit de portalblade extraheren](./media/quick-create-simulated-device-x509-csharp/copy-scope.png) 


2. Typ de volgende opdracht om de voorbeeldcode voor het X.509-apparaat te bouwen en uit te voeren. Vervang de waarde voor `<IDScope>` door het id-bereik voor uw provisioning-service. 

    Het certificaat bestand wordt standaard ingesteld op *./Certificate.pfx* en prompt voor het pfx-wacht woord.  

    ```powershell
    dotnet run -- -s <IDScope>
    ```

    Als u alles wilt door geven als een para meter, kunt u de volgende voorbeeld indeling gebruiken.

    ```powershell
    dotnet run -- -s 0ne00000A0A -c certificate.pfx -p 1234
    ```


3. Het apparaat maakt verbinding met DPS en wordt toegewezen aan een IoT Hub. Op het apparaat wordt ook een telemetrie-bericht naar de hub verzonden.

    ```output
    Loading the certificate...
    Found certificate: 10952E59D13A3E388F88E534444484F52CD3D9E4 CN=iothubx509device1, O=TEST, C=US; PrivateKey: True
    Using certificate 10952E59D13A3E388F88E534444484F52CD3D9E4 CN=iothubx509device1, O=TEST, C=US
    Initializing the device provisioning client...
    Initialized for registration Id iothubx509device1.
    Registering with the device provisioning service...
    Registration status: Assigned.
    Device iothubx509device2 registered to sample-iot-hub1.azure-devices.net.
    Creating X509 authentication for IoT Hub...
    Testing the provisioned device with IoT Hub...
    Sending a telemetry message...
    Finished.
    ```

4. Controleer of het apparaat is ingericht. Als het gesimuleerde apparaat is ingericht met de IoT-hub die is gekoppeld met de provisioning-service, wordt de apparaat-id weergegeven op de blade **IoT-apparaten** van de hub. 

    ![Apparaat wordt geregistreerd voor de IoT-hub](./media/quick-create-simulated-device-x509-csharp/registration.png) 

    Als u de standaardwaarde van de *initiële status van de apparaatdubbel* hebt gewijzigd in de inschrijvingsvermelding voor uw apparaat, kan de gewenste status van de dubbel uit de hub worden gehaald en er dienovereenkomstig naar worden gehandeld. Zie [Understand and use device twins in IoT Hub](../iot-hub/iot-hub-devguide-device-twins.md) (Apparaatdubbelen begrijpen en gebruiken in IoT Hub) voor meer informatie


## <a name="clean-up-resources"></a>Resources opschonen

Als u wilt blijven doorwerken met het voorbeeld van de apparaatclient en deze beter wilt leren kennen, wis de resources die in deze quickstart zijn gemaakt dan niet. Als u niet wilt doorgaan, gebruikt u de volgende stappen om alle resources te verwijderen die via deze quickstart zijn gemaakt.

1. Sluit het uitvoervenster van het voorbeeld van de apparaatclient op de computer.
1. Sluit het TPM-simulatorvenster op de computer.
1. Selecteer in het linkermenu in Azure Portal **Alle resources** en selecteer uw Device Provisioning Service. Klik bovenaan de blade **Overzicht** op **Verwijderen** bovenaan het deelvenster.  
1. Selecteer in het linkermenu in Azure Portal **Alle resources** en selecteer vervolgens uw IoT-hub. Klik bovenaan de blade **Overzicht** op **Verwijderen** bovenaan het deelvenster.  

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u een gesimuleerd X.509-apparaat op uw Windows-computer gemaakt en het ingericht voor uw IoT-hub met de Azure IoT Hub Device Provisioning Service in de portal. Als u wilt weten hoe u uw X.509-apparaat programmatisch kunt registreren, gaat u verder met de quickstart voor programmatische registratie van een X.509-apparaat. 

> [!div class="nextstepaction"]
> [Azure-quickstart: X.509-apparaat inschrijven bij Azure IoT Hub Device Provisioning Service](quick-enroll-device-x509-csharp.md)
