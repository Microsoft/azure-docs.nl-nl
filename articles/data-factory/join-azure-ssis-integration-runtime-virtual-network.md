---
title: Een Azure-SSIS Integration Runtime samenvoegen met een virtueel netwerk
description: Meer informatie over hoe u een Azure-SSIS-integratie-runtime kunt koppelen aan een virtueel Azure-netwerk.
ms.service: data-factory
ms.topic: conceptual
ms.date: 11/02/2020
author: swinarko
ms.author: sawinark
ms.openlocfilehash: 9a82b305adec1385bf659987ea39df6bb953cd70
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100370968"
---
# <a name="join-an-azure-ssis-integration-runtime-to-a-virtual-network"></a>Een Azure-SSIS Integration Runtime samenvoegen met een virtueel netwerk

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Wanneer u SQL Server Integration Services (SSIS) in Azure Data Factory gebruikt, moet u uw Azure-SSIS Integration runtime (IR) koppelen aan een virtueel Azure-netwerk in de volgende scenario's:

- U wilt verbinding maken met on-premises gegevens archieven uit SSIS-pakketten die worden uitgevoerd op uw Azure-SSIS IR zonder een zelf-hostende IR te hoeven configureren of beheren als proxy. 

- U wilt de SSIS-catalogus database (SSISDB) hosten in Azure SQL Database met IP-firewall regels/virtuele netwerk service-eind punten of in een door SQL beheerd exemplaar met een persoonlijk eind punt. 

- U wilt verbinding maken met Azure-resources die zijn geconfigureerd met virtuele netwerk service-eind punten van SSIS-pakketten die worden uitgevoerd op uw Azure-SSIS IR.

- U wilt verbinding maken met gegevens archieven/-resources die zijn geconfigureerd met IP-firewall regels van SSIS-pakketten die worden uitgevoerd op uw Azure-SSIS IR.

Met Data Factory kunt u uw Azure-SSIS IR toevoegen aan een virtueel netwerk dat is gemaakt via het klassieke implementatie model of het implementatie model van Azure Resource Manager.

> [!IMPORTANT]
> Het klassieke virtuele netwerk wordt afgeschaft. gebruik in plaats daarvan het Azure Resource Manager virtuele netwerk.  Als u het klassieke virtuele netwerk al gebruikt, schakelt u zo snel mogelijk over naar het Azure Resource Manager virtuele netwerk.

[Bij het configureren van een Azure-SQL Server Integration Services (SSIS) Integration runtime (IR) om deel te nemen aan een virtuele netwerk](tutorial-deploy-ssis-virtual-network.md) zelf studie worden de minimale stappen via Azure portal weer gegeven. In dit artikel wordt de zelf studie uitgebreid en worden alle optionele taken beschreven:

- Als u een virtueel netwerk (klassiek) gebruikt.
- Als u uw eigen open bare IP-adressen voor de Azure-SSIS IR meebrengt.
- Als u uw eigen Domain Name System (DNS)-server gebruikt.
- Als u een netwerk beveiligings groep (NSG) gebruikt in het subnet.
- Als u Azure ExpressRoute of een door de gebruiker gedefinieerde route (UDR) gebruikt.
- Als u aangepaste Azure-SSIS IR gebruikt.
- Als u Azure Power shell-inrichting gebruikt.

## <a name="access-to-on-premises-data-stores"></a>Toegang tot on-premises gegevens archieven

Als uw SSIS-pakketten toegang hebben tot on-premises gegevens opslag, kunt u uw Azure-SSIS IR koppelen aan een virtueel netwerk dat is verbonden met het on-premises netwerk. U kunt ook een zelf-hostende IR configureren en beheren als proxy voor uw Azure-SSIS IR. Zie [een zelf-hostende IR configureren als proxy voor een Azure-SSIS IR](./self-hosted-integration-runtime-proxy-ssis.md)voor meer informatie. 

Onthoud de volgende belang rijke punten wanneer u uw Azure-SSIS IR lid maakt van een virtueel netwerk: 

- Als er geen virtueel netwerk is verbonden met uw on-premises netwerk, moet u eerst een [Azure Resource Manager virtueel netwerk](../virtual-network/quick-create-portal.md#create-a-virtual-network) maken om uw Azure-SSIS IR te koppelen. Configureer vervolgens een site-naar-site [-VPN-gateway verbinding](../vpn-gateway/vpn-gateway-howto-site-to-site-classic-portal.md) of een [ExpressRoute](../expressroute/expressroute-howto-linkvnet-classic.md) -verbinding van het virtuele netwerk naar uw on-premises netwerk. 

- Als een Azure Resource Manager virtueel netwerk al is verbonden met uw on-premises netwerk op dezelfde locatie als uw Azure-SSIS IR, kunt u de IR toevoegen aan het virtuele netwerk. 

- Als er al een klassiek virtueel netwerk is verbonden met uw on-premises netwerk op een andere locatie dan uw Azure-SSIS IR, kunt u een [Azure Resource Manager virtueel netwerk](../virtual-network/quick-create-portal.md#create-a-virtual-network) maken om aan uw Azure-SSIS IR deel te nemen. Configureer vervolgens een [klassieke-naar-Azure Resource Manager virtuele netwerk](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md) verbinding. 
 
- Als er al een Azure Resource Manager virtueel netwerk is verbonden met uw on-premises netwerk op een andere locatie dan uw Azure-SSIS IR, kunt u eerst een [Azure Resource Manager virtueel netwerk](../virtual-network/quick-create-portal.md#create-a-virtual-network) maken om uw Azure-SSIS IR te koppelen. Configureer vervolgens een Azure Resource Manager-to-Azure Resource Manager virtuele netwerk verbinding. 

## <a name="hosting-the-ssis-catalog-in-sql-database"></a>De SSIS-catalogus in SQL Database hosten

Als u uw SSIS-catalogus host in een Azure SQL Database met virtuele netwerk service-eind punten, moet u uw Azure-SSIS IR toevoegen aan hetzelfde virtuele netwerk en subnet.

Als u uw SSIS-catalogus in SQL Managed instance met een persoonlijk eind punt host, moet u ervoor zorgen dat u uw Azure-SSIS IR toevoegt aan hetzelfde virtuele netwerk, maar in een ander subnet dan het beheerde exemplaar. Als u uw Azure-SSIS IR wilt toevoegen aan een ander virtueel netwerk dan het exemplaar van SQL Managed, kunt u het beste de peering van het virtuele netwerk (dit is beperkt tot dezelfde regio) of een verbinding tussen een virtueel netwerk en een virtueel netwerk. Zie [verbinding maken tussen uw toepassing en Azure SQL Managed instance](../azure-sql/managed-instance/connect-application-instance.md)(Engelstalig) voor meer informatie.

## <a name="access-to-azure-services"></a>Toegang tot Azure-services

Als uw SSIS pakketten toegang tot Azure-resources die ondersteuning bieden voor [virtuele netwerk service-eind punten](../virtual-network/virtual-network-service-endpoints-overview.md) en u de toegang tot deze resources wilt beveiligen vanaf Azure-SSIS IR, kunt u uw Azure-SSIS IR toevoegen aan een subnet van een virtueel netwerk dat is geconfigureerd voor eind punten van virtuele netwerken en vervolgens een regel voor het virtuele netwerk toevoegen aan de relevante Azure-resources om toegang vanuit hetzelfde subnet toe te staan.

## <a name="access-to-data-sources-protected-by-ip-firewall-rule"></a>Toegang tot gegevens bronnen die worden beveiligd door de IP-firewall regel

Als uw SSIS pakketten toegang biedt tot gegevens archieven/resources die alleen specifieke statische open bare IP-adressen toestaan en u de toegang tot deze resources wilt beveiligen vanaf Azure-SSIS IR, kunt u [open bare IP-adressen](../virtual-network/virtual-network-public-ip-address.md) koppelen aan Azure-SSIS IR terwijl u deze toevoegt aan een virtueel netwerk, en vervolgens een IP-firewall regel toevoegen aan de relevante bronnen om toegang vanaf deze IP-adressen toe te staan. Er zijn twee alternatieve manieren om dit te doen: 

- Wanneer u Azure-SSIS IR maakt, kunt u uw eigen open bare IP-adressen opnemen en deze opgeven via [Data Factory gebruikers interface of SDK](#join-the-azure-ssis-ir-to-a-virtual-network). Alleen de uitgaande Internet verbinding van Azure-SSIS IR gebruikt uw openbaar IP-adres en andere apparaten in het subnet worden niet gebruikt.
- U kunt ook [Virtual Network NAT](../virtual-network/nat-overview.md) instellen voor het subnet dat Azure-SSIS IR wordt toegevoegd en alle uitgaande verbindingen in dit subnet zullen gebruikmaken van uw opgegeven open bare IP-adressen.

In alle gevallen kan het virtuele netwerk alleen worden geïmplementeerd via het Azure Resource Manager-implementatie model.

In de volgende secties vindt u meer informatie. 

## <a name="virtual-network-configuration"></a>Configuratie van virtueel netwerk

Stel uw virtuele netwerk in om aan deze vereisten te voldoen: 

- Zorg ervoor dat u `Microsoft.Batch` een geregistreerde provider hebt onder het abonnement van het subnet van het virtuele netwerk dat als host fungeert voor de Azure-SSIS IR. Als u een klassiek virtueel netwerk gebruikt, voegt u ook `MicrosoftAzureBatch` toe aan de rol van de klassieke virtuele machine voor dat virtuele netwerk. 

- Zorg ervoor dat u over de vereiste machtigingen beschikt. Zie [machtigingen instellen](#perms)voor meer informatie.

- Selecteer het juiste subnet om de Azure-SSIS IR te hosten. Zie [het subnet selecteren](#subnet)voor meer informatie. 

- Als u uw eigen open bare IP-adressen voor de Azure-SSIS IR, raadpleegt u [de statische open bare IP-adressen selecteren](#publicIP)

- Als u uw eigen Domain Name System (DNS)-server in het virtuele netwerk gebruikt, raadpleegt u [de DNS-server instellen](#dns_server). 

- Als u een netwerk beveiligings groep (NSG) in het subnet gebruikt, raadpleegt u [een NSG instellen](#nsg). 

- Als u Azure ExpressRoute of een door de gebruiker gedefinieerde route (UDR) gebruikt, raadpleegt u [Azure ExpressRoute of een UDR gebruiken](#route). 

- Zorg ervoor dat de resource groep van het virtuele netwerk (of de resource groep Public IP-adressen als u uw eigen open bare IP-adressen meebrengt) bepaalde Azure-netwerk resources kan maken en verwijderen. Zie [de resource groep instellen](#resource-group)voor meer informatie. 

- Als u uw Azure-SSIS IR aanpast zoals beschreven in de [aangepaste installatie voor Azure-SSIS IR](./how-to-configure-azure-ssis-ir-custom-setup.md), gebruiken we het interne proces voor het beheren van de knoop punten privé IP-adressen van een vooraf gedefinieerd bereik van 172.16.0.0 tot 172.31.255.255. Zorg er daarom voor dat de privé-IP-adresbereiken van uw virtuele of on-premises netwerken geen conflict veroorzaken met dit bereik.

Dit diagram toont de vereiste verbindingen voor uw Azure-SSIS IR:

![Diagram waarin de vereiste verbindingen voor uw Azure-SSIS IR worden weer gegeven.](media/join-azure-ssis-integration-runtime-virtual-network/azure-ssis-ir.png)

### <a name="set-up-permissions"></a><a name="perms"></a> Machtigingen instellen

De gebruiker die de Azure-SSIS IR maakt, moet de volgende machtigingen hebben:

- Als u uw SSIS-IR koppelt aan een Azure Resource Manager virtueel netwerk, hebt u twee opties:

  - Gebruik de ingebouwde rol netwerk bijdrager. Deze rol wordt geleverd met het _micro soft. \* Network/-_ machtiging, met een veel groter bereik dan nodig is.

  - Maak een aangepaste rol die alleen de benodigde machtigingen voor _micro soft. Network/virtualNetworks/ \* /join/Action_ bevat. Als u ook uw eigen open bare IP-adressen voor Azure-SSIS IR wilt gebruiken terwijl u deze toevoegt aan een Azure Resource Manager virtueel netwerk, moet u ook de machtiging _micro soft. Network/publicIPAddresses/*/join/Action_ opnemen in de rol.

- Als u uw SSIS-IR aan een klassiek virtueel netwerk koppelt, wordt u aangeraden de ingebouwde rol van de klassieke virtuele machine te gebruiken. Anders moet u een aangepaste rol definiëren die de machtiging bevat om lid te worden van het virtuele netwerk.

### <a name="select-the-subnet"></a><a name="subnet"></a> Het subnet selecteren

Bij het kiezen van een subnet: 

- Selecteer de GatewaySubnet voor het implementeren van een Azure-SSIS IR niet. Het is toegewezen voor virtuele netwerk gateways. 

- Zorg ervoor dat het door u geselecteerde subnet voldoende beschik bare adres ruimte heeft om de Azure-SSIS IR te gebruiken. Houd de beschik bare IP-adressen voor ten minste twee keer het IR-knooppunt nummer. Azure reserveert een aantal IP-adressen binnen elk subnet. Deze adressen kunnen niet worden gebruikt. Het eerste en laatste IP-adres van de subnetten zijn gereserveerd voor protocol conformiteit en er worden nog drie adressen voor Azure-Services gebruikt. Zie [zijn er beperkingen voor het gebruik van IP-adressen in deze subnetten?](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets) voor meer informatie. 

- Gebruik geen subnet dat uitsluitend wordt ingen Omen door andere Azure-Services (bijvoorbeeld SQL Database SQL Managed instance, App Service, enzovoort). 

### <a name="select-the-static-public-ip-addresses"></a><a name="publicIP"></a>De statische open bare IP-adressen selecteren

Als u uw eigen statische open bare IP-adressen voor Azure-SSIS IR wilt meenemen tijdens het toevoegen aan een virtueel netwerk, moet u ervoor zorgen dat ze voldoen aan de volgende vereisten:

- Er moeten precies twee ongebruikte taken worden gegeven die nog niet aan andere Azure-resources zijn gekoppeld. De extra versie wordt gebruikt wanneer uw Azure-SSIS IR regel matig wordt bijgewerkt. Houd er rekening mee dat één openbaar IP-adres niet kan worden gedeeld met uw actieve Azure SSIS-IRs.

- Ze moeten beide statisch zijn van het standaard type. Raadpleeg de [sku's van het open bare IP-adres](../virtual-network/public-ip-addresses.md#sku) voor meer informatie.

- Ze moeten beide een DNS-naam hebben. Als u geen DNS-naam hebt ingevoerd bij het maken van deze, kunt u dit doen op Azure Portal.

![Azure-SSIS IR](media/ssis-integration-runtime-management-troubleshoot/setup-publicipdns-name.png)

- Ze en het virtuele netwerk moeten zich onder hetzelfde abonnement en in dezelfde regio bevinden.

### <a name="set-up-the-dns-server"></a><a name="dns_server"></a> De DNS-server instellen 
Als u uw eigen DNS-server moet gebruiken in een virtueel netwerk dat is gekoppeld aan uw Azure-SSIS IR om de naam van uw particuliere host op te lossen, moet u ervoor zorgen dat deze ook globale namen van Azure-hosts kan omzetten (bijvoorbeeld een Azure Storage-blob met de naam `<your storage account>.blob.core.windows.net` ). 

Hieronder vindt u een aanbevolen benadering: 

-   Configureer de aangepaste DNS-server voor het door sturen van aanvragen naar Azure DNS. U kunt niet-omgezette DNS-records door sturen naar het IP-adres van de recursieve resolvers van Azure (168.63.129.16) op uw eigen DNS-server. 

Zie [naam omzetting die gebruikmaakt van uw eigen DNS-server](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server)voor meer informatie. 

> [!NOTE]
> Gebruik een FQDN-naam (Fully Qualified Domain Name) voor de naam van uw particuliere host (gebruik bijvoorbeeld `<your_private_server>.contoso.com` in plaats van `<your_private_server>` ). U kunt ook een standaard aangepaste installatie op uw Azure-SSIS IR gebruiken om automatisch uw eigen DNS-achtervoegsel toe te voegen (bijvoorbeeld `contoso.com` ) aan een niet-gekwalificeerde domein naam met één label en deze in te scha kelen in een FQDN voordat u deze in DNS-query's gebruikt, Zie [standaard aangepaste installatie voorbeelden](./how-to-configure-azure-ssis-ir-custom-setup.md#standard-custom-setup-samples). 

### <a name="set-up-an-nsg"></a><a name="nsg"></a> Een NSG instellen
Als u een NSG wilt implementeren voor het subnet dat door uw Azure-SSIS IR wordt gebruikt, kunt u inkomend en uitgaand verkeer via de volgende poorten toestaan: 

-   **Inkomende vereiste van Azure-SSIS IR**

| Richting | Transport Protocol | Bron | Poortbereik van bron | Doel | Poortbereik van doel | Opmerkingen |
|---|---|---|---|---|---|---|
| Inkomend | TCP | BatchNodeManagement | * | VirtualNetwork | 29876, 29877 (als u de IR koppelt aan een virtueel netwerk van Resource Manager) <br/><br/>10100, 20100, 30100 (als u de IR koppelt aan een klassiek virtueel netwerk)| De Data Factory-service gebruikt deze poorten om te communiceren met de knoop punten van uw Azure-SSIS IR in het virtuele netwerk. <br/><br/> Of u nu een NSG op subnetniveau maakt, Data Factory altijd een NSG configureert op het niveau van de netwerk interface kaarten (Nic's) die zijn gekoppeld aan de virtuele machines die de Azure-SSIS IR hosten. Alleen binnenkomend verkeer van Data Factory IP-adressen op de opgegeven poorten is toegestaan door deze NSG op NIC-niveau. Zelfs als u deze poorten opent op het Internet verkeer op subnetniveau, wordt verkeer van IP-adressen die niet Data Factory IP-adressen worden geblokkeerd op het niveau van de NIC. |
| Inkomend | TCP | CorpNetSaw | * | VirtualNetwork | 3389 | Beschrijving Deze regel is alleen vereist wanneer micro soft-ondersteunings team de klant vraagt om te openen voor geavanceerde probleem oplossing en direct kan worden gesloten na het oplossen van problemen. De **CorpNetSaw** -service code staat alleen beveiligde toegang op werk stations in het bedrijfs netwerk van micro soft toe om extern bureau blad te gebruiken. En deze servicetag kan niet worden geselecteerd vanuit de portal en is alleen beschikbaar via Azure PowerShell of Azure CLI. <br/><br/> Op NIC-niveau NSG is poort 3389 standaard geopend en kunt u poort 3389 op subnetniveau NSG beheren, terwijl Azure-SSIS IR niet is toegestaan poort 3389 uitgaande van de Windows Firewall regel op elk IR-knoop punt voor beveiliging. |
||||||||

-   **Uitgaande vereiste van Azure-SSIS IR**

| Richting | Transport Protocol | Bron | Poortbereik van bron | Doel | Poortbereik van doel | Opmerkingen |
|---|---|---|---|---|---|---|
| Uitgaand | TCP | VirtualNetwork | * | AzureCloud | 443 | De knoop punten van uw Azure-SSIS IR in het virtuele netwerk gebruiken deze poort voor toegang tot Azure-Services, zoals Azure Storage en Azure Event Hubs. |
| Uitgaand | TCP | VirtualNetwork | * | Internet | 80 | Beschrijving De knoop punten van uw Azure-SSIS IR in het virtuele netwerk gebruiken deze poort om een certificaatintrekkingslijst van Internet te downloaden. Als u dit verkeer blokkeert, kan dit leiden tot prestatie downgrade bij het starten van IR en verliest u de mogelijkheid om de intrekkings lijst voor certificaten te controleren op het gebruik van certificaten. Als u de bestemming verder wilt beperken tot bepaalde FQDN-verwijzingen, raadpleegt u de sectie **Azure ExpressRoute of UDR gebruiken**|
| Uitgaand | TCP | VirtualNetwork | * | SQL | 1433, 11000-11999 | Beschrijving Deze regel is alleen vereist wanneer de knoop punten van uw Azure-SSIS IR in het virtuele netwerk toegang hebben tot een SSISDB die wordt gehost door uw server. Als uw server verbindings beleid is ingesteld op **proxy** in plaats van **omleiden**, is alleen poort 1433 vereist. <br/><br/> Deze uitgaande beveiligings regel is niet van toepassing op een SSISDB die wordt gehost door uw SQL Managed instance in het virtuele netwerk of SQL Database geconfigureerd met een persoonlijk eind punt. |
| Uitgaand | TCP | VirtualNetwork | * | VirtualNetwork | 1433, 11000-11999 | Beschrijving Deze regel is alleen vereist wanneer de knoop punten van uw Azure-SSIS IR in het virtuele netwerk toegang hebben tot een SSISDB die wordt gehost door uw SQL Managed instance in het virtuele netwerk of SQL Database geconfigureerd met een persoonlijk eind punt. Als uw server verbindings beleid is ingesteld op **proxy** in plaats van **omleiden**, is alleen poort 1433 vereist. |
| Uitgaand | TCP | VirtualNetwork | * | Storage | 445 | Beschrijving Deze regel is alleen vereist als u SSIS-pakket wilt uitvoeren dat is opgeslagen in Azure Files. |
||||||||

### <a name="use-azure-expressroute-or-udr"></a><a name="route"></a> Azure ExpressRoute of UDR gebruiken
Als u uitgaand verkeer van Azure-SSIS IR wilt controleren, kunt u verkeer dat vanaf Azure-SSIS IR naar een on-premises firewall apparaat wordt geïnitieerd, routeren via [Azure ExpressRoute](https://azure.microsoft.com/services/expressroute/) Force tunneling (adverteren van een BGP-route, 0.0.0.0/0, naar het virtuele netwerk) of naar het virtuele netwerk apparaat (NVA) als firewall of [Azure firewall](../firewall/index.yml) via [udr's](../virtual-network/virtual-networks-udr-overview.md). 

![NVA-scenario voor Azure-SSIS IR](media/join-azure-ssis-integration-runtime-virtual-network/azure-ssis-ir-nva.png)

U moet onder andere dingen doen om het hele scenario te laten werken
   -   Inkomend verkeer tussen Azure Batch beheer Services en de Azure-SSIS IR kan niet worden gerouteerd via een firewall apparaat.
   -   Het firewall apparaat moet uitgaand verkeer toestaan dat door Azure-SSIS IR wordt vereist.

Inkomend verkeer tussen Azure Batch beheer Services en de Azure-SSIS IR kan niet worden gerouteerd naar een firewall apparaat anders wordt het verkeer verbroken vanwege een asymmetrisch routerings probleem. Routes moeten worden gedefinieerd voor binnenkomend verkeer zodat het verkeer op dezelfde manier als het kan worden beantwoord. U kunt specifieke Udr's definiëren voor het routeren van verkeer tussen Azure Batch beheer Services en de Azure-SSIS IR met het type volgende hop als **Internet**.

Als uw Azure-SSIS IR bijvoorbeeld zich bevindt op `UK South` en u het uitgaande verkeer via Azure firewall wilt controleren, krijgt u eerst een lijst met IP-adres bereik van `BatchNodeManagement.UKSouth` de service [Tags uit de download koppeling met het IP-adres bereik](https://www.microsoft.com/download/details.aspx?id=56519) of de [service label detectie-API](../virtual-network/service-tags-overview.md#service-tags-on-premises). Pas vervolgens de volgende Udr's van gerelateerde IP-bereik routes toe met het volgende hop-type als **Internet** , samen met de 0.0.0.0/0-route met het type van de volgende hop als **virtueel apparaat**.

![UDR-instellingen voor Azure Batch](media/join-azure-ssis-integration-runtime-virtual-network/azurebatch-udr-settings.png)

> [!NOTE]
> Deze aanpak is een extra onderhouds kosten. Controleer regel matig het IP-adres bereik en voeg nieuwe IP-adresbereiken toe aan uw UDR om te voor komen dat de Azure-SSIS IR wordt verbroken. Het is raadzaam om het IP-bereik maandelijks te controleren. Wanneer het nieuwe IP-adres wordt weer gegeven in de servicetag van de service, zal het IP-adres van kracht worden. 

Als u de instelling van UDR-regels eenvoudiger wilt maken, kunt u het volgende Power shell-script uitvoeren om UDR-regels toe te voegen voor Azure Batch-beheer Services:
```powershell
$Location = "[location of your Azure-SSIS IR]"
$RouteTableResourceGroupName = "[name of Azure resource group that contains your Route Table]"
$RouteTableResourceName = "[resource name of your Azure Route Table ]"
$RouteTable = Get-AzRouteTable -ResourceGroupName $RouteTableResourceGroupName -Name $RouteTableResourceName
$ServiceTags = Get-AzNetworkServiceTag -Location $Location
$BatchServiceTagName = "BatchNodeManagement." + $Location
$UdrRulePrefixForBatch = $BatchServiceTagName
if ($ServiceTags -ne $null)
{
    $BatchIPRanges = $ServiceTags.Values | Where-Object { $_.Name -ieq $BatchServiceTagName }
    if ($BatchIPRanges -ne $null)
    {
        Write-Host "Start to add rule for your route table..."
        for ($i = 0; $i -lt $BatchIPRanges.Properties.AddressPrefixes.Count; $i++)
        {
            $UdrRuleName = "$($UdrRulePrefixForBatch)_$($i)"
            Add-AzRouteConfig -Name $UdrRuleName `
                -AddressPrefix $BatchIPRanges.Properties.AddressPrefixes[$i] `
                -NextHopType "Internet" `
                -RouteTable $RouteTable `
                | Out-Null
            Write-Host "Add rule $UdrRuleName to your route table..."
        }
        Set-AzRouteTable -RouteTable $RouteTable
    }
}
else
{
    Write-Host "Failed to fetch service tags, please confirm that your Location is valid."
}
```

Als u wilt dat uitgaand verkeer door een firewall apparaat wordt toegestaan, moet u uitgaand naar de onderstaande poorten als vereist toestaan in NSG uitgaande regels.
-   Poort 443 met bestemming als Azure-Cloud Services.

    Als u Azure Firewall gebruikt, kunt u netwerk regel met Cloud-service label opgeven. Voor de firewall van de andere typen kunt u ofwel alleen bestemming toestaan voor poort 443, of onder FQDN-namen toestaan op basis van het type van uw Azure-omgeving:

    | Azure-omgeving | Eindpunten                                                                                                                                                                                                                                                                                                                                                              |
    |-------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | Openbare Azure-peering      | <ul><li><b>Azure Data Factory (beheer)</b><ul><li>\*. frontend.clouddatahub.net</li></ul></li><li><b>Azure Storage (beheer)</b><ul><li>\*.blob.core.windows.net</li><li>\*. table.core.windows.net</li></ul></li><li><b>Azure Container Registry (aangepaste installatie)</b><ul><li>\*. azurecr.io</li></ul></li><li><b>Event hub (logboek registratie)</b><ul><li>\*.servicebus.windows.net</li></ul></li><li><b>Micro soft-logboek registratie service (intern gebruik)</b><ul><li>gcs.prod.monitoring.core.windows.net</li><li>prod.warmpath.msftcloudes.com</li><li>azurewatsonanalysis-prod.core.windows.net</li></ul></li></ul> |
    | Azure Government  | <ul><li><b>Azure Data Factory (beheer)</b><ul><li>\*. frontend.datamovement.azure.us</li></ul></li><li><b>Azure Storage (beheer)</b><ul><li>\*. blob.core.usgovcloudapi.net</li><li>\*. table.core.usgovcloudapi.net</li></ul></li><li><b>Azure Container Registry (aangepaste installatie)</b><ul><li>\*. azurecr.us</li></ul></li><li><b>Event hub (logboek registratie)</b><ul><li>\*. servicebus.usgovcloudapi.net</li></ul></li><li><b>Micro soft-logboek registratie service (intern gebruik)</b><ul><li>fairfax.warmpath.usgovcloudapi.net</li><li>azurewatsonanalysis.usgovcloudapp.net</li></ul></li></ul> |
    | Azure China 21Vianet     | <ul><li><b>Azure Data Factory (beheer)</b><ul><li>\*. frontend.datamovement.azure.cn</li></ul></li><li><b>Azure Storage (beheer)</b><ul><li>\*. blob.core.chinacloudapi.cn</li><li>\*. table.core.chinacloudapi.cn</li></ul></li><li><b>Azure Container Registry (aangepaste installatie)</b><ul><li>\*. azurecr.cn</li></ul></li><li><b>Event hub (logboek registratie)</b><ul><li>\*. servicebus.chinacloudapi.cn</li></ul></li><li><b>Micro soft-logboek registratie service (intern gebruik)</b><ul><li>mooncake.warmpath.chinacloudapi.cn</li><li>azurewatsonanalysis.chinacloudapp.cn</li></ul></li></ul> |

    Net als voor de FQDN-namen van Azure Storage, Azure Container Registry en Event hub kunt u ervoor kiezen om de volgende service-eind punten in te scha kelen voor uw virtuele netwerk, zodat het netwerk verkeer naar deze eind punten via het Azure-backbone netwerk loopt in plaats van naar uw firewall apparaat te worden doorgestuurd:
    -  Microsoft.Storage
    -  Microsoft.ContainerRegistry
    -  Microsoft.EventHub


-   Poort 80 met doel als CRL-download sites.

    U mag onder de volgende FQDN-namen die worden gebruikt als CRL (certificaatintrekkingslijst) de sites van certificaten voor Azure-SSIS IR beheer doel downloaden:
    -  crl.microsoft.com:80
    -  mscrl.microsoft.com:80
    -  crl3.digicert.com:80
    -  crl4.digicert.com:80
    -  ocsp.digicert.com:80
    -  cacerts.digicert.com:80
    
    Als u certificaten met een andere CRL gebruikt, wordt u geadviseerd om deze ook toe te voegen. U kunt dit lezen voor meer [informatie over de certificaatintrekkingslijst.](https://social.technet.microsoft.com/wiki/contents/articles/2303.understanding-access-to-microsoft-certificate-revocation-list.aspx)

    Als u dit verkeer niet toestaat, kunt u prestatie downgrade ervaren wanneer u Azure-SSIS IR start en de mogelijkheid verliezen om de certificaatintrekkingslijst te controleren op basis van het certificaat gebruik dat niet wordt aanbevolen vanuit het beveiligings oogpunt van de weer gave.

-   Poort 1433, 11000-11999 met bestemming als Azure SQL Database (alleen vereist wanneer de knoop punten van uw Azure-SSIS IR in het virtuele netwerk toegang hebben tot een SSISDB die wordt gehost door uw server).

    Als u Azure Firewall gebruikt, kunt u de netwerk regel met de Azure SQL-servicetag opgeven, anders kan de bestemming als specifieke Azure SQL-URL in het firewall apparaat worden toegestaan.

-   Poort 445 met doel als Azure Storage (alleen vereist wanneer u SSIS-pakket uitvoert dat in Azure Files is opgeslagen).

    Als u Azure Firewall gebruikt, kunt u netwerk regel met opslag servicetag opgeven, anders kan de bestemming als specifieke URL voor Azure-bestands opslag in het firewall apparaat worden toegestaan.

> [!NOTE]
> Als u in Azure SQL en Storage Virtual Network Service-eind punten op uw subnet configureert, wordt verkeer tussen Azure-SSIS IR en Azure SQL in dezelfde regio \ Azure Storage in dezelfde regio of in het gekoppelde gebied direct doorgestuurd naar Microsoft Azure backbone-netwerk in plaats van uw firewall apparaat.

Als u geen mogelijkheid hebt om uitgaand verkeer van Azure-SSIS IR te controleren, kunt u gewoon een route Toep assen om al het verkeer naar het volgende hop-type **Internet** te forceren:

-   In een Azure ExpressRoute-scenario kunt u de route 0.0.0.0/0 Toep assen met het type van de volgende hop als **Internet** op het subnet dat als host fungeert voor de Azure-SSIS IR. 
-   In een NVA-scenario kunt u de bestaande, 0.0.0.0/0-route die is toegepast op het subnet waarop de Azure-SSIS IR als host van het **virtuele apparaat** wordt gehost op **Internet**, wijzigen.

![Een route toevoegen](media/join-azure-ssis-integration-runtime-virtual-network/add-route-for-vnet.png)

> [!NOTE]
> Route opgeven met het type van de volgende hop **Internet** betekent niet dat al het verkeer via internet verloopt. Zolang het doel adres is voor een van de services van Azure, stuurt Azure het verkeer rechtstreeks naar de service via het backbone-netwerk van Azure, in plaats van het verkeer naar Internet te routeren.

### <a name="set-up-the-resource-group"></a><a name="resource-group"></a> De resource groep instellen

Het Azure-SSIS IR moet bepaalde netwerk bronnen maken onder dezelfde resource groep als het virtuele netwerk. Deze resources omvatten:
- Een Azure-load balancer met de naam *\<Guid> -azurebatch-cloudserviceloadbalancer*.
- Een openbaar IP-adres van Azure, met de naam *\<Guid> -azurebatch-cloudservicepublicip*.
- Een netwerk beveiligings groep, met de naam *\<Guid> -azurebatch-cloudservicenetworksecuritygroup*. 

> [!NOTE]
> U kunt nu uw eigen statische open bare IP-adressen voor Azure-SSIS IR nemen. In dit scenario worden alleen de Azure load balancer-en netwerk beveiligings groep onder dezelfde resource groep gemaakt als uw statische open bare IP-adressen in plaats van het virtuele netwerk.

Deze resources worden gemaakt wanneer uw Azure-SSIS IR wordt gestart. Ze worden verwijderd wanneer uw Azure-SSIS IR wordt gestopt. Als u uw eigen statische open bare IP-adressen voor Azure-SSIS IR meebrengt, worden uw eigen statische open bare IP-adressen niet verwijderd wanneer de Azure-SSIS IR wordt gestopt. Als u wilt voor komen dat uw Azure-SSIS IR worden gestopt, moet u deze netwerk bronnen niet opnieuw gebruiken in uw andere resources.

Zorg ervoor dat er geen resource vergrendeling is voor de resource groep/het abonnement waartoe het virtuele netwerk/uw statische open bare IP-adressen behoren. Als u een alleen-lezen/Verwijder vergrendeling configureert, mislukt het starten en stoppen van de Azure-SSIS IR, of reageert het niet meer.

Zorg ervoor dat u geen Azure Policy toewijzing hebt waarmee wordt voor komen dat de volgende resources worden gemaakt onder de resource groep/het abonnement waartoe het virtuele netwerk/uw statische open bare IP-adressen behoren: 
- Micro soft. Network/LoadBalancers 
- Micro soft. Network/NetworkSecurityGroups 
- Micro soft. Network/PublicIPAddresses 

Zorg ervoor dat het resource quotum van uw abonnement voldoende is voor de bovenstaande drie netwerk bronnen. In het bijzonder moet u voor elke Azure-SSIS IR die in het virtuele netwerk is gemaakt, twee gratis quota's reserveren voor elk van de bovenstaande drie netwerk bronnen. Het extra quotum wordt gebruikt wanneer uw Azure-SSIS IR regel matig wordt bijgewerkt.

### <a name="faq"></a><a name="faq"></a> FAQ

- Hoe kan ik het open bare IP-adres dat op mijn Azure-SSIS IR wordt weer gegeven, beveiligen voor een binnenkomende verbinding? Is het mogelijk om het open bare IP-adres te verwijderen?
 
  Nu wordt er automatisch een openbaar IP-adres gemaakt wanneer uw Azure-SSIS IR lid wordt van een virtueel netwerk. We hebben een NSG op NIC-niveau waarmee alleen Azure Batch-beheer Services als inkomend-verbinding kunnen maken met uw Azure-SSIS IR. U kunt ook een NSG op subnetniveau opgeven voor inkomende beveiliging.

  Als u niet wilt dat een openbaar IP-adres wordt weer gegeven, kunt u [een zelf-hostende IR configureren als proxy voor uw Azure-SSIS IR](./self-hosted-integration-runtime-proxy-ssis.md) in plaats van uw Azure-SSIS IR aan een virtueel netwerk te koppelen, als dit van toepassing is op uw scenario.
 
- Kan ik het open bare IP-adres van mijn Azure-SSIS IR toevoegen aan de acceptatie lijst van de firewall voor mijn gegevens bronnen?

  U kunt nu uw eigen statische open bare IP-adressen voor Azure-SSIS IR nemen. In dit geval kunt u uw IP-adressen toevoegen aan de acceptatie lijst van de firewall voor uw gegevens bronnen. U kunt ook andere opties hieronder gebruiken om de toegang tot gegevens vanuit uw Azure-SSIS IR te beveiligen, afhankelijk van uw scenario:

  - Als uw gegevens bron zich op locatie bevindt, nadat u een virtueel netwerk hebt verbonden met uw on-premises netwerk en uw Azure-SSIS IR aan het subnet van het virtuele netwerk hebt toegevoegd, kunt u het privé IP-adres bereik van dat subnet toevoegen aan de acceptatie lijst van de firewall voor uw gegevens bron.
  - Als uw gegevens bron een Azure-service is die ondersteuning biedt voor service-eind punten van een virtueel netwerk, kunt u een service-eind punt voor een virtueel netwerk configureren in het subnet van het virtuele netwerk en uw Azure-SSIS IR toevoegen aan dat subnet. U kunt vervolgens een regel voor het virtuele netwerk met dat subnet toevoegen aan de firewall voor uw gegevens bron.
  - Als uw gegevens bron een niet-Azure-Cloud service is, kunt u een UDR gebruiken om uitgaand verkeer van uw Azure-SSIS IR door te sturen naar een NVA/Azure Firewall via een statisch openbaar IP-adres. U kunt vervolgens het statische open bare IP-adres van uw NVA/Azure Firewall toevoegen aan de acceptatie lijst van de firewall voor uw gegevens bron.
  - Als geen van de bovenstaande opties aan uw behoeften voldoet, kunt u overwegen om [een zelf-hostende IR te configureren als proxy voor uw Azure-SSIS IR](./self-hosted-integration-runtime-proxy-ssis.md). Vervolgens kunt u het statische open bare IP-adres van de computer die als host fungeert voor uw zelf-hostende IR toevoegen aan de acceptatie lijst van de firewall voor uw gegevens bron.

- Waarom moet ik twee statische open bare adressen opgeven als ik mijn eigen persoonlijke adres wil opnemen voor Azure-SSIS IR?

  Azure-SSIS IR wordt regel matig automatisch bijgewerkt. Nieuwe knoop punten worden tijdens de upgrade gemaakt en oude worden verwijderd. Om uitval tijd te voor komen, worden de oude knoop punten echter pas verwijderd als de nieuwe knooppunten gereed zijn. Uw eerste statische open bare IP-adres dat wordt gebruikt door de oude knoop punten kan dus niet onmiddellijk worden vrijgegeven en we hebben uw tweede statische open bare IP-adres nodig om de nieuwe knoop punten te maken.

- Ik heb mijn eigen statische open bare IP-adressen voor Azure-SSIS IR en waarom heb ik nog steeds geen toegang tot mijn gegevens bronnen?

  - Controleer of de twee statische open bare IP-adressen beide zijn toegevoegd aan de acceptatie lijst van de firewall voor uw gegevens bronnen. Telkens wanneer uw Azure-SSIS IR wordt geüpgraded, wordt het statische open bare IP-adres tussen de twee door u overgezet. Als u slechts een van beide toevoegt aan de acceptatie lijst, wordt de toegang tot gegevens voor uw Azure-SSIS IR verbroken na de upgrade.
  - Als uw gegevens bron een Azure-service is, controleert u of u deze hebt geconfigureerd met Service-eind punten voor virtuele netwerken. Als dat het geval is, zal het verkeer van Azure-SSIS IR naar uw gegevens bron overschakelen op het gebruik van de privé-IP-adressen die worden beheerd door Azure-Services, en het toevoegen van uw eigen statische open bare IP-adressen aan de acceptatie lijst van de firewall voor uw gegevens bron wordt niet van kracht.

## <a name="azure-portal-data-factory-ui"></a>Azure Portal (Data Factory gebruikers interface)

In deze sectie wordt beschreven hoe u een bestaande Azure-SSIS IR kunt toevoegen aan een virtueel netwerk (klassiek of Azure Resource Manager) met behulp van de Azure Portal en Data Factory gebruikers interface. 

Voordat u uw Azure-SSIS IR aan het virtuele netwerk toevoegt, moet u het virtuele netwerk correct configureren. Volg de stappen in de sectie die van toepassing is op uw type virtueel netwerk (klassiek of Azure Resource Manager). Volg vervolgens de stappen in het derde gedeelte om uw Azure-SSIS IR toe te voegen aan het virtuele netwerk. 

### <a name="configure-an-azure-resource-manager-virtual-network"></a>Een Azure Resource Manager virtueel netwerk configureren

Gebruik de portal om een Azure Resource Manager virtueel netwerk te configureren voordat u probeert een Azure-SSIS IR te koppelen.

1. Start micro soft Edge of Google Chrome. Momenteel ondersteunen alleen deze webbrowsers de Data Factory-gebruikers interface. 

1. Meld u aan bij [Azure Portal](https://portal.azure.com). 

1. Selecteer **meer services**. Filter voor en selecteer **virtuele netwerken**. 

1. Filter en selecteer uw virtuele netwerk in de lijst. 

1. Selecteer op de pagina **virtueel netwerk** **Eigenschappen**. 

1. Selecteer de knop kopiëren voor de **resource-id** om de resource-id voor het virtuele netwerk naar het klem bord te kopiëren. Sla de ID op in het klem bord in OneNote of in een bestand. 

1. Selecteer **subnetten** in het menu links. Zorg ervoor dat het aantal beschik bare adressen groter is dan de knoop punten in uw Azure-SSIS IR. 

1. Controleer of de Azure Batch provider is geregistreerd in het Azure-abonnement met het virtuele netwerk. Of Registreer de Azure Batch-provider. Als u al een Azure Batch account in uw abonnement hebt, wordt uw abonnement geregistreerd voor Azure Batch. (Als u de Azure-SSIS IR maakt in de Data Factory Portal, wordt de Azure Batch provider automatisch voor u geregistreerd.) 

   1. Selecteer in het Azure Portal in het linkermenu **abonnementen**. 

   1. Selecteer uw abonnement. 

   1. Selecteer aan de linkerkant **resource providers** en controleer of **Microsoft.BatCH** een geregistreerde provider is. 

   ![Bevestiging van de status ' geregistreerd '](media/join-azure-ssis-integration-runtime-virtual-network/batch-registered-confirmation.png)

   Als **Microsoft.BatCH** niet in de lijst wordt weer geven, [maakt u een leeg Azure batch-account](../batch/batch-account-create-portal.md) in uw abonnement om het te registreren. U kunt deze later verwijderen. 

### <a name="configure-a-classic-virtual-network"></a>Een klassiek virtueel netwerk configureren

Gebruik de portal om een klassiek virtueel netwerk te configureren voordat u probeert een Azure-SSIS IR toe te voegen. 

1. Start micro soft Edge of Google Chrome. Momenteel ondersteunen alleen deze webbrowsers de Data Factory-gebruikers interface. 

1. Meld u aan bij [Azure Portal](https://portal.azure.com). 

1. Selecteer **meer services**. Filter voor en selecteer **virtuele netwerken (klassiek)**. 

1. Filter en selecteer uw virtuele netwerk in de lijst. 

1. Selecteer op de pagina **virtueel netwerk (klassiek)** de optie **Eigenschappen**. 

   ![Resource-ID van klassiek virtueel netwerk](media/join-azure-ssis-integration-runtime-virtual-network/classic-vnet-resource-id.png)

1. Selecteer de knop kopiëren voor **resource-id** om de resource-id voor het klassieke netwerk naar het klem bord te kopiëren. Sla de ID op in het klem bord in OneNote of in een bestand. 

1. Selecteer **subnetten** in het menu links. Zorg ervoor dat het aantal beschik bare adressen groter is dan de knoop punten in uw Azure-SSIS IR. 

   ![Aantal beschik bare adressen in het virtuele netwerk](media/join-azure-ssis-integration-runtime-virtual-network/number-of-available-addresses.png)

1. Voeg **MicrosoftAzureBatch** toe aan de rol **Inzender voor klassieke virtuele machines** voor het virtuele netwerk. 

   1. Selecteer **toegangs beheer (IAM)** in het menu links en selecteer het tabblad **roltoewijzingen** . 

       ![De knoppen toegangs beheer en toevoegen](media/join-azure-ssis-integration-runtime-virtual-network/access-control-add.png)

   1. Selecteer **Roltoewijzing toevoegen**.

   1. Selecteer op de pagina **roltoewijzing toevoegen** , voor **rol**, de **Inzender voor klassieke virtuele machines**. Plak **ddbf3205-c6bd-46ae-8127-60eb93363864** in het **selectie** vakje en selecteer vervolgens **Microsoft Azure batch** in de lijst met zoek resultaten. 

       ![Zoek resultaten op de pagina functie toewijzing toevoegen](media/join-azure-ssis-integration-runtime-virtual-network/azure-batch-to-vm-contributor.png)

   1. Selecteer **Opslaan** om de instellingen op te slaan en de pagina te sluiten. 

       ![Toegangs instellingen opslaan](media/join-azure-ssis-integration-runtime-virtual-network/save-access-settings.png)

   1. Controleer of **Microsoft Azure batch** wordt weer geven in de lijst met inzenders. 

       ![Azure Batch toegang bevestigen](media/join-azure-ssis-integration-runtime-virtual-network/azure-batch-in-list.png)

1. Controleer of de Azure Batch provider is geregistreerd in het Azure-abonnement met het virtuele netwerk. Of Registreer de Azure Batch-provider. Als u al een Azure Batch account in uw abonnement hebt, wordt uw abonnement geregistreerd voor Azure Batch. (Als u de Azure-SSIS IR maakt in de Data Factory Portal, wordt de Azure Batch provider automatisch voor u geregistreerd.) 

   1. Selecteer in het Azure Portal in het linkermenu **abonnementen**. 

   1. Selecteer uw abonnement. 

   1. Selecteer aan de linkerkant **resource providers** en controleer of **Microsoft.BatCH** een geregistreerde provider is. 

   ![Bevestiging van de status ' geregistreerd '](media/join-azure-ssis-integration-runtime-virtual-network/batch-registered-confirmation.png)

   Als **Microsoft.BatCH** niet in de lijst wordt weer geven, [maakt u een leeg Azure batch-account](../batch/batch-account-create-portal.md) in uw abonnement om het te registreren. U kunt deze later verwijderen. 

### <a name="join-the-azure-ssis-ir-to-a-virtual-network"></a>De Azure-SSIS IR toevoegen aan een virtueel netwerk

Nadat u uw Azure Resource Manager virtuele netwerk of het klassieke virtuele netwerk hebt geconfigureerd, kunt u de Azure-SSIS IR toevoegen aan het virtuele netwerk:

1. Start micro soft Edge of Google Chrome. Momenteel ondersteunen alleen deze webbrowsers de Data Factory-gebruikers interface. 

1. Selecteer in de [Azure Portal](https://portal.azure.com)in het linkermenu **gegevens fabrieken**. Als er geen **gegevens fabrieken** worden weer gegeven in het menu, selecteert u **meer services** en selecteert u in de sectie **Intelligence en analyse** **gegevens fabrieken**. 

   ![Lijst met gegevens fabrieken](media/join-azure-ssis-integration-runtime-virtual-network/data-factories-list.png)

1. Selecteer uw data factory met de Azure-SSIS IR in de lijst. U ziet de start pagina voor uw data factory. Selecteer de tegel **Maken en bewaken**. U ziet de Data Factory gebruikers interface op een afzonderlijk tabblad. 

   ![Startpagina van de gegevensfactory](media/join-azure-ssis-integration-runtime-virtual-network/data-factory-home-page.png)

1. Ga in de Data Factory-gebruikers interface naar het tabblad **bewerken** , selecteer **verbindingen** en schakel over naar het tabblad **Integration Runtimes** . 

   ![Tabblad Integration Runtimes](media/join-azure-ssis-integration-runtime-virtual-network/integration-runtimes-tab.png)

1. Als uw Azure-SSIS IR wordt uitgevoerd, selecteert u in de kolom **acties** van de lijst met **integratie-Runtimes** de knop **stoppen** voor uw Azure-SSIS IR. U kunt uw Azure-SSIS IR pas bewerken nadat u het hebt gestopt. 

   ![De IR stoppen](media/join-azure-ssis-integration-runtime-virtual-network/stop-ir-button.png)

1. Selecteer de knop **bewerken** voor uw Azure-SSIS IR in de kolom **acties** van de lijst **Integration Runtimes** . 

   ![De Integration runtime bewerken](media/join-azure-ssis-integration-runtime-virtual-network/integration-runtime-edit.png)

1. Ga in het configuratie venster voor Integration runtime naar de secties **algemene instellingen** en **SQL-instellingen** en selecteer de knop **volgende** . 

1. In het gedeelte **Geavanceerde instellingen** : 

   1. Selecteer **een VNet selecteren voor uw Azure-SSIS Integration runtime om toe te voegen, ADF toestaan om bepaalde netwerk resources te maken en eventueel uw eigen statische open bare IP-adressen** in te stellen. 

   1. Selecteer bij **abonnement** het Azure-abonnement met uw virtuele netwerk.

   1. Voor **locatie** is dezelfde locatie van de Integration runtime geselecteerd.

   1. Selecteer bij **type** het type van uw virtuele netwerk: klassiek of Azure Resource Manager. We raden u aan om een Azure Resource Manager virtueel netwerk te selecteren, omdat klassieke virtuele netwerken binnenkort worden afgeschaft.

   1. Selecteer voor **VNet-naam** de naam van het virtuele netwerk. Dit moet hetzelfde zijn als de SQL Database met virtuele netwerk service-eind punten of een SQL Managed instance met een persoonlijk eind punt om SSISDB te hosten. Of het moet hetzelfde zijn als het account dat is verbonden met uw on-premises netwerk. Anders kan het een virtueel netwerk zijn om uw eigen statische open bare IP-adressen te maken voor Azure-SSIS IR.

   1. Selecteer bij **subnet naam** de naam van het subnet voor het virtuele netwerk. Dit moet hetzelfde zijn als de SQL Database met de service-eind punten van het virtuele netwerk om SSISDB te hosten. Of het moet een ander subnet zijn dat wordt gebruikt voor een SQL Managed instance met een persoonlijk eind punt om SSISDB te hosten. Anders kan het een wille keurige subnet zijn om uw eigen statische open bare IP-adressen voor Azure-SSIS IR te brengen.

   1. Schakel het selectie vakje **statische open bare IP-adressen voor uw Azure-SSIS Integration runtime** in om te kiezen of u uw eigen statische open bare IP-adressen wilt meenemen voor Azure-SSIS IR, zodat u ze op de firewall voor uw gegevens bronnen kunt toestaan.

      Als u het selectie vakje inschakelt, voert u de volgende stappen uit.

      1. Voor het **eerste statische open bare IP-adres** selecteert u het eerste statische open bare IP-adres dat [voldoet aan de vereisten](#publicIP) voor uw Azure-SSIS IR. Als u er geen hebt, klikt u op **nieuwe koppeling maken** om statische open bare IP-adressen te maken op Azure Portal en klikt u vervolgens op de knop Vernieuwen hier, zodat u ze kunt selecteren.
      
      1. Voor het **tweede statische open bare IP-adres** selecteert u het tweede statische open bare IP-adres dat [voldoet aan de vereisten](#publicIP) voor uw Azure-SSIS IR. Als u er geen hebt, klikt u op **nieuwe koppeling maken** om statische open bare IP-adressen te maken op Azure Portal en klikt u vervolgens op de knop Vernieuwen hier, zodat u ze kunt selecteren.

   1. Selecteer **VNet-validatie**. Als de validatie is geslaagd, selecteert u **door gaan**. 

   ![Geavanceerde instellingen met een virtueel netwerk](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-vnet.png)

1. Controleer in de sectie **samen vatting** alle instellingen voor uw Azure-SSIS IR. Selecteer vervolgens **bijwerken**.

1. Start uw Azure-SSIS IR door de knop **Start** te selecteren in de kolom **acties** voor uw Azure-SSIS IR. Het duurt ongeveer 20 tot 30 minuten om de Azure-SSIS IR die aan een virtueel netwerk zijn gekoppeld, te starten. 

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="define-the-variables"></a>De variabelen definiëren

```powershell
$ResourceGroupName = "[your Azure resource group name]"
$DataFactoryName = "[your data factory name]"
$AzureSSISName = "[your Azure-SSIS IR name]"
# Virtual network info: Classic or Azure Resource Manager
$VnetId = "[your virtual network resource ID or leave it empty]" # REQUIRED if you use SQL Database with IP firewall rules/virtual network service endpoints or SQL Managed Instance with private endpoint to host SSISDB, or if you require access to on-premises data without configuring a self-hosted IR. We recommend an Azure Resource Manager virtual network, because classic virtual networks will be deprecated soon.
$SubnetName = "[your subnet name or leave it empty]" # WARNING: Use the same subnet as the one used for SQL Database with virtual network service endpoints, or a different subnet from the one used for SQL Managed Instance with a private endpoint
# Public IP address info: OPTIONAL to provide two standard static public IP addresses with DNS name under the same subscription and in the same region as your virtual network
$FirstPublicIP = "[your first public IP address resource ID or leave it empty]"
$SecondPublicIP = "[your second public IP address resource ID or leave it empty]"
```

### <a name="configure-a-virtual-network"></a>Een virtueel netwerk configureren

Voordat u uw Azure-SSIS IR kunt toevoegen aan een virtueel netwerk, moet u het virtuele netwerk configureren. Als u de machtigingen en instellingen van het virtuele netwerk voor uw Azure-SSIS IR automatisch wilt configureren om lid te worden van het virtuele netwerk, voegt u het volgende script toe:

```powershell
# Make sure to run this script against the subscription to which the virtual network belongs.
if(![string]::IsNullOrEmpty($VnetId) -and ![string]::IsNullOrEmpty($SubnetName))
{
    # Register to the Azure Batch resource provider
    $BatchApplicationId = "ddbf3205-c6bd-46ae-8127-60eb93363864"
    $BatchObjectId = (Get-AzADServicePrincipal -ServicePrincipalName $BatchApplicationId).Id
    Register-AzResourceProvider -ProviderNamespace Microsoft.Batch
    while(!(Get-AzResourceProvider -ProviderNamespace "Microsoft.Batch").RegistrationState.Contains("Registered"))
    {
    Start-Sleep -s 10
    }
    if($VnetId -match "/providers/Microsoft.ClassicNetwork/")
    {
        # Assign the VM contributor role to Microsoft.Batch
        New-AzRoleAssignment -ObjectId $BatchObjectId -RoleDefinitionName "Classic Virtual Machine Contributor" -Scope $VnetId
    }
}
```

### <a name="create-an-azure-ssis-ir-and-join-it-to-a-virtual-network"></a>Een Azure-SSIS IR maken en koppelen aan een virtueel netwerk

U kunt op hetzelfde moment een Azure-SSIS IR maken en koppelen aan een virtueel netwerk. Zie [een Azure-SSIS IR maken](create-azure-ssis-integration-runtime.md#use-azure-powershell-to-create-an-integration-runtime)voor het volledige script en de instructies.

### <a name="join-an-existing-azure-ssis-ir-to-a-virtual-network"></a>Een bestaande Azure-SSIS IR toevoegen aan een virtueel netwerk

In het artikel [een Azure-SSIS IR maken](create-azure-ssis-integration-runtime.md) ziet u hoe u een Azure-SSIS IR maakt en deze koppelt aan een virtueel netwerk in hetzelfde script. Als u al een Azure-SSIS IR hebt, volgt u deze stappen om het aan het virtuele netwerk toe te voegen: 
1. Stop de Azure-SSIS IR. 
1. Configureer de Azure-SSIS IR om lid te worden van het virtuele netwerk. 
1. Start de Azure-SSIS IR. 

### <a name="stop-the-azure-ssis-ir"></a>De Azure-SSIS IR stoppen

U moet de Azure-SSIS IR stoppen voordat u deze kunt koppelen aan een virtueel netwerk. Met deze opdracht worden alle knoop punten vrijgegeven en wordt de facturering gestopt:

```powershell
Stop-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $DataFactoryName `
    -Name $AzureSSISName `
    -Force 
```

### <a name="configure-virtual-network-settings-for-the-azure-ssis-ir-to-join"></a>Configureer de instellingen van het virtuele netwerk voor de Azure-SSIS IR om lid te worden

Als u instellingen wilt configureren voor het virtuele netwerk waaraan de Azure-SSIS wordt toegevoegd, gebruikt u dit script: 

```powershell
# Make sure to run this script against the subscription to which the virtual network belongs.
if(![string]::IsNullOrEmpty($VnetId) -and ![string]::IsNullOrEmpty($SubnetName))
{
    # Register to the Azure Batch resource provider
    $BatchApplicationId = "ddbf3205-c6bd-46ae-8127-60eb93363864"
    $BatchObjectId = (Get-AzADServicePrincipal -ServicePrincipalName $BatchApplicationId).Id
    Register-AzResourceProvider -ProviderNamespace Microsoft.Batch
    while(!(Get-AzResourceProvider -ProviderNamespace "Microsoft.Batch").RegistrationState.Contains("Registered"))
    {
        Start-Sleep -s 10
    }
    if($VnetId -match "/providers/Microsoft.ClassicNetwork/")
    {
        # Assign VM contributor role to Microsoft.Batch
        New-AzRoleAssignment -ObjectId $BatchObjectId -RoleDefinitionName "Classic Virtual Machine Contributor" -Scope $VnetId
    }
}
```

### <a name="configure-the-azure-ssis-ir"></a>De Azure-SSIS IR configureren

Voer de volgende opdracht uit om uw Azure-SSIS IR toe te voegen aan een virtueel netwerk `Set-AzDataFactoryV2IntegrationRuntime` : 

```powershell
Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $DataFactoryName `
    -Name $AzureSSISName `
    -VnetId $VnetId `
    -Subnet $SubnetName

# Add public IP address parameters if you bring your own static public IP addresses
if(![string]::IsNullOrEmpty($FirstPublicIP) -and ![string]::IsNullOrEmpty($SecondPublicIP))
{
    $publicIPs = @($FirstPublicIP, $SecondPublicIP)
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
        -DataFactoryName $DataFactoryName `
        -Name $AzureSSISName `
        -PublicIPs $publicIPs
}
```

### <a name="start-the-azure-ssis-ir"></a>De Azure-SSIS IR starten

Als u de Azure-SSIS IR wilt starten, voert u de volgende opdracht uit: 

```powershell
Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $DataFactoryName `
    -Name $AzureSSISName `
    -Force
```

Het volt ooien van deze opdracht duurt 20 tot 30 minuten.

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de volgende artikelen voor meer informatie over Azure-SSIS IR: 
- [Azure-SSIS IR](concepts-integration-runtime.md#azure-ssis-integration-runtime). Dit artikel bevat algemene conceptuele informatie over IRs, met inbegrip van Azure-SSIS IR. 
- [Zelf studie: SSIS-pakketten implementeren in azure](./tutorial-deploy-ssis-packages-azure.md). Deze zelf studie bevat stapsgewijze instructies voor het maken van uw Azure-SSIS IR. Er wordt gebruikgemaakt van Azure SQL Database voor het hosten van de SSIS-catalogus. 
- [Maak een Azure-SSIS IR](create-azure-ssis-integration-runtime.md). In dit artikel wordt de zelf studie uitgebreid. Dit onderwerp bevat instructies over het gebruik van Azure SQL Database met virtuele netwerk service-eind punten of een SQL Managed instance in een virtueel netwerk om de SSIS-catalogus te hosten. U ziet hoe u uw Azure-SSIS IR kunt koppelen aan een virtueel netwerk. 
- [Een Azure-SSIS IR controleren](monitor-integration-runtime.md#azure-ssis-integration-runtime). In dit artikel leest u hoe u informatie over uw Azure-SSIS IR kunt ophalen. Het bevat status beschrijvingen voor de geretourneerde informatie. 
- [Een Azure-SSIS IR beheren](manage-azure-ssis-integration-runtime.md). In dit artikel leest u hoe u uw Azure-SSIS IR kunt stoppen, starten of verwijderen. U ziet ook hoe u uw Azure-SSIS IR kunt uitschalen door knoop punten toe te voegen.
