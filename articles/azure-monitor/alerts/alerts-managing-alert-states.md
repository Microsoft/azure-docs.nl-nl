---
title: Statussen van waarschuwingen en slimme groepen beheren
description: De status van de instanties van de waarschuwing en de slimme groep beheren
ms.subservice: alerts
ms.topic: conceptual
author: anantr
ms.date: 09/24/2018
ms.openlocfilehash: fa1701c59f099f5222ab6454cc67b9dc20d1e7e7
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101701107"
---
# <a name="manage-alert-and-smart-group-states"></a>Statussen van waarschuwingen en slimme groepen beheren

Waarschuwingen in Azure Monitor hebben nu een [waarschuwings status en een monitor voorwaarde](./alerts-overview.md) , en op dezelfde manier hebben slimme groepen de status van een [slimme groep](./alerts-smartgroups-overview.md?toc=%2fazure%2fazure-monitor%2ftoc.json). Wijzigingen in de status worden nu vastgelegd in de geschiedenis die is gekoppeld aan de betreffende waarschuwing of slimme groep. Dit artikel begeleidt u bij het wijzigen van de status, zowel voor een waarschuwing als voor een slimme groep.

## <a name="change-the-state-of-an-alert"></a>De status van een waarschuwing wijzigen

1. U kunt de status van een waarschuwing op de volgende verschillende manieren wijzigen: 
    * Klik op de pagina alle waarschuwingen op het selectie vakje naast de waarschuwingen waarvan u de status wilt wijzigen en klik op status wijzigen.   
    ![Scherm afbeelding toont de pagina alle waarschuwingen waarvoor wijzigings status is geselecteerd.](./media/alerts-managing-alert-states/state-all-alerts.jpg)
    * U kunt op de pagina waarschuwings Details voor een bepaald waarschuwings exemplaar op status wijzigen klikken   
    ![Scherm afbeelding toont de pagina waarschuwings Details met de status wijzigings waarschuwing geselecteerd.](./media/alerts-managing-alert-states/state-alert-details.jpg)
    * Op de pagina waarschuwings Details voor een specifiek waarschuwings exemplaar klikt u in het deel venster slimme groep op het selectie vakje naast de gewenste waarschuwingen    
    ![Scherm afbeelding toont de pagina waarschuwings Details voor de heartbeat-waarschuwing waarbij sommige exemplaren een vinkje hebben.](./media/alerts-managing-alert-states/state-alert-details-sg.jpg)

    * Klik op de pagina Details van Smart Group, in de lijst met waarschuwingen voor leden, op het selectie vakje naast de waarschuwingen waarvan u de status wilt wijzigen en klik op wijzigen Stateto Wijzig de status van en klik op wijzigings status.   
    ![Scherm afbeelding toont de pagina Details van de Smart groep waar u waarschuwingen kunt selecteren waarvoor u de status wilt wijzigen.](./media/alerts-managing-alert-states/state-sg-details-alerts.jpg)
1. Wanneer u op status wijzigen klikt, wordt er een pop-upvenster geopend, zodat u de status kunt selecteren (nieuw/bevestigd/gesloten) en een opmerking moet invoeren, indien nodig.   
![Scherm afbeelding toont het dialoog venster waarschuwing voor wijziging van Details.](./media/alerts-managing-alert-states/state-alert-change.jpg)
1. Zodra dit is gebeurd, wordt de status wijziging vastgelegd in de geschiedenis van de respectieve waarschuwing. Dit kan worden weer gegeven door de betreffende detail pagina te openen en het gedeelte geschiedenis te controleren.    
![Scherm afbeelding toont de geschiedenis van status wijzigingen.](./media/alerts-managing-alert-states/state-alert-history.jpg)

## <a name="change-the-state-of-a-smart-group"></a>De status van een slimme groep wijzigen
1. U kunt de status van een slimme groep op de volgende manieren wijzigen:
    1. Op de pagina lijst met slimme groepen kunt u klikken op het selectie vakje naast de slimme groepen waarvan u de status wilt wijzigen en klikt u op status wijzigen  
    ![Scherm afbeelding toont de wijzigings status pagina voor slimme groepen.](./media/alerts-managing-alert-states/state-sg-list.jpg)
    1. Op de pagina Details van Smart Group kunt u klikken op status wijzigen        
    ![Scherm afbeelding toont de pagina Details van slimme groep waarvoor de status van de slimme groep wijzigen is geselecteerd.](./media/alerts-managing-alert-states/state-sg-details.jpg)
1. Wanneer u op status wijzigen klikt, wordt er een pop-upvenster geopend, zodat u de status kunt selecteren (nieuw/bevestigd/gesloten) en een opmerking moet invoeren, indien nodig. 
![Scherm afbeelding toont het dialoog venster wijzigings status voor de slimme groep.](./media/alerts-managing-alert-states/state-sg-change.jpg)
   > [!NOTE]
   >  Als u de status van een Smart groep wijzigt, wordt de status van de waarschuwingen voor afzonderlijke leden niet gewijzigd.

1. Zodra dit is gebeurd, wordt de status wijziging vastgelegd in de geschiedenis van de respectieve slimme groep. Dit kan worden weer gegeven door de betreffende detail pagina te openen en het gedeelte geschiedenis te controleren.     
![Scherm afbeelding toont de geschiedenis van de wijzigingen voor de slimme groep.](./media/alerts-managing-alert-states/state-sg-history.jpg)