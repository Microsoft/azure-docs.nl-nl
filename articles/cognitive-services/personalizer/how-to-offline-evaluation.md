---
title: Offline-evaluatie uitvoeren-persoonlijker
titleSuffix: Azure Cognitive Services
description: In dit artikel wordt uitgelegd hoe u offline-evaluatie kunt gebruiken om de effectiviteit van uw app te meten en uw leer proces te analyseren.
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: how-to
ms.date: 02/20/2020
ms.openlocfilehash: a473085f9c94ca42a75d01b342d60cc33836b096
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "88244836"
---
# <a name="analyze-your-learning-loop-with-an-offline-evaluation"></a>Analyseer uw leer proces met een offline-evaluatie

Meer informatie over het volt ooien van een offline-evaluatie en het begrijpen van de resultaten.

Met offline-evaluaties kunt u meten hoe effectief Personaler wordt vergeleken met het standaard gedrag van uw toepassing, hoe functies het meest aan persoonlijke voor keur hebben en nieuwe machine learning waarden automatisch detecteren.

Lees meer over [offline-evaluaties](concepts-offline-evaluation.md) voor meer informatie.

## <a name="prerequisites"></a>Vereisten

* Een geconfigureerde aangepaste lus
* De Personaler-lus moet beschikken over een representatieve hoeveelheid gegevens-als een indicatieve. we raden ten minste 50.000 gebeurtenissen in de logboeken aan voor zinvolle evaluatie resultaten. U kunt eventueel ook eerder de _Learning-beleids_ bestanden hebben geëxporteerd die u in dezelfde evaluatie wilt vergelijken en testen.

## <a name="run-an-offline-evaluation"></a>Een offline-evaluatie uitvoeren

1. Zoek uw persoonlijke resource in het [Azure Portal](https://azure.microsoft.com/free/cognitive-services).
1. Ga in het Azure Portal naar de sectie **evaluaties** en selecteer **evaluatie maken**.
    ![Ga in het Azure Portal naar de sectie * *-evaluaties * * en selecteer * * evaluatie maken * *.](./media/offline-evaluation/create-new-offline-evaluation.png)
1. Configureer de volgende waarden:

    * Een evaluatie naam.
    * Begin-en eind datum: Dit zijn datums die het gegevens bereik opgeven dat in de evaluatie moet worden gebruikt. Deze gegevens moeten aanwezig zijn in de logboeken, zoals opgegeven in de waarde voor [gegevens retentie](how-to-settings.md) .
    * Optimalisatie detectie is ingesteld op **Ja**.

    > [!div class="mx-imgBorder"]
    > ![Instellingen voor offline-evaluatie kiezen](./media/offline-evaluation/create-an-evaluation-form.png)

1. Start de evaluatie door **OK** te selecteren.

## <a name="review-the-evaluation-results"></a>De evaluatie resultaten controleren

Het uitvoeren van evaluaties kan veel tijd in beslag nemen, afhankelijk van de hoeveelheid gegevens die moet worden verwerkt, het aantal trainings beleid dat moet worden vergeleken en of er een optimalisatie is aangevraagd.

Als u klaar bent, kunt u de evaluatie selecteren in de lijst met evaluaties en vervolgens **de Score van uw toepassing vergelijken met andere mogelijke leer instellingen**. Selecteer deze functie als u wilt zien hoe het huidige trainings beleid wordt uitgevoerd in vergelijking met een nieuw beleid.

1. Bekijk de prestaties van het [trainings beleid](concepts-offline-evaluation.md#discovering-the-optimized-learning-policy).

    > [!div class="mx-imgBorder"]
    > [![Evaluatie resultaten controleren](./media/offline-evaluation/evaluation-results.png)](./media/offline-evaluation/evaluation-results.png#lightbox)

1. Selecteer **Toep assen** om het beleid toe te passen waarmee het model voor uw gegevens het beste wordt verbeterd.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over de werking van [offline-evaluaties](concepts-offline-evaluation.md).
