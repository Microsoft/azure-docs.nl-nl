---
title: 'Hoe Personaler werkt: Personaler'
description: De Personaler- _lus_ gebruikt machine learning om het model te bouwen dat de meest voorkomende actie voor uw inhoud voorspelt. Het model wordt uitsluitend getraind op uw gegevens die u hebt verzonden met behulp van de rang en beloning.
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 02/18/2020
ms.openlocfilehash: cfbe5cf8c19bfafb38f6149391e09350785ebf9c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91303604"
---
# <a name="how-personalizer-works"></a>Hoe Personalizer werkt

De Personaler resource, uw _leer proces_, gebruikt machine learning om het model te bouwen waarmee de meest voorkomende actie voor uw inhoud wordt voor speld. Het model wordt uitsluitend getraind op uw gegevens die u hebt verzonden met behulp van de **rang** en **beloning** . Elke lus is volledig onafhankelijk van elkaar.

## <a name="rank-and-reward-apis-impact-the-model"></a>Prioriteits-en beloning-Api's die van invloed zijn op het model

U verzendt _acties met functies_ en _context functies_ naar de classificatie-API. De **positie** -API besluit om de volgende te gebruiken:

* _Exploit_: het huidige model om de beste actie te bepalen op basis van gegevens in het verleden.
* _Verkennen_: Selecteer een andere actie in plaats van de bovenste actie. U kunt [Dit percentage configureren](how-to-settings.md#configure-exploration-to-allow-the-learning-loop-to-adapt) voor uw personaler-resource in de Azure Portal.

U bepaalt de belonings Score en verzendt deze score naar de belonings-API. De **belonings** -API:

* Verzamelt gegevens om het model te trainen door de functies en belonings scores van elke positie oproep vast te leggen.
* Gebruikt die gegevens om het model bij te werken op basis van de configuratie die is opgegeven in het _Learning-beleid_.

## <a name="your-system-calling-personalizer"></a>Uw systeem Personaler aanroepen

In de volgende afbeelding ziet u de architectuur stroom van het aanroepen van de rang-en belonings aanroepen:

![alternatieve tekst](./media/how-personalizer-works/personalization-how-it-works.png "Hoe persoonlijke instellingen werken")

1. U verzendt _acties met functies_ en _context functies_ naar de classificatie-API.

    * Personaler beslist of het huidige model moet worden misbruikt of dat er nieuwe opties voor het model moeten worden verkend.
    * Het resultaat van de rang schikking wordt verzonden naar EventHub.
1. De bovenste positie wordt geretourneerd naar de systeem _Actie-id_.
    Uw systeem presenteert die inhoud en bepaalt een belonings Score op basis van uw eigen bedrijfs regels.
1. Uw systeem retourneert de belonings score naar de learning-lus.
    * Wanneer Personaler de beloning ontvangt, wordt de vergoeding verzonden naar EventHub.
    * De positie en beloning worden gecorreleerd.
    * Het AI-model wordt bijgewerkt op basis van de correlatie resultaten.
    * De engine voor het ingrijpen van de functie wordt bijgewerkt met het nieuwe model.

## <a name="personalizer-retrains-your-model"></a>Persoonlijkere traint uw model opnieuw

Personaler traint uw model opnieuw op basis van de instelling voor het bijwerken van de **model frequentie** van uw personaler-resource in de Azure Portal.

Personaler gebruikt alle gegevens die momenteel worden bewaard, op basis van de instelling voor het **bewaren van gegevens** in aantal dagen op uw personaler-resource in de Azure Portal.

## <a name="research-behind-personalizer"></a>Onderzoek achter persoonlijker

Personaler is gebaseerd op wetenschap en onderzoek op het gebied van versterking van het [onderwijs](concepts-reinforcement-learning.md) , waaronder documenten, onderzoeksactiviteiten en lopende onderzoeksgebieden in micro soft Research.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over de [belangrijkste scenario's](where-can-you-use-personalizer.md) voor personaliseren