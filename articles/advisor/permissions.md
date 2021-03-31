---
title: Machtigingen in Azure Advisor
description: Advisor-machtigingen en hoe ze de mogelijkheid kunnen blok keren om abonnementen te configureren of aanbevelingen uit te stellen of te negeren.
ms.topic: article
ms.date: 04/03/2019
ms.openlocfilehash: 402a21c47c4cba8f747d5d4601f9c95034c99262
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "91712923"
---
# <a name="permissions-in-azure-advisor"></a>Machtigingen in Azure Advisor

Azure Advisor biedt aanbevelingen op basis van het gebruik en de configuratie van uw Azure-resources en-abonnementen. Advisor maakt gebruik van de [ingebouwde rollen](../role-based-access-control/built-in-roles.md) van [Azure op rollen gebaseerd toegangs beheer (Azure RBAC)](../role-based-access-control/overview.md) voor het beheren van uw toegang tot aanbevelingen en Advisor-functies. 

## <a name="roles-and-their-access"></a>Rollen en hun toegang

De volgende tabel definieert de rollen en de toegang die ze hebben in Advisor:

| **Role** | **Aanbevelingen bekijken** | **Regels bewerken** | **Abonnements configuratie bewerken** | **Configuratie van de resource groep bewerken**| **Aanbevelingen negeren en uitstellen**|
|---|:---:|:---:|:---:|:---:|:---:|
|Abonnementeigenaar|**X**|**X**|**X**|**X**|**X**|
|Abonnementinzender|**X**|**X**|**X**|**X**|**X**|
|Abonnements lezer|**X**|--|--|--|--|
|Eigenaar van resource groep|**X**|--|--|**X**|**X**|
|Inzender voor resource groep|**X**|--|--|**X**|**X**|
|Lezer van resource groep|**X**|--|--|--|--|
|Resource-eigenaar|**X**|--|--|--|**X**|
|Resource bijdrager|**X**|--|--|--|**X**|
|Resource Reader|**X**|--|--|--|--|

> [!NOTE]
> Toegang tot de weer gave van aanbevelingen is afhankelijk van uw toegang tot de betrokken resource van de aanbeveling.

## <a name="permissions-and-unavailable-actions"></a>Machtigingen en niet-beschik bare acties

Als u niet over de juiste machtigingen beschikt, kan de mogelijkheid voor het uitvoeren van acties in Advisor worden geblokkeerd. Hieronder volgen enkele veelvoorkomende problemen.

### <a name="unable-to-configure-subscriptions-or-resource-groups"></a>Kan geen abonnementen of resource groepen configureren

Wanneer u probeert abonnementen of resource groepen in Advisor te configureren, is het mogelijk dat de optie om op te nemen of uit te sluiten is uitgeschakeld. Deze status geeft aan dat u niet over voldoende machtigingen beschikt voor die resource groep of dit abonnement. U kunt dit probleem oplossen door te leren hoe u [een gebruiker toegang verleent](../role-based-access-control/quickstart-assign-role-user-portal.md).

### <a name="unable-to-postpone-or-dismiss-a-recommendation"></a>Kan een aanbeveling niet uitstellen of negeren

Als er een fout optreedt bij het uitstellen of negeren van een aanbeveling, hebt u mogelijk niet de juiste machtigingen. Zorg ervoor dat u ten minste de Inzender toegang hebt tot de betrokken resource van de aanbeveling die u uitstelt of niet meer wilt gebruiken. U kunt dit probleem oplossen door te leren hoe u [een gebruiker toegang verleent](../role-based-access-control/quickstart-assign-role-user-portal.md).

## <a name="next-steps"></a>Volgende stappen

In dit artikel wordt een overzicht gegeven van de manier waarop Advisor gebruikmaakt van Azure RBAC voor het beheren van gebruikers machtigingen en het oplossen van veelvoorkomende problemen. Zie voor meer informatie over Advisor:

- [Wat is Azure Advisor?](./advisor-overview.md)
- [Aan de slag met Azure Advisor](./advisor-get-started.md)
