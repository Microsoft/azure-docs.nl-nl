---
title: Waarschuwingen voor activiteiten logboeken maken voor Labs in Azure DevTest Labs
description: In dit artikel worden de stappen beschreven voor het maken van waarschuwingen voor het activiteiten logboek voor Lab in Azure DevTest Labs.
ms.topic: how-to
ms.date: 07/10/2020
ms.openlocfilehash: f774e3291961c58f55a9ed24026535e076235b98
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100588765"
---
# <a name="create-activity-log-alerts-for-labs-in-azure-devtest-labs"></a>Waarschuwingen voor activiteiten logboeken maken voor Labs in Azure DevTest Labs
In dit artikel wordt uitgelegd hoe u waarschuwingen voor activiteiten logboeken voor Labs maakt in Azure DevTest Labs (bijvoorbeeld: wanneer een virtuele machine wordt gemaakt of wanneer een virtuele machine wordt verwijderd).

## <a name="create-alerts"></a>Waarschuwingen maken
In dit voor beeld maakt u een waarschuwing voor alle beheer bewerkingen op een Lab met een actie waarmee een e-mail wordt verzonden naar abonnements eigenaren. 

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
1. Typ **monitor** in de zoek balk van de Azure Portal en selecteer vervolgens **monitor** in de lijst met resultaten. 

    :::image type="content" source="./media/activity-logs/search-monitor.png" alt-text="Zoeken naar monitor":::        
1. Selecteer in het menu links **waarschuwingen** en selecteer **nieuwe waarschuwings regel** op de werk balk. 

    :::image type="content" source="./media/activity-logs/alerts-page.png" alt-text="Pagina waarschuwingen":::    
1. Klik op de pagina **waarschuwings regel maken** op **resource selecteren**. 

    :::image type="content" source="./media/activity-logs/select-resource-link.png" alt-text="Resource voor de waarschuwing selecteren":::        
1. Selecteer **DevTest Labs** voor **filteren op resource type**, selecteer uw Lab in de lijst en selecteer vervolgens **gereed**.

    :::image type="content" source="./media/activity-logs/select-lab-resource.png" alt-text="Uw Lab selecteren als de resource":::
1. Klik op de pagina **waarschuwings regel maken** op **voor waarde selecteren**. 

    :::image type="content" source="./media/activity-logs/select-condition-link.png" alt-text="Koppeling voor waarde selecteren":::    
1. Selecteer op de pagina **signaal logica configureren** een signaal dat door DevTest Labs wordt ondersteund. 

    :::image type="content" source="./media/activity-logs/select-signal.png" alt-text="Signaal selecteren":::
1. Filteren op **gebeurtenis niveau** (uitgebreid, informatief, waarschuwing, fout, kritiek, alle), **status** (mislukt, gestart, geslaagd) en wie de gebeurtenis heeft **gestart** . 
1. Selecteer **gereed** om het configureren van de voor waarde te volt ooien. 

    :::image type="content" source="./media/activity-logs/configure-signal-logic-done.png" alt-text="Signaal logica configureren-gereed":::
1. U hebt opgegeven voor het bereik (Lab) en de voor waarde voor de waarschuwing. U moet nu een actie groep opgeven met acties die moeten worden uitgevoerd wanneer aan de voor waarde wordt voldaan. Klik op de pagina **waarschuwings regel maken** op **actie groep selecteren**. 

    :::image type="content" source="./media/activity-logs/select-action-group-link.png" alt-text="Koppeling van de actie groep selecteren":::
1. Selecteer de koppeling **actie groep maken** op de werk balk. 

    :::image type="content" source="./media/activity-logs/create-action-group-link.png" alt-text="Koppeling actie groep maken":::
1. Voer op de pagina **actie groep toevoegen** de volgende stappen uit:
    1. Voer een **naam** in voor de actie groep.
    1. Voer een **korte naam** in voor de actie groep. 
    1. Selecteer de **resource groep** waarin u de waarschuwing wilt maken. 
    1. Voer een **naam in voor de actie**. 
    1. Selecteer het **actie type** (in dit voor beeld een **e-mail Azure Resource Manager rol**). 

        :::image type="content" source="./media/activity-logs/add-action-group.png" alt-text="Pagina actie groep toevoegen":::
    1. Selecteer op de pagina **e-mail Azure Resource Manager** op de rol. In dit voor beeld is dit de **eigenaar**. Selecteer vervolgens **OK**. 

        :::image type="content" source="./media/activity-logs/select-role.png" alt-text="Rol selecteren":::            
    1. Selecteer **OK** op de pagina **actie groep toevoegen** . 
1. Voer nu op de pagina **waarschuwings regel maken** een naam in voor de waarschuwings regel en selecteer **OK**. 

    :::image type="content" source="./media/activity-logs/create-alert-rule-done.png" alt-text="Waarschuwings regel maken-gereed":::

## <a name="view-alerts"></a>Waarschuwingen weergeven 
1. In dit voor beeld worden waarschuwingen weer geven over de **waarschuwingen** voor alle beheer bewerkingen. Waarschuwingen kunnen ergens worden weer gegeven. 

    :::image type="content" source="./media/activity-logs/alerts.png" alt-text="Scherm opname bevat waarschuwingen in het dash board.":::
1. Als u in een kolom getal selecteert (bijvoorbeeld: **Totaal aantal waarschuwingen**), ziet u de waarschuwingen die zijn gegenereerd. 

    :::image type="content" source="./media/activity-logs/all-alerts.png" alt-text="Alle waarschuwingen":::
1. Als u een waarschuwing selecteert, ziet u de details ervan. 

    :::image type="content" source="./media/activity-logs/alert-details.png" alt-text="Waarschuwings Details":::
1. In dit voor beeld ontvangt u ook een e-mail bericht met inhoud zoals in het volgende voor beeld wordt weer gegeven: 

    :::image type="content" source="./media/activity-logs/alert-email.png" alt-text="Waarschuwings-e-mail":::

## <a name="next-steps"></a>Volgende stappen
- Zie voor meer informatie over het maken van actie groepen met behulp van verschillende actie typen [actie groepen maken en beheren in de Azure Portal](../azure-monitor/alerts/action-groups.md).
- Zie  [Azure-activiteiten logboek](../azure-monitor/essentials/activity-log.md)voor meer informatie over activiteiten Logboeken.
- Zie [waarschuwingen in het activiteiten logboek](../azure-monitor/alerts/activity-log-alerts.md)voor meer informatie over het instellen van waarschuwingen voor activiteiten Logboeken.

