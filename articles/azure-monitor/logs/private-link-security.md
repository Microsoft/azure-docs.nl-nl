---
title: Azure Private Link gebruiken om netwerken veilig te verbinden met Azure Monitor
description: Azure Private Link gebruiken om netwerken veilig te verbinden met Azure Monitor
author: noakup
ms.author: noakuper
ms.topic: conceptual
ms.date: 10/05/2020
ms.subservice: ''
ms.openlocfilehash: 55a3cd6b02b9eeb774a084552c086acbfb9966cb
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100610053"
---
# <a name="use-azure-private-link-to-securely-connect-networks-to-azure-monitor"></a>Azure Private Link gebruiken om netwerken veilig te verbinden met Azure Monitor

Met de [persoonlijke Azure-koppeling](../../private-link/private-link-overview.md) kunt u Azure PaaS-services veilig koppelen aan uw virtuele netwerk met behulp van privé-eind punten. Voor veel services hebt u zojuist een eind punt ingesteld per resource. Azure Monitor is echter een Constellation met verschillende onderling verbonden services die samen werken om uw workloads te bewaken. Als gevolg hiervan hebben we een resource met de naam een Azure Monitor AMPLS (private link scope) gemaakt. Met AMPLS kunt u de grenzen van uw bewakings netwerk definiëren en verbinding maken met uw virtuele netwerk. In dit artikel wordt beschreven hoe u een Azure Monitor persoonlijk koppelings bereik kunt instellen.

## <a name="advantages"></a>Voordelen

Met een persoonlijke koppeling kunt u het volgende doen:

- Privé verbinding maken met Azure Monitor zonder open bare netwerk toegang te openen
- Controleren of uw bewakings gegevens alleen toegankelijk zijn via geautoriseerde particuliere netwerken
- Gegevens exfiltration uit uw particuliere netwerken voor komen door specifieke Azure Monitor bronnen te definiëren die verbinding maken via uw persoonlijke eind punt
- Uw privé on-premises netwerk veilig verbinden met Azure Monitor met behulp van ExpressRoute en een persoonlijke koppeling
- Bewaar al het verkeer binnen het Microsoft Azure backbone-netwerk

Zie  [belang rijke voor delen van een persoonlijke koppeling](../../private-link/private-link-overview.md#key-benefits)voor meer informatie.

## <a name="how-it-works"></a>Uitleg

Azure Monitor AMPLS (private link scope) verbindt persoonlijke eind punten (en de VNets die ze bevatten) aan een of meer Azure Monitor resources: Log Analytics werk ruimten en Application Insights onderdelen.

![Diagram van basis resource topologie](./media/private-link-security/private-link-basic-topology.png)

* Met het persoonlijke eind punt op uw VNet kan het Azure Monitor eind punten bereiken via privé-IP-adressen uit de groep van uw netwerk, in plaats van gebruik te maken van de open bare IP-adressen van deze eind punten. Zo kunt u uw Azure Monitor-resources blijven gebruiken zonder dat uw VNet wordt geopend met niet-vereist uitgaand verkeer. 
* Verkeer van het persoonlijke eind punt naar uw Azure Monitor-resources gaat over de Microsoft Azure backbone en worden niet doorgestuurd naar open bare netwerken. 
* U kunt elk van uw werk ruimten of onderdelen zo configureren dat opname en query's van open bare netwerken worden toegestaan of geweigerd. Die een beveiliging op resource niveau biedt, zodat u verkeer naar specifieke resources kunt beheren.

> [!NOTE]
> Een enkele Azure Monitor resource kan tot meerdere AMPLSs behoren, maar u kunt geen enkele VNet verbinden met meer dan één AMPLS. 

## <a name="planning-your-private-link-setup"></a>De configuratie van uw persoonlijke koppeling plannen

Voordat u uw Azure Monitor voor het instellen van persoonlijke koppelingen instelt, moet u rekening houden met uw netwerk topologie en met name uw DNS-routerings topologie. 

### <a name="the-issue-of-dns-overrides"></a>Het probleem van DNS-onderdrukkingen
Sommige Azure Monitor Services gebruiken globale eind punten, wat betekent dat ze aanvragen behandelen die gericht zijn op een werk ruimte/onderdeel. Enkele voor beelden zijn het Application Insights opname-eind punt en het query-eind punt van zowel Application Insights als Log Analytics.

Wanneer u een verbinding met een persoonlijke verbinding instelt, wordt uw DNS bijgewerkt om Azure Monitor-eind punten toe te wijzen aan privé-IP-adressen uit het IP-adres bereik van uw VNet. Deze wijziging overschrijft een eerdere toewijzing van deze eind punten, wat zinvolle implicaties kan hebben, zoals hieronder wordt besproken. 

### <a name="azure-monitor-private-link-applies-to-all-azure-monitor-resources---its-all-or-nothing"></a>Azure Monitor persoonlijke koppeling is van toepassing op alle Azure Monitor resources. het is allemaal of niets
Omdat sommige Azure Monitor-eind punten globaal zijn, is het onmogelijk om een koppeling van een particuliere verbinding te maken voor een specifiek onderdeel of werk ruimte. In plaats daarvan worden uw DNS-records bijgewerkt voor **alle** Application Insights-onderdelen wanneer u een persoonlijke koppeling instelt op een enkel Application Insights onderdeel. Een poging om een onderdeel in te stellen of bij te werken, gaat via de privé koppeling en kan mogelijk mislukken. Op dezelfde manier zorgt het instellen van een persoonlijke koppeling naar één werk ruimte ervoor dat alle Log Analytics query's door lopen in het query-eind punt van de persoonlijke koppeling (maar geen opname aanvragen die werk ruimte-specifieke eind punten hebben).

![Diagram van DNS-onderdrukkingen in één VNet](./media/private-link-security/dns-overrides-single-vnet.png)

Dat is niet alleen van toepassing op een specifiek VNet, maar voor alle VNets die dezelfde DNS-server delen (Zie [het probleem van DNS-onderdrukkingen](#the-issue-of-dns-overrides)). Bijvoorbeeld, de aanvraag voor het opnemen van logboeken naar een Application Insights onderdeel wordt altijd via de route van de persoonlijke koppeling verzonden. Onderdelen die niet aan de AMPLS zijn gekoppeld, zullen de validatie van de persoonlijke verbinding niet door lopen.

> [!NOTE]
> Sluiten: Zodra uw verbinding met een persoonlijke koppeling met één resource is ingesteld, is deze van toepassing op alle Azure Monitor resources in uw netwerk. Dit is allemaal of niets. Dit betekent dat u alle Azure Monitor resources in uw netwerk moet toevoegen aan uw AMPLS, of geen van beide.

### <a name="azure-monitor-private-link-applies-to-your-entire-network"></a>Azure Monitor persoonlijke koppeling is van toepassing op uw hele netwerk
Sommige netwerken bestaan uit meerdere VNets. Als de VNets dezelfde DNS-server gebruikt, worden de DNS-toewijzingen van elkaar overschreven en wordt mogelijk ook de communicatie met Azure Monitor verbroken (Zie [het probleem van DNS-onderdrukkingen](#the-issue-of-dns-overrides)). Uiteindelijk kan alleen het laatste VNet communiceren met Azure Monitor, omdat de DNS Azure Monitor-eind punten toewijst aan privé Ip's uit dit VNets bereik (wat mogelijk niet bereikbaar is vanuit andere VNets).

![Diagram van DNS-onderdrukkingen in meerdere VNets](./media/private-link-security/dns-overrides-multiple-vnets.png)

In het bovenstaande diagram maakt VNet 10.0.1. x eerst verbinding met AMPLS1 en wijst de Azure Monitor globale eind punten toe aan IP-adressen van het bereik. Later, VNet 10.0.2. x maakt verbinding met AMPLS2 en overschrijft de DNS-toewijzing van *dezelfde globale eind punten* met IP-adressen van het bereik. Omdat deze VNets niet zijn gekoppeld, kan de eerste VNet nu deze eind punten niet bereiken.

> [!NOTE]
> Te sluiten: AMPLS Setup is van invloed op alle netwerken die dezelfde DNS-zones delen. Om te voor komen dat de DNS-eindpunt toewijzingen van elkaar worden overschreven, is het raadzaam om één persoonlijk eind punt in te stellen op een gekoppeld netwerk (zoals een hub VNet) of door de netwerken op het DNS-niveau te scheiden (Foe-voor beeld met behulp van DNS-doorstuur servers of afzonderlijke DNS-server).

### <a name="hub-spoke-networks"></a>Hub-spoke-netwerken
Hub-spoke-topologieën kunnen het probleem van DNS-onderdrukkingen voor komen door een persoonlijke koppeling in te stellen op de hub (hoofd) VNet, in plaats van een persoonlijke koppeling voor elk VNet afzonderlijk in te stellen. Deze installatie is vooral handig als de Azure Monitor resources die worden gebruikt door de spoke VNets worden gedeeld. 

![Hub en spoke-single-PE](./media/private-link-security/hub-and-spoke-with-single-private-endpoint.png)

> [!NOTE]
> U kunt opzettelijk afzonderlijke persoonlijke koppelingen maken voor uw spoke-VNets, bijvoorbeeld om elk VNet toegang te geven tot een beperkt aantal bewakings bronnen. In dergelijke gevallen kunt u een speciaal privé-eind punt en AMPLS voor elk VNet maken, maar moet u ook controleren of ze niet dezelfde DNS-zones delen om DNS-onderdrukkingen te voor komen.


### <a name="consider-limits"></a>Beperkingen overwegen

Zoals vermeld in [beperkingen en beperkingen](#restrictions-and-limitations), heeft het object AMPLS een aantal limieten, zoals wordt weer gegeven in de onderstaande topologie:
* Elk VNet is verbonden met slechts **één** AMPLS-object.
* AMPLS B is verbonden met persoonlijke eind punten van twee VNets (VNet2 en VNet3), met behulp van 2 van de 10 mogelijke particuliere endpoint-verbindingen.
* AMPLS A maakt verbinding met twee werk ruimten en één toepassings inzicht onderdeel met behulp van 3 van de 50 mogelijke Azure Monitor bronnen verbindingen.
* Workspace2 maakt verbinding met AMPLS A en AMPLS B, met behulp van 2 van de 5 mogelijke AMPLS-verbindingen.

![Diagram van AMPLS-limieten](./media/private-link-security/ampls-limits.png)


## <a name="example-connection"></a>Voorbeeld verbinding

Maak eerst een Azure Monitor-bron voor een persoonlijk koppelings bereik.

1. Ga naar **een resource maken** in de Azure Portal en zoek naar **Azure monitor persoonlijk koppelings bereik**.

   ![Azure Monitor bereik van een persoonlijke koppeling zoeken](./media/private-link-security/ampls-find-1c.png)

2. Selecteer **Maken**.
3. Kies een abonnement en resource groep.
4. Geef een naam op voor de AMPLS. U kunt het beste een duidelijke en duidelijke naam gebruiken, zoals ' AppServerProdTelem '.
5. Selecteer **Controleren + maken**. 

   ![Azure Monitor bereik voor persoonlijke koppelingen maken](./media/private-link-security/ampls-create-1d.png)

6. Laat de validatie slagen en selecteer vervolgens **maken**.

### <a name="connect-azure-monitor-resources"></a>Azure Monitor-resources verbinden

Verbind Azure Monitor resources (Log Analytics werk ruimten en Application Insights onderdelen) met uw AMPLS.

1. Selecteer in uw Azure Monitor bereik voor persoonlijke koppelingen **Azure monitor resources** in het menu aan de linkerkant. Selecteer de knop **Add**.
2. Voeg de werk ruimte of het onderdeel toe. Als u de knop **toevoegen** selecteert, wordt er een dialoog venster geopend waarin u Azure monitor resources kunt selecteren. U kunt door uw abonnementen en resource groepen bladeren of u kunt hun naam typen om deze te filteren. Selecteer de werk ruimte of het onderdeel en selecteer **Toep assen** om ze toe te voegen aan uw bereik.

    ![Scherm afbeelding van een bereik UX selecteren](./media/private-link-security/ampls-select-2.png)

> [!NOTE]
> Als u Azure Monitor resources wilt verwijderen, moet u deze eerst loskoppelen van alle AMPLS-objecten waarmee ze zijn verbonden. Het is niet mogelijk om resources te verwijderen die zijn verbonden met een AMPLS.

### <a name="connect-to-a-private-endpoint"></a>Verbinding maken met een persoonlijk eind punt

Nu u resources hebt verbonden met uw AMPLS, maakt u een persoonlijk eind punt om verbinding te maken met ons netwerk. U kunt deze taak uitvoeren in het [Azure Portal persoonlijke koppelingen centrum](https://portal.azure.com/#blade/Microsoft_Azure_Network/PrivateLinkCenterBlade/privateendpoints)of binnen het bereik van uw Azure monitor persoonlijke koppeling, zoals in dit voor beeld wordt gedaan.

1. Selecteer in de resource voor uw bereik **persoonlijke eindpunt verbindingen** in het menu van de linkerkant. Selecteer **persoonlijk eind punt** om het proces voor het maken van een eind punt te starten. U kunt ook de verbindingen die in het persoonlijke koppelings centrum zijn gestart, goed keuren door ze te selecteren en **goed keuren** te selecteren.

    ![Scherm opname van de UX-verbindingen van privé-eind punten](./media/private-link-security/ampls-select-private-endpoint-connect-3.png)

2. Kies het abonnement, de resource groep en de naam van het eind punt en de regio waarin het moet worden opgenomen. De regio moet dezelfde regio zijn als het VNet waarmee u verbinding maakt.

3. Selecteer **Volgende: Resource**. 

4. In het scherm resource,

   a. Kies het **abonnement** dat uw Azure monitor persoonlijke bereik resource bevat. 

   b. Kies voor **resource type** **micro soft. Insights/privateLinkScopes**. 

   c. Kies in de vervolg keuzelijst **resource** uw persoonlijke-koppelings bereik dat u eerder hebt gemaakt. 

   d. Selecteer **volgende: configuratie >**.
      ![Scherm opname van Selecteer persoonlijk eind punt maken](./media/private-link-security/ampls-select-private-endpoint-create-4.png)

5. In het deel venster Configuratie,

   a.    Kies het **virtuele netwerk** en het **subnet** dat u wilt verbinden met uw Azure monitor-resources. 
 
   b.    Kies **Ja** om te **integreren met een privé-DNS-zone** en laat automatisch een nieuwe privé-DNS zone maken. De daad werkelijke DNS-zones kunnen afwijken van wat wordt weer gegeven in de onderstaande scherm afbeelding. 
   > [!NOTE]
   > Als u ervoor kiest **geen** DNS-records hand matig te beheren, moet u eerst uw persoonlijke koppeling instellen, met inbegrip van dit persoonlijke eind punt en de AMPLS-configuratie. Vervolgens configureer u uw DNS volgens de instructies in [DNS-configuratie van Azure-privé-eindpunt](../../private-link/private-endpoint-dns.md). Zorg ervoor dat u geen lege records maakt als voorbereiding voor het instellen van uw privékoppeling. De DNS-records die u maakt, kunnen bestaande instellingen overschrijven en de verbinding met Azure Monitor beïnvloeden.
 
   c.    Selecteer **Controleren + maken**.
 
   d.    Laat de validatie slagen. 
 
   e.    Selecteer **Maken**. 

    ![Scherm opname van Selecteer persoonlijke Endpoint2 maken](./media/private-link-security/ampls-select-private-endpoint-create-5.png)

U hebt nu een nieuw persoonlijk eind punt gemaakt dat is verbonden met deze AMPLS.

## <a name="configure-log-analytics"></a>Log Analytics configureren

Ga naar Azure Portal. In het menu van de Log Analytics werkruimte resource bevindt zich aan de linkerkant een item met de naam **netwerk isolatie** . Vanuit dit menu kunt u twee verschillende statussen beheren.

![LA-netwerk isolatie](./media/private-link-security/ampls-log-analytics-lan-network-isolation-6.png)

### <a name="connected-azure-monitor-private-link-scopes"></a>Verbonden Azure Monitor scopes voor persoonlijke koppelingen
Alle bereiken die zijn verbonden met de werk ruimte, worden weer gegeven in dit scherm. Als u verbinding maakt met scopes (AMPLSs), kan netwerk verkeer van het virtuele netwerk dat is verbonden met elke AMPLS, worden bereikt in deze werk ruimte. Het maken van een verbinding via hier heeft hetzelfde effect als het instellen ervan op het bereik, zoals we hebben gedaan [met het verbinden van Azure monitor resources](#connect-azure-monitor-resources). Als u een nieuwe verbinding wilt toevoegen, selecteert u **toevoegen** en selecteert u het Azure monitor bereik voor persoonlijke koppelingen. Selecteer **Toep assen** om de verbinding te maken. Een werk ruimte kan verbinding maken met 5 AMPLS-objecten, zoals vermeld in [beperkingen en beperkingen](#restrictions-and-limitations). 

### <a name="access-from-outside-of-private-links-scopes"></a>Toegang tot buiten persoonlijke koppelingen bereiken
De instellingen in het onderste gedeelte van deze pagina bepalen de toegang vanaf open bare netwerken, wat betekent dat netwerken niet zijn verbonden via de hierboven vermelde bereiken. Instelling **toestaan dat open bare netwerk toegang** **niet** kan worden opgenomen in de opname van logboeken van computers buiten de verbonden bereiken. Instelling **toestaan dat open bare netwerk toegang voor query's** is ingesteld op **geen** blokkeert query's die afkomstig zijn van computers buiten de scopes. Dit omvat query's die worden uitgevoerd via werkmappen, Dash boards, client ervaringen op basis van API, inzichten in de Azure Portal, en meer. Ervaringen die worden uitgevoerd buiten de Azure Portal, en die query Log Analytics gegevens moeten ook worden uitgevoerd binnen het persoonlijk gekoppelde VNET.

### <a name="exceptions"></a>Uitzonderingen
Het beperken van de toegang zoals hierboven is uitgelegd, is niet van toepassing op de Azure Resource Manager en heeft daarom de volgende beperkingen:
* Toegang tot gegevens: Hoewel het blok keren/toestaan van query's van open bare netwerken op de meeste Log Analytics-ervaringen van toepassing is, kunnen sommige gegevens query's uitvoeren via Azure Resource Manager en daarom geen query uitvoeren op gegevens tenzij persoonlijke koppelings instellingen worden toegepast op de Resource Manager, ook al is de functie binnenkort beschikbaar. Dit omvat bijvoorbeeld Azure Monitor oplossingen, werkmappen en inzichten, en de LogicApp-connector.
* Werkruimte beheer-werk ruimte-instelling en configuratie wijzigingen (waaronder het inschakelen van deze toegangs instellingen in-of uitschakelen) worden beheerd door Azure Resource Manager. Beperk de toegang tot werk ruimte beheer met de juiste rollen, machtigingen, netwerk besturings elementen en controle. Zie [Azure monitor rollen, machtigingen en beveiliging](../roles-permissions-security.md)voor meer informatie.

> [!NOTE]
> Logboeken en metrische gegevens die zijn geüpload naar een werk ruimte via [Diagnostische instellingen](../essentials/diagnostic-settings.md) , gaan over een beveiligd persoonlijk micro soft-kanaal en worden niet beheerd door deze instellingen.

### <a name="log-analytics-solution-packs-download"></a>Downloaden van Log Analytics oplossingen pakketten

Als u de Log Analytics-agent wilt toestaan om oplossings pakketten te downloaden, voegt u de juiste FQDN-namen toe aan de acceptatie lijst van de firewall. 


| Cloudomgeving | Agentresource | Poorten | Richting |
|:--|:--|:--|:--|
|Openbare Azure-peering     | scadvisorcontent.blob.core.windows.net         | 443 | Uitgaand
|Azure Government | usbn1oicore.blob.core.usgovcloudapi.net | 443 |  Uitgaand
|Azure China 21Vianet      | mceast2oicore.blob.core.chinacloudapi.cn| 443 | Uitgaand

## <a name="configure-application-insights"></a>Application Insights configureren

Ga naar Azure Portal. In uw Azure Monitor Application Insights onderdeel resource is een menu opdracht **netwerk isolatie** aan de linkerkant. Vanuit dit menu kunt u twee verschillende statussen beheren.

![AI-netwerk isolatie](./media/private-link-security/ampls-application-insights-lan-network-isolation-6.png)

Eerst kunt u deze Application Insights-resource verbinden met Azure Monitor persoonlijke koppelings bereik waartoe u toegang hebt. Selecteer **toevoegen** en selecteer het **Azure monitor bereik voor persoonlijke koppelingen**. Selecteer Toep assen om de verbinding te maken. Alle verbonden bereiken worden in dit scherm weer gegeven. Als u deze verbinding maakt, is netwerk verkeer in de verbonden virtuele netwerken mogelijk om dit onderdeel te bereiken. Dit heeft hetzelfde effect als het verbinden van het bereik op basis van de verbinding met [Azure monitor-resources](#connect-azure-monitor-resources). 

Ten tweede kunt u bepalen hoe deze bron bereikbaar kan zijn vanaf buiten de eerder vermelde AMPLS-scopes. Als u het **toestaan van open bare netwerk toegang** hebt ingesteld op **Nee**, kunnen machines of sdk's buiten de verbonden bereiken geen gegevens uploaden naar dit onderdeel. Als u het **toestaan van open bare netwerk toegang voor query's** op **Nee** instelt, hebben computers buiten de scopes geen toegang tot gegevens in deze Application Insights bron. Deze gegevens omvatten de toegang tot APM-logboeken, meet gegevens en de Live Metrics stream, evenals ervaring op het niveau van werk bladen, Dash boards, query's op basis van API-client ervaringen, inzichten in de Azure Portal, en meer. 

> [!NOTE]
> Niet-Portal-ervaringen moeten ook worden uitgevoerd op het persoonlijk gekoppelde VNET dat de bewaakte werk belastingen bevat.

U moet resources toevoegen die de bewaakte werk belastingen hosten voor de privé-koppeling. Hier vindt u [documentatie](../../app-service/networking/private-endpoint.md) over hoe u dit kunt doen voor app Services.

Het beperken van de toegang op deze manier is alleen van toepassing op gegevens in de Application Insights resource. Configuratie wijzigingen, waaronder het inschakelen van deze toegangs instellingen in-of uitschakelen, worden beheerd door Azure Resource Manager. Beperk in plaats daarvan de toegang tot Resource Manager met behulp van de juiste rollen, machtigingen, netwerk besturings elementen en controle. Zie [Azure monitor rollen, machtigingen en beveiliging](../roles-permissions-security.md)voor meer informatie.

> [!NOTE]
> Als u op werk ruimte gebaseerde Application Insights volledig wilt beveiligen, moet u de toegang tot Application Insights resource en de onderliggende Log Analytics-werk ruimte vergren delen.
>
> Voor diagnostische gegevens op code niveau (Profiler/Debugger) moet u uw eigen opslag account opgeven ter ondersteuning van een persoonlijke koppeling. Hier vindt u [documentatie](../app/profiler-bring-your-own-storage.md) over hoe u dit doet.

### <a name="handling-the-all-or-nothing-nature-of-private-links"></a>Het afhandelen van de alle-of-Nothing-aard van privé koppelingen
Zoals beschreven in [het plannen van de instelling van uw persoonlijke koppeling](#planning-your-private-link-setup), het instellen van een persoonlijke koppeling voor een enkele resource is van invloed op alle Azure monitor bronnen in die netwerken, en in andere netwerken die dezelfde DNS delen. Dit kan een onboarding-proces lastig maken. Houd rekening met de volgende opties:

* Alles op de eenvoudigste en veiligste manier is om al uw Application Insights onderdelen toe te voegen aan de AMPLS. Voor onderdelen waarvan u ook nog steeds toegang wilt hebben tot andere netwerken, laat u de vlaggen openbaar Internet toegang voor opname/query toestaan ingesteld op Ja (de standaard instelling).
* Netwerken isoleren: als u (of kunt uitlijnen met) met spoke vnets, volgt u de instructies in [hub-spoke-netwerk topologie in azure](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/hub-spoke). Stel vervolgens afzonderlijke instellingen voor persoonlijke koppelingen in de relevante spoke-VNets in. Zorg ervoor dat u ook DNS-zones scheidt, omdat het delen van DNS-zones met andere spoke-netwerken leidt tot [DNS-onderdrukkingen](#the-issue-of-dns-overrides).
* Aangepaste DNS-zones gebruiken voor specifieke apps: met deze oplossing kunt u Application Insights onderdelen selecteren via een privé-koppeling, waarbij al het verkeer via de open bare routes wordt bewaard.
    - Stel een [aangepaste privé-DNS-zone](https://docs.microsoft.com/azure/private-link/private-endpoint-dns)in en geef deze een unieke naam, bijvoorbeeld Internal.monitor.Azure.com
    - Een AMPLS en een persoonlijk eind punt maken en ervoor kiezen om **niet** automatisch te integreren met privé-DNS
    - Ga naar het persoonlijke eind punt-> DNS-configuratie en controleer de voorgestelde toewijzing van FQDN-namen die er ongeveer als volgt uitzien: ![ scherm afbeelding van voorgestelde DNS-zone configuratie](./media/private-link-security/private-endpoint-fqdns.png)
    - Kies Configuratie toevoegen en selecteer de internal.monitor.azure.com-zone die u zojuist hebt gemaakt
    - Records toevoegen voor de bovenstaande ![ scherm afbeelding van de geconfigureerde DNS-zone](./media/private-link-security/private-endpoint-global-dns-zone.png)
    - Ga naar het onderdeel Application Insights en kopieer de [verbindings reeks](https://docs.microsoft.com/azure/azure-monitor/app/sdk-connection-string).
    - Apps of scripts die dit onderdeel willen aanroepen via een privé-koppeling, moeten de connection string gebruiken met de EndpointSuffix = Internal. monitor. Azure. com
* Wijs eind punten toe via hosts-bestanden in plaats van met DNS, om een persoonlijke koppeling alleen toegang te geven vanaf een specifieke machine/VM in uw netwerk:
    - Stel een AMPLS en een persoonlijk eind punt in en kies ervoor om **niet** automatisch te integreren met privé-DNS 
    - De bovenstaande records configureren op een computer waarop de app wordt uitgevoerd in het hosts-bestand


## <a name="use-apis-and-command-line"></a>Api's en opdracht regel gebruiken

U kunt het eerder beschreven proces automatiseren met Azure Resource Manager sjablonen, REST en opdracht regel interfaces.

Als u privé-koppelings bereik wilt maken en beheren, gebruikt u de [rest API](/rest/api/monitor/private%20link%20scopes%20(preview)) of [Azure cli (AZ monitor private-link-scope)](/cli/azure/monitor/private-link-scope).

Als u toegang tot het netwerk wilt beheren, gebruikt u de vlaggen `[--ingestion-access {Disabled, Enabled}]` en `[--query-access {Disabled, Enabled}]` op [log Analytics werk ruimten](/cli/azure/monitor/log-analytics/workspace) of [Application Insights onderdelen](/cli/azure/ext/application-insights/monitor/app-insights/component).

## <a name="collect-custom-logs-and-iis-log-over-private-link"></a>Aangepaste logboeken en IIS-logboek via persoonlijke koppeling verzamelen

Opslag accounts worden gebruikt in het opname proces van aangepaste Logboeken. Standaard worden door service beheerde opslag accounts gebruikt. Als u echter aangepaste logboeken op persoonlijke koppelingen wilt opnemen, moet u uw eigen opslag accounts gebruiken en deze koppelen aan Log Analytics werk ruimte (n). Meer informatie over het instellen van dergelijke accounts met behulp van de [opdracht regel](/cli/azure/monitor/log-analytics/workspace/linked-storage).

Zie [opslag accounts van klanten die eigendom zijn van een logboek opname](private-storage.md) voor meer informatie over het inbrengen van uw eigen opslag account

## <a name="restrictions-and-limitations"></a>Beperkingen en limieten

### <a name="ampls"></a>AMPLS
Het AMPLS-object heeft een aantal beperkingen waarmee u rekening moet houden bij het plannen van de configuratie van uw particuliere verbinding:

* Een VNet kan alleen verbinding maken met één AMPLS-object. Dit betekent dat het AMPLS-object toegang moet bieden tot alle Azure Monitor bronnen waartoe het VNet toegang moet hebben.
* Een Azure Monitor resource (werk ruimte of Application Insights onderdeel) kan Maxi maal 5 AMPLSs.
* Een AMPLS-object kan Maxi maal 50 Azure Monitor resources worden verbonden.
* Een AMPLS-object kan Maxi maal 10 persoonlijke eind punten verbinden.

Zie [limieten](#consider-limits) voor een diep gaande beoordeling van deze limieten.

### <a name="agents"></a>Agents

De nieuwste versies van de Windows-en Linux-agents moeten worden gebruikt ter ondersteuning van beveiligde opname naar Log Analytics-werk ruimten. Oudere versies kunnen geen bewakings gegevens uploaden via een particulier netwerk.

**Windows-agent voor Log Analytics**

Gebruik de Log Analytics agent versie 10.20.18038.0 of hoger.

**Linux-agent voor Log Analytics**

Gebruik de agent versie 1.12.25 of hoger. Als dat niet het geval is, voert u de volgende opdrachten uit op de virtuele machine.

```cmd
$ sudo /opt/microsoft/omsagent/bin/omsadmin.sh -X
$ sudo /opt/microsoft/omsagent/bin/omsadmin.sh -w <workspace id> -s <workspace key>
```

### <a name="azure-portal"></a>Azure Portal

Als u Azure Monitor Portal-ervaringen wilt gebruiken, zoals Application Insights en Log Analytics, moet u de uitbrei dingen voor Azure Portal en Azure Monitor toegankelijk maken voor de particuliere netwerken. Voeg **AzureActiveDirectory**-, **AzureResourceManager**-, **AzureFrontDoor. FirstParty**-en **AzureFrontDoor.** front-end- [service Tags](../../firewall/service-tags.md) toe aan uw netwerk beveiligings groep.

### <a name="querying-data"></a>Query’s op gegevens uitvoeren
De [ `externaldata` operator](https://docs.microsoft.com/azure/data-explorer/kusto/query/externaldata-operator?pivots=azuremonitor) wordt niet ondersteund via een persoonlijke koppeling, omdat hiermee gegevens uit opslag accounts worden gelezen, maar niet wordt gegarandeerd dat de opslag privé wordt geopend.

### <a name="programmatic-access"></a>Toegang op programmeerniveau

Als u de REST API, [cli](/cli/azure/monitor) of Power shell met Azure monitor op particuliere netwerken wilt gebruiken, voegt u de [service Tags](../../virtual-network/service-tags-overview.md)  **AzureActiveDirectory** en **AzureResourceManager** toe aan uw firewall.

### <a name="application-insights-sdk-downloads-from-a-content-delivery-network"></a>Application Insights SDK-down loads van een Content Delivery Network

Bundel de Java script-code in uw script zodat de browser geen code van een CDN probeert te downloaden. Er wordt een voor beeld gegeven op [github](https://github.com/microsoft/ApplicationInsights-JS#npm-setup-ignore-if-using-snippet-setup)

### <a name="browser-dns-settings"></a>DNS-instellingen van browser

Als u via een persoonlijke koppeling verbinding maakt met uw Azure Monitor-resources, moet het verkeer naar deze bronnen via het persoonlijke eind punt dat in uw netwerk is geconfigureerd worden door lopen. Als u het persoonlijke eind punt wilt inschakelen, werkt u de DNS-instellingen bij, zoals wordt uitgelegd in [verbinding maken met een persoonlijk eind punt](#connect-to-a-private-endpoint). Sommige browsers gebruiken hun eigen DNS-instellingen in plaats van degene die u instelt. De browser kan proberen verbinding te maken met Azure Monitor open bare eind punten en de persoonlijke koppeling volledig over te slaan. Controleer of uw browser instellingen geen oude DNS-instellingen overschrijven of in de cache opslaan. 

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [privé opslag](private-storage.md)