---
title: Detectie van Azure Advanced Threat | Microsoft Docs
description: Meer informatie over de ingebouwde geavanceerde detectie functie voor bedreigingen voor Azure, zoals de Azure AD Identity Protection-Service.
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
ms.date: 01/24/2021
ms.author: TomSh
ms.openlocfilehash: c8fbb2f6d858b2f654ff404bef3b415bf170ab37
ms.sourcegitcommit: 3c8964a946e3b2343eaf8aba54dee41b89acc123
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/25/2021
ms.locfileid: "98747270"
---
# <a name="azure-advanced-threat-detection"></a>Geavanceerde bedreigingsdetectie van Azure

Azure biedt ingebouwde functionaliteit voor de detectie van geavanceerde bedreigingen via Services als Azure Active Directory (Azure AD), Azure Monitor-logboeken en Azure Security Center. Deze verzameling beveiligings Services en-mogelijkheden biedt een eenvoudige en snelle manier om te begrijpen wat er gebeurt in uw Azure-implementaties.

Azure biedt een breed scala aan opties voor het configureren en aanpassen van de beveiliging om te voldoen aan de vereisten van uw app-implementaties. In dit artikel wordt beschreven hoe u aan deze vereisten voldoet.

## <a name="azure-active-directory-identity-protection"></a>Azure Active Directory Identity Protection

[Azure AD Identity Protection](../../active-directory/identity-protection/overview-identity-protection.md) is een functie van een [Azure Active Directory Premium P2](../../active-directory/fundamentals/active-directory-whatis.md) -editie die een overzicht geeft van de risico detecties en mogelijke beveiligings problemen die van invloed kunnen zijn op de identiteiten van uw organisatie. Identiteits beveiliging maakt gebruik van bestaande Azure AD anomalie detectie mogelijkheden die beschikbaar zijn via [Azure AD afwijkende activiteiten rapporten](../../active-directory/reports-monitoring/overview-reports.md)en introduceert nieuwe typen risico detectie waarmee realtime afwijkingen kunnen worden gedetecteerd.

![Azure AD Identity Protection diagram](./media/threat-detection/azure-threat-detection-fig1.png)

Identiteits beveiliging maakt gebruik van adaptieve machine learning algoritmen en heuristieken voor het detecteren van afwijkingen en risico detecties die kunnen erop wijzen dat er is geknoeid met een identiteit. Met behulp van deze gegevens genereren identiteits beveiliging rapporten en waarschuwingen, zodat u deze risico detecties kunt onderzoeken en de juiste herstel-of risicoieve actie moet ondernemen.

Azure Active Directory Identity Protection is meer dan een hulp programma voor bewaking en rapportage. Op basis van risico detectie berekent de identiteits beveiliging een gebruikers risico niveau voor elke gebruiker, zodat u beleids regels op basis van Risico's kunt configureren om automatisch de identiteiten van uw organisatie te beveiligen.

Deze beleids regels op basis van Risico's, naast andere [besturings elementen voor voorwaardelijke toegang](../../active-directory/conditional-access/overview.md) die worden geleverd door Azure Active Directory en [EMS](../../active-directory/conditional-access/overview.md), kunnen automatisch adaptieve herstel acties blok keren of aanbieden die het opnieuw instellen van wacht woorden en het afdwingen van multi-factor Authentication omvatten.

### <a name="identity-protection-capabilities"></a>Mogelijkheden voor identiteits beveiliging

Azure Active Directory Identity Protection is meer dan een hulp programma voor bewaking en rapportage. Als u de identiteit van uw organisatie wilt beveiligen, kunt u op risico gebaseerd beleid configureren dat automatisch reageert op gedetecteerde problemen wanneer een opgegeven risico niveau is bereikt. Deze beleids regels, naast andere besturings elementen voor voorwaardelijke toegang van Azure Active Directory en EMS, kunnen automatisch adaptieve herstel acties blok keren of initiëren, waaronder het opnieuw instellen van wacht woorden en het afdwingen van multi-factor Authentication.

Voor beelden van een aantal manieren waarop Azure Identity Protection uw accounts en identiteiten kan beveiligen, zijn onder andere:

[Risico detecties en Risk ante accounts detecteren](../../active-directory/identity-protection/overview-identity-protection.md)
-   Detecteer zes typen risico detectie met behulp van machine learning en heuristische regels.
-   Gebruikers risico niveaus berekenen.
-   Geef aangepaste aanbevelingen voor het verbeteren van de algemene beveiligings postuur door beveiligings problemen te markeren.

[Risico detecties onderzoeken](../../active-directory/identity-protection/overview-identity-protection.md)
-   Verzend meldingen voor risico detecties.
-   Onderzoek risico detecties met behulp van relevante en contextuele informatie.
-   Geef eenvoudige werk stromen voor het bijhouden van onderzoeken.
-   Bied eenvoudige toegang tot herstel acties, zoals het opnieuw instellen van wacht woorden.

[Beleid voor voorwaardelijke toegang op basis van een risico](../../active-directory/identity-protection/overview-identity-protection.md)
-   Risk ante aanmeldingen beperken door aanmeldingen te blok keren of om multi-factor Authentication-uitdagingen te vereisen.
-   Risk ante gebruikers accounts blok keren of beveiligen.
-   Vereisen dat gebruikers zich registreren voor multi-factor Authentication.

### <a name="azure-ad-privileged-identity-management"></a>Azure AD Privileged Identity Management

Met [Azure Active Directory privileged Identity Management (PIM)](../../active-directory/privileged-identity-management/pim-configure.md)kunt u toegang beheren, controleren en bewaken in uw organisatie. Deze functie biedt toegang tot resources in azure AD en andere micro soft-onlineservices, zoals Microsoft 365 of Microsoft Intune.

![Azure AD Privileged Identity Management diagram](./media/threat-detection/azure-threat-detection-fig2.png)

Met PIM kunt u het volgende doen:

-   Ontvang waarschuwingen en rapporten over Azure AD-beheerders en just-in-time (JIT) beheerders toegang tot micro soft onlineservices, zoals Microsoft 365 en intune.

-   Rapporten over de geschiedenis van beheerders toegang en wijzigingen in beheerders toewijzingen ophalen.

-   Ontvang waarschuwingen over toegang tot een bevoorrechte rol.

## <a name="azure-monitor-logs"></a>Azure Monitor-logboeken

[Azure monitor-logboeken](../../azure-monitor/index.yml) is een micro soft-Cloud oplossing voor IT-beheer die u helpt bij het beheren en beveiligen van uw on-premises en Cloud infrastructuur. Omdat Azure Monitor-Logboeken wordt geïmplementeerd als een Cloud service, kunt u deze snel uitvoeren met minimale investeringen in infrastructuur services. Nieuwe beveiligings functies worden automatisch geleverd, waardoor voortdurend onderhoud en upgrade kosten worden bespaard.

Naast het bieden van waardevolle services zelf, kunnen Azure Monitor logboeken worden geïntegreerd met System Center-onderdelen, zoals [System Center Operations Manager](/archive/blogs/cbernier/monitoring-windows-azure-with-system-center-operations-manager-2012-get-me-started), om uw bestaande beveiligings beheer-investeringen in de Cloud uit te breiden. System Center-en Azure Monitor-logboeken kunnen samen werken om een volledige hybride beheer ervaring te bieden.

### <a name="holistic-security-and-compliance-posture"></a>Holistische beveiligings-en nalevings postuur

Het [Log Analytics beveiliging en audit-dash board](../../security-center/security-center-introduction.md) biedt een uitgebreid overzicht van de IT-beveiligings postuur van uw organisatie, met ingebouwde Zoek query's voor de belangrijkste problemen die uw aandacht vereisen. Het Beveiliging en audit-dash board is het begin scherm voor alles wat te maken heeft met beveiliging in Azure Monitor-Logboeken. Het zorgt voor hoogwaardig inzicht in de beveiligingsstatus van uw computers. U kunt ook alle gebeurtenissen van de afgelopen 24 uur, 7 dagen of een andere aangepaste periode bekijken.

Azure Monitor logboeken helpen u snel en eenvoudig inzicht te krijgen in de algemene beveiligings postuur van elke omgeving, in de context van IT-bewerkingen, inclusief software-update beoordeling, antimalware-evaluatie en configuratie basislijnen. Beveiligings logboek gegevens zijn gemakkelijk toegankelijk om de beveiligings-en nalevings controle processen te stroom lijnen.

![Het Log Analytics Beveiliging en audit dash board](./media/threat-detection/azure-threat-detection-fig3.jpg)

Het Log Analytics Beveiliging en audit dash board is onderverdeeld in vier hoofd categorieën:

-   **Beveiligings domeinen**: Hiermee kunt u beveiligings records na verloop van tijd verkennen. toegang tot malware-evaluaties; Update-evaluaties; informatie over netwerk beveiliging, identiteit en toegang weer geven; computers weer geven met beveiligings gebeurtenissen; en snel toegang tot het dash board van Azure Security Center.

-   Opvallende **problemen**: Hiermee kunt u snel het aantal actieve problemen en de ernst van de problemen identificeren.

-   **Detecties (preview)**: Hiermee kunt u aanvals patronen identificeren door beveiligings waarschuwingen weer te geven die voor uw resources worden uitgevoerd.

-   **Threat Intelligence**: Hiermee kunt u aanvals patronen identificeren door het totale aantal servers met uitgaand schadelijk IP-verkeer, het type schadelijke dreigingen en een kaart van de ip's-locaties weer te geven.

-   **Veelvoorkomende beveiligings query's**: bevat een lijst met de meest voorkomende beveiligings query's die u kunt gebruiken om uw omgeving te bewaken. Wanneer u een query selecteert, wordt het zoek venster geopend en worden de resultaten voor de query weer gegeven.

### <a name="insight-and-analytics"></a>Inzicht en analyse
In het midden van [Azure monitor logboeken](../../azure-monitor/log-query/log-query-overview.md) bevindt zich de opslag plaats, die wordt gehost door Azure.

![Inzicht-en analyse diagram](./media/threat-detection/azure-threat-detection-fig4.png)

U kunt gegevens in de opslag plaats verzamelen van verbonden bronnen door gegevens bronnen te configureren en oplossingen toe te voegen aan uw abonnement.

![Het dash board Azure Monitor logboeken](./media/threat-detection/azure-threat-detection-fig5.png)

Gegevens bronnen en oplossingen maken elk afzonderlijke record typen met hun eigen set eigenschappen, maar u kunt ze wel samen analyseren in query's naar de opslag plaats. U kunt dezelfde hulpprogram ma's en methoden gebruiken om te werken met een verscheidenheid aan gegevens die door verschillende bronnen worden verzameld.


De meeste interactie met Azure Monitor Logboeken is via de Azure Portal, die in een browser wordt uitgevoerd en die u toegang biedt tot configuratie-instellingen en meerdere hulpprogram ma's om verzamelde gegevens te analyseren en er actie op te ondernemen. Vanuit de portal kunt u het volgende gebruiken:
* [Zoek opdrachten in Logboeken](../../azure-monitor/log-query/log-query-overview.md) waar u query's samen stelt om verzamelde gegevens te analyseren.
* [Dash boards](../../azure-monitor/learn/tutorial-logs-dashboards.md), die u kunt aanpassen met grafische weer gaven van uw meest waardevolle Zoek opdrachten.
* [Oplossingen](../../azure-monitor/insights/solutions.md)die aanvullende functionaliteit en analyse hulpprogramma's bieden.

![Analyse hulpprogramma's](./media/threat-detection/azure-threat-detection-fig6.png)

Oplossingen voegen functionaliteit toe aan Azure Monitor Logboeken. Ze worden voornamelijk uitgevoerd in de Cloud en bieden een analyse van de gegevens die worden verzameld in de log Analytics-opslag plaats. Oplossingen kunnen ook nieuwe record typen definiëren die kunnen worden geanalyseerd met Zoek opdrachten in Logboeken of door een aanvullende gebruikers interface te gebruiken die door de oplossing in het dash board van log Analytics wordt geboden.

Het Beveiliging en audit-dash board is een voor beeld van deze typen oplossingen.

### <a name="automation-and-control-alert-on-security-configuration-drifts"></a>Automatisering en controle: waarschuwing over de beveiligings configuratie van drift

Azure Automation automatiseert beheer processen met runbooks die zijn gebaseerd op Power shell en worden uitgevoerd in de Cloud. Runbooks kunnen ook worden uitgevoerd op een server in uw lokale datacentrum om lokale resources te beheren. Azure Automation biedt configuratie beheer met desired state Configuration (DSC) van Power shell.

![Azure Automation diagram](./media/threat-detection/azure-threat-detection-fig7.png)

U kunt DSC-resources maken en beheren die worden gehost in Azure en deze Toep assen op Cloud-en on-premises systemen. Door dit te doen, kunt u de configuratie definiëren en automatisch afdwingen of rapporten over drift ophalen om ervoor te zorgen dat de beveiligings configuraties binnen het beleid blijven.

## <a name="azure-security-center"></a>Azure Security Center

Azure Security Center helpt bij het beschermen van uw hybride cloud omgeving. Door continue beveiligings beoordelingen van uw verbonden bronnen uit te voeren, kunnen er gedetailleerde beveiligings aanbevelingen worden geboden voor de ontdekte beveiligings problemen.

De aanbevelingen van Security Center zijn gebaseerd op de [Azure Security Bench Mark](../benchmarks/introduction.md) -de door micro soft ontworpen, specifieke set richt lijnen voor beveiliging en naleving aanbevolen procedures op basis van algemene nalevings kaders. Dit algemeen gerespecteerde Bench Mark bouwt voort op de besturings elementen van het [Center voor Internet Security (CIS)](https://www.cisecurity.org/benchmark/azure/) en het [National Institute of Standards and Technology (NIST)](https://www.nist.gov/) met een focus op Cloud gerichte beveiliging.

Het geïntegreerde CWPP (Cloud workload Protection platform) van Security Center, **Azure Defender**, biedt geavanceerde, intelligente beveiliging van uw Azure-en hybride bronnen en workloads. Het inschakelen van Azure Defender biedt diverse extra beveiligings functies (Zie [Inleiding tot Azure Defender](../../security-center/azure-defender.md)). Het Azure Defender-dashboard in Security Center biedt inzicht in en controle over de CWP-functies voor uw omgeving:

:::image type="content" source="../../security-center/media/azure-defender/sample-defender-dashboard.png" alt-text="Een voorbeeld van het Azure Defender-dashboard" lightbox="../../security-center/media/azure-defender/sample-defender-dashboard.png":::

Beveiligingsonderzoekers van Microsoft zijn voortdurend op zoek naar bedreigingen. Ze hebben toegang tot een uitgebreide set telemetrie, afkomstig van de wereldwijde aanwezigheid van Microsoft in de cloud en on-premises. Deze verrijkende en uitgebreide verzameling gegevenssets stelt Microsoft in staat om nieuwe aanvalspatronen en -trends te ontdekken in on-premises producten voor consumenten en ondernemingen, evenals in haar online services.

Security Center kunt de detectie algoritmen snel bijwerken wanneer aanvallers nieuwe en steeds meer geavanceerde cracks uitbrengen. Deze benadering helpt u bij het bedenken van een snel bewegende bedreigings omgeving.

:::image type="content" source="../../security-center/media/security-center-managing-and-responding-alerts/alerts-page.png" alt-text="Lijst met beveiligings waarschuwingen voor Azure Security Center":::

Azure Defender verzamelt automatisch beveiligings gegevens van uw resources, het netwerk en verbonden partner oplossingen. Het analyseert deze informatie en correleert informatie uit meerdere bronnen om bedreigingen te identificeren.

De waarschuwingen van Azure Defender worden in Security Center beschreven, samen met aanbevelingen voor het oplossen van de bedreigingen.

Security Center maakt gebruik van geavanceerde beveiligingsanalyses die veel verder gaan dan op handtekeningen gebaseerde benaderingen. Door braak in big data en [machine learning](https://azure.microsoft.com/blog/machine-learning-in-azure-security-center/) technologieën worden gebruikt om gebeurtenissen in de hele Cloud te evalueren. Geavanceerde analyse kan bedreigingen detecteren die onmogelijk zouden kunnen worden geïdentificeerd via hand matige benaderingen en de ontwikkeling van aanvallen voors pellen. Deze typen beveiligings analyse worden in de volgende secties besproken.

### <a name="threat-intelligence"></a>Informatie over bedreigingen

Micro soft heeft toegang tot een enorme hoeveelheid wereld wijde bedreigings informatie.

Telemetrie loopt over van meerdere bronnen, zoals Azure, Microsoft 365, micro soft CRM Online, micro soft Dynamics AX, outlook.com, MSN.com, de micro soft Digital misdrijven Unit (DCU) en het micro soft Security Response Center (MSRC).

![Resultaten van bedreigings informatie](./media/threat-detection/azure-threat-detection-fig10.jpg)

Onderzoekers ontvangen ook informatie over bedreigingen die worden gedeeld met belang rijke Cloud serviceproviders en die zich abonneren op bedreigings informatie-feeds van derden. Azure Security Center kan deze informatie gebruiken om u te waarschuwen over bedreigingen van bekende beveiligingsrisico’s. Voorbeelden zijn:

-   **Het benutten van de kracht van machine learning**: Azure Security Center heeft toegang tot een grote hoeveelheid gegevens over Cloud netwerk activiteit, die kan worden gebruikt om bedreigingen te detecteren die zijn gericht op uw Azure-implementaties.

-   **Detectie van brute kracht**: machine learning wordt gebruikt voor het maken van een historisch patroon van externe toegangs pogingen, waarmee het mogelijk is om beveiligings aanvallen te detecteren op basis van beveiliging van de SSH-, Remote Desktop Protocol (RDP) en SQL-poorten.

-   **Uitgaande DDOS-en botnet-detectie**: een gemeen schappelijke doel stelling van aanvallen om cloud resources te gebruiken, is het gebruik van de reken kracht van deze resources om andere aanvallen uit te voeren.

-   **Nieuwe gedrags Analytics-servers en-vm's**: wanneer er is geknoeid met een server of virtuele machine, gebruiken aanvallers een groot aantal technieken voor het uitvoeren van schadelijke code op dat systeem, terwijl detectie wordt voor komen, behoud van persistentie en waardoor-beveiligings controles.

-   **Azure SQL database detectie van dreigingen**: detectie van bedreigingen voor Azure SQL database, waarmee afwijkende database activiteiten worden geïdentificeerd die duiden op ongebruikelijke en potentieel schadelijke pogingen om toegang te krijgen tot data bases of deze te gebruiken.

### <a name="behavioral-analytics"></a>Gedragsanalyse

Gedragsanalyse is een techniek waarbij gegevens worden geanalyseerd en vergeleken met een verzameling bekende patronen. Deze patronen zijn echter geen eenvoudige handtekeningen. Ze worden vastgesteld aan de hand van complexe machine learning-algoritmen die worden toegepast op grote gegevenssets.

![Resultaten van gedrags analyses](./media/threat-detection/azure-threat-detection-fig11.jpg)

De patronen worden ook bepaald door een zorgvuldige analyse van schadelijk gedrag van deskundige analisten. Azure Security Center kunt gedrags analyse gebruiken om gemanipuleerde resources te identificeren op basis van de analyse van logboeken van virtuele machines, logboeken voor virtuele netwerk apparaten, infrastructuur logboeken, crash dumps en andere bronnen.

Daarnaast worden patronen gecorreleerd met andere signalen om te controleren op ondersteunende bewijzen van een veralgemeende campagne. Aan de hand van dit verband kan beter worden vastgesteld welke gebeurtenissen samenhangen met de opgestelde indicatoren van inbreuk.

Voorbeelden zijn:
-   **Uitvoering van verdachte processen**: de aanvallers maken gebruik van verschillende methoden om schadelijke software uit te voeren zonder te worden gedetecteerd. Een aanvaller kan bijvoorbeeld schadelijke software dezelfde namen geven als legitieme systeem bestanden, maar deze bestanden op een andere locatie plaatsen, een naam gebruiken die vergelijkbaar is met die van een goed aardig bestand of de extensie van het bestand maskeren. Security Center modellen proces gedrag en bewaak proces uitvoeringen om uitbijters te detecteren, zoals deze.

-   **Verborgen malware en exploitatie pogingen**: geavanceerde malware kan omzeilen van traditionele antimalware door nooit naar schijf te schrijven of software onderdelen te versleutelen die op schijf zijn opgeslagen. Dergelijke schadelijke software kan echter worden gedetecteerd met behulp van geheugen analyse, omdat de malware traceringen in het geheugen moet laten functioneren. Wanneer software vastloopt, bevat een crashdump een gedeelte van het geheugen ten tijde van de crash. Door het geheugen in de crash dump te analyseren, kunt Azure Security Center technieken detecteren die worden gebruikt voor het exploiteren van beveiligings problemen in software, toegang krijgen tot vertrouwelijke gegevens en ongemerkt blijven bestaan binnen een getroffen machine zonder dat dit van invloed is op de prestaties van uw machine.

-   **Zijdelingse verplaatsing en interne Reconnaissance**: als u in een aangetast netwerk wilt blijven en waardevolle gegevens wilt zoeken en verzamelen, trachten de aanvallers vaak later van de geïnfecteerde machine naar anderen binnen hetzelfde netwerk te gaan. Met Security Center worden proces-en aanmeldings activiteiten gecontroleerd om pogingen tot het uitbreiden van de aanvaller binnen van een aanvaller binnen het netwerk te detecteren, zoals het uitvoeren van externe opdrachten, het zoeken van netwerken en het inventariseren van accounts.

-   **Schadelijke Power shell-scripts**: Power shell kan door aanvallers worden gebruikt om schadelijke code uit te voeren op virtuele doel machines voor verschillende doel einden. Security Center inspecteert PowerShell-activiteit op tekenen van verdachte activiteiten.

-   **Uitgaande aanvallen**: aanvallers richten zich vaak op cloudresources met het doel deze resources te gebruiken voor het uitvoeren van nieuwe aanvallen. Gemanipuleerde virtuele machines kunnen bijvoorbeeld worden gebruikt voor het starten van beveiligings aanvallen op andere virtuele machines, het verzenden van spam of het scannen van open poorten en andere apparaten op internet. Door machine learning toe te passen op netwerkverkeer kan Security Center het detecteren wanneer uitgaande netwerkcommunicatie groter is dan de norm. Als er spam wordt gedetecteerd, geeft Security Center ook ongebruikelijk e-mail verkeer met intelligentie van Microsoft 365 aan om te bepalen of het e-mail bericht waarschijnlijk kwaadwillend is of het resultaat is van een legitieme e-mail campagne.

### <a name="anomaly-detection"></a>Afwijkingsdetectie

Azure Security Center maakt ook gebruik van afwijkingsdetectie om bedreigingen te identificeren. In tegenstelling tot gedragsanalyses (die afhankelijk zijn van bekende patronen die zijn afgeleid van grote gegevenssets) is afwijkingsdetectie meer "gepersonaliseerd" en richt het zich op basislijnen die specifiek voor uw implementaties zijn. Machine learning wordt toegepast om de normale activiteiten voor uw implementaties te bepalen en vervolgens worden er regels gegenereerd om uitschieter-voor waarden te definiëren die een beveiligings gebeurtenis kunnen vertegenwoordigen. Hier volgt een voorbeeld:

-   **Inkomende RDP/SSH-** aanvals aanvallen: uw implementaties hebben mogelijk drukke virtuele machines met veel aanmeldingen per dag en andere virtuele machines met weinig, indien van toepassing, aanmeldingen. Azure Security Center kunt de baseline-aanmeldings activiteit voor deze virtuele machines bepalen en machine learning gebruiken om de normale aanmeldings activiteiten te definiëren. Als er een verschil is met de basis lijn die is gedefinieerd voor aan login gerelateerde kenmerken, wordt er mogelijk een waarschuwing gegenereerd. Ook hier weer wordt door machine learning bepaald wat een aanzienlijk verschil is.

### <a name="continuous-threat-intelligence-monitoring"></a>Doorlopende controle van informatie over bedreigingen

Azure Security Center werkt met beveiligings onderzoek en data wetenschappen teams overal ter wereld die voortdurend te controleren op wijzigingen in de bedreigings instellingen. Dit omvat de volgende initiatieven:

-   **Threat Intelligence-bewaking**: bedreigings informatie omvat mechanismen, indica toren, implicaties en bruikbare adviezen over bestaande of opkomende bedreigingen. Deze informatie wordt gedeeld in de beveiligings Community, en micro soft bewaakt voortdurend bedreigings informatie-feeds van interne en externe bronnen.

-   **Signaal deling**: inzichten van beveiligings teams in de brede micro soft-portefeuille van Cloud-en on-premises Services, servers en client eindpunt apparaten worden gedeeld en geanalyseerd.

-   **Micro soft-beveiligings specialisten**: lopende betrokkenheid bij teams in micro soft die werken in gespecialiseerde beveiligings velden, zoals forensische en detectie van webaanval.

-   **Detectie afstemmen**: algoritmen worden uitgevoerd op basis van echte klant gegevens sets en beveiligings onderzoekers werken samen met klanten om de resultaten te valideren. Echte en fout-positieven worden gebruikt voor het verfijnen van machine learning-algoritmen.

Deze gecombineerde inspanningen culminate in nieuwe en verbeterde detecties, die u direct kunt voor delen. U hoeft geen actie te ondernemen.

## <a name="advanced-threat-detection-features-other-azure-services"></a>Geavanceerde functies voor detectie van bedreigingen: andere Azure-Services

### <a name="virtual-machines-microsoft-antimalware"></a>Virtuele machines: micro soft antimalware

[Micro soft antimalware](antimalware.md) voor Azure is een oplossing voor toepassingen en Tenant omgevingen met één agent, ontworpen om op de achtergrond te worden uitgevoerd zonder menselijke tussen komst. U kunt beveiliging implementeren op basis van de behoeften van uw toepassings werkbelastingen, met een basis beveiliging van standaard instellingen of geavanceerde aangepaste configuratie, waaronder bewaking van anti-malware. Azure antimalware is een beveiligings optie voor virtuele Azure-machines die automatisch worden geïnstalleerd op alle virtuele machines van Azure PaaS.

#### <a name="microsoft-antimalware-core-features"></a>Kern functies van micro soft antimalware

Hier vindt u de functies van Azure voor het implementeren en inschakelen van micro soft antimalware voor uw toepassingen:

-   **Realtime-beveiliging**: controleert activiteit in Cloud Services en op virtuele machines om de uitvoering van schadelijke software te detecteren en blok keren.

-   **Gepland scannen**: voert periodiek gerichte scans uit voor het detecteren van malware, waaronder actieve Program ma's.

-   **Herstel van malware**: werkt automatisch op gedetecteerde malware, zoals het verwijderen of in quarantaine van schadelijke bestanden en het opschonen van schadelijke Register vermeldingen.

-   **Handtekening updates**: installeert automatisch de nieuwste beveiligings handtekeningen (virus definities) om ervoor te zorgen dat de beveiliging up-to-date is op een vooraf ingestelde frequentie.

-   **Antimalware engine updates**: werkt automatisch de micro soft-antimalware engine bij.

-   **Updates van het antimalware**-platform: Hiermee wordt het micro soft antimalware-platform automatisch bijgewerkt.

-   **Actieve beveiliging**: Hiermee wordt een telemetrie-meta gegevens over gedetecteerde bedreigingen en verdachte bronnen gerapporteerd om Microsoft Azure te zorgen voor een snelle reactie op het groeiende risico op de concurrentie, waardoor synchrone hand tekeningen in realtime kunnen worden geleverd via het micro soft Active Protection-systeem.

-   Voor **beelden van rapportage**: biedt en rapport voorbeelden aan de micro soft-antimalwareservice om de service te verfijnen en probleem oplossing in te scha kelen.

-   **Uitzonde ringen**: Hiermee kunnen toepassings-en service beheerders bepaalde bestanden, processen en stations configureren voor uitsluiting van beveiliging en scannen op prestaties en andere redenen.

-   **Gebeurtenis verzameling voor antimalware**: registreert de status van de antimalware-service, verdachte activiteiten en herstel bewerkingen die zijn uitgevoerd in het gebeurtenis logboek van het besturings systeem en verzamelt deze in het Azure-opslag account van de klant.

### <a name="azure-sql-database-threat-detection"></a>Detectie van Azure SQL Database-dreigingen

[Azure SQL database detectie van bedreigingen](https://azure.microsoft.com/blog/azure-sql-database-threat-detection-your-built-in-security-expert/) is een nieuwe functie voor beveiligings informatie die is ingebouwd in de Azure SQL database-service. Het omzeilen van de klok om afwijkende database activiteiten te leren, te profileren en te detecteren, Azure SQL Database detectie van bedreigingen identificeert mogelijke dreigingen voor de data base.

Beveiligings ambtenaren of andere aangewezen beheerders kunnen een onmiddellijke melding krijgen over verdachte database activiteiten wanneer deze optreden. Elke melding bevat details over de verdachte activiteit en raadt u aan om de dreiging verder te onderzoeken en te beperken.

Azure SQL Database detectie van bedreigingen detecteert momenteel mogelijke beveiligings problemen en SQL-injectie aanvallen en afwijkende patronen voor database toegang.

Wanneer gebruikers een e-mail melding met een bedreigings detectie ontvangen, kunnen ze in de e-mail navigeren en de relevante controle records weer geven via een diepe koppeling. De koppeling opent een controle-Viewer of een vooraf geconfigureerde Excel-sjabloon voor controle die de relevante audit records rond het tijdstip van de verdachte gebeurtenis weergeeft, op basis van het volgende:

-   Controleer opslag voor de data base/server met de afwijkende database activiteiten.

-   Relevante audit Storage-tabel die is gebruikt op het moment van de gebeurtenis om het audit logboek te schrijven.

-   Controleert records van het uur direct na het gebeurtenis voorval.

-   Audit records met een vergelijk bare gebeurtenis-ID op het moment van de gebeurtenis (optioneel voor sommige Detectors).

SQL Database bedreigings detecties gebruiken een van de volgende detectie methoden:

-   **Deterministische detectie**: detecteert verdachte patronen (op regels gebaseerd) in de SQL-Client query's die overeenkomen met bekende aanvallen. Deze methodologie heeft een hoge detectie en een lage fout-positief, maar een beperkte dekking omdat deze binnen de categorie "atomische detecties" valt.

-   **Gedrags detectie**: detecteert afwijkende activiteit, die abnormaal gedrag in de data base is die gedurende de laatste 30 dagen niet is gedetecteerd. Voor beelden van afwijkende activiteiten van SQL client kunnen een piek zijn bij mislukte aanmeldingen of query's, een groot aantal gegevens dat wordt geëxtraheerd, ongebruikelijke canonieke query's of onbekende IP-adressen die worden gebruikt voor toegang tot de data base.

### <a name="application-gateway-web-application-firewall"></a>Application Gateway Web Application firewall

[Web Application firewall (WAF)](../../app-service/environment/app-service-app-service-environment-web-application-firewall.md) is een functie van [Azure-toepassing gateway](../../web-application-firewall/ag/ag-overview.md) die beveiliging biedt voor webtoepassingen die gebruikmaken van een toepassings gateway voor standaard functies voor [het bezorgen](https://kemptechnologies.com/in/application-delivery-controllers) van de levering van toepassingen. Firewall voor webtoepassingen doet dit door ze te beschermen tegen de meeste van de [OWASP (open Web Application Security project) van de Top 10 van veelvoorkomende webproblemen](https://owasp.org/www-project-top-ten/).

![Firewall diagram van webtoepassing Application Gateway](./media/threat-detection/azure-threat-detection-fig13.png)

Beveiligingen omvatten:

-   Beveiliging van SQL-injectie.

-   Beveiliging van scripts op meerdere sites.

-   Veelvoorkomende webaanvals beveiliging, zoals het invoegen van opdrachten, HTTP-aanvragen smuggling, het splitsen van HTTP-antwoorden en het insluit van externe bestanden.

-   Beveiliging tegen schendingen van het HTTP-protocol.

-   Beveiliging tegen afwijkingen van het HTTP-protocol, zoals het ontbreken van een gebruikersagent voor de host en Accept-headers.

-   Voor komen tegen bots, crawlers en scanners.

-   Detectie van veelvoorkomende onjuiste configuraties van toepassingen (dat wil zeggen Apache, IIS, enzovoort).

Het configureren van WAF in uw Application Gateway biedt de volgende voor delen:

-   Beveiligt uw webtoepassing tegen beveiligings lekken en aanvallen zonder dat de back-end code hoeft te worden gewijzigd.

-   Beveiligt meerdere webtoepassingen op hetzelfde moment achter een toepassings gateway. Een toepassings gateway ondersteunt het hosten van Maxi maal 20 websites.

-   Bewaakt webtoepassingen tegen aanvallen door gebruik te maken van real-time rapporten die worden gegenereerd door Application Gateway WAF-Logboeken.

-   Helpt te voldoen aan nalevings vereisten. Voor bepaalde nalevings controles moeten alle Internet gerichte eind punten worden beveiligd met een WAF-oplossing.

### <a name="anomaly-detection-api-built-with-azure-machine-learning"></a>Anomalie detectie-API: gebouwd met Azure Machine Learning

De anomalie detectie-API is een API die nuttig is voor het detecteren van verschillende afwijkende patronen in uw tijdreeks gegevens. De API wijst een afwijkende Score toe aan elk gegevens punt in de tijd reeks die kan worden gebruikt voor het genereren van waarschuwingen, het bewaken van de Dash boards of het maken van een verbinding met uw ticket systemen.

De [anomalie detectie-API](../../machine-learning/team-data-science-process/apps-anomaly-detection-api.md) kan de volgende typen afwijkingen in de tijdreeks gegevens detecteren:

-   **Pieken en spannings dips**: wanneer u het aantal mislukte aanmeldingen in een service of het aantal afhandelingen in een e-commerce site bewaakt, kunnen ongebruikelijke pieken of spannings dips duiden op beveiligings aanvallen of onderbrekingen in de service.

-   **Positieve en negatieve trends**: wanneer u het geheugen gebruik bewaken in computing, wordt de grootte van het beschik bare geheugen een mogelijke geheugenlek. Voor de bewaking van de service wachtrij lengte kan een permanente opwaartse trend duiden op een onderliggend software probleem.

-   **Niveau wijzigingen en wijzigingen in dynamische waarden reeks**: niveau wijzigingen in latentie van een service na een service-upgrade of lagere niveaus van uitzonde ringen na de upgrade kan interessant zijn om te bewaken.

Met de op machine learning gebaseerde API kunt u het volgende doen:

-   **Flexibele en robuuste detectie: met** de anomalie detectie modellen kunnen gebruikers gevoeligheids instellingen configureren en afwijkingen tussen seizoen-en niet-seizoen gegevens sets detecteren. Gebruikers kunnen het model voor anomalie detectie aanpassen om de detectie-API minder of meer gevoelig te maken op basis van hun behoeften. Dit leidt ertoe dat er minder of meer zicht bare afwijkingen worden gedetecteerd in gegevens met en zonder seizoen patronen.

-   **Schaal bare en tijdige detectie**: de traditionele manier om te controleren met de huidige drempel waarden die zijn ingesteld door de domein kennis van deskundigen, zijn kostbaar en niet schaalbaar voor miljoenen gegevens sets die dynamisch worden gewijzigd. De anomalie detectie modellen in deze API worden geleerd en modellen worden automatisch afgestemd op zowel historische als realtime gegevens.

-   **Proactieve en bruikbare detectie**: trage trend en detectie van niveau wijziging kan worden toegepast voor vroegtijdige anomalie detectie. De vroege abnormale signalen die worden gedetecteerd, kunnen worden gebruikt om mensen te leiden om de probleem gebieden te onderzoeken en op te treden. Daarnaast kunnen analyse modellen van de hoofd oorzaak en hulpprogram ma's voor waarschuwings Programma's worden ontwikkeld boven op deze anomalie detectie-API-service.

De anomalie detectie-API is een effectieve en efficiënte oplossing voor een breed scala aan scenario's, zoals service status en KPI-bewaking, IoT, prestatie bewaking en netwerk verkeer bewaking. Hier volgen enkele populaire scenario's waarin deze API nuttig kan zijn:

- IT-afdelingen hebben hulp middelen nodig voor het bijhouden van gebeurtenissen, fout code, gebruiks logboeken en prestaties (CPU, geheugen, enzovoort).

-   Online commerce-sites willen klant activiteiten, pagina weergaven, klikken enzovoort bijhouden.

-   NUTS bedrijven willen het verbruik van water, gas, elektriciteit en andere bronnen bijhouden.

-   Beheer Services voor de faciliteit of het gebouw willen de Tempe ratuur, het vocht, het verkeer, enzovoort bewaken.

-   IoT/fabrikanten willen sensor gegevens in tijd reeksen gebruiken voor het bewaken van de werk stroom, de kwaliteit, enzovoort.

-   Service providers, zoals call centers, moeten de trend van de service vraag, het incident volume, de lengte van de wachtrij van de wacht tijd, enzovoort, bewaken.

-   Business Analytics-groepen willen bedrijfs-Kpi's bewaken (zoals verkoop volume, klant gevoel of prijzen) abnormale verplaatsing in realtime.

### <a name="cloud-app-security"></a>Cloud App Security

[Cloud app Security](/cloud-app-security/what-is-cloud-app-security) is een essentieel onderdeel van de Microsoft Cloud-beveiligings stack. Het is een uitgebreide oplossing die uw organisatie kan helpen bij het uitvoeren van de voor delen van Cloud toepassingen. U hebt de controle over het beheer, door betere zicht baarheid in de activiteit. Het verhoogt ook de beveiliging van essentiële gegevens in cloudtoepassingen.

Met hulpprogramma's om schaduw-IT te onthullen, risico's te beoordelen, beleid af te dwingen, activiteiten te onderzoeken en bedreigingen te stoppen, kan uw organisatie veiliger overstappen naar de cloud terwijl de controle over essentiële gegevens wordt behouden.

| Categorie | Beschrijving |
| -------- | ----------- |
| Ontdekken | Onthul Shadow IT met Cloud App Security. Krijg meer inzicht door apps, activiteiten, gebruikers, gegevens en bestanden in uw cloudomgeving te detecteren. Detecteer apps van derden die zijn verbonden met uw cloud.|
|Onderzoeken | Onderzoek uw cloud-apps met forensische hulpprogramma's voor de cloud om riskante apps, specifieke gebruikers en bestanden in uw netwerk diepgaand te onderzoeken. Vind patronen in de gegevens die vanuit de cloud worden verzameld. U kunt rapporten genereren om uw cloud te bewaken. |
| Beheer | Beperk risico's door beleidsregels en waarschuwingen in te stellen om maximale controle over het netwerkverkeer in de cloud te behalen. Gebruik Cloud App Security om uw gebruikers te migreren naar veilige, goedgekeurde alternatieven voor de cloud-app. |
| Beveiligen | Gebruik Cloud App Security om toepassingen goed te keuren of te verbieden, preventie van gegevens verlies af te dwingen, machtigingen te beheren en te delen en aangepaste rapporten en waarschuwingen te genereren. |
| Beheer | Beperk risico's door beleidsregels en waarschuwingen in te stellen om maximale controle over het netwerkverkeer in de cloud te behalen. Gebruik Cloud App Security om uw gebruikers te migreren naar veilige, goedgekeurde alternatieven voor de cloud-app. |


![Cloud App Security diagram](./media/threat-detection/azure-threat-detection-fig14.png)

Cloud App Security integreert de zicht baarheid met uw Cloud door:

-   Cloud Discovery gebruiken om uw cloud omgeving en de Cloud-apps die uw organisatie gebruikt, toe te wijzen en te identificeren.

-   Het goed keuren en verbieden van apps in uw Cloud.

-   Het gebruik van eenvoudig te implementeren app-connectors die gebruikmaken van provider-Api's, voor zicht baarheid en beheer van apps waarmee u verbinding maakt.

-   Helpt u een doorlopend beheer te bieden door in te stellen en vervolgens het beleid doorlopend te verfijnen.

Bij het verzamelen van gegevens uit deze bronnen, voert Cloud App Security geavanceerde analyse uit. U wordt direct op de hoogte gesteld van afwijkende activiteiten en u krijgt meer inzicht in uw cloudomgeving. U kunt een beleid configureren in Cloud App Security en dit vervolgens gebruiken om alles in uw cloudomgeving te beveiligen.

## <a name="third-party-advanced-threat-detection-capabilities-through-the-azure-marketplace"></a>Geavanceerde detectie mogelijkheden van derden via de Azure Marketplace

### <a name="web-application-firewall"></a>Web Application Firewall

Web Application Firewall inspecteert inkomend webverkeer en blokkeert SQL-injecties, cross-site scripting, uploads van malware, DDoS aanvallen en andere aanvallen die gericht zijn op uw webtoepassingen. Ook worden de antwoorden van de back-end-webservers voor preventie van gegevens verlies (DLP) gecontroleerd. Met de geïntegreerde Access Control Engine kunnen beheerders nauw keurig toegangs beheer beleid maken voor verificatie, autorisatie en accounting (AAA), waarmee organisaties sterke authenticatie en gebruikers beheer kunnen bieden.

Web Application Firewall biedt de volgende voor delen:

-   Detecteert en blokkeert SQL-injecties, cross-site scripting, uploads van malware, toepassings DDoS of andere aanvallen tegen uw toepassing.

-   Verificatie en toegangs beheer.

-   Hiermee wordt het uitgaande verkeer gescand om gevoelige gegevens te detecteren en kunnen de gegevens worden gemaskeerd of geblokkeerd.

-   Versnelt de levering van inhoud van webtoepassingen, met mogelijkheden zoals caching, compressie en andere verkeer optimalisaties.

Voor voor beelden van firewalls voor webtoepassingen die beschikbaar zijn in azure Marketplace, Zie [BARRACUDA WAF, virtual Web Application firewall (vWAF), Imperva SecureSphere en de ThreatSTOP IP-firewall](https://azuremarketplace.microsoft.com/marketplace/apps/barracudanetworks.waf).

## <a name="next-steps"></a>Volgende stappen

- [Reageert op bedreigingen van vandaag](../../security-center/security-center-alerts-overview.md#respond-threats): helpt bij het identificeren van actieve bedreigingen die gericht zijn op uw Azure-resources en biedt de inzichten die u nodig hebt om snel te reageren.

- [Azure SQL database detectie van bedreigingen](https://azure.microsoft.com/blog/azure-sql-database-threat-detection-your-built-in-security-expert/): helpt u bij het oplossen van problemen met mogelijke bedreigingen voor uw data bases.