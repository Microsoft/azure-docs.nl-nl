---
title: 'Quickstart: X.509-apparaten registreren bij Azure Device Provisioning Service met behulp van Java'
description: In deze quickstart wordt gebruikgemaakt van zowel groepsregistraties als afzonderlijke registraties. In deze quickstart registreert u X.509-apparaten bij Azure IoT Hub Device Provisioning Service (DPS) met behulp van Java.
author: wesmc7777
ms.author: wesmc
ms.date: 11/08/2019
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
ms.devlang: java
ms.custom: mvc, devx-track-java
ms.openlocfilehash: 8591220eb80d9c60ed7873be546007449ab46f89
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96463027"
---
# <a name="quickstart-enroll-x509-devices-to-the-device-provisioning-service-using-java"></a>Quickstart: X.509-apparaten registreren bij de Device Provisioning Service met behulp van Java

[!INCLUDE [iot-dps-selector-quick-enroll-device-x509](../../includes/iot-dps-selector-quick-enroll-device-x509.md)]

In deze quickstart gebruikt u Java om programmatisch een groep gesimuleerde X.509-apparaten te registreren bij Azure IoT Hub Device Provisioning Service. Apparaten worden geregistreerd op een exemplaar van de inrichtingsservice door een registratiegroep of een afzonderlijke inschrijving te maken. Deze quickstart laat zien hoe u beide typen registraties kunt maken met behulp van de Java Service SDK en een voorbeeld van een Java-toepassing.

## <a name="prerequisites"></a>Vereisten

- U hebt [IoT Hub Device Provisioning Service instellen met Azure Portal](./quick-setup-auto-provision.md) voltooid.
- Een Azure-account met een actief abonnement. [Maak er gratis een](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- [Java SE Development Kit 8](/azure/developer/java/fundamentals/java-jdk-long-term-support). In deze quickstart wordt de [Java-service-SDK](https://azure.github.io/azure-iot-sdk-java/master/service/) hieronder geïnstalleerd. Deze is geschikt voor Windows en Linux. In deze quickstart wordt Windows gebruikt.
- [Maven 3](https://maven.apache.org/download.cgi).
- [Git](https://git-scm.com/download/).

<a id="javasample"></a>

## <a name="download-and-modify-the-java-sample-code"></a>De Java-voorbeeldcode downloaden en wijzigen

In dit gedeelte wordt een zelfondertekend X.509-certificaat gebruikt. Het is belangrijk dat u rekening houdt met de volgende punten:

* Zelfondertekende certificaten zijn alleen voor testdoeleinden en moeten niet in productieomgevingen worden gebruikt.
* De standaardvervaltermijn voor een zelfondertekend certificaat is één jaar.

In de volgende stappen wordt gedemonstreerd hoe u de inrichtingsgegevens van het X.509-apparaat toevoegt aan de voorbeeldcode. 

1. Open een opdrachtprompt. Kloon de GitHub-opslagplaats voor het codevoorbeeld van de apparaatinschrijving met behulp van de [Java service-SDK](https://azure.github.io/azure-iot-sdk-java/master/service/):
    
    ```cmd\sh
    git clone https://github.com/Azure/azure-iot-sdk-java.git --recursive
    ```

2. Navigeer in de gedownloade broncode naar de voorbeeldmap **_azure-iot-sdk-java/provisioning/provisioning-samples/service-enrollment-group-sample_**. Open het bestand **_/src/main/java/samples/com/microsoft/azure/sdk/iot/ServiceEnrollmentGroupSample.java_** in een editor naar keuze, en voeg de volgende gegevens toe:

    1. Voeg de `[Provisioning Connection String]` voor de inrichtingsservice als volgt toe vanuit de portal:
        1. Navigeer naar de inrichtingsservice in [Azure Portal](https://portal.azure.com). 
        2. Open **Gedeeld toegangsbeleid** en selecteer een beleid met de machtiging *EnrollmentWrite*.
        3. Kopieer de **Verbindingsreeks van de primaire sleutel**. 

            ![De verbindingsreeks voor de inrichting ophalen uit de portal](./media/quick-enroll-device-x509-java/provisioning-string.png)  

        4. Vervang `[Provisioning Connection String]` in het voorbeeldcodebestand **_ServiceEnrollmentGroupSample.java_** door de **Verbindingsreeks van de primaire sleutel**.

            ```Java
            private static final String PROVISIONING_CONNECTION_STRING = "[Provisioning Connection String]";
            ```

    2. Voeg het basiscertificaat voor de groep apparaten toe. Als u een voorbeeldbasiscertificaat nodig hebt, gebruikt u het hulpprogramma _X.509-certificaatgenerator_ als volgt:
        1. Navigeer in een opdrachtvenster naar de map **_azure-iot-sdk-java/provisioning/provisioning-tools/provisioning-x509-cert-generator_**.
        2. Bouw het hulpprogramma met behulp van de volgende opdracht:

            ```cmd\sh
            mvn clean install
            ```

        4. Voer het hulpprogramma uit met behulp van de volgende opdrachten:

            ```cmd\sh
            cd target
            java -jar ./provisioning-x509-cert-generator-{version}-with-deps.jar
            ```

        5. Wanneer u hierom wordt gevraagd, kunt u ook een _algemene naam_ invoeren voor de certificaten.
        6. Het hulpprogramma genereert lokaal een **Clientcertificaat**, de **Persoonlijke sleutel van clientcertificaat** en het **Basiscertificaat**.
        7. Kopieer het **Basiscertificaat**, inclusief de regels **_-----BEGIN CERTIFICATE-----_** and **_-----END CERTIFICATE-----_**. 
        8. Wijs de waarde van het **Basiscertificaat** toe aan de parameter **PUBLIC_KEY_CERTIFICATE_STRING** zoals hieronder wordt weergegeven:

            ```Java
            private static final String PUBLIC_KEY_CERTIFICATE_STRING =
            "-----BEGIN CERTIFICATE-----\n" +
            "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
            "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
            "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
            "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
            "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
            "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
            "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
            "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
            "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
            "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
            "-----END CERTIFICATE-----\n";
            ```

        9. Sluit het opdrachtvenster of voer **n** in wanneer u wordt gevraagd om een *Verificatiecode*. 
 
    3. Desgewenst kunt u de inrichtingsservice configureren via de voorbeeldcode:
        - Volg deze stappen om deze configuratie toe te voegen aan het voorbeeld:
            1. Navigeer naar de IoT-hub die is gekoppeld aan de inrichtingsservice in [Azure Portal](https://portal.azure.com). Open het tabblad **Overzicht** voor de hub en kopieer de **Hostnaam**. Wijs deze **Hostnaam** toe aan de parameter *IOTHUB_HOST_NAME*.

                ```Java
                private static final String IOTHUB_HOST_NAME = "[Host name].azure-devices.net";
                ```
            2. Wijs een beschrijvende naam toe aan de parameter *DEVICE_ID* en houd *PROVISIONING_STATUS* aan als standaardwaarde *ENABLED*. 

        - OF, als u de inrichtingsservice niet wilt configureren, zorgt u ervoor dat u de volgende instructies in het bestand _ServiceEnrollmentGroupSample.java_ als commentaar markeert of verwijdert:

            ```Java
            enrollmentGroup.setIotHubHostName(IOTHUB_HOST_NAME);                // Optional parameter.
            enrollmentGroup.setProvisioningStatus(ProvisioningStatus.ENABLED);  // Optional parameter.
            ```

    4. Bestudeer de voorbeeldcode. Hiermee wordt een groepsregistratie voor X.509-apparaten gemaakt, bijgewerkt, en verwijderd, en wordt voor deze registratie een query uitgevoerd. Markeer de volgende coderegels aan het einde van het bestand _ServiceEnrollmentGroupSample.java_ tijdelijk als commentaar voor een geslaagde inschrijving via de portal:

        ```Java
        // ************************************** Delete info of enrollmentGroup ***************************************
        System.out.println("\nDelete the enrollmentGroup...");
        provisioningServiceClient.deleteEnrollmentGroup(enrollmentGroupId);
        ```

    5. Sla het bestand _ServiceEnrollmentGroupSample.java_ op. 
 

<a id="runjavasample"></a>

## <a name="build-and-run-sample-group-enrollment"></a>Een voorbeeldgroepsinschrijving bouwen en uitvoeren

Azure IoT Device Provisioning Service ondersteunt twee typen inschrijvingen:

- [Inschrijvingsgroepen](concepts-service.md#enrollment-group): Wordt gebruikt om meerdere gerelateerde apparaten in te schrijven.
- [Individuele inschrijvingen](concepts-service.md#individual-enrollment): Wordt gebruikt om één apparaat in te schrijven.

Deze procedure maakt gebruik van een registratiegroep. In de volgende sectie wordt een afzonderlijke inschrijving gebruikt.

1. Open een opdrachtvenster en navigeer naar de map **_azure-iot-sdk-java/provisioning/provisioning-samples/service-enrollment-group-sample_**.

2. Bouw de voorbeeldcode met behulp van deze opdracht:

    ```cmd\sh
    mvn install -DskipTests
    ```

   Met deze opdracht wordt het Maven-pakket [`com.microsoft.azure.sdk.iot.provisioning.service`](https://mvnrepository.com/artifact/com.microsoft.azure.sdk.iot.provisioning/provisioning-service-client) gedownload naar de computer. Dit pakket bevat de binaire bestanden voor de Java service-SDK, die de voorbeeldcode nodig heeft voor het bouwen. Als u het hulpprogramma _X.509-certificaatgenerator_ hebt uitgevoerd in de vorige sectie, is dit pakket al gedownload op de computer. 

3. Voer het voorbeeld uit met behulp van deze opdrachten in het opdrachtvenster:

    ```cmd\sh
    cd target
    java -jar ./service-enrollment-group-sample-{version}-with-deps.jar
    ```

4. Houd het uitvoervenster in de gaten voor een geslaagde inschrijving.

5. Navigeer naar de inrichtingsservice in Azure Portal. Klik op **Inschrijvingen beheren**. U ziet dat de groep X.509-apparaten wordt weergegeven op het tabblad **Registratiegroepen** met een automatisch gegenereerde *GROEPSNAAM*. 

    ![De X.509-inschrijving controleren in de portal](./media/quick-enroll-device-x509-java/verify-x509-enrollment.png)  

## <a name="modifications-to-enroll-a-single-x509-device"></a>Wijzigingen voor het inschrijven van een enkel X.509-apparaat

Als u een enkel X.509-apparaat wilt inschrijven, wijzigt u als volgt de voorbeeldcode voor *afzonderlijke inschrijving* die wordt gebruikt in [TPM-apparaat inschrijven bij IoT Hub Device Provisioning Service met behulp van de Java service-SDK](quick-enroll-device-tpm-java.md#javasample):

1. Kopieer de *Algemene naam* van het X.509-clientcertificaat naar het klembord. Als u het hulpprogramma _X.509-certificaatgenerator_ wilt gebruiken zoals wordt weergegeven in de [vorige voorbeeldcodesectie](#javasample), voert u een _Algemene naam_ in voor uw certificaat, of gebruikt u de standaardwaarde **microsoftriotcore**. Gebruik deze **Algemene naam** als de waarde voor de variabele *REGISTRATION_ID*. 

    ```Java
    // Use common name of your X.509 client certificate
    private static final String REGISTRATION_ID = "[RegistrationId]";
    ```

2. Wijzig de naam van de variabele *TPM_ENDORSEMENT_KEY* in *PUBLIC_KEY_CERTIFICATE_STRING*. Kopieer **Clientcertificaat** uit de uitvoer van het hulpprogramma _X.509-certificaatgenerator_ als de waarde voor de variabele *PUBLIC_KEY_CERTIFICATE_STRING*. 

    ```Java
    // Rename the variable *TPM_ENDORSEMENT_KEY* as *PUBLIC_KEY_CERTIFICATE_STRING*
    private static final String PUBLIC_KEY_CERTIFICATE_STRING =
            "-----BEGIN CERTIFICATE-----\n" +
            "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
            "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
            "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
            "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
            "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
            "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
            "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
            "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
            "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
            "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
            "-----END CERTIFICATE-----\n";
    ```
3. Vervang in de **hoofdfunctie** de regel `Attestation attestation = new TpmAttestation(TPM_ENDORSEMENT_KEY);` door het volgende om het X.509-clientcertificaat te gebruiken:
    ```Java
    Attestation attestation = X509Attestation.createFromClientCertificates(PUBLIC_KEY_CERTIFICATE_STRING);
    ```

4. Sla het voorbeeldbestand voor de *afzonderlijke registratie* op, bouw het bestand en voer het uit. Gebruik hiervoor de stappen in het gedeelte [Build and run the sample code for individual enrollment](quick-enroll-device-tpm-java.md#runjavasample) (De voorbeeldcode voor afzonderlijke registraties bouwen en uitvoeren).


## <a name="clean-up-resources"></a>Resources opschonen
Als u van plan bent om het voorbeeld van de Java-service te verkennen, schoont u de resources die u in deze quickstart hebt gemaakt, niet op. Als u niet wilt doorgaan, gebruikt u de volgende stappen om alle resources te verwijderen die via deze quickstart zijn gemaakt.

1. Sluit het uitvoervenster van het Java-voorbeeld op de computer.
1. Sluit het venster voor de _X.509-certificaatgenerator_ op de computer.
1. Navigeer naar Device Provisioning Service in Azure Portal, selecteer **Inschrijvingen beheren** en selecteer vervolgens het tabblad **Inschrijvingsgroepen**. Schakel het selectievakje *GROUP NAME* in van de X.509-apparaten die u hebt ingeschreven met behulp van deze quickstart. Druk vervolgens op de knop **Verwijderen** bovenaan het deelvenster.  

## <a name="next-steps"></a>Volgende stappen
In deze quickstart hebt u een gesimuleerde groep met X.509-apparaten ingeschreven met Device Provisioning Service. Voor meer informatie over device provisioning, gaat u verder met de zelfstudie voor het instellen van Device Provisioning Service in Azure Portal. 

> [!div class="nextstepaction"]
> [Zelfstudies over Azure IoT Hub Device Provisioning Service](./tutorial-set-up-cloud.md)