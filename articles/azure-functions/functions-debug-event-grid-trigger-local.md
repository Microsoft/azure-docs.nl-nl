---
title: Azure Functions Event Grid lokale debugging
description: Meer informatie over lokaal fouten opsporen Azure Functions door een Event Grid gebeurtenis
author: craigshoemaker
ms.topic: conceptual
ms.date: 10/18/2018
ms.author: cshoe
ms.openlocfilehash: 7483a097b188b9f96221a13964992c7b02332258
ms.sourcegitcommit: 5ce88326f2b02fda54dad05df94cf0b440da284b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107891957"
---
# <a name="azure-function-event-grid-trigger-local-debugging"></a>Lokaal opsporen van fouten in Azure-functies voor Event Grid Trigger

In dit artikel wordt gedemonstreerd hoe u fouten opsport in een lokale functie die een Azure Event Grid gebeurtenis verwerkt die wordt veroorzaakt door een opslagaccount. 

## <a name="prerequisites"></a>Vereisten

- Een bestaande functie-app maken of gebruiken
- Een bestaand opslagaccount maken of gebruiken
- Download [ngrok](https://ngrok.com/) zodat Azure uw lokale functie kan aanroepen

## <a name="create-a-new-function"></a>Een nieuwe functie maken

Open uw functie-app in Visual Studio, klik met de rechtermuisknop op de projectnaam in de Solution Explorer klik op Add **> New Azure Function**.

Selecteer in *het venster Nieuwe Azure-functie* de **Event Grid en** klik op **OK.**

![Nieuwe functie maken](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-add-function.png)

Zodra de functie is gemaakt, opent u het codebestand en kopieert u de URL met opmerkingen bovenaan het bestand. Deze locatie wordt gebruikt bij het configureren van Event Grid trigger.

![Kopieerlocatie](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-copy-location.png)

Stel vervolgens een onderbrekingspunt in op de regel die begint met `log.LogInformation` .

![Onderbrekingspunt instellen](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-set-breakpoint.png)


Druk vervolgens **op F5 om** een debugging-sessie te starten.

[!INCLUDE [functions-event-grid-local-dev](../../includes/functions-event-grid-local-dev.md)]

## <a name="debug-the-function"></a>Fouten opsporen in de functie

Zodra de Event Grid een nieuw bestand wordt geüpload naar de opslagcontainer, wordt het onderbrekingspunt in uw lokale functie bereikt.

![Start ngrok](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-breakpoint.png)

## <a name="clean-up-resources"></a>Resources opschonen

Verwijder de **testcontainer** in uw opslagaccount om de resources op te schonen die in dit artikel zijn gemaakt.

## <a name="next-steps"></a>Volgende stappen

- [Formaat van geüploade afbeeldingen automatisch wijzigen met Event Grid](../event-grid/resize-images-on-storage-blob-upload-event.md)
- [Event Grid trigger voor Azure Functions](./functions-bindings-event-grid.md)
