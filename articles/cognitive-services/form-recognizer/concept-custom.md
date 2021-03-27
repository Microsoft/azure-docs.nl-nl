---
title: Aangepaste modellen-formulier herkenning
titleSuffix: Azure Cognitive Services
description: Leer concepten met betrekking tot aangepaste modellen van de API voor formulier herkenning-gebruik en limieten.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 03/25/2021
ms.author: lajanuar
ms.openlocfilehash: 74ced2ecadb5ccfe5cdb7966550e469ae4f8ab31
ms.sourcegitcommit: c94e282a08fcaa36c4e498771b6004f0bfe8fb70
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2021
ms.locfileid: "105612259"
---
# <a name="form-recognizer-custom-models"></a>Aangepaste modellen voor formulier herkenning

De formulier herkenning maakt gebruik van geavanceerde machine learning technologie voor het analyseren en extra heren van gegevens uit uw formulieren en documenten. Een model voor het herkennen van een formulier is een representatie van geëxtraheerde gegevens die wordt gebruikt als referentie voor het analyseren van uw specifieke inhoud. Er zijn twee soorten modellen voor formulier herkenning:

* **Aangepaste modellen**. Aangepaste modellen voor formulier herkenning vertegenwoordigen geëxtraheerde gegevens uit _formulieren_ die specifiek zijn voor uw bedrijf. Aangepaste modellen moeten worden getraind om uw afzonderlijke formulier gegevens te analyseren.

* **Preconstrueerde modellen**. Formulier herkenning ondersteunt momenteel vooraf gemaakte modellen voor _kwitanties, visite kaartjes, identificatie kaarten_ en _facturen_. Met vooraf gemaakte modellen worden gegevens uit document afbeeldingen opgespoord en geëxtraheerd en worden de geëxtraheerde gegevens in een gestructureerde JSON-uitvoer geretourneerd.

## <a name="what-does-a-custom-model-do"></a>Wat doet een aangepast model?

Met de formulier herkenning kunt u een model trainen waarmee informatie wordt opgehaald uit formulieren die relevant zijn voor uw use-case. U hebt slechts vijf voor beelden van hetzelfde formulier type nodig om aan de slag te gaan. Uw aangepaste model kan worden getraind met of zonder gelabelde gegevens sets.

## <a name="create-use-and-manage-your-custom-model"></a>Uw aangepaste model maken, gebruiken en beheren

Op hoog niveau zijn de stappen voor het maken, trainen en gebruiken van uw aangepaste model als volgt:

> [!div class="nextstepaction"]
> [1. uw trainings gegevensset samen stellen](build-training-data-set.md#custom-model-input-requirements)

Het bouwen van een aangepast model begint met het tot stand brengen van uw trainings gegevensset. U hebt mini maal vijf voltooide formulieren van hetzelfde type nodig voor uw voorbeeld gegevensset. Ze kunnen uit verschillende bestands typen bestaan en zowel tekst als hand Schrift bevatten. Uw formulieren moeten van hetzelfde type document zijn en de [invoer vereisten](build-training-data-set.md#custom-model-input-requirements) voor de formulier herkenning volgen.  
&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&#129155;

> [!div class="nextstepaction"]
> [2. Upload uw trainings gegevensset](build-training-data-set.md#upload-your-training-data)

U moet uw trainings gegevens uploaden naar een Azure Blob Storage-container. *Zie* [Azure Storage Snelstartgids voor Azure Portal](../../storage/blobs/storage-quickstart-blobs-portal.md)als u niet weet hoe u een Azure-opslag account maakt met een container. Gebruik de gratis prijs categorie (F0) om de service te proberen en pas later bij te werken naar een betaalde laag voor productie.  
&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&#129155;
> [!div class="nextstepaction"]
> [3. uw aangepaste model trainen](quickstarts/client-library.md#train-a-custom-model)

U kunt uw model trainen [zonder](quickstarts/client-library.md#train-a-model-without-labels) of [met](quickstarts/client-library.md#train-a-model-with-labels) gelabelde gegevens sets. Niet-gelabelde gegevens sets zijn alleen afhankelijk van de indelings-API voor het detecteren en identificeren van belang rijke informatie zonder dat menselijke invoer is toegevoegd. Gegevens sets met een label zijn ook gebaseerd op de lay-Outapi, maar aanvullende menselijke invoer is opgenomen, zoals uw specifieke labels en veld locaties. Als u zowel gelabelde als niet-gelabelde gegevens wilt gebruiken, begint u met ten minste vijf voltooide formulieren van hetzelfde type voor de gelabelde trainings gegevens en voegt u niet-gelabelde gegevens toe aan de vereiste gegevensset.  
&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&#129155;  

>[!div class="nextstepaction"]
> [4. documenten analyseren met uw aangepaste model](quickstarts/client-library.md#analyze-forms-with-a-custom-model)

Test uw pas getrainde model met behulp van een formulier dat geen deel uitmaakt van de trainings gegevensset. U kunt verder gaan met verdere training om de prestaties van uw aangepaste model te verbeteren.  
&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&#129155;

> [!div class="nextstepaction"]
> [5. uw aangepaste modellen beheren](quickstarts/client-library.md#manage-custom-models)

U kunt op elk gewenst moment een lijst weer geven met alle aangepaste modellen onder uw abonnement, informatie over een specifiek aangepast model ophalen of een aangepast model verwijderen uit uw account.

## <a name="next-steps"></a>Volgende stappen

Raadpleeg onze API-referentie documentatie voor meer informatie over de client bibliotheek voor formulier herkenning.
> [!div class="nextstepaction"]
> [API-referentie voor formulier herkenning](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/5ed8c9843c2794cbb1a96291)
>
