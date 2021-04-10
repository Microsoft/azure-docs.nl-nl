---
title: Azure container instance-recept
titleSuffix: Azure Cognitive Services
description: Meer informatie over het implementeren van Cognitive Services-containers in azure container instance
services: cognitive-services
author: aahill
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 12/18/2020
ms.author: aahi
ms.openlocfilehash: 4641723ed1ad334ab9f960728f4986b03fd0ac99
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/31/2021
ms.locfileid: "106065885"
---
# <a name="deploy-and-run-container-on-azure-container-instance"></a>Container implementeren en uitvoeren in Azure Container Instance

Met de volgende stappen kunt u Azure Cognitive Services-toepassingen in de Cloud eenvoudig schalen met Azure [container instances](../../container-instances/index.yml). Met container opslag kunt u zich richten op het bouwen van uw toepassingen in plaats van de infra structuur te beheren. Zie [functies en voor delen](../cognitive-services-container-support.md#features-and-benefits)voor meer informatie over het gebruik van containers.

## <a name="prerequisites"></a>Vereisten

Het recept werkt met een Cognitive Services-container. De cognitieve service resource moet worden gemaakt voordat u het recept gebruikt. Elke cognitieve service die containers ondersteunt, heeft het artikel ' How to install ' voor het installeren en configureren van de service voor een container. Voor sommige services is een bestand of een set bestanden vereist als invoer voor de container. het is belang rijk dat u de container hebt gebruikt voordat u deze oplossing gebruikt.

* Een Azure-resource voor de Azure cognitieve service die u gebruikt.
* URL van het cognitieve service- **eind punt** : Controleer de installatie van uw specifieke service voor de container, zodat u kunt vinden waar de eind punt-URL zich bevindt in het Azure Portal en wat een juist voor beeld van de URL is. De exacte indeling kan veranderen van service naar service.
* Cognitieve service **sleutel** : de sleutels bevinden zich op de pagina **sleutels** voor de Azure-resource. U hebt slechts een van de twee sleutels nodig. De sleutel is een teken reeks van 32 alfanumerieke tekens.

* Eén Cognitive Services-container op uw lokale host (uw computer). Zorg ervoor dat u het volgende kunt doen:
  * Haal de installatie kopie op met een `docker pull` opdracht.
  * Voer de lokale container uit met alle vereiste configuratie-instellingen met een `docker run` opdracht.
  * Roep het eind punt van de container aan, waarbij een reactie van HTTP-2xx en een JSON-antwoord wordt teruggestuurd.

Alle variabelen tussen punt haken, `<>` moeten worden vervangen door uw eigen waarden. Deze vervanging omvat de punt haken.

> [!IMPORTANT]
> De LUIS-container vereist een `.gz` model bestand dat tijdens runtime wordt opgehaald. De container moet toegang hebben tot dit model bestand via een volume koppeling vanuit het container exemplaar. Voer de volgende stappen uit om een model bestand te uploaden:
> 1. [Maak een Azure-bestands share](../../storage/files/storage-how-to-create-file-share.md). Noteer de naam van het Azure Storage account, de sleutel en de naam van de bestands share die u later nodig hebt.
> 2. [Exporteer uw Luis-model (app-pakket) vanuit de Luis-Portal](../LUIS/luis-container-howto.md#export-packaged-app-from-luis). 
> 3. Navigeer in het Azure Portal naar de **overzichts** pagina van de bron van het opslag account en selecteer **Bestands shares**. 
> 4. Selecteer de naam van de bestands share die u onlangs hebt gemaakt en selecteer vervolgens **uploaden**. Upload vervolgens uw verpakte app. 

# <a name="azure-portal"></a>[Azure-portal](#tab/portal)

[!INCLUDE [Portal instructions for creating an ACI instance](includes/create-container-instances-resource.md)]

# <a name="cli"></a>[CLI](#tab/cli)

[!INCLUDE [CLI instructions for creating an ACI instance](../containers/includes/create-container-instances-resource-from-azure-cli.md)]

---


## <a name="use-the-container-instance"></a>Het container exemplaar gebruiken

# <a name="azure-portal"></a>[Azure-portal](#tab/portal)

1. Selecteer het **overzicht** en kopieer het IP-adres. Dit is een numeriek IP-adres, zoals `55.55.55.55` .
1. Open een nieuw browser tabblad en gebruik het IP-adres, bijvoorbeeld `http://<IP-address>:5000 (http://55.55.55.55:5000` ). U ziet de start pagina van de container, zodat u weet dat de container wordt uitgevoerd.

    ![Start pagina van container](../../../includes/media/cognitive-services-containers-api-documentation/container-webpage.png)

1. Selecteer **Service-API-beschrijving** om de Swagger-pagina voor de container weer te geven.

1. Selecteer een van de **post** -api's en selecteer **try-out**.  De para meters worden weer gegeven met inbegrip van de invoer. Vul de para meters in.

1. Selecteer **uitvoeren** om de aanvraag naar uw container exemplaar te verzenden.

    U hebt Cognitive Services containers gemaakt en gebruikt in azure container instance.

# <a name="cli"></a>[CLI](#tab/cli)

[!INCLUDE [API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

> [!NOTE]
> Als u de Text Analytics uitvoert voor Status container, gebruikt u de volgende URL voor het verzenden van query's: `http://localhost:5000/text/analytics/v3.2-preview.1/entities/health`

---
