---
title: End-to-end TLS configureren met Azure-toepassing gateway
description: In dit artikel wordt beschreven hoe u end-to-end TLS configureert met Azure-toepassing gateway met behulp van Power shell
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: how-to
ms.date: 06/09/2020
ms.author: victorh
ms.openlocfilehash: 47891dfa7fc0c9b30ccdbf2ed7710125eb36e4a3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "93397804"
---
# <a name="configure-end-to-end-tls-by-using-application-gateway-with-powershell"></a>End-to-end-TLS configureren met Application Gateway met behulp van PowerShell

## <a name="overview"></a>Overzicht

Azure-toepassing gateway ondersteunt end-to-end versleuteling van verkeer. Application Gateway beëindigt de TLS/SSL-verbinding bij de Application Gateway. De gateway past vervolgens de routerings regels op het verkeer toe, versleutelt het pakket opnieuw en stuurt het pakket door naar de juiste back-endserver op basis van de gedefinieerde routerings regels. Reacties van de webserver ondergaan hetzelfde proces terug naar de eindgebruiker.

Application Gateway biedt ondersteuning voor het definiëren van aangepaste TLS-opties. Het biedt ook ondersteuning voor het uitschakelen van de volgende protocol versies: **tlsv 1.0**, **Tlsv 1.1** en **tlsv 1.2**, evenals de te definiëren coderings suites en de volg orde van voor keur. Zie het overzicht van het [TLS-beleid](application-gateway-SSL-policy-overview.md)voor meer informatie over CONFIGUREER bare TLS-opties.

> [!NOTE]
> SSL 2,0 en SSL 3,0 zijn standaard uitgeschakeld en kunnen niet worden ingeschakeld. Ze worden beschouwd als onveilig en kunnen niet worden gebruikt met Application Gateway.

![afbeelding van het scenario][scenario]

## <a name="scenario"></a>Scenario

In dit scenario leert u hoe u een toepassings gateway maakt met behulp van end-to-end TLS met Power shell.

Dit scenario gaat als volgt:

* Maak een resource groep met de naam **appgw-RG**.
* Maak een virtueel netwerk met de naam **appgwvnet** met een adres ruimte van **10.0.0.0/16**.
* Maak twee subnetten met de naam **appgwsubnet** en **appsubnet**.
* Maak een kleine toepassings gateway die end-to-end TLS-versleuteling ondersteunt en beperkt de TLS-protocol versies en coderings suites.

## <a name="before-you-begin"></a>Voordat u begint

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Als u end-to-end TLS wilt configureren met een toepassings gateway, is een certificaat vereist voor de gateway en zijn de certificaten vereist voor de back-endservers. Het gateway certificaat wordt gebruikt om een symmetrische sleutel per TLS-protocol specificatie af te leiden. De symmetrische sleutel wordt vervolgens gebruikt voor het versleutelen en ontsleutelen van het verkeer dat naar de gateway wordt verzonden. Het gateway certificaat moet zich in de PFX-indeling (Personal Information Exchange) bevindt. Met deze bestands indeling kunt u de persoonlijke sleutel exporteren die is vereist voor de toepassings gateway om de versleuteling en ontsleuteling van verkeer uit te voeren.

Voor end-to-end TLS-versleuteling moet de back-end expliciet worden toegestaan door de toepassings gateway. Upload het open bare certificaat van de back-endservers naar de Application Gateway. Door het certificaat toe te voegen, zorgt u ervoor dat de Application Gateway alleen communiceert met bekende back-end-exemplaren. Hiermee wordt de end-to-end-communicatie verder beveiligd.

Het configuratie proces wordt beschreven in de volgende secties.

## <a name="create-the-resource-group"></a>De resourcegroep maken

In deze sectie vindt u instructies voor het maken van een resource groep die de toepassings gateway bevat.

1. Meld u aan bij uw Azure-account.

   ```powershell
   Connect-AzAccount
   ```

2. Selecteer het abonnement dat u voor dit scenario wilt gebruiken.

   ```powershell
   Select-Azsubscription -SubscriptionName "<Subscription name>"
   ```

3. Een resourcegroep maken. (Sla deze stap over als u een bestaande resourcegroep gebruikt.)

   ```powershell
   New-AzResourceGroup -Name appgw-rg -Location "West US"
   ```

## <a name="create-a-virtual-network-and-a-subnet-for-the-application-gateway"></a>Een virtueel netwerk en een subnet maken voor de toepassingsgateway

In het volgende voor beeld worden een virtueel netwerk en twee subnetten gemaakt. Er wordt één subnet gebruikt om de toepassings gateway op te slaan. Het andere subnet wordt gebruikt voor de back-ends die als host fungeren voor de webtoepassing.

1. Wijs een adres bereik toe voor het subnet dat voor de toepassings gateway moet worden gebruikt.

   ```powershell
   $gwSubnet = New-AzVirtualNetworkSubnetConfig -Name 'appgwsubnet' -AddressPrefix 10.0.0.0/24
   ```

   > [!NOTE]
   > De subnetten die zijn geconfigureerd voor een toepassings gateway, moeten correct worden aangepast. Een toepassings gateway kan Maxi maal 10 instanties worden geconfigureerd. Elk exemplaar neemt één IP-adres uit het subnet. Te klein van een subnet kan een nadelige invloed hebben op het uitschalen van een toepassings gateway.
   >

2. Wijs een adres bereik toe dat moet worden gebruikt voor de back-end-adres groep.

   ```powershell
   $nicSubnet = New-AzVirtualNetworkSubnetConfig  -Name 'appsubnet' -AddressPrefix 10.0.2.0/24
   ```

3. Maak een virtueel netwerk met de subnetten die in de voor gaande stappen zijn gedefinieerd.

   ```powershell
   $vnet = New-AzvirtualNetwork -Name 'appgwvnet' -ResourceGroupName appgw-rg -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $gwSubnet, $nicSubnet
   ```

4. Haal de resources van het virtuele netwerk en de subnetten op die moeten worden gebruikt in de volgende stappen.

   ```powershell
   $vnet = Get-AzvirtualNetwork -Name 'appgwvnet' -ResourceGroupName appgw-rg
   $gwSubnet = Get-AzVirtualNetworkSubnetConfig -Name 'appgwsubnet' -VirtualNetwork $vnet
   $nicSubnet = Get-AzVirtualNetworkSubnetConfig -Name 'appsubnet' -VirtualNetwork $vnet
   ```

## <a name="create-a-public-ip-address-for-the-front-end-configuration"></a>Een openbaar IP-adres maken voor de front-endconfiguratie

Maak een open bare IP-resource die moet worden gebruikt voor de toepassings gateway. Dit open bare IP-adres wordt gebruikt in een van de volgende stappen.

```powershell
$publicip = New-AzPublicIpAddress -ResourceGroupName appgw-rg -Name 'publicIP01' -Location "West US" -AllocationMethod Dynamic
```

> [!IMPORTANT]
> Application Gateway biedt geen ondersteuning voor het gebruik van een openbaar IP-adres dat is gemaakt met een gedefinieerd domein label. Alleen een openbaar IP-adres met een dynamisch gemaakt domein label wordt ondersteund. Als u een beschrijvende DNS-naam voor de toepassings gateway nodig hebt, raden we u aan om een CNAME-record als alias te gebruiken.

## <a name="create-an-application-gateway-configuration-object"></a>Een configuratieobject voor de toepassingsgateway maken

Alle configuratie-items worden ingesteld voordat u de toepassings gateway maakt. Volg de onderstaande stappen om de configuratie-items te maken die nodig zijn voor een toepassingsgatewayresource.

1. Een IP-configuratie voor de toepassings gateway maken. Met deze instelling configureert u welke van de subnetten die de toepassings gateway gebruikt. Wanneer Application Gateway wordt gestart, wordt een IP-adres uit het geconfigureerde subnet opgehaald en wordt het netwerk verkeer gerouteerd naar de IP-adressen in de back-end-IP-pool. Onthoud dat elk exemplaar één IP-adres gebruikt.

   ```powershell
   $gipconfig = New-AzApplicationGatewayIPConfiguration -Name 'gwconfig' -Subnet $gwSubnet
   ```

2. Een front-end-IP-configuratie maken. Met deze instelling wordt een persoonlijk of openbaar IP-adres toegewezen aan de front-end van de toepassings gateway. In de volgende stap wordt het open bare IP-adres in de voor gaande stap gekoppeld aan de front-end-IP-configuratie.

   ```powershell
   $fipconfig = New-AzApplicationGatewayFrontendIPConfig -Name 'fip01' -PublicIPAddress $publicip
   ```

3. Configureer de back-end-IP-adres groep met de IP-adressen van de webservers van de back-end. Deze IP-adressen zijn de IP-adressen die het netwerkverkeer ontvangen dat afkomstig is van het front-end-IP-eindpunt. Vervang de IP-adressen in het voor beeld door de IP-adres eindpunten van uw eigen toepassing.

   ```powershell
   $pool = New-AzApplicationGatewayBackendAddressPool -Name 'pool01' -BackendIPAddresses 1.1.1.1, 2.2.2.2, 3.3.3.3
   ```

   > [!NOTE]
   > Een Fully Qualified Domain Name (FQDN) is ook een geldige waarde om te gebruiken in plaats van een IP-adres voor de back-endservers. U kunt deze inschakelen met behulp van de **-BackendFqdns-** switch. 

4. Configureer de front-end-IP-poort voor het open bare IP-eind punt. Deze poort is de poort waarmee eind gebruikers verbinding maken.

   ```powershell
   $fp = New-AzApplicationGatewayFrontendPort -Name 'port01'  -Port 443
   ```

5. Configureer het certificaat voor de toepassings gateway. Dit certificaat wordt gebruikt om het verkeer op de toepassings gateway te ontsleutelen en opnieuw te versleutelen.

   ```powershell
   $passwd = ConvertTo-SecureString  <certificate file password> -AsPlainText -Force 
   $cert = New-AzApplicationGatewaySSLCertificate -Name cert01 -CertificateFile <full path to .pfx file> -Password $passwd 
   ```

   > [!NOTE]
   > Met dit voor beeld configureert u het certificaat dat wordt gebruikt voor de TLS-verbinding. Het certificaat moet de indeling. pfx hebben en het wacht woord moet 4 tot 12 tekens lang zijn.

6. Maak de HTTP-listener voor de toepassings gateway. Wijs de front-end-IP-configuratie, het poort en het TLS/SSL-certificaat toe dat moet worden gebruikt.

   ```powershell
   $listener = New-AzApplicationGatewayHttpListener -Name listener01 -Protocol Https -FrontendIPConfiguration $fipconfig -FrontendPort $fp -SSLCertificate $cert
   ```

7. Upload het certificaat dat moet worden gebruikt voor de bronnen van een back-end-groep met TLS-functionaliteit.

   > [!NOTE]
   > De standaard test haalt de open bare sleutel op uit de *standaard* TLS-binding op het IP-adres van de back-end en vergelijkt de waarde van de open bare sleutel die wordt ontvangen voor de open bare-sleutel waarde die u hier opgeeft. 
   > 
   > Als u hostheaders en Servernaamindicatie (SNI) gebruikt op de back-end, is de opgehaalde open bare sleutel mogelijk niet de beoogde site waarnaar verkeer loopt. Als u twijfel achtig bent, gaat u naar https://127.0.0.1/ de back-endservers om te bevestigen welk certificaat wordt gebruikt voor de *standaard* -TLS-binding. Gebruik de open bare sleutel van die aanvraag in deze sectie. Als u gebruikmaakt van host-headers en SNI voor HTTPS-bindingen en u geen antwoord en certificaat van een hand matige browser aanvraag ontvangt op https://127.0.0.1/ de back-endservers, moet u een standaard TLS-binding instellen. Als u dit niet doet, kan dit mislukken en is de back-end niet toegestaan.
   
   Zie [overzicht van TLS-beëindiging en end-to-end-TLS met Application Gateway](ssl-overview.md)voor meer informatie over SNI in Application Gateway.

   ```powershell
   $authcert = New-AzApplicationGatewayAuthenticationCertificate -Name 'allowlistcert1' -CertificateFile C:\cert.cer
   ```

   > [!NOTE]
   > Het certificaat dat u in de vorige stap hebt opgegeven, moet de open bare sleutel zijn van het pfx-certificaat dat aanwezig is op de back-end. Exporteer het certificaat (niet het basis certificaat) dat is geïnstalleerd op de back-endserver in de indeling claim, bewijs en reden (CER) en gebruik het in deze stap. Met deze stap wordt de back-end met de toepassings gateway toegestaan.

   Als u de SKU van Application Gateway v2 gebruikt, maakt u een vertrouwd basis certificaat in plaats van een verificatie certificaat. Zie [overzicht van end-to-end-TLS met Application Gateway](ssl-overview.md#end-to-end-tls-with-the-v2-sku)voor meer informatie:

   ```powershell
   $trustedRootCert01 = New-AzApplicationGatewayTrustedRootCertificate -Name "test1" -CertificateFile  <path to root cert file>
   ```

8. Configureer de HTTP-instellingen voor de Application Gateway-back-end. Wijs het certificaat dat u in de vorige stap hebt geüpload toe aan de HTTP-instellingen.

   ```powershell
   $poolSetting = New-AzApplicationGatewayBackendHttpSettings -Name 'setting01' -Port 443 -Protocol Https -CookieBasedAffinity Enabled -AuthenticationCertificates $authcert
   ```

   Voor de SKU van Application Gateway v2 gebruikt u de volgende opdracht:

   ```powershell
   $poolSetting01 = New-AzApplicationGatewayBackendHttpSettings -Name “setting01” -Port 443 -Protocol Https -CookieBasedAffinity Disabled -TrustedRootCertificate $trustedRootCert01 -HostName "test1"
   ```

9. Maak een Load Balancer-routerings regel die het load balancer gedrag configureert. In dit voor beeld wordt een basis Round Robin-regel gemaakt.

   ```powershell
   $rule = New-AzApplicationGatewayRequestRoutingRule -Name 'rule01' -RuleType basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool
   ```

10. Configureer de exemplaargrootte van de toepassingsgateway. De beschik bare grootten zijn **standaard \_ klein**, **standaard \_ medium** en **standaard \_ groot**.  Voor capaciteit zijn de beschik bare waarden **1** tot en met **10**.

    ```powershell
    $sku = New-AzApplicationGatewaySku -Name Standard_Small -Tier Standard -Capacity 2
    ```

    > [!NOTE]
    > Het aantal exemplaren van 1 kan worden gekozen voor test doeleinden. Het is belang rijk te weten dat het aantal exemplaren onder twee exemplaren niet wordt gedekt door de SLA en daarom niet wordt aanbevolen. Kleine gateways moeten worden gebruikt voor dev test en niet voor productie doeleinden.

11. Configureer het TLS-beleid dat moet worden gebruikt voor de toepassings gateway. Application Gateway ondersteunt de mogelijkheid om een minimale versie voor TLS-protocol versies in te stellen.

    De volgende waarden zijn een lijst met protocol versies die kunnen worden gedefinieerd:

    - **TLSV1_0**
    - **TLSV1_1**
    - **TLSV1_2**
    
    In het volgende voor beeld wordt de minimale Protocol versie ingesteld op **TLSv1_2** en wordt **TLS \_ ECDHE \_ ECDSA \_ met \_ AES \_ 128 \_ GCM \_ sha256**, **TLS \_ ECDHE \_ ECDSA \_ met \_ AES \_ 256 \_ GCM \_ SHA384** en **TLS \_ RSA \_ met \_ AES \_ 128 \_ GCM \_ sha256** .

    ```powershell
    $SSLPolicy = New-AzApplicationGatewaySSLPolicy -MinProtocolVersion TLSv1_2 -CipherSuite "TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256", "TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384", "TLS_RSA_WITH_AES_128_GCM_SHA256" -PolicyType Custom
    ```

## <a name="create-the-application-gateway"></a>De toepassingsgateway maken

Maak de toepassings gateway met behulp van alle voor gaande stappen. Het maken van de gateway is een proces dat veel tijd kost om uit te voeren.

Voor v1 SKU gebruikt u de onderstaande opdracht
```powershell
$appgw = New-AzApplicationGateway -Name appgateway -SSLCertificates $cert -ResourceGroupName "appgw-rg" -Location "West US" -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku -SSLPolicy $SSLPolicy -AuthenticationCertificates $authcert -Verbose
```

Voor v2 SKU gebruikt u de onderstaande opdracht
```powershell
$appgw = New-AzApplicationGateway -Name appgateway -SSLCertificates $cert -ResourceGroupName "appgw-rg" -Location "West US" -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting01 -FrontendIpConfigurations $fipconfig -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku -SSLPolicy $SSLPolicy -TrustedRootCertificate $trustedRootCert01 -Verbose
```

## <a name="apply-a-new-certificate-if-the-back-end-certificate-is-expired"></a>Een nieuw certificaat Toep assen als het back-end-certificaat is verlopen

Gebruik deze procedure om een nieuw certificaat toe te passen als het back-end-certificaat is verlopen.

1. Haal de toepassings gateway op om bij te werken.

   ```powershell
   $gw = Get-AzApplicationGateway -Name AdatumAppGateway -ResourceGroupName AdatumAppGatewayRG
   ```
   
2. Voeg de nieuwe certificaat bron toe vanuit het CER-bestand, dat de open bare sleutel van het certificaat bevat en kan ook hetzelfde certificaat zijn dat is toegevoegd aan de listener voor TLS-beëindiging op de Application Gateway.

   ```powershell
   Add-AzApplicationGatewayAuthenticationCertificate -ApplicationGateway $gw -Name 'NewCert' -CertificateFile "appgw_NewCert.cer" 
   ```
    
3. Haal het nieuwe verificatie certificaat object op in een variabele (TypeName: micro soft. Azure. commands. Network. Models. PSApplicationGatewayAuthenticationCertificate).

   ```powershell
   $AuthCert = Get-AzApplicationGatewayAuthenticationCertificate -ApplicationGateway $gw -Name NewCert
   ```
 
 4. Wijs het nieuwe certificaat toe aan de **BackendHttp** -instelling en Raadpleeg het met de variabele $AuthCert. (Geef de naam op van de HTTP-instelling die u wilt wijzigen.)
 
   ```powershell
   $out= Set-AzApplicationGatewayBackendHttpSetting -ApplicationGateway $gw -Name "HTTP1" -Port 443 -Protocol "Https" -CookieBasedAffinity Disabled -AuthenticationCertificates $Authcert
   ```
    
 5. Voer de wijziging door in de toepassings gateway en geef de nieuwe configuratie door aan de variabele $out.
 
   ```powershell
   Set-AzApplicationGateway -ApplicationGateway $gw  
   ```

## <a name="remove-an-unused-expired-certificate-from-http-settings"></a>Een ongebruikt verlopen certificaat verwijderen uit de HTTP-instellingen

Gebruik deze procedure om een ongebruikt verlopen certificaat te verwijderen uit de HTTP-instellingen.

1. Haal de toepassings gateway op om bij te werken.

   ```powershell
   $gw = Get-AzApplicationGateway -Name AdatumAppGateway -ResourceGroupName AdatumAppGatewayRG
   ```
   
2. Vermeld de naam van het verificatie certificaat dat u wilt verwijderen.

   ```powershell
   Get-AzApplicationGatewayAuthenticationCertificate -ApplicationGateway $gw | select name
   ```
    
3. Het verificatie certificaat verwijderen uit een toepassings gateway.

   ```powershell
   $gw=Remove-AzApplicationGatewayAuthenticationCertificate -ApplicationGateway $gw -Name ExpiredCert
   ```
 
 4. Voer de wijziging door.
 
   ```powershell
   Set-AzApplicationGateway -ApplicationGateway $gw
   ```

   
## <a name="limit-tls-protocol-versions-on-an-existing-application-gateway"></a>TLS-protocol versies beperken op een bestaande toepassings gateway

In de voor gaande stappen hebt u geleid tot het maken van een toepassing met end-to-end TLS en het uitschakelen van bepaalde TLS-protocol versies. In het volgende voor beeld wordt bepaalde TLS-beleids regels uitgeschakeld op een bestaande toepassings gateway.

1. Haal de toepassings gateway op om bij te werken.

   ```powershell
   $gw = Get-AzApplicationGateway -Name AdatumAppGateway -ResourceGroupName AdatumAppGatewayRG
   ```

2. Definieer een TLS-beleid. In het volgende voor beeld zijn **tlsv 1.0** en **tlsv 1.1** uitgeschakeld en de coderings suites **TLS \_ ECDHE \_ ECDSA \_ met \_ AES \_ 128 \_ GCM \_ sha256**, **TLS \_ ECDHE \_ ECDSA \_ met \_ AES \_ 256 \_ GCM \_ SHA384** en **TLS \_ RSA \_ met \_ AES \_ 128 \_ GCM \_** sha256 zijn de enige toegestaan.

   ```powershell
   Set-AzApplicationGatewaySSLPolicy -MinProtocolVersion TLSv1_2 -PolicyType Custom -CipherSuite "TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256", "TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384", "TLS_RSA_WITH_AES_128_GCM_SHA256" -ApplicationGateway $gw

   ```

3. Werk tot slot de gateway bij. Deze laatste stap is een langlopende taak. Wanneer dit is gebeurd, wordt end-to-end TLS geconfigureerd op de toepassings gateway.

   ```powershell
   $gw | Set-AzApplicationGateway
   ```

## <a name="get-an-application-gateway-dns-name"></a>Een DNS-naam voor de toepassings gateway ophalen

Nadat de gateway is gemaakt, is de volgende stap het configureren van de front-end voor communicatie. Application Gateway moet een dynamisch toegewezen DNS-naam hebben wanneer u een openbaar IP-adres gebruikt. Dit is niet vriendelijk. Om ervoor te zorgen dat eind gebruikers de toepassings gateway kunnen bereiken, kunt u een CNAME-record gebruiken om te verwijzen naar het open bare eind punt van de toepassings gateway. Zie [een aangepaste domein naam configureren voor in azure](../cloud-services/cloud-services-custom-domain-name-portal.md)voor meer informatie. 

Als u een alias wilt configureren, haalt u de details van de toepassings gateway en de bijbehorende IP/DNS-naam op met behulp van het **PublicIPAddress** -element dat is gekoppeld aan de toepassings gateway. Gebruik de DNS-naam van de toepassings gateway om een CNAME-record te maken waarmee de twee webtoepassingen naar deze DNS-naam worden verwezen. Het gebruik van A-records wordt niet aanbevolen, omdat het VIP kan worden gewijzigd bij het opnieuw opstarten van de toepassings gateway.

```powershell
Get-AzPublicIpAddress -ResourceGroupName appgw-RG -Name publicIP01
```

```
Name                     : publicIP01
ResourceGroupName        : appgw-RG
Location                 : westus
Id                       : /subscriptions/<subscription_id>/resourceGroups/appgw-RG/providers/Microsoft.Network/publicIPAddresses/publicIP01
Etag                     : W/"00000d5b-54ed-4907-bae8-99bd5766d0e5"
ResourceGuid             : 00000000-0000-0000-0000-000000000000
ProvisioningState        : Succeeded
Tags                     : 
PublicIpAllocationMethod : Dynamic
IpAddress                : xx.xx.xxx.xx
PublicIpAddressVersion   : IPv4
IdleTimeoutInMinutes     : 4
IpConfiguration          : {
                                "Id": "/subscriptions/<subscription_id>/resourceGroups/appgw-RG/providers/Microsoft.Network/applicationGateways/appgwtest/frontendIP
                            Configurations/frontend1"
                            }
DnsSettings              : {
                                "Fqdn": "00000000-0000-xxxx-xxxx-xxxxxxxxxxxx.cloudapp.net"
                            }
```

## <a name="next-steps"></a>Volgende stappen

Zie het [overzicht van Web Application firewall](../web-application-firewall/ag/ag-overview.md)voor meer informatie over het beveiligen van de beveiliging van uw webtoepassingen met Web Application firewall via Application Gateway.

[scenario]: ./media/application-gateway-end-to-end-SSL-powershell/scenario.png