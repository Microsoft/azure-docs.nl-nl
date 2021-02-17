---
title: Verbeter de operationele Excellency met Advisor
description: Gebruik Azure Advisor om uw operationele uitmuntendheid voor uw Azure-abonnementen te optimaliseren en verouderd.
ms.topic: article
ms.date: 10/24/2019
ms.openlocfilehash: 0b938a0c7a42182bb8d2a50b48d65a0844d952a6
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100579953"
---
# <a name="achieve-operational-excellence-by-using-azure-advisor"></a>Behaal de operationele uitmuntendheid met behulp van Azure Advisor

Met de aanbevelingen voor operationele uitmuntendheid in Azure Advisor kunt u het volgende helpen: 
- Efficiëntie van processen en werk stromen.
- Beheer baarheid van resources.
- Aanbevolen procedures voor de implementatie. 

U kunt deze aanbevelingen vinden op het tabblad **operationele uitmuntendheid** van het Advisor-dash board.

## <a name="create-azure-service-health-alerts-to-be-notified-when-azure-problems-affect-you"></a>Azure Service Health waarschuwingen maken om een melding te ontvangen wanneer Azure-problemen van invloed zijn op u

U wordt aangeraden Azure Service Health waarschuwingen in te stellen, zodat u wordt gewaarschuwd wanneer er problemen met de Azure-service zijn. [Azure service Health](https://azure.microsoft.com/features/service-health/) is een gratis service die persoonlijke begeleiding en ondersteuning biedt wanneer u het probleem van een Azure-service ondervindt. Advisor identificeert abonnementen waarvoor geen waarschuwingen zijn geconfigureerd en raadt u aan om deze te configureren.


## <a name="design-your-storage-accounts-to-prevent-reaching-the-maximum-subscription-limit"></a>Uw opslag accounts ontwerpen om te voor komen dat de maximale abonnements limiet wordt bereikt

Een Azure-regio kan Maxi maal 250 opslag accounts per abonnement ondersteunen. Nadat u deze limiet hebt bereikt, kunt u geen opslag accounts maken in deze combi natie van regio en abonnement. Advisor controleert uw abonnementen en biedt aanbevelingen waarmee u kunt ontwerpen voor minder opslag accounts voor elke regio/elk abonnement dat bijna de limiet bereikt.

## <a name="ensure-you-have-access-to-azure-cloud-experts-when-you-need-it"></a>Zorg ervoor dat u toegang hebt tot Azure-Cloud experts wanneer u deze nodig hebt

Wanneer u een kritieke werk belasting uitvoert, is het belang rijk dat u toegang hebt tot technische ondersteuning wanneer u deze nodig hebt. Advisor identificeert potentiële bedrijfs kritieke abonnementen waarvoor geen technische ondersteuning is opgenomen in hun ondersteunings plan. Het wordt aanbevolen een upgrade uit te brengen naar een optie met technische ondersteuning.

## <a name="delete-and-re-create-your-pool-to-remove-a-deprecated-internal-component"></a>De pool verwijderen en opnieuw maken om een afgeschaft intern onderdeel te verwijderen

Als uw pool gebruikmaakt van een afgeschaft intern onderdeel, verwijdert u de groep en maakt u deze opnieuw voor verbeterde stabiliteit en prestaties.

## <a name="repair-invalid-log-alert-rules"></a>Ongeldige regels voor logboek waarschuwingen herstellen

Azure Advisor detecteert waarschuwings regels waarvoor ongeldige query's zijn opgegeven in de sectie voor waarde. U kunt waarschuwings regels voor logboeken maken in Azure Monitor en ze gebruiken om analyse query's uit te voeren op basis van opgegeven intervallen. De resultaten van de query bepalen of er een waarschuwing moet worden gegeven. Analytische query's kunnen na verloop van tijd ongeldig worden vanwege wijzigingen in resources, tabellen of opdrachten waarnaar wordt verwezen. Advisor raadt u aan de query in de waarschuwings regel te corrigeren om te voor komen dat deze automatisch wordt uitgeschakeld en de bewakings dekking van uw resources in azure te controleren. [Meer informatie over het oplossen van waarschuwings regels.](../azure-monitor/alerts/alerts-troubleshoot-log.md)

## <a name="use-azure-policy-recommendations"></a>Azure Policy aanbevelingen gebruiken

Azure Policy is een service in azure die u kunt gebruiken om beleid te maken, toe te wijzen en te beheren. Met deze beleids regels worden regels en effecten voor uw resources afgedwongen. De volgende Azure Policy aanbevelingen kunnen u helpen bij het bezorgen van operationele Excellency: 

**Tags beheren.** Met dit beleid kunt u de opgegeven tag en waarde toevoegen of vervangen wanneer een resource wordt gemaakt of bijgewerkt. U kunt bestaande resources herstellen door een herstel taak te activeren. Dit beleid wijzigt geen tags voor resource groepen.

**Vereisten voor geo-naleving afdwingen.** Met dit beleid kunt u de locaties beperken die uw organisatie kan opgeven tijdens het implementeren van resources. 

**Geef toegestane Sku's voor virtuele machines op voor implementaties.** Met dit beleid kunt u een set SKU's voor virtuele machines opgeven die uw organisatie mag implementeren.

**Controle-Vm's afdwingen *die geen beheerde schijven gebruiken*.**

**Schakel *een tag overnemen van resource groepen* in.** Met dit beleid kunt u de opgegeven tag en waarde van de bovenliggende resourcegroep toevoegen of vervangen wanneer een resource wordt gemaakt of bijgewerkt. U kunt bestaande resources herstellen door een herstel taak te activeren.

Advisor raadt een aantal afzonderlijke Azure-beleids regels aan die klanten helpen bij het bezorgen van operationele uitmuntendheid door aanbevolen procedures vast te stellen. Als een klant besluit een aanbevolen beleid toe te wijzen, zullen we de aanbeveling onderdrukken. Als de klant besluit het beleid later te verwijderen, zal Advisor de aanbeveling blijven onderdrukken omdat de verwijdering wordt geïnterpreteerd als een sterk signaal van het volgende:

1.  De klant heeft het beleid verwijderd omdat dit ondanks de aanbeveling van Advisor niet van toepassing is op de specifieke use-case. 
2.  De klant is op de hoogte en vertrouwd met het beleid na het toewijzen en verwijderen, en kan het indien nodig opnieuw toewijzen of verwijderen zonder richt lijnen als dat later relevant is voor het gebruik. Als de klant deze in hun beste belang heeft gevonden om hetzelfde beleid opnieuw toe te wijzen, kunnen ze dit doen in Azure Policy zonder dat hiervoor een aanbeveling vereist is in Advisor. Houd er rekening mee dat deze logica specifiek van toepassing is op de beleids aanbeveling in de categorie operationele uitmuntendheid. Deze regels zijn niet van toepassing op beveiligings aanbevelingen.  


## <a name="no-validation-environment-enabled"></a>Geen validatieomgeving ingeschakeld
Azure Advisor bepaalt dat er geen validatie omgeving is ingeschakeld in het huidige abonnement. Wanneer u uw hostgroepen maakt, hebt u \" Nee \" voor \" validatie omgeving geselecteerd \" op het tabblad Eigenschappen. Als er ten minste één hostgroep is waarvoor een validatie omgeving is ingeschakeld, zorgt u ervoor dat de bedrijfs continuïteit via Windows Virtual Desktop service-implementaties met vroegtijdige detectie van potentiële problemen. [Meer informatie](../virtual-desktop/create-validation-host-pool.md)

## <a name="ensure-production-non-validation-environment-to-benefit-from-stable-functionality"></a>Zorgen dat de productieomgeving (niet-validatie) profiteert van stabiele functionaliteit
Azure Advisor detecteert dat er voor een of meer hostgroepen een validatie omgeving is ingeschakeld. Om validatie omgevingen het beste te kunnen gebruiken, moet u ten minste één, maar nooit meer dan de helft van uw hostgroepen in de validatie omgeving hebben. Als u een goede balans hebt tussen uw hostgroepen waarvoor de validatie omgeving is ingeschakeld en de groepen waarvoor deze functie is uitgeschakeld, kunt u het beste profiteren van de voor delen van de implementaties met meerdere fases die door Windows Virtual Desktop worden aangeboden met bepaalde updates. Om dit probleem op te lossen, opent u de eigenschappen van uw hostgroep en selecteert \" \" u niet naast de \" instelling validatie omgeving \" .

## <a name="enable-traffic-analytics-to-view-insights-into-traffic-patterns-across-azure-resources"></a>Traffic Analytics inzicht geven in verkeerspatronen in Azure-resources
Traffic Analytics, een cloudoplossing die inzicht biedt in gebruikers- en toepassingsactiviteit in Azure. Traffic Analytics analyseert stroomlogboeken van de Network Watcher-netwerkbeveiligingsgroep (NSG) om inzicht te krijgen in de verkeersstroom. Dankzij verkeersanalyse kunt u belangrijke sprekers zien in Azure- en niet-Azure-implementaties, openstaande poorten, protocollen en kwaadaardige stromen in uw omgeving onderzoeken en de prestaties van uw netwerkimplementatie optimaliseren. U kunt stroomlogboeken verwerken met een interval van tien en zestig minuten, zodat u snellere analyses hebt van uw verkeer. Het is een goed idee om Traffic Analytics in te scha kelen voor uw Azure-resources. 


## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie over Advisor-aanbevelingen:
* [Inleiding tot Advisor](advisor-overview.md)
* [Aan de slag](advisor-get-started.md)
* [Aanbevelingen van Advisor met betrekking tot kosten](advisor-cost-recommendations.md)
* [Aanbevelingen voor Advisor-prestaties](advisor-performance-recommendations.md)
* [Aanbevelingen voor de Advisor-betrouw baarheid](advisor-high-availability-recommendations.md)
* [Aanbevelingen voor de beveiliging van Advisor](advisor-security-recommendations.md)
* [Advisor REST API](/rest/api/advisor/)
