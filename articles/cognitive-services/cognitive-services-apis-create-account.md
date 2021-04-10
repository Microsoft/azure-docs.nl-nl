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
ms.openlocfilehash: af957758918b99dcb44732eb536c0ca031231a7a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104868219"
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

* **Vision** -Computer Vision, Custom Vision, formulier Recognizer, Face
* **Spraak** : spraak
* **Language** -language UNDERSTANDING (Luis), Text Analytics, Translator
* **Beslissing** : personaler, content moderator

### <a name="single-service-resource"></a>[Resource met één service](#tab/singleservice)

Gebruik de onderstaande koppelingen om een resource te maken voor de beschik bare Cognitive Services:

| Vision                      | Speech                  | Taal                          | Besluit             |
|-----------------------------|-------------------------|-----------------------------------|----------------------|
| [Computer Vision](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision)         | [Spraakservices](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeechServices)     | [Insluitende lezer](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesImmersiveReader)              | [Anomaliedetectie](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector) | 
| [Aangepaste Vision-service](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesCustomVision) |  | [Language Understanding (LUIS)](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne) | [Content Moderator](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesContentModerator) | 
| [Face](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFace)                    |                         | [QnA Maker](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker)                     | [Personalizer](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesPersonalizer)     |
| [Form Recognizer](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer)        |                         | [Tekstanalyse](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics)                |  [Metrics Advisor](https://go.microsoft.com/fwlink/?linkid=2142156)                    |
| | | [Translator](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextTranslation) | |

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

<!--![Multi-service resource creation screen](media/cognitive-services-apis-create-account/resource_create_screen-multi.png)-->
:::image type="content" source="media/cognitive-services-apis-create-account/resource_create_screen-multi.png" alt-text="Scherm voor het maken van meerdere service resources":::

Lees en accepteer de voor waarden (indien van toepassing) en selecteer vervolgens **controleren + maken**.

### <a name="single-service-resource"></a>[Resource met één service](#tab/singleservice)

|Projectgegevens| Beschrijving   |
|--|--|
| **Abonnement** | Selecteer een van de beschik bare Azure-abonnementen. |
| **Resourcegroep** | De Azure-resource groep die uw Cognitive Services-resource bevat. U kunt een nieuwe groep maken of deze toevoegen aan een bestaande groep. |
| **Regio** | De locatie van uw cognitieve service-exemplaar. Verschillende locaties kunnen latentie veroorzaken, maar deze hebben geen invloed op de beschikbaarheid van de runtime van uw resource. |
| **Naam** | Een beschrijvende naam voor uw cognitieve Services-resource. Bijvoorbeeld *MyCognitiveServicesResource*. |
| **Prijscategorie** | De kosten van uw Cognitive Services-account zijn afhankelijk van de opties die u kiest en uw gebruik. Zie [Prijsopgaven](https://azure.microsoft.com/pricing/details/cognitive-services/) voor API's voor meer informatie.

<!--![Single-service resource creation screen](media/cognitive-services-apis-create-account/resource_create_screen.png)-->
:::image type="content" source="media/cognitive-services-apis-create-account/resource_create_screen.png" alt-text="Het scherm voor het maken van een resource voor één service":::

Selecteer **volgende: Virtual Network** en kies het type netwerk toegang dat u voor uw resource wilt toestaan, en selecteer vervolgens **controleren + maken**.

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

* Zie **[aanvragen verifiëren voor Azure Cognitive Services](authentication.md)** over het veilig werken met Cognitive Services.
* Zie **[Wat is Azure Cognitive Services?](./what-are-cognitive-services.md)** voor een lijst met verschillende categorieën in cognitive Services.
* Zie **[ondersteuning voor natuurlijke](language-support.md)** talen voor een overzicht van de natuurlijke talen die Cognitive Services ondersteunt.
* Zie **[Cognitive Services als containers gebruiken](cognitive-services-container-support.md)** voor meer informatie over het gebruik van Cognitive Services on-premises.
* Zie **[kosten plannen en beheren voor Cognitive Services](plan-manage-costs.md)** voor het schatten van de kosten van het gebruik van Cognitive Services.
