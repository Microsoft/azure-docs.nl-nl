---
title: Role-Based Access Control gebruiken in azure API Management | Microsoft Docs
description: Meer informatie over het gebruik van de ingebouwde rollen en het maken van aangepaste rollen in azure API Management
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
ms.openlocfilehash: fc41d2904d5a6676f1e9e1e2d178985bef0a9aa3
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/18/2021
ms.locfileid: "101095547"
---
# <a name="how-to-use-role-based-access-control-in-azure-api-management"></a>Op rollen gebaseerd toegangsbeheer gebruiken in API Management

Azure API Management is afhankelijk van Azure RBAC (op rollen gebaseerd toegangs beheer) voor het maken van nauw keurig toegangs beheer voor API Management Services en entiteiten (bijvoorbeeld Api's en beleids regels). In dit artikel vindt u een overzicht van de ingebouwde en aangepaste rollen in API Management. Zie [aan de slag met toegangs beheer in de Azure Portal](../role-based-access-control/overview.md)voor meer informatie over toegangs beheer in de Azure Portal.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="built-in-roles"></a>Ingebouwde rollen

API Management biedt momenteel drie ingebouwde rollen en voegt twee meer rollen toe aan de nabije toekomst. Deze rollen kunnen worden toegewezen in verschillende bereiken, waaronder het abonnement, de resource groep en de afzonderlijke API Management-instantie. Als u bijvoorbeeld de rol ' API Management service lezer ' toewijst aan een gebruiker op het niveau van de resource groep, heeft de gebruiker lees toegang tot alle API Management instanties in de resource groep. 

De volgende tabel bevat korte beschrijvingen van de ingebouwde rollen. U kunt deze rollen toewijzen met behulp van de Azure Portal of andere hulpprogram ma's, zoals Azure [Power shell](../role-based-access-control/role-assignments-powershell.md), [azure cli](../role-based-access-control/role-assignments-cli.md)en [rest API](../role-based-access-control/role-assignments-rest.md). Zie [Azure-rollen toewijzen voor het beheren van de toegang tot uw Azure-abonnements resources](../role-based-access-control/role-assignments-portal.md)voor meer informatie over het toewijzen van ingebouwde rollen.

| Rol          | Lees toegang<sup>[1]</sup> | Schrijf toegang<sup>[2]</sup> | Service maken, verwijderen, schalen, VPN en aangepaste domein configuratie | Toegang tot de verouderde Publisher-Portal | Description
| ------------- | ---- | ---- | ---- | ---- | ---- 
| Inzender van API Management-service | ✓ | ✓ | ✓ | ✓ | Super gebruiker. Heeft volledige ruwe toegang tot API Management Services en entiteiten (bijvoorbeeld Api's en beleids regels). Heeft toegang tot de verouderde Publisher-Portal. |
| API Management-service lezer | ✓ | | || Heeft alleen-lezen toegang tot API Management Services en entiteiten. |
| API Management-service operator | ✓ | | ✓ | | Kan API Management Services, maar niet de entiteiten, beheren.|
| API Management-service-editor<sup>*</sup> | ✓ | ✓ | |  | Kan API Management entiteiten, maar geen services, beheren.|
| API Management-inhouds beheerder<sup>*</sup> | ✓ | | | ✓ | Kan de ontwikkelaars portal beheren. Alleen-lezen toegang tot services en entiteiten.|

<sup>[1] Lees toegang tot API Management Services en-entiteiten (bijvoorbeeld Api's en beleid).</sup>

<sup>[2] schrijf toegang tot API Management Services en entiteiten, met uitzonde ring van de volgende bewerkingen: instanties maken, verwijderen en schalen; VPN-configuratie; en aangepaste domein instelling.</sup>

<sup>\* De functie Service-editor is beschikbaar nadat alle beheer-UI van de bestaande Publisher-Portal naar de Azure Portal zijn gemigreerd. De rol inhouds beheerder is beschikbaar nadat de Publisher-Portal is gefactord, zodat deze alleen functionaliteit bevat die betrekking heeft op het beheren van de ontwikkelaars Portal.</sup>  

## <a name="custom-roles"></a>Aangepaste rollen

Als geen van de ingebouwde rollen aan uw specifieke behoeften voldoet, kunnen er aangepaste rollen worden gemaakt voor meer gedetailleerd toegangs beheer voor API Management entiteiten. U kunt bijvoorbeeld een aangepaste rol maken met alleen-lezen toegang tot een API Management-service, maar heeft alleen schrijf toegang tot een specifieke API. Zie [aangepaste rollen in azure RBAC](../role-based-access-control/custom-roles.md)voor meer informatie over aangepaste rollen. 

> [!NOTE]
> Een aangepaste rol moet de actie bevatten om een API Management-exemplaar in het Azure Portal te kunnen zien ```Microsoft.ApiManagement/service/read``` .

Wanneer u een aangepaste rol maakt, is het gemakkelijker om te beginnen met een van de ingebouwde rollen. Bewerk de kenmerken om **acties**, **intact** of **AssignableScopes** toe te voegen en sla de wijzigingen vervolgens op als een nieuwe rol. Het volgende voor beeld begint met de rol ' API Management service lezer ' en maakt een aangepaste rol met de naam ' Rekenmachine API-editor '. U kunt de aangepaste rol toewijzen aan een specifieke API. Daarom heeft deze rol alleen toegang tot die API. 

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

Het artikel van de [Azure Resource Manager resource provider](../role-based-access-control/resource-provider-operations.md#microsoftapimanagement) bevat de lijst met machtigingen die kunnen worden verleend op het API management niveau.

## <a name="video"></a>Video


> [!VIDEO https://channel9.msdn.com/Blogs/AzureApiMgmt/Role-Based-Access-Control-in-API-Management/player]
>
>

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de volgende artikelen voor meer informatie over Role-Based Access Control in Azure:
  * [Aan de slag met toegangsbeheer in de Azure-portal](../role-based-access-control/overview.md)
  * [Azure-rollen toewijzen voor het beheren van de toegang tot uw Azure-abonnements resources](../role-based-access-control/role-assignments-portal.md)
  * [Aangepaste rollen in azure RBAC](../role-based-access-control/custom-roles.md)
  * [Bewerkingen voor Azure Resource Manager-resourceproviders](../role-based-access-control/resource-provider-operations.md#microsoftapimanagement)
