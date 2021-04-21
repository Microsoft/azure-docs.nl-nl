---
title: Een resource Cognitive Services maken met behulp van de Azure CLI
titleSuffix: Azure Cognitive Services
description: Ga aan de Azure Cognitive Services door een resource te maken en u te abonneren op een resource met behulp van de Azure-opdrachtregelinterface.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
keywords: cognitieve services, cognitieve intelligentie, cognitieve oplossingen, AI-services
ms.topic: quickstart
ms.date: 3/22/2021
ms.author: aahi
ms.openlocfilehash: 26e3b264b7268f7a9ffdb592beef7d76844646f5
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107789137"
---
# <a name="quickstart-create-a-cognitive-services-resource-using-the-azure-command-line-interfacecli"></a>Quickstart: Een Cognitive Services maken met behulp van de Azure Command-Line Interface (CLI)

Gebruik deze quickstart om aan de slag te Azure Cognitive Services met behulp van [de Azure-opdrachtregelinterface (CLI).](/cli/azure/install-azure-cli)

Azure Cognitive Services bestaat uit cloudservices met REST API's en clientbibliotheek-SDK's waarmee ontwikkelaars cognitieve intelligentie in toepassingen kunnen bouwen zonder directe kennis of vaardigheden op het gebied van kunstmatige intelligentie (AI) of gegevenswetenschap. Met Azure Cognitive Services kunnen ontwikkelaars eenvoudig cognitieve functies toevoegen aan hun toepassingen met cognitieve oplossingen die kunnen zien, horen, spreken en begrijpen. Er zijn zelfs al toepassingen die beginnen te redeneren.

Cognitive Services worden vertegenwoordigd door [Azure-resources die](../azure-resource-manager/management/manage-resources-portal.md) u in uw Azure-abonnement maakt. Nadat u de resource hebt gemaakt, gebruikt u de sleutels en het eindpunt die voor u zijn gegenereerd om uw toepassingen te verifiëren.

In deze quickstart leert u hoe u zich kunt registreren voor Azure Cognitive Services en een account kunt maken met één service of abonnement voor meerdere service, met behulp van de Azure-opdrachtregelinterface [(CLI).](/cli/azure/install-azure-cli) Deze services worden vertegenwoordigd door [Azure-resources,](../azure-resource-manager/management/manage-resources-portal.md)waarmee u verbinding kunt maken met een of meer van de Azure Cognitive Services API's.

[!INCLUDE [cognitive-services-subscription-types](../../includes/cognitive-services-subscription-types.md)]

## <a name="prerequisites"></a>Vereisten

* Een geldig Azure-abonnement: [maak er gratis](https://azure.microsoft.com/free/cognitive-services) een.
* De [Azure-opdrachtregelinterface (CLI)](/cli/azure/install-azure-cli)

## <a name="install-the-azure-cli-and-sign-in"></a>De Azure CLI installeren en u aanmelden

Installeer de [Azure CLI](/cli/azure/install-azure-cli). Voer de opdracht [az login](/cli/azure/reference-index#az_login) uit om u aan te melden bij de lokale installatie van de CLI:

```azurecli-interactive
az login
```

U kunt ook de groene knop **Uitproberen gebruiken** om deze opdrachten in uw browser uit te voeren.

## <a name="create-a-new-azure-cognitive-services-resource-group"></a>Een nieuwe resourcegroep Azure Cognitive Services maken

Voordat u een Cognitive Services resource maakt, moet u een Azure-resourcegroep hebben die de resource bevat. Wanneer u een nieuwe resource maakt, hebt u de mogelijkheid om een nieuwe resourcegroep te maken of een bestaande resourcegroep te gebruiken. In dit artikel wordt beschreven hoe u een nieuwe resourcegroep maakt.

### <a name="choose-your-resource-group-location"></a>Kies de locatie van uw resourcegroep

Als u een resource wilt maken, hebt u een van de Azure-locaties nodig die beschikbaar zijn voor uw abonnement. U kunt een lijst met beschikbare locaties ophalen met de [opdracht az account list-locations.](/cli/azure/account#az_account_list_locations) De Cognitive Services zijn toegankelijk vanaf verschillende locaties. Kies de locatie die het dichtst bij u in de buurt is of kijk welke locaties beschikbaar zijn voor de service.

> [!IMPORTANT]
> * Onthoud uw Azure-locatie, omdat u deze nodig hebt bij het aanroepen van Azure Cognitive Services.
> * De beschikbaarheid van sommige Cognitive Services kan per regio verschillen. Zie Azure-producten per [regio voor meer informatie.](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services)

```azurecli-interactive
az account list-locations \
    --query "[].{Region:name}" \
    --out table
```

Nadat u uw Azure-locatie hebt, maakt u een nieuwe resourcegroep in de Azure CLI met behulp van [de opdracht az group create.](/cli/azure/group#az_group_create)

Vervang in het onderstaande voorbeeld de Azure-locatie `westus2` door een van de Beschikbare Azure-locaties voor uw abonnement.

```azurecli-interactive
az group create \
    --name cognitive-services-resource-group \
    --location westus2
```

## <a name="create-a-cognitive-services-resource"></a>Een Cognitive Services-resource maken

### <a name="choose-a-cognitive-service-and-pricing-tier"></a>Een cognitieve service en prijscategorie kiezen

Wanneer u een nieuwe resource maakt, moet u weten welk soort service u wilt gebruiken, samen met de prijscategorie [(of](https://azure.microsoft.com/pricing/details/cognitive-services/) SKU) die u wilt gebruiken. U gebruikt deze en andere informatie als parameters bij het maken van de resource.

### <a name="multi-service"></a>Multi-service

| Service                    | Soort                      |
|----------------------------|---------------------------|
| Meerdere services. Zie de [pagina met](https://azure.microsoft.com/pricing/details/cognitive-services/) prijzen voor meer informatie.            | `CognitiveServices`     |


> [!NOTE]
> Veel van de Cognitive Services hebben een gratis laag die u kunt gebruiken om de service uit te proberen. Als u de gratis laag wilt gebruiken, gebruikt `F0` u als de SKU voor uw resource.

### <a name="vision"></a>Vision

| Service                    | Soort                      |
|----------------------------|---------------------------|
| Computer Vision            | `ComputerVision`          |
| Custom Vision - Voorspelling | `CustomVision.Prediction` |
| Custom Vision - Training   | `CustomVision.Training`   |
| Face                       | `Face`                    |
| Form Recognizer            | `FormRecognizer`          |
| Ink Recognizer             | `InkRecognizer`           |

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

U vindt een lijst met beschikbare soorten Cognitive Service met de [opdracht az cognitiveservices account list-kinds:](/cli/azure/cognitiveservices/account#az_cognitiveservices_account_list_kinds)

```azurecli-interactive
az cognitiveservices account list-kinds
```

### <a name="add-a-new-resource-to-your-resource-group"></a>Een nieuwe resource toevoegen aan uw resourcegroep

Gebruik de opdracht [az cognitiveservices account create](/cli/azure/cognitiveservices/account#az_cognitiveservices_account_create) om Cognitive Services nieuwe resource te maken en u te abonneren. Met deze opdracht wordt een nieuwe factureerbare resource toegevoegd aan de resourcegroep die u eerder hebt gemaakt. Wanneer u uw nieuwe resource maakt, moet u weten welk soort service u wilt gebruiken, samen met de prijscategorie (of SKU) en een Azure-locatie:

U kunt een F0-resource (gratis) maken voor Anomaly Detector, met de naam `anomaly-detector-resource` met de onderstaande opdracht.

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

Als u zich wilt aanmelden bij uw lokale installatie van Command-Line Interface(CLI), gebruikt u [de opdracht az login.](/cli/azure/reference-index#az_login)

```azurecli-interactive
az login
```

Gebruik de [opdracht az cognitiveservices account keys list](/cli/azure/cognitiveservices/account/keys#az_cognitiveservices_account_keys_list) om de sleutels voor uw Cognitive Service-resource op te halen.

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
* De kosten voor een vooraf gedefinieerd aantal transacties. Als u dit bedrag boven dit bedrag komt, worden extra kosten in rekening brengen, zoals opgegeven in de [prijsgegevens](https://azure.microsoft.com/pricing/details/cognitive-services/custom-vision-service/) voor uw service.

## <a name="get-current-quota-usage-for-your-resource"></a>Huidig quotumgebruik voor uw resource op halen

Gebruik de [opdracht az cognitiveservices account list-usage](/cli/azure/cognitiveservices/account#az_cognitiveservices_account_list_usage) om het gebruik voor uw Cognitive Service-resource op te halen.

```azurecli-interactive
az cognitiveservices account list-usage \
    --name anomaly-detector-resource \
    --resource-group cognitive-services-resource-group \
    --subscription subscription-name
```

## <a name="clean-up-resources"></a>Resources opschonen

Als u een resource wilt ops schonen en Cognitive Services verwijderen, kunt u deze of de resourcegroep verwijderen. Als u de resourcegroep verwijdert, worden ook eventuele andere bijbehorende resources in de groep verwijderd.

Als u de resourcegroep en de bijbehorende resources wilt verwijderen, gebruikt u de opdracht az group delete.

```azurecli-interactive
az group delete --name cognitive-services-resource-group
```

## <a name="see-also"></a>Zie ook

* Zie **[Aanvragen verifiëren voor Azure Cognitive Services](authentication.md)** over het veilig werken met Cognitive Services.
* Zie **[Wat zijn Azure Cognitive Services?](./what-are-cognitive-services.md)** voor een lijst met verschillende categorieën binnen Cognitive Services.
* Zie **[Ondersteuning voor natuurlijke taal](language-support.md)** voor een overzicht van de natuurlijke talen die Cognitive Services ondersteunt.
* Zie **[Use Cognitive Services as containers (Gebruik](cognitive-services-container-support.md)** Cognitive Services containers) om te begrijpen hoe u Cognitive Services on-Cognitive Services gebruikt.
* Zie **[Kosten plannen en beheren voor Cognitive Services](plan-manage-costs.md)** kosten van het gebruik van Cognitive Services.
