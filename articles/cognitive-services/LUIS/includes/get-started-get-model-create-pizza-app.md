---
title: bestand opnemen
description: bestand opnemen
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.date: 06/04/2020
ms.subservice: language-understanding
ms.topic: include
ms.custom: include file
ms.author: aahi
ms.reviewer: roy-har
ms.openlocfilehash: a021b7c5b26e1bca1e3f623ad762c2f3ee8d6020
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97820541"
---
Maak de Pizza-app.

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

## <a name="add-an-authoring-resource-to-the-pizza-app"></a>Een ontwerpresource aan de Pizza-app toevoegen

1. Selecteer **BEHEREN**.
1. Selecteer **Azure-resources**.
1. Selecteer **Ontwerpresource**.
1. Selecteer **Ontwerpresource wijzigen**.

Als u een ontwerpresource hebt, voert u de **tenantnaam**, **abonnementsnaam** en **LUIS-resourcenaam** van uw ontwerpresource in.

Als u geen ontwerpresource hebt:

1. Selecteer **Nieuwe resource maken**.
1. Voer een **tenantnaam**, **resourcenaam**, **abonnementsnaam** en **naam van de Azure-resourcegroep** in.

Uw Pizza-app is nu klaar voor gebruik.

## <a name="record-the-access-values-for-your-pizza-app"></a>De toegangswaarden voor uw Pizza-app vastleggen

Als u uw nieuwe Pizza-app wilt gebruiken, hebt u de app-id, een ontwerpsleutel en een ontwerpeindpunt van uw Pizza-app nodig. U hebt een afzonderlijk voorspellingseindpunt en een afzonderlijke voorspellingssleutel nodig om voorspellingen op te halen.

Deze waarden zoeken:

1. Selecteer op de pagina **Intenties** de optie **BEHEREN**.
1. Noteer de **app-id** op de pagina **Toepassingsinstellingen**.
1. Selecteer **Azure-resources**.
1. Selecteer **Ontwerpresource**.
1. Noteer de **Primaire sleutel** die u ziet op de tabbladen **Ontwerpresource** en **Voorspellingsresources**. Deze waarde is uw ontwerpsleutel.
1. Noteer de **eindpunt-URL**. Deze waarde is uw ontwerpeindpunt.
