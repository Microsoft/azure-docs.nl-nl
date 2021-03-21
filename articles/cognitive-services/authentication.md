---
title: Verificatie
titleSuffix: Azure Cognitive Services
description: 'Er zijn drie manieren om een aanvraag te verifiëren voor een Azure Cognitive Services-resource: een abonnements sleutel, een Bearer-token of een multi-service abonnement. In dit artikel vindt u informatie over elke methode en het maken van een aanvraag.'
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 11/22/2019
ms.author: erhopf
ms.openlocfilehash: c7aeb9e9f4de7b4de62f9b5a8da6d997e32a2399
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94363320"
---
# <a name="authenticate-requests-to-azure-cognitive-services"></a>Aanvragen verifiëren bij Azure Cognitive Services

Elke aanvraag voor een Azure cognitieve service moet een verificatie header bevatten. Deze header wordt door gegeven aan een abonnements sleutel of toegangs token, die wordt gebruikt om uw abonnement voor een service of groep services te valideren. In dit artikel vindt u meer informatie over drie manieren om een aanvraag te verifiëren en de vereisten voor elke.

* Verifiëren met een sleutel van [één service](#authenticate-with-a-single-service-subscription-key) of [meerdere](#authenticate-with-a-multi-service-subscription-key) abonnementen
* Verifiëren met een [token](#authenticate-with-an-authentication-token)
* Verifiëren met [Azure Active Directory (Aad)](#authenticate-with-azure-active-directory)

## <a name="prerequisites"></a>Vereisten

Voordat u een aanvraag doet, hebt u een Azure-account en een Azure Cognitive Services-abonnement nodig. Als u al een account hebt, gaat u verder met de volgende sectie. Als u geen account hebt, kunt u in een paar minuten aan de slag met de volgende opties: [een Cognitive Services-account maken voor Azure](cognitive-services-apis-create-account.md).

U kunt uw abonnements sleutel ophalen uit de [Azure Portal](cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) nadat u [uw account hebt gemaakt](https://azure.microsoft.com/free/cognitive-services/).

## <a name="authentication-headers"></a>Verificatie headers

U kunt snel de verificatie headers bekijken die beschikbaar zijn voor gebruik met Azure Cognitive Services.

| Header | Beschrijving |
|--------|-------------|
| Ocp-Apim-Subscription-Key | Gebruik deze header om te verifiëren met een abonnements sleutel voor een specifieke service of een sleutel van een abonnement op meerdere services. |
| OCP-APIM-abonnement-regio | Deze header is alleen vereist bij het gebruik van een sleutel voor meerdere services met de [Translator-service](./Translator/reference/v3-0-reference.md). Gebruik deze header om de regio van het abonnement op te geven. |
| Autorisatie | Gebruik deze header als u een verificatie token gebruikt. De stappen voor het uitvoeren van een token uitwisseling worden beschreven in de volgende secties. De waarde die wordt gegeven, heeft de volgende indeling: `Bearer <TOKEN>` . |

## <a name="authenticate-with-a-single-service-subscription-key"></a>Verifiëren met een abonnements sleutel van één service

De eerste optie is het verifiëren van een aanvraag met een abonnements sleutel voor een specifieke service, zoals Translator. De sleutels zijn beschikbaar in het Azure Portal voor elke resource die u hebt gemaakt. Als u een abonnements sleutel wilt gebruiken om een aanvraag te verifiëren, moet deze worden door gegeven aan de `Ocp-Apim-Subscription-Key` header.

In deze voorbeeld aanvragen ziet u hoe u de `Ocp-Apim-Subscription-Key` koptekst kunt gebruiken. Houd er rekening mee dat u bij het gebruik van dit voor beeld een geldige abonnements sleutel moet toevoegen.

Dit is een voor beeld van een aanroep van de Bing Webzoekopdrachten-API:
```cURL
curl -X GET 'https://api.cognitive.microsoft.com/bing/v7.0/search?q=Welsch%20Pembroke%20Corgis' \
-H 'Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY' | json_pp
```

Dit is een voor beeld van een aanroep van de Translator-service:
```cURL
curl -X POST 'https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=de' \
-H 'Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY' \
-H 'Content-Type: application/json' \
--data-raw '[{ "text": "How much for the cup of coffee?" }]' | json_pp
```

In de volgende video wordt gedemonstreerd met behulp van een Cognitive Services sleutel.

## <a name="authenticate-with-a-multi-service-subscription-key"></a>Verifiëren met een sleutel voor meerdere service abonnementen

>[!WARNING]
> Op dit moment bieden deze services **geen** ondersteuning voor meerdere service sleutels: QnA Maker, spraak services, Custom Vision en anomalie detectie.

Deze optie gebruikt ook een abonnements sleutel voor het verifiëren van aanvragen. Het belangrijkste verschil is dat een abonnements sleutel niet is gekoppeld aan een specifieke service, maar één sleutel kan worden gebruikt om aanvragen voor meerdere Cognitive Services te verifiëren. Zie [Cognitive Services prijzen](https://azure.microsoft.com/pricing/details/cognitive-services/) voor informatie over regionale Beschik baarheid, ondersteunde functies en prijzen.

De abonnements sleutel wordt in elke aanvraag gegeven als de `Ocp-Apim-Subscription-Key` header.

[![Demonstratie van een sleutel voor meerdere service abonnementen voor Cognitive Services](./media/index/single-key-demonstration-video.png)](https://www.youtube.com/watch?v=psHtA1p7Cas&feature=youtu.be)

### <a name="supported-regions"></a>Ondersteunde regio’s

Wanneer u de sleutel voor meerdere service abonnementen gebruikt om een aanvraag in te stellen `api.cognitive.microsoft.com` , moet u de regio in de URL toevoegen. Bijvoorbeeld: `westus.api.cognitive.microsoft.com`.

Wanneer u een sleutel voor meerdere services met de Translator-service gebruikt, moet u de regio van het abonnement met de `Ocp-Apim-Subscription-Region` header opgeven.

Verificatie met meerdere services wordt in deze regio's ondersteund:

- `australiaeast`
- `brazilsouth`
- `canadacentral`
- `centralindia`
- `eastasia`
- `eastus`
- `japaneast`
- `northeurope`
- `southcentralus`
- `southeastasia`
- `uksouth`
- `westcentralus`
- `westeurope`
- `westus`
- `westus2`

### <a name="sample-requests"></a>Voorbeeld aanvragen

Dit is een voor beeld van een aanroep van de Bing Webzoekopdrachten-API:

```cURL
curl -X GET 'https://YOUR-REGION.api.cognitive.microsoft.com/bing/v7.0/search?q=Welsch%20Pembroke%20Corgis' \
-H 'Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY' | json_pp
```

Dit is een voor beeld van een aanroep van de Translator-service:

```cURL
curl -X POST 'https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=de' \
-H 'Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY' \
-H 'Ocp-Apim-Subscription-Region: YOUR_SUBSCRIPTION_REGION' \
-H 'Content-Type: application/json' \
--data-raw '[{ "text": "How much for the cup of coffee?" }]' | json_pp
```

## <a name="authenticate-with-an-authentication-token"></a>Verifiëren met een verificatie token

Sommige Azure Cognitive Services accepteren, en in sommige gevallen is een verificatie token vereist. Deze services ondersteunen momenteel verificatie tokens:

* API voor tekst omzetting
* Speech Services: spraak-naar-tekst REST API
* Speech Services: REST API van tekst naar spraak

>[!NOTE]
> QnA Maker gebruikt ook de autorisatie-header, maar hiervoor is een eindpunt sleutel vereist. Zie [QnA Maker: antwoord ophalen uit de Knowledge Base](./qnamaker/quickstarts/get-answer-from-knowledge-base-using-url-tool.md)voor meer informatie.

>[!WARNING]
> De services die verificatie tokens ondersteunen, kunnen na verloop van tijd veranderen, Controleer de API-verwijzing voor een service voordat u deze verificatie methode gebruikt.

Voor verificatie tokens kunnen zowel één service als sleutels voor meerdere abonnementen worden uitgewisseld. Verificatie tokens zijn 10 minuten geldig.

Verificatie tokens worden opgenomen in een aanvraag als de `Authorization` header. De gegeven token waarde moet worden voorafgegaan door `Bearer` , bijvoorbeeld: `Bearer YOUR_AUTH_TOKEN` .

### <a name="sample-requests"></a>Voorbeeld aanvragen

Gebruik deze URL voor het uitwisselen van een abonnements sleutel voor een verificatie token: `https://YOUR-REGION.api.cognitive.microsoft.com/sts/v1.0/issueToken` .

```cURL
curl -v -X POST \
"https://YOUR-REGION.api.cognitive.microsoft.com/sts/v1.0/issueToken" \
-H "Content-type: application/x-www-form-urlencoded" \
-H "Content-length: 0" \
-H "Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY"
```

Deze meerdere service regio's ondersteunen token uitwisseling:

- `australiaeast`
- `brazilsouth`
- `canadacentral`
- `centralindia`
- `eastasia`
- `eastus`
- `japaneast`
- `northeurope`
- `southcentralus`
- `southeastasia`
- `uksouth`
- `westcentralus`
- `westeurope`
- `westus`
- `westus2`

Nadat u een verificatie token hebt ontvangen, moet u dit in elke aanvraag door geven als de `Authorization` header. Dit is een voor beeld van een aanroep van de Translator-service:

```cURL
curl -X POST 'https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=de' \
-H 'Authorization: Bearer YOUR_AUTH_TOKEN' \
-H 'Content-Type: application/json' \
--data-raw '[{ "text": "How much for the cup of coffee?" }]' | json_pp
```

[!INCLUDE [](../../includes/cognitive-services-azure-active-directory-authentication.md)]

## <a name="see-also"></a>Zie ook

* [Wat zijn cognitieve services?](./what-are-cognitive-services.md)
* [Prijzen van Cognitive Services](https://azure.microsoft.com/pricing/details/cognitive-services/)
* [Aangepaste subdomeinen](cognitive-services-custom-subdomains.md)