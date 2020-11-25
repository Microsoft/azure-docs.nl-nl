---
title: "Quickstart: Een zoekindex maken met behulp van REST API's"
titleSuffix: Azure Cognitive Search
description: In deze quickstart over REST API's leert u hoe u de REST API's van Azure Cognitive Search aanroept met behulp van Postman of Visual Studio Code.
zone_pivot_groups: URL-test-interface-rest-apis
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: quickstart
ms.devlang: rest-api
ms.date: 11/17/2020
ms.openlocfilehash: 971ee806d8b5f9a336b40d96d500ce47d2f5166e
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/17/2020
ms.locfileid: "94711300"
---
# <a name="quickstart-create-an-azure-cognitive-search-index-using-rest-apis"></a>Quickstart: Een Azure Cognitive Search-index maken met behulp van REST API's

In dit artikel wordt uitgelegd hoe u REST API-aanvragen interactief kunt formuleren met behulp van de [Azure Cognitive Search REST API's](/rest/api/searchservice) en een API-client voor het verzenden en ontvangen van aanvragen. Met een API-client en deze instructies kunt u aanvragen verzenden en antwoorden bekijken voordat u code gaat schrijven.

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

::: zone pivot="url-test-tool-rest-postman"

[!INCLUDE [Send requests using Postman](includes/search-get-started-rest-postman.md)]

::: zone-end

::: zone pivot="url-test-tool-rest-vscode-ext"

[!INCLUDE [Send requests using Visual Studio Code](includes/search-get-started-rest-vscode-ext.md)]

::: zone-end

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u in uw eigen abonnement werkt, is het een goed idee om aan het einde van een project te bepalen of u de gemaakte resources nog steeds nodig hebt. Resources die actief blijven, kunnen u geld kosten. U kunt resources afzonderlijk verwijderen, maar u kunt ook de resourcegroep verwijderen als u de volledige resourceset wilt verwijderen.

U kunt resources vinden en beheren in de portal via de koppeling **Alle resources** of **Resourcegroepen** in het navigatiedeelvenster aan de linkerkant.

Als u een gratis service gebruikt, moet u er rekening mee houden dat u bent beperkt tot drie indexen, indexeerfuncties en gegevensbronnen. U kunt afzonderlijke items in de portal verwijderen om onder de limiet te blijven. 

## <a name="next-steps"></a>Volgende stappen

Nu u weet hoe u kerntaken moet uitvoeren, kunt u doorgaan met aanvullende REST API-aanroepen voor meer geavanceerde functies, zoals indexeerfuncties of [het instellen van een verrijkingspijplijn](cognitive-search-tutorial-blob.md) waarmee transformaties van inhoud worden toegevoegd aan het indexeren. Voor de volgende stap wordt de volgende koppeling aangeraden:

> [!div class="nextstepaction"]
> [Zelfstudie: REST en AI gebruiken voor het genereren van doorzoekbare inhoud van Azure-blobs](cognitive-search-tutorial-blob.md)