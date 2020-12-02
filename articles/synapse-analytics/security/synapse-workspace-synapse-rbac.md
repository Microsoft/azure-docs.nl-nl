---
title: Op rollen gebaseerd toegangs beheer op basis van Synapse
description: Een artikel waarin op rollen gebaseerd toegangs beheer in azure Synapse Analytics wordt uitgelegd
author: billgib
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: security
ms.date: 12/1/2020
ms.author: billgib
ms.reviewer: jrasnick
ms.openlocfilehash: 7972f34bf0d2b93828899903e013c2e35bc997c0
ms.sourcegitcommit: 84e3db454ad2bccf529dabba518558bd28e2a4e6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/02/2020
ms.locfileid: "96523446"
---
# <a name="what-is-synapse-role-based-access-control-rbac"></a>Wat is Synapse op rollen gebaseerd toegangs beheer (RBAC)?

Synapse RBAC breidt de mogelijkheden van [Azure RBAC](https://docs.microsoft.com/azure/role-based-access-control/overview) voor Synapse-werk ruimten en hun inhoud uit. 

Azure RBAC wordt gebruikt om te beheren wie de Synapse-werk ruimte en de bijbehorende SQL-groepen, Apache Spark Pools en integratie-Runtimes kan maken, bijwerken of verwijderen.

Synapse RBAC wordt gebruikt om het volgende te beheren:
- Code artefacten en lijst-of toegangs gepubliceerde code artefacten publiceren, 
- Code uit te voeren op Apache Spark-Pools en Integration runtimes,
- Toegang tot gekoppelde services (gegevens) die worden beveiligd door referenties 
- Taak uitvoering controleren of annuleren, taak uitvoer en uitvoerings logboeken controleren.  

>[!Note]
>Hoewel Synapse RBAC wordt gebruikt voor het beheren van de toegang tot gepubliceerde SQL-scripts, biedt het slechts beperkt toegangs beheer voor serverloze SQL-groepen en wordt _niet_ gebruikt om de toegang tot toegewezen SQL-groepen te beheren.  Toegang tot SQL-groepen wordt voornamelijk bepaald met behulp van SQL-beveiliging.

## <a name="what-can-i-do-with-synapse-rbac"></a>Wat kan ik doen met Synapse RBAC?

Hier volgen enkele voor beelden van wat u met Synapse RBAC kunt doen:
  - Hiermee staat u een gebruiker toe om wijzigingen die zijn aangebracht in Apache Spark notitie blokken en-taken, naar de Live-service te publiceren.
  - Hiermee staat u een gebruiker toe om notebooks en Spark-taken uit te voeren en te annuleren voor een specifieke Apache Spark groep.
  - Een gebruiker toestaan om specifieke referenties te gebruiken, zodat ze pijp lijnen kunnen uitvoeren die worden beveiligd door de identiteit van het werkruimte systeem en toegang hebben tot gegevens in gekoppelde services die zijn beveiligd met referenties. 
  - Hiermee staat u toe dat een beheerder de taak uitvoering kan beheren, controleren en annuleren voor specifieke Spark-groepen.    

## <a name="how-synapse-rbac-works"></a>Hoe Synapse RBAC werkt
Net als bij Azure RBAC werkt Synapse RBAC door roltoewijzingen te maken. Een roltoewijzing bestaat uit drie elementen: een beveiligingsprincipal, een roldefinitie en een bereik.  

### <a name="security-principals"></a>Beveiligings-principals

Een _beveiligingsprincipal is een_ gebruiker, groep, Service-Principal of beheerde identiteit.

### <a name="roles"></a>Rollen
 
Een _rol_ is een verzameling machtigingen of acties die kunnen worden uitgevoerd op specifieke resource typen of artefact typen.

Synapse biedt ingebouwde rollen waarmee verzamelingen acties worden gedefinieerd die overeenkomen met de behoeften van verschillende personen:
- Beheerders kunnen volledige toegang krijgen om een werk ruimte te maken en te configureren 
- Ontwikkel aars kunnen SQL-scripts, notebooks, pijp lijnen en gegevens stromen maken, bijwerken en fouten opsporen, maar kunnen deze code niet publiceren of uitvoeren op productie Compute-resources/-gegevens
- Opera tors kunnen de systeem status controleren en beheren, de uitvoering van toepassingen en controle logboeken, zonder toegang tot code of de uitvoer van de uitvoering.
- Beveiligings personeel kan eind punten beheren en configureren zonder dat ze toegang hebben tot code, reken bronnen of gegevens.

Meer [informatie](./synapse-workspace-synapse-rbac-roles.md) over de ingebouwde Synapse-rollen. 

### <a name="scopes"></a>Bereiken

Een _bereik_ definieert de resources of artefacten waarop de toegang van toepassing is.  Synapse ondersteunt hiërarchische bereiken.  Machtigingen die worden verleend op een bereik op een hoger niveau, worden overgenomen door objecten op een lager niveau.  In Synapse RBAC is het bereik op het hoogste niveau een werk ruimte.  Het toewijzen van een rol met een werkruimte bereik verleent machtigingen voor alle toepasselijke objecten in de werk ruimte.  

Huidige ondersteunde bereiken binnen een werk ruimte zijn: Apache Spark pool, Integration runtime, gekoppelde service en referentie. 

Toegang tot code artefacten wordt verleend met het bereik van de werk ruimte.  Het verlenen van toegang tot verzamelingen artefacten binnen een werk ruimte wordt ondersteund in een latere versie.

## <a name="resolving-role-assignments-to-determine-permissions"></a>Roltoewijzingen omzetten om machtigingen te bepalen

Een roltoewijzing verleent de principal de machtigingen die zijn gedefinieerd door de rol bij het opgegeven bereik.

Synapse RBAC is een additief model zoals Azure RBAC. Meerdere rollen kunnen worden toegewezen aan één Principal en in verschillende bereiken. Bij het berekenen van de machtigingen van een beveiligingsprincipal, beschouwt het systeem alle rollen die aan de principal zijn toegewezen en aan groepen die direct of indirect de principal bevatten.  Het houdt ook rekening met het bereik van elke toewijzing bij het bepalen van de machtigingen die van toepassing zijn.  

## <a name="enforcing-assigned-permissions"></a>Toegewezen machtigingen afdwingen

In Synapse Studio kunnen specifieke knoppen of opties grijs worden weer gegeven of kan er een machtigings fout optreden wanneer u een actie uitvoert als u niet over de vereiste machtigingen beschikt. 

Als een knop of optie is uitgeschakeld, wordt met de muis aanwijzer boven de knop of optie een knop info met de vereiste machtiging weer gegeven.  Neem contact op met een Synapse-beheerder om een rol toe te wijzen waarmee de vereiste machtiging wordt verleend. U kunt de rollen zien die [hier](./synapse-workspace-synapse-rbac-roles.md)specifieke acties opgeven.

## <a name="who-can-assign-synapse-rbac-roles"></a>Wie kan Synapse RBAC-rollen toewijzen?

Alleen een Synapse-beheerder kan Synapse RBAC-rollen toewijzen.  Een Synapse-beheerder op het niveau van de werk ruimte kan in elk bereik toegang verlenen.  Een Synapse-beheerder op een lager bereik kan alleen toegang tot dat bereik verlenen. 

Wanneer er een nieuwe werk ruimte wordt gemaakt, krijgt de Maker automatisch de beheerdersrol Synapse in het werkruimte bereik.   

## <a name="where-do-i-manage-synapse-rbac"></a>Waar kan ik Synapse RBAC beheren?

Synapse RBAC wordt beheerd vanuit Synapse Studio met behulp van de toegangs beheer hulpprogramma's in de hub beheren. 

## <a name="next-steps"></a>Volgende stappen

Meer informatie over de ingebouwde [Synapse RBAC-rollen](./synapse-workspace-synapse-rbac-roles.md).

Meer informatie [over het controleren van Synapse RBAC-roltoewijzingen](./how-to-review-synapse-rbac-role-assignments.md) voor een werk ruimte.

Meer informatie [over het toewijzen van Synapse RBAC-rollen](./how-to-manage-synapse-rbac-role-assignments.md)