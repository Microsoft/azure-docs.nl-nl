---
title: Privé-eindpunten configureren voor Azure Event Grid onderwerpen of domeinen
description: In dit artikel wordt beschreven hoe u privé-eindpunten configureert voor Azure Event Grid onderwerpen of domein.
ms.topic: how-to
ms.date: 11/18/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 85546e99a8c431dc75b1af3d5044e06a18cf226d
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107770503"
---
# <a name="configure-private-endpoints-for-azure-event-grid-topics-or-domains"></a>Privé-eindpunten configureren voor Azure Event Grid onderwerpen of domeinen
U kunt [privé-eindpunten](../private-link/private-endpoint-overview.md) gebruiken om toegang tot gebeurtenissen rechtstreeks vanuit uw virtuele netwerk [](../private-link/private-link-overview.md) naar uw onderwerpen en domeinen veilig via een privékoppeling toe te staan zonder via het openbare internet te gaan. Het privé-eindpunt gebruikt een IP-adres uit de VNet-adresruimte voor uw onderwerp of domein. Zie Netwerkbeveiliging voor meer [conceptuele informatie.](network-security.md)

In dit artikel wordt beschreven hoe u privé-eindpunten configureert voor onderwerpen of domeinen.

## <a name="use-azure-portal"></a>Azure Portal gebruiken 
In deze sectie ziet u hoe u de Azure Portal om een privé-eindpunt voor een onderwerp of een domein te maken.

> [!NOTE]
> De stappen in deze sectie zijn voornamelijk voor onderwerpen. U kunt vergelijkbare stappen gebruiken om privé-eindpunten voor **domeinen te maken.** 

1. Meld u aan bij [Azure Portal](https://portal.azure.com) en navigeer naar uw onderwerp of domein.
2. Ga naar het **tabblad** Netwerken van de onderwerppagina. Selecteer **+ Privé-eindpunt op** de werkbalk.

    ![Privé-eindpunt toevoegen](./media/configure-private-endpoints/add-button.png)
2. Volg deze **stappen** op de pagina Basisinformatie: 
    1. Selecteer een **Azure-abonnement** waarin u het privé-eindpunt wilt maken. 
    2. Selecteer een **Azure-resourcegroep** voor het privé-eindpunt. 
    3. Voer een **naam** in voor het eindpunt. 
    4. Selecteer de **regio** voor het eindpunt. Uw privé-eindpunt moet zich in dezelfde regio als uw virtuele netwerk, maar kan zich in een andere regio dan de private link-resource (in dit voorbeeld een event grid-onderwerp) in. 
    5. Selecteer vervolgens **Volgende: Resource >** onder aan de pagina. 

      ![Privé-eindpunt - basispagina](./media/configure-private-endpoints/basics-page.png)
3. Volg deze **stappen** op de pagina Resource: 
    1. Als u voor de verbindingsmethode Verbinding maken met **een Azure-resource in mijn directory selecteert,** volgt u deze stappen. In dit voorbeeld ziet u hoe u verbinding maakt met een Azure-resource in uw directory. 
        1. Selecteer het **Azure-abonnement** waarin uw **onderwerp/domein** bestaat. 
        1. Bij **Resourcetype** selecteert **u Microsoft.EventGrid/topics** of **Microsoft.EventGrid/domains** als **resourcetype.**
        2. Selecteer **voor Resource** een onderwerp/domein in de vervolgkeuzelijst. 
        3. Controleer of de **doelsubresource** is ingesteld op **onderwerp** of **domein** (op basis van het resourcetype dat u hebt geselecteerd).    
        4. Selecteer **Volgende: configuratie >** onder aan de pagina. 

            ![Schermopname van de pagina 'Een privé-eindpunt maken - Resource'.](./media/configure-private-endpoints/resource-page.png)
    2. Als u Verbinding **maken met een resource selecteert met behulp van een resource-id of een alias**, volgt u deze stappen:
        1. Voer de id van de resource in. Bijvoorbeeld: `/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>`.  
        2. Voer **bij Resource** het onderwerp **of** domein **in.** 
        3. (optioneel) Voeg een aanvraagbericht toe. 
        4. Selecteer **Volgende: configuratie >** onder aan de pagina. 

            ![Privé-eindpunt - resourcepagina](./media/configure-private-endpoints/connect-azure-resource-id.png)
4. Op de **pagina** Configuratie selecteert u het subnet in een virtueel netwerk waar u het privé-eindpunt wilt implementeren. 
    1. Selecteer een **virtueel netwerk.** Alleen virtuele netwerken in het momenteel geselecteerde abonnement en de geselecteerde locatie worden weergegeven in de vervolgkeuzelijst. 
    2. Selecteer een **subnet** in het virtuele netwerk dat u hebt geselecteerd. 
    3. Selecteer **Volgende: Tags >** knop onderaan de pagina. 

    ![Privé-eindpunt - configuratiepagina](./media/configure-private-endpoints/configuration-page.png)
5. Maak op **de** pagina Tags tags (namen en waarden) die u wilt koppelen aan de privé-eindpuntresource. Selecteer vervolgens **de knop Beoordelen en** maken onderaan de pagina. 
6. Controleer in **Beoordelen en maken** alle instellingen en selecteer Maken **om** het privé-eindpunt te maken. 

    ![Privé-eindpunt - pagina & controleren](./media/configure-private-endpoints/review-create-page.png)
    

### <a name="manage-private-link-connection"></a>Verbinding met private link beheren

Wanneer u een privé-eindpunt maakt, moet de verbinding worden goedgekeurd. Als de resource waarvoor u een privé-eindpunt maakt, zich in uw directory heeft, kunt u de verbindingsaanvraag goedkeuren op voorwaarde dat u voldoende machtigingen hebt. Als u verbinding maakt met een Azure-resource in een andere directory, moet u wachten tot de eigenaar van die resource uw verbindingsaanvraag heeft goedgekeurd.

Er zijn vier inrichtingsstatussen:

| Serviceactie | Status privé-eindpunt serviceconsument | Beschrijving |
|--|--|--|
| Geen | In behandeling | De verbinding wordt handmatig gemaakt en is in afwachting van goedkeuring van de eigenaar van de Private Link-resource. |
| Goedkeuren | Goedgekeurd | De verbinding werd automatisch of handmatig goedgekeurd en is klaar om te worden gebruikt. |
| Afwijzen | Afgewezen | De verbinding werd afgewezen door de resource-eigenaar van de private link. |
| Verwijderen | Ontkoppeld | De verbinding is verwijderd door de resource-eigenaar van de private link, het privé-eindpunt wordt informatief en moet worden verwijderd voor opschoning. |
 
###  <a name="how-to-manage-a-private-endpoint-connection"></a>Een privé-eindpuntverbinding beheren
In de volgende secties ziet u hoe u een privé-eindpuntverbinding goedkeurt of weigert. 

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
1. Typ in de zoekbalk Event Grid **onderwerpen** of **Event Grid domeinen.**
1. Selecteer het **onderwerp** **of domein** dat u wilt beheren.
1. Selecteer het tabblad **Netwerken**.
1. Als er verbindingen zijn die in behandeling zijn, ziet u een verbinding met In behandeling **in** de inrichtingsstaat. 

### <a name="to-approve-a-private-endpoint"></a>Een privé-eindpunt goedkeuren
U kunt een privé-eindpunt met de status In behandeling goedkeuren. Volg deze stappen om dit goed te keuren: 

> [!NOTE]
> De stappen in deze sectie zijn voornamelijk voor onderwerpen. U kunt vergelijkbare stappen gebruiken om privé-eindpunten voor domeinen **goed te keuren.** 

1. Selecteer het **privé-eindpunt dat** u wilt goedkeuren en selecteer **Goedkeuren** op de werkbalk.

    ![Privé-eindpunt - status In behandeling](./media/configure-private-endpoints/pending.png)
1. Voer in **het dialoogvenster Verbinding** goedkeuren een opmerking in (optioneel) en selecteer **Ja.** 

    ![Privé-eindpunt - goedkeuren](./media/configure-private-endpoints/approve.png)
1. Controleer of u de status van het eindpunt ziet als **Goedgekeurd.** 

    ![Privé-eindpunt - goedgekeurde status](./media/configure-private-endpoints/approved-status.png)

### <a name="to-reject-a-private-endpoint"></a>Een privé-eindpunt weigeren
U kunt een privé-eindpunt met de status In behandeling of Goedgekeurd weigeren. Als u wilt weigeren, volgt u deze stappen: 

> [!NOTE]
> De stappen in deze sectie zijn voor onderwerpen. U kunt vergelijkbare stappen gebruiken om privé-eindpunten voor **domeinen af te wijzen.** 

1. Selecteer het **privé-eindpunt dat** u wilt afwijzen en selecteer **Weigeren** op de werkbalk.

    ![Schermopname met 'Netwerken - Privé-eindpuntverbindingen' met 'Weigeren' geselecteerd.](./media/configure-private-endpoints/reject-button.png)
1. Voer in **het dialoogvenster Verbinding** weigeren een opmerking in (optioneel) en selecteer **Ja.** 

    ![Privé-eindpunt - weigeren](./media/configure-private-endpoints/reject.png)
1. Controleer of u de status van het eindpunt ziet als **Geweigerd.** 

    ![Privé-eindpunt - geweigerde status](./media/configure-private-endpoints/rejected-status.png)

    > [!NOTE]
    > U kunt een privé-eindpunt in de Azure Portal zodra het is afgewezen. 


## <a name="use-azure-cli"></a>Azure CLI gebruiken
Als u een privé-eindpunt wilt maken, gebruikt u [de methode az network private-endpoint create,](/cli/azure/network/private-endpoint?#az_network_private_endpoint_create) zoals wordt weergegeven in het volgende voorbeeld:

```azurecli-interactive
az network private-endpoint create \
    --resource-group <RESOURECE GROUP NAME> \
    --name <PRIVATE ENDPOINT NAME> \
    --vnet-name <VIRTUAL NETWORK NAME> \
    --subnet <SUBNET NAME> \
    --private-connection-resource-id "/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<TOPIC NAME> \
    --connection-name <PRIVATE LINK SERVICE CONNECTION NAME> \
    --location <LOCATION> \
    --group-ids topic
```

Zie de documentatie voor [az network private-endpoint create](/cli/azure/network/private-endpoint?#az_network_private_endpoint_create)voor beschrijvingen van de parameters die in het voorbeeld worden gebruikt. Enkele punten om op te merken in dit voorbeeld zijn: 

- Geef `private-connection-resource-id` voor de resource-id van het **onderwerp** of **domein op.** In het voorgaande voorbeeld wordt het type: onderwerp gebruikt.
- geef `group-ids` voor op of `topic` `domain` . In het voorgaande voorbeeld wordt `topic` gebruikt. 

Als u een privé-eindpunt wilt verwijderen, gebruikt u [de methode az network private-endpoint delete,](/cli/azure/network/private-endpoint?#az_network_private_endpoint_delete) zoals wordt weergegeven in het volgende voorbeeld:

```azurecli-interactive
az network private-endpoint delete --resource-group <RESOURECE GROUP NAME> --name <PRIVATE ENDPOINT NAME>
```

> [!NOTE]
> De stappen in deze sectie zijn voor onderwerpen. U kunt vergelijkbare stappen gebruiken om privé-eindpunten voor **domeinen te maken.** 



### <a name="prerequisites"></a>Vereisten
Werk de Azure Event Grid voor CLI bij door de volgende opdracht uit te voeren: 

```azurecli-interactive
az extension update -n eventgrid
```

Als de extensie niet is geïnstalleerd, voer dan de volgende opdracht uit om deze te installeren: 

```azurecli-interactive
az extension add -n eventgrid
```

### <a name="create-a-private-endpoint"></a>Een privé-eindpunt maken
Als u een privé-eindpunt wilt maken, gebruikt u [de methode az network private-endpoint create,](/cli/azure/network/private-endpoint?#az_network_private_endpoint_create) zoals wordt weergegeven in het volgende voorbeeld:

```azurecli-interactive
az network private-endpoint create \
    --resource-group <RESOURECE GROUP NAME> \
    --name <PRIVATE ENDPOINT NAME> \
    --vnet-name <VIRTUAL NETWORK NAME> \
    --subnet <SUBNET NAME> \
    --private-connection-resource-id "/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<TOPIC NAME> \
    --connection-name <PRIVATE LINK SERVICE CONNECTION NAME> \
    --location <LOCATION> \
    --group-ids topic
```

Zie de documentatie voor [az network private-endpoint create](/cli/azure/network/private-endpoint?#az_network_private_endpoint_create)voor beschrijvingen van de parameters die in het voorbeeld worden gebruikt. Enkele punten om op te merken in dit voorbeeld zijn: 

- Geef `private-connection-resource-id` voor de resource-id van het **onderwerp** of **domein op.** In het voorgaande voorbeeld wordt het type: onderwerp gebruikt.
- geef `group-ids` voor op of `topic` `domain` . In het voorgaande voorbeeld wordt `topic` gebruikt. 

Als u een privé-eindpunt wilt verwijderen, gebruikt u [de methode az network private-endpoint delete,](/cli/azure/network/private-endpoint?#az_network_private_endpoint_delete) zoals wordt weergegeven in het volgende voorbeeld:

```azurecli-interactive
az network private-endpoint delete --resource-group <RESOURECE GROUP NAME> --name <PRIVATE ENDPOINT NAME>
```

> [!NOTE]
> De stappen in deze sectie zijn voor onderwerpen. U kunt vergelijkbare stappen gebruiken om privé-eindpunten voor **domeinen te maken.** 

#### <a name="sample-script"></a>Voorbeeldscript
Hier volgt een voorbeeldscript voor het maken van de volgende Azure-resources:

- Resourcegroep
- Virtueel netwerk
- Subnet in het virtuele netwerk
- Azure Event Grid onderwerp
- Privé-eindpunt voor het onderwerp

> [!NOTE]
> De stappen in deze sectie zijn voor onderwerpen. U kunt vergelijkbare stappen gebruiken om privé-eindpunten voor domeinen te maken.

```azurecli-interactive
subscriptionID="<AZURE SUBSCRIPTION ID>"
resourceGroupName="<RESOURCE GROUP NAME>"
location="<LOCATION>"
vNetName="<VIRTUAL NETWORK NAME>"
subNetName="<SUBNET NAME>"
topicName = "<TOPIC NAME>"
connectionName="<ENDPOINT CONNECTION NAME>"
endpointName=<ENDPOINT NAME>

# resource ID of the topic. replace <SUBSCRIPTION ID>, <RESOURCE GROUP NAME>, and <TOPIC NAME>
topicResourceID="/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<TOPIC NAME>"

# select subscription
az account set --subscription $subscriptionID

# create resource group
az group create --name $resourceGroupName --location $location

# create vnet 
az network vnet create \
    --resource-group $resourceGroupName \
    --name $vNetName \
    --address-prefix 10.0.0.0/16

# create subnet
az network vnet subnet create \
    --resource-group $resourceGroupName \
    --vnet-name $vNetName \
    --name $subNetName \
    --address-prefixes 10.0.0.0/24

# disable private endpoint network policies for the subnet
az network vnet subnet update \
    --resource-group $resourceGroupName \
    --vnet-name $vNetName \
    --name $subNetName \
    --disable-private-endpoint-network-policies true

# create event grid topic. update <LOCATION>
az eventgrid topic create \
    --resource-group $resourceGroupName \
    --name $topicName \
    --location $location

# verify that the topic was created.
az eventgrid topic show \
    --resource-group $resourceGroupName \
    --name $topicName

# create private endpoint for the topic you created
az network private-endpoint create 
    --resource-group $resourceGroupName \
    --name $endpointName \
    --vnet-name $vNetName \
    --subnet $subNetName \
    --private-connection-resource-id $topicResourceID \
    --connection-name $connectionName \
    --location $location \
    --group-ids topic

# get topic 
az eventgrid topic show \
    --resource-group $resourceGroupName \
    --name $topicName

```

### <a name="approve-a-private-endpoint"></a>Een privé-eindpunt goedkeuren
In het volgende CLI-voorbeeldfragment ziet u hoe u een verbinding met een privé-eindpunt goedkeurt. 

```azurecli-interactive
az eventgrid topic private-endpoint-connection approve \
    --resource-group $resourceGroupName \
    --topic-name $topicName \
    --name  $endpointName \
    --description "connection approved"
```


### <a name="reject-a-private-endpoint"></a>Een privé-eindpunt afwijzen
In het volgende CLI-voorbeeldfragment ziet u hoe u een verbinding met een privé-eindpunt kunt weigeren. 

```azurecli-interactive
az eventgrid topic private-endpoint-connection reject \
    --resource-group $resourceGroupName \
    --topic-name $topicName \
    --name $endpointName \
    --description "Connection rejected"
```

### <a name="disable-public-network-access"></a>Openbare netwerktoegang uitschakelen
Standaard is openbare netwerktoegang ingeschakeld voor een Event Grid onderwerp of domein. Als u alleen toegang wilt toestaan via privé-eindpunten, schakelt u openbare netwerktoegang uit door de volgende opdracht uit te voeren:  

```azurecli-interactive
az eventgrid topic update \
    --resource-group $resourceGroupName \
    --name $topicName \
    --public-network-access disabled
```


## <a name="use-powershell"></a>PowerShell gebruiken
In deze sectie ziet u hoe u een privé-eindpunt maakt voor een onderwerp of domein met behulp van PowerShell. 

### <a name="prerequisite"></a>Vereiste
Volg de instructies in Instructies: De portal gebruiken om een Azure AD-toepassing en [service-principal](../active-directory/develop/howto-create-service-principal-portal.md) te maken die toegang hebben tot resources om een Azure Active Directory-toepassing te maken en noteer de waarden voor **Map-id (tenant)-id,** **Toepassings-id (Client)** en **Toepassingsgeheim (client).** 

### <a name="prepare-token-and-headers-for-rest-api-calls"></a>Token en headers voorbereiden voor REST API aanroepen 
Voer de volgende vereiste opdrachten uit om een verificatie-token op te halen voor gebruik met REST API en autorisatie en andere header-informatie. 

```azurepowershell-interactive
$body = "grant_type=client_credentials&client_id=<CLIENT ID>&client_secret=<CLIENT SECRET>&resource=https://management.core.windows.net"

# get authentication token
$Token = Invoke-RestMethod -Method Post `
    -Uri https://login.microsoftonline.com/<TENANT ID>/oauth2/token  `
    -Body $body  `
    -ContentType 'application/x-www-form-urlencoded' 

# set authorization and content-type headers
$Headers = @{}
$Headers.Add("Authorization","$($Token.token_type) "+ " " + "$($Token.access_token)")
```

### <a name="create-a-subnet-with-endpoint-network-policies-disabled"></a>Een subnet maken met eindpuntnetwerkbeleid uitgeschakeld

```azurepowershell-interactive

# create resource group
New-AzResourceGroup -ResourceGroupName <RESOURCE GROUP NAME>  -Location <LOCATION>

# create virtual network
$virtualNetwork = New-AzVirtualNetwork `
                    -ResourceGroupName <RESOURCE GROUP NAME> `
                    -Location <LOCATION> `
                    -Name <VIRTUAL NETWORK NAME> `
                    -AddressPrefix 10.0.0.0/16

# create subnet with endpoint network policy disabled
$subnetConfig = Add-AzVirtualNetworkSubnetConfig `
                    -Name example-privatelinksubnet `
                    -AddressPrefix 10.0.0.0/24 `
                    -PrivateEndpointNetworkPoliciesFlag "Disabled" `
                    -VirtualNetwork $virtualNetwork

# update virtual network
$virtualNetwork | Set-AzVirtualNetwork
```

### <a name="create-an-event-grid-topic-with-a-private-endpoint"></a>Een Event Grid-onderwerp met een privé-eindpunt maken

> [!NOTE]
> De stappen in deze sectie zijn voor onderwerpen. U kunt vergelijkbare stappen gebruiken om privé-eindpunten voor **domeinen te maken.** 


```azurepowershell-interactive
$body = @{"location"="<LOCATION>"; "properties"=@{"publicNetworkAccess"="disabled"}} | ConvertTo-Json

# create topic
Invoke-RestMethod -Method 'Put'  `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-06-01"  `
    -Headers $Headers  `
    -Body $body

# verify that the topic was created
$topic=Invoke-RestMethod -Method 'Get'  `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-06-01"   `
    -Headers $Headers  

# create private link service connection
$privateEndpointConnection = New-AzPrivateLinkServiceConnection `
                                -Name "<PRIVATE LINK SERVICE CONNECTION NAME>" `
                                -PrivateLinkServiceId $topic.Id `
                                -GroupId "topic"

# get virtual network info 
$virtualNetwork = Get-AzVirtualNetwork  `
                    -ResourceGroupName  <RESOURCE GROUP NAME> `
                    -Name <VIRTUAL NETWORK NAME>

# get subnet info
$subnet = $virtualNetwork | Select -ExpandProperty subnets `
                             | Where-Object  {$_.Name -eq <SUBNET NAME>}  

# now, you are ready to create a private endpoint 
$privateEndpoint = New-AzPrivateEndpoint -ResourceGroupName <RESOURCE GROUP NAME>  `
                                        -Name <PRIVATE ENDPOINT NAME>   `
                                        -Location <LOCATION> `
                                        -Subnet  $subnet   `
                                        -PrivateLinkServiceConnection $privateEndpointConnection

# verify that the endpoint was created
Invoke-RestMethod -Method 'Get'  `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>/privateEndpointConnections?api-version=2020-06-01"   `
    -Headers $Headers   `
    | ConvertTo-Json -Depth 5

```

Wanneer u controleert of het eindpunt is gemaakt, ziet u het resultaat dat er ongeveer als volgt uit ziet:

```json

{
  "value": [
    {
      "properties": {
        "privateEndpoint": {
          "id": "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.Network/privateEndpoints/<PRIVATE ENDPOINT NAME>"
        },
        "groupIds": [
          "topic"
        ],
        "privateLinkServiceConnectionState": {
          "status": "Approved",
          "description": "Auto-approved",
          "actionsRequired": "None"
        },
        "provisioningState": "Succeeded"
      },
      "id": "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>/privateEndpointConnections/<PRIVATE ENDPOINT NAME>.<GUID>",
      "name": "myConnection",
      "type": "Microsoft.EventGrid/topics/privateEndpointConnections"
    }
  ]
}
```

### <a name="approve-a-private-endpoint-connection"></a>Een verbinding met een privé-eindpunt goedkeuren
In het volgende PowerShell-voorbeeldfragment ziet u hoe u een privé-eindpunt goedkeurt. 

> [!NOTE]
> De stappen in deze sectie zijn voor onderwerpen. U kunt vergelijkbare stappen gebruiken om privé-eindpunten voor domeinen **goed te keuren.** 

```azurepowershell-interactive
$approvedBody = @{"properties"=@{"privateLinkServiceConnectionState"=@{"status"="approved";"description"="connection approved";"actionsRequired"="none"}}} | ConvertTo-Json

# approve endpoint connection
Invoke-RestMethod -Method 'Put'  `
    -Uri "https://management.azure.com/subscriptions/<AzuRE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>/privateEndpointConnections/<PRIVATE ENDPOINT NAME>.<GUID>?api-version=2020-06-01"  `
    -Headers $Headers  `
    -Body $approvedBody

# confirm that the endpoint connection was approved
Invoke-RestMethod -Method 'Get'  `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>/privateEndpointConnections/<PRIVATE ENDPOINT NAME>.<GUID>?api-version=2020-06-01"  `
    -Headers $Headers

```

### <a name="reject-a-private-endpoint-connection"></a>Een verbinding met een privé-eindpunt weigeren
In het volgende voorbeeld ziet u hoe u een privé-eindpunt kunt afwijzen met behulp van PowerShell. U kunt de GUID voor het privé-eindpunt op halen uit het resultaat van de vorige GET-opdracht. 

> [!NOTE]
> De stappen in deze sectie zijn voor onderwerpen. U kunt vergelijkbare stappen gebruiken om privé-eindpunten voor domeinen **af te wijzen.** 


```azurepowershell-interactive
$rejectedBody = @{"properties"=@{"privateLinkServiceConnectionState"=@{"status"="rejected";"description"="connection rejected";"actionsRequired"="none"}}} | ConvertTo-Json

# reject private endpoint
Invoke-RestMethod -Method 'Put'  `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>/privateEndpointConnections/<PRIVATE ENDPOINT NAME>.<GUID>?api-version=2020-06-01"  `
    -Headers $Headers  `
    -Body $rejectedBody

# confirm that endpoint was rejected
Invoke-RestMethod -Method 'Get' 
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>/privateEndpointConnections/<PRIVATE ENDPOINT NAME>.<GUID>?api-version=2020-06-01" ` 
    -Headers $Headers
```

U kunt de verbinding zelfs goedkeuren nadat deze is afgewezen via de API. Als u Azure Portal, kunt u een eindpunt dat is afgewezen niet goedkeuren. 

## <a name="next-steps"></a>Volgende stappen
* Zie IP-firewall configureren voor Azure Event Grid onderwerpen of domeinen voor meer informatie over het configureren van [IP-firewallinstellingen.](configure-firewall.md)
* Zie Problemen met de netwerkverbinding oplossen voor informatie over het oplossen [van problemen met de netwerkverbinding](troubleshoot-network-connectivity.md)
