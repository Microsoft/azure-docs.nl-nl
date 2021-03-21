---
title: bestand opnemen
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 02/10/2021
ms.author: tamram
ms.openlocfilehash: 483f5853c321eee4ac6d10543f0e360a0a5e54b9
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100373744"
---
Voordat u een Azure RBAC-rol toewijst aan een beveiligingsprincipal, bepaalt u het bereik van toegang dat de beveiligingsprincipal moet hebben. Uit best practices blijkt dat het het beste is om het nauwst mogelijke bereik toe te wijzen. De Azure RBAC-rollen die zijn gedefinieerd voor een groter bereik, worden overgenomen door de onderliggende resources.

In de volgende lijst worden de niveaus beschreven waarop u toegang tot Azure-blob en -wachtrijresources kunt bepalen, beginnend met het kleinste bereik:

- **Een afzonderlijke container.** In dit bereik is een roltoewijzing van toepassing op alle blobs in de container, evenals containereigenschappen en metagegevens.
- **Een afzonderlijke wachtrij.** In dit bereik is een roltoewijzing van toepassing op berichten in de wachtrij, evenals op wachtrij-eigenschappen en metagegevens.
- **Het opslagaccount.** In dit bereik is een roltoewijzing van toepassing op alle containers en de bijbehorende blobs, of op alle wachtrijen en hun berichten.
- **De resourcegroep.** In dit bereik is een roltoewijzing van toepassing op alle containers of wachtrijen in alle opslagaccounts in de resourcegroep.
- **Het abonnement.** In dit bereik is een roltoewijzing van toepassing op alle containers of wachtrijen in alle opslagaccounts in alle resourcegroepen in het abonnement.
- **Een beheergroep.** In dit bereik is een roltoewijzing van toepassing op alle containers of wachtrijen in alle opslagaccounts in alle resourcegroepen in alle abonnementen van de beheergroep.

Raadpleeg [Wat is op rollen gebaseerd toegangsbeheer in Azure (Azure RBAC)?](../articles/role-based-access-control/overview.md) voor meer informatie over Azure-roltoewijzingen.
