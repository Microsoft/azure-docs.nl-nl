---
title: Samen vatting van de conversie van Designer naar werkmappen Azure Monitor weer geven
description: Machtigingen die zijn vereist voor toegang tot werkmappen bij het overstappen van weer gaven in Azure Monitor.
author: austonli
ms.author: aul
ms.subservice: ''
ms.topic: conceptual
ms.date: 02/07/2020
ms.openlocfilehash: c5b3096c4eb8cf71ff1d13476fa09cc65ef413a7
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100608993"
---
# <a name="view-designer-to-workbooks-conversion-summary-and-access"></a>Conversie samenvatting en toegang van Designer naar werkmappen weer geven
[View Designer](view-designer.md) is een functie van Azure monitor waarmee u aangepaste weer gaven kunt maken waarmee u gegevens in uw werk ruimte log Analytics kunt visualiseren, met grafieken, lijsten en tijd lijnen. Deze worden gefaseerd en vervangen door werkmappen, die extra functionaliteit bieden. In dit artikel vindt u informatie over het maken van overzichts samenvatting en machtigingen die vereist zijn voor toegang tot werkmappen.

## <a name="creating-your-workspace-summary-from-azure-dashboard"></a>Uw werkruimte samenvatting maken op basis van het Azure-dash board
Gebruikers van Designer weer geven kunnen bekend zijn met een overzichts tegel die een set weer gaven vertegenwoordigt. Voor het bijhouden van een visueel overzicht zoals de samen vatting van de weer gave Designer-werk bladen worden vastgemaakte stappen geboden, die kunnen worden vastgemaakt aan uw [Azure Portal dash board](../../azure-portal/azure-portal-dashboards.md). Net als de overzichts tegels in de samen vatting van de werk ruimte, worden vastgemaakte werkmap items rechtstreeks gekoppeld aan de werkmap weergave.

U kunt profiteren van de geavanceerde functies voor aanpassing van Azure-Dash boards, waarmee automatisch vernieuwen, verplaatsen, verg Roten/verkleinen en aanvullende filters voor uw vastgemaakte items en visualisaties kunnen worden gefilterd. 

![Scherm afbeelding toont een aangepast Azure-dash board met de titel samen vatting van de werk ruimte.](media/view-designer-conversion-access/dashboard.png)

Maak een nieuw Azure-dash board of selecteer een bestaand dash board om te beginnen met het vastmaken van werkmappen-items.

Als u het afzonderlijke item wilt vastmaken, moet u het PIN-pictogram inschakelen voor uw specifieke stap. Als u dit wilt doen, selecteert u de bijbehorende knop **bewerken** voor de stap en selecteert u vervolgens het tandwiel pictogram om **Geavanceerde instellingen** te openen. Schakel het selectie vakje in om **altijd het speld pictogram op deze stap weer te geven**. er wordt een speld pictogram in de rechter bovenhoek van de stap weer gegeven. Met deze pincode kunt u specifieke visualisaties aan uw dash board vastmaken, zoals in de overzichts tegels.

![Stap pincode](media/view-designer-conversion-access/pin-step.png)


Mogelijk wilt u ook meerdere visualisaties van de werkmap of de hele werkmap inhoud vastmaken aan een dash board. Als u de hele werkmap wilt vastmaken, selecteert u **bewerken** op de bovenste werk balk om de **bewerkings modus** in te scha kelen. Er wordt een speld pictogram weer gegeven, zodat u het hele werkmap item of alle afzonderlijke stappen en visualisaties in de werkmap kunt vastmaken.

![Alles vastmaken](media/view-designer-conversion-access/pin-all.png)



## <a name="sharing-and-viewing-permissions"></a>Machtigingen voor delen en weer geven 
Werkmappen hebben het voor deel van ofwel een privé-of gedeeld document. Opgeslagen werkmappen worden standaard opgeslagen onder **mijn rapporten**, wat betekent dat alleen de maker deze werkmap kan bekijken.

U kunt uw werkmappen delen door het **deel** pictogram in de bovenste werk balk te selecteren in de **bewerkings modus**. U wordt gevraagd uw werkmap te verplaatsen naar **gedeelde rapporten**, waarmee een koppeling wordt gegenereerd die directe toegang biedt tot de werkmap.

Een gebruiker kan alleen een gedeelde werkmap weer geven als ze toegang hebben tot het abonnement en de resource groep waarin de werkmap is opgeslagen.

![Toegang op basis van abonnementen](media/view-designer-conversion-access/subscription-access.png)

## <a name="next-steps"></a>Volgende stappen

- [Algemene taken](view-designer-conversion-tasks.md)