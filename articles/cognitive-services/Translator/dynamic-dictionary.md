---
title: Dynamische woorden lijst-Translator
titleSuffix: Azure Cognitive Services
description: In dit artikel wordt uitgelegd hoe u de functie voor dynamische woorden lijsten van de Azure Cognitive Services Translator gebruikt.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 05/26/2020
ms.author: lajanuar
ms.openlocfilehash: ebfccb6fed46db65f6a70937516552f5280c8016
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98897761"
---
# <a name="how-to-use-a-dynamic-dictionary"></a>Een dynamische woorden lijst gebruiken

Als u al weet wat de vertaling is die u wilt Toep assen op een woord of een woord groep, kunt u deze als aantekeningen opgeven in de aanvraag. De dynamische woorden lijst is alleen veilig voor samengestelde samen stellingen zoals eigen namen en product namen.

**Syntaxis**

<msTrans: Dictionary Translator = "vertaling van woord groep" >frase</msTrans: Dictionary>

**Vereiste**

* De `From` `To` talen en moeten Engels en een andere ondersteunde taal bevatten. 
* U moet de `From` para meter in uw API-Vertaal aanvraag toevoegen in plaats van de functie Automatische detectie te gebruiken. 

**Voor beeld: nl-de:**

Bron invoer: `The word <mstrans:dictionary translation=\"wordomatic\">word or phrase</mstrans:dictionary> is a dictionary entry.`

Doel uitvoer: `Das Wort "wordomatic" ist ein Wörterbucheintrag.`

Deze functie werkt op dezelfde manier met en zonder HTML-modus.

Gebruik de functie spaarzaam. Een betere manier om de vertaling aan te passen, is door aangepaste Translator te gebruiken. Aangepaste vertaler maakt volledig gebruik van context-en statistische kansen. Als u trainings gegevens hebt of kunt maken waarin uw werk of zin in de context wordt weer gegeven, krijgt u veel betere resultaten. Meer informatie over Custom Translator vindt u op [https://aka.ms/CustomTranslator](https://aka.ms/CustomTranslator) .