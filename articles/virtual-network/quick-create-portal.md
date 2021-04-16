---
title: 'Quickstart: Een virtueel netwerk maken - Azure Portal'
titleSuffix: Azure Virtual Network
description: In deze quickstart leert u hoe u een virtueel netwerk maakt met behulp van de Azure Portal.
author: KumudD
ms.author: kumud
ms.date: 03/17/2021
ms.topic: quickstart
ms.service: virtual-network
ms.workload: infrastructure
ms.tgt_pltfrm: virtual-network
ms.devlang: na
tags:
- azure-resource-manager
ms.custom:
- mode-portal
ms.openlocfilehash: 43c45b43084656a45d2509ee2c7a4376cdc7c052
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107531172"
---
# <a name="quickstart-create-a-virtual-network-using-the-azure-portal"></a>Quickstart: Een virtueel netwerk maken met Azure Portal

In deze quickstart leert u hoe u een virtueel netwerk maakt met behulp van de Azure-portal. U implementeert twee virtuele machines (VM’s). Vervolgens communiceert u veilig tussen VM’s en maakt u verbinding met VM’s vanaf internet. Een virtueel netwerk is de basisbouwsteen voor uw privénetwerk in Azure. Dankzij deze netwerken kunnen Azure-resources, zoals VM’s, veilig met elkaar en met internet communiceren.

## <a name="prerequisites"></a>Vereisten

* Een Azure-account met een actief abonnement. [Maak er gratis een](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u aan bij de [Azure-portal](https://portal.azure.com).

## <a name="create-a-virtual-network"></a>Een virtueel netwerk maken

1. Selecteer **Een resource maken** in de linkerbovenhoek van de portal.

2. Voer in het zoekvak **Virtual Network**. Selecteer **Virtual Network** in de zoekresultaten.

3. Selecteer op **Virtual Network** pagina **Maken.**

4. Typ of selecteer in **Virtueel netwerk maken** de volgende gegevens op het tabblad **Basisinstellingen**:

    | Instelling | Waarde |
    | ------- | ----- |
    | **Projectgegevens** |   |
    | Abonnement | Selecteer uw abonnement. |
    | Resourcegroep | Selecteer **Nieuw maken**.  </br> Voer **myResourceGroup in.** </br> Selecteer **OK**. |
    | **Exemplaardetails** |   |
    | Naam | Voer **myVNet in.** |
    | Region | Selecteer **(VS) VS - oost.** |

    :::image type="content" source="./media/quick-create-portal/create-virtual-network.png" alt-text="Virtuele netwerkverbinding Azure Portal" border="true":::

5. Selecteer het **tabblad IP-adressen** of selecteer de knop **Volgende: IP-adressen** onderaan de pagina.

6. Selecteer **in IPv4-adresruimte** de bestaande adresruimte en wijzig deze in **10.1.0.0/16.**

7. Selecteer **+ Subnet toevoegen** en voer vervolgens **MySubnet** in als **Subnetnaam** en **10.1.0.0/24** bij **Subnetadresbereik.**

8. Selecteer **Toevoegen**.

9. Selecteer het **tabblad** Beveiliging of selecteer **de knop Volgende:** Beveiliging onderaan de pagina.

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
    | Netwerk van openbare binnenkomende poorten | Selecteer **Geen**. |
   
5. Selecteer het **tabblad Beoordelen en** maken of selecteer de blauwe knop Beoordelen en **maken** onderaan de pagina.
  
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
    | Naam van de virtuele machine | Voer **myVM2 in** |
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
    | Netwerk van openbare binnenkomende poorten | Selecteer **Geen**. |
   
5. Selecteer het **tabblad Beoordelen en** maken of selecteer de blauwe knop Beoordelen en **maken** onderaan de pagina.
  
6. Controleer de instellingen en selecteer vervolgens **Maken**.

[!INCLUDE [ephemeral-ip-note.md](../../includes/ephemeral-ip-note.md)]

## <a name="connect-to-myvm1"></a>Verbinding maken met myVM1

1. Ga naar de [Azure-portal](https://portal.azure.com) om de privé VM te beheren. Zoek en selecteer **virtuele machines**.

2. Kies de naam van uw persoonlijke virtuele machine **myVM1.**

3. Selecteer in de menubalk van de VM **verbinding maken** en selecteer **vervolgens Bastion**.

    :::image type="content" source="./media/quick-create-portal/connect-to-virtual-machine.png" alt-text="Verbinding maken met myVM1 met Azure Bastion" border="true":::

4. Selecteer op **de** pagina Verbinding maken de blauwe **knop Bastion** gebruiken.

5. Voer op **de pagina Bastion** de gebruikersnaam en het wachtwoord in die u eerder voor de virtuele machine hebt gemaakt.

6. Selecteer **Verbinding maken**.

## <a name="communicate-between-vms"></a>Communiceren tussen VM's

1. Open PowerShell in de bastionverbinding **van myVM1.**

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

3. Sluit de bastionverbinding met **myVM1.**

4. Voltooi de stappen in [Verbinding maken met myVM1,](#connect-to-myvm1)maar maak verbinding **met myVM2.**

5. Open PowerShell op **myVM2** en voer `ping myvm1` in.

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

7. Sluit de bastionverbinding met **myVM2.**

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
