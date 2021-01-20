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
ms.date: 03/13/2020
ms.author: kumud
ms.openlocfilehash: e047f46e110e1f7b1d544545c80bd1097ae65167
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/14/2021
ms.locfileid: "98221915"
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

## <a name="create-an-nva"></a>Een NVA maken

Virtuele netwerkapparaten (NVA's) zijn virtuele machines die ondersteuning bieden voor netwerkfuncties, zoals routering en firewall-optimalisatie. In deze zelfstudie wordt ervan uitgegaan dat u **Windows Server 2016 Datacenter** gebruikt. Als u wilt, kunt u een ander besturingssysteem selecteren.

1. Selecteer in het menu van de [Azure-portal](https://portal.azure.com) of op de **startpagina** de optie **Een resource maken**.

1. Kies **Beveiliging** > **Windows Server 2016 Datacenter**.

    ![Windows Server 2016 Datacenter, een VM maken, Azure-portal](./media/tutorial-create-route-table-portal/vm-ws2016-datacenter.png)

1. Typ of selecteer op de pagina **Een virtuele machine maken** onder **Basisprincipes** de volgende gegevens:

    | Sectie | Instelling | Bewerking |
    | ------- | ------- | ----- |
    | **Projectgegevens** | Abonnement | Kies uw abonnement. |
    | | Resourcegroep | Selecteer **Nieuwe maken**, voer *myResourceGroup* in en selecteer **OK**. |
    | **Exemplaardetails** | Naam van de virtuele machine | Voer *myVmNva* in. |
    | | Regio | Kies **(VS) US - oost**. |
    | | Beschikbaarheidsopties | Selecteer **Geen infrastructuurredundantie vereist**. |
    | | Installatiekopie | Selecteer **Windows Server 2016 Datacenter**. |
    | | Grootte | Laat de standaardwaarde **Standard DS1 v2** staan. |
    | **Beheerdersaccount** | Gebruikersnaam | Voer een gebruikersnaam naar keuze in. |
    | | Wachtwoord | Voer een Wachtwoord naar keuze in dat minstens 12 tekens lang is en voldoet aan de [gedefinieerde complexiteitsvereisten](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm). |
    | | Wachtwoord bevestigen | Voer het wachtwoord nogmaals in. |
    | **Regels voor binnenkomende poort** | Openbare poorten voor inkomend verkeer | Kies **Geen**. |
    | **Geld besparen** | Hebt u al een Windows Server-licentie? | Kies **Nee**. |

    ![Basisbeginselen, een virtuele machine maken, Azure-portal](./media/tutorial-create-route-table-portal/basics-create-virtual-machine.png)

    Selecteer vervolgens **Volgende: Schijven >** .

1. Selecteer onder **Schijven** de instellingen die geschikt zijn voor uw behoeften en selecteer vervolgens **Volgende: Netwerken >** .

1. Onder **Netwerk**:

    1. Selecteer **Virtueel netwerk** voor **Nieuwe maken**.
    
    1. Voer in het dialoogvenster **Virtueel netwerk maken** onder **Naam** *myVirtualNetwork* in.

    1. Vervang in **Adresruimte** het bestaande adresbereik door *10.0.0.0/16*.

    1. Selecteer in **Subnetten** het pictogram **Verwijderen** om het bestaande subnet te verwijderen en voer vervolgens de volgende combinatie van **Subnetnaam** en **Adresbereik** in. Wanneer u een geldige naam en een geldig bereik heeft opgegeven, wordt er een nieuwe lege rij weergegeven.

        | Subnetnaam | Adresbereik |
        | ----------- | ------------- |
        | *Openbaar* | *10.0.0.0/24* |
        | *Privé* | *10.0.1.0/24* |
        | *DMZ* | *10.0.2.0/24* |

    1. Selecteer **OK** om het dialoogvenster te sluiten.

    1. Kies in **Subnet** de optie **DMZ (10.0.2.0/24)** .

    1. Kies in **Openbare IP** de optie **Geen**, omdat deze virtuele machine geen verbinding kan maken via internet.

    1. Selecteer **Volgende : Beheer >** .

1. Onder **Beheer**:

    1. Selecteer in **Diagnostische gegevens over het opslagaccount** de optie **Nieuwe maken**.
    
    1. Typ of selecteer in het dialoogvenster **Opslagaccount maken** de volgende gegevens:

        | Instelling | Waarde |
        | ------- | ----- |
        | Naam | *mynvastorageaccount* |
        | Soort account | **Opslag (algemeen gebruik v1)** |
        | Prestaties | **Standard** |
        | Replicatie | **Lokaal redundante opslag (LRS)** |
    
    1. Selecteer **OK** om het dialoogvenster te sluiten.

    1. Selecteer **Controleren + maken**. De pagina **Beoordelen en maken** wordt weergegeven en de configuratie wordt gevalideerd in Azure.

1. Als u het bericht **Validatie geslaagd** ziet, selecteert u **Maken**.

    Het maken van de virtuele machine duurt een paar minuten. Wacht totdat Azure klaar is met het maken van de virtuele machine. Op de pagina **Uw implementatie wordt uitgevoerd** worden de details van de implementatie weergegeven.

1. Als de VM klaar is, selecteert u **Ga naar resource**.

## <a name="create-a-route-table"></a>Een routetabel maken

1. Selecteer in het menu van de [Azure-portal](https://portal.azure.com) of op de **startpagina** de optie **Een resource maken**.

2. Voer in het zoekvak *Routeringstabel* in. Wanneer **Routeringstabel** wordt weergegeven in de zoekresultaten, selecteert u dit.

3. Selecteer op de pagina **Routeringstabel** de optie **Maken**.

4. Typ of selecteer in **Routetabel maken** de volgende gegevens:

    | Instelling | Waarde |
    | ------- | ----- |
    | Naam | *myRouteTablePublic* |
    | Abonnement | Uw abonnement |
    | Resourcegroep | **myResourceGroup** |
    | Locatie | **(VS) VS - oost** |
    | Doorgifte van route van virtuele netwerkgateway | **Ingeschakeld** |

    ![Routeringstabel maken, Azure-portal](./media/tutorial-create-route-table-portal/create-route-table.png)

5. Selecteer **Maken**.

## <a name="create-a-route"></a>Een route maken

1. Ga naar de [Azure-portal](https://portal.azure.com) om uw routeringstabel te beheren. Zoek en selecteer **Routeringstabellen**.

1. Kies de naam van uw routeringstabel (**myRouteTablePublic**).

1. Selecteer **Routes** > **Toevoegen**.

    ![Route toevoegen, routeringstabel, Azure-portal](./media/tutorial-create-route-table-portal/add-route.png)

1. Typ of selecteer in **Route toevoegen** de volgende gegevens:

    | Instelling | Waarde |
    | ------- | ----- |
    | Routenaam | *ToPrivateSubnet* |
    | Adresvoorvoegsel | *10.0.1.0/24* (het adresbereik van het *Privé*-subnet dat eerder is gemaakt) |
    | Volgend hoptype | **Virtueel apparaat** |
    | Adres van de volgende hop | *10.0.2.4* (een adres binnen het adresbereik van het *DMZ*-subnet) |

1. Selecteer **OK**.

## <a name="associate-a-route-table-to-a-subnet"></a>Een routetabel aan een subnet koppelen

1. Ga naar de [Azure-portal](https://portal.azure.com) om uw virtuele netwerk te beheren. Zoek en selecteer **Virtuele netwerken**.

1. Kies de naam van uw virtuele netwerk (**myVirtualNetwork**).

1. Kies in de menubalk van het virtuele netwerk de optie **Subnetten**.

1. Kies in de lijst met subnetten van het virtuele netwerk de optie **Openbaar**.

1. Kies in **Routeringstabel** de routeringstabel die u heeft gemaakt (**myRouteTablePublic**), en selecteer vervolgens **Opslaan** om uw routeringstabel aan het *openbare* subnet te koppelen.

    ![De routeringstabel, de subnetlijst, het virtuele netwerk, Azure-portal](./media/tutorial-create-route-table-portal/associate-route-table.png)

## <a name="turn-on-ip-forwarding"></a>Doorsturen via IP inschakelen

Schakel vervolgens Doorsturen via IP in voor uw nieuwe virtuele NVA-machine *myVmNva*. Wanneer u in Azure netwerkverkeer verzendt naar *myVmNva*, wordt dit verkeer, als het is bestemd voor een ander IP-adres, op basis van Doorsturen via IP naar de juiste locatie verzonden.

1. Ga naar de [Azure-portal](https://portal.azure.com) om uw virtuele machine te beheren. Zoek en selecteer **virtuele machines**.

1. Kies de naam van uw virtuele machine (**myVmNva**).

1. Selecteer in de menubalk van de virtuele NVA-machine **Netwerk**.

1. Selecteer **myvmnva123**. Dit is de netwerkinterface die in Azure is gemaakt voor uw VM. Azure voegt nummers toe om een unieke naam te garanderen.

    ![Netwerken, virtueel netwerkapparaat (NVA), virtuele machine (VM), Azure-portal](./media/tutorial-create-route-table-portal/virtual-machine-networking.png)

1. Selecteer in de menubalk van de netwerkinterface de optie **IP-configuraties**.

1. Stel op de pagina **IP-configuraties** de optie **Doorsturen via IP** in op **Ingeschakeld** en selecteer **Opslaan**.

    ![Doorsturen via IP inschakelen, IP-configuraties, netwerkinterface, virtueel netwerkapparaat (NVA) virtuele machine (VM), Azure-portal](./media/tutorial-create-route-table-portal/enable-ip-forwarding.png)

## <a name="create-public-and-private-virtual-machines"></a>Virtuele machines maken, openbaar en privé

Maak een openbare VM en een privé-VM in het virtuele netwerk. Later gebruikt u deze om te zien dat het *Openbare* subnetverkeer in Azure via de NVA naar het *Privé* subnet wordt gerouteerd.

Als u de openbare VM en de privé-VM wilt maken, volgt u de stappen in [Een NVA maken](#create-an-nva). U hoeft niet te wachten tot de implementatie is voltooid of naar de VM-resource te gaan. U gebruikt voornamelijk dezelfde instellingen, behalve zoals hieronder wordt beschreven.

Voordat u **Maken** selecteert om de openbare of persoonlijke VM te maken, gaat u naar de volgende twee subsecties ([Openbare VM](#public-vm) en [Privé-VM](#private-vm)), waarin de waarden worden weergegeven die anders moeten zijn. U kunt doorgaan met de volgende sectie ([Routeverkeer via een NVA](#route-traffic-through-an-nva)) nadat Azure klaar is met de implementatie van de virtuele machines.

### <a name="public-vm"></a>Openbare VM

| Tabblad | Instelling | Waarde |
| --- | ------- | ----- |
| Basisbeginselen | Resourcegroep | **myResourceGroup** |
| | Naam van de virtuele machine | *myVmPublic* |
| | Openbare poorten voor inkomend verkeer | **Geselecteerde poorten toestaan** |
| | Binnenkomende poorten selecteren | **RDP** |
| Netwerken | Virtueel netwerk | **myVirtualNetwork** |
| | Subnet | **Openbaar (10.0.0.0/24)** |
| | Openbaar IP-adres | De standaardwaarde |
| Beheer | Opslagaccount voor diagnose | **mynvastorageaccount** |

### <a name="private-vm"></a>Privé VM

| Tabblad | Instelling | Waarde |
| --- | ------- | ----- |
| Basisbeginselen | Resourcegroep | **myResourceGroup** |
| | Naam van de virtuele machine | *myVmPrivate* |
| | Openbare poorten voor inkomend verkeer | **Geselecteerde poorten toestaan** |
| | Binnenkomende poorten selecteren | **RDP** |
| Netwerken | Virtueel netwerk | **myVirtualNetwork** |
| | Subnet | **Privé (10.0.1.0/24)** |
| | Openbaar IP-adres | De standaardwaarde |
| Beheer | Opslagaccount voor diagnose | **mynvastorageaccount** |

## <a name="route-traffic-through-an-nva"></a>Verkeer routeren via een NVA

### <a name="sign-in-to-myvmprivate-over-remote-desktop"></a>Aanmelden bij myVmPrivate via het externe bureaublad

1. Ga naar de [Azure-portal](https://portal.azure.com) om de privé VM te beheren. Zoek en selecteer **virtuele machines**.

1. Kies de naam van uw privé-VM (**myVmPrivate**).

1. Selecteer in de menubalk van de virtuele machine **Verbinding maken** om een extern bureaubladverbinding te maken met de privé VM.

1. Selecteer op de pagina **Verbinding maken met RDP** de optie **RDP-bestand downloaden**. In Azure wordt een *RDP*-bestand (Remote Desktop Protocol) gemaakt en het bestand wordt gedownload naar de computer.

1. Open het gedownloade *RDP*-bestand. Selecteer **Verbinding maken** wanneer hierom wordt gevraagd. Selecteer **Meer opties** > **Een ander account gebruiken** en voer vervolgens de gebruikersnaam en het wachtwoord in die u heeft opgegeven bij het maken van de privé-VM.

1. Selecteer **OK**.

1. Als u een certificaatwaarschuwing krijgt tijdens het aanmeldingsproces, selecteert u **Ja** om verbinding te maken met de virtuele machine.

### <a name="enable-icmp-through-the-windows-firewall"></a>ICMP via Windows Firewall inschakelen

In een latere stap gebruikt u het hulpprogramma Route traceren om de routering te testen. Route traceren maakt gebruik van ICMP (Internet Control Message Protocol), wat in Windows Firewall standaard wordt geweigerd. Schakel ICMP via Windows Firewall in.

1. Open PowerShell in het externe bureaublad van *myVmPrivate*.

1. Voer de volgende opdracht in:

    ```powershell
    New-NetFirewallRule –DisplayName "Allow ICMPv4-In" –Protocol ICMPv4
    ```

    In deze zelfstudie gebruikt u Route tracering om de routering te testen. Voor productieomgevingen wordt aanbevolen om ICMP via Windows Firewall niet toe te staan.

### <a name="turn-on-ip-forwarding-within-myvmnva"></a>Doorsturen via IP inschakelen binnen myVmNva

U hebt [Doorsturen via IP ingeschakeld](#turn-on-ip-forwarding) voor de netwerkinterface van de VM met behulp van Azure. Netwerkverkeer moet ook worden doorgestuurd via het besturingssysteem van de VM. Doorsturen via IP inschakelen voor het besturingssysteem van de VM *myVmNva* met behulp van deze opdrachten.

1. Open vanaf een opdrachtprompt op de VM *myVmPrivate* een extern bureaublad naar de VM *myVmNva*:

    ```cmd
    mstsc /v:myvmnva
    ```

1. Voer vanuit PowerShell in de virtuele machine *myVmNva* deze opdracht in om Doorsturen via IP in te schakelen:

    ```powershell
    Set-ItemProperty -Path HKLM:\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters -Name IpEnableRouter -Value 1
    ```

1. Start de VM *myVmNva* opnieuw op: Selecteer in de taakbalk **Start** > **Aan/uit** **Overige (gepland)**  > **Doorgaan** .

    Hierdoor wordt de externe bureaubladsessie ook verbroken.

1. Maak nadat de VM *myVmNva* opnieuw is opgestart, een externe bureaubladsessie naar de VM *myVmPublic*. Houd de verbinding met de VM *myVmPrivate* in stand, open een opdrachtprompt en voer de volgende opdracht uit:

    ```cmd
    mstsc /v:myVmPublic
    ```
1. Open PowerShell in het externe bureaublad van *myVmPublic*.

1. Schakel ICMP via Windows Firewall in door de volgende opdracht in te voeren:

    ```powershell
    New-NetFirewallRule –DisplayName "Allow ICMPv4-In" –Protocol ICMPv4
    ```

## <a name="test-the-routing-of-network-traffic"></a>De routering van netwerkverkeer testen

Eerst gaan we de routering van het netwerkverkeer testen vanaf de VM *myVmPublic* naar de VM *myVmPrivate*.

1. Voer vanuit PowerShell in de VM *myVmPublic* de volgende opdracht uit:

    ```powershell
    tracert myVmPrivate
    ```

    Het antwoord is vergelijkbaar met dit voorbeeld:

    ```powershell
    Tracing route to myVmPrivate.vpgub4nqnocezhjgurw44dnxrc.bx.internal.cloudapp.net [10.0.1.4]
    over a maximum of 30 hops:

    1    <1 ms     *        1 ms  10.0.2.4
    2     1 ms     1 ms     1 ms  10.0.1.4

    Trace complete.
    ```

    U ziet dat de eerste hop naar 10.0.2.4 is, het privé IP-adres van het NVA. De tweede hop is naar het privé- IP-adres van de VM *myVmPrivate*: 10.0.1.4. Eerder hebt u de route toegevoegd aan de routetabel *myRouteTablePublic* en deze gekoppeld aan het *Openbare* subnet. Als gevolg hiervan is het verkeer in Azure verzonden via de NVA en niet rechtstreeks via het *Privé* subnet.

1. Sluit de externe bureaubladsessie met de VM *myVmPublic*. U houdt nog verbinding met de VM *myVmPrivate*.

1. Voer vanuit een opdrachtprompt op de VM *myVmPrivate* de volgende opdracht uit:

    ```cmd
    tracert myVmPublic
    ```

    Met deze opdracht wordt de routering van het netwerkverkeer getest vanaf de VM *myVmPrivate* naar de VM *myVmPublic*. Het antwoord is vergelijkbaar met dit voorbeeld:

    ```cmd
    Tracing route to myVmPublic.vpgub4nqnocezhjgurw44dnxrc.bx.internal.cloudapp.net [10.0.0.4]
    over a maximum of 30 hops:

    1     1 ms     1 ms     1 ms  10.0.0.4

    Trace complete.
    ```

    U ziet dat verkeer in Azure rechtstreeks vanuit *myVmPrivate* naar *myVmPublic* wordt geleid. Standaard routeert Azure verkeer rechtstreeks tussen subnetten.

1. Sluit de externe bureaubladsessie naar de VM *myVmPrivate*.

## <a name="clean-up-resources"></a>Resources opschonen

Als de resourcegroep niet meer nodig is, verwijdert u *myResourceGroup* en alle resources die het bevat:

1. Ga naar de [Azure-portal](https://portal.azure.com) om uw resourcegroep te beheren. Zoek en selecteer **Resourcegroepen**.

1. Kies de naam van uw Resourcegroep (**myResourceGroup**).

1. Selecteer **Resourcegroep verwijderen**.

1. Voer in het bevestigingsvenster *myResourceGroup* in bij **TYP DE NAAM VAN DE RESOURCEGROEP** en selecteer vervolgens **Verwijderen**. Azure verwijdert de *myResourceGroup* en alle resources die aan de resourcegroep zijn gekoppeld, inclusief uw routeringstabellen, opslagaccounts, virtuele netwerken, VM's, netwerkinterfaces en openbare IP-adressen.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u een routetabel gemaakt en die aan een subnet gekoppeld. U hebt een eenvoudig NVA gemaakt dat verkeer van een openbaar subnet naar een privé-subnet heeft geleid. U kunt nu verschillende vooraf geconfigureerde NVA's implementeren via de [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking), die een groot aantal nuttige netwerkfuncties biedt. Zie [Routeringoverzicht](virtual-networks-udr-overview.md) en [Routetabel beheren](manage-route-table.md) voor meer informatie over routeren.

Hoewel u veel Azure-resources binnen een virtueel netwerk kunt implementeren, kunnen voor sommige Azure PaaS-services met Azure geen resources in een virtueel netwerk worden geïmplementeerd. Het is mogelijk de toegang tot de resources van sommige Azure PaaS-services te beperken, maar de beperking mag alleen voor het verkeer van een subnet van een virtueel netwerk zijn. Zie de volgende zelfstudie als u wilt leren hoe u de netwerktoegang kunt beperken tot Azure PaaS-resources.

> [!div class="nextstepaction"]
> [Netwerktoegang tot PaaS-resources beperken](tutorial-restrict-network-access-to-resources.md)

> [!NOTE] 
> Azure-services kosten geld. Azure Cost Management helpt u om budgetten op te stellen en waarschuwingen te configureren om uw uitgaven onder controle te houden. Analyseer, beheer en optimaliseer uw Azure-kosten met Cost Management. Raadpleeg voor meer informatie de [snelstartgids over de analyse van uw kosten](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).