---
title: Vereiste certificaten voor het toestaan van back-endservers
titleSuffix: Azure Application Gateway
description: In dit artikel vindt u voor beelden van hoe een TLS/SSL-certificaat kan worden geconverteerd naar een verificatie certificaat en een vertrouwd basis certificaat dat vereist is voor het toestaan van back-end-instanties in Azure-toepassing gateway
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: how-to
ms.date: 06/17/2020
ms.author: absha
ms.openlocfilehash: 874e554063f64ddefce99a223678d64b2e0774c3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "93397719"
---
# <a name="create-certificates-to-allow-the-backend-with-azure-application-gateway"></a>Certificaten maken om de back-end toe te staan voor Azure Application Gateway

Als u end-to-end TLS wilt uitvoeren, moeten Application Gateway de back-end-instanties worden toegestaan door verificatie/vertrouwde basis certificaten te uploaden. Voor de V1-SKU zijn verificatie certificaten vereist, maar voor de v2 SKU vertrouwde basis certificaten zijn vereist om de certificaten toe te staan.

In dit artikel leert u het volgende:


- Verificatie certificaat exporteren vanuit een back-end-certificaat (voor v1-SKU)
- Een vertrouwd basis certificaat exporteren vanuit een back-end-certificaat (voor v2 SKU)

## <a name="prerequisites"></a>Vereisten

Er is een bestaand back-end-certificaat vereist voor het genereren van de verificatie certificaten of vertrouwde basis certificaten die vereist zijn voor het toestaan van back-end-exemplaren met Application Gateway. Het back-end-certificaat kan hetzelfde zijn als het TLS/SSL-certificaat of anders voor extra beveiliging. Application Gateway biedt geen enkel mechanisme voor het maken of kopen van een TLS/SSL-certificaat. Voor test doeleinden kunt u een zelfondertekend certificaat maken, maar u moet het niet gebruiken voor productie werkbelastingen. 

## <a name="export-authentication-certificate-for-v1-sku"></a>Verificatie certificaat exporteren (voor v1-SKU)

Een verificatie certificaat is vereist voor het toestaan van back-end-exemplaren in Application Gateway v1-SKU. Het verificatie certificaat is de open bare sleutel van back-endserver certificaten in base-64 Encoded X. 509 (. CER)-indeling. In dit voor beeld gebruikt u een TLS/SSL-certificaat voor het back-end-certificaat en exporteert u de open bare sleutel voor gebruik als verificatie-certificering. In dit voor beeld maakt u ook gebruik van het hulp programma Windows Certificate Manager voor het exporteren van de vereiste certificaten. U kunt ervoor kiezen om elk ander hulp programma te gebruiken dat handig is.

Exporteer vanuit uw TLS/SSL-certificaat het bestand met de open bare sleutel. CER (niet de persoonlijke sleutel). De volgende stappen helpen u bij het exporteren van het CER-bestand in base-64 Encoded X. 509 (. CER) voor uw certificaat:

1. Als u een CER-bestand wilt genereren van het certificaat, opent u **Gebruikerscertificaten beheren**. Zoek het certificaat, meestal in ' certificaten-Current Gebruiker\persoonlijk\certificaten ' en klik met de rechter muisknop op. Klik op **Alle taken** en vervolgens op **Exporteren**. Hiermee opent u de **Wizard Certificaat exporteren**. Als u het certificaat niet kunt vinden onder de huidige Gebruiker\persoonlijk\certificaten, hebt u mogelijk per ongeluk ' certificaten-lokale computer ' geopend, in plaats van ' certificaten-huidige gebruiker '). Als u certificaat beheer wilt openen in het huidige gebruikers bereik met behulp van Power shell, typt u *certmgr* in het console venster.

   ![Scherm afbeelding toont de certificaat beheerder waarbij certificaten zijn geselecteerd en een context menu met alle taken. vervolgens selecteert u exporteren.](./media/certificates-for-backend-authentication/export.png)

2. Klik in de wizard op **volgende**.

   ![Certificaat exporteren](./media/certificates-for-backend-authentication/exportwizard.png)

3. Selecteer **Nee, de persoonlijke sleutel niet exporteren** en klik vervolgens op **Volgende**.

   ![De persoonlijke sleutel niet exporteren](./media/certificates-for-backend-authentication/notprivatekey.png)

4. Selecteer op de pagina **Bestandsindeling voor export** de optie **Met Base64 gecodeerde X.509 (*.CER)** en klik op **Volgende**.

   ![Base-64-code ring](./media/certificates-for-backend-authentication/base64.png)

5. Als u het **bestand wilt exporteren**, **bladert** u naar de locatie waarnaar u het certificaat wilt exporteren. Geef bij **Bestandsnaam** de naam van het certificaatbestand op. Klik op **Volgende**.

   ![Scherm afbeelding toont de wizard Certificaat exporteren waarin u een bestand opgeeft dat u wilt exporteren.](./media/certificates-for-backend-authentication/browse.png)

6. Klik op **Voltooien** om het certificaat te exporteren.

   ![Scherm afbeelding toont de wizard Certificaat exporteren nadat u het bestand hebt geëxporteerd.](./media/certificates-for-backend-authentication/finish.png)

7. Het certificaat is geëxporteerd.

   ![Scherm afbeelding toont de wizard Certificaat exporteren met een geslaagd bericht.](./media/certificates-for-backend-authentication/success.png)

   Het geëxporteerde certificaat ziet er ongeveer als volgt uit:

   ![Scherm afbeelding toont een certificaat symbool.](./media/certificates-for-backend-authentication/exported.png)

8. Als u het geëxporteerde certificaat met Klad blok opent, ziet u iets dat vergelijkbaar is met dit voor beeld. De sectie in Blue bevat de informatie die wordt geüpload naar Application Gateway. Als u uw certificaat opent met Klad blok en dit niet lijkt op dit soort, betekent dit meestal dat u dit niet hebt geëxporteerd met de base-64 Encoded X. 509 (. CER)-indeling. Daarnaast kunt u, als u een andere tekst editor wilt gebruiken, begrijpen dat sommige editors onbedoelde opmaak op de achtergrond kunnen introduceren. Dit kan problemen veroorzaken bij het uploaden van de tekst van dit certificaat naar Azure.

   ![Openen met Klad blok](./media/certificates-for-backend-authentication/format.png)

## <a name="export-trusted-root-certificate-for-v2-sku"></a>Vertrouwd basis certificaat exporteren (voor v2-SKU)

Het vertrouwde basis certificaat is vereist voor het toestaan van back-end-instanties in de app Application Gateway v2 SKU. Het basis certificaat is een base-64 Encoded X. 509 (. CER) basis certificaat van de back-end-server certificaten. In dit voor beeld gebruiken we een TLS/SSL-certificaat voor het back-end-certificaat, exporteert u de open bare sleutel en exporteert u vervolgens het basis certificaat van de vertrouwde certificerings instantie uit de open bare sleutel met base64-gecodeerde indeling om het vertrouwde basis certificaat te verkrijgen. De tussenliggende certificaten moeten worden gebundeld met het server certificaat en moeten worden geïnstalleerd op de back-endserver.

Met de volgende stappen kunt u het. cer-bestand voor uw certificaat exporteren:

1. Gebruik de stappen 1-8 vermeld in de vorige sectie [verificatie certificaat exporteren (voor v1 SKU)](#export-authentication-certificate-for-v1-sku) om de open bare sleutel te exporteren vanuit uw back-end-certificaat.

2. Zodra de open bare sleutel is geëxporteerd, opent u het bestand.

   ![Autorisatie certificaat openen](./media/certificates-for-backend-authentication/openAuthcert.png)

   ![over certificaat](./media/certificates-for-backend-authentication/general.png)

3. Ga naar de weer gave certificeringspad om de certificerings instantie weer te geven.

   ![certificaat Details](./media/certificates-for-backend-authentication/certdetails.png)

4. Selecteer het basis certificaat en klik op **certificaat weer geven**.

   ![certificaatpad](./media/certificates-for-backend-authentication/rootcert.png)

   De details van het basis certificaat moeten worden weer geven.

   ![certificaat gegevens](./media/certificates-for-backend-authentication/rootcertdetails.png)

5. Ga naar de weer gave **Details** en klik op **kopiëren naar bestand...**

   ![basis certificaat kopiëren](./media/certificates-for-backend-authentication/rootcertcopytofile.png)

6. Op dit moment hebt u de details van het basis certificaat geëxtraheerd van het back-end-certificaat. U ziet de **wizard Certificaat exporteren**. Gebruik nu stap 2-9 vermeld in de sectie **verificatie certificaat exporteren van een back-end-certificaat (voor v1 sku's)** om het vertrouwde basis certificaat te exporteren in de met Base-64 gecodeerde X. 509 (. CER)-indeling.

## <a name="next-steps"></a>Volgende stappen

U hebt nu het verificatie certificaat/het vertrouwde basis certificaat in base-64 Encoded X. 509 (. CER)-indeling. U kunt dit toevoegen aan de toepassings gateway om uw back-endservers toe te staan voor end-to-end TLS-versleuteling. Zie [end-to-end TLS configureren met behulp van Application Gateway met Power shell](./application-gateway-end-to-end-ssl-powershell.md).