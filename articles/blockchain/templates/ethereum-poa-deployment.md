---
title: Ethereum proof-of-Authority consortium-oplossings sjabloon implementeren in azure
description: Gebruik de Ethereum proof-of-Authority consortium-oplossing voor het implementeren en configureren van een consortium Ethereum Network voor meerdere leden op Azure
ms.date: 03/01/2021
ms.topic: how-to
ms.reviewer: ravastra
ms.custom: contperf-fy21q3
ms.openlocfilehash: 70c9498bae9117585963e111bea4f1e127cab232
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102097938"
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

De RPC-en peering-eind punten zijn standaard toegankelijk via het open bare IP-adres om vereenvoudigde connectiviteit tussen abonnementen en Clouds mogelijk te maken. Voor toegangs beheer op toepassings niveau kunt u [de machtigings contracten van de pariteit](https://openethereum.github.io/Permissioning.html)gebruiken. Netwerken die achter Vpn's zijn geïmplementeerd, maken gebruik van VNet-gateways voor connectiviteit tussen verschillende abonnementen. Omdat VPN-en VNet-implementaties complexer zijn, kunt u beginnen met een openbaar IP-model bij het maken van een prototype van een oplossing.

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
Prestaties van het knoop punt opslag valideren | Het type beheerde schijf voor elk van de geïmplementeerde validatie knooppunten. Zie [prijzen voor opslag](https://azure.microsoft.com/pricing/details/managed-disks/) voor meer informatie over prijzen. | Standard SSD
Grootte van de virtuele machine van het validatie knooppunt | De grootte van de virtuele machine die wordt gebruikt voor validatie knooppunten. Zie [prijzen van virtuele machines](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) voor meer informatie over prijzen. | Standard D2 v3

De virtuele machine en de opslaglaag zijn van invloed op de netwerk prestaties.  Gebruik de volgende tabel om rendabele efficiëntie te kiezen:

SKU van virtuele machine|Opslaglaag|Prijs|Doorvoer|Latentie
---|---|---|---|---
F1|Standard SSD|gebrek|gebrek|hoog
D2_v3|Standard SSD|gemiddeld|gemiddeld|gemiddeld
F16s|Premium SSD|hoog|hoog|gebrek

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

    ![Overzicht van resourcegroep](./media/ethereum-poa-deployment/resource-group-overview.png)

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

## <a name="support-and-feedback"></a>Ondersteuning en feedback<a id="tutorials"></a>

Voor nieuws voer Azure Blockchain gaat u naar de [Azure Blockchain-blog](https://azure.microsoft.com/blog/topics/blockchain/) om op de hoogte te blijven van aanbiedingen van blockchainservices en informatie van het technische team van Azure Blockchain.

Als u feedback over producten wilt geven of nieuwe functies wilt aanvragen, kunt u een idee plaatsen of erop stemmen via het [Azure-feedbackforum voor blockchain](https://aka.ms/blockchainuservoice).

### <a name="community-support"></a>Ondersteuning voor community

In contact komen met Microsoft-technici en experts uit de Azure Blockchain-community.

* [Micro soft Q&een vraag pagina](/answers/topics/azure-blockchain-workbench.html). Technische ondersteuning voor Block Chain-sjablonen is beperkt tot implementatie problemen.
* [Microsoft Tech Community](https://techcommunity.microsoft.com/t5/Blockchain/bd-p/AzureBlockchain)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-blockchain-workbench)

## <a name="next-steps"></a>Volgende stappen

Zie de [Azure Block Chain-documentatie](../index.yml)voor meer Block chain-oplossingen van Azure.
