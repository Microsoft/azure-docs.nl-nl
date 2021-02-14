---
title: Problemen met Azure Data Factory UX oplossen
description: Meer informatie over het oplossen van problemen met Azure Data Factory UX.
author: ceespino
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 09/03/2020
ms.author: ceespino
ms.reviewer: daperlov
ms.openlocfilehash: b423b008453ca5f1b76e5c277f5a434dbf14cdb1
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100382834"
---
# <a name="troubleshoot-azure-data-factory-ux-issues"></a>Problemen met Azure Data Factory UX oplossen

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

In dit artikel worden algemene probleemoplossings methoden voor Azure Data Factory UX besproken.

## <a name="adf-ux-not-loading"></a>ADF UX wordt niet geladen

> [!NOTE]
> De Azure Data Factory UX heeft officiële ondersteuning voor micro soft Edge en Google Chrome. Het gebruik van andere webbrowsers kan leiden tot onverwacht of niet-gedocumenteerd gedrag.

### <a name="third-party-cookies-blocked"></a>Cookies van derden geblokkeerd

ADF UX maakt gebruik van browser cookies om gebruikers sessie te behouden en interactieve ontwikkel-en bewakings ervaringen mogelijk te maken. Het is mogelijk dat uw browser cookies van derden blokkeert omdat u een incognito-sessie gebruikt of een AD-blok kering hebt ingeschakeld. Het blok keren van cookies van derden kan problemen veroorzaken bij het laden van de portal, zoals wordt omgeleid naar een lege pagina, https://adf.azure.com/accesstoken.html of er wordt een waarschuwing weer gegeven waarin wordt vermeld dat cookies van derden worden geblokkeerd. Als u dit probleem wilt oplossen, kunt u via de volgende stappen opties voor cookies van derden inschakelen in uw browser:

### <a name="google-chrome"></a>Google Chrome

#### <a name="allow-all-cookies"></a>Alle cookies toestaan

1. Ga naar **Chrome://settings/cookies** in uw browser.
1. Selecteer optie **alle cookies toestaan** 

    ![Alle cookies in Chrome toestaan](media/data-factory-ux-troubleshoot-guide/chrome-allow-all-cookies.png)
1. Vernieuw ADF UX en probeer het opnieuw.

#### <a name="only-allow-adf-ux-to-use-cookies"></a>Alleen ADF UX toestaan cookies te gebruiken
Als u niet alle cookies wilt toestaan, kunt u eventueel gewoon ADF UX toestaan:
1. Ga naar **Chrome://settings/cookies**.
1. Selecteer **toevoegen** onder **sites waarvoor altijd de optie cookies kan worden gebruikt** 

    ![ADF-UX toevoegen aan toegestane sites in Chrome](media/data-factory-ux-troubleshoot-guide/chrome-only-adf-cookies-1.png)
1. Voeg **ADF.Azure.com** -site toe, Controleer **de optie alle cookies** en sla het bestand op. 

    ![Alle cookies van de website ADF UX toestaan](media/data-factory-ux-troubleshoot-guide/chrome-only-adf-cookies-2.png)
1. Vernieuw ADF UX en probeer het opnieuw.

### <a name="microsoft-edge"></a>Microsoft Edge

1. Ga naar **Edge://Settings/content/cookies** in uw browser.
1. Zorg ervoor dat **sites toestaan om cookie gegevens op te slaan en te lezen** zijn ingeschakeld en dat de optie cookies van derden **blok keren** is uitgeschakeld 

    ![Alle cookies in de rand toestaan](media/data-factory-ux-troubleshoot-guide/edge-allow-all-cookies.png)
1. Vernieuw ADF UX en probeer het opnieuw.

#### <a name="only-allow-adf-ux-to-use-cookies"></a>Alleen ADF UX toestaan cookies te gebruiken

Als u niet alle cookies wilt toestaan, kunt u eventueel gewoon ADF UX toestaan:

1. Ga naar **Edge://Settings/content/cookies**.
1. Onder sectie **toestaan** selecteert u **ADF.Azure.com** -site **toevoegen** en toevoegen. 

    ![ADF-UX toevoegen aan toegestane sites in de rand](media/data-factory-ux-troubleshoot-guide/edge-allow-adf-cookies.png)
1. Vernieuw ADF UX en probeer het opnieuw.

## <a name="connection-failed-on-adf-ux"></a>De verbinding is mislukt in ADF UX

Soms ziet u de fouten ' Connection Fails ' in ADF UX die vergelijkbaar zijn met de onderstaande scherm afbeelding nadat u op **verbinding testen**, **voor beeld**, enzovoort hebt geklikt.

![Verbinding is mislukt](media/data-factory-ux-troubleshoot-guide/connection-failed.png)

In dit geval kunt u eerst dezelfde bewerking met InPrivate-navigatie modus in uw browser proberen.

Als het nog steeds niet werkt, drukt u in de browser op F12 om **Ontwikkelhulpprogramma's** te openen. Ga naar het tabblad **netwerk** , **Schakel cache uitschakelen** uit, voer de mislukte bewerking opnieuw uit en zoek de mislukte aanvraag (in het rood).

![Mislukte aanvraag](media/data-factory-ux-troubleshoot-guide/failed-request.png)

Zoek vervolgens de **hostnaam** (in dit geval **dpnortheurope.SVC.DataFactory.Azure.com**) van de aanvraag- **URL** van de mislukte aanvraag.

Typ de **hostnaam** rechtstreeks in de adres balk van uw browser. Als u 404 in de browser ziet, betekent dit meestal dat de client zijde OK is en het probleem zich aan de kant van de ADF-service bevindt. Een ondersteunings ticket indienen met de **activiteits-id** van het ADF UX-fout bericht.

![Kan de resource niet vinden](media/data-factory-ux-troubleshoot-guide/status-code-404.png)

Als dat niet het geval is of als u in de browser vergelijk bare fout ziet, betekent dit meestal dat er een probleem is met de client zijde. Volg verder de stappen voor probleem oplossing.

![Fout aan de client zijde](media/data-factory-ux-troubleshoot-guide/client-side-error.png)

Open de **opdracht prompt** en typ **nslookup dpnortheurope.SVC.DataFactory.Azure.com**. Een normaal antwoord moet er als volgt uitzien:

![Opdracht antwoord 1](media/data-factory-ux-troubleshoot-guide/command-response-1.png)

Als u een normaal DNS-antwoord ziet, neemt u contact op met uw lokale IT-ondersteuning om de firewall instellingen te controleren of HTTPS-verbinding met deze hostnaam is geblokkeerd. Als het probleem niet kan worden opgelost, moet u een ondersteunings ticket met de **activiteits-id** van het ADF UX-fout bericht indienen.

Als u iets anders ziet dan dit, betekent dit meestal dat er iets mis is met uw DNS-server bij het omzetten van de DNS-naam. Het wijzigen van de ISP (Internet provider) of DNS (bijvoorbeeld bij Google DNS-8.8.8.8) is een mogelijke oplossing om te proberen. Als het probleem zich blijft voordoen, kunt u ook **nslookup DataFactory.Azure.com** en **nslookup Azure.com** proberen om te zien op welk niveau uw DNS-omzetting is mislukt en alle gegevens verzenden naar uw lokale IT-ondersteuning of uw Internet provider voor het oplossen van problemen. Als ze van mening zijn dat het probleem nog steeds aan micro soft is, kunt u een ondersteunings ticket indienen met de **activiteits-id** van het ADF UX-fout bericht.

![Opdracht antwoord 2](media/data-factory-ux-troubleshoot-guide/command-response-2.png)

## <a name="change-linked-service-type-in-datasets"></a>Het gekoppelde service type wijzigen in gegevens sets

Gegevensset voor bestands indelingen kan worden gebruikt met alle connectors op basis van bestanden. u kunt bijvoorbeeld een Parquet-gegevensset configureren op Azure Blob of Azure Data Lake Storage Gen2. Opmerking elke connector ondersteunt een andere set met instellingen voor gegevens opslag die betrekking hebben op de activiteit en met een ander app-model. 

Wanneer u in de gebruikers interface van ADF een bestands indeling gebruikt in een activiteit, zoals kopiëren, opzoeken, GetMetadata, verwijderen van activiteiten, en in de gegevensset die u wilt aanwijzen naar een gekoppelde service van een ander type vanuit de huidige (bijvoorbeeld overschakelen van het bestands systeem naar ADLS Gen2), wordt het volgende waarschuwings bericht weer gegeven. Om ervoor te zorgen dat het een schone switch is, worden de pijp lijnen en activiteiten die verwijzen naar deze gegevensset, zodanig gewijzigd dat het nieuwe type ook wordt gebruikt, en eventuele bestaande instellingen voor gegevens archieven, die niet compatibel zijn met het nieuwe type, worden gewist omdat het niet langer van toepassing is.

Als u meer wilt weten over de ondersteunde instellingen voor gegevens opslag voor elke connector, gaat u naar het bijbehorende connector artikel-> eigenschappen van de Kopieer activiteit om de lijst met gedetailleerde eigenschappen weer te geven. Raadpleeg [Amazon S3](connector-amazon-simple-storage-service.md), [azure BLOB](connector-azure-blob-storage.md), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md), [Azure data Lake Storage Gen2](connector-azure-data-lake-storage.md), [Azure File Storage](connector-azure-file-storage.md), [Bestands systeem](connector-file-system.md), [FTP](connector-ftp.md), [Google Cloud Storage](connector-google-cloud-storage.md), [HDFS](connector-hdfs.md), [http](connector-http.md)en [SFTP](connector-sftp.md).

![Waarschuwings bericht](media/data-factory-ux-troubleshoot-guide/warning-message.png)

## <a name="next-steps"></a>Volgende stappen

Probeer deze bronnen voor meer informatie over probleem oplossing:

* [Data Factory Blog](https://azure.microsoft.com/blog/tag/azure-data-factory/)
* [Data Factory functie aanvragen](https://feedback.azure.com/forums/270578-data-factory)
* [Stack Overflow forum voor Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
* [Twitter-informatie over Data Factory](https://twitter.com/hashtag/DataFactory)
* [Azure-video's](https://azure.microsoft.com/resources/videos/index/)
* [Microsoft Q&A-vragenpagina](/answers/topics/azure-data-factory.html)