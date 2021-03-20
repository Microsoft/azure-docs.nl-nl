---
title: Power shell gebruiken voor het beheren van Traffic Manager in azure
description: Met dit leer traject kunt u aan de slag met Azure PowerShell voor Traffic Manager.
services: traffic-manager
documentationcenter: na
author: duongau
ms.service: traffic-manager
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/16/2017
ms.author: duau
ms.openlocfilehash: 21076fe23301c189d9987f78706cc81691ce7a4f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "89400531"
---
# <a name="using-powershell-to-manage-traffic-manager"></a>Power shell gebruiken voor het beheren van Traffic Manager

Azure Resource Manager is de voorkeurs beheer interface voor services in Azure. Azure Traffic Manager-profielen kunnen worden beheerd met behulp van Azure Resource Manager-Api's en-hulpprogram ma's.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="resource-model"></a>Resourcemodel

Azure Traffic Manager is geconfigureerd met een verzameling instellingen die een Traffic Manager profiel worden genoemd. Dit profiel bevat DNS-instellingen, instellingen voor verkeers routering, instellingen voor eindpunt bewaking en een lijst met Service-eind punten waarop verkeer wordt gerouteerd.

Elk Traffic Manager profiel wordt vertegenwoordigd door een resource van het type ' TrafficManagerProfiles '. Op REST API niveau is de URI voor elk profiel als volgt:

`https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Network/trafficManagerProfiles/{profile-name}?api-version={api-version}`

## <a name="setting-up-azure-powershell"></a>Azure PowerShell instellen

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Deze instructies gebruiken Microsoft Azure PowerShell. In het volgende artikel wordt uitgelegd hoe u Azure PowerShell installeert en configureert.

* [Azure PowerShell installeren en configureren](/powershell/azure/)

In de voor beelden in dit artikel wordt ervan uitgegaan dat u een bestaande resource groep hebt. U kunt een resource groep maken met de volgende opdracht:

```powershell
New-AzResourceGroup -Name MyRG -Location "West US"
```

> [!NOTE]
> Azure Resource Manager vereist dat alle resource groepen een locatie hebben. Deze locatie wordt gebruikt als de standaard waarde voor resources die zijn gemaakt in die resource groep. Omdat Traffic Manager profiel bronnen echter globaal zijn, niet regionaal, heeft de keuze van de locatie van de resource groep geen invloed op Azure Traffic Manager.

## <a name="create-a-traffic-manager-profile"></a>Een Traffic Manager-profiel maken

Gebruik de cmdlet om een Traffic Manager profiel te maken `New-AzTrafficManagerProfile` :

```powershell
$TmProfile = New-AzTrafficManagerProfile -Name MyProfile -ResourceGroupName MyRG -TrafficRoutingMethod Performance -RelativeDnsName contoso -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"
```

De volgende tabel beschrijft de para meters:

| Parameter | Beschrijving |
| --- | --- |
| Name |De resource naam voor de Traffic Manager-profiel bron. Profielen in dezelfde resource groep moeten unieke namen hebben. Deze naam is gescheiden van de DNS-naam die wordt gebruikt voor DNS-query's. |
| ResourceGroupName |De naam van de resource groep die de profiel bron bevat. |
| TrafficRoutingMethod |Hiermee geeft u de routerings methode voor verkeer die wordt gebruikt om te bepalen welk eind punt wordt geretourneerd als antwoord op een DNS-query. Mogelijke waarden zijn ' performance ', ' Weightd ' of ' Priority '. |
| RelativeDnsName |Hiermee geeft u het gedeelte hostnaam van de DNS-naam op die door dit Traffic Manager profiel wordt opgegeven. Deze waarde wordt gecombineerd met de DNS-domein naam die wordt gebruikt door Azure Traffic Manager om de Fully Qualified Domain Name (FQDN) van het profiel te vormen. De waarde van ' Contoso ' wordt bijvoorbeeld ingesteld op ' contoso.trafficmanager.net '. |
| TTL |Hiermee geeft u de DNS-TTL (time-to-Live) op (in seconden). Deze TTL informeert de lokale DNS-resolvers en DNS-clients hoe lang DNS-antwoorden in de cache moeten worden opgeslagen voor dit Traffic Manager profiel. |
| MonitorProtocol |Hiermee geeft u het protocol op dat moet worden gebruikt om de eindpunt status te controleren. Mogelijke waarden zijn HTTP en HTTPS. |
| MonitorPort |Specificeert de TCP-poort die wordt gebruikt om de status van het eind punt te controleren. |
| MonitorPath |Hiermee geeft u het pad ten opzichte van de domein naam van het eind punt dat wordt gebruikt voor het testen van de eindpunt status |

De cmdlet maakt een Traffic Manager profiel in Azure en retourneert een overeenkomend profiel object in Power shell. Het profiel bevat op dit moment geen eind punten. Zie Traffic Manager-eind punten toevoegen voor meer informatie over het toevoegen van eind punten aan een Traffic Manager profiel.

## <a name="get-a-traffic-manager-profile"></a>Een Traffic Manager profiel ophalen

Als u een bestaand Traffic Manager profiel object wilt ophalen, gebruikt u de `Get-AzTrafficManagerProfle` cmdlet:

```powershell
$TmProfile = Get-AzTrafficManagerProfile -Name MyProfile -ResourceGroupName MyRG
```

Met deze cmdlet wordt een Traffic Manager profiel object geretourneerd.

## <a name="update-a-traffic-manager-profile"></a>Een Traffic Manager profiel bijwerken

Het wijzigen van Traffic Manager profielen volgt een proces van drie stappen:

1. Haal het profiel `Get-AzTrafficManagerProfile` op met of gebruik het profiel dat wordt geretourneerd door `New-AzTrafficManagerProfile` .
2. Wijzig het profiel. U kunt eind punten toevoegen en verwijderen of eind punt-of profiel parameters wijzigen. Deze wijzigingen zijn off-line bewerkingen. U wijzigt alleen het lokale object in het geheugen dat het profiel vertegenwoordigt.
3. Uw wijzigingen door voeren met de `Set-AzTrafficManagerProfile` cmdlet.

Alle profiel eigenschappen kunnen worden gewijzigd, met uitzonde ring van de RelativeDnsName van het profiel. Als u de RelativeDnsName wilt wijzigen, moet u het profiel en een nieuw profiel met een nieuwe naam verwijderen.

In het volgende voor beeld ziet u hoe u de TTL van het profiel wijzigt:

```powershell
$TmProfile = Get-AzTrafficManagerProfile -Name MyProfile -ResourceGroupName MyRG
$TmProfile.Ttl = 300
Set-AzTrafficManagerProfile -TrafficManagerProfile $TmProfile
```

Er zijn drie soorten Traffic Manager-eind punten:

1. **Azure-eind punten** zijn services die worden gehost in azure
2. **Externe eind punten** zijn services die buiten Azure worden gehost
3. **Geneste eind punten** worden gebruikt om geneste hiërarchieën van Traffic Manager profielen te maken. Met geneste eind punten kunt u geavanceerde configuratie van verkeer routeren voor complexe toepassingen.

In alle drie de gevallen kunnen eind punten op twee manieren worden toegevoegd:

1. U gebruikt een proces van drie stappen dat eerder is beschreven. Het voor deel van deze methode is dat verschillende wijzigingen in het eind punt kunnen worden aangebracht in één update.
2. De cmdlet New-AzTrafficManagerEndpoint gebruiken. Met deze cmdlet wordt een eind punt toegevoegd aan een bestaand Traffic Manager profiel in één bewerking.

## <a name="adding-azure-endpoints"></a>Azure-eindpunten toevoegen

Referentie services van Azure-eind punten die worden gehost in Azure. Twee typen Azure-eind punten worden ondersteund:

1. Azure App Service
2. Azure PublicIpAddress-resources (die kunnen worden gekoppeld aan een Load Balancer of een NIC van een virtuele machine). Aan de PublicIpAddress moet een DNS-naam zijn toegewezen om te worden gebruikt in Traffic Manager.

In elk geval:

* De service wordt opgegeven met de para meter ' targetResourceId ' van `Add-AzTrafficManagerEndpointConfig` of `New-AzTrafficManagerEndpoint` .
* De ' target ' en ' EndpointLocation ' worden geïmpliceerd door de TargetResourceId.
* Opgeven van het gewicht is optioneel. Gewichten worden alleen gebruikt als het profiel is geconfigureerd voor het gebruik van de methode ' gewogen ' verkeers routering. Anders worden ze genegeerd. Indien opgegeven, moet de waarde een getal zijn tussen 1 en 1000. De standaard waarde is 1.
* Het opgeven van de prioriteit is optioneel. Prioriteiten worden alleen gebruikt als het profiel is geconfigureerd voor het gebruik van de methode voor de route ring van het verkeer met de prioriteit. Anders worden ze genegeerd. Geldige waarden zijn 1 tot 1000 met lagere waarden die een hogere prioriteit aangeven. Indien opgegeven voor één eind punt, moeten deze worden opgegeven voor alle eind punten. Als u dit weglaat, worden de standaard waarden die beginnen van ' 1 ' toegepast in de volg orde waarin de eind punten worden weer gegeven.

### <a name="example-1-adding-app-service-endpoints-using-add-aztrafficmanagerendpointconfig"></a>Voor beeld 1: App Service-eind punten toevoegen met `Add-AzTrafficManagerEndpointConfig`

In dit voor beeld maken we een Traffic Manager profiel en voegen we twee App Service-eind punten toe met behulp van de `Add-AzTrafficManagerEndpointConfig` cmdlet.

```powershell
$TmProfile = New-AzTrafficManagerProfile -Name myprofile -ResourceGroupName MyRG -TrafficRoutingMethod Performance -RelativeDnsName myapp -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"
$webapp1 = Get-AzWebApp -Name webapp1
Add-AzTrafficManagerEndpointConfig -EndpointName webapp1ep -TrafficManagerProfile $TmProfile -Type AzureEndpoints -TargetResourceId $webapp1.Id -EndpointStatus Enabled
$webapp2 = Get-AzWebApp -Name webapp2
Add-AzTrafficManagerEndpointConfig -EndpointName webapp2ep -TrafficManagerProfile $TmProfile -Type AzureEndpoints -TargetResourceId $webapp2.Id -EndpointStatus Enabled
Set-AzTrafficManagerProfile -TrafficManagerProfile $TmProfile
```
### <a name="example-2-adding-a-publicipaddress-endpoint-using-new-aztrafficmanagerendpoint"></a>Voor beeld 2: een publicIpAddress-eind punt toevoegen met `New-AzTrafficManagerEndpoint`

In dit voor beeld wordt een resource met een openbaar IP-adres toegevoegd aan het Traffic Manager profiel. Het open bare IP-adres moet een DNS-naam hebben en kan worden gebonden aan de NIC van een virtuele machine of op een load balancer.

```powershell
$ip = Get-AzPublicIpAddress -Name MyPublicIP -ResourceGroupName MyRG
New-AzTrafficManagerEndpoint -Name MyIpEndpoint -ProfileName MyProfile -ResourceGroupName MyRG -Type AzureEndpoints -TargetResourceId $ip.Id -EndpointStatus Enabled
```

## <a name="adding-external-endpoints"></a>Externe eindpunten toevoegen

Traffic Manager gebruikt externe eind punten om verkeer te sturen naar services die buiten Azure worden gehost. Net als bij Azure-eind punten kunnen externe eind punten worden toegevoegd met behulp `Add-AzTrafficManagerEndpointConfig` van gevolgd door `Set-AzTrafficManagerProfile` , of `New-AzTrafficManagerEndpoint` .

Wanneer u externe eind punten opgeeft:

* De domein naam van het eind punt moet worden opgegeven met de para meter target
* Als de Traffic-routerings methode ' prestaties ' wordt gebruikt, is de ' EndpointLocation ' vereist. Anders is het optioneel. De waarde moet een [geldige naam](https://azure.microsoft.com/regions/)zijn van een Azure-regio.
* ' Weight ' en ' Priority ' zijn optioneel.

### <a name="example-1-adding-external-endpoints-using-add-aztrafficmanagerendpointconfig-and-set-aztrafficmanagerprofile"></a>Voor beeld 1: externe eind punten toevoegen met `Add-AzTrafficManagerEndpointConfig` en `Set-AzTrafficManagerProfile`

In dit voor beeld maken we een Traffic Manager profiel, voegt u twee externe eind punten toe en voert u de wijzigingen door.

```powershell
$TmProfile = New-AzTrafficManagerProfile -Name myprofile -ResourceGroupName MyRG -TrafficRoutingMethod Performance -RelativeDnsName myapp -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"
Add-AzTrafficManagerEndpointConfig -EndpointName eu-endpoint -TrafficManagerProfile $TmProfile -Type ExternalEndpoints -Target app-eu.contoso.com -EndpointLocation "North Europe" -EndpointStatus Enabled
Add-AzTrafficManagerEndpointConfig -EndpointName us-endpoint -TrafficManagerProfile $TmProfile -Type ExternalEndpoints -Target app-us.contoso.com -EndpointLocation "Central US" -EndpointStatus Enabled
Set-AzTrafficManagerProfile -TrafficManagerProfile $TmProfile
```

### <a name="example-2-adding-external-endpoints-using-new-aztrafficmanagerendpoint"></a>Voor beeld 2: externe eind punten toevoegen met `New-AzTrafficManagerEndpoint`

In dit voor beeld voegen we een extern eind punt toe aan een bestaand profiel. Het profiel wordt opgegeven met behulp van de namen van het profiel en de resource groep.

```powershell
New-AzTrafficManagerEndpoint -Name eu-endpoint -ProfileName MyProfile -ResourceGroupName MyRG -Type ExternalEndpoints -Target app-eu.contoso.com -EndpointStatus Enabled
```

## <a name="adding-nested-endpoints"></a>' Geneste ' eind punten toevoegen

Elk Traffic Manager profiel bevat één methode voor het routeren van verkeer. Er zijn echter scenario's die meer geavanceerde verkeers routering vereisen dan de route ring van een enkel Traffic Manager profiel. U kunt Traffic Manager profielen nesten om de voor delen van meer dan één Traffic-routerings methode te combi neren. Met geneste profielen kunt u het standaard gedrag van Traffic Manager onderdrukken om grotere en complexere toepassings implementaties te ondersteunen. Zie [Geneste Traffic Manager profielen](traffic-manager-nested-profiles.md)voor meer gedetailleerde voor beelden.

Geneste eind punten worden geconfigureerd in het bovenliggende profiel, met behulp van een specifiek eindpunt type, ' NestedEndpoints '. Wanneer u geneste eind punten opgeeft:

* Het eind punt moet worden opgegeven met de para meter ' targetResourceId '
* Als de Traffic-routerings methode ' prestaties ' wordt gebruikt, is de ' EndpointLocation ' vereist. Anders is het optioneel. De waarde moet een [geldige naam](https://azure.microsoft.com/regions/)zijn van een Azure-regio.
* Het gewicht en de prioriteit zijn optioneel voor Azure-eind punten.
* De para meter MinChildEndpoints is optioneel. De standaard waarde is 1. Als het aantal beschik bare eind punten lager is dan deze drempel waarde, beschouwt het bovenliggende profiel het onderliggende profiel ' verslechterd ' en wordt het verkeer omleiden naar de andere eind punten in het bovenliggende profiel.

### <a name="example-1-adding-nested-endpoints-using-add-aztrafficmanagerendpointconfig-and-set-aztrafficmanagerprofile"></a>Voor beeld 1: geneste eind punten toevoegen met `Add-AzTrafficManagerEndpointConfig` en `Set-AzTrafficManagerProfile`

In dit voor beeld maken we nieuwe Traffic Manager onderliggende en bovenliggende profielen, voegt u het onderliggende element toe als een genest eind punt aan het bovenliggende item en voert u de wijzigingen door.

```powershell
$child = New-AzTrafficManagerProfile -Name child -ResourceGroupName MyRG -TrafficRoutingMethod Priority -RelativeDnsName child -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"
$parent = New-AzTrafficManagerProfile -Name parent -ResourceGroupName MyRG -TrafficRoutingMethod Performance -RelativeDnsName parent -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"
Add-AzTrafficManagerEndpointConfig -EndpointName child-endpoint -TrafficManagerProfile $parent -Type NestedEndpoints -TargetResourceId $child.Id -EndpointStatus Enabled -EndpointLocation "North Europe" -MinChildEndpoints 2
Set-AzTrafficManagerProfile -TrafficManagerProfile $parent
```

In dit voor beeld hebben we geen andere eind punten aan de onderliggende of bovenliggende profielen toegevoegd.

### <a name="example-2-adding-nested-endpoints-using-new-aztrafficmanagerendpoint"></a>Voor beeld 2: geneste eind punten toevoegen met `New-AzTrafficManagerEndpoint`

In dit voor beeld voegen we een bestaand onderliggend profiel als een genest eind punt toe aan een bestaand bovenliggend profiel. Het profiel wordt opgegeven met behulp van de namen van het profiel en de resource groep.

```powershell
$child = Get-AzTrafficManagerEndpoint -Name child -ResourceGroupName MyRG
New-AzTrafficManagerEndpoint -Name child-endpoint -ProfileName parent -ResourceGroupName MyRG -Type NestedEndpoints -TargetResourceId $child.Id -EndpointStatus Enabled -EndpointLocation "North Europe" -MinChildEndpoints 2
```

## <a name="adding-endpoints-from-another-subscription"></a>Eind punten toevoegen vanuit een ander abonnement

Traffic Manager kunt werken met eind punten van verschillende abonnementen. U moet overschakelen naar het abonnement met het eind punt dat u wilt toevoegen om de vereiste invoer voor Traffic Manager op te halen. Vervolgens moet u overschakelen naar de abonnementen met het Traffic Manager profiel en het eind punt hieraan toevoegen. In het onderstaande voor beeld ziet u hoe u dit doet met een openbaar IP-adres.

```powershell
Set-AzContext -SubscriptionId $EndpointSubscription
$ip = Get-AzPublicIpAddress -Name $IpAddressName -ResourceGroupName $EndpointRG

Set-AzContext -SubscriptionId $trafficmanagerSubscription
New-AzTrafficManagerEndpoint -Name $EndpointName -ProfileName $ProfileName -ResourceGroupName $TrafficManagerRG -Type AzureEndpoints -TargetResourceId $ip.Id -EndpointStatus Enabled
```

## <a name="update-a-traffic-manager-endpoint"></a>Een Traffic Manager-eind punt bijwerken

Er zijn twee manieren om een bestaand Traffic Manager-eind punt bij te werken:

1. Haal het Traffic Manager profiel `Get-AzTrafficManagerProfile` op met, werk de eindpunt eigenschappen binnen het profiel bij en voer de wijzigingen door met behulp van `Set-AzTrafficManagerProfile` . Deze methode heeft als voor deel dat er meer dan één eind punt in één bewerking kan worden bijgewerkt.
2. Haal het Traffic Manager-eind punt `Get-AzTrafficManagerEndpoint` op met, werk de eindpunt eigenschappen bij en voer de wijzigingen door met behulp van `Set-AzTrafficManagerEndpoint` . Deze methode is eenvoudiger, omdat het niet nodig is om te indexeren in de matrix met eind punten in het profiel.

### <a name="example-1-updating-endpoints-using-get-aztrafficmanagerprofile-and-set-aztrafficmanagerprofile"></a>Voor beeld 1: eind punten bijwerken met `Get-AzTrafficManagerProfile` en `Set-AzTrafficManagerProfile`

In dit voor beeld wijzigen we de prioriteit op twee eind punten binnen een bestaand profiel.

```powershell
$TmProfile = Get-AzTrafficManagerProfile -Name myprofile -ResourceGroupName MyRG
$TmProfile.Endpoints[0].Priority = 2
$TmProfile.Endpoints[1].Priority = 1
Set-AzTrafficManagerProfile -TrafficManagerProfile $TmProfile
```

### <a name="example-2-updating-an-endpoint-using-get-aztrafficmanagerendpoint-and-set-aztrafficmanagerendpoint"></a>Voor beeld 2: een eind punt bijwerken met `Get-AzTrafficManagerEndpoint` en `Set-AzTrafficManagerEndpoint`

In dit voor beeld wijzigen we het gewicht van één eind punt in een bestaand profiel.

```powershell
$endpoint = Get-AzTrafficManagerEndpoint -Name myendpoint -ProfileName myprofile -ResourceGroupName MyRG -Type ExternalEndpoints
$endpoint.Weight = 20
Set-AzTrafficManagerEndpoint -TrafficManagerEndpoint $endpoint
```

## <a name="enabling-and-disabling-endpoints-and-profiles"></a>Eind punten en-profielen in-en uitschakelen

Traffic Manager kunnen afzonderlijke eind punten worden in-en uitgeschakeld, en kan het in-en uitschakelen van volledige profielen worden toegestaan.
Deze wijzigingen kunnen worden aangebracht door het eind punt of de profiel resources op te halen/in te stellen. Om deze algemene bewerkingen te stroom lijnen, worden ze ook ondersteund via speciale cmdlets.

### <a name="example-1-enabling-and-disabling-a-traffic-manager-profile"></a>Voor beeld 1: een Traffic Manager profiel in-en uitschakelen

Gebruik om een Traffic Manager profiel in te scha kelen `Enable-AzTrafficManagerProfile` . Het profiel kan worden opgegeven met behulp van een profiel object. Het profiel object kan worden door gegeven via de pijp lijn of door gebruik te maken van de para meter-TrafficManagerProfile. In dit voor beeld geven we het profiel op voor het profiel en de naam van de resource groep.

```powershell
Enable-AzTrafficManagerProfile -Name MyProfile -ResourceGroupName MyResourceGroup
```

Een Traffic Manager profiel uitschakelen:

```powershell
Disable-AzTrafficManagerProfile -Name MyProfile -ResourceGroupName MyResourceGroup
```

De Disable-AzTrafficManagerProfile-cmdlet vraagt om bevestiging. Deze prompt kan worden onderdrukt met de para meter-Force.

### <a name="example-2-enabling-and-disabling-a-traffic-manager-endpoint"></a>Voor beeld 2: een Traffic Manager-eind punt in-en uitschakelen

Gebruik om een Traffic Manager eind punt in te scha kelen `Enable-AzTrafficManagerEndpoint` . Er zijn twee manieren om het eind punt op te geven

1. Het gebruik van een TrafficManagerEndpoint-object dat via de pijp lijn wordt door gegeven of de para meter-TrafficManagerEndpoint
2. Met behulp van de naam van het eind punt, het type eind punt, de profiel naam en de naam van de resource groep:

```powershell
Enable-AzTrafficManagerEndpoint -Name MyEndpoint -Type AzureEndpoints -ProfileName MyProfile -ResourceGroupName MyRG
```

Op dezelfde manier kunt u een Traffic Manager-eind punt uitschakelen:

```powershell
Disable-AzTrafficManagerEndpoint -Name MyEndpoint -Type AzureEndpoints -ProfileName MyProfile -ResourceGroupName MyRG -Force
```

Net als bij `Disable-AzTrafficManagerProfile` heeft de `Disable-AzTrafficManagerEndpoint` cmdlet om bevestiging gevraagd. Deze prompt kan worden onderdrukt met de para meter-Force.

## <a name="delete-a-traffic-manager-endpoint"></a>Een Traffic Manager-eind punt verwijderen

Als u afzonderlijke eind punten wilt verwijderen, gebruikt u de `Remove-AzTrafficManagerEndpoint` cmdlet:

```powershell
Remove-AzTrafficManagerEndpoint -Name MyEndpoint -Type AzureEndpoints -ProfileName MyProfile -ResourceGroupName MyRG
```

Met deze cmdlet wordt om bevestiging gevraagd. Deze prompt kan worden onderdrukt met de para meter-Force.

## <a name="delete-a-traffic-manager-profile"></a>Een Traffic Manager profiel verwijderen

Als u een Traffic Manager profiel wilt verwijderen, gebruikt u de `Remove-AzTrafficManagerProfile` cmdlet, waarbij u de namen van het profiel en de resource groep opgeeft:

```powershell
Remove-AzTrafficManagerProfile -Name MyProfile -ResourceGroupName MyRG [-Force]
```

Met deze cmdlet wordt om bevestiging gevraagd. Deze prompt kan worden onderdrukt met de para meter-Force.

Het profiel dat moet worden verwijderd, kan ook worden opgegeven met behulp van een profiel object:

```powershell
$TmProfile = Get-AzTrafficManagerProfile -Name MyProfile -ResourceGroupName MyRG
Remove-AzTrafficManagerProfile -TrafficManagerProfile $TmProfile [-Force]
```

Deze reeks kan ook worden gepiped:

```powershell
Get-AzTrafficManagerProfile -Name MyProfile -ResourceGroupName MyRG | Remove-AzTrafficManagerProfile [-Force]
```

## <a name="next-steps"></a>Volgende stappen

[Traffic Manager bewaking](traffic-manager-monitoring.md)

[Prestatieoverwegingen voor Traffic Manager](traffic-manager-performance-considerations.md)
