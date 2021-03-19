---
title: 'Snelstartgids: een virtueel netwerk maken-Azure Portal'
titleSuffix: Azure Virtual Network
description: In deze Quick Start leert u hoe u een virtueel netwerk maakt met behulp van de Azure Portal.
author: KumudD
tags: azure-resource-manager
ms.service: virtual-network
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/17/2021
ms.author: kumud
ms.openlocfilehash: 8af5b302e3ec790b6ee9356aca0699d0edcd284e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104606060"
---
# <a name="quickstart-create-a-virtual-network-using-the-azure-portal"></a>Quickstart: Een virtueel netwerk maken met Azure Portal

In deze quickstart leert u hoe u een virtueel netwerk maakt met behulp van de Azure-portal. U implementeert twee virtuele machines (VM’s). Vervolgens communiceert u veilig tussen VM’s en maakt u verbinding met VM’s vanaf internet. Een virtueel netwerk is de basisbouwsteen voor uw privénetwerk in Azure. Dankzij deze netwerken kunnen Azure-resources, zoals VM’s, veilig met elkaar en met internet communiceren.

## <a name="prerequisites"></a>Vereisten

* Een Azure-account met een actief abonnement. [Maak er gratis een](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u aan bij de [Azure-portal](https://portal.azure.com).

## <a name="create-a-virtual-network"></a>Een virtueel netwerk maken

1. Selecteer **een resource maken** in de linkerbovenhoek van de portal.

2. Voer in het zoekvak **Virtual Network** in. Selecteer **Virtual Network** in de zoek resultaten.

3. Op de pagina **Virtual Network** selecteert u **maken**.

4. Typ of selecteer in **Virtueel netwerk maken** de volgende gegevens op het tabblad **Basisinstellingen**:

    | Instelling | Waarde |
    | ------- | ----- |
    | **Projectgegevens** |   |
    | Abonnement | Selecteer uw abonnement. |
    | Resourcegroep | Selecteer **Nieuw maken**.  </br> Voer **myResourceGroup** in. </br> Selecteer **OK**. |
    | **Exemplaardetails** |   |
    | Naam | Voer **myVNet** in. |
    | Region | Selecteer **VS Oost**. |

    :::image type="content" source="./media/quick-create-portal/create-virtual-network.png" alt-text="Virtuele netwerk Azure Portal maken" border="true":::

5. Selecteer het tabblad **IP-adressen** of selecteer de knop **volgende: IP-adressen** aan de onderkant van de pagina.

6. Selecteer in **IPv4-adres ruimte** de bestaande adres ruimte en wijzig deze in **10.1.0.0/16**.

7. Selecteer **+ subnet toevoegen** en voer vervolgens **MySubnet** in als **subnet-naam** en **10.1.0.0/24** voor **adres bereik van subnet**.

8. Selecteer **Toevoegen**.

9. Selecteer het tabblad **beveiliging** of selecteer de knop **volgende: beveiliging** aan de onderkant van de pagina.

10. Selecteer onder **BastionHost** de optie **Inschakelen**. Voer deze gegevens in:

    | Instelling            | Waarde                      |
    |--------------------|----------------------------|
    | Bastion-naam | Voer **myBastionHost** in |
    | AzureBastionSubnet-adresruimte | Voer **10.1.1.0/24** in |
    | Openbaar IP-adres | Selecteer **Nieuw maken**. </br> Voer bij **Naam** de naam **myBastionIP** in. </br> Selecteer **OK**. |

11. Selecteer het tabblad **Controleren + maken** of klik op de knop **Controleren + maken**.

12. Selecteer **Maken**.

## <a name="create-virtual-machines"></a>Virtuele machines maken

Twee virtuele machines in het virtuele netwerk maken:

### <a name="create-the-first-vm"></a>De eerste VM maken

1. Selecteer in de linkerbovenhoek van de portal de optie **Een resource maken** > **Compute** > **Virtuele machine**. 
   
2. In **Een virtuele machine maken** typt of selecteert u de waarden op het tabblad **Basisinformatie**:

    | Instelling | Waarde                                          |
    |-----------------------|----------------------------------|
    | **Projectgegevens** |  |
    | Abonnement | Selecteer uw Azure-abonnement |
    | Resourcegroep | Selecteer **myResourceGroup**. |
    | **Exemplaardetails** |  |
    | Naam van de virtuele machine | Voer **myVM1** in |
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
    | Virtueel netwerk | Selecteer **myVNet**. |
    | Subnet | Selecteer **mySubnet** |
    | Openbare IP | Selecteer **Geen** |
    | NIC-netwerkbeveiligingsgroep | Selecteer **Basic**|
    | Netwerk van open bare binnenkomende poorten | Selecteer **Geen**. |
   
5. Selecteer het tabblad **controleren + maken** of selecteer de knop blauwe **revisie + maken** onder aan de pagina.
  
6. Controleer de instellingen en selecteer vervolgens **Maken**.

### <a name="create-the-second-vm"></a>De tweede VM maken

1. Selecteer in de linkerbovenhoek van de portal de optie **Een resource maken** > **Compute** > **Virtuele machine**. 
   
2. In **Een virtuele machine maken** typt of selecteert u de waarden op het tabblad **Basisinformatie**:

    | Instelling | Waarde                                          |
    |-----------------------|----------------------------------|
    | **Projectgegevens** |  |
    | Abonnement | Selecteer uw Azure-abonnement |
    | Resourcegroep | Selecteer **myResourceGroup**. |
    | **Exemplaardetails** |  |
    | Naam van de virtuele machine | **MyVM2** invoeren |
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
    | Virtueel netwerk | Selecteer **myVNet**. |
    | Subnet | Selecteer **mySubnet** |
    | Openbare IP | Selecteer **Geen** |
    | NIC-netwerkbeveiligingsgroep | Selecteer **Basic**|
    | Netwerk van open bare binnenkomende poorten | Selecteer **Geen**. |
   
5. Selecteer het tabblad **controleren + maken** of selecteer de knop blauwe **revisie + maken** onder aan de pagina.
  
6. Controleer de instellingen en selecteer vervolgens **Maken**.

## <a name="connect-to-myvm1"></a>Verbinding maken met myVM1

1. Ga naar de [Azure-portal](https://portal.azure.com) om de privé VM te beheren. Zoek en selecteer **virtuele machines**.

2. Kies de naam van de **myVM1** van uw persoonlijke virtuele machine.

3. Selecteer in de menu balk van de virtuele machine **verbinding maken** en selecteer vervolgens **Bastion**.

    :::image type="content" source="./media/quick-create-portal/connect-to-virtual-machine.png" alt-text="Verbinding maken met myVM1 met Azure Bastion" border="true":::

4. Selecteer op de pagina **verbinding maken** de knop **Bastion blauw gebruiken** .

5. Voer op de pagina **Bastion** de gebruikers naam en het wacht woord in die u eerder voor de virtuele machine hebt gemaakt.

6. Selecteer **Verbinding maken**.

## <a name="communicate-between-vms"></a>Communiceren tussen VM's

1. Open Power shell in de Bastion-verbinding van **myVM1**.

2. Voer `ping myvm2` in.

    U ontvangt een bericht dat er ongeveer als volgt uitziet:

    ```powershell
    Pinging myvm2.cs4wv3rxdjgedggsfghkjrxuqf.bx.internal.cloudapp.net [10.1.0.5] with 32 bytes of data:
    Reply from 10.1.0.5: bytes=32 time=3ms TTL=128
    Reply from 10.1.0.5: bytes=32 time=1ms TTL=128
    Reply from 10.1.0.5: bytes=32 time=1ms TTL=128
    Reply from 10.1.0.5: bytes=32 time=1ms TTL=128

    Ping statistics for 10.1.0.5:
        Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
    Approximate round trip times in milli-seconds:
        Minimum = 1ms, Maximum = 3ms, Average = 1ms
    ```

3. Sluit de Bastion-verbinding met **myVM1**.

4. Voer de stappen in [verbinding maken met myVM1](#connect-to-myvm1)uit, maar maak verbinding met **myVM2**.

5. Open Power shell op **myVM2**, Voer in `ping myvm1` .

    U ontvangt iets als dit bericht:

    ```powershell
    Pinging myvm1.cs4wv3rxdjgedggsfghkjrxuqf.bx.internal.cloudapp.net [10.1.0.4] with 32 bytes of data:
    Reply from 10.1.0.4: bytes=32 time=1ms TTL=128
    Reply from 10.1.0.4: bytes=32 time=1ms TTL=128
    Reply from 10.1.0.4: bytes=32 time=1ms TTL=128
    Reply from 10.1.0.4: bytes=32 time=1ms TTL=128

    Ping statistics for 10.1.0.4:
        Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
    Approximate round trip times in milli-seconds:
        Minimum = 1ms, Maximum = 1ms, Average = 1ms
    ```

7. Sluit de Bastion-verbinding met **myVM2**.

## <a name="clean-up-resources"></a>Resources opschonen

In deze snelstart hebt u een standaard virtueel netwerk en twee virtuele machines gemaakt. 

U hebt met één VM verbinding gemaakt via internet en er is er veilige communicatie tussen de twee VM’s geweest.

Wanneer u klaar bent met het gebruiken van het virtuele netwerk en de VM's, verwijdert u de resourcegroep en alle resources die deze bevat:

1. Zoek en selecteer **myResourceGroup**.

1. Selecteer **Resourcegroep verwijderen**.

1. Voer **myResourceGroup** in voor **TYP DE RESOURCEGROEPNAAM** en selecteer **Verwijderen**.

## <a name="next-steps"></a>Volgende stappen

Zie [Een virtueel netwerk maken, wijzigen of verwijderen](manage-virtual-network.md) voor meer informatie over instellingen voor virtuele netwerken.

Zie [Netwerkverkeer filteren](tutorial-filter-network-traffic.md) voor meer informatie over typen VM-netwerkcommunicatie.
