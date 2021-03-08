---
title: Aangepaste Details van het Opper vlak in azure-Sentinel-waarschuwingen | Microsoft Docs
description: Details van aangepaste gebeurtenissen in waarschuwingen in azure Sentinel Analytics-regels extra heren en weer gegeven, voor betere en meer volledige informatie over incidenten
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
ms.date: 02/10/2021
ms.author: yelevin
ms.openlocfilehash: 45f0ef5366d97c275c40d4d436020dbaf3501d42
ms.sourcegitcommit: 6386854467e74d0745c281cc53621af3bb201920
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/08/2021
ms.locfileid: "102456224"
---
# <a name="surface-custom-event-details-in-alerts-in-azure-sentinel"></a>Details van aangepaste gebeurtenis voor het Opper vlak in waarschuwingen in azure Sentinel 

> [!IMPORTANT]
>
> - De functie voor aangepaste Details is beschikbaar als **Preview-versie**. Zie de [aanvullende gebruiks voorwaarden voor Microsoft Azure previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor aanvullende juridische voor waarden die van toepassing zijn op Azure-functies die in bèta, preview of op andere wijze nog niet beschikbaar zijn in algemene Beschik baarheid.

## <a name="introduction"></a>Introductie

[Geplande query Analytics-regels](tutorial-detect-threats-custom.md) analyseren **gebeurtenissen** van gegevens bronnen die zijn verbonden met Azure Sentinel, en genereren **waarschuwingen** wanneer de inhoud van deze gebeurtenissen significant is vanuit een beveiligings perspectief. Deze waarschuwingen worden verder geanalyseerd, gegroepeerd en gefilterd door de verschillende motoren van Azure Sentinel en gedestilleerd in **incidenten** die de aandacht van de SOC-analist rechtvaardigen. Wanneer de analist echter het incident bekijkt, zijn alleen de eigenschappen van de onderdeel waarschuwingen zelf zichtbaar. Ophalen van de daad werkelijke inhoud: de informatie die in de gebeurtenissen is opgenomen-vereist een aantal Blijf spitten.

Met de functie voor **aangepaste Details** in de **wizard Analytics-regel** kunt u gebeurtenis gegevens laten vallen in de waarschuwingen die zijn gemaakt op basis van die gebeurtenissen, waardoor het onderdeel gebeurtenis gegevens van de eigenschappen van de waarschuwing wordt weer gegeven. Dit geeft u onmiddellijk inzicht in de inhoud van de gebeurtenissen in uw incidenten, zodat u conclusies kunt sorteren, onderzoeken, aftrekken en kunt reageren met veel meer snelheid en efficiëntie.

De procedure die hieronder wordt beschreven, maakt deel uit van de wizard analyse regel maken. Het wordt hier afzonderlijk behandeld om het scenario van het toevoegen of wijzigen van aangepaste gegevens in een bestaande analyse regel te verhelpen.

## <a name="how-to-surface-custom-event-details"></a>Details van het Opper vlak van aangepaste gebeurtenissen

1. Selecteer in het navigatie menu van de Azure-Sentinel **Analytics**.

1. Selecteer een geplande query regel en klik op **bewerken**. Of maak een nieuwe regel door te klikken op **&#10132; geplande query regel maken** boven aan het scherm.

1. Klik op het tabblad **regel logica instellen** .

1. Selecteer in de sectie **waarschuwings verbetering** de optie **aangepaste Details**.

    :::image type="content" source="media/surface-custom-details-in-alerts/alert-enhancement.png" alt-text="Aangepaste details zoeken en selecteren":::

1. Voeg in het gedeelte nu uitgebreide **aangepaste Details** de sleutel-waardeparen toe die overeenkomen met de details die u wilt Opper vlakten:

    1. Voer in het veld **sleutel** een naam in van uw keuze die wordt weer gegeven als de veld naam in waarschuwingen.

    1. Kies in het veld **waarde** de para meter van de gebeurtenis die u wilt weer geven in de waarschuwingen in de vervolg keuzelijst. Deze lijst wordt ingevuld met waarden die overeenkomen met de velden in de tabellen die het onderwerp van de regel query zijn.
    
        :::image type="content" source="media/surface-custom-details-in-alerts/custom-details.png" alt-text="Aangepaste Details toevoegen":::

1. Klik op **Nieuw toevoegen** aan het Opper vlak meer details. Herhaal de laatste stappen voor het definiëren van sleutel-waardeparen. 

    Als u van gedachten verandert of als u een fout hebt gemaakt, kunt u een aangepast detail verwijderen door te klikken op het prullenbak pictogram naast de vervolg keuzelijst **waarde** voor dat detail.

1. Wanneer u klaar bent met het definiëren van aangepaste gegevens, klikt u op het tabblad **controleren en maken** . Zodra de validatie van de regel is voltooid, klikt u op **Opslaan**.

    > [!NOTE]
    > 
    >**Servicelimieten**
    > - U kunt **Maxi maal 20 aangepaste Details** definiëren in één analyse regel.
    >
    > - De maximale grootte voor alle aangepaste Details is **2 KB**.

## <a name="next-steps"></a>Volgende stappen
In dit document hebt u geleerd hoe u aangepaste Details in waarschuwingen kunt weer gegeven met behulp van Azure Sentinel Analytics-regels. Zie de volgende artikelen voor meer informatie over Azure Sentinel:
- Down load de volledige afbeelding over [geplande query Analytics-regels](tutorial-detect-threats-custom.md).
- Meer informatie over [entiteiten in azure Sentinel](entities-in-azure-sentinel.md).
