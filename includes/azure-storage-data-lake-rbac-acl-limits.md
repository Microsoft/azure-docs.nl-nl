---
services: storage
author: normesta
ms.service: storage
ms.topic: include
ms.date: 09/29/2020
ms.author: normesta
ms.custom: include file
ms.openlocfilehash: 97b47b48a183951002d04cd58b08d5e2b9051eda
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96017667"
---
| Mechanisme | Bereik |Limieten | Ondersteund machtigings niveau |
|---|---|---|---|
| Azure RBAC | Opslag accounts, containers. <br>Azure-roltoewijzingen voor meerdere resources op het niveau van een abonnement of resource groep. | 2000 Azure-roltoewijzingen in een abonnement | Azure-rollen (ingebouwd of aangepast) |
| MIGREREN| Map, bestand |32 ACL-vermeldingen (in feite 28 ACL-vermeldingen) per bestand en per map. De toegangs-en standaard-Acl's hebben elk een eigen 32 ACL-ingangs limiet. |ACL-machtiging|
