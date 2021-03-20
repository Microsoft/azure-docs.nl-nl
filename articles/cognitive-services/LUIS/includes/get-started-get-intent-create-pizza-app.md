---
title: bestand opnemen
description: bestand opnemen
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.date: 12/29/2020
ms.subservice: language-understanding
ms.topic: include
ms.custom: include file
ms.author: aahi
ms.reviewer: roy-har
ms.openlocfilehash: 7aa2fba6ef551a745ccaf5b00f36021b9d8680ce
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97820554"
---
1. Selecteer [pizza-app-for-luis-v6.json](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/luis/apps/pizza-app-for-luis-v6.json) om de GitHub-pagina voor het `pizza-app-for-luis.json`-bestand te openen.
1. Klik met de rechtermuisknop of tik lang op de knop **Onbewerkt** en selecteer **Koppeling opslaan als** om de `pizza-app-for-luis.json` op uw computer op te slaan.
1. Meld u aan bij de [LUIS-portal](https://www.luis.ai).
1. Selecteer [Mijn apps](https://www.luis.ai/applications).
1. Selecteer op de pagina **Mijn apps** de optie **+ Nieuwe app voor conversatie**.
1. Selecteer **Importeren als JSON**.
1. Selecteer in het dialoogvenster **Nieuwe app importeren** de knop **Bestand kiezen**.
1. Selecteer het `pizza-app-for-luis.json`-bestand dat u hebt gedownload en selecteer **Openen**.
1. Voer in het veld **Naam** in het dialoogvenster **Nieuwe app importeren** een naam in voor uw Pizza-app en selecteer vervolgens de knop **Gereed**.

De app wordt geïmporteerd.

Sluit het dialoogvenster **Een effectieve LUIS-app maken** zodra dit wordt weergegeven.

## <a name="train-and-publish-the-pizza-app"></a>De Pizza-app trainen en publiceren

Als het goed is, wordt de pagina **Intenties** weergegeven, met een lijst van de intenties in de Pizza-app.

[!INCLUDE [How to train](howto-train.md)]

[!INCLUDE [How to publish](howto-publish.md)]

Uw Pizza-app is nu klaar voor gebruik.

## <a name="record-the-app-id-prediction-key-and-prediction-endpoint-of-your-pizza-app"></a>De app-id, de voorspellingssleutel en het voorspellingseindpunt van uw Pizza-app vastleggen

Als u uw nieuwe Pizza-app wilt gebruiken, hebt u de app-id, een voorspellingssleutel en een voorspellingseindpunt van uw Pizza-app nodig.

Deze waarden zoeken:

1. Selecteer op de pagina **Intenties** de optie **BEHEREN**.
1. Noteer de **app-id** op de pagina **Toepassingsinstellingen**.
1. Selecteer **Azure-resources**.
1. Noteer de **primaire sleutel** op de pagina **Azure-resources**. Deze waarde is uw voorspellingssleutel.
1. Noteer de **eindpunt-URL**. Deze waarde is uw voorspellingseindpunt.
