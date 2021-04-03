---
title: Toegang op basis van virtueel netwerk configureren voor een Azure Cosmos-account
description: In dit document worden de stappen beschreven die nodig zijn voor het instellen van een service-eind punt voor een virtueel netwerk voor Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: how-to
ms.date: 10/13/2020
ms.author: mjbrown
ms.custom: devx-track-azurecli
ms.openlocfilehash: 1d63d21f4c49e3c7aef035208477ac9fc79f2e51
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "94637180"
---
# <a name="configure-access-to-azure-cosmos-db-from-virtual-networks-vnet"></a>Toegang tot Azure Cosmos DB van virtuele netwerken (VNet) configureren
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

U kunt het Azure Cosmos-account configureren om alleen toegang toe te staan vanuit een specifiek subnet van een virtueel netwerk (VNet). Door [service-eind punten](../virtual-network/virtual-network-service-endpoints-overview.md) in te scha kelen voor toegang tot Azure Cosmos DB op het subnet binnen een virtueel netwerk, wordt het verkeer van dat subnet verzonden naar Azure Cosmos DB met de identiteit van het subnet en Virtual Network. Zodra het eind punt van de Azure Cosmos DB service is ingeschakeld, kunt u de toegang tot het subnet beperken door het toe te voegen aan uw Azure Cosmos-account.

Een Azure Cosmos-account is standaard toegankelijk vanuit elke bron als de aanvraag vergezeld gaat van een geldig autorisatie token. Wanneer u een of meer subnetten in VNets toevoegt, krijgen alleen aanvragen die afkomstig zijn van deze subnetten een geldig antwoord. Aanvragen die afkomstig zijn van een andere bron, ontvangen een antwoord van 403 (verboden). 

U kunt Azure Cosmos DB-accounts configureren om alleen toegang toe te staan vanaf een specifiek subnet van een virtueel Azure-netwerk. De toegang tot een Azure Cosmos DB account beperken met verbindingen van een subnet in een virtueel netwerk:

1. Schakel het subnet in om de identiteit van het subnet en het virtuele netwerk naar Azure Cosmos DB te verzenden. U kunt dit doen door een service-eind punt in te scha kelen voor Azure Cosmos DB op het specifieke subnet.

1. Voeg een regel toe aan het Azure Cosmos DB-account om het subnet op te geven als bron waarvan het account toegang kan krijgen.

> [!NOTE]
> Wanneer een service-eind punt voor uw Azure Cosmos DB-account in een subnet is ingeschakeld, wordt de bron van het verkeer dat Azure Cosmos DB overschakelt van een openbaar IP-adres naar een virtueel netwerk en subnet. Het wisselen van verkeer is van toepassing op alle Azure Cosmos DB accounts die vanuit dit subnet toegankelijk zijn. Als uw Azure Cosmos DB accounts een op IP gebaseerde firewall hebben om dit subnet toe te staan, komen aanvragen van het service-subnet niet meer overeen met de IP-firewall regels en worden deze geweigerd.
>
> Zie de stappen die worden beschreven in de sectie [migreren van een IP-firewall regel naar een lijst met virtuele netwerk toegangs beheer](#migrate-from-firewall-to-vnet) in dit artikel voor meer informatie.

In de volgende secties wordt beschreven hoe u een service-eind punt voor een virtueel netwerk configureert voor een Azure Cosmos DB-account.

## <a name="configure-a-service-endpoint-by-using-the-azure-portal"></a><a id="configure-using-portal"></a>Een service-eind punt configureren met behulp van de Azure Portal

### <a name="configure-a-service-endpoint-for-an-existing-azure-virtual-network-and-subnet"></a>Een service-eind punt configureren voor een bestaand virtueel Azure-netwerk en-subnet

1. Ga op de Blade **alle resources** naar het Azure Cosmos DB account dat u wilt beveiligen.

1. Selecteer **firewalls en virtuele netwerken** in het menu instellingen en kies ervoor om toegang vanaf **geselecteerde netwerken** toe te staan.

1. Als u toegang wilt verlenen tot een bestaand subnet van een virtueel netwerk, selecteert u onder **virtuele netwerken** **bestaande Azure Virtual Network toevoegen**.

1. Selecteer het **abonnement** waarvan u een virtueel Azure-netwerk wilt toevoegen. Selecteer de virtuele Azure- **netwerken** en **subnetten** die u toegang wilt verlenen tot uw Azure Cosmos DB-account. Selecteer vervolgens **inschakelen** om geselecteerde netwerken met Service-eind punten in te scha kelen voor ' micro soft. AzureCosmosDB '. Wanneer het is voltooid, selecteert u **toevoegen**.

   :::image type="content" source="./media/how-to-configure-vnet-service-endpoint/choose-subnet-and-vnet.png" alt-text="Virtueel netwerk en subnet selecteren":::

1. Nadat het Azure Cosmos DB-account is ingeschakeld voor toegang vanuit een virtueel netwerk, is verkeer alleen toegestaan vanuit het gekozen subnet. Het virtuele netwerk en het subnet dat u hebt toegevoegd, moeten worden weer gegeven zoals in de volgende scherm afbeelding:

   :::image type="content" source="./media/how-to-configure-vnet-service-endpoint/vnet-and-subnet-configured-successfully.png" alt-text="Het virtuele netwerk en het subnet zijn geconfigureerd":::

> [!NOTE]
> Als u service-eind punten voor virtuele netwerken wilt inschakelen, hebt u de volgende abonnements machtigingen nodig:
>   * Abonnement met virtueel netwerk: Inzender voor netwerken
>   * Abonnement met Azure Cosmos DB account: Inzender van DocumentDB-account
>   * Als uw virtuele netwerk en Azure Cosmos DB account zich in verschillende abonnementen bevinden, moet u ervoor zorgen dat er voor het abonnement dat een virtueel netwerk heeft, ook een `Microsoft.DocumentDB` resource provider is geregistreerd. Als u een resource provider wilt registreren, raadpleegt u het artikel [Azure-resource providers en-typen](../azure-resource-manager/management/resource-providers-and-types.md) .

Hier vindt u de instructies voor het registreren van een abonnement bij een resource provider.

### <a name="configure-a-service-endpoint-for-a-new-azure-virtual-network-and-subnet"></a>Een service-eind punt configureren voor een nieuw virtueel Azure-netwerk en-subnet

1. Ga op de Blade **alle resources** naar het Azure Cosmos DB account dat u wilt beveiligen.  

1. Selecteer **firewalls en virtuele netwerken van Azure** in het menu instellingen en kies ervoor om toegang vanaf **geselecteerde netwerken** toe te staan.  

1. Als u toegang wilt verlenen tot een nieuw virtueel Azure-netwerk, selecteert u onder **virtuele netwerken** **nieuwe virtuele netwerk toevoegen**.  

1. Geef de vereiste gegevens op voor het maken van een nieuw virtueel netwerk en selecteer vervolgens **maken**. Het subnet wordt gemaakt met een service-eind punt voor ' micro soft. AzureCosmosDB ' ingeschakeld.

   :::image type="content" source="./media/how-to-configure-vnet-service-endpoint/choose-subnet-and-vnet-new-vnet.png" alt-text="Een virtueel netwerk en subnet selecteren voor een nieuw virtueel netwerk":::

Als uw Azure Cosmos DB-account wordt gebruikt door andere Azure-Services zoals Azure Cognitive Search, of als het is geopend vanuit stream Analytics of Power BI, kunt u toegang toestaan door verbindingen accepteren te selecteren in de **wereld wijde Azure-data centers**.

Om ervoor te zorgen dat u toegang hebt tot Azure Cosmos DB metrische gegevens uit de portal, moet u **toegang toestaan via Azure Portal** opties inschakelen. Zie het artikel [een IP-Firewall configureren](how-to-configure-firewall.md) voor meer informatie over deze opties. Nadat u toegang hebt ingeschakeld, selecteert u **Opslaan** om de instellingen op te slaan.

## <a name="remove-a-virtual-network-or-subnet"></a><a id="remove-vnet-or-subnet"></a>Een virtueel netwerk of subnet verwijderen

1. Ga op de Blade **alle resources** naar het Azure Cosmos DB account waarvoor u service-eind punten hebt toegewezen.  

1. Selecteer **firewalls en virtuele netwerken** in het menu instellingen.  

1. Als u een virtuele netwerk-of subnet-regel wilt verwijderen, selecteert u **...** naast het virtuele netwerk of subnet en selecteert u **verwijderen**.

   :::image type="content" source="./media/how-to-configure-vnet-service-endpoint/remove-a-vnet.png" alt-text="Een virtueel netwerk verwijderen":::

1. Klik op **Opslaan** om uw wijzigingen toe te passen.

## <a name="configure-a-service-endpoint-by-using-azure-powershell"></a><a id="configure-using-powershell"></a>Een service-eind punt configureren met behulp van Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Gebruik de volgende stappen om een service-eind punt te configureren voor een Azure Cosmos DB-account met behulp van Azure PowerShell:  

1. Installeer [Azure PowerShell](/powershell/azure/install-Az-ps) en [Meld u aan](/powershell/azure/authenticate-azureps).  

1. Schakel het service-eind punt in voor een bestaand subnet van een virtueel netwerk.  

   ```powershell
   $resourceGroupName = "<Resource group name>"
   $vnetName = "<Virtual network name>"
   $subnetName = "<Subnet name>"
   $subnetPrefix = "<Subnet address range>"
   $serviceEndpoint = "Microsoft.AzureCosmosDB"

   Get-AzVirtualNetwork `
      -ResourceGroupName $resourceGroupName `
      -Name $vnetName | Set-AzVirtualNetworkSubnetConfig `
      -Name $subnetName `
      -AddressPrefix $subnetPrefix `
      -ServiceEndpoint $serviceEndpoint | Set-AzVirtualNetwork
   ```

   > [!NOTE]
   > Wanneer u Power shell of de Azure CLI gebruikt, moet u ervoor zorgen dat u de volledige lijst met IP-filters en virtuele netwerk-Acl's opgeeft in para meters, niet alleen de items die moeten worden toegevoegd.

1. Gegevens van virtueel netwerk ophalen.

   ```powershell
   $vnet = Get-AzVirtualNetwork `
      -ResourceGroupName $resourceGroupName `
      -Name $vnetName

   $subnetId = $vnet.Id + "/subnets/" + $subnetName
   ```

1. Een Cosmos DB Virtual Network regel voorbereiden

   ```powershell
   $vnetRule = New-AzCosmosDBVirtualNetworkRule `
      -Id $subnetId
   ```

1. Azure Cosmos DB account eigenschappen bijwerken met de nieuwe Virtual Network eindpunt configuratie: 

   ```powershell
   $accountName = "<Cosmos DB account name>"

   Update-AzCosmosDBAccount `
      -ResourceGroupName $resourceGroupName `
      -Name $accountName `
      -EnableVirtualNetwork $true `
      -VirtualNetworkRuleObject @($vnetRule)
   ```

1. Voer de volgende opdracht uit om te controleren of uw Azure Cosmos DB-account is bijgewerkt met het virtuele netwerk service-eind punt dat u in de vorige stap hebt geconfigureerd:

   ```powershell
   $account = Get-AzCosmosDBAccount `
      -ResourceGroupName $resourceGroupName `
      -Name $accountName

   $account.IsVirtualNetworkFilterEnabled
   $account.VirtualNetworkRules
   ```

## <a name="configure-a-service-endpoint-by-using-the-azure-cli"></a><a id="configure-using-cli"></a>Een service-eind punt configureren met behulp van de Azure CLI

Azure Cosmos-accounts kunnen worden geconfigureerd voor service-eind punten wanneer deze op een later tijdstip worden gemaakt of bijgewerkt als het subnet al voor hen is geconfigureerd. Service-eind punten kunnen ook worden ingeschakeld voor het Cosmos-account waarin het subnet nog niet is geconfigureerd en die vervolgens zal werken wanneer het subnet later wordt geconfigureerd. Met deze flexibiliteit kunnen beheerders die geen toegang hebben tot zowel het Cosmos-account als de virtuele netwerk bronnen, hun configuraties onafhankelijk van elkaar maken.

### <a name="create-a-new-cosmos-account-and-connect-it-to-a-back-end-subnet-for-a-new-virtual-network"></a>Een nieuw Cosmos-account maken en deze verbinden met een back-end-subnet voor een nieuw virtueel netwerk

In dit voor beeld wordt het virtuele netwerk en het subnet gemaakt met Service-eind punten die zijn ingeschakeld voor beide wanneer ze worden gemaakt.

```azurecli-interactive
# Create an Azure Cosmos Account with a service endpoint connected to a backend subnet

# Resource group and Cosmos account variables
resourceGroupName='MyResourceGroup'
location='West US 2'
accountName='mycosmosaccount'

# Variables for a new Virtual Network with two subnets
vnetName='myVnet'
frontEnd='FrontEnd'
backEnd='BackEnd'

# Create a resource group
az group create -n $resourceGroupName -l $location

# Create a virtual network with a front-end subnet
az network vnet create \
   -n $vnetName \
   -g $resourceGroupName \
   --address-prefix 10.0.0.0/16 \
   --subnet-name $frontEnd \
   --subnet-prefix 10.0.1.0/24

# Create a back-end subnet with service endpoints enabled for Cosmos DB
az network vnet subnet create \
   -n $backEnd \
   -g $resourceGroupName \
   --address-prefix 10.0.2.0/24 \
   --vnet-name $vnetName \
   --service-endpoints Microsoft.AzureCosmosDB

svcEndpoint=$(az network vnet subnet show -g $resourceGroupName -n $backEnd --vnet-name $vnetName --query 'id' -o tsv)

# Create a Cosmos DB account with default values and service endpoints
az cosmosdb create \
   -n $accountName \
   -g $resourceGroupName \
   --enable-virtual-network true \
   --virtual-network-rules $svcEndpoint
```

### <a name="connect-and-configure-a-cosmos-account-to-a-back-end-subnet-independently"></a>Verbinding maken en configureren van een Cosmos-account op een back-end-subnet onafhankelijk

Dit voor beeld is bedoeld om te laten zien hoe u een Azure Cosmos-account verbindt met een bestaand nieuw virtueel netwerk waar het subnet nog niet is geconfigureerd voor service-eind punten. Dit wordt gedaan met behulp van de `--ignore-missing-vnet-service-endpoint` para meter. Hierdoor kan de configuratie voor het Cosmos-account zonder fouten worden voltooid voordat de configuratie naar het subnet van het virtuele netwerk is voltooid. Zodra de configuratie van het subnet is voltooid, is het Cosmos-account dan toegankelijk via het geconfigureerde subnet.

```azurecli-interactive
# Create an Azure Cosmos Account with a service endpoint connected to a backend subnet
# that is not yet enabled for service endpoints.

# Resource group and Cosmos account variables
resourceGroupName='MyResourceGroup'
location='West US 2'
accountName='mycosmosaccount'

# Variables for a new Virtual Network with two subnets
vnetName='myVnet'
frontEnd='FrontEnd'
backEnd='BackEnd'

# Create a resource group
az group create -n $resourceGroupName -l $location

# Create a virtual network with a front-end subnet
az network vnet create \
   -n $vnetName \
   -g $resourceGroupName \
   --address-prefix 10.0.0.0/16 \
   --subnet-name $frontEnd \
   --subnet-prefix 10.0.1.0/24

# Create a back-end subnet but without configuring service endpoints (--service-endpoints Microsoft.AzureCosmosDB)
az network vnet subnet create \
   -n $backEnd \
   -g $resourceGroupName \
   --address-prefix 10.0.2.0/24 \
   --vnet-name $vnetName

svcEndpoint=$(az network vnet subnet show -g $resourceGroupName -n $backEnd --vnet-name $vnetName --query 'id' -o tsv)

# Create a Cosmos DB account with default values
az cosmosdb create -n $accountName -g $resourceGroupName

# Add the virtual network rule but ignore the missing service endpoint on the subnet
az cosmosdb network-rule add \
   -n $accountName \
   -g $resourceGroupName \
   --virtual-network $vnetName \
   --subnet svcEndpoint \
   --ignore-missing-vnet-service-endpoint true

read -p'Press any key to now configure the subnet for service endpoints'

az network vnet subnet update \
   -n $backEnd \
   -g $resourceGroupName \
   --vnet-name $vnetName \
   --service-endpoints Microsoft.AzureCosmosDB
```

## <a name="port-range-when-using-direct-mode"></a>Poort bereik bij gebruik van directe modus

Wanneer u service-eind punten gebruikt met een Azure Cosmos-account via een directe modus verbinding, moet u ervoor zorgen dat het TCP-poort bereik van 10000 tot 20000 is geopend.

## <a name="migrating-from-an-ip-firewall-rule-to-a-virtual-network-acl"></a><a id="migrate-from-firewall-to-vnet"></a>Migreren van een IP-firewall regel naar een virtuele netwerk-ACL

Voer de volgende stappen uit om een Azure Cosmos DB-account te migreren met behulp van IP-firewall regels voor het gebruik van service-eind punten van een virtueel netwerk.

Nadat een Azure Cosmos DB account is geconfigureerd voor een service-eind punt voor een subnet, worden aanvragen van dat subnet verzonden naar Azure Cosmos DB met virtuele netwerk-en subnet gegevens in plaats van een openbaar IP-bron adres. Deze aanvragen komen niet meer overeen met een IP-filter dat is geconfigureerd op het Azure Cosmos DB-account. Daarom zijn de volgende stappen nodig om uitval tijd te voor komen.

Voordat u doorgaat, schakelt u het Azure Cosmos DB Service-eind punt in op het virtuele netwerk en subnet met behulp van de hierboven weer gegeven stap in het service-eind punt inschakelen voor een bestaand subnet van een virtueel netwerk.

1. Informatie over virtueel netwerk en subnet ophalen:

   ```powershell
   $resourceGroupName = "myResourceGroup"
   $accountName = "mycosmosaccount"
   $vnetName = "myVnet"
   $subnetName = "mySubnet"

   $vnet = Get-AzVirtualNetwork `
      -ResourceGroupName $resourceGroupName `
      -Name $vnetName

   $subnetId = $vnet.Id + "/subnets/" + $subnetName
   ```

1. Een nieuw Virtual Network-regel object voorbereiden voor het Azure Cosmos DB-account:

   ```powershell
   $vnetRule = New-AzCosmosDBVirtualNetworkRule `
      -Id $subnetId
   ```

1. Werk het Azure Cosmos DB-account bij om service Endpoint-toegang vanuit het subnet in te scha kelen:

   ```powershell
   Update-AzCosmosDBAccount `
      -ResourceGroupName $resourceGroupName `
      -Name $accountName `
      -EnableVirtualNetwork $true `
      -VirtualNetworkRuleObject @($vnetRule)
   ```

1. Herhaal de vorige stappen voor alle Azure Cosmos DB accounts die vanuit het subnet toegankelijk zijn.

1. Verwijder de IP-firewall regel voor het subnet uit de firewall regels van het Azure Cosmos DB-account.

## <a name="frequently-asked-questions"></a>Veelgestelde vragen

Hier volgen enkele veelgestelde vragen over het configureren van toegang vanaf virtuele netwerken:

### <a name="are-notebooks-and-mongocassandra-shell-currently-compatible-with-virtual-network-enabled-accounts"></a>Zijn er momenteel notebooks en Mongo/Cassandra shell compatibel met Virtual Network ingeschakelde accounts?

Op het moment dat de [Mongo-shell](https://devblogs.microsoft.com/cosmosdb/preview-native-mongo-shell/) en Cassandra- [Shell](https://devblogs.microsoft.com/cosmosdb/announcing-native-cassandra-shell-preview/) integraties in de Cosmos DB Data Explorer en de [Jupyter-notebooks-service](./cosmosdb-jupyter-notebooks.md), worden niet ondersteund met VNET-toegang. Dit is momenteel actief in ontwikkeling.

### <a name="can-i-specify-both-virtual-network-service-endpoint-and-ip-access-control-policy-on-an-azure-cosmos-account"></a>Kan ik zowel het virtuele netwerk service-eind punt als het IP-toegangs beheer beleid voor een Azure Cosmos-account opgeven? 

U kunt zowel het service-eind punt van de virtuele netwerk als een beleid voor IP-toegangs beheer (ook wel bekend als firewall) inschakelen voor uw Azure Cosmos-account. Deze twee functies zijn complementair en samen zorgen voor isolatie en beveiliging van uw Azure Cosmos-account. Met IP-firewall weet u zeker dat statische IP-adressen toegang hebben tot uw account. 

### <a name="how-do-i-limit-access-to-subnet-within-a-virtual-network"></a>Hoe kan ik de toegang tot subnet binnen een virtueel netwerk beperken? 

Er zijn twee stappen vereist om de toegang tot het Azure Cosmos-account te beperken vanuit een subnet. Eerst staat u verkeer van subnet toe om de identiteit van het subnet en het virtuele netwerk te Azure Cosmos DB. Dit wordt gedaan door het inschakelen van service-eind punten voor Azure Cosmos DB in het subnet. Vervolgens wordt een regel toegevoegd aan het Azure Cosmos-account dat dit subnet specificeert als bron van welk account toegang kan worden verkregen.

### <a name="will-virtual-network-acls-and-ip-firewall-reject-requests-or-connections"></a>Worden door Acl's van virtuele netwerken en IP-firewall aanvragen of verbindingen geweigerd? 

Wanneer IP-firewall of toegangs regels voor virtuele netwerken worden toegevoegd, krijgen alleen aanvragen van toegestane bronnen geldige reacties. Andere aanvragen worden afgewezen met een 403 (verboden). Het is belang rijk om de firewall van een Azure Cosmos-account te onderscheiden van een firewall op verbindings niveau. De bron kan nog steeds verbinding maken met de service en de verbindingen zelf worden niet geweigerd.

### <a name="my-requests-started-getting-blocked-when-i-enabled-service-endpoint-to-azure-cosmos-db-on-the-subnet-what-happened"></a>Mijn aanvragen die zijn gestart, worden geblokkeerd wanneer het service-eind punt is ingeschakeld voor Azure Cosmos DB op het subnet. Wat is er gebeurd?

Zodra het service-eind punt voor Azure Cosmos DB op een subnet is ingeschakeld, wordt de bron van het verkeer dat de account overschakelt van het open bare IP-adres naar het virtuele netwerk en subnet. Als uw Azure Cosmos-account alleen een IP-firewall heeft, komt het verkeer van het subnet waarvoor de service is ingeschakeld niet meer overeen met de IP-firewall regels en wordt daarom geweigerd. Lees de stappen voor probleemloze migratie van een firewall op basis van een IP-adres naar het toegangs beheer via een virtueel netwerk.

### <a name="are-additional-azure-rbac-permissions-needed-for-azure-cosmos-accounts-with-vnet-service-endpoints"></a>Zijn er extra Azure RBAC-machtigingen nodig voor Azure Cosmos-accounts met de VNET-service-eind punten?

Nadat u de VNet-service-eind punten aan een Azure Cosmos-account hebt toegevoegd, moet u toegang hebben tot de `Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action` actie voor alle VNETs die zijn geconfigureerd voor uw Azure Cosmos-account om wijzigingen aan te brengen in de account instellingen. Deze machtiging is vereist omdat het autorisatie proces de toegang tot bronnen (zoals de resources van de data base en het virtuele netwerk) valideert voordat de eigenschappen worden geëvalueerd.
 
De autorisatie valideert de machtiging voor de VNet-resource actie, zelfs als de gebruiker niet de VNET-Acl's opgeeft met behulp van Azure CLI. Op dit moment ondersteunt het besturings vlak van het Azure Cosmos-account de voltooiings status van het Azure Cosmos-account. Een van de para meters voor de besturings vlak-aanroepen is `virtualNetworkRules` . Als deze para meter niet wordt opgegeven, wordt met de Azure CLI een Get data base-aanroep opgehaald `virtualNetworkRules` en wordt deze waarde in de update aanroep gebruikt.

### <a name="do-the-peered-virtual-networks-also-have-access-to-azure-cosmos-account"></a>Hebben de gekoppelde virtuele netwerken ook toegang tot het Azure Cosmos-account? 
Alleen virtuele netwerken en hun subnetten die zijn toegevoegd aan het Azure Cosmos-account hebben toegang. Hun peered VNets heeft geen toegang tot het account totdat de subnetten in gekoppelde virtuele netwerken aan het account worden toegevoegd.

### <a name="what-is-the-maximum-number-of-subnets-allowed-to-access-a-single-cosmos-account"></a>Wat is het maximum aantal subnetten dat is toegestaan voor toegang tot één Cosmos-account? 
Op dit moment kunt u Maxi maal 256 subnetten hebben die zijn toegestaan voor een Azure Cosmos-account.

### <a name="can-i-enable-access-from-vpn-and-express-route"></a>Kan ik de toegang vanaf VPN en Express route inschakelen? 
Voor toegang tot het Azure Cosmos-account via Express route van on-premises moet u micro soft-peering inschakelen. Zodra u de toegangs regels voor de IP-firewall of het virtuele netwerk hebt ingesteld, kunt u de open bare IP-adressen die worden gebruikt voor micro soft-peering toevoegen aan de IP-firewall van uw Azure Cosmos-account, zodat lokale services toegang hebben tot het Azure Cosmos-account. 

### <a name="do-i-need-to-update-the-network-security-groups-nsg-rules"></a>Moet ik de regels voor netwerk beveiligings groepen (NSG) bijwerken? 
NSG regels worden gebruikt om de verbinding met en van een subnet met een virtueel netwerk te beperken. Wanneer u een service-eind punt voor Azure Cosmos DB aan het subnet toevoegt, is het niet nodig om uitgaande connectiviteit in NSG te openen voor uw Azure Cosmos-account. 

### <a name="are-service-endpoints-available-for-all-vnets"></a>Zijn service-eind punten beschikbaar voor alle VNets?
Nee, alleen Azure Resource Manager virtuele netwerken kunnen service-eind punten hebben ingeschakeld. Klassieke virtuele netwerken bieden geen ondersteuning voor service-eind punten.

### <a name="can-i-accept-connections-from-within-public-azure-datacenters-when-service-endpoint-access-is-enabled-for-azure-cosmos-db"></a>Kan ik ' verbindingen accepteren vanuit open bare Azure-data centers ' wanneer toegang tot het service-eind punt is ingeschakeld voor Azure Cosmos DB?  
Dit is alleen vereist als u wilt dat uw Azure Cosmos DB-account wordt geopend door andere Azure-Services voor de eerste partij, zoals Azure Data Factory, Azure Cognitive Search of een service die in de opgegeven Azure-regio is geïmplementeerd.

## <a name="next-steps"></a>Volgende stappen

* Zie het artikel [firewall ondersteuning](how-to-configure-firewall.md) voor het configureren van een firewall voor Azure Cosmos db.
