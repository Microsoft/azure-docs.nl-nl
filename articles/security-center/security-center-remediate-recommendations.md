---
title: Aanbevelingen herstellen in Azure Security Center | Microsoft Docs
description: In dit artikel wordt uitgelegd hoe u reageert op aanbevelingen in Azure Security Center om uw resources te beschermen en te voldoen aan het beveiligings beleid.
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 03/04/2021
ms.author: memildin
ms.openlocfilehash: f382646c889d004738064cae2d09fd66d897b110
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102438264"
---
# <a name="remediate-recommendations-in-azure-security-center"></a>Aanbevelingen oplossen in Azure Security Center

Aanbevelingen bieden suggesties voor een betere beveiliging van uw resources. U implementeert een aanbeveling door de herstels tappen te volgen die worden beschreven in de aanbeveling.

## <a name="remediation-steps"></a>Herstels tappen <a name="remediation-steps"></a>

Nadat u alle aanbevelingen hebt bekeken, moet u bepalen welke u het eerst wilt herstellen. We raden u aan de beveiligings maatregelen te priori teren met het hoogste potentieel om uw beveiligde score te verhogen.

1. Selecteer een aanbeveling in de lijst.

1. Volg de instructies in de sectie **herstel stappen** . Elke aanbeveling heeft een eigen set instructies. De volgende scherm afbeelding toont herbemiddelings stappen voor het configureren van toepassingen zodat alleen verkeer via HTTPS wordt toegestaan.

    :::image type="content" source="./media/security-center-remediate-recommendations/security-center-remediate-recommendation.png" alt-text="Hand matige herstel stappen voor een aanbeveling" lightbox="./media/security-center-remediate-recommendations/security-center-remediate-recommendation.png":::

1. Zodra dit is voltooid, wordt een melding weer gegeven waarin wordt gemeld of het probleem is opgelost.

## <a name="quick-fix-remediation"></a>Snelle oplossingen voor herstel

Om het herstel te vereenvoudigen en de beveiliging van uw omgeving te verbeteren (en uw veilige score te verg Roten), bevatten veel aanbevelingen een optie voor snel oplossen.

Met snelle oplossing kunt u snel een aanbeveling op meerdere resources herstellen.

> [!TIP]
> Oplossingen voor snel oplossen zijn alleen beschikbaar voor specifieke aanbevelingen. Als u de aanbevelingen met een beschik bare snelle oplossing wilt vinden, gebruikt u het filter **antwoord acties** voor de lijst met aanbevelingen:
> 
> :::image type="content" source="media/security-center-remediate-recommendations/quick-fix-filter.png" alt-text="Gebruik de filters boven de lijst met aanbevelingen om aanbevelingen met de optie snel herstellen te vinden.":::

Voor het implementeren van een oplossing voor snel oplossen:

1. In de lijst met aanbevelingen waarvoor de **snelle oplossing** is geïnstalleerd. Selecteer een aanbeveling.

    [![Selecteer snelle oplossing.](media/security-center-remediate-recommendations/security-center-quick-fix-select.png)](media/security-center-remediate-recommendations/security-center-quick-fix-select.png#lightbox)

1. Op het tabblad **beschadigde bronnen** selecteert u de resources waarvoor u de aanbeveling wilt implementeren en selecteert u **herstellen**.

    > [!NOTE]
    > Sommige van de vermelde bronnen zijn mogelijk uitgeschakeld, omdat u niet over de juiste machtigingen beschikt om deze te wijzigen.

1. In het bevestigings venster leest u de details van het herstel en de implicaties.

    ![Snel oplossen](./media/security-center-remediate-recommendations/security-center-quick-fix-view.png)

    > [!NOTE]
    > De implicaties worden weer gegeven in het grijze vak in het venster **resources herstellen** dat wordt geopend nadat u op **herstellen** hebt geklikt. Ze geven een lijst weer met de wijzigingen die worden aangebracht wanneer u doorgaat met het oplossen van problemen met snel herstel.

1. Voer indien nodig de relevante para meters in en goed keuring van het herstel.

    > [!NOTE]
    > Het kan enkele minuten duren voordat het herstel is voltooid om de resources te bekijken op het tabblad onterecht **resources** . Controleer het [activiteiten logboek](#activity-log)om de herstel acties weer te geven.

1. Zodra dit is voltooid, wordt een melding weer gegeven dat u wordt geïnformeerd als het herstel is geslaagd.

## <a name="quick-fix-remediation-logging-in-the-activity-log"></a>Snel herstel logboek registratie in het activiteiten logboek <a name="activity-log"></a>

Voor de herstel bewerking wordt gebruikgemaakt van een sjabloon implementatie of REST PATCH-API-aanroep om de configuratie van de bron toe te passen. Deze bewerkingen worden geregistreerd in het [Azure-activiteiten logboek](../azure-resource-manager/management/view-activity-logs.md).


## <a name="next-steps"></a>Volgende stappen

In dit document hebt u geleerd hoe u aanbevelingen in Security Center kunt herstellen. Zie de volgende pagina's voor meer informatie over het Security Center:

* [Beveiligings beleid instellen in azure Security Center](tutorial-security-policy.md) -meer informatie over het configureren van beveiligings beleid voor uw Azure-abonnementen en-resource groepen
* [Wat zijn beveiligings beleid, initiatieven en aanbevelingen?](security-policy-concept.md)