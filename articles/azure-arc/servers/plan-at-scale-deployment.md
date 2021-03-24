---
title: Azure Arc-servers plannen en implementeren
description: Meer informatie over het inschakelen van een groot aantal machines aan Azure Arc-servers om de configuratie van essentiële beveiligings-, beheer-en bewakings mogelijkheden in azure te vereenvoudigen.
ms.date: 03/18/2021
ms.topic: conceptual
ms.openlocfilehash: 5aa7022dba943fa3de247404522408f4660e80e3
ms.sourcegitcommit: a8ff4f9f69332eef9c75093fd56a9aae2fe65122
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2021
ms.locfileid: "105023279"
---
# <a name="plan-and-deploy-arc-enabled-servers"></a>Arc-servers plannen en implementeren

De implementatie van een IT-infrastructuur service of bedrijfs toepassing is een uitdaging voor elk bedrijf. Om het goed te kunnen uitvoeren en onwelkomloze en ongeplande kosten te voor komen, moet u deze zorgvuldig plannen om ervoor te zorgen dat u zo klaar mogelijk bent. Voor het plannen van de implementatie van Azure Arc enabled-servers op elke schaal moet het voldoen aan de ontwerp-en implementatie criteria waaraan moet worden voldaan om de taken te kunnen volt ooien.

Als u de implementatie soepel wilt uitvoeren, moet uw plan een duidelijk inzicht hebben in:

* Rollen en verantwoordelijkheden.
* Inventarisatie van fysieke servers of virtuele machines om te controleren of deze voldoen aan de netwerk-en systeem vereisten.
* De vaardig heden en training die vereist zijn om de implementatie en het continue beheer mogelijk te maken.
* Acceptatie criteria en hoe u het kunt volgen.
* Hulpprogram ma's of methoden die moeten worden gebruikt voor het automatiseren van de implementaties.
* Geïdentificeerde Risico's en beperkende plannen om vertragingen, onderbrekingen, enzovoort te voor komen.
* Hoe voorkom ik onderbrekingen tijdens de implementatie.
* Wat is het escalatie traject wanneer er een significant probleem optreedt?

Het doel van dit artikel is om ervoor te zorgen dat u bent voor bereid op een succes volle implementatie van Azure Arc-servers op meerdere fysieke productie servers of virtuele machines in uw omgeving.

## <a name="prerequisites"></a>Vereisten

* Op uw computers wordt een [ondersteund besturings systeem](agent-overview.md#supported-operating-systems) uitgevoerd voor de verbonden machine agent.
* Uw computers hebben verbinding met uw on-premises netwerk of een andere cloud omgeving, hetzij rechtstreeks, hetzij via een proxy server.
* Voor het installeren en configureren van de computer agent met Arc ingeschakelde computers, een account met verhoogde bevoegdheden (dat wil zeggen, een Administrator-of als basis)-bevoegdheid op de computers.
* Voor de onboarding van machines bent u lid van de rol **Azure Connected machine** .
* Als u een machine wilt lezen, wijzigen en verwijderen, bent u lid van de **Azure Connected machine resource Administrator** -rol.

## <a name="pilot"></a>Test

Voordat u implementeert op alle productie machines, moet u eerst het implementatie proces evalueren voordat u het globaal in uw omgeving aanneemt. Voor een pilot moet u een representatieve steek proef van machines identificeren die niet essentieel zijn voor uw bedrijf om zaken te doen. Zorg ervoor dat u voldoende tijd hebt om de pilot uit te voeren en het effect ervan te beoordelen: we raden u ten minste 30 dagen aan.

Stel een formeel plan op waarin het bereik en de details van de pilot worden beschreven. Hier volgt een voor beeld van een plan dat u kunt gebruiken om aan de slag te gaan.

* Doel stellingen: beschrijft de zakelijke en technische Stuur Programma's die hebben geleid tot de beslissing dat een pilot nood zakelijk is.
* Selectie criteria: Hiermee geeft u de criteria op die worden gebruikt om te selecteren welke aspecten van de oplossing worden gedemonstreerd via een pilot.
* Bereik: Hiermee wordt het bereik van de pilot beschreven, inclusief, maar niet beperkt tot oplossings onderdelen, verwachte planning, duur van de pilot en het aantal machines dat moet worden gericht.
* Criteria voor succes en metrische gegevens: Definieer de succes criteria van de pilot en specifieke metingen die worden gebruikt om het niveau van het succes te bepalen.
* Trainings plan: hierin wordt het plan voor het trainen van systeem technici, beheerders, enzovoort, beschreven die tijdens de pilot geen ervaring hebben met Azure en IT-Services.
* Overgangs plan: beschrijft de strategie en de criteria die worden gebruikt om de overgang van de pilot naar de productie handleiding te begeleiden.
* Rollback: hierin worden de procedures beschreven voor het terugdraaien van een pilot naar de status vóór de implementatie.
* Risico's: lijst met alle geïdentificeerde Risico's voor het uitvoeren van de pilot en het koppelen van de productie-implementatie.

## <a name="phase-1-build-a-foundation"></a>Fase 1: een basis versie bouwen

In deze fase stellen systeem engineers of beheerders de belangrijkste functies in hun organisaties Azure-abonnement in om de basis te starten voordat ze uw computers kunnen beheren door middel van Arc ingeschakelde servers en andere Azure-Services.

|Taak |Detail |Duur |
|-----|-------|---------|
| [Een resourcegroep maken](../../azure-resource-manager/management/manage-resource-groups-portal.md#create-resource-groups) | Een speciale resource groep die alleen de Arc ingeschakelde servers bevat en het beheer en de bewaking van deze resources centraliseren. | Een uur |
| [Tags](../../azure-resource-manager/management/tag-resources.md) Toep assen om machines te organiseren. | Evalueer en ontwikkel een IT-uitgelijnde [coderings strategie](/azure/cloud-adoption-framework/decision-guides/resource-tagging/) die u kan helpen de complexiteit van het beheer van uw Arc-servers te reduceren en te vereenvoudigen beheer beslissingen te nemen. | Eén dag |
| [Azure monitor-logboeken](../../azure-monitor/logs/data-platform-logs.md) ontwerpen en implementeren | Beoordeling van [overwegingen voor ontwerpen en implementeren](../../azure-monitor/logs/design-logs-deployment.md) om te bepalen of uw organisatie een bestaande log Analytics werk ruimte moet gebruiken of implementeren om verzamelde logboek gegevens van hybride servers en computers op te slaan. <sup>1</sup> | Eén dag |
| [Een Azure Policy](../../governance/policy/overview.md) governance-abonnement ontwikkelen | Bepaal hoe u bestuurt van hybride servers en machines op het abonnement of het bereik van de resource groep met Azure Policy. | Eén dag |
| [Op rollen gebaseerd toegangs beheer](../../role-based-access-control/overview.md) (RBAC) configureren | Ontwikkel een toegangs plan om te bepalen wie toegang heeft tot het beheren van Arc ingeschakelde servers en de mogelijkheid om hun gegevens te bekijken uit andere Azure-Services en-oplossingen. | Eén dag |
| Computers identificeren waarop Log Analytics-agent al is geïnstalleerd | Voer de volgende logboek query uit in [log Analytics](../../azure-monitor/logs/log-analytics-overview.md) om de conversie van bestaande implementaties van log Analytics agent naar een door extensie beheerde agent te ondersteunen:<br> Hartslag <br> &#124; waar TimeGenerated > geleden (30d) <br> &#124; waarbij resource type = = "machines" en (ComputerEnvironment = = "niet-Azure") <br> &#124; samenvatten per computer, resource provider, resource type, ComputerEnvironment | Een uur |

<sup>1</sup> een belang rijke overweging als onderdeel van het evalueren van uw log Analytics werkruimte ontwerp is integratie met Azure Automation ter ondersteuning van de functie updatebeheer en het wijzigingen bijhouden en de inventarisatie, evenals Azure Security Center en Azure Sentinel. Als uw organisatie al een Automation-account heeft en de beheer functies die zijn gekoppeld aan een Log Analytics werk ruimte hebt ingeschakeld, moet u beoordelen of u beheer bewerkingen kunt centraliseren en stroom lijnen, en hoe u de kosten minimaliseert door gebruik te maken van de bestaande resources en een duplicaat van de werk ruimte, enzovoort, enz.

## <a name="phase-2-deploy-arc-enabled-servers"></a>Fase 2: Arc ingeschakelde servers implementeren

We voegen vervolgens toe aan de basis die in fase 1 is vastgelegd door het voorbereiden en implementeren van de op Arc ingeschakelde servers aangesloten machine agent.

|Taak |Detail |Duur |
|-----|-------|---------|
| Het vooraf gedefinieerde installatie script downloaden | Bekijk en pas het vooraf gedefinieerde installatie script voor de implementatie van de verbonden machine-agent voor de geautomatiseerde implementatie vereisten aan.<br><br> Voor beeld van op schaal onboarding-resources:<br><br> <ul><li> [Script voor eenvoudige implementatie op basis van grootte](onboard-service-principal.md)</ul></li> <ul><li>[Schaal bare onboarding VMware vSphere Windows Server-Vm's](https://github.com/microsoft/azure_arc/blob/main/docs/azure_arc_jumpstart/azure_arc_servers/scaled_deployment/vmware_scaled_powercli_win/_index.md)</ul></li> <ul><li>[Voor bereiding voor onboarding VMware vSphere Linux-Vm's](https://github.com/microsoft/azure_arc/blob/main/docs/azure_arc_jumpstart/azure_arc_servers/scaled_deployment/vmware_scaled_powercli_linux/_index.md)</ul></li> <ul><li>[AWS EC2-instanties op schaal met Ansible](https://github.com/microsoft/azure_arc/blob/main/docs/azure_arc_jumpstart/azure_arc_servers/scaled_deployment/aws_scaled_ansible/_index.md)</ul></li> <ul><li>[Implementatie op schaal met behulp van externe communicatie met Power shell](./onboard-powershell.md) (alleen Windows)</ul></li>| Een of meer dagen, afhankelijk van de vereisten, bedrijfs processen (bijvoorbeeld wijzigings-en release beheer) en de gebruikte automatiserings methode. |
| [Een service-principal maken](onboard-service-principal.md#create-a-service-principal-for-onboarding-at-scale) |Maak een Service-Principal om machines niet-interactief te verbinden met Azure PowerShell of vanuit de portal.| Een uur |
| De verbonden machine agent implementeren op uw doel servers en-computers |Gebruik uw automatiserings programma om de scripts te implementeren op uw servers en deze te verbinden met Azure.| Een of meer dagen, afhankelijk van uw release plan en na een gefaseerde implementatie. |

## <a name="phase-3-manage-and-operate"></a>Fase 3: beheren en uitvoeren

Fase 3 laat beheerders of systeem technici de automatisering van hand matige taken mogelijk maken voor het beheren en uitvoeren van de verbonden machine agent en de computer tijdens hun levens cyclus.

|Taak |Detail |Duur |
|-----|-------|---------|
|Een Resource Health waarschuwing maken |Als een server langer dan 15 minuten stopt met het verzenden van heartbeats naar Azure, kan dit betekenen dat deze offline is, de netwerk verbinding is geblokkeerd of de agent niet actief is. Ontwikkel een plan voor de manier waarop u reageert en onderzoek deze incidenten en gebruik [resource Health waarschuwingen](../..//service-health/resource-health-alert-monitor-guide.md) om een melding te ontvangen wanneer ze worden gestart.<br><br> Geef het volgende op bij het configureren van de waarschuwing:<br> **Resource type**  =  **Servers met Azure-Arc ingeschakeld**<br> **Huidige resource status**  =  **Niet beschikbaar**<br> **Vorige resource status**  =  **Beschikbaar** | Een uur |
|Een Azure Advisor waarschuwing maken | Voor de beste ervaring en meest recente oplossingen voor beveiligings-en probleem oplossing raden we u aan de agent voor de Azure-servers die is ingeschakeld, up-to-date te houden. Verouderde agents worden aangeduid met een [Azure Advisor waarschuwing](../../advisor/advisor-alerts-portal.md).<br><br> Geef het volgende op bij het configureren van de waarschuwing:<br> **Type aanbeveling**  =  **Upgrade uitvoeren naar de nieuwste versie van de Azure Connected machine agent** | Een uur |
|[Azure-beleid toewijzen](../../governance/policy/assign-policy-portal.md) aan uw abonnement of het bereik van de resource groep |Wijs het [beleid](../../azure-monitor/vm/vminsights-enable-policy.md) voor **Azure monitor voor VM's inschakelen** (en andere die aan uw behoeften voldoen) toe aan het bereik van het abonnement of de resource groep. Met Azure Policy kunt u beleids definities toewijzen waarmee de vereiste agents voor Azure Monitor voor VM's in uw omgeving worden geïnstalleerd.| Varieert |
|[Updatebeheer inschakelen voor uw op Arc ingeschakelde servers](../../automation/update-management/enable-from-automation-account.md) |Updatebeheer in Azure Automation configureren voor het beheren van updates van het besturings systeem voor uw virtuele Windows-en Linux-machines die zijn geregistreerd bij servers waarop Arc is ingeschakeld. | 15 minuten |

## <a name="next-steps"></a>Volgende stappen

* Informatie over probleem oplossing vindt u in de [hand leiding problemen met verbonden machine agent oplossen](troubleshoot-agent-onboard.md).

* Meer informatie over het vereenvoudigen van de implementatie met andere Azure-Services, zoals Azure Automation [status configuratie](../../automation/automation-dsc-overview.md) en andere ondersteunde [Azure VM-extensies](manage-vm-extensions.md).