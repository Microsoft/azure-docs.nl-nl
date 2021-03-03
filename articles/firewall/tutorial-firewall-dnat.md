---
title: 'Zelfstudie: Binnenkomend verkeer filteren met Azure Firewall DNAT via de portal'
description: In deze zelfstudie leert u hoe u Azure Firewall DNAT kunt implementeren en configureren via de Azure-portal.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: tutorial
ms.date: 03/01/2021
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: a1d3bdae1e870b094472a63d4b808d9df95c129d
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101741902"
---
# <a name="tutorial-filter-inbound-internet-traffic-with-azure-firewall-dnat-using-the-azure-portal"></a>Zelfstudie: Binnenkomend verkeer filteren met Azure Firewall DNAT via Azure Portal

U kunt Azure Firewall Destination Network Address Translation (DNAT) configureren voor het omzetten en filteren van inkomend verkeer van internet naar uw subnetten. Wanneer u DNAT configureert, wordt de actie Verzameling van NAT-regels ingesteld op **Dnat**. Elke regel in de verzameling van de NAT-regel kan vervolgens worden gebruikt om het open bare IP-adres en de poort van uw firewall te vertalen naar een privé-IP-adres en-poort. Met DNAT-regels wordt er impliciet een overeenkomende netwerkregel toegevoegd om het omgezette verkeer toe te staan. Uit veiligheids overwegingen is het raadzaam een specifieke Internet bron toe te voegen om DNAT toegang te geven tot het netwerk en om te voor komen dat Joker tekens worden gebruikt. Zie [Verwerkingslogica voor Azure Firewall-regels](rule-processing.md) voor meer informatie over de verwerkingslogica voor Azure Firewall-regels.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een testnetwerkomgeving instellen
> * Een firewall implementeren
> * Een standaardroute maken
> * Een DNAT-regel configureren
> * De firewall testen

## <a name="prerequisites"></a>Vereisten

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.



## <a name="create-a-resource-group"></a>Een resourcegroep maken

1. Meld u aan bij de Azure Portal op [https://portal.azure.com](https://portal.azure.com).
2. Selecteer op de startpagina van Azure Portal **Resourcegroepen** en vervolgens **Toevoegen**.
4. Bij **Abonnement** selecteert u uw abonnement.
1. Bij **Resourcegroepnaam** typt u **RG-DNAT-Test**.
5. Selecteer voor **regio** een regio. Alle andere resources die u maakt, moeten zich in dezelfde regio bevinden.
6. Selecteer **Controleren + maken**.
1. Selecteer **Maken**.

## <a name="set-up-the-network-environment"></a>De netwerkomgeving instellen

In deze zelfstudie maakt u twee VNets die peer zijn van elkaar:

- **VN-Hub**: de firewall bevindt zich in dit VNet.
- **VN-Spoke**: de workloadserver bevindt zich in dit VNet.

Maak eerst de VNets en peer ze.

### <a name="create-the-hub-vnet"></a>Een hub-VNet maken

1. Selecteer op de startpagina van Azure Portal de optie **Alle services**.
2. Onder **Netwerken** selecteert u **Virtuele netwerken**.
3. Selecteer **Toevoegen**.
7. Selecteer voor **resource groep** de optie **RG-DNAT-test**.
1. Bij **Naam** typt u **VN-Hub**.
1. Selecteer voor **regio** dezelfde regio die u eerder hebt gebruikt.
1. Selecteer **Volgende: IP-adressen**.
1. Accepteer de standaard **10.0.0.0/16** voor **IPv4-adres ruimte**.
1. Selecteer bij **subnetnaam** de optie standaard.
1. Bewerk de **naam** van het subnet en typ **AzureFirewallSubnet**.

     De firewall zal zich in dit subnet bevinden, en de subnetnaam **moet** AzureFirewallSubnet zijn.
     > [!NOTE]
     > De grootte van het subnet AzureFirewallSubnet is /26. Zie [Veelgestelde vragen over Azure Firewall](firewall-faq.yml#why-does-azure-firewall-need-a--26-subnet-size) voor meer informatie over de grootte van het subnet.

10. Typ **10.0.1.0/26** voor het **adres bereik** van het subnet.
11. Selecteer **Opslaan**.
1. Selecteer **Controleren + maken**.
1. Selecteer **Maken**.

### <a name="create-a-spoke-vnet"></a>Een spoke-VNet maken

1. Selecteer op de startpagina van Azure Portal de optie **Alle services**.
2. Onder **Netwerken** selecteert u **Virtuele netwerken**.
3. Selecteer **Toevoegen**.
1. Selecteer voor **resource groep** de optie **RG-DNAT-test**.
1. Bij **Naam** typt u **VN-Spoke**.
1. Selecteer voor **regio** dezelfde regio die u eerder hebt gebruikt.
1. Selecteer **Volgende: IP-adressen**.
1. Bewerk voor **IPv4-adres ruimte** de standaard waarde en typ **192.168.0.0/16**.
1. Selecteer **Subnet toevoegen**.
1. Typ voor de naam van het **subnet** **sn-workload**.
10. Typ **192.168.1.0/24** bij **Subnetadresbereik**.
11. Selecteer **Toevoegen**.
1. Selecteer **Controleren en maken**.
1. Selecteer **Maken**.

### <a name="peer-the-vnets"></a>De VNets instellen als peers

Nu gaat u de twee VNets als peer van elkaar instellen.

1. Selecteer het virtuele netwerk **VN-Hub**.
2. Selecteer onder **Instellingen** de optie **Peerings**.
3. Selecteer **Toevoegen**.
4. Onder **dit virtuele netwerk**, voor de **naam van de peering-koppeling**, typt u **peer-HubSpoke**.
5. Onder **extern virtueel netwerk**, voor **koppelings naam van peering**, typt u **peer-SpokeHub**. 
1. Selecteer **VN-Spoke** voor het virtuele netwerk.
1. Accepteer alle overige standaard waarden en selecteer vervolgens **toevoegen**.

## <a name="create-a-virtual-machine"></a>Een virtuele machine maken

Maak een virtuele machine met de naam 'workload' en plaats deze in het subnet **SN-Workload**.

1. Selecteer **Een resource maken** in het menu van de Azure-portal.
2. Selecteer **Windows Server 2016 Datacenter** onder **Populair**.

**Basisinstellingen**

1. Bij **Abonnement** selecteert u uw abonnement.
1. Selecteer voor **resource groep** de optie **RG-DNAT-test**.
1. Typ **Srv-Workload** voor **Identiteit van virtuele machine**.
1. Bij **Regio** selecteert u dezelfde locatie die u eerder hebt gebruikt.
1. Typ een gebruikersnaam en wachtwoord.
1. Selecteer **Volgende: Schijven**.

**Disks**
1. Selecteer **Volgende: Netwerken**.

**Netwerken**

1. Bij **Virtueel netwerk** selecteert u **VN-Spoke**.
2. Bij **Subnet** selecteert u **SN-Workload**.
3. Selecteer **Geen** voor **Openbaar IP**.
4. Bij **Openbare binnenkomende poorten** selecteert u **Geen**. 
2. Wijzig de overige standaard instellingen en selecteer **volgende: beheer**.

**Beheer**

1. Selecteer **uitschakelen** voor **Diagnostische gegevens over opstarten**.
1. Selecteer **Controleren + maken**.

**Beoordelen en maken**

Controleer de samenvatting en selecteer **Maken**. Het duurt enkele minuten voordat dit is voltooid.

Als de implementatie is voltooid, ziet u het privé IP-adres voor de virtuele machine. Noteer dit voor later gebruik, wanneer u de firewall gaat configureren. Selecteer de naam van de virtuele machine en selecteer onder **Instellingen** de optie **Netwerken** om het privé-IP-adres te vinden.

## <a name="deploy-the-firewall"></a>De firewall implementeren

1. Selecteer op de startpagina van de portal **Een resource maken**.
1. Zoek naar **firewall** en selecteer vervolgens **firewall**.
1. Selecteer **Maken**. 
1. Gebruik op de pagina **Firewall maken** de volgende tabel om de firewall te configureren:

   |Instelling  |Waarde  |
   |---------|---------|
   |Abonnement     |\<your subscription\>|
   |Resourcegroep     |Selecteer **RG-DNAT-test** |
   |Name     |**FW-DNAT-test**|
   |Region     |Selecteer dezelfde locatie die u eerder hebt gebruikt|
   |Firewall beheer|**Firewall regels (klassiek) gebruiken voor het beheren van deze firewall**|
   |Een virtueel netwerk kiezen     |**Bestaande gebruiken**: VN-Hub|
   |Openbaar IP-adres     |**Nieuwe toevoegen**, naam: **FW-PIP**.|

5. Accepteer de andere standaard waarden en selecteer vervolgens **bekijken + maken**.
6. Controleer de samenvatting en selecteer vervolgens **Maken** om de firewall te maken.

   Het duurt enkele minuten voordat deze is geïmplementeerd.
7. Nadat de implementatie is voltooid, gaat u naar de resourcegroep **RG-DNAT-Test** en selecteert u de firewall **FW-DNAT-test**.
8. Noteer de persoonlijke en open bare IP-adressen van de firewall. U gebruikt deze later bij het maken van de standaard route en de NAT-regel.

## <a name="create-a-default-route"></a>Een standaardroute maken

Voor het subnet **SN-Workload** configureert u dat de standaardroute voor uitgaand verkeer via de firewall loopt.

1. Selecteer op de startpagina van Azure Portal de optie **Alle services**.
2. Selecteer onder **Netwerken** de optie **Routetabellen**.
3. Selecteer **Toevoegen**.
5. Bij **Abonnement** selecteert u uw abonnement.
1. Selecteer voor **resource groep** de optie **RG-DNAT-test**.
1. Bij **Regio** selecteert u dezelfde regio die u eerder hebt gebruikt.
1. Bij **Naam** typt u **RT-FWroute**.
1. Selecteer **Controleren + maken**.
1. Selecteer **Maken**.
1. Selecteer **Ga naar resource**.
1. Selecteer **Subnetten** en vervolgens **Koppelen**.
1. Bij **Virtueel netwerk** selecteert u **VN-Spoke**.
1. Bij **Subnet** selecteert u **SN-Workload**.
1. Selecteer **OK**.
1. Selecteer **Routes** en vervolgens **Toevoegen**.
1. Bij **Routenaam** typt u **FW-DG**.
1. Bij **Adresvoorvoegsel** typt u **0.0.0.0/0**.
1. Bij **Volgend hoptype** selecteert u **Virtueel apparaat**.

    Azure Firewall is eigenlijk een beheerde service, maar Virtueel apparaat werkt in deze situatie.
18. Bij **Adres van de volgende hop** typt u het privé-IP-adres voor de firewall dat u eerder hebt genoteerd.
19. Selecteer **OK**.

## <a name="configure-a-nat-rule"></a>Een NAT-regel configureren

1. Open de resource groep **RG-DNAT-test** en selecteer de **FW-DNAT-test-** firewall. 
2. Selecteer op de pagina **FW-DNAT-test** onder **instellingen** de optie **regels (klassiek)**. 
3. Selecteer **Verzameling van NAT-regels toevoegen**. 
4. Bij **Naam** typt u **RC-DNAT-01**. 
5. Bij **Prioriteit** typt u **200**. 
6. Onder **Regels** typt u bij **Naam** de naam **RL-01**.
7. Bij **Protocol** selecteert u **TCP**.
1. Selecteer **IP-adres** bij **Brontype**.
1. Typ * bij **bron**. 
1. Voor **doel adressen** typt u het open bare IP-adres van de firewall. 
1. Bij **Doelpoorten** typt u **3389**. 
1. Bij **Omgezet adres** typt u het privé-IP-adres voor de virtuele machine Srv-Workload. 
1. Bij **Vertaalde poort** typt u **3389**. 
1. Selecteer **Toevoegen**. Het duurt enkele minuten voordat dit is voltooid.

## <a name="test-the-firewall"></a>De firewall testen

1. Verbind een extern-bureaubladsessie met het openbare IP-adres van de firewall. U wordt als het goed is verbonden met de virtuele machine **Srv-Workload**.
2. Sluit de sessie van Extern bureaublad.

## <a name="clean-up-resources"></a>Resources opschonen

U kunt de firewall-resources behouden voor de volgende zelfstudie. Als u ze niet meer nodig hebt, verwijdert u de resourcegroep **RG-DNAT-Test** om alle aan de firewall gerelateerde resources te verwijderen.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie heeft u het volgende geleerd:

> [!div class="checklist"]
> * Een testnetwerkomgeving instellen
> * Een firewall implementeren
> * Een standaardroute maken
> * Een DNAT-regel configureren
> * De firewall testen

Als volgende kunt u de Azure Firewall-logboeken bewaken.

> [!div class="nextstepaction"]
> [Zelfstudie: Azure Firewall-logboeken bewaken](./firewall-diagnostics.md)
