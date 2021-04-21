---
title: Servers met Azure Arc plannen en implementeren
description: Meer informatie over het inschakelen van een groot aantal machines Azure Arc servers om de configuratie van essentiële beveiligings-, beheer- en bewakingsmogelijkheden in Azure te vereenvoudigen.
ms.date: 04/21/2021
ms.topic: conceptual
ms.openlocfilehash: e3f8fe410da56f627ceab5f17c980f2daa1a262c
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107831974"
---
# <a name="plan-and-deploy-arc-enabled-servers"></a>Arc-servers plannen en implementeren

De implementatie van een IT-infrastructuurservice of zakelijke toepassing is een uitdaging voor elk bedrijf. Om deze goed uit te voeren en ongewenste verrassingen en niet-geplande kosten te voorkomen, moet u er grondig rekening mee houden om ervoor te zorgen dat u er zo snel mogelijk klaar voor bent. Als u de implementatie van Azure Arc servers op elke schaal wilt plannen, moet deze voldoen aan de ontwerp- en implementatiecriteria waaraan moet worden voldaan om de taken te kunnen voltooien.

Om de implementatie soepel te laten verlopen, moet uw plan een duidelijk inzicht krijgen in:

* Rollen en verantwoordelijkheden.
* Inventaris van fysieke servers of virtuele machines om te controleren of ze voldoen aan netwerk- en systeemvereisten.
* De vaardighedenset en training die nodig zijn om een succesvolle implementatie en door te gaand beheer mogelijk te maken.
* Acceptatiecriteria en hoe u het succes ervan bij houdt.
* Hulpprogramma's of methoden die moeten worden gebruikt om de implementaties te automatiseren.
* Geïdentificeerde risico's en risicobeperkingsplannen om vertragingen, onderbrekingen, enzovoort te voorkomen.
* Onderbrekingen voorkomen tijdens de implementatie.
* Wat is het escalatiepad wanneer er een significant probleem optreedt?

Het doel van dit artikel is ervoor te zorgen dat u bent voorbereid op een geslaagde implementatie van Azure Arc servers op meerdere fysieke productieservers of virtuele machines in uw omgeving.

## <a name="prerequisites"></a>Vereisten

* Op uw computers wordt een [ondersteund besturingssysteem voor](agent-overview.md#supported-operating-systems) de Connected Machine-agent uitgevoerd.
* Uw computers hebben connectiviteit van uw on-premises netwerk of andere cloudomgeving naar resources in Azure, rechtstreeks of via een proxyserver.
* Voor het installeren en configureren van de Arc-servers connected machine agent, een account met verhoogde bevoegdheden (dat wil zeggen, een beheerder of als root) bevoegdheden op de computers.
* Als u machines wilt onboarden, bent u lid **van Azure Connected Machine onboarding-rol.**
* Als u een computer wilt lezen, wijzigen en verwijderen, bent u lid van de rol **Azure Connected Machine resourcebeheerder.**

## <a name="pilot"></a>Test

Voordat u implementeert op alle productiemachines, begint u met het evalueren van dit implementatieproces voordat u dit breed in uw omgeving gaat gebruiken. Voor een pilot identificeert u een representatieve steekproef van machines die niet essentieel zijn voor het vermogen van uw bedrijf om zaken te doen. U moet er zeker van zijn dat u voldoende tijd hebt om de test uit te voeren en de impact ervan te beoordelen: we raden minimaal 30 dagen aan.

Stel een formeel plan op met een beschrijving van het bereik en de details van de test. Hier volgt een voorbeeld van wat een plan moet bevatten om u op weg te helpen.

* Doelstellingen: beschrijft de zakelijke en technische stuurprogramma's die hebben geleid tot de beslissing dat een pilot noodzakelijk is.
* Selectiecriteria: hiermee geeft u de criteria op die worden gebruikt om te selecteren welke aspecten van de oplossing via een pilot worden gedemonstreerd.
* Bereik: beschrijft het bereik van de test, waaronder maar niet beperkt tot oplossingsonderdelen, een verwachte planning, de duur van de test en het aantal machines waarop de test moet worden gericht.
* Succescriteria en metrische gegevens: definieer de criteria voor succes van de test en specifieke metingen die worden gebruikt om het succesniveau te bepalen.
* Trainingsplan: beschrijft het plan voor het trainen van systeemtechnici, beheerders, enzovoort, die nieuw zijn in Azure en IT-services tijdens de pilot.
* Overgangsplan: beschrijft de strategie en criteria die worden gebruikt om de overgang van pilot naar productie te begeleiden.
* Terugdraaien: beschrijft de procedures voor het terugdraaien van een pilot naar de status vóór de implementatie.
* Risico's: alle geïdentificeerde risico's voor het uitvoeren van de test en die zijn gekoppeld aan de productie-implementatie.

## <a name="phase-1-build-a-foundation"></a>Fase 1: Een basis bouwen

In deze fase kunnen systeemtechnici of beheerders de basisfuncties in hun Azure-abonnement van hun organisatie inschakelen om de basis te starten voordat ze uw machines inschakelen voor beheer door Arc-servers en andere Azure-services.

|Taak |Detail |Duur |
|-----|-------|---------|
| [Een resourcegroep maken](../../azure-resource-manager/management/manage-resource-groups-portal.md#create-resource-groups) | Een toegewezen resourcegroep die alleen Arc-servers bevat en beheer en bewaking van deze resources centraliseert. | Eén uur |
| Pas [tags toe](../../azure-resource-manager/management/tag-resources.md) om machines te organiseren. | Evalueer en ontwikkel een op IT afgestemde [tagstrategie](/azure/cloud-adoption-framework/decision-guides/resource-tagging/) die u kan helpen de complexiteit van het beheren van uw Arc-servers te verminderen en het nemen van beheerbeslissingen te vereenvoudigen. | Eén dag |
| Logboeken voor [Azure Monitor ontwerpen en implementeren](../../azure-monitor/logs/data-platform-logs.md) | Evalueer [ontwerp- en implementatieoverwegingen](../../azure-monitor/logs/design-logs-deployment.md) om te bepalen of uw organisatie een bestaande log analytics-werkruimte moet gebruiken of een andere Log Analytics-werkruimte moet implementeren om verzamelde logboekgegevens van hybride servers en computers op te slaan. <sup>1</sup> | Eén dag |
| [Een beheerplan Azure Policy](../../governance/policy/overview.md) ontwikkelen | Bepaal hoe u beheer van hybride servers en machines implementeert op abonnements- of resourcegroepbereik met Azure Policy. | Eén dag |
| Op [rollen gebaseerd toegangsbeheer](../../role-based-access-control/overview.md) (RBAC) configureren | Ontwikkel een toegangsplan om te bepalen wie toegang heeft tot het beheren van Arc-servers en de mogelijkheid om hun gegevens van andere Azure-services en -oplossingen weer te geven. | Eén dag |
| Machines identificeren met log analytics-agent die al is geïnstalleerd | Voer de volgende logboekquery uit in [Log Analytics om de](../../azure-monitor/logs/log-analytics-overview.md) conversie van bestaande Log Analytics-agentimplementaties naar door extensies beheerde agent te ondersteunen:<br> Hartslag <br> &#124; timegenerated > ago(30d) <br> &#124; waar ResourceType == "machines" en (ComputerEnvironment == "Niet-Azure") <br> &#124; op Computer, ResourceProvider, ResourceType, ComputerEnvironment | Eén uur |

<sup>1</sup> Een belangrijke overweging bij het evalueren van het ontwerp van uw Log Analytics-werkruimte is de integratie met Azure Automation ter ondersteuning van de Updatebeheer- en Wijzigingen bijhouden en inventaris-functie, evenals Azure Security Center en Azure Sentinel. Als uw organisatie al een Automation-account heeft en de beheerfuncties heeft ingeschakeld die zijn gekoppeld aan een Log Analytics-werkruimte, moet u evalueren of u beheerbewerkingen kunt centraliseren en stroomlijnen, en de kosten kunt minimaliseren door deze bestaande resources te gebruiken in plaats van een dubbel account, werkruimte, enzovoort te maken.

## <a name="phase-2-deploy-arc-enabled-servers"></a>Fase 2: Arc-servers implementeren

Vervolgens voegen we toe aan de basis van fase 1 door de Connected Machine-agent voor Arc-servers voor te bereiden en te implementeren.

|Taak |Detail |Duur |
|-----|-------|---------|
| Het vooraf gedefinieerde installatiescript downloaden | Bekijk en pas het vooraf gedefinieerde installatiescript voor de implementatie op schaal van de Connected Machine-agent aan ter ondersteuning van uw geautomatiseerde implementatievereisten.<br><br> Voorbeeld van onboarding-resources op schaal:<br><br> <ul><li> [Basisimplementatiescript op schaal](onboard-service-principal.md)</ul></li> <ul><li>[Onboarding op schaal VMware vSphere Windows Server-VM's](https://github.com/microsoft/azure_arc/blob/main/docs/azure_arc_jumpstart/azure_arc_servers/scaled_deployment/vmware_scaled_powercli_win/_index.md)</ul></li> <ul><li>[Onboarding op schaal VMware vSphere Linux-VM's](https://github.com/microsoft/azure_arc/blob/main/docs/azure_arc_jumpstart/azure_arc_servers/scaled_deployment/vmware_scaled_powercli_linux/_index.md)</ul></li> <ul><li>[Onboarding op schaal van AWS EC2-instanties met Ansible](https://github.com/microsoft/azure_arc/blob/main/docs/azure_arc_jumpstart/azure_arc_servers/scaled_deployment/aws_scaled_ansible/_index.md)</ul></li> <ul><li>[Implementatie op schaal met behulp van remoting van PowerShell](./onboard-powershell.md) (alleen Windows)</ul></li>| Een of meer dagen, afhankelijk van de vereisten, organisatieprocessen (bijvoorbeeld Wijzigings- en releasebeheer) en de gebruikte automatiseringsmethode. |
| [Een service-principal maken](onboard-service-principal.md#create-a-service-principal-for-onboarding-at-scale) |Maak een service-principal om machines niet-interactief te verbinden met behulp Azure PowerShell of vanuit de portal.| Eén uur |
| De Connected Machine-agent implementeren op uw doelservers en -machines |Gebruik uw automatiseringshulpprogramma om de scripts te implementeren op uw servers en deze te verbinden met Azure.| Een of meer dagen, afhankelijk van uw releaseplan en als u een gefaseerd implementatie volgt. |

## <a name="phase-3-manage-and-operate"></a>Fase 3: Beheren en werken

In fase 3 zien beheerders of systeemtechnici automatisering van handmatige taken voor het beheren en uitvoeren van de Connected Machine-agent en de machine tijdens hun levenscyclus.

|Taak |Detail |Duur |
|-----|-------|---------|
|Een waarschuwing Resource Health maken |Als een server langer dan 15 minuten geen heartbeats meer naar Azure meer verstuurt, kan dit betekenen dat deze offline is, de netwerkverbinding is geblokkeerd of dat de agent niet wordt uitgevoerd. Ontwikkel een plan voor hoe u deze incidenten gaat reageren en onderzoeken en gebruik Resource Health [waarschuwingen om](../..//service-health/resource-health-alert-monitor-guide.md) een melding te ontvangen wanneer ze starten.<br><br> Geef het volgende op bij het configureren van de waarschuwing:<br> **Resourcetype**  =  **Azure Arc ingeschakelde servers**<br> **Huidige resourcestatus**  =  **Niet beschikbaar**<br> **Vorige resourcestatus**  =  **Beschikbaar** | Eén uur |
|Een waarschuwing Azure Advisor maken | Voor de beste ervaring en de meest recente beveiligings- en bugfixes raden we u aan de Azure Arc serversagent up-to-date te houden. Out-of-date agents worden geïdentificeerd met een [Azure Advisor waarschuwing](../../advisor/advisor-alerts-portal.md).<br><br> Geef het volgende op bij het configureren van de waarschuwing:<br> **Aanbevelingstype**  =  **Upgrade naar de nieuwste versie van de Azure Connected Machine Agent** | Eén uur |
|[Azure-beleid toewijzen](../../governance/policy/assign-policy-portal.md) aan het bereik van uw abonnement of resourcegroep |Wijs **het beleid Azure Monitor voor VM's** [inschakelen](../../azure-monitor/vm/vminsights-enable-policy.md) (en andere beleid dat aan uw behoeften voldoet) toe aan het abonnement of het bereik van de resourcegroep. Azure Policy kunt u beleidsdefinities toewijzen waarmee de vereiste agents voor VM-inzichten in uw omgeving worden geïnstalleerd.| Varieert |
|[Schakel Updatebeheer voor uw Arc-servers in](../../automation/update-management/enable-from-automation-account.md) |Configureer Updatebeheer in Azure Automation om besturingssysteemupdates te beheren voor uw virtuele Windows- en Linux-machines die zijn geregistreerd bij Arc-servers. | 15 minuten |

## <a name="next-steps"></a>Volgende stappen

* Informatie over probleemoplossing vindt u in de handleiding [Problemen met de Connected Machine-agent oplossen.](troubleshoot-agent-onboard.md)

* Meer informatie over het vereenvoudigen van implementatie met andere Azure-services, zoals Azure Automation [State Configuration](../../automation/automation-dsc-overview.md) en andere ondersteunde [Azure VM-extensies.](manage-vm-extensions.md)