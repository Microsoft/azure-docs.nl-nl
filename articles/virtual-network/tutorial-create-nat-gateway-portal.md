---
title: 'Zelfstudie: Een NAT-gateway maken - Azure-portal'
titlesuffix: Azure Virtual Network NAT
description: In deze zelf studie leert u hoe u een NAT-gateway maakt en valideert met behulp van de Azure Portal.
author: asudbring
ms.author: allensu
ms.service: virtual-network
ms.subservice: nat
ms.topic: tutorial
ms.date: 03/02/2021
ms.custom: template-tutorial
ms.openlocfilehash: 82b5892b027627871e5492e3c6cd3776a923632b
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102553439"
---
# <a name="tutorial-create-a-nat-gateway-using-the-azure-portal"></a>Zelfstudie: Een NAT-gateway maken met de Azure-portal

In deze zelfstudie wordt uitgelegd hoe u de Azure Virtual Network NAT-service gebruikt. U maakt een NAT-gateway om uitgaande connectiviteit te bieden voor virtuele machines in Azure. 

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Maak een virtueel netwerk.
> * Hiermee maakt u een virtuele machine.
> * Maak een NAT-gateway en koppel deze aan het virtuele netwerk.
> * Maak verbinding met de virtuele machine en controleer IP-adres van NAT.

## <a name="prerequisites"></a>Vereisten

- Een Azure-account met een actief abonnement. [Gratis een account maken](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)

## <a name="virtual-network"></a>Virtueel netwerk

Voordat u een virtuele machine implementeert en uw NAT-gateway kunt gebruiken, moet u de resourcegroep en het virtuele netwerk maken.

1. Meld u aan bij [Azure Portal](https://portal.azure.com).

2. Selecteer in de linkerbovenhoek van het scherm **Een resource maken > Netwerken > Virtueel netwerk** of zoek naar **Virtueel netwerk** in het zoekvak.

3. Selecteer **Maken**.

4. Typ of selecteer in **Virtueel netwerk maken** de volgende gegevens op het tabblad **Basisinstellingen**:

    | **Instelling**          | **Waarde**                                                           |
    |------------------|-----------------------------------------------------------------|
    | **Projectgegevens**  |                                                                 |
    | Abonnement     | Selecteer uw Azure-abonnement                                  |
    | Resourcegroep   | Selecteer **Nieuw maken**. </br> Voer **myResourceGroupNAT** in. </br> Selecteer **OK**. |
    | **Exemplaardetails** |                                                                 |
    | Naam             | Voer **myVNet** in                                    |
    | Regio           | Selecteren **(Europa) Europa-West** |

5. Selecteer het tabblad **IP-adressen** of klik op de knop **Volgende: IP-adressen** onderaan de pagina.

6. Voer op het tabblad **IP-adressen** deze gegevens in:

    | Instelling            | Waarde                      |
    |--------------------|----------------------------|
    | IPv4-adresruimte | Voer **10.1.0.0/16** in |

7. Selecteer **+ subnet toevoegen**. 

8. Voer in **Subnet bewerken** deze gegevens in:

    | Instelling            | Waarde                      |
    |--------------------|----------------------------|
    | Subnetnaam | Open **mySubnet** |
    | Subnetadresbereik | Voer **10.1.0.0/24** in |

9. Selecteer **Toevoegen**.

10. Selecteer het tabblad **Beveiliging**.

11. Selecteer onder **BastionHost** de optie **Inschakelen**. Voer deze gegevens in:

    | Instelling            | Waarde                      |
    |--------------------|----------------------------|
    | Bastion-naam | Voer **myBastionHost** in |
    | AzureBastionSubnet-adresruimte | Voer **10.1.1.0/24** in |
    | Openbaar IP-adres | Selecteer **Nieuw maken**. </br> Voer bij **Naam** de naam **myBastionIP** in. </br> Selecteer **OK**. |

12. Selecteer het tabblad **Controleren + maken** of klik op de knop **Controleren + maken**.

13. Selecteer **Maken**.

## <a name="nat-gateway"></a>NAT-gateway

U kunt een of meer openbare IP-adresresources, openbare IP-voorvoegsels of beide gebruiken. We voegen een open bare IP-resource en een NAT-gateway bron toe.

1. Selecteer in de linkerbovenhoek van het scherm de optie **een resource maken > netwerk > NAT-gateway** of zoek naar **NAT-gateway** in het zoekvak.

2. Selecteer **Maken**. 

3. In **Create Network Address Translation (NAT)-gateway**, typt of selecteert u deze informatie op het tabblad **basis beginselen** :

    | **Instelling**          | **Waarde**                                                           |
    |------------------|-----------------------------------------------------------------|
    | **Projectgegevens**  |                                                                 |
    | Abonnement     | Selecteer uw Azure-abonnement.                                  |
    | Resourcegroep   | Selecteer **myResourceGroupNAT**. |
    | **Exemplaardetails** |                                                                 |
    | Naam             | **MyNATgateway** invoeren                                    |
    | Region           | Selecteren **(Europa) Europa-West**  |
    | Beschikbaarheidszone | Selecteer **Geen**. |
    | Time-out voor inactiviteit (minuten) | Voer **10** in. |

4. Selecteer het tabblad **uitgaand IP-adres** of selecteer de knop **volgende: uitgaand IP-adres** aan de onderkant van de pagina.

5. Op het tabblad **uitgaand IP-adres** voert u de volgende gegevens in of selecteert u deze:

    | **Instelling** | **Waarde** |
    | ----------- | --------- |
    | Openbare IP-adressen | Selecteer **een nieuw openbaar IP-adres maken**. </br> Voer bij **naam** **myPublicIP** in. </br> Selecteer **OK**. |

6. Selecteer het tabblad **subnet** of selecteer de knop **volgende: subnet** onder aan de pagina.

7. Op het tabblad **subnet** selecteert u **MyVNet** in het **virtuele netwerk** .

8. Schakel het selectie vakje naast **mySubnet** in.

9. Selecteer het tabblad **controleren + maken** of selecteer de knop blauwe **revisie + maken** onder aan de pagina.

10. Selecteer **Maken**.
    
## <a name="virtual-machine"></a>Virtuele machine

In deze sectie maakt u een virtuele machine om de NAT-gateway te testen en controleert u het open bare IP-adres van de uitgaande verbinding.

1. Selecteer in de linkerbovenhoek van de portal de optie **Een resource maken** > **Compute** > **Virtuele machine**. 

2. Voer op de pagina **een virtuele machine maken** op het tabblad **basis beginselen** de volgende informatie in of Selecteer deze:

    | **Instelling** | **Waarde** |
    | ----------- | --------- |
    | **Projectgegevens** |   |
    | Abonnement | Selecteer uw abonnement. |
    | Resourcegroep | Selecteer **myResourceGroupNAT**. |
    | **Exemplaardetails** |   |
    | Naam van de virtuele machine | Voer **myVM** in. |
    | Region | Selecteer **(Europe) Europa-West**. |
    | Beschikbaarheidsopties | Behoud de standaard waarde geen redundantie vereist. |
    | Installatiekopie | Selecteer **Windows Server 2019 Data Center-gen1**. |
    | Grootte | Selecteer **Standard_DS1_v2**. |
    | **Beheerdersaccount** |   |
    | Gebruikersnaam | Voer een gebruikers naam in voor de virtuele machine. |
    | Wachtwoord | Voer een wachtwoord in. |
    | Wachtwoord bevestigen | Bevestig het wacht woord. |
    | **Regels voor binnenkomende poort** |    |
    | Openbare poorten voor inkomend verkeer | Selecteer **Geen**. |

3. Selecteer het tabblad **schijven** of selecteer de knop **volgende: schijven** onder aan de pagina.

4. Wijzig de standaard waarde op het tabblad **schijven** .

5. Selecteer het tabblad **netwerken** of selecteer de knop **volgende: netwerk** aan de onderkant van de pagina.

6. Op het tabblad **Netwerken** de volgende informatie invoeren of selecteren:

    | **Instelling** | **Waarde** |
    | ----------- | --------- |
    | **Netwerkinterface** |   |
    | Virtueel netwerk | Selecteer **myVNet**. |
    | Subnet | Selecteer **mySubnet**. |
    | Openbare IP | Selecteer **Geen**. |
    | NIC-netwerkbeveiligingsgroep | Selecteer **Basic**. |
    | Openbare poorten voor inkomend verkeer | Selecteer **Geen**. |

7. Selecteer het tabblad **controleren + maken** of selecteer de knop blauwe **revisie + maken** onder aan de pagina.

8. Selecteer **Maken**.

## <a name="test-nat-gateway"></a>NAT-gateway testen

In deze sectie testen we de NAT-gateway. Eerst wordt het open bare IP-adres van de NAT-gateway gedetecteerd. Vervolgens maakt u verbinding met de virtuele test machine en controleert u de uitgaande verbinding via de NAT-gateway.
    
1. Zoek het open bare IP-adres voor de NAT-gateway op het scherm **overzicht** . Selecteer **Alle services** in het linkermenu en selecteer **Alle resources**. Selecteer vervolgens **myPublicIP**.

2. Noteer het open bare IP-adres:

    :::image type="content" source="./media/tutorial-create-nat-gateway-portal/find-public-ip.png" alt-text="Openbaar IP-adres van NAT-gateway detecteren" border="true":::

3. Selecteer **alle services** in het linkermenu, selecteer **alle resources** en selecteer vervolgens in de lijst met resources **myVM** die zich in de resource groep **myResourceGroupNAT** bevindt.

4. Selecteer op de pagina **Overzicht** de optie **Verbinding maken** en daarna **Bastion**.

5. Selecteer de blauwe knop **Bastion gebruiken**.

6. Voer de gebruikersnaam en het wachtwoord in die zijn ingevoerd tijdens het maken van de VM.

7. Open **Internet Explorer** op **myTestVM**.

8. Voer **https://whatsmyip.com** in de adres balk in.

9. Controleer of het weer gegeven IP-adres overeenkomt met het NAT-gateway adres dat u in de vorige stap hebt genoteerd:

    :::image type="content" source="./media/tutorial-create-nat-gateway-portal/my-ip.png" alt-text="Internet Explorer met het externe uitgaande IP-adres" border="true":::

## <a name="clean-up-resources"></a>Resources opschonen

Als u deze toepassing niet wilt blijven gebruiken, verwijdert u het virtuele netwerk, de virtuele machine en de NAT-gateway door de volgende stappen uit te voeren:

1. Selecteer **Resourcegroepen** in het linkermenu.

2. Selecteer de resource groep **myResourceGroupNAT** .

3. Selecteer **Resourcegroep verwijderen**.

4. Voer **myResourceGroupNAT** in en selecteer **verwijderen**.

## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie over Azure Virtual Network NAT:
> [!div class="nextstepaction"]
> [Overzicht van Virtual Network NAT](nat-overview.md)
