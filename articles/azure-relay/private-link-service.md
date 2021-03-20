---
title: Azure Relay integreren met Azure Private Link service
description: Meer informatie over het integreren van Azure Relay met Azure Private Link service
ms.date: 09/24/2020
ms.topic: article
ms.openlocfilehash: 13644082160704ba9918e6bd6257fa314bb463a6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98134378"
---
# <a name="integrate-azure-relay-with-azure-private-link"></a>Azure Relay integreren met een persoonlijke Azure-koppeling 
Met Azure **Private Link service** kunt u toegang krijgen tot Azure-Services (bijvoorbeeld Azure Relay, Azure service bus, Azure Event Hubs, Azure Storage en Azure Cosmos DB) en Azure hostende klanten/partner services via een persoonlijk eind punt in uw virtuele netwerk. Zie [Wat is een Azure Private Link?](../private-link/private-link-overview.md) voor meer informatie.

Een **persoonlijk eind punt** is een netwerk interface waarmee de werk belastingen die worden uitgevoerd in een virtueel netwerk, privé en veilig verbinding kunnen maken met een service die een **persoonlijke koppelings bron** heeft (bijvoorbeeld een relay-naam ruimte). Het privé-eindpunt maakt gebruik van een privé-IP-adres van uw VNet, waardoor de service feitelijk in uw VNet wordt geplaatst. Al het verkeer naar de service kan worden gerouteerd via het persoonlijke eind punt, zodat er geen gateways, NAT-apparaten, ExpressRoute, VPN-verbindingen of open bare IP-adressen nodig zijn. Verkeer tussen uw virtuele netwerk en de service wordt over het micro soft backbone-netwerk gepasseerd, waardoor de bloot stelling van het open bare Internet wordt voor komen. U kunt een niveau van granulatie in toegangs beheer geven door verbindingen met specifieke Azure Relay-naam ruimten toe te staan. 


## <a name="add-a-private-endpoint-using-azure-portal"></a>Een persoonlijk eind punt toevoegen met Azure Portal

### <a name="prerequisites"></a>Vereisten
Als u een Azure Relay naam ruimte met een persoonlijke Azure-koppeling wilt integreren, hebt u de volgende entiteiten of machtigingen nodig:

- Een Azure Relay naam ruimte.
- Een Azure Virtual Network.
- Een subnet binnen het virtueel netwerk.
- Machtigingen voor eigenaar of Inzender op het virtuele netwerk.

Uw privé-eindpunt en het virtueel netwerk moeten zich in dezelfde regio bevinden. Wanneer u een regio voor het privé-eindpunt selecteert met behulp van de portal, worden er automatisch alleen virtuele netwerken gefilterd die zich in die regio bevinden. Uw naam ruimte kan zich in een andere regio bevinden.

Uw privé-eindpunt maakt gebruik van een privé IP-adres in uw virtueel netwerk.

### <a name="steps"></a>Stappen
Zie [een Azure relay naam ruimte maken met behulp van de Azure Portal](relay-create-namespace-portal.md)voor stapsgewijze instructies voor het maken van een nieuwe Azure relay naam ruimte en entiteiten hierin.

1. Meld u aan bij [Azure Portal](https://portal.azure.com). 
2. Typ in de zoek balk in **relays**.
3. Selecteer de **naam ruimte** in de lijst waaraan u een persoonlijk eind punt wilt toevoegen.
4. Selecteer het tabblad **netwerken** onder **instellingen**.
5. Selecteer het tabblad **verbindingen met privé-eind punten** boven aan de pagina
6. Selecteer de knop **+ privé-eind punt** boven aan de pagina.

    ![Knop persoonlijk eind punt toevoegen](./media/private-link-service/add-private-endpoint-button.png)
7. Voer de volgende stappen uit op de pagina **basis beginselen** : 
    1. Selecteer het **Azure-abonnement** waarin u het persoonlijke eind punt wilt maken. 
    2. Selecteer de **resource groep** voor de persoonlijke eindpunt resource.
    3. Voer een **naam** in voor het persoonlijke eind punt. 
    5. Selecteer een **regio** voor het persoonlijke eind punt. Uw persoonlijke eind punt moet zich in dezelfde regio bevinden als uw virtuele netwerk, maar kan zich in een andere regio bevinden dan de naam ruimte van Azure Relay waarmee u verbinding maakt. 
    6. Selecteer **volgende: Resource >** knop onder aan de pagina.

        ![Privé-eind punt maken-pagina basis beginselen](./media/private-link-service/create-private-endpoint-basics-page.png)
8. Voer de volgende stappen uit op de pagina **resource** :
    1. Als u bij verbindings methode **verbinding maken met een Azure-resource in mijn Directory** selecteert, hebt u de eigenaar of Inzender toegang tot de naam ruimte en die naam ruimte bevindt zich in dezelfde map als het persoonlijke eind punt, volgt u deze stappen: 
        1. Selecteer het **Azure-abonnement** waarin uw **Azure relay naam ruimte** bestaat. 
        2. Selecteer bij **resource type** de optie **micro soft. relay/naam ruimten** voor het **bron type**.
        3. Selecteer voor **resource** een relay-naam ruimte in de vervolg keuzelijst. 
        4. Controleer of de **doel-subresource** is ingesteld op **naam ruimte**.
        5. Selecteer **volgende: configuratie >** knop onder aan de pagina. 
        
            ![Privé-eind punt maken-resource pagina](./media/private-link-service/create-private-endpoint-resource-page.png)    
    2. Als u **verbinding maken met een Azure-resource selecteert op resource-id of alias** , omdat de naam ruimte zich niet in dezelfde map bevindt als die van het persoonlijke eind punt, volgt u deze stappen:
        1. Voer de **resource-id** of **alias** in. Dit kan de resource-ID of alias zijn die iemand met u heeft gedeeld. De eenvoudigste manier om de resource-ID op te halen, is door te navigeren naar de Azure Relay naam ruimte in de Azure Portal en het gedeelte van de URI te kopiëren vanaf `/subscriptions/` . Hier volgt een voor beeld: `/subscriptions/000000000-0000-0000-0000-000000000000000/resourceGroups/myresourcegroup/providers/Microsoft.Relay/namespaces/myrelaynamespace.` 
        2. Voer een **naam ruimte** in voor de **subresource** van het doel. Het is het type van de subbron waartoe uw persoonlijke eind punt toegang heeft.
        3. Beschrijving Voer een **aanvraag bericht** in. De resource-eigenaar ziet dit bericht tijdens het beheer van de verbinding met een privé-eind punt.
        4. Selecteer vervolgens **volgende: configuratie >** knop onder aan de pagina.

            ![Persoonlijk eind punt maken-verbinden via Resource-ID](./media/private-link-service/connect-resource-id.png)
9. Selecteer op de pagina **configuratie** het subnet in een virtueel netwerk waarop u het persoonlijke eind punt wilt implementeren. 
    1. Selecteer een **virtueel netwerk**. In de vervolg keuzelijst worden alleen virtuele netwerken in het geselecteerde abonnement en de huidige locatie weer gegeven. 
    2. Selecteer een **subnet** in het virtuele netwerk dat u hebt geselecteerd. 
    3. Schakel **integreren met privé-DNS-zone** in als u uw persoonlijke eind punt wilt integreren met een privé-DNS-zone. 
    
        Als u privé wilt verbinden met uw persoonlijke eind punt, moet u een DNS-record hebben. We raden u aan uw persoonlijke eind punt te integreren met een **privé-DNS-zone**. U kunt ook uw eigen DNS-servers gebruiken of DNS-records maken met behulp van de host-bestanden op uw virtuele machines. Zie voor meer informatie [Azure private endpoint DNS-configuratie](../private-link/private-endpoint-dns.md). In dit voor beeld is de optie **integreren met persoonlijke DNS-zone** geselecteerd en wordt er een privé-DNS-zone voor u gemaakt. 
    3. Selecteer **volgende: labels >** knop onder aan de pagina. 

        ![Privé-eind punt maken-configuratie pagina](./media/private-link-service/create-private-endpoint-configuration-page.png)
10. Maak op de pagina **Tags** een wille keurige code (namen en waarden) die u wilt koppelen aan het persoonlijke eind punt en de privé-DNS-zone (als u de optie hebt ingeschakeld). Selecteer vervolgens de knop **controleren + maken** onder aan de pagina. 
11. Controleer alle instellingen in het **overzicht en maken** en selecteer **maken** om het persoonlijke eind punt te maken.
    
    ![Privé-eind punt maken-pagina controleren en maken](./media/private-link-service/create-private-endpoint-review-create-page.png)
12. Op de pagina **persoonlijk eind punt** ziet u de status van de verbinding met een privé-eind punt. Als u de eigenaar van de relay-naam ruimte bent of de optie toegang beheren hebt ingeschakeld en **verbinding maken met een Azure-resource** hebt geselecteerd voor de **verbindings methode**, moet de eindpunt verbinding **automatisch worden goedgekeurd**. Zie de sectie [privé-eind punten beheren met Azure Portal](#manage-private-endpoints-using-azure-portal) als de status in **behandeling** is.

    ![Pagina privé-eind punt](./media/private-link-service/private-endpoint-page.png)
13. Ga terug naar de pagina **netwerk** van de **naam ruimte** en schakel over naar het tabblad **verbindingen van privé-eind punten** . U ziet het persoonlijke eind punt dat u hebt gemaakt. 

    ![Persoonlijk eind punt gemaakt](./media/private-link-service/private-endpoint-created.png)

## <a name="add-a-private-endpoint-using-powershell"></a>Een persoonlijk eind punt toevoegen met Power shell
In het volgende voor beeld ziet u hoe u Azure PowerShell kunt gebruiken om een persoonlijke eindpunt verbinding te maken met een Azure Relay naam ruimte.

Uw privé-eindpunt en het virtueel netwerk moeten zich in dezelfde regio bevinden. De naam ruimte van uw Azure Relay kan zich in een andere regio bevinden. En uw persoonlijke eind punt maakt gebruik van een privé-IP-adres in uw virtuele netwerk.

```azurepowershell-interactive

$rgName = "<RESOURCE GROUP NAME>"
$vnetlocation = "<VNET LOCATION>"
$vnetName = "<VIRTUAL NETWORK NAME>"
$subnetName = "<SUBNET NAME>"
$namespaceLocation = "<NAMESPACE LOCATION>"
$namespaceName = "<NAMESPACE NAME>"
$peConnectionName = "<PRIVATE ENDPOINT CONNECTION NAME>"

# create resource group
az group create -l $vnetLocation -n $rgName

# create virtual network
$virtualNetwork = New-AzVirtualNetwork `
                    -ResourceGroupName $rgName `
                    -Location $vnetlocation `
                    -Name $vnetName `
                    -AddressPrefix 10.0.0.0/16

# create subnet with endpoint network policy disabled
$subnetConfig = Add-AzVirtualNetworkSubnetConfig `
                    -Name $subnetName `
                    -AddressPrefix 10.0.0.0/24 `
                    -PrivateEndpointNetworkPoliciesFlag "Disabled" `
                    -VirtualNetwork $virtualNetwork

# update virtual network
$virtualNetwork | Set-AzVirtualNetwork

# create a relay namespace
$namespaceResource = New-AzResource -Location $namespaceLocation -ResourceName $namespaceName -ResourceGroupName $rgName -Properties @{} -ResourceType "Microsoft.Relay/namespaces" 

# create a private link service connection
$privateEndpointConnection = New-AzPrivateLinkServiceConnection `
                                -Name $peConnectionName `
                                -PrivateLinkServiceId $namespaceResource.ResourceId `
                                -GroupId "namespace"

# get subnet object that you will use in the next step                                
$virtualNetwork = Get-AzVirtualNetwork -ResourceGroupName  $rgName -Name $vnetName
$subnet = $virtualNetwork | Select -ExpandProperty subnets `
                                | Where-Object  {$_.Name -eq $subnetName}  
   
# now, create private endpoint   
$privateEndpoint = New-AzPrivateEndpoint -ResourceGroupName $rgName  `
                                -Name $vnetName   `
                                -Location $vnetlocation `
                                -Subnet  $subnet   `
                                -PrivateLinkServiceConnection $privateEndpointConnection

(Get-AzResource -ResourceId $namespaceResource.ResourceId -ExpandProperties).Properties


```

## <a name="manage-private-endpoints-using-azure-portal"></a>Privé-eind punten beheren met Azure Portal

Wanneer u een privé-eindpunt maakt, moet de verbinding worden goedgekeurd. Als de resource (relay-naam ruimte) waarvoor u een persoonlijk eind punt maakt zich in uw directory bevindt, kunt u de verbindings aanvraag goed keuren die u rechten hebt voor het beheren van de relay-naam ruimte. Als u verbinding maakt met een relay-naam ruimte waarvoor u de toegang beheren niet hebt, moet u wachten tot de eigenaar van die bron uw verbindings aanvraag goed keuren.

Er zijn vier inrichtingsstatussen:

| Serviceactie | Status privé-eindpunt serviceconsument | Beschrijving |
|--|--|--|
| Geen | In behandeling | De verbinding wordt hand matig gemaakt en is in afwachting van goed keuring van de Azure Relay naam ruimte-eigenaar. |
| Goedkeuren | Goedgekeurd | De verbinding werd automatisch of handmatig goedgekeurd en is klaar om te worden gebruikt. |
| Afwijzen | Afgewezen | De verbinding is geweigerd door de eigenaar van de Azure Relay naam ruimte. |
| Verwijderen | Ontkoppeld | De verbinding is verwijderd door de eigenaar van de Azure Relay naam ruimte, het persoonlijke eind punt wordt informatieve en moet worden verwijderd voor opschoning. |
 
###  <a name="approve-reject-or-remove-a-private-endpoint-connection"></a>Een verbinding met een privé-eind punt goed keuren, afwijzen of verwijderen

1. Meld u aan bij Azure Portal.
1. Typ in de zoek balk in **Relay**.
1. Selecteer de **naam ruimte** die u wilt beheren.
1. Selecteer het tabblad **Netwerken**.
5. Ga naar de toepasselijke sectie hieronder op basis van de bewerking die u wilt: goed keuren, afwijzen of verwijderen. 

### <a name="approve-a-private-endpoint-connection"></a>Een verbinding met een privé-eind punt goed keuren

1. Als er verbindingen zijn die in behandeling zijn, ziet u een verbinding die wordt weer gegeven met in **behandeling** in de inrichtings status. 
2. Selecteer het **privé-eind punt** dat u wilt goed keuren
3. Selecteer de knop **goed keuren** .

    ![Keur het privé-eindpunt goed](./media/private-link-service/private-endpoint-approve.png)
4. Voer op de pagina **verbinding goed keuren** een optionele **Opmerking** in en selecteer **Ja**. Als u **Nee** selecteert, gebeurt er niets. 

    ![Verbindings pagina goed keuren](./media/private-link-service/approve-connection-page.png)
5. Als het goed is, ziet u de status van de verbinding in de lijst gewijzigd in **goedgekeurd**.

### <a name="reject-a-private-endpoint-connection"></a>Een persoonlijke eindpunt verbinding weigeren

1. Als er particuliere endpoint-verbindingen zijn die u wilt weigeren, selecteert u de eindpunt verbinding en klikt u op de knop **afwijzen** , ongeacht of deze een aanvraag in behandeling of bestaande verbinding is die eerder is goedgekeurd.

    ![Knop afwijzen](./media/private-link-service/private-endpoint-reject.png)
2. Voer op de pagina **verbinding afwijzen** een optionele opmerking in en selecteer **Ja**. Als u **Nee** selecteert, gebeurt er niets. 

    ![Pagina verbinding weigeren](./media/private-link-service/reject-connection-page.png)
3. U ziet de status van de verbinding in de lijst is gewijzigd **afgewezen**.


### <a name="remove-a-private-endpoint-connection"></a>Een verbinding met een privé-eind punt verwijderen

1. Als u een verbinding met een privé-eind punt wilt verwijderen, selecteert u deze in de lijst en selecteert u **verwijderen** op de werk balk. 

    ![Knop verwijderen](./media/private-link-service/remove-endpoint.png)
2. Selecteer op de pagina **verbinding verwijderen** de optie **Ja** om het verwijderen van het persoonlijke eind punt te bevestigen. Als u **Nee** selecteert, gebeurt er niets. 

    ![Verbindings pagina verwijderen](./media/private-link-service/delete-connection-page.png)
3. U ziet dat de status is gewijzigd in **verbroken**. Vervolgens ziet u dat het eind punt verdwijnt uit de lijst. 

## <a name="validate-that-the-private-link-connection-works"></a>Controleren of de verbinding van de Private Link werkt
U moet controleren of de resources binnen het virtuele netwerk van het persoonlijke eind punt verbinding maken met uw Azure Relay naam ruimte via het bijbehorende privé-IP-adres.

Voor deze test maakt u een virtuele machine door de stappen te volgen in de [virtuele Windows-machine maken in de Azure Portal](../virtual-machines/windows/quick-create-portal.md)

Op het tabblad **netwerk** : 

1. Geef het **virtuele netwerk** en het **subnet** op. U moet het Virtual Network selecteren waarop u het persoonlijke eind punt hebt geïmplementeerd.
2. Geef een **open bare IP-** resource op.
3. Selecteer voor **NIC-netwerk beveiligings groep** de optie **geen**.
4. Selecteer **Nee** voor **taak verdeling**.

Maak verbinding met de virtuele machine en open de opdracht regel en voer de volgende opdracht uit:

```console
nslookup <your-relay-namespace-name>.servicebus.windows.net
```

U ziet een resultaat dat er als volgt uitziet. 

```console
Non-authoritative answer:
Name:    <namespace-name>.privatelink.servicebus.windows.net
Address:  10.0.0.4 (private IP address associated with the private endpoint)
Aliases:  <namespace-name>.servicebus.windows.net
```

## <a name="limitations-and-design-considerations"></a>Beperkingen en overwegingen bij het ontwerp

### <a name="design-considerations"></a>Overwegingen bij het ontwerpen
- Zie [prijzen voor persoonlijke Azure-koppelingen](https://azure.microsoft.com/pricing/details/private-link/)voor prijs informatie.

### <a name="limitations"></a>Beperkingen 
- Maximum aantal privé-eind punten per Azure Relay naam ruimte: 64.
- Maximum aantal Azure Relay-naam ruimten met persoonlijke eind punten per abonnement: 64.
- De regels voor de netwerk beveiligings groep (NSG) en User-Defined routes zijn niet van toepassing op een privé-eind punt. Zie voor meer informatie [Azure Private Link service: beperkingen](../private-link/private-link-service-overview.md#limitations)

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [Azure Private Link](../private-link/private-link-service-overview.md)
- Meer informatie over [Azure relay](relay-what-is-it.md)
