---
title: 'Zelfstudie: Uw virtuele hub beveiligen met Azure Firewall Manager'
description: In deze zelfstudie leert u hoe u uw virtuele hub beveiligt met Azure Firewall Manager met behulp van Azure Portal.
services: firewall-manager
author: vhorne
ms.service: firewall-manager
ms.topic: tutorial
ms.date: 03/19/2021
ms.author: victorh
ms.openlocfilehash: 14ec6fafbbadff2ecc37b229270c269f068a666f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104670457"
---
# <a name="tutorial-secure-your-virtual-hub-using-azure-firewall-manager"></a>Zelfstudie: Uw virtuele hub beveiligen met Azure Firewall Manager

Met behulp van Azure Firewall Manager kunt u beveiligde virtuele hubs maken om het cloudnetwerkverkeer te beveiligen dat bestemd is voor privé-IP-adressen, Azure PaaS en internet. Omleiding van het verkeer naar de firewall is geautomatiseerd, dus u hoeft geen door de gebruiker gedefinieerde routes (UDR's) te maken.

![Het cloudnetwerk beveiligen](media/secure-cloud-network/secure-cloud-network.png)

Firewall Manager biedt ook ondersteuning voor een virtuele-netwerkarchitectuur met hubs. Zie [Wat zijn de opties voor de Azure Firewall Manager-architectuur?](vhubs-and-vnets.md) voor een vergelijking van de architectuurtypen voor beveiligde virtuele hubs en virtuele hubnetwerken.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Het virtuele spoke-netwerk maken
> * Een beveiligde virtuele hub maken
> * De hub- en virtuele spoke-netwerken verbinden
> * Verkeer doorsturen naar uw hub
> * De servers implementeren
> * Een firewallbeleid maken en uw hub beveiligen
> * De firewall testen

## <a name="prerequisites"></a>Vereisten

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="create-a-hub-and-spoke-architecture"></a>Een hub-en-spoke-architectuur maken

Maak eerst virtuele spoke-netwerken waarin u uw servers kunt plaatsen.

### <a name="create-two-spoke-virtual-networks-and-subnets"></a>Maak twee virtuele spoke-netwerken en subnetten

De twee virtuele netwerken hebben elk een workloadserver en worden beveiligd door de firewall.

1. Selecteer op de startpagina van de Azure-portal **Een resource maken**.
2. Zoek naar **virtueel netwerk** en selecteer **maken**.
2. Bij **Abonnement** selecteert u uw abonnement.
1. Selecteer voor **resource groep** de optie **nieuwe maken** en typ **FW-Manager-RG** voor de naam en selecteer **OK**.
2. Als **Naam** typt u **Spoke-01**.
3. Selecteer bij **Regio** **(VS) VS - oost**.
4. Selecteer **Volgende: IP-adressen**.
1. Bij **Adresruimte** typt u **10.0.0.0/16**.
3. Onder **Subnetnaam** selecteert u **standaard**.
4. Typ voor **subnetnaam de naam**  **workload-01-SN**.
5. Als **Subnetadresbereik** typt u **10.0.1.0/24**.
6. Selecteer **Opslaan**.
1. Selecteer **Controleren + maken**.
2. Selecteer **Maken**.

Herhaal dit proces om nog een soortgelijk virtueel netwerk te maken:

Naam: **Spoke-02**<br>
Adres ruimte: **10.1.0.0/16**<br>
Subnetnaam: **Workload-02-SN**<br>
Adres bereik van subnet: **10.1.1.0/24**

### <a name="create-the-secured-virtual-hub"></a>De beveiligde virtuele hub maken

Maak uw beveiligde virtuele hub met behulp van Firewall Manager.

1. Selecteer op de startpagina van Azure Portal de optie **Alle services**.
2. In het zoekvak typt u **Firewall Manager** en selecteert u **Firewall Manager**.
3. Op de pagina **Firewall Manager** selecteert u **Beveiligde virtuele hubs weergeven**.
4. Op de pagina **Firewall Manager | Beveiligde virtuele hubs** selecteert u **Nieuwe beveiligde virtuele hub maken**.
5. Selecteer voor **resource groep** **FW-Manager-RG**.
7. Selecteer bij **Regio** **VS - oost**.
1. Als de **Naam van beveiligde virtuele hub** typt u **Hub-01**.
2. Voor de **adres ruimte** van de hub typt u **10.2.0.0/16**.
3. Als de nieuwe vWAN-naam typt u **Vwan-01**.
4. Laat het selectievakje **VPN-gateway opnemen om vertrouwde beveiligingspartners in te schakelen** leeg.
5. Selecteer **Volgende: Azure Firewall**.
6. Accepteer de standaardinstelling **Azure Firewall** **Ingeschakeld** en selecteer vervolgens **Volgende: Vertrouwde beveiligingspartner**.
7. Accepteer de standaardinstelling **Vertrouwde beveiligingspartner** **Uitgeschakeld** en selecteer **Volgende: Beoordelen en maken**.
8. Selecteer **Maken**. 

   Het duurt ongeveer 30 minuten om te implementeren.

U kunt het open bare IP-adres van de firewall ophalen nadat de implementatie is voltooid.

1. Open **firewall-beheer**.
2. Selecteer **virtuele hubs**.
3. Selecteer **hub-01**.
7. Selecteer **Configuratie van openbaar IP-adres**.
8. Noteer het openbare IP-adres om later te gebruiken.

### <a name="connect-the-hub-and-spoke-virtual-networks"></a>De hub- en virtuele spoke-netwerken verbinden

U kunt nu de virtuele hub- en spoke-netwerken koppelen.

1. Selecteer de resource groep **FW-Manager-RG** en selecteer vervolgens de **Vwan-01** virtuele WAN.
2. Onder **Connectiviteit** selecteert u **Virtuele netwerkverbindingen**.
3. Selecteer **Verbinding toevoegen**.
4. Als **Verbindingsnaam** typt u **hub-spoke-01**.
5. Als **Hubs** selecteert u **Hub-01**.
6. Selecteer voor **resource groep** **FW-Manager-RG**.
7. Als **Virtueel netwerk**, selecteert u **Spoke-01**.
8. Selecteer **Maken**.

Herhaal om verbinding te maken met de **Spoke-02** het virtuele netwerk: verbindingsnaam - **hub-spoke-02**

## <a name="deploy-the-servers"></a>De servers implementeren

1. Selecteer **Een resource maken** in de Azure-portal.
2. Selecteer in de lijst **Populair** de optie **Windows Server 2016-gegevenscentrum**.
3. Voer deze waarden in voor de virtuele machine:

   |Instelling  |Waarde  |
   |---------|---------|
   |Resourcegroep     |**FW-Manager-RG**|
   |Naam van de virtuele machine     |**Srv-workload-01**|
   |Regio     |**(VS) VS - oost**|
   |Beheerdersgebruikersnaam     |typ een gebruikersnaam|
   |Wachtwoord     |typ een wachtwoord|

4. Selecteer onder **Regels voor binnenkomende poort**, voor **Openbare binnenkomende poorten** de optie **Geen**.
6. Accepteer de overige standaardwaarden en selecteer **Volgende: Schijven**.
7. Accepteer de standaardwaarden voor schijven en selecteer **Volgende: Netwerken**.
8. Selecteer **Spoke-01** voor het virtuele netwerk en selecteer **Workload-01-SN** voor het subnet.
9. Selecteer **Geen** voor **Openbaar IP**.
11. Accepteer de overige standaardwaarden en selecteer **Volgende: Beheer**.
12. Selecteer **uitschakelen** om diagnostische gegevens over opstarten uit te scha kelen. Accepteer de overige standaardwaarden en selecteer **Beoordelen en maken**.
13. Controleer de instellingen op de overzichtspagina en selecteer **Maken**.

Gebruik de informatie in de volgende tabel om een andere virtuele machine, **Srv-Workload-02**, te configureren. De rest van de configuratie is hetzelfde als voor de virtuele machine **Srv-workload-01**.

|Instelling  |Waarde  |
|---------|---------|
|Virtueel netwerk|**Spoke-02**|
|Subnet|**Workload-02-SN**|

Nadat de servers zijn geïmplementeerd, selecteert u een serverresource en in **Netwerken** noteert u het privé-IP-adres voor elke server.

## <a name="create-a-firewall-policy-and-secure-your-hub"></a>Een firewallbeleid maken en uw hub beveiligen

Met een firewallbeleid worden verzamelingen regels gedefinieerd om verkeer om te leiden naar een of meer beveiligde virtuele hubs. U maakt uw firewallbeleid en vervolgens beveiligt u uw hub.

1. Bij Firewall Manager selecteert u **Azure Firewall-beleidsregels weergeven**.
2. Selecteer **Azure Firewall-beleid maken**.
1. Selecteer voor **resource groep** **FW-Manager-RG**.
1. Onder **Beleidsdetails** typt u voor **naam** **beleid-01** en voor **Regio** selecteert u **VS - oost**.
1. Selecteer **volgende: DNS-instellingen**.
1. Selecteer **volgende: TLS-inspectie (preview-versie)**.
1. Selecteer **volgende: regels**.
1. Op het tabblad **Regels** selecteert u **Een regelverzameling toevoegen**.
1. Op de pagina **Een regelverzameling toevoegen** typt u **App-RC-01** voor de **Naam**.
1. Als **Type regelverzameling** selecteert u **Toepassing**.
1. Bij **Prioriteit** typt u **100**.
1. Controleer of **Type regelverzameling** is ingesteld op **Toestaan**.
1. Als de **Naam** van de regel typt u **Allow-msft**.
1. Selecteer **IP-adres** als het **Brontype**.
1. Typ bij **Bron** **\*** .
1. Als **Protocol** typt u **http,https**.
1. Controleer of **Doeltype** is ingesteld op **FQDN**.
1. Als **Doel** typt u **\*.microsoft.com**.
1. Selecteer **Toevoegen**.

Voeg een DNAT-regel toe zodat u een extern bureaublad kunt verbinden met de virtuele machine **Srv-Workload-01**.

1. Selecteer **toevoegen/regel verzameling**.
1. Typ **DNAT-RDP** bij **naam**.
1. Bij **Type regelverzameling** selecteert u **DNAT**.
1. Bij **Prioriteit** typt u **100**.
1. Als de **Naam** van de regel typt u **Allow-rdp**.
1. Selecteer **IP-adres** als het **Brontype**.
1. Typ bij **Bron** **\*** .
1. Bij **Protocol** selecteert u **TCP**.
1. Typ bij **Doelpoorten** **3389**.
1. Bij **Doeltype** selecteert u **IP-adres**.
1. Bij **Doeladressen** typt u het openbare IP-adres van de firewall dat u eerder hebt bewaard.
1. Bij **Omgezet adres** typt u het privé-IP-adres voor de **Srv-Workload-01** dat u eerder hebt bewaard.
1. Bij **Vertaalde poort** typt u **3389**.
1. Selecteer **Toevoegen**.

Voeg een netwerkregel toe zodat u een extern bureaublad kunt koppelen van **Srv-workload-01** tot **SRV-workload-02**.

1. Selecteer **Een regelverzameling toevoegen**.
2. Bij **Naam** typt u **vnet-rdp**.
3. Bij **Type regelverzameling** selecteert u **Netwerk**.
4. Bij **Prioriteit** typt u **100**.
1. Selecteer **toestaan** voor **regel verzamelings actie**.
1. Als de **Naam** van de regel typt u **Allow-vnet**.
1. Selecteer **IP-adres** als het **Brontype**.
1. Typ bij **Bron** **\*** .
1. Bij **Protocol** selecteert u **TCP**.
1. Typ bij **Doelpoorten** **3389**.
1. Bij **Doeltype** selecteert u **IP-adres**.
1. Bij **Doeladressen** typt u het privé-IP-adres van **Srv-workload-02** dat u eerder hebt bewaard.
1. Selecteer **Toevoegen**.
1. Selecteer **Controleren en maken**.
1. Selecteer **Maken**.

## <a name="associate-policy"></a>Beleid koppelen

Koppel het firewall beleid aan de hub.

1. Selecteer **Azure firewall beleid** in Firewall beheer.
1. Schakel het selectie vakje voor het **beleid-01** in.
1. Selecteer **koppelingen beheren/hubs koppelen**.
1. Selecteer **hub-01**.
1. Selecteer **Toevoegen**.

## <a name="route-traffic-to-your-hub"></a>Verkeer doorsturen naar uw hub

Nu moet u ervoor zorgen dat netwerkverkeer wordt omgeleid door uw firewall.

1. Selecteer **virtuele hubs** in Firewall-beheer.
2. Selecteer **Hub-01**.
3. Onder **Instellingen** selecteert u **Beveiligingsconfiguratie**.
4. Onder **Internetverkeer** selecteert u **Azure Firewall**.
5. Onder **Privéverkeer** selecteert u **Verzenden via Azure Firewall**.
1. Selecteer **Opslaan**.

   Het duurt enkele minuten om de route tabellen bij te werken.
1. Controleer of de twee verbindingen weer geven Azure Firewall zowel het internet als het privé verkeer beveiligt.

## <a name="test-the-firewall"></a>De firewall testen

Als u de firewall regels wilt testen, maakt u een extern bureau blad met behulp van het open bare IP-adres van de firewall. dit wordt genateeerd tot **SRV-workload-01**. Van daaruit gebruikt u een browser om de toepassingsregel te testen en een extern bureau blad te verbinden met **SRV-Workload-02** om de netwerkregel te testen.

### <a name="test-the-application-rule"></a>De toepassingsregel testen

Test nu de firewallregels om te controleren of deze werkt zoals verwacht.

1. Verbind een extern-bureaubladsessie met het openbare IP-adres van de firewall en meld u aan.

3. Open Internet Explorer en blader naar `https://www.microsoft.com`.
4. Selecteer **OK** > **Sluiten** in de beveiligingswaarschuwingen van Internet Explorer.

   Als het goed is, ziet u nu de startpagina van Microsoft.

5. Blader naar `https://www.google.com`.

   U zou nu door de firewall moeten worden geblokkeerd.

Nu u hebt geverifieerd dat de regel voor de firewalltoepassing werken:

* Kunt u bladeren naar de enige toegestane FQDN, maar niet naar andere.

### <a name="test-the-network-rule"></a>De netwerkregel testen

Test nu de netwerkregel.

- Open vanuit SRV-workload-01 een extern bureau blad naar de SRV-workload-02 privé IP-adres.

   Een extern bureaublad moet verbinding maken met SRV-Workload-02.

Nu u hebt geverifieerd dat de regel voor het firewallnetwerk werken:
* U kunt een extern bureaublad verbinden met een server die zich in een ander virtueel netwerk bevindt.

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u klaar bent met het testen van uw firewall bronnen, verwijdert u de resource groep **FW-Manager-RG** om alle resources met betrekking tot de firewall te verwijderen.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Meer informatie over vertrouwde beveiligingspartners](trusted-security-partners.md)
