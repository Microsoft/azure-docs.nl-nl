---
title: Waarschuwingen instellen voor Azure Event Grid metrische gegevens en activiteiten logboek bewerkingen
description: In dit artikel wordt beschreven hoe u waarschuwingen maakt voor Azure Event Grid metrische gegevens en activiteiten logboek bewerkingen.
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: 48cb402e31435cb3e9390e8aeb461fcc5f90702f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100572021"
---
# <a name="set-alerts-on-azure-event-grid-metrics-and-activity-logs"></a>Waarschuwingen instellen voor Azure Event Grid metrische gegevens en activiteiten logboeken
In dit artikel wordt beschreven hoe u waarschuwingen maakt voor Azure Event Grid metrische gegevens en activiteiten logboek bewerkingen. U kunt waarschuwingen maken voor zowel de metrische gegevens voor publiceren als levering voor Azure Event Grid resources (onderwerpen en domeinen). Voor systeem onderwerpen [maakt u waarschuwingen op de pagina **metrische gegevens**](#create-alerts-using-the-metrics-page).

## <a name="create-alerts-on-dead-lettered-events"></a>Waarschuwingen voor onbestelbare gebeurtenissen maken
De volgende procedure laat zien hoe u een waarschuwing kunt maken voor de metriek van **onbestelbare gebeurtenissen** voor een aangepast onderwerp. In dit voor beeld wordt een e-mail bericht verzonden naar de eigenaar van de Azure-resource groep wanneer het aantal onbestelbare gebeurtenissen voor een onderwerp boven 10 komt. 

1. Selecteer op de pagina **Event grid onderwerp** voor uw onderwerp **waarschuwingen** in het menu links en selecteer vervolgens **+ nieuwe waarschuwings regel**. 

    :::image type="content" source="./media/monitor-event-delivery/new-alert-button.png" alt-text="Pagina waarschuwingen-knop nieuwe waarschuwings regel":::
2. Controleer op de pagina **waarschuwings regel maken** of het onderwerp is geselecteerd voor de resource. Klik vervolgens op **voor waarde selecteren**. 

    :::image type="content" source="./media/monitor-event-delivery/alert-select-condition.png" alt-text="Pagina waarschuwingen-voor waarde selecteren":::    
3. Voer op de pagina **signaal logica configureren** de volgende stappen uit:
    1. Selecteer een metrische waarde of een vermelding in het activiteiten logboek. In dit voor beeld wordt **onbestelbare gebeurtenissen** geselecteerd. 

        :::image type="content" source="./media/monitor-event-delivery/select-dead-lettered-events.png" alt-text="Onbestelbare gebeurtenissen selecteren":::        
    2. Selecteer dimensies (optioneel). 
        
        :::image type="content" source="./media/monitor-event-delivery/configure-signal-logic.png" alt-text="Signaallogica configureren":::        

        > [!NOTE]
        > U kunt **+** de knop voor **EventSubscriptionName** selecteren om de naam van een gebeurtenis abonnement op te geven om gebeurtenissen te filteren. 
    3. Schuif omlaag. Selecteer in de sectie **waarschuwings logica** een **operator**, **aggregatie type**, voer een **drempel waarde** in en selecteer **gereed**. In dit voor beeld wordt een waarschuwing geactiveerd wanneer het totale aantal onbestelbare gebeurtenissen groter is dan 10. 
    
        :::image type="content" source="./media/monitor-event-delivery/alert-logic.png" alt-text="Waarschuwingslogica":::                
4. Klik op de pagina **waarschuwings regel maken** op **actie groep selecteren**.

    :::image type="content" source="./media/monitor-event-delivery/select-action-group-button.png" alt-text="Knop actie groep selecteren":::
5. Selecteer **actie groep maken** op de werk balk om een nieuwe actie groep te maken. U kunt ook een bestaande actie groep selecteren.        
6. Voer op de pagina **actie groep toevoegen** de volgende stappen uit:
    1. Voer een **naam in voor de actie groep**.
    1. Voer een **korte naam** in voor de actie groep.
    1. Selecteer een **Azure-abonnement** waarin u de actie groep wilt maken.
    1. Selecteer de **Azure-resource groep** waarin u de actie groep wilt maken.
    1. Voer een **naam in voor de actie**. 
    1. Selecteer het **actie type**. In dit voor beeld is de **rol e-mail Azure Resource Manager** geselecteerd, met name de rol van **eigenaar** . 
    1. Selecteer **OK** om de pagina te sluiten. 
    
        :::image type="content" source="./media/monitor-event-delivery/add-action-group-page.png" alt-text="Pagina actie groep toevoegen":::                   
7. Ga terug naar de pagina **waarschuwings regel maken** en voer een naam in voor de waarschuwings regel en selecteer vervolgens **waarschuwings regel maken**.

    :::image type="content" source="./media/monitor-event-delivery/alert-rule-name.png" alt-text="Naam van waarschuwingsregel":::  
8. Nu ziet u op de pagina **waarschuwingen** van het onderwerp een koppeling voor het beheren van waarschuwings regels als er nog geen waarschuwingen zijn. Als er waarschuwingen zijn, selecteert u **waarschuwings regels voor beheer** op de werk balk.  

    :::image type="content" source="./media/monitor-event-delivery/manage-alert-rules.png" alt-text="Waarschuwingen beheren":::

## <a name="create-alerts-on-other-metrics-or-activity-log-operations"></a>Waarschuwingen maken voor andere metrische gegevens of activiteiten logboek bewerkingen
In de vorige sectie wordt uitgelegd hoe u waarschuwingen kunt maken over gebeurtenissen met onbestelbare berichten. De stappen voor het maken van waarschuwingen over andere metrische gegevens of activiteiten logboeken zijn vergelijkbaar. 

Als u bijvoorbeeld een waarschuwing wilt maken voor een gebeurtenis voor een leverings fout, selecteert u de optie voor het afleveren van **mislukte gebeurtenissen** op de pagina **signaal logica configureren** . 

:::image type="content" source="./media/set-alerts/delivery-failed-events.png" alt-text="Mislukte leverings gebeurtenissen selecteren":::


## <a name="create-alerts-using-the-metrics-page"></a>Waarschuwingen maken met behulp van de pagina metrische gegevens
U kunt ook waarschuwingen maken met behulp van de pagina **metrische gegevens** . De stappen zijn vergelijkbaar. Voor systeem onderwerpen kunt u de pagina **metrische gegevens** alleen gebruiken om waarschuwingen te maken, omdat de pagina **waarschuwingen** niet beschikbaar is. 

:::image type="content" source="./media/monitor-event-delivery/metric-page-create-alert-button.png" alt-text="Pagina metrische gegevens-knop waarschuwing maken":::   
    

> [!NOTE]
> Dit artikel heeft geen betrekking op alle verschillende stappen en combi naties die u kunt gebruiken om een waarschuwing te maken. Zie [overzicht van waarschuwingen](../azure-monitor/alerts/alerts-metric.md)voor een overzicht van waarschuwingen.

## <a name="next-steps"></a>Volgende stappen

* [Event grid aflevering van berichten en probeer het opnieuw](delivery-and-retry.md).
* Zie [Een inleiding tot Event Grid](overview.md) voor een inleiding tot Event Grid.
* Zie [aangepaste gebeurtenissen maken en routeren met Azure Event grid](custom-event-quickstart.md)om snel aan de slag te gaan met Event grid.
