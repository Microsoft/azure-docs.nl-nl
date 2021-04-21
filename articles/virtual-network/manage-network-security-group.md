---
title: Een Azure-netwerkbeveiligingsgroep maken, wijzigen of verwijderen
titlesuffix: Azure Virtual Network
description: Ontdek waar u informatie vindt over beveiligingsregels en hoe u een netwerkbeveiligingsgroep maakt, wijzigt of verwijdert.
services: virtual-network
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/13/2020
ms.author: kumud
ms.openlocfilehash: 82f23c1fea29e2a88dd2a67ec9c89c7bf05bfff7
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107783481"
---
# <a name="create-change-or-delete-a-network-security-group"></a>Een netwerkbeveiligingsgroep maken, wijzigen of verwijderen

Met beveiligingsregels in netwerkbeveiligingsgroepen kunt u het type netwerkverkeer filteren dat in en uit subnetten van virtuele netwerken en netwerkinterfaces kan stromen. Zie Overzicht van netwerkbeveiligingsgroepen voor meer informatie over [netwerkbeveiligingsgroepen.](./network-security-groups-overview.md) Voltooi vervolgens de zelfstudie [Netwerkverkeer filteren](tutorial-filter-network-traffic.md) om enige ervaring op te doen met netwerkbeveiligingsgroepen.

## <a name="before-you-begin"></a>Voordat u begint

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Als u nog geen abonnement hebt, stelt u een Azure-account in met een actief abonnement. [Gratis een account maken](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) Voltooi een van deze taken voordat u aan de rest van dit artikel begint:

- **Portalgebruikers:** meld u aan bij [de Azure Portal](https://portal.azure.com) met uw Azure-account.

- **PowerShell-gebruikers:** voer de opdrachten uit in [de Azure Cloud Shell](https://shell.azure.com/powershell)of voer PowerShell uit vanaf uw computer. Azure Cloud Shell is een gratis interactieve shell waarmee u de stappen in dit artikel kunt uitvoeren. In deze shell zijn algemene Azure-hulpprogramma's vooraf geïnstalleerd en geconfigureerd voor gebruik met uw account. Zoek op Azure Cloud Shell browsertabblad  de vervolgkeuzelijst Omgeving selecteren en kies **vervolgens PowerShell** als dit nog niet is geselecteerd.

    Als u PowerShell lokaal gebruikt, gebruikt u Azure PowerShell moduleversie 1.0.0 of hoger. Voer `Get-Module -ListAvailable Az.Network` uit om te kijken welke versie is geïnstalleerd. Als u PowerShell wilt upgraden, raadpleegt u [De Azure PowerShell-module installeren](/powershell/azure/install-az-ps). Voer `Connect-AzAccount` uit om een verbinding op te zetten met Azure.

- **Azure CLI-gebruikers (opdrachtregelinterface)**: voer de opdrachten uit in de Azure Cloud Shell [of](https://shell.azure.com/bash)voer de CLI uit vanaf uw computer. Gebruik Azure CLI versie 2.0.28 of hoger als u de Azure CLI lokaal gebruikt. Voer `az --version` uit om te kijken welke versie is geïnstalleerd. Zie [Azure CLI installeren](/cli/azure/install-azure-cli) als u de CLI wilt installeren of een upgrade wilt uitvoeren. Voer `az login` uit om een verbinding op te zetten met Azure.

Het account bij wie u zich aanmeldt of verbinding [](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) maakt met Azure, moet worden toegewezen aan de rol Netwerkbijdrager of aan een Aangepaste rol die de juiste acties krijgt toegewezen die worden vermeld in [Machtigingen.](#permissions) [](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json)

## <a name="work-with-network-security-groups"></a>Met netwerkbeveiligingsgroepen werken

U kunt alle maken, [weergeven,](#view-all-network-security-groups) [details van](#view-details-of-a-network-security-group)weergeven, [wijzigen](#change-a-network-security-group)en [een](#delete-a-network-security-group) netwerkbeveiligingsgroep verwijderen. U kunt een [netwerkbeveiligingsgroep ook koppelen](#associate-or-dissociate-a-network-security-group-to-or-from-a-subnet-or-network-interface) aan of loskoppelen van een netwerkinterface of subnet.

### <a name="create-a-network-security-group"></a>Een netwerkbeveiligingsgroep maken

Er is een limiet voor het aantal netwerkbeveiligingsgroepen dat u kunt maken voor elke Azure-locatie en elk azure-abonnement. Zie Azure subscription [and service limits, quotas, and constraints (Limieten, quota en beperkingen voor Azure-abonnementen en -service) voor meer informatie.](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)

1. Selecteer in het menu van [Azure Portal](https://portal.azure.com) of op de **startpagina** de optie **Een resource maken**.

2. Selecteer **Netwerken** en selecteer vervolgens **Netwerkbeveiligingsgroep.**

3. Stel op **de pagina Netwerkbeveiligingsgroep** maken op het tabblad **Basisbeginselen** waarden in voor de volgende instellingen:

    | Instelling | Bewerking |
    | --- | --- |
    | **Abonnement** | Kies uw abonnement. |
    | **Resourcegroep** | Kies een bestaande resourcegroep of selecteer **Nieuwe maken om** een nieuwe resourcegroep te maken. |
    | **Naam** | Voer een unieke tekstreeks in een resourcegroep in. |
    | **Regio** | Kies de locatie die u wilt. |

4. Selecteer **Controleren + maken**.

5. Nadat u het bericht **Validatie geslaagd ziet,** selecteert u **Maken.**

#### <a name="commands"></a>Opdracht

| Hulpprogramma | Opdracht |
| ---- | ------- |
| Azure CLI | [az network nsg create](/cli/azure/network/nsg#az_network_nsg_create) |
| PowerShell | [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup) |

### <a name="view-all-network-security-groups"></a>Alles weergeven netwerkbeveiligingsgroepen maken

Ga naar de [Azure Portal](https://portal.azure.com) uw netwerkbeveiligingsgroepen weer te bieden. Zoek en selecteer **Netwerkbeveiligingsgroepen.** De lijst met netwerkbeveiligingsgroepen wordt weergegeven voor uw abonnement.

#### <a name="commands"></a>Opdracht

| Hulpprogramma | Opdracht |
| ---- | ------- |
| Azure CLI | [az network nsg list](/cli/azure/network/nsg#az_network_nsg_list) |
| PowerShell | [Get-AzNetworkSecurityGroup](/powershell/module/az.network/get-aznetworksecuritygroup) |

### <a name="view-details-of-a-network-security-group"></a>Details van een netwerkbeveiligingsgroep weergeven

1. Ga naar de [Azure Portal](https://portal.azure.com) uw netwerkbeveiligingsgroepen weer te bieden. Zoek en selecteer **Netwerkbeveiligingsgroepen.**

2. Selecteer de naam van uw netwerkbeveiligingsgroep.

In de menubalk van de netwerkbeveiligingsgroep, onder **Instellingen,** kunt u de **inkomende** **beveiligingsregels,** uitgaande beveiligingsregels, netwerkinterfaces en subnetten weergeven waar de **netwerkbeveiligingsgroep** aan is gekoppeld.

Onder **Bewaking** kunt u Diagnostische instellingen **in- of uitschakelen.** Onder **Ondersteuning en probleemoplossing** kunt u **Effectieve beveiligingsregels weergeven.** Zie Diagnostische logboekregistratie [voor een](virtual-network-nsg-manage-log.md) netwerkbeveiligingsgroep en Diagnose uitvoeren voor een probleem met [een VM-netwerkverkeersfilter voor meer informatie.](diagnose-network-traffic-filter-problem.md)

Zie de volgende artikelen voor meer informatie over de algemene vermelde Azure-instellingen:

- [Activiteitenlogboek](../azure-monitor/essentials/platform-logs-overview.md)
- [Toegangsbeheer (IAM)](../role-based-access-control/overview.md)
- [Tags](../azure-resource-manager/management/tag-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Vergrendelingen](../azure-resource-manager/management/lock-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Automation-script](../azure-resource-manager/templates/export-template-portal.md)

#### <a name="commands"></a>Opdracht

| Hulpprogramma | Opdracht |
| ---- | ------- |
| Azure CLI | [az network nsg show](/cli/azure/network/nsg#az_network_nsg_show) |
| PowerShell | [Get-AzNetworkSecurityGroup](/powershell/module/az.network/get-aznetworksecuritygroup) |

### <a name="change-a-network-security-group"></a>Een netwerkbeveiligingsgroep wijzigen

1. Ga naar de [Azure Portal](https://portal.azure.com) uw netwerkbeveiligingsgroepen weer te bieden. Zoek en selecteer **Netwerkbeveiligingsgroepen.**

2. Selecteer de naam van de netwerkbeveiligingsgroep die u wilt wijzigen.

De meest voorkomende [](#create-a-security-rule)wijzigingen zijn het toevoegen van een beveiligingsregel, het verwijderen van een regel [en](#delete-a-security-rule)het koppelen of ontkoppelen van een netwerkbeveiligingsgroep naar of van een subnet of [netwerkinterface.](#associate-or-dissociate-a-network-security-group-to-or-from-a-subnet-or-network-interface)

#### <a name="commands"></a>Opdracht

| Hulpprogramma | Opdracht |
| ---- | ------- |
| Azure CLI | [az network nsg update](/cli/azure/network/nsg#az_network_nsg_update) |
| PowerShell | [Set-AzNetworkSecurityGroup](/powershell/module/az.network/set-aznetworksecuritygroup) |

### <a name="associate-or-dissociate-a-network-security-group-to-or-from-a-subnet-or-network-interface"></a>Een netwerkbeveiligingsgroep koppelen aan of loskoppelen van een subnet of netwerkinterface

Zie [Een netwerkbeveiligingsgroep](virtual-network-network-interface.md#associate-or-dissociate-a-network-security-group)koppelen aan of een netwerkbeveiligingsgroep loskoppelen van een netwerkinterface als u een netwerkbeveiligingsgroep wilt koppelen aan of loskoppelen van een netwerkinterface. Zie Subnetinstellingen wijzigen als u een netwerkbeveiligingsgroep wilt koppelen aan of een netwerkbeveiligingsgroep wilt loskoppelen [van een subnet.](virtual-network-manage-subnet.md#change-subnet-settings)

### <a name="delete-a-network-security-group"></a>Een netwerkbeveiligingsgroep verwijderen

Als een netwerkbeveiligingsgroep is gekoppeld aan subnetten of netwerkinterfaces, kan deze niet worden verwijderd. Koppel een netwerkbeveiligingsgroep los van alle subnetten en netwerkinterfaces voordat u deze verwijdert.

1. Ga naar de [Azure Portal](https://portal.azure.com) uw netwerkbeveiligingsgroepen weer te bieden. Zoek en selecteer **Netwerkbeveiligingsgroepen.**

2. Selecteer de naam van de netwerkbeveiligingsgroep die u wilt verwijderen.

3. Selecteer Verwijderen op de werkbalk van de **netwerkbeveiligingsgroep.** Selecteer vervolgens **Ja** in het bevestigingsvenster.

#### <a name="commands"></a>Opdracht

| Hulpprogramma | Opdracht |
| ---- | ------- |
| Azure CLI | [az network nsg delete](/cli/azure/network/nsg#az_network_nsg_delete) |
| PowerShell | [Remove-AzNetworkSecurityGroup](/powershell/module/az.network/remove-aznetworksecuritygroup) |

## <a name="work-with-security-rules"></a>Werken met beveiligingsregels

Een netwerkbeveiligingsgroep bevat nul of meer beveiligingsregels. U kunt alle maken, [weergeven,](#view-all-security-rules) [details van weergeven,](#view-details-of-a-security-rule) [wijzigen](#change-a-security-rule)en [een](#delete-a-security-rule) beveiligingsregel verwijderen.

### <a name="create-a-security-rule"></a>Een beveiligingsregel maken

Er is een limiet voor het aantal regels per netwerkbeveiligingsgroep dat u voor elke Azure-locatie en elk azure-abonnement kunt maken. Zie Limieten, quota's en beperkingen [voor Azure-abonnementen en -service voor meer informatie.](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)

1. Ga naar de [Azure Portal](https://portal.azure.com) uw netwerkbeveiligingsgroepen weer te bieden. Zoek en selecteer **Netwerkbeveiligingsgroepen.**

2. Selecteer de naam van de netwerkbeveiligingsgroep waar u een beveiligingsregel aan wilt toevoegen.

3. Kies in de menubalk van de netwerkbeveiligingsgroep **Inkomende beveiligingsregels** of **Uitgaande beveiligingsregels.**

    Er worden verschillende bestaande regels vermeld, waaronder regels die u mogelijk niet hebt toegevoegd. Wanneer u een netwerkbeveiligingsgroep maakt, worden er verschillende standaardbeveiligingsregels in gemaakt. Zie Standaardbeveiligingsregels [voor meer informatie.](./network-security-groups-overview.md#default-security-rules)  U kunt standaardbeveiligingsregels niet verwijderen, maar u kunt ze wel overschrijven met regels met een hogere prioriteit.

4. <a name="security-rule-settings"></a>Selecteer **Toevoegen.** Selecteer of voeg waarden toe voor de volgende instellingen en selecteer vervolgens **OK:**

    | Instelling | Waarde | Details |
    | ------- | ----- | ------- |
    | **Bron** | Een van de volgende:<ul><li>**Alle**</li><li>**IP-adressen**</li><li>**Servicetag** (beveiligingsregel voor binnenkomende gegevens) of **VirtualNetwork** (uitgaande beveiligingsregel)</li><li>**&nbsp;Toepassingsbeveiligingsgroep &nbsp;**</li></ul> | <p>Als u **IP-adressen kiest,** moet u ook **bron-IP-adressen/CIDR-bereik opgeven.**</p><p>Als u kiest voor **Servicetag,** kunt u ook een **bronservicetag kiezen.**</p><p>Als u **Toepassingsbeveiligingsgroep kiest,** moet u ook een bestaande toepassingsbeveiligingsgroep kiezen. Als u **Toepassingsbeveiligingsgroep kiest** voor zowel **Bron** als **Doel,** moeten de netwerkinterfaces binnen beide toepassingsbeveiligingsgroepen zich in hetzelfde virtuele netwerk.</p> |
    | **BRON-IP-adressen/CIDR-adresbereiken** | Een door komma's scheidingstekenslijst met IP-adressen en CIDR-adresbereiken (Classless Interdomain Routing) | <p>Deze instelling wordt weergegeven als u **Bron wijzigt in** **IP-adressen.** U moet één waarde of een door komma's gescheiden lijst met meerdere waarden opgeven. Een voorbeeld van meerdere waarden is `10.0.0.0/16, 192.188.1.1` . Er gelden limieten voor het aantal waarden dat u kunt opgeven. Zie Azure-limieten [voor meer informatie.](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)</p><p>Als het IP-adres dat u opgeeft is toegewezen aan een Azure-VM, geeft u het privé-IP-adres op, niet het openbare IP-adres. Azure verwerkt beveiligingsregels nadat het openbare IP-adres heeft omgezet in een privé-IP-adres voor inkomende beveiligingsregels, maar voordat een privé-IP-adres wordt omgezet in een openbaar IP-adres voor uitgaande regels. Zie IP-adrestypen voor meer informatie over openbare en [privé-IP-adressen](./public-ip-addresses.md)in Azure.</p> |
    | **Bronservicetag** | Een servicetag uit de vervolgkeuzelijst | Deze optionele instelling wordt weergegeven als u **Bron instelt** op **Servicetag** voor een beveiligingsregel voor binnenkomende gegevens. Een servicetag is een vooraf gedefinieerde id voor een categorie IP-adressen. Zie Servicetags voor meer informatie over beschikbare servicetags en wat elke tag [vertegenwoordigt.](./network-security-groups-overview.md#service-tags) |
    | **Beveiligingsgroep voor brontoepassing** | Een bestaande toepassingsbeveiligingsgroep | Deze instelling wordt weergegeven als u Bron **instelt** op **Toepassingsbeveiligingsgroep.** Selecteer een toepassingsbeveiligingsgroep die zich in dezelfde regio bevindt als de netwerkinterface. Meer informatie over het [maken van een toepassingsbeveiligingsgroep.](#create-an-application-security-group) |
    | **Poortbereiken van bron** | Een van de volgende:<ul><li>Eén poort, zoals `80`</li><li>Een bereik van poorten, zoals `1024-65535`</li><li>Een door komma's gescheiden lijst met enkele poorten en/of poortbereiken, zoals `80, 1024-65535`</li><li>Een sterretje ( `*` ) om verkeer op elke poort toe te staan</li></ul> | Met deze instelling geeft u de poorten op waarop de regel verkeer toestaat of afkent. Er gelden limieten voor het aantal poorten dat u kunt opgeven. Zie Azure-limieten [voor meer informatie.](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) |
    | **Doel** | Een van de volgende:<ul><li>**Alle**</li><li>**IP-adressen**</li><li>**Servicetag** (uitgaande beveiligingsregel) of **VirtualNetwork** (beveiligingsregel voor binnenkomende gegevens)</li><li>**&nbsp;Toepassingsbeveiligingsgroep &nbsp;**</li></ul> | <p>Als u **IP-adressen kiest,** geeft u ook **doel-IP-adressen/CIDR-bereiken op.**</p><p>Als u **VirtualNetwork kiest,** wordt verkeer naar alle IP-adressen binnen de adresruimte van het virtuele netwerk toegestaan. **VirtualNetwork** is een servicetag.</p><p>Als u **Toepassingsbeveiligingsgroep selecteert,** moet u vervolgens een bestaande toepassingsbeveiligingsgroep selecteren. Meer informatie over het [maken van een toepassingsbeveiligingsgroep.](#create-an-application-security-group)</p> |
    | **DOEL-IP-adressen/CIDR-bereiken** | Een door komma's scheidingstekenslijst met IP-adressen en CIDR-adresbereiken | <p>Deze instelling wordt weergegeven als u Doel **wijzigt in** **IP-adressen.** Net als **bij bron-** en **bron-IP-adressen/CIDR-adresbereiken,** kunt u één of meerdere adressen of bereik opgeven. Er gelden limieten voor het getal dat u kunt opgeven. Zie Azure-limieten [voor meer informatie.](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)</p><p>Als het IP-adres dat u opgeeft, is toegewezen aan een Azure-VM, moet u ervoor zorgen dat u het privé-IP-adres opgeeft, niet het openbare IP-adres. Azure verwerkt beveiligingsregels nadat het openbare IP-adres heeft omgezet in een privé-IP-adres voor inkomende beveiligingsregels, maar voordat Azure een privé-IP-adres vertaalt naar een openbaar IP-adres voor uitgaande regels. Zie IP-adrestypen voor meer informatie over openbare en [privé-IP-adressen](./public-ip-addresses.md)in Azure.</p> |
    | **Doelservicetag** | Een servicetag uit de vervolgkeuzelijst | Deze optionele instelling wordt weergegeven als u **Doel wijzigt in** **Servicetag** voor een uitgaande beveiligingsregel. Een servicetag is een vooraf gedefinieerde id voor een categorie IP-adressen. Zie Servicetags voor meer informatie over beschikbare servicetags en wat elke tag [vertegenwoordigt.](./network-security-groups-overview.md#service-tags) |
    | **Doeltoepassingsbeveiligingsgroep** | Een bestaande toepassingsbeveiligingsgroep | Deze instelling wordt weergegeven als u Doel **instelt op** **Toepassingsbeveiligingsgroep**. Selecteer een toepassingsbeveiligingsgroep die zich in dezelfde regio bevindt als de netwerkinterface. Meer informatie over het [maken van een toepassingsbeveiligingsgroep.](#create-an-application-security-group) |
    | **Poortbereiken van doel** | Een van de volgende:<ul><li>Eén poort, zoals `80`</li><li>Een bereik van poorten, zoals `1024-65535`</li><li>Een door komma's gescheiden lijst met enkele poorten en/of poortbereiken, zoals `80, 1024-65535`</li><li>Een sterretje ( `*` ) om verkeer op elke poort toe te staan</li></ul> | Net als **bij Bronpoortbereiken** kunt u enkele of meerdere poorten en bereik opgeven. Er gelden limieten voor het getal dat u kunt opgeven. Zie Azure-limieten [voor meer informatie.](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) |
    | **Protocol** | **Alle,** **TCP,** **UDP** of **ICMP** | U kunt de regel beperken tot de Transmission Control Protocol (TCP), UDP (User Datagram Protocol) of Internet Control Message Protocol (ICMP). De standaardwaarde is dat de regel van toepassing is op alle protocollen. |
    | **Actie** | **Toestaan** of **weigeren** | Met deze instelling geeft u aan of deze regel de toegang voor de opgegeven bron- en doelconfiguratie toestaat of niet. |
    | **Priority** | Een waarde tussen 100 en 4096 die uniek is voor alle beveiligingsregels binnen de netwerkbeveiligingsgroep | In Azure worden beveiligingsregels in volgorde van prioriteit verwerkt. Hoe lager het getal, hoe hoger de prioriteit. We raden u aan om een hiaat tussen prioriteitsnummers te laten wanneer u regels maakt, zoals 100, 200 en 300. Door hiaten achter te laten, kunt u in de toekomst eenvoudiger regels toevoegen, zodat u ze een hogere of lagere prioriteit kunt geven dan bestaande regels. |
    | **Naam** | Een unieke naam voor de regel binnen de netwerkbeveiligingsgroep | De naam mag maximaal 80 tekens lang zijn. Het moet beginnen met een letter of cijfer en moet eindigen met een letter, cijfer of onderstrepingsteken. De naam mag alleen letters, cijfers, onderstrepingstekens, punten of afbreekstreepingen bevatten. |
    | **Beschrijving** | Een tekstbeschrijving | U kunt eventueel een tekstbeschrijving voor de beveiligingsregel opgeven. De beschrijving mag niet langer zijn dan 140 tekens. |

#### <a name="commands"></a>Opdracht

| Hulpprogramma | Opdracht |
| ---- | ------- |
| Azure CLI | [az network nsg rule create](/cli/azure/network/nsg/rule#az_network_nsg_rule_create) |
| PowerShell | [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig) |

### <a name="view-all-security-rules"></a>Alles weergeven beveiligingsregels

Een netwerkbeveiligingsgroep bevat nul of meer regels. Zie Overzicht van netwerkbeveiligingsgroep voor meer informatie over de informatie die wordt weergegeven bij het weergeven [van regels.](./network-security-groups-overview.md)

1. Ga naar de [Azure Portal](https://portal.azure.com) om de regels van een netwerkbeveiligingsgroep weer te bieden. Zoek en selecteer **Netwerkbeveiligingsgroepen.**

2. Selecteer de naam van de netwerkbeveiligingsgroep waar u de regels voor wilt weergeven.

3. Kies in de menubalk van de netwerkbeveiligingsgroep **Inkomende beveiligingsregels** of **Uitgaande beveiligingsregels.**

De lijst bevat alle regels die u hebt gemaakt en de standaardbeveiligingsregels van de [netwerkbeveiligingsgroep.](./network-security-groups-overview.md#default-security-rules)

#### <a name="commands"></a>Opdracht

| Hulpprogramma | Opdracht |
| ---- | ------- |
| Azure CLI | [az network nsg rule list](/cli/azure/network/nsg/rule#az_network_nsg_rule_list) |
| PowerShell | [Get-AzNetworkSecurityRuleConfig](/powershell/module/az.network/get-aznetworksecurityruleconfig) |

### <a name="view-details-of-a-security-rule"></a>Details van een beveiligingsregel weergeven

1. Ga naar de [Azure Portal](https://portal.azure.com) om de regels van een netwerkbeveiligingsgroep weer te bieden. Zoek en selecteer **Netwerkbeveiligingsgroepen.**

2. Selecteer de naam van de netwerkbeveiligingsgroep voor wie u de details van een regel wilt weergeven.

3. Kies in de menubalk van de netwerkbeveiligingsgroep **Inkomende beveiligingsregels** of **Uitgaande beveiligingsregels.**

4. Selecteer de regel voor wie u de details wilt weergeven. Zie Instellingen voor beveiligingsregel voor een uitleg [van alle instellingen.](#security-rule-settings)

    > [!NOTE]
    > Deze procedure is alleen van toepassing op een aangepaste beveiligingsregel. Dit werkt niet als u een standaardbeveiligingsregel kiest.

#### <a name="commands"></a>Opdracht

| Hulpprogramma | Opdracht |
| ---- | ------- |
| Azure CLI | [az network nsg rule show](/cli/azure/network/nsg/rule#az_network_nsg_rule_show) |
| PowerShell | [Get-AzNetworkSecurityRuleConfig](/powershell/module/az.network/get-aznetworksecurityruleconfig) |

### <a name="change-a-security-rule"></a>Een beveiligingsregel wijzigen

1. Voltooi de stappen in [Details van een beveiligingsregel weergeven.](#view-details-of-a-security-rule)

2. Wijzig de instellingen naar behoefte en selecteer vervolgens **Opslaan.** Zie Beveiligingsregelinstellingen voor een uitleg [van alle instellingen.](#security-rule-settings)

    > [!NOTE]
    > Deze procedure is alleen van toepassing op een aangepaste beveiligingsregel. U mag geen standaardbeveiligingsregel wijzigen.

#### <a name="commands"></a>Opdracht

| Hulpprogramma | Opdracht |
| ---- | ------- |
| Azure CLI | [az network nsg rule update](/cli/azure/network/nsg/rule#az_network_nsg_rule_update) |
| PowerShell | [Set-AzNetworkSecurityRuleConfig](/powershell/module/az.network/set-aznetworksecurityruleconfig) |

### <a name="delete-a-security-rule"></a>Een beveiligingsregel verwijderen

1. Voltooi de stappen in [Details van een beveiligingsregel weergeven.](#view-details-of-a-security-rule)

2. Selecteer **Verwijderen** en selecteer vervolgens **Ja**.

    > [!NOTE]
    > Deze procedure is alleen van toepassing op een aangepaste beveiligingsregel. U mag geen standaardbeveiligingsregel verwijderen.

#### <a name="commands"></a>Opdracht

| Hulpprogramma | Opdracht |
| ---- | ------- |
| Azure CLI | [az network nsg rule delete](/cli/azure/network/nsg/rule#az_network_nsg_rule_delete) |
| PowerShell | [Remove-AzNetworkSecurityRuleConfig](/powershell/module/az.network/remove-aznetworksecurityruleconfig) |

## <a name="work-with-application-security-groups"></a>Werken met toepassingsbeveiligingsgroepen

Een toepassingsbeveiligingsgroep bevat nul of meer netwerkinterfaces. Zie [Toepassingsbeveiligingsgroepen voor meer informatie.](./network-security-groups-overview.md#application-security-groups) Alle netwerkinterfaces in een toepassingsbeveiligingsgroep moeten zich in hetzelfde virtuele netwerk hebben. Zie Een netwerkinterface toevoegen aan een toepassingsbeveiligingsgroep voor meer informatie over het toevoegen van een netwerkinterface aan een [toepassingsbeveiligingsgroep.](virtual-network-network-interface.md#add-to-or-remove-from-application-security-groups)

### <a name="create-an-application-security-group"></a>Een toepassingsbeveiligingsgroep maken

1. Selecteer in het menu van [Azure Portal](https://portal.azure.com) of op de **startpagina** de optie **Een resource maken**.

2. Voer in het zoekvak *Toepassingsbeveiligingsgroep in.*

3. Selecteer maken op de pagina Toepassingsbeveiligingsgroep.  

4. Stel op **de pagina Een toepassingsbeveiligingsgroep** maken op het tabblad **Basisinstellingen** waarden in voor de volgende instellingen:

    | Instelling | Bewerking |
    | --- | --- |
    | **Abonnement** | Kies uw abonnement. |
    | **Resourcegroep** | Kies een bestaande resourcegroep of selecteer **Nieuwe maken om** een nieuwe resourcegroep te maken. |
    | **Naam** | Voer een unieke tekstreeks in een resourcegroep in. |
    | **Regio** | Kies de locatie die u wilt. |

5. Selecteer **Controleren + maken**.

6. Selecteer op **het tabblad Beoordelen en** maken het bericht **Validatie** geslaagd en selecteer **Maken.**

#### <a name="commands"></a>Opdracht

| Hulpprogramma | Opdracht |
| ---- | ------- |
| Azure CLI | [az network asg create](/cli/azure/network/asg#az_network_asg_create) |
| PowerShell | [New-AzApplicationSecurityGroup](/powershell/module/az.network/new-azapplicationsecuritygroup) |

### <a name="view-all-application-security-groups"></a>Alles weergeven toepassingsbeveiligingsgroepen maken

Ga naar de [Azure Portal](https://portal.azure.com) om uw toepassingsbeveiligingsgroepen weer te bieden. Zoek en selecteer **Toepassingsbeveiligingsgroepen.** In Azure Portal wordt een lijst met uw toepassingsbeveiligingsgroepen weergegeven.

#### <a name="commands"></a>Opdracht

| Hulpprogramma | Opdracht |
| ---- | ------- |
| Azure CLI | [az network asg list](/cli/azure/network/asg#az_network_asg_list) |
| PowerShell | [Get-AzApplicationSecurityGroup](/powershell/module/az.network/get-azapplicationsecuritygroup) |

### <a name="view-details-of-a-specific-application-security-group"></a>Details van een specifieke toepassingsbeveiligingsgroep weergeven

1. Ga naar de [Azure Portal](https://portal.azure.com) een toepassingsbeveiligingsgroep weer te bieden. Zoek en selecteer **Toepassingsbeveiligingsgroepen.**

2. Selecteer de naam van de toepassingsbeveiligingsgroep waar u de details van wilt bekijken.

#### <a name="commands"></a>Opdracht

| Hulpprogramma | Opdracht |
| ---- | ------- |
| Azure CLI | [az network asg show](/cli/azure/network/asg#az_network_asg_show) |
| PowerShell | [Get-AzApplicationSecurityGroup](/powershell/module/az.network/get-azapplicationsecuritygroup) |

### <a name="change-an-application-security-group"></a>Een toepassingsbeveiligingsgroep wijzigen

1. Ga naar de [Azure Portal](https://portal.azure.com) een toepassingsbeveiligingsgroep weer te bieden. Zoek en selecteer **Toepassingsbeveiligingsgroepen.**

2. Selecteer de naam van de toepassingsbeveiligingsgroep die u wilt wijzigen.

3. Selecteer **Wijzigen** naast de instelling die u wilt wijzigen. U kunt bijvoorbeeld Tags toevoegen of **verwijderen,** of u kunt de **resourcegroep** of het abonnement **wijzigen.**

    > [!NOTE]
    > U kunt de locatie niet wijzigen.

    In de menubalk kunt u ook **Toegangsbeheer (IAM) selecteren.** Op de **pagina Toegangsbeheer (IAM)** kunt u machtigingen toewijzen aan of verwijderen voor de toepassingsbeveiligingsgroep.

#### <a name="commands"></a>Opdracht

| Hulpprogramma | Opdracht |
| ---- | ------- |
| Azure CLI | [az network asg update](/cli/azure/network/asg#az_network_asg_update) |
| PowerShell | Geen PowerShell-cmdlet |

### <a name="delete-an-application-security-group"></a>Een toepassingsbeveiligingsgroep verwijderen

U kunt een toepassingsbeveiligingsgroep niet verwijderen als deze netwerkinterfaces bevat. Als u alle netwerkinterfaces uit de toepassingsbeveiligingsgroep wilt verwijderen, wijzigt u de instellingen van de netwerkinterface of verwijdert u de netwerkinterfaces. Zie Toevoegen aan of verwijderen uit [toepassingsbeveiligingsgroepen of](virtual-network-network-interface.md#add-to-or-remove-from-application-security-groups) Een [netwerkinterface verwijderen](virtual-network-network-interface.md#delete-a-network-interface)voor meer informatie.

1. Ga naar de [Azure Portal](https://portal.azure.com) om uw toepassingsbeveiligingsgroepen te beheren. Zoek en selecteer **Toepassingsbeveiligingsgroepen.**

2. Selecteer de naam van de toepassingsbeveiligingsgroep die u wilt verwijderen.

3. Selecteer **Verwijderen** en selecteer vervolgens Ja **om** de toepassingsbeveiligingsgroep te verwijderen.

#### <a name="commands"></a>Opdracht

| Hulpprogramma | Opdracht |
| ---- | ------- |
| Azure CLI | [az network asg delete](/cli/azure/network/asg#az_network_asg_delete) |
| PowerShell | [Remove-AzApplicationSecurityGroup](/powershell/module/az.network/remove-azapplicationsecuritygroup) |

## <a name="permissions"></a>Machtigingen

Als u taken wilt uitvoeren voor netwerkbeveiligingsgroepen, beveiligingsregels en toepassingsbeveiligingsgroepen, [](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) moet uw account zijn toegewezen aan de rol Netwerkbijdrager of aan een Aangepaste rol die de juiste machtigingen krijgt toegewezen, zoals vermeld in de volgende tabellen: [](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)

### <a name="network-security-group"></a>Netwerkbeveiligingsgroep

| Bewerking                                                        |   Name                                                                |
|-------------------------------------------------------------- |   -------------------------------------------                         |
| Microsoft.Network/networkSecurityGroups/read                  |   Netwerkbeveiligingsgroep op halen                                          |
| Microsoft.Network/networkSecurityGroups/write                 |   Netwerkbeveiligingsgroep maken of bijwerken                             |
| Microsoft.Network/networkSecurityGroups/delete                |   Netwerkbeveiligingsgroep verwijderen                                       |
| Microsoft.Network/networkSecurityGroups/join/action           |   Een netwerkbeveiligingsgroep koppelen aan een subnet of netwerkinterface 


>[!NOTE]
> Als u `write` bewerkingen wilt uitvoeren op een netwerkbeveiligingsgroep, moet het abonnementsaccount ten minste machtigingen hebben voor de `read` resourcegroep, samen met de `Microsoft.Network/networkSecurityGroups/write` machtiging .


### <a name="network-security-group-rule"></a>Regel voor netwerkbeveiligingsgroep

| Bewerking                                                        |   Name                                                                |
|-------------------------------------------------------------- |   -------------------------------------------                         |
| Microsoft.Network/networkSecurityGroups/securityRules/read            |   Regel op halen                                                            |
| Microsoft.Network/networkSecurityGroups/securityRules/write           |   Regel maken of bijwerken                                               |
| Microsoft.Network/networkSecurityGroups/securityRules/delete          |   Regel verwijderen                                                         |

### <a name="application-security-group"></a>Toepassingsbeveiligingsgroep

| Bewerking                                                                     | Name                                                     |
| --------------------------------------------------------------             | -------------------------------------------              |
| Microsoft.Network/applicationSecurityGroups/joinIpConfiguration/action     | Een IP-configuratie aan een toepassingsbeveiligingsgroep deelnemen|
| Microsoft.Network/applicationSecurityGroups/joinNetworkSecurityRule/action | Een beveiligingsregel aan een toepassingsbeveiligingsgroep deelnemen    |
| Microsoft.Network/applicationSecurityGroups/read                           | Een toepassingsbeveiligingsgroep krijgen                        |
| Microsoft.Network/applicationSecurityGroups/write                          | Een toepassingsbeveiligingsgroep maken of bijwerken           |
| Microsoft.Network/applicationSecurityGroups/delete                         | Een toepassingsbeveiligingsgroep verwijderen                     |

## <a name="next-steps"></a>Volgende stappen

- Een netwerk- of toepassingsbeveiligingsgroep maken met [powershell-](powershell-samples.md) of Azure CLI-voorbeeldscripts of Azure [](cli-samples.md) [Resource Manager sjablonen](template-samples.md)
- Definities voor [virtuele Azure Policy maken](./policy-reference.md) en toewijzen
