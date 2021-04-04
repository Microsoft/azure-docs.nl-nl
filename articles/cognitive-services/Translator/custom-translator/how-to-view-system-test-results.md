---
title: Resultaten van systeem testen en implementatie-aangepaste vertaler weer geven
titleSuffix: Azure Cognitive Services
description: Wanneer uw training is geslaagd, controleert u de systeem tests om uw trainings resultaten te analyseren. Als u tevreden bent met de resultaten van de training, plaatst u een implementatie aanvraag voor het getrainde model.
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 08/17/2020
ms.author: lajanuar
ms.topic: conceptual
ms.openlocfilehash: cae2c95e56312c58d396d1e578f4677ce2b14aa2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "98895931"
---
# <a name="view-system-test-results"></a>Testresultaten voor systeem weergeven

Wanneer uw training is geslaagd, controleert u de systeem tests om uw trainings resultaten te analyseren. Als u tevreden bent met de resultaten van de training, plaatst u een implementatie aanvraag voor het getrainde model.

## <a name="system-test-results-page"></a>Pagina resultaten van systeem test

Selecteer een project en selecteer vervolgens het tabblad modellen van het project, zoek het model dat u wilt gebruiken en selecteer ten slotte het tabblad testen.

Op het tabblad test ziet u het volgende:

1.  **Systeem testresultaten:** Het resultaat van het test proces in de training. Het test proces produceert de BLEU-Score.

    **Aantal zinnen:** Hoeveel parallelle zinnen er zijn gebruikt in de testset.

     **Bleu Score:** BLEU Score gegenereerd voor een model na voltooiing van de training.

    **Status:** Hiermee wordt aangegeven of het test proces is voltooid of wordt uitgevoerd.

    ![Resultaten van systeem test](media/how-to/how-to-system-test-results.png)

2.  Klik op de resultaten van de systeem test en u gaat dan naar de pagina resultaat Details testen. Op deze pagina wordt de automatische vertaling van zinnen weer gegeven die deel uitmaakten van de test gegevensset.

3.  De tabel op de pagina resultaten Details van test heeft twee kolommen: één voor elke taal in de set. De kolom voor de bron taal bevat de zin die moet worden vertaald. De kolom voor de doel taal bevat twee zinnen in elke rij.

    **Ref:** Deze zin is de referentie vertaling van de bron zin zoals opgegeven in de test gegevensset.

    **Mt:** Deze zin is de automatische vertaling van de bron zin die is gemaakt door het model dat is gebouwd nadat de training is uitgevoerd.

    ![Resultaten van de systeem test worden vergeleken](media/how-to/how-to-system-test-results-2.png)

## <a name="download-test"></a>Test downloaden

Klik op de koppeling vertalingen downloaden om een zip-bestand te downloaden. De zip bevat de computer vertalingen van bron zinnen in de test gegevensset.

![Test downloaden](media/how-to/how-to-system-test-download.png)

Dit gedownloade zip-archief bevat drie bestanden.

1.  **custom.mt.txt:** Dit bestand bevat machine vertalingen van bron taal zinnen in de doel taal die worden uitgevoerd door het model dat is getraind met de gegevens van de gebruiker.

2.  **ref.txt:** Dit bestand bevat de door de gebruiker verschafte vertalingen van bron taal zinnen in de doel taal.

3.  **source.txt:** Dit bestand bevat zinnen in de bron taal.

    ![Resultaten van gedownloade systeem test](media/how-to/how-to-download-system-test.png)

## <a name="deploy-a-model"></a>Een model implementeren

Een implementatie aanvragen:

1.  Selecteer een project, ga naar het tabblad modellen.

2. Voor een goed opgeleid model wordt de knop implementeren weer gegeven als deze niet is geïmplementeerd.

    ![Scherm opname van de knop implementeren voor het implementeren van een model.](media/how-to/how-to-deploy-model.png)

3.  Klik op implementeren.
4.  Selecteer **geïmplementeerd** voor de regio (s) waar u het model wilt implementeren en klik op opslaan. U kunt **geïmplementeerde** voor meerdere regio's selecteren.

    ![Scherm afbeelding die laat zien waar u een model kunt implementeren of uitzetten.](media/how-to/how-to-deploy-model-regions.png)

5.  U kunt de status van uw model weer geven in de kolom Status.

>[!Note]
>Custom Translator ondersteunt 10 geïmplementeerde modellen binnen een werk ruimte op elk gewenst moment.

## <a name="update-deployment-settings"></a>Implementatie-instellingen bijwerken

Implementatie-instellingen bijwerken:

1.  Selecteer een project en ga naar het tabblad **modellen** .

2. Voor een correct geïmplementeerd model wordt een knop **bijwerken** weer gegeven.

    ![Scherm opname van de knop bijwerken voor het bijwerken van de implementatie-instellingen.](media/how-to/how-to-update-undeploy-model.png)

3.  Selecteer **Update**.
4.  Selecteer **geïmplementeerd** of niet- **geïmplementeerd** voor de regio's waar u uw model wilt implementeren of niet wilt implementeren en klik vervolgens op **Opslaan**.

    ![Model implementeren](media/how-to/how-to-undeploy-model.png)

>[!Note]
>Als u niet- **geïmplementeerde** voor alle regio's selecteert, wordt het model uit alle regio's ongeïmplementeerd en wordt de status niet-geïmplementeerd. Het is nu niet beschikbaar voor gebruik.

## <a name="next-steps"></a>Volgende stappen

- Begin met het gebruik van uw geïmplementeerde aangepaste Vertaal model via [micro soft Translator text-API v3](../reference/v3-0-translate.md?tabs=curl).
- Meer informatie [over het beheren van instellingen](how-to-manage-settings.md) voor het delen van uw werk ruimte, het beheren van de abonnements sleutel.
- Meer informatie [over het migreren van uw werk ruimte en project](how-to-migrate.md) van [micro soft Translator hub](https://hub.microsofttranslator.com)