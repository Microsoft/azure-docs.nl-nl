---
title: Problemen met slechte gateway fouten oplossen-Azure-toepassing gateway
description: 'Informatie over het oplossen van Application Gateway server fout: 502-webserver heeft een ongeldig antwoord ontvangen terwijl deze als gateway of proxy server fungeert.'
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: troubleshooting
ms.date: 11/16/2019
ms.author: amsriva
ms.openlocfilehash: d44f9109540c3899ab50bd5c4c02afa19045bafb
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/26/2020
ms.locfileid: "96182934"
---
# <a name="troubleshooting-bad-gateway-errors-in-application-gateway"></a>Fouten met ongeldige gateway oplossen in Application Gateway

Informatie over het oplossen van problemen met slechte gateway (502) die worden ontvangen bij het gebruik van Azure-toepassing gateway.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="overview"></a>Overzicht

Na het configureren van een toepassings gateway is een van de fouten die u ziet ' server fout: 502-webserver heeft een ongeldig antwoord ontvangen terwijl deze als gateway of proxy server wordt ingezet '. Deze fout kan de volgende oorzaken hebben:

* NSG, UDR of Custom DNS blokkeert de toegang tot back-end-groeps leden.
* Back-end-Vm's of exemplaren van virtuele-machine schaal sets reageren niet op de standaard status test.
* Ongeldige of onjuiste configuratie van aangepaste statustests.
* De [back-end-pool van Azure-toepassing gateway is niet geconfigureerd of is leeg](#empty-backendaddresspool).
* Geen van de Vm's of exemplaren in een [schaalset voor virtuele machines is in orde](#unhealthy-instances-in-backendaddresspool).
* [Time-out of verbindings problemen](#request-time-out) met gebruikers aanvragen aanvragen.

## <a name="network-security-group-user-defined-route-or-custom-dns-issue"></a>Netwerk beveiligings groep, door de gebruiker gedefinieerde route of aangepast DNS-probleem

### <a name="cause"></a>Oorzaak

Als de toegang tot de back-end wordt geblokkeerd vanwege een NSG-, UDR-of aangepaste DNS-exemplaar, kunnen de Application Gateway-instanties de back-end-pool niet bereiken. Dit veroorzaakt test fouten, wat resulteert in 502-fouten.

De NSG/UDR kan aanwezig zijn in het subnet van de toepassings gateway of in het subnet waar de virtuele machines van de toepassing worden geïmplementeerd.

Op dezelfde manier kan de aanwezigheid van een aangepaste DNS in het VNet ook problemen veroorzaken. Een FQDN die wordt gebruikt voor leden van de back-end-groep wordt mogelijk niet correct omgezet door de door de gebruiker geconfigureerde DNS-server voor het VNet.

### <a name="solution"></a>Oplossing

Valideer de NSG-, UDR-en DNS-configuratie door de volgende stappen uit te voeren:

* Controleer de Nsg's die zijn gekoppeld aan het Application Gateway-subnet. Zorg ervoor dat de communicatie met de back-end niet wordt geblokkeerd.
* Controleer de UDR die zijn gekoppeld aan het Application Gateway-subnet. Zorg ervoor dat de UDR geen verkeer doorstuurt van het back-end-subnet. Controleer bijvoorbeeld op route ring naar virtuele netwerk apparaten of standaard routes die worden geadverteerd naar het subnet van de toepassings gateway via ExpressRoute/VPN.

```azurepowershell
$vnet = Get-AzVirtualNetwork -Name vnetName -ResourceGroupName rgName
Get-AzVirtualNetworkSubnetConfig -Name appGwSubnet -VirtualNetwork $vnet
```

* Effectief NSG controleren en routeren met de back-end-VM

```azurepowershell
Get-AzEffectiveNetworkSecurityGroup -NetworkInterfaceName nic1 -ResourceGroupName testrg
Get-AzEffectiveRouteTable -NetworkInterfaceName nic1 -ResourceGroupName testrg
```

* Controleer de aanwezigheid van aangepaste DNS-server in het VNet. DNS kan worden gecontroleerd door Details van de VNet-eigenschappen in de uitvoer te bekijken.

```json
Get-AzVirtualNetwork -Name vnetName -ResourceGroupName rgName 
DhcpOptions            : {
                           "DnsServers": [
                             "x.x.x.x"
                           ]
                         }
```
Als dat het geval is, moet u ervoor zorgen dat de DNS-server de FQDN van het back-endadresgroep correct kan omzetten.

## <a name="problems-with-default-health-probe"></a>Problemen met de standaard status test

### <a name="cause"></a>Oorzaak

502-fouten kunnen ook regel matige indica toren zijn die de standaard status test geen back-end-Vm's kan bereiken.

Wanneer een instantie van de toepassings gateway is ingericht, configureert deze automatisch een standaard status test op elke BackendAddressPool met behulp van de eigenschappen van de BackendHttpSetting. Er is geen gebruikers invoer vereist om deze test in te stellen. In het bijzonder, wanneer een taakverdelings regel is geconfigureerd, wordt een koppeling gemaakt tussen een BackendHttpSetting en een BackendAddressPool. Een standaard test wordt voor elk van deze koppelingen geconfigureerd en de toepassings gateway start een regel voor een periodieke status controle van de verbinding met elk exemplaar in het BackendAddressPool op de poort die is opgegeven in het BackendHttpSetting-element. 

De volgende tabel geeft een lijst van de waarden die zijn gekoppeld aan de standaard status test:

| Probe-eigenschap | Waarde | Beschrijving |
| --- | --- | --- |
| Test-URL |`http://127.0.0.1/` |URL-pad |
| Interval |30 |Test interval in seconden |
| Time-out |30 |Time-out van de test in seconden |
| Drempelwaarde voor beschadigde status |3 |Aantal nieuwe pogingen testen. De back-endserver is gemarkeerd wanneer het aantal opeenvolgende test fouten de drempel waarde voor de onjuiste status bereikt. |

### <a name="solution"></a>Oplossing

* Zorg ervoor dat er een standaard site is geconfigureerd en luistert op 127.0.0.1.
* Als BackendHttpSetting een andere poort dan 80 opgeeft, moet de standaard site worden geconfigureerd om op die poort te Luis teren.
* De aanroep `http://127.0.0.1:port` van moet een HTTP-resultaat code van 200 retour neren. Dit moet binnen de time-outperiode van 30 seconden worden geretourneerd.
* Zorg ervoor dat de geconfigureerde poort is geopend en dat er geen firewall regels of Azure-netwerk beveiligings groepen zijn, die inkomend of uitgaand verkeer op de geconfigureerde poort blok keren.
* Als Azure Classic Vm's of Cloud service wordt gebruikt met een FQDN of een openbaar IP-adres, moet u ervoor zorgen dat het bijbehorende [eind punt](/previous-versions/azure/virtual-machines/windows/classic/setup-endpoints?toc=%2fazure%2fapplication-gateway%2ftoc.json) wordt geopend.
* Als de virtuele machine is geconfigureerd via Azure Resource Manager en zich buiten het VNet bevindt waarin de toepassings gateway is geïmplementeerd, moet er een [netwerk beveiligings groep](../virtual-network/network-security-groups-overview.md) worden geconfigureerd om toegang toe te staan op de gewenste poort.

## <a name="problems-with-custom-health-probe"></a>Problemen met een aangepaste status test

### <a name="cause"></a>Oorzaak

Aangepaste status tests bieden extra flexibiliteit voor het standaard gedrag van probings. Wanneer u aangepaste tests gebruikt, kunt u het test interval, de URL, het pad dat moet worden getest en het aantal mislukte antwoorden dat moet worden geaccepteerd, configureren voordat het exemplaar van de back-end-groep als beschadigd wordt gemarkeerd.

De volgende aanvullende eigenschappen worden toegevoegd:

| Probe-eigenschap | Beschrijving |
| --- | --- |
| Name |De naam van de test. Deze naam wordt gebruikt om te verwijzen naar de test in back-end-HTTP-instellingen. |
| Protocol |Het protocol dat wordt gebruikt om de test te verzenden. De test gebruikt het protocol dat is gedefinieerd in de back-end-HTTP-instellingen |
| Host |De hostnaam voor het verzenden van de test. Alleen van toepassing wanneer meerdere locaties op de toepassings gateway zijn geconfigureerd. Dit wijkt af van de naam van de VM-host. |
| Pad |Het relatieve pad van de test. Het geldige pad wordt gestart vanaf/. De test wordt verzonden naar \<protocol\> :// \<host\> :\<port\>\<path\> |
| Interval |Test interval in seconden. Dit is het tijds interval tussen twee opeenvolgende tests. |
| Time-out |Time-out van de test (in seconden). Als er binnen deze time-outperiode geen geldig antwoord wordt ontvangen, wordt de test als mislukt gemarkeerd. |
| Drempelwaarde voor beschadigde status |Aantal nieuwe pogingen testen. De back-endserver is gemarkeerd wanneer het aantal opeenvolgende test fouten de drempel waarde voor de onjuiste status bereikt. |

### <a name="solution"></a>Oplossing

Controleer of de aangepaste status test correct is geconfigureerd als de voor gaande tabel. Naast de voor gaande stappen voor probleem oplossing moet u ook rekening houden met het volgende:

* Zorg ervoor dat de test juist is opgegeven volgens de [hand leiding](application-gateway-create-probe-ps.md).
* Als de toepassings gateway voor één site is geconfigureerd, moet de hostnaam standaard worden opgegeven als `127.0.0.1` , tenzij anders geconfigureerd in een aangepaste test.
* Zorg ervoor dat een aanroep van http:// \<host\> : \<port\> \<path\> retourneert een http-resultaat code van 200.
* Zorg ervoor dat interval, time-out en UnhealtyThreshold binnen de aanvaard bare bereiken vallen.
* Als u een HTTPS-test gebruikt, moet u ervoor zorgen dat de back-endserver geen SNI vereist door een terugval certificaat te configureren op de back-endserver zelf.

## <a name="request-time-out"></a>Time-out van aanvraag

### <a name="cause"></a>Oorzaak

Wanneer een gebruikers aanvraag wordt ontvangen, past de toepassings gateway de geconfigureerde regels toe op de aanvraag en stuurt deze door naar een exemplaar van de back-end-pool. Er wordt gewacht op een configureerbaar tijds interval voor een reactie van het back-end-exemplaar. Dit interval is standaard **20** seconden. Als de toepassings gateway in dit interval geen reactie van een back-endtoepassing ontvangt, krijgt de gebruikers aanvraag een 502-fout.

### <a name="solution"></a>Oplossing

Met Application Gateway kunt u deze instelling configureren via de BackendHttpSetting, die vervolgens kan worden toegepast op verschillende groepen. Andere back-endservers kunnen verschillende BackendHttpSetting hebben en een andere time-out voor de aanvraag is geconfigureerd.

```azurepowershell
    New-AzApplicationGatewayBackendHttpSettings -Name 'Setting01' -Port 80 -Protocol Http -CookieBasedAffinity Enabled -RequestTimeout 60
```

## <a name="empty-backendaddresspool"></a>Lege BackendAddressPool

### <a name="cause"></a>Oorzaak

Als er in de back-end-adres groep geen Vm's of virtuele-machine schaal sets zijn geconfigureerd voor de toepassings gateway, kan er geen klant aanvraag worden doorgestuurd en wordt er een fout met een ongeldige gateway verzonden.

### <a name="solution"></a>Oplossing

Zorg ervoor dat de back-end-adres groep niet leeg is. U kunt dit doen via Power shell, CLI of portal.

```azurepowershell
Get-AzApplicationGateway -Name "SampleGateway" -ResourceGroupName "ExampleResourceGroup"
```

De uitvoer van de voor gaande cmdlet moet een niet-lege back-end-adres groep bevatten. In het volgende voor beeld ziet u twee groepen die zijn geretourneerd die zijn geconfigureerd met een FQDN-of IP-adres voor de back-end-Vm's. De inrichtings status van de BackendAddressPool moet ' geslaagd ' zijn.

BackendAddressPoolsText:

```json
[{
    "BackendAddresses": [{
        "ipAddress": "10.0.0.10",
        "ipAddress": "10.0.0.11"
    }],
    "BackendIpConfigurations": [],
    "ProvisioningState": "Succeeded",
    "Name": "Pool01",
    "Etag": "W/\"00000000-0000-0000-0000-000000000000\"",
    "Id": "/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Network/applicationGateways/<application gateway name>/backendAddressPools/pool01"
}, {
    "BackendAddresses": [{
        "Fqdn": "xyx.cloudapp.net",
        "Fqdn": "abc.cloudapp.net"
    }],
    "BackendIpConfigurations": [],
    "ProvisioningState": "Succeeded",
    "Name": "Pool02",
    "Etag": "W/\"00000000-0000-0000-0000-000000000000\"",
    "Id": "/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Network/applicationGateways/<application gateway name>/backendAddressPools/pool02"
}]
```

## <a name="unhealthy-instances-in-backendaddresspool"></a>Beschadigde instanties in BackendAddressPool

### <a name="cause"></a>Oorzaak

Als alle instanties van BackendAddressPool een slechte status hebben, heeft de toepassings gateway geen back-end om de gebruikers aanvraag naar te sturen. Dit kan ook het geval zijn wanneer back-end-exemplaren in orde zijn, maar niet de vereiste toepassing hebben geïmplementeerd.

### <a name="solution"></a>Oplossing

Zorg ervoor dat de instanties in orde zijn en dat de toepassing correct is geconfigureerd. Controleer of de back-end-exemplaren kunnen reageren op een ping van een andere virtuele machine in hetzelfde VNet. Als het is geconfigureerd met een openbaar eind punt, moet u ervoor zorgen dat een browser aanvraag voor de webtoepassing service is.

## <a name="next-steps"></a>Volgende stappen

Als de voor gaande stappen het probleem niet verhelpen, opent u een [ondersteunings ticket](https://azure.microsoft.com/support/options/).