---
title: VPN configureren op uw Azure Stack Edge mini-R-apparaat via Azure PowerShell
description: Hierin wordt beschreven hoe u VPN configureert op uw Azure Stack Edge mini-R-apparaat met een Azure PowerShell script om Azure-resources te maken.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 11/17/2020
ms.author: alkohli
ms.openlocfilehash: 763ccd397d8cd704ca161032e65f17979bccb53b
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/01/2020
ms.locfileid: "96466902"
---
# <a name="configure-vpn-on-your-azure-stack-edge-mini-r-device-via-azure-powershell"></a>VPN configureren op uw Azure Stack Edge mini-R-apparaat via Azure PowerShell

<!--[!INCLUDE [applies-to-r-skus](../../includes/azure-stack-edge-applies-to-r-sku.md)]-->

De VPN-optie biedt een tweede laag versleuteling voor de gegevens in beweging via *TLS* van uw Azure stack Edge mini maal r of Azure stack Edge Pro r-apparaat naar Azure. U kunt VPN configureren op uw Azure Stack Edge mini-R-apparaat via de Azure Portal of via de Azure PowerShell. 

In dit artikel worden de stappen beschreven die nodig zijn voor het configureren van een punt-naar-site-VPN (P2S) op uw Azure Stack Edge mini-R-apparaat met een Azure PowerShell script om de configuratie in de cloud te maken. De configuratie op het Azure Stack edge-apparaat wordt uitgevoerd via de lokale gebruikers interface.

## <a name="about-vpn-setup"></a>Over VPN-installatie

Met een P2S VPN-gateway verbinding kunt u een beveiligde verbinding met uw virtuele netwerk maken vanaf een afzonderlijke client computer of uw Azure Stack Edge mini-R-apparaat. U start de P2S-verbinding vanaf de client computer of het apparaat. De P2S-verbinding in dit geval maakt gebruik van IKEv2 VPN, een op standaarden gebaseerde IPsec-VPN-oplossing.

De typische werk stroom omvat de volgende stappen:

1. Vereisten configureren.
2. Stel de benodigde resources in op Azure.
    1. Een virtueel netwerk en vereiste subnetten maken en configureren. 
    2. Een Azure VPN-gateway (virtuele netwerk gateway) maken en configureren.
    3. Azure Firewall instellen en netwerk-en app-regels toevoegen.
    4. Maak Azure-routerings tabellen en voeg routes toe.
    5. Schakel punt-naar-site in VPN-gateway in.
        1. Voeg de client-adres groep toe.
        2. Tunnel Type configureren.
        3. Verificatie type configureren.
        4. Certificaat maken.
        5. Certificaat uploaden.
    6. Telefoon boek downloaden.
3. VPN instellen in de lokale web-UI van het apparaat. 
    1. Telefoon lijst opgeven.
    2. Een JSON-bestand (Service Tags) opgeven.


De gedetailleerde stappen zijn opgenomen in de volgende secties.

## <a name="configure-prerequisites"></a>Vereisten configureren

- U moet toegang hebben tot een mini maal Azure Stack edge-apparaat dat is geïnstalleerd volgens de instructies in de [installatie van uw Azure stack Edge mini-r-apparaat](azure-stack-edge-mini-r-deploy-install.md). Op dit apparaat wordt een P2S-verbinding met Azure tot stand gebracht. 

- U moet toegang hebben tot een geldig Azure-abonnement dat is ingeschakeld voor Azure Stack Edge-service in Azure. Gebruik dit abonnement om een bijbehorende resource in azure te maken voor het beheren van uw Azure Stack Edge mini-R-apparaat.  

- U hebt toegang tot een Windows-client die u gebruikt om toegang te krijgen tot uw Azure Stack Edge mini-R-apparaat. U gebruikt deze client om via een programma de configuratie in de cloud te maken.

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
        Geef de naam van het Azure-abonnement op dat u gebruikt met uw Azure Stack Edge mini-R-apparaat om VPN te configureren.

    3. [Down load het script](https://aka.ms/ase-vpn-deployment) dat vereist is voor het maken van de configuratie in de Cloud. Via dit script gebeurt het volgende:
        
        - Maak een virtueel Azure-netwerk en de volgende subnetten: *GatewaySubnet* en *AzureFirewallSubnet*.
        - Een Azure VPN-gateway maken en configureren.
        - Een lokale Azure-netwerk gateway maken en configureren.
        - Een Azure VPN-verbinding tussen de Azure VPN-gateway en de lokale netwerk gateway maken en configureren.
        - Maak een Azure Firewall en voeg netwerk regels toe, app-regels.
        - Een Azure-routerings tabel maken en er routes aan toevoegen.

    4. Maak de resource groep in het Azure Portal waaronder u de Azure-resources wilt maken. Ga naar de lijst met Services in Azure Portal, selecteer **resource groep** en selecteer **+ toevoegen**. Geef de abonnements gegevens en de naam van de resource groep op en selecteer vervolgens **maken**. Als u naar deze resource groep gaat, moeten er op dit moment geen resources onder zijn.

        ![Azure-resourcegroep](media/azure-stack-edge-mini-r-configure-vpn-powershell/azure-resource-group-1.png)
    
    5. U moet een base 64-gecodeerd certificaat hebben in `.cer` de indeling voor uw Azure stack Edge mini-R-apparaat. Dit certificaat moet worden geüpload naar uw Azure Stack edge-apparaat, net als `pfx` bij een persoonlijke sleutel. Dit certificaat moet ook worden geïnstalleerd in de vertrouwde basis van de opslag op de client die de P2S-verbinding probeert te maken.

## <a name="use-the-script"></a>Het script gebruiken

Eerst wijzigt u het `parameters-p2s.json` bestand om de para meters in te voeren. Vervolgens voert u het script uit met het gewijzigde JSON-bestand.

Elk van deze stappen wordt beschreven in de volgende secties.

### <a name="download-service-tags-file"></a>Service Tags bestand downloaden

Mogelijk hebt u al een `ServiceTags.json` bestand in de map waar u het script hebt gedownload. Als dat niet het geval is, kunt u het service Tags-bestand downloaden.

[!INCLUDE [azure-stack-edge-gateway-download-service-tags](../../includes/azure-stack-edge-gateway-download-service-tags.md)]

### <a name="modify-parameters-file"></a>Parameter bestand wijzigen

De eerste stap is het wijzigen van het `parameters-p2s.json` bestand en het opslaan van de wijzigingen. 

Voor de Azure-resources die u maakt, geeft u de volgende namen op:

|Parameternaam  |Beschrijving  |
|---------|---------|
|virtualNetworks_vnet_name    | Naam van Azure-Virtual Network        |
|azureFirewalls_firewall_name     | Azure Firewall naam        |
|routeTables_routetable_name     | Naam van de Azure-route tabel        |
|publicIPAddresses_VNGW_public_ip_name     | De naam van het open bare IP-adres voor de gateway van uw virtuele netwerk       |
|virtualNetworkGateways_VNGW_name    | Azure VPN-gateway (naam van virtuele netwerk gateway)        |
|publicIPAddresses_firewall_public_ip_name     | De naam van het open bare IP-adres voor uw Azure Firewall         |
|location     |Dit is de regio waarin u het virtuele netwerk wilt maken. Selecteer de regio die aan het apparaat is gekoppeld.         |
|RouteTables_routetable_onprem_name| Dit is de naam van de aanvullende route tabel waarmee de firewall pakketten terugstuurt naar Azure Stack edge-apparaat. Het script maakt twee extra routes en koppelt *standaard* en *FirewallSubnet* aan deze route tabel.|

Geef de volgende IP-adressen en adres ruimten op voor de Azure-resources die worden gemaakt, met inbegrip van het virtuele netwerk en de gekoppelde subnetten (*standaard*, *firewall*, *GatewaySubnet*).

|Parameternaam  |Beschrijving  |
|---------|---------|
|VnetIPv4AddressSpace    | Dit is de adres ruimte die is gekoppeld aan uw virtuele netwerk. Geef het IP-adres bereik voor Vnet op als privé IP-bereik ( https://en.wikipedia.org/wiki/Private_network#Private_IPv4_addresses) .     |
|DefaultSubnetIPv4AddressSpace    |Dit is de adres ruimte die is gekoppeld aan het `Default` subnet voor het virtuele netwerk.         |
|FirewallSubnetIPv4AddressSpace    |Dit is de adres ruimte die is gekoppeld aan het `Firewall` subnet voor het virtuele netwerk.          |
|GatewaySubnetIPv4AddressSpace    |Dit is de adres ruimte die is gekoppeld aan de `GatewaySubnet` voor het virtuele netwerk.          |
|GatewaySubnetIPv4bgpPeeringAddress    | Dit is het IP-adres dat is gereserveerd voor BGP-communicatie en is gebaseerd op de adres ruimte die is gekoppeld aan de `GatewaySubnet` voor uw virtuele netwerk.          |
|ClientAddressPool    | Dit IP-adres wordt gebruikt voor de adres groep in de P2S-configuratie in Azure Portal.         |
|PublicCertData     | Open bare certificaat gegevens worden door de VPN Gateway gebruikt voor het verifiëren van P2S-clients waarmee verbinding wordt gemaakt. Installeer het basis certificaat om de certificaat gegevens op te halen. Zorg ervoor dat het certificaat base-64 is gecodeerd met de extensie. cer. Open dit certificaat en kopieer de tekst in het certificaat tussen = = BEGIN certificaat = = and = = eind certificaat = = in één doorlopende regel.     |



### <a name="run-the-script"></a>Het script uitvoeren

Volg deze stappen om het gewijzigde `parameters-p2s.json` en het script uit te voeren om Azure-resources te maken.

1. Voer Power shell uit. Ga naar de map waarin het script zich bevindt.

3. Voer het script uit.

    `.\AzDeployVpn.ps1 -Location <Location> -AzureAppRuleFilePath "appRule.json" -AzureIPRangesFilePath "<Service tag json file>"  -ResourceGroupName "<Resource group name>" -AzureDeploymentName "<Deployment name>" -NetworkRuleCollectionName "<Name for collection of network rules>" -Priority 115 -AppRuleCollectionName "<Name for collection of app rules>"`

    > [!NOTE]
    > In deze release werkt het script alleen in de locatie VS-Oost.

    Wanneer u het script uitvoert, moet u de volgende informatie invoeren:

    
    |Parameter  |Beschrijving  |
    |---------|---------|
    |Locatie     |Dit is de regio waarin de Azure-resources moeten worden gemaakt.         |
    |AzureAppRuleFilePath     | Dit is het bestandspad voor `appRule.json` .       |
    |AzureIPRangesFilePath     |Dit is het JSON-bestand van de service label dat u in de vorige stap hebt gedownload.         |
    |ResourceGroupName     | Dit is de naam van de resource groep waarin alle Azure-resources worden gemaakt.        |
    |AzureDeploymentName    |Dit is de naam voor uw Azure-implementatie.         |
    |NetworkRuleCollectionName            | Dit is de naam voor de verzameling van alle netwerk regels die worden gemaakt en toegevoegd aan uw Azure Firewall.             |
    |Prioriteit            | Dit is de prioriteit die wordt toegewezen aan alle netwerk-en toepassings regels die worden gemaakt.              |
    |AppRuleCollectionName            |Dit is de naam voor de verzameling van alle toepassings regels die worden gemaakt en toegevoegd aan uw Azure Firewall.                |


    Hieronder ziet u een voorbeeld van de uitvoer.
    
    ```powershell
    PS C:\Offline docs\AzureVpnConfigurationScripts> .\AzDeployVpn.ps1 -Location eastus -AzureAppRuleFilePath "appRule.json" -AzureIPRangesFilePath ".\ServiceTags_Public_20200203.json" -ResourceGroupName "mytmarg3" -AzureDeploymentName "tmap2stestdeploy1" -NetworkRuleCollectionName "testnrc1" -Priority 115 -AppRuleCollectionName "testarc2"
        validating vpn deployment parameters
        Starting vpn deployment
        C:\Offline docs\AzureVpnConfigurationScripts\parameters-p2s.json
        C:\Offline docs\AzureVpnConfigurationScripts\template-p2s.json
        vpn deployment: tmap2stestdeploy1 started and status: Running
        Waiting for vpn deployment completion....
        ==== CUT ==================== CUT ==============
        Adding route 191.236.0.0/18 for AzureCloud.eastus
        Adding route 191.237.0.0/17 for AzureCloud.eastus
        Adding route 191.238.0.0/18 for AzureCloud.eastus
        Total Routes:294, Existing Routes: 74, New Routes Added: 220
        Additional routes getting added
    ```    

    > [!IMPORTANT]
    > - Het uitvoeren van het script duurt ongeveer 90 minuten. Zorg ervoor dat u zich aanmeldt bij uw netwerk voordat het script wordt gestart.
    > - Als er om een of andere reden een mislukte sessie met het script is, moet u ervoor zorgen dat u de resource groep verwijdert om alle resources te verwijderen die zijn gemaakt.
  
    
    Nadat het script is voltooid, wordt er een implementatie logboek gegenereerd in de map waarin het script zich bevindt.


## <a name="verify-the-azure-resources"></a>De Azure-resources controleren

Nadat u het script hebt uitgevoerd, controleert u of alle resources zijn gemaakt in Azure. Ga naar de resource groep die u hebt gemaakt. De volgende resources moeten worden weer geven:

![Azure-resources](media/azure-stack-edge-mini-r-configure-vpn-powershell/script-resources.png)


<!--## Enable point-to-site in VPN gateway

1. In the Azure portal, go to the resource group and then select the virtual network gateway that you created in the earlier step.

    ![Azure virtual network gateway](media/azure-stack-edge-mini-r-configure-vpn-powershell/azure-virtual-network-gateway.png)

2. Go to **Settings > Point-to-site configuration**. Select **Configure now**.

    ![Enable P2S configuration](media/azure-stack-edge-mini-r-configure-vpn-powershell/enable-p2s.png)


3.  On the **Point-to-site configuration** blade:

    1. You'll add the client address pool. This pool is a range of private IP addresses that you specify. The clients that connect over a P2S VPN dynamically receive an IP address from this range. Use a private IP address range that does not overlap with the on-premises location that you connect from, or the VNet that you want to connect to. 
    2. You can select the tunnel type. For the VPN tunnel, you will use the IKEv2 protocol. 
    3. You will now define the type of authentication. Before Azure accepts a P2S VPN connection, the user has to be authenticated first. In this case, you authenticate using the native Azure certificate authentication. Set **Authentication type** to **Azure certificate**.
    4. You will now create and upload the certificates to Azure and your client. You will need to install a root certificate on your VPN gateway. The VPN gateway validates the client certificates when the P2S connection is being established by the client. The root certificate is required for the validation and must be uploaded to Azure.
    
        The clients trying to establish the P2S connection will have a client certificate that authenticates the connecting user.  To create these certificates, follow the steps in [Generate and export certificates for Point-to-Site using PowerShell](../vpn-gateway/vpn-gateway-certificates-point-to-site.md). 

        To install the root certificate, make sure the certificate is Base-64 encoded with a .cer extension. Open this certificate and copy the text in the certificate between ==BEGIN CERTIFICATE== and ==END CERTIFICATE== in one continuous line in the public certificate data under Root certificates.

        To upload the root certificates, follow the detailed steps in [Upload the root certificate public certificate data](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md#uploadfile).
    
    5. Save the configuration.

        ![Save P2S configuration](media/azure-stack-edge-mini-r-configure-vpn-powershell/enable-p2s-config.png)-->

## <a name="download-phone-book-for-vpn-profile"></a>Telefoon lijst voor VPN-profiel downloaden

In deze stap gaat u het VPN-profiel voor uw apparaat downloaden.

1. Ga in het Azure Portal naar de resource groep en selecteer vervolgens de virtuele netwerk gateway die u hebt gemaakt in de vorige stap.

    ![Virtuele Azure-netwerk gateway](media/azure-stack-edge-mini-r-configure-vpn-powershell/azure-virtual-network-gateway.png)

2. Ga naar **instellingen > punt-naar-site-configuratie**. Selecteer **VPN-client downloaden**.

    ![P2S-configuratie 1 inschakelen](media/azure-stack-edge-mini-r-configure-vpn-powershell/download-vpn-client.png)

2. Sla het gezipte profiel en uittreksel op in uw Windows-client.

    ![P2S-configuratie 2 inschakelen](media/azure-stack-edge-mini-r-configure-vpn-powershell/save-extract-profile.png)

3. Ga naar de map *WindowsAmd64* en pak het `.exe` volgende uit: *VpnClientSetupAmd64.exe*.

    ![P2S-configuratie 3 inschakelen](media/azure-stack-edge-mini-r-configure-vpn-powershell/extract-exe.png)

3. Maak een tijdelijk pad. Bijvoorbeeld:

    `C:\NewTemp\vnet\tmp`

4. Voer Power shell uit en ga naar de map waarin `.exe` zich zich bevindt. Voer het `.exe` volgende in:

    `.\VpnClientSetupAmd64.exe /Q /C /T:"C:\NewTemp\vnet\tmp"`

5. Het tijdelijke pad heeft nieuwe bestanden. Hier volgt een voorbeeld van uitvoer:

    ```powershell
    
    PS C:\windows\system32> cd "C:\Users\Ase\Downloads\vngw5\WindowsAmd64"
    PS C:\Users\Ase\Downloads\vngw5\WindowsAmd64> .\VpnClientSetupAmd64.exe /Q /C /T:"C:\NewTemp\vnet\tmp"
    PS C:\Users\Ase\Downloads\vngw5\WindowsAmd64> cd "C:\NewTemp\vnet"
    PS C:\NewTemp\vnet> ls .\tmp
    
        Directory: C:\NewTemp\vnet\tmp
    
    Mode                LastWriteTime         Length Name
    ----                -------------         ------ ----
    -a----         2/6/2020   6:18 PM            947 8c670077-470b-421a-8dd8-8cedb4f2f08a.cer
    -a----         2/6/2020   6:18 PM            155 8c670077-470b-421a-8dd8-8cedb4f2f08a.cmp
    -a----         2/6/2020   6:18 PM           3564 8c670077-470b-421a-8dd8-8cedb4f2f08a.cms
    -a----         2/6/2020   6:18 PM          11535 8c670077-470b-421a-8dd8-8cedb4f2f08a.inf
    -a----         2/6/2020   6:18 PM           2285 8c670077-470b-421a-8dd8-8cedb4f2f08a.pbk
    -a----         2/6/2020   6:18 PM           5430 azurebox16.ico
    -a----         2/6/2020   6:18 PM           4286 azurebox32.ico
    -a----         2/6/2020   6:18 PM         138934 azurevpnbanner.bmp
    -a----         2/6/2020   6:18 PM          46064 cmroute.dll
    -a----         2/6/2020   6:18 PM            196 routes.txt
    
    PS C:\NewTemp\vnet>
    ```

6. Het *pbk* -bestand is de telefoon lijst voor het VPN-profiel. U gebruikt deze in de lokale gebruikers interface.


## <a name="vpn-configuration-on-the-device"></a>VPN-configuratie op het apparaat

Volg deze stappen op de lokale gebruikers interface van uw Azure Stack edge-apparaat.

1. Ga in de lokale gebruikers interface naar de pagina **VPN** . Selecteer **configureren** onder VPN-status.

    ![VPN configureren 1](media/azure-stack-edge-mini-r-configure-vpn-powershell/configure-vpn-1.png)

2. Op de Blade **VPN configureren** :
    
    1. Wijs in het bestand telefoon lijst uploaden het. pbk-bestand aan dat u in de vorige stap hebt gemaakt.
    2. Geef in het configuratie bestand voor de open bare IP-lijst een Azure Data Center-JSON-bestand op als invoer. U hebt dit bestand in een eerdere stap gedownload van: [https://www.microsoft.com/download/details.aspx?id=56519](https://www.microsoft.com/download/details.aspx?id=56519) .
    3. Selecteer **Oost** -as als regio en selecteer **Toep assen**.

    ![VPN configureren 2](media/azure-stack-edge-mini-r-configure-vpn-powershell/configure-vpn-2.png)

3. Voer in de sectie IP-adresbereiken die moeten **worden geopend met behulp van alleen VPN** het Vnet IPv4-bereik in dat u voor uw Azure-Virtual Network hebt gekozen.

    ![VPN configureren 3](media/azure-stack-edge-mini-r-configure-vpn-powershell/configure-vpn-3.png)

## <a name="verify-client-connection"></a>Client verbinding controleren

1. Ga in het Azure Portal naar de VPN-gateway.
2. Ga naar **instellingen > punt-naar-site-configuratie**. Onder **toegewezen IP-adressen** moet het IP-adres van uw Azure stack edge-apparaat worden weer gegeven.

## <a name="validate-data-transfer-through-vpn"></a>Gegevens overdracht via VPN valideren

Kopieer gegevens naar een SMB-share om te controleren of de VPN-verbinding werkt. Volg de stappen in [een share toevoegen](azure-stack-edge-j-series-manage-shares.md#add-a-share) op uw Azure stack edge-apparaat. 

1. Kopieer een bestand, bijvoorbeeld \data\pictures\waterfall.jpg naar de SMB-share die u op het client systeem hebt gekoppeld. 
2. Controleren of de gegevens via VPN worden verzonden terwijl de gegevens worden gekopieerd:

    1. Ga naar de VPN-gateway in de Azure Portal. 

    2. Ga naar **bewaking > metrische gegevens**.

    3. Kies in het rechterdeel venster het **bereik** als uw VPN-gateway, **metrische gegevens** als gateway P2S band breedte en **aggregatie** als Gem.

    4. Wanneer de gegevens worden gekopieerd, ziet u een toename van het bandbreedte gebruik en wanneer het kopiëren van de gegevens is voltooid, wordt het bandbreedte gebruik verwijderd.

        ![Metrische gegevens van Azure-VPN](media/azure-stack-edge-mini-r-configure-vpn-powershell/vpn-metrics-1.png)

3. Controleer of dit bestand wordt weer gegeven in uw opslag account in de Cloud.
 
## <a name="debug-issues"></a>Problemen met fout opsporing

Gebruik de volgende opdrachten om problemen op te lossen:

```
Get-AzResourceGroupDeployment -DeploymentName $deploymentName -ResourceGroupName $ResourceGroupName
```

De voorbeeld uitvoer wordt hieronder weer gegeven:


```azurepowershell
PS C:\Projects\TZL\VPN\Azure-VpnDeployment> Get-AzResourceGroupDeployment -DeploymentName "tznvpnrg14_deployment" -ResourceGroupName "tznvpnrg14"


DeploymentName          : tznvpnrg14_deployment
ResourceGroupName       : tznvpnrg14
ProvisioningState       : Succeeded
Timestamp               : 1/21/2020 6:23:13 PM
Mode                    : Incremental
TemplateLink            :
Parameters              :
                          Name                                         Type                       Value
                          ===========================================  =========================  ==========
                          virtualNetworks_vnet_name                    String                     tznvpnrg14_vnet
                          azureFirewalls_firewall_name                 String                     tznvpnrg14_firewall
                          routeTables_routetable_name                  String                     tznvpnrg14_routetable
                          publicIPAddresses_VNGW_public_ip_name        String                     tznvpnrg14_vngwpublicip
                          virtualNetworkGateways_VNGW_name             String                     tznvpnrg14_vngw
                          publicIPAddresses_firewall_public_ip_name    String                     tznvpnrg14_fwpip
                          localNetworkGateways_LNGW_name               String                     tznvpnrg14_lngw
                          connections_vngw_lngw_name                   String                     tznvpnrg14_connection
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

[VPN configureren via de lokale gebruikers interface op uw Azure stack edge-apparaat](azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption.md#configure-vpn).