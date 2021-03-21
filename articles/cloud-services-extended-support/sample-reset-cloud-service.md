---
title: 'Azure PowerShell voor beelden: Azure Cloud Services opnieuw instellen (uitgebreide ondersteuning)'
description: Voorbeeld scripts voor het opnieuw instellen van een implementatie van een Azure-Cloud service (uitgebreide ondersteuning)
ms.topic: sample
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: ''
ms.openlocfilehash: 5c43d61b1e7cd98674eab4c6d857cc1114a06013
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "99475317"
---
# <a name="reset-an-azure-cloud-service-extended-support"></a>Een Azure-Cloud service opnieuw instellen (uitgebreide ondersteuning) 
Deze voor beelden beslaan verschillende manieren om een bestaande implementatie van een Azure Cloud service (uitgebreide ondersteuning) opnieuw in te stellen.

## <a name="reimage-role-instances-of-cloud-service"></a>Installatie kopie van rolinstantie van Cloud service terugzetten
```powershell
$roleInstances = @("ContosoFrontEnd_IN_0", "ContosoBackEnd_IN_1")
Invoke-AzCloudServiceReimage -ResourceGroupName "ContosOrg" -CloudServiceName "ContosoCS" -RoleInstance $roleInstances
```
Met deze opdracht worden de installatie kopieën van de rolinstanties ContosoFrontEnd_IN_0 en ContosoBackEnd_IN_1 van de Cloud service met de naam ContosoCS die deel uitmaakt van de resource groep met de naam ContosOrg.

## <a name="reimage-all-roles-of-cloud-service"></a>Installatie kopie van alle rollen van de Cloud service terugzetten
```powershell
Invoke-AzCloudServiceReimage -ResourceGroupName "ContosOrg" -CloudServiceName "ContosoCS" -RoleInstance "*"
```
Met deze opdracht worden alle rolinstanties van de Cloud service met de naam ContosoCS die deel uitmaakt van de resource groep met de naam ContosOrg.

## <a name="reimage-a-single-role-instance-of-a-cloud-service"></a>Installatie kopie van één rolinstantie van een Cloud service terugzetten
```powershell
Invoke-AzCloudServiceRoleInstanceReimage -ResourceGroupName "ContosOrg" -CloudServiceName "ContosoCS" -RoleInstanceName "ContosoFrontEnd_IN_0"
```
Met deze opdracht wordt de rolinstantie ContosoFrontEnd_IN_0 van de Cloud service met de naam ContosoCS die deel uitmaakt van de resource groep met de naam ContosOrg.

## <a name="rebuild-role-instances-of-cloud-service"></a>Rolinstanties van de Cloud service opnieuw samen stellen
```powershell
$roleInstances = @("ContosoFrontEnd_IN_0", "ContosoBackEnd_IN_1")
Invoke-AzCloudServiceRebuild -ResourceGroupName "ContosOrg" -CloudServiceName "ContosoCS" -RoleInstance $roleInstances
```
Met deze opdracht worden twee rolinstanties opnieuw opgebouwd ContosoFrontEnd_IN_0 en ContosoBackEnd_IN_1 van de Cloud service met de naam ContosoCS die behoort tot de resource groep met de naam ContosOrg.

## <a name="rebuild-all-roles-of-cloud-service"></a>Alle rollen van de Cloud service opnieuw samen stellen
```powershell
Invoke-AzCloudServiceRebuild -ResourceGroupName "ContosOrg" -CloudServiceName "ContosoCS" -RoleInstance "*"
```
Met deze opdracht worden alle rolinstanties van de Cloud service met de naam ContosoCS opnieuw opgebouwd die horen bij de resource groep met de naam ContosOrg.

## <a name="restart-role-instances-of-cloud-service"></a>Rolinstanties van Cloud service opnieuw starten
```powershell
$roleInstances = @("ContosoFrontEnd_IN_0", "ContosoBackEnd_IN_1")
Restart-AzCloudService -ResourceGroupName "ContosOrg" -CloudServiceName "ContosoCS" -RoleInstance $roleInstances
```
Met deze opdracht worden twee rolinstanties ContosoFrontEnd_IN_0 en ContosoBackEnd_IN_1 van de Cloud service met de naam ContosoCS opnieuw gestart die deel uitmaakt van de resource groep met de naam ContosOrg.

## <a name="restart-all-roles-of-cloud-service"></a>Alle rollen van de Cloud service opnieuw starten
```powershell
Restart-AzCloudService -ResourceGroupName "ContosOrg" -CloudServiceName "ContosoCS" -RoleInstance "*"
```
Met deze opdracht worden alle rolinstanties van de Cloud service met de naam ContosoCS opnieuw gestart die horen bij de resource groep met de naam ContosOrg.

## <a name="next-steps"></a>Volgende stappen

- Zie [overzicht van azure Cloud Services (uitgebreide ondersteuning)](overview.md)voor meer informatie over Azure Cloud Services (uitgebreide ondersteuning).
- Ga naar de [opslag plaats voor beelden van Cloud Services (uitgebreide ondersteuning)](https://github.com/Azure-Samples/cloud-services-extended-support)
