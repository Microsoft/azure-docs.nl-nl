---
title: Een virtueel Azure-netwerk maken, wijzigen of verwijderen
titlesuffix: Azure Virtual Network
description: Maak en verwijder een virtueel netwerk en Wijzig instellingen, zoals DNS-servers en IP-adres ruimten, voor een bestaand virtueel netwerk.
services: virtual-network
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/10/2019
ms.author: kumud
ms.openlocfilehash: 61ca4dc8cd7048df69c827e7ca657b9882900819
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/14/2021
ms.locfileid: "98218906"
---
# <a name="create-change-or-delete-a-virtual-network"></a>Een virtueel netwerk maken, wijzigen of verwijderen

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Meer informatie over het maken en verwijderen van een virtueel netwerk en het wijzigen van instellingen, zoals DNS-servers en IP-adres ruimten, voor een bestaand virtueel netwerk. Als u geen ervaring hebt met virtuele netwerken, kunt u meer informatie hierover vinden in het [overzicht van Virtual Network](virtual-networks-overview.md) of door een [zelf studie](quick-create-portal.md)te volt ooien. Een virtueel netwerk bevat subnetten. Zie [subnets beheren](virtual-network-manage-subnet.md)voor meer informatie over het maken, wijzigen en verwijderen van subnetten.

## <a name="before-you-begin"></a>Voordat u begint

Voer de volgende taken uit voordat u de stappen in een van de secties van dit artikel uitvoert:

- Als u nog geen Azure-account hebt, kunt u zich aanmelden voor een [gratis proef account](https://azure.microsoft.com/free).
- Als u de portal gebruikt, opent https://portal.azure.com u en meldt u zich aan met uw Azure-account.
- Als u Power shell-opdrachten gebruikt om taken in dit artikel te volt ooien, moet u de opdrachten uitvoeren in de [Azure Cloud shell](https://shell.azure.com/powershell)of Power shell uitvoeren vanaf uw computer. Azure Cloud Shell is een gratis interactieve shell waarmee u de stappen in dit artikel kunt uitvoeren. In deze shell zijn algemene Azure-hulpprogramma's vooraf geïnstalleerd en geconfigureerd voor gebruik met uw account. Voor deze zelf studie is de Azure PowerShell module versie 1.0.0 of hoger vereist. Voer `Get-Module -ListAvailable Az` uit om te kijken welke versie is geïnstalleerd. Als u PowerShell wilt upgraden, raadpleegt u [De Azure PowerShell-module installeren](/powershell/azure/install-az-ps). Als u PowerShell lokaal uitvoert, moet u ook `Connect-AzAccount` uitvoeren om verbinding te kunnen maken met Azure.
- Als u Azure-opdracht regel interface opdrachten gebruikt om taken in dit artikel te volt ooien, moet u de opdrachten uitvoeren in de [Azure Cloud shell](https://shell.azure.com/bash)of door de CLI vanaf uw computer uit te voeren. Voor deze zelf studie is de Azure CLI-versie 2.0.31 of hoger vereist. Voer `az --version` uit om te kijken welke versie is geïnstalleerd. Zie [Azure CLI installeren](/cli/azure/install-azure-cli) als u de CLI wilt installeren of een upgrade wilt uitvoeren. Als u de Azure CLI lokaal uitvoert, moet u ook uitvoeren `az login` om een verbinding te maken met Azure.
- Het account waarmee u zich aanmeldt of verbinding maakt met Azure met, moet worden toegewezen aan de rol [netwerk bijdrager](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) of aan een [aangepaste rol](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) waaraan de juiste acties zijn toegewezen die worden vermeld in [machtigingen](#permissions).

## <a name="create-a-virtual-network"></a>Een virtueel netwerk maken

1. Selecteer **+ een resource**  >  **netwerken**  >  **virtueel netwerk** maken.
2. Typ of selecteer waarden voor de volgende instellingen en selecteer vervolgens **maken**:
   - **Naam**: de naam moet uniek zijn in de [resource groep](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group) die u selecteert voor het maken van het virtuele netwerk in. U kunt de naam niet wijzigen nadat het virtuele netwerk is gemaakt. U kunt meerdere virtuele netwerken gedurende een periode maken. Zie [naamgevings conventies](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging#naming-and-tagging-resources)voor naamgevings suggesties. Als u een naamgevings Conventie volgt, kunt u eenvoudiger meerdere virtuele netwerken beheren.
   - **Adres ruimte**: de adres ruimte voor een virtueel netwerk bestaat uit een of meer niet-overlappende adresbereiken die zijn opgegeven in CIDR-notatie. Het adres bereik dat u definieert, kan openbaar of privé zijn (RFC 1918). Of u het adres bereik als openbaar of privé definieert, het adres bereik is alleen bereikbaar vanuit het virtuele netwerk, van gekoppelde virtuele netwerken en van alle on-premises netwerken die u met het virtuele netwerk hebt verbonden. U kunt de volgende adresbereiken niet toevoegen:
     - 224.0.0.0/4 (multi cast)
     - 255.255.255.255/32 (broadcast)
     - 127.0.0.0/8 (loop back)
     - 169.254.0.0/16 (link-local)
     - 168.63.129.16/32 (interne DNS-, DHCP-en Azure Load Balancer- [status test](../load-balancer/load-balancer-custom-probe-overview.md#probesource))

     Hoewel u slechts één adres bereik kunt definiëren wanneer u het virtuele netwerk in de portal maakt, kunt u meer adresbereiken toevoegen aan de adres ruimte nadat het virtuele netwerk is gemaakt. Zie [een adres bereik toevoegen of verwijderen](#add-or-remove-an-address-range)voor meer informatie over het toevoegen van een adres bereik aan een bestaand virtueel netwerk.

     > [!WARNING]
     > Als een virtueel netwerk adresbereiken heeft die overlappen met een ander virtueel netwerk of een on-premises netwerk, kunnen de twee netwerken niet worden verbonden. Voordat u een adres bereik definieert, moet u overwegen of u het virtuele netwerk in de toekomst mogelijk wilt verbinden met andere virtuele netwerken of on-premises netwerken. Micro soft raadt aan om virtuele netwerk adresbereiken te configureren met een privé adres ruimte of open bare adres ruimte die eigendom is van uw organisatie.
     >

     - **Subnetnaam**: de naam van het subnet moet uniek zijn binnen het virtuele netwerk. U kunt de naam van het subnet niet wijzigen nadat het subnet is gemaakt. Voor de portal moet u één subnet definiëren wanneer u een virtueel netwerk maakt, zelfs als een virtueel netwerk geen subnets nodig heeft. In de portal kunt u slechts één subnet definiëren wanneer u een virtueel netwerk maakt. U kunt later meer subnetten toevoegen aan het virtuele netwerk nadat het virtuele netwerk is gemaakt. Zie [subnets beheren](virtual-network-manage-subnet.md)om een subnet toe te voegen aan een virtueel netwerk. U kunt een virtueel netwerk met meerdere subnetten maken met behulp van Azure CLI of Power shell.

       >[!TIP]
       >Soms maken beheerders verschillende subnetten om verkeers routering tussen de subnetten te filteren of te beheren. Bedenk voordat u subnetten definieert hoe u verkeer tussen uw subnetten wilt filteren en routeren. Zie [netwerk beveiligings groepen](./network-security-groups-overview.md)voor meer informatie over het filteren van verkeer tussen subnetten. Azure routeert automatisch verkeer tussen subnetten, maar u kunt de standaard routes van Azure onderdrukken. Zie [route ring Overview](virtual-networks-udr-overview.md)voor meer informatie over de standaard routerings routering van het subnet van Azure.
       >

     - **Adres bereik van subnet**: het bereik moet zich binnen de adres ruimte bevinden die u hebt ingevoerd voor het virtuele netwerk. Het kleinste bereik dat u kunt opgeven, is/29, dat acht IP-adressen voor het subnet bevat. Azure reserveert het eerste en laatste adres in elk subnet voor protocol conformiteit. Er zijn drie extra adressen gereserveerd voor Azure-service gebruik. Als gevolg hiervan heeft een virtueel netwerk met een subnetadres van/29 slechts drie bruikbare IP-adressen. Als u van plan bent om een virtueel netwerk te verbinden met een VPN-gateway, moet u een gateway-subnet maken. Meer informatie over [specifieke aandachtspunten voor de adres bereik voor gateway-subnetten](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md?toc=%2fazure%2fvirtual-network%2ftoc.json#gwsub). Onder specifieke voor waarden kunt u het adres bereik wijzigen nadat het subnet is gemaakt. Zie [subnets beheren](virtual-network-manage-subnet.md)voor meer informatie over het wijzigen van het adres bereik van een subnet.
     - **Abonnement**: Selecteer een [abonnement](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription). U kunt niet hetzelfde virtuele netwerk gebruiken in meer dan één Azure-abonnement. U kunt echter een virtueel netwerk in één abonnement verbinden met virtuele netwerken in andere abonnementen met [peering op virtueel netwerk](virtual-network-peering-overview.md). Elke Azure-resource waarmee u verbinding maakt met het virtuele netwerk moet zich in hetzelfde abonnement als het virtuele netwerk bevindt.
     - **Resource groep**: Selecteer een bestaande [resource groep](../azure-resource-manager/management/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-groups) of maak een nieuwe. Een Azure-resource waarmee u verbinding maakt met het virtuele netwerk, kan zich in dezelfde resource groep bevindt als het virtuele netwerk of in een andere resource groep.
     - **Locatie**: Selecteer een Azure- [locatie](https://azure.microsoft.com/regions/), ook wel een regio genoemd. Een virtueel netwerk kan zich op slechts één Azure-locatie bevindt. U kunt echter een virtueel netwerk op één locatie verbinden met een virtueel netwerk op een andere locatie met behulp van een VPN-gateway. Elke Azure-resource waarmee u verbinding maakt met het virtuele netwerk moet zich op dezelfde locatie bevindt als het virtuele netwerk.

**Opdrachten**

- Azure CLI: [AZ Network vnet Create](/cli/azure/network/vnet)
- Power shell: [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork)

## <a name="view-virtual-networks-and-settings"></a>Virtuele netwerken en instellingen weer geven

1. Voer in het zoekvak boven aan de portal *virtuele netwerken* in het zoekvak in. Wanneer **virtuele netwerken** worden weer gegeven in de zoek resultaten, selecteert u deze.
2. Selecteer in de lijst met virtuele netwerken het virtuele netwerk waarvan u de instellingen wilt weer geven.
3. De volgende instellingen worden weer gegeven voor het virtuele netwerk dat u hebt geselecteerd:
   - **Overzicht**: Hier vindt u informatie over het virtuele netwerk, met inbegrip van adres ruimte en DNS-servers. Op de volgende scherm afbeelding ziet u de overzichts instellingen voor een virtueel netwerk met de naam **MyVNet**:

     ![Overzicht van de netwerk interface](./media/manage-virtual-network/vnet-overview.png)

     U kunt een virtueel netwerk verplaatsen naar een ander abonnement of een andere resource groep door **wijzigen** te selecteren naast de **resource groep** of de naam van het **abonnement**. Zie [resources verplaatsen naar een andere resource groep of een ander abonnement](../azure-resource-manager/management/move-resource-group-and-subscription.md?toc=%2fazure%2fvirtual-network%2ftoc.json)voor meer informatie over het verplaatsen van een virtueel netwerk. In het artikel vindt u vereisten en hoe u resources kunt verplaatsen met behulp van de Azure Portal, Power shell en Azure CLI. Alle resources die zijn verbonden met het virtuele netwerk moeten worden verplaatst met het virtuele netwerk.
   - **Adres ruimte**: de adres ruimten die worden toegewezen aan het virtuele netwerk worden weer gegeven. Volg de stappen in [een adres bereik toevoegen of verwijderen](#add-or-remove-an-address-range)voor meer informatie over het toevoegen en verwijderen van een adres bereik aan de adres ruimte.
   - **Verbonden apparaten**: alle resources die zijn verbonden met het virtuele netwerk, worden weer gegeven. In de vorige scherm afbeelding zijn drie netwerk interfaces en één load balancer verbonden met het virtuele netwerk. Nieuwe resources die u maakt en verbinding maken met het virtuele netwerk, worden weer gegeven. Als u een resource verwijdert die is verbonden met het virtuele netwerk, wordt deze niet meer weer gegeven in de lijst.
   - **Subnetten**: er wordt een lijst weer gegeven met de subnetten die in het virtuele netwerk aanwezig zijn. Zie [subnets beheren](virtual-network-manage-subnet.md)voor meer informatie over het toevoegen en verwijderen van een subnet.
   - **DNS-servers**: u kunt opgeven of de interne DNS-server van Azure of een aangepaste DNS-server naam omzetting biedt voor apparaten die zijn verbonden met het virtuele netwerk. Wanneer u een virtueel netwerk maakt met behulp van de Azure Portal, worden de DNS-servers van Azure standaard gebruikt voor naam omzetting binnen een virtueel netwerk. Als u de DNS-servers wilt wijzigen, voert u de stappen in [DNS-servers wijzigen](#change-dns-servers) in dit artikel uit.
   - **Peerings**: als er al peerings zijn in het abonnement, worden deze hier vermeld. U kunt instellingen voor bestaande peerings weer geven of peerings maken, wijzigen of verwijderen. Zie [peering op virtueel netwerk](virtual-network-peering-overview.md)voor meer informatie over peerings.
   - **Eigenschappen**: geeft instellingen weer over het virtuele netwerk, met inbegrip van de resource-id van het virtuele netwerk en het Azure-abonnement waarin deze zich bevindt.
   - **Diagram**: het diagram biedt een visuele weer gave van alle apparaten die zijn verbonden met het virtuele netwerk. Het diagram bevat enkele belang rijke informatie over de apparaten. Als u een apparaat in deze weer gave wilt beheren, selecteert u het apparaat in het diagram.
   - **Algemene Azure-instellingen**: voor meer informatie over algemene Azure-instellingen raadpleegt u de volgende informatie:
     - [Activiteitenlogboek](../azure-monitor/platform/platform-logs-overview.md)
     - [Toegangsbeheer (IAM)](../role-based-access-control/overview.md)
     - [Tags](../azure-resource-manager/management/tag-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
     - [Vergrendelingen](../azure-resource-manager/management/lock-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
     - [Automation-script](../azure-resource-manager/management/manage-resource-groups-portal.md#export-resource-groups-to-templates)

**Opdrachten**

- Azure CLI: [AZ Network vnet show](/cli/azure/network/vnet)
- Power shell: [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork)

## <a name="add-or-remove-an-address-range"></a>Een adres bereik toevoegen of verwijderen

U kunt adresbereiken voor een virtueel netwerk toevoegen en verwijderen. Een adres bereik moet worden opgegeven in CIDR-notatie en mag niet overlappen met andere adresbereiken binnen hetzelfde virtuele netwerk. De adresbereiken die u definieert, kunnen openbaar of privé zijn (RFC 1918). Of u het adres bereik als openbaar of privé definieert, het adres bereik is alleen bereikbaar vanuit het virtuele netwerk, van gekoppelde virtuele netwerken en van alle on-premises netwerken die u met het virtuele netwerk hebt verbonden. 

U kunt het adres bereik voor een virtueel netwerk verlagen, zolang het nog steeds de bereiken van alle gekoppelde subnetten bevat. Daarnaast kunt u het adres bereik uitbreiden, bijvoorbeeld om een/16 te wijzigen in/8. 

<!-- the above statement has been edited to reflect the most recent comments on the reopened issue: https://github.com/MicrosoftDocs/azure-docs/issues/20572 -->

U kunt de volgende adresbereiken niet toevoegen:

- 224.0.0.0/4 (multi cast)
- 255.255.255.255/32 (broadcast)
- 127.0.0.0/8 (loop back)
- 169.254.0.0/16 (link-local)
- 168.63.129.16/32 (interne DNS-, DHCP-en Azure Load Balancer- [status test](../load-balancer/load-balancer-custom-probe-overview.md#probesource))

Een adres bereik toevoegen of verwijderen:

1. Voer in het zoekvak boven aan de portal *virtuele netwerken* in het zoekvak in. Wanneer **virtuele netwerken** worden weer gegeven in de zoek resultaten, selecteert u deze.
2. Selecteer in de lijst met virtuele netwerken het virtuele netwerk waarvoor u een adres bereik wilt toevoegen of verwijderen.
3. Selecteer **adres ruimte** onder **instellingen**.
4. Voer een van de volgende opties uit:
    - **Een adres bereik toevoegen**: Voer het nieuwe adres bereik in. Het adres bereik mag niet overlappen met een bestaand adres bereik dat is gedefinieerd voor het virtuele netwerk.
    - **Verwijder een adres bereik**: Klik aan de rechter kant van het adres bereik dat u wilt verwijderen, selecteer **...** en selecteer vervolgens **verwijderen**. Als er een subnet in het adres bereik bestaat, kunt u het adres bereik niet verwijderen. Als u een adres bereik wilt verwijderen, moet u eerst alle subnetten (en alle resources in de subnetten) verwijderen die voor komen in het adres bereik.
5. Selecteer **Opslaan**.

**Opdrachten**

- Azure CLI: [AZ Network vnet update](/cli/azure/network/vnet)
- Power shell: [set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork)

## <a name="change-dns-servers"></a>DNS-servers wijzigen

Alle virtuele machines die zijn verbonden met het virtuele netwerk, registreren bij de DNS-servers die u opgeeft voor het virtuele netwerk. Ze gebruiken ook de opgegeven DNS-server voor naam omzetting. Elke netwerk interface (NIC) in een virtuele machine kan zijn eigen DNS-server instellingen hebben. Als een NIC zijn eigen DNS-server instellingen heeft, overschrijven ze de DNS-server instellingen voor het virtuele netwerk. Zie [Network Interface tasks and Settings](virtual-network-network-interface.md#change-dns-servers)(Engelstalig) voor meer informatie over DNS-instellingen voor de NIC. Zie [naam omzetting voor vm's en rolinstanties](virtual-networks-name-resolution-for-vms-and-role-instances.md)voor meer informatie over naam omzetting voor vm's en Rolinstanties in azure Cloud Services. Een DNS-server toevoegen, wijzigen of verwijderen:

1. Voer in het zoekvak boven aan de portal *virtuele netwerken* in het zoekvak in. Wanneer **virtuele netwerken** worden weer gegeven in de zoek resultaten, selecteert u deze.
2. Selecteer in de lijst met virtuele netwerken het virtuele netwerk waarvoor u de DNS-servers wilt wijzigen.
3. Selecteer **DNS-servers** onder **instellingen**.
4. Selecteer één van de volgende opties:
   - **Standaard (door Azure)**: alle resource namen en privé-IP-adressen worden automatisch geregistreerd bij de Azure DNS-servers. U kunt namen omzetten tussen resources die zijn verbonden met hetzelfde virtuele netwerk. U kunt deze optie niet gebruiken om namen in virtuele netwerken op te lossen. Als u namen in virtuele netwerken wilt omzetten, moet u een aangepaste DNS-server gebruiken.
   - **Aangepast**: u kunt een of meer servers toevoegen, tot aan de Azure-limiet voor een virtueel netwerk. Zie [Azure-limieten](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#virtual-networking-limits-classic)voor meer informatie over DNS-server limieten. U hebt de volgende opties:
   - **Een adres toevoegen**: Hiermee voegt u de server toe aan de lijst met DNS-servers voor het virtuele netwerk. Met deze optie wordt de DNS-server ook geregistreerd bij Azure. Als u al een DNS-server met Azure hebt geregistreerd, kunt u die DNS-server selecteren in de lijst.
   - **Een adres verwijderen**: naast de server die u wilt verwijderen, selecteert u **...** en vervolgens **verwijderen**. Als u de server verwijdert, wordt de server alleen uit deze lijst met virtuele netwerken verwijderd. De DNS-server blijft geregistreerd in azure voor het gebruik van uw andere virtuele netwerken.
   - De **volg orde van de DNS-server adressen wijzigen**: het is belang rijk om te controleren of u uw DNS-servers in de juiste volg orde voor uw omgeving vermeldt. DNS-Server lijsten worden gebruikt in de volg orde waarin ze zijn opgegeven. Ze werken niet als Round Robin-installatie. Als de eerste DNS-server in de lijst kan worden bereikt, gebruikt de client die DNS-server, ongeacht of de DNS-server goed werkt. Verwijder alle DNS-servers die worden vermeld en voeg deze weer toe in de gewenste volg orde.
   - **Een adres wijzigen**: Markeer de DNS-server in de lijst en voer vervolgens het nieuwe adres in.
5. Selecteer **Opslaan**.
6. Start de virtuele machines die zijn verbonden met het virtuele netwerk opnieuw op, zodat deze de nieuwe DNS-server instellingen krijgen. Vm's blijven hun huidige DNS-instellingen gebruiken totdat ze opnieuw zijn opgestart.

**Opdrachten**

- Azure CLI: [AZ Network vnet update](/cli/azure/network/vnet)
- Power shell: [set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork)

## <a name="delete-a-virtual-network"></a>Een virtueel netwerk verwijderen

U kunt een virtueel netwerk alleen verwijderen als er geen resources zijn verbonden. Als er resources zijn verbonden met een subnet binnen het virtuele netwerk, moet u eerst de bronnen verwijderen die zijn verbonden met alle subnetten in het virtuele netwerk. De stappen die u moet nemen om een resource te verwijderen, variëren afhankelijk van de resource. Raadpleeg de documentatie voor elk resource type dat u wilt verwijderen voor meer informatie over het verwijderen van resources die zijn verbonden met subnetten. Een virtueel netwerk verwijderen:

1. Voer in het zoekvak boven aan de portal *virtuele netwerken* in het zoekvak in. Wanneer **virtuele netwerken** worden weer gegeven in de zoek resultaten, selecteert u deze.
2. Selecteer in de lijst met virtuele netwerken het virtuele netwerk dat u wilt verwijderen.
3. Controleer of er geen apparaten zijn verbonden met het virtuele netwerk door **verbonden apparaten** te selecteren onder **instellingen**. Als er apparaten zijn aangesloten, moet u deze verwijderen voordat u het virtuele netwerk kunt verwijderen. Als er geen verbonden apparaten zijn, selecteert u **overzicht**.
4. Selecteer **Verwijderen**.
5. Selecteer **Ja** om het verwijderen van het virtuele netwerk te bevestigen.

**Opdrachten**

- Azure CLI: [Azure Network vnet verwijderen](/cli/azure/network/vnet)
- Power shell: [Remove-AzVirtualNetwork](/powershell/module/az.network/remove-azvirtualnetwork)

## <a name="permissions"></a>Machtigingen

Als u taken wilt uitvoeren op virtuele netwerken, moet uw account worden toegewezen aan de rol [netwerk bijdrager](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) of aan een [aangepaste](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) rol waaraan de juiste acties in de volgende tabel zijn toegewezen:

| Actie                                  |   Naam                                |
|---------------------------------------- |   --------------------------------    |
|Micro soft. Network/virtualNetworks/lezen   |   Een virtueel netwerk lezen              |
|Micro soft. Network/virtualNetworks/schrijven  |   Een virtueel netwerk maken of bijwerken  |
|Micro soft. Network/virtualNetworks/verwijderen |   Een virtueel netwerk verwijderen            |

## <a name="next-steps"></a>Volgende stappen

- Een virtueel netwerk maken met behulp van [Power shell](powershell-samples.md) of [Azure cli](cli-samples.md) -voorbeeld scripts of met behulp van Azure [Resource Manager-sjablonen](template-samples.md)
- [Azure Policy definities](./policy-reference.md) voor virtuele netwerken maken en toewijzen