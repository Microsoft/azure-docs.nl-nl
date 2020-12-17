---
title: Een persoonlijke Azure-koppeling configureren voor een Azure Cosmos-account
description: Meer informatie over het instellen van een persoonlijke Azure-koppeling voor toegang tot een Azure Cosmos-account met behulp van een privé-IP-adres in een virtueel netwerk.
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: how-to
ms.date: 12/16/2020
ms.author: thweiss
ms.custom: devx-track-azurecli
ms.openlocfilehash: 9a6db0d25165059581d7ffafa5b8e7fd19330c87
ms.sourcegitcommit: 8c3a656f82aa6f9c2792a27b02bbaa634786f42d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/17/2020
ms.locfileid: "97629643"
---
# <a name="configure-azure-private-link-for-an-azure-cosmos-account"></a>Een persoonlijke Azure-koppeling configureren voor een Azure Cosmos-account
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Met behulp van een persoonlijke Azure-koppeling kunt u verbinding maken met een Azure Cosmos-account via een persoonlijk eind punt. Het persoonlijke eind punt is een reeks privé-IP-adressen in een subnet binnen het virtuele netwerk. Vervolgens kunt u de toegang tot een Azure Cosmos-account beperken via privé-IP-adressen. Wanneer een persoonlijke koppeling wordt gecombineerd met een beperkt NSG-beleid, vermindert dit het risico van gegevens exfiltration. Zie het artikel over een [persoonlijke Azure-koppeling](../private-link/private-link-overview.md) voor meer informatie over privé-eind punten.

> [!NOTE]
> Persoonlijke koppeling verhindert niet dat uw Azure Cosmos-eind punten worden omgezet door open bare DNS. Het filteren van binnenkomende aanvragen gebeurt op toepassings niveau, niet op Trans Port of op netwerk niveau.

Met persoonlijke koppeling kunnen gebruikers toegang krijgen tot een Azure Cosmos-account vanuit het virtuele netwerk of via een peered virtueel netwerk. Resources die zijn toegewezen aan een privé koppeling, zijn ook on-premises toegankelijk via privé-peering via VPN of Azure ExpressRoute. 

U kunt verbinding maken met een Azure Cosmos-account dat is geconfigureerd met een persoonlijke koppeling met behulp van de automatische of hand matige goedkeurings methode. Zie de sectie [goedkeurings werk stroom](../private-link/private-endpoint-overview.md#access-to-a-private-link-resource-using-approval-workflow) van de documentatie van de persoonlijke koppeling voor meer informatie. 

In dit artikel worden de stappen beschreven voor het maken van een privé-eindpunt. Hierbij wordt ervan uitgegaan dat u de automatische goedkeurings methode gebruikt.

## <a name="create-a-private-endpoint-by-using-the-azure-portal"></a>Een persoonlijk eind punt maken met behulp van de Azure Portal

Gebruik de volgende stappen om een persoonlijk eind punt te maken voor een bestaand Azure Cosmos-account met behulp van de Azure Portal:

1. Kies in het deel venster **alle resources** een Azure Cosmos-account.

1. Selecteer **particuliere endpoint-verbindingen** in de lijst met instellingen en selecteer vervolgens **persoonlijk eind punt**:

   :::image type="content" source="./media/how-to-configure-private-endpoints/create-private-endpoint-portal.png" alt-text="Selecties voor het maken van een persoonlijk eind punt in de Azure Portal":::

1. Voer in het deel venster **een persoonlijk eind punt maken-basis beginselen** de volgende gegevens in of Selecteer deze:

    | Instelling | Waarde |
    | ------- | ----- |
    | **Projectgegevens** | |
    | Abonnement | Selecteer uw abonnement. |
    | Resourcegroep | Selecteer een resourcegroep.|
    | **Exemplaardetails** |  |
    | Naam | Voer een naam in voor uw privé-eindpunt. Als deze naam wordt gebruikt, maakt u er een. |
    |Regio| Selecteer de regio waar u een persoonlijke koppeling wilt implementeren. Maak het persoonlijke eind punt op de locatie waar het virtuele netwerk zich bevindt.|
    |||
1. Selecteer **Volgende: Resource**.
1. Typ of selecteer in **Een privé-eindpunt maken – Resource** de volgende gegevens:

    | Instelling | Waarde |
    | ------- | ----- |
    |Verbindingsmethode  | Selecteer **Verbinding maken met een Azure-resource in mijn directory**. <br/><br/> U kunt vervolgens een van uw resources kiezen om een persoonlijke koppeling in te stellen. U kunt ook verbinding maken met de resource van iemand anders door een resource-ID of alias te gebruiken die met u is gedeeld.|
    | Abonnement| Selecteer uw abonnement. |
    | Resourcetype | Selecteer **micro soft. AzureCosmosDB/databaseAccounts**. |
    | Resource |Selecteer uw Azure Cosmos-account. |
    |Stel subresource in |Selecteer het Azure Cosmos DB-API-type dat u wilt toewijzen. Dit wordt standaard ingesteld op slechts één keuze voor de Api's SQL, MongoDB en Cassandra. Voor de Gremlin-en Table-Api's kunt u ook **SQL** kiezen omdat deze api's compatibel zijn met de SQL-API. |
    |||

1. Selecteer **Volgende: Configuratie**.
1. Voer in **een persoonlijk eind punt maken-configuratie** de volgende gegevens in of Selecteer deze:

    | Instelling | Waarde |
    | ------- | ----- |
    |**Netwerken**| |
    | Virtueel netwerk| Selecteer uw virtuele netwerk. |
    | Subnet | Selecteer uw subnet. |
    |**Integratie van Privé-DNS**||
    |Integreren met privé-DNS-zone |Selecteer **Ja**. <br><br/> Als u privé wilt verbinden met uw persoonlijke eind punt, moet u een DNS-record hebben. We raden u aan uw persoonlijke eind punt te integreren met een privé-DNS-zone. U kunt ook uw eigen DNS-servers gebruiken of DNS-records maken met behulp van de host-bestanden op uw virtuele machines. |
    |Privé-DNS-zone |Selecteer **privatelink.documents.Azure.com**. <br><br/> De privé-DNS-zone wordt automatisch bepaald. U kunt deze niet wijzigen met behulp van de Azure Portal.|
    |||

1. Selecteer **Controleren en maken**. Op de pagina **controleren en maken** valideert Azure uw configuratie.
1. Als u het bericht **Validatie geslaagd** ziet, selecteert u **Maken**.

Wanneer u een persoonlijke koppeling hebt goedgekeurd voor een Azure Cosmos-account, is de optie **alle netwerken** in het deel venster **firewall en virtuele netwerken** niet beschikbaar in de Azure Portal.

In de volgende tabel ziet u de toewijzing tussen de verschillende Azure Cosmos-account-API-typen, ondersteunde subresources en de bijbehorende namen van de persoonlijke zones. U kunt ook toegang krijgen tot de Gremlin-en Table-API-accounts via de SQL-API, zodat er twee vermeldingen voor deze Api's zijn.

|API-type voor Azure Cosmos-account  |Ondersteunde subbronnen (of groeps-Id's) |Naam van particuliere zone  |
|---------|---------|---------|
|SQL    |   SQL      | privatelink.documents.azure.com   |
|Cassandra    | Cassandra        |  privatelink.cassandra.cosmos.azure.com    |
|Mongo   |  MongoDB       |  privatelink.mongo.cosmos.azure.com    |
|Gremlin     | Gremlin        |  privatelink.gremlin.cosmos.azure.com   |
|Gremlin     |  SQL       |  privatelink.documents.azure.com    |
|Tabel    |    Tabel     |   privatelink.table.cosmos.azure.com    |
|Tabel     |   SQL      |  privatelink.documents.azure.com    |

### <a name="fetch-the-private-ip-addresses"></a>De privé-IP-adressen ophalen

Nadat het persoonlijke eind punt is ingericht, kunt u een query uitvoeren op de IP-adressen. De IP-adressen van de Azure Portal weer geven:

1. Selecteer **Alle resources**.
1. Zoek naar het persoonlijke eind punt dat u eerder hebt gemaakt. In dit geval is het **cdbPrivateEndpoint3**.
1. Selecteer het tabblad **overzicht** om de DNS-instellingen en IP-adressen te bekijken.

:::image type="content" source="./media/how-to-configure-private-endpoints/private-ip-addresses-portal.png" alt-text="Privé-IP-adressen in de Azure Portal":::

Er zijn meerdere IP-adressen gemaakt per particulier eind punt:

* Een voor het algemene eind punt (Region-neutraal) van het Azure Cosmos-account
* Eén voor elke regio waar het Azure Cosmos-account wordt geïmplementeerd

## <a name="create-a-private-endpoint-by-using-azure-powershell"></a>Een persoonlijk eind punt maken met behulp van Azure PowerShell

Voer het volgende Power shell-script uit om een persoonlijk eind punt met de naam ' MyPrivateEndpoint ' te maken voor een bestaand Azure Cosmos-account. Vervang de variabele waarden door de gegevens voor uw omgeving.

```azurepowershell-interactive
$SubscriptionId = "<your Azure subscription ID>"
# Resource group where the Azure Cosmos account and virtual network resources are located
$ResourceGroupName = "myResourceGroup"
# Name of the Azure Cosmos account
$CosmosDbAccountName = "mycosmosaccount"

# API type of the Azure Cosmos account: Sql, MongoDB, Cassandra, Gremlin, or Table
$CosmosDbApiType = "Sql"
# Name of the existing virtual network
$VNetName = "myVnet"
# Name of the target subnet in the virtual network
$SubnetName = "mySubnet"
# Name of the private endpoint to create
$PrivateEndpointName = "MyPrivateEndpoint"
# Location where the private endpoint can be created. The private endpoint should be created in the same location where your subnet or the virtual network exists
$Location = "westcentralus"

$cosmosDbResourceId = "/subscriptions/$($SubscriptionId)/resourceGroups/$($ResourceGroupName)/providers/Microsoft.DocumentDB/databaseAccounts/$($CosmosDbAccountName)"

$privateEndpointConnection = New-AzPrivateLinkServiceConnection -Name "myConnectionPS" -PrivateLinkServiceId $cosmosDbResourceId -GroupId $CosmosDbApiType
 
$virtualNetwork = Get-AzVirtualNetwork -ResourceGroupName  $ResourceGroupName -Name $VNetName  
 
$subnet = $virtualNetwork | Select -ExpandProperty subnets | Where-Object  {$_.Name -eq $SubnetName}  
 
$privateEndpoint = New-AzPrivateEndpoint -ResourceGroupName $ResourceGroupName -Name $PrivateEndpointName -Location "westcentralus" -Subnet  $subnet -PrivateLinkServiceConnection $privateEndpointConnection
```

### <a name="integrate-the-private-endpoint-with-a-private-dns-zone"></a>Het persoonlijke eind punt integreren met een privé-DNS-zone

Nadat u het persoonlijke eind punt hebt gemaakt, kunt u het integreren met een privé-DNS-zone met behulp van het volgende Power shell-script:

```azurepowershell-interactive
Import-Module Az.PrivateDns
$zoneName = "privatelink.documents.azure.com"
$zone = New-AzPrivateDnsZone -ResourceGroupName $ResourceGroupName `
  -Name $zoneName

$link  = New-AzPrivateDnsVirtualNetworkLink -ResourceGroupName $ResourceGroupName `
  -ZoneName $zoneName `
  -Name "myzonelink" `
  -VirtualNetworkId $virtualNetwork.Id  
 
$pe = Get-AzPrivateEndpoint -Name $PrivateEndpointName `
  -ResourceGroupName $ResourceGroupName

$networkInterface = Get-AzResource -ResourceId $pe.NetworkInterfaces[0].Id `
  -ApiVersion "2019-04-01"
 
foreach ($ipconfig in $networkInterface.properties.ipConfigurations) { 
foreach ($fqdn in $ipconfig.properties.privateLinkConnectionProperties.fqdns) { 
Write-Host "$($ipconfig.properties.privateIPAddress) $($fqdn)"  
$recordName = $fqdn.split('.',2)[0] 
$dnsZone = $fqdn.split('.',2)[1] 
New-AzPrivateDnsRecordSet -Name $recordName `
  -RecordType A -ZoneName $zoneName  `
  -ResourceGroupName $ResourceGroupName -Ttl 600 `
  -PrivateDnsRecords (New-AzPrivateDnsRecordConfig `
  -IPv4Address $ipconfig.properties.privateIPAddress)  
}
}
```

### <a name="fetch-the-private-ip-addresses"></a>De privé-IP-adressen ophalen

Nadat het persoonlijke eind punt is ingericht, kunt u een query uitvoeren op de IP-adressen en de FQDN-toewijzing met behulp van het volgende Power shell-script:

```azurepowershell-interactive
$pe = Get-AzPrivateEndpoint -Name MyPrivateEndpoint -ResourceGroupName myResourceGroup
$networkInterface = Get-AzNetworkInterface -ResourceId $pe.NetworkInterfaces[0].Id
foreach ($IPConfiguration in $networkInterface.IpConfigurations)
{
    Write-Host $IPConfiguration.PrivateIpAddress ":" $IPConfiguration.PrivateLinkConnectionProperties.Fqdns
}
```

## <a name="create-a-private-endpoint-by-using-azure-cli"></a>Een persoonlijk eind punt maken met behulp van Azure CLI

Voer het volgende Azure CLI-script uit om een persoonlijk eind punt met de naam ' myPrivateEndpoint ' te maken voor een bestaand Azure Cosmos-account. Vervang de variabele waarden door de gegevens voor uw omgeving.

```azurecli-interactive
# Resource group where the Azure Cosmos account and virtual network resources are located
ResourceGroupName="myResourceGroup"

# Subscription ID where the Azure Cosmos account and virtual network resources are located
SubscriptionId="<your Azure subscription ID>"

# Name of the existing Azure Cosmos account
CosmosDbAccountName="mycosmosaccount"

# API type of your Azure Cosmos account: Sql, MongoDB, Cassandra, Gremlin, or Table
CosmosDbApiType="Sql"

# Name of the virtual network to create
VNetName="myVnet"

# Name of the subnet to create
SubnetName="mySubnet"

# Name of the private endpoint to create
PrivateEndpointName="myPrivateEndpoint"

# Name of the private endpoint connection to create
PrivateConnectionName="myConnection"

az network vnet create \
 --name $VNetName \
 --resource-group $ResourceGroupName \
 --subnet-name $SubnetName

az network vnet subnet update \
 --name $SubnetName \
 --resource-group $ResourceGroupName \
 --vnet-name $VNetName \
 --disable-private-endpoint-network-policies true

az network private-endpoint create \
    --name $PrivateEndpointName \
    --resource-group $ResourceGroupName \
    --vnet-name $VNetName  \
    --subnet $SubnetName \
    --private-connection-resource-id "/subscriptions/$SubscriptionId/resourceGroups/$ResourceGroupName/providers/Microsoft.DocumentDB/databaseAccounts/$CosmosDbAccountName" \
    --group-ids $CosmosDbApiType \
    --connection-name $PrivateConnectionName
```

### <a name="integrate-the-private-endpoint-with-a-private-dns-zone"></a>Het persoonlijke eind punt integreren met een privé-DNS-zone

Nadat u het persoonlijke eind punt hebt gemaakt, kunt u het integreren met een privé-DNS-zone met behulp van het volgende Azure CLI-script:

```azurecli-interactive
zoneName="privatelink.documents.azure.com"

az network private-dns zone create --resource-group $ResourceGroupName \
   --name  $zoneName

az network private-dns link vnet create --resource-group $ResourceGroupName \
   --zone-name  $zoneName\
   --name myzonelink \
   --virtual-network $VNetName \
   --registration-enabled false 

#Query for the network interface ID  
networkInterfaceId=$(az network private-endpoint show --name $PrivateEndpointName --resource-group $ResourceGroupName --query 'networkInterfaces[0].id' -o tsv)
 
# Copy the content for privateIPAddress and FQDN matching the Azure Cosmos account 
az resource show --ids $networkInterfaceId --api-version 2019-04-01 -o json 
 
#Create DNS records 
az network private-dns record-set a create --name recordSet1 --zone-name privatelink.documents.azure.com --resource-group $ResourceGroupName
az network private-dns record-set a add-record --record-set-name recordSet2 --zone-name privatelink.documents.azure.com --resource-group $ResourceGroupName -a <Private IP Address>
```

## <a name="create-a-private-endpoint-by-using-a-resource-manager-template"></a>Een persoonlijk eind punt maken met behulp van een resource manager-sjabloon

U kunt een persoonlijke koppeling instellen door een persoonlijk eind punt te maken in een subnet van een virtueel netwerk. U kunt dit doen met behulp van een Azure Resource Manager sjabloon.

Gebruik de volgende code om een resource manager-sjabloon met de naam ' PrivateEndpoint_template.jsop ' te maken. Met deze sjabloon maakt u een persoonlijk eind punt voor een bestaand Azure Cosmos SQL API-account in een bestaand virtueel netwerk.

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "location": {
          "type": "string",
          "defaultValue": "[resourceGroup().location]",
          "metadata": {
            "description": "Location for all resources."
          }
        },
        "privateEndpointName": {
            "type": "string"
        },
        "resourceId": {
            "type": "string"
        },
        "groupId": {
            "type": "string"
        },
        "subnetId": {
            "type": "string"
        }
    },
    "resources": [
        {
            "name": "[parameters('privateEndpointName')]",
            "type": "Microsoft.Network/privateEndpoints",
            "apiVersion": "2019-04-01",
            "location": "[parameters('location')]",
            "properties": {
                "subnet": {
                    "id": "[parameters('subnetId')]"
                },
                "privateLinkServiceConnections": [
                    {
                        "name": "MyConnection",
                        "properties": {
                            "privateLinkServiceId": "[parameters('resourceId')]",
                            "groupIds": [ "[parameters('groupId')]" ],
                            "requestMessage": ""
                        }
                    }
                ]
            }
        }
    ],
    "outputs": {
        "privateEndpointNetworkInterface": {
          "type": "string",
          "value": "[reference(concat('Microsoft.Network/privateEndpoints/', parameters('privateEndpointName'))).networkInterfaces[0].id]"
        }
    }
}
```

**Definieer het parameter bestand voor de sjabloon**

Maak een parameter bestand voor de sjabloon en noem het ' PrivateEndpoint_parameters.jsaan '. Voeg de volgende code toe aan het parameter bestand:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "privateEndpointName": {
            "value": ""
        },
        "resourceId": {
            "value": ""
        },
        "groupId": {
            "value": ""
        },
        "subnetId": {
            "value": ""
        }
    }
}
```

**De sjabloon implementeren met behulp van een Power shell-script**

Maak een Power shell-script met de volgende code. Voordat u het script uitvoert, moet u de abonnements-ID, de naam van de resource groep en andere variabelen waarden vervangen door de gegevens voor uw omgeving.

```azurepowershell-interactive
### This script creates a private endpoint for an existing Azure Cosmos account in an existing virtual network

## Step 1: Fill in these details. Replace the variable values with the details for your environment.
$SubscriptionId = "<your Azure subscription ID>"
# Resource group where the Azure Cosmos account and virtual network resources are located
$ResourceGroupName = "myResourceGroup"
# Name of the Azure Cosmos account
$CosmosDbAccountName = "mycosmosaccount"
# API type of the Azure Cosmos account. It can be one of the following: "Sql", "MongoDB", "Cassandra", "Gremlin", "Table"
$CosmosDbApiType = "Sql"
# Name of the existing virtual network
$VNetName = "myVnet"
# Name of the target subnet in the virtual network
$SubnetName = "mySubnet"
# Name of the private endpoint to create
$PrivateEndpointName = "myPrivateEndpoint"

$cosmosDbResourceId = "/subscriptions/$($SubscriptionId)/resourceGroups/$($ResourceGroupName)/providers/Microsoft.DocumentDB/databaseAccounts/$($CosmosDbAccountName)"
$VNetResourceId = "/subscriptions/$($SubscriptionId)/resourceGroups/$($ResourceGroupName)/providers/Microsoft.Network/virtualNetworks/$($VNetName)"
$SubnetResourceId = "$($VNetResourceId)/subnets/$($SubnetName)"
$PrivateEndpointTemplateFilePath = "PrivateEndpoint_template.json"
$PrivateEndpointParametersFilePath = "PrivateEndpoint_parameters.json"

## Step 2: Sign in to your Azure account and select the target subscription.
Login-AzAccount
Select-AzSubscription -SubscriptionId $subscriptionId

## Step 3: Make sure private endpoint network policies are disabled in the subnet.
$VirtualNetwork= Get-AzVirtualNetwork -Name "$VNetName" -ResourceGroupName "$ResourceGroupName"
($virtualNetwork | Select -ExpandProperty subnets | Where-Object  {$_.Name -eq "$SubnetName"} ).PrivateEndpointNetworkPolicies = "Disabled"
$virtualNetwork | Set-AzVirtualNetwork

## Step 4: Create the private endpoint.
Write-Output "Deploying private endpoint on $($resourceGroupName)"
$deploymentOutput = New-AzResourceGroupDeployment -Name "PrivateCosmosDbEndpointDeployment" `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile $PrivateEndpointTemplateFilePath `
    -TemplateParameterFile $PrivateEndpointParametersFilePath `
    -SubnetId $SubnetResourceId `
    -ResourceId $CosmosDbResourceId `
    -GroupId $CosmosDbApiType `
    -PrivateEndpointName $PrivateEndpointName

$deploymentOutput
```

In het Power shell-script `GroupId` mag de variabele slechts één waarde bevatten. Deze waarde is het API-type van het account. Toegestane waarden zijn: `Sql` , `MongoDB` , `Cassandra` , en `Gremlin` `Table` . Sommige Azure Cosmos-account typen zijn toegankelijk via meerdere Api's. Bijvoorbeeld:

* U kunt toegang krijgen tot een Gremlin-API-account vanuit zowel de Gremlin-als SQL-API-accounts.
* Een Table-API account kan worden geopend vanuit zowel de tabel-als de SQL-API-accounts.

Voor deze accounts moet u één persoonlijk eind punt maken voor elk API-type. Het bijbehorende API-type is opgegeven in de `GroupId` matrix.

Nadat de sjabloon is geïmplementeerd, kunt u een uitvoer zien die vergelijkbaar is met wat er in de volgende afbeelding wordt weer gegeven. De `provisioningState` waarde is `Succeeded` als de persoonlijke eind punten correct zijn ingesteld.

:::image type="content" source="./media/how-to-configure-private-endpoints/resource-manager-template-deployment-output.png" alt-text="Implementatie-uitvoer voor de Resource Manager-sjabloon":::

Nadat de sjabloon is geïmplementeerd, worden de privé-IP-adressen in het subnet gereserveerd. De firewall regel van het Azure Cosmos-account is geconfigureerd om alleen verbindingen van het privé-eind punt te accepteren.

### <a name="integrate-the-private-endpoint-with-a-private-dns-zone"></a>Het persoonlijke eind punt integreren met een Privé-DNS zone

Gebruik de volgende code om een resource manager-sjabloon met de naam ' PrivateZone_template.jsop ' te maken. Met deze sjabloon maakt u een privé-DNS-zone voor een bestaand Azure Cosmos SQL API-account in een bestaand virtueel netwerk.

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "privateZoneName": {
            "type": "string"
        },
        "VNetId": {
            "type": "string"
        }        
    },
    "resources": [
        {
            "name": "[parameters('privateZoneName')]",
            "type": "Microsoft.Network/privateDnsZones",
            "apiVersion": "2018-09-01",
            "location": "global",
            "properties": {                
            }
        },
        {
            "type": "Microsoft.Network/privateDnsZones/virtualNetworkLinks",
            "apiVersion": "2018-09-01",
            "name": "[concat(parameters('privateZoneName'), '/myvnetlink')]",
            "location": "global",
            "dependsOn": [
                "[resourceId('Microsoft.Network/privateDnsZones', parameters('privateZoneName'))]"
            ],
            "properties": {
                "registrationEnabled": false,
                "virtualNetwork": {
                    "id": "[parameters('VNetId')]"
                }
            }
        }        
    ]
}
```

Gebruik de volgende code om een resource manager-sjabloon met de naam ' PrivateZoneRecords_template.jsop ' te maken.

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "DNSRecordName": {
            "type": "string"
        },
        "IPAddress": {
            "type":"string"
        }        
    },
    "resources": [
         {
            "type": "Microsoft.Network/privateDnsZones/A",
            "apiVersion": "2018-09-01",
            "name": "[parameters('DNSRecordName')]",
            "properties": {
                "ttl": 300,
                "aRecords": [
                    {
                        "ipv4Address": "[parameters('IPAddress')]"
                    }
                ]
            }
        }    
    ]
}
```

**Definieer het parameter bestand voor de sjabloon**

Maak het volgende twee parameter bestand voor de sjabloon. Maak de PrivateZone_parameters.jsop. met de volgende code:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "privateZoneName": {
            "value": ""
        },
        "VNetId": {
            "value": ""
        }
    }
}
```

Maak de PrivateZoneRecords_parameters.jsop. met de volgende code:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "DNSRecordName": {
            "value": ""
        },
        "IPAddress": {
            "type":"object"
        }
    }
}
```

**De sjabloon implementeren met behulp van een Power shell-script**

Maak een Power shell-script met de volgende code. Voordat u het script uitvoert, moet u de abonnements-ID, de naam van de resource groep en andere variabelen waarden vervangen door de gegevens voor uw omgeving.

```azurepowershell-interactive
### This script:
### - creates a private zone
### - creates a private endpoint for an existing Cosmos DB account in an existing VNet
### - maps the private endpoint to the private zone

## Step 1: Fill in these details. Replace the variable values with the details for your environment.
$SubscriptionId = "<your Azure subscription ID>"
# Resource group where the Azure Cosmos account and virtual network resources are located
$ResourceGroupName = "myResourceGroup"
# Name of the Azure Cosmos account
$CosmosDbAccountName = "mycosmosaccount"
# API type of the Azure Cosmos account. It can be one of the following: "Sql", "MongoDB", "Cassandra", "Gremlin", "Table"
$CosmosDbApiType = "Sql"
# Name of the existing virtual network
$VNetName = "myVnet"
# Name of the target subnet in the virtual network
$SubnetName = "mySubnet"
# Name of the private zone to create
$PrivateZoneName = "myPrivateZone.documents.azure.com"
# Name of the private endpoint to create
$PrivateEndpointName = "myPrivateEndpoint"

$cosmosDbResourceId = "/subscriptions/$($SubscriptionId)/resourceGroups/$($ResourceGroupName)/providers/Microsoft.DocumentDB/databaseAccounts/$($CosmosDbAccountName)"
$VNetResourceId = "/subscriptions/$($SubscriptionId)/resourceGroups/$($ResourceGroupName)/providers/Microsoft.Network/virtualNetworks/$($VNetName)"
$SubnetResourceId = "$($VNetResourceId)/subnets/$($SubnetName)"
$PrivateZoneTemplateFilePath = "PrivateZone_template.json"
$PrivateZoneParametersFilePath = "PrivateZone_parameters.json"
$PrivateZoneRecordsTemplateFilePath = "PrivateZoneRecords_template.json"
$PrivateZoneRecordsParametersFilePath = "PrivateZoneRecords_parameters.json"
$PrivateEndpointTemplateFilePath = "PrivateEndpoint_template.json"
$PrivateEndpointParametersFilePath = "PrivateEndpoint_parameters.json"

## Step 2: Login your Azure account and select the target subscription
Login-AzAccount 
Select-AzSubscription -SubscriptionId $subscriptionId

## Step 3: Make sure private endpoint network policies are disabled in the subnet
$VirtualNetwork= Get-AzVirtualNetwork -Name "$VNetName" -ResourceGroupName "$ResourceGroupName"
($virtualNetwork | Select -ExpandProperty subnets | Where-Object  {$_.Name -eq "$SubnetName"} ).PrivateEndpointNetworkPolicies = "Disabled"
$virtualNetwork | Set-AzVirtualNetwork

## Step 4: Create the private zone
New-AzResourceGroupDeployment -Name "PrivateZoneDeployment" `
    -ResourceGroupName $ResourceGroupName `
    -TemplateFile $PrivateZoneTemplateFilePath `
    -TemplateParameterFile $PrivateZoneParametersFilePath `
    -PrivateZoneName $PrivateZoneName `
    -VNetId $VNetResourceId

## Step 5: Create the private endpoint
Write-Output "Deploying private endpoint on $($resourceGroupName)"
$deploymentOutput = New-AzResourceGroupDeployment -Name "PrivateCosmosDbEndpointDeployment" `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile $PrivateEndpointTemplateFilePath `
    -TemplateParameterFile $PrivateEndpointParametersFilePath `
    -SubnetId $SubnetResourceId `
    -ResourceId $CosmosDbResourceId `
    -GroupId $CosmosDbApiType `
    -PrivateEndpointName $PrivateEndpointName
$deploymentOutput

## Step 6: Map the private endpoint to the private zone
$networkInterface = Get-AzResource -ResourceId $deploymentOutput.Outputs.privateEndpointNetworkInterface.Value -ApiVersion "2019-04-01"
foreach ($ipconfig in $networkInterface.properties.ipConfigurations) {
    foreach ($fqdn in $ipconfig.properties.privateLinkConnectionProperties.fqdns) {
        $recordName = $fqdn.split('.',2)[0]
        $dnsZone = $fqdn.split('.',2)[1]
        Write-Output "Deploying PrivateEndpoint DNS Record $($PrivateZoneName)/$($recordName) Template on $($resourceGroupName)"
        New-AzResourceGroupDeployment -Name "PrivateEndpointDNSDeployment" `
            -ResourceGroupName $ResourceGroupName `
            -TemplateFile $PrivateZoneRecordsTemplateFilePath `
            -TemplateParameterFile $PrivateZoneRecordsParametersFilePath `
            -DNSRecordName "$($PrivateZoneName)/$($RecordName)" `
            -IPAddress $ipconfig.properties.privateIPAddress
    }
}
```

## <a name="configure-custom-dns"></a>Aangepaste DNS configureren

U moet een privé-DNS-zone gebruiken in het subnet waar u het persoonlijke eind punt hebt gemaakt. Configureer de eind punten zodat elk privé-IP-adres wordt toegewezen aan een DNS-vermelding. (Zie de `fqdns` eigenschap in het antwoord dat eerder is weer gegeven.)

Wanneer u het persoonlijke eind punt maakt, kunt u het integreren met een privé-DNS-zone in Azure. Als u in plaats daarvan een aangepaste DNS-zone wilt gebruiken, moet u deze configureren voor het toevoegen van DNS-records voor alle privé-IP-adressen die zijn gereserveerd voor het persoonlijke eind punt.

> [!IMPORTANT]
> Het is de DNS-omzetting van uw aanvragen die bepaalt of deze aanvragen worden verzonden via uw privé-eind punten of de standaard open bare route nemen. Zorg ervoor dat uw lokale DNS op de juiste wijze verwijst naar het privé IP-adres dat is toegewezen door uw persoonlijke eind punt.

## <a name="private-link-combined-with-firewall-rules"></a>Privé koppeling gecombineerd met firewall regels

De volgende situaties en resultaten zijn mogelijk wanneer u een persoonlijke koppeling gebruikt in combi natie met firewall regels:

* Als u geen firewall regels configureert, kan standaard al het verkeer toegang krijgen tot een Azure Cosmos-account.

* Als u openbaar verkeer of een service-eind punt configureert en u persoonlijke eind punten maakt, worden verschillende soorten binnenkomend verkeer geautoriseerd door het bijbehorende type firewall regel. Als een persoonlijk eind punt is geconfigureerd in een subnet waar service-eind punt ook is geconfigureerd:
  * verkeer naar het database account dat is toegewezen door het persoonlijke eind punt, wordt doorgestuurd via een particulier eind punt,
  * verkeer naar andere database accounts van het subnet wordt doorgestuurd via service-eind punt.

* Als u geen openbaar verkeer of service-eind punt configureert en u persoonlijke eind punten maakt, is het Azure Cosmos-account alleen toegankelijk via de persoonlijke eind punten. Als u geen openbaar verkeer of een service-eind punt configureert nadat alle goedgekeurde persoonlijke eind punten zijn afgewezen of verwijderd, is het account geopend voor het hele netwerk, tenzij PublicNetworkAccess is ingesteld op uitgeschakeld (Zie de sectie hieronder).

## <a name="blocking-public-network-access-during-account-creation"></a>Toegang tot open bare netwerken blok keren tijdens het maken van het account

Zoals beschreven in de vorige sectie, en tenzij er specifieke firewall regels zijn ingesteld, is het toevoegen van een persoonlijk eind punt dat uw Azure Cosmos-account alleen toegankelijk is via privé-eind punten. Dit betekent dat het Azure Cosmos-account kan worden bereikt vanuit het open bare verkeer nadat het is gemaakt en voordat een persoonlijk eind punt wordt toegevoegd. Als u er zeker van wilt zijn dat open bare netwerk toegang is uitgeschakeld, zelfs vóór het maken van privé-eind punten, kunt u de `publicNetworkAccess` markering instellen op tijdens het maken van het `Disabled` account. Houd er rekening mee dat deze vlag voor rang heeft boven een regel voor het IP-of virtueel netwerk. alle open bare en virtuele netwerk verkeer wordt geblokkeerd wanneer de vlag is ingesteld op `Disabled` , zelfs als de bron-IP of het virtuele netwerk is toegestaan in de firewall configuratie.

Zie [deze Azure Resource Manager sjabloon](https://azure.microsoft.com/resources/templates/101-cosmosdb-private-endpoint/) voor een voor beeld waarin wordt getoond hoe u deze vlag gebruikt.

## <a name="adding-private-endpoints-to-an-existing-cosmos-account-with-no-downtime"></a>Privé-eind punten toevoegen aan een bestaand Cosmos-account zonder downtime

Standaard resulteert het toevoegen van een persoonlijk eind punt aan een bestaand account in een korte downtime van ongeveer 5 minuten. Volg de onderstaande instructies om deze downtime te voor komen:

1. Voeg regels voor IP-of virtueel netwerk aan uw firewall configuratie toe om uw client verbindingen expliciet toe te staan.
1. Wacht tien minuten om er zeker van te zijn dat de configuratie-update wordt toegepast.
1. Configureer uw nieuwe persoonlijke eind punt.
1. Verwijder de firewall regels die bij stap 1 zijn ingesteld.

## <a name="port-range-when-using-direct-mode"></a>Poort bereik bij gebruik van directe modus

Wanneer u een persoonlijke koppeling met een Azure Cosmos-account via een directe modus verbinding gebruikt, moet u ervoor zorgen dat het volledige bereik van TCP-poorten (0-65535) is geopend.

## <a name="update-a-private-endpoint-when-you-add-or-remove-a-region"></a>Een persoonlijk eind punt bijwerken wanneer u een regio toevoegt of verwijdert

Tenzij u een privé-DNS-zone groep gebruikt, voegt u voor het toevoegen of verwijderen van regio's een Azure Cosmos-account toe om DNS-vermeldingen toe te voegen aan of te verwijderen uit dat account. Nadat er regio's zijn toegevoegd of verwijderd, kunt u de privé-DNS-zone van het subnet bijwerken om de toegevoegde of verwijderde DNS-vermeldingen en de bijbehorende privé-IP-adressen weer te geven.

Stel bijvoorbeeld dat u een Azure Cosmos-account in drie regio's implementeert: "VS-West," "centraal VS" en "Europa-west". Wanneer u een persoonlijk eind punt voor uw account maakt, worden vier privé Ip's gereserveerd in het subnet. Er is één IP-adres voor elk van de drie regio's en er is één IP-adres voor het neutraal-eind punt globaal/regio.

U kunt later een nieuwe regio (bijvoorbeeld ' vs Oost ') toevoegen aan het Azure Cosmos-account. Nadat u de nieuwe regio hebt toegevoegd, moet u een bijbehorende DNS-record toevoegen aan uw privé-DNS-zone of uw aangepaste DNS.

U kunt dezelfde stappen gebruiken wanneer u een regio verwijdert. Nadat u de regio hebt verwijderd, moet u de bijbehorende DNS-record verwijderen uit uw privé-DNS-zone of uw aangepaste DNS.

## <a name="current-limitations"></a>Huidige beperkingen

De volgende beperkingen zijn van toepassing wanneer u een privé-verbinding met een Azure Cosmos-account gebruikt:

* U kunt niet meer dan 200 persoonlijke eind punten hebben op één Azure Cosmos-account.

* Wanneer u een persoonlijke koppeling met een Azure Cosmos-account via een directe modus verbinding gebruikt, kunt u alleen het TCP-protocol gebruiken. Het HTTP-protocol wordt momenteel niet ondersteund.

* Wanneer u de API van Azure Cosmos DB gebruikt voor MongoDB-accounts, wordt een persoonlijk eind punt alleen ondersteund voor accounts op Server versie 3,6 (dat wil zeggen, accounts die gebruikmaken van het eind punt in de indeling `*.mongo.cosmos.azure.com` ). Privé koppeling wordt niet ondersteund voor accounts op Server versie 3,2 (dat wil zeggen, accounts die gebruikmaken van het eind punt in de indeling `*.documents.azure.com` ). Als u een persoonlijke koppeling wilt gebruiken, moet u oude accounts migreren naar de nieuwe versie.

* Wanneer u de API van een Azure Cosmos DB gebruikt voor een MongoDB-account met een persoonlijke koppeling, werken sommige hulpprogram ma's of bibliotheken mogelijk niet wanneer ze de `appName` para meter automatisch uit de Connection String verwijderen. Deze para meter is vereist om verbinding te maken met het account via een persoonlijk eind punt. Sommige hulpprogram ma's, zoals Visual Studio code, verwijderen deze para meter niet uit de connection string en zijn daarom compatibel.

* Aan een netwerk beheerder moet ten minste de `Microsoft.DocumentDB/databaseAccounts/PrivateEndpointConnectionsApproval/action` machtiging voor het bereik van het Azure Cosmos-account worden verleend om automatisch goedgekeurde privé-eind punten te maken.

### <a name="limitations-to-private-dns-zone-integration"></a>Beperkingen voor de integratie van particuliere DNS-zones

Tenzij u een privé-DNS-zone groep gebruikt, worden DNS-records in de privé-DNS-zone niet automatisch verwijderd wanneer u een persoonlijk eind punt verwijdert of een regio verwijdert uit het Azure Cosmos-account. U moet de DNS-records hand matig verwijderen voordat:

* Het toevoegen van een nieuw persoonlijk eind punt dat is gekoppeld aan deze privé-DNS-zone.
* Het toevoegen van een nieuwe regio aan een database account met persoonlijke eind punten die zijn gekoppeld aan deze privé-DNS-zone.

Als u de DNS-records niet opschoont, kunnen er onverwachte problemen met het gegevens vlak optreden. Deze problemen bevatten gegevens onderbrekingen voor regio's die zijn toegevoegd na het verwijderen van het privé-eind punt of het verwijderen van de regio.

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de volgende artikelen voor meer informatie over Azure Cosmos DB beveiligings functies:

* Zie [firewall ondersteuning](how-to-configure-firewall.md)als u een firewall wilt configureren voor Azure Cosmos db.

* Zie [toegang vanaf virtuele netwerken configureren](how-to-configure-vnet-service-endpoint.md)voor meer informatie over het configureren van een service-eind punt voor een virtueel netwerk voor uw Azure Cosmos-account.

* Zie de documentatie van [Azure private link](../private-link/private-link-overview.md) voor meer informatie over persoonlijke koppelingen.
