---
title: Gebruik de analyse van entiteits gedrag om geavanceerde bedreigingen te detecteren | Microsoft Docs
description: Gedrags analyse van gebruikers en entiteiten inschakelen in azure Sentinel en gegevens bronnen configureren
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/25/2021
ms.author: yelevin
ms.openlocfilehash: 3f3e945a00ec7bba75deebb56118d45aa7ff571d
ms.sourcegitcommit: 7ec45b7325e36debadb960bae4cf33164176bc24
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/16/2021
ms.locfileid: "100530719"
---
# <a name="enable-user-and-entity-behavior-analytics-ueba-in-azure-sentinel"></a>UEBA (User and entity Behavior Analytics) inschakelen in azure Sentinel 

> [!IMPORTANT]
>
> - De functies UEBA en Entity pages zijn nu **algemeen beschikbaar** in **_alle_** Azure Sentinel-geografi en-regio's.

## <a name="prerequisites"></a>Vereisten

Om deze functie in of uit te scha kelen (deze vereisten zijn niet vereist voor het gebruik van de functie):

- Uw gebruiker moet lid zijn van de Azure Active Directory van uw organisatie en geen gast gebruiker.

- Aan uw gebruiker moet de rol van **globale beheerder** of **beveiligings beheerder** zijn toegewezen in azure AD.

- Aan uw gebruiker moet ten minste één van de volgende **Azure-rollen** zijn toegewezen (meer [informatie over Azure RBAC](roles.md)):
    - **Azure Sentinel contributor** op het niveau van de werk ruimte of de resource groep.
    - **Log Analytics Inzender** op het niveau van de resource groep of het abonnement.

- Er mogen geen Azure-resource vergrendelingen op uw werk ruimte worden toegepast. Meer [informatie over het vergren delen van Azure-resources](../azure-resource-manager/management/lock-resources.md).

> [!NOTE]
> Er is geen speciale licentie vereist om de UEBA-functionaliteit toe te voegen aan Azure Sentinel, maar er kunnen **extra kosten** in rekening worden gebracht.

## <a name="how-to-enable-user-and-entity-behavior-analytics"></a>De analyse van gebruikers-en entiteits gedrag inschakelen

1. Selecteer **entiteit gedrag** in het navigatie menu van de Azure-Sentinel.

1. Schakel onder de kop **deze in** **op aan.**

1. Klik op de knop **gegevens bronnen selecteren** .

1. Schakel in het selectie deel venster **gegevens bron** de selectie vakjes in naast de gegevens bronnen waarvoor u UEBA wilt inschakelen en selecteer vervolgens **Toep assen**.

    > [!NOTE]
    >
    > In de onderste helft van het **selectie deel venster gegevens bron** ziet u een lijst met UEBA-ondersteunde gegevens bronnen die u nog niet hebt ingeschakeld. 
    >
    > Wanneer u UEBA hebt ingeschakeld, hebt u de optie om nieuwe gegevens bronnen te verbinden, zodat ze rechtstreeks vanuit het deel venster gegevens connector kunnen worden UEBA als deze UEBA-compatibel zijn.

1. Selecteer **Ga naar entiteit zoeken**. Hiermee gaat u naar het deel venster entiteit zoeken, dat vanaf nu wordt weer geven wanneer u **entiteit gedrag** kiest in het hoofd menu van Azure.

## <a name="next-steps"></a>Volgende stappen
In dit document hebt u geleerd hoe u UEBA (User and entity Behavior Analytics) inschakelt en configureert in azure Sentinel. Zie de volgende artikelen voor meer informatie over Azure Sentinel:
- Meer informatie over het [verkrijgen van inzicht in uw gegevens en mogelijke bedreigingen](quickstart-get-visibility.md).
- Ga aan de slag met [het detecteren van bedreigingen met Azure Sentinel](tutorial-detect-threats-built-in.md).
