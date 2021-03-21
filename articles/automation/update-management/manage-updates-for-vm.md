---
title: Updates en patches voor uw virtuele machines in Azure Automation beheren
description: In dit artikel leest u hoe u Updatebeheer kunt gebruiken voor het beheren van updates en patches voor uw Azure-en niet-Azure-Vm's.
services: automation
ms.subservice: update-management
ms.topic: conceptual
ms.date: 01/27/2021
ms.openlocfilehash: c86c9049bc0afc81f5dfd8553d2aa98cfd4b1a46
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98915979"
---
# <a name="manage-updates-and-patches-for-your-vms"></a>Updates en patches voor uw virtuele machines beheren

Software-updates in Azure Automation Updatebeheer biedt een set hulpprogram ma's en resources waarmee u de complexe taak voor het bijhouden en Toep assen van software-updates op machines in Azure en hybride Cloud kunt beheren. Er is een effectief beheer proces van software-updates nodig om operationele efficiëntie te behouden, beveiligings problemen op te lossen en de Risico's van verhoogde beveiligings Risico's voor Cyber-aanvallen te verminderen. Wegens de veranderende aard van technologie en het permanent opduiken van nieuwe veiligheidsbedreigingen, vergt effectief beheer van software-updates consistente en continue aandacht.

> [!NOTE]
> Updatebeheer ondersteunt de implementatie van updates voor de eerste partij en het vooraf downloaden ervan. Deze ondersteuning vereist wijzigingen op de systemen die worden bijgewerkt. Zie [Windows Update instellingen configureren voor Azure Automation updatebeheer](configure-wuagent.md) voor meer informatie over het configureren van deze instellingen op uw systemen.

Voordat u probeert updates voor uw virtuele machines te beheren, moet u ervoor zorgen dat u Updatebeheer hebt ingeschakeld met een van de volgende methoden:

* [Updatebeheer inschakelen vanaf een Automation-account](enable-from-automation-account.md)
* [Updatebeheer in te scha kelen door te bladeren door de Azure Portal](enable-from-portal.md)
* [Updatebeheer inschakelen vanuit een runbook](enable-from-runbook.md)
* [Updatebeheer inschakelen vanaf een virtuele Azure-machine](enable-from-vm.md)

## <a name="limit-the-scope-for-the-deployment"></a><a name="scope-configuration"></a>Het bereik voor de implementatie beperken

Updatebeheer gebruikt een scope configuratie in de werk ruimte om de computers te richten op het ontvangen van updates. Zie [limiet updatebeheer-implementatie bereik](scope-configuration.md)voor meer informatie.

## <a name="compliance-assessment"></a>Nalevings beoordeling

Voordat u software-updates op uw computers implementeert, controleert u de resultaten van de update nalevings beoordeling voor ingeschakelde machines. Voor elke software-update wordt de nalevings status vastgelegd en nadat de evaluatie is voltooid, wordt deze in bulk verzameld en doorgestuurd naar Azure Monitor Logboeken.

Op een Windows-computer wordt de compatibiliteits scan standaard elke 12 uur uitgevoerd en wordt deze binnen 15 minuten van de Log Analytics-agent voor Windows opnieuw opgestart. De evaluatie gegevens worden vervolgens doorgestuurd naar de werk ruimte en de **Update** tabel vernieuwd. Vóór en na de installatie van de update wordt een update nalevings scan uitgevoerd om ontbrekende updates te identificeren, maar worden de resultaten niet gebruikt voor het bijwerken van de beoordelings gegevens in de tabel.

Het is belang rijk om onze aanbevelingen te lezen over [het configureren van de Windows Update-client](configure-wuagent.md) met updatebeheer om te voor komen dat deze problemen kunnen worden beheerd.

Voor een Linux-computer wordt standaard elk uur de compatibiliteits scan uitgevoerd. Als de Log Analytics-agent voor Linux opnieuw is gestart, wordt een nalevings scan binnen 15 minuten gestart.

De compliantie resultaten worden weer gegeven in Updatebeheer voor elke computer die wordt beoordeeld. Het kan tot 30 minuten duren voordat het dash board bijgewerkte gegevens van een nieuwe machine weergeeft die is ingeschakeld voor beheer.

Bekijk de [controle van software-updates](view-update-assessments.md) voor meer informatie over het weer geven van compliantie resultaten.

## <a name="deploy-updates"></a>Updates implementeren

Na het controleren van de nalevings resultaten is de implementatie fase software-update het proces van het implementeren van software-updates. Als u updates wilt installeren, plant u een implementatie die wordt uitgelijnd met uw release planning en service venster. U kunt kiezen welke typen updates moeten worden opgenomen in de implementatie. Zo kunt u belangrijke updates of beveiligingsupdates opnemen en updatepakketten uitsluiten.

Bekijk [software-updates implementeren](deploy-updates.md) voor meer informatie over het plannen van een update-implementatie.

## <a name="review-update-deployments"></a>Update-implementaties controleren

Nadat de implementatie is voltooid, controleert u het proces om te bepalen of de update-implementatie is geslaagd per computer of doel groep. Zie [Implementatie status controleren](deploy-updates.md#check-deployment-status) voor meer informatie over hoe u de implementatie status kunt controleren.

## <a name="next-steps"></a>Volgende stappen

* Zie [waarschuwingen maken voor updatebeheer](configure-alerts.md)voor meer informatie over het maken van waarschuwingen om u te waarschuwen over update-implementatie resultaten.

* U kunt [Azure monitor logboeken opvragen](query-logs.md) om update-evaluaties, implementaties en andere gerelateerde beheer taken te analyseren. Het bevat vooraf gedefinieerde query's waarmee u aan de slag kunt gaan.