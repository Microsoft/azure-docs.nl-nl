---
title: 'Quickstart: Een gesimuleerd TPM-apparaat met Python inrichten voor Azure IoT Hub'
description: 'Quickstart: Met de SDK voor Java een gesimuleerd TPM-apparaat maken en inrichten voor IoT Hub Device Provisioning Service (DPS). In deze snelstart wordt gebruikgemaakt van afzonderlijke inschrijvingen.'
author: wesmc7777
ms.author: wesmc
ms.date: 11/08/2018
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
ms.devlang: python
ms.custom: mvc, devx-track-python
ms.openlocfilehash: 23a2ca3cae92f65786a3d2fb978b1727e18857e8
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/20/2020
ms.locfileid: "94966633"
---
# <a name="quickstart-create-and-provision-a-simulated-tpm-device-using-python-device-sdk-for-iot-hub-device-provisioning-service"></a>Een gesimuleerd TPM-apparaat maken en inrichten voor IoT Hub Device Provisioning Service met de apparaat-SDK voor Python

[!INCLUDE [iot-dps-selector-quick-create-simulated-device-tpm](../../includes/iot-dps-selector-quick-create-simulated-device-tpm.md)]

In deze quickstart maakt u een gesimuleerd IoT-apparaat op uw Windows-computer. Het gesimuleerde apparaat bevat een TPM-simulator als HSM (Hardware Security Module). U gebruikt een Python-voorbeeldcode voor het apparaat om dit gesimuleerde apparaat te verbinden met uw IoT-hub met behulp van een individuele inschrijving bij de Device Provisioning Service (DPS).

## <a name="prerequisites"></a>Vereisten

- Vertrouwd zijn met het [inrichten](about-iot-dps.md#provisioning-process) van concepten.
- U hebt [IoT Hub Device Provisioning Service instellen met Azure Portal](./quick-setup-auto-provision.md) voltooid.
- Een Azure-account met een actief abonnement. [Maak er gratis een](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- [Visual Studio 2015+](https://visualstudio.microsoft.com/vs/) met Desktopontwikkeling C++.
- [CMake-bouwsysteem](https://cmake.org/download/).
- [Git](https://git-scm.com/download/).

> [!IMPORTANT]
> Dit artikel is alleen van toepassing op de afgeschafte Python SDK V1. Apparaat- en serviceclients voor de IoT Hub Device Provisioning Service zijn nog niet beschikbaar in V2. Het team is momenteel hard aan het werk om V2 van functiepariteit te voorzien.

[!INCLUDE [IoT Device Provisioning Service basic](../../includes/iot-dps-basic.md)]

## <a name="prepare-the-environment"></a>De omgeving voorbereiden 

1. Zorg ervoor dat u [Visual Studio](https://visualstudio.microsoft.com/vs/) 2015 of hoger hebt geïnstalleerd met de workload Desktopontwikkeling met C++ ingeschakeld voor uw Visual Studio-installatie.

1. Download en installeer het [CMake-bouwsysteem](https://cmake.org/download/).

1. Zorg ervoor dat `git` op de computer wordt geïnstalleerd en toegevoegd aan de omgevingsvariabelen die voor het opdrachtvenster toegankelijk zijn. Zie [Software Freedom Conservancy's Git client tools](https://git-scm.com/download/) (Git-clienthulpprogramma's van Software Freedom Conservancy) om de nieuwste versie van `git`-hulpprogramma's te installeren, waaronder **Git Bash**, de opdrachtregel-app die u kunt gebruiken voor interactie met de lokale Git-opslagplaats. 

1. Open een opdrachtprompt of Git Bash. Kloon het codevoorbeeld voor de GitHub-opslagplaats voor apparaatsimulatie:
    
    ```cmd/sh
    git clone --single-branch --branch v1-deprecated https://github.com/Azure/azure-iot-sdk-python.git --recursive
    ```

1. Maak een map in de lokale kopie van deze GitHub-opslagplaats voor het CMake-bouwproces. 

    ```cmd/sh
    cd azure-iot-sdk-python/c
    mkdir cmake
    cd cmake
    ```

1. In het codevoorbeeld wordt een Windows TPM-simulator gebruikt. Voer de volgende opdracht uit om SAS-tokenverificatie in te schakelen. Er wordt ook een Visual Studio-oplossing voor het gesimuleerde apparaat gegenereerd.

    ```cmd/sh
    cmake -Duse_prov_client:BOOL=ON -Duse_tpm_simulator:BOOL=ON ..
    ```

1. Ga door middel van een aparte opdrachtprompt naar de map van de TPM-simulator en voer de [TPM](/windows/device-security/tpm/trusted-platform-module-overview)-simulator uit, zodat deze de [HSM](https://azure.microsoft.com/blog/azure-iot-supports-new-security-hardware-to-strengthen-iot-security/) wordt van het gesimuleerde apparaat. Klik op **Toegang toestaan**. Deze luistert via een socket op poorten 2321 en 2322. Sluit dit opdrachtvenster niet; de simulator moet actief blijven tot u deze quickstart hebt voltooid. 

    ```cmd/sh
    .\azure-iot-sdk-python\c\provisioning_client\deps\utpm\tools\tpm_simulator\Simulator.exe
    ```

    ![TPM-simulator](./media/python-quick-create-simulated-device/tpm-simulator.png)


## <a name="create-a-device-enrollment-entry"></a>Een vermelding voor apparaatinschrijving maken

Azure IoT Device Provisioning Service ondersteunt twee typen inschrijvingen:

- [Registratiegroepen](concepts-service.md#enrollment-group): wordt gebruikt om meerdere gerelateerde apparaten in te schrijven.
- [Afzonderlijke inschrijvingen](concepts-service.md#individual-enrollment): wordt gebruikt om een enkel apparaat in te schrijven.

In dit artikel worden individuele inschrijvingen gedemonstreerd.

1. Open de in de map *cmake* gemaakt oplossing met de naam `azure_iot_sdks.sln` en bouw deze in Visual Studio.

1. Klik met de rechtermuisknop op het **tpm_device_provision**-project en selecteer **Set as Startup Project**. Voer de oplossing uit. In het uitvoervenster worden de voor apparaatinschrijving vereiste **_goedkeuringssleutel_** en **_registratie-id_** weergegeven. Noteer deze waarden. 

    ![TPM-installatie](./media/python-quick-create-simulated-device/tpm-setup.png)

1. Meld u aan bij Azure Portal, selecteer in het linkermenu de knop **Alle resources** en open Device Provisioning Service.

1. Selecteer **Inschrijvingen beheren** in het Device Provisioning Service-menu. Selecteer het tabblad **Individuele inschrijvingen** en selecteer vervolgens de knop **Individuele inschrijving toevoegen** bovenaan. 

1. Voer in het deelvenster **Inschrijving toevoegen** de volgende gegevens in:
   - Selecteer **TPM** als *mechanisme* voor identiteitscontrole.
   - Voer de *registratie-id* en *goedkeuringssleutel* voor het TPM-apparaat in aan de hand van de waarden die u eerder hebt genoteerd.
   - Selecteer een IoT-hub die is gekoppeld aan uw inrichtingsservice.
   - Desgewenst kunt u de volgende informatie verstrekken:
       - Voer een unieke *apparaat-id* in. Vermijd gevoelige gegevens bij het benoemen van uw apparaat. Als u ervoor kiest om er geen op te geven, wordt in plaats daarvan de registratie-id gebruikt om het apparaat te identificeren.
       - Werk de **initiële status van de apparaatdubbel** bij met de gewenste beginconfiguratie voor het apparaat.
   - Klik op de knop **Opslaan** als u klaar bent. 

     ![Gegevens van apparaatinschrijving invoeren in de portalblade](./media/python-quick-create-simulated-device/enterdevice-enrollment.png)  

   Als het apparaat is ingeschreven, wordt de *Registration ID* ervan weergegeven in de lijst onder het tabblad *Individual Enrollments*. 


## <a name="simulate-the-device"></a>Het apparaat simuleren

1. [Download en installeer Python 2.x of 3.x](https://www.python.org/downloads/). Zorg ervoor dat u de 32-bits of 64-bits installatie gebruikt, zoals vereist door uw configuratie. Zorg ervoor dat u Python toevoegt aan uw platformspecifieke omgevingsvariabelen als u hierom wordt gevraagd tijdens de installatie.
    - Als u een Windows-besturingssysteem hebt, gebruikt u vervolgens het [herdistribueerbare pakket van Visual C++](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads) om het gebruik van systeemeigen DLL's van Python mogelijk te maken.

1. Volg [deze instructies](https://github.com/Azure/azure-iot-sdk-python/blob/v1-deprecated/doc/python-devbox-setup.md) voor het bouwen van de Python-pakketten.

   > [!NOTE]
   > Als u `build_client.cmd` uitvoert, moet u ervoor zorgen dat u de vlag `--use-tpm-simulator` gebruikt.
   > 
   > [!NOTE]
   > Als u `pip` gebruikt, zorg dan ook dat u het pakket `azure-iot-provisioning-device-client` installeert. Houd er rekening mee dat de uitgebrachte PIP-pakketten van de echte TPM gebruikmaken, niet van de simulator. Als u de simulator wilt gebruiken, moet u vanuit de bron compileren met behulp van de vlag `--use-tpm-simulator`.

1. Navigeer naar de map met voorbeelden.

    ```cmd/sh
    cd azure-iot-sdk-python/provisioning_device_client/samples
    ```

1. Gebruik Python IDE om het Python-script met de naam **provisioning\_device\_client\_sample.py** te bewerken. Wijzig de variabelen *GLOBAL\_PROV\_URI* en *ID\_SCOPE* in de waarden die u eerder hebt genoteerd. Zorg er ook voor dat *SECURITY\_DEVICE\_TYPE* zijn ingesteld op `ProvisioningSecurityDeviceType.TPM`.

    ```python
    GLOBAL_PROV_URI = "{globalServiceEndpoint}"
    ID_SCOPE = "{idScope}"
    SECURITY_DEVICE_TYPE = ProvisioningSecurityDeviceType.TPM
    PROTOCOL = ProvisioningTransportProvider.HTTP
    ```

    ![Service-informatie](./media/python-quick-create-simulated-device/extract-dps-endpoints.png)

1. Voet het voorbeeld uit. 

    ```cmd/sh
    python provisioning_device_client_sample.py
    ```

1. De gegevens van uw IoT-hub leest u in de berichten die het opstarten van het apparaat en het verbinding maken met Device Provisioning Service simuleren. 

    ![Succesvolle registratie](./media/python-quick-create-simulated-device/registration-success.png)

1. Als het gesimuleerde apparaat is ingericht met de IoT-hub die is gekoppeld met de provisioning-service, wordt de apparaat-id weergegeven op de blade **IoT-apparaten** van de hub.

    ![Apparaat wordt geregistreerd voor de IoT-hub](./media/python-quick-create-simulated-device/hubregistration.png) 

    Als u de standaardwaarde van de *initiële status van de apparaatdubbel* hebt gewijzigd in de inschrijvingsvermelding voor uw apparaat, kan de gewenste status van de dubbel uit de hub worden gehaald en er dienovereenkomstig naar worden gehandeld. Zie [Understand and use device twins in IoT Hub](../iot-hub/iot-hub-devguide-device-twins.md) (Apparaatdubbelen begrijpen en gebruiken in IoT Hub) voor meer informatie


## <a name="clean-up-resources"></a>Resources opschonen

Als u wilt blijven doorwerken met het voorbeeld van de apparaatclient en deze beter wilt leren kennen, wis de resources die in deze quickstart zijn gemaakt dan niet. Als u niet wilt doorgaan, gebruikt u de volgende stappen om alle resources te verwijderen die via deze quickstart zijn gemaakt.

1. Sluit het uitvoervenster van het voorbeeld van de apparaatclient op de computer.
1. Sluit het TPM-simulatorvenster op de computer.
1. Selecteer in het linkermenu in Azure Portal **Alle resources** en selecteer uw Device Provisioning Service. Open de blade **Inschrijvingen beheren** voor uw service en selecteer vervolgens het tabblad **Individuele inschrijvingen**. Schakel het selectievakje naast de *Registratie-id* in van het apparaat dat u hebt ingeschreven in deze quickstart. Druk vervolgens op de knop **Verwijderen** bovenaan het deelvenster. 
1. Selecteer in het linkermenu in Azure Portal **Alle resources** en selecteer vervolgens uw IoT-hub. Open de blade **IoT-apparaten** voor uw hub, schakel het selectievakje *DEVICE ID* in van het apparaat dat u hebt geregistreerd in deze quickstart en druk vervolgens bovenaan op de knop **Verwijderen**.

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u een gesimuleerd TPM-apparaat op de computer gemaakt en dit ingericht voor uw IoT-hub met IoT Hub Device Provisioning Service. Als u wilt weten hoe u uw TPM-apparaat programmatisch kunt registreren, gaat u verder met de quickstart voor programmatische registratie van een TPM-apparaat. 

> [!div class="nextstepaction"]
> [Azure-quickstart: TPM-apparaat registreren bij Azure IoT Hub Device Provisioning Service](quick-enroll-device-tpm-python.md)