---
title: Azure-beveiligings basislijn voor Azure front deur
description: De Azure front-deur beveiligings basislijn biedt procedure richtlijnen en resources voor het implementeren van de beveiligings aanbevelingen die zijn opgegeven in de Azure Security-Bench Mark.
author: msmbaldwin
ms.service: frontdoor
ms.topic: conceptual
ms.date: 11/18/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 6d6a392d25aa96ab9b4dbb7763b37c1021db71aa
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/21/2020
ms.locfileid: "95026281"
---
# <a name="azure-security-baseline-for-azure-front-door"></a>Azure-beveiligings basislijn voor Azure front deur

Deze beveiligings basislijn past richt lijnen toe van de [Azure Security Bench Mark-versie 2,0](../security/benchmarks/overview.md) naar Azure front deur. De Azure Security-benchmark biedt aanbevelingen voor hoe u uw cloudoplossingen in Azure kunt beveiligen. De inhoud wordt gegroepeerd op de **beveiligings controles** die zijn gedefinieerd door de Azure Security-Bench Mark en de bijbehorende richt lijnen die van toepassing zijn op de Azure front-deur. **Besturings elementen** die niet van toepassing zijn op de voor deur van Azure, zijn uitgesloten.

Als u wilt zien hoe de front-deur van Azure volledig is toegewezen aan de beveiligings benchmark van Azure, raadpleegt u het [volledige Azure front-deur beveiliging Baseline toewijzings bestand](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Netwerkbeveiliging

*Zie [Azure Security Bench Mark: Network Security](/azure/security/benchmarks/security-controls-v2-network-security)(Engelstalig) voor meer informatie.*

### <a name="ns-4-protect-applications-and-services-from-external-network-attacks"></a>NS-4: toepassingen en Services beveiligen tegen aanvallen van externe netwerken

**Richt lijnen**: gebruik Azure PowerShell om een beleid voor geofiltering te maken en het beleid te koppelen aan uw bestaande Azure front klep-frontend-host. Dit beleid voor geo-filtering blokkeert aanvragen van externe netwerken, zoals de items uit andere landen of regio's, behalve Verenigde Staten.

- [Zelf studie-een WAF-beleid voor geo-filtering voor uw voor deur instellen](front-door-tutorial-geo-filtering.md)

**Azure Security Center bewaking**: Ja

**Verantwoordelijkheid**: klant

### <a name="ns-6-simplify-network-security-rules"></a>NS-6: netwerk beveiligings regels vereenvoudigen

**Hulp**: gebruik Virtual Network Service Tags om netwerk toegangs beheer te definiëren voor netwerk beveiligings groepen die zijn geconfigureerd voor uw Azure front-deur-aanbod bronnen. Service tags kunnen worden gebruikt in plaats van specifieke IP-adressen bij het maken van beveiligings regels. Door de servicetag naam (AzureFrontDoor. frontend, AzureFrontDoor. back-end, AzureFrontDoor. FirstParty) op te geven in het juiste bron-of doel veld van een regel, kunt u het verkeer voor de bijbehorende service toestaan of weigeren. 

Micro soft beheert de adres voorvoegsels die zijn opgenomen in het servicetag van de service en werkt de servicetag automatisch bij met gewijzigde adressen.

- [Service Tags begrijpen en gebruiken](../virtual-network/service-tags-overview.md)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

## <a name="identity-management"></a>Identiteitsbeheer

*Zie [Azure Security Bench Mark: Identity Management](/azure/security/benchmarks/security-controls-v2-identity-management)(Engelstalig) voor meer informatie.*

### <a name="im-6-restrict-azure-resource-access-based-on-conditions"></a>Chat-6: toegang tot Azure-bronnen beperken op basis van voor waarden

**Richt lijnen**: standaard reageert Azure front deur op alle gebruikers aanvragen, ongeacht waar de aanvraag vandaan komt. Klanten kunnen de toegang tot hun webtoepassingen ook beperken op basis van landen of regio's. Met de Web Application Firewall-service in azure front deur kunnen klanten een beleid definiëren met behulp van aangepaste toegangs regels, voor een specifiek pad op hun eind punt, voor het toestaan of blok keren van de toegang vanuit opgegeven landen of regio's.

- [Zelf studie-een WAF-beleid voor geo-filtering instellen voor uw Azure-deur](front-door-tutorial-geo-filtering.md)

**Azure Security Center bewaking**: momenteel niet beschikbaar

**Verantwoordelijkheid**: klant

## <a name="privileged-access"></a>Privileged Access

*Zie [Azure Security Bench Mark: Privileged Access](/azure/security/benchmarks/security-controls-v2-privileged-access)(Engelstalig) voor meer informatie.*

### <a name="pa-2-restrict-administrative-access-to-business-critical-systems"></a>PA-2: beheer toegang beperken tot essentiële bedrijfs systemen

**Hulp**: Azure front deur maakt gebruik van Azure RBAC (op rollen gebaseerd toegangs beheer) voor het isoleren van toegang tot bedrijfs kritieke systemen. Gebruik Azure RBAC om accounts te beperken die privileged toegang krijgen tot de abonnementen en beheer groepen, waar ze zich bevinden.

Zorg voor beperkte toegang tot beheer-, identiteits-en beveiligings systemen met beheerders toegang tot bedrijfskritische systemen, zoals Active Directory-domein-controllers, beveiligings hulpprogramma's en Systeembeheer hulpprogramma's. Alle soorten toegangs beheer voor een continue en consistente implementatie uitlijnen op uw strategie voor bedrijfs segmenten.

- [Azure-onderdelen en referentie model](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151) 

- [Toegang tot beheer groepen](../governance/management-groups/overview.md#management-group-access) 

- [Beheerders van Azure-abonnementen](../cost-management-billing/manage/add-change-subscription-administrator.md)

**Azure Security Center bewaking**: Ja

**Verantwoordelijkheid**: gedeeld

### <a name="pa-6-use-privileged-access-workstations"></a>PA-6: bevoorrechte toegang gebruiken werk stations

**Richt lijnen**: beveiligde, geïsoleerde werk stations zijn van cruciaal belang voor de beveiliging van gevoelige rollen als beheerders, ontwikkel aars en essentiële service operators. 

Gebruik werk stations met zeer beveiligde gebruikers met Azure Bastion voor beheer taken. Kies Azure Active Directory (Azure AD), micro soft Defender Advanced Threat Protection (ATP) en Microsoft Intune voor het implementeren van beveiligde en beheerde gebruikers werkstations voor beheer taken. De beveiligde werk stations moeten centraal worden beheerd voor het afdwingen van beveiligde configuratie, waaronder sterke authenticatie, software-en hardware-basis lijnen, beperkte logische en netwerk toegang.

- [Meer informatie over privileged Access workstations](../active-directory/devices/concept-azure-managed-workstation.md) 

- [Een privileged Access-werk station implementeren](../active-directory/devices/howto-azure-managed-workstation.md)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="pa-7-follow-just-enough-administration-least-privilege-principle"></a>PA-7: Volg gewoon voldoende beheer (minimale bevoegdheids methode) 

**Hulp**: Azure front deur is geïntegreerd met op rollen gebaseerd toegangs beheer (Azure RBAC) van Azure voor het beheren van de bijbehorende resources. Met Azure RBAC kunt u toegang tot Azure-resources beheren via roltoewijzingen. U kunt deze rollen toewijzen aan gebruikers, groeperingen van service-principals en beheerde identiteiten. Er zijn vooraf gedefinieerde ingebouwde rollen voor bepaalde resources, en deze rollen kunnen worden geïnventariseerd of opgevraagd via hulpprogram ma's als Azure CLI, Azure PowerShell of de Azure Portal. 

Volg het minimale privileges model voor op rollen gebaseerde machtigingen die zijn toegewezen aan resources met Azure RBAC en zorg ervoor dat ze zijn gebaseerd op zakelijke behoeften. Dit is een aanvulling op de just-in-time (JIT) benadering van Azure AD Privileged Identity Management (PIM) en moet regel matig worden gecontroleerd.

Gebruik ingebouwde rollen om machtigingen toe te wijzen en alleen aangepaste rollen te maken op basis van bedrijfs vereisten.

- [Wat is Azure Role-based Access Control (Azure RBAC)](../role-based-access-control/overview.md) 

- [RBAC configureren in azure](../role-based-access-control/role-assignments-portal.md) 

- [Azure AD-identiteits-en toegangs beoordelingen gebruiken](../active-directory/governance/access-reviews-overview.md)

**Azure Security Center bewaking**: Ja

**Verantwoordelijkheid**: gedeeld

## <a name="data-protection"></a>Gegevensbeveiliging

*Zie [Azure Security Bench Mark: Data Protection](/azure/security/benchmarks/security-controls-v2-data-protection)voor meer informatie.*

### <a name="dp-4-encrypt-sensitive-information-in-transit"></a>DP-4: gevoelige gegevens tijdens de overdracht versleutelen

**Richt lijnen**: als aanvulling op de toegangs controle, moeten gegevens in transit worden beschermd tegen buiten-band-aanvallen (zoals het vastleggen van verkeer) met behulp van versleuteling om ervoor te zorgen dat aanvallers de gegevens niet eenvoudig kunnen lezen of wijzigen.

De front-deur ondersteunt TLS-versies 1,0, 1,1 en 1,2. TLS 1,3 wordt nog niet ondersteund. Voor alle profielen voor de voorste deur die na september 2019 worden gemaakt, wordt TLS 1,2 als standaard minimum gebruikt.

Hoewel dit optioneel is voor verkeer op particuliere netwerken, is dit van cruciaal belang voor verkeer op externe en open bare netwerken. Voor HTTP-verkeer moet u ervoor zorgen dat clients die verbinding maken met uw Azure-resources, TLS v 1.2 of hoger kunnen onderhandelen. Voor extern beheer gebruikt u SSH (voor Linux) of RDP/TLS (voor Windows) in plaats van een niet-versleuteld protocol. Verouderde SSL-, TLS-en SSH-versies en-protocollen en zwakke cijfers moeten worden uitgeschakeld.

Azure biedt standaard versleuteling voor gegevens in transit tussen Azure-data centers.

- [Zelf studie-HTTPS configureren op een aangepast domein voor de voor deur](front-door-custom-domain-https.md)

- [Meer informatie over versleuteling in transit met Azure](../security/fundamentals/encryption-overview.md#encryption-of-data-in-transit) 

- [Informatie over TLS-beveiliging](/security/engineering/solving-tls1-problem) 

- [Dubbele versleuteling voor Azure-gegevens in transit](../security/fundamentals/double-encryption.md#data-in-transit)

**Azure Security Center bewaking**: Ja

**Verantwoordelijkheid**: gedeeld

## <a name="asset-management"></a>Asset-management

*Zie [Azure Security Bench Mark: Asset Management](/azure/security/benchmarks/security-controls-v2-asset-management)voor meer informatie.*

### <a name="am-1-ensure-security-team-has-visibility-into-risks-for-assets"></a>AM-1: controleren of het beveiligings team inzicht heeft in Risico's voor assets

**Richt lijnen**: Zorg ervoor dat aan beveiligings teams machtigingen voor beveiligings lezers worden verleend in uw Azure-Tenant en abonnementen zodat ze beveiligings Risico's kunnen bewaken met behulp van Azure Security Center. 

Afhankelijk van de manier waarop de verantwoordelijkheden van het beveiligings team zijn gestructureerd, kan bewaking voor beveiligings Risico's de verantwoordelijkheid zijn van een centraal beveiligings team of een lokaal team. Beveiligings inzichten en-risico's moeten echter altijd centraal worden geaggregeerd in een organisatie. 

Machtigingen voor beveiligings lezers kunnen breed worden toegepast op een hele Tenant (hoofd beheer groep) of in het bereik van beheer groepen of specifieke abonnementen. 

Opmerking: deze aanvullende machtigingen zijn mogelijk vereist voor zicht baarheid in werk belastingen en services. 

- [Overzicht van de rol van beveiligings lezer](../role-based-access-control/built-in-roles.md#security-reader)

- [Overzicht van Azure Beheergroepen](../governance/management-groups/overview.md)

**Azure Security Center bewaking**: momenteel niet beschikbaar

**Verantwoordelijkheid**: klant

### <a name="am-2-ensure-security-team-has-access-to-asset-inventory-and-metadata"></a>AM-2: controleren of het beveiligings team toegang heeft tot de inventaris en meta gegevens van de Asset

**Richt lijnen**: Tags Toep assen op Azure-resources, resource groepen en abonnementen om ze logisch in een taxonomie te organiseren. Elke tag bestaat uit een naam en een waardepaar. U kunt bijvoorbeeld de naam ' environment ' en de waarde ' production ' toep assen op alle resources in de productie omgeving.

- [Query's maken met Azure Resource Graph Explorer](../governance/resource-graph/first-query-portal.md) 

- [Azure Security Center Asset Inventory Management](../security-center/asset-inventory.md) 

- [Handleiding voor beslissingen over taggen en naamgeving voor resources](https://docs.microsoft.com/azure/cloud-adoption-framework/decision-guides/resource-tagging/?toc=/azure/azure-resource-manager/management/toc.json)

**Azure Security Center bewaking**: Ja

**Verantwoordelijkheid**: klant

### <a name="am-3-use-only-approved-azure-services"></a>AM-3: alleen goedgekeurde Azure-Services gebruiken

**Hulp**: gebruik Azure Policy om te controleren welke Services gebruikers in uw omgeving kunnen inrichten en beperken. Vraag en Zoek resources op in hun abonnementen met Azure resource Graph.

Gebruik Azure Monitor om regels te maken voor het activeren van waarschuwingen wanneer een niet-goedgekeurde service wordt gedetecteerd.

- [Azure Policy configureren en beheren](../governance/policy/tutorials/create-and-manage.md) 

- [Een specifiek resource type weigeren met Azure Policy](../governance/policy/samples/built-in-policies.md#general) 

- [Query's maken met Azure Resource Graph Explorer](../governance/resource-graph/first-query-portal.md)

**Azure Security Center bewaking**: Ja

**Verantwoordelijkheid**: klant

### <a name="am-4-ensure-security-of-asset-lifecycle-management"></a>AM-4: zorg voor de beveiliging van levenscyclus beheer van middelen

**Richt lijn**: het is de verantwoordelijkheid van de klant om kenmerken en netwerk configuraties van Azure front-deur activa te onderhouden die als hoge impact worden beschouwd.

Het is raadzaam dat de klant een proces maakt om het kenmerk en de wijzigingen in de netwerk configuratie vast te leggen, de wijzigings invloed te meten en herstel taken te maken, indien van toepassing.

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

## <a name="logging-and-threat-detection"></a>Logboek registratie en detectie van bedreigingen

*Zie [Azure Security Bench Mark: Logging and Threat Detection](/azure/security/benchmarks/security-controls-v2-logging-threat-detection)(Engelstalig) voor meer informatie.*

### <a name="lt-3-enable-logging-for-azure-network-activities"></a>LT-3: logboek registratie inschakelen voor Azure-netwerk activiteiten

**Richt lijnen**: Azure Frond endnetwerkverbindingener is niet bedoeld voor implementatie in virtuele netwerken; door deze klanten kan stroom registratie door de netwerk beveiligings groep niet worden ingeschakeld, verkeer via een firewall worden doorgestuurd of pakket opnames worden uitgevoerd.

Azure front deur registreert al het netwerk verkeer dat IT voor de toegang van klanten verwerkt. Schakel de functie netwerk stroom Logboeken in en configureer deze logboeken om te worden verzonden naar een opslag account voor lange termijn retentie en controle.

- [Zelf studie-metrische gegevens en logboeken voor controle instellen in azure front-deur](front-door-diagnostics.md)

**Azure Security Center bewaking**: momenteel niet beschikbaar

**Verantwoordelijkheid**: gedeeld

### <a name="lt-4-enable-logging-for-azure-resources"></a>LT-4: logboek registratie inschakelen voor Azure-resources

**Hulp**: activiteiten logboeken, die automatisch beschikbaar zijn, bevatten alle schrijf bewerkingen (put, post, Delete) voor uw Azure front-deur bronnen, met uitzonde ring van Lees bewerkingen (Get). Activiteiten logboeken kunnen worden gebruikt om een fout te vinden bij het oplossen van problemen of om te controleren hoe een gebruiker in uw organisatie een resource heeft gewijzigd.

Schakel Azure-resource Logboeken in voor Azure front-deur. U kunt Azure Security Center en Azure Policy gebruiken om resource logboeken en het verzamelen van logboek gegevens in te scha kelen. Deze logboeken kunnen essentieel zijn voor het later onderzoeken van beveiligings incidenten en het uitvoeren van forensische-oefeningen.

- [Platform logboeken en metrische gegevens verzamelen met Azure Monitor](../azure-monitor/platform/diagnostic-settings.md) 

- [Logboek registratie en verschillende logboek typen in azure](../azure-monitor/platform/platform-logs-overview.md) 

- [Meer informatie over het verzamelen van Azure Security Center gegevens](../security-center/security-center-enable-data-collection.md)

**Azure Security Center bewaking**: Ja

**Verantwoordelijkheid**: gedeeld

## <a name="incident-response"></a>Reageren op incidenten

*Zie [Azure Security Bench Mark: Incident Response](/azure/security/benchmarks/security-controls-v2-incident-response)(Engelstalig) voor meer informatie.*

### <a name="ir-1-preparation--update-incident-response-process-for-azure"></a>IR-1: voor bereiding-respons proces van incidenten bijwerken voor Azure

**Hulp**: Zorg ervoor dat uw organisatie processen heeft gedefinieerd om te reageren op beveiligings incidenten. Controleer of deze processen zijn bijgewerkt voor Azure-resources en deze regel matig testen om te zorgen voor gereedheid.

- [Implementeer beveiliging in de bedrijfs omgeving](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

- [Naslag Gids voor respons op incidenten](/microsoft-365/downloads/IR-Reference-Guide.pdf)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="ir-2-preparation--setup-incident-notification"></a>IR-2: voor bereiding-incident melding instellen

**Richt lijnen**: contact gegevens voor beveiligings incidenten instellen in azure Security Center. Deze contact gegevens worden door micro soft gebruikt om contact met u op te nemen als het micro soft Security Response Center (MSRC) detecteert dat uw gegevens zijn geopend door een onrecht matige of niet-gemachtigde partij. U hebt ook opties voor het aanpassen van incident waarschuwingen en meldingen in verschillende Azure-Services op basis van de behoeften van uw incident respons. 

- [De Azure Security Center Security-contact persoon instellen](../security-center/security-center-provide-security-contact-details.md)

**Azure Security Center bewaking**: Ja

**Verantwoordelijkheid**: klant

### <a name="ir-3-detection-and-analysis--create-incidents-based-on-high-quality-alerts"></a>IR-3: detectie en analyse: incidenten maken op basis van waarschuwingen van hoge kwaliteit

**Richt lijnen**: Zorg ervoor dat u een proces hebt om waarschuwingen van hoge kwaliteit te maken en de kwaliteit van waarschuwingen te meten. Zo kunt u lessen uit eerdere incidenten ontdekken en waarschuwingen voor analisten prioriteiten geven, zodat ze geen tijd verspillen op valse positieven. 

Waarschuwingen van hoge kwaliteit kunnen worden gebouwd op basis van ervaringen van eerdere incidenten, gevalideerde community-bronnen en hulpprogram ma's die zijn ontworpen om waarschuwingen te genereren en op te schonen door diverse signaal bronnen te weigeren en te correleren. 

Azure Security Center biedt waarschuwingen van hoge kwaliteit in veel Azure-assets. U kunt de ASC data connector gebruiken om de waarschuwingen naar Azure Sentinel te streamen. Met Azure Sentinel kunt u geavanceerde waarschuwings regels maken om incidenten automatisch te genereren voor een onderzoek. 

Exporteer uw Azure Security Center waarschuwingen en aanbevelingen met behulp van de export functie om Risico's voor Azure-resources te identificeren. Waarschuwingen en aanbevelingen hand matig of op een doorlopende manier exporteren.

- [Exporteren configureren](../security-center/continuous-export.md)

- [Waarschuwingen streamen naar Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Azure Security Center bewaking**: momenteel niet beschikbaar

**Verantwoordelijkheid**: klant

### <a name="ir-4-detection-and-analysis--investigate-an-incident"></a>IR-4: detectie en analyse: een incident onderzoeken

**Richt lijnen**: Zorg ervoor dat analisten verschillende gegevens bronnen kunnen opvragen en gebruiken bij het onderzoeken van mogelijke incidenten om een volledig overzicht te maken van wat er is gebeurd. Diverse logboek typen moeten worden verzameld om de activiteiten van een mogelijke aanvaller in de Kill-keten bij te houden om blinde vlekken te voor komen.  Zorg ervoor dat inzichten en informatie worden vastgelegd voor andere analisten en voor toekomstige historische Naslag informatie.  

De gegevens bronnen voor onderzoek bevatten de gecentraliseerde logboek registratie bronnen die al worden verzameld van de services binnen het bereik en het uitvoeren van systemen, maar kan ook het volgende omvatten:

- Netwerk gegevens: gebruik stroom logboeken voor netwerk beveiligings groepen, Azure Network Watcher en Azure Monitor om netwerk stroom logboeken en andere analyse-informatie vast te leggen. 

- Moment opnamen van actieve systemen: 

    - Gebruik de functie voor moment opnamen van de virtuele machine van Azure om een moment opname te maken van de schijf waarop het systeem wordt uitgevoerd. 

    - Gebruik de systeem eigen geheugen dump functie van het besturings systeem om een moment opname te maken van het actieve systeem geheugen.

    - Gebruik de functie snap shot van de Azure-Services of de eigen mogelijkheid van uw software om moment opnamen van de actieve systemen te maken.

Azure Sentinel biedt uitgebreide gegevens analyse voor vrijwel elke logboek bron en een case beheer Portal om de volledige levens duur van incidenten te beheren. Informatie over Intelligence tijdens een onderzoek kan worden gekoppeld aan een incident voor het bijhouden en rapporteren van de doel einden. 

- [Moment opname maken van de schijf van een Windows-computer](../virtual-machines/windows/snapshot-copy-managed-disk.md)

- [Moment opname maken van de schijf van een Linux-machine](../virtual-machines/linux/snapshot-copy-managed-disk.md)

- [Microsoft Azure ondersteuning voor diagnostische gegevens en geheugen dump verzameling](https://azure.microsoft.com/support/legal/support-diagnostic-information-collection/) 

- [Incidenten onderzoeken met Azure Sentinel](../sentinel/tutorial-investigate-cases.md)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="ir-5-detection-and-analysis--prioritize-incidents"></a>IR-5: detectie en analyse: de prioriteit van incidenten bepalen

**Richt lijnen**: Geef een context voor analisten waarop incidenten zich richten op basis van ernst van de waarschuwing en de gevoeligheid van een activa. 

Azure Security Center wijst aan elke waarschuwing een Ernst toe om u te helpen bepalen welke waarschuwingen het eerst moeten worden onderzocht. De ernst is gebaseerd op de manier waarop vertrouwen Security Center is in de zoek actie of het analyse programma dat wordt gebruikt om de waarschuwing te geven, evenals het betrouwbaarheids niveau waarvan er sprake is van schadelijke opzet achter de activiteit die tot de waarschuwing heeft geleid.

Daarnaast kunt u resources markeren met behulp van tags en een naamgevings systeem maken om Azure-resources te identificeren en categoriseren, met name voor de verwerking van gevoelige gegevens.  Het is uw verantwoordelijkheid om prioriteit te geven aan het herstel van waarschuwingen op basis van de ernst van de Azure-resources en-omgeving waar het incident heeft plaatsgevonden.

- [Beveiligingswaarschuwingen in Azure Security Center](../security-center/security-center-alerts-overview.md)

- [Tags gebruiken om Azure-resources te organiseren](/azure/azure-resource-manager/resource-group-using-tags)

**Azure Security Center bewaking**: momenteel niet beschikbaar

**Verantwoordelijkheid**: klant

### <a name="ir-6-containment-eradication-and-recovery--automate-the-incident-handling"></a>IR-6: insluiting, uitroeiing en herstel: de verwerking van incidenten automatiseren

**Richt lijnen**: Automatiseer hand matige terugkerende taken om de reactie tijd te versnellen en de overhead voor analisten te verlagen. Het uitvoeren van hand matige taken duurt langer om uit te voeren, elk incident te vertragen en te verminderen hoeveel incidenten een analist kan verwerken. Hand matige taken verg Roten ook analisten intensief, waardoor het risico van menselijke fout wordt verg root dat vertragingen veroorzaakt, en de mogelijkheid van analisten om effectief te best Eden aan complexe taken te degraderen. Gebruik werk stroom automatiserings functies in Azure Security Center en Azure Sentinel om automatisch acties te activeren of een Playbook uit te voeren om te reageren op binnenkomende beveiligings waarschuwingen. De Playbook neemt acties, zoals het verzenden van meldingen, het uitschakelen van accounts en het isoleren van problematische netwerken. 

- [Werk stroom automatisering configureren in Security Center](../security-center/workflow-automation.md)

- [Automatische reacties op dreigingen instellen in Azure Security Center](../security-center/tutorial-security-incident.md#triage-security-alerts)

- [Automatische bedreigings reacties instellen in azure Sentinel](../sentinel/tutorial-respond-threats-playbook.md)

**Azure Security Center bewaking**: momenteel niet beschikbaar

**Verantwoordelijkheid**: klant

## <a name="posture-and-vulnerability-management"></a>Postuur en beveiligings beheer

*Zie voor meer informatie de [Azure Security Bench Mark: postuur en het beveiligings beleid](/azure/security/benchmarks/security-controls-v2-posture-vulnerability-management).*

### <a name="pv-3-establish-secure-configurations-for-compute-resources"></a>PV-3: veilige configuraties voor reken bronnen instellen

**Hulp**: gebruik Azure Security Center en Azure Policy om beveiligde configuraties te maken voor alle reken resources, waaronder virtual machines, containers en meer.

- [Azure Security Center aanbevelingen bewaken](../security-center/security-center-recommendations.md) 

- [Aanbevelingen voor beveiliging: een naslaggids](../security-center/recommendations-reference.md)

**Azure Security Center bewaking**: momenteel niet beschikbaar

**Verantwoordelijkheid**: gedeeld

### <a name="pv-7-rapidly-and-automatically-remediate-software-vulnerabilities"></a>HW-7: snel en automatisch problemen met software oplossen

**Hulp**: software-updates snel implementeren om software problemen in besturings systemen en toepassingen op te lossen.

Volg prioriteiten een gemeen schappelijke risico Score programma (bijvoorbeeld gemeen schappelijke restrictie systeem voor beveiligings problemen) of de standaard risico classificaties die worden ondersteund door een scan programma van derden dat u gebruikt. en pas uw omgeving aan met behulp van context van welke toepassingen een hoog beveiligings risico opleveren en waarvoor een hoge uptime nodig is.

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="pv-8-conduct-regular-attack-simulation"></a>PV-8: regel matige aanvals simulatie uitvoeren

**Richt lijnen**: u kunt zo nodig indringings tests of rode team activiteiten uitvoeren op uw Azure-resources en ervoor zorgen dat alle essentiële beveiligings resultaten worden hersteld.
Volg de Microsoft Cloud regels voor het testen van de indringings fase om ervoor te zorgen dat uw indringings tests niet worden geschonden door het micro soft-beleid. Gebruik de strategie van micro soft en de uitvoering van de implementatie van de indringing van een live site in de Cloud, services en toepassingen die door micro soft worden beheerd.

- [Indringings tests in azure](../security/fundamentals/pen-testing.md)

- [Regels van betrokkenheid voor het testen van indringing](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Microsoft Cloud rode teams](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: gedeeld

## <a name="governance-and-strategy"></a>Governance en strategie

*Zie [Azure Security Bench Mark: governance en strategie](/azure/security/benchmarks/security-controls-v2-governance-strategy)voor meer informatie.*

### <a name="gs-1-define-asset-management-and-data-protection-strategy"></a>GS-1: de strategie voor Asset Management en gegevens bescherming definiëren 

**Richt lijnen**: Zorg ervoor dat u een duidelijke strategie documenteert en communiceert voor continue bewaking en bescherming van systemen en gegevens. Volg prioriteiten voor detectie, beoordeling, beveiliging en bewaking van bedrijfs kritieke gegevens en systemen. 

Deze strategie moet gedocumenteerde richt lijnen, beleid en standaarden bevatten voor de volgende elementen: 

-   Standaard gegevens classificatie in overeenstemming met de zakelijke Risico's

-   Zicht baarheid van beveiligings organisaties in Risico's en activa-inventaris 

-   Goed keuring van beveiligings organisaties van Azure-Services voor gebruik 

-   Beveiliging van assets via hun levens cyclus

-   Vereiste strategie voor toegangs beheer in overeenstemming met organisatie gegevens classificatie

-   Gebruik van functies voor gegevens bescherming van Azure native en derden

-   Gegevens versleutelings vereisten voor in-transit-en rest-use cases

-   Juiste cryptografische normen

Meer informatie vindt u op de koppelingen waarnaar wordt verwezen.

- [Aanbeveling van Azure-beveiligings architectuur-opslag, gegevens en versleuteling](https://docs.microsoft.com/azure/architecture/framework/security/storage-data-encryption?toc=/security/compass/toc.json&amp;bc=/security/compass/breadcrumb/toc.json)

- [Basis beginselen van Azure-beveiliging-Azure-gegevens beveiliging,-versleuteling en-opslag](../security/fundamentals/encryption-overview.md)

- [Azure Security Bench Mark-gegevens beveiliging](/azure/security/benchmarks/security-controls-v2-data-protection)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="gs-2-define-enterprise-segmentation-strategy"></a>GS-2: ondernemings segmentatie strategie definiëren 

**Richt lijnen**: Stel een bedrijfs strategie in om de toegang tot assets te segmenteren met een combi natie van identiteits-, netwerk-, toepassings-, abonnements-, beheer groep-en andere besturings elementen.

Houd de nood zaak van beveiligings scheiding zorgvuldig bij met de nood zaak om de dagelijkse werking van de systemen in te scha kelen die met elkaar moeten communiceren en toegang hebben tot gegevens.

Zorg ervoor dat de segmentatie strategie consistent wordt geïmplementeerd in alle controle typen, zoals netwerk beveiliging, identiteits-en toegangs modellen, en toepassings machtigingen/toegangs modellen en besturings elementen voor menselijke processen.

- [Richt lijnen voor segmentatie strategie in azure (video)](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151)

- [Richt lijnen voor segmentatie strategie in azure (document)](/security/compass/governance#enterprise-segmentation-strategy)

- [Netwerk segmentatie uitlijnen met strategie voor bedrijfs segmentatie](/security/compass/network-security-containment#align-network-segmentation-with-enterprise-segmentation-strategy)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="gs-3-define-security-posture-management-strategy"></a>GS-3: beveiligings strategie voor postuur-beheer definiëren

**Hulp**: regel matig de Risico's voor uw afzonderlijke assets en de omgeving waarin ze worden gehost. Volg prioriteiten voor hoogwaardige en zeer belichte aanvallen, zoals gepubliceerde toepassingen, netwerk binnenkomend en uitgevende punten, gebruikers-en beheerders eindpunten, enzovoort.

- [Azure Security Bench Mark-postuur en beveiligings beheer](/azure/security/benchmarks/security-controls-v2-posture-vulnerability-management)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="gs-4-align-organization-roles-responsibilities-and-accountabilities"></a>GS-4: organisatie rollen, verantwoordelijkheden en accountabilities uitlijnen

**Richt lijnen**: Zorg ervoor dat u een duidelijke strategie voor rollen en verantwoordelijkheden in uw beveiligings organisatie documenteert en communiceert. Volg prioriteiten voor een duidelijke verantwoordelijkheid voor beveiligings beslissingen, het trainen van iedereen op het gedeelde verantwoordelijkheids model en technische teams op technologie om de cloud te beveiligen.

- [Best Practice voor Azure-beveiliging 1: personen: teams trainen in Cloud Security traject](/azure/cloud-adoption-framework/security/security-top-10#1-people-educate-teams-about-the-cloud-security-journey)

- [Aanbevolen beveiligings procedure voor Azure-gebruikers: teams in de Cloud-beveiligings technologie trainen](/azure/cloud-adoption-framework/security/security-top-10#2-people-educate-teams-on-cloud-security-technology)

- [Best Practice voor Azure-beveiliging 3-proces: verantwoordelijkheid toewijzen voor Cloud beveiligings beslissingen](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="gs-5-define-network-security-strategy"></a>GS-5: netwerk beveiligings strategie definiëren

**Richt lijnen**: Stel een Azure-netwerk beveiligings benadering in als onderdeel van de algehele strategie voor het toegangs beheer van uw organisatie.  

Deze strategie moet gedocumenteerde richt lijnen, beleid en standaarden bevatten voor de volgende elementen: 

-   Gecentraliseerd netwerk beheer en beveiligings verantwoordelijkheid

-   Segment model voor virtuele netwerken dat is afgestemd op de strategie voor bedrijfs segmentatie

-   Herstel strategie in verschillende scenario's voor bedreigingen en aanvallen

-   Internet-en ingangs-en uitgangs strategie

-   Hybride Cloud en on-premises interconnectiviteit-strategie

-   Up-to-date netwerk beveiligings artefacten (zoals netwerk diagrammen, referentie netwerk architectuur)

Meer informatie vindt u op de koppelingen waarnaar wordt verwezen.

- [Aanbevolen procedures voor Azure-beveiliging 11-architectuur. Eén uniforme beveiligings strategie](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Azure Security Bench Mark-netwerk beveiliging](/azure/security/benchmarks/security-controls-v2-network-security)

- [Overzicht van Azure-netwerk beveiliging](../security/fundamentals/network-overview.md)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="gs-6-define-identity-and-privileged-access-strategy"></a>GS-6: identiteits-en bevoorrechte toegangs strategie definiëren

**Richt lijnen**: Stel een Azure Identity and privileged Access benaderingen in als onderdeel van de algehele strategie voor beveiligings beheer van uw organisatie.  

Deze strategie moet gedocumenteerde richt lijnen, beleid en standaarden bevatten voor de volgende elementen: 

-   Een gecentraliseerd identiteits-en verificatie systeem en de interconnectiviteit daarvan met andere interne en externe identiteits systemen

-   Sterke authenticatie methoden in verschillende use-cases en-voor waarden

-   Beveiliging van gebruikers met hoge bevoegdheden

-   Bewaking en verwerking van afwijkende gebruikers activiteiten  

-   Beoordelings-en afstemmings proces voor gebruikers-id en-toegang

Zie voor meer informatie de volgende verwijzingen:

- [Azure Security Bench Mark-Identity Management](/azure/security/benchmarks/security-controls-v2-identity-management)

- [Azure Security-benchmark-privileged Access](/azure/security/benchmarks/security-controls-v2-privileged-access)

- [Aanbevolen procedures voor Azure-beveiliging 11-architectuur. Eén uniforme beveiligings strategie](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Overzicht van Azure Identity Management-beveiliging](../security/fundamentals/identity-management-overview.md)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

### <a name="gs-7-define-logging-and-threat-response-strategy"></a>GS-7: vastleggen van de logboek registratie en de reactie op bedreigingen

**Richt lijnen**: Stel een strategie voor logboek registratie en reactie op bedreigingen in om bedreigingen snel te detecteren en op te lossen terwijl aan nalevings vereisten wordt voldaan. Volg prioriteiten om analisten te voorzien van waarschuwingen met hoge kwaliteit en naadloze ervaring, zodat ze zich kunnen concentreren op bedreigingen in plaats van integratie en hand matige stappen. 

Deze strategie moet gedocumenteerde richt lijnen, beleid en standaarden bevatten voor de volgende elementen: 

-   De rol en verantwoordelijkheden van de organisatie voor beveiligings bewerkingen (SecOps) 

-   Een goed gedefinieerd respons proces voor incidenten dat wordt afgestemd op het NIST of een ander branche raamwerk 

-   Vastleggen en bewaren in Logboeken voor ondersteuning van detectie van bedreigingen, reacties op incidenten en nalevings behoeften

-   Gecentraliseerde zicht baarheid van en correlatie-informatie over bedreigingen, met behulp van SIEM, systeem eigen Azure-mogelijkheden en andere bronnen 

-   Communicatie-en meldings abonnement met uw klanten, leveranciers en open bare partijen

-   Gebruik van Azure native en platforms van derden voor het verwerken van incidenten, zoals logboek registratie en detectie van bedreigingen, forensische, herstel en uitroeiing van aanvallen

-   Processen voor het verwerken van incidenten en activiteiten na incidenten, zoals geleerde lessen en bewijs behoud

Meer informatie vindt u op de koppelingen waarnaar wordt verwezen.

- [Azure Security Bench Mark-logboek registratie en detectie van bedreigingen](/azure/security/benchmarks/security-controls-v2-logging-threat-detection)

- [Azure Security Bench Mark-incident respons](/azure/security/benchmarks/security-controls-v2-incident-response)

- [Aanbevolen procedure voor Azure-beveiliging 4-proces. Reactie processen voor incidenten bijwerken voor Cloud](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

- [Hand leiding Azure-acceptatie raamwerk, logboek registratie en rapportage](/azure/cloud-adoption-framework/decision-guides/logging-and-reporting)

**Azure Security Center bewaking**: niet van toepassing

**Verantwoordelijkheid**: klant

## <a name="next-steps"></a>Volgende stappen

- Zie [overzicht van Azure Security Bench Mark v2](/azure/security/benchmarks/overview)
- Meer informatie over [Azure-beveiligings basislijnen](/azure/security/benchmarks/security-baselines-overview)
