---
title: bestand opnemen
description: bestand opnemen
services: azure-resource-manager
author: tfitzmac
ms.service: cost-management-billing
ms.topic: include
ms.date: 01/25/2021
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: ad0c532c2ac80fd8a3bb3e68431ff7fc274d73e0
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/26/2021
ms.locfileid: "98792317"
---
| Resource | Limiet |
| --- | --- |
| Abonnementen per Azure Active Directory-tenant | Onbeperkt |
| [Medebeheerders](../articles/cost-management-billing/manage/add-change-subscription-administrator.md) per abonnement |Onbeperkt |
| [Resourcegroepen](../articles/azure-resource-manager/management/overview.md) per abonnement |980 |
| Aanvraaggrootte voor Azure Resource Manager API |4.194.304 bytes |
| Tags per abonnement<sup>1</sup> |50 |
| Unieke tagberekeningen per abonnement<sup>1</sup> | 80,000 |
| [Implementaties van abonnementsniveau](../articles/azure-resource-manager/templates/deploy-to-subscription.md) per locatie | 800<sup>2</sup> |

<sup>1</sup>U kunt maximaal 50 tags rechtstreeks op een abonnement toepassen. Het abonnement kan echter een onbeperkt aantal tags bevatten die worden toegepast op resourcegroepen en resources binnen het abonnement. Het aantal tags per resource of resourcegroep is beperkt tot 50. Resource Manager retourneert alleen een [lijst met unieke label namen en-waarden](/rest/api/resources/tags) in het abonnement wanneer het aantal Tags 80.000 of minder is. U kunt nog steeds een resource vinden op label wanneer het aantal groter is dan 80.000.

<sup>2</sup>Als u de limiet van 800 implementaties bereikt, verwijdert u implementaties die niet meer nodig zijn uit de geschiedenis. Als u implementaties op abonnementsniveau wilt verwijderen, gebruikt u [Remove-AzDeployment](/powershell/module/az.resources/Remove-AzDeployment) of [az deployment sub delete](/cli/azure/deployment/sub#az-deployment-sub-delete).
