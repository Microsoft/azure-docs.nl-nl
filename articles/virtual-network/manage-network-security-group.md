---
title: Een Azure-netwerk beveiligings groep maken, wijzigen of verwijderen
titlesuffix: Azure Virtual Network
description: Meer informatie over beveiligings regels en het maken, wijzigen of verwijderen van een netwerk beveiligings groep.
services: virtual-network
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/13/2020
ms.author: kumud
ms.openlocfilehash: d42e0f5594fdde55f1b4183a806e388658e86dc3
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/14/2021
ms.locfileid: "98222935"
---
# <a name="create-change-or-delete-a-network-security-group"></a>Een netwerkbeveiligingsgroep maken, wijzigen of verwijderen

Met beveiligings regels in netwerk beveiligings groepen kunt u het type netwerk verkeer filteren dat in en uit de subnetten van het virtuele netwerk en netwerk interfaces kan stromen. Zie overzicht van de [netwerk beveiligings groep](./network-security-groups-overview.md)voor meer informatie over netwerk beveiligings groepen. Voltooi vervolgens de zelf studie [netwerk verkeer filteren](tutorial-filter-network-traffic.md) om te profiteren van de netwerk beveiligings groepen.

## <a name="before-you-begin"></a>Voordat u begint

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Als u er nog geen hebt, stelt u een Azure-account in met een actief abonnement. [Gratis een account maken](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) Voer een van de volgende taken uit voordat u de rest van dit artikel start:

- **Portal gebruikers**: Meld u aan bij de [Azure Portal](https://portal.azure.com) met uw Azure-account.

- **Power shell-gebruikers**: Voer de opdrachten uit in de [Azure Cloud shell](https://shell.azure.com/powershell)of voer Power shell uit vanaf uw computer. Azure Cloud Shell is een gratis interactieve shell waarmee u de stappen in dit artikel kunt uitvoeren. In deze shell zijn algemene Azure-hulpprogramma's vooraf geïnstalleerd en geconfigureerd voor gebruik met uw account. Zoek de vervolg keuzelijst **omgeving selecteren** op het tabblad Azure Cloud shell browser en kies vervolgens **Power shell** als deze optie nog niet is geselecteerd.

    Als u Power shell lokaal uitvoert, gebruikt u Azure PowerShell module versie 1.0.0 of hoger. Voer `Get-Module -ListAvailable Az.Network` uit om te kijken welke versie is geïnstalleerd. Als u PowerShell wilt upgraden, raadpleegt u [De Azure PowerShell-module installeren](/powershell/azure/install-az-ps). Voer `Connect-AzAccount` uit om een verbinding op te zetten met Azure.

- **Azure-opdracht regel interface (CLI)-gebruikers**: Voer de opdrachten uit in de [Azure Cloud shell](https://shell.azure.com/bash)of voer de CLI uit vanaf uw computer. Gebruik Azure CLI versie 2.0.28 of hoger als u de Azure CLI lokaal uitvoert. Voer `az --version` uit om te kijken welke versie is geïnstalleerd. Zie [Azure CLI installeren](/cli/azure/install-azure-cli) als u de CLI wilt installeren of een upgrade wilt uitvoeren. Voer `az login` uit om een verbinding op te zetten met Azure.

Het account waarmee u zich aanmeldt of verbinding maakt met Azure, moet worden toegewezen aan de [rol netwerk bijdrager](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) of aan een [aangepaste rol](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) waaraan de juiste acties zijn toegewezen die worden vermeld in [machtigingen](#permissions).

## <a name="work-with-network-security-groups"></a>Met netwerkbeveiligingsgroepen werken

U kunt alle [gegevens van](#view-details-of-a-network-security-group)een netwerk beveiligings groep maken, [weer geven, bekijken](#view-all-network-security-groups), [wijzigen](#change-a-network-security-group)en [verwijderen](#delete-a-network-security-group) . U kunt ook een netwerk beveiligings groep [koppelen aan of loskoppelen](#associate-or-dissociate-a-network-security-group-to-or-from-a-subnet-or-network-interface) van een netwerk interface of subnet.

### <a name="create-a-network-security-group"></a>Een netwerkbeveiligingsgroep maken

Er is een limiet voor het aantal netwerk beveiligings groepen dat u kunt maken voor elke Azure-locatie en-abonnement. Zie [Azure-abonnement en service limieten, quota's en beperkingen](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)voor meer informatie.

1. Selecteer in het menu van [Azure Portal](https://portal.azure.com) of op de **startpagina** de optie **Een resource maken**.

2. Selecteer **netwerken** en selecteer vervolgens **netwerk beveiligings groep**.

3. Stel op de pagina **netwerk beveiligings groep maken** op het tabblad **basis beginselen** waarden in voor de volgende instellingen:

    | Instelling | Bewerking |
    | --- | --- |
    | **Abonnement** | Kies uw abonnement. |
    | **Resourcegroep** | Kies een bestaande resource groep of selecteer **nieuwe maken** om een nieuwe resource groep te maken. |
    | **Naam** | Voer een unieke teken reeks in een resource groep in. |
    | **Regio** | Kies de gewenste locatie. |

4. Selecteer **Controleren + maken**.

5. Nadat u het bericht **validatie geslaagd** hebt weer gegeven, selecteert u **maken**.

#### <a name="commands"></a>Opdracht

| Hulpprogramma | Opdracht |
| ---- | ------- |
| Azure CLI | [az network nsg create](/cli/azure/network/nsg#az-network-nsg-create) |
| PowerShell | [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup) |

### <a name="view-all-network-security-groups"></a>Alle netwerk beveiligings groepen weer geven

Ga naar de [Azure Portal](https://portal.azure.com) om uw netwerk beveiligings groepen weer te geven. Zoek en selecteer **netwerk beveiligings groepen**. De lijst met netwerk beveiligings groepen wordt weer gegeven voor uw abonnement.

#### <a name="commands"></a>Opdracht

| Hulpprogramma | Opdracht |
| ---- | ------- |
| Azure CLI | [AZ Network NSG List](/cli/azure/network/nsg#az-network-nsg-list) |
| PowerShell | [Get-AzNetworkSecurityGroup](/powershell/module/az.network/get-aznetworksecuritygroup) |

### <a name="view-details-of-a-network-security-group"></a>Details van een netwerk beveiligings groep weer geven

1. Ga naar de [Azure Portal](https://portal.azure.com) om uw netwerk beveiligings groepen weer te geven. Zoek en selecteer **netwerk beveiligings groepen**.

2. Selecteer de naam van de netwerk beveiligings groep.

In de menu balk van de netwerk beveiligings groep, onder **instellingen**, kunt u de **regels voor binnenkomende beveiliging**, **uitgaande beveiligings regels**, **netwerk interfaces** en **subnetten** weer geven waaraan de netwerk beveiligings groep is gekoppeld.

Onder **bewaking** kunt u **Diagnostische instellingen** in-of uitschakelen. Onder **ondersteuning en probleem oplossing** kunt u de **juiste beveiligings regels** weer geven. Zie voor meer informatie [Diagnostische logboek registratie voor een netwerk beveiligings groep](virtual-network-nsg-manage-log.md) en [een diagnose van een VM-netwerk verkeer filter probleem](diagnose-network-traffic-filter-problem.md).

Zie de volgende artikelen voor meer informatie over de algemene Azure-instellingen die worden weer gegeven:

- [Activiteitenlogboek](../azure-monitor/platform/platform-logs-overview.md)
- [Toegangsbeheer (IAM)](../role-based-access-control/overview.md)
- [Tags](../azure-resource-manager/management/tag-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Vergrendelingen](../azure-resource-manager/management/lock-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Automation-script](../azure-resource-manager/templates/export-template-portal.md)

#### <a name="commands"></a>Opdracht

| Hulpprogramma | Opdracht |
| ---- | ------- |
| Azure CLI | [AZ Network NSG show](/cli/azure/network/nsg#az-network-nsg-show) |
| PowerShell | [Get-AzNetworkSecurityGroup](/powershell/module/az.network/get-aznetworksecuritygroup) |

### <a name="change-a-network-security-group"></a>Een netwerk beveiligings groep wijzigen

1. Ga naar de [Azure Portal](https://portal.azure.com) om uw netwerk beveiligings groepen weer te geven. Zoek en selecteer **netwerk beveiligings groepen**.

2. Selecteer de naam van de netwerk beveiligings groep die u wilt wijzigen.

De meest voorkomende wijzigingen zijn het [toevoegen van een beveiligings regel](#create-a-security-rule), het [verwijderen van een regel](#delete-a-security-rule)en het [koppelen of loskoppelen van een netwerk beveiligings groep naar of van een subnet of netwerk interface](#associate-or-dissociate-a-network-security-group-to-or-from-a-subnet-or-network-interface).

#### <a name="commands"></a>Opdracht

| Hulpprogramma | Opdracht |
| ---- | ------- |
| Azure CLI | [AZ Network NSG update](/cli/azure/network/nsg#az-network-nsg-update) |
| PowerShell | [Set-AzNetworkSecurityGroup](/powershell/module/az.network/set-aznetworksecuritygroup) |

### <a name="associate-or-dissociate-a-network-security-group-to-or-from-a-subnet-or-network-interface"></a>Een netwerk beveiligings groep koppelen aan of loskoppelen van een subnet of netwerk interface

Zie [een netwerk beveiligings groep koppelen aan of een netwerk beveiligings groep loskoppelen van een netwerk interface](virtual-network-network-interface.md#associate-or-dissociate-a-network-security-group)om een netwerk beveiligings groep aan te koppelen aan of om een netwerk beveiligings groep te loskoppelen van een netwerk interface. Zie [subnet-instellingen wijzigen](virtual-network-manage-subnet.md#change-subnet-settings)als u een netwerk beveiligings groep aan wilt koppelen of een netwerk beveiligings groep wilt loskoppelen van een subnet.

### <a name="delete-a-network-security-group"></a>Een netwerk beveiligings groep verwijderen

Als een netwerk beveiligings groep is gekoppeld aan subnetten of netwerk interfaces, kan deze niet worden verwijderd. Verbreken van een netwerk beveiligings groep van alle subnetten en netwerk interfaces voordat u deze verwijdert.

1. Ga naar de [Azure Portal](https://portal.azure.com) om uw netwerk beveiligings groepen weer te geven. Zoek en selecteer **netwerk beveiligings groepen**.

2. Selecteer de naam van de netwerk beveiligings groep die u wilt verwijderen.

3. Selecteer **verwijderen** op de werk balk van de netwerk beveiligings groep. Selecteer vervolgens **Ja** in het bevestigingsvenster.

#### <a name="commands"></a>Opdracht

| Hulpprogramma | Opdracht |
| ---- | ------- |
| Azure CLI | [AZ Network NSG Delete](/cli/azure/network/nsg#az-network-nsg-delete) |
| PowerShell | [Remove-AzNetworkSecurityGroup](/powershell/module/az.network/remove-aznetworksecuritygroup) |

## <a name="work-with-security-rules"></a>Werken met beveiligings regels

Een netwerk beveiligings groep bevat geen of meer beveiligings regels. U kunt alle regels maken, [weer geven](#view-all-security-rules), [Details van](#view-details-of-a-security-rule)een beveiligings regel bekijken, [wijzigen](#change-a-security-rule)en [verwijderen](#delete-a-security-rule) .

### <a name="create-a-security-rule"></a>Een beveiligings regel maken

Er is een limiet voor het aantal regels per netwerk beveiligings groep dat u kunt maken voor elke Azure-locatie en-abonnement. Zie [Azure-abonnement en service limieten, quota's en beperkingen](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)voor meer informatie.

1. Ga naar de [Azure Portal](https://portal.azure.com) om uw netwerk beveiligings groepen weer te geven. Zoek en selecteer **netwerk beveiligings groepen**.

2. Selecteer de naam van de netwerk beveiligings groep waaraan u een beveiligings regel wilt toevoegen.

3. Kies in de menu balk van de netwerk beveiligings groep de optie **regels voor binnenkomende beveiliging** of **uitgaande beveiligings regels**.

    Er worden verschillende bestaande regels weer gegeven, inclusief enkele die u mogelijk niet hebt toegevoegd. Wanneer u een netwerk beveiligings groep maakt, worden er diverse standaard beveiligings regels in gemaakt. Zie [standaard beveiligings regels](./network-security-groups-overview.md#default-security-rules)voor meer informatie.  Het is niet mogelijk om standaard beveiligings regels te verwijderen, maar u kunt ze vervangen door regels met een hogere prioriteit.

4. <a name="security-rule-settings"></a>Selecteer **toevoegen**. Selecteer of voeg waarden toe voor de volgende instellingen en selecteer **OK**:

    | Instelling | Waarde | Details |
    | ------- | ----- | ------- |
    | **Bron** | Een van de volgende opties:<ul><li>**Alle**</li><li>**IP-adressen**</li><li>**Servicetag (binnenkomende** beveiligings regel) of **VirtualNetwork** (uitgaande beveiligings regel)</li><li>**Toepassings &nbsp; beveiligings &nbsp; groep**</li></ul> | <p>Als u **IP-adressen** kiest, moet u ook **bron-IP-adressen/CIDR-bereiken** opgeven.</p><p>Als u **service label** kiest, kunt u ook een **bron service label** kiezen.</p><p>Als u **toepassings beveiligings groep** kiest, moet u ook een bestaande toepassings beveiligings groep kiezen. Als u **toepassings beveiligings groep** voor zowel de **bron** als de **bestemming** kiest, moeten de netwerk interfaces binnen beide toepassings beveiligings groepen zich in hetzelfde virtuele netwerk bevallen.</p> |
    | **IP-adressen van bron/CIDR-bereiken** | Een door komma's gescheiden lijst met IP-adressen en CIDR-bereiken (Classloos interdomein-route ring) | <p>Deze instelling wordt weer gegeven als u **bron** wijzigt in **IP-adressen**. U moet een enkele waarde of een door komma's gescheiden lijst met meerdere waarden opgeven. Een voor beeld van meerdere waarden is `10.0.0.0/16, 192.188.1.1` . Er zijn limieten voor het aantal waarden dat u kunt opgeven. Zie [Azure-limieten](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)voor meer informatie.</p><p>Als het door u opgegeven IP-adres is toegewezen aan een virtuele machine van Azure, geeft u het persoonlijke IP-adres op, niet het open bare IP-adres. Azure verwerkt beveiligings regels nadat het open bare IP-adres is omgezet naar een privé-IP-adres voor binnenkomende beveiligings regels, maar voordat een privé-IP-adres wordt omgezet in een openbaar IP-adres voor uitgaande regels. Zie [IP-adres typen](./public-ip-addresses.md)voor meer informatie over open bare en privé-IP-adressen in Azure.</p> |
    | **Bronservicetag** | Een servicetag in de vervolg keuzelijst | Deze optionele instelling wordt weer gegeven als u **bron** -naar- **service-tag** voor een binnenkomende beveiligings regel instelt. Een servicetag is een vooraf gedefinieerde id voor een categorie met IP-adressen. Zie [service Tags](./network-security-groups-overview.md#service-tags)voor meer informatie over de beschik bare service tags en wat elke tag vertegenwoordigt. |
    | **Beveiligings groep voor de bron toepassing** | Een bestaande toepassings beveiligings groep | Deze instelling wordt weer gegeven als u **bron** instelt op **toepassings beveiligings groep**. Selecteer een toepassings beveiligings groep die zich in dezelfde regio bevindt als de netwerk interface. Meer informatie over het [maken van een toepassings beveiligings groep](#create-an-application-security-group). |
    | **Poortbereiken van bron** | Een van de volgende opties:<ul><li>Eén poort, zoals `80`</li><li>Een bereik van poorten, zoals `1024-65535`</li><li>Een door komma's gescheiden lijst met enkele poorten en/of poortbereiken, zoals `80, 1024-65535`</li><li>Een asterisk ( `*` ) om verkeer toe te staan op een wille keurige poort</li></ul> | Met deze instelling geeft u de poorten op waarop de regel verkeer toestaat of weigert. Er zijn limieten voor het aantal poorten dat u kunt opgeven. Zie [Azure-limieten](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)voor meer informatie. |
    | **Doel** | Een van de volgende opties:<ul><li>**Alle**</li><li>**IP-adressen**</li><li>**Servicetag (uitgaande** beveiligings regel) of **VirtualNetwork** (binnenkomende beveiligings regel)</li><li>**Toepassings &nbsp; beveiligings &nbsp; groep**</li></ul> | <p>Als u **IP-adressen** kiest, moet u ook **doel-IP-adressen/CIDR-bereiken** opgeven.</p><p>Als u **VirtualNetwork** kiest, wordt verkeer toegestaan voor alle IP-adressen binnen de adres ruimte van het virtuele netwerk. **VirtualNetwork** is een servicetag.</p><p>Als u **toepassings beveiligings groep** selecteert, moet u vervolgens een bestaande toepassings beveiligings groep selecteren. Meer informatie over het [maken van een toepassings beveiligings groep](#create-an-application-security-group).</p> |
    | **Doel-IP-adressen/CIDR-bereiken** | Een door komma's gescheiden lijst met IP-adressen en CIDR-bereiken | <p>Deze instelling wordt weer gegeven als u de **bestemming** wijzigt in **IP-adressen**. Net als **bron** **-en bron-IP-adressen/CIDR-bereiken**, kunt u één of meerdere adressen of bereiken opgeven. Er zijn limieten voor het aantal dat u kunt opgeven. Zie [Azure-limieten](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)voor meer informatie.</p><p>Als het door u opgegeven IP-adres is toegewezen aan een virtuele machine van Azure, moet u ervoor zorgen dat u het privé-IP-adres opgeeft, niet het open bare IP-adressen. Azure verwerkt beveiligings regels nadat het open bare IP-adres is omgezet naar een privé-IP-adres voor binnenkomende beveiligings regels, maar voordat Azure een privé-IP-adres omzet in een openbaar IP-adres voor uitgaande regels. Zie [IP-adres typen](./public-ip-addresses.md)voor meer informatie over open bare en privé-IP-adressen in Azure.</p> |
    | **Doelservicetag** | Een servicetag in de vervolg keuzelijst | Deze optionele instelling wordt weer gegeven als  u doel **wijzigt in servicetag voor een** uitgaande beveiligings regel. Een servicetag is een vooraf gedefinieerde id voor een categorie met IP-adressen. Zie [service Tags](./network-security-groups-overview.md#service-tags)voor meer informatie over de beschik bare service tags en wat elke tag vertegenwoordigt. |
    | **Beveiligings groep voor de doel toepassing** | Een bestaande toepassings beveiligings groep | Deze instelling wordt weer gegeven als u **doel** instelt op **toepassings beveiligings groep**. Selecteer een toepassings beveiligings groep die zich in dezelfde regio bevindt als de netwerk interface. Meer informatie over het [maken van een toepassings beveiligings groep](#create-an-application-security-group). |
    | **Poortbereiken van doel** | Een van de volgende opties:<ul><li>Eén poort, zoals `80`</li><li>Een bereik van poorten, zoals `1024-65535`</li><li>Een door komma's gescheiden lijst met enkele poorten en/of poortbereiken, zoals `80, 1024-65535`</li><li>Een asterisk ( `*` ) om verkeer toe te staan op een wille keurige poort</li></ul> | Net als bij **bron poortbereiken** kunt u enkele of meerdere poorten en bereiken opgeven. Er zijn limieten voor het aantal dat u kunt opgeven. Zie [Azure-limieten](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)voor meer informatie. |
    | **Protocol** | **Any**, **TCP**, **UDP** of **ICMP** | U kunt de regel beperken tot de Transmission Control Protocol (TCP), User Data gram Protocol (UDP) of Internet Control Message Protocol (ICMP). De standaard waarde is voor de regel die op alle protocollen moet worden toegepast. |
    | **Actie** | **Toestaan** of **weigeren** | Met deze instelling geeft u op of deze regel de toegang voor de opgegeven bron-en doel configuratie toestaat of weigert. |
    | **Priority** | Een waarde tussen 100 en 4096 die uniek is voor alle beveiligings regels in de netwerk beveiligings groep | Azure verwerkt beveiligings regels in volg orde van prioriteit. Hoe lager het getal, des te hoger de prioriteit. We raden u aan een hiaat tussen prioriteits nummers te laten wanneer u regels maakt, zoals 100, 200 en 300. Als u hiaten hebt, is het eenvoudiger om regels in de toekomst toe te voegen, zodat u ze een hogere of lagere prioriteit kunt geven dan bestaande regels. |
    | **Naam** | Een unieke naam voor de regel in de netwerk beveiligings groep | De naam mag Maxi maal 80 tekens lang zijn. De naam moet beginnen met een letter of cijfer en moet eindigen met een letter, cijfer of onderstrepings teken. De naam mag alleen letters, cijfers, onderstrepings tekens, punten of afbreek streepjes bevatten. |
    | **Beschrijving** | Een tekst beschrijving | U kunt eventueel een beschrijving van de tekst voor de beveiligings regel opgeven. De beschrijving mag niet langer zijn dan 140 tekens. |

#### <a name="commands"></a>Opdracht

| Hulpprogramma | Opdracht |
| ---- | ------- |
| Azure CLI | [az network nsg rule create](/cli/azure/network/nsg/rule#az-network-nsg-rule-create) |
| PowerShell | [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig) |

### <a name="view-all-security-rules"></a>Alle beveiligings regels weer geven

Een netwerk beveiligings groep bevat nul of meer regels. Zie [overzicht van netwerk beveiligings groepen](./network-security-groups-overview.md)voor meer informatie over de informatie die wordt weer gegeven wanneer regels worden weer gegeven.

1. Ga naar de [Azure Portal](https://portal.azure.com) om de regels van een netwerk beveiligings groep weer te geven. Zoek en selecteer **netwerk beveiligings groepen**.

2. Selecteer de naam van de netwerk beveiligings groep waarvoor u de regels wilt weer geven.

3. Kies in de menu balk van de netwerk beveiligings groep de optie **regels voor binnenkomende beveiliging** of **uitgaande beveiligings regels**.

De lijst bevat de regels die u hebt gemaakt en de [standaard beveiligings regels](./network-security-groups-overview.md#default-security-rules)voor de netwerk beveiligings groep.

#### <a name="commands"></a>Opdracht

| Hulpprogramma | Opdracht |
| ---- | ------- |
| Azure CLI | [az network nsg rule list](/cli/azure/network/nsg/rule#az-network-nsg-rule-list) |
| PowerShell | [Get-AzNetworkSecurityRuleConfig](/powershell/module/az.network/get-aznetworksecurityruleconfig) |

### <a name="view-details-of-a-security-rule"></a>Details van een beveiligingsregel weergeven

1. Ga naar de [Azure Portal](https://portal.azure.com) om de regels van een netwerk beveiligings groep weer te geven. Zoek en selecteer **netwerk beveiligings groepen**.

2. Selecteer de naam van de netwerk beveiligings groep waarvoor u de details van een regel wilt weer geven.

3. Kies in de menu balk van de netwerk beveiligings groep de optie **regels voor binnenkomende beveiliging** of **uitgaande beveiligings regels**.

4. Selecteer de regel waarvan u de details wilt weer geven. Zie [beveiligings regel instellingen](#security-rule-settings)voor een uitleg van alle instellingen.

    > [!NOTE]
    > Deze procedure is alleen van toepassing op een aangepaste beveiligings regel. Als u een standaard beveiligings regel kiest, werkt deze niet.

#### <a name="commands"></a>Opdracht

| Hulpprogramma | Opdracht |
| ---- | ------- |
| Azure CLI | [AZ Network NSG Rule show](/cli/azure/network/nsg/rule#az-network-nsg-rule-show) |
| PowerShell | [Get-AzNetworkSecurityRuleConfig](/powershell/module/az.network/get-aznetworksecurityruleconfig) |

### <a name="change-a-security-rule"></a>Een beveiligings regel wijzigen

1. Volg de stappen in [Details van een beveiligings regel weer geven](#view-details-of-a-security-rule).

2. Wijzig de instellingen naar wens en selecteer vervolgens **Opslaan**. Zie [beveiligings regel instellingen](#security-rule-settings)voor een uitleg van alle instellingen.

    > [!NOTE]
    > Deze procedure is alleen van toepassing op een aangepaste beveiligings regel. Het is niet toegestaan om een standaard beveiligings regel te wijzigen.

#### <a name="commands"></a>Opdracht

| Hulpprogramma | Opdracht |
| ---- | ------- |
| Azure CLI | [az network nsg rule update](/cli/azure/network/nsg/rule#az-network-nsg-rule-update) |
| PowerShell | [Set-AzNetworkSecurityRuleConfig](/powershell/module/az.network/set-aznetworksecurityruleconfig) |

### <a name="delete-a-security-rule"></a>Een beveiligings regel verwijderen

1. Volg de stappen in [Details van een beveiligings regel weer geven](#view-details-of-a-security-rule).

2. Selecteer **Verwijderen** en selecteer vervolgens **Ja**.

    > [!NOTE]
    > Deze procedure is alleen van toepassing op een aangepaste beveiligings regel. U bent niet gemachtigd om een standaard beveiligings regel te verwijderen.

#### <a name="commands"></a>Opdracht

| Hulpprogramma | Opdracht |
| ---- | ------- |
| Azure CLI | [AZ Network NSG regel Delete](/cli/azure/network/nsg/rule#az-network-nsg-rule-delete) |
| PowerShell | [Remove-AzNetworkSecurityRuleConfig](/powershell/module/az.network/remove-aznetworksecurityruleconfig) |

## <a name="work-with-application-security-groups"></a>Werken met toepassingsbeveiligingsgroepen

Een toepassings beveiligings groep bevat geen of meer netwerk interfaces. Zie [toepassings beveiligings groepen](./network-security-groups-overview.md#application-security-groups)voor meer informatie. Alle netwerk interfaces in een toepassings beveiligings groep moeten zich in hetzelfde virtuele netwerk bevinden. Zie [een netwerk interface toevoegen aan een toepassings beveiligings groep](virtual-network-network-interface.md#add-to-or-remove-from-application-security-groups)voor meer informatie over het toevoegen van een netwerk interface aan een toepassings beveiligings groep.

### <a name="create-an-application-security-group"></a>Een toepassings beveiligings groep maken

1. Selecteer in het menu van [Azure Portal](https://portal.azure.com) of op de **startpagina** de optie **Een resource maken**.

2. Voer in het zoekvak *toepassings beveiligings groep* in.

3. Selecteer op de pagina **toepassings beveiligings groep** de optie **maken**.

4. Stel op de pagina **een toepassings beveiligings groep maken** onder het tabblad **basis beginselen** waarden in voor de volgende instellingen:

    | Instelling | Bewerking |
    | --- | --- |
    | **Abonnement** | Kies uw abonnement. |
    | **Resourcegroep** | Kies een bestaande resource groep of selecteer **nieuwe maken** om een nieuwe resource groep te maken. |
    | **Naam** | Voer een unieke teken reeks in een resource groep in. |
    | **Regio** | Kies de gewenste locatie. |

5. Selecteer **Controleren + maken**.

6. Klik op het tabblad **controleren en maken** , nadat u het bericht validatie is geslaagd hebt weer **gegeven** , en selecteer **maken**.

#### <a name="commands"></a>Opdracht

| Hulpprogramma | Opdracht |
| ---- | ------- |
| Azure CLI | [AZ Network ASG Create](/cli/azure/network/asg#az-network-asg-create) |
| PowerShell | [New-AzApplicationSecurityGroup](/powershell/module/az.network/new-azapplicationsecuritygroup) |

### <a name="view-all-application-security-groups"></a>Alle toepassings beveiligings groepen weer geven

Ga naar de [Azure Portal](https://portal.azure.com) om uw toepassings beveiligings groepen weer te geven. Zoek en selecteer **toepassings beveiligings groepen**. In de Azure Portal wordt een lijst met uw toepassings beveiligings groepen weer gegeven.

#### <a name="commands"></a>Opdracht

| Hulpprogramma | Opdracht |
| ---- | ------- |
| Azure CLI | [AZ Network ASG List](/cli/azure/network/asg#az-network-asg-list) |
| PowerShell | [Get-AzApplicationSecurityGroup](/powershell/module/az.network/get-azapplicationsecuritygroup) |

### <a name="view-details-of-a-specific-application-security-group"></a>Details van een specifieke toepassings beveiligings groep weer geven

1. Ga naar de [Azure Portal](https://portal.azure.com) om een toepassings beveiligings groep weer te geven. Zoek en selecteer **toepassings beveiligings groepen**.

2. Selecteer de naam van de toepassings beveiligings groep waarvan u de details wilt weer geven.

#### <a name="commands"></a>Opdracht

| Hulpprogramma | Opdracht |
| ---- | ------- |
| Azure CLI | [AZ Network ASG show](/cli/azure/network/asg#az-network-asg-show) |
| PowerShell | [Get-AzApplicationSecurityGroup](/powershell/module/az.network/get-azapplicationsecuritygroup) |

### <a name="change-an-application-security-group"></a>Een toepassings beveiligings groep wijzigen

1. Ga naar de [Azure Portal](https://portal.azure.com) om een toepassings beveiligings groep weer te geven. Zoek en selecteer **toepassings beveiligings groepen**.

2. Selecteer de naam van de toepassings beveiligings groep die u wilt wijzigen.

3. Selecteer **wijzigen** naast de instelling die u wilt wijzigen. U kunt bijvoorbeeld **Tags** toevoegen of verwijderen, of u kunt de **resource groep** of het **abonnement** wijzigen.

    > [!NOTE]
    > U kunt de locatie niet wijzigen.

    In de menu balk kunt u ook **toegangs beheer (IAM)** selecteren. U kunt op de pagina **toegangs beheer (IAM)** machtigingen toewijzen of verwijderen voor de toepassings beveiligings groep.

#### <a name="commands"></a>Opdracht

| Hulpprogramma | Opdracht |
| ---- | ------- |
| Azure CLI | [AZ Network ASG update](/cli/azure/network/asg#az-network-asg-update) |
| PowerShell | Geen Power shell-cmdlet |

### <a name="delete-an-application-security-group"></a>Een toepassings beveiligings groep verwijderen

U kunt een toepassings beveiligings groep niet verwijderen als deze netwerk interfaces bevat. Als u alle netwerk interfaces uit de toepassings beveiligings groep wilt verwijderen, wijzigt u de instellingen voor de netwerk interface of verwijdert u de netwerk interfaces. Zie [toevoegen aan of verwijderen uit toepassings beveiligings groepen](virtual-network-network-interface.md#add-to-or-remove-from-application-security-groups) of [een netwerk interface verwijderen](virtual-network-network-interface.md#delete-a-network-interface)voor meer informatie.

1. Ga naar de [Azure Portal](https://portal.azure.com) om uw toepassings beveiligings groepen te beheren. Zoek en selecteer **toepassings beveiligings groepen**.

2. Selecteer de naam van de toepassings beveiligings groep die u wilt verwijderen.

3. Selecteer **verwijderen** en selecteer vervolgens **Ja** om de toepassings beveiligings groep te verwijderen.

#### <a name="commands"></a>Opdracht

| Hulpprogramma | Opdracht |
| ---- | ------- |
| Azure CLI | [AZ Network ASG Delete](/cli/azure/network/asg#az-network-asg-delete) |
| PowerShell | [Remove-AzApplicationSecurityGroup](/powershell/module/az.network/remove-azapplicationsecuritygroup) |

## <a name="permissions"></a>Machtigingen

Om taken uit te voeren op netwerk beveiligings groepen, beveiligings regels en toepassings beveiligings groepen, moet uw account worden toegewezen aan de rol [netwerk bijdrager](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) of aan een [aangepaste rol](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) waaraan de juiste machtigingen zijn toegewezen, zoals vermeld in de volgende tabellen:

### <a name="network-security-group"></a>Netwerkbeveiligingsgroep

| Actie                                                        |   Naam                                                                |
|-------------------------------------------------------------- |   -------------------------------------------                         |
| Micro soft. Network/networkSecurityGroups/lezen                  |   Netwerk beveiligings groep ophalen                                          |
| Micro soft. Network/networkSecurityGroups/schrijven                 |   Een netwerk beveiligings groep maken of bijwerken                             |
| Micro soft. Network/networkSecurityGroups/verwijderen                |   Netwerk beveiligings groep verwijderen                                       |
| Micro soft. Network/networkSecurityGroups/samen voegen/actie           |   Een netwerk beveiligings groep aan een subnet of netwerk interface koppelen 

### <a name="network-security-group-rule"></a>Regel voor netwerk beveiligings groep

| Actie                                                        |   Naam                                                                |
|-------------------------------------------------------------- |   -------------------------------------------                         |
| Micro soft. Network/networkSecurityGroups/securityRules/lezen            |   Regel ophalen                                                            |
| Micro soft. Network/networkSecurityGroups/securityRules/schrijven           |   Regel maken of bijwerken                                               |
| Micro soft. Network/networkSecurityGroups/securityRules/verwijderen          |   Regel verwijderen                                                         |

### <a name="application-security-group"></a>Toepassingsbeveiligingsgroep

| Actie                                                                     | Naam                                                     |
| --------------------------------------------------------------             | -------------------------------------------              |
| Micro soft. Network/applicationSecurityGroups/joinIpConfiguration/Action     | Een IP-configuratie toevoegen aan een toepassings beveiligings groep|
| Micro soft. Network/applicationSecurityGroups/joinNetworkSecurityRule/Action | Een beveiligings regel toevoegen aan een toepassings beveiligings groep    |
| Micro soft. Network/applicationSecurityGroups/lezen                           | Een toepassings beveiligings groep ophalen                        |
| Micro soft. Network/applicationSecurityGroups/schrijven                          | Een toepassings beveiligings groep maken of bijwerken           |
| Micro soft. Network/applicationSecurityGroups/verwijderen                         | Een toepassings beveiligings groep verwijderen                     |

## <a name="next-steps"></a>Volgende stappen

- Een netwerk-of toepassings beveiligings groep maken met [Power shell](powershell-samples.md) -of [Azure cli](cli-samples.md) -voorbeeld scripts of Azure [Resource Manager-sjablonen](template-samples.md)
- [Azure Policy definities](./policy-reference.md) voor virtuele netwerken maken en toewijzen