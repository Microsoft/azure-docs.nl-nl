---
title: 'Zelfstudie: apparaat inrichten met behulp van Azure IoT Hub Device Provisioning Service'
description: In deze zelfstudie leert u hoe u uw apparaat kunt inrichten in één IoT-hub met behulp van Azure IoT Hub Device Provisioning Service (DPS)
author: wesmc7777
ms.author: wesmc
ms.date: 11/12/2019
ms.topic: tutorial
ms.service: iot-dps
services: iot-dps
ms.custom: mvc
ms.openlocfilehash: 876fd8260b64fba4d3d34a766b4259323c660b76
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94968074"
---
# <a name="tutorial-provision-the-device-to-an-iot-hub-using-the-azure-iot-hub-device-provisioning-service"></a>Zelfstudie: Het apparaat inrichten in een IoT-hub met behulp van IoT Hub Device Provisioning Service

In de vorige zelfstudie hebt u geleerd hoe u een apparaat kunt instellen om verbinding te maken met Device Provisioning Service. In deze zelfstudie leert u hoe u deze service gebruikt om uw apparaat in te richten voor één IoT-hub. U gebruikt daarvoor automatische inrichting **_inschrijvingslijsten_**. In deze zelfstudie ontdekt u hoe u:

> [!div class="checklist"]
> * Het apparaat inschrijven
> * Het apparaat starten
> * Controleren of het apparaat is geregistreerd

## <a name="prerequisites"></a>Vereisten

Voordat u doorgaat, moet uw apparaat zijn geconfigureerd zoals is besproken in de zelfstudie [Een apparaat instellen voor inrichten met behulp van Azure IoT Hub Device Provisioning Service](./tutorial-set-up-device.md).

Als u niet bekend bent met het proces van automatische inrichting, bekijkt u het overzicht voor [inrichting](about-iot-dps.md#provisioning-process) voordat u verdergaat.

<a id="enrolldevice"></a>
## <a name="enroll-the-device"></a>Het apparaat inschrijven

In deze stap worden de unieke beveiligingsartefacten van het apparaat toegevoegd aan Device Provisioning Service. Deze beveiligingsartefacten zijn als volgt gebaseerd op het [Attestation-mechanisme](concepts-service.md#attestation-mechanism) van het apparaat:

- Voor TPM-apparaten hebt u het volgende nodig:
    - De *Goedkeuringssleutel* die uniek is voor elke TPM-chip of -simulatie en kan worden verkregen bij de TPM-chipfabrikant.  Lees [TPM-goedkeuringssleutel begrijpen](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc770443(v=ws.11)) voor meer informatie.
    - De *Registratie-id* die wordt gebruikt om een apparaat op unieke wijze te identificeren in de naamruimte of het bereik. Deze id is al dan niet dezelfde id als van het apparaat. De id is verplicht voor elk apparaat. Voor TPM-apparaten kan de registratie-id worden afgeleid van de TPM zelf, bijvoorbeeld een SHA-256-hash van de TPM-goedkeuringssleutel.

      [![Inschrijvingsgegevens voor TPM in de portal](./media/tutorial-provision-device-to-hub/tpm-device-enrollment.png)](./media/tutorial-provision-device-to-hub/tpm-device-enrollment.png#lightbox)  

- Voor X.509-apparaten hebt u het volgende nodig:
    - Het [certificaat dat is verleend aan de X.509](/windows/win32/seccertenroll/about-x-509-public-key-certificates)-chip of -simulatie, in de vorm van een *.pem*- of *.cer*-bestand. Voor een afzonderlijke inschrijving moet u het *ondertekende certificaat* per apparaat voor het X.509-systeem gebruiken, maar voor het inschrijven van groepen, moet u het *hoofdcertificaat* gebruiken. 

      [![Afzonderlijke inschrijving voor X.509-attestation toevoegen in de portal](./media/tutorial-provision-device-to-hub/individual-enrollment.png)](./media/tutorial-provision-device-to-hub/individual-enrollment.png#lightbox)

Er zijn twee manieren om het apparaat te registreren bij Device Provisioning Service:

- **Inschrijvingsgroepen**: dit is een groep apparaten die een specifiek attestation-mechanisme delen. We raden u aan om een inschrijvingsgroep te gebruiken bij een groot aantal apparaten die een gewenste initiële configuratie delen, of voor apparaten die allemaal zijn verbonden met dezelfde tenant. Raadpleeg [Beveiliging](concepts-x509-attestation.md#controlling-device-access-to-the-provisioning-service-with-x509-certificates) voor meer informatie over identiteit-attestation voor registratiegroepen.

    [![Groepsinschrijving voor attestation X.509 toevoegen in de portal](./media/tutorial-provision-device-to-hub/group-enrollment.png)](./media/tutorial-provision-device-to-hub/group-enrollment.png#lightbox)

- **Afzonderlijke inschrijvingen**: dit is een vermelding voor een enkel apparaat dat kan worden geregistreerd met Device Provisioning Service. Afzonderlijke inschrijvingen kunnen X509-certificaten of SAS-tokens (in een echt of virtueel TPM) gebruiken als attestation-mechanismen. We raden u aan om afzonderlijke inschrijvingen te gebruiken bij apparaten die unieke initiële configuraties vereisen, en voor apparaten die alleen SAS-tokens via TPM of virtuele TPM kunnen gebruiken als het attestation-mechanisme. Afzonderlijke inschrijvingen hebben mogelijk de gewenste apparaat-id voor IoT Hub die is opgegeven.

Nu schrijft u het apparaat in met uw Device Provisioning Service-instantie, met behulp van de vereiste beveiligingsartefacten op basis van het attestation-mechanisme van het apparaat: 

1. Meld u aan bij Azure Portal, klik in het linkermenu op de knop **Alle bronnen** en open Device Provisioning Service.

2. Selecteer **Manage enrollments** in de overzichtsblade van Device Provisioning Service. Selecteer het tabblad **Afzonderlijke inschrijvingen** of het tabblad **Inschrijvingsgroepen** afhankelijk van de gekozen installatie voor het apparaat. Klik bovenaan op de knop **Toevoegen**. Selecteer **TPM** of **X.509** als het attestation-*mechanisme*, en voer de juiste beveiligingsartefacten toe, zoals eerder besproken. U kunt een nieuwe **apparaat-id voor IoT Hub** invoeren. Klik op de knop **Save** als u klaar bent. 

3. Wanneer het apparaat is ingeschreven, ziet u dat deze in de portal als volgt wordt weergegeven:

    ![Geslaagde TPM-inschrijving in de portal](./media/tutorial-provision-device-to-hub/tpm-enrollment-success.png)

Na de inschrijving wacht de inrichtingsservice tot het apparaat op enig moment later is opgestart en verbonden met de service. Als het apparaat voor de eerste keer wordt opgestart, communiceert de SDK-clientbibliotheek met uw chip om de beveiligingsartefacten te extraheren uit het apparaat, en wordt de registratie bij Device Provisioning Service geverifieerd. 

## <a name="start-the-iot-device"></a>Het IoT-apparaat starten

Uw IoT-apparaat kan een echt apparaat of een gesimuleerd apparaat zijn. Aangezien het IoT-apparaat nu is geregistreerd met een Device Provisioning Service-instantie, kan het apparaat opstarten en de inrichtingsservice aanroepen om te worden herkend met het attestation-mechanisme. Nadat de inrichtingsservice het apparaat heeft herkend, wordt het apparaat toegewezen aan een IoT-hub. 

Voorbeelden van gesimuleerde apparaten die zowel TPM als X.509-attestation gebruiken, zijn opgenomen voor C, Java, C#, Node.js en Python. Een gesimuleerd apparaat dat TMP en de [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) gebruikt, volgt bijvoorbeeld het proces dat in de sectie [Eerste opstartvolgorde voor het apparaat simuleren](quick-create-simulated-device.md#simulate-first-boot-sequence-for-the-device) wordt beschreven. Hetzelfde apparaat dat voor attestation een X.509-certificaat gebruikt, verwijst naar deze [opstartvolgordesectie](quick-create-simulated-device-x509.md#simulate-first-boot-sequence-for-the-device).

Raadpleeg [How-to guide for the MXChip Iot DevKit](how-to-connect-mxchip-iot-devkit.md) (Handleiding voor de MXChip IoT DevKit) voor een voorbeeld van een echt apparaat.

Start het apparaat zodat de registratie via de clienttoepassing van uw apparaat met Device Provisioning Service kan worden gestart.  

## <a name="verify-the-device-is-registered"></a>Controleren of het apparaat is geregistreerd

Nadat het apparaat is opgestart, worden de volgende acties uitgevoerd:

1. Vanaf het apparaat wordt een registratie-aanvraag verzonden naar Device Provisioning Service.
2. Voor TPM-apparaten wordt vanuit Device Provisioning Service een registratie-uitdaging teruggestuurd waarop het apparaat reageert. 
3. Als de registratie is gelukt, worden Device Provisioning Service de IoT Hub-URI, apparaat-id en versleutelingssleutel via Device Provisioning Service teruggestuurd naar het apparaat. 
4. De IoT Hub-clienttoepassing op het apparaat maakt vervolgens verbinding met de hub. 
5. Als de verbinding met de hub is gemaakt, wordt het apparaat weergegeven in de **IoT Device Explorer** van de IoT-hub. 

    ![Geslaagde verbinding met de hub in de portal](./media/tutorial-provision-device-to-hub/hub-connect-success.png)

Zie het voorbeeld voor het inrichten van de apparaatclient, [prov_dev_client_sample.c](https://github.com/Azure/azure-iot-sdk-c/blob/master/provisioning_client/samples/prov_dev_client_sample/prov_dev_client_sample.c), voor meer informatie. Het voorbeeld demonstreert het inrichten van een gesimuleerd apparaat met TPM, X.509-certificaten en symmetrische sleutels. Raadpleeg de quickstarts voor attestation van [TPM](./quick-create-simulated-device.md), [X.509](./quick-create-simulated-device-x509.md) en [Symmetrische sleutels](./quick-create-simulated-device-symm-key.md) voor stapsgewijze aanwijzingen voor het gebruik van het voorbeeld.

## <a name="next-steps"></a>Volgende stappen
In deze zelfstudie heeft u het volgende geleerd:

> [!div class="checklist"]
> * Het apparaat inschrijven
> * Het apparaat starten
> * Controleren of het apparaat is geregistreerd

Ga naar de volgende zelfstudie voor informatie over het inrichten van meerdere apparaten via hubs met gelijke taakverdeling. 

> [!div class="nextstepaction"]
> [Apparaten inrichten in meerdere IoT-hubs met gelijke taakverdeling](./tutorial-provision-multiple-hubs.md)