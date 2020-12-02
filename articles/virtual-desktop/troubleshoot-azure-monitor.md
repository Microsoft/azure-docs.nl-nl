---
title: Problemen met het bewaken van Windows virtueel bureau blad preview oplossen-Azure
description: Problemen oplossen met Azure Monitor voor virtueel bureau blad van Windows.
author: Heidilohr
ms.topic: troubleshooting
ms.date: 12/01/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 91cf6729911cdb674c5451f172e76a2e9d5943e4
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/01/2020
ms.locfileid: "96466787"
---
# <a name="troubleshoot-azure-monitor-for-windows-virtual-desktop-preview"></a>Problemen met Azure Monitor voor virtueel bureau blad van Windows oplossen (preview-versie)

>[!IMPORTANT]
>Azure Monitor voor het virtuele bureau blad van Windows is momenteel beschikbaar als open bare preview. Deze preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt. Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

In dit artikel vindt u bekende problemen en oplossingen voor veelvoorkomende problemen in Azure Monitor voor virtueel bureau blad van Windows (preview).

## <a name="the-configuration-workbook-isnt-working-properly"></a>De configuratie werkmap werkt niet correct

Als de Azure Monitor-configuratie werkmap niet werkt, kunt u deze resources gebruiken om de onderdelen hand matig in te stellen:

- Als u Diagnostische gegevens hand matig wilt inschakelen of toegang wilt krijgen tot de Log Analytics-werk ruimte, raadpleegt [u Windows diagnostische gegevens over virtueel bureau blad naar log Analytics](diagnostics-log-analytics.md)
- Zie [log Analytics virtuele-machine-extensie voor Windows](../virtual-machines/extensions/oms-windows.md)als u de log Analytics-extensie hand matig wilt installeren op een host.
- Zie [een log Analytics-werk ruimte maken in de Azure Portal](../azure-monitor/learn/quick-create-workspace.md)om een nieuwe log Analytics-werk ruimte in te stellen.
- Zie [Configuring Performance Counters](../azure-monitor/platform/data-sources-performance-counters.md)om prestatie meter items toe te voegen of te verwijderen.
- Zie [gegevens bronnen van Windows-gebeurtenis logboeken verzamelen met log Analytics agent](../azure-monitor/platform/data-sources-windows-events.md)voor het configureren van gebeurtenissen voor een log Analytics-werk ruimte.

Het probleem kan ook worden veroorzaakt door een tekort aan resources of niet met de vereiste machtigingen.

Als het abonnement geen virtuele Windows-bureau blad-resources heeft, wordt deze niet weer gegeven in de para meter van het *abonnement* .

Als u geen lees toegang hebt tot de juiste abonnementen, worden deze niet weer gegeven in de para meter *abonnement* en worden de gegevens in het dash board niet zichtbaar. Als u dit probleem wilt oplossen, neemt u contact op met de eigenaar van uw abonnement en vraagt u om Lees toegang.

## <a name="my-data-isnt-displaying-properly"></a>Mijn gegevens worden niet correct weer gegeven

Als uw gegevens niet goed worden weer gegeven, is er mogelijk een probleem opgetreden tijdens het Azure Monitor configuratie proces. Zorg er eerst voor dat u alle velden in de configuratie werkmap hebt ingevuld, zoals beschreven in [Azure monitor voor Windows Virtual Desktop gebruiken om uw implementatie te controleren](azure-monitor.md). U kunt de instellingen voor zowel nieuwe als bestaande omgevingen op elk gewenst moment wijzigen. Als er items of gebeurtenissen ontbreken, worden de bijbehorende gegevens niet weer gegeven in de Azure Portal.

Als u geen gegevens mist, maar uw gegevens nog steeds niet goed worden weer gegeven, is er mogelijk een probleem in de query of de gegevens bronnen. 

Als er geen Setup-fouten worden weer gegeven en u de verwachte gegevens nog steeds niet ziet, kunt u het beste 15 minuten wachten en de feed vernieuwen. Azure Monitor heeft een latentie periode van 15 minuten voor het invullen van de logboek gegevens. Zie [gegevens opname tijd vastleggen in azure monitor](../azure-monitor/platform/data-ingestion-time.md)voor meer informatie.

Ten slotte, als u geen gegevens mist, maar uw gegevens nog steeds niet worden weer gegeven, is er mogelijk een probleem in de query of de gegevens bronnen. Mogelijk moet u contact opnemen met de ondersteuning om het probleem op te lossen, als dat het geval is.

## <a name="i-want-to-customize-azure-monitor-for-windows-virtual-desktop"></a>Ik wil Azure Monitor aanpassen voor het virtuele bureau blad van Windows

Azure Monitor voor virtueel bureau blad van Windows maakt gebruik van Azure Monitor-werkmappen. Met werkmappen kunt u een kopie van de sjabloon virtuele Windows-bureau blad-werkmap opslaan en uw eigen aanpassingen door voeren.

Aangepaste sjablonen worden niet bijgewerkt wanneer de oorspronkelijke sjabloon wordt bijgewerkt door de product groep. Dit is in het hulp programma werkmappen ontworpen, u moet een kopie van de bijgewerkte sjabloon opslaan en uw aanpassingen opnieuw bouwen om updates te kunnen aanbrengen. Zie voor meer informatie [problemen oplossen op basis van een werkmap](../azure-monitor/insights/troubleshoot-workbooks.md) en het [overzicht van werkmappen](../azure-monitor/platform/workbooks-overview.md).

## <a name="i-cant-interpret-the-data"></a>Ik kan de gegevens niet interpreteren

Meer informatie over de gegevens termen vindt u in de [woorden lijst Azure monitor voor het virtuele bureau blad van Windows](azure-monitor-glossary.md).

## <a name="the-data-i-need-isnt-available"></a>De benodigde gegevens zijn niet beschikbaar

Kunt u geen gegevens punt vinden om te helpen bij het vaststellen van een probleem? Stuur ons feedback.

- Zie [probleemoplossings overzicht, feedback en ondersteuning voor virtueel bureau blad van Windows](troubleshoot-set-up-overview.md)voor meer informatie over het geven van feedback.
- U kunt ook feedback geven voor Windows virtueel bureau blad op de [Windows Virtual Desktop feedback hub](https://support.microsoft.com/help/4021566/windows-10-send-feedback-to-microsoft-with-feedback-hub-app) of [ons UserVoice-forum](https://windowsvirtualdesktop.uservoice.com/forums/921118-general).

## <a name="known-issues"></a>Bekende problemen

Dit zijn de problemen waarvan we momenteel op de hoogte zijn en die kunnen worden gebruikt om het probleem op te lossen:

- U kunt momenteel slechts één abonnement, resource groep en hostgroep selecteren om tegelijk te bewaken. Daarom moet u, wanneer u de pagina gebruikers rapporten gebruikt om inzicht te krijgen in de gebruikers ervaring, controleren of u de juiste hostgroep hebt die door de gebruiker is gebruikt of dat de gegevens niet in de visuals worden ingevuld.

- Het is momenteel niet mogelijk om favoriete instellingen op te slaan in Azure Monitor tenzij u een aangepaste sjabloon van de werkmap opslaat. Dit betekent dat de IT-beheerder de naam, de namen van de resource groepen en de voor keuren voor de hostgroep moet invoeren telkens wanneer ze Azure Monitor voor Windows virtueel bureau blad openen.

- Er is momenteel geen manier om gegevens van Azure Monitor voor virtuele Windows-Bureau bladen te exporteren naar Excel.

- Alle waarschuwingen voor Ernst 1 Azure Monitor voor alle producten binnen het geselecteerde abonnement worden weer gegeven op de pagina overzicht. Dit is een ontwerp, omdat waarschuwingen van andere producten in het abonnement van invloed kunnen zijn op het virtuele bureau blad van Windows. Op dit moment is de query beperkt tot waarschuwingen van Ernst 1, met uitzonde ring van Ernst 0-waarschuwingen met hoge prioriteit van de pagina overzicht.

- Sommige fout berichten worden niet op een gebruiks vriendelijke manier aangeduid en niet alle fout berichten worden beschreven in de documentatie.

## <a name="next-steps"></a>Volgende stappen

Als u niet zeker weet hoe u de gegevens kunt interpreteren of meer wilt weten over algemene termen, raadpleegt u de [Azure monitor voor de Windows-woorden lijst voor virtueel bureau blad](azure-monitor-glossary.md). Zie [Azure monitor voor Windows virtueel bureau blad gebruiken voor het controleren van uw implementatie](azure-monitor.md)voor meer informatie over het instellen en gebruiken van Azure monitor voor virtueel bureau blad van Windows.