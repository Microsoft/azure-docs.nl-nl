---
title: 'Quickstart: Azure Management-clientbibliotheek voor Python'
description: Ga in deze quickstart aan de slag met de Azure Management-clientbibliotheek voor Python.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 08/05/2020
ms.author: pafarley
ms.openlocfilehash: 521f6c39a10259b09d741a61dcd8e81d8a0c35b2
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/28/2021
ms.locfileid: "98948325"
---
[Referentiedocumentatie](/python/api/azure-mgmt-cognitiveservices/azure.mgmt.cognitiveservices) | [Broncode bibliotheek](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-mgmt-cognitiveservices) | [Package (PyPi)](https://pypi.org/project/azure-mgmt-cognitiveservices/) | [Voorbeelden](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-mgmt-cognitiveservices/tests)

## <a name="python-prerequisites"></a>Python-vereisten

* Een geldig Azure-abonnement - [Maak een gratis abonnement](https://azure.microsoft.com/free/).
* [Python 3.x](https://www.python.org/)

[!INCLUDE [Create a service principal](./create-service-principal.md)]

[!INCLUDE [Create a resource group](./create-resource-group.md)]

## <a name="create-a-new-python-application"></a>Een nieuwe Python-toepassing maken

Maak een nieuwe Python-toepassing in uw voorkeurseditor of IDE en navigeer naar uw project in een consolevenster.

### <a name="install-the-client-library"></a>De clientbibliotheek installeren

U kunt de clientbibliotheek installeren met:

```console
pip install azure-mgmt-cognitiveservices
```

Als u de Visual Studio-IDE, gebruikt, is de clientbibliotheek beschikbaar als downloadbaar NuGet-pakket.

### <a name="import-libraries"></a>Bibliotheken importeren

Open uw Python-script en importeer de volgende bibliotheken.

[!code-python[](~/cognitive-services-quickstart-code/python/azure_management_service/create_delete_resource.py?name=snippet_imports)]

## <a name="authenticate-the-client"></a>De client verifiëren

Voeg de volgende velden toe aan de hoofdmap van uw script en vul hun waarden in met behulp van de service-principal die u hebt gemaakt en uw Azure-accountgegevens.

[!code-python[](~/cognitive-services-quickstart-code/python/azure_management_service/create_delete_resource.py?name=snippet_constants)]

Voeg vervolgens de volgende code toe om een ​​**CognitiveServicesManagementClient**-object te maken. Dit object is nodig voor al uw Azure Management-bewerkingen.

[!code-python[](~/cognitive-services-quickstart-code/python/azure_management_service/create_delete_resource.py?name=snippet_auth)]

## <a name="create-a-cognitive-services-resource-python"></a>Een Cognitive Services-resource maken (Python)

Als u een nieuwe Cognitive Services-resource wilt maken en zich hierop wilt abonneren, gebruikt u de functie **Maken**. Met deze functie voegt u een nieuwe factureerbare resource toe aan de resourcegroep die u doorgeeft. Wanneer u uw nieuwe resource maakt, moet u weten welk soort service u wilt gebruiken, samen met de prijscategorie (of SKU) en een Azure-locatie. Met de volgende functie worden al deze argumenten gebruikt en wordt een resource gemaakt.

[!code-python[](~/cognitive-services-quickstart-code/python/azure_management_service/create_delete_resource.py?name=snippet_create)]

### <a name="choose-a-service-and-pricing-tier"></a>Een service en prijscategorie kiezen

Wanneer u een nieuwe resource maakt, moet u weten welk soort service u wilt gebruiken, samen met de [prijscategorie](https://azure.microsoft.com/pricing/details/cognitive-services/) (of SKU) die u wilt. U gebruikt deze en andere informatie als parameters bij het maken van de resource. De volgende functie geeft een overzicht van de beschikbare soorten Cognitive Service.

[!code-python[](~/cognitive-services-quickstart-code/python/azure_management_service/create_delete_resource.py?name=snippet_list_avail)]

[!INCLUDE [cognitive-services-subscription-types](../../../../includes/cognitive-services-subscription-types.md)]

[!INCLUDE [SKUs and pricing](./sku-pricing.md)]

## <a name="view-your-resources"></a>Uw resources weergeven

Als u alle resources onder uw Azure-account (voor alle resourcegroepen) wilt weergeven, gebruikt u de volgende functie:

[!code-python[](~/cognitive-services-quickstart-code/python/azure_management_service/create_delete_resource.py?name=snippet_list)]

## <a name="delete-a-resource"></a>Een resource verwijderen

Met de volgende functie verwijdert u de opgegeven resource uit de opgegeven resourcegroep.

[!code-python[](~/cognitive-services-quickstart-code/python/azure_management_service/create_delete_resource.py?name=snippet_delete)]

## <a name="call-management-functions"></a>Beheerfuncties voor aanroep

Voeg de volgende code toe onderin het script om de bovenstaande functies aan te roepen. Deze code geeft een overzicht van de beschikbare resources, maakt een voorbeeldresource, geeft een overzicht van uw eigen resources en verwijdert vervolgens de voorbeeldresource.

[!code-python[](~/cognitive-services-quickstart-code/python/azure_management_service/create_delete_resource.py?name=snippet_calls)]

## <a name="run-the-application"></a>De toepassing uitvoeren

Voer de toepassing uit vanaf de opdrachtregel met de opdracht `python`.

```console
python <your-script-name>.py
```

## <a name="see-also"></a>Zie ook

* [Referentiedocumentatie voor Azure Management SDK](/python/api/azure-mgmt-cognitiveservices/azure.mgmt.cognitiveservices)
* [Wat zijn Azure Cognitive Services?](../../what-are-cognitive-services.md)
* [Aanvragen verifiëren bij Azure Cognitive Services](../../authentication.md)
* [Een nieuwe resource maken met de Azure-portal](../../cognitive-services-apis-create-account.md)
