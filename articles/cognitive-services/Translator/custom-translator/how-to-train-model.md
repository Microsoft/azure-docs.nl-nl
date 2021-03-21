---
title: Een model trainen-aangepaste Translator
titleSuffix: Azure Cognitive Services
description: Trainings een model is een belang rijke stap bij het bouwen van een Vertaal model. De training vindt plaats op basis van documenten die u voor die trainingen selecteert.
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 08/17/2020
ms.author: lajanuar
ms.topic: conceptual
ms.openlocfilehash: 5c17f842b93fb99a6a06b94f84ae26126794b776
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98896423"
---
# <a name="train-a-model"></a>Een model trainen

De training van een model is de eerste en belangrijkste stap voor het bouwen van een Vertaal model, anders kan het model niet worden samengesteld. De training vindt plaats op basis van documenten die u voor de trainingen selecteert. Wanneer u documenten van het document type training selecteert, moet u mindful van de minimum vereiste voor de 10.000-parallelle zinnen. Wanneer u documenten selecteert, wordt het totale aantal trainings zinnen weer gegeven om u te helpen. Deze vereiste is niet van toepassing wanneer u alleen documenten van het woordenlijst document type selecteert om een model te trainen.

Een model trainen:

1. Selecteer het project waar u een model wilt bouwen.

2. Op het tabblad gegevens voor het project worden alle relevante documenten voor het taal paar van het project weer gegeven. Selecteer hand matig de documenten die u wilt gebruiken voor het trainen van uw model. U kunt in dit scherm trainings-, afstemmings-en test documenten selecteren. U hoeft alleen de Trainingsset te selecteren en aangepaste Translator te hebben om de afstemmings-en test sets voor u te maken.

    - Document naam: naam van het document.

    - Koppelen: als dit document een parallel-of Monolingual-document is. Monolingual documenten worden momenteel niet ondersteund voor training.

    - Document type: kan training, afstemming, testen of woorden lijst zijn.

    - Taal paar: hier worden de bron-en doel taal voor het project weer gegeven.

    - Bron zinnen: toont het aantal zinnen dat is geëxtraheerd uit het bron bestand.

    - Doel zinnen: toont het aantal zinnen dat is geëxtraheerd uit het doel bestand.

    ![Model trainen](media/how-to/how-to-train-model.png)

3. Klik op de knop model maken.

4. Geef in het dialoog venster de naam op voor uw model. Standaard wordt ' Train onmiddellijk ' geselecteerd om de trainings pijplijn te starten wanneer u op de knop model maken klikt. U kunt ' opslaan als concept ' selecteren om de meta gegevens van het model te maken en het model in een concept status te plaatsen, maar model training zou niet starten. U moet op een later tijdstip hand matig modellen in concept status selecteren om te trainen.

5. Klik op de knop model maken.

    ![Dialoog venster voor Train model](media/how-to/how-to-train-model-2.png)

6. De training wordt door aangepast Translator verzonden en de status van de training wordt weer gegeven op het tabblad modellen.

    ![Model pagina Train](media/how-to/how-to-train-model-3.png)

>[!Note]
>Custom Translator ondersteunt tien gelijktijdige trainingen binnen een werk ruimte op elk gewenst moment.

## <a name="modify-a-model-name"></a>Een model naam wijzigen

U kunt een model naam wijzigen op de pagina model Details.

1. Klik op de pagina projecten op de naam van het project waarin het model zich bevindt.
2. Klik op het tabblad model.
3. Klik op de naam van het model om de details van het model weer te geven.
4. Klik op het potlood pictogram.

    ![Model bewerken](media/how-to/how-to-edit-model.png)

5. Wijzig in het dialoog venster de naam van het model en geef uw model een zinvolle naam.

    ![Meer dialoog venster bewerken](media/how-to/how-to-edit-model-dialog.png)

6. Klik op Opslaan.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie [over het weer geven van model Details](how-to-view-model-details.md).
