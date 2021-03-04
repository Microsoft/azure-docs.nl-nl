---
title: Analyse in VM-inzichten wijzigen
description: Met de integratie van de VM-Insights met de integratie van toepassings wijzigingen kunt u wijzigingen in een virtuele machine bekijken die mogelijk invloed hebben op de prestaties.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 09/23/2020
ms.openlocfilehash: 375812813d704eb9b48d0ed8fbbc65dd5e47da49
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102046768"
---
# <a name="change-analysis-in-vm-insights"></a>Analyse in VM-inzichten wijzigen
Met de integratie van de VM-Insights met de integratie van [toepassings wijzigingen](../app/change-analysis.md) kunt u wijzigingen in een virtuele machine bekijken die mogelijk invloed hebben op de prestaties.

## <a name="overview"></a>Overzicht
Stel dat u een VM hebt die langzaam wordt uitgevoerd en u wilt onderzoeken of recente wijzigingen in de configuratie van invloed kunnen zijn op de prestaties. U kunt de prestaties van de virtuele machine weer geven met behulp van VM Insights en er wordt gebruikgemaakt van een toename van het geheugen gebruik in het afgelopen uur. Met wijzigings analyse kunt u bepalen of de configuratie wijzigingen die zijn aangebracht rond deze tijd, de oorzaak van deze toename zijn.

Met de service voor het wijzigen van de toepassings wijziging worden wijzigingen van de [Azure-resource grafiek](../../governance/resource-graph/how-to/get-resource-changes.md) en geneste eigenschappen, zoals netwerk beveiligings regels van Azure Resource Manager, geaggregeerd. 

## <a name="enabling-change-analysis"></a>Wijzigings analyse inschakelen
U moet de resource provider *micro soft. ChangeAnalysis* registreren voor het onboarden van wijzigings analyse in VM Insights. De eerste keer dat u de VM Insights-of toepassings wijzigings analyse in de Azure Portal start, wordt deze resource provider automatisch voor u geregistreerd. Analyse van toepassings wijzigingen is een gratis service zonder prestatie overhead op resources.

## <a name="view-change-analysis"></a>Wijzigings analyse weer geven
Wijzigings analyse is beschikbaar op het tabblad **prestaties** of **toewijzing** van VM Insights door de **wijzigings** optie te selecteren. 

[![Wijzigingen onderzoeken](media/vminsights-change-analysis/investigate-changes-screenshot.png)](media/vminsights-change-analysis/investigate-changes-screenshot-zoom.png#lightbox)


Klik op de knop **wijzigingen onderzoeken** om de analyse pagina voor het wijzigen van de toepassing te starten die is gefilterd voor de virtuele machine. U kunt de weer gegeven wijzigingen bekijken om te zien of er problemen zijn die het probleem kunnen hebben veroorzaakt. Als u twijfelt over een bepaalde wijziging, kunt u verwijzen naar de kolom **wijzigen per** om te bepalen welke persoon de wijziging heeft aangebracht.

[![Details wijzigen](media/vminsights-change-analysis/change-details-screenshot.png)](media/vminsights-change-analysis/change-details-screenshot.png#lightbox)

## <a name="next-steps"></a>Volgende stappen
- Meer informatie over het [analyseren van toepassings wijzigingen](../app/change-analysis.md).
- Meer informatie over [Azure resource Graph](../../governance/resource-graph/how-to/get-resource-changes.md). 

