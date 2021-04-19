---
title: Best practices voor beveiliging voor uw Azure-assets
titleSuffix: Azure security
description: Dit artikel bevat een aantal operationele best practices voor het beveiligen van uw gegevens, toepassingen en andere assets in Azure.
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: tomsh
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/06/2019
ms.author: terrylan
ms.openlocfilehash: 6634a536828b3c19d771d135fdb3a1224d3dfdf3
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107717419"
---
# <a name="azure-operational-security-best-practices"></a>Best practices voor Operationele beveiliging van Azure
Dit artikel bevat een aantal operationele best practices voor het beveiligen van uw gegevens, toepassingen en andere assets in Azure.

De best practices zijn gebaseerd op een consensus van mening en werken met de huidige mogelijkheden en functiesets van het Azure-platform. Adviezen en technologieën veranderen in de tijd en dit artikel wordt regelmatig bijgewerkt om deze wijzigingen weer te geven.

## <a name="define-and-deploy-strong-operational-security-practices"></a>Krachtige operationele beveiligingsprocedures definiëren en implementeren
Operationele beveiliging van Azure verwijst naar de services, besturingselementen en functies die beschikbaar zijn voor gebruikers voor het beveiligen van hun gegevens, toepassingen en andere assets in Azure. Operationele beveiliging van Azure is gebaseerd op een framework dat gebruikmaakt van de kennis die is opgedaan via mogelijkheden die uniek zijn voor Microsoft, waaronder de [Security Development Lifecycle (SDL),](https://www.microsoft.com/sdl)het [Microsoft Security Response Center-programma](https://www.microsoft.com/msrc?rtc=1) en een diepgaande kennis van het bedreigingslandschap voor cyberbeveiliging.

## <a name="manage-and-monitor-user-passwords"></a>Gebruikerswachtwoorden beheren en bewaken
De volgende tabel bevat een aantal best practices met betrekking tot het beheren van gebruikerswachtwoorden:

**Best practice:** zorg ervoor dat u het juiste niveau van wachtwoordbeveiliging in de cloud hebt.   
**Detail:** volg de richtlijnen in [Microsoft Password Guidance (Richtlijnen](https://www.microsoft.com/research/publication/password-guidance/)voor Microsoft-wachtwoorden), die zijn ingesteld op gebruikers van de Microsoft-identiteitsplatforms (Azure Active Directory, Active Directory en Microsoft-account).

**Best practice:** controleer op verdachte acties met betrekking tot uw gebruikersaccounts.   
**Detail:** Controleer op gebruikers [die risico lopen en](../../active-directory/identity-protection/overview-identity-protection.md) riskante aanmeldingen met behulp van Azure [AD-beveiligingsrapporten.](../../active-directory/identity-protection/overview-identity-protection.md)

**Best practice:** automatisch wachtwoorden met een hoog risico detecteren en herstellen.   
**Detail:** [Azure AD Identity Protection](../../active-directory/identity-protection/overview-identity-protection.md) is een functie van de Azure AD Premium P2-editie waarmee u het volgende kunt doen:

- Mogelijke beveiligingsproblemen detecteren die van invloed zijn op de identiteiten van uw organisatie
- Geautomatiseerde reacties configureren op gedetecteerde verdachte acties die zijn gerelateerd aan de identiteiten van uw organisatie
- Verdachte incidenten onderzoeken en passende acties ondernemen om deze op te lossen

## <a name="receive-incident-notifications-from-microsoft"></a>Incidentmeldingen ontvangen van Microsoft
Zorg ervoor dat uw beveiligingsteam Azure-incidentmeldingen ontvangt van Microsoft. Een incidentmelding laat uw beveiligingsteam weten dat u Azure-resources hebt aangetast, zodat deze snel kunnen reageren op mogelijke beveiligingsrisico's en deze kunnen verhelpen.

In de Azure-inschrijvingsportal kunt u ervoor zorgen dat contactgegevens van beheerders details bevatten die beveiligingsbewerkingen melden. Contactgegevens zijn een e-mailadres en telefoonnummer.

## <a name="organize-azure-subscriptions-into-management-groups"></a>Azure-abonnementen organiseren in beheergroepen
Als uw organisatie veel abonnementen heeft, wilt u mogelijk de toegang, het beleid en de naleving voor die abonnementen op efficiënte wijze beheren. [Azure-beheergroepen](../../governance/management-groups/create-management-group-portal.md) bieden een bereikniveau dat hoger is dan abonnementen. U ordent abonnementen in containers die beheergroepen worden genoemd en u en past uw governancevoorwaarden toe op de beheergroepen. Alle abonnementen in een beheergroep nemen automatisch de voorwaarden over die op de beheergroep zijn toegepast.

U kunt een flexibele structuur van beheergroepen en abonnementen in een directory inbouwen. Elke directory krijgt één beheergroep op het hoogste niveau, de hoofdbeheergroep. Deze hoofdbeheergroep is zo in de hiërarchie ingebouwd dat alle beheergroepen en abonnementen hierin zijn opgevouwen. Met de hoofdbeheergroep kunnen globale beleidsregels en Azure-roltoewijzingen worden toegepast op mapniveau.

Hier volgen enkele best practices voor het gebruik van beheergroepen:

**Best practice:** zorg ervoor dat nieuwe abonnementen governance-elementen zoals beleidsregels en machtigingen toepassen wanneer ze worden toegevoegd.   
**Detail:** gebruik de hoofdbeheergroep om beveiligingselementen voor de hele onderneming toe te wijzen die van toepassing zijn op alle Azure-assets. Beleidsregels en machtigingen zijn voorbeelden van elementen.

**Best practice:** Lijn de bovenste niveaus van beheergroepen uit met segmentatiestrategie om een punt te bieden voor controle en beleidsconsistentie binnen elk segment.   
**Detail:** maak één beheergroep voor elk segment onder de hoofdbeheergroep. Maak geen andere beheergroepen in de hoofdmap.

**Best practice:** Beperk de diepte van de beheergroep om verwarring te voorkomen die zowel bij bewerkingen als bij de beveiliging te maken heeft.   
**Detail:** beperk uw hiërarchie tot drie niveaus, waaronder de hoofdmap.

**Best practice:** selecteer zorgvuldig welke items u wilt toepassen op de hele onderneming met de hoofdbeheergroep.   
**Detail:** zorg ervoor dat elementen van de hoofdbeheergroep duidelijk moeten worden toegepast op elke resource en dat ze weinig impact hebben.

Goede kandidaten zijn onder andere:

- Wettelijke vereisten die een duidelijke bedrijfsimpact hebben (bijvoorbeeld beperkingen met betrekking tot gegevenssoevereiniteit)
- Vereisten met bijna nul potentiële negatieve gevolgen voor bewerkingen, zoals beleid met controle-effect of Azure RBAC-machtigingstoewijzingen die zorgvuldig zijn gecontroleerd

**Best practice:** plan en test alle wijzigingen voor het hele bedrijf zorgvuldig in de hoofdbeheergroep voordat u ze gaat toepassen (beleid, Azure RBAC-model, etc.).   
**Detail:** wijzigingen in de hoofdbeheergroep kunnen van invloed zijn op elke resource in Azure. Hoewel ze een krachtige manier bieden om consistentie in de onderneming te garanderen, kunnen fouten of onjuist gebruik een negatieve invloed hebben op productiebewerkingen. Test alle wijzigingen in de hoofdbeheergroep in een testlab of productietest.

## <a name="streamline-environment-creation-with-blueprints"></a>Het maken van een omgeving stroomlijnen met blauwdrukken
[Met de Azure Blueprints-service](../../governance/blueprints/overview.md) kunnen cloudarchitecten en centrale informatietechnologiegroepen een herhaalbare set Azure-resources definiëren die voldoet aan de normen, patronen en vereisten van een organisatie en deze implementeert. Azure Blueprints maakt het ontwikkelteams mogelijk om snel nieuwe omgevingen te bouwen en op te zetten met een set ingebouwde onderdelen en het vertrouwen dat ze deze omgevingen maken binnen de naleving van de organisatie.

## <a name="monitor-storage-services-for-unexpected-changes-in-behavior"></a>Opslagservices controleren op onverwachte wijzigingen in gedrag
Het diagnosticeren en oplossen van problemen in een gedistribueerde toepassing die wordt gehost in een cloudomgeving kan complexer zijn dan in traditionele omgevingen. Toepassingen kunnen worden geïmplementeerd in een PaaS- of IaaS-infrastructuur, on-premises, op een mobiel apparaat of in een combinatie van deze omgevingen. Het netwerkverkeer van uw toepassing kan openbare en particuliere netwerken doorlopen en uw toepassing kan gebruikmaken van meerdere opslagtechnologieën.

U moet continu de opslagservices bewaken die uw toepassing gebruikt voor onverwachte wijzigingen in het gedrag (zoals tragere reactietijden). Gebruik logboekregistratie om gedetailleerdere gegevens te verzamelen en een probleem uitgebreid te analyseren. Aan de hand van de diagnostische gegevens die u verkrijgt van zowel bewaking als logboekregistratie, kunt u de hoofdoorzaak van het probleem vaststellen dat uw toepassing heeft ondervonden. Vervolgens kunt u het probleem oplossen en de juiste stappen bepalen om het probleem op te lossen.

[Azure Opslaganalyse](../../storage/common/storage-analytics.md) voert logboekregistratie uit en biedt metrische gegevens voor een Azure-opslagaccount. U wordt aangeraden deze gegevens te gebruiken om aanvragen te traceren, gebruikstrends te analyseren en problemen met uw opslagaccount te diagnosticeren.

## <a name="prevent-detect-and-respond-to-threats"></a>Bedreigingen voorkomen, detecteren en hierop reageren
[Azure Security Center](../../security-center/security-center-introduction.md) helpt u bedreigingen te voorkomen, te detecteren en hierop te reageren door meer inzicht te bieden in (en controle over) de beveiliging van uw Azure-resources. Het biedt geïntegreerde beveiligingsbewaking en beleidsbeheer voor uw Azure-abonnementen, helpt bedreigingen te detecteren die anders ongemerkt zouden blijven en werkt met verschillende beveiligingsoplossingen.

De gratis laag van Security Center biedt beperkte beveiliging voor alleen uw Azure-resources. De prijscategorie Standard breidt deze mogelijkheden uit naar on-premises en andere clouds. Security Center Standard helpt u beveiligingsproblemen te vinden en op te lossen, toegangs- en toepassingsbesturingselementen toe te passen om schadelijke activiteiten te blokkeren, bedreigingen te detecteren met behulp van analyses en informatie en snel te reageren wanneer deze worden aangevallen. U kunt Security Center Standard de eerste 60 dagen kosteloos proberen. U wordt aangeraden uw [Azure-abonnement te upgraden naar Security Center Standard](../../security-center/security-center-get-started.md).

Gebruik Security Center om een centraal overzicht te krijgen van de beveiligingstoestand van al uw Azure-resources. Controleer in één oogopslag of de juiste beveiligingscontroles zijn ingesteld en correct zijn geconfigureerd, en identificeer snel resources die aandacht nodig hebben.

Security Center kan ook worden geïntegreerd [met Microsoft Defender Advanced Threat Protection (ATP),](../../security-center/security-center-wdatp.md)dat uitgebreide EDR-mogelijkheden (Endpoint Detection and Response) biedt. Met Microsoft Defender ATP-integratie kunt u afwijkingen herkennen. U kunt ook geavanceerde aanvallen op server-eindpunten detecteren en hierop reageren die worden bewaakt door Security Center.

Bijna alle bedrijfsorganisaties hebben een SIEM-systeem (Security Information and Event Management) om opkomende bedreigingen te identificeren door logboekgegevens van apparaten voor het verzamelen van diverse signalen samen te consolideren. De logboeken worden vervolgens geanalyseerd door een systeem voor gegevensanalyse om te bepalen wat 'interessant' is aan de ruis die onvermijdelijk is in alle oplossingen voor het verzamelen en analyseren van logboeken.

[Azure Sentinel](../../sentinel/overview.md) is een schaalbare, cloudeigen OPLOSSING voor beveiligingsinformatie en gebeurtenisbeheer (SIEM) en soar(Security Orchestration Automated Response). Azure Sentinel biedt intelligente beveiligingsanalyses en bedreigingsinformatie via waarschuwingsdetectie, zichtbaarheid van bedreigingen, proactieve opsporing en geautomatiseerde reactie op bedreigingen.

Hier volgen enkele best practices voor het voorkomen, detecteren en reageren op bedreigingen:

**Best practice:** verhoog de snelheid en schaalbaarheid van uw SIEM-oplossing met behulp van een SIEM in de cloud.   
**Detail:** onderzoek de functies en mogelijkheden van [Azure Sentinel](../../sentinel/overview.md) en vergelijk deze met de mogelijkheden van wat u momenteel on-premises gebruikt. Overweeg de Azure Sentinel als deze voldoet aan de SIEM-vereisten van uw organisatie.

**Best practice:** zoek de ernstigste beveiligingsproblemen, zodat u onderzoek kunt prioriteren.   
**Detail:** controleer uw [Azure-veilige score](../../security-center/secure-score-security-controls.md) om de aanbevelingen te zien die het resultaat zijn van de Azure-beleidsregels en -initiatieven die zijn ingebouwd in Azure Security Center. Deze aanbevelingen helpen bij het aanpakken van de belangrijkste risico's, zoals beveiligingsupdates, eindpuntbeveiliging, versleuteling, beveiligingsconfiguraties, ontbrekende WAF, met internet verbonden VM's en nog veel meer.

Met de beveiligingsscore, die is gebaseerd op CIS-controles (Center for Internet Security), kunt u de Azure-beveiliging van uw organisatie benchmarken ten opzichte van externe bronnen. Externe validatie helpt bij het valideren en verrijken van de beveiligingsstrategie van uw team.

**Best practice:** beveilig de beveiligingsstatus van computers, netwerken, opslag- en gegevensservices en toepassingen om potentiële beveiligingsproblemen te ontdekken en te prioriteren.  
**Detail:** volg de [beveiligingsaanbevelingen](../../security-center/security-center-recommendations.md) in Security Center beginnen, met items met de hoogste prioriteit.

**Best practice:** integreer Security Center in uw SIEM-oplossing (Security Information and Event Management).   
**Detail:** de meeste organisaties met een SIEM gebruiken deze als een centrale clearinghouse voor beveiligingswaarschuwingen waarvoor een reactie van een analist is vereist. Verwerkte gebeurtenissen die door Security Center worden gepubliceerd naar het Azure-activiteitenlogboek, een van de logboeken die beschikbaar zijn via Azure Monitor. Azure Monitor biedt een geconsolideerde pijplijn voor het routeren van bewakingsgegevens naar een SIEM-hulpprogramma. Zie [Stream-waarschuwingen voor een SIEM-, SOAR- of IT Service Management-oplossing](../../security-center/export-to-siem.md) voor instructies. Zie Verbinding maken met Azure Sentinel als [u Azure Security Center.](../../sentinel/connect-azure-security-center.md)

**Best practice:** Integreer Azure-logboeken met uw SIEM.   
**Detail:** gebruik [Azure Monitor om gegevens te verzamelen en te exporteren.](../../azure-monitor/overview.md#integrate-and-export-data) Deze praktijk is essentieel voor het inschakelen van onderzoek naar beveiligingsincidents en het online bewaren van logboeken is beperkt. Zie Verbinding maken met gegevensbronnen als Azure Sentinel [gebruikt.](../../sentinel/connect-data-sources.md)

**Best practice:** verlaag uw onderzoeks- en opsporingsprocessen en verminder fout-positieven door de mogelijkheden van Endpoint Detection and Response (EDR) te integreren in uw aanvalsonderzoek.   
**Detail:** [schakel de integratie van Microsoft Defender for Endpoint](../../security-center/security-center-wdatp.md#enable-the-microsoft-defender-for-endpoint-integration) in via uw Security Center beveiligingsbeleid. Overweeg het gebruik Azure Sentinel voor de dreigings- en incidentrespons.

## <a name="monitor-end-to-end-scenario-based-network-monitoring"></a>End-to-end netwerkbewaking op basis van scenario's bewaken
Klanten bouwen een end-to-end netwerk in Azure door netwerkbronnen zoals een virtueel netwerk, ExpressRoute, Application Gateway en load balancers te combineren. Bewaking is beschikbaar op elk van de netwerkbronnen.

[Azure Network Watcher](../../network-watcher/network-watcher-monitoring-overview.md) is een regionale service. Gebruik de hulpprogramma's voor diagnostische gegevens en visualisatie om omstandigheden op netwerkscenarioniveau in, naar en van Azure te bewaken en te diagnosticeren.

Hier volgen best practices voor netwerkbewaking en beschikbare hulpprogramma's.

**Best practice:** Externe netwerkbewaking automatiseren met pakketopname.  
**Details:** netwerkproblemen bewaken en diagnosticeren zonder u aan te melden bij uw VM's met behulp van Network Watcher. Activeer [pakketopname](../../network-watcher/network-watcher-alert-triggered-packet-capture.md) door waarschuwingen in te stellen en toegang te krijgen tot realtime prestatiegegevens op pakketniveau. Wanneer u een probleem ziet, kunt u dit in detail onderzoeken voor betere diagnoses.

**Best practice:** krijg inzicht in uw netwerkverkeer met behulp van stroomlogboeken.  
**Detail:** bouw meer inzicht in uw netwerkverkeerspatronen met behulp van stroomlogboeken [van netwerkbeveiligingsgroep.](../../network-watcher/network-watcher-nsg-flow-logging-overview.md) Informatie in stroomlogboeken helpt u bij het verzamelen van gegevens voor naleving, controle en bewaking van uw netwerkbeveiligingsprofiel.

**Best practice:** Problemen met VPN-connectiviteit vaststellen.  
**Details:** gebruik Network Watcher om de meest voorkomende problemen met VPN Gateway [en verbinding op te sporen.](../../network-watcher/network-watcher-diagnose-on-premises-connectivity.md) U kunt het probleem niet alleen identificeren, maar ook gedetailleerde logboeken gebruiken om verder onderzoek te doen.

## <a name="secure-deployment-by-using-proven-devops-tools"></a>Implementatie beveiligen met bewezen DevOps-hulpprogramma's
Gebruik de volgende best practices voor DevOps om ervoor te zorgen dat uw onderneming en teams productief en efficiënt zijn.

**Best practice:** automatiseer het bouwen en implementeren van services.  
**Detail:** [Infrastructuur als code](/azure/devops/learn/what-is-infrastructure-as-code) is een set technieken en procedures waarmee IT-professionals de dagelijkse belasting van het bouwen en beheren van modulaire infrastructuur kunnen wegnemen. It-professionals kunnen hiermee hun moderne serveromgeving bouwen en onderhouden op een manier die lijkt op hoe softwareontwikkelaars toepassingscode bouwen en onderhouden.

U kunt de [Azure Resource Manager](../../azure-resource-manager/templates/template-syntax.md) gebruiken om uw toepassingen in terichten met behulp van een declaratieve sjabloon. U kunt in één enkele sjabloon meerdere services plus de bijbehorende afhankelijkheden implementeren. U gebruikt dezelfde sjabloon om uw toepassing herhaaldelijk te implementeren in elke fase van de levenscyclus van de toepassing.

**Best practice:** automatisch bouwen en implementeren in Azure-web-apps of cloudservices.  
**Detail:** u kunt uw service configureren Azure DevOps Projects  [automatisch](/azure/devops/pipelines/index) te bouwen en implementeren in Azure-web-apps of cloudservices. Azure DevOps implementeert na elke incheckcode automatisch de binaire bestanden nadat er een build naar Azure is uitgevoerd. Het buildproces van het pakket is gelijk aan de opdracht Package in Visual Studio en de publicatiestappen zijn gelijk aan de opdracht Publish in Visual Studio.

**Best practice:** Releasebeheer automatiseren.  
**Detail:** [Azure Pipelines](/azure/devops/pipelines/index) is een oplossing voor het automatiseren van implementatie in meerdere fases en het beheren van het releaseproces. Maak beheerde pijplijnen voor continue implementatie om snel, eenvoudig en vaak vrij te geven. Met Azure Pipelines kunt u uw releaseproces automatiseren en kunt u vooraf gedefinieerde goedkeuringswerkstromen hebben. Implementeer on-premises en in de cloud, breid uit en pas ze naar wens aan.

**Best practice:** controleer de prestaties van uw app voordat u deze start of updates implementeert voor productie.  
**Detail:** Voer belastingstests in de cloud [uit om:](/azure/devops/test/load-test/overview#alternatives)

- Prestatieproblemen vinden in uw app.
- De kwaliteit van de implementatie verbeteren.
- Zorg ervoor dat uw app altijd beschikbaar is.
- Zorg ervoor dat uw app verkeer kan verwerken voor uw volgende lancerings- of marketingcampagne.

[Apache JMeter](https://jmeter.apache.org/) is een gratis, populaire open source hulpprogramma met een sterke community-backing.

**Best practice:** Be monitor de prestaties van toepassingen.  
**Detail:** [Azure-toepassing Insights](../../azure-monitor/app/app-insights-overview.md) is een extensible APM-service (Application Performance Management) voor webontwikkelaars op meerdere platforms. Gebruik Application Insights om uw live webtoepassing te bewaken. Prestatieafwijkingen worden automatisch gedetecteerd. Het bevat analysehulpprogramma's om u te helpen bij het vaststellen van problemen en om te begrijpen wat gebruikers daadwerkelijk doen met uw app. Het is bedoeld om u te helpen de prestaties en bruikbaarheid continu te verbeteren.

## <a name="mitigate-and-protect-against-ddos"></a>Bescherming tegen DDoS
DDoS (Distributed Denial of Service) is een type aanval dat probeert toepassingsbronnen uit teputten. Het doel is om de beschikbaarheid van de toepassing te beïnvloeden en de mogelijkheid om legitieme aanvragen af te handelen. Deze aanvallen worden steeds geavanceerder en groter in omvang en impact. Ze kunnen worden gericht op elk eindpunt dat openbaar bereikbaar is via internet.

Ontwerpen en bouwen voor DDoS-tolerantie vereist planning en ontwerp voor verschillende foutmodi. Hieronder volgen de best practices voor het bouwen van DDoS-flexibele services in Azure.

**Best practice:** zorg ervoor dat beveiliging een prioriteit is gedurende de hele levenscyclus van een toepassing, van ontwerp en implementatie tot implementatie en bewerkingen. Toepassingen kunnen fouten hebben waardoor een relatief klein aantal aanvragen veel resources kan gebruiken, wat resulteert in een servicestoring.  
**Detail:** als u een service wilt beveiligen die wordt uitgevoerd op Microsoft Azure, moet u een goed begrip hebben van uw toepassingsarchitectuur en zich richten op de vijf pijlers [van softwarekwaliteit.](/azure/architecture/guide/pillars) U moet bekend zijn met typische verkeersvolumes, het connectiviteitsmodel tussen de toepassing en andere toepassingen en de service-eindpunten die worden blootgesteld aan het openbare internet.

Het is belangrijk om ervoor te zorgen dat een toepassing flexibel genoeg is voor het afhandelen van een Denial of Service die is gericht op de toepassing zelf. Beveiliging en privacy zijn ingebouwd in het Azure-platform, te beginnen met [security development lifecycle (SDL).](https://www.microsoft.com/sdl) De SDL richt zich op de beveiliging in elke ontwikkelingsfase en zorgt ervoor dat Azure voortdurend wordt bijgewerkt om het nog veiliger te maken.

**Best practice:** Ontwerp [](/azure/architecture/guide/design-principles/scale-out) uw toepassingen zo dat ze horizontaal worden geschaald om te voldoen aan de vraag naar een verhoogde belasting, met name in het geval van een DDoS-aanval. Als uw toepassing afhankelijk is van één exemplaar van een service, wordt er één storingspunt gemaakt. Het inrichten van meerdere exemplaren maakt uw systeem robuuster en schaalbaarder.  
**Detail:** voor [Azure App Service](../../app-service/overview.md)selecteert u [een App Service abonnement](../../app-service/overview-hosting-plans.md) met meerdere exemplaren.

Voor Azure Cloud Services configureert u elk van uw rollen voor het gebruik [van meerdere exemplaren.](../../cloud-services/cloud-services-choose-me.md)

Voor [Azure Virtual Machines](../../virtual-machines/windows/overview.md)moet u ervoor zorgen dat uw VM-architectuur meer dan één VM bevat en dat elke VM is opgenomen in een [beschikbaarheidsset.](../../virtual-machines/windows/tutorial-availability-sets.md) We raden u aan om virtuele-machineschaalsets te gebruiken voor mogelijkheden voor automatisch schalen.

**Best practice:** Het gelaagd maken van beveiligingsbeveiliging in een toepassing vermindert de kans op een geslaagde aanval. Implementeert beveiligde ontwerpen voor uw toepassingen met behulp van de ingebouwde mogelijkheden van het Azure-platform.  
**Detail:** het risico op aanvallen neemt toe met de grootte (surface area) van de toepassing. U kunt de surface area beperken door een goedkeuringslijst te gebruiken om de beschikbare IP-adresruimte en luisterende poorten te sluiten die niet nodig zijn op de load balancers[(Azure Load Balancer](../../load-balancer/quickstart-load-balancer-standard-public-portal.md) en [Azure Application Gateway).](../../application-gateway/application-gateway-create-probe-portal.md)

[Netwerkbeveiligingsgroepen](../../virtual-network/network-security-groups-overview.md) zijn een andere manier om de kans op aanvallen te verminderen. U kunt [servicetags](../../virtual-network/network-security-groups-overview.md#service-tags) en toepassingsbeveiligingsgroepen gebruiken om de complexiteit voor het maken van beveiligingsregels en het configureren van netwerkbeveiliging te minimaliseren, als een natuurlijke uitbreiding van de structuur van een toepassing. [](../../virtual-network/network-security-groups-overview.md#application-security-groups)

U moet waar mogelijk [Azure-services](../../virtual-network/virtual-networks-overview.md) implementeren in een virtueel netwerk. Met deze praktijk kunnen servicebronnen communiceren via privé-IP-adressen. Azure-serviceverkeer van een virtueel netwerk gebruikt standaard openbare IP-adressen als bron-IP-adressen.

Het [gebruik van service-eindpunten](../../virtual-network/virtual-network-service-endpoints-overview.md) schakelt serviceverkeer om om privéadressen van virtuele netwerken te gebruiken als bron-IP-adressen wanneer ze toegang hebben tot de Azure-service vanuit een virtueel netwerk.

We zien vaak dat de on-premises resources van klanten samen met hun resources in Azure worden aangevallen. Als u een on-premises omgeving verbindt met Azure, minimaliseert u de blootstelling van on-premises resources aan het openbare internet.

Azure heeft twee [DDoS-serviceaanbiedingen](../../ddos-protection/ddos-protection-overview.md) die bescherming bieden tegen netwerkaanvallen:

- Basisbeveiliging is standaard zonder extra kosten geïntegreerd in Azure. De schaal en capaciteit van het wereldwijd geïmplementeerde Azure-netwerk bieden bescherming tegen veelvoorkomende aanvallen op de netwerklaag door middel van bewaking van altijd ingeschakeld verkeer en realtime beperking. Basic vereist geen wijzigingen in de gebruikersconfiguratie of toepassing en helpt bij het beveiligen van alle Azure-services, met inbegrip van PaaS-services zoals Azure DNS.
- Standaardbeveiliging biedt geavanceerde mogelijkheden voor DDoS-risicobeperking tegen netwerkaanvallen. Deze is automatisch afgestemd op het beveiligen van uw specifieke Azure-resources. Beveiliging is eenvoudig in te stellen tijdens het maken van virtuele netwerken. Het kan ook worden uitgevoerd na het maken en vereist geen wijzigingen in de toepassing of resource.

## <a name="enable-azure-policy"></a>Enable Azure Policy
[Azure Policy](../../governance/policy/overview.md) is een service in Azure die u gebruikt om beleidsregels te maken, toe te wijzen en te beheren. Met dit beleid worden regels en effecten afgedwongen voor uw resources, zodat deze resources voldoen aan de standaarden en serviceovereenkomsten van uw bedrijf. Azure Policy voorziet in deze behoefte door uw resources met toegewezen beleid te controleren op niet-naleving.

Schakel Azure Policy om het geschreven beleid van uw organisatie te bewaken en af te dwingen. Dit zorgt voor naleving van de beveiligingsvereisten van uw bedrijf of regelgeving door het beveiligingsbeleid voor uw hybride cloudworkloads centraal te beheren. Meer informatie over het maken [en beheren van beleid om naleving af te dwingen.](../../governance/policy/tutorials/create-and-manage.md) Zie [Azure Policy definitiestructuur voor](../../governance/policy/concepts/definition-structure.md) een overzicht van de elementen van een beleid.

Hier volgen enkele best practices voor beveiliging die u moet volgen nadat u de Azure Policy:

**Best practice:** Beleid ondersteunt verschillende soorten effecten. U kunt er meer over lezen in [Azure Policy definitiestructuur](../../governance/policy/concepts/definition-structure.md#policy-rule). Bedrijfsactiviteiten kunnen negatief worden beïnvloed door het weigerende **effect** en het hersteleffect. Begin daarom met het **controle-effect**  om het risico op negatieve gevolgen van het beleid te beperken.   
**Detail:** [start beleidsimplementaties in de controlemodus](../../governance/policy/concepts/definition-structure.md#policy-rule) en ga vervolgens verder om **te weigeren** of **te herstellen.** Test en bekijk de resultaten van het  controle-effect voordat u gaat weigeren **of herstellen.**

Zie Beleid maken en beheren om naleving af [te dwingen voor meer informatie.](../../governance/policy/tutorials/create-and-manage.md)

**Best practice:** identificeer de rollen die verantwoordelijk zijn voor het controleren op beleidsschendingen en zorg ervoor dat snel de juiste herstelactie wordt ondernomen.   
**Detail:** de toegewezen rol naleving laten bewaken via de [Azure Portal](../../governance/policy/how-to/get-compliance-data.md#portal) of via de [opdrachtregel](../../governance/policy/how-to/get-compliance-data.md#command-line).

**Best practice:** Azure Policy is een technische weergave van het geschreven beleid van een organisatie. Wijs alle Azure Policy toe aan organisatiebeleid om verwarring te verminderen en de consistentie te vergroten.   
**Detail:** Documenttoewijzing in de documentatie van uw organisatie of in de Azure Policy-definitie [](../../governance/policy/concepts/definition-structure.md#display-name-and-description) zelf door een verwijzing naar het organisatiebeleid toe te voegen in de beleidsdefinitie of de beschrijving van de [initiatiefdefinitie.](../../governance/policy/concepts/initiative-definition-structure.md#metadata)

## <a name="monitor-azure-ad-risk-reports"></a>Azure AD-risicorapporten bewaken
Het overgrote deel van de beveiligingsschending vindt plaats wanneer aanvallers toegang krijgen tot een omgeving door de identiteit van een gebruiker te stelen. Het detecteren van aangetaste identiteiten is geen eenvoudige taak. Azure AD maakt gebruik van machine learning algoritmen en heuristieken voor het detecteren van verdachte acties die zijn gerelateerd aan uw gebruikersaccounts. Elke gedetecteerde verdachte actie wordt opgeslagen in een record die [risicodetectie wordt genoemd.](../../active-directory/identity-protection/overview-identity-protection.md) Risicodetecties worden vastgelegd in Azure AD-beveiligingsrapporten. Lees voor meer informatie over het beveiligingsrapport [over gebruikers die](../../active-directory/identity-protection/overview-identity-protection.md) risico lopen en het beveiligingsrapport over [riskante aanmeldingen.](../../active-directory/identity-protection/overview-identity-protection.md)

## <a name="next-steps"></a>Volgende stappen
Zie [Best practices en patronen](best-practices-and-patterns.md) voor Azure-beveiliging voor meer best practices voor beveiliging die u kunt gebruiken bij het ontwerpen, implementeren en beheren van uw cloudoplossingen met behulp van Azure.

De volgende resources zijn beschikbaar voor meer algemene informatie over azure-beveiliging en gerelateerde Microsoft-services:
* [Blog van het Azure-beveiligingsteam:](/archive/blogs/azuresecurity/) voor up-to-date informatie over de nieuwste versie van Azure Security
* [Microsoft Security Response Center:](https://technet.microsoft.com/library/dn440717.aspx) waar beveiligingsproblemen van Microsoft, waaronder problemen met Azure, kunnen worden gerapporteerd of via e-mail naar secure@microsoft.com