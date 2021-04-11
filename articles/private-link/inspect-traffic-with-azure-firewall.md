---
title: Azure Firewall gebruiken om verkeer te controleren dat is bestemd voor een privé-eindpunt
titleSuffix: Azure Private Link
description: Meer informatie over hoe u verkeer kunt controleren dat is bestemd voor een persoonlijk eind punt met behulp van Azure Firewall.
services: private-link
author: jocortems
ms.service: private-link
ms.topic: how-to
ms.date: 09/02/2020
ms.author: allensu
ms.openlocfilehash: c3218d8781377e76f05d10a8da2c954ac0b685a7
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105641991"
---
# <a name="use-azure-firewall-to-inspect-traffic-destined-to-a-private-endpoint"></a>Azure Firewall gebruiken om verkeer te controleren dat is bestemd voor een privé-eindpunt

Persoonlijk Azure-eind punt is de fundamentele bouw steen voor persoonlijke Azure-koppelingen. Met persoonlijke eind punten kunnen Azure-resources worden geïmplementeerd in een virtueel netwerk om privé te communiceren met persoonlijke koppelings bronnen.

Met persoonlijke eind punten is toegang tot de persoonlijke koppelings service in een virtueel netwerk mogelijk. Toegang tot het persoonlijke eind punt via de peering van virtuele netwerken en on-premises netwerk verbindingen breiden de connectiviteit uit.

U moet mogelijk verkeer van clients controleren of blok keren naar de services die worden weer gegeven via persoonlijke eind punten. Voltooi deze inspectie door gebruik te maken van [Azure firewall](../firewall/overview.md) of een virtueel netwerk apparaat van derden.

De volgende beperkingen zijn van toepassing:

* Netwerk beveiligings groepen (NSG) worden overgeslagen door verkeer dat afkomstig is van privé-eind punten
* Door de gebruiker gedefinieerde routes (UDR) worden overgeslagen door verkeer dat afkomstig is van privé-eind punten
* U kunt één route tabel aan een subnet koppelen
* Een route tabel ondersteunt Maxi maal 400 routes

Met Azure Firewall wordt verkeer gefilterd met behulp van:

* [FQDN in netwerk regels](../firewall/fqdn-filtering-network-rules.md) voor TCP-en UDP-protocollen
* [FQDN in toepassings regels](../firewall/features.md#application-fqdn-filtering-rules) voor http, HTTPS en MSSQL. 

> [!IMPORTANT] 
> Het gebruik van toepassings regels via netwerk regels wordt aanbevolen bij het inspecteren van verkeer dat is bestemd voor privé-eind punten om stroom symmetrie te hand haven. Als er netwerk regels worden gebruikt, of als er een NVA wordt gebruikt in plaats van Azure Firewall, moet SNAT worden geconfigureerd voor verkeer dat is bestemd voor privé-eind punten.

> [!NOTE]
> SQL FQDN-filtering wordt alleen ondersteund in de [proxy modus](../azure-sql/database/connectivity-architecture.md#connection-policy) (poort 1433). **Proxy** modus kan leiden tot meer latentie in vergelijking met *omleiden*. Als u de omleidings modus wilt blijven gebruiken. Dit is de standaard instelling voor clients die verbinding maken in Azure. u kunt de toegang filteren met behulp van FQDN in Firewall-netwerk regels.

## <a name="scenario-1-hub-and-spoke-architecture---dedicated-virtual-network-for-private-endpoints"></a>Scenario 1: hub-en spoke-architectuur-toegewezen virtueel netwerk voor privé-eind punten

:::image type="content" source="./media/inspect-traffic-using-azure-firewall/hub-and-spoke.png" alt-text="Toegewezen Virtual Network voor privé-eind punten" border="true":::

Dit scenario is de meest uitbreid bare architectuur om persoonlijke verbinding te maken met meerdere Azure-Services met behulp van privé-eind punten. Er wordt een route gemaakt die verwijst naar de netwerk adres ruimte waar de persoonlijke eind punten worden geïmplementeerd. Deze configuratie vermindert de administratieve overhead en voor komt dat de limiet van 400 routes wordt uitgevoerd.

Verbindingen van een virtueel netwerk van de client naar de Azure Firewall in een hub-virtueel netwerk zullen kosten in rekening brengen als de virtuele netwerken aan elkaar zijn gekoppeld. Verbindingen van Azure Firewall in een hub virtueel netwerk naar privé-eind punten in een gekoppeld virtueel netwerk worden niet in rekening gebracht.

Zie de sectie Veelgestelde vragen van de pagina met [prijzen](https://azure.microsoft.com/pricing/details/private-link/) voor meer informatie over de kosten voor verbindingen met gekoppelde virtuele netwerken.

## <a name="scenario-2-hub-and-spoke-architecture---shared-virtual-network-for-private-endpoints-and-virtual-machines"></a>Scenario 2: een hub-en spoke-architectuur-gedeeld virtueel netwerk voor persoonlijke eind punten en virtuele machines

:::image type="content" source="./media/inspect-traffic-using-azure-firewall/shared-spoke.png" alt-text="Persoonlijke eind punten en Virtual Machines in dezelfde Virtual Network" border="true":::

Dit scenario wordt geïmplementeerd wanneer:

* Het is niet mogelijk om een speciaal virtueel netwerk te hebben voor de privé-eind punten

* Wanneer er slechts enkele services worden weer gegeven in het virtuele netwerk met behulp van privé-eind punten

De virtuele machines hebben/32 systeem routes die naar elk privé-eind punt wijzen. Eén route per particulier eind punt is geconfigureerd voor het routeren van verkeer via Azure Firewall. 

De administratieve overhead van het onderhouden van de route tabel neemt toe naarmate services worden weer gegeven in het virtuele netwerk. De mogelijkheid om de route limiet te verhogen, neemt ook toe.

Afhankelijk van uw algemene architectuur is het mogelijk om de limiet van 400 routes uit te voeren. Het is raadzaam om scenario 1 zoveel mogelijk te gebruiken.

Verbindingen van een virtueel netwerk van de client naar de Azure Firewall in een hub-virtueel netwerk zullen kosten in rekening brengen als de virtuele netwerken aan elkaar zijn gekoppeld. Verbindingen van Azure Firewall in een hub virtueel netwerk naar privé-eind punten in een gekoppeld virtueel netwerk worden niet in rekening gebracht.

Zie de sectie Veelgestelde vragen van de pagina met [prijzen](https://azure.microsoft.com/pricing/details/private-link/) voor meer informatie over de kosten voor verbindingen met gekoppelde virtuele netwerken.

## <a name="scenario-3-single-virtual-network"></a>Scenario 3: Eén virtueel netwerk

:::image type="content" source="./media/inspect-traffic-using-azure-firewall/single-vnet.png" alt-text="Eén virtueel netwerk" border="true":::

Gebruik dit patroon wanneer een migratie naar een hub-en-spoke-architectuur niet mogelijk is. Dezelfde overwegingen als in scenario 2 zijn van toepassing. In dit scenario zijn de kosten voor peering voor virtuele netwerken niet van toepassing.

## <a name="scenario-4-on-premises-traffic-to-private-endpoints"></a>Scenario 4: on-premises verkeer naar privé-eind punten

:::image type="content" source="./media/inspect-traffic-using-azure-firewall/on-premises.png" alt-text="On-premises verkeer naar privé-eind punten" border="true":::

Deze architectuur kan worden geïmplementeerd als u verbinding met uw on-premises netwerk hebt geconfigureerd met behulp van: 

* [ExpressRoute](..\expressroute\expressroute-introduction.md)
* [VPN van site naar site](../vpn-gateway/tutorial-site-to-site-portal.md) 

Als uw beveiligings vereisten client verkeer vereisen voor services die worden weer gegeven via persoonlijke eind punten om te worden gerouteerd via een beveiligings apparaat, implementeert u dit scenario.

Dezelfde overwegingen als in scenario 2 hierboven zijn van toepassing. In dit scenario zijn er geen kosten voor peering van virtuele netwerken. Zie [on-premises workloads met een DNS-doorstuur server](./private-endpoint-dns.md#on-premises-workloads-using-a-dns-forwarder)voor meer informatie over het configureren van uw DNS-servers om on-premises workloads toegang te geven tot privé-eind punten.

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement.
* Een Log Analytics-werkruimte.  

Zie [een log Analytics-werk ruimte maken in de Azure Portal](../azure-monitor/logs/quick-create-workspace.md) om een werk ruimte te maken als u er nog geen hebt in uw abonnement.


## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u aan bij Azure Portal op https://portal.azure.com.

## <a name="create-a-vm"></a>Een virtuele machine maken

In deze sectie maakt u een virtueel netwerk en een subnet voor het hosten van de virtuele machine die wordt gebruikt voor toegang tot uw persoonlijke koppelings bron. Een Azure-SQL database wordt gebruikt als de voorbeeld service.

## <a name="virtual-networks-and-parameters"></a>Virtuele netwerken en para meters

Maak drie virtuele netwerken en de bijbehorende subnetten om:

* Bevatten de Azure Firewall waarmee de communicatie tussen de virtuele machine en het persoonlijke eind punt wordt beperkt.
* Host de virtuele machine die wordt gebruikt voor toegang tot uw persoonlijke koppelings bron.
* Host het persoonlijke eind punt.

Vervang de volgende para meters in de stappen door de onderstaande informatie:

### <a name="azure-firewall-network"></a>Azure Firewall netwerk

| Parameter                   | Waarde                 |
|-----------------------------|----------------------|
| **\<resource-group-name>**  | myResourceGroup |
| **\<virtual-network-name>** | myAzFwVNet          |
| **\<region-name>**          | VS - zuid-centraal      |
| **\<IPv4-address-space>**   | 10.0.0.0/16          |
| **\<subnet-name>**          | AzureFirewallSubnet        |
| **\<subnet-address-range>** | 10.0.0.0/24          |

### <a name="virtual-machine-network"></a>Netwerk van de virtuele machine

| Parameter                   | Waarde                |
|-----------------------------|----------------------|
| **\<resource-group-name>**  | myResourceGroup |
| **\<virtual-network-name>** | myVMVNet          |
| **\<region-name>**          | VS - zuid-centraal      |
| **\<IPv4-address-space>**   | 10.1.0.0/16          |
| **\<subnet-name>**          | VM      |
| **\<subnet-address-range>** | 10.1.0.0/24          |

### <a name="private-endpoint-network"></a>Particulier eindpunt netwerk

| Parameter                   | Waarde                 |
|-----------------------------|----------------------|
| **\<resource-group-name>**  | myResourceGroup |
| **\<virtual-network-name>** | myPEVNet         |
| **\<region-name>**          | VS - zuid-centraal      |
| **\<IPv4-address-space>**   | 10.2.0.0/16          |
| **\<subnet-name>**          | PrivateEndpointSubnet |
| **\<subnet-address-range>** | 10.2.0.0/24          |

[!INCLUDE [virtual-networks-create-new](../../includes/virtual-networks-create-new.md)]

10. Herhaal stap 1 tot en met 9 om de virtuele netwerken te maken voor het hosten van de resources van de virtuele machine en het persoonlijke eind punt.

### <a name="create-virtual-machine"></a>Virtuele machine maken

1. Selecteer in de linkerbovenhoek van het scherm in het Azure Portal **een**  >    >  **virtuele machine** voor het berekenen van een resource maken.

2. Typ of selecteer in **Een virtuele machine maken - Basisprincipes** de volgende gegevens:

    | Instelling | Waarde |
    | ------- | ----- |
    | **Projectgegevens** | |
    | Abonnement | Selecteer uw abonnement. |
    | Resourcegroep | Selecteer **myResourceGroup**. U hebt deze resourcegroep in de vorige sectie gemaakt.  |
    | **Exemplaardetails** |  |
    | Naam van de virtuele machine | Voer **myVM** in. |
    | Region | Selecteer **(VS) Zuid-Centraal VS**. |
    | Beschikbaarheidsopties | Laat de standaardwaarde **Geen infrastructuurredundantie vereist** staan. |
    | Installatiekopie | Selecteer **Ubuntu Server 18,04 LTS-gen1**. |
    | Grootte | Selecteer **Standard_B2s**. |
    | **Beheerdersaccount** |  |
    | Verificatietype | Selecteer **Wachtwoord**. |
    | Gebruikersnaam | Voer een gebruikersnaam naar keuze in. |
    | Wachtwoord | Voer een wachtwoord naar keuze in. Het wachtwoord moet minstens 12 tekens lang zijn en moet voldoen aan de [gedefinieerde complexiteitsvereisten](../virtual-machines/linux/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    | Wachtwoord bevestigen | Voer het wachtwoord opnieuw in. |
    | **Regels voor binnenkomende poort** |  |
    | Openbare poorten voor inkomend verkeer | Selecteer **Geen**. |
    |||

3. Selecteer **Volgende: Schijven**.

4. Behoud de standaardinstellingen in **Een virtuele machine maken – schijven** en selecteer **Volgende: Netwerken**.

5. Selecteer in **Een virtuele machine maken - Netwerken** de volgende gegevens:

    | Instelling | Waarde |
    | ------- | ----- |
    | Virtueel netwerk | Selecteer **myVMVNet**.  |
    | Subnet | Selecteer het VM-subnet **(10.1.0.0/24)**.|
    | Openbare IP | Handhaaf de standaardinstelling **(new) myVm-ip**. |
    | Openbare poorten voor inkomend verkeer | Selecteer **Geselecteerde poorten toestaan**. |
    | Binnenkomende poorten selecteren | Selecteer **SSH**.|
    ||

6. Selecteer **Controleren + maken**. De pagina **Beoordelen en maken** wordt weergegeven, waar uw configuratie wordt gevalideerd in Azure.

7. Als u het bericht **Validatie geslaagd** ziet, selecteert u **Maken**.

## <a name="deploy-the-firewall"></a>De firewall implementeren

1. Selecteer in het menu van de Azure-portal of op de **startpagina** de optie **Een resource maken**.

2. Typ **firewall** in het zoekvak en druk op **Enter**.

3. Selecteer **Firewall** en vervolgens **Maken**.

4. Gebruik op de pagina **Firewall maken** de volgende tabel om de firewall te configureren:

    | Instelling | Waarde |
    | ------- | ----- |
    | **Projectgegevens** | |
    | Abonnement | Selecteer uw abonnement. |
    | Resourcegroep | Selecteer **myResourceGroup**.  |
    | **Exemplaardetails** |  |
    | Naam | Voer **myAzureFirewall** in. |
    | Region | Selecteer **Zuid-Centraal VS**. |
    | Beschikbaarheidszone | Laat de standaardwaarde **Geen** staan. |
    | Een virtueel netwerk kiezen    |    Selecteer **bestaande gebruiken**.    |
    | Virtueel netwerk    |    Selecteer **myAzFwVNet**.    |
    | Openbaar IP-adres    |    Selecteer **nieuwe toevoegen** en geef **myFirewall-IP** op bij naam.    |
    | Geforceerde tunneling    | De standaard instelling **uitgeschakeld** laten.    |
    |||
5. Selecteer **Controleren + maken**. De pagina **Beoordelen en maken** wordt weergegeven, waar uw configuratie wordt gevalideerd in Azure.

6. Als u het bericht **Validatie geslaagd** ziet, selecteert u **Maken**.

## <a name="enable-firewall-logs"></a>Firewall logboeken inschakelen

In deze sectie schakelt u de logboeken in op de firewall.

1. Selecteer in het Azure Portal **alle resources** in het menu aan de linkerkant.

2. Selecteer de firewall **myAzureFirewall** in de lijst met resources.

3. Onder **bewaking** in de firewall instellingen selecteert u **Diagnostische instellingen**

4. Selecteer **+ Diagnostische instelling toevoegen** in de diagnostische instellingen.

5. Typ of Selecteer in de **instelling diagnostische** gegevens de volgende informatie:

    | Instelling | Waarde |
    | ------- | ----- |
    | Naam van diagnostische instelling | Voer **myDiagSetting** in. |
    | Categorie Details | |
    | logboek | Selecteer **AzureFirewallApplicationRule** en **AzureFirewallNetworkRule**. |
    | Doel Details | Selecteer **verzenden naar log Analytics**. |
    | Abonnement | Selecteer uw abonnement. |
    | Log Analytics-werkruimte | Selecteer uw Log Analytics-werk ruimte. |

6. Selecteer **Opslaan**.

## <a name="create-azure-sql-database"></a>Een Azure SQL-database maken

In deze sectie maakt u een persoonlijke SQL Database.

1. Selecteer in de linkerbovenhoek van het scherm in de Azure Portal **een resource**  >  **databases** maken  >  **SQL database**.

2. Voer in **SQL database basis beginselen maken** de volgende gegevens in of Selecteer deze:

    | Instelling | Waarde |
    | ------- | ----- |
    | **Projectgegevens** | |
    | Abonnement | Selecteer uw abonnement. |
    | Resourcegroep | Selecteer **myResourceGroup**. U hebt deze resourcegroep in de vorige sectie gemaakt.|
    | **Databasedetails** |  |
    | Databasenaam  | Voer **mydatabase** in.  |
    | Server | Selecteer **nieuwe maken** en voer de onderstaande gegevens in.    |
    | Servernaam | Voer **mydbserver** in. Voer een unieke naam in als deze naam wordt gebruikt.   |
    | Aanmeldgegevens van serverbeheerder | Voer een naam in voor uw keuze. |
    | Wachtwoord    |    Voer een wachtwoord naar keuze in.    |
    | Wachtwoord bevestigen | Voer het wachtwoord opnieuw in    |
    | Locatie    | Selecteer **(VS) Zuid-Centraal VS**.    |
    | Elastische SQL-pool wilt gebruiken    | Laat de standaardwaarde **Nee** staan. |
    | Berekening en opslag | Behoud de standaard **Algemeen GEN5, 2 vCores, 32 GB opslag ruimte**. |
    |||

3. Selecteer **Controleren + maken**. De pagina **Beoordelen en maken** wordt weergegeven, waar uw configuratie wordt gevalideerd in Azure.

4. Als u het bericht **Validatie geslaagd** ziet, selecteert u **Maken**.

## <a name="create-private-endpoint"></a>Privé-eindpunt maken

In deze sectie maakt u een persoonlijk eind punt voor de Azure-SQL database in de vorige sectie.

1. Selecteer in het Azure Portal **alle resources** in het menu aan de linkerkant.

2. Selecteer de **mydbserver** van Azure SQL Server in de lijst met Services.  Als u een andere server naam hebt gebruikt, kiest u die naam.

3. Selecteer in de server instellingen **particuliere endpoint-verbindingen** onder **beveiliging**.

4. Selecteer **+ Privé-eindpunt**.

5. In **een persoonlijk eind punt maken**, typt of selecteert u deze informatie op het tabblad **basis beginselen** :

    | Instelling | Waarde |
    | ------- | ----- |
    | **Projectgegevens** | |
    | Abonnement | Selecteer uw abonnement. |
    | Resourcegroep | Selecteer **myResourceGroup**. |
    | **Exemplaardetails** | |
    | Naam | Voer **SQLPrivateEndpoint** in. |
    | Region | Selecteer **(VS) Zuid-Centraal vs.** |

6. Selecteer het tabblad **resource** of selecteer **volgende: resource** aan de onderkant van de pagina.

7. Op het tabblad **resource** voert u de volgende gegevens in of selecteert u deze:

    | Instelling | Waarde |
    | ------- | ----- |
    | Verbindingsmethode | Selecteer **Verbinding maken met een Azure-resource in mijn directory**. |
    | Abonnement | Selecteer uw abonnement. |
    | Resourcetype | Selecteer **Microsoft.Sql/servers**. |
    | Resource | Selecteer **mydbserver** of de naam van de server die u in de vorige stap hebt gemaakt.
    | Stel subresource in | Selecteer **sqlServer**. |

8. Selecteer het tabblad **configuratie** of selecteer **volgende: Configuratie** onder aan de pagina.

9. Voer op het tabblad **configuratie** de volgende gegevens in of Selecteer deze:

    | Instelling | Waarde |
    | ------- | ----- |
    | **Netwerken** | |
    | Virtueel netwerk | Selecteer **myPEVnet**. |
    | Subnet | Selecteer **PrivateEndpointSubnet**. |
    | **Privé-DNS-integratie** | |
    | Integreren met privé-DNS-zone | Selecteer **Ja**. |
    | Abonnement | Selecteer uw abonnement. |
    | Privé-DNS-zones | De standaard **privatelink.database.Windows.net** behouden. |

10. Selecteer het tabblad **controleren + maken** of Selecteer onder aan de pagina **controleren + maken** .

11. Selecteer **Maken**.

12. Nadat het eind punt is gemaakt, selecteert u **firewalls en virtuele netwerken** onder **beveiliging**.

13. Selecteer in **firewalls en virtuele netwerken** **Ja** naast **Azure-Services en-bronnen toestaan om toegang te krijgen tot deze server**.

14. Selecteer **Opslaan**.

## <a name="connect-the-virtual-networks-using-virtual-network-peering"></a>Verbind de virtuele netwerken met behulp van peering op virtueel netwerk

In deze sectie worden virtuele netwerken **myVMVNet** en **myPEVNet** met **myAzFwVNet** verbonden met behulp van peering. Er is geen directe verbinding tussen **myVMVNet** en **myPEVNet**.

1. Voer in de zoek balk van de portal **myAzFwVNet** in.

2. Selecteer **peerings** in het menu **instellingen** en selecteer **+ toevoegen**.

3. Typ of Selecteer in **peering toevoegen** de volgende informatie:

    | Instelling | Waarde |
    | ------- | ----- |
    | Naam van de peering van myAzFwVNet naar extern virtueel netwerk | Voer **myAzFwVNet-to-myVMVNet in**. |
    | **Details van peer** |  |
    | Implementatiemodel voor het virtuele netwerk  | Behoud de standaard **Resource Manager**.  |
    | Ik weet wat mijn Resource-ID is | Schakel dit selectievakje niet in.    |
    | Abonnement | Selecteer uw abonnement.    |
    | Virtueel netwerk | Selecteer **myVMVNet**. |
    | De naam van de peering van het externe virtuele netwerk naar de myAzFwVNet    |    Voer **myVMVNet-to-myAzFwVNet in**.    |
    | **Configuratie** | |
    | **Toegangs instellingen voor het virtuele netwerk configureren** | |
    | Toegang tot het virtuele netwerk vanaf myAzFwVNet toestaan voor een extern virtueel netwerk | Laat de standaardwaarde **Ingeschakeld** staan.    |
    | Toegang tot het virtuele netwerk van het externe virtuele netwerk met myAzFwVNet toestaan    | Laat de standaardwaarde **Ingeschakeld** staan.    |
    | **Instellingen voor doorgestuurd verkeer configureren** | |
    | Doorgestuurd verkeer van extern virtueel netwerk naar myAzFwVNet toestaan    | Selecteer **Ingeschakeld**. |
    | Doorgestuurd verkeer van myAzFwVNet naar extern virtueel netwerk toestaan | Selecteer **Ingeschakeld**. |
    | **Instellingen voor gateway-door Voer configureren** | |
    | Gateway doorvoer toestaan | Schakel dit selectievakje niet in |
    |||

4. Selecteer **OK**.

5. Selecteer **+ Toevoegen**.

6. Typ of Selecteer in **peering toevoegen** de volgende informatie:

    | Instelling | Waarde |
    | ------- | ----- |
    | Naam van de peering van myAzFwVNet naar extern virtueel netwerk | Voer **myAzFwVNet-to-myPEVNet in**. |
    | **Details van peer** |  |
    | Implementatiemodel voor het virtuele netwerk  | Behoud de standaard **Resource Manager**.  |
    | Ik weet wat mijn Resource-ID is | Schakel dit selectievakje niet in.    |
    | Abonnement | Selecteer uw abonnement.    |
    | Virtueel netwerk | Selecteer **myPEVNet**. |
    | De naam van de peering van het externe virtuele netwerk naar de myAzFwVNet    |    Voer **myPEVNet-to-myAzFwVNet in**.    |
    | **Configuratie** | |
    | **Toegangs instellingen voor het virtuele netwerk configureren** | |
    | Toegang tot het virtuele netwerk vanaf myAzFwVNet toestaan voor een extern virtueel netwerk | Laat de standaardwaarde **Ingeschakeld** staan.    |
    | Toegang tot het virtuele netwerk van het externe virtuele netwerk met myAzFwVNet toestaan    | Laat de standaardwaarde **Ingeschakeld** staan.    |
    | **Instellingen voor doorgestuurd verkeer configureren** | |
    | Doorgestuurd verkeer van extern virtueel netwerk naar myAzFwVNet toestaan    | Selecteer **Ingeschakeld**. |
    | Doorgestuurd verkeer van myAzFwVNet naar extern virtueel netwerk toestaan | Selecteer **Ingeschakeld**. |
    | **Instellingen voor gateway-door Voer configureren** | |
    | Gateway doorvoer toestaan | Schakel dit selectievakje niet in |

7. Selecteer **OK**.

## <a name="link-the-virtual-networks-to-the-private-dns-zone"></a>Koppel de virtuele netwerken aan de privé-DNS-zone

In deze sectie koppelt u virtuele netwerken **myVMVNet** en **myAzFwVNet** aan de persoonlijke DNS-zone **privatelink.database.Windows.net** . Deze zone is gemaakt toen het persoonlijke eind punt werd gemaakt. 

De koppeling is vereist voor de virtuele machine en Firewall voor het omzetten van de FQDN van de Data Base naar het adres van het persoonlijke eind punt. De **myPEVNet** voor het virtuele netwerk is automatisch gekoppeld toen het persoonlijke eind punt werd gemaakt.

>[!NOTE]
>Als u de virtuele machines van de VM en firewall niet koppelt aan de privé-DNS-zone, kunnen de VM en de firewall de SQL Server FQDN nog wel omzetten. Ze worden omgezet naar het open bare IP-adres.

1. Voer in de zoek balk van de portal **privatelink. data base** in.

2. Selecteer **privatelink.database.Windows.net** in de zoek resultaten.

3. Selecteer **koppelingen naar virtueel netwerk** onder **instellingen**.

4. Selecteer **+ Toevoegen**

5. Voer in de **koppeling virtueel netwerk toevoegen** de volgende gegevens in of Selecteer deze:

    | Instelling | Waarde |
    | ------- | ----- |
    | Naam van koppeling | Voer **het link-to-myVMVNet in**. |
    | **Details van virtueel netwerk** |  |
    | Ik weet de resource-ID van het virtuele netwerk  | Schakel dit selectievakje niet in.  |
    | Abonnement | Selecteer uw abonnement.    |
    | Virtueel netwerk | Selecteer **myVMVNet**. |
    | **CONFIGURATIE** | |
    | Automatische registratie inschakelen | Schakel dit selectievakje niet in.    |


6. Selecteer **OK**.

## <a name="configure-an-application-rule-with-sql-fqdn-in-azure-firewall"></a>Een toepassings regel met een SQL-FQDN configureren in Azure Firewall

In deze sectie configureert u een toepassings regel voor het toestaan van communicatie tussen **myVM** en het persoonlijke eind punt voor SQL Server **mydbserver.database.Windows.net**. 

Deze regel staat communicatie toe via de firewall die we in de vorige stappen hebben gemaakt.

1. Voer in de zoek balk van de portal **myAzureFirewall** in.

2. Selecteer **myAzureFirewall** in de zoek resultaten.

3. Selecteer **regels** onder **instellingen** in het overzicht van **myAzureFirewall** .

4. Selecteer het tabblad **Verzameling met toepassingsregels**.

5. Selecteer **+ toepassings regel verzameling toevoegen**.

6. In **verzameling toepassings regel toevoegen** voert u de volgende gegevens in of selecteert u deze:

    | Instelling | Waarde |
    | ------- | ----- |
    | Naam | Voer **SQLPrivateEndpoint** in. |
    | Prioriteit | Voer **100** in. |
    | Bewerking | Voer **toestaan** in. |
    | **Regels** |  |
    | **FQDN-tags** | |
    | Name  | Leeg laten.  |
    | Brontype | Wijzig het standaard **IP-adres**.    |
    | Bron | Leeg laten. |
    | FQDN-tags | De standaard instelling **0** behouden. |
    | **Doel-FQDN-naam** | |
    | Name | Voer **SQLPrivateEndpoint** in.    |
    | Brontype | Wijzig het standaard **IP-adres**. |
    | Bron | Voer **10.1.0.0/16** in. |
    | Protocol: poort | Voer **MSSQL: 1433** in. |
    | Doel-FQDN-naam | Voer **mydbserver.database.Windows.net** in. |
    |||

7. Selecteer **Toevoegen**.

## <a name="route-traffic-between-the-virtual-machine-and-private-endpoint-through-azure-firewall"></a>Verkeer tussen de virtuele machine en het persoonlijke eind punt routeren via Azure Firewall

Er is geen peering voor het virtuele netwerk gemaakt tussen virtuele netwerken **myVMVNet** en **myPEVNet**. De **myVM** van de virtuele machine heeft geen route naar het persoonlijke eind punt dat is gemaakt. 

In deze sectie maakt u een route tabel met een aangepaste route. 

De route verzendt verkeer van het **myVM** -subnet naar de adres ruimte van het virtuele netwerk **myPEVNet**, via de Azure firewall.

1. Selecteer in het menu van de Azure-portal of op de **startpagina** de optie **Een resource maken**.

2. Typ **route tabel** in het zoekvak en druk op **Enter**.

3. Selecteer **route tabel** en selecteer vervolgens **maken**.

4. Gebruik op de pagina **route tabel maken** de volgende tabel om de route tabel te configureren:

    | Instelling | Waarde |
    | ------- | ----- |
    | **Projectgegevens** | |
    | Abonnement | Selecteer uw abonnement. |
    | Resourcegroep | Selecteer **myResourceGroup**.  |
    | **Exemplaardetails** |  |
    | Region | Selecteer **Zuid-Centraal VS**. |
    | Name | Voer **het VM-AzureFirewall in**. |
    | Gateway routes door geven | Selecteer **Nee**. |

5. Selecteer **Controleren + maken**. De pagina **Beoordelen en maken** wordt weergegeven, waar uw configuratie wordt gevalideerd in Azure.

6. Als u het bericht **Validatie geslaagd** ziet, selecteert u **Maken**.

7. Zodra de implementatie is voltooid, selecteert **u Ga naar resource**.

8. Selecteer **routes** onder **instellingen**.

9. Selecteer **+ Toevoegen**.

10. Voer op de pagina **route toevoegen** de volgende gegevens in of Selecteer deze:

    | Instelling | Waarde |
    | ------- | ----- |
    | Routenaam | Voer **myVMsubnet-to-privateendpoint in**. |
    | Adresvoorvoegsel | Voer **10.2.0.0/16** in.  |
    | Volgend hoptype | Selecteer **Virtueel apparaat**. |
    | Adres van de volgende hop | Voer **10.0.0.4** in. |

11. Selecteer **OK**.

12. Selecteer **subnetten** onder **instellingen**.

13. Selecteer **+ koppelen**.

14. Op de pagina **subnet koppelen** voert u de volgende gegevens in of selecteert u deze:

    | Instelling | Waarde |
    | ------- | ----- |
    | Virtueel netwerk | Selecteer **myVMVNet**. |
    | Subnet | Selecteer **VM**-subnet.  |

15. Selecteer **OK**.

## <a name="connect-to-the-virtual-machine-from-your-client-computer"></a>Verbinding maken met de virtuele machine vanaf uw client computer

Maak als volgt verbinding met de VM **myVm** van Internet:

1. Voer **myVm-IP** in op de zoek balk van de portal.

2. Selecteer **myVM-IP** in de zoek resultaten.

3. Kopieer of noteer de waarde onder **IP-adres**.

4. Als u met Windows 10 werkt, voert u de volgende opdracht uit met behulp van Power shell. Gebruik voor andere Windows-client versies een SSH-client zoals [putty](https://www.putty.org/):

* Vervang **username** door de gebruikers naam van de beheerder die u hebt opgegeven tijdens het maken van de VM.

* Vervang **IPAddress** door het IP-adres uit de vorige stap.

    ```bash
    ssh username@IPaddress
    ```

5. Voer het wacht woord in dat u hebt opgegeven bij het maken van **myVm**

## <a name="access-sql-server-privately-from-the-virtual-machine"></a>SQL Server privé toegang tot de virtuele machine

In deze sectie maakt u een persoonlijke verbinding met de SQL Database met behulp van het persoonlijke eind punt.

1. Voer `nslookup mydbserver.database.windows.net` in
    
    U ontvangt een bericht dat er ongeveer als volgt uitziet:

    ```bash
    Server:         127.0.0.53
    Address:        127.0.0.53#53

    Non-authoritative answer:
    mydbserver.database.windows.net       canonical name = mydbserver.privatelink.database.windows.net.
    Name:   mydbserver.privatelink.database.windows.net
    Address: 10.2.0.4
    ```

2. Installeer [SQL Server opdracht regel Programma's](/sql/linux/quickstart-install-connect-ubuntu#tools).

3. Voer de volgende opdracht uit om verbinding te maken met de SQL Server. Gebruik de server beheerder en het wacht woord die u hebt gedefinieerd tijdens het maken van de SQL Server in de vorige stappen.

* Vervang door **\<ServerAdmin>** de gebruikers naam van de beheerder die u hebt ingevoerd tijdens het maken van de SQL-Server.

* Vervang door **\<YourPassword>** het beheerders wachtwoord dat u hebt opgegeven tijdens het maken van SQL Server.

    ```bash
    sqlcmd -S mydbserver.database.windows.net -U '<ServerAdmin>' -P '<YourPassword>'
    ```
4. Er wordt een SQL-opdracht prompt weer gegeven bij een geslaagde aanmelding. Voer **Exit** in om het **Sqlcmd** -hulp programma af te sluiten.

5. Sluit de verbinding met **myVM** door **Afsluiten** in te voeren.

## <a name="validate-the-traffic-in-azure-firewall-logs"></a>Het verkeer in Azure Firewall logboeken valideren

1. Selecteer in het Azure Portal **alle resources** en selecteer uw log Analytics werk ruimte.

2. Selecteer **Logboeken** onder **Algemeen** op de pagina log Analytics werk ruimte.

3. Selecteer de knop Blue **aan de slag** .

4. Selecteer in het venster **voorbeeld Query's** **firewalls** onder **alle query's**.

5. Selecteer de knop **uitvoeren** onder **toepassings regel logboek gegevens**.

6. Controleer in de uitvoer van de logboek query of **mydbserver.database.Windows.net** wordt weer gegeven onder **FQDN** en **SQLPrivateEndpoint** wordt vermeld onder **RuleCollection**.

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u klaar bent met het gebruik van de resources, verwijdert u de resource groep en alle resources die deze bevat:

1. Typ **myResourceGroup** in het vak **Zoeken** bovenaan de portal en selecteer **myResourceGroup** in de zoekresultaten.

1. Selecteer **Resourcegroep verwijderen**.

1. Voer **myResourceGroup** in voor **TYP DE RESOURCEGROEPNAAM** en selecteer **Verwijderen**.

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u verschillende scenario's besproken die u kunt gebruiken om het verkeer tussen een virtuele machine en een persoonlijk eind punt te beperken met behulp van Azure Firewall. 

U hebt verbinding gemaakt met de virtuele machine en u kunt een beveiligde verbinding met de data base tot stand gebracht via Azure Firewall privé-koppeling.

Zie [Wat is Azure private endpoint?](private-endpoint-overview.md)voor meer informatie over privé-eind punten.
