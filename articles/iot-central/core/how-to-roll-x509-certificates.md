---
title: X.509-certificaten in een Azure IoT Central
description: X.509-certificaten met uw toepassing IoT Central rollen
author: dominicbetts
ms.author: dobett
ms.date: 07/31/2020
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.openlocfilehash: c25af944b4c748307f4f974ca8616ecc9f7b28c3
ms.sourcegitcommit: 3ed0f0b1b66a741399dc59df2285546c66d1df38
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107714520"
---
# <a name="how-to-roll-x509-device-certificates-in-iot-central-application"></a>X.509-apparaatcertificaten in een toepassing IoT Central rollen

Tijdens de levenscyclus van uw IoT-oplossing moet u certificaten rollen. Twee van de belangrijkste redenen voor het rolling van certificaten zijn een beveiligingsschending en verlopen certificaten.

Als u een beveiligingsschending hebt, zijn rolling certificaten een beveiligingsrisico best practice uw systeem te helpen beveiligen. Als onderdeel van [de Methodologie Voor inbreuk aannemen](https://download.microsoft.com/download/C/1/9/C1990DBA-502F-4C2A-848D-392B93D9B9C3/Microsoft_Enterprise_Cloud_Red_Teaming.pdf)is het voor Microsoft belangrijk dat er reactieve beveiligingsprocessen en preventieve maatregelen worden genomen. Het rolling van uw apparaatcertificaten moet worden opgenomen als onderdeel van deze beveiligingsprocessen. De frequentie waarmee u uw certificaten rolleert, is afhankelijk van de beveiligingsbehoeften van uw oplossing. Klanten met oplossingen waarbij zeer gevoelige gegevens betrokken zijn, kunnen dagelijks certificaten rollen, terwijl andere hun certificaten om de paar jaar rollen.


## <a name="obtain-new-x509-certificates"></a>Nieuwe X.509-certificaten verkrijgen

U kunt uw eigen X.509-certificaten maken met behulp van een hulpprogramma zoals OpenSSL. Deze aanpak is zeer goed voor het testen van X.509-certificaten, maar biedt enkele garanties met het gebied van beveiliging. Gebruik deze methode alleen voor testen, tenzij u bent voorbereid om te fungeren als uw eigen CA-provider.

## <a name="enrollment-groups-and-security-breaches"></a>Registratiegroepen en beveiligingsschending

Als u een groepsinschrijving wilt bijwerken als reactie op een beveiligingsschending, moet u de volgende methode gebruiken om het huidige certificaat onmiddellijk bij te werken:

1. **Navigeer naar Beheer** in het linkerdeelvenster en selecteer **Apparaatverbinding.**

2. Selecteer **Inschrijvingsgroepen** en selecteer de groepsnaam in de lijst.

3. Selecteer voor certificaatupdate primaire **of secundaire** **beheren.**

4. Voeg het X.509-basiscertificaat toe aan en verifieer het in de registratiegroep.

   Voltooi deze stappen voor de primaire en secundaire certificaten, als beide zijn aangetast.

## <a name="enrollment-groups-and-certificate-expiration"></a>Verlopen van inschrijvingsgroepen en certificaten

Als u certificaten doorrolt om verlopen certificaten af te handelen, gebruikt u de volgende methode om het huidige certificaat onmiddellijk bij te werken:

1. **Navigeer naar Beheer** in het linkerdeelvenster en selecteer **Apparaatverbinding.**

2. Selecteer **Inschrijvingsgroepen** en selecteer de groepsnaam in de lijst.

3. Selecteer primaire beheren voor de **certificaatupdate.**

4. Voeg het X.509-basiscertificaat toe aan en verifieer het in de registratiegroep.

5. Later, wanneer het secundaire certificaat is verlopen, komt u terug en werkt u dat secundaire certificaat bij.

## <a name="individual-enrollments-and-security-breaches"></a>Afzonderlijke inschrijvingen en beveiligingsschending

Als u certificaten uitrolt als reactie op een beveiligingsschending, gebruikt u de volgende methode om het huidige certificaat onmiddellijk bij te werken:

1. Selecteer **Apparaten** en selecteer het apparaat.

2. Selecteer **Verbinding** maken en selecteer Verbindingsmethode als **Afzonderlijke inschrijving**

3. Selecteer **Certificaten (X.509)** als mechanisme.

    ![Afzonderlijke inschrijvingen beheren](./media/how-to-roll-x509-certificates/certificate-update.png)

4. Selecteer voor certificaatupdate het mappictogram om het nieuwe certificaat te selecteren dat moet worden geüpload voor de inschrijvingsinvoer. Selecteer **Opslaan**.

    Voltooi deze stappen voor de primaire en secundaire certificaten, als beide zijn aangetast

## <a name="individual-enrollments-and-certificate-expiration"></a>Afzonderlijke inschrijvingen en verlopen certificaten

Als u certificaten doorrolt voor het afhandelen van verlopen certificaten, moet u de configuratie van het secundaire certificaat als volgt gebruiken om downtime te verminderen voor apparaten die proberen in terichten.

Wanneer het secundaire certificaat bijna is verlopen en moet worden geroteerd, kunt u roteren naar met behulp van de primaire configuratie. Als u op deze manier tussen de primaire en secundaire certificaten wisselt, vermindert u de downtime voor apparaten die proberen in terichten.

1. Selecteer **Apparaten** en selecteer het apparaat.

2. Selecteer **Verbinding** maken en selecteer Verbindingsmethode als **Afzonderlijke inschrijving**

3. Selecteer **Certificaten (X.509)** als mechanisme.

    ![Afzonderlijke inschrijvingen beheren](./media/how-to-roll-x509-certificates/certificate-update.png)

4. Selecteer voor de update van het secundaire certificaat het mappictogram om het nieuwe certificaat te selecteren dat moet worden geüpload voor de inschrijvingsinvoer. Selecteer **Opslaan**.

5. Later, wanneer het primaire certificaat is verlopen, komt u terug en werkt u dat primaire certificaat bij.

## <a name="next-steps"></a>Volgende stappen

Nu u hebt geleerd hoe u X.509-certificaten in uw Azure IoT Central-toepassing kunt gebruiken, kunt u verbinding maken met [Azure IoT Central](concepts-get-connected.md).


