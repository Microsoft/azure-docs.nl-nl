---
title: Netwerkverkeer filteren - zelfstudie - Azure Portal
titlesuffix: Azure Virtual Network
description: In deze zelfstudie leert u hoe u, met een netwerkbeveiligingsgroep, netwerkverkeer naar een subnet filtert met behulp van Azure Portal.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
tags: azure-resource-manager
Customer intent: I want to filter network traffic to virtual machines that perform similar functions, such as web servers.
ms.service: virtual-network
ms.devlang: ''
ms.topic: tutorial
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 12/13/2018
ms.author: kumud
ms.openlocfilehash: 97690618de5d58fa4022d01fa36a872f9d220083
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/14/2021
ms.locfileid: "98221677"
---
# <a name="tutorial-filter-network-traffic-with-a-network-security-group-using-the-azure-portal"></a>Zelfstudie: Netwerkverkeer filteren met een netwerkbeveiligingsgroep met behulp van Azure Portal

U kunt het netwerkverkeer inkomend in en uitgaand naar een subnet van een virtueel netwerk filteren met een netwerkbeveiligingsgroep. Netwerkbeveiligingsgroepen bevatten beveiligingsregels die netwerkverkeer filteren op IP-adres, poort en protocol. Beveiligingsregels worden toegepast op resources die zijn geïmplementeerd in een subnet. In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een netwerkbeveiligingsgroep en beveiligingsregels maken
> * Een virtueel netwerk maken en een netwerkbeveiligingsgroep koppelen aan een subnet
> * Virtuele machines (VM) implementeren in een subnet
> * Verkeersfilters testen

U kunt deze zelfstudie desgewenst voltooien met behulp van de [Azure CLI](tutorial-filter-network-traffic-cli.md) of [PowerShell](tutorial-filter-network-traffic-powershell.md).

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u aan bij Azure Portal op https://portal.azure.com.

## <a name="create-a-virtual-network"></a>Een virtueel netwerk maken

1. Selecteer in het menu van de Azure-portal of op de **startpagina** de optie **Een resource maken**. 
2. Selecteer **Netwerken** en selecteer vervolgens **Virtueel netwerk**.
3. Voer de volgende informatie in of selecteer deze, accepteer de standaardwaarden voor de overige instellingen en selecteer **Maken**:

    | Instelling                 | Waarde                                              |
    | ---                     | ---                                                |
    | Naam                    | myVirtualNetwork                                   |
    | Adresruimte           | 10.0.0.0/16                                        |
    | Abonnement            | Selecteer uw abonnement.                          |
    | Resourcegroep          | Selecteer **Nieuwe maken** en voer *myResourceGroup* in. |
    | Locatie                | Selecteer **VS - oost**.                                |
    | Subnet - naam            | mySubnet                                           |
    | Subnet - adresbereik  | 10.0.0.0/24                                        |

## <a name="create-application-security-groups"></a>Toepassingsbeveiligingsgroepen maken

Met een toepassingsbeveiligingsgroep kunt u servers met vergelijkbare functies groeperen, zoals webservers.

1. Selecteer in het menu van de Azure-portal of op de **startpagina** de optie **Een resource maken**. 
2. Voer *Toepassingsbeveiligingsgroep* in het vak **In Marketplace zoeken** in. Wanneer **Toepassingsbeveiligingsgroep** wordt weergegeven in de zoekresultaten, selecteert u deze optie. Vervolgens selecteert u onder **Alles** opnieuw **Toepassingsbeveiligingsgroep** en selecteert u **Maken**.
3. Voer de volgende gegevens in of selecteer deze, en selecteer **Maken**:

    | Instelling        | Waarde                                                         |
    | ---            | ---                                                           |
    | Naam           | myAsgWebServers                                               |
    | Abonnement   | Selecteer uw abonnement.                                     |
    | Resourcegroep | Selecteer **Bestaande gebruiken** en selecteer vervolgens **myResourceGroup**. |
    | Locatie       | VS - oost                                                       |

4. Voer stap 3 opnieuw uit en geef hierbij de volgende waarden op:

    | Instelling        | Waarde                                                         |
    | ---            | ---                                                           |
    | Naam           | myAsgMgmtServers                                              |
    | Abonnement   | Selecteer uw abonnement.                                     |
    | Resourcegroep | Selecteer **Bestaande gebruiken** en selecteer vervolgens **myResourceGroup**. |
    | Locatie       | VS - oost                                                       |

## <a name="create-a-network-security-group"></a>Een netwerkbeveiligingsgroep maken

1. Selecteer in het menu van de Azure-portal of op de **startpagina** de optie **Een resource maken**. 
2. Selecteer **Netwerken** en selecteer **Netwerkbeveiligingsgroep**.
3. Voer de volgende gegevens in of selecteer deze, en selecteer **Maken**:

    |Instelling|Waarde|
    |---|---|
    |Naam|myNsg|
    |Abonnement| Selecteer uw abonnement.|
    |Resourcegroep | Selecteer **Bestaande gebruiken** en selecteer *myResourceGroup*.|
    |Locatie|VS - oost|

## <a name="associate-network-security-group-to-subnet"></a>Netwerkbeveiligingsgroep koppelen aan subnet

1. Begin in het vak *Resources, services en documenten zoeken* boven aan de portal *myNsg* te typen. Wanneer **myNsg** wordt weergegeven in de zoekresultaten, selecteert u deze.
2. Selecteer onder **INSTELLINGEN** de optie **Subnetten**. Selecteer vervolgens **+ Koppelen**, zoals in de volgende afbeelding:

    ![NSG koppelen aan subnet](./media/tutorial-filter-network-traffic/associate-nsg-subnet.png)

3. Selecteer onder **Subnet koppelen** de optie **Virtueel netwerk**. Selecteer vervolgens **myVirtualNetwork**. Selecteer **Subnet**, selecteer **mySubnet** en selecteer vervolgens **OK**.

## <a name="create-security-rules"></a>Beveiligingsregels maken

1. Selecteer onder **INSTELLINGEN** de optie **Inkomende beveiligingsregels**. Selecteer vervolgens **+ Toevoegen** zoals wordt weergegeven in de volgende afbeelding:

    ![Een inkomende beveiligingsregel toevoegen](./media/tutorial-filter-network-traffic/add-inbound-rule.png)

2. Maak een beveiligingsregel op basis waarvan poorten 80 en 443 worden gekoppeld aan de beveiligingsgroep **myAsgWebServers**. Typ of selecteer onder **Inkomende beveiligingsregel toevoegen** de volgende waarden, accepteer de resterende standaardwaarden en selecteer vervolgens **Toevoegen**:

    | Instelling                 | Waarde                                                                                                           |
    | ---------               | ---------                                                                                                       |
    | Doel             | Selecteer **Toepassingsbeveiligingsgroep** en selecteer vervolgens **myAsgWebServers** als **Toepassingsbeveiligingsgroep**.  |
    | Poortbereiken van doel | Voer 80,443 in                                                                                                    |
    | Protocol                | Selecteer TCP                                                                                                      |
    | Name                    | Allow-Web-All                                                                                                   |

3. Voer stap 2 opnieuw uit, met behulp van de volgend waarden:

    | Instelling                 | Waarde                                                                                                           |
    | ---------               | ---------                                                                                                       |
    | Doel             | Selecteer **Toepassingsbeveiligingsgroep** en selecteer vervolgens **myAsgMgmtServers** als **Toepassingsbeveiligingsgroep**. |
    | Poortbereiken van doel | Voer 3389 in                                                                                                      |
    | Protocol                | Selecteer TCP                                                                                                      |
    | Prioriteit                | Voer 110 in                                                                                                       |
    | Name                    | Allow-RDP-All                                                                                                   |

    In deze zelfstudie wordt RDP (poort 3389) voor internet beschikbaar gemaakt voor de VM die is toegewezen aan de toepassingsbeveiligingsgroep *myAsgMgmtServers*. Voor productieomgevingen wordt u aanbevolen in plaats van poort 3389 voor internet te gebruiken, via een VPN of persoonlijke netwerkverbinding verbinding te maken met Azure-resources die u wilt beheren.

Zodra u stap 1 tot en met 3 hebt voltooid, controleert u de regels die u hebt gemaakt. De lijst ziet er ongeveer uit zoals de lijst in de volgende afbeelding:

![Beveiligingsregels](./media/tutorial-filter-network-traffic/security-rules.png)

## <a name="create-virtual-machines"></a>Virtuele machines maken

Maak twee VM’s in het virtuele netwerk.

### <a name="create-the-first-vm"></a>De eerste VM maken

1. Selecteer in het menu van de Azure-portal of op de **startpagina** de optie **Een resource maken**. 
2. Selecteer **Compute** en vervolgens **Windows Server 2016 Datacenter**.
3. Voer de volgende informatie in of selecteer deze, en accepteer de standaardwaarden voor de overige instellingen:

    |Instelling|Waarde|
    |---|---|
    |Abonnement| Selecteer uw abonnement.|
    |Resourcegroep| Selecteer **Bestaande gebruiken** en selecteer **myResourceGroup**.|
    |Name|myVmWeb|
    |Locatie| Selecteer **VS - oost**.|
    |Gebruikersnaam| Voer een gebruikersnaam naar keuze in.|
    |Wachtwoord| Voer een wachtwoord naar keuze in. Het wachtwoord moet minstens 12 tekens lang zijn en moet voldoen aan de [gedefinieerde complexiteitsvereisten](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|

   

4. Selecteer een grootte voor de virtuele machine en selecteer **Selecteren**.
5. Selecteer onder **Netwerken** de volgende waarden en accepteer de resterende standaardwaarden:

    |Instelling|Waarde|
    |---|---|
    |Virtueel netwerk |Selecteer **myVirtualNetwork**.|
    |NIC-netwerkbeveiligingsgroep |Selecteer **Geen**.|
  

6. Selecteer links onderaan **Bekijken en maken** en selecteer **Maken** om de implementatie van de VM te starten.

### <a name="create-the-second-vm"></a>De tweede VM maken

Voer stap 1 tot en met 6 opnieuw uit, maar geef in stap 3 de VM de naam *myVmMgmt*. Het implementeren van de VM duurt een paar minuten. Ga pas verder met de volgende stap wanneer de VM is geïmplementeerd.

## <a name="associate-network-interfaces-to-an-asg"></a>Netwerkinterfaces koppelen aan een ASG

Toen de VM’s werden gemaakt in de portal, werd voor elke VM ook een netwerkinterface gemaakt en gekoppeld aan de VM. Voeg de netwerkinterface voor elke VM toe aan een van de toepassingsbeveiligingsgroepen die u eerder hebt gemaakt:

1. Begin in het vak *Resources, services en documenten zoeken* boven aan de portal *myVmWeb* te typen. Wanneer de VM **myVmPrivate** wordt weergegeven in de zoekresultaten, selecteert u deze.
2. Selecteer onder **INSTELLINGEN** de optie **Netwerken**.  Selecteer **De toepassingsbeveiligingsgroepen configureren**, selecteer **myAsgWebServers** voor **Toepassingsbeveiligingsgroepen** en selecteer vervolgens **Opslaan** zoals wordt weergegeven in de volgende afbeelding:

    ![Koppelen aan ASG](./media/tutorial-filter-network-traffic/associate-to-asg.png)

3. Voer stap 1 en 2 opnieuw uit, waarbij u zoekt naar de VM **myVmMgmt** en de ASG **myAsgMgmtServers** selecteert.

## <a name="test-traffic-filters"></a>Verkeersfilters testen

1. Maak verbinding met de VM *myVmMgmt*. Voer in het zoekvak boven aan de portal *myVmMgmt* in. Wanneer **myVmMgmt** wordt weergegeven in de zoekresultaten, selecteert u deze. Selecteer de knop **Verbinding maken**.
2. Selecteer **RDP-bestand downloaden**.
3. Open het gedownloade RDP-bestand en selecteer **Verbinding maken**. Voer de gebruikersnaam en het wachtwoord in die u hebt opgegeven bij het maken van de virtuele machine. Mogelijk moet u **Meer opties** en vervolgens **Een ander account gebruiken** selecteren om de aanmeldingsgegevens op te geven die u hebt ingevoerd tijdens het maken van de VM.
4. Selecteer **OK**.
5. Er wordt mogelijk een certificaatwaarschuwing weergegeven tijdens het aanmelden. Als u de waarschuwing ontvangt, selecteert u **Ja** of **Doorgaan** om door te gaan met de verbinding.

    De verbinding slaagt, omdat inkomend verkeer van internet naar de toepassingsbeveiligingsgroep *myAsgMgmtServers* waarin de netwerkinterface die is gekoppeld aan de VM *myVmMgmt* zich bevindt, is toegestaan voor poort 3389.

6. Maak vanaf de VM *myVmMgmt* verbinding met de VM *myVmWeb* door de volgende opdracht uit te voeren in een PowerShell-sessie:

    ``` 
    mstsc /v:myVmWeb
    ```

    U kunt vanaf de VM myVmMgmt verbinding maken met de VM myVmWeb omdat VM’s in hetzelfde virtuele netwerk standaard via elke willekeurige poort met elkaar kunnen communiceren. U kunt echter geen externe bureaubladverbinding tot stand brengen met de VM *myVmWeb* via internet omdat de beveiligingsregel voor de *myAsgWebServers* geen inkomend verkeer van internet toestaat via poort 3389 en inkomende verkeer vanaf internet standaard wordt geweigerd voor alle resources.

7. Als u Microsoft IIS wilt installeren op de VM *myVmWeb*, voert u de volgende opdracht uit vanuit een PowerShell-sessie op de VM *myVmWeb*:

    ```powershell
    Install-WindowsFeature -name Web-Server -IncludeManagementTools
    ```

8. Nadat de installatie van IIS is voltooid, verbreekt u de verbinding met de VM *myVmWeb*, waarbij u de externe bureaubladverbinding met VM *myVmMgmt* behoudt.
9. Verbreek de verbinding met de VM *myVmMgmt*.
10. Begin in het vak *Resources, services en documenten zoeken* boven aan Azure Portal *myVmWeb* te typen op de computer. Wanneer **myVmWeb** wordt weergegeven in de zoekresultaten, selecteert u deze. Noteer het **openbare IP-adres** van de VM. Het adres dat wordt weergegeven in de volgende afbeelding, is 137.135.84.74 maar u hebt een ander adres:

    ![Openbaar IP-adres](./media/tutorial-filter-network-traffic/public-ip-address.png)
  
11. Om te bevestigen dat u vanaf internet toegang hebt tot de *myVmWeb*-webserver, opent u een internetbrowser op de computer en bladert u naar `http://<public-ip-address-from-previous-step>`. U ziet het ISS-welkomstscherm, omdat inkomend verkeer van internet naar de toepassingsbeveiligingsgroep *myAsgWebServers* waarin de netwerkinterface die is gekoppeld aan de VM *myVmWeb* zich bevindt, is toegestaan voor poort 80.

## <a name="clean-up-resources"></a>Resources opschonen

U kunt de resourcegroep en alle gerelateerde resources die deze bevat verwijderen wanneer u deze niet meer nodig hebt:

1. Voer *myResourceGroup* in het vak **Zoeken** bovenaan de portal in. Wanneer u **myResourceGroup** ziet in de zoekresultaten, selecteert u deze.
2. Selecteer **Resourcegroep verwijderen**.
3. Voer *myResourceGroup* in voor **TYP DE RESOURCEGROEPNAAM:** en selecteer **Verwijderen**.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u een netwerkbeveiligingsgroep gemaakt en die aan een subnet van een virtueel netwerk gekoppeld. Zie [Overzicht van netwerkbeveiligingsgroepen](./network-security-groups-overview.md) en [Een beveiligingsgroep beheren](manage-network-security-group.md) voor meer informatie over netwerkbeveiligingsgroepen.

Azure routeert standaard verkeer tussen subnetten. In plaats daarvan kunt u verkeer routeren tussen subnetten via een virtuele machine, die bijvoorbeeld als een firewall fungeert. Ga verder met de volgende zelfstudie om te leren hoe u een routetabel maakt.

> [!div class="nextstepaction"]
> [Een routetabel maken](./tutorial-create-route-table-portal.md)