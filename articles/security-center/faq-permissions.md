---
title: 'Azure Security Center Veelgestelde vragen: vragen over machtigingen'
description: Deze veelgestelde vragen vindt u antwoorden op vragen over machtigingen in Azure Security Center, een product waarmee u bedreigingen kunt voor komen, detecteren en reageren.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: be2ab6d5-72a8-411f-878e-98dac21bc5cb
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/25/2020
ms.author: memildin
ms.openlocfilehash: 88d6207bf44d1ec04fde4f74ac9295412ea88fbc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "87824610"
---
# <a name="permissions"></a>Machtigingen

## <a name="how-do-permissions-work-in-azure-security-center"></a>Hoe werken de machtigingen in Azure Security Center?

Azure Security Center maakt gebruik van [op rollen gebaseerd toegangsbeheer van Azure (Azure RBAC)](../role-based-access-control/role-assignments-portal.md), dat [ingebouwde rollen](../role-based-access-control/built-in-roles.md) biedt die kunnen worden toegewezen aan gebruikers, groepen en services in Azure.

In Azure Security Center wordt de configuratie van uw resources beoordeeld om beveiligingsproblemen en kwetsbaarheden te identificeren. In Security Center ziet u alleen informatie over een resource wanneer aan u de rol van eigenaar, bijdrager of lezer is toegewezen voor het abonnement of de resourcegroep waar een resource bij hoort.

Zie [machtigingen in azure Security Center](security-center-permissions.md) voor meer informatie over rollen en toegestane acties in Security Center.



## <a name="who-can-modify-a-security-policy"></a>Wie kan een beveiligings beleid wijzigen?

Als u een beveiligings beleid wilt wijzigen, moet u een beveiligings beheerder of een eigenaar of bijdrager van het abonnement zijn.

Zie [beveiligings beleid instellen in azure Security Center](tutorial-security-policy.md)voor meer informatie over het configureren van een beveiligings beleid.