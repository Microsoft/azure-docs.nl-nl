---
title: Een Cognitive Services resource maken met behulp van de Azure CLI
titleSuffix: Azure Cognitive Services
description: Ga aan de slag met Azure Cognitive Services door met de Azure-opdracht regel interface een abonnement te maken op een resource.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
keywords: cognitieve services, cognitieve intelligentie, cognitieve oplossingen, AI-services
ms.topic: quickstart
ms.date: 09/14/2020
ms.author: aahi
ms.openlocfilehash: 95d74601ca912647eadd1bd4e1045108be6b2adb
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102050066"
---
# <a name="quickstart-create-a-cognitive-services-resource-using-the-azure-command-line-interfacecli"></a>Snelstartgids: een Cognitive Services resource maken met behulp van de Azure Command-Line interface (CLI)

Gebruik deze Quick Start om aan de slag te gaan met Azure Cognitive Services met behulp van de [Azure-opdracht regel interface (CLI)](/cli/azure/install-azure-cli).

Azure Cognitive Services bestaat uit cloudservices met REST API's en clientbibliotheek-SDK's waarmee ontwikkelaars cognitieve intelligentie in toepassingen kunnen bouwen zonder directe kennis of vaardigheden op het gebied van kunstmatige intelligentie (AI) of gegevenswetenschap. Met Azure Cognitive Services kunnen ontwikkelaars eenvoudig cognitieve functies toevoegen aan hun toepassingen met cognitieve oplossingen die kunnen zien, horen, spreken en begrijpen. Er zijn zelfs al toepassingen die beginnen te redeneren.

Cognitive Services worden vertegenwoordigd door Azure- [resources](../azure-resource-manager/management/manage-resources-portal.md) die u in uw Azure-abonnement hebt gemaakt. Nadat u de resource hebt gemaakt, gebruikt u de sleutels en het eind punt dat u hebt gegenereerd voor het verifiëren van uw toepassingen.

In deze Quick Start leert u hoe u zich kunt registreren voor Azure Cognitive Services en hoe u een account met een single-service of meerdere service-abonnement kunt maken met behulp van de [Azure-opdracht regel interface (CLI)](/cli/azure/install-azure-cli). Deze services worden vertegenwoordigd door Azure- [resources](../azure-resource-manager/management/manage-resources-portal.md), waarmee u verbinding kunt maken met een of meer van de azure-Cognitive Services-API's.

[!INCLUDE [cognitive-services-subscription-types](../../includes/cognitive-services-subscription-types.md)]

## <a name="prerequisites"></a>Vereisten

* Een geldig Azure-abonnement: [Maak er gratis een](https://azure.microsoft.com/free/cognitive-services) .
* De [Azure-opdracht regel interface (CLI)](/cli/azure/install-azure-cli)

## <a name="install-the-azure-cli-and-sign-in"></a>De Azure CLI installeren en aanmelden

Installeer de [Azure CLI](/cli/azure/install-azure-cli). Als u zich wilt aanmelden bij de lokale installatie van de CLI, voert u de opdracht [AZ login](/cli/azure/reference-index#az-login) :

```azurecli-interactive
az login
```

U kunt ook de knop groene **try it** gebruiken om deze opdrachten uit te voeren in uw browser.

## <a name="create-a-new-azure-cognitive-services-resource-group"></a>Een nieuwe Azure Cognitive Services-resource groep maken

Voordat u een Cognitive Services resource maakt, moet u een Azure-resource groep hebben om de resource te kunnen bevatten. Wanneer u een nieuwe resource maakt, hebt u de optie om een nieuwe resource groep te maken of een bestaande te gebruiken. In dit artikel wordt beschreven hoe u een nieuwe resourcegroep maakt.

### <a name="choose-your-resource-group-location"></a>De locatie van de resource groep kiezen

Als u een resource wilt maken, hebt u een van de Azure-locaties die beschikbaar zijn voor uw abonnement. U kunt een lijst met beschik bare locaties ophalen met de opdracht [AZ account list-locations](/cli/azure/account#az-account-list-locations) . De meeste Cognitive Services kunnen vanaf verschillende locaties worden geopend. Kies het account dat het dichtst bij u ligt of Bekijk welke locaties beschikbaar zijn voor de service.

> [!IMPORTANT]
> * Onthoud uw Azure-locatie, omdat u deze nodig hebt wanneer u de Azure Cognitive Services aanroept.
> * De beschik baarheid van sommige Cognitive Services kan per regio verschillen. Zie [Azure-producten per regio](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services)voor meer informatie.

```azurecli-interactive
az account list-locations \
    --query "[].{Region:name}" \
    --out table
```

Nadat u uw Azure-locatie hebt, maakt u een nieuwe resource groep in de Azure CLI met behulp van de opdracht [AZ Group Create](/cli/azure/group#az-group-create) .

Vervang in het onderstaande voor beeld de Azure-locatie door `westus2` een van de Azure-locaties die beschikbaar zijn voor uw abonnement.

```azurecli-interactive
az group create \
    --name cognitive-services-resource-group \
    --location westus2
```

## <a name="create-a-cognitive-services-resource"></a>Een Cognitive Services-resource maken

### <a name="choose-a-cognitive-service-and-pricing-tier"></a>Een cognitieve service en prijs categorie kiezen

Wanneer u een nieuwe resource maakt, moet u weten wat de soort service is die u wilt gebruiken, samen met de gewenste [prijs categorie](https://azure.microsoft.com/pricing/details/cognitive-services/) (of SKU). U gebruikt deze en andere informatie als para meters bij het maken van de resource.

### <a name="multi-service"></a>Multi-service

| Service                    | Soort                      |
|----------------------------|---------------------------|
| Meerdere services. Zie de pagina met [prijzen](https://azure.microsoft.com/pricing/details/cognitive-services/) voor meer informatie.            | `CognitiveServices`     |


> [!NOTE]
> Veel van de onderstaande Cognitive Services hebben een gratis laag die u kunt gebruiken om de service te proberen. Als u de gratis laag wilt gebruiken, gebruikt u `F0` als de SKU voor uw resource.

### <a name="vision"></a>Vision

| Service                    | Soort                      |
|----------------------------|---------------------------|
| Computer Vision            | `ComputerVision`          |
| Custom Vision - Voorspelling | `CustomVision.Prediction` |
| Custom Vision - Training   | `CustomVision.Training`   |
| Face                       | `Face`                    |
| Form Recognizer            | `FormRecognizer`          |
| Ink Recognizer             | `InkRecognizer`           |

### <a name="search"></a>Search

| Service            | Soort                  |
|--------------------|-----------------------|
| Bing Automatische suggesties   | `Bing.Autosuggest.v7` |
| Bing Aangepaste zoekopdrachten | `Bing.CustomSearch`   |
| Bing Entiteiten zoeken | `Bing.EntitySearch`   |
| Bing Search        | `Bing.Search.v7`      |
| Bing Spellingcontrole   | `Bing.SpellCheck.v7`  |

### <a name="speech"></a>Spraak

| Service            | Soort                 |
|--------------------|----------------------|
| Speech Services    | `SpeechServices`     |
| Spraakherkenning | `SpeakerRecognition` |

### <a name="language"></a>Taal

| Service            | Soort                |
|--------------------|---------------------|
| Informatie over formulier | `FormUnderstanding` |
| LUIS               | `LUIS`              |
| QnA Maker          | `QnAMaker`          |
| Tekstanalyse     | `TextAnalytics`     |
| Tekstomzetting   | `TextTranslation`   |

### <a name="decision"></a>Besluit

| Service           | Soort               |
|-------------------|--------------------|
| Anomaly Detector  | `AnomalyDetector`  |
| Content Moderator | `ContentModerator` |
| Personalizer      | `Personalizer`     |

U vindt een lijst met ' soorten ' beschik bare cognitieve service ' met de opdracht [AZ cognitiveservices account list-typen](/cli/azure/cognitiveservices/account#az-cognitiveservices-account-list-kinds) :

```azurecli-interactive
az cognitiveservices account list-kinds
```

### <a name="add-a-new-resource-to-your-resource-group"></a>Een nieuwe resource toevoegen aan de resource groep

Als u een nieuwe Cognitive Services resource wilt maken en hierop wilt abonneren, gebruikt u de opdracht [AZ cognitiveservices account create](/cli/azure/cognitiveservices/account#az-cognitiveservices-account-create) . Met deze opdracht wordt een nieuwe factureer bare resource toegevoegd aan de resource groep die u eerder hebt gemaakt. Wanneer u een nieuwe resource maakt, moet u weten wat de soort service is die u wilt gebruiken, samen met de prijs categorie (of SKU) en een Azure-locatie:

U kunt een F0 (gratis) resource maken voor anomalie detectie, `anomaly-detector-resource` met de naam met de onderstaande opdracht.

```azurecli-interactive
az cognitiveservices account create \
    --name anomaly-detector-resource \
    --resource-group cognitive-services-resource-group \
    --kind AnomalyDetector \
    --sku F0 \
    --location westus2 \
    --yes
```

[!INCLUDE [Register Azure resource for subscription](./includes/register-resource-subscription.md)]

## <a name="get-the-keys-for-your-resource"></a>De sleutels voor uw resource ophalen

Als u zich wilt aanmelden bij de lokale installatie van de Command-Line interface (CLI), gebruikt u de opdracht [AZ login](/cli/azure/reference-index#az-login) .

```azurecli-interactive
az login
```

Gebruik de opdracht [AZ cognitiveservices account Keys List](/cli/azure/cognitiveservices/account/keys#az-cognitiveservices-account-keys-list) om de sleutels voor uw cognitieve service resource op te halen.

```azurecli-interactive
    az cognitiveservices account keys list \
    --name anomaly-detector-resource \
    --resource-group cognitive-services-resource-group
```

[!INCLUDE [cognitive-services-environment-variables](../../includes/cognitive-services-environment-variables.md)]

## <a name="pricing-tiers-and-billing"></a>Prijscategorieën en facturering

Prijscategorieën (en het bedrag dat in rekening wordt gebracht) zijn gebaseerd op het aantal transacties dat u verzendt met behulp van uw verificatiegegevens. Voor elke prijsklasse wordt het volgende gespecificeerd:
* het maximumaantal toegestane transacties per seconde (TPS).
* servicefuncties die zijn ingeschakeld binnen de prijscategorie.
* De kosten voor een vooraf gedefinieerd bedrag aan trans acties. Boven deze hoeveelheid worden er extra kosten in rekening gebracht, zoals is opgegeven in de [prijs informatie](https://azure.microsoft.com/pricing/details/cognitive-services/custom-vision-service/) voor uw service.

## <a name="get-current-quota-usage-for-your-resource"></a>Huidig quotum gebruik voor uw resource ophalen

Gebruik de opdracht [AZ cognitiveservices account list-Usage](/cli/azure/cognitiveservices/account#az-cognitiveservices-account-list-usage) om het gebruik van uw cognitieve service resource op te halen.

```azurecli-interactive
az cognitiveservices account list-usage \
    --name anomaly-detector-resource \
    --resource-group cognitive-services-resource-group \
    --subscription subscription-name
```

## <a name="clean-up-resources"></a>Resources opschonen

Als u een Cognitive Services resource wilt opschonen en verwijderen, kunt u deze of de resource groep verwijderen. Als u de resourcegroep verwijdert, worden ook eventuele andere bijbehorende resources in de groep verwijderd.

Als u de resource groep en de bijbehorende resources wilt verwijderen, gebruikt u de opdracht AZ Group Delete.

```azurecli-interactive
az group delete --name cognitive-services-resource-group
```

## <a name="see-also"></a>Zie ook

* [Aanvragen verifiëren bij Azure Cognitive Services](authentication.md)
* [Wat is Azure Cognitive Services?](./what-are-cognitive-services.md)
* [Ondersteuning voor natuurlijke taal](language-support.md)
* [Ondersteuning voor Docker-container](cognitive-services-container-support.md)
