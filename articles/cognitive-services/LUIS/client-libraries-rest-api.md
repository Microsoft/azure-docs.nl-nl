---
title: 'Quickstart: SDK-clientbibliotheken en REST API van Language Understanding (LUIS)'
description: Maak en doorzoek een LUIS-app met de SDK-clientbibliotheken en REST API van LUIS.
ms.topic: quickstart
ms.date: 03/29/2021
ms.service: cognitive-services
ms.author: aahi
manager: nitinme
ms.subservice: language-understanding
author: aahill
keywords: Azure, kunstmatige intelligentie, ai, natuurlijke taalverwerking, nlp, LUIS, azure luis, natuurlijk taalbegrip, ai-chatbot, chatbot-maker, begrip van natuurlijke taal
ms.custom: devx-track-python, devx-track-js, devx-track-csharp, cog-serv-seo-aug-2020
zone_pivot_groups: programming-languages-set-luis
ms.openlocfilehash: ca45266ce4b8ca784c3d54aafb80a66efaf2a1da
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/03/2021
ms.locfileid: "106278912"
---
# <a name="quickstart-language-understanding-luis-client-libraries-and-rest-api"></a>Quickstart: clientbibliotheken en REST API van Language Understanding (LUIS)

Maak een LUIS-app en voer er query's op uit met de LUIS SDK-clientbibliotheken in deze quickstart die gebruikmaakt van C#, Python of JavaScript. U kunt ook cURL gebruiken om aanvragen te verzenden met behulp van de REST API.

Met Language Understanding (LUIS) kunt u natuurlijke taalverwerking toepassen (NLP) op tekst in de natuurlijke spreektaal van een gebruiker om de algemene betekenis te voorspellen en relevante informatie eruit te filteren.

* Met de REST API en de clientbibliotheek voor **creatie** kunt u uw LUIS-app maken, bewerken, trainen en publiceren.
* Met de REST API en clientbibliotheek van de **Prediction-runtime** kunt u een query uitvoeren op de gepubliceerde app.

::: zone pivot="programming-language-csharp"
[!INCLUDE [LUIS development with C# SDK](./includes/sdk-csharp.md)]
::: zone-end

::: zone pivot="programming-language-javascript"
[!INCLUDE [LUIS development with Node.js SDK](./includes/sdk-nodejs.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [LUIS development with Python SDK](./includes/sdk-python.md)]
::: zone-end

::: zone pivot="rest-api"
[!INCLUDE [LUIS development with REST API](./includes/rest-api.md)]
::: zone-end

## <a name="clean-up-resources"></a>Resources opschonen

U kunt de app verwijderen uit de [LUIS-portal](https://www.luis.ai) en de Azure-resources verwijderen uit [Azure Portal](https://portal.azure.com/).

Als u de REST API gebruikt, verwijdert u het bestand `ExampleUtterances.JSON` uit het bestandssysteem wanneer u klaar bent met de quickstart.

## <a name="troubleshooting"></a>Problemen oplossen

* Verificatie bij de clientbibliotheek: verificatiefouten geven meestal aan dat de verkeerde sleutel en eindpunt is gebruikt. Deze quickstart maakt gebruik van de ontwerpsleutel en het eindpunt voor de prediction runtime, maar werkt alleen als u het maandelijkse quotum nog niet hebt gebruikt. Als u de ontwerpsleutel en het eindpunt niet kunt gebruiken, moet u de prediction runtime-sleutel en eindpunt gebruiken bij het openen van de prediction runtime SDK-clientbibliotheek.
* Entiteiten maken: als er een fout optreedt bij het maken van de geneste machine learning-entiteit die in deze zelfstudie wordt gebruikt, moet u ervoor zorgen dat u de code hebt gekopieerd en de code niet hebt gewijzigd om een andere entiteit te maken.
* Voorbeeld-uitingen: als u een foutbericht krijgt bij het maken van de gelabelde voorbeelduiting die in deze zelfstudie wordt gebruikt, moet u ervoor zorgen dat u de code hebt gekopieerd en de code niet hebt gewijzigd om een ander gelabeld voorbeeld te maken.
* Training: als u een trainingsfout krijgt, duidt dit meestal op een lege app (geen intenties met voorbeelduitingen) of een app met intenties of entiteiten die misvormd zijn.
* Diverse fouten: omdat de code clientbibliotheken met tekst- en JSON-objecten aanroept, moet u ervoor zorgen dat u de code niet hebt gewijzigd.

Andere fouten: als er een fout optreedt die hierboven niet wordt behandeld, laat het ons dan weten door onderaan deze pagina feedback te geven. Vermeld de programmeertaal en -versie van de clientbibliotheken die u hebt geïnstalleerd.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Terugkerende app-ontwikkeling voor LUIS](./luis-concept-app-iteration.md)