---
title: Azure Stream Analytics taken lokaal ontwikkelen en fouten opsporen
description: Meer informatie over het ontwikkelen en testen van Azure Stream Analytics taken op uw lokale computer voordat u deze uitvoert in Azure Portal.
ms.author: sujie
author: su-jie
ms.topic: conceptual
ms.date: 03/31/2020
ms.service: stream-analytics
ms.openlocfilehash: 18df480dab90d9ab127bb96971fc19cdc5a361ce
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98016470"
---
# <a name="develop-and-debug-azure-stream-analytics-jobs-locally"></a>Azure Stream Analytics taken lokaal ontwikkelen en fouten opsporen

U kunt in de Azure Portal Azure Stream Analytics-taken maken en testen, maar veel ontwikkel aars geven de voor keur aan een lokale ontwikkel ervaring. Met Stream Analytics kunt u eenvoudig uw favoriete code-editor en ontwikkel hulpprogramma's gebruiken om taken te maken en te testen met live gebeurtenis stromen van Azure Event hub, IoT Hub en Blob Storage met behulp van een volledig functiond Local runtime met één knoop punt. U kunt ook rechtstreeks vanuit uw lokale ontwikkel omgeving taken verzenden naar Azure.

## <a name="local-development-environments"></a>Lokale ontwikkel omgevingen

De manier waarop u Stream Analytics taken op uw lokale computer ontwikkelt, is afhankelijk van de programma voorkeuren en de beschik baarheid van de functie. Zie [Azure stream Analytics functie vergelijking](feature-comparison.md) om te zien welke functies worden ondersteund voor elke ontwikkel omgeving.

De omgevingen in de volgende tabel ondersteunen lokale ontwikkeling:

|Omgeving                              |Beschrijving    |
|-----------------------------------------|------------|
|[Visual Studio Code](visual-studio-code-explore-jobs.md)| Met de [uitbrei ding voor de Azure stream Analytics-Hulpprogram ma's](https://marketplace.visualstudio.com/items?itemName=ms-bigdatatools.vscode-asa) voor Visual Studio code kunt u uw stream Analytics-taak zowel lokaal als in de Cloud ontwerpen, beheren en testen met uitgebreide IntelliSense en systeem eigen broncode beheer. Ondersteunt de ontwikkeling van Linux, MacOS en Windows. Zie [een Azure stream Analytics-taak maken in Visual Studio code](quick-create-visual-studio-code.md)voor meer informatie. De uitbrei ding biedt ook ondersteuning voor [Visual Studio Codespaces](https://visualstudio.microsoft.com/services/visual-studio-codespaces/) . Dit is een in de Cloud gehoste ontwikkel omgeving.|
|[Visual Studio 2019](stream-analytics-tools-for-visual-studio-install.md) |Stream Analytics-Hulpprogram Ma's maken deel uit van de werk belastingen voor ontwikkeling en gegevens opslag en-verwerking van Azure in Visual Studio. U kunt Visual Studio gebruiken om aangepaste door de gebruiker gedefinieerde C#-functies en-deserials te schrijven. Zie [een Azure stream Analytics-taak maken met behulp van Visual Studio](stream-analytics-quick-create-vs.md)voor meer informatie.|
|[Opdracht prompt of Terminal](stream-analytics-tools-for-visual-studio-cicd.md)|Het Azure Stream Analytics CI/CD NuGet-pakket bevat hulpprogram ma's voor Visual Studio project build, lokale tests op een wille keurige computer. Het Azure Stream Analytics CI/CD NPM-pakket bevat hulpprogram ma's voor Visual Studio code project builds (waarmee een Azure Resource Manager sjabloon wordt gegenereerd) op een wille keurige computer.|

## <a name="next-steps"></a>Volgende stappen

* [Stream Analytics-query's lokaal testen met voorbeeldgegevens testen met behulp van Visual Studio Code](visual-studio-code-local-run.md)
* [Stream Analytics query's lokaal testen op invoer van live streams met behulp van Visual Studio code](visual-studio-code-local-run-live-input.md)
* [Stream Analytics query's lokaal testen met Visual Studio](stream-analytics-vs-tools-local-run.md)
* [Live-gegevens lokaal testen met Azure Stream Analytics-hulpprogram ma's voor Visual Studio](stream-analytics-live-data-local-testing.md)
