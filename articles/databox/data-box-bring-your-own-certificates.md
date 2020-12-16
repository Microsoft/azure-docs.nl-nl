---
title: Uw eigen certificaten gebruiken met Azure Data Box/Azure Data Box Heavy-apparaten
description: Uw eigen certificaten gebruiken om toegang te krijgen tot de lokale web-UI en blog opslag op uw Data Box of Data Box Heavy apparaat.
services: databox
author: v-dalc
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 12/11/2020
ms.author: alkohli
ms.openlocfilehash: deb52c8716f97874beae4accbf6f34f72e20ca04
ms.sourcegitcommit: 66479d7e55449b78ee587df14babb6321f7d1757
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/15/2020
ms.locfileid: "97516472"
---
# <a name="use-your-own-certificates-with-data-box-and-data-box-heavy-devices"></a>Uw eigen certificaten gebruiken met Data Box en Data Box Heavy apparaten

Tijdens de verwerking van de bestelling worden zelfondertekende certificaten gegenereerd voor toegang tot de lokale web-UI en Blob-opslag voor een Data Box of Data Box Heavy apparaat. Als u liever met uw apparaat communiceert via een vertrouwd kanaal, kunt u uw eigen certificaten gebruiken.

In dit artikel wordt beschreven hoe u uw eigen certificaten installeert en hoe u de standaard certificaten herstelt voordat u uw apparaat naar het Data Center stuurt. Het biedt ook een samen vatting van de certificaat vereisten.

## <a name="about-certificates"></a>Over certificaten

Een certificaat biedt een koppeling tussen een **open bare sleutel** en een entiteit (zoals domein naam) die is **ondertekend** (geverifieerd) door een vertrouwde derde partij, zoals een **certificerings instantie**.  Een certificaat biedt een handige manier om vertrouwde open bare versleutelings sleutels te distribueren. Op deze manier zorgt u ervoor dat uw communicatie vertrouwd is en dat u versleutelde gegevens naar de juiste server verzendt.

Wanneer uw Data Box-apparaat in eerste instantie is geconfigureerd, worden automatisch zelfondertekende certificaten gegenereerd. U kunt desgewenst uw eigen certificaten meenemen. Er zijn richt lijnen die u moet volgen als u van plan bent om uw eigen certificaten te nemen.

Op een Data Box-of Data Box Heavy-apparaat worden twee typen eindpunt certificaten gebruikt:

- Blob-opslag certificaat
- Lokaal gebruikers interface certificaat

### <a name="certificate-requirements"></a>Certificaatvereisten

De certificaten moeten voldoen aan de volgende vereisten:

- Het eindpunt certificaat moet in `.pfx` een indeling zijn met een persoonlijke sleutel die kan worden geëxporteerd.
- U kunt een afzonderlijk certificaat voor elk eind punt, een multi domein certificaat voor meerdere eind punten of een eindpunt certificaat voor joker tekens gebruiken.
- De eigenschappen van een eindpunt certificaat zijn vergelijkbaar met de eigenschappen van een typisch SSL-certificaat.
- Op de client computer is een overeenkomend certificaat met de-indeling ( `.cer` bestands extensie) vereist.
- Nadat u het lokale gebruikers interface certificaat hebt geüpload, moet u de browser opnieuw opstarten en de cache wissen. Raadpleeg de specifieke instructies voor uw browser.
- De certificaten moeten worden gewijzigd als de naam van het apparaat of de DNS-domein naam wordt gewijzigd.
- Gebruik de volgende tabel bij het maken van eindpunt certificaten:

  |Type |Onderwerpnaam (SN)  |Alternatieve naam voor onderwerp (SAN)  |Voor beeld van onderwerpnaam |
  |---------|---------|---------|---------|
  |Lokale gebruikers interface| `<DeviceName>.<DNSdomain>`|`<DeviceName>.<DNSdomain>`| `mydevice1.microsoftdatabox.com` |
  |Blob Storage|`*.blob.<DeviceName>.<DNSdomain>`|`*.blob.< DeviceName>.<DNSdomain>`|`*.blob.mydevice1.microsoftdatabox.com` |
  |Multi-SAN enkel certificaat|`<DeviceName>.<DNSdomain>`|`<DeviceName>.<DNSdomain>`<br>`*.blob.<DeviceName>.<DNSdomain>`|`mydevice1.microsoftdatabox.com` |

Zie [certificaat vereisten](../../articles/databox-online/azure-stack-edge-j-series-certificate-requirements.md)voor meer informatie.

## <a name="add-certificates-to-device"></a>Certificaten toevoegen aan apparaat

U kunt uw eigen certificaten gebruiken om toegang te krijgen tot de lokale web-UI en om toegang te krijgen tot Blob Storage.

> [!IMPORTANT]
> Als de apparaatnaam of het DNS-domein wordt gewijzigd, moeten nieuwe certificaten worden gemaakt. De client certificaten en de certificaten van het apparaat moeten vervolgens worden bijgewerkt met de nieuwe apparaatnaam en het DNS-domein.

Voer de volgende stappen uit om uw eigen certificaat toe te voegen aan uw apparaat:

1. Ga naar   >  **certificaten** beheren.

   **Naam** toont de naam van het apparaat. **DNS-domein** toont de domein naam voor de DNS-server.

   De onderkant van het scherm toont de certificaten die momenteel in gebruik zijn. Voor een nieuw apparaat ziet u de zelfondertekende certificaten die zijn gegenereerd tijdens de verwerking van de order.

   ![Pagina certificaten voor een Data Box apparaat](media/data-box-bring-your-own-certificates/certificates-manage-certs.png)

2. Als u de **naam** (apparaatnaam) of het **DNS-domein** (het domein van de DNS-server voor het apparaat) wilt wijzigen, doet u dat nu voordat u het certificaat toevoegt. Selecteer vervolgens **Toepassen**.

   Het certificaat moet worden gewijzigd als de naam van het apparaat of de DNS-domein naam wordt gewijzigd.

   ![Een nieuwe apparaatnaam en een DNS-domein voor een Data Box Toep assen](media/data-box-bring-your-own-certificates/certificates-device-name-dns.png)

3. Als u een certificaat wilt toevoegen, selecteert u **certificaat toevoegen** om het paneel **certificaat toevoegen** te openen. Selecteer vervolgens het **certificaat type** - **Blob-opslag** of **lokale webinterface**.

   ![Paneel certificaten toevoegen op de pagina certificaten voor een Data Box apparaat](media/data-box-bring-your-own-certificates/certificates-add-certificate-cert-type.png)

4. Kies het certificaat bestand (in `.pfx` indeling) en voer het wacht woord in dat is ingesteld toen het certificaat werd geëxporteerd. Selecteer vervolgens **valideren & toevoegen**.

   ![Instellingen voor het toevoegen van een BLOB-eindpunt certificaat aan een Data Box](media/data-box-bring-your-own-certificates/certificates-add-blob-cert.png)

   Nadat het certificaat is toegevoegd, wordt in het scherm **certificaten** de vinger afdruk voor het nieuwe certificaat weer gegeven. De status van het certificaat is **geldig**.

   ![Een geldig nieuw certificaat dat is toegevoegd](media/data-box-bring-your-own-certificates/certificates-view-new-certificate.png)

5. Selecteer en klik op de naam van het certificaat om de details van het certificaat te bekijken. Het certificaat verloopt na een jaar.

   ![Certificaat Details voor een Data Box apparaat weer geven](media/data-box-bring-your-own-certificates/certificates-cert-details.png)

   <!--If you changed the local web UI certificate, you'll see the following error. This error will go away when you install the new certificate on the client computer.

   ![Error after a new Local web UI certificate is added to a Data Box device](media/data-box-bring-your-own-certificates/certificates-unable-to-communicate-error.png) TEST. RESTORE IF ERROR IS REPRODUCED.-->

6. Als u het certificaat voor de lokale web-UI hebt gewijzigd, moet u de browser opnieuw opstarten en vervolgens de lokale webgebruikersinterface. Deze stap is nodig om problemen met de SSL-cache te voor komen.

  <!-- TESTING THIS - The communication error should be gone from the **Certificates** screen.-->

7. Installeer het nieuwe certificaat op de client computer die u gebruikt voor toegang tot de lokale webgebruikersinterface. Zie [certificaten naar client importeren](#import-certificates-to-client)hieronder voor instructies.


## <a name="import-certificates-to-client"></a>Certificaten importeren naar client

Nadat u een certificaat aan uw Data Box-apparaat hebt toegevoegd, moet u het certificaat importeren op de client computer die u gebruikt voor toegang tot het apparaat. U importeert het certificaat in het archief met vertrouwde basis certificerings instanties voor de lokale machine.

Voer de volgende stappen uit om een certificaat op een Windows-client te importeren:

1. Klik in Verkenner met de rechter muisknop op het certificaat bestand (met de `.cer` indeling) en selecteer **certificaat installeren**. Deze actie start de wizard Certificaat importeren.

    ![Certificaat 1 importeren](media/data-box-bring-your-own-certificates/import-cert-01.png)

2. Selecteer voor de **Archief locatie** de optie **lokale computer** en selecteer vervolgens **volgende**.

    ![Lokale computer selecteren als de opslag locatie in de wizard Certificaat importeren](media/data-box-bring-your-own-certificates/import-cert-02.png)

3. Selecteer **alle certificaten in het volgende archief plaatsen**, selecteer **vertrouwde basis certificerings instantie** en selecteer **volgende**.

   ![Selecteer het archief vertrouwde basis certificerings instanties in de wizard Certificaat importeren](media/data-box-bring-your-own-certificates/import-cert-03.png)

4. Controleer uw instellingen en selecteer **volt ooien**. Er wordt een bericht weer gegeven met de melding dat het importeren is gelukt.

   ![Controleer de certificaat instellingen en voltooi de wizard Certificaat importeren](media/data-box-bring-your-own-certificates/import-cert-04.png)

## <a name="revert-to-default-certificates"></a>Herstellen naar standaard certificaten

Voordat u uw apparaat naar het Data Center van Azure herstelt, moet u terugkeren naar de oorspronkelijke certificaten die tijdens de verwerking van de order zijn gegenereerd.

Voer de volgende stappen uit om terug te keren naar de certificaten die tijdens de verwerking van de order zijn gegenereerd:

1. Ga naar **beheer**  >  **certificaten** en selecteer **certificaten herstellen**.

   Als u certificaten herstelt, worden de zelfondertekende certificaten gebruikt die zijn gegenereerd tijdens de verwerking van de order. Uw eigen certificaten worden van het apparaat verwijderd.

   ![De optie certificaten herstellen in certificaten voor een Data Box apparaat beheren](media/data-box-bring-your-own-certificates/certificates-revert-certificates.png)

2. Nadat de herversie van het certificaat is voltooid, gaat u naar **afsluiten of opnieuw opstarten** en selecteert u **opnieuw opstarten**. Deze stap is nodig om problemen met de SSL-cache te voor komen.

   ![De lokale web-UI afsluiten of opnieuw opstarten nadat de certificaten op een Data Box apparaat zijn hersteld](media/data-box-bring-your-own-certificates/certificates-restart-ui.png)

   Wacht enkele minuten en meld u opnieuw aan bij de lokale webinterface.
