---
title: 'Zelfstudie: Port forwarding configureren - Azure Portal'
titleSuffix: Azure Load Balancer
description: In deze zelfstudie ziet u hoe u Port Forwarding kunt configureren met Azure Load Balancer om verbinding te maken met VM’s in een virtueel Azure-netwerk.
services: load-balancer
documentationcenter: na
author: asudbring
manager: twooley
ms.service: load-balancer
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/26/2019
ms.author: allensu
ms.custom: seodec18
ms.openlocfilehash: 27851a44dbe3085610fadc8b9bdaf400b4d2876c
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/31/2021
ms.locfileid: "106056059"
---
# <a name="tutorial-configure-port-forwarding-in-azure-load-balancer-using-the-portal"></a>Zelfstudie: Port Forwarding configureren in Azure Load Balancer met behulp van de portal

Met Port Forwarding kunt u verbinding maken met virtuele machines (VM's) in een virtueel Azure-netwerk door gebruik te maken van een openbaar IP-adres en poortnummer van Azure Load Balancer. 

In deze zelfstudie stelt u Port Forwarding in op een Azure Load Balancer. In deze zelfstudie leert u procedures om het volgende te doen:

> [!div class="checklist"]
> * Een standaardversie van een openbare load balancer maken om netwerkverkeer te verdelen over virtuele machines. 
> * Een virtueel netwerk en VM's met een netwerkbeveiligingsgroepregel (NSG) maken. 
> * De virtuele machines toevoegen aan de back-endadresgroep van de load balancer.
> * Een load balancer-statustest en verkeersregels maken.
> * Inkomende NAT Port Forwarding-regels voor een load balancer maken.
> * IIS installeren en configureren op de virtuele machines om taakverdeling en Port Forwarding in actie te zien.

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint. 

Voor alle stappen in deze zelfstudie moet u zich aanmelden bij Azure Portal op [https://portal.azure.com](https://portal.azure.com).

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement.

## <a name="create-a-standard-load-balancer"></a>Een standaardversie van een load balancer maken

Maak eerst een standaardversie van een openbare load balancer die verkeersbelasting over virtuele machines kan verdelen. Een standaardversie van een load balancer biedt alleen ondersteuning voor een standaard, openbaar IP-adres. Wanneer u een standaardversie van een load balancer maakt, moet u ook een nieuw, standaard, openbaar IP-adres maken dat als de front-end load balancer wordt geconfigureerd en standaard **LoadBalancerFrontend** wordt genoemd. 

1. Klik linksboven in het scherm op **Een resource maken** > **Netwerken** > **Load balancer**.
2. Voer op het tabblad **Basis** van de pagina **Load balancer maken** de volgende gegevens in of selecteer deze, accepteer de standaardwaarden voor de overige instellingen en selecteer vervolgens **Controleren + maken**:

    | Instelling                 | Waarde                                              |
    | ---                     | ---                                                |
    | Abonnement               | Selecteer uw abonnement.    |    
    | Resourcegroep         | Selecteer **Nieuwe maken** en typ *MyResourceGroupLB* in het tekstvak.|
    | Naam                   | *myLoadBalancer*                                   |
    | Regio         | Selecteer **Europa - west**.                                        |
    | Type          | Selecteer **Openbaar**.                                        |
    | SKU           | selecteer **Standaard**.                          |
    | Openbaar IP-adres | Selecteer **Nieuw maken**. |
    | Naam openbaar IP-adres              | Typ *myPublicIP* in het tekstvak.   |
    |Beschikbaarheidszone| Selecteer **Zone-redundant**.    |
     
    >[!NOTE]
     >Zorg ervoor dat u de load balancer en alle resources ervoor maakt op een locatie die ondersteuning voor beschikbaarheidszones biedt. Zie [Regio's die beschikbaarheidszones ondersteunen](../availability-zones/az-region.md) voor meer informatie. 

3. Klik op het tabblad **Beoordelen en maken** op **Maken**.  
  
## <a name="create-and-configure-back-end-servers"></a>Back-endservers maken en configureren

Maak een virtueel netwerk met twee virtuele machines en voeg de VM's toe aan de back-endadresgroep van uw load balancer. 

## <a name="virtual-network-and-parameters"></a>Virtueel netwerk en parameters

In deze sectie moet u de volgende parameters in de stappen vervangen door onderstaande informatie:

| Parameter                   | Waarde                |
|-----------------------------|----------------------|
| **\<resource-group-name>**  | myResourceGroupLB (selecteer bestaande resourcegroep) |
| **\<virtual-network-name>** | myVNet          |
| **\<region-name>**          | Europa -west      |
| **\<IPv4-address-space>**   | 10.3.0.0\16          |
| **\<subnet-name>**          | myBackendSubnet        |
| **\<subnet-address-range>** | 10.3.0.0\24          |

[!INCLUDE [virtual-networks-create-new](../../includes/virtual-networks-create-new.md)]

### <a name="create-vms-and-add-them-to-the-load-balancer-back-end-pool"></a>VM's maken en toevoegen aan de back-endadresgroep van de load balancer

1. Selecteer **Een resource maken** > **Compute** > **Windows Server 2016 Datacenter** linksboven in de portal. 
   
1. In **Een virtuele machine maken** typt of selecteert u de volgende waarden op het tabblad **Basisinformatie**:
   - **Abonnement** > **Resourcegroep**: selecteer in de vervolgkeuzelijst de optie **MyResourceGroupLB**.
   - **Naam van virtuele machine**: typ *MyVM1*.
   - **Regio**: Selecteer **Europa - west**. 
   - **Gebruikersnaam**: typ *azureuser*.
   - **Wachtwoord**: typ *Azure1234567*. 
     Typ nogmaals het wachtwoord in het veld **Wachtwoord bevestigen**.
   
1. Selecteer het tabblad **Netwerken** of selecteer **Volgende: Schijven** en vervolgens **Volgende: Netwerken**. 
   
   Zorg ervoor dat de volgende opties zijn geselecteerd:
   - **Virtueel netwerk**: **MyVNet**
   - **Subnet**: **MyBackendSubnet**
   
1. Selecteer onder **Openbare IP** de optie **Nieuwe maken**, selecteer **Standaard** op de pagina **Openbaar IP-adres maken** en selecteer **OK**. 
   
1. Selecteer onder **Netwerkbeveiligingsgroep** de optie **Geavanceerd** als u een nieuwe netwerkbeveiligingsgroep (NSG), een soort firewall, wilt maken. 
   1. Selecteer in het veld **Netwerkbeveiligingsgroep configureren** de optie **Nieuwe maken**. 
   1. Typ *MyNetworkSecurityGroup* en selecteer **OK**. 
   
   >[!NOTE]
   >U ziet dat de NSG standaard al een regel voor binnenkomend verkeer heeft voor het openen van poort 3389, de poort voor Extern bureaublad (RDP).
   
1. De virtuele machine toevoegen aan een back-endadresgroep van de load balancer die u maakt:
   
   1. Selecteer onder **Taakverdeling** > **Deze virtuele machine achter een bestaande oplossing voor taakverdeling plaatsen?** de optie **Ja**. 
   1. Kies in de vervolgkeuzelijst **Opties voor taakverdeling** de optie **Azure Load Balancer**. 
   1. Kies in de vervolgkeuzelijst **Een Load Balancer selecteren** de optie **MyLoadBalancer**. 
   1. Selecteer onder **Een back-endadresgroep selecteren** de optie **Nieuwe maken**, typ vervolgens *MyBackendPool* en selecteer **Maken**. 
   
   ![Een virtueel netwerk maken](./media/tutorial-load-balancer-port-forwarding-portal/create-vm-networking.png)
   
1. Selecteer het tabblad **Beheer** of selecteer **Volgende** > **Beheer**. Stel bij **Bewaking** **Diagnostische gegevens over opstarten** in op **Uit**.
   
1. Selecteer **Controleren + maken**.
   
1. Controleer de instellingen en selecteer **Maken** als de validatie is geslaagd. 

1. Volg de stappen voor het maken van een tweede virtuele machine met de naam *MyVM2*, en stel daarvoor alle andere instellingen in zoals bij MyVM1. 
   
   Voor **Netwerkbeveiligingsgroep** selecteert u nadat u de vervolgkeuzelijst **Geavanceerd** hebt geselecteerd de **MyNetworkSecurityGroup** die u al hebt gemaakt. 
   
   Controleer onder **Een back-endadresgroep selecteren** of **MyBackendPool** is geselecteerd. 

### <a name="create-an-nsg-rule-for-the-vms"></a>Een NSG-regel maken voor de VM's

Maak een netwerkbeveiligingsgroepregel (NSG) voor de virtuele machines om binnenkomende internetverbindingen (HTTP) toe te staan.

>[!NOTE]
>Standaard heeft de NSG al een regel waarmee poort 3389, de poort voor Extern bureaublad (RDP), wordt geopend.

1. Selecteer **Alle resources** in het menu aan de linkerkant. Selecteer in de lijst met resources **MyNetworkSecurityGroup** in de resourcegroep **MyResourceGroupLB**.
   
1. Selecteer onder **Instellingen** **Inkomende beveiligingsregels** en selecteer vervolgens **Toevoegen**.
   
1. Typ of selecteer in het dialoogvenster **Binnenkomende beveiligingsregel toevoegen** de volgende waarden:
   
   - **Bron**: selecteer **Servicetag**.  
   - **Bronservicetag**: selecteer **Internet**. 
   - **Poortbereiken van doel**: typ *80*.
   - **Protocol**: selecteer **TCP**. 
   - **Actie**: selecteer **Toestaan**.  
   - **Prioriteit**: typ *100*. 
   - **Naam**: typ *MyHTTPRule*. 
   - **Beschrijving**: typ *HTTP toestaan*. 
   
1. Selecteer **Toevoegen**. 
   
   ![Een NSG-regel maken](./media/tutorial-load-balancer-port-forwarding-portal/8-load-balancer-nsg-rules.png)
   
## <a name="create-load-balancer-resources"></a>Resources voor load balancer maken

In deze sectie controleert u de back-endadresgroep van de load balancer en configureert u een statustest en verkeersregels voor de load balancer.

### <a name="view-the-back-end-address-pool"></a>De back-endadresgroep weergeven

Om verkeer te distribueren naar de VM's gebruikt de load balancer een back-endadresgroep die de IP-adressen bevat van de virtuele netwerkinterfacekaarten (NIC's) die zijn verbonden met de load balancer. 

U hebt de back-endadresgroep van de load balancer gemaakt en er virtuele machines aan toegevoegd tijdens het maken van de virtuele machines. U kunt ook back-endadresgroepen maken en virtuele machines toevoegen aan of verwijderen van de pagina **Back-endadresgroepen** van de load balancer. 

1. Selecteer **Alle resources** in het linkermenu en selecteer vervolgens **MyLoadBalancer** in de lijst met resources.
   
1. Selecteer **Back-endgroepen** in **Instellingen**.
   
1. Vouw op de pagina **Back-endpools** **MyBackendPool** uit en controleer of zowel **VM1** als **VM2** worden vermeld.

1. Selecteer **MyBackendPool**. 
   
   Op de pagina **MyBackendPool** onder **Virtuele machine** en **IP-adres** kunt u beschikbare virtuele machines verwijderen aan of toevoegen aan de groep.

U kunt nieuwe back-end-adresgroepen maken door **Toevoegen** te selecteren op de pagina **Back-endadresgroepen**.

### <a name="create-a-health-probe"></a>Een statustest maken

U gebruikt een statustest om de load balancer toe te staan de status van uw virtuele machine te bewaken. De statustest voegt dynamisch VM's toe aan de load balancer-rotatie of verwijdert ze, op basis van hun reactie op statuscontroles. 

1. Selecteer **Alle resources** in het linkermenu en selecteer vervolgens **MyLoadBalancer** in de lijst met resources.
   
1. Selecteer onder **Instellingen** de optie **Statustests** en selecteer vervolgens **Toevoegen**.
   
1. Op de pagina **Test toevoegen** typt of selecteert u de volgende waarden:
   
   - **Naam**: typ *MyHealthProbe*.
   - **Protocol**: selecteer **HTTP** in de vervolgkeuzelijst. 
   - **Poort**: typ *80*. 
   - **Pad**: accepteer */* als de standaard-URI. U kunt deze waarde vervangen door een andere URI. 
   - **Interval**: typ *15*. Een interval is het aantal seconden tussen tests.
   - **Drempelwaarde voor onjuiste status**: typ *2*. Deze waarde duidt het aantal opeenvolgende mislukte tests aan dat moet optreden voordat wordt besloten dat een VM een onjuiste status heeft.
   
1. Selecteer **OK**.
   
   ![Test toevoegen](./media/tutorial-load-balancer-port-forwarding-portal/4-load-balancer-probes.png)

### <a name="create-a-load-balancer-rule"></a>Een load balancer-regel maken

Een load balancer-regel bepaalt hoe het verkeer over de VM's wordt verdeeld. De regel definieert de front-end-IP-configuratie voor het inkomende verkeer, de back-end-IP-groep om het verkeer te ontvangen en de vereiste bron- en doelpoorten. 

De load balancer-regel met de naam **myLoadBalancerRuleWeb** luistert op poort 80 van de front-end **LoadBalancerFrontEnd**. Met de regel wordt netwerkverkeer naar de back-endadresgroep **myBackEndPool** verzonden, ook op poort 80. 

1. Selecteer **Alle resources** in het linkermenu en selecteer vervolgens **MyLoadBalancer** in de lijst met resources.
   
1. Selecteer onder **Instellingen** **Taakverdelingsregels** en selecteer vervolgens **Toevoegen**.
   
1. Op de pagina **Load balancer-regel toevoegen** typt of selecteert u de volgende waarden:
   
   - **Naam**: typ *MyLoadBalancerRule*.
   - **Protocol**: selecteer **TCP**.
   - **Poort**: typ *80*.
   - **Back-endpoort**: typ *80*.
   - **Back-end-pool**: selecteer **MyBackEndPool**.
   - **Statustest**: selecteer **MyHealthProbe**. 
   
1. Selecteer **OK**.
   
   ![Load balancer-regel toevoegen](./media/tutorial-load-balancer-port-forwarding-portal/5-load-balancing-rules.png)

## <a name="create-an-inbound-nat-port-forwarding-rule"></a>Inkomende NAT Port Forwarding-regel maken

Maak een inkomende NAT-regel (Network Address Translation) voor de load balancer om verkeer vanuit een specifieke poort van het front-end-IP-adres door te sturen naar een specifieke poort van een back-end VM.

1. Selecteer **Alle resources** in het linkermenu en selecteer vervolgens **MyLoadBalancer** in de lijst met resources.
   
1. Selecteer onder **Instellingen****Inkomende NAT-regels** en selecteer vervolgens **Toevoegen**. 
   
1. Op de pagina **Binnenkomende NAT-regel toevoegen** typt of selecteert u de volgende waarden:
   
   - **Naam**: typ *MyNATRuleVM1*.
   - **Poort**: typ *4221*.
   - **Doel-virtuele-machine**: selecteer **MyVM1** in de vervolgkeuzelijst.
   - **IP-configuratie van het netwerk**: Selecteer **ipconfig1** in de vervolgkeuzelijst.
   - **Poorttoewijzing**: selecteer **Aangepast**.
   - **Doelpoort**: typ *3389*.
   
1. Selecteer **OK**.
   
1. Herhaal de stappen voor het toevoegen van een binnenkomende NAT-regel met de naam *MyNATRuleVM2*, die gebruikmaakt van **Poort**: *4222* en **Doel-virtuele-machine**: **MyVM2**.

## <a name="test-the-load-balancer"></a>Load balancer testen

In deze sectie installeert u IIS (Internet Information Services) op de back-endservers en past u de standaardwebpagina aan om de naam van de machine weer te geven. Vervolgens gebruikt u het openbare IP-adres van de load balancer om de load balancer te testen. 

Elke back-end-VM bedient een andere versie van de standaard IIS-webpagina, zodat u kunt zien dat de load balancer aanvragen over de twee virtuele machines verdeelt.

### <a name="connect-to-the-vms-with-rdp"></a>Verbinding maken met de virtuele machines via Extern bureaublad (RDP)

Maak verbinding met elke virtuele machine met Extern bureaublad (RDP). 

1. Selecteer in de portal **Alle resources** in het menu aan de linkerkant. Selecteer in de lijst met resources elke afzonderlijke VM in de resourcegroep **MyResourceGroupLB**.
   
1. Op de **overzichtspagina** selecteert u **Verbinding maken** en vervolgens **RDP-bestand downloaden**. 
   
1. Open het gedownloade RDP-bestand en selecteer **Verbinding maken**.
   
1. Selecteer in het scherm Windows-beveiliging **Meer opties** en vervolgens **Een ander account gebruiken**. 
   
   Voer de gebruikersnaam *azureuser* en het wachtwoord *Azure1234567* in en selecteer **OK**.
   
1. Antwoord met **Ja** als u om een certificaat wordt gevraagd. 
   
   Het bureaublad van de virtuele machine wordt in een nieuw venster geopend. 

### <a name="install-iis-and-replace-the-default-iis-web-page"></a>IIS installeren en de standaard-IIS-webpagina vervangen 

Gebruik PowerShell om IIS te installeren en de standaard IIS-webpagina te vervangen door een pagina die de naam van de VM weergeeft.

1. Start vanuit het **startmenu** op MyVM1 en MyVM2 **Windows PowerShell**. 

2. Voer de volgende opdrachten uit om IIS te installeren en de standaard webpagina van IIS te vervangen:
   
   ```powershell-interactive
    # Install IIS
      Install-WindowsFeature -name Web-Server -IncludeManagementTools
    
    # Remove default htm file
     remove-item  C:\inetpub\wwwroot\iisstart.htm
    
    #Add custom htm file that displays server name
     Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from " + $env:computername)
    
   ```
   
1. Sluit de RDP-verbindingen met MyVM1 en MyVM2 door **Verbinding verbreken** te selecteren. Sluit de virtuele machines niet af.

### <a name="test-load-balancing"></a>Taakverdeling testen

1. Kopieer in de portal, op de **overzichtspagina** voor **MyLoadBalancer**, het openbare IP-adres onder **Openbaar IP-adres**. Beweeg de muisaanwijzer over het adres en selecteer het pictogram **Kopiëren** om het te kopiëren. In dit voorbeeld is dat **40.67.218.235**. 
   
1. Plak of typ het openbare IP-adres van de load balancer (*40.67.218.235*) in de adresbalk van uw internetbrowser. 
   
   De aangepaste standaardpagina van de IIS-webserver wordt weergegeven in de browser. Het bericht bestaat uit te tekst **Hallo wereld van MyVM1** of uit **Hallo wereld van MyVM2**.
   
   ![Nieuwe IIS-standaardpagina](./media/tutorial-load-balancer-port-forwarding-portal/9-load-balancer-test.png) 
   
1. Vernieuw de browser zodat u kunt zien dat de load balancer verkeer verdeelt tussen de virtuele machines. Soms wordt de pagina **MyVM1** weergegeven en op andere momenten wordt de pagina **MyVM2** weergegeven, omdat de load balancer de aanvragen naar elke back-end-VM distribueert.
   
   >[!NOTE]
   >Mogelijk moet u uw browsercache wissen of een nieuw browservenster openen tussen pogingen.

## <a name="test-port-forwarding"></a>Port Forwarding testen

Met Port Forwarding kunt u via Extern bureaublad verbinding maken met een back-end-VM met behulp van het IP-adres van de load balancer en de waarde van de front-endpoort die is gedefinieerd in de NAT-regel. 

1. Kopieer in de portal, op de **overzichtspagina** voor **MyLoadBalancer**, het openbare IP-adres. Beweeg de muisaanwijzer over het adres en selecteer het pictogram **Kopiëren** om het te kopiëren. In dit voorbeeld is dat **40.67.218.235**. 
   
1. Open een opdrachtprompt en gebruik de volgende opdracht om een Extern-bureaubladsessie te maken met MyVM2, met behulp van het openbare IP-adres van de load balancer en de front-endpoort die u hebt gedefinieerd in de NAT-regel van de virtuele machine. 
   
   ```
   mstsc /v:40.67.218.235:4222
   ```
  
1. Open het gedownloade RDP-bestand en selecteer **Verbinding maken**.
   
1. Selecteer in het scherm Windows-beveiliging **Meer opties** en vervolgens **Een ander account gebruiken**. 
   
   Voer de gebruikersnaam *azureuser* en het wachtwoord *Azure1234567* in en selecteer **OK**.
   
1. Antwoord met **Ja** als u om een certificaat wordt gevraagd. 
   
   Het bureaublad van MyVM2 wordt in een nieuw venster geopend. 

De RDP-verbinding is geslaagd, omdat de binnenkomende NAT-regel **MyNATRuleVM2** verkeer van de front-endpoort 4222 van de load balancer doorstuurt naar poort 3389 (de RDP-poort) van MyVM2.

## <a name="clean-up-resources"></a>Resources opschonen

Als u de load balancer en alle bijbehorende resources wilt verwijderen omdat u deze niet meer nodig hebt, opent u de resourcegroep **MyResourceGroupLB** en selecteert u **Resourcegroep verwijderen**.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt een standaardversie van een openbare load balancer gemaakt. U hebt netwerresources, back-endservers, een statustest en regels voor de load balancer gemaakt en geconfigureerd. U hebt IIS geïnstalleerd op de back-end-VM's en het openbare IP-adres van de load balancer gebruikt om de load balancer te testen. U hebt Port Forwarding vanuit een opgegeven poort op de load balancer naar een poort op een back-end VM ingesteld en getest. 

Voor meer informatie over Azure Load Balancer gaat u verder met meer zelfstudies over de load balancer.

> [!div class="nextstepaction"]
> [Zelfstudies voor Azure Load Balancer](tutorial-load-balancer-standard-public-zone-redundant-portal.md)
