---
title: Een persoonlijk eind punt maken voor een beveiligde verbinding
titleSuffix: Azure Cognitive Search
description: Stel een persoonlijk eind punt in een virtueel netwerk in voor een beveiligde verbinding met een Azure Cognitive Search-service.
manager: nitinme
author: markheff
ms.author: maheff
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/16/2021
ms.openlocfilehash: 7445ac5d750ac29d3e6ce466a48e82efd1bcde40
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100545527"
---
# <a name="create-a-private-endpoint-for-a-secure-connection-to-azure-cognitive-search"></a>Een persoonlijk eind punt maken voor een beveiligde verbinding met Azure Cognitive Search

In dit artikel gebruikt u de Azure Portal voor het maken van een nieuw exemplaar van Azure Cognitive Search service dat niet via internet toegankelijk is. Vervolgens configureert u een virtuele Azure-machine in hetzelfde virtuele netwerk en gebruikt u deze om toegang te krijgen tot de zoek service via een persoonlijk eind punt.

Privé-eind punten worden als een afzonderlijke service verzorgd door een [persoonlijke Azure-koppeling](../private-link/private-link-overview.md). Zie de [pagina met prijzen](https://azure.microsoft.com/pricing/details/private-link/)voor meer informatie over de kosten.

U kunt een persoonlijk eind punt maken in de Azure Portal, zoals beschreven in dit artikel. U kunt ook de [beheer rest API versie 2020-03-13](/rest/api/searchmanagement/), [Azure POWERSHELL](/powershell/module/az.search)of [Azure cli](/cli/azure/search)gebruiken.

> [!NOTE]
> Wanneer het service-eind punt privé is, zijn sommige Portal functies uitgeschakeld. U kunt informatie over service niveaus weer geven en beheren, maar index, Indexeer functie en vaardigheidset-informatie is om veiligheids redenen verborgen. Als alternatief voor de portal kunt u de [VS code-extensie](https://aka.ms/vscode-search) gebruiken om te communiceren met de verschillende onderdelen in de service.

## <a name="why-use-a-private-endpoint-for-secure-access"></a>Waarom een persoonlijk eind punt gebruiken voor beveiligde toegang?

[Privé-eind punten](../private-link/private-endpoint-overview.md) voor Azure Cognitive Search een client in een virtueel netwerk in staat stellen om veilig toegang te krijgen tot gegevens in een zoek index via een [persoonlijke koppeling](../private-link/private-link-overview.md). Het persoonlijke eind punt gebruikt een IP-adres uit de [adres ruimte van het virtuele netwerk](../virtual-network/private-ip-addresses.md) voor uw zoek service. Netwerk verkeer tussen de client en de zoek service gaat over het virtuele netwerk en een privé koppeling op het micro soft-backbone-netwerk, waardoor de bloot stelling van het open bare Internet wordt geëlimineerd. Raadpleeg de [sectie Beschik baarheid](../private-link/private-link-overview.md#availability) in de product documentatie voor een lijst met andere PaaS-services die persoonlijke koppelingen ondersteunen.

Met persoonlijke eind punten voor uw zoek service kunt u het volgende doen:

- Alle verbindingen op het open bare eind punt voor uw zoek service blok keren.
- Verbeter de beveiliging van het virtuele netwerk door u in staat te stellen exfiltration van gegevens van het virtuele netwerk te blok keren.
- Maak veilig verbinding met uw zoek service vanuit on-premises netwerken die verbinding maken met het virtuele netwerk via [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md) of [expressroutes waaraan](../expressroute/expressroute-locations.md) met privé-peering.

## <a name="create-the-virtual-network"></a>Het virtuele netwerk maken

In deze sectie maakt u een virtueel netwerk en een subnet voor het hosten van de virtuele machine die wordt gebruikt om toegang te krijgen tot het privé-eind punt van uw zoek service.

1. Selecteer op Azure Portal het tabblad Start **een resource**  >  **netwerk**  >  **virtueel netwerk** maken.

1. Typ of selecteer in **Virtueel netwerk maken** de volgende gegevens:

    | Instelling | Waarde |
    | ------- | ----- |
    | Abonnement | Selecteer uw abonnement|
    | Resourcegroep | Selecteer **nieuwe maken**, Voer *myResourceGroup* in en selecteer **OK** . |
    | Name | *MyVirtualNetwork* invoeren |
    | Region | Selecteer de gewenste regio |
    |||

1. Laat de standaard waarden voor de overige instellingen ongewijzigd. Klik op **beoordeling + maken** en vervolgens op **maken**

## <a name="create-a-search-service-with-a-private-endpoint"></a>Een zoek service met een persoonlijk eind punt maken

In deze sectie maakt u een nieuwe Azure Cognitive Search-service met een persoonlijk eind punt. 

1. Selecteer in de linkerbovenhoek van het scherm in het Azure Portal **een resource maken**  >  **Web**  >  **Azure Cognitive Search**.

1. In **nieuwe Search service basis principes** voert u de volgende gegevens in of selecteert u deze:

    | Instelling | Waarde |
    | ------- | ----- |
    | **PROJECTGEGEVENS** | |
    | Abonnement | Selecteer uw abonnement. |
    | Resourcegroep | Selecteer **myResourceGroup**. U hebt deze in de vorige sectie gemaakt.|
    | **EXEMPLAARDETAILS** |  |
    | URL | Voer een unieke naam in. |
    | Locatie | Selecteer de gewenste regio. |
    | Prijscategorie | Selecteer **prijs categorie wijzigen** en kies de gewenste servicelaag. (Niet ondersteund op de laag **gratis** . Moet **Basic** of hoger zijn.) |
    |||
  
1. Selecteer **volgende: schalen**.

1. Wijzig de waarden als standaard en selecteer **volgende: netwerken**.

1. Selecteer in **nieuwe Search service netwerken** **persoonlijke** voor **endpoint Connectivity (gegevens)**.

1. In **nieuwe Search service-netwerken** selecteert u **+ toevoegen** onder **persoonlijk eind punt**. 

1. Voer in **persoonlijk eind punt maken** de volgende gegevens in of Selecteer deze:

    | Instelling | Waarde |
    | ------- | ----- |
    | Abonnement | Selecteer uw abonnement. |
    | Resourcegroep | Selecteer **myResourceGroup**. U hebt deze in de vorige sectie gemaakt.|
    | Locatie | Selecteer **VS - west**.|
    | Naam | Voer *myPrivateEndpoint* in.  |
    | Stel subresource in | De standaard **searchService** behouden. |
    | **NETWERKEN** |  |
    | Virtueel netwerk  | Selecteer *MyVirtualNetwork* in de resource groep *myResourceGroup*. |
    | Subnet | Selecteer *mySubnet*. |
    | **INTEGRATIE VAN PRIVÉ-DNS** |  |
    | Integreren met privé-DNS-zone  | Laat de standaardwaarde **Ja** staan. |
    | Privé-DNS-zone  | Wijzig de standaard waarde * * (nieuw) privatelink.search.windows.net * *. |
    |||

1. Selecteer **OK**. 

1. Selecteer **Controleren + maken**. De pagina **Beoordelen en maken** wordt weergegeven, waar uw configuratie wordt gevalideerd in Azure. 

1. Als u het bericht **Validatie geslaagd** ziet, selecteert u **Maken**. 

1. Zodra de inrichting van de nieuwe service is voltooid, bladert u naar de resource die u zojuist hebt gemaakt.

1. Selecteer **sleutels** in het menu links.

1. Kopieer de **primaire Administrator sleutel** voor later, wanneer u verbinding maakt met de service.

## <a name="create-a-virtual-machine"></a>Een virtuele machine maken

1. Selecteer in de linkerbovenhoek van het scherm in het Azure Portal **een**  >    >  **virtuele machine** voor het berekenen van een resource maken.

1. Typ of selecteer in **Een virtuele machine maken - Basisprincipes** de volgende gegevens:

    | Instelling | Waarde |
    | ------- | ----- |
    | **PROJECTGEGEVENS** | |
    | Abonnement | Selecteer uw abonnement. |
    | Resourcegroep | Selecteer **myResourceGroup**. U hebt deze in de vorige sectie gemaakt.  |
    | **EXEMPLAARDETAILS** |  |
    | Naam van de virtuele machine | Voer *myVm* in. |
    | Regio | Selecteer **VS-West** of de regio die u gebruikt. |
    | Beschikbaarheidsopties | Laat de standaardwaarde **Geen infrastructuurredundantie vereist** staan. |
    | Installatiekopie | Selecteer **Windows Server 2019 Datacenter**. |
    | Grootte | Laat de standaardwaarde **Standard DS1 v2** staan. |
    | **ADMINISTRATOR-ACCOUNT** |  |
    | Gebruikersnaam | Voer een gebruikersnaam naar keuze in. |
    | Wachtwoord | Voer een wachtwoord naar keuze in. Het wachtwoord moet minstens 12 tekens lang zijn en moet voldoen aan de [gedefinieerde complexiteitsvereisten](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    | Wachtwoord bevestigen | Voer het wachtwoord opnieuw in. |
    | **REGELS VOOR BINNENKOMENDE POORT** |  |
    | Openbare poorten voor inkomend verkeer | Laat de standaard **geselecteerde poorten toestaan**. |
    | Binnenkomende poorten selecteren | Behoud de standaard **-RDP (3389)**. |
    | **GELD BESPAREN** |  |
    | Hebt u al een Windows-licentie? | Laat de standaardwaarde **Nee** staan. |
    |||

1. Selecteer **Volgende: Schijven**.

1. Behoud de standaardinstellingen in **Een virtuele machine maken – schijven** en selecteer **Volgende: Netwerken**.

1. Selecteer in **Een virtuele machine maken - Netwerken** de volgende gegevens:

    | Instelling | Waarde |
    | ------- | ----- |
    | Virtueel netwerk | Laat de standaardwaarde **MyVirtualNetwork** staan.  |
    | Adresruimte | Laat de standaardwaarde **10.1.0.0/24** staan.|
    | Subnet | Laat de standaardwaarde **mySubnet (10.1.0.0/24)** staan.|
    | Openbare IP | Handhaaf de standaardinstelling **(new) myVm-ip**. |
    | Openbare poorten voor inkomend verkeer | Selecteer **Geselecteerde poorten toestaan**. |
    | Binnenkomende poorten selecteren | Selecteer **HTTP** en **RDP**.|
    ||

   > [!NOTE]
   > IPv4-adressen kunnen worden uitgedrukt in [CIDR](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) -indeling. Vermijd het IP-bereik dat is gereserveerd voor particuliere netwerken, zoals beschreven in [RFC 1918](https://tools.ietf.org/html/rfc1918):
   >
   > - `10.0.0.0 - 10.255.255.255  (10/8 prefix)`
   > - `172.16.0.0 - 172.31.255.255  (172.16/12 prefix)`
   > - `192.168.0.0 - 192.168.255.255 (192.168/16 prefix)`

1. Selecteer **Controleren + maken**. De pagina **Beoordelen en maken** wordt weergegeven, waar uw configuratie wordt gevalideerd in Azure.

1. Als u het bericht **Validatie geslaagd** ziet, selecteert u **Maken**. 

## <a name="connect-to-the-vm"></a>Verbinding maken met de virtuele machine

Down load en maak vervolgens als volgt verbinding met de VM- *myVm* :

1. Voer in de zoekbalk van de portal *myVm* in.

1. Selecteer de knop **Verbinding maken**. Na het selecteren van de knop **Verbinden** wordt **Verbinden met virtuele machine** geopend.

1. Selecteer **RDP-bestand downloaden**. In Azure wordt een *RDP*-bestand (Remote Desktop Protocol) gemaakt en het bestand wordt gedownload naar de computer.

1. Open het downloaded.rdp*-bestand.

    1. Selecteer **Verbinding maken** wanneer hierom wordt gevraagd.

    1. Voer de gebruikersnaam en het wachtwoord in die u hebt opgegeven bij het maken van de virtuele machine.

        > [!NOTE]
        > Mogelijk moet u **Meer opties** > **Een ander account gebruiken** selecteren om de referenties op te geven die u hebt ingevoerd tijdens het maken van de VM.

1. Selecteer **OK**.

1. Er wordt mogelijk een certificaatwaarschuwing weergegeven tijdens het aanmelden. Als er een certificaatwaarschuwing wordt weergegeven, selecteert u **Ja** of **Doorgaan**.

1. Wanneer het VM-bureaublad wordt weergegeven, minimaliseert u het om terug te gaan naar het lokale bureaublad.  

## <a name="test-connections"></a>Verbindingen testen

In deze sectie gaat u de persoonlijke netwerk toegang tot de zoek service controleren en een persoonlijke verbinding maken met het gebruik van het persoonlijke eind punt.

Wanneer het eind punt van de zoek service privé is, zijn sommige Portal functies uitgeschakeld. U kunt instellingen voor het service niveau weer geven en beheren, maar de toegang tot de portal voor het indexeren van gegevens en verschillende andere onderdelen in de service, zoals de definities index, Indexer en vaardig heden, is beperkt om veiligheids redenen.

1. Open PowerShell in het extern bureaublad van *myVM*.

1. Voer ' nslookup [naam zoek service ']. Search. Windows. net ' in

    U ontvangt een bericht dat er ongeveer als volgt uitziet:
    ```azurepowershell
    Server:  UnKnown
    Address:  168.63.129.16
    Non-authoritative answer:
    Name:    [search service name].privatelink.search.windows.net
    Address:  10.0.0.5
    Aliases:  [search service name].search.windows.net
    ```

1. Maak vanuit de virtuele machine verbinding met de zoek service om een index te maken. U kunt deze [Snelstartgids](search-get-started-rest.md) volgen om een nieuwe zoek index in uw service te maken met behulp van de rest API. Voor het instellen van aanvragen van een web API-test programma is het Search service-eind punt (https://[Zoek service naam]. Search. Windows. net) en de beheer-API-sleutel die u in een vorige stap hebt gekopieerd.

1. Het volt ooien van de Quick Start van de VM is uw bevestiging dat de service volledig operationeel is.

1. Sluit de externe bureaubladverbinding met *myVM*. 

1. Als u wilt controleren of uw service niet toegankelijk is op een openbaar eind punt, opent u postman op uw lokale werk station en voert u de eerste verschillende taken in de Quick Start uit. Als er een fout bericht wordt weer gegeven dat de externe server niet bestaat, hebt u een persoonlijk eind punt geconfigureerd voor uw zoek service.

## <a name="clean-up-resources"></a>Resources opschonen 
Wanneer u klaar bent met het persoonlijke eind punt, de zoek service en de virtuele machine, verwijdert u de resource groep en alle resources die deze bevat:
1. Voer  *myResourceGroup*   in het **zoekvak** boven aan de portal in en selecteer  *myResourceGroup*   in de zoek resultaten. 
1. Selecteer **Resourcegroep verwijderen**. 
1. Voer  *myResourceGroup*   in bij **Typ de naam van de resource groep** en selecteer **verwijderen**.

## <a name="next-steps"></a>Volgende stappen
In dit artikel hebt u een VM gemaakt in een virtueel netwerk en een zoek service met een persoonlijk eind punt. U hebt verbinding gemaakt met de virtuele machine via internet en u kunt deze veilig door gegeven aan de zoek service met behulp van een persoonlijke koppeling. Zie [Wat is Azure private endpoint?](../private-link/private-endpoint-overview.md)voor meer informatie over privé-eind punten.
