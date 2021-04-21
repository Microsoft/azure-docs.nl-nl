---
title: Een Azure-netwerkinterface maken, wijzigen of verwijderen
titlesuffix: Azure Virtual Network
description: Ontdek wat een netwerkinterface is en hoe u instellingen voor maakt, wijzigt en verwijdert.
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 1/22/2020
ms.author: kumud
ms.openlocfilehash: 8003bf14bcade08f36a7877fdb3a53998aff9e63
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107773063"
---
# <a name="create-change-or-delete-a-network-interface"></a>Netwerkinterface maken, wijzigen of verwijderen

Meer informatie over het maken, wijzigen van instellingen voor en het verwijderen van een netwerkinterface. Met een netwerkinterface kan een virtuele Azure-machine communiceren met internet-, Azure- en on-premises resources. Wanneer u een virtuele machine maakt met behulp Azure Portal, maakt de portal één netwerkinterface met standaardinstellingen. U kunt er in plaats daarvan voor kiezen om netwerkinterfaces met aangepaste instellingen te maken en een of meer netwerkinterfaces toe te voegen aan een virtuele machine wanneer u deze maakt. U kunt ook de standaardinstellingen voor de netwerkinterface voor een bestaande netwerkinterface wijzigen. In dit artikel wordt uitgelegd hoe u een netwerkinterface maakt met aangepaste instellingen, bestaande instellingen wijzigt, zoals toewijzing van netwerkfilters (netwerkbeveiligingsgroep), subnettoewijzing, DNS-serverinstellingen en doorsturen via IP, en een netwerkinterface verwijdert.

Zie IP-adressen beheren als u IP-adressen wilt toevoegen, wijzigen of verwijderen voor een [netwerkinterface.](virtual-network-network-interface-addresses.md) Zie Netwerkinterfaces toevoegen of verwijderen als u netwerkinterfaces wilt toevoegen aan of verwijderen uit [virtuele machines.](virtual-network-network-interface-vm.md)

## <a name="before-you-begin"></a>Voordat u begint

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Voltooi de volgende taken voordat u de stappen in een van de secties van dit artikel uitvoert:

- Als u nog geen Azure-account hebt, kunt u zich aanmelden voor een [gratis proefaccount.](https://azure.microsoft.com/free)
- Als u de portal gebruikt, opent https://portal.azure.com u en meld u zich aan met uw Azure-account.
- Als u PowerShell-opdrachten gebruikt om taken in dit artikel uit te voeren, voert u de opdrachten uit in de [Azure Cloud Shell](https://shell.azure.com/powershell)of door PowerShell uit te voeren vanaf uw computer. Azure Cloud Shell is een gratis interactieve shell waarmee u de stappen in dit artikel kunt uitvoeren. In deze shell zijn algemene Azure-hulpprogramma's vooraf geïnstalleerd en geconfigureerd voor gebruik met uw account. Voor deze zelfstudie is Azure PowerShell moduleversie 1.0.0 of hoger vereist. Voer `Get-Module -ListAvailable Az` uit om te kijken welke versie is geïnstalleerd. Als u PowerShell wilt upgraden, raadpleegt u [De Azure PowerShell-module installeren](/powershell/azure/install-az-ps). Als u PowerShell lokaal uitvoert, moet u ook `Connect-AzAccount` uitvoeren om verbinding te kunnen maken met Azure.
- Als u azure CLI-opdrachten (Opdrachtregelinterface) gebruikt om taken in dit artikel uit te voeren, voert u de opdrachten uit in [de Azure Cloud Shell](https://shell.azure.com/bash)of door de CLI uit te voeren vanaf uw computer. Voor deze zelfstudie is azure CLI versie 2.0.28 of hoger vereist. Voer `az --version` uit om te kijken welke versie is geïnstalleerd. Zie [Azure CLI installeren](/cli/azure/install-azure-cli) als u de CLI wilt installeren of een upgrade wilt uitvoeren. Als u de Azure CLI lokaal gebruikt, moet u ook uitvoeren om `az login` een verbinding met Azure te maken.

Het account dat u aanmeldt of verbinding maakt met [](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) Azure, moet worden [](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) toegewezen aan de rol Netwerkbijdrager of aan een aangepaste rol die de juiste acties krijgt toegewezen die worden vermeld in [Machtigingen.](#permissions)

## <a name="create-a-network-interface"></a>Een netwerkinterface maken

Wanneer u een virtuele machine maakt met Azure Portal, maakt de portal een netwerkinterface met standaardinstellingen voor u. Als u liever al uw netwerkinterface-instellingen opgeeft, kunt u een netwerkinterface met aangepaste instellingen maken en de netwerkinterface koppelen aan een virtuele machine bij het maken van de virtuele machine (met behulp van PowerShell of de Azure CLI). U kunt ook een netwerkinterface maken en deze toevoegen aan een bestaande virtuele machine (met behulp van PowerShell of de Azure CLI). Zie Netwerkinterfaces toevoegen of verwijderen voor meer informatie over het maken van een virtuele machine met een bestaande netwerkinterface of om netwerkinterfaces toe te voegen aan of te verwijderen uit bestaande virtuele [machines.](virtual-network-network-interface-vm.md) Voordat u een netwerkinterface maakt, moet u een bestaand virtueel netwerk [hebben](manage-virtual-network.md) op dezelfde locatie en abonnement waarin u een netwerkinterface maakt.

1. Typ in het vak met de tekst *Resources* zoeken bovenaan de Azure Portal *netwerkinterfaces.* Wanneer **netwerkinterfaces** worden weergegeven in de zoekresultaten, selecteert u deze.
2. Selecteer **+ Toevoegen onder** **Netwerkinterfaces.**
3. Voer waarden in of selecteer waarden voor de volgende instellingen en selecteer vervolgens **Maken:**

    |Instelling|Vereist?|Details|
    |---|---|---|
    |Name|Yes|De naam moet uniek zijn binnen de resourcegroep die u selecteert. Na een periode hebt u waarschijnlijk verschillende netwerkinterfaces in uw Azure-abonnement. Zie Naamconventies voor suggesties bij het maken van een naamconventie om het beheer van verschillende netwerkinterfaces [te vereenvoudigen.](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging#resource-naming) De naam kan niet worden gewijzigd nadat de netwerkinterface is gemaakt.|
    |Virtueel netwerk|Yes|Selecteer het virtuele netwerk voor de netwerkinterface. U kunt alleen een netwerkinterface toewijzen aan een virtueel netwerk dat zich in hetzelfde abonnement en dezelfde locatie bevindt als de netwerkinterface. Zodra een netwerkinterface is gemaakt, kunt u het virtuele netwerk dat is toegewezen niet meer wijzigen. De virtuele machine waar u de netwerkinterface aan toevoegt, moet zich ook op dezelfde locatie en hetzelfde abonnement bevinden als de netwerkinterface.|
    |Subnet|Yes|Selecteer een subnet in het virtuele netwerk dat u hebt geselecteerd. U kunt het subnet wijzigen aan wie de netwerkinterface is toegewezen nadat deze is gemaakt.|
    |Toewijzing van privé-IP-adres|Yes| In deze instelling kiest u de toewijzingsmethode voor het IPv4-adres. Kies uit de volgende toewijzingsmethoden: **Dynamisch:** wanneer u deze optie selecteert, wijst Azure automatisch het volgende beschikbare adres toe uit de adresruimte van het subnet dat u hebt geselecteerd. **Statisch:** Wanneer u deze optie selecteert, moet u handmatig een beschikbaar IP-adres toewijzen vanuit de adresruimte van het subnet dat u hebt geselecteerd. Statische en dynamische adressen worden pas gewijzigd als u ze wijzigt of de netwerkinterface wordt verwijderd. U kunt de toewijzingsmethode wijzigen nadat de netwerkinterface is gemaakt. De Azure DHCP-server wijst dit adres toe aan de netwerkinterface binnen het besturingssysteem van de virtuele machine.|
    |Netwerkbeveiligingsgroep|No| Laat ingesteld op **Geen,** selecteer een bestaande [netwerkbeveiligingsgroep](./network-security-groups-overview.md)of [maak een netwerkbeveiligingsgroep.](tutorial-filter-network-traffic.md) Met netwerkbeveiligingsgroepen kunt u netwerkverkeer in en uit een netwerkinterface filteren. U kunt nul of één netwerkbeveiligingsgroep toepassen op een netwerkinterface. Er kan ook nul of één netwerkbeveiligingsgroep worden toegepast op het subnet waar de netwerkinterface aan is toegewezen. Wanneer een netwerkbeveiligingsgroep wordt toegepast op een netwerkinterface en het subnet waar de netwerkinterface aan is toegewezen, treden soms onverwachte resultaten op. Zie Problemen met netwerkbeveiligingsgroepen oplossen voor problemen met netwerkbeveiligingsgroepen die zijn toegepast op netwerkinterfaces [en subnetten.](diagnose-network-traffic-filter-problem.md)|
    |Abonnement|Yes|Selecteer een van uw [Azure-abonnementen.](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription) De virtuele machine waar u een netwerkinterface aan koppelt en het virtuele netwerk waar u deze mee verbindt, moet in hetzelfde abonnement aanwezig zijn.|
    |Privé-IP-adres (IPv6)|No| Als u dit selectievakje in selecteert, wordt een IPv6-adres toegewezen aan de netwerkinterface, naast het IPv4-adres dat is toegewezen aan de netwerkinterface. Zie de sectie IPv6 van dit artikel voor belangrijke informatie over het gebruik van IPv6 met netwerkinterfaces. U kunt geen toewijzingsmethode selecteren voor het IPv6-adres. Als u ervoor kiest om een IPv6-adres toe te wijzen, wordt dit toegewezen met de dynamische methode.
    |IPv6-naam (wordt alleen weergegeven wanneer het selectievakje **Privé-IP-adres (IPv6)** is ingeschakeld) |Ja, als het **selectievakje Privé IP-adres (IPv6)** is ingeschakeld.| Deze naam wordt toegewezen aan een secundaire IP-configuratie voor de netwerkinterface. Zie Instellingen voor netwerkinterface weergeven voor meer informatie [over IP-configuraties.](#view-network-interface-settings)|
    |Resourcegroep|Yes|Selecteer een bestaande [resourcegroep of](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group) maak er een. Een netwerkinterface kan bestaan in dezelfde of een andere resourcegroep dan de virtuele machine waar u deze aan koppelt of het virtuele netwerk waar u verbinding mee maakt.|
    |Locatie|Ja|De virtuele machine waar u een netwerkinterface aan koppelt en het virtuele netwerk waar u deze mee [verbindt,](https://azure.microsoft.com/regions)moet zich op dezelfde locatie bevinden, ook wel een regio genoemd.|

De portal biedt niet de mogelijkheid om een openbaar IP-adres toe te wijzen aan de netwerkinterface wanneer u het maakt, maar de portal maakt wel een openbaar IP-adres en wijst dit toe aan een netwerkinterface wanneer u een virtuele machine maakt met behulp van de portal. Zie IP-adressen beheren voor meer informatie over het toevoegen van een openbaar IP-adres aan de netwerkinterface nadat het [is gemaakt.](virtual-network-network-interface-addresses.md) Als u een netwerkinterface met een openbaar IP-adres wilt maken, moet u de CLI of PowerShell gebruiken om de netwerkinterface te maken.

De portal biedt niet de mogelijkheid om de netwerkinterface toe te wijzen aan toepassingsbeveiligingsgroepen bij het maken van een netwerkinterface, maar de Azure CLI en PowerShell wel. U kunt echter een bestaande netwerkinterface toewijzen aan een toepassingsbeveiligingsgroep via de portal, zolang de netwerkinterface is gekoppeld aan een virtuele machine. Zie Toevoegen aan of verwijderen uit toepassingsbeveiligingsgroepen voor meer informatie over het toewijzen van een netwerkinterface aan een [toepassingsbeveiligingsgroep.](#add-to-or-remove-from-application-security-groups)

>[!Note]
> Azure wijst pas een MAC-adres toe aan de netwerkinterface nadat de netwerkinterface is gekoppeld aan een virtuele machine en de virtuele machine de eerste keer wordt gestart. U kunt het MAC-adres dat azure toewijst aan de netwerkinterface niet opgeven. Het MAC-adres blijft toegewezen aan de netwerkinterface totdat de netwerkinterface wordt verwijderd of het privé-IP-adres dat is toegewezen aan de primaire IP-configuratie van de primaire netwerkinterface wordt gewijzigd. Zie IP-adressen beheren voor meer informatie over [IP-adressen en IP-configuraties](virtual-network-network-interface-addresses.md)

[!INCLUDE [ephemeral-ip-note.md](../../includes/ephemeral-ip-note.md)]

**Opdrachten**

|Hulpprogramma|Opdracht|
|---|---|
|CLI|[az network nic create](/cli/azure/network/nic)|
|PowerShell|[New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface)|

## <a name="view-network-interface-settings"></a>Netwerkinterface-instellingen weergeven

U kunt de meeste instellingen voor een netwerkinterface weergeven en wijzigen nadat deze is gemaakt. In de portal wordt het DNS-achtervoegsel of het lidmaatschap van de toepassingsbeveiligingsgroep voor de netwerkinterface niet weergegeven. U kunt de PowerShell- of Azure CLI-opdrachten gebruiken [om](#view-settings-commands) het DNS-achtervoegsel en het lidmaatschap van de toepassingsbeveiligingsgroep weer te geven.

1. Typ in het vak met de tekst *Resources* zoeken bovenaan de Azure Portal *netwerkinterfaces.* Wanneer **netwerkinterfaces** worden weergegeven in de zoekresultaten, selecteert u deze.
2. Selecteer in de lijst de netwerkinterface voor wie u de instellingen wilt weergeven of wijzigen.
3. De volgende items worden weergegeven voor de netwerkinterface die u hebt geselecteerd:
   - **Overzicht:** Bevat informatie over de netwerkinterface, zoals de IP-adressen die eraan zijn toegewezen, het virtuele netwerk/subnet waar de netwerkinterface aan is toegewezen en de virtuele machine waar de netwerkinterface aan is gekoppeld (als deze is gekoppeld aan een netwerkinterface). In de volgende afbeelding ziet u de overzichtsinstellingen voor een netwerkinterface met de **naam mywebserver256:** ![ Overzicht van de netwerkinterface](./media/virtual-network-network-interface/nic-overview.png)

     U kunt een netwerkinterface verplaatsen naar een andere resourcegroep of een ander abonnement door **naast** de **resourcegroep** of abonnementsnaam te selecteren ( **wijzigen ).** Als u de netwerkinterface naar een nieuw abonnement verplaatst, moet u alle resources verplaatsen die betrekking hebben op de netwerkinterface. Als de netwerkinterface bijvoorbeeld is gekoppeld aan een virtuele machine, moet u ook de virtuele machine en andere aan virtuele machines gerelateerde resources verplaatsen. Zie Resource verplaatsen naar een nieuwe resourcegroep of een nieuw abonnement als u een [netwerkinterface wilt verplaatsen.](../azure-resource-manager/management/move-resource-group-and-subscription.md?toc=%2fazure%2fvirtual-network%2ftoc.json#use-the-portal) Het artikel bevat een lijst met vereisten en informatie over het verplaatsen van resources met behulp van Azure Portal, PowerShell en de Azure CLI.
   - **IP-configuraties:** Openbare en persoonlijke IPv4- en IPv6-adressen die zijn toegewezen aan IP-configuraties worden hier vermeld. Als een IPv6-adres is toegewezen aan een IP-configuratie, wordt het adres niet weergegeven. Zie IP-adressen configureren voor een Azure-netwerkinterface voor meer informatie over IP-configuraties en het toevoegen en verwijderen van [IP-adressen.](virtual-network-network-interface-addresses.md) Doorsturen via IP en subnettoewijzing zijn ook geconfigureerd in deze sectie. Zie Doorsturen via [IP](#enable-or-disable-ip-forwarding) in- of uitschakelen en Subnettoewijzing wijzigen voor meer informatie over [deze instellingen.](#change-subnet-assignment)
   - **DNS-servers:** U kunt opgeven welke DNS-server een netwerkinterface wordt toegewezen door de Azure DHCP-servers. De netwerkinterface kan de instelling overnemen van het virtuele netwerk waar de netwerkinterface aan is toegewezen, of een aangepaste instelling hebben die de instelling overschrijven voor het virtuele netwerk dat aan de interface is toegewezen. Zie [DNS-servers](#change-dns-servers)wijzigen als u wilt wijzigen wat er wordt weergegeven.
   - **Netwerkbeveiligingsgroep (NSG):** Geeft weer welke NSG is gekoppeld aan de netwerkinterface (indien van u). Een NSG bevat binnenkomende en uitgaande regels om netwerkverkeer voor de netwerkinterface te filteren. Als een NSG is gekoppeld aan de netwerkinterface, wordt de naam van de bijbehorende NSG weergegeven. Zie Een netwerkbeveiligingsgroep koppelen of ontkoppelen als u wilt wijzigen wat er [wordt weergegeven.](#associate-or-dissociate-a-network-security-group)
   - **Eigenschappen:** Geeft belangrijke instellingen weer over de netwerkinterface, inclusief het MAC-adres (leeg als de netwerkinterface niet is gekoppeld aan een virtuele machine) en het abonnement waarin deze zich bevindt.
   - **Effectieve beveiligingsregels:**  Beveiligingsregels worden weergegeven als de netwerkinterface is gekoppeld aan een virtuele machine die wordt uitgevoerd en een NSG is gekoppeld aan de netwerkinterface, het subnet dat eraan is toegewezen of beide. Zie Effectieve beveiligingsregels weergeven voor meer informatie over [wat er wordt weergegeven.](#view-effective-security-rules) Zie Netwerkbeveiligingsgroepen voor meer informatie [over NSG's.](./network-security-groups-overview.md)
   - **Effectieve routes:** Routes worden weergegeven als de netwerkinterface is gekoppeld aan een virtuele machine die wordt uitgevoerd. De routes zijn een combinatie van de standaardroutes van Azure, door de gebruiker gedefinieerde routes en eventuele BGP-routes die kunnen bestaan voor het subnet waar de netwerkinterface aan is toegewezen. Zie Effectieve routes weergeven voor meer informatie over [wat wordt weergegeven.](#view-effective-routes) Zie Routeringsoverzicht voor meer informatie over standaardroutes van Azure en door de gebruiker [gedefinieerde routes.](virtual-networks-udr-overview.md)
Algemene Azure Resource Manager instellingen: zie Activiteitenlogboek, Toegangsbeheer [](../azure-monitor/essentials/platform-logs-overview.md) [(IAM),](../role-based-access-control/overview.md) [Tags,](../azure-resource-manager/management/tag-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)Vergrendelingen en [](../azure-resource-manager/management/lock-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)Automation-script voor meer informatie over algemene [Azure Resource Manager-instellingen.](../azure-resource-manager/templates/export-template-portal.md)

<a name="view-settings-commands"></a>**Opdrachten**

Als een IPv6-adres is toegewezen aan een netwerkinterface, retourneert de PowerShell-uitvoer het feit dat het adres is toegewezen, maar retourneert het niet het toegewezen adres. Op dezelfde manier retourneert de CLI het feit dat het adres is toegewezen, maar retourneert *null* in de uitvoer voor het adres.

|Hulpprogramma|Opdracht|
|---|---|
|CLI|[az network nic list om](/cli/azure/network/nic) netwerkinterfaces in het abonnement weer te bieden; [az network nic show om](/cli/azure/network/nic) instellingen voor een netwerkinterface weer te geven|
|PowerShell|[Get-AzNetworkInterface voor](/powershell/module/az.network/get-aznetworkinterface) het weergeven van netwerkinterfaces in het abonnement of de instellingen voor een netwerkinterface|

## <a name="change-dns-servers"></a>DNS-servers wijzigen

De DNS-server wordt door de Azure DHCP-server toegewezen aan de netwerkinterface binnen het besturingssysteem van de virtuele machine. De toegewezen DNS-server is ongeacht de DNS-serverinstelling voor een netwerkinterface. Zie Naamresolutie voor virtuele machines voor meer informatie over instellingen voor naamresolutie [voor een netwerkinterface.](virtual-networks-name-resolution-for-vms-and-role-instances.md) De netwerkinterface kan de instellingen overnemen van het virtuele netwerk of de eigen unieke instellingen gebruiken die de instelling voor het virtuele netwerk overschrijven.

1. Typ in het vak met de tekst *Resources* zoeken bovenaan de Azure Portal *netwerkinterfaces.* Wanneer **netwerkinterfaces** worden weergegeven in de zoekresultaten, selecteert u deze.
2. Selecteer in de lijst de netwerkinterface voor wie u een DNS-server wilt wijzigen.
3. Selecteer **DNS-servers** onder **INSTELLINGEN.**
4. Selecteer een van de volgende opties:
   - **Overnemen van virtueel netwerk: kies** deze optie om de DNS-serverinstelling over te nemen die is gedefinieerd voor het virtuele netwerk waar de netwerkinterface aan is toegewezen. Op virtueel netwerkniveau wordt een aangepaste DNS-server of de door Azure geleverde DNS-server gedefinieerd. De door Azure geleverde DNS-server kan hostnamen oplossen voor resources die zijn toegewezen aan hetzelfde virtuele netwerk. FQDN moet worden gebruikt voor het oplossen van resources die zijn toegewezen aan verschillende virtuele netwerken.
   - **Aangepast:** u kunt uw eigen DNS-server configureren voor het oplossen van namen in meerdere virtuele netwerken. Voer het IP-adres in van de server die u wilt gebruiken als DNS-server. Het DNS-serveradres dat u opgeeft, wordt alleen toegewezen aan deze netwerkinterface en overschrijvingen dns-instellingen voor het virtuele netwerk waar de netwerkinterface aan is toegewezen.
     >[!Note]
     >Als de VM een NIC gebruikt die deel uitmaakt van een beschikbaarheidsset, worden alle DNS-servers overgenomen die zijn opgegeven voor elk van de VM's van alle NIC's die deel uitmaken van de beschikbaarheidsset.
5. Selecteer **Opslaan**.

**Opdrachten**

|Hulpprogramma|Opdracht|
|---|---|
|CLI|[az network nic update](/cli/azure/network/nic)|
|PowerShell|[Set-AzNetworkInterface](/powershell/module/az.network/set-aznetworkinterface)|

## <a name="enable-or-disable-ip-forwarding"></a>Doorsturen via IP in- of uitschakelen

Doorsturen via IP maakt het mogelijk om de virtuele machine aan een netwerkinterface te koppelen:
- Ontvang netwerkverkeer dat niet is bestemd voor een van de IP-adressen die zijn toegewezen aan een van de IP-configuraties die zijn toegewezen aan de netwerkinterface.
- Netwerkverkeer verzenden met een ander bron-IP-adres dan het adres dat is toegewezen aan een van de IP-configuraties van een netwerkinterface.

De instelling moet zijn ingeschakeld voor elke netwerkinterface die is gekoppeld aan de virtuele machine die verkeer ontvangt dat de virtuele machine moet doorsturen. Een virtuele machine kan verkeer doorsturen, ongeacht of er meerdere netwerkinterfaces of één netwerkinterface aan zijn gekoppeld. Hoewel doorsturen via IP een Azure-instelling is, moet op de virtuele machine ook een toepassing worden uitgevoerd die het verkeer kan doorsturen, zoals firewall, WAN-optimalisatie en toepassingen voor taakverdeling. Wanneer op een virtuele machine netwerktoepassingen worden uitgevoerd, wordt de virtuele machine vaak een virtueel netwerkapparaat genoemd. U kunt een lijst weergeven met gereed voor het implementeren van virtuele netwerkapparaten in [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking?page=1&subcategories=appliances). Doorsturen via IP wordt doorgaans gebruikt met door de gebruiker gedefinieerde routes. Zie Door de gebruiker gedefinieerde routes voor meer informatie over door de gebruiker [gedefinieerde routes.](virtual-networks-udr-overview.md)

1. Typ in het vak met de tekst *Resources* zoeken bovenaan de Azure Portal *netwerkinterfaces.* Wanneer **netwerkinterfaces** worden weergegeven in de zoekresultaten, selecteert u deze.
2. Selecteer de netwerkinterface voor wie u doorsturen via IP wilt in- of uitschakelen.
3. Selecteer **IP-configuraties** in de **sectie** INSTELLINGEN.
4. Selecteer **Ingeschakeld of** **Uitgeschakeld** (standaardinstelling) om de instelling te wijzigen.
5. Selecteer **Opslaan**.

**Opdrachten**

|Hulpprogramma|Opdracht|
|---|---|
|CLI|[az network nic update](/cli/azure/network/nic)|
|PowerShell|[Set-AzNetworkInterface](/powershell/module/az.network/set-aznetworkinterface)|

## <a name="change-subnet-assignment"></a>Subnettoewijzing wijzigen

U kunt het subnet wijzigen, maar niet het virtuele netwerk waar een netwerkinterface aan is toegewezen.

1. Typ in het vak met de tekst *Resources* zoeken bovenaan de Azure Portal *netwerkinterfaces.* Wanneer **netwerkinterfaces** worden weergegeven in de zoekresultaten, selecteert u deze.
2. Selecteer de netwerkinterface voor wie u de subnettoewijzing wilt wijzigen.
3. Selecteer **IP-configuraties** onder **INSTELLINGEN.** Als er privé-IP-adressen voor alle vermelde IP-configuraties **(statisch)** naast staan, moet u de toewijzingsmethode voor IP-adressen wijzigen in dynamisch door de volgende stappen uit te voeren. Alle privé-IP-adressen moeten worden toegewezen met de dynamische toewijzingsmethode om de subnettoewijzing voor de netwerkinterface te wijzigen. Als de adressen zijn toegewezen met de dynamische methode, gaat u verder met stap 5. Als er IPv4-adressen zijn toegewezen met de statische toewijzingsmethode, moet u de volgende stappen uitvoeren om de toewijzingsmethode te wijzigen in dynamisch:
   - Selecteer de IP-configuratie voor wie u de toewijzingsmethode voor IPv4-adressen wilt wijzigen in de lijst met IP-configuraties.
   - Selecteer **Dynamisch als** de toewijzingsmethode voor het **privé-IP-adres.** U kunt geen IPv6-adres toewijzen met de statische toewijzingsmethode.
   - Selecteer **Opslaan**.
4. Selecteer in de vervolgkeuzelijst Subnet het subnet waar u de **netwerkinterface** naar wilt verplaatsen.
5. Selecteer **Opslaan**. Nieuwe dynamische adressen worden toegewezen vanuit het adresbereik van het subnet voor het nieuwe subnet. Nadat u de netwerkinterface aan een nieuw subnet hebt toegewezen, kunt u een statisch IPv4-adres uit het nieuwe subnetadresbereik toewijzen als u dat wilt. Zie IP-adressen beheren voor meer informatie over het toevoegen, wijzigen en verwijderen van IP-adressen voor een [netwerkinterface.](virtual-network-network-interface-addresses.md)

**Opdrachten**

|Hulpprogramma|Opdracht|
|---|---|
|CLI|[az network nic ip-config update](/cli/azure/network/nic/ip-config)|
|PowerShell|[Set-AzNetworkInterfaceIpConfig](/powershell/module/az.network/set-aznetworkinterfaceipconfig)|

## <a name="add-to-or-remove-from-application-security-groups"></a>Toevoegen aan of verwijderen uit toepassingsbeveiligingsgroepen

U kunt alleen een netwerkinterface toevoegen aan of een netwerkinterface verwijderen uit een toepassingsbeveiligingsgroep met behulp van de portal als de netwerkinterface is gekoppeld aan een virtuele machine. U kunt PowerShell of de Azure CLI gebruiken om een netwerkinterface toe te voegen aan of een netwerkinterface te verwijderen uit een toepassingsbeveiligingsgroep, ongeacht of de netwerkinterface is gekoppeld aan een virtuele machine of niet. Meer informatie over [toepassingsbeveiligingsgroepen](./network-security-groups-overview.md#application-security-groups) en het [maken van een toepassingsbeveiligingsgroep.](manage-network-security-group.md)

1. Begin in het vak Resources, services en documenten zoeken bovenaan de portal de naam in te typen van een virtuele machine met een netwerkinterface die u wilt toevoegen aan of verwijderen uit een *toepassingsbeveiligingsgroep.* Wanneer de naam van uw VM wordt weergegeven in de zoekresultaten, selecteert u deze.
2. Selecteer onder **INSTELLINGEN** de optie **Netwerken**.  Selecteer **Toepassingsbeveiligingsgroepen** en selecteer vervolgens De toepassingsbeveiligingsgroepen configureren de toepassingsbeveiligingsgroepen waar u de netwerkinterface aan wilt toevoegen of verwijder de selectie van de toepassingsbeveiligingsgroepen waaruit u de netwerkinterface wilt verwijderen en selecteer vervolgens  **Opslaan.** Alleen netwerkinterfaces die in hetzelfde virtuele netwerk bestaan, kunnen worden toegevoegd aan dezelfde toepassingsbeveiligingsgroep. De toepassingsbeveiligingsgroep moet zich op dezelfde locatie bevinden als de netwerkinterface.

**Opdrachten**

|Hulpprogramma|Opdracht|
|---|---|
|CLI|[az network nic update](/cli/azure/network/nic)|
|PowerShell|[Set-AzNetworkInterface](/powershell/module/az.network/set-aznetworkinterface)|

## <a name="associate-or-dissociate-a-network-security-group"></a>Een netwerkbeveiligingsgroep koppelen of ontkoppelen

1. Voer in het zoekvak boven aan de portal *netwerkinterfaces* in het zoekvak in. Wanneer **netwerkinterfaces** worden weergegeven in de zoekresultaten, selecteert u deze.
2. Selecteer de netwerkinterface in de lijst waar u een netwerkbeveiligingsgroep aan wilt koppelen of koppel een netwerkbeveiligingsgroep aan.
3. Selecteer **Netwerkbeveiligingsgroep** onder **INSTELLINGEN.**
4. Selecteer **Bewerken**.
5. Selecteer **Netwerkbeveiligingsgroep** en selecteer vervolgens de netwerkbeveiligingsgroep die u wilt koppelen aan de netwerkinterface, of selecteer **Geen** om een netwerkbeveiligingsgroep te ontkoppelen.
6. Selecteer **Opslaan**.

**Opdrachten**

- Azure CLI: [az network nic update](/cli/azure/network/nic#az_network_nic_update)
- PowerShell: [Set-AzNetworkInterface](/powershell/module/az.network/set-aznetworkinterface)

## <a name="delete-a-network-interface"></a>Een netwerkinterface verwijderen

U kunt een netwerkinterface verwijderen zolang deze niet is gekoppeld aan een virtuele machine. Als een netwerkinterface is gekoppeld aan een virtuele machine, moet u de virtuele machine eerst in de gestopte status (toewijzing van de toewijzing) plaatsen en vervolgens de netwerkinterface loskoppelen van de virtuele machine. Als u een netwerkinterface wilt loskoppelen van een virtuele machine, voltooit u de stappen in [Een netwerkinterface loskoppelen van een virtuele machine.](virtual-network-network-interface-vm.md#remove-a-network-interface-from-a-vm) U kunt een netwerkinterface echter niet loskoppelen van een virtuele machine als dit de enige netwerkinterface is die aan de virtuele machine is gekoppeld. Aan een virtuele machine moet altijd ten minste één netwerkinterface zijn gekoppeld. Als u een virtuele machine verwijdert, worden alle gekoppelde netwerkinterfaces losgekoppeld, maar worden de netwerkinterfaces niet verwijderd.

1. Typ in het vak met de tekst *Resources* zoeken bovenaan de Azure Portal *netwerkinterfaces.* Wanneer **netwerkinterfaces** worden weergegeven in de zoekresultaten, selecteert u deze.
2. Selecteer de netwerkinterface in de lijst die u wilt verwijderen.
3. Selecteer **onder Overzicht** de optie **Verwijderen.**
4. Selecteer **Ja om** de verwijdering van de netwerkinterface te bevestigen.

Wanneer u een netwerkinterface verwijdert, worden alle MAC- of IP-adressen die aan de interface zijn toegewezen, vrijgegeven.

**Opdrachten**

|Hulpprogramma|Opdracht|
|---|---|
|CLI|[az network nic delete](/cli/azure/network/nic)|
|PowerShell|[Remove-AzNetworkInterface](/powershell/module/az.network/remove-aznetworkinterface)|

## <a name="resolve-connectivity-issues"></a>Verbindingsproblemen oplossen

Als u niet kunt communiceren met of vanaf een virtuele machine, kunnen beveiligingsregels voor netwerkbeveiligingsgroep of routes die van kracht zijn voor een netwerkinterface, het probleem veroorzaken. U hebt de volgende opties om het probleem op te lossen:

### <a name="view-effective-security-rules"></a>Effectieve beveiligingsregels weergeven

De effectieve beveiligingsregels voor elke netwerkinterface die aan een virtuele machine is gekoppeld, zijn een combinatie van de regels die u hebt gemaakt in een netwerkbeveiligingsgroep en [standaardbeveiligingsregels.](./network-security-groups-overview.md#default-security-rules) Inzicht in de effectieve beveiligingsregels voor een netwerkinterface kan u helpen te bepalen waarom u niet kunt communiceren met of vanaf een virtuele machine. U kunt de effectieve regels weergeven voor elke netwerkinterface die is gekoppeld aan een virtuele machine die wordt uitgevoerd.

1. Voer in het zoekvak bovenaan de portal de naam in van een virtuele machine voor wie u effectieve beveiligingsregels wilt weergeven. Als u de naam van een virtuele machine niet weet, voert u *virtuele machines* in het zoekvak in. Wanneer **virtuele machines** worden weergegeven in de zoekresultaten, selecteert u deze en selecteert u vervolgens een virtuele machine in de lijst.
2. Selecteer **Netwerken** onder **INSTELLINGEN.**
3. Selecteer de naam van een netwerkinterface.
4. Selecteer **Effectieve beveiligingsregels onder** ONDERSTEUNING EN **PROBLEEMOPLOSSING.**
5. Bekijk de lijst met effectieve beveiligingsregels om te bepalen of de juiste regels bestaan voor de vereiste binnenkomende en uitgaande communicatie. Meer informatie over wat u ziet in de lijst in [Overzicht van netwerkbeveiligingsgroep.](./network-security-groups-overview.md)

Met de functie IP-stroom controleren van Azure Network Watcher u ook bepalen of communicatie tussen een virtuele machine en een eindpunt wordt voorkomen door beveiligingsregels. Zie IP-stroom [controleren voor meer informatie.](../network-watcher/diagnose-vm-network-traffic-filtering-problem.md?toc=%2fazure%2fvirtual-network%2ftoc.json)

**Opdrachten**

- Azure CLI: [az network nic list-effective-nsg](/cli/azure/network/nic#az_network_nic_list_effective_nsg)
- PowerShell: [Get-AzEffectiveNetworkSecurityGroup](/powershell/module/az.network/get-azeffectivenetworksecuritygroup)

### <a name="view-effective-routes"></a>Effectieve routes weergeven

De effectieve routes voor de netwerkinterfaces die zijn gekoppeld aan een virtuele machine zijn een combinatie van standaardroutes, alle routes die u hebt gemaakt en routes die zijn doorgegeven vanuit on-premises netwerken via BGP via een gateway van een virtueel Azure-netwerk. Inzicht in de effectieve routes voor een netwerkinterface kan u helpen te bepalen waarom u niet kunt communiceren met of vanaf een virtuele machine. U kunt de effectieve routes weergeven voor elke netwerkinterface die is gekoppeld aan een virtuele machine die wordt uitgevoerd.

1. Voer in het zoekvak boven aan de portal de naam in van een virtuele machine voor wie u effectieve beveiligingsregels wilt weergeven. Als u de naam van een virtuele machine niet weet, voert u *virtuele machines* in het zoekvak in. Wanneer **virtuele machines** worden weergegeven in de zoekresultaten, selecteert u deze en selecteert u vervolgens een virtuele machine in de lijst.
2. Selecteer **Netwerken** onder **INSTELLINGEN.**
3. Selecteer de naam van een netwerkinterface.
4. Selecteer **Effectieve routes onder** ONDERSTEUNING EN **PROBLEEMOPLOSSING.**
5. Bekijk de lijst met effectieve routes om te bepalen of de juiste routes bestaan voor de vereiste binnenkomende en uitgaande communicatie. Meer informatie over wat u ziet in de lijst in [Routeringsoverzicht.](virtual-networks-udr-overview.md)

Met de functie volgende hop van Azure Network Watcher u ook bepalen of de communicatie tussen een virtuele machine en een eindpunt wordt voorkomen door routes. Zie Volgende hop voor [meer informatie.](../network-watcher/diagnose-vm-network-routing-problem.md?toc=%2fazure%2fvirtual-network%2ftoc.json)

**Opdrachten**

- Azure CLI: [az network nic show-effective-route-table](/cli/azure/network/nic#az_network_nic_show_effective_route_table)
- PowerShell: [Get-AzEffectiveRouteTable](/powershell/module/az.network/get-azeffectiveroutetable)

## <a name="permissions"></a>Machtigingen

Als u taken wilt uitvoeren op netwerkinterfaces, moet uw [](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) account worden toegewezen aan de rol Netwerkbijdrager of aan een aangepaste rol die de juiste machtigingen krijgt die in de volgende tabel worden vermeld: [](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)

| Bewerking                                                                     | Name                                                      |
| ---------                                                                  | -------------                                             |
| Microsoft.Network/networkInterfaces/read                                   | Netwerkinterface op halen                                     |
| Microsoft.Network/networkInterfaces/write                                  | Netwerkinterface maken of bijwerken                        |
| Microsoft.Network/networkInterfaces/join/action                            | Een netwerkinterface koppelen aan een virtuele machine           |
| Microsoft.Network/networkInterfaces/delete                                 | Netwerkinterface verwijderen                                  |
| Microsoft.Network/networkInterfaces/joinViaPrivateIp/action                | Een resource aan een netwerkinterface via een...     |
| Microsoft.Network/networkInterfaces/effectiveRouteTable/action             | Effectieve routetabel voor netwerkinterfaces krijgen               |
| Microsoft.Network/networkInterfaces/effectiveNetworkSecurityGroups/action  | Effectieve netwerkinterfacebeveiligingsgroepen op te halen           |
| Microsoft.Network/networkInterfaces/loadBalancers/read                     | Load balancers voor netwerkinterfaces op halen                      |
| Microsoft.Network/networkInterfaces/serviceAssociations/read               | Service-associatie op halen                                   |
| Microsoft.Network/networkInterfaces/serviceAssociations/write              | Een service-associatie maken of bijwerken                    |
| Microsoft.Network/networkInterfaces/serviceAssociations/delete             | Service-associatie verwijderen                                |
| Microsoft.Network/networkInterfaces/serviceAssociations/validate/action    | Service-associatie valideren                              |
| Microsoft.Network/networkInterfaces/ipconfigurations/read                  | IP-configuratie van netwerkinterface op halen                    |

## <a name="next-steps"></a>Volgende stappen

- Een VM met meerdere NIC's maken met behulp van [de Azure CLI](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json) of [PowerShell](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- Een enkele NIC-VM met meerdere IPv4-adressen maken met behulp van [de Azure CLI](virtual-network-multiple-ip-addresses-cli.md) of [PowerShell](virtual-network-multiple-ip-addresses-powershell.md)
- Maak één NIC-VM met een privé-IPv6-adres (achter een Azure Load Balancer) met behulp van de [Azure CLI-,](../load-balancer/load-balancer-ipv6-internet-cli.md?toc=%2fazure%2fvirtual-network%2ftoc.json) [PowerShell-](../load-balancer/load-balancer-ipv6-internet-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json)of [Azure Resource Manager sjabloon](../load-balancer/load-balancer-ipv6-internet-template.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- Een netwerkinterface maken met [behulp van PowerShell-](powershell-samples.md) of [Azure CLI-voorbeeldscripts](cli-samples.md) of met behulp van een Azure [Resource Manager sjabloon](template-samples.md)
- Definities voor [virtuele Azure Policy maken](./policy-reference.md) en toewijzen