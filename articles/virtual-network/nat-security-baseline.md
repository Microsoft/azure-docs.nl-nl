---
title: Azure-beveiligings basislijn voor Virtual Network NAT
description: De Virtual Network NAT-beveiligings basislijn biedt procedure richtlijnen en resources voor het implementeren van de beveiligings aanbevelingen die zijn opgegeven in de Azure Security-Bench Mark.
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 11/20/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 4ab71138f110df19ec84fa8707b480ad1bc6e72c
ms.sourcegitcommit: 30906a33111621bc7b9b245a9a2ab2e33310f33f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/22/2020
ms.locfileid: "95255138"
---
# <a name="azure-security-baseline-for-virtual-network-nat"></a>Azure-beveiligings basislijn voor Virtual Network NAT

Deze beveiligings basislijn is van toepassing op de richt lijnen van [Azure Security Bench Mark versie 1,0](https://docs.microsoft.com/azure/security/benchmarks/overview-v1) naar micro soft Virtual Network nat. De Azure Security-benchmark biedt aanbevelingen voor hoe u uw cloudoplossingen in Azure kunt beveiligen.
De inhoud wordt gegroepeerd op de **beveiligings controles** die zijn gedefinieerd door de Azure Security-Bench Mark en de bijbehorende richt lijnen die van toepassing zijn op Virtual Network nat. **Besturings elementen** die niet van toepassing zijn op Virtual Network nat zijn uitgesloten.

 
Zie het [volledige Virtual Network NAT-beveiligings basislijn toewijzings bestand](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)voor meer informatie over de manier waarop Virtual Network NAT volledig is toegewezen aan de beveiligings benchmark van Azure.

## <a name="network-security"></a>Netwerkbeveiliging

*Zie [Azure Security Bench Mark: Network Security](https://docs.microsoft.com/azure/security/benchmarks/security-control-network-security)(Engelstalig) voor meer informatie.*

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-nics"></a>1,2: de configuratie en het verkeer van virtuele netwerken, subnetten en Nic's bewaken en vastleggen

**Hulp**: Virtual Network NAT biedt geen ondersteuning voor het beheren van uitgaand verkeer met netwerk beveiligings groepen (NSG).  Binnenkomend verkeer wordt alleen toegestaan als reactie op een uitgaande stroom.

U kunt echter NSG-stroom Logboeken (netwerk beveiligings groep) gebruiken met NAT-gateway bronnen voor het bewaken van uitgaand verkeer.

Gebruik Azure Security Center en volg de aanbevelingen voor netwerk beveiliging om uw Azure-netwerk bronnen te beveiligen. Schakel logboeken stroom van de netwerk beveiligings groep in en verzend de logboeken naar een Azure Storage account voor controle. U kunt de stroom logboeken ook naar een Log Analytics-werk ruimte verzenden en vervolgens Traffic Analytics gebruiken om inzicht te krijgen in verkeers patronen in uw Azure-Cloud. Enkele voor delen van Traffic Analytics zijn de mogelijkheid om netwerk activiteiten te visualiseren, HOTS pots en beveiligings Risico's te identificeren, verkeers patronen te begrijpen en netwerk configuraties te lokaliseren. 

- [Overzicht van Virtual Network NAT](https://docs.microsoft.com/azure/virtual-network/nat-overview)

- [NAT-gatewayresource](https://docs.microsoft.com/azure/virtual-network/nat-gateway-resource)

- [Stroom logboeken van netwerk beveiligings groepen inschakelen](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal) 

- [Traffic Analytics inschakelen en gebruiken](https://docs.microsoft.com/azure/network-watcher/traffic-analytics)

- [Informatie over de netwerk beveiliging die wordt verschaft door Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-network-recommendations)

**Azure Security Center bewaking**: momenteel niet beschikbaar

**Verantwoordelijkheid**: klant

### <a name="15-record-network-packets"></a>1,5: netwerk pakketten opnemen

**Hulp**: Schakel Network Watcher pakket vastleggen in om afwijkende activiteiten te onderzoeken. 

- [Een Network Watcher-exemplaar maken](https://docs.microsoft.com/azure/network-watcher/network-watcher-create)

**Azure Security Center bewaking**: Ja

**Verantwoordelijkheid**: klant

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: standaard beveiligings configuraties voor netwerk apparaten onderhouden

**Richt lijnen**: standaard beveiligings configuraties definiëren en implementeren voor subnetten die zijn geconfigureerd met NAT-gateway bronnen met behulp van ingebouwde of aangepaste Azure Policy definities en-toewijzingen.

- [Azure Policy configureren en beheren](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

- [Voor beelden Azure Policy voor netwerken](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#network)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: gebruik automatische hulpprogram ma's om netwerk bron configuraties te bewaken en wijzigingen te detecteren

**Hulp**: Azure-activiteiten logboek gebruiken om resource configuraties te bewaken en wijzigingen in uw NAT-gateway bronnen en virtuele netwerk bronnen te detecteren. Maak waarschuwingen in Azure Monitor om u te waarschuwen wanneer kritieke resources worden gewijzigd.

- [Activiteiten logboek gebeurtenissen van Azure weer geven en ophalen](https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view)

- [Waarschuwingen maken in Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

## <a name="logging-and-monitoring"></a>Logboekregistratie en bewaking

*Zie [Azure Security Bench Mark: Logging and monitoring](https://docs.microsoft.com/azure/security/benchmarks/security-control-logging-monitoring)(Engelstalig) voor meer informatie.*

### <a name="22-configure-central-security-log-management"></a>2,2: Centraal beveiligings logboek beheer configureren

**Hulp**: opname logboeken die betrekking hebben op Virtual Network NAT via Azure monitor om beveiligings gegevens te verzamelen die worden gegenereerd door eindpunt apparaten, netwerk bronnen en andere beveiligings systemen. Gebruik in Azure Monitor Log Analytics-werk ruimten om analyses uit te voeren en te uitvoeren en gebruik Azure Storage accounts voor lange termijn-en archiverings opslag.

U kunt deze gegevens ook in-of uitschakelen voor Azure Sentinel of een SIEM van derden.

- [Azure-Sentinel onboarden](https://docs.microsoft.com/azure/sentinel/quickstart-onboard) 

- [Platform logboeken en metrische gegevens verzamelen met Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings) 

- [Aan de slag met Azure Monitor en integratie van SIEM van derden](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)

**Azure Security Center bewaking**: Ja

**Verantwoordelijkheid**: klant

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: controle logboek registratie inschakelen voor Azure-resources

**Hulp**: activiteiten logboeken, die automatisch beschikbaar zijn, bevatten alle schrijf bewerkingen (put, post, Delete) voor uw NAT gateway-resources, met uitzonde ring van Lees bewerkingen (Get). Activiteiten logboeken kunnen worden gebruikt om een fout te vinden bij het oplossen van problemen of om te controleren hoe een gebruiker in uw organisatie een resource heeft gewijzigd.

- [Platform logboeken en metrische gegevens verzamelen met Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings) 

- [Logboek registratie en verschillende logboek typen in azure](https://docs.microsoft.com/azure/azure-monitor/platform/platform-logs-overview) 

Virtual Network NAT maakt momenteel geen aanvullende diagnostische logboeken die door klanten kunnen worden geconfigureerd.

**Azure Security Center bewaking**: Ja

**Verantwoordelijkheid**: klant

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: waarschuwingen inschakelen voor afwijkende activiteiten

**Hulp**: gebruik Azure Security Center met log Analytics werk ruimte voor bewaking en waarschuwingen over afwijkende activiteiten die in beveiligings logboeken en gebeurtenissen zijn gevonden. In plaats daarvan kunt u gegevens in-en inschakelen voor Azure Sentinel.

- [Azure-Sentinel onboarden](https://docs.microsoft.com/azure/sentinel/quickstart-onboard) 

- [Waarschuwingen beheren in Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts) 

- [Een waarschuwing over logboek gegevens van log Analytics](https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-response)

**Azure Security Center bewaking**: Ja

**Verantwoordelijkheid**: klant

## <a name="inventory-and-asset-management"></a>Inventarisatie en Asset Management

*Zie [Azure Security Bench Mark: Inventory and Asset Management](https://docs.microsoft.com/azure/security/benchmarks/security-control-inventory-asset-management)voor meer informatie.*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: automatische Asset-detectie oplossing gebruiken

**Hulp**: Azure resource Graph gebruiken voor het opvragen/detecteren van alle resources (zoals compute, opslag, netwerk, poorten en protocollen enzovoort) binnen uw abonnementen. 

Zorg ervoor dat u de juiste (Lees) machtigingen in uw Tenant hebt en alle Azure-abonnementen en-resources in uw abonnementen hebt geïnventariseerd.

Hoewel klassieke Azure-resources kunnen worden gedetecteerd via resource grafiek, is het raadzaam om Azure Resource Manager gebaseerde resources te maken en te gebruiken.

- [Query's voor Azure resource Graph](https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="62-maintain-asset-metadata"></a>6,2: meta gegevens van activa onderhouden

**Richt lijnen**: Tags Toep assen op uw Azure-resources, resource groepen en abonnementen om ze logisch in een taxonomie te organiseren. Elke tag bestaat uit een naam en een waardepaar. U kunt de naam Omgeving en de waarde Productie bijvoorbeeld toepassen op alle resources in de productie.

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: niet-geautoriseerde Azure-resources verwijderen

**Hulp**: Labels Toep assen voor het beheer van Azure-resources.

- [Aanvullende Azure-abonnementen maken](https://docs.microsoft.com/azure/billing/billing-create-subscription) 

- [Beheergroepen maken](https://docs.microsoft.com/azure/governance/management-groups/create) 

- [Tags maken en gebruiken](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6,4: een inventaris van goedgekeurde Azure-resources definiëren en onderhouden

**Hulp**: momenteel niet beschikbaar

**Azure Security Center bewaking**: momenteel niet beschikbaar

**Verantwoordelijkheid**: klant

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: monitor voor niet-goedgekeurde Azure-resources

**Hulp**: gebruik Azure Policy om beperkingen toe te voegen voor het type resources dat kan worden gemaakt in uw abonnement (en).
Gebruik Azure resource Graph voor het opvragen/detecteren van resources binnen hun abonnement (en). Zorg ervoor dat alle Azure-resources die aanwezig zijn in de omgeving, zijn goedgekeurd.

- [Azure Policy configureren en beheren](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage) 
- [Query's maken met Azure Graph](https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="69-use-only-approved-azure-services"></a>6,9: alleen goedgekeurde Azure-Services gebruiken

**Hulp**: gebruik Azure Policy om te beperken welke services u in uw omgeving kunt inrichten.

- [Azure Policy configureren en beheren](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

- [Een specifiek resource type weigeren met Azure Policy](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#general)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

## <a name="secure-configuration"></a>Veilige configuratie

*Zie [Azure Security Bench Mark: Secure Configuration](https://docs.microsoft.com/azure/security/benchmarks/security-control-secure-configuration)(Engelstalig) voor meer informatie.*

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: Beveilig Azure-resource configuraties onderhouden

**Richt lijnen**: niet van toepassing; Virtual Network NAT heeft geen beveiligings configuraties.

**Azure Security Center bewaking**: momenteel niet beschikbaar

**Verantwoordelijkheid**: klant

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: de configuratie van Azure-resources veilig opslaan

**Hulp**: Azure DevOps gebruiken om uw code veilig op te slaan en te beheren, zoals aangepaste Azure Policy definities, Azure Resource Manager sjablonen en desired state Configuration-scripts. Als u toegang wilt krijgen tot de resources die u beheert in azure DevOps, kunt u machtigingen verlenen of weigeren aan specifieke gebruikers, ingebouwde beveiligings groepen of groepen die zijn gedefinieerd in Azure Active Directory (Azure AD) als deze zijn geïntegreerd met Azure DevOps, of Active Directory als dit is geïntegreerd met TFS. 

- [Code opslaan in azure DevOps](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops) 

- [Over machtigingen en groepen in azure DevOps](https://docs.microsoft.com/azure/devops/organizations/security/about-permissions)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: hulpprogram ma's voor configuratie beheer voor Azure-resources implementeren

**Richt lijnen**: niet van toepassing; Virtual Network NAT heeft geen beveiligings configuraties.

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

## <a name="incident-response"></a>Reageren op incidenten

*Zie [Azure Security Bench Mark: Incident Response](https://docs.microsoft.com/azure/security/benchmarks/security-control-incident-response)(Engelstalig) voor meer informatie.*

### <a name="101-create-an-incident-response-guide"></a>10,1: een hand leiding voor reactie op incidenten maken

**Hulp**: er moet een procedure voor reactie op incidenten worden gemaakt om ervoor te zorgen dat er geschikte incidenten kunnen worden verwerkt, zodat het het juiste prioriteits niveau krijgt als de incidenten worden opgelost.

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: een beoordelings procedure voor incidenten en prioriteits procedures maken

**Richt lijnen**: er moet een beoordelings-en prioriteiten procedure worden gemaakt om ervoor te zorgen dat het juiste risico of bedreigings Score kan optreden, zodat deze het juiste prioriteits niveau krijgt voor de oplossing van incidenten.

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="103-test-security-response-procedures"></a>10,3: procedures voor beveiligings antwoorden testen

**Hulp**: er moet een procedure voor beveiligings reacties worden gemaakt en getest om te zorgen voor een goede risico beperking met het juiste prioriteits niveau van de incidenten van de verwachte oplossing.

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: contact gegevens van het beveiligings incident opgeven en waarschuwings meldingen configureren voor beveiligings incidenten

**Hulp**: er moet een procedure voor beveiligings reacties worden gemaakt en getest om te zorgen voor een goede risico beperking met het juiste prioriteits niveau van de incidenten van de verwachte oplossing.

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: beveiligings waarschuwingen opnemen in uw reactie systeem van uw incident

**Hulp**: er moet een procedure voor beveiligings reacties worden gemaakt en getest om te zorgen voor een goede risico beperking met het juiste prioriteits niveau van de incidenten van de verwachte oplossing.

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="106-automate-the-response-to-security-alerts"></a>10,6: de reactie op beveiligings waarschuwingen automatiseren

**Hulp**: er moet een procedure voor beveiligings reacties worden gemaakt en getest om te zorgen voor een goede risico beperking met het juiste prioriteits niveau van de incidenten van de verwachte oplossing.

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

## <a name="penetration-tests-and-red-team-exercises"></a>Penetratietests en Red Team-oefeningen

*Zie [Azure Security Bench Mark: Indringings tests en rode team oefeningen](https://docs.microsoft.com/azure/security/benchmarks/security-control-penetration-tests-red-team-exercises)voor meer informatie.*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11,1: voert regel matig indringings tests van uw Azure-resources uit en zorgt voor herbemiddeling van alle essentiële beveiligings resultaten

**Richt lijnen**: Volg de stappen voor het testen van de Microsoft Cloud indringings regels om ervoor te zorgen dat uw indringings tests niet worden geschonden door het micro soft-beleid. Gebruik de strategie van micro soft en de uitvoering van de implementatie van de indringing van een live site in de Cloud, services en toepassingen die door micro soft worden beheerd. 

- [Regels van betrokkenheid voor het testen van indringing](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Microsoft Cloud rode teams](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

## <a name="next-steps"></a>Volgende stappen

- Zie [overzicht van Azure Security Bench Mark v2](/azure/security/benchmarks/overview)
- Meer informatie over [Azure-beveiligings basislijnen](/azure/security/benchmarks/security-baselines-overview)
