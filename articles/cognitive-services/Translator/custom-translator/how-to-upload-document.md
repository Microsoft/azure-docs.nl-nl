---
title: Een document-aangepaste vertaler uploaden
titleSuffix: Azure Cognitive Services
description: De functie voor het uploaden van documenten uploadt parallelle documenten (twee documenten waarbij een van de oorsprong en de andere de vertaling is) naar de service.
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 08/17/2020
ms.author: lajanuar
ms.topic: conceptual
ms.openlocfilehash: 83cce31d3eee9b5cca7b54e996e41a270fec0030
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/27/2021
ms.locfileid: "98896391"
---
# <a name="upload-a-document"></a>Een document uploaden

In [Custom Translator](https://portal.customtranslator.azure.ai)kunt u parallelle documenten uploaden om uw Vertaal modellen te trainen. [Parallelle documenten](what-are-parallel-documents.md) zijn paren van documenten waarbij de ene vertaling van de andere is. Een document in het paar bevat zinnen in de bron taal en het andere document bevat de volgende zinnen die in de doel taal worden vertaald.

Voordat u uw documenten uploadt, raadpleegt u de [richt lijnen voor document indelingen en naamgevings regels](document-formats-naming-convention.md) om te controleren of uw bestands indeling wordt ondersteund in het aangepaste conversie programma.

## <a name="how-to-upload-document"></a>Hoe kunt u het document uploaden?

Klik in de [aangepaste Vertaal](https://portal.customtranslator.azure.ai) Portal op het tabblad documenten om naar de pagina documenten te gaan.

![Koppeling voor document uploaden](media/how-to/how-to-upload-1.png)


1.  Klik op de knop bestanden uploaden op de pagina documenten.

    ![Document pagina uploaden](media/how-to/how-to-upload-2.png)

2.  Vul de volgende gegevens in het dialoog venster in:

    a.  Document type:

    -  Training: deze documenten worden gebruikt voor trainingen.
    -  Afstemming: deze documenten worden gebruikt voor het afstemmen van de set.
    -  Testen: deze documenten worden gebruikt voor het testen van de set.
    -  Woordgroepen lijst: deze documenten worden gebruikt voor de woorden lijst voor woord groepen.
    -  Woorden lijst voor zinnen: deze documenten worden gebruikt voor de woorden lijst

    b.  Taal paar

    c.  Document overschrijven indien aanwezig: Schakel dit selectie vakje in als u bestaande documenten met dezelfde naam wilt overschrijven.

    d.  Vul de relevante sectie in voor parallelle gegevens of de combinatie gegevens.

    -  Parallelle gegevens:
        -  Bron bestand: Selecteer een bron taal bestand op de lokale computer.
        -  Doel bestand: Selecteer een doel taal bestand op de lokale computer.
        -  Document naam: wordt alleen gebruikt als u parallelle bestanden uploadt.

    - Combinatie gegevens:
        -  Combinatie bestand: Selecteer het keuze lijst bestand op de lokale computer. Uw keuze lijst met invoervak bevat de zinnen van de bron-en doel taal. [Naamgevings regels](document-formats-naming-convention.md) zijn belang rijk voor de combi natie van bestanden.

    e.  Klik op uploaden

    ![Dialoog venster Document uploaden](media/how-to/how-to-upload-dialog.png)

3.  Op dit moment verwerken we uw documenten en proberen we zinnen te extra heren. U kunt klikken op Upload voortgang weer geven om de status van uw documenten te controleren tijdens de verwerking.

    ![Dialoog venster document verwerking uploaden](media/how-to/how-to-upload-processing-dialog.png)

4.  Op deze pagina worden de status en eventuele fouten voor elk bestand in de upload weer gegeven. U kunt de oude upload status op elk gewenst moment weer geven door op het tabblad Upload geschiedenis te klikken.

    ![Dialoog venster document geschiedenis uploaden](media/how-to/how-to-upload-document-history.png)


## <a name="view-upload-history"></a>Upload geschiedenis weer geven

Op de pagina upload geschiedenis kunt u de geschiedenis bekijken van alle uploads van documenten, zoals document type, taal paar, upload status etc.

1. Klik in de [aangepaste Vertaal](https://portal.customtranslator.azure.ai) Portal op het tabblad geschiedenis uploaden om de geschiedenis weer te geven.

    ![Tabblad Upload geschiedenis](media/how-to/how-to-upload-history-1.png)

2. Op deze pagina ziet u de status van al uw eerdere uploads. Er worden uploads van de meest recente naar minst recente weer gegeven. Voor elke upload worden de document naam, de upload status, de upload datum, het aantal geüploade bestanden, het type van het bestand dat is geüpload en het taal paar van het bestand weer gegeven.

    ![Pagina geschiedenis uploaden](media/how-to/how-to-document-history-2.png)

3. Klik op een record Upload geschiedenis. In de detail pagina geschiedenis uploaden kunt u de bestanden weer geven die zijn geüpload als onderdeel van de upload, de geüploade status van het bestand, de taal van het bestand en het fout bericht (als er een fout is opgetreden bij het uploaden).

## <a name="next-steps"></a>Volgende stappen

- Gebruik de [pagina document Details](how-to-view-document-details.md) om een lijst met geëxtraheerde zinnen weer te geven.
- [Het trainen van een model](how-to-train-model.md).
