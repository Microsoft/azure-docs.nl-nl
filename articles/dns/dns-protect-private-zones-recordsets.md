---
title: Privé-DNS-zones en -records beveiligen - Azure DNS
description: In dit leertraject gaat u aan de slag met het beveiligen van privé-DNS-zones en -recordsets in Microsoft Azure DNS.
services: dns
author: asudbring
ms.service: dns
ms.topic: how-to
ms.date: 02/18/2020
ms.author: allensu
ms.openlocfilehash: a68b40852750e124749ac838c50acae2212c4732
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107785848"
---
# <a name="how-to-protect-private-dns-zones-and-records"></a>Privé-DNS-zones en -records beveiligen

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Privé-DNS zones en records zijn essentiële resources. Het verwijderen van een DNS-zone of één DNS-record kan leiden tot een servicestoring. Het is belangrijk dat DNS-zones en -records zijn beveiligd tegen niet-geautoriseerde of onbedoelde wijzigingen.

In dit artikel wordt uitgelegd Azure DNS u uw privé-DNS-zones en -records kunt beveiligen tegen dergelijke wijzigingen.  We passen twee krachtige functies van Azure Resource Manager toe: op rollen gebaseerd toegangsbeheer [van Azure (Azure RBAC)](../role-based-access-control/overview.md) en [resourcevergrendelingen.](../azure-resource-manager/management/lock-resources.md)

## <a name="azure-role-based-access-control"></a>Op rollen gebaseerd toegangsbeheer voor Azure

Met op rollen gebaseerd toegangsbeheer van Azure (Azure RBAC) kunt u een fijnkeurig toegangsbeheer voor Azure-gebruikers, -groepen en -resources uitvoeren. Met Azure RBAC kunt u het toegangsniveau verlenen dat gebruikers nodig hebben. Zie Wat is op rollen gebaseerd toegangsbeheer [van Azure (Azure RBAC)](../role-based-access-control/overview.md)voor meer informatie over hoe u met Azure RBAC toegang kunt beheren.

### <a name="the-private-dns-zone-contributor-role"></a>De rol Privé-DNS Zone-inzender

De Privé-DNS Zone-inzender is een ingebouwde rol voor het beheren van privé-DNS-resources. Met deze rol die wordt toegepast op een gebruiker of groep, kunnen ze privé-DNS-resources beheren.

De resourcegroep *myPrivateDNS bevat* vijf zones voor Contoso Corporation. Door de DNS-beheerder Privé-DNS zone-inzendermachtigingen te verlenen aan die resourcegroep, hebt u volledige controle over deze DNS-zones. Zo voorkomt u dat u onnodige machtigingen verleent. De DNS-beheerder kan geen virtuele machines maken of stoppen.

De eenvoudigste manier om Azure RBAC-machtigingen toe te wijzen, is [via de Azure Portal.](../role-based-access-control/role-assignments-portal.md)  

Open **Toegangsbeheer (IAM)** voor de resourcegroep, selecteer **Toevoegen** en selecteer vervolgens de rol **Privé-DNS Zone-inzender.** Selecteer de vereiste gebruikers of groepen om machtigingen te verlenen.

![Azure RBAC op resourcegroepniveau via de Azure Portal](./media/dns-protect-private-zones-recordsets/rbac1.png)

Machtigingen kunnen ook worden [verleend met behulp Azure PowerShell](../role-based-access-control/role-assignments-powershell.md):

```azurepowershell-interactive
# Grant 'Private DNS Zone Contributor' permissions to all zones in a resource group

$rsg = "<resource group name>"
$usr = "<user email address>"
$rol = "Private DNS Zone Contributor"

New-AzRoleAssignment -SignInName $usr -RoleDefinitionName $rol -ResourceGroupName $rsg
```

De equivalente opdracht is [ook beschikbaar via de Azure CLI](../role-based-access-control/role-assignments-cli.md):

```azurecli-interactive
# Grant 'Private DNS Zone Contributor' permissions to all zones in a resource group

az role assignment create \
--assignee "<user email address>" \
--role "Private DNS Zone Contributor" \
--resource-group "<resource group name>"
```

### <a name="private-zone-level-azure-rbac"></a>Azure RBAC op privézoneniveau

Azure RBAC-regels kunnen worden toegepast op een abonnement, een resourcegroep of op een afzonderlijke resource. Deze resource kan een afzonderlijke DNS-zone of een afzonderlijke recordset zijn.

De resourcegroep *myPrivateDNS* bevat bijvoorbeeld de zone *private.contoso.com* en een subzone *customers.private.contoso.com*. CNAME-records worden gemaakt voor elk klantaccount. Het beheerdersaccount dat wordt gebruikt voor het beheren van CNAME-records, krijgt machtigingen voor het maken van records in *customers.private.contoso.com* zone. Het account kan alleen *customers.private.contoso.com* beheren.

Azure RBAC-machtigingen op zoneniveau kunnen worden verleend via de Azure Portal.  Open **Toegangsbeheer (IAM)** voor de zone, selecteer **Toevoegen** en selecteer vervolgens de rol **Privé-DNS Zone-inzender.** Selecteer de vereiste gebruikers of groepen om machtigingen te verlenen.

![Azure RBAC op DNS-zoneniveau via de Azure Portal](./media/dns-protect-private-zones-recordsets/rbac2.png)

Machtigingen kunnen ook worden [verleend met behulp Azure PowerShell](../role-based-access-control/role-assignments-powershell.md):

```azurepowershell-interactive
# Grant 'Private DNS Zone Contributor' permissions to a specific zone

$rsg = "<resource group name>"
$usr = "<user email address>"
$zon = "<zone name>"
$rol = "Private DNS Zone Contributor"
$rsc = "Microsoft.Network/privateDnsZones"

New-AzRoleAssignment -SignInName $usr -RoleDefinitionName $rol -ResourceGroupName $rsg -ResourceName $zon -ResourceType $rsc
```

De equivalente opdracht is [ook beschikbaar via de Azure CLI](../role-based-access-control/role-assignments-cli.md):

```azurecli-interactive
# Grant 'Private DNS Zone Contributor' permissions to a specific zone

az role assignment create \
--assignee <user email address> \
--role "Private DNS Zone Contributor" \
--scope "/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Network/privateDnsZones/<zone name>/"
```

### <a name="record-set-level-azure-rbac"></a>Azure RBAC op recordniveau

Machtigingen worden toegepast op het niveau van de recordset.  De gebruiker krijgt controle over de vermeldingen die hij nodig heeft en kan geen andere wijzigingen aanbrengen.

Azure RBAC-machtigingen op recordniveau kunnen worden geconfigureerd via de Azure Portal met behulp van de **knop Access Control (IAM)** op de pagina van de recordset:

![Schermopname van de Access Control knop (I A M).](./media/dns-protect-private-zones-recordsets/rbac3.png)

![Schermopname toont Access Control met Roltoewijzing toevoegen geselecteerd.](./media/dns-protect-private-zones-recordsets/rbac4.png)

Azure RBAC-machtigingen op recordniveau kunnen ook worden verleend met [behulp van Azure PowerShell](../role-based-access-control/role-assignments-powershell.md):

```azurepowershell-interactive
# Grant permissions to a specific record set

$usr = "<user email address>"
$rol = "Private DNS Zone Contributor"
$sco = 
"/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Network/privateDnsZones/<zone name>/<record type>/<record name>"

New-AzRoleAssignment -SignInName $usr -RoleDefinitionName $rol -Scope $sco
```

De equivalente opdracht is [ook beschikbaar via de Azure CLI](../role-based-access-control/role-assignments-cli.md):

```azurecli-interactive
# Grant permissions to a specific record set

az role assignment create \
--assignee "<user email address>" \
--role "Private DNS Zone Contributor" \
--scope "/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Network/privateDnsZones/<zone name>/<record type>/<record name>"
```

### <a name="custom-roles"></a>Aangepaste rollen

De ingebouwde rol Privé-DNS zone-inzender maakt volledige controle over een DNS-resource mogelijk. Het is mogelijk om uw eigen aangepaste Azure-rollen te bouwen om een fijner beheer te bieden.

Het account dat wordt gebruikt voor het beheren van CNAMEE's, krijgt alleen toestemming om CNAME-records te beheren. Het account kan geen records van andere typen wijzigen. Het account kan geen bewerkingen op zoneniveau uitvoeren, zoals zone verwijderen.

In het volgende voorbeeld ziet u een aangepaste roldefinitie voor het alleen beheren van CNAME-records:

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

De eigenschap Actions definieert de volgende DNS-specifieke machtigingen:

* `Microsoft.Network/privateDnsZones/CNAME/*` verleent volledige controle over CNAME-records
* `Microsoft.Network/privateDNSZones/read` verleent machtigingen voor het lezen van privé-DNS-zones, maar niet om ze te wijzigen, zodat u de zone kunt zien waarin de CNAME wordt gemaakt.

> [!NOTE]
> Het gebruik van een aangepaste Azure-rol om te voorkomen dat recordsets worden verwijderd terwijl ze nog steeds kunnen worden bijgewerkt, is geen effectief beheer. Hiermee voorkomt u dat recordsets worden verwijderd, maar dit voorkomt niet dat ze worden gewijzigd.  Toegestane wijzigingen omvatten het toevoegen en verwijderen van records uit de recordset, inclusief het verwijderen van alle records om een lege recordset te verlaten. Dit heeft hetzelfde effect als het verwijderen van de recordset vanuit het oogpunt van DNS-resolutie.

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

Zie Aangepaste Azure-rollen voor meer informatie over het maken, beheren en toewijzen van [aangepaste rollen.](../role-based-access-control/custom-roles.md)

## <a name="resource-locks"></a>Resourcevergrendelingen

Azure Resource Manager ondersteunt een ander type beveiligingsbeheer, de mogelijkheid om resources te vergrendelen. Resourcevergrendelingen worden toegepast op de resource en zijn effectief voor alle gebruikers en rollen. Zie voor meer informatie [Resources vergrendelen met Azure Resource Manager](../azure-resource-manager/management/lock-resources.md).

Er zijn twee typen resourcevergrendeling: **CanNotDelete** en **ReadOnly.** Deze vergrendelingstypen kunnen worden toegepast op een Privé-DNS zone of op een afzonderlijke recordset.  In de volgende secties worden verschillende veelvoorkomende scenario's beschreven en wordt beschreven hoe u deze kunt ondersteunen met behulp van resourcevergrendelingen.

### <a name="protecting-against-all-changes"></a>Bescherming tegen alle wijzigingen

Als u wilt voorkomen dat er wijzigingen worden aangebracht, moet u een ReadOnly-vergrendeling toepassen op de zone. Deze vergrendeling voorkomt dat er nieuwe recordsets worden gemaakt en bestaande recordsets kunnen worden gewijzigd of verwijderd.

Resourcevergrendelingen op zoneniveau kunnen worden gemaakt via de Azure Portal.  Selecteer vergrendelingen op de pagina DNS-zone **en** selecteer **vervolgens +Toevoegen:**

![Resourcevergrendelingen op zoneniveau via de Azure Portal](./media/dns-protect-private-zones-recordsets/locks1.png)

Resourcevergrendelingen op zoneniveau kunnen ook worden gemaakt via [Azure PowerShell:](/powershell/module/az.resources/new-azresourcelock)

```azurepowershell-interactive
# Lock a DNS zone

$lvl = "<lock level>"
$lnm = "<lock name>"
$rsc = "<zone name>"
$rty = "Microsoft.Network/privateDnsZones"
$rsg = "<resource group name>"

New-AzResourceLock -LockLevel $lvl -LockName $lnm -ResourceName $rsc -ResourceType $rty -ResourceGroupName $rsg
```

De equivalente opdracht is [ook beschikbaar via de Azure CLI](/cli/azure/lock#az_lock_create):

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

Als u wilt voorkomen dat een bestaande DNS-recordset wordt gewijzigd, moet u een ReadOnly-vergrendeling toepassen op de recordset.

> [!NOTE]
> Het toepassen van een CanNotDelete-vergrendeling op een recordset is geen effectief besturingselement. Hiermee voorkomt u dat de recordset wordt verwijderd, maar niet dat deze wordt gewijzigd.  Toegestane wijzigingen omvatten het toevoegen en verwijderen van records uit de recordset, inclusief het verwijderen van alle records om een lege recordset te verlaten. Dit heeft hetzelfde effect als het verwijderen van de recordset vanuit het oogpunt van DNS-resolutie.

Resourcevergrendelingen op recordsetniveau kunnen momenteel alleen worden geconfigureerd met behulp Azure PowerShell.  Ze worden niet ondersteund in de Azure Portal of Azure CLI.

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
### <a name="protecting-against-zone-deletion"></a>Beveiligen tegen zone-verwijdering

Wanneer een zone wordt verwijderd in Azure DNS, worden alle recordsets in de zone verwijderd.  Deze bewerking kan niet ongedaan worden gemaakt. Het per ongeluk verwijderen van een kritieke zone kan een aanzienlijke bedrijfsimpact hebben.  Het is belangrijk om te beveiligen tegen onbedoeld verwijderen van zones.

Het toepassen van een CanNotDelete-vergrendeling op een zone voorkomt dat de zone wordt verwijderd. Vergrendelingen worden overgenomen door onderliggende resources. Een vergrendeling voorkomt dat recordsets in de zone worden verwijderd. Zoals beschreven in de opmerking hierboven, is het ineffectief omdat records nog steeds kunnen worden verwijderd uit de bestaande recordsets.

Als alternatief kunt u een CanNotDelete-vergrendeling toepassen op een recordset in de zone, zoals de SOA-recordset. De zone wordt niet verwijderd zonder ook de recordsets te verwijderen. Deze vergrendeling beschermt tegen zonevergrendeling, terwijl recordsets binnen de zone vrij kunnen worden gewijzigd. Als er een poging wordt gedaan om de zone te verwijderen, Azure Resource Manager deze verwijdering gedetecteerd. Bij het verwijderen wordt ook de SOA-recordset verwijderd, Azure Resource Manager de aanroep wordt geblokkeerd omdat de SOA is vergrendeld.  Er worden geen recordsets verwijderd.

Met de volgende PowerShell-opdracht maakt u een CanNotDelete-vergrendeling voor de SOA-record van de opgegeven zone:

```azurepowershell-interactive
# Protect against zone delete with CanNotDelete lock on the record set

$lvl = "CanNotDelete"
$lnm = "<lock name>"
$rnm = "<zone name>/@"
$rty = "Microsoft.Network/privateDnsZones/SOA"
$rsg = "<resource group name>"

New-AzResourceLock -LockLevel $lvl -LockName $lnm -ResourceName $rnm -ResourceType $rty -ResourceGroupName $rsg
```
Een andere optie om onbedoeld verwijderen van zones te voorkomen, is door een aangepaste rol te gebruiken. Deze rol zorgt ervoor dat de accounts die worden gebruikt voor het beheren van uw zones geen zone-verwijdermachtigingen hebben. 

Wanneer u een zone moet verwijderen, kunt u een verwijder uit twee stappen afdwingen:

 - Verleen eerst machtigingen voor zone verwijderen
 - Verleen vervolgens machtigingen om de zone te verwijderen.

De aangepaste rol werkt voor alle zones die toegankelijk zijn voor deze accounts. Accounts met zone-verwijdermachtigingen, zoals de eigenaar van het abonnement, kunnen nog steeds per ongeluk een zone verwijderen.

Het is mogelijk om beide benaderingen , resourcevergrendelingen en aangepaste rollen, tegelijkertijd te gebruiken als een diepgaande verdedigingsbenadering voor DNS-zonebeveiliging.

## <a name="next-steps"></a>Volgende stappen

* Zie Wat is op rollen gebaseerd toegangsbeheer van [Azure (Azure RBAC)](../role-based-access-control/overview.md)voor meer informatie over het werken met Azure RBAC.
* Zie Resources vergrendelen met Azure Resource Manager voor meer informatie over het werken [met resourcevergrendelingen.](../azure-resource-manager/management/lock-resources.md)
