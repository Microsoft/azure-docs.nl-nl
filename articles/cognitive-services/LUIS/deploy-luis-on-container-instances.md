---
title: LUIS-container implementeren in azure container instances
titleSuffix: Azure Cognitive Services
description: Implementeer de LUIS-container in een Azure-container exemplaar en test deze in een webbrowser.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 04/07/2020
ms.author: aahi
ms.openlocfilehash: edc2ad0f895b8a1bb6448ce1cdf79b1b2ce83951
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/21/2020
ms.locfileid: "95997189"
---
# <a name="deploy-the-language-understanding-luis-container-to-azure-container-instances"></a>De Language Understanding-container (LUIS) implementeren in azure container instances

Meer informatie over het implementeren van de Cognitive Services [Luis](luis-container-howto.md) -container in azure [container instances](../../container-instances/index.yml). In deze procedure wordt het maken van een afwijkende detector-resource gedemonstreerd. Vervolgens bespreken we het verzamelen van de bijbehorende container installatie kopie. Ten slotte markeren we de mogelijkheid om de indeling van de twee uit een browser uit te oefenen. Door gebruik te maken van containers kan de aandacht van de ontwikkel aars van de infra structuur afnemen in plaats van de ontwikkeling van toepassingen te richten.

[!INCLUDE [Prerequisites](../containers/includes/container-prerequisites.md)]

[!INCLUDE [Create LUIS resource](includes/create-luis-resource.md)]

[!INCLUDE [Gathering required parameters](../containers/includes/container-gathering-required-parameters.md)]

## <a name="create-an-azure-file-share"></a>Een Azure-bestandsshare maken

De LUIS-container vereist een `.gz` model bestand dat tijdens runtime wordt opgehaald. De container moet toegang hebben tot dit model bestand via een volume koppeling vanuit het container exemplaar. Zie [een bestands share maken](../../storage/files/storage-how-to-create-file-share.md)voor meer informatie over het maken van een Azure-bestands share. Noteer de naam van het Azure Storage account, de sleutel en de naam van de bestands share die u later nodig hebt.

### <a name="export-and-upload-packaged-luis-app"></a>Een verpakte LUIS-app exporteren en uploaden

Als u het LUIS-model (verpakte app) naar de Azure-bestands share wilt uploaden, moet u het <a href="luis-container-howto.md#export-packaged-app-from-luis" target="_blank" rel="noopener">eerst <span class="docon docon-navigate-external x-hidden-focus"></span> exporteren vanuit de Luis-Portal </a>. Ga vanuit het Azure Portal naar de **overzichts** pagina van de bron van het opslag account en selecteer **Bestands shares**. Selecteer de naam van de bestands share die u onlangs hebt gemaakt en selecteer vervolgens de knop **uploaden** .

> [!div class="mx-imgBorder"]
> ![Uploaden naar de bestands share](media/luis-how-to-deploy-to-aci/upload-file-share.png)

Upload het LUIS-model bestand.

[!INCLUDE [Create LUIS Container instance resource](../containers/includes/create-container-instances-resource-from-azure-cli.md)]

[!INCLUDE [API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

[!INCLUDE [Next steps](../containers/includes/containers-next-steps.md)]