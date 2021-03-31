---
title: bestand opnemen
description: bestand opnemen
services: active-directory
author: daveba
ms.service: active-directory
ms.subservice: msi
ms.topic: include
ms.date: 05/31/2018
ms.author: daveba
ms.custom: include file
ms.openlocfilehash: 6e5885e076222cd23ba127f3be41c1218f327ca0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92379763"
---
- Elke beheerde identiteit telt mee voor de quotumlimiet voor objecten in een Azure AD-tenant, zoals beschreven in [Limieten en beperkingen van de Azure AD-service](../articles/active-directory/enterprise-users/directory-service-limits-restrictions.md).
-   De snelheid waarmee beheerde identiteiten kunnen worden gemaakt, kent de volgende limieten:

    1. Per Azure AD-tenant per Azure-regio: 200 maakbewerkingen per 20 seconden.
    2. Per Azure-abonnement per Azure-regio: 40 maakbewerkingen per 20 seconden.

- Wanneer u aan gebruikers toegewezen beheerde identiteiten maakt, worden alleen alfanumerieke tekens (0-9, a-z, A-Z) en het afbreekstreepje (-) ondersteund. De toewijzing aan een virtuele machine of virtuele-machineschaalset verloopt goed als de naam wordt beperkt tot 24 tekens.
