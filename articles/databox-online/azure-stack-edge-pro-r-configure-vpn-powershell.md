---
title: VPN configureren op uw Azure Stack Edge Pro R-apparaat met behulp van Azure PowerShell
description: Hierin wordt beschreven hoe u VPN op uw Azure Stack Edge Pro R-apparaat configureert met een Azure PowerShell script om Azure-resources te maken.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 10/23/2020
ms.author: alkohli
ms.openlocfilehash: 2139080367cdce9a5f018afab0970a7bd0e7504c
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/01/2020
ms.locfileid: "96466604"
---
# <a name="configure-vpn-on-your-azure-stack-edge-pro-r-device-via-azure-powershell"></a>VPN configureren op uw Azure Stack Edge Pro R-apparaat via Azure PowerShell

<!--[!INCLUDE [applies-to-r-skus](../../includes/azure-stack-edge-applies-to-r-sku.md)]-->

De VPN-optie biedt een tweede laag code ring voor de gegevens in beweging via *TLS* van uw Azure stack Edge Pro R-apparaat naar Azure. U kunt VPN via de Azure Portal of via Azure PowerShell configureren op uw Azure Stack Edge Pro R-apparaat.

In dit artikel worden de stappen beschreven die nodig zijn om VPN op uw Azure Stack Edge Pro R-apparaat te configureren met behulp van een Azure PowerShell om de configuratie in de cloud te maken.

## <a name="about-vpn-setup"></a>Over VPN-installatie

Een cross-premises VPN-verbinding bestaat uit een Azure VPN-gateway, een on-premises VPN-apparaat en een IPsec S2S VPN-tunnel die de twee verbindt. De typische werk stroom omvat de volgende stappen:

- Vereisten configureren.
- Stel de benodigde resources in op Azure.
    - Een Azure VPN-gateway (virtuele netwerk gateway) maken en configureren.
    - Een lokale Azure-netwerk gateway maken en configureren die uw on-premises netwerk en VPN-apparaat vertegenwoordigt.
    - Een Azure VPN-verbinding tussen de Azure VPN-gateway en de lokale netwerk gateway maken en configureren.
    - Azure Firewall instellen en netwerk-en app-regels toevoegen.
    - Maak een Azure-routerings tabel en voeg routes toe.
- VPN instellen in de lokale web-UI van het apparaat. U configureert het on-premises VPN-apparaat dat wordt vertegenwoordigd door de lokale netwerk gateway om de daad werkelijke S2S VPN-tunnel met de Azure VPN-gateway in te stellen.

De gedetailleerde stappen zijn opgenomen in de volgende secties.

## <a name="configure-prerequisites"></a>Vereisten configureren

1. U moet toegang hebben tot een Pro R-apparaat van Azure Stack Edge dat is geïnstalleerd volgens de instructies in [uw Azure stack Edge Pro r-apparaat installeren](azure-stack-edge-pro-r-deploy-install.md). Dit apparaat kan worden gebruikt als het on-premises VPN-apparaat om de VPN-verbinding met Azure te maken. 

2. Uw VPN-apparaat moet een statisch openbaar IP-adres (extern) hebben. Dit adres mag niet NAT zijn.

3. U moet toegang hebben tot een geldig Azure-abonnement dat is ingeschakeld voor Azure Stack Edge-service in Azure. Gebruik dit abonnement om een bijbehorende resource in azure te maken voor het beheren van uw Azure Stack Edge Pro R-apparaat.  

4. U hebt toegang tot een Windows-client die u gebruikt om toegang te krijgen tot uw Azure Stack Edge Pro R-apparaat. U gebruikt deze client om via een programma de configuratie in de cloud te maken.

    1. Als u de vereiste versie van Power shell op uw Windows-client wilt installeren, voert u de volgende opdrachten uit:

        ```azurepowershell
        Install-Module -Name Az -AllowClobber -Scope CurrentUser 
        Import-Module Az.Accounts
        ```
    2. Voer de volgende opdrachten uit om verbinding te maken met uw Azure-account en-abonnement:

        ```azurepowershell
        Connect-AzAccount 
        Set-AzContext -Subscription "<Your subscription name>"
        ```
        Geef de naam van het Azure-abonnement op dat u met uw Azure Stack Edge Pro R-apparaat gebruikt om VPN te configureren.

    3. [Down load het script](https://aka.ms/ase-vpn-deployment) dat vereist is voor het maken van de configuratie in de Cloud. Via dit script gebeurt het volgende:
        
        - Maak een virtueel Azure-netwerk en de volgende subnetten: *GatewaySubnet* en *AzureFirewallSubnet*.
        - Een Azure VPN-gateway maken en configureren.
        - Een lokale Azure-netwerk gateway maken en configureren.
        - Een Azure VPN-verbinding tussen de Azure VPN-gateway en de lokale netwerk gateway maken en configureren.
        - Maak een Azure Firewall en voeg netwerk regels toe, app-regels.
        - Een Azure-routerings tabel maken en er routes aan toevoegen.


## <a name="use-the-script"></a>Het script gebruiken

Eerst wijzigt u het `parameters.json` bestand om de para meters in te voeren. Vervolgens voert u het script uit met het gewijzigde JSON-bestand.

Elk van deze stappen wordt beschreven in de volgende secties.

### <a name="download-service-tags-file"></a>Service Tags bestand downloaden

Mogelijk hebt u al een `ServiceTags.json` bestand in de map waar u het script hebt gedownload. Als dat niet het geval is, kunt u het service Tags-bestand downloaden.

[!INCLUDE [azure-stack-edge-gateway-download-service-tags](../../includes/azure-stack-edge-gateway-download-service-tags.md)]

### <a name="modify-parameters-file"></a>Parameter bestand wijzigen

De eerste stap is het wijzigen van het `parameters.json` bestand en het opslaan van de wijzigingen. 


Voor de Azure-resources die u maakt, geeft u de volgende namen op:

|Parameternaam  |Beschrijving  |
|---------|---------|
|virtualNetworks_vnet_name    | Naam van Azure-Virtual Network        |
|azureFirewalls_firewall_name     | Azure Firewall naam        |
|routeTables_routetable_name     | Naam van de Azure-route tabel        |
|publicIPAddresses_VNGW_public_ip_name     | De naam van het open bare IP-adres voor de gateway van uw virtuele netwerk       |
|virtualNetworkGateways_VNGW_name    | Azure VPN-gateway (naam van virtuele netwerk gateway)        |
|publicIPAddresses_firewall_public_ip_name     | De naam van het open bare IP-adres voor uw Azure Firewall         |
|localNetworkGateways_LNGW_name    |Naam van het lokale Azure-netwerk gateway          |
|connections_vngw_lngw_name    | Naam van de Azure VPN-verbinding. Dit is de verbinding tussen de gateway van uw virtuele netwerk en de lokale netwerk gateway.       |
|location     |Dit is de regio waarin u het virtuele netwerk wilt maken. Selecteer de regio die aan het apparaat is gekoppeld.         |

De volgende IP-adressen en adres ruimten zijn van toepassing op de Azure-resources die zijn gemaakt met inbegrip van het virtuele netwerk en gekoppelde subnetten (standaard, firewall, GatewaySubnet).

|Parameternaam  |Beschrijving  |
|---------|---------|
|VnetIPv4AddressSpace    | Dit is de adres ruimte die is gekoppeld aan uw virtuele netwerk.       |
|DefaultSubnetIPv4AddressSpace    |Dit is de adres ruimte die is gekoppeld aan het `Default` subnet voor het virtuele netwerk.         |
|FirewallSubnetIPv4AddressSpace    |Dit is de adres ruimte die is gekoppeld aan het `Firewall` subnet voor het virtuele netwerk.          |
|GatewaySubnetIPv4AddressSpace    |Dit is de adres ruimte die is gekoppeld aan de `GatewaySubnet` voor het virtuele netwerk.          |
|GatewaySubnetIPv4bgpPeeringAddress    | Dit is het IP-adres dat is gereserveerd voor BGP-communicatie en is gebaseerd op de adres ruimte die is gekoppeld aan de `GatewaySubnet` voor uw virtuele netwerk.          |

De volgende IP-adressen en adres ruimten zijn van toepassing op het on-premises netwerk (waar uw Azure Stack Edge Pro R-apparaat wordt geïmplementeerd).

|Parameternaam  |Beschrijving  |
|---------|---------|
|CustomerNetworkAddressSpace    |  Dit is de adres ruimte voor uw privé-IP-adres.       |
|CustomerPublicNetworkAddressSpace    |  Dit is de adres ruimte voor uw open bare IP-adres.       |
|DbeIOTNetworkAddressSpace    |Dit IP-adres is gereserveerd door de IoT-service. Wijzig deze para meter niet.        |
|AzureVPNsharedKey    |Deze gedeelde sleutel wordt gebruikt tijdens het maken van de Azure VPN-verbindings bron. Deze sleutel wordt ook verschaft als het gedeelde VPN-geheim tijdens de VPN-configuratie van de lokale web-UI.         |
|DBE-gateway-IP-adres   | Openbaar IP-adres voor uw Azure Stack Edge Pro R-apparaat. Dit is mogelijk niet bekend en u kunt het script uitvoeren met een IP-adres van de tijdelijke aanduiding. Bewerk de lokale netwerk gateway later met het werkelijke IP-adres.     |

#### <a name="caveats-to-keep-in-mind"></a>Voor behoud dat u moet onthouden:

- U hebt niet het IP-adres van de Azure Stack Edge Pro R-apparaat. U kunt een IP-adres van een tijdelijke aanduiding gebruiken om uw bron te maken en later de gateway van het lokale Azure-netwerk wijzigen om het werkelijke IP-adres van het apparaat en de adres ruimte van het lokale netwerk voor het apparaat toe te wijzen.
- Op basis van de richting van de IETF op Internet Assigned Numbers Authority (IANA), gebruikt u een wille keurig subnet van 172.16. x. y naar 172.24. z.a. We behouden de 172,24 IPv4-adresbereiken voor het Azure-netwerk.

### <a name="run-the-script"></a>Het script uitvoeren

Volg deze stappen om het gewijzigde `parameters.json` en het script uit te voeren om Azure-resources te maken.

1. Voer PowerShell uit als beheerder.

2. Ga naar de map waarin het script zich bevindt.

3. Voer het script uit.

    `.\AzDeployVpn.ps1 -Location <Location> -AzureAppRuleFilePath "appRule.json" -AzureIPRangesFilePath "<Service tag json file>"  -ResourceGroupName "<Resource group name>" -AzureDeploymentName "<Deployment name>" -NetworkRuleCollectionName "<Name for collection of network rules>" -Priority 115 -AppRuleCollectionName "<Name for collection of app rules>"`

    Hieronder ziet u een voorbeeld van de uitvoer.

    `.\AzDeployVpn.ps1 -Location eastus -AzureAppRuleFilePath "appRule.json" -AzureIPRangesFilePath "ServiceTags_Public_20191216.json"  -ResourceGroupName "devtestrg4" -AzureDeploymentName "dbetestdeployment20" -NetworkRuleCollectionName "testnrc20" -Priority 115 -AppRuleCollectionName "testarc20"`

    Het uitvoeren van het script duurt ongeveer 90 minuten. Nadat het script is voltooid, wordt er een implementatie logboek gegenereerd in de map waarin het script zich bevindt.


## <a name="verify-the-azure-resources"></a>De Azure-resources controleren

Nadat u het script hebt uitgevoerd, controleert u of alle resources zijn gemaakt in Azure.

Vervolgens configureert u de VPN-verbinding op de lokale webgebruikersinterface van uw apparaat.


## <a name="vpn-configuration-on-the-device"></a>VPN-configuratie op het apparaat

[!INCLUDE [azure-stack-edge-gateway-configure-vpn-local-ui](../../includes/azure-stack-edge-gateway-configure-vpn-local-ui.md)]


## <a name="verify-vpn"></a>VPN controleren

[!INCLUDE [azure-stack-edge-gateway-verify-vpn](../../includes/azure-stack-edge-gateway-verify-vpn.md)]

## <a name="validate-data-transfer-through-vpn"></a>Gegevens overdracht via VPN valideren

Kopieer gegevens naar een SMB-share om te controleren of de VPN-verbinding werkt. Volg de stappen in [een share toevoegen](azure-stack-edge-j-series-manage-shares.md#add-a-share) op uw Azure stack Edge Pro R-apparaat. 

1. Kopieer een bestand, bijvoorbeeld \data\pictures\waterfall.jpg naar de SMB-share die u op het client systeem hebt gekoppeld. 
2. Controleer of dit bestand wordt weer gegeven in uw opslag account in de Cloud.

Controleren of de gegevens door VPN worden verzonden:

1. Open de verbindings resource die in de resource groep aanwezig is. 

2. Controleer de gegevens in en de waarde voor de gegevens.
 
3. Open de Virtual Network-gateway in de resource groep. Bekijk de grafieken voor het **totale aantal** **binnenkomende en uitgaande tunnels**.
 
## <a name="debug-issues"></a>Problemen met fout opsporing

Gebruik de volgende opdrachten om problemen op te lossen:

```
Get-AzResourceGroupDeployment -DeploymentName $deploymentName -ResourceGroupName $ResourceGroupName
```

De voorbeeld uitvoer wordt hieronder weer gegeven:


```azurepowershell
PS C:\Projects\VPN\Azure-VpnDeployment> Get-AzResourceGroupDeployment -DeploymentName "aseprorvpnrg14_deployment" -ResourceGroupName "aseprorvpnrg14"


DeploymentName          : aseprorvpnrg14_deployment
ResourceGroupName       : aseprorvpnrg14
ProvisioningState       : Succeeded
Timestamp               : 10/21/2020 6:23:13 PM
Mode                    : Incremental
TemplateLink            :
Parameters              :
                          Name                                         Type                       Value
                          ===========================================  =========================  ==========
                          virtualNetworks_vnet_name                    String                     aseprorvpnrg14_vnet
                          azureFirewalls_firewall_name                 String                     aseprorvpnrg14_firewall
                          routeTables_routetable_name                  String                     aseprorvpnrg14_routetable
                          publicIPAddresses_VNGW_public_ip_name        String                     aseprorvpnrg14_vngwpublicip
                          virtualNetworkGateways_VNGW_name             String                     aseprorvpnrg14_vngw
                          publicIPAddresses_firewall_public_ip_name    String                     aseprorvpnrg14_fwpip
                          localNetworkGateways_LNGW_name               String                     aseprorvpnrg14_lngw
                          connections_vngw_lngw_name                   String                     aseprorvpnrg14_connection
                          location                                     String                     East US
                          vnetIPv4AddressSpace                         String                     172.24.0.0/16
                          defaultSubnetIPv4AddressSpace                String                     172.24.0.0/24
                          firewallSubnetIPv4AddressSpace               String                     172.24.1.0/24
                          gatewaySubnetIPv4AddressSpace                String                     172.24.2.0/24
                          gatewaySubnetIPv4bgpPeeringAddress           String                     172.24.2.254
                          customerNetworkAddressSpace                  String                     10.0.0.0/18
                          customerPublicNetworkAddressSpace            String                     207.68.128.0/24
                          dbeIOTNetworkAddressSpace                    String                     10.139.218.0/24
                          azureVPNsharedKey                            String                     1234567890
                          dbE-Gateway-ipaddress                        String                     207.68.128.113

Outputs                 :
                          Name                     Type                       Value
                          =======================  =========================  ==========
                          virtualNetwork           Object                     {
                            "provisioningState": "Succeeded",
                            "resourceGuid": "dcf673d3-5c73-4764-b077-77125eda1303",
                            "addressSpace": {
                              "addressPrefixes": [
                                "172.24.0.0/16"
                              ]
================= CUT ============================= CUT ===========================
```


```
Get-AzResourceGroupDeploymentOperation -ResourceGroupName $ResourceGroupName -DeploymentName $AzureDeploymentName
```

## <a name="next-steps"></a>Volgende stappen

[VPN configureren via de lokale gebruikers interface op uw Azure Stack Edge Pro R-apparaat](azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption.md#configure-vpn)
