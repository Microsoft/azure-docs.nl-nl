---
title: Een Azure Remote Rendering-toepassing maken die commercieel gereed is
description: Strategieën en overwegingen voor het maken van een commerciële kant-en-klare toepassing met behulp van Azure Remote Rendering
author: FlorianBorn71
ms.author: flborn
ms.date: 06/15/2020
ms.topic: tutorial
ms.openlocfilehash: 7258b37c9d92f25227eac678dde77963e644e64b
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/02/2020
ms.locfileid: "96483314"
---
# <a name="tutorial-creating-a-commercial-ready-azure-remote-rendering-application"></a>Zelfstudie: Een Azure Remote Rendering-toepassing maken die commercieel gereed is

In deze zelfstudie komen deze onderwerpen aan bod:

> [!div class="checklist"]
>
> * Sessiebeheer voor commerciële toepassingen
> * Sessies voor facturering bijhouden
> * De gebruikerservaring met betrekking tot het laden van de sessie optimaliseren
> * Overwegingen met betrekking tot netwerklatentie

## <a name="prerequisites"></a>Vereisten

* Deze zelfstudie is gebaseerd op [Zelfstudie: Azure Remote Rendering en modelopslag beveiligen](../security/security.md).

## <a name="introduction-to-commercial-readiness"></a>Inleiding tot commerciële gereedheid

Azure Remote Rendering verlegt de grenzen van mixed reality. Zodra de basisbeginselen in uw oplossing zijn geïntegreerd, is er een aantal extra aandachtspunten om ervoor te zorgen dat uw oplossing veilig, schaalbaar en gereed is om waarde te leveren.

Deze module bevat een aantal aanvullende mogelijkheden die u kunt overwegen voor uw commerciële toepassing.

Ga voor een uitgebreid overzicht van de best practices voor de architectuur van het hele systeem naar:

* [Azure Architecture Center](/azure/architecture/)
* [Introductiehandleiding voor Azure-ontwikkelaars](../../../../guides/developer/azure-developer-guide.md)

## <a name="analytics"></a>Analyse

De integratie van analysehulpprogramma's kan u helpen uw oplossing te beheren, bij te houden en te verbeteren.

Voor een uitgebreide lijst met beschikbare analyseresources gaat u naar:

* [Azure Analytics Services](https://azure.microsoft.com/product-categories/analytics/)

### <a name="tracking-usage-for-billing"></a>Gebruik voor facturering bijhouden

Het bijhouden van het verbruik van Azure Remote Rendering door meerdere interne teams of externe clients is een belangrijke overweging, met name in situaties met meerdere tenants.

Azure biedt resourcetagging, waarmee het verbruik van de Azure Remote Rendering-service aan elke client wordt gekoppeld.

Zie voor meer informatie over naamgeving en taggen van resources:

* [Handleiding voor beslissingen over naamgeving en taggen van resources](/azure/cloud-adoption-framework/decision-guides/resource-tagging/?toc=%2fazure%2fazure-resource-manager%2fmanagement%2ftoc.json)

### <a name="diagnostics"></a>Diagnostiek

Met krachtige hulpprogramma's zoals Event Tracing for Windows (ETW) en Event Trace Logging (ETL) kunt u eenvoudig traceringsgebeurtenissen in uw toepassing genereren en een diagnose stellen voor problemen met netwerken, gegevensopname, sessies, toepassingen en andere problemen die zich kunnen voordoen in een implementatie van een commerciële oplossing.

Ga voor meer informatie naar:

* [Prestatietracering aan de clientzijde maken](../../../how-tos/performance-tracing.md)
* [ETW-gegevens (Event Tracing for Windows) verzamelen](/visualstudio/profiling/how-to-collect-event-tracing-for-windows-etw-data)
* [Windows Apparaatportal gebruiken: logboekregistratie](/windows/mixed-reality/using-the-windows-device-portal)

### <a name="usage-analysis"></a>Gebruiksanalyse

Azure Application Insights helpt u inzicht te krijgen in de manier waarop mensen uw Azure Remote Rendering-toepassing gebruiken. Steeds wanneer u uw app bijwerkt, kunt u beoordelen hoe goed deze werkt voor gebruikers en kunt u uw oplossing daarop aanpassen. Op basis van deze kennis kunt u op gegevens gebaseerde beslissingen nemen voor toekomstige ontwikkelcycli.

Ga voor meer informatie naar:

* [Gebruiksanalyse met Application Insights](../../../../azure-monitor/app/usage-overview.md)

## <a name="fast-startup-time-strategies"></a>Strategieën voor snelle opstarttijd

Voor uw gebruikscase moet u mogelijk snel kunnen schakelen naar de weergave van een 3D-model na het opstarten van de toepassing. Bijvoorbeeld tijdens een belangrijke vergadering waarbij het van essentieel belang is dat alles van te voren actief en werkend is. Een ander voorbeeld is tijdens een CAD 3D-modelbeoordeling waar een snelle ontwerpiteratie tussen een CAD-toepassing en mixed reality de sleutel is tot efficiëntie.

Voor Azure Remote Rendering zijn voorverwerkte 3D-modellen vereist; Azure heeft momenteel enkele minuten nodig om een sessie op te zetten en een model voor rendering te laden. Om dit proces zo naadloos en snel mogelijk te laten verlopen, moeten de 3D-modelgegevens en de ARR-sessie op voorhand worden voorbereid.

De suggesties die hier worden gedeeld maken op dit moment geen deel uit van de standaard Azure Remote Rendering, maar u kunt ze zelf implementeren voor snellere opstarttijden.

### <a name="initiate-early"></a>Vroeg initiëren

Voor een kortere opstarttijd kunt u de sessie het beste zo vroeg mogelijk in de gebruikerswerkstroom maken en initialiseren. Een strategie is om de sessie te initialiseren zodra bekend is dat er een ARR-sessie nodig is. Dit is vaak het geval wanneer de gebruiker een 3D-model uploadt naar Azure Blob Storage voor gebruik met Azure Remote Rendering. In dit geval kunt u het maken van sessies en initialisatie op hetzelfde moment starten als de upload van het 3D-model, zodat beide werkstromen parallel worden uitgevoerd.

Dit proces kan verder worden gestroomlijnd door ervoor te zorgen dat de gekozen invoer- en uitvoercontainers van Azure Blob Storage zich in hetzelfde regionale datacentrum bevinden als de sessie van Azure Remote Rendering.

### <a name="scheduling"></a>Planning

Als u weet dat u in de toekomst behoefte heeft aan Azure Remote Rendering, kunt u een specifieke datum en tijd plannen om de sessie van Azure Remote Rendering op te starten.

Deze optie kan worden aangeboden via een webportal waar zowel een 3D-model kan worden geüpload als een tijd kan worden gepland voor weergave in de toekomst. Dit zou ook een goede plaats zijn om te vragen naar andere renderingvoorkeuren, zoals [*Standard*](../../../reference/vm-sizes.md) of [*Premium*](../../../reference/vm-sizes.md). *Premium*-rendering kan geschikt zijn als u een combinatie assets wilt tonen, waarbij de ideale grootte moeilijker automatisch te bepalen is of als er behoefte is om ervoor te zorgen dat de Azure-regio op dat specifieke moment over VM's beschikt.

### <a name="session-pooling"></a>Sessies groeperen

In de meest veeleisende situaties kan ook worden gekozen voor het groeperen van sessies, waarbij een of meer sessies te allen tijde zijn gemaakt en geïnitialiseerd. Zo maakt u een sessiegroep die direct kan worden gebruikt door een gebruiker die een dergelijke groep aanvraagt. Het nadeel van deze benadering is dat als de VM wordt geïnitialiseerd, de facturering voor de service wordt gestart. Het is misschien niet kosteneffectief om een sessiegroep continu te laten draaien, maar op basis van analyses kan het mogelijk zijn om piekbelastingen te voorspellen. Bovendien kan aan de hand van de bovenstaande planningsstrategie worden voorspeld wanneer de sessies nodig zullen zijn en kan de uitvoering van de sessiegroep op de pieken en dalen worden aangepast.

Deze strategie helpt u ook bij het dynamischer optimaliseren van de keuze tussen *Standard*- en *Premium*-sessies, omdat u veel sneller kunt schakelen tussen de twee typen binnen één gebruikerssessie, bijvoorbeeld wanneer een *Premium*-complexiteitsmodel eerst wordt weergegeven, gevolgd door een voorbeeld dat kan worden gebruikt in *Standard*. Als deze gebruikerssessies behoorlijk lang zijn, kan dit aanzienlijke kostenbesparingen opleveren.

Zie voor meer informatie over Azure Remote Rendering-sessies:

* [Remote Rendering-sessies](../../../concepts/sessions.md)

## <a name="standard-vs-premium-server-size-routing-strategies"></a>Standard versus Premium VM-routeringsstrategieën

Het selecteren van een *Standard*- of *Premium*-servergrootte vormt een uitdaging bij het ontwerpen van uw gebruikerservaring en end-to-endsysteem. Hoewel het gebruik van alleen *Premium*-sessies een optie is, gebruiken *Standard*-sessies veel minder Azure Compute-resources en zijn ze voordeliger dan *Premium*. Dit biedt een sterke motivatie om waar mogelijk *Standard*-sessies te gebruiken en *Premium* alleen wanneer dat nodig is.

Hier delen we verschillende opties, van de minst tot de meest uitgebreide, om tegemoet te komen aan de wens om de sessiekeuzes te beheren.

### <a name="use-only-standard-or-premium"></a>Alleen Standard of Premium gebruiken

Als u zeker weet dat uw behoeften *altijd* onder de drempelwaarde tussen *Standard* en *Premium* vallen, wordt uw beslissing aanzienlijk vereenvoudigd. Gewoon *Standard* gebruiken. Houd er echter rekening mee dat de impact op de gebruikerservaring aanzienlijk is als de som van de totale complexiteit van de geladen assets wordt afgewezen als te complex voor een *Standard*-sessie.

Ook als u verwacht dat een groot deel van het gebruik de drempel tussen *Standard* en *Premium* zal overschrijden of als de kosten geen belangrijke factor zijn in uw use-case zijn, kunt u het simpel houden door altijd voor *Premium* te kiezen.

### <a name="ask-the-user"></a>Vraag de gebruiker

Als u zowel *Standard* als *Premium* wilt ondersteunen, is de eenvoudigste manier om te bepalen welk type sessie u wilt instantiëren de gebruiker te vragen wanneer hij 3D-assets selecteert om weer te geven. De uitdaging van deze benadering is dat de gebruiker de complexiteit moet begrijpen van de 3D-asset of zelfs meerdere assets die worden weergegeven. Dit wordt om die reden doorgaans niet aanbevolen. Als de gebruiker verkeerd selecteert en de optie *Standard* kiest, kan de resulterende gebruikerservaring op een ongelegen moment worden aangetast.

### <a name="analyze-the-3d-model"></a>Het 3D-model analyseren

Een andere relatief eenvoudige aanpak is het analyseren van de complexiteit van de geselecteerde 3D-assets. Als de complexiteit van het model onder de drempel voor *Standard* ligt, start u een *Standard*-sessie, anders een *Premium*-sessie. Hier is de uitdaging dat een enkele sessie uiteindelijk kan worden gebruikt om meerdere modellen te bekijken, waarvan sommige de complexiteitsdrempel van een *Standard*-sessie kunnen overschrijden, waardoor dezelfde sessie niet naadloos kan worden gebruikt voor een opeenvolging van verschillende 3D-assets.

### <a name="automatic-switching"></a>Automatisch schakelen

Automatisch schakelen tussen *Standard*- en *Premium*-sessies kan zinvol zijn in een systeemontwerp dat ook sessiegroeperingen bevat. Deze strategie biedt meer optimalisatie van het resourcegebruik. Terwijl de gebruiker modellen laadt om te bekijken, wordt de complexiteit bepaald en wordt de juiste sessiegrootte aangevraagd bij de sessiegroeperingsservice.

## <a name="working-with-networks"></a>Werken met netwerken

### <a name="diagnostics"></a>Diagnostiek

Voor Azure Remote Rendering is een snelle internetverbinding met lage latentie vereist. De kwaliteit van het netwerk van de gebruiker kan een belangrijke invloed hebben op de kwaliteit van de ervaring. Gezien het feit dat uw clients waarschijnlijk verschillende netwerkconfiguraties hebben en slechts af en toe een slechte netwerklatentie, zijn diagnostische hulpprogramma's van groot belang.  

Om er zeker van te zijn dat u een consistente hoge kwaliteitservaring kunt leveren, is het raadzaam de analysehulpprogramma's aan server- en clientzijde te integreren in uw Azure Remote Rendering-toepassingen. Zo wordt u gewapend met de informatie die u nodig hebt om eventuele netwerkproblemen van uw klanten te diagnosticeren en te beperken.

### <a name="client-network-configurations"></a>Netwerkconfiguraties van klanten

Een van de grootste uitdagingen bij het ontwikkelen van krachtige samenwerkingsoplossingen die worden geïmplementeerd in een grote verscheidenheid aan bedrijfsomgevingen is de voorbereiding op de verschillende netwerktopologieën en bedrijfsfirewall-configuraties die uw clients mogelijk gebruiken.

Veel ondernemingen blokkeren al het peer-to-peerverkeer binnen een LAN. Dit maakt het moeilijk om te profiteren van de eenvoud en de gestroomlijnde UX van automatische LAN-detectie om een lokale gedeelde sessie op te zetten tussen alle gedetecteerde instanties van uw mixed reality-toepassing.

Andere potentiële storingspunten zijn routers die zo zijn geconfigureerd dat deze opzettelijk bandbreedte beperken en firewalls die de meeste TCP/IP-poorten blokkeren.

Wanneer u van plan bent om Azure Remote Rendering te gebruiken op een niet-vertrouwd netwerk, wordt het volgende aanbevolen:

* Verstrek een controlelijst voorafgaand aan de vergadering om de gereedheid van het netwerk te controleren.
* Zorg ervoor dat het juiste regionale datacentrum de aanvraag kan verwerken.
* Geef voldoende tijd om een diagnose van problemen te stellen.
* Neem een mobiele hotspot met een gegevensabonnement met hoge bandbreedte mee als reserve.

### <a name="end-to-end-bandwidth"></a>End-to-end bandbreedte

Het is belangrijk om de bandbreedtemogelijkheden te beoordelen voor elk deel van het netwerk dat kan bestaan tussen de Azure Remote Rendering VM en de eindclient. Houd er rekening mee dat het netwerksegment van het Azure-datacentrum naar de ISP van de klant meer een beperkende factor kan zijn dan van de ISP naar de klant. Een downloadsnelheidstest van de blob kan worden gebruikt om te helpen bij de diagnose van dergelijke problemen.

### <a name="bandwidth-competition"></a>Concurrerende bandbreedte

Houd er bij het ontwerpen van uw mixed reality-toepassing rekening mee dat verschillende functies van de app kunnen concurreren met Azure Remote Rendering voor bandbreedte. Het meest waarschijnlijke onverwachte voorbeeld is wanneer veel deelnemers in een enkele ruimte allemaal verwachten tegelijkertijd ARR te zullen gebruiken om een 3D-asset te bekijken. Elke deel van de netwerkgegevensstroom zal capaciteit moeten hebben om de som van alle ARR-gegevensstromen samen te kunnen transporteren.

Andere voorbeelden zijn gestreamde video, gelijktijdige achtergronduploads van andere gerelateerde inhoud en spraakchat, vooral wanneer er veel deelnemers zijn en het systeem een gedistribueerde peer-to-peermethode toepast in tegenstelling tot een methode met de audio-mixserver in het midden.

Zie voor meer informatie over netwerkanalyse:

* [Downloadsnelheidstest voor Azure Storage Blob](https://www.azurespeed.com/Azure/Download)
* [Latentiestatistieken voor retourtijd van Azure Network](../../../../networking/azure-network-latency.md)
* [Prestatietraceringen aan de serverzijde](../../../overview/features/performance-queries.md)
* [Prestatietraceringen aan de clientzijde](../../../how-tos/performance-tracing.md)

## <a name="collaboration-considerations"></a>Overwegingen voor samenwerking

Enkele van de meest waardevolle toepassingen van Azure Remote Rendering zijn samenwerking tussen meerdere deelnemers die dezelfde 3D-ervaring op hetzelfde moment bekijken. In deze gedeelde sessies is het belangrijk om te erkennen dat elke deelnemer een unieke Azure Remote Rendering-sessie nodig heeft, ongeacht of ze zich op dezelfde plaats in hetzelfde netwerk bevinden of niet.

Dit is waar, omdat elke deelnemer eigenlijk dezelfde ervaring ziet vanuit verschillende invalshoeken, waardoor vanuit elk van die invalshoeken tegelijkertijd dezelfde 3D-assets moeten worden weergegeven.

### <a name="multiple-azure-remote-rendering-sessions"></a>Meerdere Azure Remote Rendering-sessies

Als u van plan bent om gedeelde ervaringen met Azure Remote Rendering te ondersteunen, zullen de systemen die u installeert om ARR-sessies te maken en te beheren, moeten zijn voorbereid voor het initiëren van meerdere sessies. Deze sessies moeten mogelijk worden geïnitialiseerd in verschillende Azure-datacentra als de deelnemers geografisch verspreid zijn.

In uw systeem moet ook de mogelijkheid worden beheerd dat een of meer van de deelnemers zich in een geografische regio bevinden die momenteel niet wordt ondersteund door Azure Remote Rendering of waar momenteel geen Azure Remote Rendering VM-instanties beschikbaar zijn.

Dit beheer van meerdere gelijktijdige sessies kan verder worden gestroomlijnd wanneer het wordt gecombineerd met sessiegroepering en andere strategieën die in dit document worden besproken.

### <a name="azure-blob-storage-considerations"></a>Overwegingen voor Azure Blob Storage

Alle gelijktijdige ARR-sessies kunnen verwijzen naar dezelfde SAS-URI voor het geconverteerde model dat moet worden weergegeven. Dit maakt het mogelijk om de gewenste 3D-assets eenmalig te uploaden en te converteren en vervolgens te delen over alle sessies. Dit is met name het geval wanneer de deelnemers zich op dezelfde locatie bevinden en gebruik maken van hetzelfde datacentrum waar geen prestatieproblemen zijn met betrekking tot de Azure Blob Storage die zich in een ander datacentrum bevindt dan de Azure Remote Rendering-server en de gebruiker.

Als 3D-assets meestal worden geüpload voor een enkele weergavesessie en vervolgens worden verwijderd, zoals in een ontwerpbeoordelingssessie, is de geografische regio van de Azure Blob Storage ten opzichte van de Azure Remote Rendering-server ook minder van belang.

Echter, voor 3D-assets die herhaaldelijk zullen worden gebruikt, zoals in een use-case voor training, is het raadzaam om kant-en-klare 3D-assets in blobopslag te houden in elk regionaal datacentrum waar u van plan bent om Azure Remote Rendering te gebruiken. Dit kan worden geautomatiseerd met Azure Storage-redundantie. CDN wordt ook vaak voor dit doel gebruikt, maar dit is nog geen optie voor Azure Remote Rendering.

Voor meer informatie:

* [Gedeelde ervaringen in mixed reality](/windows/mixed-reality/shared-experiences-in-mixed-reality)
* [Azure Storage-redundantie](../../../../storage/common/storage-redundancy.md)

## <a name="managing-model-access"></a>Modeltoegang beheren

Voor het gebruik van Azure Remote Rendering moet een zorgvuldige afweging worden gemaakt van de end-to-endinfrastructuur voor het beheer van 3D-modellen.

Een voordeel van het gebruik van Azure Remote Rendering is dat grote 3D-assets nooit direct naar het mixed reality-apparaat hoeven te worden verzonden voordat ze worden weergegeven.  Bovendien kan zodra een 3D-asset is geüpload en geconverteerd voor gebruik met Azure Remote Rendering elk aantal gebruikers die instantie van het 3D-model delen.

### <a name="considerations-for-3d-model-access"></a>Overwegingen voor toegang tot 3D-modellen

Dit zijn een paar belangrijke overwegingen bij het bepalen van uw modeltoegangsstrategie.

Bepaal aan de hand van de verwachte use-case de beste plaats of combinatie van plaatsen om een gebruiker in staat te stellen de 3D-assets te selecteren die moeten worden weergegeven. Enkele algemene opties zijn:

* Direct binnen de mixed reality-ervaring
* Via een bijbehorend webportal
* In een bijbehorend desktop- of mobiele toepassing

Als uw use-case gebruikspatronen heeft waarbij dezelfde 3D-asset meerdere keren kan worden geüpload, zal de back-end bijhouden welke modellen al zijn geconverteerd voor gebruik met ARR, zodat een model slechts één keer wordt voorverwerkt voor meerdere toekomstige selecties. Een voorbeeld van een ontwerpbeoordeling zou zijn dat een team toegang heeft tot een gemeenschappelijke originele 3D-asset. Van elk teamlid wordt verwacht dat hij of zij het model met behulp van ARR op een bepaald moment in zijn of haar werkstroom beoordeelt. Alleen de eerste weergave zou de voorverwerking in gang zetten. In de daaropvolgende weergaven zouden het bijbehorende naverwerkte bestand in de SAS-uitvoercontainer worden opgezocht.

Afhankelijk van de use-case wilt u waarschijnlijk de juiste Azure Remote Rendering servergrootte, *Standard* of *Premium*, bepalen en mogelijk aanhouden voor elke 3D-asset of assetgroep die samen in dezelfde sessie worden weergegeven.  

### <a name="on-device-model-selection-list"></a>Selectielijst voor modellen op apparaten

In veel use-cases, zoals een training, taakbegeleiding of marketingtoepassing, kan de set 3D-assets die vaak wordt weergegeven in Azure Remote Rendering vrij statisch zijn. In dergelijke situaties kan een aangepaste set 3D-assets vooraf worden geconverteerd en beschikbaar worden gesteld via een database die de nodige informatie bevat om een selectielijst met aangepaste assets in te vullen. Deze gegevens kunnen vervolgens worden opgehaald uit de mixed reality-toepassing om een selectiemenu te vullen.

Dit kan nog een stap verder gaan door ook een manier te bieden om 3D-privéassets te uploaden die uniek zijn voor elk individu of elke groep. Die lijst met privé-assets kan vervolgens worden gecombineerd met de lijst met gemeenschappelijke, gecureerde assets in de gebruikerservaring voor het selecteren van 3D-assets voor weergave.

### <a name="on-device-onedrive-access"></a>OneDrive-toegang op apparaat

Gezien het feit dat een OneDrive-bestandenkiezer is ingebouwd in de mixed reality-apparaten van Microsoft, is het selecteren van 3D-assets op apparaten met OneDrive aantrekkelijk, met name voor use-cases waar het gebruikelijk is om verschillende of aangepaste 3D-modellen te laden. In dit scenario zou de gebruiker een of meer 3D-assets selecteren via de OneDrive-bestandenkiezer binnen uw mixed reality-toepassing. De 3D-assets worden vervolgens gemigreerd naar een SAS-invoercontainer, geconverteerd naar een SAS-uitvoercontainer en gekoppeld aan de ARR-sessie. Idealiter zou de mixed reality-toepassing een cloudgebaseerd proces aanroepen om deze stappen uit te voeren, in tegenstelling tot het verplaatsen van alle items van OneDrive naar het apparaat en terug naar Azure Blob Storage.

Deze aanpak kan nog een stap verder worden gebracht door een koppeling aan te houden tussen 3D-assets die eerder zijn bekeken, zodat wanneer hetzelfde model opnieuw wordt gekozen uit OneDrive, de toepassing het conversieproces kan omzeilen en de bijbehorende geconverteerde 3D-asset direct kan laden via zijn SAS URI.

Voor meer informatie:

* [Microsoft Power Automate-sjabloon voor replicatie van OneDrive naar Azure Storage](https://flow.microsoft.com/galleries/public/templates/2f90b5d3-029b-4e2e-ad37-1c0fe6d187fe/when-a-file-is-uploaded-to-onedrive-copy-it-to-azure-storage-container/)
* [Overzicht van OneDrive-bestandsopslag-API](/graph/onedrive-concept-overview)

### <a name="direct-cad-access"></a>Directe CAD-toegang

Een overtuigende use-case voor mixed reality zijn ontwerpbeoordelingen van CAD-werk in uitvoering. In dit scenario is de snelste laadtijd van het bureaublad naar de mixed reality van essentieel belang. Een ideale oplossing zou kunnen bestaan uit het ontwikkelen van invoegtoepassingen voor specifieke CAD-toepassingen. Deze invoegtoepassingen zouden elk aspect van het laad-, conversie- en weergaveproces direct beheren:

* Geef een UX op om:
  * De CAD-toepassing te koppelen aan een specifiek mixed reality-apparaat (eenmaal).
  * De geselecteerde geometrie te laten weergeven op dat mixed reality-apparaat.
* Als dit nog niet gebeurd is, moet u de Azure Remote Rendering-sessie opvoeren, zodat deze parallel aan het uploaden en converteren van het CAD-bestand kan worden verwerkt
* Normaliseer CAD-geometriegegevens naar een van de indelingen die worden ondersteund door Azure Remote Rendering
* Zend de genormaliseerde gegevens direct door naar de invoercontainer van Azure Blob Storage
* Initieer het modelconversieproces
* Koppel de uitvoercontainer SAS URI van het model aan de Azure Remote Rendering-sessie
* Informeer de gekoppelde mixed reality-toepassing dat het model beschikbaar en klaar is om te worden weergegeven en verstrek de uitvoercontainer SAS URI, zodat de toepassing deze aan de sessie kan koppelen.

In een veel eenvoudigere maar iets minder gestroomlijnde aanpak zou het opslaan van het 3D-model op een lokale harde schijf kunnen worden geautomatiseerd en een proces worden gestart om dat opgeslagen bestand naar de SAS-invoercontainer te sturen.

### <a name="azure-marketplace"></a>Azure Marketplace

Veel Enterprise-klanten eisen uit veiligheidsoverwegingen dat uw Azure Stack kan worden geïmplementeerd onder hun eigen Azure-accounts en -referenties. Om dit mogelijk te maken, kunt u overwegen om uw door Azure beheerde toepassing zodanig te verpakken dat deze op de Azure Marketplace kan worden gepubliceerd als een Azure-toepassingsaanbieding.

Voor meer informatie:

* [Azure Marketplace](https://azure.microsoft.com/marketplace/)
* [Zelfstudie: Door Azure beheerde toepassingen in Marketplace publiceren](../../../../marketplace/create-new-azure-apps-offer.md)

### <a name="security"></a>Beveiliging

Het is essentieel om uw end-to-end Azure Remote Rendering-oplossing direct af te stemmen op beveiliging. Er zijn vele beveiligingsaspecten die moeten worden overwogen in het ontwerp van uw end-to-endoplossing, waaronder:

* Verificatiestrategieën
* Toegangsbeheer: groepen, beleidsregels en machtigingen
* Multitenancy
* Gegevensopslag en overdrachtversleuteling
* Tijdelijke tokens voor gebruik
* DDoS-aanvallen (Distributed Denial of Service)
* Detectie van bedreigingen
* VPN's en beveiligde netwerken
* Firewalls
* Beheer van certificaten en geheime sleutels
* Kwetsbaarheid van en aanvallen op toepassingen

Voor de verificatie is het verstandig om zoveel mogelijk van de ARR-verificatie en het sessiebeheer te verplaatsen naar een Azure Web Service. Dit leidt tot een beter beheerde en veiligere oplossing.

Voor meer informatie:

* [Verificatie van Azure AD-service](../../../../spatial-anchors/concepts/authentication.md?tabs=csharp#azure-ad-service-authentication)
* [Uw beveiligingsstatus versterken met Azure](https://azure.microsoft.com/overview/security/)
* [Cloudbeveiliging](https://azure.microsoft.com/product-categories/security/)