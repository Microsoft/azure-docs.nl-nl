---
title: Netwerk verkeer filteren-zelf studie-Azure Portal
titlesuffix: Azure Virtual Network
description: In deze zelfstudie leert u hoe u, met een netwerkbeveiligingsgroep, netwerkverkeer naar een subnet filtert met behulp van Azure Portal.
services: virtual-network
author: KumudD
Customer intent: I want to filter network traffic to virtual machines that perform similar functions, such as web servers.
ms.service: virtual-network
ms.topic: tutorial
ms.date: 03/06/2021
ms.author: kumud
ms.openlocfilehash: 746e44c85d4dd9a662556a73f1e4ab0701d31400
ms.sourcegitcommit: 5bbc00673bd5b86b1ab2b7a31a4b4b066087e8ed
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/07/2021
ms.locfileid: "102435894"
---
# <a name="tutorial-filter-network-traffic-with-a-network-security-group-using-the-azure-portal"></a>Zelfstudie: Netwerkverkeer filteren met een netwerkbeveiligingsgroep met behulp van Azure Portal

U kunt een netwerk beveiligings groep gebruiken om het netwerk verkeer inkomend en uitgaand van een subnet van een virtueel netwerk te filteren.

Netwerkbeveiligingsgroepen bevatten beveiligingsregels die netwerkverkeer filteren op IP-adres, poort en protocol. Beveiligingsregels worden toegepast op resources die zijn geïmplementeerd in een subnet. 

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een netwerkbeveiligingsgroep en beveiligingsregels maken
> * Een virtueel netwerk maken en een netwerkbeveiligingsgroep koppelen aan een subnet
> * Virtuele machines (VM) implementeren in een subnet
> * Verkeersfilters testen

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten

- Een Azure-abonnement.

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u aan bij Azure Portal op https://portal.azure.com.

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

5. Selecteer het tabblad **controleren + maken** of selecteer de knop blauwe **revisie + maken** onder aan de pagina.

6. Selecteer **Maken**.

## <a name="create-application-security-groups"></a>Toepassingsbeveiligingsgroepen maken

Met een toepassingsbeveiligingsgroep kunt u servers met vergelijkbare functies groeperen, zoals webservers.

1. Selecteer **een resource maken** in de linkerbovenhoek van de portal.

2. Voer in het zoekvak **toepassings beveiligings groep** in. Selecteer **toepassings beveiligings groep** in de zoek resultaten.

3. Selecteer op de pagina **toepassings beveiligings groep** de optie **maken**.

4. In **een toepassings beveiligings groep maken**, typt of selecteert u deze informatie op het tabblad **basis beginselen** :

    | Instelling | Waarde |
    | ------- | ----- |
    |**Projectgegevens** |  |
    | Abonnement | Selecteer uw abonnement. |
    | Resourcegroep | Selecteer **myResourceGroup**. |
    | **Exemplaardetails** |  |
    | Naam | Voer **myAsgWebServers** in. |
    | Region | Selecteer **VS Oost**. | 

5. Selecteer het tabblad **controleren + maken** of selecteer de knop blauwe **revisie + maken** onder aan de pagina.

6. Selecteer **Maken**.

7. Herhaal stap 4 opnieuw, waarbij u de volgende waarden opgeeft:

    | Instelling | Waarde |
    | ------- | ----- |
    |**Projectgegevens** |  |
    | Abonnement | Selecteer uw abonnement. |
    | Resourcegroep | Selecteer **myResourceGroup**. |
    | **Exemplaardetails** |  |
    | Naam | Voer **myAsgMgmtServers** in. |
    | Region | Selecteer **VS Oost**. |

8. Selecteer het tabblad **controleren + maken** of selecteer de knop blauwe **revisie + maken** onder aan de pagina.

9. Selecteer **Maken**.

## <a name="create-a-network-security-group"></a>Een netwerkbeveiligingsgroep maken

Een netwerk beveiligings groep beveiligt netwerk verkeer in het virtuele netwerk.

1. Selecteer **een resource maken** in de linkerbovenhoek van de portal.

2. Voer in het zoekvak **netwerk beveiligings groep** in. Selecteer **netwerk beveiligings groep** in de zoek resultaten.

3. Selecteer op de pagina **netwerk beveiligings groep** de optie **maken**.

4. In **netwerk beveiligings groep maken**, typt of selecteert u deze informatie op het tabblad **basis beginselen** :

    | Instelling | Waarde |
    | ------- | ----- |
    | **Projectgegevens** |   |
    | Abonnement | Selecteer uw abonnement. |
    | Resourcegroep | Selecteer **myResourceGroup**. |
    | **Exemplaardetails** |   |
    | Naam | Voer **mijnnbg** in. |
    | Locatie | Selecteer **VS Oost**. | 

5. Selecteer het tabblad **controleren + maken** of selecteer de knop blauwe **revisie + maken** onder aan de pagina.

6. Selecteer **Maken**.

## <a name="associate-network-security-group-to-subnet"></a>Netwerkbeveiligingsgroep koppelen aan subnet

In deze sectie wordt de netwerk beveiligings groep gekoppeld aan het subnet van het virtuele netwerk dat u eerder hebt gemaakt.

1. Begin in het vak **Resources, services en documenten zoeken** boven aan de portal **myNsg** te typen. Wanneer **myNsg** wordt weergegeven in de zoekresultaten, selecteert u deze.

2. Selecteer op de pagina overzicht van **Mijnnbg** **subnetten** in **instellingen**.

3. Selecteer op de pagina **instellingen** de optie **koppelen**:

    :::image type="content" source="./media/tutorial-filter-network-traffic/associate-nsg-subnet.png" alt-text="NSG koppelen aan subnet." border="true":::

3. Onder **subnet koppelen** selecteert u **virtueel netwerk** en selecteert u vervolgens **myVNet**. 

4. Selecteer **subnet**, selecteer **standaard** en selecteer vervolgens **OK**.

## <a name="create-security-rules"></a>Beveiligingsregels maken

1. Selecteer **binnenkomende beveiligings regels** in **instellingen** van **mijnnbg**.

2. Selecteer **+ toevoegen** bij **regels voor binnenkomende beveiliging**:

    :::image type="content" source="./media/tutorial-filter-network-traffic/add-inbound-rule.png" alt-text="Binnenkomende beveiligings regel toevoegen." border="true":::

3. Maak een beveiligingsregel op basis waarvan poorten 80 en 443 worden gekoppeld aan de beveiligingsgroep **myAsgWebServers**. Voer bij **regel voor binnenkomende beveiliging toevoegen** de volgende informatie in of Selecteer deze:

    | Instelling | Waarde |
    | ------- | ----- |
    | Bron | De standaard waarde van **any** laten. |
    | Poortbereiken van bron | De standaard waarde van **(*)** behouden |
    | Doel | Selecteer **toepassings beveiligings groep**. |
    | Beveiligings groep voor de doel toepassing | Selecteer **myAsgWebServers**. |
    | Service | Wijzig de standaard instelling **aangepast**. |
    | Poortbereiken van doel | Voer **80.443** in. |
    | Protocol | selecteer **TCP**. |
    | Bewerking | Laat de standaard waarde **toestaan**. |
    | Prioriteit | Wijzig de standaard waarde van **100**. |
    | Name | Voer **allow-web-all** in. |

    :::image type="content" source="./media/tutorial-filter-network-traffic/inbound-security-rule.png" alt-text="Binnenkomende beveiligings regel." border="true":::

3. Voer stap 2 opnieuw uit, met behulp van de volgend waarden:

    | Instelling | Waarde |
    | ------- | ----- |
    | Bron | De standaard waarde van **any** laten. |
    | Poortbereiken van bron | De standaard waarde van **(*)** behouden |
    | Doel | Selecteer **toepassings beveiligings groep**. |
    | Beveiligings groep voor de doel toepassing | Selecteer **myAsgMgmtServers**. |
    | Service | Wijzig de standaard instelling **aangepast**. |
    | Poortbereiken van doel | Voer **3389** in. |
    | Protocol | selecteer **TCP**. |
    | Bewerking | Laat de standaard waarde **toestaan**. |
    | Prioriteit | Wijzig de standaard waarde van **110**. |
    | Name | Voer **Allow-RDP-all** in. |

    > [!CAUTION]
    > In dit artikel wordt RDP (poort 3389) blootgesteld aan Internet voor de virtuele machine die is toegewezen aan de **myAsgMgmtServers** -toepassings beveiligings groep. 
    >
    > Voor productie omgevingen, in plaats van poort 3389 aan Internet weer te geven, is het raadzaam om verbinding te maken met Azure-resources die u wilt beheren met een VPN, een particuliere netwerk verbinding of Azure Bastion.
    >
    > Zie [Wat is Azure Bastion?](../bastion/bastion-overview.md) voor meer informatie over Azure Bastion.

Zodra u stap 1 tot en met 3 hebt voltooid, controleert u de regels die u hebt gemaakt. De lijst moet eruitzien als in de lijst in het volgende voor beeld:

:::image type="content" source="./media/tutorial-filter-network-traffic/security-rules.png" alt-text="Beveiligings regels." border="true":::

## <a name="create-virtual-machines"></a>Virtuele machines maken

Maak twee VM’s in het virtuele netwerk.

### <a name="create-the-first-vm"></a>De eerste VM maken

1. Selecteer **een resource maken** in de linkerbovenhoek van de portal.

2. Selecteer **Compute** en selecteer vervolgens **Virtual Machine**.

3. In **een virtuele machine maken**, typt of selecteert u deze informatie op het tabblad **basis beginselen** :

    | Instelling | Waarde |
    | ------- | ----- |
    | **Projectgegevens** |  |
    | Abonnement | Selecteer uw abonnement. |
    | Resourcegroep | Selecteer **myResourceGroup**. |
    | **Exemplaardetails** |   |
    | Naam van de virtuele machine | Voer **VM myvmweb** in. |
    | Region | Selecteer **VS Oost**. |
    | Beschikbaarheidsopties | Behoud de standaard waarde geen redundantie vereist. |
    | Installatiekopie | Selecteer **Windows Server 2019 Data Center-gen1**. |
    | Azure Spot-exemplaar | De standaard instelling van uitgeschakeld laten. |
    | Grootte | Selecteer **Standard_D2s_V3**. |
    | **Beheerdersaccount** |   |
    | Gebruikersnaam | Voer een gebruikers naam in. |
    | Wachtwoord | Voer een wachtwoord in. |
    | Wachtwoord bevestigen | Voer het wachtwoord opnieuw in. |
    | **Regels voor binnenkomende poort** |   |
    | Openbare poorten voor inkomend verkeer | Selecteer **Geen**. |

4. Selecteer het tabblad **Netwerken**.

5. Op het tabblad **Netwerken** de volgende informatie invoeren of selecteren:

    | Instelling | Waarde |
    | ------- | ----- |
    | **Netwerkinterface** |   |
    | Virtueel netwerk | Selecteer **myVNet**. |
    | Subnet | Selecteer **standaard (10.0.0.0/24)**. |
    | Openbare IP | De standaard waarde van een nieuw openbaar IP-adres behouden. |
    | NIC-netwerkbeveiligingsgroep | Selecteer **Geen**. | 

6. Selecteer het tabblad **controleren + maken** of selecteer de knop blauwe **revisie + maken** onder aan de pagina.

7. Selecteer **Maken**.

### <a name="create-the-second-vm"></a>De tweede VM maken

Voer de stappen 1-7 opnieuw uit, maar noem de VM- **VM myvmmgmt** in stap 3. Het implementeren van de VM duurt een paar minuten. 

Ga niet verder met de volgende stap totdat de virtuele machine is geïmplementeerd.

## <a name="associate-network-interfaces-to-an-asg"></a>Netwerkinterfaces koppelen aan een ASG

Toen de VM’s werden gemaakt in de portal, werd voor elke VM ook een netwerkinterface gemaakt en gekoppeld aan de VM. 

Voeg de netwerkinterface voor elke VM toe aan een van de toepassingsbeveiligingsgroepen die u eerder hebt gemaakt:

1. Begin in het vak **resources, services en documenten zoeken** bovenaan de portal **VM myvmweb** te typen. Wanneer de virtuele machine **VM myvmweb** wordt weer gegeven in de zoek resultaten, selecteert u deze.

2. Selecteer in **instellingen** de optie **netwerken**.  

3. Selecteer het tabblad **toepassings beveiligings groepen** en selecteer vervolgens **de toepassings beveiligings groepen configureren**.

    :::image type="content" source="./media/tutorial-filter-network-traffic/configure-app-sec-groups.png" alt-text="Configureer toepassings beveiligings groepen." border="true":::

4. Selecteer **myAsgWebServers** in **de toepassings beveiligings groepen configureren**. Selecteer **Opslaan**.

    :::image type="content" source="./media/tutorial-filter-network-traffic/select-asgs.png" alt-text="Selecteer toepassings beveiligings groepen." border="true":::

5. Voer stap 1 en 2 opnieuw uit, zoek naar de virtuele machine **VM myvmmgmt** en selecteer de  **myAsgMgmtServers** -ASG.

## <a name="test-traffic-filters"></a>Verkeersfilters testen

1. Maak verbinding met de **VM myvmmgmt** -VM. Voer **VM myvmmgmt** in het zoekvak boven aan de portal in. Wanneer **VM myvmmgmt** wordt weer gegeven in de zoek resultaten, selecteert u dit. Selecteer de knop **Verbinding maken**.

2. Selecteer **RDP-bestand downloaden**.

3. Open het gedownloade RDP-bestand en selecteer **Verbinding maken**. Voer de gebruikersnaam en het wachtwoord in die u hebt opgegeven bij het maken van de virtuele machine.

4. Selecteer **OK**.

5. Er wordt mogelijk een certificaat waarschuwing weer gegeven tijdens het verbindings proces. Als u de waarschuwing ontvangt, selecteert u **Ja** of **door gaan** om door te gaan met de verbinding.

    De verbinding is geslaagd, omdat poort 3389 binnenkomend van het internet naar de **myAsgMgmtServers** toepassings beveiligings groep is toegestaan. 
    
    De netwerk interface voor **VM myvmmgmt** is gekoppeld aan de beveiligings groep van de **myAsgMgmtServers** -toepassing en maakt de verbinding mogelijk.

6. Open een Power shell-sessie op **VM myvmmgmt**. Maak verbinding met **VM myvmweb** met behulp van het volgende voor beeld: 

    ```powershell
    mstsc /v:myVmWeb
    ```

    De RDP-verbinding van **VM myvmmgmt** naar **VM myvmweb** slaagt, omdat virtuele machines in hetzelfde netwerk standaard met elkaar kunnen communiceren via een wille keurige poort.
    
    U kunt geen RDP-verbinding met de virtuele **VM myvmweb** -machine maken via internet. De beveiligings regel voor de **myAsgWebServers** voor komt dat verbindingen met poort 3389 binnenkomend via internet. Binnenkomend verkeer van Internet wordt standaard voor alle resources geweigerd.

7. Als u micro soft IIS wilt installeren op de virtuele **VM myvmweb** -machine, voert u de volgende opdracht uit vanuit een Power shell-sessie op de virtuele **VM myvmweb** -machine:

    ```powershell
    Install-WindowsFeature -name Web-Server -IncludeManagementTools
    ```

8. Nadat de installatie van IIS is voltooid, verbreekt u de verbinding met de virtuele **VM myvmweb** -machine, waarmee u in de virtuele machine met **VM myvmmgmt** -verbinding met extern bureau blad.

9. Verbreek de verbinding met de **VM myvmmgmt** -VM.

10. Begin in het vak **resources, services en documenten zoeken** bovenaan de Azure Portal **VM myvmweb** van uw computer te typen. Wanneer **VM myvmweb** wordt weer gegeven in de zoek resultaten, selecteert u dit. Noteer het **openbare IP-adres** van de VM. Het adres dat wordt weer gegeven in het volgende voor beeld is 23.96.39.113, maar uw adres verschilt:

    :::image type="content" source="./media/tutorial-filter-network-traffic/public-ip-address.png" alt-text="Openbaar IP-adres." border="true":::
    
11. Als u wilt controleren of u via internet toegang hebt tot de **VM myvmweb** -webserver, opent u een Internet browser op uw computer en bladert u naar `http://<public-ip-address-from-previous-step>` . 

Het welkomst scherm van IIS wordt weer gegeven, omdat poort 80 inkomende van Internet naar de **myAsgWebServers** -toepassings beveiligings groep is toegestaan. 

De netwerk interface die is gekoppeld aan **VM myvmweb** is gekoppeld aan de beveiligings groep van de **myAsgWebServers** -toepassing en maakt de verbinding mogelijk. 

## <a name="clean-up-resources"></a>Resources opschonen

U kunt de resourcegroep en alle gerelateerde resources die deze bevat verwijderen wanneer u deze niet meer nodig hebt:

1. Voer **myResourceGroup** in het vak **Zoeken** bovenaan de portal in. Wanneer u **myResourceGroup** ziet in de zoekresultaten, selecteert u deze.
2. Selecteer **Resourcegroep verwijderen**.
3. Voer **myResourceGroup** in voor **TYP DE RESOURCEGROEPNAAM:** en selecteer **Verwijderen**.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u:

* Er is een netwerk beveiligings groep gemaakt die aan een subnet van een virtueel netwerk is gekoppeld. 
* De toepassings beveiligings groepen voor web en beheer zijn gemaakt.
* Er zijn twee virtuele machines gemaakt.
* De netwerk filtering van de toepassings beveiligings groep is getest.

Zie [Overzicht van netwerkbeveiligingsgroepen](./network-security-groups-overview.md) en [Een beveiligingsgroep beheren](manage-network-security-group.md) voor meer informatie over netwerkbeveiligingsgroepen.

Azure routeert standaard verkeer tussen subnetten. In plaats daarvan kunt u verkeer routeren tussen subnetten via een virtuele machine, die bijvoorbeeld als een firewall fungeert. 

Ga verder met de volgende zelfstudie om te leren hoe u een routetabel maakt.
> [!div class="nextstepaction"]
> [Een routetabel maken](./tutorial-create-route-table-portal.md)