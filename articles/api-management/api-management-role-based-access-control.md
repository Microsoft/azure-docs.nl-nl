---
title: Hoe u Role-Based Access Control in Azure API Management | Microsoft Docs
description: Meer informatie over het gebruik van de ingebouwde rollen en het maken van aangepaste rollen in Azure API Management
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 364cd53e-88fb-4301-a093-f132fa1f88f5
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 06/20/2018
ms.author: apimpm
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: ef71591d6d5a26aa737db4e7cb547c8b2c39d92a
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107812170"
---
# <a name="how-to-use-role-based-access-control-in-azure-api-management"></a>Op rollen gebaseerd toegangsbeheer gebruiken in API Management

Azure API Management is afhankelijk van op rollen gebaseerd toegangsbeheer van Azure (Azure RBAC) voor het inschakelen van fijner toegangsbeheer voor API Management-services en -entiteiten (bijvoorbeeld API's en beleidsregels). In dit artikel vindt u een overzicht van de ingebouwde en aangepaste rollen in API Management. Zie Aan de slag met toegangsbeheer in de Azure Portal voor meer informatie over [toegangsbeheer in Azure Portal](../role-based-access-control/overview.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="built-in-roles"></a>Ingebouwde rollen

API Management biedt momenteel drie ingebouwde rollen en voegt in de nabije toekomst nog twee rollen toe. Deze rollen kunnen worden toegewezen op verschillende scopes, waaronder abonnement, resourcegroep en API Management exemplaar. Als u bijvoorbeeld de rol 'API Management Service Reader' toewijst aan een gebruiker op het niveau van de resourcegroep, heeft de gebruiker leestoegang tot alle API Management-exemplaren in de resourcegroep. 

De volgende tabel bevat korte beschrijvingen van de ingebouwde rollen. U kunt deze rollen toewijzen met behulp van de Azure Portal of andere hulpprogramma's, waaronder Azure [PowerShell,](../role-based-access-control/role-assignments-powershell.md) [Azure CLI](../role-based-access-control/role-assignments-cli.md)en [REST API.](../role-based-access-control/role-assignments-rest.md) Zie Azure-rollen toewijzen om toegang tot uw Azure-abonnementsbronnen te beheren voor meer informatie over het toewijzen van ingebouwde [rollen.](../role-based-access-control/role-assignments-portal.md)

| Rol          | Leestoegang<sup>[1]</sup> | Schrijftoegang<sup>[2]</sup> | Service maken, verwijderen, schalen, VPN en aangepaste domeinconfiguratie | Toegang tot de verouderde publicatieportal | Description
| ------------- | ---- | ---- | ---- | ---- | ---- 
| API Management-servicebijdrager | ✓ | ✓ | ✓ | ✓ | Supergebruiker. Heeft volledige CRUD-toegang tot API Management services en entiteiten (bijvoorbeeld API's en beleidsregels). Heeft toegang tot de verouderde publicatieportal. |
| API Management Service Reader | ✓ | | || Heeft alleen-lezentoegang tot API Management services en entiteiten. |
| API Management Service Operator | ✓ | | ✓ | | Kan API Management services beheren, maar geen entiteiten.|
| API Management Service Editor<sup>*</sup> | ✓ | ✓ | |  | Kan API Management entiteiten beheren, maar geen services.|
| API Management Content Manager<sup>*</sup> | ✓ | | | ✓ | Kan de ontwikkelaarsportal beheren. Alleen-lezentoegang tot services en entiteiten.|

<sup>[1] Leestoegang tot API Management services en entiteiten (bijvoorbeeld API's en beleidsregels).</sup>

<sup>[2] Schrijftoegang tot API Management services en entiteiten, met uitzondering van de volgende bewerkingen: exemplaar maken, verwijderen en schalen; VPN-configuratie; en aangepaste domeininstellingen.</sup>

<sup>\* De rol Service-editor is beschikbaar nadat alle beheer-UI is gemigreerd van de bestaande publicatieportal naar de Azure Portal. De rol Content Manager is beschikbaar nadat de publicatieportal is gefactoreerd en alleen functionaliteit bevat die betrekking heeft op het beheren van de ontwikkelaarsportal.</sup>  

## <a name="custom-roles"></a>Aangepaste rollen

Als geen van de ingebouwde rollen aan uw specifieke behoeften voldoet, kunnen aangepaste rollen worden gemaakt voor een gedetailleerder toegangsbeheer voor API Management entiteiten. U kunt bijvoorbeeld een aangepaste rol maken die alleen-lezentoegang heeft tot een API Management-service, maar alleen schrijftoegang heeft tot één specifieke API. Zie Aangepaste rollen in [Azure RBAC](../role-based-access-control/custom-roles.md)voor meer informatie over aangepaste rollen. 

> [!NOTE]
> Als u een exemplaar van een API Management in de Azure Portal, moet een aangepaste rol de actie ```Microsoft.ApiManagement/service/read``` bevatten.

Wanneer u een aangepaste rol maakt, is het eenvoudiger om te beginnen met een van de ingebouwde rollen. Bewerk de kenmerken om **Acties**, **NotActions** of **AssignableScopes** toe te voegen en sla de wijzigingen vervolgens op als een nieuwe rol. Het volgende voorbeeld begint met de rol 'API Management Service Reader' en maakt een aangepaste rol met de naam 'Calculator API Editor'. U kunt de aangepaste rol toewijzen aan een specifieke API. Daarom heeft deze rol alleen toegang tot die API. 

```powershell
$role = Get-AzRoleDefinition "API Management Service Reader Role"
$role.Id = $null
$role.Name = 'Calculator API Contributor'
$role.Description = 'Has read access to Contoso APIM instance and write access to the Calculator API.'
$role.Actions.Add('Microsoft.ApiManagement/service/apis/write')
$role.Actions.Add('Microsoft.ApiManagement/service/apis/*/write')
$role.AssignableScopes.Clear()
$role.AssignableScopes.Add('/subscriptions/<subscription ID>/resourceGroups/<resource group name>/providers/Microsoft.ApiManagement/service/<service name>/apis/<api ID>')
New-AzRoleDefinition -Role $role
New-AzRoleAssignment -ObjectId <object ID of the user account> -RoleDefinitionName 'Calculator API Contributor' -Scope '/subscriptions/<subscription ID>/resourceGroups/<resource group name>/providers/Microsoft.ApiManagement/service/<service name>/apis/<api ID>'
```

Het [Azure Resource Manager resourceprovider bevat](../role-based-access-control/resource-provider-operations.md#microsoftapimanagement) de lijst met machtigingen die kunnen worden verleend op API Management niveau.

## <a name="video"></a>Video


> [!VIDEO https://channel9.msdn.com/Blogs/AzureApiMgmt/Role-Based-Access-Control-in-API-Management/player]
>
>

## <a name="next-steps"></a>Volgende stappen

Zie de volgende artikelen Role-Based Access Control meer informatie over uw gegevens in Azure:
  * [Aan de slag met toegangsbeheer in de Azure-portal](../role-based-access-control/overview.md)
  * [Azure-rollen toewijzen om de toegang tot de resources van uw Azure-abonnement te beheren](../role-based-access-control/role-assignments-portal.md)
  * [Aangepaste rollen in Azure RBAC](../role-based-access-control/custom-roles.md)
  * [Bewerkingen voor Azure Resource Manager-resourceproviders](../role-based-access-control/resource-provider-operations.md#microsoftapimanagement)
