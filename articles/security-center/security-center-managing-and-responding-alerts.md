---
title: Beveiligingswaarschuwingen beheren in Azure Security Center | Microsoft Docs
description: Dit document bevat informatie over het gebruik van de mogelijkheden van Azure Security Center om beveiligingswaarschuwingen te beheren en hierop te reageren.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: b88a8df7-6979-479b-8039-04da1b8737a7
ms.service: security-center
ms.topic: how-to
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/17/2021
ms.author: memildin
ms.openlocfilehash: a47ece762f2df3fa18cf2b79d338c28d4069c4ad
ms.sourcegitcommit: 58ff80474cd8b3b30b0e29be78b8bf559ab0caa1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100633471"
---
# <a name="manage-and-respond-to-security-alerts-in-azure-security-center"></a>Beveiligingswaarschuwingen beheren en erop reageren in Azure Security Center

In dit onderwerp wordt beschreven hoe u de waarschuwingen van Security Center kunt weer geven en verwerken en uw resources kunt beveiligen.

Geavanceerde detecties die beveiligings waarschuwingen activeren, zijn alleen beschikbaar met Azure Defender. Er is een gratis proefversie beschikbaar. Zie [Azure Defender inschakelen](security-center-pricing.md#enable-azure-defender)als u een upgrade wilt uitvoeren.

## <a name="what-are-security-alerts"></a>Wat zijn beveiligingswaarschuwingen?
Security Center verzamelt, analyseert en integreert automatisch logboekgegevens van uw Azure-resources, het netwerk en verbonden partneroplossingen, zoals firewall- en eindpuntbeveiligingsoplossingen om werkelijke dreigingen te detecteren en fout-positieven te reduceren. In Security Center wordt een lijst met beveiligingswaarschuwingen met prioriteiten weergegeven samen met de informatie die u nodig hebt om snel onderzoek te doen naar het probleem en aanbevelingen voor het herstellen van een aanval.

Zie [beveiligings waarschuwingen-een referentie gids](alerts-reference.md)voor meer informatie over de verschillende typen waarschuwingen.

Zie [how Azure Security Center detecteert en reageert op bedreigingen](security-center-alerts-overview.md)voor een overzicht van de manier waarop Security Center waarschuwingen genereert.


## <a name="manage-your-security-alerts"></a>Beveiligings waarschuwingen beheren

1. Selecteer op de pagina overzicht van Security Center de tegel **beveiligings waarschuwingen** boven aan de pagina of de koppeling vanuit de zijbalk...

    :::image type="content" source="media/security-center-managing-and-responding-alerts/overview-page-alerts-links.png" alt-text="Ophalen van de pagina beveiligings waarschuwingen op de pagina overzicht van Azure Security Center":::

    De pagina beveiligings waarschuwingen wordt geopend.

    :::image type="content" source="media/security-center-managing-and-responding-alerts/alerts-page.png" alt-text="Lijst met beveiligings waarschuwingen voor Azure Security Center":::

1. Als u de lijst met waarschuwingen wilt filteren, selecteert u een van de relevante filters. U kunt eventueel ook extra filters toevoegen met de optie **filter toevoegen** .

    :::image type="content" source="./media/security-center-managing-and-responding-alerts/alerts-adding-filters-small.png" alt-text="Filters toevoegen aan de weer gave waarschuwingen" lightbox="./media/security-center-managing-and-responding-alerts/alerts-adding-filters-large.png":::

    De lijst wordt bijgewerkt op basis van de filter opties die u hebt geselecteerd. Filteren kan zeer nuttig zijn. U kunt u bijvoorbeeld concentreren op de beveiligingswaarschuwingen van de afgelopen 24 uur, omdat u een mogelijke inbreuk in het systeem onderzoekt.


## <a name="respond-to-security-alerts"></a>Reageren op beveiligingswaarschuwingen

1. Selecteer een waarschuwing in de lijst **beveiligings waarschuwingen** . Er wordt een zijvenster geopend met een beschrijving van de waarschuwing en alle betrokken resources. 

    :::image type="content" source="./media/security-center-managing-and-responding-alerts/alerts-details-pane.png" alt-text="Weer gave van Mini Details van een beveiligings waarschuwing":::

    > [!TIP]
    > Als dit zijvenster is geopend, kunt u de lijst met waarschuwingen snel bekijken met de pijl omhoog en omlaag op het toetsen bord.

1. Selecteer **volledige details weer geven** voor meer informatie.

    In het linkerdeel venster van de pagina beveiligings waarschuwing wordt informatie op hoog niveau weer gegeven met betrekking tot de beveiligings waarschuwing: titel, Ernst, status, activiteit tijd, beschrijving van de verdachte activiteit en de betrokken resource. Naast de betreffende resource bevinden de Azure-Tags die relevant zijn voor de resource. Gebruik deze om de organisatie context van de resource af te leiden bij het onderzoeken van de waarschuwing.

    Het rechterdeel venster bevat het tabblad **waarschuwings Details** met meer details over de waarschuwing die u kan helpen bij het onderzoeken van het probleem: IP-adressen, bestanden, processen en meer.
     
    ![Suggesties voor wat u moet doen over beveiligings waarschuwingen](./media/security-center-managing-and-responding-alerts/security-center-alert-remediate.png)

    In het rechterdeel venster vindt u ook het tabblad **actie ondernemen** . Gebruik dit tabblad om verdere acties uit te voeren met betrekking tot de beveiligings waarschuwing. Acties zoals:
    - *De dreiging verminderen* : Hiermee worden hand matige herstel stappen voor deze beveiligings waarschuwing geboden
    - *Toekomstige aanvallen voor komen* : bevat aanbevelingen voor beveiliging om de kwets baarheid te verminderen, de beveiliging postuur te verbeteren en toekomstige aanvallen te voor komen
    - *Automatische reactie activeren* : biedt de mogelijkheid om een logische app te activeren als reactie op deze beveiligings waarschuwing
    - *Vergelijk bare waarschuwingen onderdrukken* : biedt de mogelijkheid om toekomstige waarschuwingen met vergelijk bare kenmerken te onderdrukken als de waarschuwing niet relevant is voor uw organisatie

    ![Tabblad actie uitvoeren](./media/security-center-managing-and-responding-alerts/alert-take-action.png)




## <a name="see-also"></a>Zie ook

In dit document hebt u geleerd hoe u beveiligings waarschuwingen kunt weer geven. Raadpleeg de volgende pagina's voor gerelateerde materialen:

- [Regels voor het onderdrukken van waarschuwingen configureren](alerts-suppression-rules.md)
- [Reacties op Security Center triggers automatiseren](workflow-automation.md)
- [Referentiegids met beveiligingswaarschuwingen](alerts-reference.md)
