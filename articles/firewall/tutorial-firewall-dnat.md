---
title: 'Zelfstudie: Binnenkomend verkeer filteren met Azure Firewall DNAT via de portal'
description: In deze zelfstudie leert u hoe u Azure Firewall DNAT kunt implementeren en configureren via de Azure-portal.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: tutorial
ms.date: 08/28/2020
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: 281d0587ca4c041c7149e49aad6227f6dc0b7fbf
ms.sourcegitcommit: 8dd8d2caeb38236f79fe5bfc6909cb1a8b609f4a
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/08/2021
ms.locfileid: "98050864"
---
# <a name="tutorial-filter-inbound-internet-traffic-with-azure-firewall-dnat-using-the-azure-portal"></a>Zelfstudie: Binnenkomend verkeer filteren met Azure Firewall DNAT via Azure Portal

U kunt Azure Firewall Destination Network Address Translation (DNAT) configureren voor het omzetten en filteren van inkomend verkeer van internet naar uw subnetten. Wanneer u DNAT configureert, wordt de actie Verzameling van NAT-regels ingesteld op **Dnat**. Elke regel in de verzameling van NAT-regels kan vervolgens worden gebruikt voor de omzetting van het openbare IP-adres en de openbare poort van uw firewall in een privé-IP-adres en -poort. Met DNAT-regels wordt er impliciet een overeenkomende netwerkregel toegevoegd om het omgezette verkeer toe te staan. U kunt dit gedrag overschrijven door expliciet een verzameling netwerkregels toe te voegen met regels voor weigeren die overeenkomen met het omgezette verkeer. Zie [Verwerkingslogica voor Azure Firewall-regels](rule-processing.md) voor meer informatie over de verwerkingslogica voor Azure Firewall-regels.

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
3. Bij **Resourcegroepnaam** typt u **RG-DNAT-Test**.
4. Bij **Abonnement** selecteert u uw abonnement.
5. Bij **Resourcegroeplocatie** selecteert u een locatie. Alle volgende resources die u maakt, moeten zich op dezelfde locatie bevinden.
6. Selecteer **Maken**.

## <a name="set-up-the-network-environment"></a>De netwerkomgeving instellen

In deze zelfstudie maakt u twee VNets die peer zijn van elkaar:

- **VN-Hub**: de firewall bevindt zich in dit VNet.
- **VN-Spoke**: de workloadserver bevindt zich in dit VNet.

Maak eerst de VNets en peer ze.

### <a name="create-the-hub-vnet"></a>Een hub-VNet maken

1. Selecteer op de startpagina van Azure Portal de optie **Alle services**.
2. Onder **Netwerken** selecteert u **Virtuele netwerken**.
3. Selecteer **Toevoegen**.
4. Bij **Naam** typt u **VN-Hub**.
5. Bij **Adresruimte** typt u **10.0.0.0/16**.
6. Bij **Abonnement** selecteert u uw abonnement.
7. Bij **Resourcegroep** selecteert u **Bestaande gebruiken** en selecteert u vervolgens **RG-DNAT-Test**.
8. Bij **Locatie** selecteert u dezelfde locatie die u eerder hebt gebruikt.
9. Onder **Subnet** typt u bij **Naam** de naam **AzureFirewallSubnet**.

     De firewall zal zich in dit subnet bevinden, en de subnetnaam **moet** AzureFirewallSubnet zijn.
     > [!NOTE]
     > De grootte van het subnet AzureFirewallSubnet is /26. Zie [Veelgestelde vragen over Azure Firewall](firewall-faq.yml#why-does-azure-firewall-need-a--26-subnet-size) voor meer informatie over de grootte van het subnet.

10. Bij **Adresbereik** typt u **10.0.1.0/26**.
11. Gebruik de overige standaardinstellingen en selecteer **Maken**.

### <a name="create-a-spoke-vnet"></a>Een spoke-VNet maken

1. Selecteer op de startpagina van Azure Portal de optie **Alle services**.
2. Onder **Netwerken** selecteert u **Virtuele netwerken**.
3. Selecteer **Toevoegen**.
4. Bij **Naam** typt u **VN-Spoke**.
5. Bij **Adresruimte** typt u **192.168.0.0/16**.
6. Bij **Abonnement** selecteert u uw abonnement.
7. Bij **Resourcegroep** selecteert u **Bestaande gebruiken** en selecteert u vervolgens **RG-DNAT-Test**.
8. Bij **Locatie** selecteert u dezelfde locatie die u eerder hebt gebruikt.
9. Onder **Subnet** typt u **SN-Workload** bij **Naam**.

    De server wordt in dit subnet geplaatst.
10. Bij **Adresbereik** typt u **192.168.1.0/24**.
11. Gebruik de overige standaardinstellingen en selecteer **Maken**.

### <a name="peer-the-vnets"></a>De VNets instellen als peers

Nu gaat u de twee VNets als peer van elkaar instellen.

1. Selecteer het virtuele netwerk **VN-Hub**.
2. Selecteer onder **Instellingen** de optie **Peerings**.
3. Selecteer **Toevoegen**.
4. Typ **Peer-HubSpoke** voor de **naam van de peering van VN-Hub naar VN-Spoke**.
5. Selecteer **VN-Spoke** voor het virtuele netwerk.
6. Typ **Peer-SpokeHub** voor de **naam van de peering van VN-Spoke naar VN-Hub**.
7. Selecteer **Ingeschakeld** onder **Doorgestuurd verkeer van VN-Spoke to VN-Hub toestaan**.
8. Selecteer **OK**.

## <a name="create-a-virtual-machine"></a>Een virtuele machine maken

Maak een virtuele machine met de naam 'workload' en plaats deze in het subnet **SN-Workload**.

1. Selecteer **Een resource maken** in het menu van de Azure-portal.
2. Selecteer **Windows Server 2016 Datacenter** onder **Populair**.

**Basisinstellingen**

1. Bij **Abonnement** selecteert u uw abonnement.
1. Bij **Resourcegroep** selecteert u **Bestaande gebruiken** en selecteert u vervolgens **RG-DNAT-Test**.
1. Typ **Srv-Workload** voor **Identiteit van virtuele machine**.
1. Bij **Regio** selecteert u dezelfde locatie die u eerder hebt gebruikt.
1. Typ een gebruikersnaam en wachtwoord.
1. Selecteer **Volgende: Schijven**.

**Disks**
1. Selecteer **Volgende: Netwerken**.

**Netwerken**

1. Bij **Virtueel netwerk** selecteert u **VN-Spoke**.
2. Bij **Subnet** selecteert u **SN-Workload**.
3. Bij **Openbaar IP-adres** selecteert u **Geen**.
4. Bij **Openbare binnenkomende poorten** selecteert u **Geen**. 
2. Wijzig de andere standaardinstellingen niet en selecteer **Volgende: Beheer**.

**Beheer**

1. Selecteer **Uit** voor **Diagnostische gegevens over opstarten**.
1. Selecteer **Controleren + maken**.

**Beoordelen en maken**

Controleer de samenvatting en selecteer **Maken**. Het duurt enkele minuten voordat dit is voltooid.

Als de implementatie is voltooid, ziet u het privé IP-adres voor de virtuele machine. Noteer dit voor later gebruik, wanneer u de firewall gaat configureren. Selecteer de naam van de virtuele machine en selecteer onder **Instellingen** de optie **Netwerken** om het privé-IP-adres te vinden.

## <a name="deploy-the-firewall"></a>De firewall implementeren

1. Selecteer op de startpagina van de portal **Een resource maken**.
2. Selecteer **Netwerken** en selecteer bij **Aanbevolen** **Alles weergeven**.
3. Selecteer **Firewall** en vervolgens **Maken**. 
4. Gebruik op de pagina **Firewall maken** de volgende tabel om de firewall te configureren:

   |Instelling  |Waarde  |
   |---------|---------|
   |Naam     |FW-DNAT-test|
   |Abonnement     |\<your subscription\>|
   |Resourcegroep     |**Bestaande gebruiken**: RG-DNAT-Test |
   |Locatie     |Selecteer dezelfde locatie die u eerder hebt gebruikt|
   |Een virtueel netwerk kiezen     |**Bestaande gebruiken**: VN-Hub|
   |Openbaar IP-adres     |**Nieuwe maken**. Het openbare IP-adres moet van het type Standaard-SKU zijn.|

5. Selecteer **Controleren + maken**.
6. Controleer de samenvatting en selecteer vervolgens **Maken** om de firewall te maken.

   Het duurt enkele minuten voordat deze is geïmplementeerd.
7. Nadat de implementatie is voltooid, gaat u naar de resourcegroep **RG-DNAT-Test** en selecteert u de firewall **FW-DNAT-test**.
8. Noteer het privé-IP-adres. U zult het later gebruiken wanneer u de standaardroute maakt.

## <a name="create-a-default-route"></a>Een standaardroute maken

Voor het subnet **SN-Workload** configureert u dat de standaardroute voor uitgaand verkeer via de firewall loopt.

1. Selecteer op de startpagina van Azure Portal de optie **Alle services**.
2. Selecteer onder **Netwerken** de optie **Routetabellen**.
3. Selecteer **Toevoegen**.
4. Bij **Naam** typt u **RT-FWroute**.
5. Bij **Abonnement** selecteert u uw abonnement.
6. Bij **Resourcegroep** selecteert u **Bestaande gebruiken** en selecteert u **RG-DNAT-Test**.
7. Bij **Locatie** selecteert u dezelfde locatie die u eerder hebt gebruikt.
8. Selecteer **Maken**.
9. Selecteer **Vernieuwen** en vervolgens de routetabel **RT-FWroute**.
10. Selecteer **Subnetten** en vervolgens **Koppelen**.
11. Selecteer **Virtueel netwerk** en vervolgens **VN-Spoke**.
12. Bij **Subnet** selecteert u **SN-Workload**.
13. Selecteer **OK**.
14. Selecteer **Routes** en vervolgens **Toevoegen**.
15. Bij **Routenaam** typt u **FW-DG**.
16. Bij **Adresvoorvoegsel** typt u **0.0.0.0/0**.
17. Bij **Volgend hoptype** selecteert u **Virtueel apparaat**.

    Azure Firewall is eigenlijk een beheerde service, maar Virtueel apparaat werkt in deze situatie.
18. Bij **Adres van de volgende hop** typt u het privé-IP-adres voor de firewall dat u eerder hebt genoteerd.
19. Selecteer **OK**.

## <a name="configure-a-nat-rule"></a>Een NAT-regel configureren

1. Open **RG-DNAT-Test** en selecteer de firewall **FW-DNAT-test**. 
2. Selecteer op de pagina **FW-DNAT-test**. onder **Instellingen**, de optie **Regels**. 
3. Selecteer **Verzameling van NAT-regels toevoegen**. 
4. Bij **Naam** typt u **RC-DNAT-01**. 
5. Bij **Prioriteit** typt u **200**. 
6. Onder **Regels** typt u bij **Naam** de naam **RL-01**.
7. Bij **Protocol** selecteert u **TCP**.
8. Bij **Bronadressen** typt u *. 
9. Bij **Doeladressen** typt u het openbare IP-adres van de firewall. 
10. Bij **Doelpoorten** typt u **3389**. 
11. Bij **Omgezet adres** typt u het privé-IP-adres voor de virtuele machine Srv-Workload. 
12. Bij **Vertaalde poort** typt u **3389**. 
13. Selecteer **Toevoegen**. 

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