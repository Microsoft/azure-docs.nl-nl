---
title: Azure Private Link gebruiken om netwerken veilig te verbinden met Azure Monitor
description: Azure Private Link gebruiken om netwerken veilig te verbinden met Azure Monitor
author: noakup
ms.author: noakuper
ms.topic: conceptual
ms.date: 10/05/2020
ms.subservice: ''
ms.openlocfilehash: a85619b4947808ba1c13df3c1543102eea7273fd
ms.sourcegitcommit: 48cb2b7d4022a85175309cf3573e72c4e67288f5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/08/2020
ms.locfileid: "96853919"
---
# <a name="use-azure-private-link-to-securely-connect-networks-to-azure-monitor"></a>Azure Private Link gebruiken om netwerken veilig te verbinden met Azure Monitor

Met de [persoonlijke Azure-koppeling](../../private-link/private-link-overview.md) kunt u Azure PaaS-services veilig koppelen aan uw virtuele netwerk met behulp van privé-eind punten. Voor veel services hebt u zojuist een eind punt ingesteld per resource. Azure Monitor is echter een Constellation met verschillende onderling verbonden services die samen werken om uw workloads te bewaken. Daarom hebben we een resource met de naam een Azure Monitor AMPLS (private link scope) gemaakt waarmee u de grenzen van uw bewakings netwerk kunt definiëren en verbinding kunt maken met uw virtuele netwerk. In dit artikel wordt beschreven hoe u een Azure Monitor persoonlijk koppelings bereik kunt instellen.

## <a name="advantages"></a>Voordelen

Met een persoonlijke koppeling kunt u het volgende doen:

- Privé verbinding maken met Azure Monitor zonder open bare netwerk toegang te openen
- Controleren of uw bewakings gegevens alleen toegankelijk zijn via geautoriseerde particuliere netwerken
- Gegevens exfiltration uit uw particuliere netwerken voor komen door specifieke Azure Monitor bronnen te definiëren die verbinding maken via uw persoonlijke eind punt
- Uw privé on-premises netwerk veilig verbinden met Azure Monitor met behulp van ExpressRoute en een persoonlijke koppeling
- Bewaar al het verkeer binnen het Microsoft Azure backbone-netwerk

Zie  [belang rijke voor delen van een persoonlijke koppeling](../../private-link/private-link-overview.md#key-benefits)voor meer informatie.

## <a name="how-it-works"></a>Uitleg

Azure Monitor bereik van een persoonlijke koppeling is een groeperings bron voor het verbinden van een of meer privé-eind punten (en dus de virtuele netwerken die ze bevatten) naar een of meer Azure Monitor resources. De resources bevatten Log Analytics-werk ruimten en Application Insights onderdelen.

![Diagram van resource topologie](./media/private-link-security/private-link-topology-1.png)

> [!NOTE]
> Een enkele Azure Monitor resource kan tot meerdere AMPLSs behoren, maar u kunt geen enkele VNet verbinden met meer dan één AMPLS. 

## <a name="planning-based-on-your-network"></a>Planning op basis van uw netwerk

Voordat u uw AMPLS-bronnen instelt, moet u rekening houden met de vereisten voor netwerk isolatie. Evalueer de toegang tot het open bare Internet van uw virtuele netwerken en de toegangs beperkingen van elk van uw Azure Monitor resources (dat wil zeggen, Application Insights onderdelen en Log Analytics werk ruimten).

> [!NOTE]
> Hub-spoke-netwerken of een andere topologie van peered netwerken kunnen een persoonlijke koppeling instellen tussen de hub (hoofd) VNet en de relevante Azure Monitor resources, in plaats van een persoonlijke koppeling in te stellen op elke en elk VNet. Dit is vooral handig als de Azure Monitor bronnen die door deze netwerken worden gebruikt, worden gedeeld. Als u echter wilt toestaan dat elk VNet toegang krijgt tot een afzonderlijke set bewakings resources, maakt u een persoonlijke koppeling naar een toegewezen AMPLS voor elk netwerk.

### <a name="evaluate-which-virtual-networks-should-connect-to-a-private-link"></a>Evalueren welke virtuele netwerken verbinding moeten maken met een privé-koppeling

Begin door te evalueren welke virtuele netwerken (VNets) toegang hebben tot internet. Voor VNets met gratis internet is mogelijk geen persoonlijke koppeling vereist voor toegang tot uw Azure Monitor-resources. De bewakings bronnen waarmee uw VNets verbinding maakt, kunnen inkomend verkeer beperken en vereisen een koppeling van een persoonlijke verbinding (ofwel voor logboek opname of query). In dergelijke gevallen kan zelfs een VNet met toegang tot het open bare Internet verbinding maken met deze bronnen via een persoonlijke koppeling en via een AMPLS.

### <a name="evaluate-which-azure-monitor-resources-should-have-a-private-link"></a>Evalueren welke Azure Monitor resources een persoonlijke koppeling moeten hebben

Bekijk elk van uw Azure Monitor-resources:

- Moet de resource opname van logboeken van resources alleen op specifieke VNets toestaan?
- Moet de resource alleen worden doorzocht op clients die zich op specifieke VNETs bevinden?

Als het antwoord op een van deze vragen ja is, stelt u de beperkingen in zoals wordt uitgelegd in Log Analytics-werk ruimten [configureren](#configure-log-analytics) en [Application Insights onderdelen configureren](#configure-application-insights) en deze resources koppelen aan een of meer AMPLS (s). Virtuele netwerken die toegang moeten hebben tot deze bewakings resources, moeten een persoonlijk eind punt hebben dat verbinding maakt met de relevante AMPLS.
Onthoud: u kunt dezelfde werk ruimten of toepassingen verbinden met meerdere AMPLS, zodat ze door verschillende netwerken kunnen worden bereikt.

### <a name="group-together-monitoring-resources-by-network-accessibility"></a>Bewakings bronnen groeperen op netwerk toegankelijkheid

Aangezien elk VNet verbinding kan maken met slechts één AMPLS-resource, moet u bewakings bronnen groeperen die toegankelijk moeten zijn voor dezelfde netwerken. De eenvoudigste manier om deze groepering te beheren is door één AMPLS per VNet te maken en de bronnen te selecteren die u wilt verbinden met dat netwerk. Als u echter resources wilt reduceren en de beheer baarheid wilt verbeteren, kunt u een AMPLS in meerdere netwerken gebruiken.

Als uw interne virtuele netwerken bijvoorbeeld VNet1 en VNet2 verbinding moeten maken met werk ruimten Workspace1 en Workspace2 en Application Insights onderdeel Application Insights 3, koppelt u alle drie resources aan dezelfde AMPLS. Als VNet3 alleen toegang moet krijgen tot Workspace1, maakt u een andere AMPLS-resource, koppelt u Workspace1 aan het netwerk en verbindt u VNet3, zoals wordt weer gegeven in de volgende diagrammen:

![Diagram van AMPLS een topologie](./media/private-link-security/ampls-topology-a-1.png)

![Diagram van AMPLS B-topologie](./media/private-link-security/ampls-topology-b-1.png)

### <a name="consider-limits"></a>Beperkingen overwegen

Er zijn een aantal beperkingen waarmee u rekening moet houden bij het plannen van de configuratie van uw particuliere verbinding:

* Een VNet kan alleen verbinding maken met één AMPLS-object. Dit betekent dat het AMPLS-object toegang moet bieden tot alle Azure Monitor bronnen waartoe het VNet toegang moet hebben.
* Een Azure Monitor resource (werk ruimte of Application Insights onderdeel) kan Maxi maal 5 AMPLSs.
* Een AMPLS-object kan Maxi maal 50 Azure Monitor resources worden verbonden.
* Een AMPLS-object kan Maxi maal 10 persoonlijke eind punten verbinden.

In de onderstaande topologie:
* Elk VNet is verbonden met slechts **één** AMPLS-object.
* AMPLS B is verbonden met persoonlijke eind punten van twee VNets (VNet2 en VNet3), met 2/10 (20%) van de mogelijke VPN-verbindingen.
* AMPLS A maakt verbinding met twee werk ruimten en een toepassings inzicht onderdeel met 3/50 (6%) van de mogelijke Azure Monitor bronnen verbindingen.
* Workspace2 maakt verbinding met AMPLS A en AMPLS B met 2/5 (40%) van de mogelijke AMPLS-verbindingen.

![Diagram van AMPLS-limieten](./media/private-link-security/ampls-limits.png)

> [!NOTE]
> In sommige netwerktopologieën (voornamelijk hub-spoke) kunt u snel de 10 VNets-limiet voor één AMPLS bereiken. In dergelijke gevallen is het raadzaam om een gedeelde koppeling van een persoonlijke verbinding te gebruiken in plaats van afzonderlijke verbindingen. Maak een enkel persoonlijk eind punt in het hub-netwerk, koppel het aan uw AMPLS en peer de relevante netwerken naar het hub-netwerk.

![Hub en spoke-single-PE](./media/private-link-security/hub-and-spoke-with-single-private-endpoint.png)

## <a name="example-connection"></a>Voorbeeld verbinding

Maak eerst een Azure Monitor-bron voor een persoonlijk koppelings bereik.

1. Ga naar **een resource maken** in de Azure Portal en zoek naar **Azure monitor persoonlijk koppelings bereik**.

   ![Azure Monitor bereik van een persoonlijke koppeling zoeken](./media/private-link-security/ampls-find-1c.png)

2. Klik op **maken**.
3. Kies een abonnement en resource groep.
4. Geef een naam op voor de AMPLS. Het is raadzaam om een naam te gebruiken die duidelijk is wat doel is en de beveiligings grens waarin de scope wordt gebruikt, zodat iemand niet per ongeluk netwerk beveiligings grenzen afbreekt. Bijvoorbeeld ' AppServerProdTelem '.
5. Klik op **Controleren + maken**. 

   ![Azure Monitor bereik voor persoonlijke koppelingen maken](./media/private-link-security/ampls-create-1d.png)

6. Laat de validatie slagen en klik vervolgens op **maken**.

### <a name="connect-azure-monitor-resources"></a>Azure Monitor-resources verbinden

Verbind Azure Monitor resources (Log Analytics werk ruimten en Application Insights onderdelen) met uw AMPLS.

1. Klik in het Azure Monitor bereik voor persoonlijke koppelingen op **Azure monitor resources** in het linkermenu. Klik op de knop **Toevoegen**.
2. Voeg de werk ruimte of het onderdeel toe. Als u op de knop **toevoegen** klikt, wordt er een dialoog venster geopend waarin u Azure monitor resources kunt selecteren. U kunt door uw abonnementen en resource groepen bladeren of u kunt hun naam typen om deze te filteren. Selecteer de werk ruimte of het onderdeel en klik op **Toep assen** om ze toe te voegen aan uw bereik.

    ![Scherm afbeelding van een bereik UX selecteren](./media/private-link-security/ampls-select-2.png)

> [!NOTE]
> Als u Azure Monitor resources wilt verwijderen, moet u deze eerst loskoppelen van alle AMPLS-objecten waarmee ze zijn verbonden. Het is niet mogelijk om resources te verwijderen die zijn verbonden met een AMPLS.

### <a name="connect-to-a-private-endpoint"></a>Verbinding maken met een persoonlijk eind punt

Nu u resources hebt verbonden met uw AMPLS, maakt u een persoonlijk eind punt om verbinding te maken met ons netwerk. U kunt deze taak uitvoeren in het [Azure Portal persoonlijke koppelingen centrum](https://portal.azure.com/#blade/Microsoft_Azure_Network/PrivateLinkCenterBlade/privateendpoints)of binnen het bereik van uw Azure monitor persoonlijke koppeling, zoals in dit voor beeld wordt gedaan.

1. Klik in de bereik bron op **VPN-verbindingen** in het menu aan de linkerkant. Klik op **persoonlijk eind punt** om het proces voor het maken van een eind punt te starten. U kunt ook de verbindingen die in het persoonlijke koppelings centrum zijn gestart, goed keuren door ze te selecteren en op **goed keuren** te klikken.

    ![Scherm opname van de UX-verbindingen van privé-eind punten](./media/private-link-security/ampls-select-private-endpoint-connect-3.png)

2. Kies het abonnement, de resource groep en de naam van het eind punt en de regio waarin het moet worden opgenomen. De regio moet dezelfde regio zijn als het virtuele netwerk waarmee u verbinding maakt.

3. Klik op **volgende: resource**. 

4. In het scherm resource,

   a. Kies het **abonnement** dat uw Azure monitor persoonlijke bereik resource bevat. 

   b. Kies voor **resource type** **micro soft. Insights/privateLinkScopes**. 

   c. Kies in de vervolg keuzelijst **resource** uw persoonlijke-koppelings bereik dat u eerder hebt gemaakt. 

   d. Klik op **volgende: configuratie >**.
      ![Scherm opname van Selecteer persoonlijk eind punt maken](./media/private-link-security/ampls-select-private-endpoint-create-4.png)

5. In het deel venster Configuratie,

   a.    Kies het **virtuele netwerk** en het **subnet** dat u wilt verbinden met uw Azure monitor-resources. 
 
   b.    Kies **Ja** om te **integreren met een privé-DNS-zone** en laat automatisch een nieuwe privé-DNS zone maken. De daad werkelijke DNS-zones kunnen afwijken van wat wordt weer gegeven in de onderstaande scherm afbeelding. 
   > [!NOTE]
   > Als u ervoor kiest **geen** DNS-records hand matig te beheren, moet u eerst uw persoonlijke koppeling instellen, met inbegrip van dit persoonlijke eind punt en de AMPLS-configuratie. Configureer vervolgens uw DNS volgens de instructies in de [DNS-configuratie voor het persoonlijke eind punt van Azure](../../private-link/private-endpoint-dns.md). Zorg ervoor dat u geen lege records maakt als voor bereiding voor het instellen van uw privé-verbinding. De DNS-records die u maakt, kunnen bestaande instellingen overschrijven en de verbinding met Azure Monitor beïnvloeden.
 
   c.    Klik op **Controleren + maken**.
 
   d.    Laat de validatie slagen. 
 
   e.    Klik op **Create**. 

    ![Scherm opname van Selecteer persoonlijke Endpoint2 maken](./media/private-link-security/ampls-select-private-endpoint-create-5.png)

U hebt nu een nieuw persoonlijk eind punt gemaakt dat is verbonden met dit Azure Monitor bereik van een persoonlijke koppeling.

## <a name="configure-log-analytics"></a>Log Analytics configureren

Ga naar Azure Portal. In uw Log Analytics werkruimte resource bevindt zich aan de linkerkant een menu opdracht **netwerk isolatie** . Vanuit dit menu kunt u twee verschillende statussen beheren.

![LA-netwerk isolatie](./media/private-link-security/ampls-log-analytics-lan-network-isolation-6.png)

### <a name="connected-azure-monitor-private-link-scopes"></a>Verbonden Azure Monitor scopes voor persoonlijke koppelingen
Alle bereiken die zijn verbonden met deze werk ruimte, worden weer gegeven in dit scherm. Als u verbinding maakt met scopes (AMPLSs), kan netwerk verkeer van het virtuele netwerk dat is verbonden met elke AMPLS, worden bereikt in deze werk ruimte. Het maken van een verbinding via hier heeft hetzelfde effect als het instellen ervan op het bereik, zoals we hebben gedaan [met het verbinden van Azure monitor resources](#connect-azure-monitor-resources). Als u een nieuwe verbinding wilt toevoegen, klikt u op **toevoegen** en selecteert u het Azure monitor bereik voor persoonlijke koppelingen. Klik op **Toep assen** om de verbinding te maken. Een werk ruimte kan verbinding maken met 5 AMPLS-objecten, zoals wordt beschreven in [limieten](#consider-limits). 

### <a name="access-from-outside-of-private-links-scopes"></a>Toegang tot buiten persoonlijke koppelingen bereiken
De instellingen in het onderste gedeelte van deze pagina bepalen de toegang vanaf open bare netwerken, wat betekent dat netwerken niet zijn verbonden via de hierboven vermelde bereiken. Als u het **toestaan van open bare netwerk toegang** hebt ingesteld op **Nee**, kunnen computers buiten de verbonden bereiken geen gegevens uploaden naar deze werk ruimte. Als u het **toestaan van open bare netwerk toegang voor query's** op **Nee** instelt, hebben computers buiten de scopes geen toegang tot gegevens in deze werk ruimte, wat betekent dat het niet mogelijk is om gegevens in de werk ruimte op te vragen. Dit omvat query's in werkmappen, Dash boards, client ervaringen op basis van API, inzichten in de Azure Portal, en meer. Ervaringen die worden uitgevoerd buiten de Azure Portal, en die query Log Analytics gegevens moeten ook worden uitgevoerd binnen het persoonlijk gekoppelde VNET.

### <a name="exceptions"></a>Uitzonderingen
Het beperken van de toegang zoals hierboven is uitgelegd, is niet van toepassing op de Azure Resource Manager en heeft daarom de volgende beperkingen:
* Toegang tot gegevens: Hoewel het blok keren/toestaan van query's van open bare netwerken op de meeste Log Analytics-ervaringen van toepassing is, kunnen sommige gegevens query's uitvoeren via Azure Resource Manager en daarom geen query uitvoeren op gegevens tenzij persoonlijke koppelings instellingen worden toegepast op de Resource Manager, ook al is de functie binnenkort beschikbaar. Dit omvat bijvoorbeeld Azure Monitor oplossingen, werkmappen en inzichten, en de LogicApp-connector.
* Werkruimte beheer-werk ruimte-instelling en configuratie wijzigingen (waaronder het inschakelen van deze toegangs instellingen in-of uitschakelen) worden beheerd door Azure Resource Manager. Beperk de toegang tot werk ruimte beheer met de juiste rollen, machtigingen, netwerk besturings elementen en controle. Zie [Azure monitor rollen, machtigingen en beveiliging](roles-permissions-security.md)voor meer informatie.

> [!NOTE]
> Logboeken en metrische gegevens die zijn geüpload naar een werk ruimte via [Diagnostische instellingen](diagnostic-settings.md) , gaan over een beveiligd persoonlijk micro soft-kanaal en worden niet beheerd door deze instellingen.

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

Eerst kunt u deze Application Insights-resource verbinden met Azure Monitor persoonlijke koppelings bereik waartoe u toegang hebt. Klik op **toevoegen** en selecteer het **Azure monitor bereik voor persoonlijke koppelingen**. Klik op Toep assen om de verbinding te maken. Alle verbonden bereiken worden in dit scherm weer gegeven. Als u deze verbinding maakt, is netwerk verkeer in de verbonden virtuele netwerken mogelijk om dit onderdeel te bereiken. Het maken van de verbinding heeft hetzelfde effect als wanneer er verbinding wordt gemaakt met het bereik van [Azure monitor resources](#connect-azure-monitor-resources). 

Ten tweede kunt u bepalen hoe deze bron bereikbaar kan worden vanaf buiten de eerder vermelde beveiligingsbereiken. Als u het **toestaan van open bare netwerk toegang** hebt ingesteld op **Nee**, kunnen machines of sdk's buiten de verbonden bereiken geen gegevens uploaden naar dit onderdeel. Als u het **toestaan van open bare netwerk toegang voor query's** op **Nee** instelt, hebben computers buiten de scopes geen toegang tot gegevens in deze Application Insights bron. Deze gegevens omvatten de toegang tot APM-logboeken, meet gegevens en de Live Metrics stream, evenals ervaring op het niveau van werk bladen, Dash boards, query's op basis van API-client ervaringen, inzichten in de Azure Portal, en meer. 

Houd er rekening mee dat de ervaring met niet-Portal verbruik ook moet worden uitgevoerd binnen het persoonlijk gekoppelde VNET dat de bewaakte workloads bevat. 

U moet resources toevoegen die de bewaakte werk belastingen hosten voor de privé-koppeling. Hier vindt u [documentatie](../../app-service/networking/private-endpoint.md) over hoe u dit kunt doen voor app Services.

Het beperken van de toegang op deze manier is alleen van toepassing op gegevens in de Application Insights resource. Configuratie wijzigingen, waaronder het inschakelen van deze toegangs instellingen in-of uitschakelen, worden beheerd door Azure Resource Manager. Beperk in plaats daarvan de toegang tot Resource Manager met behulp van de juiste rollen, machtigingen, netwerk besturings elementen en controle. Zie [Azure monitor rollen, machtigingen en beveiliging](roles-permissions-security.md)voor meer informatie.

> [!NOTE]
> Als u op werk ruimte gebaseerde Application Insights volledig wilt beveiligen, moet u de toegang tot Application Insights resource en de onderliggende Log Analytics-werk ruimte vergren delen.
>
> Voor diagnostische gegevens op code niveau (Profiler/Debugger) moet u uw eigen opslag account opgeven ter ondersteuning van een persoonlijke koppeling. Hier vindt u [documentatie](../app/profiler-bring-your-own-storage.md) over hoe u dit doet.

## <a name="use-apis-and-command-line"></a>Api's en opdracht regel gebruiken

U kunt het eerder beschreven proces automatiseren met Azure Resource Manager sjablonen en opdracht regel interfaces.

Als u privé-koppelings bereik wilt maken en beheren, gebruikt u [AZ monitor private-link-Scope](/cli/azure/monitor/private-link-scope?view=azure-cli-latest). Met deze opdracht kunt u bereiken maken, Log Analytics werk ruimten en Application Insights onderdelen koppelen en privé-eind punten toevoegen/verwijderen/goed keuren.

Als u toegang tot het netwerk wilt beheren, gebruikt u de vlaggen `[--ingestion-access {Disabled, Enabled}]` en `[--query-access {Disabled, Enabled}]` op [log Analytics werk ruimten](/cli/azure/monitor/log-analytics/workspace?view=azure-cli-latest) of [Application Insights onderdelen](/cli/azure/ext/application-insights/monitor/app-insights/component?view=azure-cli-latest).

## <a name="collect-custom-logs-over-private-link"></a>Aangepaste logboeken via een persoonlijke koppeling verzamelen

Opslag accounts worden gebruikt in het opname proces van aangepaste Logboeken. Standaard worden door service beheerde opslag accounts gebruikt. Als u echter aangepaste logboeken op persoonlijke koppelingen wilt opnemen, moet u uw eigen opslag accounts gebruiken en deze koppelen aan Log Analytics werk ruimte (n). Meer informatie over het instellen van dergelijke accounts met behulp van de [opdracht regel](/cli/azure/monitor/log-analytics/workspace/linked-storage?view=azure-cli-latest).

Zie [opslag accounts van klanten die eigendom zijn van een logboek opname](private-storage.md) voor meer informatie over het inbrengen van uw eigen opslag account

## <a name="restrictions-and-limitations"></a>Beperkingen en beperkingen

### <a name="agents"></a>Agents

De nieuwste versies van de Windows-en Linux-agents moeten worden gebruikt op particuliere netwerken om veilige opname van Log Analytics werk ruimten mogelijk te maken. Oudere versies kunnen geen bewakings gegevens uploaden in een particulier netwerk.

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

### <a name="programmatic-access"></a>Toegang op programmeerniveau

Als u de REST API, [cli](/cli/azure/monitor?view=azure-cli-latest) of Power shell met Azure monitor op particuliere netwerken wilt gebruiken, voegt u de [service Tags](../../virtual-network/service-tags-overview.md)  **AzureActiveDirectory** en **AzureResourceManager** toe aan uw firewall.

Door deze tags toe te voegen, kunt u acties uitvoeren zoals het opvragen van gegevens, het maken en beheren van Log Analytics-werk ruimten en AI-onderdelen.

### <a name="application-insights-sdk-downloads-from-a-content-delivery-network"></a>Application Insights SDK-down loads van een Content Delivery Network

Bundel de Java script-code in uw script zodat de browser geen code van een CDN kan downloaden. Er wordt een voor beeld gegeven op [github](https://github.com/microsoft/ApplicationInsights-JS#npm-setup-ignore-if-using-snippet-setup)

### <a name="browser-dns-settings"></a>DNS-instellingen van browser

Als u via een persoonlijke koppeling verbinding maakt met uw Azure Monitor-resources, moet het verkeer naar deze bron worden via het persoonlijke eind punt dat in uw netwerk is geconfigureerd. Als u het persoonlijke eind punt wilt inschakelen, werkt u de DNS-instellingen bij, zoals wordt uitgelegd in [verbinding maken met een persoonlijk eind punt](#connect-to-a-private-endpoint). Sommige browsers gebruiken hun eigen DNS-instellingen in plaats van degene die u instelt. De browser kan proberen verbinding te maken met Azure Monitor open bare eind punten en de persoonlijke koppeling volledig over te slaan. Controleer of uw browser instellingen geen oude DNS-instellingen overschrijven of in de cache opslaan. 

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [privé opslag](private-storage.md)