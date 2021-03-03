---
title: Azure-operationele beveiliging | Microsoft Docs
description: Laat u kennismaken met Microsoft Azure controle logboeken, de bijbehorende services en hoe deze werkt door dit overzicht te lezen.
services: security
documentationcenter: na
author: UnifyCloud
manager: barbkess
editor: TomSh
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: TomSh
ms.openlocfilehash: 7d71820db3d58931f2fcd8d18441534ad36183c2
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101711991"
---
# <a name="azure-operational-security"></a>Operationele Azure-beveiliging
## <a name="introduction"></a>Inleiding

### <a name="overview"></a>Overzicht
We weten dat de veiligheid taak in de Cloud is en hoe belang rijk het is dat u nauw keurige en tijdige informatie over Azure-beveiliging vindt. Een van de beste redenen voor het gebruik van Azure voor uw toepassingen en services is om te profiteren van de uitgebreide reeks beveiligings hulpprogramma's en mogelijkheden die beschikbaar zijn. Met deze hulpprogram ma's en mogelijkheden kunt u beveiligde oplossingen maken op het beveiligde Azure-platform. Windows Azure moet vertrouwelijkheid, integriteit en beschik baarheid van klant gegevens bieden, terwijl u ook transparante verantwoording mogelijk maakt.

Om klanten te helpen beter inzicht te krijgen in de matrix met beveiligings controles die zijn geïmplementeerd in Microsoft Azure van de perspectieven van de klant en micro soft, is dit technisch document, ' Azure Operational Security ', geschreven met een uitgebreid overzicht van de operationele beveiliging die beschikbaar is met Windows Azure.

### <a name="azure-platform"></a>Azure-platform
Azure is een openbaar Cloud service platform dat ondersteuning biedt voor een groot aantal besturings systemen, programmeer talen, frameworks, hulpprogram ma's, data bases en apparaten. Het kan Linux-containers uitvoeren met docker-integratie; bouw apps met Java script, Python, .NET, PHP, Java en Node.js; Maak back-ends voor iOS-, Android-en Windows-apparaten. De Azure-Cloud service ondersteunt dezelfde technologieën waarvan miljoenen ontwikkel aars en IT-professionals al vertrouwen.

Wanneer u de IT-activa bouwt op of migreert naar, een open bare Cloud serviceprovider die u vertrouwt over de mogelijkheden van die organisatie om uw toepassingen en gegevens te beschermen met de services en de besturings elementen die ze bieden om de beveiliging van uw cloud-gebaseerde assets te beheren.

De infra structuur van Azure is ontworpen met het oog op toepassingen om miljoenen klanten tegelijkertijd te hosten en biedt een betrouw bare basis waarop bedrijven kunnen voldoen aan de beveiligings vereisten. Bovendien biedt Azure u een breed scala aan Configureer bare beveiligings opties en de mogelijkheid om deze te beheren, zodat u de beveiliging kunt aanpassen om te voldoen aan de unieke vereisten van de implementaties van uw organisatie. Dit document helpt u te begrijpen hoe de beveiligings mogelijkheden van Azure u kunnen helpen om aan deze vereisten te voldoen.

### <a name="abstract"></a>Abstract
Azure Operational Security heeft betrekking op de services, besturings elementen en functies die beschikbaar zijn voor gebruikers voor het beveiligen van hun gegevens, toepassingen en andere assets in Microsoft Azure. Azure Operational Security is gebaseerd op een Framework met de kennis die is opgedaan via verschillende mogelijkheden die uniek zijn voor micro soft, waaronder micro soft Security Development Lifecycle (SDL), het micro soft Security Response Center-programma en dieper bewustzijn van de Cyber beveiliging Threat-landschap.

Dit technisch document geeft een overzicht van de micro soft-benadering van de operationele beveiliging van Azure binnen het Microsoft Azure Cloud platform en behandelt de volgende services:
1.  [Azure Monitor](../../azure-monitor/index.yml)

2.  [Azure Security Center](../../security-center/security-center-introduction.md)

3.  [Azure Monitor](../../azure-monitor/overview.md)

4.  [Azure Network Watcher](../../network-watcher/network-watcher-monitoring-overview.md)

5.  [Azure Storage Analytics](/rest/api/storageservices/fileservices/storage-analytics)

6.  [Azure Active Directory](../../active-directory/fundamentals/active-directory-whatis.md)


## <a name="microsoft-azure-monitor-logs"></a>Microsoft Azure controle logboeken

Microsoft Azure controle Logboeken is de IT-beheer oplossing voor de hybride Cloud. Als u de bestaande System Center-implementatie gebruikt of uitbreidt, biedt Azure Monitor logboeken u de maximale flexibiliteit en controle voor het beheer van uw infra structuur op basis van de Cloud.

![Azure Monitor-logboeken](./media/operational-security/azure-operational-security-fig1.png)

Met Azure Monitor-Logboeken kunt u elk exemplaar in elke Cloud beheren, met inbegrip van on-premises, azure, AWS, Windows Server, Linux, VMware en open stack, tegen lagere kosten dan concurrerende oplossingen. Azure Monitor-logboeken zijn gebouwd voor de hele wereld en bieden een nieuwe benadering voor het beheren van uw onderneming die de snelste, rendabelste manier is om te voldoen aan nieuwe zakelijke uitdagingen en nieuwe werk belastingen, toepassingen en Cloud omgevingen te bieden.

### <a name="azure-monitor-services"></a>Azure Monitor Services

De kern functionaliteit van Azure Monitor Logboeken wordt verschaft door een set services die worden uitgevoerd in Azure. Elke service biedt een specifieke beheerfunctie. U kunt services combineren om verschillende beheerscenario's te bewerkstelligen.

| Service  | Beschrijving|
| :------------- | :-------------|
| Azure Monitor-logboeken | Bewaak en analyseer de beschikbaarheid en prestaties van verschillende resources, met inbegrip van fysieke en virtuele machines. |
|Automation | Automatiseer handmatige processen en dwing configuraties af voor fysieke en virtuele machines. |
| Backup | Back-ups maken van essentiële gegevens en deze herstellen. |
| Siteherstel | Bied hoge beschikbaarheid voor kritieke toepassingen. |

### <a name="azure-monitor-logs"></a>Azure Monitor-logboeken

[Azure monitor logboeken](https://azure.microsoft.com/documentation/services/log-analytics) biedt bewakings Services door gegevens van beheerde resources te verzamelen in een centrale opslag plaats. Deze gegevens kunnen gebeurtenissen, prestatiegegevens en aangepaste gegevens omvatten die via de API worden geleverd. Na verzameling zijn de gegevens beschikbaar voor waarschuwingen, analyse en export.


Met deze methode kunt u gegevens uit verschillende bronnen consolideren, zodat u gegevens uit uw Azure-Services kunt combi neren met uw bestaande on-premises omgeving. De methode maakt ook een duidelijk onderscheid tussen het verzamelen van gegevens en het bewerken hiervan. Zo zijn alle bewerkingen beschikbaar voor alle soorten gegevens.


![Diagram waarin de gegevens consolidatie van verschillende bronnen wordt weer gegeven, zodat u gegevens uit uw Azure-Services kunt combi neren met uw bestaande on-premises omgeving.](./media/operational-security/azure-operational-security-fig2.png)

De Azure Monitor-service beheert uw gegevens op basis van de Cloud veilig door de volgende methoden te gebruiken:
-   gegevens scheiding
-   bewaren van gegevens
-   fysieke beveiliging
-   incident beheer
-   acht
-   certificeringen voor beveiligings standaarden

### <a name="azure-backup"></a>Azure Backup

[Azure backup](https://azure.microsoft.com/documentation/services/backup) biedt services voor het maken van back-ups en het herstellen van gegevens en maakt deel uit van de Azure monitor pakket met producten en services.
Het beschermt uw toepassingsgegevens en bewaart deze jarenlang, zonder dat u grote investeringen hoeft te doen en met een minimum aan operationele kosten. Het kan een back-up maken van gegevens van fysieke en virtuele Windows-servers, naast de werk belastingen van toepassingen, zoals SQL Server en share point. Het kan ook worden gebruikt door [System Center Data Protection Manager (DPM)](https://en.wikipedia.org/wiki/System_Center_Data_Protection_Manager) om beveiligde gegevens te repliceren naar Azure voor redundantie en lange termijn opslag.


Beveiligde gegevens in Azure Backup worden opgeslagen in een back-upkluis in een bepaalde geografische regio. De gegevens worden in dezelfde regio gerepliceerd en kunnen, afhankelijk van het type kluis, ook worden gerepliceerd naar een andere regio voor verdere tolerantie.

### <a name="management-solutions"></a>Beheeroplossingen
[Azure monitor](../../security-center/security-center-introduction.md) is de Cloud oplossing van micro soft die u helpt bij het beheren en beveiligen van uw on-premises en Cloud infrastructuur.


[Beheer oplossingen](../../azure-monitor/insights/solutions.md) zijn voorverpakte sets van logica die een bepaald beheer scenario implementeren met behulp van een of meer Azure monitor Services. Er zijn verschillende oplossingen beschikbaar van micro soft en partners die u eenvoudig kunt toevoegen aan uw Azure-abonnement om de waarde van uw investering in Azure Monitor te verg Roten. Als partner kunt u uw eigen oplossingen maken ter ondersteuning van uw toepassingen en services en deze aan gebruikers verstrekken via de sjablonen voor Azure Marketplace of Quick Start.


![Beheeroplossingen](./media/operational-security/azure-operational-security-fig4.png)

Een goed voor beeld van een oplossing die gebruikmaakt van meerdere services om aanvullende functionaliteit te bieden is de [updatebeheer oplossing](../../automation/update-management/overview.md). Deze oplossing maakt gebruik van de [Azure monitor logboeken](../../azure-monitor/logs/log-query-overview.md) agent voor Windows en Linux voor het verzamelen van informatie over de vereiste updates op elke agent. Deze gegevens worden naar de opslag plaats van Azure Monitor-logboeken geschreven, waar u deze kunt analyseren met een dash board.

Wanneer u een implementatie maakt, worden runbooks in [Azure Automation](../../automation/automation-intro.md) gebruikt om vereiste updates te installeren. U beheert dit hele proces in de portal en hoeft zich geen zorgen te maken over de onderliggende details.

## <a name="azure-security-center"></a>Azure Security Center

Azure Security Center helpt bij het beveiligen van uw Azure-resources. Het biedt geïntegreerde beveiligings bewaking en beleids beheer in uw Azure-abonnementen. Binnen de service kunt u alleen policies definiëren voor uw Azure-abonnementen, maar ook voor [resource groepen](../../azure-resource-manager/management/overview.md#resource-groups), zodat u meer gedetailleerder kunt zijn.

### <a name="security-policies-and-recommendations"></a>Beveiligingsbeleid en aanbevelingen

Een beveiligingsbeleid bepaalt welke set besturingselementen wordt aanbevolen voor resources binnen het opgegeven abonnement of de opgegeven resourcegroep.

In Security Center definieert u de beleidsregels op grond van de beveiligingsvereisten van uw bedrijf en het type toepassingen of de gevoeligheid van de gegevens.

![Beveiligingsbeleid en aanbevelingen](./media/operational-security/azure-operational-security-fig5.png)


Beleids regels die zijn ingeschakeld in het abonnements niveau, worden automatisch door gegeven aan alle resource groepen binnen het abonnement, zoals wordt weer gegeven in het diagram aan de rechter kant:


### <a name="data-collection"></a>Gegevens verzamelen

Met Security Center worden gegevens van uw virtuele machines (VM's) verzameld om de beveiligingsstatus van de VM's te beoordelen, aanbevelingen voor beveiliging te geven en u te waarschuwen bij bedreigingen. Wanneer u voor het eerst toegang Security Center, wordt gegevens verzameling ingeschakeld op alle virtuele machines in uw abonnement. Gegevensverzameling wordt aanbevolen, maar u kunt gegevensverzameling indien gewenst ook uitschakelen in het Security Center-beleid.

### <a name="data-sources"></a>Gegevensbronnen

- Azure Security Center analyseert gegevens uit de volgende bronnen om inzicht in uw beveiligingsstatus te geven, beveiligingsproblemen te identificeren, oplossingen aan te raden en actieve bedreigingen te detecteren:

-   Azure Services: gebruikt informatie over de configuratie van de Azure-services die u hebt geïmplementeerd door te communiceren met de resourceprovider van die service.

- Netwerkverkeer: gebruikt steekproefgewijs netwerkverkeermetagegevens uit de infrastructuur van Microsoft, zoals bron-/doel-IP/poort, pakketgrootte en netwerkprotocol.

-   Oplossingen van partners: gebruikt beveiligingswaarschuwingen van geïntegreerde partneroplossingen, zoals firewalls en antimalwareoplossingen.

-   Uw virtuele machines: gebruikt configuratie-informatie en informatie over beveiligingsgebeurtenissen, zoals Windows-gebeurtenis- en auditlogboeken, IIS-logboeken, syslog-berichten en crashdumpbestanden van uw virtuele machines.

### <a name="data-protection"></a>Gegevensbeveiliging

Om klanten te helpen bedreigingen te voorkomen, te detecteren en erop te reageren, verzamelt en verwerkt Azure Security Center gegevens over beveiliging, zoals configuratie-informatie, metagegevens, gebeurtenislogboeken, crashdumpbestanden en nog veel meer. Microsoft voldoet aan strikte nalevings- en beveiligingsrichtlijnen - van het schrijven van code tot de uitvoering van een service.

-   **Scheiding van gegevens**: gegevens worden op een logische manier apart van elkaar gehouden, in elk onderdeel van de service. Alle gegevens worden gemarkeerd per organisatie. Deze markering blijft aanwezig gedurende de levenscyclus van de gegevens en deze wordt afgedwongen op elke laag van de service.

-   **Gegevens toegang**: om aanbevelingen voor beveiliging te bieden en mogelijke beveiligings Risico's te onderzoeken, kunnen mede werkers van micro soft toegang hebben tot gegevens die worden verzameld of geanalyseerd door Azure-Services, waaronder crash dump bestanden, gebeurtenissen voor het maken van processen, moment opnamen van virtuele schijven en artefacten, die per ongeluk klant gegevens of persoonlijke gegevens van uw virtuele machines kunnen bevatten. We voldoen aan de [voor waarden van de micro soft Online Services en de privacyverklaring](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31), die aangeven dat micro soft de klant gegevens niet gebruikt of dat er gegevens worden afgeleid voor reclame-of vergelijk bare commerciële doel einden.

-   **Gegevensgebruik**: Microsoft gebruikt informatie over patronen en bedreigingen die worden gezien tussen meerdere tenants voor het verbeteren van onze mogelijkheden voor voorkoming en detectie; wij doen dit in overeenstemming met de privacyverplichtingen beschreven in onze [Privacyverklaring](https://www.microsoft.com/en-us/privacystatement/OnlineServices/).

### <a name="data-location"></a>Locatie van gegevens

Azure Security Center verzamelt tijdelijke kopieën van uw crashdumpbestanden en analyseert deze op bewijs van pogingen tot misbruik en geslaagde aanvallen. Azure Security Center voert deze analyse uit binnen hetzelfde geografische gebied als de werkruimte en verwijdert de tijdelijke kopieën wanneer de analyse is voltooid. Machine-artefacten worden centraal opgeslagen in dezelfde regio als de virtuele machine.

-   **Uw opslag accounts**: er is een opslag account opgegeven voor elke regio waarin de virtuele machines worden uitgevoerd. Hierdoor kunt u gegevens in dezelfde regio opslaan als de virtuele machine waarvan de gegevens worden verzameld.

-   **Azure Security Center-opslag**: informatie over beveiligingswaarschuwingen, waaronder waarschuwingen van partners, aanbevelingen en de status van de beveiliging, worden centraal opgeslagen, momenteel in de Verenigde Staten. Deze informatie omvat mogelijk gerelateerde configuratie-informatie en beveiligingsgebeurtenissen die indien nodig zijn verzameld van uw virtuele machines om u de beveiligingswaarschuwing, aanbeveling of de beveiligingsstatus door te geven.


## <a name="azure-monitor"></a>Azure Monitor

Met de beveiligings-en controle oplossing van de [Azure monitor-logboeken](../../security-center/security-center-monitoring.md) kunt u alle resources actief bewaken, waardoor de impact van beveiligings incidenten kan worden geminimaliseerd. Azure Monitor logboeken Beveiliging en audit beveiligings domeinen hebben die kunnen worden gebruikt voor het bewaken van resources. Het beveiligings domein biedt snelle toegang tot opties. voor beveiligings bewaking worden de volgende domeinen in meer details behandeld:

-   Malware-evaluatie
-   Update-evaluatie
-   Identiteit en toegang.

Azure Monitor biedt verwijzingen naar informatie over specifieke typen resources. Het biedt visualisatie, query's, route ring, waarschuwingen, automatisch schalen en automatisering op gegevens van de Azure-infra structuur (activiteiten logboek) en elke afzonderlijke Azure-resource (Diagnostische logboeken).

![Azure Monitor](./media/operational-security/azure-operational-security-fig6.png)


Cloud toepassingen zijn complex met veel bewegende onderdelen. Bewaking biedt gegevens om ervoor te zorgen dat uw toepassing in een goede staat actief blijft. Het helpt u ook om mogelijke problemen op te lossen of om Stave te oplossen.

Daarnaast kunt u bewakings gegevens gebruiken om grondige inzichten over uw toepassing te krijgen. Deze kennis kan u helpen bij het verbeteren van de prestaties of het onderhoud van toepassingen, of het automatiseren van acties waarvoor anders hand matige interventie nodig zou zijn.

### <a name="azure-activity-log"></a>Azure-activiteitenlogboek


Het is een logboek dat inzicht geeft in de bewerkingen die zijn uitgevoerd op resources in uw abonnement. Het activiteiten logboek was voorheen bekend als ' audit logs ' of ' operationele logboeken ', omdat het controle-vlak gebeurtenissen voor uw abonnementen rapporteert.

![Azure-activiteitenlogboek](./media/operational-security/azure-operational-security-fig7.png)

Met het activiteiten logboek kunt u de ' wat, wie en wanneer ' bepalen voor schrijf bewerkingen (PUT, POST, DELETE) die zijn uitgevoerd op de resources in uw abonnement. U kunt ook de status van de bewerking en andere relevante eigenschappen begrijpen. Het activiteiten logboek bevat geen lees bewerkingen (GET) of bewerkingen voor resources die gebruikmaken van het klassieke model.

### <a name="azure-diagnostic-logs"></a>Diagnostische logboeken van Azure

Deze logboeken worden verzonden door een resource en bieden uitgebreide, frequente gegevens over de werking van die resource. De inhoud van deze logboeken is afhankelijk van het bron type.

Windows-gebeurtenis systeem logboeken zijn bijvoorbeeld een categorie met Diagnostische logboeken voor Vm's en blob-, tabel-en wachtrij logboeken zijn categorieën met Diagnostische logboeken voor opslag accounts.

Diagnostische logboeken verschillen van het [activiteiten logboek (voorheen bekend als audit logboek of operationeel logboek)](../../azure-monitor/essentials/platform-logs-overview.md). Het activiteiten logboek biedt inzicht in de bewerkingen die zijn uitgevoerd voor de resources in uw abonnement. Diagnoselogboeken bieden inzicht in bewerkingen die door de resources zelf zijn uitgevoerd.

### <a name="metrics"></a>Metrische gegevens

Met Azure Monitor kunt u telemetrie gebruiken om inzicht te krijgen in de prestaties en status van uw workloads op Azure. Het belangrijkste type Azure-telemetriegegevens is de metrische gegevens (ook wel prestatie meters genoemd) die worden verzonden door de meeste Azure-resources. Azure Monitor biedt verschillende manieren om deze [metrische gegevens](../../azure-monitor/data-platform.md) te configureren en te gebruiken voor bewaking en probleem oplossing. Metrische gegevens zijn een waardevolle bron van telemetrie en u kunt de volgende taken uitvoeren:

-   **Volg de prestaties** van uw resource (zoals een VM, website of logische app) door de metrische gegevens in een portal diagram te tekenen en de grafiek vast te maken aan een dash board.

-   **Ontvang een melding over een probleem** dat invloed heeft op de prestaties van uw resource wanneer een metriek een bepaalde drempel overschrijdt.

-   **Automatische acties configureren**, zoals het automatisch schalen van een resource of het activeren van een runbook wanneer een metriek een bepaalde drempel overschrijdt.

-   **Geavanceerde analyses uitvoeren** of rapportages over prestaties of gebruiks trends van uw resource.

-   **Archiveer** de prestaties of de status geschiedenis van uw resource voor nalevings-en controle doeleinden.

### <a name="azure-diagnostics"></a>Azure Diagnostics

Het is de mogelijkheid binnen Azure om het verzamelen van diagnostische gegevens op een geïmplementeerde toepassing mogelijk te maken. U kunt de diagnostische uitbrei ding van verschillende bronnen gebruiken. Dit wordt momenteel ondersteund: [Web-en werk rollen van Azure Cloud service](/visualstudio/azure/vs-azure-tools-configure-roles-for-cloud-service), [Azure virtual machines](../../virtual-machines/windows/overview.md) met micro soft Windows en [service Fabric](../../azure-monitor/agents/diagnostics-extension-overview.md). Andere Azure-Services hebben hun eigen afzonderlijke diagnoses.

## <a name="azure-network-watcher"></a>Azure Network Watcher

Het controleren van de netwerk beveiliging is essentieel voor het detecteren van problemen met het netwerk en het controleren van de naleving van uw IT-beveiligings beleid en het regulerende governance model. Met de weer gave beveiligings groep kunt u de geconfigureerde netwerk beveiligings groep en beveiligings regels en de juiste beveiligings regels ophalen. Met de lijst met toegepaste regels kunt u bepalen welke poorten zijn geopend en het netwerk probleem beoordelen.

[Network Watcher](../../network-watcher/network-watcher-monitoring-overview.md) is een regionale service waarmee u voor waarden kunt controleren en diagnosticeren op netwerk niveau in, naar en Azure. U kunt de beschikbare diagnostische en visualisatiehulpprogramma's voor netwerken in Network Watcher gebruiken om uw netwerk in Azure te begrijpen, te analyseren en inzichten voor uw netwerk te verkrijgen. Deze service omvat pakket opname, volgende hop, IP-stroom controleren, beveiligings groep weer geven, NSG stroom Logboeken. Bewaking op scenario niveau biedt een end-to-end weer gave van netwerk bronnen in tegens telling tot afzonderlijke netwerk bron bewaking.

![Azure Network Watcher](./media/operational-security/azure-operational-security-fig8.png)

Network Watcher heeft momenteel de volgende mogelijkheden:

-   **<a href="/azure/network-watcher/network-watcher-monitoring-overview">Audit logboeken</a>**: bewerkingen die worden uitgevoerd als onderdeel van de configuratie van netwerken, worden vastgelegd. Deze logboeken kunnen worden weer gegeven in de Azure Portal of worden opgehaald met micro soft-hulpprogram ma's, zoals Power BI of hulpprogram ma's van derden. Audit logboeken zijn beschikbaar via de portal, Power shell, CLI en rest-API. Zie bewerkingen controleren met Resource Manager voor meer informatie over audit Logboeken. Audit logboeken zijn beschikbaar voor bewerkingen die op alle netwerk bronnen worden uitgevoerd.


-   **<a href="/azure/network-watcher/network-watcher-ip-flow-verify-overview">IP-stroom wordt geverifieerd</a>** : controleert of een pakket is toegestaan of geweigerd op basis van de stroom informatie 5-tuple Packet para meters (doel-IP, bron-IP, doel poort, bron poort en Protocol). Als het pakket wordt geweigerd door een netwerk beveiligings groep, wordt de regel en netwerk beveiligings groep die het pakket heeft geweigerd, geretourneerd.

-   **<a href="/azure/network-watcher/network-watcher-next-hop-overview">Volgende hop</a>** : bepaalt de volgende hop voor pakketten die worden gerouteerd in de Azure-netwerk infrastructuur, zodat u eventuele onjuist geconfigureerde, door de gebruiker gedefinieerde routes kunt vaststellen.

-   **<a href="/azure/network-watcher/network-watcher-security-group-view-overview">Weer gave van beveiligings groep</a>** : Hiermee worden de effectief en toegepaste beveiligings regels opgehaald die op een virtuele machine worden toegepast.

-   **<a href="/azure/network-watcher/network-watcher-nsg-flow-logging-overview">NSG flow</a>** -logboek registratie: stroom logboeken voor netwerk beveiligings groepen kunt u Logboeken vastleggen die betrekking hebben op verkeer dat is toegestaan of geweigerd door de beveiligings regels in de groep. De stroom wordt gedefinieerd door een gegevens van vijf Tuples: bron-IP, doel-IP, bron poort, doel poort en protocol.

## <a name="azure-storage-analytics"></a>Azure Opslaganalyse

[Opslaganalyse](/rest/api/storageservices/fileservices/storage-analytics) kunt metrische gegevens opslaan die geaggregeerde trans actie-statistieken en capaciteitgegevens over aanvragen voor een opslag service bevatten. Trans acties worden gerapporteerd op het niveau van de API-bewerking en op het niveau van de opslag service, en de capaciteit wordt gerapporteerd op het niveau van de opslag service. Metrische gegevens kunnen worden gebruikt voor het analyseren van het gebruik van opslag Services, het diagnosticeren van problemen met aanvragen die zijn gedaan voor de opslag service en voor het verbeteren van de prestaties van toepassingen die gebruikmaken van een service.

[Azure Opslaganalyse](/rest/api/storageservices/fileservices/storage-analytics) voert logboek registratie uit en geeft metrische gegevens voor een opslag account. U kunt deze gegevens gebruiken om aanvragen te traceren, gebruiks trends te analyseren en problemen met uw opslag account op te sporen. Opslaganalyse logboek registratie is beschikbaar voor de [Services blob, Queue en Table](../../storage/common/storage-introduction.md). Opslaganalyse registreert gedetailleerde informatie over geslaagde en mislukte aanvragen bij een opslagservice.

Deze informatie kan worden gebruikt voor het bewaken van afzonderlijke aanvragen en voor het vaststellen van problemen met een opslagservice. Aanvragen worden op de beste basis geregistreerd. Logboek vermeldingen worden alleen gemaakt als er aanvragen worden gedaan voor het service-eind punt. Als een opslag account bijvoorbeeld activiteit heeft in het BLOB-eind punt, maar niet in de tabel-of wachtrij-eind punten, worden alleen logboeken die betrekking hebben op de Blob service gemaakt.

Als u Opslaganalyse wilt gebruiken, moet u deze afzonderlijk inschakelen voor elke service die u wilt bewaken. U kunt deze inschakelen in de [Azure Portal](https://portal.azure.com/); Zie [een opslag account bewaken in de Azure Portal](../../storage/common/manage-storage-analytics-logs.md)voor meer informatie. U kunt Opslaganalyse ook via een programma inschakelen via de REST API of de client bibliotheek. Gebruik de bewerking service-eigenschappen instellen om Opslaganalyse afzonderlijk in te scha kelen voor elke service.

De geaggregeerde gegevens worden opgeslagen in een bekende BLOB (voor logboek registratie) en in bekende tabellen (voor metrieken), die mogelijk worden geopend met behulp van de Blob service-en Table service-Api's.

Opslaganalyse heeft een limiet van 20 TB voor de hoeveelheid opgeslagen gegevens die onafhankelijk is van de totale limiet voor uw opslag account. Alle logboeken worden opgeslagen in [blok-blobs](../../storage/common/storage-analytics.md) in een container met de naam $Logs, die automatisch worden gemaakt wanneer Opslaganalyse is ingeschakeld voor een opslag account.

De volgende acties die door Opslaganalyse worden uitgevoerd, zijn Factureerbaar:

-   Aanvragen voor het maken van blobs voor logboek registratie
-   Aanvragen voor het maken van tabel entiteiten voor metrische gegevens.

> [!Note]
> Zie [Opslaganalyse en facturering](/rest/api/storageservices/fileservices/storage-analytics-and-billing)voor meer informatie over de facturering en het Bewaar beleid voor gegevens.
> Voor optimale prestaties wilt u het aantal zeer gebruikte schijven dat is gekoppeld aan de virtuele machine beperken om mogelijke beperking te voor komen. Als alle schijven op hetzelfde moment niet Maxi maal worden gebruikt, kan het opslag account een grotere schijf ondersteunen.

> [!Note]
> Zie [schaalbaarheids doelen voor standaard opslag accounts](../../storage/common/scalability-targets-standard-account.md)voor meer informatie over limieten voor opslag accounts.


De volgende typen geverifieerde en anonieme aanvragen worden geregistreerd.

| Geverifieerd  | Anoniem|
| :------------- | :-------------|
| Geslaagde aanvragen | Geslaagde aanvragen |
|Mislukte aanvragen, inclusief time-out-, beperkings-, netwerk-, autorisatiefouten en overige fouten | Aanvragen met behulp van een Shared Access Signature (SAS), waaronder mislukte en geslaagde aanvragen |
| Aanvragen met behulp van een Shared Access Signature (SAS), waaronder mislukte en geslaagde aanvragen |Time-outfouten voor zowel de client als de server |
|   Aanvragen voor analysegegevens |    Mislukte GET-aanvragen met foutcode 304 (Niet gewijzigd) |
| Aanvragen die worden gedaan door Opslaganalyse zichzelf, zoals het maken of verwijderen van een logboek, worden niet geregistreerd. Een volledige lijst met de geregistreerde gegevens wordt gedocumenteerd in de onderwerpen [Opslaganalyse vastgelegde bewerkingen en status berichten](/rest/api/storageservices/fileservices/storage-analytics-logged-operations-and-status-messages) en [Opslaganalyse logboek indeling](/rest/api/storageservices/fileservices/storage-analytics-log-format) . | Alle andere mislukte anonieme aanvragen worden niet geregistreerd. Een volledige lijst met de geregistreerde gegevens wordt gedocumenteerd in de [Opslaganalyse geregistreerde bewerkingen en status berichten](/rest/api/storageservices/fileservices/storage-analytics-logged-operations-and-status-messages) en [Opslaganalyse logboek indeling](/rest/api/storageservices/fileservices/storage-analytics-log-format). |

## <a name="azure-active-directory"></a>Azure Active Directory

Azure AD omvat ook een volledige suite met mogelijkheden voor identiteits beheer, waaronder multi-factor Authentication, apparaatregistratie, selfservice wachtwoord beheer, beheer van selfservice groepen, Privileged account management, op rollen gebaseerd toegangs beheer, bewaking van toepassings gebruik, uitgebreide controle en beveiligings bewaking en waarschuwingen.

-   Verbeter de beveiliging van toepassingen met Azure AD multi-factor Authentication en voorwaardelijke toegang.

-   Bewaak het gebruik van de toepassing en Bescherm uw bedrijf tegen geavanceerde bedreigingen met beveiligings rapportage en-bewaking.

Azure Active Directory (Azure AD) bevat beveiligings-, activiteits- en controlerapporten voor uw directory. [Het Azure Active Directory controle rapport](../../active-directory/reports-monitoring/overview-reports.md) helpt klanten bij het identificeren van geprivilegieerde acties die zijn opgetreden in hun Azure Active Directory. Geprivilegieerde acties omvatten verhogings wijzigingen (bijvoorbeeld het maken van functies of het opnieuw instellen van wacht woorden), het wijzigen van beleids configuraties (voor beeld van wachtwoord beleid) of wijzigingen in de configuratie van mappen (bijvoorbeeld wijzigingen in de instellingen van een domein Federatie).

De rapporten bieden de controle record voor de gebeurtenis naam, de actor die de actie heeft uitgevoerd, de doel resource die wordt beïnvloed door de wijziging en de datum en tijd (in UTC). Klanten kunnen de lijst met controle gebeurtenissen voor hun Azure Active Directory ophalen via de [Azure Portal](https://portal.azure.com/), zoals wordt beschreven in [uw audit logboeken weer geven](../../active-directory/reports-monitoring/overview-reports.md). Hierna volgt een lijst met de beschikbare rapporten:

| Beveiligingsrapporten  | Activiteitsrapporten| Controlerapporten |
| :------------- | :-------------| :-------------|
|Aanmeldingen van onbekende bronnen | Toepassingsgebruik: samenvatting | Directorycontrolerapport |
|Aanmeldingen na meerdere mislukte pogingen | Toepassingsgebruik: gedetailleerd |   |
|Aanmeldingen vanuit meerdere locaties | Toepassingsdashboard |  |
|Aanmeldingen van IP-adressen met verdachte activiteit |Fouten bij het inrichten van een account |  |
|Onregelmatige aanmeldingsactiviteiten |Afzonderlijke gebruikersapparaten |  |
|Aanmeldingen vanaf mogelijk geïnfecteerde apparaten |Afzonderlijke gebruikersactiviteiten |   |
|Gebruikers met afwijkende aanmeldingsactiviteiten |Rapport met groepsactiviteiten |   |
| |Rapport voor de registratie van opnieuw ingestelde wachtwoorden |   |
| |Activiteit voor wachtwoord opnieuw instellen |   |



De gegevens van deze rapporten kunnen nuttig zijn voor uw toepassingen, zoals SIEM Systems, audit en business intelligence tools. De Azure AD Reporting- [api's](../../active-directory/reports-monitoring/concept-reporting-api.md) bieden programmatische toegang tot de gegevens via een set op rest gebaseerde api's. U kunt deze Api's aanroepen vanuit verschillende programmeer talen en hulpprogram ma's.

Gebeurtenissen in het Azure AD-controle rapport worden 180 dagen bewaard.

> [!Note]
> Zie [Azure Active Directory retentie beleid](../../active-directory/reports-monitoring/reference-reports-data-retention.md)voor rapporten voor meer informatie over retentie op rapport.

De rapportage-API kan worden gebruikt voor klanten die hun [audit gebeurtenissen](../../active-directory/reports-monitoring/concept-audit-logs.md) willen opslaan voor langere Bewaar perioden, waarmee ze regel matig controle gebeurtenissen in een afzonderlijk gegevens archief kunnen ophalen.

## <a name="summary"></a>Samenvatting

In dit artikel vindt u een samen vatting van de beveiliging van uw privacy en het beveiligen van uw gegevens, en het leveren van software en services waarmee u de IT-infra structuur van uw organisatie kunt beheren. Micro soft erkent dat de vertrouwens relatie strikt nood zakelijk is wanneer ze hun gegevens toevertrouwen aan anderen. Microsoft voldoet aan strikte nalevings- en beveiligingsrichtlijnen - van het schrijven van code tot de uitvoering van een service. Het beveiligen en beveiligen van gegevens is een topprioriteit bij micro soft.

In dit artikel wordt uitgelegd

-   Hoe gegevens worden verzameld, verwerkt en beveiligd in de Azure Monitor Suite.

-   Snel gebeurtenissen analyseren op meerdere gegevens bronnen. Beveiligings Risico's identificeren en inzicht krijgen in de omvang en impact van bedreigingen en aanvallen om de schade van een inbreuk op de beveiliging te beperken.

-   Identificeer aanvals patronen door uitgaand schadelijk IP-verkeer en schadelijke bedreigings typen te visualiseren. Inzicht in de beveiligings postuur van uw hele omgeving, ongeacht het platform.

-   Leg alle logboek-en gebeurtenis gegevens vast die nodig zijn voor een beveiligings-of nalevings controle. Slash de tijd en resources die nodig zijn voor het leveren van een beveiligings controle met een volledige, Doorzoek bare en exporteer bare logboek-en gebeurtenis gegevensset.

<ul>
<li>Verzamel beveiligings gebeurtenissen, audit en schendings analyse om uw activa te sluiten:</li>
<ul>
<li>Beveiligingspostuur</li>
<li>Belang rijk probleem</li>
<li>Samen vattingen van bedreigingen</li>
</ul>
</ul>

## <a name="next-steps"></a>Volgende stappen

- [Ontwerp en operationele beveiliging](https://www.microsoft.com/trustcenter/security/designopsecurity)

Micro soft ontwerpt haar services en software met behulp van beveiliging om ervoor te zorgen dat de Cloud infrastructuur robuust is en beschermt tegen aanvallen.

- [Azure Monitor-logboeken | Naleving beveiligings &](https://www.microsoft.com/cloud-platform/security-and-compliance)

Gebruik micro soft-beveiligings gegevens en-analyse om intelligente en efficiënte detectie van bedreigingen uit te voeren.

- [Planning en bewerkingen Azure Security Center](../../security-center/security-center-planning-and-operations-guide.md) Een reeks stappen en taken die u kunt volgen om uw gebruik van Security Center te optimaliseren op basis van de beveiligings vereisten van uw organisatie en het model voor Cloud beheer.