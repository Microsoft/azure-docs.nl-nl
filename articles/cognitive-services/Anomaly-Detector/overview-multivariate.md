---
title: Wat is de Anomaliey detector multidimensionale-API?
titleSuffix: Azure Cognitive Services
description: Overzicht van nieuwe Api's voor de open bare preview-versie van anomalie detectie multidimensionale.
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: conceptual
ms.date: 04/01/2021
ms.author: mbullwin
keywords: afwijkingsdetectie, machine learning, algoritmes
ms.openlocfilehash: 63ad63cd57dce5f503e317e8c6330dca0325ba05
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107315458"
---
# <a name="multivariate-time-series-anomaly-detection-public-preview"></a>Anomalie detectie multidimensionale time series (open bare preview)

Met de eerste versie van de Azure Cognitive Services anomalie detectie hebt u de mogelijkheid om metrische bewakings oplossingen te maken met behulp van de gebruiks vriendelijke [univariate time series anomalie detectie-api's](overview.md). Door analyse van tijd reeksen afzonderlijk toe te staan, biedt afwijkende detector univariate eenvoud en schaal baarheid.

De nieuwe **multidimensionale anomalie detectie** -api's maken ontwikkel aars nog verder door geavanceerde AI te integreren voor het detecteren van afwijkingen van groepen met metrische gegevens, zonder dat hiervoor machine learning kennis of gelabelde informatie nodig is. Afhankelijkheden en tussen correlaties tussen Maxi maal 300 verschillende signalen worden nu automatisch geteld als sleutel factoren. Deze nieuwe mogelijkheid helpt u bij het proactief beveiligen van uw complexe systemen, zoals software toepassingen, servers, fabrieks machines, ruimte vaartuigen of zelfs uw bedrijf, van storingen.

Stel dat 20 Sens oren van een automatische engine 20 verschillende signalen genereren, zoals trillingen, Tempe ratuur, brand belasting, enzovoort. De leesingen van deze signalen geven u mogelijk geen informatie over problemen op systeem niveau, maar ze kunnen samen de status van de engine vertegenwoordigen. Wanneer de interactie van deze signalen buiten het gebruikelijke bereik verschilt, kan de afwijkende detectie functie van multidimensionale de afwijkende ervaring bedenken, zoals een ervaren expert. De onderliggende AI-modellen worden getraind en aangepast aan de hand van uw gegevens, zodat deze de unieke behoeften van uw bedrijf begrijpt. Met de nieuwe Api's in anomalie detectie kunnen ontwikkel aars nu eenvoudig de multidimensionale time series-detectie mogelijkheden integreren in voorspellende onderhouds oplossingen, oplossingen voor AIOps-bewaking voor complexe bedrijfs software of business intelligence-hulpprogram ma's.

## <a name="when-to-use-multivariate-versus-univariate"></a>Wanneer **multidimensionale** gebruiken versus **univariate**

Gebruik univariate anomalie detectie-Api's als uw doel heeft om afwijkingen van een normaal patroon voor elke afzonderlijke tijd reeks op basis van hun eigen historische gegevens op te sporen. Voor beelden: u wilt dagelijkse opbrengst afwijkingen detecteren op basis van de gegevens van de omzet zelf, of u wilt een CPU-piek op basis van de CPU-gegevens detecteren.
- `POST /anomalydetector/v1.0/timeseries/last/detect`
- `POST /anomalydetector/v1.0/timeseries/batch/detect`
- `POST /anomalydetector/v1.0/timeseries/changepoint/detect`

![Lijn diagram van de tijd reeks met de schommel waarden van een enkele variabele die worden vastgelegd door een blauwe lijn met afwijkingen die worden geïdentificeerd door de oranje cirkels](./media/anomaly_detection2.png)

Gebruik hieronder multidimensionale anomalie detectie-Api's als uw doel is om afwijkingen op systeem niveau te detecteren vanuit een groep tijdreeks gegevens. In het bijzonder, wanneer een wille keurige tijd reeks u niet meer vertelt en u alle signalen (een groep tijd reeksen) op een holistische wijze moet bekijken om een probleem op systeem niveau te bepalen. Voor beeld: u hebt een dure fysieke activa zoals vlieg tuigen, apparatuur op een olie-of satelliet. Elk van deze activa heeft tien tallen of honderden verschillende typen Sens oren. U moet al deze tijd reeks signalen van deze Sens oren bekijken om te bepalen of er een probleem is met het systeem niveau.

- `POST /anomalydetector/v1.1-preview/multivariate/models`
- `GET /anomalydetector/v1.1-preview/multivariate/models[?$skip][&$top]`
- `GET /anomalydetector/v1.1-preview/multivariate/models/{modelId}`
- `POST/anomalydetector/v1.1-preview/multivariate/models/{modelId}/detect`
- `GET /anomalydetector/v1.1-preview/multivariate/results/{resultId}`
- `DELETE /anomalydetector/v1.1-preview/multivariate/models/{modelId}`
- `GET /anomalydetector/v1.1-preview/multivariate/models/{modelId}/export`

![Meerdere tijdreeks lijn grafieken voor variabelen van: trillingen, Tempe ratuur, druk, snelheid, rotatie snelheid met afwijkingen die in het oranje zijn gemarkeerd](./media/multivariate-graph.png)

## <a name="region-support"></a>Ondersteuning voor regio

De open bare preview van anomalie detectie multidimensionale is momenteel beschikbaar in drie regio's: West-VS2, Oost-VS2 en Europa-west.

## <a name="algorithms"></a>Algoritmen

- [Afwijkings detectie voor multidimensionale tijd series via grafiek attentie netwerk](https://arxiv.org/abs/2009.02040)

## <a name="join-the-anomaly-detector-community"></a>Lid worden van de Anomaly Detector-community

- Word lid van de [Anomaly Detector-adviesgroep van Microsoft Teams](https://aka.ms/AdAdvisorsJoin)

## <a name="next-steps"></a>Volgende stappen

- [Quick](./quickstarts/client-libraries-multivariate.md)starts.
- [Best practices](./concepts/best-practices-multivariate.md): dit artikel is een aanbevolen patroon voor gebruik met de multidimensionale-api's.
