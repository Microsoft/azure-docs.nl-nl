---
title: bestand opnemen
description: Include-bestand
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 09/30/2020
ms.author: dacoulte
ms.openlocfilehash: f3f706789e14cb20214bf17fd91f6ec1e503848f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "91605826"
---
Voor elk objecttype in Azure Policy bestaat er een maximum. Voor definities betekent _Bereik_ de [beheergroep](../articles/governance/management-groups/overview.md) of het abonnement.
Voor toewijzingen en uitzonderingen betekent _Bereik_ de [beheergroep](../articles/governance/management-groups/overview.md), het abonnement, de resource groep of een afzonderlijke resource.

| Waar | Wat | Maximum |
|---|---|---|
| Bereik | Beleidsdefinities | 500 |
| Bereik | Initiatiefdefinities | 200 |
| Tenant | Initiatiefdefinities | 2500 |
| Bereik | Beleids- of initiatieftoewijzingen | 200 |
| Bereik | Uitzonderingen | 1000 |
| Beleidsdefinitie | Parameters | 20 |
| Initiatiefdefinitie | Beleidsregels | 1000 |
| Initiatiefdefinitie | Parameters | 100 |
| Beleids- of initiatieftoewijzingen | Uitzonderingen (geen bereiken) | 400 |
| Beleidsregel | Geneste voorwaarden | 512 |
| Hersteltaak | Resources | 500 |
