---
title: Standaard antwoord ophalen-QnA Maker
description: Het standaard antwoord wordt geretourneerd wanneer er geen overeenkomst is met de vraag. Mogelijk wilt u het standaard antwoord wijzigen van het standaard antwoord standaard.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: how-to
ms.date: 11/09/2020
ms.openlocfilehash: 5aab021ab5194b4af18e3ff1b2c154ed74710353
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96346117"
---
# <a name="change-default-answer-for-a-qna-maker-resource"></a>Standaard antwoord voor een QnA Maker resource wijzigen

Het standaard antwoord voor een Knowledge Base is bedoeld om te worden geretourneerd als er geen antwoord wordt gevonden. Als u een client toepassing gebruikt, zoals de [Azure bot-service](/azure/bot-service/bot-builder-howto-qna), kan deze ook een afzonderlijk standaard antwoord hebben. Dit geeft aan dat er geen antwoord is met de drempel waarde voor de score.

## <a name="types-of-default-answer"></a>Typen standaard antwoord

Er zijn twee typen standaard antwoorden in uw Knowledge Base. Het is belang rijk om te begrijpen hoe en wanneer elk wordt geretourneerd door een Voorspellings query:

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabiele release)](#tab/v1)

|Typen standaard antwoorden|Beschrijving van antwoord|
|--|--|
|KB-antwoord wanneer er geen antwoord wordt bepaald|`No good match found in KB.` -Wanneer de [GenerateAnswer-API](/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer) geen overeenkomend antwoord op de vraag vindt, `DefaultAnswer` wordt de instelling van de app service geretourneerd. Alle kennis grondslagen in dezelfde QnA Maker resource delen dezelfde standaard antwoord tekst.<br>U kunt de instelling in het Azure Portal beheren via de app service, of met de REST-Api's voor het [ophalen](/rest/api/appservice/webapps/listapplicationsettings) of [bijwerken](/rest/api/appservice/webapps/updateapplicationsettings) van de instelling.|
|Instructie tekst voor opvolging van prompt|Wanneer u een opvolgings prompt in een conversatie stroom gebruikt, hebt u mogelijk geen antwoord nodig in het QnA-paar omdat u wilt dat de gebruiker uit de opvolgings prompts wordt geklikt. In dit geval stelt u specifieke tekst in door de standaard antwoord tekst in te stellen, die wordt geretourneerd met elke voor spelling voor opvolgings prompts. De tekst moet als instructie tekst worden weer gegeven voor de selectie van opvolgings prompts. Een voor beeld van deze standaard antwoord tekst is `Please select from the following choices` . Deze configuratie wordt uitgelegd in de volgende secties van dit document. Kan ook worden ingesteld als onderdeel van de Knowledge Base-definitie van `defaultAnswerUsedForExtraction` met behulp van [rest API](/rest/api/cognitiveservices/qnamaker/knowledgebase/create).|

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker beheerd (preview-release)](#tab/v2)

|Typen standaard antwoorden|Beschrijving van antwoord|
|--|--|
|KB-antwoord wanneer er geen antwoord wordt bepaald|`No good match found in KB.` -Wanneer de [GenerateAnswer-API](/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer) geen overeenkomend antwoord op de vraag vindt, wordt een standaard tekst antwoord weer gegeven. In QnA Maker Managed (preview) kunt u deze tekst instellen in de **instellingen** van uw Knowledge Base. <br><br> ![QnA Maker beheerd (preview) standaard antwoord instellen](../media/qnamaker-how-change-default-answer/qnamaker-v2-change-default-answer.png)|
|Instructie tekst voor opvolging van prompt|Wanneer u een opvolgings prompt in een conversatie stroom gebruikt, hebt u mogelijk geen antwoord nodig in het QnA-paar omdat u wilt dat de gebruiker uit de opvolgings prompts wordt geklikt. In dit geval stelt u specifieke tekst in door de standaard antwoord tekst in te stellen, die wordt geretourneerd met elke voor spelling voor opvolgings prompts. De tekst moet als instructie tekst worden weer gegeven voor de selectie van opvolgings prompts. Een voor beeld van deze standaard antwoord tekst is `Please select from the following choices` . Deze configuratie wordt uitgelegd in de volgende secties van dit document. U kunt dit ook instellen als onderdeel van een Knowledge Base-definitie met `defaultAnswerUsedForExtraction` behulp van de [rest API](/rest/api/cognitiveservices/qnamaker/knowledgebase/create).|

---

### <a name="client-application-integration"></a>Integratie van client toepassingen

Voor een client toepassing, zoals een bot met de **Azure bot-service**, kunt u kiezen uit de volgende algemene scenario's:

* De instelling van de kennis basis gebruiken
* Gebruik andere tekst in de client toepassing om onderscheid te maken wanneer een antwoord wordt geretourneerd, maar niet voldoet aan de drempel waarde voor de score. Deze tekst kan bestaan uit statische tekst die is opgeslagen in code of die kan worden opgeslagen in de instellingen lijst van de client toepassing.

## <a name="set-follow-up-prompts-default-answer-when-you-create-knowledge-base"></a>Het standaard antwoord voor het opvolgings bericht instellen wanneer u een Knowledge Base maakt

Wanneer u een nieuwe Knowledge Base maakt, is de standaard antwoord tekst een van de instellingen. Als u ervoor kiest deze niet in te stellen tijdens het aanmaak proces, kunt u dit later doen met de volgende procedure.

## <a name="change-follow-up-prompts-default-answer-in-qna-maker-portal"></a>Het standaard antwoord voor het opvolgings bericht wijzigen in QnA Maker Portal

Het standaard antwoord van de Knowledge Base wordt geretourneerd als er geen antwoord wordt geretourneerd door de QnA Maker-service.

1. Meld u aan bij de [QnA Maker-Portal](https://www.qnamaker.ai/) en selecteer uw Knowledge Base in de lijst.
1. Selecteer **instellingen** in de navigatie balk.
1. Wijzig de waarde van **standaard antwoord tekst** in het gedeelte **Knowledge Base beheren** .

    :::image type="content" source="../media/qnamaker-concepts-confidencescore/change-default-answer.png" alt-text="Scherm opname van QnA Maker Portal, instellingen pagina, met standaard antwoord tekstvak gemarkeerd.":::

1. Selecteer **opslaan en trainen** om de wijziging op te slaan.

## <a name="next-steps"></a>Volgende stappen

* [Een kennisdatabase maken](../How-to/manage-knowledge-bases.md)