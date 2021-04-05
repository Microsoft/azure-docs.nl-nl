---
title: "Zelfstudie: Taken over VM's binnen beschikbaarheidszones verdelen - Azure Portal | Microsoft Docs"
titleSuffix: Azure Load Balancer
description: Deze zelfstudie laat zien hoe u met behulp van Azure Portal een standaard Load Balancer kunt maken met een zone-redundante frontend voor de taakverdeling van VM's over beschikbaarheidszones
services: load-balancer
documentationcenter: na
author: asudbring
manager: twooley
Customer intent: As an IT administrator, I want to create a load balancer that load balances incoming internet traffic to virtual machines across availability zones in a region, so that the customers can still access the web service if a datacenter is unavailable.
ms.service: load-balancer
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/27/2019
ms.author: allensu
ms.custom: seodec18
ms.openlocfilehash: 4e07285eca0fd10b73b386fcf139cdad5b94ddc2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "94696401"
---
# <a name="tutorial-load-balance-vms-across-availability-zones-with-a-standard-load-balancer-using-the-azure-portal"></a>Zelfstudie: Met behulp van Azure Portal taakverdeling uitvoeren van virtuele machines over beschikbaarheidszones met een standaard Load Balancer

Taakverdeling zorgt voor een hogere beschikbaarheid door binnenkomende aanvragen te spreiden over meerdere virtuele machines. In deze zelfstudie leert u stapsgewijs hoe u een openbare Standard Load Balancer maakt die de taakverdeling van VM's over beschikbaarheidszones regelt. Hiermee kunt u uw apps en gegevens beschermen tegen het onwaarschijnlijke risico van een storing of het verloren gaan van een heel datacenter. Met zone-redundantie kunnen een of meer beschikbaarheidszones mislukken en overleeft het gegevenspad zolang één zone in de regio een goede status behoudt. In deze zelfstudie leert u procedures om het volgende te doen:

> [!div class="checklist"]
> * Een Load Balancer van het type Standard maken
> * Netwerkbeveiligingsgroepen maken voor het definiëren van regels voor inkomend verkeer
> * Zone-redundante VM's over meerdere zones maken en koppelen aan een load balancer
> * Een load balancer-statustest maken
> * Load balancer-verkeersregels maken
> * Een basis-IIS-site maken
> * Een load balancer in actie zien

Zie [Standard Load Balancer en beschikbaarheidszones](load-balancer-standard-availability-zones.md) voor meer informatie over het gebruik van beschikbaarheidszones met Standard Load Balancer.

U kunt deze zelfstudie desgewenst volgen met behulp van de [Azure CLI](./quickstart-load-balancer-standard-public-cli.md).

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint. 

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u aan bij de Azure Portal op [https://portal.azure.com](https://portal.azure.com).

## <a name="create-a-standard-load-balancer"></a>Een Load Balancer van het type Standard maken

Standard Load Balancer biedt alleen ondersteuning voor een standaard, openbaar IP-adres. Wanneer u een nieuwe openbaar IP-adres maakt tijdens het maken van de load balancer, wordt het automatisch geconfigureerd als een standaard SKU-versie en is het ook automatisch zone-redundant.

1. Klik linksboven in het scherm op **Een resource maken** > **Netwerken** > **Load balancer**.
2. Voer op het tabblad **Basis** van de pagina **Load balancer maken** de volgende gegevens in of selecteer deze, accepteer de standaardwaarden voor de overige instellingen en selecteer vervolgens **Controleren + maken**:

    | Instelling                 | Waarde                                              |
    | ---                     | ---                                                |
    | Abonnement               | Selecteer uw abonnement.    |    
    | Resourcegroep         | Selecteer **Nieuwe maken** en typ *MyResourceGroupLBAZ* in het tekstvak.|
    | Naam                   | *myLoadBalancer*                                   |
    | Regio         | Selecteer **Europa - west**.                                        |
    | Type          | Selecteer **Openbaar**.                                        |
    | SKU           | selecteer **Standaard**.                          |
    | Openbaar IP-adres | Selecteer **Nieuw maken**. |
    | Naam openbaar IP-adres              | Typ *myPublicIP* in het tekstvak.   |
    |Beschikbaarheidszone| Selecteer **Zone-redundant**.    |
   

## <a name="create-backend-servers"></a>Back-endservers maken

In deze sectie maakt u een virtueel netwerk en virtuele machines in verschillende zones voor de regio en installeert u vervolgens IIS op de virtuele machines om de zone-redundante load balancer te testen. Dus als één zone niet werkt, dan mislukt de teststatus voor de virtuele machine in dezelfde zone, maar wordt het verkeer nog steeds geregeld door VM's in de andere zones.

## <a name="virtual-network-and-parameters"></a>Virtueel netwerk en parameters

In deze sectie moet u de volgende parameters in de stappen vervangen door onderstaande informatie:

| Parameter                   | Waarde                |
|-----------------------------|----------------------|
| **\<resource-group-name>**  | myResourceGroupLBAZ (selecteer een bestaande resourcegroep) |
| **\<virtual-network-name>** | myVNet          |
| **\<region-name>**          | Europa -west      |
| **\<IPv4-address-space>**   | 10.0.0.0/16          |
| **\<subnet-name>**          | myBackendSubnet        |
| **\<subnet-address-range>** | 10.0.0.0/24          |

[!INCLUDE [virtual-networks-create-new](../../includes/virtual-networks-create-new.md)]

## <a name="create-a-network-security-group"></a>Een netwerkbeveiligingsgroep maken

Maak een netwerkbeveiligingsgroep om binnenkomende verbindingen met uw virtuele netwerk te definiëren.

1. Klik linksboven in het scherm op **Een resource maken**, typ *Netwerkbeveiligingsgroep* in het zoekvak en klik op **Maken** op de pagina Netwerkbeveiligingsgroep.
2. Voer deze waarden in op de pagina Netwerkbeveiligingsgroep maken:
    - *myNetworkSecurityGroup* als naam van de netwerkbeveiligingsgroep.
    - *myResourceGroupLBAZ* als naam van de bestaande resourcegroep.
   
![Schermopname toont het deelvenster Netwerkbeveiligingsgroep maken.](./media/load-balancer-standard-public-availability-zones-portal/create-nsg.png)

### <a name="create-network-security-group-rules"></a>Regels voor netwerkbeveiligingsgroepen

In deze sectie maakt u netwerkbeveiligingsgroepsregels om inkomende verbindingen via HTTP en RDP toe te staan met de Azure Portal.

1. Klik in de Azure Portal op **Alle resources** in het linkermenu en zoek dan naar en klik op **myNetworkSecurityGroup**, die zich in de resourcegroep **myResourceGroupLBAZ** bevindt.
2. Klik onder **Instellingen** op **Inkomende beveiligingsregels** en vervolgens op **Toevoegen**.
3. Voer deze waarden in voor de inkomende beveiligingsregel met de naam *myHTTPRule* om een binnenkomende HTTP-verbinding via poort 80 toe te staan:
    - *Service Tag* bij **Bron**.
    - *Internet* bij **Bronservicetag**
    - *80* bij **Poortbereiken van doel**
    - *TCP* bij **Protocol**
    - *Allow* bij **Actie**
    - *100* bij **Prioriteit**
    - *myHTTPRule* als naam van de taakverdelingsregel.
    - *Allow HTTP* als beschrijving van de taakverdelingsregel.
4. Klik op **OK**.
 
   ![Schermopname toont het deelvenster Inkomende beveiligingsregel toevoegen.](./media/load-balancer-standard-public-availability-zones-portal/8-load-balancer-nsg-rules.png)
5. Herhaal stap 2 t/m 4 om nog een regel te maken (*myRDPRule*) om een binnenkomende RDP-verbinding toe te staan via poort 3389 met de volgende waarden:
    - *Service Tag* bij **Bron**.
    - *Internet* bij **Bronservicetag**
    - *3389* bij **Poortbereiken van doel**
    - *TCP* bij **Protocol**
    - *Allow* bij **Actie**
    - *200* bij **Prioriteit**
    - *myRDPRule* als naam
    - *Allow RDP* als beschrijving

### <a name="create-virtual-machines"></a>Virtuele machines maken

Maak virtuele machines in verschillende zones (zone 1, zone 2 en zone 3) voor de regio die als back-endservers voor de load balancer kunnen functioneren.

1. Klik linksboven in het scherm op **Een resource maken** > **Compute** > **Windows Server 2016 Datacenter** en voer deze waarden in voor de virtuele machine:
    - *myVM1* als naam van de virtuele machine.        
    - *azureuser* als gebruikersnaam van de beheerder.    
    - *myResourceGroupLBAZ*: voor **Resourcegroep** selecteert u **Bestaande gebruiken** en vervolgens *myResourceGroupLBAZ*.
2. Klik op **OK**.
3. Selecteer **DS1_V2** als grootte van de virtuele machine en klik op **Selecteren**.
4. Voer deze waarden in voor de instellingen van de VM:
    - *zone 1* voor de zone waar u de VM wilt plaatsen.
    -  *myVNet*: controleer of dit als virtueel netwerk is geselecteerd.
    - *myBackendSubnet*: controleer of dit als subnet is geselecteerd.
    - *myNetworkSecurityGroup* als naam van netwerkbeveiligingsgroep (firewall).
5. Klik op **Uitgeschakeld** om diagnostische gegevens over opstarten uit te schakelen.
6. Klik op **OK**, controleer de instellingen op de overzichtspagina en klik op **Maken**.
7. Volg stap 1 tot en met 6 om een tweede VM te maken met de naam *VM2* in Zone 2 en een derde VM in Zone 3, en met *myVnet* als het virtuele netwerk, *myBackendSubnet* als het subnet en **myNetworkSecurityGroup* als de netwerkbeveiligingsgroep.

### <a name="install-iis-on-vms"></a>IIS installeren op VM's

1. Klik in het linkermenu op **Alle resources** en klik in de lijst met resources op **myVM1**, die zich in de resourcegroep *myResourceGroupLBAZ* bevindt.
2. Klik op de pagina **Overzicht** op **Verbinding maken** om extern verbinding te maken met de VM.
3. Meld u aan bij de VM met gebruikersnaam *azureuser*.
4. Ga op de serverdesktop naar **Windows Systeembeheer**>**Windows Powershell**.
5. Voer in het venster PowerShell de volgende opdrachten uit om de IIS-server te installeren, het standaardbestand iisstart.htm te verwijderen en een nieuw bestand iisstart.htm toe te voegen dat de naam van de VM weergeeft:
   ```azurepowershell-interactive
    
    # install IIS server role
    Install-WindowsFeature -name Web-Server -IncludeManagementTools
    
    # remove default htm file
     remove-item  C:\inetpub\wwwroot\iisstart.htm
    
    # Add a new htm file that displays server name
     Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from" + $env:computername)
   ```
6. Sluit de RDP-sessie met *myVM1*.
7. Herhaal stappen 1 tot en met 6 om IIS en het bijgewerkte bestand iisstart.htm te installeren op *myVM2* en *myVM3*.

## <a name="create-load-balancer-resources"></a>Resources voor load balancer maken

In deze sectie configureert u de instellingen voor de load balancer voor een back-endadresgroep en een statustest en geeft u regels voor de load balancer en NAT-regels op.


### <a name="create-a-backend-address-pool"></a>Een back-endadresgroep maken

Om verkeer te distribueren naar de VM's bevat een back-end-adresgroep de IP-adressen van de virtuele netwerkinterfacekaarten (NIC's) die zijn verbonden met de load balancer. Maak de back-endadresgroep *myBackendPool* om *VM1* en *VM2* en *VM3* op te nemen.

1. Klik in het linkermenu op **Alle resources** en vervolgens in de lijst met resources op **myLoadBalancer**.
2. Klik onder **Instellingen** op **Back-endpools** en vervolgens op **Toevoegen**.
3. Ga als volgt te werk op de pagina **Een back-endpool toevoegen**:
    - Typ *myBackEndPool* als de naam van uw back-endpool.
    - Voor **Virtueel netwerk** klikt u in de vervolgkeuzelijst op **myVNet**
    - Voor **Virtuele machine** klikt u in de vervolgkeuzelijst op **myVM1**.
    - Voor **IP-adres** klikt op het IP-adres van myVM1 in de vervolgkeuzelijst.
4. Klik op **Nieuwe back-endresource toevoegen** om elke virtuele machine (*myVM2* en *myVM3*)  toe te voegen aan de back-endpool van de load balancer.
5. Klik op **Add**.

    ![Toevoegingen doen aan de back-endadresgroep](./media/load-balancer-standard-public-availability-zones-portal/add-backend-pool.png)

3. Controleer of de instelling voor de back-endpool van de load balancer alle drie de VM's (**myVM1**, **myVM2** en **myVM3**) weergeeft.

### <a name="create-a-health-probe"></a>Een statustest maken

U gebruikt een statustest om de load balancer de status van uw app te laten bewaken. De statustest voegt dynamisch VM's toe aan de load balancer-rotatie of verwijdert ze, op basis van hun reactie op statuscontroles. Maak een statustest (*myHealthProbe*) om de status van de VM's te bewaken.

1. Klik in het linkermenu op **Alle resources** en vervolgens in de lijst met resources op **myLoadBalancer**.
2. Klik onder **Instellingen** op **Tests** en klik op **Toevoegen**.
3. Gebruik deze waarden om de statustest te maken:
    - *myHealthProbe* als naam van de statustest.
    - **HTTP** als protocoltype.
    - *80* als poortnummer.
    - *15* als **interval** in seconden tussen tests.
    - *2* als aantal **drempelwaarden voor onjuiste status** of opeenvolgende mislukte tests dat moet optreden voordat wordt besloten dat een VM een onjuiste status heeft.
4. Klik op **OK**.

   ![Een test toevoegen](./media/load-balancer-standard-public-availability-zones-portal/4-load-balancer-probes.png)

### <a name="create-a-load-balancer-rule"></a>Een load balancer-regel maken

Een load balancer-regel wordt gebruikt om de verdeling van het verkeer over de VM's te definiëren. U definieert de front-end-IP-configuratie voor het inkomende verkeer en de back-end-IP-groep om het verkeer te ontvangen, samen met de gewenste bron- en doelpoort. Maak de regel *myLoadBalancerRuleWeb* voor de load balancer voor het luisteren naar poort 80 in de front-end *FrontendLoadBalancer* en het verzenden van netwerkverkeer met gelijke taakverdeling naar de back-endadresgroep *myBackEndPool* waarbij ook van poort 80 gebruik wordt gemaakt. 

1. Klik in het linkermenu op **Alle resources** en vervolgens in de lijst met resources op **myLoadBalancer**.
2. Klik onder **Instellingen** op **Taakverdelingsregels** en vervolgens op **Toevoegen**.
3. Gebruik deze waarden om de taakverdelingsregel te configureren:
    - *myHTTPRule* als naam van de taakverdelingsregel.
    - **TCP** als protocoltype.
    - *80* als poortnummer.
    - *80* als back-endpoort.
    - *myBackendPool* als naam van de back-endpool.
    - *myHealthProbe* als naam van de statustest.
4. Klik op **OK**.
    
    
    ![Een taakverdelingsregel toevoegen](./media/load-balancer-standard-public-availability-zones-portal/load-balancing-rule.png)

## <a name="test-the-load-balancer"></a>Load balancer testen
1. Zoek op het scherm **Overzicht** het openbare IP-adres voor de load balancer. Klik op **Alle resources** en vervolgens op **myPublicIP**.

2. Kopieer het openbare IP-adres en plak het in de adresbalk van de browser. De standaardpagina van IIS-webserver wordt weergegeven in de browser.

      ![IIS-webserver](./media/tutorial-load-balancer-standard-zonal-portal/load-balancer-test.png)

Als u wilt zien hoe de load balancer verkeer distribueert naar de VM's die zijn gedistribueerd in de zone, kunt u vernieuwing van uw webbrowser afdwingen.

## <a name="clean-up-resources"></a>Resources opschonen

Verwijder de resourcegroep, de load balancer en alle gerelateerde resources, wanneer u deze niet meer nodig hebt. Selecteer hiervoor de resourcegroep die de load balancer bevat en selecteer **Verwijderen**.

## <a name="next-steps"></a>Volgende stappen

Meer leren over taakverdeling op een VM binnen een specifieke beschikbaarheidszone.
> [!div class="nextstepaction"]
> [Taken over VM's binnen een beschikbaarheidszone verdelen](tutorial-load-balancer-standard-public-zonal-portal.md)