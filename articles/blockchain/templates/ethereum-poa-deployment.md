---
title: Ethereum proof-of-Authority consortium-oplossings sjabloon implementeren in azure
description: Gebruik de Ethereum proof-of-Authority consortium-oplossing voor het implementeren en configureren van een consortium Ethereum Network voor meerdere leden op Azure
ms.date: 07/23/2020
ms.topic: how-to
ms.reviewer: ravastra
ms.custom: devx-track-js
ms.openlocfilehash: 5bbfca4d890440574ee6717ca910969226fc781a
ms.sourcegitcommit: 9889a3983b88222c30275fd0cfe60807976fd65b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/20/2020
ms.locfileid: "94987062"
---
# <a name="deploy-ethereum-proof-of-authority-consortium-solution-template-on-azure"></a>Ethereum proof-of-Authority consortium-oplossings sjabloon implementeren in azure

U kunt [de Azure-oplossings sjabloon Ethereum proof-of-Authority consortium preview](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-azure-blockchain.azure-blockchain-ethereum) gebruiken voor het implementeren, configureren en beheren van een consortium Ethereum netwerk met meerdere leden met minimale Azure-en Ethereum-kennis.

De oplossings sjabloon kan door elk consortium onderdeel worden gebruikt om een Block chain-netwerk footprint in te richten met behulp van Microsoft Azure compute-, netwerk-en opslag Services. De netwerk footprint van elk consortium bestaat uit een verzameling knoop punten met gelijke taak verdeling die een toepassing of gebruiker kan gebruiken voor het indienen van Ethereum-trans acties.

[!INCLUDE [Preview note](./includes/preview.md)]

## <a name="choose-an-azure-blockchain-solution"></a>Een Azure Block Chain-oplossing kiezen

Voordat u kiest voor het gebruik van de sjabloon Ethereum proof-of-Authority consortium, vergelijkt u uw scenario met de algemene use-cases van de beschik bare Azure Block Chain-opties.

> [!IMPORTANT]
> U kunt overwegen de [Azure Block Chain-Service](../service/overview.md) te gebruiken in plaats van de Ethereum op de Azure-oplossings sjabloon. De Azure Block Chain-service is een ondersteunde beheerde Azure-service. Pariteit Ethereum overgang naar gestuurde ontwikkeling en onderhoud door de community. Zie [overstappen op pariteit Ethereum naar OPENETHEREUM DAO](https://www.parity.io/parity-ethereum-openethereum-dao/)voor meer informatie.

Optie | Service model | Algemeen scenario
-------|---------------|-----------------
Oplossingssjablonen | IaaS | Oplossings sjablonen zijn Azure Resource Manager sjablonen die u kunt gebruiken om een volledig geconfigureerde Block chain-netwerk topologie in te richten. De sjablonen implementeren en configureren Microsoft Azure compute-, netwerk-en opslag Services voor een bepaald Block chain-netwerk type. Er zijn oplossings sjablonen zonder service level agreement. Gebruik de [Microsoft Q&A-vragenpagina](/answers/topics/azure-blockchain-workbench.html) voor ondersteuning.
[Azure Blockchain-service](../service/overview.md) | PaaS | De preview-versie van Azure Block Chain Service vereenvoudigt de vorming, het beheer en de governance van consortium Block Chain Networks. Gebruik Azure Block Chain Service voor oplossingen waarvoor PaaS, consortium beheer of de privacy van contracten en trans acties vereist is.
[Azure Blockchain Workbench](../workbench/overview.md) | IaaS en PaaS | Azure Blockchain Workbench (preview-versie) is een verzameling Azure-services en -functies die zijn ontworpen om u te helpen bij het maken en implementeren van blockchain-toepassingen voor het delen van bedrijfsprocessen en gegevens met andere organisaties. Gebruik Azure Block Chain Workbench voor het prototypen van een Block Chain-oplossing of een Block Chain-toepassings bewijs van een concept. Azure Blockchain Workbench wordt zonder Service Level Agreement geleverd. Gebruik de [Microsoft Q&A-vragenpagina](/answers/topics/azure-blockchain-workbench.html) voor ondersteuning.

## <a name="solution-architecture"></a>Architectuur voor de oplossing

U kunt met behulp van de oplossings sjabloon Ethereum één of meerdere regio's implementeren op basis van een Ethereum-controle netwerk met meerdere locaties.

![implementatie architectuur](./media/ethereum-poa-deployment/deployment-architecture.png)

De implementatie van elk consortium omvat:

* Virtual Machines voor het uitvoeren van de PoA-validatie functies
* Azure Load Balancer voor het distribueren van RPC-, peering-en governance DApp-aanvragen
* Azure Key Vault voor het beveiligen van de validator-identiteiten
* Azure Storage voor het hosten van permanente netwerk gegevens en het coördineren van leasing
* Azure Monitor voor het samen voegen van Logboeken en prestatie statistieken
* VNet-gateway (optioneel) voor het toestaan van VPN-verbindingen tussen privé-VNets

De RPC-en peering-eind punten zijn standaard toegankelijk via het open bare IP-adres om vereenvoudigde connectiviteit tussen abonnementen en Clouds mogelijk te maken. Voor toegangs beheer op toepassings niveau kunt u de machtigings contracten van de pariteit gebruiken. Netwerken die achter Vpn's zijn geïmplementeerd, maken gebruik van VNet-gateways voor connectiviteit tussen verschillende abonnementen. Omdat VPN-en VNet-implementaties complexer zijn, kunt u beginnen met een openbaar IP-model bij het maken van een prototype van een oplossing.

Docker-containers worden gebruikt voor betrouw baarheid en modulariteit. Azure Container Registry wordt gebruikt voor het hosten en leveren van geversiete installatie kopieën als onderdeel van elke implementatie. De container installatie kopieën bestaan uit:

* Orchestrator-Hiermee worden identiteits-en beheer contracten gegenereerd. Slaat identiteiten op in een identiteits opslag.
* Pariteits identiteit van de identiteits opslag. Detecteert en maakt verbinding met peers.
* EthStats-agent: verzamelt lokale logboeken en statistieken via RPC en pusht informatie naar Azure Monitor.
* Governance DApp: webinterface voor interactie met Beheer contracten.

### <a name="validator-nodes"></a>Validatie knooppunten

In het test-of-Authority-protocol nemen validatie knooppunten de plaats van traditionele Miner-knoop punten. Elke validator heeft een unieke Ethereum-identiteit zodat deze kan deel nemen aan het proces voor het maken van een blok kering. Elk consortium kan twee of meer validatie knooppunten voor vijf regio's inrichten voor geo-redundantie. Validatie knooppunten communiceren met andere knoop punten voor validatie om tot consensus te komen in de status van het onderliggende gedistribueerde groot boek. Om te zorgen voor een billijke deelname aan het netwerk, is het niet toegestaan dat elk consortium lid meer validatie functies gebruikt dan het eerste lid van het netwerk. Als het eerste lid bijvoorbeeld drie validatie functies implementeert, mag elk lid slechts Maxi maal drie validatie functies hebben.

### <a name="identity-store"></a>Identiteits opslag

Er wordt een identiteits opslag geïmplementeerd in het abonnement van elk lid waarbij de gegenereerde Ethereum-identiteiten veilig worden bewaard. Voor elke validator genereert de Orchestration-container een persoonlijke sleutel Ethereum en slaat deze op in Azure Key Vault.

## <a name="deploy-ethereum-consortium-network"></a>Ethereum consortium-netwerk implementeren

In dit overzicht gaan we ervan uit dat u een Ethereum consortium van een multi-party maakt. De volgende stroom is een voor beeld van een implementatie met meerdere partijen:

1. Drie leden genereren een Ethereum-account met behulp van het gebruik van een gebruikers sjabloon
1. *Lid A* implements Ethereum PoA, dat hun Ethereum open bare adres biedt
1. *Lid A* levert de consortium-URL naar *lid B* en *lid C*
1. *Lid B* en *lid C* implementeren, Ethereum PoA, voorzien van hun Ethereum open bare adres en de consortium-URL van *een lid*
1. *Lid A een* stemmen van *lid B* als beheerder
1. *Lid A* en *lid B* beide stemmen *lid C* als beheerder

In de volgende secties ziet u hoe u de footprint van het eerste lid in het netwerk kunt configureren.

### <a name="create-resource"></a>Bron maken

Selecteer in de [Azure Portal](https://portal.azure.com) **een resource maken** in de linkerbovenhoek.

Selecteer **Block Chain**  >  **Ethereum proof-of-Authority Consortium (preview)**.

### <a name="basics"></a>Basisbeginselen

Geef onder **basis principes** waarden op voor de standaard parameters voor elke implementatie.

![Basisbeginselen](./media/ethereum-poa-deployment/basic-blade.png)

Parameter | Beschrijving | Voorbeeldwaarde
----------|-------------|--------------
Een nieuw netwerk maken of lid worden van het bestaande netwerk | U kunt een nieuw consortium netwerk maken of lid worden van een bestaand consortium netwerk. Voor het toevoegen van een bestaand netwerk zijn aanvullende para meters vereist. | Nieuwe maken
E-mailadres | U ontvangt een e-mail melding wanneer uw implementatie is voltooid met informatie over uw implementatie. | Een geldig e-mail adres
VM-gebruikers naam | Gebruikers naam van de beheerder van elke geïmplementeerde VM | 1-64 alfanumerieke tekens
Verificatietype | De methode voor verificatie bij de virtuele machine. | Wachtwoord
Wachtwoord | Het wacht woord voor het beheerders account voor elk van de virtuele machines die zijn geïmplementeerd. Alle Vm's hebben in eerste instantie hetzelfde wacht woord. U kunt het wacht woord na het inrichten wijzigen. | 12-72 tekens 
Abonnement | Het abonnement waarop het consortium netwerk moet worden geïmplementeerd |
Resourcegroep| De resource groep waarvoor het consortium netwerk moet worden geïmplementeerd. | myResourceGroup
Locatie | De Azure-regio voor de resource groep. | VS - west 2

Selecteer **OK**.

### <a name="deployment-regions"></a>Implementatie regio's

Geef onder *implementatie regio's* het aantal regio's en locaties voor elk op. U kunt Maxi maal vijf regio's implementeren. De eerste regio moet overeenkomen met de locatie van de resource groep uit de sectie *basis beginselen* . Voor ontwikkel-en test netwerken kunt u één regio per lid gebruiken. Implementeer voor productie over twee of meer regio's voor hoge Beschik baarheid.

![implementatie regio's](./media/ethereum-poa-deployment/deployment-regions.png)

Parameter | Beschrijving | Voorbeeldwaarde
----------|-------------|--------------
Aantal regio's|Aantal regio's voor de implementatie van het consortium netwerk| 2
Eerste regio | Eerste regio voor het implementeren van het consortium netwerk | VS - west 2
Tweede regio | Tweede regio voor het implementeren van het consortium netwerk. Extra regio's zijn zichtbaar wanneer het aantal regio's twee of meer is. | VS - oost 2

Selecteer **OK**.

### <a name="network-size-and-performance"></a>Netwerk grootte en-prestaties

Geef onder *netwerk grootte en prestaties* invoer op voor de grootte van het consortium netwerk. De opslag grootte van het validatie knooppunt bepaalt de mogelijke grootte van de Block chain. De grootte kan na de implementatie worden gewijzigd.

![Netwerk grootte en-prestaties](./media/ethereum-poa-deployment/network-size-and-performance.png)

Parameter | Beschrijving | Voorbeeldwaarde
----------|-------------|--------------
Aantal validator-knoop punten met gelijke taak verdeling | Het aantal validatie knooppunten dat moet worden ingericht als onderdeel van het netwerk. | 2
Prestaties van het knoop punt opslag valideren | Het type beheerde schijf voor elk van de geïmplementeerde validatie knooppunten. Zie [prijzen voor opslag](https://azure.microsoft.com/pricing/details/managed-disks/) voor meer informatie over prijzen. | Standard - SSD
Grootte van de virtuele machine van het validatie knooppunt | De grootte van de virtuele machine die wordt gebruikt voor validatie knooppunten. Zie [prijzen van virtuele machines](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) voor meer informatie over prijzen. | Standard D2 v3

De virtuele machine en de opslaglaag zijn van invloed op de netwerk prestaties.  Gebruik de volgende tabel om rendabele efficiëntie te kiezen:

SKU van virtuele machine|Opslaglaag|Prijs|Doorvoer|Latentie
---|---|---|---|---
F1|Standard - SSD|gebrek|gebrek|hoog
D2_v3|Standard - SSD|gemiddeld|gemiddeld|gemiddeld
F16s|Premium - SSD|hoog|hoog|gebrek

Selecteer **OK**.

### <a name="ethereum-settings"></a>Ethereum-instellingen

Geef onder *Ethereum-instellingen* Ethereum configuratie-instellingen op.

![Ethereum-instellingen](./media/ethereum-poa-deployment/ethereum-settings.png)

Parameter | Beschrijving | Voorbeeldwaarde
----------|-------------|--------------
Lid-ID van consortium | De ID die is gekoppeld aan elk lid dat deel uitmaakt van het consortium netwerk. Het wordt gebruikt om IP-adres ruimten te configureren om conflicten te voor komen. De lid-ID voor een particulier netwerk moet uniek zijn voor verschillende organisaties in hetzelfde netwerk.  Een unieke lid-ID is vereist, zelfs wanneer dezelfde organisatie op meerdere regio's wordt geïmplementeerd. Noteer de waarde van deze para meter, omdat u deze moet delen met andere join-leden om ervoor te zorgen dat er geen conflicten zijn. Het geldige bereik is 0 tot en met 255. | 0
Netwerk-ID | De netwerk-ID voor het consortium Ethereum-netwerk dat wordt geïmplementeerd. Elk Ethereum-netwerk heeft een eigen netwerk-ID, waarbij 1 de ID is van het open bare netwerk. Het geldige bereik is 5 tot en met 999.999.999 | 10101010
Ethereum-adres van de beheerder | Het Ethereum-account adres dat wordt gebruikt voor deelname aan PoA governance. U kunt een Ethereum-adres genereren met behulp van het automask. |
Geavanceerde opties | Geavanceerde opties voor Ethereum-instellingen | Inschakelen
Implementeren met behulp van een openbaar IP-adres | Als privé-VNet is geselecteerd, wordt het netwerk geïmplementeerd achter een VNet-gateway en wordt peering-toegang verwijderd. Voor privé-VNet moeten alle leden een VNet-gateway gebruiken om de verbinding compatibel te maken. | Openbare IP
Limiet voor blok-gas | De limiet voor het blok-gas van het netwerk starten. | 50000000
Verzegelings periode blok keren (SEC) | De frequentie waarmee lege blokken worden gemaakt wanneer er geen trans acties op het netwerk zijn. Een hogere frequentie heeft een snellere eind verhouding, maar verhoogde opslag kosten. | 15
Machtigings contract voor trans acties | Byte code voor het transactie machtigings contract. Hiermee worden de implementatie van het slimme contract en de uitvoering van een lijst met Ethereum-accounts beperkt. |

Selecteer **OK**.

### <a name="monitoring"></a>Bewaking

Met bewaking kunt u een logboek bron configureren voor uw netwerk. De bewakings agent verzamelt en bewaart bruikbare metrische gegevens en logboeken van uw netwerk, zodat u snel de problemen met de netwerk status of de fout kunt controleren.

![Monitor voor Azure](./media/ethereum-poa-deployment/azure-monitor.png)

Parameter | Beschrijving | Voorbeeldwaarde
----------|-------------|--------------
Bewaking | Optie om bewaking in te scha kelen | Inschakelen
Verbinding maken met bestaande Azure Monitor-logboeken | Optie voor het maken van een nieuw exemplaar van Azure Monitor Logboeken of voor het toevoegen van een bestaand exemplaar | Nieuwe maken
Locatie | De regio waar het nieuwe exemplaar is geïmplementeerd | VS - oost
Bestaande log Analytics-werk ruimte-ID (verbinding maken met bestaande Azure Monitor logs = deel nemen aan bestaande bestanden)|Werk ruimte-ID van het bestaande exemplaar van Azure Monitor logboeken||NA
Bestaande primaire sleutel voor logboek analyse (verbinding maken met bestaande Azure Monitor logs = lid worden van bestaande)|De primaire sleutel die wordt gebruikt om verbinding te maken met het bestaande exemplaar van Azure Monitor logboeken||NA

Selecteer **OK**.

### <a name="summary"></a>Samenvatting

Klik op de samen vatting om de opgegeven invoer te controleren en voer een basis validatie vooraf uit voor de implementatie. Voordat u implementeert, kunt u de sjabloon en de para meters downloaden.

Selecteer **maken** om te implementeren.

Als de implementatie VNet-gateways bevat, kan de implementatie van 45 tot 50 minuten duren.

## <a name="deployment-output"></a>Implementatie-uitvoer

Zodra de implementatie is voltooid, kunt u de benodigde para meters openen met behulp van de Azure Portal.

### <a name="confirmation-email"></a>Bevestigings-e-mail

Als u een e-mail adres ([sectie met basis informatie](#basics)) opgeeft, wordt er een e-mail bericht verzonden met daarin de implementatie-informatie en koppelingen naar deze documentatie.

![e-mail over implementatie](./media/ethereum-poa-deployment/deployment-email.png)

### <a name="portal"></a>Portal

Zodra de implementatie is voltooid en alle resources zijn ingericht, kunt u de uitvoer parameters weer geven in de resource groep.

1. Ga naar uw resource groep in de portal.
1. Selecteer **overzicht > implementaties**.

    ![Overzicht van resource groep](./media/ethereum-poa-deployment/resource-group-overview.png)

1. Selecteer de implementatie van **micro soft-Azure-Block chain. Azure-Block Chain-ether-...** .
1. Selecteer de sectie **outputs** .

    ![Implementatie-uitvoer](./media/ethereum-poa-deployment/deployment-outputs.png)

## <a name="growing-the-consortium"></a>Het consortium uitbreiden

Als u uw consortium wilt uitbreiden, moet u eerst verbinding maken met het fysieke netwerk. Als u zich achter een VPN-verbinding bevindt, raadpleegt u de sectie [een VNet-gateway verbinden](#connecting-vnet-gateways) Configureer de netwerk verbinding als onderdeel van de nieuwe leden implementatie. Wanneer de implementatie is voltooid, gebruikt u de [governance DApp](#governance-dapp) om een netwerk beheerder te worden.

### <a name="new-member-deployment"></a>Nieuwe leden implementatie

Deel de volgende informatie met het lid join. De informatie is te vinden in uw e-mail bericht na de implementatie of in de portal-implementatie-uitvoer.

* URL van consortium gegevens
* Het aantal knoop punten dat u hebt geïmplementeerd
* Resource-ID van de VNet-gateway (als u VPN gebruikt)

Het geïmplementeerde lid moet dezelfde Ethereum-sjabloon voor de verificatie van de verificatie van de certificerings instantie gebruiken bij het implementeren van de netwerk aanwezigheid, met behulp van de volgende richt lijnen:

* **Bestaand lid** selecteren
* Kies hetzelfde aantal validator-knoop punten als de rest van de leden op het netwerk om een billijke weer gave te garanderen
* Hetzelfde Ethereum-adres voor de beheerder gebruiken
* De meegeleverde *consortium gegevens-URL* gebruiken in de *Ethereum-instellingen*
* Als de rest van het netwerk zich achter een VPN bevindt, selecteert u **privé-VNet** in het gedeelte Geavanceerd

### <a name="connecting-vnet-gateways"></a>VNet-gateways verbinden

Deze sectie is alleen vereist als u hebt geïmplementeerd met behulp van een privé-VNet. U kunt deze sectie overs Laan als u open bare IP-adressen gebruikt.

Voor een particulier netwerk zijn de verschillende leden verbonden via VNet-gateway verbindingen. Voordat een lid kan deel nemen aan het netwerk en transactie verkeer kan zien, moet een bestaand lid een definitieve configuratie op hun VPN-gateway uitvoeren om de verbinding te accepteren. De Ethereum-knoop punten van het lid join worden pas uitgevoerd als er een verbinding tot stand is gebracht. Als u de kans op een Single Point of Failure wilt beperken, maakt u redundante netwerk verbindingen in het consortium.

Nadat het nieuwe lid is geïmplementeerd, moet het bestaande lid de bidirectionele verbinding volt ooien door een VNet-gateway verbinding met het nieuwe lid in te stellen. Het bestaande lid heeft:

* De VNet-gateway ResourceID van het lid dat verbinding maakt. Zie [implementatie-uitvoer](#deployment-output).
* De gedeelde verbindings sleutel.

Het bestaande lid moet het volgende Power shell-script uitvoeren om de verbinding te volt ooien. U kunt Azure Cloud Shell in de rechter navigatie balk in de portal gebruiken.

![Cloud shell](./media/ethereum-poa-deployment/cloud-shell.png)

```Powershell
$MyGatewayResourceId = "<EXISTING_MEMBER_RESOURCEID>"
$OtherGatewayResourceId = "<NEW_MEMBER_RESOURCEID]"
$ConnectionName = "Leader2Member"
$SharedKey = "<NEW_MEMBER_KEY>"

## $myGatewayResourceId tells me what subscription I am in, what ResourceGroup and the VNetGatewayName
$splitValue = $MyGatewayResourceId.Split('/')
$MySubscriptionid = $splitValue[2]
$MyResourceGroup = $splitValue[4]
$MyGatewayName = $splitValue[8]

## $otherGatewayResourceid tells me what the subscription and VNet GatewayName are
$OtherGatewayName = $OtherGatewayResourceId.Split('/')[8]
$Subscription=Select-AzSubscription -SubscriptionId $MySubscriptionid

## create a PSVirtualNetworkGateway instance for the gateway I want to connect to
$OtherGateway=New-Object Microsoft.Azure.Commands.Network.Models.PSVirtualNetworkGateway
$OtherGateway.Name = $OtherGatewayName
$OtherGateway.Id = $OtherGatewayResourceId
$OtherGateway.GatewayType = "Vpn"
$OtherGateway.VpnType = "RouteBased"

## get a PSVirtualNetworkGateway instance for my gateway
$MyGateway = Get-AzVirtualNetworkGateway -Name $MyGatewayName -ResourceGroupName $MyResourceGroup

## create the connection
New-AzVirtualNetworkGatewayConnection -Name $ConnectionName -ResourceGroupName $MyResourceGroup -VirtualNetworkGateway1 $MyGateway -VirtualNetworkGateway2 $OtherGateway -Location $MyGateway.Location -ConnectionType Vnet2Vnet -SharedKey $SharedKey -EnableBgp $True
```

## <a name="service-monitoring"></a>Servicecontrole

U kunt uw Azure Monitor-Portal vinden door de koppeling te volgen in de e-mail implementatie of door de para meter te vinden in de implementatie-uitvoer [OMS_PORTAL_URL].

In de portal worden eerst netwerk statistieken en knooppunt overzicht op hoog niveau weer gegeven.

![Monitor Categorieën](./media/ethereum-poa-deployment/monitor-categories.png)

**Knooppunt overzicht** selecteren toont de infrastructuur statistieken per knoop punt.

![Knooppunt statistieken](./media/ethereum-poa-deployment/node-stats.png)

Als u **netwerk statistieken** selecteert, worden de Ethereum-netwerk statistieken weer gegeven.

![Netwerk statistieken](./media/ethereum-poa-deployment/network-stats.png)

### <a name="sample-kusto-queries"></a>Voor beeld van Kusto-query's

U kunt een query uitvoeren op de bewakings Logboeken om fouten of waarschuwingen over de drempel waarde voor de installatie te onderzoeken. De volgende query's zijn voor beelden die u kunt uitvoeren in het hulp programma voor *Zoeken in Logboeken* :

Lijst blokken die zijn gerapporteerd door meer dan één validatie query kunnen nuttig zijn om de keten vorken te vinden.

```sql
MinedBlock_CL
| summarize DistinctMiners = dcount(BlockMiner_s) by BlockNumber_d, BlockMiner_s
| where DistinctMiners > 1
```

Het gemiddelde aantal peers ophalen voor een opgegeven validator-knoop punt, gemiddeld over een Bucket van 5 minuten.

```sql
let PeerCountRegex = @"Syncing with peers: (\d+) active, (\d+) confirmed, (\d+)";
ParityLog_CL
| where Computer == "vl-devn3lgdm-reg1000001"
| project RawData, TimeGenerated
| where RawData matches regex PeerCountRegex
| extend ActivePeers = extract(PeerCountRegex, 1, RawData, typeof(int))
| summarize avg(ActivePeers) by bin(TimeGenerated, 5m)
```

## <a name="ssh-access"></a>SSH-toegang

Uit veiligheids overwegingen wordt de SSH-poort toegang standaard geweigerd door een regel voor de beveiliging van een netwerk groep. Voor toegang tot de exemplaren van de virtuele machine in het PoA-netwerk moet u de volgende beveiligings regel wijzigen in *toestaan*.

1. Ga naar de sectie **overzicht** van de geïmplementeerde resource groep in de Azure Portal.

    ![overzicht van SSH](./media/ethereum-poa-deployment/ssh-overview.png)

1. Selecteer de **netwerk beveiligings groep** voor de regio van de virtuele machine die u wilt openen.

    ![SSH-NSG](./media/ethereum-poa-deployment/ssh-nsg.png)

1. Selecteer de regel voor het **toestaan van SSH** .

    ![Scherm opname toont een overzichts venster met de geselecteerde SSH-toestemming.](./media/ethereum-poa-deployment/ssh-allow.png)

1. **Actie** wijzigen in **toestaan**

    ![SSH inschakelen toestaan](./media/ethereum-poa-deployment/ssh-enable-allow.png)

1. Selecteer **Opslaan**. Het kan een paar minuten duren voordat wijzigingen zijn toegepast.

U kunt via SSH met uw opgegeven gebruikers naam en wacht woord/SSH-sleutel op afstand verbinding maken met de virtuele machines voor de validator-knoop punten. De SSH-opdracht voor toegang tot het eerste knoop punt voor validatie wordt weer gegeven in de sjabloon implementatie-uitvoer. Bijvoorbeeld:

``` bash
ssh -p 4000 poaadmin\@leader4vb.eastus.cloudapp.azure.com.
```

Als u extra transactie knooppunten wilt ophalen, verhoogt u het poort nummer met één.

Als u in meer dan één regio hebt geïmplementeerd, wijzigt u de opdracht in de DNS-naam of het IP-adres van de load balancer in die regio. Als u de DNS-naam of het IP-adres van de andere regio's wilt vinden, zoekt u de bron met de naam Conventie **\* \* \* \* \* -lbpip-REG \#** en bekijkt u de eigenschappen van de DNS-naam en IP-adres.

## <a name="azure-traffic-manager-load-balancing"></a>Taak verdeling van Azure Traffic Manager

Azure Traffic Manager kan u helpen downtime te reduceren en de reactie tijd van het PoA-netwerk te verbeteren door inkomend verkeer te routeren over meerdere implementaties in verschillende regio's. Ingebouwde status controles en automatische omleiding helpen hoge Beschik baarheid van de RPC-eind punten en het governance-DApp te garanderen. Deze functie is handig als u hebt geïmplementeerd in meerdere regio's en gereed is voor productie.

Gebruik Traffic Manager om de beschik baarheid van PoA-netwerken te verbeteren met automatische failover. U kunt Traffic Manager ook gebruiken om uw reactie snelheid van uw netwerken te verhogen door eind gebruikers naar de Azure-locatie te routeren met de laagste netwerk latentie.

Als u besluit een Traffic Manager profiel te maken, kunt u de DNS-naam van het profiel gebruiken om toegang te krijgen tot uw netwerk. Zodra andere consortium leden zijn toegevoegd aan het netwerk, kan de Traffic Manager ook worden gebruikt om de taak verdeling over de geïmplementeerde validators te verdelen.

### <a name="creating-a-traffic-manager-profile"></a>Een Traffic Manager profiel maken

1. Selecteer in de [Azure Portal](https://portal.azure.com) **een resource maken** in de linkerbovenhoek.
1. Zoeken naar **Traffic Manager profiel**.

    ![Zoeken naar Azure Traffic Manager](./media/ethereum-poa-deployment/traffic-manager-search.png)

    Geef het profiel een unieke naam en selecteer de resource groep die voor de PoA-implementatie is gebruikt.

1. Selecteer **maken** om te implementeren.

    ![Traffic Manager maken](./media/ethereum-poa-deployment/traffic-manager-create.png)

1. Zodra de implementatie is geïmplementeerd, selecteert u het exemplaar in de resource groep. De DNS-naam voor toegang tot Traffic Manager vindt u op het tabblad Overzicht.

    ![Traffic Manager DNS zoeken](./media/ethereum-poa-deployment/traffic-manager-dns.png)

1. Kies het tabblad **eind punten** en selecteer de knop **toevoegen** .
1. Geef het eind punt een unieke naam.
1. Kies **openbaar IP-adres** bij **doel bron type**.
1. Kies het open bare IP-adres van de load balancer van de eerste regio.

    ![Routering verkeer beheer](./media/ethereum-poa-deployment/traffic-manager-routing.png)

Herhaal deze stap voor elke regio in het geïmplementeerde netwerk. Zodra de eind punten de **ingeschakelde** status hebben, worden ze automatisch geladen en wordt de regio binnen de DNS-naam van Traffic Manager verdeeld. U kunt deze DNS-naam nu gebruiken in plaats van de para meter [CONSORTIUM_DATA_URL] in andere stappen van het artikel.

## <a name="data-api"></a>Gegevens-API

Elk consortium heeft de benodigde informatie voor anderen om verbinding te maken met het netwerk. Om het gemak van de connectiviteit in te scha kelen, host elk lid een aantal verbindings gegevens op het data API-eind punt.

Het bestaande lid biedt [CONSORTIUM_DATA_URL] vóór de implementatie van het lid. Na de implementatie haalt een lid van de JSON-interface op het volgende eind punt informatie op:

`<CONSORTIUM_DATA_URL>/networkinfo`

Het antwoord bevat informatie die nuttig is voor het toevoegen van leden (Genesis-blok, validator set contract ABI, bootnodes) en informatie die nuttig is voor het bestaande lid (validator-adressen). U kunt deze standaardisatie gebruiken om het consortium uit te breiden over cloud providers. Deze API retourneert een JSON-indelings antwoord met de volgende structuur:

```json
{
  "$id": "",
  "type": "object",
  "definitions": {},
  "$schema": "https://json-schema.org/draft-07/schema#",
  "properties": {
    "majorVersion": {
      "$id": "/properties/majorVersion",
      "type": "integer",
      "title": "This schema’s major version",
      "default": 0,
      "examples": [
        0
      ]
    },
    "minorVersion": {
      "$id": "/properties/minorVersion",
      "type": "integer",
      "title": "This schema’s minor version",
      "default": 0,
      "examples": [
        0
      ]
    },
    "bootnodes": {
      "$id": "/properties/bootnodes",
      "type": "array",
      "items": {
        "$id": "/properties/bootnodes/items",
        "type": "string",
        "title": "This member’s bootnodes",
        "default": "",
        "examples": [
          "enode://a348586f0fb0516c19de75bf54ca930a08f1594b7202020810b72c5f8d90635189d72d8b96f306f08761d576836a6bfce112cfb6ae6a3330588260f79a3d0ecb@10.1.17.5:30300",
          "enode://2d8474289af0bb38e3600a7a481734b2ab19d4eaf719f698fe885fb239f5d33faf217a860b170e2763b67c2f18d91c41272de37ac67386f80d1de57a3d58ddf2@10.1.17.4:30300"
        ]
      }
    },
    "valSetContract": {
      "$id": "/properties/valSetContract",
      "type": "string",
      "title": "The ValidatorSet Contract Source",
      "default": "",
      "examples": [
        "pragma solidity 0.4.21;\n\nimport \"./SafeMath.sol\";\nimport \"./Utils.sol\";\n\ncontract ValidatorSet …"
      ]
    },
    "adminContract": {
      "$id": "/properties/adminContract",
      "type": "string",
      "title": "The AdminSet Contract Source",
      "default": "",
      "examples": [
        "pragma solidity 0.4.21;\nimport \"./SafeMath.sol\";\nimport \"./SimpleValidatorSet.sol\";\nimport \"./Admin.sol\";\n\ncontract AdminValidatorSet is SimpleValidatorSet { …"
      ]
    },
    "adminContractABI": {
      "$id": "/properties/adminContractABI",
      "type": "string",
      "title": "The Admin Contract ABI",
      "default": "",
      "examples": [
        "[{\"constant\":false,\"inputs\":[{\"name\":\"proposedAdminAddress\",\"type\":\"address\"},…"
      ]
    },
    "paritySpec": {
      "$id": "/properties/paritySpec",
      "type": "string",
      "title": "The Parity client spec file",
      "default": "",
      "examples": [
        "\n{\n \"name\": \"PoA\",\n \"engine\": {\n \"authorityRound\": {\n \"params\": {\n \"stepDuration\": \"2\",\n \"validators\" : {\n \"safeContract\": \"0x0000000000000000000000000000000000000006\"\n },\n \"gasLimitBoundDivisor\": \"0x400\",\n \"maximumExtraDataSize\": \"0x2A\",\n \"minGasLimit\": \"0x2FAF080\",\n \"networkID\" : \"0x9a2112\"\n }\n }\n },\n \"params\": {\n \"gasLimitBoundDivisor\": \"0x400\",\n \"maximumExtraDataSize\": \"0x2A\",\n \"minGasLimit\": \"0x2FAF080\",\n \"networkID\" : \"0x9a2112\",\n \"wasmActivationTransition\": \"0x0\"\n },\n \"genesis\": {\n \"seal\": {\n \"authorityRound\": {\n \"step\": \"0x0\",\n \"signature\": \"0x0000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000\"\n }\n },\n \"difficulty\": \"0x20000\",\n \"gasLimit\": \"0x2FAF080\"\n },\n \"accounts\": {\n \"0x0000000000000000000000000000000000000001\": { \"balance\": \"1\", \"builtin\": { \"name\": \"ecrecover\", \"pricing\": { \"linear\": { \"base\": 3000, \"word\": 0 } } } },\n \"0x0000000000000000000000000000000000000002\": { \"balance\": \"1\", \"builtin\": { \"name\": \"sha256\", \"pricing\": { \"linear\": { \"base\": 60, \"word\": 12 } } } },\n \"0x0000000000000000000000000000000000000003\": { \"balance\": \"1\", \"builtin\": { \"name\": \"ripemd160\", \"pricing\": { \"linear\": { \"base\": 600, \"word\": 120 } } } },\n \"0x0000000000000000000000000000000000000004\": { \"balance\": \"1\", \"builtin\": { \"name\": \"identity\", \"pricing\": { \"linear\": { \"base\": 15, \"word\": 3 } } } },\n \"0x0000000000000000000000000000000000000006\": { \"balance\": \"0\", \"constructor\" : \"…\" }\n }\n}"
      ]
    },
    "errorMessage": {
      "$id": "/properties/errorMessage",
      "type": "string",
      "title": "Error message",
      "default": "",
      "examples": [
        ""
      ]
    },
    "addressList": {
      "$id": "/properties/addressList",
      "type": "object",
      "properties": {
        "addresses": {
          "$id": "/properties/addressList/properties/addresses",
          "type": "array",
          "items": {
            "$id": "/properties/addressList/properties/addresses/items",
            "type": "string",
            "title": "This member’s validator addresses",
            "default": "",
            "examples": [
              "0x00a3cff0dccc0ecb6ae0461045e0e467cff4805f",
              "0x009ce13a7b2532cbd89b2d28cecd75f7cc8c0727"
            ]
          }
        }
      }
    }
  }
}

```

## <a name="governance-dapp"></a>Governance DApp

De kern van het controle bewijs is gedecentraliseerd bestuur. Omdat de controle van de instantie afhankelijk is van een lijst met netwerk instanties om het netwerk in orde te laten blijven, is het belang rijk om een redelijk mechanisme te bieden voor het wijzigen van de machtigingen lijst. Elke implementatie wordt geleverd met een set slimme contracten en een portal voor beheer op basis van de keten van deze toegestane lijst. Zodra een voorgestelde wijziging een meerderheids stem heeft bereikt door consortium leden, wordt de wijziging aangenomen. Met stemmen kunnen deel nemers aan de nieuwe consensus worden toegevoegd of geknoeid om op een transparante manier te worden verwijderd, waardoor een eerlijke netwerk wordt aangemoedigd.

De governance DApp is een reeks vooraf geïmplementeerde [slimme contracten](https://github.com/Azure-Samples/blockchain/tree/master/ledger/template/ethereum-on-azure/permissioning-contracts) en een webtoepassing die wordt gebruikt om de autoriteiten op het netwerk te beheren. Instanties worden opgedeeld in beheerders identiteiten en validatie knooppunten.
Beheerders hebben de bevoegdheid om consensus te delegeren aan een set validatie knooppunten. Beheerders kunnen ook andere beheerders aan of uit het netwerk stemmen.

![Governance DApp](./media/ethereum-poa-deployment/governance-dapp.png)

* **Gedecentraliseerde governance:** Wijzigingen in de netwerk instanties worden beheerd via de stemming op de keten door beheerders te selecteren.
* Controle van **validatie functies:** Instanties kunnen de validatie knooppunten beheren die in elke PoA-implementatie zijn ingesteld.
* **Controleer bare wijzigings geschiedenis:** Elke wijziging wordt vastgelegd op de Block chain die transparantie en controle biedt.

### <a name="getting-started-with-governance"></a>Aan de slag met governance

Als u trans acties wilt uitvoeren via de governance DApp, moet u een Ethereum Wallet gebruiken. De meest eenvoudige aanpak is het gebruik van een in-browser wallet zoals het deel [masker](https://metamask.io). omdat deze slimme contracten echter worden geïmplementeerd op het netwerk, kunt u ook uw interacties voor het beheer contract automatiseren.

Nadat u het DApp hebt geïnstalleerd, gaat u naar het beheer beleid in de browser.  U vindt de URL via Azure Portal in de implementatie-uitvoer.  Als er geen in-browser wallet is geïnstalleerd, kunt u geen acties uitvoeren. u kunt echter wel de beheerders status weer geven.  

### <a name="becoming-an-admin"></a>Een beheerder worden

Als u het eerste lid bent dat op het netwerk is geïmplementeerd, wordt u automatisch een beheerder en worden uw pariteits knooppunten vermeld als validator-functies. Als u lid wordt van het netwerk, moet u worden afgestemd als beheerder met een meerderheid (groter dan 50%) van de bestaande beheerder. Als u ervoor kiest geen beheerder te worden, worden uw knoop punten nog steeds gesynchroniseerd en valideren we de Block Chain; ze nemen echter niet deel aan het proces voor het maken van een blok kering. Als u het stem proces wilt starten om een beheerder te worden, selecteert u **benoemen** en voert u uw Ethereum-adres en-alias in.

![Benoemen](./media/ethereum-poa-deployment/governance-dapp-nominate.png)

### <a name="candidates"></a>Uitgesloten

Als u het tabblad **kandidaten** selecteert, wordt de huidige set kandidaten beheerders weer gegeven.  Zodra een kandidaat een meerderheids stem heeft bereikt door de huidige beheerders, wordt de kandidaat gepromoveerd naar een beheerder.  Als u wilt stemmen op een kandidaat, selecteert u de rij en selecteert u **stem in**. Als u van gedachten verandert in een stem, selecteert u de kandidaat en selecteert u **Rescind-stem**.

![Uitgesloten](./media/ethereum-poa-deployment/governance-dapp-candidates.png)

### <a name="admins"></a>Beheerders

Op het tabblad **beheerders** ziet u de huidige set beheerders en biedt u de mogelijkheid om te stemmen.  Zodra een beheerder meer dan 50% van de ondersteuning verliest, worden deze verwijderd als beheerder op het netwerk. Alle validator-knoop punten waarvan de beheerder de status van de validator verliest en er worden transactie knooppunten in het netwerk. Een beheerder kan om verschillende redenen worden verwijderd. het is echter wel aan het consortium om op voor hand akkoord te gaan met een beleid.

![Beheerders](./media/ethereum-poa-deployment/governance-dapp-admins.png)

### <a name="validators"></a>Controles

Als u het tabblad **validators** selecteert, worden de huidige geïmplementeerde pariteits knooppunten voor het exemplaar en de huidige status (knooppunt type) weer gegeven. Elk consortium heeft een andere set validatie functies in deze lijst, aangezien deze weer gave het huidige geïmplementeerde consortium-lid vertegenwoordigt. Als het exemplaar nieuw is geïmplementeerd en u uw validatie functies nog niet hebt toegevoegd, kunt u de optie **validatie functies toevoegen**. Door validatie functies toe te voegen, wordt automatisch een gebalanceerde set pariteits knooppunten gekozen en toegewezen aan uw validator-set. Als u meer knoop punten hebt geïmplementeerd dan de toegestane capaciteit, worden de resterende knoop punten transactie knooppunten in het netwerk.

Het adres van elke validator wordt automatisch toegewezen via het [identiteits archief](#identity-store) in Azure.  Als een knoop punt uitvalt, wordt het relinquishes van de identiteit, waardoor een ander knoop punt in uw implementatie zijn locatie kan overnemen. Dit proces zorgt ervoor dat de deelname van consensus Maxi maal beschikbaar is.

![Controles](./media/ethereum-poa-deployment/governance-dapp-validators.png)

### <a name="consortium-name"></a>Consortium-naam

Elke beheerder kan de naam van het consortium bijwerken.  Selecteer het tandwiel pictogram in de linkerbovenhoek om de naam van het consortium bij te werken.

### <a name="account-menu"></a>Account menu

Rechts bevindt zich de alias van uw Ethereum-account en identicon.  Als u een beheerder bent, hebt u de mogelijkheid om uw alias bij te werken.

![Account](./media/ethereum-poa-deployment/governance-dapp-account.png)

## <a name="ethereum-development"></a>Ethereum-ontwikkeling<a id="tutorials"></a>

Voor het compileren, implementeren en testen van slimme contracten, zijn hier enkele opties die u kunt overwegen voor Ethereum-ontwikkeling:
* [Truffle-Suite](https://www.trufflesuite.com/docs/truffle/overview) -Ethereum-ontwikkel omgeving op basis van client
* [Ethereum Remix](https://remix-ide.readthedocs.io/en/latest/index.html ) -ontwikkel omgeving op basis van een browser en lokale Ethereum

### <a name="compile-deploy-and-execute-smart-contract"></a>Slim contract compileren, implementeren en uitvoeren

In het volgende voor beeld maakt u een eenvoudig slim contract. U gebruikt Truffle om het slimme contract te compileren en te implementeren in uw Block chain-netwerk. Na de implementatie roept u een slimme contract functie op via een trans actie.

#### <a name="prerequisites"></a>Vereisten

* [Python 2.7.15](https://www.python.org/downloads/release/python-2715/)installeren. Python is vereist voor Truffle en web3. Selecteer de optie installeren om python op te laten voegen in uw pad.
* Installeer Truffle v 5.0.5 `npm install -g truffle@v5.0.5` . Truffle vereist dat er verschillende hulpprogram ma's worden geïnstalleerd, waaronder [Node.js](https://nodejs.org), [Git](https://git-scm.com/). Zie [Truffle-documentatie](https://github.com/trufflesuite/truffle)voor meer informatie.

### <a name="create-truffle-project"></a>Truffle-project maken

Voordat u een slim contract kunt compileren en implementeren, moet u een Truffle-project maken.

1. Open een opdrachtprompt of shell.
1. Maak een map met de naam `HelloWorld`.
1. Wijzig de map naar de nieuwe `HelloWorld` map.
1. Initialiseer een nieuw Truffle-project met behulp van de opdracht `truffle init` .

    ![Een nieuw Truffle-project maken](./media/ethereum-poa-deployment/create-truffle-project.png)

### <a name="add-a-smart-contract"></a>Een slim contract toevoegen

Maak uw slimme contracten in de submap **contracten** van uw Truffle-project.

1. Maak een bestand in de naam `postBox.sol` in de submap **contracten** van uw Truffle-project.
1. Voeg de volgende code voor de volheid toe aan **postBox. Sol**.

    ```javascript
    pragma solidity ^0.5.0;
    
    contract postBox {
        string message;
        function postMsg(string memory text) public {
            message = text;
        }
        function getMsg() public view returns (string memory) {
            return message;
        }
    }
    ```

### <a name="deploy-smart-contract-using-truffle"></a>Een slim contract implementeren met behulp van truffle

Truffle-projecten bevatten een configuratie bestand voor de netwerk verbindings gegevens van Block chain. Wijzig het configuratie bestand zodat de verbindings gegevens voor uw netwerk zijn.

> [!WARNING]
> Stuur nooit uw persoonlijke Ethereum-sleutel via het netwerk. Zorg ervoor dat elke trans actie eerst lokaal wordt ondertekend en dat de ondertekende trans actie via het netwerk wordt verzonden.

1. U hebt de instructie voor het Ethereum-beheerders account nodig voor het [implementeren van uw Block chain-netwerk](#ethereum-settings). Als u de account hebt gemaakt met het-gebruikgemaakt van het-gemaskeerd masker, kunt u de instructie van het-demask ophalen. Selecteer het pictogram Administrator-account in de rechter bovenhoek van de gegevensmasker extensie en selecteer **instellingen > beveiliging & Privacy > Geef zaad woorden** op.
1. Vervang de inhoud van `truffle-config.js` in uw Truffle-project door de volgende inhoud. Vervang de waarden van de tijdelijke aanduiding voor het eind punt en de instructie.

    ```javascript
    const HDWalletProvider = require("truffle-hdwallet-provider");
    const rpc_endpoint = "<Ethereum RPC endpoint>";
    const mnemonic = "Twelve words you can find in MetaMask > Security & Privacy > Reveal Seed Words";

    module.exports = {
      networks: {
        development: {
          host: "localhost",
          port: 8545,
          network_id: "*" // Match any network id
        },
        poa: {
          provider: new HDWalletProvider(mnemonic, rpc_endpoint),
          network_id: 10101010,
          gasPrice : 0
        }
      }
    };
    ```

1. Omdat we de Truffle HD Wallet-provider gebruiken, installeert u de module in uw project met behulp van de opdracht `npm install truffle-hdwallet-provider --save` .

Truffle maakt gebruik van migratie scripts om slimme contracten te implementeren in een Block chain-netwerk. U hebt een migratie script nodig om uw nieuwe slimme contract te implementeren.

1. Voeg een nieuwe migratie toe om het nieuwe contract te implementeren. Maak `2_deploy_contracts.js` een bestand in de submap **migraties** van het Truffle-project.

    ``` javascript
    var postBox = artifacts.require("postBox");
    
    module.exports = deployer => {
        deployer.deploy(postBox);
    };
    ```

1. Implementeer het PoA-netwerk met behulp van de Truffle migrate-opdracht. Voer vanaf de opdracht prompt in de Truffle-projectmap het volgende uit:

    ```javascript
    truffle migrate --network poa
    ```

### <a name="call-a-smart-contract-function"></a>Een slimme contract functie aanroepen

Nu uw slimme contract is geïmplementeerd, kunt u een trans actie verzenden om een functie aan te roepen.

1. Maak in de projectmap Truffle een nieuw bestand met de naam `sendtransaction.js` .
1. Voeg de volgende inhoud toe aan **sendtransaction.js**.

    ``` javascript
    var postBox = artifacts.require("postBox");
    
    module.exports = function(done) {
      console.log("Getting the deployed version of the postBox smart contract")
      postBox.deployed().then(function(instance) {
        console.log("Calling postMsg function for contract ", instance.address);
        return instance.postMsg("Hello, blockchain!");
      }).then(function(result) {
        console.log("Transaction hash: ", result.tx);
        console.log("Request complete");
        done();
      }).catch(function(e) {
        console.log(e);
        done();
      });
    };
    ```

1. Voer het script uit met behulp van de opdracht Truffle Execute.

    ```javascript
    truffle exec sendtransaction.js --network poa
    ```

    ![Script uitvoeren om functie via trans actie aan te roepen](./media/ethereum-poa-deployment/send-transaction.png)

## <a name="webassembly-wasm-support"></a>Ondersteuning voor webassembly (WASM)

Ondersteuning voor webassembly is al voor u ingeschakeld op nieuwe geïmplementeerde PoA-netwerken. Dit maakt het mogelijk om intelligente contracten te ontwikkelen in elke taal die wordt gepoold naar Web-Assembly (roest, C, C++). Zie [zelf studie over pariteits technische](https://github.com/paritytech/pwasm-tutorial)voor meer informatie.

## <a name="faq"></a>Veelgestelde vragen

### <a name="i-notice-there-are-many-transactions-on-the-network-that-i-didnt-send-where-are-these-coming-from"></a>Ik zie dat er veel trans acties op het netwerk zijn die ik niet heb verzonden. Waar komen ze vandaan?

Het is niet veilig om de [persoonlijke API](https://web3js.readthedocs.io/en/v1.2.0/web3-eth-personal.html)te ontgrendelen. Bots Luis teren naar niet-vergrendelde Ethereum-accounts en proberen de fondsen uit te voeren. De bot gaat ervan uit dat deze accounts echte-ether bevatten en proberen de eerste om het saldo te Siphon. Schakel de persoonlijke API niet in op het netwerk. In plaats daarvan moet u de trans acties vooraf hand matig gebruiken met een wallet zoals het-vergelijkings masker of via een programma.

### <a name="how-to-ssh-onto-a-vm"></a>SSH naar een VM?

De SSH-poort is niet beschikbaar vanwege veiligheids redenen. Volg [deze hand leiding om de SSH-poort in te scha kelen](#ssh-access).

### <a name="how-do-i-set-up-an-audit-member-or-transaction-nodes"></a>Hoe kan ik een audit-lid of transactie knooppunten instellen?

Transactie knooppunten zijn een set pariteits clients die zijn gekoppeld aan het netwerk, maar die niet deel uitmaken van consensus. Deze knoop punten kunnen nog steeds worden gebruikt voor het verzenden van Ethereum-trans acties en het lezen van de status van het slimme contract. Dit mechanisme kan worden gebruikt voor het leveren van controle van niet-bevoegde consortium leden op het netwerk. Volg hiervoor de stappen in [groeiende het consortium](#growing-the-consortium).

### <a name="why-are-metamask-transactions-taking-a-long-time"></a>Waarom nemen trans acties met een geruime tijd in beslag?

Om ervoor te zorgen dat trans acties in de juiste volg orde worden ontvangen, wordt elke Ethereum-trans actie geleverd met een incrementele nonce. Als u een account in een ander netwerk hebt gebruikt in het submasker, moet u de nonce-waarde opnieuw instellen. Klik op het instellingen pictogram (drie staven), instellingen en account opnieuw instellen. De transactie geschiedenis wordt gewist en de trans actie kan nu opnieuw worden ingediend.

### <a name="do-i-need-to-specify-gas-fee-in-metamask"></a>Moet ik de kosten voor het gas opgeven in het hand-outmasker?

Eer is geen doel van het controleren van het controle-of-Authority consortium. Daarom is het niet nodig om de kosten voor gas op te geven bij het indienen van trans acties in een-gemaskeerd masker.

### <a name="what-should-i-do-if-my-deployment-fails-due-to-failure-to-provision-azure-oms"></a>Wat moet ik doen als mijn implementatie mislukt als gevolg van een fout bij het inrichten van Azure OMS?

Bewaking is een optionele functie. In zeldzame gevallen waarbij uw implementatie mislukt omdat het niet mogelijk is om Azure Monitor resource in te richten, kunt u zonder Azure Monitor opnieuw implementeren.

### <a name="are-public-ip-deployments-compatible-with-private-network-deployments"></a>Zijn open bare IP-implementaties die compatibel zijn met implementaties van particuliere netwerken?

Nee. Peering vereist twee richtings communicatie, zodat het hele netwerk openbaar of privé moet zijn.

### <a name="what-is-the-expected-transaction-throughput-of-proof-of-authority"></a>Wat is de verwachte transactie doorvoer van het bewijs van de instantie?

De trans actie-door Voer is zeer afhankelijk van de typen trans acties en de netwerk topologie. Met eenvoudige trans acties hebben we een gemiddeld aantal van 400 trans acties per seconde gebenchmarkd met een netwerk dat is geïmplementeerd in meerdere regio's.

### <a name="how-do-i-subscribe-to-smart-contract-events"></a>Hoe kan ik abonneren op slimme contract gebeurtenissen?

Ethereum-test-of-Authority ondersteunt nu web-sockets.  Controleer de implementatie-uitvoer om de URL en poort van de Web-socket te vinden.

## <a name="support-and-feedback"></a>Ondersteuning en feedback

Voor nieuws voer Azure Blockchain gaat u naar de [Azure Blockchain-blog](https://azure.microsoft.com/blog/topics/blockchain/) om op de hoogte te blijven van aanbiedingen van blockchainservices en informatie van het technische team van Azure Blockchain.

Als u feedback over producten wilt geven of nieuwe functies wilt aanvragen, kunt u een idee plaatsen of erop stemmen via het [Azure-feedbackforum voor blockchain](https://aka.ms/blockchainuservoice).

### <a name="community-support"></a>Ondersteuning voor community

In contact komen met Microsoft-technici en experts uit de Azure Blockchain-community.

* [Micro soft Q&een vraag pagina](/answers/topics/azure-blockchain-workbench.html). Technische ondersteuning voor Block Chain-sjablonen is beperkt tot implementatie problemen.
* [Microsoft Tech Community](https://techcommunity.microsoft.com/t5/Blockchain/bd-p/AzureBlockchain)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-blockchain-workbench)

## <a name="next-steps"></a>Volgende stappen

Zie de [Azure Block Chain-documentatie](../index.yml)voor meer Block chain-oplossingen van Azure.
