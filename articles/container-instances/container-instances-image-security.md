---
title: Beveiligingsoverwegingen voor container instances
description: Aanbevelingen voor het beveiligen van afbeeldingen en geheimen voor Azure Container Instances en algemene beveiligingsoverwegingen voor elk containerplatform
ms.topic: article
ms.date: 01/10/2020
ms.custom: ''
ms.openlocfilehash: acccd79ecd1c216f92f19d1cf035682414cd17ca
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107750136"
---
# <a name="security-considerations-for-azure-container-instances"></a>Beveiligingsoverwegingen voor Azure Container Instances

In dit artikel worden beveiligingsoverwegingen voor het gebruik van Azure Container Instances om container-apps uit te voeren. Dit zijn een aantal van de onderwerpen:

> [!div class="checklist"]
> * **Beveiligingsaanbevelingen** voor het beheren van afbeeldingen en geheimen voor Azure Container Instances
> * **Overwegingen voor het containerecosysteem** gedurende de levenscyclus van de container, voor elk containerplatform

Zie de Azure-beveiligingsbasislijn voor meer informatie over het verbeteren van de beveiligingsstatus van [uw implementatie Container Instances.](security-baseline.md)


## <a name="security-recommendations-for-azure-container-instances"></a>Beveiligingsaanbevelingen voor Azure Container Instances

### <a name="use-a-private-registry"></a>Een privéregister gebruiken

Containers worden gemaakt van installatiekopieën die zijn opgeslagen in een of meer opslagplaatsen. Deze opslagplaatsen kunnen deel uitmaken van een openbaar register, zoals [Docker Hub](https://hub.docker.com)of een privéregister. Een voorbeeld van een persoonlijk register is de [Docker Trusted Registry](https://docs.docker.com/datacenter/dtr/), dat lokaal kan worden geïnstalleerd of in een virtuele privécloud. U kunt ook privécontainerregisterservices in de cloud gebruiken, waaronder [Azure Container Registry.](../container-registry/container-registry-intro.md) 

Een openbaar beschikbare containerafbeelding garandeert geen beveiliging. Container-afbeeldingen bestaan uit meerdere softwarelagen en elke softwarelaag kan beveiligingsproblemen hebben. Als u de dreiging van aanvallen wilt verminderen, moet u afbeeldingen opslaan en ophalen uit een persoonlijk register, zoals Azure Container Registry of Docker Trusted Registry. Naast het bieden van een beheerd privéregister, Azure Container Registry ondersteuning voor verificatie op basis van [service-principals](../container-registry/container-registry-authentication.md) via Azure Active Directory basisverificatiestromen. Deze verificatie omvat op rollen gebaseerde toegang voor alleen-lezen (pull), schrijven (push) en andere machtigingen.

### <a name="monitor-and-scan-container-images"></a>Containerafbeeldingen bewaken en scannen

Profiteer van oplossingen voor het scannen van containerafbeeldingen in een privéregister en het identificeren van mogelijke beveiligingsproblemen. Het is belangrijk om inzicht te krijgen in de diepte van de detectie van bedreigingen die de verschillende oplossingen bieden.

Zo kunt u Azure Container Registry optioneel integreren [met Azure Security Center](../security-center/defender-for-container-registries-introduction.md) om automatisch alle Linux-afbeeldingen te scannen die naar een register worden pushen. Azure Security Center de geïntegreerde Qualys-scanner detecteert beveiligingsproblemen van afbeeldingen, classificeert deze en biedt richtlijnen voor herstel.

Beveiligingsbewaking en scanoplossingen voor afbeeldingen, [zoals Twistlock](https://azuremarketplace.microsoft.com/marketplace/apps/twistlock.twistlock?tab=Overview) en [Aqua Security,](https://azuremarketplace.microsoft.com/marketplace/apps/aqua-security.aqua-security?tab=Overview) zijn ook beschikbaar via de Azure Marketplace.  

### <a name="protect-credentials"></a>Referenties beveiligen

Containers kunnen worden verdeeld over verschillende clusters en Azure-regio's. U moet dus referenties beveiligen die vereist zijn voor aanmeldingen of API-toegang, zoals wachtwoorden of tokens. Zorg ervoor dat alleen bevoegde gebruikers toegang hebben tot deze containers in transit en at-rest. Inventariseren van alle referentiegeheimen en vereisen dat ontwikkelaars opkomende hulpprogramma's voor geheimenbeheer gebruiken die zijn ontworpen voor containerplatforms.  Zorg ervoor dat uw oplossing versleutelde databases bevat, TLS-versleuteling voor geheime gegevens die worden verzonden en op rollen gebaseerd toegangsbeheer met de minste bevoegdheden [(Azure RBAC).](../role-based-access-control/overview.md) [Azure Key Vault](../key-vault/general/security-overview.md) is een cloudservice die versleutelingssleutels en geheimen (zoals certificaten, verbindingsreeksen en wachtwoorden) bewaarde voor toepassingen in containers. Omdat deze gegevens gevoelig en bedrijfskritiek zijn, is beveiligde toegang tot uw sleutelkluizen, zodat alleen geautoriseerde toepassingen en gebruikers er toegang toe hebben.

## <a name="considerations-for-the-container-ecosystem"></a>Overwegingen voor het containerecosysteem

De volgende beveiligingsmaatregelen, goed geïmplementeerd en effectief beheerd, kunnen u helpen uw containerecosysteem te beveiligen en te beveiligen. Deze metingen zijn van toepassing gedurende de levenscyclus van containers, van ontwikkeling tot productie-implementatie en voor een reeks container-orchestrators, hosts en platformen. 

### <a name="use-vulnerability-management-as-part-of-your-container-development-lifecycle"></a>Gebruik vulnerability management als onderdeel van de levenscyclus van uw containerontwikkeling 

Door effectieve beveiliging vulnerability management de levenscyclus van de containerontwikkeling te gebruiken, verbetert u de kans dat u beveiligingsproblemen identificeert en oplost voordat ze een ernstiger probleem worden. 

### <a name="scan-for-vulnerabilities"></a>Scannen op beveiligingsproblemen 

Er worden voortdurend nieuwe beveiligingsproblemen ontdekt, dus het zoeken naar en identificeren van beveiligingsproblemen is een continu proces. Scannen op beveiligingsleed opnemen in de levenscyclus van de container:

* Als laatste controle in uw ontwikkelingspijplijn moet u een beveiligingsscan uitvoeren op containers voordat u de afbeeldingen naar een openbaar of persoonlijk register pusht. 
* Ga door met het scannen van containerafbeeldingen in het register om eventuele fouten te identificeren die tijdens de ontwikkeling zijn gemist en om eventuele nieuwe beveiligingsproblemen op te pakken die mogelijk aanwezig zijn in de code die in de containerafbeeldingen wordt gebruikt.  

### <a name="map-image-vulnerabilities-to-running-containers"></a>Beveiligingsproblemen van afbeeldingen aan het uitvoeren van containers 

U moet over een manier voor het toewijzen van beveiligingsproblemen in containerafbeeldingen aan het uitvoeren van containers, zodat beveiligingsproblemen kunnen worden verholpen of opgelost.  

### <a name="ensure-that-only-approved-images-are-used-in-your-environment"></a>Zorg ervoor dat alleen goedgekeurde afbeeldingen worden gebruikt in uw omgeving 

Er is voldoende verandering en schommelingen in een containerecosysteem zonder ook onbekende containers toe te staan. Alleen goedgekeurde containerafbeeldingen toestaan. Er zijn hulpprogramma's en processen beschikbaar om het gebruik van niet-goedgekeurde containerafbeeldingen te controleren en te voorkomen. 

Een effectieve manier om het aanvalsoppervlak te verminderen en te voorkomen dat ontwikkelaars kritieke beveiligingsfouten maken, is door de stroom van containerafbeeldingen in uw ontwikkelomgeving te controleren. U kunt bijvoorbeeld één Linux-distributie als basisafbeelding goed te staan, bij voorkeur een die mager is (Alpine of CoreOS in plaats van Ubuntu), om de kans op mogelijke aanvallen te minimaliseren. 

Ondertekening van afbeeldingen of vingerafdrukken kan een controleketen bieden waarmee u de integriteit van de containers kunt controleren. Zo ondersteunt Azure Container Registry het inhoudsvertrouwensmodel van Docker, waarmee uitgevers van afbeeldingen afbeeldingen kunnen ondertekenen die naar een register worden pushen en gebruikers van afbeeldingen alleen ondertekende afbeeldingen kunnen pullen. [](https://docs.docker.com/engine/security/trust/content_trust)

### <a name="permit-only-approved-registries"></a>Alleen goedgekeurde registers toestaan 

Een uitbreiding om ervoor te zorgen dat uw omgeving alleen goedgekeurde afbeeldingen gebruikt, is om alleen het gebruik van goedgekeurde containerregisters toe te staan. Door het gebruik van goedgekeurde containerregisters te vereisen, vermindert u de blootstelling aan risico's door de kans op de introductie van onbekende beveiligingsproblemen of beveiligingsproblemen te beperken. 

### <a name="ensure-the-integrity-of-images-throughout-the-lifecycle"></a>De integriteit van afbeeldingen gedurende de levenscyclus garanderen 

Een onderdeel van het beheren van de beveiliging gedurende de levenscyclus van de container is het garanderen van de integriteit van de container-afbeeldingen in het register en wanneer deze worden gewijzigd of geïmplementeerd in productie. 

* Afbeeldingen met beveiligingsproblemen, zelfs kleine, mogen niet worden uitgevoerd in een productieomgeving. In het ideale moment moeten alle in productie geïmplementeerde afbeeldingen worden opgeslagen in een privéregister dat toegankelijk is voor enkele. Houd het aantal productie-afbeeldingen klein om ervoor te zorgen dat ze effectief kunnen worden beheerd.

* Omdat het moeilijk is om de oorsprong van software aan te wijzen op basis van een openbaar beschikbare containerafbeelding, kunt u afbeeldingen bouwen vanuit de bron om kennis van de oorsprong van de laag te garanderen. Wanneer er een beveiligingsprobleem aan het licht komt in een zelf gebouwde containerinstallatiekopie, kunnen klanten sneller een oplossing vinden. Met een openbare afbeelding moeten klanten de hoofdmap van een openbare afbeelding vinden om deze op te lossen of om een andere beveiligde afbeelding van de uitgever op te halen. 

* Een grondig gescande afbeelding die in productie is geïmplementeerd, is niet gegarandeerd up-to-date voor de levensduur van de toepassing. Er kunnen beveiligingsproblemen worden gerapporteerd voor lagen van de installatiekopie die niet bekend waren of die na de implementatie naar productie zijn geïntroduceerd. 

  Controleer regelmatig de afbeeldingen die in productie zijn geïmplementeerd om afbeeldingen te identificeren die verouderd zijn of al een tijdje niet zijn bijgewerkt. U kunt blauw-groene implementatiemethoden gebruiken en rolling upgrade gebruiken om containerafbeeldingen zonder downtime bij te werken. U kunt afbeeldingen scannen met behulp van hulpprogramma's die in de vorige sectie zijn beschreven. 

* Gebruik een CI-pijplijn (continue integratie) met geïntegreerde beveiligingsscans om beveiligde afbeeldingen te bouwen en deze naar uw privéregister te pushen. De ingebouwde scanfunctie voor beveiligingsproblemen van de CI-oplossing zorgt ervoor dat installatiekopieën die voor alle tests slagen, naar het persoonlijke register worden gepusht van waaruit productieworkloads worden geïmplementeerd. 

  Een fout in een CI-pijplijn zorgt ervoor dat kwetsbare installatie afbeeldingen niet naar het privéregister worden pushen dat wordt gebruikt voor implementaties van productieworkloads. Het automatiseert ook het scannen van de beveiliging van afbeeldingen als er een groot aantal afbeeldingen is. Het handmatig controleren van installatiekopieën op beveiligingsproblemen kan erg langdurig en foutgevoelig zijn. 

### <a name="enforce-least-privileges-in-runtime"></a>Minste bevoegdheden in runtime afdwingen 

Het concept van minste bevoegdheden is een basisbeveiligingsprincipe best practice ook van toepassing is op containers. Wanneer een beveiligingsprobleem wordt misbruikt, krijgt de aanvaller over het algemeen toegang en bevoegdheden die gelijk zijn aan die van de gecompromitteerde toepassing of het proces. Door ervoor te zorgen dat containers werken met de laagste bevoegdheden en toegang die nodig zijn om de taak uit te kunnen kunnen, vermindert u de blootstelling aan risico's. 

### <a name="reduce-the-container-attack-surface-by-removing-unneeded-privileges"></a>Verklein het aanvalsoppervlak voor containers door onnodige bevoegdheden te verwijderen 

U kunt ook het potentiële aanvalsoppervlak minimaliseren door ongebruikte of onnodige processen of bevoegdheden uit de container-runtime te verwijderen. Containers met bevoegdheden worden uitgevoerd als hoofdmap. Als een kwaadwillende gebruiker of workload een escape-bestand maakt in een geprivilegieerde container, wordt de container vervolgens uitgevoerd als root op dat systeem.

### <a name="preapprove-files-and-executables-that-the-container-is-allowed-to-access-or-run"></a>Bestanden en uitvoerbare bestanden die de container mag openen of uitvoeren vooraf goedkeuren 

Door het aantal variabelen of onbekende variabelen te verminderen, kunt u een stabiele, betrouwbare omgeving onderhouden. Het beperken van containers zodat ze alleen toegang hebben tot vooraf goedgekeurde of op een veilige lijst geplaatste bestanden en uitvoerbare bestanden, is een bewezen methode om blootstelling aan risico's te beperken.  

Het is veel eenvoudiger om een lijst met veilige lijsten te beheren wanneer deze vanaf het begin wordt geïmplementeerd. Een lijst met veilige bestanden biedt een meting van controle en beheerbaarheid wanneer u weet welke bestanden en uitvoerbare bestanden vereist zijn om de toepassing correct te laten functioneren. 

Een lijst met veilige omgevingen vermindert niet alleen het aanvalsoppervlak, maar kan ook een basislijn bieden voor afwijkingen en voorkomen dat de scenario's met ruis en containerscenario's worden gebruikt. 

### <a name="enforce-network-segmentation-on-running-containers"></a>Netwerksegmentatie afdwingen voor het uitvoeren van containers  

Om containers in het ene subnet te beschermen tegen beveiligingsrisico's in een ander subnet, moet u netwerksegmentatie (of nanosegmentatie) of scheiding tussen de lopende containers onderhouden. Het onderhouden van netwerksegmentatie kan ook nodig zijn voor het gebruik van containers in branches die vereist zijn om te voldoen aan de nalevingsvereisten.  

Het partnerhulpprogramma [Aqua biedt bijvoorbeeld](https://azuremarketplace.microsoft.com/marketplace/apps/aqua-security.aqua-security?tab=Overview) een geautomatiseerde benadering voor nanosegmentatie. Aqua bewaakt containernetwerkactiviteiten in runtime. Het identificeert alle binnenkomende en uitgaande netwerkverbindingen naar/van andere containers, services, IP-adressen en het openbare internet. Nano-segmentatie wordt automatisch gemaakt op basis van bewaakt verkeer. 

### <a name="monitor-container-activity-and-user-access"></a>Containeractiviteit en gebruikerstoegang bewaken 

Net als bij elke IT-omgeving moet u de activiteit en gebruikerstoegang tot uw containerecosysteem consistent bewaken om snel verdachte of schadelijke activiteiten te identificeren. Azure biedt oplossingen voor containerbewaking, waaronder:

* [Azure Monitor voor containers bewaakt](../azure-monitor/containers/container-insights-overview.md) de prestaties van uw workloads die zijn geïmplementeerd in Kubernetes-omgevingen die worden gehost op Azure Kubernetes Service (AKS). Azure Monitor containers biedt u inzicht in de prestaties door metrische geheugen- en processorgegevens te verzamelen van controllers, knooppunten en containers die beschikbaar zijn in Kubernetes via de API voor metrische gegevens. 

* De [Azure Container Monitoring-oplossing helpt](../azure-monitor/containers/containers.md) u bij het weergeven en beheren van andere Docker- en Windows-containerhosts op één locatie. Bijvoorbeeld:

  * Gedetailleerde controle-informatie weergeven met opdrachten die worden gebruikt met containers. 
  * Problemen met containers oplossen door gecentraliseerde logboeken te bekijken en te doorzoeken zonder dat u op afstand Docker- of Windows-hosts hoeft te bekijken.  
  * Containers zoeken die ruis kunnen hebben en overtollige resources op een host verbruiken.
  * Gecentraliseerde CPU- en geheugen-, opslag- en netwerkgebruiks- en prestatiegegevens voor containers weergeven.  

  De oplossing ondersteunt containeror orchestrators, waaronder Docker Swarm, DC/OS, onmanaged Kubernetes, Service Fabric en Red Hat OpenShift. 

### <a name="monitor-container-resource-activity"></a>Activiteit van containerresources bewaken 

Controleer uw resourceactiviteit, zoals bestanden, netwerken en andere resources die uw containers openen. Het bewaken van resourceactiviteit en -verbruik is nuttig voor zowel prestatiebewaking als als veiligheidsmaatregel. 

[Azure Monitor](../azure-monitor/overview.md) maakt kernbewaking voor Azure-services mogelijk door het verzamelen van metrische gegevens, activiteitenlogboeken en diagnostische logboeken toe te staan. Het activiteitenlogboek geeft bijvoorbeeld aan wanneer nieuwe resources worden gemaakt of gewijzigd. 

  Er zijn metrische gegevens beschikbaar met prestatiestatistieken voor verschillende resources en zelfs het besturingssysteem binnen een virtuele machine. U kunt deze gegevens bekijken met een van de verkenners in de Azure-portal en waarschuwingen maken op basis van deze metrische gegevens. Azure Monitor biedt de snelste pijplijn voor metrische gegevens (vijf minuten tot één minuut); gebruik het dus voor waarschuwingen en meldingen waarbij tijd van cruciaal belang is. 

### <a name="log-all-container-administrative-user-access-for-auditing"></a>Alle toegang van gebruikers met beheerderstoegang voor containers voor controle in een logboek 

Behoud een nauwkeurig audittrail van beheerderstoegang tot uw containerecosysteem, inclusief uw Kubernetes-cluster, containerregister en containerafbeeldingen. Deze logboeken kunnen nodig zijn voor controledoeleinden en zijn nuttig als forensisch bewijs na een beveiligingsincident. Azure-oplossingen zijn onder andere:

* [Integratie van Azure Kubernetes Service met Azure Security Center](../security-center/defender-for-kubernetes-introduction.md) om de beveiligingsconfiguratie van de clusteromgeving te bewaken en beveiligingsaanbevelingen te genereren
* [Azure Container Monitoring-oplossing](../azure-monitor/containers/containers.md)
* Resourcelogboeken voor [Azure Container Instances](container-instances-log-analytics.md) en [Azure Container Registry](../container-registry/container-registry-diagnostics-audit-logs.md)

## <a name="next-steps"></a>Volgende stappen

* Zie de [Azure-beveiligingsbasislijn voor Container Instances](security-baseline.md) voor uitgebreide aanbevelingen die u helpen de beveiligingsstatus van uw implementatie te verbeteren.

* Meer informatie over het gebruik [Azure Security Center](../security-center/container-security.md) voor realtime detectie van bedreigingen in uw containeromgevingen.

* Meer informatie over het beheren van beveiligingsproblemen in containers met oplossingen [van Twistlock](https://www.twistlock.com/solutions/microsoft-azure-container-security/) en [Aqua Security.](https://www.aquasec.com/solutions/azure-container-security/)