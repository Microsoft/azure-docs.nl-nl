---
title: Wat is de Anomaly Detector-API?
titleSuffix: Azure Cognitive Services
description: Met de algoritmen van de Anomaly Detector-API's kunt u anomalieën in uw tijdreeksgegevens detecteren.
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: overview
ms.date: 11/23/2020
ms.author: mbullwin
keywords: afwijkingsdetectie, machine learning, algoritmes
ms.custom: cog-serv-seo-aug-2020
ms.openlocfilehash: 1a13b962a000e6e3b2143dc23f94b63c122bd963
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/24/2020
ms.locfileid: "95999094"
---
# <a name="what-is-the-anomaly-detector-api"></a>Wat is de Anomaly Detector-API?

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

Met de Anomaly Detector API kunt u afwijkingen in uw tijdreeksgegevens bewaken en detecteren zonder dat u machine learning hoeft te kennen. De algoritmes van Anomaly Detector-API worden aangepast door automatisch de best-passende modellen voor uw gegevens te bepalen en toe te passen, ongeacht de branche, het scenario of het gegevensvolume. Met behulp van uw tijdreeksgegevens worden de grenzen voor anomaliedetectie en verwachte waarden vastgesteld, alsmede welke gegevenspunten anomalieën zijn.

![Detectiepatroonwijzigingen in serviceaanvragen](./media/anomaly_detection2.png)

Voor het gebruik van Anomaly Detector is geen ervaring met machine learning vereist. Met de RESTful-API kunt u de service eenvoudig integreren in uw toepassingen en processen.

## <a name="features"></a>Functies

Met Anomaly Detector kunt u automatisch anomalieën in uw tijdreeksgegevens detecteren of wanneer deze in realtime optreden.

|Functie  |Beschrijving  |
|---------|---------|
|Afwijkingsdetectie in realtime. | Anomalieën in uw streaminggegevens detecteren door de nieuwste punten te vergelijken met eerder gedetecteerde gegevenspunten. Met deze bewerking wordt een model gegenereerd met de gegevenspunten die u verzendt, en wordt bepaald of het doelpunt een anomalie is. Door de API aan te roepen bij elk nieuw gegevenspunt dat u genereert, kunt u uw gegevens bewaken terwijl ze worden gemaakt. |
|Detecteert anomalieën in uw gegevensset als een batch. | Uw tijdreeks gebruiken om eventuele anomalieën in uw gegevens op te sporen. Met deze bewerking wordt een model gegenereerd op basis van uw volledige tijdreeksgegevens, waarbij elk punt wordt geanalyseerd met hetzelfde model.         |
|Detecteert wijzigingspunten in uw gegevensset als een batch. | Uw tijdreeks om eventuele wijzigingspunten in uw gegevens te detecteren. Met deze bewerking wordt een model gegenereerd op basis van uw volledige tijdreeksgegevens, waarbij elk punt wordt geanalyseerd met hetzelfde model.    |
| Aanvullende informatie over het probleem ophalen. | Nuttige informatie over uw gegevens en gedetecteerde anomalieën, waaronder verwachte waarden, grenzen en posities van anomalieën. |
| Grenzen voor anomaliedetectie aanpassen. | Met de Anomaly Detector-API worden automatisch grenzen voor anomaliedetectie gemaakt. U kunt deze grenzen aanpassen om de gevoeligheid van de API voor anomalieën in gegevens te verhogen of verlagen, en te optimaliseren voor uw gegevens. |

## <a name="demo"></a>Demo

Bekijk deze [interactieve demo](https://aka.ms/adDemo) om beter te begrijpen hoe Anomaly Detector werkt.
Als u de demo wilt uitvoeren, moet u een Anomaly Detector-resource maken en de API-sleutel en het eindpunt ophalen.

## <a name="notebook"></a>Notebook

Als u wilt weten hoe u de Anomaly Detector-API aanroept, probeert u dit [notebook](https://aka.ms/adNotebook). Dit Jupyter Notebook laat zien hoe u een API-aanvraag verzendt en het resultaat visualiseert.

Voer de volgende stappen uit om het notebook uit te voeren:

1. Haal een geldige abonnementssleutel en een geldig eindpunt voor de Anomaly Detector-API op. In de volgende sectie vindt u instructies voor het registreren.
1. Meld u aan en klik op Klonen in de rechterbovenhoek.
1. Schakel de optie 'public' in het dialoogvenster uit voordat u de kloonbewerking voltooit, anders is uw notebook, inclusief abonnementssleutels, openbaar.
1. Klik op **Uitvoeren op gratis Compute**
1. Selecteer een van de notebooks.
1. Voeg uw geldige abonnementssleutel voor de Anomaly Detector-API toe aan de variabele `subscription_key`.
1. Wijzig de variabele `endpoint` in uw eindpunt. Bijvoorbeeld: `https://westus2.api.cognitive.microsoft.com/anomalydetector/v1.0/timeseries/last/detect`
1. Klik in de bovenste menubalk op **Cel** en vervolgens op **Alle uitvoeren**.

## <a name="workflow"></a>Werkstroom

De Anomaly Detector-API is een RESTful-webservice die eenvoudig kan worden aangeroepen vanuit elke programmeertaal waarmee HTTP-aanvragen kunnen worden gedaan en JSON kan worden geparseerd.

[!INCLUDE [cognitive-services-anomaly-detector-data-requirements](../../../includes/cognitive-services-anomaly-detector-data-requirements.md)]

[!INCLUDE [cognitive-services-anomaly-detector-signup-requirements](../../../includes/cognitive-services-anomaly-detector-signup-requirements.md)]

Nadat u zich hebt geregistreerd:

1. Converteer uw tijdreeksgegevens naar een geldige JSON-indeling. Bereid uw gegevens voor in overeenstemming met de [best practices](concepts/anomaly-detection-best-practices.md) voor de beste resultaten.
1. Verzend een aanvraag naar de Anomaly Detector-API met uw gegevens.
1. Verwerk de API-reactie door het geretourneerde JSON-bericht te parseren.

## <a name="algorithms"></a>Algoritmen

* Zie de volgende technische blogs voor informatie over de gebruikte algoritmen:
    * [Inleiding tot de Azure Anomaly Detector-API](https://techcommunity.microsoft.com/t5/AI-Customer-Engineering-Team/Introducing-Azure-Anomaly-Detector-API/ba-p/490162)
    * [Overzicht van het SR-CNN-algoritme in Azure Anomaly Detector](https://techcommunity.microsoft.com/t5/AI-Customer-Engineering-Team/Overview-of-SR-CNN-algorithm-in-Azure-Anomaly-Detector/ba-p/982798)

U kunt de paper [Time-Series Anomaly Detection Service at Microsoft](https://arxiv.org/abs/1906.03821) (goedgekeurd door KDD 2019) lezen voor meer informatie over de SR-CNN-algoritmen die zijn ontwikkeld door Microsoft.


> [!VIDEO https://www.youtube.com/embed/ERTaAnwCarM]

## <a name="deploy-on-premises-using-docker-containers"></a>On-premises implementeren met behulp van Docker-containers

[Gebruik Anomaly Detector-containers](anomaly-detector-container-howto.md) om API-functies on-premises te implementeren. Docker-containers stellen u in staat om de service dichter bij uw gegevens te brengen voor naleving, beveiliging en andere operationele redenen.

## <a name="join-the-anomaly-detector-community"></a>Lid worden van de Anomaly Detector-community

* Word lid van de [Anomaly Detector-adviesgroep van Microsoft Teams](https://aka.ms/AdAdvisorsJoin)
* Zie de geselecteerde [door gebruikers gegenereerde inhoud](user-generated-content.md)

## <a name="next-steps"></a>Volgende stappen

* [Snelstart: Anomalieën detecteren in uw tijdreeksgegevens met behulp van de Anomaly Detector-REST API](quickstarts/detect-data-anomalies-csharp.md)
* [Online demo](https://notebooks.azure.com/AzureAnomalyDetection/projects/anomalydetector) voor de Anomaly Detector-API
* [Naslaginformatie over de REST API](https://aka.ms/anomaly-detector-rest-api-ref) van Anomaly Detector
