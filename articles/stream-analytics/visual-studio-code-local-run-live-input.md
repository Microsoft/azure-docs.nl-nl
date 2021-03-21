---
title: Azure Stream Analytics query's lokaal testen op invoer van live streams met behulp van Visual Studio code
description: In dit artikel wordt beschreven hoe u query's lokaal kunt testen met behulp van Azure Stream Analytics-Hulpprogram Ma's voor Visual Studio code.
ms.service: stream-analytics
author: su-jie
ms.author: sujie
ms.date: 11/14/2019
ms.topic: how-to
ms.openlocfilehash: dd4966ee75e9dc0ff401823e4291f8d299c8893c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "93122891"
---
# <a name="test-stream-analytics-queries-locally-against-live-stream-input-by-using-visual-studio-code"></a>Stream Analytics query's lokaal testen op invoer van live streams met behulp van Visual Studio code

U kunt Azure Stream Analytics-Hulpprogram Ma's voor Visual Studio code gebruiken om uw Stream Analytics-taken lokaal te testen op de Live stroom invoer. De invoer kan afkomstig zijn uit een bron, zoals Azure Event Hubs of Azure IoT Hub. De uitvoer resultaten worden als JSON-bestanden verzonden naar een map in uw project met de naam **LocalRunOutputs**.

## <a name="prerequisites"></a>Vereisten

* Installeer de [.net core SDK](https://dotnet.microsoft.com/download) en Start Visual Studio code opnieuw.

* In [deze Quick](quick-create-visual-studio-code.md) start leert u hoe u een stream Analytics-taak kunt maken met behulp van Visual Studio code.

## <a name="define-a-live-stream-input"></a>Een live stream-invoer definiëren

1. Klik met de rechtermuisknop op de map **Invoer** in uw Stream Analytics-project. Selecteer vervolgens **ASA: Invoer toevoegen** vanuit het contextmenu.

   ![Invoer toevoegen vanuit de map Invoer](./media/quick-create-visual-studio-code/add-input-from-inputs-folder.png)

   U kunt ook **CTRL + SHIFT + P** selecteren om het opdracht palet te openen en **ASA: invoer toevoegen**.

   ![Stream Analytics-invoer toevoegen in Visual Studio Code](./media/quick-create-visual-studio-code/add-input.png)

2. Kies een invoer bron type in de vervolg keuzelijst.

   ![IoT-hub als de invoeroptie selecteren](./media/quick-create-visual-studio-code/iot-hub.png)

3. Als u de invoer vanuit het opdrachtenpalet hebt toegevoegd, kiest u het Stream Analytics-queryscript dat de invoer gaat gebruiken. Hierin moet het bestandspad naar **myASAproj.asaql** automatisch worden ingevuld.

   ![Een Stream Analytics-script selecteren in Visual Studio Code](./media/quick-create-visual-studio-code/asa-script.png)

4. Kies **Selecteren in uw Azure-abonnementen** in de vervolgkeuzelijst.

    ![Selecteren in abonnementen](./media/quick-create-visual-studio-code/add-input-select-subscription.png)

5. Configureer het zojuist gegenereerde JSON-bestand. U kunt de functie CodeLens gebruiken om een tekenreeks in te voeren, een selectie in een vervolgkeuzelijst te maken of de tekst rechtstreeks in het bestand te wijzigen. De volgende schermopname toont **Selecteren in uw abonnementen** als voorbeeld.

   ![Invoer configureren in Visual Studio Code](./media/quick-create-visual-studio-code/configure-input.png)

## <a name="preview-input"></a>Invoer vooraf bekijken

Om ervoor te zorgen dat de invoer gegevens afkomstig zijn, selecteert u **voorbeeld gegevens** in uw Live-invoer configuratie bestand vanaf de bovenste regel. Sommige invoer gegevens zijn afkomstig van een IoT-hub en worden weer gegeven in het voorbeeld venster. Het kan een paar seconden duren voordat het voor beeld wordt weer gegeven.

 ![Live invoer vooraf bekijken](./media/quick-create-visual-studio-code/preview-live-input.png)

## <a name="run-queries-locally"></a>Query's lokaal uitvoeren

Ga terug naar de query-editor en selecteer **lokaal uitvoeren**. Selecteer vervolgens **Live-invoer gebruiken** uit de vervolg keuzelijst.

![Selecteer lokaal uitvoeren in de query-editor](./media/vscode-local-run/run-locally.png)

![Selecteer Live invoer gebruiken](./media/vscode-local-run-live-input/run-locally-use-live-input.png)

Het resultaat wordt weer gegeven in het rechter venster en elke 3 seconden vernieuwd. U kunt **uitvoeren** selecteren om opnieuw te testen. U kunt ook **openen in map** selecteren om de resultaat bestanden in Verkenner te bekijken en te openen met Visual Studio code of een hulp programma zoals Excel. Houd er rekening mee dat de resultaten bestanden alleen beschikbaar zijn in JSON-indeling.

De standaard waarde voor de taak voor het maken van de uitvoer is ingesteld op **nu**. U kunt de tijd aanpassen door de knop **Start tijd** van de uitvoer te selecteren in het resultaat venster.

![Resultaat van lokale uitvoering weer geven](./media/vscode-local-run-live-input/vscode-livetesting.gif)

## <a name="next-steps"></a>Volgende stappen

* [Azure Stream Analytics-taken verkennen met Visual Studio code (preview)](visual-studio-code-explore-jobs.md)

* [CI/CD-pijplijnen instellen met behulp van het NPM-pakket](./cicd-overview.md)