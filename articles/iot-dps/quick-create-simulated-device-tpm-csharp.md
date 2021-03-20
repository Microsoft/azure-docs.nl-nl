---
title: 'Quickstart: een gesimuleerd TPM-apparaat inrichten voor Azure IoT Hub met behulp van C#'
description: 'Quickstart: een gesimuleerd TPM-apparaat maken en inrichten met de C#-apparaat-SDK voor Azure IoT Hub Device Provisioning Service (DPS). In deze snelstart wordt gebruikgemaakt van afzonderlijke inschrijvingen.'
author: wesmc7777
ms.author: wesmc
ms.date: 11/08/2018
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
ms.custom: mvc
ms.openlocfilehash: b2648382fb19fafcfc342379aa9da974f6f8d1ff
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "90528416"
---
# <a name="quickstart-create-and-provision-a-simulated-tpm-device-using-c-device-sdk-for-iot-hub-device-provisioning-service"></a>Quickstart: Een gesimuleerd TPM-apparaat met de SDK voor C# maken en inrichten voor IoT Hub Device Provisioning Service

[!INCLUDE [iot-dps-selector-quick-create-simulated-device-tpm](../../includes/iot-dps-selector-quick-create-simulated-device-tpm.md)]

Deze stappen laten u zien hoe u de [Azure IoT-voorbeelden voor C# ](https://github.com/Azure-Samples/azure-iot-samples-csharp) gebruikt om een TPM-apparaat te simuleren op een ontwikkelcomputer met het Windows-besturingssysteem. In het voorbeeld wordt het gesimuleerde apparaat ook verbonden met een IoT Hub met behulp van een apparaatinrichtingsservice. 

De voorbeeldcode gebruikt de Windows TPM-simulator als de [HSM (Hardware Security Module)](https://azure.microsoft.com/blog/azure-iot-supports-new-security-hardware-to-strengthen-iot-security/) van het apparaat. 

Raadpleeg het overzicht [Inrichten](about-iot-dps.md#provisioning-process) als u niet bekend bent met het proces van automatisch inrichten. Controleer ook of u de stappen in [IoT Hub Device Provisioning Service instellen met Azure Portal](./quick-setup-auto-provision.md) hebt voltooid voordat u verdergaat. 

Azure IoT Device Provisioning Service ondersteunt twee typen inschrijvingen:

- [Inschrijvingsgroepen](concepts-service.md#enrollment-group): Wordt gebruikt om meerdere gerelateerde apparaten in te schrijven.
- [Afzonderlijke inschrijvingen](concepts-service.md#individual-enrollment): Wordt gebruikt om één apparaat in te schrijven.

In dit artikel worden afzonderlijke inschrijvingen gedemonstreerd.

[!INCLUDE [IoT Device Provisioning Service basic](../../includes/iot-dps-basic.md)]

<a id="setupdevbox"></a>
## <a name="prepare-the-development-environment"></a>De ontwikkelomgeving voorbereiden 

1. Controleer of de [.NET Core 2.1 SDK of hoger](https://www.microsoft.com/net/download/windows) op uw computer is geïnstalleerd. 

1. Zorg ervoor dat `git` op de computer wordt geïnstalleerd en toegevoegd aan de omgevingsvariabelen die voor het opdrachtvenster toegankelijk zijn. Zie [Software Freedom Conservancy's Git client tools](https://git-scm.com/download/) (Git-clienthulpprogramma's van Software Freedom Conservancy) om de nieuwste versie van `git`-hulpprogramma's te installeren, waaronder **Git Bash**, de opdrachtregel-app die u kunt gebruiken voor interactie met de lokale Git-opslagplaats. 

1. Open een opdrachtprompt of Git Bash. Kloon de Azure IoT-voorbeelden GitHub-opslagplaats voor C#:

    ```cmd
    git clone https://github.com/Azure-Samples/azure-iot-samples-csharp.git
    ```

## <a name="provision-the-simulated-device"></a>Het gesimuleerde apparaat inrichten

1. Meld u aan bij Azure Portal. Klik in het linkermenu op de knop **All resources** en open de Device Provisioning-service. Noteer in de blade **Overzicht** de waarde van het **_Id-bereik_**.

    ![Bereik-id van provisioning-service kopiëren van portal-blade](./media/quick-create-simulated-device-tpm-csharp/copy-scope.png) 

1. Ga op een opdrachtprompt naar de projectmap met de voorbeeldcode voor het inrichten van het de TPM-apparaat.

    ```cmd
    cd .\azure-iot-samples-csharp\provisioning\Samples\device\TpmSample
    ```

1. Typ de volgende opdracht om de voorbeeldcode voor het TPM-apparaat te bouwen en uit te voeren. Vervang de waarde voor `<IDScope>` door het id-bereik voor uw provisioning-service. 

    ```cmd
    dotnet run <IDScope>
    ```

    Met deze opdracht wordt de TPM-chip-simulator in een aparte opdrachtprompt gestart. In Windows kunt u een beveiligingswaarschuwing van Windows krijgen, waarin u wordt gevraagd of u Simulator.exe wilt toestaan om via openbare netwerken te communiceren. Voor de doeleinden van dit voorbeeld kunt u de aanvraag negeren.

1. In het oorspronkelijke opdrachtvenster ziet u waarden voor **_Goedkeuringssleutel_**, **_Registratie-id_** en een voorgestelde **_Apparaat-id_**. Deze waarden hebt u nodig voor registratie van het apparaat. Noteer deze waarden. U gebruikt deze waarde om een afzonderlijke inschrijving te maken in uw Device Provisioning Service-instantie. 
   > [!NOTE]
   > Verwar het venster met opdrachtuitvoer niet met het venster dat uitvoer van de TPM-simulator bevat. U moet mogelijk het opdrachtvenster selecteren om dit naar de voorgrond te halen.

    ![Uitvoer van het opdrachtvenster](./media/quick-create-simulated-device-tpm-csharp/output1.png) 

1. Selecteer in Azure Portal, in het Device Provisioning Service-menu, de optie **Inschrijvingen beheren**. Selecteer het tabblad **Individuele inschrijvingen** en selecteer vervolgens de knop **Individuele inschrijving toevoegen** bovenaan. 

1. Voer in het deelvenster **Inschrijving toevoegen** de volgende gegevens in:
   - Selecteer **TPM** als *mechanisme* voor identiteitscontrole.
   - Voer de *registratie-id* en *goedkeuringssleutel* voor het TPM-apparaat in aan de hand van de waarden die u eerder hebt genoteerd.
   - Selecteer een IoT-hub die is gekoppeld aan uw inrichtingsservice.
   - Desgewenst kunt u de volgende informatie verstrekken:
       - Voer een unieke *apparaat-id* in (u kunt de voorgestelde id gebruiken of een eigen id opgeven). Vermijd gevoelige gegevens bij het benoemen van uw apparaat. Als u ervoor kiest om er geen op te geven, wordt in plaats daarvan de registratie-id gebruikt om het apparaat te identificeren.
       - Werk de **initiële status van de apparaatdubbel** bij met de gewenste beginconfiguratie voor het apparaat.
   - Klik op de knop **Opslaan** als u klaar bent. 

     ![Gegevens van apparaatinschrijving invoeren in de portalblade](./media/quick-create-simulated-device-tpm-csharp/enterdevice-enrollment.png)  

   Als het apparaat is ingeschreven, wordt de *Registration ID* ervan weergegeven in de lijst onder het tabblad *Individual Enrollments*. 

1. Druk op *Enter* in het opdrachtvenster (waarin de **_goedkeuringssleutel_**, de **_registratie-id_** en een voorgestelde **_apparaat-id_** worden weergegeven) om het gesimuleerde apparaat te registreren. De gegevens van uw IoT-hub leest u in de berichten die het opstarten van het apparaat en het verbinding maken met Device Provisioning Service simuleren. 

1. Controleer of het apparaat is ingericht. Als het gesimuleerde apparaat is ingericht met de IoT-hub die is gekoppeld met de provisioning-service, wordt de apparaat-id weergegeven op de blade **IoT-apparaten** van de hub. 

    ![Apparaat wordt geregistreerd voor de IoT-hub](./media/quick-create-simulated-device-tpm-csharp/hub_registration.png) 

    Als u de standaardwaarde van de *initiële status van de apparaatdubbel* hebt gewijzigd in de inschrijvingsvermelding voor uw apparaat, kan de gewenste status van de dubbel uit de hub worden gehaald en er dienovereenkomstig naar worden gehandeld. Zie [Apparaatdubbels begrijpen en gebruiken in IoT Hub](../iot-hub/iot-hub-devguide-device-twins.md) voor meer informatie.

## <a name="clean-up-resources"></a>Resources opschonen

Als u wilt blijven doorwerken met het voorbeeld van de apparaatclient en deze beter wilt leren kennen, wis de resources die in deze quickstart zijn gemaakt dan niet. Als u niet wilt doorgaan, gebruikt u de volgende stappen om alle resources te verwijderen die via deze quickstart zijn gemaakt.

1. Sluit het uitvoervenster van het voorbeeld van de apparaatclient op de computer.
1. Sluit het TPM-simulatorvenster op de computer.
1. Selecteer in het linkermenu in Azure Portal **Alle resources** en selecteer uw Device Provisioning Service. Klik bovenaan de blade **Overzicht** op **Verwijderen** bovenaan het deelvenster.  
1. Selecteer in het linkermenu in Azure Portal **Alle resources** en selecteer vervolgens uw IoT-hub. Klik bovenaan de blade **Overzicht** op **Verwijderen** bovenaan het deelvenster.  

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u een gesimuleerd TPM-apparaat op de computer gemaakt en dit ingericht voor uw IoT-hub met IoT Hub Device Provisioning Service. Als u wilt weten hoe u uw TPM-apparaat programmatisch kunt registreren, gaat u verder met de quickstart voor programmatische registratie van een TPM-apparaat. 

> [!div class="nextstepaction"]
> [Azure-quickstart: TPM-apparaat registreren bij Azure IoT Hub Device Provisioning Service](quick-enroll-device-tpm-csharp.md)
