---
title: IP-Firewall configureren voor Azure Event Grid onderwerpen of domeinen
description: In dit artikel wordt beschreven hoe u Firewall instellingen configureert voor Event Grid onderwerpen of domeinen.
ms.topic: conceptual
ms.date: 03/02/2021
ms.openlocfilehash: 178b9d84ea8b2e0f764f7584526db8dbcf5284f3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102031834"
---
# <a name="configure-ip-firewall-for-azure-event-grid-topics-or-domains"></a>IP-Firewall configureren voor Azure Event Grid onderwerpen of domeinen 
Het onderwerp en het domein zijn standaard toegankelijk vanaf internet, zolang de aanvraag geldig is voor verificatie en autorisatie. Met IP-firewall kunt u dit nog verder beperken tot een aantal IPv4-adressen of IPv4-adresbereiken in CIDR-notatie [(klasseloze Inter-Domain route ring)](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) . Uitgevers die afkomstig zijn van een ander IP-adres, worden geweigerd en ontvangen een 403 (verboden)-antwoord. Zie [netwerk beveiliging voor Event grid](network-security.md)voor meer informatie over de beveiligings functies van het netwerk die door Event grid worden ondersteund.

In dit artikel wordt beschreven hoe u IP-Firewall instellingen configureert voor Azure Event Grid onderwerpen of domeinen.

## <a name="use-azure-portal"></a>Azure Portal gebruiken
In deze sectie wordt beschreven hoe u de Azure Portal gebruikt voor het maken van binnenkomende IP-firewall regels. De stappen die in deze sectie worden weer gegeven, zijn voor onderwerpen. U kunt soort gelijke stappen gebruiken om inkomende IP-regels voor **domeinen** te maken. 

1. Ga in het [Azure Portal](https://portal.azure.com)naar het onderwerp of het domein van het gebeurtenis raster en schakel over naar het tabblad **netwerken** .
2. Selecteer **open bare netwerken** om alle netwerken, inclusief Internet, toegang te geven tot de resource. 

    U kunt het verkeer beperken met behulp van firewall regels op basis van IP. Geef één IPv4-adres of een bereik van IP-adressen in een CIDR-notatie (Classless Inter-Domain Routing) op. 

    ![Scherm opname van de pagina ' open bare netwerk toegang ' waarvoor ' open bare netwerken ' is geselecteerd.](./media/configure-firewall/public-networks-page.png)
3. Selecteer **privé-eind punten alleen** om alleen privé-eindpunt verbindingen toe te staan om toegang te krijgen tot deze bron. Gebruik het tabblad **verbindingen met privé-eind punten** op deze pagina om verbindingen te beheren. 

    ![Pagina open bare netwerken](./media/configure-firewall/private-endpoints-page.png)
4. Selecteer **Opslaan** op de werkbalk. 



## <a name="use-azure-cli"></a>Azure CLI gebruiken
In deze sectie wordt beschreven hoe u Azure CLI-opdrachten gebruikt om onderwerpen te maken met inkomende IP-regels. De stappen die in deze sectie worden weer gegeven, zijn voor onderwerpen. U kunt soort gelijke stappen gebruiken om inkomende IP-regels voor **domeinen** te maken. 


### <a name="enable-or-disable-public-network-access"></a>Open bare netwerk toegang in-of uitschakelen
De toegang tot het open bare netwerk is standaard ingeschakeld voor onderwerpen en domeinen. U kunt deze ook expliciet inschakelen of uitschakelen. U kunt het verkeer beperken door binnenkomende IP-firewall regels te configureren. 

#### <a name="enable-public-network-access-while-creating-a-topic"></a>Open bare netwerk toegang inschakelen tijdens het maken van een onderwerp

```azurecli-interactive
az eventgrid topic create \
    --resource-group $resourceGroupName \
    --name $topicName \
    --location $location \
    --public-network-access enabled
```


#### <a name="disable-public-network-access-while-creating-a-topic"></a>Open bare netwerk toegang uitschakelen tijdens het maken van een onderwerp

```azurecli-interactive
az eventgrid topic create \
    --resource-group $resourceGroupName \
    --name $topicName \
    --location $location \
    --public-network-access disabled
```

> [!NOTE]
> Wanneer open bare netwerk toegang is uitgeschakeld voor een onderwerp of domein, is verkeer via open bare Internet niet toegestaan. Alleen verbindingen met een privé-eind punt hebben toegang tot deze resources. 


#### <a name="enable-public-network-access-for-an-existing-topic"></a>Open bare toegang tot het netwerk inschakelen voor een bestaand onderwerp

```azurecli-interactive
az eventgrid topic update \
    --resource-group $resourceGroupName \
    --name $topicName \
    --public-network-access enabled 
```

#### <a name="disable-public-network-access-for-an-existing-topic"></a>Open bare netwerk toegang voor een bestaand onderwerp uitschakelen 

```azurecli-interactive
az eventgrid topic update \
    --resource-group $resourceGroupName \
    --name $topicName \
    --public-network-access disabled
```

### <a name="create-a-topic-with-single-inbound-ip-rule"></a>Een onderwerp maken met één inkomende IP-regel
Met de volgende voorbeeld CLI-opdracht maakt u een event grid-onderwerp met inkomende IP-regels. 

```azurecli-interactive
az eventgrid topic create \
    --resource-group $resourceGroupName \
    --name $topicName \
    --location $location \
    --public-network-access enabled \
    --inbound-ip-rules <IP ADDR or CIDR MASK> allow 
```

### <a name="create-a-topic-with-multiple-inbound-ip-rules"></a>Een onderwerp met meerdere inkomende IP-regels maken

Met de volgende voorbeeld CLI-opdracht maakt u een event grid-onderwerp twee inkomende IP-regels in één stap: 

```azurecli-interactive
az eventgrid topic create \
    --resource-group $resourceGroupName \
    --name $topicName \
    --location $location \
    --public-network-access enabled \
    --inbound-ip-rules <IP ADDR 1 or CIDR MASK 1> allow \
    --inbound-ip-rules <IP ADDR 2 or CIDR MASK 2> allow
```

### <a name="update-an-existing-topic-to-add-inbound-ip-rules"></a>Een bestaand onderwerp bijwerken om binnenkomende IP-regels toe te voegen
In dit voor beeld wordt eerst een event grid-onderwerp gemaakt en worden vervolgens binnenkomende IP-regels voor het onderwerp in een afzonderlijke opdracht toegevoegd. Ook worden de binnenkomende IP-regels bijgewerkt die in de tweede opdracht zijn ingesteld. 

```azurecli-interactive

# create the event grid topic first
az eventgrid topic create \
    --resource-group $resourceGroupName \
    --name $topicName \
    --location $location

# add inbound IP rules to an existing topic
az eventgrid topic update \
    --resource-group $resourceGroupName \
    --name $topicName \
    --public-network-access enabled \
    --inbound-ip-rules <IP ADDR or CIDR MASK> allow

# later, update topic with additional ip rules
az eventgrid topic update \
    --resource-group $resourceGroupName \
    --name $topicName \
    --public-network-access enabled \
    --inbound-ip-rules <IP ADDR 1 or CIDR MASK 1> allow \
    --inbound-ip-rules <IP ADDR 2 or CIDR MASK 2> allow
```

### <a name="remove-an-inbound-ip-rule"></a>Een regel voor inkomend IP-verkeer verwijderen
Met de volgende opdracht wordt de tweede regel die u in de vorige stap hebt gemaakt, verwijderd door alleen de eerste regel op te geven tijdens het bijwerken van de instelling. 

```azurecli-interactive
az eventgrid topic update \
    --resource-group $resourceGroupName \
    --name $topicName \
    --public-network-access enabled \
    --inbound-ip-rules <IP ADDR 1 or CIDR MASK 1> allow
```


## <a name="use-powershell"></a>PowerShell gebruiken
In deze sectie wordt beschreven hoe u Azure PowerShell-opdrachten gebruikt om Azure Event Grid onderwerpen te maken met binnenkomende IP-firewall regels. De stappen die in deze sectie worden weer gegeven, zijn voor onderwerpen. U kunt soort gelijke stappen gebruiken om inkomende IP-regels voor **domeinen** te maken. 

### <a name="prerequisites"></a>Vereisten
Volg de instructies [voor het maken van een Azure AD-toepassing en Service-Principal](../active-directory/develop/howto-create-service-principal-portal.md) met behulp van de portal om een Azure Active Directory-toepassing te maken en noteer de volgende waarden:

- (Tenant-)id van de map
- Toepassings-ID (client)
- Geheim van toepassing (client)

### <a name="prepare-token-and-headers-for-rest-api-calls"></a>Token en headers voorbereiden voor REST API-aanroepen 
Voer de volgende vereiste opdrachten uit om een verificatie token op te halen dat moet worden gebruikt met REST API-aanroepen en autorisatie-en andere header-informatie. 

```azurepowershell-interactive
# replace <CLIENT ID> and <CLIENT SECRET>
$body = "grant_type=client_credentials&client_id=<CLIENT ID>&client_secret=<CLIENT SECRET>&resource=https://management.core.windows.net"

# get the authentication token. Replace <TENANT ID>
$Token = Invoke-RestMethod -Method Post `
    -Uri https://login.microsoftonline.com/<TENANT ID>/oauth2/token `
    -Body $body `
    -ContentType 'application/x-www-form-urlencoded'

# set authorization and content-type headers
$Headers = @{}
$Headers.Add("Authorization","$($Token.token_type) "+ " " + "$($Token.access_token)")
$Headers.Add("Content-Type","application/json")
```

### <a name="enable-public-network-access-for-an-existing-topic"></a>Open bare toegang tot het netwerk inschakelen voor een bestaand onderwerp
De toegang tot het open bare netwerk is standaard ingeschakeld voor onderwerpen en domeinen. U kunt het verkeer beperken door binnenkomende IP-firewall regels te configureren. 

```azurepowershell-interactive
$body = @{"properties"=@{"publicNetworkAccess"="enabled"}} | ConvertTo-Json -Depth 5

Invoke-RestMethod -Method 'Patch' `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-06-01" `
    -Headers $Headers `
    -Body $body `
    | ConvertTo-Json -Depth 5
```

### <a name="disable-public-network-access-for-an-existing-topic"></a>Open bare netwerk toegang voor een bestaand onderwerp uitschakelen
Wanneer open bare netwerk toegang is uitgeschakeld voor een onderwerp of domein, is verkeer via open bare Internet niet toegestaan. Alleen verbindingen met een privé-eind punt hebben toegang tot deze resources. 

```azurepowershell-interactive
$body = @{"properties"=@{"publicNetworkAccess"="disabled"}} | ConvertTo-Json -Depth 5

Invoke-RestMethod -Method 'Patch' `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-06-01" `
    -Headers $Headers `
    -Body $body `
    | ConvertTo-Json -Depth 5
```

### <a name="create-an-event-grid-topic-with-inbound-rules-in-one-step"></a>Een event grid-onderwerp maken met binnenkomende regels in één stap

```azurepowershell-interactive

# prepare the body for the REST PUT method. Notice that inbound IP rules are included. 
$body = @{"location"="<LOCATION>"; "sku"= @{"name"="basic"}; "properties"=@{"publicNetworkAccess"="enabled"; "inboundIpRules"=@(@{"ipmask"="<IP ADDR or CIDR MASK>";"action"="allow"})}} | ConvertTo-Json -Depth 5

# create the event grid topic with inbound IP rules
Invoke-RestMethod -Method 'Put' `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-06-01" `
    -Headers $Headers `
    -Body $body

# verify that the topic was created
Invoke-RestMethod -Method 'Get' `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-06-01" `
    -Headers $Headers `
    | ConvertTo-Json -Depth 5
```


### <a name="create-event-grid-topic-first-and-then-add-inbound-ip-rules"></a>Eerst Event grid-onderwerp maken en vervolgens binnenkomende IP-regels toevoegen

```azurepowershell-interactive

# prepare the body for the REST PUT method. Notice that no inbound IP rules are specified. 
$body = @{"location"="<LOCATION>"; "sku"= @{"name"="basic"}; "properties"=@{"publicNetworkAccess"="enabled";}} | ConvertTo-Json -Depth 5

# create the Event Grid topic
Invoke-RestMethod -Method 'Put' `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-06-01" `
    -Headers $Headers `
    -Body $body`

# verify that the topic was created
Invoke-RestMethod -Method 'Get' `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-06-01" `
    -Headers $Headers `
    | ConvertTo-Json -Depth 5

# prepare the body for REST PUT method. Notice that it includes inbound IP rules now. This feature is available in both basic and premium tiers.
$body = @{"location"="<LOCATION>"; "sku"= @{"name"="basic"}; "properties"=@{"publicNetworkAccess"="enabled"; "inboundIpRules"=@(@{"ipmask"="<IP ADDR or CIDR MASK>";"action"="allow"}, @{"ipmask"="<IP ADDR or CIDR MASK>";"action"="allow"})}} | ConvertTo-Json -Depth 5

# update the topic with inbound IP rules
Invoke-RestMethod -Method 'Put' `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-06-01" `
    -Headers $Headers `
    -Body $body

# verify that the topic was updated
Invoke-RestMethod -Method 'Get' 
    -Uri "https://management.azure.com/subscriptions/<AzURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-06-01" `
    -Headers $Headers `
    | ConvertTo-Json -Depth 5

```

## <a name="next-steps"></a>Volgende stappen

* Zie [Event grid bericht bezorging bewaken](monitor-event-delivery.md)voor meer informatie over het bewaken van gebeurtenis leveringen.
* Zie [Event grid beveiliging en verificatie](security-authentication.md)voor meer informatie over de verificatie sleutel.
* Zie [Event grid Subscription schema](subscription-creation-schema.md)voor meer informatie over het maken van een Azure Event grid-abonnement.
* Zie problemen [met netwerk connectiviteit oplossen](troubleshoot-network-connectivity.md) voor informatie over het oplossen van problemen met de netwerk verbinding
