---
title: "Zelfstudie: Load Balancer-VM's in een zone - Azure Portal"
titleSuffix: Azure Load Balancer
description: Deze zelfstudie laat zien hoe u met behulp van Azure Portal een standaard load balancer kunt maken met een zonegebonden front-end voor de taakverdeling van VM's binnen een beschikbaarheidszone.
services: load-balancer
documentationcenter: na
author: asudbring
manager: twooley
Customer intent: As an IT administrator, I want to create a load balancer that load balances incoming internet traffic to virtual machines within a specific zone in a region.
ms.service: load-balancer
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/27/2019
ms.author: allensu
ms.custom: seodec18
ms.openlocfilehash: f91c9c0f401a455543b12af81eed48bd1a3349bd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "94696435"
---
# <a name="tutorial-load-balance-vms-within-an-availability-zone-with-standard-load-balancer-by-using-the-azure-portal"></a>Zelfstudie: Met behulp van Azure Portal taakverdeling van virtuele machines uitvoeren binnen een beschikbaarheidszone met Standard Load Balancer

Deze zelfstudie toont de stappen voor het maken van een openbare [Azure Standard Load Balancer-instantie](https://aka.ms/azureloadbalancerstandard) met een zonegebonden front-end met behulp van een openbaar IP-standaardadres met Azure Portal. In dit scenario geeft u een bepaalde zone op voor uw front-end- en back-endinstanties om uw gegevenspad en resources op één lijn te brengen met een specifieke zone. U leert hoe u de volgende functies kunt uitvoeren:

> [!div class="checklist"]
> * Een Standard Load Balancer van Azure met een zonegebonden front-end maken.
> * Netwerkbeveiligingsgroepen maken voor het definiëren van regels voor inkomend verkeer.
> * Virtuele zonegebonden virtuele machines (VM's) maken en koppelen aan een load balancer.
> * Een load balancer-statustest maken.
> * Load balancer-verkeersregels maken.
> * Een elementaire IIS-site (Internet Information Services) maken.
> * Een load balancer in actie zien.

Zie [Standard Load Balancer en beschikbaarheidszones](load-balancer-standard-availability-zones.md) voor meer informatie over het gebruik van beschikbaarheidszones met Standard Load Balancer.

Als u dat liever wilt, kunt u [Azure CLI](./quickstart-load-balancer-standard-public-cli.md) gebruiken om deze zelfstudie te voltooien.

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u aan bij de Azure Portal op [https://portal.azure.com](https://portal.azure.com).

## <a name="create-a-public-standard-load-balancer-instance"></a>Een openbare Standard Load Balancer-instantie maken

Standard Load Balancer biedt alleen ondersteuning voor een standaard, openbaar IP-adres. Wanneer u een nieuw openbaar IP-adres maakt tijdens het maken van de load balancer, wordt het automatisch geconfigureerd als een standaard SKU-versie. Het is ook automatisch zoneredundant.

1. Selecteer **Een resource maken** > **Netwerken** > **Load Balancer** linksboven in het scherm.
2. Voer op het tabblad **Basis** van de pagina **Load balancer maken** de volgende gegevens in of selecteer deze, accepteer de standaardwaarden voor de overige instellingen en selecteer vervolgens **Controleren + maken**:

    | Instelling                 | Waarde                                              |
    | ---                     | ---                                                |
    | Abonnement               | Selecteer uw abonnement.    |    
    | Resourcegroep         | Selecteer **Nieuwe maken** en typ *MyResourceGroupZLB* in het tekstvak.|
    | Naam                   | *myLoadBalancer*                                   |
    | Regio         | Selecteer **Europa - west**.                                        |
    | Type          | Selecteer **Openbaar**.                                        |
    | SKU           | selecteer **Standaard**.                          |
    | Openbaar IP-adres | Selecteer **Nieuw maken**. |
    | Naam openbaar IP-adres              | Typ *myPublicIP* in het tekstvak.   |
    |Beschikbaarheidszone| Selecteer **1**.    |
3. Klik op het tabblad **Beoordelen en maken** op **Maken**.   

## <a name="create-backend-servers"></a>Back-endservers maken

In deze sectie gaat u een virtueel netwerk maken. U gaat ook twee virtuele machines maken in dezelfde zone (namelijk in zone 1) voor de regio die aan de back-endpool van de load balancer wordt toegevoegd. Vervolgens installeert u IIS op de virtuele machines om de zone-redundante Load Balancer te testen. Als u dit op één virtuele machine mislukt, mislukt de statustest voor de virtuele machine in dezelfde regio. Verkeer blijft worden aangeleverd door andere virtuele machines binnen dezelfde regio.

## <a name="virtual-network-and-parameters"></a>Virtueel netwerk en parameters

In deze sectie moet u de volgende parameters in de stappen vervangen door onderstaande informatie:

| Parameter                   | Waarde                |
|-----------------------------|----------------------|
| **\<resource-group-name>**  | myResourceGroupZLB (selecteer bestaande resourcegroep) |
| **\<virtual-network-name>** | myVNet          |
| **\<region-name>**          | Europa -west      |
| **\<IPv4-address-space>**   | 10.0.0.0\16          |
| **\<subnet-name>**          | myBackendSubnet        |
| **\<subnet-address-range>** | 10.0.0.0\24          |

[!INCLUDE [virtual-networks-create-new](../../includes/virtual-networks-create-new.md)]

## <a name="create-a-network-security-group"></a>Een netwerkbeveiligingsgroep maken

1. Selecteer **Een resource maken** linksboven in het scherm. Voer in het zoekvak **Netwerkbeveiligingsgroep** in. Selecteer **Maken** op de pagina Netwerkbeveiligingsgroep.
2. Voer deze waarden in op de pagina **Netwerkbeveiligingsgroep** maken:
   - **myNetworkSecurityGroup** als naam van de netwerkbeveiligingsgroep.
   - **myResourceGroupLBAZ** als naam van de bestaande resourcegroep.
   
     ![Een netwerkbeveiligingsgroep maken](./media/tutorial-load-balancer-standard-zonal-portal/create-network-security-group.png)

### <a name="create-nsg-rules"></a>NSG-regels maken

In deze sectie maakt u NSG-regels om inkomende verbindingen via HTTP en Microsoft Remote Desktop Protocol (RDP) toe te staan met Azure Portal.

1. Selecteer in het meest linkse menu van Azure Portal **Alle resources**. Vervolgens zoekt en selecteert u **myNetworkSecurityGroup**. Deze bevindt zich in de resourcegroep **myResourceGroupZLB**.
2. Selecteer onder **Instellingen** **Inkomende beveiligingsregels**. Selecteer vervolgens **Toevoegen**.
3. Voer deze waarden in voor de inkomende beveiligingsregel met de naam **myHTTPRule** om binnenkomende HTTP-verbindingen via poort 80 toe te staan:
    - **Service Tag** bij **Bron**
    - **Internet** bij **Bronservicetag**
    - **80** bij **Poortbereiken van doel**
    - **vTCP** bij **Protocol**
    - **Toestaan** bij **Actie**
    - **100** bij **Prioriteit**
    - **myHTTPRule** bij **Naam**
    - **HTTP toestaan** bij **Beschrijving**.
4. Selecteer **OK**.
 
   ![NSG-regels maken](./media/load-balancer-standard-public-availability-zones-portal/8-load-balancer-nsg-rules.png)

5. Herhaal de stappen 2 tot 4 om een andere regel met de naam **myRDPRule** te maken. Deze regel staat een binnenkomend RDP-verbinding toe die gebruikmaakt van poort 3389, met de volgende waarden:
    - **Service Tag** bij **Bron**
    - **Internet** bij **Bronservicetag**
    - **3389** bij **Poortbereiken van doel**
    - **TCP** bij **Protocol**
    - **Toestaan** bij **Actie**
    - **200** bij **Prioriteit**
    - **myRDPRule** bij **Naam**
    - **RDP toestaan** bij **Beschrijving**

      ![Een RDP-regel maken](./media/tutorial-load-balancer-standard-zonal-portal/create-rdp-rule.png)

### <a name="create-virtual-machines"></a>Virtuele machines maken

1. Selecteer **Een resource maken** > **Compute** > **Windows Server 2016 Datacenter** linksboven in het scherm. Voer deze waarden in voor de virtuele machine:
    - **myVM1** als naam van de virtuele machine.        
    - **azureuser** als gebruikersnaam van de beheerder.    
    - **myResourceGroupZLB** bij **Resourcegroep**. Selecteer **Bestaande gebruiken** en vervolgens **myResourceGroupZLB**
2. Selecteer **OK**.
3. Selecteer **DS1_V2** als grootte van de virtuele machine. Kies **Selecteren**.
4. Voer deze waarden in voor de instellingen van de VM:
    - **zone 1** voor de Beschikbaarheidszone waar u de VM wilt plaatsen.
    -  **myVNet**. Controleer of dit als virtueel netwerk is geselecteerd.
    - **myVM1PIP** voor het standaard openbare IP-adres dat u maakt. Selecteer **Nieuw maken**. Selecteer vervolgens **myVM1PIP** als naamtype. Selecteer **1** voor **Zone**. Het IP-adres SKU is altijd standaard.
    - **myBackendSubnet**. Zorg ervoor dat deze optie is geselecteerd als het subnet.
    - **myNetworkSecurityGroup** als naam van de netwerkbeveiligingsgroep Firewall die al bestaat.
5. Selecteer **Uitgeschakeld** om diagnostische gegevens over opstarten uit te schakelen.
6. Selecteer **OK**. Controleer de instellingen op de overzichtspagina. Selecteer vervolgens **Maken**.
7. Herhaal stap 1 tot en met 6 om een tweede virtuele machine met de naam **myVM2** in Zone 1 te maken. Maak van **myVnet** het virtuele netwerk. Maak van **myVM2PIP** het standaard openbare IP-adres. Maak van **myBackendSubnet** het subnet. En maak van **myNetworkSecurityGroup** de netwerkbeveiligingsgroep.

    ![Virtuele machines maken](./media/tutorial-load-balancer-standard-zonal-portal/create-virtual-machine.png) 

### <a name="install-iis-on-vms"></a>IIS installeren op VM's

1. Selecteer **Alle resources** in het meest linkse menu. Selecteer vervolgens **myVM1** uit de lijst met resources. Deze bevindt zich in de resourcegroep **myResourceGroupZLB**.
2. Selecteer op de **overzichtspagina** de optie **Verbinding maken** om via RDP verbinding te maken met de virtuele machine.
3. Meld u aan bij de virtuele machine met de gebruikersnaam en het wachtwoord die u hebt opgegeven tijdens het maken van de virtuele machine. U moet mogelijk **Meer opties** selecteren om de aanmeldingsgegevens op te geven die u hebt ingevoerd tijdens het maken van de VM. Selecteer vervolgens **Een ander account gebruiken**. En selecteer vervolgens **OK**. Er wordt mogelijk een certificaatwaarschuwing weergegeven tijdens het aanmelden. Selecteer **Ja** om door te gaan met de verbinding.
4. Ga op de serverdesktop naar **Windows Systeembeheer** > **Windows Powershell**.
6. In het venster **PowerShell** voert u de volgende opdrachten uit om de IIS-server te installeren. Met deze opdrachten wordt ook het standaardbestand iisstart.htm verwijderd en een nieuw bestand iisstart.htm toegevoegd dat de naam van de VM weergeeft:

   ```azurepowershell-interactive
    # install IIS server role
    Install-WindowsFeature -name Web-Server -IncludeManagementTools
    # remove default htm file
     remove-item  C:\inetpub\wwwroot\iisstart.htm
    # Add a new htm file that displays server name
     Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from" + $env:computername)
   ```
7. Sluit de RDP-sessie met **myVM1**.
8. Herhaal stappen 1 tot en met 7 om IIS te installeren op **myVM2**.

## <a name="create-load-balancer-resources"></a>Resources voor load balancer maken

In deze sectie configureert u de instellingen voor de load balancer voor een back-endadresgroep en een statustest. U geeft ook de load balancer en vertaalregels voor netwerkadressen op.


### <a name="create-a-backend-address-pool"></a>Een back-endadresgroep maken

Om verkeer te distribueren naar de VM's bevat een back-endadresgroep de IP-adressen van de virtuele netwerkinterfacekaarten die zijn verbonden met de load balancer. Maak de back-endadresgroep **myBackendPool** om **VM1** en **VM2** op te nemen.

1. Selecteer **Alle resources** in het meest linkse menu. Selecteer vervolgens **myLoadBalancer** uit de lijst met resources.
2. Selecteer **Back-endgroepen** in **Instellingen**. Selecteer vervolgens **Toevoegen**.
3. Voer de volgende acties uit op de pagina **Een back-endpool toevoegen**:
    - Voer **myBackEndPool** in als de naam van uw back-endpool.
    - Voor **Virtueel netwerk** selecteert u in de vervolgkeuzelijst **myVNet**. 
    - Voor **Virtuele machine** en **IP-adres** voegt u **myVM1** en **myVM2** toe met de bijbehorende openbare IP-adressen.
4. Selecteer **Toevoegen**.
5. Controleer of de instelling voor de back-endpool van de load balancer beide VM's (**myVM1** en **myVM2**) weergeeft.
 
    ![Een back-endpool maken](./media/tutorial-load-balancer-standard-zonal-portal/create-backend-pool.png) 

### <a name="create-a-health-probe"></a>Een statustest maken

U gebruikt een statustest om de load balancer de status van uw app te laten bewaken. De statustest voegt dynamisch VM's toe aan de load balancer-rotatie of verwijdert ze, op basis van hun reactie op statuscontroles. Maak een statustest (**myHealthProbe**) om de status van de VM's te bewaken.

1. Selecteer **Alle resources** in het meest linkse menu. Selecteer vervolgens **myLoadBalancer** uit de lijst met resources.
2. Selecteer onder **Instellingen** de optie **Statustesten**. Selecteer vervolgens **Toevoegen**.
3. Gebruik deze waarden om de statustest te maken:
    - **myHealthProbe** als naam van de statustest.
    - **HTTP** als protocoltype.
    - **80** als poortnummer.
    - **15** als **interval** in seconden tussen tests.
    - **2** als aantal **drempelwaarden voor onjuiste status** of opeenvolgende mislukte tests dat moet optreden voordat wordt besloten dat een VM een onjuiste status heeft.
4. Selecteer **OK**.

   ![Een statuscontrole toevoegen](./media/load-balancer-standard-public-availability-zones-portal/4-load-balancer-probes.png)

### <a name="create-a-load-balancer-rule"></a>Een load balancer-regel maken

Een load balancer-regel bepaalt hoe het verkeer over de VM's wordt verdeeld. U definieert de front-end-IP-configuratie voor het inkomende verkeer en de back-end-IP-groep om het verkeer te ontvangen, samen met de gewenste bron- en doelpoort. Maak een load balancer-regel met de naam **myLoadBalancerRuleWeb** om te luisteren op poort 80 van de front-end **FrontEndLoadBalancer**. De regel is bedoeld voor het verzenden van netwerkverkeer met taakverdeling naar de back-endadresgroep **myBackEndPool**, ook via poort 80. 

1. Selecteer **Alle resources** in het meest linkse menu. Selecteer vervolgens **myLoadBalancer** uit de lijst met resources.
2. Selecteer onder **Instellingen** de optie **Taakverdelingsregels**. Selecteer vervolgens **Toevoegen**.
3. Gebruik deze waarden om de taakverdelingsregel te configureren:
    - **myHTTPRule** als naam van de load balancer-regel.
    - **TCP** als protocoltype.
    - **80** als poortnummer.
    - **80** als back-endpoort.
    - **myBackendPool** als naam van de back-endpool.
    - **myHealthProbe** als naam van de statustest.
4. Selecteer **OK**.
    
    ![Een load balancer-regel toevoegen](./media/tutorial-load-balancer-standard-zonal-portal/load-balancing-rule.png)

## <a name="test-the-load-balancer"></a>Load balancer testen
1. Zoek op het scherm **Overzicht** het openbare IP-adres voor de load balancer op. Selecteer **Alle resources**. Selecteer vervolgens **myPublicIP**. 

2. Kopieer het openbare IP-adres. Plak het vervolgens in de adresbalk van de browser. De standaardpagina met de naam van de webpagina van de server wordt weergegeven in de browser.

      ![IIS-webserver](./media/tutorial-load-balancer-standard-zonal-portal/load-balancer-test.png)
3. U kunt de load balancer in actie zien door de virtuele machine die wordt weergegeven, geforceerd te stoppen. Vernieuw de browser om de andere servernaam weer te geven die in de browser wordt weergegeven.

## <a name="clean-up-resources"></a>Resources opschonen

Verwijder de resourcegroep, de load balancer en alle gerelateerde resources, wanneer u deze niet meer nodig hebt. Selecteer de resourcegroep die de load balancer bevat. Selecteer vervolgens **Verwijderen**.

## <a name="next-steps"></a>Volgende stappen

Ga door met het volgende artikel om te leren hoe u taken kunt verdelen over VM's in meerdere beschikbaarheidszones.
> [!div class="nextstepaction"]
> [Taken over VM's in meerdere beschikbaarheidszones verdelen](tutorial-load-balancer-standard-public-zone-redundant-portal.md)