---
title: 'Quickstart: SDK-clientbibliotheken en REST API van Language Understanding (LUIS)'
description: Maak en doorzoek een LUIS-app met de SDK-clientbibliotheken en REST API van LUIS.
ms.topic: quickstart
ms.date: 12/09/2020
ms.service: cognitive-services
ms.author: aahi
manager: nitinme
ms.subservice: language-understanding
author: aahill
keywords: Azure, kunstmatige intelligentie, ai, natuurlijke taalverwerking, nlp, LUIS, azure luis, natuurlijk taalbegrip, ai-chatbot, chatbot-maker, begrip van natuurlijke taal
ms.custom: devx-track-python, devx-track-js, devx-track-csharp, cog-serv-seo-aug-2020
zone_pivot_groups: programming-languages-set-luis
ms.openlocfilehash: 7ff5844cf9f1bce45df438fc00e24da28c438b05
ms.sourcegitcommit: dea56e0dd919ad4250dde03c11d5406530c21c28
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/09/2020
ms.locfileid: "96941516"
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

* [Wat is de Language Understanding (LUIS)-API?](what-is-luis.md)
* [Nieuwe functies](whats-new.md)
* [Intenties](luis-concept-intent.md), [entiteiten](luis-concept-entity-types.md) en [voorbeelduitingen](luis-concept-utterance.md), en [vooraf samengestelde entiteiten](luis-reference-prebuilt-entities.md)
* De broncode voor dit voorbeeld is te vinden op [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code).
* Informatie over natuurlijke taal: [natuurlijk taalbegrip (NLU) en natuurlijke taalverwerking (NLP)](artificial-intelligence.md)
* Bots: [AI-chatbots](luis-csharp-tutorial-bf-v4.md "zelfstudie om een chatbot te maken")
