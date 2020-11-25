---
title: Log Analytics gegevens beveiliging | Microsoft Docs
description: Meer informatie over hoe Log Analytics uw privacy beschermt en uw gegevens beveiligt.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/11/2020
ms.openlocfilehash: 5aa379f6601bc324bd08c53f251b2097141eec69
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "95911631"
---
# <a name="log-analytics-data-security"></a>Log Analytics gegevens beveiliging
Dit document is bedoeld om informatie te verschaffen die specifiek is voor Log Analytics, een functie van Azure Monitor, om de informatie over [Vertrouwenscentrum van Azure](https://www.microsoft.com/en-us/trust-center?rtc=1)aan te vullen.  

In dit artikel wordt uitgelegd hoe data door Log Analytics wordt verzameld, verwerkt en beveiligd. U kunt agents gebruiken om verbinding te maken met de webservice, System Center Operations Manager te gebruiken om operationele gegevens te verzamelen of gegevens op te halen uit Azure Diagnostics voor gebruik door Log Analytics. 

De Log Analytics-service beheert uw gegevens op basis van de Cloud veilig door de volgende methoden te gebruiken:

* Gegevensscheiding
* Bewaartijd voor gegevens
* Fysieke beveiliging
* Incidentbeheer
* Naleving
* Certificeringen voor beveiligings standaarden

U kunt ook aanvullende beveiligings functies gebruiken die zijn ingebouwd in Azure Monitor en Log Analytics. Voor deze functies is meer beheerders beheer vereist. 
* Door de klant beheerde sleutels (Security)
* Persoonlijke Azure-opslag
* Private Link-netwerken 
* Toegangs limieten voor Azure die zijn ingesteld door Azure lockbox

Neem contact met ons op met eventuele vragen, suggesties of problemen met betrekking tot een van de volgende informatie, waaronder ons beveiligings beleid voor [ondersteunings opties voor Azure](https://azure.microsoft.com/support/options/).

## <a name="sending-data-securely-using-tls-12"></a>Veilig gegevens verzenden met behulp van TLS 1,2 

Om ervoor te zorgen dat de beveiliging van gegevens die onderweg zijn naar Log Analytics, raden we u ten zeerste aan de agent te configureren voor het gebruik van ten minste Transport Layer Security (TLS) 1,2. Oudere versies van TLS/Secure Sockets Layer (SSL) zijn kwetsbaar voor aanvallen en terwijl ze nog steeds werken om achterwaartse compatibiliteit mogelijk te maken, worden ze **niet aanbevolen** en wordt de branche snel verplaatst om ondersteuning voor deze oudere protocollen te annuleren. 

De [PCI Security Standards Council](https://www.pcisecuritystandards.org/) heeft een [deadline ingesteld van 30 juni 2018](https://www.pcisecuritystandards.org/pdfs/PCI_SSC_Migrating_from_SSL_and_Early_TLS_Resource_Guide.pdf) om oudere versies van TLS/SSL uit te scha kelen en te upgraden naar meer beveiligde protocollen. Als de ondersteuning voor Azure wordt verouderd als uw agents niet via ten minste TLS 1,2 kunnen communiceren, kunt u geen gegevens verzenden naar Log Analytics. 

Het wordt niet aanbevolen om uw agent expliciet in te stellen voor gebruik van TLS 1,2, tenzij dit absoluut nood zakelijk is, omdat de beveiligings functies op platform niveau kunnen worden vermeden, zodat u nieuwe beveiligde protocollen automatisch kunt detecteren en gebruiken wanneer deze beschikbaar zijn, zoals TLS 1,3. 

### <a name="platform-specific-guidance"></a>Platformspecifieke richt lijnen

|Platform/taal | Ondersteuning | Meer informatie |
| --- | --- | --- |
|Linux | Linux-distributies zijn vaak afhankelijk van [openssl](https://www.openssl.org) voor TLS 1,2-ondersteuning.  | Controleer de [openssl wijzigingen logboek](https://www.openssl.org/news/changelog.html) om te bevestigen dat uw versie van openssl wordt ondersteund.|
| Windows 8,0-10 | Wordt ondersteund en is standaard ingeschakeld. | Om te bevestigen dat u nog steeds de [standaard instellingen](/windows-server/security/tls/tls-registry-settings)gebruikt.  |
| Windows Server 2012-2016 | Wordt ondersteund en is standaard ingeschakeld. | Controleren of u nog steeds de [standaard instellingen](/windows-server/security/tls/tls-registry-settings) gebruikt |
| Windows 7 SP1 en Windows Server 2008 R2 SP1 | Ondersteund, maar is niet standaard ingeschakeld. | Zie de pagina met [register instellingen voor Transport Layer Security (TLS)](/windows-server/security/tls/tls-registry-settings) voor meer informatie over het inschakelen van.  |

## <a name="data-segregation"></a>Gegevensscheiding
Nadat uw gegevens door de Log Analytics-service zijn opgenomen, worden de gegevens in de gehele service logisch gescheiden gehouden van elk onderdeel. Alle gegevens worden gelabeld per werk ruimte. Deze markering blijft aanwezig gedurende de levenscyclus van de gegevens en deze wordt afgedwongen op elke laag van de service. Uw gegevens worden opgeslagen in een specifieke data base in het opslag cluster in de regio die u hebt geselecteerd.

## <a name="data-retention"></a>Bewaartijd voor gegevens
Zoek gegevens voor geïndexeerde logboeken worden opgeslagen en bewaard volgens uw prijs plan. Zie [log Analytics prijzen](https://azure.microsoft.com/pricing/details/log-analytics/)voor meer informatie.

Als onderdeel van uw [abonnements overeenkomst](https://azure.microsoft.com/support/legal/subscription-agreement/)houdt micro soft uw gegevens volgens de voor waarden van de overeenkomst.  Wanneer klant gegevens worden verwijderd, worden er geen fysieke stations vernietigd.  

De volgende tabel bevat een aantal van de beschik bare oplossingen en biedt voor beelden van het type gegevens dat ze verzamelen.

| **Oplossing** | **Gegevenstypen** |
| --- | --- |
| Capaciteit en prestaties |Prestatie gegevens en meta gegevens |
| Updatebeheer |Meta gegevens en status gegevens |
| Logboek beheer |Door de gebruiker gedefinieerde gebeurtenis logboeken, Windows-gebeurtenis logboeken en/of IIS-logboeken |
| Wijzigingen bijhouden |Meta gegevens van software-inventaris, Windows-service en Linux-daemon en Windows/Linux-bestanden |
| SQL en Active Directory-evaluatie |WMI-gegevens, register gegevens, prestatie gegevens en SQL Server dynamische beheer resultaten weer geven |

De volgende tabel bevat voor beelden van gegevens typen:

| **Gegevenstype** | **Fields** |
| --- | --- |
| Waarschuwing |Naam van waarschuwing, beschrijving van waarschuwing, BaseManagedEntityId, probleem-ID, IsMonitorAlert, RuleId, ResolutionState, prioriteit, Ernst, categorie, eigenaar, ResolvedBy, TimeRaised, TimeAdded, LastModified, LastModifiedBy, LastModifiedExceptRepeatCount, TimeResolved, TimeResolutionStateLastModified, TimeResolutionStateLastModifiedInDB, RepeatCount |
| Configuratie |KlantId, AgentID, EntityID, ManagedTypeID, ManagedTypePropertyID, CurrentValue, Change date |
| Gebeurtenis |Gebeurtenis-instantie, EventOriginalID, BaseManagedEntityInternalId, RuleId, PublisherId, Uitgevernaam, FullNumber, Number, Category, ChannelLevel, LoggingComputer, Event Data, EventParameters, TimeGenerated, TimeAdded <br>**Opmerking:** Wanneer u gebeurtenissen met aangepaste velden in het Windows-gebeurtenis logboek schrijft, worden deze door Log Analytics verzameld. |
| Metagegevens |BaseManagedEntityId, object status, OrganizationalUnit, ActiveDirectoryObjectSid, PhysicalProcessors, netwerkwerknaam, IPAddress, ForestDNSName, NetbiosComputerName, VirtualMachineName, LastInventoryDate, HostServerNameIsVirtualMachine, IP-adres, NetbiosDomainName, LogicalProcessors, DNSName, DisplayName, DomainDnsName, ActiveDirectorySite, principalnaam, OffsetInMinuteFromGreenwichTime |
| Prestaties |ObjectName, CounterName, PerfmonInstanceName, PerformanceDataId, PerformanceSourceInternalID, SampleValue, TimeSampled, TimeAdded |
| Status |StateChangeEventId, StateId, NewHealthState, OldHealthState, context, TimeGenerated, TimeAdded, StateId2, BaseManagedEntityId, MonitorId, HealthState, LastModified, LastGreenAlertGenerated, DatabaseTimeModified |

## <a name="physical-security"></a>Fysieke beveiliging
De Log Analytics-service wordt beheerd door personeel van micro soft en alle activiteiten worden vastgelegd en kunnen worden gecontroleerd. Log Analytics wordt gebruikt als een Azure-service en voldoet aan alle vereisten voor naleving en beveiliging van Azure. U kunt details weer geven over de fysieke beveiliging van Azure-assets op pagina 18 van het [overzicht van Microsoft Azure beveiliging](https://download.microsoft.com/download/6/0/2/6028B1AE-4AEE-46CE-9187-641DA97FC1EE/Windows%20Azure%20Security%20Overview%20v1.01.pdf). Fysieke toegangs rechten voor beveiligde gebieden worden binnen één werkdag gewijzigd voor iedereen die niet meer verantwoordelijk is voor de Log Analytics service, met inbegrip van overdracht en beëindiging. Meer informatie over de wereld wijde fysieke infra structuur die [wordt gebruikt voor micro soft-data centers](https://azure.microsoft.com/global-infrastructure/).

## <a name="incident-management"></a>Incidentbeheer
Log Analytics een incident beheer proces heeft dat alle micro soft-Services naleven. Als u wilt samenvatten, doet u het volgende:

* Een gedeeld verantwoordelijkheids model gebruiken waarbij een deel van de beveiligings verantwoordelijkheid deel uitmaakt van micro soft en een deel van de klant is
* Azure-beveiligings incidenten beheren:
  * Een onderzoek starten bij het detecteren van een incident
  * Beoordeling van de impact en ernst van een incident door een lid van een incident response-teamlid. Op basis van bewijs materiaal kan de evaluatie niet meer leiden tot verdere escalatie naar het beveiligings antwoord team.
  * Diagnose een incident door experts voor beveiligings reacties om het technisch of forensische onderzoek uit te voeren, de strategieën voor het inperken, beperken en het oplossen van oplossingen te identificeren. Als het beveiligings team meent dat klant gegevens kunnen worden blootgesteld aan een onrecht matige of niet-geautoriseerde persoon, wordt parallelle uitvoering van het proces voor het melden van het incident parallel gestart.  
  * Het incident te stabiliseren en te herstellen. Het incident response team maakt een herstel plan om het probleem te verhelpen. De stappen voor het opnemen van de crisis, zoals in quarantaine-getroffen systemen, kunnen onmiddellijk en parallel met de diagnose worden uitgevoerd. Er kunnen oplossingen voor langere termijn worden gepland die optreden nadat het directe risico is verstreken.  
  * Sluit het incident en voer een postmortemkeuring uit. Het incident response team maakt een post met de details van het incident, met de bedoeling om het beleid, de procedures en de processen te herzien om een terugkeer patroon van de gebeurtenis te voor komen.
* Klanten informeren over beveiligings incidenten:
  * Bepaal het bereik van de betrokken klanten en om iedereen te voorzien van een mede deling, indien mogelijk
  * Maak een kennisgeving om klanten te voorzien van voldoende informatie, zodat ze een onderzoek kunnen uitvoeren op hun einde en voldoen aan de verplichtingen die ze aan hun eind gebruikers hebben gegeven, terwijl het meldings proces niet onnodig wordt vertraagd.
  * Bevestig en Declareer het incident, indien nodig.
  * Informeer klanten met een melding over incidenten zonder onredelijke vertraging en in overeenstemming met juridische of contractuele verplichtingen. Meldingen van beveiligings incidenten worden aan een of meer van de beheerders van een klant bezorgd, op welke manier micro soft selecteert, bijvoorbeeld via e-mail.
* Team voorbereiding en-training uitvoeren:
  * Micro soft-personeel is verplicht om beveiligings-en bewustmakings training te volt ooien, waarmee ze mogelijk verdachte beveiligings problemen kunnen identificeren en rapporteren.  
  * Opera tors die aan de Microsoft Azure-service werken, hebben aanvullende opleidings verplichtingen rond hun toegang tot gevoelige systemen die klant gegevens hosten.
  * Mede werkers van micro soft-beveiligings reacties ontvangen gespecialiseerde training voor hun rollen

Als er klant gegevens verloren gaan, sturen we elke klant binnen één dag op de hoogte. Het verlies van klant gegevens heeft echter nooit plaatsgevonden bij de service. 

Zie [Microsoft Azure Security Response in the Cloud](https://gallery.technet.microsoft.com/Azure-Security-Response-in-dd18c678/file/150826/4/Microsoft%20Azure%20Security%20Response%20in%20the%20cloud.pdf)(Engelstalig) voor meer informatie over hoe micro soft reageert op beveiligings incidenten.

## <a name="compliance"></a>Naleving
De informatie beveiliging en het beheer programma voor de Log Analytics software ontwikkeling en het service team ondersteunt zijn bedrijfs vereisten en voldoet aan de wetten en voor schriften zoals beschreven in [Microsoft Azure vertrouwens centrum](https://azure.microsoft.com/support/trust-center/) en naleving van het [micro soft vertrouwens centrum](https://www.microsoft.com/en-us/trustcenter/compliance/default.aspx). Hoe Log Analytics beveiligings vereisten aanbrengt, de beveiligings maatregelen identificeert, beheert en bewaakt, worden ook de Risico's beschreven. Jaarlijks bekijken we beleids regels, standaarden, procedures en richt lijnen.

Elk lid van het ontwikkelings team ontvangt formeel toepassings beveiligings training. Intern gebruiken we een versie beheersysteem voor het ontwikkelen van software. Elk software project wordt beveiligd door het versie beheersysteem.

Micro soft heeft een team voor beveiliging en naleving dat alle services in micro soft overziet en evalueert. Informatie beveiligings managers maken het team samen en ze zijn niet gekoppeld aan de technische teams die Log Analytics ontwikkelen. De beveiligings ambtenaren hebben hun eigen beheer keten en voeren onafhankelijke evaluaties van producten en services uit om te zorgen voor beveiliging en naleving.

De Raad van bestuur van micro soft wordt gewaarschuwd door een jaarlijks rapport over alle informatie beveiligings Programma's van micro soft.

Het Log Analytics Software Development and service-team werkt actief samen met de juridische en nalevings teams van micro soft en andere partners van de branche om diverse certificeringen te verkrijgen.

## <a name="certifications-and-attestations"></a>Certificeringen en verklaringen
Azure Log Analytics voldoet aan de volgende vereisten:

* [ISO/IEC 27001](https://www.iso.org/iso/home/standards/management-standards/iso27001.htm)
* [ISO/IEC 27018:2014](https://www.iso.org/iso/home/store/catalogue_tc/catalogue_detail.htm?csnumber=61498)
* [ISO 22301](https://azure.microsoft.com/blog/iso22301/)
* [Betaal kaart (PCI-compatibel) Data Security Standard (PCI DSS)](https://www.microsoft.com/en-us/TrustCenter/Compliance/PCI) door de PCI Security Standards-Raad.
* [Service organization Controls (SOC) 1 type 1 en SOC 2 type 1](https://www.microsoft.com/en-us/TrustCenter/Compliance/SOC1-and-2) voldoen aan het beleid
* [HIPAA en Hitech](https://www.microsoft.com/en-us/TrustCenter/Compliance/hipaa) voor bedrijven die een HIPAA-overeenkomst voor bedrijven hebben
* Algemene technische criteria van Windows
* Micro soft Trustworthy Computing
* Als Azure-service worden de onderdelen die Log Analytics gebruikt, voldoen aan de vereisten voor naleving van Azure. Meer informatie vindt u op de naleving van het [vertrouwens centrum van micro soft](https://www.microsoft.com/en-us/trustcenter/compliance/default.aspx).

> [!NOTE]
> In sommige certificeringen/verklaringen wordt Log Analytics vermeld onder de voormalige naam van *Operational Insights*.
>
>

## <a name="cloud-computing-security-data-flow"></a>Cloud Computing Security data flow
In het volgende diagram ziet u een Cloud beveiligings architectuur als de stroom van gegevens van uw bedrijf en hoe deze wordt beveiligd zoals deze wordt verplaatst naar de Log Analytics-service, die uiteindelijk door u in de Azure Portal wordt weer gegeven. Meer informatie over elke stap volgt het diagram.

![Afbeelding van het verzamelen en beveiligen van Log Analytics gegevens](./media/data-security/log-analytics-data-security-diagram.png)

## <a name="1-sign-up-for-log-analytics-and-collect-data"></a>1. Registreer u voor Log Analytics en gegevens verzamelen
Als u wilt dat uw organisatie gegevens naar Log Analytics verzendt, configureert u een Windows-of Linux-agent die wordt uitgevoerd op virtuele machines van Azure of op virtuele of fysieke computers in uw omgeving of een andere Cloud provider.  Als u Operations Manager gebruikt, configureert u de Operations Manager agent in de beheer groep. Gebruikers (dit kunnen u, andere afzonderlijke gebruikers of een groep personen) een of meer Log Analytics werk ruimten maken en agents registreren met behulp van een van de volgende accounts:

* [Organisatie-ID](../../active-directory/fundamentals/sign-up-organization.md)
* [Micro soft-account: Outlook, Office Live, MSN](https://account.microsoft.com/account)

Een Log Analytics werk ruimte is waar gegevens worden verzameld, geaggregeerd, geanalyseerd en gepresenteerd. Een werk ruimte wordt hoofd zakelijk gebruikt als een manier om gegevens te partitioneren en elke werk ruimte is uniek. Stel dat u uw productie gegevens wilt beheren met één werk ruimte en dat uw test gegevens worden beheerd met een andere werk ruimte. Met werk ruimten kunnen beheerders ook gebruikers toegang tot de gegevens beheren. Aan elke werk ruimte kunnen meerdere gebruikers accounts worden gekoppeld en elk gebruikers account heeft toegang tot meerdere Log Analytics-werk ruimten. U kunt werk ruimten maken op basis van de regio Data Center.

Voor Operations Manager brengt de Operations Manager beheer groep een verbinding tot stand met de Log Analytics service. Vervolgens configureert u welke door agents beheerde systemen in de beheer groep mogen gegevens verzamelen en verzenden naar de service. Afhankelijk van de oplossing die u hebt ingeschakeld, worden gegevens van deze oplossingen rechtstreeks van een Operations Manager-beheer server naar de Log Analytics-service verzonden, of vanwege het volume van de gegevens die worden verzameld door het door de agent beheerde systeem, worden ze rechtstreeks van de agent naar de service verzonden. Voor systemen die niet worden bewaakt door Operations Manager, maakt elk een beveiligde verbinding met de Log Analytics-service.

Alle communicatie tussen verbonden systemen en de Log Analytics-service is versleuteld. Het TLS-protocol (HTTPS) wordt gebruikt voor versleuteling.  Het micro soft SDL-proces wordt gevolgd om te garanderen dat Log Analytics is bijgewerkt met de meest recente voor uitgang in cryptografische protocollen.

Elk type agent verzamelt gegevens voor Log Analytics. Welk type gegevens er wordt verzameld, is afhankelijk van de gebruikte oplossingen. U kunt een samen vatting van gegevens verzameling weer geven op [log Analytics oplossingen toevoegen vanuit de Oplossingengalerie](../insights/solutions.md). Daarnaast is er meer gedetailleerde verzamelings gegevens beschikbaar voor de meeste oplossingen. Een oplossing is een bundel van vooraf gedefinieerde weer gaven, logboek zoekopdracht query's, regels voor het verzamelen van gegevens en de verwerking van logica. Alleen beheerders kunnen Log Analytics gebruiken om een oplossing te importeren. Nadat de oplossing is geïmporteerd, wordt deze verplaatst naar de Operations Manager beheerser vers (indien gebruikt) en vervolgens naar de agents die u hebt gekozen. Daarna verzamelen de agents de gegevens.

## <a name="2-send-data-from-agents"></a>2. gegevens verzenden van agents
U registreert alle agent typen met een registratie sleutel en er wordt een beveiligde verbinding tot stand gebracht tussen de agent en de Log Analytics service met verificatie op basis van certificaten en TLS met poort 443. Log Analytics gebruikt een geheim archief om sleutels te genereren en te onderhouden. Persoonlijke sleutels worden elke 90 dagen gedraaid en worden opgeslagen in Azure en worden beheerd door de Azure-bewerkingen die voldoen aan de strikte regelgeving en nalevings procedures.

Met Operations Manager is de beheer groep die is geregistreerd met een Log Analytics-werk ruimte, een beveiligde HTTPS-verbinding met een Operations Manager-beheer server tot stand gebracht.

Voor Windows-of Linux-agents die worden uitgevoerd op virtuele machines van Azure, wordt een alleen-lezen opslag sleutel gebruikt voor het lezen van diagnostische gebeurtenissen in azure-tabellen.  

Als een agent rapporteert aan een Operations Manager-beheer groep die is geïntegreerd met Log Analytics en de beheer server om welke reden dan ook niet kan communiceren met de service, worden de verzamelde gegevens lokaal opgeslagen in een tijdelijke cache op de-beheer server.   Ze proberen de gegevens om de acht minuten voor twee uur opnieuw te verzenden.  Voor gegevens die de beheer server omzeilt en rechtstreeks naar Log Analytics worden verzonden, is het gedrag consistent met de Windows-agent.  

De in de cache opgeslagen gegevens van de Windows-of beheer Server Agent worden beveiligd door het referentie archief van het besturings systeem. Als de service de gegevens na twee uur niet kan verwerken, worden de gegevens in de agents in de wachtrij geplaatst. Als de wachtrij vol raakt, begint de agent gegevens typen te verwijderen, beginnend met de prestatie gegevens. De limiet voor de agent wachtrij is een register sleutel, zodat u deze zo nodig kunt wijzigen. Verzamelde gegevens worden gecomprimeerd en naar de service verzonden, waarbij de Operations Manager-beheer groep-data bases worden overgeslagen, zodat er geen belasting aan de toepassingen kan worden toegevoegd. Nadat de verzamelde gegevens zijn verzonden, worden ze uit de cache verwijderd.

Zoals hierboven beschreven, worden gegevens van de beheer server of direct verbonden agents via TLS verzonden naar Microsoft Azure data centers. U kunt ExpressRoute ook gebruiken om extra beveiliging te bieden voor de gegevens. ExpressRoute is een manier om rechtstreeks verbinding te maken met Azure vanuit uw bestaande WAN-netwerk, zoals een MPLS-VPN (multi-protocol label switching), dat wordt verschaft door een netwerk serviceprovider. Zie [ExpressRoute](https://azure.microsoft.com/services/expressroute/)voor meer informatie.

## <a name="3-the-log-analytics-service-receives-and-processes-data"></a>3. de Log Analytics-service ontvangt en verwerkt gegevens
De Log Analytics-service zorgt ervoor dat inkomende gegevens afkomstig zijn van een vertrouwde bron door certificaten en de integriteit van gegevens te valideren met Azure-verificatie. De onverwerkte onbewerkte gegevens worden vervolgens opgeslagen in een Azure Event hub in de regio waarin de gegevens uiteindelijk worden opgeslagen in rust. Het type gegevens dat wordt opgeslagen, is afhankelijk van de typen oplossingen die zijn geïmporteerd en gebruikt voor het verzamelen van gegevens. Vervolgens worden de onbewerkte gegevens door de Log Analytics-Service verwerkt en opgenomen in de-data base.

De Bewaar periode van verzamelde gegevens die zijn opgeslagen in de data base is afhankelijk van het geselecteerde prijs plan. Voor de *gratis* laag zijn verzamelde gegevens zeven dagen beschikbaar. Verzamelde gegevens voor de laag *betaald* zijn standaard 31 dagen beschikbaar, maar kunnen worden verlengd tot 730 dagen. Gegevens worden in de rest van Azure Storage versleuteld opgeslagen om gegevens geheim te controleren en de gegevens worden gerepliceerd binnen de lokale regio met behulp van lokaal redundante opslag (LRS). De laatste twee weken van gegevens worden ook opgeslagen in de cache op SSD-basis en deze cache is versleuteld.

## <a name="4-use-log-analytics-to-access-the-data"></a>4. gebruik Log Analytics om toegang te krijgen tot de gegevens
Als u toegang wilt krijgen tot uw Log Analytics-werk ruimte, meldt u zich aan bij de Azure Portal met behulp van het organisatie account of Microsoft-account dat u eerder hebt ingesteld. Al het verkeer tussen de portal en de Log Analytics-service wordt verzonden via een beveiligd HTTPS-kanaal. Wanneer u de portal gebruikt, wordt er een sessie-ID gegenereerd op de gebruikers-client (webbrowser) en worden gegevens opgeslagen in een lokale cache totdat de sessie wordt beëindigd. Wanneer het is beëindigd, wordt de cache verwijderd. Cookies aan de client zijde, die geen persoons gegevens bevatten, worden niet automatisch verwijderd. Sessie cookies zijn gemarkeerd als HTTPOnly en zijn beveiligd. Na een vooraf vastgestelde niet-actieve periode wordt de Azure Portal-sessie beëindigd.


## <a name="additional-security-features"></a>Aanvullende beveiligings functies
U kunt deze aanvullende beveiligings functies gebruiken om uw Azure Monitor/Log Analytics-omgeving verder te beveiligen. Voor deze functies is meer beheerders beheer vereist. 
- Door de [klant beheerde sleutels (beveiliging)](customer-managed-keys.md) : u kunt door de klant beheerde sleutels gebruiken om gegevens te versleutelen die naar uw log Analytics-werk ruimten worden verzonden. Hiervoor is het gebruik van Azure Key Vault vereist. 
- [Persoonlijke/door de klant beheerde opslag](private-storage.md) : beheer uw persoonlijke versleutelde opslag account en vertel log Analytics het gebruik ervan om bewakings gegevens op te slaan 
- [Particulier netwerk](private-link-security.md) : met de persoonlijke Azure-koppeling kunt u Azure PaaS-Services (met inbegrip van Azure monitor) veilig koppelen aan uw virtuele netwerk met behulp van privé-eind punten. 
- [Azure klant-lockbox](/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-preview) -Klanten-lockbox voor Microsoft Azure biedt klanten een interface voor het controleren en goed keuren of afwijzen van aanvragen voor toegang tot klant gegevens. Dit wordt gebruikt in gevallen waarin een Microsoft-engineer toegang heeft tot klant gegevens tijdens een ondersteuningsaanvraag.


## <a name="next-steps"></a>Volgende stappen
* Meer informatie over het verzamelen van gegevens met Log Analytics voor uw virtuele Azure-machines na de [Snelstartgids van Azure VM](../learn/quick-collect-azurevm.md).  

*  Als u gegevens wilt verzamelen van fysieke of virtuele Windows-of Linux-computers in uw omgeving, raadpleegt u de [Quick start voor Linux-computers](../learn/quick-collect-linux-computer.md) of [Quick start voor Windows-computers](../learn/quick-collect-windows-computer.md)

