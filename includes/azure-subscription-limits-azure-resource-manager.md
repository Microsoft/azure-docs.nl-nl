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
ms.openlocfilehash: f02b7f0f80cfb875cc6207b542db90607b379b67
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107799958"
---
| Resource | Limiet |
| --- | --- |
| Abonnementen die [zijn gekoppeld aan een Azure Active Directory tenant](../articles/active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md) | Onbeperkt |
| [Medebeheerders](../articles/cost-management-billing/manage/add-change-subscription-administrator.md) per abonnement |Onbeperkt |
| [Resourcegroepen](../articles/azure-resource-manager/management/overview.md) per abonnement |980 |
| Aanvraaggrootte voor Azure Resource Manager API |4.194.304 bytes |
| Tags per abonnement<sup>1</sup> |50 |
| Unieke tagberekeningen per abonnement<sup>1</sup> | 80,000 |
| [Implementaties van abonnementsniveau](../articles/azure-resource-manager/templates/deploy-to-subscription.md) per locatie | 800<sup>2</sup> |

<sup>1</sup>U kunt maximaal 50 tags rechtstreeks op een abonnement toepassen. Het abonnement kan echter een onbeperkt aantal tags bevatten die worden toegepast op resourcegroepen en resources binnen het abonnement. Het aantal tags per resource of resourcegroep is beperkt tot 50. Resource Manager retourneert alleen een lijst met unieke [tagnaam](/rest/api/resources/tags) en -waarden in het abonnement wanneer het aantal tags 80.000 of minder is. U kunt een resource nog steeds vinden op tag wanneer het aantal hoger is dan 80.000.

<sup>2</sup>Als u de limiet van 800 implementaties bereikt, verwijdert u implementaties die niet meer nodig zijn uit de geschiedenis. Als u implementaties op abonnementsniveau wilt verwijderen, gebruikt u [Remove-AzDeployment](/powershell/module/az.resources/Remove-AzDeployment) of [az deployment sub delete](/cli/azure/deployment/sub#az_deployment_sub_delete).
