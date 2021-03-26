---
title: Azure-beveiligings basislijn voor Cloud Shell
description: De Cloud Shell Security Baseline voorziet in procedure richtlijnen en resources voor het implementeren van de beveiligings aanbevelingen die zijn opgegeven in de Azure Security-Bench Mark.
author: msmbaldwin
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 02/17/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: b0045eacea7fdc13abdad35b5567eba3e994dcf9
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2021
ms.locfileid: "105558870"
---
# <a name="azure-security-baseline-for-cloud-shell"></a>Azure-beveiligings basislijn voor Cloud Shell

In deze beveiligings basislijn worden richt lijnen van de [Azure Security Bench Mark-versie 1,0](../security/benchmarks/overview-v1.md) ingesteld op Cloud shell. De Azure Security-benchmark biedt aanbevelingen voor hoe u uw cloudoplossingen in Azure kunt beveiligen.
De inhoud wordt gegroepeerd op de **beveiligings controles** die zijn gedefinieerd door de Azure Security-benchmark en de bijbehorende richt lijnen die van toepassing zijn op Cloud shell. **Besturings elementen** die niet van toepassing zijn op Cloud shell, zijn uitgesloten.

 
Als u wilt zien hoe Cloud Shell volledig is toegewezen aan de beveiligings benchmark van Azure, raadpleegt u het [volledige Cloud shell beveiligings basislijn toewijzings bestand](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Netwerkbeveiliging

*Zie [Azure Security Benchmark: netwerkbeveiliging](../security/benchmarks/security-control-network-security.md) voor meer informatie.*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: Azure-resources in virtuele netwerken beveiligen

**Hulp**: klanten kunnen Azure Cloud shell implementeren in een Virtual Network van een klant.

Wanneer u Azure Cloud Shell implementeert in een Virtual Network van een klant, moet u een bestaand virtueel netwerk maken of gebruiken. Zorg ervoor dat het gekozen virtuele netwerk een netwerk beveiligings groep heeft die is toegepast op de subnetten en netwerk toegangscontrole besturings elementen die specifiek zijn geconfigureerd voor de vertrouwde poorten en bronnen van uw toepassing.

- [Cloud Shell implementeren in een virtueel Azure-netwerk](private-vnet.md)

- [Een netwerk beveiligings groep met beveiligings regels maken](../virtual-network/tutorial-filter-network-traffic.md)

- [Azure Firewall implementeren en configureren](../firewall/tutorial-firewall-deploy-portal.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

## <a name="identity-and-access-control"></a>Identiteits- en toegangsbeheer

*Zie [Azure Security Bench Mark: Identity and Access Control](../security/benchmarks/security-control-identity-access-control.md)voor meer informatie.*

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3,4: eenmalige aanmelding (SSO) met Azure Active Directory gebruiken

**Richt lijnen**: Azure Cloud shell is een op een browser gebaseerde opdracht regel ervaring waarbij gebruik wordt gemaakt van dezelfde autorisatie die wordt gebruikt voor toegang tot de Azure Portal. in dit geval wordt een SSO in de Azure Portal ook geverifieerd met Cloud shell.

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: multi-factor Authentication gebruiken voor alle op Azure Active Directory gebaseerde toegang

**Richt lijnen**: Azure Cloud shell is een op een browser gebaseerde opdracht regel ervaring waarbij gebruik wordt gemaakt van dezelfde autorisatie die wordt gebruikt voor toegang tot de Azure Portal. in dit geval moet u multi-factor Authentication die vereist is voor het maken van een verbinding met de Azure Portal, ook vereist zijn voor Cloud shell.

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

## <a name="vulnerability-management"></a>Beheer van beveiligingsproblemen

*Zie voor meer informatie de [Azure Security Bench Mark: beveiligingslek beheer](../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5,1: automatische hulpprogram ma's voor het scannen van beveiligings problemen uitvoeren

**Hulp**: Azure Cloud shell is een op een browser gebaseerde opdracht regel ervaring die wordt gebruikt voor interactief beheer van cloud resources.  Elke klant container is een kortstondige nieuwe container die voor elke sessie wordt gebruikt.  De container installatie kopieën worden bewaakt en bijgewerkt door het Cloud Shell team.

Met Azure Cloud Shell kunnen klanten hun eigen hulpprogram ma's of software installeren in hun eigen installatie kopie volgens hun organisatorische behoeften.

Klanten zijn verantwoordelijk voor het uitvoeren van geautomatiseerde hulpprogram ma's voor het scannen van problemen met software die in de omgeving wordt uitgevoerd.

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="53-deploy-an-automated-patch-management-solution-for-third-party-software-titles"></a>5,3: een geautomatiseerde oplossing voor patch beheer implementeren voor titels van software van derden

**Richt lijnen**: niet van toepassing; Azure Cloud Shell is een op een browser gebaseerde opdracht regel ervaring die wordt gebruikt voor interactief beheer van cloud resources.  Elke klant container is een kortstondige nieuwe container die voor elke sessie wordt gebruikt.  De container installatie kopieën worden bewaakt en bijgewerkt door het Cloud Shell team.

Met Azure Cloud Shell kunnen klanten hun eigen hulpprogram ma's of software installeren in hun eigen installatie kopie volgens hun organisatorische behoeften.

Klanten zijn verantwoordelijk voor het beheer van software patches die in hun omgeving worden uitgevoerd.

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5,4: vergelijken van back-to-back-problemen

**Hulp**: Azure Cloud shell is een op een browser gebaseerde opdracht regel ervaring die wordt gebruikt voor interactief beheer van cloud resources.  Elke klant container is een kortstondige nieuwe container die voor elke sessie wordt gebruikt.  De container installatie kopieën worden bewaakt en bijgewerkt door het Cloud Shell team.

Met Azure Cloud Shell kunnen klanten hun eigen hulpprogram ma's of software installeren in hun eigen installatie kopie volgens hun organisatorische behoeften.

Klanten zijn verantwoordelijk voor het oplossen van beveiligings problemen die worden gedetecteerd via de scan van het software beveiligings probleem. Scan resultaten worden met consistente intervallen uitgevoerd en vergelijken de resultaten met eerdere scans om te controleren of beveiligings problemen zijn opgelost. Wanneer u aanbevelingen voor beveiligings beheer gebruikt die worden voorgesteld door Azure Security Center, kunt u in de portal van de geselecteerde oplossing draaien om historische scan gegevens weer te geven.

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5,5: een risico classificatie proces gebruiken om prioriteit te geven aan het herstel van ontdekte beveiligings problemen

**Hulp**: Azure Cloud shell is een op een browser gebaseerde opdracht regel ervaring die wordt gebruikt voor interactief beheer van cloud resources.  Elke klant container is een kortstondige nieuwe container die voor elke sessie wordt gebruikt.  De container installatie kopieën worden bewaakt en bijgewerkt door het Cloud Shell team.

Met Azure Cloud Shell kunnen klanten hun eigen hulpprogram ma's of software installeren in hun eigen installatie kopie volgens hun organisatorische behoeften.

Klanten zijn verantwoordelijk voor het oplossen van beveiligings problemen die worden gedetecteerd via de scan van het software beveiligings probleem.  Gebruik een gemeen schappelijke risico Score programma (bijvoorbeeld gemeen schappelijke restrictie systeem voor beveiligings problemen) of de standaard risico classificaties die worden meegeleverd met het hulp programma van derden. 

- [NIST-publicatie-algemene punten voor beveiligings problemen](https://www.nist.gov/publications/common-vulnerability-scoring-system)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

## <a name="inventory-and-asset-management"></a>Inventarisatie en Asset Management

*Zie [Azure Security Bench Mark: Inventory and Asset Management](../security/benchmarks/security-control-inventory-asset-management.md)voor meer informatie.*

### <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6,4: een inventaris van goedgekeurde Azure-resources definiëren en onderhouden

**Hulp**: Azure Cloud shell is een op een browser gebaseerde opdracht regel ervaring die wordt gebruikt voor interactief beheer van cloud resources.  Elke klant container is een kortstondige nieuwe container die voor elke sessie wordt gebruikt.  De container installatie kopieën en hulpprogram ma's worden bewaakt en bijgewerkt door het Cloud Shell team.  De klant kan hun eigen hulpprogram ma's installeren in hun eigen installatie kopie op basis van de behoeften van de organisatie en de hulpprogram ma's hebben geen `sudo` machtigingen nodig tijdens de installatie.

Klanten wordt aangeraden een inventaris te maken van goedgekeurde software die via Azure Cloud Shell conform uw organisatie behoeften is geïnstalleerd.

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6,6: monitor voor niet-goedgekeurde software toepassingen binnen reken resources

**Hulp**: Azure Cloud shell is een gratis service zonder activa van de klant.  De container installatie kopieën en hulpprogram ma's worden bewaakt en bijgewerkt door het Cloud Shell team. 

Met Azure Cloud Shell kunnen klanten hun eigen hulpprogram ma's of software installeren in hun eigen installatie kopie volgens hun organisatorische behoeften.

Klanten zijn verantwoordelijk voor het bewaken van software toepassingen die in de omgeving worden uitgevoerd om ervoor te zorgen dat ze per organisatie beleid worden goedgekeurd.

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6,7: niet-goedgekeurde Azure-resources en software toepassingen verwijderen

**Hulp**: Azure Cloud shell is een gratis service zonder activa van de klant.  De container installatie kopieën en hulpprogram ma's worden bewaakt en bijgewerkt door het Cloud Shell team. 

Met Azure Cloud Shell kunnen klanten hun eigen hulpprogram ma's of software installeren in hun eigen installatie kopie volgens hun organisatorische behoeften.

Klanten zijn verantwoordelijk voor het bewaken van software toepassingen die in de omgeving worden uitgevoerd om ervoor te zorgen dat niet-goedgekeurde software per organisatie beleid wordt beheerd.

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="68-use-only-approved-applications"></a>6,8: alleen goedgekeurde toepassingen gebruiken

**Hulp**: Azure Cloud shell is een gratis service zonder activa van de klant.  De container installatie kopieën en hulpprogram ma's worden bewaakt en bijgewerkt door het Cloud Shell team.  Specifieke hulpprogram ma's kunnen niet worden verwijderd door de klant.

Met Azure Cloud Shell kunnen klanten hun eigen hulpprogram ma's of toepassingen installeren in hun eigen installatie kopie op basis van hun organisatorische behoeften.

Klanten zijn verantwoordelijk voor het bewaken van toepassingen die in de omgeving worden uitgevoerd om er zeker van te zijn dat ze per organisatie beleid zijn goedgekeurd.

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6,10: een inventaris van goedgekeurde software titels onderhouden

**Richt lijnen**: niet van toepassing; Azure Cloud Shell is een op een browser gebaseerde opdracht regel ervaring die wordt gebruikt voor interactief beheer van cloud resources.  Elke klant container is een kortstondige nieuwe container die voor elke sessie wordt gebruikt.  De container installatie kopieën worden bewaakt en bijgewerkt door het Cloud Shell team.

Met Azure Cloud Shell kunnen klanten hun eigen hulpprogram ma's of software installeren in hun eigen installatie kopie volgens hun organisatorische behoeften.

Klanten zijn verantwoordelijk voor het onderhouden van een inventaris van goedgekeurde software die in de omgeving wordt uitgevoerd om er zeker van te zijn dat ze goedgekeurde software per organisatie beleid zijn.

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="612-limit-users-ability-to-execute-scripts-in-compute-resources"></a>6,12: de mogelijkheid van gebruikers om scripts in reken bronnen uit te voeren, beperken

**Hulp**: Azure Cloud shell is een op een browser gebaseerde opdracht regel ervaring die wordt gebruikt voor interactief beheer van cloud resources.  Acties die worden uitgevoerd binnen Cloud Shell werken hetzelfde als acties uit dezelfde hulpprogram ma's of talen die worden uitgevoerd in een lokale omgeving.  Acties van afzonderlijke hulpprogram ma's en talen moeten worden beperkt, klanten kunnen de toegang tot Cloud Shell niet beperken of beperken wat er beschikbaar is voor een gebruiker.

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6,13: toepassingen met een hoog risico fysiek of logisch scheiden

**Hulp**: Azure Cloud shell kunnen worden geïsoleerd in een virtueel netwerk van de klant.

- [Cloud Shell implementeren in een virtueel Azure-netwerk](private-vnet.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

## <a name="secure-configuration"></a>Veilige configuratie

*Zie [Azure Security Bench Mark: Secure Configuration](../security/benchmarks/security-control-secure-configuration.md)(Engelstalig) voor meer informatie.*

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: onbedoelde referentie blootstelling elimineren

**Hulp**: met Cloud shell kunt u scripts uitvoeren in, ontwerpen en uploaden naar de Cloud shell omgeving.  Het verplaatsen van referenties naar Azure Key Vault is onze aanbeveling.

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

## <a name="malware-defense"></a>Beveiliging tegen malware

*Zie voor meer informatie de [Azure Security Bench Mark: beveiliging tegen schadelijke software](../security/benchmarks/security-control-malware-defense.md).*

### <a name="81-use-centrally-managed-antimalware-software"></a>8,1: centraal beheerde antimalware-software gebruiken

**Hulp**: Azure Cloud shell is een op een browser gebaseerde opdracht regel ervaring die wordt gebruikt voor interactief beheer van cloud resources.  Elke klant container is een kortstondige nieuwe container die voor elke sessie wordt gebruikt.  De container installatie kopieën en hulpprogram ma's worden bewaakt en bijgewerkt door het Cloud Shell team.  De klant kan hun eigen hulpprogram ma's installeren in hun eigen installatie kopie op basis van de behoeften van de organisatie en de hulpprogram ma's hebben geen `sudo` machtigingen nodig tijdens de installatie.

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="83-ensure-antimalware-software-and-signatures-are-updated"></a>8,3: controleren of antimalware-software en hand tekeningen zijn bijgewerkt

**Hulp**: Azure Cloud shell is een op een browser gebaseerde opdracht regel ervaring die wordt gebruikt voor interactief beheer van cloud resources.  Elke klant container is een kortstondige nieuwe container die voor elke sessie wordt gebruikt.  De container installatie kopieën en hulpprogram ma's worden bewaakt en bijgewerkt door het Cloud Shell team.  De klant kan hun eigen hulpprogram ma's installeren in hun eigen installatie kopie op basis van de behoeften van de organisatie en de hulpprogram ma's hebben geen `sudo` machtigingen nodig tijdens de installatie.

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

## <a name="incident-response"></a>Reageren op incidenten

*Zie [Azure Security Benchmark: respons op incidenten](../security/benchmarks/security-control-incident-response.md) voor meer informatie.*

### <a name="101-create-an-incident-response-guide"></a>10,1: een hand leiding voor reactie op incidenten maken

**Richtlijnen**: Stel voor uw organisatie een responshandleiding op voor gebruik bij incidenten. Zorg ervoor dat er schriftelijke responsplannen zijn waarin alle rollen van het personeel worden gedefinieerd, evenals alle fasen in het afhandelen/managen van incidenten, vanaf de detectie van het incident tot een evaluatie ervan achteraf.

- [Werk stroom automatisering configureren in Azure Security Center](../security-center/security-center-planning-and-operations-guide.md) 

- [Richt lijnen voor het bouwen van uw eigen beveiligings incident antwoord proces](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/) 

- [Micro soft Security Response Center anatomie van een incident](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/) 

- [Klant kan ook gebruikmaken van de hand leiding voor de verwerking van het computer beveiligings incident van het NIST om hulp te bieden bij het maken van een eigen incident respons plan](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: een beoordelings procedure voor incidenten en prioriteits procedures maken

**Hulp**: Security Center wijst aan elke waarschuwing een Ernst toe om u te helpen bepalen welke waarschuwingen het eerst moeten worden onderzocht. De ernst is gebaseerd op de manier waarop vertrouwen Security Center zich in de zoek actie bevindt of de analyse die wordt gebruikt om de waarschuwing te geven, evenals het betrouwbaarheids niveau dat er schadelijke intentie is achter de activiteit die tot de waarschuwing heeft geleid.

Daarnaast kunt u ook duidelijk abonnementen markeren (voor bijvoorbeeld productie, niet-productie) en maak een naamgevings systeem om Azure-resources duidelijk te identificeren en te categoriseren.

- [Beveiligingswaarschuwingen in Azure Security Center](../security-center/security-center-alerts-overview.md) 

- [Tags gebruiken om Azure-resources te organiseren](../azure-resource-manager/management/tag-resources.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="103-test-security-response-procedures"></a>10,3: procedures voor beveiligings antwoorden testen

**Richt lijnen**: oefent oefeningen uit om de respons mogelijkheden van uw systeem op een gewone uitgebracht te testen. Stel vast waar zich zwakke plekken en hiaten bevinden, en wijzig zo nodig het plan.

- [Raadpleeg de publicatie van het NIST: hand leiding voor het testen, trainen en uitoefenen van Program Ma's voor IT-plannen en-mogelijkheden](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: contact gegevens van het beveiligings incident opgeven en waarschuwings meldingen configureren voor beveiligings incidenten

**Hulp**: contact gegevens van beveiligings incidenten worden door micro soft gebruikt om contact met u op te nemen als het micro soft Security Response Center (MSRC) detecteert dat de gegevens van de klant zijn geopend door een onrecht matige of niet-gemachtigde partij. Bekijk incidenten na het feit om te controleren of de problemen zijn opgelost.

- [De Azure Security Center Security-contact persoon instellen](../security-center/security-center-provide-security-contact-details.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: beveiligings waarschuwingen opnemen in uw reactie systeem van uw incident

**Richt lijnen**: uw Azure Security Center waarschuwingen en aanbevelingen exporteren met de functie continue export. Met doorlopend exporteren kunt u waarschuwingen en aanbevelingen hand matig of op een doorlopende manier exporteren. U kunt de Azure Security Center Data Connector gebruiken om de waarschuwingen naar Azure Sentinel te streamen.

- [Continue export configureren](../security-center/continuous-export.md) 

- [Waarschuwingen streamen naar Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

### <a name="106-automate-the-response-to-security-alerts"></a>10,6: de reactie op beveiligings waarschuwingen automatiseren

**Hulp**: gebruik de functie werk stroom automatisering in azure Security Center om automatisch reacties te activeren via ' Logic apps ' in beveiligings waarschuwingen en aanbevelingen.

- [Werk stroom automatisering en Logic Apps configureren](../security-center/workflow-automation.md)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

## <a name="penetration-tests-and-red-team-exercises"></a>Penetratietests en Red Team-oefeningen

*Zie [Azure Security Bench Mark: Indringings tests en rode team oefeningen](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md)voor meer informatie.*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11,1: voert regel matig indringings tests van uw Azure-resources uit en zorgt voor herbemiddeling van alle essentiële beveiligings resultaten

**Richt lijnen**: Volg de stappen voor het testen van de Microsoft Cloud indringings regels om ervoor te zorgen dat uw indringings tests niet worden geschonden door het micro soft-beleid. Gebruik de strategie van Microsoft en de uitvoering van Red Teaming-activiteiten, en voer een penetratietest van de live site uit op basis van een infrastructuur, services en toepassingen die door Microsoft worden beheerd.

- [Regels voor het inzetten van penetratietests](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Microsoft Cloud Red Teaming](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Verantwoordelijkheid**: Klant

**Azure Security Center bewaking**: geen

## <a name="next-steps"></a>Volgende stappen

- Zie [Overzicht Azure Security Benchmark V2](../security/benchmarks/overview.md)
- Meer informatie over [Azure-beveiligingsbasislijnen](../security/benchmarks/security-baselines-overview.md)