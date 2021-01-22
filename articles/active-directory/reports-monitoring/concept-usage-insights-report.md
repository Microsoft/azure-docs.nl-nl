---
title: Het rapport gebruik en inzichten | Microsoft Docs
description: Inleiding tot het rapport over gebruik en inzichten in de Azure Active Directory Portal
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 3fba300d-18fc-4355-9924-d8662f563a1f
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 05/13/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: 54bce5e839786862a6dac9aeb685dd364547a09a
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/22/2021
ms.locfileid: "98685022"
---
# <a name="usage-and-insights-report-in-the-azure-active-directory-portal"></a>Het rapport gebruik en inzichten in de Azure Active Directory Portal

Met het rapport gebruik en inzichten kunt u een toepassings gerichte weer gave van uw aanmeldings gegevens verkrijgen. U vindt antwoorden op de volgende vragen:

*   Wat zijn de meestgebruikte toepassingen in mijn organisatie?
*   Welke toepassingen hebben de meeste mislukte aanmeldingen? 
*   Wat zijn de belangrijkste aanmeldings fouten voor elke toepassing?

## <a name="prerequisites"></a>Vereisten 

Voor toegang tot de gegevens in het rapport gebruik en inzichten hebt u het volgende nodig:

* Een Azure AD-tenant
* Een Azure AD Premium-licentie (P1/P2) voor het weer geven van de aanmeldings gegevens
* Een gebruiker in de rollen globale beheerder, beveiligings beheerder, beveiligings lezer of rapport lezers. Daarnaast hebben alle gebruikers (niet-beheerders) toegang tot hun eigen aanmeldingen. 

## <a name="access-the-usage-and-insights-report"></a>Het rapport gebruik en inzichten openen

1. Navigeer naar [Azure Portal](https://portal.azure.com).
2. Selecteer de juiste map, selecteer **Azure Active Directory** en kies **bedrijfs toepassingen**.
3. Selecteer in de sectie **activiteit** **gebruik & inzichten** om het rapport te openen. 

![Scherm opname toont het gebruik & inzichten die zijn geselecteerd in de sectie activiteit.](./media/concept-usage-insights-report/main-menu.png)
                                     

## <a name="use-the-report"></a>Het rapport gebruiken

In het rapport gebruik en inzichten ziet u een lijst met toepassingen met een of meer aanmeldings pogingen, en kunt u sorteren op het aantal geslaagde aanmeldingen, mislukte aanmeldingen en het succes percentage.

Als u onder aan de lijst op **laden** klikt, kunt u aanvullende toepassingen op de pagina bekijken. U kunt het datum bereik selecteren om alle toepassingen weer te geven die binnen het bereik zijn gebruikt.

![Scherm opname toont het gebruik & inzichten voor toepassings activiteit, waar u een bereik kunt selecteren en aanmeldings activiteiten voor verschillende apps weer geven.](./media/concept-usage-insights-report/usage-and-insights-report.png)

U kunt ook de focus instellen voor een specifieke toepassing. Selecteer **aanmeldings activiteit weer geven** om de aanmeldings activiteit gedurende een bepaalde periode voor de toepassing en de belangrijkste fouten te bekijken.  

Wanneer u een dag selecteert in de grafiek voor het gebruik van de toepassing, krijgt u een gedetailleerde lijst van de aanmeldings activiteiten voor de toepassing.  

:::image type="content" source="./media/concept-usage-insights-report/usage-and-insights-application-report.png" alt-text="Scherm opname toont het gebruik & inzichten voor een specifieke toepassing, waar u een grafiek voor de aanmeldings activiteit kunt zien.":::

## <a name="next-steps"></a>Volgende stappen

* [Aanmeldingenrapport](concept-sign-ins.md)
