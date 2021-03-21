---
title: Quickstart - Een gesimuleerd X.509-apparaat inrichten voor Azure IoT Hub met behulp van Java
description: 'Azure-quickstart: een gesimuleerd X.509-apparaat maken en inrichten voor IoT Hub Device Provisioning Service (DPS) met de SDK voor Java. Voor deze quickstart worden afzonderlijke registraties gebruikt.'
author: wesmc7777
ms.author: wesmc
ms.date: 11/08/2018
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
ms.devlang: java
ms.custom: mvc, devx-track-java
ms.openlocfilehash: 2ed458ee0267a65bb7551940fc522509dc310311
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94966612"
---
# <a name="quickstart-create-and-provision-a-simulated-x509-device-using-java-device-sdk-for-iot-hub-device-provisioning-service"></a>Quickstart: Een gesimuleerd X.509-apparaat maken en inrichten voor IoT Hub Device Provisioning Service met behulp van de Java apparaat-SDK

[!INCLUDE [iot-dps-selector-quick-create-simulated-device-x509](../../includes/iot-dps-selector-quick-create-simulated-device-x509.md)]

In deze quickstart maakt u een gesimuleerd X.509-apparaat op uw Windows-computer. U gebruikt een Java-voorbeeldcode voor het apparaat om dit gesimuleerde apparaat te verbinden met uw IoT-hub met behulp van een individuele inschrijving bij de Device Provisioning Service (DPS).

## <a name="prerequisites"></a>Vereisten

- Vertrouwd zijn met de concepten van [inrichten](about-iot-dps.md#provisioning-process).
- U hebt [IoT Hub Device Provisioning Service instellen met Azure Portal](./quick-setup-auto-provision.md) voltooid.
- Een Azure-account met een actief abonnement. [Maak er gratis een](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- [Java SE Development Kit 8](/azure/developer/java/fundamentals/java-jdk-long-term-support).
- [Maven](https://maven.apache.org/install.html).
- [Git](https://git-scm.com/download/).

## <a name="prepare-the-environment"></a>De omgeving voorbereiden 

1. Zorg ervoor dat [Java SE Development Kit 8](/azure/developer/java/fundamentals/java-jdk-long-term-support) is geïnstalleerd op de computer.

2. Download en installeer [Maven](https://maven.apache.org/install.html).

3. Zorg ervoor dat Git op de computer is geïnstalleerd en is toegevoegd aan de omgevingsvariabelen die voor het opdrachtvenster toegankelijk zijn. Zie [Software Freedom Conservancy's Git client tools](https://git-scm.com/download/) (Git-clienthulpprogramma's van Software Freedom Conservancy) om de nieuwste versie van `git`-hulpprogramma's te installeren, waaronder **Git Bash**, de opdrachtregel-app die u kunt gebruiken voor interactie met de lokale Git-opslagplaats. 

4. Open een opdrachtprompt. Kloon het codevoorbeeld voor de GitHub-opslagplaats voor apparaatsimulatie:
    
    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-sdk-java.git --recursive
    ```
5. Navigeer naar de hoofdmap van `azure-iot-sdk-`-java en bouw het project om alle benodigde pakketten te downloaden.
   
   ```cmd/sh
   cd azure-iot-sdk-java
   mvn install -DskipTests=true
   ```
6. Navigeer naar het certificaatgeneratorproject en bouw het project. 

    ```cmd/sh
    cd azure-iot-sdk-java/provisioning/provisioning-tools/provisioning-x509-cert-generator
    mvn clean install
    ```

## <a name="create-a-self-signed-x509-device-certificate-and-individual-enrollment-entry"></a>Een zelfondertekend X.509-certificaat voor apparaten en een vermelding voor afzonderlijke registratie maken

In deze sectie gebruikt u een zelf-ondertekend X.509-certificaat. Het is hierbij belangrijk dat u rekening houdt met het volgende:

* Zelfondertekende certificaten zijn alleen voor testdoeleinden en moeten niet in productieomgevingen worden gebruikt.
* De standaardvervaltermijn voor een zelfondertekend certificaat is één jaar.

U gaat voorbeeldcode van de [Azure IoT C-SDK voor Java](https://github.com/Azure/azure-iot-sdk-java.git) gebruiken om het certificaat te maken dat moet worden gebruikt met de afzonderlijke inschrijvingsvermelding voor het gesimuleerde apparaat.

Azure IoT Device Provisioning Service ondersteunt twee typen inschrijvingen:

- [Inschrijvingsgroepen](concepts-service.md#enrollment-group): Wordt gebruikt om meerdere gerelateerde apparaten in te schrijven.
- [Individuele inschrijvingen](concepts-service.md#individual-enrollment): Wordt gebruikt om één apparaat in te schrijven.

In dit artikel worden individuele inschrijvingen gedemonstreerd.

1. Ga met behulp van de opdrachtprompt uit de vorige stappen naar de map `target` en voer het JAR-bestand uit dat in de vorige stap is gemaakt.

    ```cmd/sh
    cd target
    java -jar ./provisioning-x509-cert-generator-{version}-with-deps.jar
    ```

2. Voer **N** in bij _Wilt u een algemene naam invoeren?_ Kopieer de uitvoer van `Client Cert` naar het klembord, vanaf *-----BEGIN CERTIFICATE-----* tot en met *-----END CERTIFICATE-----* .

   ![Generator voor afzonderlijke certificaten](./media/java-quick-create-simulated-device-x509/individual.png)

3. Maak een bestand met de naam **_X509individual.pem_** op de Windows-computer, open het in een editor naar keuze en kopieer de inhoud van het klembord naar dit bestand. Sla het bestand op en sluit de editor.

4. Voer in de opdrachtprompt **N** in bij _Wilt u de verificatiecode invoeren?_ en houd de programma-uitvoer geopend zodat u deze later in deze quickstart kunt raadplegen. Later kopieert u de waarden `Client Cert` en `Client Cert Private Key` voor gebruik in de volgende sectie.

5. Meld u aan bij [Azure Portal](https://portal.azure.com), selecteer in het linkermenu de knop **Alle resources** en open uw exemplaar van Device Provisioning Service.

6. Selecteer **Inschrijvingen beheren** in het Device Provisioning Service-menu. Selecteer het tabblad **Individuele inschrijvingen** en selecteer vervolgens de knop **Individuele inschrijving toevoegen** bovenaan. 

7. Voer in het deelvenster **Inschrijving toevoegen** de volgende gegevens in:
   - Selecteer **X.509** als *mechanisme* voor identiteitscontrole.
   - Kies onder het *PEM- of CER-bestand van het primaire certificaat* de optie *Een bestand selecteren* om het certificaatbestand **X509individual.pem** te selecteren dat in de vorige stappen is gemaakt.  
   - Desgewenst kunt u de volgende informatie verstrekken:
     - Selecteer een IoT-hub die is gekoppeld aan uw inrichtingsservice.
     - Voer een unieke apparaat-id in. Vermijd gevoelige gegevens bij het benoemen van uw apparaat. 
     - Werk de **initiële status van de apparaatdubbel** bij met de gewenste beginconfiguratie voor het apparaat.
     - Klik op de knop **Opslaan** als u klaar bent. 

     [![Afzonderlijke inschrijving voor X.509-attestation toevoegen in de portal](./media/java-quick-create-simulated-device-x509/device-enrollment.png)](./media/how-to-manage-enrollments/individual-enrollment.png#lightbox)

     Als het apparaat is ingeschreven, wordt het X.509-apparaat weergegeven als **microsoftriotcore** onder de kolom *Registratie-id* op het tabblad *Afzonderlijke registraties*. 



## <a name="simulate-the-device"></a>Het apparaat simuleren

1. Selecteer **Overzicht** in het menu Device Provisioning Service en noteer de waarden voor _Id-bereik_ en _Globaal eindpunt voor inrichtingsservice_.

    ![Service-informatie](./media/java-quick-create-simulated-device-x509/extract-dps-endpoints.png)

2. Open een opdrachtprompt. Ga naar de map met het voorbeeldproject van de Java-SDK-opslagplaats.

    ```cmd/sh
    cd azure-iot-sdk-java/provisioning/provisioning-samples/provisioning-X509-sample
    ```

3. Voer de gegevens voor de inrichtingsservice en de X.509-identiteit in de code in. Deze worden voorafgaand aan de registratie van het apparaat tijdens het automatisch inrichten gebruikt voor attestation van het gesimuleerde apparaat:

   - Bewerk bestand `/src/main/java/samples/com/microsoft/azure/sdk/iot/ProvisioningX509Sample.java` om de eerder genoteerde waarden voor _Id-bereik_ en _Globaal eindpunt voor inrichtingsservice_ toe te voegen. Neem ook de waarden voor _Clientcertificaat_ en _Persoonlijke sleutel van clientcertificaat_ op die u in de vorige sectie hebt genoteerd.

      ```java
      private static final String idScope = "[Your ID scope here]";
      private static final String globalEndpoint = "[Your Provisioning Service Global Endpoint here]";
      private static final ProvisioningDeviceClientTransportProtocol PROVISIONING_DEVICE_CLIENT_TRANSPORT_PROTOCOL = ProvisioningDeviceClientTransportProtocol.HTTPS;
      private static final String leafPublicPem = "<Your Public PEM Certificate here>";
      private static final String leafPrivateKey = "<Your Private PEM Key here>";
      ```

   - Gebruik de volgende indeling bij het kopiëren/plakken van uw certificaat en privésleutel:
        
      ```java
      private static final String leafPublicPem = "-----BEGIN CERTIFICATE-----\n" +
        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
        "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
        "+XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
        "-----END CERTIFICATE-----\n";
      private static final String leafPrivateKey = "-----BEGIN PRIVATE KEY-----\n" +
            "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
            "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
            "XXXXXXXXXX\n" +
            "-----END PRIVATE KEY-----\n";
      ```

4. Bouw het voorbeeld. Navigeer naar de map `target` en voer het gemaakte JAR-bestand uit.

    ```cmd/sh
    mvn clean install
    cd target
    java -jar ./provisioning-x509-sample-{version}-with-deps.jar
    ```

5. Navigeer in Azure Portal naar de IoT-hub die is gekoppeld aan uw inrichtingsservice en open de blade **Device Explorer**. Wanneer het inrichten van het gesimuleerde X.509-apparaat voor de hub is voltooid, wordt de apparaat-id weergegeven op de blade **Device Explorer** met de *STATUS***ingeschakeld**.  U moet mogelijk op de knop **Vernieuwen** bovenaan drukken als u de blade vóór het uitvoeren van de voorbeeldapparaattoepassing al hebt geopend. 

    ![Apparaat wordt geregistreerd voor de IoT-hub](./media/java-quick-create-simulated-device-x509/hubregistration.png) 

> [!NOTE]
> Als u de standaardwaarde van de *initiële status van de apparaatdubbel* hebt gewijzigd in de inschrijvingsvermelding voor uw apparaat, kan de gewenste status van de dubbel uit de hub worden gehaald en er dienovereenkomstig naar worden gehandeld. Zie [Apparaatdubbels begrijpen en gebruiken in IoT Hub](../iot-hub/iot-hub-devguide-device-twins.md) voor meer informatie.
>


## <a name="clean-up-resources"></a>Resources opschonen

Als u wilt blijven doorwerken met het voorbeeld van de apparaatclient en deze beter wilt leren kennen, wis de resources die in deze quickstart zijn gemaakt dan niet. Als u niet wilt doorgaan, gebruikt u de volgende stappen om alle resources te verwijderen die via deze quickstart zijn gemaakt.

1. Sluit het uitvoervenster van het voorbeeld van de apparaatclient op de computer.
2. Selecteer in het linkermenu in Azure Portal **Alle resources** en selecteer uw Device Provisioning Service. Open de blade **Inschrijvingen beheren** voor uw service en selecteer vervolgens het tabblad **Individuele inschrijvingen**. Schakel het selectievakje naast de *Registratie-id* in van het apparaat dat u hebt ingeschreven in deze quickstart. Druk vervolgens op de knop **Verwijderen** bovenaan het deelvenster. 
3. Selecteer in het linkermenu in Azure Portal **Alle resources** en selecteer vervolgens uw IoT-hub. Open de blade **IoT-apparaten** voor uw hub, schakel het selectievakje *DEVICE ID* in van het apparaat dat u hebt geregistreerd in deze quickstart en druk vervolgens bovenaan op de knop **Verwijderen**.


## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u een gesimuleerd X.509-apparaat gemaakt op uw Windows-computer. U hebt de registratie ervan geconfigureerd in Azure IoT Hub Device Provisioning Service en vervolgens het apparaat automatisch ingericht voor IoT Hub. Als u wilt weten hoe u uw X.509-apparaat programmatisch kunt registreren, gaat u verder met de quickstart voor programmatische registratie van een X.509-apparaat. 

> [!div class="nextstepaction"]
> [Azure-quickstart: X.509-apparaat inschrijven bij Azure IoT Hub Device Provisioning Service](quick-enroll-device-x509-java.md)