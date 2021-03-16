---
title: Een Cognitive Services resource maken in de Azure Portal
titleSuffix: Azure Cognitive Services
description: Ga aan de slag met Azure Cognitive Services door een resource te maken en te abonneren in de Azure Portal.
services: cognitive-services
author: aahill
manager: nitinme
keywords: cognitieve services, cognitieve intelligentie, cognitieve oplossingen, AI-services
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 03/15/2021
ms.author: aahi
ms.openlocfilehash: 69c83e9172a8369b7ff31116ee4db74fc33d86bb
ms.sourcegitcommit: 66ce33826d77416dc2e4ba5447eeb387705a6ae5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/15/2021
ms.locfileid: "103472128"
---
# <a name="quickstart-create-a-cognitive-services-resource-using-the-azure-portal"></a>Snelstartgids: een Cognitive Services resource maken met behulp van de Azure Portal

Gebruik deze Quick Start om Azure Cognitive Services te gebruiken. Nadat u een cognitieve service resource hebt gemaakt in de Azure Portal, krijgt u een eind punt en een sleutel voor het verifiëren van uw toepassingen.

Azure Cognitive Services bestaat uit cloudservices met REST API's en clientbibliotheek-SDK's waarmee ontwikkelaars cognitieve intelligentie in toepassingen kunnen bouwen zonder directe kennis of vaardigheden op het gebied van kunstmatige intelligentie (AI) of gegevenswetenschap. Met Azure Cognitive Services kunnen ontwikkelaars eenvoudig cognitieve functies toevoegen aan hun toepassingen met cognitieve oplossingen die kunnen zien, horen, spreken en begrijpen. Er zijn zelfs al toepassingen die beginnen te redeneren.

[!INCLUDE [cognitive-services-subscription-types](../../includes/cognitive-services-subscription-types.md)]

## <a name="prerequisites"></a>Vereisten

* Een geldig Azure-abonnement - [Maak een gratis abonnement](https://azure.microsoft.com/free/cognitive-services/).

## <a name="create-a-new-azure-cognitive-services-resource"></a>Een nieuwe Azure Cognitive Services-resource maken

1. Een resource maken.

### <a name="multi-service-resource"></a>[Resource voor meerdere services](#tab/multiservice)

De resource met meerdere services heeft de naam **Cognitive Services** in de portal. [Maak een Cognitive Services resource](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAllInOne).

Op dit moment biedt de resource met meerdere services toegang tot de volgende Cognitive Services:

* Computer Vision
* Content Moderator
* Face
* Language Understanding (LUIS)
* Tekstanalyse
* Vertaler

### <a name="single-service-resource"></a>[Resource met één service](#tab/singleservice)

Gebruik de onderstaande koppelingen om een resource te maken voor de beschik bare Cognitive Services:

| Vision                      | Speech                  | Taal                          | Besluit             |
|-----------------------------|-------------------------|-----------------------------------|----------------------|
| [Computer Vision](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision)         | [Spraak Services](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeechServices)     | [Insluitende lezer](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesImmersiveReader)              | [Anomaliedetectie](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector) | 
| [Aangepaste Vision-service](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesCustomVision) | [Speaker Recognition](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeakerRecognition) | [Language Understanding (LUIS)](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne) | [Content Moderator](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesContentModerator) | 
| [Face](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFace)                    |                         | [QnA Maker](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker)                     | [Personalizer](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesPersonalizer)     |
| [Ink Recognizer](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesInkRecognizer)        |                         | [Tekstanalyse](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics)                |  [Metrics Advisor](https://go.microsoft.com/fwlink/?linkid=2142156)                    |

---

2. Geef op de pagina **maken** de volgende informatie op:
<!-- markdownlint-disable MD024 -->

### <a name="multi-service-resource"></a>[Resource voor meerdere services](#tab/multiservice)

|Projectgegevens| Beschrijving   |
|--|--|
| **Abonnement** | Selecteer een van de beschik bare Azure-abonnementen. |
| **Resourcegroep** | De Azure-resource groep die uw Cognitive Services-resource bevat. U kunt een nieuwe groep maken of deze toevoegen aan een bestaande groep. |
| **Regio** | De locatie van uw cognitieve service-exemplaar. Verschillende locaties kunnen latentie veroorzaken, maar deze hebben geen invloed op de beschikbaarheid van de runtime van uw resource. |
| **Naam** | Een beschrijvende naam voor uw cognitieve Services-resource. Bijvoorbeeld *MyCognitiveServicesResource*. |
| **Prijscategorie** | De kosten van uw Cognitive Services-account zijn afhankelijk van de opties die u kiest en uw gebruik. Zie [Prijsopgaven](https://azure.microsoft.com/pricing/details/cognitive-services/) voor API's voor meer informatie.

![Scherm voor het maken van resources voor meerdere services](media/cognitive-services-apis-create-account/resource_create_screen-multi.png)

Selecteer **Maken**.

### <a name="single-service-resource"></a>[Resource met één service](#tab/singleservice)

|Projectgegevens| Beschrijving   |
|--|--|
| **Abonnement** | Selecteer een van de beschik bare Azure-abonnementen. |
| **Resourcegroep** | De Azure-resource groep die uw Cognitive Services-resource bevat. U kunt een nieuwe groep maken of deze toevoegen aan een bestaande groep. |
| **Regio** | De locatie van uw cognitieve service-exemplaar. Verschillende locaties kunnen latentie veroorzaken, maar deze hebben geen invloed op de beschikbaarheid van de runtime van uw resource. |
| **Naam** | Een beschrijvende naam voor uw cognitieve Services-resource. Bijvoorbeeld *MyCognitiveServicesResource*. |
| **Prijscategorie** | De kosten van uw Cognitive Services-account zijn afhankelijk van de opties die u kiest en uw gebruik. Zie [Prijsopgaven](https://azure.microsoft.com/pricing/details/cognitive-services/) voor API's voor meer informatie.

![Het scherm voor het maken van een resource voor één service](media/cognitive-services-apis-create-account/resource_create_screen.png)

Selecteer **Maken**.

---

[!INCLUDE [Register Azure resource for subscription](./includes/register-resource-subscription.md)]

## <a name="get-the-keys-for-your-resource"></a>De sleutels voor uw resource ophalen

1. Nadat de implementatie van de resource is voltooid, klikt u in de **volgende stappen** op **Ga naar resource** .

    ![Zoeken naar Cognitive Services](media/cognitive-services-apis-create-account/resource-next-steps.png)

2. In het deel venster Quick start dat wordt geopend, kunt u toegang krijgen tot uw sleutel en eind punt.

    ![Sleutel en eind punt ophalen](media/cognitive-services-apis-create-account/get-cog-serv-keys.png)

[!INCLUDE [cognitive-services-environment-variables](../../includes/cognitive-services-environment-variables.md)]

## <a name="clean-up-resources"></a>Resources opschonen

Als u een Cognitive Services-abonnement wilt opschonen en verwijderen, kunt u de resource of resourcegroep verwijderen. Als u de resourcegroep verwijdert, worden ook eventuele andere bijbehorende resources in de groep verwijderd.

1. Vouw het menu aan de linkerkant in Azure Portal uit om het menu met services te openen en kies **Resourcegroepen** om de lijst met resourcegroepen weer te geven.
2. Zoek de resourcegroep die de resource bevat die u wilt verwijderen
3. Klik met de rechtermuisknop op de vermelding van de resourcegroep. Selecteer **Resourcegroep verwijderen** en bevestig dit.

## <a name="see-also"></a>Zie ook

* [Aanvragen verifiëren bij Azure Cognitive Services](authentication.md)
* [Wat is Azure Cognitive Services?](./what-are-cognitive-services.md)
* [Een nieuwe resource maken met behulp van de Azure Management-client bibliotheek](.\cognitive-services-apis-create-account-client-library.md)
* [Ondersteuning voor natuurlijke taal](language-support.md)
* [Ondersteuning voor Docker-container](cognitive-services-container-support.md)
