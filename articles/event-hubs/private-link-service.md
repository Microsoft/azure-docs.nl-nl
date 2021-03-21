---
title: Azure Event Hubs integreren met de persoonlijke koppelings service van Azure
description: Meer informatie over het integreren van Azure Event Hubs met de persoonlijke koppelings service van Azure
ms.date: 08/22/2020
ms.topic: article
ms.openlocfilehash: 996779e103dae2d2d950f447d2ac72667fc9e754
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94427748"
---
# <a name="allow-access-to-azure-event-hubs-namespaces-via-private-endpoints"></a>Toegang tot Azure Event Hubs-naam ruimten toestaan via persoonlijke eind punten 
Met Azure Private Link service kunt u toegang krijgen tot Azure-Services (bijvoorbeeld Azure Event Hubs, Azure Storage en Azure Cosmos DB) en door Azure gehoste klanten/partner services via een **persoonlijk eind punt** in uw virtuele netwerk.

Een persoonlijk eind punt is een netwerk interface waarmee u privé en veilig kunt verbinden met een service die wordt aangestuurd door een persoonlijke Azure-koppeling. Het persoonlijke eind punt maakt gebruik van een privé-IP-adres van uw virtuele netwerk, waardoor de service in het virtuele netwerk effectief wordt. Al het verkeer naar de service kan worden gerouteerd via het privé-eindpunt, zodat er geen gateways, NAT-apparaten, ExpressRoute of VPN-verbindingen of openbare IP-adressen nodig zijn. Verkeer tussen uw virtuele netwerk en de services wordt via het backbonenetwerk van Microsoft geleid, waarmee de risico's van het openbare internet worden vermeden. U kunt verbinding maken met een exemplaar van een Azure-resource, zodat u het hoogste granulariteit krijgt in toegangsbeheer.

Zie [Wat is een Azure Private Link?](../private-link/private-link-overview.md) voor meer informatie.

> [!WARNING]
> Het inschakelen van persoonlijke eind punten kan verhinderen dat andere Azure-Services communiceren met Event Hubs.  Aanvragen die zijn geblokkeerd, zijn onder andere die van andere Azure-Services, van de Azure Portal, van de services logboek registratie en metrische gegevens, enzovoort. Als uitzonde ring kunt u toegang verlenen tot Event Hubs resources van bepaalde vertrouwde services, zelfs wanneer persoonlijke eind punten zijn ingeschakeld. Zie [Trusted Services](#trusted-microsoft-services)(Engelstalig) voor een lijst met vertrouwde services.

>[!NOTE]
> Deze functie wordt ondersteund voor zowel de **standaard** als de **toegewezen** laag. Het wordt niet ondersteund in de laag **basis** .

## <a name="add-a-private-endpoint-using-azure-portal"></a>Een persoonlijk eind punt toevoegen met Azure Portal

### <a name="prerequisites"></a>Vereisten

Als u een Event Hubs naam ruimte met een persoonlijke Azure-koppeling wilt integreren, hebt u de volgende entiteiten of machtigingen nodig:

- Een Event Hubs naam ruimte.
- Een Azure Virtual Network.
- Een subnet binnen het virtueel netwerk. U kunt het **standaard** subnet gebruiken. 
- Eigenaar-of Inzender machtigingen voor de naam ruimte en het virtuele netwerk.

Uw privé-eindpunt en het virtueel netwerk moeten zich in dezelfde regio bevinden. Wanneer u een regio voor het privé-eindpunt selecteert met behulp van de portal, worden er automatisch alleen virtuele netwerken gefilterd die zich in die regio bevinden. Uw naam ruimte kan zich in een andere regio bevinden.

Uw privé-eindpunt maakt gebruik van een privé IP-adres in uw virtueel netwerk.

### <a name="steps"></a>Stappen
Als u al een Event Hubs naam ruimte hebt, kunt u een koppeling voor een particuliere verbinding maken door de volgende stappen uit te voeren:

1. Meld u aan bij [Azure Portal](https://portal.azure.com). 
2. Typ in de zoek balk in **Event hubs**.
3. Selecteer de **naam ruimte** in de lijst waaraan u een persoonlijk eind punt wilt toevoegen.
4. Selecteer **netwerken** onder **instellingen** in het menu links.

    > [!NOTE]
    > U ziet het tabblad **netwerken** alleen voor **standaard** of **toegewezen** naam ruimten. 

    :::image type="content" source="./media/private-link-service/selected-networks-page.png" alt-text="Tabblad netwerken-opties voor geselecteerde netwerken" lightbox="./media/private-link-service/selected-networks-page.png":::    

    > [!NOTE]
    > Standaard is de optie **geselecteerde netwerken** geselecteerd. Als u geen IP-firewall regel opgeeft of een virtueel netwerk toevoegt, is de naam ruimte toegankelijk via het open bare Internet. 
1. Selecteer het tabblad **verbindingen met privé-eind punten** boven aan de pagina. 
1. Selecteer de knop **+ privé-eind punt** boven aan de pagina.

    :::image type="content" source="./media/private-link-service/private-link-service-3.png" alt-text="Pagina netwerken-tabblad verbindingen met privé-eind punten-koppeling persoonlijke eind punt toevoegen":::
7. Voer de volgende stappen uit op de pagina **basis beginselen** : 
    1. Selecteer het **Azure-abonnement** waarin u het persoonlijke eind punt wilt maken. 
    2. Selecteer de **resource groep** voor de persoonlijke eindpunt resource.
    3. Voer een **naam** in voor het persoonlijke eind punt. 
    5. Selecteer een **regio** voor het persoonlijke eind punt. Uw persoonlijke eind punt moet zich in dezelfde regio bevinden als uw virtuele netwerk, maar kan zich in een andere regio bevinden dan de resource van de persoonlijke koppeling waarmee u verbinding maakt. 
    6. Selecteer **volgende: Resource >** knop onder aan de pagina.

        ![Privé-eind punt maken-pagina basis beginselen](./media/private-link-service/create-private-endpoint-basics-page.png)
8. Voer de volgende stappen uit op de pagina **resource** :
    1. Als u **verbinding maken met een Azure-resource in mijn Directory** selecteert, voert u de volgende stappen uit om de verbindings methode te selecteren: 
        1. Selecteer het **Azure-abonnement** waarin uw **Event hubs naam ruimte** bestaat. 
        2. Selecteer voor **resource type** de optie **micro soft. EventHub/naam ruimten** voor het **bron type**.
        3. Selecteer voor **resource** een event hubs naam ruimte in de vervolg keuzelijst. 
        4. Controleer of de **doel-subresource** is ingesteld op **naam ruimte**.
        5. Selecteer **volgende: configuratie >** knop onder aan de pagina. 
        
            ![Privé-eind punt maken-resource pagina](./media/private-link-service/create-private-endpoint-resource-page.png)    
    2. Als u **verbinding maken met een Azure-resource selecteert op resource-id of alias**, voert u de volgende stappen uit:
        1. Voer de **resource-id** of **alias** in. Dit kan de resource-ID of alias zijn die iemand met u heeft gedeeld. De eenvoudigste manier om de resource-ID op te halen, is door te navigeren naar de Event Hubs naam ruimte in de Azure Portal en het gedeelte van de URI te kopiëren vanaf `/subscriptions/` . Zie de volgende afbeelding voor een voor beeld. 
        2. Voer een **naam ruimte** in voor de **subresource** van het doel. Het is het type van de subbron waartoe uw persoonlijke eind punt toegang heeft.
        3. Beschrijving Voer een **aanvraag bericht** in. De resource-eigenaar ziet dit bericht tijdens het beheer van de verbinding met een privé-eind punt.
        4. Selecteer vervolgens **volgende: configuratie >** knop onder aan de pagina.

            ![Persoonlijk eind punt maken-verbinden via Resource-ID](./media/private-link-service/connect-resource-id.png)
9. Selecteer op de pagina **configuratie** het subnet in een virtueel netwerk waarop u het persoonlijke eind punt wilt implementeren. 
    1. Selecteer een **virtueel netwerk**. In de vervolg keuzelijst worden alleen virtuele netwerken in het geselecteerde abonnement en de huidige locatie weer gegeven. 
    2. Selecteer een **subnet** in het virtuele netwerk dat u hebt geselecteerd. 
    3. Selecteer **volgende: labels >** knop onder aan de pagina. 

        ![Privé-eind punt maken-configuratie pagina](./media/private-link-service/create-private-endpoint-configuration-page.png)
10. Maak op de pagina **Tags** een wille keurige labels (namen en waarden) die u wilt koppelen aan de persoonlijke eindpunt resource. Selecteer vervolgens de knop **controleren + maken** onder aan de pagina. 
11. Controleer alle instellingen in het **overzicht en maken** en selecteer **maken** om het persoonlijke eind punt te maken.
    
    ![Privé-eind punt maken-pagina controleren en maken](./media/private-link-service/create-private-endpoint-review-create-page.png)
12. Controleer of de verbinding van het privé-eind punt dat u hebt gemaakt, wordt weer gegeven in de lijst met eind punten. In dit voor beeld wordt het persoonlijke eind punt automatisch goedgekeurd omdat u verbinding hebt gemaakt met een Azure-resource in uw directory en u voldoende machtigingen hebt. 

    ![Persoonlijk eind punt gemaakt](./media/private-link-service/private-endpoint-created.png)

[!INCLUDE [event-hubs-trusted-services](../../includes/event-hubs-trusted-services.md)]

Als u vertrouwde services toegang wilt verlenen tot uw naam ruimte, gaat u naar het tabblad **firewalls en virtuele netwerken** op de pagina **netwerk** en selecteert u **Ja** **Als u wilt dat vertrouwde micro soft-services deze firewall kunnen overs Laan?**. 

## <a name="add-a-private-endpoint-using-powershell"></a>Een persoonlijk eind punt toevoegen met Power shell
In het volgende voor beeld ziet u hoe u Azure PowerShell kunt gebruiken om een verbinding met een privé-eind punt te maken. Er wordt geen speciaal cluster voor u gemaakt. Volg de stappen in [dit artikel](event-hubs-dedicated-cluster-create-portal.md) om een toegewezen Event hubs-cluster te maken. 

```azurepowershell-interactive
# create resource group

$rgName = "<RESOURCE GROUP NAME>"
$vnetlocation = "<VIRTUAL NETWORK LOCATION>"
$vnetName = "<VIRTUAL NETWORK NAME>"
$subnetName = "<SUBNET NAME>"
$namespaceLocation = "<NAMESPACE LOCATION>"
$namespaceName = "<NAMESPACE NAME>"
$peConnectionName = "<PRIVATE ENDPOINT CONNECTION NAME>"

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

# create an event hubs namespace in a dedicated cluster
$namespaceResource = New-AzResource -Location $namespaceLocation `
                                    -ResourceName $namespaceName `
                                    -ResourceGroupName $rgName `
                                    -Sku @{name = "Standard"; capacity = 1} `
                                    -Properties @{clusterArmId = "/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventHub/clusters/<EVENT HUBS CLUSTER NAME>"} `
                                    -ResourceType "Microsoft.EventHub/namespaces" -ApiVersion "2018-01-01-preview"

# create private endpoint connection
$privateEndpointConnection = New-AzPrivateLinkServiceConnection `
                                -Name $peConnectionName `
                                -PrivateLinkServiceId $namespaceResource.ResourceId `
                                -GroupId "namespace"

# get subnet object that you will use later
$virtualNetwork = Get-AzVirtualNetwork -ResourceGroupName  $rgName -Name $vnetName
$subnet = $virtualNetwork | Select -ExpandProperty subnets `
                                | Where-Object  {$_.Name -eq $subnetName}  
   
# create a private endpoint   
$privateEndpoint = New-AzPrivateEndpoint -ResourceGroupName $rgName  `
                                -Name $vnetName   `
                                -Location $vnetlocation `
                                -Subnet  $subnet   `
                                -PrivateLinkServiceConnection $privateEndpointConnection

(Get-AzResource -ResourceId $namespaceResource.ResourceId -ExpandProperties).Properties


```

### <a name="configure-the-private-dns-zone"></a>De privé-DNS-zone configureren
Maak een privé-DNS-zone voor Event Hubs domein en maak een koppelings koppeling met het virtuele netwerk:

```azurepowershell-interactive
$zone = New-AzPrivateDnsZone -ResourceGroupName $rgName `
                            -Name "privatelink.servicebus.windows.net" 
 
$link  = New-AzPrivateDnsVirtualNetworkLink -ResourceGroupName $rgName `
                                            -ZoneName "privatelink.servicebus.windows.net" `
                                            -Name "mylink" `
                                            -VirtualNetworkId $virtualNetwork.Id  
 
$networkInterface = Get-AzResource -ResourceId $privateEndpoint.NetworkInterfaces[0].Id -ApiVersion "2019-04-01" 
 
foreach ($ipconfig in $networkInterface.properties.ipConfigurations) { 
    foreach ($fqdn in $ipconfig.properties.privateLinkConnectionProperties.fqdns) { 
        Write-Host "$($ipconfig.properties.privateIPAddress) $($fqdn)"  
        $recordName = $fqdn.split('.',2)[0] 
        $dnsZone = $fqdn.split('.',2)[1] 
        New-AzPrivateDnsRecordSet -Name $recordName -RecordType A -ZoneName "privatelink.servicebus.windows.net"  `
                                -ResourceGroupName $rgName -Ttl 600 `
                                -PrivateDnsRecords (New-AzPrivateDnsRecordConfig -IPv4Address $ipconfig.properties.privateIPAddress)  
    } 
}
```

## <a name="manage-private-endpoints-using-azure-portal"></a>Privé-eind punten beheren met Azure Portal

Wanneer u een privé-eindpunt maakt, moet de verbinding worden goedgekeurd. Als de resource waarvoor u een persoonlijk eind punt maakt zich in uw directory bevindt, kunt u de verbindings aanvraag goed keuren die u voldoende machtigingen hebt. Als u verbinding maakt met een Azure-resource in een andere Directory, moet u wachten tot de eigenaar van die resource uw verbindings aanvraag goed keuren.

Er zijn vier inrichtingsstatussen:

| Serviceactie | Status privé-eindpunt serviceconsument | Beschrijving |
|--|--|--|
| Geen | In behandeling | De verbinding wordt handmatig gemaakt en in afwachting van goedkeuring door de resource-eigenaar van de Private Link. |
| Goedkeuren | Goedgekeurd | De verbinding werd automatisch of handmatig goedgekeurd en is klaar om te worden gebruikt. |
| Afwijzen | Afgewezen | De verbinding werd afgewezen door de resource-eigenaar van de private link. |
| Verwijderen | Ontkoppeld | De verbinding is verwijderd door de resource-eigenaar van de private link, het privé-eindpunt wordt informatief en moet worden verwijderd voor opschoning. |
 
###  <a name="approve-reject-or-remove-a-private-endpoint-connection"></a>Een verbinding met een privé-eind punt goed keuren, afwijzen of verwijderen

1. Meld u aan bij Azure Portal.
2. Typ in de zoek balk in **Event hubs**.
3. Selecteer de **naam ruimte** die u wilt beheren.
4. Selecteer het tabblad **Netwerken**.
5. Ga naar de toepasselijke sectie hieronder op basis van de bewerking die u wilt: goed keuren, afwijzen of verwijderen.

### <a name="approve-a-private-endpoint-connection"></a>Een verbinding met een privé-eind punt goed keuren
1. Als er verbindingen zijn die in behandeling zijn, ziet u een verbinding die wordt weer gegeven met in **behandeling** in de inrichtings status. 
2. Selecteer het **privé-eind punt** dat u wilt goed keuren
3. Selecteer de knop **goed keuren** .

    ![Keur het privé-eindpunt goed](./media/private-link-service/approve-private-endpoint.png)
4. Voeg op de pagina **verbinding goed keuren** een opmerking toe (optioneel) en selecteer **Ja**. Als u **Nee** selecteert, gebeurt er niets. 
5. Als het goed is, ziet u de status van de verbinding met een privé-eind punt in de lijst gewijzigd in **goedgekeurd**. 

### <a name="reject-a-private-endpoint-connection"></a>Een persoonlijke eindpunt verbinding weigeren

1. Als er particuliere endpoint-verbindingen zijn die u wilt weigeren, ongeacht of het een aanvraag in behandeling of een bestaande verbinding is, selecteert u de verbinding en klikt u op de knop **afwijzen** .

    ![Persoonlijk eind punt afwijzen](./media/private-link-service/private-endpoint-reject-button.png)
2. Voer op de pagina **verbinding afwijzen** een opmerking in (optioneel) en selecteer **Ja**. Als u **Nee** selecteert, gebeurt er niets. 
3. Als het goed is, ziet u de status van de verbinding met een privé-eind punt in de lijst gewijzigd in **afgewezen**. 

### <a name="remove-a-private-endpoint-connection"></a>Een verbinding met een privé-eind punt verwijderen

1. Als u een verbinding met een privé-eind punt wilt verwijderen, selecteert u deze in de lijst en selecteert u **verwijderen** op de werk balk.
2. Selecteer op de pagina **verbinding verwijderen** de optie **Ja** om het verwijderen van het persoonlijke eind punt te bevestigen. Als u **Nee** selecteert, gebeurt er niets.
3. U ziet dat de status is gewijzigd in **verbroken**. Vervolgens ziet u dat het eind punt verdwijnt uit de lijst.

## <a name="validate-that-the-private-link-connection-works"></a>Controleren of de verbinding van de Private Link werkt

U moet valideren dat bronnen binnen het virtuele netwerk van het persoonlijke eind punt verbinding maken met uw Event Hubs naam ruimte via een privé-IP-adres en dat ze de juiste integratie van de persoonlijke DNS-zone hebben.

Maak eerst een nieuwe virtuele machine door de instructies te volgen in [Een virtuele Windows-machine in de Azure Portal maken](../virtual-machines/windows/quick-create-portal.md)

Op het tabblad **netwerk** : 

1. Geef het **virtuele netwerk** en het **subnet** op. U moet het Virtual Network selecteren waarop u het persoonlijke eind punt hebt geïmplementeerd.
2. Geef een **open bare IP-** resource op.
3. Selecteer voor **NIC-netwerk beveiligings groep** de optie **geen**.
4. Selecteer **Nee** voor **taak verdeling**.

Maak verbinding met de virtuele machine, open de opdracht regel en voer de volgende opdracht uit:

```console
nslookup <event-hubs-namespace-name>.servicebus.windows.net
```

U ziet een resultaat dat er als volgt uitziet. 

```console
Non-authoritative answer:
Name:    <event-hubs-namespace-name>.privatelink.servicebus.windows.net
Address:  10.0.0.4 (private IP address associated with the private endpoint)
Aliases:  <event-hubs-namespace-name>.servicebus.windows.net
```

## <a name="limitations-and-design-considerations"></a>Beperkingen en ontwerp overwegingen

**Pricing**: Zie [Prijs van Azure Private Link](https://azure.microsoft.com/pricing/details/private-link/) voor meer informatie over prijzen.

**Beperkingen**: deze functie is beschikbaar in alle open bare Azure-regio's.

**Maximum aantal privé-eind punten per Event hubs naam ruimte**: 120.

Zie [Azure Private Link-service: beperkingen](../private-link/private-link-service-overview.md#limitations) voor meer informatie

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [Azure Private Link](../private-link/private-link-service-overview.md)
- Meer informatie over [Azure Event hubs](event-hubs-about.md)
