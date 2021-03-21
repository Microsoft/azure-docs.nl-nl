---
title: Een Cognitive Services Text Analytics-resource maken
titleSuffix: Azure Cognitive Services
description: Leer hoe u een Cognitive Services Text Analytics-resource kunt maken.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 02/09/2021
ms.author: aahi
ms.openlocfilehash: 5b6479d48a51ba962f2f6bfba16dac3b0886a9ff
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101750153"
---
## <a name="create-a-cognitive-services-text-analytics-resource"></a>Een Cognitive Services Text Analytics-resource maken

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
1. Selecteer **Een resource maken** en ga vervolgens naar **AI en Machine Learning** > **Text Analytics**.
   Of ga naar [Text Analytics maken](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics).
1. Voer alle vereiste instellingen in:

    |Instelling|Waarde|
    |--|--|
    |Naam|Voer een naam in (2-64 tekens).|
    |Abonnement|Selecteer het juiste abonnement.|
    |Locatie|Selecteer een locatie in de buurt.|
    |Prijscategorie| Voer **S** in, de standaardprijscategorie.|
    |Resourcegroep|Selecteer een beschikbare resourcegroep.|

1. Selecteer **Maken** en wacht tot de resource is gemaakt. Uw browser wordt automatisch omgeleid naar de zojuist gemaakte resourcepagina.
1. De geconfigureerde `endpoint` en een API-sleutel verzamelen:

    |Het tabblad resource in de portal|Instelling|Waarde|
    |--|--|--|
    |**Overzicht**|Eindpunt|Kopieer het eind punt. Deze lijkt op `https://my-resource.cognitiveservices.azure.com/text/analytics/v3.0` .|
    |**Sleutels**|API-sleutel|Kopieer een van de twee sleutels. Het is een alfanumerieke teken reeks van 32 tekens zonder spaties of streepjes: <`xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`>.|
