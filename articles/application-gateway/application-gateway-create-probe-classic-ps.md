---
title: Een aangepaste test maken met behulp van het klassieke implementatie model-Azure-toepassing gateway
description: Meer informatie over het maken van een aangepaste test voor Application Gateway met behulp van Power shell in het klassieke implementatie model
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: how-to
ms.date: 11/13/2019
ms.author: victorh
ms.openlocfilehash: 13441899eeb5ca2b7c60977ab2858fe40a398d1a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "93397855"
---
# <a name="create-a-custom-probe-for-azure-application-gateway-classic-by-using-powershell"></a>Een aangepaste test voor Azure-toepassing gateway (klassiek) maken met behulp van Power shell

> [!div class="op_single_selector"]
> * [Azure-portal](application-gateway-create-probe-portal.md)
> * [Azure Resource Manager PowerShell](application-gateway-create-probe-ps.md)
> * [Azure Classic PowerShell](application-gateway-create-probe-classic-ps.md)

In dit artikel voegt u een aangepaste test toe aan een bestaande toepassings gateway met Power shell. Aangepaste tests zijn handig voor toepassingen met een specifieke status controle pagina of voor toepassingen die geen geslaagde reactie op de standaard webtoepassing bieden.

> [!IMPORTANT]
> Azure heeft twee verschillende implementatie modellen voor het maken van en werken met resources: [Resource Manager en klassiek](../azure-resource-manager/management/deployment-models.md). In dit artikel wordt beschreven hoe u het klassieke implementatie model gebruikt. U doet er verstandig aan voor de meeste nieuwe implementaties het Resource Manager-model te gebruiken. Lees [meer informatie over het uitvoeren van deze stappen met het Resource Manager-model](application-gateway-create-probe-ps.md).

[!INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]

## <a name="create-an-application-gateway"></a>Een toepassingsgateway maken

Ga als volgt te werk om een toepassingsgateway te maken:

1. Maak een toepassingsgatewayresource.
2. Maak een XML-configuratiebestand of een configuratieobject.
3. Voer de configuratie door voor de zojuist gemaakte toepassingsgatewayresource.

### <a name="create-an-application-gateway-resource-with-a-custom-probe"></a>Een resource voor een toepassings gateway maken met een aangepaste test

Gebruik de cmdlet `New-AzureApplicationGateway` en vervang de waarden door uw eigen waarden om een gateway te maken. Er worden op dat moment nog geen kosten in rekening gebracht voor gebruik van de gateway. De kosten zijn pas vanaf een latere stap van toepassing, wanneer de gateway wordt geopend.

In het volgende voorbeeld wordt een toepassingsgateway gemaakt met een virtueel netwerk met de naam testvnet1 en een subnet met de naam subnet-1.

```powershell
New-AzureApplicationGateway -Name AppGwTest -VnetName testvnet1 -Subnets @("Subnet-1")
```

Gebruik de cmdlet `Get-AzureApplicationGateway` om te controleren of de gateway is gemaakt.

```powershell
Get-AzureApplicationGateway AppGwTest
```

> [!NOTE]
> De standaardwaarde voor *InstanceCount* is 2 en de maximale waarde is 10. De standaardwaarde voor *GatewaySize* is Medium. U kunt kiezen tussen klein, gemiddeld en groot.
> 
> 

*VirtualIPs* en *DnsName* zijn leeg, omdat de gateway nog niet is geopend. Deze waarden worden gemaakt zodra de gateway de status actief heeft.

### <a name="configure-an-application-gateway-by-using-xml"></a>Een toepassings gateway configureren met XML

In het volgende voorbeeld gebruikt u een XML-bestand om alle instellingen voor de toepassingsgateway te configureren en deze door te voeren voor de toepassingsgatewayresource.  

Kopieer de volgende tekst naar Kladblok.

```xml
<ApplicationGatewayConfiguration xmlns:i="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/windowsazure">
<FrontendIPConfigurations>
    <FrontendIPConfiguration>
        <Name>fip1</Name>
        <Type>Private</Type>
    </FrontendIPConfiguration>
</FrontendIPConfigurations>
<FrontendPorts>
    <FrontendPort>
        <Name>port1</Name>
        <Port>80</Port>
    </FrontendPort>
</FrontendPorts>
<Probes>
    <Probe>
        <Name>Probe01</Name>
        <Protocol>Http</Protocol>
        <Host>contoso.com</Host>
        <Path>/path/custompath.htm</Path>
        <Interval>15</Interval>
        <Timeout>15</Timeout>
        <UnhealthyThreshold>5</UnhealthyThreshold>
    </Probe>
    </Probes>
    <BackendAddressPools>
    <BackendAddressPool>
        <Name>pool1</Name>
        <IPAddresses>
            <IPAddress>1.1.1.1</IPAddress>
            <IPAddress>2.2.2.2</IPAddress>
        </IPAddresses>
    </BackendAddressPool>
</BackendAddressPools>
<BackendHttpSettingsList>
    <BackendHttpSettings>
        <Name>setting1</Name>
        <Port>80</Port>
        <Protocol>Http</Protocol>
        <CookieBasedAffinity>Enabled</CookieBasedAffinity>
        <RequestTimeout>120</RequestTimeout>
        <Probe>Probe01</Probe>
    </BackendHttpSettings>
</BackendHttpSettingsList>
<HttpListeners>
    <HttpListener>
        <Name>listener1</Name>
        <FrontendIP>fip1</FrontendIP>
    <FrontendPort>port1</FrontendPort>
        <Protocol>Http</Protocol>
    </HttpListener>
</HttpListeners>
<HttpLoadBalancingRules>
    <HttpLoadBalancingRule>
        <Name>lbrule1</Name>
        <Type>basic</Type>
        <BackendHttpSettings>setting1</BackendHttpSettings>
        <Listener>listener1</Listener>
        <BackendAddressPool>pool1</BackendAddressPool>
    </HttpLoadBalancingRule>
</HttpLoadBalancingRules>
</ApplicationGatewayConfiguration>
```

Bewerk de waarden tussen de haakjes voor de configuratie-items. Sla het bestand op met de bestandsextensie .xml.

In het volgende voor beeld ziet u hoe u een configuratie bestand gebruikt om de toepassings gateway in te stellen voor het verdelen van HTTP-verkeer op open bare poort 80 en het verzenden van netwerk verkeer naar een back-end-poort 80 tussen twee IP-adressen met behulp van een aangepaste test.

> [!IMPORTANT]
> Het protocolitem Http of Https is hoofdlettergevoelig.

Er wordt een nieuw configuratie-item \<Probe\> toegevoegd om aangepaste tests te configureren.

De configuratie parameters zijn:

|Parameter|Beschrijving|
|---|---|
|**Naam** |Referentie naam voor aangepaste test. |
| **Protocol** | Gebruikt protocol (mogelijke waarden zijn HTTP of HTTPS).|
| **Host** en **pad** | Volledige URL-pad dat door de toepassings gateway wordt aangeroepen om de status van het exemplaar te bepalen. Als u bijvoorbeeld een website http: \/ /contoso.com/hebt, kan de aangepaste test worden geconfigureerd voor "http: \/ /contoso.com/Path/custompath.htm" voor test controles om een geslaagde http-reactie te krijgen.|
| **Interval** | Hiermee configureert u de controles van het test interval in seconden.|
| **Out** | Hiermee wordt de time-out van de test voor een HTTP-antwoord controle gedefinieerd.|
| **UnhealthyThreshold** | Het aantal mislukte HTTP-antwoorden dat nodig is voor het markeren van het back-end-exemplaar als *beschadigd*.|

Er wordt in de configuratie naar de test naam verwezen \<BackendHttpSettings\> om toe te wijzen welke back-end-pool aangepaste test instellingen gebruikt.

## <a name="add-a-custom-probe-to-an-existing-application-gateway"></a>Een aangepaste test toevoegen aan een bestaande toepassings gateway

Het wijzigen van de huidige configuratie van een toepassings gateway vereist drie stappen: het huidige XML-configuratie bestand ophalen, wijzigen zodat het een aangepaste test heeft en de toepassings gateway configureren met de nieuwe XML-instellingen.

1. Haal het XML-bestand op met behulp van `Get-AzureApplicationGatewayConfig` . Met deze cmdlet wordt de configuratie-XML geëxporteerd die moet worden gewijzigd om een test instelling toe te voegen.

   ```powershell
   Get-AzureApplicationGatewayConfig -Name "<application gateway name>" -Exporttofile "<path to file>"
   ```

1. Open het XML-bestand in een tekst editor. Voeg een `<probe>` sectie toe na `<frontendport>` .

   ```xml
   <Probes>
    <Probe>
        <Name>Probe01</Name>
        <Protocol>Http</Protocol>
        <Host>contoso.com</Host>
        <Path>/path/custompath.htm</Path>
        <Interval>15</Interval>
        <Timeout>15</Timeout>
        <UnhealthyThreshold>5</UnhealthyThreshold>
    </Probe>
   </Probes>
   ```

   Voeg in de sectie backendHttpSettings van de XML de test naam toe, zoals wordt weer gegeven in het volgende voor beeld:

   ```xml
    <BackendHttpSettings>
        <Name>setting1</Name>
        <Port>80</Port>
        <Protocol>Http</Protocol>
        <CookieBasedAffinity>Enabled</CookieBasedAffinity>
        <RequestTimeout>120</RequestTimeout>
        <Probe>Probe01</Probe>
    </BackendHttpSettings>
   ```

   Sla het XML-bestand op.

1. Werk de configuratie van de toepassings gateway bij met het nieuwe XML-bestand met behulp van `Set-AzureApplicationGatewayConfig` . Met deze cmdlet wordt uw toepassings gateway bijgewerkt met de nieuwe configuratie.

```powershell
Set-AzureApplicationGatewayConfig -Name "<application gateway name>" -Configfile "<path to file>"
```

## <a name="next-steps"></a>Volgende stappen

Zie [een toepassings gateway configureren voor TLS-offload](./tutorial-ssl-powershell.md)als u Transport Layer Security (TLS), voorheen bekend als Secure Sockets Layer (SSL)-offload, wilt configureren.

Als u een toepassingsgateway wilt configureren voor gebruik met een interne load balancer, raadpleegt u [Create an application gateway with an internal load balancer (ILB)](./application-gateway-ilb-arm.md) (Een toepassingsgateway met een interne load balancer (ILB) maken).