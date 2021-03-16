---
title: bestand opnemen
description: bestand opnemen
services: azure-resource-manager
author: tfitzmac
ms.service: cost-management-billing
ms.topic: include
ms.date: 03/15/2021
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: 97d80e999ac61a2c2f8f561dc19213419014beb8
ms.sourcegitcommit: 66ce33826d77416dc2e4ba5447eeb387705a6ae5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/15/2021
ms.locfileid: "103471021"
---
| Resource | Limiet |
| --- | --- |
| Abonnementen [die zijn gekoppeld aan een Azure Active Directory Tenant](../articles/active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md) | Onbeperkt |
| [Medebeheerders](../articles/cost-management-billing/manage/add-change-subscription-administrator.md) per abonnement |Onbeperkt |
| [Resourcegroepen](../articles/azure-resource-manager/management/overview.md) per abonnement |980 |
| Aanvraaggrootte voor Azure Resource Manager API |4.194.304 bytes |
| Tags per abonnement<sup>1</sup> |50 |
| Unieke tagberekeningen per abonnement<sup>1</sup> | 80,000 |
| [Implementaties van abonnementsniveau](../articles/azure-resource-manager/templates/deploy-to-subscription.md) per locatie | 800<sup>2</sup> |

<sup>1</sup>U kunt maximaal 50 tags rechtstreeks op een abonnement toepassen. Het abonnement kan echter een onbeperkt aantal tags bevatten die worden toegepast op resourcegroepen en resources binnen het abonnement. Het aantal tags per resource of resourcegroep is beperkt tot 50. Resource Manager retourneert alleen een [lijst met unieke label namen en-waarden](/rest/api/resources/tags) in het abonnement wanneer het aantal Tags 80.000 of minder is. U kunt nog steeds een resource vinden op label wanneer het aantal groter is dan 80.000.

<sup>2</sup>Als u de limiet van 800 implementaties bereikt, verwijdert u implementaties die niet meer nodig zijn uit de geschiedenis. Als u implementaties op abonnementsniveau wilt verwijderen, gebruikt u [Remove-AzDeployment](/powershell/module/az.resources/Remove-AzDeployment) of [az deployment sub delete](/cli/azure/deployment/sub#az-deployment-sub-delete).
