---
title: Activiteiten Logboeken in Azure DevTest Labs | Microsoft Docs
description: In dit artikel worden de stappen beschreven voor het weer geven van activiteiten logboeken voor Azure DevTest Labs.
ms.topic: how-to
ms.date: 07/10/2020
ms.openlocfilehash: 51bdfc6c3857a3e59d75094b4c847c80c58de045
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100582779"
---
# <a name="view-activity-logs-for-labs-in-azure-devtest-labs"></a>Activiteiten logboeken voor Labs in Azure DevTest Labs weer geven 
Nadat u een of meer Labs hebt gemaakt, wilt u waarschijnlijk controleren hoe en wanneer uw lessen worden geopend, gewijzigd en beheerd, en door wie. Azure DevTest Labs maakt gebruik van Azure Monitor, met name **activiteiten logboeken**, om informatie te geven over deze bewerkingen op Labs. 

In dit artikel wordt uitgelegd hoe u activiteiten logboeken voor een lab in Azure DevTest Labs kunt weer geven.

## <a name="view-activity-log-for-a-lab"></a>Activiteiten logboek voor een Lab weer geven

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
1. Selecteer **alle services** en selecteer vervolgens **DevTest Labs** in de sectie **DEVOPS** . Als u * (Star) selecteert naast **DevTest Labs** in de sectie **DEVOPS** . Met deze actie worden **DevTest Labs** toegevoegd aan het navigatie menu aan de linkerkant, zodat u de volgende keer snel toegang kunt krijgen. Vervolgens kunt u **DevTest Labs** selecteren in het navigatie menu aan de linkerkant.

    ![Alle services: Selecteer DevTest Labs](./media/devtest-lab-create-lab/all-services-select.png)
1. Selecteer in de lijst met Labs uw Lab.
1. Selecteer op de start pagina van het lab **configuraties en beleid** in het menu links. 

    :::image type="content" source="./media/activity-logs/configuration-policies-link.png" alt-text="Configuratie en beleid selecteren in het menu links":::
1. Selecteer op de pagina **configuratie en beleid** de optie **activiteiten logboek** in het menu links onder **beheren**. U ziet vermeldingen voor bewerkingen die zijn uitgevoerd op het lab. 

    :::image type="content" source="./media/activity-logs/activity-log.png" alt-text="Activiteitenlogboek":::    
1. Selecteer een gebeurtenis om de details ervan weer te geven. Op de pagina **samen vatting** ziet u informatie zoals de naam van de bewerking, de tijds tempel en wie de bewerking heeft uitgevoerd. 
    
    :::image type="content" source="./media/activity-logs/stop-vm-event.png" alt-text="VM-gebeurtenis stoppen-samen vatting":::        
1. Ga naar het tabblad **JSON** om meer details te bekijken. In het volgende voor beeld ziet u de naam van de virtuele machine en de bewerking die is uitgevoerd op de VM (gestopt).

    :::image type="content" source="./media/activity-logs/stop-vm-event-json.png" alt-text="VM-gebeurtenis stoppen-JSON":::           
1. Ga naar het tabblad **wijzigings overzicht (preview)** voor een overzicht van de wijzigingen. In het volgende voor beeld ziet u de wijziging die op de virtuele machine is aangebracht. 

    :::image type="content" source="./media/activity-logs/change-history.png" alt-text="Gebeurtenis-wijzigings geschiedenis van VM stoppen":::             
1. Selecteer de wijziging in de lijst wijzigings overzicht om meer informatie over de wijziging weer te geven. 

    :::image type="content" source="./media/activity-logs/change-details.png" alt-text="VM-gebeurtenis stoppen-details wijzigen":::             

Zie [Azure-activiteiten logboek](../azure-monitor/essentials/activity-log.md)voor meer informatie over activiteiten Logboeken.

## <a name="next-steps"></a>Volgende stappen

- Zie [waarschuwingen maken](create-alerts.md)voor meer informatie over het instellen van **waarschuwingen** voor activiteiten Logboeken.
- Zie  [Azure-activiteiten logboek](../azure-monitor/essentials/activity-log.md)voor meer informatie over activiteiten Logboeken.

