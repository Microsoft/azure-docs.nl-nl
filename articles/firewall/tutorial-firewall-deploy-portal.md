---
title: 'Zelfstudie: Azure Firewall implementeren en configureren met de Azure-portal'
description: In deze zelfstudie leert u hoe u Azure Firewall kunt implementeren en configureren via de Azure-portal.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: tutorial
ms.date: 07/15/2020
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: 40e4a3d3a819280255ee931e5e12206f99ef28fe
ms.sourcegitcommit: 8dd8d2caeb38236f79fe5bfc6909cb1a8b609f4a
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/08/2021
ms.locfileid: "98051510"
---
# <a name="tutorial-deploy-and-configure-azure-firewall-using-the-azure-portal"></a>Zelfstudie: Azure Firewall implementeren en configureren met de Azure-portal

Het beheren van toegang tot uitgaande netwerken is een belangrijk onderdeel van een algemeen netwerkbeveiligingsabonnement. U kunt bijvoorbeeld de toegang tot websites beperken. U kunt ook de toegang tot uitgaande IP-adressen en poorten beperken.

Een van de manieren waarop u de toegang tot uitgaande netwerken kunt beheren vanaf een Azure-subnet is met Azure Firewall. Met Azure Firewall kunt u het volgende configureren:

* Toepassingsregels die volledig gekwalificeerde domeinnamen (FQDN's) definiëren waartoe toegang kan worden verkregen via een subnet.
* Netwerkregels die een bronadres, protocol, doelpoort en doeladres definiëren.

Netwerkverkeer is onderhevig aan de geconfigureerde firewallregels wanneer u het routeert naar de firewall als standaardgateway van het subnet.

Voor deze zelfstudie maakt u een vereenvoudigd VNet met twee subnetten voor eenvoudige implementatie.

Voor productie-implementaties wordt een [hub en spoke-model](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke) aanbevolen, waarbij de firewall zich in een eigen VNet bevindt. De werkbelastingservers bevinden zich in gepeerde VNets in dezelfde regio met een of meer subnetten.

* **AzureFirewallSubnet** – De firewall bevindt zich in dit subnet.
* **Workload-SN** – De workloadserver bevindt zich in dit subnet. Het netwerkverkeer van dit subnet gaat via de firewall.

![Netwerkinfrastructuur van zelfstudie](media/tutorial-firewall-deploy-portal/tutorial-network.png)

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een testnetwerkomgeving instellen
> * Een firewall implementeren
> * Een standaardroute maken
> * Een toepassingsregel configureren om toegang tot www.google.com toe te staan
> * Een netwerkregel configureren om toegang tot externe DNS-servers toe te staan
> * Een NAT-regel configureren om een extern bureaublad op de testserver toe te staan
> * De firewall testen

U kunt deze zelfstudie desgewenst volgen met behulp van [Azure PowerShell](deploy-ps.md).

## <a name="prerequisites"></a>Vereisten

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="set-up-the-network"></a>Het netwerk instellen

Maak eerst een resourcegroep met de resources die nodig zijn om de firewall te implementeren. Maak vervolgens een VNet, subnetten en een testserver.

### <a name="create-a-resource-group"></a>Een resourcegroep maken

De resourcegroep bevat alle resources voor de zelfstudie.

1. Meld u aan bij de Azure Portal op [https://portal.azure.com](https://portal.azure.com).
2. Selecteer in het menu van Azure-portal de optie **Resourcegroepen** of zoek ernaar en selecteer *Resourcegroepen* vanaf een willekeurige pagina. Selecteer vervolgens **Toevoegen**.
3. Bij **Resourcegroepnaam** typt u *Test-FW-RG*.
4. Bij **Abonnement** selecteert u uw abonnement.
5. Bij **Resourcegroeplocatie** selecteert u een locatie. Alle andere resources die u maakt, moeten zich op dezelfde locatie bevinden.
6. Selecteer **Maken**.

### <a name="create-a-vnet"></a>Een VNet maken

Dit VNet bevat drie subnetten.

> [!NOTE]
> De grootte van het subnet AzureFirewallSubnet is /26. Zie [Veelgestelde vragen over Azure Firewall](firewall-faq.yml#why-does-azure-firewall-need-a--26-subnet-size) voor meer informatie over de grootte van het subnet.

1. Selecteer in het menu van de Azure-portal of op de **startpagina** de optie **Een resource maken**.
1. Selecteer **Netwerk** > **Virtueel netwerk**.
2. Bij **Abonnement** selecteert u uw abonnement.
3. Bij **Resourcegroep** selecteert u **Test-FW-RG**.
4. Bij **Naam** typt u **Test-FW-VN**.
5. Bij **Regio** selecteert u dezelfde locatie die u eerder hebt gebruikt.
6. Selecteer **Volgende: IP-adressen**.
7. Bij **IPv4-adresruimte** typt u **10.0.0.0/16**.
8. Onder **Subnet** selecteert u **standaard**.
9. Bij **Subnetnaam** typt u **AzureFirewallSubnet**. De firewall zal zich in dit subnet bevinden, en de subnetnaam **moet** AzureFirewallSubnet zijn.
10. Bij **Adresbereik** typt u **10.0.1.0/26**.
11. Selecteer **Opslaan**.

   Maak hierna een subnet voor de werkbelastingserver.

1. Selecteer **Subnet toevoegen**.
4. Bij **Subnetnaam** typt u **Workload-SN**.
5. Als **Subnetadresbereik** typt u **10.0.2.0/24**.
6. Selecteer **Toevoegen**.
7. Selecteer **Controleren en maken**.
8. Selecteer **Maken**.

### <a name="create-a-virtual-machine"></a>Een virtuele machine maken

Maak nu de virtuele machine voor de werkbelasting en plaats deze in het subnet **Workload-SN**.

1. Selecteer in het menu van de Azure-portal of op de **startpagina** de optie **Een resource maken**.
2. Selecteer **Compute** en vervolgens **Virtuele machine**.
3. **Windows Server 2016 Datacenter** in the de lijst Aanbevolen.
4. Voer deze waarden in voor de virtuele machine:

   |Instelling  |Waarde  |
   |---------|---------|
   |Resourcegroep     |**Test-FW-RG**|
   |Naam van de virtuele machine     |**Srv-Work**|
   |Regio     |Hetzelfde als vorige|
   |Installatiekopie|Windows Server 2019 Datacenter|
   |Beheerdersgebruikersnaam     |Typ een gebruikersnaam|
   |Wachtwoord     |Typ een wachtwoord|

4. Selecteer onder **Regels voor binnenkomende poort**, **Openbare binnenkomende poorten** de optie **Geen**.
6. Accepteer de overige standaardwaarden en selecteer **Volgende: Schijven**.
7. Accepteer de standaardwaarden voor schijven en selecteer **Volgende: Netwerken**.
8. Zorg ervoor dat **Test-FW-VN** is geselecteerd voor het virtuele netwerk en dat het subnet **Workload-SN** is.
9. Selecteer **Geen** voor **Openbaar IP**.
11. Accepteer de overige standaardwaarden en selecteer **Volgende: Beheer**.
12. Selecteer **Uit** om diagnostische gegevens over opstarten uit te schakelen. Accepteer de overige standaardwaarden en selecteer **Beoordelen en maken**.
13. Controleer de instellingen op de overzichtspagina en selecteer **Maken**.

## <a name="deploy-the-firewall"></a>De firewall implementeren

Implementeer de firewall in het VNet.

1. Selecteer in het menu van de Azure-portal of op de **startpagina** de optie **Een resource maken**.
2. Typ **firewall** in het zoekvak en druk op **Enter**.
3. Selecteer **Firewall** en vervolgens **Maken**.
4. Gebruik op de pagina **Firewall maken** de volgende tabel om de firewall te configureren:

   |Instelling  |Waarde  |
   |---------|---------|
   |Abonnement     |\<your subscription\>|
   |Resourcegroep     |**Test-FW-RG** |
   |Naam     |**Test-FW01**|
   |Locatie     |Selecteer dezelfde locatie die u eerder hebt gebruikt|
   |Een virtueel netwerk kiezen     |**Bestaande gebruiken**: **Test-FW-VN**|
   |Openbaar IP-adres     |**Nieuwe toevoegen**<br>**Naam**:  **fw-pip**|

5. Selecteer **Controleren + maken**.
6. Controleer de samenvatting en selecteer vervolgens **Maken** om de firewall te maken.

   Het duurt enkele minuten voordat deze is geïmplementeerd.
7. Zodra de implementatie is voltooid, gaat u naar de resourcegroep **Test-FW-RG** en selecteert u de firewall **Test-FW01**.
8. Noteer het privé- en openbare IP-adres van de firewall. U hebt deze later nodig.

## <a name="create-a-default-route"></a>Een standaardroute maken

Voor het subnet **Workload-SN** configureert u de standaardroute voor uitgaand verkeer om via de firewall te gaan.

1. Selecteer in het menu van Azure-portal de optie **Alle services** of zoek naar en selecteer *Alle services* vanaf elke willekeurige pagina.
2. Selecteer onder **Netwerken** de optie **Routetabellen**.
3. Selecteer **Toevoegen**.
4. Bij **Naam** typt u **Firewall-route**.
5. Bij **Abonnement** selecteert u uw abonnement.
6. Bij **Resourcegroep** selecteert u **Test-FW-RG**.
7. Bij **Locatie** selecteert u dezelfde locatie die u eerder hebt gebruikt.
8. Selecteer **Maken**.
9. Selecteer **Vernieuwen** en selecteer vervolgens de routetabel **Firewall-route**.
10. Selecteer **Subnetten** en selecteer vervolgens **Koppelen**.
11. Selecteer **Virtueel netwerk** > **Test-FW-VN**.
12. Bij **Subnet** selecteert u **Workload-SN**. Zorg ervoor dat u alleen het subnet **Workload-SN** selecteert voor deze route, anders werkt de firewall niet correct.

13. Selecteer **OK**.
14. Selecteer **Routes** en vervolgens **Toevoegen**.
15. Bij **Routenaam** typt u **fw-dg**.
16. Bij **Adresvoorvoegsel** typt u **0.0.0.0/0**.
17. Bij **Volgend hoptype** selecteert u **Virtueel apparaat**.

    Azure Firewall is eigenlijk een beheerde service, maar Virtueel apparaat werkt in deze situatie.
18. Bij **Adres van de volgende hop** typt u het privé-IP-adres voor de firewall dat u eerder hebt genoteerd.
19. Selecteer **OK**.

## <a name="configure-an-application-rule"></a>Een toepassingsregel configureren

Dit is de toepassingsregel waarmee uitgaande toegang tot `www.google.com` wordt toegestaan.

1. Open de resourcegroep **Test-FW-RG** en selecteer de firewall **Test-FW01**.
2. Selecteer op de pagina **Test-FW01** onder **Instellingen** de optie **Regels**.
3. Selecteer het tabblad **Verzameling met toepassingsregels**.
4. Selecteer **Toepassingsregelverzameling toevoegen**.
5. Bij **Naam** typt u **App-Coll01**.
6. Bij **Prioriteit** typt u **200**.
7. Bij **Actie** selecteert u **Toestaan**.
8. Onder **Regels**, **Doel-FQDN's**, typt u bij **Naam** **Allow-Google**.
9. Selecteer **IP-adres** bij **Brontype**.
10. Typ bij **Bron** **10.0.2.0/24**.
11. Bij **Protocol:poort** typt u **http, https**.
12. Bij **Doel-FQDN's** typt u **`www.google.com`**
13. Selecteer **Toevoegen**.

Azure Firewall bevat een ingebouwde regelverzameling voor infrastructuur-FQDN’s die standaard zijn toegestaan. Deze FQDN’s zijn specifiek voor het platform en kunnen niet voor andere doeleinden worden gebruikt. Zie [FQDN's voor infrastructuur](infrastructure-fqdns.md) voor meer informatie.

## <a name="configure-a-network-rule"></a>Een netwerkregel configureren

Dit is de netwerkregel waarmee uitgaande toegang tot twee IP-adressen op poort 53 (DNS) wordt toegestaan.

1. Selecteer het tabblad **Verzameling met netwerkregels**.
2. Selecteer **Verzameling met netwerkregels toevoegen**.
3. Bij **Naam** typt u **Net-Coll01**.
4. Bij **Prioriteit** typt u **200**.
5. Bij **Actie** selecteert u **Toestaan**.
6. Onder **Regels**, **IP-adressen**, **Naam** typt u **Allow-DNS**.
7. Bij **Protocol** selecteert u **UDP**.
9. Selecteer **IP-adres** bij **Brontype**.
1. Typ bij **Bron** **10.0.2.0/24**.
2. Bij **Doeltype** selecteert u **IP-adres**.
3. Typ bij **Doeladres** **209.244.0.3,209.244.0.4**

   Dit zijn openbare DNS-servers die worden beheerd door CenturyLink.
1. Bij **Doelpoorten** typt u **53**.
2. Selecteer **Toevoegen**.

## <a name="configure-a-dnat-rule"></a>Een DNAT-regel configureren

Met deze regel kunt u een extern bureaublad via de firewall verbinden met de virtuele machine Srv-Work.

1. Selecteer het tabblad **Verzameling van NAT-regels**.
2. Selecteer **Verzameling van NAT-regels toevoegen**.
3. Typ **rdp** bij **Naam**.
4. Bij **Prioriteit** typt u **200**.
5. Onder **Regels** typt u bij **Naam** de naam **rdp-nat**.
6. Bij **Protocol** selecteert u **TCP**.
7. Selecteer **IP-adres** bij **Brontype**.
8. Typ * *\** _ bij **Bron**.
9. Typ bij _*Doeladressen** het openbare IP-adres van de firewall.
10. Typ bij **Doelpoorten** **3389**.
11. Bij **Omgezet adres** typt u het privé-IP-adres voor **Srv-work**.
12. Bij **Vertaalde poort** typt u **3389**.
13. Selecteer **Toevoegen**.


### <a name="change-the-primary-and-secondary-dns-address-for-the-srv-work-network-interface"></a>Het primaire en secundaire DNS-adres voor de netwerkinterface **Srv-Work** wijzigen

Voor testdoeleinden in deze zelfstudie configureert u het primaire en secundaire DNS-adres van de server. Dit is geen algemene Azure Firewall-vereiste.

1. Selecteer in het menu van Azure-portal de optie **Resourcegroepen** of zoek ernaar en selecteer *Resourcegroepen* vanaf een willekeurige pagina. Selecteer de resourcegroep **Test-FW-RG**.
2. Selecteer de netwerkinterface voor de virtuele machine **Srv-Work**.
3. Selecteer onder **Instellingen** de optie **DNS-servers**.
4. Selecteer onder **DNS-servers** de optie **Aangepast**.
5. Typ **209.244.0.3** in het tekstvak **DNS-server toevoegen** en **209.244.0.4** in het volgende tekstvak.
6. Selecteer **Opslaan**.
7. Start de virtuele machine **Srv-Work** opnieuw.

## <a name="test-the-firewall"></a>De firewall testen

Test nu de firewall om te controleren of deze werkt zoals verwacht.

1. Verbind een extern bureaublad met het openbare IP-adres van de firewall en meld u aan bij de virtuele machine **Srv-Work**. 
3. Open Internet Explorer en blader naar `https://www.google.com`.
4. Selecteer **OK** > **Sluiten** in de beveiligingswaarschuwingen van Internet Explorer.

   U zou nu de startpagina van Google moeten zien.

5. Blader naar `https://www.microsoft.com`.

   U zou nu door de firewall moeten worden geblokkeerd.

Nu u hebt geverifieerd dat de firewallregels werken:

* Kunt u bladeren naar de enige toegestane FQDN, maar niet naar andere.
* Kunt u DNS-namen omzetten met behulp van de geconfigureerde externe DNS-server.

## <a name="clean-up-resources"></a>Resources opschonen

U kunt de firewall-resources voor de volgende zelfstudie bewaren. Als u ze niet meer nodig hebt, verwijdert u de resourcegroep **Test-FW-RG** om alle firewall-gerelateerde resources te verwijderen.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Zelfstudie: Azure Firewall-logboeken bewaken](./firewall-diagnostics.md)