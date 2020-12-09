---
title: Machtigingen in Azure Security Center | Microsoft Docs
description: In dit artikel wordt uitgelegd hoe Azure Security Center toegangsbeheer op basis van rollen gebruikt om machtigingen aan gebruikers toe te wijzen en de toegestane acties voor elke rol aan te geven.
services: security-center
cloud: na
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 12/01/2020
ms.author: memildin
ms.openlocfilehash: 668fe1436feeb0888f7d871144da6cb6b8e3864e
ms.sourcegitcommit: fec60094b829270387c104cc6c21257826fccc54
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/09/2020
ms.locfileid: "96922599"
---
# <a name="permissions-in-azure-security-center"></a>Machtigingen in Azure Security Center

Azure Security Center maakt gebruik van [op rollen gebaseerd toegangsbeheer van Azure (Azure RBAC)](../role-based-access-control/role-assignments-portal.md), dat [ingebouwde rollen](../role-based-access-control/built-in-roles.md) biedt die kunnen worden toegewezen aan gebruikers, groepen en services in Azure.

In Azure Security Center wordt de configuratie van uw resources beoordeeld om beveiligingsproblemen en kwetsbaarheden te identificeren. In Security Center ziet u alleen informatie over een resource wanneer aan u de rol van eigenaar, bijdrager of lezer is toegewezen voor het abonnement of de resourcegroep waar een resource bij hoort.

Naast deze rollen zijn er twee specifieke Security Center-rollen:

* **Beveiligingslezer**: Een gebruiker met deze rol heeft weergaverechten voor Security Center. De gebruiker kan aanbevelingen, waarschuwingen, een beveiligingsbeleid en beveiligingsstatuswaarden weergeven, maar kan geen wijzigingen aanbrengen.
* **Beveiligingsbeheerder**: Een gebruiker met deze rol heeft dezelfde rechten als de beveiligingslezer en kan het beveiligingsbeleid ook bijwerken en waarschuwingen en aanbevelingen verwijderen.

> [!NOTE]
> De beveiligingsrollen Beveiligingslezer en Beveiligingsbeheerder hebben alleen toegang tot Security Center. De beveiligingsrollen hebben geen toegang tot andere servicegebieden van Azure, zoals Storage, Web & Mobile of Internet of Things.
>

## <a name="roles-and-allowed-actions"></a>Rollen en toegestane acties

In de volgende tabel worden rollen en toegestane acties in Security Center vermeld.

|Bewerking|Beveiligingslezer/ <br> Lezer |Beveiligingsbeheerder  |Resourcegroepinzender/ <br> Resourcegroepeigenaar  |Abonnementinzender  |Abonnementeigenaar  |
|:--- |:---:|:---:|:---:|:---:|:---:|
|Beveiligingsbeleid bewerken|-|✔|-|-|✔|
|Initiatieven (waaronder) reglementaire nalevingsstandaarden toevoegen/toewijzen|-|-|-|-|✔|
|Azure Defender inschakelen/uitschakelen|-|✔|-|-|✔|
|Automatische inrichting inschakelen/uitschakelen|-|✔|-|✔|✔|
|Aanbevelingen voor beveiliging toepassen op een resource</br> (en [Snelle oplossing](security-center-remediate-recommendations.md#quick-fix-remediation) gebruiken)|-|-|✔|✔|✔|
|Waarschuwingen verwijderen|-|✔|-|✔|✔|
|Meldingen en aanbevelingen weergeven|✔|✔|✔|✔|✔|

> [!NOTE]
> We raden u aan de rol toe te wijzen die gebruikers minimaal nodig hebben om hun taken uit te voeren. Wijs bijvoorbeeld gebruikers die alleen informatie hoeven te bekijken over de beveiligingsstatus van resources, maar geen maatregelen hoeven te nemen zoals het toepassen van aanbevelingen of het bewerken van beleid, de rol Lezer toe.
>
>

## <a name="next-steps"></a>Volgende stappen
In dit artikel wordt uitgelegd hoe Security Center Azure-RBAC gebruikt om machtigingen aan gebruikers toe te wijzen en de toegestane acties voor elke rol aan te geven. Nu u bekend bent met de roltoewijzingen die nodig zijn voor het bewaken van de beveiligingsstatus van uw abonnement, het bewerken van beveiligingsbeleid en het toepassen van aanbevelingen, kunt u het volgende gaan leren:

- [Beveiligingsbeleid instellen in Security Center](tutorial-security-policy.md)
- [Aanbevelingen voor beveiliging beheren in Security Center](security-center-recommendations.md)
- [De beveiligingsstatus van uw Azure-resources bewaken](security-center-monitoring.md)
- [Beveiligingswaarschuwingen beheren en erop reageren in Security Center](security-center-managing-and-responding-alerts.md)
- [Beveiligingsoplossingen van partners bewaken](./security-center-partner-integration.md)