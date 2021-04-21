---
title: bestand opnemen
description: bestand opnemen
author: tfitzmac
ms.service: governance
ms.topic: include
ms.date: 03/26/2020
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: 53e3f37d14153f3a2d7b5886a49b08ca9052b128
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107799914"
---
| Resource | Limiet |
| --- | --- |
| Beheergroepen per Azure AD-tenant | 10.000 |
| Abonnementen per beheergroep | Onbeperkt. |
| Niveaus van hiërarchie voor beheergroepen | Hoofdmapniveau plus 6 niveaus<sup>1</sup> |
| Directe bovenliggende beheergroep per beheergroep | Eén |
| [Implementaties van beheergroepniveau](../articles/azure-resource-manager/templates/deploy-to-management-group.md) per locatie | 800<sup>2</sup> |

<sup>1</sup>De 6 niveaus zijn exclusief het abonnementsniveau.

<sup>2</sup>Als u de limiet van 800 implementaties bereikt, verwijdert u implementaties die niet meer nodig zijn uit de geschiedenis. Gebruik [Remove-AzManagementGroupDeployment](/powershell/module/az.resources/Remove-AzManagementGroupDeployment) of [az deployment mg delete](/cli/azure/deployment/mg#az_deployment_mg_delete) om implementaties op beheergroepsniveau te verwijderen.
