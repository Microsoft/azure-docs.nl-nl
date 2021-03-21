---
title: Privé-eind punten voor Azure Event Grid onderwerpen of domeinen configureren
description: In dit artikel wordt beschreven hoe u privé-eind punten voor Azure Event Grid-onderwerpen of-domein kunt configureren.
ms.topic: how-to
ms.date: 11/18/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: f8a9ac46596b1c2611ce9df387ac995e8149e7b9
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102425342"
---
# <a name="configure-private-endpoints-for-azure-event-grid-topics-or-domains"></a>Privé-eind punten voor Azure Event Grid onderwerpen of domeinen configureren
U kunt [privé-eind punten](../private-link/private-endpoint-overview.md) gebruiken om direct vanuit uw virtuele netwerk toegang te geven tot uw onderwerpen en domeinen, zonder dat u het open bare Internet [hoeft te passeren](../private-link/private-link-overview.md) . Het persoonlijke eind punt gebruikt een IP-adres uit de VNet-adres ruimte voor uw onderwerp of domein. Zie [netwerk beveiliging](network-security.md)voor meer conceptuele informatie.

In dit artikel wordt beschreven hoe u privé-eind punten voor onderwerpen of domeinen kunt configureren.

## <a name="use-azure-portal"></a>Azure Portal gebruiken 
In deze sectie wordt beschreven hoe u de Azure Portal gebruikt om een persoonlijk eind punt te maken voor een onderwerp of een domein.

> [!NOTE]
> De stappen die in deze sectie worden beschreven, zijn voornamelijk voor onderwerpen. U kunt soort gelijke stappen gebruiken om persoonlijke eind punten voor **domeinen** te maken. 

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) en navigeer naar uw onderwerp of domein.
2. Ga naar het tabblad **netwerken** op de pagina onderwerp. Selecteer **+ persoonlijk eind punt** op de werk balk.

    ![Persoonlijk eind punt toevoegen](./media/configure-private-endpoints/add-button.png)
2. Voer de volgende stappen uit op de pagina **basis beginselen** : 
    1. Selecteer een **Azure-abonnement** waarin u het persoonlijke eind punt wilt maken. 
    2. Selecteer een **Azure-resource groep** voor het persoonlijke eind punt. 
    3. Voer een **naam** in voor het eind punt. 
    4. Selecteer de **regio** voor het eind punt. Uw persoonlijke eind punt moet zich in dezelfde regio bevinden als uw virtuele netwerk, maar kan in een andere regio van de bron van de privé koppeling (in dit voor beeld een event grid-onderwerp). 
    5. Selecteer vervolgens **volgende: Resource >** knop onder aan de pagina. 

      ![Persoonlijk eind punt-pagina basis beginselen](./media/configure-private-endpoints/basics-page.png)
3. Voer de volgende stappen uit op de pagina **resource** : 
    1. Als u **verbinding maken met een Azure-resource in mijn Directory** selecteert, voert u de volgende stappen uit voor de verbindings methode. In dit voor beeld ziet u hoe u verbinding maakt met een Azure-resource in uw Directory. 
        1. Selecteer het **Azure-abonnement** waarin uw **onderwerp/domein** bestaat. 
        1. Selecteer voor **resource type** **micro soft. EventGrid/topics** of **micro soft. EventGrid/domains** voor het **resource type**.
        2. Selecteer voor **resource** een onderwerp/domein in de vervolg keuzelijst. 
        3. Controleer of de **doel-subresource** is ingesteld op **onderwerp** of **domein** (op basis van het bron type dat u hebt geselecteerd).    
        4. Selecteer **volgende: configuratie >** knop onder aan de pagina. 

            ![Scherm afbeelding met de pagina ' een privé-eind punt maken-resource ' weer gegeven.](./media/configure-private-endpoints/resource-page.png)
    2. Als u **verbinding maken met een resource selecteert met een resource-id of alias**, voert u de volgende stappen uit:
        1. Voer de ID van de resource in. Bijvoorbeeld: `/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>`.  
        2. Voor **resource** voert u **onderwerp** of **domein** in. 
        3. Beschrijving Een aanvraag bericht toevoegen. 
        4. Selecteer **volgende: configuratie >** knop onder aan de pagina. 

            ![Persoonlijk eind punt-resource pagina](./media/configure-private-endpoints/connect-azure-resource-id.png)
4. Selecteer op de pagina **configuratie** het subnet in een virtueel netwerk waarop u het persoonlijke eind punt wilt implementeren. 
    1. Selecteer een **virtueel netwerk**. In de vervolg keuzelijst worden alleen virtuele netwerken in het geselecteerde abonnement en de huidige locatie weer gegeven. 
    2. Selecteer een **subnet** in het virtuele netwerk dat u hebt geselecteerd. 
    3. Selecteer **volgende: labels >** knop onder aan de pagina. 

    ![Persoonlijk eind punt-configuratie pagina](./media/configure-private-endpoints/configuration-page.png)
5. Maak op de pagina **Tags** een wille keurige labels (namen en waarden) die u wilt koppelen aan de persoonlijke eindpunt resource. Selecteer vervolgens de knop **controleren + maken** onder aan de pagina. 
6. Controleer alle instellingen in het **overzicht en maken** en selecteer **maken** om het persoonlijke eind punt te maken. 

    ![Persoonlijk eind punt-& pagina maken bekijken](./media/configure-private-endpoints/review-create-page.png)
    

### <a name="manage-private-link-connection"></a>Verbinding met private link beheren

Wanneer u een privé-eindpunt maakt, moet de verbinding worden goedgekeurd. Als de resource waarvoor u een persoonlijk eind punt maakt zich in uw directory bevindt, kunt u de verbindings aanvraag goed keuren die u voldoende machtigingen hebt. Als u verbinding maakt met een Azure-resource in een andere Directory, moet u wachten tot de eigenaar van die resource uw verbindings aanvraag goed keuren.

Er zijn vier inrichtingsstatussen:

| Serviceactie | Status privé-eindpunt serviceconsument | Beschrijving |
|--|--|--|
| Geen | In behandeling | De verbinding wordt hand matig gemaakt en in afwachting van goed keuring van de resource-eigenaar van de persoonlijke koppeling. |
| Goedkeuren | Goedgekeurd | De verbinding werd automatisch of handmatig goedgekeurd en is klaar om te worden gebruikt. |
| Afwijzen | Afgewezen | De verbinding werd afgewezen door de resource-eigenaar van de private link. |
| Verwijderen | Ontkoppeld | De verbinding is verwijderd door de resource-eigenaar van de private link, het privé-eindpunt wordt informatief en moet worden verwijderd voor opschoning. |
 
###  <a name="how-to-manage-a-private-endpoint-connection"></a>Een verbinding met een privé-eind punt beheren
In de volgende secties ziet u hoe u een verbinding met een privé-eind punt kunt goed keuren of afwijzen. 

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
1. Typ in de zoek balk **Event grid onderwerpen** of **Event grid domeinen**.
1. Selecteer het **onderwerp** dat of het **domein** dat u wilt beheren.
1. Selecteer het tabblad **Netwerken**.
1. Als er verbindingen zijn die in behandeling zijn, ziet u een verbinding die wordt weer gegeven met in **behandeling** in de inrichtings status. 

### <a name="to-approve-a-private-endpoint"></a>Een persoonlijk eind punt goed keuren
U kunt een persoonlijk eind punt goed keuren met de status in behandeling. Voer de volgende stappen uit om goed te keuren: 

> [!NOTE]
> De stappen die in deze sectie worden beschreven, zijn voornamelijk voor onderwerpen. U kunt soort gelijke stappen gebruiken om privé-eind punten voor **domeinen** goed te keuren. 

1. Selecteer het **privé-eind punt** dat u wilt goed keuren en selecteer **goed keuren** op de werk balk.

    ![Privé-eind punt-status in behandeling](./media/configure-private-endpoints/pending.png)
1. In het dialoog venster **verbinding goed keuren** , voert u een opmerking in (optioneel) en selecteert u **Ja**. 

    ![Persoonlijk eind punt-goed keuren](./media/configure-private-endpoints/approve.png)
1. Bevestig dat u de status van het eind punt als **goedgekeurd** ziet. 

    ![Privé-eind punt-goedkeurings status](./media/configure-private-endpoints/approved-status.png)

### <a name="to-reject-a-private-endpoint"></a>Een persoonlijk eind punt afwijzen
U kunt een privé-eind punt dat de status in behandeling of goedgekeurd heeft, afwijzen. Voer de volgende stappen uit om af te wijzen: 

> [!NOTE]
> De stappen die in deze sectie worden weer gegeven, zijn voor onderwerpen. U kunt soort gelijke stappen gebruiken om persoonlijke eind punten voor **domeinen** af te wijzen. 

1. Selecteer het **persoonlijke eind punt** dat u wilt afwijzen en selecteer op de werk balk de optie **weigeren** .

    ![Scherm opname waarin de ' netwerk-privé-eindpunt verbindingen ' met ' afwijzen ' wordt weer gegeven.](./media/configure-private-endpoints/reject-button.png)
1. Voer in het dialoog venster **verbinding afwijzen** een opmerking in (optioneel) en selecteer **Ja**. 

    ![Persoonlijk eind punt-afwijzen](./media/configure-private-endpoints/reject.png)
1. Bevestig dat u de status van het eind punt ziet als **afgekeurd**. 

    ![Persoonlijk eind punt-status geweigerd](./media/configure-private-endpoints/rejected-status.png)

    > [!NOTE]
    > U kunt een persoonlijk eind punt niet goed keuren in de Azure Portal zodra het is afgewezen. 


## <a name="use-azure-cli"></a>Azure CLI gebruiken
Als u een persoonlijk eind punt wilt maken, gebruikt u de methode [AZ Network private-endpoint Create](/cli/azure/network/private-endpoint?#az-network-private-endpoint-create) , zoals wordt weer gegeven in het volgende voor beeld:

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

Voor beschrijvingen van de para meters die in het voor beeld worden gebruikt, raadpleegt u de documentatie voor [AZ Network private-endpoint Create](/cli/azure/network/private-endpoint?#az-network-private-endpoint-create). In dit voor beeld ziet u een paar punten: 

- `private-connection-resource-id`Geef voor de resource-id van het **onderwerp** of het **domein** op. In het vorige voor beeld wordt het onderwerp type: gebruikt.
- voor `group-ids` , opgeven `topic` of `domain` . In het voor gaande voor beeld `topic` wordt gebruikt. 

Als u een persoonlijk eind punt wilt verwijderen, gebruikt u de methode [AZ Network private-endpoint delete](/cli/azure/network/private-endpoint?#az-network-private-endpoint-delete) , zoals wordt weer gegeven in het volgende voor beeld:

```azurecli-interactive
az network private-endpoint delete --resource-group <RESOURECE GROUP NAME> --name <PRIVATE ENDPOINT NAME>
```

> [!NOTE]
> De stappen die in deze sectie worden weer gegeven, zijn voor onderwerpen. U kunt soort gelijke stappen gebruiken om persoonlijke eind punten voor **domeinen** te maken. 



### <a name="prerequisites"></a>Vereisten
Werk de Azure Event Grid extensie voor CLI bij door de volgende opdracht uit te voeren: 

```azurecli-interactive
az extension update -n eventgrid
```

Als de extensie niet is geïnstalleerd, voert u de volgende opdracht uit om deze te installeren: 

```azurecli-interactive
az extension add -n eventgrid
```

### <a name="create-a-private-endpoint"></a>Een privé-eindpunt maken
Als u een persoonlijk eind punt wilt maken, gebruikt u de methode [AZ Network private-endpoint Create](/cli/azure/network/private-endpoint?#az-network-private-endpoint-create) , zoals wordt weer gegeven in het volgende voor beeld:

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

Voor beschrijvingen van de para meters die in het voor beeld worden gebruikt, raadpleegt u de documentatie voor [AZ Network private-endpoint Create](/cli/azure/network/private-endpoint?#az-network-private-endpoint-create). In dit voor beeld ziet u een paar punten: 

- `private-connection-resource-id`Geef voor de resource-id van het **onderwerp** of het **domein** op. In het vorige voor beeld wordt het onderwerp type: gebruikt.
- voor `group-ids` , opgeven `topic` of `domain` . In het voor gaande voor beeld `topic` wordt gebruikt. 

Als u een persoonlijk eind punt wilt verwijderen, gebruikt u de methode [AZ Network private-endpoint delete](/cli/azure/network/private-endpoint?#az-network-private-endpoint-delete) , zoals wordt weer gegeven in het volgende voor beeld:

```azurecli-interactive
az network private-endpoint delete --resource-group <RESOURECE GROUP NAME> --name <PRIVATE ENDPOINT NAME>
```

> [!NOTE]
> De stappen die in deze sectie worden weer gegeven, zijn voor onderwerpen. U kunt soort gelijke stappen gebruiken om persoonlijke eind punten voor **domeinen** te maken. 

#### <a name="sample-script"></a>Voorbeeldscript
Hier volgt een voorbeeld script voor het maken van de volgende Azure-resources:

- Resourcegroep
- Virtueel netwerk
- Subnet in het virtuele netwerk
- Azure Event Grid onderwerp
- Persoonlijk eind punt voor het onderwerp

> [!NOTE]
> De stappen die in deze sectie worden weer gegeven, zijn voor onderwerpen. U kunt soort gelijke stappen gebruiken om persoonlijke eind punten voor domeinen te maken.

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

### <a name="approve-a-private-endpoint"></a>Een persoonlijk eind punt goed keuren
Het volgende voor beeld-CLI-fragment laat zien hoe u een verbinding met een privé-eind punt kunt goed keuren. 

```azurecli-interactive
az eventgrid topic private-endpoint-connection approve \
    --resource-group $resourceGroupName \
    --topic-name $topicName \
    --name  $endpointName \
    --description "connection approved"
```


### <a name="reject-a-private-endpoint"></a>Een persoonlijk eind punt afwijzen
Het volgende voor beeld-CLI-fragment laat zien hoe u een verbinding met een privé-eind punt kunt afwijzen. 

```azurecli-interactive
az eventgrid topic private-endpoint-connection reject \
    --resource-group $resourceGroupName \
    --topic-name $topicName \
    --name $endpointName \
    --description "Connection rejected"
```

### <a name="disable-public-network-access"></a>Open bare netwerk toegang uitschakelen
Open bare netwerk toegang is standaard ingeschakeld voor een Event Grid onderwerp of domein. Als u alleen toegang via persoonlijke eind punten wilt toestaan, schakelt u open bare netwerk toegang uit door de volgende opdracht uit te voeren:  

```azurecli-interactive
az eventgrid topic update \
    --resource-group $resourceGroupName \
    --name $topicName \
    --public-network-access disabled
```


## <a name="use-powershell"></a>PowerShell gebruiken
In deze sectie wordt beschreven hoe u een persoonlijk eind punt maakt voor een onderwerp of domein met behulp van Power shell. 

### <a name="prerequisite"></a>Vereiste
Volg de instructies in [de volgende stappen: gebruik de portal om een Azure AD-toepassing en Service-Principal te maken die toegang heeft tot resources](../active-directory/develop/howto-create-service-principal-portal.md) om een Azure Active Directory-toepassing te maken en noteer de waarden van de **Directory (Tenant) ID**, **toepassings-id** en **toepassing (client) geheim**. 

### <a name="prepare-token-and-headers-for-rest-api-calls"></a>Token en headers voorbereiden voor REST API-aanroepen 
Voer de volgende vereiste opdrachten uit om een verificatie token op te halen dat moet worden gebruikt met REST API-aanroepen en autorisatie en andere header-informatie. 

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

### <a name="create-a-subnet-with-endpoint-network-policies-disabled"></a>Een subnet maken met beleids regels voor eindpunt netwerken uitgeschakeld

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

### <a name="create-an-event-grid-topic-with-a-private-endpoint"></a>Een event grid-onderwerp maken met een persoonlijk eind punt

> [!NOTE]
> De stappen die in deze sectie worden weer gegeven, zijn voor onderwerpen. U kunt soort gelijke stappen gebruiken om persoonlijke eind punten voor **domeinen** te maken. 


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

Wanneer u controleert of het eind punt is gemaakt, ziet u het resultaat dat lijkt op het volgende:

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

### <a name="approve-a-private-endpoint-connection"></a>Een verbinding met een privé-eind punt goed keuren
Het volgende Power shell-voorbeeld fragment laat zien hoe u een persoonlijk eind punt kunt goed keuren. 

> [!NOTE]
> De stappen die in deze sectie worden weer gegeven, zijn voor onderwerpen. U kunt soort gelijke stappen gebruiken om privé-eind punten voor **domeinen** goed te keuren. 

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

### <a name="reject-a-private-endpoint-connection"></a>Een persoonlijke eindpunt verbinding weigeren
In het volgende voor beeld ziet u hoe u een persoonlijk eind punt afwijst met behulp van Power shell. U kunt de GUID voor het persoonlijke eind punt ophalen uit het resultaat van de vorige GET-opdracht. 

> [!NOTE]
> De stappen die in deze sectie worden weer gegeven, zijn voor onderwerpen. U kunt soort gelijke stappen gebruiken om persoonlijke eind punten voor **domeinen** af te wijzen. 


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

U kunt de verbinding ook goed keuren nadat deze is geweigerd via de API. Als u Azure Portal gebruikt, kunt u een eind punt dat is geweigerd, niet goed keuren. 

## <a name="next-steps"></a>Volgende stappen
* Zie [Configure IP firewall for Azure Event grid topics or domains](configure-firewall.md)(Engelstalig) voor meer informatie over het configureren van IP-Firewall-instellingen.
* Zie problemen [met netwerk connectiviteit oplossen](troubleshoot-network-connectivity.md) voor informatie over het oplossen van problemen met de netwerk verbinding
