---
title: Nauw keurige reactie met behulp van de detectie van antwoord bereik-QnA Maker
description: Inzicht in de nauw keurige antwoord functie die beschikbaar is in QnA Maker beheerd.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: reference
ms.date: 11/09/2020
ms.openlocfilehash: 5dde3da693d87d537fd2177a6f12b55297b5776e
ms.sourcegitcommit: 2817d7e0ab8d9354338d860de878dd6024e93c66
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/05/2021
ms.locfileid: "99582193"
---
# <a name="precise-answering"></a>Nauwkeurige antwoorden

Met de nauw keurige antwoord functie die is geïntroduceerd in QnA Maker Managed (preview), kunt u het exacte korte antwoord krijgen van het antwoord op de best mogelijke vraag die aanwezig is in de Knowledge Base voor elke gebruikers query. Deze functie maakt gebruik van een diepe leer model dat in runtime inzicht heeft in het doel van de gebruikers query en detecteert het precieze korte antwoord van het antwoord op de hand. als er een kort antwoord aanwezig is in het antwoord dat wordt door gegeven. 

Deze functie is standaard ingeschakeld in het test venster, zodat u de functionaliteit kunt testen die specifiek is voor uw scenario. Deze functie is zeer nuttig voor ontwikkel aars van inhoud en eind gebruikers. Ontwikkel aars van inhoud hoeven de specifieke QnA-paren nu niet hand matig toe te kennen voor elk feit dat aanwezig is in de Knowledge Base, en de eind gebruiker hoeft niet te kijken naar het volledige antwoord dat wordt geretourneerd door de service om het daad werkelijke feit te vinden dat de query van de gebruiker beantwoordt. 

## <a name="precise-answering-on-qna-maker-portal"></a>Nauw keurige beantwoording op QnA Maker Portal

Wanneer u in de QnA Maker-Portal het deel venster testen opent, ziet u een optie om het **korte antwoord bovenaan weer te geven** . Deze optie wordt standaard geselecteerd. Wanneer u een query in het test venster invoert, ziet u een korte antwoord samen met de antwoord richting, als er een kort antwoord aanwezig is in het antwoord door te passeren. 
 
![Beheerd ingeschakeld testvenster](../QnAMaker/media/conversational-context/test-pane-with-managed.png)

U kunt de selectie van de optie **kort antwoord weer geven** opheffen als u alleen de beantwoording wilt zien in het test venster. 

De service retourneert ook de betrouwbaarheids Score van het exacte antwoord als een **Score voor een antwoord bereik** die u kunt controleren door de optie **controleren** te selecteren, net onder de query in het test venster.

![Score Managed answer-bereik](../QnAMaker/media/conversational-context/managed-answer-span-score.png)

## <a name="publishing-a-qna-maker-bot"></a>Een QnA Maker-bot publiceren

Wanneer u een bot publiceert, krijgt u de exacte antwoord ervaring standaard in uw toepassing, waar u een kort antwoord ziet samen met het antwoord dat u ontvangt. Raadpleeg de API-verwijzing voor het [genereren van antwoord](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerv5.0-preview.1/knowledgebase/generateanswer#answerspan) voor meer informatie over het gebruik van het exacte antwoord (AnswerSpan) in het antwoord. De gebruiker heeft de flexibiliteit om andere ervaringen te kiezen door de sjabloon bij te werken via de app service van bot. 

## <a name="language-support"></a>Taalondersteuning

De exacte antwoord functie wordt momenteel alleen ondersteund voor Engels.
