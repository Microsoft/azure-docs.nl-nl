---
services: storage
author: normesta
ms.service: storage
ms.topic: include
ms.date: 09/29/2020
ms.author: normesta
ms.custom: include file
ms.openlocfilehash: 97b47b48a183951002d04cd58b08d5e2b9051eda
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96017667"
---
| Mechanisme | Bereik |Limieten | Ondersteund machtigings niveau |
|---|---|---|---|
| Azure RBAC | Opslag accounts, containers. <br>Azure-roltoewijzingen voor meerdere resources op het niveau van een abonnement of resource groep. | 2000 Azure-roltoewijzingen in een abonnement | Azure-rollen (ingebouwd of aangepast) |
| MIGREREN| Map, bestand |32 ACL-vermeldingen (in feite 28 ACL-vermeldingen) per bestand en per map. De toegangs-en standaard-Acl's hebben elk een eigen 32 ACL-ingangs limiet. |ACL-machtiging|
