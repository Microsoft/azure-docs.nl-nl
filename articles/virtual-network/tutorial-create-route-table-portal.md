---
title: Netwerkverkeer routeren - zelfstudie - Azure-portal
titlesuffix: Azure Virtual Network
description: In deze zelfstudie leert u netwerkverkeer te routeren met een routetabel met behulp van Azure Portal.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
Customer intent: I want to route traffic from one subnet, to a different subnet, through a network virtual appliance.
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: tutorial
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/16/2021
ms.author: kumud
ms.openlocfilehash: f8090ea9c0d307d1bd290c4cf4dac9bfaabf7c4b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104576318"
---
# <a name="tutorial-route-network-traffic-with-a-route-table-using-the-azure-portal"></a>Zelfstudie: Netwerkverkeer routeren met een routetabel met behulp van de Azure-portal

In Azure wordt verkeer standaard geretourneerd tussen alle subnetten in een virtueel netwerk. U kunt uw eigen routes maken om de standaardroutering van Azure te overschrijven. Aangepaste routes zijn handig als u bijvoorbeeld verkeer tussen subnetten wilt routeren via een NVA (virtueel netwerkapparaat). In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een NVA maken voor het routeren van verkeer
> * Een routetabel maken
> * Een route maken
> * Een routetabel aan een subnet koppelen
> * Virtuele machines (VM's) implementeren in verschillende subnetten
> * Verkeer van het ene subnet naar het andere leiden via een NVA

In deze zelfstudie wordt de [Azure-portal](https://portal.azure.com) gebruikt om het volgende te doen. U kunt ook [Azure CLI](tutorial-create-route-table-cli.md) of [Azure PowerShell](tutorial-create-route-table-powershell.md) gebruiken.

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten

- Een Azure-abonnement.

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u aan bij Azure Portal op https://portal.azure.com.

## <a name="create-a-virtual-network"></a>Een virtueel netwerk maken

1. Selecteer **Een resource maken** in het menu van de Azure-portal. Selecteer in de Azure Marketplace de optie **netwerken**  >  **virtueel netwerk** of zoek naar **Virtual Network** in het zoekvak.

2. Selecteer **Maken**.

2. Typ of selecteer in **Virtueel netwerk maken** de volgende gegevens:

    | Instelling | Waarde |
    | ------- | ----- |
    | Abonnement | Selecteer uw abonnement.|
    | Resourcegroep | Selecteer **nieuwe maken** en voer **myResourceGroup** in. </br> Selecteer **OK**. |
    | Naam | Voer **myVirtualNetwork** in. |
    | Locatie | Selecteer **VS Oost**.|

3. Selecteer het tabblad **IP-adressen** of selecteer de knop **volgende: IP-adressen** aan de onderkant van de pagina.

4. In **IPv4-adres ruimte** selecteert u de bestaande adres ruimte en wijzigt u deze in **10.0.0.0/16**.

4. Selecteer **+ subnet toevoegen** en voer **openbaar** in voor het **subnet naam** en **10.0.0.0/24** voor het **adres bereik** van het subnet.

5. Selecteer **Toevoegen**.

6. Selecteer **+ subnet toevoegen** en voer **privé** in voor de **subnetnaam** en **10.0.1.0/24** voor het **adres bereik** van het subnet.

7. Selecteer **Toevoegen**.

8. Selecteer **+ subnet toevoegen** en voer vervolgens **DMZ** in als **subnet-naam** en **10.0.2.0/24** voor **adres bereik van subnet**.

9. Selecteer **Toevoegen**.

10. Selecteer het tabblad **beveiliging** of selecteer de knop **volgende: beveiliging** aan de onderkant van de pagina.

11. Selecteer onder **BastionHost** de optie **Inschakelen**. Voer deze gegevens in:

    | Instelling            | Waarde                      |
    |--------------------|----------------------------|
    | Bastion-naam | Voer **myBastionHost** in |
    | AzureBastionSubnet-adresruimte | Voer **10.0.3.0/24** in |
    | Openbaar IP-adres | Selecteer **Nieuw maken**. </br> Voer bij **Naam** de naam **myBastionIP** in. </br> Selecteer **OK**. |

12. Selecteer het tabblad **Controleren + maken** of klik op de knop **Controleren + maken**.

13. Selecteer **Maken**.

## <a name="create-an-nva"></a>Een NVA maken

Virtuele netwerkapparaten (NVA's) zijn virtuele machines die ondersteuning bieden voor netwerkfuncties, zoals routering en firewall-optimalisatie. In deze zelf studie wordt ervan uitgegaan dat u **Windows Server 2019 Data Center** gebruikt. Als u wilt, kunt u een ander besturingssysteem selecteren.

1. Selecteer in de linkerbovenhoek van de portal de optie **Een resource maken** > **Compute** > **Virtuele machine**. 
   
2. In **Een virtuele machine maken** typt of selecteert u de waarden op het tabblad **Basisinformatie**:

    | Instelling | Waarde                                          |
    |-----------------------|----------------------------------|
    | **Projectgegevens** |  |
    | Abonnement | Selecteer uw Azure-abonnement |
    | Resourcegroep | Selecteer **myResourceGroup**. |
    | **Exemplaardetails** |  |
    | Naam van de virtuele machine | **VM myvmnva** invoeren |
    | Regio | Selecteer **(US) VS - oost** |
    | Beschikbaarheidsopties | Selecteer **Geen infrastructuurredundantie vereist** |
    | Installatiekopie | Selecteer **Windows Server 2019 Datacenter** |
    | Azure Spot-exemplaar | Selecteer **Nee** |
    | Grootte | Kies een VM-grootte of kies de standaardinstelling |
    | **Beheerdersaccount** |  |
    | Gebruikersnaam | Voer een gebruikersnaam in |
    | Wachtwoord | Voer een wachtwoord in |
    | Wachtwoord bevestigen | Voer het wachtwoord opnieuw in |
    | **Regels voor binnenkomende poort** |    |
    | Openbare poorten voor inkomend verkeer | Selecteer **Geen**. |
    |

3. Selecteer het tabblad **Netwerken** of selecteer **Volgende: Schijven** en vervolgens **Volgende: Netwerken**.
  
4. Op het tabblad Netwerken selecteert u of voert u het volgende in:

    | Instelling | Waarde |
    |-|-|
    | **Netwerkinterface** |  |
    | Virtueel netwerk | Selecteer **myVirtualNetwork**. |
    | Subnet | **DMZ** selecteren |
    | Openbare IP | Selecteer **Geen** |
    | NIC-netwerkbeveiligingsgroep | Selecteer **Basic**|
    | Netwerk van open bare binnenkomende poorten | Selecteer **Geen**. |
   
5. Selecteer het tabblad **controleren + maken** of selecteer de knop blauwe **revisie + maken** onder aan de pagina.
  
6. Controleer de instellingen en selecteer vervolgens **Maken**.

## <a name="create-a-route-table"></a>Een routetabel maken

1. Selecteer in het menu van de [Azure-portal](https://portal.azure.com) of op de **startpagina** de optie **Een resource maken**.

2. Voer in het zoekvak **Routeringstabel** in. Wanneer **Routeringstabel** wordt weergegeven in de zoekresultaten, selecteert u dit.

3. Selecteer op de pagina **Routeringstabel** de optie **Maken**.

4. Voer in de **tabel route maken** op het tabblad **basis beginselen** de volgende informatie in of Selecteer deze:

    | Instelling | Waarde |
    | ------- | ----- |
    | **Projectgegevens** |   |
    | Abonnement | Selecteer uw abonnement.|
    | Resourcegroep | Selecteer **myResourceGroup**. |
    | **Exemplaardetails** |    |
    | Regio | Selecteer **VS - oost**. |
    | Name | Voer **myRouteTablePublic** in. |
    | Gateway routes door geven | Selecteer **Ja**. |

    :::image type="content" source="./media/tutorial-create-route-table-portal/create-route-table.png" alt-text="Maak een route tabel, Azure Portal." border="true":::

5. Selecteer het tabblad **controleren + maken** of selecteer de knop blauwe **revisie + maken** onder aan de pagina.

## <a name="create-a-route"></a>Een route maken

1. Ga naar de [Azure-portal](https://portal.azure.com) om uw routeringstabel te beheren. Zoek en selecteer **Routeringstabellen**.

2. Selecteer de naam van de route tabel **myRouteTablePublic**.

3. Selecteer op de pagina **myRouteTablePublic** in de sectie **instellingen** de optie **routes**.

4. Selecteer op de pagina routes de knop **+ toevoegen** .

5. Typ of selecteer in **Route toevoegen** de volgende gegevens:

    | Instelling | Waarde |
    | ------- | ----- |
    | Routenaam | **ToPrivateSubnet** invoeren |
    | Adresvoorvoegsel | Voer **10.0.1.0/24** in (het adres bereik van het **privé** -subnet dat eerder is gemaakt) |
    | Volgend hoptype | Selecteer **Virtueel apparaat**. |
    | Adres van de volgende hop | Voer **10.0.2.4** (een adres binnen het adres bereik van het subnet **DMZ** ) |

6. Selecteer **OK**.

## <a name="associate-a-route-table-to-a-subnet"></a>Een routetabel aan een subnet koppelen

1. Ga naar de [Azure-portal](https://portal.azure.com) om uw virtuele netwerk te beheren. Zoek en selecteer **Virtuele netwerken**.

2. Selecteer de naam van uw virtuele netwerk **myVirtualNetwork**.

3. Selecteer op de pagina **myVirtualNetwork** in de sectie **instellingen** de optie **subnetten**.

4. Selecteer **openbaar** in de lijst met subnetten van het virtuele netwerk.

5. Kies in **route tabel** de route tabel die u hebt gemaakt **myRouteTablePublic**. 

6. Selecteer **Opslaan** om uw route tabel te koppelen aan het **open bare** subnet.

    :::image type="content" source="./media/tutorial-create-route-table-portal/associate-route-table.png" alt-text="Koppel de route tabel, de subnetlijst, het virtuele netwerk Azure Portal." border="true":::

## <a name="turn-on-ip-forwarding"></a>Doorsturen via IP inschakelen

Schakel vervolgens door sturen via IP in voor uw nieuwe virtuele NVA-machine, **VM myvmnva**. Wanneer Azure netwerk verkeer naar **VM myvmnva** verzendt en het verkeer bestemd is voor een ander IP-adres, verzendt door sturen via IP het verkeer naar de juiste locatie.

1. Ga naar de [Azure-portal](https://portal.azure.com) om uw virtuele machine te beheren. Zoek en selecteer **virtuele machines**.

2. Selecteer de naam van de **VM myvmnva** van de virtuele machine.

3. Selecteer op de pagina overzicht van **VM myvmnva** in **instellingen** de optie **netwerken**.

4. Selecteer op de pagina **netwerk** van **VM myvmnva** de netwerk interface naast **netwerk interface**.  De naam van de interface begint met **VM myvmnva**.

    :::image type="content" source="./media/tutorial-create-route-table-portal/virtual-machine-networking.png" alt-text="Netwerken, virtueel netwerkapparaat (NVA), virtuele machine (VM), Azure-portal" border="true":::

5. Selecteer op de pagina overzicht van netwerk interface in **instellingen** de optie **IP-configuraties**.

6. Stel op de pagina **IP-configuraties** **door sturen via IP** in op **ingeschakeld** en selecteer vervolgens **Opslaan**.

    :::image type="content" source="./media/tutorial-create-route-table-portal/enable-ip-forwarding.png" alt-text="Doorsturen via IP inschakelen" border="true":::

## <a name="create-public-and-private-virtual-machines"></a>Virtuele machines maken, openbaar en privé

Maak een openbare VM en een privé-VM in het virtuele netwerk. Later gebruikt u deze om te zien dat het **Openbare** subnetverkeer in Azure via de NVA naar het **Privé** subnet wordt gerouteerd.


### <a name="public-vm"></a>Openbare VM

1. Selecteer in de linkerbovenhoek van de portal de optie **Een resource maken** > **Compute** > **Virtuele machine**. 
   
2. In **Een virtuele machine maken** typt of selecteert u de waarden op het tabblad **Basisinformatie**:

    | Instelling | Waarde                                          |
    |-----------------------|----------------------------------|
    | **Projectgegevens** |  |
    | Abonnement | Selecteer uw Azure-abonnement |
    | Resourcegroep | Selecteer **myResourceGroup**. |
    | **Exemplaardetails** |  |
    | Naam van de virtuele machine | **VM myvmpublic** invoeren |
    | Regio | Selecteer **(US) VS - oost** |
    | Beschikbaarheidsopties | Selecteer **Geen infrastructuurredundantie vereist** |
    | Installatiekopie | Selecteer **Windows Server 2019 Datacenter** |
    | Azure Spot-exemplaar | Selecteer **Nee** |
    | Grootte | Kies een VM-grootte of kies de standaardinstelling |
    | **Beheerdersaccount** |  |
    | Gebruikersnaam | Voer een gebruikersnaam in |
    | Wachtwoord | Voer een wachtwoord in |
    | Wachtwoord bevestigen | Voer het wachtwoord opnieuw in |
    | **Regels voor binnenkomende poort** |    |
    | Openbare poorten voor inkomend verkeer | Selecteer **Geen**. |
    |

3. Selecteer het tabblad **Netwerken** of selecteer **Volgende: Schijven** en vervolgens **Volgende: Netwerken**.
  
4. Op het tabblad Netwerken selecteert u of voert u het volgende in:

    | Instelling | Waarde |
    |-|-|
    | **Netwerkinterface** |  |
    | Virtueel netwerk | Selecteer **myVirtualNetwork**. |
    | Subnet | **Openbaar** selecteren |
    | Openbare IP | Selecteer **Geen** |
    | NIC-netwerkbeveiligingsgroep | Selecteer **Basic**|
    | Netwerk van open bare binnenkomende poorten | Selecteer **Geen**. |
   
5. Selecteer het tabblad **controleren + maken** of selecteer de knop blauwe **revisie + maken** onder aan de pagina.
  
6. Controleer de instellingen en selecteer vervolgens **Maken**.

### <a name="private-vm"></a>Privé VM

1. Selecteer in de linkerbovenhoek van de portal de optie **Een resource maken** > **Compute** > **Virtuele machine**. 
   
2. In **Een virtuele machine maken** typt of selecteert u de waarden op het tabblad **Basisinformatie**:

    | Instelling | Waarde                                          |
    |-----------------------|----------------------------------|
    | **Projectgegevens** |  |
    | Abonnement | Selecteer uw Azure-abonnement |
    | Resourcegroep | Selecteer **myResourceGroup**. |
    | **Exemplaardetails** |  |
    | Naam van de virtuele machine | **VM myvmprivate** invoeren |
    | Regio | Selecteer **(US) VS - oost** |
    | Beschikbaarheidsopties | Selecteer **Geen infrastructuurredundantie vereist** |
    | Installatiekopie | Selecteer **Windows Server 2019 Datacenter** |
    | Azure Spot-exemplaar | Selecteer **Nee** |
    | Grootte | Kies een VM-grootte of kies de standaardinstelling |
    | **Beheerdersaccount** |  |
    | Gebruikersnaam | Voer een gebruikersnaam in |
    | Wachtwoord | Voer een wachtwoord in |
    | Wachtwoord bevestigen | Voer het wachtwoord opnieuw in |
    | **Regels voor binnenkomende poort** |    |
    | Openbare poorten voor inkomend verkeer | Selecteer **Geen**. |
    |

3. Selecteer het tabblad **Netwerken** of selecteer **Volgende: Schijven** en vervolgens **Volgende: Netwerken**.
  
4. Op het tabblad Netwerken selecteert u of voert u het volgende in:

    | Instelling | Waarde |
    |-|-|
    | **Netwerkinterface** |  |
    | Virtueel netwerk | Selecteer **myVirtualNetwork**. |
    | Subnet | **Privé** selecteren |
    | Openbare IP | Selecteer **Geen** |
    | NIC-netwerkbeveiligingsgroep | Selecteer **Basic**|
    | Netwerk van open bare binnenkomende poorten | Selecteer **Geen**. |
   
5. Selecteer het tabblad **controleren + maken** of selecteer de knop blauwe **revisie + maken** onder aan de pagina.
  
6. Controleer de instellingen en selecteer vervolgens **Maken**.

## <a name="route-traffic-through-an-nva"></a>Verkeer routeren via een NVA

### <a name="sign-in-to-private-vm"></a>Aanmelden bij een particuliere VM

1. Ga naar de [Azure-portal](https://portal.azure.com) om de privé VM te beheren. Zoek en selecteer **virtuele machines**.

2. Kies de naam van de **VM myvmprivate** van uw persoonlijke virtuele machine.

3. Selecteer in de menu balk van de virtuele machine **verbinding maken** en selecteer vervolgens **Bastion**.

4. Selecteer op de pagina **verbinding maken** de knop **Bastion blauw gebruiken** .

5. Voer op de pagina **Bastion** de gebruikers naam en het wacht woord in die u eerder voor de virtuele machine hebt gemaakt.

6. Selecteer **Verbinding maken**.

### <a name="configure-firewall"></a>Firewall configureren

In een latere stap gebruikt u het hulpprogramma Route traceren om de routering te testen. Route traceren maakt gebruik van ICMP (Internet Control Message Protocol), wat in Windows Firewall standaard wordt geweigerd. 

Schakel ICMP via Windows Firewall in.

1. Open Power shell met beheerders bevoegdheden in de Bastion-verbinding van **VM myvmprivate**.

2. Voer de volgende opdracht in:

    ```powershell
    New-NetFirewallRule –DisplayName "Allow ICMPv4-In" –Protocol ICMPv4
    ```

    In deze zelfstudie gebruikt u Route tracering om de routering te testen. Voor productieomgevingen wordt aanbevolen om ICMP via Windows Firewall niet toe te staan.

### <a name="turn-on-ip-forwarding-within-myvmnva"></a>Door sturen via IP inschakelen in VM myvmnva

U hebt [Doorsturen via IP ingeschakeld](#turn-on-ip-forwarding) voor de netwerkinterface van de VM met behulp van Azure. Het besturings systeem van de virtuele machine moet ook netwerk verkeer door sturen. 

Schakel door sturen via IP in voor **VM myvmnva** met deze opdrachten.

1. Open vanuit Power shell op de **VM myvmprivate** -VM een extern bureau blad naar de **VM myvmnva** -VM:

    ```powershell
    mstsc /v:myvmnva
    ```

2. Voer in Power shell op de **VM myvmnva** -VM de volgende opdracht in om door sturen via IP in te scha kelen:

    ```powershell
    Set-ItemProperty -Path HKLM:\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters -Name IpEnableRouter -Value 1
    ```

3. Start **VM myvmnva** opnieuw.

    ```powershell
    Restart-Computer
    ```

4. Nadat **VM myvmnva** opnieuw is opgestart, maakt u een extern-bureaublad sessie naar **VM myvmpublic**. 
    
    Terwijl u nog steeds verbinding hebt gemaakt met **VM myvmprivate**, opent u Power shell en voert u deze opdracht uit:

    ```powershell
    mstsc /v:myvmpublic
    ```
5. Open Power shell in het extern bureau blad van **VM myvmpublic**.

6. Schakel ICMP via Windows Firewall in door de volgende opdracht in te voeren:

    ```powershell
    New-NetFirewallRule –DisplayName "Allow ICMPv4-In" –Protocol ICMPv4
    ```

## <a name="test-the-routing-of-network-traffic"></a>De routering van netwerkverkeer testen

Eerst testen we de route ring van netwerk verkeer van **VM myvmpublic** naar **VM myvmprivate**.

1. Voer in Power shell op **VM myvmpublic** de volgende opdracht in:

    ```powershell
    tracert myvmprivate
    ```

    Het antwoord is vergelijkbaar met dit voorbeeld:

    ```powershell
    Tracing route to myvmprivate.q04q2hv50taerlrtdyjz5nza1f.bx.internal.cloudapp.net [10.0.1.4]
    over a maximum of 30 hops:

      1     1 ms     *        2 ms  myvmnva.internal.cloudapp.net [10.0.2.4]
      2     2 ms     1 ms     1 ms  myvmprivate.internal.cloudapp.net [10.0.1.4]

    Trace complete.
    ```

    * U ziet dat de eerste hop 10.0.2.4 is. Dit is het privé-IP-adres **van VM myvmnva** . 

    * De tweede hop is het privé-IP-adres **VM myvmprivate**: 10.0.1.4. 

    Eerder hebt u de route toegevoegd aan de routetabel **myRouteTablePublic** en deze gekoppeld aan het *Openbare* subnet. Azure heeft het verkeer verzonden via de NVA en niet rechtstreeks naar het *privé* -subnet.

2. Sluit de Extern-bureaublad sessie naar **VM myvmpublic**, waarmee u verbinding met **VM myvmprivate** kunt blijven.

3. Open Power shell op **VM myvmprivate** en voer de volgende opdracht in:

    ```powershell
    tracert myvmpublic
    ```

    Met deze opdracht wordt de routering van het netwerkverkeer getest vanaf de VM *myVmPrivate* naar de VM *myVmPublic*. Het antwoord is vergelijkbaar met dit voorbeeld:

    ```cmd
    Tracing route to myvmpublic.q04q2hv50taerlrtdyjz5nza1f.bx.internal.cloudapp.net [10.0.0.4]
    over a maximum of 30 hops:

      1     1 ms     1 ms     1 ms  myvmpublic.internal.cloudapp.net [10.0.0.4]

    Trace complete.
    ```

    U kunt zien dat Azure verkeer rechtstreeks van *VM myvmprivate* naar *VM myvmpublic* stuurt. Standaard routeert Azure verkeer rechtstreeks tussen subnetten.

4. Sluit de Bastion-sessie naar **VM myvmprivate**.

## <a name="clean-up-resources"></a>Resources opschonen

Als de resource groep niet meer nodig is, verwijdert u **myResourceGroup** en alle resources die deze bevat:

1. Ga naar de [Azure-portal](https://portal.azure.com) om uw resourcegroep te beheren. Zoek en selecteer **Resourcegroepen**.

2. Selecteer de naam van de resource groep **myResourceGroup**.

3. Selecteer **Resourcegroep verwijderen**.

4. Voer in het bevestigingsvenster **myResourceGroup** in bij **TYP DE NAAM VAN DE RESOURCEGROEP** en selecteer vervolgens **Verwijderen**. 


## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u:

* Een route tabel gemaakt en gekoppeld aan een subnet.
* Er is een eenvoudige NVA gemaakt waarmee verkeer van een openbaar subnet naar een privé subnet wordt gerouteerd. 

U kunt verschillende vooraf geconfigureerde Nva's implementeren vanuit [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking), die een groot aantal nuttige netwerk functies biedt. 

Zie [Routeringoverzicht](virtual-networks-udr-overview.md) en [Routetabel beheren](manage-route-table.md) voor meer informatie over routeren.

Zie voor het filteren van netwerk verkeer in een virtueel netwerk:
> [!div class="nextstepaction"]
> [Zelfstudie: Netwerkverkeer filteren met een netwerkbeveiligingsgroep met behulp van Azure Portal](tutorial-filter-network-traffic.md)
