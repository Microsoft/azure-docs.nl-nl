---
title: Persoonlijke DNS-zones en records beveiligen-Azure DNS
description: In dit leer traject kunt u aan de slag met het beveiligen van privé-DNS-zones en-record sets in Microsoft Azure DNS.
services: dns
author: asudbring
ms.service: dns
ms.topic: how-to
ms.date: 02/18/2020
ms.author: allensu
ms.openlocfilehash: 6e77f983f3600ae7c54d7d88f2ad1a006d7325fa
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/10/2021
ms.locfileid: "102614187"
---
# <a name="how-to-protect-private-dns-zones-and-records"></a>Persoonlijke DNS-zones en-records beveiligen

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Privé-DNS zones en records zijn essentiële bronnen. Het verwijderen van een DNS-zone of één DNS-record kan leiden tot een onderbreking van de service. Het is belang rijk dat DNS-zones en-records worden beschermd tegen onbevoegde of onopzettelijke wijzigingen.

In dit artikel wordt uitgelegd hoe u met Azure DNS uw persoonlijke DNS-zones en-records kunt beveiligen tegen dergelijke wijzigingen.  We hebben twee krachtige effecten functies toegepast door Azure Resource Manager: [op rollen gebaseerd toegangs beheer (Azure RBAC)](../role-based-access-control/overview.md) en [resource vergrendelingen](../azure-resource-manager/management/lock-resources.md)van Azure.

## <a name="azure-role-based-access-control"></a>Op rollen gebaseerd toegangsbeheer voor Azure

Met op rollen gebaseerd toegangs beheer op basis van Azure (Azure RBAC) kunt u verfijnd toegang beheren voor Azure-gebruikers,-groepen en-resources. Met Azure RBAC kunt u het toegangs niveau verlenen dat gebruikers nodig hebben. Zie [Wat is Azure-op rollen gebaseerd toegangs beheer (Azure RBAC)](../role-based-access-control/overview.md)voor meer informatie over hoe Azure RBAC u helpt bij het beheren van de toegang.

### <a name="the-private-dns-zone-contributor-role"></a>De rol van de Privé-DNS zone Inzender

De rol Inzender voor Privé-DNS zone is een ingebouwde rol voor het beheren van privé-DNS-resources. Met deze rol die wordt toegepast op een gebruiker of groep, kunnen ze privé-DNS-resources beheren.

De *myPrivateDNS* van de resource groep bevat vijf zones voor Contoso Corporation. Het verlenen van de DNS-beheerder Privé-DNS machtigingen voor de zone Inzender voor die resource groep biedt volledige controle over deze DNS-zones. Hiermee wordt voor komen dat onnodige machtigingen worden verleend. De DNS-beheerder kan geen virtuele machines maken of stoppen.

De eenvoudigste manier om Azure RBAC-machtigingen toe te wijzen, is [via de Azure Portal](../role-based-access-control/role-assignments-portal.md).  

Open **toegangs beheer (IAM)** voor de resource groep, selecteer **toevoegen** en selecteer vervolgens de rol van de **privé-DNS zone Inzender** . Selecteer de vereiste gebruikers of groepen om machtigingen te verlenen.

![Resource groeps niveau Azure RBAC via de Azure Portal](./media/dns-protect-private-zones-recordsets/rbac1.png)

Machtigingen kunnen ook worden [verleend met behulp van Azure PowerShell](../role-based-access-control/role-assignments-powershell.md):

```azurepowershell-interactive
# Grant 'Private DNS Zone Contributor' permissions to all zones in a resource group

$rsg = "<resource group name>"
$usr = "<user email address>"
$rol = "Private DNS Zone Contributor"

New-AzRoleAssignment -SignInName $usr -RoleDefinitionName $rol -ResourceGroupName $rsg
```

De overeenkomstige opdracht is ook [beschikbaar via de Azure cli](../role-based-access-control/role-assignments-cli.md):

```azurecli-interactive
# Grant 'Private DNS Zone Contributor' permissions to all zones in a resource group

az role assignment create \
--assignee "<user email address>" \
--role "Private DNS Zone Contributor" \
--resource-group "<resource group name>"
```

### <a name="private-zone-level-azure-rbac"></a>Privé zone niveau Azure RBAC

Azure RBAC-regels kunnen worden toegepast op een abonnement, een resource groep of een afzonderlijke resource. De bron kan een afzonderlijke DNS-zone of een afzonderlijke recordset zijn.

De *myPrivateDNS* van de resource groep bevat bijvoorbeeld de zone *private.contoso.com* en een *Customers.private.contoso.com* subzone. Er worden CNAME-records gemaakt voor elk klant account. Aan het beheerders account dat wordt gebruikt voor het beheren van CNAME-records zijn machtigingen toegewezen voor het maken van records in de zone *Customers.private.contoso.com* . Het account kan alleen *Customers.private.contoso.com* beheren.

Azure RBAC-machtigingen op zone niveau kunnen worden verleend via de Azure Portal.  Open **toegangs beheer (IAM)** voor de zone, selecteer **toevoegen** en selecteer vervolgens de rol van de **privé-DNS zone Inzender** . Selecteer de vereiste gebruikers of groepen om machtigingen te verlenen.

![DNS-zone niveau Azure RBAC via de Azure Portal](./media/dns-protect-private-zones-recordsets/rbac2.png)

Machtigingen kunnen ook worden [verleend met behulp van Azure PowerShell](../role-based-access-control/role-assignments-powershell.md):

```azurepowershell-interactive
# Grant 'Private DNS Zone Contributor' permissions to a specific zone

$rsg = "<resource group name>"
$usr = "<user email address>"
$zon = "<zone name>"
$rol = "Private DNS Zone Contributor"
$rsc = "Microsoft.Network/privateDnsZones"

New-AzRoleAssignment -SignInName $usr -RoleDefinitionName $rol -ResourceGroupName $rsg -ResourceName $zon -ResourceType $rsc
```

De overeenkomstige opdracht is ook [beschikbaar via de Azure cli](../role-based-access-control/role-assignments-cli.md):

```azurecli-interactive
# Grant 'Private DNS Zone Contributor' permissions to a specific zone

az role assignment create \
--assignee <user email address> \
--role "Private DNS Zone Contributor" \
--scope "/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Network/privateDnsZones/<zone name>/"
```

### <a name="record-set-level-azure-rbac"></a>Record sets niveau Azure RBAC

Machtigingen worden toegepast op het niveau van de recordset.  De gebruiker heeft controle over de benodigde vermeldingen en kan geen andere wijzigingen aanbrengen.

Record-set level Azure RBAC-machtigingen kunnen worden geconfigureerd via de Azure Portal, met behulp van de knop **Access Control (IAM)** op de pagina Recordset:

![Scherm afbeelding toont de Access Control knop (I A M).](./media/dns-protect-private-zones-recordsets/rbac3.png)

![Scherm afbeelding toont Access Control waarvoor roltoewijzing toevoegen is geselecteerd.](./media/dns-protect-private-zones-recordsets/rbac4.png)

Record-set level Azure RBAC-machtigingen kunnen ook worden [verleend met behulp van Azure PowerShell](../role-based-access-control/role-assignments-powershell.md):

```azurepowershell-interactive
# Grant permissions to a specific record set

$usr = "<user email address>"
$rol = "Private DNS Zone Contributor"
$sco = 
"/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Network/privateDnsZones/<zone name>/<record type>/<record name>"

New-AzRoleAssignment -SignInName $usr -RoleDefinitionName $rol -Scope $sco
```

De overeenkomstige opdracht is ook [beschikbaar via de Azure cli](../role-based-access-control/role-assignments-cli.md):

```azurecli-interactive
# Grant permissions to a specific record set

az role assignment create \
--assignee "<user email address>" \
--role "Private DNS Zone Contributor" \
--scope "/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Network/privateDnsZones/<zone name>/<record type>/<record name>"
```

### <a name="custom-roles"></a>Aangepaste rollen

Met de ingebouwde rol Inzender voor Privé-DNS zone kunt u volledige controle over een DNS-bron. Het is mogelijk om uw eigen aangepaste Azure-rollen te bouwen om nauw keurige controle te leveren.

Het account dat wordt gebruikt voor het beheren van CNAME, is gemachtigd om alleen CNAME-records te beheren. Het account kan geen records van andere typen wijzigen. Het account kan geen bewerkingen op zone niveau uitvoeren, zoals het verwijderen van een zone.

In het volgende voor beeld ziet u een aangepaste roldefinitie voor het beheer van CNAME-records:

```json
{
    "Name": "Private DNS CNAME Contributor",
    "Id": "",
    "IsCustom": true,
    "Description": "Can manage DNS CNAME records only.",
    "Actions": [
        "Microsoft.Network/privateDnsZones/CNAME/*",
        "Microsoft.Network/privateDNSZones/read",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
    ],
    "NotActions": [
    ],
    "AssignableScopes": [
        "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e"
    ]
}
```

De eigenschap actions definieert de volgende DNS-specifieke machtigingen:

* `Microsoft.Network/privateDnsZones/CNAME/*` Hiermee verleent u volledige controle over CNAME-records
* `Microsoft.Network/privateDNSZones/read` verleent toestemming om persoonlijke DNS-zones te lezen, maar niet om ze te wijzigen, zodat u de zone kunt zien waarin de CNAME wordt gemaakt.

> [!NOTE]
> Een aangepaste Azure-rol gebruiken om te voor komen dat record sets worden verwijderd terwijl ze nog steeds kunnen worden bijgewerkt, is geen effectief besturings element. Zo voor komt u dat record sets worden verwijderd, maar niet om te voor komen dat ze worden gewijzigd.  Toegestane wijzigingen zijn het toevoegen en verwijderen van records uit de recordset, inclusief het verwijderen van alle records om een lege recordset te verlaten. Dit heeft hetzelfde effect als het verwijderen van de recordset uit het oogpunt van een DNS-oplossing.

Aangepaste roldefinities kunnen momenteel niet worden gedefinieerd via de Azure Portal. Een aangepaste rol op basis van deze roldefinitie kan worden gemaakt met behulp van Azure PowerShell:

```azurepowershell-interactive
# Create new role definition based on input file

New-AzRoleDefinition -InputFile <file path>
```

Het kan ook worden gemaakt via de Azure CLI:

```azurecli-interactive
# Create new role definition based on input file

az role create -inputfile <file path>
```

De rol kan vervolgens op dezelfde manier worden toegewezen als ingebouwde rollen, zoals eerder in dit artikel is beschreven.

Zie [aangepaste rollen voor Azure](../role-based-access-control/custom-roles.md)voor meer informatie over het maken, beheren en toewijzen van aangepaste rollen.

## <a name="resource-locks"></a>Resourcevergrendelingen

Azure Resource Manager ondersteunt een ander type beveiligings controle, de mogelijkheid om resources te vergren delen. Resource vergrendelingen worden toegepast op de resource en zijn van kracht voor alle gebruikers en rollen. Zie voor meer informatie [Resources vergrendelen met Azure Resource Manager](../azure-resource-manager/management/lock-resources.md).

Er zijn twee typen bron vergrendeling: **CanNotDelete** en **alleen-lezen**. Deze vergrendelings typen kunnen worden toegepast op een Privé-DNS zone of op een afzonderlijke Recordset.  In de volgende secties worden verschillende algemene scenario's beschreven en wordt uitgelegd hoe u deze kunt ondersteunen met resource vergrendelingen.

### <a name="protecting-against-all-changes"></a>Beveiligen tegen alle wijzigingen

Als u wilt voor komen dat er wijzigingen worden aangebracht, past u een alleen-lezen vergrendeling toe op de zone. Met deze vergren deling voor komt u dat er nieuwe record sets worden gemaakt en dat bestaande record sets worden gewijzigd of verwijderd.

Resource vergrendelingen op zone niveau kunnen worden gemaakt via de Azure Portal.  Selecteer op de pagina DNS-zone de optie **vergren** delen en selecteer **+ toevoegen**:

![Resource vergrendeling op zone niveau via de Azure Portal](./media/dns-protect-private-zones-recordsets/locks1.png)

Resource vergrendelingen op zone niveau kunnen ook worden gemaakt via [Azure PowerShell](/powershell/module/az.resources/new-azresourcelock):

```azurepowershell-interactive
# Lock a DNS zone

$lvl = "<lock level>"
$lnm = "<lock name>"
$rsc = "<zone name>"
$rty = "Microsoft.Network/privateDnsZones"
$rsg = "<resource group name>"

New-AzResourceLock -LockLevel $lvl -LockName $lnm -ResourceName $rsc -ResourceType $rty -ResourceGroupName $rsg
```

De overeenkomstige opdracht is ook [beschikbaar via de Azure cli](/cli/azure/lock#az-lock-create):

```azurecli-interactive
# Lock a DNS zone

az lock create \
--lock-type "<lock level>" \
--name "<lock name>" \
--resource-name "<zone name>" \
--namespace "Microsoft.Network" \
--resource-type "privateDnsZones" \
--resource-group "<resource group name>"
```
### <a name="protecting-individual-records"></a>Afzonderlijke records beveiligen

Als u wilt voor komen dat een bestaande DNS-record wordt ingesteld tegen wijziging, moet u een alleen-lezen vergrendeling Toep assen op de recordset.

> [!NOTE]
> Het Toep assen van een CanNotDelete-vergren deling op een recordset is geen effectief besturings element. Hiermee wordt voor komen dat de recordset wordt verwijderd, maar wordt niet voor komen dat deze wordt gewijzigd.  Toegestane wijzigingen zijn het toevoegen en verwijderen van records uit de recordset, inclusief het verwijderen van alle records om een lege recordset te verlaten. Dit heeft hetzelfde effect als het verwijderen van de recordset uit het oogpunt van een DNS-oplossing.

Resource vergrendelingen op record sets kunnen momenteel alleen worden geconfigureerd met behulp van Azure PowerShell.  Ze worden niet ondersteund in de Azure Portal of Azure CLI.

Azure PowerShell

```azurepowershell-interactive
# Lock a DNS record set

$lvl = "<lock level>"
$lnm = "<lock name>"
$rnm = "<zone name>/<record set name>"
$rty = "Microsoft.Network/privateDnsZones"
$rsg = "<resource group name>"

New-AzResourceLock -LockLevel $lvl -LockName $lnm -ResourceName $rnm -ResourceType $rty -ResourceGroupName $rsg
```
### <a name="protecting-against-zone-deletion"></a>Beveiligen tegen zone verwijdering

Wanneer een zone wordt verwijderd in Azure DNS, worden alle record sets in de zone verwijderd.  Deze bewerking kan niet ongedaan worden gemaakt. Het per ongeluk verwijderen van een kritieke zone heeft mogelijk een aanzienlijke invloed op het bedrijf.  Het is belang rijk om te beschermen tegen het onbedoeld verwijderen van een zone.

Wanneer u een CanNotDelete-vergren deling toepast op een zone, voor komt u dat de zone wordt verwijderd. De vergren delingen worden overgenomen door onderliggende resources. Een vergren deling voor komt dat record sets in de zone worden verwijderd. Zoals beschreven in de bovenstaande opmerking, is het niet effectief omdat records nog steeds uit de bestaande record sets kunnen worden verwijderd.

U kunt ook een CanNotDelete-vergren deling Toep assen op een recordset in de zone, zoals de SOA-Recordset. De zone wordt niet verwijderd zonder ook de record sets te verwijderen. Deze vergren deling beschermt tegen zone verwijdering, terwijl wel toestaat dat record sets in de zone vrij kunnen worden gewijzigd. Als er wordt geprobeerd om de zone te verwijderen, Azure Resource Manager dit verwijderen gedetecteerd. Als u de SOA-recordset verwijdert, wordt de oproep ook verwijderd Azure Resource Manager de aanroep geblokkeerd omdat de SOA is vergrendeld.  Geen record sets worden verwijderd.

Met de volgende Power shell-opdracht wordt een CanNotDelete-vergren deling voor de SOA-record van de opgegeven zone gemaakt:

```azurepowershell-interactive
# Protect against zone delete with CanNotDelete lock on the record set

$lvl = "CanNotDelete"
$lnm = "<lock name>"
$rnm = "<zone name>/@"
$rty = "Microsoft.Network/privateDnsZones/SOA"
$rsg = "<resource group name>"

New-AzResourceLock -LockLevel $lvl -LockName $lnm -ResourceName $rnm -ResourceType $rty -ResourceGroupName $rsg
```
Een andere optie om te voor komen dat onbedoeld zone wordt verwijderd met behulp van een aangepaste rol. Deze rol zorgt ervoor dat de accounts die worden gebruikt voor het beheren van uw zones, geen zone-verwijderings machtigingen hebben. 

Wanneer u een zone moet verwijderen, kunt u een verwijdering uit twee stappen afdwingen:

 - Ken eerst machtigingen voor het verwijderen van de zone toe
 - Ken vervolgens machtigingen toe om de zone te verwijderen.

De aangepaste rol werkt voor alle zones die worden gebruikt door deze accounts. Accounts met machtigingen voor zone verwijdering, zoals de eigenaar van het abonnement, kunnen nog steeds per ongeluk een zone verwijderen.

Het is mogelijk om zowel benaderingen-resource vergrendelingen als aangepaste rollen te gebruiken als een ingrijpende benadering van DNS-zone beveiliging.

## <a name="next-steps"></a>Volgende stappen

* Zie [Wat is Azure Role-based Access Control (Azure RBAC)](../role-based-access-control/overview.md)voor meer informatie over het werken met Azure RBAC.
* Zie [resources vergren delen met Azure Resource Manager](../azure-resource-manager/management/lock-resources.md)voor meer informatie over het werken met resource vergrendelingen.
