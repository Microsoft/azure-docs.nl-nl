---
title: 'Een virtueel netwerk maken: quickstart - Azure-portal'
titleSuffix: Azure Virtual Network
description: 'Quickstart: Maak een virtueel netwerk in de Azure-portal. Dankzij deze netwerken kunnen Azure-resources, zoals VM’s, veilig met elkaar en met internet communiceren.'
author: KumudD
tags: azure-resource-manager
ms.service: virtual-network
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/05/2020
ms.author: kumud
ms.openlocfilehash: cc3ba3a0519400368e0cbfec7abe2d9bd1731b34
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/14/2021
ms.locfileid: "98217631"
---
# <a name="quickstart-create-a-virtual-network-using-the-azure-portal"></a>Quickstart: Een virtueel netwerk maken met Azure Portal

In deze quickstart leert u hoe u een virtueel netwerk maakt met behulp van de Azure-portal. U implementeert twee virtuele machines (VM’s). Vervolgens communiceert u veilig tussen VM’s en maakt u verbinding met VM’s vanaf internet. Een virtueel netwerk is de basisbouwsteen voor uw privénetwerk in Azure. Dankzij deze netwerken kunnen Azure-resources, zoals VM’s, veilig met elkaar en met internet communiceren.

## <a name="prerequisites"></a>Vereisten

* Een Azure-account met een actief abonnement. [Maak er gratis een](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u aan bij de [Azure-portal](https://portal.azure.com).

## <a name="create-a-virtual-network"></a>Een virtueel netwerk maken

1. Selecteer **Een resource maken** in het menu van de Azure-portal. Selecteer **Netwerk** > **Virtueel netwerk** in Azure Marketplace.

1. Typ of selecteer in **Virtueel netwerk maken** de volgende gegevens:

    | Instelling | Waarde |
    | ------- | ----- |
    | Abonnement | Selecteer uw abonnement.|
    | Resourcegroep | Selecteer **Nieuwe maken**, voer *myResourceGroup* in en selecteer vervolgens **OK**. |
    | Naam | Voer *myVirtualNetwork* in. |
    | Locatie | Selecteer **VS - oost**.|

1. Selecteer **Volgende: IP-adressen**, en bij **IPv4-adresruimte** voert u *10.1.0.0/16* in.

1. Selecteer **Subnet toevoegen** en typ vervolgens *myVirtualSubnet* bij **Subnetnaam** en *10.1.0.0/24* bij **Subnetadresbereik**.

1. Selecteer **Toevoegen** en selecteer vervolgens **Controleren en maken**. Laat voor de rest de standaardwaarden staan en selecteer **Maken**.

1. Selecteer in **Virtueel netwerk maken** de optie **Maken**.

## <a name="create-virtual-machines"></a>Virtuele machines maken

Twee virtuele machines in het virtuele netwerk maken:

### <a name="create-the-first-vm"></a>De eerste VM maken

1. Selecteer **Een resource maken** in het menu van de Azure-portal.

1. Selecteer **Compute** > **Windows Server 2019 Datacenter** in Azure Marketplace. Selecteer **Maken**.

1. Typ of selecteer in **Een virtuele machine maken - Basisprincipes** de volgende gegevens:

    | Instelling | Waarde |
    | ------- | ----- |
    | **Projectgegevens** | |
    | Abonnement | Selecteer uw abonnement. |
    | Resourcegroep | Selecteer **myResourceGroup**. U hebt deze resourcegroep in de vorige sectie gemaakt. |
    | **Exemplaardetails** |  |
    | Naam van de virtuele machine | Voer *myVm1* in. |
    | Regio | Selecteer **VS - oost**. |
    | Beschikbaarheidsopties | Laat de standaardwaarde **Geen infrastructuurredundantie vereist** staan. |
    | Installatiekopie | Laat de standaardwaarde **Windows Server 2019 Datacenter** staan. |
    | Grootte | Laat de standaardwaarde **Standard DS1 v2** staan. |
    | **Beheerdersaccount** |  |
    | Gebruikersnaam | Voer een gebruikersnaam naar keuze in. |
    | Wachtwoord | Voer een wachtwoord naar keuze in. Het wachtwoord moet minstens 12 tekens lang zijn en moet voldoen aan de [gedefinieerde complexiteitsvereisten](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    | Wachtwoord bevestigen | Voer het wachtwoord opnieuw in. |
    | **Regels voor binnenkomende poort** |  |
    | Openbare poorten voor inkomend verkeer | Selecteer **Geselecteerde poorten toestaan**. |
    | Binnenkomende poorten selecteren | Voer *HTTP (80)* en *RDP (3389)* in. |
    | **Geld besparen** |  |
    | Hebt u al een Windows-licentie? | Laat de standaardwaarde **Nee** staan. |

1. Selecteer **Volgende: Schijven**.

1. Laat de standaardwaarden in **Een virtuele machine maken – schijven** staan en selecteer **Volgende: Netwerken**.

1. Selecteer in **Een virtuele machine maken - Netwerken** de volgende gegevens:

    | Instelling | Waarde |
    | ------- | ----- |
    | Virtueel netwerk | Laat de standaardwaarde **myVirtualNetwork** staan. |
    | Subnet | Laat de standaardwaarde **myVirtualSubnet (10.1.0.0/24)** staan. |
    | Openbare IP | Laat de standaardwaarde **(nieuw) myVm-ip** staan. |
    | NIC-netwerkbeveiligingsgroep | Laat de standaardwaarde **Basis** staan. |
    | Openbare poorten voor inkomend verkeer | Laat de standaardwaarde **Geselecteerde poorten toestaan** staan. |
    | Binnenkomende poorten selecteren | Laat de standaardwaarden **HTTP** en **RDP** staan.

1. Selecteer **Volgende: Beheer**.

1. Selecteer in **Een virtuele machine maken - Beheer** voor **Opslagaccount voor diagnose** de optie **Nieuw**.

1. Typ of selecteer in **Opslagaccount maken** de volgende gegevens:

    | Instelling | Waarde |
    | ------- | ----- |
    | Naam | Voer *myvmstorageaccount* in. Als deze naam al wordt gebruikt, maakt u een unieke naam.|
    | Soort account | Laat de standaardwaarde **Opslag (algemeen gebruik v1)** staan. |
    | Prestaties | Laat de standaardwaarde **Standaard** staan. |
    | Replicatie | Laat de standaardwaarde **Lokaal redundante opslag (LRS)** staan. |

1. Selecteer **OK** en selecteer vervolgens **Controleren en maken**. De pagina **Beoordelen en maken** wordt weergegeven, waar uw configuratie wordt gevalideerd in Azure.

1. Als u het bericht **Validatie geslaagd** ziet, selecteert u **Maken**.

### <a name="create-the-second-vm"></a>De tweede VM maken

Herhaal de procedure in de vorige sectie om nog een virtuele machine te maken.

> [!IMPORTANT]
> Bij **Naam van de virtuele machine** voert u *myVm2* in.
>
> Bij **Opslagaccount voor diagnose** selecteert u **myvmstorageaccount** in plaats van er een te maken.

## <a name="connect-to-a-vm-from-the-internet"></a>Verbinding maken met een virtuele machine via internet

Nadat u *myVm1* hebt gemaakt, maakt u verbinding met internet.

1. Zoek en selecteer *myVm1* in de Azure-portal.

1. Selecteer **Verbinding maken** en selecteer vervolgens **RDP**.

    ![Verbinding maken met een virtuele machine](./media/quick-create-portal/connect-to-virtual-machine.png)

    De pagina **Verbinding maken** wordt geopend.

1. Selecteer **RDP-bestand downloaden**. In Azure wordt een *RDP*-bestand (Remote Desktop Protocol) gemaakt en het bestand wordt gedownload naar de computer.

1. Open het RDP-bestand. Selecteer **Verbinding maken** wanneer hierom wordt gevraagd.

1. Voer de gebruikersnaam en het wachtwoord in die u hebt opgegeven bij het maken van de virtuele machine.

    > [!NOTE]
    > Mogelijk moet u **Meer opties** > **Een ander account gebruiken** selecteren om de referenties op te geven die u hebt ingevoerd tijdens het maken van de VM.

1. Selecteer **OK**.

1. Er kan een certificaatwaarschuwing worden weergegeven wanneer u zich aanmeldt. Als er een certificaatwaarschuwing wordt weergegeven, selecteert u **Ja** of **Doorgaan**.

1. Wanneer het VM-bureaublad wordt weergegeven, minimaliseert u het om terug te gaan naar het lokale bureaublad.

## <a name="communicate-between-vms"></a>Communiceren tussen VM's

1. Open PowerShell in het externe bureaublad van *myVm1*.

1. Voer `ping myVm2` in.

    U ontvangt een bericht dat er ongeveer als volgt uitziet:

    ```output
    Pinging myVm2.0v0zze1s0uiedpvtxz5z0r0cxg.bx.internal.clouda
    Request timed out.
    Request timed out.
    Request timed out.
    Request timed out.

    Ping statistics for 10.1.0.5:
    Packets: Sent = 4, Received = 0, Lost = 4 (100% loss),
    ```

    De `ping` mislukt omdat `ping` gebruikmaakt van het Internet Control Message Protocol (ICMP). ICMP wordt standaard niet toegestaan via de Windows-firewall.

1. Voer de volgende opdracht in om *myVm2* toe te staan *myVm1* in een latere stap te pingen:

    ```powershell
    New-NetFirewallRule –DisplayName "Allow ICMPv4-In" –Protocol ICMPv4
    ```

    Met deze opdracht wordt binnenkomend verkeer van ICMP via de Windows-firewall toegestaan:

1. Sluit de externe bureaubladverbinding met *myVm1*.

1. Voer de stappen in [Verbinding maken met een virtuele machine via internet](#connect-to-a-vm-from-the-internet) opnieuw uit, maar maak nu verbinding met *myVm2*.

1. Voer `ping myvm1` in vanaf een opdrachtprompt.

    Er wordt een dergelijk bericht weergegeven:

    ```output
    Pinging myVm1.0v0zze1s0uiedpvtxz5z0r0cxg.bx.internal.cloudapp.net [10.1.0.4] with 32 bytes of data:
    Reply from 10.1.0.4: bytes=32 time=1ms TTL=128
    Reply from 10.1.0.4: bytes=32 time<1ms TTL=128
    Reply from 10.1.0.4: bytes=32 time<1ms TTL=128
    Reply from 10.1.0.4: bytes=32 time<1ms TTL=128

    Ping statistics for 10.1.0.4:
        Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
    Approximate round trip times in milli-seconds:
        Minimum = 0ms, Maximum = 1ms, Average = 0ms
    ```

    U ontvangt antwoorden van *myVm1* omdat u ICMP hebt toegestaan via de Windows-firewall op de VM *myVm1* in stap 3.

1. Sluit de externe bureaubladverbinding met *myVm2*.

## <a name="clean-up-resources"></a>Resources opschonen

In deze snelstart hebt u een standaard virtueel netwerk en twee virtuele machines gemaakt. U hebt met één VM verbinding gemaakt via internet en er is er veilige communicatie tussen de twee VM’s geweest.

Wanneer u klaar bent met het gebruiken van het virtuele netwerk en de VM's, verwijdert u de resourcegroep en alle resources die deze bevat:

1. Zoek en selecteer *myResourceGroup*.

1. Selecteer **Resourcegroep verwijderen**.

1. Voer *myResourceGroup* in voor **TYP DE RESOURCEGROEPNAAM** en selecteer **Verwijderen**.

## <a name="next-steps"></a>Volgende stappen

Zie [Een virtueel netwerk maken, wijzigen of verwijderen](manage-virtual-network.md) voor meer informatie over instellingen voor virtuele netwerken.

Azure staat standaard veilige communicatie tussen VM’s toe. Daarentegen zijn alleen inkomende verbindingen met extern bureaublad met Windows-VM's via internet toegestaan. Zie [Netwerkverkeer filteren](tutorial-filter-network-traffic.md) voor meer informatie over typen VM-netwerkcommunicatie.

> [!NOTE] 
> Azure-services kosten geld. Azure Cost Management helpt u om budgetten op te stellen en waarschuwingen te configureren om uw uitgaven onder controle te houden. Analyseer, beheer en optimaliseer uw Azure-kosten met Cost Management. Raadpleeg voor meer informatie de [snelstartgids over de analyse van uw kosten](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).