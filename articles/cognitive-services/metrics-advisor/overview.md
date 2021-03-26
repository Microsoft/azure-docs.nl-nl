---
title: Wat is de Metrics Advisor-service?
titleSuffix: Azure Cognitive Services
description: Wat is Metrics Advisor?
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: overview
ms.date: 09/14/2020
ms.author: mbullwin
ms.openlocfilehash: 901d86b5569be61f89178dac460b8750bce9ea73
ms.sourcegitcommit: 73d80a95e28618f5dfd719647ff37a8ab157a668
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2021
ms.locfileid: "105605532"
---
# <a name="what-is-metrics-advisor-preview"></a>Wat is Metrics Advisor (preview)? 

Metrics Advisor is een onderdeel van Azure Cognitive Services dat gebruikmaakt van AI voor het uitvoeren van gegevens bewaking en anomalie detectie in tijdreeks gegevens. De service automatiseert het proces van het Toep assen van modellen op uw gegevens en biedt een reeks Api's en een Webwerk ruimte voor gegevens opname, afwijkings detectie en diagnoses, zonder dat u machine learning hoeft te weten. Ontwikkel aars kunnen AIOps, predicaat onderhoud en toepassingen voor bedrijfs monitors op de service bouwen. Met Metrics Advisor kunt u:

* Multidimensionale gegevens van meerdere gegevensbronnen analyseren
* Anomalieën identificeren en correleren
* Het anomaliedetectiemodel dat wordt gebruikt voor uw gegevens configureren en afstemmen
* Problemen vaststellen en hulp krijgen bij het analyseren van hoofd oorzaken

:::image type="content" source="media/metrics-advisor-overview.png" alt-text="Overzicht van Metrics Advisor":::

## <a name="connect-to-a-variety-of-data-sources"></a>Verbinding maken met verschillende gegevensbronnen

Met Metrics Advisor kunt u verbinding maken met en [multidimensionale metrische gegevens opnemen](how-tos/onboard-your-data.md) uit allerlei gegevensarchieven, met inbegrip van: SQL Server, Azure Blob Storage, MongoDB en meer.

## <a name="easy-to-use-and-customizable-anomaly-detection"></a>Gebruiksvriendelijke en aanpasbare anomaliedetectie

* Metrics Advisor selecteert automatisch het beste model voor uw gegevens, zonder dat u enige kennis van machine learning hoeft te hebben.
* Bewaak automatisch elke tijdreeks in [multidimensionale metrische gegevens](glossary.md#multi-dimensional-metric).
* Gebruik [parameterafstemming](how-tos/configure-metrics.md) en [interactieve feedback](how-tos/anomaly-feedback.md) om het model dat op uw gegevens en toekomstige anomaliedetectieresultaten wordt toegepast, naar wens aan te passen.

## <a name="real-time-alerts-through-multiple-channels"></a>Real-time waarschuwingen via meerdere kanalen

Wanneer afwijkingen worden gedetecteerd, kan met metrische gegevens [real-time waarschuwingen worden verzonden](how-tos/alerts.md) via meerdere kanalen met behulp van hooks, zoals e-mail hooks, webhooks en Azure DevOps-hooks. Met flexibele waarschuwings regels kunt u aanpassen welke waarschuwingen worden verzonden en op welke bestemming.

## <a name="smart-diagnostic-insights-by-analyzing-anomalies"></a>Slimme diagnostische inzichten door anomalieën te analyseren

Analyseer anomalieën die zijn gedetecteerd in multidimensionale metrische gegevens en genereer [slimme diagnostische inzichten](how-tos/diagnose-incident.md), met inbegrip van de meest waarschijnlijke hoofdoorzaak, diagnostische structuren, metrische analyses, en meer. Door [metrische gegevens](how-tos/metrics-graph.md)te configureren, kan de analyse van biometrische gegevens worden ingeschakeld om u te helpen bij het visualiseren van incidenten.


## <a name="typical-workflow"></a>Standaardwerkstroom

De werkstroom is eenvoudig: na onboarding van de gegevens kunt u de anomaliedetectie afstemmen en configuraties maken die passen bij uw scenario.

1. [Maak een Azure-resource](https://go.microsoft.com/fwlink/?linkid=2142156) voor Metrics Advisor. 
2. Bouw uw eerste gegevensset met behulp van de webportal.
    1. Onboarding van uw gegevens
    2. Anomaliedetectie afstemmen
    3. Abonneren op waarschuwingen
    4. Diagnostische inzichten weergeven
3. Gebruik de REST API om uw exemplaar aan te passen.

## <a name="next-steps"></a>Volgende stappen

* Een quickstart verkennen: [Uw eerste metriek op het web controleren](quickstarts/web-portal.md).
* Een quickstart verkennen: [De REST API's gebruiken om uw oplossing aan te passen](./quickstarts/rest-api-and-client-library.md).
