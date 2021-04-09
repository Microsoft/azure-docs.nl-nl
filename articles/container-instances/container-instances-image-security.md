---
title: Beveiligings overwegingen voor container instanties
description: Aanbevelingen voor het beveiligen van installatie kopieën en geheimen voor Azure Container Instances en algemene veiligheids overwegingen voor elk container platform
ms.topic: article
ms.date: 01/10/2020
ms.custom: ''
ms.openlocfilehash: cbceeea24501bc9815cb07e1b0a054914ba8e964
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100579274"
---
# <a name="security-considerations-for-azure-container-instances"></a>Beveiligings overwegingen voor Azure Container Instances

In dit artikel worden beveiligings overwegingen geïntroduceerd voor het gebruik van Azure Container Instances om container-apps uit te voeren. Dit zijn een aantal van de onderwerpen:

> [!div class="checklist"]
> * **Beveiligings aanbevelingen** voor het beheren van installatie kopieën en geheimen voor Azure container instances
> * **Overwegingen voor het container ecosysteem** gedurende de levens cyclus van de container, voor elk container platform

Zie de [Azure-beveiligings basislijn voor container instances](security-baseline.md)voor uitgebreide aanbevelingen waarmee u de beveiligings postuur van uw implementatie kunt verbeteren.


## <a name="security-recommendations-for-azure-container-instances"></a>Beveiligings aanbevelingen voor Azure Container Instances

### <a name="use-a-private-registry"></a>Een persoonlijk REGI ster gebruiken

Containers worden gemaakt van installatiekopieën die zijn opgeslagen in een of meer opslagplaatsen. Deze opslag plaatsen kunnen deel uitmaken van een openbaar REGI ster, zoals [docker hub](https://hub.docker.com)of een persoonlijk REGI ster. Een voorbeeld van een persoonlijk register is de [Docker Trusted Registry](https://docs.docker.com/datacenter/dtr/), dat lokaal kan worden geïnstalleerd of in een virtuele privécloud. U kunt ook in de cloud gebaseerde persoonlijke container register Services gebruiken, met inbegrip van [Azure container Registry](../container-registry/container-registry-intro.md). 

Een openbaar beschik bare container installatie kopie biedt geen garantie voor beveiliging. Container installatie kopieën bestaan uit meerdere software lagen en elke software-laag heeft mogelijk beveiligings problemen. Om het risico van aanvallen te verminderen, moet u installatie kopieën opslaan en ophalen uit een persoonlijk REGI ster, zoals Azure Container Registry of een vertrouwd REGI ster van de docker. Naast het bieden van een beheerd persoonlijk REGI ster, Azure Container Registry ondersteunt [verificatie op basis van de Service-Principal](../container-registry/container-registry-authentication.md) via Azure Active Directory voor basis verificatie stromen. Deze verificatie omvat op rollen gebaseerde toegang voor alleen-lezen (pull), write (push) en andere machtigingen.

### <a name="monitor-and-scan-container-images"></a>Container installatie kopieën controleren en scannen

Profiteer van oplossingen om container installatie kopieën in een persoonlijk REGI ster te scannen en mogelijke beveiligings problemen te identificeren. Het is belang rijk om inzicht te krijgen in de diepte van de detectie van bedreigingen die de verschillende oplossingen bieden.

Azure Container Registry kan bijvoorbeeld optioneel worden [geïntegreerd met Azure Security Center](../security-center/defender-for-container-registries-introduction.md) , zodat alle Linux-installatie kopieën die naar een REGI ster zijn gepusht, automatisch worden gescand. De geïntegreerde Qualys scanner van Azure Security Center detecteert beveiligings lekken van de installatie kopie, classificeert deze en biedt richt lijnen voor herstel.

De beveiligings bewaking en oplossingen voor het scannen van afbeeldingen, zoals [Twistlock](https://azuremarketplace.microsoft.com/marketplace/apps/twistlock.twistlock?tab=Overview) en de licht [blauwe beveiliging](https://azuremarketplace.microsoft.com/marketplace/apps/aqua-security.aqua-security?tab=Overview) , zijn ook beschikbaar via Azure Marketplace.  

### <a name="protect-credentials"></a>Referenties beveiligen

Containers kunnen worden verspreid over verschillende clusters en Azure-regio's. Daarom moet u de referenties beveiligen die vereist zijn voor aanmeldingen of API-toegang, zoals wacht woorden of tokens. Zorg ervoor dat alleen bevoegde gebruikers toegang hebben tot deze containers tijdens de overdracht en in rust. Inventariseer alle referentie geheimen en vraag ontwikkel aars om nieuwe geheimen te gebruiken-beheer Programma's die zijn ontworpen voor container platforms.  Zorg ervoor dat uw oplossing versleutelde data bases, TLS-versleuteling voor geheimen gegevens in transit en minimale bevoegdheden op [Azure Role-based Access Control (Azure RBAC)](../role-based-access-control/overview.md)bevat. [Azure Key Vault](../key-vault/general/secure-your-key-vault.md) is een Cloud service die versleutelings sleutels en geheimen (zoals certificaten, verbindings reeksen en wacht woorden) beveiligt voor container toepassingen. Omdat deze gegevens gevoelig en zakelijk kritiek zijn, moet u beveiligde toegang tot uw sleutel kluizen zodat alleen geautoriseerde toepassingen en gebruikers toegang hebben.

## <a name="considerations-for-the-container-ecosystem"></a>Overwegingen voor het container ecosysteem

De volgende beveiligings maatregelen, die effectief zijn geïmplementeerd en worden beheerd, kunnen u helpen uw container ecosysteem te beveiligen en beveiligen. Deze maat regelen zijn van toepassing in de levens cyclus van de container, van ontwikkeling tot productie-implementatie en tot een reeks container orchestrator, hosts en platformen. 

### <a name="use-vulnerability-management-as-part-of-your-container-development-lifecycle"></a>Beveiligings problemen gebruiken als onderdeel van de levens cyclus voor de ontwikkeling van containers 

Door gebruik te maken van effectief beveiligings beheer tijdens de levens cyclus voor de ontwikkeling van de container, verbetert u de conflicteert die u hebt gevonden en opgelost om beveiligings problemen te voor komen voordat ze een ernstiger probleem worden. 

### <a name="scan-for-vulnerabilities"></a>Controleren op beveiligings problemen 

Er zijn voortdurend nieuwe beveiligings lekken gedetecteerd, waardoor het scannen en identificeren van beveiligings problemen een doorlopend proces is. Integreer het scannen van beveiligings problemen tijdens de levens cyclus van de container:

* Als laatste controle in uw ontwikkelings pijplijn moet u een beveiligings controle voor containers uitvoeren voordat u de installatie kopieën naar een openbaar of persoonlijk REGI ster pusht. 
* Ga door met het scannen van container installatie kopieën in het REGI ster om fouten te identificeren die tijdens de ontwikkeling zijn gemist en om nieuwe beveiligings problemen op te lossen die mogelijk aanwezig zijn in de code die wordt gebruikt in de container installatie kopieën.  

### <a name="map-image-vulnerabilities-to-running-containers"></a>Problemen met de installatie kopie toewijzen aan het uitvoeren van containers 

U moet een manier hebben voor het toewijzen van beveiligings problemen die in container installatie kopieën worden aangegeven voor het uitvoeren van containers, waardoor beveiligings problemen kunnen worden verholpen of opgelost.  

### <a name="ensure-that-only-approved-images-are-used-in-your-environment"></a>Zorg ervoor dat alleen goedgekeurde installatie kopieën in uw omgeving worden gebruikt 

Er is voldoende wijziging en volatiliteit in een container ecosysteem zonder dat er ook onbekende containers worden toegestaan. Alleen goedgekeurde container installatie kopieën toestaan. Beschikken over hulpprogram ma's en processen voor het bewaken en voor komen van het gebruik van niet-goedgekeurde container installatie kopieën. 

Een efficiënte manier om de kwets baarheid te beperken en te voor komen dat ontwikkel aars kritieke beveiligings fouten kunnen aanbrengen, is het beheren van de stroom van container installatie kopieën in uw ontwikkel omgeving. U kunt bijvoorbeeld een Linux-distributie gebruiken als basis installatie kopie, bij voor keur voor een Lean (alpine of CoreOS in plaats van Ubuntu), om het Opper vlak voor mogelijke aanvallen te minimaliseren. 

Het hand tekening-of vingerafdruk proces van een afbeelding kan een keten van bewaring bieden waarmee u de integriteit van de containers kunt controleren. Azure Container Registry biedt bijvoorbeeld ondersteuning voor het [vertrouwens](https://docs.docker.com/engine/security/trust/content_trust) model van de docker-functie, waarmee installatie kopieën kunnen worden ondertekend die naar een REGI ster worden gepusht en installatie kopieën van gebruikers om alleen ondertekende installatie kopieën op te halen.

### <a name="permit-only-approved-registries"></a>Alleen goedgekeurde registers toestaan 

Een uitbrei ding om ervoor te zorgen dat uw omgeving alleen goedgekeurde installatie kopieën gebruikt, is alleen het gebruik van goedgekeurde container registers toe te staan. Het vereisen van het gebruik van goedgekeurde container registers vermindert de risico beperking van de Risico's door het potentieel te beperken van de introductie van onbekende beveiligings lekken of beveiligings problemen. 

### <a name="ensure-the-integrity-of-images-throughout-the-lifecycle"></a>Zorg voor de integriteit van installatie kopieën gedurende de hele levens cyclus 

Onderdeel van het beheer van de beveiliging in de hele levens cyclus van de container is het garanderen van de integriteit van de container installatie kopieën in het REGI ster en wanneer ze worden gewijzigd of geïmplementeerd in de productie omgeving. 

* Installatie kopieën met beveiligings problemen, zelfs kleine, mogen niet worden uitgevoerd in een productie omgeving. In het ideale geval moeten alle installatie kopieën die in productie zijn geïmplementeerd, worden opgeslagen in een persoonlijk REGI ster dat toegankelijk is voor een aantal selecteren. Bewaar het aantal productie-installatie kopieën klein om ervoor te zorgen dat ze effectief kunnen worden beheerd.

* Omdat het moeilijk is om de oorsprong van de software van een openbaar beschik bare container installatie kopie te herkennen, moet u installatie kopieën van de bron maken om te zorgen voor kennis van de oorsprong van de laag. Wanneer er een beveiligingsprobleem aan het licht komt in een zelf gebouwde containerinstallatiekopie, kunnen klanten sneller een oplossing vinden. Met een open bare installatie kopie moeten klanten de hoofdmap van een open bare installatie kopie vinden om deze te herstellen of een andere veilige installatie kopie van de uitgever te verkrijgen. 

* Een grondig gescande afbeelding die in productie is geïmplementeerd, is niet gegarandeerd up-to-date voor de levens duur van de toepassing. Er kunnen beveiligingsproblemen worden gerapporteerd voor lagen van de installatiekopie die niet bekend waren of die na de implementatie naar productie zijn geïntroduceerd. 

  Controleer periodiek de installatie kopieën die zijn geïmplementeerd in de productie om te identificeren dat de installatie kopieën zijn verouderd of niet zijn bijgewerkt. U kunt Blue-groen implementatie methodologieën en rolling upgrade mechanismen gebruiken om container installatie kopieën bij te werken zonder uitval tijd. U kunt afbeeldingen scannen met de hulpprogram ma's die in de voor gaande sectie worden beschreven. 

* Gebruik een CI-pijp lijn (continue integratie) met geïntegreerde beveiligings scans om veilige installatie kopieën te bouwen en deze naar uw persoonlijke REGI ster te pushen. De ingebouwde scanfunctie voor beveiligingsproblemen van de CI-oplossing zorgt ervoor dat installatiekopieën die voor alle tests slagen, naar het persoonlijke register worden gepusht van waaruit productieworkloads worden geïmplementeerd. 

  Een CI-pijplijn fout zorgt ervoor dat kwets bare installatie kopieën niet worden gepusht naar het persoonlijke REGI ster dat wordt gebruikt voor implementaties van productie-workloads. Ook wordt het scannen van afbeeldingen automatisch geautomatiseerd als er sprake is van een groot aantal installatie kopieën. Het handmatig controleren van installatiekopieën op beveiligingsproblemen kan erg langdurig en foutgevoelig zijn. 

### <a name="enforce-least-privileges-in-runtime"></a>Minimale bevoegdheden afdwingen tijdens runtime 

Het concept van minimale bevoegdheden is een basis beveiligings best practice die ook van toepassing is op containers. Als er misbruik wordt gemaakt van een beveiligings probleem, geeft het over het algemeen toegang tot de aanvaller en de bevoegdheden die gelijk zijn aan die van de beschadigde toepassing of het verwerkte proces. Om ervoor te zorgen dat containers met de laagste bevoegdheden en toegang die nodig zijn om de taak uit te voeren, vermindert u de bloot stelling aan risico. 

### <a name="reduce-the-container-attack-surface-by-removing-unneeded-privileges"></a>Verminder de kwets baarheid voor de container door overbodige bevoegdheden te verwijderen 

U kunt ook de mogelijke kwets baarheid minimaliseren door ongebruikte of overbodige processen of bevoegdheden uit de container runtime te verwijderen. Geprivilegieerde containers worden uitgevoerd als root. Als een kwaadwillende gebruiker of werk belasting een escape-teken in een geprivilegieerde container heeft, wordt de container vervolgens als hoofdmap op dat systeem uitgevoerd.

### <a name="preapprove-files-and-executables-that-the-container-is-allowed-to-access-or-run"></a>Bestands-en uitvoer bare bestanden die de container mag openen of uitvoeren, vóór goed keuren 

Het verminderen van het aantal variabelen of onbekende waarden helpt u bij het onderhouden van een stabiele, betrouw bare omgeving. Het beperken van containers zodat ze alleen voorgoedgekeurde of safelisted-bestanden kunnen openen of uitvoeren, is een bewezen methode om bloot stelling aan Risico's te beperken.  

Het is veel eenvoudiger om een Safelist te beheren wanneer het vanaf het begin wordt geïmplementeerd. Een Safelist biedt een meting van de controle-en beheer baarheid, terwijl u leert welke bestanden en bestands uitvoer vereist zijn om de toepassing correct te laten functioneren. 

Een Safelist vermindert niet alleen de kwets baarheid van aanvallen, maar kan ook een basis lijn voor afwijkingen bieden en voor komen dat de gebruiks voorbeelden van de ' ruislijke buur ' en container groepen-scenario's worden verzorgd. 

### <a name="enforce-network-segmentation-on-running-containers"></a>Netwerk segmentatie afdwingen op actieve containers  

Als u containers in één subnet wilt beveiligen tegen beveiligings Risico's in een ander subnet, houdt u netwerk segmentatie (of nano segmentering) of schei ding tussen actieve containers bij. Het onderhouden van netwerk segmentering kan ook nodig zijn voor het gebruik van containers in branches die nodig zijn om te voldoen aan nalevings mandaat.  

Het hulp programma partner licht [blauw](https://azuremarketplace.microsoft.com/marketplace/apps/aqua-security.aqua-security?tab=Overview) biedt bijvoorbeeld een geautomatiseerde benadering van nano segmentatie. Hiermee worden container netwerk activiteiten bewaakt in runtime. Hiermee worden alle binnenkomende en uitgaande netwerk verbindingen geïdentificeerd van/naar andere containers, services, IP-adressen en het open bare Internet. Nano segmentatie wordt automatisch gemaakt op basis van bewaakt verkeer. 

### <a name="monitor-container-activity-and-user-access"></a>Container activiteit en gebruikers toegang bewaken 

Net als bij elke IT-omgeving moet u de activiteit en gebruikers toegang tot uw container ecosysteem consistent controleren om snel verdachte of schadelijke activiteiten te identificeren. Azure biedt oplossingen voor container bewaking, waaronder:

* [Azure monitor voor containers](../azure-monitor/containers/container-insights-overview.md) bewaakt de prestaties van uw workloads die zijn geïmplementeerd op Kubernetes-omgevingen die worden gehost op Azure Kubernetes service (AKS). Azure Monitor voor containers biedt u inzicht in de prestaties door het verzamelen van geheugen-en processor metrieken van controllers, knoop punten en containers die beschikbaar zijn in Kubernetes via de API voor metrische gegevens. 

* Met de [Azure container monitoring-oplossing](../azure-monitor/containers/containers.md) kunt u andere docker-en Windows-container-hosts op één locatie weer geven en beheren. Bijvoorbeeld:

  * Gedetailleerde controle-informatie weer geven met opdrachten die worden gebruikt met containers. 
  * Problemen met containers oplossen door gecentraliseerde logboeken te bekijken en te zoeken zonder dat u op afstand docker of Windows-hosts hoeft weer te geven.  
  * Zoek containers die ruis kunnen veroorzaken en overtollige resources op een host gebruiken.
  * Bekijk gecentraliseerde gegevens over CPU, geheugen, opslag en netwerk gebruik en prestaties voor containers.  

  De oplossing ondersteunt container orchestrator, waaronder docker Swarm, DC/OS, onbeheerd Kubernetes, Service Fabric en Red Hat open SHIFT. 

### <a name="monitor-container-resource-activity"></a>Container resource activiteit bewaken 

Bewaak uw resource activiteit, zoals bestanden, netwerk en andere bronnen die uw containers benaderen. Het bewaken van de activiteit en het verbruik van resources is handig voor het bewaken van de prestaties en als beveiligings maatregel. 

[Azure monitor](../azure-monitor/overview.md) maakt kern bewaking voor Azure-Services mogelijk door het verzamelen van metrische gegevens, activiteiten logboeken en diagnostische Logboeken toe te staan. Het activiteitenlogboek geeft bijvoorbeeld aan wanneer nieuwe resources worden gemaakt of gewijzigd. 

  Er zijn metrische gegevens beschikbaar met prestatiestatistieken voor verschillende resources en zelfs het besturingssysteem binnen een virtuele machine. U kunt deze gegevens bekijken met een van de verkenners in de Azure-portal en waarschuwingen maken op basis van deze metrische gegevens. Azure Monitor biedt de snelste pijplijn voor metrische gegevens (vijf minuten tot één minuut); gebruik het dus voor waarschuwingen en meldingen waarbij tijd van cruciaal belang is. 

### <a name="log-all-container-administrative-user-access-for-auditing"></a>Alle container gebruikers toegang voor controle voor auditing vastleggen 

Behoud een nauw keurige controle spoor van beheerders toegang tot uw container ecosysteem, met inbegrip van uw Kubernetes-cluster, container register en container installatie kopieën. Deze logboeken kunnen nood zakelijk zijn voor controle doeleinden en kunnen worden gebruikt als forensische-materiaal na een beveiligings incident. Azure-oplossingen omvatten:

* [Integratie van de Azure Kubernetes-service met Azure Security Center](../security-center/defender-for-kubernetes-introduction.md) om de beveiligings configuratie van de cluster omgeving te controleren en aanbevelingen voor beveiliging te genereren
* [Azure container bewakings oplossing](../azure-monitor/containers/containers.md)
* Resource logboeken voor [Azure container instances](container-instances-log-analytics.md) en [Azure container Registry](../container-registry/container-registry-diagnostics-audit-logs.md)

## <a name="next-steps"></a>Volgende stappen

* Raadpleeg de [Azure Security Baseline voor container instances](security-baseline.md) voor uitgebreide aanbevelingen waarmee u de beveiligings postuur van uw implementatie kunt verbeteren.

* Meer informatie over het gebruik van [Azure Security Center](../security-center/container-security.md) voor realtime detectie van bedreigingen in uw container omgevingen.

* Meer informatie over het beheren van container beveiligings problemen met oplossingen van [Twistlock](https://www.twistlock.com/solutions/microsoft-azure-container-security/) en de licht [blauw](https://www.aquasec.com/solutions/azure-container-security/).