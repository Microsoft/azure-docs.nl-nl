---
title: bestand opnemen
description: bestand opnemen
author: tfitzmac
ms.service: governance
ms.topic: include
ms.date: 03/26/2020
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: cdcf6215973755444da9e513761de7ac71e479d4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98738561"
---
| Resource | Limiet |
| --- | --- |
| Beheergroepen per Azure AD-tenant | 10.000 |
| Abonnementen per beheergroep | Onbeperkt. |
| Niveaus van hiërarchie voor beheergroepen | Hoofdmapniveau plus 6 niveaus<sup>1</sup> |
| Directe bovenliggende beheergroep per beheergroep | Eén |
| [Implementaties van beheergroepniveau](../articles/azure-resource-manager/templates/deploy-to-management-group.md) per locatie | 800<sup>2</sup> |

<sup>1</sup>De 6 niveaus zijn exclusief het abonnementsniveau.

<sup>2</sup>Als u de limiet van 800 implementaties bereikt, verwijdert u implementaties die niet meer nodig zijn uit de geschiedenis. Gebruik [Remove-AzManagementGroupDeployment](/powershell/module/az.resources/Remove-AzManagementGroupDeployment) of [az deployment mg delete](/cli/azure/deployment/mg#az-deployment-mg-delete) om implementaties op beheergroepsniveau te verwijderen.
