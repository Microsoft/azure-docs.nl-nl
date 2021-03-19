---
title: Exploratie-persoonlijker
titleSuffix: Azure Cognitive Services
description: Met verkennen kan Personaler goede resultaten blijven leveren, zelfs wanneer het gebruikers gedrag verandert. Het kiezen van een instelling voor een exploratie is een bedrijfs beslissing over het aandeel van gebruikers interacties om te verkennen met, om het model te verbeteren.
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 10/23/2019
ms.openlocfilehash: 00b9cc881d9414e957d7503bb6156095bb821c06
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "87128145"
---
# <a name="exploration-and-exploitation"></a>Exploratie en exploitatie

Met verkennen kan Personaler goede resultaten blijven leveren, zelfs wanneer het gebruikers gedrag verandert.

Wanneer Personaler een classificatie oproep ontvangt, retourneert deze een RewardActionID dat:
* Maakt gebruik van exploitatie om te voldoen aan het meest waarschijnlijke gebruikers gedrag op basis van het huidige machine learning model.
* Maakt gebruik van verkennen, wat niet overeenkomt met de actie die de hoogste kans op de positie heeft.

Personaler maakt momenteel gebruik van een algoritme met de naam *Epsilon Greedy* om te verkennen. 

## <a name="choosing-an-exploration-setting"></a>Een instelling voor verkennen kiezen

U configureert het percentage verkeer dat voor het verkennen moet worden gebruikt op de **configuratie** pagina van de Azure portal voor persoonlijk gebruik. Met deze instelling bepaalt u het percentage van de Rangings aanvragen dat het verkennen uitvoert. 

Personaler bepaalt of u deze kans op elke positie oproep wilt verkennen of ermee wilt worden misbruikt. Dit wijkt af van het gedrag in sommige A/B-frameworks die een behandeling voor specifieke gebruikers-Id's vergren delen.

## <a name="best-practices-for-choosing-an-exploration-setting"></a>Aanbevolen procedures voor het kiezen van een instelling voor verkennen

Het kiezen van een instelling voor een exploratie is een bedrijfs beslissing over het aandeel van gebruikers interacties om te verkennen met, om het model te verbeteren. 

Met een instelling van nul worden veel van de voor delen van persoonlijkere voor komen. Met deze instelling maakt Personaler geen gebruik van gebruikers interacties voor het ontdekken van betere gebruikers interacties. Dit leidt tot het model stagnation, drift en uiteindelijk lagere prestaties.

Een instelling die te hoog is, leidt niet tot de voor delen van het leren van het gedrag van gebruikers. Als u deze instelt op 100% impliceert dit een constante wille keurige waarde, en zijn geleerde gedrag van gebruikers niet van invloed op het resultaat.

Het is belang rijk dat u het toepassings gedrag niet wijzigt, op basis van het feit of u ziet of de Personaler zich moet verkennen of misbruikt. Dit zou leiden tot het leren van BIASS die uiteindelijk de potentiële prestaties zouden verlagen.

## <a name="next-steps"></a>Volgende stappen

[Bekrachtigend leren](concepts-reinforcement-learning.md) 