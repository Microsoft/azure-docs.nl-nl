---
title: CA-certificaat keten van vertrouwde client exporteren voor client verificatie
titleSuffix: Azure Application Gateway
description: Meer informatie over het exporteren van een CA-certificaat keten voor een vertrouwde client voor client verificatie op Azure-toepassing gateway
services: application-gateway
author: mscatyao
ms.service: application-gateway
ms.topic: how-to
ms.date: 03/31/2021
ms.author: caya
ms.openlocfilehash: 2329dc7426b223ef2c81dd0e2e607bccf73192e6
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/02/2021
ms.locfileid: "106231457"
---
# <a name="export-a-trusted-client-ca-certificate-chain-to-use-with-client-authentication"></a>Een CA-certificaat keten voor een vertrouwde client exporteren voor gebruik met client verificatie
Als u wederzijdse verificatie wilt configureren met de client of client verificatie, moet Application Gateway een vertrouwde CA-certificaat keten voor de client uploaden naar de gateway. Als u meerdere certificaat ketens hebt, moet u de ketens afzonderlijk maken en deze als verschillende bestanden uploaden op de Application Gateway. In dit artikel wordt beschreven hoe u een vertrouwde client CA-certificaat keten exporteert die u kunt gebruiken in uw client verificatie configuratie op uw gateway.  

## <a name="prerequisites"></a>Vereisten

Een bestaand client certificaat is vereist voor het genereren van de CA-certificaat keten van de vertrouwde client. 

## <a name="export-trusted-client-ca-certificate"></a>CA-certificaat van vertrouwde client exporteren

Er is een vertrouwd client-CA-certificaat vereist om client authenticatie op Application Gateway toe te staan. In dit voor beeld gebruiken we een TLS/SSL-certificaat voor het client certificaat, exporteert u de open bare sleutel en exporteert u vervolgens de CA-certificaten uit de open bare sleutel om de certificaten van de vertrouwde client CA op te halen. Vervolgens worden alle client CERTIFICERINGs certificaten samengevoegd tot één CA-certificaat keten van een vertrouwde client. 

Met de volgende stappen kunt u het. pem-of CER-bestand voor uw certificaat exporteren:

### <a name="export-public-certificate"></a>Openbaar certificaat exporteren 

1. Als u een CER-bestand wilt genereren van het certificaat, opent u **Gebruikerscertificaten beheren**. Zoek het certificaat, meestal in ' certificaten-Current Gebruiker\persoonlijk\certificaten ' en klik met de rechter muisknop op. Klik op **Alle taken** en vervolgens op **Exporteren**. Hiermee opent u de **Wizard Certificaat exporteren**. Als u het certificaat niet kunt vinden onder de huidige Gebruiker\persoonlijk\certificaten, hebt u mogelijk per ongeluk ' certificaten-lokale computer ' geopend, in plaats van ' certificaten-huidige gebruiker '). Als u certificaat beheer wilt openen in het huidige gebruikers bereik met behulp van Power shell, typt u *certmgr* in het console venster.

    > [!div class="mx-imgBorder"]
    > ![Scherm afbeelding toont de certificaat beheerder waarbij certificaten zijn geselecteerd en een context menu met alle taken. vervolgens selecteert u exporteren.](./media/certificates-for-backend-authentication/export.png)

2. Klik in de wizard op **volgende**.
    > [!div class="mx-imgBorder"]
    > ![Certificaat exporteren](./media/certificates-for-backend-authentication/exportwizard.png)

3. Selecteer **Nee, de persoonlijke sleutel niet exporteren** en klik vervolgens op **Volgende**.
    > [!div class="mx-imgBorder"]
    > ![De persoonlijke sleutel niet exporteren](./media/certificates-for-backend-authentication/notprivatekey.png)

4. Selecteer op de pagina **Bestandsindeling voor export** de optie **Met Base64 gecodeerde X.509 (*.CER)** en klik op **Volgende**.
    > [!div class="mx-imgBorder"]
    > ![Base-64-code ring](./media/certificates-for-backend-authentication/base64.png)

5. Als u het **bestand wilt exporteren**, **bladert** u naar de locatie waarnaar u het certificaat wilt exporteren. Geef bij **Bestandsnaam** de naam van het certificaatbestand op. Klik op **Volgende**.

    > [!div class="mx-imgBorder"]
   > ![Scherm afbeelding toont de wizard Certificaat exporteren waarin u een bestand opgeeft dat u wilt exporteren.](./media/certificates-for-backend-authentication/browse.png)

6. Klik op **Voltooien** om het certificaat te exporteren.

    > [!div class="mx-imgBorder"]
    > ![Scherm afbeelding toont de wizard Certificaat exporteren nadat u het bestand hebt geëxporteerd.](./media/certificates-for-backend-authentication/finish.png)

7. Het certificaat is geëxporteerd.

    > [!div class="mx-imgBorder"]
    > ![Scherm afbeelding toont de wizard Certificaat exporteren met een geslaagd bericht.](./media/certificates-for-backend-authentication/success.png)

   Het geëxporteerde certificaat ziet er ongeveer als volgt uit:

    > [!div class="mx-imgBorder"]
    > ![Scherm afbeelding toont een certificaat symbool.](./media/certificates-for-backend-authentication/exported.png)

### <a name="export-ca-certificates-from-the-public-certificate"></a>CA-certificaat (en) uit het open bare certificaat exporteren

Nu u uw open bare certificaat hebt geëxporteerd, exporteert u nu de CA-certificaten uit uw open bare certificaat. Als u alleen een basis-CA hebt, hoeft u alleen dat certificaat te exporteren. Als u echter 1 + tussenliggende Ca's hebt, moet u deze ook exporteren. 

1. Zodra de open bare sleutel is geëxporteerd, opent u het bestand.

    > [!div class="mx-imgBorder"]
    > ![Autorisatie certificaat openen](./media/certificates-for-backend-authentication/openAuthcert.png)

    > [!div class="mx-imgBorder"]
    > ![over certificaat](./media/mutual-authentication-certificate-management/general.png)

1. Selecteer het tabblad Certificeringspad om de certificerings instantie weer te geven.

    > [!div class="mx-imgBorder"]
    > ![certificaat Details](./media/mutual-authentication-certificate-management/cert-details.png) 

1. Selecteer het basis certificaat en klik op **certificaat weer geven**.

    > [!div class="mx-imgBorder"]
    > ![certificaatpad](./media/mutual-authentication-certificate-management/root-cert.png) 

   De details van het basis certificaat moeten worden weer geven.

    > [!div class="mx-imgBorder"]
    > ![certificaat gegevens](./media/mutual-authentication-certificate-management/root-cert-details.png)

1. Selecteer het tabblad **Details** en klik op **kopiëren naar bestand...**

    > [!div class="mx-imgBorder"]
    > ![basis certificaat kopiëren](./media/mutual-authentication-certificate-management/root-cert-copy-to-file.png)

1. Op dit moment hebt u de details van het basis-CA-certificaat uit het open bare certificaat geëxtraheerd. U ziet de **wizard Certificaat exporteren**. Volg stap 2-7 van de vorige sectie ([openbaar certificaat exporteren](./mutual-authentication-certificate-management.md#export-public-certificate)) om de wizard Certificaat exporteren te volt ooien. 

1. Herhaal nu stap 2-6 van deze huidige sectie ([CA-certificaat (en) van het open bare certificaat exporteren](./mutual-authentication-certificate-management.md#export-ca-certificates-from-the-public-certificate)) voor alle tussenliggende certificerings instanties om alle tussenliggende CA-certificaten te exporteren in de met Base-64 gecodeerde X. 509 (. CER)-indeling.

    > [!div class="mx-imgBorder"]
    > ![tussenliggend certificaat](./media/mutual-authentication-certificate-management/intermediate-cert.png)

    Zo herhaalt u stap 2-6 van deze sectie op de *MSIT CAZ2* tussenliggende CA om deze uit te pakken als een eigen certificaat. 

### <a name="concatenate-all-your-ca-certificates-into-one-file"></a>Al uw CA-certificaten samen voegen in één bestand

1. Voer de volgende opdracht uit met alle CA-certificaten die u eerder hebt opgehaald. 

    Windows:
    ```console
    type intermediateCA.cer rootCA.cer > combined.cer
    ```
    
    Linux:
    ```console
    cat intermediateCA.cer rootCA.cer >> combined.cer
    ```

    Het resulterende gecombineerde certificaat moet er ongeveer als volgt uitzien:
    
    > [!div class="mx-imgBorder"]
    > ![gecombineerd certificaat](./media/mutual-authentication-certificate-management/combined-cert.png)

## <a name="next-steps"></a>Volgende stappen

U hebt nu de CA-certificaat keten van een vertrouwde client. U kunt dit toevoegen aan de configuratie van de client verificatie op de Application Gateway om wederzijdse verificatie met uw gateway toe te staan. Zie [wederzijdse verificatie configureren met Application Gateway met portal](./mutual-authentication-portal.md) of [wederzijdse verificatie met behulp van Application Gateway configureren met Power shell](./mutual-authentication-powershell.md).

