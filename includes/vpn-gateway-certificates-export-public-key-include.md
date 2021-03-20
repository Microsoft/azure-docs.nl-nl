---
title: bestand opnemen
description: bestand opnemen
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/19/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 12e9bec0c560f1b068b07a1b6afe218a112e439f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94553078"
---
Nadat u een zelfondertekend basis certificaat hebt gemaakt, exporteert u het bestand met de open bare sleutel van het basis certificaat (niet de persoonlijke sleutel). U kunt dit bestand later uploaden naar Azure. Met de volgende stappen kunt u het. cer-bestand voor uw zelfondertekende basis certificaat exporteren:

1. Als u een CER-bestand wilt genereren van het certificaat, opent u **Gebruikerscertificaten beheren**. Zoek het zelfondertekende basiscertificaat, meestal in 'Certificaten - Huidige gebruiker\Persoonlijk\Certificaten', en klik er met de rechtermuisknop op. Klik op **Alle taken** en vervolgens op **Exporteren**. Hiermee opent u de **Wizard Certificaat exporteren**. Als u het certificaat niet kunt vinden onder de huidige Gebruiker\persoonlijk\certificaten, hebt u mogelijk per ongeluk ' certificaten-lokale computer ' geopend, in plaats van ' certificaten-huidige gebruiker '). Als u certificaat beheer wilt openen in het huidige gebruikers bereik met behulp van Power shell, typt u *certmgr* in het console venster.

   ![Scherm afbeelding toont het venster certificaten voor de huidige gebruiker waarbij certificaten zijn geselecteerd en een context menu met export geselecteerd uit alle taken.](./media/vpn-gateway-certificates-export-public-key-include/export.png)
2. Klik in de wizard op **volgende**.

   ![Certificaat exporteren](./media/vpn-gateway-certificates-export-public-key-include/exportwizard.png)
3. Selecteer **Nee, de persoonlijke sleutel niet exporteren** en klik vervolgens op **Volgende**.

   ![De persoonlijke sleutel niet exporteren](./media/vpn-gateway-certificates-export-public-key-include/notprivatekey.png)
4. Selecteer op de pagina **Bestandsindeling voor export** de optie **Met Base64 gecodeerde X.509 (*.CER)** en klik op **Volgende**.

   ![Base-64-code ring](./media/vpn-gateway-certificates-export-public-key-include/base64.png)
5. Als u het **bestand wilt exporteren**, **bladert** u naar de locatie waarnaar u het certificaat wilt exporteren. Geef bij **Bestandsnaam** de naam van het certificaatbestand op. Klik op **Volgende**.

   ![Scherm afbeelding toont de wizard Certificaat exporteren met het tekstvak bestands naam en een Blader optie.](./media/vpn-gateway-certificates-export-public-key-include/browse.png)
6. Klik op **Voltooien** om het certificaat te exporteren.

   ![Scherm afbeelding toont de wizard Certificaat exporteren met de geselecteerde instellingen.](./media/vpn-gateway-certificates-export-public-key-include/finish.png)
7. Het certificaat is geëxporteerd.

   ![Scherm afbeelding toont een bericht dat de export is geslaagd.](./media/vpn-gateway-certificates-export-public-key-include/success.png)
8. Het geëxporteerde certificaat ziet er ongeveer als volgt uit:

   ![Scherm afbeelding toont een certificaat pictogram en een bestands naam met de bestandsnaam extensie c e r.](./media/vpn-gateway-certificates-export-public-key-include/exported.png)
9. Als u het geëxporteerde certificaat met Klad blok opent, ziet u iets dat vergelijkbaar is met dit voor beeld. De sectie in Blue bevat de informatie die wordt geüpload naar Azure. Als u uw certificaat opent met Klad blok en dit niet lijkt op deze soort, betekent dit dat u dit niet hebt geëxporteerd met de base-64 Encoded X. 509 (. CER)-indeling. Daarnaast kunt u, als u een andere tekst editor wilt gebruiken, begrijpen dat sommige editors onbedoelde opmaak op de achtergrond kunnen introduceren. Dit kan problemen veroorzaken bij het uploaden van de tekst van dit certificaat naar Azure.

   ![Openen met Klad blok](./media/vpn-gateway-certificates-export-public-key-include/notepad.png)
