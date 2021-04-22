---
title: Azure Private Link gebruiken om netwerken veilig te verbinden met Azure Monitor
description: Azure Private Link gebruiken om netwerken veilig te verbinden met Azure Monitor
author: noakup
ms.author: noakuper
ms.topic: conceptual
ms.date: 10/05/2020
ms.openlocfilehash: b3bbd8510c47831c9568bb10c62e2a5ca751902a
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107863025"
---
# <a name="use-azure-private-link-to-securely-connect-networks-to-azure-monitor"></a>Azure Private Link gebruiken om netwerken veilig te verbinden met Azure Monitor

[Azure Private Link](../../private-link/private-link-overview.md) kunt u Azure PaaS-services veilig koppelen aan uw virtuele netwerk met behulp van privé-eindpunten. Voor veel services hebt u alleen een eindpunt per resource ingesteld. Maar Azure Monitor is een groot aantal verschillende onderling verbonden services die samenwerken om uw workloads te bewaken. Als gevolg hiervan hebben we een resource gemaakt met de naam Azure Monitor Private Link Scope (AMPLS). Met AMPLS kunt u de grenzen van uw bewakingsnetwerk definiëren en verbinding maken met uw virtuele netwerk. In dit artikel wordt beschreven wanneer u gebruikt en hoe u een Azure Monitor Private Link instellen.

## <a name="advantages"></a>Voordelen

Met Private Link kunt u het volgende doen:

- Privé verbinding maken met Azure Monitor zonder openbare netwerktoegang te openen
- Zorg ervoor dat uw bewakingsgegevens alleen toegankelijk zijn via geautoriseerde particuliere netwerken
- Gegevens exfiltratie van uw particuliere netwerken voorkomen door specifieke Azure Monitor resources te definiëren die verbinding maken via uw privé-eindpunt
- Uw privé-on-premises netwerk veilig verbinden met Azure Monitor expressroute en Private Link
- Houd al het verkeer binnen het Microsoft Azure backbone-netwerk

Zie Voor meer informatie [Belangrijke voordelen van Private Link.](../../private-link/private-link-overview.md#key-benefits)

## <a name="how-it-works"></a>Uitleg

Azure Monitor Private Link Scope (AMPLS) verbindt privé-eindpunten (en de VNets waarin ze zijn opgenomen) met een of meer Azure Monitor-resources: Log Analytics-werkruimten en Application Insights-onderdelen.

![Diagram van de basisresourcetopologie](./media/private-link-security/private-link-basic-topology.png)

* Met het privé-eindpunt op uw VNet kunnen Azure Monitor-eindpunten worden bereikt via privé-IP's van de pool van uw netwerk, in plaats van te gebruiken voor de openbare IP's van deze eindpunten. Op die manier kunt u uw Azure Monitor gebruiken zonder dat u uw VNet kunt openen voor niet-uitgaand verkeer. 
* Verkeer van het privé-eindpunt naar Azure Monitor resources gaat via de Microsoft Azure backbone en wordt niet doorgeleid naar openbare netwerken. 
* U kunt elk van uw werkruimten of onderdelen configureren om opname en query's van openbare netwerken toe te staan of te weigeren. Dit biedt beveiliging op resourceniveau, zodat u het verkeer naar specifieke resources kunt beheren.

> [!NOTE]
> Een enkele Azure Monitor resource kan deel uitmaken van meerdere AMPLS's, maar u kunt één VNet niet verbinden met meer dan één AMPLS. 

## <a name="planning-your-private-link-setup"></a>Uw configuratie Private Link plannen

Voordat u uw Azure Monitor Private Link instellen, moet u rekening houden met uw netwerktopologie en met name uw DNS-routeringstopologie. 

### <a name="the-issue-of-dns-overrides"></a>Het probleem van DNS-overschrijvingen
Sommige Azure Monitor gebruiken globale eindpunten, wat betekent dat ze aanvragen verwerken die zijn gericht op een werkruimte/onderdeel. Een aantal voorbeelden zijn het Application Insights opname-eindpunt en het query-eindpunt van zowel Application Insights als Log Analytics.

Wanneer u een verbinding Private Link, wordt uw DNS bijgewerkt om Azure Monitor-eindpunten toe te voegen aan privé-IP-adressen uit het IP-bereik van uw VNet. Deze wijziging overschrijven alle eerdere toewijzingen van deze eindpunten, die betekenisvolle gevolgen kunnen hebben, zoals hieronder wordt beschreven. 

### <a name="azure-monitor-private-link-applies-to-all-azure-monitor-resources---its-all-or-nothing"></a>Azure Monitor Private Link is van toepassing op Azure Monitor resources- het is Alles of Niets
Omdat sommige Azure Monitor globaal zijn, is het niet mogelijk om een Private Link maken voor een specifiek onderdeel of specifieke werkruimte. Wanneer u in plaats daarvan een Private Link één Application Insights-onderdeel of Log Analytics-werkruimte in stelt, worden uw DNS-records bijgewerkt voor **alle** Application Insights onderdelen. Elke poging om een onderdeel op te nemen of op te vragen, gaat door de Private Link en mislukt mogelijk. Met betrekking tot Log Analytics zijn opname- en configuratie-eindpunten werkruimte-specifiek, wat betekent dat de privékoppeling alleen van toepassing is op de opgegeven werkruimten. Opname en configuratie van andere werkruimten worden omgeleid naar de standaard openbare Log Analytics-eindpunten.

![Diagram van DNS-overschrijvingen in één VNet](./media/private-link-security/dns-overrides-single-vnet.png)

Dat geldt niet alleen voor een specifiek VNet, maar voor alle VNets die dezelfde DNS-server delen (zie Het probleem van [DNS-overschrijvingen).](#the-issue-of-dns-overrides) Een aanvraag voor het opnemen van logboeken naar een Application Insights onderdeel wordt dus altijd verzonden via de Private Link route. Onderdelen die niet aan de AMPLS zijn gekoppeld, mislukken de Private Link validatie en worden niet uitgevoerd.

> [!NOTE]
> Tot slot: nadat u een verbinding Private Link met één resource hebt ingesteld, is deze van toepassing op Azure Monitor resources in uw netwerk. Voor Application Insights resources is dat Alles of Niets. Dit betekent dat u alle Application Insights resources in uw netwerk moet toevoegen aan uw AMPLS, of geen van de resources.
> 
> Voor het afhandelen van gegevens exfiltratierisico's, is het onze aanbeveling om alle Application Insights- en Log Analytics-resources toe te voegen aan uw AMPLS en het verkeer dat uit uw netwerken weggaat zoveel mogelijk te blokkeren.

### <a name="azure-monitor-private-link-applies-to-your-entire-network"></a>Azure Monitor Private Link is van toepassing op uw hele netwerk
Sommige netwerken bestaan uit meerdere VNets. Als de VNets dezelfde DNS-server gebruiken, overschrijven ze elkaars DNS-toewijzingen en wordt mogelijk de communicatie van elkaar met Azure Monitor verbreekt (zie Het probleem van [DNS-overschrijvingen).](#the-issue-of-dns-overrides) Uiteindelijk kan alleen het laatste VNet communiceren met Azure Monitor, omdat de DNS Azure Monitor-eindpunten toekent aan privé-IP's uit dit VNets-bereik (die mogelijk niet bereikbaar zijn vanaf andere VNets).

![Diagram van DNS-overschrijvingen in meerdere VNets](./media/private-link-security/dns-overrides-multiple-vnets.png)

In het bovenstaande diagram maakt VNet 10.0.1.x eerst verbinding met AMPLS1 en worden de Azure Monitor globale eindpunten vanuit het bereik aan DEP's van het netwerknetwerk. Later maakt VNet 10.0.2.x verbinding met AMPLS2 en overschrijvingen de DNS-toewijzing van dezelfde globale eindpunten met DEP's uit het bereik.  Omdat deze VNets niet zijn peered, kan het eerste VNet deze eindpunten niet bereiken.

> [!NOTE]
> Tot slot: AMPLS-installatie is van invloed op alle netwerken die dezelfde DNS-zones delen. Om te voorkomen dat elkaars DNS-eindpunttoewijzingen worden overschrijven, kunt u het beste één privé-eindpunt instellen in een peered netwerk (zoals een hub-VNet) of de netwerken op DNS-niveau scheiden (bijvoorbeeld door DNS-doorstuurservers of dns-servers volledig te scheiden).

### <a name="hub-spoke-networks"></a>Hub-spoke-netwerken
Hub-spoke-topologies kunnen het probleem van DNS-overschrijvingen voorkomen door een Private Link in te stellen op het hub (hoofd)VNet, in plaats van een Private Link voor elk VNet afzonderlijk in te stellen. Deze installatie is met name zinvol als de Azure Monitor die worden gebruikt door de spoke-VNets worden gedeeld. 

![Hub-and-spoke-single-PE](./media/private-link-security/hub-and-spoke-with-single-private-endpoint.png)

> [!NOTE]
> Mogelijk wilt u liever afzonderlijke privékoppelingen maken voor uw spoke-VNets, bijvoorbeeld om elk VNet toegang te geven tot een beperkte set bewakingsbronnen. In dergelijke gevallen kunt u een toegewezen privé-eindpunt en AMPLS maken voor elk VNet, maar moet u ook controleren of ze niet dezelfde DNS-zones delen om DNS-overschrijvingen te voorkomen.


### <a name="consider-limits"></a>Limieten overwegen

Zoals vermeld in [Beperkingen heeft](#restrictions-and-limitations)het AMPLS-object een aantal limieten, zoals weergegeven in de onderstaande topologie:
* Elk VNet maakt verbinding met **slechts één** AMPLS-object.
* AMPLS B is verbonden met privé-eindpunten van twee VNets (VNet2 en VNet3), met behulp van 2 van de 10 mogelijke privé-eindpuntverbindingen.
* AMPLS A maakt verbinding met twee werkruimten en één Application Insight-onderdeel, met behulp van 3 van de 50 mogelijke Azure Monitor resourcesverbindingen.
* Workspace2 maakt verbinding met AMPLS A en AMPLS B, met behulp van 2 van de 5 mogelijke AMPLS-verbindingen.

![Diagram van AMPLS-limieten](./media/private-link-security/ampls-limits.png)


## <a name="example-connection"></a>Voorbeeldverbinding

Begin met het maken van een Azure Monitor Private Link Scope-resource.

1. Ga naar **Een resource maken** in het Azure Portal zoek naar Azure Monitor Private Link **Bereik**.

   ![Bereik Azure Monitor Private Link zoeken](./media/private-link-security/ampls-find-1c.png)

2. Selecteer **Maken**.
3. Kies een abonnement en resourcegroep.
4. Geef de AMPLS een naam. U kunt het beste een betekenisvolle en duidelijke naam gebruiken, zoals 'AppServerProdTelem'.
5. Selecteer **Controleren + maken**. 

   ![Een Azure Monitor Private Link maken](./media/private-link-security/ampls-create-1d.png)

6. Laat de validatie slagen en selecteer vervolgens **Maken.**

### <a name="connect-azure-monitor-resources"></a>Verbinding maken Azure Monitor resources

Verbind Azure Monitor resources (Log Analytics-werkruimten en Application Insights onderdelen) met uw AMPLS.

1. Selecteer in Azure Monitor Private Link bereik de **optie Azure Monitor Resources** in het menu aan de linkerkant. Selecteer de knop **Add**.
2. Voeg de werkruimte of het onderdeel toe. Als u **de knop** Toevoegen selecteert, wordt er een dialoogvenster weergegeven waarin u de Azure Monitor selecteren. U kunt door uw abonnementen en resourcegroepen bladeren, of u kunt hun naam typen om ze te filteren. Selecteer de werkruimte of het onderdeel en selecteer **Toepassen om** deze toe te voegen aan uw bereik.

    ![Schermopname van het selecteren van een bereik-UX](./media/private-link-security/ampls-select-2.png)

> [!NOTE]
> Als u Azure Monitor resources wilt verwijderen, moet u deze eerst loskoppelen van alle AMPLS-objecten waar ze mee zijn verbonden. Het is niet mogelijk om resources te verwijderen die zijn verbonden met een AMPLS.

### <a name="connect-to-a-private-endpoint"></a>Verbinding maken met een privé-eindpunt

Nu u resources hebt verbonden met uw AMPLS, maakt u een privé-eindpunt om ons netwerk te verbinden. U kunt deze taak uitvoeren in het [Azure Portal Private Link of](https://portal.azure.com/#blade/Microsoft_Azure_Network/PrivateLinkCenterBlade/privateendpoints)binnen uw Azure Monitor Private Link bereik, zoals in dit voorbeeld.

1. Selecteer in uw bereikresource **Privé-eindpuntverbindingen** in het resourcemenu aan de linkerkant. Selecteer **Privé-eindpunt om** het proces voor het maken van eindpunten te starten. U kunt verbindingen die zijn gestart in het Private Link hier ook goedkeuren door ze te selecteren en Goedkeuren **te selecteren.**

    ![Schermopname van UX voor privé-eindpuntverbindingen](./media/private-link-security/ampls-select-private-endpoint-connect-3.png)

2. Kies het abonnement, de resourcegroep en de naam van het eindpunt en de regio waarin het moet zijn op te nemen. De regio moet dezelfde regio zijn als het VNet waar u de regio mee verbindt.

3. Selecteer **Volgende: Resource**. 

4. In het scherm Resource

   a. Kies het **abonnement dat** uw privébereikresource Azure Monitor bevat. 

   b. Voor **resourcetype** kiest u **Microsoft.insights/privateLinkScopes.** 

   c. Kies in **de vervolgkeuzekeuzeop** de resource uw Private Link bereik dat u eerder hebt gemaakt. 

   d. Selecteer **Volgende: Configuratie >.**
      ![Schermopname van het selecteren van Een privé-eindpunt maken](./media/private-link-security/ampls-select-private-endpoint-create-4.png)

5. In het configuratiedeelvenster

   a.    Kies het **virtuele netwerk en** **subnet** dat u wilt verbinden met uw Azure Monitor resources. 
 
   b.    Kies **Ja** voor **Integreren met privé-DNS-zone** en laat automatisch een nieuwe Privé-DNS maken. De werkelijke DNS-zones kunnen verschillen van wat wordt weergegeven in de onderstaande schermopname. 
   > [!NOTE]
   > Als u Nee **kiest en** de DNS-records liever handmatig beheert, voltooit u eerst het instellen van uw Private Link, inclusief dit privé-eindpunt en de AMPLS-configuratie. Vervolgens configureer u uw DNS volgens de instructies in [DNS-configuratie van Azure-privé-eindpunt](../../private-link/private-endpoint-dns.md). Zorg ervoor dat u geen lege records maakt als voorbereiding voor het instellen van uw privékoppeling. De DNS-records die u maakt, kunnen bestaande instellingen overschrijven en de verbinding met Azure Monitor beïnvloeden.
 
   c.    Selecteer **Controleren + maken**.
 
   d.    Validatie laten slagen. 
 
   e.    Selecteer **Maken**. 

    ![Schermopname van De details van het privé-eindpunt selecteren.](./media/private-link-security/ampls-select-private-endpoint-create-5.png)

U hebt nu een nieuw privé-eindpunt gemaakt dat is verbonden met deze AMPLS.

## <a name="review-and-validate-your-private-link-setup"></a>De configuratie van uw Private Link controleren en valideren

### <a name="reviewing-your-endpoints-dns-settings"></a>De DNS-instellingen van uw eindpunt controleren
Op het privé-eindpunt dat u hebt gemaakt, moeten nu vier DNS-zones zijn geconfigureerd:

[![Schermopname van DNS-zones voor privé-eindpunten.](./media/private-link-security/private-endpoint-dns-zones.png)](./media/private-link-security/private-endpoint-dns-zones-expanded.png#lightbox)

* privatelink-monitor-azure-com
* privatelink-oms-opinsights-azure-com
* privatelink-ods-opinsights-azure-com
* privatelink-agentsvc-azure-automation-net

> [!NOTE]
> Elk van deze zones Azure Monitor specifieke eindpunten toe aan privé-IP's uit de pool van DEP's van het VNet. De IP-adressen die in de onderstaande afbeeldingen worden weergegeven, zijn slechts voorbeelden. Uw configuratie moet in plaats daarvan privé-IP's van uw eigen netwerk tonen.

#### <a name="privatelink-monitor-azure-com"></a>Privatelink-monitor-azure-com
Deze zone heeft betrekking op de globale eindpunten die worden gebruikt door Azure Monitor, wat betekent dat deze eindpunten aanvragen verwerken met alle resources, niet met een specifieke. Aan deze zone moeten eindpunten zijn toe te staan voor:
* `in.ai` - Application Insights opname-eindpunt (zowel een globale als een regionale vermelding)
* `api` - Application Insights- en Log Analytics-API-eindpunt
* `live` - Application Insights eindpunt voor live metrische gegevens
* `profiler` - Application Insights profiler-eindpunt
* `snapshot`- Application Insights eindpunt voor momentopnamen [ ![ Schermopname van Privé-DNS zone monitor-azure-com.](./media/private-link-security/dns-zone-privatelink-monitor-azure-com.png)](./media/private-link-security/dns-zone-privatelink-monitor-azure-com-expanded.png#lightbox)

#### <a name="privatelink-oms-opinsights-azure-com"></a>privatelink-oms-opinsights-azure-com
Deze zone heeft betrekking op werkruimtespecifieke toewijzing aan OMS-eindpunten. Als het goed is, ziet u een vermelding voor elke werkruimte die is gekoppeld aan de AMPLS die is verbonden met dit privé-eindpunt.
[![Schermopname van Privé-DNS zone oms-opinsights-azure-com.](./media/private-link-security/dns-zone-privatelink-oms-opinsights-azure-com.png)](./media/private-link-security/dns-zone-privatelink-oms-opinsights-azure-com-expanded.png#lightbox)

#### <a name="privatelink-ods-opinsights-azure-com"></a>privatelink-ods-opinsights-azure-com
Deze zone heeft betrekking op werkruimtespecifieke toewijzing aan ODS-eindpunten: het opname-eindpunt van Log Analytics. Als het goed is, ziet u een vermelding voor elke werkruimte die is gekoppeld aan de AMPLS die is verbonden met dit privé-eindpunt.
[![Schermopname Privé-DNS zone ods-opinsights-azure-com.](./media/private-link-security/dns-zone-privatelink-ods-opinsights-azure-com.png)](./media/private-link-security/dns-zone-privatelink-ods-opinsights-azure-com-expanded.png#lightbox)

#### <a name="privatelink-agentsvc-azure-automation-net"></a>privatelink-agentsvc-azure-automation-net
Deze zone heeft betrekking op werkruimtespecifieke toewijzing aan de automatiserings-eindpunten van de agentservice. Als het goed is, ziet u een vermelding voor elke werkruimte die is gekoppeld aan de AMPLS die is verbonden met dit privé-eindpunt.
[![Schermopname Privé-DNS zoneagent svc-azure-automation-net.](./media/private-link-security/dns-zone-privatelink-agentsvc-azure-automation-net.png)](./media/private-link-security/dns-zone-privatelink-agentsvc-azure-automation-net-expanded.png#lightbox)

#### <a name="privatelink-blob-core-windows-net"></a>privatelink-blob-core-windows-net
In deze zone configureert u connectiviteit met het opslagaccount van de oplossingspakketten van de globale agents. Via deze oplossing kunnen agents nieuwe of bijgewerkte oplossingspakketten (ook wel management packs genoemd) downloaden. Er is slechts één vermelding vereist voor het verwerken van Log Analytics-agents, ongeacht hoeveel werkruimten er worden gebruikt.
[![Schermopname van Privé-DNS zone blob-core-windows-net.](./media/private-link-security/dns-zone-privatelink-blob-core-windows-net.png)](./media/private-link-security/dns-zone-privatelink-blob-core-windows-net-expanded.png#lightbox)
> [!NOTE]
> Deze vermelding wordt alleen toegevoegd aan instellingen voor privékoppelingen die zijn gemaakt op of na 19 april 2021.


### <a name="validating-you-are-communicating-over-a-private-link"></a>Valideren of u communiceert via een Private Link
* Als u wilt controleren of uw aanvragen nu worden verzonden via het privé-eindpunt en de privé-IP-eindpunten, kunt u deze controleren met een hulpprogramma voor netwerktracking of zelfs uw browser. Als u bijvoorbeeld probeert een query uit te voeren op uw werkruimte of toepassing, moet u ervoor zorgen dat de aanvraag wordt verzonden naar het privé-IP-adres dat is toegezonden aan het API-eindpunt. In dit voorbeeld is dat *172.17.0.9.*

    Opmerking: Sommige browsers gebruiken mogelijk andere DNS-instellingen (zie [BROWSER-DNS-instellingen).](#browser-dns-settings) Zorg ervoor dat uw DNS-instellingen van toepassing zijn.

* Om ervoor te zorgen dat uw werkruimte of onderdeel geen aanvragen ontvangt van openbare netwerken (niet verbonden  via AMPLS), stelt u de openbare opname en queryvlaggen van de resource in op Nee, zoals wordt uitgelegd in Toegang beheren van buiten het bereik van [privékoppelingen.](#manage-access-from-outside-of-private-links-scopes)

* Gebruik vanaf een client in uw beveiligde netwerk naar een van de `nslookup` eindpunten die worden vermeld in uw DNS-zones. Deze moet door uw DNS-server worden opgelost met de toe te voegen privé-IP's in plaats van de openbare IP's die standaard worden gebruikt.


## <a name="configure-log-analytics"></a>Log Analytics configureren

Ga naar Azure Portal. In het resourcemenu van uw Log Analytics-werkruimte ziet u aan de linkerkant een item met de naam Netwerkisolatie.  U kunt twee verschillende staten in dit menu bepalen.

![LA-netwerkisolatie](./media/private-link-security/ampls-log-analytics-lan-network-isolation-6.png)

### <a name="connected-azure-monitor-private-link-scopes"></a>Verbonden Azure Monitor Private Link bereiken
Alle scopes die zijn verbonden met de werkruimte, worden in dit scherm weergegeven. Door verbinding te maken met bereiken (AMPLS's) kan netwerkverkeer van het virtuele netwerk dat is verbonden met elke AMPLS deze werkruimte bereiken. Het maken van een verbinding via hier heeft hetzelfde effect als het instellen ervan op het bereik, zoals we hebben gedaan in Verbinding maken [met Azure Monitor resources.](#connect-azure-monitor-resources) Als u een nieuwe verbinding wilt toevoegen, **selecteert u Toevoegen** en selecteert u Azure Monitor Private Link bereik. Selecteer **Toepassen om** er verbinding mee te maken. Houd er rekening mee dat een werkruimte verbinding kan maken met 5 AMPLS-objecten, zoals vermeld in [Beperkingen.](#restrictions-and-limitations) 

### <a name="manage-access-from-outside-of-private-links-scopes"></a>Toegang beheren van buiten het bereik van privékoppelingen
De instellingen onder aan deze pagina bepalen de toegang van openbare netwerken, wat betekent dat netwerken die niet zijn verbonden via de vermelde bereiken (AMPLS's). Als **u Openbare netwerktoegang voor opname toestaan instelt** op Geen blokkeert u de opname van logboeken van computers buiten de verbonden bereiken.  Als **u Openbare netwerktoegang voor query's toestaan instelt** **op** Nee, worden query's die afkomstig zijn van computers buiten de bereiken, niet toegestaan. Dit omvat query's die worden uitgevoerd via werkmappen, dashboards, op API gebaseerde clientervaringen, inzichten in de Azure Portal en meer. Ervaringen die buiten het Azure Portal worden uitgevoerd en die query's uitvoeren op Log Analytics-gegevens moeten ook worden uitgevoerd binnen het privé-gekoppelde VNET.

### <a name="exceptions"></a>Uitzonderingen
Toegang beperken zoals hierboven wordt uitgelegd, is niet van toepassing op de Azure Resource Manager en heeft daarom de volgende beperkingen:
* Toegang tot gegevens: hoewel het blokkeren/toestaan van query's van openbare netwerken van toepassing is op de meeste Log Analytics-ervaringen, wordt in sommige ervaringen een query uitgevoerd op gegevens via Azure Resource Manager en kunnen er dus geen query's worden uitgevoerd op gegevens tenzij Private Link-instellingen ook worden toegepast op de Resource Manager (de functie komt binnenkort beschikbaar). Voorbeelden zijn Azure Monitor oplossingen, Werkmappen en Inzichten en de LogicApp-connector.
* Werkruimtebeheer: wijzigingen in de werkruimte-instellingen en configuratie (inclusief het in- of uitschakelen van deze toegangsinstellingen) worden beheerd door Azure Resource Manager. Beperk de toegang tot werkruimtebeheer met behulp van de juiste rollen, machtigingen, netwerkbesturingselementen en controle. Zie rollen, machtigingen Azure Monitor [beveiliging voor meer informatie.](../roles-permissions-security.md)

> [!NOTE]
> Logboeken en metrische gegevens [](../essentials/diagnostic-settings.md) die via diagnostische instellingen naar een werkruimte worden geüpload, gaan via een beveiligd privé-Microsoft-kanaal en worden niet beheerd door deze instellingen.

### <a name="log-analytics-solution-packs-download"></a>Log Analytics-oplossingspakketten downloaden
Log Analytics-agents moeten toegang hebben tot een globaal opslagaccount om oplossingspakketten te downloaden. Private Link die zijn gemaakt op of na 19 april 2021, kunnen de opslag van de oplossingspakketten van de agents via de private link bereiken. Dit wordt mogelijk gemaakt via de nieuwe DNS-zone die is gemaakt voor [blob.core.windows.net](#privatelink-blob-core-windows-net).

Als uw Private Link is gemaakt vóór 19 april 2021, wordt de opslag van oplossingspakketten niet via een privékoppeling bereikt. U kunt dit op een van de volgende dingen doen:
* Maak uw AMPLS en het privé-eindpunt dat er verbinding mee heeft, opnieuw
* Sta uw agents toe het opslagaccount te bereiken via het openbare eindpunt door de volgende regels toe te voegen aan uw firewall-allowlist:

    | Cloudomgeving | Agentresource | Poorten | Richting |
    |:--|:--|:--|:--|
    |Openbare Azure-peering     | scadvisorcontent.blob.core.windows.net         | 443 | Uitgaand
    |Azure Government | usbn1oicore.blob.core.usgovcloudapi.net | 443 |  Uitgaand
    |Azure China 21Vianet      | mceast2oicore.blob.core.chinacloudapi.cn| 443 | Uitgaand


## <a name="configure-application-insights"></a>Application Insights configureren

Ga naar Azure Portal. In uw Azure Monitor Application Insights onderdeelresource staat een menu-item **Netwerkisolatie** aan de linkerkant. U kunt twee verschillende staten in dit menu bepalen.

![AI-netwerkisolatie](./media/private-link-security/ampls-application-insights-lan-network-isolation-6.png)

Eerst kunt u deze Application Insights verbinden met Azure Monitor Private Link bereiken waar u toegang tot hebt. Selecteer **Toevoegen** en selecteer het **Azure Monitor Private Link Bereik.** Selecteer Toepassen om er verbinding mee te maken. Alle verbonden scopes worden in dit scherm weergegeven. Door deze verbinding te maken, kan netwerkverkeer in de verbonden virtuele netwerken dit onderdeel bereiken. Dit heeft hetzelfde effect als het verbinden ervan vanuit het bereik als in [Connecting Azure Monitor resources](#connect-azure-monitor-resources). 

Vervolgens kunt u bepalen hoe deze resource kan worden bereikt buiten de eerder vermelde PRIVATE Link-bereiken (AMPLS). Als u **Openbare netwerktoegang voor** opname toestaan in stelt op Nee, kunnen computers of SDK's buiten de verbonden bereiken geen gegevens uploaden naar dit onderdeel.  Als u Openbare **netwerktoegang voor** query's toestaan in stelt op **Nee,** hebben computers buiten de bereiken geen toegang tot gegevens in Application Insights resource. Deze gegevens omvatten toegang tot APM-logboeken, metrische gegevens en de livestream met metrische gegevens, evenals ervaringen die erop zijn gebouwd, zoals werkmappen, dashboards, clientervaringen op basis van query's op API's, inzichten in de Azure Portal en meer. 

> [!NOTE]
> Verbruikservaringen buiten de portal moeten ook worden uitgevoerd op het privé-gekoppelde VNET met de bewaakte workloads.

U moet resources die de bewaakte workloads hosten, toevoegen aan de privékoppeling. Zie bijvoorbeeld Using [Private Endpoints for Azure Web App (Privé-eindpunten gebruiken voor Azure Web App).](../../app-service/networking/private-endpoint.md)

Toegang op deze manier beperken is alleen van toepassing op gegevens in Application Insights resource. Configuratiewijzigingen, waaronder het in- of uitschakelen van deze toegangsinstellingen, worden echter beheerd door Azure Resource Manager. Daarom moet u de toegang tot Resource Manager met behulp van de juiste rollen, machtigingen, netwerkbesturingselementen en controle. Zie rollen, machtigingen [Azure Monitor beveiliging voor meer informatie.](../roles-permissions-security.md)

> [!NOTE]
> Als u een werkruimte op basis van Application Insights wilt beveiligen, moet u zowel de toegang tot Application Insights resource als de onderliggende Log Analytics-werkruimte vergrendelen.
>
> Voor diagnostische gegevens op codeniveau (profiler/debugger) moet u uw eigen opslagaccount bieden ter ondersteuning van private link. [](../app/profiler-bring-your-own-storage.md)

### <a name="handling-the-all-or-nothing-nature-of-private-links"></a>Het verwerken van de aard alles-of-niets van privékoppelingen
Zoals uitgelegd in [Uw Private Link-installatie](#planning-your-private-link-setup)plannen, is het instellen van een Private Link zelfs voor één resource van invloed op alle Azure Monitor-resources in die netwerken en in andere netwerken die dezelfde DNS delen. Dit gedrag kan uw onboardingproces lastig maken. Houd rekening met de volgende opties:

* All in: de eenvoudigste en veiligste aanpak is om al uw Application Insights toe te voegen aan de AMPLS. Laat de vlaggen 'Openbare internettoegang toestaan voor opname/query' ingesteld op Ja (de standaardinstelling) voor onderdelen die u ook vanuit andere netwerken wilt gebruiken.
* Netwerken isoleren: als u met behulp van spoke-vnets bent (of kunt uitlijnen met), volgt u de richtlijnen in [Hub-spoke-netwerktopologie in Azure](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke). Stel vervolgens afzonderlijke private link-instellingen in de relevante spoke-VNets in. Zorg ervoor dat u ook DNS-zones scheidt, omdat het delen van DNS-zones met andere spoke-netwerken [dns-overschrijvingen veroorzaakt.](#the-issue-of-dns-overrides)
* Aangepaste DNS-zones gebruiken voor specifieke apps: met deze oplossing hebt u toegang tot bepaalde Application Insights-onderdelen via een Private Link, terwijl al het andere verkeer via de openbare routes wordt behouden.
    - Stel een aangepaste [privé-DNS-zone in](../../private-link/private-endpoint-dns.md)en geef deze een unieke naam, zoals internal.monitor.azure.com
    - Maak een AMPLS en een privé-eindpunt en kies ervoor **om niet** automatisch te integreren met privé-DNS
    - Ga naar Privé-eindpunt -> DNS-configuratie en bekijk de voorgestelde toewijzing van FQDN's.
    - Kies Configuratie toevoegen en kies de internal.monitor.azure.com zone die u zojuist hebt gemaakt
    - Records toevoegen voor de bovenstaande ![ schermopname van de geconfigureerde DNS-zone](./media/private-link-security/private-endpoint-global-dns-zone.png)
    - Ga naar uw Application Insights en kopieer de [verbindingsreeks](../app/sdk-connection-string.md).
    - Apps of scripts die dit onderdeel via een Private Link willen aanroepen, moeten de connection string gebruiken met EndpointSuffix=internal.monitor.azure.com
* Eindpunten via hosts-bestanden in plaats van DNS toe te staan : als u een Private Link alleen toegang hebt vanaf een specifieke computer/VM in uw netwerk:
    - Stel een AMPLS en een privé-eindpunt in en kies ervoor **om niet** automatisch te integreren met privé-DNS 
    - Configureer de bovenstaande A-records op een computer met de app in het hosts-bestand


## <a name="use-apis-and-command-line"></a>API's en opdrachtregel gebruiken

U kunt het eerder beschreven proces automatiseren met behulp Azure Resource Manager sjablonen, REST en opdrachtregelinterfaces.

Als u private link-scopes wilt maken en beheren, gebruikt [u de REST API](/rest/api/monitor/privatelinkscopes(preview)/private%20link%20scoped%20resources%20(preview)) of Azure CLI [(az monitor private-link-scope).](/cli/azure/monitor/private-link-scope)

Als u de netwerktoegang wilt beheren, gebruikt u de vlaggen `[--ingestion-access {Disabled, Enabled}]` `[--query-access {Disabled, Enabled}]` en in Log [Analytics-werkruimten](/cli/azure/monitor/log-analytics/workspace) [of Application Insights onderdelen](/cli/azure/monitor/app-insights/component).

## <a name="collect-custom-logs-and-iis-log-over-private-link"></a>Aangepaste logboeken en IIS-logboeken verzamelen via Private Link

Opslagaccounts worden gebruikt in het opnameproces van aangepaste logboeken. Standaard worden door de service beheerde opslagaccounts gebruikt. Als u aangepaste logboeken echter wilt opnemen in privékoppelingen, moet u uw eigen opslagaccounts gebruiken en deze koppelen aan Een of meer Log Analytics-werkruimten. Zie voor meer informatie over het instellen van dergelijke accounts met behulp van [de opdrachtregel](/cli/azure/monitor/log-analytics/workspace/linked-storage).

Zie Opslagaccounts van de klant voor logboekgegevens opnemen voor meer informatie over het meenemen van uw [eigen opslagaccount](private-storage.md)

## <a name="restrictions-and-limitations"></a>Beperkingen en limieten

### <a name="ampls"></a>AMPLS
Het AMPLS-object heeft een aantal limieten die u moet overwegen bij het plannen van Private Link installatie:

* Een VNet kan slechts verbinding maken met één AMPLS-object. Dit betekent dat het AMPLS-object toegang moet bieden tot alle Azure Monitor resources waar het VNet toegang tot moet hebben.
* Een Azure Monitor resource (werkruimte of Application Insights) kan verbinding maken met 5 AMPL's.
* Een AMPLS-object kan verbinding maken met 50 Azure Monitor resources.
* Een AMPLS-object kan verbinding maken met 10 privé-eindpunten.

Zie [Limieten overwegen](#consider-limits) voor een diepere beoordeling van deze limieten.

### <a name="agents"></a>Agents

De nieuwste versies van de Windows- en Linux-agents moeten worden gebruikt ter ondersteuning van beveiligde opname in Log Analytics-werkruimten. Oudere versies kunnen geen bewakingsgegevens uploaden via een particulier netwerk.

**Windows-agent voor Log Analytics**

Gebruik de Log Analytics-agent versie 10.20.18038.0 of hoger.

**Linux-agent voor Log Analytics**

Gebruik agentversie 1.12.25 of hoger. Als dat niet mogelijk is, voert u de volgende opdrachten uit op uw VM.

```cmd
$ sudo /opt/microsoft/omsagent/bin/omsadmin.sh -X
$ sudo /opt/microsoft/omsagent/bin/omsadmin.sh -w <workspace id> -s <workspace key>
```

### <a name="azure-portal"></a>Azure Portal

Als u Azure Monitor portalervaringen zoals Application Insights en Log Analytics wilt gebruiken, moet u toestaan dat de Azure Portal- en Azure Monitor-extensies toegankelijk zijn in de particuliere netwerken. Voeg **azureActiveDirectory,** **AzureResourceManager,** **AzureFrontDoor.FirstParty** en **AzureFrontdoor.Frontend-servicetags** [toe](../../firewall/service-tags.md) aan uw netwerkbeveiligingsgroep.

### <a name="querying-data"></a>Query’s op gegevens uitvoeren
De [ `externaldata` operator](/azure/data-explorer/kusto/query/externaldata-operator?pivots=azuremonitor) wordt niet ondersteund voor een Private Link, omdat deze gegevens leest uit opslagaccounts, maar niet garandeert dat de opslag privé wordt gebruikt.

### <a name="programmatic-access"></a>Toegang op programmeerniveau

Als u de REST API, [CLI](/cli/azure/monitor) of PowerShell wilt gebruiken met Azure Monitor in particuliere netwerken, voegt u de [servicetags](../../virtual-network/service-tags-overview.md)  **AzureActiveDirectory** en **AzureResourceManager toe** aan uw firewall.

### <a name="application-insights-sdk-downloads-from-a-content-delivery-network"></a>Application Insights SDK-downloads van een netwerk voor contentlevering

Bundel de JavaScript-code in uw script, zodat de browser niet probeert code te downloaden van een CDN. Er wordt een voorbeeld gegeven op [GitHub](https://github.com/microsoft/ApplicationInsights-JS#npm-setup-ignore-if-using-snippet-setup)

### <a name="browser-dns-settings"></a>DNS-instellingen van browser

Als u verbinding maakt met uw Azure Monitor-resources via een Private Link, moet het verkeer naar deze resources via het privé-eindpunt gaan dat in uw netwerk is geconfigureerd. Als u het privé-eindpunt wilt inschakelen, moet u uw DNS-instellingen bijwerken zoals uitgelegd in [Verbinding maken met een privé-eindpunt.](#connect-to-a-private-endpoint) Sommige browsers gebruiken hun eigen DNS-instellingen in plaats van de instellingen die u in stelt. De browser kan proberen verbinding te maken met Azure Monitor openbare eindpunten en de Private Link te omzeilen. Controleer of de instellingen van uw browsers oude DNS-instellingen niet overschrijven of in de cache opgeslagen. 

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [privéopslag](private-storage.md)
