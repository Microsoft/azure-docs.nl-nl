---
title: Analyse in Azure Monitor voor VM's wijzigen
description: Azure Monitor voor VM's integratie met de integratie van het wijzigen van de toepassings wijzigingen, kunt u alle wijzigingen bekijken die zijn aangebracht op een virtuele machine waarop de IT-prestaties van invloed kunnen zijn.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 09/23/2020
ms.openlocfilehash: 59799a09436d5968a441f6f17655d3138a2d84d8
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100611250"
---
# <a name="change-analysis-in-azure-monitor-for-vms"></a>Analyse in Azure Monitor voor VM's wijzigen
Azure Monitor voor VM's integratie met de integratie van het wijzigen van de [toepassings](../app/change-analysis.md) wijzigingen, kunt u alle wijzigingen bekijken die zijn aangebracht op een virtuele machine waarop de IT-prestaties van invloed kunnen zijn.

## <a name="overview"></a>Overzicht
Stel dat u een VM hebt die langzaam wordt uitgevoerd en u wilt onderzoeken of recente wijzigingen in de configuratie van invloed kunnen zijn op de prestaties. U kunt de prestaties van de virtuele machine bekijken met Azure Monitor voor VM's en er wordt gebruikgemaakt van een toename van geheugen gebruik in het afgelopen uur. Met wijzigings analyse kunt u bepalen of de configuratie wijzigingen die zijn aangebracht rond deze tijd, de oorzaak van deze toename zijn.

Met de service voor het wijzigen van de toepassings wijziging worden wijzigingen van de [Azure-resource grafiek](../../governance/resource-graph/how-to/get-resource-changes.md) en geneste eigenschappen, zoals netwerk beveiligings regels van Azure Resource Manager, geaggregeerd. 

## <a name="enabling-change-analysis"></a>Wijzigings analyse inschakelen
U moet de resource provider *micro soft. ChangeAnalysis* registreren voor het onboarden van wijzigings analyse in azure monitor voor VM's. De eerste keer dat u Azure Monitor voor VM's of de analyse van toepassings wijzigingen in de Azure Portal start, wordt deze resource provider automatisch voor u geregistreerd. Analyse van toepassings wijzigingen is een gratis service zonder prestatie overhead op resources.

## <a name="view-change-analysis"></a>Wijzigings analyse weer geven
Wijzigingen analyse is beschikbaar op het tabblad **prestaties** of **toewijzing** van Azure monitor voor VM's door de **wijzigings** optie te selecteren. 

[![Wijzigingen onderzoeken](media/vminsights-change-analysis/investigate-changes-screenshot.png)](media/vminsights-change-analysis/investigate-changes-screenshot-zoom.png#lightbox)


Klik op de knop **wijzigingen onderzoeken** om de analyse pagina voor het wijzigen van de toepassing te starten die is gefilterd voor de virtuele machine. U kunt de weer gegeven wijzigingen bekijken om te zien of er problemen zijn die het probleem kunnen hebben veroorzaakt. Als u twijfelt over een bepaalde wijziging, kunt u verwijzen naar de kolom **wijzigen per** om te bepalen welke persoon de wijziging heeft aangebracht.

[![Details wijzigen](media/vminsights-change-analysis/change-details-screenshot.png)](media/vminsights-change-analysis/change-details-screenshot.png#lightbox)

## <a name="next-steps"></a>Volgende stappen
- Meer informatie over het [analyseren van toepassings wijzigingen](../app/change-analysis.md).
- Meer informatie over [Azure resource Graph](../../governance/resource-graph/how-to/get-resource-changes.md). 

