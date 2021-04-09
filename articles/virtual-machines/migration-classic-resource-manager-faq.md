---
title: Veelgestelde vragen over de migratie van klassiek naar Azure Resource Manager
description: Veelgestelde vragen over de migratie van klassiek naar Azure Resource Manager.
author: tanmaygore
manager: vashan
ms.service: virtual-machines
ms.subservice: classic-to-arm-migration
ms.workload: infrastructure-services
ms.topic: conceptual
ms.date: 02/06/2020
ms.author: tagore
ms.openlocfilehash: 034f09a8b616450e472dcc11427191f187cf489f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "102615156"
---
# <a name="frequently-asked-questions-about-classic-to-azure-resource-manager-migration"></a>Veelgestelde vragen over de migratie van klassiek naar Azure Resource Manager

> [!IMPORTANT]
> Nu gebruiken we op ongeveer 90% IaaS Vm's [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/). Vanaf 28 februari 2020 zijn klassieke Vm's afgeschaft en worden ze volledig buiten gebruik gesteld op 1 maart 2023. Meer [informatie]( https://aka.ms/classicvmretirement) over deze afschaffing en [hoe dit van invloed is op u](./classic-vm-deprecation.md#how-does-this-affect-me).

## <a name="what-is-azure-service-manager-and-what-does-it-mean-by-classic"></a>Wat is Azure Service Manager en wat betekent het klassiek?

Het woord ' klassiek ' in IaaS VM (klassiek) verwijst naar Vm's die worden beheerd door Azure Service Manager (ASM). Azure Service Manager (ASM) is het oude controle vlak van Azure dat verantwoordelijk is voor het maken, beheren, verwijderen van Vm's en het uitvoeren van andere beheer bewerkingen. 

## <a name="what-is-azure-resource-manager"></a>Wat is Azure Resource Manager?

[Azure Resource Manager](../azure-resource-manager/management/overview.md) is het laatste controle vlak van Azure dat verantwoordelijk is voor het maken, beheren, verwijderen van vm's en het uitvoeren van andere beheer bewerkingen. 

## <a name="what-is-the-time-required-for-migration"></a>Wat is de tijd die nodig is voor de migratie?

Het plannen en uitvoeren van de migratie is aanzienlijk afhankelijk van de complexiteit van de architectuur en kan enkele maanden duren.  

## <a name="what-is-the-definition-of-a-new-customer-on-iaas-vms-classic"></a>Wat is de definitie van een nieuwe klant op IaaS Vm's (klassiek)?

Klanten die niet over IaaS-Vm's (klassiek) beschikken in hun abonnementen in de maand februari 2020 (een maand voordat de afschaffing werd gestart), worden beschouwd als nieuwe klanten. 

## <a name="what-is-the-definition-of-an-existing-customer-on-iaas-virtual-machines-classic"></a>Wat is de definitie van een bestaande klant op IaaS-Virtual Machines (klassiek)?

Klanten die actief of gestopt zijn, maar toegewezen IaaS Vm's (klassiek) in hun abonnementen in de maand februari 2020, worden beschouwd als een bestaande klant. Alleen deze klanten krijgen tot 1 maart 2023 om hun Vm's vanuit Azure Service Manager te migreren naar Azure Resource Manager. 

## <a name="why-am-i-getting-an-error-stating-newclassicvmcreationnotallowedforsubscription"></a>Waarom krijg ik een fout melding met de mede deling "NewClassicVMCreationNotAllowedForSubscription"?

Als onderdeel van het pensioen proces zijn IaaS VM (klassiek) niet langer beschikbaar voor nieuwe klanten. We hebben u geïdentificeerd als nieuwe klanten en daarom is uw bewerking niet geautoriseerd. Het is raadzaam om Azure Resource Manager te gebruiken. Als u geen Azure-Vm's kunt gebruiken met Azure Resource Manager, neemt u contact op met de ondersteuning om uw abonnement toe te voegen aan de acceptatie lijst.

## <a name="does-this-migration-plan-affect-any-of-my-existing-services-or-applications-that-run-on-azure-virtual-machines"></a>Is dit migratieplan van invloed op mijn bestaande services en toepassingen die worden uitgevoerd op virtuele Azure-machines? 

Tot 1 maart 2023 voor IaaS Vm's (klassiek). De IaaS-Vm's (klassiek) zijn volledig ondersteunde services in algemene Beschik baarheid. U kunt deze resources blijven gebruiken om uw footprint in Microsoft Azure te vergroten. Op 1 maart 2023 worden deze Vm's volledig buiten gebruik gesteld en worden actieve of toegewezen Vm's gestopt & de toewijzing ongedaan wordt gemaakt.

Er is geen invloed op andere klassieke resources, zoals Cloud Services (klassiek), opslag accounts (klassiek), enzovoort.

## <a name="what-happens-to-my-vms-if-i-dont-plan-on-migrating-in-the-near-future"></a>Wat gebeurt er met mijn virtuele machines als ik niet van plan ben om in de nabije toekomst te migreren? 

Op 1 maart 2023 worden de IaaS Vm's (klassiek) volledig buiten gebruik gesteld en worden actieve of toegewezen Vm's gestopt & de toewijzing ongedaan wordt gemaakt. Om bedrijfs impact te voor komen, kunnen we het beste een opmerking sturen om vandaag nog te beginnen met het plannen van uw migratie en deze volt ooien vóór 1 maart 2023. De bestaande klassieke Api's, het Cloud Services en het resource model worden niet terugvallen. Het is de bedoeling om migreren eenvoudig te maken omdat er veel geavanceerde functies beschikbaar zijn in het Resource Manager-implementatiemodel. We raden u aan om te beginnen met de migratie van deze resources naar Azure Resource Manager. 

## <a name="what-does-this-migration-plan-mean-for-my-existing-tooling"></a>Wat betekent dit migratieplan voor mijn bestaande tooling? 

Het bijwerken van uw tooling voor het Resource Manager-implementatiemodel is een van de belangrijkste veranderingen die moet worden doorgevoerd voor uw migratieplannen.

## <a name="how-long-will-the-management-plane-downtime-be"></a>Hoe lang duurt de downtime voor het management? 

Dit is afhankelijk van het aantal resources dat wordt gemigreerd. Bij kleinere implementaties (enkele tientallen virtuele machines) zou de volledige migratie minder dan een uur moeten duren. Bij grootschalige implementaties (honderden virtuele machines) kan de migratie enkele uren duren.

## <a name="can-i-roll-back-after-my-migrating-resources-are-committed-in-resource-manager"></a>Kan ik het migreren van mijn resources ongedaan maken nadat de resources zijn doorgevoerd in Resource Manager? 

U kunt de migratie afbreken wanneer de resources zich nog in de staat Voorbereid bevinden. U kunt het migreren niet meer ongedaan maken wanneer de resources eenmaal zijn doorgevoerd.

## <a name="can-i-roll-back-my-migration-if-the-commit-operation-fails"></a>Kan ik de migratie ongedaan maken wanneer de doorvoerbewerking is mislukt? 

U kunt de migratie niet afbreken als wanneer de doorvoerbewerking is mislukt. Alle migratiebewerkingen, met inbegrip van de doorvoerbewerking, zijn idempotent. Daarom is het raadzaam om de bewerking na een korte tijd opnieuw uit te voeren. Als u nog steeds een fout hebt, maakt u een ondersteunings ticket.

## <a name="do-i-have-to-buy-another-express-route-circuit-if-i-have-to-use-iaas-under-resource-manager"></a>Moet ik nog een ExpressRoute-circuit kopen als ik IaaS via Resource Manager wil gebruiken? 

Nee. Recent is het [verplaatsen van ExpressRoute-circuits van het klassieke naar het Resource Manager-implementatiemodel](../expressroute/expressroute-move.md) ingeschakeld. U hoeft geen nieuw ExpressRoute-circuit te kopen als u er al een hebt.

## <a name="what-if-i-had-configured-azure-role-based-access-control-policies-for-my-classic-iaas-resources"></a>Wat gebeurt er als ik op rollen gebaseerd toegangs beheer beleid voor mijn klassieke IaaS-resources heb geconfigureerd? 

Tijdens de migratie worden de klassieke resources Resource Manager-resources. Daarom wordt u aangeraden de Azure RBAC-beleids updates te plannen die na de migratie moeten plaatsvinden.

## <a name="i-backed-up-my-classic-vms-in-a-vault-can-i-migrate-my-vms-from-classic-mode-to-resource-manager-mode-and-protect-them-in-a-recovery-services-vault"></a>Ik heb een back-up gemaakt van mijn klassieke virtuele machines in een kluis. Kan ik mijn virtuele machines migreren van de klassieke modus naar de Resource Manager-modus en ze beschermen in een Recovery Services-kluis?

Wanneer u een virtuele machine van de klassieke naar de Resource Manager-modus verplaatst, worden back-ups die zijn gemaakt vóór de migratie, niet gemigreerd naar de zojuist gemigreerde Resource Manager-VM. Als u echter uw back-ups van klassieke Vm's wilt houden, volgt u deze stappen vóór de migratie. 

1. Ga in de Recovery Services kluis naar de Blade **Back-upitems** en selecteer de virtuele machine. 
2. Klik op back-up stoppen. Selecteer ' back-upgegevens behouden ' in de vervolg keuzelijst.

> [!NOTE]
> Met deze optie worden alle toekomstige back-uptaken gestopt om uw VM te beveiligen. Azure Backup-Service behoudt echter de herstel punten waarvan een back-up is gemaakt.  U moet betalen om de herstel punten in de kluis te blijven (Zie [Azure backup prijzen](https://azure.microsoft.com/pricing/details/backup/) voor meer informatie). U kunt de VM zo nodig herstellen. Als u besluit de beveiliging van de virtuele machine te hervatten, kunt u de *back-* upoptie voor hervatten gebruiken.
>
>

Als u de virtuele machine wilt migreren naar de Resource Manager-modus, 

1. Verwijder de back-up-/momentopname-extensie uit de VM.
2. Migreer de virtuele machines van de klassieke modus naar de Resource Manager-modus. Zorg ervoor dat de opslagruimte en de netwerkgegevens die corresponderen met de virtuele machine, ook naar de Resource Manager-modus worden gemigreerd.

Als u ook een back-up wilt maken van de gemigreerde virtuele machine, gaat u naar de Blade beheer van virtuele machines om [back-ups in te scha kelen](../backup/quick-backup-vm-portal.md#enable-backup-on-a-vm).

## <a name="can-i-validate-my-subscription-or-resources-to-see-if-theyre-capable-of-migration"></a>Kan ik mijn abonnement of resources valideren om te ontdekken of ze geschikt zijn voor migratie? 

Ja. Bij de door het platform ondersteunde migratieoptie is de eerste stap van het voorbereiden op de migratie het controleren of de resources geschikt zijn voor migratie. Als de validatiebewerking mislukt, ontvangt u berichten over alle redenen waarom de migratie niet kan worden voltooid.

## <a name="what-happens-if-i-run-into-a-quota-error-while-preparing-the-iaas-resources-for-migration"></a>Wat gebeurt er als er een quotumfout optreedt bij het voorbereiden van de IaaS-resources op migratie? 

Het wordt aanbevolen om de migratie af te breken en daarna een ondersteuningsaanvraag in te dienen voor het verhogen van de quota in de regio waarin u de VM's wilt migreren. Wanneer het quotaverzoek is goedgekeurd, kunt u de migratiestappen opnieuw uitvoeren.

## <a name="how-do-i-report-an-issue"></a>Hoe meld ik een probleem? 

Post uw problemen en vragen over de migratie naar onze [micro soft Q&een vraag pagina voor de virtuele machine](/answers/topics/azure-virtual-machines.html), met het tref woord tag classiciaasmigration. Het wordt aanbevolen om al uw vragen op dit forum te plaatsen. Als u een ondersteuningscontract hebt, kunt u ook een ondersteuningsticket aanmaken.

## <a name="what-if-i-dont-like-the-names-of-the-resources-that-the-platform-chose-during-migration"></a>Wat kan ik doen als ik de resourcenamen niet leuk vind die het platform heeft gekozen tijdens de migratie? 

Alle resources waarvoor u expliciet namen opgeeft in het klassieke implementatiemodel, behouden die naam tijdens de migratie. In sommige gevallen worden er nieuwe resources gemaakt. Er wordt dan bijvoorbeeld een netwerkinterface gemaakt voor elke VM. Momenteel wordt de mogelijkheid niet ondersteund om invloed uit te oefenen op de namen van de nieuwe resources die tijdens de migratie worden gemaakt. Geef het via het [Azure-feedbackforum](https://feedback.azure.com) aan als u deze mogelijkheid graag zou willen laten toevoegen.

## <a name="can-i-migrate-expressroute-circuits-used-across-subscriptions-with-authorization-links"></a>Kan ik ExpressRoute-circuits migreren die voor verschillende abonnementen met autorisatielinks worden gebruikt? 

ExpressRoute-circuits met abonnementsoverstijgende autorisatielinks kunnen niet automatisch worden gemigreerd zonder downtime. Er is informatie beschikbaar over het uitvoeren van handmatige migratie. Zie [ExpressRoute-circuits en de bijbehorende virtuele netwerken van het klassieke naar het Resource Manager-implementatiemodel migreren](../expressroute/expressroute-migration-classic-resource-manager.md) voor stappen en meer informatie.

## <a name="i-got-the-message-vm-is-reporting-the-overall-agent-status-as-not-ready-hence-the-vm-cannot-be-migrated-ensure-that-the-vm-agent-is-reporting-overall-agent-status-as-ready-or-vm-contains-extension-whose-status-is-not-being-reported-from-the-vm-hence-this-vm-cannot-be-migrated"></a>Ik heb het bericht *' de VM meldt de algehele agent status als niet gereed. Daarom kan de virtuele machine niet worden gemigreerd. Zorg ervoor dat de VM-agent de algehele agent status gereed rapporteert, of dat de VM een* *extensie bevat waarvan de status niet wordt gerapporteerd van de virtuele machine. Daarom kan deze virtuele machine niet worden gemigreerd. "*

Dit bericht wordt weergegeven wanneer de VM geen uitgaande verbinding heeft met internet. De VM-agent maakt gebruik van een uitgaande verbinding om het Azure-opslagaccount te bereiken. Zo kan de agentstatus elke vijf minuten worden bijgewerkt.


## <a name="next-steps"></a>Volgende stappen

* [Overzicht van door het platform ondersteunde migratie van IaaS-resources van klassiek naar Azure Resource Manager](./migration-classic-resource-manager-overview.md)
* [Technische details over door platforms ondersteunde migratie van klassiek naar Azure Resource Manager](migration-classic-resource-manager-deep-dive.md)
* [Planning voor de migratie van IaaS-resources van het klassieke implementatiemodel naar Azure Resource Manager](migration-classic-resource-manager-plan.md)
* [Power shell gebruiken voor het migreren van IaaS-resources van klassiek naar Azure Resource Manager](migration-classic-resource-manager-ps.md)
* [CLI gebruiken voor het migreren van IaaS-resources van klassiek naar Azure Resource Manager](migration-classic-resource-manager-cli.md)
* [Community tools voor hulp bij de migratie van IaaS-resources van klassiek naar Azure Resource Manager](migration-classic-resource-manager-community-tools.md)
* [Bekijk de meest voorkomende migratiefouten](migration-classic-resource-manager-errors.md)
