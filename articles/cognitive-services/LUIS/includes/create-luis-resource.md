---
title: LUIS-resources maken
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/07/2020
ms.author: aahi
ms.openlocfilehash: a077d255648ff07cc88b43dece889a221c46c11f
ms.sourcegitcommit: 04fb3a2b272d4bbc43de5b4dbceda9d4c9701310
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/12/2020
ms.locfileid: "94561506"
---
<a name="create-luis-resources"></a>

## <a name="create-luis-resources-in-the-azure-portal"></a>LUIS-resources maken in Azure Portal

1. Gebruik [deze koppeling](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne) om te beginnen met het maken van LUIS-resources in Azure Portal.

1. Geef alle vereiste instellingen op:

    |Name|Doel|
    |--|--|
    |Abonnement | Het abonnement dat wordt gefactureerd voor de resource.|
    |Resourcegroep| Een aangepaste resourcegroepnaam die u kiest of maakt. Met resourcegroepen kunt u Azure-resources groeperen voor toegang en beheer.|
    |Name| Een aangepaste naam die u kiest. Deze wordt gebruikt als het aangepaste subdomein voor uw creatie- en voorspellingseindpuntquery's.|
    |Locatie van creatie|De regio die aan uw model is gekoppeld.|
    |Prijscategorie creatie|Hiermee wordt het maximumaantal transacties per seconde en maand bepaald.|
    |Voorspellingslocatie|De regio die is gekoppeld aan de gepubliceerde voorspellingseindpuntruntime.|
    |Prijscategorie voor voorspelling|Hiermee wordt het maximumaantal transacties per seconde en maand bepaald.|

    > [!div class="mx-imgBorder"]
    > [![Schermopname met het tabblad Basisgegevens onder Maken.](../media/luis-how-to-azure-subscription/create-resource-in-azure-small.png)](../media/luis-how-to-azure-subscription/create-resource-in-azure-small.png#lightbox)

1. Selecteer **Controleren + maken** en wacht totdat de resource is gemaakt.
1. Nadat beide resources zijn gemaakt, selecteert u in Azure Portal de nieuwe creatie-resource. Selecteer vervolgens **Quickstarts** om de **eindpunt-URL** en de **sleutel** voor de creatie te verkrijgen om via programmacode te ontwerpen.

> [!TIP]
> Als u de resources wilt gebruiken in de LUIS-portal [moet u de resources](../luis-how-to-azure-subscription.md#assign-an-authoring-resource-in-the-luis-portal-for-all-apps) toewijzen.
