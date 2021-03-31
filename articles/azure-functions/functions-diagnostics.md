---
title: Overzicht van Azure Functions diagnostische gegevens
description: Meer informatie over hoe u problemen met uw functie-app kunt oplossen met Azure Functions diagnostische gegevens.
author: yunjchoi
ms.topic: article
ms.date: 11/01/2019
ms.author: yunjchoi
ms.custom: na
ms.openlocfilehash: fb329273d442c023233fee2e7e01aabe5f5bff8c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "83122359"
---
# <a name="azure-functions-diagnostics-overview"></a>Overzicht van diagnostische gegevens in Azure Functions

Wanneer u een functie-app uitvoert, wilt u voor bereid zijn op eventuele problemen die zich kunnen voordoen, van 4xx-fouten om fouten te activeren. Azure Functions Diagnostics is een intelligente en interactieve ervaring om u te helpen bij het oplossen van problemen met uw functie-app zonder configuratie of extra kosten. Wanneer u problemen ondervindt met uw functie-app, wordt door Azure Functions diagnostische gegevens uitgecheckt wat het probleem is. Hiermee wordt u naar de juiste informatie geleid om eenvoudiger en snel problemen op te lossen en het probleem te verhelpen. In dit artikel leert u de basis beginselen van het gebruik van Azure Functions diagnostische gegevens voor het sneller vaststellen en oplossen van veelvoorkomende problemen met de functie-app.

## <a name="start-azure-functions-diagnostics"></a>Azure Functions diagnostische gegevens starten

Azure Functions diagnostische gegevens starten:

1. Navigeer naar uw functie-app in het [Azure Portal](https://portal.azure.com).
1. Selecteer **diagnose en los problemen** op om Azure functions diagnostische gegevens te openen.
1. Kies een categorie die het beste het probleem van uw functie-app beschrijft met behulp van de tref woorden in de tegel start pagina. U kunt ook een tref woord typen dat uw probleem het beste beschrijft in de zoek balk. U kunt bijvoorbeeld typen `execution` om een lijst met diagnostische rapporten te bekijken die betrekking hebben op de uitvoering van uw functie-app en deze rechtstreeks vanuit de start pagina te openen.

   :::image type="content" source="./media/functions-diagnostics/functions-app-search-azure-functions-diagnostics.png" alt-text="Zoeken naar diagnostische gegevens over Azure Functions." border="true":::

## <a name="use-the-interactive-interface"></a>De interactieve interface gebruiken

Wanneer u een startpagina categorie selecteert die het beste past bij het probleem van uw functie-app, kan Azure Functions diagnostische gegevens van de interactieve interface, met de naam genie, u helpen bij het vaststellen en oplossen van het probleem van uw app. U kunt de snelkoppelingen voor tegels in genie gebruiken om het volledige diagnostische rapport te bekijken van de probleem categorie waarin u bent geïnteresseerd. De snelkoppelingen voor tegels bieden u een directe manier om toegang te krijgen tot uw diagnostische gegevens.

:::image type="content" source="./media/functions-diagnostics/genie.png" alt-text="Genie is Azure Functions de interface voor diagnostische gegevens." border="false":::

Nadat u een tegel hebt geselecteerd, ziet u een lijst met onderwerpen die betrekking hebben op het probleem dat wordt beschreven op de tegel. Deze onderwerpen bevatten fragmenten van belang rijke informatie uit het volledige rapport. Selecteer een van deze onderwerpen om de problemen verder te onderzoeken. U kunt ook **volledig rapport weer geven** selecteren om alle onderwerpen op één pagina te verkennen.

:::image type="content" source="./media/functions-diagnostics/preview-of-diagnostic-report.png" alt-text="Voor beeld van diagnostisch rapport" border="false":::

## <a name="view-a-diagnostic-report"></a>Een diagnostisch rapport weer geven

Nadat u een onderwerp hebt gekozen, kunt u een diagnostisch rapport bekijken dat specifiek is voor uw functie-app. Diagnostische rapporten gebruiken status pictogrammen om aan te geven of er specifieke problemen zijn met uw app. U ziet een gedetailleerde beschrijving van het probleem, aanbevolen acties, gerelateerde metrische gegevens en nuttige docs. Aangepaste diagnostische rapporten worden gegenereerd op basis van een reeks controles die worden uitgevoerd in uw functie-app. Diagnostische rapporten kunnen een nuttig hulp middel zijn voor het lokaliseren van problemen in de functie-app en het verhelpen van het probleem.

## <a name="find-the-problem-code"></a>De probleem code zoeken

Voor functies op basis van een script kunt u **functie-uitvoering en fouten** onder **functie-app omlaag of fouten rapporteren** om te beperken op de regel code die uitzonde ringen of fouten veroorzaakt. U kunt dit hulp programma gebruiken voor het verkrijgen van de hoofd oorzaak en het oplossen van problemen met een specifieke regel code. Deze optie is niet beschikbaar voor vooraf gecompileerde C#-en Java-functies.

:::image type="content" source="./media/functions-diagnostics/diagnostic-report-on-function-execution-errors.png" alt-text="Diagnostisch rapport over fouten bij de uitvoering van functies" border="false":::

:::image type="content" source="./media/functions-diagnostics/function-exception.png" alt-text="Weer gave van uitzonderings Details." border="false":::

## <a name="next-steps"></a>Volgende stappen

U kunt vragen stellen of feedback geven over Azure Functions diagnostische gegevens op [UserVoice](https://feedback.azure.com/forums/355860-azure-functions). Neem `[Diag]` in de titel van uw feedback op.

> [!div class="nextstepaction"]
> [Uw functie-apps bewaken](functions-monitoring.md)
